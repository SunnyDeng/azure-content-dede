<properties 
   pageTitle="Was ist StorSimple?" 
   description="Beschreibt StorSimple-Features und -Architektur und stellt die StorSimple-Komponenten vor." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="05/27/2015"
   ms.author="v-sharos@microsoft.com"/>

# Was ist StorSimple? 

Microsoft Azure StorSimple ist eine effiziente, kostengünstige und einfach zu verwaltende Lösung, die viele der Probleme und Kosten im Zusammenhang mit der Datenspeicherung in Unternehmen und dem Datenschutz beseitigt. Die Lösung verwendet ein proprietäres Gerät (das Microsoft Azure StorSimple-Gerät) und integrierte Verwaltungstools, um eine transparente Ansicht aller Speicher im Unternehmen einschließlich Cloud-Speicher zu ermöglichen.

## Gründe für die Verwendung von StorSimple

Microsoft Azure StorSimple bietet die folgenden Vorteile:

- **Transparente Integration** – Microsoft Azure StorSimple verwendet das iSCSI-Protokoll (Internet Small Computer System Interface), um Datenspeicher unsichtbar miteinander zu verknüpfen. Dies sorgt dafür, dass Daten, die in der Cloud, im Datencenter oder auf Remoteservern gespeichert sind, dem Anschein nach an einem zentralen Speicherort gespeichert werden.
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

Erfahren Sie mehr über die[StorSimple-Komponenten](storsimple-components.md).



 

<!---HONumber=July15_HO4-->