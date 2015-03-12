<properties 
	pageTitle="Lernprogramm: Erstellen eines virtuellen Netzwerks mit standortübergreifenden Verbindungen" 
	description="Erfahren Sie in diesem Lernprogramm, wie Sie ein virtuelles Azure-Netzwerk standortübergreifender Konnektivität erstellen."
	services="virtual-network" 
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
	ms.date="09/23/2014" 
	ms.author="cherylmc"/>





<h1 id="vnettut1">Lernprogramm: Erstellen eines virtuellen Netzwerks mit standortübergreifenden Verbindungen</h1>.

Das Lernprogramm führt Sie durch die Schritte zum Erstellen eines virtuellen Netzwerks mit standortübergreifenden Verbindungen. 

Wenn Sie ein virtuelles Nur-Cloud-Netzwerk erstellen möchten, finden Sie entsprechende Informationen unter [Lernprogramm: Erstellen eines virtuellen Nur-Cloud-Netzwerks in Azure](http://azure.microsoft.com/documentation/articles/create-virtual-network/). Wenn Sie mit Zertifikaten und einem VPN-Client ein Punkt-zu-Standort-VPN erstellen möchten, finden Sie weitere Informationen unter [Konfigurieren eines Punkt-zu-Standort-VPNs im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkId=296653).

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Es wurde entwickelt, um Ihnen die erforderlichen Schritte zum Erstellen eines standortübergreifenden virtuellen Beispielnetzwerks nahezubringen. Wenn Sie nach Entwurfsszenarien und erweiterten Informationen zu virtuellen Netzwerken suchen, finden Sie weitere Informationen unter [Virtuelle Netzwerke im Überblick](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).

Nach Abschluss dieses Lernprogramms verfügen Sie über ein standortübergreifendes virtuelles Beispielnetzwerk. In der folgenden Abbildung sehen Sie die Details basierend auf den Einstellungen in diesem Lernprogramm.

![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_12_TutorialCrossPremVNet.png)

Eine Kopie dieser Abbildung und eine Abbildung, die Sie zum Darstellen Ihres eigenen standortübergreifenden virtuellen Netzwerks nutzen können, finden Sie unter [Example cross-premises virtual network figure from tutorial topic](http://gallery.technet.microsoft.com/Example-cross-premises-e5ecb8bb) (in englischer Sprache).

Beachten Sie, dass die Konfigurationseinstellungen für dieses Lernprogramm nicht für Ihr Unternehmensnetzwerk angepasst sind. Wenn Sie das virtuelle Netzwerk und standortübergreifende Verbindungen mithilfe der in diesem Thema beschriebenen Konfigurationseintellungen konfigurieren, werden sie kein funktionsfähiges Netzwerk erhalten. Um ein funktionsfähiges standortübergreifendes, virtuelles Netzwerk zu konfigurieren, müssen Sie sich mit Ihrer IT-Abteilung und dem Netzwerkadministrator in Verbindung setzen, um die richtigen Einstellungen anzufordern. Weitere Informationen finden Sie im Abschnitt **Voraussetzungen** in diesem Thema.

Informationen zum Hinzufügen eines virtuellen Computers und Erweitern Ihres lokalen Active Directory auf Azure Virtual Network finden Sie unter:

-  [Erstellen eines benutzerdefinierten virtuellen Computers](http://go.microsoft.com/fwlink/?LinkID=294356)

-  [Installieren eines Active Directory-Replikatdomänencontrollers in Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=299877)

Richtlinien zum Bereitstellen von AD DS auf Azure Virtual Machines finden Sie unter [Richtlinien zum Bereitstellen von Windows Server Active Directory auf Azure Virtual Machines](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Weitere Konfigurationsverfahren und Einstellungen für virtuelle Netzwerke finden Sie unter [Aufgaben bei der Konfiguration virtueller Netzwerke](http://go.microsoft.com/fwlink/?LinkId=296652).

##  Ziele

In diesem Lernprogramm lernen Sie Folgendes:

-  Einrichten eines standortübergreifenden, virtuellen Azure-Netzwerks, dem Azure-Dienste hinzugefügt werden können

-  Konfigurieren der Kommunikation zwischen virtuellem Netzwerk und Unternehmensnetzwerk

##  Voraussetzungen

-  Microsoft-Konto mit mindestens einem gültigen, aktiven Azure-Abonnement.  Wenn Sie noch kein Azure-Abonnement besitzen, können Sie sich unter [Azure ausprobieren](http://www.windowsazure.com/pricing/free-trial/) für eine kostenlose Testversion anmelden. Falls Sie über ein MSDN-Abonnement verfügen, finden Sie weitere Informationen unter [Microsoft Azure-Sonderpreise: MSDN-, MPN- und Bizspark-Vorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Wenn Sie anhand dieses Lernprogramms ein funktionsfähiges, standortübergreifendes virtuelles Netzwerk konfigurieren, das auf Ihr Unternehmen abgestimmt ist, benötigen Sie:

-  Den privaten IPv4-Adressraum (in CIDR-Schreibweise) für das virtuelle Netzwerk und dessen Subnetze.

-  Den Namen und die IP-Adresse eines lokalen DNS-Servers

-  VPN-Gerät mit einer öffentlichen IPv4-Adresse. Sie benötigen die IP-Adresse, um den Assistenten abzuschließen. Das VPN-Gerät darf sich nicht hinter der Netzwerkadressübersetzung (Network Address Translator, NAT) befinden und muss den Mindestgerätestandards entsprechen. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://go.microsoft.com/fwlink/?LinkID=248098). 

Hinweis: Sie können den Routing- und RAS-Dienst (Routing and Remote Access Service, RRAS) in Windows Server als Teil Ihrer VPN-Lösung verwenden. In diesem Lernprogramm werden Sie jedoch nicht durch die RRAS-Konfigurationsschritte geführt. 
	Informationen zur RRAS-Konfiguration finden Sie unter [Vorlagen für den Routing- und RAS-Dienst (RRAS)](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx). 

-  Kenntnisse in der Konfiguration eines Routers für eine Verbindung im IPsec-Tunnelmodus bzw. fachliche Unterstützung für diesen Schritt

-  Die Adressräume (in CIDR-Schreibweise), die die erreichbaren Standorte Ihres lokalen Netzwerks zusammenfassen


## Allgemeine Schritte

1.	[Erstellen eines virtuellen Netzwerks](#CreateVN)

2.	[Starten des Gateways und Sammeln von Informationen für den Netzwerkadministrator](#StartGateway)

3.  [Konfigurieren des VPN-Geräts](#ConfigVPN)

##  <a name="CreateVN">Erstellen eines virtuellen Netzwerks</a>

So erstellen Sie ein virtuelles Netzwerk, das mit einem Unternehmensnetzwerk verbunden ist:

1.	Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.

2.	Klicken Sie in der unteren linken Ecke des Bildschirms auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerke** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Benutzerdefiniert erstellen**, um den Konfigurations-Assistenten zu starten. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.	Geben Sie auf der Seite **Details des virtuellen Netzwerks** die folgenden Informationen ein, und klicken Sie in der unteren rechten Ecke auf den Weiter-Pfeil. Weitere Informationen zu den Einstellungen auf der Detailseite finden Sie im Abschnitt **Details des virtuellen Netzwerks** unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=248092).

	-  **NAME:** Name des virtuellen Netzwerks. Geben Sie für das Beispiel in diesem Lernprogramm **YourVirtualNetwork** ein.

	-  **REGION:** Wählen Sie in der Dropdownliste die gewünschte Region aus. Das virtuelle Netzwerk wird in dem Azure-Rechenzentrum erstellt, das sich in der angegebenen Region befindet.

	
4.	Geben Sie auf der Seite **DNS-Server und VPN-Konnektivität** die folgenden Informationen ein, und klicken Sie dann auf den Vorwärtspfeil unten rechts. 

	<div class="dev-callout"> 
	<b>Hinweis</b> 
	<p>Sie können auf dieser Seite sowohl die <b>Punkt-zu-Standort</b>- als auch die <b>Standort-zu-Standort</b>-Konfiguration gleichzeitig auswählen. Im Rahmen dieses Themas beschränken wir uns auf die Konfiguration von <b>Standort-zu-Standort</b>. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie unter <b>DNS-Server und VPN-Konnektivität</b> in <a href="http://go.microsoft.com/fwlink/?LinkID=248092">Konfigurieren eines virtuellen Netzwerks mit dem Verwaltungsportal</a>.</p> 
	</div>

	-  **DNS-SERVER:** Geben Sie den Namen und die IP-Adresse des DNS-Servers ein, der für die Namensauflösung verwendet werden soll. Normalerweise ist dies ein DNS-Server, der für die lokale Namensauflösung verwendet wird. Mit dieser Einstellung wird kein DNS-Server erstellt. Geben Sie für das Beispiel in diesem Lernprogramm **YourDNS** als Namen und **10.1.0.4** als IP-Adresse ein.
	-  **Punkt-zu-Standort-VPN konfigurieren:** Lassen Sie dieses Feld leer. 
	-  **Standort-zu-Standort-VPNs konfigurieren:** Aktivieren Sie dieses Kontrollkästchen.
	-  **LOKALES NETZWERK:** Wählen Sie **Ein neues lokales Netzwerk angeben** in der Dropdownliste aus.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.	Geben Sie auf der Seite **Verbindungen zwischen Standorten** die nachstehenden Informationen ein, und klicken Sie dann auf das Häkchen unten rechts auf der Seite. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt **Seite "Standort-zu-Standort-Konnektivität"** unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=248092). 

	-  **NAME:** Geben Sie für das Beispiel in diesem Lernprogramm **YourCorpHQ** ein.

	-  **IP-ADRESSE DES VPN-GERÄTS:** Geben Sie für das Beispiel in diesem Lernprogramm **3.2.1.1** ein. Geben Sie andernfalls die öffentliche IP-Adresse des VPN-Geräts ein. Wenn Sie diese Informationen nicht kennen, müssen Sie sie in Erfahrung bringen, bevor Sie mit den nächsten Schritten im Assistenten fortfahren. Beachten Sie, dass sich das VPN-Gerät nicht hinter einer NAT befinden darf. Weitere Informationen zu VPN-Geräten finden Sie unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://msdn.microsoft.com/library/windowsazure/jj156075.aspx).

	-  **ADRESSRAUM:** Geben Sie für das Beispiel in diesem Lernprogramm **10.1.0.0/16** ein.
	-  **Adressraum hinzufügen:** Dieses Lernprogramm erfordert keinen zusätzlichen Adressraum.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  Geben Sie auf der Seite **Virtuelle Netzwerkadressräume** die nachstehenden Informationen ein, und klicken Sie dann auf das Häkchen unten rechts, um das Netzwerk zu konfigurieren. 

	Der Adressraum muss ein privater Adressbereich sein, der in der CIDR-Schreibweise 10.0.0.0/8, 172.16.0.0/12 oder 192.168.0.0/16 (wie von RFC 1918 festgelegt) angegeben wird. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt **Seite Virtuelle Netzwerkadressräume** unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=248092).

	-  **Adressraum:** Klicken Sie im Beispiel dieses Lernprogramms oben rechts auf **CIDR**, und geben Sie Folgendes ein:
		-  **Start-IP:** 10.4.0.0
		-  **CIDR:** /16
	-  **Subnetz hinzufügen:** Geben Sie im Beispiel dieses Lernprogramms Folgendes ein.
		- Benennen Sie **Subnet-1** in **FrontEndSubnet** mit der Start-IP **10.4.2.0/24** um.
		- Fügen Sie ein Subnetz mit dem Namen **BackEndSubnet** und der Start-IP **10.4.3.0/24** hinzu.
		- Fügen Sie ein Subnetz mit dem Namen **ADDNSSubnet** und der Start-IP **10.4.4.0/24** hinzu.
		- Fügen Sie ein Gateway-Subnetz mit der Start-IP **10.4.1.0/24** hinzu.
	-  Stellen Sie im Beispiel dieses Lernprogramms sicher, dass Sie drei Subnetze und ein Gatewaysubnetz erstellt haben, und klicken Sie dann auf das Häkchen unten rechts, um das virtuelle Netzwerk zu erstellen.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.	Nachdem Sie auf das Häkchen geklickt haben, wird das virtuelle Netzwerk erstellt. Wenn das virtuelle Netzwerk erstellt wurde, wird auf der Netzwerkseite im Verwaltungsportal unter **Status** der Eintrag **Erstellt** angezeigt. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

##  <a name="StartGateway">Starten des Gateways</a>

Gehen Sie nach der Erstellung des virtuellen Azure-Netzwerks wie im Folgenden beschrieben vor, um das Gateway des virtuellen Netzwerks zu konfigurieren und damit das Standort-zu-Standort-VPN zu erstellen. Für dieses Verfahren benötigen Sie ein VPN-Gerät, das die Mindestanforderungen erfüllt. Weitere Informationen zu VPN-Geräten und der Gerätekonfiguration finden Sie unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://go.microsoft.com/fwlink/?LinkID=248098).

**So starten Sie das Gateway**

1.	Nach der Erstellung des virtuellen Netzwerks wird auf der Seite **Netzwerke** als Status des virtuellen Netzwerks **Erstellt** angegeben.

	Klicken Sie (für das Beispiel in diesem Lernprogramm) in der Spalte **NAME** auf **YourVirtualNetwork**, um das Dashboard zu öffnen.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.	Klicken Sie oben auf der Seite auf **DASHBOARD**. Klicken Sie unten auf der Seite **Dashboard** auf **GATEWAY ERSTELLEN**. Wählen Sie als zu Gatewaytyp entweder **Dynamisches Routing** oder **Statisches Routing** aus. 

	Beachten Sie, dass Sie **Dynamisches Routing** als Gatewaytyp auswählen müssen, wenn Sie dieses virtuelle Netzwerk nicht nur für Standort-zu-Standort-Verbindungen, sondern auch für Punkt-zu-Standort-Verbindungen verwenden möchten. Stellen Sie vor der Erstellung des Gateways sicher, dass das VPN-Gerät den gewünschten Gatewaytypen unterstützt. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://go.microsoft.com/fwlink/?LinkID=248098). Wenn Sie dazu aufgefordert werden, die Erstellung des Gateways zu bestätigen, klicken Sie auf **JA**.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.	Wenn die Gatewayerstellung beginnt, wird eine Nachricht angezeigt, um Ihnen dies mitzuteilen.

	Es kann bis zu 15 Minuten dauern, bis das Gateway erstellt ist.

4.	Nach der Erstellung des Gateways müssen Sie die Informationen sammeln, die Sie zum Konfigurieren des VPN-Geräts benötigen. 

	-  Gateway-IP-Adresse
	-  Gemeinsam verwendeter Schlüssel
	-  Konfigurationsskriptvorlage des VPN-Geräts

	In den nächsten Schritten werden Sie durch diesen Vorgang geführt.

5.	So suchen Sie die Gateway-IP-Adresse: Die Gateway-IP-Adresse befindet sich auf der Seite **DASHBOARD** des virtuellen Netzwerks. Beispiel:

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.	So ermitteln Sie den gemeinsam verwendeten Schlüssel: Der gemeinsam verwendete Schlüssel befindet sich auf der Seite **DASHBOARD** des virtuellen Netzwerks. Klicken Sie unten auf dem Bildschirm auf **Schlüssel verwalten**, und kopieren Sie dann den im Dialogfeld angezeigten Schlüssel. Sie benötigen diesen Schlüssel, um den IPsec-Tunnel für das VPN-Gerät Ihres Unternehmens zu konfigurieren.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.	So laden Sie die Konfigurationsskriptvorlage des VPN-Geräts herunter Klicken Sie im Dashboard auf **VPN-Geräteskript herunterladen**.

8.	Wählen Sie im Dialogfeld **Konfigurationsskript für ein VPN-Gerät herunterladen** den Anbieter, die Plattform und das Betriebssystem für das VPN-Gerät Ihres Unternehmens aus. Klicken Sie auf das Häkchen, und speichern Sie die Datei. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Wenn das VPN-Gerät nicht in der Dropdownliste angezeigt wird, finden Sie weitere Informationen unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://go.microsoft.com/fwlink/?LinkID=248098) in der MSDN-Bibliothek für weitere Skriptvorlagen.


##  <a name="ConfigVPN">Konfigurieren des VPN-Geräts (Netzwerkadministrator)</a>

Da jedes VPN-Gerät anders ist, werden hier die allgemeinen Schritte beschrieben. Dieses Verfahren sollte vom Netzwerkadministrator ausgeführt werden.

Sie erhalten das VPN-Konfigurationsskript aus dem Verwaltungsportal oder unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://go.microsoft.com/fwlink/?LinkId=248098), wo auch die Routingtypen und die mit der ausgewählten Routingkonfiguration kompatiblen Geräte erklärt werden.

Weitere Informationen zum Konfigurieren eines virtuellen Netzwerkgateways finden Sie unter [Konfigurieren eines Gateways für ein virtuelles Netzwerk im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkId=299878) und in der Dokumentation des VPN-Geräts.

Bei diesem Verfahren werden folgende Punkte vorausgesetzt:

-  Die Person, die das VPN-Gerät konfiguriert, kennt sich mit der Konfiguration des ausgewählten Geräts aus. Aufgrund der Anzahl der Geräte, die mit dem virtuellen Netzwerk kompatibel sind, und der Konfigurationen, die für verschiedene Produktreihen spezifisch sind, enthalten die folgenden Schritte keine detaillierte Beschreibung der Gerätekonfiguration. Deshalb ist es wichtig, dass die Person, die das Gerät konfiguriert, mit dem Gerät und den Konfigurationseinstellungen vertraut ist. 

-  Das ausgewählte Gerät ist mit dem virtuellen Netzwerk kompatibel. Überprüfen Sie die Gerätekompatibilität [hier](http://go.microsoft.com/fwlink/?LinkID=248098).


**So konfigurieren Sie das VPN-Gerät**

1.	Bearbeiten Sie das VPN-Konfigurationsskript. Konfigurieren Sie die folgenden Komponenten:

	a.	Sicherheitsrichtlinien

	b.	Eingehender Tunnel

	c.	Ausgehender Tunnel

2.	Führen Sie das bearbeitete VPN-Konfigurationsskript aus, um das VPN-Gerät zu konfigurieren.

3.	Testen Sie die Verbindung, indem Sie einen der folgenden Befehle ausführen:

	<table border="1">
	<tr>
	<th>-</th>
	<th>Cisco ASA</th>
	<th>Cisco ISR/ASR</th>
	<th>Juniper SSG/ISG</th>
	<th>Juniper SRX/J</th>
	</tr>
	
	<tr>
	<td><b>SAs im Hauptmodus prüfen</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get ike cookie</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ike security-association</FONT></td>
	</tr>
	
	<tr>
	<td><b>SAs im schnellen Modus prüfen</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ipsec security-association</FONT></td>
	</tr>
	</table>


##  Nächste Schritte
Fahren Sie mit den folgenden Lernprogrammen fort, um das lokale Active Directory auf das gerade erstellte virtuelle Netzwerk zu erweitern:

-  [Erstellen eines benutzerdefinierten virtuellen Computers](http://go.microsoft.com/fwlink/?LinkID=294356)

-  [Installieren eines Active Directory-Replikatdomänencontrollers in Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=299877)

Wenn Sie die Einstellungen des virtuellen Netzwerks in eine Netzwerkkonfigurationsdatei exportieren möchten, um die Konfiguration zu sichern oder als Vorlage zu verwenden, finden Sie weitere Informationen unter [Exportieren von Einstellungen virtueller Netzwerke in eine Netzwerkkonfigurationsdatei](http://go.microsoft.com/fwlink/?LinkID=299880).

## Weitere Informationen

-  [Azure Virtual Network](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [FAQs zu virtuellen Netzwerken](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Konfigurieren eines virtuellen Netzwerks mit Netzwerkkonfigurationsdateien](http://msdn.microsoft.com/library/windowsazure/jj156097.aspx)

-  [Hinzufügen eines virtuellen Computers zu einem virtuellen Netzwerk](http://azure.microsoft.com/manage/services/networking/add-a-vm-to-a-virtual-network/)

-  [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://msdn.microsoft.com/library/windowsazure/jj156075.aspx)

-  [Überblick über die Azure-Namensauflösung](http://go.microsoft.com/fwlink/?LinkId=248097)






<!--HONumber=46--> 
