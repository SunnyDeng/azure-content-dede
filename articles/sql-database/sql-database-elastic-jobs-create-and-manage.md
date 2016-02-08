<properties
	pageTitle="Erstellen und Verwalten von Aufträgen für elastische Datenbanken | Microsoft Azure"
	description="Führen Sie die Schritte zur Erstellung und Verwaltung eines elastischen Datenbankauftrags aus."
	services="sql-database"
	documentationCenter=""
	manager="jhubbard"
	authors="ddove"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/19/2016"
	ms.author="ddove; sidneyh"/>

# Erstellen und Verwalten eines elastischen SQL-Datenbankauftrags im Portal (Vorschau)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)


**Aufträge für die elastische Datenbank** ermöglichen eine einfache und zuverlässige Verwaltung einer Gruppe von Datenbanken. Dazu wird die Ausführung der folgenden administrativen Vorgänge vereinfacht: Schemaänderungen, Verwaltung von Anmeldeinformationen, Aktualisierungen von Verweisdaten, Sammlung von Leistungsdaten und Sammlung von Telemetriedaten zu Mandanten (Kunden). Elastische Datenbankaufträge sind derzeit über das Azure-Portal und PowerShell-Cmdlets verfügbar. Das Azure-Portal bietet allerdings nur eingeschränkte Funktionalität, die auf die Ausführung in allen Datenbanken in einem [elastischen Datenbankpool (Vorschau)](sql-database-elastic-pool.md) begrenzt ist. Informationen zum Zugriff auf zusätzliche Features und zum Anwenden von Skripts auf eine Gruppe von Datenbanken, einschließlich einer benutzerdefinierten Sammlung oder eines Shardsatzes (erstellt mithilfe der [Clientbibliothek für elastische Datenbanken](sql-database-elastic-scale-introduction.md)) finden Sie unter [Erstellen und Verwalten von Aufträgen mithilfe von PowerShell](sql-database-elastic-jobs-powershell.md). Weitere Informationen zu Aufträgen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).

## Voraussetzungen

* Ein Azure-Abonnement. Eine kostenlose Testversion finden Sie unter [Einen Monat kostenlos testen](https://azure.microsoft.com/pricing/free-trial/).
* Ein elastischer Datenbankpool. Siehe [Informationen zu elastischen Datenbankpools](sql-database-elastic-pool.md)
* Installation von Dienstkomponenten für elastische Datenbankaufträge. Siehe [Installieren des Diensts für elastische Datenbankaufträge](sql-database-elastic-jobs-service-installation.md).

## Erstellen von Aufträgen

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) in einem vorhandenen elastischen Pool von Datenbankaufträgen auf **Auftrag erstellen**.
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

1. Klicken Sie auf der Seite "Pool für elastische Datenbank" auf **Aufträge verwalten**.

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

 

<!---HONumber=AcomDC_0128_2016-->