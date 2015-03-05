<properties 
	pageTitle="Einrichten einer Hybrid Cloud-Umgebung für Tests" 
	description="Erfahren Sie, wie Sie eine Hybrid Cloud-Umgebung für IT-Experten oder Entwicklungstests erstellen." 
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

#Einrichten einer Hybrid Cloud-Umgebung für Tests

Dieses Thema führt Sie durch die Erstellung einer Hybrid Cloud-Umgebung mit Microsoft Azure für Tests. Die resultierende Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_5.png)

Damit wird eine echte hybride Produktionsumgebung von Ihrem Standort im Internet simuliert. Sie besteht aus:

-  Einem vereinfachten lokalen Netzwerk (Subnetz "Corpnet")
-  Einem standortübergreifenden virtuellen in Azure gehosteten Netzwerk (TestVNET)
-  Einer Site-to-Site-VPN-Verbindung
-  Einem sekundären Domänencontroller im virtuellen Netzwerk TestVNET.

Diese Konfiguration bietet eine Grundlage und einen allgemeinen Ausgangspunkt für Folgendes:

-  Entwickeln und Testen von Anwendungen in einer Hybrid Cloud-Umgebung
-  Erstellen von Testkonfigurationen der Computer im Subnetz "Corpnet" und im virtuellen Netzwerk TestVNET für Hybrid Cloud-basierte IT-Arbeitsauslastungen

Das Einrichten dieser Hybrid Cloud-Testumgebung umfasst folgenden fünf Phasen:

1.	Konfigurieren der Computer im Subnetz "Corpnet"
2.	Konfigurieren von RRAS1
3.	Erstellen des standortübergreifenden virtuellen Azure-Netzwerks
4.	Erstellen der Site-to-Site-VPN-Verbindung
5.	Konfigurieren von DC2 

Wenn Sie noch über kein Azure-Abonnement verfügen, können Sie sich unter [Azure testen](http://www.windowsazure.com/pricing/free-trial/) für eine kostenlose Testversion anmelden. Wenn Sie über ein MSDN-Abonnement verfügen, lesen Sie die Informationen unter [Azure-Vorteil für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Für virtuelle Computer und virtuelle Netzwerkgateways in Azure fallen laufende Kosten an, wenn sie ausgeführt werden. Diese Kosten werden im Rahmen der kostenlosen Testversion, des MSDN-Abonnements oder des kostenpflichtigen Abonnements abgerechnet. Weitere Informationen zum Reduzieren der Kosten für das Ausführen dieser Testumgebung, wenn sie nicht verwendet wird, finden Sie im Abschnitt [Minimieren der laufenden Kosten dieser Umgebung](#costs) in diesem Thema.

##Phase 1: Konfigurieren der Computer im Subnetz "Corpnet"

Befolgen Sie die Anweisungen im Abschnitt "Schritte zum Konfigurieren vom Subnetz ‚Corpnet' in [Test Lab Guide:  Basiskonfiguration für Windows Server 2012 R2](http://www.microsoft.com/download/details.aspx?id=39638), um DC1-, APP1- und CLIENT1-Computer in einem Subnetz mit dem Namen "Corpnet" zu konfigurieren. **Dieses Subnetz muss von Ihrem Organisationsnetzwerk isoliert werden, da es direkt über den RRAS1-Computer mit dem Internet verbunden wird.** 

Melden Sie sich zunächst bei DC1 mit den Anmeldeinformationen von CORP\User1 an. Führen Sie die folgenden Befehle von einer Windows PowerShell-Eingabeaufforderung aus, um die CORP-Domäne so zu konfigurieren, dass Computer und Benutzer ihren lokalen Domänencontroller zur Authentifizierung verwenden.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_1.png)
 
##Phase 2: Konfigurieren von RRAS1

RRAS1 bietet Datenverkehrs-Routingdienste und VPN-Gerätedienste zwischen den Computern im Subnetz "Corpnet" und im virtuellen Netzwerk TestVNET. Für RRAS1 müssen zwei Netzwerkkarten installiert sein.

Installieren Sie zunächst das Betriebssystem für RRAS1.

1.	Starten Sie die Installation von Windows Server 2012 R2.
2.	Befolgen Sie die Anweisungen zum Abschließen der Installation und geben Sie ein sicheres Kennwort für das lokale Administratorkonto an. Melden Sie sich mit dem lokalen Administratorkonto an.
3.	Stellen Sie eine Verbindung zwischen RRAS1 und einem Netzwerk her, das über Internetzugriff verfügt, und führen Sie Windows Update aus, um die neuesten Updates für Windows Server 2012 R2 zu installieren.
4.	Verbinden Sie eine Netzwerkkarte mit dem Subnetz "Corpnet" und die andere direkt mit dem Internet. RRAS1 darf sich hinter einer Internetfirewall befinden, jedoch nicht hinter einer Netzwerkadressenübersetzung (network address translator, NAT).

Als Nächstes konfigurieren Sie die TCP/IP-Eigenschaften von RRAS1. Sie benötigen eine öffentliche IP-Adresskonfiguration, einschließlich einer Adresse, Subnetzmaske (oder Präfixlänge), und das Standardgateway und DNS-Server von Ihrem Internetdienstanbieter (ISP).

Verwenden Sie diese Befehle in einer Windows PowerShell-Eingabeaufforderung auf Administratorebene für RRAS1. Füllen Sie vor dem Ausführen dieser Befehle die Variablenwerte aus, und entfernen Sie die Zeichen < und >. Sie können die aktuellen Namen der Netzwerkkarten aus der Anzeige des **Get-NetAdapter**-Befehls abrufen.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength -DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Out" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

Stellen Sie für den letzten Befehl sicher, dass es vier Antworten von der IP-Adresse 10.0.0.1 gibt.

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_2.png)

#Phase 3: Erstellen vom standortübergreifenden Azure Virtual Network

Melden Sie sich zunächst beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/All/dashboard) mit den Anmeldeinformationen für Ihr Azure-Abonnement an, und erstellen Sie ein virtuelles Netzwerk mit dem Namen "TestVNET".

1.	Klicken Sie in der Taskleiste im Azure-Verwaltungsportal auf **Neu > Network Services > Virtual Network > Benutzerdefiniert erstellen**.
2.	Geben Sie auf der Seite "Details" von Virtual Network **TestVNET** als **Name** ein.
3.	Wählen Sie unter **Standort** das entsprechende Rechenzentrum für Ihren Standort.
4.	Klicken Sie auf den Pfeil "Weiter".
5.	Geben Sie auf der Seite "DNS-Server und VPN-Konnektivität" unter **DNS-Server**, **Name auswählen oder eingeben** **DC1** ein, geben Sie **10.0.0.1** unter **IP-Adresse** ein, und wählen Sie dann **Site-to-Site-VPN konfigurieren**.
6.	Wählen Sie unter **lokales Netzwerk** **Ein neues lokales Netzwerk angeben **. 
7.	Klicken Sie auf den Pfeil "Weiter".
8.	Auf der Seite "Site-to-Site-Konnektivität" führen Sie folgende Schritte aus:
	- Geben Sie unter **Name** **Corpnet** ein. 
	- Geben Sie unter **IP-Adresse des VPN-Geräts** die öffentliche der Internetschnittstelle von RRAS1 zugewiesene IP-Adresse ein.
	- Geben Sie unter **Adressraum** in der Spalte **Start-IP** **10.0.0.0** ein. Wählen Sie unter **CIDR (Anzahl Adressen)** **/8** aus, und klicken Sie dann auf **Adressraum hinzufügen**.
9.	Klicken Sie auf den Pfeil "Weiter".
10.	Führen Sie auf der Seite "Virtuelle Netzwerkadressräume" folgende Schritte aus:
	- Wählen Sie in **Adressraum** unter **Start-IP** **192.168.0.0** aus.
	- Klicken Sie unter **Subnetze** auf **Subnetz-1**, und ersetzen Sie den Namen durch **TestSubnet**. 
	- Klicken Sie in der Spalte **CIDR (Anzahl Adressen)** für TestSubnet auf **/24 (256)**.
	- Klicken Sie auf **Gatewaysubnetz hinzufügen**.
11.	Klicken Sie auf das Symbol "Fertig stellen". Fahren Sie erst fort, wenn das virtuelle Netzwerk erstellt ist.

Befolgen Sie anschließend die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell/), um Azure PowerShell auf Ihrem lokalen Computer zu installieren.

Erstellen Sie dann einen neuen Cloud-Dienst für das virtuelle Netzwerk TestVNET. Sie müssen einen eindeutigen Namen auswählen. Sie können beispielsweise den Namen TestVNET-*UniqueSequence* wählen, wobei *UniqueSequence* eine Abkürzung für Ihre Organisation ist. Wenn Ihre Organisation z. B. "Tailspin Toys" heißt, können Sie den Cloud-Dienst "TestVNET-Tailspin" nennen.

Mit dem folgenden Azure PowerShell-Befehl können Sie den Namen auf seine Eindeutigkeit auf dem lokalen Computer testen.

	Test-AzureName -Service <Proposed cloud service name>

Wenn dieser Befehl "False" zurückgibt, ist der vorgeschlagene Name eindeutig. Erstellen Sie den Cloud-Dienst mit dem folgenden Befehl.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

Erstellen Sie dann ein neues Speicherkonto für das virtuelle Netzwerk TestVNET. Sie müssen einen eindeutigen Namen auswählen. Mit diesem Befehl können Sie den Speicherkontonamen auf Eindeutigkeit testen.

	Test-AzureName -Storage <Proposed storage account name>

Wenn dieser Befehl "False" zurückgibt, ist der vorgeschlagene Name eindeutig. Mit folgenden Befehlen erstellen und legen Sie das Speicherkonto fest.

	New-AzureStorageAccount -StorageAccountName <Unique storage account name> -Location "<Same location name as your virtual network>"
	Set-AzureStorageAccount -StorageAccountName <Unique storage account name>

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_3.png)

 
#Phase 4: Erstellen der Site-to-Site-VPN-Verbindung

Erstellen Sie zunächst ein virtuelles Netzwerkgateway.

1.	Klicken Sie im Azure-Verwaltungsportal auf Ihrem lokalen Computer im linken Bereich auf **Netzwerke**, und stellen Sie sicher, dass die Spalte **Status** für TestVNET auf **Erstellt** festgelegt ist.
2.	Klicken Sie auf **TestVNET**. Auf der Seite "Dashboard" sollte der Status **Gateway nicht erstellt** angezeigt werden.
3.	Klicken Sie in der Taskleiste auf **Gateway erstellen**, und klicken Sie dann auf **Dynamisches Routing**. Klicken Sie bei Aufforderung auf **Ja**. Warten Sie, bis das Gateway fertig gestellt ist und den Status **Verbindung wird hergestellt** aufweist. Dies kann einige Minuten in Anspruch nehmen.
4.	Notieren Sie die **Gateway-IP-Adresse** von der Seite "Dashboard". Dies ist die öffentliche IP-Adresse des Azure-VPN-Gateways für das virtuelle Netzwerk TestVNET. Sie benötigen diese IP-Adresse, um RRAS1 zu konfigurieren.
5.	Klicken Sie in der Taskleiste auf **Schlüssel verwalten** und anschließend auf das Symbol "Kopieren" neben dem Schlüssel, um diesen in die Zwischenablage zu kopieren. Fügen Sie diesen Schlüssel in ein Dokument ein, und speichern Sie es. Sie benötigen diesen Schlüsselwert, um RRAS1 zu konfigurieren. 

Konfigurieren Sie nun RRAS1 mit dem Routing- und RAS-Dienst als VPN-Gerät für das Subnetz "Corpnet". Melden Sie sich als lokaler Administrator bei RRAS1 an, und führen Sie folgende Befehle in einer Windows PowerShell-Eingabeaufforderung aus.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Als Nächstes konfigurieren Sie RRAS1, um die Site-to-Site-VPN-Verbindung vom Azure-VPN-Gateway zu erhalten. Starten Sie RRAS1 neu, melden Sie sich als lokaler Administrator an, und führen Sie folgende Befehle in einer Windows PowerShell-Eingabeaufforderung aus. Sie müssen die IP-Adresse des Azure-VPN-Gateways und den Schlüsselwert angeben.

	$PresharedKey="<Key value>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

Wechseln Sie zum Azure-Verwaltungsportal auf dem lokalen Computer, und warten Sie, bis das virtuelle Netzwerk TestVNET den Status **Verbunden** aufweist.

Als Nächstes konfigurieren Sie RRAS1 zur Unterstützung von übersetztem Datenverkehr für Internetspeicherorte. Führen Sie für RRAS1 folgende Schritte aus:

1.	Geben Sie im Startbildschirm **rras** ein, und klicken Sie dann auf **Routing und Remotezugriff**. 
2.	Öffnen Sie in der Konsolenstruktur den Servernamen, und klicken Sie dann auf **IPv4**.
3.	Klicken Sie mit der rechten Maustaste auf **Allgemein**, und klicken Sie dann auf **Neues Routingprotokoll**.
4.	Klicken Sie auf **NAT**, und klicken Sie auf **OK**.
5.	Klicken Sie in der Konsolenstruktur mit der rechten Maustaste auf **NAT**, klicken Sie dann auf **Neue Schnittstelle**, anschließend auf **Corpnet**, und klicken Sie dann zwei Mal auf **OK**.
6.	Klicken Sie mit der rechten Maustaste auf **NAT**, dann auf **Neue Schnittstelle**, anschließend auf **Internet**, und klicken Sie dann auf **OK**.
7.	Klicken Sie auf der Registerkarte **NAT** auf **An das Internet angeschlossene, öffentliche Schnittstelle**, wählen Sie **NAT auf dieser Schnittstelle aktivieren**, und klicken Sie dann auf **OK**.


Als Nächstes konfigurieren Sie DC1, APP1 und CLIENT1, um RRAS1 als Standardgateway zu verwenden.
 
Führen Sie für DC1 folgende Befehle in der Windows PowerShell-Eingabeaufforderung auf Administratorebene aus.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

Wenn der Name der Schnittstelle nicht Ethernet ist, verwenden Sie den **Get-NetAdapter**-Befehl, um den Schnittstellennamen zu bestimmen.

Führen Sie für APP1 folgenden Befehl in der Windows PowerShell-Eingabeaufforderung auf Administratorebene aus.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

Führen Sie für CLIENT1 den folgenden Befehl in der Windows PowerShell-Eingabeaufforderung auf Administratorebene aus.

	ipconfig /renew

Die aktuelle Konfiguration sieht folgendermaßen aus.
 

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_4.png)


#Phase 5: Konfigurieren von DC2

Führen Sie als Erstes auf dem lokalen Computer die folgenden Befehle in der Azure PowerShell-Eingabeaufforderung aus, um einen virtuellen Azure-Computer für DC2 zu erstellen.

	$ServiceName="<Your cloud service name from Phase 3>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<A password for the local administrator account>"
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdminName -Password $LocalAdminPW
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET


Melden Sie sich anschließend bei dem neuen virtuellen Computer für DC2 an.

1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf **Virtuelle Computer**, und klicken Sie dann in der Spalte **Status** für DC2 auf **Wird ausgeführt**.
2.	Klicken Sie in der Taskleiste auf **Verbinden**. 
3.	Wenn Sie zum Öffnen von DC2.rdp aufgefordert werden, klicken Sie auf **öffnen**.
4.	Wenn ein Meldungsfeld der Remotedesktopverbindung angezeigt wird, klicken Sie auf **Verbinden**.
5.	Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie Folgendes ein:
	- Name: **DC2\\**[Name des lokalen Administratorkontos]
	- Kennwort: [Kennwort des lokalen Administratorkontos]
6.	Wenn ein Meldungsfeld der Remotedesktopverbindung zu Zertifikaten angezeigt wird, klicken Sie auf **Ja**.

Konfigurieren Sie anschließend eine Windows-Firewall-Regel, um Datenverkehr für grundlegende Konnektivitätstests zuzulassen. Führen Sie auf DC2 in einer Windows PowerShell-Eingabeaufforderung auf Administratorebene den folgenden Befehl aus:

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Der ping-Befehl muss in vier erfolgreichen Antworten von der IP-Adresse 10.0.0.1 resultieren. Dies ist ein Test des Datenverkehrs über die Site-to-Site-VPN-Verbindung.

Fügen Sie anschließend einen zusätzlichen Datenträger als neues Volume mit dem Laufwerkbuchstaben F: hinzu.

1.	Klicken Sie im linken Bereich von Server-Manager auf **Datei- und Speicherdienste**, und klicken Sie dann auf **Datenträger**.
2.	Klicken Sie im Inhaltsbereich in der Gruppe **Datenträger** auf **Datenträger 2** (wobei **Partition** auf **Unbekannt** festgelegt ist).
3.	Klicken Sie auf **Aufgaben**, und klicken Sie dann auf **Neues Volume**.
4.	Klicken Sie auf der Seite "Vorbemerkungen" des Assistenten für neue Volumes auf **Weiter**.
5.	Klicken Sie auf der Seite "Server und Datenträger auswählen" auf **Datenträger 2**, und klicken Sie dann auf **Weiter**. Klicken Sie bei der entsprechenden Aufforderung auf **OK**.
6.	Klicken Sie auf der Seite "Geben Sie die Größe des Volumes an" auf **Weiter**.
7.	Klicken Sie auf die Seite "Einem Laufwerkbuchstaben oder Ordner zuweisen" auf **Weiter**.
8.	Klicken Sie auf der Seite "Dateisystemeinstellungen auswählen" auf **Weiter**.
9.	Klicken Sie auf der Seite "Auswahl bestätigen" auf **Erstellen**.
10.	Wenn Sie fertig sind, klicken Sie auf **Schließen**.

Konfigurieren Sie als Nächstes DC2 als replizierten Domänencontroller für die corp.contoso.com-Domäne. Führen Sie die folgenden Befehle in der Windows PowerShell-Eingabeaufforderung auf DC2 aus.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Beachten Sie, dass Sie zur Angabe des Kennworts für CORP\User1 und eines Kennworts für den Verzeichnisdienst-Wiederherstellungsmodus (DSRM) und zum Neustart von DC2 aufgefordert werden.

Da das virtuelle Netzwerk TestVNET nun über einen eigenen DNS-Server (DC2) verfügt, müssen Sie das virtuelle Netzwerk TestVNET zur Verwendung dieses DNS-Servers konfigurieren.

1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf **Netzwerke**, und klicken Sie dann auf **TestVNET**.
2.	Klicken Sie auf **Konfigurieren**.
3.	Entfernen Sie in **DNS-Server**, den Eintrag **10.0.0.1**.
4.	Fügen Sie in **DNS-Server** einen Eintrag mit **DC2** als Name und **192.168.0.4** als IP-Adresse hinzu. 
5.	Klicken Sie im unteren Bereich der Befehlsleiste auf **Speichern**.
6.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf **Virtuelle Computer**, und klicken Sie dann auf die Spalte **Status** neben DC2.
7.	Klicken Sie in der Befehlsleiste auf **Neu starten**. Warten Sie, bis DC2 neugestartet wird.


Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_5.png)

 
Die Hybrid Cloud-Umgebung kann nun getestet werden.

##Zusätzliche Ressourcen

[Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken](./virtual-networks-setup-sharepoint-hybrid-cloud-testing/)

[Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](../virtual-networks-setup-lobapp-hybrid-cloud-testing/)

[Einrichten der Office 365-Verzeichnissynchronisierung (DirSync) in einer Hybrid Cloud zu Testzwecken](../virtual-networks-setup-dirsync-hybrid-cloud-testing/)

##Minimieren der laufenden Kosten dieser Umgebung

Führen Sie zur Minimierung der Kosten für das Ausführen von virtuellen Computern in dieser Umgebung die erforderlichen Tests und Demonstrationen so schnell wie möglich durch, und löschen Sie oder fahren Sie die virtuellen Computer herunter, wenn Sie sie nicht verwenden. Sie können beispielsweise Azure Automation und ein Runbook zum automatischen Herunterfahren der virtuellen Computer im virtuellen Netzwerk Test_VNET am Ende des Geschäftstags verwenden. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Automation](../automation-create-runbook-from-samples/). 

Das Azure-VPN-Gateway ist als Gruppe von zwei virtuellen Azure-Computern implementiert, für das laufende Kosten anfallen. Weitere Informationen hierzu finden Sie unter [Virtuelles Netzwerk - Preise](http://azure.microsoft.com/pricing/details/virtual-network/). Erstellen Sie zur Minimierung der Kosten für das VPN-Gateway die Testumgebung und führen Sie die benötigten Tests und Demonstrationen so schnell wie möglich aus, oder löschen Sie das Gateway mithilfe der folgenden Schritte aus. 

1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf dem lokalen Computer auf **Netzwerke**, klicken Sie auf **TestVNET**, und klicken Sie dann auf **Dashboard**.
2.	Klicken Sie in der Taskleiste auf **Gateway löschen**. Klicken Sie bei entsprechender Aufforderung auf **Ja**. Warten Sie, bis das Gateway gelöscht ist und den Status **Das Gateway wurde nicht erstellt** aufweist.

Wenn Sie das Gateway löschen und die Testumgebung wiederherstellen möchten, müssen Sie als Erstes ein neues Gateway erstellen.

1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf dem lokalen Computer auf **Netzwerke**, und klicken Sie dann auf **TestVNET**. Auf der Seite "Dashboard" sollte der Status **Das Gateway wurde nicht erstellt** angezeigt werden.
2.	Klicken Sie in der Taskleiste auf **Gateway erstellen**, und klicken Sie dann auf **Dynamisches Routing**. Klicken Sie bei entsprechender Aufforderung auf **Ja**. Warten Sie, bis das Gateway fertig gestellt ist und den Status **Verbindung wird hergestellt** aufweist. Dies kann einige Minuten in Anspruch nehmen.
3.	Notieren Sie die **Gateway-IP-Adresse** von der Seite "Dashboard". Dies ist die neue öffentliche IP-Adresse des Azure-VPN-Gateways für das virtuelle Netzwerk TestVNET. Sie benötigen diese IP-Adresse, um RRAS1 neu zu konfigurieren.
4.	Klicken Sie in der Taskleiste auf **Schlüssel verwalten** und anschließend auf das Symbol "Kopieren" neben dem Schlüssel, um diesen in die Zwischenablage zu kopieren. Fügen Sie diesen Schlüsselwert in ein Dokument ein, und speichern Sie es. Sie benötigen diesen Schlüsselwert, um RRAS1 neu zu konfigurieren. 

Melden Sie sich als Nächstes bei RRAS1 als lokaler Administrator an, und führen Sie folgende Befehle in der Windows PowerShell-Eingabeaufforderung auf Administratorebene aus, um RRAS1 mit der neuen öffentlichen IP-Adresse neu zu konfigurieren.

	$PresharedKey="<Key value>"
	Set-VpnS2SInterface -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -SharedSecret $PresharedKey

Wechseln Sie zum Azure-Verwaltungsportal auf dem lokalen Computer, und warten Sie, bis das virtuelle Netzwerk TestVNET den Status "Verbunden" aufweist.

<!--HONumber=45--> 
