---
title: 'Gewusst wie: Skalieren der Azure Time Series Insights-Umgebung | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie Ihre Azure Time Series Insights-Umgebung skalieren. Verwenden Sie das Azure-Portal, um die Kapazität innerhalb einer Preis-SKU zu erhöhen oder zu verringern.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: ee695798dc8a2a19d5cd3d94cbf43e0b58065f84
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556678"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Gewusst wie: Skalieren der Azure Time Series Insights-Umgebung

In diesem Artikel erfahren Sie, wie Sie die Kapazität Ihrer Time Series Insights-Umgebung über das Azure-Portal ändern. Die Kapazität ist der Multiplikator, der auf die Erfassungsrate, Speicherkapazität und Kosten der gewählten SKU angewendet wird. 

Über das Azure-Portal können Sie die Kapazität innerhalb einer bestimmten Preis-SKU erhöhen oder verringern. 

Die Tarif-SKU darf hingegen nicht geändert werden. Eine Umgebung mit der SKU „S1“ kann also beispielsweise nicht in eine S2-Umgebung konvertiert werden (und umgekehrt). 


## <a name="s1-sku-ingress-rates-and-capacities"></a>SKU „S1“: Erfassungsraten und Kapazitäten

| Kapazität der SKU „S1“ | Erfassungsrate | Maximale Speicherkapazität
| --- | --- | --- |
| 1 | 1 GB (1 Mio. Ereignisse) | 30 GB (30 Mio. Ereignisse) pro Monat |
| 10 | 10 GB (10 Mio. Ereignisse) | 300 GB (300 Mio. Ereignisse) pro Monat |

## <a name="s2-sku-ingress-rates-and-capacities"></a>SKU „S2“: Erfassungsraten und Kapazitäten

| Kapazität der SKU „S2“ | Erfassungsrate | Maximale Speicherkapazität
| --- | --- | --- |
| 1 | 10 GB (10 Mio. Ereignisse) | 300 GB (300 Mio. Ereignisse) pro Monat |
| 10 | 100 GB (100 Mio. Ereignisse) | 3 TB (3 Mrd. Ereignisse) pro Monat |

Kapazitäten werden linear skaliert, sodass eine S1-SKU mit der Kapazität „2“ eine Erfassungsrate von 2 GB (2 Mio. Ereignisse) pro Tag und 60 GB (60 Mio. Ereignisse) pro Monat unterstützt.

## <a name="change-the-capacity-of-your-environment"></a>Ändern der Kapazität Ihrer Umgebung
1. Navigieren Sie im Azure-Portal zu Ihrer Time Series Insights-Umgebung, und wählen Sie sie aus. 

2. Wählen Sie im Menü für Ihre Time Series Insights-Umgebung die Option **Konfigurieren**.

   ![configure.png](media/scale-your-environment/configure.png)

3. Passen Sie die Kapazität mithilfe des Schiebereglers **Kapazität** an Ihre Anforderungen in den Bereichen Erfassungsrate und Speicherkapazität an. **Erfassungsrate**, **Speicherkapazität** und **voraussichtliche Kosten** werden dynamisch aktualisiert, um die Auswirkungen der Änderung zu zeigen. 

   ![Schieberegler](media/scale-your-environment/slider.png)

   Alternativ können Sie die Zahl für den Kapazitätsmultiplikator in das Textfeld rechts neben dem Schieberegler eingeben. 

4. Klicken Sie auf **Speichern**, um die Umgebung zu skalieren. Die Statusanzeige wird angezeigt, bis die Änderung committet wurde. 

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Überprüfen, ob die neue Kapazität ausreichend ist, um eine Drosselung zu verhindern](time-series-insights-diagnose-and-solve-problems.md)
