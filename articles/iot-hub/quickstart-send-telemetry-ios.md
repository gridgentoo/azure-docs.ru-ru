---
title: Краткое руководство по отправке телеметрии в Центр Интернета вещей | Документация Майкрософт
description: В этом кратком руководстве выполняется пример приложения iOS по отправке имитированной телеметрии в Центр Интернета вещей и чтение телеметрии из Центра Интернета вещей для обработки в облаке.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/20/2018
ms.author: kgremban
ms.openlocfilehash: dbc1cc4a72d0346c92d506358c39a66a4d780b32
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309751"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Краткое руководство. Отправка данных телеметрии с устройства в Центр Интернета вещей (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Центр Интернета вещей — это служба Azure, которая позволяет получать большие объемы телеметрии с ваших устройств Центра Интернета вещей в облаке на хранение или обработку. В этой статье телеметрия отправляется из приложения имитированного устройства в Центр Интернета вещей. Затем можно просмотреть данные из серверной части приложения. 

В этой статье используется предварительно написанное приложение Swift для отправки телеметрии и служебная программа CLI для чтения телеметрии из Центра Интернета вещей. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

- Скачать пример кода из [примеров Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip). 
- Последняя версия [XCode](https://developer.apple.com/xcode/), выполняющая последнюю версию пакета SDK для iOS. Это краткое руководство протестировано с использованием версий XCode 9.3 и iOS 11.3;
- Последняя версия [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).
- Служебная программа iothub-explorer, читающая телеметрию из Центра Интернета вещей. Чтобы установить ее, сначала установите [Node.js](https://nodejs.org) версии 4.x.x или более поздней, а затем выполните команду: 

   ```sh
   sudo npm install -g iothub-explorer
   ```

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]


## <a name="register-a-device"></a>Регистрация устройства

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. В этом кратком руководстве для регистрации имитируемого устройства используется Azure CLI.

1. Добавьте расширение CLI Центра Интернета вещей и создайте удостоверение устройства. Замените `{YourIoTHubName}` именем Центра Интернета вещей:

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

    Если вы выбрали другое имя для устройства, обновите имя устройства в примерах приложений перед их запуском.

1. Выполните следующую команду, чтобы получить _строку подключения устройства_ для зарегистрированного устройства:

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Запишите строку подключения устройства, которая выглядит как `Hostname=...=`. Это значение вам понадобится позже.

1. Кроме того, необходима _строка подключения службы_, чтобы разрешить серверным приложениям подключаться к Центру Интернета вещей и получать сообщения с устройства в облако. Следующая команда извлекает строку подключения службы для Центра Интернета вещей:

   ```azurecli-interactive
   az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
   ```

   Запишите строку подключения службы, которая выглядит как `Hostname=...=`. Это значение вам понадобится позже.

## <a name="send-simulated-telemetry"></a>Отправка имитированной телеметрии

Тот же пример приложения выполняется на устройстве iOS, которое подключается к конечной точке конкретного устройства Центра Интернета вещей и отправляет имитированную телеметрию температуры и влажности. 

### <a name="install-cocoapods"></a>Установка CocoaPods

CocoaPods управляет зависимостями проектов iOS, которые используют сторонние библиотеки.

В окне терминала перейдите в папку Azure-IoT-Samples-iOS, скачанную на этапе подготовки. Перейдите к примеру проекта:

```sh
cd quickstart/sample-device
```

Убедитесь, что XCode закрыт, а затем выполните следующую команду, чтобы установить CocoaPods, объявленный в файле **podfile**:

```sh
pod install
```

Вместе с установкой модулей, необходимых для проекта, команда установки также создает файл рабочей области XCode, который уже настроен для использования pod с зависимостями. 

### <a name="run-the-sample-application"></a>Запуск примера приложения 

1. Откройте пример рабочей области в XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Разверните проект **Пример клиента MQTT**, а затем разверните папку с тем же именем.  
3. Откройте **ViewController.swift** для редактирования в XCode. 
4. Найдите переменную **connectionString** и обновите значение с помощью строки подключения устройства, записанной ранее.
5. Сохраните изменения. 
6. Выполните проект в эмуляторе устройства, нажав кнопку **Сборка и запуск** или клавиши **COMMAND+R**. 

   ![Запуск проекта](media/quickstart-send-telemetry-ios/run-sample.png)

7. В открывшемся эмуляторе в примере приложения выберите **Запустить**.

На следующем снимке экрана показан пример выходных данных, когда приложение отправляет имитируемую телеметрию в Центр Интернета вещей:

   ![Запуск виртуального устройства](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Чтение данных телеметрии из концентратора

Пример приложения, выполняемого в эмуляторе XCode, показывает данные о сообщениях, отправленных с устройства. Вы также можете просматривать данные в Центре Интернета вещей по мере их получения. Служебная программа CLI `iothub-explorer` подключается к конечной точке **событий** на стороне сервера в Центре Интернета вещей. 

Откройте новое окно терминала. Выполните следующую команду, заменив {your hub service connection string} строкой подключения службы, полученной в начале этой статьи:

```sh
iothub-explorer monitor-events myiOSdevice --login "{your hub service connection string}"
```

На следующем снимке экрана показан тип телеметрии, используемый в окне терминала:

![Просмотр телеметрии](media/quickstart-send-telemetry-ios/view-telemetry.png)

Если при выполнении команды iothub-explorer возникает сообщение об ошибке, еще раз убедитесь, что для Центра Интернета вещей используется *строка подключения службы*, а не *строка подключения устройства* для устройства Центра Интернета вещей. Обе строки подключения начинаются с **Hostname={iothubname}**, но строка подключения службы содержит свойство **SharedAccessKeyName**, тогда как строка подключения устройства содержит **DeviceID**. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы настраивали Центр Интернета вещей, регистрировали устройство, отправляли имитируемую телеметрию в концентратор из устройства iOS, а также читали телеметрию из концентратора. 

Чтобы узнать, как управлять имитированным устройством из внутреннего приложения, перейдите к следующему краткому руководству.

> [!div class="nextstepaction"]
> [Краткое руководство по управлению подключенным к Центру Интернета вещей устройством (Java)](quickstart-control-device-node.md)

<!-- Links -->
[lnk-process-d2c-tutorial]: tutorial-routing.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
