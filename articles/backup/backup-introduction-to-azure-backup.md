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
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="trinadhk;jimpark"/>

# Was ist Azure Backup?
Azure Backup ist der Dienst zum Sichern und Wiederherstellen von Daten in der Microsoft Cloud. Dieser Dienst ersetzt Ihre vorhandene lokale bzw. standortexterne Lösung durch eine zuverlässige, sichere und wirtschaftliche Cloudlösung. Außerdem trägt er zum Schutz von in der Cloud ausgeführten Assets bei. Azure Backup bietet Wiederherstellungsdienste in einer erstklassigen Infrastruktur, die skalierbar, beständig und hoch verfügbar ist.

[Video mit einen Überblick über Azure Backup](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Gründe für Azure Backup
Herkömmliche Sicherungslösungen haben sich dahingehend entwickelt, dass die Cloud ähnlich einer Festplatte oder einem Band als Endpunkt behandelt wird. Dieser Ansatz ist zwar einfach, weist aber Einschränkungen auf, denn er nutzt die zugrunde liegende Cloudplattform nicht optimal. Das Ergebnis ist eine ineffiziente und teure Lösung. Azure Backup bietet andererseits alle Vorteile einer leistungsfähigen und erschwinglichen Cloudsicherungslösung. Es folgen einige der wesentlichen Vorteile von Azure Backup.

| Feature | Vorteil |
| ------- | ------- |
| Automatische Speicherverwaltung | Es sind keine Investitionen für lokale Speichergeräte erforderlich. Azure Backup sorgt im Rahmen eines nutzungsabhängigen Zahlungsmodells für die automatische Zuteilung und Verwaltung von Sicherungsspeicher. |
| Unbegrenzte Skalierung | Hohe Verfügbarkeit wird ohne Aufwand für Wartung und Überwachung gewährleistet. Azure Backup nutzt die zugrunde liegende Leistung und Skalierbarkeit der Azure-Cloud und automatische Skalierungsmöglichkeiten ohne Benutzereingriffe. |
| Mehrere Speicheroptionen | Wählen Sie Sicherungsspeicher je nach Bedarf: <li>Ein lokal redundanter Speicherblockblob ist ideal für preisbewusste Kunden und bietet Schutz der Daten vor lokalen Hardwareausfällen. <li>Ein Speicherblockblob mit Georeplikation bietet drei zusätzliche Kopien in einem gekoppelten Datencenter. Dadurch wird sichergestellt, dass Ihre Sicherungsdaten auch bei einem Ausfall eines Azure-Datencenters hoch verfügbar sind. |
| Unbegrenzte Dateiübertragungen | Es werden keine Kosten für die Übertragung ausgehender Daten während eines Wiederherstellungsvorgangs mithilfe des Azure Backup-Tresors berechnet. In Azure eingehende Datenübertragungen sind ebenfalls kostenlos. |
| Zentrale Verwaltung | Das Azure-Portal bietet Einfachheit und Vertrautheit. Mit der Weiterentwicklung des Diensts können Sie mit Funktionen wie z. B. der zentralen Verwaltung Ihre Sicherungsinfrastruktur von einem Standort aus verwalten. |
| Datenverschlüsselung | Dies ermöglicht eine sichere Übertragung und Speicherung von Kundendaten in der öffentlichen Cloud. Die Passphrase für die Verschlüsselung wird in der Quelle gespeichert und niemals in Azure übertragen oder gespeichert. Der Verschlüsselungsschlüssel ist zum Wiederherstellen der Daten erforderlich, und nur der Kunde hat vollen Zugriff auf die Daten im Dienst. |  
| Anwendungskonsistente Sicherungen | Anwendungskonsistente Sicherungen unter Windows helfen sicherzustellen, dass zum Zeitpunkt der Wiederherstellung keine Korrekturen erforderlich sind. Dadurch wird die angestrebte Wiederherstellungszeit verkürzt und Kunden ermöglicht, schneller zu einem Ausführungsstatus zurückzukehren. |
| Langfristige Aufbewahrung | Anstatt für standortexterne Sicherungslösungen zu zahlen, können Kunden Daten in Azure sichern. Dies ermöglicht eine überzeugende Lösung mit ähnlichen Abläufen wie bei der Bandsicherung zu sehr niedrigen Kosten. |

## Azure Backup-Komponenten
Als eine hybride Sicherungslösung besteht Azure Backup aus mehreren Komponenten, die zusammenarbeiten, um durchgängige Workflows für das Sichern und Wiederherstellen zu ermöglichen.

![Azure Backup-Komponenten](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

## Bereitstellungsszenarien

| Komponente | Bereitstellung in Azure möglich? | Lokale Bereitstellung möglich? | Unterstützter Zielspeicher|
| --- | --- | --- | --- |
| Azure Backup-Agent | <p>Ja</p> <p>Der Azure Backup-Agent kann auf allen virtuellen Windows Server-Computern bereitgestellt werden, die in Azure ausgeführt werden.</p> | <p>Ja</p> <p>Der Azure Backup-Agent kann auf allen virtuellen Windows Server-Computern oder physischen Computern bereitgestellt werden.</p> | <p>Azure Backup-Tresor</p> |
| System Center Data Protection Manager (DPM) | <p>Ja</p> <p>Erfahren Sie mehr über den [Schutz von Workloads in Azure mithilfe von System Center DPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>Ja</p> <p>Erfahren Sie mehr zum [Schutz von Workloads und virtuellen Computern im Datencenter](https://technet.microsoft.com/library/hh758173.aspx).</p> | <p>Lokal angefügter Datenträger, </p><p>Azure Backup-Tresor,</p> <p>Band (nur lokal)</p> |
| Azure Backup Server | <p>Ja</p> <p>Erfahren Sie mehr über [Schutz von Workloads in Azure mithilfe von Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> | <p>Ja</p> <p>Erfahren Sie mehr über [Schutz von Workloads in Azure mithilfe von Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> | <p>Azure Backup-Tresor</p> |
| Azure Backup (VM-Erweiterung) | <p>Ja</p> <p>Speziell für die [Sicherung von Azure IaaS-VMs (Infrastructure as a Service)](backup-azure-vms-introduction.md).</p> | <p>Nein</p> <p>Sichern Sie virtuelle Computer in Ihrem Datencenter mit System Center DPM.</p> | <p>Azure Backup-Tresor</p> |

## Welche Anwendungen und Workloads kann ich sichern?

| Workload | Quellcomputer | Azure Backup-Lösung |
| --- | --- |---|
| Dateien und Ordnern | Windows Server | <p>[Azure Backup-Agent](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Dateien und Ordnern | Windows-Client | <p>[Azure Backup-Agent](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Virtueller Hyper-V-Computer (Windows) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Virtueller Hyper-V-Computer (Linux) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Azure IaaS-VMs (Windows)| - | [Azure Backup (VM-Erweiterung)](backup-azure-vms-introduction.md) | | Azure IaaS VMs (Linux) | - | [Azure Backup (VM-Erweiterung)](backup-azure-vms-introduction.md) |

## Funktionalität
In diesen fünf Tabellen wird zusammengefasst, wie Azure Backup-Funktionen in jeder Komponente gehandhabt werden:

### Speicher

| Funktion | Azure Backup-Agent | System Center DPM | Azure Backup Server | Azure Backup (VM-Erweiterung) |
| ------- | --- | --- | --- | ---- |
| Azure Backup-Tresor | ![Ja][green] | ![Ja][green] | ![Ja][green] | ![Ja][green] |
| Datenträgerspeicher | | ![Ja][green] | ![Ja][green] | |
| Bandspeicher | | ![Ja][green] | | |
| Komprimierung (im Sicherungstresor) | ![Ja][green] | ![Ja][green]| ![Ja][green] | |
| Inkrementelle Sicherung | ![Ja][green] | ![Ja][green] | ![Ja][green] | ![Ja][green] |
| Datenträgerdeduplizierung | | ![Teilweise][yellow] | ![Teilweise][yellow]| | |

**Legende** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Ja][green]= Unterstützt &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![Teilweise][yellow]= Teilweise unterstützt &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;blank&gt;* = Nicht unterstützt

Der Azure Backup-Tresor ist das bevorzugte Speicherziel aller Komponenten. System Center DPM und Azure Backup Server bieten auch die Möglichkeit, eine Kopie des lokalen Datenträgers zu erstellen, aber nur System Center DPM ermöglicht das Schreiben von Daten auf ein Bandspeichergerät.

#### Inkrementelle Sicherung
Unabhängig vom Zielspeicher (Datenträger, Band, Sicherungstresor) unterstützen alle Komponenten inkrementelle Sicherungen. Dadurch wird sichergestellt, dass die Sicherungen speicher- und zeiteffizient sind, da nur die inkrementellen Änderungen seit der letzten Sicherung in den Zielspeicher übertragen werden. Sicherungen werden außerdem komprimiert, um die Speicherbelegung zu verringern.

Die Komponente, die keine Komprimierung vornimmt, ist die VM-Erweiterung. Alle Sicherungsdaten werden aus dem Speicherkonto des Kunden in den Sicherungstresor in derselben Region kopiert, ohne dass eine Komprimierung erfolgt. Dadurch wird zwar die Speicherbelegung etwas erhöht, doch das Speichern der Daten ohne Komprimierung ermöglicht kürzere Wiederherstellungszeiten.

#### Deduplizierung
Die Deduplizierung wird für System Center DPM und Backup Server unterstützt, wenn [die Bereitstellung in einer Hyper-V-VM erfolgt](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). Die Deduplizierung erfolgt auf Hostebene mithilfe der Deduplizierungsfunktion von Windows Server auf den virtuellen Festplatten (VHDs), die als Sicherungsspeicher an den virtuellen Computer angefügt sind.

>[AZURE.WARNING] Die Deduplizierung ist in Azure für keine der Azure Backup-Komponenten verfügbar! Wenn System Center DPM und Azure Backup Server in Azure bereitgestellt werden, können an die VM angefügte Speicherdatenträger nicht dedupliziert werden.

### Sicherheit

| Funktion | Azure Backup-Agent | System Center DPM | Azure Backup Server | Azure Backup (VM-Erweiterung) |
| ------- | --- | --- | --- | ---- |
| Netzwerksicherheit (Azure) | ![Ja][green] |![Ja][green] | ![Ja][green] | ![Teilweise][yellow]|
| Datensicherheit (in Azure) | ![Ja][green] |![Ja][green] | ![Ja][green] | ![Teilweise][yellow]|

**Legende** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Ja][green]= Unterstützt &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![Teilweise][yellow]= Teilweise unterstützt &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;blank&gt;* = Nicht unterstützt

Sämtlicher Sicherungsdatenverkehr von Ihren Servern in den Azure Backup-Tresor wird mit AES 256 (Advanced Encryption Standard) verschlüsselt. Die Daten werden über eine sichere HTTPS-Verbindung übertragen. Die Sicherungsdaten werden auch im Sicherungstresor in verschlüsselter Form gespeichert. Nur der Kunde kennt die Passphrase, um diese Daten zu entsperren. Die Sicherungsdaten können zu keinem Zeitpunkt von Microsoft entschlüsselt werden.

>[AZURE.WARNING] Den Schlüssel zum Verschlüsseln der Sicherungsdaten besitzt nur der Kunde. Microsoft behält keine Kopie in Azure und hat keinen Zugriff auf den Schlüssel. Wenn der Schlüssel verlegt wird, kann Microsoft die gesicherten Daten nicht wiederherstellen.

Für eine Sicherung von Azure-VMs müssen Sie die Verschlüsselung *in* der virtuellen Maschine explizit einrichten. Verwenden Sie BitLocker auf virtuellen Windows-Maschinen und **dm-crypt** auf virtuellen Linux-Maschinen. Daten, die auf diesem Weg eingehen, werden von Azure Backup nicht automatisch verschlüsselt.

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

**Legende** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Ja][green]= Unterstützt &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;blank&gt;* = Nicht unterstützt

### Netzwerk

| Funktion | Azure Backup-Agent | System Center DPM | Azure Backup Server | Azure Backup (VM-Erweiterung) |
| ------- | --- | --- | --- | ---- |
| Netzwerkkomprimierung (zu Sicherungsserver) | | ![Ja][green] | ![Ja][green] | |
| Netzwerkkomprimierung (zu Sicherungstresor) | ![Ja][green] | ![Ja][green] | ![Ja][green] | |
| Netzwerkprotokoll (zu Sicherungsserver) | | TCP | TCP | |
| Netzwerkprotokoll (zu Sicherungstresor) | HTTPS | HTTPS | HTTPS | HTTPS |

**Legende** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Ja][green]= Unterstützt &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;blank&gt;* = Nicht unterstützt

Da die VM-Erweiterung die Daten aus dem Azure-Speicherkonto direkt über das Speichernetzwerk liest, ist das Optimieren dieses Datenverkehrs nicht erforderlich. Der Datenverkehr erfolgt über das lokale Speichernetzwerk im Azure-Datencenter, sodass kaum Bedarf an einer Komprimierung aufgrund von Bandbreitenaspekten besteht.

Für Kunden, die die Übertragung ihrer Daten an einen Sicherungsserver (System Center DPM oder Azure Backup Server) schützen, kann der Datenverkehr vom primären Server zum Sicherungsserver auch komprimiert werden, um die Bandbreitenauslastung zu verringern.

### Sicherung und Aufbewahrung

| | Azure Backup-Agent | System Center DPM und Azure Backup Server | Azure Backup (VM-Erweiterung) |
| --- | --- | --- | --- |
| Sicherungshäufigkeit (in Azure-Tresor) | Drei Sicherungen pro Tag | Zwei Sicherungen pro Tag | Eine Sicherung pro Tag |
| Sicherungshäufigkeit (auf Datenträger) | Nicht zutreffend | <p>Alle 15 Minuten für SQL-Server</p> <p>Stündlich für andere Workloads</p> | Nicht zutreffend |
| Aufbewahrungsoptionen | Täglich, wöchentlich, monatlich, jährlich | Täglich, wöchentlich, monatlich, jährlich | Täglich, wöchentlich, monatlich, jährlich |
| Aufbewahrungszeitraum | Bis zu 99 Jahre | Bis zu 99 Jahre | Bis zu 99 Jahre |
| Wiederherstellungspunkte in Azure Backup-Tresor | Unbegrenzt | Unbegrenzt | Unbegrenzt |
| Wiederherstellungspunkte auf lokalem Datenträger | Nicht zutreffend | Nicht zutreffend | Nicht zutreffend |
| Wiederherstellungspunkte auf Band | Nicht zutreffend | Nicht zutreffend | Nicht zutreffend |

## Wie unterscheidet sich Azure Backup von Azure Site Recovery?
Viele Kunden verwechseln Sicherungswiederherstellung und Notfallwiederherstellung. Für beide Zwecke werden Daten erfasst und Wiederherstellungsmechanismen bereitgestellt, doch das Hauptanliegen ist in beiden Fällen unterschiedlich.

Azure Backup sichert Daten lokal und in der Cloud. Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server. Sie benötigen beide Komponenten für eine vollständige Lösung zur Notfallwiederherstellung. Für Ihre Notfallwiederherstellungsstrategie müssen Ihre Daten sicher und wiederherstellbar gespeichert werden (Backup) *und* Ihre Workloads verfügbar und zugänglich (Site Recovery) sein, sollte es zu Ausfällen kommen.

Um Entscheidungen hinsichtlich der Sicherung und Notfallwiederherstellung zu treffen, sollten Sie mit den folgenden wichtigen Konzepten vertraut sein:

| Konzept | Details | Sicherung | Notfallwiederherstellung |
| ------- | ------- | ------ | ----------------- |
| Recovery Point Objective (RPO) | Der Umfang des Datenverlusts, der im Fall einer erforderlichen Wiederherstellung akzeptabel ist. | Sicherungslösungen weisen eine große Varianz beim akzeptablen RPO auf. Sicherungen virtueller Computer haben zumeist ein RPO von einem Tag, während Datenbanksicherungskopien RPOs von bis zu 15 Minuten aufweisen. | Lösungen für die Notfallwiederherstellung haben überaus niedrige RPOs. Die Kopie für die Notfallwiederherstellung kann einige Sekunden bzw. einige Minuten hinterherhinken. |
| Recovery Time Objective (RTO) | Der Zeitraum, der für eine Wiederherstellung erforderlich ist. | Aufgrund des größeren RPO ist die Datenmenge, die eine Sicherungslösung verarbeiten muss, meist wesentlich größer. Dies führt zu längeren RTOs. Beispielsweise kann das Wiederherstellen von Daten von Bändern Tage dauern, was davon abhängt, wie lange der Transport des Bands von einem standortexternen Aufbewahrungsort dauert. | Lösungen für die Notfallwiederherstellung weisen kleinere RTOs auf, da sie synchroner mit der Quelle sind. Weniger Änderungen müssen verarbeitet werden. |
| Aufbewahrung | Die Dauer der Datenspeicherung. | <p>Bei Szenarien, in denen die Wiederherstellung des Geschäftsbetriebs (nach Datenbeschädigung, versehentlichem Löschen von Dateien oder Betriebssystemausfällen) erforderlich ist, werden Sicherungsdaten in der Regel maximal 30 Tage lang aufbewahrt.</p> <p>Vom Standpunkt der Compliance aus müssen Daten möglicherweise Monate oder sogar Jahre gespeichert werden. In solchen Fällen sind Sicherungsdaten ideal für die Archivierung geeignet.</p> | Für die Notfallwiederherstellung werden nur Betriebsdaten für die Wiederherstellung benötigt. Diese dauert in der Regel von einigen Stunden bis zu einem Tag. Aufgrund der differenzierten Datensammlung in Lösungen für die Notfallwiederherstellung empfehlen sich Notfallwiederherstellungsdaten nicht für eine langfristige Aufbewahrung. |


## Nächste Schritte

- [Azure Backup testen](backup-try-azure-backup-in-10-mins.md)
- [Häufig gestellte Fragen zum Azure Backup-Dienst](backup-azure-backup-faq.md)
- Besuchen Sie das [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).


[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0204_2016-->