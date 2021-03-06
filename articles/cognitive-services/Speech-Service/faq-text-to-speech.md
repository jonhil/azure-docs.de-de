---
title: Häufig gestellte Fragen zum Text-to-Speech-Dienst in Azure
titleSuffix: Azure Cognitive Services
description: Hier erhalten Sie Antworten auf die am häufigsten gestellten Fragen zum Text-to-Speech-Dienst.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: f8d80ab189d8ed1f4b153e81963ef31cc5f685b8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470046"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Häufig gestellte Fragen zu Text-to-Speech

Wenn Sie in diesen häufig gestellten Fragen keine Antworten auf Ihre Fragen finden, sehen Sie sich [weitere Supportoptionen](support.md) an.

## <a name="general"></a>Allgemein

**F: Worin besteht der Unterschied zwischen standardmäßigen und benutzerdefinierten Stimmmodellen?**

**A**: Das Standardstimmmodell (auch *Voicefont* genannt) wurde mit Microsoft-eigenen Daten trainiert und wird bereits in der Cloud bereitgestellt. Mit einem benutzerdefinierten Stimmmodelle können Sie entweder ein vorhandenes durchschnittliches Modell verwenden und Klangfarbe und Sprachausdruck an den Stil der Sprecherstimme anpassen oder ein vollständig neues Modell basierend auf vom Benutzer vorbereiteten Daten zu trainieren. Heute wünschen immer mehr Kunden eine einzigartige Stimme mit hohem Wiedererkennungswert für ihre Bots. Hierfür eignet sich die Plattform zum Erstellen benutzerdefinierter Stimmen ideal.

**F: Wo fange ich an, wenn ich ein Standardstimmmodell verwenden möchte?**

**A**: Mehr als 80 Standardstimmmodelle stehen in über 45 Sprachen über HTTP-Anforderungen zur Verfügung. Rufen Sie zunächst einen [Abonnementschlüssel](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) ab. Informationen zum Ausführen von REST-Aufrufen an die vorab bereitgestellten Stimmmodelle finden Sie in der [REST-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**F: Wenn ich ein benutzerdefiniertes Stimmmodell verwenden möchte, ist die API die gleiche wie für die Standardstimmen?**

**A**: Wenn ein benutzerdefiniertes Stimmmodell erstellt und bereitgestellt wird, erhalten Sie einen eindeutigen Endpunkt für Ihr Modell. Sie müssen den Endpunkt in Ihren HTTP-Anforderungen angeben, damit diese Stimme für Sprachausgaben in Ihren Apps verwendet werden kann. Die gleiche Funktionalität, die über die REST-API für den Text-to-Speech-Dienst verfügbar ist, steht auch für Ihren benutzerdefinierten Endpunkt zur Verfügung. Hier erfahren Sie, wie Sie Ihren [benutzerdefinierten Endpunkt erstellen und verwenden](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**F: Muss ich beim Erstellen von benutzerdefinierten Stimmmodellen die Trainingsdaten selbst vorbereiten?**

**A**: Ja, Sie müssen die Trainingsdaten für ein benutzerdefiniertes Stimmmodell selbst vorbereiten.

Zum Erstellen eines benutzerdefinierten Stimmmodells ist eine Sammlung von Sprachdaten erforderlich. Diese Sammlung besteht aus einer Reihe von Audiodateien mit Sprachaufnahmen sowie einer Textdatei mit der Transkription jeder Audiodatei. Die resultierende digitale Stimme hängt in hohem Maß von der Qualität der Trainingsdaten ab. Die Erstellung einer guten Text-to-Speech-Stimme setzt voraus, dass die Aufnahmen in einem ruhigen Raum mit einem hochwertigen Standmikrofon erfolgen. Eine gleichmäßige Lautstärke, Sprechgeschwindigkeit und Tonhöhe sowie eine ausdrucksstarke und doch konsistente Prosodie sind bei der Erstellung einer angenehmen digitalen Stimme entscheidend. Es wird dringend empfohlen, die Stimmen in einem Tonstudio aufzuzeichnen.

Zurzeit bieten wir keine Unterstützung für Onlineaufnahmen oder Empfehlungen für Tonstudios. Informationen zu den Anforderungen an das Format finden Sie unter [Vorbereiten von Aufnahmen und Transkripten](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts).

**F: Welche Skripts sollte ich zur Aufzeichnung der Sprachdaten für das benutzerdefinierte Stimmtraining verwenden?**

**A**: Es gibt keinerlei Einschränkungen hinsichtlich der für Aufnahmen verwendeten Skripts. Sie können eigene Skripts für die Sprachaufnahmen verwenden. Stellen Sie nur sicher, dass eine ausreichende phonetische Abdeckung in den Sprachdaten gegeben ist. Um eine benutzerdefinierte Stimme zu trainieren, beginnen Sie mit einer geringen Menge an Sprachdaten – ungefähr 50 verschiedene Sätze (3-5 Minuten Spracheingabe). Je mehr Daten Sie bereitstellen, desto natürlicher klingt die Stimme. Sie können einen vollständigen Voicefont trainieren, wenn Sie Aufzeichnungen mit mehr als 2.000 Sätzen bereitstellen (ca. 3-4 Stunden Spracheingabe). Um einen vollständigen Voicefont in sehr hoher Qualität zu erhalten, müssen Sie Aufzeichnungen von mehr als 6.000 Sätzen erstellen (ca. 8-10 Stunden Spracheingabe).

Wir bieten weitere Dienste, um Sie bei der Erstellung von Skripts für die Aufzeichnung zu unterstützen. Um diese Dienste anzufragen, wenden Sie sich an den [Custom Voice-Kundendienst](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation).

**F: Was geschieht, wenn ich eine höhere Parallelität als den Standardwert oder den im Portal angebotenen Wert benötige?**

**A**: Sie können Ihr Modell in Schritten von 20 gleichzeitigen Anforderungen zentral hochskalieren. Um eine höhere Skalierung anzufragen, wenden Sie sich an den [Custom Voice-Kundendienst](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation).

**F: Kann ich mein Modell herunterladen und lokal ausführen?**

**A**: Modelle können nicht heruntergeladen und lokal ausgeführt werden.

**D: Sind meine Anforderungen gedrosselt?**

**A**: Die REST-API beschränkt Anforderungen auf 25 pro 5 Sekunden. Informationen finden Sie auf unseren Seiten für [Text-to-Speech](text-to-speech.md). 

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung](troubleshooting.md)
* [Versionshinweise](releasenotes.md)
