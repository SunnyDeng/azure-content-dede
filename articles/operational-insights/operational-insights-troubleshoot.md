<properties
   pageTitle="Behandeln von Problemen mit Operational Insights"
   description="Erfahren Sie mehr über das Behandeln von Problemen mit Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

#Behandeln von Problemen mit Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Die Informationen in den folgenden Abschnitten unterstützen Sie bei der Problembehandlung. Wenn das vorliegende Problem nicht in diesem Artikel enthalten ist, können Sie den [Operational Insights-Teamblog](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx) zurate ziehen.

## Diagnostizieren von Verbindungsproblemen für Operational Insights

Da Microsoft Azure Operational Insights auf Daten beruht, die in die Cloud und aus der Cloud verschoben werden, können Verbindungsprobleme Vorgänge beeinträchtigen. Anhand der folgenden Informationen können Sie Ihre Verbindungsprobleme verstehen und lösen.


**Fehlermeldung:** Die Internetverbindung wurde überprüft, die Verbindung mit dem Operational Insights-Dienst konnte jedoch nicht hergestellt werden. Bitte versuchen Sie es später noch einmal.

**Mögliche Ursachen:** - Der Operational Insights-Dienst wird gewartet. Warten Sie, bis die Wartung von Operational Insights abgeschlossen ist. - Operational Insights wurde von Ihrem Netzwerk blockiert. Wenden Sie sich an Ihren Netzwerkadministrator, und fordern Sie Zugriff auf Operational Insights an, oder verwenden Sie einen anderen Server als Gateway.

**Fehlermeldung:** Internetverbindung konnte nicht hergestellt werden. Überprüfen Sie Ihre Proxyeinstellungen.

**Mögliche Ursachen:** - Dieser Server ist nicht mit dem Internet verbunden. Überprüfen Sie den Status der Internetkonnektivität, und verbinden Sie den Server mit dem Internet. - Die Proxyeinstellung ist nicht korrekt. Informationen zum Einrichten oder Ändern Ihrer Proxyeinstellungen finden Sie unter [Konfigurieren des Proxys und der Firewalleinstellungen](operational-insights-proxy-firewall.md). - Der Proxyserver erfordert eine Authentifizierung. Informationen zum Konfigurieren von Operations Manager für die Verwendung eines Proxyservers finden Sie unter [Konfigurieren des Proxys und der Firewalleinstellungen](operational-insights-proxy-firewall.md).


## Problembehandlung bei der SQL Server-Ermittlung

Wenn Sie Microsoft SQL Server 2008 R2 ausführen und trotz Bereitstellung des Operations Manager-Agents keine Warnungen für diesen Server angezeigt werden, liegt möglicherweise ein Ermittlungsproblem vor.

Um sicherzustellen, dass dies die Fehlerursache ist, überprüfen Sie die folgenden zwei Probleme:

- Im Operations Manager-Ereignisprotokoll wird die Ereignis-ID 4001 aufgeführt. Dieses Ereignis weist darauf hin, dass eine ungültige Klasse vorliegt.

- Wenn Sie im SQL Server-Konfigurations-Manager die SQL Server-Dienste anzeigen, sehen Sie die Fehlermeldung „Fehler bei Remoteprozeduraufruf. [0x0800706be]“.

Wenn beide Probleme vorliegen, müssen Sie SQL Server 2008 R2 Service Pack 2 installieren. Sie können dieses Service Pack im Microsoft Download Center unter [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) herunterladen.

Nach der Installation des Service Packs sollten innerhalb von 24 Stunden Operational Insights-Daten für den Server angezeigt werden.

## Problembehandlung bei Agents oder beim Operations Manager-Datenfluss zu Operational Insights

Der folgende Satz von Verfahren dient als Anleitung zum Beheben von Problemen mit direkt angeschlossenen Agents oder Operations Manager-Bereitstellungen, die für das Berichten von Daten an Azure Operational Insights konfiguriert sind.

### 1. Verfahren: Überprüfen, ob in die Operations Manager-Umgebung die richtigen Management Packs heruntergeladen werden
>[AZURE.NOTE]Wenn Sie nur Direct Agent verwenden, können Sie mit dem nächsten Verfahren fortfahren.

Je nachdem, welche Lösungen (bisher als „Intelligence Packs“ bezeichnet) Sie im OpInsights-Portal aktiviert haben, werden unterschiedlich viele Management Packs angezeigt. Suchen Sie im Namen nach dem Schlüsselwort "Advisor" oder "Intelligence". Sie können diese Management Packs mit OpsMgr PowerShell suchen:

```Powershell
Get-SCOMManagementPack | where {$_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version
Get-SCOMManagementPack | where {$_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version
```

>[AZURE.NOTE]Wenn Sie die Problembehandlung für die Capacity-Lösung durchführen, prüfen Sie, *wie viele* Management Packs „Capacity“ im Namen enthalten: Es gibt zwei Management Packs mit demselben Anzeigenamen (jedoch unterschiedlichen internen IDs), die in demselben Management Pack-Paket enthalten sind. Wenn eines der beiden nicht importiert wird (häufig aufgrund fehlender VMM-Abhängigkeiten), wird auch das andere Management Pack nicht importiert, und der Vorgang wird nicht wiederholt.

Die folgenden drei Management Packs mit „Capacity“ im Namen sollten angezeigt werden: - Microsoft System Center Advisor Capacity Intelligence Pack - Microsoft System Center Advisor Capacity Intelligence Pack - Microsoft System Center Advisor Capacity Storage Data

Wenn nur ein oder zwei davon, jedoch nicht alle drei, aufgeführt werden, entfernen Sie diese, und warten Sie 5 bis 10 Minuten, bis Operations Manager sie erneut herunterlädt und importiert – überprüfen Sie die Ereignisprotokolle auf Fehler während dieses Zeitraums.

### 2. Verfahren: Überprüfen, ob die richtigen Lösungen in Ihren Direct Agent heruntergeladen werden
>[AZURE.NOTE]Wenn Sie nur Operations Manager verwenden, können Sie dieses Verfahren ignorieren.

Im Direct Agent sollte die Zwischenspeicherung der Richtlinie für die Lösungsdatensammlung unter **C:\Programme\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs** angezeigt werden.


### 3. Verfahren: Überprüfen, ob Daten an den Advisor-Dienst gesendet werden (oder ob dies zumindest versucht wird)
Je nachdem, ob Sie direkt verbundene Agents oder Operations Manager verwenden, können Sie das folgende Verfahren auf dem Direct Agent-Computer oder auf dem Operations Manager-Verwaltungsserver ausführen:

1. - Öffnen Sie den Systemmonitor.
2. - Wählen Sie „Verwaltungsgruppen des Integritätsdiensts“.
3. - Fügen Sie alle Leistungsindikatoren hinzu, die mit „HTTP“ beginnen.

Bei einer korrekten Konfiguration sollte eine Aktivität für diese Leistungsindikatoren angezeigt werden, während Ereignisse und andere Datenelemente (basierend auf den im Portal eingebundenen Lösungen und den konfigurierten Richtlinien zur Protokollsammlung) hochgeladen werden. Diese Leistungsindikatoren müssen nicht unbedingt dauerhaft beschäftigt sein. Wenn Sie wenig oder keine Aktivität feststellen, haben Sie möglicherweise nicht viele Lösungen eingebunden oder verwenden nur eine sehr einfache Sammlungsrichtlinie.

### 4. Verfahren: Überprüfen der Ereignisprotokolle des Verwaltungsservers oder von Direct Agent auf Fehler
Wenn alle oben genannten Schritte nicht erfolgreich sind und weiterhin keine Daten vom Dienst empfangen werden, sollten Sie abschließend noch überprüfen, ob in der **Ereignisanzeige** Fehler angezeigt werden.

Öffnen Sie **Ereignisanzeige** –> **Anwendungs- und Dienstprotokolle** –> **Operations Manager**, und filtern Sie nach Ereignisquellen: **Advisor**, **Integritätsdienstmodule**, **HealthService** und **Service Connector** (das letzte Element gilt nur für Direct Agent).

Die meisten dieser Ereignisse sind in Operations Manager und in Direct Agent ähnlich, ebenso wie die Schritte zur Problembehandlung. Der einzige Teil, der sich zwischen Operations Manager und Direct Agent unterscheidet, ist der Registrierungsprozess (GUI in Operations Manager; Kombination aus ID und Schlüssel im Direct Agent-Arbeitsbereich). Nach der ersten Registrierung werden jedoch die Zertifikate ausgetauscht und verwendet, und alles weitere in der Kommunikation mit dem Dienst stimmt überein.

Daher gelten viele dieser Ereignisse für beide Arten der Berichterstellungsinfrastruktur. Hier sehen Sie eine Liste der gängigsten Einträge, die möglicherweise angezeigt werden.

### Ereignisse aus der Quelle „Integritätsdienstmodule“
##### Ereignis-ID 2138
Dies weist darauf hin, dass für den Proxy eine Authentifizierung erforderlich ist. Befolgen Sie die Schritte zum [Konfigurieren von Proxyservern](https://msdn.microsoft.com/library/azure/dn884643.aspx).

##### Ereignis-ID 2137
Operations Manager kann das Authentifizierungszertifikat nicht lesen. Durch erneutes Ausführen des Assistenten für die Advisor-Registrierung werden Zertifikate/ausführende Konten korrigiert.

##### Ereignis-ID 2132
Bedeutet **Nicht autorisiert**. Es könnte ein Problem mit dem Zertifikat und/oder der Registrierung beim Dienst vorliegen. Versuchen Sie erneut, den Registrierungs-Assistenten auszuführen, um Zertifikate und ausführende Konten zu korrigieren. Stellen Sie darüber hinaus sicher, dass der Proxy für das Zulassen von Ausschlüssen festgelegt wurde. Weitere Informationen finden Sie unter [Konfigurieren von Proxyservern](https://msdn.microsoft.com/library/azure/dn884643.aspx).

##### Ereignis-ID 2129
Dies ist eine fehlerhafte Verbindung aufgrund einer fehlerhaften SSL-Aushandlung. Stellen Sie sicher, dass Ihre Systeme für die Verwendung von TLS und nicht für SSL konfiguriert sind. Auf diesem Server könnten einige ungewöhnliche SSL-Einstellungen im Hinblick auf die Verschlüsselung vorliegen, entweder in den erweiterten Internet Explorer-Optionen (**Erweitert**) oder in der Windows-Registrierung unter folgendem Schlüssel:

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

##### Ereignis-ID 2127
Fehler beim Senden des Daten empfangen-Fehlercodes. Wenn dies nur hin und wieder auftritt, liegt möglicherweise nur eine Störung vor. Achten Sie darauf, wie häufig dieser Fehler auftritt. Wenn dasselbe Problem sehr häufig angezeigt wird (etwa alle 10 Minuten über einen längeren Zeitraum), könnte ein echtes Problem vorliegen. Überprüfen Sie die Netzwerkkonfiguration und die oben beschriebenen Proxyeinstellungen, und führen Sie den Registrierungs-Assistenten erneut aus. Wenn dieser Fall jedoch nur sporadisch (d. h. ein paar Mal am Tag) auftritt, ist wahrscheinlich alles in Ordnung, da Daten in die Warteschlange eingereiht und übertragen werden. Wahrscheinlich handelt es sich nur um ein Netzwerktimeout, wenn das Problem in dieser Frequenz auftritt.

Einige der HTTP-Fehlercodes haben eine besondere Bedeutung:

- Beim ERSTEN Mal, wenn ein MMA Direct Agent oder ein Verwaltungsserver Daten an den Dienst zu senden versucht, erhalten Sie einen Fehler 500 mit einem internen Fehlercode 404. 404 bedeutet "nicht gefunden" und weist darauf hin, dass der Speicherbereich, der für diesen neuen Arbeitsbereich verwendet werden soll, noch nicht bereit ist – er wird derzeit noch bereitgestellt. Bei der nächsten Wiederholung wird dieser jedoch bereit sein, und der Datenfluss wird (unter normalen Bedingungen) funktionieren.
- 403 weist möglicherweise auf ein Problem mit den Berechtigungen/Anmeldeinformationen etc. hin.

##### Ereignis-ID 2128
Fehler bei der DNS-Namensauflösung. Ihr Server kann die Internetadresse nicht auflösen, an die Daten gesendet werden sollen. Dies kann an den Einstellungen der DNS-Auflösung auf Ihrem Computer, fehlerhaften Proxyeinstellungen oder einem (temporären) Problem mit DNS bei Ihrem Anbieter liegen. Wie beim vorherigen Ereignis kann dies je nachdem, ob es ständig oder nur hin und wieder auftritt, auf ein echtes Problem hinweisen – oder auch nicht.

##### Ereignis-ID 2130
Timeout. Wie beim vorherigen Ereignis kann dies je nachdem, ob es ständig oder hin und wieder auftritt, auf ein Problem hinweisen – oder auch nicht.

### Ereignisse der Quelle „HealthService“
##### Ereignis-ID 4511
Modul „System.PublishDataToEndPoint“ kann nicht geladen werden – Datei nicht gefunden. Fehler bei der Initialisierung eines Moduls vom Typ „System.PublishDataToEndPoint“ (CLSID „{D407D659-65E4-4476-BF40-924E56841465}“) mit Fehlercode Die angegebene Datei wurde nicht gefunden. Dieser Fehler weist darauf hin, dass auf dem Computer alte DLLs vorliegen, welche die erforderlichen Module nicht enthalten. Die Korrektur besteht darin, Ihre Verwaltungsserver auf das neueste Updaterollup zu aktualisieren.

##### Ereignis-ID 4502
Modul abgestürzt. Wenn dies für Workflows mit Namen wie **CollectInstanceSpace** oder **CollectTypeSpace** auftritt, kann dies auf Probleme des Servers beim Senden einiger Konfigurationsdaten hinweisen. Je nachdem, ob dieses Ereignis ständig oder hin und wieder auftritt, kann dies auf ein Problem hinweisen – oder auch nicht. Wenn es häufiger als einmal pro Stunde auftritt, liegt in jedem Fall ein Problem vor. Wenn dieser Vorgang nur ein- oder zweimal pro Tag auftritt, ist alles in Ordnung, und eine Wiederherstellung ist möglich. Je nachdem, wie beim Modul tatsächlich ein Fehler auftritt (die Beschreibung weist weitere Details auf), liegt möglicherweise ein lokales Problem – d. h. beim Erfassen in der Datenbank – oder ein Problem beim Senden an die Cloud vor. Überprüfen Sie Ihre Netzwerk- und Proxyeinstellungen, und versuchen Sie im ungünstigsten Fall, den Integritätsdienst neu zu starten.

##### Ereignis-ID 4501
Das Modul "System.PublishDataToEndPoint" ist abgestürzt. Ein Modul des Typs „System.PublishDataToEndPoint“ hat den Fehler 87L gemeldet. Es wurde als Teil der Regel „Microsoft.SystemCenter.CollectAlertChangeDataToCloud“ ausgeführt, die für die Instanz „Operations Manager Management Group“ mit id:„{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}“ mit in der Verwaltungsgruppe „SCOMTEST“ ausgeführt wurde. Dieses Ereignis sollte *nicht* länger mit genau diesem Workflow, Modul und Fehler auftreten, da es sich um einen [*behobenen* Fehler](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale) handelt. Wenn Sie dieses Ereignis erneut feststellen, melden Sie es über Ihren bevorzugten Microsoft Support-Kanal.


### Ereignisse der Quelle „Service Connector“
##### Ereignis-ID 4002
Der Dienst hat den HTTP-Statuscode 403 als Antwort auf eine Abfrage zurückgegeben. Überprüfen Sie mit dem Dienstadministrator die Integrität des Diensts. Die Abfrage wird später wiederholt. Sie können einen 403-Fehler während der ersten Registrierungsphase des Agents erhalten. Sie sehen dann eine URL wie „https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest“. Fehlercode 403 bedeutet „Unzulässig“. Der Grund ist meist ein fehlerhafter Kopiervorgang einer WorkspaceId oder eines Schlüssels, oder die Uhr ist nicht mit dem Computer synchronisiert. Versuchen Sie die Synchronisierung mit einer zuverlässigen Zeitquelle, und stellen Sie mithilfe der Konnektivitätsprüfung im Systemsteuerungs-Applet für Microsoft Monitoring Agent sicher, dass Sie die richtige Arbeitsbereichs-ID und den richtigen Schlüssel verwenden.


### 5. Verfahren: Suchen nach den Agents, um Daten zu senden und im Portal zu indizieren
Melden Sie sich im OpInsights-Portal an, und navigieren Sie von der Seite „Übersicht“ zur kleinen Kachel **Server und Verwendung**. Es wird angezeigt, ob Verwaltungsgruppen (und deren Agents) und Direct Agents Daten an die Protokollsuche melden. Die Anzahl der Agents in der Kachel leitet sich aus den Daten ab. Wenn Computer zwei Wochen lang keine Daten melden, werden sie aus dem Netz entfernt.

Über Drilldowns gelangen Sie zur Protokollsuche. Die Zeitstempel der letzten indizierten Daten werden für jeden Computer angezeigt. Von dort aus können Sie untersuchen, um welche Daten es sich handelt. Je nach Umfang der konfigurierten Datensammlung und der Art der Lösungen können der Zeitplan und die Geschwindigkeit des Datenuploads variieren.

Diese Seite bietet zudem Messungsinformationen (hierbei wird nicht der Index der Protokollsuche, sondern das Abrechnungssystem verwendet, und die Daten werden alle paar Stunden aktualisiert) über die an den Dienst gesendeten Datenmengen, aufgeschlüsselt nach Lösung.

<!---HONumber=July15_HO4-->