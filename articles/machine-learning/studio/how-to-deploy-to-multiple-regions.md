---
title: Bereitstellen eines Studio-Webdiensts in mehreren Regionen – Azure Machine Learning Studio | Microsoft-Dokumentation
description: Schritte zum Bereitstellen (Kopieren) eines neuen Webdiensts in anderen Regionen. Stellen Sie einfach einen Webdienst in mehreren Regionen bereit, ohne dass mehrere Abonnements oder Arbeitsbereiche erforderlich sind.
services: machine-learning
documentationcenter: ''
author: ericlicoding
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.custom: seodec18
ms.author: amlstudiodocs
ms.openlocfilehash: 742f462ebc3bd191a045be2a0213b1d8bc52adc5
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252676"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service-to-multiple-regions"></a>Bereitstellen eines Azure Machine Learning Studio-Webdiensts in mehreren Regionen

Die neuen Azure-Webdienste ermöglichen Ihnen das einfache Bereitstellen eines Azure Machine Learning Studio-Webdiensts in mehreren Regionen, ohne dass mehrere Abonnements oder Arbeitsbereiche erforderlich sind. 

Die Preise sind regionsspezifisch. Daher müssen Sie einen Abrechnungsplan für jede Region definieren, in der der Webdienst bereitgestellt wird.

## <a name="to-create-a-plan-in-another-region"></a>So erstellen Sie einen Plan in einer anderen Region
1. Melden Sie sich bei [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/)an.
2. Klicken Sie auf die Menüoption **Plans** .
3. Klicken Sie auf der Übersichtsseite „Plans“ auf **New**.
4. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, in dem sich der neue Plan befinden soll.
5. Wählen Sie in der Dropdownliste **Region** eine Region für den neuen Plan aus. Im Abschnitt **Plan Options** auf der Seite werden die Planoptionen für die ausgewählte Region angezeigt.
6. Wählen Sie in der Dropdownliste **Resource Group** eine Ressourcengruppe für den Plan aus. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. Geben Sie in **Plan Name** den Namen des Plans ein.
8. Klicken Sie unter **Plan Options**auf den Abrechnungstarif des neuen Plans.
9. Klicken Sie auf **Create**.

## <a name="deploying-the-web-service-to-another-region"></a>Bereitstellen des Webdiensts in einer anderen Region
1. Klicken Sie auf die Menüoption **Web Services** .
2. Wählen Sie den Webdienst aus, den Sie in einer neuen Region bereitstellen.
3. Klicken Sie auf **Copy**.
4. Geben Sie in **Web Service Name**einen neuen Namen für den Webdienst ein.
5. Geben Sie in **Web service description**eine Beschreibung des Webdiensts ein.
6. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, in dem sich der neue Webdienst befinden soll.
7. Wählen Sie in der Dropdownliste **Resource Group** eine Ressourcengruppe für den Webdienst aus. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Wählen Sie in der Dropdownliste **Region** die Region aus, in der der Webdienst bereitgestellt werden soll.
9. Wählen Sie in der Dropdownliste **Storage account** ein Speicherkonto aus, in dem der Webdienst gespeichert werden soll.
10. Wählen Sie in der Dropdownliste **Price Plan** einen Plan für die Region aus, die Sie in Schritt 8 gewählt haben.
11. Klicken Sie auf **Copy**.

