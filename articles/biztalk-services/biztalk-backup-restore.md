<properties 
	pageTitle="Erstellen und Wiederherstellen eine Sicherung in BizTalk Services | Microsoft Azure" 
	description="BizTalk Services umfasst Funktionen für das Sichern und Wiederherstellen. Erfahren Sie, wie Sie eine Sicherung erstellen und wiederherstellen, und wie Sie festlegen, was gesichert werden soll. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2015" 
	ms.author="mandia"/>


# BizTalk Services: Sichern und Wiederherstellen

Azure BizTalk Services bieten eine Sicherungs- und Wiederherstellungsfunktion. In diesem Thema wird beschrieben, wie BizTalk Services mithilfe des Azure-Verwaltungsportals gesichert und wiederhergestellt werden.

Sie können BizTalk Services auch mithilfe der [BizTalk Services-REST-API](http://go.microsoft.com/fwlink/p/?LinkID=325584) sichern.

## Voraussetzungen

- Sichern und Wiederherstellen ist unter Umständen nicht für alle Editionen verfügbar. Siehe [BizTalk Services: Editionsübersicht](biztalk-editions-feature-chart.md).

	**Hinweis** Hybridverbindungen werden editionsunabhängig NICHT gesichert.

- Mit dem Azure-Verwaltungsportal können Sie bedarfsgesteuerte oder geplante Sicherungskopien erstellen.

- Gesicherte Inhalte können im selben BizTalk Service oder in einem neuen BizTalk Service wiederhergestellt werden. Wenn Sie den BizTalk Service unter demselben Namen wiederherstellen möchten, muss der vorhandene BizTalk Service gelöscht werden und der Name muss verfügbar sein. Nach dem Löschen eines BizTalk Service kann es länger dauern als erwartet, bis der gleiche Name verfügbar ist. Wenn Sie nicht warten können, bis der gleiche Name verfügbar ist, dann stellen Sie in einem neuen BizTalk Service wieder her.

- BizTalk Services können in derselben Edition oder in einer höheren Edition wiederhergestellt werden. Die Wiederherstellung von BizTalk Services in einer früheren Edition als derjenigen, von der die Sicherungskopie erstellt wurde, wird nicht unterstützt.

	Eine Sicherungskopie der Basic Edition kann z. B. auf die Premium Edition wiederhergestellt werden. Aber eine Sicherungskopie der Premium Edition kann nicht auf die Standard Edition wiederhergestellt werden.

- Die EDI-Kontrollnummern werden gesichert, um ihre Kontinuität aufrechtzuerhalten. Wenn Nachrichten nach der letzten Sicherung verarbeitet werden, kann es bei der Wiederherstellung dieses Sicherungsinhalts zu doppelten Kontrollnummern kommen.

- Wenn in einem Batch aktive Nachrichten vorliegen, verarbeiten Sie den Batch, **bevor** Sie eine Sicherung durchführen. Beim Erstellen (bedarfsgesteuerter oder geplanter) Sicherungen werden Nachrichten in Batches niemals gespeichert.

	**Wenn eine Sicherung mit aktiven Nachrichten in einem Batch durchgeführt wird, werden diese Nachrichten nicht gesichert und gehen deshalb verloren.**

- Optional: Beenden Sie alle Verwaltungsvorgänge im BizTalk Services-Portal.


## Erstellen einer Sicherung

Eine Sicherungskopie kann jederzeit erstellt werden und ist komplett benutzergesteuert. Dieser Abschnitt enthält die Schritte zum Erstellen von Sicherungen mit dem Azure-Verwaltungsportal. Dies umfasst Folgendes:

[Bedarfsgesteuerte Sicherung](#backupnow)

[Planen einer Sicherung](#backupschedule)

#### <a name="backupnow"></a>Bedarfsgesteuerte Sicherung
1. Wählen Sie im Azure-Verwaltungsportal zuerst **BizTalk Services** und dann den BizTalk Service aus, den Sie sichern möchten.
2. Wählen Sie auf der Registerkarte **Dashboard** unten auf der Seite **Sichern**.
3. Geben Sie einen Namen für die Sicherung ein. Beispiel: *meinBizTalkService*SK*Datum*.
4. Wählen Sie ein Blobspeicherkonto aus, und klicken Sie auf das Häkchen, um mit der Sicherung zu beginnen.

Sobald die Sicherung abgeschlossen ist, wird im Speicherkonto ein Container mit dem von Ihnen eingegebenen Sicherungsnamen erstellt. Dieser Container enthält die Sicherungskonfiguration Ihres BizTalk Service.

#### <a name="backupschedule"></a>Planen einer Sicherung

1. Klicken Sie im Azure-Verwaltungsportal auf **BizTalk Services**, wählen Sie den Namen des BizTalk Service aus, für den Sie die Sicherung planen möchten, und klicken Sie anschließend auf die Registerkarte **Konfigurieren**.
2. Stellen Sie den **Sicherungsstatus** auf **Automatisch** ein. 
3. Wählen Sie das **Speicherkonto** zum Speichern der Sicherung aus, geben Sie unter **Häufigkeit** an, wie oft Sicherungen erstellt werden sollen, und unter **Aufbewahrung in Tagen** deren Aufbewahrungszeit:

	![][AutomaticBU]

	**Hinweise** – Die unter **Aufbewahrung in Tagen** angegebene Aufbewahrungszeit muss länger sein als die Sicherungshäufigkeit. – Wählen Sie **Bewahren Sie immer mindestens eine Sicherung auf**, auch wenn die Aufbewahrungszeit überschritten ist.
	

4. Wählen Sie **Speichern** aus.


Wenn ein geplanter Sicherungsauftrag ausgeführt wird, wird im von Ihnen angegebenen Speicherkonto ein Container zum Speichern der Sicherungsdateien erstellt. Der Name des Containers weist das Format *BizTalk Service-Name-Datum-Zeit* auf.

Wenn auf dem BizTalk Service-Dashboard der Status **Fehler** angezeigt wird:

![Status der letzten geplanten Sicherung][BackupStatus]

Über den Link werden die Vorgangsprotokolle der Verwaltungsdienste geöffnet, die Sie bei der Behebung von Problemen unterstützen. Siehe [BizTalk Services: Fehlerbehebung mit Vorgangsprotokollen](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## Wiederherstellen

Sie können eine Sicherung entweder über das Azure-Verwaltungsportal oder über die [REST-API zum Wiederherstellen von BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=325582) wiederherstellen. Dieser Abschnitt enthält die Schritte zur Wiederherstellung über das Verwaltungsportal.

#### Vor dem Wiederherstellen einer Sicherung

- Bei der Wiederherstellung eines BizTalk Service können neue Speicher für Nachverfolgung, Archivierung und Überwachung festgelegt werden.

- Die EDI-Laufzeitdaten werden wiederhergestellt. Die Kontrollnummern werden in der EDI-Laufzeit-Sicherungskopie gespeichert. Die wiederhergestellten Kontrollnummern haben dieselbe Reihenfolge wie zum Zeitpunkt der Sicherung. Wenn Nachrichten nach der letzten Sicherung verarbeitet werden, kann es bei der Wiederherstellung dieses Sicherungsinhalts zu doppelten Kontrollnummern kommen.

#### Wiederherstellen einer Sicherung

1. Wählen Sie im Azure-Verwaltungsportal **Neu** > **App Services** > **BizTalk Service** > **Wiederherstellen** aus:

	![Wiederherstellen einer Sicherung][Restore]

2. Wählen Sie unter **Sicherungs-URL** das Ordnersymbol aus, und erweitern Sie das Azure-Speicherkonto mit der Konfigurationssicherung des BizTalk Service. Erweitern Sie den Container, und wählen Sie im rechten Bereich die entsprechende TXT-Sicherungsdatei aus. <br/><br/>Wählen Sie **Öffnen** aus.

3. Geben Sie auf der Seite **BizTalk Service wiederherstellen** einen**Namen für den BizTalk Service** ein, und überprüfen Sie die **Domänen-URL**, **Edition** und **Region** für den wiederherzustellenden BizTalk Service. **Erstellen Sie eine neue SQL-Datenbankinstanz** für die Nachverfolgungsdatenbank:

	![][RestoreBizTalkService]

	Wählen Sie den Pfeil für "Weiter" aus.

4. 	Überprüfen Sie den Namen der SQL-Datenbank, und geben Sie den physischen Server, auf dem die SQL-Datenbank erstellt wird, sowie einen Benutzernamen/ein Kennwort für diesen Server ein.


	Wenn Sie die Edition, Größe und weitere Eigenschaften der SQL-Datenbank konfigurieren möchten, wählen Sie **Erweiterte Datenbankeinstellungen konfigurieren** aus.

	Wählen Sie den Pfeil für "Weiter" aus.

5. Erstellen Sie ein neues Speicherkonto, oder geben Sie ein vorhandenes Speicherkonto für den BizTalk Service an.

7. Klicken Sie auf das Häkchen, um die Wiederherstellung zu starten.

Wenn die Wiederherstellung erfolgreich abgeschlossen wurde, wird auf der Seite "BizTalk Services" im Azure-Verwaltungsportal ein neuer BizTalk Service im angehaltenen Status angezeigt.



### <a name="postrestore"></a>Nach dem Wiederherstellen einer Sicherungskopie

BizTalk Services werden immer im Status **Angehalten** wiederhergestellt. In diesem Status können Sie vor Inbetriebnahme der neuen Umgebung beliebige Konfigurationsänderungen vornehmen. Beispiele:

- Wenn Sie BizTalk Service-Anwendungen mithilfe des Azure BizTalk Services SDK erstellt haben, müssen Sie eventuell die ACS-Anmeldeinformationen (Access Control Service, Zugriffssteuerungsdienst) in diesen Anwendungen aktualisieren, damit diese in der wiederhergestellten Umgebung funktionieren.

- Sie stellen einen BizTalk Service wieder her, um eine vorhandene BizTalk Service-Umgebung zu replizieren. Wenn Sie in einem solchen Fall im ursprünglichen BizTalk Services-Portal Vereinbarungen konfiguriert haben, die FTP-Quellordner verwenden, müssen Sie unter Umständen die Vereinbarungen in der wiederhergestellten Umgebung auf die Verwendung eines anderen FTP-Quellordners aktualisieren. Ansonsten versuchen möglicherweise zwei unterschiedliche Vereinbarungen, dieselbe Nachricht abzurufen.

- Wenn Sie eine Wiederherstellung durchgeführt haben, um mehrere BizTalk Service-Umgebungen laufen zu lassen, stellen Sie sicher, dass in den Visual Studio-Anwendungen, PowerShell-Cmdlets, REST-APIs oder Trading Partner Management OM-APIs die richtige Umgebung als Ziel angegeben ist.

- Es empfiehlt sich auch, automatisierte Sicherungen der wiederhergestellten BizTalk Service-Umgebung zu konfigurieren.

Um den BizTalk Service im Azure-Verwaltungsportal zu starten, wählen Sie zuerst den wiederhergestellten BizTalk Service und dann in der Taskleiste **Fortsetzen** aus.



## Was wird gesichert?

Wenn eine Sicherung erstellt wird, werden die folgenden Elemente gesichert:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Gesicherte Elemente</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Azure BizTalk Services-Portal</strong></td>
</tr> 
<tr>
<td>Konfiguration und Laufzeit</td> 
<td>
<ul>
<li>Partner- und Profildetails</li>
<li>Partnerverträge</li>
<li>Bereitgestellte benutzerdefinierte Assemblies</li>
<li>Bereitgestellte Brücken</li>
<li>Zertifikate</li>
<li>Bereitgestellte Transformationen</li>
<li>Pipelines</li>
<li>Im BizTalk Services-Portal erstellte und gespeicherte Vorlagen</li>
<li>X12 ST01- und GS01-Zuordnungen</li>
<li>Kontrollnummern (EDI)</li>
<li>AS2-Nachrichten-MIC-Werte</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Azure BizTalk Service</strong></td>
</tr> 
<tr>
<td>SSL-Zertifikat</td> 
<td>
<ul>
<li>SSL-Zertifikatdaten</li>
<li>SSL-Zertifikatkennwort</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk Service-Einstellungen</td> 
<td>
<ul>
<li>Skalierungseinheitenzählung</li>
<li>Edition</li>
<li>Produktversion</li>
<li>Region/Rechenzentrum</li>
<li>Access Control Service (ACS)-Namespace und -Schlüssel</li>
<li>Nachverfolgungsdatenbank-Verbindungszeichenfolgen</li>
<li>Archivierung von Speicherkonto-Verbindungszeichenfolgen</li>
<li>Überwachung von Speicherkonto-Verbindungszeichenfolgen</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Zusätzliche Elemente</strong></td>
</tr> 
<tr>
<td>Nachverfolgungsdatenbank</td> 
<td>Bei der Erstellung des BizTalk Service werden die Details zur Nachverfolgungsdatenbank, darunter der Azure-SQL-Datenbankserver und der Name der Nachverfolgungsdatenbank, eingegeben. Die Nachverfolgungsdatenbank wird nicht automatisch gesichert.
<br/><br/>
<strong>Wichtig</strong><br/>
Wenn die Nachverfolgungsdatenbank gelöscht wird und wiederhergestellt werden soll, muss bereits eine vorherige Sicherung existieren. Wenn keine Sicherungskopie existiert, können die Nachverfolgungsdatenbank und ihre Daten nicht wiederhergestellt werden. Erstellen Sie in diesem Fall eine neue Nachverfolgungsdatenbank mit demselben Datenbanknamen. Georeplikation wird empfohlen.</td>
</tr> 
</table>

## Weiter

Um Azure BizTalk Services im Azure-Verwaltungsportal zu erstellen, wechseln Sie zu [BizTalk Services: Erstellen eines BizTalk Service im Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280). Wenn Sie mit dem Erstellen von Anwendungen beginnen möchten, wechseln Sie zu [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Weitere Informationen
- [Sichern von BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Wiederherstellen von BizTalk Services aus einer Sicherung](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk Services: Editionsübersicht](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk Services: Erstellen eines BizTalk Service im Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk Services: Bereitstellungsstatusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Wie verwende ich das Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 

<!---HONumber=Oct15_HO3-->