<properties 
	pageTitle="Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure"
	description="Dieses Thema enthält bewährte Methoden zur Optimierung der Leistung von SQL Server in Microsoft Azure Virtual Machines."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/01/2015"
	ms.author="jroth"/>

# Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure

## Übersicht

Dieses Thema enthält bewährte Methoden zur Optimierung der Leistung von SQL Server in Microsoft Azure Virtual Machines. Es empfiehlt sich, beim Ausführen von SQL Server auf virtuellen Azure-Computern weiterhin die gleichen Optionen zur Optimierung der Datenbankleistung zu verwenden, die für SQL Server in der lokalen Server-Umgebung gelten. Die Leistung einer relationalen Datenbank in einer öffentlichen Cloud hängt jedoch von vielen Faktoren ab, z. B. der Größe eines virtuellen Computers und der Konfiguration der Datenträger für Daten.

>[AZURE.NOTE]Dieser Artikel konzentriert sich auf die optimale Leistung von SQL Server auf Azure-VMs. Wenn Ihre Workload weniger anspruchsvoll ist, sind möglicherweise nicht alle unten aufgeführten Optimierungen erforderlich. Berücksichtigen Sie bei der Evaluierung dieser Empfehlungen Ihre Leistungsanforderungen und Workloadmuster.

Wenn Sie begleitende Informationen zu diesem Thema benötigen, [laden Sie das folgende Whitepaper herunter](http://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/Performance%20Guidance%20for%20SQL%20Server%20in%20Windows%20Azure%20Virtual%20Machines.docx). Beachten Sie, dass die Informationen in diesem Whitepaper in einigen Bereichen veraltet sind. Es wurde beispielsweise vor der Einführung von Storage Premium verfasst, welches jetzt zur Leistungsoptimierung empfohlen wird.

## Checkliste

Im folgenden finden eine kurze Checkliste für die optimale Leistung von SQL Server auf virtuellen Azure-Computern:

|Bereich|Optimierungen|
|---|---|
|**Größe des virtuellen Computers**|[DS3](virtual-machines-size-specs.md#standard-tier-ds-series) oder höher für SQL Enterprise-Edition.<br/><br/>[DS2](virtual-machines-size-specs.md#standard-tier-ds-series) oder höher für SQL Standard- und Web-Editionen.|
|**Speicher**|Verwenden Sie [Storage Premium ](../storage/storage-premium-storage-preview-portal.md).<br/><br/>Behalten Sie das [Speicherkonto](../storage/storage-create-storage-account.md) und den virtuellen SQL Server-Computer in derselben Region bei.<br/><br/>Deaktivieren Sie auf dem Speicherkonto den [georedundanten Speicher](../storage/storage-redundancy.md) (Georeplikation).|
|**Datenträger**|Verwenden Sie mindestens 2 [P30-Datenträger](../storage/storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whde-DEing-premium-storage) (1 für Protokolldateien, 1 für Datendateien und TempDB).<br/><br/>Vermeiden Sie die Verwendung von Betriebssystem- oder temporären Datenträgern für die Datenbankspeicherung oder Protokollierung.<br/><br/>Aktivieren Sie das Caching für Lesevorgänge auf den Datenträgern, auf denen die Datendateien und TempDB gehostet werden.<br/><br/>Aktivieren Sie kein Caching auf Datenträgern, auf denen die Protokolldatei gehostet wird.<br/><br/>Erstellen Sie ein Stripeset mehrerer Azure-Datenträger für Daten, um einen höheren E/A-Durchsatz zu erzielen.<br/><br/>Nehmen Sie Formatierungen mit dokumentierten Zuordnungsgrößen vor.|
|**E/A**|Aktivieren Sie die Datenbankseitenkomprimierung.<br/><br/>Aktivieren Sie die sofortige Dateiinitialisierung für Datendateien.<br/><br/>Begrenzen oder deaktivieren Sie die automatische Vergrößerung der Datenbank.<br/><br/>Deaktivieren Sie die automatische Verkleinerung der Datenbank.<br/><br/>Verschieben Sie alle Datenbanken, einschließlich der Systemdatenbanken, auf Datenträger für Daten.<br/><br/>Verschieben Sie die Verzeichnisse für das SQL Server-Fehlerprotokoll und die Ablaufverfolgungsdateien auf die Datenträger für Daten.<br/><br/>Richten Sie Standardspeicherorte für die Sicherung und die Datenbankdatei ein.<br/><br/>Aktivieren Sie gesperrte Seiten.<br/><br/>Wenden Sie SQL Server-Leistungskorrekturen an.|
|**Funktionsspezifisch**|Sichern Sie direkt in den Blobspeicher.|

Weitere Informationen finden Sie in den Richtlinien in den folgenden Unterabschnitten.

## Überlegungen zu VM-Größe und Speicherkonten

Für leistungsabhängige Anwendungen empfiehlt es sich die Verwendung der folgenden Größen für virtuelle Computer:

- **SQL Server Enterprise Edition**: DS3 oder höher

- **SQL Server Standard und Web Edition**: DS2 oder höher

Aktuelle Informationen zu unterstützten Größen für virtuelle Computer finden Sie unter [Größen für virtuelle Computer](virtual-machines-size-specs.md).

Außerdem sollten Sie Ihr Azure-Speicherkonto im selben Rechenzentrum wie Ihre SQL Server-VMs erstellen, um Übertragungsverzögerungen zu verringern. Wenn Sie ein Speicherkonto erstellen, deaktivieren Sie die Georeplikation, da keine konsistente Schreibreihenfolge über mehrere Datenträger gewährleistet werden kann. Konfigurieren Sie stattdessen eine SQL Server-Notfallwiederherstellungstechnologie zwischen zwei Azure-Rechenzentren. Weitere Informationen finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Überlegungen zu Datenträgern und Leistung

Wenn Sie einen virtuellen Azure-Computer erstellen, fügt die Plattform mindestens einen Datenträger für Ihr Betriebssystem zum virtuellen Computer hinzu. Bei diesem Datenträger handelt es sich um eine VHD, die als Seiten-BLOB gespeichert wird. Sie können auch weitere Datenträger für Daten zum virtuellen Computer hinzufügen. Diese werden als Seiten-BLOBs gespeichert. Auf virtuellen Azure-Computern gibt es einen weiteren Datenträgertyp, den so genannten temporären Datenträger. Dies ist ein Datenträger für temporären Speicherbereich auf dem Knoten.

### Betriebssystem-Datenträger

Ein Betriebssystem ist eine VHD, die Sie als ausgeführte Version eines Betriebssystems starten und einbinden können und die als Laufwerk **C** gekennzeichnet ist.

Die Standardcachingrichtlinie auf dem Betriebssystem-Datenträger ist **Lese-/Schreibzugriff**. Für leistungsabhängige Anwendungen empfiehlt sich die Verwendung von Datenträgern für Daten anstelle des Betriebssystem-Datenträgers. Weitere Informationen finden Sie im Abschnitt über Datenträger für Daten weiter unten.

### Temporärer Datenträger

Das als Laufwerk **D** bezeichnete temporäre Speicherlaufwerk wird nicht persistent in Azure Blob-Speicher gespeichert. Speichern Sie Ihre Daten- oder Protokolldateien nicht auf Laufwerk **D**.

Speichern Sie bei Verwendung virtueller Computer (VMs) der D-oder der G-Serie nur „TempDB“ und/oder Pufferpoolerweiterungen auf Laufwerk **D**. Im Gegensatz zu anderen VM-Reihen ist basiert das Laufwerk **D** auf VMs der D- und G-Serie auf SSD. Dies kann die Leistung von Workloads verbessern, die starken Gebrauch von temporären Objekten machen oder über Arbeitssätze verfügen, die nicht in den Arbeitsspeicher passen. Weitere Informationen finden Sie unter [Verwenden von SSDs in virtuellen Azure-Computern zum Speichern von SQL Server-TempDB- und Pufferpoolerweiterungen](http://blogs.technet.com/b/dataplatforminsider/archive/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions.aspx).

### Datenträger für Daten

- **Anzahl der Datenträger für Daten und Protokolldateien**: Verwenden Sie mindestens zwei [P30-Datenträger](../storage/storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whde-DEing-premium-storage), von denen ein Datenträger die Protokolldatei(en) und der andere Datenträger die Datendatei(en) und TempDB enthält. Für einen höheren Durchsatz werden möglicherweise zusätzliche Datenträger benötigt. Um die Anzahl der Datenträger für Daten zu bestimmen, müssen Sie die Anzahl der für Ihre Daten- und Protokolldatei-Datenträger verfügbaren IOPS analysieren. Diese Informationen finden Sie in den Tabellen zu IOPS pro [Größe des virtuellen Computers](virtual-machines-size-specs.md) und Datenträgergröße im folgenden Artikel: [Verwenden von Storage Premium für Datenträger](../storage/storage-premium-storage-preview-portal.md). Wenn Sie mehr Bandbreite benötigen, können Sie zusätzliche Datenträger hinzufügen und Datenträgerstriping verwenden. Wenn Sie Storage Premium nicht verwenden, lautet die Empfehlung, die für Ihre [Größe des virtuellen Computers](virtual-machines-size-specs.md) maximal unterstützte Anzahl von Datenträgern für Daten hinzuzufügen und Datenträgerstriping zu verwenden. Weitere Informationen zu Datenträgerstriping finden Sie unter dem entsprechenden Abschnitt unten.

- **Cachingrichtlinie**: Aktivieren Sie das Caching für Lesevorgänge nur für die Datenträger, auf denen Ihre Datendateien und TempDB gehostet werden. Wenn Sie Storage Premium nicht verwenden, aktivieren Sie Caching für keinen Datenträger für Daten. Informationen zum Konfigurieren des Datenträgercachings finden Sie in den folgenden Themen: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) und [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx).

- **Größe der NTFS-Zuordnungseinheiten**: Es empfiehlt sich, beim Formatieren des Datenträgers für Daten als Größe der Zuordnungseinheiten für Daten- und Protokolldateien sowie für TempDB einen Wert von 64 KB zu verwenden.

- **Datenträgerstriping**: Es wird empfohlen, die folgenden Richtlinien zu beachten:

	- Ziehen Sie für Windows 8 / Windows Server 2012 oder höher [Speicherplätze](https://technet.microsoft.com/library/hh831739.aspx) zurate. Legen Sie die Stripesetgröße für OLTP-Workloads auf 64 KB und für Data Warehousing-Workloads auf 256 KB fest, um Leistungseinbußen durch falsche Partitionsausrichtung zu vermeiden. Legen Sie außerdem Anzahl der Spalten = Anzahl der physischen Datenträger fest. Um einen Speicherplatz mit mehr als 8 Datenträgern zu konfigurieren, müssen Sie PowerShell (nicht die Server-Manager-Benutzeroberfläche) verwenden, um die Anzahl der Spalten explizit so festzulegen, dass sie der Anzahl der Datenträger entspricht. Weitere Informationen zum Konfigurieren von [Speicherplätzen](https://technet.microsoft.com/library/hh831739.aspx), finden Sie unter [Speicherplatz-Cmdlets in Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx)
	
	- Für Windows 2008 R2 oder früher können Sie dynamische Datenträger (Betriebssystem-Stripesetvolumes) verwenden, und die Stripesetgröße ist immer 64 KB. Beachten Sie, dass diese Option seit Windows 8 / Windows Server 2012 veraltet ist. Informationen hierzu finden Sie in der Supporterklärung unter [Übergang des Diensts für virtuelle Datenträger in die Windows-Speicherverwaltungs-API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).
	
	- Wenn Ihre Workload nicht protokollintensiv ist und keine dedizierten IOPS benötigt werden, können Sie einen einzigen Speicherpool konfigurieren. Andernfalls erstellen Sie zwei Speicherpools, einen für die Protokolldatei(en) und einem weiteren für die Datendatei(en) und TempDB. Bestimmen Sie die Anzahl der jedem Speicherpool zugeordneten Datenträger basierend auf der erwarteten Auslastung. Bedenken Sie, dass verschiedene VM-Größen unterschiedlich viele angefügte Datenträger für Daten unterstützen. Weitere Informationen finden Sie unter [Größen für virtuelle Computer](virtual-machines-size-specs.md).

## Überlegungen zur E/A-Leistung

- Die besten Ergebnisse mit Storage Premium werden erzielt, wenn Sie Ihre Anwendung und Anforderungen parallelisieren. Storage Premium ist für Szenarien bestimmt, in denen die E/A-Warteschlangentiefe größer als 1 ist, sodass nur geringe oder gar keine Leistungssteigerungen für serielle Singlethread-Anforderungen erreicht werden (auch wenn sie speicherintensiv sind). Dies kann sich z. B. auf die Singlethread-Testergebnisse der Leistungsanalysetools, z. B. SQLIO, auswirken.

- Ziehen Sie die Verwendung der [Datenbankseitenkomprimierung](https://msdn.microsoft.com/library/cc280449.aspx) in Betracht, weil sich dadurch die Leistung von E/A-intensiven Workloads verbessern lässt. Die Datenkomprimierung erhöht jedoch möglicherweise den CPU-Verbrauch auf dem Datenbankserver.

- Es könnte auch sinnvoll sein, alle Datendateien beim Übertragen in bzw. aus Azure zu komprimieren.

- Erwägen Sie die Aktivierung der sofortigen Dateiinitialisierung, um die für die anfängliche Dateizuordnung erforderliche Zeit zu verringern. Um die sofortige Dateiinitialisierung nutzen zu können, gewähren Sie dem SQL Server-Dienstkonto (MSSQLSERVER) mit SE\_MANAGE\_VOLUME\_NAME, und fügen Sie es zur Sicherheitsrichtlinie **Volumewartungsaufgaben durchführen** hinzu. Wenn Sie ein SQL Server-Plattformimage für Azure verwenden, wird das Standarddienstkonto (NT Service\\MSSQLSERVER) nicht zur Sicherheitsrichtlinie **Volumewartungsaufgaben durchführen** hinzugefügt. Anders ausgedrückt: Die sofortige Dateiinitialisierung wird in einem SQL Server Azure-Plattformimages nicht aktiviert. Starten Sie den SQL Server-Dienst nach dem Hinzufügen des SQL Server-Dienstkontos zur Sicherheitsrichtlinie **Volumewartungsaufgaben durchführen** neu. Möglicherweise sind bei Verwendung dieses Features bestimmte Sicherheitsaspekte zu berücksichtigen. Weitere Informationen finden Sie unter [Datenbankdatei-Initialisierung](https://msdn.microsoft.com/library/ms175935.aspx).

- **Automatische Vergrößerung** gilt lediglich als Behelfslösung für unerwartetes Wachstum. Führen Sie die tägliche Verwaltung Ihres Daten- und Protokolldateiwachstums nicht durch automatische Vergrößerung. Wenn die automatische Vergrößerung verwendet wird, vergrößern Sie die Datei vorab mit dem Switch "Größe".

- Stellen Sie sicher, dass **Automatische Verkleinerung** deaktiviert ist, um unnötigen Mehraufwand zu vermeiden, der sich negativ auf die Leistung auswirken kann.

- Wenn Sie SQL Server 2012 ausführen, installieren Sie Service Pack 1, kumulatives Update 10. Dieses Update enthält ein Fix für schlechte E/A-Leistung beim Ausführen von SELECT in temporären Tabellenanweisungen in SQL Server 2012 Informationen dazu finden Sie in diesem [Knowledge Base-Artikel](http://support.microsoft.com/kb/2958012).

- Verschieben Sie Systemdatenbanken (z. B. msdb und TempDB), Sicherungen und die Standardverzeichnisse für Daten- und Protokolldateien von SQL Server auf nicht zwischengespeicherte Datenträger für Daten, um die Leistung zu verbessern. Führen Sie dann die folgenden Aktionen aus:

	- Passen Sie die Pfade für XEvent- und Ablaufverfolgungsdateien an.
	
	- Passen Sie den Pfad für das SQL-Fehlerprotokoll an.
	
	- Passen Sie den Standardsicherungspfad an.
	
	- Passen Sie den Standarddatenbankspeicherort an.

- Richten Sie gesperrte Seiten ein, um E/A- und Auslagerungsaktivitäten zu verringern.

## Überlegungen zur Leistung bestimmter Features

Für manche Bereitstellungen können durch Verwendung erweiterter Konfigurationsmethoden zusätzliche Leistungsvorteile erzielt werden. In der folgenden Liste sind einige SQL Server-Features zusammengestellt, mit denen Sie eine bessere Leistung erzielen können:

- **Sicherung in Azure-Speicher**: Beim Durchführen von Sicherungen für SQL Server auf virtuellen Azure-Computern ausführen können Sie [SQL Server-Sicherung über URLs](https://msdn.microsoft.com/library/dn435916.aspx) zurate ziehen. Dieses Feature steht ab SQL Server 2012 SP1 CU2 zur Verfügung und wird für Sicherungen auf die angefügten Datenträgern für Daten empfohlen. Bei Sicherungen/Wiederherstellungen nach/von Azure-Speicher folgen Sie den Empfehlungen unter [SQL Server-URL-Sicherungen – Bewährte Methoden und Problembehandlung – und Wiederherstellen aus in Azure-Speicher gespeicherten Sicherungen](https://msdn.microsoft.com/library/jj919149.aspx). Darüber hinaus können Sie diese Sicherungen mit [automatisierten Sicherungen für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-automated-backup.md) automatisieren.

	In Versionen vor SQL Server 2012 können Sie das [Tool zum Erstellen von Microsoft SQL Server-Sicherungen in Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=40740) verwenden. Dieses Tool hilft, den Sicherungsdurchsatz durch Verwenden mehrerer Sicherungsstripeset-Ziele zu erhöhen.

- **SQL Server-Datendateien in Azure**: Dieses neue Feature [SQL Server-Datendateien in Windows Azure](https://msdn.microsoft.com/library/dn385720.aspx), ist ab SQL Server 2014 verfügbar. Das Ausführen von SQL Server mit Datendateien in Azure zeigt vergleichbare Leistungseigenschaften wie die Verwendung von Azure-Datenträgern.

## Nächste Schritte

Wenn Sie SQL Server und Storage Premium genauer untersuchen möchten, lesen Sie den Artikel [Verwenden von Azure Storage Premium mit SQL Server auf virtuellen Computern](virtual-machines-sql-server-use-premium-storage.md).

Bewährte Methoden für Sicherheit finden Sie unter [Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-security-considerations.md).

Weitere Themen zu virtuellen SQL Server-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern.](virtual-machines-sql-server-infrastructure-services.md)

<!---HONumber=September15_HO1-->