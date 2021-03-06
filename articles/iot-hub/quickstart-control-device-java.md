---
title: Краткое руководство по управлению устройством из Центра Интернета вещей (Java) | Документация Майкрософт
description: 'В этом кратком руководстве описано, как запустить два примера приложений Java: внутреннее приложение, которое может удаленно управлять подключенными к центру устройствами, и приложение, которое имитирует подключенное к центру устройство, которым можно управлять удаленно.'
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/22/2018
ms.author: dobett
ms.openlocfilehash: 5da4248f0b0a72c3614b4c3e5ea042c4341f4e03
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38573506"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-java"></a>Краткое руководство по управлению подключенным к Центру Интернета вещей устройством (Java)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Центр Интернета вещей — это служба Azure, которая позволяет получать большие объемы данных телеметрии с устройств Центра Интернета вещей в облаке и управлять устройствами из облака. В этом кратком руководстве управление имитированным устройством, подключенным к Центру Интернета вещей, осуществляется с помощью *прямого метода*. Этот метод позволяет удаленно изменить поведение подключенного к Центру Интернета вещей устройства.

В этом кратком руководстве используется два предварительно созданных приложения Java:

* Приложение имитированного устройства, реагирующее на прямые методы, вызванные из внутреннего приложения. Чтобы получать вызовы прямого метода, это приложение подключается к конечной точке конкретного устройства в Центре Интернета вещей.
* Внутреннее приложение, вызывающее прямые методы в имитированном устройстве. Чтобы вызвать прямой метод в устройстве, это приложение подключается к конечной точке на стороне службы в Центре Интернета вещей.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Примеры приложений, запускаемые в рамках этого краткого руководства, написаны на языке Java. Вам потребуется установить Java SE 8 или более позднюю версию на компьютере для разработки.

Java, предназначенный для нескольких платформ, можно скачать в [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

Текущую версию Java на компьютере, на котором ведется разработка, можно проверить, используя следующую команду:

```cmd/sh
java --version
```

Для создания примеров необходимо установить Maven версии 3. Maven, предназначенный для нескольких платформ, можно скачать в [Apache Maven](https://maven.apache.org/download.cgi).

Текущую версию Maven на компьютере, на котором ведется разработка, можно проверить, используя следующую команду:

```cmd/sh
mvn --version
```

Если вы это еще не сделали, загрузите пример проекта Java по адресу https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip и извлеките ZIP-архив.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Если вы закончили работу с предыдущим [руководством по отправке данных телеметрии с устройства в Центр Интернета вещей](quickstart-send-telemetry-java.md), этот шаг можно пропустить.

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Регистрация устройства

Если вы закончили работу с предыдущим [руководством по отправке данных телеметрии с устройства в Центр Интернета вещей](quickstart-send-telemetry-java.md), этот шаг можно пропустить.

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. В этом кратком руководстве для регистрации имитируемого устройства используется Azure CLI.

1. Добавьте расширение CLI Центра Интернета вещей и создайте удостоверение устройства. Замените переменную `{YourIoTHubName}` именем своего Центра Интернета вещей:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

    Если вы выбрали другое имя для устройства, обновите имя устройства в примерах приложений перед их запуском.

2. Выполните следующую команду, чтобы получить _строку подключения устройства_ для зарегистрированного устройства:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Запишите строку подключения устройства, которая выглядит как `Hostname=...=`. Это значение понадобится позже в рамках этого краткого руководства.

## <a name="retrieve-the-service-connection-string"></a>Получение строки подключения к службе

Чтобы разрешить внутренним приложениям подключаться к Центру Интернета вещей и получать сообщения, необходима _строка подключения к службе_. Следующая команда извлекает строку подключения службы для Центра Интернета вещей:

```azurecli-interactive
az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
```

Запишите строку подключения службы, которая выглядит как `Hostname=...=`. Это значение понадобится позже в рамках этого краткого руководства.

## <a name="listen-for-direct-method-calls"></a>Ожидание передачи данных при вызове прямого метода

Приложение имитированного устройства подключается к конечной точке конкретного устройства в Центре Интернета вещей, отправляет имитированные данные телеметрии и ожидает передачи данных при вызове прямого метода из центра. В рамках этого краткого руководства вызов прямого метода из центра инициирует изменение интервала, с которым устройство отправляет данные телеметрии. После выполнения прямого метода имитированное устройство отправляет подтверждение в центр.

1. В окне терминала перейдите в корневую папку примера проекта Java. Затем перейдите в папку **iot-hub\Quickstarts\simulated-device-2**.

2. Откройте файл **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** в любом текстовом редакторе.

    Замените значение переменной `connString` записанной ранее строкой подключения к устройству. Сохраните изменения в файле **SimulatedDevice.java**.

3. Установите необходимые библиотеки и создайте приложение имитированного устройства, выполнив в окне терминала следующие команды:

    ```cmd/sh
    mvn clean package
    ```

4. Запустите приложение имитированного устройства, выполнив в окне терминала следующие команды:

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    На следующем снимке экрана показан пример выходных данных, когда приложение имитированного устройства отправляет данные телеметрии в Центр Интернета вещей:

    ![Запуск виртуального устройства](media/quickstart-control-device-java/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Вызов прямого метода

Внутреннее приложение подключается к конечной точке на стороне службы в Центре Интернета вещей. Приложение выполняет вызовы прямых методов к устройству через Центр Интернета вещей и ожидает подтверждений. Внутреннее приложение Центра Интернета вещей обычно работает в облаке.

1. В другом окне терминала перейдите в корневую папку примера проекта Java. Затем перейдите в папку **iot-hub\Quickstarts\back-end-application**.

2. Откройте файл **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** в любом текстовом редакторе.

    Замените значение переменной `iotHubConnectionString` записанной ранее строкой подключения к службе. Сохраните изменения в файле **BackEndApplication.java**.

3. Установите необходимые библиотеки и создайте внутреннее приложение, выполнив в окне терминала следующие команды:

    ```cmd/sh
    mvn clean package
    ```

4. Запустите внутреннее приложение, выполнив в окне терминала следующие команды:

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    На следующем снимке экрана показан пример выходных данных, когда приложение выполняет вызов прямого метода к устройству и получает подтверждение:

    ![Запуск внутреннего приложения](media/quickstart-control-device-java/BackEndApplication.png)

    После запуска внутреннего приложения в окне консоли, в котором выполняется имитированное устройство, отобразится сообщение и изменится интервал, с которым это приложение отправляет сообщения:

    ![Изменения в имитированном клиенте](media/quickstart-control-device-java/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

В рамках этого краткого руководства вы вызвали прямой метод в устройстве из внутреннего приложения, а также ответили на этот вызов в приложении имитированного устройства.

Чтобы узнать, как маршрутизировать сообщения с устройства в облако в разные расположения в облаке, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Маршрутизация сообщений с помощью Центра Интернета вещей (Java)](tutorial-routing.md)