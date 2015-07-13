<properties 
   pageTitle="Azure Storage-Blobconnector" 
   description="Erste Schritte mit Azure Storage-Blobconnector" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="05/11/2015"
   ms.author="rajram"/>
   
#Azure Storage-Blobconnector

##Übersicht
Mit dem Azure Storage-Blobconnector können Sie Blobs aus einem Blobcontainer hochladen, herunterladen und löschen.

##Erstellen eines neuen Azure Storage-Blobconnectors
Um einen neuen Azure Storage-Connector zu erstellen, führen Sie die unten aufgeführten Schritte aus. <ul> <li>Starten Sie das Azure-Portal. <li>Öffnen Sie den Azure Marketplace über "+Neu" (unten auf der Seite) -> "Web + Mobil" -> "Azure Marketplace".</ul>

![Starten von Azure Marketplace][1]<br> <ul> <li>Klicken Sie auf API-Apps. <li>Suchen Sie nach <i>Blob</i>, und wählen Sie "Azure Storage-Blobconnector" aus.</ul>

![Wählen des Azure Storage-Blobconnectors][2] <br> <ul> <li>Klicken Sie auf "Erstellen". <li>Geben Sie die folgenden Daten im daraufhin geöffneten Blatt zum Azure Storage-Blobconnector an. </ul>

![Erstellen des Azure Storage-Blobconnectors][3]

- **Standort** – Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
- **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
- **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
- **Webhostingplan** – Wählen oder erstellen Sie einen Webhostingplan.
- **Preisstufe** – Wählen Sie eine Preisstufe für den Connector.
- **Name** – Geben Sie einen Namen für den Blobspeicherconnector ein.
- **Paketeinstellungen** 
	- **Container/SAS-URI** – Geben Sie den URI des Blobcontainers an. Der URI kann auch das SAS-Token umfassen. Beispiel: http://storageaccountname.blob.core.windows.net/containername oder http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah.
	- **Zugriffsschlüssel** – Geben Sie einen gültigen primären/sekundären Zugriffsschlüssel für das Speicherkonto an. Lassen Sie dieses Feld leer, wenn Sie SAS-Token für die Authentifizierung verwenden.
- Klicken Sie auf "Erstellen". Ein neuer Azure Storage-Blobconnector wird erstellt.

##Verwenden von Azure Storage-Blobconnectors in Logik-App
Sobald der Azure Storage-Blobconnector erstellt wurde, kann er vom Datenfluss genutzt werden.

Erstellen Sie einen neuen Datenfluss über "+Neu" -> "Web + Mobil" -> "Logik-App". Stellen Sie die Metadaten für den Datenfluss samt Ressourcengruppe bereit.

![Erstellen einer Logik-App][4]

Klicken Sie auf *Trigger und Aktionen*. Der Datenfluss-Designer wird geöffnet.

![Logik-App – Leerer Datenfluss-Designer][5]

Der Azure Storage-Blobconnector kann als Aktion verwendet werden.

###Aktionen
Klicken Sie im rechten Bereich auf "Azure Storage-Blobconnector". Der Connector listet die unterstützten Aktionen auf.

![Liste der Azure Storage-Blobaktionen][10]

Der Azure Storage-Blob-Connector unterstützt sechs Aktionen. Dies sind:

- **Blob abrufen** – Abrufen eines bestimmten Blobs aus dem Container.
- **Blob hochladen** – Hochladen eines neuen Blobs oder Aktualisieren eines vorhandenen Blobs.
- **Blob löschen** – Löschen eines bestimmten Blobs aus einem Container.
- **Blobs auflisten** – Auflisten aller Blobs in einem Verzeichnis.
- **Blobmomentaufnahme erstellen** – Erstellen einer schreibgeschützten Momentaufnahme eines bestimmten Blobs.
- **Blob kopieren** – Erstellen eines neuen Blobs durch Kopieren von einem anderen Blob. Das Quellblob kann sich in demselben Konto oder in einem anderen Konto befinden.

Nehmen wir ein Beispiel – Blob hochladen. Klicken Sie auf "Blob hochladen".

![Eingaben der Aktion "Blob hochladen"][11]


- **Blobpfad** – Gibt den Pfad des Blobs an, das hochgeladen werden soll. Der Pfad wird relativ zum konfigurierten Containerpfad interpretiert.
- **Blobinhalt schreiben** – Geben Sie den Inhalt und die Eigenschaften des Blobs an, das hochgeladen werden soll.
- **Codierung für die Inhaltsübertragung** – Geben Sie keine oder Base64 an.
- **Überschreiben** – Ist diese Option auf "True" festgelegt, wird ein vorhandenes Blob überschrieben. Andernfalls wird ein Fehler zurückgegeben, wenn ein Blob im gleichen Pfad bereits vorhanden ist.

Stellen Sie die Eingaben bereit, und klicken Sie auf das Häkchen, um die Eingabekonfiguration abzuschließen.


Beachten Sie, dass die konfigurierte Aktion zum Hochladen des Azure Storage-Blobs sowohl Eingabeparameter als auch Ausgabeparameter anzeigt.

####Verwenden der Ausgaben früherer Aktionen als Eingabe für Azure Storage-Blobaktionen
Beachten Sie, dass der Wert für den Inhalt im konfigurierten Screenshot auf einen Ausdruck festgelegt ist.

	@triggers().outputs.body.Content


Sie können ihn auf einen beliebigen Wert festlegen. Dies ist nur ein Beispiel. Der Ausdruck nimmt die Ausgabe des Triggers der Logik-App und verwendet sie als Inhalt der Datei, die hochgeladen werden soll. So können Sie die Ausgabe einer vorherigen Aktion, beispielsweise einer Transformation, verwenden. In diesem Fall lautet der Ausdruck folgendermaßen:

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-azurestorageblob/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG
 

<!---HONumber=62-->