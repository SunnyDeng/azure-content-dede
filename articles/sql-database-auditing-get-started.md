<properties title="Erste Schritte mit SQL Database-Auditing" pageTitle="Erste Schritte mit SQL Database-Auditing | Azure" description="Erste Schritte mit SQL Database-Auditing" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId="" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg" />

# Erste Schritte mit SQL Database-Auditing

Azure SQL Database-Auditing erfasst Datenbankereignisse und trägt die überwachten Ereignisse in ein Überwachungsprotokoll in Ihrem Azure Storage-Konto ein. Das Auditing ist in der Vorschau für die Dienstebenen Basic, Standard und Premium verfügbar. Um das Auditing zu verwenden, [registrieren Sie sich für die Vorschau][registrieren Sie sich für die Vorschau].

Auditing kann Ihnen helfen, gesetzliche Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Diskrepanzen sowie Anomalien zu erkennen, die Hinweis auf geschäftliche Probleme oder mutmaßliche Sicherheitsverletzungen sein können.

Auditingtools ermöglichen und erleichtern die Einhaltung von Normen, garantieren diese jedoch nicht. Weitere Informationen über Azure-Programme, die die Einhaltung von Normen unterstützen, finden Sie im [Azure Trust Center][Azure Trust Center].

-   [Grundlagen des Azure SQL Database-Auditings][Grundlagen des Azure SQL Database-Auditings]
-   [Einrichten des Auditings für Ihre Datenbank][Einrichten des Auditings für Ihre Datenbank]
-   [Analysieren von Überwachungsprotokollen und -berichten][Analysieren von Überwachungsprotokollen und -berichten]

## <span id="subheading-1"></span>Grundlagen des Azure SQL Database-Auditings</a>

Das Auditing wird im Azure-Vorschauportal eingerichtet; es spielt jedoch keine Rolle, ob Sie die Datenbank mit dem Azure-Portal oder mit dem Azure-Vorschauportal erstellt haben. Das SQL Database-Auditing bietet folgende Möglichkeiten:

-   **Speichern** eines Audit-Trails ausgewählter Ereignisse. Definieren Sie Kategorien für zu protokollierende Datenbankaktionen und -ereignisse.
-   **Melden** von Datenbankaktivitäten. Verwenden Sie vorkonfigurierte Berichte und ein Dashboard, um schnell mit der Berichterstattung über Aktivitäten und Ereignisse zu beginnen.
-   **Analysieren** von Berichten. Suchen Sie verdächtige Ereignisse, ungewöhnliche Aktivitäten und Trends.

Sie können die folgenden Aktivitäten und Ereignisse überwachen:

-   **Datenzugriffe**
-   **Schemaänderungen (DDL)**
-   **Datenänderungen (DML)**
-   **Konten, Rollen und Berechtigungen (DCL)**
-   **Sicherheitsausnahmen**

Weitere Einzelheiten zu den protokollierten Aktivitäten und Ereignissen finden Sie unter [Überwachungsprotokoll-Formatreferenz (DOC-Datei zum Herunterladen)][Überwachungsprotokoll-Formatreferenz (DOC-Datei zum Herunterladen)].

Außerdem können Sie das Speicherkonto wählen, in dem Überwachungsprotokolle gespeichert werden.

### Verbindungszeichenfolge mit aktivierter Sicherheit

Beim Einrichten des Auditings stellt Azure für die Datenbank eine Verbindungszeichenfolge mit aktivierter Sicherheit bereit. Aktivitäten und Ereignisse werden nur für Clientanwendungen protokolliert, die diese Verbindungszeichenfolge verwenden. Sie müssen vorhandene Clientanwendungen also aktualisieren, damit sie das neue Zeichenfolgenformat verwenden.

Herkömmliches Format für die Verbindungszeichenfolge: \<*Servername*\>.database.windows.net

Verbindungszeichenfolge mit aktivierter Sicherheit: \<*Servername*\>.database.**secure**.windows.net

## <span id="subheading-2"></span></a>Einrichten des Auditings für Ihre Datenbank

1.  [Anmelden für die Auditing-Vorschau][registrieren Sie sich für die Vorschau].
2.  Rufen Sie das [Azure-Vorschauportal][Azure-Vorschauportal] unter <https://portal.azure.com> auf.
3.  Klicken Sie auf die Datenbank, die Sie überwachen möchten, und klicken Sie anschließend auf **Auditing Preview**, um die Auditing-Vorschau zu aktivieren und das Fenster für die Konfiguration des Auditings zu starten.

    ![][0]

4.  Wählen Sie im Fenster zur Konfiguration des Auditings das Azure-Speicherkonto aus, in dem die Protokolle gespeichert werden sollen. **Tipp:** Verwenden Sie für alle überwachten Datenbanken dasselbe Speicherkonto, um die vorkonfigurierten Berichtsvorlagen optimal einzusetzen.

    ![][1]

5.  Klicken Sie unter **Auditing Options** auf **All**, um alle Ereignisse zu protokollieren, oder wählen Sie einzelne Ereignistypen.

    ![][2]

6.  Aktivieren Sie **Save this Configuration as Default**, wenn Sie diese Einstellungen auf alle künftigen Datenbanken auf dem Server sowie auf alle Datenbanken anwenden möchten, für die noch kein Auditing eingerichtet wurde. Mit den gleichen Schritten können Sie die Einstellungen später für jede Datenbank außer Kraft setzen.

    ![][3]

7.  Klicken Sie auf **Show database connection strings**, und kopieren oder notieren Sie anschließend die entsprechende Verbindungszeichenfolge mit aktivierter Sicherheit für Ihre Anwendung. Verwenden Sie diese Zeichenfolge für alle Clientanwendungen, deren Aktivität Sie überwachen möchten.

    ![][4]

8.  Klicken Sie auf **OK**.

## <span id="subheading-3"></span>Analysieren von Überwachungsprotokollen und -berichten</a>

Überwachungsprotokolle werden in einer einzigen Azure Store-Tabelle namens **AuditLogs** in dem Azure-Speicherkonto zusammengefasst, das Sie während der Einrichtung gewählt haben. Sie können Protokolldateien mit einem Tool wie [Azure Storage Explorer][Azure Storage Explorer] anzeigen.

Eine vorkonfigurierte Dashboard-Berichtsvorlage steht als [herunterladbares Excel-Tabellenblatt][herunterladbares Excel-Tabellenblatt] zur Verfügung, mit dem Sie Protokolldaten schnell analysieren können. Um die Vorlage in Ihren Überwachungsprotokollen verwenden zu können, benötigen Sie Excel 2013 oder höher und Power Query, das Sie [hier][hier] herunterladen können.

Die Vorlage enthält fiktionale Beispieldaten, und Sie können Power Query so einrichten, dass das Überwachungsprotokoll direkt aus Ihrem Azure-Speicherkonto importiert wird.

Detailliertere Anweisungen zum Arbeiten mit der Berichtsvorlage finden Sie unter [02-Azure SQL DB Audit Logs Excel Report How-To.docx (herunterladbare DOCX-Datei)][02-Azure SQL DB Audit Logs Excel Report How-To.docx (herunterladbare DOCX-Datei)].

![][5]

<!--Anchors--> <!--Image references--> <!--Link references-->

  [registrieren Sie sich für die Vorschau]: http://go.microsoft.com/fwlink/?LinkId=404163
  [Azure Trust Center]: http://azure.microsoft.com/de-de/support/trust-center/compliance/
  [Grundlagen des Azure SQL Database-Auditings]: #subheading-1
  [Einrichten des Auditings für Ihre Datenbank]: #subheading-2
  [Analysieren von Überwachungsprotokollen und -berichten]: #subheading-3
  [Überwachungsprotokoll-Formatreferenz (DOC-Datei zum Herunterladen)]: http://go.microsoft.com/fwlink/?LinkId=506733
  [Azure-Vorschauportal]: https://portal.azure.com
  [0]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
  [1]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
  [2]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
  [3]: ./media/sql-database-auditing-get-started/sql-database-get-started-saveconfigasdefault.png
  [4]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
  [Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
  [herunterladbares Excel-Tabellenblatt]: http://go.microsoft.com/fwlink/?LinkId=403540
  [hier]: http://www.microsoft.com/de-de/download/details.aspx?id=39379
  [02-Azure SQL DB Audit Logs Excel Report How-To.docx (herunterladbare DOCX-Datei)]: http://go.microsoft.com/fwlink/?LinkId=506731
  [5]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
