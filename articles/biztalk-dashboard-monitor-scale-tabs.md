<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Dashboard, Monitor, Scale, Configure and Hybrid Connections tabs" pageTitle="Dashboard, Monitor, and Scale in BizTalk Services | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale, wabs, mabs" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Konfigurieren" und "Hybridverbindungen"

Wenn Sie das Azure-Verwaltungsportal zum ersten Mal öffnen, ist automatisch die Registerkarte **ALLE ELEMENTE** geöffnet. Die Spalten auf der Registerkarte **ALLE ELEMENTE** können sortiert werden. Wenn Sie Ihren BizTalk Service anzeigen möchten, wählen Sie diesen auf der Registerkarte **ALLE ELEMENTE** aus. Alternativ können Sie auf der Registerkarte **BIZTALK SERVICES** den Namen des BizTalk Service auswählen.

Dadurch wird ein neues Fenster mit den folgenden Registerkarten geöffnet. In diesem Thema werden diese Registerkarten beschrieben.

-   ![Quick Start][QuickStart] [Schnellstart](#QuickStart)

-   [Dashboard](#Dashboard)

-   [Überwachen](#Monitor)

-   [Skalieren](#Scale)

-   [Konfigurieren](#Configure)

-   [Hybridverbindungen](#HybridConnections)

## <a name="QuickStart"></a>Schnellstart (![Schnellstart][Quick Start])

Eventuell stehen nicht alle aufgeführten Optionen für alle BizTalk Services Editionen zur Verfügung.
<table border="1">
    <tr>
        <td><strong>Tools herunterladen</strong></td>

        <td>Laden Sie das BizTalk Services SDK herunter, um die Visual Studio-Projektvorlagen auf Ihrem lokalen Entwicklungscomputer zu speichern. Mithilfe dieser Vorlagen werden die Visual Studio-Projekte <strong>BizTalk Services</strong> (Brücke) und strong>BizTalk Service Artifacts</strong> (Transformation) erstellt, die in Ihrem BizTalk Service bereitgestellt werden. 
        <br/><br/>
        Unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335"> How do I Start Using the Azure BizTalk Services SDK (Wie verwende ich das Azure BizTalk Servies SDK?, in englischer Sprache)</a> und <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Installing the Azure BizTalk Services SDK (Installieren des Azure BizTalk Services SDK, in englischer Sprache)</a> werden die ersten Schritte aufgelistet.
        </td>
    </tr>

    <tr>
        <td><strong>Partnervereinbarungen erstellen</strong></td>

        <td>Öffnet das auf Azure gehostete Azure BizTalk Services-Portal, auf dem Sie Partner hinzufügen und X12- sowie AS2 EDI-Verträge erstellen können.
        <br/><br/>
        Unter  <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal (Konfigurieren der Komponenten für EDI-Messaging im BizTalk Services-Portal)</a> werden die ersten Schritte aufgelistet.
        </td>
    </tr>

<tr>
        <td><strong>Erfahren Sie mehr über BizTalk Services</strong></td>
        <td>Im <a HREF="http://azure.microsoft.com/de-de/documentation/services/biztalk-services/">learning center</a> erfahren Sie mehr über Azure BizTalk Services.</td>
</tr>
</table>


In der Taskleiste am unteren Rand stehen folgende Optionen zur Verfügung:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Verwalten</strong> der Anwendungsbereitstellung</td>
<td align="left">Hiermit öffnen Sie das Azure BizTalk Services-Portal. Das BizTalk Services-Portal ist das Tor zur EDI-Konfiguration. Hier können Sie Partner hinzufügen und X12-, AS2- sowie EDIFACT-Vereinbarungen erstellen.<br /><br /> Dies ist funktionsgleich mit <strong>Partnervereinbarungen erstellen</strong> auf der Registerkarte <strong>Schnellstart</strong>.<br /><br /> Unter <a href="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal (Konfigurieren von Komponenten für EDI-Nachrichten im BizTalk Services-Portal, in englischer Sprache</a> erhalten Sie weitere Informationen zum BizTalk Services-Portal.</td>
</tr>
<tr class="even">
<td align="left"><strong>Verbindungsinformationen</strong> des Access Control-Namespace</td>
<td align="left">Wenn Sie &quot;Verbindungsinformationen&quot; auswählen, werden der Access Control-Namespace, der Standardaussteller und der Standardschlüssel angezeigt. Diese Werte können kopiert werden.<br /><br /> Sie können auch das Access Control-Verwaltungsportal öffnen. Dieses Verwaltungsportal entspricht der Option <strong>Active Directory</strong> im linken Navigationsbereich.<br /><br /> Unter <a href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Verwalten Ihres ACS-Namespace</a> finden Sie weitere Informationen zum Access Control-Verwaltungsportal.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Synchronisierungsschlüssel</strong> im Speicherkonto</td>
<td align="left">Beim Erstellen eines Speicherkontos werden automatisch ein Primärschlüssel und ein Sekundärschlüssel erstellt. Diese Verschlüsselungsschlüssel steuern den Zugriff auf das Speicherkonto. Der BizTalk Service verwendet automatisch den Primärschlüssel. Mit <strong>Synchronisierungsschlüssel</strong> können Sie zwischen dem Primär- und dem Sekundärschlüssel umschalten, ohne den BizTalk Service zu unterbrechen.<br /><br /> Angenommen, Sie möchten, dass der BizTalk Service einen neuen Primärschlüssel für das Speicherkonto verwendet. Gehen Sie dazu folgendermaßen vor:<br /><br />
<ol>
<li>Wählen Sie Ihren BizTalk Service und dann <strong>Synchronisierungsschlüssel</strong> aus. Wählen Sie den Sekundärschlüssel aus. Wenn Sie dies tun, startet der BizTalk Service die Verwendung des Sekundärschlüssels.</li>
<li>Klicken Sie im Azure-Verwaltungsportal auf Ihr Speicherkonto, und generieren Sie den Primärschlüssel neu. Denken Sie daran, dass Ihr BizTalk Service den Sekundärschlüssel verwendet.</li>
<li>Wählen Sie Ihren BizTalk Service und dann <strong>Synchronisierungsschlüssel</strong> aus. Wählen Sie nun den Primärschlüssel. Dies ist der neue Primärschlüssel, den Sie generiert haben.</li>
<li>Wählen Sie im Azure-Verwaltungsportal Ihr Speicherkonto aus, und generieren Sie den Sekundärschlüssel neu.</li>
</ol>
<br /> Man spricht hier von &quot;Rollover-Schlüsseln&quot;. Der Zweck ist, zwischen dem Primär- und dem Sekundärschlüssel umzuschalten, ohne den BizTalk Service zu unterbrechen.</td>
</tr>
<tr class="even">
<td align="left"><strong>Löschen</strong> der Anwendung</td>
<td align="left">Wenn Sie &quot;Löschen&quot; auswählen, werden Ihr BizTalk Service und alle in ihm bereitgestellten Elemente entfernt.</td>
</tr>
</tbody>
</table>

## <a name="Dashboard"></a>Dashboard

Eventuell stehen nicht alle aufgeführten Optionen für alle BizTalk Services Editionen zur Verfügung.

Wenn Sie den Namen Ihres BizTalk Service auswählen, wird die Registerkarte "Dashboard" angezeigt. "Dashboard" enthält Folgendes:

##### Verwendungsübersicht: Zeigt die Anzahl der verwendeten Hybridverbindungen an.

Gibt außerdem über die Datennutzung in GB Aufschluss.

##### Metrikendiagramm: Enthält eine feste Liste von Leistungsmetriken.

Diese Metriken liefern Echtzeitwerte über den Zustand des BizTalk Service. Sie können auch die **relativen** oder **absoluten** Werte sowie ein **Zeitintervall** für die im Diagramm angezeigten Metriken angeben.

Eine Beschreibung dieser Metriken finden Sie in diesem Thema unter [Verfügbare Metriken](#Metrics).

##### Auf einen Blick: Führt die Eigenschaften Ihres BizTalk Service auf.

<table border="1">
<tr>
<td><strong>Anmeldeinformationen für die Nachverfolgungsdatenbank aktualisieren</strong></td>
<td>Hiermit können Sie den Benutzernamen und das Kennwort zum Anmelden bei der Nachverfolgungsdatenbank ändern.</td>
</tr>
<tr>
<td><strong>SSL-Zertifikat aktualisieren</strong></td>
<td>Hiermit können Sie Ihren BizTalk Service so anpassen, dass ein anderes SSL-Zertifikat verwendet wird. Wenn Sie <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">den BizTalk Service erstellen</a>, wird automatisch ein selbstsigniertes SSL-Zertifikat erstellt.</td>
</tr>
<tr>
<td><strong>Zertifikat herunterladen</strong></td>
<td>Sie können das von Ihrem BizTalk Service verwendete SSL-Zertifikat auf einen lokalen Rechner herunterladen.</td>
</tr>
<tr>
<td><strong>Status</strong></td>
<td>Zeigt den aktuellen Status Ihres BizTalk Service an. Siehe <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk Services: Dienststatusübersicht</a>.</td>
</tr>
<tr>
<td><strong>Dienst-URL</strong></td>
<td>Die URL für Ihren BizTalk Service. Dies ist dieselbe URL wie die <strong>Domänen-URL</strong>, die Sie bei der Erstellung Ihres BizTalk Service eingegeben haben.</td>
</tr>
<tr>
<td><strong>Öffentliche Virtual IP (VIP)-Adresse</strong></td>
<td>Die IP-Adresse wird Ihrem BizTalk Service zugewiesen. Sie wird für alle Eingabeendpunkte verwendet und ist die Quelladresse für ausgehenden Verkehr. Diese IP-Adresse gehört so lange zu Ihrem BizTalk Service, wie dieser bereitgestellt wird. Wenn Sie den BizTalk Service löschen, wird die IP-Adresse einem anderen BizTalk Service zugewiesen.</td>
</tr>
<tr>
<td><strong>ACS-Namespace</strong></td>
<td>Zur Authentifizierung im BizTalk Service.</td>
</tr>
<tr>
<td><strong>Edition</strong></td>
<td>Führt die Edition auf, die bei der Erstellung des BizTalk Service eingegeben wurde.</td>
</tr>
<tr>
<td><strong>Verzeichnis</strong></td>
<td>Zeigt die geografische Region an, in der Ihr BizTalk Service gehostet wird.</td>
</tr>
<tr>
<td><strong>Erstellt</strong></td>
<td>Zeigt das Datum und den Zeitpunkt der Erstellung Ihres BizTalk Service an.</td>
</tr>
<tr>
<td><strong>Nachverfolgungsdatenbank</strong></td>
<td>Der Name der Azure SQL-Datenbank, in der die von Ihrem BizTalk Service verwendeten Nachverfolgungstabellen gespeichert sind.
<br></br>
<br></br>
Unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Erläuterung der Anforderungen den BizTalk Service erstellen</a> finden Sie Einzelheiten zur Nachverfolgungsdatenbank.</td>
</tr>
<tr>
<td><strong>Speicherkonto für die Überwachung/Archivierung</strong></td>
<td>Der Name des Azure-Speicherkontos, in dem die Überwachungsergebnisse Ihres BizTalk Service gespeichert sind.
<br></br>
<br></br>
Unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Erläuterung der Anforderungen den BizTalk Service erstellen</a> finden Sie Einzelheiten zum Speicherkonto.</td>
</tr>
<tr>
<td><strong>Abonnementname</strong></td>
<td>Führt das Abonnement auf, das Ihren BizTalk Service hostet. Das Abonnement regelt den Zugriff auf das Azure-Verwaltungsportal.</td>
</tr>
<tr>
<td><strong>Abonnement-ID</strong></td>
<td>Wenn ein Abonnement erstellt wird, wird automatisch eine Abonnement-ID erstellt. Wenn Sie REST-APIS verwenden, müssen Sie eventuell die Abonnement-ID eingeben.</td>
</tr>
</table>

[BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280) enthält die Schritte zur Erstellung eines BizTalk Service.

##### "Verwalten", "Verbindungsinformationen", "Synchronisierungsschlüssel" und "Löschen" in der Taskleiste:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Verwalten</strong> der Anwendungsbereitstellung</td>
<td align="left">Hiermit öffnen Sie das Azure BizTalk Services-Portal. Das BizTalk Services-Portal ist das Tor zur EDI-Konfiguration. Hier können Sie Partner hinzufügen und X12-, AS2- sowie EDIFACT-Vereinbarungen erstellen.<br /><br /> Dies ist funktionsgleich mit <strong>Partnervereinbarungen erstellen</strong> auf der Registerkarte <strong>Schnellstart</strong>.<br /><br /> Unter <a href="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal (Konfigurieren von Komponenten für EDI-Nachrichten im BizTalk Services-Portal, in englischer Sprache</a> erhalten Sie weitere Informationen zum BizTalk Services-Portal.</td>
</tr>
<tr class="even">
<td align="left"><strong>Verbindungsinformationen</strong> des Access Control-Namespace</td>
<td align="left">Hier finden Sie die Werte für den Access Control-Namespace, den Standardaussteller und den Standardschlüssel. Diese lassen sich kopieren.<br /><br /> Sie können auch das Access Control-Verwaltungsportal öffnen. Dieses Verwaltungsportal entspricht der Option &quot;Active Directory&quot; im linken Navigationsbereich.<br /><br /> Unter <a href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Verwalten Ihres ACS-Namespace</a> finden Sie weitere Informationen zum Access Control-Verwaltungsportal.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Synchronisierungsschlüssel</strong> im Speicherkonto</td>
<td align="left">Beim Erstellen eines Speicherkontos werden automatisch ein Primärschlüssel und ein Sekundärschlüssel erstellt. Diese Verschlüsselungsschlüssel steuern den Zugriff auf das Speicherkonto. Der BizTalk Service verwendet automatisch den Primärschlüssel. Mit <strong>Synchronisierungsschlüssel</strong> können Sie zwischen dem Primär- und dem Sekundärschlüssel umschalten, ohne den BizTalk Service zu unterbrechen.<br /><br /> Angenommen, Sie möchten, dass der BizTalk Service einen neuen Primärschlüssel für das Speicherkonto verwendet. Gehen Sie dazu folgendermaßen vor:<br /><br />
<ol>
<li>Wählen Sie Ihren BizTalk Service und dann <strong>Synchronisierungsschlüssel</strong> aus. Wählen Sie den Sekundärschlüssel aus. Wenn Sie dies tun, startet der BizTalk Service die Verwendung des Sekundärschlüssels.</li>
<li>Klicken Sie im Azure-Verwaltungsportal auf Ihr Speicherkonto, und generieren Sie den Primärschlüssel neu. Denken Sie daran, dass Ihr BizTalk Service den Sekundärschlüssel verwendet.</li>
<li>Wählen Sie Ihren BizTalk Service und dann <strong>Synchronisierungsschlüssel</strong> aus. Wählen Sie nun den Primärschlüssel. Dies ist der neue Primärschlüssel, den Sie generiert haben.</li>
<li>Wählen Sie im Azure-Verwaltungsportal Ihr Speicherkonto aus, und generieren Sie den Sekundärschlüssel neu.</li>
</ol>
<br /> Man spricht hier von &quot;Rollover-Schlüsseln&quot;. Der Zweck ist, zwischen dem Primär- und dem Sekundärschlüssel umzuschalten, ohne den BizTalk Service zu unterbrechen.</td>
</tr>
<tr class="even">
<td align="left"><strong>Löschen</strong> der Anwendung</td>
<td align="left">Ihr BizTalk Service und alle darin bereitgestellten Elemente werden entfernt.</td>
</tr>
</tbody>
</table>

## <a name="Monitor"></a>Überwachen

Gilt nicht für die Free Edition.

Wenn Sie den Namen Ihres BizTalk Service auswählen, steht die Registerkarte "Überwachen" mit folgendem Inhalt zur Verfügung:

##### Metrikendiagramm: Zeigt die ausgewählten Leistungsmetriken an.

Diese Metriken liefern Echtzeitwerte über den Zustand des BizTalk Service. Sie wählen aus, welche Leistungsmetriken angezeigt werden. Bis zu sechs Leistungsmetriken können gleichzeitig angezeigt werden.

Sie können **relative** oder **absolute** Werte sowie ein **Zeitintervall** für die angezeigten Metriken angeben.

##### So zeigen Sie Metriken im Diagramm an oder entfernen diese aus ihm

1.  Wählen Sie die Registerkarte **Überwachen** aus.
2.  Klicken Sie in der Taskleiste auf **Metriken hinzufügen**:
    !['Metriken hinzufügen' auswählen][AddMetrics]
3.  Prüfen Sie die Leistungsmetriken, die Sie anzeigen möchten.
4.  Wählen Sie das Häkchen aus, um zur Registerkarte **Überwachen** zurückzukehren.
5.  Wählen Sie den Kreis neben der Metrik aus, um deren Wert im Diagramm anzuzeigen.<br></br>
    Zum Beispiel ist die Metrik für die **CPU-Auslastung** ausgegraut. Ihre Ausgabe wird im Diagramm nicht dargestellt:<br></br>
    ![Ausgegraute Metrik für CPU-Auslastung][GrayedMetric]<br></br>
    Wählen Sie den ausgegrauten Kreis aus, um die Ausgabe der Metrik für die **CPU-Auslastung** im Diagramm zu aktivieren:<br></br>
    ![Aktivierte Metrik für die CPU-Auslastung][EnabledMetric]

6.  Wenn Sie eine Metrik aus dem Diagramm und der Liste entfernen möchten, wählen Sie in der Taskleiste **Metrik löschen** aus. Um die Metrik der Liste wieder hinzuzufügen, klicken Sie in der Taskleiste auf **Metriken hinzufügen**, markieren die Metrik und wählen das Häkchen aus, um zur Registerkarte **Überwachen** zurückzukehren. Wählen Sie den ausgegrauten Kreis aus, um die Metrik zu aktivieren.

## <a name="Metrics"></a>Verfügbare Metriken

Die folgenden Leistungsindikatoren/Metriken stehen zur Verfügung:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Paketumlaufzeit</strong></td>
<td align="left">Diese Leistungsmetrik zeigt die durchschnittliche Zeit in Millisekunden (ms) vom Eingang einer Nachricht bis zu ihrer vollständigen Verarbeitung durch den BizTalk Service zwischen allen Brücken an. Es werden nur erfolgreich verarbeitete Nachrichten gezählt.<br /><br /> Bei folgenden Ereignissen wird ein Zeitstempel erstellt:
<ul>
<li>Eintritt der Nachricht in das Gateway</li>
<li>Weiterleitung der Nachricht an das Ziel</li>
<li>Übermittlung der Zielbestätigung</li>
<li>Senden der Zielbestätigung an das Gateway</li>
</ul>
<br /> Diese Metrik zeigt das Ergebnis der folgenden Berechnung:<br /><br /> [Senden der Zielbestätigung an das Gateway] - [Eintritt der Nachricht in das Gateway]</td>
</tr>
<tr class="even">
<td align="left"><strong>Fehler an der Quelle</strong></td>
<td align="left">Zeigt die Gesamtzahl der Nachrichten des BizTalk Service an, die beim Abruf von den Quellendpunkten fehlgeschlagen sind.</td>
</tr>
<tr class="odd">
<td align="left"><strong>CPU-Auslastung</strong></td>
<td align="left">Listet die durchschnittliche Prozessorzeit aller Rolleninstanzen in % auf.</td>
</tr>
<tr class="even">
<td align="left"><strong>Verarbeitungszeit</strong></td>
<td align="left">Zeigt in Millisekunden (ms) die durchschnittliche Zeit für die Verarbeitung einer Nachricht durch den BizTalk Service zwischen allen Brücken an, ausgenommen die am Ziel verbrachte Zeit. Es werden nur erfolgreich verarbeitete Nachrichten gezählt.<br /><br /> Bei folgenden Ereignissen wird ein Zeitstempel erstellt:
<ul>
<li>Eintritt der Nachricht in das Gateway</li>
<li>Weiterleitung der Nachricht an das Ziel</li>
<li>Übermittlung der Zielbestätigung</li>
<li>Senden der Zielbestätigung an das Gateway</li>
</ul>
<br />Diese Metrik zeigt das Ergebnis der folgenden Berechnung:<br /><br /> [Senden der Zielbestätigung an das Gateway] - [Eintritt der Nachricht in das Gateway] - [Empfang der Zielbestätigung] + [Weiterleitung der Nachricht an das Ziel]</td>
</tr>
<tr class="odd">
<td align="left"><strong>Fehler im Verlauf</strong></td>
<td align="left">Zeigt die Gesamtzahl der Nachrichten an, die innerhalb eines bestimmten Zeitintervalls während der Verarbeitung durch den BizTalk Service zwischen allen Brücken fehlgeschlagen sind.</td>
</tr>
<tr class="even">
<td align="left"><strong>Gesendete Nachrichten</strong></td>
<td align="left">Zeigt die Gesamtzahl der Nachrichten an, die innerhalb eines bestimmten Zeitintervalls vom BizTalk Service zwischen allen Brücken gesendet worden sind. Diese Metrik wird erhöht, wenn eine aus einer Pipeline gesendete Nachricht das Routenziel erreicht. Diese Metrik zeigt nicht die erfolgreiche Verarbeitung einer Nachricht an.<br /><br /> In einem Abfrage-Antwort-Szenario wird die Metrik erhöht, wenn das Routenziel eine Empfangsbestätigung an die Pipeline zurücksendet.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Empfangene Nachrichten</strong></td>
<td align="left">Zeigt die Gesamtzahl der Nachrichten an, die innerhalb eines bestimmten Zeitintervalls vom BizTalk Service zwischen allen Brücken empfangen worden sind. Diese Metrik wird erhöht, wenn eine Nachricht aus der Pipeline empfangen wird.</td>
</tr>
<tr class="even">
<td align="left"><strong>Nachrichten in Verarbeitung</strong></td>
<td align="left">Zeigt die Gesamtzahl der Nachrichten an, die aktuell innerhalb eines bestimmten Zeitintervalls vom BizTalk Service verarbeitet werden.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Verarbeitete Nachrichten</strong></td>
<td align="left">Zeigt die Gesamtzahl der Nachrichten an, die innerhalb eines bestimmten Zeitintervalls vom BizTalk Service zwischen allen Brücken erfolgreich verarbeitet wurden. Diese Metrik wird erhöht, wenn eine Nachricht erfolgreich on der Pipeline empfangen und erfolgreich an das Ziel weitergeleitet wird.</td>
</tr>
</tbody>
</table>

## <a name="Scale"></a>Skalieren

Auf der Registerkarte "Skalieren" können Sie die Anzahl der von Ihrem BizTalk Service verwendeten Einheiten addieren oder subtrahieren. Standardmäßig ist eine Einheit konfiguriert. Zur Skalierung Ihres BizTalk Service können zusätzliche Einheiten hinzugefügt werden. Wenn Sie die Skalierung erhöhen, wird auch der Durchsatz erhöht. Auch die Menge der Ressourcen, also der bereitgestellten Brücken, Verträge und lokalen Branchenverbindungen, sowie die Verarbeitungsleistung werden erhöht. Beispiel: Sie erhöhen die Skalierung von 1 auf 2 Einheiten. In dieser Lage können Sie nun die doppelte Anzahl von Brücken bereitstellen sowie die Verträge, die lokalen Branchenverbindungen und die Verarbeitungsleistung verdoppeln.

Einige BizTalk-Editionen bieten keine Skalierungsoption. In diesen Editionen ist nur eine Einheit erlaubt. Informationen darüber, auf wie viele Einheiten Ihre Edition skaliert werden kann, finden Sie unter [BizTalk Services: Editionen-Diagramm](http://go.microsoft.com/fwlink/p/?LinkID=302279).

Eine Erhöhung der Anzahl der Einheiten kann sich auf den Preis auswirken. Wenn Sie die Anzahl der Einheiten erhöhen, erhalten Sie nach Auswahl von **Speichern** die Meldung, dass dies Einfluss auf Ihre Rechnung hat. Sie können dann wählen, ob Sie fortfahren möchten. Wenn Sie die Anzahl der Einheiten erhöhen, ändert sich der Status des BizTalk Service von "Active" auf "Updating". Im Status "Updating" läuft Ihr BizTalk Service weiter.

[BizTalk Services: Editionen-Diagramm](http://go.microsoft.com/fwlink/p/?LinkID=302279) enthält die Definition von "Einheit".

## <a name="Configure"></a>Konfigurieren

Gilt nicht für Hybridverbindungen.

Stellt den Sicherungsstatus auf "Keine" oder "Automatisch" ein. Bei Einstellung auf "Keine" werden keine Sicherungen automatisch erstellt. Bei Einstellung auf "Automatisch" konfigurieren Sie den Speicherort für die Sicherung, die Häufigkeit der Sicherung und die Aufbewahrungszeit für die Sicherungsdateien.

[BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873) enthält Einzelheiten dazu.

## <a name="HybridConnections"></a>Hybridverbindungen

Über Hybridverbindungen werden Azure-Anwendungen wie Websites oder Mobile Services mit einer lokalen Ressource (wie SQL Server, MySQL, HTTP Web-APIs und die meisten benutzerdefinierten Webdienste) verbunden, die einen statischen TCP-Port verwendet. Hybridverbindungen werden in BizTalk Services über das Azure-Verwaltungsportal verwaltet.

Informationen zum Erstellen von Hybridverbindungen in Azure Websites finden Sie unter[Hybrid Connection: Connect an Azure Web Site to an On-Premises Resource](http://go.microsoft.com/fwlink/p/?LinkId=397538) (Hybridverbindungen: Verbinden einer Azure-Website mit einer lokalen Ressource, in englischer Sprache).

Informationen zur Verwendung von Hybridverbindungen in Azure Mobile Services finden Sie unter [Azure Mobile Services and Hybrid Connections](http://azure.microsoft.com/de-de/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started) (Azure Mobile Services und Hybridverbindungen, in englischer Sprache).

Informationen zum Erstellen oder Verwalten von Hybridverbindungen in Azure BizTalk Services finden Sie unter [Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274).

## Weiter

Nun, da Sie mit den verschiedenen Registerkarten vertraut sind, können Sie mehr über die Funktionen von Azure BizTalk Services erfahren:

-   [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873)

## Weitere Informationen

-   [Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274)
-   [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [BizTalk Services: BizTalk-Dienststatusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Wie verwende ich das Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

  [Quick Start]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
  [QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
  [AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
  [GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
  [EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png


  [Verfügbare Metriken]: #Metrics
  ['Metriken hinzufügen' auswählen]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
  [Ausgegraute Metrik für CPU-Auslastung]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
  [Aktivierte Metrik für die CPU-Auslastung]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
  [BizTalk Services: Editionen-Diagramm]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [BizTalk Services: Sichern und Wiederherstellen]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Hybrid Connection: Connect an Azure Web Site to an On-Premises Resource]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Azure Mobile Services and Hybrid Connections]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [BizTalk Services: Drosselung]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk Services: Name und Schlüssel des Ausstellers]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Wie verwende ich das Azure BizTalk Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=302335
