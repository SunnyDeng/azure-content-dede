<properties
	pageTitle="Hochladen einer Windows-VHD für den Ressourcen-Manager | Microsoft Azure"
	description="Lernen Sie, wie Sie das Image einer VM auf Windows-Basis zur Verwendung mit dem Ressourcen-Manager-Bereitstellungsmodell hochladen."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="dkshir"/>

# Hochladen eines Windows-VM-Images in Microsoft Azure für Ressourcen-Manager-Bereitstellungen

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-createupload-vhd.md).


Dieser Artikel erläutert, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD) mit einem Betriebssystem auf Windows-Basis hochladen, um sie zum Erstellen neuer Windows-VMs mithilfe des Ressourcen-Manager-Bereitstellungsmodells zu nutzen. Weitere Details zu Datenträgern und VHDs in Microsoft Azure finden Sie unter [Informationen zu Datenträgern und VHDs für virtuelle Computer](virtual-machines-linux-about-disks-vhds.md).



## Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über Folgendes verfügen:

1. **Ein Azure-Abonnement** – wenn Sie keines besitzen, [eröffnen Sie ein kostenloses Azure-Konto](/pricing/free-trial/?WT.mc_id=A261C142F). Sie erhalten ein Guthaben, das Sie zum Ausprobieren der zahlungspflichtigen Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlosen Azure-Dienste – wie z. B. Websites – nutzen, wenn das Guthaben aufgebraucht ist. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern. Sie können auch Ihre [Vorteile für MSDN-Abonnenten aktivieren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Das MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.

2. **Microsoft Azure PowerShell 1.0.x** – stellen Sie sicher, dass Sie Microsoft Azure PowerShell Version 1.0.x installiert haben. Wenn Sie dies noch nicht installiert haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Sie sollten die Versionen 1.0 und höher verwenden, weil neue Ressourcen-Manager-Features nicht älteren PowerShell-Versionen hinzugefügt werden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie unter [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

3. **Einen virtuellen Computer, auf dem das Betriebssystem Windows ausgeführt wird** – es stehen viele Tools zum lokalen Erstellen virtueller Computer zur Verfügung. Sie können z. B. Hyper-V Manager verwenden, um einen virtuellen Computer zu erstellen und das Betriebssystem zu installieren. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx). Ausführliche Informationen zu unterstützen Windows-Betriebssystemen finden Sie unter [Microsoft Server Software-Support für virtuelle Microsoft Azure-Computer](https://support.microsoft.com/kb/2721672).


## Stellen Sie sicher, dass der virtuelle Computer das richtige Dateiformat hat.

Microsoft Azure akzeptiert nur Images für [virtuelle Computer der Generation 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx), die im VHD-Dateiformat gespeichert sind. Die Größe der VHD muss fest sein und einer ganzen Zahl in MB entsprechen. Die maximal zulässige Größe für die VHD beträgt 1.023 GB.

- Der Hyper-V Manager speichert in der Regel Ihr VM-Image in einem VHDX-Format, das in Microsoft Azure nicht unterstützt wird. Sie können es entweder mit Hyper-V oder dem [Convert-VHD-PowerShell-Cmdlet](http://technet.microsoft.com/library/hh848454.aspx) in das VHD-Format konvertieren. Informationen zu Verwendungsschritten mit PowerShell finden Sie unter [Converting Hyper-V .vhdx to .vhd file formats](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) (Konvertieren von Hyper-V-VHDX in VHD-Dateiformate). Oder wählen Sie in Hyper-V Ihren lokalen Computer auf der linken Seite, und klicken Sie dann im Menü darüber auf **Aktionen** > **Datenträger bearbeiten...**. Navigieren Sie durch die Bildschirme, indem Sie auf **Weiter** klicken und diese Optionen eingeben: Pfad für die VHDX-Datei > **Konvertieren** > **VHD** > **Feste Größe** > Pfad für die neue VHD-Datei. Klicken Sie zum Abschluss auf **Fertig stellen**.

- Wenn Sie ein Windows-VM-Image im [VMDK-Dateiformat](https://en.wikipedia.org/wiki/VMDK) besitzen, können Sie es mit dem [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) in ein VHD-Format konvertieren. Lesen Sie den Blog [How to Convert a VMWare VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Konvertieren von VMWare-VMDK in Hyper-V-VHD), um weitere Informationen zu erhalten.


## Vorbereiten des VHD-Images für den Upload

In diesem Abschnitt wird erläutert, wie Sie den virtuellen Windows-Computer generalisieren. Dadurch werden unter anderem alle persönlichen Kontoinformationen entfernt. Sie sollten diesen Schritt üblicherweise ausführen, wenn Sie ein VM-Image zum schnellen Bereitstellen ähnlicher virtueller Computer verwenden möchten. Weitere Informationen zu Sysprep finden Sie unter [How to Use Sysprep: An Introduction](http://technet.microsoft.com/library/bb457073.aspx) (in englischer Sprache).

1. Melden Sie sich bei einem virtuellen Windows-Computer an.

2. Öffnen Sie ein Eingabeaufforderungsfenster als Administrator. Wechseln Sie in das Verzeichnis **%windir%\\system32\\sysprep**, und führen Sie dann `sysprep.exe` aus.

3. Führen Sie im Dialogfeld **Systemvorbereitungstool** folgende Schritte aus:

	1. Wählen Sie unter **Systembereinigungsaktion** die Option **Out-of-Box-Experience (OOBE) für System aktivieren** und stellen Sie sicher, dass **Verallgemeinern** aktiviert ist.

	2. Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

	3. Klicken Sie auf **OK**.

	![Starten von Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br>
## Erstellen oder Suchen eines Azure-Speicherkontos

Sie benötigen ein Speicherkonto in Azure, um das VM-Image hochzuladen. Sie können ein vorhandenes Speicherkonto auswählen oder ein neues erstellen. Sie können das Portal oder die PowerShell zu diesem Zweck nutzen.

### Verwenden des Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf **Durchsuchen** > **Speicherkonten**.

3. Überprüfen Sie, ob ein Speicherkonto vorhanden ist, dass Sie für das Hochladen dieses Bilds verwenden möchten. Notieren Sie den Namen dieses Speicherkontos. Wenn Sie ein vorhandenes Speicherkonto verwenden, können Sie mit dem Abschnitt [Hochladen des VM-Images auf Ihr Speicherkonto](#uploadvm) fortfahren.

4. Wenn Sie ein neues Speicherkonto erstellen möchten, klicken Sie auf **Hinzufügen**, und geben Sie die folgenden Informationen ein:

	1. Geben Sie den **Namen** für das Speicherkonto ein. Er sollte nur zwischen 3 und 24 Kleinbuchstaben und Ziffern enthalten. Dieser Name wird Teil der URL, mit der Sie vom Speicherkonto aus auf Blob, Dateien und andere Ressourcen zugreifen.

	2. Wählen Sie den **Typ** des Speicherkontos, das Sie erstellen möchten. Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md).

	3. Geben Sie den Namen der **Ressourcengruppe** ein. Das Portal erstellt eine neue Ressourcengruppe, wenn es keine vorhandene dieses Namens findet.

	4. Wählen Sie den **Speicherort** für das Speicherkonto.

	5. Klicken Sie auf **Erstellen**. Das Konto wird nun im Bereich **Speicherkonten** angezeigt.

		![Speicherkontodetails eingeben](./media/virtual-machines-windows-upload-image/portal_create_storage_account.png)

	6. Dieser und die nächsten Schritte zeigen Ihnen, wie Sie einen Blobcontainer in diesem Speicherkonto erstellen. Dies ist optional, da der PowerShell-Befehl zum Hochladen des Images auch einen neuen Blobcontainer für Ihr Image erstellen kann. Wenn Sie ihn nicht selbst erstellen möchten, fahren Sie mit dem Abschnitt [Hochladen des VM-Images auf Ihr Speicherkonto](#uploadvm) fort. Klicken Sie andernfalls in der Kachel **Dienste** auf **Blobs**.

		![Blob-Dienst](./media/virtual-machines-windows-upload-image/portal_create_blob.png)

	7. Wenn der Blobbereich angezeigt wird, klicken Sie auf **+ Container**, um einen neuen Blobspeichercontainer zu erstellen. Geben Sie den Namen des Containers und den Zugriffstyp ein.

		![Erstellen eines neuen Blobs](./media/virtual-machines-windows-upload-image/portal_create_container.png)

  		> [AZURE.NOTE] Der Container ist standardmäßig privat, und der Containerzugriff ist auf den Kontobesitzer eingeschränkt. Um öffentliche Lesezugriffe auf die im Container enthaltenen Blobs, jedoch nicht auf die Containereigenschaften und -metadaten zuzulassen, verwenden Sie die Option **Blob**. Um vollständigen öffentlichen Lesezugriff auf den Container und die Blobs zuzulassen, verwenden Sie die Option **Container**.

	8. Im Bereich **Blob-Dienst** wird der neue Blobcontainer aufgelistet. Notieren Sie die URL dieses Containers, denn Sie benötigen sie für den PowerShell-Befehl zum Hochladen des Images. Abhängig von der Länge der URL und Ihrer Bildschirmauflösung wird die URL möglicherweise teilweise ausgeblendet. Maximieren Sie in diesem Fall den Bereich, indem Sie auf das Symbol *Maximieren* in der oberen rechten Ecke klicken.


### Mithilfe von PowerShell

1. Öffnen Sie Azure PowerShell 1.0.x, und melden Sie sich bei Ihrem Azure-Konto an.

		Login-AzureRmAccount

	Dieser Befehl öffnet ein Popupfenster, in dem Sie Ihre Azure-Anmeldeinformationen eingeben können.

2. Wenn sich die standardmäßig ausgewählte Abonnement-ID von derjenigen unterscheidet, mit der Sie arbeiten möchten, verwenden Sie einen der beiden folgenden Befehle, um das gewünschte Abonnement festzulegen.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	oder

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Sie finden die in Ihrem Azure-Konto enthaltenen Abonnements mit dem Befehl `Get-AzureRmSubscription`.

3. Suchen Sie die unter diesem Abonnement verfügbaren Speicherkonten.

		Get-AzureRmStorageAccount

	Wenn Sie ein vorhandenes Speicherkonto verwenden möchten, fahren Sie mit dem Abschnitt [Hochladen des VM-Images auf Ihr Speicherkonto](#uploadvm) fort.

4. Wenn Sie ein neues Speicherkonto zum Speichern dieses Images erstellen möchten, gehen Sie folgendermaßen vor:

	1. Stellen Sie sicher, dass Sie über eine Ressourcengruppe für dieses Speicherkonto verfügen. Mit folgendem Befehl finden Sie alle Ressourcengruppen in Ihrem Abonnement:

			Get-AzureRmResourceGroup

	2. Wenn Sie eine neue Ressourcengruppe erstellen möchten, verwenden Sie diesen Befehl:

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. Erstellen Sie ein neues Speicherkonto in dieser Ressourcengruppe mit:

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>
## Hochladen des VM-Images auf Ihr Speicherkonto

Verwenden Sie diese Schritte in Azure PowerShell, um das VM-Image auf Ihr Speicherkonto hochzuladen. Ihr Image wird in einen Blobspeichercontainer in diesem Konto hochgeladen; Sie können einen vorhandenen Container verwenden oder einen neuen erstellen.

1. Melden Sie sich mit `Login-AzureRmAccount` bei Azure PowerShell 1.0.x an, und stellen Sie mit `Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` sicher, dass Sie das richtige Abonnement verwenden, wie im vorherigen Abschnitt erwähnt.

2. Fügen Sie die allgemeine Azure-VHD mit [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) dem Speicherkonto hinzu:

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	Hierbei gilt:
	- **StorageAccountURL** ist die URL für das Speicherkonto. In der Regel hat sie folgendes Format: `https://YourStorageAccountName.blob.core.windows.net`. Beachten Sie, dass Sie anstelle von **YourStorageAccountName** den Namen des vorhandenen oder neuen Speicherkontos verwenden müssen.
	- **BlobContainer** ist der BLOB-Container, in dem Sie Ihre Images speichern möchten. Wenn das Cmdlet keinen vorhandenen BLOB-Container dieses Namens findet, erstellt es einen neuen für Sie.
	- **TargetVHDName** ist der Name, unter dem Sie das Image speichern möchten.
	- **LocalPathOfVHDFile** ist der vollständige Pfad und Name der VHD-Datei auf Ihrem lokalen Computer.

	Eine erfolgreiche Ausführung von `Add-AzureRmVhd` sieht folgendermaßen aus:

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file  C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

	Die Ausführung dieses Befehls nimmt – abhängig von Ihrer Netzwerkverbindung und der Größe Ihrer VHD-Datei – einige Zeit in Anspruch.

</br>
## Bereitstellen eines neuen virtuellen Computers vom hochgeladenen Image aus

Jetzt können Sie das hochgeladene Image verwenden, um eine neue Windows-VM zu erstellen. In den folgenden Schritten lernen Sie, wie Sie diese VM mit Azure PowerShell und dem in den vorherigen Schritten hochgeladenen VM-Image in einem neuen virtuellen Netzwerk erstellen.

>[AZURE.NOTE] Das VM-Image und der zu erstellende virtuelle Computer müssen sich in dem gleichen Speicherkonto befinden.

### Erstellen von Netzwerkressourcen

Verwenden Sie das folgende PowerShell-Beispielskript, um ein virtuelles Netzwerk und eine Netzwerkschnittstellenkarte (Network Interface Card, NIC) für den neuen virtuellen Computer zu erstellen. Verwenden Sie für die durch das **$**-Zeichen dargestellten Variablen Werte, die für Ihre Anwendung geeignet sind.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Erstellen eines neuen virtuellen Computers

Das folgende PowerShell-Skript veranschaulicht, wie Sie die Konfiguration für den virtuellen Computer einrichten und das hochgeladene VM-Image als Quelle für die neue Installation verwenden. </br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Beispielsweise könnte der Workflow wie folgt aussehen:

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

Nun müsste der neu erstellte virtuelle Computer angezeigt werden – entweder im [Azure-Portal](https://portal.azure.com) unter **Durchsuchen** > **Virtuelle Computer** ODER nach Ausführen der folgenden PowerShell-Befehle:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Nächste Schritte

Informationen zum Verwalten des neuen virtuellen Computers mit Azure PowerShell finden Sie unter [Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager und PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0323_2016-->