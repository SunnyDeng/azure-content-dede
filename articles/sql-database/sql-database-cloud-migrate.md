<properties
   pageTitle="Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank"
   description="Microsoft Azure SQL-Datenbank, Datenbankbereitstellung, Datenbankmigration, Datenbank importieren, Datenbank exportieren, Migrations-Assistent"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank

In diesem Artikel erfahren Sie, wie Sie eine lokale SQL Server-Datenbank (ab SQL Server 2005) zu einer Azure SQL-Datenbank migrieren. Im Zuge des Migrationsprozesses werden nach erfolgreichem Abschluss der Kompatibilitätstests sowohl Ihr Schema als auch Ihre Daten aus der SQL Server-Datenbank in Ihrer aktuellen Umgebung in die SQL-Datenbank migriert. Mit [SQL-Datenbank V12](sql-database-v12-whats-new.md) wurden bis auf Vorgänge auf Serverebene und datenbankübergreifende Vorgänge nahezu alle Kompatibilitätsprobleme behoben. Datenbanken und Anwendungen, die auf [teilweise oder nicht unterstützten Funktionen](sql-database-transact-sql-information.md) basieren, müssen zum [Beheben dieser Kompatibilitätsprobleme](sql-database-cloud-migrate-fix-compatibility-issues.md) vor der Migration der SQL Server-Datenbank etwas umstrukturiert werden.

> [AZURE.NOTE]Informationen zum Migrieren einer SQL Server-fremden Datenbank (einschließlich Microsoft Access, Sybase, MySQL Oracle und DB2) zu einer Azure SQL-Datenbank finden Sie unter [SQL Server-Migrations-Assistent](http://blogs.msdn.com/b/ssma/).

## Ermitteln, ob eine SQL Server-Datenbank für die Migration zur SQL-Datenbank geeignet ist

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

Verwenden Sie eine der folgenden Methoden, um die Kompatibilität mit der SQL-Datenbank zu testen, bevor Sie die Migration starten:

- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): „SqlPackage“ ist ein Hilfsprogramm für die Eingabeaufforderung. Es sucht nach Kompatibilitätsproblemen und generiert ggf. einen entsprechenden Bericht.
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): Der Assistent „Datenebenenanwendung exportieren“ in SQL Server Management Studio zeigt gefundene Fehler auf dem Bildschirm an.

## Beheben von Kompatibilitätsproblemen

Gefundene Kompatibilitätsprobleme müssen behoben werden, bevor Sie mit der Migration fortfahren.

- Mit dem [SQL Azure-Migrations-Assistenten](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Mit [SQL Server Data Tools für Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Mit [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Migrieren einer kompatiblen SQL Server-Datenbank zur SQL-Datenbank

Für die Migration einer kompatiblen SQL Server-Datenbank bietet Microsoft mehrere Migrationsmethoden für verschiedene Szenarien. Für welche Methode Sie sich entscheiden, hängt von Ihrer Toleranz für Ausfallzeiten, von der Größe und Komplexität Ihrer SQL Server-Datenbank sowie von der Konnektivität mit der Microsoft Azure-Cloud ab.

> [AZURE.SELECTOR]
- [SSMS Migration Wizard](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Export to BACPAC File](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Import from BACPAC File](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactional Replication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Fragen Sie sich bei der Wahl der passenden Migrationsmethode zunächst, ob die Datenbank während der Migration aus der Produktion genommen werden kann. Wenn eine Datenbank während der Abwicklung aktiver Transaktionen migriert wird, kann dies zu Inkonsistenzen führen und die Datenbank unter Umständen beschädigen. Eine Datenbank kann auf unterschiedliche Weise stillgelegt werden – vom Deaktivieren der Clientkonnektivität bis hin zum Erstellen einer [Datenbankmomentaufnahme](https://msdn.microsoft.com/library/ms175876.aspx).

Mit der [SQL Server-Transaktionsreplikation](sql-database-cloud-migrate-compatible-using-transactional-replication.md) können Sie die Ausfallzeit bei der Migration möglichst gering halten, sofern die Datenbank die entsprechenden Anforderungen erfüllt. Sehen Sie sich die drei folgenden Methoden an, falls eine gewisse Ausfallzeit akzeptabel ist oder Sie eine Testmigration für eine später zu migrierende Produktionsdatenbank durchführen möchten:

- [SSMS-Migrations-Assistent](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): Für kleine und mittelgroße Datenbanken muss zum Migrieren einer kompatiblen SQL Server 2005-Datenbank (oder höher) lediglich der [Assistent zum Bereitstellen einer Datenbank unter Microsoft Azure](sql-database-cloud-migrate-compatible-using-migration-wizard.md) in SQL Server Management Studio ausgeführt werden. 
- [Exportieren in eine BACPAC-Datei](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) und [Importieren aus der BACPAC-Datei](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): Bei Konnektivitätsproblemen (keine Verbindung, geringe Bandbreite oder Zeitüberschreitungen) sowie bei mittelgroßen bis großen Datenbanken können Sie eine [BACPAC-Datei](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) verwenden. Mit dieser Methode können Sie das SQL Server-Schema und die Daten in eine BACPAC-Datei exportieren und diese anschließend mithilfe des Assistenten „Datenebenenanwendung exportieren“ (in SQL Server Management Studio) oder mithilfe des Hilfsprogramms [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) über die Eingabeaufforderung in die SQL-Datenbank importieren.
- Verwenden von BACPAC-Datei und BCP: Verwenden Sie für besonders große Datenbanken sowohl eine [BACPAC-Datei](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) als auch [BCP](https://msdn.microsoft.com/library/ms162802.aspx), um durch eine bessere Parallelisierung eine höhere Leistung (aber auch eine höhere Komplexität) zu erreichen. Bei dieser Methode werden Schema und Daten jeweils separat migriert. 
 - [Exportieren Sie nur das Schema in eine BACPAC-Datei](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importieren Sie nur das Schema aus der BACPAC-Datei](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) in die SQL-Datenbank. 
 - Extrahieren Sie die Daten mithilfe von [BCP](https://msdn.microsoft.com/library/ms162802.aspx) in Flatfiles, und laden Sie die Dateien anschließend [parallel](https://technet.microsoft.com/library/dd425070.aspx) in die Azure SQL-Datenbank.

	 ![Diagramm der SSMS-Migration](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_1223_2015-->