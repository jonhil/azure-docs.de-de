---
title: Konfigurieren einer verwalteten Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine verwaltete Azure SQL-Datenbank-Instanz konfigurieren und verwalten.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: 10a33ac09b5cfa588bef88e6c1587d67036b1aef
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439517"
---
# <a name="how-to-use-managed-instance"></a>Verwenden einer verwalteten Instanz

In diesem Abschnitt sind verschiedene Anleitungen, Skripts und Erläuterungen aufgeführt, die beim Verwalten und Konfigurieren einer verwalteten Azure SQL-Datenbank-Instanz hilfreich sein können.

## <a name="migration"></a>Migration

- [Migrieren zur verwalteten Instanz](sql-database-managed-instance-migrate.md): Hier finden Sie Informationen zum empfohlenen Migrationsprozess und zu den Tools für die Migration zur verwalteten Instanz.

- [Migrieren von TDE zur verwalteten Instanz](sql-database-managed-instance-migrate-tde-certificate.md): Wenn Ihre SQL Server-Datenbank mit transparenter Datenverschlüsselung (TDE) geschützt ist, müssen Sie ein Zertifikat migrieren, das die verwaltete Instanz zum Entschlüsseln der Sicherung verwenden kann, die Sie in Azure wiederherstellen möchten.

## <a name="network-configuration"></a>Netzwerkkonfiguration

- [Bestimmen der Größe des Subnetzes für verwaltete Instanzen](sql-database-managed-instance-determine-size-vnet-subnet.md): Die verwaltete Instanz wird in einem dedizierten Subnetz platziert, dessen Größe nach dem Hinzufügen der Ressource nicht geändert werden kann. Deshalb müssen Sie berechnen, welcher IP-Adressbereich für das Subnetz erforderlich ist, je nach Anzahl und Arten von Instanzen, die im Subnetz bereitgestellt werden sollen.
- [Erstellen eines neuen VNET und Subnetzes für die verwaltete Instanz](sql-database-managed-instance-create-vnet-subnet.md): Azure-VNET und Subnetz, in denen Ihre verwalteten Instanzen bereitgestellt werden sollen, müssen entsprechend den [hier beschriebenen Netzwerkanforderungen](sql-database-managed-instance-connectivity-architecture.md#network-requirements) konfiguriert werden. In dieser Anleitung ist die einfachste Möglichkeit zum Erstellen des neuen VNET und Subnetzes angegeben, sodass sie für verwaltete Instanzen ordnungsgemäß konfiguriert sind.
- [Konfigurieren eines vorhandenen VNET und Subnetzes für die verwaltete Instanz](sql-database-managed-instance-configure-vnet-subnet.md): Wenn Sie das vorhandene VNET und Subnetz so konfigurieren möchten, dass darin verwaltete Instanzen bereitgestellt werden, finden Sie hier ein Skript, mit dem die [Netzwerkanforderungen](sql-database-managed-instance-connectivity-architecture.md#network-requirements) überprüft werden und das Subnetz entsprechend den Anforderungen konfiguriert wird.
- [Konfigurieren von benutzerdefiniertem DNS](sql-database-managed-instance-custom-dns.md): Sie müssen benutzerdefiniertes DNS konfigurieren, wenn Sie von der verwalteten Instanz über einen Verbindungsserver mit Datenbank-E-Mail-Profilen auf externe Ressourcen in den benutzerdefinierten Domänen zugreifen möchten.
- [Synchronisieren der Netzwerkkonfiguration](sql-database-managed-instance-sync-network-configuration.md): Möglicherweise können Sie trotz [Integrieren Ihrer App in ein Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md) keine Verbindung mit der verwalteten Instanz herstellen. Sie können dann versuchen, die Netzwerkkonfiguration für Ihren Dienstplan zu aktualisieren.
- [Ermitteln der IP-Adresse des Verwaltungsendpunkts](sql-database-managed-instance-find-management-endpoint-ip-address.md): Die verwaltete Instanz verwendet den öffentlichen Endpunkt nur zu Verwaltungszwecken. Mit dem hier beschriebenen Skript können Sie die IP-Adresse des Verwaltungsendpunkts ermitteln.
- [Überprüfen des integrierten Firewallschutzes](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md): Die verwaltete Instanz ist durch eine integrierte Firewall geschützt, die den Datenverkehr nur an erforderlichen Ports zulässt. Mit dem in dieser Anleitung beschriebenen Skript können Sie die Regeln für die integrierte Firewall überprüfen.
- [Herstellen einer Verbindung mit Anwendungen](sql-database-managed-instance-connect-app.md): Die verwaltete Instanz wird in Ihrem eigenen privaten Azure-VNET mit privater IP-Adresse platziert. Hier lernen Sie verschiedene Möglichkeiten zum Verbinden von Anwendungen mit Ihrer verwalteten Instanz kennen.

## <a name="feature-configuration"></a>Featurekonfiguration

- [Transaktionsreplikation](replication-with-sql-database-managed-instance.md): Dies ermöglicht Ihnen das Replizieren Ihrer Daten zwischen verwalteten Instanzen oder von einem lokalen SQL Server zur verwalteten Instanz und umgekehrt. In dieser Anleitung finden Sie weitere Informationen zum Verwenden und Konfigurieren der Transaktionsreplikation.
- [Konfigurieren der Bedrohungserkennung](sql-database-managed-instance-threat-detection.md): Die [Bedrohungserkennung](sql-database-threat-detection-overview.md) ist ein integriertes Azure SQL-Datenbank-Feature, mit dem verschiedene potenzielle Angriffe, z.B. die Einschleusung von SQL-Befehlen oder ein Zugriff von verdächtigen Standorten, erkannt werden. In dieser Anleitung erfahren Sie, wie Sie die [Bedrohungserkennung](sql-database-threat-detection-overview.md) für die verwaltete Instanz aktivieren und konfigurieren.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Anleitungen zum Verwenden einer Einzeldatenbank](sql-database-howto-single-database.md).