---
title: Использование Azure Database Migration Service для переноса SQL Server в базу данных SQL Azure | Документация Майкрософт
description: Узнайте, как выполнять миграцию из локального экземпляра SQL Server в базу данных SQL Azure с помощью Azure Database Migration Service.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/08/2018
ms.openlocfilehash: f64b2922818eddcab02f7d1c7b8f97671d92589e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850259"
---
# <a name="migrate-sql-server-to-azure-sql-database-using-dms"></a>Миграция с SQL Server в базу данных SQL Azure с помощью DMS
Azure Database Migration Service можно использовать для переноса баз данных из локального экземпляра SQL Server в [базу данных SQL Azure](https://docs.microsoft.com/en-us/azure/sql-database/). В этом руководстве выполняется миграция базы данных **Adventureworks2012**, восстановленной в локальном экземпляре SQL Server 2016 (или более поздней версии), в базу данных SQL Azure с помощью Azure Database Migration Service.

Из этого руководства вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * получение доступа к локальной базе данных с помощью помощника по миграции данных;
> * перенос примера схемы с помощью помощника по миграции данных;
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * мониторинг миграции.
> * скачивание отчета о миграции.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством вам потребуется следующее:

- Скачайте и установите [SQL Server 2016 или более поздней версии](https://www.microsoft.com/sql-server/sql-server-downloads) (любой выпуск).
- При установке SQL Server Express протокол TCP/IP отключен по умолчанию. Включите его, выполнив инструкции в статье [Включение или отключение сетевого протокола сервера](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Создайте экземпляр базы данных SQL Azure, следуя инструкциям в статье [Создание базы данных SQL Azure на портале Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Скачайте и установите [Помощник по миграции данных](https://www.microsoft.com/download/details.aspx?id=53595) версии 3.3 или более поздней.
- Создайте виртуальную сеть для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Убедитесь, что правила группы безопасности сети для виртуальной сети Azure не блокируют порты связи 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
- Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
- Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
- Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для сервера базы данных SQL Azure, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.
- Убедитесь, что учетные данные, используемые для подключения к исходному экземпляру SQL Server, имеют разрешения [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Убедитесь, что учетные данные, используемые для подключения к целевому экземпляру базы данных SQL Azure, имеют разрешения CONTROL DATABASE в целевых базах данных SQL Azure.

## <a name="assess-your-on-premises-database"></a>Оценка локальной базы данных
Перед переносом данных из локального экземпляра SQL Server в базу данных SQL Azure необходимо оценить базу данных SQL Server на наличие любых проблем, связанных с блокировкой, которые могут помешать миграции. В Помощнике по миграции данных версии 3.3 или более поздней выполните инструкции по [оценке миграции SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem), чтобы завершить оценку локальной базы данных. Ниже приведена сводка необходимых действий:
1.  В помощнике по миграции данных выберите значок "Создать" (+), а затем выберите тип проекта **Оценка**.
2.  Укажите имя проекта в текстовом поле **Тип исходного сервера**, выберите **SQL Server**, в текстовом поле **Тип конечного сервера** выберите **База данных SQL Azure**, а затем нажмите **Создать**, чтобы создать проект.

    При оценке исходной базы данных SQL Server, переносимой в базу данных SQL Azure, можно выбрать один или несколько следующих типов отчетов об оценке:
    - проверка совместимости базы данных;
    - проверка четности компонентов.

    По умолчанию выбраны оба типа отчетов.

3.  В помощнике по миграции данных на экране **Параметры** выберите **Далее**.
4.  На экране **Выберите источники** в диалоговом окне **Соединение с сервером** предоставьте сведения о подключении к SQL Server, а затем выберите **Подключить**.
5.  В диалоговом окне **Add sources** (Добавление источников) выберите **AdventureWorks2012**, а затем щелкните **Add** (Добавить) и выберите **Start Assessment** (Начать оценку).

    После завершения оценки результаты будут показаны, как на следующем рисунке:

    ![Оценка миграции данных](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    В базе данных SQL Azure внутренняя оценка идентифицирует проблемы с равенством функций и проблемы, блокирующие миграцию.

    - Для категории **четности компонентов SQL Server** доступны разные рекомендации, а также описание альтернативных механизмы, используемых в Azure, и мер по устранению, которые помогут вам рассчитать трудозатраты для выполнения миграции.
    - Категория **проблем совместимости** определяет частично поддерживаемые или неподдерживаемые возможности, связанные с проблемами совместимости, которые могут блокировать миграцию локальных баз данных SQL Server в базу данных SQL Azure. Также предлагаются рекомендации, которые помогут вам решить эти проблемы.

6.  Просмотрите результаты оценки проблем, блокирующих миграцию, и проблем с четностью компонентов, выбрав конкретные параметры.

## <a name="migrate-the-sample-schema"></a>Перенос примера схемы
Если результаты оценки вас устраивают и выбранная база данных подходит для миграции в базу данных SQL Azure, воспользуйтесь Помощником по миграции данных для переноса схемы в базу данных SQL Azure.

> [!NOTE]
> Прежде чем создавать проект миграции в Data Migration Assistant, убедитесь, что база данных SQL Azure уже подготовлена, как описано выше. В рамках этого руководства для базы данных SQL Azure используется имя **AdventureWorksAzure**, но вы можете назвать ее иначе.

Чтобы перенести схему **AdventureWorks2012** в базу данных SQL Azure, выполните следующие действия:

1.  В Помощнике по миграции данных щелкните значок New (+) (Создать (+)), а затем в разделе **Project type** (Тип проекта) выберите **Migration** (Миграция).
2.  Укажите имя проекта в текстовом поле **Source server type** (Тип исходного сервера), выберите **SQL Server**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **База данных SQL Azure**.
3.  В разделе **Migration Scope** (Область переноса) выберите **Schema only** (Только схема).

    После выполнения предыдущих действий интерфейс помощника по миграции данных должен выглядеть, как показано на следующем рисунке:
    
    ![Создание проекта в помощнике по миграции данных](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

4.  Выберите **Создать**, чтобы создать проект.
5.  В помощнике по миграции данных задайте сведения о подключении к источнику SQL Server, щелкните **Подключение**, а затем выберите базу данных **AdventureWorks2012**.

    ![Сведения о подключении к источнику в помощнике по миграции данных](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)

6.  В разделе **Connect to target server** (Подключение к целевому серверу) нажмите кнопку **Next** (Далее), укажите сведения о подключении к целевому объекту для базы данных SQL Azure, щелкните **Connect** (Подключение), а затем выберите базу данных **AdventureWorksAzure**, предварительно подготовленную в базе данных SQL Azure.

    ![Сведения о подключении к целевому объекту в помощнике по миграции данных](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)

7.  Выберите **Далее**, чтобы перейти на экран **Выбор объектов**, на котором можно указать объекты схемы в базе данных **AdventureWorks2012**, которую нужно развернуть в базе данных SQL Azure.

    По умолчанию выбраны все объекты.

    ![Создание сценариев SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)

8.  Выберите **Создать скрипт SQL**, чтобы создать скрипт SQL, а затем просмотрите его на наличие ошибок.

    ![Скрипт схемы](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)

9.  Выберите **Deploy schema** (Развернуть схему), чтобы развернуть схему в базу данных SQL Azure, а затем проверьте целевой сервер на наличие аномалий.

    ![Развертывание схемы](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration
1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.
 
   ![Отображение подписок на портале](media\tutorial-sql-server-to-azure-sql\portal-select-subscription1.png)
       
2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.
 
    ![Отображение поставщиков ресурсов](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)
    
3.  В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.
 
    ![Регистрация поставщика ресурсов](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Создание экземпляра
1.  На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)

2.  На экране **Azure Database Migration Service** выберите **Создать**.
 
    ![Создание экземпляра Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-create1.png)
  
3.  На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите существующую виртуальную сеть или создайте новую.

    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру SQL Server и конечному экземпляру базы данных SQL Azure.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

5. Выберите ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

    Если вам нужна помощь в выборе подходящей категории Azure Database Migration Service, см. рекомендации в [этой записи блога](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Настройка параметров экземпляра Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-settings1.png)

6.  Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции
После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).
 
      ![Поиск всех экземпляров Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. На экране **Azure Database Migration Services** найдите имя созданного экземпляра Azure Database Migration Service и выберите его.
 
     ![Поиск экземпляра службы миграции базы данных Azure](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Выберите **+ Новый проект миграции**.
4. На экране **New migration project** (Новый проект миграции) задайте имя для проекта, в текстовом поле **Source server type** (Тип исходного сервера) выберите **SQL Server**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **База данных SQL Azure**.

    ![Создание проекта Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-create-project1.png)

5.  Выберите **Создать**, чтобы создать проект.

## <a name="specify-source-details"></a>Указание сведений об источнике
1. На экране **Сведения об источнике** задайте сведения о подключении для исходного экземпляра SQL Server.
 
    Используйте для имени исходного экземпляра SQL Server полное доменное имя (FQDN). В случаях, когда разрешение DNS-имен невозможно, можно использовать IP-адрес.

2. Если на исходном сервере доверенный сертификат не установлен, установите флажок **Доверять сертификату сервера**.

    Если доверенный сертификат не установлен, SQL Server создает самозаверяющий сертификат при запуске экземпляра. Этот сертификат используется с целью шифрования учетных данных для клиентских подключений.

    > [!CAUTION]
    > SSL-соединения, шифруемые с помощью самозаверяющего сертификата, не обеспечивают надежной защиты. Они уязвимы для атак "злоумышленник в середине". В рабочей среде или на серверах, подключенных к Интернету, не следует применять самозаверяющие сертификаты для SSL.

   ![Сведения об источнике](media\tutorial-sql-server-to-azure-sql\dms-source-details1.png)
  
2. Выберите **Сохранить**, а затем щелкните базу данных **AdventureWorks2012** для миграции.

    ![Выбор исходной базы данных](media\tutorial-sql-server-to-azure-sql\dms-select-source-db1.png)

## <a name="specify-target-details"></a>Указание сведений о цели
1. Выберите **Сохранить**, а затем на экране **Данные целевого объекта** задайте сведения о подключении для конечного сервера базы данных SQL — предварительно подготовленной базы данных SQL Azure, в которой была развернута схема **AdventureWorks2012** посредством Помощника по миграции данных.

    ![Выбор цели](media\tutorial-sql-server-to-azure-sql\dms-select-target1.png)

2. Выберите **Сохранить**, чтобы сохранить проект.

3. На экране **Сводка по проекту** просмотрите и проверьте сведения, связанные с проектом миграции.

    ![Сводка DMS](media\tutorial-sql-server-to-azure-sql\dms-summary1.png)

4. Щелкните **Сохранить**.

## <a name="run-the-migration"></a>Выполнение миграции
1.  Выберите недавно сохраненный проект, а затем щелкните **+ Новое действие** и выберите **Выполнить миграцию**.

    ![Новое действие](media\tutorial-sql-server-to-azure-sql\dms-new-activity1.png)

2.  При появлении запроса введите учетные данные исходного и целевого сервера, а затем выберите **Сохранить**.

3.  На экране **Map to target databases** (Сопоставить с целевыми базами данных) сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится такое же имя базы данных, что и в исходной базе данных, Azure DMS выберет целевую базу данных по умолчанию.

    ![Сопоставление с целевыми базами данных](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity1.png)

4. На экране **Выбрать таблицы** выберите **Сохранить**, разверните список таблиц и просмотрите список затронутых полей.

    Обратите внимание на то, что служба Azure Database Migration Service автоматически выбирает все пустые исходные таблицы, имеющиеся в конечном экземпляре базы данных SQL Azure. Чтобы повторно перенести таблицы, уже содержащие данные, необходимо явным образом выбрать их в этой колонке.

    ![Выбор таблиц](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity1.png)

5.  На экране **Migration summary** (Сводка по миграции) выберите **Сохранить**, а в текстовом поле **Имя действия** задайте имя для действия миграции.

6. Разверните раздел **Вариант проверки**, чтобы открыть экран **Выбор варианта проверки**, где можно выбрать категорию проверки переносимых баз данных: **сравнение схемы**; **согласованность данных** или **правильность запроса**.
    
    ![Выбор варианта проверки](media\tutorial-sql-server-to-azure-sql\dms-configuration1.png)

6.  Выберите **Сохранить**, просмотрите сводку, чтобы убедиться, что сведения об источнике и целевом объекте соответствуют предварительно заданным.

    ![Сводка по миграции](media\tutorial-sql-server-to-azure-sql\dms-run-migration1.png)

7.  Выберите **Запустить миграцию**.

    Появится окно действия миграции, в котором будет указано **состояние** действия **Ожидание**.

    ![Состояние действия](media\tutorial-sql-server-to-azure-sql\dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Мониторинг миграции
1. На экране действия миграции нажимайте кнопку **Обновить**, чтобы обновить содержимое экрана, пока **состояние** миграции не поменяется на **Завершено**.

    ![Состояние действия "Завершено"](media\tutorial-sql-server-to-azure-sql\dms-completed-activity1.png)

2. После завершения миграции нажмите **Скачать отчет**, чтобы получить отчет с подробными сведениями о процессе миграции.

3. Проверьте целевые базы данных в целевом сервере база данных SQL Azure.

### <a name="additional-resources"></a>Дополнительные ресурсы

 * Практическая работа [Миграция SQL с помощью Azure Data Migration Service (DMS)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587)