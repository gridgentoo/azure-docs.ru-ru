---
title: Добавление настраиваемого элемента HTML в службе "Карты Azure" | Документация Майкрософт
description: Как добавить настраиваемый элемент HTML на карту в JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3ee1f394536061cecaa5d775650149b30098560e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600227"
---
# <a name="add-custom-html-to-the-map"></a>Добавление настраиваемого элемента HTML на карту

В этой статье показано, как добавить на карту настраиваемый элемент HTML, например файл изображения. 

## <a name="understand-the-code"></a>Изучение кода

<iframe height='466' scrolling='no' title='Добавление настраиваемого элемента HTML на карту — PNG' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Добавление настраиваемого элемента HTML на карту — PNG</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. Инструкции см. в статье о [создании карты](./map-create.md).

Во втором блоке кода из изображения создается элемент HTML.

В последнем блоке кода используется функция [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) класса карты, чтобы добавить изображение в указанную точку на карте.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 
* класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest);
    * метод [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml).
    
Дополнительные примеры кода для добавления в карты см. в следующих статьях: 
* [Отображение результатов поиска на карте](./map-search-location.md)
* [Получение сведений на основе координат](./map-get-information-from-coordinate.md)

