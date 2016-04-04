<properties 
   pageTitle="Verwenden des Azure Storage-Blob-Connectors in Logik-Apps | Microsoft Azure App Service" 
   description="Erstellen und Konfigurieren des Azure Storage-Blob-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/16/2016"
   ms.author="rajram"/>
   
# Erste Schritte mit dem Azure Storage-Blob-Connector und das Hinzufügen zur Logik-App 

>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview. Um die Schemaversion 2015-08-01-preview aufzurufen, klicken Sie auf [Azure Storage-Blob-API](../connectors/connectors-create-api-azureblobstorage.md).

Verbinden Sie sich mit Ihrem Azure Storage-Blobconnector, um Blobs in den Blobcontainer hochzuladen, diese daraus herunterladen und zu löschen. Connectors werden als Teil eines "Workflows" in Logik-Apps verwendet.

## Trigger und Aktionen
*Trigger* sind Ereignisse, die stattfinden. Z. B. wenn ein Auftrag aktualisiert oder ein neuer Kunde hinzugefügt wird. Eine *Aktion* ist das Ergebnis des Triggers. Z. B. bei Aktualisierung eines Auftrags eine Warnung an den Verkäufer senden. Oder bei Hinzufügen eines neuen Kunden eine E-Mail zur Begrüßung an den neuen Kunden senden.

Der Speicherblob-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format. Es gibt derzeit keine Trigger für den Speicherblob-Connector.

Der Speicherblob-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Keine | <ul><li>Blob abrufen: Abrufen eines bestimmten Blobs aus dem Container</li><li>Blob hochladen: Hochladen eines neuen Blobs oder Aktualisieren eines vorhandenen Blobs</li><li>Blob löschen: Löschen eines bestimmten Blobs aus einem Container</li><li>Blobs auflisten: Auflisten aller Blobs in einem Verzeichnis</li><li>Momentaufnahme für Blob erstellen : Erstellen einer schreibgeschützten Momentaufnahme eines bestimmten Blobs</li><li>Blob kopieren: Erstellen eines neuen Blobs durch Kopieren eines anderen Blobs. Das Quellblob kann sich in demselben Konto oder in einem anderen Konto befinden.</li></ul>


## Erstellen des Azure Storage-Blobconnectors

Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "Blob": ![Wählen des Azure Storage-Blobconnectors][2]

3. Wählen Sie "Blob" aus, und klicken Sie auf **Erstellen**.
4. Geben Sie den Namen, die App Service-Plan und andere Eigenschaften ein.
5. Geben Sie die folgenden Paketeinstellungen ein:

	Name | Erforderlich | Beschreibung
--- | --- | ---
Container/SAS-URI | Ja | Geben Sie den URI des Blobcontainers ein. Der URI kann auch das SAS-Token umfassen. Geben Sie z. B. http://*storageaccountname*.blob.core.windows.net/containername oder http://*storageaccountname*.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah ein.
Zugriffsschüssel | Nein | Geben Sie einen gültigen primären oder sekundären Zugriffsschlüssel für das Speicherkonto ein. Wenn Sie das SAS-Token für die Authentifizierung verwenden, lassen Sie dieses Feld leer.

	![Erstellen des Azure Storage-Blobconnectors][3]

6. Klicken Sie auf **Erstellen**.

## Verwenden von Azure Storage-Blobconnectors in Logik-App
Sobald der Azure Storage-Blobconnector erstellt wurde, kann er Ihrem Workflow hinzugefügt werden.

1. Erstellen einer neuen Logik App: Neu -> Web + Mobil -> Logik-App. Geben Sie die Eigenschaften für Ihre Logik-App ein: ![Erstellen einer Logik-App][4]

2. Klicken Sie auf **Trigger und Aktionen**. Der Workflow-Designer wird geöffnet: ![Logik-App – Leerer Datenfluss-Designer][5]

3. Wählen Sie im rechten Bereich Ihren Azure Storage-Blobconnector aus. Für den Connector werden die verfügbaren Aktionen aufgeführt: ![Liste der Azure Storage-Blobaktionen][10]

4. In diesem Szenario verwenden wir die Aktion **Blob hochladen**: ![Eingaben der Aktion "Blob hochladen"][11]

5. Geben Sie die Eingabewerte ein, und klicken Sie auf das Häkchen, um die Konfiguration abzuschließen:

	Eingabe | Beschreibung
--- | ---
Blobpfad | Legt den Pfad des hochzuladenden Blobs fest. Der Pfad wird relativ zum konfigurierten Containerpfad interpretiert.
Schreibinhalt des Blobs | Geben Sie den Inhalt und die Eigenschaften des hochzuladenden Blobs ein.
Codierung für die Inhaltsübertragung | Geben Sie "Keine" oder "Base64" ein.
Überschreiben | Falls auf "True" festgelegt, wird das vorhandene Blob überschrieben. Falls auf "False" festgelegt, wird ein Fehler zurückgegeben, wenn im gleichen Pfad bereits ein Blob vorhanden ist.

Beachten Sie, dass die konfigurierte Aktion zum Hochladen des Azure Storage-Blobs sowohl Eingabeparameter als auch Ausgabeparameter anzeigt.

#### Verwenden der Ausgaben früherer Aktionen als Eingabe für Azure Storage-Blobaktionen
Im vorherigen Bildschirmfoto kann der Wert **Inhalt** ein Ausdruck sein:

	@triggers().outputs.body.Content

Sie können ihn auf einen beliebigen Wert festlegen. Der Ausdruck nimmt die Ausgabe des Triggers der Logik-App und verwendet sie als Inhalt der Datei, die hochgeladen werden soll. Sie möchten z. B. die Ausgabe einer Transformation verwenden. In diesem Szenario lautet der Ausdruck:

	@actions('transformservice').outputs.body.OutputXML

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
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
 

<!---HONumber=AcomDC_0323_2016-->