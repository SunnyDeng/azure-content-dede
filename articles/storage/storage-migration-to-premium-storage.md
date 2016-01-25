<properties
    pageTitle="Migrieren zu Azure Premium-Speicher | Microsoft Azure"
    description="Migrieren Sie die vorhandenen virtuellen Computer zu Azure Storage Premium. Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für E/A-intensive Workloads, die auf virtuellen Azure-Computern ausgeführt werden."
    services="storage"
    documentationCenter="na"
    authors="ms-prkhad"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="prkhad"/>


# Migrieren zu Azure Premium-Speicher

## Übersicht

Azure Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für virtuelle Computer mit E/A-intensiven Workloads. Datenträger von virtuellen Computern (VM), die Storage Premium nutzen, speichern Daten auf SSDs (Solid State Drives). Sie können die VM-Datenträger Ihrer Anwendung zu Azure Storage Premium migrieren, um von der Geschwindigkeit und Leistung dieser Laufwerke zu profitieren.

Ein virtueller Azure-Computer unterstützt das Anfügen mehrerer Storage Premium-Datenträger, damit Ihre Anwendung bis zu 64 TB Speicher pro virtuellem Computer nutzen kann. Mit Storage Premium können Ihre Anwendungen bis zu 80.000 IOPS (Input/Output Operations Per Second, E/A-Vorgänge pro Sekunde) pro virtuellem Computer nutzen sowie 2.000 MB Datenträgerdurchsatz pro Sekunde und virtuellem Computer mit äußerst niedriger Latenz für Lesevorgänge erzielen.

>[AZURE.NOTE]Es wird empfohlen, alle Datenträger von virtuellen Computer, die eine hohe IOPS-Leistung erfordern, zu Azure Storage Premium zu migrieren, um die beste Leistung für Ihre Anwendung zu erzielen. Wenn Ihr Datenträger keine hohe IOPS-Leistung erfordert, können Sie die Kosten beschränken, indem Sie sie im Standard-Speicher belassen. Dieser Speicher speichert Daten auf Datenträgern virtueller Computer auf Festplatten (Hard Disk Drives, HDDs) anstelle von SSDs.

Der Zweck dieser Anleitung ist es, neue Benutzer von Azure Storage Premium besser auf einen reibungslosen Übergang vom aktuellen System hin zu Storage Premium vorzubereiten. Die Anleitung behandelt drei der wichtigsten Komponenten in diesem Prozess: Planen der Migration zu Storage Premium, Migrieren vorhandener virtueller Festplatten (VHDs) zu Storage Premium und Erstellen von virtuellen Azure-Computerinstanzen in Storage Premium.

Für den Abschluss des vollständigen Migrationsprozesses sind möglicherweise zusätzliche Aktionen vor und nach den in dieser Anleitung angegebenen Schritten erforderlich. Beispiele sind das Konfigurieren von virtuellen Netzwerken oder Endpunkten oder Ändern von Code innerhalb der Anwendung selbst. Diese Aktionen sind für jede Anwendung eindeutig. Sie sollten sie zusammen mit den Schritten in dieser Anleitung ausführen, um den kompletten Wechsel zu Storage Premium so reibungslos wie möglich zu gestalten.

Eine Funktionsübersicht von Azure Storage Premium finden Sie unter [Storage Premium: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](storage-premium-storage-preview-portal.md).

Dieser Leitfaden ist in zwei Abschnitte gegliedert, in denen die folgenden zwei Migrationsszenarien erläutert werden:

- [Migrieren von VMs außerhalb von Azure zu Azure Storage Premium](#migrating-vms-from-outside-azure-to-azure-premium-storage).
- [Migrieren von vorhandenen Azure-VMs zu Azure Storage Premium](#migrating-existing-azure-vms-to-azure-premium-storage).

Befolgen Sie je nach Szenario die im entsprechenden Abschnitt angegebenen Schritte.

## Migrieren von VMs von anderen Plattformen zu Azure Storage Premium

### Voraussetzungen
- Sie benötigen ein Azure-Abonnement. Wenn Sie kein Abonnement besitzen, können Sie für einen Monat eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) abonnieren, oder Sie besuchen die Seite mit den [Azure-Preisen](http://azure.microsoft.com/pricing/) für weitere Optionen.
- Zum Ausführen von PowerShell-Cmdlets benötigen Sie das Microsoft Azure PowerShell-Modul. Informationen zum Herunterladen dieses Moduls finden Sie unter [Microsoft Azure-Downloads](http://azure.microsoft.com/downloads/).
- Wenn Sie Azure-VMs unter Storage Premium nutzen möchten, müssen Sie VMs der DS-Serie oder GS-Serie verwenden. Mit VMs der DS-Serie können Sie Standard- und Premium-Speicherdatenträger verwenden. Premium-Datenträger werden zukünftig mit mehreren VM-Typen verfügbar sein. Weitere Informationen zu den verfügbaren Typen und Größen von Azure-VM-Datenträgern finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

### Überlegungen

#### VM-Größen
Die Größenspezifikationen der Azure-VM sind unter [Größen für virtuelle Computer](../virtual-machines/virtual-machines-size-specs.md) aufgelistet. Sehen Sie sich die Leistungsmerkmale von virtuellen Computern für Storage Premium an, und wählen Sie die am besten für Ihre Workload geeignete VM-Größe aus. Stellen Sie sicher, dass auf Ihrem virtuellen Computer ausreichend Bandbreite zum Steuern des Datenverkehrs des Datenträgers verfügbar ist.


#### Datenträgergrößen
Es gibt drei Datenträgertypen, die Sie mit Ihrem virtuellen Computer verwenden können, und jeder Typ weist bestimmte IOPs und Einschränkungen auf. Berücksichtigen Sie diese Beschränkungen beim Auswählen des Datenträgertyps für Ihren virtuellen Computer je nach Anforderungen Ihrer Anwendung hinsichtlich Kapazität, Leistung, Skalierbarkeit und Spitzenlasten.

|Datenträgertyp des Premium-Speichers|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Datenträgergröße|128 GB|512 GB|1024 GB (1 TB)|
|IOPS pro Datenträger|500|2\.300|5\.000|
|Durchsatz pro Datenträger|100 MB pro Sekunde|150 MB pro Sekunde|200 MB pro Sekunde|

#### Skalierbarkeitsziele für das Speicherkonto

Premium-Speicherkonten haben zusätzlich zu den unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/azure/dn249410.aspx) aufgeführten Zielen folgende Skalierbarkeitsziele. Wenn die Anforderungen Ihrer Anwendung die Skalierbarkeitsziele eines einzelnen Speicherkontos überschreiten, erstellen Sie die Anwendung so, dass mehrere Speicherkonten verwendet werden, und partitionieren Sie Ihre Daten in diesen Speicherkonten.

|Gesamtkapazität des Kontos|Gesamtbandbreite für ein lokal redundantes Speicherkonto|
|:--|:---|
|Festplattenkapazität: 35 TB<br />Kapazität für Momentaufnahmen: 10 TB|Bis zu 50 GB pro Sekunde für eingehenden und ausgehenden Datenverkehr|

Weitere Informationen zu den Spezifikationen für Premium-Speicher finden Sie unter [Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whde-DEing-premium-storage).

#### Zusätzliche Datenträger
Legen Sie je nach Workload fest, ob zusätzliche Datenträger für Ihren virtuellen Computer erforderlich sind. Sie können mehrere Datenträger für permanente Daten auf Ihrem virtuellen Computer anfügen. Bei Bedarf können Sie Daten über die Datenträger verteilen, um die Kapazität und die Leistung des Volumens zu erhöhen. Wenn Sie Daten über Premium-Speicher-Datenträger mithilfe von [Speicherplätzen](http://technet.microsoft.com/library/hh831739.aspx) verteilen, sollten Sie sie für jeden verwendeten Datenträger eine Spalte konfigurieren. Andernfalls kann die Gesamtleistung des Stripesetvolume aufgrund ungleicher Verteilung des Datenverkehrs auf die Datenträger niedriger sein als erwartet. Für Linux-VMs können Sie dazu das Hilfsprogramm *mdadm* verwenden. Weitere Informationen finden Sie im Artikel [Konfigurieren von Software-RAID unter Linux](../virtual-machines-linux-configure-raid.md).

#### Zwischenspeicherungsrichtlinie für Datenträger
Standardmäßig ist die Richtlinie für das Zwischenspeichern für alle Premium-Datenträger *Schreibgeschützt* und für die Premium-Betriebssystem-Datenträger, die an den virtuellen Computer angeschlossen sind, *Lesen/Schreiben*. Diese Konfigurationseinstellung wird empfohlen, um die optimale E/A-Leistung für Ihre Anwendung zu erreichen. Für Datenträger mit hohem oder ausschließlichem Schreibzugriff (z. B. SQL Server-Protokolldateien) deaktivieren Sie das Zwischenspeichern, sodass Sie eine bessere Anwendungsleistung erzielen können. Die Einstellungen für das Zwischenspeichern bei vorhandenen Datenträgern können Sie über das [Azure-Portal](portal.azure.com) oder den Parameter *-HostCaching* des Cmdlets *Set-AzureDataDisk* aktualisieren.

#### Standort
Wählen Sie einen Speicherort, an dem Azure Premium-Speicher verfügbar ist. Aktuelle Informationen zu verfügbaren Standorten finden Sie unter [Azure: Dienste nach Region](http://azure.microsoft.com/regions/#services). Virtuelle Computer in der gleichen Umgebung wie das Speicherkonto, in dem die Datenträger für den virtuellen Computer gespeichert sind, erzielen eine höhere Leistung als solche in unterschiedlichen Regionen.

#### Sonstige Azure-VM-Konfigurationseinstellungen

Beim Erstellen einer Azure-VM müssen Sie bestimmte Einstellungen für die virtuellen Computer konfigurieren. Denken Sie daran, dass es einige Einstellungen gibt, die für die Lebensdauer des virtuellen Computers festgelegt sind, während Sie andere Einstellungen später hinzufügen oder ändern können. Überprüfen Sie diese Azure-VM-Konfigurationseinstellungen, und stellen Sie sicher, dass diese korrekt konfiguriert sind, um die Anforderungen für die Workload zu erfüllen.

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
> Zum Kopieren großer Datenmengen bei begrenzter Bandbreite könnten Sie auch den [Microsoft Azure Import/Export-Dienst](storage-import-export-service.md) nutzen, um Daten durch den Versand von Festplatten an ein Azure-Datencenter zu übertragen. Mit dem Azure Import/Export-Dienst können Sie Daten nur in ein Standardspeicherkonto kopieren. Sobald sich die Daten im Standardspeicherkonto befinden, können Sie sie mit der [Copy Blob-API](https://msdn.microsoft.com/library/azure/dd894037.aspx) oder mit AzCopy in Ihr Storage Premium-Konto übertragen.
>
> Beachten Sie, dass Microsoft Azure nur VHD-Dateien mit fester Größe unterstützt. VHDX-Dateien und dynamische virtuelle Festplatten werden nicht unterstützt. Wenn Sie über eine dynamische virtuelle Festplatte verfügen, können Sie sie mit dem Cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) in eine VHD mit fester Größe konvertieren .

### Szenarios für die Vorbereitung von virtuellen Festplatten

Im Folgenden werden einige Szenarios für die Vorbereitung Ihrer virtuellen Festplatte erörtert.

#### Generalisierte Betriebssystem-VHD zum Erstellen von mehreren VM-Instanzen

Denken Sie beim Hochladen einer VHD-Datei, mit der mehrere generische Azure-VM-Instanzen erstellt werden, daran, dass Sie die VHD zunächst mit einem Dienstprogramm zur Systemvorbereitung (Sysprep) generalisieren müssen. Dies gilt für eine lokale virtuelle Festplatte ebenso wie für Cloud-Festplatten. Mit Sysprep werden alle computerspezifischen Informationen von der virtuellen Festplatte entfernt.

>[AZURE.IMPORTANT]Erstellen Sie eine Momentaufnahme oder eine Sicherung Ihres virtuellen Computers, bevor Sie ihn generalisieren. Beim Ausführen von Sysprep wird die VM-Instanz gelöscht. Führen Sie die Schritte unten zur Vorbereitung einer VHD mit Windows-Betriebssystem aus. Beachten Sie, dass beim Ausführen des Sysprep-Befehls der virtuelle Computer heruntergefahren werden muss. Weitere Informationen zu Sysprep finden Sie unter [Sysprep (Systemvorbereitung) – Übersicht](http://technet.microsoft.com/library/hh825209.aspx) oder unter [Technische Referenz zu Sysprep] (http://technet.microsoft.com/library/cc766049(v=ws.10).aspx).

1. Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator.
2. Geben Sie den folgenden Befehl zum Öffnen von Sysprep ein:

		%windir%\system32\sysprep\sysprep.exe

4. Wählen Sie im Systemvorbereitungsprogramm die Option "Out-of-Box-Experience (OOBE) für System aktivieren" aus, aktivieren Sie das Kontrollkästchen "Verallgemeinern", wählen Sie **Herunterfahren** aus, und klicken Sie dann auf **OK** (siehe dazu Abbildung unten). Dadurch werden das Betriebssystem generalisiert und das System heruntergefahren.

	![][1]

Verwenden Sie für einen virtuellen Ubuntu-Computer "virt-sysprep". Weitere Informationen finden Sie unter [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) (in englischer Sprache). Für diese Vorgänge steht auch [Open-Source-Software zur Linux-Serverbereitstellung](http://www.cyberciti.biz/tips/server-provisioning-software.html) für andere Linux-Betriebssysteme zur Verfügung.

#### Eindeutige Betriebssystem-VHD zum Erstellen einer einzelnen VM-Instanz

Wenn auf dem virtuellen Computer eine Anwendung ausgeführt wird, die die spezifischen Daten des Computers benötigt, generalisieren Sie die virtuelle Festplatte nicht. Eine nicht generalisierte virtuelle Festplatte kann verwendet werden, um eine eindeutige Azure-VM-Instanz zu erstellen. Wenn Sie beispielsweise über einen Domänencontroller für die virtuelle Festplatte verfügen, wird dieser durch die Ausführung von Sysprep außer Kraft gesetzt. Überprüfen Sie die auf dem virtuellen Computer ausgeführten Anwendungen und die Auswirkung von Sysprep auf diese, bevor Sie die VHD verallgemeinern.

#### VM-Instanzen zugeordnete Datenträger-VHDs

Wenn Sie über Datenlaufwerke in einem Cloud-Speicher verfügen, die migriert werden sollen, müssen Sie sicherstellen, dass die virtuellen Computer, die diese Datenträger nutzen, heruntergefahren wurden. Erstellen Sie für lokale Datenträger eine konsistente virtuelle Festplatte.

## Kopieren von VHDs in Azure-Speicher

Nun da die virtuelle Festplatte vorbereitet ist, können Sie die Schritte unten ausführen, um die VHD in Azure Storage hochzuladen und als Betriebssystemimage, bereitgestellten Betriebssystem-Datenträger oder bereitgestellten Datenträger zu registrieren.

### Erstellen des Ziels für die virtuelle Festplatte

Erstellen Sie ein Speicherkonto für die Verwaltung Ihrer virtuellen Festplatten. Berücksichtigen Sie die folgenden Punkte beim Planen des Speicherorts der virtuellen Festplatten:

- Der Speicherort des Zielspeicherkontos kann sich je nach Anwendungsanforderungen in einem Standard- oder Premium-Speicher befinden.
- Der Speicherort für das Speicherkonto muss mit den Azure-VMs der DS-Serie oder GS-Serie übereinstimmen, die Sie in der letzten Phase erstellen. Sie können je nach Anforderungen in ein neues Speicherkonto kopieren oder die Verwendung desselben Speicherkontos planen.
- Kopieren und speichern Sie den Speicherkontenschlüssel des Zielspeicherkontos für die nächste Phase.
- Für Datenträger können Sie auswählen, dass einige in einem Standardspeicherkonto verbleiben (z. B. Festplatten mit Daten, auf die weniger zugegriffen wird) und andere mit hohem IOPS-Wert in einem Premium-Speicherkonto gespeichert werden.

### Kopieren der virtuellen Festplatte aus der Quelle

Im Folgenden sind einige Szenarios zum Kopieren der virtuellen Festplatte beschrieben.

#### Kopieren von VHDs aus Azure Storage

Wenn Sie VHDs aus einem Azure-Standardspeicherkonto zu einem Azure Premium-Speicherkonto migrieren, müssen Sie den Quellpfad des VHD-Containers, den Namen der VHD-Datei und den Speicherkontoschlüssel des Quellspeicherkontos kopieren.

1. Wechseln Sie zu **Azure-Portal > Virtuelle Computer > Datenträger**.
2. Kopieren und speichern Sie die VHD-Container-URL aus der Spalte "Speicherort". Die Container-URL ähnelt dem folgenden Format: `https://myaccount.blob.core.windows.net/mycontainer/`.

#### Kopieren von VHDs aus anderen Clouddiensten

Wenn Sie virtuelle Festplatten aus anderen Cloud-Speichern als Azure zu Azure migrieren möchten, müssen Sie zuerst die VHD-Datei in ein lokales Verzeichnis exportieren. Kopieren Sie den vollständigen Quellpfad des lokalen Verzeichnisses, in dem die VHD-Datei gespeichert ist.

1. Bei Verwendung von AWS exportieren Sie die EC2-Instanz in eine virtuelle Festplatte in einem Amazon S3-Bucket. Befolgen Sie die in der Amazon-Dokumentation unter [Exporting Amazon EC2 Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html) (in englischer Sprache) beschriebenen Schritte, um das Befehlszeilenschnittstellen-Tool Amazon EC2 zu installieren, und führen Sie den Befehl zum Exportieren der EC2-Instanz in eine VHD-Datei aus.

	Verwenden Sie unbedingt **VHD** für die Variable "DISK&#95;IMAGE&#95;FORMAT", wenn Sie den Befehl ausführen. Die exportierte VHD-Datei wird im Amazon S3-Bucket gespeichert, der während dieses Prozesses angegeben wurde.

	![][2]

2. Laden Sie die VHD-Datei aus dem S3-Bucket herunter. Wählen Sie die VHD-Datei und dann **Aktionen** > **Herunterladen** aus.

	![][3]|

#### Kopieren einer lokalen VHD

Wenn Sie die VHD-Datei aus einer lokalen Umgebung migrieren, benötigen Sie den vollständigen Quellpfad, in dem VHD-Datei gespeichert wurde. Dies kann ein Serverspeicherort oder eine Dateifreigabe sein.

### Kopieren einer VHD mit AzCopy

Mit AzCopy können Sie auf einfache Weise die VHD-Datei über das Internet hochladen. Je nach Größe der virtuellen Festplatten kann dies einige Zeit in Anspruch nehmen. Denken Sie daran, die Eingangs-/Ausgangsgrenzen des Speicherkontos bei Verwendung dieser Option zu überprüfen. Ausführliche Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](storage-scalability-targets.md).

1. Laden Sie AzCopy über den folgendem Link herunter, und installieren Sie es: [Aktuelle Version von AzCopy](http://aka.ms/downloadazcopy).  
2. Öffnen Sie Azure PowerShell, und wechseln Sie zu dem Ordner, in dem AzCopy installiert ist.  
3. Verwenden Sie den folgenden Befehl, um die VHD-Datei von der "Quelle" zum "Ziel" zu kopieren.

		AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>  

	Es folgt die Beschreibung der im AzCopy-Befehl verwendeten Parameter:

 - **/Source: *&lt;Quelle&gt;:*** Speicherort des Ordners oder URL des Speichercontainers mit der virtuellen Festplatte (VHD).
 - **/SourceKey: *&lt;Quellspeicherschlüssel&gt;:*** Speicherkontoschlüssel des Quellspeicherkontos.  
 - **/Dest:*&lt;Ziel&gt;:*** URL des Speichercontainers, in den die virtuelle Festplatte kopiert werden soll.
 - **/DestKey: *&lt;Zielspeicherschlüssel&gt;:*** Speicherkontoschlüssel des Zielspeicherkontos.
 - **/BlobType: page:** Gibt an, dass das Ziel ein Seitenblob ist.
 - **/Pattern: *&lt;Dateiname&gt;:*** Geben Sie den Dateinamen der zu kopierenden VHD-Datei an.

Weitere Informationen zum Verwenden von AzCopy finden Sie unter [Erste Schritte mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md).

### Kopieren einer VHD mit PowerShell
Sie können die VHD-Datei auch mithilfe des PowerShell-Cmdlets "Start-AzureStorageBlobCopy" kopieren. Verwenden Sie zum Kopieren der VHD den folgenden Befehl in Azure PowerShell: Ersetzen Sie die Werte in <> mit den entsprechenden Werten aus Ihrem Quell- und Zielspeicherkonto. Um diesen Befehl zu verwenden, müssen Sie im Zielspeicherkonto über einen Container namens "vhds" verfügen. Wenn der Container nicht vorhanden ist, erstellen Sie einen vor dem Ausführen des Befehls.

    $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"
    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>
    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>
    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

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

Nachdem die Datenträger-VHD in das Speicherkonto hochgeladen wurde, registrieren Sie sie als Azure-Datenträger, sodass sie an die neue Azure-VM-Instanz der DS-Serie oder GS-Serie angefügt werden kann.

Verwenden Sie die folgenden PowerShell-Cmdlets, um die virtuelle Festplatte als Azure-Datenträger zu registrieren. Geben Sie die vollständige Container-URL an, an die die virtuelle Festplatte kopiert wurde.

	Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Kopieren und speichern Sie den Namen des neuen Azure-Datenträgers. In dem Beispiel oben ist dies *DataDisk*.

### Erstellen Sie einen neuen virtuellen Computer der DS- oder GS-Serie.

Nachdem das Betriebssystemimage oder der Betriebssystemdatenträger registriert wurde, können Sie einen neuen virtuellen Computer der DS- oder GS-Serie erstellen. Sie verwenden dazu den Namen des Betriebssystemimages oder Betriebssystemdatenträgers, den Sie registriert haben. Wählen Sie den virtuellen Computer aus der Premium-Speicherebene aus. Im folgenden Beispiel wird die VM-Größe *Standard\_DS2* verwendet.

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

Wenn Sie Datenträger-VHDs registriert haben, fügen Sie sie im letzten Schritt an die neue Azure-VM der DS-Serie oder GS-Serie an.

Verwenden Sie das folgende PowerShell-Cmdlet, um Datenträger an den neuen virtuellen Computer anzufügen, und geben Sie die Zwischenspeicherungsrichtlinie an. Im Beispiel unten wird die Zwischenspeicherungsrichtlinie als schreibgeschützt (*ReadOnly*) festgelegt.

	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

	Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm  

	Update-AzureVM  -VM $vm

>[AZURE.NOTE]Möglicherweise sind weitere Schritte zur Unterstützung Ihrer Anwendung erforderlich, die in dieser Anleitung nicht behandelt werden können.

## Migrieren von vorhandenen Azure-VMs nach Azure Storage Premium

Wenn Sie derzeit über einen virtuellen Azure-Computer mit Standarddatenträger verfügen, befolgen Sie das nachfolgend beschriebene Verfahren für die Migration zu Storage Premium. Auf einer hohen Ebene umfasst die Migration zwei Phasen: - Migrieren der Datenträger vom Standardspeicherkonto in ein Storage Premium-Konto - Konvertieren der Größe des virtuellen Computers von A/D/G nach DS oder GS, was für die Verwendung von Storage Premium-Datenträgern erforderlich ist.

Im vorherigen Abschnitt unter "Überlegungen" finden Sie darüber hinaus Informationen über verschiedene Optimierungen, die Sie für Storage Premium durchführen können. Abhängig von der Optimierungen, die für Ihre Anwendungen verfügbar sind, kann der Migrationsvorgang einem der unten aufgeführten Migrationsszenarien zugeordnet werden.

### Eine einfache Migration
In diesem einfachen Szenario versuchen Sie, Ihre Konfiguration beim Migrieren vom Standardspeicher nach Storage Premium beizubehalten. Sie verschieben die einzelnen Datenträger im Istzustand und konvertieren dann auch den virtuellen Computer. Der Vorteil ist die Einfachheit der Migration; und der Nachteil besteht darin, dass die resultierende Konfiguration möglicherweise nicht für die geringsten Kosten optimiert ist.

#### Vorbereitung
1. Stellen Sie sicher, dass Storage Premium in der Region verfügbar ist, in die migriert werden soll.
2. Bestimmen Sie die neue VM-Serie, die Sie verwenden möchten. Abhängig von der Verfügbarkeit in der Region und Ihren Bedürfnissen entsprechend sollte es sich dabei um die DS- oder GS-Serie handeln.
3. Bestimmen Sie die genaue VM-Größe, die Sie verwenden möchten. Die VM-Größe muss groß genug sein, um die Anzahl der vorhandenen Datenträger zu unterstützen. Wenn Sie z. B. über 4 Datenträger verfügen, muss der virtuelle Computer 2 oder mehr Kerne haben. Berücksichtigen Sie auch die Anforderungen hinsichtlich Verarbeitungsleistung, Arbeitsspeicher und Netzwerkbandbreite.
4. Erstellen Sie ein Storage Premium-Konto in der Zielregion. Dies ist das Konto, das Sie für den neuen virtuellen Computer verwenden werden.
5. Halten Sie die Details zum aktuellen virtuellen Computer bereit, einschließlich der Liste der Datenträger und die zugehörigen VHD-Blobs.
6. Bereiten Sie Ihre Anwendung für Ausfallzeiten vor. Für eine saubere Migration müssen Sie die gesamte Verarbeitung im aktuellen System beenden. Erst dann können Sie den konsistenten Zustand erreichen, den Sie auf die neue Plattform migrieren können. Die Dauer der Ausfallzeit hängt von der Datenmenge der zu migrierenden Datenträger ab.

#### Ausführungsschritte
1.	Beenden Sie den virtuellen Computer. Wie bereits dargelegt, muss der virtuelle Computer vollständig abgeschaltet sein, um einen fehlerfreien Zustand zu migrieren. Die Ausfallzeit hält bis zum Abschluss der Migration an.

2.	Sobald der virtuelle Computer angehalten wurde, kopieren Sie alle virtuellen Festplatten dieses virtuellen Computers in das neue Storage Premium-Konto. In diesem Fall müssen Sie den Betriebssystemdatenträger-VHD-Blob sowie alle Datenträger-VHD-Blobs kopieren. Für die Migration empfehlen wir die Verwendung von AzCopy oder CopyBlob. Auf Wunsch können Sie auch andere Drittanbietertools verwenden.

  Befehle finden Sie in den vorhergehenden Abschnitten zu [Kopieren einer VHD mit AzCopy](#copy-a-vhd-with-azcopy) oder [Kopieren einer VHD mit PowerShell](#copy-a-vhd-with-powershell).

3.	Überprüfen Sie, ob der Kopiervorgang abgeschlossen ist. Warten Sie, bis alle Datenträger kopiert wurden. Sobald alle Datenträger kopiert wurden, können Sie mit den nächsten Schritten bei der Erstellung des neuen virtuellen Computers fortfahren.
4.	Erstellen Sie einen neuen Betriebssystemdatenträger mithilfe des Betriebssystemdatenträger-VHD-Blobs, den Sie in das Storage Premium-Konto kopiert haben. Verwenden Sie dazu das PowerShell-Cmdlet "Add-AzureDisk".

    Beispielskript: Add-AzureDisk -DiskName "NewOSDisk1" -MediaLocation "https://newpremiumstorageaccount.blob.core.windows.net/vhds/MyOSDisk.vhd" -OS "Windows"
5. Als Nächstes erstellen Sie den virtuellen Computer der DS-Serie (oder GS-Serie) mithilfe des oben genannten Betriebssystemdatenträgers und der Datenträger.

    Beispielskript zum Erstellen eines neuen Clouddiensts und eines neuen virtuellen Computers in diesem Dienst: New-AzureService -ServiceName "NewServiceName" -Location "East US2"

        New-AzureVMConfig -Name "NewDSVMName" -InstanceSize "Standard_DS2" -DiskName "NewOSDisk1" | Add-AzureProvisioningConfig -Windows | Add-AzureDataDisk -LUN 0 -DiskLabel "DataDisk1" -ImportFrom -MediaLocation "https://newpremiumstorageaccount.blob.core.windows.net/vhds/Disk1.vhd" | Add-AzureDataDisk -LUN 1 -DiskLabel "DataDisk2" -ImportFrom -MediaLocation https://newpremiumstorageaccount.blob.core.windows.net/vhds/Disk2.vhd | New-AzureVM -ServiceName "NewServiceName" –Location “East US 2”

6.	Sobald der neue virtuelle Computer ausgeführt wird, können Sie mit demselben Benutzernamen und Kennwort wie für den ursprünglichen virtuellen Computer darauf zugreifen und überprüfen, ob alles wie erwartet funktioniert. Alle Einstellungen, einschließlich der Stripesetvolumes, sind auch im neuen virtuellen Computer vorhanden.

7.	Der letzte Schritt besteht darin, den Sicherungs- und Wartungszeitplan für den neuen virtuellen Computer auf Grundlage der Anwendungsanforderungen zu planen.

### Automation
Wenn Sie mehrere zu migrierende virtuelle Computer haben, kann eine Automatisierung mithilfe von PowerShell-Skripts hilfreich sein. Im Folgenden finden Sie ein Beispielskript, das die Migration eines virtuellen Computers automatisiert. Beachten Sie, dass das unten stehende Skript nur ein Beispiel ist, und dass es nur einige Annahmen über die aktuellen VM-Datenträger gibt. Sie müssen womöglich das Skript dem speziellen Szenario entsprechend aktualisieren.

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a vm from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source vm to the new storage account.
    And then it will create a new vm from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [String] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the vm mannually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires to delete the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`tMoving VM requires to remove the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
    	    Start-Sleep -Seconds 10
    	    $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a vm from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

### Optimierung
Die aktuelle Konfiguration des virtuellen Computers kann speziell für die Arbeit mit Standarddatenträgern angepasst werden, zum Beispiel, um die Leistung zu erhöhen. Verwenden Sie dazu viele Datenträger in einem Stripesetvolume. Da Storage Premium-Datenträger eine bessere Leistung bieten, können Sie Ihre Kosten optimieren, indem Sie die Anzahl der Datenträger reduzieren. Ihre Anwendung benötigt möglicherweise ein Volume mit 2000 IOPS, und Sie verwenden ein Stripeset mit 4 Standardspeicherdatenträgern, um 4 x 500 = 2000 IOPS zu erhalten. Mit Storage Premium-Datenträger kann ein einzelner 512-GB-Datenträger 2300 IOPS bereitstellen. Folglich können Sie, statt 4 Datenträger separat unter Storage Premium zu verwenden, die Kosten optimieren, indem Sie einen einzelnen Datenträger verwenden. Optimierungen wie diese Anforderung müssen von Fall zu Fall behandelt werden und erfordern benutzerdefinierte Schritte nach der Migration. Beachten Sie auch, dass dieser Prozess bei Datenbanken und Anwendungen, die von dem während der Einrichtung definierten Datenträgerlayout abhängig sind, möglicherweise nicht gut funktioniert.

#### Vorbereitung
1.	Führen Sie die einfache Migration wie im vorigen Abschnitt beschrieben durch. Optimierungen werden nach der Migration auf dem neuen virtuellen Computer ausgeführt.
2.	Definieren Sie die neue Datenträgergröße, die für eine optimale Konfiguration erforderlich ist.
3.	Bestimmen Sie die Zuordnung der aktuellen Datenträger/Volumes zu neuen Datenträgerspezifikationen.

#### Ausführungsschritte:
1.	Erstellen Sie die neue Datenträger mit der richtigen Größe auf dem virtuellen Storage Premium-Computer.
2.	Melden Sie sich beim virtuellen Computer an, und kopieren Sie die Daten vom aktuellen Volume auf den neuen Datenträger, der diesem Volume zugeordnet ist. Führen Sie diesen Vorgang für alle aktuellen Volumes durch, die einer neuen Festplatte zugeordnet werden müssen.
3.	Ändern Sie als Nächstes die Anwendungseinstellungen, um zu den neuen Datenträgern zu wechseln, und trennen Sie die alten Volumes.

###  Anwendungsmigrationen
Datenbanken und andere komplexe Anwendungen erfordern womöglich spezielle Schritte, wie vom Anwendungsanbieter für die Migration definiert. Weitere Informationen finden Sie in der Dokumentation der jeweiligen Anwendung. So können beispielsweise Datenbanken in der Regel mittels Sicherung und Wiederherstellung migriert werden.

## Nächste Schritte

Informationen zu bestimmten Szenarios zur Migration virtueller Computer finden Sie in den folgenden Ressourcen:

- [Migrate Azure Virtual Machines between Storage Accounts (in englischer Sprache)](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Erstellen und Hochladen einer Windows Server-VHD nach Azure](../virtual-machines-create-upload-vhd-windows-server.md)
- [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält](../virtual-machines-linux-create-upload-vhd.md)
- [Migrating Virtual Machines from Amazon AWS to Microsoft Azure (in englischer Sprache)](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Lesen Sie außerdem die folgenden Ressourcen, um mehr über Azure Sstorage und Azure Virtual Machines zu erfahren:

- [Azure Storage (in englischer Sprache)](http://azure.microsoft.com/documentation/services/storage/)
- [Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)
- [Premium-Speicher: Hochleistungsspeicher für Workloads in Azure Virtual Machine](storage-premium-storage-preview-portal.md)

[1]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png

<!---HONumber=AcomDC_0114_2016-->