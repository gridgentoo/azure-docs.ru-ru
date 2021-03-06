---
title: Учебник. Интеграция Azure Active Directory с Hightail | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Hightail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: jeedes
ms.openlocfilehash: 7267f8fa1ed900d1bac58b4fa61f076e5949d712
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319111"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Учебник. Интеграция Azure Active Directory с Hightail

В этом учебнике описано, как интегрировать приложение Hightail с Azure Active Directory (Azure AD).

Интеграция Hightail с Azure AD дает приведенные далее преимущества:

- С помощью Azure AD вы можете контролировать доступ к Hightail.
- Вы можете включить автоматический вход пользователей в Hightail (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Hightail, вам потребуется:

- подписка Azure AD;
- подписка Hightail с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Hightail из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-hightail-from-the-gallery"></a>Добавление Hightail из коллекции
Чтобы настроить интеграцию Hightail с Azure AD, необходимо добавить Hightail из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Hightail из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Hightail**.

    ![Создание тестового пользователя Azure AD](./media/hightail-tutorial/tutorial_hightail_search.png)

5. На панели результатов выберите **Hightail** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Hightail с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Hightail соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Hightail.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Hightail.

Чтобы настроить и проверить единый вход Azure AD в Hightail, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Hightail](#creating-a-hightail-test-user)** требуется для создания в Hightail пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Hightail.

**Чтобы настроить единый вход Azure AD в Hightail, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Hightail** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Настройка единого входа](./media/hightail-tutorial/tutorial_hightail_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе**Домены и URL-адреса приложения Hightail** выполните следующие действия:

    ![Настройка единого входа](./media/hightail-tutorial/tutorial_hightail_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес следующим образом: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`.

    > [!NOTE]
    > Значение URL-адреса ответа приведено для примера. Это значение нужно заменить на фактический URL-адрес ответа, который описан далее в этом учебнике.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Настройка единого входа](./media/hightail-tutorial/tutorial_hightail_url1.png)

    В текстовом поле **URL-адрес для входа** введите следующий URL-адрес: `https://www.hightail.com/loginSSO`

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/hightail-tutorial/tutorial_hightail_certificate.png) 

5. Приложение Hightail ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке **Атрибут** приложения. На следующем снимке экрана приведен пример. 

    ![Настройка единого входа](./media/hightail-tutorial/tutorial_hightail_attribute.png) 

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | ------------------- | -------------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |    
    | UserIdentity | user.mail |
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/hightail-tutorial/tutorial_officespace_04.png)

    ![Настройка единого входа](./media/hightail-tutorial/tutorial_officespace_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.

    д. Нажмите кнопку **ОК**.

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/hightail-tutorial/tutorial_general_400.png)

8. В разделе **Конфигурация Hightail** щелкните **Настроить Hightail**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/hightail-tutorial/tutorial_hightail_configure.png)

    >[!NOTE]
    >Перед настройкой единого входа в приложение Hightail внесите почтовый домен Hightail в список разрешенных, чтобы все пользователи этого домена могли использовать функции единого входа.

9. В новом окне браузера откройте портал администрирования **Hightail**.

10. Щелкните **значок пользователя** в правом верхнем углу страницы. 

    ![Настройка единого входа](./media/hightail-tutorial/configure1.png)

11. Перейдите на вкладку **Просмотр консоли администрирования**.

    ![Настройка единого входа](./media/hightail-tutorial/configure2.png)

12. В меню в верхней части окна перейдите на вкладку **SAML** и выполните следующие действия:

    ![Настройка единого входа](./media/hightail-tutorial/configure3.png)

    a. В текстовое поле **URL-адрес входа** вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    Б. Откройте в Блокноте сертификат в кодировке Base64, скачанный с портала Azure, скопируйте его содержимое в буфер обмена и вставьте в текстовое поле **Сертификат SAML**.

    c. Щелкните **Копировать**, чтобы скопировать URL-адрес получателя SAML для своего экземпляра, и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домен и URL-адреса Hightail** на портале Azure.

    d. Щелкните **Сохранить конфигурации**.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/hightail-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/hightail-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/hightail-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/hightail-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-hightail-test-user"></a>Создание тестового пользователя Hightail

Цель этого раздела — создать пользователя с именем Britta Simon в Hightail. 

В этом разделе никакие действия с вашей стороны не требуются. Приложение Hightail поддерживает JIT-подготовку пользователей на основе настраиваемых утверждений. Если вы настроили пользовательские утверждения, как показано в разделе **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** выше, пользователь будет автоматически создан в приложении (если он еще не существует). 

>[!NOTE]
>Если нужно создать пользователя вручную, обратитесь в [службу поддержки Hightail](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Hightail.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Hightail, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Hightail**.

    ![Настройка единого входа](./media/hightail-tutorial/tutorial_hightail_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Hightail на панели доступа, вы автоматически войдете в приложение Hightail.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hightail-tutorial/tutorial_general_01.png
[2]: ./media/hightail-tutorial/tutorial_general_02.png
[3]: ./media/hightail-tutorial/tutorial_general_03.png
[4]: ./media/hightail-tutorial/tutorial_general_04.png

[100]: ./media/hightail-tutorial/tutorial_general_100.png

[200]: ./media/hightail-tutorial/tutorial_general_200.png
[201]: ./media/hightail-tutorial/tutorial_general_201.png
[202]: ./media/hightail-tutorial/tutorial_general_202.png
[203]: ./media/hightail-tutorial/tutorial_general_203.png

