<properties
	pageTitle="Anpassen von HDInsight-Clustern mit Skriptaktionen | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit Skriptaktionen benutzerdefinierte Komponenten zu Linux-basierten HDInsight-Clustern hinzufügen. Skriptaktionen sind Bash-Skripts, die während der Clustererstellung ausgeführt werden. Sie können zum Anpassen der Clusterkonfiguration oder zum Hinzufügen zusätzlicher Dienste und Dienstprogramme wie Hue, Solr oder R verwenden werden."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="larryfr"/>

# Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen

HDInsight bietet die Konfigurationsoption **Skriptaktion**, die benutzerdefinierte Skripts aufruft, mit denen die auf den Cluster anzuwendende Anpassung während des Erstellungsvorgangs festgelegt wird. Sie können diese Skripts verwenden, um zusätzliche Software auf einem Cluster zu installieren oder die Konfiguration von Anwendungen auf einem Cluster zu ändern.

> [AZURE.NOTE] Die Informationen in diesem Artikel gelten für Linux-basierte HDInsight-Cluster. Eine Version dieses Artikels mit spezifischen Informationen zu Windows-basierten Clustern finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen (Windows)](hdinsight-hadoop-customize-cluster.md).

## Skriptaktionen im Clustererstellungsvorgang

Script Action wird nur verwendet, während ein Cluster erstellt wird. Das folgende Diagramm veranschaulicht, wann Skriptaktionen während des Erstellungsvorgangs ausgeführt werden:

![HDInsight-Clusteranpassung und Phasen während der Clustererstellung][img-hdi-cluster-states]

Das Skript wird ausgeführt, während HDInsight konfiguriert wird. In dieser Phase wird das Skript parallel auf allen angegebenen Knoten im Cluster ausgeführt. Die Ausführung erfolgt dabei mit Stammberechtigungen für die Knoten.

> [AZURE.NOTE] Da Sie über Stammberechtigungen für die Clusterknoten verfügen, wenn das Skript ausgeführt wird, können Sie Vorgänge wie z. B. das Beenden und Starten von Diensten, einschließlich Hadoop-bezogener Dienste, ausführen. Wenn Sie Dienste beenden, müssen Sie sicherstellen, dass der Ambari-Dienst und andere Hadoop-bezogene Dienste in Betrieb sind, ehe die Ausführung des Skripts beendet wird. Diese Dienste werden benötigt, um die Integrität und den Status des Clusters erfolgreich sicherzustellen, während dieser erstellt wird.

Jeder Cluster unterstützt mehrere Skriptaktionen, die in der angegebenen Reihenfolge aufgerufen werden. Ein Skript kann auf den Hauptknoten und/oder den Workerknoten ausgeführt werden.

> [AZURE.IMPORTANT] Skriptaktionen müssen innerhalb von 60 Minuten abgeschlossen sein, andernfalls tritt ein Timeout ein. Während der Knotenbereitstellung wird das Skript gleichzeitig mit anderen Einrichtungs- und Konfigurationsprozessen ausgeführt. Der Wettbewerb um Ressourcen wie CPU-Zeit oder Netzwerkbandbreite kann dazu führen, dass es länger als in Ihrer Entwicklungsumgebung dauert, bis das Skript abgeschlossen ist.
> 
> Um die Ausführungsdauer des Skripts zu minimieren, vermeiden Sie Aufgaben wie das Herunterladen und Kompilieren von Anwendungen aus der Quelle. Führen Sie stattdessen eine Vorkompilierung der Anwendung durch, und speichern Sie die binäre Version im Azure-BLOB-Speicher, damit sie schnell in den Cluster heruntergeladen werden kann.


## Script Action-Beispielskripts

Skriptaktionskripts können über das Azure-Portal, Azure PowerShell oder das HDInsight .NET SDK genutzt werden. Dieser Artikel beschreibt, wie die Skriptaktion im Portal verwendet wird. Sehen Sie sich die Beispiele in der folgenden Tabelle an, um zu erfahren, wie Sie PowerShell und das .NET SDK für die Verwendung der Skriptaktion nutzen.

HDInsight verfügt über mehrere Skripts zum Installieren der folgenden Komponenten auf HDInsight-Clustern:

Name | Skript
----- | -----
**Installieren von Hue** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. Siehe [Installieren und Verwenden von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md).
**Installieren von Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh. Siehe [Installieren und Verwenden von Spark in HDInsight-Clustern](hdinsight-hadoop-spark-install-linux.md).
**Installieren von R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Siehe [Installieren und Verwenden von R in HDInsight-Clustern](hdinsight-hadoop-r-scripts-linux.md).
**Installieren von Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Siehe [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install-linux.md).
**Installieren von Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Siehe [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install-linux.md).
| **Vorabladen von Hive-Bibliotheken** | https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Siehe [Hinzufügen von Hive-Bibliotheken zu HDInsight-Clustern](hdinsight-hadoop-add-hive-libraries.md). |

## Verwenden einer Skriptaktion über das Azure-Portal

1. Beginnen Sie mit dem Erstellen eines Clusters, wie unter [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md#portal) beschrieben.

2. Klicken Sie unter __Optionale Konfiguration__ auf dem Blatt **Skriptaktionen** auf **Skriptaktion hinzufügen**, um wie folgt Details zur Skriptaktion anzugeben:

	![Anpassen eines Clusters mit "Skriptaktion"](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

	| Eigenschaft | Wert |
	| -------- | ----- |
	| Name | Geben Sie einen Namen für die Skriptaktion an. |
	| Skript-URI | Geben Sie den URI für das Skript an, das aufgerufen wird, um den Cluster anzupassen. |
	| Haupt-/Workerknoten | Geben Sie die Knoten (**Hauptknoten**, **Workerknoten** oder **ZooKeeper**) an, auf denen das Anpassungsskript ausgeführt wird. |
	| Parameter | Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist. |

	Drücken Sie die EINGABETASTE, um dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzuzufügen.

3. Klicken Sie auf **Auswählen**, um die Skriptaktionskonfiguration zu speichern und die Clustererstellung fortzusetzen.

## Verwenden einer Skriptaktion über Azure-Ressourcen-Manager-Vorlagen

In diesem Abschnitt werden Azure-Ressourcen-Manager (ARM)-Vorlagen verwendet, um einen HDInsight-Cluster zu erstellen. Zudem werden mithilfe einer Skriptaktion benutzerdefinierte Komponenten (in diesem Beispiel R) auf dem Cluster installiert. In diesem Abschnitt wird eine ARM-Beispielvorlage bereitgestellt, um einen Cluster mithilfe von Skriptaktionen zu erstellen.

### Voraussetzungen

* Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight Powershell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure-PowerShell](../powershell-install-configure.md).
* Anweisungen zum Erstellen von ARM-Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).
* Wenn Sie Azure PowerShell noch nicht mit dem Ressourcen-Manager verwendet haben, finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md) weitere Informationen.

### Erstellen von Clustern mithilfe von Skriptaktionen

1. Kopieren Sie die folgende Vorlage in einen Speicherort auf Ihrem Computer. Mit dieser Vorlage wird R auf dem Hauptknoten sowie auf den Workerknoten im Cluster installiert. Sie können zudem überprüfen, ob die JSON-Vorlage gültig ist. Fügen Sie den Vorlageninhalt in [JSONLint](http://jsonlint.com/) (JSON-Onlineüberprüfungstool) ein.

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "username"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",
		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}

2. Starten Sie Azure PowerShell, und melden Sie sich bei Ihrem Azure-Konto an. Nach der Eingabe Ihrer Anmeldeinformationen gibt der Befehl die Informationen zu Ihrem Konto zurück.

		Add-AzureRmAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Wenn Sie über mehrere Abonnements verfügen, geben Sie die Abonnement-ID ein, die Sie für die Bereitstellung verwenden möchten.

		Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Sie können `Get-AzureRmSubscription` zum Abrufen einer Liste aller Ihrem Konto zugeordneten Abonnements verwenden, einschließlich der Abonnement-ID jedes Abonnements.

5. Erstellen Sie eine neue Ressourcengruppe, wenn noch keine vorhanden ist. Geben Sie den Namen der Ressourcengruppe und des gewünschten Speicherorts ein. Es wird eine Zusammenfassung der neuen Ressourcengruppe zurückgegeben.

		New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Führen Sie zum Erstellen einer neuen Bereitstellung für die Ressourcengruppe den Befehl **New-AzureRmResourceGroupDeployment** aus, und geben Sie die erforderlichen Parameter ein. Die Parameter enthalten den Namen der Bereitstellung, den Namen der Ressourcengruppe und den Pfad oder die URL der erstellten Vorlage. Wenn Ihre Vorlage Parameter erfordert, müssen Sie auch diese Parameter übergeben. In diesem Fall sind für die Skriptaktion zum Installieren von R auf dem Cluster keine Parameter erforderlich.


		New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Sie werden aufgefordert, Werte für die in der Vorlage definierten Parameter anzugeben.

7. Wenn die Ressourcengruppe bereitgestellt wurde, wird eine Zusammenfassung der Bereitstellung angezeigt.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. Wenn die Bereitstellung fehlschlägt, können Sie mithilfe der folgenden Cmdlets Informationen zu den Fehlern abrufen.

		Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

## Verwenden einer Skriptaktion in Azure PowerShell

In diesem Abschnitt verwenden wir das Cmdlet [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx), um Skripts mithilfe von „Skriptaktion“ zum Anpassen eines Clusters aufzurufen. Stellen Sie vor dem Fortfahren sicher, dass Azure PowerShell installiert und konfiguriert ist. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

Führen Sie die folgenden Schritte aus:

1. Öffnen Sie die Azure PowerShell-Konsole, und deklarieren Sie die folgenden Variablen:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionId = "<SubscriptionId>"		# ID of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. Legen Sie die Konfigurationswerte fest, z. B. Knoten im Cluster und den zu verwendenden Standardspeicher.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureRmSubscription -SubscriptionId $subscriptionId
		$config = New-AzureRmHDInsightClusterConfig
		$config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccountKey=$storageAccountKey

3. Rufen Sie das Skript mithilfe des Cmdlets **Add-AzureRmHDInsightScriptAction** auf. Im folgenden Beispiel wird ein Skript zum Installieren von R auf dem Cluster verwendet:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
		$config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

	Das Cmdlet **Add-AzureRmHDInsightScriptAction** verarbeitet die folgenden Parameter:

	| Parameter | Definition |
	| --------- | ---------- |
	| Konfiguration | Konfigurationsobjekt, dem Skriptaktionsinformationen hinzugefügt werden. |
	| Name | Name der Skriptaktion. |
	| NodeType | Gibt den Knoten an, auf dem das Anpassungsskript ausgeführt wird. Die gültigen Werte lauten **HeadNode** (für die Installation auf dem Hauptknoten), **WorkerNode** (für die Installation auf allen Datenknoten) oder **ZookeeperNode** (für die Installation auf dem Zookeeper-Knoten). |
	| Parameter | Vom Skript benötigte Parameter. |
	| Uri | Gibt den URI des auszuführenden Skripts an. |

4. Legen Sie den Administrator/HTTPS-Benutzer für den Cluster fest:

        $httpCreds = get-credential
        
    Geben Sie nach Aufforderung „admin“ als Namen und ein Kennwort ein.

5. Legen Sie die SSH-Anmeldeinformationen fest:

        $sshCreds = get-credential
    
    Geben Sie nach Aufforderung den SSH-Benutzernamen und das Kennwort ein. Wenn Sie das SSH-Konto mit einem Zertifikat anstatt mit einem Kennworts schützen möchten, verwenden Sie ein leeres Kennwort, und legen Sie `$sshPublicKey` auf den Inhalt des öffentlichen Schlüssels des Zertifikats fest, der verwendet werden soll. Beispiel:
    
        $sshPublicKey = Get-Content .\path\to\public.key -Raw
    
4. Erstellen Sie schließlich den Cluster:
        
        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux
    
    Wenn Sie einen öffentlichen Schlüssel zum Schutz Ihres SSH-Kontos verwenden, müssen Sie auch `-SshPublicKey $sshPublicKey` als Parameter angeben.

Die Erstellung des Clusters kann einige Minuten in Anspruch nehmen.

## Verwenden einer Skriptaktion über das HDInsight .NET SDK

Das HDInsight .NET SDK enthält Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen. Ein Codebeispiel finden Sie unter [Erstellen von Linux-basierten Clustern in HDInsight mit dem .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).


## Problembehandlung

Über die Ambari-Webbenutzeroberfläche können Sie Informationen anzeigen, die von Skripts während der Erstellung des Clusters protokolliert wurden. Wenn die Erstellung des Clusters aufgrund eines Fehlers im Skript fehlgeschlagen ist, sind die Protokolle auch im Standardspeicherkonto verfügbar, das dem Cluster zugeordnet ist. Dieser Abschnitt enthält Informationen zum Abrufen der Protokolle mit den folgenden zwei Optionen:

### Mithilfe der Ambari-Webbenutzeroberfläche

1. Navigieren Sie in Ihrem Browser zu https://CLUSTERNAME.azurehdinsight.net. Ersetzen Sie CLUSTERNAME durch den Namen Ihres HDInsight-Clusters.

	Geben Sie bei der entsprechenden Aufforderung den Namen (admin) und das Kennwort des Administratorkontos für den Cluster ein. Möglicherweise müssen Sie die Administratoranmeldeinformationen in einem Webformular erneut eingeben.

2. Wählen Sie in der Leiste oben auf der Seite die Option __ops__ aus. Dadurch wird eine Liste der aktuellen und vorherigen Vorgänge angezeigt, die über Ambari auf dem Cluster durchgeführt wurden.

	![Ambari-Webbenutzeroberfläche mit ausgewählter Option "ops"](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Suchen Sie die Einträge, die __run\_customscriptaction__ in der Spalte __Vorgänge__ enthalten. Diese werden erstellt, wenn die Skriptaktionen ausgeführt werden.

	![Screenshot von Vorgängen](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Wählen Sie diesen Eintrag aus, und führen Sie einen Drilldown durch die Links aus, um die Ausgabe für STDOUT und STDERR anzuzeigen, die beim Ausführen des Skripts auf dem Cluster generiert wurde.

### Rufen Sie Protokolle über das Standardspeicherkonto auf.

Wenn die Erstellung des Clusters aufgrund eines Fehlers der Skriptaktion fehlgeschlagen ist, kann auf die Skriptaktionsprotokolle weiterhin direkt über das Standardspeicherkonto zugegriffen werden, das dem Cluster zugeordnet ist.

* Die Speicherprotokolle stehen unter `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` zur Verfügung. 

	![Screenshot von Vorgängen](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

	Hier sind die Protokolle separat für Hauptknoten, Workerknoten und zookeeper-Knoten aufgeführt. Beispiele hierfür sind: * **Headnode** – `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net` * **Worker-Knoten** – `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net` * **Zookeeper-Knoten** – `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Alle stdout- und stderr-Elemente des entsprechenden Hosts werden in das Speicherkonto hochgeladen. Für jede Skriptaktion liegen die Dateien **output-*.txt** und **errors-*.txt** vor. Die Datei „output-*.txt“ enthält Informationen zum URI des Skripts, das auf dem Host ausgeführt wurde. Beispiel:

		'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Es kann sein, dass Sie wiederholt eine Skriptaktion mit demselben Namen erstellen. In diesem Fall können Sie die relevanten Protokolle anhand des Datums im Ordnernamen unterscheiden. Die Ordnerstruktur für einen an verschiedenen Tagen erstellten Cluster (mycluster) lautet beispielsweise wie folgt:
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Wenn Sie am gleichen Tag einen Skriptaktionscluster mit demselben Namen erstellen, können Sie die relevanten Protokolldateien anhand des eindeutigen Präfixes ermitteln.

* Wenn Sie am Ende des Tages einen Cluster erstellen, umfassen die Protokolldateien unter Umständen zwei Tage. In diesem Fall sehen Sie für den gleichen Cluster zwei Ordner mit unterschiedlichen Datumsangaben.

* Das Hochladen von Protokolldateien in den Standardcontainer kann, insbesondere bei großen Clustern, bis zu 5 Minuten dauern. Wenn Sie also auf die Protokolle zugreifen möchten, sollten Sie nicht sofort den Cluster löschen, falls eine Skriptaktion fehlschlägt.


## Unterstützung für Open-Source-Software in HDInsight-Clustern

Der Microsoft Azure HDInsight-Dienst ist eine flexible Plattform, die es Ihnen ermöglicht, Big Data-Anwendungen in der Cloud innerhalb des Ökosystems der Open-Source-Technologien rund um Hadoop zu erstellen. Microsoft Azure bietet allgemeinen Support für Open Source-Technologien. Informationen finden Sie im Abschnitt **Supportumfang** auf der [FAQ-Website zum Azure-Support](https://azure.microsoft.com/support/faq/). Der HDInsight-Dienst bietet, wie nachstehend beschrieben, zusätzliche Unterstützung für einige der Komponenten.

Es gibt zwei Arten von Open-Source-Komponenten, die im HDInsight-Dienst verfügbar sind:

- **Integrierte Komponenten** – Diese Komponenten sind in HDInsight-Clustern vorinstalliert und stellen Kernfunktionen des Clusters bereit. So gehören beispielsweise Yarn Resource Manager, die Hive-Abfragesprache (HiveQL) und die Mahout Library zu dieser Kategorie. Eine vollständige Liste der Clusterkomponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen](hdinsight-component-versioning.md).

- **Benutzerdefinierte Komponenten** – Als Benutzer des Clusters können Sie in Ihrem Workload eine beliebige in der Community verfügbare oder von Ihnen erstellte Komponente installieren oder verwenden.

> [AZURE.WARNING] Komponenten, die mit dem HDInsight-Cluster bereitgestellt werden, werden vollständig unterstützt, und Microsoft Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.
>
> Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Auf diese Weise kann das Problem behoben werden, ODER Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. So können z. B. viele Communitywebsites verwendet werden, wie: das [MSDN-Forum für HDInsight](https://social.msdn.microsoft.com/Forums/azure/de-DE/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Für Apache-Projekte gibt es Projektwebsites auf [http://apache.org](http://apache.org), zum Beispiel [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

Der HDInsight-Dienst bietet mehrere Möglichkeiten, benutzerdefinierte Komponenten zu verwenden. Unabhängig davon, wie die Komponente verwendet wird oder im Cluster installiert ist, gilt der gleiche Supportumfang. Nachfolgend finden Sie eine Liste der am häufigsten genutzten Möglichkeiten für die Verwendung von benutzerdefinierten Komponenten in HDInsight-Clustern:

1. Senden des Auftrags – Hadoop- oder andere Auftragstypen, die benutzerdefinierte Komponenten ausführen oder verwenden, können zum Cluster gesendet werden.

2. Clusteranpassung – Während der Clustererstellung können Sie zusätzliche Einstellungen und benutzerdefinierte Komponenten angeben, die auf den Clusterknoten installiert werden.

3. Beispiele – Für beliebte benutzerdefinierte Komponenten stellen Microsoft und andere Anbieter u. U. Beispiele dafür bereit, wie diese Komponenten in den HDInsight-Clustern verwendet werden können. Für diese Beispiele wird kein Support bereitgestellt.

## Nächste Schritte

Informationen und Beispiele zum Erstellen und Verwenden von Skripts zum Anpassen eines Clusters finden Sie über die folgenden Links:

- [Entwickeln von Script Action-Skripts für HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Installieren und Verwenden von Spark in HDInsight-Clustern](hdinsight-hadoop-spark-install-linux.md)
- [Installieren und Verwenden von R in HDInsight-Clustern](hdinsight-hadoop-r-scripts-linux.md)
- [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install-linux.md)
- [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Phasen während der Clustererstellung"

<!---HONumber=AcomDC_0224_2016-->