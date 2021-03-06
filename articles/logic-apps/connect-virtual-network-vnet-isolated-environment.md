---
title: Herstellen einer Verbindung mit virtuellen Azure-Netzwerken in Azure Logic Apps über eine Integrationsdienstumgebung (ISE)
description: Erstellen Sie eine Integrationsdienstumgebung (Integration Service Environment, ISE), damit Logik-Apps und Integrationskonten auf virtuelle Azure-Netzwerke (VNETs) zugreifen und dabei trotzdem privat und vom öffentlichen oder globalen Azure-Dienst isoliert bleiben können.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 12/06/2018
ms.openlocfilehash: 31f3cf9bd8f83c5da32569ed370de1ed35299749
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062382"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>Herstellen einer Verbindung mit virtuellen Azure-Netzwerken in Azure Logic Apps über eine Integrationsdienstumgebung (ISE)

> [!NOTE]
> Diese Funktion befindet sich in der *privaten Vorschau*. Um den Zugriff anzufordern, [erstellen Sie Ihre Beitrittsanforderung hier](https://aka.ms/iseprivatepreview).

Für Szenarios, in denen Ihre Logik-Apps und Integrationskonten Zugriff auf ein [virtuelles Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) benötigen, erstellen Sie eine [*Integrationsdienstumgebung* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Eine Integrationsdienstumgebung ist eine private und isolierte Umgebung, die dedizierten Speicher und andere Ressourcen verwendet, die getrennt vom öffentlichen oder „globalen“ Logic Apps-Dienst gehalten werden. Diese Trennung trägt auch dazu bei, jegliche Auswirkungen anderer Azure-Mandanten auf die Leistung Ihrer Apps zu verringern. Ihre ISE wird in Ihr virtuelles Azure-Netzwerk *eingefügt*. Daraufhin wird der Logic Apps-Dienst in Ihrem virtuellen Netzwerk bereitgestellt. Wählen Sie diese Integrationsdienstumgebung beim Erstellen einer Logik-App oder eines Integrationskontos als Standort aus. Ihre Logik-App bzw. Ihr Integrationskonto kann dann direkt auf Ressourcen wie virtuelle Computer (VMs), Server, Systeme und Dienste in Ihrem virtuellen Netzwerk zugreifen. 

![Auswählen der Integrationsdienstumgebung](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

In diesem Artikel wird gezeigt, wie Sie die folgenden Aufgaben ausführen:

* Einrichten von Berechtigungen für Ihr Azure Virtual Network, damit die private Logic Apps-Instanz auf Ihr virtuelles Netzwerk zugreifen kann.

* Erstellen Ihrer Integrationsdienstumgebung. 

* Erstellen einer Logik-App, die in Ihrer Integrationsdienstumgebung ausgeführt werden kann. 

* Erstellen eines Integrationskontos für Ihre Logik-Apps in Ihrer Integrationsdienstumgebung.

Weitere Informationen zu Integrationsdienstumgebungen finden Sie unter [Zugriff auf Azure Virtual Network-Ressourcen aus Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

  > [!IMPORTANT]
  > Für Logik-Apps, integrierte Aktionen und Connectors, die in Ihrer ISE ausgeführt werden, gilt ein anderer als der nutzungsbasierte Tarif. Weitere Informationen hierzu finden Sie unter [Logic Apps – Preise](../logic-apps/logic-apps-pricing.md).

* Ein [virtuelles Azure-Netzwerk](../virtual-network/virtual-networks-overview.md). Wenn Sie kein virtuelles Netzwerk besitzen, erfahren Sie, wie Sie [ein virtuelles Azure-Netzwerk erstellen](../virtual-network/quick-create-portal.md). 

* Um Ihren Logik-Apps direkten Zugriff auf Ihre Azure Virtual Network zu gewähren, [richten Sie rollenbasierte Zugriffssteuerungsberechtigungen ein](#vnet-access), damit der Logic Apps-Dienst über die Berechtigungen für den Zugriff auf Ihr virtuelles Netzwerk verfügt. 

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Einrichten von virtuellen Netzwerkberechtigungen

Wenn Sie eine Integrationsdienstumgebung (ISE) erstellen, wählen Sie ein virtuelles Azure-Netzwerk aus, in das Sie Ihre Umgebung *einfügen*. Bevor Sie jedoch ein virtuelles Netzwerk auswählen können, um darin Ihre Umgebung einzufügen, müssen Sie rollenbasierte Zugriffssteuerungsberechtigungen in Ihrem virtuellen Netzwerk einrichten. Um Berechtigungen einzurichten, weisen Sie die folgenden spezifischen Rollen dem Azure Logic Apps-Dienst zu:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrem virtuellen Netzwerk, und wählen Sie es aus. 

1. Wählen Sie im Menü Ihres virtuellen Netzwerks **Zugriffssteuerung (IAM)** aus. 

1. Wählen Sie unter **Zugriffssteuerung (IAM)** die Option **Rollenzuweisung hinzufügen** aus. 

   ![Hinzufügen von Rollen](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Fügen Sie im Bereich **Rollenzuweisung hinzufügen**, wie beschrieben, dem Azure Logic Apps-Dienst die notwendige Rolle hinzu. 

   1. Wählen Sie unter **Rolle** die Option **Netzwerkmitwirkender** aus. 
   
   1. Wählen Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** aus.

   1. Geben Sie unter **Auswählen** den Wert **Azure Logic Apps** ein. 

   1. Nachdem die Mitgliederliste angezeigt wird, wählen Sie **Azure Logic Apps** aus. 

      > [!TIP]
      > Wenn Sie diesen Dienst nicht finden, geben Sie die App-ID des Logic Apps-Diensts ein: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` 
   
   1. Wenn Sie fertig sind, wählen Sie **Speichern** aus.

   Beispiel: 

   ![Rollenzuweisung hinzufügen](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

Weitere Informationen finden Sie unter [Berechtigungen für den Zugriff auf virtuelle Netzwerke](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Erstellen Ihrer Integrationsdienstumgebung

Befolgen Sie zum Erstellen Ihrer Integrationsdienstumgebung diese Schritte:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Azure-Hauptmenü **Ressource erstellen** aus.

   ![Neue Ressource erstellen](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Geben Sie in das Suchfeld „Integrationsdienstumgebung“ als Ihren Filter ein.
Wählen Sie in der Ergebnisliste **Integrationsdienstumgebung (Preview)** und dann **Erstellen** aus.

   ![Auswählen von „Integrationsdienstumgebung“](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Auswählen von „Erstellen“](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Geben Sie diese Details für Ihre Umgebung an, und wählen Sie dann **Bewerten + erstellen** aus, z. B.:

   ![Angeben von Umgebungsdetails](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Abonnement** | JA | <*Name des Azure-Abonnements*> | Das für Ihre Umgebung zu verwendende Azure-Abonnement | 
   | **Ressourcengruppe** | JA | <*Name der Azure-Ressourcengruppe*> | Die Azure-Ressourcengruppe, in der Sie Ihre Umgebung erstellen möchten. |
   | **Name der Integrationsdienstumgebung** | JA | <*Umgebungsname*> | Der Name für Ihre Umgebung | 
   | **Location** | JA | <*Azure-Datencenterregion*> | Die Azure-Datencenterregion, in der Sie Ihre Umgebung bereitstellen. | 
   | **Zusätzliche Kapazität** | JA | 0, 1, 2, 3 | Die Anzahl der für diese ISE-Ressource zu verwendenden Verarbeitungseinheiten. | 
   | **Virtuelles Netzwerk** | JA | <*Azure-virtual-network-name*> | Das virtuelle Azure-Netzwerk, in das Sie Ihre Umgebung einfügen möchten, damit Logik-Apps in dieser Umgebung auf Ihr virtuelles Netzwerk zugreifen können. Wenn Sie über kein Netzwerk verfügen, können Sie hier eins erstellen. <p>**Wichtig**: Sie können diese Einfügung *nur* einmalig durchführen, wenn Sie Ihre ISE erstellen. Bevor Sie diese Beziehung jedoch erstellen können, stellen Sie sicher, dass Sie bereits [rollenbasierte Zugriffssteuerung in Ihrem virtuellen Netzwerk für Azure Logic Apps eingerichtet haben](#vnet-access). | 
   | **Subnetze** | JA | <*subnet-resource-list*> | Eine ISE erfordert vier *leere* Subnetze zum Erstellen von Ressourcen in Ihrer Umgebung. Achten Sie, darauf dass diese Subnetze *nicht* an jeden Dienst delegiert werden. Sie können diese Subnetzadressen *nicht mehr ändern*, nachdem Sie Ihre Umgebung erstellt haben. <p><p>Um jedes Subnetz zu erstellen, [führen Sie die Schritte unter dieser Tabelle aus](#create-subnet). Jedes Subnetz muss diese Kriterien erfüllen: <p>- Muss leer sein. <br>- Verwendet einen Namen, der weder mit einer Zahl noch mit einem Bindestrich beginnt. <br>– Verwendet das Format [Classless Inter-Domain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) und einen Class B-Adressraum. <br>– Enthält mindestens `/27` im Adressraum, sodass das Subnetz mindestens 32 Adressen erhält. Informationen zum Berechnen der Anzahl der Adressen finden Sie unter [IPv4 CIDR blocks (CIDR-Blöcke)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks). Beispiel:  <p>- `10.0.0.0/24` hat 256 Adressen, da 2<sup>(32 – 24)</sup> 2<sup>8</sup> oder 256 ist. <br>- `10.0.0.0/27` hat 32 Adressen, da 2<sup>(32 – 27)</sup> 2<sup>5</sup> oder 32 ist. <br>- `10.0.0.0/28` hat nur 16 Adressen, da 2<sup>(32-28)</sup> 2<sup>4</sup> oder 16 ist. |
   |||||

   <a name="create-subnet"></a>

   **Erstellen eines Subnetzes**

   1. Wählen Sie unter der Liste **Subnetze** den Eintrag **Subnetzkonfiguration verwalten** aus.

      ![Verwalten der Subnetzkonfiguration](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Wählen Sie im Bereich **Subnetze** den Eintrag **Subnetz** aus.

      ![Hinzufügen des Subnetzes](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Geben Sie im Bereich **Subnetz hinzufügen** diese Informationen an.

      * **Name**: Der Name für Ihr Subnetz.
      * **Adressbereich (CIDR-Block)**: Der Bereich Ihres Subnetzes in Ihrem virtuellen Netzwerk und im CIDR-Format

      ![Hinzufügen von Subnetzdetails](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Wählen Sie **OK** aus, wenn Sie fertig sind.

   1. Wiederholen Sie diese Schritte für drei weitere Subnetze.

1. Nachdem Azure Ihre ISE-Informationen erfolgreich überprüft hat, wählen Sie **Erstellen** aus, z. B.:

   ![Wählen Sie nach der erfolgreicher Überprüfung „Erstellen“ aus.](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure beginnt mit der Bereitstellung Ihrer Umgebung, aber dieser Prozess *könnte* bis zu zwei Stunden dauern, bevor er abgeschlossen ist. 
   Um den Bereitstellungsstatus zu überprüfen, wählen Sie in Ihrer Azure-Symbolleiste das Symbol „Benachrichtigungen“ aus, wodurch der Benachrichtigungsbereich geöffnet wird.

   ![Überprüfen des Bereitstellungsstatus](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Wenn die Bereitstellung erfolgreich abgeschlossen wurde, zeigt Azure diese Benachrichtigung an:

   ![Bereitstellung erfolgreich](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > Schlägt die Bereitstellung fehl, oder wenn Sie Ihre ISE löschen, *könnte* Azure bis zu eine Stunde benötigen, bis Ihre Subnetze freigegeben werden. Daher müssen Sie möglicherweise warten, bevor Sie diese Subnetzen in einer anderen ISE wiederverwenden können.

1. Um Ihre Umgebung anzuzeigen, wählen Sie **Zu Ressource wechseln** aus, wenn Azure nach Abschluss der Bereitstellung nicht automatisch zu Ihrer Umgebung wechselt.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Erstellen einer Logik-App – Integrationsdienstumgebung

Um Logik-Apps zu erstellen, die Ihre Integrationsdienstumgebung verwenden, führen Sie die Schritte in [Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) aus, jedoch mit folgenden Unterschieden: 

* Wenn Sie Ihre Logik-App erstellen, wählen Sie unter der Eigenschaft **Standort** Ihre Integrationsdienstumgebung im Abschnitt **Integrationsdienstumgebungen** aus, z. B.:

  ![Auswählen der Integrationsdienstumgebung](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Sie können dieselben integrierten Trigger und Aktionen wie für HTTP verwenden, die in derselben Integrationsdienstumgebung wie Ihre Logik-App ausgeführt werden. Connectors mit der Bezeichnung **ISE** werden auch in derselben Integrationsdienstumgebung wie Ihre Logik-App ausgeführt. Connectors ohne die Bezeichnung **ISE** werden im globalen Logic Apps-Dienst ausgeführt.

  ![Auswählen der ISE-Connectors](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Nachdem Sie Ihre Integrationsdienstumgebung in ein virtuelles Azure-Netzwerk eingefügt haben, können die Logik-Apps in Ihrer Integrationsdienstumgebung direkt auf Ressourcen in diesem virtuellen Netzwerk zugreifen. Bei lokalen Systemen, die mit einem virtuellen Netzwerk verbunden sind, fügen Sie eine Integrationsdienstumgebung in dieses Netzwerk ein, damit Ihre Logik-Apps direkt auf diese Systeme zugreifen können, indem sie eins der folgenden Elemente verwenden: 

  * ISE-Connector für dieses System, z.B. SQL Server
  
  * HTTP-Aktion 
  
  * Benutzerdefinierter Connector

  Für lokale Systeme, die sich nicht in einem virtuellen Netzwerk befinden oder die keine Integrationsdienstumgebungs-Connectors aufweisen, [richten Sie zuerst das lokale Datengateway ein](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Erstellen des Integrationskontos – Integrationsdienstumgebung

Um ein Integrationskonto mit Logik-Apps in einer Integrationsdienstumgebung zu verwenden, muss dieses Integrationskonto *dieselbe Umgebung* wie die Logik-Apps verwenden. Logik-Apps in einer Integrationsdienstumgebung können nur auf Integrationskonten in derselben Integrationsdienstumgebung verweisen. 

Führen Sie zum Erstellen eines Integrationskontos, das eine Integrationsdienstumgebung verwendet, die Schritte in [Erstellen von Integrationskonten](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) aus, mit Ausnahme der Eigenschaft **Standort**, an deren Stelle nun der Abschnitt **Integrationsdienstumgebungen** angezeigt wird. Wählen Sie stattdessen Ihre Integrationsdienstumgebung aus, anstelle einer Region, z. B.:

![Auswählen der Integrationsdienstumgebung](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps-Forum</a>.
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die <a href="https://aka.ms/logicapps-wish" target="_blank">Website für Logic Apps-Benutzerfeedback</a>.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Erfahren Sie mehr über die [Integration virtueller Netzwerke für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md).
