---
title: 'Tutorial: Lastenausgleich für virtuelle Computer innerhalb einer Zone – Azure-Portal'
titlesuffix: Azure Load Balancer
description: In diesem Tutorial wird gezeigt, wie Sie einen Load Balancer Standard mit einem zonalen Front-End erstellen, um mit dem Azure-Portal einen Lastenausgleich für virtuelle Computer innerhalb einer Verfügbarkeitszone vorzunehmen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2018
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 76e55c643378e689f12d485100a81ccefa4196f4
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229811"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>Tutorial: Durchführen eines Lastenausgleichs für virtuelle Computer innerhalb einer Verfügbarkeitszone mit Load Balancer Standard im Azure-Portal

In diesem Tutorial wird mit dem Azure-Portal eine öffentliche [Azure Load Balancer Standard-Instanz](https://aka.ms/azureloadbalancerstandard) mit einem Zonen-Front-End erstellt, für das eine öffentliche Standard-IP-Adresse verwendet wird. In diesem Szenario können Sie auch eine bestimmte Zone für Ihre Front-End- und Back-End-Instanzen angeben, um Ihren Datenpfad und Ihre Ressourcen mit einer bestimmten Zone zu verbinden. Sie erfahren, wie Sie die folgenden Funktionen durchführen:

> [!div class="checklist"]
> * Erstellen einer Load Balancer Standard-Instanz mit einem Zonen-Front-End
> * Erstellen von Netzwerksicherheitsgruppen zum Definieren von Regeln für eingehenden Datenverkehr
> * Erstellen von virtuellen Computern (VMs) in einer Zone und Anfügen an einen Lastenausgleich
> * Erstellen des Integritätstests für den Lastenausgleich
> * Erstellen von Lastenausgleichsregeln
> * Erstellen einer grundlegenden IIS-Website (Internetinformationsdienste)
> * Anzeigen eines Lastenausgleichs in Aktion

Informationen zur Verwendung von Verfügbarkeitszonen mit Load Balancer Standard finden Sie unter [Load Balancer Standard und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md).

Sie können auch die [Azure CLI](load-balancer-standard-public-zonal-cli.md) verwenden, um dieses Tutorial durchzuarbeiten, falls Sie dies vorziehen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [http://portal.azure.com](http://portal.azure.com) beim Azure-Portal an.

## <a name="create-a-public-standard-load-balancer-instance"></a>Erstellen einer öffentlichen Load Balancer Standard-Instanz

Für Load Balancer Standard wird nur eine öffentliche Standard-IP-Adresse unterstützt. Wenn Sie eine neue öffentliche IP-Adresse beim Erstellen des Lastenausgleichs erstellen, wird dieser automatisch als Version mit der SKU „Standard“ konfiguriert. Er ist automatisch auch zonenredundant.

1. Wählen Sie oben links auf dem Bildschirm **Ressource erstellen** > **Netzwerk** > **Lastenausgleich**.
2. Geben Sie auf der Seite **Lastenausgleich erstellen** folgende Werte für den Lastenausgleich ein:
    - **myLoadBalancer**: Der Name für den Lastenausgleich.
    - **Öffentlich**: Der Typ des Lastenausgleichs.
      - **myPublicIPZonal**: Die neue öffentliche IP-Adresse, die Sie erstellen. Wählen Sie **Öffentliche IP-Adresse auswählen**. Klicken Sie anschließend auf **Neu erstellen**. Geben Sie **myPublicIP** als Namen ein. Für die SKU wird standardmäßig „Standard“ verwendet. Wählen Sie für **Verfügbarkeitszone** die Option **Zone 1**.
    - **myResourceGroupZLB**: Der Name der neuen Ressourcengruppe, die Sie erstellen.
    - **westeurope**. Dies ist der Standort.
3. Wählen Sie **Erstellen**, um den Lastenausgleich zu erstellen.
   
    ![Erstellen einer Load Balancer Standard-Zoneninstanz mit dem Azure-Portal](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk. Außerdem erstellen Sie zwei virtuelle Computer in der derselben Zone (Zone 1) für die Region, die dem Back-End-Pool Ihres Lastenausgleichs hinzugefügt werden. Anschließend installieren Sie IIS auf den virtuellen Computern, um das Testen des zonenredundanten Lastenausgleichs zu unterstützen. Wenn eine VM ausfällt, schlägt auch der Integritätstest für die VM in derselben Zone fehl. Der Datenverkehr wird von anderen VMs innerhalb derselben Zone bereitgestellt.

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks
1. Wählen Sie oben links auf dem Bildschirm **Ressource erstellen** > **Netzwerk** > **Virtuelles Netzwerk**.  Geben Sie die folgenden Werte für das virtuelle Netzwerk ein:
    - **myVnet**: Der Name des virtuellen Netzwerks.
    - **myResourceGroupZLB**: Der Name der vorhandenen Ressourcengruppe.
    - **myBackendSubnet**: Der Subnetzname.
2. Wählen Sie **Erstellen**, um das virtuelle Netzwerk zu erstellen.

    ![Erstellen eines virtuellen Netzwerks](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

1. Wählen Sie oben links auf dem Bildschirm die Option **Ressource erstellen**. Geben Sie im Suchfeld **Netzwerksicherheitsgruppe** ein. Wählen Sie auf der Seite „Netzwerksicherheitsgruppe“ die Option **Erstellen**.
2. Geben Sie auf der Seite **Netzwerksicherheitsgruppe erstellen** die folgenden Werte ein:
    - **myNetworkSecurityGroup**: Der Name der Netzwerksicherheitsgruppe.
    - **myResourceGroupLBAZ**: Der Name der vorhandenen Ressourcengruppe.
   
     ![Erstellen einer Netzwerksicherheitsgruppe](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Erstellen von NSG-Regeln

In diesem Abschnitt erstellen Sie über das Azure-Portal NSG-Regeln, um eingehende Verbindungen zuzulassen, für die HTTP und das Microsoft-Remotedesktopprotokoll (RDP) verwendet werden.

1. Wählen Sie im Azure-Portal im Menü ganz links die Option **Alle Ressourcen**. Suchen Sie anschließend nach **myNetworkSecurityGroup**, und wählen Sie das entsprechende angezeigte Ergebnis aus. Es befindet sich unter der Ressourcengruppe **myResourceGroupZLB**.
2. Wählen Sie unter **Einstellungen** die Option **Eingangssicherheitsregeln**. Wählen Sie anschließend **Hinzufügen**.
3. Geben Sie für die Eingangssicherheitsregel **myHTTPRule** die folgenden Werte ein, um eingehende HTTP-Verbindungen über Port 80 zuzulassen:
    - **Service Tag** für **Quelle**
    - **Internet** für **Quelldiensttag**
    - **80** für **Zielportbereiche**
    - **vTCP** für **Protokoll**
    - **Zulassen** für **Aktion**
    - **100** für **Priorität**
    - **myHTTPRule** für **Name**
    - **Allow HTTP** für **Beschreibung**
4. Klicken Sie auf **OK**.
 
   ![Erstellen von NSG-Regeln](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Wiederholen Sie die Schritte 2 bis 4, um eine weitere Regel mit dem Namen **myRDPRule** zu erstellen. Diese Regel ermöglicht eine eingehende RDP-Verbindung mit Verwendung von Port 3389 und den folgenden Werten:
    - **Service Tag** für **Quelle**
    - **Internet** für **Quelldiensttag**
    - **3389** für **Zielportbereiche**
    - **TCP** für **Protokoll**
    - **Zulassen** für **Aktion**
    - **200** für **Priorität**
    - **myRDPRule** für **Name**
    - **Allow RDP** für **Beschreibung**

      ![Erstellen einer RDP-Regel](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

1. Wählen Sie oben links auf dem Bildschirm die Option **Ressource erstellen** > **Compute** > **Windows Server 2016 Datacenter**. Geben Sie die folgenden Werte für den virtuellen Computer ein:
    - **myVM1** als Name des virtuellen Computers        
    - **azureuser** als Name des Administratorbenutzers    
    - **myResourceGroupZLB** für **Ressourcengruppe** Wählen Sie **Vorhandene verwenden** und dann **myResourceGroupZLB**.
2. Klicken Sie auf **OK**.
3. Wählen Sie als Größe des virtuellen Computers **DS1_V2** aus. Wählen Sie **Auswählen**.
4. Geben Sie für die VM-Einstellungen folgende Werte ein:
    - **Zone 1** für die Verfügbarkeitszone, in der der virtuelle Computer platziert werden soll.
    -  **myVNet**: Vergewissern Sie sich, dass als virtuelles Netzwerk diese Option ausgewählt ist.
    - **myVM1PIP** für die öffentliche Standard-IP-Adresse, die Sie erstellen. Wählen Sie **Neu erstellen**. Wählen Sie als Namenstyp dann **myVM1PIP**. Wählen Sie für **Zone** die Option **1**. Als SKU der IP-Adresse ist standardmäßig die Option „Standard“ festgelegt.
    - **myBackendSubnet**: Stellen Sie sicher, dass diese Option als Subnetz ausgewählt ist.
    - **myNetworkSecurityGroup**: Name der Netzwerksicherheitsgruppen-Firewall, die bereits vorhanden ist.
5. Wählen Sie **Deaktiviert**, um die Startdiagnose zu deaktivieren.
6. Klicken Sie auf **OK**. Überprüfen Sie die Einstellungen auf der Seite mit der Zusammenfassung. Klicken Sie anschließend auf **Erstellen**.
7. Wiederholen Sie die Schritte 1 bis 6, um eine zweite VM mit dem Namen **myVM2** in Zone 1 zu erstellen. Legen Sie **myVnet** als virtuelles Netzwerk fest. Legen Sie **myVM2PIP** als öffentliche Standard-IP-Adresse fest. Legen Sie **myBackendSubnet** als Subnetz fest. Legen Sie zuletzt **myNetworkSecurityGroup** als Netzwerksicherheitsgruppe fest.

    ![Erstellen von virtuellen Computern](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Installieren von IIS auf VMs

1. Wählen Sie im Menü ganz links die Option **Alle Ressourcen**. Wählen Sie anschließend in der Ressourcenliste die Option **myVM1**. Sie befindet sich unter der Ressourcengruppe **myResourceGroupZLB**.
2. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden**, um per RDP auf die VM zuzugreifen.
3. Melden Sie sich an der VM mit dem Benutzernamen und Kennwort an, das Sie bei der Erstellung der VM angegeben haben. Zum Angeben dieser Anmeldeinformationen müssen Sie ggf. **Weitere Optionen** wählen. Wählen Sie anschließend die Option **Anderes Konto verwenden**. Wählen Sie dann **OK**. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wählen Sie **Ja** aus, um mit dem Herstellen der Verbindung fortzufahren.
4. Navigieren Sie auf dem Serverdesktop zu **Windows-Verwaltungsprogramme** > **Windows PowerShell**.
6. Führen Sie im **PowerShell**-Fenster die folgenden Befehle aus, um den IIS-Server zu installieren. Mit diesen Befehlen wird auch die Standarddatei „iisstart.htm“ entfernt und anschließend eine neue Version der Datei „iisstart.htm“ hinzugefügt, in der der Name der VM angezeigt wird:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Schließen Sie die RDP-Sitzung mit **myVM1**.
8. Wiederholen Sie die Schritte 1 bis 7 für die Installation von IIS auf **myVM2**.

## <a name="create-load-balancer-resources"></a>Erstellen von Load Balancer-Ressourcen

In diesem Abschnitt konfigurieren Sie die Load Balancer-Einstellungen für einen Back-End-Adresspool und einen Integritätstest. Außerdem geben Sie Regeln für den Lastenausgleich und die Netzwerkadressenübersetzung an.


### <a name="create-a-backend-address-pool"></a>Erstellen eines Back-End-Adresspools

Zum Verteilen von Datenverkehr auf die virtuellen Computer enthält ein Back-End-Adresspool die IP-Adressen der virtuellen Netzwerkschnittstellenkarten, die mit dem Load Balancer verbunden sind. Erstellen Sie den Back-End-Adresspool **myBackendPool** mit **VM1** und **VM2**.

1. Wählen Sie im Menü ganz links die Option **Alle Ressourcen**. Wählen Sie anschließend in der Ressourcenliste die Option **myLoadBalancer**.
2. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools**. Wählen Sie anschließend **Hinzufügen**.
3. Führen Sie auf der Seite **Back-End-Pool hinzufügen** die folgenden Aktionen durch:
    - Geben Sie unter „Name“ die Zeichenfolge **myBackEndPool** als Name für Ihren Back-End-Pool ein.
    - Wählen Sie für **Virtuelles Netzwerk** im Dropdownmenü die Option **myVNet**. 
    - Wählen Sie unter **Virtueller Computer** und **IP-Adresse** die Computer **myVM1** und **myVM2** und die zugehörigen öffentlichen IP-Adressen aus.
4. Wählen Sie **Hinzufügen**.
5. Vergewissern Sie sich, dass in der Back-End-Pool-Einstellung Ihres Lastenausgleichs beide virtuellen Computer angezeigt werden: **myVM1** und **myVM2**.
 
    ![Erstellen eines Back-End-Pools](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests

Verwenden Sie einen Integritätstest, damit der Lastenausgleich den Status Ihrer App überwachen kann. Abhängig von der Reaktion auf Integritätsüberprüfungen werden der Load Balancer-Rotation durch den Integritätstest dynamisch virtuelle Computer hinzugefügt oder daraus entfernt. Erstellen Sie zur Überwachung der Integrität der virtuellen Computer einen Integritätstest namens **myHealthProbe**.

1. Wählen Sie im Menü ganz links die Option **Alle Ressourcen**. Wählen Sie anschließend in der Ressourcenliste die Option **myLoadBalancer**.
2. Wählen Sie unter **Einstellungen** die Option **Integritätstests**. Wählen Sie anschließend **Hinzufügen**.
3. Verwenden Sie folgende Werte, um den Integritätstest zu erstellen:
    - **myHealthProbe** als Name des Integritätstests
    - **HTTP** als Protokolltyp
    - **80** als Portnummer
    - **15** als Wert für **Intervall** in Sekunden zwischen Testversuchen
    - **2** als Wert für **Fehlerschwellenwert** oder Anzahl aufeinander folgender Testfehler, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird
4. Klicken Sie auf **OK**.

   ![Hinzufügen eines Integritätstests](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mit einer Lastenausgleichsregel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt wird. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport. Erstellen Sie eine Lastenausgleichsregel mit dem Namen **myLoadBalancerRuleWeb** zum Lauschen über Port 80 des Front-Ends **FrontendLoadBalancer**. Mit der Regel wird Netzwerkdatenverkehr, für den ein Lastenausgleich durchgeführt wurde, an den Back-End-Adresspool **myBackEndPool** gesendet (ebenfalls über Port 80). 

1. Wählen Sie im Menü ganz links die Option **Alle Ressourcen**. Wählen Sie anschließend in der Ressourcenliste die Option **myLoadBalancer**.
2. Wählen Sie unter **Einstellungen** die Option **Lastenausgleichsregeln**. Wählen Sie anschließend **Hinzufügen**.
3. Konfigurieren Sie die Lastenausgleichsregel mit folgenden Werten:
    - **myHTTPRule** als Name der Lastenausgleichsregel
    - **TCP** als Protokolltyp
    - **80** als Portnummer
    - **80** als Back-End-Port
    - **myBackendPool** als Name des Back-End-Pools
    - **myHealthProbe** als Name des Integritätstests
4. Klicken Sie auf **OK**.
    
    ![Hinzufügen einer Lastenausgleichsregel](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs
1. Ermitteln Sie auf dem Bildschirm **Übersicht** die öffentliche IP-Adresse für den Load Balancer. Wählen Sie **Alle Ressourcen**. Wählen Sie anschließend **myPublicIP**. 

2. Kopieren Sie die öffentliche IP-Adresse. Fügen Sie sie in die Adressleiste des Browsers ein. Die Standardseite mit dem Namen der Webserverseite wird im Browser angezeigt.

      ![IIS-Webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Erzwingen Sie für die angezeigte VM die Beendigung, um das Lastenausgleichsmodul in Aktion zu sehen. Aktualisieren Sie den Browser, um zu sehen, wie der andere Servername im Browser angezeigt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Lastenausgleich und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie die Ressourcengruppe aus, die den Lastenausgleich enthält. Wählen Sie anschließend die Option **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Load Balancer Standard](load-balancer-standard-overview.md).
- [Durchführen eines verfügbarkeitszonenübergreifenden Lastenausgleichs für virtuelle Computer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
