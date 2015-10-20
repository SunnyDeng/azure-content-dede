## Herunterladen und Verstehen der ARM-Vorlage

Sie können die vorhandene ARM-Vorlage zum Erstellen von einem VNet und zwei Subnetzen über GitHub herunterladen, die gewünschten Änderungen vornehmen und die Vorlage anschließend wiederverwenden. Dazu führen Sie die folgenden Schritte aus.

1. Navigieren Sie zu https://github.com/Azure/azure-quickstart-templates/tree/master/101-two-subnets.
2. Klicken Sie auf **azuredeploy.json**, und klicken Sie dann auf **RAW**.
3. Speichern Sie die Datei in einem lokalen Ordner auf Ihrem Computer.
4. Wenn Sie mit ARM-Vorlagen vertraut sind, fahren Sie mit Schritt 7 fort.
5. Öffnen Sie die Datei, die Sie gerade gespeichert haben, und sehen Sie sich den Inhalt unter **Parameter** in Zeile 5 an. ARM-Vorlagenparameter enthalten Platzhalter für Werte, die während der Bereitstellung ausgefüllt werden können.

	| Parameter | Beschreibung |
	|---|---|
	| **location** | Azure-Region, in der das VNet erstellt wird |
	| **vnetName** | Name für das neue VNet |
	| **addressPrefix** | Adressraum für das VNet im CIDR-Format |
	| **subnet1Name** | Name für das erste VNet |
	| **subnet1Prefix** | CIDR-Block für das erste Subnetz |
	| **subnet2Name** | Name für das zweite VNet |
	| **subnet2Prefix** | CIDR-Block für das zweite Subnetz |

	>[AZURE.IMPORTANT]Die in GitHub verwalteten ARM-Vorlagen können mit der Zeit geändert werden. Überprüfen Sie die Vorlage stets, bevor Sie sie verwenden.
	
6. Überprüfen Sie den Inhalt unter **Ressourcen**. Beachten Sie Folgendes:

	- **type**. Typ der Ressource, die von der Vorlage erstellt wird. In diesem Fall **Microsoft.Network/virtualNetworks** (ein VNet).
	- **name**. Name der Ressource. Beachten Sie die Verwendung von **[parameters('vnetName')]**, die bedeutet, dass der Name während der Bereitstellung als Eingabe des Benutzers oder durch eine Parameterdatei bereitgestellt wird.
	- **properties**. Liste der Eigenschaften für die Ressource. Diese Vorlage verwendet bei der Erstellung des VNet die Adressraum- und Subnetzeigenschaften.

7. Navigieren Sie zurück zur https://github.com/Azure/azure-quickstart-templates/tree/master/101-two-subnets.
8. Klicken Sie auf **azuredeploy-paremeters.json**, und klicken Sie dann auf **RAW**.
9. Speichern Sie die Datei in einem lokalen Ordner auf Ihrem Computer.
10. Öffnen Sie die Datei, die Sie gerade gespeichert haben, und bearbeiten Sie die Parameterwerte. Verwenden Sie die unten stehenden Werte, um das VNet wie in unserem Szenario beschrieben bereitzustellen.

		{
		  "location": {
		    "value": "Central US"
		  },
		  "vnetName": {
		      "value": "TestVNet"
		  },
		  "addressPrefix": {
		      "value": "192.168.0.0/16"
		  },
		  "subnet1Name": {
		      "value": "FrontEnd"
		  },
		  "subnet1Prefix": {
		    "value": "192.168.1.0/24"
		  },
		  "subnet2Name": {
		      "value": "BackEnd"
		  },
		  "subnet2Prefix": {
		      "value": "192.168.2.0/24"
		  }
		}

11. Speichern Sie die Datei.
  

<!---HONumber=Oct15_HO3-->