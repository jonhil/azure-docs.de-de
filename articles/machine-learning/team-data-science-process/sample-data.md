---
title: 'Datenstichproben an anderen Azure Storage-Speicherorten: Team Data Science-Prozess'
description: Datenstichproben in Azure-Blobcontainern, SQL Server- und Hive-Tabellen, um sie auf eine kleinere, aber repräsentative und überschaubare Größe zu reduzieren.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7bc4174121a58353219e73eef86ec6c64f806647
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133212"
---
# <a name="heading"></a>Entnehmen von Datenstichproben in Azure-Blobcontainern, SQL Server und Hive-Tabellen

In den folgenden Artikeln wird das Entnehmen von Stichproben aus Daten beschrieben, die an einem von drei verschiedenen Azure-Speicherorten gespeichert sind:

* [**Azure Blob-Containerdaten:**](sample-data-blob.md) Stichproben werden durch programmgesteuertes Herunterladen entnommen, um dann anschließend mit Python-Code Stichproben zu erstellen.
* [**SQL Server-Daten:**](sample-data-sql-server.md) Stichproben werden mithilfe von SQL und der Python-Programmiersprache erstellt. 
* [**Hive-Tabellendaten:**](sample-data-hive.md) Stichproben werden mit Hive-Abfragen erstellt.

Dieser Stichprobentask ist ein Schritt im [Team Data Science-Prozess (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Warum sollten Datenstrichproben entnommen werden?**

Wenn das zu analysierende Dataset groß ist, sollten Sie in der Regel eine Komprimierung der Daten durchführen, um eine geringere aber immer noch repräsentative Größe zu erhalten. Dies erleichtert das Verständnis der Daten, das Durchsuchen und die Funktionsverarbeitung. Die Funktion besteht innerhalb des Cortana-Analyseprozesses darin, schnell Prototypen der Funktionen zur Datenverarbeitung und Modelle für das maschinelle Lernen zu erstellen.

