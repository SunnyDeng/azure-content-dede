<properties
   pageTitle="Erste Schritte mit der Bedrohungserkennung von SQL Data Warehouse"
   description="Erste Schritte mit der Bedrohungserkennung von SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;sonyama;barbkess"/>


# Erste Schritte mit der Bedrohungserkennung von SQL Data Warehouse

## Übersicht

Die Bedrohungserkennung erkennt anomale Datenbankaktivitäten, die auf potenzielle Sicherheitsrisiken für die Datenbank hindeuten. Die Bedrohungserkennung befindet sich in der Vorschauphase und wird für SQL Data Warehouse unterstützt.

Die Bedrohungserkennung bietet eine neue Sicherheitsebene und ermöglicht es den Kunden, auf erkannte potenzielle Bedrohungen zu reagieren. Zu diesem Zweck werden Sicherheitshinweise zu anomalen Aktivitäten bereitgestellt. Benutzer können die verdächtigen Ereignisse mithilfe der [Azure SQL Data Warehouse-Überwachung]sql-data-warehouse-auditing-get-started.md) untersuchen, um zu ermitteln, ob sie auf einen Zugriffsversuch zurückzuführen sind oder die Verletzung der Datensicherheit oder den Missbrauch von Daten im Data Warehouse zum Ziel haben. Die Bedrohungserkennung vereinfacht den Umgang mit potenziellen Bedrohungen für das Data Warehouse. Das Fachwissen eines Sicherheitsexperten oder die Verwaltung komplexer Sicherheitsüberwachungssysteme sind dabei nicht erforderlich.

So erkennt die Bedrohungserkennung beispielsweise bestimmte anomale Datenbankaktivitäten, die auf eine potenzielle Einschleusung von SQL-Befehlen hindeuten. Die Einschleusung von SQL-Befehlen ist ein verbreitetes Sicherheitsproblem für Webanwendungen im Internet und wird für Angriffe auf datengesteuerte Anwendungen verwendet. Die Angreifer nutzen Sicherheitslücken der Anwendung, um böswillige SQL-Anweisungen in Eingabefelder der Anwendung einzuschleusen und so an die in der Datenbank enthaltenen Daten zu gelangen oder diese zu verändern.


## Einrichten der Bedrohungserkennung für Ihre Datenbank

1. Rufen Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com) auf.

2. Navigieren Sie zum Konfigurationsblatt des SQL Data Warehouse, das Sie überwachen möchten. Wählen Sie auf dem Blatt **Einstellungen** die Option für Überwachung und Bedrohungserkennung.

	![Navigationsbereich][1]

3. Aktivieren Sie auf dem Konfigurationsblatt für die Überwachung und Bedrohungserkennung die Überwachung. Daraufhin werden die Einstellungen für die Bedrohungserkennung angezeigt.

	![Navigationsbereich][2]

4. Aktivieren Sie die Bedrohungserkennung.

5. Konfigurieren Sie die Liste der E-Mail-Empfänger, die bei Erkennung anomaler Data Warehouse-Aktivitäten eine Sicherheitsbenachrichtigung erhalten sollen.

6. Klicken Sie auf dem Konfigurationsblatt für die Überwachung und Bedrohungserkennung auf **Speichern**, um die neue oder aktualisierte Richtlinie für die Überwachung und Bedrohungserkennung zu speichern.

	![Navigationsbereich][3]


## Untersuchen anomaler Data Warehouse-Aktivitäten bei Erkennung eines verdächtigen Ereignisses

1. Bei Erkennung anomaler Datenbankaktivitäten erhalten Sie eine E-Mail-Benachrichtigung. <br/> Die E-Mail enthält Informationen zum verdächtigen Sicherheitsereignis (wie etwa Art der anomalen Aktivitäten, Datenbankname, Servername und Zeit des Ereignisses). Darüber hinaus enthält sie Angaben zu möglichen Ursachen und empfohlenen Maßnahmen zur Untersuchung und Abwehr der potenziellen Bedrohung für die Datenbank.<br/>

	![Navigationsbereich][4]

2. Klicken Sie in der E-Mail auf den Link für das Azure SQL-Überwachungsprotokoll, um das klassische Azure-Portal aufzurufen und die relevanten Überwachungsdatensätze für die Zeit des verdächtigen Ereignisses anzuzeigen.

	![Navigationsbereich][5]

3. Klicken Sie auf die Überwachungsdatensätze, um weitere Details zu den verdächtigen Datenbankaktivitäten (wie etwa SQL-Anweisung, Fehlerursache und Client-IP) anzuzeigen.

	![Navigationsbereich][6]

4. Klicken Sie auf dem Blatt mit den Überwachungsdatensätzen auf **In Excel öffnen**, um eine vorkonfigurierte Excel-Vorlage zu öffnen, das Überwachungsprotokoll zu importieren und eine ausführlichere Analyse für die Zeit des verdächtigen Ereignisses durchzuführen.<br/> **Hinweis:** Ab Excel 2010 werden Power Query und die Einstellung **Schnelles Kombinieren** benötigt.

	![Navigationsbereich][7]

5. So konfigurieren Sie die Einstellung **Schnelles Kombinieren**: Klicken Sie auf der Registerkarte **POWER QUERY** des Menübands auf **Optionen**, um das gleichnamige Dialogfeld anzuzeigen. Navigieren Sie zum Datenschutzabschnitt, und wählen Sie die zweite Option zum Ignorieren der Sicherheitsstufen und zur potenziellen Verbesserung der Leistung:

	![Navigationsbereich][8]

6. Wenn Sie SQL-Überwachungsprotokolle laden möchten, vergewissern Sie sich, dass die Parameter auf der Einstellungsregisterkarte ordnungsgemäß festgelegt sind. Klicken Sie dann im Menüband auf „Daten“ und anschließend auf die Schaltfläche „Alle aktualisieren“.

	![Navigationsbereich][9]

7. Die Ergebnisse werden auf dem Blatt mit den SQL-Überwachungsprotokollen angezeigt. Dadurch können Sie die erkannten anomalen Aktivitäten eingehender untersuchen und die Auswirkungen des Sicherheitsereignisses in Ihrer Anwendung begrenzen.


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png

<!---HONumber=AcomDC_0309_2016-->