---
title: Includedatei
description: Includedatei
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/24/2018
ms.openlocfilehash: 6f73b15ed16cfe26bf14e60a5206568e1a1564fd
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53594315"
---
Melden Sie sich mit den Anmeldeinformationen, die Sie für das Azure-Abonnement verwenden, beim [Azure-Portal](https://portal.azure.com/) an. 

Das Dashboard für Arbeitsbereiche im Portal wird nur in den Browsern Edge, Chrome und Firefox unterstützt.

   ![Azure-Portal](./media/aml-create-in-portal/portal-dashboard.png)

Wählen Sie links oben im Portal **Ressource erstellen** aus.

   ![Erstellen einer Ressource im Azure-Portal](./media/aml-create-in-portal/portal-create-a-resource.png)

Geben Sie in der Suchleiste **Machine Learning** ein. Wählen Sie das Suchergebnis **Machine Learning-Dienstarbeitsbereich** aus.

   ![Suchen nach einem Arbeitsbereich](./media/aml-create-in-portal/allservices-search.PNG)

Scrollen Sie im Bereich **ML-Dienstarbeitsbereich** nach unten, und wählen Sie **Erstellen** aus, um zu beginnen.

   ![Erstellen](./media/aml-create-in-portal/portal-create-button.png)

Konfigurieren Sie Ihren Arbeitsbereich im Bereich **ML-Dienstarbeitsbereich**.

   Feld|BESCHREIBUNG
   ---|---
   Arbeitsbereichname |Geben Sie einen eindeutigen Namen ein, der Ihren Arbeitsbereich identifiziert. In diesem Beispiel verwenden wir **docs-ws**. Namen müssen in der Ressourcengruppe eindeutig sein. Verwenden Sie einen Namen, der leicht zu merken ist und sich von den von anderen Benutzern erstellten Arbeitsbereichen unterscheidet.  
   Abonnement |Wählen Sie das gewünschte Azure-Abonnement aus.
   Ressourcengruppe | Verwenden Sie eine vorhandene Ressourcengruppe in Ihrem Abonnement, oder geben Sie einen Namen ein, um eine neue Ressourcengruppe zu erstellen. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. In diesem Beispiel verwenden wir **docs-aml**. 
   Standort | Wählen Sie den Standort aus, der Ihren Benutzern und den Datenressourcen am nächsten ist. Dort wird der Arbeitsbereich erstellt.

   ![Arbeitsbereich erstellen](./media/aml-create-in-portal/workspace-create.png)

Wählen Sie **Erstellen** aus, um den Erstellungsprozess zu starten. Es kann einige Augenblicke dauern, bis der Arbeitsbereich erstellt wurde.

Um den Status der Bereitstellung zu überprüfen, wählen Sie das Benachrichtigungssymbol (**Glocke**) in der Symbolleiste aus.

   ![Status der Arbeitsbereichserstellung](./media/aml-create-in-portal/notifications.png)

Wenn der Vorgang abgeschlossen ist, wird eine Erfolgsmeldung zur Bereitstellung angezeigt. Diese finden Sie auch im Abschnitt „Benachrichtigungen“. Um den neuen Arbeitsbereich anzuzeigen, wählen Sie **Zu Ressource wechseln** aus.
