<properties
   pageTitle="Einleitung zu U-SQL-Katalogen für Azure Data Lake Analytics | Azure"
   description="Einleitung zu U-SQL-Katalogen für Azure Data Lake Analytics"
   services="data-lake-analytics"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/26/2015"
   ms.author="jgao"/>

# Verwenden des U-SQL-Katalogs

Der U-SQL-Katalog wird zum Strukturieren von Daten und Code verwendet, damit diese von U-SQL-Skripts gemeinsam genutzt werden können. Der Katalog ermöglicht die höchstmögliche Leistung mit Daten in Azure Data Lake.

Jedes Azure Data Lake Analytics-Konto ist genau einem U-SQL-Katalog zugeordnet. Der U-SQL-Katalog kann nicht gelöscht werden. Derzeit können U-SQL-Kataloge nicht von mehreren Data Lake-Speicherkonten gemeinsam verwendet werden.

Jeder U-SQL-Katalog enthält eine Datenbank namens **Master**. Die Masterdatenbank kann nicht gelöscht werden. Jeder U-SQL-Katalog kann zusätzliche Datenbanken enthalten.

U-SQL-Datenbanken enthalten:

- Assemblys – nutzen .NET-Code in mehreren U-SQL-Skripts gemeinsam.
- Tabellenwertfunktionen – nutzen U-SQL-Code in mehreren U-SQL-Skripts gemeinsam.
- Tabellen – nutzen Daten in mehreren U-SQL-Skripts gemeinsam.
- Schemas – nutzen Tabellenschemas in mehreren U-SQL-Skripts gemeinsam.

## Verwalten von Katalogen
Jedes Azure Data Lake Analytics-Konto verfügt über ein zugehöriges Azure Data Lake-Speicherkonto. Dieses Data Lake-Speicherkonto wird als Data Lake-Standardspeicherkonto bezeichnet. Der U-SQL-Katalog wird im Data Lake-Standardspeicherkonto im Ordner „/catalog“ gespeichert. Löschen Sie keine Dateien im Ordner „/catalog“.

### Verwenden des Azure-Vorschauportals

Weitere Informationen finden Sie unter [Verwalten von Data Lake Analytics mithilfe des Portals](data-lake-analytics-use-portal.md#view-u-sql-catalog)


### Verwenden der Data Lake-Tools für Visual Studio.

Sie können die Data Lake-Tools für Visual Studio zur Katalogverwaltung nutzen. Weitere Informationen zu den Tools finden Sie unter [Verwenden der Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

**So verwalten Sie den Katalog**

1. Öffnen Sie Visual Studio, und stellen Sie eine Verbindung mit Azure her. Die Anleitung hierzu finden Sie unter [Herstellen einer Verbindung mit Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
1. Öffnen Sie den **Server-Explorer**, indem Sie **STRG+ALT+S** drücken.
2. Erweitern Sie im **Server-Explorer** nacheinander die Optionen **Azure**, **Data Lake Analytics**, dann Ihr Data Lake Analytics-Konto, die Option **Databases** und schließlich **master**.



    - Zum Hinzufügen einer neuen Datenbank klicken Sie mit rechten Maustaste auf **Database**, und klicken Sie dann auf **Create Database**.
    - Zum Hinzufügen einer neuen Assembly klicken Sie mit der rechten Maustaste auf **Assemblys**, und klicken Sie dann auf **Register Assembly**.
    - Zum Hinzufügen eines neuen Schemas klicken Sie mit der rechten Maustaste auf **Schemas**, und klicken Sie dann auf „Schema erstellen“.
    - Zum Hinzufügen einer neuen Tabelle klicken Sie mit der rechten Maustaste auf **Tabellen**, und klicken Sie dann auf „Tabelle erstellen“.
    - Informationen zum Hinzufügen einer neuen Tabellenwertfunktion finden Sie unter [Entwickeln von benutzerdefinierten U-SQL-Operatoren für Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Durchsuchen von Visual Studio U-SQL-Katalogen](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## Weitere Informationen

- Erste Schritte
    - [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md)
    - [Erste Schritte mit Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-get-started-powershell.md)
    - [Erste Schritte mit Data Lake Analytics mithilfe des Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
    - [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Erste Schritte mit der Sprache U-SQL für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)

- U-SQL und Entwicklung
    - [Erste Schritte mit der Sprache U-SQL für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
    - [Verwenden von U-SQL-Fensterfunktionen für Azure Data Lake Analytics-Aufträge](data-lake-analytics-use-window-functions.md)
    - [Entwickeln von benutzerdefinierten U-SQL-Operatoren für Data Lake Analytics-Aufträge](data-lake-analtyics-u-sql-user-defined-operators.md)

- Verwaltung
    - [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-use-portal.md)
    - [Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-use-powershell.md)
    - [Überwachen und Problembehandeln von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Vorschauportals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Komplettes Lernprogramm
    - [Verwenden interaktiver Lernprogramme zu Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
    - [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)

<!---HONumber=Nov15_HO1-->