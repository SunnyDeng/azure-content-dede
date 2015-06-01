<properties 
    pageTitle="StorSimple – Versionshinweise – Oktober 2014"
    description="Beschreibt die neuen Features, Probleme und Problemumgehungen für die StorSimple-Version vom Oktober 2014."
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

#StorSimple – Versionshinweise – Oktober 2014  

##Übersicht

Dieses Dokument enthält Anmerkungen zu dieser Version und beschreibt die wichtigsten offenen Fragen für die Microsoft Azure StorSimple-Version von Oktober 2014. Es enthält auch eine Liste der StorSimple-Software- und -Firmware-Updates, die in dieser Version enthalten sind. Dies ist die erste Version nach der allgemeinen Verfügbarkeit von Microsoft Azure StorSimple (General Availability, GA) und entspricht der Softwareversion 6.3.9600.17312.

Es wird empfohlen, dass Sie sofort nach der Installation des Geräts nach allen verfügbaren Updates suchen und diese installieren. Sie können auch automatische Updates aktivieren, um Updates mit hoher Priorität von Microsoft herunterzuladen und zu installieren, sobald diese verfügbar sind.  Weitere Informationen finden Sie im Thema zum Installieren von [Updates](https://msdn.microsoft.com/library/azure/1a2cd7de-706b-4d3c-8efb-02e322d3ae73#BKMK_Updates).

Lesen Sie vor der Bereitstellung der Updates in Ihrer StorSimple-Lösung die Informationen in den Versionsanmerkungen sorgfältig durch.

>[AZURE.IMPORTANT]
> 
-	Verwenden Sie den StorSimple-Manager-Dienst und nicht die Windows PowerShell für StorSimple, um die Oktober-Updates zu installieren.  
-	Die Fertigstellung des Updates dauert i. d. R. drei Stunden.  
-	Die Oktober-Version von StorSimple enthält keine Updates des virtuellen StorSimple-Geräts. Sie können dennoch weiterhin alle verfügbaren Windows-Updates, einschließlich der neuesten Sicherheitsupdates, anwenden, es wird aber keine Änderung der Version für das virtuelle Gerät angezeigt.  

Bevor Ihr StorSimple-Gerät aktualisiert wird, sollten folgende Voraussetzungen erfüllt sein.

- Stellen Sie sicher, dass beide Gerätecontroller ausgeführt werden, bevor Sie nach Updates suchen. Wenn einer der Controller nicht ausgeführt wird, treten bei der Überprüfung Fehler auf. Wechseln Sie unter der Seite **Wartung** zu **Hardwarestatus**, um sicherzustellen, dass die Controller fehlerfrei sind. Wenn Komponenten vorliegen, die **Aufmerksamkeit** erfordern, wenden Sie sich vor dem Fortfahren an den Microsoft-Support.  
- Stellen Sie sicher, dass die festen IPs für Controller 0 und Controller 1 routingfähig sind und eine Verbindung zum Internet herstellen können, während sie für die Wartung der Updates für das Gerät verwendet werden. Sie können das [Cmdlet "Test-Connection"](https://technet.microsoft.com/library/hh849808.aspx) verwenden, um eine bekannte Adresse außerhalb des Netzwerks wie "outlook.com" per Ping abzufragen und so sicherzustellen, dass der Controller über eine Konnektivität zum Außennetzwerk verfügt.  
- Stellen Sie sicher, dass die Ports 80 und 443 auf Ihrem StorSimple-Gerät für die ausgehende Kommunikation verfügbar sind. Weitere Informationen finden Sie unter [Netzwerkanforderungen für das StorSimple-Gerät](https://msdn.microsoft.com/library/azure/dn772371.aspx).  
- Deaktivieren Sie die Ports "Data 2" und "Data 3", sofern sie aktiviert sind, wenn die Softwareversion des Geräts älter als 6.3.9600.17312 (Oktober 2014-Update) ist, bevor Sie mit dem Update beginnen. Wenn Sie die Ports "Data 2" und "Data 3" aktiviert lassen, während Sie das Update anwenden, kann dies dazu führen, dass Ihr Gerätecontroller in den Wiederherstellungsmodus wechselt. Beachten Sie, dass beim Deaktivieren der Netzwerkschnittstellen alle zugehörigen Volumes offline geschaltet und ihre E/As für die Dauer des Updates unterbrochen werden.  

##Neuigkeiten in der Oktober-Version

Dieses Update umfasst die folgenden Verbesserungen:

- Sie können nun die Benutzeroberfläche des StorSimple-Manager-Diensts zur Verwaltung Ihrer Gerätecontroller verwenden. Zu den Verwaltungsaktionen zählen das Neustarten, Herunterfahren oder Einschalten eines Controllers. Weitere Informationen finden Sie unter [Verwalten von Gerätecontrollern](https://msdn.microsoft.com/library/azure/3216e992-f6ae-41c9-9ca4-f671342e1ab3#ManageControllers).  
- Sie können die WAN-Bandbreitenzuordnung als Kombination von Wochentag und Uhrzeit planen. Dadurch erzielen Sie eine bessere Nutzung der WAN-Bandbreite in Spitzenzeiten. Verschiedene Bandbreitenvorlagen sind für verschiedene Volumecontainer zulässig. Weitere Informationen finden Sie unter [Bandbreitenvorlagen](https://msdn.microsoft.com/library/azure/1747f56e-858a-4cfe-a020-949d7db23b8b#bt).  
- Sie können E-Mail-Benachrichtigungen konfigurieren, um die Administratoren und andere Personen proaktiv über vorhandene oder möglicherweise bevorstehende Probleme zu benachrichtigen. Weitere Informationen finden Sie unter [Konfigurieren von Warnungseinstellungen](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec_11).  

##In der Oktober-Version behobene Probleme


Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in diesem Update behoben wurden.

| Nr. | Funktion | Problem | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Netzwerkschnittstellen | In der vorherigen Version wurden die Netzwerkschnittstellen DATA 2 und DATA 3 in der Software vertauscht. Dies wurde in diesem Update behoben. Löschen Sie die Einstellungen, und deaktivieren Sie diese Netzwerkschnittstellen, bevor Sie das Update installieren. Nach der Installation des Updates müssen Sie diese Schnittstellen neu konfigurieren. | Ja | Nein |
| 2 | Supportpaket | Wenn Sie in der vorherigen Version das Windows PowerShell-Cmdlet **Export-HcsSupportPackage** zum Abrufen der Protokolle des Baseboard-Verwaltungscontrollers (Baseboard Management Controller, BMC) ausgeführt haben, trat ein Fehler des Vorgangs mit der folgenden Warnung auf: "Der Vorgang war auf diesem Domänencontroller erfolgreich. Auf dem Peercontroller ist jedoch aufgrund der folgenden Fehler ein Fehler aufgetreten. Überprüfen Sie, ob der Peer fehlerfrei ist und ob der aktuelle Knoten eine Verbindung mit dem Peer herstellen kann." Dieses Problem wurde jetzt behoben. | Ja | Nein |
| 3 | Gerätefailover | In der vorherigen Version bestand die Möglichkeit einer Dateninkonsistenz, wenn bei einem **Sicherungsermittlungsauftrag** während eines Gerätefailovers ein Fehler auftrat. Dieses Problem wurde jetzt behoben. | Ja | Nein |
| 4 | Gerätefailover | In der vorherigen Version waren nach einem Gerätefailover zwar die Sicherungen sichtbar, der zugehörige Volumecontainer war jedoch nicht auf dem Zielgerät vorhanden. Dieses Problem wurde jetzt behoben. | Ja | Nein |
| 5 | Gerätefailover | In der vorherigen Version gab es einen Fehler bei der Aufzählung von Cloud-Sicherungen während des Vorgangs "registry-restore", der potenziell zu einer Dateninkonsistenz führen konnte, wenn Verbindungsprobleme mit der Cloud bestanden. | Ja | Nein |
| 6 | Firmwareupdate | In der vorherigen Version wurde beim Auftrag für das Gerätefirmwareupdate ein Fehler angezeigt, der besagte, dass die Cmdlets nicht erkannt wurden und daher ein Fehler des Updates aufgetreten ist. Der Controller ging anschließend in den Wiederherstellungsmodus über. Dieses Problem wurde jetzt behoben. | Ja | Nein |
| 7 | Installation | Fehler aufgrund eines nicht ordnungsgemäßen Images des Geräts während der Installation wurden nun behoben. | Ja | Nein |
| 8 | Zurücksetzen auf Werkseinstellungen | Sie können jetzt optional die Firmwareüberprüfung für das Zurücksetzen auf die Werkseinstellungen überspringen. Dies ist eine Änderung gegenüber der vorherigen Version. | Ja | Nein |
| 9 | Zurücksetzen auf Werkseinstellungen | In der vorherigen Version wurden Firmwareüberprüfungen nur für bestimmte Hardwarekomponenten vorgenommen, wenn ein Cmdlet für das Zurücksetzen auf die Werkseinstellungen ausgeführt wurde. Nach dem ersten Neustart bei diesem Vorgang wurden zusätzliche Firmwareüberprüfungen vorgenommen, die einen Fehler beim Zurücksetzen verursachen konnten. Durch dieses Update wird sichergestellt, dass alle Firmwareüberprüfungen vorgenommen werden, wenn das Cmdlet für das Zurücksetzen auf die Werkseinstellungen ausgeführt wird, bevor der erste Systemneustart erfolgt. | Ja | Nein |
| 10 | Speicherkonten-Schlüsselrotation | Das Cmdlet **Invoke-HcsmServiceDataEncryptionKeyChange** zum Rotieren der Speicherkontoschlüssel fordert den Benutzer nun zur Eingabe des Verschlüsselungsschlüssels für Dienstdaten auf. Dies ist eine Änderung gegenüber der vorherigen Version, in der der Verschlüsselungsschlüssel für Dienstdaten als ein Inlineparameter übergeben wurde. | Ja | Nein |
| 11 | Failback innerhalb von 24 Stunden | Während der Notfallwiederherstellung wurde die Bereinigung auf dem Quellgerät nicht ordnungsgemäß ausgeführt, was zu einem Fehler beim Failback geführt hat. Dies wurde in dieser Version behoben. | Ja | Nein |

##Bekannte Probleme in der Oktober-Version

Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme in dieser Version.

| Nr. | Funktion | Problem | Kommentare/Problemumgehung | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Zurücksetzen auf Werkseinstellungen | Unter bestimmten Umständen kann das StorSimple-Gerät beim Zurücksetzen auf die Werkseinstellungen nicht mehr reagieren und die folgende Nachricht anzeigen: **Zurücksetzung auf Werkseinstellungen wird ausgeführt (Phase 8)**. Dies kann vorkommen, wenn Sie STRG+C drücken, während das Cmdlet ausgeführt wird. | Drücken Sie STRG+C nicht nach dem Einleiten einer Zurücksetzung auf die Werkseinstellungen. Wenn dies bereits erfolgt ist, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. | Ja | Nein |
| 2 | Zurücksetzen auf Werkseinstellungen | Nehmen Sie keine Zurücksetzung auf Werkseinstellungen bei einem StorSimple-Gerät vor, das von der allgemeinen Verfügbarkeit auf die Version Oktober 2014 aktualisiert wurde. | Dieser Vorgang funktioniert nur, wenn ein Patch installiert ist. Wenden Sie sich an den Microsoft-Support, um diesen erforderlichen Patch zu erhalten. | Ja | |	
| 3 | Datenträgerquorum | In seltenen Fällen kann der Speicherpool offline geschaltet werden, wenn der Großteil der Datenträger im EBOD-Gehäuse eines 8600-Geräts getrennt wird, sodass kein Datenträgerquorum verfügbar ist. Der Speicherpool bleibt offline, auch wenn die Verbindung zu den Datenträgern wiederhergestellt wird. | Sie müssen das Gerät neu starten. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. | Ja | Nein |
| 4 | Fehler bei der Cloud-Momentaufnahme | In seltenen Fällen tritt ggf. ein Fehler **Maximales Sicherungslimit erreicht** einer Cloud-Momentaufnahme auf. Dies kann vorkommen, wenn die Anzahl von 255 Onlineklonen auf demselben Gerät überschritten wird, die von demselben ursprünglichen Volume stammen, das gelöscht wurde. | | Ja | Ja |
| 5 | Falsche Controller-ID | Beim Austausch eines Controllers kann es vorkommen, dass Controller 0 als Controller 1 angezeigt wird. Während des Controlleraustauschs kann die Controller-ID anfänglich als ID des Peercontrollers angezeigt werden, wenn das Image vom Peerknoten geladen wurde. In seltenen Fällen kann dieses Verhalten auch nach einem Neustart des Systems auftreten. Es ist keine Benutzeraktion erforderlich. Dieses Problem löst sich von selbst, nachdem der Controlleraustausch abgeschlossen ist. | Ja | Nein |
| 6 | Geräteüberwachungsdiagramme | Im StorSimple-Manager-Dienst funktionieren die Geräteüberwachungsdiagramme nicht, wenn in der Proxyserverkonfiguration für das Gerät die Standard- oder NTLM-Authentifizierung aktiviert ist. | Ändern Sie die Webproxykonfiguration für das beim StorSimple-Manager-Dienst registrierte Gerät so, dass für die Authentifizierung die Option KEINE festgelegt ist. Führen Sie zu diesem Zweck das Windows PowerShell für StorSimple-Cmdlet "Set-HcsWebProxy" aus. | Ja | Ja |
| 7 | Speicherkonten | Das Verwenden des Speicherdiensts zum Löschen des Speicherkontos wird nicht unterstützt. Dies führt dazu, dass keine Benutzerdaten abgerufen werden können. | Ja | Ja |
| 8 | Gerätefailover | Mehrere Failover eines Volumecontainers von demselben Quellgerät auf verschiedene Zielgeräte werden nicht unterstützt. | Das Failover von einem einzelnen nicht reagierenden Gerät auf mehrere Geräte führt dazu, dass die Volumecontainer auf dem ersten Gerät mit erfolgtem Failover die Dateneigentümerschaft verlieren. Nach einem solchen Failover werden diese Volumecontainer anders angezeigt oder verhalten sich anders bei der Anzeige im Verwaltungsportal. | Ja | Nein |
| 9 | Installation | Während der Installation von StorSimple-Adapter für SharePoint müssen Sie die IP-Adresse eines Geräts angeben, damit die Installation erfolgreich abgeschlossen wird. | | Ja | Nein |
| 10 | Webproxy | Wenn Ihre Webproxykonfiguration das Protokoll "HTTPS" verwendet, ist die Kommunikation zwischen dem Gerät und dem Dienst beeinträchtigt, und das Gerät wird offline geschaltet. Supportpakete werden bei diesem Vorgang ebenfalls generiert. Sie beanspruchen auf Ihrem Gerät erhebliche Ressourcen. | Stellen Sie sicher, dass "HTTP" als Protokoll für die Webproxy-URL angegeben ist. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](https://msdn.microsoft.com/library/azure/dn764937.aspx). | Ja | Nein |
| 11 | Webproxy | Wenn Sie den Webproxy für ein registriertes Gerät konfigurieren und aktivieren, müssen Sie den aktiven Controller auf Ihrem Gerät neu starten. | | Ja | Nein |
| 12 | Hohe Cloud-Latenzen und hohe E/A-Arbeitsauslastung | Wenn Ihr StorSimple-Gerät mit einer Kombination aus sehr hohen Cloud-Latenzen (mehrere Sekunden) und hoher E/A-Arbeitsauslastung konfrontiert wird, verschlechtert sich die Leistung der Gerätevolumes, und es tritt ggf. der E/A-Fehler "Gerät nicht bereit" auf. | Sie müssen die Gerätecontroller manuell neu starten oder ein Gerätefailover ausführen, um dieses Problem zu beheben. | Ja | Nein |

##Updates für das physische Gerät in der Oktober-Version

Wenn diese Updates auf ein physisches Gerät angewendet werden, ändert sich die Version der Software in 6.3.9600.17312. Sofern nicht anders angegeben, gelten diese Versionshinweise für alle Modelle des StorSimple-Geräts. Weitere Informationen zu diesen Updates finden Sie unter den Hinweisen zum [Softwareupdate vom Oktober 2014 für physikalische Geräte mit der Microsoft Azure StorSimple Appliance](http://support.microsoft.com/kb/2986997).

##Updates des SAS-Controllers (Serial Attached SCSI) und der Firmware in der Oktober-Version

Diese Version aktualisiert die Treiber und die Firmware für den SAS-Controller Ihres physischen Geräts. Weitere Informationen zum SAS-Controllerupdate finden Sie unter [Update vom Oktober 2014 für die LSI-SAS-Controller in der Microsoft Azure StorSimple Appliance](http://support.microsoft.com/kb/2987020).

Diese Version wendet auch ein kumulatives Firmwareupdate zur Behebung von Zuverlässigkeitsproblemen mit den Hardwarekomponenten des Geräts an. Weitere Informationen zum Firmwareupdate finden Sie unter [Firmwareupdate vom Oktober 2014 für die Microsoft Azure StorSimple Appliance](http://support.microsoft.com/kb/2987015).

##Updates für das virtuelle Gerät in der Oktober-Version

Diese Version enthält keine Updates für das virtuelle Gerät. Durch die Installation des Updates ändert sich die Softwareversion des virtuellen Geräts nicht.

<!--HONumber=52-->
