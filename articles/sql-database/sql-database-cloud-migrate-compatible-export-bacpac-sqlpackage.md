<properties
   pageTitle="Exportieren einer SQL Server-Datenbank in eine BACPAC-Datei per SqlPackage"
   description="Microsoft Azure SQL-Datenbank, Datenbankmigration, Datenbank exportieren, BACPAC-Datei exportieren, sqlpackage"
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

# Exportieren einer SQL Server-Datenbank in eine BACPAC-Datei per SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

Dieser Artikel zeigt, wie Sie eine SQL-Serverdatenbank in eine [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-Datei mithilfe des [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)-Hilfsprogramms für die Eingabeaufforderung exportieren. Dieses Hilfsprogramm wird mit Visual Studio und SQL Server bereitgestellt. Sie können auch die aktuelle Version der SQL Server Data Tools [herunterladen](https://msdn.microsoft.com/library/mt204009.aspx), um dieses Hilfsprogramm zu erhalten.

1. Öffnen Sie ein Eingabeaufforderungsfenster, und wechseln Sie in ein Verzeichnis mit dem Befehlszeilen-Hilfsprogramm „sqlpackage.exe“. Dieses Hilfsprogramm ist im Lieferumfang von Visual Studio und SQL Server enthalten. Verwenden Sie die Suche auf dem Computer, um den Pfad in Ihrer Umgebung zu finden.
2. Führen Sie den folgenden sqlpackage.exe-Befehl mit den unten angegebenen Argumenten für Ihre Umgebung aus:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| Argument | Beschreibung |
	|---|---|
	| < server_name > | Name des Quellservers |
	| < database_name > | Name der Quelldatenbank |
	| < target_file > | Dateiname und Speicherort für die BACPAC-Datei |

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Nächster Schritt: Importieren aus einer BACPAC-Datei in SQL-Datenbank

- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure-Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_1223_2015-->