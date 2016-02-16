<properties
	pageTitle="Verwenden von Azure Premium-Speicher mit SQL Server | Microsoft Azure"
	description="In diesem Artikel verwendet Ressourcen, die mit dem klassischen Bereitstellungsmodell erstellt wurden, und stellt eine Anleitung zur Verwendung von Azure Premium-Speicher mit SQL Server auf virtuellen Azure-Computern bereit."
	services="virtual-machines"
	documentationCenter=""
	authors="danielsollondon"
	manager="jeffreyg"
	editor="monicar"    
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="01/22/2016"
	ms.author="jroth"/>

# Verwenden von Azure Premium-Speicher mit SQL Server auf virtuellen Computern


## Übersicht

[Azure Premium-Speicher](../storage-premium-storage-preview-portal.md) ist die nächste Speichergeneration mit geringer Latenz und hohem E/A-Durchsatz. Er ist ideal geeignet für hohe E/A-Workloads, wie z. B. SQL Server auf [virtuellen IaaS-Computern](https://azure.microsoft.com/services/virtual-machines/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Dieser Artikel enthält die Planung und Anleitungen zum Migrieren eines virtuellen Computers für die Verwendung mit Premium-Speicher. Dies umfasst Schritte für die Azure-Infrastruktur (Netzwerk, Speicher) und virtuelle Windows-Gastsysteme. Das Beispiel im [Anhang](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) zeigt eine vollständige End-to-End-Migration zum Verschieben größerer virtueller Computer mit PowerShell, um die verbesserten lokalen SSD-Speicher zu nutzen.

Es ist wichtig, den End-to-End-Prozess einschließlich der Nutzung des Premium-Azure-Speichers mit SQL Server auf virtuellen IAAS-Computern zu verstehen. Dies umfasst:

- Bestimmen der Voraussetzungen für die Verwendung von Premium-Speicher.
- Beispiele für die Bereitstellung von SQL Server unter IaaS auf Premium-Speicher für neue Bereitstellungen.
- Beispiele für die Migration vorhandener Bereitstellungen von sowohl eigenständigen Servern als auch Bereitstellungen mit SQL-AlwaysOn-Verfügbarkeitsgruppen.
- Mögliche Migrationsansätze.
- Vollständige End-to-End-Beispiel für Azure-, Windows- und SQL Server-Schritte zur Migration einer vorhandenen AlwaysOn-Implementierung.

Ausführlichere Informationen zur Verwendung von SQL Server auf virtuellen Azure-Computern finden Sie unter [SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md).

**Autor**: Daniel Sol, **Technische Bearbeiter**: Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Jürgen Thomas, Gonzalo Ruiz

## Voraussetzungen für Premium-Speicher

Es gibt einige Voraussetzungen für die Verwendung von Premium-Speicher.

### Größe des Computers

Für die Verwendung von Premium-Speicher müssen Sie virtuelle Computer der DS-Serie verwenden. Wenn Sie bisher keine Computer der DS-Serie im Clouddienst verwendet haben, müssen Sie den vorhandenen virtuellen Computer löschen, die angefügten Datenträger beibehalten und dann einen neuen Clouddienst vor der erneuten Erstellung des virtuellen Computers mit DS*-Rollengröße erstellen. Weitere Informationen zu den Größen von virtuellen Computern finden Sie unter [Größen virtueller Computer und Clouddienste für Microsoft Azure](virtual-machines-size-specs.md).

### Clouddienste

Sie können nur virtuelle Computer der DS*-Serie mit Premium-Speicher verwenden, wenn sie in einem neuen Clouddienst erstellt werden. Bei Verwendung von SQL Server AlwaysOn in Azure wird der AlwaysOn-Listener auf die interne Azure- oder die externe Lastenausgleichs-IP-Adresse verweisen, die einem Clouddienst zugeordnet ist. In diesem Artikel wird die Migration unter Aufrechterhaltung der Verfügbarkeit für dieses Szenario beschrieben.

> [AZURE.NOTE] Zunächst muss ein virtueller Computer der DS*-Serie im neuen Clouddienst bereitgestellt werden.

### Regionale VNETs

Für den virtuellen Computer der DS*-Serie müssen Sie das virtuelle Netzwerk (VNET), in dem die virtuellen Computer gehostet werden, als regional konfigurieren. Dadurch wird das virtuelle Netzwerk erweitert, da größere virtuelle Computer in anderen Clustern bereitgestellt werden und die Kommunikation zwischen diesen ermöglicht wird. Im folgenden Screenshot werden im markierten Bereich regionale virtuelle Netzwerke und im ersten Ergebnis ein eingeschränktes virtuelles Netzwerk dargestellt.

![RegionalVNET][1]

Sie können ein Microsoft-Support-Ticket für die Migration zu einem regionalen VNET erstellen. Microsoft nimmt dann eine Änderung vor und wird anschließend die Migration zu regionalen VNETs abschließen und die "AffinityGroup"-Eigenschaft in der Netzwerkkonfiguration ändern. Exportieren Sie zunächst die Netzwerkkonfiguration in PowerShell, und ersetzen Sie dann die **AffinityGroup**-Eigenschaft im **VirtualNetworkSite**-Element mit einer **Location**-Eigenschaft. Geben Sie `Location = XXXX` an, wobei `XXXX` für eine Azure-Region steht. Importieren Sie dann die neue Konfiguration.

Gehen wir beispielsweise von folgender VNET-Konfiguration aus:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Um diese in ein regionales VNET in Westeuropa zu verschieben, ändern Sie die Konfiguration folgendermaßen:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### Speicherkonten

Sie müssen ein neues Speicherkonto erstellen, das für Premium-Speicher konfiguriert ist. Beachten Sie, dass die Verwendung von Premium-Speicher für das Speicherkonto, nicht aber für einzelne VHDs festgelegt wird. Wenn Sie jedoch virtuelle Computer der DS*-Serie verwenden, können Sie VHDs aus Premium- und Standard- Speicherkonten anfügen. Sie sollten dies in Erwägung ziehen, wenn Sie die Betriebssystem-VHD nicht im Premium-Speicherkonto platzieren möchten.

Durch den folgenden **New-AzureStorageAccountPowerShell**-Befehl mit dem **Typ** "Premium\_LRS" wird ein Premium-Speicherkonto erstellt:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### Cacheeinstellungen für virtuelle Festplatten

Der Hauptunterschied beim Erstellen von Datenträgern, die Teil eines Premium-Speicherkontos sind, ist die Datenträger-Cacheeinstellung. Für SQL Server-Datenvolumes wird die Verwendung von **"Lesecache"** empfohlen. Für Transaktions-Protokollvolumes sollten die Datenträger-Cacheeinstellungen auf **"Keine"** festgelegt werden. Dies unterscheidet sich von den Empfehlungen für die Standardspeicherkonten.

Nachdem die VHDs angefügt wurden, kann die Cacheeinstellung nicht geändert werden. Sie müssen die virtuelle Festplatte trennen und mit aktualisierten Cacheeinstellungen erneut anfügen.

### Windows-Speicherplätze

Sie können [Windows-Speicherplätze](https://technet.microsoft.com/library/hh831739.aspx) wie beim vorherigen Standardspeicher verwenden. Dadurch können Sie einen virtuellen Computer migrieren, der bereits Speicherplätze verwendet. Das Beispiel im [Anhang](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) (ab Schritt 9) zeigt den PowerShell-Code zum Extrahieren und Importieren eines virtuellen Computers mit mehreren angefügten virtuellen Festplatten.

Speicherpools wurden bei Standard-Azure-Speicherkonten verwendet, um den Durchsatz zu verbessern und die Latenz zu verringern. Unter Umständen birgt es Vorteile, Speicherpools mit Premium-Speicher für neue Bereitstellungen zu testen. Dies kann die Einrichtung des Speichers jedoch komplexer gestalten.

#### Wie finden Sie heraus, welche virtuellen Azure-Festplatten Speicherpools zugeordnet sind?

Da es unterschiedliche Cache-Einstellungsempfehlungen für die angefügten virtuellen Festplatten gibt, empfiehlt es sich, die virtuellen Festplatten auf ein Premium-Speicherkonto zu kopieren. Wenn Sie sie jedoch erneut an einen neuen virtuellen Computer der DS-Serie anfügen, müssen Sie die Cacheeinstellungen möglicherweise ändern. Es ist einfacher, die empfohlenen Cacheeinstellungen für Premium-Speicher anzuwenden, wenn Sie über separate virtuelle Festplatten für die SQL-Datendateien und -Protokolldateien (anstatt einer einzelnen VHD, die beide enthält) verfügen.

> [AZURE.NOTE] Wenn Sie über SQL Server-Daten- und -Protokolldateien auf demselben Volume verfügen, hängt es von den E/A-Zugriffsmustern für die Datenbankauslastungen ab, welche Option zum Zwischenspeichern Sie wählen. Nur Tests können zeigen, welche Option zum Zwischenspeichern für dieses Szenario am besten geeignet ist.

Bei der Verwendung von Windows-Speicherplätzen, die aus mehreren VHDs bestehen, müssen Sie jedoch die ursprünglichen Skripts verwenden, um festzustellen, welche virtuellen Festplatten sich in welchem Pool befinden, sodass Sie die Cacheeinstellungen entsprechend für jeden Datenträger festlegen können.

Wenn Sie nicht mit dem ursprünglichen Skript ermitteln können, welche virtuellen Festplatten dem Speicherpool zugeordnet sind, können Sie mithilfe der folgenden Schritte die Datenträger-/Speicher-Poolzuordnung bestimmen.

Führen Sie für jeden Datenträger die folgenden Schritte aus:

1. Rufen Sie die Liste der mit dem virtuellen Computer verbundenen Datenträger mit dem Befehl **Get-AzureVM** ab:

    Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk

1. Notieren Sie Datenträgername und LUN.

	![DisknameAndLUN][2]

1. Stellen Sie eine Remotedesktopverbindung her. Wechseln Sie zu **Computerverwaltung** | **Geräte-Manager** | **Laufwerke**. Sehen Sie sich die Eigenschaften der einzelnen "virtuellen Microsoft-Datenträger" an.

	![VirtualDiskProperties][3]

1. Die hier aufgeführte LUN-Zahl ist ein Verweis auf die LUN-Zahl, die Sie beim Anfügen der virtuellen Festplatte an den virtuellen Computer angegeben haben.
1. Öffnen Sie die Registerkarte **Details** für den "virtuellen Microsoft-Datenträger", und wechseln Sie dann in der Liste **Eigenschaften** zu **Treiberschlüssel**. Notieren Sie sich unter **Wert** den **Offset**, der im folgenden Screenshot "0002" beträgt. Der Wert "0002" kennzeichnet die "PhysicalDisk2", auf die vom Speicherpool verwiesen wird.

	![VirtualDiskPropertyDetails][4]

2. Erstellen Sie ein Speicherabbild jedes zugeordneten Datenträgers im Speicherpool:

    Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk

	![GetStoragePool][5]

Jetzt können Sie diese Informationen verwenden, um physischen Datenträgern in Speicherpools virtuelle Festplatten zuzuordnen.

Wenn Sie virtuelle Festplatten physikalischen Datenträgern in Speicherpools zugeordnet haben, können Sie sie trennen, in ein Premium-Speicherkonto kopieren und sie dann mit den richtigen Cacheeinstellungen wieder anfügen. Ein Beispiel dazu finden Sie im [Anhang](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) in den Schritten 8 bis 12. Diese Schritte zeigen, wie Sie die Konfiguration einer an einen virtuellen Computer angefügten virtuellen Festplatte in eine CSV-Datei extrahieren, die virtuellen Festplatten kopieren, die Cachekonfigurationseinstellungen des Datenträgers ändern und schließlich den virtuellen Computer als VM der DS-Serie mit allen angefügten Datenträgern erneut bereitstellen.

### Speicherbandbreite des virtuellen Computers und Durchsatz des VHD-Speichers

Die Höhe der Speicherleistung hängt von der angegebenen Größe des virtuellen Computers der DS*-Serie und der Größe der virtuellen Festplatte ab. Die virtuellen Computer haben verschiedene Beschränkungen für die Anzahl von VHDs, die angefügt werden können, und für die maximal unterstützte Bandbreite (MB/s). Ausführliche Informationen zu den Bandbreitenbeschränkungen finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](virtual-machines-size-specs.md).

Höhere IOPS werden mit größeren Datenträgern erreicht. Sie sollten dies berücksichtigen, wenn Sie Ihren Migrationspfad planen. Weitere Informationen finden Sie in der [Tabelle für IOPS und Datenträgertypen](../storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whde-DEing-premium-storage).

Bedenken Sie, dass virtuelle Computer unterschiedliche maximale Datenträgerbandbreiten haben, die sie für alle verbundenen Datenträger unterstützen. Unter hoher Last könnten Sie die maximale Bandbreite für diese VM-Rollengröße auslasten. Beispielsweise unterstützt eine Standard\_DS14 bis zu 512 MB/s. Daher könnten Sie mit drei P30-Datenträgern die Bandbreite des Datenträgers des virtuellen Computers auslasten. In diesem Beispiel kann jedoch der Durchsatzgrenzwert je nach E/A-Lese- und Schreibvorgängen überschritten werden.

## Neue Bereitstellungen

In den nächsten beiden Abschnitten wird veranschaulicht, wie Sie SQL Server-VMs in Premium-Speicher bereitstellen können. Wie bereits erwähnt, müssen Sie Betriebssystemdatenträger nicht zwingend in Premium-Speicher platzieren. Sie sollten dies jedoch in Erwägung ziehen, wenn Sie beabsichtigen, alle hohen E/A-Workloads auf die Betriebssystem-VHD zu verlagern.

Im ersten Beispiel werden vorhandene Images aus dem Azure-Katalog verwendet. Im zweiten Beispiel wird veranschaulicht, wie Sie ein benutzerdefiniertes VM-Image verwenden, das sich in einem vorhandenen Speicherkonto befindet.

> [AZURE.NOTE] In diesen Beispielen wird davon ausgegangen, dass Sie bereits ein regionales VNET erstellt haben.

### Erstellen eines neuen virtuellen Computers mit Premium-Speicher aus einem Katalogimage

Das folgende Beispiel zeigt, wie Sie die Betriebssystem-VHD in Premium-Speicher platzieren und Premium-Speicher-VHDs anfügen. Sie können den Betriebssystemdatenträger jedoch auch in einem Standard-Speicherkonto speichern und virtuelle Festplatten aus einem Premium-Speicherkonto anfügen. Beide Szenarios werden erläutert.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### Schritt 1: Erstellen eines Premium-Speicherkontos


    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### Schritt 2: Erstellen eines neuen Clouddiensts

    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### Schritt 3: Reservieren einer Clouddienst-VIP-Adresse (optional)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### Schritt 4: Erstellen eines VM-Containers
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### Schritt 5: Platzieren der Betriebssystem-VHD im Standard- oder Premium-Speicherkonto
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### Schritt 6: Erstellen des virtuellen Computers
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### Erstellen eines neuen virtuellen Computers mit Premium-Speicher mit einem benutzerdefinierten Image

In diesem Szenario wird veranschaulicht, wie Sie vorhandene benutzerdefinierte Images verwenden, die sich in einem Standard-Speicherkonto befinden. Wie zuvor erläutert, können Sie die Betriebssystem-VHD in einem Premium-Speicherkonto speichern. Sie müssen jedoch das Image aus dem Standard-Speicherkonto in den Premium-Speicher kopieren, bevor es verwendet werden kann. Wenn Sie ein lokales Image haben, können Sie diese Methode auch verwenden, um dieses direkt in das Premium-Speicherkonto zu kopieren.

#### Schritt 1: Erstellen eines Speicherkontos
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### Schritt 2: Erstellen des Clouddiensts
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### Schritt 3: Verwenden des vorhandenen Images
Sie können ein vorhandenes Image verwenden. Oder Sie verwenden [ein Image eines vorhandenen Computers](virtual-machines-capture-image-windows-server.md). Der Computer, von dem Sie ein Image erstellen, muss kein Computer der DS*-Serie sein. Wenn Sie über ein Image verfügen, wird in den folgenden Schritten erläutert, wie Sie das PowerShell-Cmdlet **Start-AzureStorageBlobCopy** verwenden, um das Image in das Premium-Speicherkonto zu kopieren.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### Schritt 4: Kopieren von Blobs zwischen Speicherkonten
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### Schritt 5: Regelmäßiges Überprüfen des Kopierstatus:
    $blob | Get-AzureStorageBlobCopyState

#### Schritt 6: Hinzufügen eines Imagedatenträgers zum Azure-Datenträger-Repository im Abonnement
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [AZURE.NOTE] Es kann vorkommen, dass ein Datenträger-Leasefehler auftritt, obwohl der Status als erfolgreich angezeigt wird. In diesem Fall warten Sie ca. zehn Minuten.

#### Schritt 7: Erstellen des virtuellen Computers
Hier erstellen Sie den virtuellen Computer aus dem Image und fügen zwei Premium-Speicher-VHDs an:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## Vorhandene Bereitstellungen, die keine AlwaysOn-Verfügbarkeitsgruppen verwenden

> [AZURE.NOTE] Lesen Sie bei vorhandenen Bereitstellungen zunächst den Abschnitt [Voraussetzungen](#prerequisites-for-premium-storage) dieses Themas.

Es gibt verschiedene Überlegungen für SQL Server-Bereitstellungen, die keine AlwaysOn-Verfügbarkeitsgruppen verwenden. Wenn Sie keine AlwaysOn-Funktionen benötigen und über einen eigenständigen SQL Server verfügen, können Sie ein Upgrade auf Premium-Speicher durchführen, indem Sie einen neuen Clouddienst und ein neues Speicherkonto verwenden. Betrachten Sie die folgenden Optionen:

- **Erstellen einer neuen SQL Server-VM**. Sie können einen neuen virtuellen SQL Server-Computer erstellen, der ein Premium-Speicherkonto verwendet, wie dies unter "Neue Bereitstellungen" dokumentiert ist. Erstellen Sie anschließend eine Sicherung der SQL Server-Konfiguration und -Benutzerdatenbanken, und stellen Sie sie wieder her. Die Anwendung muss aktualisiert werden, um auf den neuen SQL Server zu verweisen, wenn intern oder extern auf diesen zugegriffen wird. Sie müssen alle "out of db"-Objekte kopieren, als ob Sie eine parallele (SxS) SQL Server-Migration ausführen. Dies umfasst Objekte, wie z. B. Anmeldenamen, Zertifikate und Verbindungsserver.
- **Migrieren eines vorhandenen SQL Server-VM**. Dies erfordert, dass die virtuellen SQL Server-Computer offline geschaltet und anschließend an einen neuen Clouddienst übertragen werden, einschließlich des Kopierens aller angefügten virtuellen Festplatten zum Premium-Speicherkonto. Wenn der virtuelle Computer online geschaltet wird, verweist die Anwendung wie zuvor auf den Serverhostnamen. Denken Sie daran, dass sich die Größe des vorhandenen Datenträgers auf die Leistungsmerkmale auswirkt. Beispielsweise wird ein 400-GB-Datenträger auf einen P20 aufgerundet. Wenn Sie wissen, dass Sie diese Datenträgerleistung nicht benötigen, können Sie den virtuellen Computer als DS-Serie-VM neu erstellen und virtuelle Premium-Speicher-VHDs mit den erforderlichen Größen-/Leistungs-Spezifikationen anfügen. Anschließend können Sie die SQL-Datenbankdateien trennen und neu verbinden.

> [AZURE.NOTE] Beim Kopieren der VHD-Datenträger sollten Sie sich über die Größe bewusst sein. Der Premium-Speicher-Datenträgertyp wird anhand der Größe festgelegt, die wiederum auch die Datenträgerleistung bestimmt. Azure wird auf die nächstliegende Größe aufgerundet, sodass ein 400-GB-Datenträger als P20 eingestuft wird. Abhängig von den bestehenden E/A-Anforderungen der Betriebssystem-VHD müssen Sie dadurch möglicherweise nicht zu einem Premium-Speicherkonto migrieren.

Wenn auf den SQL Server extern zugegriffen wird, ändert sich die Clouddienst-VIP. Außerdem müssen Sie die Endpunkt-, ACL- und DNS-Einstellungen aktualisieren.

## Vorhandene Bereitstellungen, die AlwaysOn-Verfügbarkeitsgruppen verwenden

> [AZURE.NOTE] Lesen Sie bei vorhandenen Bereitstellungen zunächst den Abschnitt [Voraussetzungen](#prerequisites-for-premium-storage) dieses Themas.

In diesem Abschnitt betrachten wir zunächst die Interaktion von AlwaysOn mit Azure-Netzwerken. Anschließend werden Migrationen anhand von zwei Szenarios erläutert: Migrationen, in denen eine Ausfallzeit toleriert werden kann, und Migrationen mit minimaler Ausfallzeit.

Lokale SQL Server-AlwaysOn-Verfügbarkeitsgruppen verwenden einen lokalen Listener, der einen virtuellen DNS-Namen mit einer IP-Adresse registriert, die von einem oder mehreren SQL Server-Computern gemeinsam verwendet wird. Wenn Clients eine Verbindung herstellen, werden sie über die Listener-IP-Adresse an den primären SQL Server-Computer weitergeleitet. Dies ist der Server, der die AlwaysOn-IP-Adressressource zu diesem Zeitpunkt besitzt.

![DeploymentsUseAlwaysOn][6]

In Microsoft Azure kann einer Netzwerkkarte auf dem virtuellen Computer nur eine IP-Adresse zugewiesen werden. Um die gleiche Abstraktionsebene zu erreichen wie lokal, nutzt Azure daher die IP-Adresse, die den internen/externen Lastenausgleichsmodulen (ILB/ELB) zugewiesen wird. Die IP-Adressressource, die von den Servern gemeinsam genutzt wird, ist auf die gleiche IP-Adresse festgelegt wie die internen/externen Lastenausgleichsmodule. Diese wird im DNS veröffentlicht, und der Clientdatenverkehr wird über die ILB/ELB an das primäre SQL Server-Replikat übergeben. Die ILB/ELB wissen, welches der primäre SQL Server ist, da dieser Prüfpunkte verwendet, um die AlwaysOn-IP-Adressressource zu prüfen. Im vorherigen Beispiel wird jeder Knoten überprüft, der einen Endpunkt mit Verweis auf die ELB/ILB hat. Der antwortende Server ist der primäre SQL Server.

> [AZURE.NOTE] Die ILB und ELB werden einem bestimmten Azure-Clouddienst zugewiesen, daher wird bei jeder Cloudmigration in Azure wahrscheinlich die IP-Adresse des Lastenausgleichsmoduls geändert.

### Migrieren von AlwaysOn-Bereitstellungen, die eine Ausfallzeit ermöglichen

Es gibt zwei Strategien zum Migrieren von AlwaysOn-Bereitstellungen, die Ausfallzeiten zulassen:

1. **Hinzufügen weiterer sekundärer Replikate zu einem vorhandenen AlwaysOn-Cluster**
1. **Migrieren zu einem neuen AlwaysOn-Cluster**

#### 1\. Hinzufügen weiterer sekundärer Replikate zu einem vorhandenen AlwaysOn-Cluster

Eine Strategie ist das Hinzufügen weiterer sekundärer Replikate zur AlwaysOn-Verfügbarkeitsgruppe. Sie müssen diese Replikate in einem neuen Clouddienst hinzufügen und den Listener mit der neuen IP-Adresse des Lastenausgleichsmoduls aktualisieren.

##### Zeitpunkte von Ausfallzeiten:

- Bei der Clusterüberprüfung.
- Bei AlwaysOn-Failovertests für die neuen sekundären Replikate.

Wenn Sie Windows-Speicherpools innerhalb des virtuellen Computers für einen höheren E/A-Durchsatz verwenden, werden diese während einer vollständigen Clusterüberprüfung offline geschaltet. Die Überprüfung ist erforderlich, wenn Sie dem Cluster Knoten hinzufügen. Der Zeitaufwand zum Ausführen des Tests kann variieren. Sie sollten den Test daher in einer repräsentativen Testumgebung ausführen, um die ungefähre Dauer festzustellen.

Sie sollten Zeit für die Durchführung manueller Failover- und Chaostests auf den neu hinzugefügten Knoten planen, um sicherzustellen, dass die AlwaysOn-Verfügbarkeitsfunktionen wie erwartet ausgeführt werden können.

![DeploymentUseAlwaysOn2][7]

> [AZURE.NOTE] Sie sollten alle SQL Server-Instanzen beenden, in denen Speicherpools verwendet werden, bevor die Überprüfung ausgeführt wird.
##### Schritte auf oberer Ebene:

1. Erstellen Sie zwei neue SQL Server im neuen Clouddienst mit angefügtem Premium-Speicher.
1. Kopieren Sie VOLLSTÄNDIGE Sicherungen, und stellen Sie sie mit **NORECOVERY** wieder her.
1. Kopieren Sie abhängige Objekte aus der Benutzerdatenbank, wie z. B. Anmeldungen usw.
1. Erstellen Sie einen neuen internen Lastenausgleich (ILB) oder verwenden Sie einen externen Lastenausgleich (ELB), und richten Sie Lastenausgleichs-Endpunkte auf beiden neuen Knoten ein.
> [AZURE.NOTE] Prüfen Sie vor dem Fortfahren, ob alle Knoten die korrekte Endpunktkonfiguration besitzen.

1. Beenden Sie den Benutzer-/Anwendungszugriff auf SQL Server (bei Verwendung von Speicherpools).
1. Beenden Sie SQL Server-Datenbankmoduldienste auf allen Knoten (bei Verwendung von Speicherpools).
1. Fügen Sie neue Knoten zum Cluster hinzu, und führen Sie eine vollständige Überprüfung durch.
1. Sobald die Überprüfung erfolgreich abgeschlossen wurde, starten Sie alle SQL Server-Dienste.
1. Sichern Sie die Transaktionsprotokolle, und stellen Sie die Benutzerdatenbanken wieder her.
1. Fügen Sie neue Knoten zur AlwaysOn-Verfügbarkeitsgruppe hinzu, und speichern Sie die Replikation in **Syncronous**.
1. Fügen Sie die IP-Adressressource der neuen ILB/ELB des Clouddiensts über PowerShell für AlwaysOn hinzu. Nehmen Sie als Grundlage das Beispiel mit mehreren Standorten im [Anhang](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage). Legen Sie in den Windows-Clustern die **Mögliche Besitzer** der Ressource **IP-Adresse** auf die neuen Knoten fest. Weitere Informationen finden Sie im Abschnitt "Hinzufügen einer IP-Adressressource im gleichen Subnetz" im [Anhang](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
1. Führen Sie ein Failover zu einem der neuen Knoten aus.
1. Legen Sie die neuen Knoten als automatische Failoverpartner fest, und testen Sie die Failoverfunktionalität.
1. Entfernen Sie die ursprünglichen Knoten aus der Verfügbarkeitsgruppe.

##### Vorteile

- Neue SQL Server (SQL Server und Anwendung) können getestet werden, bevor sie zu AlwaysOn hinzugefügt werden.
- Sie können die VM-Größe ändern und den Speicher exakt an Ihre Anforderungen anpassen. Allerdings ist es von Vorteil, die SQL-Dateipfade beizubehalten.
- Sie können festlegen, wann die Übertragung der DB-Sicherungen an die sekundären Replikate gestartet wird. Dies unterscheidet sich von der Verwendung des Azure-Cmdlets **Start-AzureStorageBlobCopy** zum Kopieren von VHDs, da es sich um eine asynchrone Kopie handelt.

##### Nachteile
- Bei der Verwendung von Windows-Speicherpools entstehen Ausfallzeiten während der vollständigen Clusterüberprüfung der neuen zusätzlichen Knoten.
- Je nach SQL Server-Version und Anzahl an vorhandenen sekundären Replikaten können Sie möglicherweise nicht mehrere sekundäre Replikate hinzufügen, ohne vorhandene sekundäre Replikate zu entfernen.
- Es können lange SQL-Datenübertragungen beim Einrichten der sekundären Replikate auftreten.
- Es entstehen zusätzliche Kosten bei der Migration, während Sie neue Computer parallel ausführen.

#### 2\. Migrieren zu einem neuen AlwaysOn-Cluster

Eine andere Strategie ist die Erstellung eines völlig neuen AlwaysOn-Clusters mit neuen Knoten im neuen Clouddienst, an den die Clients weitergeleitet werden.

##### Zeitpunkte von Ausfallzeiten:

Es entstehen Ausfallzeiten bei der Übertragung von Anwendungen und Benutzern zum neuen AlwaysOn-Listener. Diese Ausfallzeiten sind abhängig von:

- Der Zeit bis zum Abschluss der Wiederherstellung der endgültigen Transaktionsprotokollsicherungen in Datenbanken auf dem neuen Server.
- Der Zeit für Updates der Clientanwendungen, sodass diese die neuen AlwaysOn-Listener verwenden.

##### Vorteile

- Sie können die tatsächliche Produktionsumgebung, den SQL Server und die Buildänderungen des Betriebssystems testen.
- Sie haben die Möglichkeit, den Speicher anzupassen und die Größe des virtuellen Computers zu verringern. Dies kann zu Kosteneinsparungen führen.
- Während dieses Vorgangs können Sie den SQL Server-Build oder die Version aktualisieren. Sie können auch ein Upgrade des Betriebssystems durchführen.
- Die vorherigen AlwaysOn-Cluster können als stabiles Rollbackziel fungieren.

##### Nachteile

- Sie müssen den DNS-Namen des Listeners ändern, wenn beide AlwaysOn-Cluster gleichzeitig ausgeführt werden sollen. Dadurch entsteht während der Migration ein größerer Aufwand bei der Verwaltung, da die Client-Anwendungszeichenfolgen den neuen Namen des Listeners berücksichtigen müssen.
- Sie müssen einen Synchronisierungsmechanismus zwischen den beiden Umgebungen implementieren, um diese so synchron wie möglich zu halten, sodass die Anforderungen bei der letzten Synchronisierung vor der Migration nicht zu hoch sind.
- Es entstehen zusätzliche Kosten während der Migration, während die neue Umgebung ausgeführt wird.

### Migrieren von AlwaysOn-Bereitstellungen für minimale Ausfallzeiten

Es gibt zwei Strategien für die Migration von AlwaysOn-Bereitstellungen mit minimalen Ausfallzeiten:

1. **Verwenden von vorhandenen sekundären Replikaten: Einzelstandort**
1. **Verwenden von vorhandenen sekundären Replikaten: Mehrere Standorte**

#### 1\. Verwenden eines vorhandenen sekundären Replikats: Einzelstandort

Eine Strategie für minimale Ausfallzeiten ist die sekundäre Verwendung einer vorhandenen Cloud mit anschließender Entfernung aus dem aktuellen Clouddienst. Sie kopieren dann die VHDs in das neue Premium-Speicherkonto und erstellen den virtuellen Computer im neuen Clouddienst. Aktualisieren Sie dann den Listener für Cluster und Failover.

##### Zeitpunkte von Ausfallzeiten:

- Es entstehen Ausfallzeiten beim Aktualisieren des letzten Knotens mit dem Lastenausgleichsendpunkt.
- Abhängig von Ihrer Client-/DNS-Konfiguration kann die erneute Clientverbindung verzögert werden.
- Es gibt zusätzliche Ausfallzeiten, wenn Sie die AlwaysOn-Clustergruppe offline schalten, um die IP-Adressen zu wechseln. Sie können dies vermeiden, indem Sie eine OR-Abhängigkeit und mögliche Besitzer für die hinzugefügte IP-Adressressource verwenden. Weitere Informationen finden Sie im Abschnitt "Hinzufügen einer IP-Adressressource im gleichen Subnetz" im [Anhang](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).

> [AZURE.NOTE] Wenn Sie den hinzugefügten Knoten als AlwaysOn-Failoverpartner einsetzen möchten, müssen Sie einen Azure-Endpunkt mit einem Verweis auf den Lastenausgleichssatz hinzufügen. Wenn Sie dazu den Befehl **Add-AzureEndpoint** ausführen, bleiben die aktuelle Verbindungen geöffnet, es können jedoch keine neuen Verbindungen mit dem Listener hergestellt werden, bis das Lastenausgleichsmodul aktualisiert wurde. Tests ergaben, dass dies 90–120 Sekunden dauert.

##### Vorteile

- Keine zusätzlichen Kosten während der Migration.
- 1:1-Migration.
- Geringere Komplexität.
- Verbesserte IOPS durch Premium-Speicher-SKUs. Wenn die Datenträger vom virtuellen Computer getrennt und in den neuen Clouddienst kopiert werden, können Sie ein Tool eines Drittanbieters verwenden, um die VHD für höheren Durchsatz zu vergrößern. Informationen zum Vergrößern der VHD finden Sie in dieser [Forumsdiskussion](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### Nachteile

- Es entsteht ein vorübergehender Verlust der Hochverfügbarkeit und von Notfallwiederherstellungsfunktionen während der Migration.
- Da es sich um eine 1:1-Migration handelt, müssen Sie die minimale Größe für den virtuellen Computer verwenden, die die Anzahl von VHDs unterstützt. Dadurch sind Sie möglicherweise nicht in der Lage, Ihre virtuellen Computer zu verkleinern.
- Dieses Szenario wird mithilfe des asynchronen Azure-Cmdlets **Start-AzureStorageBlobCopy** ausgeführt. Es gibt nach Abschluss der Kopie keine SLA. Die Dauer der Kopiervorgänge hängt von der Wartezeit in der Warteschlange sowie der Menge der zu übertragenden Daten ab. Die Zeit für den Kopiervorgang nimmt zu, wenn die Übertragung an ein anderes Azure-Rechenzentrum erfolgt, das Premium-Speicher in einer anderen Region unterstützt. Wenn Sie lediglich über zwei Knoten verfügen, sollten Sie eine Umgehung für den Fall planen, dass das Kopieren länger dauert als in den Tests. Dies könnte die folgenden Umgehungen umfassen.
	- Fügen Sie einen temporären dritten SQL Server-Knoten für hohe Verfügbarkeit vor der Migration mit erwarteter Ausfallzeit hinzu.
	- Führen Sie die Migration außerhalb von geplanten Azure-Wartungen aus.
	- Stellen Sie sicher, dass Sie das Clusterquorum ordnungsgemäß konfiguriert haben.  

##### Schritte auf oberer Ebene:

In diesem Dokument ist kein vollständiges End-to-End-Beispiel dargestellt. Im [Anhang](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) finden Sie jedoch weitere Informationen zur Durchführung eines solchen Beispiels.

![MinimalDowntime][8]

- Erfassen Sie die Datenträgerkonfiguration, und entfernen Sie den Knoten. (Löschen Sie keine angefügten VHDs.)
- Erstellen Sie ein Premium-Speicherkonto und kopieren Sie VHDs aus dem Standard-Speicherkonto.
- Erstellen Sie einen neuen Clouddienst, und stellen Sie die SQL2-VM in diesem Clouddienst erneut bereit. Erstellen Sie den virtuellen Computer mit der kopierten ursprünglichen Betriebssystem-VHD, und fügen Sie die kopierten virtuellen Festplatten an.
- Konfigurieren Sie die ILB/ELB, und fügen Sie Endpunkte hinzu.
- Aktualisieren Sie den Listener mit einem der folgenden Verfahren:
	- Offlineschalten der Gruppe "AlwaysOn" und aktualisieren des AlwaysOn-Listeners mit der neuen ILB/ELB-IP-Adresse.
	- Oder Hinzufügen der IP-Adressressource des neuen Clouddienst-ILB/ELB mithilfe von PowerShell in Windows-Clustern. Festlegen der möglichen Besitzer der IP-Adressressource auf den migrierten Knoten, SQL2, und Festlegen als OR-Abhängigkeit im Netzwerknamen. Weitere Informationen finden Sie im Abschnitt "Hinzufügen einer IP-Adressressource im gleichen Subnetz" im [Anhang](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
- Überprüfen Sie die DNS-Konfiguration/-Weitergabe an die Clients.
- Migrieren Sie die SQL1-VM, und führen Sie die Schritte 2 bis 4 durch.
- Wenn Sie die Schritte 5ii verwenden, fügen Sie SQL1 als möglichen Besitzer für die hinzugefügte IP-Adressressource hinzu.
- Testen Sie die Failover.

#### 2\. Verwenden von vorhandenen sekundären Replikaten: Mehrere Standorte

Wenn Sie über Knoten in mehreren Azure-Datencentern (DC) oder über eine Hybridumgebung verfügen, können Sie eine AlwaysOn-Konfiguration in dieser Umgebung nutzen, um Ausfallzeiten zu minimieren.

Der Ansatz dabei ist, die AlwaysOn-Synchronisierung für das lokale oder sekundäre Azure-DC in synchron zu ändern und dann ein Failover zu diesem SQL Server auszuführen. Anschließend kopieren Sie die VHDs in ein Premium-Speicherkonto und stellen den Computer in einem neuen Clouddienst bereit. Aktualisieren Sie den Listener, und führen Sie ein Failback durch.

##### Zeitpunkte von Ausfallzeiten:

Die Ausfallzeit besteht in der Zeit für das Failover zum alternativen DC und zurück. Sie hängt jedoch auch von Ihrer Client-/DNS-Konfiguration ab, und die erneute Clientverbindung kann verzögert werden. Betrachten Sie das folgende Beispiel einer Hybrid-AlwaysOn-Konfiguration:

![MultiSite1][9]

##### Vorteile

- Sie können die vorhandene Infrastruktur nutzen.
- Sie haben die Möglichkeit, im Voraus zuerst ein Upgrade des Azure-Speichers im DR-Azure-DC auszuführen.
- Der DR-Azure-DC-Speicher kann neu konfiguriert werden.
- Es gibt abgesehen von Testfailovern mindestens zwei Failover während der Migration .
- Sie müssen keine SQL Server-Daten per Sicherung und Wiederherstellung verschieben.

##### Nachteile

- Je nach Clientzugriff auf SQL Server liegt möglicherweise eine erhöhte Latenz vor, wenn SQL Server in einem anderen DC als die Anwendung ausgeführt wird.
- Die Zeit für das Kopieren der virtuellen Festplatten in den Premium-Speicher kann sehr lang sein. Dies kann Ihre Entscheidung beeinträchtigen, ob der Knoten in der Verfügbarkeitsgruppe bleiben soll oder nicht. Bedenken Sie dies bei der Ausführung stark protokollierter, erforderlicher Workloads während der Migration, da der primäre Knoten die nicht replizierten Transaktionen im Transaktionsprotokoll speichern muss. Aus diesem Grund könnte dieses Protokoll erheblich anwachsen.
- Dieses Szenario wird mithilfe des asynchronen Azure-Cmdlets **Start-AzureStorageBlobCopy** ausgeführt. Es gibt nach dem Abschluss keine SLA. Die Dauer der Kopiervorgänge hängt von der Wartezeit in der Warteschlange sowie der Menge der zu übertragenden Daten ab. Wenn Sie nur über einen Knoten im zweiten Rechenzentrum verfügen, sollten Sie daher Abhilfeschritte für den Fall planen, dass das Kopieren länger als beim Testen dauert. Dies könnte die folgenden Umgehungen umfassen.
	- Fügen Sie einen temporären zweiten SQL Server-Knoten für hohe Verfügbarkeit vor der Migration mit erwarteter Ausfallzeit hinzu.
	- Führen Sie die Migration außerhalb von geplanten Azure-Wartungen aus.
	- Stellen Sie sicher, dass Sie das Clusterquorum ordnungsgemäß konfiguriert haben.

Bei diesem Szenario wird davon ausgegangen, dass Sie Ihre Installation dokumentiert haben und wissen, wie der Speicher zugeordnet wurde, um Änderungen in Bezug auf die optimalen Cacheeinstellungen vorzunehmen.

##### Schritte auf oberer Ebene:
![Multisite2][10]

- Machen Sie das lokale/alternative Azure-DC zum primären DC für SQL Server und zum Partner für automatische Failover (AFP).
- Erfassen Sie die Datenträgerkonfiguration von SQL2, und entfernen Sie den Knoten. (Löschen Sie keine angefügten VHDs.)
- Erstellen Sie ein Premium-Speicherkonto, und kopieren Sie VHDs aus dem Standard-Speicherkonto.
- Erstellen Sie einen neuen Clouddienst und den SQL2-VM mit angefügten Premium-Speicherdatenträgern.
- Konfigurieren Sie die ILB/ELB, und fügen Sie Endpunkte hinzu.
- Aktualisieren Sie die AlwaysOn-Listener mit der neuen ILB/ELB-IP-Adresse, und testen Sie Failover.
- Überprüfen Sie die DNS-Konfiguration.
- Ändern Sie die AFP in SQL2, migrieren Sie SQL1, und führen Sie die Schritte 2 bis 5 durch.
- Testen Sie die Failover.
- Ändern Sie den AFP von SQL1 zurück zu SQL2.

## Anhang: Migrieren eines AlwaysOn-Clusters mit mehreren Standorten zu Premium-Speicher

Im weiteren Verlauf dieses Themas finden Sie ein ausführliches Beispiel zum Konvertieren eines AlwaysOn-Clusters mit mehreren Standorten in Premium-Speicher. Weiterhin wird der Listener so konvertiert, das er statt eines externen Lastenausgleichs (ELB) einen internen Lastenausgleich (ILB) verwendet.

### Environment

- Windows 2k12/SQL 2k12
- 1 Datenbankdatei auf SP
- 2 Speicherpools pro Knoten

![Anhang1][11]

### VM:

In diesem Beispiel wird die Umstellung von einem ELB zu einem ILB veranschaulicht. ELB war vor ILB verfügbar. Hier wird demnach dargestellt, wie während der Migration gewechselt wird.

![Anhang2][12]

### Vorbereitungsschritte: Verbinden mit dem Abonnement

    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### Schritt 1: Erstellen eines neues Speicherkontos und Clouddiensts
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### Schritt 2: Erhöhen der zulässigen Fehler für Ressourcen <Optional>
Für bestimmte Ressourcen, die der AlwaysOn-Verfügbarkeitsgruppe angehören, bestehen Einschränkungen in Bezug darauf, wie viele Fehler in einem bestimmten Zeitraum auftreten können, in dem der Clusterdienst versucht, die Ressourcengruppe neu zu starten. Es wird empfohlen, dass Sie diesen Wert erhöhen, während Sie dieses Verfahren durchführen, da Sie dieser Grenze nahe kommen können, wenn Sie keine manuellen Failover ausführen oder Failover auslösen, indem Sie Computer herunterfahren.

Es ist ratsam, diesen Grenzwert zu verdoppeln. Wechseln Sie dazu im Failovercluster-Manager zu den Eigenschaften der AlwaysOn-Ressourcengruppe:

![Anhang3][13]

Ändern Sie den Wert für die maximale Fehleranzahl in 6.

#### Schritt 3: Hinzufügen einer IP-Adressressource zur Clustergruppe <Optional>

Wenn Sie nur eine IP-Adresse für die Clustergruppe haben und diese am Cloudsubnetz ausgerichtet ist, denken Sie daran, dass die Cluster-IP-Adressressource und der Netzwerkname nicht online geschaltet werden können, wenn Sie versehentlich alle Clusterknoten in der Cloud in diesem Netzwerk offline schalten. Wenn dieser Fall auftritt, werden Aktualisierungen der anderen Clusterressourcen verhindert.

#### Schritt 4: DNS-Konfiguration

Der problemlose Übergang hängt von der Verwendung und Aktualisierung von DNS ab. Wenn AlwaysOn installiert ist, erstellt es eine Windows-Cluster-Ressourcengruppe. Wenn Sie den Failovercluster-Manager öffnen, werden Sie feststellen, dass mindestens drei Ressourcen enthalten sind. In diesem Dokument wird auf die folgenden beiden Ressourcen verwiesen:

- Name des virtuellen Netzwerks (VNN) – Dies ist der DNS-Name, mit dem der Client bei der Verbindung mit SQL Server über AlwaysOn eine Verbindung herstellt.
- IP-Adressressource – Dies ist die IP-Adresse, die mit dem VNN verknüpft ist. Es können mehrere vorhanden sein und in einer Konfiguration für mehrere Standorte gibt es eine IP-Adresse pro Standort/Subnetz.

Beim Herstellen einer Verbindung mit SQL Server ruft der SQL Server-Clienttreiber die mit dem Listener verbundenen DNS-Datensätze ab und versucht, eine Verbindung mit jeder IP-Adresse, die mit AlwaysOn verknüpft ist, herzustellen. Im Folgenden werden einige Faktoren besprochen, die dies beeinflussen können.

Die Anzahl der gleichzeitigen DNS-Datensätze, die dem Listenernamen zugeordnet sind, hängt nicht nur von der Anzahl der zugeordneten IP-Adressen ab, sondern von der Einstellung "RegisterAllIpProviders" im Failovercluster für die AlwaysON-VNN-Ressource.

Beim Bereitstellen von AlwaysOn in Azure gibt es verschiedene Schritte zum Erstellen des Listeners und der IP-Adressen. Sie müssen "RegisterAllIpProviders" manuell auf 1 festlegen. Dies unterscheidet sich von einer lokalen AlwaysOn-Bereitstellung, bei der dieser Wert immer auf 1 festgelegt ist.

Wenn "RegisterAllIpProviders" auf 0 festgelegt ist, sehen Sie nur einen DNS-Eintrag im dem Listener zugeordneten DNS:

![Anhang4][14]

Wenn "RegisterAllIpProviders" auf 1 festgelegt ist:

![Anhang5][15]

Im folgenden Code werden die VNN-Einstellungen ausgegeben und festgelegt. Beachten Sie bitte, dass Sie zum Übernehmen der Änderungen den VNN offline und anschließend wieder online schalten müssen. Das Offlineschalten des Listeners verursacht eine Unterbrechung der Clientverbindungen.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

In einem späteren Migrationsschritt müssen Sie den AlwaysOn-Listener mit einer aktualisierten IP-Adresse aktualisieren, die auf einen Lastenausgleich verweist. Dabei müssen Sie eine IP-Adressressource entfernen und wieder hinzufügen. Nach der IP-Aktualisierung müssen Sie sicherstellen, dass die neue IP-Adresse in der DNS-Zone aktualisiert wurde und dass die Clients ihren lokalen DNS-Cache aktualisieren.

Wenn sich die Clients in einem anderen Netzwerksegment befinden und auf einen anderen DNS-Server verweisen, müssen Sie bedenken, was mit der DNS-Zonenübertragung während der Migration geschieht, da die Zeit für die erneute Verbindung der Anwendung mindestens durch die Zonenübertragungszeit der neuen IP-Adressen des Listeners eingeschränkt wird. Wenn Sie bei diesem Schritt unter Zeitdruck stehen, sollten Sie in Erwägung ziehen, eine inkrementelle Zonenübertragung für Ihre Windows-Teams zu erzwingen. Weiterhin sollten Sie den DNS-Hosteintrag auf eine niedrigere TTL (Time To Live, Gültigkeitsdauer) einstellen, sodass die Clients eine Aktualisierung durchführen. Weitere Informationen finden Sie unter [Incremental Zone Transfers](https://technet.microsoft.com/library/cc958973.aspx) (Inkrementelle Zonenübertragungen, in englischer Sprache) und [Start-DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx) (in englischer Sprache).

Standardmäßig ist die TTL für den DNS-Datensatz, der dem Listener in AlwaysOn in Azure zugeordnet ist, auf 1.200 Sekunden festgelegt. Diesen Wert sollten Sie möglicherweise reduzieren, wenn Sie sich bei der Migration unter Zeitdruck befinden, um sicherzustellen, dass die Clients ihr DNS mit der aktualisierten IP-Adresse für den Listener aktualisieren. Sie können die Konfiguration über eine Ausgabe der VNN-Konfiguration anzeigen und ändern:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Beachten Sie, dass mit einem niedrigeren "HostRecordTTL"-Wert der DNS-Datenverkehr zunimmt.

##### Clientanwendungseinstellungen

Wenn die SQL-Clientanwendung den .Net 4.5 SQLClient unterstützt, können Sie das Schlüsselwort "MULTISUBNETFAILOVER = TRUE" verwenden. Dies wird empfohlen, da es bei einem Failover eine schnellere Verbindung mit der SQL-AlwaysOn-Verfügbarkeitsgruppe ermöglicht. Dabei werden alle mit dem AlwaysOn-Listener verbundenen IP-Adressen parallel durchlaufen und eine aggressivere Geschwindigkeit bei der TCP-Verbindungswiederholung während eines Failovers angewendet.

Weitere Informationen zu den obigen Einstellungen finden Sie unter [Erstellen oder Konfigurieren eines Verfügbarkeitsgruppenlisteners (SQL Server)](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Siehe auch [SqlClient Support for High Availability, Disaster Recovery](https://msdn.microsoft.com/library/hh205662(v=vs.110).aspx) (in englischer Sprache).

#### Schritt 5: Clusterquorum-Einstellungen

Da Sie mindestens einen SQL Server offline schalten, sollten Sie die Clusterquorum-Einstellung ändern. Wenn Sie FSW (File Share Witnes) mit zwei Knoten verwenden, sollten Sie die Quorum-Einstellung so festlegen, dass Knotenmehrheit zugelassen und dynamische Abstimmungen verwendet werden. Dies ermöglicht das Aufrechterhalten eines einzelnen Knotens.


    Set-ClusterQuorum -NodeMajority  

Weitere Informationen zur Verwaltung und zum Konfigurieren des Clusterquorums finden Sie unter [Konfigurieren und Verwalten des Quorums in einem Windows Server 2012-Failovercluster](https://technet.microsoft.com/library/jj612870.aspx).

#### Schritt 6: Extrahieren vorhandener Endpunkte und ACLs
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the AlwaysOn Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Speichern Sie diese in einer Textdatei.

#### Schritt 7: Ändern von Failoverpartnern und Replikationsmodi

Wenn Sie über mehr als zwei SQL Server verfügen, sollten Sie die Failovereinstellung für ein anderes sekundäres Replikat in einem anderen Domänencontroller oder lokal in "Synchron" ändern und es zu einem automatischen Failoverpartner (AFP) machen. Dadurch erhalten Sie hohe Verfügbarkeit, während Sie Änderungen vornehmen. Verwenden Sie dazu TSQL oder Änderungen über SSMS:

![Anhang6][16]

#### Schritt 8: Entfernen eines sekundären virtuellen Computers aus dem Clouddienst

Sie sollten zuerst die Migration eines sekundären Cloudknotens planen. Wenn es sich um den derzeit primären Knoten handelt, sollten Sie ein manuelles Failover initiieren.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### Schritt 9: Ändern und Speichern der Datenträgercache-Einstellungen in einer CSV-Datei

Für Datenvolumes sollte dies auf "READONLY" festgelegt sein.

Für TLOG-Volumes sollte dies auf "NONE" festgelegt sein.

![Anhang7][17]

#### Schritt 10: Kopieren virtueller Festplatten
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Sie können den Status des Kopiervorgangs der virtuellen Festplatten in das Premium-Speicherkonto überprüfen:

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Anhang8][18]

Warten Sie, bis diese als erfolgreich aufgezeichnet wurden.

Informationen zu einzelnen Blobs:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### Schritt 11: Registrieren des BS-Datenträgers

    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### Schritt 12: Importieren von sekundären Replikaten in den neuen Clouddienst

Der folgende Code verwendet außerdem die hinzugefügte Option. Sie können hier den Computer importieren und die beibehaltene VIP-Adresse verwenden.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### Schritt 13: Erstellen des ILB im neuen Clouddienst, Hinzufügen von Lastenausgleichsendpunkten und ACLs
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

####Schritt 14: Aktualisieren von AlwaysOn
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Anhang9][19]

Entfernen Sie jetzt die IP-Adresse des alten Clouddiensts.

![Anhang10][20]

#### Schritt 15: Überprüfen auf DNS-Aktualisierungen

Sie sollten jetzt die DNS-Server in den SQL Server-Clientnetzwerken überprüfen und sicherstellen, dass der zusätzlichen Hosteintrag für die hinzugefügte IP-Adresse durch das Clustering hinzugefügt hat. Wenn diese DNS-Server nicht aktualisiert wurden, sollten Sie eine DNS-Zonenübertragung erzwingen und sicherstellen, dass die Clients in ihren Subnetzen in AlwaysOn-IP-Adressen aufgelöst werden können. Dadurch müssen Sie nicht warten, bis die automatische DNS-Replikation durchgeführt wurde.

#### Schritt 16: Neukonfigurieren von AlwaysOn

An diesem Punkt warten Sie auf das sekundäre Replikat des Knotens, der zur vollständigen Neusynchronisierung migriert wurde, wechseln zum synchronen Replikationsmodus und machen dieses zum AFP.

#### Schritt 17: Migrieren des zweiten Knotens
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### Schritt 18: Ändern und Speichern der Datenträgercache-Einstellungen in einer CSV-Datei

Für Datenvolumes sollte dies auf "READONLY" festgelegt sein.

Für TLOG-Volumes sollte dies auf "NONE" festgelegt sein.

![Anhang11][21]

#### Schritt 19: Erstellen eines neuen unabhängigen Speicherkontos für den sekundären Knoten
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### Schritt 20: Kopieren virtueller Festplatten
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
	    -SrcContext $origContext `
	    -DestContainer $containerName `
	    -DestBlob $vhdname `
	    -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Sie können den Status des VHD-Kopiervorgangs für alle virtuellen Festplatten überprüfen: ForEach ($disk in $diskobjects) { $lun = $disk.Lun $vhdname = $disk.vhdname $cacheoption = $disk.HostCaching $disklabel = $disk.DiskLabel $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Anhang12][22]

Warten Sie, bis diese als erfolgreich aufgezeichnet wurden.

Informationen zu einzelnen Blobs: #Check individual blob status Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### Schritt 21: Registrieren des BS-Datenträgers
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### Schritt 22: Hinzufügen von Lastenausgleichs-Endpunkten und ACLs
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure classic portal or Machine Endpoints through powershell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### Schritt 23: Failovertest

Lassen Sie jetzt den migrierten Knoten mit dem lokalen AlwaysOn-Knoten synchronisieren. Versetzen Sie ihn in den Modus für synchrone Replikation, und warten Sie, bis die Synchronisierung abgeschlossen ist. Führen Sie anschließend ein Failover vom lokalen zum ersten migrierten Knoten aus, bei dem es sich um den AFP handelt. Nach Abschluss dieses Vorgangs machen Sie den letzten migrierten Knoten zum AFP.

Sie sollten zwischen allen Knoten Failover- und Chaostests durchführen, um sicherzustellen, dass die Failover ordnungsgemäß funktionieren und in angemessener Zeit durchgeführt werden.

#### Schritt 24: Implementieren der Clusterquorum-Einstellungen/DNS-TTL/Failoverpartner/Synchronisierungseinstellungen
##### Hinzufügen einer IP-Adressressource im gleichen Subnetz

Wenn Sie nur über zwei SQL Server verfügen und zu einem neuen Clouddienst migrieren möchten, die Server jedoch im gleichen Subnetz behalten möchten, können Sie das Offlineschalten des Listeners vermeiden, um die ursprüngliche AlwaysOn-IP-Adresse zu löschen und die neue IP-Adresse hinzuzufügen. Wenn Sie die virtuellen Computer in ein anderes Subnetz migrieren, ist dies nicht notwendig, da es ein zusätzliches Clusternetzwerk mit Verweis auf das Subnetz gibt.

Nachdem Sie die migrierten sekundären Replikate online geschaltet und die neue IP-Adressressource für den neuen Clouddienst hinzugefügt haben, sollten Sie vor dem Failover des vorhandenen primären Servers diese Schritte innerhalb des Clusterfailover-Managers ausführen:

Informationen zum Hinzufügen einer IP-Adresse finden Sie im [Anhang](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) in Schritt 14.

1. Ändern Sie für die aktuelle IP-Adressressource den möglichen Besitzer in "Existing Primary SQL Server" – im Beispiel unten "dansqlams4":

	![Anhang13][23]

1. Ändern Sie für die neue IP-Adressressource den möglichen Besitzer in "Migrated secondary SQL Server" – im Beispiel unten "dansqlams5":

	![Anhang14][24]

1. Wenn dies festgelegt ist, können Sie das Failover ausführen, und nach der Migration des letzten Knotens sollten die möglichen Besitzer bearbeitet werden, damit dieser Knoten als möglicher Besitzer hinzugefügt wird:

	![Anhang15][25]

## Zusätzliche Ressourcen
- [Azure Premium-Speicher](../storage-premium-storage-preview-portal.md)
- [Virtuelle Computer](https://azure.microsoft.com/services/virtual-machines/)
- [SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-sql-server-use-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-sql-server-use-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-sql-server-use-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-sql-server-use-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-sql-server-use-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-sql-server-use-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-sql-server-use-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-sql-server-use-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-sql-server-use-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-sql-server-use-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_15.png

<!------HONumber=AcomDC_0128_2016-->