<properties
	pageTitle="Aktivieren von Stretch-Datenbank für eine Tabelle | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine Tabelle für Stretch-Datenbank konfigurieren."
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

# Aktivieren von Stretch-Datenbank für eine Tabelle

Um eine Tabelle für Stretch-Datenbank zu konfigurieren, wählen Sie **Stretch | Aktivieren** für eine Tabelle in SQL Server Management Studio, um den Assistenten zum **Aktivieren einer Tabelle für Stretch** zu öffnen. Sie können auch Transact-SQL verwenden, um Stretch-Datenbank für eine Tabelle zu aktivieren.

-   Falls Sie Ihre Verlaufsdaten in einer separaten Tabelle speichern, können Sie die gesamte Tabelle migrieren.

-   Wenn die Tabelle alte und aktuelle Daten enthält, können Sie ein Filterprädikat zum Auswählen der zu migrierenden Zeilen angeben. In CTP 3.1 bis RC0 steht die Option zum Angeben eines Filterprädikats nicht im Assistent zum Aktivieren einer Datenbank für Stretch zur Verfügung. Sie müssen eine ALTER TABLE-Anweisung verwenden, um eine Tabelle für Stretch-Datenbank mit dieser Option zu konfigurieren. Weitere Informationen finden Sie unter [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

**Voraussetzungen**. Wenn Sie **Stretch | Aktivieren** für eine Tabelle auswählen und Sie Stretch-Datenbank noch nicht für die Datenbank aktiviert haben, konfiguriert der Assistent zuerst die Datenbank für Stretch-Datenbank. Führen Sie die Schritte in diesem Thema anstelle der Schritte unter [Assistent zum Aktivieren einer Datenbank für Stretch](sql-server-stretch-database-wizard.md) aus.

**Berechtigungen**. Für das Aktivieren von Stretch-Datenbank für eine Datenbank oder eine Tabelle sind db\_owner-Berechtigungen erforderlich. Zum Aktivieren von Stretch-Datenbank für eine Tabelle sind auch ALTER-Berechtigungen für die Tabelle erforderlich.

## <a name="EnableWizardTable"></a>Verwenden des Assistenten zum Aktivieren von Stretch-Datenbank für eine Tabelle
**Assistenten starten**
1.  Wählen Sie in SQL Server Management Studio im Objekt-Explorer die Tabelle aus, für die Stretch aktiviert werden soll.

2.  Klicken Sie mit der rechten Maustaste, und wählen Sie **Stretch** aus. Wählen Sie dann **Aktivieren** aus, um den Assistenten zu starten.

**Einführung** Überprüfen Sie den Zweck des Assistenten und die Voraussetzungen.

**Datenbanktabellen auswählen** Bestätigen Sie, dass die Tabelle, die Sie aktivieren möchten, angezeigt und ausgewählt ist.

In CTP 3.1 bis RC0 können Sie nur eine gesamte Tabelle mithilfe des Assistenten migrieren. Wenn Sie ein Prädikat für die Auswahl von Zeilen für die Migration aus einer Tabelle angeben möchten, die historische und aktuelle Daten enthält, führen Sie die ALTER TABLE-Anweisung aus, um ein Prädikat anzugeben, nachdem Sie den Assistenten beendet haben. Beenden Sie alternativ den Assistenten, und führen Sie die ALTER TABLE-Anweisung aus, wie nachfolgend in diesem Thema beschrieben.

**Zusammenfassung** Überprüfen Sie die Werte, die Sie eingegeben haben, und die im Assistenten ausgewählten Optionen. Wählen Sie dann **Fertig stellen**, um Stretch zu aktivieren.

**Ergebnisse** Überprüfen Sie die Ergebnisse.

## <a name="EnableTSQLTable"></a>Verwenden von Transact-SQL zum Aktivieren von Stretch-Datenbank für eine Tabelle
Um eine Tabelle für Stretch-Datenbank zu konfigurieren, führen Sie den Befehl ALTER TABLE aus.

1.  Verwenden Sie optional die `FILTER_PREDICATE = <predicate>`-Klausel, um ein Prädikat anzugeben, mit dem Sie die zu migrierenden Zeilen auswählen können, wenn die Tabelle alte und aktuelle Daten enthält. Das Prädikat muss eine Inline-Tabellenwertfunktion aufrufen. Weitere Informationen finden Sie unter [Schreiben einer Inline-Tabellenwertfunktion zum Auswählen von Zeilen (Stretch-Datenbank)](sql-server-stretch-database-predicate-function.md). Wenn Sie kein Filterprädikat angeben, wird die gesamte Tabelle migriert.

    > [WICHTIG!] Wenn Sie ein Filterprädikat mit schlechter Leistung angeben, erbringt womöglich auch die Datenmigration nicht die gewünschte Leistung. Stretch-Datenbank wendet das Filterprädikat mithilfe des CROSS APPLY-Operators auf die Tabelle an.

    In CTP 3.1 bis RC0 steht diese Option nicht im Assistent zum Aktivieren einer Datenbank für Stretch zur Verfügung. Sie müssen eine ALTER TABLE-Anweisung verwenden, um eine Tabelle für Stretch-Datenbank mit dieser Option zu konfigurieren. Weitere Informationen finden Sie unter „[ALTER TABLE (Transact-SQL)]“(https://msdn.microsoft.com/library/ms190273.aspx.

2.  Geben Sie `MIGRATION_STATE = OUTBOUND` an, um die Datenmigration sofort zu starten, oder `MIGRATION_STATE = PAUSED`, um den Start der Datenmigration zu verschieben.

Hier ist ein Beispiel, bei dem die gesamte Tabelle migriert wird und die Datenmigration unmittelbar beginnt.

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
GO;
```
Es folgt ein Beispiel, in dem nur die durch die Inline-Tabellenwertfunktion `dbo.fn_stretchpredicate` markierten Zeilen migriert werden und der Start der Datenmigration verschoben wird. Weitere Informationen zum Filterprädikat finden Sie unter [Schreiben einer Inline-Tabellenwertfunktion zum Auswählen von Zeilen (Stretch-Datenbank)](sql-server-stretch-database-predicate-function.md).

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) )
```

## Weitere Informationen

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0316_2016-->