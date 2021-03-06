---
title: включение файла
description: включение файла
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/10/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c3f727c6154a0364f151d22000d2684c361676a
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037216"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Какая разница между шлюзом Виртуальной сети Azure (VPN-шлюз) и Виртуальной глобальной сетью Azure vpngateway?

Виртуальная глобальная сеть обеспечивает крупномасштабное подключение типа "сайт — сайт" и разрабатывается для пропускной способности, масштабируемости и легкости использования. Автоматическая подготовка и подключение филиалов устройств CPE к Виртуальной глобальной сети Azure (WAN). Эти устройства доступны в растущей экосистеме SD-WAN и у партнеров VPN.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Какие поставщики устройств (партнеры Виртуальной глобальной сети) поддерживаются во время запуска? 

В настоящее время Citrix и Riverbed полностью поддерживаются автоматизированной Виртуальной глобальной сетью. В ближайшие месяцы появятся новые партнеры, в том числе: Nokia Nuage, Palo Alto и Checkpoint. Дополнительные сведения о виртуальных машинах см. в разделе [Virtual WAN Preview](https://aka.ms/virtualwan) (Виртуальная глобальная сеть (предварительный просмотр)).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Нужно ли использовать основное устройство партнера?

Нет. Можно использовать любое VPN-устройство, отвечающее требованиям предварительной версии для поддержки IKEv2 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Как партнеры Виртуальной глобальной сети автоматизируют подключение с помощью Виртуальной глобальной сети Azure?

Программно-определяемые решения обычно управляют своими филиалами устройств с помощью контроллера или центра обеспечения устройства. Контроллер может использовать API интерфейсы Azure для автоматизации подключения к Виртуальной глобальной сети Azure. Дополнительные сведения о том, как это работает, см. в разделе [Configure Virtual WAN automation - for Virtual WAN partners (Preview)](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Настройка автоматизации Виртуальной глобальной сети для партнеров Виртуальной глобальной сети (предварительный просмотр)).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Влияет ли Виртуальная глобальная сеть на существующие функции подключения?   

Никаких изменений в существующих функциях подключения Azure не происходит.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Существуют ли новые ресурсы диспетчера ресурсов для Виртуальной глобальной сети?
  
Да, Виртуальная глобальная сеть представляет новые ресурсы диспетчера ресурсов. Дополнительные сведения см. в разделе [What is Azure Virtual WAN? (Preview)](https://go.microsoft.com/fwlink/p/?LinkId=2004389) (Общие сведения о виртуальной глобальной сети Azure (предварительный просмотр)).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Существуют ли специальные требования для получения предварительной версии? 

Прежде чем настроить Виртуальную глобальную сеть Azure, необходимо зарегистрировать свою подписку для использования предварительной версии. Для регистрации отправьте электронное сообщение с идентификатором подписки по адресу <azurevirtualwan@microsoft.com>. После активации подписки на электронную почту будет отправлено письмо. Работа с Виртуальной глобальной сетью на портале невозможна до тех пор, пока не появится подтверждение о регистрации подписки.

Рекомендации:

* Предварительная версия ограничивается только общедоступными регионами Azure.
* Каждый виртуальный концентратор поддерживает до 100 подключений. Каждое подключение состоит из 2 туннелей, которые находятся в режиме настройки "активный — активный". Туннели завершаются в виртуальном узле Azure vpngateway.
* Рассмотрите возможность использования этой предварительной версии при следующих условиях.
  * Если необходимо развернуть статическую пропускную способность менее чем на 1 Гбит/с на виртуальный концентратор.
  * Если имеется VPN-устройство, которое поддерживает конфигурацию на основе маршрута, и подключение IKEv2 IPsec.
  * Если необходимо изучить использование устройств SD-WAN и эксплуатационные филиалы устройств от партнеров запуска (Riverbed и Citrix).<br>или<br>Если необходимо настроить подключение "филиал — филиал" и "филиал — Azure", которое включает управление конфигурацией локального устройства. (Свои настройки)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Поддерживает ли Виртуальная глобальная сеть Azure глобальный пиринг виртуальных сетей? 

 Нет.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Может ли периферийная зона виртуальных сетей, подключенная к виртуальному центру, обмениваться данными?

Да. Можно сделать пиринговую связь между виртуальными сетями и периферийными зонами, которые подключаются к виртуальному центру. Дополнительные сведения см. в статье [Virtual network peering](../articles/virtual-network/virtual-network-peering-overview.md) (Пиринг между виртуальными сетями).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Можно ли развернуть и использовать избранный модуль виртуальной сети (в модуле виртуальной сети) в Виртуальной глобальной сети Azure?

Да, можно подключить модуль виртуальной сети к Виртуальной глобальной сети Azure, но центру потребуются возможности маршрутизации, которые будут в общедоступной версии. Кроме того, все периферийные зоны, подключенные к модулю виртуальной сети, должны подключатся к виртуальному центру. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Может ли модуль виртуальной сети иметь шлюз?

Нет. Модуль виртуальной сети не может иметь шлюз, если он подключен к виртуальному центру. 

### <a name="is-there-support-for-bgp"></a>Поддерживается ли протокол BGP?

Да, протокол BGP поддерживается. Чтобы гарантировать, что маршруты из модуля виртуальной сети объявляются соответствующим образом, периферийные зоны должны отключить протокол BGP, в случае если они подключены к модулю виртуальной сети, который, в свою очередь, подключен к виртуальному центру. Кроме того, подключите периферийные зоны виртуальных сетей с виртуальным центром.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Можно ли направлять трафик, используя определяемый пользователем маршрут виртуального центра?

Определяемый пользователем маршрут и функции маршрутизации будут доступны в общедоступной версии.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Есть ли сведения о лицензировании и ценообразовании для Виртуальной глобальной сети?
 
За использование предварительной версии дополнительная плата не взимается. Текущие [Цены на VPN-шлюз Azure и исходящий трафик](https://azure.microsoft.com/pricing/details/vpn-gateway/) остаются неизменными во время использования предварительной версии.