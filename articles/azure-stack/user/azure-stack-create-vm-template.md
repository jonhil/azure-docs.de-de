---
title: Tutorial zum Erstellen einer Azure Stack-VM mit einer Vorlage | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie das ASDK zum Erstellen eines virtuellen Computers mit einer vordefinierten Vorlage und einer benutzerdefinierten GitHub-Vorlage verwenden.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 2f769706b5f6a9fa4326a0efeb9e8afb2edd8296
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246159"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Tutorial: Erstellen einer VM mit einer Communityvorlage

Als Azure Stack-Betreiber oder -Benutzer können Sie eine VM erstellen, indem Sie [benutzerdefinierte GitHub-Schnellstartvorlagen](https://github.com/Azure/AzureStack-QuickStart-Templates) verwenden, anstatt eine Vorlage manuell über den Azure Stack-Marketplace bereitzustellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden von Azure Stack-Schnellstartvorlagen 
> * Erstellen einer VM mit einer benutzerdefinierten GitHub-Vorlage
> * Starten von Minikube und Installieren einer Anwendung

## <a name="azure-stack-quickstart-templates"></a>Azure Stack-Schnellstartvorlagen

Azure Stack-Schnellstartvorlagen werden auf GitHub im [Repository für öffentliche Azure Stack-QuickStart-Vorlagen](https://github.com/Azure/AzureStack-QuickStart-Templates) gespeichert. Dieses Repository enthält Azure Resource Manager-Bereitstellungsvorlagen, die mit dem Microsoft Azure Stack Development Kit (ASDK) getestet wurden. Sie können sie verwenden, um Ihnen das Auswerten von Azure Stack und Verwenden der ASDK-Umgebung zu vereinfachen. 

Im Laufe der Zeit haben viele GitHub-Benutzer einen Beitrag zum Repository geleistet, sodass sich eine Sammlung mit mehr als 400 Bereitstellungsvorlagen ergeben hat. Dieses Repository ist ein hervorragender Ausgangspunkt, um ein besseres Verständnis zu entwickeln, wie Sie verschiedene Arten von Umgebungen in Azure Stack bereitstellen können. 

>[!IMPORTANT]
> Einige dieser Vorlagen wurden von Mitgliedern der Community und nicht von Microsoft erstellt. Jede Vorlage wird von dessen Besitzer, nicht von Microsoft, unter einem Lizenzvertrag lizenziert. Microsoft ist für diese Vorlagen nicht verantwortlich und führt dafür keine Tests in Bezug auf die Sicherheit, Kompatibilität oder Leistung durch. Vorlagen aus der Community werden von keinem Microsoft-Supportprogramm oder -Dienst unterstützt und „wie besehen“ ohne jegliche Gewährleistung zur Verfügung gestellt.

Wenn Sie mit Ihren Azure Resource Manager-Vorlagen einen Beitrag zu GitHub leisten möchten, sollten Sie sie im Repository [AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates) bereitstellen. Weitere Informationen zu diesem Repository und zu Beiträgen dafür finden Sie in der [Infodatei](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 

## <a name="create-a-vm-using-a-custom-github-template"></a>Erstellen einer VM mit einer benutzerdefinierten GitHub-Vorlage

In diesem Beispieltutorial wird die Azure Stack-Schnellstartvorlage [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) verwendet, um einen virtuellen Ubuntu 16.04-Computer unter Azure Stack bereitzustellen, auf dem Minikube zum Verwalten eines Kubernetes-Clusters ausgeführt wird.

Minikube ist ein Tool, mit dem die lokale Ausführung von Kubernetes leicht möglich ist. Minikube führt einen Kubernetes-Cluster mit nur einem Knoten auf einer VM aus, wodurch Sie Kubernetes ausprobieren oder damit tägliche Entwicklungsarbeiten leisten können. Das Tool unterstützt einen einfachen Kubernetes-Cluster mit einem Knoten, der auf einer Linux-VM ausgeführt wird. Minikube ist die schnellste und einfachste Möglichkeit zum Ausführen eines voll funktionsfähigen Kubernetes-Clusters. Entwickler können hiermit ihre Bereitstellungen von Kubernetes-basierten Anwendungen auf ihren lokalen Computern entwickeln und testen. Im Hinblick auf die Architektur führt die Minikube-VM sowohl Master- als auch Agent-Knotenkomponenten lokal aus:

- Masterknotenkomponenten, z.B. API-Server, Scheduler und [etcd-Server](https://coreos.com/etcd/), werden in einem einzelnen Linux-Prozess mit dem Namen „LocalKube“ ausgeführt.
- Agent-Knotenkomponenten werden in Docker-Containern ausgeführt, wie dies auch auf einem normalen Agent-Knoten der Fall wäre. Aus Sicht der Anwendungsbereitstellung besteht kein Unterschied darin, ob die Anwendung auf einem Minikube oder in einem regulären Kubernetes-Cluster bereitgestellt wird.

Mit dieser Vorlage werden die folgenden Komponenten installiert:

- Ubuntu 16.04 LTS VM
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> Das Ubuntu-VM-Image (hier: Ubuntu Server 16.04 LTS) muss dem Azure Stack-Marketplace bereits hinzugefügt worden sein, bevor Sie diese Schritte ausführen.

1.  Wählen Sie **+ Ressource erstellen** > **Benutzerdefiniert** > **Vorlagenbereitstellung** aus.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Wählen Sie **Vorlage bearbeiten** aus.

    ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Wählen Sie **Schnellstartvorlage** aus.

    ![](media/azure-stack-create-vm-template/3.PNG)

4. Wählen Sie aus den verfügbaren Vorlagen die Option **101-vm-linux-minikube** aus, indem Sie die Dropdownliste **Vorlage auswählen** verwenden und dann auf **OK** klicken.  

    ![](media/azure-stack-create-vm-template/4.PNG)

5. Sie können auch Änderungen an der JSON-Vorlagendatei vornehmen. Wenn Sie die Datei nicht ändern möchten oder mit der Bearbeitung fertig sind, wählen Sie **Speichern**, um das Dialogfeld **Vorlage bearbeiten** zu schließen.

    ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Wählen Sie **Parameter** aus, füllen Sie je nach Bedarf die verfügbaren Felder aus (bzw. ändern Sie sie), und klicken Sie anschließend auf **OK**. Wählen Sie das gewünschte Abonnement aus, erstellen oder wählen Sie einen vorhandenen Ressourcengruppennamen, und klicken Sie dann auf **Erstellen**, um die Vorlagenbereitstellung zu initiieren.

    ![](media/azure-stack-create-vm-template/6.PNG)

7. Wählen Sie das gewünschte Abonnement aus, erstellen oder wählen Sie einen vorhandenen Ressourcengruppennamen, und klicken Sie dann auf **Erstellen**, um die Vorlagenbereitstellung zu initiieren.

    ![](media/azure-stack-create-vm-template/7.PNG)

8. Die Bereitstellung der Ressourcengruppe dauert mehrere Minuten, um die benutzerdefinierte vorlagenbasierte VM zu erstellen. Sie können den Installationsstatus anhand von Benachrichtigungen und über die Ressourcengruppeneigenschaften überwachen. 

    ![](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > Die VM wird ausgeführt, wenn die Bereitstellung abgeschlossen ist. 

## <a name="start-minikube-and-install-an-application"></a>Starten von Minikube und Installieren einer Anwendung

Nachdem der virtuelle Linux-Computer nun erfolgreich erstellt wurde, können Sie sich anmelden, um Minikube zu starten und eine Anwendung zu installieren. 

1. Wählen Sie nach Abschluss der Bereitstellung **Verbinden** aus, um die öffentliche IP-Adresse anzuzeigen, die zum Herstellen der Verbindung mit dem virtuellen Linux-Computer verwendet werden soll. 

    ![](media/azure-stack-create-vm-template/9.PNG)

2. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten **mstsc.exe** aus, um die Remotedesktopverbindung zu öffnen und eine Verbindung mit der öffentlichen IP-Adresse des virtuellen Linux-Computers herzustellen, die im vorherigen Schritt ermittelt wurde. Wenn Sie zum Anmelden bei xRDP aufgefordert werden, verwenden Sie die Anmeldeinformationen, die Sie bei der Erstellung der VM angegeben haben.

    ![](media/azure-stack-create-vm-template/10.PNG)

3. Öffnen Sie die Terminalemulation, und geben Sie die folgenden Befehle ein, um Minikube zu starten:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![](media/azure-stack-create-vm-template/11.PNG)

4. Öffnen Sie den Webbrowser, und rufen Sie die Adresse des Kubernetes-Dashboards auf. Herzlichen Glückwunsch! Sie verfügen nun über eine voll funktionsfähige Kubernetes-Installation mit Minikube.

    ![](media/azure-stack-create-vm-template/12.PNG)

5. Navigieren Sie zum Bereitstellen einer Beispielanwendung zur offiziellen Seite mit der Kubernetes-Dokumentation navigieren, und überspringen Sie den Abschnitt „Create Minikube Cluster“ (Minikube-Cluster erstellen), da Sie diesen oben bereits erstellt haben. Fahren Sie mit dem Abschnitt „Create your Node.js application“ (Node.js-Anwendung erstellen) unter https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/ fort.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Verwenden von Azure Stack-Schnellstartvorlagen 
> * Erstellen einer VM mit einer benutzerdefinierten GitHub-Vorlage
> * Starten von Minikube und Installieren einer Anwendung

