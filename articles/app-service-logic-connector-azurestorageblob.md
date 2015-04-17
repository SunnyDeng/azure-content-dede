<properties 
   pageTitle="Azure Storage-Blob-Connector" 
   description="Erste Schritte mit Azure Storage-Blob-Connector" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/18/2015"
   ms.author="rajram"/>
   
# Azure Storage-Blob-Connector

## Übersicht
Mit dem Azure Storage-Blob-Connector können Sie Blobs aus einem Blob-Container hochladen, herunterladen und löschen.

## Erstellen eines neuen Azure Storage-Blob-Connectors
Um einen neuen Azure Storage-Connector zu erstellen, führen Sie die unten aufgeführten Schritte aus.
- Starten Sie das Azure-Portal.
- Öffnen Sie den Azure Marketplace über +Neu (unten auf der Seite) -> Web + Mobil -> Azure Marketplace

![Launch Azure Marketplace][1]
- Klicken Sie auf die API-Apps.
- Suchen Sie nach _Blob_, und wählen Sie "Azure Storage-Blob-Connector" aus.

![Select Azure Storage Blob Connector][2]
- Klicken Sie auf "Erstellen".
- Geben Sie die folgenden Daten im daraufhin geöffneten Blatt zum Azure Storage-Blob-Connector an.

![Create Azure Storage Blob Connector][3]

- **Standort** - Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
- **Abonnement** - Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
- **Ressourcengruppe** - Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
- **Webhostingplan** - Wählen oder erstellen Sie einen Webhostingplan.
- **Preisstufe** - Wählen Sie eine Preisstufe für den Connector.
- **Name** - Geben Sie einen Namen für den FTP-Connector ein.
- **Paketeinstellungen** 
	- **Container/SAS-URI** - Geben Sie den URI des Blob-Containers an. Der URI kann auch das SAS Token enthalten. Beispiel: http://storageaccountname.blob.core.windows.net/containername oder http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **Zugriffsschlüssel** - Geben Sie einen gültigen primären/sekundären Zugriffsschlüssel für das Speicherkonto an. Lassen Sie dieses Feld leer, wenn Sie SAS-Token für die Authentifizierung verwenden.
- Klicken Sie auf "Erstellen". Ein neuer Azure Storage-Blob-Connector wird erstellt.

## Verwenden von Azure Storage-Blob-Connectors in Logik-App
Sobald der Azure Storage-Blob-Connector erstellt wurde, kann er vom Datenfluss genutzt werden.

Erstellen Sie einen neuen Datenfluss über +Neu -> Web + Mobil -> Logik-App. Stellen Sie die Metadaten für den Datenfluss samt Ressourcengruppe bereit.

![Create Logic App][4]

Klicken Sie auf *triggers and actions*. Der Datenfluss-Designer wird geöffnet.

![Logic App empty flow designer][5]

Der Azure Storage-Blob-Connector kann sowohl als Trigger als auch als Aktion verwendet werden. 

### Trigger
Klicken Sie im leeren Datenfluss-Designer im Galeriebereich rechts auf den Azure Storage-Blob-Connector.

![Choose Blob Available Trigger][6]

Der Azure Storage-Blob-Connector enthält einen Trigger - _BlobAvailable_. Dieser Trigger sucht im angegebenen Container nach Blobs. Der Abruf auf Verzeichnisebene und das Filtern von Blobs werden ebenfalls unterstützt. Sobald sie abgerufen wurden, werden Blobs aus dem Container gelöscht.

Klicken Sie auf den Trigger _BlobAvailable_.

![Basic inputs Blob Available Trigger][7]

Anhand der Eingaben können Sie einen bestimmten Ordnerpfad konfigurieren, der mit einer geplanten Häufigkeit abgefragt wird. Grundlegende Eingaben sind:
- Häufigkeit - Gibt die Häufigkeit der FTP-Abfrage an.
- Intervall - Gibt das Intervall für die geplante Häufigkeit an.
- Ordnerpfad - Gibt einen virtuellen Ordnerpfad für die Abfrage an. Verwenden Sie '.' für den Stammordner des Containers.
- Dateityp - Gibt eine Dateimaske an, die mit den Blob-Dateinamen verglichen wird.  Nur Blobs mit Dateinamen, die dieser Dateimaske entsprechen, werden bei der Abfrage berücksichtigt. Standardmäßig werden alle Blobs berücksichtigt.

Durch Klicken auf ... werden die erweiterten Eingaben angezeigt. 

![Advanced inputs Blob Available Trigger][8]

Erweiterte Eingaben sind:

- Dateimaske - Gibt eine Dateimaske an, die mit den Blob-Dateinamen verglichen wird.  Nur Blobs mit Dateinamen, die dieser Dateimaske entsprechen, werden bei der Abfrage berücksichtigt. Standardmäßig werden alle Blobs berücksichtigt.
- Dateimaske ausschließen - Gibt eine Dateimaske an, die mit den Blob-Dateinamen verglichen wird.  Mit dieser Dateimaske übereinstimmende Blobs werden ausgeschlossen. Standardmäßig wird kein Blob ausgeschlossen.

Stellen Sie die Eingaben bereit, und klicken Sie auf das Häkchen, um die Eingabekonfiguration abzuschließen.

![Configured Blob Available Trigger][9]

Beachten Sie, dass der konfigurierte Trigger _Blob Available_ sowohl die konfigurierten Eingabeparameter als auch die Ausgabeparameter anzeigt. 

Sobald die Logik-App erstellt wurde, führt der Trigger _Blob Available_ folgende Aktionen aus: 


- Fragt den Ordnerpfad nach neuen Dateien ab.
- Instanziiert den Logikfluss für jede neue Datei.
- Löscht die Datei aus dem Ordnerpfad, nachdem der Logikfluss instanziiert wurde.

#### Verwenden der Ausgabe des Triggers "Blob Available" in nachfolgenden Aktionen
Die Ausgabe des Triggers _Blob Available_ kann als Eingabe für einige andere Aktionen im Datenfluss verwendet werden. 

Sie können im Eingabedialogfeld der Aktion auf "+" klicken und die FTP-Ausgabe direkt aus der Dropdownliste auswählen.

Sie können auch einen Ausdruck direkt in das Eingabefeld der Aktion schreiben. Der Datenflussausdruck für den Verweis auf die Ausgabe des Triggers wird unten aufgeführt.

	@triggers().outputs.body.Content

### Aktionen
Klicken Sie im rechten Bereich auf "Azure Storage-Blob-Connector". Der Connector listet die unterstützten Aktionen auf.

![List of Azure Storage Blob Actions][10]

Azure Storage-Blob-Connector unterstützt vier Aktionen. Dies sind:

- **Blob abrufen** - Abrufen eines bestimmten Blobs aus dem Container
- **Blob hochladen** - Hochladen eines neuen Blobs oder Aktualisieren eines vorhandenen Blobs
- **Blob löschen** - Löschen eines bestimmten Blobs aus einem Container
- **Blobs auflisten** - Auflisten aller Blobs in einem Verzeichnis
- **Blob-Momentaufnahme erstellen** - Erstellen einer schreibgeschützten Momentaufnahme eines bestimmten Blobs
- **Blob kopieren** - Erstellen eines neuen Blobs durch Kopieren von einem anderen Blob  Das Quell-Blob kann sich in demselben Konto oder in einem anderen Konto befinden.

Nehmen wir ein Beispiel - Blob hochladen. Klicken Sie auf "Blob hochladen".

![Inputs of Upload Blob action][11]


- **Blob-Pfad** - Gibt den Pfad des Blobs an, das hochgeladen werden soll.  Der Pfad wird relativ zum konfigurierten Containerpfad interpretiert.
- **Blob-Inhalt schreiben** - Geben Sie den Inhalt und die Eigenschaften des Blobs an, das hochgeladen werden soll.
- ** Codierung für die Inhaltsübertragung** - Geben Sie keine oder Base64 an.
- **Überschreiben** - Ist diese Option auf "True" festgelegt, wird ein vorhandenes Blob überschrieben. Andernfalls wird ein Fehler zurückgegeben, wenn ein Blob im gleichen Pfad bereits vorhanden ist.

Stellen Sie die Eingaben bereit, und klicken Sie auf das Häkchen, um die Eingabekonfiguration abzuschließen.


Beachten Sie, dass die konfigurierte Aktion zum Hochladen des Azure Storage-Blobs sowohl Eingabeparameter als auch Ausgabeparameter anzeigt.

#### Verwenden der Ausgaben früherer Aktionen als Eingabe für eine FTP-Aktion
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

<!--HONumber=49-->