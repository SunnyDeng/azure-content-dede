## Traffic Manager-Profil

Der Traffic Manager und seine untergeordnete Endpunkt-Ressource ermöglichen das DNS-Routing zu Endpunkten in und außerhalb von Azure. Eine solche Datenverkehrsverteilung wird durch Routingrichtlinien bestimmt. Der Traffic Manager lässt auch eine Überwachung der Endpunkt-Integrität zu, sowie eine angemessene Umleitung des Datenverkehrs basierend auf der Integrität eines Endpunktes.

| Eigenschaft | Beschreibung |
|---|---|
|**trafficRoutingMethod**| Mögliche Werte sind *Leistung*, *Gewichtung* und *Priorität*. | 
| **dnsConfig** | FQDN für das Profil | 
| **Protokoll** | Überwachungsprotokoll, mögliche Werte sind *HTTP* und *HTTPS*|
| **Port** | Überwachungsport |  
| **Path** | Überwachungspfad |
| **Endpunkte** | Container für Endpunkt-Ressourcen | 

### Endpunkt 

Ein Endpunkt ist eine dem Traffic Manager-Profil untergeordnete Ressource. Er stellt einen Dienst- oder Webendpunkt dar, an den der Benutzerdatenverkehr anhand der in der Traffic Manager-Ressource konfigurierten Richtlinie verteilt wird.

| Eigenschaft | Beschreibung | 
|---|---| 
| **Typ** | Der Typ des Endpunkts, mögliche Werte sind *Azure-Endpunkt*, *externer Endpunkt* und *verschachtelter Endpunkt* | 
| **targetResourceId** | Öffentliche IP-Adresse eines Dienst- oder Webendpunktes. Dabei kann es sich um einen Azure- oder externen Endpunkt handeln. | 
| **Weight** | Endpunkt-Gewichtung, die für die Datenverkehrsverwaltung verwendet wird. | 
| **Priority** | Priorität des Endpunktes, die zum Definieren einer Failover-Aktion verwendet wird |

Beispiel von Traffic Manager im JSON-Format:


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## Weitere Ressourcen

Weitere Informationen erhalten Sie in der [REST-API-Dokumentation für Traffic Manager](https://msdn.microsoft.com/library/azure/mt163664.aspx).

<!---HONumber=AcomDC_1223_2015-->