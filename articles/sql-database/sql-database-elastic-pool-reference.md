<properties 
	pageTitle="Azure SQL – Referenz für elastische Datenbankpools" 
	description="Diese Referenz enthält Details und Links zu Artikeln über elastische Datenbankpools sowie Informationen zur Programmierbarkeit." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/24/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# SQL-Datenbank – Referenz für elastische Datenbankpools (Vorschau)

Für SaaS-Entwickler, die Dutzende, Hunderte oder sogar Tausende von Datenbanken haben, vereinfacht ein elastischer Datenbankpool das Erstellen, Warten und Verwalten der Leistung und Kosten der gesamten Datenbankgruppe.

Diese Referenz enthält Details und Links zu Artikeln über elastische Pools sowie Informationen zur Programmierbarkeit.

## Übersicht

Ein elastischer Pool ist eine Sammlung von Datenbank-Durchsatzeinheiten (DTUs) und -Speichern (GB), die von mehreren Datenbanken gemeinsam verwendet werden. Elastische Datenbanken können hinzugefügt und jederzeit aus dem Pool entfernt werden. Elastische Datenbanken im Pool verwenden nur die Ressourcen, die sie aus diesem benötigen, und geben somit Ressourcen für die aktiven Datenbanken frei.



## Voraussetzungen für das Erstellen und Verwalten von elastischen Pools


- Elastische Pools sind nur auf Servern in Azure SQL-Datenbank V12 verfügbar.   
- PowerShell und REST-APIs für elastische Pools werden nur im Azure-Ressourcen-Manager (ARM) unterstützt. Verwaltungsdienstbefehle (RDFE) werden nicht unterstützt. 
- Das Erstellen und Verwalten von elastischen Pools wird nur im [Microsoft Azure-Portal](https:portal.azure.com) unterstützt. 


## Einschränkungen für die aktuelle Vorschau

- Der Tarif für elastische Pools in der aktuellen Vorschau ist "Standard".  
- Das direkte Importieren einer Datenbank in einen elastischen Pool wird nicht unterstützt. Sie können in eine eigenständige Datenbank importieren und die Datenbank anschließend in einen Pool verschieben. Das Exportieren einer Datenbank aus einem Pool wird unterstützt.


## Liste der Artikel

Die folgenden Artikel helfen Ihnen beim Einstieg in elastische Datenbanken und elastische Aufträge:

| Artikel | Beschreibung |
| :-- | :-- |
| [Elastische SQL-Datenbankpools](sql-database-elastic-pool.md) | Übersicht über elastische Pools |
| [Erstellen und Verwalten elastischer SQL-Datenbankpools mit dem Azure-Portal](sql-database-elastic-pool-portal.md) | Erstellen und Verwalten elastischer Pools mit dem Azure-Portal |
| [Erstellen und Verwalten elastischer SQL-Datenbankpools mit PowerShell](sql-database-elastic-pool-powershell.md) | Erstellen und Verwalten elastischer Pools mit PowerShell-Cmdlets |
| [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md) | Ein Überblick über den Dienst für elastische Aufträge, der die Ausführung von T-SQL-Skripts in allen elastischen Datenbanken in einem Pool ermöglicht |
| [Installieren der Komponente für elastische Datenbankaufträge](sql-database-elastic-jobs-service-installation.md) | Installieren des Diensts für elastische Datenbankaufträge |
| [Erstellen des erforderlichen Benutzers für den Dienst für elastische Aufträge](sql-database-elastic-jobs-add-logins-to-dbs.md) | Um ein Skript für elastische Datenbankaufträge ausführen zu können, muss zu jeder Datenbank im Pool ein Benutzer mit den entsprechenden Berechtigungen hinzugefügt werden. |
| [Deinstallieren der Komponenten für elastische Datenbankaufträge](sql-database-elastic-jobs-uninstall.md) | Wiederherstellung nach Installationsfehlern des Diensts für elastische Datenbankaufträge |



## Namespace- und Endpunktdetails
Ein elastischer Pool ist eine ARM-Ressource vom Typ "ElasticPool" in Microsoft Azure SQL-Datenbank.

- **Namespace**: Microsoft.Sql/ElasticPool
- **secondary-endpoint** für REST-API-Aufrufe (Azure-Ressourcen-Manager): https://management.azure.com



## Eigenschaften von elastischen Datenbankpools

| Eigenschaft | Beschreibung |
| :-- | :-- |
| creationDate | Datum, an dem der Pool erstellt wurde. |
| databaseDtuMax | Maximale Anzahl von DTUs, die von einer einzelnen Datenbank im Pool verwendet werden können. Der Datenbank-DTU-Maximalwert stellt kein Ressourcengarantie dar. Der DTU-Maximalwert gilt für alle Datenbanken im Pool. |
| databaseDtuMin | Minimale Anzahl von DTUs, die einer einzelnen Datenbank im Pool garantiert werden können. Der Datenbank-DTU-Mindestwert kann auf 0 festgelegt werden. Der DTU-Mindestwert gilt für alle Datenbanken im Pool. Beachten Sie, dass das Produkt aus Anzahl von Datenbanken im Pool und Datenbank-DTU-Mindestwert nicht die tatsächliche Anzahl der DTUs im Pool übersteigen darf. |
| Dtu | Anzahl der DTUs, die von allen Datenbanken im Pool gemeinsam verwendet werden. |
| edition | Die Dienstebene des Pools. Jede Datenbank im Pool hat diese Edition. |
| elasticPoolId | Die GUID der Instanz des Pools. |
| elasticPoolName | Der Name des Pools. Der Name ist eindeutig in Bezug auf den übergeordneten Server. |
| location | Speicherort im Rechenzentrum, in dem der Pool erstellt wurde. |
| state | Der Status ist "Deaktiviert" bei Zahlungsrückstand für das Abonnement und andernfalls "Bereit". |
| storageMB | Die Speicherbeschränkung für den Pool in MB. Jede einzelne Datenbank im Pool kann Speicher bis zur Begrenzung der Standard Edition (250 GB) verwenden. Der insgesamt von allen Datenbanken im Pool verwendete Speicher darf diesen Poolgrenzwert jedoch nicht überschreiten. |


## DTUs und Speicherbeschränkungen für elastische Pools und elastische Datenbanken

Die Speicherbegrenzung des Pools wird durch die Menge der DTUs des Pools bestimmt; jede DTU = 1 GB Speicher. Beispielsweise hat ein 200-DTU-Pool einen Grenzwert von 200 GB.

| -Eigenschaft | Standardwert | Gültige Werte |
| :-- | :-- | :-- |
| Dtu | 100 | 100, 200, 400, 800, 1.200 |
| databaseDtuMax | 100 | 10, 20, 50, 100 |
| databaseDtuMin | 0 | 0, 10, 20, 50 |
| storageMB | 100 GB* | 100 GB, 200 GB, 400 GB, 800 GB, 1.200 GB |

*Einheiten der API sind MB nicht GB

## Grenzwerte für Worker und Sitzung

Die maximale Anzahl von gleichzeitigen Workern und gleichzeitigen Sitzungen, die von allen Datenbanken in einem elastischen Pool unterstützt werden, hängt von der DTU-Einstellung für den Pool ab:

| DTUs | Max. gleichzeitige Worker | Max. gleichzeitige Sitzungen |
| :-- | :-- | :-- |
| 100 | 200 | 2.400 |
| 200 | 400 | 4.800 |
| 400 | 800 | 9.600 |
| 800 | 1.600 | 19.200 |
| 1.200 | 2.400 | 28.800 |


## Einschränkungen für Azure-Ressourcen-Manager:

Ein elastischer Pool erfordert einen Server in Azure SQL-Datenbank V12. Die Server befinden sich innerhalb einer Ressourcengruppe.

- Jede Ressourcengruppe kann maximal 800 Server enthalten.
- Jeder Server kann über maximal 800 elastische Pools verfügen.
- Jeder elastische Pool kann bis zu 100 Datenbanken enthalten.


## Latenzzeit der elastischen Poolvorgänge

- Änderungen der garantierten DTUs pro Datenbank ("databaseDtuMin") oder der maximalen DTUs pro Datenbank ("databaseDtuMax") werden i. d. R. innerhalb von fünf Minuten oder weniger abgeschlossen. 
- Änderungen der DTU/Speicherbegrenzungen ("storageMB") des Pools hängen von der Gesamtmenge des Speicherplatzes aller Datenbanken im Pool ab. Änderungen dauern durchschnittlich 90 Minuten oder weniger pro 100 GB. Wenn beispielsweise der gesamte, von allen Datenbanken im Pool verwendete Speicherplatz 200 GB beträgt, ist für die Änderung der Pool-DTU/Speicherbegrenzung eine Latenzzeit von drei Stunden oder weniger zu erwarten. 



## PowerShell-Cmdlets und REST-API-Befehle (Azure-Ressourcen-Manager) für elastische Datenbankpools

Die folgenden PowerShell-Cmdlets und REST-API-Befehle stehen zum Erstellen und Verwalten elastischer Pools zur Verfügung:

| [PowerShell-Cmdlets](https://msdn.microsoft.com/library/mt125356.aspx) | [REST-API-Befehle](https://msdn.microsoft.com/library/azure/mt163571.aspx) |
| :-- | :-- |
| Get-AzureSqlDatabase | Azure SQL-Datenbank abrufen |
| Get-AzureSqLElasticPool | Elastische Datenbankpools in Azure SQL-Datenbank abrufen |
| Get-AzureSqlElasticPoolActivity | Elastische Datenbank-Poolvorgänge in Azure SQL-Datenbank abrufen |
| Get-AzureSqlElasticPoolDatabase | Elastische Datenbank in Azure SQL-Datenbank abrufen |
| Get-AzureSqlElasticPoolDatabaseActivity | Elastische Datenbankvorgänge in Azure SQL-Datenbank abrufen |
| Get-AzureSqlServer | Azure SQL-Datenbank-Server abrufen |
| Get-AzureSqlServerFirewallRule | Firewallregel des Azure SQL-Datenbank-Servers abrufen |
| Get-AzureSqlServerServiceObjective | Serverdienstziel in Azure SQL-Datenbank abrufen |
| New-AzureSqlDatabase | Azure SQL-Datenbank erstellen |
| New-AzureSqlElasticPool | Elastischen Datenbankpool in Azure SQL-Datenbank erstellen |
| New-AzureSqlServer | Azure SQL-Datenbank-Server erstellen |
| New-AzureSqlServerFirewallRule | Firewallregel für Azure SQL-Datenbank-Server erstellen |
| Remove-AzureSqlDatabase | Azure SQL-Datenbank entfernen |
| Remove-AzureSqlElasticPool | Elastischen Datenbankpool aus Azure SQL-Datenbank entfernen |
| Remove-AzureSqlServer | Azure SQL-Datenbank-Server entfernen |
| Set-AzureSqlDatabase | Azure SQL-Datenbank festlegen |
| Set-AzureSqlElasticPool | Elastische Datenbankpools in Azure SQL-Datenbank festlegen |
| Set-AzureSqlServer | Azure SQL-Datenbank-Server festlegen |
| Set-AzureSqlServerFirewallRule | Firewallregel für Azure SQL-Datenbank-Server festlegen |
| Get-Metrics | Metriken abrufen |


## Abrechnungs- und Preisinformationen

Elastische Datenbankpools werden anhand der folgenden Merkmale abgerechnet:

- Ein elastischer Pool wird bei seiner Erstellung abgerechnet, auch wenn keine Datenbanken im Pool vorhanden sind. 
- Ein elastischer Pool wird stündlich abgerechnet. Dies ist die gleiche Häufigkeit wie für Leistungsstufen bei eigenständigen Datenbanken. 
- Wenn ein elastischer Pool auf die Größe einer neuen Anzahl von DTUs geändert wird, wird der Pool erst dann gemäß den neuen DTUs berechnet, wenn die Größenänderung abgeschlossen ist. Dies erfolgt nach dem gleichen Muster wie beim Ändern der Leistungsstufe von eigenständigen Datenbanken. 


- Der Preis für einen elastischen Pool basiert auf der Anzahl der DTUs und der Datenbanken im Pool.
- Der Preis wird folgendermaßen berechnet: (Anzahl der Pool-DTUs) x (Einzelpreis pro DTU) + (Anzahl der Datenbanken) x (Einzelpreis pro Datenbank)

Der DTU-Einzelpreis für einen elastischen Anwendungspool ist höher als der DTU-Stückpreis für eine eigenständige Datenbank derselben Dienstebene. Weitere Informationen finden Sie unter [SQL-Datenbank Preise](http://azure.microsoft.com/pricing/details/sql-database/).

## Fehler in elastischen Datenbankpools

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | Der Speichergrenzwert des elastischen Pools wurde erreicht. Die Speicherauslastung für der elastischen Pool darf (%d) MB nicht überschreiten. | Speicherplatzbeschränkung des elastischen Pools in MB. | Es wurde versucht, Daten in eine Datenbank zu schreiben, während die Speicherbegrenzung des elastischen Pools erreicht wurde. | Erhöhen Sie nach Möglichkeit die DTUs des elastischen Pools, um die Speicherkapazität zu erweitern, reduzieren Sie die Speichernutzung der einzelnen Datenbanken innerhalb des elastischen Pools, oder entfernen Sie Datenbanken aus dem elastischen Pool. |
| 10929 | EX_USER | Die %s-Mindestgarantie beträgt %d, der maximale Wert beträgt %d und die aktuelle Nutzung für die Datenbank beträgt %d. Der Server ist jedoch derzeit zu stark ausgelastet, um Anforderungen über %d für diese Datenbank zu unterstützen. Weitere Informationen dazu finden Sie unter [Ressourcenkontrolle für Azure SQL-Datenbank](http://go.microsoft.com/fwlink/?LinkId=267637). Bitte versuchen Sie es andernfalls später noch einmal. | DTU-Minimum pro Datenbank; DTU-Maximum pro Datenbank | Die Gesamtanzahl der gleichzeitigen Worker (Anforderungen) in allen Datenbanken im elastischen Pool hat versucht, den Poolgrenzwert zu überschreiten. | Bitte erhöhen Sie nach Möglichkeit die DTU-Anzahl des elastischen Pools, um die Begrenzung für Worker zu steigern, oder entfernen Sie Datenbanken aus dem elastischen Pool. |
| 40844 | EX_USER | Datenbank "%Ls" auf Server "%ls" ist eine Datenbank der "%ls"-Edition in einem elastischen Pool und kann nicht über eine fortlaufende Kopierbeziehung verfügen. | Datenbankname, Datenbank-Edition, Servername | Ein StartDatabaseCopy-Befehl wurde für eine Nicht-Premium-Datenbank in einem elastischen Pool ausgegeben. | In Kürze verfügbar |
| 40857 | EX_USER | Der elastische Pool wurde für folgenden Server nicht gefunden: "%ls", Name des elastischen Pools: "%ls". | Name des Servers; Name des elastisches Pools | Der angegebene elastische Pool ist nicht auf dem angegebenen Server vorhanden. | Geben Sie einen gültigen Namen für den elastischen Pool an. |
| 40858 | EX_USER | Der elastische Pool "%ls" ist bereits auf folgendem Server vorhanden: "%ls" | Name des elastischen Pools, Servername | Der angegebene elastische Pool existiert bereits auf dem angegebenen logischen Server. | Geben Sie einen neuen Namen für den elastischen Pool an. |
| 40859 | EX_USER | Der elastische Pool unterstützt Dienstebene "%ls" nicht. | Dienstebene des elastischen Pools | Die angegebene Dienstebene wird für die Bereitstellung elastischer Pools nicht unterstützt. | Geben Sie die richtige Edition an, oder lassen Sie die Dienstebene leer, um die Standarddienstebene zu verwenden. |
| 40860 | EX_USER | Die Kombination aus elastischem Pool "%ls" und Dienstziel "%ls" ist ungültig. | Name des elastischen Pools; Name des Dienstebenenziels | Der elastische Pool und das Dienstziel können gemeinsam angegeben werden, wenn das Dienstziel als "ElasticPool" angegeben wird. | Geben Sie die richtige Kombination aus elastischem Pool und Dienstziel an. |
| 40861 | EX_USER | Die Datenbankedition "%.*ls" darf sich nicht von der Dienstebene des elastischen Pools ("%.*ls") unterscheiden. | Datenbankedition, Dienstebene des elastischen Pools | Die Datenbankedition unterscheidet sich von der Dienstebene des elastischen Pools. | Geben Sie keine Datenbankedition an, die sich von der Dienstebene des elastischen Pools unterscheidet. Beachten Sie, dass die Datenbankedition nicht angegeben werden muss. |
| 40862 | EX_USER | Der Name des elastischen Pools muss angegeben werden, wenn das Dienstziel des elastischen Pools angegeben wurde. | Keine | Das Dienstziel des elastischen Pools identifiziert einen elastischen Pool nicht eindeutig. | Geben Sie den Namen des elastischen Pools an, wenn Sie das Dienstziel des elastischen Pools verwenden. |
| 40864 | EX_USER | Die DTU-Anzahl für den elastischen Pool muss mindestens (%d) DTUs für die Dienstebene "%.*ls" betragen. | DTUs für elastischen Pool; Dienstebene des elastischen Pools. | Es wurde versucht, eine DTU-Anzahl für den elastischen Pool unterhalb des unteren Grenzwerts festzulegen. | Legen Sie die DTU-Einstellung für den elastischen Pool mindestens auf die Untergrenze fest. |
| 40865 | EX_USER | Die DTU-Anzahl für den elastischen Pool darf höchstens (%d) DTUs für die Dienstebene "%.*ls" betragen. | DTUs für elastischen Pool; Dienstebene des elastischen Pools. | Es wurde versucht, eine DTU-Anzahl für den elastischen Pool oberhalb des oberen Grenzwerts festzulegen. | Legen Sie die DTU-Einstellung für den elastischen Pool höchstens auf die Obergrenze fest. |
| 40867 | EX_USER | Die maximalen DTUs pro Datenbank müssen mindestens (%d) für Dienstebene "%.*ls" betragen. | Maximale DTUs pro Datenbank; Dienstebene des elastischen Pools | Es wurde versucht, eine maximale DTU-Anzahl pro Datenbank festzulegen, die unter der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des elastischen Pools zu verwenden, die die gewünschte Einstellung unterstützt. |
| 40868 | EX_USER | Die maximale DTU-Anzahl pro Datenbank kann nicht mehr als (%d) für Dienstebene "%.*ls" betragen. | Maximale DTUs pro Datenbank; Dienstebene des elastischen Pools. | Es wurde versucht, eine maximale DTU-Anzahl pro Datenbank festzulegen, die über der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des elastischen Pools zu verwenden, die die gewünschte Einstellung unterstützt. |
| 40870 | EX_USER | Die minimale DTU-Anzahl pro Datenbank kann nicht mehr als (%d) für Dienstebene "%.*ls" betragen. | Minimale DTUs pro Datenbank; Dienstebene des elastischen Pools. | Es wurde versucht, eine minimale DTU-Anzahl pro Datenbank festzulegen, die über der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des elastischen Pools zu verwenden, die die gewünschte Einstellung unterstützt. |
| 40873 | EX_USER | Die Anzahl der Datenbanken (%d) und minimalen DTUs pro Datenbank (%d) darf die DTU-Anzahl des elastischen Pools (%d) nicht überschreiten. | Anzahl der Datenbanken im elastischen Pool; minimale DTUs pro Datenbank; DTUs im elastischen Pool. | Es wurde versucht, eine Mindestanzahl von DTUs für den elastischen Pool anzugeben, die die DTU-Anzahl des elastischen Pools überschreitet. | Sie sollten in Betracht ziehen, die Anzahl der DTUs im elastischen Pool zu erhöhen oder die Mindestanzahl der DTUs pro Datenbank zu verringern, oder Sie verringern die Anzahl der Datenbanken im elastischen Pool. |
| 40877 | EX_USER | Ein elastischer Pool kann nur gelöscht werden, wenn er keine Datenbanken enthält. | Keine | Der elastische Pool enthält eine oder mehrere Datenbanken und kann nicht gelöscht werden. | Entfernen Sie Datenbanken aus dem elastischen Pool, um ihn zu löschen. |
| 40881 | EX_USER | Der elastische Pool "%.*ls" hat den Grenzwert für die Anzahl an Datenbanken erreicht. Der Grenzwert für die Datenbankanzahl im elastischen Pool darf (%d) für einen elastischen Pool mit (%d) DTUs nicht überschreiten. | Name des elastischen Pools; Grenzwert für die Datenbankanzahl im elastischen Pool; DTUs für Ressourcenpool. | Es wurde versucht, eine Datenbank zu erstellen oder zum elastischen Pool hinzuzufügen, während der Grenzwert für die Datenbankanzahl des elastischen Pools erreicht wurde. | Bitte erhöhen Sie nach Möglichkeit die DTU-Anzahl des elastischen Pools, um die Begrenzung für Datenbanken zu steigern, oder entfernen Sie Datenbanken aus dem elastischen Pool. |
| 40889 | EX_USER | Die Begrenzung für DTUs oder Speicher für den elastischen Pool "%.*ls" kann nicht verkleinert werden, da nicht genügend Speicherplatz für die Datenbanken verfügbar wäre. | Name der elastischen Pools. | Es wurde versucht, die Speicherbegrenzung des elastischen Pools unter die Speicherauslastung zu verringern. | Reduzieren Sie die Speicherauslastung der einzelnen Datenbanken im elastischen Pool, oder entfernen Sie Datenbanken aus dem Pool, um die DTUs oder Speicherbegrenzung zu verringern. |
| 40891 | EX_USER | Die Mindestanzahl von DTUs pro Datenbank (%d) darf die Höchstanzahl von DTUs pro Datenbank (%d) nicht überschreiten. | DTU-Mindestanzahl pro Datenbank; DTU-Höchstanzahl pro Datenbank. | Es wurde versucht, die DTU-Mindestanzahl pro Datenbank höher festzulegen, als die maximale DTU-Anzahl pro Datenbank. | Stellen Sie sicher, dass die Mindestanzahl von DTUs pro Datenbank nicht die Höchstanzahl von DTUs pro Datenbank überschreitet. |
| TBD | EX_USER | Die Speichergröße für eine einzelne Datenbank in einem elastischen Pool darf die maximal zulässige Größe für die Dienstebene des elastischen Pools "%.*ls" nicht überschreiten. | Dienstebene des elastischen Pools | Die maximale Größe der Datenbank überschreitet die maximale Größe, die von der Dienstebene des elastische Pools zugelassen wird. | Legen Sie die maximale Größe der Datenbank höchstens auf die maximal zulässige Größe der Dienstebene des elastische Pools fest. |

<!---HONumber=62-->