<properties
	pageTitle="DataStax auf Ubuntu-Ressourcen-Manager-Vorlage"
	description="Erfahren Sie, wie Sie auf einfache Weise auf Ubuntu-VMs mit Azure PowerShell oder der Azure-CLI und einer Resource Manager-Vorlage einen neuen DataStax-Cluster bereitstellen"
	services="multiple"
	documentationCenter=""
	authors="karthmut"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="karthmut"/>

# DataStax auf Ubuntu-Ressourcen-Manager-Vorlage

DataStax ist ein anerkannter Branchenführer bei der Entwicklung und Bereitstellung von Lösungen basierend auf dem kommerziell unterstützten, für den Unternehmenseinsatz bereiten Apache Cassandra ™. Die NoSQL-verteilte Open Source-Datenbanktechnologie ist weithin anerkannt als agil, immer bereit und vorhersehbar auf jede Größe skalierbar. DataStax ist sowohl für Unternehmen (DSE) als auch für die Community (DSC) verfügbar und bietet Funktionen wie In-Memory-Computing, unternehmensweite Sicherheit, schnelle und leistungsfähige integrierte Analysen und Unternehmenssuche.

Zusätzlich zu dem, was bereits in Azure Marketplace verfügbar war, können Sie jetzt auch auf einfache Weise auf Ubuntu-VMs mit Azure PowerShell oder der Azure-CLI mithilfe einer Vorlage für Ressourcen-Manager einen neuen DataStax-Cluster bereitstellen.

Neu bereitgestellte Cluster auf Basis dieser Vorlage werden die im folgenden Diagramm beschriebene Topologie besitzen, auch wenn andere Topologien leicht durch das Anpassen des vorgestellten Ansatzes erreicht werden können:

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

Im Grunde können Sie die Anzahl der Knoten definieren, die im neuen Apache Cassandra-Cluster bereitgestellt werden. Zusätzlich dazu wird eine Instanz des Operation Center-Dienstes von DataStax auch auf einem eigenständigen virtuellen Computer innerhalb des gleichen VNETs bereitgestellt, was Ihnen die Möglichkeit gibt, den Clusterstatus und jeden einzelnen Knoten zu überwachen, Knoten hinzuzufügen/zu entfernen und alle administrativen Aufgaben im Zusammenhang mit diesem Cluster durchzuführen.

Nachdem die Bereitstellung abgeschlossen ist, können Sie auf die Datastax Operations Center-VM-Instanz mithilfe der konfigurierten DNS-Adresse zugreifen. Die OpsCenter-VM verfügt über SSH-Port 22 aktiviert sowie den Port 8443 für HTTPS. Die DNS-Adresse für das Operations Center enthält den DNS-Namen und die Region, die als Parameter bei der Erstellung einer Bereitstellung auf Basis dieser Vorlage im Format `{dnsName}.{region}.cloudapp.azure.com` eingegeben wird. Wenn Sie eine Bereitstellung mit dem Parameter `dnsName` festgelegt auf "Datastax" in der Region "West US" erstellt haben, können Sie auf den virtuellen Computer von Datastax Operations Center für die Bereitstellung über `https://datastax.westus.cloudapp.azure.com:8443` zugreifen.

> [AZURE.NOTE]Das in der Bereitstellung verwendete Zertifikat ist ein selbstsigniertes Zertifikat, das eine Browser-Warnung erzeugt. Auf der [Datastax](http://www.datastax.com/)-Website können Sie nachverfolgen, wie das Zertifikat durch Ihr eigenes SSL-Zertifikat ersetzt wird.

Bevor wir zu näheren Einzelheiten zum Azure Resource Manager und der für diese Bereitstellung verwendeten Vorlage kommen, stellen Sie sicher, dass Azure, PowerShell und Azure-CLI konfiguriert und einsatzbereit sind.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## Erstellen Sie einen auf DataStax basierenden Cassandra-Cluster mit einer Resource Manager-Vorlage und Azure PowerShell

Gehen folgendermaßen Sie vor, um ein auf DataStax basierendes Apache Cassandra-Cluster mithilfe einer Resource Manager-Vorlage im Github-Vorlagenrepository mit Azure PowerShell zu erstellen.

### Schritt 1: Laden Sie die JSON-Datei für die Vorlage und andere Dateien herunter.

Bestimmen Sie einen lokalen Ordner als Speicherort für die JSON-Vorlage und andere Dateien, und erstellen Sie den Ordner (z. B. C:\\Azure\\Vorlagen\\DataStax).

Geben Sie den Ordnernamen an, und führen Sie die folgenden Befehle aus:

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

Als Alternative können Sie auch das Vorlagen-Repository mithilfe eine Git-Clients Ihrer Wahl klonen, zum Beispiel:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Sobald dies abgeschlossen ist, suchen Sie in C:\\Azure\\Templates nach dem Ordner datastax-on-ubuntu.

### Schritt 2: (optional) Machen Sie sich mit Vorlagenparametern vertraut.

Wenn Sie nicht-triviale Lösungen wie ein auf Datastax basierendes Apache Cassandra-Cluster bereitstellen, müssen Sie eine Reihe von Konfigurationsparametern für den Umgang mit einer Anzahl an erforderlichen Einstellungen angeben. Durch Deklarieren dieser Parameter in der Vorlagendefinition ist es möglich, Werte während der Bereitstellungsausführung über eine externe Datei oder auf der Befehlszeile anzugeben.

Wenn Sie im Abschnitt "Parameter" am Anfang der Datei azuredeploy.json nachsehen, finden Sie den Parametersatz, der von der Vorlage zum Konfigurieren eines DataStax-Clusters verlangt wird. Hier ist ein Beispiel für diesen Abschnitt in der azuredeploy.json-Vorlage:

	"parameters": {
		"region": {
			"type": "string",
			"defaultValue": "West US",
			"metadata": {
				"Description": "Location where resources will be provisioned"
			}
		},
		"storageAccountPrefix": {
			"type": "string",
			"defaultValue": "uniqueStorageAccountName",
			"metadata": {
				"Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the opserations center public IP"
			}
		},
		"virtualNetworkName": {
			"type": "string",
			"defaultValue": "myvnet",
			"metadata": {
				"Description": "Name of the virtual network provisioned for the cluster"
			}
		},
		"adminUsername": {
			"type": "string",
			"metadata": {
				"Description": "Administrator user name used when provisioning virtual machines"
			}
		},
		"adminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Administrator password used when provisioning virtual machines"
			}
		},
		"opsCenterAdminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Sets the operations center admin user password"
			}
		},
		"clusterVmSize": {
			"type": "string",
			"defaultValue": "Standard_D3",
			"allowedValues": [
				"Standard_D1",
				"Standard_D2",
				"Standard_D3",
				"Standard_D4",
				"Standard_D11",
				"Standard_D12",
				"Standard_D13",
				"Standard_D14"
			],
			"metadata": {
				"Description": "The size of the virtual machines used when provisioning cluster nodes"
			}
		},
		"clusterNodeCount": {
			"type": "int",
			"metadata": {
				"Description": "The number of nodes provisioned in the cluster"
			}
		},
		"clusterName": {
			"type": "string",
			"metadata": {
				"Description": "The name of the cluster provisioned"
			}
		}
	}

Indem Sie die erforderlichen Parameter beschreiben, einschließlich Details wie Datentypen, zulässige Werte usw., ist klar, dass dieser Abschnitt nicht nur sehr nützlich für alle auf Parameterwerte bezogenen Überprüfungsaufgaben ist, die bei der Vorlagenausführung im interaktiven Modus (z. B. PowerShell oder Azure-CLI) übergeben werden, sondern auch auf beliebige automatische Erkennungs-Benutzeroberflächen, die durch das Analysieren der Liste der erforderlichen Parameter und der zugehörigen Beschreibung dynamisch erstellt werden konnten.

### Schritt 3: Bereitstellen von neuen DataStax-Clustern mit der Vorlage.

Das Vorbereiten einer Parameterdatei für die Bereitstellung bedeutet das Erstellen einer JSON-Datei, die Runtime-Werte für alle Parameter enthält, die dann als eine Einheit an den Bereitstellungsbefehl übergeben werden.

Hier ist ein Beispiel, das Sie in der Datei Azuredeploy parameters.json finden können:

	{
		"storageAccountPrefix": {
			"value": "scorianisa"
		},
		"dnsName": {
			"value": "scorianids"
		},
		"virtualNetworkName": {
			"value": "datastax"
		},
		"adminUsername": {
			"value": "scoriani"
		},
		"adminPassword": {
			"value": ""
		},
		"region": {
			"value": "West US"
		},
		"opsCenterAdminPassword": {
			"value": ""
		},
		"clusterVmSize": {
			"value": "Standard_D3"
		},
		"clusterNodeCount": {
			"value": 3
		},
		"clusterName": {
			"value": "cl1"
		}
	}

Geben Sie einen Namen für die Azure-Bereitstellung, einen Namen für die Ressourcengruppe, einen Azure-Speicherort sowie den Ordner mit der gespeicherte JSON-Datei an, und führen Sie die folgenden Befehle aus:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Beim Ausführen des **New-AzureResourceGroupDeployment**-Befehls werden Parameterwerte aus der JSON-Datei extrahiert und die Ausführung der Vorlage entsprechend gestartet. Das Definieren und Verwenden mehrerer Parameterdateien in den verschiedenen Umgebungen (z. B. Test, Produktion usw.) fördert die Wiederverwendung von Vorlagen und vereinfacht komplexe Multi-Umgebungslösungen.

Denken Sie beim Bereitstellen daran, dass ein neues Azure-Speicherkonto erstellt werden muss, sodass der Name, den Sie als Speicherkonto-Parameter angeben, eindeutig ist und alle Anforderungen für ein Azure-Speicherkonto erfüllt werden.

Während und nach der Bereitstellung können Sie alle Anforderungen überprüfen, die während der Bereitstellung vorgenommen wurden, einschließlich der aufgetretenen Fehler.

Wechseln Sie zu diesem Zweck ins [Azure Portal](https://portal.azure.com) und gehen Sie folgendermaßen vor:

- Klicken Sie auf der linken Navigationsleiste auf "Durchsuchen", scrollen Sie nach unten und klicken Sie auf "Ressourcengruppen".  
- Nach dem Klicken auf die Ressourcengruppe, die Sie gerade erstellt haben, wird das Blatt "Ressourcengruppe" angezeigt.  
- Durch Klicken auf das Balken-Diagramm "Ereignisse" im Bereich "Überwachung" des Blattes "Ressourcengruppe" erscheinen die Ereignisse für die Bereitstellung:
- Durch Klicken auf die einzelnen Ereignisse können Sie sich die Details jedes einzelnen Vorgangs, der von der Vorlage angestoßen wurde, noch näher anzeigen.

Nach den Tests verwenden Sie zum Entfernen dieser Ressourcengruppe und all ihrer Ressourcen (Speicherkonto, virtueller Computer, virtuelles Netzwerk) den folgenden Befehl:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Schritt 3-b: Stellen Sie ein DataStax-Cluster mit einer Resource Manager-Vorlage von Azure CLI bereit

Das Bereitstellen eines Apache Cassandra-Clusters über die Azure-CLI ist funktionell gleichwertig mit dem oben aufgeführten PowerShell-Ansatz und erfordert zunächst die Erstellung einer Ressourcengruppe, für die Name und Speicherort angegeben wird:

	azure group create dsc "West US"

Und anschließend das Aufrufen einer Bereitstellungserstellung und Übergeben des Namens der Ressourcengruppe, der Parameterdatei und der tatsächlichen Vorlage, wie unten dargestellt:

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Es ist auch möglich, den Status der einzelnen Bereitstellungen zu überprüfen, indem Sie den folgenden Befehl aufrufen:

	azure group deployment list dsc

## Einen Überblick über die Vorlagenstruktur und die Dateiorganisation, die zum Bereitstellen von DataStax auf Ubuntu erstellt wurden

Um einen stabilen und wiederverwendbaren Ansatz für den Entwurf von Resource Manager-Vorlagen zu finden, ist zusätzliches Nachdenken erforderlich, um die Reihe komplexer und zusammenhängender Aufgaben während der Bereitstellung einer komplexen Lösung wie DataStax zu organisieren. Durch die Nutzung von ARM-Funktionen zum **Verknüpfen von Vorlagen**, **Ressource-Schleifen** und zusätzlich Skriptausführung über verknüpfte Erweiterungen ist es möglich, einen modularen Ansatz zu implementieren, der mit nahezu alle komplexen vorlagenbasierten Bereitstellungen wiederverwendet werden kann.

In diesem Diagramm werden die Beziehungen zwischen allen Dateien beschrieben, die von GitHub für diese Bereitstellung heruntergeladen werden:

![datastax-files](media/virtual-machines-datastax-template/datastax-files.png)

Wir haben bereits die Rolle der Datei **azuredeploy-parameters.json** erwähnt, die verwendet werden soll, um einen bestimmten Satz von Parameterwerten während der Vorlagenausführung zu übergeben. Der Kern dieses Bereitstellungsansatzes befindet sich jedoch in der Datei **azuredeploy.json**. Der Parameter-Abschnitt wird übersprungen, da diese in diesem Dokument bereits beschrieben wurden. Im folgenden Abschnitt werden **"Variablen"** dargestellt. Im Grunde ist hier eine Anzahl an Feldern (JSON-Datentypen oder -Fragmente) enthalten, die auf Konstanten oder berechnete Werte zum Zeitpunkt der Ausführung festgelegt werden, wie im folgenden Beispiel ersichtlich:

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/trentmswanson/azure-quickstart-templates/master/datastax-on-ubuntu/",
	"sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
	"clusterNodesTemplateUrl": "[concat(variables('templateBaseUrl'), 'ephemeral-nodes-resources.json')]",
	"opsCenterTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-resources.json')]",
	"opsCenterInstallTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-install-resources.json')]",
	"opsCenterVmSize": "Standard_A1",
	"networkSettings": {
		"virtualNetworkName": "[parameters('virtualNetworkName')]",
		"addressPrefix": "10.0.0.0/16",
		"subnet": {
			"dse": {
				"name": "dse",
				"prefix": "10.0.0.0/24",
				"vnet": "[parameters('virtualNetworkName')]"
			}
		},
		"statics": {
			"clusterRange": {
				"base": "10.0.0.",
				"start": 5
			},
			"opsCenter": "10.0.0.240"
		}
	},
	"osSettings": {
		"imageReference": {
			"publisher": "Canonical",
			"offer": "UbuntuServer",
			"sku": "14.04.2-LTS",
			"version": "latest"
		},
		"scripts": [
			"[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
			"[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
			"https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
		]
	},
	"sharedStorageAccountName": "[concat(parameters('storageAccountPrefix'),'cmn')]",
	"nodeList": "[concat(variables('networkSettings').statics.clusterRange.base, variables('networkSettings').statics.clusterRange.start, '-', parameters('clusterNodeCount'))]"
	},

Wenn Sie sich dieses Beispiel im Detail ansehen, erkennen Sie zwei verschiedene Ansätze. Im ersten Fragment wird die "OsSettings"-Variable auf ein geschachteltes JSON-Element mit 4 Schlüssel-Wertpaaren festgelegt:

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
Im zweiten Fragment ist stattdessen die "Skripts"-Variable ein JSON-Array, in dem einzelne Elemente zur Laufzeit mithilfe einer Vorlagensprachenfunktion (concat) und dem Wert einer anderen Variablen plus Zeichenfolgenkonstanten berechnet werden:

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

Im Abschnitt **“Ressourcen“** geschieht am meisten. Schauen Sie sich diesen Abschnitt sorgfältig an. Sie können sofort zwei verschiedene Fälle identifizieren: der erste ist ein vom Typ `Microsoft.Resources/deployments` definiertes Element, was im Grunde den Aufruf einer geschachtelten Bereitstellung innerhalb der Hauptbereitstellung bedeutet. Durch das Element `templateLink` (und zugehörige Versionseigenschaften) kann eine verknüpfte Vorlagendatei bestimmt werden, die zur Übergabe eines Parametersatz als Eingabe aufgerufen wird, wie Sie in diesem Fragment sehen:

	{
	      "name": "shared",
	      "type": "Microsoft.Resources/deployments",
	      "apiVersion": "2015-01-01",
	      "properties": {
	        "mode": "Incremental",
	        "templateLink": {
	          "uri": "[variables('sharedTemplateUrl')]",
	          "contentVersion": "1.0.0.0"
	        },
	        "parameters": {
	          "region": {
	            "value": "[parameters('region')]"
	          },
	          "networkSettings": {
	            "value": "[variables('networkSettings')]"
	          },
	          "storageAccountName": {
	            "value": "[variables('sharedStorageAccountName')]"
	          }
	        }
	      }
	    },

Im ersten Beispiel wird deutlich, wie die Datei **azuredeploy.json** in diesem Szenario als ein Mechanismus zur Orchestrierung organisiert wurde, indem eine Anzahl anderer Vorlagendateien aufgerufen wird, wobei jede jeweils für einen Teil der erforderlichen Bereitstellungsaufgaben verantwortlich ist.

Insbesondere folgende verknüpfte Vorlagen werden für diese Bereitstellung eingesetzt:

-	**shared-resource.json**: enthält die Definition aller Ressourcen, die in der Bereitstellung geteilt werden. Beispiele hierfür sind Speicherkonten, die zum Speichern der Datenträger auf dem Betriebssystem oder den virtuellen Netzwerken des virtuellen Computers verwendet werden.
-	**opscenter-resources.json**: stellt die OpsCenter-VM und alle dazugehörigen Ressourcen wie Netzwerkschnittstelle, öffentliche IP-Adresse und so weiter bereit.
-	**opscenter-install-resources.json**: stellt eine OpsCenter-VM-Erweiterung (benutzerdefiniertes Skript für Linux) bereit, die die spezifische Bash-Skriptdatei aufruft (** opscenter.sh**), die für die Einrichtung des OpsCenter-Dienstes auf diesem virtuellen Computer erforderlich ist.
-	**ephemeral-nodes-resources.json**: stellt alle Clusterknoten-VMs und verbundene Ressourcen (z. B. Netzwerkkarten, private IP-Adressen, etc.) bereit. Diese Vorlage stellt auch VM-Erweiterungen bereit (benutzerdefinierte Skripts für Linux) und ruft ein Bash-Skript (** dsenode.sh**) ab, um Apache Cassandra-Bits auf jedem Knoten physisch zu installieren.

Sehen wir uns nun die letzte Vorlage genauer an, da diese aus Sicht der Vorlagenentwicklung am interessantesten ist. Ein wichtiges, hervorzuhebendes Konzept ist, wie eine einzelne Vorlagendatei mehrere Kopien eines einzelnen Ressourcentyps bereitstellen und für jede Instanz eindeutige Werte für die erforderlichen Einstellungen festgelegen kann. Dieses Konzept ist als **Ressourcen-Looping** bekannt.

Wenn **ephemeral-nodes-resources.json** aus der Hauptdatei **azuredeploy.json** aufgerufen wird, wird tatsächlich ein Parameter namens **NodeCount** als Teil der Parameterliste bereitgestellt. Innerhalb der untergeordneten Vorlage wird dieser Parameter (Anzahl der bereitzustellenden Knoten im Cluster) innerhalb des Elements **"kopieren"** jeder einzelnen Ressource benutzt, die in mehreren Kopien bereitgestellt werden muss, wie im folgenden Fragment verdeutlicht wird. Für alle Einstellungen, in denen es erforderlich ist, eindeutige Werte zwischen verschiedenen Instanzen der bereitgestellten Ressource anzugeben, kann die **copyindex()**-Funktion verwendet werden, um einen numerischen Wert zu erhalten, der den aktuellen Index in der bestimmten Ressourcenschleifenerstellung angibt. Im folgenden Fragment können Sie sehen, wie dieses Konzept für die Erstellung mehrerer VMs für Clusterknoten angewendet wird:

			   {
			      "apiVersion": "2015-05-01-preview",
			      "type": "Microsoft.Compute/virtualMachines",
			      "name": "[concat(parameters('namespace'), 'vm', copyindex())]",
			      "location": "[parameters('region')]",
			      "copy": {
			        "name": "[concat(parameters('namespace'), 'vmLoop')]",
			        "count": "[parameters('nodeCount')]"
			      },
			      "dependsOn": [
			        "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]",
			        "[concat('Microsoft.Compute/availabilitySets/', parameters('namespace'), 'set')]",
			        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]"
			      ],
			      "properties": {
			        "availabilitySet": {
			          "id": "[resourceId('Microsoft.Compute/availabilitySets', concat(parameters('namespace'), 'set'))]"
			        },
			        "hardwareProfile": {
			          "vmSize": "[parameters('vmSize')]"
			        },
			        "osProfile": {
			          "computername": "[concat(parameters('namespace'), 'vm', copyIndex())]",
			          "adminUsername": "[parameters('adminUsername')]",
			          "adminPassword": "[parameters('adminPassword')]"
			        },
			        "storageProfile": {
			          "imageReference": "[parameters('osSettings').imageReference]",
			          "osDisk": {
			            "name": "osdisk",
			            "vhd": {
			              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/', variables('vmName'), copyindex(), '-osdisk.vhd')]"
			            },
			            "caching": "ReadWrite",
			            "createOption": "FromImage"
			          },
			          "dataDisks": [
			            {
			              "name": "datadisk1",
			              "diskSizeGB": 1023,
			              "lun": 0,
			              "vhd": {
			                "Uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/','vhds/', variables('vmName'), copyindex(), 'DataDisk1.vhd')]"
			              },
			              "caching": "None",
			              "createOption": "Empty"
			            }
			          ]
			        },
			        "networkProfile": {
			          "networkInterfaces": [
			            {
			              "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('namespace'), 'nic', copyindex()))]"
			            }
			          ]
			        }
			      }
			    },

Ein weiteres wichtiges Konzept bei der Ressourcenerstellung ist die Möglichkeit, Abhängigkeiten und Vorränge zwischen Ressourcen anzugeben, wie Sie am JSON-Array **DependsOn** sehen können. In dieser Vorlage verfügt jeder Knoten zusätzlich über einen angeschlossenen Datenträger mit 1 TB (siehe `dataDisks`), der zum Hosten von Backups und Snapshots von der Apache Cassandra-Instanz verwendet werden kann.

Angeschlossene Datenträger werden als Teil der Knoten-vorbereitenden Aktivitäten formatiert, die durch das Ausführen der Skriptdatei **dsenode.sh** ausgelöst werden. Die erste Zeile des Skripts ruft tatsächlich ein anderes Skript auf:

	bash vm-disk-utils-0.1.sh

vm-disk-utils-0.1.sh ist Teil des Ordners **shared_scripts\\ubuntu** innerhalb der Azure-Schnellstart-Vorlage aus dem Github-Repository und enthält nützliche Funktionen für die Bereitstellung von Datenträgern, der Formatierung und des Stripings, die jedes Mal wiederverwendet werden können, wenn Sie ähnliche Aufgaben im Rahmen der Vorlagenerstellung ausführen müssen.

Ein weiteres interessantes Fragment zum Erkunden ist das Fragment im Zusammenhang mit den CustomScriptForLinux -VM-Erweiterungen. Diese werden als gesonderter Typ Ressource installiert, mit einer Abhängigkeit auf jedem Clusterknoten (und in der OpsCenter-Instanz), sodass der gleiche Mechanismus für die Ressourcenschleife genutzt wird, der für virtuelle Maschinen beschrieben ist:

	{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat(parameters('namespace'), 'vm', copyindex(), '/installdsenode')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "[concat(parameters('namespace'), 'vmLoop')]",
		"count": "[parameters('nodeCount')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', parameters('namespace'), 'vm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": "[parameters('osSettings').scripts]",
			"commandToExecute": "bash dsenode.sh"
		}
	}
	}

Alle anderen Vorlagendateien, die für diese Bereitstellung erforderlich sind, erstellen stattdessen einzelne Instanzen von allen erforderlichen Ressourcen, können also als eine vereinfachte Version der Datei **ephemeral-nodes-resources.json** betrachtet werden.

Indem Sie sich mit den anderen Dateien in dieser Bereitstellung vertraut machen, werden Sie alle Details und bewährten Methoden zum Organisieren und Orchestrieren komplexer Bereitstellungsstrategien für Lösungen mit mehreren Knoten verstehen, basierend auf jeder Technologie, unter Nutzung von Azure Resource Manager-Vorlagen. Obwohl nicht zwingend erforderlich, ist das Strukturieren der Vorlagendateien eine empfohlene Vorgehensweise, wie im folgenden Diagramm verdeutlicht:

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

Im Wesentlichen empfiehlt diesen Ansatz:

-	Definieren Sie die Kernvorlagendatei als eine zentrale Orchestrierungsstelle für alle spezifischen Aktivitäten der Bereitstellung, indem Sie Vorlagen nutzen, die zum Aufrufen von Sub-Vorlagenausführungen verknüpfen.
-	Erstellen Sie eine spezifische Vorlagendatei, die alle Ressourcen bereitstellt, die gemeinsam von allen anderen bestimmten Bereitstellungsaufgaben geteilt werden (z. B. Speicherkonten, Vnet-Konfiguration usw.). Diese kann häufig zwischen Bereitstellungen wiederverwendet werden, die ähnliche Anforderungen im Hinblick auf die allgemeine Infrastruktur haben.
-	Schließen Sie optionale Ressourcenvorlagen ein, um Ressourcen-spezifische Anforderungen zu erkennen.
-	Erstellen Sie für identische Mitglieder einer Gruppe von Ressourcen (Knoten in einem Cluster usw.) bestimmte Vorlagen, die die Ressource-Schleife nutzen, um mehreren Instanzen eindeutige Eigenschaften bereitstellen.
-	Für alle Post-Bereitstellungsaufgaben (z. B. Produktinstallation, Konfiguration usw.) nutzen Sie die Bereitstellung von Skripterweiterungen und erstellen erstellen Sie Skripte, die für die einzelnen Technologien spezifisch sind.

Weitere Informationen finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn835138.aspx).

<!--HONumber=52-->
