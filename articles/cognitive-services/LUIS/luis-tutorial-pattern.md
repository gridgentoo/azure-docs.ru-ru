---
title: Руководство по улучшению прогнозирования в LUIS с помощью шаблонов — Azure | Документы Майкрософт
titleSuffix: Azure
description: В этом руководстве используются шаблоны для улучшения прогнозирования намерений и сущностей LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ff5572366be548132b28e5ce03b9595e7f98128c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265322"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Руководство: использование шаблонов для улучшения прогнозирования

В этом руководстве используются шаблоны для улучшения прогнозирования намерений и сущностей.  

> [!div class="checklist"]
* Определение необходимости в использовании шаблона для вашего приложения
* Создание шаблона 
* Использование встроенных и пользовательских сущностей в шаблоне 
* Проверка улучшения прогнозирования для шаблона
* Добавление роли в сущность для поиска контекстно-зависимых сущностей
* Добавление шаблона Pattern.any для поиска сущностей произвольной формы

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="import-humanresources-app"></a>Импорт приложения HumanResources
В этом учебнике импортируется приложение HumanResources. Оно содержит три намерения: None, GetEmployeeOrgChart, GetEmployeeBenefits. Приложение содержит две сущности: Prebuilt number и Employee. Сущность Employee — это простая сущность для извлечения имени сотрудника. 

1. Создайте файл приложения LUIS с именем `HumanResources.json`. 

2. Скопируйте следующее определение приложения в файл:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. На странице **Приложения** LUIS выберите **Импортировать новое приложение**. 

4. В диалоговом окне **Импорт нового приложения** выберите файл `HumanResources.json`, созданный на шаге 1.

5. Выберите намерение **GetEmployeeOrgChart** и переключитесь с **представления "Сущности"** на **представление "Маркеры"**. Вы увидите несколько примеров высказываний. Каждое высказывание содержит имя, которое представляет собой сущность Employee. Обратите внимание, что имена и порядок слов в высказываниях различны. Это разнообразие помогает LUIS пройти обучение для широкого диапазона высказываний.

    ![Снимок экрана страницы "Намерение" с выбранным представлением "Сущности"](media/luis-tutorial-pattern/utterances-token-view.png)

6. Выберите **Обучение** на верхней панели навигации, чтобы обучить приложение. Подождите, пока на экране появится зеленый индикатор хода выполнения, означающий успешное завершение обучения.

7. Выберите **Тестирование** на верхней панели. Введите `Who does Patti Owens report to?`, затем нажмите клавишу ВВОД. Выберите **Просмотр** под высказыванием для просмотра дополнительных сведений о тесте.
    
    Имя сотрудника Patti Owens еще не использовалось в примерах высказываний. Это тестовое имя, которое используется для намерения `GetEmployeeOrgChart` и сущности Employee `Patti Owens`, чтобы проверить, насколько хорошо LUIS изучил это высказывание. Результат для намерения `GetEmployeeOrgChart` должен составлять менее 50 % (0,5). Хотя намерение указано верно, оценка низкая. Сущность Employee также правильно определена как `Patti Owens`. Шаблоны увеличивают эту начальную оценку прогнозирования. 

    ![Снимок экрана панели "Тестирование"](media/luis-tutorial-pattern/original-test.png)

8. Закройте панель "Тестирование", нажав кнопку **Тестирование** на верхней панели навигации. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Обучение LUIS общим высказываниям с меньшим числом примеров благодаря использованию шаблонов
Из-за особенностей предметной области отдела кадров существует несколько общих способов для отправки запросов о связях сотрудников в организациях. Например: 

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Эти высказывания слишком близки для определения контекстной уникальности каждого из них без использования большого числа примеров высказываний. Благодаря добавлению шаблона для намерения LUIS изучает шаблоны общих высказываний для намерения без использования слишком большого числа примеров высказываний. 

К примерам шаблонов высказываний для этого намерения относятся:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Шаблон представляет собой сочетание сопоставления регулярных выражений и машинного обучения. Затем необходимо указать несколько примеров высказываний для шаблона, чтобы LUIS изучил шаблон. Эти примеры наряду с высказываниями шаблона дают LUIS лучшее представление о том, какие высказывания соответствуют намерению и где в пределах высказывания существует сущность. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>Добавление высказываний шаблона

1. В левой области навигации в разделе **Повышение производительности приложения** выберите **Шаблоны** в панели навигации слева.

2. Выберите намерение **GetEmployeeOrgChart**, затем укажите следующие высказывания шаблона по одному, нажимая клавишу ВВОД после указания каждого высказывания:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    Синтаксис `{Employee}` отмечает расположение сущности в высказывании шаблона, а также указывает, какая это сущность. 

    ![Снимок экрана с вводом высказываний шаблона для намерения](./media/luis-tutorial-pattern/enter-pattern.png)

3. Выберите **Обучение** на верхней панели навигации. Подождите, пока на экране появится зеленый индикатор хода выполнения, означающий успешное завершение обучения.

4. Выберите **Тестирование** на верхней панели. Введите `Who does Patti Owens report to?` в текстовое поле. Нажмите клавишу ВВОД. Это то же высказывание, которое проверялось в предыдущем разделе. Результат для намерения `GetEmployeeOrgChart` должен быть выше по сравнению с предыдущим разделом. 

    Оценка стала намного лучше. LUIS изучил шаблон, соответствующий намерению, без использования большого числа примеров.

    ![Снимок экрана панели "Тестирование" с высокой оценкой](./media/luis-tutorial-pattern/high-score.png)

    Сначала обнаруживается сущность, затем определяется шаблон, указывающий намерение. Если в результате вашего теста не удалось обнаружить сущность и, следовательно, определить шаблон, необходимо добавить дополнительные примеры высказываний в намерение (не в шаблон). 

5. Закройте панель "Тестирование", нажав кнопку **Тестирование** на верхней панели навигации.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Использование сущности с ролью в шаблоне
Приложение LUIS используется для перемещения сотрудников из одного расположения в другое. Пример высказывания — `Move Bob Jones from Seattle to Los Colinas`. Каждое расположение в высказывании имеет различное значение. Сиэтл (Seattle) — исходное расположение, а Лас Колинас (Los Colinas) — конечное расположение для перемещения. Чтобы различать эти расположения в шаблоне, в следующих разделах мы создадим простую сущность для расположения с двумя ролями: исходное место и место назначения. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Создание намерения для перемещения пользователей и ресурсов
Создайте новое намерение для всех высказываний, которые связаны с перемещением пользователей или ресурсов.

1. Выберите **Намерения** на панели навигации слева.
2. Выберите **Создать намерение**.
3. Укажите имя нового намерения `MoveAssetsOrPeople`.
4. Добавьте примеры высказываний:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Снимок экрана с примером высказывания для намерения MoveAssetsOrPeople](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    Цель примеров высказываний — предоставить достаточное количество примеров. Если далее в ходе тестирования сущность расположения не обнаружена и, следовательно, шаблон не определен, необходимо вернуться к этому шагу и добавить дополнительные примеры. Затем необходимо повторно провести обучение и тестирование. 

5. Пометьте сущности в примерах высказываний с сущностью Employee, выбрав имя и фамилию в высказывании и затем выбрав сущность Employee в списке.

    ![Снимок экрана высказываний в намерении MoveAssetsOrPeople с помеченной сущностью Employee](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Выберите текст `portland` в высказывании `move travis hinton from portland to orlando`. Во всплывающем диалоговом окне введите имя новой сущности `Location` и нажмите кнопку **Создать новую сущность**. Выберите **Простая** в качестве типа сущности и нажмите кнопку **Готово**.

    ![Снимок экрана с созданием новой сущности расположения](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Пометьте остальные имена расположений в высказывании. 

    ![Снимок экрана со всеми помеченными сущностями](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    Шаблон подбора и порядка слов на предыдущем рисунке очевиден. Если вы **не** используете шаблоны и у высказываний в намерении есть очевидный шаблон, это указывает на то, что следует использовать шаблоны. 

    Если вы ожидаете получить широкий набор высказываний вместо шаблона, эти примеры высказываний могут быть некорректными. В этом случае нужно разнообразить высказывания с точки зрения подбора слов или терминов, длины высказывания и расположения сущности. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Добавьте роль в сущность расположения 
Роли могут использоваться только для шаблонов. Добавьте роли Origin и Destination в сущность Location. 

1. Выберите **Сущности** на панели навигации слева, затем выберите **Расположение** в списке сущностей.

2. Добавьте роли `Origin` и `Destination` в сущность.

    ![Снимок экрана новой сущности с ролями](./media/luis-tutorial-pattern/location-entity.png)

    Эти роли не помечены на странице намерения MoveAssetsOrPeople, так как роли в высказываниях намерения отсутствуют. Они существуют только в высказываниях шаблона. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Добавление высказываний шаблона, в которых используются роли расположения и места назначения
Добавьте высказывания шаблона, которые используют новую сущность.

1. Выберите **Шаблоны** в меню навигации слева.

2. Выберите намерение **MoveAssetsOrPeople**.

3. Укажите высказывание для нового шаблона с использованием новой сущности `Move {Employee} from {Location:Origin} to {Location:Destination}`. Синтаксис сущности и роли в высказывании шаблона — `{entity:role}`.

    ![Снимок экрана новой сущности с ролями](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Обучите приложение с новыми намерением, сущностью и шаблоном.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Проверка извлечения данных роли для нового шаблона
Проверьте новый шаблон с помощью теста.

1. Выберите **Тестирование** на верхней панели. 
2. Введите высказывание `Move Tammi Carlson from Bellingham to Winthrop`.
3. Выберите **Просмотр** под результатом для просмотра результатов теста для сущности и намерения.

    ![Снимок экрана новой сущности с ролями](./media/luis-tutorial-pattern/test-with-roles.png)

    Сначала обнаруживаются сущности, затем определяется шаблон, указывающий намерение. Если в результате вашего теста не удалось обнаружить сущности и, следовательно, определить шаблон, необходимо добавить дополнительные примеры высказываний в намерение (не в шаблон). 

4. Закройте панель "Тестирование", нажав кнопку **Тестирование** на верхней панели навигации.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Использование сущности Pattern.any для поиска сущностей произвольной формы в шаблоне
Приложение HumanResources также помогает сотрудникам находить формы организации. Многие из форм имеют заголовки, которые различаются по длине. Заголовки переменной длины включают фразы, из-за которых LUIS может неправильно определить окончание названия формы. С помощью сущности **Pattern.any** в шаблоны вы сможете указать начало и конец названия формы, чтобы LUIS правильно извлек имя формы. 

### <a name="create-a-new-intent-for-the-form"></a>Создание намерения для формы
Создайте новое намерение для высказываний, которые необходимы для поиска форм.

1. Выберите **Намерения** на панели навигации слева.

2. Выберите **Создать намерение**.

3. Укажите имя нового намерения `FindForm`.

4. Добавьте пример высказывания.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Снимок экрана новой сущности с ролями](./media/luis-tutorial-pattern/intent-findform.png)

    Название формы — `What to do when a fire breaks out in the Lab`. Высказывание запрашивает форму расположение формы и также запрашивает, кто должен подписать форму, чтобы подтвердить ее прочтение. Без использования сущности Pattern.any было бы трудно понять, где заканчивается название формы, и извлечь название формы в качестве сущности высказывания.

### <a name="create-a-patternany-entity-for-the-form-title"></a>Создание сущности Pattern.any для названия формы
Сущность Pattern.any позволяет использовать сущности переменной длины. Эта сущность работает только в шаблоне, так как шаблон отмечает начало и окончание сущности. Если ваш шаблон, который включает сущность Pattern.any, извлекает сущности неправильно, используйте [явный список](luis-concept-patterns.md#explicit-lists), чтобы решить эту проблему. 

1. Выберите **Сущности** в меню навигации слева.

2. Нажмите кнопку **Создать сущность**. 

3. Укажите имя сущности `FormName` и тип **Pattern.any**. Для этого учебника добавлять роли к сущности необязательно.

    ![Диалоговое окно с именем и типом сущности](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Добавление шаблона, который использует сущность Pattern.any

1. Выберите **Шаблоны** в меню навигации слева.

2. Выберите намерение **FindForm**.

3. Укажите высказывание шаблона, в котором используется новая сущность `Where is the form {FormName} and who needs to sign it after I read it?`.

    ![Снимок экрана высказывания шаблона, использующего сущность Pattern.any](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Обучите приложение с новыми намерением, сущностью и шаблоном.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Проверка извлечения произвольных данных для нового шаблона
1. Выберите **Тестирование** на верхней панели навигации, чтобы открыть панель тестирования. 

2. Введите высказывание `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Выберите **Просмотр** под результатом для просмотра результатов теста для сущности и намерения.

    ![Снимок экрана высказывания шаблона, использующего сущность Pattern.any](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    Сначала обнаруживается сущность, затем определяется шаблон, указывающий намерение. Если в результате вашего теста не удалось обнаружить сущности и, следовательно, определить шаблон, необходимо добавить дополнительные примеры высказываний в намерение (не в шаблон).

4. Закройте панель "Тестирование", нажав кнопку **Тестирование** на верхней панели навигации.

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Чтобы сделать это, щелкните меню с тремя точками (...) справа от имени приложения в списке приложений и выберите **Удалить**. Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Улучшение прогнозирования с помощью списка фраз](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions