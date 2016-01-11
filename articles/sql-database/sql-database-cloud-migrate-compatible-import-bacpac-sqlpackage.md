<properties
   pageTitle="Importieren aus einer BACPAC-Datei in SQL-Datenbank per SqlPackage"
   description="Microsoft Azure SQL-Datenbank, Datenbankmigration, Datenbank importieren, BACPAC-Datei importieren, sqlpackage"
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

# Importieren aus einer BACPAC-Datei in SQL-Datenbank per SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Dieser Artikel zeigt, wie Sie aus einer [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-Datei mithilfe des [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)-Hilfsprogramms für die Eingabeaufforderung in SQL-Datenbank importieren. Dieses Hilfsprogramm wird mit Visual Studio und SQL Server bereitgestellt. Sie können auch die aktuelle Version der SQL Server Data Tools [herunterladen](https://msdn.microsoft.com/library/mt204009.aspx), um dieses Hilfsprogramm zu erhalten.

> [AZURE.NOTE]Bei den Schritten unten wird vorausgesetzt, dass Sie bereits einen SQL-Datenbankserver bereitgestellt haben, dass Ihnen die Verbindungsinformationen vorliegen und dass Sie die Kompatibilität der Quelldatenbank überprüft haben.

## Importieren aus einer BACPAC-Datei in Azure SQL-Datenbank per SqlPackage

Führen Sie die unten angegebenen Schritte aus, um das Befehlszeilen-Hilfsprogramm [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) zum Importieren einer kompatiblen SQL Server-Datenbank (oder Azure SQL-Datenbank) aus einer BACPAC-Datei zu verwenden.

> [AZURE.NOTE]Bei den folgenden Schritten wird davon ausgegangen, dass Sie bereits einen Azure SQL-Datenbank-Server bereitgestellt haben und dass Ihnen die Verbindungsinformationen vorliegen.

1. Öffnen Sie ein Eingabeaufforderungsfenster, und wechseln Sie in ein Verzeichnis mit dem Befehlszeilen-Hilfsprogramm „sqlpackage.exe“. Dieses Hilfsprogramm ist im Lieferumfang von Visual Studio und SQL Server enthalten.
2. Führen Sie den folgenden sqlpackage.exe-Befehl mit den unten angegebenen Argumenten für Ihre Umgebung aus:

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >

	| Argument | Beschreibung |
	|---|---|
	| < server_name > | Zielservername |
	| < database_name > | Zieldatenbankname |
	| < user_name > | Benutzername auf dem Zielserver |
	| < password > | Kennwort des Benutzers |
	| < source_file > | Dateiname und Speicherort für die zu importierende BACPAC-Datei |

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

<!---HONumber=AcomDC_1223_2015-->