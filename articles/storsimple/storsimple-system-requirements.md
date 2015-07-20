<properties 
   pageTitle="StorSimple-Systemanforderungen" 
   description="Beschreibt Systemanforderungen und bewährte Methoden für Software, hohe Verfügbarkeit und Netzwerke für Azure StorSimple-Lösungen." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="06/03/2015"
   ms.author="alkohli"/>

# StorSimple-Systemanforderungen

## Übersicht

Willkommen bei Microsoft Azure StorSimple In diesem Artikel werden wichtige Systemanforderungen sowie bewährte Methoden für Ihr StorSimple-Gerät und für die Speicherclients beschrieben, die auf das Gerät zugreifen. Diese Anforderungen umfassen u. a.:

- **Softwareanforderungen für Speicherclients:** beschreibt die unterstützten Betriebssysteme und zusätzlichen Anforderungen für diese Betriebssysteme.
- **Anforderungen an hohe Verfügbarkeit für StorSimple:** beschreibt die Anforderungen an hohe Verfügbarkeit und bewährte Methoden für das StorSimple-Gerät und den Hostcomputer. 
- **Netzwerkanforderungen für das StorSimple-Gerät:** bietet Informationen zu den Ports, die in der Firewall für Datenverkehr von iSCSI, Cloud oder Verwaltung geöffnet sein müssen.

Sie sollten die folgenden Informationen sorgfältig überprüfen, bevor Sie Ihr Azure StorSimple-System bereitstellen. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

## Softwareanforderungen für Speicherclients 

Die folgenden Softwareanforderungen gelten für Speicherclients, die auf das StorSimple-Gerät zugreifen.

| Unterstützte Betriebssysteme | Erforderliche Version | Details/Hinweise |
| --------------------------- | ---------------- | ------------- |
| Windows Server | 2008 R2 SP1, 2012, 2012 R2 |<ul><li>StorSimple iSCSI-Volumes werden nur für die Verwendung auf den folgenden Windows-Datenträgertypen unterstützt:<ul><li>einfache Volumes auf Basisdatenträgern</li><li>einfache und gespiegelte Volumes auf dynamischen Datenträgern</li></ul>Die Verwendung von StorSimple-Momentaufnahme-Manager unter Windows Server ist für die Sicherung/Wiederherstellung gespiegelter dynamischer Datenträger und für anwendungskonsistente Sicherungen erforderlich.</li><li>Der StorSimple-Momentaufnahme-Manager wird nur unter Windows Server 2008 R2 SP1 (64 Bit), Windows 2012 R2 und Windows Server 2012 unterstützt. <ul><li>Wenn Sie Windows Server 2012 verwenden, müssen Sie .NET 3.5 bis 4.5 installieren, bevor Sie StorSimple-Momentaufnahme-Manager installieren.</li><li>Wenn Sie Windows Server 2008 R2 SP1 verwenden, müssen Sie Windows Management Framework 3.0 installieren, bevor Sie StorSimple-Momentaufnahme-Manager installieren.</li></ul></li><li>Die schlanke Speicherzuweisung von Windows Server 2012 und ODX-Funktionen werden bei Verwendung eines StorSimple iSCSI-Volumes unterstützt.<ul><li>StorSimple kann nur Volumes mit schlanker Speicherzuweisung erstellen. Es können damit keine vollständig oder teilweise bereitgestellten Volumes erstellt werden.</li><li>Das erneute Formatieren eines mit schlanker Speicherzuweisung erstellten Volumes kann lange dauern. Es wird empfohlen, das Volume zu löschen und dann ein neues zu erstellen, anstatt eine Neuformatierung durchzuführen.</li><li>Wenn Sie das Volume trotzdem formatieren möchten:<ul><li>Führen Sie vor der Neuformatierung den folgenden Befehl aus, um Verzögerungen durch die Speicherplatzrückgewinnung zu vermeiden: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Verwenden Sie nach Abschluss der Formatierung den folgenden Befehl, um die Speicherplatzrückgewinnung wieder zu aktivieren:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Wenden Sie den in KB2870270 beschriebenen Windows Server 2012-Hotfix auf dem Windows Server-Computer an.</li></ul></li></ul><li>Der StorSimple-Adapter für SharePoint wird nur auf SharePoint 2010 und SharePoint 2013 unterstützt. RBS erfordert SQL Server Enterprise Edition, Version 2008 R2 oder 2012.</li></ul>|
| VMWare | Getestet mit VMware vSphere 5.1 als iSCSI-Client | Die VAAI-Blockfunktion wurde mit VMware vSphere v.5.1 auf StorSimple-Geräten getestet. 
| Linux RHEL/CentOS | Unterstützung für Linux-iSCSI-Clients mit Open-iSCSI-Initiator, Versionen 5 und 6 | Nur mit open-iSCSI-Initiator unterstützt. |
| Linux | SUSE Linux 11 | |
 >[AZURE.NOTE]Basierend auf eingeschränkten, intern durchgeführten Tests wird IBM AIX derzeit nicht mit StorSimple unterstützt.

## Anforderungen an hohe Verfügbarkeit für StorSimple

Die Hardwareplattform der StorSimple-Lösung bietet erstklassige Features für Verfügbarkeit und Zuverlässigkeit, die als Grundlage für eine hochverfügbare, fehlertolerante Speicherinfrastruktur in Ihrem Rechenzentrum dienen. Es gibt jedoch Anforderungen und bewährte Methoden, die Sie einhalten sollten, um eine hohe Verfügbarkeit der Azure StorSimple-Lösung sicherzustellen. Überprüfen Sie vor dem Bereitstellen von Azure StorSimple sorgfältig die folgenden Anforderungen und bewährten Methoden für das StorSimple-Gerät und die verbundenen Hostcomputer.

### Anforderungen und bewährte Methoden für eine hohe Verfügbarkeit des StorSimple-Geräts

Lesen Sie die folgenden Informationen sorgfältig durch, um eine hohe Verfügbarkeit des StorSimple-Geräts sicherzustellen.

#### Stromversorgungs- und Kühleinheiten (Power and Cooling Modules, PCMs)

StorSimple-Geräte enthalten redundante Kühleinheiten, die per Hot-Swap ausgetauscht werden können. Jedes PCM verfügt über genügend Kapazität, um das gesamte Gehäuse zu versorgen. Um eine hohe Verfügbarkeit zu gewährleisten, müssen beide Stromversorgungs- und Kühleinheiten installiert sein.

- Verbinden Sie Ihre Stromversorgungs- und Kühleinheiten mit unterschiedlichen Stromquellen, um auch beim Ausfall einer Stromquelle die Verfügbarkeit zu gewährleisten.

- Beim Ausfall einer Stromversorgungs- und Kühleinheit fordern Sie sofort einen Austausch an.

- Entfernen Sie eine fehlerhafte Stromversorgungs- und Kühleinheit nur, wenn Sie das Ersatzteil zur Hand haben und bereit zur Installation sind.

- Entfernen Sie nicht beide Stromversorgungs- und Kühleinheiten gleichzeitig. Die Stromversorgungs- und Kühleinheit enthält den Akku. Wenn Sie beide Stromversorgungs- und Kühleinheiten entfernen, ist kein akkugeschütztes Herunterfahren mehr möglich.

#### Controllermodule

StorSimple-Geräte enthalten redundante Controllermodule, die per Hot-Swap ausgetauscht werden können. Die Controllermodule werden aktiv/passiv ausgeführt. Zu jedem Zeitpunkt ist ein Controllermodul aktiv und dienstbereit, während das anderen Controllermodul passiv ist. Das passive Controllermodul wird eingeschaltet und in Betrieb versetzt, wenn das aktive Controllermodul ausfällt oder entfernt wird. Jedes Controllermodul weist genügend Kapazität auf, um das gesamte Gehäuse zu versorgen. Beide Controllermodule müssen installiert sein, um eine hohe Verfügbarkeit sicherzustellen.

- Stellen Sie sicher, dass jederzeit beide Controllermodule installiert sind.

- Beim Ausfall eines Controllermoduls fordern Sie sofort einen Austausch an.

- Entfernen Sie eine fehlerhaftes Controllermodul nur, wenn Sie das Ersatzteil zur Hand haben und bereit für die Installation sind. Das Entfernen eines Moduls über einen längeren Zeitraum beeinträchtigt die Luftströmung und somit die Kühlung des Systems.

- Stellen Sie sicher, dass die Netzwerkverbindungen auf beiden Controllermodulen identisch sind und dass die verbundenen Netzwerkschnittstellen eine identische Netzwerkkonfigurationen aufweisen.

- Wenn ein Controllermodul ausfällt oder ausgetauscht werden muss, stellen Sie sicher, dass das andere Controllermodul aktiv ist, bevor Sie das fehlerhafte Controllermodul austauschen.

- Entfernen Sie nicht beide Controllermodule zur gleichen Zeit.

- Wenn ein Failover für die Controller durchgeführt wird, dürfen Sie das Standby-Controllermodul nicht herunterfahren oder aus dem Gehäuse entfernen.

- Warten Sie nach einem Failover für die Controller mindestens fünf Minuten, bevor Sie eines der beiden Controllermodule entfernen.

#### Netzwerkschnittstellen

Die Controllermodule von StorSimple-Geräten verfügen jeweils über vier 1-Gigabit-und zwei 10-Gigabit-Ethernet-Netzwerkschnittstellen.

- Stellen Sie sicher, dass die Netzwerkverbindungen auf beiden Controllermodulen identisch sind und dass die Netzwerkschnittstellen, mit denen die Controllermodul-Schnittstellen verbunden sind, eine identische Netzwerkkonfigurationen aufweisen.

- Wenn möglich, stellen Sie die Netzwerkverbindungen über unterschiedliche Switches bereit, um die Dienstverfügbarkeit beim Ausfall eines Netzwerkgerätes sicherzustellen.

- Wenn die einzige oder die letzte verbleibende iSCSI-aktivierte Schnittstelle (mit zugewiesenen IP-Adressen) entfernt wird, deaktivieren Sie zunächst die Schnittstelle, und trennen Sie dann die Kabel. Wenn die Schnittstelle zuerst getrennt wird, erfolgt ein Failover vom aktiven Controller zum passiven Controller. Wenn beim passiven Controller auch die entsprechenden Schnittstellen getrennt wurden, führen beide Controller mehrere Neustarts durch, bevor ein Controller aktiviert wird.

- Verbinden Sie auf jedem Controllermodul mindestens zwei DATA-Schnittstellen mit dem Netzwerk.

- Wenn Sie die beiden 10-GbE-Schnittstellen aktiviert haben, stellen Sie diese über unterschiedliche Switches bereit.

- Verwenden Sie nach Möglichkeit auf Servern MPIO, damit die Server Fehler bei Links, dem Netzwerk oder einzelnen Schnittstellen tolerieren können.

Weitere Informationen zum Einrichten des Netzwerks für Ihr Gerät für hohe Verfügbarkeit und hohe Leistung finden Sie unter [Verkabeln eines Geräts vom Typ 8100](https://msdn.microsoft.com/library/azure/dn757738.aspx) oder [Verkabeln eines Geräts vom Typ 8600](https://msdn.microsoft.com/library/azure/dn757762.aspx).

#### SSDs und HDDs

StorSimple-Geräte enthalten Solid-State-Datenträger (SSDs) und Festplattenlaufwerke (HDDs), die mit gespiegelten Speicherbereichen geschützt werden. Für die Festplattenlaufwerke ist ein Hot-Spare-Austausch möglich. Durch die Verwendung von gespiegelten Speicherbereichen wird sichergestellt, dass das Gerät Fehler von einzelnen oder mehreren SSDs oder HDDs tolerieren kann.

- Stellen Sie sicher, dass alle SSD- und HDD-Module installiert sind.

- Beim Ausfall einer SSD oder HDD fordern Sie sofort einen Austausch an.

- Fällt eine SSD oder HDD aus oder erfordert einen Austausch, dürfen Sie nur die SSD oder HDD entfernen, die ersetzt werden muss.

- Entfernen Sie zu keinem Zeitpunkt mehrere SSDs oder HDDs aus dem System.

- Überwachen Sie während des Austauschs den **Hardwarestatus** auf der Seite **Wartung** für die Laufwerke auf den SSDs und HDDs. Ein grünes Häkchen beim Status zeigt an, dass die Datenträger fehlerfrei funktionieren, während ein rotes Ausrufezeichen einen Fehler bei einer SSD oder HDD angibt.

- Ein Ausfall von zwei oder mehr Datenträgern eines bestimmten Typs (HDD, SSD) oder aufeinander folgende Fehler innerhalb eines kurzen Zeitraums können zu einer Fehlfunktion im System und zu potenziellen Datenverlusten führen. Wenden Sie sich in einem solchen Fall an den technischen Support. Es wird empfohlen, Cloudmomentaufnahmen für alle Volumes zu konfigurieren, die Sie im Falle eines Systemfehlers schützen müssen.

#### EBOD-Gehäuse

StorSimple-Geräte des Modells 8600 bieten zusätzlich zum normalen Gehäuse ein EBOD (Extended Bunch of Disks)-Gehäuse. Ein EBOD enthält EBOD-Controller und Festplattenlaufwerke (HDDs), die mit gespiegelten Speicherbereichen geschützt werden. Durch die Verwendung von gespiegelten Speicherbereichen wird sichergestellt, dass das Gerät Fehler von einzelnen oder mehreren HDDs tolerieren kann. Das EBOD-Gehäuse ist mit dem primären Gehäuse über redundante SAS-Kabel verbunden.

- Stellen Sie sicher, dass beide Controllermodule der EBOD-Gehäuse, beide SAS-Kabel und alle Festplatten jederzeit installiert sind.

- Wenn ein Controllermodul oder eine Festplatte des EBOD-Gehäuses ausfällt, fordern Sie sofort einen Austausch an.

- Fällt ein Controllermodul des EBOD-Gehäuses aus, vergewissern Sie sich vor dem Austausch, dass das andere Controllermodul aktiv ist.

- Fällt eine HDD aus oder erfordert einen Austausch, dürfen Sie nur die HDD entfernen, die ersetzt werden muss. Entfernen Sie nur dann eine Festplatte, wenn die Datenträger und das Array fehlerfrei funktionieren.

- Entfernen Sie zu keinem Zeitpunkt mehrere HDDs aus dem System.

- Überwachen Sie während des Austauschs eines EBOD-Controllermoduls oder einer HDD ständig den Status der entsprechenden Komponente im StorSimple Manager-Dienst unter **Wartung** > **Hardware**.

- Wenn ein SAS-Kabel ausfällt oder ersetzt werden muss (der Microsoft Support sollte in eine solche Entscheidung einbezogen werden), tauschen Sie nur das SAS-Kabel aus, das auch ersetzt werden muss.

- Entfernen Sie niemals gleichzeitig beide SAS-Kabel aus dem System.

### Anforderungen an hohe Verfügbarkeit für Hostcomputer

Lesen Sie sorgfältig diese Anforderungen und bewährten Methoden durch, um eine hohe Verfügbarkeit der Hosts sicherzustellen, die mit dem StorSimple-Gerät verbunden sind.

- Konfigurieren von StorSimple mit [Dateiserverclustern mit zwei Knoten] (https://technet.microsoft.com/library/cc731844(v=WS.10).aspx). Wenn Sie einzelne Fehlerquellen eliminieren und auf Hostseite Redundanz erzeugen, wird die gesamte Lösung höher verfügbar.

- Verwenden Sie die in Windows Server 2012 (SMB 3.0) verfügbaren permanent verfügbaren Freigaben für eine hohe Verfügbarkeit während Failovern der Speichercontroller. Weitere Informationen zum Konfigurieren von Dateiserverclustern und permanent verfügbaren Freigaben mit Windows Server 2012 erhalten Sie in diesem [Vorführvideo](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares) (in englischer Sprache).

## Netzwerkanforderungen für Ihr StorSimple-Gerät

Das StorSimple-Gerät ist ein gesperrtes Gerät. Allerdings müssen Ports in der Firewall für iSCSI-, Cloud- oder Verwaltungsdatenverkehr geöffnet werden. In der folgenden Tabelle sind die Ports aufgeführt, die in der Firewall geöffnet werden müssen. In dieser Tabelle bezieht sich *ein* oder *eingehend* auf die Richtung, aus der eingehende Clientanforderungen auf das Gerät zugreifen. Entsprechend bezieht sich *aus* oder *ausgehend* auf die Richtung, in der das StorSimple-Gerät Daten über die Bereitstellung hinaus an externe Ziele sendet: z. B. ausgehende Verbindungen mit dem Internet.

| Portnr.<sup>1,2</sup> | ein oder aus | Portbereich | Erforderlich | Hinweise |
|------------------------|-----------|------------|----------|-------| 
|TCP 80 (HTTP)<sup>3</sup>| aus | WAN | Nein |<ul><li>Der ausgehende Port wird für den Internetzugriff zum Abrufen von Updates verwendet.</li><li>Der ausgehende Webproxy ist vom Benutzer konfigurierbar.</li><li>Zum Ermöglichen von Systemupdates muss dieser Port auch für die festen IP-Adressen des Controllers geöffnet sein.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| aus | WAN | Ja |<ul><li>Der ausgehende Port wird für den Zugriff auf Daten in der Cloud verwendet.</li><li>Der ausgehende Webproxy ist vom Benutzer konfigurierbar.</li><li>Zum Ermöglichen von Systemupdates muss dieser Port auch für die festen IP-Adressen des Controllers geöffnet sein.</li></ul>|
|UDP 53 (DNS) | aus | WAN | In einigen Fällen; siehe Hinweise. |Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten DNS-Server verwenden. |
| UDP 123 (NTP) | aus | WAN | In einigen Fällen; siehe Hinweise. |Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten NTP-Server verwenden. |
| TCP 9354 | aus | WAN | In einigen Fällen; siehe Hinweise. |Der ausgehende Port wird vom StorSimple Manager-Dienst für die Kommunikation mit dem Gerät verwendet. Dieser Port ist erforderlich, wenn Ihr aktuelles Netzwerk für Verbindungen mit dem Internet nicht HTTP 1.1 unterstützt, z. B. wenn Sie einen HTTP-1.0-basierten Proxyserver verwenden.<br> Informationen zum Herstellen von Verbindungen über einen Proxyserver finden Sie unter [Hosten hinter einer Firewall mit Servicebus](https://msdn.microsoft.com/library/azure/ee706729.aspx). |
| 3260 (iSCSI) | Geben Sie in | LAN | Nein | Dieser Port wird für den Datenzugriff über iSCSI verwendet.|
| 5985 | Geben Sie in | LAN | Nein | Der eingehende Port wird vom StorSimple-Momentaufnahme-Manager für die Kommunikation mit dem StorSimple-Gerät verwendet.<br>Dieser Port wird auch verwendet, wenn Sie eine Remoteverbindung mit Windows PowerShell für StorSimple über HTTP oder HTTPS herstellen. |

<sup>1</sup> Es müssen keine eingehenden Ports für das öffentliche Internet geöffnet werden.

<sup>2</sup> Wenn mehrere Ports eine Gatewaykonfiguration besitzen, wird die Reihenfolge des ausgehenden weitergeleiteten Datenverkehrs basierend auf unten beschriebenen Reihenfolge der Portweiterleitungen ermittelt .

<sup>3</sup> Die festen IP-Adressen der Controller im StorSimple-Gerät müssen routingfähig sein und eine Verbindung mit dem Internet herstellen können. Die festen IP-Adressen werden für Anwendung von Updates auf dem Gerät verwendet. Wenn die Gerätecontroller über die festen IP-Adressen keine Verbindung mit dem Internet herstellen können, können Sie das StorSimple-Gerät nicht aktualisieren.

### Portweiterleitung

Die Portweiterleitung unterscheidet sich je nach Version der Software auf Ihrem StorSimple-Gerät.

- Wenn auf Gerät eine Version vor Update 1 ausgeführt wird, z. B. die GA-Version (allgemeine Verfügbarkeit), 0.1, 0.2 oder 0.3, erfolgt die Portweiterleitung nach folgenden Regeln:

     Zuletzt konfigurierte 10-GbE-Netzwerkschnittstelle > andere 10-GbE-Netzwerkschnittstelle > zuletzt konfigurierte 1-GbE-Netzwerkschnittstelle > andere 1-GbE-Netzwerkschnittstelle

- Wenn auf dem Gerät Update 1 ausgeführt wird, erfolgt die Portweiterleitung nach folgenden Regeln:

     DATA 0 > zuletzt konfigurierte 10-GbE-Netzwerkschnittstelle > andere 10-GbE-Netzwerkschnittstelle > zuletzt konfigurierte 1-GbE-Netzwerkschnittstelle > andere 1-GbE-Netzwerkschnittstelle

In Update 1 ist DATA 0 die niedrigste Routingmetrik. Daher wird der gesamte Datenverkehr der Cloud über DATA 0 weitergeleitet. Notieren Sie dies, wenn es mehr als eine Cloud-Netzwerkschnittstelle auf dem StorSimple-Gerät gibt.

### Bewährte Methoden für Netzwerke

Befolgen Sie zusätzlich zu den oben genannten Netzwerkanforderungen die folgenden bewährten Methoden, um eine optimale Leistung für Ihre StorSimple-Lösung zu gewährleisten:

- Stellen Sie sicher, dass für das StorSimple-Gerät jederzeit eine dedizierte Bandbreite von 40 MBit/s (oder mehr) zur Verfügung steht. Diese Bandbreite sollte nicht gemeinsam mit anderen Anwendungen genutzt werden.

- Stellen Sie sicher, dass jederzeit eine Netzwerkverbindung mit dem Internet verfügbar ist. Zeitweise unterbrochene oder unzuverlässige Internetverbindungen mit den Geräten, also auch eine Unterbrechung der Internetverbindung, führt zu einer nicht unterstützten Konfiguration.

- Isolieren Sie den Datenverkehr für iSCSI und Cloud durch Verwendung jeweils eigener Netzwerkschnittstellen auf dem Gerät für den iSCSI- und Cloudzugriff. Weitere Informationen finden Sie unter [Netzwerkanforderungen für das StorSimple-Gerät](https://msdn.microsoft.com/library/dn772371.aspx).

## Nächste Schritte

- [StorSimple-Einschränkungen](storsimple-limits.md)
- [Bereitstellen der StorSimple-Lösung](storsimple-deployment-walkthrough.md)
 

<!---HONumber=July15_HO2-->