---
title: Матрица поддержки для репликации Hyper-V в Azure | Документация Майкрософт
description: Краткое описание поддерживаемых компонентов и требований для репликации Hyper-V в Azure с помощью службы Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 3204329dc7c9efe2b0ba0ae05d17bc93d51620b4
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37923472"
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Матрица поддержки для репликации Hyper-V в Azure


В этой статье перечислены компоненты и параметры, поддерживаемые для аварийного восстановления локальных виртуальных машин Hyper-V в Azure с помощью [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

**Сценарий** | **Дополнительные сведения**
--- | ---
Hyper-V с Virtual Machine Manager | Вы можете выполнять аварийное восстановление в Azure для виртуальных машин, работающих на узлах Hyper-V под управлением структуры System Center Virtual Machine Manager.<br/><br/> Этот сценарий можно развернуть с помощью портала Azure или PowerShell.<br/><br/> Если узлами Hyper-V управляет Virtual Machine Manager, вы можете также выполнять аварийное восстановление на вторичный локальный сайт. Дополнительные сведения об этом сценарии см. в [этом руководстве](tutorial-vmm-to-vmm.md).
Hyper-V без Virtual Machine Manager | Вы можете выполнять аварийное восстановление в Azure для виртуальных машин, работающих на узлах Hyper-V без управления Virtual Machine Manager.<br/><br/> Этот сценарий можно развернуть с помощью портала Azure или PowerShell.


## <a name="on-premises-servers"></a>Локальные серверы

**Сервер** | **Требования** | **Дополнительные сведения**
--- | --- | ---
Hyper-V (без Virtual Machine Manager) | Windows Server 2016 (включая установку основных серверных компонентов), Windows Server 2012 R2 с последними обновлениями | При настройке сайта Hyper-V в службе Site Recovery использование разных узлов, работающих под управлением Windows Server 2016 и 2012 R2, не поддерживается.<br/><br/> Для виртуальных машин, размещенных на узле под управлением Windows Server 2016, восстановление в альтернативное расположение не поддерживается.
Hyper-V (с Virtual Machine Manager) | Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Если используется Virtual Machine Manager, все узлы Windows Server 2016 должны находиться под управлением Virtual Machine Manager 2016.<br/><br/> Сейчас не поддерживаются облака Virtual Machine Manager, в которых сочетаются узлы Hyper-V под управлением Windows Server 2016 и 2012 R2.<br/><br/> Также не поддерживаются среды, в которых существующий сервер VMM 2012 R2 обновляется до версии 2016.


## <a name="replicated-vms"></a>Реплицированные виртуальные машины


В таблице ниже перечислены требования к виртуальной машине. Служба Site Recovery поддерживает все рабочие нагрузки в поддерживаемой операционной системе.

 **Компонент** | **Дополнительные сведения**
--- | ---
Конфигурация виртуальной машины | Виртуальные машины, которые реплицируются в Azure, должны соответствовать [требованиям Azure](#failed-over-azure-vm-requirements).
Операционная система на виртуальной машине | Любая гостевая ОС, поддерживаемая в Azure.<br/><br/> Nano Server Windows Server 2016 не поддерживается.




## <a name="hyper-v-network-configuration"></a>Конфигурация сети Hyper-V

**Компонент** | **Hyper-V с Virtual Machine Manager** | **Hyper-V без Virtual Machine Manager**
--- | --- | ---
Сеть узла: объединение сетевых карт | Yes
Сеть узла: виртуальная локальная сеть | Yes
Сеть узла: IPv4 | Yes
Сеть узла: IPv6 | Нет 
Сеть гостевой виртуальной машины: объединение сетевых карт | Нет 
Сеть гостевой виртуальной машины: IPv4 | Yes
Сеть гостевой виртуальной машины: IPv6 | Нет 
Сеть гостевой виртуальной машины: статический IP-адрес (Windows) | Yes
Сеть гостевой виртуальной машины: статический IP-адрес (Linux) | Нет 
Сеть гостевой виртуальной машины: несколько сетевых карт | Yes



## <a name="azure-vm-network-configuration-after-failover"></a>Конфигурация сети виртуальных машин Azure (после отработки отказа)

**Компонент** | **Hyper-V с Virtual Machine Manager** | **Hyper-V без Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Yes | Yes
Внутренний балансировщик нагрузки | Yes | Yes
Внешний балансировщик нагрузки | Yes | Yes
Azure Traffic Manager | Yes | Yes
Несколько сетевых адаптеров | Yes | Yes
Зарезервированный IP-адрес | Yes | Yes
IPv4 | Yes | Yes
Сохранение исходного IP-адреса | Yes | Yes
Конечные точки служб виртуальной сети<br/> (без брандмауэров службы хранилища Azure) | Yes | Yes
Ускорение работы в сети | Нет  | Нет 


## <a name="hyper-v-host-storage"></a>Хранилище узла Hyper-V

**Хранилище** | **Hyper-V с Virtual Machine Manager** | **Hyper-V без Virtual Machine Manager**
--- | --- | --- | ---
NFS | Нет данных | Нет данных
SMB 3.0 | Yes | Yes
Сеть SAN (iSCSI) | Yes | Yes
Многопутевое (Multipath I/O). Протестировано с использованием:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM для CLARiiON | Yes | Yes

## <a name="hyper-v-vm-guest-storage"></a>Гостевое хранилище виртуальной машины Hyper-V

**Хранилище** | **Hyper-V с Virtual Machine Manager** | **Hyper-V без Virtual Machine Manager**
--- | --- | ---
VMDK | Нет данных | Нет данных
VHD (VHDX) | Yes | Yes
Виртуальная машина поколения 2 | Yes | Yes
UEFI (EFI)| Yes | Yes
Общий диск кластера | Нет  | Нет 
Зашифрованный диск | Нет  | Нет 
NFS | Нет данных | Нет данных
SMB 3.0 | Нет  | Нет 
RDM | Нет данных | Нет данных
Диски размером более 1 ТБ | Да, до 4095 ГБ | Да, до 4095 ГБ
Диск: логический и физический сектор размером 4 КБ | Не поддерживается: поколение 1 или 2 | Не поддерживается: поколение 1 или 2
Диск: логический сектор размером 4 КБ и физический сектор размером 512 байт | Yes |  Yes
Том с чередующимся диском размером более 1 ТБ<br/><br/> Управление логическими томами (LVM) | Yes | Yes
Дисковые пространства | Yes | Yes
"Горячее" добавление или удаление диска | Нет  | Нет 
Исключение диска | Yes | Yes
Многопутевой (MPIO) | Yes | Yes

## <a name="azure-storage"></a>Хранилище Azure

**Компонент** | **Hyper-V с Virtual Machine Manager** | **Hyper-V без Virtual Machine Manager**
--- | --- | ---
Локально избыточное хранилище | Yes | Yes
Геоизбыточное хранилище | Yes | Yes
Геоизбыточное хранилище с доступом для чтения | Yes | Yes
"Холодное" хранилище | Нет  | Нет 
"Горячее" хранилище| Нет  | Нет 
Blob-блоки | Нет  | Нет 
Шифрование неактивных данных (SSE)| Yes | Yes
Хранилище уровня "Премиум" | Yes | Yes
Служба импорта и экспорта | Нет  | Нет 
Брандмауэры службы хранилища Azure для виртуальных сетей, настроенные в целевой учетной записи хранения или кэширования (используемой для хранения данных репликации) | Нет  | Нет 


## <a name="azure-compute-features"></a>Вычислительные компоненты Azure

**Компонент** | **Hyper-V с Virtual Machine Manager** | **Hyper-V без Virtual Machine Manager**
--- | --- | ---
Группы доступности | Yes | Yes
Концентратор | Yes | Yes  
Управляемые диски | Да, для отработки отказа.<br/><br/> Восстановление размещения для управляемых дисков не поддерживается. | Да, для отработки отказа.<br/><br/> Восстановление размещения для управляемых дисков не поддерживается.

## <a name="azure-vm-requirements"></a>Требования для виртуальных машин Azure

Локальные виртуальные машины, которые вы реплицируете в Azure, должны соответствовать требованиям Azure, приведенным в этой таблице.

**Компонент** | **Требования** | **Дополнительные сведения**
--- | --- | ---
Операционная система на виртуальной машине | Служба Site Recovery поддерживает все операционные системы, [поддерживаемые Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Если не поддерживается, проверка на соответствие обязательным требованиям завершается ошибкой.
Архитектура операционной системы на виртуальной машине | 64-разрядная | Если не поддерживается, проверка на соответствие обязательным требованиям завершается ошибкой.
Размер диска операционной системы | До 2048 ГБ для виртуальных машин поколения 1.<br/><br/> До 300 ГБ для виртуальных машин поколения 2  | Если не поддерживается, проверка на соответствие обязательным требованиям завершается ошибкой.
Число дисков операционной системы | 1 | Если не поддерживается, проверка на соответствие обязательным требованиям завершается ошибкой.
Число дисков с данными | 16 ГБ или меньше  | Если не поддерживается, проверка на соответствие обязательным требованиям завершается ошибкой.
Размер виртуального жесткого диска с данными | До 4095 ГБ | Если не поддерживается, проверка на соответствие обязательным требованиям завершается ошибкой.
Сетевые адаптеры | Поддерживаются несколько адаптеров |
Общий виртуальный жесткий диск | Не поддерживается | Если не поддерживается, проверка на соответствие обязательным требованиям завершается ошибкой.
Диск FC | Не поддерживается | Если не поддерживается, проверка на соответствие обязательным требованиям завершается ошибкой.
Формат жесткого диска | VHD  <br/><br/> VHDX | Служба Site Recovery автоматически преобразует формат VHDX в VHD при отработке отказа в Azure. При восстановлении до локальной системы виртуальные машины продолжают использовать формат VHDX.
BitLocker | Не поддерживается | Прежде чем включать репликацию для виртуальной машины, необходимо отключить BitLocker.
имя виртуальной машины; | От 1 до 63 символов, при этом допустимы только буквы, цифры и дефисы Имя виртуальной машины должно начинаться и заканчиваться буквой или цифрой. | Обновите значение в свойствах виртуальной машины в службе Site Recovery.
Тип виртуальной машины | Поколение 1<br/><br/> Поколение 2 — Windows | Поддерживаются виртуальные машины второго поколения с диском ОС типа "Базовый" (включая один или два тома данных в формате VHDX) и объемом менее 300 ГБ.<br></br>Виртуальные машины Linux второго поколения не поддерживаются. [Узнайте больше](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Действия хранилища служб восстановления

**Действие** |  **Hyper-V с Virtual Machine Manager** | **Hyper-V без Virtual Machine Manager**
--- | --- | ---
Перемещение хранилища между группами ресурсов<br/><br/> В пределах подписок и между ними | Нет  | Нет 
Перемещение хранилищ, сетей, виртуальных машин Azure между группами ресурсов<br/><br/> В пределах подписок и между ними | Нет  | Нет 


## <a name="provider-and-agent"></a>Поставщик и агент

Чтобы обеспечить совместимость развертывания с параметрами, описанными в этой статье, используйте последние версии поставщика и агента.

**Имя** | **Описание** | **Дополнительные сведения**
--- | --- | --- | --- | ---
Поставщик Azure Site Recovery | Координирует взаимодействие между локальными серверами и Azure <br/><br/> Hyper-V (с Virtual Machine Manager): устанавливается на серверах Virtual Machine Manager<br/><br/> Hyper-V (без Virtual Machine Manager): устанавливается на узлах Hyper-V| Последняя версия: 5.1.2700.1 (доступна на портале Azure)<br/><br/> [Новейшие функции и последние исправления](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Агент служб восстановления Microsoft Azure | Координирует репликацию между виртуальными машинами Hyper-V и Azure.<br/><br/> Устанавливается на локальных серверах Hyper-V (с или без Virtual Machine Manager). | Последняя версия агента, доступная на портале






## <a name="next-steps"></a>Дополнительная информация
Узнайте, как [подготовить Azure](tutorial-prepare-azure.md) для аварийного восстановления локальных виртуальных машин Hyper-V.
