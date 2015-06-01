<properties 
	pageTitle="Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken" 
	description="Erfahren Sie, wie Sie eine webbasierte Branchenanwendung in einer Hybrid Cloud-Umgebung für Tests durch IT-Spezialisten oder zu Entwicklungszwecken erstellen." 
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
	ms.date="03/04/2015" 
	ms.author="josephd"/>

# Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken

In diesem Thema werden die Schritte zum Erstellen einer Hybrid Cloud-Umgebung zum Testen einer in Microsoft Azure gehosteten Intranetbranchenanwendung erläutert. Die resultierende Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-set-up-LOB-App-hybrid-cloud-for-testing/CreateLOBAppHybridCloud_3.png)

Ein Beispiel für eine in Azure gehostete Produktionsbranchenanwendung finden Sie in der Architekturblaupause für **Branchenanwendungen** unter [Blaupausen für Software-Architekten](http://msdn.microsoft.com/dn630664).

Diese Konfiguration simuliert eine Branchenanwendung in einer Azure-Produktionsumgebung von Ihrem Standort im Internet. Sie besteht aus:

- Einem vereinfachten lokalen Netzwerk (Subnetz "Corpnet")
- Einem standortübergreifenden virtuellen in Azure gehosteten Netzwerk (TestVNET)
- Einer Site-to-Site-VPN-Verbindung
- Einem Branchenserver, einem SQL Server-Computer und einem sekundären Domänencontroller im virtuellen Netzwerk "TestVNET".

Diese Konfiguration bietet eine Grundlage und einen allgemeinen Ausgangspunkt für Folgendes:

- Entwickeln und Testen von Branchenanwendungen, die in Internetinformationsdienste (IIS) gehostet werden und über ein SQL Server 2014-Datenbank-Back-End in Azure verfügen.
- Testen dieser Hybrid Cloud-basierten IT-Arbeitsauslastung

Die Einrichtung dieser Hybrid Cloud-Testumgebung besteht aus drei Hauptphasen:

1.	Einrichten der Hybrid Cloud-Umgebung zu Testzwecken
2.	Konfigurieren des SQL Server-Computers (SQL1)
3.	Konfigurieren den Branchenservers (LOB1)

Wenn Sie noch kein Azure-Abonnement besitzen, können Sie sich unter [Azure testen](http://azure.microsoft.com/pricing/free-trial/) für eine kostenlose Testversion registrieren. Wenn Sie über ein MSDN-Abonnement verfügen, lesen Sie die Informationen unter [Azure-Vorteil für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Phase 1: Einrichten der Hybrid Cloud-Umgebung

Verwenden Sie die Anweisungen im Thema [Einrichten einer hybriden Cloud-Umgebung zum Testen](virtual-networks-setup-hybrid-cloud-environment-testing.md) . Da das Vorhandensein des APP1-Servers im Subnetz des Unternehmensnetzwerks in dieser Testumgebung nicht erforderlich ist, können Sie jetzt herunterfahren.

Dies ist die aktuelle Konfiguration.

![](./media/virtual-networks-set-up-LOB-App-hybrid-cloud-for-testing/CreateLOBAppHybridCloud_1.png)
 
## Phase 2: Konfigurieren des SQL Server-Computers (SQL1)

Starten Sie ggf. den Computer DC2 über das Azure-Verwaltungsportal.

Erstellen Sie dann in einer Azure PowerShell-Eingabeaufforderung auf dem lokalen Computer mit den folgenden Befehlen einen virtuellen Azure-Computer für SQL1. Füllen Sie vor dem Ausführen dieser Befehle die Variablenwerte aus, und entfernen Sie die Zeichen < und >.

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<The password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Stellen Sie dann *using the local administrator account* eine Verbindung mit dem neuen virtuellen Computer für SQL1 her.

1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf **Virtuelle Computer**, und klicken Sie dann in der Statusspalte für SQL1 auf **Wird ausgeführt**.
2.	Klicken Sie in der Taskleiste auf **Verbinden**. 
3.	Wenn Sie zum Öffnen von "SQL1.rdp" aufgefordert werden, klicken Sie auf **Öffnen**.
4.	Wenn ein Meldungsfeld für eine Remotedesktopverbindung angezeigt wird, klicken Sie auf **Verbinden**.
5.	Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, verwenden Sie:
	- Name: **SQL1**[Name des lokalen Administratorkontos]
	- Kennwort: [Kennwort des lokalen Administratorkontos]
6.	Wenn ein Meldungsfeld der Remotedesktopverbindung zu Zertifikaten angezeigt wird, klicken Sie auf **Ja**.

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
7.	Klicken Sie auf die Seite "Einem Laufwerkbuchstaben oder Ordner zuweisen" auf **Weiter**.
8.	Klicken Sie auf der Seite "Dateisystemeinstellungen auswählen" auf **Weiter**.
9.	Klicken Sie auf der Seite "Auswahl bestätigen" auf **Erstellen**.
10.	Wenn Sie fertig sind, klicken Sie auf **Schließen**.

Führen Sie die folgenden Befehle in der Windows PowerShell-Eingabeaufforderung auf SQL1 aus:

	md f:\Data
	md f:\Log
	md f:\Backup

Konfigurieren Sie anschließend SQL Server 2014 so, dass das Laufwerk F: für neue Datenbanken und Berechtigungen für Benutzerkonten verwendet werden kann.

1.	Geben Sie im Startbildschirm **SQL Server Management** ein, und klicken Sie auf **SQL Server 2014 Management Studio**.
2.	Klicken Sie unter **Mit Server verbinden** auf **Verbinden**.
3.	Klicken Sie im Strukturbereich "Objekt-Explorer" mit der rechten Maustaste auf **SQL1**, und klicken Sie anschließend auf **Eigenschaften**.
4.	Klicken Sie im Fenster **Servereigenschaften** auf **Datenbankeinstellungen**.
5.	Suchen Sie den Eintrag **Standardspeicherorte für Datenbank**, und legen Sie die folgenden Werte fest: 
	- Geben Sie für **Daten** den Pfad **f:\Data** ein.
	- Geben Sie für **Protokoll** den Pfad **f:\Log** ein.
	- Geben Sie für **Sicherung ** den Pfad **f:\Backup** ein.
	- Hinweis: Nur neue Datenbanken verwenden diese Speicherorte.
6.	Klicken Sie auf **OK**, um das Fenster zu schließen.
7.	Öffnen Sie im Strukturbereich **Objekt-Explorer** den Abschnitt **Sicherheit**.
8.	Klicken Sie mit der rechten Maustaste auf **Anmeldungen**, und klicken Sie dann auf **Neue Anmeldung**.
9.	Geben Sie in das Feld **Anmeldename** den Namen **CORP\User1** ein.
10.	Klicken Sie auf der Seite **Serverrollen** auf **Sysadmin** und anschließend auf **OK**.
11.	Schließen Sie Microsoft SQL Server Management Studio.

Dies ist die aktuelle Konfiguration.

![](./media/virtual-networks-set-up-LOB-App-hybrid-cloud-for-testing/CreateLOBAppHybridCloud_2.png)
 
## Phase 3: Konfigurieren den Branchenservers (LOB1)

Führen Sie als Erstes auf dem lokalen Computer die folgenden Befehlen in der Azure PowerShell-Eingabeaufforderung aus, um einen virtuellen Azure-Computer für LOB1 zu erstellen.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<The password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name LOB1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Stellen Sie dann unter Verwendung der Anmeldeinformationen des Kontos "CORP\User1" eine Verbindung mit dem virtuellen Computer für LOB1 her.

Konfigurieren Sie anschließend eine Windows-Firewall-Regel, um Datenverkehr für grundlegende Konnektivitätstests zuzulassen. Führen Sie auf LOB1 in einer Windows PowerShell-Eingabeaufforderung auf Administratorebene die folgenden Befehle aus.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Der ping-Befehl muss in vier erfolgreichen Antworten von der IP-Adresse 10.0.0.1 resultieren.

Konfigurieren Sie dann LOB1 für IIS, und testen Sie den Zugriff von CLIENT1.

1.	Führen Sie Server-Manager aus, und klicken Sie dann auf **Rollen und Features hinzufügen**.
2.	Klicken Sie auf der Seite "Vorbemerkungen" auf **Weiter**.
3.	Klicken Sie auf der Seite "Installationstyp auswählen" auf **Weiter**.
4.	Klicken Sie auf der Seite "Zielserver auswählen" auf **Weiter**.
5.	Klicken Sie auf der Seite "Serverrollen" in der Liste **Rollen** auf **Webserver (IIS)**.
6.	Klicken Sie bei der entsprechenden Aufforderung auf **Features hinzufügen** und anschließend auf **Weiter**.
7.	Klicken Sie auf der Seite "Features auswählen" auf **Weiter**.
8.	Klicken Sie auf der Seite "Webserver (IIS)" auf **Weiter**.
9.	Aktivieren oder deaktivieren Sie auf der Seite "Rollendienste auswählen" die Kontrollkästchen für die Dienste, die Sie zum Testen der Branchenanwendung benötigen, und klicken Sie dann auf **Weiter**.
10.	Klicken Sie auf der Seite "Installationsauswahl bestätigen" auf **Installieren**.
11.	Warten Sie, bis die Installation der Komponenten abgeschlossen ist, und klicken Sie dann auf **Schließen**.
12.	Melden Sie sich am Computer CLIENT1 mit den Anmeldeinformationen des Kontos "CORP\User1" an, und starten Sie Internet Explorer.
13.	Geben Sie in die Adressleiste **http://lob1/** ein, und drücken Sie dann die EINGABETASTE. Die Standardwebseite für IIS 8 sollte angezeigt werden.

Dies ist die aktuelle Konfiguration.

![](./media/virtual-networks-set-up-LOB-App-hybrid-cloud-for-testing/CreateLOBAppHybridCloud_3.png)
 
Diese Umgebung kann jetzt zum Bereitstellen von webbasierten Anwendungen auf LOB1 und für Funktions- und Leistungstests aus dem Subnetz "Corpnet" verwendet werden.

## Zusätzliche Ressourcen

[Blaupausen für Software-Architekten](http://msdn.microsoft.com/dn630664)

[Hostinggerechte Webserverplattform (IIS)](http://technet.microsoft.com/library/hh831818)

[Einrichten einer Hybrid Cloud-Umgebung zu Testzwecken](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Einrichten der Office 365-Verzeichnissynchronisierung (DirSync) in einer Hybrid Cloud zu Testzwecken](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[Einrichten einer simulierten Hybrid Cloud-Umgebung zu Testzwecken](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)
<!--HONumber=47-->
