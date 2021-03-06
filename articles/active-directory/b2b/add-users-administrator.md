---
title: 'Добавление пользователей службы совместной работы B2B на портале Azure: Azure Active Directory | Документация Майкрософт'
description: В этой статье вы узнаете, как администратор может добавлять гостевых пользователей в свои каталоги из партнерских организаций с помощью службы совместной работы Azure Active Directory (Azure AD) B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 07/10/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f654aaa6d44011a089008558849d37bf6cdfa6f6
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037228"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Добавление пользователей службы совместной работы B2B на портале Azure

Находясь в роли глобального администратора или пользователя, которому назначена любая из ограниченных ролей администратора каталога, вы можете использовать портал Azure для приглашения пользователей службы совместной работы B2B. Вы можете приглашать гостевых пользователей в каталог, группу или приложение. После приглашения пользователя (любым из способов) его учетная запись добавляется в Azure Active Directory (Azure AD) с типом пользователя *Гость*. Затем для доступа к ресурсам гостевой пользователь должен активировать приглашение.

После добавления гостевого пользователя в каталог вы можете отправить ему прямую ссылку на общее приложение или он может щелкнуть URL-адрес активации в электронном письме с приглашением. Дополнительные сведения о процессе активации приглашений см. в статье [Azure Active Directory B2B collaboration invitation redemption](redemption-experience.md) (Активация приглашения службы совместной работы Azure Active Directory B2B).

> [!IMPORTANT]
> Добавьте URL-адрес заявления о конфиденциальности вашей организации, следуя инструкциям в статье [Практическое руководство. Добавление сведений о конфиденциальности организации в Azure Active Directory](https://aka.ms/adprivacystatement). В рамках процесса первой активации приглашения приглашенный пользователь должен согласиться на ваши условия конфиденциальности, чтобы продолжить. 

## <a name="add-guest-users-to-the-directory"></a>Добавление гостевых пользователей в каталог

Чтобы добавить пользователей для службы совместной работы B2B в каталог, выполните следующие действия:

1. Войдите на [портал Azure](https://portal.azure.com) с учетной записью администратора Azure AD.
2. В области навигации выберите **Azure Active Directory**.
3. В разделе **Управление** выберите **Пользователи**.
4. Затем выберите **Новый гостевой пользователь**.

   ![Снимок экрана с пользовательским интерфейсом, где выделена команда "Новый гостевой пользователь"](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
5. В колонке **Имя пользователя** введите адрес электронной почты внешнего пользователя. При желании допишите приветственное сообщение. Например: 

   ![Снимок экрана с пользовательским интерфейсом, где выделена команда "Новый гостевой пользователь"](./media/add-users-administrator/InviteGuest.png) 

6. Чтобы автоматически отправить приглашение гостевому пользователю, нажмите кнопку **Пригласить**. 
 
После отправки приглашения учетная запись пользователя автоматически добавляется в каталог в качестве гостя.


![Отображение пользователя B2B в качестве типа гостевого пользователя](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Добавление гостевых пользователей в группу
Чтобы вручную добавить пользователей службы совместной работы B2B в группу в качестве администратора Azure AD, выполните следующие действия:

1. Войдите на [портал Azure](https://portal.azure.com) с учетной записью администратора Azure AD.
2. В области навигации выберите **Azure Active Directory**.
3. В разделе **Управление** выберите **Группы**.
4. Выберите группу (или щелкните **Новая группа** для ее создания). Рекомендуется включить в описание группы информацию о том, что группа состоит из гостевых пользователей B2B.
5. Выберите **Элементы**. 
6. Выполните одно из следующих действий.
   - Если пользователь уже существует в каталоге, найдите пользователя B2B. Чтобы добавить пользователя в группу, выберите его, а затем щелкните **Выбрать**.
   - Если гостевой пользователь не существует в каталоге, пригласите его в группу. Для этого введите его адрес электронной почты в поле поиска, введите необязательное личное сообщение, а затем щелкните **Выбрать**. Приглашение автоматически отправляется приглашенному пользователю.
     
     ![Кнопка приглашения для добавления гостей](./media/add-users-administrator/GroupInvite.png)
   
Также для службы совместной работы Azure AD B2B можно использовать динамические группы. Дополнительные сведения см. в статье [Динамические группы и служба совместной работы Azure Active Directory B2B](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Добавление гостевых пользователей в приложение

Чтобы добавить пользователей службы совместной работы B2B в приложение в качестве администратора Azure AD, выполните следующие действия:

1. Войдите на [портал Azure](https://portal.azure.com) с учетной записью администратора Azure AD.
2. В области навигации выберите **Azure Active Directory**.
3. В разделе **Управление** щелкните **Корпоративные приложения** > **Все приложения**.
4. Выберите приложение, в которое необходимо добавить пользователей.
5. На панели мониторинга приложения выберите **Всего пользователей**, чтобы открыть область **Пользователи и группы**.

    ![Кнопка "Всего пользователей" для открытия области "Пользователи и группы"](./media/add-users-administrator/AppUsersAndGroups.png)

6. Выберите **Добавить пользователя**.
7. В области **Добавление назначения** выберите **Пользователи и группы**.
8. Выполните одно из следующих действий.
   - Если пользователь уже существует в каталоге, найдите пользователя B2B. Чтобы добавить пользователя в приложение, выберите его, щелкните **Выбрать**, а затем **Назначить**.
   - Если пользователь не существует в каталоге, выберите **Пригласить**.
           
       ![Кнопка приглашения для добавления гостей](./media/add-users-administrator/AppInviteUsers.png)
   
      В колонке **Пригласить гостя** введите адрес электронной почты, введите необязательное личное сообщение, а затем нажмите кнопку **Пригласить**. Чтобы добавить пользователя в приложение, щелкните **Выбрать**, а затем **Назначить**. Приглашение автоматически отправляется приглашенному пользователю.

9. Гостевой пользователь отображается в списке приложения **Пользователи и группы** с назначенной ролью **Доступ по умолчанию**. Если вы хотите изменить роль, сделайте следующее.
   - Выберите гостевого пользователя, а затем **Изменить**. 
   - В разделе **Изменение назначений** нажмите кнопку **Выбор роли**, а затем выберите роль, которую хотите назначить выбранному пользователю.
   - Нажмите кнопку **Выбрать**.
   - Щелкните **Назначить**.
 
## <a name="resend-invitations-to-guest-users"></a>Повторная отправка приглашений гостевым пользователям

Если гостевой пользователь еще не активировал свое приглашение, письмо с приглашением можно отправить повторно.

1. Войдите на [портал Azure](https://portal.azure.com) с учетной записью администратора Azure AD.
2. В области навигации выберите **Azure Active Directory**.
3. В разделе **Управление** выберите **Пользователи**.
5. Выберите учетную запись пользователя.
6. В разделе **Управление** выберите **Профиль**.
7. Если пользователь еще не принял приглашения, доступен вариант **Повторно отправить приглашение**. Чтобы повторно отправить приглашение, нажмите эту кнопку.

   ![Вариант повторной отправки приглашения в профиле пользователя](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Начальное приглашение направляло пользователя к конкретному приложению. Если же приглашение пересылается, то ссылка в новом приглашении перенаправит пользователя на панель доступа верхнего уровня.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о том, как обычный пользователь, а не администратор Azure AD, может добавить гостевых пользователей B2B, см. в статье [Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory?](add-users-information-worker.md)
- Дополнительные сведения о сообщениях электронной почты с приглашением см. в статье [Элементы сообщения с приглашением службы совместной работы B2B Azure Active Directory](invitation-email-elements.md).

