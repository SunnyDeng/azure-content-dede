<properties 
	pageTitle="Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer" 
	description="Erfahren Sie, wie Sie Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer verwenden." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="josephd"/>

#Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer

Diese Schritte zeigen, wie Sie eine Reihe von Azure PowerShell-Befehlen anpassen, mit denen ein Windows-basierter virtueller Azure-Computer mit einem Bausteinansatz erstellt und vorab konfiguriert wird. Sie können diesen Prozess verwenden, um schnell einen Befehlssatz für einen neuen Windows-basierten virtuellen Computer zu erstellen und eine vorhandene Bereitstellung zu erweitern oder mehrere Befehlssätze zu erstellen, die schnell eine benutzerdefinierte Entwicklungs-/Test- oder IT-Expertenumgebung erstellen.

Diese Schritte folgen einem lückenfüllenden Ansatz zur Erstellung von Azure PowerShell-Befehlssätzen. Dieser Ansatz kann hilfreich sein, wenn Sie noch nicht mit PowerShell gearbeitet haben oder einfach wissen möchten, welche Werte Sie für die erfolgreiche Konfiguration angeben müssen. Erweiterte PowerShell-Benutzer können die Befehle verwenden und sie durch eigene Werte für die Variablen ersetzen (Zeilen, die mit "$" beginnen).

Das Begleitthema zum Konfigurieren der Linux-basierten virtuellen Computer finden Sie unter [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Linux-basierter virtueller Computer](http://azure.microsoft.com/documentation/articles/virtual-machines-ps-create-preconfigure-linux-vms/).

##Schritt 1: Installieren von Azure PowerShell

Wenn Sie dies noch nicht getan haben, verwenden Sie die Anweisungen unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell/), um Azure PowerShell auf Ihrem lokalen Computer zu installieren. Öffnen Sie dann eine Azure PowerShell-Befehlseingabeaufforderung auf Administratorebene.

##Schritt 2: Festlegen Ihres Abonnements und Speicherkontos

Legen Sie Ihr Azure-Abonnement und Speicherkonto fest, indem Sie diese Befehle in der Azure PowerShell-Eingabeaufforderung ausführen. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < und >, durch die korrekten Namen.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr -Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Sie erhalten den korrekten Abonnementnamen aus der Eigenschaft "SubscriptionName" der Ausgabe des Befehls **Get-AzureSubscription**. Sie erhalten den korrekten Speicherkontonamen aus der Eigenschaft "Beschriftung" der Ausgabe des Befehls **Get-AzureStorageAccount**, nachdem Sie den Befehl **Select-AzureSubscription** ausgeben. Sie können diese Befehle auch in einer Textdatei für die zukünftige Verwendung speichern.

##Schritt 3: Bestimmen der ImageFamily

Als Nächstes müssen Sie den Wert "ImageFamily" oder "Beschriftung" für das Image bestimmen, das dem virtuellen Computer in Azure entspricht, den Sie erstellen möchten. Hier finden Sie einige Beispiele aus der Galerie im Azure-Verwaltungsportal.

![](./media/virtual-machines-use-PS-create-preconfigure-windows-vms/PSPreconfigWindowsVMs_1.png)
 
Sie können die Liste der verfügbaren ImageFamily-Werte mit diesem Befehl abrufen.

	Get-AzureVMImage | select ImageFamily -Unique

Hier finden Sie einige Beispiele für ImageFamily-Werte für Windows-basierte Computer:

- Windows Server 2012 R2 Datacenter 
- Windows Server 2008 R2 SP1 
- Windows Server Technical Preview 
- SQL Server 2012 SP1 Enterprise unter Windows Server 2012 

Wenn Sie das Image finden, das Sie suchen, öffnen Sie eine neue Instanz des Texteditors Ihrer Wahl, und kopieren Sie Folgendes in die neue Textdatei. Ersetzen Sie dabei den ImageFamily-Wert. 

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

In einigen Fällen befindet sich der Name des Images in der Eigenschaft "Beschriftung" anstelle des ImageFamily-Wertes. Wenn Sie das Image, das Sie mithilfe der Eigenschaft "ImageFamily" suchen, gefunden haben, listen Sie die Images nach der Eigenschaft "Beschriftung" mit dem folgenden Befehl auf.

	Get-AzureVMImage | select Label -Unique

Wenn Sie das richtige Image mit diesem Befehl finden, öffnen Sie eine neue Instanz des Texteditors Ihrer Wahl, und kopieren Sie Folgendes in die neue Textdatei. Ersetzen Sie dabei den Wert "Beschriftung". 

	$label="<Label value>"
	$image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

##Schritt 4: Erstellen des Befehlssatzes

Erstellen Sie den Rest des Befehlssatzes, indem Sie den entsprechenden Satz an Blöcken unten in Ihre neue Textdatei kopieren und dann die Variablenwerte eingeben und die Zeichen < und > entfernen. In den beiden [Beispielen](#examples) am Ende dieses Artikels erhalten Sie eine Idee des Endergebnisses.

Starten Sie den Befehlssatz, indem Sie einen dieser beiden Befehlssätze auswählen (erforderlich).


Option 1: Geben Sie einen Namen für den virtuellen Computer und eine Größe an.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Option 2: Geben Sie einen Namen, eine Größe und einen Verfügbarkeitsgruppennamen an.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Die InstanceSize-Werte für virtuelle Computer der D-, DS- oder G-Serie finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Geben Sie optional für einen eigenständigen Windows-Computer das lokale Administratorkonto und Kennwort an.

	$localadminusername="<local administrator account name>"
	$localadminpassword="<local administrator account password>"
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $localadminusername -Password $localadminpassword

Geben Sie optional das lokale Administratorkonto und Kennwort, die Domäne und die Anmeldeinformationen eines Domänenkontos zum Hinzufügen des Windows-Computers zu einer vorhandenen Active Directory-Domäne an.

	$localadminusername="<local administrator account name>"
	$localadminpassword="<local administrator account password>"
	$domaindns="<FQDN of the domain that the machine is joining>"
	$domacctdomain="<domain of the account that has permission to add the machine to the domain>"
	$domacctname="<domain account name that has permission to add the machine to the domain>"
	$domacctpassword="<password of the domain account that has permission to add the machine to the domain>"
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $localadminusername -Password $localadminpassword -WindowsDomain -Domain $domacctdomain -DomainUserName $domacctname -DomainPassword $domacctpassword -JoinDomain $domaindns

Beachten Sie, dass dies die Angabe des Kontonamens und des Kennworts für ein Active Directory-Domänenkonto erfordert. Wenn Sie den resultierenden Befehlssatz als Datei speichern, stellen Sie sicher, dass Sie ihn an einem sicheren Ort zum Schutz des Domänenkontonamens und -kennworts speichern.

Zusätzliche Vorabkonfigurationsoptionen für Windows-basierte virtuelle Computer finden Sie in der Syntax für die **Windows**- und **WindowsDomain**-Parametersätze in [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

Weisen Sie dem virtuellen Computer optional eine bestimmte IP-Adresse (statische DIP) zu.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Sie können überprüfen, ob eine bestimmte IP-Adresse verfügbar ist mit:

	Test-AzureStaticVNetIP -VNetName <VNet name> -IPAddress <IP address>

Weisen Sie optional den virtuellen Computer zu einem bestimmten Subnetz in einem virtuellen Azure-Netzwerk zu.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Fügen Sie optional einen einzelnen Datenträger zum virtuellen Computer hinzu. 

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Legen Sie für einen Active Directory-Domänencontroller "$hcaching" auf "Kein" fest.

Optional können Sie den virtuellen Computer einem vorhandenen Satz mit Lastenausgleich für den externen Datenverkehr hinzufügen.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Schließlich starten Sie den Erstellungsprozess für die virtuellen Computer durch Auswahl eines dieser Befehlsblöcke (erforderlich).

Option 1: Erstellen Sie den virtuellen Computer in einem neuen Clouddienst. 

	New-AzureVM -Location "<An Azure location, such as US West>" -VMs $vm1

Option 2: Erstellen Sie den virtuellen Computer in einem vorhandenen Clouddienst. 

	New-AzureVM -ServiceName "<short name of the cloud service>" -VMs $vm1

Der kurze Name des Clouddiensts ist der Name in der Liste der Clouddienste im Azure-Verwaltungsportal oder in der Liste der Ressourcengruppen im Azure-Vorschauportal. 

Option 3: Erstellen Sie den virtuellen Computer in einem vorhandenen Clouddienst und virtuellen Netzwerk.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

##Schritt 5: Ausführen des Befehlssatzes

Überprüfen Sie den Azure PowerShell-Befehl in einem Texteditor, bestehend aus mehreren Blöcken von Befehlen aus Schritt 4. Stellen Sie sicher, dass Sie alle erforderlichen Variablen angegeben haben und diese die richtigen Werte aufweisen. Stellen Sie außerdem sicher, dass Sie alle < und > entfernt haben.

Kopieren Sie den Befehlssatz in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste auf Ihre offene Azure PowerShell-Eingabeaufforderung. Dies gibt den Befehlssatz als Serie von PowerShell-Befehlen aus und erstellt den virtuellen Azure-Computer.

Wenn Sie diesen virtuellen Computer erneut oder einen ähnlichen Computer erstellen, können Sie: 

- diesen Befehlssatz als Textdatei oder PowerShell-Skriptdatei (*.ps1) speichern
- diesen Befehlssatz als Azure-Automatisierungsrunbook im Bereich **Automatisierung** des Azure-Verwaltungsportals speichern 

##<a id="examples"></a>Beispiele

Hier finden Sie zwei Beispiele für die Verwendung der vorangegangenen Schritte zum Erstellen von Azure PowerShell-Befehlssätzen, die Windows-basierte virtuelle Azure-Computer erstellen.

###Beispiel 1

Ich brauche einen PowerShell-Befehl, um den ersten virtuellen Computer für einen Active Directory-Domänencontroller zu erstellen, der:

- das Windows Server 2012 R2 Datacenter-Image verwendet
- AZDC1 heißt 
- ein eigenständiger Computer ist
- einen zusätzlichen Datenträger mit 20 GB aufweist
- die statische IP-Adresse 192.168.244.4 umfasst
- sich im BackEnd-Subnetz des virtuellen Netzwerks "AZDatacenter" befindet
- sich im Clouddienst "Azure-TailspinToys" befindet

Hier finden Sie den entsprechenden Azure PowerShell-Befehlssatz zum Erstellen dieses virtuellen Computers, mit Leerzeilen zwischen jedem Block für Lesbarkeit.

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="AZDC1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$localadminusername="DCLocalAdmin"
	$localadminpassword="DCeq7294*"
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $localadminusername -Password $localadminpassword

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=20
	$disklabel="DCData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

###Beispiel 2

Ich brauche einen PowerShell-Befehlssatz, um einen virtuellen Computer für einen Line-of-Business-Server zu erstellen, der:

- das Windows Server 2012 R2 Datacenter-Image verwendet
- LOB1 heißt
- Mitglied der Domäne "corp.contoso.com" ist
- einen zusätzlichen Datenträger mit 200 GB aufweist 
- sich im FrontEnd-Subnetz des virtuellen Netzwerks "AZDatacenter" befindet
- sich im Clouddienst "Azure-TailspinToys" befindet

Hier finden Sie den entsprechenden Azure PowerShell-Befehlssatz zum Erstellen dieses virtuellen Computers.

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="LOB1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$localadminusername="LOBLocalAdmin"
	$localadminpassword="LOBmx7137*"
	$domacctdomain="CORP"
	$domacctname="admin7"
	$domacctpassword="frank0987&"
	$domaindns="corp.contoso.com"
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $localadminusername -Password $ localadminpassword -WindowsDomain -Domain $domacctdomain -DomainUserName $domacctname -DomainPassword $domacctpassword -JoinDomain $domaindns

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=200
	$disklabel="LOBData"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname


##Zusätzliche Ressourcen

[Dokumentation zu virtuellen Maschinen](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Virtuelle Computer in Azure - häufig gestellte Fragen](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Übersicht über Azure Virtual Machines](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell/)

[Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren von Linux-basierten virtuellen Computern](../virtual-machines-ps-create-preconfigure-linux-vms/)

<!--HONumber=45--> 
