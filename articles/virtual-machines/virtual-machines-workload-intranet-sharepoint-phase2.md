<properties
	pageTitle="SharePoint Server 2013-Farm, Phase 2 | Microsoft Azure"
	description="In Phase 2 der SharePoint Server 2013-Farm erstellen und konfigurieren Sie in Azure die zwei Active Directory-Replikatdomänencontroller."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# SharePoint-Intranetfarm-Workload Phase 2: Konfigurieren von Domänencontrollern

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Bereitstellungsmodell

In dieser Phase der Intranet-Bereitstellung einer SharePoint 2013-Farm mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in den Azure-Infrastrukturdiensten konfigurieren Sie zwei Domänencontroller im virtuellen Azure-Netzwerk in der Dienstverwaltung. Clientwebanforderungen für SharePoint-Farmressourcen können dann im virtuellen Azure-Netzwerk authentifiziert werden, anstatt diesen Authentifizierungsdatenverkehr über die VPN- oder Azure ExpressRoute-Verbindung an Ihr lokales Netzwerk zu senden.

Diese Phase muss vor Beginn von [Phase 3](virtual-machines-workload-intranet-sharepoint-phase3.md) ausgeführt worden sein. Eine Übersicht über alle Phasen finden Sie unter [Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

## Erstellen der virtuellen Computer für die Domänencontroller in Azure

Zunächst müssen Sie in Tabelle M die Spalte **Name des virtuellen Computers** ausfüllen und die Größen der virtuellen Computer nach Bedarf in Spalte **Mindestgröße** ändern.

Element | Name des virtuellen Computers | Katalogimage | Mindestgröße
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (erster Domänencontroller, Beispiel: DC1) | Windows Server 2012 R2 Datacenter | A2 (Mittel)
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (zweiter Domänencontroller, Beispiel: DC2) | Windows Server 2012 R2 Datacenter | A2 (Mittel)
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (erster SQL Server-Computer, Beispiel: SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (zweiter SQL Server-Computer, Beispiel: SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (Mehrheitsknoten des Clusters, Beispiel: MN1) | Windows Server 2012 R2 Datacenter | A1 (Klein)
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (erster SharePoint-Anwendungsserver, Beispiel: APP1) | Microsoft SharePoint Server 2013-Testversion – Windows Server 2012 R2 | A4 (Extragroß)
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (zweiter SharePoint-Anwendungsserver, Beispiel: APP2) | Microsoft SharePoint Server 2013-Testversion – Windows Server 2012 R2 | A4 (Extragroß)
8\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (erster SharePoint-Webserver, Beispiel: WEB1) | Microsoft SharePoint Server 2013-Testversion – Windows Server 2012 R2 | A4 (Extragroß)
9\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (zweiter SharePoint-Webserver, Beispiel: WEB2) | Microsoft SharePoint Server 2013-Testversion – Windows Server 2012 R2 | A4 (Extragroß)

**Tabelle M – Virtuelle Computer für die SharePoint 2013-Intranetfarm in Azure**

Einzelheiten zu den Größen der virtuellen Computer finden Sie unter [Größen virtueller Computer](virtual-machines-size-specs.md).

Mit dem folgenden Azure PowerShell-Befehlsblock erstellen Sie die virtuellen Computer für die beiden Domänencontroller. Geben Sie die Werte für die Variablen ein, lassen Sie dabei aber die < and >-Zeichen weg. Die Werte für diesen Azure PowerShell-Befehlsblock entnehmen Sie den folgenden Tabellen:

- Tabelle M für Ihre virtuellen Computer
- Tabelle V für die Einstellungen Ihres virtuellen Netzwerks
- Tabelle S für Ihr Subnetz
- Tabelle A für Ihre Verfügbarkeitsgruppen
- Tabelle C für Ihre Clouddienste

Die Tabellen V, S, A und C haben Sie in [Phase 1: Konfigurieren von Azure](virtual-machines-workload-intranet-sharepoint-phase1.md) ausgefüllt.

Führen Sie nach der Bereitstellung der richtigen Werte den daraus resultierenden Befehlsblock an der Azure-PowerShell-Eingabeaufforderung aus.

	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 1 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table S – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 6 – Value column>

	$serviceName="<Table C – Item 1 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 7 – Value column>

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## Konfigurieren des ersten Domänencontrollers

Melden Sie sich bei dem ersten Domänencontroller mit den Anmeldeinformationen des lokalen Administratorkontos an.

### <a id="logon"></a>So melden Sie sich bei einem virtuellen Computer über eine Remotedesktopverbindung an

1.	Klicken Sie im Azure-Portal im linken Bereich auf **Virtuelle Computer**.
2.	Zum Herstellen einer Verbindung mit einem virtuellen Computer klicken Sie in der Spalte **Status** neben dem Namen des virtuellen Computers auf **Ausführen**.
3.	Klicken Sie in der Befehlsleiste am unteren Rand der Seite auf **Verbinden**.
4.	Im Azure-Portal wird angezeigt, dass die RDP-Datei abgerufen wird. Klicken Sie auf **OK**.
5.	Sie werden nun im Browserdialogfeld gefragt, ob "ComputerName.rdp" von "manage.windowsazure.com" geöffnet oder gespeichert werden soll. Klicken Sie auf **Öffnen**.
6.	Klicken Sie im Dialogfeld **Remotedesktopverbindung** auf **Verbinden**.
7.	Klicken Sie im Dialogfeld **Windows-Sicherheit** auf **Anderes Konto verwenden**.
8.	Geben Sie im Feld **Benutzername** den Namen des virtuellen Computers sowie den Benutzernamen des lokalen Administratorkontos ein, das mit dem virtuellen Computer erstellt wurde (ein lokales Computerkonto). Verwenden Sie Folgendes Format: *ComputerName*\*LokalesAdministratorkontoName*
9.	Geben Sie im Feld **Kennwort** das Kennwort für das lokale Administratorkonto ein.
10.	Klicken Sie auf **OK**.
11.	Klicken Sie im Dialogfeld **Remotedesktopverbindung** auf **Ja**. Der Desktop des neuen Computers wird in einem Remotedesktop-Sitzungsfenster angezeigt.

Als Nächstes müssen Sie dem ersten Domänencontroller den zusätzlichen Datenträger hinzufügen.

### <a id="datadisk"></a>Initialisieren eines leeren Datenträgers

1.	Klicken Sie im linken Bereich des Server-Managers auf **Datei- und Speicherdienste** und anschließend auf **Datenträger**.
2.	Klicken Sie im Inhaltsbereich in der Gruppe **Datenträger** auf **Datenträger 2** (wobei **Partition** die Einstellung **Unbekannt** aufweisen muss).
3.	Klicken Sie auf **Aufgaben** und anschließend auf **Neues Volume**.
4.	Klicken Sie auf der Seite **Voraussetzungen** des Assistenten für neue Volumes auf **Weiter**.
5.	Klicken Sie auf der Seite **Server und Datenträger auswählen** auf **Datenträger 2** und anschließend auf **Weiter**. Wenn Sie dazu aufgefordert werden, klicken Sie auf **OK**.
6.	Klicken Sie auf der Seite **Geben Sie die Größe des Volumes an** auf **Weiter**.
7.	Klicken Sie auf der Seite **Einem Laufwerkbuchstaben oder Ordner zuweisen** auf **Weiter**.
8.	Klicken Sie auf der Seite **Dateisystemeinstellungen auswählen** auf **Weiter**.
9.	Klicken Sie auf der Seite **Auswahl bestätigen** auf **Erstellen**.
10.	Wenn die Initialisierung abgeschlossen ist, klicken Sie auf **Schließen**.

Testen Sie danach die Konnektivität des ersten Domänencontrollers mit Standorten Ihres Unternehmensnetzwerks.

### <a id="testconn"></a>Testen der Konnektivität

1.	Öffnen Sie vom Desktop eine Windows PowerShell-Eingabeaufforderung.
2.	Testen Sie mit dem Befehl **Ping** die Konnektivität zu Namen und IP-Adressen von Ressourcen in Ihrem Unternehmensnetzwerk.

Dadurch stellen Sie sicher, dass die DNS-Namensauflösung ordnungsgemäß funktioniert (d. h., dass der virtuelle Computer korrekt für die lokalen DNS-Server konfiguriert ist) und Pakete zum und vom standortübergreifenden virtuellen Netzwerk gesendet werden können.

Führen Sie danach an der Windows PowerShell-Eingabeaufforderung des ersten Domänencontrollers die folgenden Befehle aus:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Der Computer wird neu gestartet.

## Konfigurieren des zweiten Domänencontrollers

Melden Sie sich bei dem zweiten Domänencontroller mit den Anmeldeinformationen seines lokalen Administratorkontos an. Anweisungen finden Sie in der Beschreibung der Prozedur [Anmelden bei einem virtuellen Computer über eine Remotedesktopverbindung](#logon).

Als Nächstes müssen Sie dem zweiten Domänencontroller den zusätzlichen Datenträger hinzufügen. Anweisungen finden Sie in der Beschreibung der Prozedur [Initialisieren eines leeren Datenträgers](#datadisk).

Testen Sie danach die Konnektivität des zweiten Domänencontrollers mit Standorten Ihres Unternehmensnetzwerks. Anweisungen finden Sie in der Beschreibung der Prozedur [Testen der Konnektivität](#testconn). Dadurch stellen Sie sicher, dass die DNS-Namensauflösung ordnungsgemäß funktioniert (d. h., dass der virtuelle Computer korrekt für die lokalen DNS-Server konfiguriert ist) und Pakete zum und vom standortübergreifenden virtuellen Netzwerk gesendet werden können.

Führen Sie danach an der Windows PowerShell-Eingabeaufforderung des zweiten Domänencontrollers die folgenden Befehle aus:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Der Computer wird neu gestartet.

## Konfigurieren der Konten und Berechtigungen für die SharePoint-Farm

Für die SharePoint-Farm sind die folgenden Benutzerkonten erforderlich:

- sp\_farm: Benutzerkonto für die Verwaltung von SharePoint-Farmen
- sp\_farm\_db: Benutzerkonto mit Systemadministratorrechten für SQL Server-Instanzen
- sp\_install: Benutzerkonto mit Domänenverwaltungsrechten für die Installation von Rollen und Features
- sqlservice: Benutzerkonto, unter dem SQL Server-Instanzen ausgeführt werden können

Melden Sie sich nun bei einem Computer mit einem Domänenadministratorkonto für die Domäne an, deren Mitglied die Domänencontroller sind, öffnen Sie auf Administratorebene eine Windows PowerShell-Eingabeaufforderung, und führen Sie *nacheinander* die folgenden Befehle aus:

	New-ADUser -SamAccountName sp_farm -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_farm_db -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm_db" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_install -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_install" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

Sie werden bei jedem Befehl zur Eingabe eines Kennworts aufgefordert. Notieren Sie sich diese Kontonamen und Kennwörter, und bewahren Sie sie an einem sicheren Ort auf.

Führen Sie als Nächstes die folgenden Schritte aus, um den neuen Benutzerkonten weitere Konteneigenschaften hinzuzufügen.

1.	Geben Sie auf dem Startbildschirm **Active Directory-Benutzer** ein, und klicken Sie dann auf **Active Directory-Benutzer und -Computer**.
2.	Öffnen Sie im Strukturbereich Ihre Domäne, und klicken Sie auf **Benutzer**.
3.	Klicken Sie im Inhaltsbereich mit der rechten Maustaste auf **sp\_install**, und klicken Sie dann auf **Zu einer Gruppe hinzufügen**.
4.	Geben Sie im Dialogfeld **Gruppen auswählen** **Domänenadministratoren** ein, und klicken Sie dann zweimal hintereinander auf **OK**.
5.	Klicken Sie im Dialogfeld auf **Anzeigen** und dann auf „Erweiterte Features“. Mit dieser Option zeigen Sie alle ausgeblendeten Container und Registerkarten in den Eigenschaftenfenstern von Active Directory-Objekten an.
6.	Klicken Sie mit der rechten Maustaste auf Ihren Domänennamen, und klicken Sie dann auf **Eigenschaften**.
7.	Klicken Sie im Dialogfeld **Eigenschaften** auf die Registerkarte **Sicherheit**, und klicken Sie dann auf die Schaltfläche **Erweitert**.
8.	Klicken Sie im Fenster **Erweiterte Sicherheitseinstellungen für <YourDomain>** auf **Hinzufügen**.
9.	Klicken Sie im Fenster **Berechtigungseintrag für <YourDomain> ** auf **Prinzipal auswählen**.
10.	Geben Sie im Textfeld **<YourDomain>\\sp\_install** ein, und klicken Sie auf **OK**.
11.	Aktivieren Sie für **Computerobjekte erstellen** die Option **Zulassen**, und klicken Sie dann dreimal hintereinander auf **OK**.

Aktualisieren Sie danach die DNS-Server für Ihr virtuelles Netzwerk, damit Azure den virtuellen Computern die IP-Adressen der beiden neuen Domänencontroller als deren DNS-Server zuweist. Die für dieses Verfahren benötigten Werte stammen aus Tabelle V (Einstellungen Ihres virtuellen Netzwerks).

1.	Klicken Sie im linken Bereich des Azure-Portals auf **Netzwerke**, und klicken Sie dann auf den Namen Ihres virtuellen Netzwerks (Tabelle V – Element 1 – Wertspalte).
2.	Klicken Sie auf **Konfigurieren**.
3.	Entfernen Sie unter **DNS-Server** die Einträge der DNS-Server, die sich in Ihrem lokalen Netzwerk befinden.
4.	Fügen Sie unter **DNS-Server** zwei Einträge mit den Anzeigenamen und IP-Adressen der beiden folgenden Tabellenelemente hinzu:
 - Tabelle V – Element 6 – Wertspalte
 - Tabelle V – Element 7 – Wertspalte
5.	Klicken Sie unten in der Befehlsleiste auf **Speichern**.
6.	Klicken Sie im linken Bereich des Azure-Portals auf **Virtuelle Computer**, und klicken Sie dann neben dem Namen Ihres ersten Domänencontrollers auf die Spalte **Status**.
7.	Klicken Sie in der Befehlsleiste auf **Neu starten**.
8.	Klicken Sie nach dem Start des ersten Domänencontrollers neben dem Namen des zweiten Domänencontrollers auf die Spalte **Status**.
9.	Klicken Sie in der Befehlsleiste auf **Neu starten**. Warten Sie, bis der zweite Domänencontroller gestartet ist.

Die beiden Domänencontroller mussten neu gestartet werden, damit sie nicht mit dem lokalen DNS-Server als DNS-Server konfiguriert sind. Da es sich bei beiden selbst um DNS-Server handelt, werden sie bei ihrer Heraufstufung auf Domänencontroller automatisch mit den lokalen DNS-Servern als DNS-Weiterleitung konfiguriert.

Um sicherzustellen, dass die Server im virtuellen Azure-Netzwerk die lokalen Domänencontroller verwenden, müssen Sie nun eine Active Directory-Replikationssite erstellen. Melden Sie sich unter dem Konto „sp\_install“ beim primären Domänencontroller an, und führen Sie die folgenden Befehle auf Administratorebene an einer Windows PowerShell-Eingabeaufforderung aus:

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

Im folgenden Diagramm ist die aus dem erfolgreichen Abschluss dieser Phase resultierende Konfiguration mit Platzhaltern anstelle der Computernamen dargestellt.

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## Nächster Schritt

Zum Fortsetzen der Konfiguration dieser Workload gehen Sie zu [Phase 3: Konfigurieren der SQL Server-Infrastruktur](virtual-machines-workload-intranet-sharepoint-phase3.md).

## Zusätzliche Ressourcen

[Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen](virtual-machines-sharepoint-infrastructure-services.md)

[Infografik zu SharePoint mit SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: Branchenanwendung mit hoher Verfügbarkeit](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=AcomDC_1125_2015-->