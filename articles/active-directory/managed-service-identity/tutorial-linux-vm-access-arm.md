---
title: Доступ к Azure Resource Manager с помощью MSI виртуальной машины Linux
description: В рамках этого руководства вы узнаете, как получить доступ к Azure Resource Manager с помощью управляемого удостоверения службы (MSI) виртуальной машины Linux.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 60a15c69f1ec748e366697640707804565245cea
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001591"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-resource-manager"></a>Получение доступа к Azure Resource Manager с помощью управляемого удостоверения службы виртуальной машины Linux

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этой статье описывается активация управляемого удостоверения службы (MSI) виртуальной машины Linux и его использование для получения доступа к API Azure Resource Manager. Управляемыми удостоверениями службы автоматически управляет Azure. Они позволяют проходить аутентификацию в службах, поддерживающих аутентификацию Azure AD, без указания учетных данных в коде. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Включение MSI на виртуальной машине Linux. 
> * Предоставление виртуальной машине доступа к группе ресурсов в Azure Resource Manager 
> * Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Azure Resource Manager с его помощью. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Linux в новой группе ресурсов

В рамках этого руководства мы создадим виртуальную машину Linux. Вы также можете активировать MSI на имеющейся виртуальной машине.

1. Нажмите кнопку **Создать ресурс** в верхнем левом углу окна портала Azure.
2. Выберите **Вычисления**, а затем — **Сервер Ubuntu 16.04 LTS**.
3. Введите сведения о виртуальной машине. Для параметра **Тип проверки подлинности** выберите значение **Открытый ключ SSH** или **Пароль**. Созданные учетные данные позволят вам выполнить вход на виртуальную машину.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. В раскрывающемся списке выберите **подписку** для виртуальной машины.
5. Чтобы выбрать новую **группу ресурсов**, в которой вы хотите создать виртуальную машину, щелкните **Создать**. По завершении нажмите кнопку **ОК**.
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр "Поддерживаемые типы диска". В колонке параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

## <a name="enable-msi-on-your-vm"></a>Активация MSI на виртуальной машине

MSI на виртуальной машине позволяет получить маркер доступа из Azure AD без необходимости указывать в коде учетные данные. Включение MSI предназначено для двух целей: выполняется регистрация виртуальной машины в Azure Active Directory для создания управляемого удостоверения и его настройка на этой виртуальной машине.

1. Выберите **виртуальную машину**, на которой нужно активировать MSI.
2. В левой области навигации щелкните **Конфигурация**.
3. Появится страница **Managed Service Identity** (Управляемое удостоверение службы). Чтобы зарегистрировать и активировать MSI, нажмите кнопку **Да**. Чтобы удалить удостоверение, нажмите кнопку "Нет".
4. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-azure-resource-manager"></a>Предоставление виртуальной машине доступа к группе ресурсов в Azure Resource Manager 

С помощью MSI код может получить маркеры доступа, чтобы пройти аутентификацию и получить доступ к ресурсам, поддерживающим аутентификацию Azure AD. API Azure Resource Manager поддерживает аутентификацию Azure AD. Сначала нам необходимо предоставить этому удостоверению виртуальной машины доступ к ресурсу в Azure Resource Manager, в этом случае — к группе ресурсов, в которой находится виртуальная машина.  

1. Перейдите на вкладку **Группы ресурсов**.
2. Выберите определенную **группу ресурсов**, созданную ранее.
3. Перейдите к элементу **Управление доступом (IAM)** на панели слева.
4. Щелкните **Добавить**, чтобы добавить новое назначение роли виртуальной машине. В поле **Роль** выберите **Читатель**.
5. В раскрывающемся списке далее в поле **Назначение доступа к** выберите ресурс **Виртуальная машина**.
6. Проверьте, чтобы в раскрывающемся списке **Подписка** была выбрана нужная подписка. В поле **Группа ресурсов** выберите **Все группы ресурсов**.
7. И наконец, в поле **Выбрать** выберите свою виртуальную машину Linux в раскрывающемся списке, а затем нажмите кнопку **Сохранить**.

    ![Замещающий текст](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Resource Manager с его помощью 

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](../../virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале перейдите на виртуальную машину Linux и в разделе **Обзор** щелкните **Подключиться**.  
2. **Подключитесь** к виртуальной машине с помощью выбранного клиента SSH. 
3. В окне терминала с помощью cURL выполните запрос к локальной конечной точке MSI, чтобы получить маркер доступа для Azure Resource Manager.  
 
    Запрос cURL маркера доступа приведен ниже.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
    
    > [!NOTE]
    > Значение параметра resource должно точно совпадать со значением, которое будет использоваться в Azure AD.  Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI). 
    
    Ответ включает маркер доступа, необходимый для доступа к Azure Resource Manager. 
    
    Ответ:  

    ```bash
    {"access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
    ```
    
    Этот маркер доступа можно использовать для доступа к Azure Resource Manager, например для просмотра подробных сведений о группе ресурсов, к которой вы ранее предоставили доступ виртуальной машине. Замените значения \<SUBSCRIPTION ID\>, \<RESOURCE GROUP\> и \<ACCESS TOKEN\> идентификатором подписки, группой ресурсов и маркером доступа, созданными ранее. 
    
    > [!NOTE]
    > В URL-адресе учитывается регистр знаков, поэтому должен использоваться тот же регистр, который использовался, когда вы присваивали имя группе ресурсов. Обязательно укажите прописную букву "G" в имени группы resourceGroup.  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Ответ с определенными сведениями о группе ресурсов выглядит следующим образом: 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```     

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать назначенное пользователем удостоверение и подключить его к виртуальной машине Azure, а затем получить доступ к API Azure Resource Manager.  Сведения об Azure Resource Manager см. здесь:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)