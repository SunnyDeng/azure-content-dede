<properties 
   pageTitle="Sammeln von Computerdaten" 
   description="Sammeln von Computerdaten von Servern in Ihrer lokalen oder Cloudinfrastruktur" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/20/2015"
   ms.author="banders"/>
#Sammeln von Computerdaten

Operational Insights verwendet Daten von Servern in Ihrer lokalen oder Cloudinfrastruktur. Sie können Computerdaten aus den folgenden Quellen erfassen:

- Operations Manager-Agents
- Computer, die direkt mit Operational Insights verbunden sind
- Diagnosedaten von virtuellen Computern in Azure Storage-Diensten

Nachdem Daten gesammelt wurden, werden sie an den Operational Insights-Dienst gesendet.

## Verbinden mit Operational Insights von System Center Operations Manager aus 

Sie können Operational Insights mit einer vorhandenen System Center Operations Manager-Umgebung verbinden. Dadurch können Sie vorhandene Operations Manager-Agents als Operational Insights-Agents verwenden. Weitere Informationen zur Verwendung von Operations Manager mit Operational Insights finden Sie unter [Überlegungen zu Operations Manager mit Operational Insights](operational-insights-operations-manager.md).

Wenn Sie Operations Manager zum Überwachen der folgenden Arbeitsauslastungen verwenden, müssen Sie dafür ausführende Operations Manager-Konten festlegen. Weitere Informationen zum Festlegen der Konten finden Sie unter [Ausführende Operations Manager-Konten für Operational Insights](operational-insights-run-as.md).

- SQL Assessment
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE] Ab Operations Manager 2012 SP1 UR6 und Operations Manager 2012 R2 UR2 steht Unterstützung für Operational Insights zur Verfügung. Proxy-Unterstützung wurde in System Center Operations Manager 2012 SP1 UR7 und System Center Operations Manager 2012 R2 UR3 hinzugefügt.

#### So verbinden Sie Operations Manager mit Operational Insights und fügen Agents hinzu

1. Klicken Sie in der Operations Manager-Konsole auf **Verwaltung**.

2. Erweitern Sie den Knoten **Operational Insights**, und klicken Sie auf **Operational Insights-Verbindung**.

3. Klicken Sie auf den Link **Registrieren bei Operational Insights**, und befolgen Sie die Anweisungen am Bildschirm. 

4. Klicken Sie nach Abschluss des Registrierungs-Assistenten auf **Computer/Gruppe hinzufügen**.

5. Im Dialogfeld **Computersuche** können Sie nach Computern oder Gruppen suchen, die von Operations Manager überwacht werden. Wählen Sie Computer oder Gruppen aus, die in Operational Insights aufgenommen werden sollen, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **OK**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Direktes Verbinden von Computern mit Operational Insights 

Sie können Computer direkt mit Operational Insights verbinden, indem Sie den Operational Insights-Agent auf jedem Computer installieren, den Sie hinzufügen möchten. 


###Herunterladen und Installieren des Agents

####So laden Sie die Setupdatei für den Agent herunter
1. Klicken Sie im **Operational Insights-Portal** auf der Seite **Übersicht** auf **Server und Verwendung**.
1. Klicken Sie unter **Direkt angeschlossene Server** auf **Konfigurieren**.
1. Klicken Sie neben **Agents hinzufügen** auf den Agent-Link, um die Setupdatei herunterzuladen.
1. Wählen Sie im Feld **Primärer Arbeitsbereichsschlüssel** den Schlüssel aus, und kopieren Sie ihn (STRG + C).


#### So installieren Sie den Agent über Setup
1. Führen Sie Setup aus, um den Agent auf einem Computer zu installieren, den Sie verwalten möchten.
1. Wählen Sie **Agent mit Microsoft Azure Operational Insights verbinden**, und klicken Sie dann auf **Weiter**.
1. Geben Sie nach Aufforderung die Informationen ein, die Sie in Schritt 4 kopiert haben.
1. Zum Abschluss wird der **Microsoft Verwaltungs-Agent** in der **Systemsteuerung** angezeigt.

#### So installieren Sie den Agent über die Befehlszeile
Ändern Sie das folgende Beispiel, und verwenden Sie es dann zur Installation des Agents über die Befehlszeile.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

### Konfigurieren von Microsoft Monitoring Agent (Optional)
Verwenden Sie die folgenden Informationen, um einen Agent für die direkte Kommunikation mit dem Microsoft Azure Operational Insights-Dienst zu aktivieren. Nachdem Sie den Agent konfiguriert haben, registriert dieser sich beim Agent-Dienst und ruft die erforderlichen Konfigurationsinformationen und Management Packs ab, die Intelligence Pack-Informationen enthalten.

Nachdem die Daten von Computern erfasst wurden, die vom Agent überwacht werden, wird die Anzahl der überwachten Computer im Operational Insights-Portal auf der Seite "Verwendung" unter **Direkt verbundene Agents** angezeigt. Für alle Computer, die Daten senden, können Sie die Daten und Bewertungsinformationen im Operational Insights-Portal einsehen.

Der Agent kann bei Bedarf auch deaktiviert oder über die Befehlszeile oder ein Skript aktiviert werden.

#### So konfigurieren Sie den Agent
- Nach der Installation von **Microsoft Monitoring Agent** öffnen Sie die **Systemsteuerung**.
- Öffnen Sie Microsoft Monitoring Agent, und klicken Sie dann auf die Registerkarte "Azure Operational Insights".
- Wählen Sie **Verbinden mit Azure Operational Insights**.
- Geben Sie im Feld **Arbeitsbereichs-ID** die Arbeitsbereichs-ID ein, die im Operational Insights-Portal bereitgestellt wird.
- Fügen Sie im Feld "Kontoschlüssel" den **Primären Arbeitsbereichsschlüssel** ein, den Sie beim Installieren des Agents kopiert haben, und klicken Sie dann auf **OK**.

#### So deaktivieren Sie einen Agent
- Öffnen Sie nach der Installation des Agents die **Systemsteuerung**.
- Öffnen Sie Microsoft Monitoring Agent, und klicken Sie dann auf die Registerkarte **Azure Operational Insights**.
- Deaktivieren Sie "Verbinden mit **Azure Operational Insights**".

#### So aktivieren Sie den Agent über die Befehlszeile oder ein Skript
Sie können entweder Windows PowerShell oder ein VB-Skript mit dem folgenden Beispiel verwenden.

    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Konfigurieren von Proxy- und Firewall-Einstellungen (Optional)
Wenn Sie in Ihrer Umgebung Proxyserver oder Firewalls verwenden, die den Zugriff auf das Internet einschränken, müssen Sie möglicherweise die folgenden Verfahren befolgen, um Operations Manager und/oder Agents für die Kommunikation mit dem Operational Insights-Dienst zu aktivieren. 



- [Konfigurieren von Proxy- und Firewall-Einstellungen (Optional)](operational-insights-proxy-filewall.md) 

## Analysieren der Daten von Servern in Microsoft Azure

Mit Operational Insights können Sie schnell Ereignis- und IIS-Protokolle für Cloud-Dienste und virtuelle Computer durchsuchen, indem Sie die [Diagnose in Azure Cloud-Diensten aktivieren](operational-insights-log-collection.md). Außerdem erhalten Sie weitere Einblicke in Ihre virtuellen Computer durch die Installation von Microsoft Monitoring Agent.

Die Intelligence Packs Update Assessment, Change Tracking und SQL Assessment beruhen alle auf den tieferen Einblicken, die Microsoft Monitoring Agent für Ihre virtuellen Computer bereitstellt. Diese Intelligence Packs können Sie aktivieren, wenn Sie im [Operational Insights-Portal angemeldet sind](https://preview.opinsights.azure.com/).

Für virtuelle Computer in Azure gibt es zwei einfache Methoden, die Agent-basierte Datensammlung zu aktivieren:

- Im Microsoft Azure-Verwaltungsportal

- Mithilfe von PowerShell

Bei Verwendung der Agent-basierten Sammlung für Protokolldaten müssen Sie auf der Konfigurationsseite der Protokollverwaltung im [Operational Insights-Portal](https://preview.opinsights.azure.com/) konfigurieren, welche Protokolle erfasst werden sollen.

Wenn Sie Operational Insights so konfiguriert haben, dass Protokolldaten mithilfe der Diagnose in Azure Cloud Services indiziert werden, und Sie den Agent für das Erfassen von Protokollen konfigurieren, werden für dieselben Ereignisprotokolle 2 Kopien der Daten indiziert. Wenn Sie den Agent installiert haben, sollten Sie die Protokolldaten mithilfe des Agents sammeln und nicht die von der Diagnose in Azure Cloud Services erfassten Protokolle indizieren.

### Microsoft Azure-Verwaltungsportal

Rufen Sie auf dem [Operational Insights-Portal](https://preview.opinsights.azure.com/), den Operational Insights-Arbeitsbereich auf, und wählen Sie die Registerkarte "Server" aus. Auf der Registerkarte sehen Sie eine Liste der virtuellen Computer. Wählen Sie den virtuellen Computer aus, auf dem Sie den Agent installieren möchten, und klicken Sie dann auf **Op Insights aktivieren**.

Der Agent wird installiert und für den Operational Insights-Arbeitsbereich konfiguriert.

![Abbildung der Seite mit Operational Insights-Servern](./media/operational-insights-collect-data/servers.png)

### PowerShell

Wenn Sie für Änderungen an Ihren virtuellen Azure-Computern lieber die Skriptfunktion verwenden, können Sie Microsoft Monitoring Agent über PowerShell aktivieren.

Microsoft Monitoring Agent ist eine [Azure Virtual Machine-Erweiterung](https://msdn.microsoft.com/library/azure/dn832621.aspx) die über PowerShell wie im unten dargestellten Beispiel verwaltet werden kann.

    Add-AzureAccount
    
    $workspaceId="enter workspace here"
    $workspaceKey="enter workspace key here"
    $hostedService="enter hosted service here"
    
    $vm = Get-AzureVM -ServiceName $hostedService
    Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

Bei der Konfiguration mit PowerShell müssen Sie die Arbeitsbereichs-ID und einen Arbeitsbereichsschlüssel bereitstellen. Sie finden Ihre Arbeitsbereichs-ID und den Arbeitsbereichsschlüssel auf der Seite "Server und Verwendung" im Operational Insights-Portal.

![Abbildung der direkten Konfiguration des Operational Insights-Agents](./media/operational-insights-collect-data/direct-agent-cfg.png)

### Sammeln von Daten mithilfe der Diagnose in Azure Cloud Services

Operational Insights kann Daten analysieren, die von der Diagnose für Azure Cloud Services in Azure Storage-Dienste geschrieben werden. Es sind zwei grundlegende Schritte auszuführen:

- Konfigurieren der Sammlung von Diagnosedaten in Azure Storage
- Konfigurieren von Operational Insights für die Analyse der Daten im Speicherkonto

Die unten stehenden Themen beschreiben, wie Sie die Sammlung von Diagnosedaten in Azure Storage aktivieren und anschließend Operational Insights zur Analyse der Daten in Azure Storage konfigurieren.

Die Azure-Diagnose ist eine Azure-Erweiterung, mit der Sie Telemetriedaten zur Diagnose von einer Worker- oder Webrolle oder einem virtuellen Computer erfassen können, die bzw. der in Azure ausgeführt wird. Die Telemetriedaten werden in einem Azure-Speicherkonto gespeichert und können dann von Operational Insights verwendet werden.

>[AZURE.NOTE] Ihnen werden normale Datenraten für Speicherung und Transaktionen in Rechnung gestellt, wenn Sie Diagnoseinformationen an ein Speicherkonto senden.

Mit der Azure-Diagnose können die folgenden Arten von Telemetriedaten erfasst werden:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Datenquelle</b></td>
		<td><b>

Beschreibung </b></td>
    </tr>
    <tr align="left" valign="top">
		<td>IIS-Protokolle</td>
		<td>Informationen zu IIS-Websites</td>
    </tr>
    <tr align="left" valign="top">
		<td>Infrastrukturprotokolle der Azure-Diagnose</td>
		<td>Informationen zur Diagnose selbst</td>
    </tr>
	<tr align="left" valign="top">
		<td>Protokolle zu IIS-Anforderungsfehlern </td>
		<td>Informationen zu fehlgeschlagenen IIS-Website- oder IIS-Anwendungsanforderungen</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Windows-Ereignisprotokolle</td>
		<td>An das Windows-System für die Ereignisprotokollierung gesendete Informationen</td>
    </tr>
    <tr align="left" valign="top">
		<td>Leistungsindikatoren</td>
		<td>Leistungsindikatoren des Betriebssystems und benutzerdefinierte Leistungsindikatoren</td>
    </tr>
    <tr align="left" valign="top">
		<td>Absturzabbilder</td>
		<td>Informationen zum Status des Prozesses im Fall eines Anwendungsabsturzes</td>
    </tr>
    <tr align="left" valign="top">
		<td>Benutzerdefinierte Fehlerprotokolle</td>
		<td>Von Ihrer Anwendung oder Ihrem Dienst erstellte Protokolle</td>
    </tr>
    <tr align="left" valign="top">
		<td>NET-EventSource</td>
		<td>Von Ihrem Code mit der .NET<a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">-EventSource-Klasse</a> generierte Ereignisse</td>
    </tr>
    <tr align="left" valign="top">
		<td>Manifestbasiertes ETW</td>
		<td>Von einem beliebigen Prozess generierte ETW-Ereignisse</td>
    </tr>
    </tbody>
    </table>


Derzeit kann Operational Insights IIS-Protokolle von Webrollen und Windows-Ereignisprotokolle von Web- und Workerrollen sowie virtuellen Azure-Computern analysieren. Die Protokolle müssen in folgenden Speicherorten enthalten sein:

- WADWindowsEventLogsTable (Tabellenspeicherung)- Enthält Informationen aus Windows-Ereignisprotokollen.

- wad-iis-logfiles (Blob-Speicher) - Enthält Informationen zu IIS-Protokollen.

Für virtuelle Maschinen haben Sie die Möglichkeit, [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) auf Ihrem virtuellen Computer zu installieren. Auf diese Weise können Sie IIS-Protokolle und Ereignisprotokolle analysieren und außerdem zusätzliche Analysen durchführen, einschließlich der Nachverfolgung von Konfigurationsänderungen und der Bewertung von Updates. Ermöglichen Sie weitere Einblicke durch die Installation von [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269).

>[AZURE.NOTE] IIS-Protokolle von Azure-Websites werden derzeit nicht unterstützt.

Helfen Sie uns, die Prioritäten für weitere zu analysierende Protokolle für Operational Insights zu verteilen - durch Ihre Stimme auf unserer [Feedbackseite](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

### Aktivieren der Azure-Diagnose in einer Webrolle für die Sammlung von IIS-Protokollen und -Ereignissen

Informationen hierzu finden Sie unter [Aktivieren der Diagnose in einem Cloud-Dienst](https://msdn.microsoft.com/library/azure/dn482131.aspx). Sie verwenden die grundlegenden Informationen von dort und passen hier die Schritte für die Verwendung mit Microsoft Azure Operational  Insights an.

Bei aktivierter Azure-Diagnose:

- IIS-Protokolle werden standardmäßig gespeichert, und Protokolldaten werden im Übertragungsintervall ScheduledTransferPeriod übertragen.

- Windows-Ereignisprotokolle werden standardmäßig nicht übertragen.

#### So aktivieren Sie die Diagnose

Zum Aktivieren der Windows-Ereignisprotokolle oder zum Ändern von "ScheduledTransferPeriod" konfigurieren Sie die Azure-Diagnose mithilfe der XML-Konfigurationsdatei (diagnostics.wadcfg), wie in [Schritt2: Hinzufügen der Datei "diagnostics.wadcfg" zu Ihrer Visual Studio-Lösung](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) und [Schritt 3: Konfigurieren der Diagnose für Ihre Anwendung](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) im Thema "Aktivieren der Diagnose in einem Cloud-Dienst" gezeigt wird. Die folgende Beispielkonfigurationsdatei sammelt IIS-Protokolle und alle Ereignisse aus dem Anwendungs- und dem Systemprotokoll:

    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">
     
      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>
     
      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>
     
    </DiagnosticMonitorConfiguration>


Stellen Sie in [Schritt 4: Konfigurieren der Speicherung von Diagnosedaten](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) im Thema "Aktivieren der Dignose in einem Cloud-Dienst" sicher, dass "ConfigurationSettings" ein Speicherkonto angibt, wie im folgenden Beispiel gezeigt wird:


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


Die Werte für **AccountName** und **AccountKey** finden Sie im Microsoft Azure-Verwaltungsportal im Speicherkonto-Dashboard unter "Zugriffsschlüssel verwalten". Als Protokoll für die Verbindungszeichenfolge muss **https** verwendet werden.

Sobald die aktualisierte Diagnosekonfiguration auf den Cloud-Dienst angewendet wurde und Diagnoseinformationen in Azure Storage geschrieben werden, sind Sie zur Konfiguration von Operational Insights bereit.

### Aktivieren der Diagnose von Azure Cloud Services auf einem virtuellen Computer zur Erfassung von Ereignis- und IIS-Protokollen

Gehen Sie folgendermaßen vor, um die Azure Cloud Services-Diagnose mithilfe des Microsoft Azure-Verwaltungsportals auf einem virtuellen Computer für die Erfassung von Ereignis- und IIS-Protokollen zu aktivieren.

#### So aktivieren Sie die Azure Cloud Services-Diagnose auf einem virtuellen Computer mit dem Azure-Verwaltungsportal

1. Installieren Sie den VM-Agent, wenn Sie einen virtuellen Computer erstellen. Ist der virtuelle Computer bereits vorhanden, stellen Sie sicher, dass der VM-Agent bereits installiert ist.
	- Wenn Sie das standardmäßige Azure-Verwaltungsportal zum Erstellen des virtuellen Computers verwenden, führen Sie **Benutzerdefiniert erstellen** durch, und wählen Sie **VM-Agent installieren**.
	- Wenn Sie das neue Azure-Verwaltungsportal zum Erstellen eines virtuellen Computers verwenden, wählen Sie **Optionale Konfiguration** und dann **Diagnose**, und setzen Sie **Status** auf **Ein**.
	
	Nach Abschluss des Vorgangs wird auf dem virtuellen Computer automatisch die Azure-Diagnoseerweiterung installiert und ausgeführt. Diese ist für das Sammeln von Diagnosedaten zuständig.

2. Aktivieren Sie die Überwachung, und konfigurieren Sie die Ereignisprotokollierung für einen vorhandenen virtuellen Computer. Sie können die Diagnose auf VM-Ebene aktivieren. Zum Aktivieren der Diagnose und zur anschließenden Konfiguration der Ereignisprotokollierung führen Sie die folgenden Schritte aus:
	1. Wählen Sie den virtuellen Computer aus.
	2. Klicken Sie auf **Überwachen**.
	3. Klicken Sie auf **Diagnose**.
	4. Legen Sie den **Status** auf **Ein** fest.
	5. Wählen Sie die einzelnen Diagnosemetriken aus, die Sie verwenden möchten. Operational Insights kann Windows-Ereignissystemprotokolle, Windows-Ereignisanwendungsprotokolle und IIS-Protokolle analysieren.
	7. Klicken Sie auf **OK**.

Mit Azure PowerShell können Sie die Ereignisse genauer angeben, die in Azure Storage geschrieben werden. Eine Beispielkonfigurationsdatei und eine detaillierte Dokumentation des Schemas finden Sie im Konfigurationsschema von Azure-Diagnose 1.2. Stellen Sie sicher, dass Sie Azure PowerShell, Version 0.8.7 oder höher, von folgendem Link herunterladen: [Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell/). Wenn eine Version von Microsoft Azure-Diagnose installiert ist, die älter ist als Version 1.2, können Sie das neue Portal nicht zum Aktivieren oder Konfigurieren der Diagnose verwenden.

Sie können den Agent mit dem folgenden PowerShell-Skript aktivieren und aktualisieren. Dieses Skript können Sie auch mit benutzerdefinierter Protokollierungskonfiguration verwenden. Sie müssen das Skript ändern, um das Speicherkonto, den Dienstnamen und den Namen des virtuellen Computers festzulegen.

#### So aktivieren Sie die Diagnose auf einem virtuellen Computer mit Azure PowerShell

Überprüfen Sie das folgende Beispielskript, kopieren Sie es, ändern Sie es nach Bedarf, speichern Sie das Beispiel als PowerShell-Skriptdatei, und führen Sie das Skript aus.

    # Herstellen einer Verbindung mit Azure
    Add-AzureAccount 
     
    # Zu ändernde Einstellungen:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"
     
    # Erstellen einer öffentlichen Azure-Diagnosekonfiguration und Konvertieren in das Konfigurationsformat 
    
    # Sammeln von Systemfehlerereignissen (ausschließlich):
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"
    
    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)
    
    #Erstellen einer privaten Azure-Diagnosekonfiguration
    
    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)
    
    #Aktivieren der Diagnoseerweiterung für 	Virtual Machine
    
    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Ruft neueste Version der Erweiterung ab
    
    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
    

### Aktivieren der Azure Storage-Analyse durch Operational Insights

Navigieren Sie im Azure-Standardportal zu Ihrem Operational Insights-Arbeitsbereich, und wählen Sie die Registerkarte **Storage** aus. Verwenden Sie die folgenden Informationen, um Operational Insights für das Lesen aus dem Azure Storage-Konto mit der Azure-Diagnose zu konfigurieren.

#### So aktivieren Sie die Analyse über Operational Insights

1. Klicken Sie auf **Hinzufügen**, um das Feld **Speicherkonto hinzufügen** zu öffnen.

2. Wählen Sie das **Speicherkonto** aus.

3. Wählen Sie einen Datentyp aus: entweder **Ereignisse** oder **IIS-Protokolle**.

4. Klicken Sie auf **OK**.

5. Wiederholen Sie die oben genannten Schritte für jede Kombination aus Datentyp und Speicherkonto, die Sie erfassen möchten.

Nach etwa einer Stunde werden Daten aus dem Speicherkonto für die Analyse in Operational Insights verfügbar.




## Verwandte Inhalte

- [Blogbeitrag: Connect servers directly to Operational Insights](in englischer Sprache)(http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Blogbeitrag: Enable Operational Insights for Azure Virtual machines](in englischer Sprache)(http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Nächste Schritte

[Konfigurieren von Proxy- und Firewall-Einstellungen (Optional)](operational-insights-proxy-filewall.md)




<!--HONumber=52-->