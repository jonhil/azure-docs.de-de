---
title: Azure PowerShell-Skriptbeispiel – Löschen einer Sicherung für eine Web-App | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Löschen einer Sicherung für eine Web-App
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d8a3d8f890001138f0582a099021b4537227a337
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53586413"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Löschen einer Sicherung für eine Web-App mit Azure PowerShell

Dieses Beispielskript erstellt eine Web-App in App Service mit den zugehörigen Ressourcen und anschließend eine einmalige Sicherung für sie. 

Um dieses Skript auszuführen, benötigen Sie eine vorhandene Sicherung für eine Web-App. Wie Sie eine Sicherung erstellen, erfahren Sie unter [Backup up a web app](powershell-backup-onetime.md) (Sichern einer Web-app) oder [Create a scheduled backup for a web app](powershell-backup-scheduled.md) (Erstellen einer geplanten Sicherung für eine Web-App).

## <a name="sample-script"></a>Beispielskript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Nach dem Ausführen des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe, die Web-App und alle zugehörigen Ressourcen entfernt werden.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Ruft eine Liste der Sicherungen für eine Web-App ab. |
| [Remove-AzureRmWebAppBackup](/powershell/module/azurerm.websites/remove-azurermwebappbackup) | Entfernt die angegebene Sicherung einer Web-App. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche Azure PowerShell-Beispiele für Azure App Service-Web-Apps finden Sie unter [Azure PowerShell-Beispiele](../samples-powershell.md).
