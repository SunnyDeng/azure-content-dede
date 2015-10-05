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
	ms.date="09/17/2015" 
	ms.author="ddove; sidneyh" />

# Erstellen und Verwalten von Aufträgen für die elastische SQL-Datenbank mithilfe von PowerShell (Vorschau)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)

## Übersicht

Mithilfe des Features **Aufträge für die elastische Datenbank** (Vorschau) können Sie ein Transact-SQL-Skript (T-SQL) zuverlässig ausführen oder ein DACPAC auf eine gesamte Gruppe von Datenbanken anwenden, einschließlich einer benutzerdefinierten Sammlung von Datenbanken, aller Datenbanken in einem [elastischen Datenbankpool (Vorschau)](sql-database-elastic-pool.md) oder einem Shardset (das mithilfe der [Clientbibliothek für die elastische Datenbank](sql-database-elastic-database-client-library.md) erstellt wurde). Im Vorschaustadium stellt **Aufträge für die elastische Datenbank** derzeit einen vom Kunden gehosteten Azure Cloud Service dar, der die Ad-Hoc-Ausführung und die geplante Ausführung von Verwaltungsaufgaben ermöglicht, die als Aufträge bezeichnet werden. Mithilfe dieses Features können Sie auf einfache und zuverlässige Weise eine große Anzahl von Azure SQL-Datenbanken im Maßstab durch Ausführen von Transact-SQL-Skripts zum Durchführen von Verwaltungsvorgängen, wie Schemaänderungen, Verwaltung von Anmeldeinformationen, Aktualisierung von Verweisdaten, Sammlung von Leistungsdaten oder Erfassung von Mandantentelemetrie (Kundentelemetrie) verwalten. Weitere Informationen zu elastischen Datenbankaufträgen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).

Die PowerShell-APIs für **Aufträge für die elastische Datenbank** geben Ihnen die Flexibilität, zu definieren, für welche Gruppe von Datenbanken welche Skripts ausgeführt werden. Aktuell weist die im Azure-Portal verfügbare Funktionalität der **Aufträge für die elastische Datenbank** eine eingeschränkte Funktionalität auf und ist auf die Ausführung in elastischen Datenbankpools beschränkt.

**Aufträge für die elastische Datenbank** (Vorschau) verwendet mehrere Azure-Komponenten, um die auszuführenden Aufträge zu definieren, den Zeitpunkt ihrer Ausführung zu definieren, die Aufträge auszuführen, ihre erfolgreiche oder fehlerhafte Ausführung nachzuverfolgen und optional ein Ergebnisziel für die Ergebnisse zurückgebenden Abfragen anzugeben. Da die in dieser Vorschau enthaltenen Powershell-APIs gegenüber der ursprünglichen Vorschau über das Portal zusätzliche Funktionen umfassen, wird die Installation der neuesten Komponenten der **Aufträge für die elastische Datenbank** empfohlen. Wenn sie bereits installiert sind, können Sie einfach ein Upgrade der Komponenten der **Aufträge für die elastische Datenbank** durchführen. Weitere Informationen zur Installation von [Nuget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.Jobs) finden Sie unter [Installieren der Komponenten für Aufträge für die elastische Datenbank](sql-database-elastic-jobs-service-installation.md).

In diesem Artikel erfahren Sie, wie Sie alle erforderlichen Elemente mit Ausnahme des Azure-Abonnements erhalten, die Sie zum Erstellen und Verwalten der **Aufträge für die elastische Datenbank** benötigen. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf „Kostenlose Testversion“ klicken. Lesen Sie anschließend den Artikel weiter. Dieses Thema baut auf dem Beispiel unter [Erste Schritte mit den Tools für die elastische Datenbank](sql-database-elastic-scale-get-started.md) auf. Nach dem Abschluss erfahren Sie, wie Sie Aufträge zum Durchführen von Verwaltungsvorgängen für eine Gruppe von Shard-Datenbanken, die durch ein **Shard-Set** definiert sind, und alternativ für eine benutzerdefinierte Sammlung von Datenbanken erstellen und verwalten.

## Voraussetzungen
* Ein Azure-Abonnement. Eine kostenlose Testversion finden Sie unter [Einen Monat kostenlos testen](http://azure.microsoft.com/pricing/free-trial/).
* Das PowerShell-Paket **Aufträge für die elastische Datenbank** muss zuerst heruntergeladen und importiert werden, und die Komponenten der Aufträge für die elastische Datenbank müssen installiert werden. Führen Sie diese Schritte aus: [Installieren von Aufträgen für die elastische Datenbank](sql-database-elastic-jobs-service-installation.md)
* Azure PowerShell, Version >= 0.8.16. Installieren Sie die neueste Version (0.9.5) mithilfe des [Webplattform-Installationsprogramms](http://go.microsoft.com/fwlink/p/?linkid=320376). Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

### Auswählen des Azure-Abonnements

Zur Auswahl des Abonnements benötigen Sie Ihre Abonnement-ID (**-SubscriptionId**) oder den Abonnementnamen (**-SubscriptionName**). Falls Sie über mehrere Abonnements verfügen, können Sie sie über das Cmdlet **Get-AzureSubscription** abrufen und die gewünschten Abonnementinformationen aus dem ResultSet kopieren. Nachdem Sie Ihre Abonnementinformationen ermittelt haben, führen Sie das folgende Cmdlet aus, um das betreffende Abonnement als Standard festzulegen, insbesondere als Ziel für das Erstellen und Verwalten von Aufträgen:

	Select-AzureSubscription -SubscriptionId {SubscriptionID}

Die [PowerShell-ISE](https://technet.microsoft.com/library/dd315244.aspx) wird zum Entwickeln und Ausführen von PowerShell-Skripts für die Ausführung in Aufträgen für die elastische Datenbank empfohlen.

## Auftragsobjekte für die elastische Datenbank

In der folgenden Tabelle sind alle Objekttypen von **Aufträgen für die elastische Datenbank** zusammen mit einer Beschreibung und den relevanten PowerShell-APIs aufgelistet.

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
**Aufträge für die elastische Datenbank** ermöglicht das Ausführen von Transact-SQL (T-SQL)-Skripts oder die Anwendung von DACPACs für eine gesamte Gruppe von Datenbanken. Wenn ein Auftrag für die übergreifende Ausführung für eine Gruppe von Datenbanken gesendet wird, „erweitern“ die Aufträge für die elastische Datenbank den Auftrag in untergeordnete Aufträge, von denen jeder Einzelauftrag die angeforderte Ausführung für eine einzelne Datenbank der Gruppe übernimmt.
 
Dies ist eine Liste der aktuell unterstützten Gruppentypen:

* [Shardzuordnung](sql-database-elastic-scale-shard-map-management.md): Wenn ein Auftrag gesendet wird, dessen Ziel eine Shardzuordnung ist, fragen die Aufträge zunächst die Shardzuordnung ab, um deren aktuellen Shardsatz zu bestimmen, und erweitern dann den Auftrag in untergeordnete Aufträge, die den einzelnen Shards der Shardzuordnung entsprechen.
* Benutzerdefinierte Sammlung: Angabe, die auf einen benutzerdefinierten Satz von Datenbanken verweist. Wenn ein Auftrag gesendet wird, der eine benutzerdefinierte Sammlung zum Ziel hat, wird der Auftrag in untergeordnete Aufträge erweitert, die den einzelnen aktuell in der benutzerdefinierten Sammlung definierten Datenbanken entsprechen.

## Angeben der Verbindung für Aufträge für die elastische Datenbank
Nach dem Laden des PowerShell-Moduls muss die Verbindung für die *Steuerdatenbank* der Aufträge für die elastische Datenbank eingerichtet werden, bevor die Auftrags-APIs verwendet werden. Durch das Aufrufen dieses Cmdlets wird ein Anmeldeinformationsfenster aufgerufen, das die bei der Installation der Aufträge für die elastische Datenbank angegebene Kombination aus Benutzername und Kennwort anfordert. Alle in diesem Thema gegebenen Beispiele gehen davon aus, dass dieser erste Schritt bereits ausgeführt wurde.

Öffnen einer Verbindung mit den Aufträgen für die elastische Datenbank:

	Use-AzureSqlJobConnection -CurrentAzureSubscription 

## Verschlüsselte Anmeldeinformationen innerhalb der Aufträge für die elastische Datenbank
Datenbankanmeldeinformationen können in die *Steuerdatenbank* der Aufträge für die elastische Datenbank mit verschlüsseltem Kennwort eingefügt werden. Es ist erforderlich, Anmeldeinformationen zu speichern, um die Ausführung von Aufträgen zu einem späteren Zeitpunkt zu ermöglichen, auch unter Verwendung von Auftragszeitplänen.
 
Verschlüsselung funktioniert mithilfe eines Zertifikats, das im Rahmen des Installationsskripts erstellt wird. Das Installationsskript erstellt das Zertifikat und lädt es zwecks Entschlüsselung der gespeicherten verschlüsselten Kennwörter auf den Azure Cloud Service hoch. Der Azure Cloud Service speichert den öffentlichen Schlüssel anschließend in der *Steuerdatenbank* der Aufträge für die elastische Datenbank, was der PowerShell-API oder der Azure-Portalschnittstelle die Verschlüsselung eines übergebenen Kennworts ermöglicht, ohne dass das Zertifikat dazu lokal installiert sein muss.
 
Zwar sind die Kennwörter der Anmeldeinformationen verschlüsselt und vor Benutzern mit schreibgeschütztem Zugriff auf Auftragsobjekte der elastischen Datenbank sicher, es ist jedoch für böswillige Benutzer mit Lese-/Schreibzugriff auf Auftragsobjekte der elastischen Datenbank möglich, ein Kennwort zu extrahieren. Die Anmeldeinformationen sind für die erneute Verwendung bei der Auftragsausführung bestimmt. Die Anmeldeinformationen werden beim Einrichten der Verbindungen an die Zieldatenbanken übergeben. Zurzeit unterliegen die für die einzelnen Anmeldeinformationen verwendeten Zieldatenbanken keinen Einschränkungen, daher ist es für einen böswilligen Benutzer möglich, ein Datenbankziel für eine seiner Kontrolle unterliegende Datenbank hinzuzufügen und anschließend einen Auftrag mit dieser Datenbank als Ziel zu starten, um Kenntnis vom Kennwort der Anmeldeinformationen zu erhalten.

Für Aufträge der elastischen Datenbank wurden bewährte Sicherheitsverfahren definiert:

* Schränken Sie die Nutzung der APIs auf einen vertrauenswürdigen Personenkreis ein.
* Die Anmeldeinformationen sollten immer nur mit den für die Ausführung von Auftragsaufgaben erforderlichen Mindestberechtigungen ausgestattet sein. Weitere Informationen finden Sie im MSDN-Artikel [Autorisierung und Berechtigungen in SQL Server](https://msdn.microsoft.com/library/bb669084.aspx).

### Erstellen von verschlüsselten Anmeldeinformationen für die übergreifende Ausführung von Aufträgen für Datenbanken
Zur Erstellung neuer verschlüsselter Anmeldeinformationen fordert das Cmdlet „Get-Credential“ zur Eingabe von Benutzername und Kennwort auf, die an das **New-AzureSqlJobCredential** übergeben werden können.

	$credentialName = "{Credential Name}"
	$databaseCredential = Get-Credential
	$credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
	Write-Output $credential

### Aktualisieren von Anmeldeinformationen
Verwenden Sie zum Aktualisieren vorhandener Anmeldeinformationen für den Fall, dass Kennwörter sich ändern, das Cmdlet **Set-AzureSqlJobCredential**, und legen Sie den Parameter **CredentialName** fest.

	$credentialName = "{Credential Name}"
	Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## Definieren eines Shardzuordnungsziels für die elastische Datenbank
Führen Sie einen Auftrag für alle Datenbanken in einem Shardsatz (der mithilfe der [Clientbibliothek für die elastische Datenbank](sql-database-elastic-database-client-library.md) erstellt wurde) aus, und verwenden Sie dazu eine Shardzuordnung als Datenbankziel. Dieses Beispiel setzt voraus, dass Sie mithilfe der Clientbibliothek für die elastische Datenbank eine Anwendung erstellen, die Sharding unterstützt. Laden Sie das Beispiel [Erste Schritte mit den Tools für die elastische Datenbank](sql-database-elastic-scale-get-started.md) herunter, und führen Sie es aus.

###Erstellen eines Shardzuordnungs-Managers mithilfe der Beispiel-App

Hier erstellen Sie einen Shardzuordnungs-Manager und mehrere Shards und fügen anschließend Daten in die Shards ein. Wenn Sie bereits Shards eingerichtet haben, können Sie die folgenden Schritte überspringen und zum nächsten Abschnitt wechseln.

1. Erstellen Sie die Beispielanwendung **Erste Schritte mit den Tools für die elastische Datenbank**, und führen Sie sie aus. Führen Sie die Schritte bis zu Schritt 7 im Abschnitt [Herunterladen und Ausführen der Beispiel-App](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools) aus. Am Ende von Schritt 7 wird die folgende Eingabeaufforderung angezeigt:

	![Eingabeaufforderung][1]

2.  Geben Sie im Befehlsfenster "1" ein, und drücken Sie die **EINGABETASTE**. Dadurch wird der Shardzuordnungs-Manager erstellt, und es werden zwei Shards zum Server hinzugefügt. Geben Sie dann "3" ein, und drücken Sie die **EINGABETASTE**. Wiederholen Sie den Vorgang viermal. Dadurch werden Beispieldatenzeilen in die Shards eingefügt.
  
3.  Im [Azure-Vorschauportal](https://portal.azure.com) sollten drei neue Datenbanken auf dem V12-Server angezeigt werden:

	![Visual Studio-Bestätigung][2]

Erstellen Sie jetzt ein Shardzuordnungsziel mithilfe des Cmdlets **New-AzureSqlJobTarget**. Die Datenbank des Shardzuordnungs-Managers muss dann als Datenbankziel festgelegt werden, anschließend wird die bestimmte Shardzuordnung als Ziel angegeben.

	$shardMapCredentialName = "{Credential Name}"
	$shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
	$shardMapDatabaseServerName = "{ShardMapServerName}"
	$shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
	$shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
	$shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
	Write-Output $shardMapTarget

## Erstellen eines T-SQL-Skripts für die datenbankübergreifende Ausführung

Beim Erstellen von T-SQL-Skripts für die Ausführung empfiehlt es sich dringend, sie idempotent und widerstandsfähig gegen Fehler zu gestalten. Aufträge für die elastische Datenbank versuchen bei jedem auftretenden Fehler, die Skriptausführung zu wiederholen, unabhängig von der Klassifikation des Fehlers.

Verwenden Sie das Cmdlet **New-AzureSqlJobContent**, um ein Skript zu erstellen und für die Ausführung zu speichern, und legen Sie die Parameter **-ContentName** und **-CommandText** fest.

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
Wenn das T-SQL-Skript in einer Datei definiert ist, kann das folgende Skript zum Importieren des Skripts verwendet werden:

	$scriptName = "My Script Imported from a File"
	$scriptPath = "{Path to SQL File}"
	$scriptCommandText = Get-Content -Path $scriptPath
	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Aktualisieren eines T-SQL-Skripts für die datenbankübergreifende Ausführung  

Das folgende PowerShell-Skript kann verwendet werden, um den T-SQL-Befehlstext für ein vorhandenes Skript zu aktualisieren.

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

### Aktualisieren der Definition eines vorhandenen Skripts

	Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

##Erstellen eines Auftrags zur übergreifenden Ausführung eines Skripts für eine Shardzuordnung

Das folgende PowerShell-Skript kann für das Starten der Ausführung eines Auftrags für jeden Shard in einer Shardzuordnung mit elastischer Skalierung verwendet werden.

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

##Ausführen eines Auftrags 

Das folgende PowerShell-Skript kann verwendet werden, um einen vorhandenen Auftrag auszuführen:

Aktualisieren Sie die folgende Variable, sodass sie den gewünschten Auftragsnamen für die Ausführung angibt:

	$jobName = "{Job Name}"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## Abrufen des Ausführungsstatus eines einzelnen Auftrags

Verwenden Sie das **Get-AzureSqlJobExecution**-Cmdlet, und legen Sie den **JobExecutionId**-Parameter fest, um den Status der Auftragsausführung anzuzeigen.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
	Write-Output $jobExecution

Verwenden Sie das gleiche Cmdlet **Get-AzureSqlJobExecution** mit dem Parameter **IncludeChildren**, um den Ausführungsstatus untergeordneter Aufträge anzuzeigen, insbesondere den spezifischen Status für die Ausführung der einzelnen Aufträge auf jeder der im Auftrag enthaltenen Datenbanken.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## Übergreifendes Anzeigen des Ausführungsstatus über mehrere Aufträge

Das Cmdlet **Get-AzureSqlJobExecution** weist mehrere optionale Parameter auf, die zum Anzeigen der Ausführung mehrerer Aufträge verwendet werden können, die anhand der angegebenen Parameter gefiltert werden. Die folgenden Beispiele zeigen einige der möglichen Verwendungsweisen von „Get-AzureSqlJobExecution“:

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

## Abrufen von Ausführungsfehlern innerhalb von Auftragsaufgaben

Das Objekt „JobTaskExecution“ enthält eine Eigenschaft für den Lebenszyklus der Aufgabe und darüber hinaus eine Message-Eigenschaft. Bei einem Ausführungsfehler einer Auftragsaufgabe wird die Eigenschaft „Lifecycle“ auf *Failed* festgelegt, und die Eigenschaft „Message“ übernimmt die resultierende Ausnahmenachricht und deren Stapel als Inhalt. Wenn ein Auftrag nicht erfolgreich abgeschlossen wurde, müssen die Details der Auftragsaufgaben angezeigt werden, die für einen bestimmten Auftrag nicht erfolgreich abgeschlossen wurden.

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## Warten auf den Abschluss einer Auftragsausführung

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
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
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

1. Abbruch aktuell ausgeführter Aufgaben: Wenn ein Abbruch erkannt wird, während eine Aufgabe aktuell ausgeführt wird, wird ein Abbruch innerhalb des aktuell ausgeführten Aspekts der Aufgabe versucht. Beispiel: Wenn ein versuchter Abbruch sich auf eine aktuell ausgeführte Abfrage mit langer Laufzeit bezieht, wird versucht, die Abfrage abzubrechen.
2. Stornierung von Aufgabenwiederholungen: Wenn ein Abbruch vom Steuerthread erkannt wird, bevor die Ausführung einer Aufgabe gestartet wurde, verhindert der Steuerthread das Starten der Aufgabe und erklärt die Anforderung für storniert.

Wenn für einen übergeordneten Auftrag ein Auftragsabbruch angefordert wird, wird die Abbruchanforderung für den übergeordneten Auftrag und für alle seine untergeordneten Aufträge berücksichtigt.
 
Verwenden Sie zum Senden von Abbruchanforderungen das Cmdlet **Stop-AzureSqlJobExecution**, und legen Sie den Parameter **JobExecutionId** fest.

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## Löschen eines Auftrags anhand seines Namens und seines Verlaufs

Aufträge für die elastische Datenbank unterstützen die asynchrone Löschung von Aufträgen. Ein Auftrag kann für die Löschung markiert werden, und das System löscht den Auftrag und seinen gesamten Verlauf, nachdem die gesamte Ausführung von Unteraufträgen für den Auftrag abgeschlossen wurde. Das System bricht aktiv ausgeführte Aufträge nicht automatisch ab.

Stattdessen muss „Stop-AzureSqlJobExecution“ aufgerufen werden, um die Ausführung aktiver Aufträge abzubrechen.

Verwenden Sie zum Auslösen der Löschung von Aufträgen das Cmdlet **Remove-AzureSqlJob**, und legen Sie den Parameter **JobName** fest.

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## Erstellen eines benutzerdefinierten Datenbankziels
In Aufträgen für die elastische Datenbank können benutzerdefinierte Datenbankziele definiert werden, die entweder direkt für die Ausführung oder für die Aufnahme in eine benutzerdefinierte Datenbankgruppe verwendet werden können. Da **elastische Datenbankpools** durch die PowerShell-APIs noch nicht direkt unterstützt werden, erstellen Sie einfach ein benutzerdefiniertes Datenbankziel und ein benutzerdefiniertes Datenbanksammlungsziel, das sämtliche Datenbanken im Pool enthält.

Legen Sie die folgenden Variablen fest, um die gewünschten Datenbankinformationen anzugeben:

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## Erstellen eines benutzerdefinierten Datenbanksammlungsziels
Um die übergreifende Ausführung für mehrere definierte Datenbankziele zu ermöglichen, kann ein benutzerdefiniertes Datenbanksammlungsziel definiert werden. Nach dem Erstellen einer Datenbankgruppe können Datenbanken dem benutzerdefinierten Sammlungsziel zugeordnet werden.

Legen Sie die folgenden Variablen fest, um die gewünschte Konfiguration für das benutzerdefinierte Sammlungsziel anzugeben:

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### Hinzufügen von Datenbanken zu einem benutzerdefinierten Datenbanksammlungsziel

Datenbankziele können benutzerdefinierten Datenbanksammlungszielen zugeordnet werden, um eine Gruppe von Datenbanken zu erstellen. Jeder erstellte Auftrag mit einem benutzerdefinierten Datenbanksammlungsziel als Ziel wird bei der Ausführung auf die der Gruppe zugeordneten Datenbanken erweitert.

Fügen Sie die gewünschte Datenbank einer bestimmten benutzerdefinierten Sammlung hinzu:

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### Überprüfen der Datenbanken in einem benutzerdefinierten Datenbanksammlungsziel

Verwenden Sie das Cmdlet **Get-AzureSqlJobTarget**, um die untergeordneten Datenbanken in einem benutzerdefinierten Datenbankziel abzurufen.
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### Erstellen eines Auftrags zum Ausführen eines Skripts für ein benutzerdefiniertes Datenbanksammlungsziel

Verwenden Sie das Cmdlet **New-AzureSqlJob**, um einen Auftrag für eine Gruppe von Datenbanken zu erstellen, die durch ein benutzerdefiniertes Datenbanksammlungsziel definiert ist. Aufträge für die elastische Datenbank erweitern den Auftrag in mehrere untergeordnete Aufträge, von denen jeder einer dem benutzerdefinierten Datenbanksammlungsziel zugeordneten Datenbank entspricht, und stellen sicher, dass das Skript für jede der Datenbanken ausgeführt wird. Um Wiederholungsresilienz sicherzustellen, müssen die Skripts idempotent sein.

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## Datenbankübergreifende Datensammlung

**Aufträge für die elastische Datenbank** unterstützt das übergreifende Ausführen einer Abfrage für eine Gruppe von Datenbanken und sendet die Ergebnisse an eine Tabelle einer angegebenen Datenbank zurück. Anschließend kann eine Abfrage für die Tabelle ausgeführt werden, um die Ergebnisse der Abfrage für die einzelnen Datenbanken anzuzeigen. Dadurch wird ein asynchrones Verfahren zum übergreifenden Ausführen einer Abfrage in vielen Datenbanken bereitgestellt. Auftretende Fehler, wie die vorübergehende Nichtverfügbarkeit einer Datenbank, werden automatisch mithilfe von Wiederholungsversuchen behandelt.

Die angegebene Zieltabelle wird automatisch erstellt, wenn sie noch nicht vorhanden ist, und entspricht dem Schema der zurückgegebenen Ergebnismenge. Wenn bei einer Skriptausführung mehrere Ergebnismengen zurückgegeben werden, sendet Aufträge für die elastische Datenbank nur die erste an die angegebene Zieltabelle.

Das folgende PowerShell-Skript kann verwendet werden, um ein Skript auszuführen, das die Ergebnisse in einer angegebenen Tabelle sammelt. Das Skript geht davon aus, dass ein T-SQL-Skript erstellt wurde, das eine einzelne Ergebnismenge ausgibt, und ein benutzerdefiniertes Datenbanksammlungsziel erstellt wurde.

Legen Sie die folgenden Werte fest, um das gewünschte Skript, die Anmeldeinformationen und das Ausführungsziel anzugeben:

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

### Erstellen und Starten eines Auftrags für Datensammlungsszenarien
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## Erstellen eines Zeitplans für die Auftragsausführung mithilfe eines Auftragsauslösers

Das folgende PowerShell-Skript kann zum Erstellen eines wiederkehrenden Zeitplans verwendet werden. Dieses Skript verwendet ein Minutenintervall, „New-AzureSqlJobSchedule“ unterstützt aber außerdem auch die Parameter „-DayInterval“, „-HourInterval“, „-MonthInterval“ und „-WeekInterval“. Zeitpläne, die nur einmalig ausgeführt werden sollen, können durch Angeben von „-OneTime“ erstellt werden.

Erstellen Sie einen neuen Zeitplan:

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
	Write-Output $schedule

### Erstellen eines Auftragsauslösers, um einen Auftrag nach einem Zeitplan auszuführen

Ein Auftragsauslöser kann so definiert werden, dass ein Auftrag gemäß einem Zeitplan ausgeführt wird. Das folgenden PowerShell-Skript kann zum Erstellen eines Auftragsauslösers verwendet werden.

Legen Sie die folgenden Variablen entsprechend dem gewünschten Auftrag und Zeitplan fest:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
	Write-Output $jobTrigger

### Entfernen einer Zeitplanzuordnung, um die Ausführung eines Auftrags gemäß einem Zeitplan zu beenden

Um die Fortsetzung der Auftragsausführung durch einen Auftragsauslöser aufzuheben, kann der Auftragsauslöser entfernt werden. Entfernen Sie einen Auftragsauslöser, um die fortlaufende Ausführung eines Auftrags gemäß einem Zeitplan zu beenden, mithilfe des Cmdlets **Remove-AzureSqlJobTrigger**.

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName

### Abrufen von Auftragsauslösern, die an einen Zeitplan gebunden sind

Das folgende PowerShell-Skript kann verwendet werden, um die für einen bestimmten Zeitplan registrierten Auftragsauslöser anzurufen und anzuzeigen.

	$scheduleName = "{Schedule Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
	Write-Output $jobTriggers

### Abrufen der an einen Auftrag gebundenen Auftragsauslöser 

Das folgende PowerShell-Skript kann verwendet werden, um Zeitpläne abzurufen und anzuzeigen, die einen registrierten Auftrag enthalten.

	$jobName = "{Job Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
	Write-Output $jobTriggers

## Erstellen eines DACPACs (Data-tier Application Deployment, Anwendungsbereitstellung auf Datenebene) für die datenbankübergreifende Ausführung

Aufträge für die elastische Datenbank kann verwendet werden, um ein DACPAC (Anwendungsbereitstellung auf Datenebene) für eine Gruppe von Datenbanken bereitzustellen. Informationen zum Erstellen von DACPACs finden Sie in dieser Dokumentation. Damit Aufträge für die elastische Datenbank ein DACPAC für eine Gruppe von Datenbanken bereitstellen kann, muss das DACPAC für den Dienst zugänglich sein. Es empfiehlt sich, ein erstelltes DACPAC auf Azure Storage hochzuladen und einen signierten URI für das DACPAC zu erstellen.

Das folgende PowerShell-Skript kann verwendet werden, um ein DACPAC in Aufträge für die elastische Datenbank einzufügen:

	$dacpacUri = "{Uri}"
	$dacpacName = "{Dacpac Name}"
	$dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
	Write-Output $dacpac

### Aktualisieren eines DACPACs (Data-tier Application Deployment) für die datenbankübergreifende Ausführung

Vorhandene DACPACs, die bei Aufträge für die elastische Datenbank registriert sind, können so aktualisiert werden, dass sie auf neue URIs verweisen. Das folgende PowerShell-Skript kann verwendet werden, um den DACPAC-URI für ein vorhandenes registriertes DACPAC zu aktualisieren:

	$dacpacName = "{Dacpac Name}"
	$newDacpacUri = "{Uri}"
	$updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
	Write-Output $updatedDacpac

## Erstellen eines Auftrags zur datenbankübergreifenden Anwendung eines DACPACs (Data-tier Application Deployment)

Nach der Erstellung eines DACPACs in Aufträge für die elastische Datenbank kann ein Auftrag erstellt werden, um das DACPAC übergreifend auf eine Gruppe von Datenbanken anzuwenden. Das folgende PowerShell-Skript kann verwendet werden, um einen DACPAC-Auftrag übergreifend auf eine benutzerdefinierte Sammlung von Datenbanken anzuwenden:

	$jobName = "{Job Name}"
	$dacpacName = "{Dacpac Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $dacpacName -TargetId $target.TargetId
	Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

<!---HONumber=Sept15_HO4-->