<properties 
	pageTitle="SQL Server auf virtuellen Azure-Computern" 
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
	ms.date="04/17/2015"
	ms.author="jroth"/>

# SQL Server auf virtuellen Azure-Computern

## Übersicht
Sie können [SQL Server auf virtuellen Azure-Computern][sqlvmlanding] in einer Vielzahl von Konfigurationen hosten, die von einem einzelnen Datenbankserver bis hin zu einer Konfiguration mehrerer Computer mit AlwaysOn-Verfügbarkeitsgruppen und einem virtuellen Azure-Netzwerk reichen.

> [AZURE.NOTE]Die Ausführung von SQL Server auf einem virtuellen Azure-Computer stellt eine Möglichkeit zum Speichern von relationalen Daten in Azure dar. Sie können auch den Azure SQL-Datenbankdienst verwenden. Weitere Informationen finden Sie unter [Grundlegendes zur Azure SQL-Datenbank und SQL Server in Azure VMs][sqldbcompared].
 
## Bereitstellen einer SQL Server-Instanz auf einem einzelnen virtuellen Computer
Sobald Sie [einen virtuellen Azure-Computer mithilfe des Azure-Verwaltungsportals][createvmportal] (in englischer Sprache) oder eine Automatisierung erstellt haben, installieren Sie eine beliebige SQL Server-Instanz, für die Sie eine Lizenz besitzen. Sie müssen jedoch zusätzliche Schritte zum [Einrichten der Verbindung][setupconnectivity] zwischen dem SQL Server-Computer und anderen Clientcomputern ausführen.
 
Sie können auch eines der vielen verschiedenen Images virtueller SQL Server-Computer aus dem Katalog installieren. Diese Images enthalten in den Preisen für den virtuellen Computer SQL Server-Lizenzen. Weitere Informationen finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure][provisionsqlvm].

## Bereitstellen einer hochverfügbaren Konfiguration mit mehreren virtuellen Computern
Sie können hohe Verfügbarkeit für SQL Server mithilfe von SQL Server-AlwaysOn-Verfügbarkeitsgruppen erreichen. Dies beinhaltet mehrere Azure-VMs in einem virtuellen Netzwerk. Das Azure-Vorschauportal bietet eine Vorlage, die diese Konfiguration für Sie einrichtet. Weitere Informationen finden Sie unter [SQL Server AlwaysOn Offering in Microsoft Azure Portal Gallery][sqlalwaysonportal] (in englischer Sprache). Sie können [eine AlwaysOn-Verfügbarkeitsgruppe auch manuell konfigurieren][sqlalwaysonmanual]. Weitere Informationen finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern][sqlhadr].

## Ausführen von Business Intelligence, Data Warehousing und OLTP-Arbeitslasten in Azure   
Sie können häufig verwendete SQL Server-Arbeitslasten auf virtuellen Azure-Computern ausführen. SQL Server bietet einige optimierte VM-Images im Katalog. Dazu gehören Images für [Business Intelligence][sqlbi], [Data Warehousing][sqldw] und [OLTP][sqloltp].

## Migrieren Ihrer Daten
Es gibt mehrere Möglichkeiten zum Migrieren der Daten zu virtuellen Azure-Computern, auf denen SQL Server ausgeführt wird. Stellen Sie zuerst über das Azure-Verwaltungsportal, PowerShell-Automatisierung oder den Bereitstellungs-Assistenten von SQL Server Management Studio einen virtuellen SQL Server-Computer bereit. Optimierte SQL Server-Images enthalten Lizenzen im Preismodell. Sie können jedoch auch SQL Server mit eigener Lizenz installieren. Zum Migrieren der Daten stehen Ihnen mehrere Optionen zur Verfügung, z. B. mithilfe des Bereitstellungs-Assistenten oder durch die Migration eines Datenträgers zum virtuellen Zielcomputer. Weitere Informationen finden Sie unter [Bereit für die Migration zu SQL Server auf Azure-Virtuellen Computern][migratesql].

## Sichern und Wiederherstellen
Bei lokalen Datenbanken kann Azure als sekundäres Datencenter zum Speichern von SQL Server-Sicherungsdateien fungieren. Die [SQL Server-URL-Sicherung][backupurl] speichert Azure-Backupdateien im Azure-Blob-Speicher. Mithilfe von [SQL Server Managed Backup][managedbackup] können Sie eine Sicherung und Archivierung in Azure planen. Diese Dienste können mit lokalen SQL Server-Instanzen oder SQL Server auf virtuellen Azure-Computern verwendet werden. Azure-VMs können auch [automatisierte Sicherungen][autobackup] und [automatische Anwendung von Patches][autopatching] für SQL Server nutzen. Weitere Informationen finden Sie unter [Verwaltungsaufgaben für SQL Server auf virtuellen Azure-Computern][managementtasks].

## Zusätzliche Ressourcen:
[SQL Server auf virtuellen Azure-Computern][sqlmsdnlanding]

[Erste Schritte mit SQL Server auf virtuellen Azure-Computern][sqlvmgetstarted]

[Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure][sqlperf]

[Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern][sqlsecurity]

[Technische Artikel zu SQL Server auf virtuellen Azure-Computern][technicalarticles]

  [sqlvmlanding]: http://azure.microsoft.com/services/virtual-machines/sql-server/
  [sqldbcompared]: http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas
  [createvmportal]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
  [setupconnectivity]: https://msdn.microsoft.com/library/azure/dn133152.aspx
  [provisionsqlvm]: http://azure.microsoft.com/documentation/articles/virtual-machines-provision-sql-server/
  [sqlalwaysonportal]: http://go.microsoft.com/fwlink/?LinkId=526941
  [sqlalwaysonmanual]: https://msdn.microsoft.com/library/azure/dn249504.aspx
  [sqlhadr]: https://msdn.microsoft.com/library/azure/jj870962.aspx
  [sqlbi]: https://msdn.microsoft.com/library/azure/jj992719.aspx
  [sqldw]: https://msdn.microsoft.com/library/azure/dn387396.aspx
  [sqloltp]: https://msdn.microsoft.com/library/azure/eb0188e2-5569-48ff-b92c-1f6c0bf79620#about
  [migratesql]: https://msdn.microsoft.com/library/azure/dn133142.aspx
  [backupurl]: https://msdn.microsoft.com/library/dn435916(v=sql.120).aspx
  [managedbackup]: https://msdn.microsoft.com/library/dn449496.aspx
  [autobackup]: https://msdn.microsoft.com/library/azure/dn906091.aspx
  [autopatching]: https://msdn.microsoft.com/library/azure/dn961166.aspx
  [managementtasks]: https://msdn.microsoft.com/library/azure/dn906886.aspx
  [sqlmsdnlanding]: https://msdn.microsoft.com/library/azure/jj823132.aspx
  [sqlvmgetstarted]: https://msdn.microsoft.com/library/azure/dn133151.aspx
  [sqlperf]: https://msdn.microsoft.com/library/azure/dn133149.aspx
  [sqlsecurity]: https://msdn.microsoft.com/library/azure/dn133147.aspx
  [technicalarticles]: https://msdn.microsoft.com/library/azure/dn248435.aspx

<!---HONumber=July15_HO2-->