## Virtuelles Netzwerk
Mit virtuellen Netzwerken (VNET) und Subnetzressourcen können Sie eine Sicherheitsbegrenzung für in Azure ausgeführte Workloads definieren. Ein VNet ist durch eine Auflistung von Adressräumen gekennzeichnet, die als CIDR-Blöcke bezeichnet werden.

>[AZURE.NOTE] Netzwerkadministratoren sind mit der CIDR-Notation vertraut. Wenn Sie CIDR nicht kennen, [erfahren Sie hier mehr darüber](http://whatismyipaddress.com/cidr).

![VNet mit mehreren Subnetzen](./media/resource-groups-networking/Figure4.png)

VNets umfassen die folgenden Eigenschaften:

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**addressSpace**|Auflistung der Adresspräfixe, aus denen das VNet besteht, in CIDR-Notation|192\.168.0.0/16|
|**Subnetze**|Auflistung von Subnetzen, aus denen das VNet besteht|siehe unten unter [Subnetze](#Subnets)|
|**ipAddress**|Die dem Objekt zugewiesene IP-Adresse. Dies ist eine schreibgeschützte Eigenschaft.|104\.42.233.77|

### Subnetze
Ein Subnetz ist eine untergeordnete Ressource eines VNet und hilft, die Segmente von Adressräumen innerhalb eines CIDR-Blocks mithilfe von IP-Adressenpräfixen zu definieren. NICs können zu Subnetzen hinzugefügt und mit virtuellen Computern verbunden werden, sodass sie Konnektivität für verschiedene Workloads bereitstellen.

Subnetze umfassen die folgenden Eigenschaften:

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**addressPrefix**|Einzelnes Adresspräfix für das Subnetz in CIDR-Notation|192\.168.1.0/24|
|**networkSecurityGroup**|Auf das Subnetz angewendete NSG|siehe [NSGs](#Network-Security-Group)|
|**routeTable**|Auf das Subnetz angewendete Routentabelle|siehe [UDR](#Route-table)|
|**ipConfigurations**|Auflistung von IP-Konfigurationsobjekten, die von mit dem Subnetz verbundenen NICs verwendet werden|siehe [UDR](#Route-table)|


Beispiel-VNet im JSON-Format:

	{
	    "name": "TestVNet",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/virtualNetworks",
	    "location": "westus",
	    "tags": {
	        "displayName": "VNet"
	    },
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "addressSpace": {
	            "addressPrefixes": [
	                "192.168.0.0/16"
	            ]
	        },
	        "subnets": [
	            {
	                "name": "FrontEnd",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "addressPrefix": "192.168.1.0/24",
	                    "networkSecurityGroup": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
	                    },
	                    "routeTable": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
	                    },
	                    "ipConfigurations": [
	                        {
	                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
	                        },
	                        ...]
	                }
	            },
	            ...]
	    }
	}

### Zusätzliche Ressourcen

- Weitere Informationen zu [VNet](../articles/virtual-network/virtual-networks-overview.md).
- Lesen Sie die [REST-API-Referenzdokumentation](https://msdn.microsoft.com/library/azure/mt163650.aspx) für Informationen zu VNets.
- Lesen Sie die [REST-API-Referenzdokumentation](https://msdn.microsoft.com/library/azure/mt163618.aspx) für Informationen zu Subnetzen.

<!---HONumber=AcomDC_0323_2016-->