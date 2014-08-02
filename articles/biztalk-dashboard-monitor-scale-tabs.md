<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Dashboard, Monitor and Scale tabs" pageTitle="Dashboard, Monitor, and Scale in Biztalk Services | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"
==========================================================================

Wenn Sie das Azure-Verwaltungsportal zum ersten Mal öffnen, ist automatisch die Registerkarte **ALL ITEMS** geöffnet. Die Spalten auf der Registerkarte **ALL ITEMS** können sortiert werden. Wenn Sie Ihren BizTalk Service anzeigen möchten, wählen Sie diesen auf der Registerkarte **ALL ITEMS** aus, oder klicken Sie auf die Registerkarte **BIZTALK SERVICES**, und klicken Sie anschließend auf den Namen des BizTalk Service.

Dadurch wird ein neues Fenster mit den folgenden Optionen geöffnet:

-   [Schnellstart](#QuickStart)

-   [Dashboard](#Dashboard)

-   [Überwachen](#Monitor)

-   [Skalieren](#Scale)

In diesem Thema werden diese Registerkarten beschrieben.

Schnellstart
------------

Auf der Registerkarte "Schnellstart" haben Sie folgende Möglichkeiten:

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Option</strong></td>
        <td><strong>Beschreibung</strong></td>
</tr>
    <tr>
        <td>Tools herunterladen</td>

        <td>Laden Sie das BizTalk Services SDK herunter, um die Visual Studio-Projektvorlagen auf Ihrem lokalen Entwicklungscomputer zu speichern. Mithilfe dieser Vorlagen werden die Visual Studio-Projekte <strong>BizTalk Services</strong> (Brücke) und <strong>BizTalk Service Artifacts</strong> (Transformation) erstellt, die in Ihrem BizTalk Service bereitgestellt werden.

        <br/><br/>
		Unter<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335"> How do I Start Using the Azure BizTalk Services SDK (Wie verwende ich das Azure BizTalk Servies SDK?, in englischer Sprache) </a> und <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Installing the Azure BizTalk Services SDK (Installieren des Azure BizTalk Services SDK, in englischer Sprache)</a> werden die ersten Schritte aufgelistet. 
        </td>
    </tr>

    <tr>
        <td>Erstellen von Partnerverträgen</td>

        <td>Öffnet das auf Azure gehostete Azure BizTalk Services-Portal, auf dem Sie Partner hinzufügen und X12- sowie AS2 EDI-Verträge erstellen können. 

        <br/><br/>

        Unter<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal (Konfigurieren der Komponenten für EDI-Messaging im BizTalk Services-Portal) </a> werden die ersten Schritte aufgelistet.
        </td>
    </tr>

<tr>
        <td>Erfahren Sie mehr über BizTalk Services</td>

        <td>Gehen Sie in das Learning Center, um mehr über Azure BizTalk Services zu erfahren.</td>
</tr>

</table>




Auf der Symbolleiste unten auf der Seite können Sie den BizTalk Service **verwalten**, den Access Control-Namespace, die **Verbindungsinformationen** und die **Synchronisierungsschlüssel** des Speicherkontos anzeigen oder den BizTalk Service **löschen**:


<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Option</strong></td>
        <td><strong>Beschreibung</strong></td>
</tr>
<tr>
<td>Verwalten</td>
<td>Klicken Sie auf "Verwalten", um das Azure BizTalk Services-Portal zu öffnen. Das BizTalk Services-Portal ist der Eingang zur EDI-Konfiguration. Hier können Sie Partner hinzufügen und AS2- sowie X12-Verträge erstellen. 
<br/><br/>
Dies ist dieselbe Funktion wie <strong>Create partner agreements (Partnerverträge erstellen)</strong> auf der Registerkarte <strong>Schnellstart</strong>.
<br/><br/>
Unter<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal (Konfigurieren von Komponenten für EDI-Nachrichten im BizTalk Services-Portal, in englischer Sprache</a> erhalten Sie weitere Informationen zum BizTalk Services-Portal.</td>
</tr>

<tr>
<td>Verbindungsinformationen</td>
<td>Wenn Sie auf "Verbindungsinformationen" klicken, werden der Access Control-Namespace, der Standardaussteller und der Standardschlüssel angezeigt. Diese Werte können kopiert werden.
<br/><br/>
Sie können auch das Access Control-Verwaltungsportal öffnen. Dieses Verwaltungsportal ist dasselbe Tool wie die Option "Active Directory" im linken Navigationsbereich.
<br/><br/>
Unter<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Verwalten Ihres ACS-Namespace</a> finden Sie weitere Informationen zum Access Control-Verwaltungsportal.</td>
</tr>

<tr>
<td>Synchronisierungsschlüssel</td>
<td> 	Beim Erstellen eines Speicherkontos werden automatisch ein Primärschlüssel und ein Sekundärschlüssel erstellt. Diese Schlüssel steuern den Zugriff auf das Speicherkonto. Der BizTalk Service verwendet automatisch den Primärschlüssel. Mit <strong>Synchronisierungsschlüssel</strong> können Sie zwischen dem Primär- und dem Sekundärschlüssel umschalten, ohne den BizTalk Service zu unterbrechen. 
<br/><br/>
Wenn Sie z. B. möchten, dass der BizTalk Service einen neuen Primärschlüssel für das Speicherkonto verwendet, gehen Sie folgendermaßen vor:
<br/><br/>
<ol>
<li>Klicken Sie auf Ihren BizTalk Service und auf <strong>Synchronisierungsschlüssel</strong>. Wählen Sie den Sekundärschlüssel aus. Wenn Sie dies tun, startet der BizTalk Service die Verwendung des Sekundärschlüssels.</li>
<li>Klicken Sie im Azure-Verwaltungsportal auf Ihr Speicherkonto, und generieren Sie den Primärschlüssel neu. Denken Sie daran, dass Ihr BizTalk Service den Sekundärschlüssel verwendet.</li>
<li>Klicken Sie auf Ihren BizTalk Service und auf <strong>Synchronisierungsschlüssel</strong>. Wählen Sie nun den Primärschlüssel. Dies ist der neue Primärschlüssel, den Sie generiert haben.</li>
<li>Klicken Sie im Azure-Verwaltungsportal auf Ihr Speicherkonto, und generieren Sie den Sekundärschlüssel neu..</li>
</ol>
<br/>
Dieser Vorgang wird "Schlüssel-Rollover" genannt. Der Zweck ist, zwischen dem Primär- und dem Sekundärschlüssel umzuschalten, ohne den BizTalk Service zu unterbrechen.</td>
</tr>

<tr>
<td>Löschen</td>
<td>Wenn Sie auf "Löschen" klicken, werden Ihr BizTalk Service und alle in ihm bereitgestellten Elemente entfernt.</td>
</tr>
</table>


Dashboard
---------

Wenn Sie auf den Namen Ihres BizTalk Service klicken, wird die Registerkarte "Dashboard" angezeigt. Auf dem Dashboard werden die folgenden Informationen angezeigt:

#### Metrikendiagramm

Ein Diagramm, das eine nicht bearbeitbare Liste von Leistungsmetriken anzeigt. Diese Metriken liefern Echtzeit-Werte über den Zustand Ihres BizTalk Service. Zu diesen Metriken gehören:

-   CPU-Auslastung
-   Fehler an der Quelle
-   Fehler im Verlauf
-   Verarbeitete Nachrichten
-   Empfangene Nachrichten
-   Verarbeitungszeit

Eine Beschreibung dieser Metriken finden Sie in diesem Thema unter [Verfügbare Metriken](#Metrics).

##### Relativ oder Absolut

Das Diagramm zeigt Trends und den aktuellen Wert jeder Metrik an. Dies ist die Option **Relativ**. Wählen Sie **Absolut** aus, um eine Y-Achse einzublenden, so dass absolute Werte angezeigt werden.

##### Intervall

Zur Änderung des Zeitbereichs, innerhalb dessen die Metriken im Diagramm angezeigt werden. Folgende Optionen sind verfügbar:

-   1 Stunde
-   1 Tag
-   7 Tage

#### Auf einen Blick

Listet die Eigenschaften Ihres BizTalk Service auf. Dazu gehören:

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Option</strong></td>
        <td><strong>Beschreibung</strong></td>
</tr>
<tr>
<td>Anmeldeinformationen zur Aktualisierung der Nachverfolgungsdatenbank</td>
<td>Zum Ändern des Benutzernamens und des Kennworts zum Anmelden an der Nachverfolgungsdatenbank.<br/><br/>
Wenn Sie den BizTalk Service bereitstellen, geben Sie einen Benutzernamen und ein Kennwort für die Anmeldung in der Nachverfolgungsdatenbank ein. Mit dieser Option können Sie Ihren BizTalk Service so modifizieren, dass ein anderer Benutzername und ein anderes Kennwort erforderlich sind, um sich in der Nachverfolgungsdatenbank anzumelden.<br/><br/>
Unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal </a> finden Sie die Schritte zur Bereitstellung eines BizTalk Service.</td>
</tr>
<tr>
<td>Aktualisieren des SSL-Zertifikats</td>
<td>Sie können ein anderes SSL-Zertifikat eingeben.<br/><br/>
Beim Bereitstellen des BizTalk Service wird automatisch ein selbstsigniertes SSL-Zertifikat erstellt. Mit dieser Option können Sie Ihren BizTalk Service so modifizieren, dass ein anderes SSL-Zertifikat verwendet wird.<br/><br/>
Unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal</a> finden Sie die Schritte zur Bereitstellung eines BizTalk Service.</td>
</tr>
<tr>
<td>Zertifikat herunterladen</td>
<td>Mit dieser Option können Sie das SSL-Zertifikat herunterladen, das von Ihrem BizTalk Service verwendet wird.<br/><br/>
Unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal</a> finden Sie die Schritte zur Bereitstellung eines BizTalk Service.</td>
</tr>
<tr>
<td>Status</td>
<td>Zeigt den aktuellen Status Ihres BizTalk Service an.</td>
</tr>
<tr>
<td>Dienst-URL</td>
<td>Diese URL führt zu Ihrem BizTalk Service. Dies ist dieselbe URL wie die <strong>Domänen-URL</strong>, die Sie bei der Bereitstellung Ihres BizTalk Service eingegeben haben. </td>
</tr>
<tr>
<td>Öffentliche Virtual IP (VIP)-Adresse</td>
<td>Diese IP-Adresse wird Ihrem BizTalk Service zugewiesen. Sie wird für alle Eingabeendpunkte verwendet und ist die Quelladresse für ausgehenden Verkehr. Diese IP-Adresse gehört so lange zu Ihrem BizTalk Service, wie dieser bereitgestellt wird. Wenn Sie den BizTalk Service löschen, wird die IP-Adresse einer anderen Dienstbereitstellung zugeordnet.</td>
</tr>
<tr>
<td>ACS-Namespace</td>
<td>Zur Authentifikation im BizTalk Service. Dies ist der <strong>ACS-Namespace</strong>, den Sie bei der Bereitstellung Ihres BizTalk Service eingegeben haben.</td>
</tr>
<tr>
<td>Edition</td>
<td>Nennt die Edition. Dies kann Developer, Basic, Standard oder Premium sein. Dies ist derselbe ACS-Edition, die Sie bei der Bereitstellung Ihres BizTalk Service eingegeben haben. <br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302281">BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition</a> bietet einen Überblick über die Unterschiede zwischen den einzelnen Editionen einschließlich der Kosten.</td>
</tr>
<tr>
<td>Standort</td>
<td>Zeigt die geografische Region an, in der Ihr BizTalk Service gehostet wird. Dies ist die <strong>Region</strong>, die Sie bei der Bereitstellung Ihres BizTalk Service eingegeben haben.</td>
</tr>
<tr>
<td>Erstellt</td>
<td>Zeigt das Datum und den Zeitpunkt der Bereitstellung Ihres BizTalk Service an.</td>
</tr>
<tr>
<td>Nachverfolgungsdatenbank</td>
<td>Der Name der Azure SQL-Datenbank, in der die von Ihrem BizTalk Service verwendeten Nachverfolgungstabellen gespeichert sind. Dies ist die <strong>Nachverfolgungsdatenbank</strong>, die Sie bei der Bereitstellung Ihres BizTalk Service eingegeben haben.</td>
</tr>
<tr>
<td>Speicherkonto für die Überwachung/Archivierung</td>
<td>Der Name des Azure-Speicherkontos, in dem die Überwachungsergebnisse Ihres BizTalk Service gespeichert sind. Dies ist das <strong>Speicherkonto für die Überwachung/Archivierung</strong>, das Sie bei der Bereitstellung Ihres BizTalk Service eingegeben haben.</td>
</tr>
<tr>
<td>Abonnementname</td>
<td>Das Abonnement regelt den Zugriff auf das Azure-Verwaltungsportal. Dies ist der <strong>Abonnement</strong>-Name, den Sie bei der Bereitstellung Ihres BizTalk Service eingegeben haben.</td>
</tr>
<tr>
<td>Abonnement-ID</td>
<td>Das Abonnement regelt den Zugriff auf das Azure-Verwaltungsportal. Wenn ein Abonnement erstellt wird, wird automatisch eine Abonnement-ID erstellt. Wenn Sie REST-APIS verwenden, müssen Sie eventuell die Abonnement-ID eingeben.</td>
</tr>
</table>


Unter [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280) finden Sie die Schritte zur Bereitstellung eines BizTalk Service.

#### Verwalten, Verbindungsinformationen, Synchronisierungsschlüssel und Löschen

Auf der Symbolleiste unten auf der Seite können Sie den BizTalk Service **verwalten**, den Access Control-Namespace, die **Verbindungsinformationen** und die **Synchronisierungsschlüssel** des Speicherkontos anzeigen oder den BizTalk Service **löschen**:

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Option</strong></td>
        <td><strong>Beschreibung</strong></td>
</tr>
<tr>
<td>Verwalten</td>
<td>Klicken Sie auf "Verwalten", um das Azure BizTalk Services-Portal zu öffnen. Das BizTalk Services-Portal ist der Eingang zur EDI-Konfiguration. Hier können Sie Partner hinzufügen und AS2- sowie X12-Verträge erstellen.
<br/><br/>
Dies ist dieselbe Funktion wie <strong>Create partner agreements (Partnerverträge erstellen)</strong> auf der Registerkarte <strong>Schnellstart</strong>. 
<br/><br/>
Unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal (Konfigurieren von Komponenten für EDI-Nachrichten im BizTalk Services-Portal, in englischer Sprache</a> perhalten Sie weitere Informationen zum BizTalk Services-Portal.</td>
</tr>
<tr>
<td>Verbindungsinformationen</td>
<td> 	Wenn Sie auf "Verbindungsinformationen" klicken, werden der Access Control-Namespace, der Standardaussteller und der Standardschlüssel angezeigt. Diese Werte können kopiert werden.
<br/><br/>
Sie können auch das Access Control-Verwaltungsportal öffnen. Dieses Verwaltungsportal ist dasselbe Tool wie die Option "Active Directory" im linken Navigationsbereich.
<br/><br/>
Unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Verwalten Ihres ACS-Namespace</a> finden Sie weitere Informationen zum Access Control-Verwaltungsportal.</td>
</tr>
<tr>
<td>Synchronisierungsschlüssel</td>
<td>Beim Erstellen eines Speicherkontos werden automatisch ein Primärschlüssel und ein Sekundärschlüssel erstellt. Diese Schlüssel steuern den Zugriff auf das Speicherkonto. Der BizTalk Service verwendet automatisch den Primärschlüssel. Mit <strong>Synchronisierungsschlüssel</strong> können Sie zwischen dem Primär- und dem Sekundärschlüssel umschalten, ohne den BizTalk Service zu unterbrechen.
<br/><br/>
Wenn Sie z. B. möchten, dass der BizTalk Service einen neuen Primärschlüssel für das Speicherkonto verwendet, gehen Sie folgendermaßen vor:
<br/><br/>
<ol>
<li>Klicken Sie auf Ihren BizTalk Service und auf <strong>Synchronisierungsschlüssel</strong>. Wählen Sie den Sekundärschlüssel aus. Wenn Sie dies tun, startet der BizTalk Service die Verwendung des Sekundärschlüssels.</li>
<li>Klicken Sie im Azure-Verwaltungsportal auf Ihr Speicherkonto, und generieren Sie den Primärschlüssel neu. Denken Sie daran, dass Ihr BizTalk Service den Sekundärschlüssel verwendet.</li>
<li>Klicken Sie auf Ihren BizTalk Service und auf <strong>Synchronisierungsschlüssel</strong>. Wählen Sie nun den Primärschlüssel. Dies ist der neue Primärschlüssel, den Sie generiert haben.</li>
<li>Klicken Sie im Azure-Verwaltungsportal auf Ihr Speicherkonto, und generieren Sie den Sekundärschlüssel neu.</li>
</ol>
<br/>
Dieser Vorgang wird "Schlüssel-Rollover" genannt. Der Zweck ist, zwischen dem Primär- und dem Sekundärschlüssel umzuschalten, ohne den BizTalk Service zu unterbrechen.</td>
</tr>

<tr>
<td>Löschen</td>
<td>Wenn Sie auf "Löschen" klicken, werden Ihr BizTalk Service und alle in ihm bereitgestellten Elemente entfernt.</td>
</tr>
</table>

Überwachen
----------

Auf der Registerkarte "Überwachen" werden die folgenden Informationen angezeigt:

#### Metrikendiagramm

Ein Diagramm, das die ausgewählten Leistungsmetriken anzeigt. Diese Metriken liefern Echtzeit-Werte über den Zustand Ihres BizTalk Service. Sie wählen aus, welche Leistungsmetriken angezeigt werden. Bis zu sechs Leistungsmetriken können gleichzeitig angezeigt werden.

##### Relativ oder Absolut

Das Diagramm zeigt Trends und den aktuellen Wert jeder Metrik an. Dies ist die Option **Relativ**. Wählen Sie **Absolut** aus, um eine Y-Achse einzublenden, so dass absolute Werte angezeigt werden.

##### Intervall

Zur Änderung des Zeitbereichs, innerhalb dessen die Metriken im Diagramm angezeigt werden. Folgende Optionen sind verfügbar:

-   1 Stunde
-   1 Tag
-   7 Tage

#### Entfernen oder Anzeigen von Metriken im Diagramm

> 1.  Klicken Sie auf die Registerkarte **Überwachen**.
> 2.  Klicken Sie in der Symbolleiste auf **Metriken hinzufügen**.<br/>
![Klicken Sie auf "Metriken hinzufügen".](./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png)
> 3.  Überprüfen Sie die Leistungsmetriken, die Sie anzeigen möchten, auf der Registerkarte **Überwachen**.
> 4.  Klicken Sie auf das Häkchen, um zur Registerkarte **Überwachen** zurückzukehren.
> 5.  Klicken Sie auf den Kreis neben der Metrik, um den Wert dieser Metrik im Diagramm anzuzeigen.<br/>
>      Die Metrik **CPU-Auslastung** ist grau unterlegt. Das heißt, ihr Wert wird im Diagramm nicht angezeigt:<br/>
>      ![Metrik "CPU-.Auslastung" ist grau unterlegt](./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png)<br/>
>      Klicken Sie auf den grau unterlegten Kreis, um die Metrik **CPU-Auslastung** zu aktivieren und ihren Wert im Diagramm anzuzeigen:<br/>
>      ![Metrik "CPU-.Auslastung" ist aktiviert](./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png)

> 1.  Wenn Sie eine Metrik aus dem Diagramm und der Liste entfernen möchten, klicken Sie in der Symbolleiste auf **Metrik löschen**. Durch Klicken auf **Metrik löschen** wird die entsprechende Metrik von der Registerkarte "Überwachung" entfernt. Wenn Sie die Metrik wieder zur Liste hinzufügen möchten, klicken Sie in der Symbolleiste auf **Metriken hinzufügen**, und markieren Sie die Metrik, und klicken Sie auf das Kontrollkästchen, um zur Registerkarte **Überwachen** zurückzukehren. Klicken Sie auf den grau unterlegten Kreis, um die Metrik im Diagramm zu aktivieren.

Verfügbare Metriken
-------------------

Die folgenden Leistungsindikatoren/Metriken stehen zur Verfügung:


<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Metrik</strong></td>
<td><strong>Beschreibung</strong></td>
</tr>
<tr>
<td>Paketumlaufzeity</td>
<td>Diese Leistungsmetrik zeigt die durchschnittliche Zeit in Millisekunden (ms) vom Eingang einer Nachricht bis zu ihrer vollständigen Bearbeitung durch den BizTalk Service zwischen allen Brücken an. Es werden nur erfolgreich verarbeitete Nachrichten gezählt.<br/><br/>
Bei folgenden Ereignissen wird ein Zeitstempel erstellt:
<ul>
<li>Eintritt der Nachricht in das Gateway</li>
<li>Weiterleitung der Nachricht an das Ziel</li>
<li>Übermittlung der Zielbestätigung</li>
<li>Senden der Zielbestätigung an das Gateway</li>
</ul>
<br/>
Diese Metrik zeigt das Ergebnis der folgenden Berechnung:
<br/><br/>
[Senden der Zielbestätigung an das Gateway] - [Eintritt der Nachricht in das Gateway]</td>
</tr>
<tr>
<td>Fehler an der Quelle</td>
<td>Diese Leistungsmetrik zeigt die Gesamtzahl der Nachrichten des BizTalk Service an, die beim Abruf von den Quellendpunkten fehlgeschlagen sind.</td>
</tr>
<tr>
<td>CPU-Auslastung</td>
<td>Listet die durchschnittliche Prozessorzeit aller Rolleninstanzen in % auf.</td>
</tr>
<tr>
<td>Verarbeitungszeit</td>
<td>Diese Leistungsmetrik zeigt in Millisekunden (ms) die durchschnittliche Zeit für die Verarbeitung einer Nachricht durch den BizTalk Service zwischen allen Brücken an - ausgenommen die am Ziel verbrachte Zeit. Es werden nur erfolgreich verarbeitete Nachrichten gezählt.<br/><br/>
Bei folgenden Ereignissen wird ein Zeitstempel erstellt:

<ul>
<li>Eintritt der Nachricht in das Gateway</li>
<li>Weiterleitung der Nachricht an das Ziel</li>
<li>Übermittlung der Zielbestätigung</li>
<li>Senden der Zielbestätigung an das Gateway</li>
</ul>
<br/>Diese Metrik zeigt das Ergebnis der folgenden Berechnung:<br/><br/>
[Senden der Zielbestätigung an das Gateway] - [Eintritt der Nachricht in das Gateway] - [Übermittlung der Zielbestätigung] + [Übermittlung der Zielbestätigung]</td>
</tr>
<tr>
<td>Fehler im Verlauf</td>
<td>Diese Leistungsmetrik zeigt die Gesamtzahl der Nachrichten an, die innerhalb eines bestimmten Zeitintervalls während der Verarbeitung durch den BizTalk Service zwischen allen Brücken fehlgeschlagen sind.</td>
</tr>
<tr>
<td>Gesendete Nachrichten</td>
<td>Diese Leistungsmetrik zeigt die Gesamtzahl der Nachrichten an, die innerhalb eines bestimmten Zeitintervalls vom BizTalk Service zwischen allen Brücken gesendet worden sind. Diese Metrik wird erhöht, wenn eine aus einer Pipeline gesendete Nachricht das Routenziel erreicht. Diese Metrik zeigt nicht die erfolgreiche Verarbeitung einer Nachricht an.<br/><br/>
In einem Abfrage-Antwort-Szenario wird die Metrik erhöht, wenn das Routenziel eine Empfangsbestätigung an die Pipeline zurücksendet.</td>
</tr>
<tr>
<td>Empfangene Nachrichten</td>
<td>Diese Leistungsmetrik zeigt die Gesamtzahl der Nachrichten an, die innerhalb eines bestimmten Zeitintervalls vom BizTalk Service zwischen allen Brücken empfangen worden sind. Diese Metrik wird erhöht, wenn eine Nachricht aus der Pipeline empfangen wird.</td>
</tr>
<tr>
<td>Nachrichten in Verarbeitung</td>
<td>Diese Leistungsmetrik zeigt die Gesamtzahl der Nachrichten an, die aktuell innerhalb eines bestimmten Zeitintervalls vom BizTalk Service bearbeitet werden.</td>
</tr>
<tr>
<td>Verarbeitete Nachrichten</td>
<td>Diese Leistungsmetrik zeigt die Gesamtzahl der Nachrichten an, die innerhalb eines bestimmten Zeitintervalls vom BizTalk Service zwischen allen Brücken erfolgreich verarbeitet worden sind. Diese Metrik wird erhöht, wenn eine Nachricht erfolgreich on der Pipeline empfangen und erfolgreich an das Ziel weitergeleitet wird.</td>
</tr>
</table>



Skalieren
---------

Auf der Registerkarte "Skalieren" können Sie die Anzahl der von Ihrem BizTalk Service verwendeten Einheiten addieren oder subtrahieren. Standardmäßig ist eine Einheit konfiguriert. Zur Skalierung Ihres BizTalk Service können zusätzliche Einheiten hinzugefügt werden. Wenn Sie die Skalierung erhöhen, wird auch der Durchsatz erhöht. Auch die Menge der Ressourcen, also der bereitgestellten Brücken, Verträge und lokalen Branchenverbindungen, sowie die Verarbeitungsleistung werden erhöht. Beispiel: Sie erhöhen die Skalierung von 1 auf 2 Einheiten. In dieser Lage können Sie nun die doppelte Anzahl von Brücken bereitstellen sowie die Verträge, die lokalen Branchenverbindungen und die Verarbeitungsleistung verdoppeln.

Einige BizTalk-Editionen bieten keine Skalierungsoption. In diesen Editionen ist nur eine Einheit erlaubt. Informationen darüber, auf wie viele Einheiten Ihre Edition skaliert werden kann, finden Sie unter [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition](http://go.microsoft.com/fwlink/p/?LinkID=302279).

Eine Erhöhung der Anzahl der Einheiten kann sich auf den Preis auswirken. Wenn Sie die Anzahl der Einheiten erhöhen, erhalten Sie nach dem Klicken auf **Speichern** die Meldung, dass dies Einfluss auf Ihre Rechnung hat. Sie können dann wählen, ob Sie fortfahren möchten. Wenn Sie die Anzahl der Einheiten erhöhen, ändert sich der Status des BizTalk Service von "Active" auf "Updating". Im Status "Updating" läuft Ihr BizTalk Service weiter.

Nächste Schritte
----------------

Nun, da Sie mit den verschiedenen Registerkarten vertraut sind, können Sie mehr über die Funktionen von Azure BizTalk Services erfahren:

-   [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873)

Weitere Informationen
---------------------

-   [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [BizTalk Services: BizTalk Service-Statusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Wie verwende ich das Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

