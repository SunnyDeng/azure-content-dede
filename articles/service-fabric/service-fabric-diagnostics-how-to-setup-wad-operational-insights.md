<properties
   pageTitle="Sammeln von Protokollen mit Microsoft Azure-Diagnose und Operational Insights | Microsoft Azure"
   description="In diesem Artikel wird beschrieben, wie Sie Microsoft Azure-Diagnose und Operational Insights so konfigurieren können, dass Protokolle von einem Service Fabric-Cluster unter Azure gesammelt werden."
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
   ms.date="10/20/2015"
   ms.author="kunalds"/>


# Sammeln von Protokollen eines Service Fabric-Clusters in Azure per Microsoft Azure-Diagnose und Operational Insights

Beim Ausführen eines Service Fabric-Clusters in Azure ist es ratsam, die Protokolle aller Knoten an einem zentralen Speicherort zu sammeln. Das Vorhalten der Protokolle an einem zentralen Ort vereinfacht die Analyse und Behandlung von Problemen, die in Ihrem Cluster oder in den Anwendungen und Diensten des Clusters unter Umständen auftreten. Eine Möglichkeit zum Hochladen und Sammeln von Protokollen ist die Verwendung der Erweiterung „Microsoft Azure-Diagnose“, mit der Protokolle in den Azure-Tabellenspeicher hochgeladen werden. Bei Operational Insights (Teil der Microsoft Operations Management Suite) handelt es sich um eine SaaS-Lösung, mit der das Analysieren und Durchsuchen von Protokollen vereinfacht wird. In den folgenden Schritten wird beschrieben, wie Sie die Microsoft Azure-Diagnose auf den virtuellen Computern im Cluster einrichten können, um Protokolle in einen zentralen Speicher hochzuladen. Es wird auch beschrieben, wie Sie Operational Insights konfigurieren, um die Protokolle so zu sammeln, dass Sie sie im Operational Insights-Portal anzeigen können. Operational Insights identifiziert die Quellen der unterschiedlichen Arten von Protokollen, die von einem Service Fabric-Cluster hochgeladen werden, anhand der Namen der Azure-Speichertabellen, in denen sie gespeichert sind. Microsoft Azure-Diagnose muss also so konfiguriert werden, dass die Protokolle mit den Namen in die Azure-Speichertabellen hochgeladen werden, die mit den Angaben übereinstimmen, nach denen Operational Insights sucht. Die Beispiele zu den Konfigurationseinstellungen in diesem Dokument verdeutlichen, wie die Namen der Speichertabellen lauten sollten.

## Empfohlene Artikel
* [Microsoft Azure-Diagnose](../cloud-services/cloud-services-dotnet-diagnostics.md) (bezieht sich auf Clouddienste, aber enthält nützliche Informationen und Beispiele)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)
* [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-overview/)

## Voraussetzungen
Diese Tools werden verwendet, um einige Vorgänge in diesem Dokument durchzuführen: * [Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) * [ARM Client](https://github.com/projectkudu/ARMClient)

## Andere Protokollquellen, die gesammelt werden können
1. Service Fabric-Protokolle: Werden von der Plattform für standardmäßige ETW- und EventSource-Kanäle ausgegeben. Sie können die folgenden Typen aufweisen:
  - Betriebsereignisse: Dies sind Protokolle für Vorgänge, die von der Service Fabric-Plattform durchgeführt werden. Beispiele hierfür sind die Erstellung von Anwendungen und Diensten, Node-Zustandsänderungen und Upgradeinformationen.
  - [Ereignisse des Actor-Programmiermodells](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-actors-diagnostics/)
  - [Ereignisse des Reliable Services-Programmiermodells](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-services-diagnostics/)
2. Anwendungsereignisse: Dies sind die Ereignisse, die von Ihrem Dienstcode ausgegeben werden und mit der EventSource-Hilfsklasse der Visual Studio-Vorlagen geschrieben werden. Weitere Informationen zum Schreiben von Protokollen aus Ihrer Anwendung finden Sie in [diesem Artikel](https://azure.microsoft.com/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/).


## Bereitstellen von Microsoft Azure-Diagnose für einen Service Fabric-Cluster zum Sammeln und Hochladen von Protokollen
Der erste Schritt zum Sammeln von Protokollen ist das Bereitstellen der Erweiterung „Microsoft Azure-Diagnose“ auf allen virtuellen Computern im Service Fabric-Cluster. Microsoft Azure-Diagnose sammelt Protokolle auf allen virtuellen Computern und lädt sie in das von Ihnen angegebene Speicherkonto hoch. Die Schritte können leicht variieren. Sie richten sich danach, ob Sie das Portal oder ARM verwenden und ob die Bereitstellung im Rahmen der Clustererstellung oder für ein bereits vorhandenes Cluster erfolgt. Wir sehen uns nun die Schritte für die einzelnen Szenarien an.

### Bereitstellen von Microsoft Azure-Diagnose im Rahmen der Clustererstellung über das Portal
Zum Bereitstellen von Microsoft Azure-Diagnose für die virtuellen Computer im Cluster im Rahmen der Clustererstellung wird die Diagnoseeinstellung verwendet, die in der Abbildung unten zu sehen ist. Sie ist standardmäßig aktiviert. ![Microsoft Azure-Diagnose-Einstellung im Portal für die Clustererstellung](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### Bereitstellen von Microsoft Azure-Diagnose im Rahmen der Clustererstellung mit ARM
Zum Erstellen eines Clusters mit ARM müssen Sie das JSON-Element für die Konfiguration von Microsoft Azure-Diagnose der ARM-Vorlage vom Typ „Vollständiger Cluster“ hinzufügen, bevor Sie den Cluster erstellen. Wir stellen für Sie eine ARM-Beispielvorlage für ein Cluster mit fünf virtuellen Computern und hinzugefügter Microsoft Azure-Diagnose-Konfiguration bereit. Sie finden Sie im Katalog mit den Azure-Beispielen unter [ARM-Beispielvorlage für ein Cluster mit fünf Knoten](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad).

Suchen Sie nach „WadCfg“, um die Microsoft Azure-Diagnose-Einstellung in der ARM-Vorlage anzuzeigen. Klicken Sie zum Erstellen eines Clusters mit dieser Vorlage einfach auf die Schaltfläche „In Azure bereitstellen“, die unter dem oben angegebenen Link verfügbar ist. Alternativ dazu können Sie das ARM-Beispiel herunterladen, Änderungen daran vornehmen und einen Cluster mit der geänderten Vorlage erstellen, indem Sie den Befehl `New-AzureResourceGroupDeployment` in einem Azure Powershell-Fenster verwenden. Unten sind die Parameter angegeben, die Sie an den Befehl übergeben müssen. Außerdem müssen Sie vor dem Aufrufen dieses Bereitstellungsbefehls unter Umständen einige Setupschritte ausführen, z. B. das Hinzufügen Ihres Azure-Kontos (`Add-AzureAccount`), Auswählen eines Abonnements (`Select-AzureSubscription`), Wechseln in den ARM-Modus (`Switch-AzureMode AzureResourceManager`) und Erstellen der Ressourcengruppe, falls Sie dies noch nicht getan haben (`New-AzureResourceGroup`).

```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>Bereitstellen von Microsoft Azure-Diagnose für einen vorhandenen Cluster
Wenn Sie über einen vorhandenen Cluster verfügen, für den Microsoft Azure-Diagnose nicht bereitgestellt wurde, können Sie Microsoft Azure-Diagnose mit den unten angegebenen Schritten hinzufügen. Erstellen Sie mit dem unten angegebenen JSON-Code die beiden Dateien „WadConfigUpdate.json“ und „WadConfigUpdateParams.json“.

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
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountNamee')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountNamee')]",
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
Ersetzen Sie vmNamePrefix durch das Präfix, das Sie beim Erstellen des Clusters für die Namen der virtuellen Computer ausgewählt haben. Ändern Sie vmStorageAccountName in das Speicherkonto, in das Sie die Protokolle der virtuellen Computer hochladen möchten. ```json
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
``` Nachdem Sie die JSON-Dateien wie oben beschrieben erstellt und an die besonderen Anforderungen Ihrer Umgebung angepasst haben, können Sie diesen Befehl aufrufen und den Namen der Ressourcengruppe für Ihren Service Fabric-Cluster übergeben. Nach der erfolgreichen Ausführung dieses Befehls wird Microsoft Azure-Diagnose auf allen virtuellen Computern bereitgestellt. Es wird damit begonnen, die Protokolle aus dem Cluster in die Tabellen im angegebenen Azure-Speicherkonto hochzuladen. Außerdem müssen Sie vor dem Aufrufen dieses Bereitstellungsbefehls unter Umständen einige Setupschritte ausführen, z. B. das Hinzufügen Ihres Azure-Kontos (`Add-AzureAccount`), Auswählen des richtigen Abonnements (`Select-AzureSubscription`) und Wechseln in den ARM-Modus (`Switch-AzureMode AzureResourceManager`). ```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## Einrichten von Operational Insights zum Anzeigen und Durchsuchen von Clusterprotokollen
Nachdem Microsoft Azure-Diagnose im Cluster eingerichtet wurde und Protokolle in ein Speicherkonto hochgeladen werden, ist der nächste Schritt die Einrichtung von Operational Insights. Das Ziel ist, dass Sie alle Clusterprotokolle über die Portal-UI von Operational Insights anzeigen, durchsuchen und abfragen können.

### Erstellen eines Operational Insights-Arbeitsbereichs
Die Schritte zum Erstellen eines Operational Insights-Arbeitsbereichs sind im unten angegebenen Artikel enthalten. Darin werden zwei unterschiedliche Ansätze zum Erstellen eines Arbeitsbereichs beschrieben. Wählen Sie den Ansatz mit dem Azure-Portal und dem Abonnement. Sie benötigen den Namen des Operational Insights-Arbeitsbereichs in den weiteren Abschnitten dieses Dokuments. Erstellen Sie den Operational Insights-Arbeitsbereich mit demselben Abonnement, das Sie zum Erstellen aller Clusterressourcen verwendet haben, einschließlich der Speicherkonten.

[Operational Insights-Onboarding](https://technet.microsoft.com/library/mt484118.aspx)

### Konfigurieren des Operational Insights-Arbeitsbereichs zum Anzeigen der Clusterprotokolle
Nachdem Sie den Operational Insights-Arbeitsbereich wie oben beschrieben erstellt haben, ist der nächste Schritt das Konfigurieren des Arbeitsbereichs. Das Ziel ist, die Protokolle aus den Azure-Tabellen abzurufen, in die sie vom Cluster über Microsoft Azure-Diagnose hochgeladen werden. Derzeit ist das Durchführen dieser Konfiguration über das Operational Insights-Portal nicht möglich, sondern nur mit PowerShell-Befehlen. Führen Sie dieses PS-Skript aus. ```powershell <# Mit diesem Skript wird ein Operations Management Suite-Arbeitsbereich (also Operational Insights-Arbeitsbereich) konfiguriert, um Microsoft Azure Diagnostics-Daten aus einem Azure-Speicherkonto auszulesen.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and ARM storage accounts.

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

$workspace = Select-Workspace $storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try { $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop } catch [Hyak.Common.CloudException] { # HTTP Not Found wird zurückgegeben, wenn die Speichereinsicht nicht vorhanden ist }

if ($existingConfig) { Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else { if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") { Switch-AzureMode -Name AzureServiceManagement $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary Switch-AzureMode -Name AzureResourceManager } else { $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1 } New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers } ``` Nachdem Sie den Operational Insights-Arbeitsbereich für das Auslesen der Daten aus Ihrem Speicherkonto konfiguriert haben, sollten Sie sich am Azure-Portal anmelden und die Registerkarte „Speicher“ für die Operational Insights-Ressource aufrufen. Es sollte in etwa Folgendes angezeigt werden: ![Operational Insights-Speicherkonfiguration im Azure-Portal](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Durchsuchen und Anzeigen von Protokollen in Operational Insights
Nachdem Sie den Operational Insights-Arbeitsbereich für das Auslesen der Daten aus dem angegebenen Speicherkonto konfiguriert haben, kann es bis zu zehn Minuten dauern, bis die Protokolle in der Benutzeroberfläche von Operational Insights angezeigt werden. Um sicherzustellen, dass neue Protokolle generiert werden, sollten Sie eine Service Fabric-Anwendung in Ihrem Cluster bereitstellen. Hierüber werden Betriebsereignisse über die Service Fabric-Plattform generiert.

1. Wählen Sie zum Anzeigen der Protokolle auf der Hauptseite des Operational Insights-Portals „LogSearch“ aus. ![Operational Insights – Option für Protokollsuche](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. Verwenden Sie auf der Seite zum Durchsuchen von Protokollen die Abfrage „Type=ServiceFabricOperationalEvent“. Die Betriebsprotokolle Ihres Clusters sollten wie unten dargestellt angezeigt werden. Um alle Protokolle für das Actor-Programmiermodell anzuzeigen, verwenden Sie die Abfrage „Type=ServiceFabricReliableActorEvent“, und zum Anzeigen aller Protokolle des Reliable Services-Programmiermodells geben Sie „Type=ServiceFabricReliableServiceEvent“ ein. ![Operational Insights – Protokollabfrage und -anzeige](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### Beispielabfragen für die Problembehandlung
Hier sind Beispiele für einige Szenarien und die dazugehörigen Abfragen für die Problembehandlung angegeben.

1. Ermitteln, ob RunAsync für einen bestimmten Dienst von Service Fabric aufgerufen wurde. Dies kann hilfreich sein, wenn Sie ausschließen müssen, dass ein Dienst beim Starten abstürzt. Verwenden Sie für diese Suche eine Abfrage, die der unten angegebenen Abfrage ähnelt. Ersetzen Sie den Dienstnamen und den Anwendungsnamen gemäß den Angaben für Ihre Bereitstellung, und testen Sie, ob Ergebnisse ausgegeben werden:

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. Wenn Sie einen zustandsbehafteten Dienst ausführen und testen möchten, ob darüber Ausnahmen ausgelöst wurden, die von Service Fabric als Fehler gekennzeichnet wurden, finden Sie diese Ereignisse mit einer Abfrage der folgenden Art:

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. Zum Suchen nach Ereignissen, die von Actor-Methoden ausgelösten Ausnahmen entsprechen, in allen bereitgestellten Anwendungen und Diensten können Sie eine Abfrage der folgenden Art verwenden:

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## Aktualisieren von Microsoft Azure-Diagnose zum Sammeln und Hochladen von Protokollen aus neuen EventSource-Kanälen
Wenn Sie Microsoft Azure-Diagnose für das Sammeln von Protokollen über einen neuen EventSource-Kanal aktualisieren möchten, der für eine neue bereitzustellende Anwendung steht, müssen Sie nur die gleichen Schritte wie im [obigen Abschnitt](#deploywadarm) ausführen. Darin wird das Einrichten von Microsoft Azure-Diagnose für einen vorhandenen Cluster beschrieben. Sie müssen den EtwEventSourceProviderConfiguration-Abschnitt in der Datei „WadConfigUpdate.json“ aktualisieren, um Einträge für die neuen EventSource-Elemente hinzuzufügen, bevor Sie das Konfigurationsupdate über den ARM-Befehl anwenden. Die Tabelle für das Hochladen ist identisch (ETWEventTable). Dies ist die Tabelle, die für Operational Insights zum Auslesen von ETW-Anwendungsereignissen konfiguriert wurde.


## Nächste Schritte
Sehen Sie sich die Diagnoseereignisse an, die für [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) und [Reliable Services](service-fabric-reliable-services-diagnostics.md) ausgegeben werden, um besser zu verstehen, welche Ereignisse Sie beim Behandeln von Problemen untersuchen sollten.

<!---HONumber=Nov15_HO4-->