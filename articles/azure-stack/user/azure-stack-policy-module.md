---
title: Verwenden des Azure Stack-Richtlinienmoduls | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Azure-Abonnement so beschränken, dass es sich wie ein Azure Stack-Abonnement verhält.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: sethm
ms.openlocfilehash: b0236a790200feec7f1d16724f351882056b2cd5
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678524"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Verwalten von Azure-Richtlinien mithilfe des Azure Stack-Richtlinienmoduls

*Gilt für: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Das Azure Stack-Richtlinienmodul ermöglicht Ihnen die Konfiguration eines Azure-Abonnements mit der gleichen Versionsverwaltung und Dienstverfügbarkeit wie Azure Stack. Das Modul verwendet das [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition)-Cmdlet zum Erstellen einer Azure-Richtlinie, mit der die in einem Abonnement verfügbaren Ressourcentypen und Dienste eingeschränkt werden. Anschließend erstellen Sie eine Richtlinienzuweisung im geeigneten Bereich, indem Sie das Cmdlet [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) verwenden. Nach dem Konfigurieren der Richtlinie können Sie Ihr Azure-Abonnement zum Entwickeln von Apps für Azure Stack verwenden.

## <a name="install-the-module"></a>Installieren des Moduls

1. Installieren Sie die erforderliche Version des AzureRM PowerShell-Moduls, wie in Schritt 1 von [Installieren von PowerShell für Azure Stack](azure-stack-powershell-install.md) beschrieben.
2. [Laden Sie die Azure Stack-Tools aus GitHub herunter](azure-stack-powershell-download.md).
3. [Konfigurieren Sie PowerShell für die Verwendung mit Azure Stack](azure-stack-powershell-configure-user.md).
4. Importieren Sie das AzureStack.Policy.psm1-Modul:

    ```PowerShell
    Import-Module .\Policy\AzureStack.Policy.psm1
    ```

## <a name="apply-policy-to-azure-subscription"></a>Übernehmen der Richtlinie für das Azure-Abonnement

Mit dem folgenden Befehl können Sie eine Azure Stack-Standardrichtlinie auf Ihr Azure-Abonnement anwenden. Ersetzen Sie vor der Ausführung dieses Befehls `Azure Subscription Name` durch den Namen Ihres Azure-Abonnements.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Übernehmen einer Richtlinie für eine Ressourcengruppe

Möglicherweise möchten Sie präzisere Richtlinien anwenden. Beispielsweise könnten andere Ressourcen im selben Abonnement ausgeführt werden. Sie können die Anwendung der Richtlinie auf eine bestimmte Ressourcengruppe beschränken, was es Ihnen ermöglicht, Ihre Apps für Azure Stack mithilfe von Azure-Ressourcen zu testen. Ersetzen Sie vor der Ausführung des folgenden Befehls `Azure Subscription Name` durch den Namen Ihres Azure-Abonnements.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Ausgeführte Richtlinien

Nach der Bereitstellung der Azure-Richtlinie wird eine Fehlermeldung angezeigt, wenn Sie versuchen, eine durch die Richtlinie untersagte Ressource bereitzustellen.

![Ergebnis eines Ressourcenbereitstellungsfehlers aufgrund einer Richtlinienbeschränkung](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
* [Bereitstellen von Vorlagen mit der Azure CLI](azure-stack-deploy-template-command-line.md)
* [Bereitstellen von Vorlagen mithilfe von Visual Studio](azure-stack-deploy-template-visual-studio.md)
