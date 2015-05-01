<properties 
   pageTitle="connect-scom" 
   description="Direktes Verbinden von Computern mit Operational Insights" 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Direktes Verbinden von Computern mit Operational Insights 

Sie können Computer direkt mit Operational Insights verbinden, indem Sie den Operational Insights-Agent auf jedem Computer installieren, den Sie hinzufügen möchten. 


##Herunterladen, Installieren und Konfigurieren des Agents
###So laden Sie die Setupdatei für den Agent herunter
1. Klicken Sie im **Operational Insights-Portal** auf der Seite **Übersicht** auf **Server und Verwendung**.
1. Klicken Sie unter **Direkt angeschlossene Server** auf **Konfigurieren**.
1. Klicken Sie neben **Agents hinzufügen** auf den Agent-Link, um die Setupdatei herunterzuladen.
1. Wählen Sie im Feld **Primärer Arbeitsbereichsschlüssel** den Schlüssel aus, und kopieren Sie ihn (STRG + C).


### So installieren Sie den Agent über Setup
1. Führen Sie Setup aus, um den Agent auf einem Computer zu installieren, den Sie verwalten möchten.
1. Wählen Sie **Agent mit Microsoft Azure Operational Insights verbinden**, und klicken Sie dann auf **Weiter**.
1. Geben Sie nach Aufforderung die Informationen ein, die Sie in Schritt 4 kopiert haben.
1. Zum Abschluss wird der **Microsoft Verwaltungs-Agent** in der **Systemsteuerung** angezeigt.

### So installieren Sie den Agent über die Befehlszeile
Ändern Sie das folgende Beispiel, und verwenden Sie es dann zur Installation des Agents über die Befehlszeile.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

## Neukonfigurieren von Microsoft Monitoring Agent (Optional)
Verwenden Sie die folgenden Informationen, um einen Agent für die direkte Kommunikation mit dem Microsoft Azure Operational Insights-Dienst zu aktivieren. Nachdem Sie den Agent konfiguriert haben, registriert dieser sich beim Agent-Dienst und ruft die erforderlichen Konfigurationsinformationen und Management Packs ab, die Intelligence Pack-Informationen enthalten.

Nachdem die Daten von Computern erfasst wurden, die vom Agent überwacht werden, wird die Anzahl der überwachten Computer im Operational Insights-Portal auf der Seite "Verwendung" unter **Direkt verbundene Agents** angezeigt. Für alle Computer, die Daten senden, können Sie die Daten und Bewertungsinformationen im Operational Insights-Portal einsehen.

Der Agent kann bei Bedarf auch deaktiviert oder über die Befehlszeile oder ein Skript aktiviert werden.

### So konfigurieren Sie den Agent
- Nach der Installation von **Microsoft Monitoring Agent** öffnen Sie die **Systemsteuerung**.
- Öffnen Sie Microsoft Monitoring Agent, und klicken Sie dann auf die Registerkarte "Azure Operational Insights".
- Wählen Sie **Verbinden mit Azure Operational Insights**.
- Geben Sie im Feld **Arbeitsbereichs-ID** die Arbeitsbereichs-ID ein, die im Operational Insights-Portal bereitgestellt wird.
- Fügen Sie im Feld "Kontoschlüssel" den **Primären Arbeitsbereichsschlüssel** ein, den Sie beim Installieren des Agents kopiert haben, und klicken Sie dann auf **OK**.

### So deaktivieren Sie einen Agent
- Öffnen Sie nach der Installation des Agents die **Systemsteuerung**.
- Öffnen Sie Microsoft Monitoring Agent, und klicken Sie dann auf die Registerkarte **Azure Operational Insights**.
- Deaktivieren Sie "Verbinden mit **Azure Operational Insights**".

### So aktivieren Sie den Agent über die Befehlszeile oder ein Skript
Sie können entweder Windows PowerShell oder ein VB-Skript mit dem folgenden Beispiel verwenden.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Konfigurieren von Proxy- und Firewall-Einstellungen (Optional)
Wenn Sie in Ihrer Umgebung Proxyserver oder Firewalls verwenden, die den Zugriff auf das Internet einschränken, müssen Sie möglicherweise die folgenden Verfahren befolgen, um Operations Manager und/oder Agents für die Kommunikation mit dem Operational Insights-Dienst zu aktivieren. 



- [Konfigurieren von Proxy- und Firewall-Einstellungen (Optional)](https://msdn.microsoft.com/library/azure/dn884643.aspx) 


## Verwandte Inhalte

- [Blogbeitrag: Connect servers directly to Operational Insights](in englischer Sprache)(http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Blogbeitrag: Enable Operational Insights for Azure Virtual machines](in englischer Sprache)(http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)



<!--HONumber=52-->