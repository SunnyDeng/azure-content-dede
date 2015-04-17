<properties 
	pageTitle="FTP-Connector"
	description="Erste Schritte mit FTP-Connector"
	authors="rajeshramabathiran" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/05/2015"
	ms.author="rajeshramabathiran"/>

# FTP-Connector

## Übersicht
Mit dem FTP-Connector können Sie Daten von einem und auf einen FTP-Server verschieben. Der FTP-Connector besitzt folgende Hauptfunktionen:

- Abrufen von Dateien vom FTP-Server nach Bedarf
- Ausführen von Umfragen basierend auf einem konfigurierbaren Zeitplan
- Abfragen des FTP-Servers und Auslösen des Logikdatenflusses basierend auf neuen Dokumenten auf dem FTP-Server
- Angeben des FTP-Server als IP-Adresse, Port, Kennwort und Hostnamen
- Möglichkeit zum Ausführen von Sendevorgängen nach Bedarf
- Möglichkeit zum Löschen von Dateien auf dem FTP-Server nach Bedarf

## Erstellen eines neuen FTP-Connectors
Um einen neuen FTP-Connector zu erstellen, führen Sie die unten aufgeführten Schritte aus.
- Starten Sie das Azure-Portal.
- Öffnen Sie den Azure Marketplace über +Neu (unten auf der Seite) -> Web + Mobil -> Azure Marketplace

![Launch Azure Marketplace][1]
- Klicken Sie auf die API-Apps.
- Suchen Sie nach "FTP", und wählen Sie "FTP-Connector".

![Select FTP Connector][2]
- Klicken Sie auf "Erstellen".
- Geben Sie die folgenden Daten im daraufhin geöffneten Blatt zum FTP-Connector an.

![Create FTP Connector][3]

- **Standort** - Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
- **Abonnement** - Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
- **Ressourcengruppe** - Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
- **Webhostingplan** - Wählen oder erstellen Sie einen Webhostingplan.
- **Preisstufe** - Wählen Sie eine Preisstufe für den Connector.
- **Name** - Geben Sie einen Namen für den FTP-Connector ein.
- **Paketeinstellungen** 
	- **Serveradresse** - Geben Sie den FTP-Servernamen oder die IP-Adresse an.
	- **Benutzername** - Geben Sie den Benutzernamen für die Verbindung mit dem FTP-Server an.
	- **Kennwort** - Geben Sie das Kennwort für die Verbindung mit dem FTP-Server an.
	- **Stammordner** - Geben Sie den Pfad des Stammordners an.
	- **Binär verwenden** - Geben Sie "true" an, um den binären Übertragungsmodus zu verwenden, "false", um ASCII zu verwenden.
	- **SSL verwenden** - Geben Sie "true" an, um FTP über einen sicheren SSL-/TLS-Kanal zu verwenden.
	- **Serverport** - Geben Sie die Portnummer des FTP-Servers an.
- Klicken Sie auf "Erstellen". Ein neuer FTP-Connector wird erstellt.

## Verwenden des FTP-Connectors in Logik-Apps
Sobald der FTP-Connector erstellt wurde, kann er vom Datenfluss genutzt werden.

Erstellen Sie einen neuen Datenfluss über +Neu -> Web + Mobil -> Logik-App. Stellen Sie die Metadaten für den Datenfluss samt Ressourcengruppe bereit.

![Create Logic App][4]

Klicken Sie auf *triggers and actions*. Der Datenfluss-Designer wird geöffnet.

![Logic App empty flow designer][5]

Der FTP-Connector kann sowohl als Trigger als auch als Aktion verwendet werden. 

### Trigger
Klicken Sie im leeren Datenfluss-Designer im Galeriebereich rechts auf den FTP-Connector.

![Choose FTP Trigger][6]

Der FTP-Connector besitzt einen Trigger - TriggerOnFileAvailable. Dieser wird ausgelöst, sobald eine neue Datei in einen bestimmten Ordner auf dem FTP-Server hochgeladen wird.

Klicken Sie auf den Trigger 'TriggerOnFileAvailable'.

![Basic inputs FTP Trigger][7]

Anhand der Eingaben können Sie einen bestimmten Ordnerpfad konfigurieren, der mit einer geplanten Häufigkeit abgefragt wird. Grundlegende Eingaben sind:
- Häufigkeit - Gibt die Häufigkeit der FTP-Abfrage an.
- Intervall - Gibt das Intervall für die geplante Häufigkeit an.
- Ordnerpfad - Gibt den Ordnerpfad auf dem FTP-Server an.
- Dateityp - Gibt an, ob es sich um Text- oder Binärdateien handelt.

Durch Klicken auf ... werden die erweiterten Eingaben angezeigt. 

![Basic inputs FTP Trigger][8]

Erweiterte Eingaben sind:
- Dateimaske - Gibt die Dateimaske während des Abrufens an.
- Dateimaske ausschließen - Gibt die Dateimasken an, die während des Abrufens ausgeschlossen werden sollen.

Stellen Sie die Eingaben bereit, und klicken Sie auf das Häkchen, um die Eingabekonfiguration abzuschließen.

![Basic inputs FTP Trigger][9]

Beachten Sie, dass der konfigurierte FTP-Trigger sowohl die konfigurierten Eingabeparameter als auch die Ausgabe des FTP-Triggers anzeigt. 

Sobald die Logik-App erstellt wurde, führt der FTP-Trigger folgende Aktionen aus: 


- Fragt den Ordnerpfad nach neuen Dateien ab.
- Instanziiert den Logikfluss für jede neue Datei.
- Löscht die Datei aus dem Ordnerpfad, nachdem der Logikfluss instanziiert wurde.

#### Verwenden der Ausgabe des FTP-Triggers in nachfolgenden Aktionen
Die Ausgabe des FTP-Triggers kann als Eingabe für einige andere Aktionen im Datenfluss verwendet werden. 

Sie können im Eingabedialogfeld der Aktion auf "+" klicken und die FTP-Ausgabe direkt aus der Dropdownliste auswählen.

Sie können auch einen Ausdruck direkt in das Eingabefeld der Aktion schreiben. Der Datenflussausdruck für den Verweis auf die Ausgabe des FTP-Triggers wird unten aufgeführt.

	@triggers('ftpconnector').outputs.body.Content

### Aktionen
Klicken Sie im rechten Bereich auf den FTP-Connector. Der FTP-Connector listet die unterstützten Aktionen auf.

![List of FTP Actions][10]

Der FTP-Connector unterstützt vier Aktionen. Dies sind:

- **Datei abrufen** - Ruft den Inhalt einer bestimmten Datei ab.
- **Datei hochladen** - Lädt eine Datei in den FTP-Ordnerpfad hoch.
- **Datei löschen** - Löscht eine Datei aus dem FTP-Ordnerpfad.
- **Datei auflisten** - Listet alle Dateien im FTP-Ordnerpfad auf.

Nehmen wir ein Beispiel - Datei hochladen. Klicken Sie auf "Datei hochladen".

Die grundlegenden Eingaben werden zuerst angezeigt.

![Basic inputs of Upload File action][11]


- **Inhalt** - Gibt den Inhalt der Datei an, die hochgeladen werden soll.
- ** Codierung für die Inhaltsübertragung** - Geben Sie keine oder base64 an.
- **Dateipfad** - Gibt den Dateipfad der Datei an, die hochgeladen werden soll.

Klicken Sie auf ..., um die erweiterten Eingaben anzuzeigen.

![Basic inputs of Upload File action][12]


- **Anfügen, falls vorhanden** - Aktivieren oder deaktivieren Sie 'Append If Exist'. Wenn diese Option aktiviert ist, werden die Daten an die Datei angefügt, falls diese vorhanden ist. Ist die Option deaktiviert, wird die Datei überschrieben, falls sie vorhanden ist.
- **Temporärer Ordner** - Optional. Falls angegeben, lädt der Adapter die Datei in den 'Temporary Folder Path' hoch und verschiebt sie nach dem Hochladen in 'Folder Path'. Der 'Temporary Folder Path' sollte sich auf demselben physischen Datenträger befinden wie der 'Folder Path', um sicherzustellen, dass die Verschieben-Operation atomarisch verläuft. Der temporäre Ordner kann nur verwendet werden, wenn die Eigenschaft 'Append If Exist' deaktiviert ist.

Stellen Sie die Eingaben bereit, und klicken Sie auf das Häkchen, um die Eingabekonfiguration abzuschließen.

![Configured Upload File action][13]

Beachten Sie, dass die konfigurierte FTP-Aktion zum Hochladen der Datei sowohl Eingabeparameter als auch Ausgabeparameter anzeigt.

#### Verwenden der Ausgaben früherer Aktionen als Eingabe für eine FTP-Aktion
Beachten Sie, dass der Wert für den Inhalt im konfigurierten Screenshot auf einen Ausdruck festgelegt ist.

	@triggers().outputs.body.Content


Sie können ihn auf einen beliebigen Wert festlegen. Dies ist nur ein Beispiel. Der Ausdruck nimmt die Ausgabe des Triggers der Logik-App und verwendet sie als Inhalt der Datei, die hochgeladen werden soll. So können Sie die Ausgabe einer vorherigen Aktion, beispielsweise einer Transformation, verwenden. In diesem Fall lautet der Ausdruck folgendermaßen:

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-FTP/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-FTP/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-FTP/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-FTP/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-FTP/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-FTP/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-FTP/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-FTP/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-FTP/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-FTP/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-FTP/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-FTP/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-FTP/ConfiguredUploadFile.PNG

<!--HONumber=49-->