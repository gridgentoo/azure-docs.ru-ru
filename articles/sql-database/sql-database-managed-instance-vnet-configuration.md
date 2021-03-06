---
title: Конфигурация виртуальной сети для Управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт
description: В этой статье описываются параметры конфигурации виртуальной сети (VNet) для Управляемого экземпляра Базы данных SQL Azure.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 0fea91fb067a6d78ef25cb0ff8014b65a8b6a916
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258106"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Настройка виртуальной сети для Управляемого экземпляра Базы данных SQL Azure

Управляемый экземпляр Базы данных SQL Azure (предварительная версия) должен быть развернут в [виртуальной сети (VNet)](../virtual-network/virtual-networks-overview.md). Это развертывание поддерживает следующие сценарии: 
- Подключение к управляемому экземпляру непосредственно из локальной сети 
- Подключение управляемого экземпляра к связанному серверу или другому локальному хранилищу данных 
- Подключение управляемого экземпляра к ресурсам Azure  

## <a name="plan"></a>План

Составьте план по развертыванию управляемого экземпляра в виртуальной сети в соответствии с ответами на следующие вопросы: 
- Сколько управляемых экземпляров вы планируете развертывать: один или несколько? 

  От числа управляемых экземпляров зависит минимальный размер подсети для их размещения. Дополнительные сведения см. в разделе [Определение размера подсети для управляемого экземпляра](#create-a-new-virtual-network-for-managed-instances). 
- Будет ли управляемый экземпляр развернут в имеющейся виртуальной сети или же вы создадите для него новую? 

   Если вы планируете использовать имеющуюся виртуальную сеть, необходимо изменить ее конфигурацию, чтобы она соответствовала управляемому экземпляру. Дополнительные сведения см. в разделе [Изменение существующей виртуальной сети для управляемого экземпляра](#modify-an-existing-virtual-network-for-managed-instances). 

   Если вы планируете создать виртуальную сеть, см. раздел [Создание виртуальной сети для управляемого экземпляра](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Требования

Для создания управляемого экземпляра внутри виртуальной сети необходимо выделить подсеть, которая соответствует следующим требованиям:
- **Пустая подсеть**. С подсетью не должны быть связаны другие облачные службы и она не должна быть подсетью шлюза. Вы не сможете создать управляемый экземпляр в подсети, если в ней содержатся другие ресурсы, кроме управляемого экземпляра, а также не сможете позже добавить в такую подсеть другие ресурсы.
- **Отсутствие группы безопасности сети**. С подсетью не должна быть связана группа безопасности сети.
- **Наличие определенной таблицы маршрутов**. Подсеть должна иметь пользовательскую таблицу маршрутов (UDR) с интернет-маршрутом следующего прыжка 0.0.0.0/0, который должен быть единственным назначенным ей маршрутом. Дополнительные сведения см. в разделе [Создание необходимой таблицы маршрутов и ее привязка](#create-the-required-route-table-and-associate-it)
3. **Наличие необязательной пользовательской DNS**. Если в виртуальной сети определена пользовательская DNS, то в список должен быть добавлен IP-адрес рекурсивных сопоставителей Azure (например, 168.63.129.16). Дополнительные сведения см. в статье [Configuring a Custom DNS for Azure SQL Database Managed Instance](sql-database-managed-instance-custom-dns.md) (Настройка пользовательской DNS для Управляемого экземпляра Базы данных SQL Azure).
4. **Отсутствие конечной точки службы**. С подсетью не должна быть связана конечная точка службы (хранилище или база данных SQL). При создании виртуальной сети отключите параметр "Конечные точки службы".
5. **Наличие достаточного количества IP-адресов**. Для подсети необходимо как минимум 16 IP-адресов. Дополнительные сведения см. в разделе [Определение размера подсети для управляемого экземпляра](#determine-the-size-of-subnet-for-managed-instances).

> [!IMPORTANT]
> Если целевая подсеть не соответствует каким-либо из перечисленных выше требований, в ней невозможно развернуть управляемый экземпляр. Целевая виртуальная сеть и подсети должны соответствовать этим требованиям для управляемого экземпляра (до и после развертывания), так как любое их нарушение может привести к неисправности или недоступности экземпляра. Для восстановления из этого состояния необходимо создать новый экземпляр в виртуальной сети с соответствующими политиками сети, повторно создать данные уровня экземпляра и восстановить базы данных. Это приведет к простою ваших приложений в течение значительного времени.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Определение размера подсети для управляемого экземпляра

При создании управляемого экземпляра Azure размещает несколько виртуальных машин, число которых зависит от размера уровня, выбранного во время подготовки. Поскольку виртуальные машины связанны с подсетью, им необходимы IP-адреса. Azure может выделить дополнительные виртуальные машины для обеспечения высокой доступности во время выполнения обычных операций и технического обслуживания. В результате число необходимых IP-адресов будет больше, чем число управляемых экземпляров в подсети. 

Для управляемого экземпляра необходимо не менее 16 и не более 256 IP-адресов в подсети. Поэтому при определении IP-адресов подсети для маски подсети можно использовать значения от /28 до /24. 

Если планируется развертывать несколько управляемых экземпляров внутри подсети и при этом необходимо оптимизировать размер подсети, для расчета нужно использовать такие параметры: 

- Azure использует пять IP-адресов в подсети для своих потребностей 
- Каждому экземпляру общего назначения требуются два адреса 
- Каждому критически важному для бизнеса экземпляру требуются четыре адреса.

**Пример**. Вы планируете использовать три экземпляра общего назначения и два критически важных для бизнеса экземпляра. Это означает, что требуется 5 + 3 * 2 + 2 * 4 = 19 IP-адресов. Поскольку диапазоны IP-адресов определяются как значения в степени 2, вам потребуется диапазон из 32 (2 ^ 5) IP-адресов. Таким образом, необходимо зарезервировать подсеть с маской подсети /27. 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>Создание виртуальной сети для управляемых экземпляров 

Создание виртуальной сети Azure является необходимым условием для создания управляемого экземпляра. Для этого можно использовать портал Azure, [PowerShell](../virtual-network/quick-create-powershell.md) или [Azure CLI](../virtual-network/quick-create-cli.md). В следующих разделах продемонстрированы шаги использования портала Azure. Изложенные здесь сведения касаются каждого из этих методов.

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.
2. Найдите и выберите **виртуальную сеть**, для режима развертывания выберите **диспетчер ресурсов**, а затем нажмите кнопку **Создать**.

   ![создание виртуальной сети](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Укажите запрошенные сведения в форме виртуальной сети способом, указанным на следующем снимке экрана:

   ![форма создания виртуальной сети](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Нажмите кнопку **Создать**.

   Адресное пространство и подсеть указываются в нотации CIDR. 

   > [!IMPORTANT]
   > Подсеть, созданная со значениями по умолчанию, будет занимать все адресное пространство виртуальной сети. Если будет выбран этот параметр, внутри виртуальной сети можно создать только управляемый экземпляр, а другие ресурсы создать невозможно. 

   Рекомендуется использовать следующий подход: 
   - Вычислите размер подсети, следуя указаниям в разделе [Определение размера подсети для управляемого экземпляра](#determine-the-size-of-subnet-for-managed-instances).  
   - Оцените потребности для остальной виртуальной сети. 
   - Заполните соответствующим образом диапазон адресов для подсети и виртуальной сети. 

   Убедитесь, что параметр "Конечные точки службы" **отключен**. 

   ![форма создания виртуальной сети](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>Создание необходимой таблицы маршрутов и ее привязка

1. Вход на портал Azure  
2. Найдите и выберите **Таблица маршрутов**, а затем на странице таблицы маршрутов нажмите кнопку **Создать**.

   ![форма создания таблицы маршрутов](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Создайте интернет-маршрут следующего прыжка 0.0.0.0/0 способом, указанным на следующем снимке экрана:

   ![добавление таблицы маршрутов](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![маршрут](./media/sql-database-managed-instance-tutorial/route.png)

4. Свяжите этот маршрут с подсетью управляемого экземпляра способом, указанным на следующем снимке экрана:

    ![подсеть](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![настройка таблицы маршрутов](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![настройка и сохранение таблицы маршрутов](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


После создания виртуальной сети можно приступать к созданию управляемого экземпляра.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Изменение существующей виртуальной сети для управляемого экземпляра 

Просмотрев вопросы и ответы в этом разделе, вы узнаете, как добавить управляемый экземпляр в существующую виртуальную сеть. 

**Какая модель используется для развертывания существующей виртуальной сети: классическая или развертывание с помощью Resource Manager?** 

Управляемый экземпляр можно создать только в виртуальных сетях Resource Manager. 

**Вы хотите создать подсеть для управляемого экземпляра SQL или использовать имеющуюся?**.

Перед созданием подсети сделайте следующее: 

- Вычислите размер подсети, следуя инструкциям из раздела [Определение размера подсети для управляемого экземпляра](#determine-the-size-of-subnet-for-managed-instances).
- Следуйте инструкциям из статьи [Создание, изменение или удаление виртуальной сети](../virtual-network/virtual-network-manage-subnet.md). 
- Создайте таблицу маршрутов, которая содержит одну запись **0.0.0.0/0** в качестве интернет-маршрута следующего прыжка и свяжите ее с подсетью управляемого экземпляра.  

В этом случае необходимо создать управляемый экземпляр внутри имеющейся подсети. 
- Если подсеть пуста — управляемый экземпляр не может быть создан в подсети, которая содержит другие ресурсы, включая подсеть шлюза. 
- Вычислите размер подсети, следуя инструкциям из раздела [Определение размера подсети для управляемого экземпляра](#determine-the-size-of-subnet-for-managed-instances), настройте его соответствующим образом. 
- Убедитесь, что конечные точки службы не включены в подсеть.
- Убедитесь, что с подсетью не связаны группы безопасности сети. 

**Настроен ли у вас пользовательский сервер DNS?** 

Если ответ утвердительный, см. статью [Configuring a Custom DNS for Azure SQL Database Managed Instance](sql-database-managed-instance-custom-dns.md) (Настройка пользовательской DNS для Управляемого экземпляра Базы данных SQL Azure). 

- Создайте необходимую таблицу маршрутов и выполните ее привязку: см. раздел [Создание необходимой таблицы маршрутов и ее привязка](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [Общие сведения об Управляемом экземпляре Базы данных SQL Azure (предварительная версия)](sql-database-managed-instance.md).
- Чтобы узнать как создать виртуальную сеть и управляемый экземпляр, а также восстановить базу данных из резервной копии базы данных см. статью [Создание Управляемого экземпляра Базы данных SQL Azure на портале Azure](sql-database-managed-instance-create-tutorial-portal.md).
- Во избежание проблем при настройке DNS см. статью [Configuring a Custom DNS for Azure SQL Database Managed Instance](sql-database-managed-instance-custom-dns.md) (Настройка пользовательской DNS для Управляемого экземпляра Базы данных SQL Azure)
