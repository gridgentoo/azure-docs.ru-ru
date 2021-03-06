---
title: Памятка по алгоритмам Машинного обучения Microsoft Azure | Документация Майкрософт
description: Памятка по алгоритмам Машинного обучения Microsoft Azure для печати поможет выбрать в Студии машинного обучения Azure правильный алгоритм для модели прогнозирования.
keywords: памятка по алгоритмам, памятка, алгоритм машинного обучения
services: machine-learning
documentationcenter: ''
author: pakalra
ms.author: pakalra
manager: cgronlun
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: a448d6931330f7b2f0730add65473097bb2b5a57
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833569"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Памятка по алгоритмам машинного обучения для Студии машинного обучения Microsoft Azure
**Памятка по алгоритмам Машинного обучения Microsoft Azure** поможет выбрать правильный алгоритм для модели прогнозной аналитики.

[Студия машинного обучения Azure](https://studio.azureml.net/) включает большую библиотеку алгоритмов из семейств ***регрессии***, ***классификации***, ***кластеризации*** и ***обнаружения аномалий***. Каждое семейство предназначено для решения определенного типа проблем, связанных с машинным обучением.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Скачивание памятки по алгоритмам Машинного обучения Microsoft Azure
**Скачать можно здесь: [Памятка по алгоритмам Машинного обучения Microsoft Azure (27,94 x 43,18 см)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Памятка по алгоритмам машинного обучения: как выбрать алгоритм машинного обучения?][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Скачайте и распечатайте памятку по алгоритмам машинного обучения размером 27,94 x 43,18 см (примерно A3), чтобы вы всегда могли обратиться к ней при выборе алгоритма.

> [!NOTE]
> Подробное руководство по использованию этой памятки см. в статье [Выбор алгоритмов машинного обучения Microsoft Azure](algorithm-choice.md).
> 
> 

## <a name="more-help-with-algorithms"></a>Дополнительная помощь с алгоритмами
* Более детальное обсуждение различных типов алгоритмов машинного обучения, их использования и выбора правильного алгоритма с использованием этой памятки см. в статье [Выбор алгоритмов Машинного обучения Microsoft Azure](algorithm-choice.md).
* Сведения о скачиваемой инфографике с описанием алгоритмов и примерами см. в статье [Загружаемая инфографика по основам машинного обучения с примерами алгоритмов](basics-infographic-with-algorithm-examples.md).
* Список всех категорий алгоритмов, доступных в Студии машинного обучения, см. в разделе [Инициализация модели][initialize-model] в справке по алгоритмам и модулям Студии машинного обучения.
* Полный список всех алгоритмов и модулей машинного обучения, доступных в Студии машинного обучения, расположенных в алфавитном порядке, см. в разделе [A-Z list of Machine Learning Studio][a-z-list] (Полный список модулей Студии машинного обучения) в справке по алгоритмам и модулям Студии машинного обучения.
* Чтобы скачать и распечатать схему, на которой представлены общие возможности Студии машинного обучения, см. [обзорную схему возможностей Студии машинного обучения Azure](studio-overview-diagram.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Памятка по алгоритмам машинного обучения: заметки и определения терминов

* Рекомендации, предлагаемые в этой памятке алгоритмов, представляют собой общие правила. Некоторые можно приспособить к конкретной ситуации, а некоторые можно грубо нарушать. Они представлены в качестве отправной точки. Не бойтесь использовать несколько алгоритмов одновременно при обработке данных. Вам просто нужно понять принцип действия каждого алгоритма, а также систему, создавшую данные.

* Каждому алгоритму машинного обучения присущ собственный стиль *индуктивного смещения*. Для решения конкретной проблемы могут подходить несколько алгоритмов, но один из них может подходить лучше других. Однако не всегда возможно узнать это заранее. В подобных случаях в памятке указано сразу несколько алгоритмов. Лучше всего будет использовать один алгоритм, и если результаты неудовлетворительные, применить другие алгоритмы. Ниже приведен пример эксперимента из [Коллекции решений ИИ Azure](http://gallery.cortanaintelligence.com/), в котором используется несколько алгоритмов для одних и тех же данных, а затем сравниваются результаты: [Compare Multi-class Classifiers: Letter recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92) (Сравнение многоклассовых классификаторов: распознавание букв).

* Существуют три основные категории машинного обучения: **контролируемое**, **неконтролируемое** и **обучение с подкреплением**.

  * В **контролируемом обучении** каждая точка данных помечается или привязывается к интересующей категории или значению.  Пример категориальной метки — назначение изображению значения cat или dog.  Пример метки значения — цена продажи, связанная с подержанным автомобилем. Цель контролируемого обучения заключается в изучении множества помеченных таким образом примеров, а затем в возможности прогнозирования будущих точек данных. Например, чтобы правильно определить животных для новых фотографий или назначить точные цены продажи для других подержанных автомобилей. Это популярный и полезный тип машинного обучения. Все модули в Машинном обучении Azure являются алгоритмами контролируемого обучения, кроме [кластеризации методом k-средних][k-means-clustering].

  * При **неконтролируемом обучении** точкам данных не присваиваются метки. Вместо этого цель алгоритма неконтролируемого обучения — определенное упорядочивание данных или описание их структуры. Это может означать группировку данных в кластеры, как при кластеризации К-средних, или поиск различных способов анализа сложных данных для их упрощения.

  * В **обучении с подкреплением** алгоритм выбирает действие в ответ на каждую точку данных. Это наиболее распространенный подход в робототехнике, где набор показаний датчиков в один момент времени представляет собой точку данных, а алгоритму необходимо выбрать следующее действие робота. Кроме того, он естественным образом подходит для приложений из Интернета вещей. Алгоритм обучения также вскоре получает сигнал, оповещающий об успехе, который дает понять, насколько удачно было принято решение. На основе этого алгоритм изменяет свою стратегию для достижения лучшего результата. Сейчас в Машинном обучении Azure модули алгоритмов обучения с подкреплением отсутствуют.

* В **байесовских методах** делаются предположения о статистически независимых точках данных. Это означает, что несмоделированная изменчивость в одной точке данных не коррелирует с другими точками данных, то есть ее нельзя предсказать. Например, если записываемые данные представляют собой число минут до прибытия следующего поезда в метро, два измерения, полученные с разницей в один день, статистически независимые. Но два измерения, полученные с разницей в минуту, не независимы статистически, так как по одному значению можно с высокой долей вероятности спрогнозировать другое значение.

* **Регрессия увеличивающегося дерева принятия решений** использует преимущества перекрытия компонентов или взаимодействия между ними. Это означает, что в любой заданной точке данных по значению одного компонента можно в некоторой степени спрогнозировать значение другого. Например, при анализе ежедневных данных по высокой и низкой температуре, зная низкую температуру за день, можно сделать правдоподобное предположение относительно высокой температуры. Информация, содержащаяся в двух компонентах, отчасти избыточна.

* Классифицировать данные на несколько категорий можно с помощью изначально многоклассового классификатора или объединив набор двухклассовых классификаторов в **множество**. При использовании множества для каждого класса есть отдельный двухклассовый классификатор, который разделяет данные на две категории — "этот класс" и "другой класс". Затем эти классификаторы выбирают правильное назначение для точки данных. Это рабочий принцип, лежащий в основе [многоклассовой классификации "один — все"][one-vs-all-multiclass].

* В некоторых методах, включая логистическую регрессию и байесовскую точечную машину, предполагаются **границы линейного класса**. То есть то, что границы между классами предположительно являются прямыми линиями (или гиперплоскостями в более общем случае). Зачастую это характеристика данных, неизвестная до того момента, пока вы не попытались выделить ее, но обычно ее можно заранее узнать с помощью визуализации. Если границы класса выглядят очень нерегулярными, воспользуйтесь деревьями принятия решений, джунглями принятия решений, методом опорных векторов или нейронными сетями.

* Нейронные сети можно использовать с категориальными переменными, создав **вспомогательную переменную** для каждой категории и задавая для нее значение 1 в случаях, где категория применяется, и 0, когда она не применяется.


<!-- This is how you can embed a link in an image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-docs-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
