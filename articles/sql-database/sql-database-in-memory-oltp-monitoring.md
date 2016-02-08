<properties
	pageTitle="Überwachen von XTP-In-Memory-Speicher | Microsoft Azure"
	description="Einschätzen und Überwachen der XTP-In-Memory-Speicherverwendung und -kapazität; Beheben des Kapazitätsfehlers 41805"
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
	ms.topic="article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Überwachen von XTP-In-Memory-Speicher

Bei der Verwendung von [In-Memory](sql-database-in-memory.md) befinden sich die Daten der speicheroptimierten Tabellen und Tabellenvariablen in XTP-In-Memory-Speicher. Jede Premium-Serviceebene hat eine maximale In-Memory-Speichergröße, die im [Artikel zu Serviceebenen von SQL-Datenbanken](sql-database-service-tiers.md#service-tiers-for-single-databases) dokumentiert ist. Wenn dieses Limit überschritten wird, treten bei Einfüge- und Aktualisierungsvorgänge möglicherweise Fehler auf (mit Fehlercode 41805). An diesem Punkt müssen Sie entweder Daten löschen, um Speicherplatz freizugeben, oder ein Upgrade der Leistungsstufe Ihrer Datenbank durchführen.

## Bestimmen, ob genügend In-Memory-Speicherplatz für die Daten vorhanden ist

Bestimmen Sie die Speicherabdeckung: Nutzen Sie zur Information über die Speicherabdeckungen der verschiedenen Premium-Serviceebenen den [Artikel zu Serviceebenen von SQL-Datenbanken](sql-database-service-tiers.md#service-tiers-for-single-databases).

Das Einschätzen des Speicherbedarfs für eine speicheroptimierte Tabelle funktioniert für SQL Server genauso wie in Azure SQL-Datenbank. Lesen Sie die Informationen hierzu in [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Beachten Sie, dass sowohl die Tabellen- und Tabellenvariablenzeilen als auch die Indizes in die maximale Größe für Benutzerdaten eingerechnet werden. Darüber hinaus benötigt ALTER TABLE genügend Platz, um eine neue Version der gesamten Tabelle und ihrer Indizes zu erstellen.

## Überwachung und Warnung

Sie können die Nutzung von In-Memory-Speicher als Prozentsatz der [Speicherabdeckung für Ihre Leistungsebene](sql-database-service-tiers.md#service-tiers-for-single-databases) im Azure-[Portal](https://portal.azure.com/) überwachen:

- Suchen Sie auf dem Blatt „Datenbank“ das Feld „Ressourcenverwendung“, und klicken Sie auf „Bearbeiten“.
- Wählen Sie dann die Metrik für den Prozentsatz des XTP-In-Memory-Speichers aus.
- Um eine Warnung hinzuzufügen, klicken Sie auf das Feld „Ressourcenverwendung“, um das Blatt „Metrik“ zu öffnen, und klicken Sie dann auf „Warnung hinzufügen“.

Oder verwenden Sie folgende Abfrage, um die In-Memory-Speicherverwendung anzuzeigen:

    select xtp_storage_percent from sys.dm_db_resource_stats


## Korrigieren von Fehlern aufgrund von ungenügendem Arbeitsspeicher – Fehler 41805

Wenn nicht genügend Speicherplatz zur Verfügung steht, treten bei den Vorgängen INSERT, UPDATE und CREATE Fehler mit Fehlercode 41805 auf.

Die Fehlermeldung 41805 weist darauf hin, dass die speicheroptimierten Tabellen und Tabellenvariablen die maximale Speichergröße überschritten haben.

Beheben Sie den Fehler mit einer der folgenden Methoden:


- Löschen Sie Daten aus den speicheroptimierten Tabellen – Sie können die Daten beispielsweise in herkömmliche datenträgerbasierte Tabellen auslagern.
- Führen Sie ein Upgrade auf eine Dienstebene durch, die genügend In-Memory-Speicher für die Daten bietet, die Sie in In-Memory-Tabellen speichern müssen.

## Nächste Schritte
Weitere Informationen zum [Überwachen von Azure SQL-Datenbank mit dynamischen Verwaltungssichten](sql-database-monitoring-with-dmvs.md)

<!---HONumber=AcomDC_0128_2016-->