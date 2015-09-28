<properties
   pageTitle="Analysieren der Daten von Servern in Microsoft Azure"
   description="Durchsuchen der Ereignis- und IIS-Protokolle für Cloud-Dienste und virtuelle Maschinen durch Aktivieren der Azure-Diagnose."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="banders"/>
# Analysieren der Daten von Servern in Microsoft Azure

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights verwendet Daten von Servern in Ihrer lokalen oder Cloudinfrastruktur. Sie können Computerdaten aus dem Azure-Speicher sammeln, wenn sie von Azure-Diagnose generiert werden.

Mit den gesammelten Daten aus dem Azure-Speicher können Sie schnell Ereignis- und IIS-Protokolle für Clouddienste und virtuelle Computer durchsuchen. Außerdem erhalten Sie weitere Einblicke in Ihre virtuellen Computer durch die Installation von Microsoft Monitoring Agent.

Die Lösungen Update Assessment, Change Tracking und SQL Assessment beruhen alle auf den tieferen Einblicken, die Microsoft Monitoring Agent für Ihre virtuellen Computer bereitstellt. Falls Sie dies noch nicht getan haben, können Sie [Lösungen hinzufügen](operational-insights-setup-workspace.md), wenn Sie im [Operational Insights-Portal](https://www.microsoft.com/oms/) angemeldet sind.

Für virtuelle Computer in Azure gibt es zwei einfache Methoden, die Agent-basierte Datensammlung zu aktivieren:

- Im Microsoft Azure-Verwaltungsportal

- Mithilfe von PowerShell

Bei Verwendung der Agent-basierten Sammlung für Protokolldaten müssen Sie auf der Konfigurationsseite der Protokollverwaltung im [Operational Insights-Portal](https://www.microsoft.com/oms/) konfigurieren, welche Protokolle erfasst werden sollen

 >[AZURE.NOTE]Wenn Sie Operational Insights so konfiguriert haben, dass Protokolldaten mithilfe der Azure-Diagnose indiziert werden, und Sie den Agent für das Erfassen von Protokollen konfigurieren, werden dieselben Protokolle doppelt indiziert. Für beide Datenquellen werden die regulären Datenraten berechnet. Wenn Sie den Agent installiert haben, sollten Sie die Protokolldaten mithilfe des Agents sammeln und nicht die von der Azure-Diagnose erfassten Protokolle indizieren.

## Microsoft Azure-Verwaltungsportal

Sie können den Agent für Operational Insights aus dem [Azure-Portal](https://manage.windowsazure.com/#Workspaces/OperationalInsightExtension/Workspaces) installieren.

### So installieren Sie den Operational Insights-Agent:
1. Wechseln Sie im Azure-Portal zu Ihrem Operational Insights-Arbeitsbereich, und wählen Sie die Registerkarte **Server** aus.
2. Auf der Registerkarte sehen Sie eine Liste der virtuellen Computer. Wählen Sie den virtuellen Computer aus, auf dem Sie den Agent installieren möchten, und klicken Sie dann auf **Op Insights aktivieren**.

Der Agent wird automatisch installiert und für den Operational Insights-Arbeitsbereich konfiguriert.

![Abbildung der Seite mit Operational Insights-Servern](./media/operational-insights-analyze-data-azure/servers.png)

 >[AZURE.NOTE]Der [VM-Agent von Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) muss installiert sein, damit der Agent für Operational Insights automatisch installiert wird. Wenn Sie über einen virtuellen Azure-Ressourcen-Manager-Computer verfügen, wird dieser in der Liste nicht angezeigt, und Sie müssen PowerShell verwenden oder eine ARM-Vorlage erstellen, um den Agent zu installieren.



## PowerShell

Wenn Sie für Änderungen an Ihren virtuellen Azure-Computern lieber die Skriptfunktion verwenden, können Sie Microsoft Monitoring Agent über PowerShell aktivieren.

Microsoft Monitoring Agent ist eine [Erweiterung des virtuellen Azure-Computers](https://msdn.microsoft.com/library/azure/dn832621.aspx), den Sie mithilfe von PowerShell verwalten können, wie in den folgenden Beispielen gezeigt wird.

Verwenden Sie für „klassische“ virtuelle Azure-Computer diese PowerShell:

```powershell
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```
Verwenden Sie für virtuelle Azure-Ressourcen-Manager-Computer diese PowerShell:

```powershell
Add-AzureAccount
Switch-AzureMode -Name AzureResourceManager

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"

$resourcegroup = "enter resource group"
$resourcename = "enter resource group"

$vm = Get-AzureVM -ResourceGroupName $resourcegroup -Name $resourcename
$location = $vm.Location

Set-AzureVMExtension -ResourceGroupName $resourcegroup -VMName $resourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId':  '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey' }"

```

Bei der Konfiguration mit PowerShell müssen Sie die Arbeitsbereichs-ID und einen Primärschlüssel bereitstellen. Sie finden Ihre Arbeitsbereichs-ID und den Primärschlüssel auf der Seite **Einstellungen** im Operational Insights-Portal.

![Quellen](./media/operational-insights-analyze-data-azure/sources01.png)

## Sammeln von Daten mithilfe von Azure-Diagnose

Operational Insights kann Daten analysieren, die von der Azure-Diagnose in den Azure-Speicher geschrieben werden. Es sind zwei grundlegende Schritte auszuführen:

- Konfigurieren der Sammlung von Diagnosedaten in Azure Storage
- Konfigurieren von Operational Insights für die Analyse der Daten im Speicherkonto

Die unten stehenden Themen beschreiben, wie Sie die Sammlung von Diagnosedaten in Azure Storage aktivieren und anschließend Operational Insights zur Analyse der Daten in Azure Storage konfigurieren.

Die Azure-Diagnose ist eine Azure-Erweiterung, mit der Sie Diagnosedaten von einer Worker- oder Webrolle oder einem virtuellen Computer erfassen können, die bzw. der in Azure ausgeführt wird. Die Daten werden in einem Azure-Speicherkonto gespeichert und können dann von Operational Insights verwendet werden.

>[AZURE.NOTE]Ihnen werden die gängigen Datenraten für Speicherung und Transaktionen in Rechnung gestellt, wenn Sie Diagnoseinformationen an ein Speicherkonto senden und wenn Operational Insights die Daten aus Ihrem Speicherkonto liest.

Mit der Azure-Diagnose können die folgenden Arten von Telemetriedaten erfasst werden:

Datenquelle|Beschreibung
 ---|---
IIS-Protokolle|Informationen zu IIS-Websites
Infrastrukturprotokolle der Azure-Diagnose|Informationen zur Diagnose selbst
Protokolle zu IIS-Anforderungsfehlern |Informationen zu fehlgeschlagenen IIS-Website- oder IIS-Anwendungsanforderungen
Windows-Ereignisprotokolle|An das Windows-System für die Ereignisprotokollierung gesendete Informationen
Leistungsindikatoren|Leistungsindikatoren des Betriebssystems und benutzerdefinierte Leistungsindikatoren
Absturzabbilder|Informationen zum Status des Prozesses im Fall eines Anwendungsabsturzes
Benutzerdefinierte Fehlerprotokolle|Von Ihrer Anwendung oder Ihrem Dienst erstellte Protokolle
NET-EventSource|Von Ihrem Code mit der .NET-[EventSource-Klasse](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx) generierte Ereignisse
Manifestbasiertes ETW|Von einem beliebigen Prozess generierte ETW-Ereignisse.
Syslog|An die Syslog- oder Rsyslog-Daemons gesendete Ereignisse.


Derzeit kann Operational Insights Folgendes analysieren:

- IIS-Protokolle von Webrollen und virtuellen Maschinen
- Windows-Ereignisprotokolle von Webrollen, Workerrollen und virtuellen Azure-Computern unter einem Windows-Betriebssystem
- Syslog aus virtuellen Azure-Computern mit Linux-Betriebssystem

Die Protokolle müssen in folgenden Speicherorten enthalten sein:

- WADWindowsEventLogsTable (Tabellenspeicherung) – Enthält Informationen aus Windows-Ereignisprotokollen.
- wad-iis-logfiles (Blob-Speicher) – Enthält Informationen zu IIS-Protokollen.
- LinuxsyslogVer2v0 (Tabellenspeicherung) – enthält Linux-Syslog-Ereignisse.

 >[AZURE.NOTE]IIS-Protokolle von Azure-Websites werden derzeit nicht unterstützt.

Bei virtuellen Computern haben Sie auch die Möglichkeit, [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) auf Ihrem virtuellen Computer zu installieren, um weitere Einblicke zu aktivieren. Auf diese Weise können Sie nicht nur IIS-Protokolle und Ereignisprotokolle analysieren, sondern auch zusätzliche Analysen durchführen, einschließlich der Nachverfolgung von Konfigurationsänderungen, SQL-Bewertung und der Bewertung von Updates.

Sie können uns durch Ihre Stimme auf unserer [Feedbackseite](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy) helfen, die Prioritäten für weitere zu analysierende Protokolle für Operational Insights zu verteilen.

## Aktivieren der Azure-Diagnose in einer Webrolle für die Sammlung von IIS-Protokollen und -Ereignissen

Informationen hierzu finden Sie unter [Aktivieren der Diagnose in einem Clouddienst](https://msdn.microsoft.com/library/azure/dn482131.aspx). Sie verwenden die grundlegenden Informationen von dort und passen hier die Schritte für die Verwendung mit Microsoft Azure Operational Insights an.

Bei aktivierter Azure-Diagnose:

- IIS-Protokolle werden standardmäßig gespeichert, und Protokolldaten werden im Übertragungsintervall ScheduledTransferPeriod übertragen.

- Windows-Ereignisprotokolle werden standardmäßig nicht übertragen.

### So aktivieren Sie die Diagnose

Um Windows-Ereignisprotokolle zu aktivieren oder „scheduledTransferPeriod“ zu ändern, konfigurieren Sie die Azure-Diagnose mithilfe der XML-Konfigurationsdatei (diagnostics.wadcfg), wie im Thema „Aktivieren der Diagnose in einem Clouddienst“ in [Schritt 2: Hinzufügen der Datei „diagnostics.wadcfg“ zur Visual Studio-Projektmappe](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) und in [Schritt 3: Konfigurieren der Diagnose für Ihre Anwendung](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) beschrieben wird. Die folgende Beispielkonfigurationsdatei sammelt IIS-Protokolle und alle Ereignisse aus dem Anwendungs- und dem Systemprotokoll:

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


Stellen Sie in [Schritt 4: Konfigurieren des Speichers der Diagnosedaten](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) im Thema „Aktivieren der Diagnose in einem Clouddienst“ sicher, dass „ConfigurationSettings“ ein Speicherkonto angibt, wie im folgenden Beispiel gezeigt wird:


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


Die Werte für **AccountName** und **AccountKey** finden Sie im Microsoft Azure-Verwaltungsportal im Speicherkonto-Dashboard unter „Zugriffsschlüssel verwalten“. Als Protokoll für die Verbindungszeichenfolge muss **https** verwendet werden.

Sobald die aktualisierte Diagnosekonfiguration auf den Cloud-Dienst angewendet wurde und Diagnoseinformationen in Azure Storage geschrieben werden, sind Sie zur Konfiguration von Operational Insights bereit.

## Aktivieren der Azure-Diagnose auf einem virtuellen Computer zur Erfassung von Ereignis- und IIS-Protokollen

Gehen Sie folgendermaßen vor, um die Azure-Diagnose mithilfe des Microsoft Azure-Verwaltungsportals auf einem virtuellen Computer für die Erfassung von Ereignis- und IIS-Protokollen zu aktivieren.

### So aktivieren Sie die Azure-Diagnose auf einem virtuellen Computer mit dem Azure-Verwaltungsportal:

1. Installieren Sie den VM-Agent, wenn Sie einen virtuellen Computer erstellen. Ist der virtuelle Computer bereits vorhanden, stellen Sie sicher, dass der VM-Agent bereits installiert ist.
	- Wenn Sie das standardmäßige Azure-Verwaltungsportal zum Erstellen des virtuellen Computers verwenden, führen Sie **Benutzerdefiniert erstellen** durch, und wählen Sie **VM-Agent installieren** aus.
	- Wenn Sie das neue Azure-Verwaltungsportal zum Erstellen eines virtuellen Computers verwenden, wählen Sie **Optionale Konfiguration** und dann **Diagnose**, und setzen Sie **Status** auf **Ein**.

	Nach Abschluss des Vorgangs wird auf dem virtuellen Computer automatisch die Azure-Diagnoseerweiterung installiert und ausgeführt. Diese ist für das Sammeln von Diagnosedaten zuständig.

2. Aktivieren Sie die Überwachung, und konfigurieren Sie die Ereignisprotokollierung für einen vorhandenen virtuellen Computer. Sie können die Diagnose auf VM-Ebene aktivieren. Zum Aktivieren der Diagnose und zur anschließenden Konfiguration der Ereignisprotokollierung führen Sie die folgenden Schritte aus:
	1. Wählen Sie den virtuellen Computer aus.
	2. Klicken Sie auf **Überwachen**.
	3. Klicken Sie auf **Diagnose**.
	4. Setzen Sie den **Status** auf **EIN**.
	5. Wählen Sie die einzelnen Diagnosemetriken aus, die Sie verwenden möchten. Operational Insights kann Windows-Ereignissystemprotokolle, Windows-Ereignisanwendungsprotokolle und IIS-Protokolle analysieren.
	7. Klicken Sie auf **OK**.

Mit Azure PowerShell können Sie die Ereignisse genauer angeben, die in Azure Storage geschrieben werden. Eine Beispielkonfigurationsdatei und eine detaillierte Dokumentation des Schemas finden Sie im Konfigurationsschema von Azure-Diagnose 1.2. Stellen Sie sicher, dass Sie Azure PowerShell, Version 0.8.7 oder höher aus [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure) installieren und konfigurieren. Wenn eine Version von Microsoft Azure-Diagnose installiert ist, die älter ist als Version 1.2, können Sie das neue Portal nicht zum Aktivieren oder Konfigurieren der Diagnose verwenden.

Sie können den Agent mit dem folgenden PowerShell-Skript aktivieren und aktualisieren. Dieses Skript können Sie auch mit benutzerdefinierter Protokollierungskonfiguration verwenden. Sie müssen das Skript ändern, um das Speicherkonto, den Dienstnamen und den Namen des virtuellen Computers festzulegen.

### So aktivieren Sie die Diagnose auf einem virtuellen Computer mit Azure PowerShell

Überprüfen Sie das folgende Beispielskript, kopieren Sie es, ändern Sie es nach Bedarf, speichern Sie das Beispiel als PowerShell-Skriptdatei, und führen Sie das Skript aus.


	#Connect to Azure
	Add-AzureAccount

	# settings to change:
	$wad_storage_account_name = "myStorageAccount"
	$service_name = "myService"
	$vm_name = "myVM"

	#Construct Azure Diagnostics public config and convert to config format

	# Collect just system error events:
	$wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

	$wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
	$wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

	#Construct Azure diagnostics private config

	$wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
	$wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

	#Enable Diagnostics Extension for Virtual Machine

	$wad_extension_name = "IaaSDiagnostics"
	$wad_publisher = "Microsoft.Azure.Diagnostics"
	$wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

	(Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM


## Aktivieren der Azure Storage-Analyse durch Operational Insights

Wenden Sie folgendes Verfahren an, um die Speicheranalyse zu aktivieren und um Operational Insights für das Lesen aus dem Azure Storage-Konto mit der Azure-Diagnose zu konfigurieren.

### So aktivieren Sie die Analyse über Operational Insights

1. Navigieren Sie im Azure-Standardportal zu Ihrem Operational Insights-Arbeitsbereich, und wählen Sie die Registerkarte **Speicher** aus. ![Registerkarte „Arbeitsbereichspeicher“](./media/operational-insights-analyze-data-azure/workspace-storage-tab.png)
2. Klicken Sie auf **Hinzufügen eines Speicherkontos**, um das Feld **Speicherkonto hinzufügen** zu öffnen.
3. Wählen Sie das Speicherkonto, das Sie verwenden möchten.
4. Wählen Sie in der Liste **Datentyp** einen Datentyp: entweder **Ereignisse**, **IIS-Protokolle** oder **Syslog (Linux)**.
5. Klicken Sie auf das Symbol **OK**.<br> ![Feld „Speicherkonto“](./media/operational-insights-analyze-data-azure/storage-account.png)
6. Wiederholen Sie die oben genannten Schritte für jede Kombination aus Datentyp und Speicherkonto, die Sie erfassen möchten.

Nach etwa einer Stunde werden Daten aus dem Speicherkonto für die Analyse in Operational Insights verfügbar.

## Verwandte Inhalte

- [Direktes Verbinden von Computern mit Operational Insights](operational-insights-direct-agent)
- [Blogbeitrag: Aktivieren von Operational Insights für virtuelle Azure-Computer](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Nächste Schritte

[Konfigurieren von Proxy- und Firewall-Einstellungen (Optional)](../operational-insights-proxy-filewall.md)

<!---HONumber=Sept15_HO3-->