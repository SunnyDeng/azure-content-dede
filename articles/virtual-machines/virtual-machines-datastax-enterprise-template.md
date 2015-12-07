<properties
	pageTitle="DataStax Enterprise auf Ubuntu mit einer Ressourcen-Manager-Vorlage | Microsoft Azure"
	description="Erfahren Sie, wie Sie auf einfache Weise auf Ubuntu-VMs mit Azure PowerShell oder der Azure-CLI und einer Ressourcen-Manager-Vorlage einen neuen DataStax Enterprise-Cluster bereitstellen"
	services="virtual-machines"
	documentationCenter=""
	authors="scoriani"
	manager="timlt"
	editor="tysonn"/>
<!-- In pageTitle, to follow corporate style (sentence-case caps), s/b lowercase "template", correct? This matches what is used later in article too. Also, precede first mention of "Azure" with "Microsoft" -->
<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="scoriani"/>

# DataStax Enterprise auf Ubuntu mit einer Ressourcen-Manager-Vorlage

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.


DataStax ist ein anerkannter Branchenführer bei der Entwicklung und Bereitstellung von Lösungen, die auf dem kommerziell unterstützten, für den Unternehmenseinsatz bereiten Apache Cassandra™ basieren. Diese verteilte NoSQL-Datenbanktechnologie ist weithin anerkannt als agil, immer funktionsbereit und vorhersehbar auf jede Größe skalierbar. DataStax bietet die Versionen Enterprise (DSE) und Community (DSC) an. Zusätzlich zu den Funktionen der Community-Edition bietet DataStax Enterprise Cassandra für die Produktion zertifiziert mit Funktionen wie speicherinternem Computing, unternehmensweiter Sicherheit, schnellen und leistungsfähigen integriertn Analysen und Unternehmensverwaltung an.

>[AZURE.NOTE]Im Gegensatz zur Community-Edition benötigen Sie zum Bereitstellen von DataStax Enterprise ein gültiges DataStax-Konto (Benutzername und Kennwort), die während der Vorlagenbereitstellung als Parameter übergeben werden. Besuchen Sie die [Website von DataStax](http://www.datastax.com) zur Einrichtung Ihres Kontos, wenn Sie nicht bereits eines besitzen.

Zusätzlich zu dem, was bereits in Azure Marketplace verfügbar war, können Sie jetzt auch auf einfache Weise auf Ubuntu-VMs mit [Azure PowerShell](../powershell-install-configure.md) oder der [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) mithilfe einer Vorlage für Ressourcen-Manager einen neuen DataStax Enterprise-Cluster bereitstellen.

Neu bereitgestellte Cluster auf Basis dieser Vorlage besitzen die im folgenden Diagramm beschriebene Topologie, auch wenn andere Topologien leicht durch das Anpassen der in diesem Artikel gezeigten Vorlage erreicht werden können.

![cluster-architecture](media/virtual-machines-datastax-enterprise-template/cluster-architecture.png)

Mithilfe von Parametern können Sie die Anzahl der Knoten, die im neuen Apache Cassandra-Cluster bereitgestellt werden, definieren. Eine Instanz des Operation Center-Diensts von DataStax wird auch auf einem eigenständigen virtuellen Computer innerhalb des gleichen VNETs bereitgestellt, was Ihnen die Möglichkeit gibt, den Clusterstatus und jeden einzelnen Knoten zu überwachen, Knoten hinzuzufügen/zu entfernen und alle administrativen Aufgaben im Zusammenhang mit diesem Cluster durchzuführen.

Nachdem die Bereitstellung abgeschlossen ist, können Sie mithilfe der konfigurierten DNS-Adresse auf die DataStax Operations Center-VM-Instanz zugreifen. Bei der OpsCenter-VM ist der SSH-Port 22 aktiviert sowie der Port 8443 für HTTPS. Die DNS-Adresse für das Operations Center enthält den *DNS-Namen* und die *Region*, die als Parameter übergeben werden. Daraus ergibt sich folgendes Format: `{dnsName}.{region}.cloudapp.azure.com`. Wenn Sie eine Bereitstellung mit dem *dnsName*-Parameter festgelegt auf "datastax" in der Region "West US" erstellt haben, können Sie auf den virtuellen Computer von DataStax Operations Center für die Bereitstellung über `https://datastax.westus.cloudapp.azure.com:8443` zugreifen.

> [AZURE.NOTE]Das in der Bereitstellung verwendete Zertifikat ist ein eigensigniertes Zertifikat, das eine Browser-Warnung erzeugt. Auf der [DataStax](http://www.datastax.com/)-Website können Sie nachverfolgen, wie das Zertifikat durch Ihr eigenes SSL-Zertifikat ersetzt wird.

Bevor wir zu näheren Einzelheiten zum Azure-Ressourcen-Manager und der für diese Bereitstellung verwendeten Vorlage kommen, stellen Sie sicher, dass Azure PowerShell oder die Azure-Befehlszeilenschnittstelle konfiguriert und einsatzbereit ist.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Erstellen von auf DataStax Enterprise basierenden Cassandra-Clustern mit einer Ressourcen-Manager-Vorlage

Gehen folgendermaßen Sie vor, um einen auf DataStax Enterprise basierenden Apache Cassandra-Cluster mithilfe einer Ressourcen-Manager-Vorlage im GitHub-Vorlagenrepository zu erstellen. Jeder Schritt umfasst Anweisungen für Azure PowerShell und die Azure-Befehlszeilenschnittstelle.

### Schritt 1-a: Herunterladen der Vorlagendateien mithilfe von PowerShell

Erstellen Sie einen lokalen Ordner für die JSON-Vorlage und andere zugehörige Dateien (z. B. "C:\\Azure\\Vorlagen\\DataStax").

Fügen Sie dabei den Namen Ihres lokalen Ordners ein, und führen Sie den nächsten Satz von Befehlen aus.

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

### Schritt 1-b: Herunterladen der Vorlagendateien mithilfe der Azure-Befehlszeilenschnittstelle

Klonen Sie das komplette Vorlagenrepository mithilfe eines Git-Clients Ihrer Wahl, wie im folgenden Beispiel dargestellt.

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Sobald dies abgeschlossen ist, suchen Sie im Verzeichnis "C:\\Azure\\Vorlagen" nach dem Ordner "datastax-enterprise". <!--Wrapping name of folder in bold typeface is not corp style  -->
### Schritt 2 (optional): Erlernen der Vorlagenparameter

Wenn Sie schwierige Lösungen wie einen auf DataStax basierenden Apache Cassandra-Cluster bereitstellen, müssen Sie eine Reihe von Konfigurationsparametern für den Umgang mit unterschiedlichen erforderlichen Einstellungen angeben. Durch das Deklarieren dieser Parameter in der Vorlagendefinition ist es möglich, Werte während der Bereitstellung über eine externe Datei oder an der Befehlszeile anzugeben.

Im Abschnitt "parameters" am Anfang der Datei "azuredeploy.json" finden Sie den Parametersatz, der von der Vorlage zum Konfigurieren eines DataStax Enterprise-Clusters verlangt wird. Nachfolgend sehen Sie ein Beispiel für den Abschnitt "parameters" aus der Datei "azuredeploy.json" dieser Vorlage.

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
			"datastaxUsername": {
				"type": "string",
				"metadata": {
					"description": "Your Datastax account username.  If you do not have one go to (datastax.com)"
				}
			},
			"datastaxPassword": {
				"type": "securestring",
				"metadata": {
					"description": "Your Datastax account password"
				}
			},
			"opsCenterAdminPassword": {
				"type": "securestring",
				"metadata": {
					"description": "Sets the operations center admin user password.  You will use this when you login to the operations center portal"
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

### Schritt 3-a: Bereitstellen eines DataStax Enterprise-Clusters mit einer Vorlage mithilfe von PowerShell

Bereiten Sie eine Parameterdatei für die Bereitstellung vor, indem Sie eine JSON-Datei mit den Laufzeitwerten für alle Parameter erstellen. Diese Datei wird dann als eine Entität an den Bereitstellungsbefehl übergeben. Wenn Sie keine Parameterdatei einfügen, verwendet PowerShell die in der Vorlage angegebenen Standardwerte und fordert Sie auf, die restlichen Werte einzugeben.

Das nächste Beispiel stellt einen Satz von Parametern aus der Datei "azuredeploy-parameters.json" dar:

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
		"datastaxUsername": {
			"value": "scorianidx"
		},
		"datastaxPassword": {
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

Geben Sie Namen für die Azure-Bereitstellung, die Ressourcengruppe, den Azure-Speicherort sowie den Ordner mit der gespeicherten JSON-Bereitstellungsdatei an. Führen Sie dann den nächsten Satz von Befehlen aus.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Beim Ausführen des Befehls **New-AzureResourceGroupDeployment** werden damit die Parameterwerte aus der JSON-Parameterdatei extrahiert und die Ausführung der Vorlage entsprechend gestartet. Das Definieren und Verwenden mehrerer Parameterdateien in den verschiedenen Umgebungen (z. B. Test oder Produktion) fördert die Wiederverwendung von Vorlagen und vereinfacht komplexe Lösungen mit mehreren Umgebungen.

Denken Sie beim Bereitstellen daran, dass ein neues Azure-Speicherkonto erstellt werden muss, daher muss der Name, den Sie als Speicherkontoparameter angeben, eindeutig sein und alle Anforderungen an ein Azure-Speicherkonto erfüllen (nur Kleinbuchstaben und Ziffern).

Während und nach der Bereitstellung können Sie alle Anforderungen überprüfen, die während der Bereitstellung vorgenommen wurden, einschließlich der aufgetretenen Fehler.

Öffnen Sie dazu das [Azure-Vorschauportal](https://portal.azure.com), und gehen Sie folgendermaßen vor:

- Klicken Sie auf der linken Navigationsleiste auf "Durchsuchen", scrollen Sie nach unten, und klicken Sie auf "Ressourcengruppen".
- Nach dem Klicken auf die Ressourcengruppe, die Sie gerade erstellt haben, wird das Blatt "Ressourcengruppe" angezeigt.
- Durch Klicken auf das Balkendiagramm "Ereignisse" im Bereich "Überwachung" des Blattes "Ressourcengruppe" werden die Ereignisse für die Bereitstellung angezeigt:
- Durch Klicken auf die einzelnen Ereignisse können Sie sich die Details jedes einzelnen Vorgangs, der von der Vorlage angestoßen wurde, noch näher ansehen.

Verwenden Sie nach den Tests zum Entfernen dieser Ressourcengruppe und all ihrer Ressourcen (Speicherkonto, virtueller Computer, virtuelles Netzwerk) den nächsten Befehl.

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Schritt 3-b: Bereitstellen eines DataStax Enterprise-Clusters mit einer Vorlage mithilfe der Azure-Befehlszeilenschnittstelle

Zum Bereitstellen eines DataStax Enterprise-Clusters über die Azure-CLI erstellen Sie zuerst eine Ressourcengruppe durch Angabe eines Namens und eines Speicherorts. Verwenden Sie dazu den nächsten Befehl.

	azure group create dsc "West US"

Übergeben Sie diesen Namen für die Ressourcengruppe, den Speicherort der JSON-Vorlagendatei und den Speicherort der Parameterdatei (siehe Abschnitt oben zur PowerShell) an den folgenden Befehl.

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Sie können den Status der einzelnen Ressourcenbereitstellungen mit dem folgenden Befehl überprüfen.

	azure group deployment list dsc

## Übersicht über die DataStax Enterprise-Vorlagenstruktur und -Dateianordnung

Um eine stabile und wiederverwendbare Ressourcen-Manager-Vorlage zu erstellen, ist es erforderlich, genau über eine Reihe komplexer und zusammenhängender Aufgaben während der Bereitstellung einer komplexen Lösung wie DataStax Enterprise nachzudenken. Durch die Nutzung von ARM-Funktionen zum **Verknüpfen von Vorlagen** und **Ressourcenschleifen** und die zusätzliche Skriptausführung über zugehörige Erweiterungen ist es möglich, einen modularen Ansatz zu implementieren, der bei nahezu allen komplexen vorlagenbasierten Bereitstellungen wiederverwendet werden kann. <!-- In previous paragraph, we can't use bold typeface to show emphasis. You can use italic to denote emphasis. --> Im nächsten Diagramm werden die Beziehungen zwischen allen Dateien beschrieben, die für diese Bereitstellung von GitHub heruntergeladen werden.

![datastax-enterprise-files](media/virtual-machines-datastax-enterprise-template/datastax-enterprise-files.png)

Dieser Abschnitt führt Sie schrittweise durch die Struktur der Datei "azuredeploy.json" für den DataStax Enterprise-Cluster.

### Abschnitt "parameters"

Im Abschnitt "parameters" von "azuredeploy.json" werden veränderbare Parameter angegeben, die in dieser Vorlage verwendet werden. Die oben genannte Datei "azuredeploy-parameters.json" wird während der Vorlagenausführung zum Übergeben der Werte in den Abschnitt "parameters" von "azuredeploy.json" verwendet.

### Abschnitt "Variablen"

Im Abschnitt "variables" sind Variablen angegeben, die in dieser Vorlage verwendet werden können. Er enthält eine Reihe von Feldern (JSON-Datentypen oder -Fragmente), die zum Zeitpunkt der Ausführung auf Konstanten oder berechnete Werte festgelegt werden. Das nächste Beispiel stellt den Abschnitt "variables" für diese DataStax-Vorlage dar.

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-enterprise/",
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

Im vorherigen Beispiel erkennen Sie zwei verschiedene Ansätze. Im ersten Fragment ist die "osSettings"-Variable ein geschachteltes JSON-Element mit vier Schlüssel-Wert-Paaren.

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
Im zweiten Fragment ist stattdessen die "scripts"-Variable ein JSON-Array, in dem einzelne Elemente zur Laufzeit mithilfe einer Vorlagensprachenfunktion ("concat") und dem Wert einer anderen Variablen plus Zeichenfolgenkonstanten berechnet werden.

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

### Abschnitt "Ressourcen"

Im Abschnitt "resources" geschieht am meisten. Schauen Sie sich diesen Abschnitt an. Sie können sofort zwei verschiedene Fälle identifizieren: Der erste ist ein definiertes Element des Typs `Microsoft.Resources/deployments`, was im Grunde den Aufruf einer geschachtelten Bereitstellung innerhalb der Hauptbereitstellung bedeutet. Durch das Element "templateLink" (und die zugehörige Versionseigenschaft) kann eine verknüpfte Vorlagendatei bestimmt werden, die zur Übergabe eines Parametersatzes als Eingabe aufgerufen wird, wie im nächsten Beispiel dargestellt.

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

Im ersten Beispiel wird deutlich, wie die Datei "azuredeploy.json" in diesem Szenario als Mechanismus zur Orchestrierung organisiert wurde, indem eine Anzahl anderer Vorlagendateien aufgerufen wird, wobei jede jeweils für einen Teil der erforderlichen Bereitstellungsaufgaben verantwortlich ist.

Insbesondere die folgenden verknüpften Vorlagen werden für diese Bereitstellung verwendet: <!-- In list format, using bold typeface in the following manner is ok --> – **shared-resource.json**: Enthält die Definition aller Ressourcen, die in der gesamten Bereitstellung freigegeben werden sollen. Beispiele hierfür sind Speicherkonten, die zum Speichern der Betriebssystem-Datenträger und virtuellen Netzwerke des virtuellen Computers verwendet werden. – **opscenter-resources.json**: Stellt eine OpsCenter-VM und alle zugehörigen Ressourcen bereit, einschließlich einer Netzwerkschnittstelle und einer öffentlichen IP-Adresse. – **opscenter-install-resources.json**: Stellt die OpsCenter-VM-Erweiterung (benutzerdefiniertes Skript für Linux) bereit, die die spezifische Bash-Skriptdatei ("opscenter.sh") aufruft, das zum Einrichten des OpsCenter-Diensts innerhalb dieses virtuellen Computers erforderlich ist. – **ephemeral-nodes-resources.json**: Stellt alle Clusterknoten-VMs und verbundenen Ressourcen bereit (z. B. Netzwerkkarten und private IPs). Diese Vorlage stellt auch VM-Erweiterungen bereit (benutzerdefinierte Skripts für Linux) und ruft ein Bash-Skript ("dsenode.sh") ab, um Apache Cassandra-Bits auf jedem Knoten physisch zu installieren.

Sehen wir uns nun die Verwendung der letzten Vorlage genauer an, da diese aus Sicht der Vorlagenentwicklung am interessantesten ist. Ein wichtiges hervorzuhebendes Konzept ist, wie eine einzelne Vorlagendatei mehrere Kopien eines einzelnen Ressourcentyps bereitstellen und für jede Instanz eindeutige Werte für die erforderlichen Einstellungen festgelegen kann. Dieses Konzept ist als "Ressourcenschleife" bekannt.

Wenn "ephemeral-nodes-resources.json" aus der Hauptdatei "azuredeploy.json" aufgerufen wird, wird der Parameter "nodeCount" als Teil der Parameterliste bereitgestellt. Innerhalb der untergeordneten Vorlage wird dieser Parameter (Anzahl der bereitzustellenden Knoten im Cluster) innerhalb des **copy**-Elements jeder einzelnen Ressource verwendet, die in mehreren Kopien bereitgestellt werden muss, wie im nächsten Beispiel verdeutlicht wird. Für alle Einstellungen, in denen es erforderlich ist, eindeutige Werte zwischen verschiedenen Instanzen der bereitgestellten Ressource anzugeben, kann die **copyindex()**-Funktion verwendet werden, um einen numerischen Wert zu erhalten, der den aktuellen Index in der bestimmten Ressourcenschleifenerstellung angibt. Im nächsten Beispiel sehen Sie, wie dieses Konzept auf die Erstellung mehrerer VMs für DataStax Enterprise-Clusterknoten angewendet wird.

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

Angeschlossene Datenträger werden als Teil der Aktivitäten zur Vorbereitung der Knoten formatiert, die durch das Ausführen der Skriptdatei "dsenode.sh" ausgelöst werden. Im nächsten Beispiel wird durch die erste Zeile des Skripts ein weiteres Skript aufgerufen.

	bash vm-disk-utils-0.1.sh

"vm-disk-utils-0.1.sh" ist im Ordner "shared\_scripts\\ubuntu" im GitHub-Repository "azure-quickstart-tempates" enthalten und bietet nützliche Funktionen für die Bereitstellung von Datenträgern, die Formatierung und das Striping. Diese Funktionen können in allen Vorlagen im Repository verwendet werden.

Ein weiteres interessantes Fragment ist das im Zusammenhang mit den VM-Erweiterungen CustomScriptForLinux. Diese werden als gesonderter Ressourcentyp mit einer Abhängigkeit auf jedem Clusterknoten (und der OpsCenter-Instanz) installiert. Sie nutzen denselben Ressourcenschleifenmechanismus, der im Zusammenhang mit virtuellen Computern beschrieben wurde.

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

Indem Sie sich mit den anderen Dateien in dieser Bereitstellung vertraut machen, werden Sie alle Details und bewährten Methoden zum Organisieren und Orchestrieren komplexer Bereitstellungsstrategien für Lösungen mit mehreren Knoten verstehen, basierend auf sämtlichen Technologien und unter Nutzung von Azure-Ressourcen-Manager-Vorlagen. Obwohl nicht zwingend erforderlich, ist das Strukturieren der Vorlagendateien eine empfohlene Vorgehensweise, wie im folgenden Diagramm dargestellt.

![datastax-enterprise-template-structure](media/virtual-machines-datastax-enterprise-template/datastax-enterprise-template-structure.png)

Dieser Ansatz empfiehlt folgende Schritte:

-	Definieren Sie die Kernvorlagendatei als zentrale Orchestrierungsstelle für alle spezifischen Bereitstellungsaktivitäten, indem Sie Vorlagen nutzen, die auf Aufrufe von untergeordneten Vorlagenausführungen verweisen.
-	Erstellen Sie eine spezifische Vorlagendatei, die alle Ressourcen bereitstellt, die gemeinsam von allen anderen speziellen Bereitstellungsaufgaben gemeinsam genutzt werden (z. B. Speicherkonten und VNET-Konfiguration). Diese kann häufig zwischen Bereitstellungen wiederverwendet werden, die ähnliche Anforderungen im Hinblick auf die allgemeine Infrastruktur haben.
-	Schließen Sie optionale Ressourcenvorlagen ein, um ressourcenspezifische Anforderungen zu erkennen.
-	Erstellen Sie für identische Mitglieder einer Ressourcengruppe (z. B. Knoten in einem Cluster) spezielle Vorlagen, die die Ressourcenschleife nutzen, um für mehrere Instanzen eindeutige Eigenschaften bereitzustellen.
-	Für alle Nachbereitstellungsaufgaben (z. B. Produktinstallation und Konfigurationen) nutzen Sie die Bereitstellung von Skripterweiterungen, und erstellen Sie Skripts, die für die einzelnen Technologien spezifisch sind.

Weitere Informationen finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](../resource-group-authoring-templates.md).

<!---HONumber=AcomDC_1125_2015-->