---
title: Обзор машинного обучения Пользовательской службы визуального распознавания в Azure Cognitive Services | Документы Майкрософт
description: Пользовательская служба визуального распознавания — это служба Microsoft Cognitive Service, которая позволяет создавать пользовательские классификаторы изображений на платформе Azure.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087801"
---
# <a name="what-is-the-custom-vision-service"></a>Что собой представляет Пользовательская служба визуального распознавания?

Пользовательская служба визуального распознавания — это служба Microsoft Cognitive Service, которая позволяет создавать пользовательские классификаторы изображений. Она позволяет легко и быстро создавать, развертывать и совершенствовать классификаторы изображений. Пользовательская служба визуального распознавания предоставляет REST API и веб-интерфейс для отправки изображений и обучения классификатора.

## <a name="what-does-custom-vision-service-do-well"></a>Возможности Пользовательской службы визуального распознавания

Пользовательская служба визуального распознавания работает наиболее эффективно, когда элемент, который вы пытаетесь классифицировать, выделяется на изображении. 

Для создания классификатора или средства обнаружения требуется несколько изображений. Для запуска прототипа достаточно 50 образов на класс. Пользовательская служба визуального распознавания использует надежные методы распознавания отличий, поэтому вы можете создать прототип на основе небольшого объема данных. Это означает, что Пользовательская служба визуального распознавания — не лучший вариант для ситуаций, когда необходимо обнаружить незначительные отличия. Например, мелкие трещины или сколы при контроле качества.

## <a name="release-notes"></a>Заметки о выпуске

### <a name="may-7-2018"></a>7 мая 2018 г.
- Появилась предварительная версия возможности обнаружения объекта для проектов в пробной версии с ограниченным временем действия.
- Обновление до API версии 2.0
- Уровень S0 теперь вмещает до 250 тегов 50 000 изображений. 
- Значительные улучшения в серверной части конвейера машинного обучения для проектов по классификации изображений. Эти преимущества будут доступны для проектов с обучением после 27 апреля 2018 г.
- Добавлена модель экспорта в ONNX для использования с машинным обучением Windows.
- Добавлен экспорт модели в Dockerfile. Это позволяет загрузить артефакты для создания собственных контейнеров Windows или Linux, включая DockerFile, модель TensorFlow и код службы. 
- Для вновь обученных моделей, экспортированных в TensorFlow в доменах "Общее" (компактный) и "Достопримечательность" (компактный), [средние значения теперь равны (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) для согласованности во всех проектах. 

### <a name="march-1-2018"></a>1 марта 2018 г.
- Введение платной предварительной версии и включение на портал Azure. Теперь проекты можно подключить к ресурсам Azure с помощью уровня F0 (Бесплатный) или S0 (Стандартный). Появились проекты уровня S0 с вместимостью 100 тегов и 25 000 изображений. 
- Изменения внутренних служб для конвейера машинного обучения/параметра нормализации. Так клиенты смогут точнее контролировать баланс точности и полноты при настройке порога вероятности. В рамках этих изменений порог вероятности по умолчанию на портале CustomVision.ai был установлен на 50 %.

### <a name="december-19-2017"></a>19 декабря 2017 г.

- Добавлен экспорт в Android (TensorFlow) в дополнение к добавленному ранее экспорту в iOS (CoreML). Благодаря этому обученную компактную модель можно экспортировать автономно в приложении.
- Добавлены компактные домены "Розничная торговля" и "Достопримечательности" для экспорта модели для этих доменов.
- Выпущены версии [API обучения 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) и [API прогнозирования 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Обновленные API-интерфейсы поддерживают экспорт моделей, новую операцию прогнозирования, которая не сохраняет изображения в разделе "Прогнозирование", а также пакетные операции в API обучения.
- Настройки пользовательского интерфейса, включая возможность видеть, какой домен использовался для обучения итерации.
- Обновленный [пакет SDK для C# и пример](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

## <a name="next-steps"></a>Дальнейшие действия

[Узнайте, как создать классификатор](getting-started-build-a-classifier.md)
