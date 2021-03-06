---
title: Tutorial – Verwalten von virtuellen Azure-Computern mit Azure PowerShell | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Azure PowerShell zum Verwalten von virtuellen Azure-Computern durch Anwenden von RBAC, Richtlinien, Sperren und Tags verwenden.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 10/12/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 6377a54cc862bb5f62726c3ce91a41cc6eb0763d
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311387"
---
# <a name="tutorial-learn-about-windows-virtual-machine-governance-with-azure-powershell"></a>Tutorial: Informationen zur Verwaltung von virtuellen Windows-Computern mit Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Die Beispiele in diesem Artikel erfordern Version 6.0 oder höher von Azure PowerShell. Wenn Sie PowerShell lokal ausführen und nicht mindestens über Version 6.0 verfügen, [aktualisieren Sie Ihre Version](/powershell/azure/install-azurerm-ps). Sie müssen außerdem `Connect-AzureRmAccount` ausführen, um eine Verbindung mit Azure herzustellen. Bei lokalen Installationen müssen Sie auch [das Azure AD PowerShell-Modul herunterladen](https://www.powershellgallery.com/packages/AzureAD/), um eine neue Azure Active Directory-Gruppe zu erstellen.

## <a name="understand-scope"></a>Der Bereich

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

In diesem Tutorial wenden Sie alle Verwaltungseinstellungen auf eine Ressourcengruppe an, Sie können diese Einstellungen also am Ende problemlos entfernen.

Zuerst wird eine Ressourcengruppe erstellt.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Die Ressourcengruppe ist derzeit leer.

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Sie sollten sicherstellen, dass Benutzer in Ihrer Organisation einen geeigneten Zugriff auf diese Ressourcen haben. Den Benutzern sollte kein uneingeschränkter Zugriff erteilt werden, aber Sie müssen auch sicherstellen, dass sie ihre Arbeit erledigen können. Mit der [rollenbasierten Zugriffssteuerung](../../role-based-access-control/overview.md) können Sie verwalten, welche Benutzer die Berechtigung zum Ausführen bestimmter Aktionen in einem Bereich erhalten.

Um Rollenzuweisungen erstellen und entfernen zu können, benötigen Benutzer den Zugriff `Microsoft.Authorization/roleAssignments/*`. Dieser Zugriff wird über die Rolle „Besitzer“oder „Benutzerzugriffsadministrator“ gewährt.

Zum Verwalten virtueller Computerlösungen gibt es drei ressourcenspezifische Rollen, die Zugriff auf häufig benötigte Ressourcen bereitstellen:

* [Mitwirkender von virtuellen Computern](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Mitwirkender von virtuellem Netzwerk](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Mitwirkender von Speicherkonto](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Anstatt einzelnen Benutzern Rollen zuzuweisen, ist es häufig einfacher, eine Azure Active Directory-Gruppe mit Benutzern zu verwenden, die ähnliche Aktionen ausführen müssen. Danach weisen Sie diese Gruppe der entsprechenden Rolle zu. Verwenden Sie in diesem Artikel entweder eine vorhandene Gruppe für die VM-Verwaltung, oder [erstellen Sie eine Azure Active Directory-Gruppe](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) über das Portal.

Nachdem Sie entweder eine neue Gruppe erstellt oder nach einer vorhandenen Gruppe gesucht haben, weisen Sie die Azure Active Directory-Gruppe mithilfe des Befehls [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) der Rolle „Mitwirkender für virtuelle Computer“ für die Ressourcengruppe zu.  

```azurepowershell-interactive
$adgroup = Get-AzureRmADGroup -DisplayName <your-group-name>

New-AzureRmRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Sollte der Fehler **Der Prinzipal <guid> ist im Verzeichnis nicht enthalten.** auftreten, wurde die neue Gruppe noch nicht innerhalb von Azure Active Directory verteilt. Führen Sie den Befehl dann erneut aus.

In der Regel müssen Sie den Prozess für die Rollen *Mitwirkender von virtuellem Netzwerk* und *Mitwirkender von Speicherkonto* wiederholen, um sicherzustellen, dass die Benutzer den richtigen Rollen für die Verwaltung der bereitgestellten Ressourcen zugewiesen werden. In diesem Artikel können Sie diese Schritte überspringen.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../azure-policy/azure-policy-introduction.md) hilft Ihnen dabei, sicherzustellen, dass alle Ressourcen im Abonnement die Unternehmensstandards erfüllen. Ihr Abonnement enthält bereits verschiedene Richtliniendefinitionen. Um die verfügbaren Richtliniendefinitionen anzuzeigen, verwenden Sie den Befehl [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition):

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Die vorhandenen Richtliniendefinitionen werden angezeigt. Der Richtlinientyp lautet entweder **BuiltIn** oder **Custom**. Durchsuchen Sie die Definitionen, um eine Definition zu finden, in der eine Bedingung beschrieben wird, die Sie zuweisen möchten. In diesem Artikel weisen Sie Richtlinien zu, mit denen Folgendes erreicht wird:

* Begrenzen der Standorte für alle Ressourcen
* Begrenzen der SKUs für virtuelle Computer
* Überwachen von virtuellen Computern, die keine verwalteten Datenträger verwenden

Im folgenden Beispiel rufen Sie drei Richtliniendefinitionen anhand des Anzeigenamens ab. Sie weisen diese Definitionen mit dem Befehl [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) den Ressourcengruppen zu. Für einige Richtlinien stellen Sie Parameterwerte bereit, um die zulässigen Werte anzugeben.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Bereitstellen des virtuellen Computers

Sie haben Rollen und Richtlinien zugewiesen und können daher Ihre Lösung bereitstellen. Die Standardgröße ist „Standard_DS1_v2“ – eine Ihrer zulässigen SKUs. Wenn Sie diesen Schritt ausführen, werden Sie zur Eingabe von Anmeldeinformationen aufgefordert. Die Werte, die Sie eingeben, werden als Benutzername und Kennwort für den virtuellen Computer konfiguriert.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Nach Abschluss der Bereitstellung können Sie weitere Verwaltungseinstellungen auf die Lösung anwenden.

## <a name="lock-resources"></a>Sperren von Ressourcen

[Ressourcensperren](../../azure-resource-manager/resource-group-lock-resources.md) verhindern, dass Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern. Im Gegensatz zur rollenbasierten Zugriffssteuerung wenden Ressourcensperren eine Einschränkung auf alle Benutzer und Rollen an. Sie können die Sperrebene auf *CanNotDelete* oder *ReadOnly* festlegen.

Zum Sperren des virtuellen Computers und der Netzwerksicherheitsgruppe verwenden Sie den Befehl [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock):

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Um die Sperren zu testen, führen Sie den folgenden Befehl aus:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Es wird eine Fehlermeldung mit dem Hinweis angezeigt, dass der Löschvorgang aufgrund einer Sperre nicht ausgeführt werden kann. Die Ressourcengruppe kann nur gelöscht werden, wenn Sie die Sperren direkt entfernen. Dieser Schritt wird unter [Bereinigen von Ressourcen](#clean-up-resources) gezeigt.

## <a name="tag-resources"></a>Markieren von Ressourcen

Durch Anwenden von [Tags](../../azure-resource-manager/resource-group-using-tags.md) können Sie Ihre Azure-Ressourcen logisch nach Kategorien organisieren. Jedes Tag besteht aus einem Namen und einem Wert. So können Sie beispielsweise den Namen „Umgebung“ und den Wert „Produktion“ auf alle Ressourcen in der Produktion anwenden.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Verwenden Sie zum Anwenden von Tags auf einen virtuellen Computer den Befehl [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource):

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Suchen von Ressourcen nach Tag

Um Ressourcen über einen Tagnamen und einen Wert zu suchen, verwenden Sie den Befehl [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource):

```azurepowershell-interactive
(Get-AzureRmResource -Tag @{ Environment="Test"}).Name
```

Sie können die zurückgegebenen Werte für Verwaltungstasks verwenden, z.B. zum Beenden aller virtuellen Computer mit einem Tagwert.

```azurepowershell-interactive
Get-AzureRmResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Anzeigen von Kosten nach Tagwert

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Die gesperrte Netzwerksicherheitsgruppe kann erst gelöscht werden, wenn die Sperre entfernt wird. Um die Sperre zu entfernen, verwenden Sie den Befehl [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock):

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) entfernen.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein benutzerdefiniertes Image eines virtuellen Computers erstellt. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Zuweisen von Benutzern zu einer Rolle
> * Anwenden von Richtlinien zum Durchsetzen von Standards
> * Schützen wichtiger Ressourcen mit Sperren
> * Markieren von Ressourcen mit Tags für die Abrechnung und Verwaltung

Im nächsten Tutorial erhalten Sie Informationen zu hoch verfügbaren virtuellen Computern.

> [!div class="nextstepaction"]
> [Überwachen virtueller Computer](tutorial-monitoring.md)

