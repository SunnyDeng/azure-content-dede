<properties 
   pageTitle="Azure Automation-Webhooks | Microsoft Azure"
   description="Ein Webhook, der es einem Client ermöglicht, ein Runbook in Azure Automation über einen HTTP-Aufruf zu starten. In diesem Artikel wird beschrieben, wie Sie einen Webhook erstellen und aufrufen, um ein Runbook zu starten."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="bwren;sngun"/>

# Azure Automation-Webhooks

Mit einem *Webhook* können Sie ein bestimmtes Runbook in Azure Automation über eine einfache HTTP-Anforderung starten. Dadurch wird es externen Diensten wie Visual Studio Team Services, GitHub oder benutzerdefinierten Anwendungen ermöglicht, Runbooks zu starten, ohne eine vollständige Lösung unter Verwendung der Azure Automation API zu implementieren. ![Webhooks](media/automation-webhooks/webhooks-overview.png)

In [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md) können Sie Webhooks mit anderen Methoden zum Starten eines Runbooks vergleichen.

## Details zu einem Webhook

Die folgende Tabelle beschreibt die Eigenschaften, die Sie für einen Webhook konfigurieren müssen.

| Eigenschaft | Beschreibung |
|:---|:---|
|Name | Sie können einem Webhook einen beliebigen Namen zuweisen, da er nicht für den Client verfügbar gemacht wird. Sie benötigen den Namen nur zur Identifizierung des Runbooks in Azure Automation. <br> Es empfiehlt sich, den Webhook entsprechend dem Client zu benennen, der ihn verwenden wird. |
|URL |Die URL des Webhooks ist die eindeutige Adresse, die ein Client mit einer HTTP POST-Anforderung aufruft, um das mit dem Webhook verknüpfte Runbook zu starten. Sie wird beim Erstellen des Webhooks automatisch generiert. Sie können keine benutzerdefinierte URL angeben. <br> <br> Die URL enthält ein Sicherheitstoken, das es ermöglicht, dass das Runbook ohne weitere Authentifizierung von einem Drittanbietersystem aufgerufen werden kann. Daher sollte sie wie ein Kennwort behandelt werden. Aus Sicherheitsgründen können Sie die URL im Azure-Vorschauportal nur zu dem Zeitpunkt anzeigen, zu dem der Webhook erstellt wird. Sie sollten die URL zur späteren Verwendung an einem sicheren Ort speichern. |
|Ablaufdatum | Ebenso wie ein Zertifikat verfügt jeder Webhook über ein Ablaufdatum, nach dem er nicht mehr verwendet werden kann. Das Ablaufdatum kann nach der Erstellung des Webhooks nicht geändert werden, und der Webhook kann nach Erreichen des Ablaufdatums nicht wieder aktiviert werden. In diesem Fall müssen Sie einen neuen Webhook erstellen, um den aktuellen zu ersetzen, und den Client aktualisieren, sodass er den neuen Webhook verwendet. |
| Aktiviert | Ein Webhook ist bei Erstellung standardmäßig aktiviert. Wenn Sie "Deaktiviert" festlegen, kann es von keinem Client mehr verwendet werden. Sie können die Eigenschaft **Aktiviert** beim Erstellen des Webhooks oder zu einem anderen Zeitpunkt nach der Erstellung festlegen. |


### Parameter
Ein Webhook kann Werte für Runbookparameter definieren, die verwendet werden, wenn das Runbook von diesem Webhook gestartet wird. Der Webhook muss Werte für alle obligatorischen Parameter des Runbooks einschließen und kann Werte für optionale Parameter einschließen. Ein Parameterwert, der für ein Webhook konfiguriert wurde, kann auch nach der Erstellung des Webhooks geändert werden. Mehrere mit einem einzelnen Runbook verknüpften Webhooks können verschiedene Parameterwerte verwenden.

Wenn ein Client ein Runbook mithilfe eines Webhooks startet, können die im Webhook definierten Parameterwerte nicht überschrieben werden. Um Daten aus dem Client abzurufen, kann das Runbook nur einen Parameter namens **$WebhookData** vom Typ [object] akzeptieren, der Daten enthält, die der Client in die POST-Anforderung einschließt.

![Webhookdaten](media/automation-webhooks/webhookdata.png)

Das Objekt **$WebhookData** verfügt über folgende Eigenschaften:

| Eigenschaft | Beschreibung |
|:--- |:---|
| WebhookName | Der Name des Webhooks. |
| RequestHeader | Die Hashtabelle mit den Headern der eingehenden POST-Anforderung |
| RequestBody | Der Header der eingehenden POST-Anforderung. Hiermit werden sämtliche Formatierungen beibehalten, z. B. Zeichenfolgenformat, JSON, XML oder formularcodierte Daten. Das Runbook muss so verfasst werden, dass es mit dem erwarteten Datenformat funktioniert.|


Zur Unterstützung des Parameters **$WebhookData** ist keine Konfiguration des Webhooks erforderlich, und das Runbook muss ihn nicht akzeptieren. Wenn das Runbook den Parameter nicht definiert, werden alle vom Client gesendeten Details der Anforderung ignoriert.

Wenn Sie beim Erstellen des Webhooks einen Wert für "$WebhookData" angeben, wird dieser Wert beim Starten des Runbooks durch den Webhook mit den Daten aus der POST-Anforderung des Clients überschrieben, selbst wenn der Anforderungstext des Clients keine Daten enthält. Wenn Sie ein Runbook, das einen "$WebhookData"-Wert enthält, mit einer anderen Methode als einem Webhook starten, können Sie einen Wert für "$WebhookData" bereitstellen, der vom Runbook erkannt wird. Bei diesem Wert sollte es sich um ein Objekt mit den gleichen [Eigenschaften](#details-of-a-webhook) wie „$WebhookData“ handeln, damit das Runbook damit ordnungsgemäß funktioniert, als würde es mit WebhookData arbeiten, die über einen Webhook übergeben werden.

Wenn Sie beispielsweise das folgende Runbook aus dem Azure-Portal starten und WebhookData als Testbeispiele übergeben möchten, sollten diese in der Benutzerschnittstelle als JSON übergeben werden, da WebhookData ein Objekt ist.

![WebhookData-Parameter von der Benutzeroberfläche](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Angenommen, dass für das oben genannte Runbook Eigenschaften mit folgenden Werten für den WebhookData-Parameter vorliegen:

1. WebhookName: *MyWebhook*
2. RequestHeader: *From=Testbenutzer*
3. RequestBody: *["VM1", "VM2"]*

Dann würden Sie den folgenden JSON-Wert in der Benutzeroberfläche für den WebhookData-Parameter übergeben:

* {"WebhookName":"MyWebhook", "RequestHeader":{"From":"Testbenutzer"}, "RequestBody":"["VM1","VM2"]"}

![WebhookData-Startparameter von der Benutzeroberfläche](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)


>[AZURE.NOTE]Die Werte aller Eingabeparameter werden mit dem Runbookauftrag protokolliert. Dies bedeutet, dass alle vom Client in der Webhookanforderung bereitgestellten Eingaben protokolliert werden und jedem Benutzer mit Zugriff auf den Automatisierungsauftrag zur Verfügung stehen. Aus diesem Grund sollten Sie sorgfältig überlegen, welche vertraulichen Daten Sie in Webhookaufrufe einschließen.

## Sicherheit

Die Sicherheit eines Webhooks beruht auf dem Datenschutz seiner URL, die ein Sicherheitstoken enthält, mit dem es aufgerufen werden kann. Azure Automation führt keinerlei Authentifizierung der Anforderung durch, sofern diese über die richtige URL erfolgt. Aus diesem Grund sollten Webhooks nicht für Runbooks verwendet werden, die sehr vertrauliche Funktionen ausführen, ohne eine alternative Möglichkeit zur Überprüfung der Anforderung zu verwenden.

Sie können Logik in das Runbook integrieren, um zu ermitteln, ob es von einem Webhook aufgerufen wurde, indem Sie die Eigenschaft **WebhookName** des Parameters "$WebhookData" aktivieren. Das Runbook kann eine weitere Überprüfung durchführen, indem es in den Eigenschaften **RequestHeader** oder **RequestBody** nach bestimmten Informationen sucht.

Eine andere Strategie besteht darin, dass das Runbook eine externe Bedingung überprüft, wenn es eine Webhookanforderung empfängt. Betrachten Sie z. B. ein Runbook, das von GitHub aufgerufen wird, sobald ein neuer Commit für ein GitHub-Repository vorhanden ist. Das Runbook kann eine Verbindung mit GitHub herstellen, um zu überprüfen, ob tatsächlich gerade ein neuer Commit durchgeführt wurde, bevor es mit der Ausführung fortfährt.

## Erstellen eines Webhooks

Gehen Sie wie folgt vor, um einen neuen Webhook zu erstellen, der mit einem Runbook im Azure-Vorschauportal verknüpft ist

1. Klicken Sie im Blatt **Runbooks** im Azure-Vorschauportal auf das Runbook, das vom Webhook gestartet werden soll, um das Detailblatt des Runbooks anzuzeigen. 
3. Klicken Sie oben im Blatt auf **Webhook**, um das Blatt **Webhook hinzufügen** zu öffnen. <br> ![Schaltfläche "Webhooks"](media/automation-webhooks/webhooks-button.png)
4. Klicken Sie auf **Neuen Webhook erstellen**, um das Blatt **Webhook erstellen** zu öffnen.
5. Geben Sie einen **Namen** und ein **Ablaufdatum** für den Webhook an, und legen Sie fest, ob er aktiviert werden soll. Weitere Informationen zu diesen Eigenschaften finden Sie unter [Details zu einem Webhook](#details-of-a-webhook).
6. Klicken Sie auf das Symbol zum Kopieren, und drücken Sie STRG+C, um die URL des Webhooks zu kopieren. Speichern Sie die URL an einem sicheren Ort. **Nachdem Sie den Webhook erstellt haben, können Sie die URL nicht erneut abrufen.** <br> ![Webhook-URL](media/automation-webhooks/copy-webhook-url.png)
3. Klicken Sie auf **Parameter**, um Werte für die Runbookparameter bereitzustellen. Wenn das Runbook über obligatorische Parameter verfügt, können Sie den Webhook nur erstellen, wenn die entsprechenden Werte bereitgestellt wurden.
1. Klicken Sie auf **Erstellen**, um das Projekt zu erstellen.


## Verwenden eines Webhooks

Nachdem ein Webhook erstellt wurde, muss Ihre Clientanwendung zum Verwenden des Webhooks eine HTTP POST-Anforderung mit der URL für den Webhook ausgeben. Die Syntax des Webhooks weist folgendes Format auf.

	http://<Webhook Server>/token?=<Token Value>

Der Client empfängt einen der folgenden Rückgabecodes als Antwort auf die POST-Anforderung.

| Code | Text | Beschreibung |
|:---|:----|:---|
| 202 | Zulässig | Die Anforderung wurde akzeptiert, und das Runbook wurde erfolgreich in die Warteschlange gestellt. |
| 400 | Ungültige Anforderung | Die Anforderung wurde aus einem der folgenden Gründe nicht akzeptiert. <ul> <li>Der Webhook ist abgelaufen.</li> <li>Der Webhook ist deaktiviert.</li> <li>Das Token in der URL ist ungültig.</li> </ul>|
| 404 | Nicht gefunden | Die Anforderung wurde aus einem der folgenden Gründe nicht akzeptiert. <ul> <li>Der Webhook wurde nicht gefunden.</li> <li>Das Runbook wurde nicht gefunden.</li> <li>Das Konto wurde nicht gefunden.</li> </ul> |
| 500 | Interner Serverfehler | Die URL ist gültig, es ist jedoch ein Fehler aufgetreten. Senden Sie die Anforderung erneut. |

Wenn die Anforderung erfolgreich ist, enthält die Antwort des Webhooks wie im Folgenden dargestellt die Auftrags-ID im JSON-Format. Die Antwort enthält eine einzelne Auftrags-ID, wobei das JSON-Format Potenzial für künftige Verbesserungen bietet.

	{"JobIds":["<JobId>"]}  

Der Client kann weder die Bestätigung, dass ein Runbookauftrag abgeschlossen wurde, noch den Abschlussstatus aus einem Webhook abrufen. Diese Informationen können mithilfe der Auftrags-ID unter Verwendung einer anderen Methode ermittelt werden, z. B. mit [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) oder der [Azure Automation-API](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### Beispiel

Im folgenden Beispiel wird Windows PowerShell verwendet, um ein Runbook mit einem Webhook zu starten. Beachten Sie, dass in jeder Sprache, in der eine HTTP-Anforderung gesendet werden kann, auch die Verwendung eines Webhooks möglich ist; Windows PowerShell dient hier lediglich als Beispiel.

Das Runbook erwartet im Anforderungstext eine Liste von virtuellen Computern, die in JSON formatiert ist. Darüber hinaus fügen wir dem Header der Anforderung Informationen zum Benutzer, der das Runbook startet, sowie zu Datum und Uhrzeit des Starts hinzu.

	$uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}
    
    $vms  = @(
    			@{ Name="vm01";ServiceName="vm01"},
    			@{ Name="vm02";ServiceName="vm02"}
    		)
	$body = ConvertTo-Json -InputObject $vms 

	$response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
	$jobid = ConvertFrom-Json $response 


Die folgende Abbildung zeigt die Headerinformationen der Anforderung (unter Verwendung einer [Fiddler](http://www.telerik.com/fiddler)-Ablaufverfolgung). Diese beinhalten zusätzlich zu den von uns hinzugefügten benutzerdefinierten Headern mit Datums- und Benutzerangaben die Standardheader einer HTTP-Anforderung. Jeder dieser Werte steht dem Runbook in der **RequestHeaders**-Eigenschaft von **WebhookData** zur Verfügung.

![Schaltfläche "Webhooks"](media/automation-webhooks/webhook-request-headers.png)

Die folgende Abbildung zeigt den Anforderungstext (unter Verwendung einer [Fiddler](http://www.telerik.com/fiddler)-Ablaufverfolgung), der dem Runbook in der **RequestBody**-Eigenschaft von **WebhookData** zur Verfügung steht. Der Text ist in JSON formatiert, da dieses Format im Anforderungstext enthalten war.

![Schaltfläche "Webhooks"](media/automation-webhooks/webhook-request-body.png)

Die folgende Abbildung zeigt die Anforderung, die von Windows PowerShell gesendet wurde, sowie die daraus resultierende Antwort. Die Auftrags-ID wurde aus der Antwort extrahiert und in eine Zeichenfolge konvertiert.

![Schaltfläche "Webhooks"](media/automation-webhooks/webhook-request-response.png)

Das folgende Beispiel-Runbook akzeptiert die Anforderung aus dem vorherigen Beispiel und startet die im Anforderungstext angegebenen virtuellen Computer.

	workflow Test-StartVirtualMachinesFromWebhook
	{
		param (	
			[object]$WebhookData
		)

		# If runbook was called from Webhook, WebhookData will not be null.
		if ($WebhookData -ne $null) {	
			
			# Collect properties of WebhookData
			$WebhookName 	= 	$WebhookData.WebhookName
			$WebhookHeaders = 	$WebhookData.RequestHeader
			$WebhookBody 	= 	$WebhookData.RequestBody
			
			# Collect individual headers. VMList converted from JSON.
			$From = $WebhookHeaders.From
			$VMList = ConvertFrom-Json -InputObject $WebhookBody
			Write-Output "Runbook started from webhook $WebhookName by $From."
			
			# Authenticate to Azure resources
			$Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
			Add-AzureAccount -Credential $Cred
			
            # Start each virtual machine
			foreach ($VM in $VMList)
			{
				$VMName = $VM.Name
				Write-Output "Starting $VMName"
				Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
			}
		}
		else {
			Write-Error "Runbook mean to be started only from webhook." 
		} 
	}


## Erstellen von Runbooks als Antwort auf Azure-Warnungen

Webhook-fähige Runbooks können verwendet werden, um auf [Azure-Warnungen](../azure-portal/insights-receive-alert-notifications.md) zu reagieren. Durch das Sammeln von Statistiken wie Leistung, Verfügbarkeit und Nutzung mithilfe der Azure-Warnungen können Ressourcen in Azure überwacht werden. Sie können auf der Grundlage von Überwachungsmetriken oder Ereignissen für Ihre Azure-Ressourcen eine Warnung empfangen, derzeit unterstützen Automation-Konten nur Metriken. Wenn der Wert einer angegebenen Metrik den zugewiesenen Schwellenwert überschreitet oder wenn das konfigurierte Ereignis ausgelöst wird, wird eine Benachrichtigung an den Dienstadministrator oder an die Co-Administratoren gesendet, um die Warnung zu beheben. Weitere Informationen zu Metriken und Ereignissen finden Sie unter [Azure-Warnungen](../azure-portal/insights-receive-alert-notifications.md).

Neben der Verwendung von Azure-Warnungen als Benachrichtigungssystem können Sie auch Runbooks als Reaktion auf Warnungen erstellen. Azure Automation bietet die Möglichkeit, Webhook-fähige Runbooks mit Azure-Warnungen auszuführen. Wenn eine Metrik den konfigurierten Schwellenwert überschreitet, wird die Warnregel aktiv und löst den Automation-Webhook aus, der wiederum das Runbook ausführt.

![Webhooks](media/automation-webhooks/webhook-alert.jpg)

### Der Warnungskontext

Bei einer Azure Ressource wie einem virtuellen Computer ist die CPU-Auslastung dieses Computers eine der wichtigsten Leistungsmetriken. Wenn die CPU-Auslastung über einen langen Zeitraum 100 % oder mehr beträgt, empfiehlt sich ein Neustart des virtuellen Computers, um das Problem zu beheben. Dies kann gelöst werden, indem für den virtuellen Computer eine Warnregel mit dem CPU-Prozentsatz als Metrik konfiguriert wird. Der CPU-Prozentsatz dient hier lediglich als Beispiel. Es gibt zahlreiche weitere Metriken, die Sie für Ihre Azure-Ressourcen konfigurieren können. Der Neustart des virtuellen Computers ist eine Aktion, um dieses Problem zu beheben. Sie können aber auch andere Aktionen für das Runbook konfigurieren.

Wenn diese Warnregel aktiv wird und das Webhook-fähige Runbook auslöst, wird der Warnungskontext an das Runbook gesendet. Der [Warnungskontext](../azure-portal/insights-receive-alert-notifications.md) enthält Details wie **SubscriptionID**, **ResourceGroupName**, **ResourceName**, **ResourceType**, **ResourceId** und **Timestamp**, die erforderlich sind, damit das Runbook die Ressource identifizieren kann, für die eine Aktion ausgeführt wird. Der Warnungskontext ist im Textteil des **WebhookData**-Objekts eingebettet, das an das Runbook gesendet wird, und der Zugriff darauf ist über die **Webhook.RequestBody**-Eigenschaft möglich.


### Beispiel

Erstellen Sie einen virtuellen Azure-Computer unter Ihrem Abonnement, und weisen Sie eine [Warnung zur Überwachung der CPU-Prozentsatzmetrik zu](../azure-portal/insights-receive-alert-notifications.md). Achten Sie beim Erstellen der Warnung darauf, im Webhook-Feld die URL des Webhooks anzugeben, die beim Erstellen des Webhooks generiert wurde.

Das folgende Beispiel-Runbook wird immer dann ausgelöst, wenn die Warnregel aktiv wird, und es werden die Parameter des Warnungskontexts gesammelt, die erforderlich sind, damit das Runbook die Ressource identifizieren kann, für die die Aktion ausgeführt wird.

	workflow Invoke-RunbookUsingAlerts
	{
	    param (  	
	        [object]$WebhookData 
	    ) 

	    # If runbook was called from Webhook, WebhookData will not be null.
	    if ($WebhookData -ne $null) {   
	        # Collect properties of WebhookData. 
	        $WebhookName    =   $WebhookData.WebhookName 
	        $WebhookBody    =   $WebhookData.RequestBody 
	        $WebhookHeaders =   $WebhookData.RequestHeader 

	        # Outputs information on the webhook name that called This 
	        Write-Output "This runbook was started from webhook $WebhookName." 

	        
			# Obtain the WebhookBody containing the AlertContext 
			$WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody) 
	        Write-Output "`nWEBHOOK BODY" 
	        Write-Output "=============" 
	        Write-Output $WebhookBody 

	        # Obtain the AlertContext     
	        $AlertContext = [object]$WebhookBody.context

	        # Some selected AlertContext information 
	        Write-Output "`nALERT CONTEXT DATA" 
	        Write-Output "===================" 
	        Write-Output $AlertContext.name 
	        Write-Output $AlertContext.subscriptionId 
	        Write-Output $AlertContext.resourceGroupName 
	        Write-Output $AlertContext.resourceName 
	        Write-Output $AlertContext.resourceType 
	        Write-Output $AlertContext.resourceId 
	        Write-Output $AlertContext.timestamp 

	    	# Act on the AlertContext data, in our case restarting the VM. 
	    	# Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine. 
	        $cred = Get-AutomationPSCredential -Name "MyAzureCredential" 
	        Add-AzureAccount -Credential $cred 
	        Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN" 
	      
	        #Check the status property of the VM
	        Write-Output "Status of VM before taking action"
	        Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
	        Write-Output "Restarting VM"

	        # Restart the VM by passing VM name and Service name which are same in this case
	        Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName 
	        Write-Output "Status of VM after alert is active and takes action"
	        Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
	    } 
	    else  
	    { 
	        Write-Error "This runbook is meant to only be started from a webhook."  
	    }  
	}

 

## Nächste Schritte

- Weitere Informationen zu verschiedenen Methoden zum Starten eines Runbooks finden Sie unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md).
- Informationen zum Anzeigen des Status eines Runbookauftrags finden Sie unter [Ausführen von Runbooks in Azure Automation](automation-runbook-execution.md).
- [Ausführen von Aktionen nach Azure-Warnungen mithilfe von Azure Automation](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/)

<!---HONumber=AcomDC_0121_2016-->