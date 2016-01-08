<properties
	pageTitle="Sicherung und Wiederherstellung für SQLServer | Microsoft Azure"
	description="Erläutert Überlegungen zur Sicherung und Wiederherstellung für SQL Server-Datenbanken auf virtuellen Azure-Computern."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/13/2015"
	ms.author="jroth" />

# Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern

## Übersicht

Das Sichern von Daten in SQL Server-Datenbanken ist ein wichtiger Bestandteil der Strategie zum Schutz vor Datenverlust aufgrund von Anwendungs- oder Benutzerfehlern. Dies gilt in gleichem Maße auch für SQL Server auf virtuellen Azure-Computern (VMs).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.

Für SQL Server auf Azure-VMs können Sie systemeigene Sicherungs- und Wiederherstellungsverfahren mit angefügten Datenträgern als Ziel der Sicherungsdateien verwenden. Die Anzahl von Datenträgern, die an einen virtuellen Azure-Computer angefügt werden können, ist allerdings je nach [Größe des virtuellen Computers](virtual-machines-size-specs.md) begrenzt. Zudem muss der Mehraufwand für die Datenträgerverwaltung berücksichtigt werden.

Ab SQL Server 2014 können Sie Microsoft Azure-Blob-Speicher für die Sicherung und Wiederherstellung verwenden. SQL Server 2016 bietet auch Erweiterungen für diese Option. Darüber hinaus bietet SQL Server 2016 für in Microsoft Azure-Blob-Speicher gespeicherte Datenbankdateien eine Option für nahezu sofortige Backups und schnelle Wiederherstellungen mithilfe von Azure-Momentaufnahmen. Dieser Artikel bietet eine Übersicht über diese Optionen. Weitere Informationen finden Sie unter [SQL Server-Sicherung und -Wiederherstellung mit dem Microsoft Azure-Blob-Speicherdienst](https://msdn.microsoft.com/library/jj919148(v=sql.130).aspx).

>[AZURE.NOTE]Eine Beschreibung der Optionen zum Sichern sehr großer Datenbanken finden Sie im Blogbeitrag zu den [Sicherungsstrategien für mehrere Terabyte große SQL Server-Datenbanken für virtuelle Azure-Computer](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).

Die folgenden Abschnitte enthalten Informationen zu den verschiedenen Versionen von SQL Server, die auf einem virtuellen Azure-Computer unterstützt werden.

## Überlegungen zur Sicherung von im Microsoft Azure-Blob-Dienst gespeicherten Datenbankdateien

Wenn Ihre Datenbankdateien in Microsoft Azure-Blob-Speicher gespeichert werden, gelten andere Gründe für die Durchführung von Datenbanksicherungen, und auch die zugrunde liegende Sicherungstechnologie selbst ändert sich. Weitere Informationen zum Speichern von Datenbankdateien in Azure-Blob-Speicher finden Sie unter [SQL Server-Datendateien in Azure](https://msdn.microsoft.com/library/jj919148.aspx).

- Sie müssen keine Datenbanksicherungen mehr durchführen, um Ihre Daten vor Hardwarefehlern oder Fehlern auf Speichermedien zu schützen, da Microsoft Azure diesen Schutz im Rahmen des Microsoft Azure-Diensts bereitstellt.

- Sie müssen jedoch weiterhin Datenbanksicherungen zum Schutz vor Benutzerfehlern oder zu Archivierungszwecken, aus gesetzlichen Gründen oder zu administrativen Zwecken durchführen.

- Die SQL Server-Funktion für die Sicherung anhand von Dateimomentaufnahmen in Microsoft SQL Server 2016 Community Technology Preview 3 (CTP3) ermöglicht nahezu sofortige Sicherungen und schnelle Wiederherstellungen. Weitere Informationen finden Sie im Artikel zu [Dateimomentaufnahme-Sicherungen für Datenbankdateien in Azure](https://msdn.microsoft.com/library/mt169363.aspx).

## Sichern und Wiederherstellen in Microsoft SQL Server 2016 Community Technology Preview 3 (CTP3)

Microsoft SQL Server 2016 Community Technology Preview 3 (CTP3) unterstützt die im Folgenden beschriebenen Funktionen zum [Sichern und Wiederherstellen mit Azure-Blobs](https://msdn.microsoft.com/library/jj919148.aspx) von SQL Server 2014. Die Version enthält aber auch die folgenden Erweiterungen:

- **Striping**: Für Sicherungen in Microsoft Azure-Blob-Speicher unterstützt SQL Server 2016 das Sichern in mehreren Blobs, um die Sicherung großer Datenbanken mit bis zu 12,8 TB zu ermöglichen.

- **Momentaufnahmesicherung**: Durch die Verwendung von Azure-Momentaufnahmen ermöglicht die Dateimomentaufnahme-Sicherung von SQL Server nahezu sofortige Backups und schnelle Wiederherstellungen für Datenbankdateien, die mit dem Azure-Blob-Speicherdienst gespeichert werden. Sicherungs- und Wiederherstellungsrichtlinien lassen sich dank dieser Funktion vereinfachen. Die Dateimomentaufnahme-Sicherung unterstützt auch Point-in-Time-Wiederherstellungen. Weitere Informationen finden Sie im Artikel zu [Momentaufnahmesicherungen für Datenbankdateien in Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx).

- **Verwaltete Sicherungsplanung**: SQL Server Managed Backup für Azure unterstützt jetzt benutzerdefinierte Zeitpläne. Weitere Informationen finden Sie unter [SQL Server Managed Backup für Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).

>[AZURE.NOTE]Eine praxisnahe Einführung in die Funktionen von SQL Server 2016 für Azure-Blob-Speicher bietet das [Lernprogramm zur Verwendung des Microsoft Azure-Blob-Speicherdiensts mit SQL Server 2016-Datenbanken](https://msdn.microsoft.com/library/dn466438.aspx).

## Sicherung und Wiederherstellung in SQL Server 2014

SQL Server 2014 enthält die folgenden Erweiterungen:

1. **Sichern und Wiederherstellen in Azure**:

 - *SQL Server-URL-Sicherung* wird jetzt in SQL Server Management Studio unterstützt. Die Option zum Sichern in Azure ist jetzt bei der Verwendung der Aufgabe „Sichern oder Wiederherstellen“ oder des Wartungsplanungs-Assistenten in SQL Server Management Studio verfügbar. Weitere Informationen finden Sie unter [SQL Server-URL-Sicherung](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
 - *SQL Server Managed Backup für Azure* verfügt über neue Funktionen, die eine automatisierte Sicherungsverwaltung ermöglichen. Diese Funktionen erleichtern insbesondere die Automatisierung der Sicherungsverwaltung für SQL Server 2014-Instanzen auf einem Azure-Computer. Weitere Informationen finden Sie unter [SQL Server Managed Backup für Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
 - *Automatisierte Sicherung* bietet zusätzliche Automatisierungsfunktionen, mit denen *SQL Server Managed Backup für Azure* automatisch für alle vorhandenen und neuen Datenbanken für einen virtuellen SQL Server-Computer in Azure aktiviert werden kann. Weitere Informationen finden Sie unter [Automatisierte Sicherung für SQL Server in Azure Virtual Machines](virtual-machines-sql-server-automated-backup.md).
 - Eine Übersicht über alle Optionen für die SQL Server 2014-Sicherung in Azure finden Sie unter [SQL Server-Sicherung und -Wiederherstellung mit dem Microsoft Azure-Blob-Speicherdienst](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).

1. **Verschlüsselung**: SQL Server 2014 unterstützt das Verschlüsseln von Daten beim Erstellen einer Sicherung. Es werden mehrere Verschlüsselungsalgorithmen und die Verwendung eines Zertifikats oder asymmetrischen Schlüssels unterstützt. Weitere Informationen finden Sie unter [Sicherungsverschlüsselung](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## Sicherung und Wiederherstellung in SQL Server 2012

Ausführliche Informationen zur SQL Server-Sicherung und -Wiederherstellung in SQL Server 2012 finden Sie unter [Sichern und Wiederherstellen von SQL Server-Datenbanken (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

Ab SQL Server 2012 SP1, kumulatives Update 2, ist die Sicherung und Wiederherstellung im bzw. aus dem Azure-Blob-Speicherdienst möglich. Diese Erweiterung kann verwendet werden, um SQL Server-Datenbanken in einer SQL Server-Instanz auf einem virtuellen Azure-Computer oder einer lokalen Instanz zu sichern. Weitere Informationen finden Sie unter [SQL Server-Sicherung und -Wiederherstellung mit dem Microsoft Azure-Blob-Speicherdienst](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Zu den Vorteilen des Azure-Blob-Speicherdiensts zählen die Möglichkeit, die Beschränkung auf 16 angefügte Datenträger zu umgehen, die einfache Verwaltung und die direkte Verfügbarkeit der Sicherungsdatei für eine andere SQL Server-Instanz auf einem virtuellen Azure-Computer oder eine lokale Instanz zur Migration oder Notfallwiederherstellung. Eine vollständige Liste der Vorteile, die die Verwendung des Azure-Blob-Speicherdiensts für SQL Server-Sicherungen bietet, finden Sie im Abschnitt *Vorteile* unter [SQL Server-Sicherung und -Wiederherstellung mit dem Azure-Blob-Speicherdienst](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Bewährte Methoden, Empfehlungen und Informationen zur Problembehandlung finden Sie unter [Bewährte Methoden für die Sicherung und Wiederherstellung (Azure-Blob-Speicherdienst)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## Sicherung und Wiederherstellung in anderen unterstützten SQL Server-Versionen auf virtuellen Azure-Computern

Informationen zur SQL Server-Sicherung und -Wiederherstellung in SQL Server 2008 R2 finden Sie unter [Sichern und Wiederherstellen von Datenbanken in SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Informationen zur SQL Server-Sicherung und -Wiederherstellung in SQL Server 2008 finden Sie unter [Sichern und Wiederherstellen von Datenbanken in SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## Nächste Schritte

Wenn Sie noch an der Planung Ihrer Bereitstellung von SQL Server auf einem virtuellen Azure-Computer arbeiten, finden Sie im folgenden Tutorial Informationen zur Bereitstellung: [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-provision-sql-server.md).

Obwohl Sie Ihre Daten durch Sicherung und Wiederherstellung migrieren können, sind möglicherweise einfachere Migrationspfade für SQL Server auf einer Azure-VM verfügbar. Eine vollständige Erläuterung der Migrationsoptionen und Empfehlungen finden Sie unter [Migration einer Datenbank zu SQL Server auf einem virtuellen Azure-Computer](virtual-machines-migrate-onpremises-database.md).

Lesen Sie auch die weiteren [Ressourcen für die Ausführung von SQL Server in Azure Virtual Machines](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Nov15_HO4-->