---
title: Includedatei
description: Includedatei
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c212bda3b59037f99139e02ee6adc63b0084cbe5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305613"
---
## <a name="prepare-your-repository"></a>Vorbereiten Ihres Repositorys

Um automatische Builds vom App Service-Kudu-Buildserver zu erhalten, muss das Stammverzeichnis Ihres Repositorys die richtigen Dateien enthalten.

| Laufzeit | Stammverzeichnisdateien |
|-|-|
| ASP.NET (nur Windows) | _*.sln_, _*.csproj_ oder _default.aspx_ |
| ASP.NET Core | _*.sln_ oder _*.csproj_ |
| PHP | _index.php_ |
| Ruby (nur Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ oder _package.json_ mit einem Startskript |
| Python (nur Windows) | _\*.py_, _requirements.txt_ oder _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ oder _iisstart.htm_ |
| WebJobs | _\<job_name>/run.\<erweiterung>_ unter _App\_Data/jobs/continuous_ (für fortlaufende WebJobs) oder _App\_Data/jobs/triggered_ (für ausgelöste WebJobs). Weitere Informationen finden Sie in der [Kudu-Dokumentation zu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Siehe [Kontinuierliche Bereitstellung für Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Zum Anpassen Ihrer Bereitstellung schließen Sie eine Datei vom Typ _.deployment_ im Repositorystamm ein. Weitere Informationen finden Sie unter [Customizing deployments](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) (Anpassen von Bereitstellungen) und [Custom Deployment Script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) (Benutzerdefiniertes Bereitstellungsskript).

> [!NOTE]
> Wenn Sie in Visual Studio entwickeln, kann [Visual Studio ein Repository für Sie erstellen](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Das Projekt kann sofort über Git bereitgestellt werden.
>
>

