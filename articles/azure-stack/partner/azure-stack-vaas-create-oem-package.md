---
title: Bewährte Methoden für die Azure Stack-Validierung | Microsoft-Dokumentation
description: In diesem Artikel werden bewährte Methoden für die Verwendung von Validation-as-a-Service beschrieben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: a90ec05fa3224033436830e7666c7eb81ff881f6
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246992"
---
# <a name="create-an-oem-package"></a>Erstellen eines OEM-Pakets

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Das Azure Stack-OEM-Erweiterungspaket ist der Mechanismus, über den Ihrer Azure Stack-Infrastruktur OEM-spezifische Inhalte hinzugefügt werden, die Sie bei der Bereitstellung sowie für operative Vorgänge wie Update, Erweiterung und Austausch im Betrieb verwenden können.

## <a name="creating-the-package"></a>Erstellen des Pakets

Nach dem Erstellen und Überprüfen kann das OEM-Erweiterungspaket in VaaS verwendet werden.  Stellen Sie vor dem Fortfahren sicher, dass Sie die Schritte zum [Erstellen eines OEM-Pakets](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true) abgeschlossen haben. Das Paket wird dann zusammen mit VaaS-Testergebnissen für die Anmeldung beim Workflow „Lösungsvalidierung“ an Microsoft übermittelt. Die folgenden Schritte beschreiben, wie Sie die generierten Dateien in einer einzelnen ZIP-Datei bündeln können, die von VaaS verwendet werden kann.

1. Identifizieren Sie den folgenden Inhalt für das Paket:
    - Eine ausführbare Datei mit dem Namen `<Publisher>-<Model>-<Version>.exe`.
    - Eine oder mehrere Dateien Binärdateien mit dem Namensmuster `<Publisher><Model>-<Version>-#.bin`, wobei # eine sequenzielle Zahl ab 1 ist. Die Anzahl der Binärdateien ist von der Gesamtgröße des Paketinhalts abhängig.
    - Eine Manifestdatei namens `oemMetadata.xml`, die inhaltlich mit der Datei „metadata.xml“ im Stammverzeichnis des Paketinhalts identisch sein sollte.

2. Wählen Sie die Inhaltsdateien aus, und erstellen Sie aus dem Inhalt eine ZIP-Datei:

    ![ZIP-Dateiinhalt](media/vaas-create-oem-package-1.png) ![Komprimieren des Elementinhalts](media/vaas-create-oem-package-2.png)

3. Benennen Sie die resultierende Datei um, damit Sie sie anhand des aussagekräftigen Namens identifizieren können.

## <a name="verifying-the-contents"></a>Überprüfen des Inhalts

Um die Struktur der ZIP-Datei zu überprüfen, untersuchen Sie sie, und vergewissern Sie sich, dass keine Unterordner vorhanden sind. Die TLD enthält den gezippten Inhalt. Eine gültige Paketstruktur ist unten dargestellt.
> [!IMPORTANT]
> Wenn Sie den übergeordneten Ordner anstelle des Inhalts zippen, tritt bei der Paketsignierung ein Fehler auf.

![Ordnungsgemäß gezippter Paketinhalt](media/vaas-create-oem-package-3.png)

Die ZIP-Datei kann jetzt zu VaaS hochgeladen und im Rahmen des Workflows „Lösungsvalidierung“ von Microsoft signiert werden.

## <a name="next-steps"></a>Nächste Schritte

- [Validieren eines OEM-Pakets](azure-stack-vaas-validate-oem-package.md)
