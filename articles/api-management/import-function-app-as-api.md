---
title: Импорт приложения-функции в качестве API с помощью портала Azure | Документация Майкрософт
description: В этом руководстве показано, как импортировать приложение-функцию в качестве API с помощью службы управления API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 0ee83446bb08e66c7f325bdd5585b8cc0484a74e
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090934"
---
# <a name="import-a-function-app-as-an-api"></a>Импорт приложения-функции в качестве API

В этой статье объясняется, как импортировать приложение-функцию в качестве интерфейса API. Также здесь показано, как проверить API службы управления API.

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * импорт приложения-функции в качестве API;
> * проверка API на портале Azure;
> * проверка API на портале разработчика.

## <a name="prerequisites"></a>Предварительные требования

+ Выполните задачи из краткого руководства по [созданию экземпляра службы управления API Azure](get-started-create-service-instance.md)
+ Убедитесь, что в вашей подписке есть приложение-функция Azure. Дополнительные сведения см. в разделе [Создание приложения-функции](../azure-functions/functions-create-first-azure-function.md#create-a-function-app).
+ [Создание определения OpenAPI](../azure-functions/functions-openapi-definition.md) приложения-функции Azure

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Импорт и публикация API серверной части

1. Выберите **Интерфейсы API** в разделе **УПРАВЛЕНИЕ API**.
2. Выберите **Приложение-функция** в списке **Add a new API** (Добавление нового API).

    ![Приложение-функция](./media/import-function-app-as-api/function-app-api.png)
3. Нажмите кнопку **Обзор**, чтобы просмотреть список приложений-функций в вашей подписке.
4. Выберите приложение. Служба управления API найдет файл Swagger, связанный с выбранным приложением, извлечет и импортирует его. 
5. Добавьте суффикс URL-адреса API. Суффиксом называется имя, которое идентифицирует конкретный API в экземпляре службы управления API. Он должен быть уникальным в пределах экземпляра службы управления API.
6. Чтобы опубликовать API, его нужно связать с определенным продуктом. В нашем случае используется продукт *Без ограничений*.  Если вы планируете опубликовать API и предоставить разработчикам доступ к нему, добавьте его в продукт. Это можно сделать во время создания API или позже.

    Продуктами называют ассоциации из одного или нескольких API. Вы можете объединить в них определенное количество API и предлагать их разработчикам через портал разработчика. Чтобы получить доступ к API, разработчикам необходимо сначала подписаться на продукт. После этого они получат ключ подписки, который подходит для любого API в этом продукте. Создавая экземпляр службы управления API, вы автоматически становитесь его администратором. Поэтому вы по умолчанию будете подписаны на все продукты.

    По умолчанию каждый экземпляр API управления поставляется с двумя демонстрационными продуктами:

    * **Starter**
    * **Unlimited**   
7. Нажмите кнопку **Создать**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Заполнение ключей Функций Azure в службе управления API Azure

Если импортируемые Функции Azure защищены с помощью ключей, служба управления API Azure автоматически создает **именованные значения** для них, но не заполняет записи секретными данными. Для каждой записи необходимо выполнить следующие действия.  

1. Перейдите на вкладку **Именованные значения** в экземпляре службы управления API.
2. Щелкните запись и нажмите кнопку **Показать значение** на боковой панели.

    ![Именованные значения](./media/import-function-app-as-api/apim-named-values.png)

3. Если содержимое имеет вид *код для {имя Функции Azure}*, перейдите к импортированному приложению-функции Azure и выберите свою Функцию Azure.
4. Перейдите в раздел **Управление** для выбранной Функции Azure и скопируйте соответствующий ключ на основе метода проверки подлинности Функции Azure.

    ![Приложение-функция](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Вставьте в текстовое поле ключ, полученный на вкладке **Именованные значения**, и нажмите кнопку **Сохранить**.

    ![Приложение-функция](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Проверка нового API службы управления API на портале Azure

Операции можно вызывать непосредственно на портале Azure. Это удобный способ просмотра и проверки операций API.  

1. Выберите API, созданный на предыдущем шаге.
2. Откройте вкладку **Тест**.
3. Выберите операцию.

    На странице отобразятся поля для параметров запроса и для заголовков. Один из заголовков, Ocp-Apim-Subscription-Key, содержит ключ подписки для продукта, связанного с этим API. Как создатель экземпляра службы управления API, вы автоматически являетесь администратором, поэтому сведения о ключе будут заполнены автоматически. 
1. Нажмите кнопку **Отправить**.

    Служба серверной части вернет ответ **200 — ОК** и другие данные.

## <a name="call-operation"> </a>Вызов операции с портала разработчика

Также операции можно вызвать через **портал разработчиков**, чтобы проверить API. 

1. Выберите API, который вы создали на шаге "Импорт и публикация API серверной части".
2. Щелкните **Портал разработчика**.

    Откроется сайт портала разработчика.
3. Выберите созданный **API**.
4. Выберите операцию, которую необходимо проверить.
5. Щелкните **Попробовать**.
6. Нажмите кнопку **Отправить**.
    
    После вызова операции портал разработчика отображает **состояние ответа**, **заголовки ответа** и **содержимое ответа**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Преобразование и защита опубликованного API](transform-api.md)