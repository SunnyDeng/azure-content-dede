<properties
   pageTitle="StorSimple Virtual Array – Übersicht | Microsoft Azure"
   description="Enthält eine Beschreibung von StorSimple Virtual Array. Hierbei handelt es sich um eine integrierte Speicherlösung, mit der Speicheraufgaben zwischen einem lokalen virtuellen Gerät und dem Microsoft Azure-Cloudspeicher verwaltet werden."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="03/01/2016"
   ms.author="alkohli" />

# Einführung in das StorSimple Virtual Array

## Übersicht

Willkommen beim Microsoft Azure StorSimple Virtual Array. Hierbei handelt es sich um eine integrierte Speicherlösung, mit der Speicheraufgaben zwischen einem lokalen virtuellen Gerät, das in einem Hypervisor ausgeführt wird, und dem Microsoft Azure-Cloudspeicher verwaltet werden. Das Virtual Array (auch als lokales virtuelles StorSimple-Gerät bezeichnet) ist eine effiziente, kosteneffektive und leicht verwaltbare Dateiserver- oder iSCSI-Serverlösung, mit der viele Probleme und Ausgaben in Bezug auf die Speicherung und den Schutz von Daten in Unternehmen beseitigt werden. Das Virtual Array ist besonders gut für Szenarios mit weit auseinanderliegenden Büros bzw. Zweigniederlassungen geeignet.

In dieser Übersicht geht es um das Virtual Array.

- Eine Übersicht über StorSimple-Geräte der 8000er Serie finden Sie unter [StorSimple 8000er Serie: Hybridcloud-Lösung](storsimple-overview.md). 

- Informationen zu den Geräten der StorSimple 5000/7000er Serie finden Sie unter [StorSimple-Onlinehilfe](http://onlinehelp.storsimple.com/).

Das Virtual Array unterstützt das iSCSI- oder Server Message Block-Protokoll (SMB). Es wird in Ihrer vorhandenen Hypervisor-Infrastruktur ausgeführt und bietet Staffelung in der Cloud, Cloudsicherung, schnelle Wiederherstellung, Wiederherstellung auf Elementebene und Features für die Notfallwiederherstellung.

In der folgenden Tabelle sind die wichtigen Features des Virtual Array zusammengefasst.

| Funktion | Virtual Array |
| ------- | ------------- |
|Installationsanforderungen | Verwendung der Virtualisierungsinfrastruktur (Hyper-V oder VMware)|
| Availability | Einzelner Knoten |
| Gesamtkapazität (einschließlich Cloud) |Bis zu 64 TB nutzbare Kapazität pro virtuellem Gerät |
| Lokale Kapazität | 390 GB bis 6,4 TB nutzbare Kapazität pro virtuellem Gerät (Bereitstellung von Datenträger-Speicherplatz im Bereich 500 GB bis 8 TB erforderlich)|
| Systemeigene Protokolle | iSCSI oder SMB |
| Recovery Time Objective (RTO) | iSCSI: weniger als 2 Minuten, unabhängig von der Größe |
| Recovery Point Objective (RPO) | Tägliche Sicherungen und bedarfsgesteuerte Sicherungen |
| Speicherstaffelung | Verwendung von Heat Maps zum Ermitteln, welche Daten einbezogen bzw. nicht einbezogen werden sollen |
| Support | Vom Anbieter unterstützte Virtualisierungsinfrastruktur |
| Leistung | Variiert je nach zugrunde liegender Infrastruktur |
| Datenmobilität | Wiederherstellung auf demselben Gerät oder Wiederherstellung auf Elementebene (Dateiserver) |
| Speicherebenen | Lokale Hypervisor-Speicherung und Cloud |
| Freigabe- oder Volumegröße |Mehrstufig: bis zu 20 TB; Lokal: bis zu 2 TB |
| Momentaufnahmen | Absturzkonsistenz |
| Wiederherstellung auf Elementebene | Ja, Benutzer können von Freigaben wiederherstellen |

## Gründe für die Verwendung von StorSimple

StorSimple verbindet Benutzer und Server in wenigen Minuten mit Azure-Speicher – ohne Änderung der Anwendung.

In der folgenden Tabelle sind einige wichtige Vorteile der Virtual Array-Lösung aufgeführt.

| Funktion | Vorteil |
|---------|---------|
| Transparente Integration | Das Virtual Array unterstützt das iSCSI- oder SMB-Protokoll. Dies sorgt dafür, dass Daten, die in der Cloud, im Datencenter oder auf Remoteservern gespeichert sind, dem Anschein nach an einem zentralen Speicherort gespeichert werden.|
| Reduzierte Speicherkosten | Mit StorSimple stellen Sie jeweils ausreichend lokalen Speicher bereit, um die aktuellen Anforderungen zu erfüllen. Wenn die Speicheranforderungen steigen, ordnet StorSimple die Daten im kostengünstigen Cloudspeicher an. In der Cloud werden die Deduplizierung und Komprimierung verwendet, um die Speicheranforderungen und -kosten weiter zu reduzieren.|
| Vereinfachte Speicherverwaltung | StorSimple ermöglicht die zentralisierte Verwaltung in der Cloud. Sie können die webbasierten Tools verwenden, um im Virtual Array gespeicherte Daten, Daten auf einem Remoteserver und Daten in der Cloud zu konfigurieren und zu verwalten.| 
| Verbesserte Notfallwiederherstellung und Vorschrifteneinhaltung | Für StorSimple ist keine verlängerte Wiederherstellungszeit erforderlich. Stattdessen werden Metadaten sofort und gespeicherte Daten je nach Bedarf wiederhergestellt. Dies bedeutet, dass normale Vorgänge mit minimaler Unterbrechung fortgesetzt werden können.|
| Datenmobilität | Auf die in der Cloud auf verschiedenen Ebenen angeordneten Daten kann über andere Standorte zu Wiederherstellungs- und Migrationszwecken zugegriffen werden. Beachten Sie, dass Sie Daten nur auf dem ursprünglichen Virtual Array wiederherstellen können. Sie verwenden aber Features für die Notfallwiederherstellung, um das gesamte Virtual Array auf einem anderen Virtual Array wiederherzustellen.|

## Workflows

Das StorSimple Virtual Array eignet sich besonders gut für die folgenden Workflows:

- [Cloudbasierte Speicherverwaltung](#cloud-based-storage-management)
- [Standortunabhängige Sicherungen](#location-independent-backup)
- [Schutz von Daten und Notfallwiederherstellung](#data-protection-and-disaster-recovery)

### Cloudbasierte Speicherverwaltung

Sie können den im klassischen Azure-Portal ausgeführten StorSimple Manager-Dienst verwenden, um Daten zu verwalten, die auf mehreren Geräten und an mehreren Standorten gespeichert sind. Dies ist besonders bei Szenarien mit weit auseinanderliegenden Zweigstellen hilfreich. Beachten Sie, dass Sie separate Instanzen des StorSimple Manager-Diensts erstellen müssen, um Virtual Arrays und physische StorSimple-Geräte zu verwalten.

![Cloudbasierte Speicherverwaltung](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### Standortunabhängige Sicherungen

Beim Virtual Array ermöglichen Cloudmomentaufnahmen die Erstellung einer standortunabhängigen Point-in-Time-Kopie eines Volumes oder einer Freigabe. Cloudmomentaufnahmen sind standardmäßig aktiviert und können nicht deaktiviert werden. Alle Volumes und Freigaben werden über eine Richtlinie für die tägliche Sicherung gleichzeitig gesichert, und Sie können jederzeit zusätzliche Ad-hoc-Sicherungen erstellen, wenn dies erforderlich ist.

### Schutz von Daten und Notfallwiederherstellung

Das Virtual Array unterstützt die folgenden Szenarien für den Schutz der Daten und die Notfallwiederherstellung:

- **Wiederherstellung von Volumes oder Freigaben:** Verwenden Sie die Wiederherstellung als neuen Workflow zum Wiederherstellen eines Volumes oder einer Freigabe. Nutzen Sie diesen Ansatz, um das gesamte Volume oder die Freigabe wiederherzustellen.
- **Wiederherstellung auf Elementebene:** Freigaben ermöglichen den vereinfachten Zugriff auf die letzten Sicherungen. Sie können eine einzelne Datei leicht über einen speziellen Ordner mit der Bezeichnung „.backup“ in der Cloud wiederherstellen. Diese Wiederherstellungsfunktion ist benutzergesteuert, und es ist kein Eingreifen des Administrators erforderlich.
- **Notfallwiederherstellung:** Verwenden Sie die Failoverfunktion, um alle Volumes oder Freigaben auf einem neuen Virtual Array wiederherzustellen. Sie erstellen ein neues Virtual Array und registrieren es beim StorSimple Manager-Dienst. Anschließend führen Sie das Failover für das ursprüngliche Virtual Array durch. Das neue Virtual Array übernimmt dann die bereitgestellten Ressourcen. 

## Virtual Array-Komponenten

Das Virtual Array umfasst die folgenden Komponenten:

- [Virtual Array:](#virtual-array) ein Hybrid-Cloudspeichergerät basierend auf einem virtuellen Computer, der in Ihrer virtualisierten Umgebung oder im Hypervisor bereitgestellt wird.  
- [StorSimple Manager-Dienst:](#storsimple-manager-service) eine Erweiterung des klassischen Azure-Portals, mit der Sie ein oder mehrere StorSimple-Geräte über eine zentrale Webschnittstelle verwalten können, auf die Zugriff von verschiedenen geografischen Standorten aus besteht. Sie können den StorSimple Manager-Dienst verwenden, um Dienste zu erstellen und zu verwalten, Geräte und Warnungen anzuzeigen und zu verwalten und Volumes, Freigaben und vorhandene Momentaufnahmen zu verwalten.
- [Lokale Webbenutzeroberfläche:](#local-web-user-interface) eine webbasierte UI, die zum Konfigurieren des Geräts verwendet wird. So kann die Verbindung mit dem lokalen Netzwerk hergestellt werden, und anschließend kann das Gerät beim StorSimple Manager-Dienst registriert werden. 
- [Befehlszeilenschnittstelle:](#command-line-interface) eine Windows PowerShell-Schnittstelle, die Sie zum Starten einer Supportsitzung auf dem Virtual Array verwenden können. In den folgenden Abschnitten wird jede dieser Komponenten ausführlicher beschrieben. Es wird zudem erläutert, wie die Lösung Daten anordnet, Speicher zuweist sowie die Speicherverwaltung und den Schutz von Daten vereinfacht.

### Virtual Array

Das Virtual Array ist eine Speicherlösung mit einem Knoten, die Folgendes ermöglicht: primäre Speicherung, Verwaltung der Kommunikation mit dem Cloudspeicher und Gewährleistung der Sicherheit und Vertraulichkeit aller Daten, die auf dem Gerät gespeichert sind.

Das Virtual Array ist als Modell verfügbar, das heruntergeladen werden kann. Das Storage Array hat eine maximale Kapazität von 6,4 TB auf dem Gerät (mit einer zugrunde liegenden Speicheranforderung von 8 TB) und – einschließlich des Cloudspeichers – von 64 TB.

Das Virtual Array bietet die folgenden Features:

- Es ist kostengünstig. Es nutzt Ihre vorhandene Virtualisierungsinfrastruktur und kann auf Ihrem vorhandenen Hyper-V- oder VMware-Hypervisor bereitgestellt werden.
- Es befindet sich im Rechenzentrum und kann als iSCSI-Server oder Dateiserver konfiguriert werden. 
- Es ist in die Cloud integriert.
- Sicherungen werden in der Cloud gespeichert, um die Notfallwiederherstellung und Wiederherstellung auf Elementebene zu vereinfachen. 
- Sie können Updates auf das Virtual Array anwenden, wie Sie dies auch für ein physisches Gerät durchführen.

>[AZURE.NOTE] Ein Virtual Array kann nicht erweitert werden. Daher ist es wichtig, ausreichend Speicherplatz bereitzustellen, wenn Sie das virtuelle Gerät erstellen.

### StorSimple Manager-Dienst

Microsoft Azure StorSimple stellt eine webbasierte Benutzeroberfläche (den StorSimple-Manager-Dienst) zur Verfügung, die die zentrale Verwaltung des Datencenters und Cloudspeichers ermöglicht. Mithilfe des StorSimple-Manager-Diensts können Sie die folgenden Aufgaben ausführen:

- Verwalten von mehreren StorSimple Virtual Arrays über einen zentralen Dienst 
- Konfigurieren und Verwalten von Sicherheitseinstellungen für StorSimple-Geräte (Die Verschlüsselung in der Cloud richtet sich nach den Microsoft Azure-APIs.)
- Konfigurieren der Anmeldeinformationen und Eigenschaften für das Speicherkonto
- Konfigurieren und Verwalten der Volumes oder Freigaben
- Sichern und Wiederherstellen von Daten auf Volumes oder Freigaben
- Überwachen der Leistung
- Überprüfen von Systemeinstellungen und Bestimmen möglicher Probleme

Sie verwenden den StorSimple Manager-Dienst für die tägliche Verwaltung des Virtual Array.

Weitere Informationen finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

### Lokale Webbenutzeroberfläche

Das Virtual Array enthält eine webbasierte Benutzeroberfläche, die für die einmalige Konfiguration und Registrierung des Geräts beim StorSimple-Manager-Dienst verwendet wird. Sie können sie zum Herunterfahren und Neustarten des Virtual Array, Ausführen von Diagnosetests, Aktualisieren von Software, Ändern des Geräteadministratorkennworts, Anzeigen von Systemprotokollen und Senden einer Serviceanfrage an den Microsoft Support verwenden.

Informationen zur Verwendung der webbasierten Benutzeroberfläche finden Sie unter [Verwenden der webbasierten Benutzeroberfläche zum Verwalten des StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

### Befehlszeilenschnittstelle

Mit der integrierten Windows PowerShell-Schnittstelle können Sie eine Supportsitzung mit dem Microsoft Support initiieren, um Hilfe bei der Problembehandlung und beim Lösen von Problemen zu erhalten, die auf dem virtuellen Gerät auftreten können.

## Speicherverwaltungstechnologien

Zusätzlich zum Virtual Array und anderen Komponenten nutzt die StorSimple-Lösung die folgende Softwaretechnologie, um den schnellen Zugriff auf wichtige Daten zu ermöglichen, den Speicherverbrauch zu verringern und auf dem Virtual Array gespeicherte Daten zu schützen:

- [Automatische Speicherstaffelung](#automatic-storage-tiering) 
- [Lokale Freigaben und Volumes](#locally-pinned-shares-and-volumes)
- [Deduplizierung und Komprimierung für in der Cloud angeordnete oder gesicherte Daten](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [Geplante und bedarfsgesteuerte Sicherungen](#scheduled-and-on-demand-backups)

### Automatische Speicherstaffelung

Für das Virtual Array wird ein neuer Staffelungsmechanismus zum Verwalten gespeicherter Daten im Virtual Array und in der Cloud verwendet. Es gibt nur zwei Ebenen: das lokale Virtual Array und den Azure-Cloudspeicher. Das StorSimple Virtual Array ordnet die Daten basierend auf einer Heat Map automatisch auf den Ebenen an. Mit der Heat Map werden die aktuelle Nutzung, das Alter und die Beziehungen mit anderen Daten nachverfolgt. Daten, die am aktivsten („hot“) sind, werden lokal gespeichert, während weniger aktive und inaktive Daten automatisch in die Cloud migriert werden. (Alle Sicherungen werden in der Cloud gespeichert.) StorSimple passt Daten- und Speicherzuordnungen an und ordnet diese neu, wenn sich die Verwendungsmuster ändern. Im Lauf der Zeit können einige Informationen z. B. weniger aktiv werden. Wenn der Aktivitätsgrad immer mehr absinkt, werden sie in die Cloud verlagert. Wenn diese Daten dann wieder aktiv werden, werden sie im Storage Array angeordnet.

Für Daten einer bestimmten mehrstufigen Freigabe oder eines Volumes ist eigener Speicherplatz auf der lokalen Ebene garantiert (ca. 10 % des insgesamt bereitgestellten Speicherplatzes einer Freigabe bzw. eines Volumes). Hierdurch wird zwar der verfügbare Speicher auf dem virtuellen Gerät für die Freigabe oder das Volume reduziert, aber es wird auch sichergestellt, dass die Staffelung für eine Freigabe oder ein Volume nicht durch die Staffelungsanforderungen anderer Freigaben oder Volumes beeinträchtigt wird. Daher kann es nicht passieren, dass eine Workload mit hoher Auslastung auf einer Freigabe oder einem Volume alle anderen Workloads in die Cloud verdrängt.

![Automatische Speicherstaffelung](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE] Sie können für ein Volume angeben, dass es nur lokal vorhanden sein soll. In diesem Fall verbleiben die Daten auf dem Virtual Array und werden nicht in der Cloud angeordnet. Weitere Informationen finden Sie unter [Lokale Freigaben und Volumes](#locally-pinned-shares-and-volumes).

### Lokale Freigaben und Volumes

Sie können bestimmte Freigaben und Volumes als lokale Komponenten erstellen. Mit dieser Funktion wird sichergestellt, dass von kritischen Anwendungen erforderliche Daten im Virtual Array verbleiben und nicht in die Cloud ausgelagert werden. Lokale Freigaben und Volumes verfügen über die folgenden Features:

- Sie unterliegen keinen Cloudlatenzen oder Konnektivitätsproblemen.
- Sie profitieren dennoch von den Features für die StorSimple-Cloudsicherung und -Notfallwiederherstellung.

Sie können eine lokale Freigabe bzw. ein Volume als mehrstufiges Element oder eine mehrstufige Freigabe bzw. ein Volume als lokales Element wiederherstellen.

Weitere Informationen zu lokalen Volumes finden Sie unter [Verwalten von Volumes mithilfe des StorSimple Manager-Diensts](storsimple-manage-volumes-u2.md).

### Deduplizierung und Komprimierung für in der Cloud angeordnete oder gesicherte Daten

StorSimple arbeitet mit Deduplizierung und Datenkomprimierung, um die Speicheranforderungen in der Cloud weiter zu reduzieren. Durch Deduplizierung wird die Datenmenge verringert, indem Redundanz im gespeicherten Dataset beseitigt wird. Wenn sich Informationen ändern, ignoriert Azure StorSimple die unveränderten Daten und erfasst nur die Änderungen. Außerdem verringert StorSimple die Menge der gespeicherten Daten, indem doppelte Informationen ermittelt und entfernt werden.

>[AZURE.NOTE] Im Virtual Array gespeicherte Daten werden nicht dedupliziert oder komprimiert. Die Deduplizierung und Komprimierung wird jeweils direkt vor dem Senden der Daten in die Cloud durchgeführt.

### Geplante und bedarfsgesteuerte Sicherungen

Mit StorSimple-Features zum Schutz der Daten können Sie bedarfsgesteuerte Sicherungen erstellen. Außerdem wird mit einem standardmäßigen Sicherungszeitplan dafür gesorgt, dass die Daten täglich gesichert werden. Sicherungen werden in Form von inkrementellen Momentaufnahmen erstellt, die in der Cloud gespeichert werden. Momentaufnahmen, bei denen nur die Änderungen seit der letzten Sicherung aufgezeichnet werden, können schnell erstellt und wiederhergestellt werden. Diese Momentaufnahmen können bei Wiederherstellungen im Notfall extrem wichtig sein, da sie sekundäre Speichersysteme (z. B. Backup auf Bandlaufwerken) ersetzen und es Ihnen ermöglichen, Daten bei Bedarf in Ihrem Rechenzentrum oder an anderen Standorten wiederherzustellen.

## Nächste Schritte

Erfahren Sie, wie Sie das [Virtual Array-Portal vorbereiten](storsimple-ova-deploy1-portal-prep.md).

<!---HONumber=AcomDC_0302_2016-->