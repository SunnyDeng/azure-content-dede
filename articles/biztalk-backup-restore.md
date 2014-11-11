<properties linkid="biztalk-backup-restore" urlDisplayName="BizTalk Services: Backup and Restore" pageTitle="BizTalk Services: Backup and Restore | Azure" metaKeywords="" description="BizTalk Services includes Backup and Restore capabilities. When creating a Backup, a snapshot of the BizTalk Services configuration is taken." metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Backup and Restore" authors="mandia"  solutions="" writer="mandia" manager="dwrede" editor="cgronlun"  />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# BizTalk Services: Sichern und Wiederherstellen

Azure BizTalk Services bieten eine Sicherungs- und Wiederherstellungsfunktion. In diesem Thema wird beschrieben, wie BizTalk Services mithilfe des Azure-Verwaltungsportals gesichert und wiederhergestellt werden. Dies umfasst:

-   [Voraussetzungen][Voraussetzungen]
-   [Erstellen einer Sicherungskopie][Erstellen einer Sicherungskopie]
-   [Wiederherstellen einer Sicherung][Wiederherstellen einer Sicherung]
-   [Was wird gesichert?][Was wird gesichert?]

Sie können BizTalk Services auch mithilfe der [BizTalk Services-REST-API][BizTalk Services-REST-API] sichern.

## <a name="beforebackup"></a>Voraussetzungen

-   Sichern und Wiederherstellen ist unter Umständen nicht für alle Editionen verfügbar. Siehe [BizTalk Services: Editionen-Diagramm][BizTalk Services: Editionen-Diagramm].

    **Hinweis** Hybridverbindungen werden editionsunabhängig NICHT gesichert.

-   Mit dem Azure-Verwaltungsportal können Sie bedarfsgesteuerte oder geplante Sicherungskopien erstellen.

-   Gesicherte Inhalte können im selben BizTalk Service oder in einem neuen BizTalk Service wiederhergestellt werden. Wenn Sie den BizTalk Service unter demselben Namen wiederherstellen möchten, muss der vorhandene BizTalk Service gelöscht werden. Andernfalls schlägt die Wiederherstellung fehl.

-   BizTalk Services können in derselben Edition oder in einer höheren Edition wiederhergestellt werden. Die Wiederherstellung von BizTalk Services in einer früheren Edition als derjenigen, von der die Sicherungskopie erstellt wurde, wird nicht unterstützt.

    Eine Sicherungskopie der Basic Edition kann z. B. auf die Premium Edition wiederhergestellt werden. Aber eine Sicherungskopie der Premium Edition kann nicht auf die Standard Edition wiederhergestellt werden.

-   Die EDI-Kontrollnummern werden gesichert, um ihre Kontinuität aufrechtzuerhalten. Wenn Nachrichten nach der letzten Sicherung verarbeitet werden, kann es bei der Wiederherstellung dieses Sicherungsinhalts zu doppelten Kontrollnummern kommen.

-   Wenn in einem Stapel aktive Nachrichten vorliegen, verarbeiten Sie den Stapel, **bevor** Sie eine Sicherung durchführen. Beim Erstellen (bedarfsgesteuerter oder geplanter) Sicherungskopien werden Nachrichten in Stapeln niemals gespeichert.

    **Wenn eine Sicherung mit aktiven Nachrichten in einem Stapel durchgeführt wird, werden diese Nachrichten nicht gesichert und gehen deshalb verloren.**

-   Optional: Stoppen Sie alle eventuellen Verwaltungsvorgänge im BizTalk Services-Portal.

## <a name="createbu"></a>Erstellen einer Sicherungskopie

Eine Sicherungskopie kann jederzeit erstellt werden und ist komplett benutzergesteuert. Dieser Abschnitt enthält die Schritte zum Erstellen von Sicherungskopien mit dem Azure-Verwaltungsportal. Dies umfasst:

[Bedarfsgesteuerte Sicherung][Bedarfsgesteuerte Sicherung]

[Planen einer Sicherung][Planen einer Sicherung]

#### <a name="backupnow"></a>Bedarfsgesteuerte Sicherung

1.  Wählen Sie im Azure-Verwaltungsportal zuerst **BizTalk Services** und dann den zu sichernden BizTalk Service aus.
2.  Wählen Sie auf der Registerkarte **Dashboard** unten auf der Seite **Sichern**.
3.  Geben Sie einen Namen für die Sicherungskopie ein. Beispiel: *meinBizTalkService*SK*Datum*.
4.  Wählen Sie ein Blob-Speicherkonto aus, und klicken Sie auf das Häkchen, um mit der Sicherung zu beginnen.

Sobald die Sicherung abgeschlossen ist, wird im Speicherkonto ein Container mit dem von Ihnen eingegebenen Sicherungsnamen erstellt. Dieser Container enthält die Sicherungskonfiguration Ihres BizTalk Service.

#### <a name="backupschedule"></a>Planen einer Sicherung

1.  Klicken Sie im Azure-Verwaltungsportal auf **BizTalk Services**, wählen Sie den Namen des BizTalk Service aus, für den Sie die Sicherung planen möchten, und klicken Sie anschließend auf die Registerkarte **Konfigurieren**.
2.  Stellen Sie den **Sicherungsstatus** auf **Automatisch** ein.
3.  Wählen Sie das **Speicherkonto** zum Speichern der Sicherung aus, geben Sie unter **Häufigkeit** an, wie oft Sicherungen erstellt werden sollen, und unter **Aufbewahrung in Tagen** deren Aufbewahrungszeit:

    ![][0]

    **Hinweise**

-   Unter **Aufbewahrung in Tagen** muss die Aufbewahrungszeit länger sein als die Sicherungshäufigkeit.
-   Wählen Sie **Bewahren Sie immer mindestens eine Sicherung auf** aus, auch wenn die Aufbewahrungszeit überschritten ist.

1.  Wählen Sie **Speichern** aus.

Wenn ein geplanter Sicherungsjob ausgeführt wird, wird im von Ihnen angegebenen Speicherkonto ein Container zum Speichern der Sicherungsdateien erstellt. Der Name des Containers hat das Format *BizTalk Service-Name-Datum-Zeit*.

Wenn auf dem BizTalk Service-Dashboard der Status **Fehlgeschlagen** angezeigt wird:

![Status der letzten geplanten Sicherung][Status der letzten geplanten Sicherung]

Über den Link werden die Operationsprotokolle der Verwaltungsdienste geöffnet, die Sie bei der Behebung von Problemen unterstützen. Siehe [BizTalk Services: Fehlerbehebung mit Operationsprotokollen][BizTalk Services: Fehlerbehebung mit Operationsprotokollen].

## <a name="restore"></a>Wiederherstellen

Sie können eine Sicherungskopie entweder über das Azure-Verwaltungsportal oder über die [REST-API zum Wiederherstellen von BizTalk Services][REST-API zum Wiederherstellen von BizTalk Services] wiederherstellen. Dieser Abschnitt enthält die Schritte zur Wiederherstellung über das Verwaltungsportal.

#### Vor dem Wiederherstellen einer Sicherungskopie

-   Bei der Wiederherstellung eines BizTalk Service können neue Stores für Verfolgung, Archivierung und Überwachung festgelegt werden.

-   Wenn Sie den BizTalk Service mit demselben Namen wiederherstellen möchten, löschen Sie den existierenden BizTalk Service, bevor Sie mit der Wiederherstellung beginnen. Andernfalls schlägt die Wiederherstellung fehl.

-   Die EDI-Laufzeitdaten werden wiederhergestellt. Die Kontrollnummern werden in der EDI-Laufzeit-Sicherungskopie gespeichert. Die wiederhergestellten Kontrollnummern haben dieselbe Reihenfolge wie zum Zeitpunkt der Sicherung. Wenn Nachrichten nach der letzten Sicherung verarbeitet werden, kann es bei der Wiederherstellung dieses Sicherungsinhalts zu doppelten Kontrollnummern kommen.

#### Wiederherstellen einer Sicherung

1.  Wählen Sie im Azure-Verwaltungsportal **Neu** \> **App-Dienste** \> **BizTalk Service** \> **Wiederherstellen** aus:

    ![Wiederherstellen einer Sicherung][1]

2.  Wählen Sie unter **Sicherungs-URL** das Ordnersymbol aus, und erweitern Sie das Azure-Speicherkonto mit der Konfigurationssicherung des BizTalk Service. Erweitern Sie den Container, und wählen Sie im rechten Bereich die entsprechende TXT-Sicherungsdatei aus.

    Wählen Sie **Öffnen** aus.

3.  Geben Sie auf der Seite **BizTalk Service wiederherstellen** den **** Namen des BizTalk Service ein, und überprüfen Sie die **Domänen-URL**, **Edition** und **Region** für den wiederherzustellenden BizTalk Service. **Erstellen Sie eine neue SQL-Datenbankinstanz** für die Nachverfolgungsdatenbank:

    ![][2]

    Wählen Sie den Pfeil für "Weiter" aus.

4.  Überprüfen Sie den Namen der SQL-Datenbank, und geben Sie den physischen Server, auf dem die SQL-Datenbank erstellt wird, sowie einen Benutzernamen/ein Kennwort für diesen Server ein.

    Wenn Sie die Edition, Größe und weitere Eigenschaften der SQL-Datenbank konfigurieren möchten, wählen Sie **Erweiterte Datenbankeinstellungen konfigurieren** aus.

    Wählen Sie den Pfeil für "Weiter" aus.

5.  Erstellen Sie ein neues Speicherkonto, oder geben Sie ein vorhandenes Speicherkonto für den BizTalk Service an.

6.  Klicken Sie auf das Häkchen, um die Wiederherstellung zu starten.

Wenn die Wiederherstellung erfolgreich abgeschlossen wurde, wird auf der Seite "BizTalk Services" im Azure-Verwaltungsportal ein neuer BizTalk Service im Standby-Status angezeigt.

### <a name="postrestore"></a>Nach dem Wiederherstellen einer Sicherungskopie

BizTalk Services werden immer im **Standby**-Status wiederhergestellt. In diesem Status können Sie vor Inbetriebnahme der neuen Umgebung beliebige Konfigurationsänderungen vornehmen. Beispiele:

-   Wenn Sie BizTalk Service-Anwendungen mithilfe des Azure BizTalk Services SDK erstellt haben, müssen Sie eventuell die ACS-Anmeldeinformationen in diesen Anwendungen aktualisieren, damit diese in der wiederhergestellten Umgebung funktionieren.

-   Sie stellen einen BizTalk Service wieder her, um eine vorhandene BizTalk Service-Umgebung zu replizieren. Wenn Sie in einem solchen Fall im ursprünglichen BizTalk Services-Portal Vereinbarungen konfiguriert haben, die FTP-Quellordner verwenden, müssen Sie unter Umständen die Vereinbarungen in der wiederhergestellten Umgebung auf die Verwendung eines anderen FTP-Quellordners aktualisieren. Ansonsten versuchen möglicherweise zwei unterschiedliche Vereinbarungen, dieselbe Nachricht abzurufen.

-   Wenn Sie eine Wiederherstellung durchgeführt haben, um mehrere BizTalk Service-Umgebungen laufen zu lassen, stellen Sie sicher, dass in den Visual Studio-Anwendungen, PowerShell-Cmdlets, REST-APIs oder Trading Partner Management OM-APIs die richtige Umgebung als Ziel angegeben ist.

-   Es empfiehlt sich auch, automatisierte Sicherungen der wiederhergestellten BizTalk Service-Umgebung zu konfigurieren.

Um den BizTalk Service im Azure-Verwaltungsportal zu starten, wählen Sie zuerst den wiederhergestellten BizTalk Service und dann in der Taskleiste **Fortsetzen** aus.

## <a name="budata"></a>Was wird gesichert?

Wenn eine Sicherungskopie erstellt wird, werden die folgenden Elemente gesichert:

<table border="1">
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Elemente, die gesichert werden

</th>
</tr>
<tr>
<td colspan="2">
**Azure BizTalk Services-Portal**

</td>
</tr>
<tr>
<td>
Konfiguration und Laufzeit

</td>
<td>
<UL>
<li>Partnerund Profildetails</li>
<li>Partnerverträge</li>
<li>Bereitgestellte benutzerdefinierte Assemblies</li>
<li>Bereitgestellte Brücken</li>
<li>Zertifikate</li>
<li>Bereitgestellte Transformationen</li>
<li>Pipelines</li>
<li>Im BizTalk Services-Portal erstellte und gespeicherte Vorlagen</li>
<li>X12 ST01und GS01-Zuordnungen</li>
<li>Kontrollnummern (EDI)</li>
<li>AS2-Nachrichten-MIC-Werte</li>
</UL>
</td>
</tr>
<tr>
<td colspan="2">
**Azure BizTalk Service**

</td>
</tr>
<tr>
<td>SSL-Zertifikat</td>

<td>
<UL>
<li>SSL-Zertifikatdaten</li>
<li>SSL-Zertifikatkennwort</li>
</UL>
</td>
</tr>
<tr>
<td>
BizTalk Service-Einstellungen

</td>
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
**Zusätzliche Elemente**

</td>
</tr>
<tr>
<td>
Nachverfolgungsdatenbank

</td>
<td>
Bei der Erstellung des BizTalk Service werden die Details zur Nachverfolgungsdatenbank, darunter der Azure-SQL-Datenbankserver und der Name der Nachverfolgungsdatenbank, eingegeben. Die Nachverfolgungsdatenbank wird nicht automatisch gesichert.<br></br>
<br></br>
<strong>Wichtig</strong><br></br>
 Wenn die Nachverfolgungsdatenbank gelöscht wird und wiederhergestellt werden soll, muss bereits eine vorherige Sicherungskopie existieren. Wenn keine Sicherungskopie existiert, können die Nachverfolgungsdatenbank und ihre Daten nicht wiederhergestellt werden. Erstellen Sie in diesem Fall eine neue Nachverfolgungsdatenbank mit demselben Datenbanknamen. Georeplikation wird empfohlen.

</td>
</tr>
</table>
## Weiter

Um Azure BizTalk Services im Azure-Verwaltungsportal zu erstellen, wechseln Sie zu [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal][BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal]. Wenn Sie mit dem Erstellen von Anwendungen beginnen möchten, wechseln Sie zu [Azure BizTalk Services][Azure BizTalk Services].

## Weitere Informationen

-   [Backup BizTalk Service (Sichern von BizTalk Services, in englischer Sprache)][BizTalk Services-REST-API]
-   [Restore BizTalk Service from Backup (Wiederherstellen von BizTalk Services aus Sicherungskopien, in englischer Sprache)][REST-API zum Wiederherstellen von BizTalk Services]
-   [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition][BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition]
-   [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal][BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal]
-   [BizTalk Services: Bereitstellungsstatusübersicht][BizTalk Services: Bereitstellungsstatusübersicht]
-   [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"][BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"]
-   [BizTalk Services: Drosselung][BizTalk Services: Drosselung]
-   [BizTalk Services: Name und Schlüssel des Ausstellers][BizTalk Services: Name und Schlüssel des Ausstellers]
-   [Wie verwende ich das Azure BizTalk Services SDK][Wie verwende ich das Azure BizTalk Services SDK]

  [Voraussetzungen]: #beforebackup
  [Erstellen einer Sicherungskopie]: #createbu
  [Wiederherstellen einer Sicherung]: #restore
  [BizTalk Services-REST-API]: http://go.microsoft.com/fwlink/p/?LinkID=325584
  [BizTalk Services: Editionen-Diagramm]: http://azure.microsoft.com/de-de/documentation/articles/biztalk-editions-feature-chart/
  [Bedarfsgesteuerte Sicherung]: #backupnow
  [Planen einer Sicherung]: #backupschedule
  [0]: ./media/biztalk-backup-restore/AutomaticBU.png
  [Status der letzten geplanten Sicherung]: ./media/biztalk-backup-restore/status-last-backup.png
  [BizTalk Services: Fehlerbehebung mit Operationsprotokollen]: http://go.microsoft.com/fwlink/?LinkId=391211
  [REST-API zum Wiederherstellen von BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=325582
  [1]: ./media/biztalk-backup-restore/restore-ui.png
  [2]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
  [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Azure BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=235197
  [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [BizTalk Services: Bereitstellungsstatusübersicht]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk Services: Drosselung]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk Services: Name und Schlüssel des Ausstellers]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Wie verwende ich das Azure BizTalk Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=302335
