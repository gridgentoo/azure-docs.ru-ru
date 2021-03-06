---
title: Пользовательские модули в коллекции решений ИИ Azure | Документация Майкрософт
description: Поиск пользовательских модулей машинного обучения в коллекции решений ИИ Azure.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.openlocfilehash: c53bab2e838425dfdd124e64c3d7d3114fa30429
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834436"
---
# <a name="discover-custom-machine-learning-modules-in-azure-ai-gallery"></a>Поиск пользовательских модулей машинного обучения в коллекции решений ИИ Azure
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Пользовательские модули для Студии машинного обучения
В коллекции решений ИИ Azure есть несколько [пользовательских модулей](https://gallery.cortanaintelligence.com/customModules), которые расширяют возможности Студии машинного обучения Azure. Вы можете импортировать эти модули и использовать их в экспериментах, чтобы создать еще более сложные решения для прогнозной аналитики.

В настоящее время коллекция предлагает модули для *аналитики временных рядов*, *правил взаимосвязей*, *алгоритмов кластеризации* (помимо К-средних), а также для *визуализаций* и других основных служебных модулей.


## <a name="discover"></a>Поиск
Для просмотра пользовательских модулей [в коллекции](http://gallery.cortanaintelligence.com) в разделе **More** (Дополнительно) выберите **Custom Modules** (Пользовательские модули).

![Выбор пользовательских модулей на домашней странице коллекции](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

На странице **[Custom Modules](https://gallery.cortanaintelligence.com/customModules)** (Пользовательские модули) отображается список недавно добавленных и популярных модулей. Чтобы просмотреть все пользовательские модули, нажмите кнопку **See all** (Просмотреть все). Чтобы найти нужный пользовательский модуль, нажмите кнопку **See all** (Просмотреть все), а затем задайте условия фильтра. Вы можете также ввести условия поиска в поле **поиска** в верхней части страницы коллекции.

![Выбор See all (Просмотреть все) для просмотра всех пользовательских модулей](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Общие сведения

Понять, как работает опубликованный пользовательский модуль, можно на странице сведений о нем. Чтобы открыть такую страницу, выберите соответствующий модуль. Страница сведений обеспечивает согласованный и информативный процесс обучения. Например, на этой странице описываются сведения о назначении модуля и перечисляются ожидаемые входные и выходные данные, а также параметры. Страница сведений также имеет ссылку на базовый исходный код, который можно изучить и настроить.

### <a name="comment-and-share"></a>Комментарии и общий доступ
На этой странице в разделе **Comments** (Комментарии) можно оставлять комментарии, отзывы и задавать вопросы о модуле. Можно даже поделиться модулем с друзьями и коллегами на Twitter и LinkedIn. Кроме того, можно отправить ссылку на страницу сведений о модуле по электронной почте, чтобы другие пользователи также могли просмотреть ее.

![Расскажите друзьям о том, что вы нашли](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Добавляйте свои комментарии](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Импорт
Любой пользовательский модуль из коллекции можно импортировать в свои эксперименты.

Коллекция решений ИИ Azure поддерживает два способа импорта копии модуля.

* **Из коллекции**. При импорте пользовательского модуля из коллекции можно также получить пример эксперимента, который поможет разобраться, как можно использовать этот модуль.
* **Из Студии машинного обучения Azure**. Любой пользовательский модуль можно импортировать во время работы в Студии машинного обучения (в этом случае вы не получите пример эксперимента).

### <a name="from-the-gallery"></a>Из коллекции

1. Откройте страницу сведений о модуле в коллекции. 
2. Выберите **Open in Studio** (Открыть в Студии).
   
    ![Откройте пользовательский модуль из коллекции](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Каждый пользовательский модуль содержит пример эксперимента, в котором демонстрируется использование этого модуля. Если выбрать **Open in Studio** (Открыть в Студии), пример эксперимента откроется в рабочей области Студии машинного обучения. (Если вы еще не вошли в Студию, вам будет предложено сначала войти, используя учетную запись Майкрософт.)

В дополнение к получению примера эксперимента, в рабочую область копируется пользовательский модуль. Он также размещается на палитре модулей со всеми встроенными и пользовательскими модулями Студии машинного обучения. Теперь вы можете использовать его в своих экспериментах так же, как любой другой модуль в рабочей области.

### <a name="from-within-machine-learning-studio"></a>Из Студии машинного обучения Azure

1. В Студии машинного обучения выберите **New** (Создать).
2. Выберите **Модуль**. Модуль можно выбрать в списке модулей коллекции или найти, используя поле **поиска**.
3. Укажите мышью модуль, а затем выберите **Импортировать модуль**. (Чтобы увидеть сведения о модуле, выберите **View in Gallery** (Просмотреть в коллекции). В результате в коллекции откроется страница сведений о модуле.)
   
    ![Импорт пользовательского модуля в Студию машинного обучения](./media/gallery-custom-modules/add-custom-module-in-studio.png)

Пользовательский модуль копируется в рабочую область и размещается на палитре модулей рядом с остальными встроенными и пользовательскими модулями Студии машинного обучения. Теперь вы можете использовать его в своих экспериментах так же, как любой другой модуль в рабочей области.

## <a name="use"></a>Использование

Независимо от того, какой метод вы решили использовать для импорта пользовательского модуля, модуль будет размещен на палитре модулей в Студии машинного обучения. С помощью палитры модулей вы сможете использовать пользовательский модуль в любых экспериментах в рабочей области, как и любой другой модуль.

Чтобы использовать импортированный модуль:

1. Создайте эксперимент или откройте существующий.
2. Чтобы развернуть список пользовательских модулей в рабочей области, на палитре модулей выберите **Custom** (Пользовательский). Палитра модулей находится слева от холста эксперимента.
   
    ![Список пользовательских модулей на палитре Студии](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Выберите импортированный модуль и перетащите его в эксперимент.


**[Перейти к коллекции](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

