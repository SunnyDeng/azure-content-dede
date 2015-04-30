<properties 
	pageTitle="Operational Insights - Problembehandlung von Agents oder des Operations Manager-Datenflusses" 
	description="Enthält Informationen zum Behandeln von Problemen mit direkt verbundenen Agents und dem Operations Manager-Datenfluss zu Azure Operational Insights" 
	services="operational-insights" 
	documentationCenter="" 
	authors="dani3l3" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/23/2015" 
	ms.author="dmuscett"/>


#Problembehandlung bei Agents oder beim Operations Manager-Datenfluss zu Operational Insights
Der folgende Satz von Verfahren dient als Anleitung zum Beheben von Problemen mit direkt angeschlossenen Agents oder Operations Manager-Bereitstellungen, die für das Berichten von Daten an Azure Operational Insights konfiguriert sind.

##Verfahren 1: Überprüfen Sie, ob die richtigen Management Packs in die Operations Manager-Umgebung heruntergeladen werden
*Hinweis: Wenn Sie nur Direct Agent verwenden, können Sie mit dem nächsten Verfahren fortfahren.*

Je nachdem, welche Intelligence Packs Sie im OpInsights-Portal aktiviert haben, werden unterschiedlich viele Management Packs angezeigt. Suchen Sie im Namen nach dem Schlüsselwort "Advisor" oder "Intelligence". 
Sie können diese Management Packs mit OpsMgr PowerShell suchen:

      Get-SCOMManagementPack | where {$_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version
      Get-SCOMManagementPack | where {$_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version
    
Hinweis: Wenn Sie die Problembehandlung für das Capacity Intelligence Pack durchführen, prüfen Sie, WIE VIELE Management Packs 'Capacity' im Namen enthalten: Es gibt zwei Management Packs mit demselben Anzeigenamen (jedoch unterschiedlichen internen IDs), die in demselben Management Pack-Paket enthalten sind. Wenn eines der beiden nicht importiert wird (häufig aufgrund fehlender VMM-Abhängigkeiten), wird auch das andere Management Pack nicht importiert und der Vorgang wird nicht wiederholt.

Es sollten die folgenden drei Management Packs im Zusammenhang mit 'Capacity' aufgeführt werden
1. Microsoft System Center Advisor Capacity Intelligence Pack 
1. Microsoft System Center Advisor Capacity Intelligence Pack 
1. Microsoft System Center Advisor Capacity Storage Data 

Wenn nur ein oder zwei davon, jedoch nicht alle drei, aufgeführt werden, entfernen Sie diese, und warten Sie 5 bis 10 Minuten, bis Operations Manager sie erneut herunterlädt und importiert - überprüfen Sie die Ereignisprotokolle auf Fehler während dieses Zeitraums.

##Verfahren 2: Überprüfen Sie, ob die richtigen Intelligence Packs in Ihren Direct Agent heruntergeladen werden
*Hinweis: Wenn Sie nur Operations Manager verwenden, können Sie dieses Verfahren ignorieren.*

Im Direct Agent sollte die Zwischenspeicherung der Intelligence Packs-Datensammlungsrichtlinie unter **C:\Programme\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs** angezeigt werden


##Verfahren 3: Überprüfen Sie, ob Daten an den Advisor-Dienst gesendet werden (oder ob dies zumindest versucht wird)
Je nachdem, ob Sie direkt verbundene Agents oder Operations Manager verwenden, können Sie das folgende Verfahren auf dem Direct Agent-Computer oder auf dem Operations Manager-Verwaltungsserver ausführen:

1. - Öffnen Sie den 'Systemmonitor' 
1. - Wählen Sie 'Verwaltungsgruppen des Integritätsdiensts'
1. - Fügen Sie alle Leistungsindikatoren hinzu, die mit 'HTTP' beginnen

Bei einer korrekten Konfiguration sollte eine Aktivität für diese Leistungsindikatoren angezeigt werden, während Ereignisse und andere Datenelemente (basierend auf den im Portal eingebundenen Intelligence Packs und den konfigurierten Richtlinien zur Protokollsammlung) hochgeladen werden. Diese Leistungsindikatoren müssen nicht unbedingt dauerhaft 'beschäftigt' sein. Wenn Sie wenig oder keine Aktivität feststellen, haben Sie möglicherweise nicht viele Intelligence Packs eingebunden oder verwenden nur eine sehr einfache Sammlungsrichtlinie. 

##Verfahren 4: Überprüfen Sie die Ereignisprotokolle des Verwaltungsservers oder von Direct Agent auf Fehler 
Wenn alle oben genannten Schritte nicht zum Ergebnis führen, jedoch weiterhin keine Daten vom Dienst empfangen werden, überprüfen Sie, ob in der **Ereignisanzeige** Fehler angezeigt werden.

Öffnen Sie die **Ereignisanzeige** -> **Anwendungen und Dienste** -> **Operations Manager**, und filtern Sie nach Ereignisquellen: **Advisor**, **Health Service-Module**, **HealthService** und **Service Connector** (letzterer gilt nur für Direct Agent). 

Die meisten dieser Ereignisse sind in Operations Manager und in Direct Agent ähnlich, ebenso wie die Schritte zur Problembehandlung. 
Der einzige Teil, der sich zwischen Operations Manager und Direct Agent unterscheidet, ist der Registrierungsprozess (GUI in Operations Manager; Kombination aus ID und Schlüssel im Direct Agent-Arbeitsbereich). Nach der ersten Registrierung werden jedoch die Zertifikate ausgetauscht und verwendet, und alles weitere in der Kommunikation mit dem Dienst stimmt überein.

Daher gelten viele dieser Ereignisse für beide Arten der Berichterstellungsinfrastruktur. Hier sehen Sie eine Liste der gängigsten Einträge, die möglicherweise angezeigt werden.

###Ereignisse aus der Quelle 'Health Service-Module'
#####Ereignis-ID 2138
Dies weist darauf hin, dass für den Proxy eine Authentifizierung erforderlich ist. Befolgen Sie die Schritte zum [Konfigurieren von Proxyservern](https://msdn.microsoft.com/library/azure/dn884643.aspx)

#####Ereignis-ID 2137
Operations Manager kann das Authentifizierungszertifikat nicht lesen. Durch erneutes Ausführen des Assistenten für die Advisor-Registrierung werden Zertifikate/ausführende Konten korrigiert.

#####Ereignis-ID 2132
Bedeutet **Nicht autorisiert**. Es könnte ein Problem mit dem Zertifikat und/oder der Registrierung beim Dienst vorliegen. Versuchen Sie erneut, den Registrierungs-Assistenten auszuführen, um Zertifikate und ausführende Konten zu korrigieren. Stellen Sie darüber hinaus sicher, dass der Proxy für das Zulassen von Ausschlüssen festgelegt wurde. Weitere Informationen finden Sie unter [Konfigurieren von Proxy-Servern](https://msdn.microsoft.com/library/azure/dn884643.aspx)

#####Ereignis-ID 2129
Dies ist eine fehlerhafte Verbindung aufgrund einer fehlerhaften SSL-Aushandlung. Stellen Sie sicher, dass Ihre Systeme für die Verwendung von TLS und nicht für SSL konfiguriert sind. Auf diesem Server könnten einige ungewöhnliche SSL-Einstellungen im Hinblick auf die Verschlüsselung vorliegen, entweder in den **erweiterten** Internet Explorer-Optionen oder in der Windows-Registrierung unter folgendem Schlüssel: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

#####Ereignis-ID 2127
Fehler beim Senden des Daten empfangen-Fehlercodes. Wenn dies nur hin und wieder auftritt, liegt möglicherweise nur eine Störung vor. Achten Sie darauf, wie häufig dieser Fehler auftritt. Wenn dasselbe Problem sehr häufig angezeigt wird (etwa alle 10 Minuten über einen längeren Zeitraum), könnte ein echtes Problem vorliegen. Überprüfen Sie die Netzwerkkonfiguration und die oben beschriebenen Proxyeinstellungen, und führen Sie den Registrierungs-Assistenten erneut aus. Wenn dieser Fall jedoch nur sporadisch (d. h. ein paar Mal am Tag) auftritt, ist wahrscheinlich alles in Ordnung, da Daten in die Warteschlange eingereiht und übertragen werden. Wahrscheinlich handelt es sich nur um ein Netzwerktimeout, wenn das Problem in dieser Frequenz auftritt.
 
Einige der HTTP-Fehlercodes haben eine besondere Bedeutung: 

- Beim ERSTEN Mal, wenn ein MMA Direct Agent oder ein Verwaltungsserver Daten an den Dienst zu senden versucht, erhalten Sie einen Fehler 500 mit einem internen Fehlercode 404. 404 bedeutet "nicht gefunden" und weist darauf hin, dass der Speicherbereich, der für diesen neuen Arbeitsbereich verwendet werden soll, noch nicht bereit ist - er wird derzeit noch bereitgestellt. Bei der nächsten Wiederholung wird dieser jedoch bereit sein, und der Datenfluss wird (unter normalen Bedingungen) funktionieren.
- 403 weist möglicherweise auf ein Problem mit den Berechtigungen/Anmeldeinformationen etc. hin. 

#####Ereignis-ID 2128
Fehler bei der DNS-Namensauflösung. Ihr Server kann die Internetadresse nicht auflösen, an die Daten gesendet werden sollen. Dies kann an den Einstellungen der DNS-Auflösung auf Ihrem Computer, fehlerhaften Proxyeinstellungen oder einem (temporären) Problem mit DNS bei Ihrem Anbieter liegen. Wie beim vorherigen Ereignis kann dies je nachdem, ob es ständig oder 'hin und wieder' auftritt, auf ein echtes Problem hinweisen - oder auch nicht.

#####Ereignis-ID 2130
Timeout. Wie beim vorherigen Ereignis kann dies je nachdem, ob es ständig oder hin und wieder auftritt, auf ein Problem hinweisen - oder auch nicht.

### Ereignisse aus der Quelle 'HealthService'
#####Ereignis-ID 4511
Modul "System.PublishDataToEndPoint" kann nicht geladen werden - Datei nicht gefunden. Die Initialisierung eines Moduls vom Typ "System.PublishDataToEndPoint" (CLSID "{D407D659-65E4-4476-BF40-924E56841465}") hat einen Fehler mit Fehlercode hervorgerufen. Das System kann die angegebene Datei nicht finden.  
Dieser Fehler weist darauf hin, dass auf dem Computer alte DLLs vorliegen, welche die erforderlichen Module nicht enthalten. Die Korrektur besteht darin, Ihre Verwaltungsserver auf das neueste Updaterollup zu aktualisieren.

#####Ereignis-ID 4502
Modul abgestürzt. Wenn dies für Workflows mit Namen wie **CollectInstanceSpace** oder **CollectTypeSpace** auftritt, kann dies auf Probleme des Servers beim Senden einiger Konfigurationsdaten hinweisen. Je nachdem, ob dieses Ereignis ständig oder hin und wieder auftritt, kann dies auf ein Problem hinweisen - oder auch nicht. Wenn es häufiger als einmal pro Stunde auftritt, liegt in jedem Fall ein Problem vor. Wenn dieser Vorgang nur ein- oder zweimal pro Tag auftritt, ist alles in Ordnung, und eine Wiederherstellung ist möglich. Je nachdem, wie beim Modul tatsächlich ein Fehler auftritt (die Beschreibung weist weitere Details auf), liegt möglicherweise ein lokales Problem - d. h. beim Erfassen in der Datenbank - oder ein Problem beim Senden an die Cloud vor. Überprüfen Sie Ihre Netzwerk- und Proxyeinstellungen, und versuchen Sie im ungünstigsten Fall, den Integritätsdienst neu zu starten.

#####Ereignis-ID 4501
Das Modul "System.PublishDataToEndPoint" ist abgestürzt. Ein Modul des Typs "System.PublishDataToEndPoint" hat einen Fehler 87L gemeldet. Es wurde als Teil der Regel "Microsoft.SystemCenter.CollectAlertChangeDataToCloud" ausgeführt, die für die Instanz "Operations Manager Management Group" mit id:"{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}" in der Verwaltungsgruppe "SCOMTEST" ausgeführt wurde. 
Dieses Ereignis sollte nicht länger mit genau diesem Workflow, Modul und Fehler auftreten, da es sich um [einen *behobenen* Fehler](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale) handelt. Wenn Sie dieses Ereignis erneut feststellen, melden Sie es über Ihren bevorzugten Microsoft Support-Kanal.


### Ereignisse aus der Quelle 'Service Connector'
#####Ereignis-ID 4002
Der Dienst hat den HTTP-Statuscode 403 als Antwort auf eine Abfrage zurückgegeben.  Überprüfen Sie mit dem Dienstadministrator die Integrität des Diensts. Die Abfrage wird später wiederholt. Sie können einen Fehler 403 während der ersten Registrierungsphase des Agents erhalten. Eine URL wie https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest wird angezeigt.
Fehlercode 403 bedeutet 'Nicht zulässig' - Dies bezieht sich normalerweise auf falsch kopierte Arbeitsbereichs-IDs oder Schlüssel, oder die Uhr ist auf dem Computer nicht synchronisiert. Versuchen Sie die Synchronisierung mit einer zuverlässigen Zeitquelle, und stellen Sie mithilfe der Konnektivitätsprüfung im Systemsteuerungs-Applet für Microsoft Monitoring Agent sicher, dass Sie die richtige Arbeitsbereichs-ID und den richtigen Schlüssel verwenden. 





##Verfahren 5: Suchen Sie nach den Agents, um Daten zu senden und im Portal zu indizieren
Melden Sie sich im OpInsights-Portal an, und navigieren Sie von der Seite "Übersicht" zur kleinen Kachel **Server und Verwendung**. Es wird angezeigt, ob Verwaltungsgruppen (und deren Agents) und Direct Agents Daten an Search melden. Die Anzahl der Agents in der Kachel leitet sich aus den Daten ab. Wenn Computer zwei Wochen lang keine Daten melden, werden sie aus dem Netz entfernt.

Über Drilldowns gelangen Sie zu Search. Die Zeitstempel der letzten indizierten Daten werden für jeden Computer angezeigt. Von dort aus können Sie untersuchen, um welche Daten es sich handelt. Je nach Umfang der konfigurierten Datensammlung und Intelligence Packs können der Zeitplan und die Geschwindigkeit des Datenuploads variieren.

Diese Seite bietet zudem Messungsinformationen (hierbei wird nicht der Search-Index, sondern das Abrechnungssystem verwendet, die Daten werden alle paar Stunden aktualisiert) über die an den Dienst gesendeten Datenmengen, aufgeschlüsselt nach Intelligence Pack.

<!--HONumber=52-->