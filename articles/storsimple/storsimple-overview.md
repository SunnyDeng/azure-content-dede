<properties 
   pageTitle="Was ist StorSimple?" 
   description="Beschreibt StorSimple-Features und -Architektur und stellt die StorSimple-Komponenten vor." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/14/2015"
   ms.author="v-sharos@microsoft.com"/>

# Was ist StorSimple? 

## Übersicht

Microsoft Azure StorSimple ist eine effiziente, kostengünstige und einfach zu verwaltende Lösung, die viele der Probleme und Kosten im Zusammenhang mit der Datenspeicherung in Unternehmen und dem Datenschutz beseitigt. Die Lösung verwendet ein proprietäres Gerät (das Microsoft Azure StorSimple-Gerät), die Integration mit Clouddiensten und einen Satz integrierter Verwaltungstools, um eine transparente Ansicht aller Speicher im Unternehmen einschließlich Cloud-Speicher zu ermöglichen.

StorSimple verwendet zur Verwaltung von gespeicherten Daten auf verschiedenen Speichermedien eine Speicherstaffelung. Der aktuelle Arbeitssatz wird lokal auf Solid-State-Laufwerken (SSDs) gespeichert, weniger häufig verwendete Daten auf Festplattenlaufwerken (HDDs), und Archivdaten werden in die Cloud verschoben. Darüber hinaus nutzt StorSimple die Deduplizierung und Komprimierung, um die Menge an Speicher zu reduzieren, den die Daten nutzen. Die Speicherstaffelung erfolgt folgendermaßen:

1. Ein Systemadministrator richtet ein Microsoft Azure-Cloudspeicherkonto ein.
2. Der Administrator verwendet die serielle Konsole sowie den StorSimple Manager-Dienst (der im Azure-Verwaltungsportal ausgeführt wird) zum Konfigurieren des Geräts und des Dateiservers, und er erstellt Volumes und Richtlinien zur Datensicherheit. Der lokale Dateiserver verwendet die iSCSI-Schnittstelle (Internet Small Computer System Interface) auf dem StorSimple-Gerät.
3. StorSimple speichert zunächst Daten auf der schnellen SSD-Ebene des Geräts.
4. Wenn die SSD-Ebene sich der Kapazitätsgrenze nähert, dupliziert und komprimiert StorSimple die ältesten Datenblöcke und verschiebt sie auf die HDD-Ebene.
5. Wenn sich die HDD-Ebene der Kapazitätsgrenze nähert, verschlüsselt StorSimple die ältesten Datenblöcke und sendet diese sicher per HTTPS an das Microsoft Azure-Speicherkonto.
6. Microsoft Azure erstellt mehrere Replikate der Daten im Rechenzentrum und einem Remoterechenzentrum, um sicherzustellen, dass die Daten wiederhergestellt werden können, wenn ein Notfall eintritt. 
7. Wenn der Dateiserver in der Cloud gespeicherte Daten anfordert, gibt StorSimple diese nahtlos zurück und speichert eine Kopie auf der SSD-Ebene des StorSimple-Geräts.

Zusätzlich zur Speicherverwaltung ermöglichen Ihnen StorSimple-Funktionen zum Schutz von Daten das Erstellen bedarfsgesteuerter und geplanter Sicherungen, und deren Speicherung entweder lokal oder in der Cloud. Sicherungen erfolgen in Form von inkrementellen Momentaufnahmen, sodass sie schnell erstellt und wiederhergestellt werden können. Cloud-Momentaufnahmen können bei Wiederherstellungen im Notfall extrem wichtig sein, da sie sekundäre Speichersystemen (z. B. Backup auf Bandlaufwerken) ersetzen und es Ihnen ermöglichen, Daten bei Bedarf in Ihrem Rechenzentrum oder an anderen Standorten wiederherzustellen.

>[AZURE.NOTE]Die StorSimple 8000-Serie mit Software Update 1 oder höher unterstützt Amazon S3 mit RRS, HP und OpenStack-Clouddiensten sowie Microsoft Azure. (Sie benötigen immer noch ein Microsoft Azure-Speicherkonto zur Geräteverwaltung.) Weitere Informationen finden Sie unter [Konfigurieren eines neuen Speicherkontos für den Dienst](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).

## Gründe für die Verwendung von StorSimple

Microsoft Azure StorSimple bietet die folgenden Vorteile:

- **Transparente Integration** – Microsoft Azure StorSimple verwendet das iSCSI-Protokoll, um Datenspeicher unsichtbar miteinander zu verknüpfen. Dies sorgt dafür, dass Daten, die in der Cloud, im Datencenter oder auf Remoteservern gespeichert sind, dem Anschein nach an einem zentralen Speicherort gespeichert werden.
- **Reduzierte Speicherkosten** – Microsoft Azure StorSimple reserviert ausreichend lokalen oder Cloudspeicher, um aktuelle Anforderungen zu erfüllen, und erweitert Cloudspeicher nur bei Bedarf. Außerdem werden Speicheranforderungen und -kosten durch die Beseitigung redundanter Versionen der gleichen Daten (Deduplizierung) und eine Komprimierung noch weiter verringert.
- **Vereinfachte Speicherverwaltung** – Microsoft Azure StorSimple stellt Systemverwaltungstools bereit, mit denen Sie lokal, auf einem Remoteserver und in der Cloud gespeicherte Daten konfigurieren und verwalten können. Darüber hinaus können Sie Backup- und Wiederherstellungsfunktionen über ein Microsoft Management Console-Snap-In verwalten. StorSimple bietet eine separate, optionale Schnittstelle, die Sie zum Erweitern der StorSimple-Verwaltungs- und Datenschutzdienste auf Inhalte nutzen können, die auf SharePoint-Servern gespeichert sind. 
- **Verbesserte Notfallwiederherstellung und Vorschrifteneinhaltung** – Microsoft Azure StorSimple weist keine verlängerte Wiederherstellungszeit auf. Stattdessen stellt die Lösung Daten nach Bedarf wieder her. Dies bedeutet, dass normale Vorgänge mit minimaler Unterbrechung fortgesetzt werden können. Darüber hinaus können Sie Richtlinien zum Angeben von Sicherungszeitplänen und zur Aufbewahrung von Daten konfigurieren.
- **Datenmobilität** – Auf in Microsoft Azure Cloud Services hochgeladene Daten kann für Wiederherstellungs- und Migrationszwecke von anderen Standorten aus zugegriffen werden. Darüber hinaus können Sie mithilfe von StorSimple virtuelle StorSimple-Geräte auf virtuellen Computern (VMs) konfigurieren, die in Microsoft Azure ausgeführt werden. Die virtuellen Computer nutzen dann für Test-oder Wiederherstellungszwecke virtuelle Geräte für den Zugriff auf gespeicherte Daten. 

Das folgende Diagramm bietet einen allgemeinen Überblick über die Microsoft Azure StorSimple-Lösung.

![StorSimple-Architektur](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Microsoft Azure StorSimple-Architektur**

## StorSimple-Komponenten

Die Microsoft Azure StorSimple-Lösung umfasst die folgenden Komponenten:

- **Microsoft Azure StorSimple-Gerät** – Ein lokales Hybridspeicherarray mit Solid-State-Laufwerken (SSDs) und Festplattenlaufwerken (HDDs) sowie redundanten Controllern und automatischen Failoverfunktionen. Die Controller verwalten die Speicherstaffelung, wobei derzeit verwendete (d. h. aktiv genutzte) Daten in lokalem Speicher (auf dem Gerät oder lokalen Server) abgelegt werden, während weniger häufig verwendete Daten in die Cloud verschoben werden.
- **Virtuelles StorSimple-Gerät** – Dies ist eine Softwareversion des StorSimple-Geräts, die die Architektur und Funktionen des physischen Hybridspeichergeräts repliziert. Das virtuelle StorSimple-Gerät wird auf einem einzelnen Knoten in einem virtuellen Azure-Computer ausgeführt. Das virtuelle Gerät eignet sich für Test-und kleine Pilotprojektszenarien. Ein virtuelles StorSimple-Gerät kann nicht auf einem StorSimple-Gerät oder einem lokalen Server erstellt werden.
- **Windows PowerShell für StorSimple** – Eine Befehlszeilenschnittstelle, mit der Sie das StorSimple-Gerät verwalten können. Windows PowerShell für StorSimple bietet Features, mit denen Sie Ihr StorSimple-Gerät registrieren, die Netzwerkschnittstelle Ihres Geräts konfigurieren, bestimmte Arten von Updates installieren, Probleme Ihres Geräts durch Zugriff auf eine Supportsitzung beheben und den Gerätestatus ändern können. Sie können auf Windows PowerShell für StorSimple zugreifen, indem Sie eine Verbindung mit der seriellen Konsole herstellen oder Windows PowerShell-Remoting verwenden.
- **Azure PowerShell-Cmdlets für StorSimple** – eine Auflistung von Windows PowerShell-Cmdlets, die es Ihnen ermöglichen, Servicelevel- und Migrationsaufgaben über die Befehlszeile zu automatisieren. Weitere Informationen zu den Azure PowerShell-Cmdlets für StorSimple finden Sie unter [Cmdlet-Referenz](https://msdn.microsoft.com/library/dn920427.aspx).
- **StorSimple-Manager-Dienst** – Eine Erweiterung des Azure-Verwaltungsportals, mit der Sie ein StorSimple-Gerät oder virtuelles StorSimple-Gerät über eine einzelne Weboberfläche verwalten können. Sie können den StorSimple-Manager-Dienst nutzen, um Dienste zu erstellen und zu verwalten, Geräte anzuzeigen und zu verwalten, Warnungen anzuzeigen, Volumes zu verwalten und Sicherungsrichtlinien und den Sicherungskatalog anzuzeigen und zu verwalten.
- **StorSimple Snapshot Manager** – Ein MMC-Snap-In, das Volumegruppen und den Volumeschattenkopie-Dienst von Windows verwendet, um anwendungskonsistente Backups zu generieren. Darüber hinaus können Sie den StorSimple-Momentaufnahmen-Manager zum Erstellen von Sicherungszeitplänen und Klonen oder Wiederherstellen von Volumes einsetzen. 
- **StorSimple-Adapter für SharePoint** – Ein Tool, das den Microsoft Azure StorSimple-Speicher und den Datenschutz auf SharePoint Server-Farmen transparent erweitert, während der Azure StorSimple-Speicher über das SharePoint-Verwaltungsportal angezeigt und verwaltet werden kann.

## Nächste Schritte

Lesen Sie die [StorSimple-Lösungsübersicht zur Serie 8000](http://www.microsoft.com/server-cloud/products/storsimple/resources.aspx) und schauen Sie sich den [StorSimple-Vortrag](http://www.microsoft.com/server-cloud/products/storsimple/Features.aspx) an.

Erfahren Sie mehr über [StorSimple-Komponenten und- Terminologie](storsimple-components.md).


 

<!---HONumber=August15_HO8-->