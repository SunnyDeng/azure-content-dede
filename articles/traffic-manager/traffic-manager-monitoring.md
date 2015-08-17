<properties 
   pageTitle="Traffic Manager-Überwachung"
   description="In diesem Artikel werden die Traffic Manager-Überwachung und ihre Konfiguration erläutert."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# Traffic Manager-Überwachung

Azure Traffic Manager kann Ihre Endpunkte inklusive Cloud-Diensten und Websites überwachen, um sicherzustellen, dass sie verfügbar sind. Damit die Überwachung ordnungsgemäß funktioniert, müssen Sie Traffic Manager für jeden Endpunkt, den Sie in den Profileinstellungen angeben, auf die gleiche Weise einrichten. Nach dem Konfigurieren der Überwachung zeigt Traffic Manager den Status Ihrer Endpunkte und Ihres Profile im Verwaltungsportal an. Sie können die Überwachungseinstellungen im Verwaltungsportal auf der Konfigurationsseite für Ihr Traffic Manager-Profil konfigurieren. Sie können die folgenden Einstellungen angeben:

- **Protokoll** – Wählen Sie HTTP oder HTTPS aus. Beachten Sie, dass bei der HTTPS-Überwachung nicht überprüft wird, ob Ihr SSL-Zertifikat gültig ist. Es wird nur überprüft, ob es vorhanden ist.

- **Port** – Wählen Sie den Port aus, der für die Anforderung verwendet wird. Die Standardports lauten 80 für HTTP und 443 für HTTPS.

- **Relativer Pfad und Dateiname** – Geben Sie den Pfad und den Namen der Datei an, auf die das Überwachungssystem zuzugreifen versucht. Beachten Sie, dass ein Schrägstrich "/" ein gültiger Eintrag für den relativen Pfad ist und voraussetzt, dass sich die Datei im Stammverzeichnis befindet (Standard).

## Informationen zur Überwachung des Integritätsstatus

Azure Traffic Manager zeigt die Integrität von Profil und Endpunkt im Verwaltungsportal an. In der Statusspalte für das Profil und dem Endpunkt wird der zuletzt ermittelte Status angezeigt. Mit diesem Status können Sie den Zustand der Profile gemäß Ihrer Traffic Manager-Überwachungseinstellungen ermitteln. Wenn das Profil fehlerfrei (integer) ist, werden DNS-Abfragen an Ihre Dienste basierend auf den Lastenausgleichseinstellungen für das Profil (Roundrobin, Leistung oder Failover) verteilt. Sobald das Traffic Manager-Überwachungssystem eine Änderung des überwachten Status erkennt, wird der Statuseintrag im Verwaltungsportal aktualisiert. Es kann bis zu fünf Minuten dauern, bis die Statusänderung aktualisiert wird.

### Überwachungsstatus von Endpunkten

Der Überwachungsstatus von Endpunkten in der folgenden Tabelle ist das Ergebnis einer Kombination von Tests der Integrität des Endpunkts und Ihres Profils und der Endpunktkonfigurationen.

|Profilstatus|Endpunktstatus|Überwachungsstatus von Endpunkten (API und Portal)|Hinweise|
|---|---|---|---|
|Deaktiviert|Aktiviert|Inaktiv|Deaktivierte Profile werden nicht überwacht. Allerdings kann der Endpunktstatus innerhalb der deaktivierten Profile weiterhin verwaltet werden.|
|&lt;any&gt;|Deaktiviert|Deaktiviert|Deaktivierte Profile werden nicht überwacht. Allerdings kann der Endpunktstatus innerhalb der deaktivierten Profile weiterhin verwaltet werden.|
|Aktiviert|Aktiviert|Online|Endpunkt wird überwacht und ist fehlerfrei.|
|Aktiviert|Aktiviert|Heruntergestuft|Endpunkt wird überwacht und ist fehlerhaft.|
|Aktiviert|Aktiviert|CheckingEndpoint|Endpunkt wird überwacht, die Ergebnisse der ersten Überprüfung wurden aber noch nicht empfangen. Dieser Status ist vorübergehend, wenn Sie dem Profil gerade einen neuen Endpunkt hinzugefügt haben oder nur einen Endpunkt oder ein Profil aktiviert haben.|
|Aktiviert|Aktiviert|Beendet|Der zugrunde liegende Clouddienst oder die Website wird nicht ausgeführt.|

### Überwachungsstatus von Profilen

Der Überwachungsstatus von Profilen in der folgenden Tabelle ist das Ergebnis der Kombination des Status der Endpunktüberwachung und des konfigurierten Profils.

|Profilstatus (wie konfiguriert)|Überwachungsstatus von Endpunkten|Profilüberwachungsstatus (API und Portal)|Hinweise|
|---|---|---|---|
|Deaktiviert|&lt;any&gt; oder ein Profil ohne definierte Endpunkte.|Deaktiviert|Endpunkte werden nicht überwacht.|
|Aktiviert|Der Status mindestens eines Endpunkts lautet "Heruntergestuft".|Heruntergestuft|Das ist eine Kennzeichnung, die Kundenaktion erforderlich macht.|
|Aktiviert|Der Status mindestens eines Endpunkts lautet "Online". Keine Endpunkte sind "Heruntergestuft".|Online|Der Dienst akzeptiert Datenverkehr, und es ist keine Kundenaktion erforderlich.|
|Aktiviert|Der Status mindestens eines Endpunkts lautet "CheckingEndpoint". Es sind keine Endpunkte "Online" oder "Heruntergestuft".|CheckingEndpoints|Übergangsstatus. Dies tritt i. d. R. auf, wenn ein Profil gerade aktiviert wurde und die Endpunkt-Integrität getestet wird.|
|Aktiviert|Der Status aller im Profil definierten Endpunkte ist "Deaktiviert" oder "Beendet" oder im Profil wurden keine Endpunkte definiert.|Inaktiv|Es sind keine Endpunkte aktiv, das Profil ist jedoch weiterhin aktiviert.|

## Funktionsweise der Überwachung

Ein beispielhafter zeitlicher Ablauf zur Veranschaulichung der Überwachung mit einem Cloud-Dienst wird unten gezeigt. Dieses Szenario zeigt Folgendes:

- Der Cloud-Dienst ist verfügbar und empfängt Datenverkehr NUR über dieses Traffic Manager-Profil.

- Der Cloud-Dienst ist nicht verfügbar.

- Der Cloud-Dienst verbleibt für einen längeren Zeitraum als die DNS-Gültigkeitsdauer (TTL) nicht verfügbar.

- Der Cloud-Dienst ist wieder verfügbar.

- Der Cloud-Dienst empfängt wieder Datenverkehr AUSSCHLIESSLICH über dieses Traffic Manager-Profil.

![Ablauf der Traffic Manager-Überwachung](./media/traffic-manager-monitoring/IC697947.jpg)

**Abbildung 1** – Beispiel für den Ablauf der Überwachung. Die Zahlen im Diagramm entsprechen der nummerierten Erklärung unten.

1. **GET** – Das Traffic Manager-Überwachungssystem führt einen GET-Befehl für den Pfad und die Datei aus, die Sie in den Überwachungseinstellungen angegeben haben.
2. **200 OK** – Das Überwachungssystem erwartet eine HTTP 200 OK-Nachricht innerhalb von zehn Sekunden. Wenn es diese Meldung empfängt, wird davon ausgegangen, dass der Cloud-Dienst verfügbar ist. 

>[AZURE.NOTE]Traffic Manager geht davon aus, dass nur ein Endpunkt online ist, wenn die Antwortnachricht "200 OK" lautet. Wenn nicht "200 OK" als Antwort empfangen wird, wird davon ausgegangen, dass der Endpunkt nicht verfügbar ist. Der Vorgang wird dann als Überprüfungsfehler gewertet. Weitere Informationen zur Behandlung von Fehler bei Überprüfungen finden Sie unter [Problembehandlung beim Status "Heruntergestuft" in Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

3. **30 Sekunden zwischen Überprüfungen** – Diese Überprüfung wird alle 30 Sekunden ausgeführt.
4. **Clouddienst nicht verfügbar** – Der Clouddienst ist nicht verfügbar. Traffic Manager kann den Status erst bei der nächsten Überwachung ermitteln.
5. **Zugriffsversuche auf die Überwachungsdatei (4 Versuche)** – Das Überwachungssystem führt einen GET-Befehl durch, empfängt jedoch nicht innerhalb von maximal zehn Sekunden eine Antwort. Es werden dann drei weitere Versuche in Intervallen von 30 Sekunden durchgeführt. Dies bedeutet, dass es höchstens 1,5 Minuten dauert, bis das Überwachungssystem erkennt, wann ein Dienst nicht mehr verfügbar ist. Wenn einer der Versuche erfolgreich ist, wird die Anzahl der Versuche zurückgesetzt. Auch wenn dies nicht explizit im Diagramm dargestellt wird gilt: Wenn die 200 OK-Meldung mehr als zehn Sekunden nach dem GET-Befehl empfangen wird, wertet das Überwachungssystem dies als eine fehlgeschlagene Überprüfung.
6. **Markiert heruntergestuft** – Nach dem vierten aufeinanderfolgenden Fehler kennzeichnet das Überwachungssystem den nicht verfügbaren Clouddienst als "Heruntergestuft". 

7. **Datenverkehr zum Clouddienst verringert** – Es wird möglicherweise weiterhin Datenverkehr an den nicht verfügbaren Clouddienst weitergeleitet. Bei den Clients tritt dann ein Fehler auf, da der Dienst nicht verfügbar ist. Clients und sekundäre DNS-Server speichern den DNS-Eintrag für die IP-Adresse des nicht verfügbaren Cloud-Diensts zwischen. Daher lösen sie auch weiterhin den DNS-Namen der Unternehmensdomäne in die IP-Adresse des Diensts auf. Darüber hinaus geben sekundäre DNS-Server möglicherweise weiterhin die DNS-Informationen des nicht verfügbaren Diensts weiter. Wenn die Clients und sekundären DNS-Server aktualisiert werden, verringert sich der Datenverkehr an die IP-Adresse des nicht verfügbaren Diensts. Das Überwachungssystem führt weiterhin alle 30 Sekunden Überprüfungen durch. In diesem Beispiel antwortet der Dienst nicht und bleibt weiterhin nicht verfügbar.
8. **Datenverkehr zum Clouddienst beendet** – Nach dieser Zeit sollten die meisten DNS-Server und -Clients aktualisiert und der Datenverkehr zum nicht verfügbaren Dienst beendet werden. Die maximale Zeit vor der vollständigen Beendigung des Datenverkehrs hängt von der Gültigkeitsdauer (TTL) ab. Die DNS-Standardgültigkeitsdauer ist 300 Sekunden (fünf Minuten). Gemäß diesem Wert verwenden Clients den Dienst nach fünf Minuten nicht mehr. Das Überwachungssystem führt weiterhin alle 30 Sekunden eine Überprüfung durch, auch wenn der Cloud-Dienst nicht reagiert.
9. **Cloud-Dienst ist wieder online und empfängt Datenverkehr** – Der Dienst ist wieder verfügbar, aber Traffic Manager kennt den Status erst nach der nächsten Überprüfung durch das Überwachungssystem.
10. **Datenverkehr zum Dienst wiederhergestellt** – Traffic Manager sendet einen GET-Befehl und empfängt eine 200 OK-Meldung in weniger als zehn Sekunden. Anschließend wird wieder der DNS-Name des Cloud-Diensts an DNS-Server ausgeben, sobald diese Aktualisierungen anfordern. Damit wird auch der Datenverkehr an den Dienst wieder aufgenommen.

## Status untergeordneter und übergeordneter Endpunkte bei geschachtelten Profilen

Die folgende Tabelle zeigt das Verhalten der Traffic Manager-Überwachung bei den untergeordneten und übergeordneten Profilen eines geschachtelten Profils und die minChildEndpoints-Einstellung. Weitere Informationen finden Sie unter [Traffic Manager - Übersicht](traffic-manager-overview.md).

|Überwachungsstatus von untergeordneten Profilen|Überwachungsstatus von übergeordneten Endpunkten|Hinweise|
|---|---|---|
|Deaktiviert – Das Profil wurde von Ihnen deaktiviert.|Beendet|Der Status des übergeordneten Endpunkts ist "Beendet", nicht "Deaktiviert". Der Status "Deaktiviert" zeigt lediglich an, dass Sie den Endpunkt im übergeordneten Profil deaktiviert haben.|
|Heruntergestuft - Der Status mindestens eines Endpunkts lautet "Heruntergestuft".|Online – Status, wenn die Anzahl der Online-Endpunkte im untergeordneten Profil mindestens den Wert des Status "minChildEndpoints.CheckingEndpoint" hat, sofern die Anzahl der Endpunkte mit "Online" plus "CheckingEndpoint" im untergeordneten Profil mindestens den Wert "minChildEndpoints.Otherwise" im Status "Heruntergestuft" aufweist|Der Datenverkehr wird an einen Endpunkt mit dem Status "CheckingEndpoint" weitergeleitet. Wenn "minChildEndpoints" zu hoch festgelegt wurde, hat der übergeordnete Endpunkt stets den Status "Heruntergestuft".|
|Online – Mindestens ein untergeordneter Endpunkt hat den Status "Online" und keiner den Status "Heruntergestuft".|Wie oben.||
|CheckingEndpoints – Mindestens einer weist "CheckingEndpoint" auf, keiner ist "Online" oder "Heruntergestuft".|Wie oben.||
|Inaktiv – Alle Endpunkte sind "Deaktiviert" oder "Beendet", oder dies ist ein Profil ohne Endpunkte|Beendet||

## So konfigurieren Sie die Überwachung für einen bestimmten Pfad und Dateinamen

1. Erstellen Sie eine Datei mit dem gleichen Namen für jeden Endpunkt, den Sie in das Profil einfügen möchten.
2. Testen Sie für jeden Endpunkt mit einem Webbrowser den Zugriff auf die Datei. Die URL besteht aus dem Domänennamen des jeweiligen Endpunkts (Cloud-Dienst oder Website), dem Pfad zur Datei und dem Dateinamen. 
3. Geben Sie im Verwaltungsportal unter **Überwachungseinstellungen** im Feld **Relativer Pfad und Dateiname** den Pfad und den Dateinamen an.
4. Wenn Sie Ihre Änderungen an der Konfiguration abgeschlossen haben, klicken Sie unten auf der Seite auf **Speichern**.

## Siehe auch

[Traffic Manager – Übersicht](traffic-manager-overview.md)

[Informationen zu Lastenausgleichsmethoden von Traffic Manager](traffic-manager-load-balancing-methods.md)

[Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Problembehandlung beim Status "Heruntergestuft" in Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md)
 

<!---HONumber=August15_HO6-->