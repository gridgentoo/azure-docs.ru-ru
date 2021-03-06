---
title: Создание концентратора событий Azure | Документация Майкрософт
description: Узнайте, как создать пространство имен концентраторов событий Azure и концентратор событий с помощью портала Azure.
services: event-hubs
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: sethm
ms.openlocfilehash: 9b466d4e727c1511ca2318c0da3ec2807a965a5d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625548"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Создание пространства имен концентраторов событий и концентратора событий с помощью портала Azure

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен концентраторов событий

1. Войдите на [портал Azure][Azure portal] и щелкните **Создать ресурс** в левой верхней части экрана.
2. Последовательно выберите **Интернет вещей** и **Концентраторы событий**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. В разделе **создания пространства имен** укажите имя пространства имен. Система немедленно проверяет, доступно ли оно.  

4. Убедившись, что пространство имен доступно, выберите ценовую категорию: "Базовый" или "Стандартный". Также выберите подписку Azure, группу ресурсов и расположение для создания ресурса.
 
5. Щелкните **Создать** , чтобы создать пространство имен. Полная подготовка ресурсов для системы может занять несколько минут.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. На портале в списке пространств имен щелкните имя только что созданного пространства имен.

7. В колонке **Политики общего доступа** щелкните **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Нажмите кнопку копирования, чтобы скопировать строку подключения **RootManageSharedAccessKey** в буфер обмена. Сохраните эту строку подключения во временном расположении папке, например в Блокноте, для последующего использования.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Создание концентратора событий

1. В списке пространств имен концентраторов событий щелкните созданное пространство имен.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. В колонке пространства имен щелкните **Концентраторы событий**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. Щелкните **+ Концентратор событий** в верхней части колонки.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Введите имя концентратора событий, а затем щелкните **Создать**. 

Теперь концентратор событий создан, и у вас есть строки подключения, необходимые для отправки и приема событий.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о концентраторах событий см. в следующих статьях:

* [Event Hubs overview](event-hubs-what-is-event-hubs.md)
* [Общие сведения об API концентраторов событий](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/