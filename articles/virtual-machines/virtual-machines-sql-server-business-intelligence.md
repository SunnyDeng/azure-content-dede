<properties 
	pageTitle="SQL Server Business Intelligence | Microsoft Azure"
	description="In diesem Thema werden die mit dem klassischen Bereitstellungsmodell erstellten Ressourcen verwendet, um die verfügbaren Business Intelligence (BI)-Funktionen für SQL Server auf Azure Virtual Machines (VMs) zu beschreiben."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth" />

# SQL Server-Business Intelligence in Azure Virtual Machines

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.
 
 
Der Microsoft Azure Virtual Machine-Katalog verfügt über Images, die SQL Server-Installationen enthalten. Die in den Katalogimages unterstützten SQL Server-Editionen sind die gleichen Installationsdateien, die Sie auf lokalen Computern und virtuellen Computern installieren können. In diesem Thema sind die SQL Server Business Intelligence (BI)-Features zusammengefasst, die in den Images installiert sind, und es enthält die Konfigurationsschritte, die nach dem Bereitstellen eines virtuellen Computers erforderlich sind. Außerdem werden in diesem Thema die unterstützten Bereitstellungstopologien für BI-Funktionen und bewährte Methoden beschrieben.

## Lizenzaspekte

Es gibt zwei Möglichkeiten, SQL Server auf Microsoft Azure Virtual Machines zu lizenzieren:

1. Lizenzmobilitätsvorteile, die Teil der Software Assurance sind. Weitere Informationen finden Sie unter [Lizenzmobilität durch Software Assurance für Azure](http://azure.microsoft.com/pricing/license-mobility/).

1. Stundenbasierte Abrechnung für Azure Virtual Machines mit Installation von SQL Server. Siehe Abschnitt "SQL Server" unter [Virtuelle Computer Preise](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Weitere Informationen zur Lizenzierung und zu aktuellen Preisen finden Sie unter [Häufig gestellte Fragen zur Lizenzierung von Virtual Machines](http://azure.microsoft.com/pricing/licensing-faq/%20).

## Im Azure Virtual Machine-Katalog verfügbare SQL Server-Images

Im Microsoft Azure Virtual Machine-Katalog sind verschiedene Images zu finden, die Microsoft SQL Server enthalten. Die Software, die auf den Images für die virtuellen Computer installiert ist, variiert je nach Version des Betriebssystems und der Version von SQL Server. Die Liste der Images, die im Katalog mit den virtuellen Azure-Computern verfügbar ist, ändert sich häufig.

![SQL-Image in Azure VM-Katalog](./media/virtual-machines-sql-server-business-intelligence/IC741367.png)

![PowerShell](./media/virtual-machines-sql-server-business-intelligence/IC660119.gif) Mit dem folgenden PowerShell-Skript wird die Liste mit den Azure-Images zurückgegeben, bei denen "SQL-Server" Teil des Namens ("ImageName") ist:

	# assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure portal.
	
	$subscriptionID = ""    # REQUIRED: Provide your subscription ID.
	$subscriptionName = "" # REQUIRED: Provide your subscription name.
	$thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
	$certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert
	
	Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID
	
	Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
	Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
	get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description
	
	Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2012"
	Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
	get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2012*"} | select imagename,category, location, label, description

Weitere Informationen zu Editionen und Features, die von SQL Server unterstützt werden, finden Sie hier:

- [SQL Server-Editionen](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [Von den Editionen von SQL Server 2014 unterstützte Features](https://msdn.microsoft.com/library/cc645993.aspx)

### BI-Features, die auf den Images im SQL Server Virtual Machine-Katalog installiert sind

In der folgenden Tabelle sind die Business Intelligence-Features zusammengefasst, die in den allgemeinen Images für SQL Server im Microsoft Azure Virtual Machine-Katalog installiert sind:

- SQL Server 2014 RTM Enterprise

- SQL Server 2014 Standard

- SQL Server 2012 SP2 Enterprise

- SQL Server 2012 SP2 Standard

|BI-Funktion von SQL Server|In Katalogimage installiert|Hinweise|
|---|---|---|
|**Reporting Services – Einheitlicher Modus**|Ja|Installiert, erfordert aber eine Konfiguration, einschließlich Berichts-Manager-URL. Siehe Abschnitt [Konfigurieren von Reporting Services](#configure-reporting-services).|
|**Reporting Services – SharePoint-Modus**|Nein|Das Image des Microsoft Azure Virtual Machine-Katalogs enthält weder SharePoint noch SharePoint-Installationsdateien. <sup>1</sup>|
|**Analysis Services – Mehrdimensional und Data Mining (OLAP)**|Ja|Als standardmäßige Analysis Services-Instanz installiert und konfiguriert|
|**Analysis Services – Tabellarisch**|Nein|In SQL Server 2012- und 2014-Images unterstützt, aber nicht standardmäßig installiert. Installieren Sie eine weitere Instanz von Analysis Services. Siehe Abschnitt „Installieren anderer SQL Server-Dienste und -Features“ in diesem Thema.|
|**Analysis Services Power Pivot für SharePoint**|Nein|Das Image des Microsoft Azure Virtual Machine-Katalogs enthält weder SharePoint noch SharePoint-Installationsdateien. <sup>1</sup>|

<sup>1</sup> Weitere Informationen zu SharePoint und virtuellen Azure-Computern finden Sie unter [Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) und [SharePoint Deployment on Windows Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598) (SharePoint-Bereitstellung auf Microsoft Azure Virtual Machines, in englischer Sprache).

![PowerShell](./media/virtual-machines-sql-server-business-intelligence/IC660119.gif) Führen Sie den folgenden PowerShell-Befehl aus, um eine Liste mit den installierten Diensten abzurufen, bei denen der Dienstname "SQL" enthält.

	get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## Allgemeine Empfehlungen und bewährte Methoden

- Die empfohlene Minimalgröße für einen virtuellen Computer bei Verwendung von SQL Server Enterprise Edition ist **A3**. **A4** wird als Größe für einen virtuellen Computer empfohlen, wenn SQL Server-BI-Bereitstellungen von Analysis Services und Reporting Services verwendet werden.

	Informationen zu den aktuellen Größen virtueller Computer finden Sie unter [Größen virtueller Computer unter Azure](virtual-machines-size-specs.md).

- Eine bewährte Methode für die Datenträgerverwaltung ist das Speichern von Daten-, Protokoll- und Sicherungsdateien auf anderen Laufwerken als **C:** und **D:**. Erstellen Sie beispielsweise die Datenträger **E:** und **F:** für Daten.

	- Die Laufwerk-Cacherichtlinie für das Standardlaufwerk **C:** ist für die Verwendung von Daten nicht optimal.
	
	- Das Laufwerk **D:** ist ein temporäres Laufwerk, das hauptsächlich für die Auslagerungsdatei verwendet wird. Das Laufwerk **D:** wird nicht beibehalten und nicht im BLOB-Speicher gespeichert. Bei Verwaltungsaufgaben, z. B. einer Änderung der Größe des virtuellen Computers, wird das Laufwerk **D:** zurückgesetzt. Es wird empfohlen, das Laufwerk **D:** **NICHT** für Datenbankdateien zu verwenden, einschließlich "tempdb".

	Weitere Informationen zum Erstellen und Anfügen von Datenträgern finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Computer](storage-windows-attach-disk.md).

- Beenden oder deinstallieren Sie Dienste, die Sie nicht verwenden möchten. Beenden oder deinstallieren Sie beispielsweise Analysis Services und SQL Server Integration Services, falls der virtuelle Computer nur für Reporting Services verwendet wird. Die folgende Abbildung enthält ein Beispiel für die Dienste, die standardmäßig gestartet werden.

	![SQL Server-Dienste](./media/virtual-machines-sql-server-business-intelligence/IC650107.gif)

	>[AZURE.NOTE]Das SQL Server-Datenbankmodul ist in den unterstützten BI-Szenarios erforderlich. Bei einer VM-Topologie mit Einzelserver muss das Datenbankmodul auf demselben virtuellen Computer ausgeführt werden.

	Weitere Informationen finden Sie unter [Deinstallieren von Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) und [Vorgehensweise: Deinstallieren einer Instanz von Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).

- Prüfen Sie **Windows-Update** auf neue "Wichtige Updates". Die Microsoft Azure Virtual Machine-Images werden häufig aktualisiert. Es kann aber sein, dass unter **Windows-Update** wichtige Updates bereitgestellt werden, nachdem das VM-Image zum letzten Mal aktualisiert wurde.

## Beispiele für Bereitstellungstopologien

Im Folgenden sind Beispielbereitstellungen angegeben, bei denen Microsoft Azure Virtual Machines verwendet werden. Die Topologien in diesen Diagrammen stellen nur einige der möglichen Topologien dar, die Sie mit SQL Server BI-Funktionen und Microsoft Azure Virtual Machines verwenden können.

### Einzelner virtueller Computer

Analysis Services, Reporting Services, SQL Server-Datenbankmodul und Datenquellen auf einem einzelnen virtuellen Computer.

![BI-IaaS-Szenario mit 1 virtuellem Computer](./media/virtual-machines-sql-server-business-intelligence/IC650108.gif)

### Zwei virtuelle Computer

- Analysis Services, Reporting Services und SQL Server-Datenbankmodul auf einem einzelnen virtuellen Computer. Diese Bereitstellung enthält die Berichtsserver-Datenbanken.

- Datenquellen auf einem zweiten virtuellen Computer. Der zweite virtuelle Computer enthält das SQL Server-Datenbankmodul als Datenquelle.

![BI-IaaS-Szenario mit 2 virtuellen Computern](./media/virtual-machines-sql-server-business-intelligence/IC650109.gif)

### Azur-Mischung – Daten in Azure SQL-Datenbank

- Analysis Services, Reporting Services und SQL Server-Datenbankmodul auf einem einzelnen virtuellen Computer. Diese Bereitstellung enthält die Berichtsserver-Datenbanken.

- Die Datenquelle ist Azure SQL-Datenbank.

![BI-IaaS-Szenarios mit VM und Azure SQL als Datenquelle](./media/virtual-machines-sql-server-business-intelligence/IC650110.gif)

### Hybrid – Lokale Daten

- In dieser Beispielbereitstellung werden Analysis Services, Reporting Services und das SQL Server-Datenbankmodul auf einem einzelnen virtuellen Computer ausgeführt. Der virtuelle Computer hostet die Berichtsserver-Datenbanken. Für den virtuellen Computer wird der Beitritt zu einer lokalen Domäne per Azure Virtual Networking oder mit einer anderen VPN-Tunnellösung durchgeführt.

- Die Datenquelle liegt lokal vor.

![BI-IaaS-Szenarios mit VM und lokalen Datenquellen](./media/virtual-machines-sql-server-business-intelligence/IC654384.gif)

## Konfiguration von Reporting Services – Einheitlicher Modus

Das Image für SQL Server im Katalog mit den virtuellen Computern enthält eine Installation von Reporting Services – Einheitlicher Modus. Der Berichtsserver ist aber nicht konfiguriert. Mit den Schritten in diesem Abschnitt wird der Reporting Services-Berichtsserver konfiguriert. Ausführlichere Informationen zur Konfiguration des einheitlichen Modus der Reporting Services finden Sie unter [Installieren des Reporting Services-Berichtsservers im einheitlichen Modus](https://msdn.microsoft.com/library/ms143711.aspx).

>[AZURE.NOTE]Ähnliche Inhalte, bei denen Windows PowerShell-Skripts zum Konfigurieren des Berichtsservers verwendet werden, finden Sie unter [Verwenden von PowerShell zum Erstellen einer Azure-VM mit einem Berichtsserver im einheitlichen Modus](virtual-machines-sql-server-create-native-mode-report-server-powershell.md).

### Herstellen einer Verbindung mit dem virtuellen Computer und Starten des Konfigurations-Managers für Reporting Services

Es gibt zwei allgemeine Workflows zum Herstellen einer Verbindung mit einem virtuellen Azure-Computer:

- Klicken Sie zum Herstellen einer Verbindung auf den Namen des virtuellen Computers und dann auf **Verbinden**. Eine Remotedesktopverbindung wird geöffnet, und der Computername wird automatisch eingefügt.

	![Verbindung mit virtuellem Azure-Computer herstellen](./media/virtual-machines-sql-server-business-intelligence/IC650112.gif)

- Stellen Sie per Windows-Remotedesktopverbindung eine Verbindung mit dem virtuellen Computer her. Auf der Benutzeroberfläche des Remotedesktops:

	1. Geben Sie den **Clouddienstnamen** als Computernamen ein.
	
	1. Geben Sie einen Doppelpunkt (:) und die Nummer des öffentlichen Ports ein, der für den TCP-Remotedesktopendpunkt konfiguriert wurde.
		
		Myservice.cloudapp.net:63133
		
		Weitere Informationen finden Sie unter [Computehostingoptionen in Azure?](http://www.windowsazure.com/manage/services/cloud-services/what-is-a-cloud-service/).

**Starten Sie den Konfigurations-Manager für Reporting Services.**

1. In **Windows Server 2012**:

1. Geben Sie auf dem Bildschirm **Start** den Text **Reporting Services** ein, um eine Liste mit Apps anzuzeigen.

1. Klicken Sie mit der rechten Maustaste auf **Konfigurations-Manager für Reporting Services**, und klicken Sie auf **Als Administrator ausführen**.

1. In **Windows Server 2008 R2**:

1. Klicken Sie auf **Start** und dann auf **Alle Programme**.

1. Klicken Sie auf **Microsoft SQL Server 2012**.

1. Klicken Sie auf **Konfigurationstools**.

1. Klicken Sie mit der rechten Maustaste auf **Konfigurations-Manager für Reporting Services**, und klicken Sie auf **Als Administrator ausführen**.

Oder

1. Klicken Sie auf **Start**.

1. Geben Sie im Dialogfeld **Programme/Dateien durchsuchen** den Text **Reporting Services** ein. Wenn auf dem virtuellen Computer Windows Server 2012 ausgeführt wird, geben Sie auf dem Startbildschirm von Windows Server 2012 den Text **Reporting Services** ein.

1. Klicken Sie mit der rechten Maustaste auf **Konfigurations-Manager für Reporting Services**, und klicken Sie auf **Als Administrator ausführen**.

	![Nach SSRS-Konfigurations-Manager suchen](./media/virtual-machines-sql-server-business-intelligence/IC650113.gif)

### Konfigurieren von Reporting Services

**Dienstkonto und Webdienst-URL:**

1. Vergewissern Sie sich, dass **Servername** dem Namen des lokalen Servers entspricht, und klicken Sie auf **Verbinden**.

1. Beachten Sie, dass **Berichtsserver-Datenbankname** leer ist. Die Datenbank wird erstellt, nachdem die Konfiguration abgeschlossen ist.

1. Stellen Sie sicher, dass für **Berichtsserverstatus** der Status **Gestartet** angezeigt wird. Wenn Sie den Dienst in Windows Server-Manager überprüfen möchten, ist der Dienst der Windows-Dienst **SQL Server Reporting Services**.

1. Klicken Sie auf **Dienstkonto**, und ändern Sie das Konto nach Bedarf. Wenn der virtuelle Computer in einer Umgebung verwendet wird, die keiner Domäne beigetreten ist, reicht das integrierte **ReportServer**-Konto aus. Weitere Informationen zum Dienstkonto finden Sie unter [Dienstkonto](https://msdn.microsoft.com/library/ms189964.aspx).

1. Klicken Sie im linken Bereich auf **Webdienst-URL**.

1. Klicken Sie auf **Übernehmen** , um die Standardwerte zu konfigurieren.

1. Sehen Sie sich die Option **URLs für Berichtsserver-Webdienst** an. Beachten Sie, dass der TCP-Standardport 80 lautet und Teil der URL ist. In einem späteren Schritt erstellen Sie einen Microsoft Azure Virtual Machine-Endpunkt für den Port.

1. Überprüfen Sie im Bereich **Ergebnisse**, ob die Aktionen erfolgreich abgeschlossen wurden.

**Datenbank:**

1. Klicken Sie im linken Bereich auf **Datenbank**.

1. Klicken Sie auf **Datenbank ändern**.

1. Stellen Sie sicher, dass **Neue Berichtsserver-Datenbank erstellen** ausgewählt ist, und klicken Sie dann auf "Weiter".

1. Überprüfen Sie die Option **Servername**, und klicken Sie auf **Verbindung testen**.

1. Wenn das Ergebnis **Die Testverbindung war erfolgreich** lautet, klicken Sie auf **OK** und dann auf **Weiter**.

1. Beachten Sie, dass der Datenbankname **ReportServer** lautet und der **Berichtsservermodus** auf **Einheitlich** festgelegt ist. Klicken Sie anschließend auf **Weiter**.

1. Klicken Sie auf der Seite **Anmeldeinformationen** auf **Weiter**.

1. Klicken Sie auf der Seite **Zusammenfassung** auf **Weiter**.

1. Klicken Sie auf der Seite **Fortsetzen und Fertigstellen** auf **Weiter**.

**Berichts-Manager-URL:**

1. Klicken Sie im linken Bereich auf **Berichts-Manager-URL**.

1. Klicken Sie auf **Übernehmen**.

1. Überprüfen Sie im Bereich **Ergebnisse**, ob die Aktionen erfolgreich abgeschlossen wurden.

1. Klicken Sie auf **Beenden**.

Informationen zu Berichtsserverberechtigungen finden Sie unter [Erteilen von Berechtigungen für einen Berichtsserver im einheitlichen Modus](https://msdn.microsoft.com/library/ms156014.aspx).

### Wechseln zum lokalen Berichts-Manager

Navigieren Sie zum Berichts-Manager auf dem virtuellen Computer, um die Konfiguration zu überprüfen.

1. Starten Sie Internet Explorer auf dem virtuellen Computer mit Administratorrechten.

1. Navigieren Sie auf dem virtuellen Computer zu http://localhost/reports.

### So stellen Sie eine Verbindung mit dem Remote-Berichts-Manager her

Wenn Sie über einen Remotecomputer eine Verbindung mit dem Berichts-Manager herstellen möchten, erstellen Sie einen neuen TCP-Endpunkt für den virtuellen Computer. Standardmäßig überwacht der Berichtsserver **Port 80** auf HTTP-Anforderungen. Wenn Sie die Berichtsserver-URLs für die Verwendung eines anderen Ports konfigurieren, müssen Sie diese Portnummer bei den folgenden Schritten angeben.

1. Erstellen Sie einen Endpunkt für den virtuellen Computer von TCP-Port 80. Weitere Informationen finden Sie im Abschnitt [Endpunkte und Firewallports von virtuellen Computern](#virtual-machine-endpoints-and-firewall-ports) in diesem Dokument.

1. Öffnen Sie Port 80 in der Firewall des virtuellen Computers.

1. Navigieren Sie zum Berichts-Manager, indem Sie den **DNS-Namen** von Azure Virtual Machine als Servernamen in der URL verwenden. Beispiel:

	**Berichts-Manager**: http://uebi.cloudapp.net/reportserver **Berichtsserver**: http://uebi.cloudapp.net/reports

	[Konfigurieren einer Firewall für den Zugriff auf den Berichtsserver](https://technet.microsoft.com/library/bb934283.aspx)

### So erstellen und veröffentlichen Sie Berichte auf der Azure Virtual Machine

In der folgenden Tabelle sind einige der Optionen zusammengefasst, mit denen vorhandene Berichte von einem lokalen Computer auf dem Berichtsserver veröffentlicht werden können, der auf dem virtuellen Microsoft Azure-Computer gehostet wird:

- **Berichts-Generator**: Der virtuelle Computer umfasst die ClickOnce-Version von Microsoft SQL Server-Berichts-Generator. So starten Sie den Berichts-Generator das erste Mal auf dem virtuellen Computer:
											
	1. Starten Sie Ihren Browser mit Administratorrechten.
	
	1. Navigieren Sie zum Berichts-Manager auf dem virtuellen Computer, und klicken Sie im Menüband auf **Berichts-Generator**.
	
	Weitere Informationen finden Sie unter [Installation, Deinstallation und Unterstützung des Berichts-Generators](https://technet.microsoft.com/library/dd207038.aspx).

- **SQL Server Data Tools: Virtueller Computer**: SQL Server Data Tools ist auf dem virtuellen Computer installiert. Mit dieser Sammlung von Tools können **Berichtsserverprojekte** und Berichte auf dem virtuellen Computer erstellt werden. SQL Server Data Tools kann die Berichte im Berichtsserver auf dem virtuellen Computer veröffentlichen.

- **SQL Server Data Tools: Remote**: Erstellen Sie auf Ihrem lokalen Computer in SQL Server Data Tools ein Reporting Services-Projekt, das Reporting Services-Berichte enthält. Konfigurieren Sie das Projekt so, dass es eine Verbindung mit dem Webdienst-URL herstellt.

	![SSDT-Projekteigenschaften für SSRS-Projekt](./media/virtual-machines-sql-server-business-intelligence/IC650114.gif)

- Erstellen Sie eine VHD-Festplatte, die Berichte enthält. Laden Sie das Laufwerk dann hoch, und fügen Sie es an.

	1. Erstellen Sie eine VHD-Festplatte auf dem lokalen Computer, der die Berichte enthält.
	
	1. Erstellen Sie ein Verwaltungszertifikat, und installieren Sie es.
	
	1. Laden Sie die VHD-Datei mit dem Cmdlet "Add-AzureVHD" nach Azure hoch ([Erstellen und Hochladen einer Windows Server-VHD nach Azure](virtual-machines-create-upload-vhd-windows-server.md)).
	
	1. Fügen Sie den Datenträger an den virtuellen Computer an.

## Installieren anderer SQL Server-Dienste und -Features

Führen Sie den SQL Server-Setup-Assistenten aus, um zusätzliche SQL Server-Dienste zu installieren, z. B. Analysis Services im tabellarischen Modus. Die Setupdateien befinden sich auf dem lokalen Datenträger des virtuellen Computers.

1. Klicken Sie auf **Start** und dann auf **Alle Programme**.

1. Klicken Sie auf **Microsoft SQL Server 2014** oder **Microsoft SQL Server 2012** und dann auf **Konfigurationstools**.

1. Klicken Sie auf **SQL Server-Installationscenter**.

Oder führen Sie „C:\\SQLServer\_12.0\_full\\setup.exe“ oder „C:\\SQLServer\_11.0\_full\\setup.exe“ aus.

>[AZURE.NOTE]Beim ersten Ausführen des SQL Server-Setups können mehr Setupdateien heruntergeladen werden und erfordern einen Neustart des virtuellen Computers und einen Neustart des SQL Server-Setups.
>
>Wenn Sie das von der Microsoft Azure Virtual Machine ausgewählte Image wiederholt anpassen müssen, können Sie erwägen, ein eigenes SQL Server-Image zu erstellen. Die Analysis Services-SysPrep-Funktionalität wurde mit SQL Server 2012 SP1 CU2 aktiviert. Weitere Informationen finden Sie unter [Überlegungen zur Installation von SQL Server mit SysPrep](https://msdn.microsoft.com/library/ee210754.aspx).

### So installieren Sie Analysis Services im tabellarischen Modus

Die Schritte in diesem Abschnitt sind eine **Zusammenfassung** der Installation von Analysis Services im tabellarischen Modus. Weitere Informationen finden Sie unter den folgenden Links:

- [Installieren von Analysis Services im Tabellenmodus](https://msdn.microsoft.com/library/hh231722.aspx)

- [Tabellenmodellierung (Adventure Works-Tutorial)](https://technet.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**So installieren Sie Analysis Services im tabellarischen Modus**

1. Klicken Sie im SQL Server-Installations-Assistenten im linken Bereich auf **Installation**, und klicken Sie dann auf **Neue eigenständige SQL Server-Installation oder Hinzufügen von Features zu einer vorhandenen Installation**.

	- Navigieren Sie nach "C:\\SQLServer\_12.0\_full" oder "C:\\SQLServer\_11.0\_full", wenn **Ordner suchen** angezeigt wird, und klicken Sie dann auf **OK**.

1. Klicken Sie auf der Seite mit den Produktupdates auf **Weiter**.

1. Wählen Sie auf der Seite **Installationstyp** die Option **SQL Server neu installieren** aus, und klicken Sie auf **Weiter**.

1. Klicken Sie auf der Seite **Setuprolle** auf **SQL Server Features Installation**.

1. Klicken Sie auf der Seite **Featureauswahl** auf **Analysis Services**.

1. Geben Sie auf der Seite **Instanzkonfiguration** einen beschreibenden Namen, z. B. **Tabellarisch**, in die Textfelder **Benannte Instanz** und **Instanz-ID** ein.

1. Wählen Sie auf der Seite **Analysis Services-Konfiguration** die Option **Tabellarischer Modus** aus. Fügen Sie den aktuellen Benutzer der Liste mit den Administratorberechtigungen hinzu.

1. Beenden Sie den SQL Server-Installations-Assistenten, und schließen Sie ihn.

## Analysis Services-Konfiguration

### Remotezugriff auf Analysis Services-Server

Der Analysis Services-Server unterstützt nur die Windows-Authentifizierung. Um den Remotezugriff auf Analysis Services mit Clientanwendungen durchzuführen, z. B. SQL Server Management Studio oder SQL Server Data Tools, muss der virtuelle Computer Ihrer lokalen Domäne per Azure Virtual Networking beitreten. Weitere Informationen finden Sie unter [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Eine **Standardinstanz** von Analysis Services überwacht TCP-Port **2383**. Öffnen Sie den Port in der Firewall für die virtuellen Computer. Eine benannte Clusterinstanz von Analysis Services überwacht ebenfalls Port **2383**.

Für eine **benannte Instanz** von Analysis Services muss der SQL Server-Browser-Dienst den Portzugriff verwalten. Für die SQL Server-Browser-Standardkonfiguration wird Port **2382** verwendet.

Öffnen Sie in der Firewall für die virtuellen Computer Port **2382**, und erstellen Sie einen statischen Analysis Services-Port für die benannte Instanz.

1. Führen Sie den folgenden Befehl mit Administratorrechten aus, um Ports zu überprüfen, die auf dem virtuellen Computer bereits verwendet werden, sowie den Prozess, von dem die Ports genutzt werden:

		netstat /ao

1. Verwenden Sie SQL Server Management Studio, um einen statischen Analysis Services-Port für die benannte Instanz zu erstellen, indem Sie den Wert „Port“ in den allgemeinen Eigenschaften der tabellarischen Analysis Services aktualisieren. Weitere Informationen finden Sie unter "Verwenden eines festen Ports für eine Standardinstanz oder eine benannte Instanz von Analysis Services" im Thema [Konfigurieren der Windows-Firewall, um den Zugriff auf Analysis Services zuzulassen](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).

1. Starten Sie die tabellarische Instanz des Analysis Services-Diensts neu.

Weitere Informationen finden Sie im Abschnitt **Endpunkte und Firewallports von virtuellen Computern** in diesem Dokument.

## Endpunkte und Firewallports von virtuellen Computern

In diesem Abschnitt werden die zu erstellenden Microsoft Azure Virtual Machine-Endpunkte und die Ports zusammengefasst, die in der Firewall für den virtuellen Computer geöffnet werden müssen. In der folgenden Tabelle sind die **TCP**-Ports aufgeführt, für die Endpunkte erstellt werden müssen, sowie die Ports, die in der Firewall von virtuellen Computern geöffnet werden müssen.

- Wenn Sie einen einzelnen virtuellen Computer verwenden und die folgenden beiden Elemente wahr sind, müssen Sie keine VM-Endpunkte erstellen und die Ports in der Firewall auf dem virtuellen Computer nicht öffnen.

	- Sie stellen keine Remoteverbindung mit den SQL Server-Features auf dem virtuellen Computer her. Das Einrichten einer Remotedesktopverbindung mit dem virtuellen Computer und das lokale Zugreifen auf die SQL Server-Features auf dem virtuellen Computer wird in Bezug auf die SQL Server-Features nicht als Remoteverbindung angesehen.
	
	- Sie führen für den virtuellen Computer keinen Beitritt zu einer lokalen Domäne durch, indem Sie Azure Virtual Networking oder eine andere VPN-Tunnellösung verwenden.

- Gehen Sie wie folgt vor, wenn der virtuelle Computer keiner Domäne beigetreten ist, Sie aber eine Remoteverbindung mit den SQL Server-Features auf dem virtuellen Computer herstellen möchten:

	- Öffnen Sie die Ports in der Firewall auf dem virtuellen Computer.
	
	- Erstellen Sie die Endpunkte des virtuellen Computers für die genannten Ports (*).

- Wenn für den virtuellen Computer der Beitritt zu einer Domäne mit einer VPN-Tunnellösung durchgeführt wird, z. B. Azure Virtual Networking, sind die Endpunkte nicht erforderlich. Öffnen Sie aber die Ports in der Firewall auf dem virtuellen Computer.

	|Port|Typ|Beschreibung|
|---|---|---|
|**80**|TCP|Remotezugriff auf den Berichtsserver (*).| |**1433**|TCP|SQL Server Management Studio (*).| |**1434**|UDP|SQL Server-Browser. Ist erforderlich, wenn der virtuelle Computer einer Domäne beigetreten ist.|
|**2382**|TCP|SQL Server-Browser.|
|**2383**|TCP|SQL Server Analysis Services-Standardinstanz und gruppierte benannte Instanzen.|
|**Benutzerdefiniert**|TCP|Erstellen Sie einen statischen Analysis Services-Port für die benannte Instanz. Wählen Sie hierfür eine Portnummer aus, und entsperren Sie dann die Portnummer in der Firewall.|

Weitere Informationen zum Erstellen von Endpunkten finden Sie hier:

- Erstellen von Endpunkten: [Einrichten von Endpunkten für einen virtuellen Computer](virtual-machines-set-up-endpoints.md).

- SQL Server: Abschnitt "Konfigurationsschritte zum Verbinden des virtuellen Computers mit SQL Server Management Studio" unter [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](virtual-machines-provision-sql-server.md)

Im folgenden Diagramm sind die Ports dargestellt, die in der Firewall des virtuellen Computers geöffnet werden müssen, um den Remotezugriff auf die Funktionen und Komponenten auf dem virtuellen Computer zu ermöglichen.

![Zu öffnende Ports für BI-Anwendungen in Azure-VMs](./media/virtual-machines-sql-server-business-intelligence/IC654385.gif)

## Ressourcen

- Informieren Sie sich über die Unterstützungsrichtlinie für Microsoft-Serversoftware, die in der Azure Virtual Machine-Umgebung verwendet wird. Im folgenden Thema wird die Unterstützung für Features wie BitLocker, Failoverclustering und Netzwerklastenausgleich zusammengefasst: [Microsoft Server Software-Support für Microsoft Azure virtuelle Maschinen](http://support.microsoft.com/kb/2721672)

- [Übersicht zu SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md)

- [Virtuelle Computer](http://azure.microsoft.com/documentation/services/virtual-machines/)

- [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](virtual-machines-provision-sql-server.md)

- [Anfügen eines Datenträgers an einen virtuellen Computer](storage-windows-attach-disk.md)

- [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](virtual-machines-migrate-onpremises-database.md)

- [Bestimmen des Servermodus einer Analysis Services-Instanz](https://msdn.microsoft.com/library/gg471594.aspx)

- [Mehrdimensionale Modellierung (Adventure Works-Tutorial)](https://technet.microsoft.com/library/ms170208.aspx)

- [Azure-Dokumentationscenter](http://azure.microsoft.com/documentation/)

- [Verwenden von Power BI in einer Hybridumgebung](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [Submit feedback and contact information through Microsoft SQL Server Connect](https://connect.microsoft.com/SQLServer/Feedback)

### Community-Inhalte

- [Verwalten von Azure SQL-Datenbank mit PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)

<!---HONumber=AcomDC_1125_2015-->