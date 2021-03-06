---
Titel: Erstellen von Webdienst-Endpunkten titleSuffix: Azure Machine Learning Studio Beschreibung: Erstellen von Webdienst-Endpunkten in Azure Machine Learning Jeder Endpunkt im Webdienst wird unabhängig adressiert, gedrosselt und verwaltet.
Dienste: machine-learning ms.service: machine-learning ms.component: studio ms.topic: Artikel

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 04.10.2016
---
# <a name="creating-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>Erstellen von Endpunkten für bereitgestellte Azure Machine Learning Studio-Webdienste
> [!NOTE]
>  In diesem Thema werden für einen **klassischen** Machine Learning-Webdienst geltende Verfahren beschrieben.
> 
> 

Wenn Sie Webdienste erstellen, die Sie an Ihre Kunden weiter verkaufen, müssen Sie jedem Kunden trainierte Modelle zur Verfügung stellen, die weiter mit dem Experiment verknüpft sind, anhand dessen der Webdienst erstellt wurde. Darüber hinaus müssen Aktualisierungen am Experiment selektiv auf einen Endpunkt angewendet werden, ohne die Anpassungen zu überschreiben.

Hierzu ermöglicht Ihnen Azure Machine Learning Studio, mehrere Endpunkte für einen bereitgestellten Webdienst zu erstellen. Jeder Endpunkt im Webdienst wird unabhängig adressiert, gedrosselt und verwaltet. Zu jedem Endpunkt gehört eine eindeutige URL und ein Autorisierungsschlüssel zur Verteilung an Ihre Kunden.



## <a name="adding-endpoints-to-a-web-service"></a>Hinzufügen von Endpunkten zu einem Webdienst
Es gibt zwei Möglichkeiten zum Hinzufügen eines Endpunkts zu einem Webdienst.

* Programmgesteuert
* Über das Azure Machine Learning Web Services-Portal

Nach dem Erstellen kann der Endpunkt über synchrone APIs, Batch-APIs und Excel-Arbeitsblätter genutzt werden. Zusätzlich zum Hinzufügen von Endpunkten über diese Benutzeroberfläche können Sie auch die Endpunktverwaltungs-APIs verwenden, um Endpunkte programmgesteuert hinzuzufügen.

> [!NOTE]
> Wenn Sie dem Webdienst zusätzliche Endpunkte hinzugefügt haben, können Sie den Standardendpunkt nicht löschen.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Programmgesteuertes Hinzufügen eines Endpunkts
Sie können Ihrem Webdienst einen Endpunkt mithilfe des Beispielcodes [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) programmgesteuert hinzufügen.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Hinzufügen eines Endpunkts über das Azure Machine Learning Web Services-Portal
1. Klicken Sie in Machine Learning Studio links auf „Webdienste“.
2. Klicken Sie unten im Dashboard des Webdiensts auf **Manage endpoints**. Das Azure Machine Learning Web Services-Portal wird mit der Seite mit den Endpunkten für den Webdienst geöffnet.
3. Klicken Sie auf **New**.
4. Geben Sie einen Namen und eine Beschreibung für den neuen Endpunkt ein. Endpunktnamen dürfen maximal 24 Zeichen lang sein und müssen aus Kleinbuchstaben oder Zahlen bestehen. Wählen Sie die Protokollierungsstufe aus, und legen Sie fest, ob Beispieldaten aktiviert sind. Weitere Informationen zur Protokollierung finden Sie unter [Aktivieren der Protokollierung für Machine Learning-Webdienste](web-services-logging.md).

## <a name="next-steps"></a>Nächste Schritte
[Nutzen eines Azure Machine Learning-Webdiensts](consume-web-services.md)

