<properties linkid="manage-services-create-a-virtual-network" urlDisplayName="Create a virtual network" pageTitle="Create a virtual network - Azure service management" metaKeywords="" description="Learn how to create an Azure Virtual Network." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Create a Virtual Network in Azure" authors="" solutions="" manager="" editor="" />

Erstellen eines virtuellen Netzwerks in Azure
=============================================

Dieses Lernprogramm führt Sie durch die Schritte zum Erstellen eines allgemeinen virtuellen Azure-Netzwerks mit dem Azure-Verwaltungsportal. Weitere Informationen zu Azure Virtual Network finden Sie unter [Überblick über Azure Virtual Network](http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx).

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Ziel ist es, Sie mit den Schritten zum Erstellen eines virtuellen Netzwerks vertraut zu machen. Designszenarios und weiterführende Informationen zu Virtual Network finden Sie unter [Überblick über Azure Virtual Network](http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx).

Nach Abschluss dieses Lernprogramms verfügen Sie über ein virtuelles Netzwerk, in dem Sie Ihre Azure-Dienste und virtuellen Computer bereitstellen können.

**Hinweis**

Dieses Lernprogramm zeigt Ihnen nicht den Erstellungsprozess bei einer standortübergreifenden Konfiguration. Ein Lernprogramm für das Erstellen einer virtuellen Netzwerks mit einer standortübergreifenden Verbindung zwischen Standorten (z. B. Verbindung mit Active Directory oder SharePoint in Ihrem Unternehmen) finden Sie unter [Erstellen eines virtuellen Netzwerks für standortübergreifende Verbindungen](/de-de/manage/services/networking/cross-premises-connectivity/).

Weitere Virtual Network-Konfigurationsverfahren und -einstellungen finden Sie unter [Azure Virtual Network-Konfigurationsaufgaben](http://go.microsoft.com/fwlink/?LinkId=296652).

Richtlinien zum Bereitstellen von AD DS auf Azure Virtual Machines finden Sie unter [Richtlinien zum Bereitstellen von Windows Server Active Directory auf Azure Virtual Machines](http://msdn.microsoft.com/de-de/library/windowsazure/jj156090.aspx).

Ziele
-----

In diesem Lernprogramm lernen Sie Folgendes:

-   Einrichten eines allgemeinen virtuellen Azure-Netzwerks, dem Sie Azure Cloud Services und virtuelle Computer hinzufügen können

Voraussetzungen
---------------

-   Windows Live-Konto mit mindestens einem gültigen, aktiven Abonnement

Erstellen eines virtuellen Netzwerks
------------------------------------

**So erstellen Sie ein virtuelles Netzwerk ausschließlich für die Cloud:**

1.  Melden Sie sich beim [Windows Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.

2.  Klicken Sie in der unteren linken Ecke des Bildschirms auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerke** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Custom Create**, um den Konfigurationsassistenten zu starten.

    ![](./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png)

3.  Geben Sie auf der Seite **Details des virtuellen Netzwerks** die folgenden Informationen ein, und klicken Sie in der unteren rechten Ecke auf den Weiter-Pfeil. Weitere Informationen zu den Einstellungen auf der Seite mit Details finden Sie im Abschnitt zur Seite **Details des virtuellen Netzwerks** unter [Konfigurieren eines virtuellen Netzwerks mit dem Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=248092).

-   **Name:** Name des virtuellen Netzwerks. Geben Sie *EigenesVirtuellesNetzwerk* ein.

-   **Affinitätsgruppe:** Wählen Sie aus der Dropdown-Liste **Neue Affinitätsgruppe erstellen** aus. Affinitätsgruppen stellen eine Methode dar, um Azure-Dienste physisch miteinander im gleichen Datencenter zu gruppieren, um die Leistung zu steigern. Einer Affinitätsgruppe kann nur ein virtuelles Netzwerk zugewiesen sein.

-   **Region:** Wählen Sie aus der Dropdown-Liste die gewünschte Region aus. Ihr virtuelles Netzwerk wird in einem Datencenter in der angegebenen Region erstellt.

-   **Affinitätsgruppenname:** Name der neuen Affinitätsgruppe. Geben Sie *EigeneAffinitätsgruppe* ein.

    ![](./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png)

1.  Geben Sie auf der Seite **DNS Servers and VPN Connectivity** die folgenden Informationen ein, und klicken Sie dann in der unteren rechten Ecke auf den Weiter-Pfeil. Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt zur Seite **DNS Servers and VPN Connectivity** unter [Konfigurieren eines virtuellen Netzwerks mit dem Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=248092).

    -   **DNS-Server (optional):** Geben Sie den DNS-Servernamen und die IP-Adresse ein, die Sie verwenden möchten. Mit dieser Einstellung wird kein DNS-Server erstellt, sie verweist vielmehr auf einen bereits existierenden DNS-Server.

        **Hinweis**

        Wenn Sie einen öffentlichen DNS-Dienst verwenden möchten, können Sie diese Informationen in diesem Bildschirm eingeben. Anderenfalls wird die Namensauflösung auf den Standardwert des Azure-Dienstes gesetzt. Weitere Informationen finden Sie unter [Überblick über die Azure-Namensauflösung](http://go.microsoft.com/fwlink/?linkid=248097).

    -   **Aktivieren Sie nicht das Kontrollkästchen für eine Verbindung zwischen Punkt und Standort oder zwischen Standorten**. Das virtuelle Netzwerk, das wir in diesem Lernprogramm erstellen, ist nicht auf eine standortübergreifende Verbindung ausgelegt.

    ![](./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png)

2.  Geben Sie auf der Seite **Virtual Network Address Spaces** die folgenden Informationen ein, und klicken Sie dann in der unteren rechten Ecke auf das Häkchen, um das Netzwerk zu konfigurieren. Der Adressraum muss ein privater Adressbereich sein, angegeben in der CIDR-Notation 10.0.0.0/8, 172.16.0.0/12 oder 192.168.0.0/16 (wie im RFC 1918 angegeben). Weitere Informationen zu den Einstellungen auf dieser Seite finden Sie im Abschnitt zur Seite **Virtual Network Address Spaces** unter [Konfigurieren eines virtuellen Netzwerks mit dem Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=248092).

    -   **Adressraum:** Klicken Sie in der oberen rechten Ecke auf „CIDR“, und geben Sie Folgendes ein:

        -   **Starting IP:** 10.4.0.0

        -   **CIDR:** /16

    -   **Subnetz hinzufügen:** Geben Sie Folgendes ein:

        -   **Benennen Sie Subnetz-1** in *FrontEndSubnetz* mit der Start-IP-Adresse *10.4.2.0/24* um, und klicken Sie dann auf **Subnetz hinzufügen**.

        -   **Erstellen Sie ein Subnetz** mit dem Namen *BackEndSubnetz* und der Start-IP-Adresse *10.4.3.0/24*.

        -   Stellen Sie sicher, dass zwei Subnetze erstellt wurden, und klicken Sie dann auf das Häkchen in der unteren rechten Ecke, um Ihr virtuelles Netzwerk zu erstellen.

    ![](./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png)

3.  Nachdem Sie auf das Häkchen geklickt haben, wird das virtuelle Netzwerk erstellt. Wenn das virtuelle Netzwerk erstellt wurde, wird auf der Seite mit Netzwerken im Verwaltungsportal unter **Status** der Eintrag **Created** angezeigt.

    ![](./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png)

4.  Nachdem das virtuelle Netzwerk erstellt wurde, können Sie mit den folgenden Lernprogrammen fortfahren:

    -   [Hinzufügen eines virtuellen Computers zu einem virtuellen Netzwerk](/de-de/manage/services/networking/add-a-vm-to-a-virtual-network/) – Verwenden Sie dieses grundlegende Lernprogramm, um in Ihrem virtuellen Netzwerk einen virtuellen Computer zu installieren.

    -   Weitere Informationen über virtuelle Computer und Installationsoptionen finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](/de-de/manage/windows/how-to-guides/custom-create-a-vm/) und [Azure Virtual Machines](/de-de/manage/windows/).

    -   [Installieren einer neuen Active Directory-Gesamtstruktur in Azure](/de-de/manage/services/networking/active-directory-forest/) – Verwenden Sie dieses Lernprogramm, um eine neue Active Directory-Gesamtstruktur ohne Verbindung zu einem anderen Netzwerk zu installieren. Das Lernprogramm erläutert die spezifischen Schritte, die zum Erstellen eines virtuellen Computers für eine neue Gesamtstrukturinstallation erforderlich sind. Wenn Sie dieses Lernprogramm verwenden möchten, erstellen Sie keine virtuellen Computer mit dem Verwaltungsportal.

Siehe auch
----------

-   [Überblick über Azure Virtual Network](http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx)

-   [Häufig gestellte Fragen über Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=296650)

-   [Azure Virtual Network-Konfigurationsaufgaben](http://go.microsoft.com/fwlink/?LinkId=296652)

-   [Konfigurieren eines virtuellen Netzwerks mit Netzwerkkonfigurationsdateien](http://msdn.microsoft.com/de-de/library/windowsazure/jj156097.aspx)

-   [Überblick über die Azure-Namensauflösung](http://go.microsoft.com/fwlink/?LinkId=248097)


