---
title: 'Migrieren des TDE-Zertifikats: Verwaltete Azure SQL-Datenbank-Instanz | Microsoft Docs'
description: Migrieren des Zertifikats zum Schutz des Datenbankverschlüsselungsschlüssels einer Datenbank mit transparenter Datenverschlüsselung (TDE) zu einer verwalteten Azure SQL-Datenbank-Instanz
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
manager: craigg
ms.date: 08/09/2018
ms.openlocfilehash: 078a64bf625fad15b66a3c4e6e31e798f675fc33
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161776"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrieren des Zertifikats einer durch TDE geschützten Datenbank zu einer verwalteten Azure SQL-Instanz

Beim Migrieren einer durch die [Transparente Datenverschlüsselung (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) geschützten Datenbank zu einer verwalteten Azure SQL-Datenbank-Instanz mithilfe der nativen Wiederherstellungsoption muss das entsprechende Zertifikat aus dem lokalen oder IaaS-SQL-Server vor der Wiederherstellung der Datenbank migriert werden. Dieser Artikel stellt Ihnen schrittweise den Vorgang der manuellen Migration des Zertifikats zu einer verwalteten Azure SQL-Datenbank-Instanz vor:

> [!div class="checklist"]
> * Exportieren des Zertifikats in eine PFX-Datei (Personal Information Exchange)
> * Extrahieren des Zertifikats aus der Datei in eine Base64-Zeichenfolge
> * Hochladen der Zeichenfolge mit einem PowerShell-Cmdlet

Eine alternative Option, einen vollständig verwalteten Dienst für die nahtlose Migration der durch TDE geschützten Datenbank und des entsprechenden Zertifikats zu verwenden, finden Sie unter [Migrieren Ihrer lokale Datenbank zu einer verwalteten Instanz mit Azure Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Die transparente Datenverschlüsselung (TDE) für die verwaltete Azure SQL-Datenbank-Instanz funktioniert im dienstverwalteten Modus. Das migrierte Zertifikat wird nur für die Wiederherstellung der durch TDE geschützten Datenbank verwendet. Kurz nach dem Abschluss der Wiederherstellung wird das migrierte Zertifikat durch ein anderes, vom System verwaltetes Zertifikat ersetzt.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- Das [Pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx)-Befehlszeilentool, das auf dem lokalen Server oder einem anderen Computer mit Zugriff auf das Zertifikat installiert ist, das als Datei exportiert wurde. Das Pvk2pfx-Tool ist Bestandteil des [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), einer eigenständigen Befehlszeilenumgebung.
- [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell), Version 5.0 oder höher.
- AzureRM PowerShell-Modul ist [installiert und aktualisiert](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
- [AzureRM.Sql-Modul](https://www.powershellgallery.com/packages/AzureRM.Sql) Version 4.10.0 oder höher.
  Führen Sie die folgenden Befehle in PowerShell zum Installieren/Aktualisieren des PowerShell-Moduls aus:

   ```powershell
   Install-Module -Name AzureRM.Sql
   Update-Module -Name AzureRM.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Exportieren des TDE-Zertifikats in eine PFX-Datei (Personal Information Exchange)

Das Zertifikat kann direkt aus dem SQL-Quellserver oder aus dem Zertifikatspeicher exportiert werden, wenn es dort gespeichert ist

### <a name="export-certificate-from-the-source-sql-server"></a>Exportieren des Zertifikats aus dem SQL-Quellserver

Führen Sie die folgenden Schritte aus, um das Zertifikat mit SQL Server Management Studio zu exportieren und in das PFX-Format zu konvertieren. Die generischen Namen *TDE_Cert* und *full_path* werden für die Zertifikat- und Dateinamen und Pfade in diesen Schritten verwendet. Sie sollten durch die tatsächlichen Namen ersetzt werden.

1. Öffnen Sie in SSMS ein neues Abfragefenster, und stellen Sie eine Verbindung mit dem SQL-Quellserver her.
2. Verwenden Sie das folgende Skript, um durch TDE geschützte Datenbanken aufzulisten und den Namen des Zertifikats abzurufen, das die Verschlüsselung der zu migrierenden Datenbank schützt:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![Liste der TDE-Zertifikate](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

3. Führen Sie das folgende Skript zum Exportieren des Zertifikats in zwei Dateien (CER- und PVK-Datei) aus, die die Informationen zum öffentlichen und privaten Schlüssel enthalten:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![Sichern des TDE-Zertifikats](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

4. Verwenden Sie die PowerShell-Konsole, um Zertifikatinformationen mit dem Pvk2Pfx-Tool aus zwei neu erstellten Dateien in eine PFX-Datei (Personal Information Exchange) zu kopieren:

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Exportieren des Zertifikats aus dem Zertifikatspeicher

Wenn das Zertifikat im Zertifikatspeicher des lokalen Computers mit SQL Server gespeichert wird, kann es mithilfe der folgenden Schritte exportiert werden:

1. Öffnen Sie die PowerShell-Konsole, und führen Sie den folgenden Befehl aus, um das Zertifikate-Snap-In der Microsoft-Verwaltungskonsole zu öffnen:

   ```powershell
   certlm
   ```

2. Erweitern Sie im Zertifikate-MMC-Snap-In den Pfad „Personal > Certificates“, um eine Liste der Zertifikate anzuzeigen.

3. Klicken Sie mit der rechten Maustaste auf das Zertifikat, und klicken Sie dann auf „Exportieren“.

4. Führen Sie den Assistenten zum Exportieren von Zertifikaten und privaten Schlüsseln in das PFX-Format (Personal Information Exchange) aus.

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Hochladen des Zertifikats in eine verwaltete Azure SQL-Datenbank-Instanz mit dem Azure PowerShell-Cmdlet

1. Beginnen Sie mit den Vorbereitungsschritten in PowerShell:

   ```powershell
   # Import the module into the PowerShell session
   Import-Module AzureRM
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzureRmAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzureRmSubscription
   # Set subscription for the session (replace Guid_Subscription_Id with actual subscription id)
   Select-AzureRmSubscription Guid_Subscription_Id
   ```

2. Nachdem alle Vorbereitungsschritte abgeschlossen wurden, führen Sie die folgenden Befehle zum Hochladen des Base64-codierten Zertifikats in die verwaltete Zielinstanz aus:

   ```powershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzureRmSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

Das Zertifikat ist nun für die angegebene verwaltete Instanz verfügbar, und die Sicherung der entsprechenden durch TDE geschützten Datenbank kann erfolgreich wiederhergestellt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie das Zertifikat, das den Verschlüsselungsschlüssel der Datenbank mit TDE (transparente Datenverschlüsselung) schützt, aus dem lokalen oder IaaS-SQL-Server zu einer verwalteten Azure SQL-Instanz migriert wird.

Lesen Sie [Wiederherstellen einer Datenbanksicherung in einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-get-started-restore.md), um zu erfahren, wie Sie eine Datenbanksicherung in einer verwalteten Azure SQL-Datenbank-Instanz wiederherstellen können.
