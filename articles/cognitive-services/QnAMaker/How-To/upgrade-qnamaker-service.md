---
title: 'Ausführen eines Upgrades für Ihren QnA Maker-Dienst: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Sie können sich nach der erstmaligen Erstellung für ein Upgrade einzelner Komponenten des QnA Maker-Stapels entscheiden.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 5e5392f04b7894f0fe39a940865035d967c2d19a
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53629937"
---
# <a name="upgrade-your-qna-maker-service"></a>Upgrade Ihres QnA Maker-Diensts
Sie können sich nach der erstmaligen Erstellung für ein Upgrade einzelner Komponenten des QnA Maker-Stapels entscheiden. Die Details der abhängigen Komponenten und die Auswahl der SKUs finden Sie [hier](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>Upgrade der QnA Maker Management-SKU

Wenn Sie in Ihrer Wissensdatenbank weitere Fragen und Antworten benötigen, die über Ihren aktuellen Tarif hinausgehen, upgraden Sie den Tarif Ihres QnA Maker-Diensts. 

So führen Sie ein Upgrade der QnA Maker Management-SKU aus:

1. Wechseln Sie im Azure-Portal zu Ihrer QnA Maker-Ressource, und wählen Sie **Tarif** aus.

    ![QnA Maker-Ressource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Wählen Sie die passende SKU aus, und drücken Sie **Auswählen**.

    ![QnA Maker-Preise](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Upgrade von App Service

 Wenn Ihre Wissensdatenbank mehr Anforderungen von Ihrer Client-App verarbeiten muss, upgraden Sie den Tarif Ihres App-Diensts.

Sie können den App Service [hochskalieren](https://docs.microsoft.com/azure/app-service/web-sites-scale) oder herunterskalieren.

1. Navigieren Sie zur App Service-Ressource im Azure-Portal, und wählen Sie nach Bedarf die Optionen **scale up** (zentral hochskalieren) oder **scale down** (zentral herunterskalieren) aus.

    ![QnA Maker App Service-Staffelung](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Upgrade des Azure Search-Diensts

Wenn Sie voraussichtlich eine Vielzahl von Wissensdatenbanken benötigen, upgraden Sie den Tarif Ihres Azure Search-Dienst. 

Derzeit ist es nicht möglich, an Ort und Stelle ein Upgrade der Azure Search-SKU auszuführen. Allerdings können Sie eine neue Azure Search-Ressource mit der gewünschten SKU erstellen, die Daten in der neuen Ressource wiederherstellen und anschließend eine Verknüpfung mit dem QnA Maker-Stapel herstellen.

1. Erstellen Sie im Azure-Portal eine neue Azure Search-Ressource, und wählen Sie die gewünschte SKU aus.

    ![QnA Maker-Azure Search-Ressource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Stellen Sie die Indizes aus Ihrer ursprünglichen Azure Search-Ressource in der neuen wieder her. Sehen Sie dazu den Beispielcode für die Wiederherstellung aus einer Sicherung [hier](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Nachdem die Daten wiederhergestellt wurden, navigieren Sie zu Ihrer neuen Azure Search-Ressource, wählen Sie **Schlüssel** aus, und notieren Sie den **Namen** und den **Administratorschlüssel**.

    ![Schlüssel von QnA Maker-Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Um die neue Azure Search-Ressource mit dem QnA Maker-Stapel zu verknüpfen, wechseln Sie zum QnA Maker App Service.

    ![QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Wählen Sie **Anwendungseinstellungen** aus, und ersetzen Sie die Felder **AzureSearchName** und **AzureSearchAdminKey** aus Schritt 3.

    ![QnA Maker App Service-Einstellung](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Starten Sie App Service neu.

    ![Neustart des QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden der QnA Maker-API](../Quickstarts/csharp.md)
