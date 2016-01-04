## Application Gateway

Application Gateway bietet eine von Azure verwaltete HTTP-Lastenausgleichslösung, die auf Lastenausgleich der HTTP-Ebene 7 basiert. Anwendungslastenausgleich ermöglicht die Verwendung von Weiterleitungsregeln für Netzwerkverkehr auf Basis von HTTP.

| Eigenschaft | Beschreibung | 
|---|---|
|*Back-End-Serverpool* | Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder zum Subnetz des virtuellen Netzwerks gehören oder eine öffentliche IP-Adresse/VIP oder eine private IP-Adresse sein. |
| *Einstellung für den Back-End-Serverpool* | Jeder Pool verfügt über Einstellungen wie Port, Protokoll und cookiebasierte Affinität. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools. |
| *Front-End-Port* | Dieser Port ist der öffentliche Port, der im Anwendungsgateway geöffnet ist. Der Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet. |
| *Listener* | Der Listener umfasst einen Front-End-Port, ein Protokoll (HTTP oder HTTPS, bei beiden muss die Groß-/Kleinschreibung beachtet werden) und den Namen des SSL-Zertifikats (falls SSL-Auslagerung konfiguriert wird). |
| *Regel* | Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und es wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden sollen, wenn er einen bestimmten Listener erreicht. Derzeit wird nur die Regel "basic" unterstützt. Die Regel "basic" ist eine Round-Robin-Lastverteilung. |

Beispiel für eine Json-Vorlage für Anwendung Gateway:

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "metadata": {
	        "description": "Location to deploy to"
	      }
	    },
	    "addressPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/16",
	      "metadata": {
	        "description": "Address prefix for the Virtual Network"
	      }
	    },
	    "subnetPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/28",
	      "metadata": {
	        "description": "Subnet prefix"
	      }
	    },
	    "skuName": {
	      "type": "string",
	      "allowedValues": [
	        "Standard_Small",
	        "Standard_Medium",
	        "Standard_Large"
	      ],
	      "defaultValue": "Standard_Medium",
	      "metadata": {
	        "description": "Sku Name"
	      }
	    },
	    "capacity": {
	      "type": "int",
	      "defaultValue": 2,
	      "metadata": {
	        "description": "Number of instances"
	      }
	    },
	    "backendIpAddress1": {
	      "type": "string",
	      "metadata": {
	        "description": "IP Address for Backend Server 1"
	      }
	    },
	    "backendIpAddress2": {
	      "type": "string",
	      "metadata": {
	        "description": "IP Address for Backend Server 2"
	      }
	    }
	  },
	  "variables": {
	    "applicationGatewayName": "applicationGateway1",
	    "publicIPAddressName": "publicIp1",
	    "virtualNetworkName": "virtualNetwork1",
	    "subnetName": "appGatewaySubnet",
	    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    	"subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
    	"publicIPRef": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
    	"applicationGatewayID": "[resourceId('Microsoft.Network/applicationGateways',variables('applicationGatewayName'))]",
		"apiVersion": "2015-05-01-preview"
  	},
 	 "resources": [
    	{
    	  "apiVersion": "[variables('apiVersion')]",
    	  "type": "Microsoft.Network/publicIPAddresses",
    	  "name": "[variables('publicIPAddressName')]",
    	  "location": "[parameters('location')]",
    	  "properties": {
    	    "publicIPAllocationMethod": "Dynamic"
    	  }
    	},
    	{
    	  "apiVersion": "[variables('apiVersion')]",
    	  "type": "Microsoft.Network/virtualNetworks",
    	  "name": "[variables('virtualNetworkName')]",
    	  "location": "[parameters('location')]",
    	  "properties": {
    	    "addressSpace": {
    	      "addressPrefixes": [
    	        "[parameters('addressPrefix')]"
    	      ]
    	    },
    	    "subnets": [
    	      {
    	        "name": "[variables('subnetName')]",
    	        "properties": {
    	          "addressPrefix": "[parameters('subnetPrefix')]"
    	        }
    	      }
    	    ]
    	  }
    	},
    	{
    	  "apiVersion": "[variables('apiVersion')]",
    	  "name": "[variables('applicationGatewayName')]",
    	  "type": "Microsoft.Network/applicationGateways",
    	  "location": "[parameters('location')]",
    	  "dependsOn": [
    	    "[concat('Microsoft.Network/virtualNetworks/', variables	('virtualNetworkName'))]",
    	    "[concat('Microsoft.Network/publicIPAddresses/', variables	('publicIPAddressName'))]"
    	  ],
    	  "properties": {
    	    "sku": {
    	      "name": "[parameters('skuName')]",
    	      "tier": "Standard",
    	      "capacity": "[parameters('capacity')]"
    	    },
    	    "gatewayIPConfigurations": [
    	      {
    	        "name": "appGatewayIpConfig",
    	        "properties": {
    	          "subnet": {
    	            "id": "[variables('subnetRef')]"
    	          }
    	        }
    	      }
    	    ],
    	    "frontendIPConfigurations": [
    	      {
    	        "name": "appGatewayFrontendIP",
    	        "properties": {
    	          "PublicIPAddress": {
    	            "id": "[variables('publicIPRef')]"
    	          }
    	        }
    	      }
    	    ],
    	    "frontendPorts": [
    	      {
    	        "name": "appGatewayFrontendPort",
    	        "properties": {
    	          "Port": 80
    	        }
    	      }
    	    ],
    	    "backendAddressPools": [
    	      {
    	        "name": "appGatewayBackendPool",
    	        "properties": {
    	          "BackendAddresses": [
    	            {
    	              "IpAddress": "[parameters('backendIpAddress1')]"
    	            },
    	            {
    	              "IpAddress": "[parameters('backendIpAddress2')]"
    	            }
    	          ]
    	        }
    	      }
    	    ],
    	    "backendHttpSettingsCollection": [
    	      {
    	        "name": "appGatewayBackendHttpSettings",
    	        "properties": {
    	          "Port": 80,
    	          "Protocol": "Http",
    	          "CookieBasedAffinity": "Disabled"
    	        }
    	      }
    	    ],
    	    "httpListeners": [
    	      {
    	        "name": "appGatewayHttpListener",
    	        "properties": {
    	          "FrontendIPConfiguration": {
    	            "Id": "[concat(variables('applicationGatewayID'), '/	frontendIPConfigurations/appGatewayFrontendIP')]"
    	          },
    	          "FrontendPort": {
    	            "Id": "[concat(variables('applicationGatewayID'), '/	frontendPorts/appGatewayFrontendPort')]"
    	          },
    	          "Protocol": "Http",
    	          "SslCertificate": null
    	        }
    	      }
    	    ],
    	    "requestRoutingRules": [
    	      {
    	        "Name": "rule1",
    	        "properties": {
    	          "RuleType": "Basic",
    	          "httpListener": {
    	            "id": "[concat(variables('applicationGatewayID'), '/	httpListeners/appGatewayHttpListener')]"
    	          },
    	          "backendAddressPool": {
    	            "id": "[concat(variables('applicationGatewayID'), '/	backendAddressPools/appGatewayBackendPool')]"
    	          },
    	          "backendHttpSettings": {
    	            "id": "[concat(variables('applicationGatewayID'), '/	backendHttpSettingsCollection/	appGatewayBackendHttpSettings')]"
    	          }
    	        }
    	      }
    	    ]
    	  }
    	}
  	]	
	}

### Zusätzliche Ressourcen

Weitere Informationen finden Sie unter [ Application Gateway REST-API](https://msdn.microsoft.com/library/azure/mt299388.aspx).

<!---HONumber=Nov15_HO4-->