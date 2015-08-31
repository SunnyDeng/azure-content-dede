<properties 
    pageTitle="Versionsanmerkungen zur Freigabeversion der StorSimple 8000-Serie – Juli 2014 | Microsoft Azure"
    description="Beschreibt die neuen Features, Probleme und Problemumgehungen für die Microsoft Azure StorSimple-Version vom Juli 2014."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="carolz"
    editor="" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="08/19/2015"
    ms.author="v-sharos" />

# Versionsanmerkungen zur Freigabeversion der StorSimple 8000-Serie – Juli 2014 

## Übersicht

Die folgenden Versionsanmerkungen enthalten die kritischen offenen Probleme der StorSimple 8000-Serie von Microsoft Azure StorSimple in der Version von Juli 2014 für die allgemeine Verfügbarkeit. Diese Version bezieht sich auf Softwareversion 6.3.9600.17215.

Sofern nicht anders angegeben, gelten diese Versionshinweise für alle Modelle des StorSimple-Geräts. Die Anmerkungen zu dieser Version werden fortlaufend aktualisiert. Wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Bevor Sie Ihre Microsoft Azure StorSimple-Lösung bereitstellen, sollten Sie die folgenden Informationen beachten.

## Bekannte Probleme in dieser Version
Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme in dieser Version.
 
| Nr. | Funktion | Problem | Kommentare/Problemumgehung | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Zurücksetzen auf Werkseinstellungen | Unter bestimmten Umständen kann das StorSimple-Gerät beim Zurücksetzen auf die Werkseinstellungen nicht mehr reagieren und die folgende Nachricht anzeigen: **Zurücksetzung auf Werkseinstellungen wird ausgeführt (Phase 8)**. Dies kann vorkommen, wenn Sie STRG+C drücken, während das Cmdlet ausgeführt wird. | Drücken Sie STRG+C nicht nach dem Einleiten einer Zurücksetzung auf die Werkseinstellungen. Wenn dies bereits erfolgt ist, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. | Ja | Nein |
| 2 | Datenträgerquorum | In seltenen Fällen kann der Speicherpool offline geschaltet werden, wenn der Großteil der Datenträger im EBOD-Gehäuse eines 8600-Geräts getrennt wird, sodass kein Datenträgerquorum verfügbar ist. Der Speicherpool bleibt offline, auch wenn die Verbindung zu den Datenträgern wiederhergestellt wird. | Sie müssen das Gerät neu starten. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. | Ja | Nein |
| 3 | Fehler bei der Cloud-Momentaufnahme | In seltenen Fällen tritt ggf. ein Fehler **Maximales Sicherungslimit erreicht** einer Cloud-Momentaufnahme auf. Dies kann vorkommen, wenn die Anzahl von 255 Onlineklonen auf demselben Gerät überschritten wird, die von demselben ursprünglichen Volume stammen, das gelöscht wurde. | | Ja | Ja |
| 4 | Falsche Controller-ID | Beim Austausch eines Controllers kann es vorkommen, dass Controller 0 als Controller 1 angezeigt wird. Während des Controlleraustauschs kann die Controller-ID anfänglich als ID des Peercontrollers angezeigt werden, wenn das Image vom Peerknoten geladen wurde. In seltenen Fällen kann dieses Verhalten auch nach einem Neustart des Systems auftreten. | Es ist keine Benutzeraktion erforderlich. Dieses Problem löst sich von selbst, nachdem der Controlleraustausch abgeschlossen ist. | Ja | Nein |
| 5 | Geräteüberwachungsdiagramme | Im StorSimple-Manager-Dienst funktionieren die Geräteüberwachungsdiagramme nicht, wenn in der Proxyserverkonfiguration für das Gerät die Standard- oder NTLM-Authentifizierung aktiviert ist. | Ändern Sie die Webproxykonfiguration für das beim StorSimple-Manager-Dienst registrierte Gerät so, dass für die Authentifizierung die Option KEINE festgelegt ist. Führen Sie zu diesem Zweck das Windows PowerShell für StorSimple-Cmdlet "Set-HcsWebProxy" aus. | Ja | Ja |
| 6 | Speicherkonten | Das Verwenden des Speicherdiensts zum Löschen des Speicherkontos wird nicht unterstützt. Dies führt dazu, dass keine Benutzerdaten abgerufen werden können. | | Ja | Ja |
| 7 | Failback | Ein Failback innerhalb von 24 Stunden wird bei der Notfallwiederherstellung nicht unterstützt. | | Ja | Nein |
| 8 | Gerätefailover | Mehrere Failover eines Volumecontainers von demselben Quellgerät auf verschiedene Zielgeräte werden nicht unterstützt. Das Failover von einem einzelnen nicht reagierenden Gerät auf mehrere Geräte führt dazu, dass die Volumecontainer auf dem ersten Gerät mit erfolgtem Failover die Dateneigentümerschaft verlieren. Nach einem solchen Failover werden diese Volumecontainer anders angezeigt oder verhalten sich anders bei der Anzeige im Verwaltungsportal. | | Ja | Nein |
| 9 | Installation | Während der Installation von StorSimple-Adapter für SharePoint müssen Sie die IP-Adresse eines Geräts angeben, damit die Installation erfolgreich abgeschlossen wird. | | Ja | Nein |
| 10 | Netzwerkschnittstellen | Die Netzwerkschnittstellen DATA 2 und DATA 3 wurden in der Software ausgetauscht. | Wenden Sie sich an den Microsoft-Support, wenn Sie diese Schnittstellen konfigurieren müssen. | Ja | Nein |


 

<!---HONumber=August15_HO8-->