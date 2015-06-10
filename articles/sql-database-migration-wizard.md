<properties 
   pageTitle="Migration mit dem SQL-Migrations-Assistenten" 
   description="Microsoft Azure SQL-Datenbank, Datenbankmigration, Datenbank importieren, Datenbank exportieren, Migrations-Assistent" 
   services="sql-database" 
   documentationCenter="" 
   authors="pehteh" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/14/2015"
   ms.author="pehteh"/>

# Migrieren mit dem SQL Azure-Migrations-Assistenten

![alt text](./media/sql-database-migration-wizard/01SAMWDiagram.png)

Diese Option verwendet den SQL Azure-Migrations-Assistenten, um ein T-SQL-Skript aus einer Quelldatenbank zu generieren, das dann vom Assistenten für Kompatibilität mit SQL-Datenbank transformiert und anschließend mit der Azure SQL-Datenbank verbunden wird, um das Skript für eine leere Azure SQL-Datenbank auszuführen. Das Skript kann mit der Option "Nur Schema" generiert werden oder Daten im BCP-Format enthalten. Der Assistent ermöglicht auch das Hinzufügen oder Ausschließen bestimmter Objekte aus der Datenbank im Skript.

Beachten Sie, dass nicht jedes inkompatible Schema, das vom Assistenten erkannt werden kann, durch dessen integrierte Transformationen verarbeitet werden kann. Ein inkompatibles Skript, das nicht verarbeitet werden kann, wird als Fehler gemeldet, wobei Kommentare in das generierte Skript eingefügt werden. In diesem Fall muss das Skript gespeichert und manuell bearbeitet werden, bevor es an die Azure SQL-Datenbank übermittelt werden kann. Wenn Änderungen erforderlich sind, kann das Skript gespeichert und dann mit SSMS oder den SQL Server-Tools in Visual Studio bearbeitet werden. Nachdem es kompatibel ist, kann es extern für die Azure SQL-Datenbank ausgeführt werden.

> Hinweis: Wenn viele Fehler erkannt werden und deren Korrektur nicht einfach ist, können Sie als Erweiterung zu dieser Option die generierte Skript-Datei in ein Datenbankprojekt in Visual Studio importieren. Wenn Sie als Ziel des Projekts "SQL-Datenbank V12" festlegen, können Sie anschließend das Projekt erstellen und die Fehler mithilfe der SQL Server-Tools in Visual Studio progressiv korrigieren. Wenn das Schema keine Fehler mehr enthält, können Sie es in einer Kopie der Quelldatenbank veröffentlichen und dann SSMS verwenden, um die Datenbank in der Azure SQL-Datenbank bereitzustellen oder zu exportieren/importieren, wie in Option 1 beschrieben.

## Schritte bei der Migration
1.	Stellen Sie eine neue Datenbank entweder auf einem neuen Server in der SQL-Datenbank oder auf einem vorhandenen Server in aktualisierter Form bereit, wie weiter oben beschrieben. Als letzten Schritt führen Sie das in dieser Option erstellte Migrationsskript für diese Datenbank aus. 

2.	Öffnen Sie den Migrations-Assistenten, und wählen Sie die Option zum **Analysieren/Migrieren einer Datenbank** aus. Legen Sie als **Zielserver** "Azure SQL-Datenbank V12" fest, und klicken Sie dann auf **Weiter**.

![alt text](./media/sql-database-migration-wizard/02MigrationWizard.png)

3.	Klicken Sie auf der nächsten Seite auf **Verbindung mit Server herstellen**, und geben Sie die Verbindungsinformationen für den Quellserver ein. Geben Sie entweder die Quelldatenbank im Verbindungsdialogfeld an, oder stellen Sie eine Verbindung mit dem Server her und wählen Sie dann aus der Liste der Datenbanken die Quelldatenbank aus. 

![alt text](./media/sql-database-migration-wizard/03MigrationWizard.png)

4.	Auf der nächsten Seite,"Objekte auswählen" haben Sie die Wahl zwischen den beiden Optionen "Skript für gesamte Datenbank erstellen" (Standard) und "Bestimmte Datenbankobjekte auswählen"  Es empfiehlt sich, zunächst die Skripterstellung für alle Objekte auf einmal vorzunehmen und dann bei Bedarf zu diesem Schritt zurückzukehren und Objekte auszuschließen, wenn der Assistent Skript- oder Transformationsfehler meldet. Der Assistent führt zuerst die Skripterstellung für die Objekte in der Datenbank durch (unter Verwendung von SMO) und nimmt anschließend eine Nachbearbeitung des generierten Skripts vor, um eine Reihe von Regex-basierten Validierungs- und Transformationsregeln anzuwenden.

![alt text](./media/sql-database-migration-wizard/04MigrationWizard.png)

4.	Wählen Sie "Erweitert" aus, und überprüfen Sie die vom Assistenten verwendeten Konfigurationsoptionen. Für einen ersten Durchlauf, insbesondere bei einer großen Datenbank, legen Sie **Skript-/Tabellendaten** auf "Nur Tabellenschema" fest und stellen sicher, dass **Zielserver** auf "Microsoft Azure SQL-Datenbank V12" und **Kompatibilitätsprüfungen** auf "Überschreiben: Alle Kompatibilitätsprüfungen durchführen" festgelegt ist.

![alt text](./media/sql-database-migration-wizard/05MigrationWizard.png)

5.	Klicken Sie auf **Weiter**, um die Optionen zu überprüfen. Klicken Sie dann erneut auf **Weiter**, um das Skript zu generieren und zu transformieren. Sie können das Skript auf der Registerkarte "SQL-Skript" überprüfen.

6.	Bei der Skriptgenerierung werden Fehler gemeldet, wenn das Schema inkompatibel mit der SQL-Datenbank ist und vom Assistenten nicht umgewandelt werden kann. Im nachfolgenden Screenshot wurden vom Transformationsprozess Probleme mit einer gespeicherten Prozedur festgestellt. In diesem Fall wurde eine Prozedur für Volltextsuche geschrieben, die in V12 noch nicht unterstützt wird (sie wird jedoch in einer späteren Version unterstützt).

![alt text](./media/sql-database-migration-wizard/06MigrationWizard.png) – Abhängig von Ihrer Bewertung der Fehler empfiehlt es sich, zum Assistenten zurückzukehren und Objekte auszuschließen, die Probleme verursachen, und dann das Skript erneut zu generieren. Berücksichtigen Sie bei Ihren Überlegungen zur Behandlung der Fehler die Auswirkungen auf andere Objekte in der Datenbank und auf die Anwendungen, die die Datenbank verwenden.– Wenn das Skript Fehler enthält, die korrigiert werden müssen, können Sie eine Nur-Schema-Skriptdatei auf der Registerkarte "SQL-Skript" speichern und das Skript in Ihrem bevorzugten Texteditor öffnen und bearbeiten, um die Fehler zu korrigieren, bevor Sie es außerhalb des SQL Azure Migrations-Assistenten (SAMW) für die neue Datenbank ausführen, die Sie in Schritt 1 erstellt haben. Wenn das Skript sehr groß ist oder eine große Anzahl von Fehlern enthält, sollten Sie Option 3 verwenden. Beachten Sie, dass Sie die generierte Skriptdatei zwar in Visual Studio importieren können, jedoch der Import aus einer SQL-Datei erheblich länger dauern kann als der Import aus einer Datenbank, wie in Option 3 beschrieben.

7.	Wenn keine Fehler vorliegen oder Sie die aus der Generierung resultierende Fehlerquelle eliminiert haben, können Sie auf **Weiter** klicken und dann auf der Folgeseite auf **Verbindung mit Server herstellen**, um eine Verbindung zu dem Azure SQL-Datenbankserver herzustellen, den Sie in Schritt 1 erstellt haben.

![alt text](./media/sql-database-migration-wizard/07MigrationWizard.png)

8.	Im nächsten Schritt wählen Sie die Datenbank aus, für die das Skript ausgeführt werden soll. Alle auf dem Server vorhandenen Datenbanken werden aufgelistet. Wählen Sie die Datenbank aus, die Sie in Schritt 1 erstellt haben. Die Datenbank sollte leer sein und über einen entsprechenden Tarif für diesen Vorgang verfügen. 
- Wenn Sie möchten, können Sie an diesem Punkt mithilfe des Assistenten eine Datenbank erstellen. Klicken Sie hierzu auf "Datenbank erstellen", um die Datenbank zu konfigurieren und zu erstellen. Im Abschnitt "Erste Schritte" finden Sie Anleitungen zum Auswählen der Leistungsstufe, die beim Migrationsvorgang verwendet werden soll.
- Nachdem Sie die Datenbank erstellt haben, wählen Sie sie aus, und fahren Sie fort. 

9.	Klicken Sie bei Auswahl einer leeren Datenbank auf **Weiter**, und bestätigen Sie, dass Sie das Skript ausführen möchten, um die Migration abzuschließen.

<!---HONumber=58-->