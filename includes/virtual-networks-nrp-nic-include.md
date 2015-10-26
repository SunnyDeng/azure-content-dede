## NIC
 
Eine Netzwerkkarte (NIC) stellt als Ressource Netzwerkkonnektivität für ein bestehendes Subnetz in einer VNet-Ressource bereit. Obwohl Sie eine Netzwerkkarte als eigenständiges Objekt erstellen können, müssen Sie sie einem anderen Objekt zuweisen, um Konnektivität bereitstellen zu können. Eine Netzwerkkarte kann verwendet werden, um einen virtuellen Computer mit einem Subnetz, einer öffentlichen IP-Adresse oder einem Load Balancer zu verbinden.

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**virtualMachine**|Der virtuelle Computer, dem die Netzwerkkarte zugeordnet ist|/subscriptions/{GUID}/../Microsoft.Compute/virtualMachines/vm1|
|**macAddress**|Die MAC-Adresse der Netzwerkkarte|Ein beliebiger Wert zwischen 4 und 30|
|**networkSecurityGroup**|Die NSG, der die Netzwerkkarte zugeordnet ist|/subscriptions/{GUID}/../Microsoft.Network/networkSecurityGroups/myNSG1|
|**dnsSettings**|DNS-Einstellungen für die Netzwerkkarte|Weitere Informationen finden Sie unter [PIP](#Public-IP-address).|

Eine Netzwerkschnittstellenkarte (Network Interface Card, NIC) stellt eine Netzwerkschnittstelle dar, die einem virtuellen Computer (VM) zugeordnet werden kann. Ein virtueller Computer hat ein oder mehrere NICs.

![NICs auf einem einzelnen virtuellen Computer](./media/resource-groups-networking/Figure3.png)

### IP-Konfigurationen
Netzwerkkarten verfügen über ein untergeordnetes Objekt mit dem Namen **ipConfigurations**, das die folgenden Eigenschaften enthält:

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**subnet**|Das Subnetz, mit dem die Netzwerkkarte verbunden ist|/subscriptions/{GUID}/../Microsoft.Network/virtualNetworks/myvnet1/subnets/mysub1|
|**privateIPAddress**|Die IP-Adresse für die Netzwerkkarte im Subnetz|10\.0.0.8|
|**privateIPAllocationMethod**|Die IP-Zuordnungsmethode|Dynamisch oder statisch|
|**enableIPForwarding**|Gibt an, ob die Netzwerkkarte für das Routing verwendet werden kann|"true" oder "false"|
|**primary**|Gibt an, ob die Netzwerkkarte die primäre Netzwerkkarte für den virtuellen Computer ist|"true" oder "false"|
|**publicIPAddress**|Die öffentliche IP-Adresse, die der Netzwerkkarte zugeordnet ist.|Weitere Informationen finden Sie unter [DNS-Einstellungen](#DNS-settings).|
|**loadBalancerBackendAddressPools**|Back-End-Adresspools, denen die Netzwerkkarte zugeordnet ist||
|**loadBalancerInboundNatRules**|Eingehende NAT-Regeln für den Load Balancer, dem die Netzwerkkarte zugeordnet ist||

Beispiel für eine öffentliche IP-Adresse im JSON-Format:

	{
	    "name": "lb-nic1-be",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/networkInterfaces",
	    "location": "eastus",
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "ipConfigurations": [
	            {
	                "name": "NIC-config",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
	                "etag": "W/"0027f1a2-3ac8-49de-b5d5-fd46550500b1"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "privateIPAddress": "10.0.0.4",
	                    "privateIPAllocationMethod": "Dynamic",
	                    "subnet": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
	                    },
	                    "loadBalancerBackendAddressPools": [
	                        {
	                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
	                        }
	                    ],
	                    "loadBalancerInboundNatRules": [
	                        {
	                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
	                        }
	                    ]
	                }
	            }
	        ],
	        "dnsSettings": { ... },
	        "macAddress": "00-0D-3A-10-F1-29",
	        "enableIPForwarding": false,
	        "primary": true,
	        "virtualMachine": {
	            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
	        }
	    }
	}

### Zusätzliche Ressourcen

- Lesen Sie die [REST-API-Referenzdokumentation](https://msdn.microsoft.com/library/azure/mt163579.aspx) für Informationen zu Netzwerkkarten.

<!---HONumber=Oct15_HO3-->