<properties 
	pageTitle="Erste Schritte bei der Überwachung von SQL-Datenbank | Azure" 
	description="Erste Schritte bei der Überwachung von SQL-Datenbank" 
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
	ms.date="06/01/2015" 
	ms.author="jeffreyg"/>
 
# Erste Schritte bei der Überwachung von SQL- Datenbank 
<p> Bei der Azure SQL-Datenbank-Überwachung werden Datenbankereignisse erfasst und die überwachten Ereignisse in einem Überwachungsprotokoll in Ihrem Azure Storage-Konto protokolliert. Die Überwachung ist in den Tarifen Basic, Standard und Premium allgemein verfügbar.

Die Überwachung kann Ihnen dabei helfen, die gesetzlichen Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftsspezifische Bedenken oder mutmaßliche Sicherheitsverstöße hinweisen können.

Überwachungstools ermöglichen und erleichtern die Einhaltung von Normen, garantieren diese jedoch nicht. Weitere Informationen über Azure-Programme, die die Einhaltung von Normen unterstützen, finden Sie im <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure Trust Center</a>.

+ [Grundlagen zur Überwachung von Azure SQL-Datenbank] 
+ [Einrichten der Überwachung für Ihre Datenbank]
+ [Analysieren von Überwachungsprotokollen und -berichten]

##<a id="subheading-1"></a>Grundlagen zur Überwachung von Azure SQL-Datenbank

In den folgenden Abschnitten wird die Konfiguration der Überwachung mit dem Azure-Vorschauportal beschrieben. Alternativ können Sie das klassische Portal verwenden, siehe [Einrichten der Überwachung für Ihre Datenbank über das klassische Azure-Portal].

Die Überwachung von SQL-Datenbank bietet folgende Möglichkeiten:

- **Beibehalten** eines Überwachungspfads von ausgewählten Ereignissen. Sie können Kategorien für zu protokollierende Datenbankaktionen und -ereignisse konfigurieren.
- **Melden** von Datenbankaktivitäten. Sie können vorkonfigurierte Berichte und ein Dashboard verwenden, um schnell mit der Berichterstattung über Aktivitäten und Ereignisse zu beginnen.
- **Analysieren** von Berichten. Sie können nach verdächtigen Ereignissen, ungewöhnliche Aktivitäten und Trends suchen.

Sie können eine Überwachung für die folgenden Ereigniskategorien einrichten:

**Einfaches SQL** und **Parametrisiertes SQL**, wobei die erfassten Überwachungsprotokolle folgendermaßen klassifiziert werden:

- **Datenzugriffe**
- **Schemaänderungen (DDL)**
- **Datenänderungen (DML)**
- **Konten, Rollen und Berechtigungen (DCL)**

**Gespeicherte Prozeduren**, **Anmeldung** und **Transaktionsverwaltung**.

Für jede Ereigniskategorie wird die Überwachung auf **Erfolg** und **Fehler** getrennt voneinander konfiguriert.

Weitere Einzelheiten zu den überwachten Aktivitäten und Ereignissen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referenz zu Überwachungsprotokollformaten (DOC-Datei zum Herunterladen)</a>.

Überwachungsprotokolle werden in Ihrem Azure-Speicherkonto gespeichert. Sie können eine Aufbewahrungsdauer für ein Überwachungsprotokoll definieren.

Eine Überwachungsrichtlinie kann für eine spezifische Datenbank oder als Standardserverrichtlinie definiert werden. Eine Standardrichtlinie für die Serverüberwachung wird auf alle Datenbanken auf einem Server angewendet, für die keine Datenbanküberwachungsrichtlinie definiert wurde, die Vorrang hat.

Überprüfen Sie vor der Einrichtung der Überwachung, ob Sie einen [kompatiblen Client](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) verwenden.


##<a id="subheading-2"></a>Einrichten der Überwachung für Ihre Datenbank

1. Starten Sie das <a href="https://portal.azure.com" target="_blank">Azure-Vorschauportal</a> unter https://portal.azure.com. Alternativ können Sie auch das <a href= "https://manage.windowsazure.com/" target="_bank">klassische Azure-Portal</a> unter https://manage.windowsazure.com/ starten. Details finden Sie weiter unten.

2. Navigieren Sie zum Blatt "Konfiguration" der SQL-Datenbank / der SQL Server-Instanz, die Sie überwachen möchten. Klicken Sie oben auf die Schaltfläche **Einstellungen**, und wählen Sie auf dem Blatt "Einstellungen" die Option **Überwachung**.

	![][1]

3. Wählen Sie auf dem Blatt für die Überwachungskonfiguration SPEICHERDETAILS, um das Blatt "Überwachungsprotokolle" für den Speicher zu öffnen. Wählen Sie im Fenster zur Konfiguration der Überwachung das Azure-Speicherkonto aus, in dem die Protokolle gespeichert werden sollen, und geben Sie die Aufbewahrungsdauer an. **Tipp:** Verwenden Sie für alle überwachten Datenbanken dasselbe Speicherkonto, um die vorkonfigurierten Berichtvorlagen optimal einzusetzen.

	![][2]

4. Klicken Sie unterhalb von **PROTOKOLLIERUNG NACH EREIGNIS** auf **ERFOLG** und **FEHLER**, um alle Ereignisse zu protokollieren, oder wählen Sie einzelne Ereigniskategorien.


5. Wenn Sie die Überwachung für eine SQL-Datenbank konfigurieren, klicken Sie auf **Damit die Überwachung durchgesetzt wird, klicken Sie hier...**, und wählen Sie für **SICHERHEITSAKTIVIERTER ZUGRIFF** die Einstellung **ERFORDERLICH**. Wenn Sie die Überwachung für eine SQL Server-Instanz konfigurieren, haben Sie zwei Optionen: (a) Navigieren Sie nach Schritt 6 zu jeder SQL-Datenbank auf dem Server, und wenden Sie diesen Schritt an, oder (2) [ändern Sie den Server-FQDN in der Verbindungszeichenfolge](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


	![][5]

6. Klicken Sie auf **OK**.



##<a id="subheading-3">Analysieren von Überwachungsprotokollen und -berichten</a>

Überwachungsprotokolle werden im Azure-Speicherkonto, das Sie während der Einrichtung ausgewählt haben, in einer einzelnen Azure-Speichertabelle mit dem Präfix **SQLDBAuditLogs** zusammengefasst. Sie können Protokolldateien mit einem Tool wie <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Storage-Explorer</a> anzeigen.

Eine vorkonfigurierte Dashboardberichtvorlage steht als <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">herunterladbares Excel-Arbeitsblatt</a> zur Verfügung, mit dem Sie Protokolldaten schnell analysieren können. Um die Vorlage in Ihren Überwachungsprotokollen verwenden zu können, benötigen Sie Excel 2013 oder höher und Power Query, das Sie <a href="http://www.microsoft.com/download/details.aspx?id=39379">hier</a> herunterladen können.

Die Vorlage enthält fiktionale Beispieldaten, und Sie können Power Query so einrichten, dass das Überwachungsprotokoll direkt aus Ihrem Azure-Speicherkonto importiert wird.

Ausführlichere Anweisungen zum Arbeiten mit der Berichtvorlage finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Vorgehensweise (Dokumentdownload)</a>.

![][6]


##<a id="subheading-4"></a>Einrichten der Überwachung für Ihre Datenbank über das klassische Azure-Portal

1. Starten Sie das <a href= "https://manage.windowsazure.com/" target="_bank">klassische Azure-Portal</a> unter https://manage.windowsazure.com/.
 
2.   Klicken Sie auf die SQL-Datenbank / SQL Server-Instanz, die Sie überwachen möchten, und klicken Sie dann auf die Registerkarte **ÜBERWACHUNG & SICHERHEIT**.

3.   Wenn Sie die Überwachung für eine SQL-Datenbank konfigurieren, wählen Sie für **SICHERHEITSAKTIVIERTER ZUGRIFF** die Einstellung **ERFORDERLICH**. Wenn Sie die Überwachung für eine SQL Server-Instanz konfigurieren, haben Sie zwei Optionen: (a) Navigieren Sie nach Schritt 7 zu jeder SQL-Datenbank auf dem Server, und wenden Sie diesen Schritt an, oder (2) [ändern Sie den Server-FQDN in der Verbindungszeichenfolge](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).

4. Klicken Sie im Überwachungsabschnitt auf **AKTIVIERT**.


	![][7]

5. Bearbeiten Sie **PROTOKOLLIERUNG NACH EREIGNIS** nach Ihren Anforderungen.

	![][8]

6. Wählen Sie ein **SPEICHERKONTO**, und konfigurieren Sie die **AUFBEWAHRUNG**.

	![][11]

7. Klicken Sie auf **SPEICHERN**.




##<a id="subheading-3">Methoden zur Verwendung in der Produktion</a>
Die Beschreibung in diesem Abschnitt bezieht sich auf die obigen Screenshots. Sie können entweder das <a href="https://portal.azure.com" target="_blank">Azure-Vorschauportal</a> oder das <a href= "https://manage.windowsazure.com/" target="_bank">klassische Azure-Portal</a> verwenden.
 

##<a id="subheading-4"></a>Erneute Speicherschlüsselgenerierung

In der Produktion werden Sie wahrscheinlich Ihre Speicherschlüssel regelmäßig aktualisieren. Sie müssen beim Aktualisieren Ihrer Schlüssel die Richtlinie erneut speichern. Dieser Prozess verläuft wie folgt:


1. Wechseln Sie auf dem Blatt für die Überwachungskonfiguration (oben im Abschnitt zur Einrichtung der Überwachung beschrieben) die Einstellung **Speicherzugriffsschlüssel** von *Primär* in *Sekundär*, und klicken Sie auf **SPEICHERN**. ![][10]
2. Wechseln Sie zum Blatt für die Speicherkonfiguration, und **generieren Sie erneut** den *primären Zugriffsschlüssel*.

3. Wechseln Sie zurück zum Blatt für die Überwachungskonfiguration, ändern Sie den **Speicherzugriffsschlüssel** von *Sekundär* in *Primär*, und klicken Sie auf **SPEICHERN**.

4. Wechseln Sie zurück zur Speicherbenutzeroberfläche, und **generieren Sie erneut** den *sekundären Zugriffsschlüssel* (als Vorbereitung auf den nächsten Schlüsselaktualisierungszyklus).
  
##<a id="subheading-4"></a>Automatisierung
Es gibt mehrere PowerShell-Cmdlets, die Sie zum Konfigurieren der Überwachung in Azure SQL-Datenbank verwenden können. Für den Zugriff auf die Cmdlets zur Überwachung muss PowerShell im Azure-Ressourcen-Manager-Modus ausgeführt werden.

> [AZURE.NOTE]Das [Azure-Ressourcen-Manager](https://msdn.microsoft.com/library/dn654592.aspx)-Modul befindet sich derzeit in der Vorschau. Möglicherweise stellt es nicht die gleichen Verwaltungsfunktionen wie das Azure-Modul bereit.

Wenn Sie im Azure-Ressourcen-Manager-Modus arbeiten, führen Sie `Get-Command *AzureSql*` aus, um die verfügbaren Cmdlets aufzulisten.







<!--Anchors-->
[Grundlagen zur Überwachung von Azure SQL-Datenbank]: #subheading-1
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
[11]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure-storage.png






<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

 

<!---HONumber=July15_HO3-->