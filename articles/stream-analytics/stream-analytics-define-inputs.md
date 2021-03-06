---
title: Streamen von Daten als Eingabe in Azure Stream Analytics
description: Hier erfahren Sie, wie eine Datenverbindung mit Stream Analytics eingerichtet wird. Eingaben umfassen einen Datenstrom von Ereignissen und Verweisdaten.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 66e57d95125bd6404ec23302e4c336ab0b71ea78
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099731"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Streamen von Daten als Eingabe in Stream Analytics

Stream Analytics verfügt über eine erstklassige Integration in Azure-Datenströme als Eingaben aus drei Arten von Ressourcen:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Diese Eingaberessourcen können aus demselben Azure-Abonnement wie dem Ihres Stream Analytics-Auftrags oder aus einem anderen Abonnement stammen.

### <a name="compression"></a>Komprimierung
Stream Analytics unterstützt die Komprimierung für alle Datenstrom-Eingabequellen. Derzeit unterstützte Verweistypen sind: Keine, GZip- und Deflate-Komprimierung. Die Unterstützung der Komprimierung steht für Referenzdaten nicht zur Verfügung. Wenn das Eingabeformat komprimierte Avro-Daten sind, werden sie transparent behandelt. Sie müssen bei der Avro-Serialisierung keinen Komprimierungstyp angeben. 

## <a name="create-edit-or-test-inputs"></a>Erstellen, Bearbeiten oder Testen von Eingaben
Sie können das [Azure-Portal](https://portal.azure.com) verwenden, um [neue Eingaben zu erstellen](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal#configure-job-input) und vorhandene Eingaben für Ihren Streamingauftrag anzuzeigen oder zu bearbeiten. Außerdem können Sie anhand von Beispieldaten Eingabeverbindungen und [Abfragen testen](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-manage-job#test-your-query). Beim Schreiben einer Abfrage listen Sie die Eingabe in der FROM-Klausel auf. Sie erhalten die Liste mit den verfügbaren Eingaben im Portal über die Seite **Abfrage**. Wenn Sie mehrere Eingaben verwenden möchten, können Sie sie per `JOIN` verknüpfen oder mehrere `SELECT`-Abfragen schreiben.


## <a name="stream-data-from-event-hubs"></a>Streamen von Daten aus Event Hubs

Azure Event Hubs sind hoch skalierbare Ereigniserfasser zum Veröffentlichen/Abonnieren. Ein Event Hub kann Millionen von Ereignissen pro Sekunde erfassen. Auf diese Weise können Sie riesige Datenmengen verarbeiten und analysieren, die von vernetzten Geräten und Anwendungen erzeugt werden. Event Hubs und Stream Analytics stellen zusammen eine End-to-End-Lösung für Echtzeitanalysen dar. Event Hubs ermöglichen es Ihnen, Ereignisse in Echtzeit an Azure zu übergeben, sodass Stream Analytics-Aufträge diese Ereignisse in Echtzeit verarbeiten können. Beispielsweise können Sie Webklicks, Sensormesswerte oder Onlineprotokollereignisse an Event Hubs senden. Anschließend können Sie Stream Analytics-Aufträge erstellen, um Event Hubs als Eingabedatenströme zum Filtern, Aggregieren und Korrelieren in Echtzeit zu verwenden.

`EventEnqueuedUtcTime` ist der Zeitstempel für die Ankunft eines Ereignisses in einem Event Hub und der Standardzeitstempel für Ereignisse aus Event Hubs für Stream Analytics. Zum Verarbeiten der Daten als Datenstrom mit einem Zeitstempel in der Ereignisnutzlast müssen Sie das Schlüsselwort [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) verwenden.

### <a name="consumer-groups"></a>Verbrauchergruppen
Sie sollten für jede Event Hub-Eingabe in Stream Analytics eine eigene Consumergruppe konfigurieren. Wenn ein Auftrag eine Selbstverknüpfung oder mehrere Eingaben enthält, können einige Eingaben unter Umständen von mehreren nachgeschalteten Lesern gelesen werden. Dies wirkt sich auf die Anzahl der Leser in einer einzelnen Consumergruppe aus. Zur Vermeidung der Überschreitung des Event Hub-Limits von fünf Lesern pro Consumergruppe pro Partition empfiehlt es sich, eine Consumergruppe für jeden Stream Analytics-Auftrag anzugeben. Darüber hinaus gilt ein Grenzwert von 20 Consumergruppen pro Event Hub. Weitere Informationen finden Sie unter [Troubleshoot input connections](stream-analytics-troubleshoot-input.md) (Problembehandlung für Eingabeverbindungen).

### <a name="stream-data-from-event-hubs"></a>Streamen von Daten aus Event Hubs
In der folgenden Tabelle wird jede Eigenschaft im Azure-Portal auf der Seite **Neue Eingabe** erläutert, um Dateneingaben aus einem Event Hub zu streamen:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **Eingabealias** |Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen. |
| **Abonnement** | Wählen Sie das Abonnement, in dem die Event Hub-Ressource vorhanden ist. | 
| **Event Hub-Namespace** | Ein Event Hub-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung einer neuen Event Hub-Instanz auch den Namespace erstellt. |
| **Event Hub-Name** | Der Name des Event Hubs, der als Eingabe verwendet wird. |
| **Event Hub-Richtlinienname** | Die SAS-Richtlinie, die Zugriff auf den Event Hub ermöglicht. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Diese Option wird automatisch ausgefüllt, sofern Sie nicht die Option zum manuellen Festlegen der Event-Hub-Einstellungen wählen.|
| **Event Hub-Consumergruppe** (empfohlen) | Es wird dringend empfohlen, für jeden Stream Analytics-Auftrag eine eigene Consumergruppe zu verwenden. Diese Zeichenfolge identifiziert die Consumergruppe, die zum Erfassen von Daten aus dem Event Hub verwendet werden soll. Wenn keine Consumergruppe angegeben wird, verwendet der Stream Analytics-Auftrag die $Default-Consumergruppe.  |
| **Ereignisserialisierungsformat** | Das Serialisierungsformat (JSON, CSV oder Avro) des eingehenden Datenstroms.  Stellen Sie sicher, dass das JSON-Format der Spezifikation entspricht und Dezimalzahlen keine führende 0 enthalten. |
| **Codieren** | UTF-8 ist derzeit das einzige unterstützte Codierungsformat. |
| **Typ der Ereigniskomprimierung** | Der Komprimierungstyp, der zum Lesen des eingehenden Datenstroms verwendet wird, z.B. „Keine“, „GZip“ oder „Deflate“. |

Wenn Ihre Daten aus einer Event Hub-Datenstromeingabe stammen, haben Sie Zugriff auf folgende Metadatenfelder in Ihrer Stream Analytics-Abfrage:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **EventProcessedUtcTime** |Das Datum und die Uhrzeit der Verarbeitung des Ereignisses durch Stream Analytics. |
| **EventEnqueuedUtcTime** |Das Datum und die Uhrzeit des Ereignisempfangs durch die Event Hubs. |
| **PartitionId** |Die nullbasierte Partitions-ID für den Eingabeadapter. |

Beispielsweise können Sie anhand dieser Felder eine wie im folgenden Beispiel gezeigte Abfrage schreiben:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Bei Verwendung von Event Hub als Endpunkt für IoT Hub-Routen können Sie mithilfe der [GetMetadataPropertyValue-Funktion](https://msdn.microsoft.com/library/azure/mt793845.aspx) auf die IoT Hub-Metadaten zugreifen.
> 

## <a name="stream-data-from-iot-hub"></a>Streamen von Daten aus IoT Hub
Azure IoT Hub ist ein hochgradig skalierbares Erfassungsmodul für das Veröffentlichen und Abonnieren von Ereignissen, das für IoT-Szenarien optimiert ist.

Der Standardzeitstempel von Ereignissen, die von IoT Hub in Stream Analytics stammen, ist der Zeitstempel, an dem das Ereignis in IoT Hub eingeht, also `EventEnqueuedUtcTime`. Zum Verarbeiten der Daten als Datenstrom mit einem Zeitstempel in der Ereignisnutzlast müssen Sie das Schlüsselwort [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) verwenden.

### <a name="consumer-groups"></a>Verbrauchergruppen
Sie sollten für jede IoT Hub-Eingabe in Stream Analytics eine eigene Consumergruppe konfigurieren. Wenn ein Auftrag eine Selbstverknüpfung oder mehrere Eingaben enthält, können einige Eingaben möglicherweise von mehreren nachgeschalteten Lesern gelesen werden. Dies wirkt sich auf die Anzahl der Leser in einer einzelnen Consumergruppe aus. Zur Vermeidung der Überschreitung des Azure IoT Hub-Limits von fünf Lesern pro Consumergruppe pro Partition empfiehlt es sich, eine Consumergruppe für jeden Stream Analytics-Auftrag anzugeben.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurieren von IoT Hub als Datenstromeingabe
In der folgenden Tabelle wird jede Eigenschaft im Azure-Portal auf der Seite **Neue Eingabe** erläutert, wenn Sie IoT Hub als Datenstromeingabe konfigurieren.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **Eingabealias** | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen.|
| **Abonnement** | Wählen Sie das Abonnement, in dem die IoT Hub-Ressource vorhanden ist. | 
| **IoT Hub** | Der Name von IoT Hub, der als Eingabe verwendet wird. |
| **Endpunkt** | Der Endpunkt für IoT Hub.|
| **Name der SAS-Richtlinie** | Die SAS-Richtlinie, die Zugriff auf IoT Hub ermöglicht. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| **Schlüssel für SAS-Richtlinie** | Der Schlüssel für den gemeinsamen Zugriff, der für die Autorisierung des Zugriffs auf IoT Hub verwendet wird.  Diese Option wird automatisch ausgefüllt, es sei denn, Sie wählen die Option zum manuellen Festlegen der IoT Hub-Einstellungen. |
| **Consumergruppe** | Es wird dringend empfohlen, für jeden Stream Analytics-Auftrag eine andere Consumergruppe zu verwenden. Die Consumergruppe, die zum Erfassen von Daten aus IoT Hub verwendet werden soll. Stream Analytics verwendet die $Default-Consumergruppe, sofern nicht anders angegeben.  |
| **Ereignisserialisierungsformat** | Das Serialisierungsformat (JSON, CSV oder Avro) des eingehenden Datenstroms.  Stellen Sie sicher, dass das JSON-Format der Spezifikation entspricht und Dezimalzahlen keine führende 0 enthalten. |
| **Codieren** | UTF-8 ist derzeit das einzige unterstützte Codierungsformat. |
| **Typ der Ereigniskomprimierung** | Der Komprimierungstyp, der zum Lesen des eingehenden Datenstroms verwendet wird, z.B. „Keine“, „GZip“ oder „Deflate“. |


Wenn Ihre gestreamten Daten aus IoT Hub stammen, haben Sie Zugriff auf folgende Metadatenfelder in Ihrer Stream Analytics-Abfrage:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **EventProcessedUtcTime** | Das Datum und die Uhrzeit der Verarbeitung des Ereignisses. |
| **EventEnqueuedUtcTime** | Das Datum und die Uhrzeit des Ereignisempfangs durch den IoT Hub. |
| **PartitionId** | Die nullbasierte Partitions-ID für den Eingabeadapter. |
| **IoTHub.MessageId** | Eine ID, die zum Korrelieren einer bidirektionalen Kommunikation in IoT Hub verwendet wird. |
| **IoTHub.CorrelationId** | Eine ID, die in Nachrichtenantworten und im Feedback in IoT Hub verwendet wird. |
| **IoTHub.ConnectionDeviceId** | Die Authentifizierung-ID, die zum Senden dieser Nachricht verwendet wird. Dieser Wert wird auf servicebound-Nachrichten von IoT Hub gestempelt. |
| **IoTHub.ConnectionDeviceGenerationId** | Die Generierungs-ID des authentifizierten Geräts, das zum Senden dieser Nachricht verwendet wurde. Dieser Wert wird auf servicebound-Nachrichten von IoT Hub gestempelt. |
| **IoTHub.EnqueuedTime** | Der Zeitpunkt, an dem die Nachricht durch IoT Hub empfangen wurde. |


## <a name="stream-data-from-blob-storage"></a>Streamen von Daten aus Blob Storage
Für Szenarien mit großen Mengen unstrukturierter Daten, die in der Cloud gespeichert werden sollen, bietet der Azure Blob Storage eine kostengünstige und skalierbare Lösung. Daten im Blobspeicher werden im Allgemeinen als „ruhende“ Daten angesehen. Blobdaten können aber als Datenstrom von Stream Analytics verarbeitet werden. 

Ein häufig verwendetes Szenario für Blob Storage-Eingaben bei Stream Analytics ist die Protokollverarbeitung. In diesem Szenario wurden Telemetriedatendateien von einem System erfasst, die zur Extraktion aussagekräftiger Daten analysiert und verarbeitet werden müssen.

Der Standardzeitstempel von Blob Storage-Ereignissen in Stream Analytics ist der Zeitstempel, an dem das Blob zuletzt geändert wurde, also `BlobLastModifiedUtcTime`. Zum Verarbeiten der Daten als Datenstrom mit einem Zeitstempel in der Ereignisnutzlast müssen Sie das Schlüsselwort [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) verwenden. Bei einem Stream Analytics-Auftrag werden Daten jede Sekunde per Pullvorgang aus der Azure-Blobspeichereingabe abgerufen, wenn die Blobdatei verfügbar ist. Falls die Blobdatei nicht verfügbar ist, kommt es zu einem exponentiellen Backoff mit einer maximalen Zeitverzögerung von 90 Sekunden.

Eingaben im CSV-Format *müssen* über eine Überschriftenzeile verfügen, um Felder für das Dataset zu definieren. Alle Felder der Überschriftenzeile müssen eindeutig sein.

Stream Analytics unterstützt derzeit keine Deserialisierung von AVRO-Nachrichten, die durch die Event Hub-Erfassung oder benutzerdefinierte Endpunkte von IoT Hub Azure Storage-Containern generiert wurden.

> [!NOTE]
> Stream Analytics unterstützt das Hinzufügen von Inhalten zu einer vorhandenen Blobdatei nicht. Stream Analytics zeigt jede Datei nur einmal an. Des Weiteren werden alle Änderungen, die in der Datei vorgenommen wurden, nachdem der Auftrag die Daten gelesen hat, nicht verarbeitet. Die Methode, alle Daten für eine Blobdatei auf einmal hochzuladen und dann zusätzliche neuere Ereignisse einer anderen, neuen Blobdatei hinzuzufügen, hat sich bewährt.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurieren von Blob Storage als Datenstromeingabe 

In der folgenden Tabelle wird jede Eigenschaft im Azure-Portal auf der Seite **Neue Eingabe** erläutert, wenn Sie Blob Storage als Datenstromeingabe konfigurieren.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **Eingabealias** | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen. |
| **Abonnement** | Wählen Sie das Abonnement, in dem die IoT Hub-Ressource vorhanden ist. | 
| **Speicherkonto** | Der Name des Speicherkontos an, in dem sich die Blobdateien befinden. |
| **Speicherkontoschlüssel** | Der geheime Schlüssel, der dem Speicherkonto zugeordnet ist. Diese Option wird automatisch ausgefüllt, es sei denn, Sie wählen die Option zum manuellen Festlegen der Blob Storage-Einstellungen. |
| **Container** | Der Container für die Blobeingabe. Container stellen eine logische Gruppierung für Blobs bereit, die im Microsoft Azure-Blobdienst gespeichert sind. Wenn Sie ein Blob in den Azure Blob Storage-Dienst hochladen, müssen Sie einen Container für dieses Blob angeben. Sie können entweder **Vorhandenes Element verwenden** oder **Neues Element erstellen** wählen, um einen neuen Container zu erstellen.|
| **Pfadmuster** (optional) | Der Dateipfad, der verwendet wird, um die Blobs im angegebenen Container zu suchen. In dem Pfad können Sie mindestens eine Instanz der folgenden drei Variablen angeben: `{date}`, `{time}` oder `{partition}`.<br/><br/>Beispiel 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Beispiel 2: `cluster1/logs/{date}`<br/><br/>Das Zeichen `*` ist kein zulässiger Wert für das Pfadpräfix. Es sind nur gültige <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure Blob-Zeichen</a> zulässig. Schließen Sie keine Containernamen oder Dateinamen ein. |
| **Datumsformat** (optional) | Wenn Sie die Datumsvariable im Pfad verwenden, wird das Datumsformat, in dem die Dateien organisiert sind, verwendet. Beispiel: `YYYY/MM/DD` |
| **Zeitformat** (optional) |  Wenn Sie die Zeitvariable im Pfad verwenden, wird das Zeitformat, in dem die Dateien organisiert sind, verwendet. Der einzige derzeit unterstützte Wert ist `HH` für Stunden. |
| **Ereignisserialisierungsformat** | Das Serialisierungsformat (JSON, CSV oder Avro) des eingehenden Datenstroms.  Stellen Sie sicher, dass das JSON-Format der Spezifikation entspricht und Dezimalzahlen keine führende 0 enthalten. |
| **Codieren** | Bei CSV und JSON ist UTF-8 gegenwärtig das einzige unterstützte Codierungsformat. |
| **Komprimierung** | Der Komprimierungstyp, der zum Lesen des eingehenden Datenstroms verwendet wird, z.B. „Keine“, „GZip“ oder „Deflate“. |

Wenn Ihre Daten aus einer Blob Storage-Quelle stammen, haben Sie Zugriff auf folgende Metadatenfelder in Ihrer Stream Analytics-Abfrage:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **BlobName** |Der Name des Eingabe-Blobs, aus dem das Ereignis stammt. |
| **EventProcessedUtcTime** |Das Datum und die Uhrzeit der Verarbeitung des Ereignisses durch Stream Analytics. |
| **BlobLastModifiedUtcTime** |Das Datum und die Uhrzeit der letzten Änderung des Blobs. |
| **PartitionId** |Die nullbasierte Partitions-ID für den Eingabeadapter. |

Beispielsweise können Sie anhand dieser Felder eine wie im folgenden Beispiel gezeigte Abfrage schreiben:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
