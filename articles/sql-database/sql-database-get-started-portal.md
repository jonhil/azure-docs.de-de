---
title: Erstellen einer Azure SQL-Datenbank im Portal | Microsoft-Dokumentation
description: Erstellen Sie im Azure-Portal einen logischen Azure SQL-Datenbankserver und eine Datenbank, die Sie anschließend abfragen können.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 12/21/2018
ms.openlocfilehash: b8ff482f2aec406ef4c1c545db7844a861317518
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994418"
---
# <a name="quickstart-create-an-azure-sql-database-in-the-azure-portal"></a>Schnellstart: Erstellen einer Azure SQL-Datenbank im Azure-Portal

Azure SQL-Datenbank ist ein *Database as a Service*-Angebot, das Ihnen die Ausführung und Skalierung von hoch verfügbaren SQL Server-Datenbanken in der Cloud ermöglicht. In diesem Schnellstart wird gezeigt, wie Sie zum Einstieg eine Azure SQL-Datenbank im Azure-Portal erstellen und anschließend abfragen. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Melden Sie sich zum Durchführen aller Schritte in diesem Schnellstart beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-sql-database"></a>Erstellen einer SQL-Datenbank

Eine Azure SQL-Datenbank verfügt über definierte [Compute- und Speicherressourcen](sql-database-service-tiers-dtu.md). Sie erstellen die Datenbank auf einem [logischen Azure SQL-Datenbankserver](sql-database-features.md) in einer [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md).

So erstellen Sie eine SQL-Datenbank mit den AdventureWorksLT-Beispieldaten:

1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.
   
1. Wählen Sie **Datenbanken** und dann **SQL-Datenbank** aus.
   
1. Geben Sie im Formular **SQL-Datenbank** die folgenden Werte ein, oder wählen Sie sie aus: 
   
   - **Datenbankname**: Geben Sie *mySampleDatabase* ein.
   - **Abonnement**: Wählen Sie in der Dropdownliste das richtige Abonnement aus, falls es nicht angezeigt wird.  
   - **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, geben Sie *myResourceGroup* ein, und klicken Sie auf **OK**. 
   - **Quelle auswählen**: Wählen Sie in der Dropdownliste **Sample (AdventureWorksLT)** aus. 
   
   >[!IMPORTANT]
   >Sie müssen die Daten **Sample (AdventureWorksLT)** auswählen, um diesen Schnellstart und andere Schnellstarts für Azure SQL-Datenbank, in denen diese Daten verwendet werden, ausführen zu können. 
   
   ![Erstellen einer Azure SQL-Datenbank](./media/sql-database-get-started-portal/create-database-1.png)
   
1. Wählen Sie **Server** und dann **Neuen Server erstellen** aus. 
   
1. Geben Sie im Formular **Neuer Server** die folgenden Werte ein, oder wählen Sie sie aus: 
   
   - **Servername**: Geben Sie *mysqlserver* ein.
   - **Serveradministratoranmeldung**: Geben Sie *azureuser* ein. 
   - **Kennwort**: Geben Sie *Azure1234567* ein. 
   - **Kennwort bestätigen**: Geben Sie das Kennwort erneut ein.
   - **Standort**: Wählen Sie in der Dropdownliste einen gültigen Standort aus.  
   
   >[!IMPORTANT]
   >Merken oder notieren Sie sich die Serveradministratoranmeldung (Anmelde-ID) und das Kennwort, damit Sie sich für diesen und andere Schnellstarts beim Server und bei den Datenbanken anmelden können. Falls Sie die Anmeldeinformationen vergessen, können Sie auf der Seite **SQL Server** die Anmelde-ID abrufen oder das Kennwort zurücksetzen. Um die Seite **SQL Server** zu öffnen, wählen Sie nach dem Erstellen der Datenbank auf der Seite **Übersicht** für die Datenbank den Servernamen aus.
   
1. Wählen Sie **Auswählen**.
   
   ![Server erstellen](./media/sql-database-get-started-portal/create-database-server.png)
   
1. Wählen Sie im Formular **SQL-Datenbank** die Option **Tarif** aus. Sehen Sie sich die Anzahl von Datenbanktransaktionseinheiten (Database Transaction Unit, DTU) und den verfügbaren Speicher für die einzelnen Dienstebenen an.
   
   >[!NOTE]
   >In diesem Schnellstart wird das [DTU-basierte Kaufmodell](sql-database-service-tiers-dtu.md) verwendet, das [V-Kern-basierte Kaufmodell](sql-database-service-tiers-vcore.md) ist jedoch ebenfalls verfügbar.
   
   >[!NOTE]
   >In allen Regionen außer den folgenden ist im Premium-Tarif derzeit mehr als 1 TB Speicher verfügbar: „Vereinigtes Königreich, Norden“, „USA, Westen-Mitte“, „Vereinigtes Königreich, Süden2“, „China, Osten“, „US DoD, Mitte“, „Deutschland, Mitte“, „US DoD, Osten“, „US Gov, Südwesten“, „US Gov, Süden-Mitte“, „Deutschland, Nordosten“, „China, Norden“ und „US Gov, Osten“. In diesen Regionen ist der Speicher im Tarif „Premium“ auf 1 TB begrenzt. Weitere Informationen finden Sie unter [Einschränkungen von P11 und P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   
1. Wählen Sie für diesen Schnellstart die Dienstebene **Standard** und anschließend mithilfe des Schiebereglers **10 DTUs (S0)** und **1** GB Speicher aus.
   
1. Wählen Sie **Übernehmen**.  
   
   ![Auswählen des Tarifs](./media/sql-database-get-started-portal/create-database-s1.png)
   
1. Klicken Sie im Formular **SQL-Datenbank** auf **Erstellen**, um die Ressourcengruppe, den Server und die Datenbank bereitzustellen. 
   
   Die Bereitstellung nimmt einige Minuten in Anspruch. Sie können auf der Symbolleiste auf **Benachrichtigungen** klicken, um den Status des Bereitstellungsprozesses zu überwachen.

   ![Benachrichtigung](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Abfragen der SQL-Datenbank

Nachdem Sie eine Azure SQL-Datenbank erstellt haben, verwenden Sie das integrierte Abfragetool im Azure-Portal, um eine Verbindung mit der Datenbank herzustellen und die Daten abzufragen.

1. Wählen Sie auf der Seite **SQL-Datenbank** für Ihre Datenbank im Menü auf der linken Seite die Option **Abfrage-Editor (Vorschau)** aus. 
   
   ![Anmelden beim Abfrage-Editor](./media/sql-database-get-started-portal/query-editor-login.png)
   
1. Geben Sie Ihre Anmeldeinformationen ein, und klicken Sie auf **OK**.
   
1. Geben Sie die folgende Abfrage im Bereich **Abfrage-Editor** ein.
   
   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```
   
1. Klicken Sie auf **Ausführen**, und sehen Sie sich dann die Abfrageergebnisse im Bereich **Ergebnisse** an.

   ![Ergebnisse im Abfrage-Editor](./media/sql-database-get-started-portal/query-editor-results.png)
   
1. Schließen Sie die Seite **Abfrage-Editor**, und klicken Sie auf **OK**, um Ihre nicht gespeicherten Änderungen zu verwerfen, wenn Sie dazu aufgefordert werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Behalten Sie diese Ressourcengruppe, den SQL-Server und die SQL-Datenbank bei, wenn Sie mit den [nächsten Schritten](#next-steps) fortfahren möchten, um zu erfahren, wie Sie eine Verbindung herstellen und Ihre Datenbank mit verschiedenen Methoden abfragen. 

Wenn Sie diese Ressourcen nicht mehr benötigen, können Sie sie wie folgt löschen:

1. Wählen Sie im Azure-Portal im Menü auf der linken Seite die Option **Ressourcengruppen** und dann **myResourceGroup** aus.
1. Wählen Sie auf der Seite für die Ressourcengruppe die Option **Ressourcengruppe löschen** aus. 
1. Geben Sie *myResourceGroup* in das Feld ein, und wählen Sie dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

- Sie müssen eine Firewallregel auf Serverebene erstellen, um über lokale Tools oder Remotetools eine Verbindung mit Ihrer Azure SQL-Datenbank herstellen zu können. Weitere Informationen finden Sie unter [Erstellen einer Firewallregel auf Serverebene](sql-database-get-started-portal-firewall.md).
- Nachdem Sie eine Firewallregel auf Serverebene erstellt haben, können Sie mit verschiedenen Tools und Programmiersprachen eine [Verbindung mit Ihrer Datenbank herstellen und Abfragen ausführen](sql-database-connect-query.md). 
  - [Verbinden und Abfragen mit SQL Server Management Studio (SSMS)](sql-database-connect-query-ssms.md)
  - [Verbinden und Abfragen mit Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Informationen zum Erstellen von Azure SQL-Datenbanken mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) finden Sie unter [Azure CLI-Beispiele](sql-database-cli-samples.md).
- Informationen zum Erstellen von Azure SQL-Datenbanken mit Azure PowerShell finden Sie unter [Azure PowerShell-Beispiele](sql-database-powershell-samples.md).
