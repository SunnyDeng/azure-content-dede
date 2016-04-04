<properties
	pageTitle="Erstellen eines virtuellen Linux-Computers mit Azure Powershell | Microsoft Azure"
	description="In diesem Artikel erhalten Sie Informationen zum Erstellen und Vorabkonfigurieren eines virtuellen Linux-Computers mit Azure PowerShell."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="cynthn"/>

# Erstellen und Vorabkonfigurieren eines virtuellen Linux-Computers mit Azure PowerShell

> [AZURE.SELECTOR]
- [Azure-Befehlszeilenschnittstelle](virtual-machines-linux-cli-create.md)
- [PowerShell](virtual-machines-linux-classic-createpowershell.md)


<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.
 
Diese Schritte zeigen, wie Sie einen virtuellen Linux-Computer mit einem lückenfüllenden Ansatz zur Erstellung von Azure PowerShell-Befehlssätzen erstellen können. Dieser Ansatz kann hilfreich sein, wenn Sie noch nicht mit Azure PowerShell gearbeitet haben oder einfach wissen möchten, welche Werte Sie für die erfolgreiche Konfiguration angeben müssen.

Sie erstellen Ihren Befehlssatz, indem Sie die Sätze an Befehlsblöcken in eine Textdatei oder die PowerShell ISE kopieren und dann die Variablenwerte eingeben und die Zeichen < and > entfernen. Anhand der beiden [Beispiele](#examples) am Ende dieses Artikels erhalten Sie eine Idee des Endergebnisses.

Das Begleitthema für Windows-basierte virtuelle Computer finden Sie unter [Erstellen eines virtuellen Windows-Computers mit Azure PowerShell](virtual-machines-windows-classic-create-powershell.md).

## Installieren von Azure PowerShell

Sofern dies noch nicht erfolgt ist, [installieren und konfigurieren Sie Azure PowerShell](powershell-install-configure.md). Öffnen Sie dann eine Azure PowerShell-Befehlseingabeaufforderung.

## Festlegen Ihres Abonnements und Speicherkontos

Legen Sie Ihr Azure-Abonnement und Speicherkonto fest, indem Sie die folgenden Befehle in der Azure PowerShell-Eingabeaufforderung ausführen.

Sie erhalten den korrekten Abonnementnamen aus der **SubscriptionName**-Eigenschaft der Ausgabe des Befehls **Get-AzureSubscription**.

Sie erhalten den korrekten Speicherkontonamen aus der Eigenschaft **Label** der Ausgabe des Befehls **Get-AzureStorageAccount**, nachdem Sie den Befehl „Select-AzureSubscription“ ausgeführt haben.

Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount


## Suchen des zu verwendenden Images

Als Nächstes müssen Sie den ImageFamily-Wert für das Image bestimmen, das Sie verwenden möchten. Sie können die Liste der verfügbaren ImageFamily-Werte mit dem folgenden Befehl abrufen.

	Get-AzureVMImage | select ImageFamily -Unique

Hier finden Sie einige Beispiele für ImageFamily-Werte für Linux-basierte Computer:

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

Öffnen Sie eine neue Instanz des Texteditors Ihrer Wahl oder der PowerShell Integrated Scripting Environment (ISE). Kopieren Sie den folgenden Code in die neue Textdatei oder PowerShell ISE, wobei Sie den Wert von "ImageFamily" ersetzen.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Angeben des Namens, der Größe und optional der Verfügbarkeitsgruppe

Starten Sie den Befehlssatz, indem Sie einen dieser beiden Befehlssätze auswählen (erforderlich).

**Option 1**: Geben Sie einen Namen für den virtuellen Computer und eine Größe an.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

**Option 2**: Geben Sie einen Namen, eine Größe und einen Verfügbarkeitsgruppennamen an.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Die InstanceSize-Werte für virtuelle Computer der D-, DS- oder G-Serie finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).


## Einrichten der Sicherheitsoptionen für den Benutzerzugriff

**Option 1**: Geben Sie den anfänglichen Linux-Benutzernamen und das Kennwort an (erforderlich). Verwenden Sie ein sicheres Kennwort. Sie können die Sicherheit des Kennworts unter [Password Checker: Using Strong Passwords](https://www.microsoft.com/security/pc-security/password-checker.aspx) (Kennwortprüfung – Verwenden sicherer Kennwörter) überprüfen.

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

**Option 2**: Geben Sie einen Satz von SSH-Schlüsselpaaren an, die bereits im Abonnement bereitgestellt wurden.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux auf Azure](virtual-machines-linux-ssh-from-linux.md).

**Option 3**: Geben Sie eine Liste von öffentlichen SSH-Schlüsseln an, die bereits im Abonnement bereitgestellt wurden.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Zusätzliche Vorabkonfigurationsoptionen für Linux-basierte virtuelle Computer finden Sie in der Syntax für den **Linux**-Parametersatz in [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).


## Optional: Zuweisen einer statischen DIP

Weisen Sie dem virtuellen Computer optional eine bestimmte IP-Adresse (statische DIP) zu.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Mithilfe des folgenden Befehls können Sie überprüfen, ob eine bestimmte IP-Adresse verfügbar ist:

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

## Optional: Zuweisen des virtuellen Computers zu einem bestimmten Subnetz 

Weisen Sie den virtuellen Computer einem bestimmten Subnetz in einem virtuellen Azure-Netzwerk zu.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

	
## Optional: Hinzufügen eines Datenträgers
	
Fügen Sie dem Befehlssatz folgenden Code hinzu, um dem virtuellen Computer einen Datenträger hinzuzufügen.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

## Optional: Hinzufügen des virtuellen Computers zu einem vorhandenen Satz mit Lastenausgleich für den externen Datenverkehr 

Fügen Sie dem Befehlssatz folgenden Code hinzu, um den virtuellen Computer einem vorhandenen Satz mit Lastenausgleich für den externen Datenverkehr hinzuzufügen.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, http>"
	$probeport=<TCP or HTTP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

## Festlegen, wie der Erstellungsprozess für die virtuellen Computer gestartet wird 

Fügen Sie dem Befehlssatz einen Block hinzu, um den Erstellungsprozess für die virtuellen Computer durch Auswahl eines der folgenden Befehlsblöcke zu starten.

**Option 1**: Erstellen Sie den virtuellen Computer in einem vorhandenen Clouddienst.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Der kurze Name des Clouddiensts ist der Name in der Liste der Azure Cloud Services im klassischen Azure-Portal oder in der Liste der Ressourcengruppen im Azure-Portal.

**Option 2**: Erstellen Sie den virtuellen Computer in einem vorhandenen Clouddienst und virtuellen Netzwerk.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Ausführen des Befehlssatzes

Überprüfen Sie den Azure PowerShell-Befehlssatz, den Sie im Texteditor oder der PowerShell ISE erstellt haben, und stellen Sie sicher, dass Sie alle Variablen angegeben haben und diese die korrekten Werte aufweisen. Stellen Sie außerdem sicher, dass Sie alle < and > entfernt haben.

Kopieren Sie den Befehlssatz in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste auf Ihre offene Azure PowerShell-Eingabeaufforderung. Dies gibt den Befehlssatz als Serie von PowerShell-Befehlen aus und erstellt den virtuellen Azure-Computer.

Informieren Sie sich nach dem Erstellen des virtuellen Computers unter [Anmelden bei einem mit Linux betriebenen virtuellen Computer](virtual-machines-linux-classic-log-on.md).

Wenn Sie den Befehlssatz erneut verwenden möchten, können Sie:

- diesen Befehlssatz als PowerShell-Skriptdatei (*.ps1) speichern
- diesen Befehlssatz als Azure-Automatisierungsrunbook im Bereich **Automatisierung** des klassischen Azure-Portals speichern

## <a id="examples"></a>Beispiele

Hier finden Sie zwei Beispiele für die Verwendung der vorangegangenen Schritte zum Erstellen von Azure PowerShell-Befehlssätzen, die Linux-basierte virtuelle Azure-Computer erstellen.

### Beispiel 1

Ich brauche einen PowerShell-Befehlssatz, um den anfänglichen virtuellen Linux-Computer für einen MySQL-Server zu erstellen, der:

- das Ubuntu Server 12.10-Image verwendet
- AZMYSQL1 heißt
- einen zusätzlichen Datenträger mit 500 GB aufweist
- die statische IP-Adresse 192.168.244.4 umfasst
- sich im BackEnd-Subnetz des virtuellen Netzwerks "AZDatacenter" befindet
- sich im Clouddienst "Azure-TailspinToys" befindet

Hier finden Sie den entsprechenden Azure PowerShell-Befehlssatz zum Erstellen dieses virtuellen Computers, mit Leerzeilen zwischen jedem Block für Lesbarkeit.

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### Beispiel 2

Ich brauche einen PowerShell-Befehlssatz, um einen virtuellen Linux-Computer für einen Apache-Server zu erstellen, der:

- das SUSE Linux Enterprise Server 12-Image verwendet
- LOB1 heißt
- einen zusätzlichen Datenträger mit 50 GB aufweist
- ein Mitglied des LOBServers-Lastausgleichssatzes für standardmäßigen Webdatenverkehr ist
- sich im FrontEnd-Subnetz des virtuellen Netzwerks "AZDatacenter" befindet
- sich im Clouddienst "Azure-TailspinToys" befindet

Hier finden Sie den entsprechenden Azure PowerShell-Befehlssatz zum Erstellen dieses virtuellen Computers.

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Zusätzliche Ressourcen

[Dokumentation zu virtuellen Computern](https://azure.microsoft.com/documentation/services/virtual-machines/)

[Virtuelle Computer in Azure – häufig gestellte Fragen](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Übersicht über Azure Virtual Machines](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md)

[Anmelden bei einem mit Linux betriebenen virtuellen Computer](virtual-machines-linux-classic-log-on.md)

[Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-windows-classic-create-powershell.md)

<!---HONumber=AcomDC_0323_2016-->