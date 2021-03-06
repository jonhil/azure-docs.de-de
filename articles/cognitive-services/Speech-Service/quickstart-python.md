---
title: 'Schnellstart: Erkennen von Sprache in Python mit dem Speech Service SDK'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mit dem Speech Service SDK Sprache in Python erkennen.
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: chlandsi
ms.openlocfilehash: 7610b12b351b2652df7ade603711d4d92e587292
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723908"
---
# <a name="quickstart-using-the-speech-service-from-python"></a>Schnellstart: Verwenden von Speech Service mit Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In diesem Artikel wird erläutert, wie Speech Service über das Speech SDK für Python verwendet wird. Es wird veranschaulicht, wie Sie Sprache in einer Mikrofoneingabe erkennen können.

## <a name="prerequisites"></a>Voraussetzungen

Folgendes wird vorausgesetzt:

* Ein Azure-Abonnementschlüssel für Speech Service. [Hier erhalten Sie einen kostenlosen Schlüssel](get-started.md).
* [Python 3.5 (64-Bit)](https://www.python.org/downloads/) oder höher.
* Das Speech SDK-Paket für Python ist für Windows (x64), Mac (macOS X-Version 10.12 oder höher) und Linux (Ubuntu 16.04 oder 18.04 auf x64) verfügbar.
* Führen Sie zum Installieren der erforderlichen Pakete unter Ubuntu die folgenden Befehle aus:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* Unter Windows benötigen Sie auch [Visual C++ Redistributable für Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) für Ihre Plattform.

## <a name="get-the-speech-sdk-python-package"></a>Installieren des Speech SDK-Pakets für Python

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Das Cognitive Services Speech SDK-Paket für Python kann über [PyPI](https://pypi.org/) installiert werden, indem Sie in der Befehlszeile den folgenden Befehl eingeben:

```sh
pip install azure-cognitiveservices-speech
```

Die aktuelle Version des Cognitive Services Speech SDK ist `1.2.0`.

## <a name="support-and-updates"></a>Support und Updates

Updates für das Speech SDK-Paket für Python werden über PyPI verteilt und auf der Seite [Versionshinweise](./releasenotes.md) angekündigt.
Wenn eine neue Version verfügbar ist, können Sie mit dem Befehl `pip install --upgrade azure-cognitiveservices-speech` auf diese Version aktualisieren.
Die derzeit installierte Version können Sie der Variablen `azure.cognitiveservices.speech.__version__` entnehmen.

Falls Probleme auftreten oder ein Feature fehlt, besuchen Sie unsere [Supportseite](./support.md).

## <a name="create-a-python-application-using-the-speech-sdk"></a>Erstellen einer Python-Anwendung mit dem Speech SDK

### <a name="running-the-sample-in-a-terminal"></a>Ausführen des Beispiels in einem Terminal

Sie können den [Code](#quickstart-code) aus diesem Schnellstart in eine Quelldatei `quickstart.py` kopieren und in Ihrer integrierten Entwicklungsumgebung (Integrated Development Environment, IDE) oder in der Konsole ausführen.

```sh
python quickstart.py
```

Alternativ können Sie dieses Schnellstarttutorial als [Jupyter](https://jupyter.org) Notebook aus dem [Beispielrepository für das Cognitive Services Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) herunterladen und als Notebook ausführen.

### <a name="quickstart-code"></a>Schnellstartcode

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="installing-the-speech-sdk-python-package-and-running-the-sample-in-visual-studio-code"></a>Installieren des Speech SDK-Pakets für Python und Ausführen des Beispiels in Visual Studio Code

1. Zunächst müssen Sie eine 64-Bit-Version (3.5 oder höher) von Python [herunterladen](https://www.python.org/downloads/) und auf Ihrem Computer installieren.
1. [Laden](https://code.visualstudio.com/Download) Sie Visual Studio Code herunter, und installieren Sie die Software.
1. Öffnen Sie Visual Studio Code, und installieren Sie die Python-Erweiterung, indem Sie im Menü **Datei** > **Einstellungen** > **Erweiterungen** auswählen und nach „Python“ suchen.
   ![Installieren der Python-Erweiterung](media/sdk/qs-python-vscode-python-extension.png)
1. Erstellen Sie einen Ordner zum Speichern des Projekts (z. B. mit dem Windows-Explorer).
1. Klicken Sie in Visual Studio Code auf das Symbol **Datei**, und öffnen Sie den Ordner, den Sie erstellt haben.
   ![Ordner öffnen](media/sdk/qs-python-vscode-python-open-folder.png)
1. Erstellen Sie eine neue Python-Quelldatei `speechsdk.py`, indem Sie auf das Symbol „Neue Datei“ klicken.
   ![Datei erstellen](media/sdk/qs-python-vscode-python-newfile.png)
1. Kopieren Sie den [Python-Code](#quickstart-code), fügen Sie ihn in die neu erstellte Datei ein, und speichern Sie die Datei.
1. Fügen Sie Ihre Speech Service-Abonnementinformationen ein.
1. Wenn bereits ein Python-Interpreter ausgewählt wurde, wird er auf der linken Seite der Statusleiste am unteren Rand des Fensters angezeigt.
   Andernfalls können Sie eine Liste der verfügbaren Python-Interpreter anzeigen, indem Sie die **Befehlspalette** öffnen (`Ctrl+Shift+P`), **Python: Interpreter auswählen** eingeben und einen geeigneten Interpreter auswählen.
1. Wenn das Speech SDK-Paket für Python noch nicht für den ausgewählten Python-Interpreter installiert ist, können Sie es ganz einfach in Visual Studio Code installieren.
   Öffnen Sie zum Installieren des Speech SDK-Pakets ein Terminal, indem Sie erneut die Befehlspalette öffnen (`Ctrl+Shift+P`) und **Terminal: Neues integriertes Terminal erstellen** eingeben.
   Geben Sie im Terminal, das geöffnet wird, den Befehl `python -m pip install azure-cognitiveservices-speech` bzw. den entsprechenden Befehl für Ihr System ein.
1. Klicken Sie zum Ausführen des Beispielcodes mit der rechten Maustaste an einer beliebigen Stelle im Editor, und wählen Sie **Run Python File in Terminal** (Python-Datei im Terminal ausführen) aus.
   Sprechen Sie einige Wörter, wenn Sie dazu aufgefordert werden. Der transkribierte Text sollte kurz darauf angezeigt werden.
   ![Ausführen des Beispiels](media/sdk/qs-python-vscode-python-run.png)

Falls Sie Probleme beim Durchführen dieser Anweisungen haben, können Sie das ausführlichere [Visual Studio Code Python Tutorial](https://code.visualstudio.com/docs/python/python-tutorial) (Visual Studio Code-Tutorial für Python) zurate ziehen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Python-Beispiele auf GitHub](https://aka.ms/csspeech/samples)
