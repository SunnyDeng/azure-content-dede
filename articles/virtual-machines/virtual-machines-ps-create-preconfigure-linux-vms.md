<properties
	pageTitle="Erstellen eines virtuellen Linux-Computers mit Azure Powershell | Microsoft Azure"
	description="In diesem Artikel erhalten Sie Informationen zum Erstellen und Vorabkonfigurieren eines virtuellen Linux-Computers mit Azure PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="cynthn"/>

# Erstellen und Vorabkonfigurieren eines virtuellen Linux-Computers mit Azure PowerShell

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-linux-vms.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.
 

Mit den folgenden Schritten können Sie mithilfe einer Reihe von Azure PowerShell-Befehlen einen virtuellen Linux-Computer mit dem klassischen Verwaltungsmodell erstellen und vorkonfigurieren. Sie können diesen Prozess verwenden, um schnell einen Befehlssatz für einen neuen Linux-basierten virtuellen Computer zu erstellen und eine vorhandene Bereitstellung zu erweitern oder mehrere Befehlssätze zu erstellen, die schnell eine benutzerdefinierte Entwicklungs-/Test- oder IT-Expertenumgebung erstellen.

Diese Schritte folgen einem lückenfüllenden Ansatz zur Erstellung von Azure PowerShell-Befehlssätzen. Dieser Ansatz kann hilfreich sein, wenn Sie noch nicht mit Azure PowerShell gearbeitet haben oder einfach wissen möchten, welche Werte Sie für die erfolgreiche Konfiguration angeben müssen. Fortgeschrittene Azure PowerShell-Benutzer können die Befehle verwenden und dabei die Variablen (Zeilen, die mit "$" beginnen) durch eigene Werte ersetzen.

Das Begleitthema zum Konfigurieren der Windows-basierten virtuellen Computer finden Sie unter [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Schritt 1: Installieren von Azure PowerShell

Wenn Sie dies noch nicht getan haben, verwenden Sie die Anweisungen unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md), um Azure PowerShell auf Ihrem lokalen Computer zu installieren. Öffnen Sie dann eine Azure PowerShell-Befehlseingabeaufforderung.

## Schritt 2: Festlegen Ihres Abonnements und Speicherkontos

Legen Sie Ihr Azure-Abonnement und Speicherkonto fest, indem Sie die folgenden Befehle in der Azure PowerShell-Eingabeaufforderung ausführen. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Sie erhalten den korrekten Abonnementnamen aus der **SubscriptionName**-Eigenschaft der Ausgabe des Befehls **Get-AzureSubscription**. Sie erhalten den korrekten Speicherkontonamen aus der Eigenschaft **Beschriftung** der Ausgabe des Befehls **Get-AzureStorageAccount**, nachdem Sie den Befehl **Select-AzureSubscription** ausgeben. Sie können diese Befehle auch in einer Textdatei für die zukünftige Verwendung speichern.

## Schritt 3: Bestimmen der ImageFamily

Als Nächstes müssen Sie den Wert "ImageFamily" für das Image bestimmen, das dem virtuellen Computer in Azure entspricht, den Sie erstellen möchten. Sie können die Liste der verfügbaren ImageFamily-Werte mit dem folgenden Befehl abrufen.

	Get-AzureVMImage | select ImageFamily -Unique

Hier finden Sie einige Beispiele für ImageFamily-Werte für Linux-basierte Computer:

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

Öffnen Sie eine neue Instanz des Texteditors Ihrer Wahl oder der PowerShell Integrated Scripting Environment (ISE). Kopieren Sie den folgenden Code in die neue Textdatei oder PowerShell ISE, wobei Sie den Wert von "ImageFamily" ersetzen.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Schritt 4: Erstellen des Befehlssatzes

Erstellen Sie den Rest des Befehlssatzes, indem Sie einen der folgenden Sätze an Befehlsblöcken in Ihre neue Textdatei oder PowerShell ISE kopieren und dann die Variablenwerte eingeben und die Zeichen < and > entfernen. Anhand der beiden [Beispiele](#examples) am Ende dieses Artikels erhalten Sie eine Idee des Endergebnisses.

Starten Sie den Befehlssatz, indem Sie einen dieser beiden Befehlssätze auswählen (erforderlich).

Option 1: Geben Sie einen Namen für den virtuellen Computer und eine Größe an.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Option 2: Geben Sie einen Namen, eine Größe und einen Verfügbarkeitsgruppennamen an.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Die InstanceSize-Werte für virtuelle Computer der D-, DS- oder G-Serie finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Verwenden Sie die folgenden Befehle, um den anfänglichen Linux-Benutzernamen und das Kennwort anzugeben (erforderlich). Verwenden Sie ein sicheres Kennwort. Sie können die Sicherheit des Kennworts unter [Password Checker: Using Strong Passwords](https://www.microsoft.com/security/pc-security/password-checker.aspx) (Kennwortprüfung – Verwenden sicherer Kennwörter) überprüfen.

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

Geben Sie optional einen Satz von SSH-Schlüsselpaaren an, die bereits im Abonnement bereitgestellt wurden.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux auf Azure](virtual-machines-linux-use-ssh-key.md).

Geben Sie optional eine Liste von öffentlichen SSH-Schlüsseln an, die bereits im Abonnement bereitgestellt wurden.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Zusätzliche Vorabkonfigurationsoptionen für Linux-basierte virtuelle Computer finden Sie in der Syntax für den **Linux**-Parametersatz in [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

Weisen Sie dem virtuellen Computer optional eine bestimmte IP-Adresse (statische DIP) zu.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Mithilfe des folgenden Befehls können Sie überprüfen, ob eine bestimmte IP-Adresse verfügbar ist:

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Weisen Sie optional den virtuellen Computer zu einem bestimmten Subnetz in einem virtuellen Azure-Netzwerk zu.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Fügen Sie optional einen einzelnen Datenträger zum virtuellen Computer hinzu.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Optional können Sie den virtuellen Computer einem vorhandenen Satz mit Lastenausgleich für den externen Datenverkehr hinzufügen.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, http>"
	$probeport=<TCP or HTTP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Schließlich starten Sie den Erstellungsprozess für die virtuellen Computer durch Auswahl eines der folgenden Befehlsblöcke (erforderlich).

Option 1: Erstellen Sie den virtuellen Computer in einem vorhandenen Clouddienst.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Der kurze Name des Clouddiensts ist der Name in der Liste der Azure Cloud Services im Azure-Portal oder in der Liste der Ressourcengruppen im Azure-Vorschauportal.

Option 2: Erstellen Sie den virtuellen Computer in einem vorhandenen Clouddienst und virtuellen Netzwerk.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Schritt 5: Ausführen des Befehlssatzes

Überprüfen Sie den aus mehreren Blöcken von Befehlen aus Schritt 4 bestehenden Azure PowerShell-Befehlssatz in einem Texteditor oder der PowerShell ISE. Stellen Sie sicher, dass Sie alle erforderlichen Variablen angegeben haben und diese die richtigen Werte aufweisen. Stellen Sie außerdem sicher, dass Sie alle < and > entfernt haben.

Wenn Sie einen Texteditor verwenden, kopieren Sie den Befehlssatz schließlich in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste auf Ihre offene Azure PowerShell-Eingabeaufforderung. Dies gibt den Befehlssatz als Serie von PowerShell-Befehlen aus und erstellt den virtuellen Azure-Computer. Führen Sie alternativ den Befehlssatz in der PowerShell ISE aus.

Wenn Sie den virtuellen Computer in falschen Abonnements, Speicherkonten, Clouddiensten, Verfügbarkeitsgruppen, virtuellen Netzwerken oder Subnetzen erstellen, löschen Sie den virtuellen Computer, korrigieren Sie die Befehlsblocksyntax, und führen Sie dann den korrigierten Befehlssatz aus.

Informieren Sie sich nach dem Erstellen des virtuellen Computers unter [Anmelden bei einem mit Linux betriebenen virtuellen Computer](virtual-machines-linux-how-to-log-on.md).

Wenn Sie diesen virtuellen Computer erneut oder einen ähnlichen Computer erstellen, können Sie:

- diesen Befehlssatz als PowerShell-Skriptdatei (*.ps1) speichern
- diesen Befehlssatz als Azure-Automatisierungsrunbook im Bereich **Automatisierung** des Azure-Portals speichern

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

[Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Virtuelle Computer in Azure – häufig gestellte Fragen](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Übersicht über Azure Virtual Machines](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md)

[Anmelden bei einem mit Linux betriebenen virtuellen Computer](virtual-machines-linux-how-to-log-on.md)

[Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=Nov15_HO3-->