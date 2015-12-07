<properties
	pageTitle="Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen (GUI) | Microsoft Azure"
	description="Erstellen Sie eine AlwaysOn-Verfügbarkeitsgruppe mit Azure Virtual Machines. In diesem Tutorial werden die Benutzeroberfläche und Tools anstelle von Skripts verwendet."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/12/2015"
	ms.author="jroth" />

# Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen in einem virtuellen Azure-Computer (GUI)

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


In diesem End-to-End-Tutorial erfahren Sie, wie Sie Verfügbarkeitsgruppen mithilfe von SQL Server AlwaysOn implementieren, das auf virtuellen Computern in Azure ausgeführt wird.

>[AZURE.NOTE]Im Azure-Verwaltungsportal gibt es einen neuen Katalog, der für AlwaysOn-Verfügbarkeitsgruppen mit einem Listener eingerichtet ist. Hierüber wird alles automatisch konfiguriert, was Sie für AlwaysOn-Verfügbarkeitsgruppen benötigen. Weitere Informationen finden Sie unter [SQL Server AlwaysOn Offering in Microsoft Azure Portal Gallery](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx) (in englischer Sprache). Informationen zum Verwenden von PowerShell finden Sie im Tutorial desselben Szenarios unter [Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen in Azure mit PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md).

Am Ende des Tutorials besteht Ihre SQL Server AlwaysOn-Lösung in Azure aus folgenden Elementen:

- Einem virtuellen Netzwerk, das mehrere Subnetze enthält, einschließlich einem Front-End- und Back-End-Subnetz

- Einem Domänencontroller mit einer Active Directory-Domäne (AD)

- Zwei virtuellen SQL Server-Computern, die im Back-End-Subnetz bereitgestellt und der AD-Domäne beigetreten sind

- Einem WSFC-Cluster aus 3 Knoten mit dem Knotenmehrheit-Quorummodell

- Einer Verfügbarkeitsgruppe mit zwei Replikaten einer Verfügbarkeitsdatenbank mit synchronem Commit

Die folgende Abbildung ist eine grafische Darstellung der Lösung.

![Test Lab-Architektur für AG in Azure](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC791912.png)

Beachten Sie, dass dies nur eine mögliche Konfiguration ist. Beispielsweise können Sie die Anzahl der virtuellen Computer für eine Verfügbarkeitsgruppe aus zwei Replikaten verringern, um Rechenzeit in Azure zu sparen, indem Sie den Domänencontroller als Quorum-Dateifreigabezeugen in einem WSFC-Cluster mit 2 Knoten verwenden. Diese Methode verringert die Anzahl der virtuellen Computer in der oben dargestellten Konfiguration um einen Computer.

In diesem Tutorial wird Folgendes vorausgesetzt:

- Sie besitzen bereits ein Azure-Abonnement.

- Sie wissen bereits, wie ein virtueller SQL Server-Computer mithilfe der GUI aus dem virtuellen Computerkatalog bereitgestellt wird. Weitere Informationen finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-provision-sql-server.md).

- Sie verfügen bereits über solide Kenntnisse über AlwaysOn-Verfügbarkeitsgruppen. Weitere Informationen finden Sie unter [AlwaysOn-Verfügbarkeitsgruppen (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE]Wenn Sie an der Verwendung von AlwaysOn-Verfügbarkeitsgruppen mit SharePoint interessiert sind, finden Sie Informationen hierzu unter [Konfigurieren von SQL Server 2012 AlwaysOn-Verfügbarkeitsgruppen für SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## Erstellen des virtuellen Netzwerks und des Domänencontrollerservers

Sie beginnen mit einem neuen Azure-Testkonto. Nachdem Sie die Einrichtung Ihres Kontos fertig gestellt haben, sollten Sie sich im Startbildschirm des Azure-Portals befinden.

1. Klicken Sie, wie unten dargestellt, in der linken unteren Ecke der Seite auf die Schaltfläche **Neu**.

	![Klicken Sie im Portal auf "Neu"](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665511.gif)

1. Klicken Sie auf **Netzwerkdienste** und auf **Virtuelles Netzwerk**, und klicken Sie dann auf **Benutzerdefiniert erstellen**.

	![Erstellen eines virtuellen Netzwerks](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665512.gif)

1. Erstellen Sie im Dialogfeld **VIRTUELLES NETZWERK ERSTELLEN** ein neues virtuelles Netzwerk, indem Sie nacheinander die Seiten mit den nachfolgend aufgeführten Einstellungen durchlaufen.

	|Seite|Einstellungen|
|---|---|
|Details zum virtuellen Netzwerk|**NAME = ContosoNET**<br/>**REGION = USA, Westen**|
|DNS-Server und VPN-Konnektivität|Keine|
|Adressräume des virtuellen Netzwerks|Die Einstellungen sind im nachfolgenden Screenshot zu sehen: ![Erstellen eines virtuellen Netzwerks](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784620.png)|

1. Als Nächstes erstellen Sie den virtuellen Computer, der als Domänencontroller (DC) verwendet werden soll. Klicken Sie, wie unten dargestellt, erneut auf **Neu**, dann auf **Compute**, auf **Virtueller Computer** und schließlich auf **Aus Katalog**.

	![Erstellen einer VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784621.png)

1. Konfigurieren Sie im Dialogfeld **VIRTUELLEN COMPUTER ERSTELLEN** einen neuen virtuellen Computer, indem Sie nacheinander die Seiten mit den nachfolgend aufgeführten Einstellungen durchlaufen.

	|Seite|Einstellungen|
|---|---|
|Betriebssystem des virtuellen Computers auswählen|Windows Server 2012 R2 Datacenter|
|Konfiguration des virtuellen Computers|**VERÖFFENTLICHUNGSDATUM DER VERSION** = (neueste Version)<br/>**NAME DES VIRTUELLEN COMPUTERS** = ContosoDC<br/>**EBENE** = BASIC<br/>**GRÖSSE** = A2 (2 Kerne)<br/>**NEUER BENUTZERNAME** = AzureAdmin<br/>**NEUES KENNWORT** = Contoso!000<br/>**BESTÄTIGEN** = Contoso!000|
|Konfiguration des virtuellen Computers|**CLOUDDIENST** = Neuen Clouddienst erstellen<br/>**DNS-NAME DES CLOUDDIENSTS** = Ein eindeutiger Clouddienstname<br/>**DNS-NAME** = Ein eindeutiger Name (z. B. ContosoDC123)<br/>**REGION/AFFINITÄTSGRUPPE/VIRTUELLES NETZWERK** = ContosoNET<br/>**VIRTUELLES NETZWERK – SUBNETZE** = Back(10.10.2.0/24)<br/>**SPEICHERKONTO** = Automatisch generiertes Speicherkonto verwenden<br/>**VERFÜGBARKEITSGRUPPE** = (Keine)|
|Virtueller Computer - Optionen|Standardwerte verwenden|

Nachdem Sie die Konfiguration des neuen virtuellen Computers fertig gestellt haben, warten Sie auf die Bereitstellung des virtuellen Computers. Dieser Prozess dauert einige Zeit, und wenn Sie im Azure-Portal auf die Registerkarte **Virtueller Computer** klicken, können Sie sehen, wie ContosoDC die Zustände von **Wird gestartet (Bereitstellung)** zu **Beendet**, **Wird gestartet** über **Wird ausgeführt (Bereitstellung)** und schließlich **Wird ausgeführt** durchläuft.

Der DC-Server ist jetzt erfolgreich bereitgestellt. Als Nächstes konfigurieren Sie die Active Directory-Domäne auf diesem DC-Server.

## Konfigurieren des Domänencontrollers

In den folgenden Schritten konfigurieren Sie den Computer "ContosoDC" als Domänencontroller für "corp.contoso.com".

1. Wählen Sie im Portal den Computer **ContosoDC** aus. Klicken Sie auf der Registerkarte **Dashboard** auf **Verbinden**, um eine RDP-Datei für den Remotedesktopzugriff zu öffnen.

	![Herstellen einer Verbindung mit einem virtuellen Computer](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784622.png)

1. Melden Sie sich mit Ihrem konfigurierten Administratorkonto (**\\AzureAdmin**) und Kennwort (**Contoso!000**) an.

1. Standardmäßig sollte das Dashboard **Server-Manager** angezeigt werden.

1. Klicken Sie im Dashboard auf den Link **Rollen und Features hinzufügen**.

	!["Rollen hinzufügen" in Server-Explorer](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784623.png)

1. Wählen Sie **Weiter** aus, bis Sie zum Abschnitt **Serverrollen** gelangen.

1. Wählen Sie die Rollen **Active Directory-Domänendienste** und **DNS-Server** aus. Wenn Sie dazu aufgefordert werden, fügen Sie alle für diese Rollen erforderlichen, zusätzlichen Funktionen hinzu.

	>[AZURE.NOTE]Sie erhalten eine Validierungswarnung, dass keine statische IP-Adresse vorhanden ist. Wenn Sie die Konfiguration testen, klicken Sie auf "Weiter". In Produktionsszenarien [verwenden Sie die PowerShell, um die statische IP-Adresse des Domänencontrollercomputers festzulegen](./virtual-network/virtual-networks-reserved-private-ip.md).

	![Dialogfeld "Rollen hinzufügen"](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784624.png)

1. Klicken Sie auf **Weiter**, bis Sie zum Abschnitt **Bestätigung** gelangen. Aktivieren Sie das Kontrollkästchen **Zielserver bei Bedarf automatisch neu starten**.

1. Klicken Sie auf **Installieren**.

1. Nachdem die Installation der Funktionen abgeschlossen ist, kehren Sie zum Dashboard **Server-Manager** zurück.

1. Wählen Sie die neue Option **AD DS** im linken Bereich aus.

1. Klicken Sie in der gelben Warnungsleiste auf den Link **Mehr**.

	![AD DS-Dialogfeld auf virtuellem DNS-Servercomputer](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784625.png)

1. Klicken Sie im Dialogfeld **Alle Serveraufgabendetails** in der Spalte **Aktion** auf **Server zu einem Domänencontroller heraufstufen**.

1. Verwenden Sie im **Konfigurations-Assistenten für Active Directory-Domänendienste** die folgenden Werte:

	|Seite|Einstellung|
|---|---|
|Bereitstellungskonfiguration|**Neue Gesamtstruktur hinzufügen** = Aktiviert<br/>**Rootdomänenname** = corp.contoso.com|
|Domänencontrolleroptionen|**Kennwort** = Contoso!000<br/>**Kennwort bestätigen** = Contoso!000|

1. Klicken Sie auf **Weiter**, um die anderen Seiten des Assistenten zu durchlaufen. Überprüfen Sie auf der Seite **Prüfung der erforderlichen Komponenten**, ob die folgende Meldung angezeigt wird: **Alle erforderlichen Komponenten wurden erfolgreich überprüft**. Beachten Sie, dass Sie zwar alle zutreffenden Warnmeldungen überprüfen sollten, es aber möglich ist, die Installation fortzusetzen.

1. Klicken Sie auf **Installieren**. Der virtuelle Computer **ContosoDC** wird automatisch neu gestartet.

## Konfigurieren von Domänenkonten

In den nächsten Schritten werden die Active Directory-Konten (AD) für die spätere Verwendung konfiguriert.

1. Melden Sie sich wieder bei dem Computer **ContosoDC** an.

1. Wählen Sie im **Server-Manager** den Eintrag **Tools** aus, und klicken Sie dann auf **Active Directory-Verwaltungscenter**.

	![Active Directory-Verwaltungscenter](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784626.png)

1. Wählen Sie im **Active Directory-Verwaltungscenter** im linken Bereich **corp (lokal)** aus.

1. Wählen Sie im rechten **Aufgaben**bereich den Eintrag **Neu** aus, und klicken Sie dann auf **Benutzer**. Verwenden Sie folgende Einstellungen:

	|Einstellung|Wert|
|---|---|
|**Vorname**|Installieren|
|**SamAccountName von Benutzer**|Installieren|
|**Kennwort**|Contoso!000|
|**Kennwort bestätigen**|Contoso!000|
|**Andere Kennwortoptionen**|Aktiviert|
|**Kennwort läuft nie ab**|Aktiviert|

1. Klicken Sie auf **OK**, um den **Install**-Benutzer zu erstellen. Dieses Konto wird zum Konfigurieren des Failoverclusters und der Verfügbarkeitsgruppe verwendet.

1. Erstellen Sie zwei zusätzliche Benutzer mit denselben Schritten: **CORP\\SQLSvc1** und **CORP\\SQLSvc2**. Diese Konten werden für die SQL Server-Instanzen verwendet. Anschließend müssen Sie **CORP\\Install** die erforderlichen Berechtigungen für das Konfigurieren von Windows Service Failover Clustering (WSFC) erteilen.

1. Wählen Sie im **Active Directory-Verwaltungscenter** im linken Bereich **corp (lokal)** aus. Klicken Sie dann im rechten **Aufgaben**bereich auf **Eigenschaften**.

	![Eigenschaften des Benutzers CORP](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784627.png)

1. Wählen Sie **Erweiterungen** aus, und klicken Sie auf der Registerkarte **Sicherheit** auf die Schaltfläche **Erweitert**.

1. Im Dialogfeld **Erweiterte Sicherheitseinstellungen für corp**: Klicken Sie auf **Hinzufügen**.

1. Klicken Sie auf **Prinzipal auswählen**. Suchen Sie dann nach **CORP\\Install**. Klicken Sie auf **OK**.

1. Wählen Sie die Berechtigungen **Alle Eigenschaften lesen** und **Computerobjekte erstellen** aus.

	![Berechtigungen des Benutzers CORP](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784628.png)

1. Klicken Sie auf **OK** und dann erneut auf **OK**. Schließen Sie das Eigenschaftenfenster von corp.

Nachdem Sie nun die Konfiguration von Active Directory und den Benutzerobjekten abgeschlossen haben, erstellen drei virtuelle SQL Server-Computer und lassen sie dieser Domäne beitreten.

## Erstellen der virtuellen SQL Server-Computer

Als Nächstes erstellen Sie drei virtuelle Computer, einschließlich eines WSFC-Clusterknotens und zweier virtueller SQL Server-Computer. Um jeden dieser virtuellen Computer zu erstellen, kehren Sie zum Azure-Portal zurück. Klicken Sie auf **Neu**, **Compute**, **Virtueller Computer** und dann auf **Aus Katalog**. Verwenden Sie dann die Vorlagen aus der folgenden Tabelle, die Sie bei der Erstellung der virtuellen Computer unterstützen.

|Seite|VM1|VM2|VM3|
|---|---|---|---|
|Betriebssystem des virtuellen Computers auswählen|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|Konfiguration des virtuellen Computers|**VERÖFFENTLICHUNGSDATUM DER VERSION** = (neueste Version)<br/>**NAME DES VIRTUELLEN COMPUTERS** = ContosoWSFCNode<br/>**EBENE** = BASIC<br/>**GRÖSSE** = A2 (2 Kerne)<br/>**NEUER BENUTZERNAME** = AzureAdmin<br/>**NEUES KENNWORT** = Contoso!000<br/>**BESTÄTIGEN** = Contoso!000|**VERÖFFENTLICHUNGSDATUM DER VERSION** = (neueste Version)<br/>**NAME DES VIRTUELLEN COMPUTERS** = ContosoSQL1<br/>**EBENE** = BASIC<br/>**GRÖSSE** = A3 (4 Kerne)<br/>**NEUER BENUTZERNAME** = AzureAdmin<br/>**NEUES KENNWORT** = Contoso!000<br/>**BESTÄTIGEN** = Contoso!000|**VERÖFFENTLICHUNGSDATUM DER VERSION** = (neueste Version)<br/>**NAME DES VIRTUELLEN COMPUTERS** = ContosoSQL2<br/>**EBENE** = BASIC<br/>**GRÖSSE** = A3 (4 Kerne)<br/>**NEUER BENUTZERNAME** = AzureAdmin<br/>**NEUES KENNWORT** = Contoso!000<br/>**BESTÄTIGEN** = Contoso!000|
|Konfiguration des virtuellen Computers|**CLOUDDIENST** = Zuvor erstellter eindeutiger DNS-Name des Clouddiensts (z. B. ContosoDC123)<br/>**REGION/AFFINITÄTSGRUPPE/VIRTUELLES NETZWERK** = ContosoNET<br/>**VIRTUELLES NETZWERK – SUBNETZE** = Back(10.10.2.0/24)<br/>**SPEICHERKONTO** = Automatisch generiertes Speicherkonto verwenden<br/>**VERFÜGBARKEITSGRUPPE** = Verfügbarkeitsgruppe erstellen<br/>**VERFÜGBARKEITSGRUPPENNAME** = SQLHADR|**CLOUDDIENST** = Zuvor erstellter eindeutiger DNS-Name des Clouddiensts (z. B. ContosoDC123)<br/>**REGION/AFFINITÄTSGRUPPE/VIRTUELLES NETZWERK** = ContosoNET<br/>**VIRTUELLES NETZWERK – SUBNETZE** = Back(10.10.2.0/24)<br/>**SPEICHERKONTO** = Automatisch generiertes Speicherkonto verwenden<br/>**VERFÜGBARKEITSGRUPPE** = SQLHADR (Sie können die Verfügbarkeitsgruppe auch konfigurieren, nachdem der Computer erstellt wurde. Alle drei Computer sollten der Verfügbarkeitsgruppe SQLHADR zugewiesen werden.)|**CLOUDDIENST** = Zuvor erstellter eindeutiger DNS-Name des Clouddiensts (z. B. ContosoDC123)<br/>**REGION/AFFINITÄTSGRUPPE/VIRTUELLES NETZWERK** = ContosoNET<br/>**VIRTUELLES NETZWERK – SUBNETZE** = Back(10.10.2.0/24)<br/>**SPEICHERKONTO** = Automatisch generiertes Speicherkonto verwenden<br/>**VERFÜGBARKEITSGRUPPE** = SQLHADR (Sie können die Verfügbarkeitsgruppe auch konfigurieren, nachdem der Computer erstellt wurde. Alle drei Computer sollten der Verfügbarkeitsgruppe SQLHADR zugewiesen werden.)|
|Virtueller Computer - Optionen|Standardwerte verwenden|Standardwerte verwenden|Standardwerte verwenden|

Sobald die drei virtuellen Computer vollständig bereitgestellt wurden, müssen Sie sie der Domäne **corp.contoso.com** beitreten lassen und CORP\\Install Administratorrechte für die Computer gewähren. Verwenden Sie hierzu die folgenden Schritte für jeden der drei virtuellen Computer.

1. Ändern Sie zunächst die Adresse des bevorzugte DNS-Servers. Beginnen Sie damit, dass Sie die RDP-Datei (Remotedesktop) jedes virtuellen Computers in Ihr lokales Verzeichnis herunterladen, indem Sie den virtuellen Computer in der Liste auswählen und dann auf die Schaltfläche **Verbinden** klicken. Um einen virtuellen Computer auszuwählen, klicken Sie, wie unten gezeigt, an eine beliebige Stelle, außer auf die erste Zelle in der Zeile.

	![Herunterladen der RDP-Datei](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664953.jpg)

1. Starten Sie die RDP-Datei, die Sie heruntergeladen haben, und melden Sie sich beim virtuellen Computer mit Ihrem konfigurierten Administratorkonto (**BUILTIN\\AzureAdmin**) und dem Kennwort (**Contoso!000**) an.

1. Sobald Sie angemeldet sind, sollte das Dashboard **Server-Manager** angezeigt werden. Klicken Sie im linken Bereich auf **Lokaler Server**.

1. Wählen Sie den Link **IPv4-Adresse wird per DHCP zugewiesen, IPv6-fähig** aus.

1. Wählen Sie im Fenster **Netzwerkverbindungen** das Netzwerksymbol aus.

	![Ändern des bevorzugten DNS-Servers für den virtuellen Computer](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784629.png)

1. Klicken Sie auf der Befehlsleiste auf **Einstellungen dieser Verbindung ändern** (abhängig von der Größe Ihres Fensters müssen Sie möglicherweise auf den Doppelpfeil nach rechts klicken, um diesen Befehl anzuzeigen).

1. Wählen Sie **Internetprotokoll Version 4 (TCP/IPv4)** aus, und klicken Sie auf „Eigenschaften“.

1. Wählen Sie „Folgende DNS-Serveradressen verwenden“ aus, und geben Sie im Feld **Bevorzugter DNS-Server** **10.10.2.4** an.

1. Die Adresse **10.10.2.4** ist die einem virtuellen Computer im Subnetz 10.10.2.0/24 zugewiesene Adresse in einem virtuellen Azure-Netzwerk, und dieser virtuelle Computer ist **ContosoDC**. Um die IP-Adresse von **ContosoDC** zu überprüfen, verwenden Sie, wie unten dargestellt, **nslookup contosodc** in der Befehlszeile.

	![Verwenden von NSLOOKUP zum Suchen der IP-Adresse für den DC](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664954.jpg)

1. Klicken Sie auf **OK** und anschließend auf **Schließen**, um die Änderungen zu übernehmen. Sie können den virtuellen Computer jetzt zu **corp.contoso.com** beitreten lassen.

1. Klicken Sie, nachdem Sie sich wieder im Fenster **Lokaler Server** befinden, auf den Link **ARBEITSGRUPPE**.

1. Klicken Sie im Abschnitt **Computername** auf **Ändern**.

1. Aktivieren Sie das Kontrollkästchen **Domäne**, und geben Sie in das Textfeld **corp.contoso.com** ein. Klicken Sie auf **OK**.

1. Geben Sie im Popup-Dialogfeld **Windows-Sicherheit** die Anmeldeinformationen für das Standarddomänen-Administratorkonto (**CORP\\AzureAdmin**) und das Kennwort (**Contoso!000**) an.

1. Wenn die Meldung „Willkommen in der Domäne ‚corp.contoso.com‘“ angezeigt wird, klicken Sie auf **OK**.

1. Klicken Sie auf **Schließen**, und klicken Sie dann im Popupdialogfeld auf **Jetzt neu starten**.

### Fügen Sie auf jedem virtuellen Computer den Benutzer "Corp\\Install" als Administrator hinzu:

1. Warten Sie, bis der virtuelle Computer neu gestartet wird, und starten Sie dann die RDP-Datei erneut, um sich beim virtuellen Computer mithilfe des Kontos **BUILTIN\\AzureAdmin** anzumelden.

1. Wählen Sie im **Server-Manager** den Eintrag **Tools** aus, und klicken Sie dann auf **Computerverwaltung**.

	![Computerverwaltung](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784630.png)

1. Erweitern Sie im Fenster **Computerverwaltung** den Eintrag **Lokale Benutzer und Gruppen**, und wählen Sie dann **Gruppen** aus.

1. Doppelklicken Sie auf die Gruppe **Administratoren**.

1. Klicken Sie im Dialogfeld **Administratoreigenschaften** auf die Schaltfläche **Hinzufügen**.

1. Geben Sie den Benutzer **CORP\\Install** ein, und klicken Sie dann auf **OK**. Verwenden Sie, wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, das Konto **AzureAdmin** mit dem Kennwort **Contoso!000**.

1. Klicken Sie auf **OK**, um das Dialogfeld **Administratoreigenschaften** zu schließen.

### Fügen Sie jedem virtuellen Computer die Funktion **Failoverclustering** hinzu.

1. Klicken Sie im Dashboard **Server-Manager** auf **Rollen und Features hinzufügen**.

1. Klicken Sie im **Assistenten zum Hinzufügen von Rollen und Features** auf **Weiter**, bis Sie zur Seite **Features** gelangen.

1. Wählen Sie **Failoverclustering** aus. Wenn Sie dazu aufgefordert werden, fügen Sie alle weiteren, abhängigen Features hinzu.

	![Hinzufügen der Funktion "Failoverclustering" zum virtuellen Computer](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784631.png)

1. Klicken Sie auf **Weiter**, und klicken Sie dann auf der Seite **Bestätigung** auf **Installieren**.

1. Wenn die Installation des Features **Failoverclustering** abgeschlossen ist, klicken Sie auf **Schließen**.

1. Melden Sie sich bei dem virtuellen Computer ab.

1. Wiederholen Sie die Schritte in diesem Abschnitt für alle drei Server – **ContosoWSFCNode**, **ContosoSQL1** und **ContosoSQL2**.

Die virtuellen SQL Server-Computer sind jetzt bereitgestellt und werden ausgeführt, sie sind aber mit SQL Server mit Standardoptionen installiert.

## Erstellen des WSFC-Clusters

In diesem Abschnitt erstellen Sie den WSFC-Cluster, der die Verfügbarkeitsgruppe hostet, die Sie später erstellen werden. Mittlerweile sollen Sie Folgendes auf jedem der drei virtuellen Computer, die im WSFC-Cluster verwendet werden sollen, erledigt haben:

- Vollständige Bereitstellung in Azure

- Beitritt des virtuellen Computers zur Domäne

- Hinzufügen von **CORP\\Install** zur lokalen Administratorgruppe

- Hinzufügen der Funktion "Failoverclustering"

All dies sind Voraussetzungen für jeden der virtuellen Computer, damit er dem WSFC-Cluster hinzugefügt werden kann.

Beachten Sie außerdem, dass sich das virtuelle Azure-Netzwerk nicht auf dieselbe Weise wie ein lokales Netzwerk verhält. Sie müssen die Cluster in der folgenden Reihenfolge erstellen:

1. Erstellen Sie einen Einzelknotencluster auf einem der Knoten (**ContosoSQL1**).

1. Ändern Sie die IP-Adresse des Clusters in eine nicht verwendete IP-Adresse (**10.10.2.101**).

1. Schalten Sie den Clusternamen online.

1. Fügen Sie die anderen Knoten hinzu (**ContosoSQL2** und **ContosoWSFCNode**).

Gehen Sie folgendermaßen vor, um diese Aufgaben durchzuführen, mit denen der Cluster vollständig konfiguriert wird.

1. Starten Sie die RDP-Datei für **ContosoSQL1**, und melden Sie sich mit dem Domänenkonto **CORP\\Install** an.

1. Wählen Sie im Dashboard **Server-Manager** den Eintrag **Tools** aus, und klicken Sie dann auf **Failovercluster-Manager**.

1. Klicken Sie, wie unten gezeigt, im linken Bereich mit der rechten Maustaste auf **Failovercluster-Manager**, und klicken Sie dann auf **Cluster erstellen**.

	![Cluster erstellen](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784632.png)

1. Erstellen Sie im Assistenten "Cluster erstellen" einen Cluster mit einem Knoten, indem Sie nacheinander die Seiten mit den nachfolgend aufgeführten Einstellungen durchlaufen:

	|Seite|Einstellungen|
|---|---|
|Voraussetzungen|Standardwerte verwenden|
|Server auswählen|Geben Sie **ContosoSQL1** unter **Servernamen eingeben** ein, und klicken Sie auf **Hinzufügen**.|
|Validierungswarnung|Wählen Sie **Nein. Microsoft-Support für diesen Cluster nicht nötig. Validierungstests nicht durchführen. Beim Klicken auf "Weiter" Erstellung des Clusters fortsetzen.** aus.|
|Zugriffspunkt für die Clusterverwaltung|Geben Sie **Cluster1** in **Clustername** ein.|
|Bestätigung|Standardeinstellungen verwenden, es sei denn, Sie verwenden Speicherplätze. Siehe den Hinweis im Anschluss an diese Tabelle.|

	>[AZURE.WARNING]Bei Verwendung von [Speicherplätzen](https://technet.microsoft.com/library/hh831739), die mehrere Datenträger zu Speicherpools gruppieren, müssen Sie das Kontrollkästchen **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden** auf der Seite **Bestätigung** deaktivieren. Wenn Sie diese Option nicht deaktivieren, werden die virtuellen Datenträger während des Clusteringprozesses getrennt. Als Folge daraus werden sie dann auch erst im Datenträger-Manager oder -Explorer angezeigt, nachdem die Speicherplätze aus dem Cluster entfernt und mithilfe der PowerShell erneut angefügt wurden.

1. Erweitern Sie im linken Bereich **Failovercluster-Manager**, und klicken Sie dann auf **Cluster1.corp.contoso.com**.

1. Scrollen Sie im mittleren Bereich nach unten bis zum Abschnitt **Hauptressourcen des Clusters**, und erweitern Sie die Details von **Name: Cluster1**. Sie sollten sowohl die Ressource **Name** als auch **IP-Adresse** im Zustand **Fehler** sehen. Die IP-Adressressource kann nicht online geschaltet werden, da dem Cluster dieselbe IP-Adresse wie dem Computer selbst zugewiesen ist, wobei es sich damit um eine doppelte Adresse handelt.

1. Klicken Sie mit der rechten Maustaste auf die fehlgeschlagene Ressource **IP-Adresse**, und klicken Sie dann auf **Eigenschaften**.

	![Eigenschaften des Clusters](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784633.png)

1. Wählen Sie **Statische IP-Adresse** aus, und geben Sie in das Textfeld „Adresse“ den Wert **10.10.2.101** ein. Klicken Sie dann auf **OK**.

1. Klicken Sie im Abschnitt **Hauptressourcen des Clusters** mit der rechten Maustaste auf **Name: Cluster1**, und klicken Sie dann auf **Online schalten**. Warten Sie dann, bis beide Ressourcen online sind. Wenn die Clusternamensressource online ist, aktualisiert sie den DC-Server mit einem neuen AD-Computerkonto. Dieses AD-Konto wird später zum Ausführen des Clusterdiensts der Verfügbarkeitsgruppe verwendet.

1. Schließlich fügen Sie die übrigen Knoten dem Cluster hinzu. Klicken Sie, wie unten dargestellt, in der Browserstruktur mit der rechten Maustaste auf **Cluster1.corp.contoso.com**, und klicken Sie dann auf **Knoten hinzufügen**.

	![Hinzufügen eines Knotens zum Cluster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784634.png)

1. Klicken Sie im **Assistenten „Knoten hinzufügen“** auf **Weiter**. Fügen Sie auf der Seite **Server auswählen** der Liste die Namen **ContosoSQL2** und **ContosoWSFCNode** hinzu, indem Sie den Servernamen in **Servernamen eingeben** eingeben, und klicken Sie dann auf **Hinzufügen**. Wenn Sie fertig sind, klicken Sie auf **Weiter**.

1. Klicken Sie auf der Seite **Validierungswarnung** auf **Nein** (in einem Produktionsszenario sollten Sie die Validierungstests ausführen). Klicken Sie auf **Weiter**.

1. Klicken Sie auf der Seite **Bestätigung** auf **Weiter**, um die Knoten hinzuzufügen.

	>[AZURE.WARNING]Bei Verwendung von [Speicherplätzen](https://technet.microsoft.com/library/hh831739), die mehrere Datenträger zu Speicherpools gruppieren, müssen Sie das Kontrollkästchen **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden** deaktivieren. Wenn Sie diese Option nicht deaktivieren, werden die virtuellen Datenträger während des Clusteringprozesses getrennt. Als Folge daraus werden sie dann auch erst im Datenträger-Manager oder -Explorer angezeigt, nachdem die Speicherplätze aus dem Cluster entfernt und mithilfe der PowerShell erneut angefügt wurden.

1. Nachdem die Knoten dem Cluster hinzugefügt wurden, klicken Sie auf **Fertig stellen**. Im Failovercluster-Manager sollte nun angezeigt werden, dass Ihr Cluster über drei Knoten verfügt, und diese sollten im Container **Knoten** aufgelistet werden.

1. Melden Sie sich bei der Remotedesktopsitzung ab.

## Vorbereiten der SQL Server-Instanzen für die Verfügbarkeitsgruppe

In diesem Abschnitt führen Sie die folgenden Aktionen sowohl mit **ContosoSQL1** als auch mit **ContosoSQL2** durch:

- Hinzufügen einer Anmeldung für **NT-AUTORITÄT\\System** mit Festlegung der erforderlichen Berechtigungen auf die Standardinstanz von SQL Server

- Hinzufügen von **CORP\\Install** zur Standardinstanz von SQL Server als „sysadmin“-Rolle

- Öffnen der Firewall für den Remotezugriff von SQL Server

- Aktivieren der Funktion "AlwaysOn-Verfügbarkeitsgruppen"

- Ändern des SQL Server-Dienstkontos in **CORP\\SQLSvc1** bzw. **CORP\\SQLSvc2**

Diese Aktionen können in beliebiger Reihenfolge ausgeführt werden. Dennoch werden die folgenden Schritte in der vorgegebenen Reihenfolge durchlaufen. Führen Sie die Schritte sowohl für **ContosoSQL1** als auch für **ContosoSQL2** aus:

1. Wenn Sie sich noch nicht von der Remotedesktopsitzung für den virtuellen Computer abgemeldet haben, tun Sie dies jetzt.

1. Starten Sie die RDP-Dateien für **ContosoSQL1** und **ContosoSQL2**, und melden Sie sich als **BUILTIN\\AzureAdmin** an.

1. Als Erstes fügen Sie **NT-AUTORITÄT\\System** den SQL Server-Anmeldungen mit den erforderlichen Berechtigungen hinzu. Starten Sie **SQL Server Management Studio**.

1. Klicken Sie auf **Verbinden**, um eine Verbindung mit der Standardinstanz von SQL Server herzustellen.

1. Erweitern Sie im **Objekt-Explorer** den Eintrag **Sicherheit**, und erweitern Sie dann **Anmeldungen**.

1. Klicken Sie mit der rechten Maustaste auf die Anmeldung **NT-AUTORITÄT\\System**, und klicken Sie auf **Eigenschaften**.

1. Wählen Sie auf der Seite **Sicherungsfähige Elemente** für den lokalen Server für die folgenden Berechtigungen die Option **Erteilen** aus, und klicken Sie auf **OK**.

	- Beliebige Verfügbarkeitsgruppe ändern

	- SQL verbinden

	- Serverstatus anzeigen

1. Als Nächstes fügen Sie **CORP\\Install** als **sysadmin**-Rolle der Standardinstanz von SQL Server hinzu. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf **Anmeldungen**, und klicken Sie dann auf **Neue Anmeldung**.

1. Geben Sie **CORP\\Install** in das Feld **Anmeldename** ein.

1. Wählen Sie auf der Seite **Serverrollen** den Eintrag **sysadmin** aus. Klicken Sie dann auf **OK**. Nachdem die Anmeldung erstellt wurde, können Sie sie anzeigen, indem Sie die **Anmeldungen** im **Objekt-Explorer** erweitern.

1. Als Nächstes erstellt Sie eine Firewallregel für SQL Server. Starten Sie vom **Startbildschirm** aus die **Windows-Firewall mit erweiterter Sicherheit**.

1. Klicken Sie im linken Bereich auf **Eingehende Regeln**. Klicken Sie im rechten Bereich auf **Neue Regel**.

1. Wählen Sie auf der Seite **Regeltyp** die Option **Programm** aus, und klicken Sie dann auf **Weiter**.

1. Wählen Sie auf der Seite **Programm** die Option **Dieser Programmpfad** aus, und geben Sie **%ProgramFiles%\\Microsoft SQL Server\\MSSQL12. MSSQLSERVER\\MSSQL\\Binn\\sqlservr.exe** in das Textfeld ein (wenn Sie diese Anleitung befolgen, aber SQL Server 2012 verwenden, ist das SQL Server-Verzeichnis **MSSQL11. MSSQLSERVER**). Klicken Sie auf **Next**.

1. Lassen Sie auf der Seite **Aktion** die Option **Verbindung zulassen** aktiviert, und klicken Sie auf **Weiter**.

1. Akzeptieren Sie auf der Seite **Profil** die Standardeinstellungen, und klicken Sie auf **Weiter**.

1. Geben Sie auf der Seite **Name** im Textfeld **Name** einen Regelnamen an, z. B.**SQL Server (Programmregel)**, und klicken Sie auf **Fertig stellen**.

1. Als Nächstes aktivieren Sie die Funktion **AlwaysOn-Verfügbarkeitsgruppen**. Starten Sie aus dem **Startbildschirm** heraus den **SQL Server-Konfigurations-Manager**.

1. Klicken Sie in der Browserstruktur auf **SQL Server-Dienste**, klicken Sie dann mit der rechten Maustaste auf den Dienst **SQL Server (MSSQLSERVER)**, und klicken Sie auf **Eigenschaften**.

1. Klicken Sie, wie unten gezeigt, auf die Registerkarte **Hohe Verfügbarkeit mit AlwaysOn**, wählen Sie **AlwaysOn-Verfügbarkeitsgruppen aktivieren** aus, und klicken Sie dann auf **Übernehmen**. Klicken Sie im Popupdialogfenster auf **OK**, und lassen Sie das Eigenschaftenfenster noch geöffnet. Nachdem Sie das Dienstkonto geändert haben, starten Sie den SQL Server-Dienst neu.

	![Aktivieren von AlwaysOn-Verfügbarkeitsgruppen](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665520.gif)

1. Als Nächstes ändern Sie das SQL Server-Dienstkonto. Klicken Sie auf die Registerkarte **Anmelden**, und geben Sie **CORP\\SQLSvc1** (für **ContosoSQL1**) oder **CORP\\SQLSvc2** (für **ContosoSQL2**) in **Kontoname** ein. Geben Sie dann das Kennwort ein, und bestätigen Sie es, und klicken Sie auf **OK**.

1. Klicken Sie im Popupfenster auf **Ja**, um den SQL Server-Dienst neu zu starten. Nach dem Neustart des SQL Server-Diensts sind die Änderungen, die Sie im Eigenschaftenfenster vorgenommen haben, wirksam

1. Melden Sie sich bei den virtuellen Computern ab.

## Erstellen der Verfügbarkeitsgruppe

Sie sind jetzt bereit, um eine Verfügbarkeitsgruppe zu konfigurieren. Im Folgenden finden Sie einen Überblick der vorzunehmenden Aktionen:

- Erstellen einer neuen Datenbank (**MyDB1**) auf **ContosoSQL1**

- Erstellen sowohl einer vollständigen Sicherung als auch einer Transaktionsprotokollsicherung der Datenbank

- Wiederherstellen der vollständigen Sicherung und der Protokollsicherung auf **ContosoSQL2** mit der Option **NORECOVERY**

- Erstellen der Verfügbarkeitsgruppe (**AG1**) mit synchronem Commit, automatischem Failover und lesbaren, sekundären Replikaten

### Erstellen der Datenbank "MyDB1" auf "ContosoSQL1":

1. Wenn Sie sich noch nicht von den Remotedesktopsitzungen für **ContosoSQL1** und **ContosoSQL2** abgemeldet haben, tun Sie dies jetzt.

1. Starten Sie die RDP-Datei für **ContosoSQL1**, und melden Sie sich als **CORP\\Install** an.

1. Erstellen Sie im **Datei-Explorer** unter **C:** ein Verzeichnis namens **backup**. Dieses Verzeichnis verwenden Sie zum Sichern und Wiederherstellen Ihrer Datenbank.

1. Klicken Sie, wie unten gezeigt, mit der rechten Maustaste auf das neue Verzeichnis, zeigen Sie auf **Freigeben für**, und klicken Sie dann auf **Bestimmte Personen**.

	![Erstellen eines Sicherungsordners](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665521.gif)

1. Fügen Sie **CORP\\SQLSvc1** hinzu, und erteilen Sie dafür die Berechtigung **Lesen/Schreiben**, fügen Sie anschließend **CORP\\SQLSvc2** hinzu, und erteilen Sie dafür die Berechtigung **Lesen**, wie unten dargestellt, und klicken Sie dann auf **Freigeben**. Nachdem der Dateifreigabeprozess abgeschlossen ist, klicken Sie auf **Fertig**.

	![Erteilen von Berechtigungen für den Sicherungsordner](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665522.gif)

1. Erstellen Sie als Nächstes die Datenbank. Starten Sie aus dem **Start**menü heraus **SQL Server Management Studio**, und klicken Sie dann auf **Verbinden**, um eine Verbindung mit der Standardinstanz von SQL Server herzustellen.

1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf **Datenbanken**, und klicken Sie dann auf **Neue Datenbank**.

1. Geben Sie in **Datenbankname** den Namen **MyDB1** ein, und klicken Sie dann auf **OK**.

### Erstellen Sie eine vollständige Sicherung von MyDB1, und stellen Sie sie auf ContosoSQL2 wieder her:

1. Erstellen Sie als Nächstes eine vollständige Sicherung der Datenbank. Erweitern Sie im **Objekt-Explorer** den Eintrag **Datenbanken**, klicken Sie mit der rechten Maustaste auf **MyDB1**, zeigen Sie auf **Aufgaben**, und klicken Sie dann auf **Sichern**.

1. Lassen Sie im Abschnitt **Quelle** die Option **Sicherungstyp** auf **Vollständig** festgelegt. Klicken Sie im Abschnitt **Ziel** auf **Entfernen**, um den Standarddateipfad der Sicherungsdatei zu entfernen.

1. Klicken Sie im Abschnitt **Ziel** auf **Hinzufügen**.

1. Geben Sie in das Textfeld **Dateiname** den Wert **\\ContosoSQL1\\backup\\MyDB1.bak** ein. Klicken Sie dann auf **OK**, und klicken Sie anschließend wieder auf **OK**, um die Datenbank zu sichern. Wenn der Sicherungsvorgang abgeschlossen ist, klicken Sie wieder auf **OK**, um das Dialogfeld zu schließen.

1. Als Nächstes erstellen Sie eine Transaktionsprotokollsicherung der Datenbank. Erweitern Sie im **Objekt-Explorer** den Eintrag **Datenbanken**, klicken Sie mit der rechten Maustaste auf **MyDB1**, zeigen Sie auf **Aufgaben**, und klicken Sie dann auf **Sichern**.

1. Wählen Sie unter **Sicherungstyp** die Option **Transaktionsprotokoll** aus. Behalten Sie für den Dateipfad **Ziel** den von Ihnen zuvor angegebenen Pfad bei, und klicken Sie auf **OK**. Wenn der Sicherungsvorgang abgeschlossen ist, klicken Sie wieder auf **OK**.

1. Als Nächstes stellen Sie die vollständige Sicherung und die Transaktionsprotokollsicherung auf **ContosoSQL2** wieder her. Starten Sie die RDP-Datei für **ContosoSQL2**, und melden Sie sich als **CORP\\Install** an. Lassen Sie die Remotedesktopsitzung für **ContosoSQL1** geöffnet.

1. Starten Sie aus dem **Start**menü heraus **SQL Server Management Studio**, und klicken Sie dann auf **Verbinden**, um eine Verbindung mit der Standardinstanz von SQL Server herzustellen.

1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf **Datenbanken**, und klicken Sie dann auf **Datenbank wiederherstellen**.

1. Wählen Sie im Abschnitt **Quelle** die Option **Gerät** aus, und klicken Sie dann auf die Schaltfläche mit den Auslassungspunkten (**...**).

1. Klicken Sie unter **Sicherungsmedien auswählen** auf **Hinzufügen**.

1. Geben Sie in "Speicherort der Sicherungsdatei" den Pfad "\\ContosoSQL1\\backup" ein, klicken Sie auf "Aktualisieren", wählen Sie dann "MyDB1.bak" aus, klicken Sie auf "OK", und klicken Sie wieder auf "OK". Im Abschnitt "Wiederherzustellende Sicherungssätze" sollten jetzt die vollständige Sicherung und die Protokollsicherung angezeigt werden.

1. Wechseln Sie zur Seite "Optionen", wählen Sie dann in "Wiederherstellungsstatus" die Option "MIT NORECOVERY WIEDERHERSTELLEN" aus, und klicken Sie dann auf "OK", um die Datenbank wiederherzustellen. Wenn der Wiederherstellungsvorgang abgeschlossen ist, klicken Sie auf "OK".

### Erstellen der Verfügbarkeitsgruppe:

1. Wechseln Sie zurück zur Remotedesktopsitzung für **ContosoSQL1**. Klicken Sie, wie unten dargestellt, im **Objekt-Explorer** in SSMS mit der rechten Maustaste auf **Hohe Verfügbarkeit mit AlwaysOn**, und klicken Sie dann auf **Assistent für neue Verfügbarkeitsgruppen**.

	![Starten des Assistenten für neue Verfügbarkeitsgruppen](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665523.gif)

1. Klicken Sie auf der Seite **Einführung** auf **Weiter**. Geben Sie auf der Seite **Namen der Verfügbarkeitsgruppe angeben** den Namen **AG1** in das Feld **Name der Verfügbarkeitsgruppe** ein, und klicken Sie dann erneut auf **Weiter**.

	![Assistent für neue Verfügbarkeitsgruppen, Namen der Verfügbarkeitsgruppe angeben](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665524.gif)

1. Wählen Sie auf der Seite **Datenbanken auswählen** den Eintrag **MyDB1** aus, und klicken Sie auf **Weiter**. Die Datenbank erfüllt die Voraussetzungen für eine Verfügbarkeitsgruppe, weil Sie mindestens eine vollständige Sicherung auf dem vorgesehenen primären Replikat erstellt haben.

	![Assistent für neue Verfügbarkeitsgruppen, Datenbanken auswählen](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665525.gif)

1. Klicken Sie auf der Seite **Replikate angeben** auf **Replikat hinzufügen**.

	![Assistent für neue Verfügbarkeitsgruppen, Replikate angeben](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665526.gif)

1. Das Dialogfeld **Verbindung mit Server herstellen** wird angezeigt. Geben Sie **ContosoSQL2** in **Servername** ein, und klicken Sie dann auf **Verbinden**.

	![Assistent für neue Verfügbarkeitsgruppen, Verbindung mit dem Server herstellen](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665527.gif)

1. Auf der Seite **Replikate angeben** sollte jetzt **ContosoSQL2** unter **Verfügbare Replikate** aufgelistet werden. Konfigurieren Sie die Replikate, wie unten gezeigt. Wenn Sie fertig sind, klicken Sie auf **Weiter**.

	![Assistent für neue Verfügbarkeitsgruppen, Replikate angeben (abgeschlossen)](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665528.gif)

1. Wählen Sie auf der Seite **Anfängliche Datensynchronisierung auswählen** die Option **Nur verknüpfen** aus, und klicken Sie dann auf **Weiter**. Sie haben bereits manuell eine Datensynchronisierung ausgeführt, als Sie die vollständige Sicherung und die Transaktionssicherung auf **ContosoSQL1** erstellt und diese auf **ContosoSQL2** wiederhergestellt haben. Alternativ können Sie sich entscheiden, die Sicherungs- und Wiederherstellungsvorgänge nicht mit Ihrer Datenbank durchzuführen, und **Vollständig** auswählen, damit der „Assistent für neue Verfügbarkeitsgruppen“ die Datensynchronisierung für Sie ausführt. Hiervon wird jedoch bei sehr großen Datenbanken, wie sie in manchen Unternehmen vorhanden sind, abgeraten.

	![Assistent für neue Verfügbarkeitsgruppen, anfängliche Datensynchronisierung auswählen](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665529.gif)

1. Klicken Sie auf der Seite **Validierung** auf **Weiter**. Diese Seite sollte ungefähr wie unten abgebildet aussehen. Es liegt eine Warnung für die Listenerkonfiguration vor, weil Sie keinen Listener für die Verfügbarkeitsgruppe konfiguriert haben. Diese Warnung können Sie ignorieren, weil in diesem Tutorial kein Listener konfiguriert wird. Informationen zum Konfigurieren des Listeners nach Abschluss dieses Tutorials finden Sie unter [Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

	![Assistent für neue Verfügbarkeitsgruppen, Validierung](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665530.gif)

1. Klicken Sie auf der Seite **Zusammenfassung** auf **Fertig stellen**, und warten Sie dann, während der Assistent die neue Verfügbarkeitsgruppe konfiguriert. Auf der Seite **Status** können Sie auf **Weitere Details** klicken, um den detaillierten Status anzuzeigen. Überprüfen Sie nach Abschluss des Assistenten, wie unten dargestellt, die Seite **Ergebnisse**, um sich zu vergewissern, dass die Verfügbarkeitsgruppe erfolgreich erstellt wurde, und klicken Sie dann auf **Schließen**, um den Assistenten zu beenden.

	![Assistent für neue Verfügbarkeitsgruppen, Ergebnisse](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665531.gif)

1. Erweitern Sie im **Objekt-Explorer** den Eintrag **Hohe Verfügbarkeit mit AlwaysOn**, und erweitern Sie dann **Verfügbarkeitsgruppen**. Sie sollten jetzt die neue Verfügbarkeitsgruppe in diesem Container sehen können. Klicken Sie mit der rechten Maustaste auf **AG1 (primär)**, und klicken Sie dann auf **Dashboard anzeigen**.

	![Anzeigen des Verfügbarkeitsgruppen-Dashboards](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665532.gif)

1. Ihr **AlwaysOn-Dashboard** sollte ähnlich wie unten dargestellt aussehen. Es werden die Replikate, der Failovermodus jedes Replikats und der Synchronisierungsstatus angezeigt.

	![Verfügbarkeitsgruppen-Dashboard](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665533.gif)

1. Kehren Sie zum **Server-Manager** zurück, wählen Sie **Tools** aus, und starten Sie dann den **Failovercluster-Manager**.

1. Erweitern Sie **Cluster1.corp.contoso.com**, und erweitern Sie dann **Dienste und Anwendungen**. Wählen Sie **Rollen** aus, und beachten Sie, dass die Verfügbarkeitsgruppenrolle **AG1** erstellt wurde. Beachten Sie, dass AG1 keine IP-Adresse besitzt, über die Datenbankclients eine Verbindung mit der Verfügbarkeitsgruppe herstellen können, weil Sie keinen Listener konfiguriert haben. Sie können direkt mit dem primären Knoten eine Verbindung herstellen, um Lese-/ Schreibvorgänge auszuführen, und mit dem sekundären Knoten für reine Leseabfragen.

	![Verfügbarkeitsgruppe im Failovercluster-Manager](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665534.gif)

>[AZURE.WARNING]Versuchen Sie nicht, ein Failover der Verfügbarkeitsgruppe aus dem Failovercluster-Manager heraus durchzuführen. Alle Failovervorgänge sollten aus dem **AlwaysOn-Dashboard** in SSMS ausgeführt werden. Weitere Informationen finden Sie unter [Einschränkungen für die Verwendung des WSFC-Failovercluster-Managers mit Verfügbarkeitsgruppen](https://msdn.microsoft.com/library/ff929171.aspx).

## Nächste Schritte
Sie haben nun erfolgreich SQL Server AlwaysOn implementiert, indem Sie eine Verfügbarkeitsgruppe in Azure erstellt haben. Informationen zum Konfigurieren eines Listeners für diese Verfügbarkeitsgruppe finden Sie unter [Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

Weitere Informationen zur Verwendung von SQL Server in Azure finden Sie unter [SQL Server auf virtuellen Azure-Computern](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_1125_2015-->