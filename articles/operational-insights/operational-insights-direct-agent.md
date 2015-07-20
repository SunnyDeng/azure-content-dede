<properties
	pageTitle="Direktes Verbinden von Computern mit Operational Insights"
	description="Sie können Computer direkt mit Operational Insights verbinden, indem Sie den Operational Insights-Agent auf jedem Computer installieren, den Sie hinzufügen möchten."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015" 
	ms.author="banders"/>
# Direktes Verbinden von Computern mit Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Sie können Computer direkt mit Operational Insights verbinden, indem Sie den Operational Insights-Agent auf jedem Computer installieren, den Sie hinzufügen möchten.

> [AZURE.TIP]Für virtuelle Azure-Maschinen installieren Sie den Agent mithilfe der Schritte in [Analysieren von Daten von Servern in Microsoft Azure](operational-insights-analyze-data-azure.md)

## Herunterladen und Installieren des Agents
Verwenden Sie die folgenden Verfahren zum Herunterladen und Installieren des Operational Insights-Agents.

### So laden Sie die Setup-Datei für den Agent herunter
1. Klicken Sie im Operational Insights-Portal auf der Seite **Übersicht** auf die Kachel **Einstellungen**. Klicken Sie oben auf die Registerkarte **Verbundene Datenquellen**. ![Seite "Einstellungen"](./media/operational-insights-direct-agent/direct-agent01.png)
2. Klicken Sie unter **Server direkt anfügen (64-Bit)** auf die Schaltfläche "Agent herunterladen", um die Installationsdatei herunterzuladen.
3. Klicken Sie am rechten Rand der **Arbeitsbereichs-ID** auf das Symbol "kopieren", und fügen Sie die ID in den Editor ein.
4. Klicken Sie am rechten Rand des **Primärschlüssels** auf das Symbol "kopieren", und fügen Sie die ID in den Editor ein. ![Seite "Einstellungen"](./media/operational-insights-direct-agent/direct-agent02.png)

### So installieren Sie den Agent über Setup
1. Führen Sie Setup aus, um den Agent auf einem Computer zu installieren, den Sie verwalten möchten.
2. Wählen Sie **Agent mit Microsoft Azure Operational Insights verbinden**, und klicken Sie dann auf **Weiter**.
3. Wenn Sie dazu aufgefordert werden, geben Sie die **Arbeitsbereichs-ID** und den **Primärschlüssel** ein, die Sie im vorherigen Schritt in den Editor kopiert haben.

4. Klicken Sie auf **Weiter**. Der Agent stellt sicher, dass er eine Verbindung zu Operational Insights herstellen kann.
5. Zum Abschluss wird der **Microsoft Verwaltungs-Agent** in der **Systemsteuerung** angezeigt.

### So installieren Sie den Agent über die Befehlszeile
- Ändern Sie das folgende Beispiel, und verwenden Sie es dann zur Installation des Agents über die Befehlszeile. ```MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"```

## Konfigurieren von Microsoft Monitoring Agent (optional)
Verwenden Sie die folgenden Informationen, um einen Agent für die direkte Kommunikation mit dem Microsoft Azure Operational Insights-Dienst zu aktivieren. Nachdem Sie den Agent konfiguriert haben, registriert dieser sich beim Agent-Dienst und ruft die erforderlichen Konfigurationsinformationen und Management Packs ab, die die Lösung enthalten.

Nachdem die Daten von Computern erfasst wurden, die vom Agent überwacht werden, erscheint die Anzahl der überwachten Computer im Operational Insights-Portal in der Registerkarte **Verbundene Datenquellen** in den **Einstellungen** unter **Server direkt anfügen (64-Bit)**. Für alle Computer, die Daten senden, können Sie die Daten und Bewertungsinformationen im Operational Insights-Portal einsehen.

Der Agent kann bei Bedarf auch deaktiviert oder über die Befehlszeile oder ein Skript aktiviert werden.

### So konfigurieren Sie den Agent
1. Nach der Installation von **Microsoft Monitoring Agent** öffnen Sie die **Systemsteuerung**.
2. Öffnen Sie Microsoft Monitoring Agent, und klicken Sie dann auf die Registerkarte Azure Operational Insights.
3. Wählen Sie **Verbinden mit Azure Operational Insights**.
4. Fügen Sie in das Feld **Arbeitsbereichs-ID** die Arbeitsbereichs-ID aus dem Operational Insights-Portal ein.
5. Fügen Sie im Eingabefeld **Kontoschlüssel** den **Primärschlüssel** aus dem Operational Insights-Portal ein und klicken Sie dann auf **OK**.

### So deaktivieren Sie einen Agent
1. Öffnen Sie nach der Installation des Agents die **Systemsteuerung**.
2. Öffnen Sie Microsoft Monitoring Agent, und klicken Sie dann auf die Registerkarte **Azure Operational Insights**.
3. Deaktivieren Sie **Verbinden mit Azure Operational Insights**.

### So aktivieren Sie den Agent über die Befehlszeile oder ein Skript
- Sie können entweder Windows PowerShell oder ein VB-Skript mit dem folgenden Beispiel verwenden.

```powershell
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
$healthServiceSettings.ReloadConfiguration()
```

## Konfigurieren von Proxy- und Firewall-Einstellungen (optional)
Wenn Sie in Ihrer Umgebung Proxyserver oder Firewalls verwenden, die den Zugriff auf das Internet einschränken, müssen Sie möglicherweise die folgenden Verfahren befolgen, um Operations Manager oder Agents für die Kommunikation mit dem Operational Insights-Dienst zu aktivieren.

- [Konfigurieren von Proxy- und Firewall-Einstellungen (optional)](operational-insights-proxy-firewall.md)

<!---HONumber=July15_HO2-->