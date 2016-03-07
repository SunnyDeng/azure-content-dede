<properties
   pageTitle="Verwendung des SharePoint-Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des SharePoint-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
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
   ms.date="02/11/2016"
   ms.author="sameerch"/>

# Erste Schritte mit dem SharePoint-Connector und das Hinzufügen zur Logik-App
>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview.

Stellen Sie eine Verbindung mit dem SharePoint-Server oder mit SharePoint Online her, um Dokumente und Listenelemente zu verwalten. Sie können verschiedene Aktionen ausführen, beispielsweise das Erstellen, Aktualisieren, Abrufen und Löschen für Dokumente und Listenelemente. Bei Verwendung eines lokalen SharePoint-Servers geben Sie Service Bus-Verbindungszeichenfolgen als Teil der Connectorkonfiguration ein installieren den lokalen Listener-Agent für die Verbindung mit dem Server.

Die Katalog-App für den SharePoint Online-Connector und den SharePoint Server-Connector bietet Trigger und Aktionen als Mechanismen für die Interaktion mit SharePoint.

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten. Sie können den SharePoint-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.

## Erstellen Sie einen SharePoint Online-Connector

Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Wählen Sie **API-Apps** aus und suchen Sie nach "SharePoint Online-Connector".
3. Geben Sie den Namen, die App Service-Plan und andere Eigenschaften ein.
4. Geben Sie die folgenden Paketeinstellungen ein:

	Name | Erforderlich | Beschreibung
--- | --- | ---
Website-URL | Ja | Geben Sie die vollständige URL der SharePoint-Website ein. Geben Sie z. B. Folgendes ein: **https://microsoft.sharepoint.com/teams/wabstest*. Document Library / List Relative URLs | Yes | Geben Sie die URLS von Dokumentbibliotheken/Listen relativ zur SharePoint-Website-URL ein, die vom Connector geändert werden dürfen. Geben Sie z. B. ein: *Listen/Aufgabe, Freigegebene Dokumente*.

5. Nach Abschluss des Vorgangs sehen die Paketeinstellungen etwa wie folgt aus: ![][1]

Sobald Sie fertig sind, können Sie jetzt in derselben Ressourcengruppe eine Logik-App zur Verwendung des SharePoint Online-Connectors erstellen.

## Erstellen eines SharePoint Server-Connectors

Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Wählen Sie **API-Apps** aus und suchen Sie nach "SharePoint Server-Connector".
3. Geben Sie den Namen, die App Service-Plan und andere Eigenschaften ein.
4. Geben Sie die folgenden Paketeinstellungen ein:

	Name | Erforderlich | Beschreibung
--- | --- | ---
Website-URL | Ja | Geben Sie die vollständige URL der SharePoint-Website ein. Geben Sie z. B. Folgendes ein: *https://microsoft.sharepoint.com/teams/wabstest*. Authentication Mode | Yes | Geben Sie den Authentifizierungsmodus zum Verbinden mit einer SharePoint-Website ein. Optionen umfassen:<ul><li>Standard</li><li>WindowsAuthentication</li><li>FormBasedAuthentication</li></ul><br/><br/>Wenn Sie "Standard" wählen, werden die Anmeldeinformationen für den SharePoint-Connector verwendet. Benutzername und Kennwort sind nicht erforderlich. Benutzername und Kennwort sind für andere Authentifizierungstypen erforderlich.<br/><br/>** Hinweis ** anonyme Authentifizierung wird nicht unterstützt.
Benutzername | Nein | Geben Sie einen gültigen Benutzernamen zum Verbinden mit einer SharePoint-Website ein, wenn als Authentifizierungsmodus nicht "Standard" verwendet wird.
Kennwort | Nein | Geben Sie ein gültiges Kennwort zum Verbinden mit einer SharePoint-Website ein, wenn als Authentifizierungsmodus nicht "Standard" verwendet wird.
Relative URLs zur Dokumentbibliothek/Liste | Ja | Geben Sie die URLS von Dokumentbibliotheken/Listen relativ zur SharePoint-Website-URL ein, die vom Connector geändert werden dürfen. Geben Sie z. B. ein: *Listen/Aufgabe, Freigegebene Dokumente*.
Service Bus-Verbindungszeichenfolge | Nein | Wenn Sie lokale eine Verbindung herstellen, geben Sie die Verbindungszeichenfolge von Service Bus Relay ein.<br/><br/>[Verwendung mit dem Hybrid Connection Manager](app-service-logic-hybrid-connection-manager.md)<br/>[Service Bus-Preise](https://azure.microsoft.com/pricing/details/service-bus/)

5. Nach Abschluss des Vorgangs sehen die Paketeinstellungen etwa wie folgt aus: ![][2]

Sobald Sie fertig sind, können Sie jetzt in derselben Ressourcengruppe eine Logik-App zur Verwendung des SharePoint Server-Connectors erstellen.


## Verwenden des SharePoint-Connectors in Logik-Apps

Sobald Ihre API-App erstellt wurde, können Sie jetzt den SharePoint-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1. Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der SharePoint-Connector befindet.

2. Öffnen Sie **Trigger und Aktionen**, um den Logik-Apps-Designer zu öffnen und den Workflow zu konfigurieren. Der SharePoint-Connector wird im Abschnitt "Zuletzt verwendet" im Katalog auf der rechten Seite angezeigt. Wählen Sie dies aus.

3. Wenn der SharePoint-Connector am Anfang der Logik-App aktiviert ist, fungiert dieser wie ein Trigger. Andernfalls könnte Aktionen auf dem SharePoint-Konto über den Connector ausgeführt werden.

4. Wenn Sie den SharePoint-Online-Connector verwenden, müssen Logik-Apps in Ihrem Namen authentifiziert und autorisiert werden. Zum Starten der Autorisierung klicken Sie im SharePoint-Connector auf **Autorisieren**: ![][3]

5. Durch das Klicken auf "Autorisieren" wird das Authentifizierungsdialogfeld von SharePoint angezeigt. Geben Sie die Anmeldeinformationen des SharePoint-Kontos an, mit dem Sie die Vorgänge ausführen möchten: ![][4]

6. Erteilen Sie Logik-Apps Zugriff auf Ihr Konto, um Vorgänge in Ihrem Namen auszuführen: ![][5]

7. Wenn der SharePoint-Connector als Trigger konfiguriert ist, werden die Trigger angezeigt. Andernfalls wird die Liste der Aktionen angezeigt, und Sie können die entsprechenden Vorgänge auswählen, die Sie ausführen möchten: ![][6]
  
**Für Dokumentbibliothek konfigurierte relative URL**![][7]

**Für Dokumentliste konfigurierte relative URL**

> [AZURE.NOTE]Für die unten aufgeführten Trigger wird davon ausgegangen, dass Sie in den Connector-Paketeinstellungen "Freigegebene Dokumente, Listen/Aufgabe" angegeben hat, wobei es sich bei "Freigegebene Dokumente" um eine Dokumentbibliothek und bei "Listen/Aufgabe" um eine Liste handelt.

##  Trigger
Verwenden Sie Trigger, wenn Sie eine Logik-App starten möchten.

> [AZURE.NOTE] Trigger löschen die Dateien nach dem Lesen. Um diese Dateien zu erhalten, geben Sie einen Wert für den Archivspeicherort ein.

### 1\. Neues Dokument in freigegebenen Dokumenten (JSON)
Dieser Trigger wird ausgelöst, wenn ein neues Dokument in "Freigegebene Dokumente" verfügbar ist.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Ansichtsname | Nein | Geben Sie eine gültige Ansicht zum Filtern der zur Auswahl stehenden Dokumente ein. Geben Sie z. B. "Genehmigte Aufträge" ein. Lassen Sie dieses Feld leer, um alle vorhandenen Dokumente zu verarbeiten.
Archivspeicherort | Nein | Geben Sie eine gültige Ordner-URL relativ zur SharePoint-Website ein, in der die verarbeiteten Dokumente archiviert werden.
In Archiv überschreiben | Nein | Aktivieren Sie diese Option, um eine Datei im Archivpfad zu überschreiben, wenn sie bereits vorhanden ist.
CAML-Abfrage | Nein, erweitert | Geben Sie eine gültige CAML-Abfrage zum Filtern von Dokumenten ein. Geben Sie z. B. Folgendes ein: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Ausgabe

Name | Beschreibung
--- | ---
Name | Name des Dokuments
Inhalt | Der Inhalt des Dokuments.
ContentTransferEncoding | Codierung für die Inhaltsübertragung der Nachricht. ("none"oder "base64")

**Hinweis:** Alle Spalten des Dokumentelements werden in den Ausgabeeigenschaften "Erweitert" angezeigt.


### 2\. Neues Element in Aufgaben (JSON)
Dieser Trigger wird ausgelöst, wenn der Liste "Aufgaben" ein neues Element hinzugefügt wird.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Ansichtsname | Nein | Geben Sie eine gültige Ansicht zum Filtern von Elementen in der Liste ein. Geben Sie z. B. "Genehmigte Aufträge" ein. Lassen Sie dieses Feld leer, um alle neuen Elemente zu verarbeiten.
Archivspeicherort | Nein | Geben Sie eine gültige Ordner-URL relativ zur SharePoint-Website ein, in der die verarbeiteten Listenelemente archiviert werden.
CAML-Abfrage | Nein, erweitert | Geben Sie eine gültige CAML-Abfrage zum Filtern von Dokumenten ein. Geben Sie z. B. Folgendes ein: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Ausgabe

Name | Beschreibung
--- | ---
Die Spalten in der Liste werden dynamisch aufgefüllt und in den Ausgabeparametern angezeigt. | &nbsp;


### 3\. Neues Dokument in freigegebenen Dokumenten (XML)

Dieser Trigger wird ausgelöst, wenn ein neues Dokument in "Freigegebene Dokumente" verfügbar ist. Das neue Dokument wird als XML-Nachricht zurückgegeben.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Ansichtsname | Nein | Geben Sie eine gültige Ansicht zum Filtern der zur Auswahl stehenden Dokumente ein. Geben Sie z. B. "Genehmigte Aufträge" ein. Lassen Sie dieses Feld leer, um alle vorhandenen Dokumente zu verarbeiten.
Archivspeicherort | Nein | Geben Sie eine gültige Ordner-URL relativ zur SharePoint-Website ein, in der die verarbeiteten Listendokumente archiviert werden.
In Archiv überschreiben | Nein | Aktivieren Sie diese Option, um eine Datei im Archivpfad zu überschreiben, wenn sie bereits vorhanden ist.
CAML-Abfrage | Nein, erweitert | Geben Sie eine gültige CAML-Abfrage zum Filtern von Dokumenten ein. Geben Sie z. B. Folgendes ein: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Ausgabe

Name | Beschreibung
--- | ---
Inhalt | Der Inhalt des Dokuments.
ContentTransferEncoding | Codierung für die Inhaltsübertragung der Nachricht. ("none"oder "base64")


### 4\. Neues Element in Aufgaben (XML)

Dieser Trigger wird ausgelöst, wenn der Liste "Aufgaben" ein neues Element hinzugefügt wird. Das neue Listenelement wird als XML-Nachricht zurückgegeben.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Ansichtsname | Nein | Geben Sie eine gültige Ansicht zum Filtern von Elementen in der Liste ein. Beispiel: "Genehmigte Aufträge". Lassen Sie dieses Feld leer, um alle neuen Elemente zu verarbeiten.
Archivspeicherort | Nein | Geben Sie eine gültige Ordner-URL relativ zur SharePoint-Website ein, in der die verarbeiteten Listenelemente archiviert werden.
CAML-Abfrage | Nein, erweitert | Geben Sie eine gültige CAML-Abfrage zum Filtern der Listenelemente ein. Geben Sie z. B. Folgendes ein: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Ausgabe

Name | Beschreibung
--- | ---
Inhalt | Der Inhalt des Dokuments.
ContentTransferEncoding | Codierung für die Inhaltsübertragung der Nachricht. ("none"oder "base64")


##  Aktionen
Für die unten aufgeführten Aktionen wird davon ausgegangen, dass Sie in den Connector-Paketeinstellungen "Freigegebene Dokumente, Listen/Aufgabe" angegeben hat, wobei es sich bei "Freigegebene Dokumente" um eine Dokumentbibliothek und bei "Listen/Aufgabe" um eine Liste handelt.

### 1\. Upload in freigegebene Dokumente (JSON)

Diese Aktion lädt das neue Dokument in "Freigegebene Dokumente" hoch. Die Eingabe ist ein stark typisiertes JSON-Objekt mit allen Spaltenfeldern der Dokumentbibliothek.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Name | Ja | Der Name des Dokuments.
Inhalt | Ja | Der Inhalt des Dokuments.
ContentTransferEncoding | Ja | Codierung für die Inhaltsübertragung der Nachricht. ("none"oder "base64")
Überschreiben erzwingen | Ja | Wenn diese Option auf TRUE festgelegt und ein Dokument mit dem angegebenen Namen vorhanden ist, wird dieses überschrieben.
ReqParam1* | Ja | Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Dokuments zur Dokumentbibliothek.
ReqParam2* | Ja | Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Dokuments zur Dokumentbibliothek.
OptionalParam1* | Nein. Erweitert | Dies ist einer der optionalen Parameter zum Hinzufügen eines Dokuments zur Dokumentbibliothek.
OptionalParam2* | Nein. Erweitert | Dies ist einer der optionalen Parameter zum Hinzufügen eines Dokuments zur Dokumentbibliothek.

**Hinweis:** Alle Parameter der Dokumentbibliothek werden dynamisch aufgefüllt. Die erforderlichen Parameter sind sichtbar, während sich die optionalen Parameter im erweiterten Abschnitt befinden.

#### Ausgabe

Name | Beschreibung
--- | ---
ItemId | Element-ID des Dokuments, das der Dokumentbibliothek hinzugefügt wurde.
Status | Ein erfolgreicher Upload des Dokuments gibt den Statuscode 200 (OK) zurück.


 

### 2\. Abrufen aus freigegebenen Dokumenten (JSON)
Diese Aktion ruft das Dokument anhand der relativen URL (Ordnerstruktur) des Dokuments aus der Dokumentbibliothek ab.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Relativer Dokument-URI | Nein | Geben Sie die URL des Dokuments relativ zu "Freigegebene Dokumente" ein. Geben Sie z. B. Folgendes ein: *myspec1, myfolder/orders*.

#### Ausgabe

Name | Beschreibung
--- | ---
Inhalt | Dokumentinhalt
ContentTransferEncoding | Codierung für die Inhaltsübertragung der Nachricht. ("none"oder "base64")
Status | Eine erfolgreiche Aktionsausführung gibt den Statuscode 200 (OK) zurück.
Param1* | Dies ist einer der Parameter eines Dokuments in der Dokumentbibliothek.
Param2* | Dies ist einer der Parameter eines Dokuments in der Dokumentbibliothek.

**Hinweis:** Alle Parameter der Dokumentbibliothek werden dynamisch aufgefüllt. Sie befinden sich im erweiterten Abschnitt.

 

### 3\. Löschen aus freigegebenen Dokumenten

Diese Aktion löscht das Dokument anhand der relativen URL (Ordnerstruktur) des Dokuments aus der Dokumentbibliothek.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Relativer Dokument-URI | Nein | Geben Sie die URL des Dokuments relativ zu "Freigegebene Dokumente" ein. Geben Sie z. B. Folgendes ein: *myspec1, myfolder/orders*.

#### Ausgabe

Name | Beschreibung
--- | ---
Status | Eine erfolgreiche Aktionsausführung gibt den Statuscode 200 (OK) zurück.


### 4\. Einfügen in Aufgaben (JSON)

Diese Aktion fügt der Elementliste ein Element hinzu.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
ReqParam1* | Ja | Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.
ReqParam2* | Ja | Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.
OptionalParam1* | Nein. Erweitert | Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.
OptionalParam2* | Nein. Erweitert | Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.

**Hinweis:** Alle Parameter der "Liste" werden dynamisch aufgefüllt. Die erforderlichen Parameter sind sichtbar, während sich die optionalen Parameter im erweiterten Abschnitt befinden.

#### Ausgabe

Name | Beschreibung
--- | ---
ItemId | Element-ID des hinzugefügten Listenelements.
Status | Ein erfolgreicher Einfügevorgang des Listenelements gibt den Statuscode 200 (OK) zurück.


### 5\. Aktualisieren von Aufgaben (JSON)

Diese Aktion aktualisiert ein Element in der Elementliste.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
ItemId | Ja | Element-ID des Listenelements.
ReqParam1* | Nein | Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.
ReqParam2* | Nein | Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.
OptionalParam1* | Nein. Erweitert | Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.
OptionalParam2* | Nein. Erweitert | Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.

**Hinweis:** Alle Parameter der "Liste" werden dynamisch aufgefüllt. Die erforderlichen Parameter sind sichtbar, während sich die optionalen Parameter im erweiterten Abschnitt befinden.

#### Ausgabe

Name | Beschreibung
--- | ---
Status | Eine erfolgreiche Aktualisierung des Listenelements gibt den Statuscode 200 (OK) zurück.


### 6\. Abrufen eines Elements aus Aufgaben (JSON)

Diese Aktion ruft ein Element aus der Liste ab.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
ItemId | Ja | Element-ID des Listenelements

#### Ausgabe

Name | Beschreibung
--- | ---
Column1* | Dies ist einer der Parameter in der Liste.
Column2* | Dies ist einer der Parameter in der Liste.
Status | Eine erfolgreiche Ausführung der Aktion gibt den Statuscode 200 (OK) zurück.

**Hinweis:** Die Spalten in der Liste werden dynamisch aufgefüllt und in den Ausgabeparametern angezeigt.


### 7\. Löschen von Element aus den Aufgaben

Diese Aktion löscht ein Element aus der Elementliste.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
ItemId | Ja | Element-ID des Listenelements

#### Ausgabe

Name | Beschreibung
--- | ---
Status | Ein erfolgreicher Löschvorgang des Listenelements gibt den Statuscode 200 (OK) zurück.


### 8\. Auflisten freigegebener Dokumente (JSON)

Diese Aktion listet alle Dokumente in einer Dokumentbibliothek auf. Sie können eine Ansicht oder eine CAML-Abfrage zum Filtern der Dokumente verwenden.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Ansichtsname | Nein | Geben Sie eine gültige Ansicht zum Filtern der zur Auswahl stehenden Dokumente ein. Geben Sie z. B. "Genehmigte Aufträge" ein. Lassen Sie dieses Feld leer, um alle vorhandenen Dokumente zu verarbeiten.
CAML-Abfrage | Nein | Geben Sie eine gültige CAML-Abfrage zum Filtern von Dokumenten ein. Geben Sie z. B. Folgendes ein: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Ausgabe

Name | Beschreibung
--- | ---
Dokumente | Ein Array aller Dokumente. Jedes Dokument verfügt über die folgenden Felder: <ul><li>Dokumente </li><li>Name</li><li>Element-Id</li><li>Vollständige URL des Elements</li><li>Erweitert</li><li>Element-URL bearbeiten</li><li>Listenname</li><li>Vollständige URL der Liste</li></ul>
Status | Ein erfolgreicher Einfügevorgang des Listenelements gibt den Statuscode 200 (OK) zurück.


### 9\. Hochladen in freigegebene Dokumente (XML)

Diese Aktion lädt das neue Dokument in "Freigegebene Dokumente" hoch. Das Eingabedokument sollte eine XML-Nutzlast sein. Die Antwort der Aktion ist eine XML-Nutzlast.
 
#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Name | Ja | Der Name des Dokuments.
Inhalt | Ja | Der Inhalt des Dokuments.
ContentTransferEncoding | Ja | Codierung für die Inhaltsübertragung der Nachricht. ("none"oder "base64")
Überschreiben erzwingen | Ja | Wenn diese Option auf TRUE festgelegt und ein Dokument mit dem angegebenen Namen vorhanden ist, wird dieses überschrieben.
 
#### Ausgabe

Name | Beschreibung
--- | ---
Ausgabe-XML | Die Antwort der Aktion "Hochladen" im XML-Format.
Status | Ein erfolgreicher Upload des Dokuments gibt den Statuscode 200 (OK) zurück.

### 10\. Abrufen aus freigegebenen Dokumenten (XML)

Diese Aktion ruft das Dokument anhand der relativen URL (Ordnerstruktur) des Dokuments aus der Dokumentbibliothek ab.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Relativer Dokument-URI | Nein | Geben Sie die URL des Dokuments relativ zu "Freigegebene Dokumente" ein. Geben Sie z. B. Folgendes ein: *myspec1, myfolder/orders*.
Dateityp | Ja | Geben Sie ein, ob die Datei eine Binärdatei oder eine Textdatei ist.

#### Ausgabe

Name | Beschreibung
--- | ---
Ausgabe-XML | Dokumentinhalt
ContentTransferEncoding | Codierung für die Inhaltsübertragung der Nachricht. ("none"oder "base64")
Status | Eine erfolgreiche Aktionsausführung gibt den Statuscode 200 (OK) zurück.

### 11\. Einfügen in Aufgaben (XML)

Diese Aktion fügt der Elementliste ein Element hinzu. Als Eingabe wird eine XML-Nutzlast erwartet.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
Eingabe-XML | Ja | Die XML-Nachricht mit den Werten der Felder des Listenelements, das eingefügt werden soll. Sie können die Transformieren-API-App zum Generieren der XML-Nachricht verwenden.

**Hinweis:** Alle Parameter der "Liste" werden dynamisch aufgefüllt. Die erforderlichen Parameter sind sichtbar, während sich die optionalen Parameter im erweiterten Abschnitt befinden.

#### Ausgabe

Name | Beschreibung
--- | ---
ItemId | Element-ID des hinzugefügten Listenelements.
Status | Ein erfolgreicher Einfügevorgang des Listenelements gibt den Statuscode 200 (OK) zurück.


### 12\. Aktualisieren von Aufgaben (XML)

Diese Aktion aktualisiert ein Element in der Elementliste. Als Eingabe wird eine XML-Nutzlast erwartet.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
ItemID | Ja | Element-ID des Listenelements.
Eingabe-XML | Ja | Die XML-Nachricht mit den Werten der Felder des Listenelements, das eingefügt werden soll. Sie können die Transformieren-API-App zum Generieren der XML-Nachricht verwenden.

**Hinweis:** Alle Parameter der "Liste" werden dynamisch aufgefüllt. Die erforderlichen Parameter sind sichtbar, während sich die optionalen Parameter im erweiterten Abschnitt befinden.

#### Ausgabe

Name | Beschreibung
--- | ---
Status | Eine erfolgreiche Aktualisierung des Listenelements gibt den Statuscode 200 (OK) zurück.


### 13\. Abrufen eines Elements aus Aufgaben (XML)

Diese Aktion ruft ein Element aus der Liste ab.

#### Eingabe

Name | Erforderlich | Beschreibung
--- | --- | ---
ItemID | Ja | Element-ID des Listenelements

#### Ausgabe

Name | Beschreibung
--- | ---
Ausgabe-XML | Die XML-Nachricht mit den Werten der Felder des ausgewählten Listenelements.
Status | Eine erfolgreiche Ausführung der Aktion gibt den Statuscode 200 (OK) zurück.


## Hybridkonfiguration (optional)

> [AZURE.NOTE] Dieser Schritt ist nur bei lokaler Verwendung von SharePoint hinter Ihrer Firewall erforderlich.

App Service verwendet den Hybrid-Konfigurations-Manager, um sicher eine Verbindung auf Ihr lokales System herzustellen. Wenn Ihr Connector SharePoint Server lokal verwendet, ist der Hybrid Connection Manager erforderlich.

Informationen finden Sie unter [Hybrid Connection Manager konfigurieren](app-service-logic-hybrid-connection-manager.md).

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Wenn Sie sich zunächst mit Azure-Logik-Apps vertraut machen möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie [Logik-Apps testen](https://tryappservice.azure.com/?appservice=logic). Sie können sofort eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [API-Apps und Connector verwalten und überwachen](../app-service-api/app-service-api-manage-in-portal.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!---HONumber=AcomDC_0224_2016-->