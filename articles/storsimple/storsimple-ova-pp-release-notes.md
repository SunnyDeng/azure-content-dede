<properties 
   pageTitle="Versionshinweise zu StorSimple Virtual Array| Microsoft Azure"
   description="Beschreibt wichtige offene Probleme und Lösungen für das StorSimple Virtual Array."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# Versionshinweise zu StorSimple Virtual Array (Vorschau) 

## Übersicht

Die folgenden Versionshinweise identifizieren die kritischen offenen Probleme für die öffentliche Vorschau von Microsoft Azure StorSimple Virtual Array von Dezember 2015 (auch als „lokales virtuelles StorSimple-Gerät“ oder „virtuelles StorSimple-Gerät“ bezeichnet).

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres virtuellen StorSimple-Geräts die Informationen in den Versionsanmerkungen sorgfältig durch.

>[AZURE.IMPORTANT]
>
>- StorSimple Virtual Array befindet sich in der Vorschauphase und ist für die Evaluierung und Bereitstellungsplanung vorgesehen. Die Installation dieser Vorschauversion in einer Produktionsumgebung wird nicht unterstützt. 
>
>- Wenn Probleme mit StorSimple Virtual Array auftreten, posten Sie sie im [StorSimple-MSDN-Forum](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme in dieser Version.

| Nr.| Funktion | Problem | Problemumgehung/Kommentare |
|----|---------|-------|---------------------|                                                                                                                                                                                                                                                      
| **1.** | Aktualisierungen | In der Vorabversion erstellte virtuellen Geräte können nicht auf eine unterstützte Version für allgemeine Verfügbarkeit aktualisiert werden.| Für diese virtuellen Geräte muss mithilfe eines Notfallwiederherstellungs-Workflows ein Failover für die allgemein verfügbare Version ausgeführt werden.|
| **2.** | Mehrstufige Volumes oder Freigaben | Bytebereichsperren für Anwendungen, die mit den mehrstufigen StorSimple-Volumes arbeiten, werden nicht unterstützt. Wenn Bytebereichsperren aktiviert sind, funktioniert die StorSimple-Staffelung nicht. | Empfohlene Maßnahmen:<ul><li>Deaktivieren Sie Bytebereichsperren in der Anwendungslogik.</li><li> Wählen Sie zum Einfügen von Daten für diese Anwendung lokal angeheftete Volumes anstatt mehrstufige Volumes.</li>*Nachteil*: Wenn lokal angeheftete Volumes verwendet werden und Bytebereichsperren aktiviert sind, beachten Sie, dass das lokal angeheftete Volume online sein kann, bevor die Wiederherstellung abgeschlossen ist. Wenn in solchen Fällen eine Wiederherstellung ausgeführt wird, müssen Sie auf den Abschluss des Wiederherstellungsvorgangs warten.|
| **3.** | Mehrstufige Freigaben | Das Arbeiten mit großen Dateien kann zu einer langsamen Abstufung führen. | Stellen Sie bei Arbeit mit großen Dateien sicher, dass die größte Datei kleiner als 3 % der Größe der Dateifreigabe ist.|
| **4.** | Lokal angeheftete Volumes oder Freigaben | Die nutzbare Kapazität eines lokal angehefteten Volumes oder einer Freigabe ist 85 - 90 % der bereitgestellten Kapazität. Bereich von 10 bis 15 % ist für die Metadaten reserviert. | Sie müssen ein größeres Volume bereitstellen, um die gewünschte nutzbare Kapazität zu erzielen. Für 1 TB nutzbare Kapazität müssen Sie beispielsweise ein Volume mit 1,15 TB bereitstellen.|
| **5.** | Lokale Web-UI | Wenn in Internet Explorer (IE ESC) erweiterten Sicherheitsfeatures aktiviert sind, funktionieren einige lokale Web-UI-Seiten wie z  B. **Problembehandlung** oder **Wartung** möglicherweise nicht ordnungsgemäß. Außerdem funktionieren Schaltflächen auf diesen Seiten möglicherweise nicht. | Deaktivieren Sie erweiterte Sicherheitsfeatures in Internet Explorer.|
| **6.** | Lokale Web-UI | Lokalisierung für lokale Web-UI wird in dieser Version nicht unterstützt. | Dies wird in einer späteren Version implementiert werden.|
| **7.** | Lokale Web-UI | Auf einer virtuellen Hyper-V-Maschine werden die Netzwerkschnittstellen in der Web-UI als 10-Gbit/s-Schnittstellen angezeigt. | Dieses Verhalten ist eine Spiegelung von Hyper-V. Hyper-V zeigt immer 10 Gbit/s für virtuelle Netzwerkadapter an.|                                                                                                 
| **8.** | Notfallwiederherstellung | Sie können die Notfallwiederherstellung eines Dateiservers nur in der Domäne des Quellgeräts ausführen. Die Notfallwiederherstellung auf einem Zielgerät in einer anderen Domäne wird in dieser Version nicht unterstützt. | Dies wird in einer späteren Version implementiert werden.|
| **9.**| Für Freigaben genutzte Kapazität| Möglicherweise wird eine Nutzung durch die Freigabe angezeigt, auch wenn diese keine Daten enthält. Dies liegt daran, dass die für Freigaben genutzte Kapazität Metadaten umfasst. | |                                                                                                                                                                                                                                                         
| **10.** | Zeitzoneneinstellungen| Wenn die Zeitzone für ein registriertes Gerät geändert wird, wird die Änderung in den über die lokale Web-UI ausgeführten Diagnosetests nicht berücksichtigt. | Dieses Problem wird in einer zukünftigen Version behoben.|
| **11.** | Netzwerkeinstellungen | Wenn eine statische IP-Adresse für eine Netzwerkschnittstelle über die Web-UI konfiguriert wird, erfolgt die Änderung der IP. Die DNS-Server IP-Adresse wird jedoch auch in eine standortlokale IPv6-Adresse geändert. | Dieses Problem wird in einer zukünftigen Version behoben.|
| **12.** | Microsoft Azure PowerShell | Die virtuellen StorSimple-Geräte können in dieser Version nicht mithilfe von Azure PowerShell verwaltet werden. | Die gesamte Verwaltung der virtuellen Geräte sollte über das klassische Azure-Portal und die lokale Web-UI erfolgen.|
| **13.** | Aufträge | Der Auftragsfortschritt wird nicht präzise angegeben. Der Fortschritt des Auftrags kann direkt von 0 zu 100 % wechseln. | Dies wird in einer zukünftigen Version behoben.|
| **14.** | Bereitgestellter Datenträger | Nachdem Sie einen Datenträger mit einer bestimmten angegebenen Größe bereitgestellt und das entsprechende virtuelle StorSimple-Gerät erstellt haben, darf der Datenträger nicht erweitert oder verkleinert werden. Ein entsprechender Versuch kann zum Verlust aller Daten in den lokalen Ebenen des Geräts führen. | |
| **15.** | Hilfe | Hilfethemen sind nicht verfügbar.| Die gesamte Dokumentation ist im Microsoft Download Center und auf der Website mit der Azure-Dokumentation verfügbar. |   

<!---HONumber=AcomDC_0121_2016-->