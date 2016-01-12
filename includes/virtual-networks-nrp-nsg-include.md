## Netzwerksicherheitsgruppen (NSG)
Eine NSG-Ressource ermöglicht das Erstellen einer Sicherheitsbegrenzung für Arbeitsauslastungen durch Implementieren von Zulassungs- und Ablehnungsregeln. Diese Regeln können für einen virtuellen Computer, eine Netzwerkkarte oder ein Subnetz gelten.

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**Subnetze**|Liste der Subnetz-IDs, die zur NSG gehören.|/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd|
|**securityRules**|Liste der Sicherheitsregeln, die die NSG bilden|Siehe [Sicherheitsregel](#Security-rule) unten.|
|**defaultSecurityRules**|Liste der Standardsicherheitsregeln, die in jeder NSG vorhanden sind|Siehe [Standardsicherheitsregeln](#Default-security-rules) unten.|

- **Sicherheitsregeln** - Eine NSG kann mehrere Sicherheitsregeln definieren. Jede Regel kann verschiedene Datenverkehrstypen zulassen oder ablehnen.

### Sicherheitsregel
Eine Sicherheitsregel ist eine untergeordnete Ressource einer NSG, die die nachstehenden Eigenschaften aufweist.

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**description**|Beschreibung der Regel|Eingehenden Datenverkehr für alle virtuellen Computer in Subnetz X zulassen|
|**protocol**|Protokoll entsprechend der Regel|TCP, UDP oder *|
|**sourcePortRange**|Quellportbereich entsprechend der Regel|80, 100-200, *|
|**destinationPortRange**|Zielportbereich entsprechend der Regel|80, 100-200, *|
|**sourceAddressPrefix**|Quelladresspräfix entsprechend der Regel|10\.10.10.1, 10.10.10.0/24, VirtualNetwork|
|**destinationAddressPrefix**|Zieladresspräfix entsprechend der Regel|10\.10.10.1, 10.10.10.0/24, VirtualNetwork|
|**direction**|Richtung des Datenverkehrs entsprechend der Regel|Eingehend oder ausgehend|
|**priority**|Priorität der Regel. Regeln werden in der Reihenfolge ihrer Priorität überprüft. Sobald eine Regel erfüllt ist, wird keine weitere Übereinstimmung mit der Regel gesucht.|10, 100, 65000|
|**access**|Typ des Zugriffs bei Übereinstimmung mit der Regel|Zulassen oder verweigern|

Beispiel-NSG im JSON-Format:

	{
	    "name": "NSG-BackEnd",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/networkSecurityGroups",
	    "location": "westus",
	    "tags": {
	        "displayName": "NSG - Front End"
	    },
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "securityRules": [
	            {
	                "name": "rdp-rule",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
	                "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "description": "Allow RDP",
	                    "protocol": "Tcp",
	                    "sourcePortRange": "*",
	                    "destinationPortRange": "3389",
	                    "sourceAddressPrefix": "Internet",
	                    "destinationAddressPrefix": "*",
	                    "access": "Allow",
	                    "priority": 100,
	                    "direction": "Inbound"
	                }
	            }
	        ],
	        "defaultSecurityRules": [
	            { [...],
	        "subnets": [
	            {
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
	            }
	        ]
	    }
	}

### Standardsicherheitsregeln
Standardsicherheitsregeln weisen dieselben Eigenschaften wie Sicherheitsregeln auf. Ihr Zweck besteht darin, die grundlegende Konnektivität zwischen Ressourcen zu ermöglichen, die zu NSGs gehören. Machen Sie sich mit den [Standardsicherheitsregeln](./virtual-networks-nsg.md#Default-Rules) vertraut.

### Zusätzliche Ressourcen

- Weitere Informationen zu [NSGs](virtual-networks-nsg.md).
- Weitere Informationen zu NSGs finden Sie in der [Referenzdokumentation zur REST-API](https://msdn.microsoft.com/library/azure/mt163615.aspx).
- Weitere Informationen zu Sicherheitsregeln finden Sie in der [Referenzdokumentation zur REST-API](https://msdn.microsoft.com/library/azure/mt163580.aspx).

<!---HONumber=Oct15_HO3-->
