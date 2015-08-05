<properties
	pageTitle="Einführung in Azure Backup"
	description="Dieser Artikel bietet eine Übersicht über den Azure Backup-Dienst, mit dem Kunden Daten lokal und in Azure sichern können."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="trinadhk"/>

# Einführung in Azure Backup
Dieser Artikel bietet eine allgemeine Übersicht über die in die Cloud integrierte Sicherungslösung von Microsoft, mit der Kunden ihre Daten entweder lokal oder in Azure sichern können.

## Was ist Azure Backup?
Azure Backup ist ein Azure-Mehrinstanzendienst, mit dem Sie Ihre Daten sichern können: lokal oder in Azure. Ihre vorhandene lokale oder Offsite-Sicherungslösung wird durch eine zuverlässige und sichere Cloud-basierte Lösung mit wettbewerbsfähigen Kosten ersetzt. Zudem bietet dieser Dienst die Flexibilität, Ressourcen zu schützen, die in der Cloud ausgeführt werden. Azure Backup baut auf einer erstklassigen Infrastruktur auf, die skalierbar, robust und hoch verfügbar ist. Mit dieser Lösung können Sie Daten und Anwendungen von ihren System Center Data Protection Manager (SCDPM)-Servern, Windows-Servern, Windows-Clientcomputern oder virtuellen Azure IaaS-Computern sichern. Azure Backup und SCDPM bilden die technologischen Grundlagen der Cloud-basierten Microsoft-Sicherungslösung.

> [AZURE.VIDEO what-is-azure-backup]

## Cloud-Design
Herkömmliche Sicherungslösungen haben sich dahingehend entwickelt, dass die Cloud ähnlich einer Festplatte oder einem Band als Endpunkt behandelt wird. Dieser Ansatz ist einfach, leicht bereitzustellen und bietet eine einheitliche Benutzererfahrung, ist jedoch in der Verwendung eingeschränkt und nutzt die zugrunde liegende Plattform nicht in vollem Umfang aus. Daraus ergibt sich eine ineffiziente und teure Lösung für Endkunden. Wenn Azure lediglich als Speicherendpunkt behandelt wird, können Sicherungslösungen nicht den vollen Umfang und die Leistungsfähigkeit der Public-Cloud-Plattform nutzen. Azure Backup hingegen ist ein echter Dienst, der die Cloud-Konstruktion nutzt und eine leistungsfähige und erschwingliche Sicherungslösung bietet. Azure Backup lässt sich in Ihre lokale Sicherungslösung (SCDPM) integrieren und bietet so eine End-to-End-Hybridlösung.

Dieser Ansatz bietet folgende Vorteile:

+ Effiziente Cloud-Speicherarchitektur mit kostengünstiger und stabiler Datenspeicherung

+ Nicht intrusive, automatische Skalierung des Diensts mit Gewährleistung einer hohen Verfügbarkeit

+ Einheitliches Verfahren zur Sicherung von lokalen, Hybrid- und IaaS-Bereitstellungen

Wichtigste Funktionen dieser Lösung:

1. **Zuverlässiger Dienst**: Durch die Einführung von Azure Backup erhalten Sie einen Sicherungsdienst mit hoher Verfügbarkeit. Es handelt sich um einen IT-Mehrinstanzendienst, bei dem Sie sich nicht um die Verwaltung der zugrunde liegenden Rechen- und Speichervorgänge kümmern müssen.

2. **Zuverlässige Speicherung**: Azure Backup baut auf einer zuverlässigen Cloud-Speicherung auf, die durch hohe SLAs gestützt wird. Sie müssen sich keine Gedanken über die laufenden oder betrieblichen Ausgaben der Speicherverwaltung machen. Darüber hinaus haben Sie die Wahl zwischen der Sicherung im lokal redundanten Speicher (LRS) oder im georedundanten Speicher (GRS). LRS ermöglicht 3 Kopien der Daten in der gleichen Region und schützt die Daten vor lokalen Hardwareausfällen. Mit GRS werden 3 weitere Kopien (insgesamt 6 Kopien) in einem gekoppelten Rechenzentrum erstellt. Dadurch wird sichergestellt, dass Ihre Sicherungsdaten hoch verfügbar sind. Selbst bei einem Azure-Ausfall sind die Sicherungsdaten bei uns sicher gespeichert.

3. **Sicherheit**: Azure-Sicherungsdaten werden in der Quelle und während der Übertragung verschlüsselt und in Azure verschlüsselt gespeichert. Der Verschlüsselungsschlüssel wird in der Quelle gespeichert und niemals in Azure übertragen oder gespeichert. Der Verschlüsselungsschlüssel ist zum Wiederherstellen der Daten erforderlich, und nur der Kunde hat vollen Zugriff auf die Daten im Dienst.

4. **Offsite-Schutz**: Anstatt in Offsite-Bandsicherungslösungen zu investieren, können Kunden ihre Daten in Azure sichern – eine überzeugende Lösung mit einer ähnlichen Semantik wie bei der Bandsicherung und mit äußerst niedrigen Kosten.

5. **Einfachheit**: Azure Backup bietet eine vertraute Benutzeroberfläche, die zum Schutz einer Bereitstellung beliebiger Größe skaliert werden kann. Mit der Weiterentwicklung des Diensts können Sie mit Funktionen wie z. B. der zentralen Verwaltung Ihre Sicherungsinfrastruktur von einem Standort aus verwalten.

6. **Kosteneffizienz**: Der Preis für Azure Backup umfasst eine Verwaltungsgebühr für die Sicherung pro Instanz und die Kosten für den in Azure verbrauchten Speicher (Preis für Blockblobspeicher). Im Gegensatz zu anderen Cloud-basierten Sicherungslösungen werden Kunden von Azure Backup Wiederherstellungsvorgänge nicht in Rechnung gestellt. Außerdem werden keine Kosten für die Übertragung ausgehender Daten während eines Wiederherstellungsvorgangs berechnet.

7. **Backup in der Cloud:** Azure Backup bietet eine VSS-basierte anwendungskonsistente Sicherung von virtuellen Azure IaaS-Computern, ohne dass die virtuellen Computer heruntergefahren werden müssen. Darüber hinaus können virtuelle Linux-Computer mit Dateisystemkonsistenz in Azure gesichert werden.


## Anwendung und Workloads

| Workload | Quellcomputer | Azure Backup-Lösung |
| --- | --- |---|
| Datei und Ordner | Windows Server, Windows-Client | Azure Backup-Agent |
| Datei und Ordner | Windows Server, Windows-Client | System Center DPM |
| Virtueller Hyper-V-Computer (Windows) | Windows Server | System Center DPM |
| Virtueller Hyper-V-Computer (Linux) | Windows Server | System Center DPM |
| Microsoft SQL Server | Windows Server | System Center DPM |
| Microsoft SharePoint | Windows Server | System Center DPM |
| Microsoft Exchange | Windows Server | System Center DPM |
| Azure IaaS-VMs (Windows)| - | Azure Backup | | Azure IaaS-VMs (Linux) | - | Azure Backup |

## Nächste Schritte
+ Häufig gestellte Fragen zum Azure Backup-Dienst sind [hier](backup-azure-backup-faq.md) aufgeführt.
+ Besuchen Sie das [Azure Sicherungs-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=July15_HO4-->