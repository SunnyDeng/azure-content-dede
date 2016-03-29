<properties
   pageTitle="Ermitteln der SQL-Datenbankkompatibilität mithilfe von SqlPackage.exe"
   description="Microsoft Azure SQL-Datenbank, Datenbankmigration, SQL-Datenbankkompatibilität, SqlPackage"
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
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Ermitteln der SQL-Datenbankkompatibilität mithilfe von SqlPackage.exe

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

In diesem Artikel erfahren Sie, wie Sie ermitteln können, ob eine SQL Server-Datenbank für die Migration zur SQL-Datenbank geeignet ist, indem Sie das [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)-Hilfsprogramm für die Eingabeaufforderung verwenden.

## Verwenden von SqlPackage.exe

1. Öffnen Sie ein Eingabeaufforderungsfenster, und wechseln Sie in ein Verzeichnis mit der neuesten Version von „sqlpackage.exe“. Dieses Hilfsprogramm wird mit Visual Studio und SQL Server bereitgestellt. Laden Sie die [neueste Version von SQL Server Data Tools für Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) herunter, um die neueste Version des SqlPackage-Hilfsprogramms zu erhalten.
2. Führen Sie den folgenden SqlPackage-Befehl mit den unten angegebenen Argumenten für Ihre Umgebung aus:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	| Argument | Beschreibung |
	|---|---|
	| < server_name > | Name des Quellservers |
	| < database_name > | Name der Quelldatenbank |
	| < target_file > | Dateiname und Speicherort für die BACPAC-Datei |
	| < schema_name.table_name > | Tabellen, für die Daten in die Zieldatei ausgegeben werden |
	| < output_file > | Dateiname und Speicherort für die Ausgabedatei mit Fehlern, falls vorhanden |

	Der Grund für das Argument „/p:TableName“ ist, dass wir nur die Datenbankkompatibilität in Bezug auf das Exportieren nach Azure SQL-Datenbank V12 testen möchten und nicht die Daten aus allen Tabellen exportieren möchten. Leider unterstützt das export-Argument für „sqlpackage.exe“ das Extrahieren von 0 Tabellen nicht. Sie müssen also eine kleine Tabelle angeben. < output_file > enthält den Bericht zu etwaigen Fehlern. Mit der Zeichenfolge „> 2>&1“ werden sowohl die Standardausgabe als auch der Standardfehler, die bzw. der sich aus der Befehlsausführung ergibt, an die angegebene Ausgabedatei geleitet.

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Öffnen Sie die Ausgabedatei, und sehen Sie sich die Kompatibilitätsfehler an, falls vorhanden.

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Nächster Schritt: Beheben von Kompatibilitätsproblemen, falls vorhanden

[Beheben von Problemen mit der Datenbankkompatibilität](sql-database-cloud-migrate-fix-compatibility-issues.md), falls vorhanden.

<!---HONumber=AcomDC_0316_2016-->