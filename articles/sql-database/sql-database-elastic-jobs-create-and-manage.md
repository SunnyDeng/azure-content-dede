<properties
	pageTitle="Erstellen und Verwalten von elastischen Datenbankaufträgen"
	description="Führen Sie die Schritte zur Erstellung und Verwaltung eines elastischen Datenbankauftrags aus."
	services="sql-database"
	documentationCenter=""
	manager="jhubbard"
	authors="sidneyh"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/25/2015"
	ms.author="sidneyh"/>

# Erstellen und Verwalten von elastischen Datenbankaufträgen

**Elastische Datenbankpools** bieten ein vorhersagbares Modell zur Bereitstellung einer großen Anzahl von Datenbanken. Sie können die minimalen Datendurchsatzeinheiten (DTUs) für jede Datenbank zu einem bestimmten Preis festlegen. Das Verwalten von häufig verwendeten Objekten in diesen Datenbanken erledigen Sie am einfachsten mit **elastischen Datenbankaufträgen**. Der Dienst ermöglicht es Ihnen, T-SQL-Skripts für alle Datenbanken im Pool in einem einzigen Vorgang auszuführen. Beispielsweise können Sie eine Richtlinie für jede Datenbank festlegen, sodass nur Personen mit den richtigen Anmeldeinformationen zum Anzeigen vertraulicher Daten berechtigt sind.

## Voraussetzungen

* Ein Azure-Abonnement. Eine kostenlose Testversion finden Sie unter [Einen Monat kostenlos testen](http://azure.microsoft.com/pricing/free-trial/).
* Ein elastischer Datenbankpool. Siehe [Informationen zu elastischen Datenbankpools](sql-database-elastic-pool.md)
* Installation von Dienstkomponenten für elastische Datenbankaufträge. Siehe [Installieren des Diensts für elastische Datenbankaufträge](sql-database-elastic-jobs-service-installation.md).

## Erstellen von Aufträgen

1. Klicken Sie auf dem Blatt für elastische Datenbankauftragspools auf **Auftrag erstellen**.
2. Geben Sie den Benutzernamen und das Kennwort des Datenbankadministrators (der bei der Installation der Aufträge erstellt wurde) für die Auftragsverwaltungs-Datenbank (Metadatenspeicher für Aufträge) ein.

	![Benennen Sie den Auftrag, geben Sie den Code ein, oder fügen Sie ihn ein, und klicken Sie auf "Ausführen".][1]
2. Geben Sie auf dem Blatt **Auftrag erstellen** einen Namen für den Auftrag ein.
3. Geben Sie zum Herstellen der Verbindung mit den Zieldatenbanken einen Benutzernamen mit dem zugehörigen Kennwort ein, der über ausreichende Berechtigungen für die Skriptausführung verfügt.
4. Geben Sie das T-SQL-Skript ein, oder fügen Sie es ein.
5. Klicken Sie auf **Speichern**, und klicken Sie dann auf **Ausführen**.

	![Erstellen und Ausführen von Aufträgen][5]

## Ausführen idempotenter Aufträge

Beim Ausführen eines Skripts für einen Satz von Datenbanken müssen Sie darauf achten, dass das Skript idempotent ist. Das heißt, das Skript muss mehrmals ausgeführt werden können, auch wenn es zuvor in einem unvollständigen Zustand Fehler verursacht hat. Wenn beispielsweise ein Skript fehlschlägt, wird der Auftrag automatisch wiederholt, bis er erfolgreich abgeschlossen wurde (mit Einschränkungen, da die Wiederholungslogik irgendwann keine Wiederholung mehr ausführt). Die Vorgehensweise dabei ist, eine "IF EXISTS"-Klausel zu verwenden und jede gefundene Instanz zu löschen, bevor Sie ein neues Objekt erstellen. Im Folgenden ist ein Beispiel dargestellt:

	IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
	GO
	CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Verwenden Sie alternativ eine "IF NOT EXISTS"-Klausel vor dem Erstellen einer neuen Instanz:

	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	 CREATE TABLE TestTable(
	  TestTableId INT PRIMARY KEY IDENTITY,
	  InsertionTime DATETIME2
	 );
	END
	GO

	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO

Dieses Skript aktualisiert dann die zuvor erstellte Tabelle.

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN

	ALTER TABLE TestTable

	ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO


## Überprüfen des Auftragsstatus

Nachdem ein Auftrag gestartet wurde, können Sie seinen Fortschritt überprüfen.

1. Klicken Sie auf der Seite für elastische Datenbankpools auf **Aufträge verwalten**.

	![Klicken Sie auf "Aufträge verwalten".][2]

2. Klicken Sie auf den Namen (a) eines Auftrags. Der **STATUS** kann "Abgeschlossen" oder "Fehlgeschlagen" lauten. Die Auftragsdetails (b) werden mit Datum und Uhrzeit der Erstellung und Ausführung angezeigt. Die Liste (c) zeigt den Fortschritt des Skripts für jede Datenbank im Pool unter Angabe des Datums und der Uhrzeit an.

	![Überprüfen beendeter Aufträge][3]


## Überprüfen der fehlgeschlagenen Aufträge

Wenn ein Auftrag fehlschlägt, wird ein Protokoll zu seiner Ausführung erstellt. Klicken Sie auf den Namen des fehlgeschlagenen Auftrags, um dessen Details anzuzeigen.

![Überprüfen fehlgeschlagener Aufträge][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 

<!---HONumber=August15_HO6-->