---
title: Руководство по доверительному интернет-соединению с Azure
description: Руководство по доверительному интернет-соединению со службами Azure и SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: 9d71efa35713500911c67d1df15612b64c8e97da
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990835"
---
# <a name="trusted-internet-connection-guidance"></a>Руководство по доверительному интернет-соединению

## <a name="background"></a>Фоновый

Цель инициативы по доверительному интернет-соединению (TIC) заключается в оптимизации и стандартизации безопасности отдельных внешних сетевых подключений, используемых в настоящее время на федеральном уровне. Политика описана в [меморандуме M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf) Административно-бюджетного управления (OMB, Office of Management and Budgets).

В ноябре 2007 г. управление OMB разработало программу TIC для повышения безопасности периметра федеральной сети и улучшения функций реагирования на инциденты. Программа TIC предназначена для выполнения сетевого анализа всего входящего и исходящего трафика государственных организаций в целях выявления определенных сигнатур и данных на основе шаблонов и обнаружения аномального поведения, например действий ботнета. Учреждениям было поручено консолидировать свои внешние сетевые подключения и перенаправлять весь трафик через устройства по обнаружению и предотвращению вторжений (известные как EINSTEIN), которые размещались на ограниченном количестве сетевых конечных точек (называемых доверенными интернет-соединениями).

Проще говоря, программа TIC призвана дать ответы на следующие вопросы, интересующие учреждения:
- Кто находится в моей сети (авторизованный или неавторизованный)?
- Когда и по какой причине осуществляется доступ в мою сеть?
- Доступ к каким ресурсам выполняется?

В настоящее время все внешние подключения учреждения должны перенаправляться через доверительное интернет-соединение, утвержденное управлением OMB. Федеральные службы должны участвовать в программе TIC как поставщики доступа TIC (TICAP) либо им следует заключить контракты с одним из основных поставщиков услуг Интернета уровня 1, называемых поставщиками MTIPS (Managed Trusted Internet Protocol Service).  В состав TIC входят обязательные и крайне важные возможности, реализуемые в настоящее время учреждениями и поставщиками MTIPS. В текущей версии TIC устройства обнаружения вторжений EINSTEIN версии 2 и устройства ускоренного (3A) предотвращения вторжений EINSTEIN версии 3 развертываются в каждом TICAP и MTIPS, а учреждение заключает с Министерством национальной безопасности (Department of Homeland Security, DHS) меморандум о договоренности, предусматривающий развертывание возможностей EINSTEIN в федеральных системах.

В рамках своей ответственности за защиту сети государственных организаций министерству требуются каналы необработанных данных для корреляции инцидентов в федеральном учреждении и выполнения анализа с помощью специализированных инструментов. Маршрутизаторы министерства позволяют собирать IP-трафик при его поступлении в интерфейс и выходе из него. Путем анализа чистых данных потока администратор сети определяет источник и назначение трафика, класс службы и т. д. Чистые данные потока считаются данными без содержимого (например, заголовок, исходный IP-адрес, IP-адрес назначения и т. д.), на основе которых министерство получает информацию типа "кто что делал и в течение какого времени".

В рамках инициативы также действуют политики безопасности, рекомендации и платформы, которые предполагают наличие локальной инфраструктуры. Так как государственные учреждения переходят на облачные службы в целях сокращения затрат, повышения эффективности эксплуатации и внедрения инноваций, в некоторых случаях требования к реализации TIC снижают пропускную способность сети и ограничивают скорость и гибкость доступа сотрудников государственных организаций к данным на основе облака.

В этой статье описывается, как государственные учреждения могут использовать Microsoft Azure для государственных организаций для обеспечения соответствия требованиям политики TIC в службах IaaS и PaaS.

## <a name="summary-of-azure-networking-options"></a>Обзор вариантов сетевого подключения Azure

Доступно три основных варианта подключения к службам Azure.

1. Прямое подключение к Интернету. Подключение к службам Azure выполняется напрямую с помощью открытого подключения к Интернету. Среда и само подключение являются открытыми. Для обеспечения конфиденциальности применяется шифрование на уровне приложения и транспорта. Пропускная способность ограничивается подключением узла к Интернету, поэтому для обеспечения устойчивости можно использовать несколько действующих поставщиков.
1. Виртуальная частная сеть. Подключение к виртуальной сети Azure выполняется в частном порядке с помощью VPN-шлюза.
Среда является общедоступной, так как она проходит через стандартное подключение к Интернету, но для обеспечения конфиденциальности соединение зашифровано в туннель. Пропускная способность ограничивается в зависимости от VPN-устройств и выбранной конфигурации. Как правило, для подключений "точка-сеть" в Azure действует ограничение в 100 Мбит/с, а для подключений "сеть-сеть" действует ограничение 1,25 Гбит/с.
1. Microsoft ExpressRoute. ExpressRoute — это прямое подключение к службам Майкрософт. Поскольку подключение осуществляется через Fibre Channel, в зависимости от используемой конфигурации оно может быть общедоступным или частным. К пропускной способности обычно применяется ограничение в максимум 10 Гбит/с.

Существует несколько способов удовлетворения требований, приведенных в приложении (Appendix H) о факторах облачной среды, которое является дополнением к документу "Trusted Internet Connections (TIC) Reference Architecture Document, Version 2.0" Министерства национальной безопасности. В этом документе рекомендации TIC для DHS будут называться TIC 2.0.

Чтобы включить подключение от отдела или учреждения к Azure или Office 365 без маршрутизации трафика через TIC отдела или учреждения, отделу или учреждению необходимо использовать зашифрованный туннель и (или) выделенное подключение к поставщику облачных служб (CSP). Службы CSP гарантируют, что подключение к облачным ресурсам отдела или учреждения и прямой доступ сотрудников учреждения не осуществляются через общедоступное интернет-подключение.

Office 365 соответствует приведенным в приложении к документу TIC 2.0 требованиям, та как использует Express Route с включенным [пирингом Майкрософт](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) или интернет-подключение, которое шифрует весь трафик с помощью TLS 1.2.  Конечные пользователи в сети отдела или учреждения могут подключаться по сети агентства, а инфраструктура TIC — через Интернет. Все удаленные интернет-подключения к Office 365 блокируются и маршрутизируются через учреждение. Отдел или учреждение может также подключаться к Office 365 через подключение Express Route с пирингом Майкрософт, который представляет собой включенный тип общедоступного пиринга.  

Только для Azure: варианты 2 (VPN) и 3 (ExpressRoute) могут соответствовать этим требованиям при использовании вместе со службами, которые ограничивают доступ к Интернету.

![Схема TIC Microsoft](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Каким образом предложения инфраструктуры как услуги Azure могут помочь обеспечить соответствие TIC

Соблюдение политики TIC при использовании IaaS является относительно простой задачей, так как клиенты Azure управляют собственной маршрутизацией виртуальной сети.

Главное требование к обеспечению соответствия эталонной архитектуре TIC заключается в том, чтобы виртуальная сеть стала частным расширением сети отдела или учреждения. Чтобы сеть стала _частным_ расширением, политика разрешает передачу трафика за пределы сети только через локальное сетевое подключение TIC. Этот процесс известен как "принудительное туннелирование", который при использовании для соответствия требованиям TIC представляет собой процесс маршрутизации всего трафика из любой системы в среде CSP через локальный шлюз сети организации в Интернет через TIC.

Соответствие требованиям TIC для IaaS Azure можно разделить на два основных этапа.

1. Параметр Configuration
1. Аудит

### <a name="azure-iaas-tic-compliance-configuration"></a>Конфигурация соответствия требованиям TIC для IaaS Azure

Чтобы настроить соответствующую TIC архитектуру в Azure, сначала необходимо запретить прямой интернет-доступ к виртуальной сети, а затем перенаправлять интернет-трафик через локальную сеть.

#### <a name="prevent-direct-internet-access"></a>Запрет прямого доступа к Интернету

Сетевое подключение к IaaS Azure выполняется через виртуальные сети, состоящие из подсетей, с которыми связаны сетевые карты (NIC) виртуальных машин.

В простейшем сценарии поддержки соответствия TIC следует запретить виртуальной машине или коллекции виртуальных машин подключаться к любым внешним ресурсам. Отключение от внешних сетей можно выполнить с помощью групп безопасности сети (NSG), которые можно использовать для управления трафиком к одной или нескольким сетевым картам или подсетям в виртуальной сети. Группа безопасности сети содержит правила контроля доступа, которые разрешают или запрещают трафик в зависимости от направления трафика, протокола, исходного и целевого адреса и порта. Эти правила можно изменять в любое время, при этом изменения применяются ко всем связанным экземплярам.  Дополнительные сведения о том, как создать группу безопасности сети, см. в [этой статье](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-on-premises-network"></a>Перенаправление интернет-трафика через локальную сеть

Azure автоматически создает системные маршруты и назначает их каждой подсети в виртуальной сети. Вы не можете создавать и удалять системные маршруты, но можете переопределить некоторые из них с помощью настраиваемых маршрутов. Azure создает системные маршруты по умолчанию для каждой подсети и добавляет необязательные маршруты по умолчанию к конкретным подсетям или к каждой подсети при использовании определенных возможностей Azure. Эта маршрутизация гарантирует, что трафик, поступающий в виртуальную сеть, остается в пределах виртуальной сети, назначенные IANA частные адресные пространства, например 10.0.0.0/8, удаляются (если только они не входят в адресное пространство виртуальной сети) и в крайнем случае выполняется маршрутизация 0.0.0.0/0 в конечную интернет-точку виртуальной сети.

![Принудительное туннелирование TIC](media/tic-diagram-c.png)

Чтобы весь трафик проходил через TIC отдела или учреждения, весь трафик, выходящий из виртуальной сети должен перенаправляться через локальное подключение.  Создать настраиваемые маршруты можно путем создания определяемых пользователем маршрутов или обмена маршрутами протокола BGP между локальным сетевым шлюзом и шлюзом виртуальной сети Azure. Дополнительные сведения об определяемых пользователем маршрутах см. в https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined. Дополнительные сведения о протоколе BGP см. в https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol.

#### <a name="user-defined-routes"></a>Определяемые пользователем маршруты

При использовании шлюза виртуальной сети на основе маршрутов принудительное туннелирование в Azure можно выполнить путем добавления параметра определяемого пользователем маршрута для перенаправления трафика 0.0.0.0/0 к "следующему прыжку" шлюза виртуальной сети. В Azure определяемые пользователем маршруты имеют более высокий приоритет, чем определяемые системой маршруты, поэтому весь трафик вне виртуальной сети отправляется на шлюз виртуальной сети, который затем можно направить в локальную сеть. Определяемый пользователем маршрут должен быть связан со всеми существующими или недавно созданными подсетями во всех виртуальных сетях в вашей среде Azure.

![Определяемые пользователем маршруты и TIC](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>Протокол BGP

Если вы используете ExpressRoute или шлюз виртуальной сети с поддержкой протокола BGP, BGP является предпочтительным методом для объявления маршрутов. При наличии маршрута 0.0.0.0/0, объявленного с помощью протокола BGP, шлюзы виртуальной сети с поддержкой ExpressRoute и BGP гарантируют, что этот маршрут по умолчанию применяется ко всем подсетям в виртуальных сетях.

### <a name="azure-iaas-tic-compliance-auditing"></a>Аудит соответствия TIC для IaaS Azure

В Azure доступно несколько способов аудита соответствия требованиям TIC.

#### <a name="effective-routes"></a>Фактические маршруты

Чтобы проверить, что маршрут по умолчанию был распространен, взгляните на колонку "Действующие маршруты" для конкретной виртуальной машины, конкретной сетевой карты или определяемой пользователем таблицы маршрутов. Это можно сделать на портале Azure, как описано в статье https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal, или с помощью PowerShell, как описано в статье https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell. В колонке "Действующие маршруты" отображаются соответствующие определяемые пользователем маршруты, маршруты, объявленные BGP, и системные маршруты, которые применяются к соответствующей сущности, как показано ниже.

![Снимок экрана с маршрутами](media/tic-screen-1.png)

**Примечание**. Просмотреть действующие маршруты для сетевой карты можно только в случае, если она связана с работающей виртуальной машиной.

#### <a name="network-watcher"></a>Наблюдатель за сетями

Наблюдатель за сетями Azure предлагает несколько средств для аудита соответствия требованиям TIC.  Дополнительные сведения о Наблюдателе за сетями см. в https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview.

##### <a name="network-security-groups-flow-logs"></a>Журналы потоков для группы безопасности сети 

Наблюдатель за сетями Azure позволяет собирать данные журналов сетевых потоков с метаданными, связанными с IP-трафиком. Наряду с другими данными журналы сетевых потоков содержат исходные и конечные адреса целевых объектов. Эти журналы вместе с журналами из шлюза виртуальной сети, локальных граничных устройств и (или) TIC позволят отслеживать, что весь трафик действительно перенаправляется в локальную сеть. 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Каким образом предложения платформы как услуги Azure могут помочь обеспечить соответствие TIC

Службы Azure PaaS, такие как служба хранилища Azure, доступны по URL-адресу в Интернете. Любой пользователь с утвержденными учетными данными может получить доступ к ресурсу, например учетной записи хранения, из любого места без обхода TIC. По этой причине многие клиенты из числа государственных организаций делают неправильный вывод о том, что службы Azure PaaS не соответствуют политикам TIC. На самом деле многие службы Azure PaaS могут соответствовать политике TIC при использовании той же архитектуры, что и в описанной выше соответствующей TIC среде IaaS, если они могут быть присоединены к виртуальной сети. Интеграция служб Azure PaaS с виртуальной сетью Azure обеспечивает закрытый доступ к службе из этой виртуальной сети и позволяет применять настраиваемую маршрутизацию для 0.0.0.0/0 через определяемые пользователем маршруты или протокол BGP, гарантируя, что весь интернет-трафик перенаправляется через локальное подключение в обход TIC.  Некоторые службы Azure можно интегрировать в виртуальные сети с помощью следующих шаблонов.

- **Развертывание выделенного экземпляра службы.** Все больше и больше служб PaaS можно развертывать в качестве выделенных экземпляров с конечными точками, присоединенными к виртуальной сети. Например, среду службы приложений (ASE) можно развернуть в изолированном режиме, а ее сетевую конечную точку ограничить виртуальной сетью. Затем в этой среде можно разместить многие службы Azure PaaS, такие как веб-приложения, API и функции.
- **Конечные точки службы виртуальной сети.** Все больше служб PaaS предлагают возможность перемещения конечной точки на частный, а не общедоступный IP-адрес виртуальной сети.

Ниже перечислены службы, которые поддерживают развертывание выделенных экземпляров в виртуальной сети или на конечных точках службы по состоянию на май 2018 г.: * (доступно для Azure для коммерческих организаций, ожидается доступность для Azure для государственных организаций).

### <a name="service-endpoints"></a>Конечные точки службы

|Service                   |Status            |
|--------------------------|------------------|
|Azure KeyVault            | Закрытая предварительная версия  |
|База данных Cosmos                 | Закрытая предварительная версия  |
|Удостоверение                  | Закрытая предварительная версия  |
|Azure Data Lake;           | Закрытая предварительная версия  |
|Sql Postgress/Mysql       | Закрытая предварительная версия  |
|Хранилище данных SQL Azure  | Общедоступная предварительная версия   |
|Azure SQL                 | GA               |
|Служба хранилища                   | GA               |

### <a name="vnet-injection"></a>Внедрение виртуальной сети

|Service                            |Status            |
|-----------------------------------|------------------|
|Управляемый экземпляр SQL               | Общедоступная предварительная версия   |
|Служба контейнеров Azure (AKS)       | Общедоступная предварительная версия   |
|Service Fabric                     | GA               |
|Управление API                     | GA               |
|Azure Active Directory             | GA               |
|Пакетная служба Azure                        | GA               |
|ASE                                | GA               |
|Redis                              | GA               |
|HDI                                | GA               |
|Вычисление масштабируемого набора виртуальных машин  | GA               |
|Вычисление облачной службы              | GA               |

### <a name="vnet-integration-details"></a>Сведения об интеграции с виртуальной сетью

На приведенной ниже диаграмме показан общий сетевой поток для доступа к службам PaaS с использованием внедрения виртуальной сети и туннелирования службы виртуальной сети.  Дополнительные сведения о шлюзах сетевой службы, безопасности виртуальных сетей и тегах служб можно найти здесь https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags.

![Варианты подключений PaaS для TIC](media/tic-diagram-e.png)

1. Частное подключение к Azure с использованием ExpressRoute. Частный пиринг ExpressRoute с принудительным туннелированием используется для перенаправления всего трафика виртуальной сети клиента, передаваемого через ExpressRoute, обратно в локальную сеть. Пиринг Майкрософт не требуется.
2. VPN-шлюз Azure, используемый в сочетании с пирингом Майкрософт ExpressRoute, можно применять для наложения сквозного шифрования IPSec между клиентской виртуальной сетью и границей локальной сети. 
3. Сетевое подключение к виртуальной сети клиента контролируется с помощью групп безопасности сети (NSG), что позволяет разрешать или запрещать трафик на основе IP-адреса, порта и протокола.
4. Виртуальная сеть клиента распространяется на службу PaaS путем создания конечной точки службы для службы клиента.
5. Конечная точка службы PaaS защищена тем, что по умолчанию запрещает все подключения и разрешает доступ только из указанных подсетей в виртуальной сети клиента.  В число запрещенных по умолчанию входят подключения из Интернета.
6. Любые другие службы Azure, которым требуется доступ к ресурсам в виртуальной сети клиента, должны соответствовать следующим условиям.  
  a. Развернуты непосредственно в виртуальной сети.  
  b. Выборочно разрешены в соответствии с рекомендациями из соответствующей службы Azure.

#### <a name="option-1-dedicated-instance-vnet-injection"></a>Вариант 1. Выделенный экземпляр "Внедрение виртуальной сети"

С помощью внедрения виртуальной сети клиенты могут выборочно развертывать выделенные экземпляры конкретной службы Azure, например HDInsight, в своей собственной виртуальной сети. Экземпляры служб развертываются в выделенной подсети в виртуальной сети клиента. Внедрение виртуальной сети позволяет обращаться к ресурсам службы через маршрутизируемые адреса без доступа в Интернет.  Локальные экземпляры могут получать доступ к этим экземплярам службы с помощью адресного пространства виртуальной сети напрямую через подключение ExpressRoute или VPN-подключение типа "сеть-сеть", а не открывать брандмауэры для общедоступного адресного пространства в Интернете. Если выделенный экземпляр подключен к конечной точке, можно использовать те же стратегии, которые работали для соответствия TIC для IaaS. При этом маршрутизация по умолчанию обеспечивает перенаправление интернет-трафика на шлюз виртуальной сети, настроенный для локального подключения. Входящий и исходящий доступ можно контролировать с помощью групп безопасности сети для данной подсети.

![Обзорная схема внедрения виртуальной сети](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>Вариант 2. Конечные точки службы виртуальной сети 

Целый ряд мультитенантных служб Azure предлагает функционал конечной точки — альтернативный способ интеграции с виртуальными сетями Azure. Конечные точки службы виртуальной сети расширяют пространство IP-адресов виртуальной сети и возможности идентификации вашей виртуальной сети в службах Azure через прямое соединение.  Трафик, поступающий из виртуальной сети в службу Azure, всегда остается в магистральной сети Azure. После включения конечной точки для службы подключения к службе можно ограничить этой виртуальной сетью, применив соответствующие политики. Служба Azure выполняет проверки доступа на платформе, и доступ к заблокированным ресурсам предоставляется, только если запрос поступает из разрешенной виртуальной сети или подсети и (или) для идентификации локального трафика используются два IP-адреса (при применении ExpressRoute). Это позволяет эффективно предотвращать прямой выход входящего или исходящего трафика из службы PaaS.

![Обзорная схема конечных точек служб](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>Использование собственных облачных средств для сетевой ситуационной информированности

Azure предоставляет собственные облачные средства, которые помогут поддерживать информированность о потоках трафика в сети. Они не должны соответствовать политике TIC, но могут значительно улучшить возможности безопасности.

### <a name="azure-policy"></a>Политика Azure

Политика Azure (https://azure.microsoft.com/services/azure-policy/)) — это служба Azure, которая предоставляет организации лучшие возможности аудита и реализации инициатив по соответствию требованиям.  Сейчас политика Azure доступна в общедоступной предварительной версии в коммерческих облачных средах Azure и пока недоступна в Microsoft Azure. Клиенты, поддерживающие TIC, могут приступить к планированию тестированию правил политики для обеспечения соответствия в будущем. Политика Azure предназначена для использования на уровне подписки и предоставляет централизованный интерфейс для управления инициативами и определениями политик, для аудита и последующего исполнения его результатов, а также для управления исключениями. Помимо целого ряда встроенных определений политики Azure, администраторы могут определять собственные определения на основе простых шаблонов JSON. Многим клиентам корпорация Майкрософт рекомендует уделять первоочередное внимание аудиту, а не реализации.

Следующие примеры политик могут быть полезны в сценариях соответствия TIC.

|Политика  |Пример сценария  |Запуск шаблона  |
|---------|---------|---------|
|Применение определяемой пользователем таблицы маршрутов |     Маршрут по умолчанию на всех точках виртуальных сетей ведет к утвержденному шлюзу виртуальной сети для маршрутизации в локальную сеть | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|Проверка отключения службы "Наблюдатель за сетями" для региона  | Включение Наблюдателя за сетями для всех используемых регионов  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|NSG x для каждой подсети  | Применение группы безопасности сети (или набора утвержденных групп безопасности сети) с заблокированным интернет-трафиком ко всем подсетям в каждой виртуальной сети | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|Конкретная группа безопасности сети на каждой сетевой карте | Применение группы безопасности сети с заблокированным интернет-трафиком ко всем сетевым картам на всех виртуальных машинах. | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|Использование утвержденной виртуальной сети для сетевых интерфейсов виртуальной машины  | Размещение всех сетевых карт в утвержденной виртуальной сети | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|Allowed locations; | Развертывание всех ресурсов в регионах с соответствующими виртуальными сетями и конфигурацией Наблюдателя за сетями  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|Недопустимые типы ресурсов, такие как PublicIPs  | Запрет развертывания типов ресурсов, которые не имеют плана соответствия. К примеру, с помощью этой политики можно запретить развертывание ресурсов общедоступного IP-адреса. Несмотря на то, что правила группы безопасности сети можно использовать для эффективной блокировки входящего интернет-трафика, запрет на использование общедоступных IP-адресов дополнительно снижает вероятность атак.    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>[Аналитика трафика](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/) Azure

Служба аналитики трафика Azure Наблюдателя за сетями использует данные журнала потока и другие журналы для предоставления общего обзора сетевого трафика. Эти данные полезны для аудита соответствия TIC и определения проблемных мест. На панелях мониторинга высокого уровня можно быстро увидеть, какие виртуальные машины взаимодействуют с Интернетом, и затем получить конкретный список для маршрутизации TIC.

![Снимок экрана аналитики трафика](media/tic-traffic-analytics-1.png)

С помощью географической карты можно быстро установить вероятные физические назначения интернет-трафика для виртуальных машин и в результате выявить и приоритизировать подозрительные расположениях или изменения шаблона.

![Снимок экрана аналитики трафика](media/tic-traffic-analytics-2.png)

Карту топологии сети можно использовать для быстрого обзора существующих виртуальных сетей.

![Снимок экрана аналитики трафика](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Следующий прыжок Наблюдателя за сетями Azure

В сетях в регионах, отслеживаемых Наблюдателем за сетями, можно проводить тесты на следующий прыжок для простого доступа на основе портала к типу в источнике и назначении для примера сетевого потока, для которого Наблюдатель за сетями разрешит назначение "следующий прыжок". Их можно выполнять для виртуальных машин и примеров интернет-адресов, чтобы следующий прыжок действительно приходился на шлюз виртуальной сети.

![Следующий прыжок Наблюдателя за сетями](media/tic-network-watcher.png)

## <a name="conclusions"></a>Заключение

Доступ к Microsoft Azure, Office 365 и Dynamics 365 можно легко настроить в соответствии с рекомендациями в приложении (Appendix H) к документу TIC 2.0, созданному и определенному в мае 2018 года.  Корпорации Майкрософт известно, что это руководство может быть изменено, поэтому она постарается помочь клиентам своевременно выполнять требования, которые будут приводиться в новом выпущенном документе.

## <a name="appendix-tic-patterns-for-common-workloads"></a>Приложение. Шаблоны TIC для распространенных рабочих нагрузок

| Категория | Рабочая нагрузка | IaaS | Выделенная служба PaaS или внедрение виртуальной сети  | Конечные точки службы  |
|---------|---------|---------|---------|--------|
| Службы вычислений | Виртуальные машины Linux | Yes | | |
| Службы вычислений | Виртуальные машины Windows | Yes | | |
| Службы вычислений | Масштабируемые наборы виртуальных машин Microsoft Azure | Yes | | |
| Службы вычислений | Функции Azure | | через среду службы приложений (ASE) | |
| Интернет и мобильные устройства | Внутреннее веб-приложение | | через среду службы приложений (ASE) | |
| Интернет и мобильные устройства | Внутреннее мобильное приложение | | через среду службы приложений (ASE) | |
| Интернет и мобильные устройства | Приложения API | | через среду службы приложений (ASE) | |
| Контейнеры | Служба контейнеров Azure (ACS) | | | Yes |
| Контейнеры | Служба контейнеров Azure (AKS)* | | | Yes |
| База данных | База данных SQL | | Управляемый экземпляр базы данных SQL Azure* | Azure SQL |
| База данных | База данных Azure для MySQL | | | Yes |
| База данных | База данных Azure для PostgreSQL | | | Yes |
| База данных | Хранилище данных SQL | | | Yes |
| База данных | Azure Cosmos DB | | | Yes |
| База данных | кэш Redis; | | Yes | |
| Служба хранилища | BLOB-объекты | Yes | | |
| Служба хранилища | Файлы | Yes | | |
| Служба хранилища | Очереди | Yes | | |
| Служба хранилища | Таблицы | Yes | | |
| Служба хранилища | диски; | Yes | | |

*: общедоступная предварительная версия в Azure для государственных организаций по состоянию на май 2018 г.  
**: закрытая предварительная версия в Azure для государственных организаций по состоянию на май 2018 г.
