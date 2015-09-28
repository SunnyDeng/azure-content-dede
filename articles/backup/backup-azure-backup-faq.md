<properties
   pageTitle="Häufig gestellte Fragen zu Azure Backup | Microsoft Azure"
   description="Häufig gestellte Fragen zum Azure Backup-Dienst"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="shreeshd"
   editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/26/2015" ms.author="trinadhk";"giridham"; "arunak"; "jimpark"; "aashishr"/>

# Azure Backup – Häufig gestellte Fragen
Im Folgenden finden Sie eine Liste häufig gestellter Fragen zu Azure Backup. Weitere Fragen zu Azure Backup können im [Diskussionsforum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup) gestellt werden. Ein Mitglied der Community hilft Ihnen dabei, Ihre Antworten zu erhalten. Wenn eine Frage häufiger gestellt wird, fügen wir sie diesem Artikel hinzu, damit sie schnell und einfach gefunden werden kann.

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

**F2. Wo kann ich den neuesten Azure Backup-Agent herunterladen?** <br/> A2. Sie können den neuesten Agent [hier](http://aka.ms/azurebackup_agent) herunterladen. Er kann auf Windows Server, SCDPM-Server oder Windows-Client installiert werden.

**F3. Welche SCDPM-Serverversion wird unterstützt?** <br/> A3. Wir empfehlen, den [aktuellen](http://aka.ms/azurebackup_agent) Azure Backup-Agent aus dem aktuellen Updaterollup von SCDPM (UR6; Stand: Juli 2015) zu installieren.

**F4. Beim Konfigurieren des Azure Backup-Agents werde ich aufgefordert, die "Tresoranmeldedaten" einzugeben. Gibt es ein Ablaufdatum für die Tresoranmeldedaten?** <br/> A4. Ja, die Tresoranmeldedaten laufen nach 48 Stunden ab. Wenn die Datei abläuft, melden Sie sich beim Azure-Portal an, und laden Sie die Dateien mit den Tresoranmeldedaten aus Ihrem Azure Backup-Tresor herunter.

**F5. Gibt es eine Beschränkung hinsichtlich der Anzahl von Sicherungstresoren, die in einem Azure-Abonnement erstellt werden können?** <br/> A5. Ja. Sie können pro Abonnement 25 Tresore erstellen (Stand: Juli 2015). Erstellen Sie ein neues Abonnement, wenn Sie weitere Tresore benötigen.

**F6. Kann ich einen Tresor als Abrechnungseinheit betrachten?** <br/> A6. Zwar besteht die Möglichkeit, eine detaillierte Rechnung für jeden Tresor zu erhalten, es empfiehlt sich aber unbedingt, ein Azure-Abonnement als Abrechnungseinheit zu verwenden. Es ist für alle Dienste konsistent und einfacher zu verwalten.

**F7. Gibt es Beschränkungen im Hinblick auf die Anzahl von Servern/Computern, die pro Tresor registriert werden können?** <br/> A7. Ja, Sie können bis zu 50 Computer pro Tresor registrieren. Bei virtuellen Azure-IaaS-Computern liegt die Beschränkung bei 100 virtuellen Computer pro Depot. Wenn Sie weitere Computer registrieren müssen, erstellen Sie einen neuen Tresor.

**F8. Gibt es Beschränkungen für die Datenmenge, die von einem Windows-Server/-Client oder einem SCDPM-Server gesichert werden kann?** <br/> A8. Nein.

**F9. Wie registriere ich meinen Server bei einem anderen Datencenter?**<br/> A9. Im Allgemeinen werden die Sicherungsdaten an das Datencenter des Backup-Diensts gesendet, bei dem er registriert ist. Die einfachste Methode zum Ändern des Datencenters besteht darin, den Agent zu deinstallieren, neu zu installieren und bei einem neuen Datencenter zu registrieren.

**F10. Was geschieht, wenn ich einen Windows-Server umbenenne, der Daten in Azure sichert?** <br/> A10. Alle derzeit konfigurierten Sicherungen werden beendet. Sie müssen den Server erneut beim Sicherungstresor registrieren, und er wird von Recovery Services als neuer Server betrachtet. Der erste Sicherungsvorgang nach der Registrierung ist daher eine vollständige Sicherung aller in der Sicherung enthaltenen Daten, nicht nur der Änderungen seit der letzten Sicherung. Wenn Sie einen Wiederherstellungsvorgang durchführen müssen, können Sie jedoch die gesicherten Daten wiederherstellen, indem Sie die Option zur Wiederherstellung von einem anderen Server wählen. Weitere Informationen finden Sie unter "Umbenennen eines Servers".

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

**F13. Welche Mindestgröße gilt für den Cacheordner?** <br/> A13. Die Größe des Cacheordners wird durch die Menge der Daten bestimmt, die Sie sichern möchten. Im Allgemeinen sollten Sie davon ausgehen, dass 10 bis 15 % des für die Datenspeicherung erforderlichen Speicherplatzes für den Cacheordner zugewiesen werden sollte.

**F14. Wie kann ich bestimmte Serverdaten isolieren, sodass sie nicht von anderen Servern in meiner Organisation wiederhergestellt werden?**<br/> A14. Alle über denselben Tresor registrierten Server können die Daten wiederherstellen, die von anderen Servern mit derselben Passphrase gesichert wurden. Wenn Sie Server verwenden, deren Wiederherstellung nur auf bestimmten Servern in Ihrer Organisation erfolgen darf, sollten Sie eine separate Passphrase für diese Server festlegen. So können Sie beispielsweise für die Server der Personalabteilung, für die Server der Buchhaltung und für die Speicherserver jeweils eine eigene Verschlüsselungspassphrase verwenden.

**F15. Kann ich meine Sicherungsdaten zwischen den Abonnements "migrieren"?** <br/> A15: Nein

**F16. Kann ich meinen Sicherungstresor zwischen Abonnements "migrieren"?** <br/> A16: Nein. Der Tresor wird auf einer Abonnementstufe erstellt und kann nach seiner Erstellung keinem anderen Abonnement zugewiesen werden.

**F17. Funktioniert der Azure Backup-Agent auf einem Server, der die Deduplizierung von Windows Server 2012 verwendet?** <br/> A17: Ja. Der Agent-Dienst konvertiert die deduplizierten Daten bei der Vorbereitung des Sicherungsvorgangs in normale Daten. Anschließend optimiert er die Daten für die Sicherung, verschlüsselt sie und sendet die verschlüsselten Daten an den Onlinesicherungsdienst.

**F18. Werden die Sicherungsdaten gelöscht, wenn ich eine bereits gestartete Sicherung abbreche?** <br/> A18: Nein. Der Sicherungstresor speichert die übertragenen Sicherungsdaten, die bis zum Zeitpunkt des Abbruchs übertragen wurden. Azure Backup verwendet einen Prüfpunktmechanismus: Während der Sicherung werden immer wieder Prüfpunkte erstellt, und die Integrität der Dateien kann vom nächsten Sicherungsprozess geprüft werden. Die nächste ausgelöste Sicherung ist eine inkrementelle Sicherung, die auf dem vorherigen Sicherungsvorgang aufbaut. Dies sorgt für eine bessere Nutzung der Bandbreite, da nicht wiederholt die gleichen Daten übertragen werden müssen.

**F19. Warum wird eine Warnung mit dem Hinweis angezeigt, dass für diesen Server keine Azure-Sicherungen konfiguriert wurden, obwohl ich zuvor regelmäßige Sicherungen geplant habe?** <br/> A19: Dieses Problem kann auftreten, wenn die auf dem lokalen Server gespeicherten Sicherungszeitplaneinstellungen nicht den Einstellungen im Sicherungstresor entsprechen. Wenn für den Server oder die Einstellungen ein als funktionierend bekannter Zustand wiederhergestellt wurde, ist unter Umständen die Synchronisierung der Sicherungszeitpläne verloren gegangen. Konfigurieren Sie in diesem Fall die Sicherungsrichtlinie neu, und verwenden Sie dann **Sicherung jetzt ausführen**, um den lokalen Server wieder mit Azure zu synchronisieren.

**F20. Welche Firewallregeln müssen für die Sicherung von Azure Backup konfiguriert werden?** <br/> A20. Stellen Sie sicher, dass Firewallregeln die Kommunikation mit den folgenden URLs ermöglichen, um eine nahtlose Sicherung der lokalen Daten in Azure und Workloadschutz auf Azure zu gewährleisten:

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net


## Sicherung und Aufbewahrung
**F1. Gibt es eine Größenbeschränkung für die einzelnen Datenquellen, die gesichert werden?** <br/> A1. Ab August 2015 gelten folgende Größenbeschränkung für die einzelnen Datenquellen für die verschiedenen Betriebssysteme:

|Nr. |	Betriebssystem |	Maximale Größe der Datenquelle |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 oder höher| 54\.400 GB|
|2| Windows Server 8 oder höher| 54\.400 GB|
|3| Windows Server 2008, Windows Server 2008 R2 | 1\.700 GB|
|4| Windows 7 | 1\.700 GB|
 
Die Größe der Datenquelle wird wie folgt gemessen.

|	Datenquelle |	Details |
| :-------------: |:-------------|
|Volume |Die Datenmenge, die von einem Volume eines Computers gesichert wird. Dies gilt für die Volumes, die auf Server- sowie auf Clientcomputern geschützt werden.|
|Virtueller Hyper-V-Computer|Summe der Daten aller virtuellen Festplatten des zu sichernden virtuellen Computers|
|Microsoft SQL Server-Datenbank|Größe einer einzelnen zu sichernden SQL-Datenbank |
|Microsoft SharePoint|Summe der Inhalts- und Konfigurationsdatenbanken in einer zu sichernden SharePoint-Farm|
|Microsoft Exchange|Summe aller Exchange-Datenbanken eines zu sichernden Exchange-Servers|
|BMR/Systemstatus|Jede einzelne Kopie der BMR oder des Systemstatus des zu sichernden Computers|

**F2. Gibt es einen Grenzwert für die Häufigkeit der geplanten Sicherungen pro Tag?**<br/> A2. Ja. Azure Backup ermöglicht drei Sicherungskopien pro Tag über Windows Server oder Windows Client, zwei Sicherungskopien pro Tag über SCDPM und eine Sicherungskopie am Tag für virtuelle IaaS-Computer.

**F3. Gibt es einen Unterschied zwischen den Richtlinien zur Sicherungsplanung von DPM und Azure Backup (d. h. auf Windows Server ohne DPM)?** <br/> A3. Ja. Mithilfe von DPM können Sie tägliche, wöchentliche, monatliche und jährliche Planungen angeben. Auf einem Windows-Server (ohne DPM) hingegen können Sie nur tägliche und wöchentliche Zeitpläne festlegen.

**F4. Gibt es einen Unterschied zwischen den Aufbewahrungsrichtlinien von DPM und Azure Backup (d. h. auf Windows Server ohne DPM)?**<br/> A4. Nein, es stehen dieselben Funktionen zur Verfügung. Sie können tägliche, wöchentliche, monatliche und jährliche Aufbewahrungsrichtlinien angeben.

**F5. Kann ich meine Aufbewahrungsrichtlinien selektiv konfigurieren – d. h. wöchentlich und täglich, jedoch nicht jährlich und monatlich?**<br/> A5. Ihnen steht die vollständige Optionsauswahl zur Verfügung, um Richtlinien zu erstellen, die Ihre Compliance-/Aufbewahrungsanforderungen optimal definieren.

**F6. Kann ich eine Sicherung um 18:00 Uhr planen und Aufbewahrungsrichtlinien für einen anderen Zeitpunkt festlegen?**<br/> A6. Nein. Aufbewahrungsrichtlinien können nur bei den Sicherungspunkten angewendet werden. In der folgenden Abbildung wird die Aufbewahrungsrichtlinie für Sicherungen festgelegt, die um 12:00 Uhr und um 18:00 Uhr erfolgen. <br/>

![Planen von Sicherung und Aufbewahrung](./media/backup-azure-backup-faq/Schedule.png) <br/>

**F7. Wird für die geplanten Aufbewahrungsrichtlinien eine inkrementelle Kopie übertragen?** <br/> A7. Nein, die inkrementelle Kopie wird dem Zeitpunkt entsprechend gesendet, der auf der Seite für den Sicherungszeitplan angegeben ist. Die Punkte, die aufbewahrt werden können, werden gemäß der Aufbewahrungsrichtlinie ermittelt.

**F8. Wenn die Sicherung für längere Zeit aufbewahrt wird, dauert die Datenwiederherstellung dann deutlich länger (z. B. des ältesten Punkts)?** <br/> A8. Nein – die Zeitdauer für die Wiederherstellung des ältesten oder des neuesten Punkts ist ein und dieselbe. Jeder Wiederherstellungspunkt verhält sich wie ein vollständiger Punkt.

**F9. Wenn jeder Wiederherstellungspunkt sich wie ein vollständiger Punkt verhält, wie wirkt sich dies auf den abrechenbaren Gesamtspeicher für die Sicherung aus?**<br/> A9. Bei typischen Produkten für die langfristige Aufbewahrung werden Sicherungsdaten als vollständige Punkte gespeichert. Diese sind im Hinblick auf den Speicher ineffizient, jedoch einfacher und schneller wiederherzustellen. Inkrementelle Kopien sind speichereffizient. Allerdings müssen Sie eine Datenkette wiederherstellen, was sich auf die Wiederherstellungszeit auswirkt. Die besondere Speicherarchitektur von Azure Backup bietet Ihnen die Vorteile beider Ansätze, indem die Daten optimal zur schnellen Wiederherstellung und zu geringen Speicherkosten gespeichert werden. Durch diesen Ansatz wird sichergestellt, dass Ihre (eingehende und ausgehende) Bandbreite effizient genutzt, der Speicher möglichst gering gehalten und die Zeit zur Wiederherstellung auf ein Minimum beschränkt wird.

**F10. Gibt es eine Beschränkung für die Anzahl der Wiederherstellungspunkte, die erstellt werden können?**<br/> A10. Nein. Wir haben eine Beschränkung für Wiederherstellungspunkte ausgeschlossen. Sie können so viele Wiederherstellungspunkte wie gewünscht erstellen.

**F11. Warum entspricht die Menge der in der Sicherung übertragenen Daten nicht der Menge der gesicherten Daten?**<br/> A11. Alle Daten, die gesichert werden, werden vor der Übertragung komprimiert und verschlüsselt. Je nach Typ der zu sichernden Daten können Sie mit einem Komprimierungsvorteil von 30 bis 40 % rechnen.

## Wiederherstellen
**F1. Wie viele Wiederherstellungen kann ich für die Daten ausführen, die auf Azure gesichert werden?**<br/> A1. Es gibt keine Beschränkung für die Anzahl der Wiederherstellungen aus Azure Backup.

**F2. Muss ich bei Wiederherstellungen für ausgehenden Datenverkehr aus dem Azure-Datencenter bezahlen?**<br/> A2. Nein. Ihre Wiederherstellungen sind kostenlos, und der ausgehende Datenverkehr wird nicht in Rechnung gestellt.

## Sicherheit
**F1. Werden die Daten verschlüsselt an Azure gesendet?** <br/> A1. Ja. Daten werden auf dem lokalen Server-/Client-/SCDPM-Computer mit AES256 verschlüsselt, und die Daten werden über eine sichere HTTPS-Verbindung gesendet.

**F2. Werden die Sicherungsdaten auf Azure ebenfalls verschlüsselt?**<br/> A2. Ja. Die an Azure gesendeten Daten bleiben verschlüsselt (im Ruhezustand). Die Sicherungsdaten werden zu keinem Zeitpunkt von Microsoft entschlüsselt.

**F3. Wie lang muss der Verschlüsselungsschlüssel zur Verschlüsselung von Sicherungsdaten mindestens sein?** <br/> A3. Der Verschlüsselungsschlüssel sollte mindestens 16 Zeichen lang sein.

**F4. Was geschieht, wenn ich den Schlüssel verlege? Kann ich die Daten wiederherstellen, oder kann Microsoft die Daten wiederherstellen?** <br/> A4. Der Schlüssel zum Verschlüsseln der Sicherungsdaten ist nur am Standort des Kunden vorhanden. Microsoft behält keine Kopie in Azure und hat keinen Zugriff auf den Schlüssel. Wenn der Kunde den Schlüssel verlegt, kann Microsoft die gesicherten Daten nicht wiederherstellen.

## Backup-Cache

**F1. Wie ändere ich den Cachespeicherort für den Azure Backup-Agent?**

+ Beenden Sie die OBEngine durch Ausführen des nachfolgenden Befehls in einer Eingabeaufforderung mit erhöhten Rechten:

  ```PS C:\> Net stop obengine```

+ Kopieren Sie den Cacheordner auf ein anderes Laufwerk mit ausreichendem Speicherplatz. Es wird empfohlen, dass Sie die Dateien aus dem Cacheordner kopieren und nicht verschieben. Der ursprüngliche Cachespeicher kann nach der Überprüfung der Sicherungen im neuen Cachespeicher entfernt werden.

+ Aktualisieren Sie die folgenden Registrierungseinträge mit dem Pfad zum neuen Cacheordner:


	| Registrierungspfad | Registrierungsschlüssel | Wert |
	| ------ | ------- | ------ |
	| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | <i>Neuer Speicherort des Cacheordners</i> |
	| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | <i>Neuer Speicherort des Cacheordners</i> |


+ Starten Sie die OBEngine durch Ausführen des nachfolgenden Befehls in einer Eingabeaufforderung mit erhöhten Rechten:

  ```PS C:\> Net start obengine```

Sobald die Sicherungen erfolgreich mit dem neuen Cachespeicherort ausgeführt werden, können Sie den ursprünglichen Cacheordner entfernen.

<!---HONumber=Sept15_HO3-->