<properties 
	pageTitle="Übersicht zu SQL Server auf virtuellen Azure-Computern" 
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
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Übersicht zu SQL Server auf virtuellen Azure-Computern

## Übersicht
Sie können [SQL Server auf virtuellen Azure-Computern](http://azure.microsoft.com/services/virtual-machines/sql-server/) in einer Vielzahl von Konfigurationen hosten, die von einem einzelnen Datenbankserver bis hin zu einer Konfiguration mehrerer Computer mit AlwaysOn-Verfügbarkeitsgruppen und einem virtuellen Azure-Netzwerk reichen. Dieses Thema möchte Sie auf einige der besten Ressourcen für den Einstieg in das Ausführen von SQL Server auf virtuellen Azure-Computern aufmerksam machen.

>[AZURE.NOTE]Die Ausführung von SQL Server auf einem virtuellen Azure-Computer stellt eine Möglichkeit zum Speichern von relationalen Daten in Azure dar. Sie können auch den Azure SQL-Datenbankdienst verwenden. Weitere Informationen finden Sie unter [Grundlegendes zur Azure SQL-Datenbank und SQL Server in Azure VMs](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).
 
## Bereitstellen einer SQL Server-Instanz auf einem einzelnen virtuellen Computer

Die einfachste Möglichkeit zum Bereitstellen eines virtuellen SQL Server-Computers in Azure besteht im [Bereitstellen eines Images aus dem SQL Server-Computerkatalog im Azure-Verwaltungsportal](virtual-machines-provision-sql-server.md). Diese Images enthalten in den Preisen für den virtuellen Computer SQL Server-Lizenzen.

Sie können aber auch [einen virtuellen Azure-Computer](virtual-machines-windows-tutorial.md) ohne vorinstallierten SQL Server erstellen. Sie können eine beliebige SQL Server-Instanz darauf installieren, für die Sie über eine Lizenz verfügen.

Zu den häufigen Aufgaben in diesen frühen Phasen der Bereitstellung und Konfiguration gehören:

- [Durcharbeiten der optimalen Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](https://msdn.microsoft.com/library/azure/dn133149.aspx)
- [Durcharbeiten der optimalen Verfahren für die Sicherheit für SQL Server auf virtuellen Computern in Azure](https://msdn.microsoft.com/library/azure/dn133147.aspx)
- [Einrichten von Konnektivität](virtual-machines-sql-server-connectivity.md)

## Bereitstellen einer hochverfügbaren Konfiguration mit mehreren virtuellen Computern

Sie können hohe Verfügbarkeit für SQL Server mithilfe von SQL Server-AlwaysOn-Verfügbarkeitsgruppen erreichen. Dies beinhaltet mehrere Azure-VMs in einem virtuellen Netzwerk. Das Azure-Vorschauportal bietet eine Vorlage, die diese Konfiguration für Sie einrichtet. Weitere Informationen finden Sie unter [SQL Server AlwaysOn Offering in Microsoft Azure Portal Gallery](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx) (in englischer Sprache).

Wenn Sie Ihre Verfügbarkeitsgruppe und den ihr zugeordneten Listener manuell konfigurieren möchten, lesen Sie die folgenden Artikel:

- [Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen in Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Erweitern von lokalen AlwaysOn-Verfügbarkeitsgruppen auf Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Weitere Informationen finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Ausführen von Business Intelligence, Data Warehousing und OLTP-Arbeitslasten in Azure   
Sie können häufig verwendete SQL Server-Arbeitslasten auf virtuellen Azure-Computern ausführen. SQL Server bietet einige optimierte VM-Images im Katalog. Dazu gehören Images für:

- [Business Intelligence](https://msdn.microsoft.com/library/azure/jj992719.aspx)
- [Data Warehousing](https://msdn.microsoft.com/library/azure/dn387396.aspx)
- [OLTP](https://msdn.microsoft.com/library/azure/dn387396.aspx)

## Migrieren Ihrer Daten

Nachdem Ihr virtueller SQL Server-Computer eingerichtet wurde und ausgeführt wird, empfiehlt es sich, vorhandene Datenbanken auf den Computer zu migrieren. Es gibt verschiedene Methoden, aber der Bereitstellungs-Assistent in SQL Server Management Studio eignet sich gut für die meisten Szenarien. Eine Erläuterung der Szenarien und ein Lernprogramm für den Assistenten finden Sie unter [Migrieren einer Datenbank nach SQL Server auf einer Azure-VM](virtual-machines-migrate-onpremises-database.md).

## Sichern und Wiederherstellen
Bei lokalen Datenbanken kann Azure als sekundäres Datencenter zum Speichern von SQL Server-Sicherungsdateien fungieren. Eine Übersicht der Sicherungs- und Wiederherstellungsoptionen finden Sie unter [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-backup-and-restore.md).

Die [SQL Server-URL-Sicherung](https://msdn.microsoft.com/library/dn435916.aspx) speichert Azure-Backupdateien im Azure-Blob-Speicher. Mithilfe von [SQL Server Managed Backup](https://msdn.microsoft.com/library/dn449496.aspx) können Sie eine Sicherung und Archivierung in Azure planen. Diese Dienste können mit lokalen SQL Server-Instanzen oder SQL Server auf virtuellen Azure-Computern verwendet werden. Azure-VMs können auch [automatisierte Sicherungen](virtual-machines-sql-server-automated-backup.md) und [automatische Anwendung von Patches](virtual-machines-sql-server-automated-patching.md) für SQL Server nutzen.

<!---HONumber=August15_HO8-->