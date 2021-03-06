---
title: Versionshinweise zu Validation-as-a-Service in Azure Stack | Microsoft-Dokumentation
description: Versionshinweise zu Validation-as-a-Service in Azure Stack
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
ms.openlocfilehash: 735f1d7cce8621a29e5a582917d3eea8ee399b8f
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319647"
---
# <a name="release-notes-for-validation-as-a-service"></a>Versionshinweise zu Validation-as-a-Service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Dieser Artikel enthält die Versionshinweise zu Validation-as-a-Service in Azure Stack.

## <a name="version-402"></a>Version 4.0.2

7. Januar 2019

Wenn Sie den Workflow für die monatliche Updateüberprüfung für Azure Stack ausführe, und die Version für Ihr OEM-Updatepaket nicht 1810 oder höher ist, erhalten Sie einen Fehler, sobald Sie zum Schritt für das OEM-Update gelangen. Dies ist ein Fehler. Eine Korrektur wird derzeit entwickelt. Die Schritte zur Behebung sehen wie folgt aus:

1.  Führen Sie das OEM-Update wie gewohnt aus.
2.  Führen Sie nach der erfolgreichen Anwendung des Pakets „Test-AzureStack“ aus, und speichern Sie die Ausgabe.
3.  Brechen Sie den Test ab.
4.  Senden Sie die gespeicherte Ausgabe an VaaSHelp@microsoft.com, um erfolgreiche Ergebnisse für die Ausführung zu erhalten.

## <a name="version-402"></a>Version 4.0.2

30. November 2018

- Interne Fehlerkorrekturen

## <a name="version-401"></a>Version 4.0.1

8. Oktober 2018

- Voraussetzungen für VaaS

    `Install-VaaSPrerequisites` erfordert keine Cloudadministrator-Anmeldeinformationen mehr. Wenn Sie die neueste Version dieses Cmdlets ausführen, finden Sie die überarbeiteten Befehle für die Installation der Voraussetzungen unter [Herunterladen und Installieren des Agents](azure-stack-vaas-local-agent.md#download-and-install-the-agent). Hier sind die Befehle angegeben:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Version 4.0.0

29. August 2018

- Voraussetzungen für VaaS und VHD-Updates

    `Install-VaaSPrerequisites` erfordert nun Cloudadministrator-Anmeldeinformationen aufgrund eines Problems bei der Lösungsvalidierung. Die Dokumentation unter [Herunterladen und Installieren des Agents](azure-stack-vaas-local-agent.md#download-and-install-the-agent) wurde folgendermaßen aktualisiert:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > Die im Skript erforderliche Angabe `$CloudAdminCreds` wird für die Überprüfung der Azure Stack-Instanz benötigt. Es handelt sich nicht um die Azure Active Directory-Anmeldeinformationen, die vom VaaS-Mandanten verwendet werden.

- Aktualisierung des lokalen Agents

    Die vorherige Version des lokalen Agents ist nicht mit der aktuellen Version 4.0.0 des Diensts kompatibel. Alle Benutzer müssen ihre lokalen Agents aktualisieren. Anweisungen zum Installieren des neuesten Agents finden Sie unter [Herunterladen und Installieren des Agents](azure-stack-vaas-local-agent.md#download-and-install-the-agent).

- Update der PowerShell-Automatisierung

    Es wurden Änderungen an den PowerShell-Skripts `LaunchVaaSTests` vorgenommen, die die neueste Version der Skripterstellungspakete erfordern. Anweisungen zum Installieren der neuesten Version des Skripterstellungspakets finden Sie unter [Starten des Testdurchlauf-Workflows](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow).

- Validation-as-a-Service-Portal

  - Benachrichtigungen bei der Paketsignierung

    Wenn ein OEM-Anpassungspaket im Rahmen des Workflows „Lösungsvalidierung“ gesendet wird, wird das Paketformat überprüft, um sicherzustellen, dass es die veröffentlichte Spezifikation erfüllt. Wenn das Paket sie nicht erfüllt, tritt bei der Ausführung ein Fehler auf. E-Mail-Benachrichtigungen werden an die E-Mail-Adresse des registrierten Azure Active Directory-Kontakts für den Mandanten gesendet.

  - Testkategorie „Interaktiv“

    Die Testkategorie **Interaktiv** wurde hinzugefügt. Mit diesen Tests können Partner interaktive und nicht automatisierte Azure Stack-Szenarien testen.

  - Interaktive Featureüberprüfung

    Die Möglichkeit, spezifisches Feedback für bestimmte Features abzugeben, ist jetzt in den Testdurchlauf-Workflows verfügbar. Der Test `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` überprüft, ob bestimmte Updates richtig angewandt wurden, und sammelt dann Feedback.

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung für Validation-as-a-Service](azure-stack-vaas-troubleshoot.md)
