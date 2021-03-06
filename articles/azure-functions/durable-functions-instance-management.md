---
title: Управление экземплярами в устойчивых функциях (Azure)
description: Сведения о том, как управлять экземплярами в расширении устойчивых функций для Функций Azure.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 5cb3ccbc949f8250101fab6cb7899b859149fdfd
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341098"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Управление экземплярами в устойчивых функциях (Функции Azure)

Экземпляры оркестрации [устойчивых функций](durable-functions-overview.md) можно запускать и завершать, а также отправлять в них запросы и события уведомления. Все операции управления экземпляром осуществляются с помощью [привязки клиента оркестрации](durable-functions-bindings.md). В этой статье подробно описаны все операции по управлению экземплярами.

## <a name="starting-instances"></a>Запуск экземпляров

Метод [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) из класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) запускает новый экземпляр функции оркестратора. Экземпляры этого класса можно получить с помощью привязки `orchestrationClient`. На внутреннем уровне этот метод ставит в очередь управления сообщение, которое инициирует запуск функции с заданным именем и привязкой к триггеру `orchestrationTrigger`. 

Задача завершается при запуске процесса оркестрации. Процесс оркестрации должен запуститься в течение 30 секунд. Если на это уходит больше времени, создается исключение `TimeoutException`. 

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) принимает следующие параметры:

* **Name.** Имя функции оркестратора, для которой назначается запуск.
* **Input.** Любые данные, сериализуемые в формат JSON, которые нужно передать в функцию оркестратора как входные данные.
* **InstanceId.** Уникальный идентификатор экземпляра (необязательно). Если идентификатор экземпляра не указан, ему будет присвоено случайное значение.

Ниже приведен простой пример на C#.

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

В языках, отличных от .NET, также можно использовать привязку к выходным данным функции для запуска новых экземпляров. В этом случае можно применить любой объект, сериализуемый в формат JSON, с тремя полями для указанных выше параметров. Например, рассмотрим следующую функцию JavaScript:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> Рекомендуем использовать случайный идентификатор экземпляра. Это помогает более равномерно распределять нагрузку при масштабировании функций оркестратора между несколькими виртуальными машинами. Указывать конкретные идентификаторы экземпляров имеет смысл в том случае, когда они должны поступать из внешнего источника, а также при реализации схемы [одноэлементного оркестратора](durable-functions-singletons.md).

## <a name="querying-instances"></a>Запросы к экземплярам

Метод [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) из класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) подает запрос на сведения о состоянии экземпляра оркестрации. Он принимает параметры `instanceId` (обязательный), `showHistory` (необязательный) и `showHistoryOutput` (необязательный). Если `showHistory` имеет значение `true`, ответ будет содержать журнал выполнения. Если `showHistoryOutput` имеет значение `true`, журнал выполнения будет содержать выходные данные действия. Этот метод возвращает объект со следующими свойствами:

* **Name.** Имя функции оркестратора.
* **InstanceId.** Идентификатор экземпляра оркестрации (будет совпадать с переданным значением `instanceId`).
* **CreatedTime.** Время создания, то есть время начала выполнения функции оркестратора.
* **LastUpdatedTime.** Время прохождения последней контрольной точки оркестрации.
* **Input.** Входные данные функции в формате JSON.
* **CustomStatus.** Настраиваемое значение состояния оркестрации в формате JSON. 
* **Output.** Выходные данные функции в формате JSON (если выполнение функции успешно завершено). Если выполнение функции оркестратора завершилась сбоем, это свойство содержит сведения об ошибке. Если функция оркестратора была прервана, это свойство содержит сведения о причине завершения (при их наличии).
* **RuntimeStatus.** Состояние выполнения может иметь одно из следующих значений:
    * **Pending**. Выполнение экземпляра было запланировано, но еще не начато.
    * **Running.** Началось выполнение экземпляра.
    * **Completed.** Выполнение экземпляра завершилось в обычном режиме.
    * **ContinuedAsNew.** Экземпляр выполнил перезапуск с очисткой журнала выполнения. Это переходное состояние.
    * **Failed.** Выполнение экземпляра завершилось ошибкой.
    * **Terminated.** Выполнение экземпляра прервано.
* **History**: журнал выполнения оркестрации. Это поле заполняется, только если `showHistory` имеет значение `true`.
    
Этот метод возвращает `null`, если экземпляр не существует или его выполнение еще не начато.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```
## <a name="querying-all-instances"></a>Запросы ко всем экземплярам

С помощью метода `GetStatusAsync` вы можете запросить данные о состоянии всех экземпляров оркестрации. Метод не принимает параметры. Чтобы отменить метод, вы можете передать объект `CancellationToken`. Метод возвращает объекты с теми же свойствами, что и метод `GetStatusAsync` с параметрами, но не возвращает журнал. 

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    TraceWriter log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.Info(JsonConvert.SerializeObject(instance));
    };
}
```

## <a name="terminating-instances"></a>Прерывание выполнения экземпляров

Запуск экземпляра оркестрации можно прервать с помощью метода [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) из класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Он принимает два параметра: `instanceId` и строку `reason`, которая сохраняется в журнале выполнения и в сведениях о состоянии экземпляра. Выполнение экземпляра прекращается по достижении очередной точки ожидания (`await`). Если он уже находится в состоянии ожидания (`await`), выполнение прекращается немедленно. 

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Завершение работы экземпляр в настоящее время не распространяется. Функции действий и суборкестрации будут выполняться до конца, даже если экземпляр оркестрации, вызвавший их, будет завершен.

## <a name="sending-events-to-instances"></a>Отправка событий в экземпляры

В выполняющиеся экземпляры можно отсылать уведомления о событиях с помощью метода [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) из класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Чтобы обрабатывать эти уведомления, экземпляр должен ожидать вызова при помощи функции [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) принимает следующие параметры:

* **InstanceId** — уникальный идентификатор экземпляра;
* **EventName** — имя отправляемого события;
* **EventData** — данные полезной нагрузки в формате JSON, отправляемые в экземпляр.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!WARNING]
> Если экземпляра оркестрации с указанным *идентификатором* не существует или этот экземпляр не ожидает указанное *имя события*, сообщение о событии игнорируется. Дополнительные сведения об этом поведении см. в [описании проблемы на GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="wait-for-orchestration-completion"></a>Дождитесь, пока завершится оркестрация.

Класс [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) предоставляет API-интерфейс [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_), который позволяет синхронно получать фактические выходные данные от экземпляра оркестрации. Этот метод по умолчанию использует значения 10 секунд для `timeout` и 1 секунду для `retryInterval`, если вы не укажете свои значения.  

Ниже приведен пример функции HTTP-триггера, в котором показано, как использовать этот API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

Следующая строка вызывает эту функцию с временем ожидания в 2 секунды и интервалом повторов 0,5 секунды:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

В зависимости от того, сколько времени уйдет на получение ответа от экземпляра оркестрации, возможны два варианта развития событий.

1. Если экземпляр оркестрации завершит работу до истечения указанного времени ожидания (2 секунды в нашем примере), то фактический ответ от экземпляра оркестрации доставляется синхронно:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. Если экземпляр оркестрации не успеет завершить работу до истечения указанного времени ожидания (2 секунды в нашем примере), возвращается ответ по умолчанию, как указано в **обнаружении URL-адреса API HTTP**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Формат URL-адресов веб-перехватчиков может отличаться в зависимости от того, какая версия узла Функций Azure выполняется. Описанный выше пример предназначен для узла Функций Azure 2.0.

## <a name="retrieving-http-management-webhook-urls"></a>Получение URL-адресов веб-перехватчика для управления HTTP

Внешние системы могут взаимодействовать с устойчивыми функциями через URL-адреса веб-перехватчиков, которые содержаться в ответе по умолчанию, описанном в разделе [Обнаружение URL-адреса API HTTP](durable-functions-http-api.md). Но доступ к URL-адресам веб-перехватчиков можно получить и программными средствами в клиенте оркестрации или функции действия с помощью метода [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) имеет один параметр:

* **InstanceId** — уникальный идентификатор экземпляра.

Метод возвращает экземпляр класса [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) со следующими строковыми свойствами:

* **Id** — идентификатор экземпляра оркестрации (будет совпадать с переданным значением `InstanceId`).
* **StatusQueryGetUri** — URL-адрес состояния экземпляра оркестрации.
* **SendEventPostUri** — URL-адрес вызова события экземпляра оркестрации.
* **TerminatePostUri** — URL-адрес завершения работы экземпляра оркестрации.

Функции действий позволяют отправлять экземпляр класса [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) во внешние системы, чтобы отслеживать или вызывать события для оркестрации:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> Узнайте, как использовать [HTTP-интерфейсы API для управления экземплярами](durable-functions-http-api.md)
