---
title: 'Azure PowerShell-Skriptbeispiel: Erstellen eines virtuellen Computers durch Anfügen eines verwalteten Datenträgers als Betriebssystemdatenträger | Microsoft-Dokumentation'
description: 'Azure PowerShell-Skriptbeispiel: Erstellen eines virtuellen Computers durch Anfügen eines verwalteten Datenträgers als Betriebssystemdatenträger'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: d727b33c8b955667e78442387f27b9adecf66798
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386016"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Erstellen eines virtuellen Computers mit einem vorhandenen verwalteten Betriebssystemdatenträger mit PowerShell

Dieses Skript erstellt einen virtuellen Computer durch Anfügen eines vorhandenen verwalteten Datenträgers als Betriebssystemdatenträger. Verwenden Sie dieses Skript in den vorherigen Szenarien:
* Erstellen eines virtuellen Computers aus einem vorhandenen verwalteten Betriebssystemdatenträger, der von einem verwalteten Datenträger in einem anderen Abonnement kopiert wurde
* Erstellen eines virtuellen Computers aus einem vorhandenen verwalteten Datenträger, der aus einer spezialisierten VHD-Datei erstellt wurde 
* Erstellen eines virtuellen Computers aus einem vorhandenen verwalteten Betriebssystemdatenträger, der aus einer Momentaufnahme erstellt wurde 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle, um die Eigenschaften eines verwalteten Datenträgers abzurufen, einen verwalteten Datenträger an einen neuen virtuellen Computer anzufügen und einen virtuellen Computer zu erstellen. Jedes Element in der Tabelle ist mit der befehlsspezifischen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Get-AzureRmDisk](/powershell/module/azurerm.compute/Get-AzureRmDisk) | Ruft das Datenträgerobjekt basierend auf dem Namen und der Ressourcengruppe eines Datenträgers ab. Die ID-Eigenschaft des zurückgegebenen Datenträgerobjekts wird verwendet, um den Datenträger an einen neuen virtuellen Computer anzufügen. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Erstellt eine VM-Konfiguration. Diese Konfiguration umfasst Informationen wie VM-Name, Betriebssystem und Administratoranmeldeinformationen. Die Konfiguration wird während der VM-Erstellung verwendet. |
| [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | Fügt einen verwalteten Datenträger mit der ID-Eigenschaft des Datenträgers als Betriebssystemdatenträger an einen neuen virtuellen Computer an. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Erstellt eine öffentliche IP-Adresse. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Erstellt eine Netzwerkschnittstelle. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Erstellen Sie eine VM. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Entfernt eine Ressourcengruppe und alle darin enthaltenen Ressourcen. |

Verwenden Sie für Marketplace-Images [Set-AzureRmVMPlan](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmplan?view=azurermps-6.7.0) zum Festlegen der Planinformationen.
```powershell
Set-AzureRmVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche VM-PowerShell-Skriptbeispiele finden Sie in der [Dokumentation zu Windows-VMs in Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
