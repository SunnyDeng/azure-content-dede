<properties 
	pageTitle="Ressourcen-Manager-Vorlage für die hochverfügbare SharePoint-Farm" 
	description="Erfahren Sie etwas über die Struktur der azuredeploy.json-Vorlagendatei für die SharePoint-Farm mit hoher Verfügbarkeit." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# Ressourcen-Manager-Vorlage für die hochverfügbare SharePoint-Farm

Dieses Thema führt Sie schrittweise durch die Struktur der azuredeploy.json-Vorlagendatei für die SharePoint-Farm mit hoher Verfügbarkeit.

## Abschnitt "Parameter"

"Parameter" gibt die Parameter an, mit denen Daten in dieser Vorlage eingegeben werden. Es können bis zu 50 Parameter definiert werden. Im Folgenden finden Sie ein Beispiel für einen Parameter für den Azure-Speicherort:

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

Im Abschnitt "Variablen" sind Variablen angegeben, die in dieser Vorlage verwendet werden können. Es können bis zu 100 Variablen definiert werden. Hier einige Beispiele:

	"RDPNAT":"RDP", 
	"spWebLB":"spWeb", 
	"SQLAOListener":"SQLAlwaysOnEndPointListener", 
	"SQLAOProbe":"SQLAlwaysOnEndPointProbe", 
	"staticSubnetName": "staticSubnet", 
	"sqlSubnetName": "sqlSubnet", 
	"spwebSubnetName": "spwebSubnet", 
	"spappSubnetName": "spappSubnet", 

## Abschnitt "Ressourcen"

Im Abschnitt **"Ressourcen"** werden Informationen festgelegt, die benötigt werden, um die Ressourcen für die SharePoint-Farm in einer Ressourcengruppe bereitstellen. Es lassen sich maximal 250 Ressourcen definieren, und Ressourcenabhängigkeiten können nur fünf Ebenen tief definiert sein.

Dieser Abschnitt enthält die folgenden Unterabschnitte:

### Microsoft.Storage/storageAccounts  

In diesem Abschnitt wird ein neues Speicherkonto für alle Datenträger- und VHD-Ressourcen für die Farm erstellt. Hier ist der JSON-Code für das Speicherkonto:

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2014-12-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

In diesen Abschnitten erstellen Sie einen Satz von öffentlichen IP-Adressen, mit denen die einzelnen virtuellen Computer über das Internet erreicht werden können. Beispiel:

	{
		"apiVersion": "2014-12-01-preview",
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

Diese Abschnitte erstellen vier Verfügbarkeitsgruppen für jede Bereitstellungsebene:

- Active Directory-Domänencontroller
- SQL Server-Cluster
- Server auf Anwendungsebene
- Server auf Webebene

Beispiel:

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2014-12-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

In diesem Abschnitt wird ein virtuelles Nur-Cloud-Netzwerk mit vier Subnetzen erstellt \(eines für jede Bereitstellungsebene\), in dem sich die virtuellen Computer befinden. Hier finden Sie den JSON-Code:

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkPrefix')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},



### Microsoft.Network/loadBalancers

In diesen Abschnitten werden Lastenausgleichsinstanzen für jeden virtuellen Computer erstellt, die NAT und Filterung für eingehenden Datenverkehr aus dem Internet bereitstellen. Für jedes Lastenausgleichssystem werden Front-End- und Back-End-Einstellungen sowie NAT-Regeln für eingehenden Datenverkehr festgelegt. Es gibt z. B. Remotedesktop-Verkehrsregeln für jeden virtuellen Computer und eine Regel zum Zulassen von eingehendem Webdatenverkehr \(TCP-Port 80\) über das Internet für die Server auf Webebene. Es folgt ein Beispiel für den Server auf Webebene:

        {
            "apiVersion": "2014-12-01-preview",
            "name": "[variables('splbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "[variables('spLBFE')]",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
                            },
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "[variables('spWebLBBE')]"
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "[variables('spWebLB')]",
                        "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "probe": {
                            "id": "[variables('spwebProbeID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": 80,
                        "backendPort": 80,
                        "enableFloatingIP": false
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "[variables('spWebProbe')]",
                        "properties": {
                            "protocol": "http",
                            "port": "[variables('spWebProbePort')]",
                            "intervalInSeconds": "15",
                            "numberOfProbes": "5",
                            "requestPath":"/"
                        }
                    }
                ]
            }
        },

### Microsoft.Network/networkInterfaces

In diesen Abschnitten wird eine Netzwerkschnittstelle für die einzelnen virtuellen Computer erstellt, und anschließend werden statische IP-Adressen für die Domänencontroller konfiguriert. Es folgt ein Beispiel für eine Netzwerkschnittstelle für den primären Domänencontroller:

	{
		"name": "[variables('adPDCNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('rdpLBName'))]"
		],
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress" :"[parameters('adPDCNicIPAddress')]",
						"subnet": {
							"id": "[variables('staticSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id":"[variables('adBEAddressPoolID')]"
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

In diesen Abschnitten erstellen und konfigurieren Sie die neun virtuellen Computer in der Bereitstellung.

In den ersten beiden Hauptabschnitten erstellen und konfigurieren Sie die Domänencontroller in der Bereitstellung. In jedem Abschnitt geschieht Folgendes:

- Angeben des Speicherkontos, des Verfügbarkeitssatzes, der Netzwerkschnittstelle und der Lastenausgleichsinstanz für jeden virtuellen Domänencontroller-Computer
- Hinzufügen eines zusätzlichen Datenträgers zu jedem virtuellen Domänencontroller-Computer
- Ausführen eines Windows PowerShell-Skripts zum Konfigurieren des virtuellen Computers als Domänencontroller

Es folgt ein Beispiel für den primären Domänencontroller:

        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('adPDCVMName')]",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]",
                "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
                "[resourceId('Microsoft.Network/loadBalancers',variables('rdpLBName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('adVMSize')]"
                },
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
                },
                "osProfile": {
                    "computername": "[variables('adPDCVMName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "windowsProfile": {
                        "provisionVMAgent": "true"
                    }
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('adSourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/')]",
                    "dataDisks": [
                        {
                            "vhd": {
                                "uri":"[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adPDCVMName'),'data-1.vhd')]"
                                },
                            "name":"[concat(variables('adPDCVMName'),'-data-disk1')]",
                            "caching" : "None",
                            "diskSizeGB": "[variables('adDataDiskSize')]",
                            "lun": 0
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]"
                        }
                    ]
                }
            },
            "resources" :[
                {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "name": "[concat(variables('adPDCVMName'),'/InstallDomainController')]",
                    "apiVersion": "2014-12-01-preview",
                    "location": "[parameters('deploymentLocation')]",
                    "dependsOn":[
                        "[resourceId('Microsoft.Compute/virtualMachines', variables('adPDCVMName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "1.7",
                        "settings": {
                            "ModulesUrl": "[variables('adPDCModulesURL')]",
                            "ConfigurationFunction": "[variables('adPDCConfigurationFunction')]",
                            "Properties": {
                                "DomainName": "[parameters('domainName')]",
                                "AdminCreds":{
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


In einem zusätzlichen Abschnitt nach jedem Domänencontroller, beginnend mit **"Name": "UpdateVNetDNS"**, werden die DNS-Server des virtuellen Netzwerks für die Verwendung der statischen IP-Adressen der beiden Domänencontroller konfiguriert.

In den folgenden drei Abschnitten **"Typ": "Microsoft.Compute/virtualMachines"** werden die virtuellen Computer des SQL Server-Clusters in der Bereitstellung erstellt. In jedem Abschnitt geschieht Folgendes:

- Angeben des Speicherkontos, des Verfügbarkeitssatzes, des Lastenausgleichs, des virtuellen Netzwerks und der Netzwerkschnittstelle für jeden virtuellen Computer
- Hinzufügen zweier zusätzlicher Festplatten zu jedem SQL-Server

Zusätzliche Abschnitte **"Microsoft.Compute/virtualMachines/extensions"** rufen das PowerShell-Skript zum Konfigurieren des SQL Server-Clusters sowie der virtuellen Computer des SQL Server-Clusters und zum Aktivieren von AlwaysOn-Verfügbarkeitsgruppen auf.

In den nächsten vier Abschnitten **"Typ": "Microsoft.Compute/virtualMachines"** werden die virtuellen Computer der SharePoint-Farm in der Bereitstellung erstellt. In jedem Abschnitt werden das Speicherkonto, der Verfügbarkeitssatz, der Lastenausgleich, das virtuelle Netzwerk sowie die Netzwerkschnittstelle für jeden virtuellen Computer angegeben.

Zusätzliche Abschnitte **"Microsoft.Compute/virtualMachines/extensions"** rufen die PowerShell-Skripts zum Konfigurieren von SharePoint-Servern als SharePoint-Farm auf.

Beachten Sie die allgemeine Organisation der Unterabschnitte des Abschnitts **"Ressourcen"** der JSON-Datei:

1.	Erstellen Sie die Elemente der Azure-Infrastruktur, die für die Unterstützung mehrerer virtueller Computer erforderlich sind \(ein Speicherkonto, öffentliche IP-Adressen, Verfügbarkeitsgruppen, ein virtuelles Netzwerk, Netzwerkschnittstellen, Lastenausgleichsinstanzen\).
2.	Erstellen Sie die virtuellen Computer des Domänencontrollers, die die zuvor erstellten allgemeinen und spezifischen Elemente der Azure-Infrastruktur verwenden, fügen Sie Datenträger hinzu, und führen Sie PowerShell-Skripts aus. Aktualisieren Sie außerdem das virtuelle Netzwerk, sodass die statischen IP-Adressen des Domänencontrollers verwendet werden.
3.	Erstellen Sie die virtuellen Computer des SQL Server-Clusters, die die zuvor für die Domänencontroller erstellten allgemeinen und spezifischen Elemente der Azure-Infrastruktur verwenden, fügen Sie Datenträger hinzu, und führen Sie PowerShell-Skripts zum Konfigurieren des Clusters und der SQL Server-AlwaysOn-Verfügbarkeitsgruppen aus.
4.	Erstellen Sie die virtuellen Computer des SharePoint-Servers, die die zuvor erstellten allgemeinen und spezifischen Elemente der Azure-Infrastruktur verwenden, fügen Sie Datenträger hinzu, und führen Sie PowerShell-Skripts aus.

Bei Ihren eigenen JSON-Vorlagen zum Erstellen einer Infrastruktur mit mehreren Ebenen in Azure sollten Sie die gleichen Schritte ausführen:

1.	Erstellen Sie die allgemeinen Elemente \(Speicherkonto, virtuelles Netzwerk\), spezifische Elemente für die Ebene \(Verfügbarkeitsgruppen\) und spezifische Elemente für die virtuellen Computer \(öffentliche IP-Adressen, Verfügbarkeitsgruppen, Netzwerkschnittstellen und Lastenausgleichsinstanzen\) der Azure-Infrastruktur, die für die Bereitstellung erforderlich sind.
2.	Erstellen Sie für jede Ebene in der Anwendung \(z. B. Authentifizierung , Datenbank, Web\) die Server in dieser Ebene mithilfe der allgemeinen Elemente \(Speicherkonto, virtuelles Netzwerk\), der spezifischen Elemente für die Ebene \(Verfügbarkeitsgruppe\) und der spezifischen Elemente für die virtuellen Computer \(öffentliche IP-Adressen, Netzwerkschnittstellen, Lastenausgleichsinstanzen\), und konfigurieren Sie sie.

Weitere Informationen finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn835138.aspx).


## Zusätzliche Ressourcen

[Azure Compute, Network and Storage Providers under Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md) (in englischer Sprache)

[Azure Resource Manager Overview](resource-group-overview.md) (in englischer Sprache)

[Authoring Azure Resource Manager Templates](resource-group-authoring-templates.md) (in englischer Sprache)

[Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)


<!--HONumber=52-->
