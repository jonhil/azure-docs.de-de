---
title: Erstellen eines Batch-Kontos im Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Azure Batch-Konto im Azure-Portal erstellen, um umfangreiche parallele Workloads in der Cloud auszuführen.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/18/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e526c5f2f446a1ae8134a3e2ca72fcc42024d904
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724093"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Erstellen eines Batch-Kontos mit dem Azure-Portal

Hier erfahren Sie, wie Sie im [Azure-Portal][azure_portal] ein Azure Batch-Konto erstellen und geeignete Kontoeigenschaften für Ihr Computeszenario auswählen. Außerdem erfahren Sie, wo Sie wichtige Kontoeigenschaften wie Zugriffsschlüssel und Konto-URLs finden.

Hintergrundinformationen zu Batch-Konten und -Szenarien finden Sie in der [Funktionsübersicht](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Erstellen eines Batch-Kontos

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Melden Sie sich beim [Azure-Portal][azure_portal] an.

1. Klicken Sie auf **Ressource erstellen** > **Compute** > **Batch-Dienst**.

    ![Batch im Marketplace][marketplace_portal]

1. Geben Sie unter **Neues Batch-Konto** die Einstellungen ein. Orientieren Sie sich an den folgenden Details:

    ![Erstellen eines Batch-Kontos][account_portal]

    a. **Kontoname**: Der gewählte Name muss innerhalb der Azure-Region, in der das Konto erstellt wird, eindeutig sein (siehe **Standort** weiter unten). Der Kontoname darf nur Kleinbuchstaben und Zahlen enthalten und muss 3 bis 24 Zeichen lang sein.

    b. **Abonnement**: Das Abonnement, in dem das Batch-Konto erstellt werden soll. Wenn Sie nur über ein Abonnement verfügen, ist es standardmäßig ausgewählt.

    c. **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe für Ihr neues Batch-Konto aus, oder erstellen Sie eine neue.

    d. **Standort**: Die Azure-Region, in der das Batch-Konto erstellt werden soll. Nur die von Ihrem Abonnement und der Ressourcengruppe unterstützten Regionen werden als Optionen angezeigt.

    e. **Speicherkonto** (optional): Ein Azure Storage-Konto, das Sie dem Batch-Konto zuordnen. Dies ist bei den meisten Batch-Konten empfehlenswert. Informationen zu den Optionen für Speicherkonten in Batch finden Sie unter [Entwickeln von parallelen Computelösungen in größerem Umfang mit Batch](batch-api-basics.md#azure-storage-account). Wählen Sie im Portal ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues.

      ![Speicherkonto erstellen][storage_account]

    f. **Poolzuordnungsmodus**: In den meisten Szenarien übernehmen Sie die Standardeinstellung **Batch-Dienst**.

1. Wählen Sie **Erstellen**, um das Konto zu erstellen.



## <a name="view-batch-account-properties"></a>Anzeigen der Eigenschaften des Batch-Kontos
Klicken Sie nach der Kontoerstellung auf das Konto, um auf dessen Einstellungen und Eigenschaften zuzugreifen. Über das linke Menü können Sie auf alle Kontoeinstellungen und -eigenschaften zugreifen.

![Seite „Batch-Konto“ im Azure-Portal][account_blade]

* **Batch-Kontoname, URL und Schlüssel**: Wenn Sie eine Anwendung mit den [Batch-APIs](batch-apis-tools.md#azure-accounts-for-batch-development) entwickeln, benötigen Sie eine Konto-URL und einen Schlüssel für den Zugriff auf Ihre Batch-Ressourcen. (Batch unterstützt auch die Azure Active Directory-Authentifizierung.)

    Klicken Sie zum Anzeigen der Zugriffsinformationen für das Batch-Konto auf **Schlüssel**.

    ![Batch-Kontoschlüssel im Azure-Portal][account_keys]

* Klicken Sie auf **Speicherkonto**, um den Namen und die Schlüssel des mit Ihrem Batch-Konto verknüpften Speicherkontos anzuzeigen.

* Klicken Sie zum Anzeigen der für das Batch-Konto geltenden Ressourcenkontingente auf **Kontingente**. Ausführliche Informationen finden Sie unter [Batch-Dienst – Kontingente und Limits](batch-quota-limit.md).


## <a name="additional-configuration-for-user-subscription-mode"></a>Zusätzliche Konfiguration für den Benutzerabonnementmodus

Wenn Sie ein Batch-Konto im Benutzerabonnementmodus erstellen möchten, führen Sie vor der Erstellung des Kontos die folgenden zusätzlichen Schritte aus.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Gewähren des Zugriffs auf Ihr Abonnement für Azure Batch (einmaliger Vorgang)
Wenn Sie Ihr erstes Batch-Konto im Modus „Benutzerabonnement“ erstellen, müssen Sie Ihr Abonnement bei Batch registrieren. (Falls Sie diesen Schritt bereits ausgeführt haben, fahren Sie mit dem nächsten Abschnitt fort.)

1. Melden Sie sich beim [Azure-Portal][azure_portal] an.

1. Klicken Sie auf **Alle Dienste** > **Abonnements** und anschließend auf das Abonnement, das Sie für das Batch-Konto verwenden möchten.

1. Klicken Sie auf der Seite **Abonnement** auf **Ressourcenanbieter**, und suchen Sie nach **Microsoft.Batch**. Überprüfen Sie, ob der Ressourcenanbieter **Microsoft.Batch** im Abonnement registriert ist. Wenn er nicht registriert ist, klicken Sie auf den Link **Registrieren**.

    ![Registrieren des Microsoft.Batch-Anbieters][register_provider]

1. Wählen Sie auf der Seite **Abonnement** die Optionen **Zugriffssteuerung (IAM)** > **Rollenzuweisungen** > **Rollenzuweisung hinzufügen** aus.

    ![Abonnementzugriffssteuerung][subscription_access]

1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** die Rolle **Mitwirkender** aus, und suchen Sie die Batch-API. Suchen Sie diese Zeichenfolgen, bis Sie die API gefunden haben:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Neuere Azure AD-Mandanten verwenden diesen Namen unter Umständen.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** ist die ID für die Batch-API. 

1. Wenn Sie die Batch-API gefunden haben, wählen Sie diese aus, und klicken Sie auf **Speichern**.

    ![Hinzufügen von Batch-Berechtigungen][add_permission]

### <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors
Im Modus „Benutzerabonnement“ wird ein Azure-Schlüsseltresor benötigt. Dieser muss der gleichen Ressourcengruppe angehören wie das zu erstellende Batch-Konto. Stellen Sie sicher, dass sich die Ressourcengruppe in einer Region befindet, in der Batch [verfügbar](https://azure.microsoft.com/regions/services/) ist und die von Ihrem Abonnement unterstützt wird.

1. Klicken Sie im [Azure-Portal][azure_portal] auf **Neu** > **Sicherheit** > **Key Vault**.

1. Geben Sie auf der Seite **Schlüsseltresor erstellen** einen Namen für den Schlüsseltresor ein, und erstellen Sie eine Ressourcengruppe in der Region, die Sie für Ihr Batch-Konto verwenden möchten. Behalten Sie bei den übrigen Einstellungen die Standardwerte bei, und klicken Sie auf **Erstellen**.

Verwenden Sie beim Erstellen des Batch-Kontos im Modus „Benutzerabonnement“ die Ressourcengruppe für den Schlüsseltresor, geben Sie als Poolzuordnungsmodus **Benutzerabonnement** an, und wählen Sie den Schlüsseltresor aus.

## <a name="other-batch-account-management-options"></a>Weitere Optionen für die Verwaltung von Batch-Konten
Neben der Verwendung des Azure-Portals stehen Ihnen zum Erstellen und Verwalten von Batch-Konten die folgenden Tools zur Verfügung:

* [Batch-PowerShell-Cmdlets](batch-powershell-cmdlets-get-started.md)
* [Azure-Befehlszeilenschnittstelle](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu den Konzepten und Features des Batch-Diensts finden Sie in der [Funktionsübersicht für Batch](batch-api-basics.md). In diesem Artikel werden die primären Batch-Ressourcen (wie etwa Pools, Computeknoten, Aufträge und Aufgaben) beschrieben, und er enthält eine Übersicht über die Dienstfeatures für umfangreiche Computeworkloads.
* Informieren Sie sich über die Grundlagen der Entwicklung einer Batch-fähigen Anwendung mit der [Batch-.NET-Clientbibliothek](quick-run-dotnet.md) oder mit [Python](quick-run-python.md). In diesen Schnellstarts werden Sie durch eine Beispielanwendung geführt, die den Batch-Dienst zum Ausführen einer Workload auf mehreren Computeknoten verwendet und Azure Storage zum Bereitstellen und Abrufen von Workloaddateien nutzt.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png

