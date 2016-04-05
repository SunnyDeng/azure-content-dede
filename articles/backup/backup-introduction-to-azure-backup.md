<properties
	pageTitle="Was ist Azure Backup? | Microsoft Azure"
	description="Mithilfe von Azure Backup und Recovery Services können Sie Daten und Anwendungen von Windows-Servern, Windows-Clientcomputern, System Center DPM-Servern oder virtuellen Azure-VMs sichern und wiederherstellen."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="Sichern und Wiederherstellen; Wiederherstellungsdienste; Sicherungslösungen"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/28/2016"
	ms.author="jimpark; trinadhk"/>

# Was ist Azure Backup?
Azure Backup ist der Dienst zum Sichern und Wiederherstellen von Daten in der Microsoft Cloud. Dieser Dienst ersetzt Ihre vorhandene lokale bzw. standortexterne Lösung durch eine zuverlässige, sichere und wirtschaftliche Cloudlösung. Außerdem trägt er zum Schutz von in der Cloud ausgeführten Assets bei. Azure Backup bietet Wiederherstellungsdienste in einer erstklassigen Infrastruktur, die skalierbar, beständig und hoch verfügbar ist.

[Video mit einen Überblick über Azure Backup](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Gründe für Azure Backup
Herkömmliche Sicherungslösungen haben sich dahingehend entwickelt, dass die Cloud ähnlich einer Festplatte oder einem Band als Endpunkt behandelt wird. Dieser Ansatz ist zwar einfach, weist aber Einschränkungen auf, Hierbei wird die zugrunde liegende Cloudplattform nicht vollständig genutzt, was zu einer ineffizienten, teuren Lösung führt. Azure Backup verfügt dagegen über alle Vorteile einer leistungsstarken und kostengünstigen Cloudsicherungslösung. Es folgen einige der wesentlichen Vorteile von Azure Backup.

| Feature | Vorteil |
| ------- | ------- |
| Automatische Speicherverwaltung | Es sind keine Investitionen für lokale Speichergeräte erforderlich. Azure Backup sorgt im Rahmen eines nutzungsabhängigen Zahlungsmodells für die automatische Zuteilung und Verwaltung von Sicherungsspeicher. |
| Unbegrenzte Skalierung | Hohe Verfügbarkeit wird ohne Aufwand für Wartung und Überwachung gewährleistet. Azure Backup nutzt die zugrunde liegende Leistung und Skalierbarkeit der Azure-Cloud und automatische Skalierungsmöglichkeiten ohne Benutzereingriffe. |
| Mehrere Speicheroptionen | Wählen Sie Sicherungsspeicher je nach Bedarf: <li>Ein lokal redundanter Speicherblockblob ist ideal für preisbewusste Kunden und bietet Schutz der Daten vor lokalen Hardwareausfällen. <li>Ein Speicherblockblob mit Georeplikation bietet drei weitere Kopien in einem gekoppelten Rechenzentrum. Mit diesen zusätzlichen Kopien wird sichergestellt, dass Ihre Sicherungsdaten auch bei einem Ausfall eines Azure-Rechenzentrums hoch verfügbar sind. |
| Unbegrenzte Dateiübertragungen | Es werden keine Kosten für die Übertragung ausgehender Daten während eines Wiederherstellungsvorgangs mithilfe des Azure Backup-Tresors berechnet. In Azure eingehende Datenübertragungen sind ebenfalls kostenlos. Funktioniert in Verbindung mit dem Importdienst, falls verfügbar. |
| Datenverschlüsselung | Die Datenverschlüsselung ermöglicht eine sichere Übertragung und Speicherung von Kundendaten in der öffentlichen Cloud. Die Passphrase für die Verschlüsselung wird in der Quelle gespeichert und niemals in Azure übertragen oder gespeichert. Der Verschlüsselungsschlüssel ist zum Wiederherstellen der Daten erforderlich, und nur der Kunde hat vollen Zugriff auf die Daten im Dienst. |  
| Anwendungskonsistente Sicherungen | Mit anwendungskonsistenten Sicherungen unter Windows kann sichergestellt werden, dass zum Zeitpunkt der Wiederherstellung keine Korrekturen erforderlich sind. Dadurch wird die angestrebte Wiederherstellungszeit verkürzt. Dadurch können Kunden schneller den Betrieb wieder aufnehmen. |
| Langfristige Aufbewahrung | Anstatt für standortexterne Bandsicherungslösungen zu zahlen, können Kunden Daten in Azure sichern. Dies ist eine attraktive Lösung, die mit der Bandsicherung vergleichbar und kostengünstig ist. |

## Azure Backup-Komponenten
Als eine hybride Sicherungslösung besteht Azure Backup aus mehreren Komponenten, die zusammenarbeiten, um durchgängige Workflows für das Sichern und Wiederherstellen zu ermöglichen.

![Azure Backup-Komponenten](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

### Bereitstellungsszenarien

| Komponente | Bereitstellung in Azure möglich? | Lokale Bereitstellung möglich? | Unterstützter Zielspeicher|
| --- | --- | --- | --- |
| Azure Backup-Agent | <p>Ja</p> <p>Der Azure Backup-Agent kann auf allen virtuellen Windows Server-Computern bereitgestellt werden, die in Azure ausgeführt werden.</p> | <p>Ja</p> <p>Der Azure Backup-Agent kann auf allen virtuellen Windows Server-Computern oder physischen Computern bereitgestellt werden.</p> | <p>Azure Backup-Tresor</p> |
| System Center Data Protection Manager (DPM) | <p>Ja</p><p>Erfahren Sie mehr über den [Schutz von Workloads in Azure mithilfe von System Center DPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>Ja</p> <p>Erfahren Sie mehr zum [Schutz von Workloads und virtuellen Computern im Rechenzentrum](https://technet.microsoft.com/library/hh758173.aspx).</p> | <p>Lokal angefügter Datenträger, </p><p>Azure Backup-Tresor,</p> <p>Band (nur lokal)</p> |
| Azure Backup Server | <p>Ja</p><p>Erfahren Sie mehr zum [Schutz von Workloads in Azure mithilfe von Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> | <p>Ja</p> <p>Erfahren Sie mehr zum [Schutz von Workloads in Azure mithilfe von Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> | <p>Lokal angefügter Datenträger,</p> <p>Azure Backup-Tresor</p> |
| Azure Backup (VM-Erweiterung) | <p>Ja</p><p>Teil von Azure Fabric</p><p>Speziell für die [Sicherung von Azure IaaS-VMs (Infrastructure as a Service)](backup-azure-vms-introduction.md).</p> | <p>Nein</p> <p>Sichern Sie virtuelle Computer in Ihrem Rechenzentrum mit System Center DPM.</p> | <p>Azure Backup-Tresor</p> |

### Vorteile und Einschränkungen auf Komponentenebene

| Component | Vorteile | Einschränkungen | Granularität der Wiederherstellung |
| --- | --- | --- | --- |
| Azure Backup-Agent (MARS) | <li>Ermöglicht die Sicherung von Dateien und Ordnern auf einem Computer mit Windows-Betriebssystem – physisch oder virtuell (VMs können sich an einem beliebigen Ort lokal oder unter Azure befinden)<li>Kein separater Sicherungsserver erforderlich<li>Verwendung des Azure-Sicherungstresors | <li>Dreimal pro Tag Sicherung bzw. Wiederherstellung auf Dateiebene<li>Nur Wiederherstellung auf Datei-/Ordner-/Volumeebene, nicht anwendungsorientiert<li>Keine Unterstützung von Linux | Dateien/Ordner/Volumes |
| System Center Data Protection Manager | <li>App-fähige Momentaufnahmen (VSS)<li>Vollständige Flexibilität in Bezug auf Sicherungszeitpunkt<li>Wiederherstellungsgranularität (alle)<li>Verwendung des Azure-Sicherungstresors möglich<li>Linux-Unterstützung (bei Hosting unter Hyper-V) | <li>Fehlende heterogene Unterstützung (VMware-VM-Sicherung, Oracle-Workloadsicherung) | Dateien/Ordner/Volumes<br>/VMs/Anwendungen |
| Microsoft Azure Backup Server | <li>App-fähige Momentaufnahmen (VSS)<li>Vollständige Flexibilität in Bezug auf Sicherungszeitpunkt<li>Wiederherstellungsgranularität (alle)<li>Verwendung des Azure-Sicherungstresors möglich<li>Linux-Unterstützung (bei Hosting unter Hyper-V)<li>Keine System Center-Lizenz erforderlich | <li>Fehlende heterogene Unterstützung (VMware-VM-Sicherung, Oracle-Workloadsicherung)<li>Aktives Azure-Abonnement immer erforderlich<li>Keine Unterstützung der Bandsicherung | Dateien/Ordner/Volumes<br>/VMs/Anwendungen |
| Azure IaaS-VM-Sicherung | <li>Native Sicherungen für Windows/Linux<li>Keine spezielle Agentinstallation erforderlich<li>Sicherung auf Fabric-Ebene ohne Sicherungsinfrastruktur<li>Verwendung des Azure-Sicherungstresors möglich | <li>Einmal pro Tag Sicherung/Wiederherstellung auf Datenträgerebene<li>Lokale Sicherung nicht möglich | VMs<br>Einzelne Datenträger |

## Welche Anwendungen und Workloads kann ich sichern?

| Workload | Quellcomputer | Azure Backup-Lösung |
| --- | --- |---|
| Dateien und Ordnern | Windows Server | <p>[Azure Backup-Agent](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ Azure Backup-Agent),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (enthält Azure Backup-Agent)</p> |
| Dateien und Ordnern | Windows-Client | <p>[Azure Backup-Agent](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ Azure Backup-Agent),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (enthält Azure Backup-Agent)</p> |
| Virtueller Hyper-V-Computer (Windows) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure Backup-Agent),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (enthält Azure Backup-Agent)</p> |
| Virtueller Hyper-V-Computer (Linux) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure Backup-Agent),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (enthält Azure Backup-Agent)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure Backup-Agent),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (enthält Azure Backup-Agent)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure Backup-Agent),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (enthält Azure Backup-Agent)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure Backup-Agent),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (enthält Azure Backup-Agent)</p> |
| Azure IaaS-VMs (Windows) | - | [Azure Backup (VM-Erweiterung)](backup-azure-vms-introduction.md) |
| Azure IaaS-VMs (Linux) | - | [Azure Backup (VM-Erweiterung)](backup-azure-vms-introduction.md) |

## ARM- und Linux-Unterstützung

| Component | ARM-Unterstützung | Linux-Unterstützung (von Azure unterstützt) |
| --- | --- | --- |
| Azure Backup-Agent (MARS) | Ja | Keine (nur Windows-basierter Agent) |
| System Center Data Protection Manager | Ja (Agent in Gastbetriebssystem) | Nur Hyper-V (nicht Azure VM) Nur dateikonsistente Sicherung möglich |
| Azure Backup Server (MABS) | Ja (Agent in Gastbetriebssystem) | Nur Hyper-V (nicht Azure VM) Nur dateikonsistente Sicherung möglich (wie bei DPM) |
| Azure IaaS-VM-Sicherung | In Kürze verfügbar | In Kürze verfügbar – V2 Linux-VMs <br><br>(Konsistenz auf Dateisystemebene) |

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

## Funktionalität
In diesen fünf Tabellen wird zusammengefasst, wie Sicherungsfunktionen in jeder Komponente gehandhabt werden:

### Speicher

| Funktion | Azure Backup-Agent | System Center DPM | Azure Backup Server | Azure Backup (VM-Erweiterung) |
| ------- | --- | --- | --- | ---- |
| Azure Backup-Tresor | ![Ja][green] | ![Ja][green] | ![Ja][green] | ![Ja][green] |
| Datenträgerspeicher | | ![Ja][green] | ![Ja][green] | |
| Bandspeicher | | ![Ja][green] | | |
| Komprimierung (im Sicherungstresor) | ![Ja][green] | ![Ja][green]| ![Ja][green] | |
| Inkrementelle Sicherung | ![Ja][green] | ![Ja][green] | ![Ja][green] | ![Ja][green] |
| Datenträgerdeduplizierung | | ![Teilweise][yellow] | ![Teilweise][yellow]| | |

![Tabellenschlüssel](./media/backup-introduction-to-azure-backup/table-key.png)

Der Azure Backup-Tresor ist das bevorzugte Speicherziel aller Komponenten. System Center DPM und Backup Server verfügen auch über die Option für eine lokale Datenträgerkopie. Nur System Center DPM bietet aber die Möglichkeit, Daten auf ein Bandspeichergerät zu schreiben.

#### Inkrementelle Sicherung
Unabhängig vom Zielspeicher (Datenträger, Band, Sicherungstresor) unterstützen alle Komponenten inkrementelle Sicherungen. Mit der inkrementellen Sicherung wird sichergestellt, dass Sicherungen speicher- und zeiteffizient sind, da nur die seit der letzten Sicherung vorgenommenen Änderungen übertragen werden.

#### Komprimierung
Sicherungen werden komprimiert, um den erforderlichen Speicherplatz zu reduzieren. Die einzige Komponente, für die keine Komprimierung verwendet wird, ist die VM-Erweiterung. Bei der VM-Erweiterung werden alle Sicherungsdaten aus dem Speicherkonto des Kunden in den Sicherungstresor in derselben Region kopiert, ohne dass eine Komprimierung erfolgt. Ohne Komprimierung wird zwar die Speicherbelegung etwas erhöht, doch das Speichern der Daten ohne Komprimierung ermöglicht kürzere Wiederherstellungszeiten.

#### Deduplizierung
Die Deduplizierung wird für System Center DPM und Backup Server unterstützt, wenn [die Bereitstellung in einer Hyper-V-VM erfolgt](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). Die Deduplizierung erfolgt auf Hostebene mithilfe der Deduplizierung von Windows Server auf den virtuellen Festplatten (VHDs), die als Sicherungsspeicher an den virtuellen Computer angefügt sind.

>[AZURE.WARNING] Die Deduplizierung ist in Azure für keine der Azure Backup-Komponenten verfügbar! Wenn System Center DPM und Azure Backup Server in Azure bereitgestellt werden, können an die VM angefügte Speicherdatenträger nicht dedupliziert werden.

### Sicherheit

| Funktion | Azure Backup-Agent | System Center DPM | Azure Backup Server | Azure Backup (VM-Erweiterung) |
| ------- | --- | --- | --- | ---- |
| Netzwerksicherheit (Azure) | ![Ja][green] |![Ja][green] | ![Ja][green] | ![Teilweise][yellow]|
| Datensicherheit (in Azure) | ![Ja][green] |![Ja][green] | ![Ja][green] | ![Teilweise][yellow]|

![Tabellenschlüssel](./media/backup-introduction-to-azure-backup/table-key.png)

Sämtlicher Sicherungsdatenverkehr von Ihren Servern in den Azure Backup-Tresor wird mit AES 256 (Advanced Encryption Standard) verschlüsselt. Die Daten werden über eine sichere HTTPS-Verbindung übertragen. Die Sicherungsdaten werden auch im Sicherungstresor in verschlüsselter Form gespeichert. Nur der Kunde kennt die Passphrase, um diese Daten zu entsperren. Die Sicherungsdaten können zu keinem Zeitpunkt von Microsoft entschlüsselt werden.

>[AZURE.WARNING] Den Schlüssel zum Verschlüsseln der Sicherungsdaten besitzt nur der Kunde. Microsoft behält keine Kopie in Azure und hat keinen Zugriff auf den Schlüssel. Wenn der Schlüssel verlegt wird, kann Microsoft die gesicherten Daten nicht wiederherstellen.

Für das Sichern von Azure VMs ist das Einrichten der Verschlüsselung *im* virtuellen Computer erforderlich. Verwenden Sie BitLocker auf virtuellen Windows-Computern und **dm-crypt** auf virtuellen Linux-Computern. Daten, die auf diesem Weg eingehen, werden von Azure Backup nicht automatisch verschlüsselt.

### Unterstützte Workloads

| Funktion | Azure Backup-Agent | System Center DPM | Azure Backup Server | Azure Backup (VM-Erweiterung) |
| ------- | --- | --- | --- | ---- |
| Windows Server-Computer – Dateien und Ordner | ![Ja][green] | ![Ja][green] | ![Ja][green] | |
| Windows-Clientcomputer – Dateien und Ordner | ![Ja][green] | ![Ja][green] | ![Ja][green] | |
| Virtueller Hyper-V-Computer (Windows) | | ![Ja][green] | ![Ja][green] | |
| Virtueller Hyper-V-Computer (Linux) | | ![Ja][green] | ![Ja][green] | |
| Microsoft SQL Server | | ![Ja][green] | ![Ja][green] | |
| Microsoft SharePoint | | ![Ja][green] | ![Ja][green] | |
| Microsoft Exchange | | ![Ja][green] | ![Ja][green] | |
| Virtueller Azure-Computer (Windows) | | | | ![Ja][green] |
| Virtueller Azure-Computer (Linux) | | | | ![Ja][green] |

![Tabellenschlüssel](./media/backup-introduction-to-azure-backup/table-key-2.png)

### Netzwerk

| Funktion | Azure Backup-Agent | System Center DPM | Azure Backup Server | Azure Backup (VM-Erweiterung) |
| ------- | --- | --- | --- | ---- |
| Netzwerkkomprimierung (zum Sicherungsserver) | | ![Ja][green] | ![Ja][green] | |
| Netzwerkkomprimierung (zum Sicherungstresor) | ![Ja][green] | ![Ja][green] | ![Ja][green] | |
| Netzwerkprotokoll (zum Sicherungsserver) | | TCP | TCP | |
| Netzwerkprotokoll (zum Sicherungstresor) | HTTPS | HTTPS | HTTPS | HTTPS |

![Tabellenschlüssel](./media/backup-introduction-to-azure-backup/table-key-2.png)

Da die VM-Erweiterung die Daten aus dem Azure-Speicherkonto direkt über das Speichernetzwerk liest, ist das Optimieren dieses Datenverkehrs nicht erforderlich. Der Datenverkehr erfolgt über das lokale Speichernetzwerk im Azure-Datencenter, sodass kaum Bedarf an einer Komprimierung aufgrund von Bandbreitenaspekten besteht.

Wenn Sie Ihre Daten auf einem Sicherungsserver (DPM oder Backup Server) sichern, kann der Datenverkehr vom primären Server zum Sicherungsserver komprimiert werden, um die Bandbreitenauslastung zu verringern.

#### Netzwerkdrosselung
Der Azure Backup-Agent verfügt über eine Drosselungsfunktion, mit der Sie steuern können, wie die Netzwerkbandbreite während der Datenübertragung verwendet wird. Die Drosselung kann hilfreich sein, wenn Sie Daten während der Geschäftszeiten sichern möchten, der Sicherungsprozess aber keine Auswirkung auf den anderen Internetdatenverkehr haben soll. Die Drosselung der Datenübertragung gilt für Sicherungs- und Wiederherstellungsaktivitäten.

### Sicherung und Aufbewahrung

| | Azure Backup-Agent | System Center DPM | Azure Backup Server | Azure Backup (VM-Erweiterung) |
| --- | --- | --- | --- | --- |
| Sicherungshäufigkeit (für Sicherungstresor) | Drei Sicherungen pro Tag | Zwei Sicherungen pro Tag |Zwei Sicherungen pro Tag | Eine Sicherung pro Tag |
| Sicherungshäufigkeit (auf Datenträger) | Nicht zutreffend | <p>Alle 15 Minuten für SQL-Server</p> <p>Stündlich für andere Workloads</p> | <p>Alle 15 Minuten für SQL-Server</p> <p>Stündlich für andere Workloads</p> |Nicht zutreffend |
| Aufbewahrungsoptionen | Täglich, wöchentlich, monatlich, jährlich | Täglich, wöchentlich, monatlich, jährlich | Täglich, wöchentlich, monatlich, jährlich |Täglich, wöchentlich, monatlich, jährlich |
| Aufbewahrungszeitraum | Bis zu 99 Jahre | Bis zu 99 Jahre | Bis zu 99 Jahre | Bis zu 99 Jahre |
| Wiederherstellungspunkte in Azure Backup-Tresor | Unbegrenzt | Unbegrenzt | Unbegrenzt | Unbegrenzt |
| Wiederherstellungspunkte auf lokalem Datenträger | Nicht zutreffend | 64 für Dateiserver,<br><br>448 für Anwendungsserver | 64 für Dateiserver,<br><br>448 für Anwendungsserver |Nicht zutreffend |
| Wiederherstellungspunkte auf Band | Nicht zutreffend | Unbegrenzt | Nicht zutreffend | Nicht zutreffend |

## Was ist die Datei mit Tresoranmeldeinformationen?

Die Datei mit Tresoranmeldeinformationen ist ein Zertifikat, das vom Portal für jeden Sicherungstresor generiert wird. Das Portal lädt anschließend den öffentlichen Schlüssel in den Access Control Service (ACS) hoch. Der private Schlüssel wird für Benutzer beim Herunterladen der Anmeldeinformationen bereitgestellt und dann beim Registrieren des Computers eingegeben. Mit dem privaten Schlüssel wird der Computer zum Senden von Sicherungsdaten an einen identifizierten Tresor im Azure Backup-Dienst authentifiziert.

Die Tresoranmeldeinformationen werden nur während des Registrierungsworkflows verwendet. Sie müssen sicherstellen, dass die Datei mit den Tresoranmeldeinformationen sicher aufbewahrt wird. Fällt sie in die Hände eines böswilligen Benutzers, kann dieser die Datei mit den Tresoranmeldeinformationen zur Registrierung weiterer Computer beim selben Tresor verwenden. Da die Sicherungsdaten aber mit einer Passphrase verschlüsselt sind, die allein dem Kunden gehört, sind vorhandene Sicherungsdaten nicht gefährdet. Um dieses Risiko auf ein Mindestmaß zu verringern, laufen die Tresoranmeldeinformationen nach 48 Stunden ab. Sie können die Tresoranmeldeinformationen zwar beliebig oft aus einem Sicherungstresor herunterladen, aber nur die neueste Datei ist für den Registrierungsworkflow gültig.

## Wie unterscheidet sich Azure Backup von Azure Site Recovery?
Viele Kunden verwechseln Sicherungswiederherstellung und Notfallwiederherstellung. Für beide Zwecke werden Daten erfasst und Wiederherstellungsmechanismen bereitgestellt, doch das Hauptanliegen ist in beiden Fällen unterschiedlich.

Azure Backup sichert Daten lokal und in der Cloud. Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server. Beide Dienste sind wichtig, da die Daten im Rahmen Ihrer Notfallwiederherstellungsstrategie sicher und wiederherstellbar gespeichert werden müssen (Backup) *und* Ihre Workloads verfügbar sein müssen (Site Recovery), falls es zu Ausfällen kommen sollte.

Die folgenden Konzepte dienen Ihnen als Unterstützung beim Treffen wichtiger Entscheidungen in Bezug auf die Sicherung und Notfallwiederherstellung.

| Konzept | Details | Sicherung | Notfallwiederherstellung |
| ------- | ------- | ------ | ----------------- |
| Recovery Point Objective (RPO) | Dies ist der Umfang des zulässigen Datenverlusts, falls eine Wiederherstellung durchgeführt werden muss. | Sicherungslösungen weisen eine große Varianz beim akzeptablen RPO auf. Sicherungen virtueller Computer haben zumeist ein RPO von einem Tag, während Datenbanksicherungskopien RPOs von bis zu 15 Minuten aufweisen. | Lösungen für die Notfallwiederherstellung haben niedrige RPO-Werte. Die Kopie für die Notfallwiederherstellung kann einige Sekunden bzw. einige Minuten hinterherhinken. |
| Recovery Time Objective (RTO) | Der Zeitraum, der für eine Wiederherstellung erforderlich ist. | Aufgrund des höheren RPO-Werts ist die Datenmenge, die eine Sicherungslösung verarbeiten muss, meist wesentlich größer, was zu längeren RTOs führt. Beispielsweise kann das Wiederherstellen von Daten von Bändern Tage dauern, was davon abhängt, wie lange der Transport des Bands von einem standortexternen Aufbewahrungsort dauert. | Lösungen für die Notfallwiederherstellung weisen kleinere RTOs auf, da sie synchroner mit der Quelle sind. Weniger Änderungen müssen verarbeitet werden. |
| Aufbewahrung | Die Dauer der Datenspeicherung. | Für Szenarien, für die eine operative Wiederherstellung (Beschädigung von Daten, versehentliches Löschen von Dateien, Ausfall des Betriebssystems) erforderlich ist, werden die Sicherungsdaten normalerweise maximal 30 Tage aufbewahrt.<br>Aus Gründen der Compliance kann es sein, dass Daten mehrere Monate oder sogar Jahre gespeichert werden müssen. In solchen Fällen sind Sicherungsdaten ideal für die Archivierung geeignet. | Für die Notfallwiederherstellung werden nur betriebliche Wiederherstellungsdaten benötigt. Dies dauert normalerweise einige Stunden oder bis zu einem Tag. Aufgrund der differenzierten Datensammlung in Lösungen für die Notfallwiederherstellung empfehlen sich Notfallwiederherstellungsdaten nicht für eine langfristige Aufbewahrung. |

## Nächste Schritte

Testen Sie eine einfache Sicherung mit Azure Backup. Weitere Anweisungen finden Sie in einem der folgenden Lernprogramme:

- [Azure Backup testen](backup-try-azure-backup-in-10-mins.md)
- [Azure-VM-Sicherung testen](backup-azure-vms-first-look.md)

Da diese Tutorials als schnelle Hilfe beim Sichern dienen sollen, wird nur der direkte Weg zum Sichern Ihrer Daten gezeigt. Weitere Informationen zur gewünschten Art der Sicherung finden Sie unter:

- [Sichern von Windows-Computern](backup-configure-vault.md)
- [Sichern von Anwendungsworkloads](backup-azure-microsoft-azure-backup.md)
- [Sichern von Azure IaaS-VMs](backup-azure-vms-prepare.md)



[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0330_2016-->