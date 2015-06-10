<properties
	pageTitle="DataStax auf Ubuntu-Ressourcen-Manager-Vorlage"
	description="Erfahren Sie, wie Sie auf einfache Weise auf Ubuntu-VMs mit Azure PowerShell oder der Azure-CLI und einer Resource Manager-Vorlage einen neuen DataStax-Cluster bereitstellen"
	services="virtual-machines"
	documentationCenter=""
	authors="karthmut"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="karthmut"/>

# DataStax auf Ubuntu mit einer Ressourcen-Manager-Vorlage

DataStax ist ein anerkannter Branchenführer bei der Entwicklung und Bereitstellung von Lösungen, die auf dem kommerziell unterstützten, für den Unternehmenseinsatz bereiten Apache Cassandra™ basieren. Diese verteilte NoSQL-Datenbanktechnologie ist weithin anerkannt als agil, immer funktionsbereit und vorhersehbar auf jede Größe skalierbar. DataStax bietet die Versionen Enterprise (DSE) und Community (DSC) an. Zudem beinhaltet es Funktionen wie In-Memory-Computing, unternehmensweite Sicherheit, schnelle und leistungsfähige integrierte Analysen und eine Unternehmenssuche.

Zusätzlich zu den bereits in Azure Marketplace verfügbaren Funktionen können Sie jetzt auch auf einfache Weise auf Ubuntu-VMs mit [Azure PowerShell](powershell-install-configure.md) oder der [Azure-Befehlszeilenschnittstelle](xplat-cli.md) unter Verwendung einer Ressourcen-Manager-Vorlage einen neuen DataStax-Cluster bereitstellen.

Neu bereitgestellte Cluster auf Basis dieser Vorlage besitzen die im folgenden Diagramm beschriebene Topologie, auch wenn andere Topologien leicht durch das Anpassen der in diesem Artikel gezeigten Vorlage erreicht werden können:

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

Mithilfe von Parametern können Sie die Anzahl der Knoten, die im neuen Apache Cassandra-Cluster bereitgestellt werden, definieren. Eine Instanz des Operation Center-Diensts von DataStax wird auch auf einem eigenständigen virtuellen Computer innerhalb des gleichen VNETs bereitgestellt, was Ihnen die Möglichkeit gibt, den Clusterstatus und jeden einzelnen Knoten zu überwachen, Knoten hinzuzufügen/zu entfernen und alle administrativen Aufgaben im Zusammenhang mit diesem Cluster durchzuführen.

Nachdem die Bereitstellung abgeschlossen ist, können Sie mithilfe der konfigurierten DNS-Adresse auf die DataStax Operations Center-VM-Instanz zugreifen. Bei der OpsCenter-VM ist der SSH-Port 22 aktiviert sowie der Port 8443 für HTTPS. Die DNS-Adresse für das Operations Center enthält den *DNS-Namen* und die *Region*, die als Parameter übergeben werden. Daraus ergibt sich folgendes Format: `{dnsName}.{region}.cloudapp.azure.com`. Wenn Sie eine Bereitstellung mit dem *dnsName*-Parameter festgelegt auf "datastax" in der Region "West US" erstellt haben, können Sie auf den virtuellen Computer von DataStax Operations Center für die Bereitstellung über `https://datastax.westus.cloudapp.azure.com:8443` zugreifen.

> [AZURE.NOTE]Das in der Bereitstellung verwendete Zertifikat ist ein eigensigniertes Zertifikat, das eine Browser-Warnung erzeugt. Auf der [DataStax](http://www.datastax.com/)-Website können Sie nachverfolgen, wie das Zertifikat durch Ihr eigenes SSL-Zertifikat ersetzt wird.

Bevor wir zu näheren Einzelheiten zum Azure-Ressourcen-Manager und der für diese Bereitstellung verwendeten Vorlage kommen, stellen Sie sicher, dass Azure PowerShell oder die Azure-Befehlszeilenschnittstelle konfiguriert und einsatzbereit ist.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## Erstellen von auf DataStax basierenden Cassandra-Clustern mit einer Ressourcen-Manager-Vorlage

Gehen Sie wie folgt vor, um ein auf DataStax basierendes Apache Cassandra-Cluster mithilfe einer Ressourcen-Manager-Vorlage aus dem GitHub-Vorlagenrepository zu erstellen. Jeder Schritt umfasst Anweisungen für Azure PowerShell und die Azure-Befehlszeilenschnittstelle.

### Schritt 1-a: Herunterladen der Vorlagendateien mithilfe von PowerShell

Erstellen Sie einen lokalen Ordner für die JSON-Vorlage und andere zugehörige Dateien (z. B. "C:\Azure\Vorlagen\DataStax").

Fügen Sie dabei den Namen Ihres lokalen Ordners ein, und führen Sie damit die folgenden Befehle aus:

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

### Schritt 1-b: Herunterladen der Vorlagendateien mithilfe der Azure-Befehlszeilenschnittstelle

Klonen Sie das komplette Vorlagen-Repository mithilfe eines Git-Clients Ihrer Wahl, zum Beispiel:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Ermitteln Sie anschließend unter "C:\Azure\Vorlagen" den Ordner **datastax-on-ubuntu**.

### Schritt 2 (optional): Erlernen der Vorlagenparameter

Wenn Sie schwierige Lösungen wie einen auf DataStax basierenden Apache Cassandra-Cluster bereitstellen, müssen Sie eine Reihe von Konfigurationsparametern für den Umgang mit unterschiedlichen erforderlichen Einstellungen angeben. Durch das Deklarieren dieser Parameter in der Vorlagendefinition ist es möglich, Werte während der Bereitstellung über eine externe Datei oder an der Befehlszeile anzugeben.

Im Abschnitt "parameters" am Anfang der Datei **azuredeploy.json** finden Sie den Parametersatz, der für die Vorlage zum Konfigurieren eines DataStax-Clusters erforderlich ist. Dies ist Beispiel für den Abschnitt "parameters" aus der Datei azuredeploy.json dieser Vorlage:

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

Jeder Parameter enthält Details wie Datentyp und zulässige Werte. Dies ermöglicht die Überprüfung von Parametern, die während der Vorlagenausführung im interaktiven Modus (z. B. PowerShell oder Azure-CLI) übergeben werden, und ebenso eine Benutzeroberfläche mit automatischer Erkennung, die dynamisch durch das Analysieren der Liste der erforderlichen Parameter und ihrer Beschreibungen erstellt werden könnte.

### Schritt 3-a: Bereitstellen eines DataStax-Clusters mit einer Vorlage mithilfe von PowerShell

Bereiten Sie eine Parameterdatei für die Bereitstellung vor, indem Sie eine JSON-Datei mit den Laufzeitwerten für alle Parameter erstellen. Diese Datei wird dann als eine Entität an den Bereitstellungsbefehl übergeben. Wenn Sie keine Parameterdatei einfügen, verwendet PowerShell die in der Vorlage angegebenen Standardwerte und fordert Sie auf, die restlichen Werte einzugeben.

Hier ist ein Beispielsatz von Parametern aus der Datei **azuredeploy-parameters.json**:

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

Geben Sie Namen für die Azure-Bereitstellung, die Ressourcengruppe, den Azure-Speicherort sowie den Ordner mit der gespeicherten JSON-Bereitstellungsdatei an. Führen Sie dann diese Befehle aus:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Beim Ausführen des Befehls **New-AzureResourceGroupDeployment** werden damit die Parameterwerte aus der JSON-Parameterdatei extrahiert und die Ausführung der Vorlage entsprechend gestartet. Das Definieren und Verwenden mehrerer Parameterdateien in den verschiedenen Umgebungen (z. B. Test, Produktion usw.) fördert die Wiederverwendung von Vorlagen und vereinfacht komplexe Lösungen mit mehreren Umgebungen.

Denken Sie beim Bereitstellen daran, dass ein neues Azure-Speicherkonto erstellt werden muss, daher muss der Name, den Sie als Speicherkonto-Parameter angeben, eindeutig sein und alle Anforderungen an ein Azure-Speicherkonto erfüllen (nur Kleinbuchstaben und Ziffern).

Während und nach der Bereitstellung können Sie alle Anforderungen überprüfen, die während der Bereitstellung vorgenommen wurden, einschließlich der aufgetretenen Fehler.

Wechseln Sie dazu in das [Azure-Portal](https://portal.azure.com), und gehen Sie folgendermaßen vor:

- Klicken Sie auf der linken Navigationsleiste auf "Durchsuchen", scrollen Sie nach unten, und klicken Sie auf "Ressourcengruppen".  
- Nach dem Klicken auf die Ressourcengruppe, die Sie gerade erstellt haben, wird das Blatt "Ressourcengruppe" angezeigt.  
- Durch Klicken auf das Balkendiagramm "Ereignisse" im Bereich "Überwachung" des Blattes "Ressourcengruppe" werden die Ereignisse für die Bereitstellung angezeigt:
- Durch Klicken auf die einzelnen Ereignisse können Sie sich die Details jedes einzelnen Vorgangs, der von der Vorlage angestoßen wurde, noch näher ansehen.

Verwenden Sie nach den Tests zum Entfernen dieser Ressourcengruppe und all ihrer Ressourcen (Speicherkonto, virtueller Computer, virtuelles Netzwerk) den folgenden Befehl:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Schritt 3-b: Bereitstellen eines DataStax-Clusters mit einer Vorlage mithilfe der Azure-Befehlszeilenschnittstelle

Zum Bereitstellen eines DataStax-Clusters über die Azure-Befehlszeilenschnittstelle erstellen Sie zuerst eine Ressourcengruppe, indem Sie einen Namens und einen Speicherort angeben:

	azure group create dsc "West US"

Übergeben Sie diesen Namen für die Ressourcengruppe, den Speicherort der JSON-Vorlagendatei und den Speicherort der Parameterdatei (siehe Abschnitt oben zur PowerShell) an den folgenden Befehl:

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Sie können den Status der einzelnen Ressourcenbereitstellungen mit dem folgenden Befehl überprüfen:

	azure group deployment list dsc

## Überblick über die DataStax-Vorlagenstruktur und -Dateianordnung

Um eine stabile und wiederverwendbare Ressourcen-Manager-Vorlage zu erstellen, sind eine Reihe komplexer und zusammenhängender Aufgaben während der Bereitstellung einer komplexen Lösung wie DataStax erforderlich. Durch die Nutzung von ARM-Funktionen zum **Verknüpfen von Vorlagen** und **Ressourcenschleifen** und die zusätzliche Skriptausführung über zugehörige Erweiterungen ist es möglich, einen modularen Ansatz zu implementieren, der bei nahezu allen komplexen vorlagenbasierten Bereitstellungen wiederverwendet werden kann.

In diesem Diagramm werden die Beziehungen zwischen allen Dateien beschrieben, die für diese Bereitstellung von GitHub heruntergeladen werden:

![datastax-files](media/virtual-machines-datastax-template/datastax-files.png)

Dieser Abschnitt führt Sie schrittweise durch die Struktur der Datei **azuredeploy.json** für das DataStax-Cluster.

### Abschnitt "parameters"

Im Abschnitt "parameters" von **azuredeploy.json** werden veränderbare Parameter angegeben, die in dieser Vorlage verwendet werden. Die oben genannte Datei **azuredeploy-parameters.json** wird während der Vorlagenausführung zum Übergeben der Werte in den Abschnitt "parameters" von "azuredeploy.json" verwendet.

### Abschnitt "variables"

Im Abschnitt "variables" sind Variablen angegeben, die in dieser Vorlage verwendet werden können. Er enthält eine Reihe von Feldern (JSON-Datentypen oder -Fragmente), die zum Zeitpunkt der Ausführung auf Konstanten oder berechnete Werte festgelegt werden. Dies ist der Abschnitt "variables" für diese DataStax-Vorlage:

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-on-ubuntu/",
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

Wenn Sie sich dieses Beispiel im Detail ansehen, erkennen Sie zwei verschiedene Ansätze. Im ersten Fragment ist die "osSettings"-Variable ein geschachteltes JSON-Element mit vier Schlüssel-Wert-Paaren:

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
Im zweiten Fragment ist stattdessen die "scripts"-Variable ein JSON-Array, in dem einzelne Elemente zur Laufzeit mithilfe einer Vorlagensprachenfunktion ("concat") und dem Wert einer anderen Variablen plus Zeichenfolgenkonstanten berechnet werden:

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

### Abschnitt "resources"

Im Abschnitt **"resources"** geschieht am meisten. Schauen Sie sich diesen Abschnitt sorgfältig an. Sie können sofort zwei verschiedene Fälle identifizieren: Der erste ist ein definiertes Element des Typs `Microsoft.Resources/deployments`, was im Grunde den Aufruf einer geschachtelten Bereitstellung innerhalb der Hauptbereitstellung bedeutet. Durch das Element "templateLink" (und die zugehörige Versionseigenschaft) kann eine verknüpfte Vorlagendatei bestimmt werden, die zur Übergabe eines Parametersatzes als Eingabe aufgerufen wird, wie Sie in diesem Fragment sehen:

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

Im ersten Beispiel wird deutlich, wie die Datei **azuredeploy.json** in diesem Szenario als Mechanismus zur Orchestrierung organisiert wurde, indem eine Anzahl anderer Vorlagendateien aufgerufen wird, wobei jede jeweils für einen Teil der erforderlichen Bereitstellungsaufgaben verantwortlich ist.

Insbesondere folgende verknüpfte Vorlagen werden für diese Bereitstellung eingesetzt:

-	**shared-resource.json**: enthält die Definition aller Ressourcen, die in der Bereitstellung gemeinsam genutzt werden. Beispiele hierfür sind Speicherkonten, die zum Speichern der Datenträger auf dem Betriebssystem und den virtuellen Netzwerken des virtuellen Computers verwendet werden.
-	**opscenter-resources.json**: stellt eine OpsCenter-VM und alle dazugehörigen Ressourcen, einschließlich einer Netzwerkschnittstelle und einer öffentlichen IP-Adresse bereit.
-	**opscenter-install-resources.json**: stellt die OpsCenter-VM-Erweiterung (benutzerdefiniertes Skript für Linux) bereit, die die spezifische Bash-Skriptdatei aufruft (**opscenter.sh**), die für die Einrichtung des OpsCenter-Diensts auf diesem virtuellen Computer erforderlich ist.
-	**ephemeral-nodes-resources.json**: stellt alle Clusterknoten-VMs und verbundene Ressourcen (Netzwerkkarten, private IP-Adressen usw.) bereit. Diese Vorlage stellt auch VM-Erweiterungen bereit (benutzerdefinierte Skripts für Linux) und ruft ein Bash-Skript (**dsenode.sh**) ab, um Apache Cassandra-Bits auf jedem Knoten physisch zu installieren.

Sehen wir uns nun die Verwendung der letzten Vorlage genauer an, da diese aus Sicht der Vorlagenentwicklung am interessantesten ist. Ein wichtiges hervorzuhebendes Konzept ist, wie eine einzelne Vorlagendatei mehrere Kopien eines einzelnen Ressourcentyps bereitstellen und für jede Instanz eindeutige Werte für die erforderlichen Einstellungen festgelegen kann. Dieses Konzept ist als **Ressourcenschleife** bekannt.

Wenn **ephemeral-nodes-resources.json** aus der Hauptdatei **azuredeploy.json** aufgerufen wird, wird der Parameter **nodeCount** als Teil der Parameterliste bereitgestellt. Innerhalb der untergeordneten Vorlage wird dieser Parameter (Anzahl der bereitzustellenden Knoten im Cluster) innerhalb des **copy**-Elements jeder einzelnen Ressource benutzt, die in mehreren Kopien bereitgestellt werden muss, wie im folgenden Fragment verdeutlicht wird. Für alle Einstellungen, in denen es erforderlich ist, eindeutige Werte zwischen verschiedenen Instanzen der bereitgestellten Ressource anzugeben, kann die **copyindex()**-Funktion verwendet werden, um einen numerischen Wert zu erhalten, der den aktuellen Index in der bestimmten Ressourcenschleifenerstellung angibt. Im folgenden Fragment können Sie sehen, wie dieses Konzept auf die Erstellung mehrerer VMs für DataStax-Clusterknoten angewendet wird:

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

Ein weiteres wichtiges Konzept bei der Ressourcenerstellung ist die Möglichkeit, Abhängigkeiten und Vorränge zwischen Ressourcen anzugeben, wie Sie am JSON-Array **dependsOn** sehen können. In dieser Vorlage verfügt jeder Knoten zusätzlich über einen angeschlossenen Datenträger mit 1 TB (siehe "dataDisks"), der zum Hosten von Backups und Momentaufnahmen von der Apache Cassandra-Instanz verwendet werden kann.

Angeschlossene Datenträger werden als Teil der Aktivitäten zur Vorbereitung der Knoten formatiert, die durch das Ausführen der Skriptdatei **dsenode.sh** ausgelöst werden. Die erste Zeile des Skripts ruft ein weiteres Skript auf:

	bash vm-disk-utils-0.1.sh

"vm-disk-utils-0.1.sh" ist im Ordner **shared_scripts\ubuntu** im GitHub-Repository "azure-quickstart-tempates" enthalten und bietet nützliche Funktionen für die Bereitstellung von Datenträgern, die Formatierung und das Striping. Diese Funktionen können in allen Vorlagen im Repository verwendet werden.

Ein weiteres interessantes Fragment ist das im Zusammenhang mit den VM-Erweiterungen CustomScriptForLinux. Diese werden als gesonderter Ressourcentyp mit einer Abhängigkeit auf jedem Clusterknoten (und der OpsCenter-Instanz) installiert. Sie nutzen denselben Ressourcenschleifenmechanismus, der im Zusammenhang mit virtuellen Computern beschrieben wurde:

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

Indem Sie sich mit den anderen Dateien in dieser Bereitstellung vertraut machen, werden Sie alle Details und bewährten Methoden zum Organisieren und Orchestrieren komplexer Bereitstellungsstrategien für Lösungen mit mehreren Knoten verstehen, basierend auf sämtlichen Technologien und unter Nutzung von Azure-Ressourcen-Manager-Vorlagen. Obwohl nicht zwingend erforderlich, ist das Strukturieren der Vorlagendateien eine empfohlene Vorgehensweise, wie im folgenden Diagramm verdeutlicht:

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

Im Wesentlichen empfiehlt dieser Ansatz:

-	Definieren Sie die Kernvorlagendatei als zentrale Orchestrierungsstelle für alle spezifischen Bereitstellungsaktivitäten, indem Sie Vorlagen nutzen, die auf Aufrufe von untergeordneten Vorlagenausführungen verweisen.
-	Erstellen Sie eine spezifische Vorlagendatei, die alle Ressourcen bereitstellt, die von allen anderen speziellen Bereitstellungsaufgaben gemeinsam genutzt werden (z. B. Speicherkonten, VNET-Konfiguration usw.). Diese kann häufig zwischen Bereitstellungen wiederverwendet werden, die ähnliche Anforderungen im Hinblick auf die allgemeine Infrastruktur haben.
-	Schließen Sie optionale Ressourcenvorlagen ein, um ressourcenspezifische Anforderungen zu erkennen.
-	Erstellen Sie für identische Mitglieder einer Ressourcengruppe (Knoten in einem Cluster usw.) spezielle Vorlagen, die die Ressourcenschleife nutzen, um für mehrere Instanzen eindeutige Eigenschaften bereitzustellen.
-	Für alle Nachbereitstellungsaufgaben (z. B. Produktinstallation, Konfigurationen usw.) nutzen Sie die Bereitstellung von Skripterweiterungen, und erstellen Sie Skripts, die für die einzelnen Technologien spezifisch sind.

Weitere Informationen finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn835138.aspx).

<!---HONumber=58-->