<properties 
    pageTitle="StorSimple – Versionshinweise – Februar 2015"
    description="Beschreibt die neuen Features, Probleme und Problemumgehungen für die StorSimple-Version vom Februar 2015."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="adinah"
    editor="tysonn" />
<tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="04/13/2015"
    ms.author="v-sharos" />

#StorSimple – Versionshinweise – Februar 2015

##Übersicht

Dieses Dokument beschreibt die wichtigsten offenen Fragen für die Microsoft Azure StorSimple-Version von Februar 2015. Es enthält auch eine Liste der StorSimple-Software- und -Firmware-Updates, die in dieser Version enthalten sind. Dies ist die dritte Version nach der allgemeinen Verfügbarkeit von Microsoft Azure StorSimple \(General Availability, GA\).
  
Dieses Update ändert die Softwareversion des Geräts nach dem Januar-Update nicht. Es bleibt weiterhin Version 6.3.9600.17312. Sie können überprüfen, ob das Update installiert wurde, indem Sie das Datum für **Letzte Aktualisierung** prüfen. Wenn das Datum nach dem 10.02.2015 liegt, wurde das Update erfolgreich installiert.

Es wird empfohlen, dass Sie sofort nach der Installation des StorSimple-Geräts nach allen verfügbaren Updates suchen und diese installieren. Sie können auch automatische Updates aktivieren, um Updates mit hoher Priorität von Microsoft herunterzuladen und zu installieren, sobald diese verfügbar sind.  Weitere Informationen finden Sie im Thema zum Installieren von [Updates](https://msdn.microsoft.com/library/azure/1a2cd7de-706b-4d3c-8efb-02e322d3ae73#BKMK_Updates).

Lesen Sie vor der Bereitstellung des Updates in Ihrer StorSimple-Lösung die Informationen in den Versionsanmerkungen sorgfältig durch.

>[AZURE.IMPORTANT] 
>
> - Verwenden Sie den StorSimple-Manager-Dienst und nicht die Windows PowerShell für StorSimple, um die Februar-Updates zu installieren.   
> - Es dauert ungefähr eine Stunde, dieses Update zu installieren. Wenn Sie jedoch kumulative Updates installieren, kann dieser Vorgang ungefähr drei Stunden dauern.  
> -	Die Februar-Version von StorSimple enthält keine Updates des virtuellen StorSimple-Geräts. Sie können aber weiterhin alle verfügbaren Windows-Updates für das virtuelle Gerät, einschließlich der neuesten Sicherheitsupdates, anwenden, es wird aber keine Änderung der Version für das virtuelle Gerät angezeigt.  

Bevor Ihr StorSimple-Gerät aktualisiert wird, sollten folgende Voraussetzungen erfüllt sein.

- Stellen Sie sicher, dass beide Gerätecontroller ausgeführt werden, bevor Sie nach Updates suchen. Wenn einer der Controller nicht ausgeführt wird, treten bei der Überprüfung Fehler auf. Wechseln Sie unter der Seite **Wartung** zu **Hardwarestatus**, um sicherzustellen, dass die Controller fehlerfrei sind. Wenn Komponenten vorliegen, die **Aufmerksamkeit** erfordern, wenden Sie sich vor dem Fortfahren an den Microsoft-Support.
- Stellen Sie sicher, dass die festen IPs für Controller 0 und Controller 1 routingfähig sind und eine Verbindung zum Internet herstellen können, während sie für die Wartung der Updates für das Gerät verwendet werden. Sie können das [Cmdlet "Test-Connection"](https://technet.microsoft.com/library/hh849808.aspx) verwenden, um eine bekannte Adresse außerhalb des Netzwerks wie "outlook.com" per Ping abzufragen und so sicherzustellen, dass der Controller über eine Konnektivität zum Außennetzwerk verfügt.
- Stellen Sie sicher, dass die Ports 80 und 443 auf Ihrem StorSimple-Gerät für die ausgehende Kommunikation verfügbar sind. Weitere Informationen finden Sie unter [Netzwerkanforderungen für das StorSimple-Gerät](https://msdn.microsoft.com/library/azure/dn772371.aspx).
- Deaktivieren Sie die Ports "Data 2" und "Data 3", sofern sie aktiviert sind, wenn die Softwareversion des Geräts älter als 6.3.9600.17312 \(Oktober 2014-Update\) ist, bevor Sie mit dem Update beginnen. Wenn Sie die Ports "Data 2" und "Data 3" aktiviert lassen, während Sie das Update anwenden, kann dies dazu führen, dass Ihr Gerätecontroller in den Wiederherstellungsmodus wechselt. Beachten Sie, dass beim Deaktivieren der Netzwerkschnittstellen alle zugehörigen Volumes offline geschaltet und ihre E/As für die Dauer des Updates unterbrochen werden.  
  
##Neuigkeiten in der Februar-Version

Dieses Update enthält eine Korrektur des Problems beim Zurücksetzen auf die Werkseinstellungen auf Geräten, deren GA-Version mit der Version vom Oktober 2014 aktualisiert wurde. Weitere Informationen finden Sie unter [In dieser Version behobene Probleme](#issues-fixed-in-the-february-release).

Dieses Update enthält keine neuen Features oder Funktionen.

##In der Februar-Version behobene Probleme

Die folgende Tabelle beschreibt das Problem, das in diesem Update behoben wurde.
 
| Nr. | Funktion | Problem | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
|-----|---------|-------|---------------------------------|-------------------------------|
| 1 | Zurücksetzen auf Werkseinstellungen | Sie versuchen ein Zurücksetzen auf die Werkseinstellungen auf einem Gerät, auf dem ursprünglich die GA-Version \(Version 6.3.9600.17215\) installiert war, das aber auf die Version vom Oktober \(Version 6.3.9600.17312\) aktualisiert wurde. Das Zurücksetzen auf die Werkseinstellungen misslingt, und das Gerät wird instabil. | Ja | Nein |


##Bekannte Probleme in der Februar-Version

Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme in dieser Version.
 
| Nr. | Funktion | Problem | Kommentare/Problemumgehung | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
|-----|---------|-------|----------------------------|-----------------------------|--------------------------|
| 1 | Zurücksetzen auf Werkseinstellungen | Unter bestimmten Umständen kann das StorSimple-Gerät beim Zurücksetzen auf die Werkseinstellungen nicht mehr reagieren und die folgende Nachricht anzeigen: **Zurücksetzung auf Werkseinstellungen wird ausgeführt \(Phase 8\)**. Dies kann vorkommen, wenn Sie STRG+C drücken, während das Cmdlet ausgeführt wird. | Drücken Sie STRG+C nicht nach dem Einleiten einer Zurücksetzung auf die Werkseinstellungen. Wenn dies bereits erfolgt ist, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. | Ja | Nein |
| 2 | Datenträgerquorum | In seltenen Fällen kann der Speicherpool offline geschaltet werden, wenn der Großteil der Datenträger im EBOD-Gehäuse eines 8600-Geräts getrennt wird, sodass kein Datenträgerquorum verfügbar ist. Der Speicherpool bleibt offline, auch wenn die Verbindung zu den Datenträgern wiederhergestellt wird. | Sie müssen das Gerät neu starten. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. | Ja | Nein |
| 3 | Fehler bei der Cloud-Momentaufnahme | In seltenen Fällen tritt ggf. ein Fehler **Maximales Sicherungslimit erreicht** einer Cloud-Momentaufnahme auf. Dies kann vorkommen, wenn die Anzahl von 255 Onlineklonen auf demselben Gerät überschritten wird, die von demselben ursprünglichen Volume stammen, das gelöscht wurde. | | Ja | Ja |
| 4 | Falsche Controller-ID | Beim Austausch eines Controllers kann es vorkommen, dass Controller 0 als Controller 1 angezeigt wird. Während des Controlleraustauschs kann die Controller-ID anfänglich als ID des Peercontrollers angezeigt werden, wenn das Image vom Peerknoten geladen wurde. In seltenen Fällen kann dieses Verhalten auch nach einem Neustart des Systems auftreten. | Es ist keine Benutzeraktion erforderlich. Dieses Problem löst sich von selbst, nachdem der Controlleraustausch abgeschlossen ist. | Ja | Nein |
| 5 | Geräteüberwachungsdiagramme | Im StorSimple-Manager-Dienst funktionieren die Geräteüberwachungsdiagramme nicht, wenn in der Proxyserverkonfiguration für das Gerät die Standard- oder NTLM-Authentifizierung aktiviert ist. | Ändern Sie die Webproxykonfiguration für das beim StorSimple-Manager-Dienst registrierte Gerät so, dass für die Authentifizierung die Option KEINE festgelegt ist. Führen Sie zu diesem Zweck das Windows PowerShell für StorSimple-Cmdlet "Set-HcsWebProxy" aus. | Ja | Ja |
| 6 | Speicherkonten | Das Verwenden des Speicherdiensts zum Löschen des Speicherkontos wird nicht unterstützt. Dies führt dazu, dass keine Benutzerdaten abgerufen werden können. | | Ja | Ja |
| 7 | Gerätefailover | Mehrere Failover eines Volumecontainers von demselben Quellgerät auf verschiedene Zielgeräte werden nicht unterstützt. Das Failover von einem einzelnen nicht reagierenden Gerät auf mehrere Geräte führt dazu, dass die Volumecontainer auf dem ersten Gerät mit erfolgtem Failover die Dateneigentümerschaft verlieren. Nach einem solchen Failover werden diese Volumecontainer anders angezeigt oder verhalten sich anders bei der Anzeige im Verwaltungsportal. | Ja | Nein |
| 8 | Installation | Während der Installation von StorSimple-Adapter für SharePoint müssen Sie die IP-Adresse eines Geräts angeben, damit die Installation erfolgreich abgeschlossen wird. | | Ja | Nein |
| 9 | Webproxy | Wenn Ihre Webproxykonfiguration das Protokoll "HTTPS" verwendet, ist die Kommunikation zwischen dem Gerät und dem Dienst beeinträchtigt, und das Gerät wird offline geschaltet. Supportpakete werden bei diesem Vorgang ebenfalls generiert. Sie beanspruchen auf Ihrem Gerät erhebliche Ressourcen. | Stellen Sie sicher, dass "HTTP" als Protokoll für die Webproxy-URL angegeben ist. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](https://msdn.microsoft.com/library/azure/dn764937.aspx). | Ja | Nein |
| 10 | Webproxy | Wenn Sie den Webproxy für ein registriertes Gerät konfigurieren und aktivieren, müssen Sie den aktiven Controller auf Ihrem Gerät neu starten. | | Ja | Nein |
| 11 | Hohe Cloud-Latenzen und hohe E/A-Arbeitsauslastung | Wenn Ihr StorSimple-Gerät mit einer Kombination aus sehr hohen Cloud-Latenzen \(mehrere Sekunden\) und hoher E/A-Arbeitsauslastung konfrontiert wird, verschlechtert sich die Leistung der Gerätevolumes, und es tritt ggf. der E/A-Fehler "Gerät nicht bereit" auf. | Sie müssen die Gerätecontroller manuell neu starten oder ein Gerätefailover ausführen, um dieses Problem zu beheben. | Ja | Nein |

##Updates für das physische Gerät in der Februar-Version

Dieses Update enthält eine Korrektur des Problems beim Zurücksetzen auf die Werkseinstellungen auf Geräten, deren GA-Version mit der Version vom Oktober 2014 aktualisiert wurde. Es enthält keine anderen Updates für das StorSimple-Gerät.

##Updates des SAS-Controllers \(Serial Attached SCSI\) und der Firmware in der Februar-Version

Diese Version enthält keine Updates für den SAS-Controller \(Serial Attached SCSI\) oder die Firmware. Das Treiberupdate wurde in der Version vom Oktober 2014 durchgeführt.

##Updates für das virtuelle Gerät in der Februar-Version

Diese Version enthält keine Updates für das virtuelle Gerät. Durch die Installation des Updates ändert sich die Softwareversion des virtuellen Geräts nicht.

<!--HONumber=52-->
