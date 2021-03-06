---
title: Руководство по интеграции Azure Active Directory с Settling music | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Settling music.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.openlocfilehash: 4a4d4fa704381ed9ab7c79c6ad0f6196a9ac37f2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040377"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Руководство по интеграции Azure Active Directory с Settling music

В этом руководстве описано, как интегрировать Settling music с Azure Active Directory (Azure AD).

Интеграция Settling music с Azure AD обеспечивает следующие преимущества:

- C помощью Azure AD вы можете контролировать доступ к Settling music.
- Вы можете включить автоматический вход пользователей в Settling music (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD со Settling music, вам потребуется:

- подписка Azure AD;
- подписка Settling music с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Settling music из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-settling-music-from-the-gallery"></a>Добавление Settling music из коллекции
Чтобы настроить интеграцию Settling music с Azure AD, необходимо добавить Settling music из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Settling music из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Settling music**, на панели результатов выберите **Settling music** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Settling music в списке результатов](./media/settlingmusic-tutorial/tutorial_settlingmusic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Settling music с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Settling music соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Settling music.

Чтобы настроить и проверить единый вход Azure AD в Settling music, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Settling music](#create-a-settling-music-test-user)** требуется для того, чтобы в Settling music существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Settling music.

**Чтобы настроить единый вход Azure AD в Settling music, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Settling music** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/settlingmusic-tutorial/tutorial_settlingmusic_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Settling music** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Settling music](./media/settlingmusic-tutorial/tutorial_settlingmusic_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Settling music](https://rakurakuseisan.jp/). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/settlingmusic-tutorial/tutorial_settlingmusic_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/settlingmusic-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Settling music** щелкните **Настроить Settling music**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Settling music](./media/settlingmusic-tutorial/tutorial_settlingmusic_configure.png) 

7. В другом окне браузера войдите в приложение Settling music в качестве администратора безопасности.

8. Вверху страницы щелкните вкладку **management** (Управление).

    ![Settling music: шаг 1](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

9. Щелкните вкладку **System setting** (Параметры системы).

    ![Settling music: шаг 2](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

10. Выберите вкладку **Security** (Безопасность).

    ![Settling music: шаг 3](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

11. В разделе **Single sign-on setting** (Параметры единого входа) выполните следующие действия:

    ![Settling music: шаг 5](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Щелкните **To enable** (Включение).

    b. В текстовое поле **Login URL of the ID provider** (URL-адрес входа поставщика удостоверений) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    c. В текстовое поле **ID provider logout URL** (URL-адрес выхода поставщика удостоверений) вставьте значение **Sign-Out URL** (URL-адрес выхода), скопированное на портале Azure.

    d. Нажмите кнопку **Choose File** (Выбрать файл), чтобы передать **сертификат в формате Base64**, скачанный с портала Azure.

    д. Нажмите кнопку **Сохранить** .

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/settlingmusic-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/settlingmusic-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/settlingmusic-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/settlingmusic-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-settling-music-test-user"></a>Создание тестового пользователя Settling music

В этом разделе описано, как создать пользователя Britta Simon в Settling music. Обратитесь к [группе поддержки клиентов Settling music](https://rakurakuseisan.jp/), чтобы добавить пользователей на платформу Settling music. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Settling music.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Settling music, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Settling music**.

    ![Ссылка на Settling music в списке "Приложения"](./media/settlingmusic-tutorial/tutorial_settlingmusic_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Settling music" на панели доступа, вы автоматически войдете в приложение Settling music.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/settlingmusic-tutorial/tutorial_general_01.png
[2]: ./media/settlingmusic-tutorial/tutorial_general_02.png
[3]: ./media/settlingmusic-tutorial/tutorial_general_03.png
[4]: ./media/settlingmusic-tutorial/tutorial_general_04.png

[100]: ./media/settlingmusic-tutorial/tutorial_general_100.png

[200]: ./media/settlingmusic-tutorial/tutorial_general_200.png
[201]: ./media/settlingmusic-tutorial/tutorial_general_201.png
[202]: ./media/settlingmusic-tutorial/tutorial_general_202.png
[203]: ./media/settlingmusic-tutorial/tutorial_general_203.png

