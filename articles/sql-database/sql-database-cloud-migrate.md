<properties
   pageTitle="Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank | Microsoft Azure"
   description="Erfahren Sie, wie lokale SQL Server-Datenbanken zu Azure SQL-Datenbank in der Cloud migriert werden. Verwenden Sie Datenbankmigrationstools, um vor der Datenbankmigration die Kompatibilität zu testen."
   keywords="Datenbankmigration,SQL Server-Datenbankmigration,Datenbankmigrationstools,Migrieren einer Datenbank,Migrieren einer SQL-Datenbank"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/22/2016"
   ms.author="carlrab"/>

# Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank in der Cloud

In diesem Artikel erfahren Sie, wie Sie eine lokale SQL Server-Datenbank (ab SQL Server 2005) zu einer Azure SQL-Datenbank migrieren. Im Zuge des Datenmigrationsprozesses werden nach erfolgreichem Abschluss der Kompatibilitätstests sowohl Ihr Schema als auch Ihre Daten aus der SQL Server-Datenbank in Ihrer aktuellen Umgebung in die SQL-Datenbank migriert. Mit [SQL-Datenbank V12](sql-database-v12-whats-new.md) wurden bis auf Vorgänge auf Serverebene und datenbankübergreifende Vorgänge nahezu alle Kompatibilitätsprobleme behoben. Datenbanken und Anwendungen, die auf [teilweise oder nicht unterstützten Funktionen](sql-database-transact-sql-information.md) basieren, müssen zum Beheben dieser Kompatibilitätsprobleme vor der Migration der SQL Server-Datenbank etwas umstrukturiert werden.

Zum Migrieren müssen Sie die folgenden Schritte ausführen:

- **Testen auf Kompatibilität**: Sie müssen zuerst die Datenbankkompatibilität mit [SQL-Datenbank V12](sql-database-v12-whats-new.md) überprüfen. 
- **Beheben von ggf. vorhandenen Kompatibilitätsproblemen**: Wenn die Überprüfung fehlschlägt, müssen Sie die Überprüfungsfehler beheben.  
- **Durchführen der Migration**: Sobald die Datenbank kompatibel ist, können Sie mit verschiedenen Methoden die Migration durchführen. 

SQL Server bietet mehrere Methoden für jede dieser Aufgaben. Dieser Artikel bietet eine Übersicht über die verfügbaren Methoden für jede Aufgabe. Das folgende Diagramm veranschaulicht die Schritte und die Methoden.

  ![Diagramm der VSSSDT-Migration](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] Informationen zum Migrieren einer SQL Server-fremden Datenbank (einschließlich Microsoft Access, Sybase, MySQL Oracle und DB2) zu einer Azure SQL-Datenbank finden Sie unter [SQL Server-Migrations-Assistent](http://blogs.msdn.com/b/ssma/).

## Datenbankmigrationstools zum Testen der SQL Server-Datenbankkompatibilität mit SQL-Datenbank

Um vor dem Start des Datenbankmigrationsprozesses auf Kompatibilitätsprobleme mit SQL-Datenbank zu prüfen, verwenden Sie eine der folgenden Methoden:

- [SQL Server Data Tools für Visual Studio (SSDT)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT verwendet die neuesten Kompatibilitätsregeln, um Inkompatibilitäten mit SQL-Datenbank V12 zu erkennen. Wenn Inkompatibilitäten erkannt werden, können Sie erkannte Probleme direkt in diesem Tool beheben. Dies ist derzeit die empfohlene Methode zum Testen und Beheben von Kompatibilitätsproblemen mit SQL-Datenbank V12. 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage ist ein Hilfsprogramm für die Eingabeaufforderung. Es sucht nach Kompatibilitätsproblemen und generiert ggf. einen entsprechenden Bericht. Wenn Sie dieses Tool einsetzen, stellen Sie sicher, dass Sie die neueste Version verwenden, um die neuesten Kompatibilitätsregeln zu nutzen. Wenn Fehler gefunden werden, müssen Sie ein anderes Tool verwenden, um alle erkannten Kompatibilitätsprobleme zu beheben. Dazu wird SSDT empfohlen.  
- [Assistent „Datenebenenanwendung exportieren“ in SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): Dieser Assistent erkennt Fehler und zeigt sie auf dem Bildschirm an. Wenn keine Fehler gefunden werden, können Sie fortfahren und die Migration zu SQL-Datenbank abschließen. Wenn Fehler gefunden werden, müssen Sie ein anderes Tool verwenden, um alle erkannten Kompatibilitätsprobleme zu beheben. Dazu wird SSDT empfohlen.
- [Aktualisierungsratgeber von Microsoft SQL Server 2016 (Vorschau)](http://www.microsoft.com/download/details.aspx?id=48119): Dieses eigenständige Tools (derzeit in der Vorschauphase) erkennt Inkompatibilitäten mit SQL-Datenbank V12 und generiert einen entsprechenden Bericht. Dieses Tool verfügt noch nicht über die neuesten Kompatibilitätsregeln. Wenn keine Fehler gefunden werden, können Sie fortfahren und die Migration zu SQL-Datenbank abschließen. Wenn Fehler gefunden werden, müssen Sie ein anderes Tool verwenden, um alle erkannten Kompatibilitätsprobleme zu beheben. Dazu wird SSDT empfohlen. 
- [SQL Azure-Migrations-Assistent (SAMW)](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW ist ein Codeplex-Tool, das die Kompatibilitätsregeln von Azure SQL-Datenbank V11 nutzt, um Inkompatibilitäten mit Azure SQL-Datenbank V12 zu erkennen. Wenn Inkompatibilitäten erkannt werden, können Sie einige Probleme direkt in diesem Tool beheben. Dieses Tool findet unter Umständen Inkompatibilitäten, die nicht behoben werden müssen. Es war jedoch das erste verfügbare Tool zur Unterstützung der Azure SQL-Datenbankmigration, und es wird in großem Umfang von der SQL Server-Community unterstützt. Dieses Tool kann darüber hinaus die Migration über das Tool selbst abschließen. 

## Beheben von Kompatibilitätsproblemen in Bezug auf die Datenbankmigration

Gefundene Kompatibilitätsprobleme müssen behoben werden, bevor Sie mit der SQL Server-Datenbankmigration fortfahren. Es gibt eine Vielzahl von Kompatibilitätsproblemen, die auftreten können. Sie hängen von der SQL Server-Version in der Quelldatenbank und der Komplexität der Datenbank ab, die Sie migrieren. Je älter die SQL Server-Version der Quelldatenbank ist, desto mehr mögliche Inkompatibilitäten können auftreten. Verwenden Sie die folgenden Ressourcen und eine gezielte Internetsuche mithilfe Ihrer bevorzugten Suchmaschine:

- [In Azure SQL-Datenbank nicht unterstützte SQL Server-Datenbankfunktionen](sql-database-transact-sql-information.md)
- [Nicht mehr unterstützte Datenbankmodul-Funktionalität in SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Nicht mehr unterstützte Datenbankmodul-Funktionalität in SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Nicht mehr unterstützte Datenbankmodul-Funktionalität in SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Nicht mehr unterstützte Datenbankmodul-Funktionalität in SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Nicht mehr unterstützte Datenbankmodul-Funktionalität in SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Zusätzlich zur Suche im Internet und der Verwendung der genannten Ressourcen sind [Foren der MSDN-Community zu SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) und [StackOverflow](http://stackoverflow.com/) weitere gute Ressourcen, um herauszufinden, wie ein Inkompatibilitätsproblem am besten behoben werden kann.

Verwenden Sie eines der folgenden Datenbank-Migrationstools, um erkannte Probleme zu beheben:

- Verwenden Sie [SQL Server Data Tools für Visual Studio (SSDT)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): Zur Verwendung von SSDT importieren Sie Ihr Datenbankschema in SQL Server Data Tools für Visual Studio (SSDT), erstellen das Projekt für eine Bereitstellung von SQL-Datenbank V12, beheben alle erkannten Kompatibilitätsprobleme in SSDT und synchronisieren dann die Änderungen mit der Quelldatenbank (oder einer Kopie der Quelldatenbank). Dies ist derzeit die empfohlene Methode zum Testen und Beheben von Kompatibilitätsproblemen mit SQL-Datenbank V12. Folgen Sie dem Link, um sich eine [exemplarische Vorgehensweise mit SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) anzusehen.
- Verwenden Sie [SQL Server Management Studio (SSMS)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): Zur Verwendung von SSMS beheben Sie die Fehler, die mit einem anderen Tool erkannt wurden, durch Ausführen von Transact-SQL-Befehlen zum Beheben der erkannten Fehler. Diese Methode eignet sich hauptsächlich für erfahrene Benutzer und erfordert das Ändern des Datenbankschemas direkt in der Quelldatenbank. 
- Verwenden Sie den [SQL Azure-Migrations-Assistenten (SAMW)](sql-database-cloud-migrate-fix-compatibility-issues.md): Zur Verwendung von SAMW generieren Sie ein Transact-SQL-Skript aus der Quelldatenbank, das dann vom Assistenten transformiert wird, um das Schema mit SQL-Datenbank V12 kompatibel zu machen, sofern dies möglich ist. Anschließend kann SAMW eine Verbindung mit SQL-Datenbank V12 herstellen, um das Skript auszuführen. Mit diesem Tool werden außerdem Ablaufverfolgungsdateien analysiert, um Kompatibilitätsprobleme zu ermitteln. Das Skript kann mit der Option "Nur Schema" generiert werden oder Daten im BCP-Format enthalten.

## Migrieren einer kompatiblen SQL Server-Datenbank zur SQL-Datenbank

Für die Migration einer kompatiblen SQL Server-Datenbank bietet Microsoft mehrere Migrationsmethoden für verschiedene Szenarien. Für welche Methode Sie sich entscheiden, hängt von Ihrer Toleranz für Ausfallzeiten, von der Größe und Komplexität Ihrer SQL Server-Datenbank sowie von der Konnektivität mit der Microsoft Azure-Cloud ab.

> [AZURE.SELECTOR]
- [SSMS-Migrations-Assistent](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportieren in eine BACPAC-Datei](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importieren aus einer BACPAC-Datei](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transaktionsreplikation](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Fragen Sie sich bei der Wahl der passenden Migrationsmethode zunächst, ob die Datenbank während der Migration aus der Produktion genommen werden kann. Wenn eine Datenbank während der Abwicklung aktiver Transaktionen migriert wird, kann dies zu Inkonsistenzen führen und die Datenbank unter Umständen beschädigen. Eine Datenbank kann auf unterschiedliche Weise stillgelegt werden – vom Deaktivieren der Clientkonnektivität bis hin zum Erstellen einer [Datenbankmomentaufnahme](https://msdn.microsoft.com/library/ms175876.aspx).

Mit der [SQL Server-Transaktionsreplikation](sql-database-cloud-migrate-compatible-using-transactional-replication.md) können Sie die Ausfallzeit bei der Migration möglichst gering halten, sofern die Datenbank die entsprechenden Anforderungen erfüllt. Sehen Sie sich die drei folgenden Methoden an, falls eine gewisse Ausfallzeit akzeptabel ist oder Sie eine Testmigration für eine später zu migrierende Produktionsdatenbank durchführen möchten:

- [SSMS-Migrations-Assistent](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): Für kleine und mittelgroße Datenbanken muss zum Migrieren einer kompatiblen SQL Server 2005-Datenbank (oder höher) lediglich der [Assistent zum Bereitstellen einer Datenbank unter Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) in SQL Server Management Studio ausgeführt werden.
- [Exportieren in eine BACPAC-Datei](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) und [Importieren aus der BACPAC-Datei](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): Bei Konnektivitätsproblemen (keine Verbindung, geringe Bandbreite oder Zeitüberschreitungen) sowie bei mittelgroßen bis großen Datenbanken können Sie eine [BACPAC-Datei](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) verwenden. Mit dieser Methode können Sie das SQL Server-Schema und die Daten in eine BACPAC-Datei exportieren und diese anschließend mithilfe des Assistenten „Datenebenenanwendung exportieren“ (in SQL Server Management Studio) oder mithilfe des Hilfsprogramms [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) über die Eingabeaufforderung in die SQL-Datenbank importieren.
- Verwenden von BACPAC-Datei und BCP: Verwenden Sie für besonders große Datenbanken sowohl eine [BACPAC-Datei](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) als auch [BCP](https://msdn.microsoft.com/library/ms162802.aspx), um durch eine bessere Parallelisierung eine höhere Leistung (aber auch eine höhere Komplexität) zu erreichen. Bei dieser Methode werden Schema und Daten jeweils separat migriert.
 - [Exportieren Sie nur das Schema in eine BACPAC-Datei](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importieren Sie nur das Schema aus der BACPAC-Datei](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) in die SQL-Datenbank.
 - Extrahieren Sie die Daten mithilfe von [BCP](https://msdn.microsoft.com/library/ms162802.aspx) in Flatfiles, und laden Sie die Dateien anschließend [parallel](https://technet.microsoft.com/library/dd425070.aspx) in die Azure SQL-Datenbank.

	 ![SQL Server-Datenbankmigration – Migrieren zu SQL-Datenbank in der Cloud.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_0323_2016-->