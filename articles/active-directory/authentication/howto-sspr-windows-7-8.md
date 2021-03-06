---
title: Self-Service-Kennwortzurücksetzung in Azure AD unter Windows 7 und 8.1
description: Aktivieren der Self-Service-Kennwortzurücksetzung mithilfe von „Kennwort vergessen“ auf dem Windows 7- oder 8.1-Anmeldebildschirm
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 1465ba23f6fed85f0d4164f8e3fdc8c38be43ad3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427728"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>Aktivieren der Kennwortzurücksetzung unter Windows 7, 8 und 8.1

Als Administrator ist die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) für Sie aktiviert. Benutzer die ihr Kennwort zurücksetzen möchten, rufen jedoch beim Helpdesk an, da sie kein Browserfenster für den Zugriff auf das [SSPR-Portal](https://aka.ms/sspr) öffnen können. Für Windows 10-Computer können Sie den Link „Kennwort zurücksetzen“ auf dem Anmeldebildschirm anhand des Tutorials [Azure AD-Kennwortzurücksetzung über den Anmeldebildschirm](tutorial-sspr-windows.md) aktivieren. Mithilfe des folgenden Leitfadens können Sie Windows 7-, 8- und 8.1-Benutzern das Zurücksetzen ihres Kennworts mithilfe von SSPR auf dem Windows-Anmeldebildschirm ermöglichen.

Im Gegensatz zu Windows 10-Computern müssen Windows 7-, 8- und 8.1-Computer nicht in eine Azure AD-Domäne oder Active Directory-Domäne eingebunden sein, um das Kennwort zurückzusetzen.

![Beispiel: Windows 7-Anmeldebildschirm mit „Kennwort vergessen?“ angezeigter Link](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>Voraussetzungen

* Die Azure AD-Self-Service-Kennwortzurücksetzung muss aktiviert sein.
* Gepatchtes Windows 7- oder Windows 8.1-Betriebssystem.
* TLS 1.2 wurde gemäß der Anleitung unter [Registrierungseinstellungen für Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) aktiviert.

> [!WARNING]
> TLS 1.2 muss aktiviert sein, nicht nur auf „Automatische Aushandlung“ festgelegt.

## <a name="install"></a>Installieren

1. Laden Sie das Installationsprogramm für die Windows-Version herunter, die Sie aktivieren möchten.

   1. Software ist im Microsoft Download Center unter [https://aka.ms/sspraddin](https://aka.ms/sspraddin) verfügbar.

1. Melden Sie sich auf dem Computer an, auf dem Sie installieren möchten, und führen Sie das Installationsprogramm aus.
1. Ein Neustart nach der Installation wird dringend empfohlen.
1. Wählen Sie nach dem Neustart auf dem Anmeldebildschirm einen Benutzer aus, und klicken Sie auf „Kennwort vergessen?“, um den Workflow zur Kennwortzurücksetzung zu initiieren.
1. Schließen Sie den Workflow mit den folgenden Schritten auf dem Bildschirm ab, um Ihr Kennwort zurückzusetzen.

![Beispiel: Windows 7 mit angeklickter Option „Kennwort vergessen?“ Ablauf der Self-Service-Kennwortzurücksetzung](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>Automatische Installation

* Verwenden Sie für die automatische Installation den Befehl „msiexec /i SsprWindowsLogon.PROD.msi /qn“.
* Verwenden Sie für die automatische Deinstallation den Befehl „msiexec /x SsprWindowsLogon.PROD.msi /qn“.

## <a name="caveats"></a>Einschränkungen

Sie müssen sich für SSPR registrieren, bevor Sie den Link „Kennwort vergessen“ verwenden können.

![Zusätzliche Sicherheitsinformationen sind erforderlich, um Ihr Kennwort zurückzusetzen.](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

Die Verwendung der Microsoft Authenticator-App für Benachrichtigungen und Codes zum Zurücksetzen Ihres Kennworts ist in dieser ersten Version nicht möglich. Benutzer müssen alternative Methoden registriert haben, die die Anforderungen Ihrer Richtlinie erfüllen.

## <a name="troubleshooting"></a>Problembehandlung

Ereignisse werden sowohl auf dem Computer als auch in Azure AD protokolliert.

Azure AD-Ereignisse enthalten Informationen zur IP-Adresse und zum Clienttyp, für die das Kennwort zurückgesetzt wurde.

![Beispiel: Kennwortzurücksetzung auf Windows 7-Anmeldebildschirm im Azure AD-Überwachungsprotokoll](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

Wenn zusätzliche Protokollierung erforderlich ist, kann ein Registrierungsschlüssel auf dem Computer geändert werden, um die ausführliche Protokollierung zu aktivieren. Aktivieren Sie die ausführliche Protokollierung nur zur Problembehandlung.

```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

* Zum Aktivieren der ausführlichen Protokollierung erstellen Sie den REG_DWORD-Wert „EnableLogging“ und legen ihn auf 1 fest.
* Zum Deaktivieren der ausführlichen Protokollierung ändern Sie den REG_DWORD-Wert „EnableLogging“ in 0 fest.

Wenn sich Ihre Computer mit Windows 7, 8 oder 8.1 hinter einem Proxyserver oder einer Firewall befinden, sollte HTTPS-Datenverkehr (443) zu „passwordreset.microsoftonline.com“ zugelassen werden.

## <a name="next-steps"></a>Nächste Schritte

[Ermöglichen Sie Windows 10-Benutzern das Zurücksetzen ihres Kennworts auf dem Anmeldebildschirm.](tutorial-sspr-windows.md)