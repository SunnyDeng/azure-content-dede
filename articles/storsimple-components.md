<properties 
   pageTitle="Was sind die Komponenten von StorSimple?" 
   description="Beschreibung der StorSimple-Geräte, -Dienste und -Verwaltungstechnologien." 
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
   ms.date="02/17/2015"
   ms.author="v-sharos@microsoft.com"/>


# Was sind die Komponenten von StorSimple? 

Willkommen bei Microsoft Azure StorSimple, einer integrierten Speicherlösung, die Speicheraufgaben zwischen lokalen Geräten und Microsoft Azure-Cloud-Speicher verwaltet.  StorSimple ist darauf ausgelegt, Speicherkosten zu senken, die Speicherverwaltung zu vereinfachen, die Fähigkeiten zur Notfallwiederherstellung und deren Effizienz zu verbessern und Datenmobilität zu ermöglichen.

In den folgenden Abschnitten werden die Microsoft Azure StorSimple-Komponenten beschrieben. Es wird erläutert, wie die Lösung Daten anordnet, Speicher zuweist sowie die Speicherverwaltung und den Datenschutz vereinfacht. 

> [AZURE.NOTE] Die auf der Microsoft Azure-Website veröffentlichten StorSimple-Bereitstellungsinformationen gelten nur für Geräte der StorSimple 8000-Reihe. Informationen zu der 7000-er Reihe finden Sie unter: [StorSimple-Hilfe](http://onlinehelp.storsimple.com/).

## StorSimple-Gerät

Das Microsoft Azure StorSimple-Gerät ist ein lokales Hybridspeicherarray, das primären Speicher und iSCSI-Zugriff auf an anderer Stelle gespeicherte Daten bereitstellt. Es verwaltet die Kommunikation mit Cloud-Speicher und gewährleistet die Sicherheit und Vertraulichkeit aller Daten, die in der Microsoft Azure StorSimple-Lösung gespeichert sind.

Zum StorSimple-Gerät gehören Solid-State-Laufwerke (SSDs) und Festplattenlaufwerken (HDDs) sowie Unterstützung für Clustering und automatisches Failover. Das Gerät enthält einen gemeinsam genutzten Prozessor, gemeinsam genutzten Speicher sowie zwei gespiegelte Controller. Jeder Controller stellt Folgendes bereit:

- Verbindung mit einem Hostcomputer
- Bis zu sechs Netzwerkanschlüsse zum Herstellen einer Verbindung mit dem lokalen Netzwerk (LAN)
- Hardwareüberwachung
- NVRAM (Non-Volatile Random Access Memory), der Informationen speichert, selbst wenn die Stromzufuhr unterbrochen wird
- Clusterfähiges Aktualisieren für die Verwaltung von Softwareupdates auf Servern in einem Failovercluster, damit die Updates nur minimale oder keine Auswirkungen auf die Dienstverfügbarkeit haben
- Einen Clusterdienst, der wie ein Back-End-Cluster funktioniert und hohe Verfügbarkeit bereitstellt sowie negative Auswirkungen verringert, die ggf. auftreten können, wenn ein HDD oder SSD ausfällt oder offline geschaltet wird

Es ist nur ein Controller gleichzeitig aktiv.  Wenn der aktive Controller ausfällt, wird automatisch der zweite Controller aktiviert. 

Weitere Informationen finden Sie unter [StorSimple-Geräte](https://msdn.microsoft.com/library/azure/dn772363.aspx).

## StorSimple - Virtuelles Gerät

Sie können Azure StorSimple zum Erstellen eines virtuellen Geräts verwenden, das die Architektur und Funktionen des tatsächlichen Hybridspeichergeräts repliziert. 

Das virtuelle StorSimple-Gerät wird auf einem einzelnen Knoten eines virtuellen Azure-Computers ausgeführt. (Ein virtuelles Gerät kann nur auf einem virtuellen Azure-Computer erstellt werden. Es kann nicht auf einem StorSimple-Gerät oder auf einem lokalen Server erstellt werden.) Ein virtuelles StorSimple-Gerät unterscheidet sich auf folgende Weise von einem physischen StorSimple-Gerät: 

- Das virtuelle Gerät verfügt nur über eine Schnittstelle, während das physische Gerät sechs Netzwerkschnittstellen bereitstellt.  
- Sie registrieren das virtuelle Gerät während der Gerätekonfiguration und nicht in einem separaten Vorgang.
- Sie können den Verschlüsselungsschlüssel für Dienstdaten nicht mittels eines virtuellen Geräts neu generieren. Während des Schlüsselrollovers generieren Sie den Schlüssel auf dem physischen Gerät erneut und aktualisieren dann das virtuelle Gerät mit dem neuen Schlüssel.
- Wenn Sie Updates auf das virtuelle Gerät anwenden müssen, treten Ausfallzeiten auf. Dies ist bei einem physischen StorSimple-Gerät nicht der Fall.

Es wird empfohlen, dass Sie das virtuelle StorSimple-Gerät für Wiederherstellungsszenarien nach einem Notfall verwenden, in denen kein physisches Gerät verfügbar ist, z. B. in Test- und kleinen Pilotbereitstellungen.

Weitere Informationen finden Sie unter [Virtuelles StorSimple-Gerät](https://msdn.microsoft.com/library/azure/dn772390.aspx).


## Speicherverwaltungstechnologien
 
Zusätzlich zum dedizierten StorSimple-Gerät und virtuellen Gerät verwendet Microsoft Azure StorSimple die folgenden Softwaretechnologien, um schnellen Zugriff auf Daten bereitzustellen und die Speicherbelegung zu verringern:

- Automatische Speicherstaffelung 
- Schlanke Speicherzuweisung 
- Deduplizierung und Komprimierung 

### Automatische Speicherstaffelung

Microsoft Azure StorSimple ordnet Daten automatisch in logischen Ebenen basierend auf aktueller Nutzung, Alter und Beziehung zu anderen Daten an. Daten, die am aktivsten sind, werden lokal gespeichert, während weniger aktive und inaktive Daten automatisch in die Cloud migriert werden.  Die folgende Abbildung zeigt dieses Speicherkonzept.
 
![StorSimple storage tiers](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**StorSimple-Speicherebenen**

Damit ein schneller Zugriff ermöglicht wird, speichert Azure StorSimple sehr aktive Daten auf SSDs im StorSimple-Gerät. Daten, die gelegentlich verwendet werden, werden auf HDDs im Gerät oder auf Servern im Datencenter gespeichert. Inaktive Daten, Sicherungsdaten und Daten, die für Archivierungs- oder Kompatibilitätszwecke verwaltet werden, werden in der Cloud gespeichert. 

StorSimple passt Daten- und Speicherzuordnungen an und ordnet diese neu, wenn sich die Verwendungsmuster ändern. Im Lauf der Zeit können einige Informationen z. B. weniger aktiv werden. Wenn die betreffenden Informationen kontinuierlich weniger aktiv werden, werden sie von SSD in HDD und dann in die Cloud migriert. Wenn diese Daten erneut aktiv werden, werden sie zurück in das Speichergerät migriert.

### Schlanke Speicherzuweisung

Schlanke Speicherzuweisung ist eine Virtualisierungstechnologie, bei der der verfügbare Speicher die physischen Ressourcen zu überschreiten scheint. Anstatt ausreichend Speicher im Voraus zu reservieren, verwendet StorSimple die schlanke Bereitstellung, um nur eben genug Speicher zum Erfüllen der aktuellen Anforderungen zuzuweisen. Die Elastizität von Cloud-Speicher ermöglicht diesen Ansatz, weil StorSimple den Cloud-Speicher vergrößern oder verkleinern kann, um sich ändernde Anforderungen zu erfüllen. 

### Deduplizierung und Komprimierung

Microsoft Azure StorSimple arbeitet mit Deduplizierung und Datenkomprimierung, um Speicheranforderungen weiter zu verringern.

Durch Deduplizierung wird die Datenmenge verringert, indem Redundanz im gespeicherten Dataset beseitigt wird. Wenn sich Informationen ändern, ignoriert Azure StorSimple die unveränderten Daten und erfasst nur die Änderungen. Außerdem verringert StorSimple die Menge der gespeicherten Daten, indem nicht erforderliche Informationen bestimmt und entfernt werden. 

## WindowsPowerShell für StorSimple

Windows PowerShell für StorSimple stellt eine Befehlszeilen-Schnittstelle zur Verfügung, die Sie zum Erstellen und Verwalten des Microsoft Azure StorSimple-Diensts sowie zum Einrichten und Überwachen von StorSimple-Geräten verwenden können. Es handelt sich um eine auf Windows PowerShell basierende Befehlszeilen-Schnittstelle, die dedizierte Cmdlets zum Verwalten Ihres StorSimple-Geräts umfasst. Mit den Funktionen von Windows PowerShell für StorSimple können Sie die folgenden Aufgaben ausführen:

- Registrieren eines Geräts
- Konfigurieren der Netzwerkschnittstelle auf einem Gerät
- Installieren bestimmter Typen von Updates
- Beheben von Geräteproblemen durch Zugreifen auf die Supportsitzung
- Ändern des Gerätezustands

Sie können auf Windows PowerShell für StorSimple über eine serielle Konsole (auf einem Hostcomputer, der direkt mit dem Gerät verbunden ist) oder remote mithilfe von Windows PowerShell-Remoting zugreifen. Beachten Sie, dass einige der Windows PowerShell für StorSimple-Aufgaben (z. B. die anfängliche Registrierung des Geräts) nur in der seriellen Konsole erfolgen können. 

Weitere Informationen finden Sie unter [Windows PowerShell für StorSimple](https://msdn.microsoft.com/library/azure/dn772425.aspx).

## StorSimple-Manager-Dienst

Microsoft Azure StorSimple stellt eine webbasierte Benutzeroberfläche (den StorSimple-Manager-Dienst) zur Verfügung, die die zentrale Verwaltung des Datencenters und Cloud-Speichers ermöglicht. Mithilfe des StorSimple-Manager-Diensts können Sie die folgenden Aufgaben ausführen:

- Konfigurieren von Systemeinstellungen für StorSimple-Geräte
- Konfigurieren und Verwalten von Sicherheitseinstellungen für StorSimple-Geräte
- Konfigurieren von Cloud-Anmeldeinformationen und -Eigenschaften
- Konfigurieren und Verwalten von Volumes auf einem Server
- Konfigurieren von Volumegruppen
- Sichern und Wiederherstellen von Daten
- Überwachen der Leistung
- Überprüfen von Systemeinstellungen und Bestimmen möglicher Probleme

Sie können den StorSimple-Manager-Dienst zum Ausführen aller Verwaltungsaufgaben mit Ausnahme der Aufgaben verwenden, die einen Systemausfall mit sich bringen (z. B. die anfängliche Einrichtung oder die Installation von Updates).

Weitere Informationen finden Sie unter [StorSimple-Manager-Dienst](https://msdn.microsoft.com/library/azure/dn772396.aspx).

## StorSimple-Momentaufnahmen-Manager

StorSimple-Momentaufnahmen-Manager ist ein MMC-Snap-In (Microsoft Management Console), das Sie zum Erstellen konsistenter zeitpunktbezogener Sicherungskopien von lokalen und Cloud-Daten verwenden können. Das Snap-In wird auf einem auf Windows Server basierenden Host ausgeführt. Sie können den StorSimple-Momentaufnahmen-Manager für die folgenden Aufgaben verwenden:

- Konfigurieren, Sichern und Löschen von Volumes
- Konfigurieren von Volumegruppen, um sicherzustellen, dass gesicherte Daten anwendungskonsistent sind
- Verwalten von Sicherungsrichtlinien, damit Daten gemäß einem vordefinierten Zeitplan gesichert und an einem festgelegten Speicherort (lokal oder in der Cloud) gespeichert werden
- Wiederherstellen von Volumes und einzelnen Dateien

Sicherungen werden als Momentaufnahmen erfasst, die nur die Änderungen seit der letzen Erstellung einer Momentaufnahme aufzeichnen und weit weniger Speicherplatz als vollständige Sicherungen erfordern. Sie können Sicherungspläne erstellen oder bei Bedarf sofortige Sicherungen vornehmen. Außerdem können Sie den StorSimple-Momentaufnahmen-Manager zum Einrichten von Aufbewahrungsrichtlinien verwenden, die steuern, wie viele Momentaufnahmen gespeichert werden. Wenn Sie anschließend Daten aus einer Sicherung wiederherstellen müssen, ermöglicht der StorSimple-Momentaufnahmen-Manager Ihnen die Auswahl aus in einem Katalog mit lokalen oder Cloud-Momentaufnahmen. 

Wenn ein Notfall eintritt oder Daten aus anderen Gründen wiederhergestellt werden müssen, stellt der StorSimple-Momentaufnahmen-Manager diese nach Bedarf inkrementell wieder her. Für die Datenwiederherstellung ist es nicht erforderlich, das gesamte System herunterzufahren, während eine Datei wiederhergestellt, Hardware ausgetauscht oder der Betrieb an einen anderen Standort verlagert wird.

Weitere Informationen finden Sie unter [StorSimple-Momentaufnahmen-Manager](https://msdn.microsoft.com/library/azure/dn772365.aspx).

## StorSimple-Adapter für SharePoint

Microsoft Azure StorSimple enthält den StorSimple-Adapter für SharePoint - eine optionale Komponente, die die Speicher- und Datenschutzfunktionen von StorSimple transparent auf SharePoint-Serverfarmen erweitert. Der Adapter arbeitet mit dem RBS-Anbieter (Remote Blob Storage) und der RBS-Funktion von SQL Server zusammen und ermöglicht das Verschieben von BLOBs auf einen Server, der durch das Microsoft Azure StorSimple-System gesichert wird. Microsoft Azure StorSimple speichert die BLOB-Daten dann basierend auf ihrer Verwendung lokal oder in der Cloud.

Der StorSimple-Adapter für SharePoint wird im Portal der SharePoint-Zentraladministration verwaltet. Aus diesem Grund bleibt die SharePoint-Verwaltung zentralisiert, und der gesamte Speicher scheint sich in der SharePoint-Farm zu befinden.

Weitere Informationen finden Sie unter [StorSimple-Adapter für SharePoint](https://msdn.microsoft.com/library/azure/dn757737.aspx). 


## Nächste Schritte

Überprüfen Sie die [StorSimple-Versionsanmerkungen](https://msdn.microsoft.com/library/azure/dn772367.aspx).






<!--HONumber=52-->