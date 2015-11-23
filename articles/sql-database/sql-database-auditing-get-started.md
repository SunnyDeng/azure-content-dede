<properties
	pageTitle="Erste Schritte bei der Überwachung von SQL-Datenbank | Microsoft Azure"
	description="Erste Schritte bei der Überwachung von SQL- Datenbank"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/12/2015"
	ms.author="jeffreyg; ronitr"/>
 
# Erste Schritte bei der Überwachung von SQL- Datenbank
Die Azure SQL-Datenbanküberwachung verfolgt Datenbankereignisse und schreibt überwachte Ereignisse in ein Überwachungsprotokoll in Ihr Azure-Speicherkonto. Die Überwachung ist in den Tarifen Basic, Standard und Premium allgemein verfügbar.

Die Überwachung kann Ihnen dabei helfen, die gesetzlichen Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftsspezifische Bedenken oder mutmaßliche Sicherheitsverstöße hinweisen können.

Überwachungstools ermöglichen und unterstützen die Einhaltung von Konformitätsstandards, gewährleisten aber keine Konformität. Weitere Informationen zu Azure-Programmen, die die Einhaltung von Standards unterstützen, finden Sie im [Microsoft Azure-Vertrauenscenter](http://azure.microsoft.com/support/trust-center/compliance).

+ [Grundlagen zur Überwachung von Azure SQL-Datenbank]
+ [Einrichten der Überwachung für Ihre Datenbank]
+ [Analysieren von Überwachungsprotokollen und -berichten]

##<a id="subheading-1"></a>Grundlagen zur Überwachung von Azure SQL-Datenbank

In den folgenden Abschnitten wird die Konfiguration der Überwachung mit dem Azure-Vorschauportal beschrieben. Alternativ können Sie das klassische Portal verwenden ([Einrichten der Überwachung für Ihre Datenbank über das klassische Azure-Portal]).

Bei der Überwachung von SQL-Datenbank haben Sie folgende Möglichkeiten:

- **Beibehalten** eines Überwachungspfads von ausgewählten Ereignissen. Sie können Kategorien für zu protokollierende Datenbankaktionen und -ereignisse konfigurieren.
- **Melden** von Datenbankaktivitäten. Sie können vorkonfigurierte Berichte und ein Dashboard verwenden, um schnell mit der Berichterstattung über Aktivitäten und Ereignisse zu beginnen.
- **Analysieren** von Berichten. Sie können nach verdächtigen Ereignissen, ungewöhnliche Aktivitäten und Trends suchen.

> [AZURE.NOTE]Sie können jetzt proaktive Warnungen vor anomalen Datenbankaktivitäten erhalten, die auf mögliche Sicherheitsbedrohungen hinweisen können, indem Sie das neue Feature **Bedrohungserkennung** verwenden. Es befindet sich derzeit in der Vorschauphase. Sie können die Bedrohungserkennung auf dem Blatt für die Überwachungskonfiguration aktivieren und konfigurieren. Weitere Informationen finden Sie unter [Erste Schritte mit der Bedrohungserkennung](sql-database-threat-detection-get-started.md).

Sie können eine Überwachung für die folgenden Ereigniskategorien einrichten:

**Einfaches SQL** und **Parametrisiertes SQL**, wobei die erfassten Überwachungsprotokolle folgendermaßen klassifiziert werden:

- **Datenzugriffe**
- **Schemaänderungen (DDL)**
- **Datenänderungen (DML)**
- **Konten, Rollen und Berechtigungen (DCL)**
- **Gespeicherte Prozeduren**, **Anmeldung** und **Transaktionsverwaltung**.

Für jede Ereigniskategorie wird die Überwachung auf **Erfolg** und **Fehler** getrennt voneinander konfiguriert.

Weitere Einzelheiten zu den überwachten Aktivitäten und Ereignissen finden Sie unter [Referenz zu Überwachungsprotokollformaten (DOC-Datei zum Herunterladen)](http://go.microsoft.com/fwlink/?LinkId=506733).

Überwachungsprotokolle werden in Ihrem Azure-Speicherkonto gespeichert. Sie können eine Aufbewahrungsdauer für das Überwachungsprotokoll festlegen, nach deren Ablauf die alten Protokolle gelöscht werden.

Eine Überwachungsrichtlinie kann für eine spezifische Datenbank oder als Standardserverrichtlinie definiert werden. Eine Standardrichtlinie für die Serverüberwachung wird auf alle Datenbanken auf einem Server angewendet, für die keine Datenbanküberwachungsrichtlinie definiert wurde, die Vorrang hat.

Überprüfen Sie vor der Einrichtung der Überwachung, ob Sie einen [kompatiblen Client](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) verwenden.


##<a id="subheading-2"></a>Einrichten der Überwachung für Ihre Datenbank

1. Starten Sie das [Azure-Vorschauportal](https://portal.azure.com) unter https://portal.azure.com. Alternativ können Sie auch das [klassische Azure-Portal](https://manage.windowsazure.com/) unter https://manage.windowsazure.com/ starten. Details finden Sie weiter unten.

2. Navigieren Sie zum Blatt mit den Einstellungen der SQL-Datenbank-/SQL Server-Instanz, die Sie überwachen möchten. Wählen Sie auf dem Blatt mit den Einstellungen die Option für **Überwachung und Bedrohungserkennung**.

	![Navigationsbereich][1]

3. Aktivieren Sie die Überwachung auf dem Blatt für die Überwachungskonfiguration (**EIN**).

4. Wählen Sie **Speicherdetails**, um das Blatt „Speicherung von Überwachungsprotokollen“ zu öffnen. Wählen Sie das Azure-Speicherkonto aus, in dem die Protokolle gespeichert werden sollen, und geben Sie die Aufbewahrungsdauer an. **Tipp:** Verwenden Sie für alle überwachten Datenbanken dasselbe Speicherkonto, um die Berichtvorlagen für die Überwachung optimal einzusetzen.

	![Navigationsbereich][2]

5. Klicken Sie auf **Überwachte Ereignisse**, um anzupassen, welche Ereignisse überwacht werden sollen. Klicken Sie auf dem Blatt **Protokollierung nach Ereignis** auf **Erfolg** und **Fehler**, um alle Ereignisse zu protokollieren, oder wählen Sie einzelne Ereigniskategorien aus.


6. Sie können das Kontrollkästchen **Überwachungseinstellungen von Server erben** aktivieren, um anzugeben, dass diese Datenbank gemäß den Einstellungen des zugehörigen Servers überwacht wird. Wenn Sie diese Option aktivieren, wird ein Link angezeigt. Sie können ihn verwenden, um die Überwachungseinstellungen des Servers für diesen Kontext anzuzeigen oder zu ändern.

	![Navigationsbereich][3]

7. Nachdem Sie Ihre Überwachungseinstellungen konfiguriert haben, können Sie die Bedrohungserkennung auf **EIN** festlegen und die E-Mails konfigurieren, um Sicherheitswarnungen zu erhalten. Weitere Informationen finden Sie auf der Seite [Erste Schritte mit der Bedrohungserkennung](sql-database-threat-detection-get-started.md).

8. Klicken Sie auf **Speichern**.



##<a id="subheading-3"></a>Analysieren von Überwachungsprotokollen und -berichten

Überwachungsprotokolle werden im Azure-Speicherkonto, das Sie während der Einrichtung ausgewählt haben, in einer einzelnen Azure-Speichertabelle mit dem Präfix **SQLDBAuditLogs** zusammengefasst. Sie können Protokolldateien mithilfe eines Tools wie [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/) anzeigen.

Eine vorkonfigurierte Berichtvorlage steht als [herunterladbares Excel-Arbeitsblatt](http://go.microsoft.com/fwlink/?LinkId=403540) zur Verfügung, mit dem Sie Protokolldaten schnell analysieren können. Damit Sie die Vorlage für Ihre Überwachungsprotokolle verwenden können, benötigen Sie Excel 2013 oder höher und Power Query, das [hier](http://www.microsoft.com/download/details.aspx?id=39379) heruntergeladen werden kann.

Sie können Ihre Überwachungsprotokolle über das Azure-Speicherkonto mit Power Query direkt in die Excel-Vorlage importieren. Anschließend können Sie Ihre Überwachungsdatensätze untersuchen und zusätzlich zu den Protokolldaten Dashboards und Berichte erstellen.


![Navigationsbereich][4]


##<a id="subheading-4"></a>Einrichten der Überwachung für Ihre Datenbank über das klassische Azure-Portal

1. Starten Sie das [klassische Azure-Portal](https://manage.windowsazure.com/) unter https://manage.windowsazure.com/.

2. Klicken Sie auf die SQL-Datenbank / SQL Server-Instanz, die Sie überwachen möchten, und klicken Sie dann auf die Registerkarte **ÜBERWACHUNG & SICHERHEIT**.

3. Legen Sie für die Überwachung **AKTIVIERT** fest.

	![Navigationsbereich][5]

4. Bearbeiten Sie **PROTOKOLLIERUNG NACH EREIGNIS** nach Bedarf, um anzupassen, welche Ereignisse überwacht werden sollen.

	![Navigationsbereich][6]

5. Wählen Sie ein **SPEICHERKONTO**, und konfigurieren Sie die **AUFBEWAHRUNG**.

	![Navigationsbereich][7]

6. Klicken Sie auf **SPEICHERN**.




##<a id="subheading-5">Methoden zur Verwendung in der Produktion</a>
Die Beschreibung in diesem Abschnitt bezieht sich auf die obigen Screenshots. Sie können entweder das [Azure-Vorschauportal](https://portal.azure.com) oder das [klassische Azure-Portal](https://manage.windowsazure.com/) verwenden.


##<a id="subheading-6"></a>Erneute Speicherschlüsselgenerierung

In der Produktion werden Sie wahrscheinlich Ihre Speicherschlüssel regelmäßig aktualisieren. Wenn Sie die Schlüssel aktualisieren, müssen Sie die Überwachungsrichtlinie neu speichern. Dieser Prozess verläuft wie folgt:


1. Schalten Sie auf dem Blatt für die Überwachungskonfiguration die Einstellung für den **Speicherzugriffsschlüssel** von *Primär* auf *Sekundär* um, und klicken Sie auf **SPEICHERN**.

	![][8]

2. Wechseln Sie zum Blatt für die Speicherkonfiguration, und **generieren Sie erneut** den *primären Zugriffsschlüssel*.

3. Wechseln Sie zurück zum Blatt für die Überwachungskonfiguration, ändern Sie den **Speicherzugriffsschlüssel** von *Sekundär* in *Primär*, und klicken Sie auf **SPEICHERN**.

4. Wechseln Sie zurück zur Speicherbenutzeroberfläche, und **generieren Sie erneut** den *sekundären Zugriffsschlüssel* (als Vorbereitung auf den nächsten Schlüsselaktualisierungszyklus).
  
##<a id="subheading-7"></a>Automatisierung
Es gibt mehrere PowerShell-Cmdlets, die Sie zum Konfigurieren der Überwachung in Azure SQL-Datenbank verwenden können.

- [Get-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603731.aspx)
- [Get-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619329.aspx)
- [Remove-AzureRMSqlDatabaseAuditing](https://msdn.microsoft.com/library/azure/mt603796.aspx)
- [Remove-AzureRMSqlServerAuditing](https://msdn.microsoft.com/library/azure/mt603574.aspx)
- [Set-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603531.aspx)
- [Set-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603794.aspx)
- [Use-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619353.aspx)




<!--Anchors-->
[Grundlagen zur Überwachung von Azure SQL-Datenbank]: #subheading-1
[Einrichten der Überwachung für Ihre Datenbank]: #subheading-2
[Analysieren von Überwachungsprotokollen und -berichten]: #subheading-3
[Einrichten der Überwachung für Ihre Datenbank über das klassische Azure-Portal]: #subheading-4
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_storage_account.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_inherit_from_server.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_report_template.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_classic_portal_enable.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_classic_portal_events.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_classic_portal_storage.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_storage_key_rotation.png


 

<!---HONumber=Nov15_HO3-->