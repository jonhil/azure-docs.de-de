---
title: Azure Media Services-LiveEvent und ein Cloud-DVR | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, was LiveOutput ist und wie ein Cloud-DVR verwendet wird.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 8df43a9b2c518e77d14dd5cb392b042b0b4846e2
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407965"
---
# <a name="using-a-cloud-dvr"></a>Verwenden eines Cloud-DVR

[LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) ermöglicht es Ihnen, die Eigenschaften des ausgehenden Livestreams zu steuern, z.B. wie viel des Streams aufgezeichnet wird (beispielsweise die Kapazität des Cloud-DVR) und ob die Zuschauer mit der Wiedergabe des Livestreams beginnen können oder nicht. Die Beziehung zwischen einem **LiveEvent** und dem zugehörigen **LiveOutput** ist ähnlich wie bei einer traditionellen Fernsehsendung, bei der ein Kanal (**LiveEvent**) einen konstanten Videodatenstrom darstellt und eine Aufzeichnung (**LiveOutput**) auf ein bestimmtes Zeitsegment (z.B. Abendnachrichten von 18:30 bis 19:00 Uhr) ausgerichtet ist. Sie können Fernsehsendungen mit einem digitalen Videorecorder (DVR) aufnehmen – die entsprechende Funktion in LiveEvents wird über die ArchiveWindowLength-Eigenschaft verwaltet. Es handelt sich um eine ISO-8601-Zeitspanne (z.B. PTHH:MM:SS), die die Kapazität des Digitalrecorders angibt. Diese kann von mindestens 3 Minuten auf bis zu maximal 25 Stunden eingestellt werden.

## <a name="liveoutput"></a>LiveOutput

Der Wert **ArchiveWindowLength** legt fest, wie weit zurück ein Zuschauer von der aktuellen Liveposition aus suchen kann.  **ArchiveWindowLength** legt außerdem fest, auf welche Länge Clientmanifeste anwachsen können.

Angenommen, Sie streamen ein Fußballspiel, und der Wert für **ArchiveWindowLength** ist nur auf 30 Minuten festgelegt. Ein Zuschauer, der 45 Minuten nach Spielbeginn damit beginnt, das Ereignis zu verfolgen, kann höchstens eine Suche bis zur 15-Minuten-Markierung durchführen. Ihr **LiveOutput** für das Spiel wird fortgesetzt, bis das **LiveEvent** beendet wird, aber Inhalt außerhalb von **ArchiveWindowLength** wird fortlaufend aus dem Speicher verworfen und ist nicht wiederherstellbar. In diesem Beispiel würden die Videodaten zwischen dem Ereignisstart und der 15-Minuten-Markierung von Ihrem DVR und aus dem Container im Blobspeicher für das [Medienobjekt](https://docs.microsoft.com/rest/api/media/assets) gelöscht werden. Das Archiv ist nicht wiederherstellbar und wird aus dem Container im Azure-Blobspeicher gelöscht.

Jedem **LiveOutput**-Objekt ist ein **Medienobjekt** zugeordnet, das zum Aufzeichnen des Videos im zugeordneten Azure Blob Storage-Container verwendet wird. Zum Veröffentlichen von LiveOutput müssen Sie einen **StreamingLocator** für das zugehörige **Medienobjekt** erstellen. Nachdem ein [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) erstellt wurde, können Sie eine Streaming-URL erstellen, die Sie Ihren Zuschauern bereitstellen.

Ein **LiveEvent** unterstützt bis zu drei gleichzeitig ausgeführte **LiveOutput**-Objekte, sodass Sie maximal 3 Aufzeichnungen/Archive aus einem Livestream erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Angenommen, Sie müssen rund um die Uhr einen linearen Livefeed senden. Sie möchten jedoch den ganzen Tag über „Aufzeichnungen“ der unterschiedlichen Programme erstellen, die Sie Ihren Kunden auf Abruf zur Nachbetrachtung anbieten können. Für dieses Szenario erstellen Sie zunächst einen primären LiveOutput mit einem kurzen Archivfenster von maximal einer Stunde. Dies ist der primäre Livestream, in den sich die Zuschauer einschalten können. Sie erstellen einen **StreamingLocator** für diesen **LiveOutput** und veröffentlichen ihn als „Livefeed“ in Ihrer Anwendung oder auf Ihrer Website. Während das **LiveEvent** ausgeführt wird, können Sie programmgesteuert einen zweiten parallelen **LiveOutput** zu Beginn eines Programms erstellen (oder 5 Minuten früher, um Material zum späteren Schneiden bereitzustellen). Dieser zweite **LiveOutput** kann 5 Minuten nach Programmende gelöscht werden. Mit diesem zweiten **Medienobjekt** können Sie einen neuen **StreamingLocator** erstellen, um dieses Programm als On-Demand-Medienobjekt in Ihrem Anwendungskatalog bereitzustellen. Sie können diesen Vorgang für andere Programmgrenzen oder für Highlights, die Sie als On-Demand-Videos freischalten möchten, mehrfach wiederholen, während der „Livefeed“ für den ersten **LiveOutput** weiterhin den linearen Feed sendet. 

> [!NOTE]
> Ein **LiveOutput** wird bei der Erstellung gestartet und beim Löschen beendet. Wenn Sie den **LiveOutput** löschen, werden das zugrunde liegende **Medienobjekt** und sein Inhalt nicht gelöscht.  

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über das Livestreaming](live-streaming-overview.md)
- [Tutorial zu Livestreaming](stream-live-tutorial-with-api.md)

