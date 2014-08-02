<properties linkid="biztalk-backup-restore" urlDisplayName="BizTalk Services: Backup and Restore" pageTitle="BizTalk Services: Backup and Restore | Azure" metaKeywords="" description="BizTalk Services includes Backup and Restore capabilities. When creating a Backup, a snapshot of the BizTalk Services configuration is taken." metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Backup and Restore" authors="" solutions="" writer="mandia" manager="paulettm" editor="cgronlun" />

BizTalk Services: Sichern und Wiederherstellen
==============================================

Azure BizTalk Services bieten eine Sicherungs- und Wiederherstellungsfunktion. Bei der Erstellung einer Sicherungskopie wird eine Momentaufnahme der gegenwärtigen Konfiguration der Azure BizTalk Services erzeugt.

Stellen Sie sich die folgenden Szenarien vor:

-   Sie können die Konfiguration von BizTalk Services auf zwei Arten über das Azure-Verwaltungsportal ändern: durch Erstellung von Sicherungskopien bei Bedarf (Ad-Hoc-Backups) und durch Ausführung geplanter Sicherungen.

-   Gesicherte Inhalte können im selben BizTalk Service oder in einem neuen BizTalk Service wiederhergestellt werden. Wenn Sie den BizTalk Service unter demselben Namen wiederherstellen möchten, muss der vorhandene BizTalk Service gelöscht werden. Andernfalls schlägt die Wiederherstellung fehl.

-   BizTalk Services können in derselben Edition oder in einer höheren Edition wiederhergestellt werden. Die Wiederherstellung von BizTalk Services in einer früheren Edition als derjenigen, von der die Sicherungskopie erstellt wurde, wird nicht unterstützt.

    Eine Sicherungskopie der Basic Edition kann z. B. auf die Premium Edition wiederhergestellt werden. Aber eine Sicherungskopie der Premium Edition kann nicht auf die Standard Edition wiederhergestellt werden.

-   Die EDI-Kontrollnummern werden gesichert, um ihre Kontinuität aufrechtzuerhalten. Wenn Nachrichten nach der letzten Sicherung verarbeitet werden, kann es bei der Wiederherstellung dieses Sicherungsinhalts zu doppelten Kontrollnummern kommen.

-   Die Sicherung und Wiederherstellung ist nicht in der BizTalk Server Developer Edition enthalten.

In diesem Thema wird beschrieben, wie BizTalk Services mithilfe des Azure-Verwaltungsportals gesichert und wiederhergestellt werden. Sie können BizTalk Services auch mithilfe von REST-APIs wiederherstellen. Weitere Informationen dazu finden Sie unter [BizTalk Services REST API (REST-APIs für BizTalk Services, in englischer Sprache)](http://msdn.microsoft.com/library/windowsazure/dn232347.aspx).

[Was wird gesichert?](#budata)

[Erstellen einer Sicherungskopie](#createbu)

[Wiederherstellen](#restore)

Was wird gesichert?
-------------------

Wenn eine Sicherungskopie erstellt wird, werden die folgenden Elemente gesichert:


<table border="1"> 
<TR bgcolor="FAF9F9">
<th> </th>
<TH>Elemente, die gesichert werden</TH> 
</TR> 
<TR>
<td colspan="2">
 <strong>Azure BizTalk Services-Portal</strong></td>
</TR> 
<TR>
<TD>Konfiguration und Laufzeit</TD> 
<TD><bl>
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
</bl></TD>
</TR> 
 
<TR>
<td colspan="2">
 <strong>Azure BizTalk Service</strong></td>
</TR> 
<TR>
<TD>SSL-Zertifikat</TD> 
<TD>
<bl>
<li>SSL-Zertifikatdaten</li>
<li>SSL-Zertifikatkennwort</li>
</bl>
</TD>
</TR> 
<TR>
<TD>BizTalk Service-Einstellungen</TD> 
<TD>
<bl>
<li>Skalierungseinheitenzählung</li>
<li>Edition</li>
<li>Produktversion</li>
<li>Region/Rechenzentrum</li>
<li>Access Control Service (ACS)-Namespace und -Schlüssel</li>
<li>Nachverfolgungsdatenbank-Verbindungszeichenfolgen</li>
<li>Archivierung von Speicherkonto-Verbindungszeichenfolgen</li>
<li>Überwachung von Speicherkonto-Verbindungszeichenfolgen</li>
</bl></TD>
</TR> 
<TR>
<td colspan="2">
 <strong>Zusätzliche Elemente</strong></td>
</TR> 
<TR>
<TD>Nachverfolgungsdatenbank</TD> 
<TD>Bei der Bereitstellung des BizTalk Services werden die Details zur Nachverfolgungsdatenbank (der Azure SQL-Datenbank-Server und der Name der Nachverfolgungsdatenbank) eingegeben. Die Nachverfolgungsdatenbank wird nicht automatisch gesichert.<br/><br/>
<strong>Wichtig</strong><br/>
Wenn die Nachverfolgungsdatenbank versehentlich gelöscht wird und wiederhergestellt werden soll, muss bereits eine vorherige Sicherungskopie existieren. Wenn keine Sicherungskopie existiert, können die Nachverfolgungsdatenbank und ihre Daten nicht wiederhergestellt werden. Erstellen Sie in diesem Fall eine neue Nachverfolgungsdatenbank mit demselben Datenbanknamen. Georeplikation wird empfohlen.</TD>
</TR> 
</table>






Erstellen einer Sicherungskopie
-------------------------------

Eine Sicherungskopie kann jederzeit erstellt werden und ist komplett benutzergesteuert. Sie können eine Sicherungskopie entweder über das Azure-Verwaltungsportal oder über die REST-API für BizTalk Services erstellen. Informationen zur Erstellung einer Sicherungskopie mithilfe der REST-API für BizTalk Services finden Sie unter [Backup BizTalk Service (Sichern von BizTalk Services, in englischer Sprache](http://go.microsoft.com/fwlink/p/?LinkID=325584)

In diesem Abschnitt erhalten Sie Informationen zur Erstellung von Sicherungskopien mithilfe des Verwaltungsportals. Sie können das Verwaltungsportal verwenden, um Ad-Hoc-Sicherungskopien zu erstellen oder Sicherungskopien in gewünschten Intervallen zu planen.

Vor dem Erstellen einer Sicherungskopie
---------------------------------------

Bitte beachten Sie Folgendes, bevor Sie eine Sicherungskopie erstellen:

1.  Bevor Sie eine Ad-Hoc-Sicherung durchführen, verarbeiten Sie den Stapel aktiver Nachrichten. Dies hilft, Nachrichtenverlust zu vermeiden, *wenn* diese Sicherungskopie wiederhergestellt wird. Nachrichten in Stapeln werden bei der Erstellung einer Sicherungskopie niemals gespeichert. Wenn Sie geplante Sicherungen durchführen, können Sie nicht sicher sein, dass sich keine Nachrichten im Stapel befinden, wenn die Sicherung beginnt.

    **Hinweis**

    Wenn eine Sicherung mit aktiven Nachrichten in einem Stapel durchgeführt wird, werden diese Nachrichten nicht gesichert und gehen deshalb verloren.
2.  Optional: Stoppen Sie alle eventuellen Verwaltungsvorgänge im BizTalk Services-Portal.
3.  Erstellen Sie die Sicherungskopie im Speicherkonto mithilfe der in der REST-API verfügbaren Befehle zum [Sichern von BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=325584).

[Ad-Hoc-Sicherungen](#backupnow)

[Planen einer Sicherung](#backupschedule)

### Ad-Hoc-Sicherungen

1.  Klicken Sie im Azure-Verwaltungsportal auf "BizTalk Services" und anschließend auf den Namen des BizTalk-.Dienstes, den Sie sichern möchten.
2.  Klicken Sie unten auf der Registerkarte **Dashboard** des BizTalk Service auf **Back up**.
3.  Geben Sie im Dialogfeld **Back up BizTalk Service** einen Namen für die Sicherungskopie an.
4.  Wählen Sie ein Blob-Speicherkonto aus, und klicken Sie auf das Häkchen, um mit der Sicherung zu beginnen.

Sobald die Sicherung abgeschlossen ist, wird unter dem Speicherkonto ein Container mit dem von Ihnen gewählten Sicherungsnamen erstellt. Dieser Container enthält die Sicherungskonfiguration Ihres BizTalk Service.

### Planen einer Sicherung

1.  Klicken Sie im Azure-Verwaltungsportal auf "BizTalk Services", klicken Sie auf den Namen des BizTalk Service, für den Sie automatisierte Sicherungen planen möchten, und klicken Sie anschließend auf die Registerkarte **Konfigurieren**.
2.  Klicken Sie unter **Backup Status** auf **Keine**, wenn Sie keine automatisierten Sicherungen planen möchten. Wenn Sie automatisierte Sicherungen planen möchten, klicken Sie auf **Automatisch**.
3.  Wählen Sie unter **Speicherkonto** ein Azure-Speicherkonto, in dem die Sicherungen erstellt werden sollen.
4.  Geben Sie unter **Frequenz** das Startdatum und die Uhrzeit für das erste Backup sowie das Intervall (in Tagen) ein, in dem Sicherungen durchgeführt werden sollen.
5.  Geben Sie unter **Retention Days** ein Zeitfenster (in Tagen) ein, in dem die Sicherungskopien aufbewahrt werden sollen. Die Aufbewahrungszeit muss länger sein als die Sicherungsfrequenz.
6.  Markieren Sie das Kontrollkästchen **Always keep at least one backup**, um mindestens eine Sicherungskopie zu speichern, auch wenn die Aufbewahrungszeit überschritten ist.
7.  Klicken Sie auf **Speichern**.

Wenn ein geplanter Sicherungsjob ausgeführt wird, wird im von Ihnen angegebenen Speicherkonto ein Container zum Speichern der Sicherungsdateien erstellt. Der Name des Containers hat das Format *BizTalk Service-Name-Datum-Zeit*.

Wenn eine Sicherung fehlschlägt, wird der Status der Sicherung auf der Seite "Dashboard" der BizTalk Services als **Fehlgeschlagen** angezeigt.

![Status der letzten geplanten Sicherung](./media/biztalk-backup-restore/status-last-backup.png)

Wenn Sie mehr Informationen zu diesem Fehler erhalten möchten, klicken Sie auf den Link, um zur Seite "Management Services Operation Logs" zu gelangen. Weitere Informationen über Operationsprotokolle für BizTalk Services erhalten Sie unter [BizTalk Services: Troubleshoot using operation logs (BizTalk Services: Fehlerbehebung mit Operationsprotokollen, in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=391211).

Wiederherstellen
----------------

Sie können eine Sicherungskopie entweder über das Azure-Verwaltungsportal oder über die REST-API für BizTalk Services wiederherstellen. In diesem Abschnitt erhalten Sie Informationen zur Wiederherstellung mithilfe des Verwaltungsportals. Informationen zur Wiederherstellung mithilfe der REST-API finden Sie unter [Restore BizTalk Service from Backup (Wiederherstellen von BizTalk Services aus einer Sicherungskopie, in englischer Sprache)](http://go.microsoft.com/fwlink/p/?LinkID=325582).

### Vor dem Wiederherstellen einer Sicherungskopie

Bitte beachten Sie Folgendes, wenn Sie eine Sicherungskopie wiederherstellen:

-   Bei der Wiederherstellung eines BizTalk Service können neue Stores für Verfolgung, Archivierung und Überwachung festgelegt werden.

-   Wenn Sie den BizTalk Service mit demselben Namen wiederherstellen möchten, löschen Sie den existierenden BizTalk Service, bevor Sie mit der Wiederherstellung beginnen. Andernfalls schlägt die Wiederherstellung fehl.

-   Die EDI-Laufzeitdaten werden wiederhergestellt. Die Kontrollnummern werden in der EDI-Laufzeit-Sicherungskopie gespeichert. Die wiederhergestellten Kontrollnummern haben dieselbe Reihenfolge wie zum Zeitpunkt der Sicherung. Wenn Nachrichten nach der letzten Sicherung verarbeitet werden, kann es bei der Wiederherstellung dieses Sicherungsinhalts zu doppelten Kontrollnummern kommen.

#### Wiederherstellen einer Sicherungskopie

1.  Klicken Sie im Azure-Verwaltungsportal auf **Neu**, zeigen Sie auf **App-Dienste** und **BizTalk Service**, und klicken Sie anschließend auf **Wiederherstellen**.

    ![Wiederherstellen einer Sicherung](./media/biztalk-backup-restore/restore-ui.png)

2.  Klicken Sie im Assistenten "New BizTalk Service - Restore" auf der Seite **Restore BizTalk Service** auf das Ordner-Symbol im Textfeld **Backup URL**, um das Dialogfeld **Browse Cloud Storage** zu öffnen. In diesem Dialogfeld werden Ihre Azure-Speicherkonten aufgelistet.

    Erweitern Sie das Speicherkonto, das Sie beim Erstellen oder Planen der Sicherung angegeben haben, und wählen Sie dann den Namen des Containers, aus dem Sie die BizTalk Service-Konfiguration wiederherstellen möchten.

    Wählen Sie im rechten Bereich die .txt-Datei für die Sicherungskopie, aus der Sie die Wiederherstellung vornehmen, und klicken Sie anschließend auf **Öffnen**.

3.  Geben Sie auf der Seite **Restore BizTalk Service** die folgenden Informationen ein:
    -   Geben Sie einen Namen für den BizTalk Service ein. Standardmäßig wird der Name des gesicherten BizTalk Service verwendet.
    -   Prüfen Sie die Domänen-URL, die Edition und die Region des wiederhergestellten BizTalk Service.
    -   Wählen Sie für die Nachverfolgungsdatenbank eine neue SQL-Datenbankinstanz, und klicken Sie auf den Pfeil nach rechts.


4.  Überprüfen Sie auf der Seite **Specify database settings** den Namen der SQL-Datenbank, und geben Sie den physischen Server, auf dem die SQL-Datenbank erstellt wird, sowie einen Benutzernamen/ein Kennwort für diesen Server an.

    Wenn Sie erweiterte Einstellungen für die SQL-Datenbank konfigurieren möchten, markieren Sie das Kontrollkästchen **Configure Advanced Database Settings**, und klicken Sie auf den Pfeil nach rechts.

    Wenn Sie keine erweiterten Datenbankeinstellungen konfigurieren möchten, klicken Sie auf den Pfeil nach rechts, und springen Sie unten zu Schritt 6.

5.  Wählen Sie auf der Seite **Advanced database settings** die Datenbank-Edition, die Sie verwenden möchten (**Web** oder **Business**), und geben Sie die maximale Datenbankgröße sowie die Sortierungsregeln an. Klicken Sie auf den Pfeil nach rechts.

6.  Erstellen Sie auf der Seite **Specify monitoring/archiving settings** ein neues Speicherkonto, oder geben Sie ein vorhandenes Speicherkonto an, in dem die Informationen zur BizTalk Service-Überwachung gespeichert werden.

7.  Klicken Sie auf das Häkchen, um den Wiederherstellungsprozess zu starten</b>.

8.  Wenn die Wiederherstellung erfolgreich abgeschlossen wurde, wird auf der Seite "BizTalk Services" im Azure-Verwaltungsportal ein neuer BizTalk Service im Standby-Status angezeigt.

### Nach dem Wiederherstellen einer Sicherungskopie

BizTalk Services werden immer im **Standby**-Status wiederhergestellt. Dies versetzt Sie in die Lage, in Ihren BizTalk Service-Anwendungen sämtliche gewünschten Konfigurationsänderungen durchzuführen, bevor die neue Umgebung funktionsfähig ist. Es folgen einige Punkte, die Sie beachten sollten, bevor Sie einen wiederhergestellten BizTalk Service starten:

-   Wenn Sie BizTalk Service-Anwendungen mithilfe des Azure BizTalk Services SDK erstellt haben, müssen Sie eventuell die ACS-Anmeldeinformationen in diesen Anwendungen aktualisieren, damit diese in der wiederhergestellten Umgebung funktionieren können.

-   Sie können einen BizTalk Service wiederherstellen, um eine bereits in Betrieb befindliche BizTalk Service-Umgebung zu replizieren. Wenn Sie im ursprünglichen BizTalk Service-Portal Verträge konfiguriert haben, die FTP-Shares als Quellen verwenden, könnte es erforderlich sein, die Verträge in der wiederhergestellten Umgebung zu aktualisieren, um andere Quellen oder FTP-Shares zu verwenden. Wenn Sie dies nicht tun, kann es sein, dass zwei verschiedene Verträge versuchen, dieselbe Nachricht abzurufen.

-   Wenn Sie den Wiederherstellungsvorgang verwendet haben, um mehrere BizTalk Service-Umgebungen laufen zu lassen, stellen Sie sicher, dass Sie mit Visual Studio-Anwendungen, PowerShell-Cmdlets, REST-APIs oder Trading Partner Management OM-APIs die richtige Umgebung anzielen.

-   Es empfiehlt sich auch, automatisierte Sicherungen der wiederhergestellten BizTalk Service-Umgebung zu konfigurieren.

Wenn Sie diese oder ähnliche Überlegungen durchdacht haben, gehen Sie im Azure-Verwaltungsportal auf die Seite "BizTalk Service", wählen Sie den wiederhergestellten BizTalk Service im Standby-Status aus, und klicken Sie unten auf der Seite auf **Resume**, um den Service zu starten.

Nächste Schritte
----------------

Um Azure BizTalk Services im Azure-Verwaltungsportal bereitzustellen, wechseln Sie zu [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280). Wenn Sie mit dem Erstellen von Anwendungen beginnen möchten, wechseln Sie zu [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

Weitere Informationen
---------------------

-   [Backup BizTalk Service (Sichern von BizTalk Services, in englischer Sprache)](http://go.microsoft.com/fwlink/p/?LinkID=325584)
-   [Restore BizTalk Service from Backup (Wiederherstellen von BizTalk Services aus Sicherungskopien, in englischer Sprache)](http://go.microsoft.com/fwlink/p/?LinkID=325582)
-   [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [BizTalk Services: Bereitstellungsstatusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Wie verwende ich das Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

