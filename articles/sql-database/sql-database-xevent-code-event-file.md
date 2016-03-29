<properties 
	pageTitle="Code für XEvent-Ereignisdatei für SQL-Datenbank | Microsoft Azure" 
	description="Stellt ein PowerShell- und ein Transact-SQL-Skript für ein zweiphasiges Codebeispiel zur Veranschaulichung des Ereignisdateiziels in einem erweiterten Ereignis in Azure SQL-Datenbank bereit. Azure Storage ist ein erforderlicher Bestandteil in diesem Szenario." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/15/2016" 
	ms.author="genemi"/>


# Code des Ereignisdateiziels für erweiterte Ereignisse in SQL-Datenbank


Sie suchen ein vollständiges Codebeispiel als stabile Option zum Erfassen und Melden von Informationen für erweiterte Ereignisse.


In Microsoft SQL Server wird das [Ereignisdateiziel](http://msdn.microsoft.com/library/ff878115.aspx) zum Speichern von Ereignisausgaben in einer Datei auf der lokalen Festplatte verwendet. Solche Dateien sind für Azure SQL-Datenbank jedoch nicht verfügbar. Stattdessen wird das Ereignisdateiziel mithilfe des Azure Storage-Diensts unterstützt.


In diesem Thema wird ein Codebeispiel in zwei Phasen vorgestellt:


- PowerShell: Erstellen eines Azure-Speichercontainers in der Cloud.

- Transact-SQL:
 - Zuweisen des Azure-Speichercontainers zu einem Ereignisdateiziel.
 - Erstellen und Starten der Ereignissitzung usw.


## Voraussetzungen


- Ein Azure-Konto und ein Azure-Abonnement. Sie können sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) registrieren.


- Jede Datenbank, in der eine Tabelle erstellt werden kann.
 - Optional können Sie in wenigen Minuten [eine **AdventureWorksLT**-Demodatenbank erstellen](sql-database-get-started.md).


- SQL Server Management Studio ("ssms.exe"), Vorschauversion August 2015 oder eine spätere Version. Sie können "ssms.exe" in der neuesten Version wie folgt herunterladen:
 - Im Thema [Download SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx) (in englischer Sprache).
 - [Über diesen direkten Link zum Herunterladen.](http://go.microsoft.com/fwlink/?linkid=616025)
 - Microsoft empfiehlt, dass Sie die Datei "ssms.exe" in regelmäßigen Abständen aktualisieren. In einigen Fällen wird "ssms.exe" monatlich aktualisiert.


- Die [Azure PowerShell-Module](http://go.microsoft.com/?linkid=9811175) müssen installiert sein.
 - Die Module umfassen verschiedene Befehle, z. B. **New-AzureStorageAccount**.


## Phase 1: PowerShell-Code für den Azure-Speichercontainer


Dieser PowerShell-Code wird in Phase 1 des zweiphasigen Codebeispiels erstellt.

Das Skript beginnt mit Befehlen zum Bereinigen nach einer möglichen vorherigen Ausführung und ist so konzipiert, dass es erneut ausgeführt werden kann.



1. Fügen Sie das PowerShell-Skript in einem einfachen Texteditor (z. B. Editor) ein, und speichern Sie es als Datei mit der Erweiterung **.ps1**.

2. Starten Sie PowerShell ISE als Administrator.

3. Geben Sie an der Eingabeaufforderung <br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>ein, und drücken Sie dann die EINGABETASTE.

4. Öffnen Sie in PowerShell ISE Ihre Datei mit der Erweiterung **.ps1**. Führen Sie das Skript aus.

5. Mit dem Skript wird zunächst ein Fenster geöffnet, in dem Sie sich bei Azure anmelden.
 - Wenn Sie das Skript ohne Unterbrechung der Sitzung erneut ausführen, können Sie den **Add-AzureAccount**-Befehl auf einfache Weise auskommentieren.


![PowerShell ISE mit installiertem Azure-Modul, bereit für die Ausführung des Skripts.][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

$subscriptionName       = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken      = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean-up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


Beachten Sie die benannten Werte, die beim Beenden des PowerShell-Skripts ausgegeben werden. Diese Werte müssen Sie im Transact-SQL-Skript bearbeiten, das als Phase 2 folgt.


## Phase 2: Transact-SQL-Code zum Verwenden des Azure-Speichercontainers


- In Phase 1 dieses Codebeispiels haben Sie ein PowerShell-Skript zum Erstellen eines Azure-Speichercontainers ausgeführt.
- In Phase 2 muss das folgende Transact-SQL-Skript nun diesen Container verwenden.


Das Skript beginnt mit Befehlen zum Bereinigen nach einer möglichen vorherigen Ausführung und ist so konzipiert, dass es erneut ausgeführt werden kann.


Beim Beenden des PowerShell-Skripts wurden einige benannte Werte ausgegeben. Sie müssen das Transact-SQL-Skript so bearbeiten, dass diese Werte verwendet werden. Die Bearbeitungspunkte finden Sie, wenn Sie im Transact-SQL-Skript nach **TODO** suchen.


1. Öffnen Sie SQL Server Management Studio ("ssms.exe").

2. Stellen Sie eine Verbindung mit Ihrer Azure SQL-Datenbank her.

3. Klicken Sie, um einen neuen Abfragebereich zu öffnen.

4. Fügen Sie das folgende Transact-SQL-Skript in den Abfragebereich ein.

5. Suchen Sie alle Vorkommen von **TODO** im Skript, und nehmen Sie jeweils die entsprechende Bearbeitung vor.

6. Speichern Sie die Änderungen, und führen Sie dann das Skript aus.


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;

GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
	(SELECT * FROM sys.objects
		WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
	DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
	EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
	EmployeeId           int                not null  identity(1,1),
	EmployeeKudosCount   int                not null  default 0,
	EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
	VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
	(SELECT * FROM sys.symmetric_keys
		WHERE symmetric_key_id = 101)
BEGIN
	CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
	(SELECT * FROM sys.database_scoped_credentials
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
	DROP DATABASE SCOPED CREDENTIAL
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
	DATABASE SCOPED
	CREDENTIAL
		-- use '.blob.',   and not '.queue.' or '.table.' etc.
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
	WITH
		IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
		-- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
		SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
	;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
	(SELECT * from sys.database_event_sessions
		WHERE name = 'gmeventsessionname240b')
BEGIN
	DROP
		EVENT SESSION
			gmeventsessionname240b
	    ON DATABASE;
END
GO


CREATE
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE

	ADD EVENT
		sqlserver.sql_statement_starting
			(
			ACTION (sqlserver.sql_text)
			WHERE statement LIKE 'UPDATE gmTabEmployee%'
			)
	ADD TARGET
		package0.event_file
			(
			-- TODO: Assign AzureStorageAccount name, and the associated Container name.
			-- Also, tweak the .xel file name at end, if you like.
			SET filename =
				'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
			)
	WITH
		(MAX_MEMORY = 10 MB,
		MAX_DISPATCH_LATENCY = 3 SECONDS)
	;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE
	STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 2
	WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 13
	WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE
	STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
		*, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
		CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
	FROM
		sys.fn_xe_file_target_read_file
			(
				-- TODO: Fill in Storage Account name, and the associated Container name.
				'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
				null, null, null
			);
GO


-------------- Step 6.  Clean up. ----------

DROP
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
	-- TODO: Assign AzureStorageAccount name, and the associated Container name.
	[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
	;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


Wenn das Ziel bei der Ausführung nicht angefügt wird, müssen Sie die Ereignissitzung beenden und dann erneut starten:


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## Ausgabe


Klicken Sie auf eine Zelle unter der Spaltenüberschrift **event\_data\_XML**, nachdem das Transact-SQL-Skript abgeschlossen ist. Es wird ein **<event>**-Element mit einer UPDATE-Anweisung angezeigt.

Hier sehen Sie ein **<event>**-Element, das beim Testen generiert wurde:


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```





&nbsp;


## Konvertieren des Codebeispiels für die Ausführung in SQL Server


Angenommen, Sie möchten das vorhergehende Transact-SQL-Codebeispiel in Microsoft SQL Server ausführen.


- Der Einfachheit halber möchten Sie die Verwendung des Azure-Speichercontainers vollständig durch eine einfache Datei, ersetzen, z. B. durch **C:\\myeventdata.xel**. Die Datei wird auf die lokale Festplatte des Computers geschrieben, der SQL Server hostet.


- Sie benötigen keine Transact-SQL-Anweisungen für **CREATE MASTER KEY** und **CREATE CREDENTIAL**.


- In der **ADD TARGET**-Klausel der **CREATE EVENT SESSION**-Anweisung ersetzen Sie den für **filename=** zugewiesenen HTTP-Wert durch eine vollständige Pfadzeichenfolge wie z. B. **C:\\myfile.xel**.
 - Es wird kein Azure-Speicherkonto benötigt.


## Weitere Informationen


Hauptthemen für erweiterte Ereignisse in Azure SQL-Datenbank:

- [Erweitere Ereignisse in SQL-Datenbank](sql-database-xevent-db-diff-from-svr.md): das Hauptthema für erweiterte Ereignisse in Azure SQL-Datenbank.
 - In diesem Thema werden Aspekte von erweiterten Ereignissen, die sich zwischen Azure SQL-Datenbank und Microsoft SQL Server unterscheiden, einander gegenübergestellt.


- [Ringpuffer-Zielcode für erweitere Ereignisse in SQL-Datenbank](sql-database-xevent-code-ring-buffer.md): enthält ein weiteres Codebeispiel, das sich schnell und einfach anwenden lässt, jedoch eher für kurze Tests vorgesehen und bei umfangreicherer Aktivität weniger stabil ist.


Weitere Informationen zu Konten und Containern im Azure Storage-Dienst finden Sie in den folgenden Artikeln:

- [Verwenden des Blob-Speichers mit .NET](storage-dotnet-how-to-use-blobs.md/)
- [Benennen von Containern, BLOBs und Metadaten und Verweisen auf diese](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Arbeiten mit dem Stammcontainer](http://msdn.microsoft.com/library/azure/ee395424.aspx)


<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

<!---HONumber=AcomDC_0316_2016-->