<properties
	pageTitle="Verwenden von In-Memory OLTP zum Verbessern der Azure SQL-Transaktionsleistung | Microsoft Azure"
	description="Verwenden von In-Memory OLTP zum Verbessern der Transaktionsleistung in einer vorhandenen SQL-Datenbank."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Verwenden von In-Memory (Vorschau) zur Verbesserung der Leistung Ihrer Azure SQL-Anwendung

Führen Sie die folgenden Schritte durch, um die Transaktionsleistung Ihrer vorhandenen Premium Azure SQL-Datenbank mithilfe von [In-Memory](sql-database-in-memory.md) zu optimieren.

Wählen Sie zu Vergleichszwecken eine Workload, die Ihrer Produktionsworkload ähnelt und eine ähnliche Anzahl gleichzeitiger Verbindungen und ein ähnliches Verhältnis von Lese-/Schreibzugriffen aufweist. Zur Minimierung der Netzwerklatenz empfiehlt es sich, die Testworkload in der gleichen Azure-Region auszuführen, in der sich auch die Datenbank befindet.

## Schritt 1: Kopieren der Daten in eine neue Premium-Datenbank
1.	Exportieren Sie Ihre Produktionsdatenbank mithilfe einer der folgenden Funktionen in eine bacpac-Datei:

	A: Die Exportfunktion im [Portal](https://portal.azure.com/)

	B. Die Funktion „Datenebenenanwendung exportieren“ in SQL Server Management Studio

2.	Importieren Sie die bacpac-Datei in eine neue Premium-Datenbank auf einem V12-Server:

	A: Im Portal: Navigieren Sie zum Server, und wählen Sie die Option „Datenbank importieren“ aus. Stellen Sie sicher, dass Sie einen Premium-Tarif auswählen.

	B. In SQL Server Management Studio (SSMS): Stellen Sie eine Verbindung mit dem Server her, klicken Sie mit der rechten Maustaste auf den Knoten „Datenbanken“, und wählen Sie „Datenebenenanwendung importieren“ aus.


## Schritt 2: Identifizieren der nach In-Memory OLTP zu migrierenden Objekte
SQL Server Management Studio (SSMS) enthält einen Transaktionsleistungsanalysebericht, den Sie in einer Datenbank mit einer aktiven Workload ausführen können, um Tabellen und gespeicherte Prozeduren zu ermitteln, die sich für die Migration zu In-Memory OLTP eignen. Weitere Informationen finden Sie unter [Bestimmen, ob eine Tabelle oder eine gespeicherte Prozedur zu In-Memory OLTP portiert werden soll](https://msdn.microsoft.com/library/dn205133.aspx).

1.	Stellen Sie über SSMS eine Verbindung mit der Produktionsdatenbank her. Wenn Sie über eine Workload verfügen, die in der neuen Testdatenbank ausgeführt wird, können Sie alternativ auch eine Verbindung mit dieser Datenbank herstellen.
2.	Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie „Berichte -> Standardberichte -> Transaktionsleistungsanalysebericht“ aus. Mithilfe dieses Berichts können Sie Tabellen und gespeicherte Prozeduren ermitteln, die je nach Nutzung von In-Memory OLTP profitieren können.


## Schritt 3: Migrieren von Tabellen
1.	Stellen Sie über SSMS eine Verbindung mit der neuen Datenbank her. Um zu vermeiden, dass Sie die Option WITH (SNAPSHOT) in Abfragen verwenden müssen, empfiehlt es sich, die Datenbankoption MEMORY\_OPTIMIZED\_ELEVATE\_TO\_SNAPSHOT festzulegen.
2.	Sobald die Verbindung zur neuen Testdatenbank hergestellt wurde, führen Sie Folgendes aus:

   	    ALTER DATABASE CURRENT SET MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT=ON

3.	Migrieren Sie die datenträgerbasierte Tabelle mit einer der folgenden Methoden in eine speicheroptimierte Tabelle:

	A: SSMS-Assistent für die Speicheroptimierung: Klicken Sie bei bestehender Verbindung mit der Testdatenbank mit der rechten Maustaste auf die Tabelle, und wählen Sie „Ratgeber für die Speicheroptimierung“. Verwenden Sie den Ratgeber, um zu bestimmen, ob die Tabelle über Funktionen verfügt, die in einer speicheroptimierten Tabelle nicht unterstützt werden. Wenn dies nicht der Fall ist, kann der Ratgeber die eigentliche Schema- und Datenmigration durchführen. Weitere Informationen finden Sie im [Ratgeber für die Speicheroptimierung in MSDN](https://msdn.microsoft.com/library/dn284308.aspx).

	B. Manuelle Migration: Stellen Sie über SSMS eine Verbindung mit der neuen Datenbank her.

Gehen Sie folgendermaßen vor, um eine Tabelle zu migrieren:

1.	Erstellen Sie ein Skript für die Tabelle, indem Sie mit der rechten Maustaste auf die Tabelle klicken und „Skript für Tabelle als -> Erstellen in -> Neues Abfragefenster“ auswählen.
2.	Ändern Sie die Indexoption CLUSTERED zu NONCLUSTERED, und fügen Sie die Option WITH (MEMORY\_OPTIMIZED=ON) hinzu.
3.	Wenn die Tabelle nicht unterstützte Funktionen verwendet, implementieren Sie Problemumgehungen. Informationen zum Umgang mit [häufigen nicht unterstützten Funktionen](https://msdn.microsoft.com/library/dn247639.aspx) finden Sie in MSDN.
4.	Benennen Sie die vorhandene Tabelle mithilfe von „sp\_rename“ um.
5.	Erstellen Sie die neue speicheroptimierte Tabelle durch Ausführen des CREATE TABLE-Skripts.
6.	Kopieren Sie die Daten, indem Sie folgende Anweisung ausführen: ``INSERT INTO <new_memory_optimized_table> SELECT * FROM <old_disk_based_table>.

## Schritt 4 (optional): Migrieren von gespeicherten Prozeduren 

Stellen Sie mithilfe von [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx), September 2015 Preview oder höher, eine Verbindung mit der Azure SQL-Datenbank her.

Identifizieren Sie alle Funktionen, die in systemintern kompilierten gespeicherten Prozeduren nicht unterstützt werden, indem Sie den [Ratgeber für systeminterne Kompilierung](https://msdn.microsoft.com/library/dn284308.aspx) ausführen. Problemumgehungen für häufige nicht unterstützte Funktionen sind in [MSDN](https://msdn.microsoft.com/library/dn296678.aspx) dokumentiert.

Bei der Migration einer gespeicherten Prozedur zu einer systeminternen Prozedur müssen zwei Aspekte berücksichtigt werden:

- Systeminterne Module erfordern folgende Optionen:

	- NATIVE\_COMPILATION
	- SCHEMABINDING



- Systeminterne Module verwenden [ATOMIC-Blöcke](https://msdn.microsoft.com/library/dn452281.aspx) für die Transaktionsverwaltung, explizite BEGIN TRAN/COMMIT/ROLLBACK-Anweisungen sind nicht erforderlich.

Eine typische systemintern kompilierte Prozedur sieht folgendermaßen aus:


   	    CREATE PROCEDURE schemaname.procedurename
   		@param1 type1, …
   		WITH NATIVE_COMPILATION, SCHEMABINDING
   		AS
   		BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = N'your_language')

   		…

   		END



Beachten Sie Folgendes: SNAPSHOT ist zwar die am häufigsten verwendete Isolationsstufe bei speicheroptimierten Tabellen, REPEATABLE READ und SERIALIZABLE werden jedoch auch unterstützt.

##### Gehen Sie folgendermaßen vor, um eine gespeicherte Prozedur zu migrieren:

1.	Erstellen Sie ein Skript für die alte gespeicherte Prozedur, indem Sie mit der rechten Maustaste auf die Prozedur klicken und „Skript für Prozedur als -> Erstellen in -> Neues Abfragefenster“ auswählen.
2.	Schreiben Sie den Prozedurheader mithilfe der obigen Vorlage neu, und entfernen Sie alle BEGIN TRAN-/ROLLBACK-/COMMIT-Anweisungen.
3.	Wenn die gespeicherte Prozedur nicht unterstützte Funktionen verwendet, implementieren Sie Problemumgehungen. Informationen zum Umgang mit [häufigen nicht unterstützten Funktionen](https://msdn.microsoft.com/library/dn296678.aspx) finden Sie in MSDN.
4.	Löschen Sie die Prozedur per DROP, oder benennen Sie die alte Prozedur mithilfe von „sp\_rename“ um.
5.	Erstellen Sie die neue systemintern kompilierte gespeicherte Prozedur, indem Sie das CREATE PROCEDURE-Skript ausführen.

## Schritt 5: Ausführen der Workload
Führen Sie Ihre Testworkload in den speicheroptimierten Tabellen und systemintern kompilierten gespeicherten Prozeduren aus, und messen Sie die Leistungssteigerung.

## Nächste Schritte

[Überwachen von In-Memory-Speicher](https://azure.microsoft.com/documentation/articles/sql-database-in-memory-oltp-monitoring/)

[Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten](sql-database-monitoring-with-dmvs.md)

<!---HONumber=Nov15_HO1-->