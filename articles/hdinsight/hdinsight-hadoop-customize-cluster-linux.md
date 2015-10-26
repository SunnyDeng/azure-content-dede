<properties
	pageTitle="Anpassen von HDInsight-Clustern mit Skriptaktionen | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit Skriptaktionen HDInsight-Cluster anpassen können."
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
	ms.date="10/02/2015"
	ms.author="larryfr"/>

# Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen (Linux)

HDInsight bietet die Konfigurationsoption **Skriptaktion**, die benutzerdefinierte Skripts aufruft, mit denen die auf den Cluster anzuwendende Anpassung während des Erstellungsvorgangs festgelegt wird. Sie können diese Skripts verwenden, um zusätzliche Software auf einem Cluster zu installieren oder die Konfiguration von Anwendungen auf einem Cluster zu ändern.

> [AZURE.NOTE]Die Informationen in diesem Artikel gelten für Linux-basierte HDInsight-Cluster. Eine Version dieses Artikels mit spezifischen Informationen zu Windows-basierten Clustern finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen (Windows)](hdinsight-hadoop-customize-cluster.md).

## Skriptaktionen im Clustererstellungsvorgang

Script Action wird nur verwendet, während ein Cluster erstellt wird. Das folgende Diagramm veranschaulicht, wann Skriptaktionen während des Erstellungsvorgangs ausgeführt werden:

![HDInsight-Clusteranpassung und Phasen während der Clustererstellung][img-hdi-cluster-states]

Das Skript wird ausgeführt, während HDInsight konfiguriert wird. In dieser Phase wird das Skript parallel auf allen angegebenen Knoten im Cluster ausgeführt. Die Ausführung erfolgt dabei mit Stammberechtigungen für die Knoten.

> [AZURE.NOTE]Da Sie über Stammberechtigungen für die Clusterknoten verfügen, wenn das Skript ausgeführt wird, können Sie Vorgänge wie z. B. das Beenden und Starten von Diensten, einschließlich Hadoop-bezogener Dienste, ausführen. Wenn Sie Dienste beenden, müssen Sie sicherstellen, dass der Ambari-Dienst und andere Hadoop-bezogene Dienste in Betrieb sind, ehe die Ausführung des Skripts beendet wird. Diese Dienste werden benötigt, um die Integrität und den Status des Clusters erfolgreich sicherzustellen, während dieser erstellt wird.

Jeder Cluster unterstützt mehrere Skriptaktionen, die in der angegebenen Reihenfolge aufgerufen werden. Ein Skript kann auf den Hauptknoten und/oder den Workerknoten ausgeführt werden.

> [AZURE.IMPORTANT]Skriptaktionen müssen innerhalb von 15 Minuten abgeschlossen sein, andernfalls werden sie mit Timeout abgebrochen.

## Script Action-Beispielskripts

Skriptaktionsskripts können über das Azure-Vorschauportal, Azure PowerShell oder das HDInsight .NET SDK genutzt werden. Dieser Artikel beschreibt, wie die Skriptaktion im Portal verwendet wird. Sehen Sie sich die Beispiele in der folgenden Tabelle an, um zu erfahren, wie Sie PowerShell und das .NET SDK für die Verwendung der Skriptaktion nutzen.

HDInsight verfügt über mehrere Skripts zum Installieren der folgenden Komponenten auf HDInsight-Clustern:

Name | Skript
----- | -----
**Installieren von Hue** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. Siehe [Installieren und Verwenden von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md).
**Installieren von Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh. Siehe [Installieren und Verwenden von Spark in HDInsight-Clustern](hdinsight-hadoop-spark-install-linux.md).
**Installieren von R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Siehe [Installieren und Verwenden von R in HDInsight-Clustern](hdinsight-hadoop-r-scripts-linux.md).
**Installieren von Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Siehe [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install-linux.md).
**Installieren von Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Siehe [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install-linux.md).

## Verwenden einer Skriptaktion über das Azure-Vorschauportal

1. Beginnen Sie mit dem Erstellen eines Clusters, wie unter [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md#portal) beschrieben.

2. Klicken Sie unter __Optionale Konfiguration__ auf dem Blatt **Skriptaktionen** auf **Skriptaktion hinzufügen**, um Details zur Skriptaktion wie folgt anzugeben:

	![Anpassen eines Clusters mit "Skriptaktion"](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png "Anpassen eines Clusters mit "Skriptaktion"")

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

* Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight Powershell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).
* Anweisungen zum Erstellen von ARM-Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).
* Wenn Sie Azure PowerShell noch nicht mit dem Ressourcen-Manager verwendet haben, finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](powershell-azure-resource-manager) weitere Informationen.

### Erstellen von Clustern mithilfe von Skriptaktionen

1. Kopieren Sie die folgende Vorlage in einen Speicherort auf Ihrem Computer. Mit dieser Vorlage wird R auf dem Hauptknoten sowie auf den Workerknoten im Cluster installiert. Sie können zudem überprüfen, ob die JSON-Vorlage gültig ist. Fügen Sie den Vorlageninhalt in [JSONLint](http://jsonlint.com/) (JSON-Online-Validator-Tool) ein.

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
		            "defaultValue": "hdiuser"
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

		Add-AzureAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Wenn Sie über mehrere Abonnements verfügen, geben Sie die Abonnement-ID ein, die Sie für die Bereitstellung verwenden möchten.

		Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

4. Wechseln Sie zum Azure-Ressourcen-Manager-Modul.

		Switch-AzureMode AzureResourceManager

5. Erstellen Sie eine neue Ressourcengruppe, wenn noch keine vorhanden ist. Geben Sie den Namen der Ressourcengruppe und des gewünschten Speicherorts ein. Es wird eine Zusammenfassung der neuen Ressourcengruppe zurückgegeben.

		New-AzureResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Führen Sie zum Erstellen einer neuen Bereitstellung für die Ressourcengruppe den Befehl **New-AzureResourceGroupDeployment** aus, und geben Sie die erforderlichen Parameter ein. Die Parameter enthalten den Namen der Bereitstellung, den Namen der Ressourcengruppe und den Pfad oder die URL der erstellten Vorlage. Wenn Ihre Vorlage Parameter erfordert, müssen Sie auch diese Parameter übergeben. In diesem Fall sind für die Skriptaktion zum Installieren von R auf dem Cluster keine Parameter erforderlich.


		New-AzureResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Sie werden aufgefordert, Werte für die in der Vorlage definierten Parameter anzugeben.

7. Wenn die Ressourcengruppe bereitgestellt wurde, wird eine Zusammenfassung der Bereitstellung angezeigt.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. Wenn die Bereitstellung fehlschlägt, können Sie mithilfe der folgenden Cmdlets Informationen zu den Fehlern abrufen.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed

	Ausführliche Informationen zu den Fehlern bei der Bereitstellung erhalten Sie mit dem folgenden Cmdlet.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed -DetailedOutput

## Verwenden einer Skriptaktion in Azure PowerShell

In diesem Abschnitt verwenden wir das Cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** zum Aufrufen von Skripts mithilfe von "Skriptaktion" zum Anpassen eines Clusters. Stellen Sie vor dem Fortfahren sicher, dass Azure PowerShell installiert und konfiguriert ist. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

Führen Sie die folgenden Schritte aus:

1. Öffnen Sie die Azure PowerShell-Konsole, und deklarieren Sie die folgenden Variablen:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"

2. Legen Sie die Konfigurationswerte fest, z. B. Knoten im Cluster und den zu verwendenden Standardspeicher.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. Rufen Sie das Skript mithilfe des Cmdlets **Add-AzureHDInsightScriptAction** auf. Im folgenden Beispiel wird das Skript zum Installieren von R auf dem Cluster verwendet:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,WorkerNode,ZookeeperNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh


	Das Cmdlet **Add-AzureHDInsightScriptAction** verwendet die folgenden Parameter:

	| Parameter | Definition |
	| --------- | ---------- |
	| Konfiguration | Konfigurationsobjekt, dem Skriptaktionsinformationen hinzugefügt werden. |
	| Name | Name der Skriptaktion. |
	| ClusterRoleCollection | Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Die gültigen Werte lauten **HeadNode** (für die Installation auf dem Hauptknoten), **WorkerNode** (für die Installation auf allen Datenknoten) oder **ZookeeperNode** (für die Installation auf dem Zookeeper-Knoten). Sie können einen oder alle Werte verwenden. |
	| Parameter | Vom Skript benötigte Parameter. |
	| Uri | Gibt den URI des auszuführenden Skripts an. |

4. Erstellen Sie schließlich den Cluster:

		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Geben Sie die Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden. Die Erstellung des Clusters kann einige Minuten in Anspruch nehmen.

## Verwenden einer Skriptaktion über das HDInsight .NET SDK

Das HDInsight .NET SDK enthält Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen. Mit den folgenden Schritten wird die Verwendung eines Skripts zum Anpassen eines Clusters über das HDInsight .NET SDK veranschaulicht.

> [AZURE.IMPORTANT]Sie müssen zunächst ein selbstsigniertes Zertifikat erstellen und auf Ihrer Arbeitsstation installieren und dann in Ihr Azure-Abonnement hochladen. Weitere Hinweise hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).


### Erstellen eines Visual Studio-Projekts


1. Erstellen Sie eine C#-Konsolenanwendung in Visual Studio.
2. Führen Sie in der Nuget-Paket-Manager-Konsole die folgenden Befehle aus.

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	Diese Befehle installieren .NET-Bibliotheken und fügen Verweise zum aktuellen Visual Studio-Projekt hinzu.

3. Öffnen Sie **Program.cs**, und fügen Sie die folgenden "using"-Anweisungen hinzu:

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

4. Ersetzen Sie den Code in der Klasse durch den folgenden Code:

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
        private const int NewClusterNumNodes = <NUMBER OF NODES>;
        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
        private const string NewClusterVersion = "3.2";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;

        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

        private const string NewClusterUsername = "admin";
        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

        private const string NewClusterSshUserName = "sshuser";
        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
			Comment: ""rsa-key-20150731""
			AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
			gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
			yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
			WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
			pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
			zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
			---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

        private static void Main(string[] args)
        {
            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();
        }

        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }


        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
                SshUserName = NewClusterSshUserName,
        		SshPublicKey = NewClusterSshPublicKey
            };

            ScriptAction rScriptAction = new ScriptAction("Install R",
                new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }
		
6. Ersetzen Sie die Klassenmemberwerte.

7. Drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Sie werden auch zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.


## Unterstützung für Open-Source-Software in HDInsight-Clustern

Der Microsoft Azure HDInsight-Dienst ist eine flexible Plattform, die es Ihnen ermöglicht, Big Data-Anwendungen in der Cloud innerhalb des Ökosystems der Open-Source-Technologien rund um Hadoop zu erstellen. Microsoft Azure bietet allgemeinen Support für Open-Source-Technologien. Siehe den Abschnitt **Supportumfang** auf der Website [Azure-Support-FAQ](http://azure.microsoft.com/support/faq/). Der HDInsight-Dienst bietet, wie nachstehend beschrieben, zusätzliche Unterstützung für einige der Komponenten.

Es gibt zwei Arten von Open-Source-Komponenten, die im HDInsight-Dienst verfügbar sind:

- **Integrierte Komponenten** – Diese Komponenten sind in HDInsight-Clustern vorinstalliert und stellen Kernfunktionen des Clusters bereit. So gehören beispielsweise Yarn Resource Manager, die Hive-Abfragesprache (HiveQL) und die Mahout Library zu dieser Kategorie. Eine vollständige Liste der Clusterkomponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen](hdinsight-component-versioning.md).

- **Benutzerdefinierte Komponenten** – Als Benutzer des Clusters können Sie in Ihrem Workload eine beliebige in der Community verfügbare oder von Ihnen erstellte Komponente installieren oder verwenden.

> [AZURE.WARNING]Komponenten, die mit dem HDInsight-Cluster bereitgestellt werden, werden vollständig unterstützt, und Microsoft Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.
>
> Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Auf diese Weise kann das Problem behoben werden, ODER Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. So können z. B. viele Communitywebsites wie die folgenden verwendet werden: [MSDN-Forum für HDInsight](https://social.msdn.microsoft.com/Forums/azure/de-DE/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Für Apache-Projekte gibt es auf [http://apache.org](http://apache.org) Projektwebsites, zum Beispiel: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

Der HDInsight-Dienst bietet mehrere Möglichkeiten, benutzerdefinierte Komponenten zu verwenden. Unabhängig davon, wie die Komponente verwendet wird oder im Cluster installiert ist, gilt der gleiche Supportumfang. Nachfolgend finden Sie eine Liste der am häufigsten genutzten Möglichkeiten für die Verwendung von benutzerdefinierten Komponenten in HDInsight-Clustern:

1. Senden des Auftrags – Hadoop- oder andere Auftragstypen, die benutzerdefinierte Komponenten ausführen oder verwenden, können zum Cluster gesendet werden.

2. Clusteranpassung – Während der Clustererstellung können Sie zusätzliche Einstellungen und benutzerdefinierte Komponenten angeben, die auf den Clusterknoten installiert werden.

3. Beispiele – Für beliebte benutzerdefinierte Komponenten stellen Microsoft und andere Anbieter u. U. Beispiele dafür bereit, wie diese Komponenten in den HDInsight-Clustern verwendet werden können. Für diese Beispiele wird kein Support bereitgestellt.

## Problembehandlung

Über die Ambari-Webbenutzeroberfläche können Sie Informationen anzeigen, die von Skripts während der Erstellung des Clusters protokolliert wurden.

1. Navigieren Sie in Ihrem Browser zu https://CLUSTERNAME.azurehdinsight.net. Ersetzen Sie CLUSTERNAME durch den Namen Ihres HDInsight-Clusters.

	Geben Sie bei der entsprechenden Aufforderung den Namen (admin) und das Kennwort des Administratorkontos für den Cluster ein. Möglicherweise müssen Sie die Administratoranmeldeinformationen in einem Webformular erneut eingeben.

2. Wählen Sie in der Leiste oben auf der Seite die Option __ops__ aus. Dadurch wird eine Liste der aktuellen und vorherigen Vorgänge angezeigt, die über Ambari auf dem Cluster durchgeführt wurden.

	![Ambari-Webbenutzeroberfläche mit ausgewählter Option "ops"](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Suchen Sie die Einträge, die __run\_customscriptaction__ in der Spalte __Operations__ enthalten. Diese werden erstellt, wenn die Skriptaktionen ausgeführt werden.

	![Screenshot von Vorgängen](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Wählen Sie diesen Eintrag aus, und führen Sie einen Drilldown durch die Links aus, um die Ausgabe für STDOUT und STDERR anzuzeigen, die beim Ausführen des Skripts auf dem Cluster generiert wurde.

## Nächste Schritte

Informationen und Beispiele zum Erstellen und Verwenden von Skripts zum Anpassen eines Clusters finden Sie über die folgenden Links:

- [Entwickeln von Script Action-Skripts für HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Installieren und Verwenden von Spark in HDInsight-Clustern](hdinsight-hadoop-spark-install-linux.md)
- [Installieren und Verwenden von R in HDInsight-Clustern](hdinsight-hadoop-r-scripts-linux.md)
- [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install-linux.md)
- [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Phasen während der Clustererstellung"

<!---HONumber=Oct15_HO3-->