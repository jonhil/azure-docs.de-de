---
title: 'Azure CLI-Skriptbeispiel: Erstellen einer SignalR Service-Instanz per App Service'
description: 'Azure CLI-Skriptbeispiel: Erstellen eines SignalR Service per App Service'
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 007bf58c06d28f4445b3e57c5d692e3ee19b5b8c
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257742"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Erstellen eines SignalR Service per App Service

In diesem Beispielskript wird eine neue Azure SignalR Service-Ressource erstellt, mit der Echtzeitinhaltsupdates mithilfe von Push an Clients übertragen werden. Dieses Skript fügt auch eine neue Web-App und einen neuen App Service-Plan zum Hosten Ihrer Web-App hinzu, die den SignalR Service verwendet. Die Web-App wird mit der App-Einstellung *AzureSignalRConnectionString* konfiguriert, um eine Verbindung mit der neuen SignalR Service-Ressource herzustellen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Beispielskript

In diesem Skript wird die *signalr*-Erweiterung für die Azure CLI verwendet. Führen Sie den folgenden Befehl zum Installieren der *signalr*-Erweiterung für die Azure CLI aus, bevor Sie dieses Beispielskript verwenden:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

Notieren Sie den tatsächlichen Namen, der für die neue Ressourcengruppe generiert wurde. Dieser wird in der Ausgabe angezeigt. Dieser Ressourcengruppenname wird verwendet, wenn Sie alle Gruppenressourcen löschen möchten.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft. Das Skript verwendet die folgenden Befehle:

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | Erstellt eine Azure SignalR Service-Ressource. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Listet die Schlüssel auf, die von Ihrer Anwendung bei der Übertragung von Echtzeitinhaltsupdates mit SignalR mithilfe von Push verwendet werden. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Erstellt einen Azure App Service-Plan zum Hosten von Web-Apps. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Erstellt eine Azure-Web-App mithilfe des App Service-Hostingplans. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Fügt eine neue App-Einstellung für die Web-App hinzu. Diese App-Einstellung wird verwendet, um die SignalR-Verbindungszeichenfolge zu speichern. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere CLI-Skriptbeispiele zum Azure SignalR Service finden Sie in der [Dokumentation zum Azure SignalR Service](../signalr-cli-samples.md).
