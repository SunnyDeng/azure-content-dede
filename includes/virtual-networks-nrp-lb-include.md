## Lastenausgleichsmodul
Ein Lastenausgleichsmodul wird verwendet, wenn Sie Ihre Anwendungen skalieren möchten. Zu den typischen Bereitstellungsszenarios zählen Anwendungen, die auf mehreren VM-Instanzen ausgeführt werden. Den VM-Instanzen ist ein Lastenausgleichsmodul vorgelagert, das hilft den Netzwerkdatenverkehr an die verschiedenen Instanzen zu verteilen.

![NICs auf einem einzelnen virtuellen Computer](./media/resource-groups-networking/figure8.png)

| Eigenschaft | Beschreibung |
|---|---|
| *Front-End-IP-Konfiguration* | Ein Load Balancer kann ein oder mehrere Front-End-IP-Adressen umfassen, auch als virtuelle IPs (VIPs) bekannt. Diese IP-Adressen dienen als Eingang für den Datenverkehr und sind öffentliche oder private IP-Adressen. |
|*Back-End-Adresspool* | Dies sind IP-Adressen, die den VM NICs zugeordnet sind, an die die Last verteilt wird. |
|*Lastenausgleichsregeln* | Eine Regeleigenschaft ordnet eine bestimmte Front-End-IP-/Port-Kombination einer Back-End-IP-Adressen-/Port-Kombination zu. Mit einer einzelnen Definition einer Load Balancer-Ressource können Sie mehrere Lastenausgleichsregeln definieren, von denen jede eine Kombination aus Front-End-IP und Port sowie Back-End-IP und Port darstellt, die dem virtuellen Computer zugeordnet ist. Die Regel ist ein Port im Front-End-Pool für viele virtuelle Computer im Back-End-Pool. |  
| *Tests* | Mit Tests können Sie die Integrität der VM-Instanzen nachverfolgen. Schlägt eine Integritätsüberprüfung fehl, wird die Instanz des virtuellen Computers automatisch aus der Rotation entfernt. |
| *Eingehende NAT-Regeln* | NAT-Regeln definieren den Eingangsdatenverkehr, der durch die Front-End-IP fließt und an die Back-End-IP an eine bestimmte Instanz eines virtuellen Computers verteilt wird. Die NAT-Regel ist ein Port im Front-End-Pool für einen virtuellen Computer im Back-End-Pool. | 

Beispiel für eine Vorlage für einen Load Balancer im Json-Format:

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "dnsNameforLBIP": {
	      "type": "string",
	      "metadata": {
	        "description": "Unique DNS name"
	      }
	    },
	    "location": {
	      "type": "string",
	      "allowedValues": [
	        "East US",
	        "West US",
	        "West Europe",
	        "East Asia",
	        "Southeast Asia"
	      ],
	      "metadata": {
	        "description": "Location to deploy"
	      }
	    },
	    "addressPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/16",
	      "metadata": {
	        "description": "Address Prefix"
	      }
	    },
	    "subnetPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/24",
	      "metadata": {
	        "description": "Subnet Prefix"
	      }
	    },
	    "publicIPAddressType": {
	      "type": "string",
	      "defaultValue": "Dynamic",
	      "allowedValues": [
	        "Dynamic",
	        "Static"
	      ],
	      "metadata": {
	        "description": "Public IP type"
	      }
	    }
	  },
	  "variables": {
	    "virtualNetworkName": "virtualNetwork1",
	    "publicIPAddressName": "publicIp1",
	    "subnetName": "subnet1",
	    "loadBalancerName": "loadBalancer1",
	    "nicName": "networkInterface1",
	    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
	    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
	    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	    "nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]",
	    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	    "backEndIPConfigID": "[concat(variables('nicId'),'/ipConfigurations/ipconfig1')]"
	  },
	  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('location')]",
      "properties": {
        "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameforLBIP')]"
        }
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
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
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnetRef')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(variables('lbID'), '/backendAddressPools/LoadBalancerBackend')]"
                }
              ],
              "loadBalancerInboundNatRules": [
                {
                  "id": "[concat(variables('lbID'),'/inboundNatRules/RDP')]"
                }
              ]
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[variables('publicIPAddressID')]"
              }
            }
          }
        ],
        "backendAddressPools": [
          {
            "name": "loadBalancerBackEnd"
          }
        ],
        "inboundNatRules": [
          {
            "name": "RDP",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPort": 3389,
              "backendPort": 3389,
              "enableFloatingIP": false
            }
          }
        ]
      }
    }
	  ]
	}

### Zusätzliche Ressourcen

Weitere Informationen finden Sie unter [Lastenausgleich für REST-API](https://msdn.microsoft.com/library/azure/mt163651.aspx).

<!---HONumber=Nov15_HO4-->