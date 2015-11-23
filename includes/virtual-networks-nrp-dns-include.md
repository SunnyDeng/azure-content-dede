## Azure DNS

Azure DNS ist ein Hostingdienst für DNS-Domänen, der die Namensauflösung mithilfe der Microsoft Azure-Infrastruktur durchführt.


| Eigenschaft | Beschreibung | Beispielwert |
|---|---|---|
| DNS-Zonen | Domänenzoneninformationen zum Hosten von DNS-Einträgen einer bestimmten Domäne | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com",
providers/Microsoft.Network/dnszones/contoso.com/A/www |
| DNS-Zonen | Domänenzoneninformationen zum Hosten von DNS-Einträgen einer bestimmten Domäne | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com“


### DNS-Datensatzgruppen

DNS-Zonen haben ein als Datensatzgruppe bezeichnetes untergeordnetes Objekt. Datensatzgruppen sind eine nach Typ geordnete Sammlung von Hostdatensätzen für eine DNS-Zone. Datensatztypen sind A, AAAA, CNAME, MX, NS, SOA, SRV und TXT.

| Eigenschaft | Beschreibung | Beispielwert |
|---|---|---|
| Eine Datei | IPv4-Datensatztyp | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA | IPv6-Datensatztyp| /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME | Datensatztyp kanonischen Namens <sup>1</sup> | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX | Maildatensatztyp | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/MX/mail |
| NS | Namenserver-Datensatztyp | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA | Autoritätsursprung-Datensatztyp <sup>2</sup> | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV | Dienstdatensatztyp | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> lässt nur einen Wert pro Datensatz zu.

<sup>2</sup> lässt nur einen Autoritätsursprung-Datensatztyp (SOA, Start of Authority) pro DNS-Zone zu.

DNS-Zonenbeispiel im Json-Format:

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "newZoneName": {
	      "type": "String",
	      "metadata": {
	          "description": "The name of the DNS zone to be created."
	      }
	    },
	    "newRecordName": {
	      "type": "String",
	      "defaultValue": "www",
	      "metadata": {
	          "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
	      }
	    }
	  },
	  "resources": 
	  [
	    {
	      "type": "microsoft.network/dnszones",
	      "name": "[parameters('newZoneName')]",
	      "apiVersion": "2015-05-04-preview",
	      "location": "global",
	      "properties": {
	      }
	    },
	    {
	      "type": "microsoft.network/dnszones/a",
		  "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
      	"apiVersion": "2015-05-04-preview",
      	"location": "global",
	  	"properties": 
	  	{
        	"TTL": 3600,
			"ARecords": 
			[
			    {
				    "ipv4Address": "1.2.3.4"
				},
				{
				    "ipv4Address": "1.2.3.5"
				}
			]
	  	},
	  	"dependsOn": [
        	"[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
      	]
    	}
	  	]
	}

## Zusätzliche Ressourcen

Weitere Informationen erhalten Sie in der [REST-API-Dokumentation für DNS-Zonen](https://msdn.microsoft.com/library/azure/mt130626.aspx).

Weitere Informationen erhalten Sie in der [REST-API-Dokumentation für DNS-Datensatzgruppen](https://msdn.microsoft.com/library/azure/mt130626.aspx).

<!---HONumber=Nov15_HO3-->