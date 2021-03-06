---
title: Аудит ролей для ресурсов Azure с помощью управления привилегированными пользователями | Документация Майкрософт
description: Узнайте, как просмотреть сведения обо всех действиях в роли для определенного ресурса.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 2740297337e1de0d041a80c7860324175413c833
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447719"
---
# <a name="audit-resource-roles-for-azure-resources-by-using-privileged-identity-management"></a>Аудит ролей для ресурсов Azure с помощью управления привилегированными пользователями 

Аудит ресурсов дает вам представление о всей активности роли для этого ресурса. Вы можете отфильтровать информацию с помощью предопределенной даты или настраиваемого диапазона.
![Сведения о фильтре](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Аудит ресурсов также обеспечивает быстрый доступ для просмотра сведений о действиях пользователя. В разделе **Тип аудита** выберите **Активировать**. Выберите **(действие)**, чтобы просмотреть сведения о действиях пользователя для ресурсов Azure.
![Сведения о действии](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Дополнительные сведения о действии](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Мой аудит

Параметр "Мой аудит" позволяет получить представление действиях в личной роли пользователя. Вы можете отфильтровать информацию с помощью предопределенной даты или настраиваемого диапазона.
![Действие в личной роли](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Просмотр активации и активность ресурсов Azure

Чтобы выяснить, какие действия выполняет конкретный пользователь с разными ресурсами, вы можете просмотреть активность ресурсов Azure, связанную с данным периодом активации. Начните с выбора пользователя из представления **Участники** или списка участников в определенной роли. В результате отображается графическое представление действий пользователя в ресурсах Azure по датам. В нем также представлены недавние активации роли за тот же период времени.

![Сведения о пользователе](media/azure-pim-resource-rbac/rbac-user-details.png)

При выборе активации определенной роли будут показаны детали активации роли и соответствующая активность с ресурсами Azure, которая произошла, когда этот пользователь был активен.

![Выбор активации роли](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

