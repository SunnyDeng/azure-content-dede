<properties
   pageTitle="Sammeln von Protokollen mit Azure-Diagnose und Azure Operational Insights | Microsoft Azure"
   description="In diesem Artikel wird beschrieben, wie Sie Azure-Diagnose und Azure Operational Insights so konfigurieren, dass Protokolle aus einem Service Fabric-Cluster unter Azure gesammelt werden."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="toddabel"/>


# Sammeln von Protokollen aus einem Service Fabric-Cluster mit Azure-Diagnose und Operational Insights

Bei Verwendung eines Azure Service Fabric-Clusters empfiehlt es sich, die Protokolle aller Knoten an einem zentralen Ort zu sammeln. Das Sammeln der Protokolle an einem zentralen Ort vereinfacht die Analyse und Behandlung von Problemen, die ggf. in Ihrem Cluster oder in den Anwendungen und Diensten des Clusters auftreten. Eine Möglichkeit zum Hochladen und Sammeln von Protokollen ist die Verwendung der Erweiterung „Azure-Diagnose“, mit der Protokolle an Azure Storage hochgeladen werden.

Bei Azure Operational Insights (Teil der Microsoft Operations Management Suite) handelt es sich um eine SaaS-Lösung, die das Analysieren und Durchsuchen von Protokollen vereinfacht. In den folgenden Schritten wird beschrieben, wie Sie die Erweiterung „Azure-Diagnose“ auf den VMs in einem Cluster einrichten, um Protokolle an einen zentralen Speicherort hochzuladen. Anschließend erfahren Sie, wie Sie Operational Insights so konfigurieren, dass die Protokolle abgerufen werden und im Operational Insights-Portal angezeigt werden können.

Operational Insights identifiziert die Quellen für die unterschiedlichen Arten von Protokollen, die von einem Service Fabric-Cluster hochgeladen werden, anhand der Namen der Speichertabellen, in denen sie gespeichert sind. Die Erweiterung „Azure-Diagnose“ muss daher so konfiguriert werden, dass die Namen der Speichertabellen, an die die Protokolle hochgeladen werden, den Namen entsprechen, nach denen von Operational Insights gesucht wird. Die Namen der Speichertabellen können Sie den in diesem Dokument enthaltenen Beispielkonfigurationseinstellungen entnehmen.

## Empfohlene Artikel
* [Azure-Diagnose](../cloud-services/cloud-services-dotnet-diagnostics.md) (bezieht sich auf Azure Cloud Services, enthält jedoch hilfreiche Informationen und Beispiele)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)
* [Azure Resource Manager](https://azure.microsoft.com/resource-group-overview/)

## Voraussetzungen
Diese Tools werden verwendet, um einige Vorgänge in diesem Dokument durchzuführen:
* [Azure PowerShell](https://azure.microsoft.com/powershell-install-configure/)
* [Azure Resource Manager-Client](https://github.com/projectkudu/ARMClient)

## Andere Protokollquellen, die gesammelt werden können
1. **Service Fabric-Protokolle:** Werden von der Plattform für standardmäßige ETW- und EventSource-Kanäle ausgegeben. Protokolle können unterschiedlicher Art sein:
  - Betriebsereignisse: Protokolle für Vorgänge, die von der Service Fabric-Plattform durchgeführt werden. Beispiele hierfür wären die Erstellung von Anwendungen und Diensten, Knotenzustandsänderungen und Upgradeinformationen.
  - [Ereignisse des Actor-Programmiermodells](service-fabric-reliable-actors-diagnostics.md)
  - [Ereignisse des Reliable Services-Programmiermodells](service-fabric-reliable-services-diagnostics.md)
2. **Anwendungsereignisse:** Ereignisse, die von Ihrem Dienstcode ausgegeben werden und mit der EventSource-Hilfsklasse der Visual Studio-Vorlagen ausgegeben werden. Weitere Informationen zum Schreiben von Protokollen aus Ihrer Anwendung finden Sie in [diesem Artikel zur Überwachung und Diagnose von Diensten in einer lokalen Installation](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## Bereitstellen der Diagnoseerweiterung für einen Service Fabric-Cluster zum Sammeln und Hochladen von Protokollen
Zum Sammeln von Protokollen muss zunächst die Diagnoseerweiterung auf allen VMs des Service Fabric-Clusters bereitgestellt werden. Die Diagnoseerweiterung sammelt Protokolle auf allen VMs und lädt sie an das angegebene Speicherkonto hoch. Je nachdem, ob Sie das Azure-Portal oder den Azure-Ressourcen-Manager verwenden und ob die Bereitstellung im Rahmen der Clustererstellung oder für einen bereits vorhandenen Cluster erfolgt, variieren die Schritte etwas. Wir sehen uns nun die Schritte für die einzelnen Szenarien an.

### Bereitstellen der Diagnoseerweiterung im Rahmen der Clustererstellung über das Portal
Um die Diagnoseerweiterung im Rahmen der Clustererstellung für die im Cluster enthaltenen VMs bereitzustellen, wird die in der folgenden Abbildung gezeigte Diagnoseeinstellung verwendet. Sie ist standardmäßig aktiviert. ![Azure-Diagnose-Einstellung im Portal für die Clustererstellung](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### Bereitstellen der Diagnoseerweiterung im Rahmen der Clustererstellung mithilfe des Azure-Ressourcen-Managers
Wenn Sie einen Cluster mithilfe des Ressourcen-Managers erstellen möchten, müssen Sie der Ressourcen-Manager-Vorlage vom Typ „Vollständiger Cluster“ vor der Clustererstellung den JSON-Code für die Diagnosekonfiguration hinzufügen. Die Vorlagenbeispiele für den Ressourcen-Manager enthalten eine Beispielvorlage mit hinzugefügter Diagnosekonfiguration für einen Cluster mit fünf VMs. Diese finden Sie im Azure-Beispielkatalog unter [Ressourcen-Manager-Beispielvorlage für einen Cluster mit fünf Knoten und Diagnose](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad).

Wenn Sie sich die Diagnoseeinstellung in der Ressourcen-Manager-Vorlage ansehen möchten, suchen Sie nach **WadCfg**. Klicken Sie zum Erstellen eines Clusters mit dieser Vorlage einfach auf die Schaltfläche **In Azure bereitstellen** (unter dem oben angegebenen Link). Alternativ können Sie das Ressourcen-Manager-Beispiel herunterladen, anpassen und den Befehl `New-AzureResourceGroupDeployment` in einem Azure PowerShell-Fenster ausführen, um einen Cluster mit der geänderten Vorlage zu erstellen. Informationen zu den Parametern, die an den Befehl übergeben werden müssen, finden Sie weiter unten.

Außerdem müssen Sie vor dem Aufrufen dieses Bereitstellungsbefehls unter Umständen einige Setupschritte ausführen. So müssen Sie eventuell Ihr Azure-Konto hinzufügen (`Add-AzureAccount`), ein Abonnement auswählen (`Select-AzureSubscription`), in den Ressourcen-Manager-Modus wechseln (`Switch-AzureMode AzureResourceManager`) und ggf. die Ressourcengruppe erstellen (`New-AzureResourceGroup`).

```powershell

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>Bereitstellen der Diagnoseerweiterung für einen vorhandenen Cluster
Einem bereits vorhandenen Cluster ohne Diagnosebereitstellung können Sie die Diagnose wie folgt hinzufügen: Erstellen Sie mit dem unten angegebenen JSON-Code die beiden Dateien „WadConfigUpdate.json“ und „WadConfigUpdateParams.json“:

##### WadConfigUpdate.json

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",

  "parameters": {
        "vmNamePrefix": {
      "type": "string"
    },
        "applicationDiagnosticsStorageAccountName": {
      "type": "string"
    },
        "vmCount": {
            "type": "int"
    }
  },

  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmNamePrefix'),copyIndex(0),'/Microsoft.Insights.VMDiagnosticsSettings')]",
            "location": "[resourceGroup().location]",
      "properties": {
        "type": "IaaSDiagnostics",
                "typeHandlerVersion": "1.5",
        "publisher": "Microsoft.Azure.Diagnostics",
                "autoUpgradeMinorVersion": true,
        "settings": {
                    "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                "EtwProviders": {
                    "EtwEventSourceProviderConfiguration": [
                        {
                            "provider": "Microsoft-ServiceFabric-Actors",
                            "scheduledTransferKeywordFilter": "1",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableActorEventTable" }
                        },
                        {
                            "provider": "Microsoft-ServiceFabric-Services",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableServiceEventTable" },
                                        "scheduledTransferPeriod": "PT5M"
                        }
                    ],
                    "EtwManifestProviderConfiguration": [
                        {
                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                        "scheduledTransferLogLevelFilter": "Information",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricSystemEventTable" }
                        }
                    ]
                }
            }
    },
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                    "storageAccountEndPoint": "https://core.windows.net/"
                }
            },
            "copy": {
                "name": "vmExtensionLoop",
                "count": "[parameters('vmCount')]"
            }
        }
    ],

    "outputs": {
    }
}
```

##### WadConfigUpdateParams.json
Ersetzen Sie „vmNamePrefix“ durch das Präfix, das Sie beim Erstellen des Clusters für VM-Namen ausgewählt haben. Legen Sie dann „vmStorageAccountName“ auf das Speicherkonto fest, in das Sie die Protokolle der VMs hochladen möchten.

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmNamePrefix": {
            "value": "VM"
        },
        "applicationDiagnosticsStorageAccountName": {
            "value": "testdiagacc"
        },
        "vmCount": {
            "value": 5
        }
    }
}
```

Passen Sie die wie oben beschrieben erstellten JSON-Dateien an die Einzelheiten Ihrer Umgebung an. Rufen Sie dann den folgenden Befehl auf, und übergeben Sie dabei den Namen der Ressourcengruppe für Ihren Service Fabric-Cluster. Nach erfolgreicher Ausführung des Befehls wird die Diagnose auf allen VMs bereitgestellt, und es wird damit begonnen, die Protokolle aus dem Cluster an Tabellen im angegebenen Azure-Speicherkonto hochzuladen.

Vor dem Aufrufen des Bereitstellungsbefehls sind unter Umständen noch einige Setupschritte erforderlich – etwa das Hinzufügen Ihres Azure-Kontos (`Add-AzureAccount`), das Auswählen des richtigen Abonnements (`Select-AzureSubscription`) und das Wechseln in den Resource Manager-Modus (`Switch-AzureMode AzureResourceManager`).

```ps

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## Einrichten von Operational Insights zum Anzeigen und Durchsuchen von Clusterprotokollen
Nachdem die Diagnose im Cluster eingerichtet wurde und Protokolle an ein Speicherkonto hochgeladen werden, muss Operational Insights so eingerichtet werden, dass Sie alle Clusterprotokolle über das Operational Insights-Portal anzeigen, durchsuchen und abfragen können.

### Erstellen eines Operational Insights-Arbeitsbereichs
Die Schritte zum Erstellen eines Operational Insights-Arbeitsbereichs werden im unten angegebenen Artikel beschrieben. Dort werden zwei unterschiedliche Vorgehensweisen für die Arbeitsbereichserstellung beschrieben. Wählen Sie die Methode, die auf der Verwendung von Azure-Portal und Abonnement basiert. Sie benötigen den Namen des Operational Insights-Arbeitsbereichs in den weiteren Abschnitten dieses Dokuments. Erstellen Sie den Operational Insights-Arbeitsbereich mit dem Abonnement, das Sie auch zum Erstellen aller Clusterressourcen (einschließlich Speicherkonten) verwendet haben.

[Operational Insights-Onboarding](https://technet.microsoft.com/library/mt484118.aspx)

### Konfigurieren eines Operational Insights-Arbeitsbereichs zum Anzeigen der Clusterprotokolle
Nachdem Sie den Operational Insights-Arbeitsbereich wie oben beschrieben erstellt haben, muss er so konfiguriert werden, dass die Protokolle aus den Azure-Speichertabellen abgerufen werden, an die sie von der Diagnoseerweiterung aus dem Cluster hochgeladen werden. Diese Konfiguration kann derzeit nicht über das Operational Insights-Portal, sondern nur über PowerShell-Befehle durchgeführt werden. Führen Sie das folgende PowerShell-Skript aus:

```powershell

    <#
    This script will configure an Operations Management Suite workspace (aka Operational Insights workspace) to read Diagnostics from an Azure Storage account.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and Resource Manager storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
    #>

Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace
$storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try
{
    $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop
}
catch [Hyak.Common.CloudException]
{
    # HTTP Not Found is returned if the storage insight doesn't exist
}

if ($existingConfig) {
    Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else {
    if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") {
        Switch-AzureMode -Name AzureServiceManagement
        $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary
        Switch-AzureMode -Name AzureResourceManager
    } else {
        $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1
    }
    New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers
}
```

Sobald Sie den Operational Insights-Arbeitsbereich so konfiguriert haben, dass die Azure-Tabellen in Ihrem Speicherkonto ausgelesen werden, sollten Sie sich beim Portal anmelden und zur Registerkarte **Speicher** für die Operational Insights-Ressource wechseln. Diese sollte in etwa wie folgt aussehen: ![Operational Insights-Speicherkonfiguration im Azure-Portal](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Durchsuchen und Anzeigen von Protokollen in Operational Insights
Nachdem Sie den Operational Insights-Arbeitsbereich für das Auslesen der Daten aus dem angegebenen Speicherkonto konfiguriert haben, kann es bis zu zehn Minuten dauern, bis die Protokolle auf der Benutzeroberfläche von Operational Insights angezeigt werden. Um sich zu vergewissern, dass neue Protokolle generiert werden, empfiehlt es sich, in Ihrem Cluster eine Service Fabric-Anwendung bereitzustellen, da dabei Betriebsereignisse der Service Fabric-Plattform generiert werden.

1. Wählen Sie auf der Hauptseite des Operational Insights-Portals die Option für die Protokollsuche aus, um die Protokolle anzuzeigen. ![Operational Insights – Option für die Protokollsuche](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. Verwenden Sie auf der Seite für die Protokollsuche die Abfrage **Type=ServiceFabricOperationalEvent**. Die Betriebsprotokolle Ihres Clusters sollten wie unten dargestellt angezeigt werden. Verwenden Sie zum Anzeigen aller Protokolle des Actor-Programmiermodells die Abfrage **Type=ServiceFabricReliableActorEvent**. Geben Sie zum Anzeigen aller Protokolle des Reliable Services-Programmiermodells die Abfrage **Type=ServiceFabricReliableServiceEvent** ein. ![Operational Insights – Protokollabfrage und -anzeige](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### Beispielabfragen für die Problembehandlung
Im Anschluss finden Sie einige Beispiele für Szenarien und die dazugehörigen Abfragen für die Problembehandlung.

1. **So ermitteln Sie, ob „RunAsync“ von Service Fabric für einen bestimmten Dienst aufgerufen wurde:** Dies kann hilfreich sein, wenn Sie ausschließen möchten, dass ein Dienst beim Start abstürzt. Verwenden Sie dabei eine Abfrage wie die folgende, ersetzen Sie aber den Dienstnamen und den Anwendungsnamen durch entsprechende Werte für Ihre Bereitstellung, und überprüfen Sie, ob Ergebnisse ausgegeben werden:

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. **So ermitteln Sie, ob ein zustandsbehafteter Dienst Ausnahmen ausgelöst hat, die von Service Fabric als Fehler gekennzeichnet wurden:** Verwenden Sie für die Suche nach entsprechenden Ereignissen eine Abfrage wie die folgende:

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. **So suchen Sie in allen bereitgestellten Anwendungen und Diensten nach Ereignissen für von Actor-Methoden ausgelöste Ausnahmen:** Verwenden Sie in diesem Fall eine Abfrage wie die folgende:

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## Aktualisieren der Diagnose zum Sammeln und Hochladen von Protokollen aus neuen EventSource-Kanälen
Wenn Sie die Diagnose für das Sammeln von Protokollen aus neuen EventSource-Kanälen aktualisieren möchten, die eine neu bereitzustellende Anwendung darstellen, müssen Sie einfach nur die gleichen Schritte wie im [obigen Abschnitt](#deploywadarm) ausführen, in denen das Einrichten der Diagnose für einen vorhandenen Cluster beschrieben wird.

Sie müssen den EtwEventSourceProviderConfiguration-Abschnitt in der Datei „WadConfigUpdate.json“ aktualisieren, um Einträge für die neuen EventSource-Elemente hinzuzufügen, bevor Sie das Konfigurationsupdate über den Ressourcen-Manager-Befehl anwenden. Die Uploadtabelle ist identisch (ETWEventTable). Dies ist die Tabelle, die für Operational Insights zum Auslesen von ETW-Anwendungsereignissen konfiguriert wurde.


## Nächste Schritte
Sehen Sie sich die Diagnoseereignisse an, die für [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) und [Reliable Services](service-fabric-reliable-services-diagnostics.md) ausgegeben werden, um besser zu verstehen, welche Ereignisse Sie beim Behandeln von Problemen untersuchen sollten.

<!---HONumber=AcomDC_0309_2016-->