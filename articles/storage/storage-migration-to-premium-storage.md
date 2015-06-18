<properties 
    pageTitle="Migrieren zu Azure Premium-Speicher | Microsoft Azure" 
    description="Migrieren Sie zu Azure Premium-Speicher für leistungsstarke Datenträgerunterstützung mit niedriger Latenz von E/A-intensiven Arbeitsauslastungen auf virtuellen Azure-Computern." 
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
    ms.date="04/14/2015" 
    ms.author="tamram"/>


# Migrieren zu Azure Premium-Speicher

## Übersicht

Azure Premium-Speicher bietet leistungsstarke Solid State-Laufwerkstechnologie (SSD) mit niedriger Latenz für Arbeitsauslastungen mit hoher E/A, die auf virtuellen Azure-Computern ausgeführt werden. Mit dem Premium-Speicher können Ihre Anwendungen bis zu 32 TB Speicher pro virtuellem Computer nutzen und 50.000 IOPS (Input/Output Operations Per Second, E/A-Vorgänge pro Sekunde) pro virtuellem Computer mit äußerst niedriger Latenz für Lesevorgänge erzielen. In diesem Artikel finden Sie Anleitungen zum Migrieren von Festplatten oder virtuellen Computern (VMs) von lokalen oder Standardspeichern bzw. anderen Cloud-Plattformen zu Premium-Azure-Speicher. Eine ausführliche Übersicht über Azure Premium-Speicher finden Sie unter folgendem Link: [Hochleistungsspeicher für Azure Virtual Machine Arbeitsauslastungen](storage-premium-storage-preview-portal.md).

## Voraussetzungen 

### Voraussetzungen
- Sie benötigen ein Azure-Abonnement. Wenn Sie kein Abonnement besitzen, können Sie für einen Monat eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) abonnieren, oder Sie besuchen die Seite mit den [Azure-Preisen](http://azure.microsoft.com/pricing/) für weitere Optionen. 
- Zum Ausführen von PowerShell-Cmdlets benötigen Sie das Microsoft Azure PowerShell-Modul. Informationen zum Herunterladen dieses Moduls finden Sie unter [Microsoft Azure-Downloads](http://azure.microsoft.com/downloads/).
- Wenn Sie Azure-VMs mit Premium-Speicher nutzen möchten, müssen Sie die VMs der DS-Serie verwenden. Mit VMs der DS-Serie können Sie Standard- und Premium-Speicherdatenträger verwenden. Premium-Datenträger werden zukünftig mit mehreren VM-Typen verfügbar sein. Weitere Informationen zu den verfügbaren Typen und Größen von Azure-VM-Datenträgern finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 

### Überlegungen 

#### VM-Größen 
Im Folgenden finden Sie die VM-Größen der DS-Serie und deren Merkmale zusammengefasst. Sehen Sie sich die Leistungsmerkmale dieser Angebote für Premium-Speicher an, und wählen Sie die am besten für Ihre Arbeitsauslastung geeignete Option für Ihre Azure-Datenträger und virtuellen Computer aus. Stellen Sie sicher, dass auf Ihrem virtuellen Computer ausreichend Bandbreite zum Steuern des Datenverkehrs des Datenträgers verfügbar ist.

|Größe des virtuellen Computers|CPU-Kerne|Max. IOPS|Max. Bandbreite des Datenträgers|
|:---:|:---:|:---:|:---:|
|**STANDARD_DS1**|1|3.200|32 MB pro Sekunde|
|**STANDARD_DS2**|2|6.400|64 MB pro Sekunde|
|**STANDARD_DS3**|4|12.800|128 MB pro Sekunde|
|**STANDARD_DS4**|8|25.600|256 MB pro Sekunde|
|**STANDARD_DS11**|2|6.400|64 MB pro Sekunde|
|**STANDARD_DS12**|4|12.800|128 MB pro Sekunde|
|**STANDARD_DS13**|8|25.600|256 MB pro Sekunde|
|**STANDARD_DS14**|16|50.000|512 MB pro Sekunde|

#### Datenträgergrößen 
Es gibt drei Datenträgertypen, die Sie mit Ihrem virtuellen Computer verwenden können, und jeder Typ weist bestimmte IOPs und Einschränkungen auf. Berücksichtigen Sie diese Beschränkungen beim Auswählen des Datenträgertyps für Ihren virtuellen Computer je nach Anforderungen Ihrer Anwendung hinsichtlich Kapazität, Leistung, Skalierbarkeit und Spitzenlasten.

|Datenträgertyp des Premium-Speichers|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Datenträgergröße|128 GB|512 GB|1024 GB (1 TB)|
|IOPS pro Datenträger|500|2.300|5.000|
|Durchsatz pro Datenträger|100 MB pro Sekunde|150 MB pro Sekunde|200 MB pro Sekunde|

#### Skalierbarkeitsziele für das Speicherkonto

Premium-Speicherkonten haben zusätzlich zu den unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/azure/dn249410.aspx) aufgeführten Zielen folgende Skalierbarkeitsziele. Wenn die Anforderungen Ihrer Anwendung die Skalierbarkeitsziele eines einzelnen Speicherkontos überschreiten, erstellen Sie die Anwendung so, dass mehrere Speicherkonten verwendet werden, und partitionieren Sie Ihre Daten in diesen Speicherkonten.

|Gesamtkapazität des Kontos|Gesamtbandbreite für ein lokal redundantes Speicherkonto|
|:--|:---|
|Festplattenkapazität: 35 TB<br />Kapazität für Momentaufnahmen: 10 TB|Bis zu 50 GB pro Sekunde für eingehenden und ausgehenden Datenverkehr|

Weitere Informationen zu den Spezifikationen für Premium-Speicher finden Sie unter [Skalierbarkeits- und Leistungsziele bei der Verwendung von Premium-Speicher](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whde-deing-premium-storage).

#### Zusätzliche Datenträger 
Legen Sie je nach Arbeitsauslastung fest, ob zusätzliche Datenträger für Ihren virtuellen Computer erforderlich sind. Sie können mehrere Datenträger für permanente Daten auf Ihrem virtuellen Computer anfügen. Bei Bedarf können Sie Daten über die Datenträger verteilen, um die Kapazität und die Leistung des Volumens zu erhöhen. Wenn Sie Daten über Premium-Speicher-Datenträger mithilfe von [Speicherplätzen](http://technet.microsoft.com/library/hh831739.aspx) verteilen, sollten Sie sie für jeden verwendeten Datenträger eine Spalte konfigurieren. Andernfalls kann die Gesamtleistung des Stripesetvolume aufgrund ungleicher Verteilung des Datenverkehrs auf die Datenträger niedriger sein als erwartet. Für Linux-VMs können Sie dazu das Dienstprogramm mdadm verwenden. Weitere Informationen finden Sie im Artikel [Konfigurieren von Software-RAID unter Linux](../virtual-machines-linux-configure-raid.md).

#### Zwischenspeicherungsrichtlinie für Datenträger 
Standardmäßig ist die Richtlinie für das Zwischenspeichern für alle Premium-Datenträger "Schreibgeschützt" und für die Premium-Betriebssystem-Datenträger, die an den virtuellen Computer angeschlossen sind, "Lesen/Schreiben". Diese Konfigurationseinstellung wird empfohlen, um die optimale E/A-Leistung für Ihre Anwendung zu erreichen. Für Datenträger mit hohem oder ausschließlichem Schreibzugriff (z. B. SQL Server-Protokolldateien) deaktivieren Sie das Zwischenspeichern, sodass Sie eine bessere Anwendungsleistung erzielen können. Die Einstellungen für das Zwischenspeichern bei vorhandenen Datenträgern können Sie über das Azure-Portal oder den Parameter *-HostCaching* des Cmdlets *Set-AzureDataDisk* aktualisieren.

#### Standort 
Wählen Sie einen Speicherort, an dem Azure Premium-Speicher verfügbar ist. Aktuelle Informationen zu Speicherorten finden Sie unter [Wichtige Informationen zum Premium-Speicher](storage-premium-storage-preview-portal.md#important-things-to-know-about-premium-storage). Virtuelle Computer in der gleichen Umgebung wie das Speicherkonto, in dem die Datenträger für den virtuellen Computer gespeichert sind, erzielen eine höhere Leistung als solche in unterschiedlichen Regionen.

#### Sonstige Azure-VM-Konfigurationseinstellungen 

Beim Erstellen einer Azure-VM müssen Sie bestimmte Einstellungen für die virtuellen Computer konfigurieren. Denken Sie daran, dass es einige Einstellungen gibt, die für die Lebensdauer des virtuellen Computers festgelegt sind, während Sie andere Einstellungen später hinzufügen oder ändern können. Überprüfen Sie diese Azure-VM-Konfigurationseinstellungen, und stellen Sie sicher, dass diese korrekt konfiguriert sind, um die Anforderungen für die Arbeitsauslastung zu erfüllen. Weitere Informationen finden Sie unter [Informationen zu Azure-VM-Konfigurationseinstellungen](https://msdn.microsoft.com/library/azure/dn763935.aspx).

## Vorbereiten von virtuellen Festplatten für die Migration 

Der folgende Abschnitt enthält Richtlinien zur Vorbereitung der Migration der virtuellen Festplatten des virtuellen Computers. Bei der virtuellen Festplatte kann es sich um Folgendes handeln:
 
- Ein allgemeines Betriebssystemimage, das verwendet werden kann, um mehrere Azure-VMs zu erstellen  
- Einen Betriebssystem-Datenträger, der mit einer einzelnen virtuellen Azure-Computer-Instanz verwendet werden kann  
- Einen Datenträger, der mit einem virtuellen Azure-Computer zur dauerhaften Speicherung verbunden werden kann.

### Voraussetzungen

- Ein Azure-Abonnement, ein Speicherkonto und ein Container in diesem Speicherkonto, in den die virtuelle Festplatte kopiert wird. Beachten Sie, dass das Zielspeicherkonto je nach Ihren Anforderung ein Standard- oder Premium-Speicherkonto sein kann. 
- Ein Tool zum Generalisieren der VHD, wenn Sie mehrere virtuelle Computerinstanzen daraus erstellen möchten. Beispiel: Sysprep für Windows oder virt-sysprep für Ubuntu. Ein Tool für den Upload der VHD-Datei in das Speicherkonto. Beispielsweise [AzCopy](storage-use-azcopy.md) oder der [Azure-Speicher-Explorer](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). In diesem Handbuch sind die Schritte mit dem Tool AzCopy beschrieben. Für große Datenmengen mit begrenzter Bandbreite können Sie den [Microsoft Azure-Import/Export-Dienst](storage-import-export-service.md) in Erwägung ziehen, bei dem Sie Festplatten für den Datentransfer an ein Azure-Datencenter senden. Mit dem Import/Export-Dienst können Sie Daten nur in ein Standardspeicherkonto kopieren. Sobald die Daten sich im Standardspeicherkonto befinden, verwenden Sie die [Copy Blob-API](https://msdn.microsoft.com/library/azure/dd894037.aspx)(in englischer Sprache) oder AzCopy zum Übertragen der Daten in Ihr Premium-Speicherkonto.
- Ein Tool zum Kopieren der virtuellen Festplatte kann unter einem virtuellen Azure-Computer in der gleichen Umgebung wie die VHDs ausgeführt werden.
- Microsoft Azure unterstützt nur VHD-Dateien mit fester Größe. VHDX-Dateien und dynamische virtuelle Festplatten werden nicht unterstützt. Wenn Sie über eine dynamische virtuelle Festplatte verfügen, können Sie sie mit dem Cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) in eine VHD mit fester Größe konvertieren .

### Vorbereiten von virtuellen Festplatten

|Szenario|Schritte|
|:---|:---|
|Generalisierte Betriebssystem-VHD zum Erstellen von mehreren VM-Instanzen|<p>Denken Sie beim Hochladen einer **VHD-Datei, die verwendet wird, um mehrere generische Azure-VM-Instanzen zu erstellen**, daran, dass Sie die VHD zunächst mit einem Dienstprogramm zur Systemvorbereitung generalisieren müssen. Dies gilt für eine lokale virtuelle Festplatte ebenso wie für Cloud-Festplatten. Sysprep entfernt alle computerspezifischen Informationen von der virtuellen Festplatte.</p><p>**Wichtig:** Erstellen Sie eine Momentaufnahme oder eine Sicherung Ihres virtuellen Computers, bevor Sie ihn generalisieren. Beim Ausführen von Sysprep wird die VM-Instanz gelöscht.</p>Führen Sie die Schritte unten zur Vorbereitung einer VHD mit Windows-Betriebssystem aus:<br />Beachten Sie, dass durch den Befehl "Sysprep" die virtuellen Computer heruntergefahren werden müssen. Weitere Informationen zu Sysprep finden Sie unter [Sysprep (Systemvorbereitung) – Übersicht](http://technet.microsoft.com/library/hh825209.aspx) oder unter [Technische Referenz zu Sysprep](http://technet.microsoft.com/library/cc766049(v=ws.10).aspx). <ul><li>Öffnen Sie ein Eingabeaufforderungsfenster als Administrator.</li><li>Geben Sie den folgenden Befehl zum Öffnen von Sysprep ein:<br />**%windir%\\system32\\sysprep\\sysprep.exe**</li><li>Wählen Sie im Systemvorbereitungsprogramm die Option "Out-of-Box-Experience (OOBE) für System aktivieren" aus, aktivieren Sie das Kontrollkästchen "Verallgemeinern", wählen Sie **Herunterfahren** aus, und klicken Sie dann auf **OK**.</li><li>Dadurch wird das Betriebssystem generalisiert und das System heruntergefahren.![][1]</li></ul>Verwenden Sie für einen virtuellen Ubuntu-Computer "virt-sysprep". Weitere Informationen finden Sie unter [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html)(in englischer Sprache). Für diese Vorgänge steht auch [Open-Source-Software zur Linux-Serverbereitstellung](http://www.cyberciti.biz/tips/server-provisioning-software.html) für andere Linux-Betriebssysteme zur Verfügung.|
|Eindeutige Betriebssystem-VHD zum Erstellen einer einzelnen VM-Instanz|Wenn auf dem virtuellen Computer eine Anwendung ausgeführt wird, die die spezifischen Daten des Computers benötigt, generalisieren Sie die virtuelle Festplatte nicht. **Eine nicht generalisierte virtuelle Festplatte kann verwendet werden, um eine eindeutige Azure-VM-Instanz zu erstellen.** Wenn Sie beispielsweise über einen Domänencontroller für die virtuelle Festplatte verfügen, wird dieser durch die Ausführung von Sysprep außer Kraft gesetzt. Überprüfen Sie die auf dem virtuellen Computer ausgeführten Anwendungen und die Auswirkung von Sysprep auf diese, bevor Sie die VHD verallgemeinern.|
|VM-Instanzen zugeordnete Datenträger-VHDs|Wenn Sie über Datenlaufwerke in einem Cloud-Speicher verfügen, die migriert werden sollen, müssen Sie sicherstellen, dass die virtuellen Computer, die diese Datenträger nutzen, heruntergefahren wurden. Erstellen Sie für lokale Datenträger eine konsistente virtuelle Festplatte.|
Da die virtuelle Festplatte jetzt bereit ist, führen Sie die Schritte im nächsten Abschnitt aus, um die VHD in den Azure-Speicher hochzuladen und als Betriebssystemimage, bereitgestellten Betriebssystem-Datenträger oder bereitgestellten Datenträger zu registrieren.

## Kopieren von VHDs in Azure-Speicher

### Quelle

|Szenario|Schritte|
|:---|:---|
|Kopieren von VHDs aus dem Azure-Speicher|Wenn Sie VHDs aus einem Standard-Azure-Speicherkonto in ein Premium-Azure-Speicherkonto migrieren, müssen Sie den Quellpfad des VHD-Containers, den Namen der VHD-Datei und den Speicherkontenschlüssel des Quellspeicherkontos kopieren.<ul><li>Öffnen Sie "Azure-Portal" > Virtuelle Computer > Datenträger"</li><li>Kopieren und speichern Sie die VHD-Container-URL aus der Spalte "Speicherort", https://*AccountName*.blob.core.windows.net/*ContainerName*/</li></ul>|
|Kopieren von VHDs aus anderen Clouddiensten|Wenn Sie virtuelle Festplatten aus anderen Cloud-Speichern als Azure zu Azure migrieren möchten, müssen Sie zuerst die VHD-Datei in ein lokales Verzeichnis exportieren. Kopieren Sie den vollständigen Quellpfad des lokalen Verzeichnisses, in dem die VHD-Datei gespeichert ist. <ul><li>Bei Verwendung von AWS exportieren Sie die EC2-Instanz in eine virtuelle Festplatte in einem Amazon S3-Bucket. Befolgen Sie die unter [Exporting Amazon EC2 Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html)(in englischer Sprache) beschriebenen Schritte, um das Befehlszeilenschnittstellen-Tool Amazon EC2 zu installieren, und führen Sie den Befehl zum Exportieren der EC2-Instanz in eine VHD-Datei aus. Verwenden Sie **VHD** für die Variable "DISK_IMAGE_FORMAT", wenn Sie den Befehl ausführen. Die exportierte VHD-Datei wird im Amazon S3-Bucket gespeichert, der während dieses Prozesses angegeben wurde.</li></ul>![][2]<ul><li>Laden Sie die VHD-Datei aus dem S3-Bucket herunter. Wählen Sie die VHD-Datei > **Aktionen** > **Herunterladen**.</li></ul>![][3]|
|Kopieren einer lokalen VHD|Wenn Sie die VHD-Datei aus einer lokalen Umgebung migrieren, benötigen Sie den vollständigen Quellpfad, in dem VHD-Datei gespeichert wurde. Dies kann ein Serverspeicherort oder eine Dateifreigabe sein.

### Ziel 

Erstellen Sie ein Speicherkonto für die Verwaltung Ihrer virtuellen Festplatten. Berücksichtigen Sie die folgenden Punkte beim Planen des Speicherorts der virtuellen Festplatten.

- Der Speicherort des Zielspeicherkontos kann sich je nach Anwendungsanforderungen in einem Standard- oder Premium-Speicher befinden. 
- Der Speicherort für das Konto muss mit den Azure-VMs der DS-Serie übereinstimmen, die Sie in der letzten Phase erstellen. Sie können je nach Anforderungen in ein neues Speicherkonto kopieren oder die Verwendung desselben Speicherkontos planen.
- Kopieren und speichern Sie den Speicherkontenschlüssel des Zielspeicherkontos für die nächste Phase.
- Für Datenträger können Sie auswählen, dass einige in einem Standardspeicherkonto verbleiben (z. B. Festplatten mit Daten, auf die weniger zugegriffen wird) und andere mit hohen IOPs im Premium-Speicherkonto gespeichert werden.

### Kopieren einer VHD mit AzCopy

Mit AzCopy können Sie auf einfache Weise die VHD-Datei über das Internet hochladen. Je nach Größe der virtuellen Festplatten kann dies einige Zeit in Anspruch nehmen. Denken Sie daran, die Eingangs-/Ausgangsgrenzen des Speicherkontos bei Verwendung dieser Option zu überprüfen. Die Azure-Speichergrenzen finden Sie [hier](azure-subscription-service-limits#storage-limits).

1. Laden Sie AzCopy über den folgendem Link herunter, und installieren Sie es: [Aktuelle Version von AzCopy](http://aka.ms/downloadazcopy).  
2. Öffnen Sie Azure PowerShell, und wechseln Sie zu dem Ordner, in dem AzCopy installiert ist.  
3. Verwenden Sie den folgenden Befehl, um die VHD-Datei von der Quelle zum Ziel zu kopieren. **AzCopy /Source:** *&lt;Quelle&gt;* **/SourceKey:** *&lt;Quellspeicherschlüssel&gt;* **/Destination:** *&lt;Ziel&gt;* **/DestKey:** *&lt;Zielspeicherschlüssel&gt;* **/BlobType:page /Pattern:** *&lt;Dateiname&gt;*  
  - *&lt;Quelle&gt;:* Speicherort des Ordners oder der Speicher-Container-URL, die die virtuelle Festplatte enthält.    
 - *&lt;Quellspeicherschlüssel&gt;:* Speicherkontenschlüssel des Quellspeicherkontos.  
 - *&lt;Ziel&gt;:* Storage-Container-URL zum Kopieren der virtuellen Festplatte.
 - *&lt;Zielspeicherschlüssel&gt;:* Speicherkontenschlüssel des Zielspeicherkontos.
 - /BlobType:page: Gibt an, dass das Ziel ein Seiten-BLOB ist.
 - *&lt;Dateiname&gt;*: der Name der VHD-Datei, die Sie kopieren möchten.
 - /Pattern:*&lt;Dateiname&gt;:* Geben Sie den Dateinamen der VHD-Datei an, die Sie kopieren möchten.
   
Dieser Befehl kopiert alle Dateien aus dem Container *&lt;Quelle&gt;* in den Container *&lt;Ziel&gt;*. Weitere Informationen zum Verwenden von AzCopy finden Sie im Abschnitt "Erste Schritte" für das [AzCopy-Befehlszeilen-Dienstprogramm](storage-use-azcopy.md).
### Weitere Optionen zum Hochladen einer virtuellen Festplatte 

- [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) 
- [Referenz zur REST-API des Import-/Exportdiensts](https://msdn.microsoft.com/library/dn529096.aspx) 

>[AZURE.NOTE]Import/Export kann nur zum Kopieren in oder aus dem Standardspeicherkonto verwendet werden. Sie benötigen ein Tool wie AzCopy zum Kopieren zwischen Standardspeicherkonto und Premium-Speicherkonto.

## Erstellen von Azure-VMs mit Premium-Speicher

Nachdem die virtuelle Festplatte in das gewünschte Speicherkonto hochgeladen wurde, führen Sie die Anweisungen in diesem Abschnitt aus, um die virtuelle Festplatte je nach Szenario als Betriebssystemimage oder Betriebssystemdatenträger zu registrieren und anschließend eine VM-Instanz daraus zu erstellen. Das VHD-Datenlaufwerk kann dem virtuellen Computer hinzugefügt werden, nachdem es erstellt wurde.

### Registrieren der virtuellen Festplatte
  
Zum Erstellen eines virtuellen Computers aus einer Betriebssystem-VHD oder zum Hinzufügen der Daten-VHD zu einem neuen virtuellen Computer müssen Sie die virtuelle Festplatte zunächst registrieren. Führen Sie je nach Szenario die nachstehenden Schritte aus.

<table>
<tr>
<th>Szenario</th>
<th>Schritte zum Registrieren der virtuellen Festplatte</th>
</tr>
<tr>
<td>Generalisierte Betriebssystem-VHD zum Erstellen mehrerer Azure-VM-Instanzen</td>
<td><p>Nachdem das generalisierte Betriebssystemimage in das VHD-Speicherkonto hochgeladen wurde, registrieren Sie es als <b>Azure-VM-Image</b>, sodass Sie damit VM-Instanzen erstellen können. </p><p>Verwenden Sie die folgenden PowerShell-Cmdlets, um die virtuelle Festplatte als Azure-VM-Betriebssystemimage zu registrieren. Geben Sie die vollständige Container-URL an, an die die virtuelle Festplatte kopiert wurde.</p><p><code>Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows</code></p>Kopieren und speichern Sie den Namen des neuen Azure-VM-Images. In dem obigen Beispiel ist dies "OSImageName".</td>
</tr>
</tr>
<td>Eindeutige Betriebssystem-VHD zum Erstellen einer einzelnen Azure-VM-Instanz</td>
<td><p>Nachdem die eindeutige Betriebssystem-VHD zum Speicherkonto hochgeladen wurde, registrieren Sie sie als <b>Azure-Betriebssystemdatenträger</b>, damit Sie eine VM-Instanz davon erstellen können.</p><p>Verwenden Sie die folgenden PowerShell-Cmdlets, um die virtuelle Festplatte als Azure-VM-Betriebssystemdatenträger zu registrieren. Geben Sie die vollständige Container-URL an, an die die virtuelle Festplatte kopiert wurde.</p><code>"https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "OSDisk" -OS "Windows"</code><p>Kopieren und speichern Sie den Namen des neuen Azure-Betriebssystemdatenträger. In dem obigen Beispiel ist dies "OSDisk".</p></td>
</tr>
</tr>
<td>Neuen Azure-VM-Instanzen zuzuordnende Datenträger-VHD</td>
<td><p>Nachdem die Datenträger-VHD auf das Speicherkonto hochgeladen wurde, registrieren Sie sie als Azure-Datenträger, sodass sie an die neue Azure-VM-Instanz der DS-Serie angefügt werden kann.</p><p>Verwenden Sie die folgenden PowerShell-Cmdlets, um die virtuelle Festplatte als Azure-Datenträger zu registrieren. Geben Sie die vollständige Container-URL an, an die die virtuelle Festplatte kopiert wurde.</p><code>Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "DataDisk"</code><p>Kopieren und speichern Sie den Namen des neuen Azure-Datenträgers. In dem obigen Beispiel ist dies "DataDisk".</p></td>
</tr>
</table>

### Erstellen eines virtuellen Azure-Computers der DS-Serie   

Sobald das Betriebssystemimage oder der Betriebssystemdatenträger registriert wurde, kann eine neue Instanz der DS-Serie von Azure-VM erstellt werden. Sie verwenden dazu den Namen des Betriebssystemimages oder Betriebssystemdatenträgers, den Sie registriert haben. Wählen Sie den virtuellen Computer aus der Premium-Speicherebene aus. Im folgenden Beispiel verwenden wir die VM-Größe "Standard_DS2".

>[AZURE.NOTE]Aktualisieren Sie die Größe des Datenträgers, um sicherzustellen, dass er Ihren Anforderungen an Kapazität und Leistung sowie den Azure-Datenträgergrößen entspricht.

Führen Sie schrittweise die folgenden PowerShell-Cmdlets aus, um den neuen virtuellen Computer zu erstellen.

Legen Sie zunächst die allgemeinen Parameter fest:

	$vmSize ="Standard_DS2"
	$adminName = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$location = "East US 2"  

Erstellen Sie anschließend je nach Szenario die Azure-VM-Instanz aus dem registrierten Betriebssystemimage oder Betriebssystemdatenträger.

<table>
<tr>
<th>Szenario</th>
<th>Schritte</th>
</tr>
<tr>
<td>Generalisierte Betriebssystem-VHD zum Erstellen mehrerer Azure-VM-Instanzen</td>
<td>Erstellen Sie eine oder mehrere neue Azure-VM-Instanzen der DS-Serie mit dem <b>Azure-Betriebssystemimage</b>, das Sie registriert haben. Geben Sie beim Erstellen eines neuen virtuellen Computers den Namen des Betriebssystemimages in der Konfiguration des virtuellen Computers an, wie im Folgenden dargestellt:</br></br>
<code>$OSImage = Get-AzureVMImage –ImageName "NameBetriebssystemimage" $vm = New-AzureVMConfig -Name "NeueVM" -InstanceSize $vmSize -ImageName $OSImage.ImageName</code>
</br></br>
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NeueVM"</code></td></tr>  
<tr>
<td>Eindeutige Betriebssystem-VHD zum Erstellen einer einzelnen Azure-VM-Instanz</td>
<td>Erstellen Sie eine neue Azure-VM-Instanz der DS-Serie mit dem <b>Azure-Betriebssystemimage</b>, das Sie registriert haben. Geben Sie beim Erstellen eines neuen virtuellen Computers den Namen des Betriebssystemdatenträgers in der Konfiguration des virtuellen Computers an, wie im Folgenden dargestellt:</br></br>
<code>$OSDisk = Get-AzureDisk –DiskName "OSDisk" $vm = New-AzureVMConfig -Name "NeueVM" -InstanceSize $vmSize -DiskName $OSDisk.DiskName</code>
</br></br>  
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NeueVM"</code>
</td>
</tr>
</table>

Geben Sie andere Azure-VM-Informationen an, z. B. Clouddienst, Region, Speicherkonto, Verfügbarkeitssatz und Zwischenspeicherungsrichtlinie. Beachten Sie, dass die VM-Instanz mit dem zugehörigen Betriebssystem oder Datenträger angeordnet werden muss, d. h., dass alle ausgewählten Clouddienste, Regionen und Speicherkonten sich an demselben Speicherort befinden müssen wie die zugrundeliegenden VHDs des Datenträgers.

### Anfügen eines Datenträgers  

Wenn Sie Datenträger-VHDs registriert haben, fügen Sie sie im letzten Schritt an die neue Azure-VM der DS-Serie an.

Verwenden Sie das folgende PowerShell-Cmdlet, um Datenträger an den neuen virtuellen Computer anzufügen, und geben Sie die Zwischenspeicherungsrichtlinie an. Im Beispiel unten wird die Zwischenspeicherungsrichtlinie als schreibgeschützt (ReadOnly) festgelegt.

	$vmName ="yourVM"
	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
	Add-AzureDataDisk -ImportFrom -DiskName “DataDisk” -LUN 0 –HostCaching ReadOnly –VM $vm | Update-AzureVM  

>[AZURE.NOTE]Es können bestimmte Schritte erforderlich sein, um Ihre Anwendungen zu unterstützen, die in diesem Handbuch nicht behandelt werden können.


## Nächste Schritte  

Informationen zu bestimmten Migrationsszenarios virtueller Computer finden Sie in den folgenden Ressourcen

- [Migrate Azure Virtual Machines between Storage Accounts](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)(in englischer Sprache)  
- [Erstellen und Hochladen einer Windows Server-VHD nach Azure](../virtual-machines-create-upload-vhd-windows-server.md)  
- [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält](../virtual-machines-linux-create-upload-vhd.md)  
- [Migrating Virtual Machines from Amazon AWS to Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)(in englischer Sprache)  

Lesen Sie außerdem die folgenden Ressourcen, um mehr über Azure-Speicher und virtuelle Azure-Computer zu erfahren:

- [Azure Storage](http://azure.microsoft.com/documentation/services/storage/)(in englischer Sprache)   
- [Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](storage-premium-storage-preview-portal.md)  



[1]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image1.png
[2]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image2.png
[3]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image3.png
<!--HONumber=52-->
 