<properties
   pageTitle="Beheben von Kompatibilitätsproblemen der SQL Server-Datenbank vor der Migration zu SQL-Datenbank"
   description="Microsoft Azure SQL-Datenbank, Datenbankmigration, Kompatibilität, Migrations-Assistent für SQL Azure"
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
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Verwenden des SQL Azure-Migrations-Assistenten

Verwenden Sie den [SQL Azure-Migrations-Assistenten](http://sqlazuremw.codeplex.com/) für das CodePlex-Tool zum Generieren eines T-SQL-Skripts aus einer inkompatiblen Quelldatenbank verwenden, das vom Assistenten dann transformiert wird, um es mit der SQL-Datenbank kompatibel zu machen. Anschließend wird eine Verbindung mit Azure SQL-Datenbank hergestellt, um das Skript auszuführen. Mit diesem Tool werden außerdem Ablaufverfolgungsdateien analysiert, um Kompatibilitätsprobleme zu ermitteln. Das Skript kann mit der Option "Nur Schema" generiert werden oder Daten im BCP-Format enthalten. Weitere Dokumentation, einschließlich einer Schritt-für-Schritt-Anleitung, finden Sie bei CodePlex unter [SQL Azure-Migrations-Assistent](http://sqlazuremw.codeplex.com/).

 ![Diagramm der SAMW-Migration](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  >[AZURE.NOTE]Beachten Sie, dass nicht jedes inkompatible Schema, das vom Assistenten erkannt werden kann, durch dessen integrierte Transformationen behoben werden kann. Ein inkompatibles Skript, das nicht verarbeitet werden kann, wird als Fehler gemeldet, wobei Kommentare in das generierte Skript eingefügt werden. Falls viele Fehler erkannt werden, sollten Sie entweder Visual Studio oder SQL Server Management Studio nutzen, um die Fehler durchzugehen und zu beheben, die mit dem SQL Server-Migrations-Assistenten nicht behoben werden konnten.

## Nächster Schritt: Auswählen der Migrationsmethode und Durchführen der Migration

[Wählen Sie die Migrationsmethode aus](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->