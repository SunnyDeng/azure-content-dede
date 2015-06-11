<properties 
	pageTitle="Erstellen von Warnungen für Azure Data Factory-Ereignisse" 
	description="Erfahren Sie, wie Sie Warnungen für Ereignisse erstellen, die von Azure für Data Factory-Vorgänge ausgelöst werden." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="spelluru"/>

# Erstellen von Warnungen zu Azure-Ereignissen
Azure-Ereignisse bieten hilfreiche Einblicke in die Aktivitäten in Ihren Azure-Ressourcen. Azure protokolliert Benutzerereignisse, wenn eine Azure-Ressource (z. B. Data Factory) erstellt, aktualisiert oder gelöscht wird. Bei Verwendung von Azure Data Factory werden Ereignisse in folgenden Fällen generiert:
 
1.	Azure Data Factory wird erstellt/aktualisiert/gelöscht.
2.	Die Datenverarbeitung (als Ausführungen bezeichnet) wurde gestartet/abgeschlossen.
3.	Ein bedarfsbezogener HDInsight-Cluster wird erstellt und entfernt.

Sie können Warnungen zu diesen Benutzerereignissen erstellen und sie so konfigurieren, dass E-Mail-Benachrichtigungen an den Administrator und an Co-Administratoren des Abonnements gesendet werden. Darüber hinaus können Sie zusätzliche E-Mail-Adressen von Benutzern angeben, die E-Mail-Benachrichtigungen erhalten sollen, wenn bestimmte Bedingungen erfüllt werden.

## Angeben einer Warnungsdefinition
Um eine Warnungsdefinition anzugeben, erstellen Sie eine JSON-Datei mit einer Beschreibung der Vorgänge, zu denen Sie benachrichtigt werden möchten. Im folgenden Beispiel sendet die Warnung eine E-Mail-Benachrichtigung für den **RunFinished**-Vorgang. Genauer gesagt: Es wird eine E-Mail-Benachrichtigung gesendet, wenn eine Ausführung in der Data Factory abgeschlossen wurde und ein Fehler aufgetreten ist (Status = FailedExecution).

	{
    	"contentVersion": "1.0.0.0",
   		 "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    	"parameters": {},
    	"resources": 
		[
        	{
            	"name": "ADFAlertsSlice",
            	"type": "microsoft.insights/alertrules",
            	"apiVersion": "2014-04-01",
            	"location": "East US",
            	"properties": 
				{
                	"name": "ADFAlertsSlice",
                	"description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                	"isEnabled": true,
                	"condition": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    	"dataSource": 
						{
                        	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        	"operationName": "RunFinished",
			         		"status": "Failed",
                        		"subStatus": "FailedExecution"   
                    	}
                	},
                	"action": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    	"customEmails": [ "<your alias>@contoso.com" ]
                	}
            	}
        	}
    	]
	}

Aus der obigen JSON-Definition kann **subStatus** entfernt werden, wenn Sie zu einem bestimmten Fehler nicht informiert werden möchten.

Eine Liste der Vorgänge sowie Status- und Unterstatuswerte finden Sie unter [Verfügbare Vorgänge und Statuswerte](#AvailableOperationsStatuses).

## Bereitstellen der Warnung
Verwenden Sie zum Bereitstellen der Warnung das Azure-PowerShell-Cmdlet **New-AzureResourceGroupDeployment**, wie im folgenden Beispiel gezeigt:

	New-AzureResourceGroupDeployment -ResourceGroupName adf 	-TemplateFile .\ADFAlertFailedSlice.json -StorageAccountName testmetricsabc

"StorageAccountName" gibt das Speicherkonto zum Speichern der bereitgestellten Warnungs-JSON-Datei an.

Nachdem die Ressourcengruppenbereitstellung erfolgreich abgeschlossen wurde, werden die folgenden Meldungen angezeigt:
	
	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## Abrufen der Liste von Azure-Ressourcengruppenbereitstellungen
Um die Liste der bereitgestellten Azure-Ressourcengruppen abzurufen, verwenden Sie das Cmdlet **Get-AzureResourceGroupDeployment**, wie im folgenden Beispiel gezeigt:
	
	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## <a name="AvailableOperationsStatuses"></a>Verfügbare Vorgangsnamen und Statuswerte

<table>
<th align="left">Vorgangsname</th>
<th align="left">Status</th>
<th align="left">Unterstatus</th>

<tr>
<td>RunStarted</td>
<td>Started</td>
<td>Starting</td>
</tr>

<tr>
<td>RunFinished</td>
<td>Failed / Succeeded</td>
<td>
	<p>FailedResourceAllocation </p>
	<p>Succeeded</p>
	<p>FailedExecution</p>
	<p>TimedOut</p>
	<p>Canceled</p>
	<p>FailedValidation</p>
	<p>Abandoned</p>
</td>
</tr>

<tr>
<td>SliceOnTime</td>
<td>In Progress</td>
<td>Ontime</td>
</tr>

<tr>
<td>SliceDelayed</td>
<td>In Progress</td>
<td>Late</td>
</tr>

<tr>
<td>OnDemandClusterCreateStarted</td>
<td>Gestartet</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterCreateSuccessful</td>
<td>Succeeded</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterDeleted</td>
<td>Succeeded</td>
<td></td>
</tr>

</table>

## Problembehandlung bei Benutzerereignissen
Führen Sie den folgenden Befehl aus, um die generierten Ereignisse anzuzeigen:

	Get-AzureResourceGroupLog –Name $ResourceGroup -All | Where-Object EventSource -eq "Microsoft.DataFactory"

<!---HONumber=GIT-SubDir--> 