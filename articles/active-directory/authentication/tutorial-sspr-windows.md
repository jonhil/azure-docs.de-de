---
title: Azure AD-SSPR über den Windows 10-Anmeldebildschirm
description: In diesem Tutorial aktivieren Sie die Kennwortzurücksetzung auf dem Windows 10-Anmeldebildschirm, um Helpdesk-Anrufe zu reduzieren.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5c40e6c681a4f37c61519040eb32531d3c8f071c
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437145"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Tutorial: Azure AD-Kennwortzurücksetzung über den Anmeldebildschirm

In diesem Tutorial ermöglichen Sie Benutzern das Zurücksetzen ihrer Kennwörter über den Windows 10-Anmeldebildschirm. Mit dem neuen Windows 10 April 2018 Update wird Benutzern mit in **Azure AD eingebundenen Geräten** oder in **Azure AD eingebundenen Hybridgeräten** auf dem Anmeldebildschirm der Link „Kennwort zurücksetzen“ angezeigt. Wenn Benutzer auf diesen Link klicken, gelangen sie zur vertrauten Benutzeroberfläche für die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR).

> [!div class="checklist"]
> * Konfigurieren des Links „Kennwort zurücksetzen“ mit Intune
> * Optionales Konfigurieren mithilfe der Windows-Registrierung
> * Grundlegendes zu den Elementen, die Benutzern angezeigt werden

## <a name="prerequisites"></a>Voraussetzungen

* Client mit Windows 10 April 2018 Update oder einer höheren Version und entweder:
   * [In Azure AD eingebundener Computer](../device-management-azure-portal.md) oder
   * [in Azure AD Hybrid eingebundener Computer](../device-management-hybrid-azuread-joined-devices-setup.md) mit Netzwerkkonnektivität zu einem Domänencontroller.
* Die Azure AD-Self-Service-Kennwortzurücksetzung muss aktiviert sein.

## <a name="configure-reset-password-link-using-intune"></a>Konfigurieren des Links „Kennwort zurücksetzen“ mit Intune

Die Bereitstellung der Konfigurationsänderung zum Aktivieren der Kennwortzurücksetzung über den Anmeldebildschirm mithilfe von Intune ist die flexibelste Methode. Mit Intune können Sie die Konfigurationsänderung für eine bestimmte Gruppe von Computern bereitstellen, die Sie definieren. Diese Methode erfordert die Registrierung des Gerätes über Intune.

### <a name="create-a-device-configuration-policy-in-intune"></a>Erstellen einer Richtlinie für die Gerätekonfiguration in Intune

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Intune**.
2. Erstellen Sie ein neues Profil für die Gerätekonfiguration, indem Sie zu **Gerätekonfiguration** > **Profile** > **Profil erstellen** navigieren.
   * Geben Sie einen aussagekräftigen Namen für das Profil an.
   * Geben Sie optional eine aussagekräftige Beschreibung des Profils an.
   * Plattform **Windows 10 und höher**
   * Profiltyp **Benutzerdefiniert**

3. Konfigurieren von **Einstellungen**
   * Fügen Sie mit **Hinzufügen** die folgende OMA-URI-Einstellung hinzu, um den Link „Kennwort zurücksetzen“ zu aktivieren.
      * Geben Sie einen aussagekräftigen Namen an, um den Zweck der Einstellung zu verdeutlichen.
      * Geben Sie optional eine aussagekräftige Beschreibung der Einstellung an.
      * Festlegung von **OMA-URI** auf `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * Festlegung von **Data type** auf **Integer**
      * Festlegung von **Value** auf **1**
      * Klicken Sie auf **OK**
   * Klicken Sie auf **OK**
4. Klicken Sie auf **Erstellen**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Zuweisen einer Richtlinie für die Gerätekonfiguration in Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Erstellen einer Gruppe, auf die die Richtlinie für die Gerätekonfiguration angewendet werden kann

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Azure Active Directory**.
2. Navigieren Sie zu **Benutzer und Gruppen** > **Alle Gruppen** > **Neue Gruppe**.
3. Geben Sie einen Namen für die Gruppe ein, und wählen Sie unter **Mitgliedschaftstyp** die Option **Zugewiesen**.
   * Wählen Sie unter **Mitglieder** die in Azure AD eingebundenen Windows 10-Geräte aus, auf die Sie die Richtlinie anwenden möchten.
   * Klicken Sie auf **Auswählen**.
4. Klicken Sie auf **Erstellen**

Weitere Informationen zum Erstellen von Gruppen finden Sie im Artikel [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](../fundamentals/active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Zuweisen einer Richtlinie für die Gerätekonfiguration zu einer Gerätegruppe

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Intune**.
2. Suchen Sie wie folgt nach dem zuvor erstellten Profil für die Gerätekonfiguration: Navigieren Sie zu **Gerätekonfiguration** > **Profile**, und klicken Sie auf das erstellte Profil.
3. Weisen Sie das Profil einer Gruppe mit Geräten zu. 
   * Klicken Sie unter **Include** > **Select groups to include** (Einschließen > Einzuschließende Gruppen auswählen) auf **Assignments** (Zuweisungen).
   * Wählen Sie die zuvor erstellte Gruppe aus, und klicken Sie auf **Auswählen**.
   * Klicken Sie im Menü „Einstellungen“ auf **Speichern**

   ![Zuweisung][Assignment]

Sie haben jetzt eine Richtlinie für die Gerätekonfiguration erstellt und zugewiesen, um den Link „Kennwort zurücksetzen“ auf dem Anmeldebildschirm mit Intune zu aktivieren.

## <a name="configure-reset-password-link-using-the-registry"></a>Konfigurieren des Links „Kennwort zurücksetzen“ über die Registrierung

1. Melden Sie sich mit Administratoranmeldeinformationen am Windows-PC an.
2. Führen Sie den Befehl **regedit** als Administrator aus.
3. Legen Sie den folgenden Registrierungsschlüssel fest:
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Anzeige für Benutzer

Was ändert sich für die Benutzer, nachdem die Richtlinie nun konfiguriert und zugewiesen wurde? Wie erfahren sie, dass sie ihr Kennwort über den Anmeldebildschirm zurücksetzen können?

![LoginScreen][LoginScreen]

Wenn Benutzer versuchen, sich anzumelden, wird jetzt der Link „Kennwort zurücksetzen“ angezeigt. Mit diesem Link wird die Oberfläche für die Self-Service-Kennwortzurücksetzung auf dem Anmeldebildschirm geöffnet. Mit dieser Funktion können Benutzer ihr Kennwort zurücksetzen, ohne ein anderes Gerät für den Zugriff auf einen Webbrowser verwenden zu müssen.

Eine Anleitung für Benutzer zur Verwendung dieses Features befindet sich unter [Ich habe mein Azure AD-Kennwort vergessen. Was nun?](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in).

Das Azure AD-Überwachungsprotokoll enthält Informationen zur IP-Adresse und zum Clienttyp, für die das Kennwort zurückgesetzt wurde.

![Beispiel: Kennwortzurücksetzung auf dem Anmeldebildschirm im Azure AD-Überwachungsprotokoll](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

## <a name="limitations"></a>Einschränkungen

Beim Testen dieser Funktionalität mit Hyper-V wird der Link „Kennwort zurücksetzen“ nicht angezeigt.

* Navigieren Sie zu der VM, die Sie zum Testen verwenden möchten, klicken Sie auf **Ansicht**, und deaktivieren Sie **Erweiterte Sitzung**.

Beim Testen dieser Funktionalität mit Remotedesktop oder einer erweiterten VM-Sitzung wird der Link „Kennwort zurücksetzen“ nicht angezeigt.

* Die Kennwortzurücksetzung wird für Remotedesktop derzeit nicht unterstützt.

Wenn die Richtlinie die Verwendung von STRG+ALT+ENTF vorschreibt oder Benachrichtigungen bei gesperrtem Bildschirm deaktiviert sind, funktioniert **Kennwort zurücksetzen** nicht.

Bei den folgenden Richtlinieneinstellungen ist bekannt, dass sie die Möglichkeit zum Zurücksetzen von Kennwörtern beeinträchtigen:

   * „HideFastUserSwitching“ ist auf „Aktiviert“ oder „1“ festgelegt.
   * „DontDisplayLastUserName“ ist auf „Aktiviert“ oder auf „1“ festgelegt.
   * „NoLockScreen“ ist auf „Aktiviert“ oder „1“ festgelegt.
   * „EnableLostMode“ ist auf dem Gerät festgelegt.
   * „Explorer.exe“ wird durch eine benutzerdefinierte Shell ersetzt.

Dieses Feature funktioniert nicht für Netzwerke mit 802.1x-Netzwerkauthentifizierung und der Option „Unmittelbar vor der Benutzeranmeldung ausführen“. In Netzwerken mit 802.1x-Netzwerkauthentifizierung empfiehlt es sich, die Computerauthentifizierung zu verwenden, um dieses Feature zu aktivieren.

Wenn sich Ihre Windows 10-Computer hinter einem Proxyserver oder einer Firewall befinden, sollte HTTPS-Datenverkehr (443) zu „passwordreset.microsoftonline.com“ und „ajax.aspnetcdn.com“ zugelassen werden.

Für Umgebungen mit hybrider Domäneneinbindung gibt es ein Szenario, in dem der SSPR-Workflow vollständig ausgeführt werden kann, ohne dass ein Active Directory-Domänencontroller erforderlich ist. Konnektivität mit einem Domänencontroller ist erforderlich, um das neue Kennwort zum ersten Mal zu verwenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die im Rahmen dieses Tutorials konfigurierte Funktion nicht mehr nutzen möchten, löschen Sie das von Ihnen erstellte Intune-Gerätekonfigurationsprofil bzw. den Registrierungsschlüssel.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Benutzern das Zurücksetzen ihrer Kennwörter über den Windows 10-Anmeldebildschirm ermöglicht. Im nächsten Tutorial erfahren Sie, wie Azure Identity Protection in die Self-Service-Kennwortzurücksetzung und Multi-Factor Authentication integriert werden kann.

> [!div class="nextstepaction"]
> [Tutorial: Use risk events to trigger Multi-Factor Authentication and password changes](tutorial-risk-based-sspr-mfa.md) (Tutorial: Auslösen von Multi-Factor Authentication und Kennwortänderungen mithilfe von Risikoereignissen)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Zuweisen der Intune-Gerätekonfigurationsrichtlinie zu einer Gruppe mit Windows 10-Geräten"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Link „Kennwort zurücksetzen“ auf dem Windows 10-Anmeldebildschirm"
