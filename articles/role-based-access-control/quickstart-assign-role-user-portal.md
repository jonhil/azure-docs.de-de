---
title: 'Tutorial: Gewähren des Zugriffs für einen Benutzer mithilfe der RBAC und des Azure-Portals | Microsoft-Dokumentation'
description: Verwenden Sie die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC), um einem Benutzer Berechtigungen zu erteilen, indem Sie ihm eine Rolle im Azure-Portal zuweisen.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.openlocfilehash: 8caa5c3b33ac1b483429251e0c1256636c4ece1a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634471"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>Tutorial: Gewähren des Zugriffs für einen Benutzer mithilfe von RBAC und Azure-Portal

Der Zugriff auf Ressourcen in Azure wird mithilfe der [rollenbasierten Zugriffssteuerung](overview.md) (Role-Based Access Control, RBAC) verwaltet. In diesem Tutorial gewähren Sie einem Benutzer Zugriff, um virtuelle Computer in einer Ressourcengruppe zu erstellen und zu verwalten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Gewähren des Zugriffs für einen Benutzer im Ressourcengruppenbereich
> * Zugriff entfernen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter http://portal.azure.com beim Azure-Portal an.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

1. Wählen Sie in der Navigationsliste die Option **Ressourcengruppen** aus.

1. Wählen Sie **Hinzufügen**, um das Blatt **Ressourcengruppe** zu öffnen.

   ![Hinzufügen einer neuen Ressourcengruppe](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Geben Sie für **Ressourcengruppenname** die Zeichenfolge **rbac-resource-group** ein.

1. Wählen Sie ein Abonnement und einen Standort aus.

1. Wählen Sie **Erstellen** aus, um die Ressourcengruppe zu erstellen.

1. Wählen Sie **Aktualisieren** aus, um die Liste der Ressourcengruppen zu aktualisieren.

   Die neue Ressourcengruppe wird in der Liste Ihrer Ressourcengruppen angezeigt.

   ![Ressourcengruppenliste](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Gewähren von Zugriff

In RBAC erstellen Sie zum Gewähren des Zugriffs eine Rollenzuweisung.

1. Wählen Sie in der Liste der **Ressourcengruppen** die neue Ressourcengruppe **rbac-resource-group** aus.

1. Klicken Sie auf **Zugriffssteuerung (IAM)**.

1. Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um die aktuelle Liste mit den Rollenzuweisungen anzuzeigen.

   ![Blatt „Zugriffssteuerung (IAM)“ für Ressourcengruppe](./media/quickstart-assign-role-user-portal/access-control.png)

1. Wählen Sie die Registerkarte **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

   Wenn Sie keine Berechtigungen zum Zuweisen von Rollen haben, ist die Option „Rollenzuweisung hinzufügen“ deaktiviert.

   ![Bereich „Rollenzuweisung hinzufügen“](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Wählen Sie in der Dropdownliste **Rolle** die Rolle **Mitwirkender für virtuelle Computer** aus.

1. Wählen Sie in der Liste **Auswählen** sich selbst oder einen anderen Benutzer aus.

1. Wählen Sie **Speichern** aus, um die Rollenzuweisung zu erstellen.

   Nach einigen Augenblicken wird dem Benutzer die Rolle „Mitwirkender für virtuelle Computer“ im Bereich der Ressourcengruppe „rbac-resource-group“ zugewiesen.

   ![Rollenzuweisung: Mitwirkender von virtuellen Computern](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Zugriff entfernen

In RBAC entfernen Sie eine Rollenzuweisung, um den Zugriff zu entfernen.

1. Fügen Sie in der Liste der Rollenzuweisungen ein Häkchen neben dem Benutzer mit der Rolle „Mitwirkender für virtuelle Computer“ hinzu.

1. Klicken Sie auf **Entfernen**.

   ![Nachricht zum Entfernen der Rollenzuweisung](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Klicken Sie in der Nachricht zum Entfernen der Rollenzuweisung auf **Ja**.

## <a name="clean-up"></a>Bereinigen

1. Wählen Sie in der Navigationsliste die Option **Ressourcengruppen** aus.

1. Wählen Sie **rbac-resource-group** aus, um die Ressourcengruppe zu öffnen.

1. Wählen Sie **Ressourcengruppe löschen** aus, um die Ressourcengruppe zu löschen.

   ![Ressourcengruppe löschen](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Geben Sie auf dem Blatt **Löschvorgang bestätigen**  den Namen der Ressourcengruppe ein: **rbac-resource-group**.

1. Wählen Sie **Löschen** aus, um die Ressourcengruppe zu löschen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Gewähren des Zugriffs für einen Benutzer mithilfe von RBAC und PowerShell](tutorial-role-assignments-user-powershell.md)

