---
title: включение файла
description: включение файла
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: b91ae155761f6357e286f4742d57b97cf96d909a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805164"
---
## <a name="scenario"></a>Сценарий
Чтобы лучше проиллюстрировать процесс создания определяемых пользователем маршрутов, в этом документе будет использоваться представленный ниже сценарий.

![ОПИСАНИЕ ОБРАЗА](./media/virtual-network-create-udr-scenario-include/figure1.png)

В данном случае вы создадите один определяемый пользователем маршрут для *интерфейсной подсети* и еще один такой маршрут для *внутренней подсети*, как описано ниже. 

* **UDR-FrontEnd**. Определяемый пользователем интерфейсный маршрут применяется к подсети *FrontEnd* и содержит один маршрут:    
  * **RouteToBackend**. Этот маршрут отправляет весь трафик, адресованный во внутреннюю подсеть, на виртуальную машину **FW1**.
* **UDR-BackEnd**. Определяемый пользователем внутренний маршрут применяется к подсети *BackEnd* и содержит один маршрут:    
  * **RouteToFrontend**. Этот маршрут отправляет весь трафик, адресованный в интерфейсную подсеть, на виртуальную машину **FW1**.

Сочетание этих маршрутов гарантирует, что любой трафик, передаваемый из одной подсети в другую, будет направляться на виртуальную машину **FW1**, которая используется в качестве виртуального модуля. Необходимо также включить IP-пересылку для виртуальной машины **FW1**, чтобы она могла получать трафик, предназначенный для других виртуальных машин.

