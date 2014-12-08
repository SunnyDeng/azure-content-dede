<properties urlDisplayName="Tutorial: Create a cloud-only virtual network" pageTitle="Lernprogramm: Erstellen eines virtuellen Nur-Cloud-Netzwerks" metaKeywords="" description="Learn how to create an example cloud-only Azure Virtual Network in this tutorial." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Tutorial: Create a Clound-only Virtual Network in Azure" authors="cherylmc" solutions="" manager="adinah" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/29/2014" ms.author="cherylmc" />

<h1 id="vnettut1">Lernprogramm: Erstellen eines virtuellen Nur-Cloud-Netzwerks in Azure</h1>

Dieses Lernprogramm führt Sie durch die Schritte im Azure-Verwaltungsportal zum Erstellen eines virtuellen Azure-Beispielnetzwerks, das ausschließlich für die Cloud gedacht ist und zwei Subnetze enthält. Das resultierende virtuelle Netzwerk sieht wie folgt aus:

![createvnet](./media/create-virtual-network/createVNet_06_VNetExample.png)

Das FrontEndSubnetz könnte beispielsweise für Webserver und das BackEndSubnetz für SQL Server oder Domänencontroller verwendet werden.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Ziel ist es, Sie mit den Schritten zum Erstellen eines eigenen virtuellen Netzwerks vertraut zu machen, indem Sie durch eine Beispielkonfiguration geführt werden. Wenn Sie ein virtuelles Nur-Cloud-Netzwerk erstellen möchten, das für Ihre spezielle Konfiguration geeignet ist, konsultieren Sie den Artikel [Konfigurieren eines virtuellen Nur-Cloud-Netzwerks im Verwaltungsportal](http://msdn.microsoft.com/library/azure/dn631643.aspx). Designszenarios und weiterführende Informationen zu Virtual Network finden Sie unter [Überblick über Azure Virtual Network](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).


<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Dieses Lernprogramm zeigt Ihnen nicht den Erstellungsprozess für eine standortübergreifende Konfiguration, in der das virtuelle Netzwerk mit Ihrem Unternehmensnetzwerk verbunden ist. Ein Lernprogramm für das Erstellen eines virtuellen Netzwerks mit standortübergreifender Konnektivität und VPN-Verbindungen zwischen Standorten (z. B. Verbindung mit Active Directory oder SharePoint in Ihrem Unternehmen) finden Sie unter <a href="/de-de/manage/services/networking/cross-premises-connectivity/">Lernprogramm: Erstellen eines virtuellen Netzwerks mit standortübergreifenden Verbindungen</a>.</p> 
</div>


##  Ziele

In diesem Lernprogramm erfahren Sie, wie Sie ein grundlegendes virtuelles Nur-Cloud-Netzwerk mit zwei Subnetzen in Azure einrichten.

##  Voraussetzungen

*  Microsoft-Konto mit mindestens einem gültigen, aktiven Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie sich unter [Azure ausprobieren](http://www.windowsazure.com/pricing/free-trial/) für eine kostenlose Testversion anmelden. Falls Sie über ein MSDN-Abonnement verfügen, finden Sie weitere Informationen unter [Microsoft Azure-Sonderpreise: MSDN-, MPN- und Bizspark-Vorteile](http://azure.microsoft.com/de-de/pricing/member-offers/msdn-benefits-details/).

##  Erstellen des virtuellen Netzwerks für dieses Lernprogramm

Gehen Sie zum Erstellen dieses virtuellen Nur-Cloud-Beispielnetzwerks wie folgt vor:

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.

2. Klicken Sie in der unteren linken Ecke des Bildschirms auf **Neu** > **Netzwerkdienste** > **Virtuelles Netzwerk** und dann auf **Benutzerdefiniert erstellen**, um den Konfigurationsassistenten zu starten.

	![][Image1]

3. Geben Sie auf der Seite **Details des virtuellen Netzwerks** die folgenden Informationen ein:

- **Name -** Geben Sie **EigenesVirtuellesNetzwerk** ein.

- **Region -** Ihr virtuelles Netzwerk wird in einem Datencenter in der angegebenen Region erstellt. Um die beste Leistung zu erzielen, wählen Sie die Region, in der Sie sich befinden, aus der Dropdown-Liste aus.
 

	![][Image2]

4. Klicken Sie auf den Pfeil für "Weiter" unten rechts. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt "Details des virtuellen Netzwerks" unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409).

5. Klicken Sie auf der Seite **DNS-Server und VPN-Konnektivität** auf den Pfeil für "Weiter" unten rechts. Azure weist neuen virtuellen Computern, die diesem virtuellen Netzwerk hinzugefügt werden, einen internetbasierten Azure-DNS-Server zu, sodass diese auf Internetressourcen zugreifen können. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt "DNS-Server und VPN-Konnektivität" unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409).
	
6.	Genau wie ein echtes Netzwerk benötigt das virtuelle Netzwerk einen Bereich von IP-Adressen (Adressraum genannt) für die Zuweisung zu virtuellen Computern innerhalb dieses Netzwerks. Das virtuelle Netzwerk unterstützt auch Subnetze, die ihre eigenen Adressräume benötigen, abgeleitet aus dem Adressraum des virtuellen Netzwerks. In diesem Tutorial erstellen Sie das BackEndSubnetz und das FrontEndSubnetz. Konfigurieren Sie auf der Seite **Virtuelle Netzwerkadressräume** Folgendes:

	- Wählen Sie für "Adressraum" **/16 (65535)** in **CIDR (Anzahl Adressen)** aus.

	- Überschreiben Sie unter "Subnetze" in der ersten Zeile den vorhandenen Namen mit **BackEndSubnetz**, und geben Sie als Start-IP **10.0.1.0** ein. Wählen Sie dann **/24 (256)** in **CIDR (Anzahl Adressen)** aus. Klicken Sie auf **Subnetz hinzufügen**, und geben Sie dann **FrontEndSubnetz** als Namen und **10.0.2.0** für die Start-IP ein.
		
	![][Image4]

 Im Diagramm des virtuellen Netzwerks sehen Sie jetzt, dass folgende Adressräume konfiguriert wurden:
 
	![][Image7]

Beachten Sie, dass der Adressraum für ein virtuelles Netzwerk aus den privaten Adressräumen 10.0.0.0/8, 172.16.0.0/12 oder 192.168.0.0/16 stammen muss und in der CIDR-Notation (Classless Inter-Domain Routing), die auch Netzwerkpräfix-Notation genannt wird, anzugeben ist. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt "Virtuelle Netzwerkadressräume" unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409).


7. Klicken Sie auf das Häkchen unten rechts auf der Seite. Das virtuelle Netzwerk wird daraufhin erstellt. Wenn die Erstellung des virtuellen Netzwerks abgeschlossen ist, wird auf der Seite mit Netzwerken im Azure-Verwaltungsportal unter **Status** der Eintrag **Erstellt** angezeigt.  

	![][Image5]

Weitere Informationen zu Azure-Infrastrukturdiensten finden Sie in folgenden Themen:

- [Erstellen eines benutzerdefinierten virtuellen Computers](http://www.windowsazure.com/de-de/manage/windows/how-to-guides/custom-create-a-vm/) Gehen Sie beim Erstellen eines virtuellen Computers in Ihrem virtuellen Netzwerk anhand dieses Themas vor. Weitere Informationen zu virtuellen Computern und Installationsoptionen finden Sie unter [Azure Virtual Machines](http://www.windowsazure.com/de-de/manage/windows/).

- [Installieren einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk](http://www.windowsazure.com/de-de/manage/services/networking/active-directory-forest/) - Verwenden Sie dieses Thema, um eine neue Windows Server Active Directory-Gesamtstruktur ohne Verbindung zu einem anderen Netzwerk zu installieren. Das Lernprogramm erläutert die spezifischen Schritte, die zum Erstellen eines virtuellen Computers für eine neue Gesamtstrukturinstallation erforderlich sind. Wenn Sie dieses Lernprogramm verwenden möchten, erstellen Sie keine virtuellen Computer mit dem Verwaltungsportal. Weitere Informationen finden Sie unter [Richtlinien zum Bereitstellen von Windows Server Active Directory auf Azure Virtual Machines](http://msdn.microsoft.com/de-de/library/windowsazure/jj156090.aspx).

Um dieses virtuelle Netzwerk zu entfernen, wählen Sie es aus, klicken auf **Löschen** und dann auf **Ja**.

Wenn Sie bereit sind, ein virtuelles Nur-Cloud-Netzwerk zu erstellen, das für Ihre spezielle Konfiguration geeignet ist, konsultieren Sie den Artikel [Konfigurieren eines virtuellen Nur-Cloud-Netzwerks im Verwaltungsportal](http://msdn.microsoft.com/library/azure/dn631643.aspx). 

Designszenarios und weiterführende Informationen zu Virtual Network finden Sie unter [Überblick über Azure Virtual Network](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx). 

Weitere Virtual Network-Konfigurationsverfahren und -einstellungen finden Sie unter [Azure Virtual Network-Konfigurationsaufgaben](http://go.microsoft.com/fwlink/?linkid=296652&clcid=0x409).


## Weitere Informationen

-  [Häufig gestellte Fragen über Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=296650)

-  [Azure Virtual Network-Konfigurationsaufgaben](http://go.microsoft.com/fwlink/?LinkId=296652)

-  [Konfigurieren eines virtuellen Netzwerks mit Netzwerkkonfigurationsdateien](http://msdn.microsoft.com/de-de/library/windowsazure/jj156097.aspx)

-  [Überblick über die Azure-Namensauflösung](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png

