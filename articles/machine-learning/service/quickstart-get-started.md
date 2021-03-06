---
title: Schnellstart über das Azure-Portal
titleSuffix: Azure Machine Learning service
description: Erste Schritte mit dem Azure Machine Learning Service. Verwenden Sie das Azure-Portal, um einen Arbeitsbereich zu erstellen. Dieser bildet in der Cloud die Grundlage zum Experimentieren, Trainieren und Bereitstellen von Machine Learning-Modellen.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 14c500d77cc0e67aaade5e6be490f599f39bfad5
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807719"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Schnellstart: Verwenden des Azure-Portals zum Ausführen der ersten Schritte mit Azure Machine Learning

In dieser Schnellstartanleitung verwenden Sie das Azure-Portal, um einen Azure Machine Learning-Arbeitsbereich zu erstellen. Dieser Arbeitsbereich bildet die Grundlage in der Cloud zum Experimentieren, Trainieren und Bereitstellen von Machine Learning-Modellen mit Machine Learning. In dieser Schnellstartanleitung werden Cloudressourcen genutzt, und es ist keine Installation erforderlich. Wie Sie stattdessen Ihren eigenen Jupyter Notebook-Server konfigurieren, erfahren Sie unter [Schnellstart: Verwenden von Python für die ersten Schritte mit Azure Machine Learning](quickstart-create-workspace-with-python.md).  
 
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]

Diese Schnellstartanleitung umfasst folgende Aktionen:

* Erstellen eines Arbeitsbereichs in Ihrem Azure-Abonnement.
* Testen mit Python in einem Azure-Notebook und Protokollieren der Werte in den unterschiedlichen Iterationen.
* Anzeigen der protokollierten Werte in Ihrem Arbeitsbereich

Folgende Azure-Ressourcen werden Ihrem Arbeitsbereich automatisch hinzugefügt, sofern sie regional verfügbar sind:

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Azure Storage (in englischer Sprache)](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Die von Ihnen erstellten Ressourcen können auch in anderen Tutorials und Anleitungen für den Machine Learning-Dienst verwendet werden. Genau wie bei anderen Azure-Diensten gelten auch für bestimmte Machine Learning-Ressourcen gewisse Grenzwerte. Ein Beispiel wäre etwa die Größe des Computeclusters. Erfahren Sie mehr über die [Standardgrenzwerte und das Erhöhen Ihres Kontingents](how-to-manage-quotas.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version des Azure Machine Learning Service](http://aka.ms/AMLFree) aus.


## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Klicken Sie auf der Seite des Arbeitsbereichs auf `Explore your Azure Machine Learning service Workspace`.

 ![Erkunden des Arbeitsbereichs](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Verwenden des Arbeitsbereichs

Sehen Sie sich nun an, wie ein Arbeitsbereich beim Verwalten Ihrer Machine Learning-Skripts hilft. In diesem Abschnitt führen Sie die folgenden Schritte aus:

* Öffnen eines Notebooks in Azure Notebooks
* Ausführen von Code zum Generieren von protokollierten Werten
* Anzeigen der protokollierten Werte in Ihrem Arbeitsbereich

Dieses Beispiel zeigt, wie der Arbeitsbereich Sie bei der Nachverfolgung der in einem Skript generierten Informationen unterstützt. 

### <a name="open-a-notebook"></a>Öffnen eines Notebooks 

Azure Notebooks bietet eine kostenlose Cloudplattform für Jupyter Notebooks, die mit allem vorkonfiguriert ist, was Sie für die Ausführung von Machine Learning benötigen.  

Klicken Sie auf `Open Azure Notebooks`, um Ihr erstes Experiment durchzuführen.

 ![Öffnen von Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

Unter Umständen müssen Sie in Ihrer Organisation zunächst die [Zustimmung des Administrators](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) einholen, um sich anmelden zu können.

Sie melden sich bei Azure Notebooks mit demselben Konto an, mit dem Sie sich auch beim Azure-Portal angemeldet haben.  Nach Ihrer Anmeldung wird eine neue Registerkarte geöffnet und die Eingabeaufforderung `Clone Library` angezeigt. Wählen Sie `Clone` aus.


### <a name="run-the-notebook"></a>Ausführen des Notebooks

Neben zwei Notebooks sehen Sie die Datei `config.json`. Diese Konfigurationsdatei enthält Informationen zu dem Arbeitsbereich, den Sie erstellt haben.  

Klicken Sie auf `01.run-experiment.ipynb`, um das Notebook zu öffnen.

Führen Sie die Zellen nacheinander aus (UMSCHALT+EINGABE). Sie können aber auch auf `Cells` > `Run All` klicken, um das gesamte Notebook auszuführen. Wenn neben einer Zelle ein Sternchen (__*__) angezeigt wird, wird sie gerade ausgeführt. Nach Abschluss der Codeausführung für die Zelle wird eine Zahl angezeigt. 

Nachdem Sie die Ausführung aller Zellen im Notebook abgeschlossen haben, können Sie die protokollierten Werte in Ihrem Arbeitsbereich anzeigen.

## <a name="view-logged-values"></a>Anzeigen protokollierter Werte

Kehren Sie nach der Ausführung aller Zellen des Notebooks zur Portalseite zurück.  

Wählen Sie `View Experiments` aus.

![Anzeigen von Experimenten](./media/quickstart-get-started/view_exp.png)

Schließen Sie das Popupelement `Reports`.

Wählen Sie `my-first-experiment` aus.

Sehen Sie sich die Informationen zu der Ausführung, die Sie gerade durchgeführt haben, an. Scrollen Sie auf der Seite nach unten, um zur Tabelle mit den Ausführungen zu gelangen. Klicken Sie auf die als Link dargestellte Ausführungsnummer.

 ![Link für den Ausführungsverlauf](./media/quickstart-get-started/report.png)

Daraufhin werden Plots angezeigt, die automatisch aus den protokollierten Werten erstellt wurden. Wenn Sie mehrere Werte mit dem gleichen Namensparameter protokollieren, wird für Sie automatisch ein Plot generiert.

   ![Anzeigen des Verlaufs](./media/quickstart-get-started/plots.png)

Da der Code für die Pi-Annäherung willkürliche Werte verwendet, enthalten Ihre Plots andere Werte.  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Sie können die Ressourcengruppe auch behalten und einen einzelnen Arbeitsbereich löschen. Zeigen Sie die Eigenschaften des Arbeitsbereichs an, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Sie haben die Ressourcen erstellt, die zum Experimentieren und zum Bereitstellen von Modellen benötigt werden. Sie haben Code in einem Notebook ausgeführt. Und Sie haben den Ausführungsverlauf dieses Codes in Ihrem Arbeitsbereich in der Cloud untersucht.

Führen Sie Machine Learning-Tutorials zum Trainieren und Bereitstellen eines Modells aus, um sich ausführlicher mit dem Workflow zu beschäftigen:  

> [!div class="nextstepaction"]
> [Tutorial: Trainieren eines Bildklassifizierungsmodells](tutorial-train-models-with-aml.md)
