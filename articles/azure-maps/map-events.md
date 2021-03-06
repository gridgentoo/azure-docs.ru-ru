---
title: Обработка событий мыши с помощью службы "Карты Azure" | Документация Майкрософт
description: Как создать интерактивную карту Javascript с использованием событий карты
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6b673b415b4e93fc7ceb4288b88d6d72740f0259
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600203"
---
# <a name="interacting-with-the-map--mouse-events"></a>Взаимодействие с картой — события мыши 

В этой статье объясняется, как создать интерактивную карту с помощью прослушивателя событий.

## <a name="try-out-the-code"></a>Протестируйте код

<iframe height='618' scrolling='no' title='Взаимодействие с картой — события мыши' src='//codepen.io/azuremaps/embed/bLZEWd/?height=618&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Взаимодействие с картой — события мыши</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

Поработайте с указанной выше картой и посмотрите, как соответствующие события мыши выделяются в разделе справа. Вы можете щелкнуть вкладку JS, чтобы просмотреть и изменить код JavaScript. Кроме того, можно нажать кнопку редактирования в CodePen и изменить код.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 

* класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest);
    * метод [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener).

Дополнительные примеры кода для добавления в карты см. в следующей статье: 
* [Отображение результатов поиска на карте](./map-search-location.md)

Чтобы узнать о других сценариях работы с картами, перейдите на эту [страницу с примерами кода](http://aka.ms/AzureMapsSamples).
