<properties 
    pageTitle="Migrieren zu Azure Premium-Speicher | Microsoft Azure" 
    description="Migrieren Sie zu Azure Premium-Speicher für leistungsstarke Datenträgerunterstützung mit niedriger Latenz von E/A-intensiven Workloads in Azure Virtual Machines." 
    services="storage" 
    documentationCenter="na" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/06/2015" 
    ms.author="tamram"/>


# Migrieren zu Azure Premium-Speicher

## Übersicht

Azure Premium-Speicher bietet leistungsstarke Solid State-Laufwerkstechnologie \(SSD\) mit niedriger Latenz für Workloads mit hoher E/A, die auf virtuellen Azure-Computern ausgeführt werden. Mit dem Premium-Speicher können Ihre Anwendungen bis zu 32 TB Speicher pro virtuellem Computer nutzen und 50.000 IOPS \(Input/Output Operations Per Second, E/A-Vorgänge pro Sekunde\) pro virtuellem Computer mit äußerst niedriger Latenz für Lesevorgänge erzielen. In diesem Artikel finden Sie Anleitungen zum Migrieren von Festplatten oder virtuellen Computern \(VMs\) von lokalen oder Standardspeichern bzw. anderen Cloud-Plattformen zu Premium-Azure-Speicher. Eine ausführliche Übersicht über Azure Premium-Speicher finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Workloads in Azure Virtual Machine](storage-premium-storage-preview-portal.md).

## Voraussetzungen 

### Voraussetzungen
- Sie benötigen ein Azure-Abonnement. Wenn Sie kein Abonnement besitzen, können Sie für einen Monat eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) abonnieren, oder Sie besuchen die Seite mit den [Azure-Preisen](http://azure.microsoft.com/pricing/) für weitere Optionen. 
- Zum Ausführen von PowerShell-Cmdlets benötigen Sie das Microsoft Azure PowerShell-Modul. Informationen zum Herunterladen dieses Moduls finden Sie unter [Microsoft Azure-Downloads](http://azure.microsoft.com/downloads/).
- Wenn Sie Azure-VMs mit Premium-Speicher nutzen möchten, müssen Sie die VMs der DS-Serie verwenden. Mit VMs der DS-Serie können Sie Standard- und Premium-Speicherdatenträger verwenden. Premium-Datenträger werden zukünftig mit mehreren VM-Typen verfügbar sein. Weitere Informationen zu den verfügbaren Typen und Größen von Azure-VM-Datenträgern finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 

### Überlegungen 

#### VM-Größen 
Im Folgenden finden Sie die VM-Größen der DS-Serie und deren Merkmale zusammengefasst. Sehen Sie sich die Leistungsmerkmale dieser Angebote für Premium-Speicher an, und wählen Sie die am besten für Ihre Workload geeignete Option für Ihre Azure-Datenträger und virtuellen Computer aus. Stellen Sie sicher, dass auf Ihrem virtuellen Computer ausreichend Bandbreite zum Steuern des Datenverkehrs des Datenträgers verfügbar ist.

|Größe des virtuellen Computers|CPU-Kerne|Max. IOPS|Max. Bandbreite des Datenträgers|
|:---:|:---:|:---:|:---:|
|**STANDARD\_DS1**|1|3\.200|32 MB pro Sekunde|
|**STANDARD\_DS2**|2|6\.400|64 MB pro Sekunde|
|**STANDARD\_DS3**|4|12\.800|128 MB pro Sekunde|
|**STANDARD\_DS4**|8|25\.600|256 MB pro Sekunde|
|**STANDARD\_DS11**|2|6\.400|64 MB pro Sekunde|
|**STANDARD\_DS12**|4|12\.800|128 MB pro Sekunde|
|**STANDARD\_DS13**|8|25\.600|256 MB pro Sekunde|
|**STANDARD\_DS14**|16|50\.000|512 MB pro Sekunde|

#### Datenträgergrößen 
Es gibt drei Datenträgertypen, die Sie mit Ihrem virtuellen Computer verwenden können, und jeder Typ weist bestimmte IOPs und Einschränkungen auf. Berücksichtigen Sie diese Beschränkungen beim Auswählen des Datenträgertyps für Ihren virtuellen Computer je nach Anforderungen Ihrer Anwendung hinsichtlich Kapazität, Leistung, Skalierbarkeit und Spitzenlasten.

|Datenträgertyp des Premium-Speichers|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Datenträgergröße|128 GB|512 GB|1024 GB \(1 TB\)|
|IOPS pro Datenträger|500|2\.300|5\.000|
|Durchsatz pro Datenträger|100 MB pro Sekunde|150 MB pro Sekunde|200 MB pro Sekunde|

#### Skalierbarkeitsziele für das Speicherkonto

Premium-Speicherkonten haben zusätzlich zu den unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/azure/dn249410.aspx) aufgeführten Zielen folgende Skalierbarkeitsziele. Wenn die Anforderungen Ihrer Anwendung die Skalierbarkeitsziele eines einzelnen Speicherkontos überschreiten, erstellen Sie die Anwendung so, dass mehrere Speicherkonten verwendet werden, und partitionieren Sie Ihre Daten in diesen Speicherkonten.

|Gesamtkapazität des Kontos|Gesamtbandbreite für ein lokal redundantes Speicherkonto|
|:--|:---|
|Festplattenkapazität: 35 TB<br />Kapazität für Momentaufnahmen: 10 TB|Bis zu 50 GB pro Sekunde für eingehenden und ausgehenden Datenverkehr|

Weitere Informationen zu den Spezifikationen für Premium-Speicher finden Sie unter [Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-when-using-premium-storage).

#### Zusätzliche Datenträger 
Legen Sie je nach Workload fest, ob zusätzliche Datenträger für Ihren virtuellen Computer erforderlich sind. Sie können mehrere Datenträger für permanente Daten auf Ihrem virtuellen Computer anfügen. Bei Bedarf können Sie Daten über die Datenträger verteilen, um die Kapazität und die Leistung des Volumens zu erhöhen. Wenn Sie Daten über Premium-Speicher-Datenträger mithilfe von [Speicherplätzen](http://technet.microsoft.com/library/hh831739.aspx) verteilen, sollten Sie sie für jeden verwendeten Datenträger eine Spalte konfigurieren. Andernfalls kann die Gesamtleistung des Stripesetvolume aufgrund ungleicher Verteilung des Datenverkehrs auf die Datenträger niedriger sein als erwartet. Für Linux-VMs können Sie dazu das Dienstprogramm *mdadm* verwenden. Weitere Informationen finden Sie im Artikel [Konfigurieren von Software-RAID unter Linux](../virtual-machines-linux-configure-raid.md).

#### Zwischenspeicherungsrichtlinie für Datenträger 
Standardmäßig ist die Richtlinie für das Zwischenspeichern für alle Premium-Datenträger *Schreibgeschützt* und für die Premium-Betriebssystem-Datenträger, die an den virtuellen Computer angeschlossen sind, *Lesen/Schreiben*. Diese Konfigurationseinstellung wird empfohlen, um die optimale E/A-Leistung für Ihre Anwendung zu erreichen. Für Datenträger mit hohem oder ausschließlichem Schreibzugriff \(z. B. SQL Server-Protokolldateien\) deaktivieren Sie das Zwischenspeichern, sodass Sie eine bessere Anwendungsleistung erzielen können. Die Einstellungen für das Zwischenspeichern bei vorhandenen Datenträgern können Sie über das Azure-Portal oder den Parameter *-HostCaching* des Cmdlets *Set-AzureDataDisk* aktualisieren.

#### Standort 
Wählen Sie einen Speicherort, an dem Azure Premium-Speicher verfügbar ist. Aktuelle Informationen zu Speicherorten finden Sie unter [Wichtige Informationen zum Premium-Speicher](storage-premium-storage-preview-portal.md#important-things-to-know-about-premium-storage). Virtuelle Computer in der gleichen Umgebung wie das Speicherkonto, in dem die Datenträger für den virtuellen Computer gespeichert sind, erzielen eine höhere Leistung als solche in unterschiedlichen Regionen.

#### Sonstige Azure-VM-Konfigurationseinstellungen 

Beim Erstellen einer Azure-VM müssen Sie bestimmte Einstellungen für die virtuellen Computer konfigurieren. Denken Sie daran, dass es einige Einstellungen gibt, die für die Lebensdauer des virtuellen Computers festgelegt sind, während Sie andere Einstellungen später hinzufügen oder ändern können. Überprüfen Sie diese Azure-VM-Konfigurationseinstellungen, und stellen Sie sicher, dass diese korrekt konfiguriert sind, um die Anforderungen für die Workload zu erfüllen. Weitere Informationen finden Sie unter [Informationen zu Azure-VM-Konfigurationseinstellungen](https://msdn.microsoft.com/library/azure/dn763935.aspx).

## Vorbereiten von virtuellen Festplatten für die Migration 

Der folgende Abschnitt enthält Richtlinien zur Vorbereitung der Migration der virtuellen Festplatten des virtuellen Computers. Bei der virtuellen Festplatte kann es sich um Folgendes handeln:
 
- Ein allgemeines Betriebssystemimage, das verwendet werden kann, um mehrere Azure-VMs zu erstellen  
- Einen Betriebssystem-Datenträger, der mit einer einzelnen virtuellen Azure-Computer-Instanz verwendet werden kann  
- Einen Datenträger, der mit einem virtuellen Azure-Computer zur dauerhaften Speicherung verbunden werden kann.

### Voraussetzungen

Zum Migrieren Ihrer virtuellen Computer benötigen Sie Folgendes:

- Ein Azure-Abonnement, ein Speicherkonto und ein Container in diesem Speicherkonto, in den die virtuelle Festplatte kopiert wird. Beachten Sie, dass das Zielspeicherkonto je nach Ihren Anforderung ein Standard- oder Premium-Speicherkonto sein kann. 
- Ein Tool zum Generalisieren der VHD, wenn Sie mehrere virtuelle Computerinstanzen daraus erstellen möchten. Beispiel: Sysprep für Windows oder virt-sysprep für Ubuntu. 
- Ein Tool für den Upload der VHD-Datei in das Speicherkonto. Beispielsweise [AzCopy](storage-use-azcopy.md) oder der [Azure-Speicher-Explorer](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). In dieser Anleitung wird erläutert, wie Sie Ihre VHD mit dem Tool AzCopy kopieren. 

> [AZURE.NOTE]Kopieren Sie die VHD im Hinblick auf eine optimale Leistung, indem Sie eines dieser Tools auf einem virtuellen Azure-Computer ausführen, der sich in der gleichen Region wie das Zielspeicherkonto befindet. Wenn Sie eine VDH von einem virtuellen Azure-Computer in einer anderen Region kopieren, kann dies die Leistung beeinträchtigen.
>
> Beim Kopieren großer Datenmengen bei begrenzter Bandbreite können Sie den [Microsoft Azure-Import/Export-Dienst](storage-import-export-service.md) nutzen und für die Datenübertragung Festplatten an ein Azure-Rechenzentrum senden. Mit dem Azure Import/Export-Dienst können Sie Daten nur in ein Standardspeicherkonto kopieren. Wenn die Daten sich im Standardspeicherkonto befinden, können Sie sie mit der [Copy Blob-API](https://msdn.microsoft.com/library/azure/dd894037.aspx) oder mit AzCopy in Ihr Premium-Speicherkonto übertragen.
> 
> Beachten Sie, dass Microsoft Azure nur VHD-Dateien mit fester Größe unterstützt. VHDX-Dateien und dynamische virtuelle Festplatten werden nicht unterstützt. Wenn Sie über eine dynamische virtuelle Festplatte verfügen, können Sie sie mit dem Cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) in eine VHD mit fester Größe konvertieren .

### Szenarios für die Vorbereitung von virtuellen Festplatten

Im Folgenden werden einige Szenarios für die Vorbereitung Ihrer virtuellen Festplatte erörtert.

#### Generalisierte Betriebssystem-VHD zum Erstellen von mehreren VM-Instanzen

Denken Sie beim Hochladen einer VHD-Datei, mit der mehrere generische Azure-VM-Instanzen erstellt werden, daran, dass Sie die VHD zunächst mit einem Dienstprogramm zur Systemvorbereitung \(Sysprep\) generalisieren müssen. Dies gilt für eine lokale virtuelle Festplatte ebenso wie für Cloud-Festplatten. Mit Sysprep werden alle computerspezifischen Informationen von der virtuellen Festplatte entfernt.

>[AZURE.IMPORTANT]Erstellen Sie eine Momentaufnahme oder eine Sicherung Ihres virtuellen Computers, bevor Sie ihn generalisieren. Beim Ausführen von Sysprep wird die VM-Instanz gelöscht. Führen Sie die Schritte unten zur Vorbereitung einer VHD mit Windows-Betriebssystem aus. Beachten Sie, dass beim Ausführen des Sysprep-Befehls der virtuelle Computer heruntergefahren werden muss. Weitere Informationen zu Sysprep finden Sie unter [Sysprep \(Systemvorbereitung\) – Übersicht](http://technet.microsoft.com/library/hh825209.aspx) oder unter \[Technische Referenz zu Sysprep\] \(http://technet.microsoft.com/library/cc766049(v=ws.10).aspx).

1. Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator.
2. Geben Sie den folgenden Befehl zum Öffnen von Sysprep ein:
 
		%windir%\system32\sysprep\sysprep.exe

4. Wählen Sie im Systemvorbereitungsprogramm die Option "Out-of-Box-Experience \(OOBE\) für System aktivieren" aus, aktivieren Sie das Kontrollkästchen "Verallgemeinern", wählen Sie **Herunterfahren** aus, und klicken Sie dann auf **OK** \(siehe dazu Abbildung unten\). Dadurch werden das Betriebssystem generalisiert und das System heruntergefahren.

	![][1]

Verwenden Sie für einen virtuellen Ubuntu-Computer "virt-sysprep". Weitere Informationen finden Sie unter [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) \(in englischer Sprache\). Für diese Vorgänge steht auch [Open-Source-Software zur Linux-Serverbereitstellung](http://www.cyberciti.biz/tips/server-provisioning-software.html) für andere Linux-Betriebssysteme zur Verfügung.

#### Eindeutige Betriebssystem-VHD zum Erstellen einer einzelnen VM-Instanz

Wenn auf dem virtuellen Computer eine Anwendung ausgeführt wird, die die spezifischen Daten des Computers benötigt, generalisieren Sie die virtuelle Festplatte nicht. Eine nicht generalisierte virtuelle Festplatte kann verwendet werden, um eine eindeutige Azure-VM-Instanz zu erstellen. Wenn Sie beispielsweise über einen Domänencontroller für die virtuelle Festplatte verfügen, wird dieser durch die Ausführung von Sysprep außer Kraft gesetzt. Überprüfen Sie die auf dem virtuellen Computer ausgeführten Anwendungen und die Auswirkung von Sysprep auf diese, bevor Sie die VHD verallgemeinern.

#### VM-Instanzen zugeordnete Datenträger-VHDs

Wenn Sie über Datenlaufwerke in einem Cloud-Speicher verfügen, die migriert werden sollen, müssen Sie sicherstellen, dass die virtuellen Computer, die diese Datenträger nutzen, heruntergefahren wurden. Erstellen Sie für lokale Datenträger eine konsistente virtuelle Festplatte.

## Kopieren von VHDs in Azure-Speicher

Nun da die virtuelle Festplatte vorbereitet ist, können Sie die Schritte unten ausführen, um die VHD in Azure Storage hochzuladen und als Betriebssystemimage, bereitgestellten Betriebssystem-Datenträger oder bereitgestellten Datenträger zu registrieren.

### Erstellen des Ziels für die virtuelle Festplatte

Erstellen Sie ein Speicherkonto für die Verwaltung Ihrer virtuellen Festplatten. Berücksichtigen Sie die folgenden Punkte beim Planen des Speicherorts der virtuellen Festplatten:

- Der Speicherort des Zielspeicherkontos kann sich je nach Anwendungsanforderungen in einem Standard- oder Premium-Speicher befinden. 
- Der Speicherort für das Konto muss mit den Azure-VMs der DS-Serie übereinstimmen, die Sie in der letzten Phase erstellen. Sie können je nach Anforderungen in ein neues Speicherkonto kopieren oder die Verwendung desselben Speicherkontos planen.
- Kopieren und speichern Sie den Speicherkontenschlüssel des Zielspeicherkontos für die nächste Phase.
- Für Datenträger können Sie auswählen, dass einige in einem Standardspeicherkonto verbleiben \(z. B. Festplatten mit Daten, auf die weniger zugegriffen wird\) und andere mit hohem IOPS-Wert in einem Premium-Speicherkonto gespeichert werden.

### Kopieren der virtuellen Festplatte aus der Quelle

Im Folgenden sind einige Szenarios zum Kopieren der virtuellen Festplatte beschrieben.

#### Kopieren von VHDs aus Azure Storage

Wenn Sie VHDs aus einem Azure-Standardspeicherkonto zu einem Azure Premium-Speicherkonto migrieren, müssen Sie den Quellpfad des VHD-Containers, den Namen der VHD-Datei und den Speicherkontoschlüssel des Quellspeicherkontos kopieren.

1. Wechseln Sie zu **Azure-Portal \> Virtual Machines \> Datenträger**.
2. Kopieren und speichern Sie die VHD-Container-URL aus der Spalte "Speicherort". Die Container-URL ähnelt dem folgenden Format: `https://myaccount.blob.core.windows.net/mycontainer/`.

#### Kopieren von VHDs aus anderen Clouddiensten

Wenn Sie virtuelle Festplatten aus anderen Cloud-Speichern als Azure zu Azure migrieren möchten, müssen Sie zuerst die VHD-Datei in ein lokales Verzeichnis exportieren. Kopieren Sie den vollständigen Quellpfad des lokalen Verzeichnisses, in dem die VHD-Datei gespeichert ist.

1. Bei Verwendung von AWS exportieren Sie die EC2-Instanz in eine virtuelle Festplatte in einem Amazon S3-Bucket. Befolgen Sie die in der Amazon-Dokumentation unter [Exporting Amazon EC2 Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html) \(in englischer Sprache\) beschriebenen Schritte, um das Befehlszeilenschnittstellen-Tool Amazon EC2 zu installieren, und führen Sie den Befehl zum Exportieren der EC2-Instanz in eine VHD-Datei aus. 
	
	Verwenden Sie **VHD** für die Variable "DISK&\#95;IMAGE&\#95;FORMAT", wenn Sie den Befehl ausführen. Die exportierte VHD-Datei wird im Amazon S3-Bucket gespeichert, der während dieses Prozesses angegeben wurde.

	![][2]

2. Laden Sie die VHD-Datei aus dem S3-Bucket herunter. Wählen Sie die VHD-Datei und dann **Aktionen** \> **Herunterladen** aus.

	![][3]\|

#### Kopieren einer lokalen VHD

Wenn Sie die VHD-Datei aus einer lokalen Umgebung migrieren, benötigen Sie den vollständigen Quellpfad, in dem VHD-Datei gespeichert wurde. Dies kann ein Serverspeicherort oder eine Dateifreigabe sein.

### Kopieren einer VHD mit AzCopy

Mit AzCopy können Sie auf einfache Weise die VHD-Datei über das Internet hochladen. Je nach Größe der virtuellen Festplatten kann dies einige Zeit in Anspruch nehmen. Denken Sie daran, die Eingangs-/Ausgangsgrenzen des Speicherkontos bei Verwendung dieser Option zu überprüfen. Ausführliche Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](storage-scalability-targets.md).

1. Laden Sie AzCopy über den folgendem Link herunter, und installieren Sie es: [Aktuelle Version von AzCopy](http://aka.ms/downloadazcopy).  
2. Öffnen Sie Azure PowerShell, und wechseln Sie zu dem Ordner, in dem AzCopy installiert ist.  
3. Verwenden Sie den folgenden Befehl, um die VHD-Datei von der "Quelle" zum "Ziel" zu kopieren. 

		AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>  

	Es folgt die Beschreibung der im AzCopy-Befehl verwendeten Parameter:

 - **/Source: *&lt;Quelle&gt;:*** Speicherort des Ordners oder URL des Speichercontainers mit der virtuellen Festplatte.    
 - **/SourceKey: *&lt;Quellspeicherschlüssel&gt;:*** Speicherkontoschlüssel des Quellspeicherkontos.  
 - **/Dest:*&lt;Ziel&gt;:*** Storage-Container-URL zum Kopieren der virtuellen Festplatte.
 - **/DestKey: *&lt;Zielspeicherschlüssel&gt;:*** Speicherkontoschlüssel des Zielspeicherkontos.
 - **/BlobType: page:** Gibt an, dass das Ziel ein Seiten-Blob ist.
 - **/Pattern: *&lt;Dateiname&gt;:*** Geben Sie den Dateinamen der zu kopierenden VHD-Datei an.
   
Weitere Informationen zum Verwenden von AzCopy finden Sie unter [Erste Schritte mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md).

### Weitere Optionen zum Hochladen einer virtuellen Festplatte 

Sie können virtuelle Festplatten zudem mit den folgenden Tools in ein Speicherkonto hochladen:

- [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) 
- [Referenz zur REST-API des Import-/Exportdiensts](https://msdn.microsoft.com/library/dn529096.aspx) 

>[AZURE.NOTE]Import/Export kann nur zum Kopieren in oder aus dem Standardspeicherkonto verwendet werden. Sie benötigen ein Tool wie AzCopy zum Kopieren zwischen Standardspeicherkonto und Premium-Speicherkonto.

## Erstellen von Azure-VMs mit Premium-Speicher

Nachdem die virtuelle Festplatte in das gewünschte Speicherkonto hochgeladen wurde, führen Sie die Anweisungen in diesem Abschnitt aus, um die virtuelle Festplatte je nach Szenario als Betriebssystemimage oder Betriebssystemdatenträger zu registrieren und anschließend eine VM-Instanz daraus zu erstellen. Das VHD-Datenlaufwerk kann dem virtuellen Computer hinzugefügt werden, nachdem es erstellt wurde.

### Registrieren der virtuellen Festplatte
  
Zum Erstellen eines virtuellen Computers von einer Betriebssystem-VHD oder zum Hinzufügen eines Datenträgers zu einem neuen virtuellen Computer müssen Sie diese zunächst registrieren. Führen Sie je nach Szenario die nachstehenden Schritte aus.

#### Generalisierte Betriebssystem-VHD zum Erstellen mehrerer Azure-VM-Instanzen

Nachdem das generalisierte Betriebssystemimage in das VHD-Speicherkonto hochgeladen wurde, registrieren Sie es als **Azure-VM-Image**, sodass Sie damit VM-Instanzen erstellen können. Verwenden Sie die folgenden PowerShell-Cmdlets, um die virtuelle Festplatte als Azure-VM-Betriebssystemimage zu registrieren. Geben Sie die vollständige Container-URL an, an die die virtuelle Festplatte kopiert wurde.

	Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Kopieren und speichern Sie den Namen des neuen Azure-VM-Images. In dem Beispiel oben ist dies *OSImageName*.

#### Eindeutige Betriebssystem-VHD zum Erstellen einer einzelnen Azure-VM-Instanz

Nachdem die eindeutige Betriebssystem-VHD zum Speicherkonto hochgeladen wurde, registrieren Sie sie als **Azure-Betriebssystemdatenträger**, damit Sie eine VM-Instanz davon erstellen können. Verwenden Sie die folgenden PowerShell-Cmdlets, um die virtuelle Festplatte als Azure-VM-Betriebssystemdatenträger zu registrieren. Geben Sie die vollständige Container-URL an, an die die virtuelle Festplatte kopiert wurde.

	Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Kopieren und speichern Sie den Namen des neuen Azure-Betriebssystemdatenträger. In dem Beispiel oben ist dies *OSDisk*.

#### Neuen Azure-VM-Instanzen zuzuordnende Datenträger-VHD

Nachdem die Datenträger-VHD auf das Speicherkonto hochgeladen wurde, registrieren Sie sie als Azure-Datenträger, sodass sie an die neue Azure-VM-Instanz der DS-Serie angefügt werden kann.

Verwenden Sie die folgenden PowerShell-Cmdlets, um die virtuelle Festplatte als Azure-Datenträger zu registrieren. Geben Sie die vollständige Container-URL an, an die die virtuelle Festplatte kopiert wurde.

	Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Kopieren und speichern Sie den Namen des neuen Azure-Datenträgers. In dem Beispiel oben ist dies *DataDisk*.

### Erstellen eines virtuellen Azure-Computers der DS-Serie  

Nachdem das Betriebssystemimage oder der Betriebssystemdatenträger registriert wurde, können Sie eine neue Azure-VM-Instanz der DS-Serie erstellen. Sie verwenden dazu den Namen des Betriebssystemimages oder Betriebssystemdatenträgers, den Sie registriert haben. Wählen Sie den virtuellen Computer aus der Premium-Speicherebene aus. Im folgenden Beispiel wird die VM-Größe *Standard\_DS2* verwendet.

>[AZURE.NOTE]Aktualisieren Sie die Größe des Datenträgers, um sicherzustellen, dass er Ihren Anforderungen an Kapazität und Leistung sowie den Azure-Datenträgergrößen entspricht.

Führen Sie nacheinander die folgenden PowerShell-Cmdlets aus, um den neuen virtuellen Computer zu erstellen. Legen Sie zunächst die allgemeinen Parameter fest:

	$serviceName = "yourVM"
	$location = "location-name" (e.g., West US) 
	$vmSize ="Standard_DS2"
	$adminUser = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$vmSize = "Standard_DS2"

Erstellen Sie zuerst einen Clouddienst, in dem die neuen virtuellen Computer gehostet werden.

	New-AzureService -ServiceName $serviceName -Location $location

Erstellen Sie anschließend je nach Szenario die Azure-VM-Instanz aus dem registrierten Betriebssystemimage oder Betriebssystemdatenträger.

#### Generalisierte Betriebssystem-VHD zum Erstellen mehrerer Azure-VM-Instanzen

Erstellen Sie eine oder mehrere neue Azure-VM-Instanzen der DS-Serie mit dem **Azure-Betriebssystemimage**, das Sie registriert haben. Geben Sie beim Erstellen eines neuen virtuellen Computers den Namen des Betriebssystemimages in der Konfiguration des virtuellen Computers an, wie dies unten dargestellt ist.

	$OSImage = Get-AzureVMImage –ImageName "OSImageName"	

	$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

	Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm  

	New-AzureVM -ServiceName $serviceName -VM $vm	

#### Eindeutige Betriebssystem-VHD zum Erstellen einer einzelnen Azure-VM-Instanz

Erstellen Sie eine neue Azure-VM-Instanz der DS-Serie mit dem **Azure-Betriebssystemimage**, das Sie registriert haben. Geben Sie beim Erstellen eines neuen virtuellen Computers den Namen des Betriebssystemdatenträgers in der Konfiguration des virtuellen Computers an, wie dies unten dargestellt ist.

	$OSDisk = Get-AzureDisk –DiskName "OSDisk"  
	
	$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName
	
	New-AzureVM -ServiceName $serviceName –VM $vm

Geben Sie andere Azure-VM-Informationen an, z. B. Clouddienst, Region, Speicherkonto, Verfügbarkeitssatz und Zwischenspeicherungsrichtlinie. Beachten Sie, dass die VM-Instanz mit dem zugehörigen Betriebssystem oder Datenträger angeordnet werden muss, d. h., dass alle ausgewählten Clouddienste, Regionen und Speicherkonten sich an demselben Speicherort befinden müssen wie die zugrundeliegenden VHDs des Datenträgers.

### Anfügen eines Datenträgers  

Wenn Sie Datenträger-VHDs registriert haben, fügen Sie sie im letzten Schritt an die neue Azure-VM der DS-Serie an.

Verwenden Sie das folgende PowerShell-Cmdlet, um Datenträger an den neuen virtuellen Computer anzufügen, und geben Sie die Zwischenspeicherungsrichtlinie an. Im Beispiel unten wird die Zwischenspeicherungsrichtlinie als schreibgeschützt \(*ReadOnly*\) festgelegt.

	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

	Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm  

	Update-AzureVM  -VM $vm

>[AZURE.NOTE]Möglicherweise sind weitere Schritte zur Unterstützung Ihrer Anwendung erforderlich, die in dieser Anleitung nicht behandelt werden können.


## Nächste Schritte  

Informationen zu bestimmten Szenarios zur Migration virtueller Computer finden Sie in den folgenden Ressourcen:

- [Migrate Azure Virtual Machines between Storage Accounts](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/) \(in englischer Sprache\)  
- [Erstellen und Hochladen einer Windows Server-VHD nach Azure](../virtual-machines-create-upload-vhd-windows-server.md)  
- [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält](../virtual-machines-linux-create-upload-vhd.md)  
- [Migrating Virtual Machines from Amazon AWS to Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure) \(in englischer Sprache\)  

Lesen Sie außerdem die folgenden Ressourcen, um mehr über Azure Sstorage und Azure Virtual Machines zu erfahren:

- [Azure Storage](http://azure.microsoft.com/documentation/services/storage/) \(in englischer Sprache\)   
- [Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [Premium-Speicher: Hochleistungsspeicher für Workloads in Azure Virtual Machine](storage-premium-storage-preview-portal.md)  

[1]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png

<!---HONumber=July15_HO5-->