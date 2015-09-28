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
   ms.date="09/10/2015"
   ms.author="banders" />

# Behandeln von Problemen mit Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Die Informationen in den folgenden Abschnitten unterstützen Sie bei der Problembehandlung. Wenn das vorliegende Problem nicht in diesem Artikel enthalten ist, können Sie den [Operational Insights-Teamblog](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx) zurate ziehen.

## Problembehandlung bei Problemen mit SQL Assessment
Die Operations Management Suite (OMS) verwendet Microsoft Monitoring Agent und Operations Manager-Verwaltungsgruppen, um Daten zu erfassen und an den OMS-Dienst zu senden. Bestimmte Arbeitsauslastungen, z. B. SQL Server, erfordern spezifische Berechtigungen zum Ausführen der Datenerfassung in einem anderen Sicherheitskontext, z. B. ein Domänenkonto. Wenn Microsoft Monitoring Agent über System Center Operations Manager verbunden ist und Berechtigungsprobleme beim Erfassen von Daten auftreten, müssen Sie Anmeldeinformationen bereitstellen, indem Sie ein ausführendes Konto konfigurieren.

Wenn Sie bereits das SQL Server Management Pack verwenden, sollten Sie dieses Windows-Konto als ausführendes Konto verwenden.

### So konfigurieren Sie das ausführende SQL-Konto in der Betriebskonsole

1. Öffnen Sie in Operations Manager die Betriebskonsole, und klicken Sie dann auf **Verwaltung**.

2. Klicken Sie unter **Ausführen als-Konfiguration** auf **Profile**, und öffnen Sie **Ausführendes Profil für Microsoft System Center Advisor**.

3. Klicken Sie auf der Seite **Ausführende Konten** auf **Hinzufügen**.

4. Wählen Sie ein ausführendes Windows-Konto aus, das die Anmeldeinformationen für SQL Server enthält, oder klicken Sie auf **Neu**, um eines zu erstellen.

    >[AZURE.NOTE]Der Typ des ausführenden Kontos muss "Windows" sein. Das ausführende Konto muss auch Teil der lokalen Administratorengruppe auf allen Windows-Servern sein, auf denen SQL Server-Instanzen gehostet werden.

5. Klicken Sie auf **Speichern**.

6. Ändern und führen Sie dann das folgende T-SQL-Beispiel auf jeder SQL Server-Instanz aus, um dem ausführenden Konto die erforderlichen Mindestberechtigungen zum Ausführen von SQL Assessment zu erteilen. Dies ist jedoch nicht erforderlich, wenn ein ausführendes Konto bereits Teil der Serverrolle "sysadmin" auf SQL Server-Instanzen ist.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

### To configure the SQL Run As account using Windows PowerShell
Alternatively, you can use the following PowerShell script to set the SQL Run As account. Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```
After the PowerShell Script finishes executing, perform the T-SQL commands provided above.

## Diagnose connection issues for Operational Insights

Because Microsoft Azure Operational Insights relies on data that is moved to and from the cloud, connection issues can be crippling. Use the following information to understand and solve your connection issues.


**Error message:** The Internet connectivity was verified, but connection to Operational Insights service could not be established. Please try again later.

**Possible causes:**
- The Operational Insights service is under maintenance. Wait until the Operational Insights maintenance is done.
- Your network has blocked Operational Insights. Contact your network administrator and request access to Operational Insights, or use another server as your gateway.

**Error message:** Internet connection could not be established. Please check your proxy settings.

**Possible causes:**
- This server is not connected to the Internet. Check the Internet connectivity status, and connect the server to the Internet.
- The proxy setting is not correct. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) for information about how to set or change your proxy settings.
- The proxy server requires authentication. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) to learn about how to configure Operations Manager to use a proxy server.


## Troubleshoot SQL Server discovery

If you are running Microsoft SQL Server 2008 R2, and despite deploying the Operations Manager agent, you do not see alerts for this server, you might have a discovery issue.

To confirm if this is the source of your trouble, check for the following two issues:

- In the Operations Manager event log, you see Event ID 4001. This event indicates that there is an invalid class.

- In SQL Server Configuration Manager, when you view SQL Server Services, you see the error message, “The remote procedure call failed. [0x0800706be]”

If both issues are true, you need to install SQL Server 2008 R2 Service Pack 2. To download this service pack, see [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) in the Microsoft Download Center.

After you install the service pack, you should see Operational Insights data for the server within 24 hours.

## Troubleshoot agents or Operations Manager data flow to Operational Insights

The following set of procedures is meant as a guide to help you troubleshoot your directly-connected agents or Operations Manager deployments configured to report data to Azure Operational Insights.

### Procedure 1: Validate if the right Management Packs get downloaded to your Operations Manager Environment
>[AZURE.NOTE] If you only use Direct Agent, you can skip to the next procedure.

Depending on which solutions (previously called intelligence packs) you have enabled from the OpInsights Portal will you see more or less of these MPs. Search for keyword ‘Advisor’ or ‘Intelligence’ in their name.
You can check for these MPs using OpsMgr PowerShell:

```Powershell
Get-SCOMManagementPack | where {$\_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version Get-SCOMManagementPack | where {$\_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version ```

>[AZURE.NOTE]Wenn Sie die Problembehandlung für die Capacity-Lösung durchführen, prüfen Sie, *wie viele* Management Packs "Capacity" im Namen enthalten: Es gibt zwei Management Packs mit demselben Anzeigenamen (jedoch unterschiedlichen internen IDs), die in demselben Management Pack-Paket enthalten sind. Wenn eines der beiden nicht importiert wird (häufig aufgrund fehlender VMM-Abhängigkeiten), wird auch das andere Management Pack nicht importiert, und der Vorgang wird nicht wiederholt.

Die folgenden drei Management Packs mit „Capacity“ im Namen sollten angezeigt werden: - Microsoft System Center Advisor Capacity Intelligence Pack - Microsoft System Center Advisor Capacity Intelligence Pack - Microsoft System Center Advisor Capacity Storage Data

Wenn nur ein oder zwei davon, jedoch nicht alle drei, aufgeführt werden, entfernen Sie diese, und warten Sie 5 bis 10 Minuten, bis Operations Manager sie erneut herunterlädt und importiert – überprüfen Sie die Ereignisprotokolle auf Fehler während dieses Zeitraums.

### 2\. Verfahren: Überprüfen, ob die richtigen Lösungen in Ihren Direct Agent heruntergeladen werden
>[AZURE.NOTE]Wenn Sie nur Operations Manager verwenden, können Sie dieses Verfahren ignorieren.

Im Direct Agent sollte die Zwischenspeicherung der Richtlinie für die Lösungsdatensammlung unter **C:\\Programme\\Microsoft Monitoring Agent\\Agent\\Health Service State\\Management Packs** angezeigt werden.


### 3\. Verfahren: Überprüfen, ob Daten an den Advisor-Dienst gesendet werden (oder ob dies zumindest versucht wird)
Je nachdem, ob Sie direkt verbundene Agents oder Operations Manager verwenden, können Sie das folgende Verfahren auf dem Direct Agent-Computer oder auf dem Operations Manager-Verwaltungsserver ausführen:

1. - Öffnen Sie den Systemmonitor.
2. - Wählen Sie „Verwaltungsgruppen des Integritätsdiensts“.
3. - Fügen Sie alle Leistungsindikatoren hinzu, die mit „HTTP“ beginnen.

Bei einer korrekten Konfiguration sollte eine Aktivität für diese Leistungsindikatoren angezeigt werden, während Ereignisse und andere Datenelemente (basierend auf den im Portal eingebundenen Lösungen und den konfigurierten Richtlinien zur Protokollsammlung) hochgeladen werden. Diese Leistungsindikatoren müssen nicht unbedingt dauerhaft beschäftigt sein. Wenn Sie wenig oder keine Aktivität feststellen, haben Sie möglicherweise nicht viele Lösungen eingebunden oder verwenden nur eine sehr einfache Sammlungsrichtlinie.

### 4\. Verfahren: Überprüfen der Ereignisprotokolle des Verwaltungsservers oder von Direct Agent auf Fehler
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

- Beim ERSTEN Mal, wenn ein MMA Direct Agent oder ein Verwaltungsserver Daten an den Dienst zu senden versucht, erhalten Sie einen Fehler 500 mit einem internen Fehlercode 404. 404 bedeutet „Nicht gefunden“ und weist darauf hin, dass der Speicherbereich, der für diesen neuen Arbeitsbereich verwendet werden soll, noch nicht bereit ist – er wird derzeit noch bereitgestellt. Bei der nächsten Wiederholung wird dieser jedoch bereit sein, und der Datenfluss wird (unter normalen Bedingungen) funktionieren.
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
Der Dienst hat den HTTP-Statuscode 403 als Antwort auf eine Abfrage zurückgegeben. Überprüfen Sie mit dem Dienstadministrator die Integrität des Diensts. Die Abfrage wird später wiederholt. Sie können einen 403-Fehler während der ersten Registrierungsphase des Agent erhalten. Sie sehen dann eine URL wie "https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest". Fehlercode 403 bedeutet "Unzulässig". Der Grund ist meist ein fehlerhafter Kopiervorgang einer WorkspaceId oder eines Schlüssels, oder die Uhr ist nicht mit dem Computer synchronisiert. Versuchen Sie die Synchronisierung mit einer zuverlässigen Zeitquelle, und stellen Sie mithilfe der Konnektivitätsprüfung im Systemsteuerungs-Applet für Microsoft Monitoring Agent sicher, dass Sie die richtige Arbeitsbereichs-ID und den richtigen Schlüssel verwenden.


### 5\. Verfahren: Suchen nach den Agents, um Daten zu senden und im Portal zu indizieren
Melden Sie sich im OpInsights-Portal an, und navigieren Sie von der Seite "Übersicht" zur kleinen Kachel **Server und Verwendung**. Es wird angezeigt, ob Verwaltungsgruppen (und deren Agents) und Direct Agents Daten an die Protokollsuche melden. Die Anzahl der Agents in der Kachel leitet sich aus den Daten ab. Wenn Computer zwei Wochen lang keine Daten melden, werden sie aus dem Netz entfernt.

Über Drilldowns gelangen Sie zur Protokollsuche. Die Zeitstempel der letzten indizierten Daten werden für jeden Computer angezeigt. Von dort aus können Sie untersuchen, um welche Daten es sich handelt. Je nach Umfang der konfigurierten Datensammlung und der Art der Lösungen können der Zeitplan und die Geschwindigkeit des Datenuploads variieren.

Diese Seite bietet zudem Messungsinformationen (hierbei wird nicht der Index der Protokollsuche, sondern das Abrechnungssystem verwendet, und die Daten werden alle paar Stunden aktualisiert) über die an den Dienst gesendeten Datenmengen, aufgeschlüsselt nach Lösung.

<!---HONumber=Sept15_HO3-->