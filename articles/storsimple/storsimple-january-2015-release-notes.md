<properties 
    pageTitle="Versionsanmerkungen zu Update 0.2 der StorSimple 8000-Serie – Januar 2015"
    description="Beschreibt die neuen Features, Probleme und Problemumgehungen für die Microsoft Azure StorSimple-Version vom Januar 2015."
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
    ms.date="06/02/2015"
    ms.author="v-sharos" />


# Versionsanmerkungen zu Update 0.2 der StorSimple 8000-Serie – Januar 2015

## Übersicht

Dieses Dokument beschreibt die wichtigsten offenen Fragen für die Microsoft Azure StorSimple-Version von Januar 2015. Es enthält auch eine Liste der StorSimple-Software- und -Firmware-Updates, die in dieser Version enthalten sind. Dies ist die zweite Version, nachdem die Freigabeversion der StorSimple 8000-Serie im Juli 2014 allgemein verfügbar gemacht wurde.
  
Dieses Update ändert die Softwareversion des physischen Geräts nach dem Oktober-Update nicht. Es bleibt weiterhin Version 6.3.9600.17312. Das vom Image des virtuellen Geräts verwendete Image wurde in dieser Version geändert. Daher wird für alle neuen nach dem 20.01.2015 erstellten virtuellen Geräte die Version 6.3.9600.17361 angezeigt.

Lesen Sie die folgenden Informationen in den Versionshinweisen für das Update vom Januar 2015.

> [AZURE.IMPORTANT]

- Dieses Update ist nicht über Windows Update verfügbar und kann nicht wie andere Updates installiert werden. Ihr Gerät erhält dieses Update selbst dann nicht, wenn Sie die Updates über das Verwaltungsportals angewendet haben. Dieses Update gilt nur für virtuelle Geräte, die nach dem 20. Januar 2015 erstellt wurden. 
 
- Die Januar-Version von StorSimple enthält keine Updates des physischen StorSimple-Geräts. Sie können weiterhin alle verfügbaren Windows-Updates für das virtuelle Gerät, einschließlich der neuesten Sicherheitsupdates, anwenden, es wird aber keine Änderung der Version für das physische StorSimple-Gerät angezeigt.

## Neuigkeiten in der Januar-Version

Dieses Update behebt ein Problem, durch das die Volumes auf dem virtuellen Gerät offline geschaltet werden. (Siehe [In dieser Version behobene Probleme](#issues-fixed-in-the-january-release).)

Das Update enthält keine neuen Features oder Funktionen.

## In der Januar-Version behobene Probleme

Die folgende Tabelle beschreibt das Problem, das in diesem Update behoben wurde.

|Nr.|Funktion|Problem|Gilt für das physische Gerät|Gilt für das virtuelle Gerät 
|---|-------|-----|--------------------------|-------------------------
|1|Wechsel von Volumes in den Offlinemodus|Wenn Cloud-Latenzen für mehrere Minuten andauern, wechseln die Volumes des virtuellen StorSimple-Geräts auf den Hosts in den Offlinemodus. Dieses Update erhöht den Schwellenwert für die Cloud-Latenzen und minimiert dadurch die Situationen, in denen die Volumes auf Hosts offline gehen.|Nein|Ja  

## Bekannte Probleme in der Januar-Version

Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme in dieser Version.
 
|Nr.|Funktion|Problem|Kommentare/Problemumgehung|Gilt für das physische Gerät|Gilt für das virtuelle Gerät 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1|	Zurücksetzen auf Werkseinstellungen|	Unter bestimmten Umständen kann das StorSimple-Gerät beim Zurücksetzen auf die Werkseinstellungen nicht mehr reagieren und die folgende Nachricht anzeigen: **Zurücksetzung auf Werkseinstellungen wird ausgeführt (Phase 8)**. Dies kann vorkommen, wenn Sie STRG+C drücken, während das Cmdlet ausgeführt wird.|	Drücken Sie STRG+C nicht nach dem Einleiten einer Zurücksetzung auf die Werkseinstellungen. Wenn dies bereits erfolgt ist, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten.|Ja|Nein|
|2|Datenträgerquorum|	In seltenen Fällen kann der Speicherpool offline geschaltet werden, wenn der Großteil der Datenträger im EBOD-Gehäuse eines 8600-Geräts getrennt wird, sodass kein Datenträgerquorum verfügbar ist. Der Speicherpool bleibt offline, auch wenn die Verbindung zu den Datenträgern wiederhergestellt wird.|Sie müssen das Gerät neu starten. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten.|Ja |Nein
|3|Fehler bei der Cloud-Momentaufnahme|In seltenen Fällen tritt ggf. ein Fehler **Maximales Sicherungslimit erreicht** einer Cloud-Momentaufnahme auf. Dies kann vorkommen, wenn die Anzahl von 255 Onlineklonen auf demselben Gerät überschritten wird, die von demselben ursprünglichen Volume stammen, das gelöscht wurde.||Ja|Ja
|4|Falsche Controller-ID|Beim Austausch eines Controllers kann es vorkommen, dass Controller 0 als Controller 1 angezeigt wird. Während des Controlleraustauschs kann die Controller-ID anfänglich als ID des Peercontrollers angezeigt werden, wenn das Image vom Peerknoten geladen wurde. In seltenen Fällen kann dieses Verhalten auch nach einem Neustart des Systems auftreten.|Es ist keine Benutzeraktion erforderlich. Dieses Problem löst sich von selbst, nachdem der Controlleraustausch abgeschlossen ist.|Ja|Nein 
|5|	Geräteüberwachungsdiagramme|Im StorSimple-Manager-Dienst funktionieren die Geräteüberwachungsdiagramme nicht, wenn in der Proxyserverkonfiguration für das Gerät die Standard- oder NTLM-Authentifizierung aktiviert ist.|Ändern Sie die Webproxykonfiguration für das beim StorSimple-Manager-Dienst registrierte Gerät so, dass für die Authentifizierung die Option KEINE festgelegt ist. Führen Sie zu diesem Zweck das Windows PowerShell für StorSimple-Cmdlet "Set-HcsWebProxy" aus.|Ja|Ja
|6|	Speicherkonten|Das Verwenden des Speicherdiensts zum Löschen des Speicherkontos wird nicht unterstützt. Dies führt dazu, dass keine Benutzerdaten abgerufen werden können.|| Ja |	Ja
|7|Gerätefailover|	Mehrere Failover eines Volumecontainers von demselben Quellgerät auf verschiedene Zielgeräte werden nicht unterstützt.|	Das Failover von einem einzelnen nicht reagierenden Gerät auf mehrere Geräte führt dazu, dass die Volumecontainer auf dem ersten Gerät mit erfolgtem Failover die Dateneigentümerschaft verlieren. Nach einem solchen Failover werden diese Volumecontainer anders angezeigt oder verhalten sich anders bei der Anzeige im Verwaltungsportal.|Ja|Nein
|8|	Installation|Während der Installation von StorSimple-Adapter für SharePoint müssen Sie die IP-Adresse eines Geräts angeben, damit die Installation erfolgreich abgeschlossen wird.||Ja|Nein
|9|	Webproxy|Wenn Ihre Webproxykonfiguration das Protokoll "HTTPS" verwendet, ist die Kommunikation zwischen dem Gerät und dem Dienst beeinträchtigt, und das Gerät wird offline geschaltet. Supportpakete werden bei diesem Vorgang ebenfalls generiert. Sie beanspruchen auf Ihrem Gerät erhebliche Ressourcen.|Stellen Sie sicher, dass "HTTP" als Protokoll für die Webproxy-URL angegeben ist. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](https://msdn.microsoft.com/library/azure/dn764937.aspx).|Ja |Nein
|10|Webproxy|	Wenn Sie den Webproxy für ein registriertes Gerät konfigurieren und aktivieren, müssen Sie den aktiven Controller auf Ihrem Gerät neu starten.||	Ja |Nein
|11|Hohe Cloud-Latenzen und hohe E/A-Arbeitsauslastung|Wenn Ihr StorSimple-Gerät mit einer Kombination aus sehr hohen Cloud-Latenzen (mehrere Sekunden) und hoher E/A-Arbeitsauslastung konfrontiert wird, verschlechtert sich die Leistung der Gerätevolumes, und es tritt ggf. der E/A-Fehler "Gerät nicht bereit" auf.|Sie müssen die Gerätecontroller manuell neu starten oder ein Gerätefailover ausführen, um dieses Problem zu beheben.|Ja|Nein

## Updates für das physische Gerät in der Januar-Version

Dieses Update enthält keine anderen Änderungen am StorSimple-Gerät.

## Updates des SAS-Controllers (Serial Attached SCSI) und der Firmware in der Januar-Version

Diese Version enthält keine Updates für den SAS-Controller (Serial Attached SCSI) oder die Firmware. Das Treiberupdate wurde in der Version vom Oktober 2014 durchgeführt.

## Updates für das virtuelle Gerät in der Januar-Version

Diese Version enthält ein aktualisiertes Image für das virtuelle Gerät. Für alle nach dem 20.01.2015 erstellten virtuellen Geräte wird die Version 6.3.9600.17361 angezeigt.

 

<!---HONumber=August15_HO6-->