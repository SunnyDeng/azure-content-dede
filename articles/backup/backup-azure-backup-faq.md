<properties
	pageTitle="Azure-Sicherung – häufig gestellte Fragen"
	description="Häufig gestellte Fragen zur Azure Backup-Dienst"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="03/26/2015"
	 ms.author="prvijay"/>

# Azure-Sicherung – häufig gestellte Fragen
Im folgenden finden eine Liste häufig gestellte Fragen zur Azure-Sicherung. Wenn Sie weiteren Fragen zur Azure-Sicherung haben, fahren Sie mit der die [Diskussionsforum](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=windowsazureonlinebackup) und Ihre Fragen. Ein Mitglied der Community hilft Ihnen Ihre Antworten zu erhalten. Wenn eine Frage häufiger gestellt wird, werden wir es zu diesem Artikel hinzufügen, damit es schnell und einfach gefunden werden kann.

## Installation und Konfiguration

**Q1. Was ist die Liste der unterstützten Betriebssysteme, die von denen ich in Azure mithilfe von Azure-Sicherung gesichert werden kann?** <br/> A1. Die folgende Liste von OS'es wird von Azure-Sicherung unterstützt.


| Betriebssystem | Plattform | SKU |
| :------------- |-------------| :-----|
| Windows 8 und neuesten SPs | 64-bit | Pro Unternehmen |
| Windows 7 und neuesten SPs | 64-bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic Starter |
| Windows 8.1 und neuesten SPs | 64-bit | Pro Unternehmen |
|Windows Server 2012 R2 und neuesten SPs|	64-bit|	Standard, Datacenter, Foundation|
|Windows Server 2012 und neuesten SPs|	64-bit|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 und neuesten SPs |64-bit|	Workgroup, Standard|
|Windows Storage Server 2012 und neuesten SPs |64-bit |Workgroup, Standard
|Windows Server 2012 R2 und neuesten SPs |64-bit|	Wichtige|
|Windows Server 2008 R2 SP1 |64-bit|	Standard, Enterprise, Datacenter Foundation|
|Windows Server 2008 SP2 |64-bit|	Standard, Enterprise, Datacenter Foundation|

**2. QUARTAL. Wo kann ich den neuesten Azure Backup-Agent herunterladen?** <br/> A2. Sie können den neuesten-Agent herunterladen [hier](http://aka.ms/azurebackup_agent). Dies kann auf Windows Server, SCDPM Server- oder Windows-Clients installiert werden

**3. QUARTAL. Welche Version von SCDPM Server werden unterstützt?** <br/> A3. Es wird empfohlen, die Installation der [neuesten](http://aka.ms/azurebackup_agent) Azure Backup-Agent auf dem neuesten Updaterollup der SCDPM (UR5 seit Februar 2015)

**Q4. Wenn Sie den Azure-Sicherungs-Agent konfigurieren, werde ich aufgefordert, "tresoranmeldedaten" eingeben. Gibt es Ablaufdatum angefügt ist die tresoranmeldedaten?** <br/> A4. Ja, die Depot-Anmeldeinformationen laufen nach 48hours ab. Wenn die Datei läuft ab, in der Azure-Portal anmelden und Tresor Anmeldeinformationen Dateien aus der sicherungstresor herunterladen.

**F5. Gibt es keine Beschränkung hinsichtlich der Anzahl der sicherungstresore, die in jeder Azure-Abonnement erstellt werden kann?** <br/> A5. Ja. Sie können ab März 2015 25 Depots pro Abonnement erstellen. Erstellen Sie ein neues Abonnement, Sie ggf. Weitere Depots.

**F6. Sollte ich Tresor als Abrechnung Entität ansehen?** <br/> A6. Wäre es möglich, eine detaillierte Rechnung für jeden Tresor zu erhalten, empfiehlt es sich dringend, ein Azure-Abonnement als Abrechnung Entität zu berücksichtigen. Es ist konsistent für alle Dienste und einfacher zu verwalten.

**F7. Gibt es Beschränkungen hinsichtlich der Anzahl der Server/Computer, der für jeden Tresor registriert werden können?** <br/> A7. Ja, Sie können bis zu 50 Computer pro Depot registrieren. Wenn Sie weitere Computer registrieren müssen, erstellen Sie ein neues Depot.

**F8. Gibt es Beschränkungen für die Menge der Daten, die von einem Windows-Server und Client oder SCDPM-Server gesichert werden können?** <br/> A8. Nein.

**F9. Wie registriere ich meinen Server an ein anderes Datencenter?**<br/> A9. Im Allgemeinen ist die Sicherungsdaten an das Rechenzentrum des Backup-Dienst gesendet, bei dem es registriert ist. Die einfachste Methode zum Wechseln des Rechenzentrums ist, deinstallieren Sie den Agent und den Agent neu installieren und registrieren, um ein neues Datencenter.

**F10. Was geschieht, wenn ich einen Windows-Server umbenennen, die in Azure sichern?** <br/> A10. Alle derzeit konfigurierten Sicherungen werden beendet. Müssen Sie den Server für den sicherungstresor zu registrieren und es berücksichtigt werden sollen einen neuen Server durch Recovery Services, also die erste Sicherung, die nach der Registrierung auftritt, kann eine vollständige Sicherung aller Daten nicht nur die Änderungen seit die letzte Sicherung aufgetreten sind. Wenn Sie einen Wiederherstellungsvorgang durchführen müssen können Sie die Daten wiederherstellen, die Wiederherstellung von einem anderen serverwiederherstellungsoption mit gesichert wurde. Weitere Informationen finden Sie unter Umbenennen eines Servers.

**F11. Welche Arten von Laufwerken kann ich Dateien und Ordner sichern?** <br/> A11. Die folgende Gruppe von Festplatten-Volumes möglich backup:

+ Wechselmedien: Als korrigiert werden, muss das Laufwerk melden Quelle für Sicherungen verwendet.

+ Schreibgeschützte Volumes: das Volume muss beschreibbar sein, damit der Volumeschattenkopie-Dienst (VSS)-Funktion sein.

+ Offlinevolumes: Das Volume muss online für die VSS-Funktion sein.

+ Netzwerkfreigabe: das Volume muss lokal auf dem Server mithilfe der online-Sicherung gesichert werden.

**F12. Werden welche Dateien und Ordner können von meinem Server gesichert?**<br/> A12. Die folgenden Typen werden unterstützt:

+ Verschlüsselt

+ Komprimiert

+ Mit geringer Dichte

+ Komprimiert und geringe Dichte

+ Feste Links: Nicht unterstützt, wird übersprungen

+ Analysepunkt: Nicht unterstützt, werden übersprungen

+ Verschlüsselt und komprimiert: Nicht unterstützt, wird übersprungen

+ Verschlüsselt und geringe Dichte: Nicht unterstützt, wird übersprungen

+ Komprimierter Stream: Nicht unterstützt, wird übersprungen

+ Datenstrom mit geringer Dichte: Nicht unterstützt, wird übersprungen

**F13. Was ist die Mindestgröße muss der Cacheordner?** <br/> A13. Die Größe des Cacheordners wird durch die Menge der Daten bestimmt, die Sie möchten sichern. Im Allgemeinen sollten Sie erwarten, dass 10 bis 15 % des Speicherplatzes für die Datenspeicherung erforderlich für den Cacheordner zugewiesen werden soll.

**F14. Wie können bestimmte Serverdaten von der Wiederherstellung von anderen Servern in meiner Organisation werden isoliert? **<br/> A14. Alle mit dem gleichen Tresor registrierten Server werden von anderen Servern, die die gleiche Passphrase verwenden gesicherten Daten wiederherstellen können. Wenn Sie über Server, die verfügen sicherstellen, dass die Wiederherstellung tritt nur auf bestimmten Servern in Ihrer Organisation verwendet werden sollen, sollten Sie eine separate Passphrase für diese Server festgelegten verwenden. Server der Personalabteilung können z. B. eine verschlüsselungspassphrase, accounting Server eine andere und Speicherserver eine dritte verwenden.

**F15. Kann ich "Meine backup-Daten zwischen den Abonnements migrieren"?** <br/> A15: Nein

**F16: Kann ich "Meine sicherungstresor zwischen Abonnements migrieren"?** <br/> A16: Nein. Das Depot wird eine Abonnementstufe erstellt und kann nicht mit einem anderen Abonnement zugewiesen werden, nachdem es erstellt wurde.

## Backup und Archivierung
**Q1. Gibt es eine Beschränkung auf die Größe der einzelnen Datenquellen, gesichert werden?** <br/> A1. Ab März 2015 sollte jede Datenquelle kleiner oder gleich 1,7 TB. Eine Datenquelle ist entweder

+ Datei/Ordner volume

+ SQL-DATENBANK

+ SharePoint-farm

+ Exchange-server

+ Hyper-V-VM

**2. QUARTAL. Ist es Grenzwert für die Häufigkeit der Sicherung pro Tag eingeplant werden kann?**<br/> A2. Ja, ermöglicht Azure Backup 3 Sicherungskopien täglich über Windows Server und Client und 2 Sicherungskopien täglich über SCDPM.

**3. QUARTAL. Gibt es einen Unterschied zwischen DPM und sichern Azure Backup (d. h. auf Windows Server ohne DPM) Planungsrichtlinie?** <br/> A3. Ja. Mithilfe von DPM können Sie täglich, wöchentlich, monatlich, jährlich planen, während Sie sich von einem Windows-Server (ohne DPM) angeben, können Sie angeben, nur tägliche, wöchentliche Zeitpläne.

**Q4. Gibt es einen Unterschied zwischen DPM-s und Azure Backup (d. h. auf Windows Server ohne DPM) Beibehaltungsrichtlinie?**<br/> A4. Nein, Sie verfügen über dieselben Funktionen. Sie können täglich, wöchentlich, monatlich und jährlich Aufbewahrungsrichtlinien angeben.

**F5. Kann ich meine Aufbewahrung, selektiv – d. h. wöchentlich Konfigurieren von Richtlinien, und täglich nicht jährlich und monatlich konfigurieren?**<br/> A5. Sie haben den vollständigen Satz von Regler Richtlinien kommen die am besten auf Ihre Kompatibilität/Aufbewahrungsanforderungen definieren.

**F6. Kann ich "Planen einer Sicherung", um 6 Uhr, und geben Sie "Aufbewahrungsrichtlinien" zu einem anderen Zeitpunkt?**<br/> A6. Nein. Aufbewahrungsrichtlinien können nur auf Sicherung angewendet werden. In den im folgenden Bild wird die Aufbewahrungsrichtlinie auf die Sicherungen auf 12: 00 Uhr und 18: 00 Uhr angegeben wird. <br/>

![Schedule Backup und Archivierung][1] <br/>

**F7. Werden eine inkrementelle Kopie wird für die geplante Aufbewahrungsrichtlinien übertragen?** <br/> A7. Nein, inkrementelle Kopie je nach der Zeit, die auf der Seite Sicherungszeitplan erwähnt gesendet wird. Die Punkte, die aufbewahrt werden, werden gemäß der Aufbewahrungsrichtlinie.

**F8. Wenn die Sicherung für längere Zeit beibehalten wird, dauert es eine längere Zeit zum Wiederherstellen der Daten (z. B. den ältesten Punkt)?** <br/> A8. Nein – ist die Zeitdauer für die Wiederherstellung der ältesten oder den letzten Zeitpunkt ein und derselben Jedem Wiederherstellungspunkt verhält sich wie eine vollständige Point.

**F9. Jedem Wiederherstellungspunkt eine vollständige Punkt ist, wirkt sich diese abrechenbare backup Gesamtspeicher auf?**<br/> A9. Typische langfristige Aufbewahrung Einzelprodukte speichern Sicherungsdaten als vollständige Punkte. Diese Speicher ineffizient sind jedoch einfacher und schneller wiederhergestellt werden. Inkrementelle Kopien sind effiziente Speicherung, jedoch ist es erforderlich, eine Kette von Daten wiederherstellen, die Auswirkungen der Wiederherstellung. Eindeutige Azure Backup-Speicherarchitektur bietet Ihnen das beste aus beiden Welten optimal Speichern von Daten für schnelle Wiederherstellungen und geringer Druckkosten. Dieser Ansatz wird sichergestellt, dass Ihre Bandbreite (eingehend und ausgehend) wird effizienter genutzt, Speicher ist auf ein Minimum gehalten werden und die Zeit zum Wiederherstellen auf ein Minimum.

**F10. Gibt es eine Beschränkung für die Anzahl der Wiederherstellungspunkte, die erstellt werden kann?**<br/> A10. Ab April 2015 haben Sie bis zu 366 Wiederherstellungspunkte. Eine beliebige Kombination können diverse ankommen, die weniger als 366 ist. Z. B. – die Aufbewahrung verweist, dem unter Bild 354 hinzufügen. <br/>

![Bildschirm für die Aufbewahrung][2]

**F11. Sobald Microsoft Grenzwert von 366 verbessert wird, muss ich den Agent aktualisieren oder die erste Sicherung durch Seeding erneut einrichten?** <br/> A11. Nein. Sobald wir in unseren Dienst ändern, werden Sie über unsere Steckdosen soziale Medien (Blogs, Azure Ankündigungen Portal usw.) benachrichtigt. Auf Grundlage Ihrer Anforderungen würde dann müssen Sie nur die Aufbewahrungsrichtlinie ändern.

**F12. Warum ist die Menge der Daten in der Sicherung nicht gleich der Menge der Daten gesichert wurden, einrichten?**<br/> A12. Alle Daten, die gesichert werden komprimiert und verschlüsselt, bevor Sie übertragen wird. Sie können die Vorteile von 30 bis 40 % Komprimierung je nach Typ der zu sichernden Daten rechnen.

## Wiederherstellungen
**Q1. Wie viele Wiederherstellungen kann ich auf die Daten ausführen, die auf Azure werden gesichert?**<br/> A1. Es gibt keine Beschränkung für die Anzahl der Wiederherstellungen von Azure-Sicherung.

**2. QUARTAL. Muss ich bei Wiederherstellungen für ausgehenden Datenverkehr aus dem Azure-Rechenzentrum zu bezahlen?**<br/> A2. Nein. Ihre Wiederherstellungen sind kostenlos, und Sie sind nicht in Rechnung gestellt für ausgehenden Datenverkehr.

## Sicherheit
**Q1. Werden die Daten werden in Azure verschlüsselt gesendet?** <br/> A1. Ja. Daten werden auf dem lokalen Server, Client/SCDPM-Computer mit AES256 verschlüsselt, und die Daten werden über eine sichere HTTPS-Verbindung gesendet.

**2. QUARTAL. Ist die Sicherungsdaten auf Azure ebenfalls verschlüsselt?**<br/> A2. Ja. Die Daten in Azure gesendeten bleibt verschlüsselt (im Ruhezustand). Die Sicherungsdaten an jedem Punkt wird von Microsoft nicht entschlüsselt.

**3. QUARTAL. Was ist die minimale Länge des Verschlüsselungsschlüssels zur Verschlüsselung von backup verwendet?** <br/> A3. Der Verschlüsselungsschlüssel sollte mindestens 16 Zeichen lang sein.

**Q4. Was geschieht, wenn ich den Schlüssel vergessen? Können die Daten wiederherstellen (oder) kann Microsoft die Daten wiederherstellen?** <br/> A4. Der Schlüssel zum Verschlüsseln der backup-Daten ist nur für Kunden vorhanden. Microsoft behält eine Kopie in Azure und hat keinen Zugriff auf den Schlüssel. Wenn der Kunde den Schlüssel misplaces, kann nicht Microsoft die gesicherten Daten wiederherstellen.


<!--Image references-->
[1]: ./media/backup-azure-backup-faq/Schedule.png
[2]: ./media/backup-azure-backup-faq/RetentionScreen1.png

<!---HONumber=GIT-SubDir--> 