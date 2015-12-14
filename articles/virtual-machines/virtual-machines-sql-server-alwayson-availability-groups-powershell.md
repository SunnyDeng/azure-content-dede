<properties
	pageTitle="Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen in virtuellen Azure-Computern | Microsoft Azure"
	description="In diesem Lernprogramm werden Ressourcen verwendet, die mit dem klassischen Bereitstellungsmodell erstellt wurden, um mithilfe von PowerShell eine AlwaysOn-Verfügbarkeitsgruppe in Azure zu erstellen."
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
	ms.date="08/14/2015"
	ms.author="jroth" />

# Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen in virtuellen Azure-Computern (PowerShell)

> [AZURE.SELECTOR]
- [Azure classic portal](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Virtuelle Azure-Computer (VMs) können Datenbankadministratoren helfen, die Kosten für die Implementierung von hoch verfügbaren SQL Server-Systemen zu senken. In diesem Tutorial erfahren Sie, wie Sie eine Verfügbarkeitsgruppe mithilfe von SQL Server AlwaysOn End-to-End in einer Azure-Umgebung implementieren können. Am Ende des Tutorials besteht Ihre SQL Server AlwaysOn-Lösung in Azure aus folgenden Elementen:

- Einem virtuellen Netzwerk, das mehrere Subnetze enthält, einschließlich einem Front-End- und Back-End-Subnetz

- Einem Domänencontroller mit einer Active Directory-Domäne (AD)

- Zwei virtuellen SQL Server-Computern, die im Back-End-Subnetz bereitgestellt und der AD-Domäne beigetreten sind

- Einem WSFC-Cluster aus 3 Knoten mit dem Knotenmehrheit-Quorummodell

- Einer Verfügbarkeitsgruppe mit zwei Replikaten einer Verfügbarkeitsdatenbank mit synchronem Commit

Dieses Szenario wurde aufgrund seiner Einfachheit in Azure gewählt, nicht wegen seiner Kosteneffizienz oder anderer Faktoren. Beispielsweise können Sie die Anzahl der virtuellen Computer für eine Verfügbarkeitsgruppe aus zwei Replikaten verringern, um Rechenzeit in Azure zu sparen, indem Sie den Domänencontroller als Quorum-Dateifreigabezeugen in einem WSFC-Cluster mit 2 Knoten verwenden. Diese Methode verringert die Anzahl der virtuellen Computer in der oben dargestellten Konfiguration um einen Computer.

Dieses Tutorial soll Ihnen die erforderlichen Schritte für das Einrichten der oben beschriebenen Lösung vermitteln, ohne die Details der einzelnen Schritte auszuführen. Daher werden Skripts in PowerShell verwendet, um Sie schnell durch die einzelnen Schritte zu führen, statt die einzelnen Konfigurationsschritte im GUI zu erläutern. Folgende Annahmen werden vorausgesetzt:

- Sie verfügen bereits über ein Azure-Konto mit dem Abonnement für den virtuellen Computer.

- Sie haben die [Azure PowerShell-Cmdlets](..\powershell-install-configure.md) installiert.

- Sie wissen bereits, wie ein virtueller SQL Server-Computer mithilfe der GUI aus dem virtuellen Computerkatalog bereitgestellt wird. Weitere Informationen finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](virtual-machines-provision-sql-server.md).

- Sie verfügen bereits über solide Kenntnisse über AlwaysOn-Verfügbarkeitsgruppen in lokalen Lösungen. Weitere Informationen finden Sie unter [AlwaysOn-Verfügbarkeitsgruppen (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## Verbinden mit dem Azure-Abonnement und Erstellen des virtuellen Netzwerks

1. Importieren Sie in einem PowerShell-Fenster auf dem lokalen Computer das Azure-Modul, laden Sie eine Datei mit Veröffentlichungseinstellungen auf Ihren Computer herunter, und verbinden Sie Ihre PowerShell-Sitzung mit Ihrem Azure-Abonnement, indem Sie die heruntergeladenen Veröffentlichungseinstellungen importieren.

		Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
		Get-AzurePublishSettingsFile
		Import-AzurePublishSettingsFile <publishsettingsfilepath>

	Der Befehl **Get-AzurePublishgSettingsFile** erstellt automatisch ein Verwaltungszertifikat für Azure, das auf Ihren Computer heruntergeladen wird. Ein Browser wird automatisch geöffnet, und Sie werden aufgefordert, die Anmeldeinformationen Ihres Microsoft-Kontos für Ihr Azure-Abonnement einzugeben. Die heruntergeladene **PUBLISHSETTINGS**-Datei enthält alle Informationen, die Sie zum Verwalten Ihres Azure-Abonnements benötigen. Importieren Sie diese Datei, nachdem Sie sie in einem lokalen Verzeichnis gespeichert haben, mithilfe des Befehls **Import-AzurePublishSettingsFile**.

	>[AZURE.NOTE]Die PUBLISHSETTINGS-Datei enthält Ihre Anmeldeinformationen (unverschlüsselt) für die Verwaltung Ihrer Azure-Abonnements und -Dienste. Die bewährte Sicherheitsmethode für diese Datei besteht im vorübergehenden Speichern außerhalb Ihrer Quellcodeverzeichnisse (beispielsweise im Ordner "Libraries\\Documents"). Löschen Sie sie nach Abschluss des Importvorgangs. Böswillige Benutzer, die Zugriff auf die „PUBLISHSETTINGS“-Datei erlangen, können Ihre Azure-Dienste bearbeiten, erstellen und löschen.

1. Definieren Sie eine Reihe von Variablen, die Sie zum Erstellen Ihrer Cloud-IT-Infrastruktur verwenden.

		$location = "West US"
		$affinityGroupName = "ContosoAG"
		$affinityGroupDescription = "Contoso SQL HADR Affinity Group"
		$affinityGroupLabel = "IaaS BI Affinity Group"
		$networkConfigPath = "C:\scripts\Network.netcfg"
		$virtualNetworkName = "ContosoNET"
		$storageAccountName = "<uniquestorageaccountname>"
		$storageAccountLabel = "Contoso SQL HADR Storage Account"
		$storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
		$winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
		$sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
		$dcServerName = "ContosoDC"
		$dcServiceName = "<uniqueservicename>"
		$availabilitySetName = "SQLHADR"
		$vmAdminUser = "AzureAdmin"
		$vmAdminPassword = "Contoso!000"
		$workingDir = "c:\scripts"

	Achten Sie auf die folgenden Punkte, um sicherzustellen, dass die Befehle später erfolgreich ausgeführt werden:

	- Die Variablen **$storageAccountName** und **$dcServiceName** müssen eindeutig sein, da sie zur Identifikation Ihres Cloudspeicherkontos bzw. Ihres Cloudservers im Internet verwendet werden.

	- Die für die Variablen **$affinityGroupName** und **$virtualNetworkName** angegebenen Namen werden im Konfigurationsdokument des virtuellen Netzwerks konfiguriert, das Sie im späteren Verlauf verwenden.

	- **$sqlImageName** gibt den aktualisierten Namen des VM-Images an, das SQL Server 2012 Service Pack 1 Enterprise Edition enthält.

	- Der Einfachheit halber wird im gesamten Lernprogramm das Kennwort **Contoso!000** verwendet.

1. Erstellen Sie eine Affinitätsgruppe.

		New-AzureAffinityGroup `
			-Name $affinityGroupName `
			-Location $location `
			-Description $affinityGroupDescription `
			-Label $affinityGroupLabel

1. Erstellen Sie ein virtuelles Netzwerk durch Importieren einer Konfigurationsdatei.

		Set-AzureVNetConfig `
			-ConfigurationPath $networkConfigPath

	Die Konfigurationsdatei enthält das folgende XML-Dokument. Kurz gesagt, gibt es ein virtuelles Netzwerk mit dem Namen **ContosoNET** in der Affinitätsgruppe **ContosoAG** an. Dieses weist den Adressbereich **10.10.0.0/16** und zwei Subnetze auf, **10.10.1.0/24** und **10.10.2.0/24**, bei denen es sich um das Frontsubnetz und das Rücksubnetz handelt. Im Frontsubnetz können Clientanwendungen, wie etwa Microsoft SharePoint, platziert werden, während im Rücksubnetz die SQL Server-VMs untergebracht werden. Wenn Sie die zuvor genannten Variablen **$affinityGroupName** und **$virtualNetworkName** ändern, müssen Sie auch die entsprechenden Namen unten ändern.

		<NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <Dns />
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
		        <AddressSpace>
		          <AddressPrefix>10.10.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Front">
		            <AddressPrefix>10.10.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="Back">
		            <AddressPrefix>10.10.2.0/24</AddressPrefix>
		          </Subnet>
		        </Subnets>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

1. Erstellen Sie ein Speicherkonto, das der von Ihnen erstellten Affinitätsgruppe zugeordnet ist, und legen Sie es als aktuelles Speicherkonto in Ihrem Abonnement fest.

		New-AzureStorageAccount `
			-StorageAccountName $storageAccountName `
			-Label $storageAccountLabel `
			-AffinityGroup $affinityGroupName
		Set-AzureSubscription `
			-SubscriptionName (Get-AzureSubscription).SubscriptionName `
			-CurrentStorageAccount $storageAccountName

1. Erstellen Sie den DC-Server in einem neuen Clouddienst und einer neuen Verfügbarkeitsgruppe.

		New-AzureVMConfig `
			-Name $dcServerName `
			-InstanceSize Medium `
			-ImageName $winImageName `
			-MediaLocation "$storageAccountContainer$dcServerName.vhd" `
			-DiskLabel "OS" |
			Add-AzureProvisioningConfig `
				-Windows `
				-DisableAutomaticUpdates `
				-AdminUserName $vmAdminUser `
				-Password $vmAdminPassword |
				New-AzureVM `
					-ServiceName $dcServiceName `
					–AffinityGroup $affinityGroupName `
					-VNetName $virtualNetworkName

	Diese Reihe von weitergeleiteten Befehlen führt Folgendes aus:

	- **New-AzureVMConfig** erstellt eine VM-Konfiguration.

	- **Add-AzureProvisioningConfig** gibt die Konfigurationsparameter eines eigenständigen Windows-Servers an.

	- **Add-AzureDataDisk** fügt den Datenträger, den Sie zum Speichern von Active Directory-Daten verwenden, mit auf "None" festgelegter Cacheoption hinzu.

	- **New-AzureVM** erstellt einen neuen Clouddienst sowie den neuen virtuellen Azure-Computer im neuen Clouddienst.

1. Warten Sie bis zur vollständigen Bereitstellung der neuen VM, und laden Sie die Remotedesktopdatei in Ihr Arbeitsverzeichnis herunter. Da die Bereitstellung der neuen Azure-VM ziemlich lange dauert, fragt die While-Schleife die neue VM ab, bis sie zur Verwendung bereit ist.

		$VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

		While ($VMStatus.InstanceStatus -ne "ReadyRole")
		{
		    write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
		    Start-Sleep -Seconds 15
		    $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
		}

		Get-AzureRemoteDesktopFile `
		    -ServiceName $dcServiceName `
		    -Name $dcServerName `
		    -LocalPath "$workingDir$dcServerName.rdp"

Der DC-Server ist jetzt erfolgreich bereitgestellt. Als Nächstes konfigurieren Sie die Active Directory-Domäne auf diesem DC-Server. Lassen Sie das PowerShell-Fenster auf dem lokalen Computer geöffnet. Sie verwenden es später erneut, um die zwei SQL Server-VMs zu erstellen.

## Konfigurieren des Domänencontrollers

1. Stellen Sie eine Verbindung mit dem DC-Server her, indem Sie die Remotedesktopdatei starten. Verwenden Sie den Benutzernamen "AzureAdmin" und das Kennwort **Contoso!000** des Computeradministrators, die Sie beim Erstellen der neuen VM angegeben hatten.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus.

1. Führen Sie den folgenden **DCPROMO.EXE**-Befehl aus, um die Domäne **corp.contoso.com** mit den Datenverzeichnissen auf Laufwerk "M" einzurichten.

		dcpromo.exe `
			/unattend `
			/ReplicaOrNewDomain:Domain `
			/NewDomain:Forest `
			/NewDomainDNSName:corp.contoso.com `
			/ForestLevel:4 `
			/DomainNetbiosName:CORP `
			/DomainLevel:4 `
			/InstallDNS:Yes `
			/ConfirmGc:Yes `
			/CreateDNSDelegation:No `
			/DatabasePath:"C:\Windows\NTDS" `
			/LogPath:"C:\Windows\NTDS" `
			/SYSVOLPath:"C:\Windows\SYSVOL" `
			/SafeModeAdminPassword:"Contoso!000"

	Nach dem Abschluss des Befehls wird die VM automatisch neu gestartet.

1. Stellen Sie erneut eine Verbindung mit dem DC-Server her, indem Sie die Remotedesktopdatei starten. Melden Sie sich diesmal als **CORP\\Administrator** an.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus, und importieren Sie mit dem folgenden Befehl das Active Directory-PowerShell-Modul:

		Import-Module ActiveDirectory

1. Führen Sie die folgenden Befehle aus, um der Domäne drei Benutzer hinzuzufügen.

		$pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
		New-ADUser `
			-Name 'Install' `
			-AccountPassword  $pwd `
			-PasswordNeverExpires $true `
			-ChangePasswordAtLogon $false `
			-Enabled $true
		New-ADUser `
			-Name 'SQLSvc1' `
			-AccountPassword  $pwd `
			-PasswordNeverExpires $true `
			-ChangePasswordAtLogon $false `
			-Enabled $true
		New-ADUser `
			-Name 'SQLSvc2' `
			-AccountPassword  $pwd `
			-PasswordNeverExpires $true `
			-ChangePasswordAtLogon $false `
			-Enabled $true

	**CORP\\Install** wird verwendet, um alles im Zusammenhang mit den SQL Server-Dienstinstanzen, dem WSFC-Cluster und der Verfügbarkeitsgruppe zu konfigurieren. **CORP\\SQLSvc1** und **CORP\\SQLSvc2** werden als die SQL Server-Dienstkonten für die zwei SQL Server-VMs verwendet.

1. Führen Sie als Nächstes die folgenden Befehle aus, um **CORP\\Install** die Berechtigungen zum Erstellen von Computerobjekten in der Domäne zu erteilen.

		Cd ad:
		$sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
		$guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
		$ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
		$corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
		$acl = Get-Acl $corp
		$acl.AddAccessRule($ace1)
		Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

	Die oben angegebene GUID ist die GUID für den Computerobjekttyp. Das Konto **CORP\\Install** muss über die Berechtigungen **Alle Eigenschaften lesen** und **Computerobjekte erstellen** verfügen, um die Active Directory-Objekte für den WSFC-Cluster zu erstellen. Die Berechtigung **Alle Eigenschaften lesen** wird "CORP\\Install" standardmäßig erteilt, daher brauchen Sie sie nicht explizit zu erteilen. Weitere Informationen zu Berechtigungen, die zum Erstellen des WSFC-Clusters erforderlich sind, finden Sie unter [Schrittweise Anleitung für Failovercluster: Konfigurieren von Konten in Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

	Nachdem Sie nun die Konfiguration von Active Directory und den Benutzerobjekten abgeschlossen haben, erstellen Sie zwei virtuelle SQL Server-Computer und lassen sie dieser Domäne beitreten.

## Erstellen der virtuellen SQL Server-Computer

1. Verwenden Sie weiterhin das PowerShell-Fenster, das auf dem lokalen Computer geöffnet ist. Definieren Sie die folgenden zusätzlichen Variablen:

		$domainName= "corp"
		$FQDN = "corp.contoso.com"
		$subnetName = "Back"
		$sqlServiceName = "<uniqueservicename>"
		$quorumServerName = "ContosoQuorum"
		$sql1ServerName = "ContosoSQL1"
		$sql2ServerName = "ContosoSQL2"
		$availabilitySetName = "SQLHADR"
		$dataDiskSize = 100
		$dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

	Die IP-Adresse **10.10.0.4** wird normalerweise dem ersten virtuellen Computer zugewiesen, die im Subnetz **10.10.0.0/16** des virtuellen Azure-Netzwerks erstellt wird. Sie sollten überprüfen, ob es sich um die Adresse Ihres DC-Servers handelt, indem Sie **IPCONFIG** ausführen.

1. Führen Sie die folgenden weitergeleiteten Befehle aus, um die erste VM im WSFC-Cluster mit dem Namen **ContosoQuorum** zu erstellen:

		New-AzureVMConfig `
			-Name $quorumServerName `
			-InstanceSize Medium `
			-ImageName $winImageName `
			-MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
			-AvailabilitySetName $availabilitySetName `
			-DiskLabel "OS" |
			Add-AzureProvisioningConfig `
				-WindowsDomain `
				-AdminUserName $vmAdminUser `
				-Password $vmAdminPassword `
				-DisableAutomaticUpdates `
				-Domain $domainName `
				-JoinDomain $FQDN `
				-DomainUserName $vmAdminUser `
				-DomainPassword $vmAdminPassword |
				Set-AzureSubnet `
					-SubnetNames $subnetName |
					New-AzureVM `
						-ServiceName $sqlServiceName `
						–AffinityGroup $affinityGroupName `
						-VNetName $virtualNetworkName `
						-DnsSettings $dnsSettings

	Beachten Sie zum oben genannten Befehl die folgenden Punkte:

	- **New-AzureVMConfig** erstellt eine VM-Konfiguration mit dem gewünschten Namen der Verfügbarkeitsgruppe. Die nachfolgenden VMs werden mit dem gleichen Namen der Verfügbarkeitsgruppe erstellt, sodass sie Mitglieder der gleichen Verfügbarkeitsgruppe sind.

	- **Add-AzureProvisioningConfig** macht die VM zum Mitglied der von Ihnen erstellten Active Directory-Domäne.

	- **Set-AzureSubnet** platziert den virtuellen Computer im Rücksubnetz.

	- **New-AzureVM** erstellt einen neuen Clouddienst sowie den neuen virtuellen Azure-Computer im neuen Clouddienst. Der **DnsSettings**-Parameter gibt an, dass der DNS-Server für die Server im neuen Clouddienst die IP-Adresse **10.10.0.4** aufweist, also die IP-Adresse des DC-Servers. Dieser Parameter ist erforderlich, um den neuen VMs im Clouddienst den erfolgreichen Beitritt zur Active Directory-Domäne zu ermöglichen. Ohne diesen Parameter müssen Sie nach der Bereitstellung der VM die IPv4-Einstellungen in Ihrer VM manuell für die Verwendung des DC-Servers als primärem DNS-Server festlegen und die VM dann mit der Active Directory-Domäne verbinden.

1. Führen Sie die folgenden weitergeleiteten Befehle aus, um die SQL Server-VMs mit den Namen **ContosoSQL1** und **ContosoSQL2** zu erstellen.

		# Create ContosoSQL1...
		New-AzureVMConfig `
		    -Name $sql1ServerName `
		    -InstanceSize Large `
		    -ImageName $sqlImageName `
		    -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
		    -AvailabilitySetName $availabilitySetName `
		    -HostCaching "ReadOnly" `
		    -DiskLabel "OS" |
		    Add-AzureProvisioningConfig `
		        -WindowsDomain `
		        -AdminUserName $vmAdminUser `
		        -Password $vmAdminPassword `
		        -DisableAutomaticUpdates `
		        -Domain $domainName `
		        -JoinDomain $FQDN `
		        -DomainUserName $vmAdminUser `
		        -DomainPassword $vmAdminPassword |
		        Set-AzureSubnet `
		            -SubnetNames $subnetName |
		            Add-AzureEndpoint `
		                -Name "SQL" `
		                -Protocol "tcp" `
		                -PublicPort 1 `
		                -LocalPort 1433 |
		                New-AzureVM `
		                    -ServiceName $sqlServiceName

		# Create ContosoSQL2...
		New-AzureVMConfig `
		    -Name $sql2ServerName `
		    -InstanceSize Large `
		    -ImageName $sqlImageName `
		    -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
		    -AvailabilitySetName $availabilitySetName `
		    -HostCaching "ReadOnly" `
		    -DiskLabel "OS" |
		    Add-AzureProvisioningConfig `
		        -WindowsDomain `
		        -AdminUserName $vmAdminUser `
		        -Password $vmAdminPassword `
		        -DisableAutomaticUpdates `
		        -Domain $domainName `
		        -JoinDomain $FQDN `
		        -DomainUserName $vmAdminUser `
		        -DomainPassword $vmAdminPassword |
		        Set-AzureSubnet `
		            -SubnetNames $subnetName |
		            Add-AzureEndpoint `
		                -Name "SQL" `
		                -Protocol "tcp" `
		                -PublicPort 2 `
		                -LocalPort 1433 |
		                New-AzureVM `
		                    -ServiceName $sqlServiceName

	Beachten Sie zu den Befehlen oben die folgenden Punkte:

	- **New-AzureVMConfig** verwendet denselben Verfügbarkeitsgruppennamen wie der DC-Server und das SQL Server 2012 Service Pack 1 Enterprise Edition-Image im Katalog für virtuelle Computer. Außerdem wird der Betriebssystemdatenträger als schreibgeschützt (ohne Schreibcache) festgelegt. Es wird empfohlen, die Datenbankdateien auf einen separaten Datenträger zu migrieren, den Sie der VM anfügen und ohne Lese- und Schreibcache konfigurieren. Die zweitbeste Lösung besteht im Entfernen des Schreibcaches für den Betriebssystemdatenträger, da der Lesecache für den Betriebssystemdatenträger nicht deaktiviert werden kann.

	- **Add-AzureProvisioningConfig** macht die VM zum Mitglied der von Ihnen erstellten Active Directory-Domäne.

	- **Set-AzureSubnet** platziert den virtuellen Computer im Rücksubnetz.

	- **Add-AzureEndpoint** fügt Zugriffsendpunkte hinzu, damit Clientanwendungen aus dem Internet auf diese SQL Server-Dienstinstanzen zugreifen können. ContosoSQL1 und ContosoSQL2 werden verschiedene Ports zugewiesen.

	- **New-AzureVM** erstellt die neue SQL Server-VM im gleichen Clouddienst wie "ContosoQuorum". Sie müssen die VMs im gleichen Clouddienst platzieren, wenn sie sich in der gleichen Verfügbarkeitsgruppe befinden sollen.

1. Warten Sie bis zur vollständigen Bereitstellung jeder einzelnen VM, und laden ihre Remotedesktopdateien in Ihr Arbeitsverzeichnis herunter. Die For-Schleife durchläuft die drei neuen VMs und führt die Befehle innerhalb der geschweiften Klammern der obersten Ebene für jede von ihnen aus.

		Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
		{
		    write-host "Waiting for " $VM.Name "..."

		    # Loop until the VM status is "ReadyRole"
		    While ($VM.InstanceStatus -ne "ReadyRole")
		    {
		        write-host "  Current Status = " $VM.InstanceStatus
		        Start-Sleep -Seconds 15
		        $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
		    }

		    write-host "  Current Status = " $VM.InstanceStatus

		    # Download remote desktop file
		    Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
		}

	Die virtuellen SQL Server-Computer sind jetzt bereitgestellt und werden ausgeführt, sie sind aber mit SQL Server mit Standardoptionen installiert.

## Initialisieren der WSFC-Cluster-VMs

In diesem Abschnitt müssen Sie die drei Server ändern, die Sie für die WSFC-Cluster und die SQL Server-Installation verwenden. Dies gilt insbesondere in folgenden Fällen:

- (Alle Server) Sie müssen das Feature **Failoverclustering** installieren.

- (Alle Server) Sie müssen **CORP\\Install** als **Computeradministrator** hinzufügen.

- (Nur "ContosoSQL1" und "ContosoSQL2") Sie müssen **CORP\\Install** zur **sysadmin**-Rolle in der Standarddatenbank hinzufügen.

- (Nur "ContosoSQL1" und "ContosoSQL2") Sie müssen **NT AUTHORITY\\System** als Anmeldekonto mit den folgenden Berechtigungen hinzufügen:

	- Beliebige Verfügbarkeitsgruppe ändern

	- SQL verbinden

	- Serverstatus anzeigen

- (Nur "ContosoSQL1" und "ContosoSQL2") Das **TCP**-Protokoll ist bereits auf der SQL Server-VM aktiviert. Jedoch müssen Sie die Firewall für den Remotezugriff auf SQL Server öffnen.

Jetzt sind Sie aber bereit. Führen Sie die nachfolgenden Schritte aus, beginnend mit **ContosoQuorum**:

1. Stellen Sie eine Verbindung mit **ContosoQuorum** her, indem Sie die Remotedesktopdateien starten. Verwenden Sie den Benutzernamen **AzureAdmin** und das Kennwort **Contoso!000** des Computeradministrators, die Sie beim Erstellen der VMs angegeben hatten.

1. Überprüfen Sie, ob die Computer **corp.contoso.com** hinzugefügt wurden.

1. Warten Sie bis zum Abschluss der automatischen Initialisierungstasks der SQL Server-Installation, bevor Sie fortfahren.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus.

1. Installieren Sie das Windows-Failoverclusteringfeature.

		Import-Module ServerManager
		Add-WindowsFeature Failover-Clustering

1. Fügen Sie **CORP\\Install** als lokalen Administrator hinzu.

		net localgroup administrators "CORP\Install" /Add

1. Melden Sie sich bei „ContosoQuorum“ ab. Sie haben die Arbeiten für diesen Server damit abgeschlossen.

		logoff.exe

Initialisieren Sie als Nächstes **ContosoSQL1** und **ContosoSQL2**. Führen Sie die unten dargestellten Schritte aus, die für beide SQL Server-VMs identisch sind.

1. Stellen Sie eine Verbindung mit den beiden SQL Server-VMs her, indem Sie die Remotedesktopdateien ausführen. Verwenden Sie den Benutzernamen **AzureAdmin** und das Kennwort **Contoso!000** des Computeradministrators, die Sie beim Erstellen der VMs angegeben hatten.

1. Überprüfen Sie, ob die Computer **corp.contoso.com** hinzugefügt wurden.

1. Warten Sie bis zum Abschluss der automatischen Initialisierungstasks der SQL Server-Installation, bevor Sie fortfahren.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus.

1. Installieren Sie das Windows-Failoverclusteringfeature.

		Import-Module ServerManager
		Add-WindowsFeature Failover-Clustering

1. Fügen Sie **CORP\\Install** als lokalen Administrator hinzu.

		net localgroup administrators "CORP\Install" /Add

1. Importieren Sie den SQL Server PowerShell-Anbieter.

		Set-ExecutionPolicy -Execution RemoteSigned -Force
		Import-Module -Name "sqlps" -DisableNameChecking

1. Fügen Sie **CORP\\Install** als sysadmin-Rolle für die SQL Server-Standardinstanz hinzu.

		net localgroup administrators "CORP\Install" /Add
		Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. Fügen Sie **NT AUTHORITY\\System** als Anmeldekonto mit den drei oben beschriebenen Berechtigungen hinzu.

		Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
		Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
		Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
		Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. Öffnen Sie die Firewall für den Remotezugriff auf SQL Server.

		netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. Melden Sie sich bei beiden VMs ab.

		logoff.exe

Sie sind jetzt bereit, die Verfügbarkeitsgruppe zu konfigurieren. Sie verwenden für alle Arbeiten an **ContosoSQL1** den SQL Server PowerShell-Anbieter.

## Konfigurieren der Verfügbarkeitsgruppe

1. Stellen Sie wieder eine Verbindung mit **ContosoSQL1** her, indem Sie die Remotedesktopdateien starten. Statt sich mit dem Computerkonto anzumelden, melden Sie sich mithilfe von **CORP\\Install** an.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus.

1. Definieren Sie die folgenden Variablen:

		$server1 = "ContosoSQL1"
		$server2 = "ContosoSQL2"
		$serverQuorum = "ContosoQuorum"
		$acct1 = "CORP\SQLSvc1"
		$acct2 = "CORP\SQLSvc2"
		$password = "Contoso!000"
		$clusterName = "Cluster1"
		$timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
		$db = "MyDB1"
		$backupShare = "\\$server1\backup"
		$quorumShare = "\\$server1\quorum"
		$ag = "AG1"

1. Importieren Sie den SQL Server PowerShell-Anbieter.

		Set-ExecutionPolicy RemoteSigned -Force
		Import-Module "sqlps" -DisableNameChecking

1. Ändern Sie das SQL Server-Dienstkonto für „ContosoSQL1“ in „CORP\\SQLSvc1“.

		$wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
		$wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
		$svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
		$svc1.Stop()
		$svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
		$svc1.Start();
		$svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Ändern Sie das SQL Server-Dienstkonto für „ContosoSQL2“ in „CORP\\SQLSvc2“.

		$wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
		$wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
		$svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
		$svc2.Stop()
		$svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
		$svc2.Start();
		$svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Laden Sie **CreateAzureFailoverCluster.ps1** von [Create WSFC Cluster for AlwaysOn Availability Groups in Windows Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) (Erstellen eines WSFC-Clusters für AlwaysOn-Verfügbarkeitsgruppen in Azure-VMs, in englischer Sprache) in das lokale Arbeitsverzeichnis herunter. Dieses Skript unterstützt Sie beim Erstellen eines funktionsfähigen WSFC-Clusters. Wichtige Informationen zum Zusammenwirken von WSFC mit dem Azure-Netzwerk finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

1. Wechseln Sie in das Arbeitsverzeichnis, und erstellen Sie mithilfe des heruntergeladenen Skripts den WSFC-Cluster.

		Set-ExecutionPolicy Unrestricted -Force
		.\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. Aktivieren Sie AlwaysOn-Verfügbarkeitsgruppen für die SQL Server-Standardinstanzen auf **ContosoSQL1** und **ContosoSQL2**.

		Enable-SqlAlwaysOn `
		    -Path SQLSERVER:\SQL\$server1\Default `
		    -Force
		Enable-SqlAlwaysOn `
		    -Path SQLSERVER:\SQL\$server2\Default `
		    -NoServiceRestart
		$svc2.Stop()
		$svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
		$svc2.Start();
		$svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Erstellen Sie ein Sicherungsverzeichnis und erteilen Sie Berechtigungen für die SQL Server-Dienstkonten. Dieses Verzeichnis verwenden Sie zum Vorbereiten der Verfügbarkeitsdatenbank für das sekundäre Replikat.

		$backup = "C:\backup"
		New-Item $backup -ItemType directory
		net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
		icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. Erstellen Sie eine Datenbank auf **ContosoSQL1** mit dem Namen **MyDB1**, fertigen Sie eine vollständige Sicherung und eine Protokollsicherung an, und stellen Sie diese auf **ContosoSQL2** mit der Option **WITH NORECOVERY** wieder her.

		Invoke-SqlCmd -Query "CREATE database $db"
		Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
		Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
		Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
		Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. Erstellen Sie die Endpunkte der Verfügbarkeitsgruppen auf den SQL Server-VMs, und legen Sie die Berechtigungen für die Endpunkte ordnungsgemäß fest.

		$endpoint =
		    New-SqlHadrEndpoint MyMirroringEndpoint `
		    -Port 5022 `
		    -Path "SQLSERVER:\SQL\$server1\Default"
		Set-SqlHadrEndpoint `
		    -InputObject $endpoint `
		    -State "Started"
		$endpoint =
		    New-SqlHadrEndpoint MyMirroringEndpoint `
		    -Port 5022 `
		    -Path "SQLSERVER:\SQL\$server2\Default"
		Set-SqlHadrEndpoint `
		    -InputObject $endpoint `
		    -State "Started"

		Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
		Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
		Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
		Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2

1. Erstellen Sie die Verfügbarkeitsreplikate.

		$primaryReplica =
		    New-SqlAvailabilityReplica `
		    -Name $server1 `
		    -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
		    -AvailabilityMode "SynchronousCommit" `
		    -FailoverMode "Automatic" `
		    -Version 11 `
		    -AsTemplate
		$secondaryReplica =
		    New-SqlAvailabilityReplica `
		    -Name $server2 `
		    -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
		    -AvailabilityMode "SynchronousCommit" `
		    -FailoverMode "Automatic" `
		    -Version 11 `
		    -AsTemplate

1. Erstellen Sie schließlich die Verfügbarkeitsgruppe, und verbinden Sie das sekundäre Replikat mit der Verfügbarkeitsgruppe.

		New-SqlAvailabilityGroup `
		    -Name $ag `
		    -Path "SQLSERVER:\SQL\$server1\Default" `
		    -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
		    -Database $db
		Join-SqlAvailabilityGroup `
		    -Path "SQLSERVER:\SQL\$server2\Default" `
		    -Name $ag
		Add-SqlAvailabilityDatabase `
		    -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
		    -Database $db

## Nächste Schritte
Sie haben nun erfolgreich SQL Server AlwaysOn implementiert, indem Sie eine Verfügbarkeitsgruppe in Azure erstellt haben. Informationen zum Konfigurieren eines Listeners für diese Verfügbarkeitsgruppe finden Sie unter [Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

Weitere Informationen zur Verwendung von SQL Server in Azure finden Sie unter [SQL Server auf virtuellen Azure-Computern](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_1203_2015-->