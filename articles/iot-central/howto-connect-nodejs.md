---
title: Verbinden einer generischen Node.js-Clientanwendung mit Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie als Geräteentwickler ein generisches Node.js-Gerät mit Ihrer Azure IoT Central-Anwendung verbinden.
author: dominicbetts
ms.author: dobett
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4d61810adb24bb56b849a0a07ad1f097a1c33744
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198081"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Verbinden einer generischen Clientanwendung mit Ihrer Azure IoT Central-Anwendung (Node.js)

In diesem Artikel wird beschrieben, wie Sie als Geräteentwickler eine generische Node.js-Anwendung, die ein physisches Gerät darstellt, mit Ihrer Microsoft Azure IoT Central-Anwendung verbinden.

## <a name="before-you-begin"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

1. Eine Azure IoT Central-Anwendung. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Anwendung](quick-deploy-iot-central.md).
1. Einen Entwicklungscomputer mit installierter [Node.js](https://nodejs.org/) Version 4.0.0 oder höher. Sie können `node --version` in der Befehlszeile ausführen, um Ihre Version zu überprüfen. Node.js ist für eine Vielzahl von Betriebssystemen verfügbar.

## <a name="create-a-device-template"></a>Erstellen einer Gerätevorlage

In Ihrer Azure IoT Central-Anwendung benötigen Sie eine Gerätevorlage, bei denen die folgenden Messungen und Geräteeigenschaften definiert sind:

### <a name="telemetry-measurements"></a>Telemetriemessungen

Fügen Sie auf der Seite **Messungen** folgende Telemetriedaten hinzu:

| Anzeigename | Feldname  | Units | Min | max | Dezimalstellen |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatur  | Temperatur | F     | 60  | 110 | 0              |
| Luftfeuchtigkeit     | Luftfeuchtigkeit    | %     | 0   | 100 | 0              |
| Pressure     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  Die Telemetriemessung gibt Daten als Gleitkommazahl aus.

Geben Sie Feldnamen genau wie in der Tabelle angegeben in die Gerätevorlage ein. Wenn die Feldnamen nicht mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, können die Telemetriedaten nicht in der Anwendung angezeigt werden.

### <a name="state-measurements"></a>Statusmessungen

Fügen Sie auf der Seite **Messungen** folgenden Status hinzu:

| Anzeigename | Feldname  | Wert 1 | Anzeigename | Wert 2 | Anzeigename |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Fan Mode     | fanmode     | 1       | Wird ausgeführt      | 0       | Beendet      |

> [!NOTE]
  Die Statusmessung gibt Daten vom Typ „string“ aus.

Geben Sie Feldnamen genau wie in der Tabelle angegeben in die Gerätevorlage ein. Wenn die Feldnamen nicht mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, kann der Status nicht in der Anwendung angezeigt werden.

### <a name="event-measurements"></a>Ereignismessung

Fügen Sie auf der Seite **Messungen** folgendes Ereignis hinzu:

| Anzeigename | Feldname  | Severity |
| ------------ | ----------- | -------- |
| Überhitzung  | overheat    | Error    |

> [!NOTE]
  Die Ereignismessung gibt Daten vom Typ „string“ aus.

### <a name="device-properties"></a>Geräteeigenschaften

Fügen Sie auf der Seite **Eigenschaften** folgende Geräteeigenschaften hinzu:

| Anzeigename        | Feldname        | Datentyp |
| ------------------- | ----------------- | --------- |
| Seriennummer       | serialNumber      | text      |
| Gerätehersteller | manufacturer      | text      |

Geben Sie die Feldnamen genau wie in der Tabelle angegeben in die Gerätevorlage ein. Wenn die Feldnamen nicht mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, kann der Geräteeigenschaftswert nicht in der Anwendung angezeigt werden.

### <a name="settings"></a>Einstellungen

Fügen Sie auf der Seite **Einstellungen** folgende **Nummerneinstellungen** hinzu:

| Anzeigename    | Feldname     | Units | Dezimalstellen | Min | max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Lüfterdrehzahl       | fanSpeed       | U/Min   | 0        | 0   | 3000 | 0       |
| Set Temperature | setTemperature | F     | 0        | 20  | 200  | 80      |

Geben Sie den Feldnamen genau wie in der Tabelle angegeben in die Gerätevorlage ein. Wenn die Feldnamen nicht mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, kann das Gerät den Einstellungswert nicht empfangen.

## <a name="add-a-real-device"></a>Hinzufügen eines echten Geräts

Fügen Sie in Ihrer Azure IoT Central-Anwendung ein echtes Gerät über die Gerätevorlage hinzu, die Sie erstellen, und notieren Sie sich die Verbindungszeichenfolge des Geräts. Ausführliche Anweisungen zum Herstellen einer Verbindung mit einer Node.js-Anwendung und mit IoT Central finden Sie im Tutorial zum Hinzufügen eines Geräts unter [Generieren der Verbindungszeichenfolge für das echte Gerät über die Anwendung](tutorial-add-device.md#generate-connection-string-for-real-device-from-application) und [Vorbereiten des Clientcodes](tutorial-add-device.md#prepare-the-client-code).

### <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung

In den folgenden Schritten wird gezeigt, wie eine Clientanwendung mit Implementierung des echten Geräts, das Sie der Anwendung hinzugefügt haben, erstellt wird. Die Node.js-Anwendung stellt hier das echte physische Gerät dar. 

1. Erstellen Sie auf Ihrem Computer den Ordner `connected-air-conditioner-adv`. Navigieren Sie in der Befehlszeilenumgebung zu diesem Ordner.

1. Um das Node.js-Projekt zu initialisieren, führen Sie die folgenden Befehle aus:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Erstellen Sie im Ordner `connected-air-conditioner-adv` eine Datei namens **connectedAirConditionerAdv.js**.

1. Fügen Sie am Anfang der Datei **connectedAirConditionerAdv.js** die folgenden `require`-Anweisungen hinzu:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Fügen Sie der Datei folgende Variablendeklarationen hinzu:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

  > [!NOTE]
  > Azure IoT Central verwendet nun Azure IoT Hub Device Provisioning Service für alle Geräteverbindungen. Führen Sie die Schritte zum [Abrufen der Verbindungszeichenfolge des Geräts](concepts-connectivity.md#getting-device-connection-string) aus, und fahren Sie dann mit dem Tutorial fort. Ausführliche Anweisungen finden Sie darüber hinaus im Tutorial zum Hinzufügen eines Geräts unter [Vorbereiten des Clientcodes](tutorial-add-device.md#prepare-the-client-code).


  Aktualisieren Sie den Platzhalter `{your device connection string}` mit der Verbindungszeichenfolge des Geräts. In diesem Beispiel wird `targetTemperature` auf NULL initialisiert, wobei Sie optional die aktuelle Ablesung vom Gerät oder den Wert vom Gerätezwilling übernehmen können. 

1. Fügen Sie zum Senden von Telemetrie-, Status- und Ereignismessungen an Ihre Azure IoT Central-Anwendung folgende Funktion zur Datei hinzu:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({ 
        temperature: temperature, 
        humidity: humidity, 
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Fügen Sie zum Senden von Eigenschaften an Ihre Azure IoT Central-Anwendung folgende Funktion zu Ihrer Datei hinzu:

    ```javascript
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Um die Einstellungen zu definieren, auf die Ihr Gerät reagiert, fügen Sie folgende Definition hinzu:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Um aktualisierte Einstellungen von Ihrer Azure IoT Central-Anwendung zu verarbeiten, fügen Sie Folgendes zur Datei hinzu:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Um eine Verbindung mit Azure IoT Central herzustellen und die Funktionen im Clientcode einzubinden, fügen Sie Folgendes hinzu:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Ausführen der Node.js-Anwendung

Führen Sie in der Befehlszeilenumgebung folgenden Befehl aus:

```cmd/sh
node connectedAirConditionerAdv.js
```

Als Operator in der Azure IoT Central-Anwendung für Ihre echtes Gerät können Sie Folgendes tun:

* Anzeigen von Telemetrie auf der Seite **Messungen**:

    ![Anzeigen von Telemetriedaten](media/howto-connect-nodejs/viewtelemetry.png)

* Anzeigen der von Ihrem Gerät gesendeten Geräteeigenschaftswerte auf der Seite **Eigenschaften**: Die Kacheln mit den Geräteeigenschaften werden aktualisiert, wenn die Verbindung hergestellt wurde. 

    ![Anzeigen von Geräteeigenschaften](media/howto-connect-nodejs/viewproperties.png)

* Festlegen der Lüfterdrehzahl und Zieltemperatur auf der Seite **Einstellungen**: Die Einstellungswerte werden synchronisiert, wenn die Verbindung hergestellt wurde. 

    ![Festlegen der Lüfterdrehzahl](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie ein generischer Node.js-Client mit Ihrer Azure IoT Central-Anwendung verbunden wird, werden als Nächstes die folgenden Schritte empfohlen:
* [Vorbereiten und Verbinden eines Raspberry Pi](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
