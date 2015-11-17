<properties
   pageTitle="Erste Schritte mit der Bedrohungserkennung von SQL-Datenbank"
   description="Gewusst wie: Erste Schritte mit der Bedrohungserkennung von SQL-Datenbank im Azure-Vorschauportal"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jeffreyg"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/29/2015"
   ms.author="ronmat; ronitr"/>


# Erste Schritte mit der Bedrohungserkennung von SQL-Datenbank

## Übersicht

Die Bedrohungserkennung erkennt anormale Datenbankaktivitäten, die auf potenzielle Sicherheitsrisiken für die Datenbank hindeuten. Die Bedrohungserkennung befindet sich in der Vorschauphase und wird für die Version V12 von Azure SQL-Datenbank unterstützt.

Die Bedrohungserkennung bietet eine neue Sicherheitsebene und ermöglicht es den Kunden, auf erkannte potenzielle Bedrohungen zu reagieren. Zu diesem Zweck werden Sicherheitshinweise zu anormalen Aktivitäten bereitgestellt. Die Benutzer können die verdächtigen Ereignisse mithilfe der [Azure SQL-Datenbanküberwachung](sql-database-auditing-get-started.md) untersuchen, um zu ermitteln, ob sie auf einen Zugriffsversuch zurückzuführen sind oder die Verletzung der Datensicherheit oder den Missbrauch von Daten zum Ziel haben. Die Bedrohungserkennung vereinfacht den Umgang mit potenziellen Bedrohungen für die Datenbank, ohne das Fachwissen eines Sicherheitsexperten besitzen oder komplexe Sicherheitsüberwachungssysteme verwalten zu müssen.

So erkennt die Bedrohungserkennung beispielsweise bestimmte anormale Datenbankaktivitäten, die auf eine potenzielle Einschleusung von SQL-Befehlen hindeuten. Die Einschleusung von SQL-Befehlen ist ein verbreitetes Sicherheitsproblem für Webanwendungen im Internet und wird für Angriffe auf datengesteuerte Anwendungen verwendet. Die Angreifer nutzen Sicherheitslücken der Anwendung, um böswillige SQL-Anweisungen in Eingabefelder der Anwendung einzuschleusen und so an die in der Datenbank enthaltenen Daten zu gelangen oder diese zu verändern.


## Einrichten der Bedrohungserkennung für Ihre Datenbank

1. Rufen Sie das Azure-Vorschauportal ([https://portal.azure.com](https://portal.azure.com)) auf.

2. Navigieren Sie zum Konfigurationsblatt der SQL-Datenbank, die Sie überwachen möchten. Wählen Sie auf dem Blatt **Einstellungen** die Option für Überwachung und Bedrohungserkennung.

	![Navigationsbereich][1]

3. Aktivieren Sie auf dem Konfigurationsblatt für die Überwachung und Bedrohungserkennung die Überwachung. Daraufhin werden die Einstellungen für die Bedrohungserkennung angezeigt.

	![Navigationsbereich][2]

4. Aktivieren Sie die Bedrohungserkennung.

5. Konfigurieren Sie die Liste der E-Mail-Empfänger, die bei Erkennung anormaler Datenbankaktivitäten einen Sicherheitshinweis erhalten sollen.

6. Klicken Sie auf dem Konfigurationsblatt für die Überwachung und Bedrohungserkennung auf **Speichern**, um die neue oder aktualisierte Richtlinie für die Überwachung und Bedrohungserkennung zu speichern.

	![Navigationsbereich][3]


## Untersuchen anormaler Datenbankaktivitäten bei Erkennung eines verdächtigen Ereignisses

1. Bei Erkennung anormaler Datenbankaktivitäten erhalten Sie eine E-Mail-Benachrichtigung. <br/> Die E-Mail enthält Informationen zum verdächtigen Sicherheitsereignis (wie etwa Art der anormalen Aktivitäten, Datenbankname, Servername und Zeit des Ereignisses). Darüber hinaus enthält sie Angaben zu möglichen Ursachen und empfohlenen Maßnahmen zur Untersuchung und Abwehr der potenziellen Bedrohung für die Datenbank.<br/>

	![Navigationsbereich][4]

2. Klicken Sie in der E-Mail auf den Link für das Azure SQL-Überwachungsprotokoll, um das Azure-Portal aufzurufen und die relevanten Überwachungsdatensätze für die Zeit des verdächtigen Ereignisses anzuzeigen.

	![Navigationsbereich][5]

3. Klicken Sie auf die Überwachungsdatensätze, um weitere Details zu den verdächtigen Datenbankaktivitäten (wie etwa SQL-Anweisung, Fehlerursache und Client-IP) anzuzeigen.

	![Navigationsbereich][6]

4. Klicken Sie auf dem Blatt mit den Überwachungsdatensätzen auf **In Excel öffnen**, um eine vorkonfigurierte Excel-Vorlage zu öffnen, das Überwachungsprotokoll zu importieren und eine ausführlichere Analyse für die Zeit des verdächtigen Ereignisses durchzuführen.<br/> **Hinweis:** Ab Excel 2010 werden Power Query und die Einstellung **Schnelles Kombinieren** benötigt.

	![Navigationsbereich][7]

5. So konfigurieren Sie die Einstellung **Schnelles Kombinieren**: Klicken Sie auf der Registerkarte **POWER QUERY** des Menübands auf **Optionen**, um das gleichnamige Dialogfeld anzuzeigen. Navigieren Sie zum Datenschutzabschnitt, und wählen Sie die zweite Option zum Ignorieren der Sicherheitsstufen und zur potenziellen Verbesserung der Leistung:

	![Navigationsbereich][8]

6. Wenn Sie SQL-Überwachungsprotokolle laden möchten, vergewissern Sie sich, dass die Parameter auf der Einstellungsregisterkarte ordnungsgemäß festgelegt sind. Klicken Sie dann im Menüband auf „Daten“ und anschließend auf die Schaltfläche „Alle aktualisieren“.

	![Navigationsbereich][9]

7. Die Ergebnisse werden auf dem Blatt mit den SQL-Überwachungsprotokollen angezeigt. Dadurch können Sie die erkannten anormalen Aktivitäten eingehender untersuchen und die Auswirkungen des Sicherheitsereignisses in Ihrer Anwendung begrenzen.


<!--Image references-->
[1]: ./media/sql-database-threat-detection-get-started/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection-get-started/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection-get-started/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection-get-started/4_td_email.png
[5]: ./media/sql-database-threat-detection-get-started/5_td_audit_records.png
[6]: ./media/sql-database-threat-detection-get-started/6_td_audit_record_details.png
[7]: ./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png
[8]: ./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png
[9]: ./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png

<!---HONumber=Nov15_HO2-->