<properties
   pageTitle="Beheben von Kompatibilitätsproblemen der SQL Server-Datenbank vor der Migration zu SQL-Datenbank"
   description="Microsoft Azure SQL-Datenbank, Datenbankmigration, Kompatibilität, Migrations-Assistent für SQL Azure, SSDT"
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

# Beheben von Kompatibilitätsproblemen der SQL Server-Datenbank vor der Migration zu SQL-Datenbank

Wenn Sie ermitteln, dass Ihre SQL Server-Quelldatenbank nicht kompatibel ist, haben Sie eine Reihe von Optionen zum Beheben der identifizierten Probleme mit der Datenbankkompatibilität.

> [AZURE.SELECTOR]
- Use [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Use [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-SSMS.md)

## Verwenden von SQL Server Data Tools für Visual Studio

Verwenden Sie SQL Server Data Tools für Visual Studio („SSDT“) verwenden, um das Datenbankschema zur Analyse in ein Visual Studio-Datenbankprojekt zu importieren. Für die Analyse muss SQL-Datenbank V12 als Zielplattform angegeben und das Projekt erstellt werden. Wenn der Erstellungsvorgang erfolgreich ist, ist die Datenbank kompatibel. Wenn der Buildvorgang fehlschlägt, können Sie die Fehler in SSDT (oder einem anderen Tool, das in diesem Thema erläutert wird) beheben. Wenn sich das Projekt erfolgreich erstellen lässt, können Sie es als Kopie der Quelldatenbank veröffentlichen und die Daten anschließend unter Verwendung des Datenvergleichsfeatures von SSDT aus der Quelldatenbank in die Azure SQL V12-kompatible Datenbank kopieren. Sie können diese aktualisierte Datenbank dann migrieren. Um diese Option verwenden zu können, laden Sie die [neueste Version von SSDT](https://msdn.microsoft.com/library/mt204009.aspx) herunter.

  ![Diagramm der VSSSDT-Migration](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  >[AZURE.NOTE]Wenn nur das Schema migriert werden muss, kann das Schema direkt in Visual Studio für die Azure SQL-Datenbank veröffentlicht werden. Diese Methode kann verwendet werden, wenn das Datenbankschema mehr Änderungen erfordert, als der Migrations-Assistent allein verarbeiten kann.

## Nächster Schritt: Auswählen der Migrationsmethode und Durchführen der Migration

[Wählen Sie die Migrationsmethode aus](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->