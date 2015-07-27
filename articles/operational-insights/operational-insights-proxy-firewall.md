<properties
   pageTitle="Konfigurieren von Proxy- und Firewalleinstellungen für Operational Insights"
   description="Erfahren Sie mehr über die Proxy- und Firewalleinstellungen, die Sie für Agents für die Verwendung mit Operational Insights konfigurieren müssen"
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

# Konfigurieren von Proxy- und Firewalleinstellungen für Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Die erforderlichen Aktionen zum Konfigurieren von Proxy- und Firewalleinstellungen für Operational Insights unterscheiden sich bei Verwendung von Operations Manager und den zugehörigen Agents im Vergleich zu Microsoft Monitoring Agent-Instanzen, die direkte Verbindungen mit Servern herstellen. Lesen Sie die folgenden Abschnitte für den verwendeten Agenttyp.

## Konfigurieren von Proxy- und Firewalleinstellungen mit dem Microsoft Monitoring Agent

Damit der Microsoft Monitoring Agent eine Verbindung herstellen und sich beim Operational Insights-Dienst registrieren kann, benötigt er Zugriff auf die Portnummer Ihrer Domänen und die URLs. Wenn Sie einen Proxyserver für die Kommunikation zwischen dem Agent und dem Operational Insights-Dienst verwenden, müssen Sie sicherstellen, dass auf die entsprechenden Ressourcen zugegriffen werden kann. Wenn Sie eine Firewall verwenden, um den Zugriff auf das Internet einzuschränken, müssen Sie die Firewall so konfigurieren, dass Operational Insights Zugriff erhält. In den folgenden Tabellen werden die von Operational Insights benötigten Ports aufgelistet.

|**Agent-Ressource**|**Ports**|
|--------------|-----|
|*.ods.opinsights.azure.com|Port 443| |*.oms.opinsights.azure.com|Port 443|
|ods.systemcenteradvisor.com|Port 443|
|*.blob.core.windows.net/*|Port 443|

Mit dem folgenden Verfahren können Sie Proxyeinstellungen für den Microsoft Monitoring Agent über die Systemsteuerung konfigurieren. Sie müssen diese Schritte für jeden Server ausführen. Wenn Sie viele Server konfigurieren müssen, ist es möglicherweise einfacher, diesen Prozess mithilfe eines Skripts zu automatisieren. Wenn dies der Fall ist, finden Sie entsprechende Anweisungen unter *So konfigurieren Sie Proxyeinstellungen für den Microsoft Monitoring Agent mithilfe eines Skripts*.

### So konfigurieren Sie Proxyeinstellungen für den Microsoft Monitoring Agent über die Systemsteuerung

1. Öffnen Sie die **Systemsteuerung**.

2. Öffnen Sie **Microsoft Monitoring Agent**.

3. Klicken Sie auf die Registerkarte **Proxyeinstellungen**. ![Registerkarte "Proxyeinstellungen"](./media/operational-insights-proxy-firewall/direct-agent-proxy.png)

4. Wählen Sie **Use a proxy server** aus, und geben Sie die URL und gegebenenfalls die Portnummer ein (siehe dazu das Beispiel oben). Wenn der Proxyserver eine Authentifizierung erfordert, geben Sie den Benutzernamen und das Kennwort für den Zugriff auf den Proxyserver ein.

Gehen Sie wie folgt vor, um ein PowerShell-Skript zu erstellen, mit dem Sie die Proxyeinstellungen für die einzelnen Agents festlegen können, die direkte Verbindungen mit dem Server herstellen.

### So konfigurieren Sie Proxyeinstellungen für den Microsoft Monitoring Agent mithilfe eines Skripts


- Kopieren Sie das folgende Beispiel, aktualisieren Sie es mit den tatsächlichen Informationen für Ihre Umgebung, speichern Sie es mit der Dateinamenerweiterung PS1, und führen Sie das Skript dann auf jedem Computer aus, der eine direkte Verbindung mit dem Operational Insights-Dienst herstellt.


```
param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

# First we get the health service configuration object.  We need to determine if we
# have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}


Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username;


Write-Output "Setting Proxy to ${ProxyDomainName} with proxy username of (${ProxyUserName})."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

## Konfigurieren von Proxy- und Firewalleinstellungen mit Operations Manager

Damit eine Operations Manager-Verwaltungsgruppe eine Verbindung herstellen und sich beim Operational Insights-Dienst registrieren kann, benötigt sie Zugriff auf die Portnummer Ihrer Domänen und die URLs. Wenn Sie einen Proxyserver für die Kommunikation zwischen dem Operations Manager-Verwaltungsserver und dem Operational Insights-Dienst verwenden, müssen Sie sicherstellen, dass auf die entsprechenden Ressourcen zugegriffen werden kann. Wenn Sie eine Firewall verwenden, um den Zugriff auf das Internet einzuschränken, müssen Sie die Firewall so konfigurieren, dass Operational Insights Zugriff erhält. In der folgenden Tabelle sind die Ports im Zusammenhang mit diesen Aufgaben aufgelistet.

>[AZURE.NOTE]In einigen der folgenden Ressourcen wird Advisor erwähnt. Die aufgelisteten Ressourcen werden jedoch in Zukunft geändert.

|**Verwaltungsserver-Ressource**|**Ports**|
|--------------|-----|
|*.ods.opinsights.azure.com|Port 443| |service.systemcenteradvisor.com|Port 443| |scadvisor.accesscontrol.windows.net|Port 443| |scadvisorservice.accesscontrol.windows.net|Port 443| |*.blob.core.windows.net/*|Port 443|
|data.systemcenteradvisor.com|Port 443|
|ods.systemcenteradvisor.com|Port 443|
|*.systemcenteradvisor.com|Port 443|


|**Operational Insights- und Operations Manager-Konsolenressource**|**Ports**|
|---|---|
|*.systemcenteradvisor.com|Port 80 und 443| |*.live.com|Ports 80 und 443|
|*.microsoftonline.com|Port 80 und 443| |login.windows.net|Port 80 und 443|


Verwenden Sie die folgenden Verfahren, um die Operations Manager-Verwaltungsgruppe beim Operational Insights-Dienst zu registrieren. Wenn Sie Probleme mit der Kommunikation zwischen der Verwaltungsgruppe und dem Operational Insights-Dienst haben, verwenden Sie die Validierungsverfahren für eine Problembehandlung der Datenübertragung an den Operational Insights-Dienst.

### So fordern Sie Ausnahmen für die Operational Insights-Dienstendpunkte an

1. Verwenden Sie die Informationen aus der ersten Tabelle weiter oben, um sicherzustellen, dass die erforderlichen Ressourcen für den Operations Manager-Verwaltungsserver über eventuell vorhandene Firewalls zugänglich sind.

2. Verwenden Sie die Informationen aus der zweiten Tabelle weiter oben, um sicherzustellen, dass die erforderlichen Ressourcen für die Operations-Konsole in Operations Manager und Operational Insights über eventuell vorhandene Firewalls zugänglich sind.

3. Wenn Sie einen Proxyserver mit Internet Explorer verwenden, stellen Sie sicher, dass dieser konfiguriert ist und ordnungsgemäß funktioniert. Um dies zu überprüfen, können Sie eine sichere Verbindung (https) öffnen, z. B. [https://bing.com](https://bing.com). Wenn die sichere Internetverbindung in einem Browser nicht funktioniert, funktioniert sie wahrscheinlich in der Operations Manager-Verwaltungskonsole mit Webdiensten in der Cloud ebenfalls nicht.

### So konfigurieren Sie den Proxyserver in der Operations Manager-Konsole

1. Öffnen Sie die Operations Manager-Konsole, und wählen Sie den Arbeitsbereich **Administration** aus.

2. Erweitern Sie **Operational Insights**, und wählen Sie dann **Operational Insights Connection** aus. ![Operations Manager, Operational Insights Connection](./media/operational-insights-proxy-firewall/proxy-om01.png)

3. Klicken Sie in der Ansicht "Operational Insights Connection" auf **Proxyserver konfigurieren**. ![Operations Manager, Operational Insights Connection, Proxyserver konfigurieren](./media/operational-insights-proxy-firewall/proxy-om02.png)

4. Wählen Sie unter "Operational Insights Settings Wizard: Proxy Server" die Option **Use a proxy server to access the Operational Insights Web Service** aus, und geben Sie dann die URL mit der Portnummer ein, z. B. **http://myproxy:80**. ![Operations Manager, Proxyadresse Operational Insights](./media/operational-insights-proxy-firewall/proxy-om03.png)


### So geben Sie Anmeldeinformationen ein, wenn der Proxyserver eine Authentifizierung erfordert

1. Öffnen Sie die Operations Manager-Konsole, und wählen Sie den Arbeitsbereich **Administration** aus.

2. Wählen Sie unter **RunAs Configuration** die Option **Profile** aus.

3. Öffnen Sie das Profil **System Center Advisor Run As Profile Proxy**. ![Bild des Profils "System Center Advisor Run As Profile Proxy"](./media/operational-insights-proxy-firewall/proxyacct1.png)
4. Klicken Sie im Assistenten für das ausführende Profil auf **Hinzufügen**, um ein ausführendes Konto zu verwenden. Sie können ein neues ausführendes Konto erstellen oder ein vorhandenes Konto verwenden. Dieses Konto muss über ausreichende Berechtigungen für die Weiterleitung über den Proxyserver verfügen. ![Bild des Assistenten für das ausführende Profil](./media/operational-insights-proxy-firewall/proxyacct2.png)

5. Um das zu verwaltende Konto festzulegen, wählen Sie **Einer bestimmten Klasse oder Gruppe bzw. eines bestimmten Objekts** aus, um das Dialogfeld "Objektsuche" zu öffnen. ![Bild des Assistenten für das ausführende Profil](./media/operational-insights-proxy-firewall/proxyacct2-1.png)
6. Suchen Sie **Operations Manager Management Servers**, und wählen Sie diese Option dann aus. ![Bild des Dialogfelds "Objektsuche"](./media/operational-insights-proxy-firewall/proxyacct3.png)
7. Klicken Sie auf **OK**, um das Dialogfeld "Ausführendes Konto hinzufügen" zu schließen. ![Bild des Assistenten für das ausführende Profil](./media/operational-insights-proxy-firewall/proxyacct4.png)
8. Schließen Sie den Assistenten ab, und speichern Sie die Änderungen. ![Bild des Assistenten für das ausführende Profil](./media/operational-insights-proxy-firewall/proxyacct5.png)


### So konfigurieren Sie den Proxyserver auf jedem Verwaltungsserver für WinHTTP

1. Wenn Operations Manager nicht mit Operations Manager 2012 R2-Updaterollup 3 oder Operations Manager 2012 SP1-Updaterollup 7 oder höher aktualisiert wurde, öffnen Sie auf dem Operations Manager-Verwaltungsserver ein Eingabeaufforderungsfenster als Administrator. Andernfalls müssen Sie dieses Verfahren nicht anwenden.

2. Geben Sie **netsh winhttp set proxy myproxy:80** ein.

3. Schließen Sie das Eingabeaufforderungsfenster, und starten Sie den System Center Management-Dienst (HealthService).

4. Führen Sie Schritt 2 für jeden Verwaltungsserver in der Verwaltungsgruppe aus.

### So konfigurieren Sie den Proxyserver auf jedem Verwaltungsserver

1. Öffnen Sie die Operations Manager-Konsole, und wählen Sie den Arbeitsbereich **Administration** aus.

2. Klicken Sie auf **Geräteverwaltung** und dann auf **Verwaltungsserver**.

3. Klicken Sie mit der rechten Maustaste auf die Namen der einzelnen Verwaltungsserver, klicken Sie auf **Eigenschaften**, und legen Sie dann die Informationen auf der Registerkarte **Proxyeinstellungen** fest. ![Registerkarte "Proxyeinstellungen"](./media/operational-insights-proxy-firewall/proxyms.png)

### So überprüfen Sie, ob Operational Insights-Management Packs heruntergeladen werden

- Wenn Sie Lösungen mit Operational Insights hinzugefügt haben, können Sie sie in der Operations Manager-Konsole unter **Administration** als Management Packs anzeigen. Suchen Sie nach *System Center Advisor*, um sie schnell zu finden. ![Management Packs heruntergeladen](./media/operational-insights-proxy-firewall/mpdownloaded.png)
- Sie können auch mithilfe der folgenden Windows PowerShell-Befehle auf dem Operations Manager-Verwaltungsserver nach Operational Insights-Management Packs suchen:

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version | ft

### So überprüfen Sie, ob Operations Manager Daten an den Operational Insights-Dienst sendet

1. Öffnen Sie auf dem Operations Manager-Verwaltungsserver den Systemmonitor ("perfmon.exe"), und wählen Sie **Systemmonitor** aus.

2. Klicken Sie auf **Hinzufügen**, und wählen Sie **Verwaltungsgruppen des Integritätsdiensts** aus.

3. Fügen Sie alle Leistungsindikatoren hinzu, die mit **HTTP** beginnen. ![Hinzufügen der Leistungsindikatoren](./media/operational-insights-proxy-firewall/sendingdata1.png)
4. Wenn die Operations Manager-Konfiguration in Ordnung ist, werden Aktivitäten für die Leistungsindikatoren der Verwaltungsgruppen des Integritätsdiensts für Ereignisse und andere Datenelemente angezeigt. Die Grundlage bilden die in Operational Insights hinzugefügten Management Packs und die konfigurierten Richtlinien für die Protokollerfassung. ![Systemmonitor mit Aktivität](./media/operational-insights-proxy-firewall/sendingdata2.png)

<!---HONumber=July15_HO3-->