<properties 
	pageTitle="Erstellen und Verwalten von Aufträgen für die elastische Datenbank mithilfe von PowerShell" 
	description="PowerShell, verwendet zum Verwalten von Pools für Azure SQL-Datenbanken" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="ddove; sidneyh" />

# Erstellen und Verwalten von Aufträgen für die elastische SQL-Datenbank mithilfe von PowerShell (Vorschau)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



Mit den PowerShell-APIs für **Aufträge für die elastische Datenbank** (Vorschauversion) können Sie eine Gruppe von Datenbanken definieren, für die Skripts ausgeführt werden sollen. In diesem Artikel wird das Erstellen und Verwalten von Aufträgen für die elastische Datenbank mithilfe von PowerShell-Cmdlets veranschaulicht. Weitere Informationen finden Sie unter [Übersicht über Aufträge für die elastische Datenbank](sql-database-elastic-jobs-overview.md).

## Voraussetzungen
* Ein Azure-Abonnement. Eine kostenlose Testversion finden Sie unter [Einen Monat kostenlos testen](http://azure.microsoft.com/pricing/free-trial/).
* Eine Reihe von Datenbanken, die mit den Tools für die elastische Datenbank erstellt wurden. Weitere Informationen finden Sie unter [Erste Schritte mit Tools für elastische Datenbanken](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).
* PowerShell-Paket **Aufträge für die elastische Datenbank**. Weitere Informationen finden Sie unter [Installieren von Aufträgen für die elastische Datenbank](sql-database-elastic-jobs-service-installation.md).

### Auswählen des Azure-Abonnements

Zum Auswählen des Abonnements benötigen Sie Ihre Abonnement-ID (**-SubscriptionId**) oder Ihren Abonnementnamen (**-SubscriptionName**). Falls Sie über mehrere Abonnements verfügen, können Sie sie über das Cmdlet **Get-AzureSubscription** abrufen und die gewünschten Abonnementinformationen aus dem Resultset kopieren. Nachdem Sie Ihre Abonnementinformationen ermittelt haben, führen Sie das folgende Cmdlet aus, um das betreffende Abonnement als Standard festzulegen, insbesondere als Ziel für das Erstellen und Verwalten von Aufträgen:

	Select-AzureSubscription -SubscriptionId {SubscriptionID}

Die [PowerShell-ISE](https://technet.microsoft.com/library/dd315244.aspx) wird zum Entwickeln und Ausführen von PowerShell-Skripts für die Ausführung in Aufträgen für die elastische Datenbank empfohlen.

## Auftragsobjekte für die elastische Datenbank

In der folgenden Tabelle sind alle Objekttypen von Aufträgen für die elastische Datenbank zusammen mit einer Beschreibung und den relevanten PowerShell-APIs aufgelistet:

<table style="width:100%">
  <tr>
    <th>Objekttyp</th>
    <th>Beschreibung</th>
    <th>Zugeordnete PowerShell-APIs</th>
  </tr>
  <tr>
    <td>Anmeldeinformation</td>
    <td>Benutzername und Kennwort für das Verbinden mit Datenbanken zwecks Ausführung von Skripts oder Anwendung von DACPACs. <p>Das Kennwort wird vor dem Senden an die und dem Speichern in der Auftragsdatenbank für die elastische Datenbank verschlüsselt. Das Kennwort wird von dem Dienst für Aufträge für die elastische Datenbank mithilfe der vom Installationsskript erstellten und hochgeladenen Anmeldeinformationen entschlüsselt.</td>
	<td><p>Get-AzureSqlJobCredential</p>
	<p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Skript</td>
    <td>Transact-SQL-Skript, das für die übergreifende Ausführung über mehrere Datenbanken verwendet werden soll. Das Skript sollte idempotent verfasst werden, da der Dienst die Ausführung des Skripts bei Fehlern wiederholt.
	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>Get-AzureSqlJobContentDefinition</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Anwendung auf Datenebene</a> für die übergreifende Anwendung für Datenbanken.

	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>
  <tr>
    <td>Datenbankziel</td>
    <td>Datenbank- und Servername, die auf eine Azure SQL-Datenbank verweisen.

	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
  <tr>
    <td>Shardzuordnungsziel</td>
    <td>Die Kombination aus einem Datenbankziel und einer Anmeldeinformation, die dafür verwendet werden soll, die in einer Shardzuordnung der elastischen Datenbank gespeicherten Informationen zu bestimmen.
	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	<p>Set-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Benutzerdefiniertes Sammlungsziel</td>
    <td>Definierte Gruppe von Datenbanken, die kollektiv für die Ausführung verwendet werden sollen.</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Untergeordnetes benutzerdefiniertes Sammlungsziel</td>
    <td>Datenbankziel, auf das eine benutzerdefinierte Sammlung verweist.</td>
	<td>
	<p>Add-AzureSqlJobChildTarget</p>
	<p>Remove-AzureSqlJobChildTarget</p>
	</td>
  </tr>

<tr>
    <td>Auftrag</td>
    <td>
	<p>Definition von Parametern für einen Auftrag, der zum Auslösen der Ausführung oder zum Erfüllen eines Zeitplans verwendet werden kann.</p>
	</td>
	<td>
	<p>Get-AzureSqlJob</p>
	<p>New-AzureSqlJob</p>
	<p>Set-AzureSqlJob</p>
	</td>
  </tr>

<tr>
    <td>Auftragsausführung</td>
    <td>
	<p>Container von Aufgaben, die zum Ausführen eines Skripts oder zum Anwenden eines DACPACs auf ein Ziel mithilfe von Anmeldeinformationen für Datenbankverbindungen erforderlich sind, wobei Fehler in Übereinstimmung mit einer Ausführungsrichtlinie behandelt werden.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Ausführung von Auftragsaufgaben</td>
    <td>
	<p>Einzelne Arbeitsaufgabe zum Erfüllen eines Auftrags.</p>
	<p>Wenn eine Auftragsaufgabe nicht erfolgreich ausgeführt werden kann, wird die resultierende Ausnahmemeldung protokolliert, und es wird eine neue entsprechende Auftragsaufgabe in Übereinstimmung mit der angegebenen Ausführungsrichtlinie erstellt und ausgeführt.</p></p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Auftragsausführungsrichtlinie</td>
    <td>
	<p>Steuert die Timeouts für die Auftragsausführung, die Grenzwerte für Wiederholungsversuche und die Intervalle zwischen Wiederholungsversuchen.</p>
	<p>Die Aufträge für die elastische Datenbank enthalten eine standardmäßige Auftragsausführungsrichtlinie, die grundsätzlich unbeschränkte Wiederholungsversuche bei Auftragsaufgaben mit Fehlern und exponentielles Backoff der Intervalle zwischen den einzelnen Wiederholungsversuchen festlegt.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecutionPolicy</p>
	<p>New-AzureSqlJobExecutionPolicy</p>
	<p>Set-AzureSqlJobExecutionPolicy</p>
	</td>
  </tr>

<tr>
    <td>Zeitplan</td>
    <td>
	<p>Zeitbasierte Festlegung der Ausführung, die entweder in einem sich wiederholenden Intervall oder einmalig erfolgen soll.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobSchedule</p>
	<p>New-AzureSqlJobSchedule</p>
	<p>Set-AzureSqlJobSchedule</p>
	</td>
  </tr>

<tr>
    <td>Auftragsauslöser</td>
    <td>
	<p>Eine Zuordnung zwischen einem Auftrag und einem Zeitplan zum Auslösen der Auftragsausführung gemäß dem Zeitplan.</p>
	</td>
	<td>
	<p>New-AzureSqlJobTrigger</p>
	<p>Remove-AzureSqlJobTrigger</p>
	</td>
  </tr>
</table>

## Von den Aufträgen für die elastische Datenbank unterstützte Gruppentypen
Der Auftrag führt Transact-SQL (T-SQL)-Skripts aus und ermöglicht die Anwendung von DACPACs für eine gesamte Gruppe von Datenbanken. Wenn ein Auftrag zur übergreifenden Ausführung für eine Gruppe von Datenbanken gesendet wird, wird der Auftrag in untergeordnete Aufträge aufgeteilt, die jeweils die angeforderte Ausführung für eine Einzeldatenbank der Gruppe übernehmen.
 
Zwei Arten von Gruppen können erstellt werden:

* [Shardzuordnung](sql-database-elastic-scale-shard-map-management.md): Wenn ein Auftrag gesendet wird, dessen Ziel eine Shardzuordnung ist, fragt der Auftrag zunächst die Shardzuordnung ab, um deren aktuellen Shardsatz zu bestimmen, und erstellt dann entsprechende untergeordnete Aufträge für die einzelnen Shards der Shardzuordnung.
* Benutzerdefinierte Sammlung: Ein benutzerdefinierter Satz von Datenbanken. Wenn ein Auftrag eine benutzerdefinierte Auflistung als Ziel hat, erstellt er untergeordnete Aufträge für die einzelnen Datenbanken, die derzeit in der benutzerdefinierten Auflistung enthalten sind.

## So legen Sie die Verbindung für Aufträge für die elastische Datenbank fest

Vor der Verwendung der Auftrags-APIs muss eine Verbindung mit der *Verwaltungsdatenbank* der Aufträge festgelegt werden. Durch Ausführen dieses Cmdlets wird ein Anmeldeinformationsfenster aufgerufen, das die Kombination aus Benutzername und Kennwort anfordert, die bei der Installation der Aufträge für die elastische Datenbank erstellt wurde. Alle in diesem Thema gegebenen Beispiele gehen davon aus, dass dieser erste Schritt bereits ausgeführt wurde.

Öffnen einer Verbindung mit den Aufträgen für die elastische Datenbank:

	Use-AzureSqlJobConnection -CurrentAzureSubscription 

## Verschlüsselte Anmeldeinformationen innerhalb der Aufträge für die elastische Datenbank

Datenbankanmeldeinformationen können mit verschlüsseltem Kennwort in die *Verwaltungsdatenbank* der Aufträge eingefügt werden. Die Anmeldeinformationen müssen gespeichert werden, um die Ausführung von Aufträgen zu einem späteren Zeitpunkt (unter Verwendung von Auftragszeitplänen) zu ermöglichen.
 
Verschlüsselung funktioniert mithilfe eines Zertifikats, das im Rahmen des Installationsskripts erstellt wird. Das Installationsskript erstellt das Zertifikat und lädt es zwecks Entschlüsselung der gespeicherten verschlüsselten Kennwörter auf den Azure Cloud Service hoch. Der Azure-Clouddienst speichert den öffentlichen Schlüssel anschließend in der *Verwaltungsdatenbank* der Aufträge, was die Verschlüsselung eines übergebenen Kennworts durch die PowerShell-API oder die Azure-Portalschnittstelle ermöglicht, ohne dass das Zertifikat dazu lokal installiert sein muss.
 
Die Kennwörter der Anmeldeinformationen werden verschlüsselt und sind vor Benutzern mit schreibgeschütztem Zugriff auf Auftragsobjekte für die elastische Datenbank geschützt. Böswillige Benutzer mit Lese-/Schreibzugriff auf Auftragsobjekte für die elastische Datenbank können jedoch ein Kennwort extrahieren. Die Anmeldeinformationen sind für die erneute Verwendung bei der Auftragsausführung bestimmt. Die Anmeldeinformationen werden beim Einrichten der Verbindungen an die Zieldatenbanken übergeben. Derzeit gibt es keinerlei Einschränkungen für die Zieldatenbanken, die für die einzelnen Anmeldeinformationen verwendet werden. Ein böswilliger Benutzer könnte also ein Datenbankziel für eine von ihm kontrollierte Datenbank hinzufügen. Der Benutzer könnte daraufhin einen Auftrag für diese Datenbank starten, um sich das Kennwort der Anmeldeinformationen zu beschaffen.

Die bewährten Sicherheitsverfahren bei Aufträgen für die elastische Datenbank umfassen Folgendes:

* Schränken Sie die Nutzung der APIs auf einen vertrauenswürdigen Personenkreis ein.
* Die Anmeldeinformationen sollten immer nur mit den für die Ausführung von Auftragsaufgaben erforderlichen Mindestberechtigungen ausgestattet sein. Weitere Informationen finden Sie im MSDN-Artikel [Autorisierung und Berechtigungen in SQL Server](https://msdn.microsoft.com/library/bb669084.aspx).

### So erstellen Sie verschlüsselte Anmeldeinformationen für die übergreifende Ausführung von Aufträgen für Datenbanken

Bei der Erstellung neuer verschlüsselter Anmeldeinformationen fordert das Cmdlet [**Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) zur Eingabe von Benutzername und Kennwort auf, die an das Cmdlet [**New-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346063.aspx) übergeben werden können.

	$credentialName = "{Credential Name}"
	$databaseCredential = Get-Credential
	$credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
	Write-Output $credential

### So aktualisieren Sie die Anmeldeinformationen

Verwenden Sie bei einer Kennwortänderung das Cmdlet [**Set-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346062.aspx), und legen Sie den Parameter **CredentialName** fest.

	$credentialName = "{Credential Name}"
	Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## So definieren Sie ein Shardzuordnungsziel für die elastische Datenbank

Führen Sie einen Auftrag für alle Datenbanken in einem Shardsatz (der mithilfe der [Clientbibliothek für die elastische Datenbank](sql-database-elastic-database-client-library.md) erstellt wurde) aus. Verwenden Sie dazu eine Shardzuordnung als Datenbankziel. Dieses Beispiel erfordert eine Anwendung, die Sharding unterstützt und mithilfe der Clientbibliothek für die elastische Datenbank erstellt wurde. Weitere Informationen finden Sie unter [Erste Schritte mit Tools für elastische Datenbanken](sql-database-elastic-scale-get-started.md).

###Erstellen eines Shardzuordnungs-Managers mithilfe der Beispiel-App

Dieses Beispiel erstellt einen Shardzuordnungs-Manager und mehrere Shards und fügt anschließend Daten in die Shards ein.

1. Erstellen Sie die Beispielanwendung aus **Erste Schritte mit den Tools für die elastische Datenbank**, und führen Sie sie aus. Führen Sie die Schritte bis zu Schritt 7 aus dem Abschnitt [Herunterladen und Ausführen der Beispiel-App](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools) aus. Am Ende von Schritt 7 wird die folgende Eingabeaufforderung angezeigt:

	![Eingabeaufforderung][1]

2.  Geben Sie im Befehlsfenster „1“ ein, und drücken Sie die EINGABETASTE. Dadurch wird der Shardzuordnungs-Manager erstellt, und es werden zwei Shards zum Server hinzugefügt. Geben Sie dann „3“ ein, und drücken Sie die EINGABETASTE. Wiederholen Sie den Vorgang viermal. Dadurch werden Beispieldatenzeilen in die Shards eingefügt.
  
3.  Im [Azure-Vorschauportal](https://portal.azure.com) werden drei neue Datenbanken auf dem V12-Server angezeigt:

	![Visual Studio-Bestätigung][2]

Erstellen Sie mithilfe des Cmdlets [**New-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346063.aspx) ein Shardzuordnungsziel. Die Datenbank des Shardzuordnungs-Managers muss dann als Datenbankziel festgelegt und die spezifische Shardzuordnung anschließend als Ziel angegeben werden.

	$shardMapCredentialName = "{Credential Name}"
	$shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
	$shardMapDatabaseServerName = "{ShardMapServerName}"
	$shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
	$shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
	$shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
	Write-Output $shardMapTarget

## Erstellen eines T-SQL-Skripts für die datenbankübergreifende Ausführung

Beim Erstellen von T-SQL-Skripts für die Ausführung empfiehlt es sich dringend, sie [idempotent](https://en.wikipedia.org/wiki/Idempotence) und widerstandsfähig gegen Fehler zu gestalten. Aufträge für die elastische Datenbank versuchen bei jedem auftretenden Fehler, die Skriptausführung zu wiederholen, unabhängig von der Klassifikation des Fehlers.

Verwenden Sie das Cmdlet [**New-AzureSqlJobContent**](https://msdn.microsoft.com/library/mt346085.aspx), um ein Skript für die Ausführung zu erstellen und zu speichern, und legen Sie die Parameter **-ContentName** und **-CommandText** fest.

	$scriptName = "Create a TestTable"

	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
		CREATE TABLE TestTable(
			TestTableId INT PRIMARY KEY IDENTITY,
			InsertionTime DATETIME2
		);
	END
	GO
	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO"

	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Erstellen eines neuen Skripts aus einer Datei
Wenn das T-SQL-Skript in einer Datei definiert ist, verwenden Sie Folgendes, um das Skript zu importieren:

	$scriptName = "My Script Imported from a File"
	$scriptPath = "{Path to SQL File}"
	$scriptCommandText = Get-Content -Path $scriptPath
	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### So aktualisieren Sie ein T-SQL-Skript für die datenbankübergreifende Ausführung  

Dieses PowerShell-Skript aktualisiert den T-SQL-Befehlstext für ein vorhandenes Skript.

Legen Sie die folgenden Variablen so fest, dass sie die gewünschte festzulegende Skriptdefinition angeben:

	$scriptName = "Create a TestTable"
	$scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	CREATE TABLE TestTable(
		TestTableId INT PRIMARY KEY IDENTITY,
		InsertionTime DATETIME2
	);
	END
	GO

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO"

### So aktualisieren Sie die Definition auf ein vorhandenes Skript

	Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## So erstellen Sie einen Auftrag zur übergreifenden Ausführung eines Skripts für eine Shardzuordnung

Dieses PowerShell-Skript startet einen Auftrag zur Ausführung eines Skripts für jeden Shard in einer Shardzuordnung mit elastischer Skalierung.

Legen Sie die folgen den Variablen fest, um das gewünschte Skript und Ziel anzugeben:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$credentialName = "{Credential Name}"
	$shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
	$job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
	Write-Output $job

## So führen Sie einen Auftrag aus 

Dieses PowerShell-Skript führt einen vorhandenen Auftrag aus:

Aktualisieren Sie die folgende Variable, sodass sie den gewünschten Auftragsnamen für die Ausführung angibt:

	$jobName = "{Job Name}"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## So rufen Sie den Ausführungsstatus eines einzelnen Auftrags ab

Verwenden Sie das Cmdlet [**Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx), und legen Sie den Parameter **JobExecutionId** fest, um den Status der Auftragsausführung anzuzeigen.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
	Write-Output $jobExecution

Verwenden Sie das Cmdlet **Get-AzureSqlJobExecution** mit dem Parameter **IncludeChildren**, um den Ausführungsstatus untergeordneter Aufträge anzuzeigen (insbesondere den spezifischen Status für die Ausführung der einzelnen Aufträge für jede der im Auftrag enthaltenen Datenbanken).

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## So zeigen Sie den Status für mehrere Auftragsausführungen an

Das Cmdlet [**Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) verfügt über mehrere optionale Parameter, die zum Anzeigen der Ausführung mehrerer Aufträge (gefiltert auf der Grundlage der angegebenen Parameter) verwendet werden können. Die folgenden Beispiele zeigen einige der möglichen Verwendungsweisen von „Get-AzureSqlJobExecution“:

Abrufen aller aktiven Auftragsausführungen auf der obersten Ebene:

	Get-AzureSqlJobExecution

Abrufen aller Auftragsausführungen auf der obersten Ebene, einschließlich der inaktiven Auftragsausführungen:

	Get-AzureSqlJobExecution -IncludeInactive

Abrufen der Ausführung aller untergeordneten Aufträge mit einer angegebenen Auftragsausführungs-ID, einschließlich inaktiver Auftragsausführungen:

	$parentJobExecutionId = "{Job Execution Id}"
	Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Abrufen aller Auftragsausführungen, die eine bestimmte Kombination aus Zeitplan und Auftrag verwenden, einschließlich inaktiver Aufträge:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Abrufen aller Aufträge, die eine angegebene Shardzuordnung als Ziel haben, einschließlich inaktiver Aufträge:

	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Abrufen aller Aufträge, die eine angegebene benutzerdefinierte Sammlung als Ziel haben, einschließlich inaktiver Aufträge:

	$customCollectionName = "{Custom Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Abrufen der Liste der Ausführungen von Auftragsaufgaben innerhalb einer bestimmten Auftragsausführung:

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Write-Output $jobTaskExecutions 

Abrufen von Ausführungsdetails zu Auftragsaufgaben:

Das folgende PowerShell-Skript kann zum Anzeigen der Ausführungsdetails einer Auftragsaufgabe verwendet werden, was besonders beim Debuggen von Ausführungsfehlern nützlich ist.

	$jobTaskExecutionId = "{Job Task Execution Id}"
	$jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
	Write-Output $jobTaskExecution

## So rufen Sie Ausführungsfehler innerhalb von Auftragsaufgaben ab

Das Objekt **JobTaskExecution** beinhaltet eine Eigenschaft für den Lebenszyklus der Aufgabe sowie eine Meldungseigenschaft. Bei einem Ausführungsfehler für eine Auftragsaufgabe wird die Lebenszykluseigenschaft auf *Failed* und die Meldungseigenschaft auf die resultierende Ausnahmemeldung und deren Stapel festgelegt. Wenn ein Auftrag nicht erfolgreich abgeschlossen wurde, müssen die Details der Auftragsaufgaben angezeigt werden, die für einen bestimmten Auftrag nicht erfolgreich abgeschlossen wurden.

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## So warten Sie auf den Abschluss einer Auftragsausführung

Das folgende PowerShell-Skript kann verwendet werden, um auf den Abschluss einer Auftragsaufgabe zu warten:

	$jobExecutionId = "{Job Execution Id}"
	Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## Erstellen einer benutzerdefinierten Ausführungsrichtlinie

Aufträge für die elastische Datenbank unterstützen das Erstellen benutzerdefinierter Ausführungsrichtlinien, die beim Starten von Aufträgen angewendet werden können.
  
Ausführungsrichtlinien lassen aktuell die folgenden Definitionen zu:

* Name: ID der Ausführungsrichtlinie.
* Auftragstimeout: Gesamtzeit bis zum Abbruch eines Auftrags durch die Aufträge für die elastische Datenbank.
* Anfängliches Wiederholungsintervall: Wartezeit vor dem ersten Wiederholungsversuch.
* Maximales Wiederholungsintervall: Höchstmenge der zu durchlaufenden Wiederholungsintervalle.
* Backoffkoeffizient des Wiederholungsintervalls: Koeffizient zur Berechnung des nächsten Intervalls zwischen Wiederholungsversuchen. Dazu wird diese Formel verwendet: (Anfängliches Wiederholungsintervall) * Math.pow((Intervallbackoffkoeffizient), (Anzahl der Wiederholungsversuche) - 2). 
* Versuche maximal: Die Höchstzahl der im Rahmen eines Auftrags auszuführenden Wiederholungsversuche.

Für die standardmäßige Ausführungsrichtlinie werden diese Werte verwendet:

* Name: Standardausführungsrichtlinie
* Auftragstimeout: 1 Woche
* Anfängliches Wiederholungsintervall: 100 Millisekunden
* Maximales Wiederholungsintervall: 30 Minuten
* Wiederholungsintervallkoeffizient: 2
* Versuche maximal: 2.147.483.647

Erstellen Sie die gewünschte Ausführungsrichtlinie:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 10
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
	-RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $executionPolicy

### Aktualisieren einer benutzerdefinierten Ausführungsrichtlinie

Aktualisieren Sie die gewünschte Ausführungsrichtlinie:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 15
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $updatedExecutionPolicy
 
## Abbrechen eines Auftrags

Aufträge für die elastische Datenbank unterstützen Abbruchanforderungen für Aufträge. Wenn Aufträge für die elastische Datenbank eine Abbruchanforderung für einen aktuell ausgeführten Auftrag erkennen, versuchen sie, den Auftrag zu beenden.

Der Abbruch kann von Aufträgen für die elastische Datenbank auf zwei verschiedene Arten ausgeführt werden:

1. Abbruch aktuell ausgeführter Aufgaben: Wird während der Aufgabenausführung ein Abbruch erkannt, wird ein Abbruch innerhalb des aktuell ausgeführten Aspekts der Aufgabe versucht. Beispiel: Wenn ein versuchter Abbruch sich auf eine aktuell ausgeführte Abfrage mit langer Laufzeit bezieht, wird versucht, die Abfrage abzubrechen.
2. Abbruch von Aufgabenwiederholungen: Wird vom Steuerthread ein Abbruch erkannt, bevor die Ausführung einer Aufgabe gestartet wurde, verhindert der Steuerthread das Starten der Aufgabe und erklärt die Anforderung für abgebrochen.

Wenn für einen übergeordneten Auftrag ein Auftragsabbruch angefordert wird, wird die Abbruchanforderung für den übergeordneten Auftrag und für alle seine untergeordneten Aufträge berücksichtigt.
 
Verwenden Sie zum Senden von Abbruchanforderungen das Cmdlet [**Stop-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx), und legen Sie den Parameter **JobExecutionId** fest.

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## So löschen Sie einen Auftrag und einen Auftragsverlauf asynchron

Aufträge für die elastische Datenbank unterstützen die asynchrone Löschung von Aufträgen. Ein Auftrag kann für die Löschung markiert werden, und das System löscht den Auftrag und seinen gesamten Verlauf, nachdem die gesamte Ausführung von Unteraufträgen für den Auftrag abgeschlossen wurde. Das System bricht aktiv ausgeführte Aufträge nicht automatisch ab.

Rufen Sie [**Stop-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) auf, um die Ausführung aktiver Aufträge abzubrechen.

Verwenden Sie zum Auslösen der Löschung von Aufträgen das Cmdlet [**Remove-AzureSqlJob**](https://msdn.microsoft.com/library/mt346083.aspx), und legen Sie den Parameter **JobName** fest.

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## So erstellen Sie ein benutzerdefiniertes Datenbankziel

Benutzerdefinierte Datenbankziele können für die direkte Ausführung oder für die Aufnahme in eine benutzerdefinierte Gruppe definiert werden. Da beispielsweise **elastische Datenbankpools** durch die PowerShell-APIs noch nicht direkt unterstützt werden, können Sie einfach ein benutzerdefiniertes Datenbankziel und ein benutzerdefiniertes Datenbanksammlungsziel erstellen, das sämtliche Datenbanken im Pool enthält.

Legen Sie die folgenden Variablen fest, um die gewünschten Datenbankinformationen anzugeben:

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## So erstellen Sie ein benutzerdefiniertes Datenbanksammlungsziel

Definieren Sie mithilfe des Cmdlets [**New-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) ein benutzerdefiniertes Datenbanksammlungsziel, um die übergreifende Ausführung für mehrere definierte Datenbankziele zu ermöglichen. Nach dem Erstellen einer Datenbankgruppe können Datenbanken dem benutzerdefinierten Sammlungsziel zugeordnet werden.

Legen Sie die folgenden Variablen fest, um die gewünschte Konfiguration für das benutzerdefinierte Sammlungsziel anzugeben:

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### So fügen Sie Datenbanken zu einem benutzerdefinierten Datenbanksammlungsziel hinzu

Verwenden Sie das Cmdlet [**Add-AzureSqlJobChildTarget**](https://msdn.microsoft.comlibrary/mt346064.aspx), um eine Datenbank zu einer bestimmten benutzerdefinierten Sammlung hinzuzufügen.

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### Überprüfen der Datenbanken in einem benutzerdefinierten Datenbanksammlungsziel

Verwenden Sie das Cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx), um die untergeordneten Datenbanken in einem benutzerdefinierten Datenbankziel abzurufen.
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### Erstellen eines Auftrags zum Ausführen eines Skripts für ein benutzerdefiniertes Datenbanksammlungsziel

Verwenden Sie das Cmdlet [**New-AzureSqlJob**](https://msdn.microsoft.com/library/mt346078.aspx), um einen Auftrag für eine Gruppe von Datenbanken zu erstellen, die durch ein benutzerdefiniertes Datenbanksammlungsziel definiert ist. Aufträge für die elastische Datenbank erweitern den Auftrag in mehrere untergeordnete Aufträge, von denen jeder einer dem benutzerdefinierten Datenbanksammlungsziel zugeordneten Datenbank entspricht, und stellen sicher, dass das Skript für jede der Datenbanken ausgeführt wird. Um Wiederholungsresilienz sicherzustellen, müssen die Skripts idempotent sein.

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## Datenbankübergreifende Datensammlung

Sie können mithilfe eines Auftrags eine Abfrage für eine Gruppe von Datenbanken ausführen und die Ergebnisse an eine bestimmte Tabelle senden. Anschließend kann eine Abfrage für die Tabelle ausgeführt werden, um die Ergebnisse der Abfrage für die einzelnen Datenbanken anzuzeigen. Dadurch erhalten Sie eine asynchrone Methode zum übergreifenden Ausführen einer Abfrage in vielen Datenbanken. Zur Behandlung nicht erfolgreicher Versuche werden automatisch Wiederholungen ausgeführt.

Die angegebene Zieltabelle wird automatisch erstellt, sofern noch nicht vorhanden. Die neue Tabelle entspricht dem Schema des zurückgegebenen Resultsets. Gibt ein Skript mehrere Resultsets zurück, wird jeweils nur das erste an die Zieltabelle gesendet.

Das folgende PowerShell-Skript führt ein Skript aus und sammelt die Ergebnisse in einer angegebenen Tabelle. In dem Skript wird vorausgesetzt, dass ein T-SQL-Skript, das eine einzelne Ergebnismenge ausgibt, sowie ein benutzerdefiniertes Datenbanksammlungsziel erstellt wurde.

Dieses Skript verwendet das Cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx). Legen Sie die Parameter für Skript, Anmeldeinformationen und Ausführungsziel fest:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$executionCredentialName = "{Execution Credential Name}"
	$customCollectionName = "{Custom Collection Name}"
	$destinationCredentialName = "{Destination Credential Name}"
	$destinationServerName = "{Destination Server Name}"
	$destinationDatabaseName = "{Destination Database Name}"
	$destinationSchemaName = "{Destination Schema Name}"
	$destinationTableName = "{Destination Table Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### So erstellen und starten Sie einen Auftrag für Datensammlungsszenarien

Dieses Skript verwendet das Cmdlet [**Start-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346055.aspx).
 
	$job = New-AzureSqlJob -JobName $jobName 
	-CredentialName $executionCredentialName 
	-ContentName $scriptName 
	-ResultSetDestinationServerName $destinationServerName 
	-ResultSetDestinationDatabaseName $destinationDatabaseName 
	-ResultSetDestinationSchemaName $destinationSchemaName 
	-ResultSetDestinationTableName $destinationTableName 
	-ResultSetDestinationCredentialName $destinationCredentialName 
	-TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## So planen Sie einen Auftragsausführungsauslöser

Das folgende PowerShell-Skript kann zum Erstellen eines wiederkehrenden Zeitplans verwendet werden. In diesem Skript wird zwar ein Minutenintervall verwendet, [**New-AzureSqlJobSchedule**](https://msdn.microsoft.com/library/mt346068.aspx) unterstützt jedoch auch die Parameter „-DayInterval“, „-HourInterval“, „-MonthInterval“ und „-WeekInterval“. Zeitpläne, die nur einmalig ausgeführt werden sollen, können durch Angeben von „-OneTime“ erstellt werden.

Erstellen Sie einen neuen Zeitplan:

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
	-ScheduleName $scheduleName 
	-StartTime $startTime 
	Write-Output $schedule

### So lösen Sie einen nach Zeitplan ausgeführten Auftrag aus

Ein Auftragsauslöser kann so definiert werden, dass ein Auftrag gemäß einem Zeitplan ausgeführt wird. Das folgenden PowerShell-Skript kann zum Erstellen eines Auftragsauslösers verwendet werden.

Verwenden Sie [New-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346069.aspx), und legen Sie die folgenden Variablen für den gewünschten Auftrag und Zeitplan fest:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger
	-ScheduleName $scheduleName
	–JobName $jobName
	Write-Output $jobTrigger

### So entfernen Sie eine geplante Zuordnung, um die zeitplanbasierte Ausführung eines Auftrags zu beenden

Um die Fortsetzung der Auftragsausführung durch einen Auftragsauslöser aufzuheben, kann der Auftragsauslöser entfernt werden. Verwenden Sie das Cmdlet [**Remove-AzureSqlJobTrigger**](https://msdn.microsoft.com/library/mt346070.aspx), um einen Auftragsauslöser zu entfernen und die zeitplanbasierte Ausführung eines Auftrags zu beenden.

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger 
	-ScheduleName $scheduleName 
	-JobName $jobName

### Abrufen von Auftragsauslösern, die an einen Zeitplan gebunden sind

Das folgende PowerShell-Skript kann verwendet werden, um die für einen bestimmten Zeitplan registrierten Auftragsauslöser anzurufen und anzuzeigen.

	$scheduleName = "{Schedule Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
	Write-Output $jobTriggers

### So rufen Sie an einen Auftrag gebundene Auftragsauslöser ab 

Verwenden Sie [Get-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx), um die Zeitpläne für einen registrierten Auftrag abzurufen und anzuzeigen.

	$jobName = "{Job Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
	Write-Output $jobTriggers

## So erstellen Sie eine Datenschichtanwendung (Data-tier Application, DACPAC) für die datenbankübergreifende Ausführung

Informationen zur DACPAC-Erstellung finden Sie unter [Datenschichtanwendungen](https://msdn.microsoft.com/library/ee210546.aspx). Verwenden Sie für die DACPAC-Bereitstellung das Cmdlet [New-AzureSqlJobContent](https://msdn.microsoft.com/library/mt346085.aspx). Der Dienst muss Zugriff auf das DACPAC haben. Es empfiehlt sich, ein erstelltes DACPAC an Azure Storage hochzuladen und eine [Shared Access Signature](storage-dotnet-shared-access-signature-part-1.md) (SAS) für das DACPAC zu erstellen.

	$dacpacUri = "{Uri}"
	$dacpacName = "{Dacpac Name}"
	$dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
	Write-Output $dacpac

### So aktualisieren Sie eine Datenschichtanwendung (Data-tier Application, DACPAC) für die datenbankübergreifende Ausführung

Vorhandene DACPACs, die bei Aufträge für die elastische Datenbank registriert sind, können so aktualisiert werden, dass sie auf neue URIs verweisen. Verwenden Sie das Cmdlet [**Set-AzureSqlJobContentDefinition**](https://msdn.microsoft.com/library/mt346074.aspx), um den DACPAC-URI für ein vorhandendes, registriertes DACPAC zu aktualisieren:

	$dacpacName = "{Dacpac Name}"
	$newDacpacUri = "{Uri}"
	$updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
	Write-Output $updatedDacpac

## So erstellen Sie einen Auftrag zur datenbankübergreifenden Anwendung einer Datenschichtanwendung (Data-tier Application, DACPAC)

Nach der Erstellung eines DACPACs in Aufträge für die elastische Datenbank kann ein Auftrag erstellt werden, um das DACPAC übergreifend auf eine Gruppe von Datenbanken anzuwenden. Das folgende PowerShell-Skript kann verwendet werden, um einen DACPAC-Auftrag übergreifend auf eine benutzerdefinierte Sammlung von Datenbanken anzuwenden:

	$jobName = "{Job Name}"
	$dacpacName = "{Dacpac Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget 
	-CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob 
	-JobName $jobName 
	-CredentialName $credentialName 
	-ContentName $dacpacName -TargetId $target.TargetId
	Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

<!---HONumber=Nov15_HO2-->