## Öffentliche IP-Adresse
Eine öffentliche IP-Adresse bietet entweder eine reservierte oder dynamische öffentliche IP-Adresse. Obwohl Sie eine öffentliche IP-Adresse als eigenständiges Objekt erstellen können, müssen Sie die Adresse einem anderen Objekt zuweisen, um sie verwenden zu können. Sie können eine öffentliche IP-Adresse einem Lastenausgleich, einem Anwendungsgateway oder einer NIC zuweisen, um den Internetzugriff für diese Ressourcen bereitzustellen.

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**publicIPAllocationMethod**|Definiert, ob die IP-Adresse *statisch* oder *dynamisch* ist.|static, dynamic|
|**idleTimeoutInMinutes**|Definiert das Leerlaufzeitlimit.|Ein beliebiger Wert zwischen 4 und 30|
|**ipAddress**|Die dem Objekt zugewiesene IP-Adresse. Dies ist eine schreibgeschützte Eigenschaft.|104\.42.233.77|

### DNS-Einstellungen
Öffentliche IP-Adressen verfügen über ein untergeordnetes Objekt mit dem Namen **dnsSettings**, das die folgenden Eigenschaften enthält:

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**domainNameLabel**|Der für die Namensauflösung verwendet Hostname.|www, ftp, vm1|
|**reverseFqdn**|Ein vollständig qualifizierter Domänenname, der zur IP-Adresse aufgelöst wird und im DNS als PTR-Eintrag registriert ist.|www.contoso.com.|

Beispiel für eine öffentliche IP-Adresse im JSON-Format:

	{
	   "name": "PIP01",
	   "location": "North US",
	   "tags": { "key": "value" },
	   "properties": {
	      "publicIPAllocationMethod": "Static",
	      "idleTimeoutInMinutes": 4,
		  "ipAddress": "104.42.233.77",
	      "dnsSettings": {
	         "domainNameLabel": "mylabel",
	         "reverseFqdn": "contoso.com."
	      }
	   }
	} 

<!---HONumber=Sept15_HO4-->