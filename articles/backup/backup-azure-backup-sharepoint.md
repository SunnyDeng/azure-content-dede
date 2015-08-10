<properties
	pageTitle="DPM-Schutz der SharePoint-Farm in Azure | Microsoft Azure"
	description="Dieser Artikel enthält eine Übersicht über den DPM-Schutz der SharePoint-Farm in Azure."
	services="backup"
	documentationCenter=""
	authors="SamirMehta"
	manager="shreeshd"
	editor=""/>

\<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt\_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/14/2015" ms.author="sammehta"; "jimpark"/\>


# Sichern einer SharePoint-Farm in Azure
Die Vorgehensweise zum Sichern einer SharePoint-Farm mithilfe von System Center Data Protection Manager \(DPM\) ähnelt der Vorgehensweise zum Sichern anderer Datenquellen. Azure ermöglicht die Verwendung eines flexiblen Sicherungszeitplans, mit dem Sie tägliche, wöchentliche, monatliche oder jährliche Sicherungspunkte erstellen und verschiedene Aufbewahrungsrichtlinienoptionen für unterschiedliche Sicherungspunkte konfigurieren können. Mit DPM können Sie lokale Festplattenkopien speichern, um die Wiederherstellung zu beschleunigen. Außerdem können Sie Daten in Azure speichern, um eine kostengünstige und langfristige Aufbewahrungslösung zu erhalten.

## Von SharePoint unterstützte Versionen und entsprechende Sicherungsszenarien
Azure Backup für DPM unterstützt folgende Szenarien:

| Workload | Version | SharePoint-Bereitstellung | Art der DPM-Bereitstellung | DPM – System Center 2012 R2 | Schutz und Wiederherstellung |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| SharePoint | SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 | Bereitstellung von SharePoint als physischer Server oder als virtueller Hyper-V-/VmWare-Computer <br> -------------- <br> SQL AlwaysOn | Physischer Server oder lokaler virtueller Hyper-V-Computer | Unterstützt das Sichern in Azure über Updaterollup 5 | Schutz der SharePoint-Farm-Wiederherstellung: Farm, Datenbank, Datei oder Listenelement mittels Datenträger, Wiederherstellungsfarm und Datenbank von Azure |

## Vorbereitung
Vor dem Sichern einer SharePoint-Farm in Azure sind ein paar Vorbereitungen erforderlich.

### Voraussetzungen
Vergewissern Sie sich zunächst, dass für den Schutz von Workloads mit Microsoft Azure Backup alle [Voraussetzungen](backup-azure-dpm-introduction.md#prerequisites) erfüllt sind. Die Voraussetzungen umfassen Aufgaben wie das Erstellen eines Sicherungstresors, das Herunterladen von Tresoranmeldedaten, das Installieren des Azure Backup-Agents und das Registrieren des Servers beim Tresor.

### DPM-Agent
Der DPM-Agent muss auf dem SharePoint-Server, auf den Servern mit SQL Server sowie auf allen anderen Servern installiert werden, die der SharePoint-Farm angehören. Weitere Informationen zum Einrichten des Schutz-Agents finden Sie unter [Einrichten des Schutz-Agents](https://technet.microsoft.com/library/hh758039.aspx). Einzige Ausnahme: Der Agent wird nur auf einem einzelnen Web-Front-End \(WFE\)-Server installiert. Dieser fungiert dann als DPM-Einstiegspunkt für den Schutz.

### SharePoint-Farm
Pro zehn Millionen Farmelemente müssen mindestens 2 GB Speicherplatz auf dem Volume verfügbar sein, auf dem sich der DPM-Ordner befindet. Dieser Speicherplatz wird für die Kataloggenerierung benötigt. Für die Wiederherstellung bestimmter Elemente \(Websitesammlungen, Websites, Listen, Dokumentbibliotheken, Ordner, einzelne Dokumente und Listenelemente\) durch DPM erstellt die Kataloggenerierung eine Liste mit den URLs aus den einzelnen Inhaltsdatenbanken. Sie können die URL-Liste im Bereich für wiederherstellbare Elemente \(im Wiederherstellungsaufgabenbereich der DPM-Verwaltungskonsole\) anzeigen.

### SQL Server
DPM wird als lokales System ausgeführt. Zum Sichern von SQL Server-Datenbanken werden Systemadministratorberechtigungen für dieses Konto für den Server mit SQL Server benötigt. Legen Sie „NT-AUTORITÄT\\SYSTEM“ auf dem zu sichernden Server mit SQL Server auf *sysadmin* fest.

Falls Sie über SQL Server-Datenbanken verfügen, die mit SQL Server-Aliasen konfiguriert sind, installieren Sie in der SharePoint-Farm die SQL Server-Clientkomponenten auf dem Front-End-Webserver, der von DPM geschützt wird.

### SharePoint Server
Die Leistung hängt von zahlreichen Faktoren ab – unter anderem von der Größe der SharePoint-Farm. Grundsätzlich gilt: Mit einem einzelnen DPM-Server kann eine SharePoint-Farm mit einem Volumen von 25 TB geschützt werden.

### DPM-Updaterollup 5
Wenn Sie eine SharePoint-Farm in Azure schützen möchten, müssen Sie zunächst mindestens das DPM-Updaterollup 5 installieren. Mit dem Updaterollup 5 kann eine \(mit SQL AlwaysOn konfigurierte\) SharePoint-Farm in Azure geschützt werden. Weitere Informationen finden Sie unter [Installieren von DPM-Updaterollup 5](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx).

### Nicht unterstützte Funktionen
1. Der Schutz einer SharePoint-Farm erstreckt sich nicht auf Suchindizes oder Anwendungsdienstdatenbanken. Für diese Datenbanken muss der Schutz separat konfiguriert werden.
2. Von DPM werden keine SharePoint-SQL-Datenbanken gesichert, die sich auf Freigaben von Dateiservern mit horizontaler Skalierung \(Scale Out File Server, SOFS\) befinden.

## Konfigurieren des SharePoint-Schutzes
Konfigurieren Sie den SharePoint VSS Writer-Dienst \(WSS Writer-Dienst\) mithilfe von **ConfigureSharePoint.exe**, um SharePoint mit DPM schützen zu können.

Die Datei **ConfigureSharePoint.exe** befindet sich auf dem Front-End-Webserver im Verzeichnis „\[DPM-Installationspfad\]\\bin“. Dieses Tool stellt den Schutz-Agent mit den Anmeldeinformationen für die SharePoint-Farm bereit. Es wird auf einem einzelnen WFE-Server ausgeführt. Wählen Sie bei Verwendung mehrerer WFE-Server einen der Server aus, wenn Sie eine Schutzgruppe konfigurieren.

### So konfigurieren Sie den SharePoint VSS Writer-Dienst
1. Navigieren Sie an einer Eingabeaufforderung auf dem WFE-Server zu „\[DPM-Installationsort\]\\bin\\“.
2. Führen Sie „ConfigureSharePoint -EnableSharePointProtection“ aus.
3. Geben Sie die Administratoranmeldeinformationen für die Farm an. Dieses Konto muss der lokalen Administratorgruppe auf dem WFE-Server angehören. Wenn der Farmadministrator kein lokaler Administrator ist, erteilen Sie auf dem WFE-Server die folgenden Berechtigungen:
  - Erteilen Sie der Gruppe „WSS\_ADMIN\_WPG“ Vollzugriff auf den DPM-Ordner \(%Programm Files%\\Microsoft Data Protection Manager\\DPM\).
  - Erteilen Sie der Gruppe „WSS\_Admin\_WPG“ Lesezugriff auf den DPM-Registrierungsschlüssel \(HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft Data Protection Manager\).

>[AZURE.NOTE]Wenn sich die Administratoranmeldeinformationen für die SharePoint-Farm ändern, muss „ConfigureSharePoint.exe“ erneut ausgeführt werden.

## Sichern einer SharePoint-Farm mit DPM
Wenn Sie DPM und die SharePoint-Farm wie oben beschrieben konfiguriert haben, kann SharePoint von DPM geschützt werden.

### So schützen Sie eine SharePoint-Farm
1. Klicken Sie auf der Registerkarte **Schutz** der DPM-Administratorkonsole auf **Neu**. ![Neue Registerkarte „Schutz“](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)

2. Wählen Sie im Bildschirm **Schutzgruppentyp auswählen** des Assistenten **Neue Schutzgruppe erstellen** die Option **Server** aus, und klicken Sie anschließend auf **Weiter**.

    ![Schutzgruppentyp auswählen](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)

3. Aktivieren Sie im Bildschirm **Gruppenmitglieder auswählen** das Kontrollkästchen für den zu schützenden SharePoint-Server, und klicken Sie auf **Weiter**.

    ![Gruppenmitglieder auswählen](./media/backup-azure-backup-sharepoint/select-group-members2.png)

    >[AZURE.NOTE]Wenn der DPM-Agent installiert ist, wird der Server im Assistenten angezeigt. Darüber hinaus wird von DPM die Struktur angezeigt. Dank der Ausführung von „ConfigureSharePoint.exe“ kommuniziert DPM mit dem SharePoint VSS Writer-Dienst sowie mit den entsprechende SQL-Datenbanken und erkennt die Struktur der SharePoint-Farm \(die zugeordneten Inhaltsdatenbanken und alle dazugehörigen Elemente\).

4. Geben Sie im Bildschirm **Datenschutzmethode auswählen** den Namen der Schutzgruppe ein, und wählen Sie Ihre bevorzugten Schutzmethoden aus. Klicken Sie auf **Weiter**.

    ![Datenschutzmethode auswählen](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

    >[AZURE.NOTE]Die Datenträgerschutzoption trägt zur Verkürzung der Wiederherstellungszeiten bei. Im Gegensatz zu Bändern stellt Azure eine kostengünstige und langfristige Schutzlösung dar. Weitere Informationen finden Sie in [diesem Artikel](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/).

5. Wählen Sie im Bildschirm **Kurzfristige Ziele angeben** Ihre bevorzugte Beibehaltungsdauer aus, und geben Sie an, wann die Sicherungen durchgeführt werden sollen.

    ![Kurzfristige Ziele angeben](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

    >[AZURE.NOTE]Da in den meisten Fällen Daten wiederhergestellt werden, die weniger als fünf Tage alt sind, haben wir in diesem Beispiel eine datenträgerbasierte Beibehaltungsdauer von fünf Tagen ausgewählt und die Sicherung so konfiguriert, dass sie nicht während der Produktionszeiten erfolgt.

6. Überprüfen Sie den für die Schutzgruppe reservierten Speicherpool-Speicherplatz, und klicken Sie auf **Weiter**.

7. DPM reserviert für jede Schutzgruppe Speicherplatz zum Speichern und Verwalten von Replikaten. An diesem Punkt muss DPM eine Kopie der ausgewählten Daten erstellen. Wählen Sie aus, wie und wann das Replikat erstellt werden soll, und klicken Sie anschließend auf **Weiter**.

    ![Replikaterstellungsmethode auswählen](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

    >[AZURE.NOTE]Wählen Sie eine Zeit außerhalb der Produktionszeit, um eine Beeinträchtigung des Netzwerkdatenverkehrs zu vermeiden.

8. DPM prüft die Konsistenz des Replikats und stellt so die Datenintegrität sicher. Hierbei stehen zwei Optionen zur Verfügung: Sie können einen Zeitplan für die Ausführung von Konsistenzprüfungen definieren oder festlegen, dass die Konsistenz des Replikats automatisch überprüft werden soll, wenn es inkonsistent wird. Wählen Sie Ihre bevorzugte Option aus, und klicken Sie anschließend auf **Weiter**.

    ![Konsistenzprüfung](./media/backup-azure-backup-sharepoint/consistency-check.png)

9. Wählen Sie im Bildschirm **Online zu schützende Daten** die zu schützende SharePoint-Farm aus, und klicken Sie anschließend auf **Weiter**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)

10. Wählen Sie im Bildschirm zum Angeben des Zeitplans für die Onlinesicherung Ihren bevorzugten Zeitplan aus, und klicken Sie anschließend auf **Weiter**.

    ![Online\_backup\_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    >[AZURE.NOTE]Mit DPM können zweimal täglich Sicherungen in Azure durchgeführt werden.

11. Wählen Sie abhängig vom ausgewählten Sicherungszeitplan im Bildschirm zum Angeben der Onlineaufbewahrungsrichtlinie die gewünschte Aufbewahrungsrichtlinie für die täglichen, wöchentlichen, monatlichen und jährlichen Sicherungspunkte aus.

    ![Online\_retention\_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    >[AZURE.NOTE]Das Aufbewahrungsschema von DPM funktioniert nach dem Großvater-Vater-Sohn-Prinzip. Dabei kann für verschiedene Sicherungspunkte jeweils eine andere Beibehaltungsrichtlinie ausgewählt werden.

12. In Azure muss \(ähnlich wie bei einem Datenträger\) ein erstes Referenzpunktreplikat erstellt werden. Wählen Sie Ihre bevorzugte Option für die Erstellung der ersten Sicherungskopie in Azure, und klicken Sie anschließend auf **Weiter**.

    ![Online\_replica](./media/backup-azure-backup-sharepoint/online-replication.png)

13. Überprüfen Sie in der Zusammenfassung die ausgewählten Einstellungen, und klicken Sie anschließend auf **Gruppe erstellen**. Nach Erstellung der Schutzgruppe erscheint eine Erfolgsmeldung.

    ![Zusammenfassung](./media/backup-azure-backup-sharepoint/summary.png)

## Datenträgerbasiertes Wiederherstellen eines SharePoint-Elements mit DPM
Im folgenden Beispiel wurde *Recovering SharePoint item* versehentlich gelöscht und muss wiederhergestellt werden. ![DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Öffnen Sie die DPM-Verwaltungskonsole. Alle von DPM geschützten SharePoint-Farmen werden auf der Registerkarte „Schutz“ angezeigt.

    ![DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)

2. Wechseln Sie zum Wiederherstellen des Elements zunächst zur Registerkarte **Wiederherstellung**.

    ![DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)

3. Sie können SharePoint mithilfe einer platzhalterbasierten Suche innerhalb eines Wiederherstellungspunktbereichs nach *Recovering SharePoint item* durchsuchen.

    ![DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)

4. Wählen Sie in den Suchergebnissen den entsprechenden Wiederherstellungspunkt aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Wiederherstellen**.

5. Sie können auch durch andere Wiederherstellungspunkte navigieren und eine Datenbank oder ein Element für die Wiederherstellung auswählen. Wählen Sie **Datum \> Wiederherstellungszeit** und anschließend die korrekte Option für **Datenbank \> SharePoint-Farm \> Wiederherstellungspunkt \> Element**.

    ![DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)

6. Klicken Sie mit der rechten Maustaste auf das Element, und wählen Sie **Wiederherstellen**, um den Wiederherstellungs-Assistenten zu öffnen. Klicken Sie auf **Weiter**.

    ![Wiederherstellungsauswahl prüfen](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)

7. Wählen Sie den gewünschten Wiederherstellungstyp aus, und klicken Sie anschließend auf **Weiter**.

    ![Wiederherstellungstyp](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

    >[AZURE.NOTE]Das obige Beispiel zeigt die Wiederherstellung eines Elements in der ursprünglichen SharePoint-Site.

8. Wählen Sie den gewünschten Wiederherstellungsprozess aus.
    - Wählen Sie **Ohne Wiederherstellungsfarm wiederherstellen**, wenn sich die SharePoint-Farm nicht geändert hat und dem Wiederherstellungspunkt entspricht, der wiederhergestellt wird.
    - Wählen Sie **Mithilfe einer Wiederherstellungsfarm wiederherstellen**, wenn sich die SharePoint-Farm seit der Erstellung des Wiederherstellungspunkts geändert hat.

    ![Wiederherstellungsprozess](./media/backup-azure-backup-sharepoint/recovery-process.png)

9. Geben Sie einen SQL-Instanz-Stagingort für die vorübergehende Wiederherstellung der Datenbank und eine Stagingdateifreigabe auf dem DPM-Server und dem SharePoint-Server an, um das Element wiederherzustellen.

    ![Staging Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    DPM verknüpft die Inhaltsdatenbank, die als Host für das SharePoint-Element fungiert, mit der temporären SQL-Instanz. Der DPM-Server stellt das Element aus der Inhaltsdatenbank wieder her und platziert es am Stagingdateispeicherort auf dem DPM-Server. Das am Stagingort auf dem DPM-Server wiederhergestellte Element muss nun an den Stagingort in der SharePoint-Farm exportiert werden.

    ![Staging Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)

10. Wählen Sie **Wiederherstellungsoptionen angeben**, und wenden Sie Sicherheitseinstellungen auf die SharePoint-Farm oder die Sicherheitseinstellungen des Wiederherstellungspunkts an. Klicken Sie auf **Weiter**.

    ![Wiederherstellungsoptionen](./media/backup-azure-backup-sharepoint/recovery-options.png)

    >[AZURE.NOTE]Sie können auch die Nutzung der Netzwerkbandbreite drosseln. Dies minimiert die Auswirkungen auf den Produktionsserver während der Produktion.

11. Überprüfen Sie die Zusammenfassung, und klicken Sie anschließend auf **Wiederherstellen**, um die Wiederherstellung der Datei zu initiieren.

    ![Wiederherstellungszusammenfassung](./media/backup-azure-backup-sharepoint/recovery-summary.png)

12. Klicken Sie auf die Registerkarte **Überwachung** der DPM-Verwaltungskonsole, um den Status der Wiederherstellung anzuzeigen.

    ![Wiederherstellungsstatus](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    >[AZURE.NOTE]Die Datei ist nun wiederhergestellt. Sie können die SharePoint-Site aktualisieren, um die wiederhergestellte Datei zu überprüfen.

## Azure-basiertes Wiederherstellen einer SharePoint-Datenbank mit von DPM

1. Navigieren Sie zum Wiederherstellen einer SharePoint-Inhaltsdatenbank durch verschiedene Wiederherstellungspunkte \(siehe oben\), und wählen Sie den wiederherzustellenden Wiederherstellungspunkt aus.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)

2. Doppelklicken Sie auf den SharePoint-Wiederherstellungspunkt, um die verfügbaren SharePoint-Kataloginformationen anzuzeigen.

    > [AZURE.NOTE]Da die SharePoint-Farm mit langfristiger Aufbewahrung in Azure geschützt ist, sind auf dem DPM-Server keine Kataloginformationen \(Metadaten\) verfügbar. Wenn also eine Zeitpunktwiederherstellung einer SharePoint-Inhaltsdatenbank erforderlich ist, muss die SharePoint-Farm neu katalogisiert werden.

3. Klicken Sie auf die entsprechende Option.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Das Fenster für die Neukatalogisierung der Cloud erscheint.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nach Abschluss der Katalogisierung ändert sich der Status in *Erfolgreich*. Klicken Sie auf **Schließen**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)

4. Klicken Sie auf das SharePoint-Objekt, das auf der DPM-Registerkarte **Wiederherstellung** angezeigt wird, um die Struktur der Inhaltsdatenbank abzurufen. Klicken Sie mit der rechten Maustaste, und wählen Sie **Wiederherstellen**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)

5. Führen Sie nun die [weiter oben beschriebenen Wiederherstellungsschritte für die datenträgerbasierte Wiederherstellung einer SharePoint-Inhaltsdatenbank](#restore-a-sharepoint-item-from-disk-using-dpm) aus.

## Häufig gestellte Fragen
F: Welche Versionen von DPM unterstützen SQL 2014 und SQL 2012 \(SP2\)?<br> A: DPM 2012 R2 mit Updaterollup 4

F: Kann ich ein SharePoint-Element am ursprünglichen Speicherort wiederherstellen, wenn SharePoint mit SQL AlwaysOn \(und datenträgerbasiertem Schutz\) konfiguriert wird?<br> A: Ja, das Element kann in der ursprünglichen SharePoint-Site wiederhergestellt werden.

F: Kann ich eine SharePoint-Datenbank am ursprünglichen Speicherort wiederherstellen, wenn SharePoint mit SQL AlwaysOn konfiguriert wird?<br> A: Da SharePoint-Datenbanken in SQL AlwaysOn konfiguriert werden, können sie nur geändert werden, wenn die Verfügbarkeitsgruppe \(Availability Group, AG\) entfernt wird. DPM kann die Datenbank daher nicht am ursprünglichen Speicherort wiederherstellen. Die SQL-Datenbank kann in einer anderen SQL-Instanz wiederhergestellt werden.

## Nächste Schritte
- Weitere Informationen zum DPM-Schutz von SharePoint finden Sie in der dazugehörigen [Videoreihe](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group).
- [Versionsanmerkungen für System Center 2012 – Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
- [Versionsanmerkungen für Data Protection Manager in System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

<!---HONumber=July15_HO5-->