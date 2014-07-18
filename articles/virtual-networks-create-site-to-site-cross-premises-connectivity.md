<properties linkid="manage-services-cross-premises-connectivity" urlDisplayName="Cross-premises Connectivity" pageTitle="Create a cross-premises virtual network - Azure" metaKeywords="" description="Learn how to create an Azure Virtual Network with cross-premises connectivity." metaCanonical="" services="virtual-network" documentationCenter="" title="Create a Virtual Network for Site-to-Site Cross-Premises Connectivity" authors="" solutions="" manager="" editor="" />

Erstellen eines virtuellen Netzwerks für standortübergreifende Verbindungen
===========================================================================

In diesem Lernprogramm werden Sie durch die Schritte zum Erstellen eines standortübergreifenden virtuellen Netzwerks geführt. Dabei handelt es sich um eine Standort-zu-Standort-Verbindung. Wenn Sie mit Zertifikaten und einem VPN-Client ein Punkt-zu-Standort-VPN erstellen möchten, finden Sie weitere Informationen unter [Konfigurieren eines Punkt-zu-Standort-VPNs mit dem Assistenten im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkId=296653).

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Es wurde entwickelt, um Ihnen die erforderlichen Schritte zum Erstellen eines virtuellen Standort-zu-Standort-Netzwerks nahezubringen. Wenn Sie nach Entwurfsszenarien und erweiterten Informationen zu Virtual Network suchen, finden Sie weitere Informationen unter [Überblick über virtuelle Azure-Netzwerke](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx).

Nach Abschluss dieses Lernprogramms verfügen Sie über ein virtuelles Netzwerk, in dem Sie Ihre Azure-Dienste und virtuellen Azure-Computer bereitstellen können. Diese können dann direkt mit dem Netzwerk Ihres Unternehmens kommunizieren.

Informationen zum Hinzufügen eines virtuellen Computers und Erweitern Ihres lokalen Active Directory auf Azure Virtual Network finden Sie unter:

-   [Erstellen eines benutzerdefinierten virtuellen Computers](http://go.microsoft.com/fwlink/?LinkID=294356)

-   [Installieren eines Active Directory-Replikatdomänencontrollers in Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=299877)

Richtlinien zum Bereitstellen von AD DS auf Azure Virtual Machines finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx).

Weitere Konfigurationsverfahren und -einstellungen für Virtual Network finden Sie unter [Konfigurationsaufgaben für virtuelle Azure-Netzwerke](http://go.microsoft.com/fwlink/?LinkId=296652).

Ziele
-----

In diesem Lernprogramm lernen Sie Folgendes:

-   Einrichten eines grundlegenden virtuellen Azure-Netzwerks, dem Azure-Dienste hinzugefügt werden können

-   Konfigurieren der Kommunikation mit dem Unternehmensnetzwerk im virtuellen Netzwerk

Voraussetzungen
---------------

-   Windows Live-Konto mit mindestens einem gültigen aktiven Abonnement.

-   Adressraum (in CIDR-Schreibweise) zur Verwendung durch das virtuelle Netzwerk und die Subnetze.

-   Name und IP-Adresse des DNS-Servers (wenn Sie den lokalen DNS-Server für die Namensauflösung verwenden möchten).

-   VPN-Gerät mit einer öffentlichen IPv4-Adresse. Sie benötigen die IP-Adresse, um den Assistenten abzuschließen. Das VPN-Gerät darf sich nicht hinter einer NAT befinden und muss den Mindestgerätestandards entsprechen. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://go.microsoft.com/fwlink/?LinkID=248098).

    Hinweis: Sie können RRAS als Teil der VPN-Lösung verwenden. In diesem Lernprogramm werden Sie jedoch nicht durch die RRAS-Konfigurationsschritte geführt.

    Informationen zur RRAS-Konfiguration finden Sie unter [Vorlagen für den Routing- und RAS-Dienst (RRAS)](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx).

-   Erfahrung mit der Routerkonfiguration oder eine Person, die Ihnen dabei behilflich sein kann.

-   Adressraum für Ihr lokales Netzwerk.

Allgemeine Schritte
-------------------

1.  [Erstellen eines virtuellen Netzwerks](#CreateVN)

2.  [Starten des Gateways und Sammeln von Informationen für den Netzwerkadministrator](#StartGateway)

3.  [Konfigurieren des VPN-Geräts](#ConfigVPN)

Erstellen eines virtuellen Netzwerks
------------------------------------

**So erstellen Sie ein virtuelles Netzwerk, das eine Verbindung mit dem Unternehmensnetzwerk herstellt**

1.  Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.

2.  Klicken Sie unten links auf dem Bildschirm auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerke** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Custom Create**, um den Konfigurationsassistenten zu starten.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.  Geben Sie auf dem Bildschirm **Details des virtuellen Netzwerks** die folgenden Informationen ein, und klicken Sie dann auf den Vorwärtspfeil unten rechts. Weitere Informationen zu den Einstellungen auf der Detailseite finden Sie im Abschnitt **Seite "Details des virtuellen Netzwerks"** unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=248092).

-   **NAME:** Name des virtuellen Netzwerks. Geben Sie *YourVirtualNetwork* ein.

-   **AFFINITÄTSGRUPPE:** Wählen Sie in der Dropdownliste **Neue Affinitätsgruppe erstellen** aus. Affinitätsgruppen stellen eine Möglichkeit dar, Azure-Dienste im selben Datencenter physisch zusammenzufassen, um die Leistung zu erhöhen. Einer Affinitätsgruppe kann nur ein einziges virtuelles Netzwerk zugewiesen werden.

-   **REGION:** Wählen Sie in der Dropdownliste die gewünschte Region aus. Das virtuelle Netzwerk wird in einem Datencenter erstellt, das sich in der angegebenen Region befindet.

-   **AFFINITÄTSGRUPPENNAME:** Benennen Sie die neue Affinitätsgruppe. Geben Sie *YourAffinityGroup* ein.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_02_VirtualNetworkDetails.png)

4.  Geben Sie auf der Seite **DNS Servers and VPN Connectivity** die folgenden Informationen ein, und klicken Sie dann auf den Vorwärtspfeil unten rechts.

    **Hinweis**

    Sie können auf dieser Seite sowohl Punkt-zu-Standort- als auch Standort-zu-Standort-Konfigurationen gleichzeitig auswählen. In diesem Lernprogramm beschränken wir uns auf die Konfiguration von Standort-zu-Standort-Konfigurationen. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt **Seite "DNS-Server und VPN-Konnektivität"** unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=248092).

-   **DNS-SERVER:** Geben Sie den Namen und die IP-Adresse des DNS-Servers ein, der für die Namensauflösung verwendet werden soll. Normalerweise ist dies ein DNS-Server, der für die lokale Namensauflösung verwendet wird. Mit dieser Einstellung wird kein DNS-Server erstellt. Geben Sie *YourDNS* als Namen und *10.1.4* als IP-Adresse ein.
-   **Configure Point-To-Site VPN:** Lassen Sie dieses Feld leer.
-   **Standort-zu-Standort-VPNs konfigurieren:** Aktivieren Sie dieses Kontrollkästchen.
-   **LOKALES NETZWERK:** Wählen Sie **Specify a New Local Network** in der Dropdownliste aus.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.  Geben Sie auf der Seite **Verbindungen zwischen Standorten** die nachstehenden Informationen ein, und klicken Sie dann auf das Häkchen unten rechts auf der Seite. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt **Seite "Standort-zu-Standort-Konnektivität"** unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=248092).

-   **NAME:** Geben Sie *YourCorpHQ* ein.

-   **VPN DEVICE IP ADDRESS:** Geben Sie die öffentliche IP-Adresse des VPN-Geräts ein. Wenn Sie diese Informationen nicht kennen, müssen Sie sie in Erfahrung bringen, bevor Sie mit den nächsten Schritten im Assistenten fortfahren. Beachten Sie, dass sich das VPN-Gerät nicht hinter einer NAT befinden darf. Weitere Informationen zu VPN-Geräten finden Sie unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://msdn.microsoft.com/en-us/library/windowsazure/jj156075.aspx).

-   **ADRESSRAUM:** Geben Sie *10.1.0.0/16* ein.
-   **Adressraum hinzufügen:** Dieses Lernprogramm erfordert keinen zusätzlichen Adressraum.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  Geben Sie auf der Seite **Virtual Network Address Spaces** die nachstehenden Informationen ein, und klicken Sie dann auf das Häkchen unten rechts, um das Netzwerk zu konfigurieren.

    Der Adressraum muss ein privater Adressbereich in der CIDR-Schreibweise 10.0.0.0/8, 172.16.0.0/12 oder 192.168.0.0/16 sein (wie von RFC 1918 vorgegeben). Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt **Seite "Virtuelle Netzwerkadressräume"** unter [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=248092).

-   **Adressraum:** Klicken Sie oben rechts auf **CIDR**, und geben Sie Folgendes ein:
    -   **Starting IP:** 10.4.0.0
    -   **CIDR:** /16
-   **Subnetz hinzufügen:** Geben Sie Folgendes ein:
    -   Benennen Sie **Subnet-1** in *FrontEndSubnet* mit der Start-IP *10.4.2.0/24* um, und klicken Sie dann auf **Subnetz hinzufügen**.
    -   Fügen Sie ein Subnetz mit dem Namen *BackEndSubnet* und der Start-IP *10.4.3.0/24* hinzu.
    -   Fügen Sie ein Subnetz mit dem Namen *ADDNSSubnet* und der Start-IP *10.4.4.0/24* hinzu.
    -   Fügen Sie ein Gatewaysubnetz mit der Start-IP **10.4.1.0/24** hinzu.
    -   Stellen Sie sicher, dass Sie drei Subnetze und ein Gatewaysubnetz erstellt haben, und klicken Sie dann auf das Häkchen unten rechts, um das virtuelle Netzwerk zu erstellen.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.  Nachdem Sie auf das Häkchen geklickt haben, wird die Erstellung des virtuellen Netzwerks gestartet. Sobald das virtuelle Netzwerk erstellt wurde, wird auf der Netzwerkseite im Verwaltungsportal als Status **Erstellt** angegeben.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

Starten des Gateways
--------------------

Gehen Sie nach der Erstellung des virtuellen Azure-Netzwerks wie im Folgenden beschrieben vor, um das Gateway des virtuellen Netzwerks zu konfigurieren und damit das Standort-zu-Standort-VPN zu erstellen. Für dieses Verfahren benötigen Sie ein VPN-Gerät, das die Mindestanforderungen erfüllt. Weitere Informationen zu VPN-Geräten und der Gerätekonfiguration finden Sie unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://go.microsoft.com/fwlink/?LinkID=248098).

**So starten Sie das Gateway**

1.  Nach der Erstellung des virtuellen Netzwerks wird auf der Seite **Netzwerke** als Status des virtuellen Netzwerks **Erstellt** angegeben.

    Klicken Sie in der Spalte **NAME** auf **YourVirtualNetwork**, um das Dashboard zu öffnen.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.  Klicken Sie oben auf der Seite auf **DASHBOARD**. Klicken Sie unten auf der Seite **Dashboard** auf **CREATE GATEWAY**. Wählen Sie als zu erstellenden Gatewaytypen entweder **Dynamic Routing** oder **Static Routing** aus.

    Beachten Sie, dass Sie dynamisches Routing als Gatewaytyp auswählen müssen, wenn Sie dieses virtuelle Netzwerk nicht nur für Standort-zu-Standort-Verbindungen, sondern auch für Punkt-zu-Standort-Verbindungen verwenden möchten. Stellen Sie vor der Erstellung des Gateways sicher, dass das VPN-Gerät den gewünschten Gatewaytypen unterstützt. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://go.microsoft.com/fwlink/?LinkID=248098). Wenn Sie dazu aufgefordert werden, die Erstellung des Gateways zu bestätigen, klicken Sie auf **JA**.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.  Wenn die Gatewayerstellung beginnt, wird eine Nachricht angezeigt, um Ihnen dies mitzuteilen.

    Es kann bis zu 15 Minuten dauern, bis das Gateway erstellt ist.

4.  Nach der Erstellung des Gateways müssen Sie die Informationen sammeln, die Sie zum Konfigurieren des VPN-Geräts benötigen.

-   Gateway-IP-Adresse
-   Gemeinsam verwendeter Schlüssel
-   Konfigurationsskriptvorlage des VPN-Geräts

    In den nächsten Schritten werden Sie durch diesen Vorgang geführt.

5.  Ermitteln der Gateway-IP-Adresse: Die Gateway-IP-Adresse befindet sich auf der Seite **DASHBOARD** des virtuellen Netzwerks.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.  Ermitteln des gemeinsam verwendeten Schlüssels: Der freigegebene Schlüssel befindet sich auf der Seite **DASHBOARD** des virtuellen Netzwerks. Klicken Sie unten auf dem Bildschirm auf **Manage Key**, und kopieren Sie dann den im Dialogfeld angezeigten Schlüssel.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.  Laden Sie die Konfigurationsskriptvorlage des VPN-Geräts herunter. Klicken Sie im Dashboard auf **Download VPN Device Script**.

8.  Wählen Sie im Dialogfeld **Download a VPN Device Configuration Script** den Anbieter, die Plattform und das Betriebssystem für das VPN-Gerät Ihres Unternehmens aus. Klicken Sie auf das Häkchen, und speichern Sie die Datei.

    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Wenn das VPN-Gerät nicht in der Dropdownliste angezeigt wird, finden Sie weitere Informationen unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://go.microsoft.com/fwlink/?LinkID=248098) in der MSDN-Bibliothek für weitere Skriptvorlagen.

Konfigurieren des VPN-Geräts (Netzwerkadministrator)
----------------------------------------------------

Da jedes VPN-Gerät anders ist, werden hier die allgemeinen Schritte beschrieben. Dieses Verfahren sollte vom Netzwerkadministrator ausgeführt werden.

Sie erhalten das VPN-Konfigurationsskript aus dem Verwaltungsportal oder unter [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://go.microsoft.com/fwlink/?LinkId=248098), wo auch die Routingtypen und die mit der ausgewählten Routingkonfiguration kompatiblen Geräte erklärt werden.

Weitere Informationen zum Konfigurieren eines virtuellen Netzwerkgateways finden Sie unter [Konfigurieren eines Gateways für ein virtuelles Netzwerk im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkId=299878) und in der Dokumentation des VPN-Geräts.

Bei diesem Verfahren werden folgende Punkte vorausgesetzt:

-   Die Person, die das VPN-Gerät konfiguriert, kennt sich mit der Konfiguration des ausgewählten Geräts aus. Aufgrund der Anzahl der Geräte, die mit dem virtuellen Netzwerk kompatibel sind, und der Konfigurationen, die für verschiedene Produktreihen spezifisch sind, enthalten die folgenden Schritte keine detaillierte Beschreibung der Gerätekonfiguration. Deshalb ist es wichtig, dass die Person, die das Gerät konfiguriert, mit dem Gerät und den Konfigurationseinstellungen vertraut ist.

-   Das ausgewählte Gerät ist mit dem virtuellen Netzwerk kompatibel. Überprüfen Sie die Gerätekompatibilität [hier](http://go.microsoft.com/fwlink/?LinkID=248098).

**So konfigurieren Sie das VPN-Gerät**

1.  Bearbeiten Sie das VPN-Konfigurationsskript. Konfigurieren Sie die folgenden Komponenten:

    a. Sicherheitsrichtlinien

    b. Eingehender Tunnel

    c. Ausgehender Tunnel

2.  Führen Sie das bearbeitete VPN-Konfigurationsskript aus, um das VPN-Gerät zu konfigurieren.

3.  Testen Sie die Verbindung, indem Sie einen der folgenden Befehle ausführen:

    <table border="1">
	  <tr>
		<th>-</th>
	    <th>Cisco ASA</th>
	    <th>Cisco ISR/ASR</th>
	    <th>Juniper SSG/ISG</th>
	    <th>Juniper SRX/J</th>
      </tr>
      <tr>
    	<td><strong>SAs im Hauptmodus prüfen</strong></td>
    	<td>show crypto isakmp sa</td>
	    <td>show crypto isakmp sa</td>
	    <td>get ike cookie</td>
	    <td>show security ike security-association</td>
	  </tr>
      <tr>    
		<td><strong>SAs im schnellen Modus prüfen</strong></td>
	    <td>show crypto ipsec sa</td>
	    <td>show crypto ipsec sa</td>
	    <td>get sa</td>
	    <td>show security ipsec security-association</td>
      </tr>
    </table>

Nächste Schritte
----------------

Führen Sie die folgenden Lernprogramme aus, um das lokale Active Directory auf das gerade erstellte virtuelle Netzwerk zu erweitern:

-   [Erstellen eines benutzerdefinierten virtuellen Computers](http://go.microsoft.com/fwlink/?LinkID=294356)

-   [Installieren eines Active Directory-Replikatdomänencontrollers in Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=299877)

Wenn Sie die Einstellungen des virtuellen Netzwerks in eine Netzwerkkonfigurationsdatei exportieren möchten, um die Konfiguration zu sichern oder als Vorlage zu verwenden, finden Sie weitere Informationen unter [Exportieren von Einstellungen virtueller Netzwerke in eine Netzwerkkonfigurationsdatei](http://go.microsoft.com/fwlink/?LinkID=299880).

Weitere Informationen
---------------------

-   [Virtuelles Azure-Netzwerk](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)

-   [FAQs zu virtuellen Netzwerken](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-   [Konfigurieren eines virtuellen Netzwerks mit Netzwerkkonfigurationsdateien](http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx)

-   [Hinzufügen eines virtuellen Computers zu einem virtuellen Netzwerk](http://www.windowsazure.com/en-us/manage/services/networking/add-a-vm-to-a-virtual-network/)

-   [Informationen zu VPN-Geräten für virtuelle Netzwerke](http://msdn.microsoft.com/en-us/library/windowsazure/jj156075.aspx)

-   [Übersicht über die Azure-Namensauflösung](http://go.microsoft.com/fwlink/?LinkId=248097)


