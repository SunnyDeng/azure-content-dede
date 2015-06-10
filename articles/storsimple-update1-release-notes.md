<properties 
    pageTitle="Versionsanmerkungen zu Update 1.0 der StorSimple 8000 Serie"
    description="Beschreibt die neuen Features sowie Probleme und Problemumgehungen für Update 1 der StorSimple 8000 Serie."
    services="storsimple"
    documentationCenter="NA"
    authors="alkohli"
    manager="adinah"
    editor="tysonn" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="05/27/2015"
    ms.author="alkohli" />

# Versionsanmerkungen zu Update 1.0 der StorSimple 8000 Serie  

## Übersicht

Die folgenden Versionsanmerkungen beschreiben die neuen Features und weisen auf wichtige offene Punkte bei Update 1.0 der StorSimple 8000 Serie hin. Sie enthalten außerdem eine Liste der StorSimple-Software- und Firmware-Updates, die in dieser Version enthalten sind. Dies ist die erste Hauptversion, nachdem die Freigabeversion der StorSimple 8000 Serie im Juli 2014 allgemein verfügbar gemacht wurde.

Dieses Update ändert die Gerätesoftware in StorSimple 8000 Serie Update 1. Lesen Sie vor der Bereitstellung des Updates in Ihrer StorSimple-Lösung die Informationen in den Versionsanmerkungen sorgfältig durch. Weitere Informationen finden Sie unter [Installieren von Update 1 auf dem StorSimple-Gerät](storsimple-install-update-1.md).

Lesen Sie vor der Bereitstellung der Updates in Ihrer StorSimple-Lösung die Informationen in den Versionsanmerkungen sorgfältig durch.

>[AZURE.IMPORTANT]
> 
- Verwenden Sie den StorSimple-Manager-Dienst und nicht Windows PowerShell für StorSimple, um Update 1.0 zu installieren.
- Diese Version enthält außerdem Festplatten-Firmware-Updates, die nur angewendet werden können, wenn sich das Gerät im Wartungsmodus befindet. Hierbei handelt es sich um störende Updates, die zu Ausfallzeiten des Geräts führen. Sie können diese Updates während der geplanten Wartung anwenden.
- Die Installation dieses Updates (einschließlich der Windows-Updates) dauert ungefähr 5 bis 10 Stunden. 

## Neuerungen in Update 1

Dieses Update enthält die folgenden neuen Features und Verbesserungen:

- **Migration von Geräten der 5000-7000 Serie auf Geräte der 8000 Serie** – Die vorliegende Version enthält eine neue Migrationsfunktion, die dem Benutzer von Geräten der StorSimple 5000-7000 Serie die Migration seiner Daten auf ein physisches Gerät der StorSimple 8000 Serie oder ein virtuelles Gerät vom Typ 1100 ermöglicht. Das Migrationsfeature bietet zwei wichtige Nutzenversprechen:                                                                  

    - **Geschäftskontinuität**, durch Ermöglichen der Migration von vorhandenen Daten auf Geräten der 5000-7000 Serie auf Geräte der 8000 Serie.
    - **Verbesserte Feature-Angebote von Geräten der 8000 Serie**, wie z. B. effiziente zentralisierte Verwaltung mehrerer Geräte durch den StorSimple-Manager-Dienst, hochwertigere Hardware und aktualisierte Firmware, virtuelle Geräte, Datenmobilität und Features in der zukünftigen Roadmap.

    Ausführliche Informationen zur Durchführung der Migration von einem Gerät der 5000-7000 Serie auf ein Gerät der 8000 Serie finden Sie im [Migrationshandbuch](http://www.microsoft.com/download/details.aspx?id=47322).

- **Verfügbarkeit im Azure-Portal für Behörden** – StorSimple ist jetzt im Azure-Portal für Behörden verfügbar. Erfahren Sie mehr über die [Bereitstellung eines StorSimple-Geräts im Azure-Portal für Behörden](storsimple-deployment-walkthrough-gov.md).

- **Unterstützung weiterer Cloud-Dienstanbieter** – Die weiteren unterstützten Cloud-Dienstanbieter sind Amazon S3, Amazon S3 mit RRS, HP und OpenStack (Beta).

- **Update auf die neuesten Speicher-APIs** – Mit dieser Version wurde StorSimple auf die neuesten Azure-Speicherdienst-APIs aktualisiert. Geräte der StorSimple 8000 Serie, auf denen GA ausgeführt wird, verwenden Versionen der Azure-Speicherdienst-APIs, die vor dem 12. Februar 2012 liegen. Wie in der [Ankündigung zum Ablösen von Speicherdienstversionen](http://azure.microsoft.com/blog/2014/08/04/microsoft-azure-storage-service-version-removal/) bekannt gegeben, werden diese APIs bis zum 10. Dezember 2015 als veraltet markiert. Es ist zwingend erforderlich, Update 1.0 für die StorSimple 8000 Serie vor dem 9. Dezember 2015 anzuwenden. Andernfalls funktionieren StorSimple-Geräte nicht mehr ordnungsgemäß.

- **Unterstützung von zonenredundantem Speicher (ZRS)** – Durch das Upgrade auf die neueste Version der Speicher-APIs unterstützt die StorSimple 8000 Serie außer lokal redundantem Speicher (LRS) und georedundantem Speicher (GRS) auch zonenredundanten Speicher (ZRS). Ausführliche Informationen über zonenredundanten Speicher (ZRS) finden Sie in diesem [Artikel über Redundanzoptionen von Azure Storage](storage-redundancy.md).

- **Verbesserte Bereitstellungs-und Update-Erfahrung** – In dieser Version wurden die Installations- und Update-Prozesse verbessert. Die Installation durch den Setup-Assistenten wurde verbessert, um dem Benutzer Feedback bereitzustellen, wenn Netzwerkkonfiguration und Firewall-Einstellungen nicht korrekt sind. Es wurden zusätzliche Diagnose-Cmdlets bereitgestellt, um Sie bei der Problembehandlung bei der Vernetzung des Geräts zu unterstützen. Weitere Informationen zu den neuen Diagnose-Cmdlets für die Problembehandlung finden Sie im [Artikel über die Problembehandlung bei der Bereitstellung](storsimple-troubleshoot-deployment/#cmdlets-available-for-troubleshooting.md) verwendet.

## Behobene Probleme in Update 1


Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in diesem Update behoben wurden.

| Nr. | Funktion | Problem | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell für StorSimple | Beim Remotezugriff eines Benutzers auf das StorSimple-Gerät mit Windows PowerShell für StorSimple und anschließendem Starten des Setup-Assistenten kam es zu einem Absturz, wenn die IP-Adresse von Data 0 eingegeben wurde. Dieses Problem wurde in Update 1 behoben. | Ja | Ja |
| 2 | Zurücksetzen auf Werkseinstellungen | Unter bestimmten Umständen kann sich das StorSimple-Gerät beim Zurücksetzen auf die Werkseinstellungen aufhängen und zeigt dann die folgende Nachricht an: **Zurücksetzung auf Werkseinstellungen wird ausgeführt (Phase 8)**. Dies kann vorkommen, wenn Sie STRG+C drücken, während das Cmdlet ausgeführt wird. Dieses Problem wurde jetzt behoben.| Ja | Nein |
| 3 | Zurücksetzen auf Werkseinstellungen | Nach der fehlgeschlagenen Zurücksetzung einer Dual-Controller-Factory auf die Werkseinstellungen konnte die Geräteregistrierung fortgesetzt werden. Dies führte zu einer nicht unterstützten Systemkonfiguration. In Update 1 wird eine Fehlermeldung angezeigt, und auf einem Gerät, das eine fehlgeschlagene Factory-Zurücksetzung aufweist, wird die Registrierung blockiert. | Ja | Nein |
| 4 | Zurücksetzen auf Werkseinstellungen | In einigen Fällen wurden falsch positive Warnungen aufgrund fehlender Übereinstimmung ausgelöst. Falsche Warnungen aufgrund fehlender Übereinstimmung werden auf Geräten mit Update 1 nicht mehr generiert. | Ja | Nein |
| 5 | Zurücksetzen auf Werkseinstellungen | Wenn die Zurücksetzung auf Werkseinstellungen vor der vollständigen Ausführung unterbrochen wurde, wechselte das Gerät in den Wiederherstellungsmodus und der Zugriff auf Windows PowerShell für StorSimple war nicht mehr möglich. Dieses Problem wurde jetzt behoben. | Ja | Nein |
| 6 | Notfallwiederherstellung | Ein Fehler der Notfallwiederherstellung wurde behoben, der ein Fehlschlagen der Notfallwiederherstellung beim Wiederherstellen von Sicherungen auf dem Zielgerät verursacht hat. | Ja | Ja |
| 7 | Überwachungs-LEDs | In einigen Fällen zeigten Überwachungs-LEDs auf der Geräterückseite nicht den korrekten Status an. Die blaue LED wurde ausgeschaltet. DATA 0- und DATA 1-LEDs blinkten, selbst wenn diese Schnittstellen nicht konfiguriert waren. Das Problem wurde behoben, und die Überwachungs-LEDs zeigen jetzt den korrekten Status an. | Ja | Nein |
| 8 | Netzwerkschnittstellen | In früheren Versionen konnte ein StorSimple-Gerät, das mit einem nicht routbaren Gateway konfiguriert war, offline geschaltet werden. In dieser Version wurde für DATA 0 die niedrigste Routingmetrik festgelegt. Daher wird selbst bei Cloud-Aktivierung anderer Schnittstellen der gesamte Cloud-Datenverkehr vom Gerät über DATA 0 geleitet. | Ja | Ja | 

## Bekannte Probleme in Update 1

Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme in dieser Version.

| Nr. | Funktion | Problem | Kommentare/Problemumgehung | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Datenträgerquorum | In seltenen Fällen kann der Speicherpool offline geschaltet werden, wenn der Großteil der Datenträger im EBOD-Gehäuse eines 8600-Geräts getrennt wird, sodass kein Datenträgerquorum verfügbar ist. Der Speicherpool bleibt offline, auch wenn die Verbindung zu den Datenträgern wiederhergestellt wird. | Sie müssen das Gerät neu starten. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. | Ja | Nein |
| 2 | Falsche Controller-ID | Beim Austausch eines Controllers kann es vorkommen, dass Controller 0 als Controller 1 angezeigt wird. Während des Controlleraustauschs kann die Controller-ID anfänglich als ID des Peercontrollers angezeigt werden, wenn das Image vom Peerknoten geladen wurde. In seltenen Fällen kann dieses Verhalten auch nach einem Neustart des Systems auftreten. | Es ist keine Benutzeraktion erforderlich. Dieses Problem löst sich von selbst, nachdem der Controlleraustausch abgeschlossen ist. | Ja | Nein |
| 3 | Speicherkonten | Das Verwenden des Speicherdiensts zum Löschen des Speicherkontos wird nicht unterstützt. Dies führt dazu, dass keine Benutzerdaten abgerufen werden können. | Ja | Ja |
| 4 | Gerätefailover | Mehrere Failover eines Volumecontainers von demselben Quellgerät auf verschiedene Zielgeräte werden nicht unterstützt. Das Failover von einem einzelnen nicht reagierenden Gerät auf mehrere Geräte führt dazu, dass die Volumecontainer auf dem ersten Gerät mit erfolgtem Failover die Dateneigentümerschaft verlieren. Nach einem solchen Failover werden diese Volumecontainer anders angezeigt oder verhalten sich anders bei der Anzeige im Verwaltungsportal. | | Ja | Nein |
| 5 | Installation | Während der Installation von StorSimple-Adapter für SharePoint müssen Sie die IP-Adresse eines Geräts angeben, damit die Installation erfolgreich abgeschlossen wird. | | Ja | Nein |
| 6 | Webproxy | Wenn Ihre Webproxykonfiguration das Protokoll "HTTPS" verwendet, ist die Kommunikation zwischen dem Gerät und dem Dienst beeinträchtigt, und das Gerät wird offline geschaltet. Supportpakete werden bei diesem Vorgang ebenfalls generiert. Sie beanspruchen auf Ihrem Gerät erhebliche Ressourcen. | Stellen Sie sicher, dass "HTTP" als Protokoll für die Webproxy-URL angegeben ist. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](https://msdn.microsoft.com/library/azure/dn764937.aspx). | Ja | Nein |
| 7 | Webproxy | Wenn Sie den Webproxy für ein registriertes Gerät konfigurieren und aktivieren, müssen Sie den aktiven Controller auf Ihrem Gerät neu starten. | | Ja | Nein |
| 8 | Hohe Cloud-Latenzen und hohe E/A-Arbeitsauslastung | Wenn Ihr StorSimple-Gerät mit einer Kombination aus sehr hohen Cloud-Latenzen (mehrere Sekunden) und hoher E/A-Arbeitsauslastung konfrontiert wird, verschlechtert sich die Leistung der Gerätevolumes, und es tritt ggf. der E/A-Fehler "Gerät nicht bereit" auf. | Sie müssen die Gerätecontroller manuell neu starten oder ein Gerätefailover ausführen, um dieses Problem zu beheben. | Ja | Nein |
| 9 | Azure PowerShell | Bei Verwendung des StorSimple-Cmdlets **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait**, um das erste Objekt zu markieren, damit ein neues **Volumecontainer** -Objekt erstellt werden kann, gibt das Cmdlet alle Objekte zurück. | Schließen Sie das Cmdlet wie folgt in Klammern ein:**(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** | Ja | Ja |
| 10| Migration | Wenn mehrere Volumecontainer für die Migration übergeben werden, ist der ETA für die neueste Sicherung nur für den ersten Volumecontainer korrekt. Außerdem tritt nach erfolgter Migration der ersten 4 Sicherungen im ersten Volumecontainer Parallelmigration auf. | Es wird empfohlen, immer nur einen Volumecontainer gleichzeitig zu migrieren. | Ja | Nein |
| 11| Migration | Nach der Wiederherstellung werden Volumes nicht der Sicherungsrichtlinie oder dem virtuellen Datenträger hinzugefügt. | Zum Erstellen von Sicherungen müssen diese Volumes einer Sicherungsrichtlinie hinzugefügt werden. | Ja | Ja |
| 12| Migration | Nach Abschluss die Migration darf das Gerät der 5000/7000 Serie nicht auf die migrierten Datencontainer zugreifen. | Es wird empfohlen, die migrierten Datencontainer zu löschen, nachdem die Migration vollständig abgeschlossen ist. | Ja | Nein |
| 13| Klonen und Notfallwiederherstellung | Ein StorSimple-Gerät mit Update 1 kann für ein Gerät, auf dem ältere Software als Update 1 ausgeführt wird, keinen Klonvorgang bzw. keine Notfallwiederherstellung durchführen. | Das Zielgerät muss auf Update 1 aktualisiert werden, damit diese Operationen ausgeführt werden können. | Ja | Ja |
| 14 | Migration | Die Konfigurationssicherung für die Migration kann auf einem Gerät der Serie 5000-7000 fehlschlagen, wenn Volumegruppen ohne zugehörige Volumes vorhanden sind. | Löschen Sie die leeren Volumegruppen ohne zugehörige Volumes, und wiederholen Sie dann die Konfigurationssicherung.| Ja | Nein |

## Updates von physischen Geräten in Update 1

Wenn diese Updates auf ein physisches Gerät angewendet werden, ändert sich die Version der Software in 6.3.9600.17491.

## Updates des SAS-Controllers (Serial Attached SCSI) und der Firmware in Update 1

Diese Version aktualisiert die Treiber und die Firmware für den SAS-Controller Ihres physischen Geräts. Außerdem wird die Festplatten-Firmware auf dem Gerät aktualisiert.
 
- Weitere Informationen zum SAS-Controller-Update finden Sie unter [Update 1 für LSI-SAS-Controller im Microsoft Azure StorSimple-Gerät](http://bemis.partners.extranet.microsoft.com/203/_layouts/ArticlePages/DisplayArticlePage.aspx?List=27d133d4%2D10ab%2D4795%2Dbb81%2D092dfe8c7866&ID=732454&RootFolder=%2F203%2FLists%2FPssxmlArticles%2F005). 

- Weitere Informationen zum Firmware-Update finden Sie unter [Firmware-Update 1 für das Microsoft Azure StorSimple-Gerät](http://bemis.partners.extranet.microsoft.com/203/_layouts/ArticlePages/DisplayArticlePage.aspx?List=27d133d4%2D10ab%2D4795%2Dbb81%2D092dfe8c7866&ID=767385&RootFolder=%2F203%2FLists%2FPssxmlArticles%2F414).

- Weitere Informationen zum Datenträger-Firmware-Update finden Sie unter [Datenträger-Firmware-Update 1 für das Microsoft Azure StorSimple-Gerät](http://bemis.partners.extranet.microsoft.com/203/_layouts/ArticlePages/DisplayArticlePage.aspx?List=27d133d4%2D10ab%2D4795%2Dbb81%2D092dfe8c7866&ID=767387&RootFolder=%2F203%2FLists%2FPssxmlArticles%2F416).
 
## Updates von virtuellen Geräten in Update 1

Diese Version enthält keine Updates für das virtuelle Gerät. Durch die Installation des Updates ändert sich die Softwareversion des virtuellen Geräts nicht.

## Nächste Schritte

- [Installieren von Update 1 auf dem Gerät](storsimple-install-update-1.md)

<!---HONumber=58-->