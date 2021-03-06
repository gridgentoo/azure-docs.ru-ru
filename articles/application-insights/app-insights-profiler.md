---
title: Профилирование динамических веб-приложений в Azure с помощью Application Insights Profiler | Документация Майкрософт
description: Определите критический путь в коде веб-сервера с помощью профилировщика небольшого размера.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 07/13/2018
ms.author: mbullwin
ms.openlocfilehash: e4712b94be94eb6d4cf363fc120b72c74f29f0a2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057926"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Профилирование динамических веб-приложений Azure с помощью Application Insights

Эта функция Azure Application Insights общедоступна для компонента "Веб-приложения" службы приложений Azure и предоставляется в предварительной версии для вычислительных ресурсов Azure. Сведения об использовании [профилировщика в локальной среде](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers).

Из этой статьи вы узнаете, сколько времени выполняется каждый метод в динамическом веб-приложении с помощью [Application Insights](app-insights-overview.md). Средство Application Insights Profiler отображает подробные данные профилей динамических запросов, обработанные приложением, а также выделяет *критический путь*, на использование которого уходит большая часть времени. Запросы с разным временем отклика профилируются на основе выборки. С помощью различных методов можно минимизировать затраты на приложение.

Сейчас профилировщик работает для веб-приложений ASP.NET и ASP.NET Core, запущенных в компоненте "Веб-приложения". Он требуется, начиная от уровня службы "Базовый".

## <a id="installation"></a> Включение профилировщика для веб-приложений

После развертывания веб-приложения, независимо от включения пакета SDK для App Insights в исходный код, сделайте следующее:

1. Перейдите к панели **служб приложений** на портале Azure.
2. Перейдите к панели **Параметры > Мониторинг**.

   ![Включение Application Insights на портале служб приложений](./media/app-insights-profiler/AppInsights-AppServices.png)

3. Следуйте инструкциям на панели, чтобы создать новый ресурс Application Insights или выбрать имеющийся ресурс для отслеживания веб-приложения. Примите все параметры по умолчанию. **Диагностика на уровне кода** включена по умолчанию. Этот параметр включает профилировщик.

   ![Добавление расширения сайта Application Insights][Enablement UI]

4. Профилировщик теперь устанавливается вместе с расширением сайта Application Insights, а также включается с помощью параметра приложения службы приложений.

    ![Параметр приложения для профилировщика][profiler-app-setting]

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Включение профилировщика для вычислительных ресурсов Azure (предварительная версия)

Дополнительные сведения см. в статье [Включение Application Insights Profiler на виртуальных машинах Azure, в Service Fabric и облачных службах](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Просмотр данных профилировщика

Убедитесь, что приложение получает трафик. Если вы выполняете эксперимент, можно создать запросы к веб-приложению [в рамках тестирования производительности Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Если вы только что включили профилировщик, можно выполнить короткий 15-минутный нагрузочный тест, чтобы получить трассировку. Обратите внимание, что если профилировщик уже включен некоторое время, то он случайно запускается дважды в час и работает на протяжении двух минут. Мы советуем сначала запустить нагрузочный тест на один час, чтобы получить образцы трассировки профилировщика.

Когда приложение получит часть трафика, откройте область **Производительность**, перейдите к разделу **Take Actions** (Предпринять действия), чтобы просмотреть трассировку профилировщика, а затем нажмите кнопку **Profiler Traces** (Трассировки Profiler).

![Кнопка Profiler traces (Трассировки Profiler) в области Preview Performance (Производительность предварительной версии) Application Insights][performance-blade-v2-examples]

Выберите пример, чтобы отобразить классификацию на уровне кода по времени, затраченному на выполнение запроса.

![Обозреватель трассировки Application Insights][trace-explorer]

Обозреватель трассировки отображает следующие сведения:

* **Показать критический путь** — открывает основной листовой узел или хотя бы что-то с ним связанное. В большинстве случаев этот узел размещается рядом с узким местом производительности.
* **Метка** — имя функции или события. В дереве отображается сочетание кода и возникающих событий (например, события SQL и HTTP). Основное событие предоставляет общую длительность запроса.
* **Истекло** — интервал времени между началом и завершением операции.
* **Когда** — время запуска функции или события по отношению к другим функциям.

## <a name="how-to-read-performance-data"></a>Чтение данных о производительности

Профилировщик службы Майкрософт использует метод выборки в сочетании с инструментированием для анализа производительности приложения. Во время подробной сборки профилировщик службы каждую миллисекунду проводит выборку указателя инструкций для всех ЦП компьютера. Каждый пример записывает полный стек вызовов выполняющегося потока. Он предоставляет подробные сведения об этом потоке на высоком и низком уровне абстракции. Профилировщик службы также собирает сведения о других событиях, включая события переключения контекста, библиотеки параллельных задач (TPL) и пула потоков, для отслеживания корреляции действий и причинно-следственных связей.

Стек вызовов, отображаемый в представлении временной шкалы, является результатом выборки и инструментирования. Так как каждый пример записывает полный стек вызовов потока, он содержит код из Microsoft .NET Framework, а также из других указанных вами платформ.

### <a id="jitnewobj"></a>Выделение объектов (clr!JIT\_New или clr!JIT\_Newarr1)

**clr!JIT\_New** и **clr!JIT\_Newarr1** — это вспомогательные функции в .NET Framework, выделяющие память из управляемой кучи. **clr!JIT\_New** вызывается при выделении объекта. **clr!JIT\_Newarr1** вызывается при выделении массива объектов. Обычно эти две функции выполняются быстро за относительно небольшое время. Если функции **clr!JIT\_New** или **clr!JIT\_Newarr1** занимают довольно много времени во временной шкале, это указывает на то, что код, возможно, выделяет несколько объектов и потребляет значительный объем памяти.

### <a id="theprestub"></a>Код загрузки (clr!ThePreStub)

**clr!ThePreStub** — это вспомогательная функция в .NET Framework, подготавливающая код к первому выполнению. Как правило, она включает в себя JIT-компиляцию (но не ограничивается ею). Для каждого метода C# **clr!ThePreStub** должна вызываться только один раз в течение всего времени существования процесса.

Если **clr!ThePreStub** требуется много времени для запроса, это значит, что запрос впервые выполняет метод. На загрузку первого метода требуется значительное время работы среды выполнения .NET Framework. Вы можете использовать процесс прогрева, выполняющий эту часть кода до предоставления к нему доступа пользователям, или запустить генератор образа в машинном коде (ngen.exe) в сборках.

### <a id="lockcontention"></a>Конфликт блокировки (clr!JITutil\_MonContention или clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** или **clr!JITutil\_MonEnterWorker** указывает, что текущий поток ожидает снятия блокировки. Этот текст обычно отображается при выполнении команды **LOCK** языка C# путем вызова метода **Monitor.Enter** или метода с атрибутом **MethodImplOptions.Synchronized**. Конфликт блокировки обычно происходит при получении блокировки потоком _A_, а также при попытке получить ту же блокировку потоком _Б_ до ее снятия потоком _A_.

### <a id="ngencold"></a>Код загрузки ([COLD])

Если имя метода содержит **[COLD]**, например **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, это значит, что в среде выполнения .NET Framework впервые выполняется код, оптимизация которого не осуществилась с помощью <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">профильной оптимизации</a>. Для каждого метода он должен отображаться только один раз в течение всего времени существования процесса.

Если коду загрузки требуется значительное время на запрос, это значит, что запрос впервые выполняет неоптимизированную часть метода. Вы можете использовать процесс прогрева, который выполняет эту часть кода до предоставления к нему доступа пользователям.

### <a id="httpclientsend"></a>Отправка HTTP-запроса

Методы, такие как **HttpClient.Send**, указывают на то, что код ожидает завершения HTTP-запроса.

### <a id="sqlcommand"></a>Операция с базой данных

Методы, такие как **SqlCommand.Execute**, указывают на то, что код ожидает завершения операции с базой данных.

### <a id="await"></a>Ожидание (AWAIT\_TIME)

**AWAIT\_TIME** указывает на то, что код ожидает завершения другой задачи. Обычно это осуществляется с помощью команды **AWAIT** языка C#. Когда код выполняет команду **AWAIT**, поток освобождает и возвращает элемент управления в пул потоков, а поток, блокирующийся при ожидании завершения **AWAIT**, отсутствует. Однако если рассуждать логически, то поток, который выполнил команду **AWAIT**, "заблокирован" и ожидает завершения операции. Команда **AWAIT\_TIME** указывает время блокировки в ожидании завершения задачи.

### <a id="block"></a>Время блокировки

**BLOCKED_TIME** указывает на то, что код ожидает, пока другой ресурс станет доступным. Например, он может ожидать объект синхронизации, или когда поток станет доступным, или когда завершится запрос.

### <a id="cpu"></a>Время ЦП

ЦП занят выполнением инструкций.

### <a id="disk"></a>Время работы диска

Приложение выполняет операции с диском.

### <a id="network"></a>Сетевое время

Приложение выполняет сетевые операции.

### <a id="when"></a>Столбец "Время"

Столбец **Когда** — это визуальное представление того, как со временем изменяются выборки INCLUSIVE, собранные для узла. Общий диапазон запроса состоит из 32 временных периодов. В них накапливаются включающие выборки для данного узла. Каждый период представляется в виде полосы. Высота полосы обозначает масштабированное значение. Для узлов, помеченных как **CPU_TIME** или **BLOCKED_TIME**, или когда существует очевидная связь с потреблением ресурсов (например, ЦП, диск или поток), в полосе представлено использование одного из этих ресурсов за определенный период времени. Для этих метрик можно получить значение, превышающее 100 процентов, за счет потребления нескольких ресурсов. Например, если в среднем за определенный интервал времени используется два процессора, то показатель достигнет 200 процентов.

## <a name="limitations"></a>Ограничения

Срок хранения данных по умолчанию — пять дней. Максимальный объем ежедневно обрабатываемых данных — 10 ГБ.

За использование службы профилировщика плата не взимается. Для использования службы профилировщика веб-приложение должно быть размещено по крайней мере на уровне "Базовый" компонента "Веб-приложения".

## <a name="overhead-and-sampling-algorithm"></a>Дополнительная нагрузка и алгоритм выборки

Раз в час профилировщик запускается случайным образом на две минуты на каждой виртуальной машине, где размещено приложение, для сбора трассировок которого настроен профилировщик. При запуске профилировщик увеличивает нагрузку на ресурсы ЦП сервера на 5–15 процентов.

Чем больше серверов доступно для размещения приложения, тем меньше профилировщик влияет на общую производительность приложения. Причина этого состоит в том, что алгоритм выборки запускает профилировщик только на 5 процентов серверов в любой момент времени. Для обслуживания веб-запросов будет доступно больше серверов, которые возьмут на себя нагрузку серверов, выполняющих дополнительную рабочую нагрузку профилировщика.

## <a name="disable-profiler"></a>Отключение профилировщика

Чтобы остановить или перезапустить профилировщик для отдельного экземпляра веб-приложения, в области **Веб-задания** перейдите к ресурсу веб-приложений. Чтобы удалить профилировщик, откройте **Расширения**.

![Отключение профилировщика для веб-задания][disable-profiler-webjob]

Мы рекомендуем включить профилировщик для всех веб-приложений, чтобы обеспечить максимально быстрое обнаружение проблем производительности.

Если вы используете WebDeploy для развертывания изменений в веб-приложении, убедитесь, что папка App_Data не удалилась во время развертывания. В противном случае файлы с расширением профилировщика будут удалены при следующем развертывании веб-приложения в Azure.


## <a id="troubleshooting"></a>Устранение неполадок

### <a name="too-many-active-profiling-sessions"></a>Слишком много активных сеансов профилирования

Сейчас вы можете включить профилировщик не более, чем в четырех веб-приложениях Azure и слотах развертывания, работающих в рамках одного плана службы. Если веб-задание профилировщика содержит слишком много активных сеансов профилирования, то перенесите некоторые веб-приложения в другой план службы.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Как определить, работает ли Application Insights Profiler?

Профилировщик запускается в качестве непрерывного веб-задания в веб-приложении. Вы можете открыть ресурс веб-приложения на [портале Azure](https://portal.azure.com). Проверьте состояние **ApplicationInsightsProfiler** в области **Веб-задания**. Если он не работает, откройте **Журналы**, чтобы просмотреть дополнительные сведения.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Почему не удается найти все примеры стека даже при работе профилировщика?

Вот несколько моментов, которые можно проверить:

* Убедитесь, что вы используете план службы веб-приложений уровня "Базовый" или выше.
* Проверьте, содержит ли веб-приложение пакет SDK Application Insights бета-версии 2.2 или более поздней.
* Убедитесь, что веб-приложение содержит параметр **APPINSIGHTS_INSTRUMENTATIONKEY**, настроенный с тем же ключом инструментирования, который использует пакет SDK Application Insights.
* Проверьте, работает ли веб-приложение на платформе .NET Framework 4.6.
* Если ваше веб-приложение является приложением ASP.NET Core, то проверьте [необходимые зависимости](#aspnetcore).

После запуска профилировщика наблюдается короткий период прогрева, во время которого профилировщик активно собирает несколько трассировок производительности. Он выполняет это действие в течение двух минут каждый час.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Я включил профилировщик службы Azure. Что с ним произошло?

При включении Application Insights Profiler агент профилировщика службы Azure отключается.

### <a id="double-counting"></a>Двойной подсчет в параллельных потоках

В некоторых случаях значение метрики общего времени в средстве просмотра стека больше, чем длительность запроса.

Это может произойти при наличии нескольких потоков, связанных с запросом и работающих в параллельном режиме. В таком случае общее время потока будет больше затраченного времени. Один поток может ожидать завершения другого. Средство просмотра пытается это обнаружить и исключить ненужное ожидание, но при этом вместо исключения отображается слишком много сведений. Это могут быть критически важные сведения.

Обнаружив параллельные потоки в трассировках, укажите, какие потоки находятся в состоянии ожидания, чтобы можно было определить критический путь к запросу. В большинстве случаев поток, который быстро переходит в состояние ожидания, просто ожидает другие потоки. Сосредоточьтесь на других потоках и игнорируйте время ожидания потоков.

### <a id="issue-loading-trace-in-viewer"></a>Нет данных профилирования

Вот несколько моментов, которые можно проверить:

* Если данные, которые вы пытаетесь просмотреть, хранятся дольше нескольких недель, попробуйте ограничить фильтр времени и повторите попытку.
* Убедитесь, что доступ к https://gateway.azureserviceprofiler.net для прокси-серверов или брандмауэра не заблокирован.
* Убедитесь, что ключ инструментирования Application Insights, используемый в приложении, совпадает с ресурсом Application Insights, который вы использовали для включения профилирования. Обычно ключ находится в файле ApplicationInsights.config, но его также можно найти в файле web.config или app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Отчет об ошибках в средстве просмотра профилирования

Отправьте запрос в службу поддержки на портале. Обязательно укажите идентификатор корреляции из сообщения об ошибке.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Ошибка развертывания: каталог не пустой "D:\\домашний\\сайт\\wwwroot\\App_Data\\jobs"

При повторном развертывании веб-приложения в ресурсе веб-приложений с включенным профилировщиком может отобразиться следующая ошибка:

*Каталог не пустой "D:\\домашний\\сайт\\wwwroot\\App_Data\\jobs"*.

Эта ошибка происходит при запуске веб-развертывания с помощью сценариев или в конвейере развертывания Visual Studio Team Services. Чтобы устранить эту проблему, необходимо добавить приведенные ниже параметры развертывания в задачу веб-развертывания.

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Эти параметры удаляют папку, используемую Application Insights Profiler, и разблокируют процесс повторного развертывания. Текущий запущенный экземпляр профилировщика не будет затронут.

## <a name="manual-installation"></a>Ручная установка

При настройке профилировщика в параметры веб-приложения вносятся обновления. Обновления можно применить вручную, если этого требует ваша среда. Например, если приложение выполняется в среде веб-приложений для PowerApps.

1. В области **управления веб-приложением** выберите **Параметры**.
2. Для **платформы .NET Framework** установите версию **4.6**.
3. Установите для параметра **Всегда включено** значение **Включено**.
4. Добавьте параметр приложения **APPINSIGHTS_INSTRUMENTATIONKEY** и задайте для него значение с тем же ключом инструментирования, который используется пакетом SDK.
5. Откройте 	**Дополнительные инструменты**.
6. Выберите **Перейти**, чтобы открыть веб-сайт Kudu.
7. На веб-сайте Kudu выберите **Site extensions** (Расширения сайта).
8. Установите **Application Insights** из коллекции веб-приложений Azure.
9. Перезапустите веб-приложение.

## <a id="profileondemand"></a> Запуск профилировщика вручную

Профилировщик можно активировать вручную одним нажатием кнопки. Предположим, вы выполняете веб-тест производительности. Чтобы понять, как работает ваше веб-приложение под нагрузкой, вам потребуются трассировки. Очень важно иметь возможность контролировать время записи трассировок, поскольку вам известно, когда будет выполняться нагрузочный тест, однако это время может не совпадать с интервалом случайной выборки.
Далее объясняется, как работает этот сценарий.

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Шаг 1. Создание трафика в веб-приложение путем запуска веб-теста производительности (необязательно)

Если в веб-приложение уже поступает входящий трафик или вы хотите создавать трафик вручную, пропустите этот раздел и перейдите к шагу 2.

Перейдите на портал Application Insights в раздел **Настройка > Тестирование производительности**. Нажмите кнопку "Создать", чтобы запустить новый тест производительности.
![создание теста производительности][create-performance-test]

В области **Новый тест производительности** настройте целевой URL-адрес теста. Примите все параметры по умолчанию и запустите нагрузочный тест.

![Настройка нагрузочного теста][configure-performance-test]

Вы увидите, что новый тест будет поставлен в очередь первым, а за ним будет следовать состояние хода выполнения.

![нагрузочный тест отправлен и поставлен в очередь][load-test-queued]

![нагрузочный тест выполняется][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Шаг 2. Запуск профилировщика по запросу

После запуска нагрузочного теста можно запустить профилировщик для записи трассировок для веб-приложения в то время, когда оно получает нагрузку.
Перейдите к области "Настройка профилировщика":

![запись области настройки профилировщика][configure-profiler-entry]

В области **Настройка профилировщика** есть кнопка **Profile Now** (Выполнить профилирование) для вызова профилировщика для всех экземпляров связанных веб-приложений. Кроме того, вы можете просмотреть, когда профилировщик запускался в прошлом.

![Профилировщик по запросу][profiler-on-demand]

Вы увидите уведомление и сведения об изменении состояния выполнения профилировщика.

### <a name="step-3-view-traces"></a>Шаг 3. Просмотр трассировок

После завершения работы профилировщика следуйте инструкциям в уведомлении, чтобы перейти к колонке "Производительность" и просмотреть трассировки.

### <a name="troubleshooting-on-demand-profiler"></a>Устранение неполадок профилировщика по запросу

Иногда после сеанса профилирования по запросу может отображаться сообщение об ошибке времени ожидания профилировщика:

![Ошибка времени ожидания профилировщика][profiler-timeout]

Возможны две причины появления этой ошибки.

1. Сеанс профилирования по запросу прошел успешно, однако для обработки собранных данных Application Insights потребовалось больше времени. Если обработка данных не завершилась в течение 15 минут, на портале отобразится сообщение об истечении времени ожидания. Тем не менее трассировки профилировщика будут показаны через некоторое время. В этом случае просто игнорируйте сообщение об ошибке. Мы работаем над исправлением.

2. Более старая версия агента профилировщика веб-приложения не поддерживает функцию профилирования по запросу. Если вы уже давно включили профиль Application Insights, скорее всего, вам потребуется обновить агент профилировщика для использования возможностей по запросу.
  
Выполните следующие действия, чтобы проверить и установить последний профилировщик.

1. Перейдите к параметрам приложений службы приложений и проверьте, заданы ли следующие параметры:
    * **APPINSIGHTS_INSTRUMENTATIONKEY** — замените правильным ключом инструментирования для Application Insights.
    * **APPINSIGHTS_PORTALINFO** — ASP.NET.
    * **APPINSIGHTS_PROFILERFEATURE_VERSION** — 1.0.0. Если любой из этих параметров не задан, перейдите на панель включения Application Insights, чтобы установить последнее расширение сайта.

2. Перейдите к панели Application Insights на портале службы приложений.

    ![Включение Application Insights на портале службы приложений][enable-app-insights]

3. Если на следующей странице отображается кнопка "Обновить", нажмите ее, чтобы обновить расширение сайта Application Insights, которое установит последнюю версию агента профилировщика.
![Обновление расширения веб-сайта][update-site-extension]

4. Затем щелкните **изменить**, чтобы включить профилировщик, и выберите **ОК** для сохранения изменений.

    ![Измените и сохраните App Insights][change-and-save-appinsights]

5. Вернитесь на вкладку **Параметры приложений** службы приложений, чтобы еще раз убедиться, что перечисленные ниже параметры приложений заданы правильно.
    * **APPINSIGHTS_INSTRUMENTATIONKEY** — замените правильным ключом инструментирования для Application Insights.
    * **APPINSIGHTS_PORTALINFO** — ASP.NET.
    * **APPINSIGHTS_PROFILERFEATURE_VERSION** — 1.0.0.

    ![параметры приложений для профилировщика][app-settings-for-profiler]

6. При необходимости проверьте версию расширения и убедитесь, что нет новых обновлений.

    ![проверка обновлений для расширения][check-for-extension-update]

## <a name="next-steps"></a>Дополнительная информация

* [Работа с Azure Application Insights в Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png
