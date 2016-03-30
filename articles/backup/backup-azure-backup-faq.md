<properties
   pageTitle="Häufig gestellte Fragen zu Azure Backup | Microsoft Azure"
   description="Hier finden Sie Antworten auf häufig gestellte Fragen zum Backup-Agent, zu Sicherung und Aufbewahrung, Wiederherstellung, Sicherheit und auf andere allgemeine Fragen zur Azure Backup-Lösung."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="Backup-Lösung; Backup-Dienst"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="03/23/2016"
	 ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# Azure Backup-Dienst – FAQ
Dieser Artikel enthält eine Liste mit häufig gestellten Fragen (und den dazugehörigen Antworten) zum Azure Backup-Dienst. In unserer Community erhalten Sie schnell eine Antwort, und wenn eine Frage häufig gestellt wird, fügen wir sie diesem Artikel hinzu. Die Antworten auf Fragen enthalten in der Regel Referenz- bzw. Supportinformationen. Sie können im Disqus-Abschnitt dieses Artikels oder eines verwandten Artikels Fragen zu Azure Backup stellen. Außerdem können Sie Fragen zum Azure Backup-Dienst im [Diskussionsforum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup) stellen.

## Installation und Konfiguration
**F1. Welche Betriebssysteme werden für die Sicherung in Azure mithilfe von Azure Backup unterstützt?** <br/> A1. Folgende Betriebssysteme werden von Azure Backup unterstützt:


| Betriebssystem | Plattform | SKU |
| :------------- |-------------| :-----|
| Windows 8 und die neuesten SPs | 64-Bit | Enterprise, Pro |
| Windows 7 und die neuesten SPs | 64-Bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 und die neuesten SPs | 64-Bit | Enterprise, Pro |
| Windows 10 | 64-Bit | Enterprise, Pro, Home |
|Windows Server 2012 R2 und die neuesten SPs|	64-Bit|	Standard, Datacenter, Foundation|
|Windows Server 2012 und die neuesten SPs|	64-Bit|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 und die neuesten SPs |64-Bit|	Standard, Workgroup|
|Windows Storage Server 2012 und neuesten SPs |64-Bit |Standard, Workgroup
|Windows Server 2012 R2 und die neuesten SPs |64-Bit|	Essential|
|Windows Server 2008 R2 SP1 |64-Bit|	Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2 |64-Bit|	Standard, Enterprise, Datacenter, Foundation|

**F2. Wo kann ich den neuesten Azure Backup-Agent herunterladen?** <br/> A2. Sie können den aktuellen Agent zum Sichern von Windows Server, System Center DPM oder des Windows-Clients [hier](http://aka.ms/azurebackup_agent) herunterladen. Verwenden Sie zum Sichern eines virtuellen Computers den VM-Agent (installiert automatisch die richtige Erweiterung). Der VM-Agent ist auf virtuellen Computern, die über den Azure-Katalog erstellt werden, bereits vorhanden.

**F3. Welche SCDPM-Serverversion wird unterstützt?** <br/> A3. Wir empfehlen, den [aktuellen](http://aka.ms/azurebackup_agent) Azure Backup-Agent aus dem aktuellen Updaterollup von SCDPM (UR6; Stand: Juli 2015) zu installieren.

****F4. Beim Konfigurieren des Azure Backup-Agents werde ich aufgefordert, die **Tresoranmeldedaten** einzugeben. Laufen die Tresoranmeldeinformationen ab? A4. Ja. Die Tresoranmeldeinformationen laufen nach 48 Stunden ab. Wenn die Datei abläuft, melden Sie sich beim Azure-Portal an, und laden Sie die Dateien mit den Tresoranmeldedaten aus Ihrem Azure Backup-Tresor herunter.

**F5. Gibt es eine Beschränkung hinsichtlich der Anzahl von Sicherungstresoren, die in einem Azure-Abonnement erstellt werden können?** <br/> A5. Ja. Sie können pro Abonnement 25 Tresore erstellen (Stand: Juli 2015). Erstellen Sie ein neues Abonnement, wenn Sie weitere Tresore benötigen.

**F6. Kann ich einen Tresor als Abrechnungseinheit betrachten?** <br/> A6. Zwar besteht die Möglichkeit, eine detaillierte Rechnung für jeden Tresor zu erhalten, es empfiehlt sich aber unbedingt, ein Azure-Abonnement als Abrechnungseinheit zu verwenden. Es ist für alle Dienste konsistent und einfacher zu verwalten.

**F7. Gibt es Beschränkungen im Hinblick auf die Anzahl von Servern/Computern, die pro Tresor registriert werden können?** <br/> A7. Ja. Sie können bis zu 50 Computer pro Tresor registrieren. Bei virtuellen Azure-IaaS-Computern liegt die Beschränkung bei 200 VMs pro Tresor. Wenn Sie weitere Computer registrieren müssen, erstellen Sie einen neuen Tresor.

**F8. Gibt es Beschränkungen für die Datenmenge, die von einem Windows-Server/-Client oder einem SCDPM-Server gesichert werden kann?** <br/> A8. Nein.

**F9. Wie registriere ich meinen Server bei einem anderen Datencenter?**<br/> A9. Die Sicherungsdaten werden an das Rechenzentrum des Backup-Diensts gesendet, bei dem dieser registriert ist. Die einfachste Methode zum Ändern des Datencenters besteht darin, den Agent zu deinstallieren, neu zu installieren und bei einem neuen Datencenter zu registrieren.

**F10. Was geschieht, wenn ich einen Windows-Server umbenenne, der Daten in Azure sichert?** A10. Wenn Sie einen Server umbenennen, werden alle derzeit konfigurierten Sicherungen angehalten. Sie müssen den neuen Namen des Servers beim Backup-Tresor registrieren. Da es sich um eine neue Registrierung handelt, ist der erste Sicherungsvorgang eine vollständige Sicherung und keine inkrementelle Sicherung. Falls Sie Daten wiederherstellen müssen, die bisher unter dem alten Servernamen im Tresor gesichert waren, können Sie hierfür die Option [**Anderer Server**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) im Assistenten **Daten wiederherstellen** nutzen.


**F11. Von welchen Laufwerkstypen kann ich Dateien und Ordner sichern?** <br/> A11. Die folgenden Laufwerke/Volumes können nicht als Backup dienen:

- Wechselmedien: Das Laufwerk muss als feststehendes Laufwerk gelten, um als Quelle für Backup-Elemente verwendet zu werden.
- Schreibgeschützte Volumes: Das Volume muss beschreibbar sein, damit der Volumeschattenkopie-Dienst funktioniert.
- Offlinevolumes: Das Volume muss online sein, damit der VSS funktioniert.
- Netzwerkfreigabe: Das Volume muss sich lokal auf dem Server befinden, um mit der Onlinesicherung gesichert zu werden.
- Mit BitLocker geschützte Volumes: Das Volume muss entsperrt werden, bevor die Sicherung durchgeführt werden kann.
- Dateisystemidentifizierung: Für diese Version des Onlinesicherungsdiensts wird ausschließlich NTFS unterstützt.

**F12. Welche Datei- und Ordnertypen können von meinem Server gesichert werden?**<br/> A12. Die folgenden Typen werden unterstützt:

- Verschlüsselt
- Komprimiert
- Mit geringer Dichte
- Komprimiert und geringe Dichte
- Feste Links: Nicht unterstützt, werden übersprungen
- Analysepunkt: Nicht unterstützt, wird übersprungen
- Verschlüsselt und komprimiert: Nicht unterstützt, wird übersprungen
- Verschlüsselt und geringe Dichte: Nicht unterstützt, wird übersprungen
- Komprimierter Stream: Nicht unterstützt, wird übersprungen
- Stream mit geringer Dichte: Nicht unterstützt, wird übersprungen

**F13. Welche Mindestgröße gilt für den Cacheordner?** <br/> A13. Die Größe des Cacheordners bestimmt die Menge der Daten, die Sie sichern. Der Cacheordner sollte 5% des Speicherplatzes umfassen, der für die Datenspeicherung erforderlich ist.

**F14. Wenn meine Organisation über einen Sicherungstresor verfügt, wie kann ich beim Wiederherstellen der Daten dann die Daten eines Servers von einem anderen Server trennen?**<br/> A14. Alle für denselben Tresor registrierten Server können die Daten wiederherstellen, die von anderen Servern *mit derselben Passphrase* gesichert wurden. Wenn Sie Server verwenden, deren Sicherungsdaten Sie von anderen Servern Ihrer Organisation isolieren möchten, sollten Sie für diese Server eine speziell dafür vorgesehene Passphrase nutzen. So können Sie beispielsweise für die Server der Personalabteilung, für die Server der Buchhaltung und für die Speicherserver jeweils eine eigene Verschlüsselungspassphrase verwenden.

**F15. Kann ich meine Sicherungsdaten zwischen den Abonnements "migrieren"?** <br/> A15: Nein

**F16. Kann ich meinen Sicherungstresor zwischen Abonnements "migrieren"?** <br/> A16: Nein. Der Tresor wird auf einer Abonnementstufe erstellt und kann nach seiner Erstellung keinem anderen Abonnement zugewiesen werden.

**F17. Funktioniert der Azure Backup-Agent auf einem Server, der die Deduplizierung von Windows Server 2012 verwendet?** <br/> A17: Ja. Der Agent-Dienst konvertiert die deduplizierten Daten bei der Vorbereitung des Sicherungsvorgangs in normale Daten. Anschließend optimiert er die Daten für die Sicherung, verschlüsselt sie und sendet die verschlüsselten Daten an den Onlinesicherungsdienst.

**F18. Werden die übertragenen Sicherungsdaten gelöscht, wenn ich einen Sicherungsauftrag nach dem Starten abbreche?** <br/> A18: Nein. Der Sicherungstresor speichert die übertragenen Sicherungsdaten, die bis zum Zeitpunkt des Abbruchs übertragen wurden. Azure Backup nutzt einen Prüfpunktmechanismus, um den Sicherungsdaten während des Sicherungsvorgangs von Zeit zu Zeit Prüfpunkte hinzuzufügen. Da in den Sicherungsdaten Prüfpunkte vorhanden sind, kann der nächste Sicherungsprozess die Integrität der Dateien überprüfen. Die nächste ausgelöste Sicherung ist eine inkrementelle Sicherung, die auf dem vorherigen Sicherungsvorgang aufbaut. Eine inkrementelle Sicherung sorgt für eine bessere Nutzung der Bandbreite, da nicht mehrfach die gleichen Daten übertragen werden müssen.

**F19. Warum wird eine Warnung mit dem Hinweis angezeigt, dass für diesen Server keine Azure-Sicherungen konfiguriert wurden, obwohl ich zuvor regelmäßige Sicherungen geplant habe?** <br/> A19: Diese Warnung kann auftreten, wenn die auf dem lokalen Server gespeicherten Sicherungszeitplaneinstellungen nicht den Einstellungen im Sicherungstresor entsprechen. Wenn für den Server oder die Einstellungen ein als funktionierend bekannter Zustand wiederhergestellt wurde, ist unter Umständen die Synchronisierung der Sicherungszeitpläne verloren gegangen. Wenn Sie diese Warnung erhalten, sollten Sie die [Sicherungsrichtlinie neu konfigurieren](backup-azure-backup-windows-server.md) und dann **Sicherung jetzt ausführen** verwenden, um den lokalen Server wieder mit Azure zu synchronisieren.

**F20. Welche Firewallregeln sollten für Azure Backup konfiguriert werden?** <br/> A20. Zur Erzielung eines nahtlosen Schutzes der Daten vom Typ „lokal zu Azure“ und „Workload zu Azure“ wird empfohlen, für die Firewall die Kommunikation mit den folgenden URLs zuzulassen:

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

**F21. Kann ich den Azure Backup-Agent auf einer Azure-VM installieren, die mithilfe der VM-Erweiterung bereits vom Azure Backup-Dienst gesichert wurde?** <br/> A21. Absolut. Azure Backup ermöglicht die Sicherung auf VM-Ebene für Azure VMs mit der VM-Erweiterung. Sie können den Azure Backup-Agent auf einem Windows-Gastbetriebssystem installieren, um Dateien und Ordner auf dem Gastbetriebssystem zu schützen.

**F22. Kann ich den Azure Backup-Agent auf einer Azure-VM installieren, um Dateien und Ordner in temporärem Speicher zu schützen, der von der Azure-VM bereitgestellt wird?** <br/> A22. Sie können den Azure Backup-Agent auf dem Windows-Gastbetriebssystem installieren und Dateien und Ordner im temporären Speicher sichern. Beachten Sie aber, dass die Sicherungen nicht erfolgreich durchgeführt werden, nachdem die temporären Speicherdaten entfernt wurden. Wenn die temporären Speicherdaten gelöscht wurden, ist außerdem nur die Wiederherstellung in einem nicht volatilen Speicher möglich.

**F23. Wie lang darf der Dateipfad sein, der im Rahmen der Azure Backup-Richtlinie mit dem Azure Backup-Agent angegeben werden kann?** <br/> A23. Der Azure Backup-Agent basiert auf NTFS. Die [Angabe der Dateipfadlänge ist durch die Windows-API beschränkt](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Wenn die Dateipfade von gesicherten Dateien länger als die Vorgabe der Windows-API sind, können Kunden auch den übergeordneten Ordner oder den Datenträger mit den Sicherungsdateien für die Sicherung angeben.

**F24. Welche Zeichen sind im Dateipfad der Azure Backup-Richtlinie mit dem Azure Backup-Agent zulässig?** <br/> A24. Der Azure Backup-Agent basiert auf NTFS. Für die Angabe der Datei können [von NTFS unterstützte Zeichen](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) verwendet werden.


## Sicherung und Aufbewahrung
**F1. Gibt es eine Größenbeschränkung für die einzelnen Datenquellen, die gesichert werden?** <br/> A1. Ab August 2015 beträgt die maximale Größe der Datenquelle für die unterstützten Betriebssysteme:

|Nr. |	Betriebssystem |	Maximale Größe der Datenquelle |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 oder höher| 54\.400 GB|
|2| Windows 8 oder höher| 54\.400 GB|
|3| Windows Server 2008, Windows Server 2008 R2 | 1\.700 GB|
|4| Windows 7 | 1\.700 GB|

In der folgenden Tabelle wird beschrieben, wie die einzelnen Datenquellengrößen bestimmt werden.

|	Datenquelle |	Details |
| :-------------: |:-------------|
|Volume |Die Datenmenge, die von einem Volume eines Server- oder Clientcomputers gesichert wird|
|Virtueller Hyper-V-Computer | Summe der Daten aller virtuellen Festplatten des zu sichernden virtuellen Computers|
|Microsoft SQL Server-Datenbank | Größe einer einzelnen zu sichernden SQL-Datenbank |
|Microsoft SharePoint |Summe der Inhalts- und Konfigurationsdatenbanken in einer zu sichernden SharePoint-Farm|
|Microsoft Exchange |Summe aller Exchange-Datenbanken eines zu sichernden Exchange-Servers|
|BMR/Systemstatus |Jede einzelne Kopie der BMR oder des Systemstatus des zu sichernden Computers|

**F2. Gibt es eine Begrenzung dafür, wie häufig ein Sicherungsauftrag pro Tag geplant werden kann?**<br/> A2. Ja. Sie können Sicherungsaufträge unter Windows Server oder auf einem Windows-Client bis zu dreimal pro Tag ausführen. Für System Center DPM können Sie Sicherungsaufträge bis zu zweimal pro Tag ausführen. Für IaaS-VMs können Sie einen Sicherungsauftrag einmal pro Tag ausführen.

**F3. Gibt es einen Unterschied zwischen der Planungsrichtlinie für DPM und Windows Server (also unter Windows Server ohne DPM)?** <br/> A3. Ja. Mit DPM können Sie tägliche, wöchentliche, monatliche und jährliche Zeitpläne angeben. Für Windows Server (ohne DPM) können Sie nur tägliche und wöchentliche Zeitpläne angeben.

**F4. Gibt es einen Unterschied zwischen der Aufbewahrungsrichtlinie für DPM und Windows Server bzw. den Windows-Client (also unter Windows Server ohne DPM)?**<br/> A4. Nein. Sowohl für DPM als auch für Windows Server bzw. Windows-Clients gelten tägliche, wöchentliche, monatliche und jährliche Aufbewahrungsrichtlinien.

**F5. Kann ich meine Aufbewahrungsrichtlinien selektiv konfigurieren – d. h. wöchentlich und täglich, jedoch nicht jährlich und monatlich?**<br/> A5. Ja, durch die Struktur der Azure Backup-Aufbewahrungsrichtlinie können Sie die Aufbewahrungsrichtlinie entsprechend Ihren Anforderungen flexibel definieren.

**F6. Kann ich eine Sicherung um 18:00 Uhr planen und Aufbewahrungsrichtlinien für einen anderen Zeitpunkt festlegen?**<br/> A6. Nein. Aufbewahrungsrichtlinien können nur bei den Sicherungspunkten angewendet werden. In der folgenden Abbildung ist die Aufbewahrungsrichtlinie für Sicherungen angegeben, die um 24:00 und 18:00 Uhr erstellt werden. <br/>

![Planen von Sicherung und Aufbewahrung](./media/backup-azure-backup-faq/Schedule.png) <br/>

**F7. Wird für die geplanten Aufbewahrungsrichtlinien eine inkrementelle Kopie übertragen?** <br/> A7. Nein, die inkrementelle Kopie wird dem Zeitpunkt entsprechend gesendet, der auf der Seite für den Sicherungszeitplan angegeben ist. Die Punkte, die aufbewahrt werden können, werden gemäß der Aufbewahrungsrichtlinie ermittelt.

**F8. Wenn die Sicherung für längere Zeit aufbewahrt wird, dauert die Datenwiederherstellung dann deutlich länger (z. B. des ältesten Punkts)?** <br/> A8. Nein – die Zeitdauer für die Wiederherstellung des ältesten oder des neuesten Punkts ist ein und dieselbe. Jeder Wiederherstellungspunkt verhält sich wie ein vollständiger Punkt.

**F9. Wenn jeder Wiederherstellungspunkt sich wie ein vollständiger Punkt verhält, wie wirkt sich dies auf den abrechenbaren Gesamtspeicher für die Sicherung aus?**<br/> A9. Bei typischen Produkten für die langfristige Aufbewahrung werden Sicherungsdaten als vollständige Punkte gespeichert. Die vollständigen Punkte sind im Hinblick auf den Speicher *ineffizient*, aber einfacher und schneller wiederherzustellen. Inkrementelle Kopien sind speicher*effizient*. Allerdings müssen Sie eine Datenkette wiederherstellen, was sich auf die Wiederherstellungszeit auswirkt. Die besondere Speicherarchitektur von Azure Backup bietet Ihnen die Vorteile beider Ansätze, indem die Daten optimal zur schnellen Wiederherstellung und zu geringen Speicherkosten gespeichert werden. Mit diesem Ansatz der Datenspeicherung wird sichergestellt, dass die Eingangs- und Ausgangsbandbreite effizient genutzt wird. So werden sowohl der Umfang der Datenspeicherung als auch die benötigte Zeit für die Wiederherstellung der Daten möglichst gering gehalten.

**F10. Gibt es eine Beschränkung für die Anzahl der Wiederherstellungspunkte, die erstellt werden können?**<br/> A10. Nein. Wir haben eine Beschränkung für Wiederherstellungspunkte ausgeschlossen. Sie können so viele Wiederherstellungspunkte wie gewünscht erstellen.

**F11. Warum entspricht die Menge der in der Sicherung übertragenen Daten nicht der Menge der gesicherten Daten?**<br/> A11. Alle Daten, die gesichert werden, werden vor der Übertragung komprimiert und verschlüsselt. Nach Anwendung der Komprimierung und Verschlüsselung sind die Daten im Sicherungstresor 30 bis 40% kleiner.

**F12. Gibt es eine Möglichkeit, die vom Backup-Dienst verwendete Bandbreitenmenge anzupassen?**<br/> A12. Ja. Verwenden Sie die Option **Eigenschaften ändern** im Backup-Agent, um die Bandbreite anzupassen. Passen Sie die Bandbreitenmenge und die Zeiten an, zu denen Sie die Bandbreite nutzen. Weitere Informationen finden Sie unter [Netzwerkdrosselung](../backup-configure-vault.md#enable-network-throttling).


## Wiederherstellen
**F1. Wie viele Wiederherstellungen kann ich für die Daten ausführen, die auf Azure gesichert werden?**<br/> A1. Es gibt keine Beschränkung für die Anzahl der Wiederherstellungen aus Azure Backup.

**F2. Muss ich bei Wiederherstellungen für ausgehenden Datenverkehr aus dem Azure-Datencenter bezahlen?**<br/> A2. Nein. Ihre Wiederherstellungen sind kostenlos, und der ausgehende Datenverkehr wird nicht in Rechnung gestellt.

## Sicherheit
**F1. Werden die Daten verschlüsselt an Azure gesendet?** <br/> A1. Ja. Daten werden auf dem lokalen Server-/Client-/SCDPM-Computer mit AES256 verschlüsselt, und die Daten werden über eine sichere HTTPS-Verbindung gesendet.

**F2. Werden die Sicherungsdaten auf Azure ebenfalls verschlüsselt?**<br/> A2. Ja. Die an Azure gesendeten Daten bleiben verschlüsselt (im Ruhezustand). Die Sicherungsdaten werden zu keinem Zeitpunkt von Microsoft entschlüsselt.

**F3. Wie lang muss der Verschlüsselungsschlüssel zur Verschlüsselung von Sicherungsdaten mindestens sein?** <br/> A3. Der Verschlüsselungsschlüssel sollte mindestens 16 Zeichen lang sein.

**F4. Was geschieht, wenn ich den Schlüssel verlege? Kann ich die Daten wiederherstellen, oder kann Microsoft die Daten wiederherstellen?** <br/> A4. Der Schlüssel zum Verschlüsseln der Sicherungsdaten ist nur am Standort des Kunden vorhanden. Microsoft behält keine Kopie in Azure und hat keinen Zugriff auf den Schlüssel. Wenn der Kunde den Schlüssel verlegt, kann Microsoft die gesicherten Daten nicht wiederherstellen.

## Backup-Cache

**F1. Wie ändere ich den Cachespeicherort für den Azure Backup-Agent?**<br/>

1. Beenden Sie das Backup-Modul, indem Sie den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten ausführen:

  ```PS C:\> Net stop obengine```

2. Kopieren Sie den Cacheordner auf ein anderes Laufwerk mit ausreichendem Speicherplatz. Es ist ratsam, die Dateien aus dem Cacheordner zu kopieren, anstatt sie zu verschieben. Der ursprüngliche Cachespeicher kann entfernt werden, nachdem bestätigt wurde, dass die Sicherungen mit dem neuen Cachespeicher funktionieren.

3. Aktualisieren Sie die folgenden Registrierungseinträge mit dem Pfad zum neuen Cacheordner.<br/>

|Registrierungspfad | Registrierungsschlüssel | Wert |
| ------ | ------- | ------|
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | *Neuer Speicherort des Cacheordners* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | *Neuer Speicherort des Cacheordners* |

4. Starten Sie das Backup-Modul neu, indem Sie den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten ausführen:

  ```PS C:\> Net start obengine```

  Nachdem die Erstellung der Sicherung am neuen Cachespeicherort erfolgreich abgeschlossen wurde, können Sie den ursprünglichen Cacheordner entfernen.

<!---HONumber=AcomDC_0323_2016-->