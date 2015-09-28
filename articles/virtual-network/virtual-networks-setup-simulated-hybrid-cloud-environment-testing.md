<properties 
	pageTitle="Simulierte Hybrid Cloud-Testumgebung | Microsoft Azure" 
	description="Erstellen Sie eine simulierte Hybrid Cloud-Umgebung für Tests durch IT-Spezialisten oder für Entwicklungstests, indem Sie zwei virtuelle Azure-Netzwerke und eine VNet-zu-VNet-Verbindung verwenden." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/10/2015" 
	ms.author="josephd"/>

# Einrichten einer simulierten Hybrid Cloud-Umgebung zu Testzwecken

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen von Ressourcen mit dem klassischen Bereitstellungsmodell.

In diesem Thema lernen Sie Schritt für Schritt, wie Sie eine simulierte Hybrid Cloud-Umgebung mit Microsoft Azure für Tests mithilfe zweier virtueller Azure-Netzwerke erstellen. Verwenden Sie diese Konfiguration als Alternative zum [Einrichten einer Hybridcloudumgebung zu Testzwecken](virtual-networks-setup-hybrid-cloud-environment-testing.md), wenn Sie nicht über eine direkte Internetverbindung und eine erreichbare öffentliche IP-Adresse verfügen. Hier sehen Sie die daraus resultierende Konfiguration.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

Dadurch wird eine Hybrid Cloud-Produktionsumgebung simuliert. Sie besteht aus:

- Einem simulierten und vereinfachten lokalen Netzwerk (TestLab), das in einem virtuellen Azure-Netzwerk gehostet wird
- Einem simulierten standortübergreifenden virtuellen Netzwerk (TestVNET), das in Azure gehostet wird
- Einer VNet-zu-VNet-Verbindung zwischen den beiden virtuellen Netzwerken
- Einem sekundären Domänencontroller im virtuellen Netzwerk TestVNET.

Diese Konfiguration bietet eine Grundlage und einen allgemeinen Ausgangspunkt für Folgendes:

- Entwickeln und Testen von Anwendungen in einer simulierten Hybrid Cloud-Umgebung
- Erstellen von Testkonfigurationen für Computer, einige davon im virtuellen Netzwerk TestLab und einige im virtuellen Netzwerks TestVNET, um IT-Arbeitsauslastungen auf Hybrid Cloud-Basis zu simulieren.

Die Einrichtung dieser Hybrid Cloud-Testumgebung besteht aus vier Hauptphasen:

1.	Konfigurieren des virtuellen Netzwerks TestLab
2.	Erstellen des standortübergreifenden virtuellen Netzwerks TestVNET
3.	Herstellen der VNet-zu-VNet-VPN-Verbindung
4.	Konfigurieren von DC2 

Wenn Sie noch über kein Azure-Abonnement verfügen, können Sie sich unter [Azure testen](http://azure.microsoft.com/pricing/free-trial/) für eine kostenlose Testversion anmelden. Wenn Sie über ein MSDN-Abonnement verfügen, lesen Sie [Azure-Vorteil für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE]Für virtuelle Computer und virtuelle Netzwerkgateways in Azure fallen laufende Kosten an, wenn sie ausgeführt werden. Diese Kosten werden im Rahmen der kostenlosen Testversion, des MSDN-Abonnements oder des kostenpflichtigen Abonnements abgerechnet. Weitere Informationen zum Reduzieren der Kosten für das Ausführen dieser Testumgebung, wenn sie nicht verwendet wird, finden Sie im Abschnitt [Minimieren der laufenden Kosten dieser Umgebung](#costs) in diesem Thema.


## Phase 1: Konfigurieren des virtuellen Netzwerks "TestLab"

Erstellen Sie gemäß den Anweisungen zur [Testumgebung für die Basiskonfiguration](../virtual-machines/virtual-machines-base-configuration-test-environment.md) die Computer "DC1", "APP1" und "CLIENT1" in einem virtuellen Azure-Netzwerk mit dem Namen "TestLab".

Stellen Sie im Azure-Verwaltungsportal auf Ihrem lokalen Computer eine Verbindung mit DC1 her. Verwenden Sie dafür die Anmeldeinformationen für CORP\\User1. Führen Sie die folgenden Befehle von einer Windows PowerShell-Eingabeaufforderung aus, um die CORP-Domäne so zu konfigurieren, dass Computer und Benutzer ihren lokalen Domänencontroller zur Authentifizierung verwenden.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet â€“Name "10.0.0.0/8" â€“Site "TestLab"
	New-ADReplicationSubnet â€“Name "192.168.0.0/16" â€“Site "TestVNET"

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## Phase 2: Erstellen des virtuellen Netzwerks "TestVNET"

Erstellen Sie zunächst ein neues virtuelles Netzwerk mit dem Namen TestVNET.

1.	Klicken Sie in der Taskleiste des Azure-Verwaltungsportals auf **Neu > Network Services > Virtual Network > Benutzerdefiniert erstellen**.
2.	Geben Sie auf der Seite „Details zum virtuellen Netzwerk“ unter **Name** den Namen **TestVNET** ein.
3.	Wählen Sie unter **Speicherort** den entsprechenden Speicherort aus.
4.	Klicken Sie auf den „Weiter“-Pfeil.
5.	Geben Sie auf der Seite "DNS-Server und VPN-Konnektivität" unter **DNS-Server** im Feld **Name auswählen oder eingeben** den Namen **DC1** ein, und klicken Sie dann auf den Pfeil für "Weiter".
6.	Führen Sie auf der Seite „Virtuelle Netzwerkadressräume“ folgende Schritte aus:
	- Geben Sie unter **Adressraum** > **Start-IP** den Wert **192.168.0.0** ein, oder wählen Sie den Wert aus.
	- Klicken Sie unter **Subnetze** auf **Subnetz-1**, und ersetzen Sie den Namen durch **TestSubnet**. 
	- Klicken Sie in der Spalte **CIDR (Anzahl Adressen)** für TestSubnet auf **/24 (256)**.
7.	Klicken Sie auf das Symbol „Abgeschlossen“. Warten Sie, bis das virtuelle Netzwerk erstellt ist, bevor Sie fortfahren.

Befolgen Sie anschließend die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md) zum Installieren von Azure PowerShell auf dem lokalen Computer.

Erstellen Sie dann einen neuen Cloud-Dienst für das virtuelle Netzwerk TestVNET. Sie müssen einen eindeutigen Namen auswählen. Sie können beispielsweise den Namen **TestVNET-***UniqueSequence* wählen, wobei *UniqueSequence* eine Abkürzung für Ihre Organisation ist. Wenn Ihre Organisation z. B. "Tailspin Toys" heißt, können Sie den Clouddienst **TestVNET-Tailspin** nennen.

Sie können die Eindeutigkeit des Namens mit diesem Azure PowerShell-Befehl auf dem lokalen Computer testen.

	Test-AzureName -Service <Proposed cloud service name>

Wird von diesem Befehl „False“ zurückgegeben, ist der vorgeschlagene Name eindeutig. Erstellen Sie den Cloud-Dienst mit dem folgenden Befehl.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##Phase 3: Erstellen der VNet-zu-VNet-Verbindung

Erstellen Sie zunächst lokale Netzwerke, die die Adressräume der beiden virtuellen Netzwerke darstellen.

1.	Klicken Sie im Azure-Verwaltungsportal auf dem lokalen Computer auf **Neu > Netzwerkdienste > Virtual Network > Lokales Netzwerk hinzufügen**.
2.	Geben Sie auf der Seite "Details zum lokalen Netzwerk angeben" unter **Name** den Namen **TestLabLNet** und unter **IP-Adresse des VPN-Geräts** die IP-Adresse **131.107.0.1** ein, und klicken Sie dann auf den Pfeil nach rechts.
3.	Geben Sie auf der Seite "Adressraum angeben" unter **Start-IP** den Wert **10.0.0.0** ein.
4.	Klicken Sie unter **CIDR (Anzahl Adressen)** auf **/24 (256)** und anschließend auf das Kontrollkästchen.
5.	Klicken Sie auf **Neu > Netzwerkdienste > Virtual Network > Lokales Netzwerk hinzufügen**.
6.	Geben Sie auf der Seite "Details zum lokalen Netzwerk angeben" unter **Name** den Namen **TestVNETLNet** und unter **IP-Adresse des VPN-Geräts** die IP-Adresse **131.107.0.2** ein, und klicken Sie dann auf den Pfeil nach rechts.
7.	Geben Sie auf der Seite "Adressraum angeben" unter **Start-IP** den Wert **192.168.0.0** ein.
8.	Klicken Sie unter **CIDR (Anzahl Adressen)** auf **/24 (256)** und anschließend auf das Kontrollkästchen.

Beachten Sie, dass die IP-Adressen 131.107.0.1 und 131.107.0.2 für die VPM-Geräte nur temporäre Platzhalterwerte sind, die so lange gelten, bis Sie Gateways für die beiden virtuellen Netzwerke konfigurieren.

Konfigurieren Sie anschließend jedes virtuelle Netzwerk so, dass es eine Site-to-Site-VPN-Verbindung verwendet, also das lokale Netzwerk entsprechend dem anderen virtuellen Netzwerk.

1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf dem lokalen Computer auf **Netzwerke**, und überprüfen Sie, ob in der Spalte **Status** für **TestLab** der Wert **Erstellt** angezeigt wird.
2.	Klicken Sie auf **TestLab** und anschließend auf **Konfigurieren**. Klicken Sie auf der Seite "TestLab" im Abschnitt **Site-to-Site-Konnektivität** auf **Eine Verbindung mit dem lokalen Netzwerk herstellen**. 
3.	Klicken Sie unter **Lokales Netzwerk** auf **TestVNETLNet**.
4.	Klicken Sie in der Taskleiste auf **Speichern**. In einigen Fällen müssen Sie möglicherweise auf **Gatewaysubnetz hinzufügen** klicken, um ein Subnetz zu erstellen, das vom Azure-VPN-Gateway verwendet wird.
5.	Klicken Sie im linken Bereich auf **Netzwerke**, und überprüfen Sie, ob in der Spalte **Status** für "TestVNET" der Wert **Erstellt** angezeigt wird.
6.	Klicken Sie auf **TestVNET** und anschließend auf **Konfigurieren**. Klicken Sie auf der Seite "TestVNET" im Abschnitt **Site-to-Site-Konnektivität** auf **Eine Verbindung mit dem lokalen Netzwerk herstellen**. 
7.	Klicken Sie unter **Lokales Netzwerk** auf **TestLabLNet**.
8.	Klicken Sie in der Taskleiste auf **Speichern**. In einigen Fällen müssen Sie möglicherweise auf **Gatewaysubnetz hinzufügen** klicken, um ein Subnetz zu erstellen, das vom Azure-VPN-Gateway verwendet wird.

Erstellen Sie anschließend die virtuellen Netzwerkgateways für die beiden virtuellen Netzwerke.

1.	Klicken Sie im Azure-Verwaltungsportal auf der Seite **Netzwerke** auf **TestLab**. Auf der Seite „Dashboard“ sollte der Status **Das Gateway wurde nicht erstellt** angezeigt werden.
2.	Klicken Sie in der Taskleiste auf **Gateway erstellen**, und klicken Sie dann auf **Dynamisches Routing**. Klicken Sie auf **Ja**, wenn Sie dazu aufgefordert werden. Warten Sie, bis das Gateway fertig gestellt ist und den Status **Verbindung wird hergestellt** aufweist. Dies kann einige Minuten in Anspruch nehmen.
3.	Notieren Sie die **Gateway-IP-Adresse** von der Seite „Dashboard“. Dies ist die öffentliche IP-Adresse des Azure-VPN-Gateways für das virtuelle Netzwerk TestLab. Notieren Sie diese IP-Adresse, da sie zum Konfigurieren der VNet-zu-VNet-Verbindung erforderlich sein wird.
4.	Klicken Sie in der Taskleiste auf **Schlüssel verwalten** und anschließend neben dem Schlüssel auf das Symbol "Kopieren", um diesen in die Zwischenablage zu kopieren. Fügen Sie diesen Schlüssel in ein Dokument ein, und speichern Sie es. Sie benötigen diesen Schlüsselwert, um die VNet-zu-VNet-Verbindung zu konfigurieren.
5.	Klicken Sie auf der Seite "Netzwerke" auf **TestVNET**. Auf der Seite „Dashboard“ sollte der Status **Das Gateway wurde nicht erstellt** angezeigt werden.
6.	Klicken Sie in der Taskleiste auf **Gateway erstellen**, und klicken Sie dann auf **Dynamisches Routing**. Klicken Sie auf **Ja**, wenn Sie dazu aufgefordert werden. Warten Sie, bis das Gateway fertig gestellt ist und den Status **Verbindung wird hergestellt** aufweist. Dies kann einige Minuten in Anspruch nehmen.
7.	Notieren Sie die **Gateway-IP-Adresse** von der Seite „Dashboard“. Dies ist die öffentliche IP-Adresse des Azure-VPN-Gateways für das virtuelle Netzwerk TestVNET. Notieren Sie diese IP-Adresse, da sie zum Konfigurieren der VNet-zu-VNet-Verbindung erforderlich sein wird.

Konfigurieren Sie anschließend die lokalen Netzwerke TestLabLNet und TestVNETLNet mit den öffentlichen IP-Adressen, die Sie beim Erstellen der virtuellen Netzwerkgateways abgerufen haben.

1.	Klicken Sie im Azure-Verwaltungsportal auf der Seite "Netzwerke" auf **Lokale Netzwerke**. 
2.	Klicken Sie auf **TestLabLNet** und anschließend in der Taskleiste auf **Bearbeiten**.
3.	Geben Sie auf der Seite "Details zum lokalen Netzwerk angeben" unter **IP-Adresse des VPN-Geräts (optional)** die IP-Adresse des virtuellen Netzwerkgateways für das virtuelle Netzwerk "TestLab" (aus Schritt 3 im vorherigen Verfahren) ein, und klicken Sie dann auf den Pfeil nach rechts.
4.	Klicken Sie auf der Seite „Adressraum eingeben“ auf das Häkchen.
5.	Klicken Sie auf der Seite "Lokale Netzwerke" auf **TestVNETLNet** und anschließend in der Taskleiste auf **Bearbeiten**.
6.	Geben Sie auf der Seite "Details zum lokalen Netzwerk angeben" unter **IP-Adresse des VPN-Geräts (optional)** die IP-Adresse des virtuellen Netzwerkgateways für das virtuelle Netzwerk "TestVNET" (aus Schritt 7 im vorherigen Verfahren) ein, und klicken Sie dann auf den Pfeil nach rechts.
7.	Klicken Sie auf der Seite „Adressraum eingeben“ auf das Häkchen.

Setzen Sie anschließend den vorinstallierten Schlüssel für beide Gateways auf denselben Wert. Dieser ist der im Azure-Verwaltungsportal festgelegte Schlüsselwert für das virtuelle Netzwerk TestLab. Führen Sie über eine Azure PowerShell-Eingabeaufforderung auf Ihrem lokalen Computer die folgenden Befehle aus, und tragen Sie den Wert des für TestLab vorinstallierten Schlüssels ein.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet â€“SharedKey $preSharedKey

Klicken Sie anschließend im Azure-Verwaltungsportal des lokalen Computers auf der Seite "Netzwerk" auf das virtuelle Netzwerk **TestLab**, klicken Sie auf **Dashboard** und anschließend in der Taskleiste auf **Verbinden**. Warten Sie, bis angezeigt wird, dass das virtuelle Netzwerk TestLab verbunden ist.

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## Phase 4: Konfigurieren von DC2

Erstellen Sie zunächst einen virtuellen Azure-Computer für DC2. Führen Sie in der Eingabeaufforderung von Azure PowerShell auf dem lokalen Computer die folgenden Befehle aus.

	$ServiceName="<Your cloud service name from Phase 2>"
	$cred=Get-Credential â€“Message "Type the name and password of the local administrator account for DC2."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles â€“LUN 0 -HostCaching None
	New-AzureVM â€“ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Melden Sie sich anschließend bei dem neuen virtuellen Computer für DC2 an.

1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf **Virtuelle Computer**, und klicken Sie dann in der Spalte **Status** für DC2 auf **Wird ausgeführt**.
2.	Klicken Sie in der Taskleiste auf **Verbinden**. 
3.	Wenn Sie zum Öffnen von DC2.rdp aufgefordert werden, klicken Sie auf **Öffnen**.
4.	Wenn ein Meldungsfeld der Remotedesktopverbindung angezeigt wird, klicken Sie auf **Verbinden**.
5.	Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie Folgendes ein:
- Name: **DC2\**[Name des lokalen Administratorkontos]
- Kennwort: [Kennwort des lokalen Administratorkontos]
6.	Wenn ein Meldungsfeld der Remotedesktopverbindung zu Zertifikaten angezeigt wird, klicken Sie auf **Ja**.

Konfigurieren Sie anschließend eine Windows-Firewall-Regel, um Datenverkehr zum Testen der allgemeinen Konnektivität zuzulassen. Führen Sie in der Windows PowerShell-Eingabeaufforderung von DC2 die folgenden Befehle auf Administratorebene aus.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Der Ping-Befehl sollte zu vier erfolgreichen Antworten von der IP-Adresse 10.0.0.4 führen. Dies ist ein Test des Datenverkehrs über die VNet-zu-VNet-Verbindung.

Fügen Sie anschließend den zusätzlichen Datenträger als neues Volume mit dem Laufwerkbuchstaben F: hinzu.

1.	Klicken Sie im linken Bereich des Server-Managers auf **Datei- und Speicherdienste** und anschließend auf **Datenträger**.
2.	Klicken Sie im Inhaltsbereich in der Gruppe **Datenträger** auf **Datenträger 2** (wobei **Partition** die Einstellung **Unbekannt** aufweisen muss).
3.	Klicken Sie auf **Aufgaben** und anschließend auf **Neues Volume**.
4.	Klicken Sie auf der Seite „Voraussetzungen“ des Assistenten für neue Volumes auf **Weiter**.
5.	Klicken Sie auf der Seite „Server und Datenträger auswählen“ auf **Datenträger 2** und anschließend auf **Weiter**. Wenn Sie dazu aufgefordert werden, klicken Sie auf **OK**.
6.	Klicken Sie auf der Seite „Geben Sie die Größe des Volumes an“ auf **Weiter**.
7.	Klicken Sie auf der Seite „Einem Laufwerkbuchstaben oder Ordner zuweisen“ auf **Weiter**.
8.	Klicken Sie auf der Seite „Dateisystemeinstellungen auswählen“ auf **Weiter**.
9.	Klicken Sie auf der Seite „Auswahl bestätigen“ auf **Erstellen**.
10.	Wenn Sie fertig sind, klicken Sie auf **Schließen**.

Konfigurieren Sie als Nächstes DC2 als replizierten Domänencontroller für die corp.contoso.com-Domäne. Führen Sie in der Windows PowerShell-Eingabeaufforderung von DC2 die folgenden Befehle aus.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Beachten Sie, dass Sie aufgefordert werden, sowohl das Kennwort für CORP\\User1 als auch ein Kennwort für den Verzeichnisdienste-Wiederherstellungsmodus anzugeben und DC2 neu zu starten.

Da das virtuelle Netzwerk TestVNET nun über einen eigenen DNS-Server (DC2) verfügt, müssen Sie das virtuelle Netzwerk TestVNET zur Verwendung dieses DNS-Servers konfigurieren.

1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf **Netzwerke**, und klicken Sie dann auf **TestVNET**.
2.	Klicken Sie auf **Konfigurieren**.
3.	Entfernen Sie unter **DNS-Server** den Eintrag "10.0.0.4".
4.	Fügen Sie in **DNS-Server** einen Eintrag mit **DC2** als Name und **192.168.0.4** als IP-Adresse hinzu. 
5.	Klicken Sie in der Befehlsleiste am unteren Rand auf **Speichern** und anschließend, wenn Sie dazu aufgefordert werden, auf **Ja**. Warten Sie, bis das Update auf das Netzwerk TestVNet abgeschlossen ist.

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
Die simulierte Hybrid Cloud-Umgebung kann jetzt zum Testen verwendet werden.

Sie können in dieser Testumgebung auch die folgenden Konfigurationen erstellen:

- [SharePoint-Intranetfarm](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)
- [Webbasierte Branchenanwendung](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)
- [Office 365-Verzeichnissynchronisierungsserver (DirSync)](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

## Zusätzliche Ressourcen

[Einrichten einer Hybridcloudumgebung zu Testzwecken](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Konfigurieren einer VNet-zu-VNet-Verbindung](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

[Testumgebung für die Basiskonfiguration](../virtual-machines/virtual-machines-base-configuration-test-environment.md)

[Testumgebungen für Azure-Hybridclouds](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)

## <a id="costs"></a>Minimieren der laufenden Kosten dieser Umgebung

Führen Sie zur Minimierung der Kosten für das Ausführen von virtuellen Computern in dieser Umgebung die erforderlichen Tests und Demonstrationen so schnell wie möglich durch, und löschen Sie oder fahren Sie die virtuellen Computer herunter, wenn Sie sie nicht verwenden. Sie können beispielsweise Azure Automation oder ein Runbook verwenden, um die virtuellen Computer in den virtuellen Netzwerken TestLab und TestVNET am Ende jedes Arbeitstages automatisch herunterzufahren. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Automation](../automation-create-runbook-from-samples.md). Wenn Sie die virtuellen Computer im Subnetz des Unternehmensnetzwerks erneut starten möchten, starten Sie zuerst DC1.

Ein Azure-VPN-Gateway wird als Gruppe von zwei virtuellen Computern in Azure implementiert, für die laufende Kosten anfallen. Nähere Informationen hierzu finden Sie unter [Preise – Virtuelles Netzwerk](http://azure.microsoft.com/pricing/details/virtual-network/). Wenn Sie die Kosten für die zwei VPN-Gateways (eines für TestLab und eines für TestVNET) minimieren möchten, erstellen sie die Testumgebung, und führen Sie die erforderlichen Test- und Demonstrationen so schnell wie möglich durch, oder löschen Sie die Gateways mit folgenden Schritten.
 
1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf dem lokalen Computer auf **Netzwerke**, klicken Sie auf **TestLab** und anschließend auf **Dashboard**.
2.	Klicken Sie in der Taskleiste auf **Gateway löschen**. Klicken Sie auf **Ja**, wenn Sie dazu aufgefordert werden. Warten Sie, bis das Gateway gelöscht ist und sein Status in **Das Gateway wurde nicht erstellt** geändert wird.
3.	Klicken Sie im linken Bereich auf **Netzwerke**, klicken Sie auf **TestVNET** und anschließend auf **Dashboard**.
4.	Klicken Sie in der Taskleiste auf **Gateway löschen**. Klicken Sie auf **Ja**, wenn Sie dazu aufgefordert werden. Warten Sie, bis das Gateway gelöscht ist und sein Status in **Das Gateway wurde nicht erstellt** geändert wird.

Wenn Sie die Gateways löschen und diese Testumgebung anschließend wiederherstellen möchten, müssen Sie zunächst neue Gateways erstellen.

1.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf dem lokalen Computer auf **Netzwerke** und anschließend auf **TestLab**. Auf der Seite „Dashboard“ sollte der Status **Das Gateway wurde nicht erstellt** angezeigt werden.
2.	Klicken Sie in der Taskleiste auf **Gateway erstellen**, und klicken Sie dann auf **Dynamisches Routing**. Klicken Sie auf **Ja**, wenn Sie dazu aufgefordert werden. Warten Sie, bis das Gateway fertig gestellt ist und den Status **Verbindung wird hergestellt** aufweist. Dies kann einige Minuten in Anspruch nehmen.
3.	Notieren Sie die **Gateway-IP-Adresse** von der Seite „Dashboard“. Dies ist die neue öffentliche IP-Adresse des Azure-VPN-Gateways für das virtuelle Netzwerk TestLab. Diese Adresse ist erforderlich, um das lokale Netzwerk TestLabLNet neu zu konfigurieren.
4.	Klicken Sie in der Taskleiste auf **Schlüssel verwalten** und anschließend auf das Symbol „Kopieren“ neben dem Schlüssel, um diesen in die Zwischenablage zu kopieren. Fügen Sie diesen Schlüsselwert in ein Dokument ein, und speichern Sie es. Dieser Schlüsselwert ist erforderlich, um das VPN-Gateway für das virtuelle Netzwerk TestVNET neu zu konfigurieren.
5.	Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf dem lokalen Computer auf **Netzwerke**, und klicken Sie dann auf **TestVNET**. Auf der Seite „Dashboard“ sollte der Status **Das Gateway wurde nicht erstellt** angezeigt werden.
6.	Klicken Sie in der Taskleiste auf **Gateway erstellen**, und klicken Sie dann auf **Dynamisches Routing**. Klicken Sie auf **Ja**, wenn Sie dazu aufgefordert werden. Warten Sie, bis das Gateway fertig gestellt ist und den Status "Verbindung wird hergestellt" aufweist. Dies kann einige Minuten in Anspruch nehmen.
7.	Notieren Sie die **Gateway-IP-Adresse** von der Seite „Dashboard“. Dies ist die neue öffentliche IP-Adresse des Azure-VPN-Gateways für das virtuelle Netzwerk TestVNET. Diese Adresse ist erforderlich, um das lokale Netzwerk TestVNETLNet neu zu konfigurieren.

Konfigurieren Sie anschließend die lokalen Netzwerke TestLabLNet und TestVNETLNet mit den neuen öffentlichen IP-Adressen, die Sie beim Erstellen der virtuellen Netzwerkgateways abgerufen haben.

1.	Klicken Sie im Azure-Verwaltungsportal auf der Seite "Netzwerke" auf **Lokale Netzwerke**. 
2.	Klicken Sie auf **TestLabLNet** und anschließend in der Taskleiste auf **Bearbeiten**.
3.	Geben Sie auf der Seite "Details zum lokalen Netzwerk angeben" unter **IP-Adresse des VPN-Geräts (optional)** die IP-Adresse des virtuellen Netzwerkgateways für das virtuelle Netzwerk "TestLab" (aus Schritt 3 im vorherigen Verfahren) ein, und klicken Sie dann auf den Pfeil nach rechts.
4.	Klicken Sie auf der Seite „Adressraum eingeben“ auf das Häkchen.
5.	Klicken Sie auf der Seite "Lokale Netzwerke" auf **TestVNETLNet** und anschließend in der Taskleiste auf **Bearbeiten**.
6.	Geben Sie auf der Seite "Details zum lokalen Netzwerk angeben" unter **IP-Adresse des VPN-Geräts (optional)** die IP-Adresse des virtuellen Netzwerkgateways für das virtuelle Netzwerk "TestVNET" (aus Schritt 7 im vorherigen Verfahren) ein, und klicken Sie dann auf den Pfeil nach rechts.
7.	Klicken Sie auf der Seite „Adressraum eingeben“ auf das Häkchen.

Setzen Sie anschließend den vorinstallierten Schlüssel für beide Gateways auf denselben Wert. Dieser ist der im Azure-Verwaltungsportal festgelegte Schlüsselwert für das virtuelle Netzwerk TestLab. Führen Sie über eine Azure PowerShell-Eingabeaufforderung auf Ihrem lokalen Computer die folgenden Befehle aus, und tragen Sie den Wert des für TestLab vorinstallierten Schlüssels ein.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet â€“SharedKey $preSharedKey

Klicken Sie auf der Seite "Netzwerk" im Azure-Verwaltungsportal auf das virtuelle Netzwerk **TestLab** und anschließend in der Taskleiste auf **Verbinden**. Warten Sie, bis angezeigt wird, dass das virtuelle Netzwerk TestLab mit dem lokalen Netzwerk TestVNET verbunden ist.
 

<!---HONumber=Sept15_HO3-->