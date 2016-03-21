<properties
	pageTitle="Referenz zu Pools für elastische Datenbanken für SQL-Datenbank | Microsoft Azure" 
	description="Diese Referenz enthält Details und Links zu Artikeln über Pools für elastische Datenbanken sowie Informationen zur Programmierbarkeit."
	keywords="eDTU"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/09/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# SQL-Datenbank – Pool für elastische Datenbanken

Für SaaS-Entwickler, die Dutzende, Hunderte oder sogar Tausende von Datenbanken verwenden, vereinfacht ein [Pool für elastische Datenbanken](sql-database-elastic-pool.md) das Erstellen, Warten und Verwalten der Leistung und Kosten der gesamten Datenbankgruppe.

## Voraussetzungen für das Erstellen und Verwalten von Pools für elastische Datenbanken

- Pools für elastische Datenbanken sind nur auf Servern in Azure SQL-Datenbank V12 verfügbar. Informationen zum Upgraden auf V12 und zum Migrieren Ihrer Datenbanken direkt in einen Pool finden Sie unter [Upgrade auf Azure SQL-Datenbank V12](sql-database-upgrade-server-powershell.md).
- Das Erstellen und Verwalten von Pools für elastische Datenbanken wird nur vom [Azure-Portal](https://portal.azure.com), von [PowerShell](sql-database-elastic-pool-powershell.md) und von einer .NET-Clientbibliothek (nur Azure-Ressourcen-Manager) unterstützt. Das [klassische Portal](https://manage.windowsazure.com/) und Dienstverwaltungsbefehle werden nicht unterstützt.
- Darüber hinaus wird das Erstellen neuer elastischer Datenbanken und das Verschieben vorhandener Datenbanken in und aus Pools für elastische Datenbanken mit [Transact-SQL](#transact-sql) unterstützt.


## Liste der Artikel

Die folgenden Artikel helfen Ihnen beim Einstieg in elastische Datenbanken und elastische Aufträge:

| Artikel | Beschreibung |
| :-- | :-- |
| [Pools für elastische Datenbanken in Azure SQL-Datenbanken](sql-database-elastic-pool.md) | Übersicht zu Pools für elastische Datenbanken |
| [Überlegungen zum Preis und zur Leistung](sql-database-elastic-pool-guidance.md) | Informationen zum Bewerten, ob der Einsatz eines Pools für elastische Datenbanken wirtschaftlich ist |
| [Erstellen und Verwalten von Pools für elastische Datenbanken mit dem Azure-Portal](sql-database-elastic-pool-portal.md) | Informationen zum Erstellen und Verwalten eines Pools für elastische Datenbanken im Azure-Portal |
| [Erstellen und Verwalten eines Pools für elastische SQL-Datenbanken mit PowerShell](sql-database-elastic-pool-powershell.md) | Erstellen und Verwalten eines Pools für elastische Datenbanken mit PowerShell-Cmdlets |
| [Erstellen und Verwalten einer SQL-Datenbank mit der Azure SQL-Datenbankbibliothek für .NET](sql-database-elastic-pool-csharp.md) | Informationen zum Erstellen und Verwalten eines Pools für elastische Datenbanken mit C# |
| [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md) | Eine Übersicht über den Dienst für elastische Aufträge, der die Ausführung von T-SQL-Skripts in allen elastischen Datenbanken in einem Pool ermöglicht |
| [Installieren der Komponente für elastische Datenbankaufträge](sql-database-elastic-jobs-service-installation.md) | Installieren des Diensts für elastische Datenbankaufträge |
| [Sichern der SQL-Datenbank](sql-database-security.md) | Um ein Skript für elastische Datenbankaufträge ausführen zu können, muss zu jeder Datenbank im Pool ein Benutzer mit den entsprechenden Berechtigungen hinzugefügt werden. |
| [Deinstallieren der Komponenten für elastische Datenbankaufträge](sql-database-elastic-jobs-uninstall.md) | Wiederherstellung nach Installationsfehlern des Diensts für elastische Datenbankaufträge |



## Namespace- und Endpunktdetails
Ein Pools für elastische Datenbanken ist eine Azure-Ressourcen-Manager-Ressource vom Typ "ElasticPool" in der Microsoft Azure SQL-Datenbank.

- **Namespace**: Microsoft.Sql/ElasticPool
- **management-endpoint** für REST-API-Aufrufe (Ressourcen-Manager): https://management.azure.com



## Eigenschaften von Pools für elastische Datenbanken

| Eigenschaft | Beschreibung |
| :-- | :-- |
| creationDate | Datum, an dem der Pool erstellt wurde. |
| databaseDtuMax | Maximale Anzahl von eDTUs, die von einer einzelnen Datenbank im Pool verwendet werden können. Der Datenbank-eDTU-Maximalwert stellt kein Ressourcengarantie dar. Der eDTU-Maximalwert gilt für alle Datenbanken im Pool. |
| databaseDtuMin | Minimale Anzahl von eDTUs, die einer einzelnen Datenbank im Pool garantiert werden können. Der Datenbank-eDTU-Mindestwert kann auf 0 festgelegt werden. Der eDTU-Mindestwert gilt für alle Datenbanken im Pool. Beachten Sie, dass das Produkt aus Anzahl von Datenbanken im Pool und Datenbank-eDTU-Mindestwert nicht die tatsächliche Anzahl der eDTUs im Pool übersteigen darf. |
| Dtu | Anzahl der eDTUs, die von allen Datenbanken im Pool gemeinsam verwendet werden. |
| edition | Die Dienstebene des Pools. Jede Datenbank im Pool hat diese Edition. |
| elasticPoolId | Die GUID der Instanz des Pools. |
| elasticPoolName | Der Name des Pools. Der Name ist eindeutig in Bezug auf den übergeordneten Server. |
| location | Speicherort im Rechenzentrum, in dem der Pool erstellt wurde. |
| state | Der Status ist "Deaktiviert" bei Zahlungsrückstand für das Abonnement und andernfalls "Bereit". |
| storageMB | Die Speicherbeschränkung für den Pool in MB. Die Summe von alle Datenbanken im Pool verwendete Speicher darf diesen Poolgrenzwert nicht überschreiten. |


## eDTUs und Speicherbeschränkungen für elastische Pools und elastische Datenbanken


[AZURE.INCLUDE [Tabelle der SQL-Datenbank-Dienstebenen für elastische Datenbanken](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]



## Einschränkungen für Azure-Ressourcen-Manager:

Azure SQL-Datenbank V12-Server befinden sich in Ressourcengruppen.

- Jede Ressourcengruppe kann maximal 800 Server enthalten.
- Jeder Server kann über maximal 800 elastische Pools verfügen.


## Latenzzeit der elastischen Poolvorgänge

- Änderungen der garantierten eDTUs pro Datenbank ("databaseDtuMin") oder der maximalen eDTUs pro Datenbank ("databaseDtuMax") werden i. d. R. innerhalb von fünf Minuten oder weniger abgeschlossen.
- Änderungen der eDTU/Speicherbegrenzungen ("storageMB") des Pools hängen von der Gesamtmenge des Speicherplatzes aller Datenbanken im Pool ab. Änderungen dauern durchschnittlich 90 Minuten oder weniger pro 100 GB. Wenn beispielsweise der gesamte, von allen Datenbanken im Pool verwendete Speicherplatz 200 GB beträgt, ist für die Änderung der Pool-eDTU/Speicherbegrenzung eine Latenzzeit von drei Stunden oder weniger zu erwarten.



## PowerShell, REST-API und die Clientbibliothek für .NET

Zum Erstellen und Verwalten elastischer Pools stehen verschiedene PowerShell-Cmdlets und REST-API-Befehle zur Verfügung: Ausführliche Informationen und Codebeispiele finden Sie unter [Erstellen und Verwalten eines Pool für elastische SQL-Datenbanken mit PowerShell](sql-database-elastic-pool-powershell.md) und [Erstellen und Verwalten von SQL-Datenbanken mit C#](sql-database-client-library.md).


| [PowerShell-Cmdlets](https://msdn.microsoft.com/library/azure/mt574084.aspx) | [REST-API-Befehle](https://msdn.microsoft.com/library/mt163571.aspx) |
| :-- | :-- |
| [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) | [Erstellen eines elastischen Datenbankpools](https://msdn.microsoft.com/library/mt163596.aspx) |
| [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) | [Festlegen der Leistungseinstellungen eines Pools für elastische Datenbanken](https://msdn.microsoft.com/library/mt163641.aspx) |
| [Remove-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619355.aspx) | [Löschen eines Pools für elastische Datenbanken](https://msdn.microsoft.com/library/mt163672.aspx) |
| [Get-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603517.aspx) | [Ruft die Pools für elastische Datenbanken und ihre Eigenschaftswerte ab.](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) | [Abrufen des Status der Vorgänge für Pools für elastische Datenbanken](https://msdn.microsoft.com/library/mt163669.aspx) |
| [Get-AzureRmSqlElasticPoolDatabase](https://msdn.microsoft.com/library/azure/mt619484.aspx) | [Abrufen der Datenbanken in einem Pool für elastische Datenbanken](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolDatabaseActivity]() | [Ruft den Status beim Verschieben von Datenbanken in und aus einem Pool ab.](https://msdn.microsoft.com/library/mt163669.aspx) |

## Transact-SQL

Mithilfe von Transact-SQL können Sie die folgenden Verwaltungsaufgaben für elastische Datenbanken ausführen:

| Aufgabe | Details |
| :-- | :-- |
| Erstellen einer neuen elastischen Datenbank direkt in einem Pool | [CREATE DATABASE (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn268335.aspx) |
| Verschieben vorhandener Datenbanken in und aus einem Pool | [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) |
| Abrufen der Ressourcennutzungsstatistik eines Pools | [sys.elastic\_pool\_resource\_stats (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/mt280062.aspx) |


## Abrechnungs- und Preisinformationen

Pools für elastische Datenbanken werden anhand der folgenden Merkmale abgerechnet:

- Ein elastischer Pool wird bei seiner Erstellung abgerechnet, auch wenn keine Datenbanken im Pool vorhanden sind.
- Ein elastischer Pool wird stündlich abgerechnet. Dies ist die gleiche Häufigkeit wie für Leistungsstufen von Einzeldatenbanken.
- Wenn ein elastischer Pool auf die Größe einer neuen Anzahl von eDTUs geändert wird, wird der Pool erst dann gemäß den neuen eDTUs berechnet, wenn die Größenänderung abgeschlossen ist. Dies erfolgt nach dem gleichen Muster wie beim Ändern der Leistungsstufe von eigenständigen Datenbanken.


- Der Preis für einen elastischen Pool basiert auf der Anzahl von eDTUs des Pools. Der Preis eines elastischen Pools hängt nicht von der Auslastung der darin enthaltenen elastischen Datenbanken ab.
- Der Preis wird wie folgt berechnet: (Anzahl von Pool-eDTUs) x (Einzelpreis pro eDTU).

Der eDTU-Einzelpreis für einen elastischen Anwendungspool ist höher als der DTU-Stückpreis für eine eigenständige Datenbank derselben Dienstebene. Weitere Informationen finden Sie unter [SQL-Datenbank Preise](https://azure.microsoft.com/pricing/details/sql-database/).

## Fehler in Pool für elastische Datenbanken

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX\_RESOURCE | Der Speichergrenzwert des elastischen Pools wurde erreicht. Die Speicherauslastung für der elastischen Pool darf (%d) MB nicht überschreiten. | Speicherplatzbeschränkung des elastischen Pools in MB. | Es wurde versucht, Daten in eine Datenbank zu schreiben, während die Speicherbegrenzung des elastischen Pools erreicht wurde. | Erhöhen Sie nach Möglichkeit die DTUs des elastischen Pools, um die Speicherkapazität zu erweitern, reduzieren Sie die Speichernutzung der einzelnen Datenbanken innerhalb des elastischen Pools, oder entfernen Sie Datenbanken aus dem elastischen Pool. |
| 10929 | EX\_USER | Die %s-Mindestgarantie beträgt %d, der maximale Wert beträgt %d und die aktuelle Nutzung für die Datenbank beträgt %d. Der Server ist jedoch derzeit zu stark ausgelastet, um Anforderungen über %d für diese Datenbank zu unterstützen. Weitere Informationen dazu finden Sie unter [Ressourcenkontrolle für Azure SQL-Datenbank](http://go.microsoft.com/fwlink/?LinkId=267637). Bitte versuchen Sie es andernfalls später noch einmal. | DTU-Minimum pro Datenbank; DTU-Maximum pro Datenbank | Die Gesamtanzahl der gleichzeitigen Worker (Anforderungen) in allen Datenbanken im elastischen Pool hat versucht, den Poolgrenzwert zu überschreiten. | Bitte erhöhen Sie nach Möglichkeit die DTU-Anzahl des elastischen Pools, um die Begrenzung für Worker zu steigern, oder entfernen Sie Datenbanken aus dem elastischen Pool. |
| 40844 | EX\_USER | Datenbank "%Ls" auf Server "%ls" ist eine Datenbank der "%ls"-Edition in einem elastischen Pool und kann nicht über eine fortlaufende Kopierbeziehung verfügen. | Datenbankname, Datenbank-Edition, Servername | Ein StartDatabaseCopy-Befehl wurde für eine Nicht-Premium-Datenbank in einem elastischen Pool ausgegeben. | In Kürze verfügbar |
| 40857 | EX\_USER | Der elastische Pool wurde für folgenden Server nicht gefunden: "%ls", Name des elastischen Pools: "%ls". | Name des Servers; Name des elastisches Pools | Der angegebene elastische Pool ist nicht auf dem angegebenen Server vorhanden. | Geben Sie einen gültigen Namen für den elastischen Pool an. |
| 40858 | EX\_USER | Der elastische Pool "%ls" ist bereits auf folgendem Server vorhanden: "%ls" | Name des elastischen Pools, Servername | Der angegebene elastische Pool existiert bereits auf dem angegebenen logischen Server. | Geben Sie einen neuen Namen für den elastischen Pool an. |
| 40859 | EX\_USER | Der elastische Pool unterstützt Dienstebene "%ls" nicht. | Dienstebene des elastischen Pools | Die angegebene Dienstebene wird für die Bereitstellung elastischer Pools nicht unterstützt. | Geben Sie die richtige Edition an, oder lassen Sie die Dienstebene leer, um die Standarddienstebene zu verwenden. |
| 40860 | EX\_USER | Die Kombination aus elastischem Pool "%ls" und Dienstziel "%ls" ist ungültig. | Name des elastischen Pools; Name des Dienstebenenziels | Der elastische Pool und das Dienstziel können gemeinsam angegeben werden, wenn das Dienstziel als "ElasticPool" angegeben wird. | Geben Sie die richtige Kombination aus elastischem Pool und Dienstziel an. |
| 40861 | EX\_USER | Die Datenbankedition "%.*ls" darf sich nicht von der Dienstebene des elastischen Pools ("%.*ls") unterscheiden. | Datenbankedition, Dienstebene des elastischen Pools | Die Datenbankedition unterscheidet sich von der Dienstebene des elastischen Pools. | Geben Sie keine Datenbankedition an, die sich von der Dienstebene des elastischen Pools unterscheidet. Beachten Sie, dass die Datenbankedition nicht angegeben werden muss. | | 40862 | EX\_USER | Der Name des elastischen Pools muss angegeben werden, wenn das Dienstziel des elastischen Pools angegeben wurde. | Keine | Das Dienstziel des elastischen Pools identifiziert einen elastischen Pool nicht eindeutig. | Geben Sie den Namen des elastischen Pools an, wenn Sie das Dienstziel des elastischen Pools verwenden. | | 40864 | EX\_USER | Die DTU-Anzahl für den elastischen Pool muss mindestens (%d) DTUs für die Dienstebene "%.*ls" betragen. | DTUs für elastischen Pool; Dienstebene des elastischen Pools. | Es wurde versucht, eine DTU-Anzahl für den elastischen Pool unterhalb des unteren Grenzwerts festzulegen. | Legen Sie die DTU-Einstellung für den elastischen Pool mindestens auf die Untergrenze fest. | | 40865 | EX\_USER |Die DTU-Anzahl für den elastischen Pool darf höchstens (%d) DTUs für die Dienstebene "%.*ls" betragen. | DTUs für elastischen Pool; Dienstebene des elastischen Pools. | Es wurde versucht, eine DTU-Anzahl für den elastischen Pool oberhalb des oberen Grenzwerts festzulegen. | Legen Sie die DTU-Einstellung für den elastischen Pool höchstens auf die Obergrenze fest. | | 40867 | EX\_USER | Die maximalen DTUs pro Datenbank müssen mindestens (%d) für Dienstebene "%.*ls" betragen. | Maximale DTUs pro Datenbank; Dienstebene des elastischen Pools | Es wurde versucht, eine maximale DTU-Anzahl pro Datenbank festzulegen, die unter der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des elastischen Pools zu verwenden, die die gewünschte Einstellung unterstützt. | | 40868 | EX\_USER | Die maximale DTU-Anzahl pro Datenbank kann nicht mehr als (%d) für Dienstebene "%.*ls" betragen. | Maximale DTUs pro Datenbank; Dienstebene des elastischen Pools. | Es wurde versucht, eine maximale DTU-Anzahl pro Datenbank festzulegen, die über der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des elastischen Pools zu verwenden, die die gewünschte Einstellung unterstützt. | | 40870 | EX\_USER | Die minimale DTU-Anzahl pro Datenbank kann nicht mehr als (%d) für Dienstebene "%.*ls" betragen. | Minimale DTUs pro Datenbank; Dienstebene des elastischen Pools. | Es wurde versucht, eine minimale DTU-Anzahl pro Datenbank festzulegen, die über der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des elastischen Pools zu verwenden, die die gewünschte Einstellung unterstützt. | | 40873 | EX\_USER | Die Anzahl der Datenbanken (%d) und minimalen DTUs pro Datenbank (%d) darf die DTU-Anzahl des elastischen Pools (%d) nicht überschreiten. | Anzahl der Datenbanken im elastischen Pool; minimale DTUs pro Datenbank; DTUs im elastischen Pool. | Es wurde versucht, eine Mindestanzahl von DTUs für den elastischen Pool anzugeben, die die DTU-Anzahl des elastischen Pools überschreitet. | Sie sollten in Betracht ziehen, die Anzahl der DTUs im elastischen Pool zu erhöhen oder die Mindestanzahl der DTUs pro Datenbank zu verringern, oder Sie verringern die Anzahl der Datenbanken im elastischen Pool. | | 40877 | EX\_USER | Ein elastischer Pool kann nur gelöscht werden, wenn er keine Datenbanken enthält. | Keine | Der elastische Pool enthält eine oder mehrere Datenbanken und kann nicht gelöscht werden. | Entfernen Sie Datenbanken aus dem elastischen Pool, um ihn zu löschen. | | 40881 | EX\_USER | Der elastische Pool "%.*ls" hat den Grenzwert für die Anzahl an Datenbanken erreicht. Der Grenzwert für die Datenbankanzahl im elastischen Pool darf (%d) für einen elastischen Pool mit (%d) DTUs nicht überschreiten. | Name des elastischen Pools; Grenzwert für die Datenbankanzahl im elastischen Pool; DTUs für Ressourcenpool. | Es wurde versucht, eine Datenbank zu erstellen oder zum elastischen Pool hinzuzufügen, während der Grenzwert für die Datenbankanzahl des elastischen Pools erreicht wurde. | Bitte erhöhen Sie nach Möglichkeit die DTU-Anzahl des elastischen Pools, um die Begrenzung für Datenbanken zu steigern, oder entfernen Sie Datenbanken aus dem elastischen Pool. | | 40889 | EX\_USER | Die Begrenzung für DTUs oder Speicher für den elastischen Pool "%.*ls" kann nicht verkleinert werden, da nicht genügend Speicherplatz für die Datenbanken verfügbar wäre. | Name der elastischen Pools. | Es wurde versucht, die Speicherbegrenzung des elastischen Pools unter die Speicherauslastung zu verringern. | Reduzieren Sie die Speicherauslastung der einzelnen Datenbanken im elastischen Pool, oder entfernen Sie Datenbanken aus dem Pool, um die DTUs oder Speicherbegrenzung zu verringern. | | 40891 | EX\_USER | Die Mindestanzahl von DTUs pro Datenbank (%d) darf die Höchstanzahl von DTUs pro Datenbank (%d) nicht überschreiten. | DTU-Mindestanzahl pro Datenbank; DTU-Höchstanzahl pro Datenbank. | Es wurde versucht, die DTU-Mindestanzahl pro Datenbank höher festzulegen, als die maximale DTU-Anzahl pro Datenbank. | Stellen Sie sicher, dass die Mindestanzahl von DTUs pro Datenbank nicht die Höchstanzahl von DTUs pro Datenbank überschreitet. | | TBD | EX\_USER | Die Speichergröße für eine einzelne Datenbank in einem elastischen Pool darf die maximal zulässige Größe für die Dienstebene des elastischen Pools "%.*ls" nicht überschreiten. | Dienstebene des elastischen Pools | Die maximale Größe der Datenbank überschreitet die maximale Größe, die von der Dienstebene des elastische Pools zugelassen wird. | Legen Sie die maximale Größe der Datenbank höchstens auf die maximal zulässige Größe der Dienstebene des elastische Pools fest. |

<!---HONumber=AcomDC_0309_2016-->