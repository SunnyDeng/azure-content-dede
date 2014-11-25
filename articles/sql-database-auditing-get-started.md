<properties title="Get started with SQL database auditing" pageTitle="Get started with SQL database auditing | Azure" description="Get started with SQL database auditing" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId=""  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg" />

# Erste Schritte mit der SQL-Datenbanküberwachung

Die Azure SQL-Datenbanküberwachung verfolgt Datenbankereignisse und schreibt überwachte Ereignisse in ein Überwachungsprotokoll in Ihr Azure-Speicherkonto. Die Überwachung steht auf den Serviceebenen „Basic“, „Standard“ und „Premium“ als Vorschau zur Verfügung. [Melden Sie sich für die Vorschau an][Melden Sie sich für die Vorschau an], um die Überwachung zu verwenden.

Die Überwachung kann Ihnen dabei helfen, die gesetzlichen Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftsspezifische Bedenken oder mutmaßliche Sicherheitsverstöße hinweisen können.

Überwachungstools ermöglichen und unterstützen die Einhaltung von Konformitätsstandards, gewährleisten aber keine Konformität. Weitere Informationen zu Azure-Programmen, die die Einhaltung von Standards unterstützen, finden Sie im [Microsoft Azure-Vertrauenscenter][Microsoft Azure-Vertrauenscenter].

-   [Grundlagen zur Azure SQL-Datenbanküberwachung][Grundlagen zur Azure SQL-Datenbanküberwachung]
-   [Einrichten der Überwachung für Ihre Datenbank][Einrichten der Überwachung für Ihre Datenbank]
-   [Analysieren von Überwachungsprotokollen und -berichten][Analysieren von Überwachungsprotokollen und -berichten]

## <span id="subheading-1"></span>Grundlagen zur Azure SQL-Datenbanküberwachung</a>

Die Überwachung richten Sie im Azure-Vorschauportal ein, aber es macht keinen Unterschied, ob Sie die Datenbank mithilfe des Azure-Portals oder des Azure-Vorschauportals erstellt haben. Die SQL-Datenbanküberwachung bietet Ihnen folgende Möglichkeiten:

-   **Beibehalten** eines Überwachungspfads von ausgewählten Ereignissen. Definieren Sie Kategorien von Datenbankaktionen und -ereignissen, die protokolliert werden sollen.
-   **Melden** von Datenbankaktivitäten. Verwenden Sie vorkonfigurierte Berichte und ein Dashboard, um schnell mit der Berichterstellung zu Aktivitäten und Ereignissen beginnen zu können.
-   **Analysieren** von Berichten. Finden Sie verdächtige Ereignisse, ungewöhnliche Aktivitäten und Trends.

Sie können die folgenden Aktivitäten und Ereignisse überwachen:

-   **Datenzugriffe**
-   **Schemaänderungen (DDL)**
-   **Datenänderungen (DML)**
-   **Konten, Rollen und Berechtigungen (DCL)**
-   **Sicherheitsausnahmen**

Weitere Informationen zu den protokollierten Aktivitäten und Ereignissen finden Sie unter [Referenz zu Überwachungsprotokollformaten (Dokumentdateidownload)][Referenz zu Überwachungsprotokollformaten (Dokumentdateidownload)].

Sie können auch das Speicherkonto auswählen, in dem Überwachungsprotokolle gespeichert werden.

### Sicherheitsaktivierte Verbindungszeichenfolge

Wenn Sie die Überwachung einrichten, stellt Azure Ihnen eine sicherheitsaktivierte Verbindungszeichenfolge für die Datenbank bereit. Nur für Clientanwendungen, die diese Verbindungszeichenfolge verwenden, werden die Aktivitäten und Ereignisse protokolliert, daher müssen Sie vorhandene Clientanwendungen aktualisieren, um das neue Zeichenfolgenformat zu verwenden.

Herkömmliches Verbindungszeichenfolgenformat: \<*Servername*\>.database.windows.net

Sicherheitsaktivierte Verbindungszeichenfolge: \<*Servername*\>.database.**secure**.windows.net

## <span id="subheading-2"></span></a>Einrichten der Überwachung für Ihre Datenbank

1.  [Registrieren Sie sich für die Überwachungsvorschau][Melden Sie sich für die Vorschau an].
2.  [Registrieren Sie sich auch für die Vorschau auf neue Serviceebenen][Registrieren Sie sich auch für die Vorschau auf neue Serviceebenen] und erstellen Sie eine „Basic“-, „Standard“- oder „Premium“-Datenbank.
3.  Starten Sie das [Azure-Vorschauportal][Azure-Vorschauportal] unter <https://portal.azure.com>.
4.  Klicken Sie auf die zu überwachende Datenbank, und klicken Sie dann auf **Überwachungsvorschau**, um die Überwachungsvorschau zu aktivieren und das Konfigurationsfenster für die Überwachung zu starten.

    ![][0]

5.  Im Konfigurationsfenster für die Überwachung wählen Sie das Azure-Speicherkonto aus, in dem die Protokolle gespeichert werden. **Tipp:** Verwenden Sie dasselbe Speicherkonto für alle überwachten Datenbanken, um die vorkonfigurierten Berichtsvorlagen optimal zu nutzen.

    ![][1]

6.  Klicken Sie unter **Überwachungsoptionen** auf **Alle**, um alle Ereignisse zu protokollieren oder einzelne Ereignistypen auszuwählen.

    ![][2]

7.  Aktivieren Sie die Option **Diese Konfiguration als Standard speichern**, wenn diese Einstellungen auf alle zukünftigen Datenbanken auf dem Server sowie auf alle Datenbank angewendet werden sollen, für die die Überwachung bereits eingerichtet ist. Sie können die Einstellungen später für die einzelnen Datenbanken außer Kraft setzen, indem Sie dieselben Schritte befolgen.

    ![][3]

8.  Klicken Sie auf **Datenbankverbindungszeichenfolgen anzeigen**, und kopieren Sie dann die entsprechende sicherheitsaktivierte Verbindungszeichenfolge für Ihre Anwendung oder notieren Sie sich die Zeichenfolge. Verwenden Sie diese Zeichenfolge für alle Clientanwendungen, deren Aktivität Sie überwachen möchten.

    ![][4]

9.  Klicken Sie auf **OK**.

## <span id="subheading-3"></span>Analysieren von Überwachungsprotokollen und -berichten</a>

Überwachungsprotokolle werden im Azure-Speicherkonto, das Sie während der Einrichtung ausgewählt haben, in einer einzelnen Azure-Speichertabelle namens **Überwachungsprotokolle** aggregiert. Sie können Protokolldateien mithilfe eines Tools wie [Azure-Speicher-Explorer][Azure-Speicher-Explorer] anzeigen.

Eine vorkonfigurierte Dashboard-Berichtsvorlage steht als [herunterladbare Excel-Arbeitsmappe][herunterladbare Excel-Arbeitsmappe] zur Verfügung, um Sie bei der schnellen Analyse von Protokolldaten zu unterstützen. Damit Sie die Vorlage für Ihre Überwachungsprotokolle verwenden können, benötigen Sie Excel 2013 oder höher und Power Query, das [hier][hier] heruntergeladen werden kann.

Die Vorlage enthält fiktive Beispieldaten und Sie können Power Query so einrichten, dass Ihr Überwachungsprotokoll direkt aus dem Azure-Speicherkonto importiert wird.

Ausführlichere Anweisungen zur Arbeit mit der Berichtsvorlage finden Sie unter [Vorgehensweise (Dokumentdownload)][Vorgehensweise (Dokumentdownload)].

![][5]

<!--Anchors--> <!--Image references--> <!--Link references-->

  [Melden Sie sich für die Vorschau an]: http://go.microsoft.com/fwlink/?LinkId=404163
  [Microsoft Azure-Vertrauenscenter]: http://azure.microsoft.com/de-de/support/trust-center/compliance/
  [Grundlagen zur Azure SQL-Datenbanküberwachung]: #subheading-1
  [Einrichten der Überwachung für Ihre Datenbank]: #subheading-2
  [Analysieren von Überwachungsprotokollen und -berichten]: #subheading-3
  [Registrieren Sie sich auch für die Vorschau auf neue Serviceebenen]: https://account.windowsazure.com/PreviewFeatures?fid=premiumdb
  [Azure-Vorschauportal]: https://portal.azure.com
  [0]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
  [1]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
  [2]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
  [3]: ./media/sql-database-auditing-get-started/sql-database-get-started-saveconfigasdefault.png
  [4]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
  [Azure-Speicher-Explorer]: http://azurestorageexplorer.codeplex.com/
  [herunterladbare Excel-Arbeitsmappe]: http://go.microsoft.com/fwlink/?LinkId=403540
  [hier]: http://www.microsoft.com/de-de/download/details.aspx?id=39379
  [5]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
