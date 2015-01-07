<properties title="Get started with SQL database auditing" pageTitle="Erste Schritte mit der SQL-Datenbanküberwachung | Azure" description="Get started with SQL database auditing" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId="" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/31/2015" ms.author="jeffreyg" />
 
# Erste Schritte mit SQL Database-Auditing 
<p> Azure SQL Database-Auditing erfasst Datenbankereignisse und trägt die überwachten Ereignisse in ein Überwachungsprotokoll in Ihrem Azure Storage-Konto ein. Das Auditing ist in der Vorschau für die Dienstebenen Basic, Standard und Premium verfügbar.

Die Überwachung kann Ihnen dabei helfen, die gesetzlichen Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftsspezifische Bedenken oder mutmaßliche Sicherheitsverstöße hinweisen können. 

Auditingtools ermöglichen und erleichtern die Einhaltung von Normen, garantieren diese jedoch nicht. Weitere Informationen zu Azure-Programmen, die die Einhaltung von Standards unterstützen, stehen im <a href="http://azure.microsoft.com/de-de/support/trust-center/compliance/" target="_blank">Azure-Vertrauenscenter</a>zur Verfügung.

+ [Grundlagen zur Azure SQL-Datenbanküberwachung] 
+ [Einrichten der Überwachung für Ihre Datenbank]
+ [Analysieren von Überwachungsprotokollen und -berichten]

## <a id="subheading-1">Grundlagen des Azure SQL Database-Auditings</a>

Das Auditing wird im Azure-Vorschauportal eingerichtet; es spielt jedoch keine Rolle, ob Sie die Datenbank mit dem Azure-Portal oder mit dem Azure-Vorschauportal erstellt haben. Das SQL Database-Auditing bietet folgende Möglichkeiten:

- **Beibehalten** eines Überwachungspfads von ausgewählten Ereignissen. Definieren Sie Kategorien für zu protokollierende Datenbankaktionen und -ereignisse.
- **Melden** von Datenbankaktivitäten. Verwenden Sie vorkonfigurierte Berichte und ein Dashboard, um schnell mit der Berichterstattung über Aktivitäten und Ereignisse zu beginnen.
- **Analysieren** von Berichten. Suchen Sie verdächtige Ereignisse, ungewöhnliche Aktivitäten und Trends.

Sie können die folgenden Aktivitäten und Ereignisse überwachen:

- **Datenzugriffe**
- **Schemaänderungen (DDL)**
- **Datenänderungen (DML)**
- **Konten, Rollen und Berechtigungen (DCL)**
- **Sicherheitsausnahmen**

Weitere Informationen zu den protokollierten Aktivitäten und Ereignissen stehen unter <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referenz zu Überwachungsprotokollformaten (Dokumentdateidownload)</a>zur Verfügung. 

Außerdem können Sie das Speicherkonto wählen, in dem Überwachungsprotokolle gespeichert werden.

### Verbindungszeichenfolge mit aktivierter Sicherheit
Beim Einrichten des Auditings stellt Azure für die Datenbank eine Verbindungszeichenfolge mit aktivierter Sicherheit bereit. Aktivitäten und Ereignisse werden nur für Clientanwendungen protokolliert, die diese Verbindungszeichenfolge verwenden. Sie müssen vorhandene Clientanwendungen also aktualisieren, damit sie das neue Zeichenfolgenformat verwenden.

Herkömmliches Format für die Verbindungszeichenfolge: <*server name*>.database.windows.net

Verbindungszeichenfolge mit aktivierter Sicherheit: <*server name*>.database.**secure**.windows.net


## <a id="subheading-2"></a>Einrichten des Auditings für Ihre Datenbank

1. Starten Sie das <a href="https://portal.azure.com" target="_blank">Azure-Vorschauportal</a> unter https://portal.azure.com. Alternativ können Sie auch das <a href= "https://manage.windowsazure.com/" target="_bank">klassische Azure-Portal</a> unter https://manage.windowsazure.com/ starten. Details finden Sie weiter unten.
2. Navigieren Sie zum Konfigurationsfenster der Datenbank, die Sie überwachen möchten. Blättern Sie nach unten zum Abschnitt **Vorgänge**, und klicken Sie dann auf **Überwachung**, um die Überwachung zu aktivieren und das Fenster für die Überwachungskonfiguration zu starten.

	![][1]

3. Wählen Sie im Fenster zur Konfiguration des Auditings das Azure-Speicherkonto aus, in dem die Protokolle gespeichert werden sollen. **Tipp:** Verwenden Sie für alle überwachten Datenbanken dasselbe Speicherkonto, um die vorkonfigurierten Berichtsvorlagen optimal einzusetzen.

	![][2]

4. Klicken Sie unter **Überwachungsoptionen** auf **Alle**, um alle Ereignisse zu protokollieren, oder wählen Sie einzelne Ereignistypen aus.

	![][3]

5. Aktivieren Sie die Option **Diese Konfiguration als Standard speichern**, wenn diese Einstellungen auf alle zukünftigen Datenbanken auf dem Server sowie auf alle Datenbank angewendet werden sollen, für die die Überwachung bereits eingerichtet ist. Mit den gleichen Schritten können Sie die Einstellungen später für jede Datenbank außer Kraft setzen. 

6. Klicken Sie auf **Datenbankverbindungszeichenfolge anzeigen**, und kopieren oder notieren Sie dann die entsprechende sicherheitsaktivierte Verbindungszeichenfolge für Ihre Anwendung. Verwenden Sie diese Zeichenfolge für alle Clientanwendungen, deren Aktivität Sie überwachen möchten.

	![][5]

7. Klicken Sie auf **OK**.



## <a id="subheading-3">Analysieren von Überwachungsprotokollen und -berichten</a>

Überwachungsprotokolle werden im Azure-Speicherkonto, das Sie während der Einrichtung ausgewählt haben, in einer einzelnen Azure-Speichertabelle namens **AuditLogs** zusammengefasst. Sie können Protokolldateien mit einem Tool wie <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure-Speicher-Explorer</a>anzeigen.

Eine vorkonfigurierte Dashboard-Berichtsvorlage steht als <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">herunterladbare Excel-Arbeitsmappe</a> zur Verfügung, um Sie bei der schnellen Analyse von Protokolldaten zu unterstützen. Damit Sie die Vorlage für Ihre Überwachungsprotokolle verwenden können, benötigen Sie Excel 2013 oder höher und Power Query, das <a href="http://www.microsoft.com/de-de/download/details.aspx?id=39379">hier</a>heruntergeladen werden kann. 

Die Vorlage enthält fiktionale Beispieldaten, und Sie können Power Query so einrichten, dass das Überwachungsprotokoll direkt aus Ihrem Azure-Speicherkonto importiert wird. 

Ausführlichere Anweisungen zur Arbeit mit der Berichtsvorlage stehen unter <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Vorgehensweise (Dokumentdownload)</a>zur Verfügung.

![][6]


## <a id="subheading-4"></a>Einrichten der Überwachung für Ihre Datenbank über das klassische Azure-Portal

1. Starten Sie das <a href= "https://manage.windowsazure.com/" target="_bank">klassische Azure-Portal</a> unter https://manage.windowsazure.com/. 
2. Klicken Sie auf die Datenbank, die Sie überwachen möchten, und dann auf die Registerkarte **Überwachung und Sicherheitsvorschau**.
3. Klicken Sie im Überwachungsabschnitt auf "Aktivieren".

![][7]

4. Bearbeiten Sie den **EREIGNISTYP** nach Bedarf.

![][8]

5. Wählen Sie ein **SPEICHERKONTO** aus.
6. Klicken Sie auf **SPEICHERN**.
7. Klicken Sie für die Verbindungszeichenfolge auf **Gesicherte Verbindungszeichenfolge anzeigenh**.


## <a id="subheading-3">Methoden zur Verwendung in der Produktion</a>
Die Beschreibung in diesem Abschnitt bezieht sich auf die obigen Bildschirmaufnahmen. Sie können das <a href="https://portal.azure.com" target="_blank">Azure-Vorschauportal</a> oder das <a href= "https://manage.windowsazure.com/" target="_bank">klassische Azure-Portal</a> verwenden.
 

## <a id="subheading-4"></a>Sicherheitsaktivierter Zugriff

In der Produktion werden Sie wahrscheinlich fordern, dass der gesamte Datenverkehr von der Datenbank zu allen Anwendungen und Tools überwacht wird. Ändern Sie dafür die Option **Sicherheitsaktivierter Zugriff** von "Optional" in "Erforderlich", und speichern Sie die Richtlinie. Nachdem "Erforderlich" konfiguriert ist, gibt es keine Option für den Zugriff auf die Datenbank über die ursprüngliche Verbindungszeichenfolge, sondern nur über die sicherheitsaktivierte Verbindungszeichenfolge.


![][9]


## <a id="subheading-4"></a>Erneute Speicherschlüsselgenerierung

In der Produktion werden Sie wahrscheinlich Ihre Speicherschlüssel regelmäßig aktualisieren. Der Überwachungsdienst behält Ihre Speicherkontoschlüssel nicht bei. Beim Speichern wird ein schreibgeschützter SAS-Schlüssel (Shared Access Signature) für die Überwachungstabelle produziert (nur der Kunde kann die Überwachungsprotokolle lesen). Für diesen Zweck müssen Sie beim Aktualisieren Ihrer Schlüssel die Richtlinie erneut speichern. Dieser Prozess verläuft wie folgt:


1. Wechseln Sie im Fenster für die Überwachungskonfiguration (oben im Abschnitt zur Einrichtung der Überwachung beschrieben) die Einstellung **Speicherzugriffsschlüssel** von "Primär" in "Sekundär", und klicken Sie auf **SPEICHERN**.
![][10]
2. Gehen Sie zum Fenster für die Speicherkonfiguration, und führen Sie eine **erneute Generation** des "Primären Zugriffsschlüssels" durch.

3. Gehen Sie zurück zum Fenster für die Überwachungskonfiguration, wechseln Sie die Einstellung für den **Speicherzugriffsschlüssel** von "Sekundär" zu "Primär", und klicken Sie auf **SPEICHERN**.

4. Gehen Sie zurück zur Speicherbenutzeroberfläche, und führen Sie eine **erneute Generation** des "Sekundären Zugriffsschlüssels" durch (als Vorbereitung für den nächsten Schlüsselaktualisierungszyklus).
  
## <a id="subheading-4"></a>Automation
Informationen zu PowerShell stehen im <a href="https://github.com/Azure/azure-powershell" target="_blank">PowerShell SDK</a>zur Verfügung.

Informationen zur REST-API finden Sie unter <a href="http://download.microsoft.com/download/D/8/D/D8D90BA1-977F-466B-A839-7823FF37FD02/04-Azure SQL DB Auditing REST API.docx">REST-API für die Azure SQL-Datenbank</a>





<!--Anchors-->
[Grundlagen zur Azure SQL-Datenbanküberwachung]: #subheading-1
[Einrichten der Überwachung für Ihre Datenbank]: #subheading-2
[Analysieren von Überwachungsprotokollen und -berichten]: #subheading-3
[Einrichten der Überwachung für Ihre Datenbank über das klassische Azure-Portal]: #subheading-4


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
[2]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
[3]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
[5]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
[6]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
[7]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-enable.png
[8]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure.png
[9]: ./media/sql-database-auditing-get-started/sql-database-auditing-security-enabled-access.png
[10]: ./media/sql-database-auditing-get-started/sql-database-auditing-storage-account.png






<!--Link references-->
[Link 1 zu einem anderen azure.microsoft.com-Dokumentationsthema]: ../virtual-machines-windows-tutorial/
[Link 2 zu einem anderen azure.microsoft.com-Dokumentationsthema]: ../web-sites-custom-domain-name/
[Link 3 zu einem anderen azure.microsoft.com-Dokumentationsthema]: ../storage-whatis-account/


<!--HONumber=35.1-->
