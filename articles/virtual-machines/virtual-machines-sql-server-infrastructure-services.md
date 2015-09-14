<properties 
	pageTitle="Übersicht über SQL Server auf virtuellen Azure-Computern"
	description="Dieser Artikel bietet einen Übersicht über SQL Server auf virtuellen Azure-IaaS-Computern. Dies schließt Links zu weiterführenden Inhalten ein."
	services="virtual-machines"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/01/2015"
	ms.author="jroth"/>

# Übersicht zu SQL Server auf virtuellen Azure-Computern

## Erste Schritte
Sie können [SQL Server auf virtuellen Azure-Computern](http://azure.microsoft.com/services/virtual-machines/sql-server/) in einer Vielzahl von Konfigurationen hosten, die von einem einzelnen Datenbankserver bis hin zu einer Konfiguration mehrerer Computer mit AlwaysOn-Verfügbarkeitsgruppen und einem virtuellen Azure-Netzwerk reichen.

>[AZURE.NOTE]Die Ausführung von SQL Server auf einem virtuellen Azure-Computer stellt eine Möglichkeit zum Speichern von relationalen Daten in Azure dar. Sie können auch den Azure SQL-Datenbankdienst verwenden. Weitere Informationen finden Sie unter [Grundlegendes zur Azure SQL-Datenbank und SQL Server in Azure VMs](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

Um einen virtuellen Computer mit SQL Server in Azure zu erstellen, müssen Sie zunächst ein Abonnement für die Azure-Plattform erwerben. Sie können ein Azure-Abonnement über [Kaufoptionen](http://azure.microsoft.com/pricing/purchase-options/) erwerben. Wenn Sie es kostenlos testen möchten, besuchen Sie [kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

### Bereitstellen einer SQL Server-Instanz auf einem einzelnen virtuellen Computer

Nachdem Sie sich für ein Abonnement angemeldet haben, besteht die einfachste Möglichkeit zum Bereitstellen eines virtuellen Computers mit SQL Server in Azure darin, [im Azure-Verwaltungsportal aus dem Imagekatalog ein Image eines virtuellen Computers mit SQL Server bereitzustellen](virtual-machines-provision-sql-server.md). Diese Images enthalten in den Preisen für den virtuellen Computer SQL Server-Lizenzen.

Die folgende Tabelle enthält eine Matrix der SQL Server-Images, die im Katalog der virtuellen Computer verfügbar sind.

|SQL Server-Version|Betriebssystem|SQL Server-Edition|
|---|---|---|
|SQL Server 2008 R2 SP2|Windows Server 2008 R2|Enterprise, Standard, Web|
|SQL Server 2008 R2 SP3|Windows Server 2008 R2|Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012|Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2014|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2014 SP1|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2016 CTP|Windows Server 2012 R2|Auswertung|

>[AZURE.NOTE]Die VM-Katalogimages für Data Warehousing und transaktionale Workloads (oben nicht gezeigt) sind veraltet und werden in Kürze aus dem Katalog entfernt. Verwenden Sie die Standardimages in der vorstehenden Tabelle, und optimieren Sie die Leistung für Ihre spezifische Workload, indem Sie die Empfehlungen unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-sql-server-performance-best-practices.md) befolgen.

Zusätzlich zu diesen vorkonfigurierten Images können Sie auch [einen virtuellen Azure-Computer](virtual-machines-windows-tutorial.md) ohne vorinstallierte SQL Server-Instanz erstellen. Sie können eine beliebige SQL Server-Instanz darauf installieren, für die Sie über eine Lizenz verfügen. Sie migrieren Ihre Lizenz nach Azure, um SQL Server auf einem virtuellen Azure-Computer auszuführen. Diese Migration erfolgt gemäß [Lizenzmobilität durch Software Assurance für Azure](http://azure.microsoft.com/pricing/license-mobility/). In diesem Szenario bezahlen Sie lediglich für die Compute- und Speicherkosten[ ](http://azure.microsoft.com/pricing/details/virtual-machines)in Azure, die im Zusammenhang mit dem virtuellen Computer anfallen.

Zu den häufigen Aufgaben in diesen frühen Phasen der Bereitstellung und Konfiguration gehören:

- [Durcharbeiten der optimalen Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-sql-server-performance-best-practices.md)
- [Durcharbeiten der optimalen Verfahren für die Sicherheit für SQL Server auf virtuellen Computern in Azure](virtual-machines-sql-server-security-considerations.md)
- [Einrichten von Konnektivität](virtual-machines-sql-server-connectivity.md)

### Migrieren Ihrer Daten

Nachdem Ihr virtueller SQL Server-Computer eingerichtet wurde und ausgeführt wird, empfiehlt es sich, vorhandene Datenbanken auf den Computer zu migrieren. Es gibt verschiedene Methoden, aber der Bereitstellungs-Assistent in SQL Server Management Studio eignet sich gut für die meisten Szenarien. Eine Erläuterung der Szenarien und ein Tutorial zum Assistenten finden Sie unter [Migrieren einer Datenbank nach SQL Server auf einer Azure-VM](virtual-machines-migrate-onpremises-database.md).

## Hohe Verfügbarkeit

Wenn Sie hohe Verfügbarkeit benötigen, sollten Sie SQL Server-AlwaysOn-Verfügbarkeitsgruppen konfigurieren. Dies beinhaltet mehrere Azure-VMs in einem virtuellen Netzwerk. Das Azure-Vorschauportal bietet eine Vorlage, die diese Konfiguration für Sie einrichtet. Weitere Informationen finden Sie unter [SQL Server AlwaysOn Offering in Microsoft Azure Portal Gallery](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx) (in englischer Sprache).

Wenn Sie Ihre Verfügbarkeitsgruppe und den ihr zugeordneten Listener manuell konfigurieren möchten, lesen Sie die folgenden Artikel:

- [Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen in Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Bereitstellen von SQL Server AlwaysOn mit einer Azure-Ressourcen-Manager-Vorlage](virtual-machines-workload-template-sql-alwayson.md)
- [Erweitern von lokalen AlwaysOn-Verfügbarkeitsgruppen auf Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Weitere Informationen finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Sichern und Wiederherstellen
Bei lokalen Datenbanken kann Azure als sekundäres Datencenter zum Speichern von SQL Server-Sicherungsdateien fungieren. Eine Übersicht der Sicherungs- und Wiederherstellungsoptionen finden Sie unter [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-backup-and-restore.md).

Die [SQL Server-URL-Sicherung](https://msdn.microsoft.com/library/dn435916.aspx) speichert Azure-Sicherungsdateien im Azure-Blob-Speicher. Mithilfe von [SQL Server Managed Backup](https://msdn.microsoft.com/library/dn449496.aspx) können Sie eine Sicherung und Archivierung in Azure planen. Diese Dienste können mit lokalen SQL Server-Instanzen oder SQL Server auf virtuellen Azure-Computern verwendet werden. Azure-VMs können auch [automatisierte Sicherungen](virtual-machines-sql-server-automated-backup.md) und [automatische Anwendung von Patches](virtual-machines-sql-server-automated-patching.md) für SQL Server nutzen.

## Konfigurationsdetails eines Image eines virtuellen Computers mit SQL Server

Die folgenden Tabellen beschreiben die Konfiguration der plattformbezogen bereitgestellten Images eines virtuellen Computers mit SQL Server.

### Windows Server

Die Windows Server-Installation im Plattformimage enthält die folgenden Konfigurationseinstellungen und Komponenten:

|Funktion|Konfiguration
|---|---|
|Remotedesktop|Aktiviert für das Administratorkonto|
|Windows-Update|Aktiviert|
|Benutzerkonten|Standardmäßig ist das bei der Bereitstellung angegebene Benutzerkonto Mitglied der lokalen Gruppe der Administratoren. Dieses Administratorkonto ist auch Mitglied der SQL Server-Serverrolle „sysadmin“.|
|Arbeitsgruppen|Der virtuelle Computer ist Mitglied einer Arbeitsgruppe namens ARBEITSGRUPPE.|
|Gastkonto|Deaktiviert|
|Windows-Firewall mit erweiterter Sicherheit|Andererseits|
|.NET Framework|Version 4|
|Datenträger|Die ausgewählte Größe begrenzt die Anzahl von Datenträgern, die Sie konfigurieren können. Weitere Informationen finden Sie unter [Größen virtueller Computer für Azure](virtual-machines-size-specs.md).|

### SQL Server

Die SQL Server-Installation im Plattformimage enthält die folgenden Konfigurationseinstellungen und Komponenten:

|Funktion|Konfiguration|
|---|---|
|Datenbankmodul|Installiert|
|Analysis Services|Installiert|
|Integration Services|Installiert|
|Reporting Services|Im einheitlichen Modus konfiguriert|
|AlwaysOn-Verfügbarkeitsgruppen|Verfügbar in SQL Server 2012 und höher. Erfordert [zusätzliche Konfigurationsschritte](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)
|Replikation|Installiert|
|Volltext- und semantische Extraktion für Suche|Installiert (semantische Extraktionen nur in SQL Server 2012 oder höher)|
|Data Quality Services|Installiert (nur SQL Server 2012 oder höher)|
|Master Data Services|Installiert (nur SQL Server 2012 oder höher). Erfordert [zusätzliche Konfigurationsschritte und Komponenten](https://msdn.microsoft.com/library/ee633752.aspx)
|PowerPivot für SharePoint|Verfügbar (nur SQL Server 2012 oder höher). Erfordert zusätzliche Konfiguration und Komponenten (einschließlich SharePoint)|
|Distributed Replay Client|Verfügbar (nur SQL Server 2012 oder höher), aber nicht installiert. Weitere Informationen finden Sie unter [Ausführen von SQL Server-Setup aus dem von der Plattform bereitgestellten SQL Server-Image](#run-sql-server-setup-from-the-platform-provided-sql-server-image)|
|Tools|Alle Tools einschließlich SQL Server Management Studio, SQL Server-Konfigurations-Manager, Business Intelligence Development Studio, SQL Server-Setup, Konnektivität der Clienttools, Clienttools SDK und SQL Client Connectivity SDK sowie Upgrade- und Migrationstools, z. B. Datenebenenanwendungen (Data-Tier Application, DAC), Sichern, Wiederherstellen, Anfügen und Trennen|
|SQL Server-Onlinedokumentation|Installiert, erfordert aber Konfiguration mithilfe von Help Viewer|

### Konfiguration des Datenbankmoduls

Die folgenden Datenbankmoduleinstellungen sind konfiguriert. Weitere Einstellungen finden Sie durch Auswerten der Instanz von SQL Server.

|Funktion|Konfiguration|
|---|---|
|Instanz|Enthält eine (unbenannte) Standardinstanz des SQL Server-Datenbankmoduls, die nur auf das Shared Memory-Protokoll lauscht|
|Authentifizierung|Standardmäßig wird für Azure während des Einrichtens von virtuellen Computern mit SQL Server die Windows-Authentifizierung ausgewählt. Wenn Sie die sa-Anmeldung verwenden oder ein neues SQL Server-Konto erstellen möchten, müssen Sie den Authentifizierungsmodus ändern. Weitere Informationen finden Sie unter [Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-security-considerations.md).|
|sysadmin|Der Azure-Benutzer, der den virtuellen Computer installiert hat, ist anfangs das einzige Mitglied der festen SQL Server-Serverrolle „sysadmin“.|
|Arbeitsspeicher|Der Arbeitsspeicher des Datenbankmoduls wird auf dynamische Arbeitsspeicherkonfiguration festgelegt.|
|Authentifizierung der eigenständigen Datenbank|Aus|
|Standardsprache:|Englisch|
|Datenbankübergreifende Besitzverkettung|Aus|

### Programm zur Verbesserung der Benutzerfreundlichkeit (Customer Experience Improvement Program, CEIP)

Das [Programm zur Verbesserung der Benutzerfreundlichkeit](https://technet.microsoft.com/library/cc730757.aspx) (Customer Experience Improvement Program, CEIP) ist aktiviert. Sie können CEIP deaktivieren, indem Sie das SQL Server-Hilfsprogramm „Fehler- und Verwendungsberichterstellung“ verwenden. So starten Sie das Hilfsprogramm „Fehler- und Verwendungsberichterstellung von SQL Server“: Klicken Sie im Startmenü auf „Alle Programme“, klicken Sie auf die Microsoft SQL Server-Version, klicken Sie auf „Konfigurationstools“, und klicken Sie dann auf „Fehler- und Verwendungsberichterstellung von SQL Server“. Wenn Sie keine Instanz von SQL Server verwenden möchten, für die CEIP aktiviert ist, möchten Sie möglicherweise auch Ihr eigenes Image des virtuellen Computers in Azure bereitstellen. Weitere Informationen finden Sie unter [Erstellen und Hochladen einer virtuellen Festplatte, die das Windows Server-Betriebssystem enthält](virtual-machines-create-upload-vhd-windows-server.md).

## Ausführen von SQL Server-Setup aus dem von der Plattform bereitgestellten SQL Server-Image

Wenn Sie einen virtuellen Computer erstellen, indem Sie ein von der Plattform bereitgestelltes SQL Server-Image verwenden, finden Sie die SQL Server-Setup-Medien, die auf dem virtuellen Computer gespeichert sind, im Verzeichnis **C:\\SqlServer\_SQLMajorVersion.SQLMinorVersion\_Full**. Sie können Setup aus diesem Verzeichnis starten, um jegliche Installationsaktionen einschließlich Hinzufügen oder Entfernen von Features, Hinzufügen einer neuen Instanz oder Reparieren der Instanz auszuführen, wenn der Speicherplatz dies zulässt.

>[AZURE.NOTE]Azure stellt auf dem Portal mehrere Versionen der SQL Server-Images bereit. Hat das auf der Plattform bereitgestellte SQL Server-Image das Versionsfreigabedatum 15. Mai 2014 oder später, enthält es standardmäßig den Product Key. Wenn Sie einen virtuellen Computer über ein auf der Plattform bereitgestelltes SQL Server-Image bereitstellen, das vor diesem Datum veröffentlicht wurde, enthält dieser virtuelle Computer den Product Key nicht. Es empfiehlt sich, dass Sie immer die neueste Imageversion auswählen, wenn Sie einen neuen virtuellen Computer bereitstellen.

## Ressourcen

- [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](virtual-machines-provision-sql-server.md)
- [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](virtual-machines-migrate-onpremises-database.md)
- [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)
- [Anwendungsmuster und Entwicklungsstrategien für SQL Server auf Azure Virtual Machines](virtual-machines-sql-server-application-patterns-and-development-strategies.md)
- [Azure Virtual Machines](virtual-machines-about.md) 

<!---HONumber=September15_HO1-->