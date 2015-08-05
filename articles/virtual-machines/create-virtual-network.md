<properties 
	pageTitle="Lernprogramm: Erstellen eines virtuellen Netzwerks ausschließlich für die Cloud" 
	description="Erfahren Sie in diesem Lernprogramm, wie Sie ein beispielhaftes, komplett auf der Cloud basierendes virtuelles Azure-Netzwerk erstellen." 
	services="virtual-machines, virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="cherylmc"/>

# Lernprogramm: Erstellen eines virtuellen Netzwerks nur für die Cloud in Azure

Dieses Lernprogramm führt Sie durch die Schritte im Azure-Verwaltungsportal zum Erstellen eines virtuellen Azure-Beispielnetzwerks, das ausschließlich für die Cloud gedacht ist und zwei Subnetze enthält. Das resultierende virtuelle Netzwerk sieht wie folgt aus:

![Virtuelles Netzwerk erstellen](./media/create-virtual-network/createVNet_06_VNetExample.png)

Das FrontEndSubnetz könnte beispielsweise für Webserver und das BackEndSubnetz für SQL Server oder Domänencontroller verwendet werden.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Ziel ist es, Sie mit den Schritten zum Erstellen eines eigenen virtuellen Netzwerks vertraut zu machen, indem Sie durch eine Beispielkonfiguration geführt werden. Wenn Sie ein virtuelles Nur-Cloud-Netzwerk erstellen möchten, das für Ihre spezielle Konfiguration geeignet ist, konsultieren Sie den Artikel [Konfigurieren eines virtuellen Nur-Cloud-Netzwerks im Verwaltungsportal](http://msdn.microsoft.com/library/azure/dn631643.aspx). Designszenarios und weiterführende Informationen zu Virtual Network finden Sie unter [Überblick über Azure Virtual Network](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).


> [AZURE.NOTE]Dieses Lernprogramm zeigt Ihnen nicht den Erstellungsprozess für eine standortübergreifende Konfiguration, in der das virtuelle Netzwerk mit Ihrem Unternehmensnetzwerk verbunden ist. Ein Lernprogramm für das Erstellen einer virtuellen Netzwerks mit standortübergreifender Konnektivität und einer VPN-Verbindung zwischen Standorten (z. B. Verbindung mit Active Directory oder SharePoint in Ihrem Unternehmen) finden Sie unter [Erstellen eines virtuellen Netzwerks für standortübergreifende Verbindungen](../virtual-network/virtual-networks-create-site-to-site-cross-premises-connectivity.md).


##  Ziele

In diesem Lernprogramm erfahren Sie, wie Sie ein grundlegendes virtuelles Nur-Cloud-Netzwerk mit zwei Subnetzen in Azure einrichten.

##  Voraussetzungen

*  Microsoft-Konto mit mindestens einem gültigen, aktiven Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie sich unter [Azure ausprobieren](http://azure.microsoft.com/pricing/free-trial/) für eine kostenlose Testversion registrieren. Wenn Sie über ein MSDN-Abonnement verfügen, finden Sie weitere Informationen unter [Microsoft Azure-Sonderpreise: MSDN-, MPN- und Bizspark-Vorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

##  Erstellen des virtuellen Netzwerks für dieses Lernprogramm

Gehen Sie zum Erstellen dieses virtuellen Nur-Cloud-Beispielnetzwerks wie folgt vor:

1. Melden Sie sich beim Verwaltungsportal an.

2. Klicken Sie in der unteren linken Ecke des Bildschirms auf **Neu** > **Netzwerkdienste** > **Virtuelles Netzwerk** und dann auf **Benutzerdefiniert erstellen**, um den Konfigurationsassistenten zu starten.

	![][Image1]

3. Geben Sie auf der Seite **Details des virtuellen Netzwerks** die folgenden Informationen ein:

- **Name -** Geben Sie **EigenesVirtuellesNetzwerk** ein.

- **Region -** Ihr virtuelles Netzwerk wird in einem Datencenter in der angegebenen Region erstellt. Um die beste Leistung zu erzielen, wählen Sie die Region, in der Sie sich befinden, aus der Dropdown-Liste aus.
 

	![][Image2]

4. Klicken Sie auf den Pfeil für "Weiter" unten rechts. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt "Details des virtuellen Netzwerks" unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).

5. Klicken Sie auf der Seite **DNS-Server und VPN-Konnektivität** auf den Pfeil für "Weiter" unten rechts. Azure weist neuen virtuellen Computern, die diesem virtuellen Netzwerk hinzugefügt werden, einen internetbasierten Azure-DNS-Server zu, sodass diese auf Internetressourcen zugreifen können. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt "DNS-Server und VPN-Konnektivität" unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).
	
6.	Genau wie ein echtes Netzwerk benötigt das virtuelle Netzwerk einen Bereich von IP-Adressen (Adressraum genannt) für die Zuweisung zu virtuellen Computern innerhalb dieses Netzwerks. Das virtuelle Netzwerk unterstützt auch Subnetze, die ihre eigenen Adressräume benötigen, abgeleitet aus dem Adressraum des virtuellen Netzwerks. In diesem Tutorial erstellen Sie das BackEndSubnetz und das FrontEndSubnetz. Konfigurieren Sie auf der Seite **Virtuelle Netzwerkadressräume** Folgendes:

	- Wählen Sie für "Adressraum" **/16 (65535)** in **CIDR (Anzahl Adressen)** aus.

	- Überschreiben Sie unter "Subnetze" in der ersten Zeile den vorhandenen Namen mit **BackEndSubnetz**, und geben Sie als Start-IP **10.0.1.0** ein. Wählen Sie dann **/24 (256)** in **CIDR (Anzahl Adressen)** aus. Klicken Sie auf **Subnetz hinzufügen**, und geben Sie dann **FrontEndSubnetz** als Namen und **10.0.2.0** für die Start-IP ein.
		
	![][Image4]

 Im Diagramm des virtuellen Netzwerks sehen Sie jetzt, dass folgende Adressräume konfiguriert wurden:
 
	
- FrontEndSubnet: 10.0.2.0/24
- BackEndSubnet: 10.0.1.0/24

 Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt "Virtuelle Netzwerkadressräume" unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).


7. Klicken Sie auf das Häkchen unten rechts auf der Seite. Das virtuelle Netzwerk wird daraufhin erstellt. Wenn die Erstellung des virtuellen Netzwerks abgeschlossen ist, wird auf der Seite mit Netzwerken im Azure-Verwaltungsportal unter **Status** der Eintrag **Erstellt** angezeigt.  

	![][Image5]

Weitere Informationen zu Azure-Infrastrukturdiensten finden Sie in folgenden Themen:

- [Erstellen eines benutzerdefinierten virtuellen Computers](virtual-machines-create-custom.md) Gehen Sie beim Erstellen eines virtuellen Computers in Ihrem virtuellen Netzwerk anhand dieses Themas vor. Weitere Informationen zu virtuellen Computern und Installationsoptionen finden Sie unter [Azure Virtual Machines](http://azure.microsoft.com/documentation/services/virtual-machines/).

- [Installieren einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk](../active-directory-new-forest-virtual-machine.md) - Verwenden Sie dieses Thema, um eine neue Windows Server Active Directory-Gesamtstruktur ohne Verbindung zu einem anderen Netzwerk zu installieren. Das Lernprogramm erläutert die spezifischen Schritte, die zum Erstellen eines virtuellen Computers für eine neue Gesamtstrukturinstallation erforderlich sind. Wenn Sie dieses Lernprogramm verwenden möchten, erstellen Sie keine virtuellen Computer mit dem Verwaltungsportal. Weitere Informationen finden Sie unter [Richtlinien zum Bereitstellen von Windows Server Active Directory auf Azure Virtual Machines](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Um dieses virtuelle Netzwerk zu entfernen, wählen Sie es aus, klicken auf **Löschen** und dann auf **Ja**.

Wenn Sie bereit sind, ein virtuelles Nur-Cloud-Netzwerk zu erstellen, das für Ihre spezielle Konfiguration geeignet ist, konsultieren Sie den Artikel [Konfigurieren eines virtuellen Nur-Cloud-Netzwerks im Verwaltungsportal](http://msdn.microsoft.com/library/azure/dn631643.aspx).

Designszenarios und weiterführende Informationen zu Virtual Network finden Sie unter [Überblick über Azure Virtual Network](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).

Weitere Virtual Network-Konfigurationsverfahren und -einstellungen finden Sie unter [Azure Virtual Network-Konfigurationsaufgaben](http://go.microsoft.com/fwlink/p/?linkid=296652&clcid=0x409).


## Siehe auch

-  [Azure Virtual Network – häufig gestellte Fragen](http://go.microsoft.com/fwlink/p/?LinkId=296650)

-  [Azure Virtual Network – Konfigurationsaufgaben](http://go.microsoft.com/fwlink/p/?LinkId=296652)

-  [Konfigurieren eines virtuellen Netzwerks mit Netzwerkkonfigurationsdateien](../virtual-network/virtual-networks-using-network-configuration-file.md)

-  [Namensauflösung für virtuelle Computer und Rolleninstanzen](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png

 

<!---HONumber=July15_HO4-->