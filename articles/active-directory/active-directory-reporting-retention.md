---
title: Политики хранения отчетов Azure Active Directory | Документация Майкрософт
description: Политики хранения данных отчета в Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8edce7d1a633148cf361deec0c3d09ef4c16ac9b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225184"
---
# <a name="azure-active-directory-report-retention-policies"></a>Политики хранения отчетов Azure Active Directory


Эта статья содержит ответы на часто задаваемые вопросы о хранении данных для разных отчетов о действиях в Azure Active Directory. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>Вопрос. Как начать сбор данных о действиях?

**Ответ.**

| Выпуск Azure AD | Начало сбора |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Когда регистрироваться для оформления подписки |
| Azure AD уровня "Бесплатный" | При первом открытии [колонки Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) или использовании [API-интерфейсов отчетности](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>Вопрос. Когда данные о действиях становятся доступными на портале Azure?

**Ответ.**

- **Немедленно** — если вы уже работали с отчетами на портале Azure.
- **В течение 2 часов** — если вы не включили функцию создания отчетов на портале Azure.

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>Вопрос. Как начать сбор сигналов системы безопасности?  

**Ответ.** Процесс сбора сигналов системы безопасности начинается, когда вы соглашаетесь использовать центр защиты идентификации. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>Вопрос. Как долго хранятся собранные данные?

**Ответ.**

**Отчеты о действиях**    

| Отчет                 | Azure AD уровня "Бесплатный" | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Аудит каталога        | 7 дней        | 30 дней             | 30 дней             |
| Действия при входе       | Недоступно           | 30 дней             | 30 дней             |
| Использование Azure MFA        | 30 дней       | 30 дней             | 30 дней             |

**Сигналы системы безопасности**

| Отчет         | Azure AD уровня "Бесплатный" | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| пользователи под угрозой;  | 7 дней        | 30 дней             | 90 дней             |
| Вход, представляющий риск | 7 дней        | 30 дней             | 90 дней             |

---
