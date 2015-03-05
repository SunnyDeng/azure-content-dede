<properties 
	pageTitle="Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken" 
	description="Hier erhalten Sie Informationen zum Erstellen einer SharePoint-Intranetfarm in einer Hybrid Cloud-Umgebung für Entwicklungs- oder IT Pro-Tests." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="josephd"/>


#Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken

In diesem Thema lernen Sie Schritt für Schritt, wie Sie eine Hybrid Cloud-Umgebung zum Testen einer in Microsoft Azure gehosteten Intranet-Sharepoint-Farm erstellen. Hier sehen Sie die daraus resultierende Konfiguration.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_3.png)
 
Mit dieser Konfiguration wird ein SharePoint in einer Azure-Produktionsumgebung an Ihrem Speicherort im Internet simuliert. Dieser besteht aus:

- einem vereinfachten lokalen Netzwerk (dem Corpnet-Subnetz).
- einem standortübergreifenden virtuellen Netzwerk (TestVNET), das in Azure gehostet wird.
- einer Site-to-Site-VPN-Verbindung.
- einer zwei-Ebenen-SharePoint-Farm und einem sekundären Domänencontroller im virtuellen Netzwerk TestVNET.

Diese Konfiguration bietet eine Grundlage und einen allgemeinen Startpunkt, an dem Sie folgende Möglichkeiten haben:

- Entwickeln und Testen von Anwendungen auf einer SharePoint-Intranetfarm in einer Hybrid Cloud-Umgebung.
- Ausführen und Testen dieser Hybrid Cloud-basierten IT-Arbeitsauslastung.

Die Einrichtung dieser Hybrid Cloud-Testumgebung besteht aus drei Phasen:

1.	Einrichten der Hybrid Cloud-Umgebung für Testzwecke.
2.	Konfigurieren des SQL Server-Computers (SQL1).
3.	Konfigurieren des SharePoint-Servers (SP1).

Wenn Sie noch kein Azure-Abonnement haben, können Sie sich unter [Azure ausprobieren](http://www.windowsazure.com/pricing/free-trial/) für eine kostenlose Testversion anmelden. Wenn Sie ein MSDN-Abonnement haben, siehe [Azure-Leistungen für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

##Phase 1: Einrichten der Hybrid Cloud-Umgebung

Folgen Sie den Anweisungen im Thema [Einrichten einer Hybrid Cloud-Umgebung für Testzwecke](../virtual-networks-setup-hybrid-cloud-environment-testing/). Da in dieser Testumgebung der APP1-Server im Subnetz des Unternehmensnetzwerks nicht erforderlich ist, können Sie ihn jetzt herunterfahren.

Dies ist die aktuelle Konfiguration.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_1.png)
 
##Phase 2: Konfigurieren des SQL Server-Computers (SQL1)

Starten Sie bei Bedarf im Azure-Verwaltungsportal den Computer DC2.

Erstellen Sie zunächst eine Remotedesktopverbindung mit DC2. Verwenden Sie dafür die Anmeldeinformationen für CORP\User1.

Erstellen Sie dann ein SharePoint-Farm-Administratorkonto. Öffnen Sie auf DC2 eine Windows PowerShell-Eingabeaufforderung auf Administratorebene, und führen Sie den folgenden Befehl aus.

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

Wenn Sie dazu aufgefordert werden, geben Sie das Kennwort für das Konto "SPFarmAdmin" an. Wählen Sie ein sicheres Kennwort, und hinterlegen Sie es an einem sicheren Ort.

Erstellen Sie dann in der Azure PowerShell-Eingabeaufforderung auf dem lokalen Computer mit den folgenden Befehlen einen virtuellen Azure-Computer für SQL1.

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<A password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Stellen Sie dann eine Verbindung zum neuen virtuellen Computer für SQL1 her *using the local administrator account*.

1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf **Virtuelle Computer** und anschließend in der Spalte "Status" für SQL1 auf **Wird ausgeführt**.
2.	Klicken Sie in der Taskleiste auf **Verbinden**. 
3.	Wenn Sie dazu aufgefordert werden, SQL1.rdp zu öffnen, klicken Sie auf **Öffnen**.
4.	Wenn ein Meldungsfeld für eine Remotedesktopverbindung angezeigt wird, klicken Sie auf **Verbinden**.
5.	Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, verwenden Sie:
	- Name: **SQL1\\**[Name des lokalen Administratorkontos]
	- Kennwort: [Kennwort des lokalen Administratorkontos]
6.	Wenn ein Meldungsfeld für eine Remotedesktopverbindung angezeigt wird, die auf Zertifikate verweist, klicken Sie auf **Ja**.

Konfigurieren Sie anschließend Windows-Firewall-Regeln, um Datenverkehr zum Testen der allgemeinen Konnektivität und von SQL Server zuzulassen. Führen Sie in der Windows PowerShell-Eingabeaufforderung von SQL1 die folgenden Befehle auf Administratorebene aus.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Der Ping-Befehl sollte zu vier erfolgreichen Antworten von der IP-Adresse 10.0.0.1 führen.

Fügen Sie anschließend den zusätzlichen Datenträger als neues Volume mit dem Laufwerkbuchstaben F: hinzu.

1.	Klicken Sie im linken Bereich des Server-Managers auf **Datei- und Speicherdienste** und anschließend auf **Datenträger**.
2.	Klicken Sie im Inhaltsbereich in der Gruppe **Datenträger** auf **Datenträger 2** (wobei **Partition** auf **Unbekannt** gesetzt sein muss).
3.	Klicken Sie auf **Aufgaben** und anschließend auf **Neues Volume**.
4.	Klicken Sie auf der Seite "Voraussetzungen" des Assistenten für neue Volumes auf **Weiter**.
5.	Klicken Sie auf der Seite "Server und Datenträger auswählen" auf **Datenträger 2** und anschließend auf **Weiter**. Wenn Sie dazu aufgefordert werden, klicken Sie auf **OK**.
6.	Klicken Sie auf der Seite "Geben Sie die Größe des Volumes an" auf **Weiter**.
7.	Klicken Sie auf der Seite "Einem Laufwerkbuchstaben oder Ordner zuweisen" auf **Weiter**.
8.	Klicken Sie auf der Seite "Dateisystemeinstellungen auswählen" auf **Weiter**.
9.	Klicken Sie auf der Seite "Auswahl bestätigen" auf **Erstellen**.
10.	Wenn Sie fertig sind, klicken Sie auf **Schließen**.

Führen Sie in der Windows PowerShell-Eingabeaufforderung von SQL1 die folgenden Befehle aus:

	md f:\Data
	md f:\Log
	md f:\Backup

Konfigurieren Sie anschließend SQL Server 2014 so, dass das Laufwerk F: für neue Datenbanken und Berechtigungen für Benutzerkonten verwendet werden kann.

1.	Geben Sie im Startbildschirm **SQL Server Management** ein, und klicken Sie auf **SQL Server 2014 Management Studio**.
2.	Klicken Sie unter **Mit Server verbinden** auf **Verbinden**.
3.	Klicken Sie im Strukturbereich "Objekt-Explorer" mit der rechten Maustaste auf **SQL1**, und klicken Sie anschließend auf **Eigenschaften**.
4.	Klicken Sie im Fenster **Servereigenschaften** auf **Datenbankeinstellungen**.
5.	Suchen Sie die **Datenbank-Standardspeicherorte**, und legen Sie die folgenden Werte fest: 
	- Geben Sie für **Daten** den Pfad **f:\Data** ein.
	- Geben Sie für **Protokolle** den Pfad **f:\Log** ein.
	- Geben Sie für **Sicherung** den Pfad **f:\Backup** ein.
	- Beachten Sie, dass diese Speicherorte nur für die neuen Datenbanken verwendet werden.
6.	Klicken Sie auf **OK**, um das Fenster zu schließen.
7.	Öffnen Sie im Strukturbereich **Objekt-Explorer** die Option **Sicherheit**.
8.	Klicken Sie mit der rechten Maustaste auf **Anmeldungen**, und klicken Sie dann auf **Neue Anmeldung**.
9.	Geben Sie unter **Benutzername** den Namen **CORP\User1** ein.
10.	Klicken Sie auf der Seite **Serverrollen** auf **Sysadmin** und anschließend auf **OK**.
11.	Klicken Sie im Strukturbereich **Objekt-Explorer** mit der rechten Maustaste auf **Anmeldungen** und anschließend auf **Neue Anmeldung**.
12.	Geben Sie auf der Seite **Allgemein** unter **Anmeldename** den Namen **CORP\SPFarmAdmin** ein.
13.	Wählen Sie auf der Seite **Serverrollen** die Option **dbcreator**, und klicken Sie dann auf **OK**.
14.	Schließen Sie Microsoft SQL Server Management Studio.

Dies ist die aktuelle Konfiguration.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_2.png)

 
##Phase 3: Konfigurieren des SharePoint-Servers (SP1)

Erstellen Sie zunächst in der Eingabeaufforderung von Azure PowerShell auf dem lokalen Computer mit den folgenden Befehlen einen virtuellen Azure-Computer für SP1.

	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<A password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$vm1=New-AzureVMConfig -Name SP1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Stellen Sie anschließend eine Verbindung mit dem virtuellen Computer für SP1 her. Verwenden Sie dafür die Anmeldeinformationen für CORP\User1.

Konfigurieren Sie anschließend eine Windows-Firewall-Regel, um Datenverkehr zum Testen der allgemeinen Konnektivität zuzulassen. Führen Sie in der Windows PowerShell-Eingabeaufforderung von SP1 die folgenden Befehle auf Administratorebene aus.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Der Ping-Befehl sollte zu vier erfolgreichen Antworten von der IP-Adresse 10.0.0.1 führen.

Konfigurieren Sie anschließend SP1 für eine neue SharePoint-Farm und eine Standard-Teamwebsite.

1.	Geben Sie auf dem Startbildschirm **SharePoint 2013-Produkte** ein, und klicken Sie dann auf **Konfigurations-Assistent für SharePoint 2013-Produkte**. Wenn Sie gefragt werden, ob Sie dem Programm erlauben möchten, Änderungen am Computer vorzunehmen, klicken Sie auf **Ja**.
2.	Klicken Sie auf der Seite "Willkommen bei den SharePoint-Produkten" auf **Weiter**. 
3.	Wenn ein Dialogfeld mit der Meldung angezeigt wird, dass während der Konfiguration einige Dienste möglicherweise neu gestartet werden müssen, klicken Sie auf **Ja**.
4.	Klicken Sie auf der Seite "Verbindung mit einer Serverfarm herstellen" auf **Eine neue Serverfarm erstellen**, und klicken Sie dann auf **Weiter**.
5.	Geben Sie auf der Seite "Einstellungen für die Konfigurationsdatenbank angeben" **sql1.corp.contoso.com** unter **Datenbankserver** ein, geben Sie unter **Benutzername** den Namen **CORP\SPFarmAdmin** ein, geben Sie unter **Kennwort** das Kennwort für das Konto "SPFarmAdmin" ein, und klicken Sie dann auf **Weiter**.
6.	Geben Sie auf der Seite "Farmsicherheitseinstellungen angeben" sowohl unter **Passphrase** als auch unter **Passphrase bestätigen** die Buchstabenkombination **P@ssphrase** ein, und klicken Sie dann auf **Weiter**.
7.	Klicken Sie auf der Seite "Webanwendung für die SharePoint-Zentraladministration konfigurieren" auf **Weiter**.
8.	Klicken Sie auf der Seite "Der Konfigurations-Assistent für SharePoint-Produkte wird abgeschlossen." auf **Weiter**. Das Abschließen des Konfigurations-Assistenten für SharePoint-Produkte kann einige Minuten dauern.
9.	Klicken Sie auf der Seite "Konfiguration erfolgreich" auf **Fertig stellen**. Nach dem Abschluss wird Internet Explorer mit einer Registerkarte namens "Assistent für die Farmerstkonfiguration" gestartet.
10.	Klicken Sie im Dialogfeld **Helfen Sie uns bei der Verbesserung von SharePoint** auf **Nein, ich möchte nicht teilnehmen** und anschließend auf **OK**.
11.	Klicken Sie unter **Wie möchten Sie die SharePoint-Farm konfigurieren?** auf **Assistenten starten**.
12.	Klicken Sie auf der Seite "SharePoint-Farm konfigurieren" unter **Dienstkonto** auf **Vorhandenes verwaltetes Konto verwenden**.
13.	Deaktivieren Sie unter **Dienste** alle Kontrollkästchen außer demjenigen neben **Statusdienst**, und klicken Sie dann auf **Weiter**. Vor der Fertigstellung könnte für eine Weile die Seite "In Bearbeitung" angezeigt werden.
14.	Geben Sie auf der Seite "Websitesammlung erstellen" unter **Titel und Beschreibung** > **Titel** den Titel **Contoso Corporation** ein, geben Sie die URL **http://sp1/** an, und klicken Sie dann auf **OK**. Vor der Fertigstellung könnte für eine Weile die Seite "In Bearbeitung" angezeigt werden. Mit diesem Schritt wird eine Teamwebsite mit der URL http://sp1 erstellt.
15.	Klicken Sie auf der Seite "Farmkonfigurations-Assistent" auf **Fertig stellen**. Auf der Registerkarte in Internet Explorer wird die Website "SharePoint 2013-Zentraladministration" angezeigt.
16.	Melden Sie sich am Computer CLIENT1 mit den Anmeldeinformationen für CORP\User1 an, und starten Sie Internet Explorer.
17.	Geben Sie in der Adressleiste **http://sp1/** ein, und drücken Sie dann die EINGABETASTE. Nun sollte die SharePoint-Teamwebsite angezeigt werden. Eventuell dauert das Rendern der Seite ein wenig.

Dies ist die aktuelle Konfiguration.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_3.png)
 
Ihre SharePoint-Intranetfarm kann jetzt in einer Hybrid Cloud-Umgebung getestet werden.

##Zusätzliche Ressourcen

[SharePoint in Azure-Infrastrukturdiensten](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[SharePoint-Serverfarm](../virtual-machines-sharepoint-farm-azure-preview/)

[Einrichten einer Hybrid Cloud-Umgebung zu Testzwecken](../virtual-networks-setup-hybrid-cloud-environment-testing/)

[Einrichten einer webbasierten LOB-Anwendung-Umgebung in einer Hybrid Cloud zu Testzwecken](../virtual-networks-setup-lobapp-hybrid-cloud-testing/)

[Einrichten der Office 365-Verzeichnissynchronisierung (DirSync) in einer Hybrid Cloud zu Testzwecken](../virtual-networks-setup-dirsync-hybrid-cloud-testing/)

<!--HONumber=45--> 
