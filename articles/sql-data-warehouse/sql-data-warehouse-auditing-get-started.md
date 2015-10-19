<properties 
	pageTitle="Erste Schritte bei der Überwachung von SQL Data Warehouse-Datenbanken | Microsoft Azure" 
	description="Erste Schritte bei der Überwachung von SQL Data Warehouse-Datenbanken" 
	services="sql-data-warehouse" 
	documentationCenter="" 
	authors="twounder" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-data-warehouse" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/06/2015" 
	ms.author="twounder"/>
 
# Erste Schritte bei der Überwachung von SQL Data Warehouse-Datenbanken 
<p> Bei der Azure SQL Data Warehouse-Überwachung werden Datenbankereignisse erfasst und die überwachten Ereignisse in einem Überwachungsprotokoll in Ihrem Azure-Speicherkonto protokolliert.

Die Überwachung kann Ihnen dabei helfen, die gesetzlichen Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftsspezifische Bedenken oder mutmaßliche Sicherheitsverstöße hinweisen können.

Überwachungstools ermöglichen und erleichtern die Einhaltung von Normen, garantieren diese jedoch nicht. Weitere Informationen über Azure-Programme, die die Einhaltung von Normen unterstützen, finden Sie im <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure Trust Center</a>.

+ [Grundlagen zur Datenbanküberwachung] 
+ [Einrichten der Überwachung für Ihre Datenbank]
+ [Analysieren von Überwachungsprotokollen und -berichten]

##<a id="subheading-1"></a>Grundlagen zur Überwachung von Azure SQL Data Warehouse


Die Überwachung von SQL Data Warehouse bietet folgende Möglichkeiten:

- **Beibehalten** eines Überwachungspfads von ausgewählten Ereignissen. Sie können Kategorien für zu protokollierende Datenbankaktionen und -ereignisse konfigurieren.
- **Melden** von Datenbankaktivitäten. Sie können vorkonfigurierte Berichte und ein Dashboard verwenden, um schnell mit der Berichterstattung über Aktivitäten und Ereignisse zu beginnen.
- **Analysieren** von Berichten. Sie können nach verdächtigen Ereignissen, ungewöhnliche Aktivitäten und Trends suchen.

Sie können eine Überwachung für die folgenden Ereigniskategorien einrichten:

**Einfaches SQL** und **Parametrisiertes SQL**, wobei die erfassten Überwachungsprotokolle folgendermaßen klassifiziert werden:

- **Datenzugriffe**
- **Schemaänderungen (DDL)**
- **Datenänderungen (DML)**
- **Konten, Rollen und Berechtigungen (DCL)**
- **Gespeicherte Prozeduren**, **Anmeldung** und **Transaktionsverwaltung**.

Für jede Ereigniskategorie wird die Überwachung auf **Erfolg** und **Fehler** getrennt voneinander konfiguriert.

Weitere Einzelheiten zu den überwachten Aktivitäten und Ereignissen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referenz zu Überwachungsprotokollformaten (DOC-Datei zum Herunterladen)</a>.

Überwachungsprotokolle werden in Ihrem Azure-Speicherkonto gespeichert. Sie können eine Aufbewahrungsdauer für ein Überwachungsprotokoll definieren.

Eine Überwachungsrichtlinie kann für eine spezifische Datenbank oder als Standardserverrichtlinie definiert werden. Eine Standardrichtlinie für die Serverüberwachung wird auf alle Datenbanken auf einem Server angewendet, für die keine Datenbanküberwachungsrichtlinie definiert wurde, die Vorrang hat.

Überprüfen Sie vor der Einrichtung der Überwachung, ob Sie einen [kompatiblen Client](sql-data-warehouse-auditing-downlevel-clients.md) verwenden.


##<a id="subheading-2"></a>Einrichten der Überwachung für Ihre Datenbank

1. Starten Sie das <a href="https://portal.azure.com" target="_blank">Azure-Vorschauportal</a>.

2. Navigieren Sie zum Blatt "Konfiguration" der SQL Data Warehouse-Datenbank/SQL Server-Instanz, die Sie überwachen möchten. Klicken Sie oben auf die Schaltfläche **Einstellungen**, und wählen Sie auf dem Blatt "Einstellungen" die Option **Überwachung**.

	![][1]

3. Deaktivieren Sie auf dem Blatt zur Überwachungskonfiguration zunächst das Kontrollkästchen **Überwachungseinstellungen von Server erben**. Dadurch können Sie die Einstellungen für eine bestimmte Datenbank angeben.
	
	![][2]

4. Aktivieren Sie anschließend die Überwachung, indem Sie auf die Schaltfläche **EIN** klicken.

	![][3]

5. Wählen Sie auf dem Blatt für die Überwachungskonfiguration **SPEICHERDETAILS** aus, um das Blatt "Überwachungsprotokolle" für den Speicher zu öffnen. Wählen Sie im Fenster zur Konfiguration der Überwachung das Azure-Speicherkonto aus, in dem die Protokolle gespeichert werden sollen, und geben Sie die Aufbewahrungsdauer an. **Tipp:** Verwenden Sie für alle überwachten Datenbanken dasselbe Speicherkonto, um die vorkonfigurierten Berichtvorlagen optimal einzusetzen.

	![][4]

6. Klicken Sie auf die Schaltfläche **OK**, um die Konfiguration für die Speicherdetails zu speichern.


7. Klicken Sie unterhalb von **PROTOKOLLIERUNG NACH EREIGNIS** auf **ERFOLG** und **FEHLER**, um alle Ereignisse zu protokollieren, oder wählen Sie einzelne Ereigniskategorien.


8. Wenn Sie die Überwachung für eine Datenbank konfigurieren, müssen Sie zum Ändern der Verbindungszeichenfolge des Clients sicherstellen, dass die Datenüberwachung ordnungsgemäß erfasst wird. Weitere Informationen zu Verbindungen mit Vorgängerversionsclients finden Sie unter [Ändern des vollqualifizierten Namens eines Servers in der Verbindungszeichenfolge](sql-data-warehouse-auditing-downlevel-clients.md).

9. Klicken Sie auf **OK**.


##<a id="subheading-3">Analysieren von Überwachungsprotokollen und -berichten</a>

Überwachungsprotokolle werden im Azure-Speicherkonto, das Sie während der Einrichtung ausgewählt haben, in einer einzelnen Azure-Speichertabelle mit dem Präfix **SQLDBAuditLogs** zusammengefasst. Sie können Protokolldateien mit einem Tool wie <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Storage-Explorer</a> anzeigen.

Eine vorkonfigurierte Dashboardberichtvorlage steht als <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">herunterladbares Excel-Arbeitsblatt</a> zur Verfügung, mit dem Sie Protokolldaten schnell analysieren können. Um die Vorlage in Ihren Überwachungsprotokollen verwenden zu können, benötigen Sie Excel 2013 oder höher und Power Query, das Sie <a href="http://www.microsoft.com/download/details.aspx?id=39379">hier</a> herunterladen können.

Die Vorlage enthält fiktionale Beispieldaten, und Sie können Power Query so einrichten, dass das Überwachungsprotokoll direkt aus Ihrem Azure-Speicherkonto importiert wird.

Ausführlichere Anweisungen zum Arbeiten mit der Berichtvorlage finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Vorgehensweise (Dokumentdownload)</a>.

![][5]


##<a id="subheading-4">Methoden zur Verwendung in der Produktion</a>
Die Beschreibung in diesem Abschnitt bezieht sich auf die obigen Screenshots. Sie können entweder das <a href="https://portal.azure.com" target="_blank">Azure-Vorschauportal</a> oder das <a href= "https://manage.windowsazure.com/" target="_bank">klassische Azure-Portal</a> verwenden.
 

##<a id="subheading-5"></a>Erneute Speicherschlüsselgenerierung

In der Produktion werden Sie wahrscheinlich Ihre Speicherschlüssel regelmäßig aktualisieren. Sie müssen beim Aktualisieren Ihrer Schlüssel die Richtlinie erneut speichern. Dieser Prozess verläuft wie folgt:


1. Wechseln Sie auf dem Blatt für die Überwachungskonfiguration (oben im Abschnitt zur Einrichtung der Überwachung beschrieben) die Einstellung **Speicherzugriffsschlüssel** von *Primär* in *Sekundär*, und klicken Sie auf **SPEICHERN**. ![][4]
2. Wechseln Sie zum Blatt für die Speicherkonfiguration, und **generieren Sie erneut** den *primären Zugriffsschlüssel*.

3. Wechseln Sie zurück zum Blatt für die Überwachungskonfiguration, ändern Sie den **Speicherzugriffsschlüssel** von *Sekundär* in *Primär*, und klicken Sie auf **SPEICHERN**.

4. Wechseln Sie zurück zur Speicherbenutzeroberfläche, und **generieren Sie erneut** den *sekundären Zugriffsschlüssel* (als Vorbereitung auf den nächsten Schlüsselaktualisierungszyklus).
  
##<a id="subheading-6"></a>Automatisierung
Es gibt mehrere PowerShell-Cmdlets, die Sie zum Konfigurieren der Überwachung in Azure SQL-Datenbank verwenden können. Für den Zugriff auf die Cmdlets zur Überwachung muss PowerShell im Azure-Ressourcen-Manager-Modus ausgeführt werden.

> [AZURE.NOTE]Das [Azure-Ressourcen-Manager](https://msdn.microsoft.com/library/dn654592.aspx)-Modul befindet sich derzeit in der Vorschau. Möglicherweise stellt es nicht die gleichen Verwaltungsfunktionen wie das Azure-Modul bereit.

Wenn Sie im Azure-Ressourcen-Manager-Modus arbeiten, führen Sie `Get-Command *AzureSql*` aus, um die verfügbaren Cmdlets aufzulisten.


<!--Anchors-->
[Grundlagen zur Datenbanküberwachung]: #subheading-1
[Einrichten der Überwachung für Ihre Datenbank]: #subheading-2
[Analysieren von Überwachungsprotokollen und -berichten]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->

<!---HONumber=Oct15_HO2-->