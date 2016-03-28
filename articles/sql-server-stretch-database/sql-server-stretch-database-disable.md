<properties
	pageTitle="Deaktivieren von Stretch-Datenbank und Zurückbringen von Remotedaten | Microsoft Azure"
	description="Erfahren Sie, wie Sie Stretch-Datenbank für eine Tabelle deaktivieren und optional Remotedaten zurückbringen können."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Deaktivieren von Stretch-Datenbank und Zurückbringen von Remotedaten

Wählen Sie zum Deaktivieren von Stretch-Datenbank für eine Tabelle in SQL Server Management Studio **Stretch** für eine Tabelle aus. Wählen Sie dann eine der folgenden Optionen.

-   **Deaktivieren | Daten aus Azure zurückbringen**. Kopieren Sie die Remotedaten für die Tabelle aus Azure, und fügen Sie sie wieder in SQL Server ein. Deaktivieren Sie dann Stretch-Datenbank für die Tabelle. Bei diesem Vorgang, der nicht abgebrochen werden kann, fallen Datenübertragungskosten an.

-   **Deaktivieren | Daten in Azure lassen**. Deaktivieren Sie Stretch-Datenbank für die Tabelle. Verwerfen Sie die Remotedaten für die Tabelle in Azure.

Nach dem Deaktivieren von Stretch-Datenbank für eine Tabelle wird die Datenmigration beendet und die Abfrageergebnisse enthalten nicht mehr die Ergebnisse aus der Remotetabelle.

Sie können auch Transact-SQL verwenden, Stretch-Datenbank für eine Tabelle oder eine Datenbank zu deaktivieren.

Wenn Sie die Datenmigration anhalten möchten, finden Sie weitere Informationen unter [Anhalten und Fortsetzen von Stretch-Datenbank](sql-server-stretch-database-pause.md).

**Hinweis** Durch das Deaktivieren von Stretch werden Remoteobjekte nicht entfernt. Wenn Sie die Remotetabelle oder die Remotedatenbank löschen möchten, müssen Sie sie mithilfe des Azure-Verwaltungsportals entfernen.

## Deaktivieren von Stretch-Datenbank für eine Tabelle

### Verwenden von SQL Server Management Studio zum Deaktivieren von Stretch-Datenbank für eine Tabelle

1.  Wählen Sie in SQL Server Management Studio im Objekt-Explorer die Tabelle aus, für die Stretch-Datenbank deaktiviert werden soll.

2.  Klicken Sie mit der rechten Maustaste, und wählen Sie **Stretch** aus. Wählen Sie dann eine der folgenden Optionen aus.

    -   **Deaktivieren | Daten aus Azure zurückbringen**. Kopieren Sie die Remotedaten für die Tabelle aus Azure, und fügen Sie sie wieder in SQL Server ein. Deaktivieren Sie dann Stretch-Datenbank für die Tabelle. Dieser Befehl kann nicht abgebrochen werden.

        Bei diesem Vorgang fallen Datenübertragungskosten an. Weitere Informationen finden Sie unter [Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/).

        Wenn alle Remotedaten aus Azure in SQL Server kopiert wurden, wird Stretch für die Tabelle deaktiviert.

    -   **Deaktivieren | Daten in Azure lassen**. Deaktivieren Sie Stretch-Datenbank für die Tabelle. Verwerfen Sie die Remotedaten für die Tabelle in Azure.

    Durch das Deaktivieren von Stretch wird die Remotetabelle nicht entfernt. Wenn Sie die Remotetabelle löschen möchten, müssen Sie sie mithilfe des Azure-Verwaltungsportals entfernen.

### Verwenden von Transact-SQL zum Deaktivieren von Stretch-Datenbank für eine Tabelle

-   Wenn Sie Stretch für eine Tabelle deaktivieren möchten, und die Remotedaten für die Tabelle aus Azure zurück in SQL Server kopieren möchten, führen Sie folgenden Befehl aus. Dieser Befehl kann nicht abgebrochen werden.

    ```tsql
    ALTER TABLE <table name>
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    ```
    Bei diesem Vorgang fallen Datenübertragungskosten an. Weitere Informationen finden Sie unter [Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/).

    Wenn alle Remotedaten aus Azure in SQL Server kopiert wurden, wird Stretch für die Tabelle deaktiviert.

-   Führen Sie den folgenden Befehl aus, um Stretch für eine Tabelle zu deaktivieren und die Remotedaten zu verwerfen.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```
Durch das Deaktivieren von Stretch wird die Remotetabelle nicht entfernt. Wenn Sie die Remotetabelle löschen möchten, müssen Sie sie mithilfe des Azure-Verwaltungsportals entfernen.

## Deaktivieren von Stretch-Datenbank für eine Datenbank
Vor dem Deaktivieren von Stretch-Datenbank für eine Datenbank müssen Sie Stretch-Datenbank für die einzelnen Stretch-fähigen Tabellen in der Datenbank deaktivieren.

Durch das Deaktivieren von Stretch wird die Remotedatenbank nicht entfernt. Wenn Sie die Remotedatenbank löschen möchten, müssen Sie sie mithilfe des Azure-Verwaltungsportals entfernen.

### Verwenden von SQL Server Management Studio zum Deaktivieren von Stretch-Datenbank für eine Datenbank

1.  Wählen Sie in SQL Server Management Studio im Objekt-Explorer die Datenbank aus, für die Stretch-Datenbank deaktiviert werden soll.

2.  Klicken Sie mit der rechten Maustaste, und wählen Sie **Aufgaben** und dann **Stretch**aus. Wählen Sie dann **Deaktivieren** aus.

### Verwenden von Transact-SQL zum Deaktivieren von Stretch-Datenbank für eine Datenbank
Führen Sie den folgenden Befehl aus:

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

## Weitere Informationen

[ALTER DATABASE SET-Optionen (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Anhalten und Fortsetzen von Stretch-Datenbank](sql-server-stretch-database-pause.md)

<!---HONumber=AcomDC_0316_2016-->