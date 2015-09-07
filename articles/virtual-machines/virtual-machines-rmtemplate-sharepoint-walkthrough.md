<properties
	pageTitle="Ressourcen-Manager-Vorlage für die SharePoint-Farm mit drei Servern"
	description="Untersuchen Sie schrittweise die Struktur der Vorlagendatei azuredeploy.json für die SharePoint-Farm mit drei Servern."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Ressourcen-Manager-Vorlage für die SharePoint-Farm mit drei Servern

Dieses Thema führt Sie schrittweise durch die Struktur der Vorlagendatei "azuredeploy.json" für die SharePoint-Farm mit drei Servern. Von [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json) aus können Sie den Inhalt dieser Vorlage in Ihrem Browser anzeigen.

Stattdessen können Sie eine lokale Kopie der Datei "azuredeploy.json" untersuchen, indem Sie einen lokalen Ordner als Speicherort für die Datei festlegen und sie dann erstellen (z. B. C:\\Azure\\Templates\\SharePointFarm). Geben Sie den Ordnernamen ein, und führen Sie diese Befehle an der Azure PowerShell- Eingabeaufforderung aus.

	$folderName="<folder name, such as C:\Azure\Templates\SharePointFarm>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

Öffnen Sie die Vorlage "azuredeploy.json" in einem Texteditor oder Tool Ihrer Wahl. Im Folgenden werden die Struktur der Vorlagendatei und der Zweck der einzelnen Abschnitte beschrieben.

## Abschnitt "parameters"

Im Abschnitt **"Parameter"** werden die Parameter angegeben, mit denen Daten in eine Vorlage eingegeben werden. Wenn die Vorlage ausgeführt wird, müssen Sie die Daten angeben. Es können bis zu 50 Parameter definiert werden. Im Folgenden finden Sie ein Beispiel für einen Parameter für den Azure-Speicherort:

	"deploymentLocation": {
		"type": "string",
		"allowedValues": [
			"West US",
			"East US",
			"West Europe",
			"East Asia",
			"Southeast Asia"
		],
		"metadata": {
			"Description": "The region to deploy the resources into"
		},
		"defaultValue":"West Europe"
	},

## Abschnitt "Variablen"

Der Abschnitt **"Variablen"** gibt Variablen und deren Werte an, die die Vorlage verwendet. Variablenwerte können explizit festgelegt oder von Parameterwerten abgeleitet werden. Im Gegensatz zu Parametern stellen Sie diese nicht bereit, wenn Sie die Vorlage ausführen. Es können bis zu 100 Variablen definiert werden. Hier einige Beispiele:

	"LBFE": "LBFE",
	"LBBE": "LBBE",
	"RDPNAT": "RDP",
	"spWebNAT": "spWeb",
	"adSubnetName": "adSubnet",
	"sqlSubnetName": "sqlSubnet",
	"spSubnetName": "spSubnet",
	"adNicName": "adNic",
	"sqlNicName": "sqlNic",
	"spNicName": "spNic",

## Abschnitt "Ressourcen"

Im Abschnitt **"Ressourcen"** werden Informationen festgelegt, die benötigt werden, um die Ressourcen für die SharePoint-Farm in einer Ressourcengruppe bereitstellen. Es lassen sich maximal 250 Ressourcen definieren, und Ressourcenabhängigkeiten können nur fünf Ebenen tief definiert sein.

Dieser Abschnitt enthält die folgenden Unterabschnitte:

### Microsoft.Storage/storageAccounts  

In diesem Abschnitt wird ein neues Speicherkonto für alle Datenträger- und VHD-Ressourcen für die Farm erstellt. Hier ist der JSON-Code für das Speicherkonto:

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2015-05-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

In diesen Abschnitten erstellen Sie einen Satz von öffentlichen IP-Adressen, mit denen die einzelnen virtuellen Computer über das Internet erreicht werden können. Beispiel:

	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('adpublicIPAddressName')]",
		"location": "[parameters('deploymentLocation')]",
		"properties": {
			"publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
			"dnsSettings": {
				"domainNameLabel": "[parameters('adDNSPrefix')]"
			}
		}
	},

### Microsoft.Compute/availabilitySets

Diese Abschnitte erstellen drei Verfügbarkeitsgruppen, eine für jede Bereitstellungsebene:

- Active Directory-Domänencontroller
- SQL Server-Cluster
- SharePoint-Server

Beispiel:

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

In diesen Abschnitten wird ein virtuelles Nur-Cloud-Netzwerk mit drei Subnetzen erstellt (eines für jede Bereitstellungsebene), in dem sich die virtuellen Computer befinden. Hier finden Sie den JSON-Code:

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkAddressRange')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},


### Microsoft.Network/loadBalancers

In diesen Abschnitten werden Lastenausgleichsinstanzen für jeden virtuellen Computer erstellt, die Netzwerkadressenübersetzung (kurz NAT) und Filterung für eingehenden Datenverkehr aus dem Internet bereitstellen. Für jedes Lastenausgleichssystem werden Front-End- und Back-End-Einstellungen sowie NAT-Regeln für eingehenden Datenverkehr festgelegt. Es gibt z. B. Remotedesktop-Verkehrsregeln für jeden virtuellen Computer und eine Regel zum Zulassen von eingehendem Webdatenverkehr (TCP-Port 80) über das Internet für den SharePoint-Server. Es folgt ein Beispiel für den SharePoint-Server:


	{
		"apiVersion": "2015-05-01-preview",
		"name": "[variables('spLBName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "[variables('LBFE')]",
					"properties": {
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "[variables('LBBE')]"
				}
			],
			"inboundNatRules": [
				{
					"name": "[variables('RDPNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": "[parameters('RDPPort')]",
						"backendPort": 3389,
						"enableFloatingIP": false
					}
				},
				{
					"name": "[variables('spWebNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": 80,
						"backendPort": 80,
						"enableFloatingIP": false
					}
				}
			]
		}
	},

### Microsoft.Network/networkInterfaces

In diesen Abschnitten wird eine Netzwerkschnittstelle für die einzelnen virtuellen Computer erstellt, und anschließend wird eine statische IP-Adresse für den Domänencontroller konfiguriert. Es folgt das Beispiel für die Netzwerkschnittstelle des Domänencontrollers:

	{
		"name": "[variables('adNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('adlbName'))]"
		],
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress": "[parameters('adNicIPAddress')]",
						"subnet": {
							"id": "[variables('adSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id": "[variables('adBEAddressPoolID')]"
							}
						],
						"loadBalancerInboundNatRules": [
							{
								"id": "[variables('adRDPNATRuleID')]"
							}
						]
					}
				}
			]
		}
	},


### Microsoft.Compute/virtualMachines

Diese Abschnitte erstellen und konfigurieren die drei virtuellen Computer in der Bereitstellung.

Im ersten Abschnitt wird der Domänencontroller erstellt und konfiguriert, der:

- das Speicherkonto, den Verfügbarkeitssatz, die Netzwerkschnittstelle und die Lastenausgleichsinstanz angibt.
- einen zusätzlichen Datenträger hinzufügt.
- ein PowerShell-Skript ausführt, um den Domänencontroller zu konfigurieren.

Hier finden Sie den JSON-Code:

		{
			"apiVersion": "2015-05-01-preview",
			"type": "Microsoft.Compute/virtualMachines",
			"name": "[parameters('adVMName')]",
			"location": "[parameters('deploymentLocation')]",
			"dependsOn": [
				"[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
				"[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]",
				"[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
				"[resourceId('Microsoft.Network/loadBalancers',variables('adlbName'))]"
			],
			"properties": {
				"hardwareProfile": {
					"vmSize": "[parameters('adVMSize')]"
				},
				"availabilitySet": {
					"id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
				},
				"osProfile": {
					"computername": "[parameters('adVMName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"storageProfile": {
					"imageReference": {
						"publisher": "[parameters('adImagePublisher')]",
						"offer": "[parameters('adImageOffer')]",
						"sku": "[parameters('adImageSKU')]",
						"version": "latest"
					},
					"osDisk": {
						"name": "osdisk",
						"vhd": {
							"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/',parameters('adVMName'),'-osdisk.vhd')]"
						},
						"caching": "ReadWrite",
						"createOption": "FromImage"
					},
					"dataDisks": [
						{
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adDataDisk'),'-1.vhd')]"
							},
							"name": "[concat(parameters('adVMName'),'-data-disk1')]",
							"caching": "None",
							"createOption": "empty",
							"diskSizeGB": "[variables('adDataDiskSize')]",
							"lun": 0
						}
					]
				},
				"networkProfile": {
					"networkInterfaces": [
						{
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]"
						}
					]
				}
			},
			"resources": [
				{
					"type": "Microsoft.Compute/virtualMachines/extensions",
					"name": "[concat(parameters('adVMName'),'/InstallDomainController')]",
					"apiVersion": "2015-05-01-preview",
					"location": "[parameters('deploymentLocation')]",
					"dependsOn": [
						"[resourceId('Microsoft.Compute/virtualMachines', parameters('adVMName'))]"
					],
					"properties": {
						"publisher": "Microsoft.Powershell",
						"type": "DSC",
						"typeHandlerVersion": "1.7",
						"settings": {
							"ModulesUrl": "[variables('adModulesURL')]",
							"ConfigurationFunction": "[variables('adConfigurationFunction')]",
							"Properties": {
								"DomainName": "[parameters('domainName')]",
								"AdminCreds": {
									"UserName": "[parameters('adminUserName')]",
									"Password": "PrivateSettingsRef:AdminPassword"
								}
							}
						},
						"protectedSettings": {
							"Items": {
								"AdminPassword": "[parameters('adminPassword')]"
							}
						}
					}
				}
			]
		},

In einem zusätzlichen Abschnitt für den Domänencontroller, der mit **"name": "UpdateVNetDNS"** beginnt, wird der DNS-Server des virtuellen Netzwerks für die Verwendung der statischen IP-Adressen des Domänencontrollers konfiguriert.

Im folgenden Abschnitt **"type": "Microsoft.Compute/virtualMachines"** werden die virtuellen Computer des SQL Server-Clusters in der Bereitstellung erstellt. Außerdem wird hier:

- das Speicherkonto, der Verfügbarkeitssatz, der Lastenausgleich, das virtuelle Netzwerk und die Netzwerkschnittstelle angegeben.
- ein zusätzlicher Datenträger hinzufügt.

In zusätzlichen **"Microsoft.Compute/virtualMachines/extensions"**-Abschnitten wird das PowerShell-Skript zum Konfigurieren des SQL-Servers aufgerufen.

Im nächsten **"type": "Microsoft.Compute/virtualMachines"**-Abschnitt werden der virtuelle SharePoint-Computer in der Bereitstellung erstellt und das Speicherkonto, der Verfügbarkeitssatz, das Lastenausgleichsmodul, das virtuelle Netzwerk und Netzwerkschnittstelle angegeben. In einem zusätzlichen "Microsoft.Compute/virtualMachines/extensions"-Abschnitt wird das PowerShell-Skript zum Konfigurieren der SharePoint-Farm aufgerufen.

Beachten Sie die allgemeine Organisation der Unterabschnitte des Abschnitts **"Ressourcen"** der JSON-Datei:

1.	Erstellen Sie die Elemente der Azure-Infrastruktur, die für die Unterstützung mehrerer virtueller Computer erforderlich sind (ein Speicherkonto, öffentliche IP-Adressen, Verfügbarkeitsgruppen, ein virtuelles Netzwerk, Netzwerkschnittstellen, und Lastenausgleichsinstanzen).
2.	Erstellen Sie den virtuellen Computer des Domänencontrollers, der die zuvor erstellten allgemeinen und spezifischen Elemente der Azure-Infrastruktur verwendet, fügen Sie Datenträger hinzu, und führen Sie ein PowerShell-Skript aus. Aktualisieren Sie außerdem das virtuelle Netzwerk, sodass die statische IP-Adresse des Domänencontrollers verwendet wird.
3.	Erstellen Sie den virtuellen Computer des SQL Servers, der die zuvor für die Domänencontroller erstellten allgemeinen und spezifischen Elemente der Azure-Infrastruktur verwendet, fügen Sie Datenträger hinzu, und führen Sie ein PowerShell-Skript zum Konfigurieren von SQL Server aus.
4.	Erstellen Sie den virtuellen Computer des SharePoint-Servers, der die zuvor erstellten allgemeinen und spezifischen Elemente der Azure-Infrastruktur verwendet, und führen Sie ein PowerShell-Skript zum Konfigurieren der SharePoint-Farm aus.

Bei Ihren eigenen JSON-Vorlagen zum Erstellen einer Infrastruktur mit mehreren Ebenen in Azure sollten Sie die gleichen Schritte ausführen:

1.	Erstellen Sie die allgemeinen Elemente (Speicherkonto, virtuelles Netzwerk), spezifische Elemente für die Ebene (Verfügbarkeitsgruppen) und spezifische Elemente für die virtuellen Computer (öffentliche IP-Adressen, Verfügbarkeitsgruppen, Netzwerkschnittstellen und Lastenausgleichsinstanzen) der Azure-Infrastruktur, die für die Bereitstellung erforderlich sind.
2.	Erstellen Sie für jede Ebene in der Anwendung (z. B. Authentifizierung , Datenbank, Web) die Server in dieser Ebene mithilfe der allgemeinen Elemente (Speicherkonto, virtuelles Netzwerk), der spezifischen Elemente für die Ebene (Verfügbarkeitsgruppe) und der spezifischen Elemente für die virtuellen Computer (öffentliche IP-Adressen, Netzwerkschnittstellen, Lastenausgleichsinstanzen), und konfigurieren Sie sie.

Weitere Informationen finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](../resource-group-authoring-templates.md).

## Zusätzliche Ressourcen

[Azure Compute-, Network- and Storage-Anbieter unter dem Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md) [Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md)

[Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md)

[Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=August15_HO9-->