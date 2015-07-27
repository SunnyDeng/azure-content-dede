<properties
	pageTitle="Einführung in Azure Backup"
	description="Dieser Artikel bietet einen Überblick über den Azure Backup-Dienst, mit dem Kunden Daten in Azure sichern können."
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2015"
	ms.author="prvijay"/>

# Einführung in Azure Backup
Dieser Artikel bietet einen allgemeinen Überblick über die Cloud-integrierte Sicherungslösung von Microsoft, mit der Kunden ihre lokalen Daten in Azure sichern können.

## Was ist Azure Backup?
Azure Backup ist ein Azure-Mehrinstanzendienst, mit dem Sie Ihre lokalen Daten in Azure sichern können. Ihre vorhandene lokale oder Offsite-Sicherungslösung wird durch eine zuverlässige und sichere Cloud-basierte Lösung mit wettbewerbsfähigen Kosten ersetzt. Azure Backup baut auf einer erstklassigen Infrastruktur auf, die skalierbar, robust und hoch verfügbar ist. Mit dieser Lösung können Sie Daten und Anwendungen von ihren System Center Data Protection Manager (SCDPM)-Servern, Windows-Servern oder Windows-Clientcomputern sichern. Azure Backup und SCDPM bilden die technologischen Grundlagen der Cloud-basierten Microsoft-Sicherungslösung.

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


## Anwendungen und Arbeitslasten, die in Azure gesichert werden können
In Kombination mit SCDPM lässt sich mit Azure Backup Folgendes sichern:

+ Dateien und Ordner von Client- und Server-Computern der Enterprise-Klasse

+ VM-Sicherungen auf Hostebene von virtuellen Microsoft Hyper-V-Computern

+ Microsoft SQL Server

+ Microsoft SharePoint

+ Microsoft Exchange

## Nächste Schritte
+ Häufig gestellte Fragen zum Azure Backup-Dienst sind [hier](backup-azure-backup-faq.md) aufgeführt.
+ Besuchen Sie das [Azure Sicherungs-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).
 

<!---HONumber=July15_HO3-->