---
title: Создание FQDN для виртуальной машины Windows на портале Azure | Документация Майкрософт
description: Узнайте, как создать полное доменное имя (FQDN) для виртуальной машины на основе Resource Manager на портале Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79b3e3ba01076dcfe5820fb832fa1839398c6c04
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597392"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Создание полного доменного имени на портале Azure для виртуальной машины Windows

При создании виртуальной машины на [портале Azure](https://portal.azure.com) для нее автоматически создается ресурс общедоступного IP-адреса. Этот IP-адрес используется для удаленного доступа к данной виртуальной машине. Несмотря на то что портал не создает [полное доменное имя](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), его можно создать после создания виртуальной машины. В этой статье показан процесс создания DNS-имени или полного доменного имени.

## <a name="create-a-fqdn"></a>Создание полного доменного имени
Для работы с руководством требуется виртуальная машина. При необходимости ее можно создать [на портале](quick-create-portal.md) или с помощью [Azure PowerShell](quick-create-powershell.md). Когда виртуальная машина будет готова, выполните следующие действия:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Теперь вы можете удаленно подключиться к виртуальной машине с помощью DNS-имени, например, используя протокол удаленного рабочего стола (RDP).

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда у виртуальной машины имеется общедоступный IP-адрес и DNS-имя, можно развернуть общие программные платформы или службы, такие как IIS, SQL или SharePoint.

Изучите дополнительные сведения об [использовании Resource Manager](../../azure-resource-manager/resource-group-overview.md), чтобы получить советы по созданию развертываний Azure.

