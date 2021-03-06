---
title: Erstellen einer geografisch verteilten App-Lösung mit Azure und Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine geografisch verteilte App-Lösung mit Azure und Azure Stack erstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: f1151c845797d74bbb9a5e50feeeb288a4ab349b
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714847"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>Tutorial: Erstellen einer geografisch verteilten App-Lösung mit Azure und Azure Stack

*Gilt für: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Es wird beschrieben, wie Sie Datenverkehr basierend auf unterschiedlichen Metriken an bestimmte Endpunkte weiterleiten, indem Sie das Muster für geografisch verteilte Apps verwenden. Durch die Erstellung eines Traffic Manager-Profils mit Weiterleitung und Endpunktkonfiguration anhand der Geografie wird sichergestellt, dass Informationen basierend auf regionalen Anforderungen, unternehmensinternen und internationalen Bestimmungen und Ihren Datenanforderungen an Endpunkte weitergeleitet werden.

In diesem Tutorial erstellen Sie eine Beispielumgebung, die Folgendes ermöglicht:

> [!div class="checklist"]
> - Erstellen einer geografisch verteilten App
> - Verwenden von Traffic Manager für Ihre App

## <a name="use-the-geo-distributed-apps-pattern"></a>Verwenden des Musters für geografisch verteilte Apps

Mit dem Muster für die geografische Verteilung kann Ihre App regionsübergreifend eingesetzt werden. Sie können als Standard die öffentliche Cloud verwenden, aber für einige Benutzer kann es erforderlich sein, dass die Daten in ihrer Region verbleiben. Je nach den Anforderungen können Sie Benutzer an die am besten geeignete Cloud weiterleiten.

### <a name="issues-and-considerations"></a>Probleme und Überlegungen

#### <a name="scalability-considerations"></a>Überlegungen zur Skalierbarkeit

In die Lösung, die Sie in diesem Tutorial erstellen, wird die Skalierbarkeit nicht einbezogen. Wenn Sie sie in Kombination mit anderen Azure- und lokalen Technologien verwenden, ist es aber möglich, dass Sie die Anforderungen an die Skalierbarkeit erfüllen. Informationen zur Erstellung einer Hybridlösung mit automatischer Skalierung per Traffic Manager finden Sie unter [Erstellen von cloudübergreifenden Skalierungslösungen mit Azure](azure-stack-solution-cloud-burst.md).

#### <a name="availability-considerations"></a>Überlegungen zur Verfügbarkeit

Wie bei den Skalierbarkeitsaspekten auch, geht es bei dieser Lösung nicht direkt um die Verfügbarkeit. Ebenso wie bei der Skalierbarkeit gilt aber auch hier, dass Azure-Technologien und lokale Technologien und Lösungen in dieser Lösung implementiert werden können, um für alle beteiligten Komponenten die Hochverfügbarkeit sicherzustellen.

### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters

- Ihre Organisation verfügt über internationale Zweigniederlassungen, für die benutzerdefinierte regionale Sicherheits- und Verteilungsrichtlinien erforderlich sind.

- Alle Niederlassungen Ihrer Organisation rufen per Pullvorgang Daten zu Mitarbeitern, Geschäft und Einrichtungen ab, sodass Berichterstellungsaktivitäten gemäß den lokalen Bestimmungen und Zeitzonen benötigt werden.

- Anforderungen für eine hohe Skalierbarkeit können durch das horizontale Hochskalieren von Apps erfüllt werden, wobei mehrere App-Bereitstellungen innerhalb einer Region sowie regionsübergreifend erfolgen, um extreme Lastanforderungen zu verarbeiten.

### <a name="planning-the-topology"></a>Planen der Topologie

Es ist hilfreich, wenn Sie über das folgende Wissen verfügen, bevor Sie den Speicherbedarf für eine verteilte App erstellen:

-   **Benutzerdefinierte Domäne für die App:** Wie lautet der Name der benutzerdefinierten Domäne, mit dem Kunden auf die App zugreifen? Für die Beispiel-App lautet der Name der benutzerdefinierten Domäne *www.scalableasedemo.com*.

-   **Traffic Manager-Domäne:** Ein Domänenname muss ausgewählt werden, wenn Sie ein [Azure Traffic Manager-Profil](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles) erstellen. Dieser Name wird mit dem Suffix *trafficmanager.net* kombiniert, um einen Domäneneintrag zu registrieren, der von Traffic Manager verwaltet wird. Für die Beispiel-App ist der ausgewählte Name *scalable-ase-demo*. Der vollständige Domänenname, der von Traffic Manager verwaltet wird, lautet also *scalable-ase-demo.trafficmanager.net*.

-   **Strategie für die Skalierung der App:** Wird die Anwendung über mehrere App Service-Umgebungen in einer Region verteilt? Über mehrere Regionen? Wird eine Kombination beider Ansätze verwendet? Die Entscheidung sollte darauf basieren, woher der Kundendatenverkehr erwartet wird, aber auch darauf, wie gut der Rest der Back-End-Infrastruktur zur Unterstützung einer App skaliert werden kann. Bei einer zu 100 % statusfreien Anwendung kann eine App beispielsweise hochgradig skaliert werden, indem eine Kombination von mehreren App Service-Umgebungen pro Azure-Region verwendet und dies mit App Service-Umgebungen, die in mehreren Azure-Regionen bereitgestellt sind, multipliziert wird. Da Kunden aus mehr als 15 globalen Azure-Regionen auswählen können, ist wirklich die Erstellung einer weltweiten, hoch skalierbaren Anwendung möglich. Für die Beispiel-App in diesem Artikel wurden drei App Service-Umgebungen in einer einzelnen Azure-Region (USA (Mitte/Süden)) erstellt.

-   **Benennungskonvention für die App Service-Umgebungen:** Jede App Service-Umgebung muss über einen eindeutigen Namen verfügen. Bei mehr als ein oder zwei App Service-Umgebungen ist es hilfreich, über eine Benennungskonvention zu verfügen, um die Identifizierung der einzelnen App Service-Umgebungen zu vereinfachen. Für die Beispiel-App wurde eine einfache Benennungskonvention verwendet. Die Namen der drei App Service-Umgebungen sind *fe1ase*, *fe2ase* und *fe3ase*.

-   **Benennungskonvention für die Apps:** Da mehrere Instanzen der App bereitgestellt werden, ist ein Name für jede Instanz der bereitgestellten App erforderlich. Bei App Service-Umgebungen kann der gleiche App-Name über mehrere App Service-Umgebungen hinweg genutzt werden. Da jede App Service-Umgebung ein eindeutiges Domänensuffix aufweist, können Entwickler den gleichen App-Namen in jeder Umgebung wiederverwenden. Ein Entwickler kann Apps beispielsweise wie folgt benennen: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net* usw. Für die App in diesem Szenario hat jede App-Instanz einen eindeutigen Namen. Die verwendeten Namen für die App-Instanzen sind *webfrontend1*, *webfrontend2*, und *webfrontend3*.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack ist eine Erweiterung von Azure. Mit Azure Stack holen Sie sich die Agilität und Innovation von Cloud Computing in Ihre lokale Umgebung. Sie erhalten die einzige Hybrid Cloud, mit der Sie Hybrid-Apps überall entwickeln und bereitstellen können.  
> 
> Im Whitepaper [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Überlegungen zum Entwurf für Hybridanwendungen) werden die wichtigen Aspekte in Bezug auf die Softwarequalität (Platzierung, Skalierbarkeit, Verfügbarkeit, Resilienz, Verwaltbarkeit und Sicherheit) beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind. Die Überlegungen zum Entwurf dienen als Hilfe beim Optimieren des Designs von Hybridanwendungen, um für Produktionsumgebungen das Auftreten von Problemen zu minimieren.

## <a name="part-1-create-a-geo-distributed-app"></a>Teil 1: Erstellen einer geografisch verteilten App

In diesem Teil erstellen Sie eine Web-App.

> [!div class="checklist"]
> - Erstellen von Web-Apps und Durchführen der Veröffentlichung
> - Hinzufügen von Code zu Azure Repos
> - Verweisen Sie für den App-Build auf mehrere Cloudziele.
> - Verwalten und Konfigurieren des CD-Prozesses

### <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Abonnement und eine Azure Stack-Installation sind erforderlich.

### <a name="geo-distributed-app-steps"></a>Geografisch verteilte App – Schritte

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Abrufen einer benutzerdefinierten Domäne und Konfigurieren des DNS

Aktualisieren Sie die DNS-Zonendatei für die Erstellung von Web-Apps, und veröffentlichen Sie die Domäne. Azure AD kann dann die Eigentümerschaft für den Namen der benutzerdefinierten Domäne überprüfen. Verwenden Sie [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) für Azure-/Office 365-/externe DNS-Einträge in Azure, oder Sie fügen den DNS-Eintrag bei einer [anderen DNS-Registrierungsstelle](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/) hinzu.

1. Registrieren Sie eine benutzerdefinierte Domäne bei einer öffentlichen Registrierungsstelle.

2. Melden Sie sich an der Domänennamen-Registrierungsstelle für die Domäne an. Unter Umständen ist ein genehmigter Administrator erforderlich, um die DNS-Updates durchzuführen.

3. Aktualisieren Sie die DNS-Zonendatei für die Domäne, indem Sie den DNS-Eintrag hinzufügen, der von Azure AD bereitgestellt wurde. Der DNS-Eintrag nimmt keine Änderungen in Bezug auf das Verhalten vor, z.B. E-Mail-Routing oder Webhosting.

### <a name="create-web-apps-and-publish"></a>Erstellen von Web-Apps und Durchführen der Veröffentlichung

Richten Sie die hybride CI/CD-Pipeline ein, um die Web-App in Azure und Azure Stack bereitzustellen und Änderungen automatisch an beide Clouds zu pushen.

> [!Note]  
> Azure Stack mit den passenden syndizierten Images für die Ausführung (Windows Server und SQL) und eine App Service-Bereitstellung sind erforderlich. Lesen Sie in der App Service-Dokumentation den Abschnitt [Vor den ersten Schritten mit App Service in Azure Stack](/articles/azure-stack/azure-stack-app-service-before-you-get-started) für den Azure Stack-Bediener.

#### <a name="add-code-to-azure-repos"></a>Hinzufügen von Code zu Azure Repos

1. Melden Sie sich bei Visual Studio mit einem **Konto an, das über Berechtigungen zum Erstellen von Projekten** in Azure Repos verfügt.

    Eine hybride Pipeline für Continuous Integration/Continuous Delivery (CI/CD) kann sowohl für Anwendungscode als auch für Infrastrukturcode gelten. Verwenden Sie [Azure Resource Manager-Vorlagen](https://azure.microsoft.com/resources/templates/) für die Entwicklung von privaten und gehosteten Clouds.

    ![Alt text](media/azure-stack-solution-geo-distributed/image1.JPG)

2. **Klonen Sie das Repository**, indem Sie die Standard-Web-App erstellen und öffnen.

    ![Alt text](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Erstellen der Web-App-Bereitstellung in beiden Clouds

1.  Bearbeiten Sie die Datei **WebApplication.csproj**: Wählen Sie Runtimeidentifier, und fügen Sie **win10-x64** hinzu. (Weitere Informationen finden Sie in der Dokumentation zur [eigenständigen Bereitstellung](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd).)

    ![Alt text](media/azure-stack-solution-geo-distributed/image3.png)

1.  **Checken Sie den Code in Azure Repos ein**, indem Sie Team Explorer verwenden.

2.  Vergewissern Sie sich, dass der **Anwendungscode** in Azure Repos eingecheckt wurde.

### <a name="create-the-build-definition"></a>Erstellen der Builddefinition

1. **Melden Sie sich bei Azure Pipelines an**, um sich zu vergewissern, dass Builddefinitionen erstellt werden können.

2. Fügen Sie den Code **-r win10-x64** hinzu. Dies ist erforderlich, um eine eigenständige Bereitstellung mit .NET Core auszulösen.

    ![Alt text](media/azure-stack-solution-geo-distributed/image4.png)

3. **Führen Sie den Buildvorgang durch.** Der Buildvorgang für die [eigenständige Bereitstellung](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) veröffentlicht Artefakte, die in Azure und Azure Stack ausgeführt werden können.

**Verwenden eines gehosteten Azure-Agents**

Mit einem gehosteten Agent lassen sich in Azure Pipelines komfortabel Web-Apps erstellen und bereitstellen. Wartungsarbeiten und Upgrades werden automatisch von Microsoft Azure durchgeführt, was kontinuierliche und ungestörte Entwicklungs-, Test- und Bereitstellungsaktivitäten ermöglicht.

### <a name="manage-and-configure-the-cd-process"></a>Verwalten und Konfigurieren des CD-Prozesses

Azure DevOps und Azure DevOps Server bieten eine äußerst flexibel konfigurier- und verwaltbare Pipeline für Releases in mehreren Umgebungen (z.B. in Entwicklungs-, Staging-, Qualitätssicherungs- und Produktionsumgebungen) – einschließlich erforderlicher Genehmigungen in bestimmten Phasen.

#### <a name="create-release-definition"></a>Erstellen einer Releasedefinition


![Alt text](media/azure-stack-solution-geo-distributed/image5.png)

1.  Wählen Sie die Schaltfläche mit dem **Pluszeichen**, um in Visual Studio Online (VSO) auf der Seite „Build und Release“ über die **Registerkarte „Releases“** ein neues Release hinzuzufügen.

    ![Alt text](media/azure-stack-solution-geo-distributed/image6.png)

2. Wenden Sie die Vorlage **Azure App Service-Bereitstellung** an.

    ![Alt text](media/azure-stack-solution-geo-distributed/image7.png)

3. Fügen Sie im Pulldownmenü **Artefakt hinzufügen** das Artefakt für die Azure Cloud-Build-App hinzu.

    ![Alt text](media/azure-stack-solution-geo-distributed/image8.png)

4. Klicken Sie auf der Registerkarte „Pipeline“ auf den Link **Phase, Aufgabe** der Umgebung, und legen Sie die Azure Cloud-Umgebungswerte fest.

    ![Alt text](media/azure-stack-solution-geo-distributed/image9.png)

5. Legen Sie unter **Umgebungsname** den Umgebungsnamen fest, und wählen Sie das **Azure-Abonnement** für den Azure Cloud-Endpunkt aus.

    ![Alt text](media/azure-stack-solution-geo-distributed/image10.png)

6. Legen Sie unter **Umgebungsname** den erforderlichen Namen des Azure-App-Diensts fest.

    ![Alt text](media/azure-stack-solution-geo-distributed/image11.png)

7. Geben Sie unter „Agent-Warteschlange“ für die gehostete Azure Cloud-Umgebung die Zeichenfolge **Hosted VS2017** ein.

    ![Alt text](media/azure-stack-solution-geo-distributed/image12.png)

8. Wählen Sie im Menü „Deploy Azure App Service“ (Azure App Service bereitstellen) **das gültige Paket oder den gültigen Ordner** für die Umgebung aus. Klicken Sie für den **Ordnerspeicherort** auf „OK“.

    ![Alt text](media/azure-stack-solution-geo-distributed/image13.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image14.png)

9. Speichern Sie alle Änderungen, und kehren Sie zur **Releasepipeline** zurück.

    ![Alt text](media/azure-stack-solution-geo-distributed/image15.png)

10. Fügen Sie ein **neues Artefakt** hinzu, und wählen Sie dabei den Build für die Azure Stack-App aus.

    ![Alt text](media/azure-stack-solution-geo-distributed/image16.png)

11. Fügen Sie mindestens eine Umgebung hinzu, und wenden Sie dabei die **Azure App Service-Bereitstellung** an.

    ![Alt text](media/azure-stack-solution-geo-distributed/image17.png)

12. Nennen Sie die neue Umgebung **Azure Stack**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image18.png)

13. Suchen Sie auf der Registerkarte **Aufgabe** nach der Umgebung „Azure Stack“.

    ![Alt text](media/azure-stack-solution-geo-distributed/image19.png)

14. Wählen Sie das **Abonnement** für den Azure Stack-Endpunkt aus.

  ![Alt text](media/azure-stack-solution-geo-distributed/image20.png)

15. Legen Sie den Namen der Azure Stack-Web-App als **Name des App-Diensts** fest.

    ![Alt text](media/azure-stack-solution-geo-distributed/image21.png)

16. Wählen Sie den **Azure Stack-Agent** aus.

    ![Alt text](media/azure-stack-solution-geo-distributed/image22.png)

17. Wählen Sie im Abschnitt „Deploy Azure App Service“ (Azure App Service bereitstellen) **das gültige Paket oder den gültigen Ordner** für die Umgebung aus. Klicken Sie für den **Ordnerspeicherort** auf „OK“.

    ![Alt text](media/azure-stack-solution-geo-distributed/image23.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image24.png)

18. Fügen Sie auf der Registerkarte **Variable** eine Variable mit dem Namen `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` hinzu, und legen Sie ihren Wert auf `true` und den Bereich auf `Azure Stack` fest.

    ![Alt text](media/azure-stack-solution-geo-distributed/image25.png)

19. Klicken Sie bei beiden Artefakten auf das Symbol **Continuous Deployment-Trigger**, und aktivieren Sie den Trigger für **Continuous Deployment**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image26.png)

20. Klicken Sie für die Azure Stack-Umgebung auf das Symbol **Bedingungen vor der Bereitstellung**, und legen Sie den Trigger auf **Nach einem Release** fest.

21. Speichern Sie alle Änderungen.

> [!Note]  
>  Bei der vorlagenbasierten Erstellung einer Releasedefinition wurden einige Einstellungen für die Aufgaben möglicherweise automatisch als [Umgebungsvariablen](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) definiert. Diese Einstellungen können in den Aufgabeneinstellungen nicht geändert werden. Zum Bearbeiten dieser Einstellungen müssen Sie das übergeordnete Umgebungselement auswählen.

## <a name="part-2-update-web-app-options"></a>Teil 2: Aktualisieren der Web-App-Optionen

Von [Azure App Service](https://docs.microsoft.com/azure/app-service/overview) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt. 

![Alt text](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure-Web-Apps
> - Verwenden Sie einen CNAME-Eintrag oder einen **A-Eintrag**, um App Service einen benutzerdefinierten DNS-Namen zuzuordnen.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure-Web-Apps

> [!Note]  
>  Verwenden Sie einen CNAME-Eintrag für alle benutzerdefinierten DNS-Namen – außer für Stammdomänen (z.B. northwind.com).

Informationen zum Migrieren einer Livewebsite und ihres DNS-Domänennamens zu App Service finden Sie unter [Migrieren einer aktiven benutzerdefinierten Domäne zu Azure App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain).

### <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

-   [Erstellen Sie eine App Service-App](https://docs.microsoft.com/azure/app-service/), oder verwenden Sie eine App, die für ein anderes Tutorial erstellt wurde.

-   Erwerben Sie einen Domänennamen, und stellen Sie sicher, dass der Zugriff auf die DNS-Registrierung für den Domänenanbieter möglich ist.

Aktualisieren Sie die DNS-Zonendatei für die Domäne. Azure AD überprüft die Eigentümerschaft für den Namen der benutzerdefinierten Domäne. Verwenden Sie [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) für Azure-/Office 365-/externe DNS-Einträge in Azure, oder fügen Sie den DNS-Eintrag bei einer [anderen DNS-Registrierungsstelle](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/) hinzu.

-   Registrieren Sie eine benutzerdefinierte Domäne bei einer öffentlichen Registrierungsstelle.

-   Melden Sie sich an der Domänennamen-Registrierungsstelle für die Domäne an. (Unter Umständen ist ein genehmigter Administrator erforderlich, um die DNS-Updates durchzuführen.)

-   Aktualisieren Sie die DNS-Zonendatei für die Domäne, indem Sie den DNS-Eintrag hinzufügen, der von Azure AD bereitgestellt wurde.

Konfigurieren Sie beispielsweise die DNS-Einstellungen für die Stammdomäne „northwindcloud.com“, um DNS-Einträge für „northwindcloud.com“ und „www.northwindcloud.com“ hinzuzufügen.

> [!Note]  
>  Ein Domänenname kann über das [Azure-Portal](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain) erworben werden.  
> Um einer Web-App einen benutzerdefinierten DNS-Namen zuzuordnen, muss der [App Service-Plan](https://azure.microsoft.com/pricing/details/app-service/) der Web-App einen kostenpflichtigen Tarif (**Shared**, **Basic**, **Standard** oder **Premium**) aufweisen.



### <a name="create-and-map-cname-and-a-records"></a>Erstellen und Zuordnen von CNAME- und A-Einträgen

#### <a name="access-dns-records-with-domain-provider"></a>Zugreifen auf DNS-Einträge mit Domänenanbieter

> [!Note]  
>  Verwenden Sie Azure DNS, um einen benutzerdefinierten DNS-Namen für Azure-Web-Apps zu konfigurieren. Weitere Informationen finden Sie unter [Bereitstellen von benutzerdefinierten Domäneneinstellungen für einen Azure-Dienst mit Azure DNS](https://docs.microsoft.com/azure/dns/dns-custom-domain).

1.  Melden Sie sich an der Website des Hauptanbieters an.

2.  Suchen Sie die Seite für die Verwaltung von DNS-Einträgen. Jeder Domänenanbieter verfügt über eine eigene Oberfläche für DNS-Einträge. Suchen Sie nach Bereichen der Website, die mit **Domänenname**, **DNS** oder **Namenserververwaltung** gekennzeichnet sind.

Die Seite mit den DNS-Einträgen kann unter **Eigene Domänen** angezeigt werden. Suchen Sie nach dem Link mit dem Namen **Zone file** (Zonendatei), **DNS Records** (DNS-Einträge) oder **Advanced configuration** (Erweiterte Konfiguration).

Der folgende Screenshot zeigt ein Beispiel für eine Seite mit DNS-Einträgen:

![Beispielseite mit DNS-Einträgen](media/azure-stack-solution-geo-distributed/image28.png)

1.  Wählen Sie unter der Domänennamen-Registrierungsstelle die Option **Add or Create** (Hinzufügen oder erstellen), um einen Eintrag zu erstellen. Einige Anbieter verfügen über unterschiedliche Links, um unterschiedliche Arten von Einträgen hinzuzufügen. Informationen hierzu finden Sie in der Dokumentation des Anbieters.

2.  Fügen Sie einen CNAME-Eintrag hinzu, um dem Standardhostnamen der App eine Unterdomäne zuzuordnen.

  Fügen Sie für das Beispiel mit der Domäne „www.northwindcloud.com“ einen CNAME-Eintrag hinzu, mit dem der Name „www“ der URL „<app\_name>.azurewebsites.net“ zugeordnet wird.

Nach dem Hinzufügen des CNAME-Eintrags sieht die Seite mit den DNS-Einträgen wie im folgenden Beispiel aus:

![Portalnavigation zur Azure-App](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Aktivieren der Zuordnung von CNAME-Einträgen in Azure

1.  Melden Sie sich auf einer neuen Registerkarte am Azure-Portal an.

2.  Navigieren Sie zu App Services.

3.  Wählen Sie die Web-App aus.

4.  Wählen Sie im linken Navigationsbereich der App-Seite im Azure-Portal die Option **Benutzerdefinierte Domänen**.

5.  Wählen Sie das **+**-Symbol neben der Option **Hostnamen hinzufügen**.

1.  Geben Sie den vollqualifizierten Domänennamen ein, z.B. `www.northwindcloud.com`.

2.  Wählen Sie **Überprüfen**.

3.  Fügen Sie, falls angegeben, den DNS-Einträgen der Domänennamen-Registrierungsstelle weitere Einträge mit anderen Typen (`A` oder `TXT`) hinzu. Azure stellt die Werte und Typen dieser Einträge bereit:

    a.  Ein **A**-Eintrag, um die IP-Adresse der App zuzuordnen.

    b.  Einen **TXT**-Eintrag, der dem Standardhostnamen (<App-Name>.azurewebsites.net) der App zugeordnet wird. App Service nutzt diesen Eintrag nur während der Konfiguration, um die Eigentümerschaft der benutzerdefinierten Domäne zu überprüfen. Löschen Sie den TXT-Eintrag, nachdem die Überprüfung abgeschlossen ist.

4.  Schließen Sie diese Aufgabe auf der Registerkarte für die Domänenregistrierungsstelle ab, und führen Sie die Überprüfung erneut durch, bis die Schaltfläche **Hostnamen hinzufügen** aktiviert wird.

5.  Stellen Sie sicher, dass der Typ des Hostnamenseintrags auf **CNAME (www.beispiel.com oder eine beliebige Unterdomäne)** festgelegt ist.

6.  Wählen Sie **Hostnamen hinzufügen**.

7.  Geben Sie den vollqualifizierten Domänennamen ein, z.B. `northwindcloud.com`.

8.  Wählen Sie **Überprüfen**.

9.  Die Schaltfläche **Hinzufügen** wird aktiviert.

10. Stellen Sie sicher, dass die Option für den Typ des Hostnamenseintrags auf **A-Datensatz (beispiel.com)** festgelegt ist.

11. Wählen Sie **Hostnamen hinzufügen**.

  Unter Umständen dauert es eine Weile, bis die neuen Hostnamen auf der Seite **Benutzerdefinierte Domänen** der App angezeigt werden. Aktualisieren Sie den Browser, um die Daten zu aktualisieren.
  
  ![Alt text](media/azure-stack-solution-geo-distributed/image31.png) 
  
  Bei einem Fehler wird unten auf der Seite eine Benachrichtigung mit einem Überprüfungsfehler angezeigt. ![Überprüfungsfehler](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  Sie können die obigen Schritte wiederholen, um eine Platzhalterdomäne zuzuordnen (\*.northwindcloud.com). Dies ermöglicht das Hinzufügen von weiteren Unterdomänen zu diesem App Service, ohne dass jeweils ein separater CNAME-Eintrag erstellt werden muss. Befolgen Sie die Anleitung der Registrierungsstelle, um diese Einstellung zu konfigurieren.

#### <a name="test-in-a-browser"></a>Testen in einem Browser

Navigieren Sie zu dem bzw. den oben konfigurierten DNS-Namen (z.B. `northwindcloud.com`, www.northwindcloud.com).

## <a name="part-3-bind-a-custom-ssl-cert"></a>Teil 3: Binden eines benutzerdefinierten SSL-Zertifikats

In diesem Teil führen Sie Folgendes durch:

> [!div class="checklist"]
> - Binden des benutzerdefinierten SSL-Zertifikats an App Service
> - Erzwingen von HTTPS für die App
> - Automatisieren der SSL-Zertifikatbindung mit Skripts

> [!Note]  
> Rufen Sie, falls erforderlich, im Azure-Portal ein SSL-Kundenzertifikat ab, und binden Sie es an die Web-App. Absolvieren Sie das [Tutorial zu App Service-Zertifikaten](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

-   [Erstellen einer App Service-App](https://docs.microsoft.com/azure/app-service/)
-   [Zuordnen eines benutzerdefinierten DNS-Namens zu Ihrer Web-App](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Beschaffen eines SSL-Zertifikats von einer vertrauenswürdigen Zertifizierungsstelle und Verwenden des Schlüssels zum Signieren der Anforderung

### <a name="requirements-for-your-ssl-certificate"></a>Anforderungen an Ihr SSL-Zertifikat

Das Zertifikat muss sämtliche der folgenden Anforderungen erfüllen, damit Ihr Zertifikat in App Service verwendet werden kann:

-   Von einer vertrauenswürdigen Zertifizierungsstelle signiert

-   Als kennwortgeschützte PFX-Datei exportiert

-   Enthält einen privaten Schlüssel mit mindestens 2048 Bit

-   Enthält alle Zwischenzertifikate in der Zertifikatkette

> [!Note]  
>  **ECC-Zertifikate (Elliptic Curve Cryptography, Kryptografie für elliptische Kurven)** funktionieren mit App Service, werden in diesem Leitfaden aber nicht behandelt. Wenden Sie sich an eine Zertifizierungsstelle, um Hilfe beim Erstellen von ECC-Zertifikaten zu erhalten. 

#### <a name="prepare-the-web-app"></a>Vorbereiten der Web-App

Um ein benutzerdefiniertes SSL-Zertifikat an die Web-App zu binden, muss der [App Service-Plan](https://azure.microsoft.com/pricing/details/app-service/) den Tarif **Basic**, **Standard** oder **Premium** aufweisen.

#### <a name="sign-in-to-azure"></a>Anmelden bei Azure

1.  Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zur Web-App.

2.  Wählen Sie im linken Menü **App Services** und anschließend den Namen der Web-App aus.

![Auswählen der Web-App](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>Überprüfen des Tarifs

1.  Scrollen Sie im linken Navigationsbereich auf der Seite mit der Web-App zum Abschnitt **Einstellungen**, und wählen Sie **Zentral hochskalieren (App Service-Plan)**.

    ![Menü „Zentral hochskalieren“](media/azure-stack-solution-geo-distributed/image34.png)

1.  Stellen Sie sicher, dass für die Web-App nicht der Tarif **Free** oder **Shared** festgelegt ist. Der aktuelle Tarif der Web-App ist mit einem dunkelblauen Rahmen hervorgehoben.

    ![Überprüfen des Tarifs](media/azure-stack-solution-geo-distributed/image35.png)

Benutzerdefiniertes SSL wird in dem Tarif **Free** oder **Shared** nicht unterstützt. Führen Sie die Schritte im nächsten Abschnitt aus, um einen höheren Tarif auszuwählen, oder verwenden Sie die Seite **Tarif wählen**, und springen Sie zu [Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure-Web-Apps](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Zentrales Hochskalieren Ihres App Service-Plans

1.  Wählen Sie den Tarif **Basic**, **Standard** oder **Premium** aus.

2.  Wählen Sie **Auswählen**.

![Auswählen eines Tarifs](media/azure-stack-solution-geo-distributed/image36.png)

Der Skalierungsvorgang ist abgeschlossen, wenn die Benachrichtigung angezeigt wird.

![Benachrichtigung zum Hochskalieren](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Binden Ihres SSL-Zertifikats und Zusammenführen von Zwischenzertifikaten

Führen Sie mehrere Zertifikate in einer Kette zusammen.

1. **Öffnen Sie alle Zertifikate**, die Sie erhalten haben, in einem Text-Editor.

2. Erstellen Sie eine Datei für das zusammengeführte Zertifikat mit dem Namen *mergedcertificate.crt*. Kopieren Sie den Inhalt der einzelnen Zertifikate in einem Text-Editor in diese Datei. Die Reihenfolge Ihrer Zertifikate sollte der Reihenfolge in der Zertifikatkette folgen, beginnend mit Ihrem Zertifikat und endend mit dem Stammzertifikat. Dies sieht in etwa wie im folgenden Beispiel aus:

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Exportieren des Zertifikats als PFX-Datei

Exportieren Sie das zusammengeführte SSL-Zertifikat mit dem privaten Schlüssel, der vom Zertifikat generiert wurde.

Eine Datei für den privaten Schlüssel wird per OpenSSL erstellt. Führen Sie zum Exportieren des Zertifikats nach PFX den folgenden Befehl aus, und ersetzen Sie die Platzhalter *<private-key-file>* und *<merged-certificate-file>* durch die Pfade zum privaten Schlüssel und die zusammengeführte Zertifikatdatei.

```PowerShell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Definieren Sie bei entsprechender Aufforderung ein Exportkennwort zum späteren Hochladen Ihres SSL-Zertifikats in App Service.

Gehen Sie wie folgt vor, wenn IIS oder **Certreq.exe** zum Generieren der Zertifikatanforderung verwendet werden: Installieren Sie das Zertifikat auf einem lokalen Computer, und [exportieren Sie das Zertifikat dann nach PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Hochladen des SSL-Zertifikats

1.  Wählen Sie im linken Navigationsbereich der Web-App die Option **SSL-Einstellungen**.

2.  Wählen Sie **Zertifikat hochladen**.

3.  Wählen Sie unter **PFX-Zertifikatdatei** die PFX-Datei aus.

4.  4. Geben Sie unter **Zertifikatkennwort** das Kennwort ein, das beim Exportieren der PFX-Datei erstellt wurde.

5.  Wählen Sie die Option **Hochladen**.

![Hochladen des Zertifikats](media/azure-stack-solution-geo-distributed/image38.png)

Wenn der Upload des Zertifikats in App Service abgeschlossen ist, wird es auf der Seite **SSL-Einstellungen** angezeigt.

![Alt text](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>Binden Ihres SSL-Zertifikats

1.  Wählen Sie im Abschnitt **SSL-Bindungen** die Option **Bindung hinzufügen**.

    > [!Note]  
    >  Wenn das Zertifikat hochgeladen wurde, aber in der Dropdownliste **Hostname** nicht als Domänenname angezeigt wird, können Sie versuchen, die Browserseite zu aktualisieren.

1.  Wählen Sie auf der Seite **SSL-Bindung hinzufügen** aus den Dropdownlisten den Domänennamen, der geschützt werden soll, sowie das zu verwendende Zertifikat aus.

2.  Wählen Sie unter **SSL-Typ** aus, ob SSL auf der [**Servernamensanzeige (Server Name Indication, SNI)**](http://en.wikipedia.org/wiki/Server_Name_Indication) oder der IP basieren soll.

-   **SNI-basiertes SSL**: Ggf. können mehrere SNI-basierte SSL-Bindungen hinzugefügt werden. Bei dieser Option können mehrere zur selben IP-Adresse zugehörige Domänen durch mehrere SSL-Zertifikate geschützt werden. Die meisten modernen Browser (einschließlich Internet Explorer, Chrome, Firefox und Opera) unterstützen SNI (ausführlichere Informationen zur Browserunterstützung finden Sie unter [Servernamensanzeige](http://wikipedia.org/wiki/Server_Name_Indication)).

-   **IP-basiertes SSL**: Ggf. kann nur eine IP-basierte SSL-Bindung hinzugefügt werden. Bei dieser Option kann eine dedizierte öffentliche IP-Adresse nur durch ein SSL-Zertifikat geschützt werden. Schützen Sie alle Domänen mit demselben SSL-Zertifikat, wenn Sie den Schutz für mehrere Domänen einrichten möchten. Dies ist die herkömmliche Option für SSL-Bindungen.

    1.  Wählen Sie **Bindung hinzufügen**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image40.png)

Wenn der Upload des Zertifikats in App Service abgeschlossen ist, wird es in den Abschnitten **SSL-Bindungen** angezeigt.

![Alt text](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Neuzuordnen des A-Eintrags für IP-SSL

Wenn IP-basiertes SSL in der Web-App nicht verwendet wird, können Sie mit [Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure-Web-Apps](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) fortfahren.

Standardmäßig verwendet die Web-App eine freigegebene öffentliche IP-Adresse. Wenn das Zertifikat per IP-basiertem SSL gebunden ist, erstellt App Service eine neue und dedizierte IP-Adresse für die Web-App.

Wenn ein A-Eintrag der Web-App zugeordnet wird, muss die Domänenregistrierung mit der dedizierten IP-Adresse aktualisiert werden.

Die Seite **Benutzerdefinierte Domäne** wird mit der neuen, dedizierten IP-Adresse aktualisiert. [Kopieren Sie diese IP-Adresse](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), und [ordnen Sie dieser neuen IP-Adresse dann den A-Eintrag erneut zu](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain).

#### <a name="test-https"></a>Testen von HTTPS

Navigieren Sie in verschiedenen Browsern zu „https://<ihre.benutzerdefinierte.domäne>“, um sicherzustellen, dass die Web-App bereitgestellt wird.

![Alt text](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> Wenn Zertifikatüberprüfungsfehler auftreten, kann ein selbstsigniertes Zertifikat der Grund sein, oder beim Exportieren in die PFX-Datei wurden Zwischenzertifikate nicht abgeschlossen.

#### <a name="enforce-https"></a>Erzwingen von HTTPS

Standardmäßig haben alle Benutzer per HTTP Zugriff auf die Web-App. Alle HTTP-Anforderungen an den HTTPS-Port werden ggf. umgeleitet.

Wählen Sie auf der Web-App-Seite die Option **SSL-Einstellungen**. Wählen Sie anschließend für **Nur HTTPS** die Option **Ein**.

![Erzwingen von HTTPS](media/azure-stack-solution-geo-distributed/image43.png)

Navigieren Sie nach Abschluss des Vorgangs zu einer beliebigen HTTP-URL, die auf die App verweist. Beispiel: 

-   http://<App-Name>.azurewebsites.net
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>Erzwingen von TLS 1.1/1.2

Die App lässt standardmäßig [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 zu. Dies ist laut Branchenstandards wie [PCI-DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) nicht mehr sicher. Gehen Sie wie folgt vor, um höhere TLS-Versionen zu erzwingen:

1.  Klicken Sie im linken Navigationsbereich der Web-App-Seite auf **SSL-Einstellungen**.

2.  Wählen Sie unter **TLS-Version** die TLS-Mindestversion aus.

![Erzwingen von TLS 1.1 oder 1.2](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

1.  Wählen Sie **Ressource erstellen** > **Netzwerk** > **Traffic Manager-Profil** > **Erstellen**.

2.  Füllen Sie den Bereich **Traffic Manager-Profil erstellen** wie folgt aus:

    1.  Geben Sie im Feld **Name** einen Namen für das Profil ein. Dieser Name muss innerhalb der Zone „trafficmanager.net“ eindeutig sein und ergibt den DNS-Namen „trafficmanager.net“, der für den Zugriff auf das Traffic Manager-Profil verwendet wird.

    2.  Wählen Sie unter **Routingmethode** die **geografische Routingmethode** aus.

    3.  Wählen Sie unter **Abonnement** das Abonnement aus, unter dem Sie dieses Profil erstellen möchten.

    4.  Erstellen Sie unter **Ressourcengruppe** eine neue Ressourcengruppe, unter der Sie das Profil platzieren möchten.

    5.  Wählen Sie unter **Ressourcengruppenstandort** den Speicherort für die Ressourcengruppe aus. Diese Einstellung bezieht sich auf den Speicherort der Ressourcengruppe und hat keine Auswirkungen auf das Traffic Manager-Profil, das global bereitgestellt wird.

    6.  Klicken Sie auf **Erstellen**.

    7.  Wenn die globale Bereitstellung des Traffic Manager-Profils abgeschlossen ist, wird sie in der betreffenden Ressourcengruppe als eine der Ressourcen aufgelistet.

    ![Alt text](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Hinzufügen von Traffic Manager-Endpunkten

1.  Suchen Sie in der Suchleiste des Portals nach dem Namen für das **Traffic Manager-Profil**, das im vorherigen Abschnitt erstellt wurde, und wählen Sie das Traffic Manager-Profil in den angezeigten Ergebnissen aus.

2.  Wählen Sie im **Traffic Manager-Profil** im Abschnitt **Einstellungen** die Option **Endpunkte**.

3.  Wählen Sie **Hinzufügen**.

4.  Der Azure Stack-Endpunkt wird hinzugefügt.

5.  Wählen Sie unter **Typ** die Option **Externer Endpunkt**.

6.  Geben Sie unter **Name** einen Namen für diesen Endpunkt ein. Dies ist idealerweise der Name der Azure Stack-Instanz.

7.  Verwenden Sie für den vollqualifizierten Domänennamen (**FQDN**) die externe URL für die Azure Stack-Web-App.

8.  Wählen Sie unter „Geografische Zuordnung“ die Region bzw. den Kontinent der Ressource aus, z.B. **Europa**.

9.  Wählen Sie in der angezeigten Dropdownliste „Land/Region“ das Land aus, das für diesen Endpunkt gilt, z.B. **Deutschland**.

10. Lassen Sie **Als deaktiviert hinzufügen** deaktiviert.

11. Klicken Sie auf **OK**.

12. Hinzufügen des Azure-Endpunkts:

    1.  Wählen Sie für **Typ** die Option **Azure-Endpunkt**.

    2.  Geben Sie unter **Name** einen Namen für den Endpunkt an.

    3.  Wählen Sie unter **Zielressourcentyp** die Option **App Service**.

    4.  Wählen Sie unter **Zielressource** die Option **App Service auswählen**, um die Auflistung der Web-Apps desselben Abonnements anzuzeigen. Wählen Sie unter **Ressource** die App Service-Instanz aus, die als erster Endpunkt verwendet werden soll.

13. Wählen Sie unter „Geografische Zuordnung“ die Region bzw. den Kontinent der Ressource aus, z.B. **Nordamerika/Zentralamerika/Karibik**.

14. Lassen Sie das Feld der Dropdownliste „Land/Region“ leer, um alle obigen regionalen Gruppierungen auszuwählen.

15. Lassen Sie **Als deaktiviert hinzufügen** deaktiviert.

16. Klicken Sie auf **OK**.

  > [!Note]  
  >  Erstellen Sie mindestens einen Endpunkt mit dem geografischen Bereich „Alle (Welt)“, der als Standardendpunkt für die Ressource dient.

1.  Wenn Sie das Hinzufügen beider Endpunkte abgeschlossen haben, werden diese unter **Traffic Manager-Profil** zusammen mit ihrem Überwachungsstatus als **Online** angezeigt.

  ![Alt text](media/azure-stack-solution-geo-distributed/image46.png)

**Globales Unternehmen nutzt Azure-Funktionen für die geografische Verteilung**

Die Weiterleitung des Datenverkehrs mit Azure Traffic Manager und geografieabhängigen Endpunkten ermöglicht es globalen Unternehmen, regionale Bestimmungen einzuhalten und für die Konformität und den Schutz der Daten zu sorgen. Dies ist für den Erfolg von lokalen Unternehmen und über Remotestandorte hinweg von entscheidender Bedeutung.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](https://docs.microsoft.com/azure/architecture/patterns).
