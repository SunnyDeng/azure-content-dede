<properties 
   pageTitle="Verwenden des SharePoint-Connectors in Logik-Apps" 
   description="Verwenden des SharePoint-Connectors in Logik-Apps" 
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
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# Verwenden des SharePoint-Connectors in Logik-Apps

Logik-Apps können basierend auf einer Vielzahl von Datenquellen auslösen und Connectors das Abrufen und Verarbeiten von Daten im Rahmen des Datenflusses ermöglichen. Mit dem Microsoft SharePoint-Connector können Sie eine Verbindung mit dem Microsoft SharePoint-Server/SharePoint Online herstellen und Dokumente und Listenelemente verwalten. Sie können verschiedene Aktionen ausführen, beispielsweise das Erstellen, Aktualisieren, Abrufen und Löschen für Dokumente und Listenelemente. Bei einem lokalen SharePoint-Server können Sie Service Bus-Verbindungszeichenfolgen als Teil der Connectorkonfiguration bereitstellen und den lokalen Listener-Agent für die Verbindung mit dem Server installieren.

Die Galerie-App für den SharePoint Online-Connector und den SharePoint Server-Connector bietet Trigger und Aktionen als Mechanismen für die Interaktion mit SharePoint.

## Erstellen eines SharePoint Online-Connectors für Ihre Logik-App

Zur Verwendung des SharePoint Online-Connectors müssen Sie zunächst eine Instanz der SharePoint Online-Connector-API-App erstellen. Dies kann wie folgt durchgeführt werden:

1. Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten rechts im Azure-Portal.

2. Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "SharePoint Online-Connector".

3. Konfigurieren Sie den SharePoint Online-Connector, und klicken Sie auf "Erstellen". Im Folgenden sind die Parameter aufgeführt, die Sie zum Erstellen des Connectors bereitstellen müssen:

	<table>
	  <tr>
	    <td><b>Name</b></td>
	    <td><b>Erforderlich</b></td>
	    <td><b>Beschreibung</b></td>
	  </tr>
	  <tr>
	    <td>Website-URL</td>
	    <td>Ja</td>
	    <td>Geben Sie die vollständige URL der SharePoint-Website an. Beispiel: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>Relative URLs zur Dokumentbibliothek/Liste</td>
	    <td>Ja</td>
	    <td>Geben Sie die URLS von Dokumentbibliotheken/Listen relativ zur SharePoint-Website-URL an, die vom Connector geändert werden dürfen. Beispiel: Listen/Aufgabe, Freigegebene Dokumente'.</td>
	  </tr>
	</table>
	![][1]


4. Sobald Sie fertig sind, können Sie jetzt in derselben Ressourcengruppe eine Logik-App zur Verwendung des SharePoint Online-Connectors erstellen.

## Erstellen eines SharePoint Server-Connectors für Ihre Logik-App

Zur Verwendung des SharePoint Server-Connectors müssen Sie zunächst eine Instanz der SharePoint Server-Connector-API-App erstellen. Dies kann wie folgt durchgeführt werden:

1. Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten rechts im Azure-Portal.

2. Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "SharePoint Server-Connector".

3. Konfigurieren Sie den SharePoint Server-Connector, und klicken Sie auf "Erstellen". Im Folgenden sind die Parameter aufgeführt, die Sie zum Erstellen des Connectors bereitstellen müssen:

	<table>
	  <tr>
	    <td><b>Name</b></td>
	    <td><b>Erforderlich</b></td>
	    <td><b>Beschreibung</b></td>
	  </tr>
	  <tr>
	    <td>Website-URL</td>
	    <td>Ja</td>
	    <td>Geben Sie die vollständige URL der SharePoint-Website an. Beispiel: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>Authentifizierungsmodus</td>
	    <td>Ja</td>
	    <td>Geben Sie den Authentifizierungsmodus zum Verbinden mit einer SharePoint-Website an. Die zulässigen Werte sind:<br><br>
			Standard<br>
			OAuth2<br>
			WindowsAuthentication<br>
			FormBasedAuthentication<br><br>
	
	Für den Fall, dass Sie die standardmäßigen Anmeldeinformationen auswählen, werden die Standardanmeldeinformationen verwendet, unter denen der SharePoint Microservice ausgeführt wird, und Benutzername und Kennwort sind nicht erforderlich. Die Felder für Benutzername und Kennwort sind für andere Authentifizierungstypen zwingend vorgeschrieben. <br><br>Hinweis: Die anonyme Authentifizierung wird nicht unterstützt.</td>
	  </tr>
	  <tr>
	    <td>Benutzername</td>
	    <td>Nein</td>
	    <td>Geben Sie einen gültigen Benutzernamen zum Verbinden mit einer SharePoint-Website an, wenn als Authentifizierungsmodus nicht Standard/OAuth2 verwendet wird.</td>
	  </tr>
	  <tr>
	    <td>Kennwort</td>
	    <td>Nein</td>
	    <td>Geben Sie ein gültiges Kennwort zum Verbinden mit einer SharePoint-Website an, wenn als Authentifizierungsmodus nicht Standard/OAuth2 verwendet wird.</td>
	  </tr>
	  <tr>
	    <td>Relative URLs zur Dokumentbibliothek/Liste</td>
	    <td>Ja</td>
	    <td>Geben Sie die URLS von Dokumentbibliotheken/Listen relativ zur SharePoint-Website-URL an, die vom Connector geändert werden dürfen. Beispiel: Listen/Aufgabe, Freigegebene Dokumente'.</td>
	  </tr>
	  <tr>
	    <td>Service Bus-Verbindungszeichenfolge</td>
	    <td>Nein</td>
	    <td>Dies muss eine gültige Verbindungszeichenfolge für den Service Bus-Namespace sein.<br><br>
	
	Sie müssen einen Listener-Agent auf einem Server installieren, der auf Ihren SharePoint-Server zugreifen kann. <br>Sie können zur API-App-Seite "Zusammenfassung" wechseln und zum Installieren des Agents auf 'Hybrid Connection' klicken.</td>
	  </tr>
	</table>


	![][2]

4. Sobald Sie fertig sind, können Sie jetzt in derselben Ressourcengruppe eine Logik-App zur Verwendung des SharePoint Server-Connectors erstellen.
5. Sie müssen einen Listener-Agent auf einem Server installieren, der auf Ihren SharePoint-Server zugreifen kann. Sie können zur API-App-Seite "Zusammenfassung" wechseln und zum Installieren des Agents auf 'Hybrid Connection' klicken.

## Verwenden des SharePoint-Connectors in Logik-Apps

Sobald Ihre API-App erstellt wurde, können Sie jetzt den SharePoint-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1. Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der SharePoint-Connector befindet.

2. Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. Der SharePoint-Connector wird im Abschnitt "Zuletzt verwendet" in der Galerie auf der rechten Seite angezeigt. Wählen Sie ihn aus.

3. Wenn der SharePoint-Connector zu Beginn der Logik-App ausgewählt wird, fungiert er als Trigger. Andernfalls können mit dem Connector Aktionen am SharePoint-Konto durchgeführt werden. 

4. Damit die Logik-Apps Operationen in Ihrem Namen durchführen können, müssen Sie bei Verwendung des SharePoint Online-Connectors oder bei Verwendung der OAuth2-Authentifizierung im SharePoint Server-Connector die Logik-Apps authentifizieren und autorisieren. Zum Starten der Autorisierung klicken Sie im SharePoint-Connector auf "Autorisieren". 

	![][3]

5. Durch das Klicken auf "Autorisieren" wird das Authentifizierungsdialogfeld von SharePoint angezeigt. Geben Sie die Anmeldeinformationen des SharePoint-Kontos an, mit dem Sie die Vorgänge ausführen möchten. 

	![][4]
6. Erteilen Sie den Logik-Apps Zugriff auf Ihr Konto, um Vorgänge in Ihrem Namen auszuführen. 

	![][5]

7. Wenn der SharePoint-Connector als Trigger konfiguriert ist, werden die Trigger angezeigt. Andernfalls wird eine Liste von Aktionen angezeigt, sodass Sie die Operation auswählen können, die durchgeführt werden soll.  

	![][6]

	<b>Für Dokumentbibliothek konfigurierte relative URL</b><br><br>

	![][7]

	<b>Für Dokumentliste konfigurierte relative URL</b>

	<b>Hinweis:</b> Für die unten aufgeführten Trigger wird davon ausgegangen, dass der Benutzer 'Shared Documents, Lists/Task' in den Connector-Paketeinstellungen angegeben hat, wobei 'Shared Documents' eine Dokumentbibliothek und 'Lists/Task' eine Liste ist. 

##  Trigger
Verwenden Sie Trigger, wenn Sie eine Logik-App starten möchten. 

### 1.	Neues Dokument in freigegebenen Dokumenten (JSON)
Dieser Trigger wird ausgelöst, wenn ein neues Dokument in 'Shared Documents' verfügbar ist. 

**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Ansichtsname</td>
    <td>Nein</td>
    <td>Geben Sie eine gültige Ansicht zum Filtern der zur Auswahl stehenden Dokumente an. Beispiel: 'Approved Orders'. Lassen Sie dieses Feld leer, um alle vorhandenen Dokumente zu verarbeiten. </td>
  </tr>
  <tr>
    <td>Archivspeicherort</td>
    <td>Nein</td>
    <td>Geben Sie eine gültige Ordner-URL relativ zur SharePoint-Website an, in der die verarbeiteten Dokumente archiviert werden. </td>
  </tr>
  <tr>
    <td>In Archiv überschreiben</td>
    <td>Nein</td>
    <td>Aktivieren Sie diese Option, um eine Datei im Archivpfad zu überschreiben, wenn sie bereits vorhanden ist. </td>
  </tr>
  <tr>
    <td>CAML-Abfrage</td>
    <td>Nein, erweitert</td>
    <td>Geben Sie eine gültige CAML-Abfrage zum Filtern von Dokumenten an. Beispiel: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**Ausgaben:**
<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Name  </td>
    <td>Der Name des Dokuments.</td>
  </tr>
  <tr>
    <td>Inhalt</td>
    <td>Der Inhalt des Dokuments.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codierung für die Inhaltsübertragung der Nachricht. ("none"|"base64")</
  </tr>
</table>


Hinweis: Alle Spalten des Dokumentelements werden in den 'Advanced'-Ausgabeeigenschaften angezeigt.


###2. Neues Element in Aufgaben (JSON)
Dieser Trigger wird ausgelöst, wenn der 'Tasks'-Liste ein neues Element hinzugefügt wird.

**Eingaben:**
<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
 </tr>
  <tr>
    <td>Ansichtsname</td>
    <td>Nein</td>
    <td>Geben Sie eine gültige Ansicht zum Filtern von Elementen in der Liste an. Beispiel: 'Approved Orders'. Lassen Sie dieses Feld leer, um alle neuen Elemente zu verarbeiten. </td>
  </tr>
  <tr>
    <td>Archivspeicherort</td>
    <td>Nein</td>
    <td>Geben Sie eine gültige Ordner-URL relativ zur SharePoint-Website an, in der die verarbeiteten Listenelemente archiviert werden. </td>
  </tr>
  <tr>
    <td>CAML-Abfrage</td>
    <td>Nein, erweitert</td>
    <td>Geben Sie eine gültige CAML-Abfrage zum Filtern der Listenelemente an. Beispiel: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Die Spalten in der Liste werden dynamisch aufgefüllt und in den Ausgabeparametern angezeigt.</td>
    <td> </td>
  </tr>

</table>


###3. Neues Dokument in freigegebenen Dokumenten (XML)

Dieser Trigger wird ausgelöst, wenn ein neues Dokument in 'Shared Documents' verfügbar ist. Das neue Dokument wird als XML-Nachricht zurückgegeben.

**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Ansichtsname</td>
    <td>Nein</td>
    <td>Geben Sie eine gültige Ansicht zum Filtern der zur Auswahl stehenden Dokumente an. Beispiel: 'Approved Orders'. Lassen Sie dieses Feld leer, um alle vorhandenen Dokumente zu verarbeiten. </td>
  </tr>
  <tr>
    <td>Archivspeicherort</td>
    <td>Nein</td>
    <td>Geben Sie eine gültige Ordner-URL relativ zur SharePoint-Website an, in der die verarbeiteten Dokumente archiviert werden. </td>
  </tr>
  <tr>
    <td>In Archiv überschreiben</td>
    <td>Nein</td>
    <td>Aktivieren Sie diese Option, um eine Datei im Archivpfad zu überschreiben, wenn sie bereits vorhanden ist. </td>
  </tr>
  <tr>
    <td>CAML-Abfrage</td>
    <td>Nein, erweitert</td>
    <td>Geben Sie eine gültige CAML-Abfrage zum Filtern von Dokumenten an. Beispiel: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Inhalt</td>
    <td>Der Inhalt des Dokuments.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codierung für die Inhaltsübertragung der Nachricht. ("none"|"base64")</td>
  </tr>
</table>


###4. Neues Element in Aufgaben (XML)

Dieser Trigger wird ausgelöst, wenn der 'Tasks'-Liste ein neues Element hinzugefügt wird. Das neue Listenelement wird als XML-Nachricht zurückgegeben.

**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Ansichtsname</td>
    <td>Nein</td>
    <td>Geben Sie eine gültige Ansicht zum Filtern von Elementen in der Liste an. Beispiel: 'Approved Orders'. Lassen Sie dieses Feld leer, um alle neuen Elemente zu verarbeiten. </td>
  </tr>
  <tr>
    <td>Archivspeicherort</td>
    <td>Nein</td>
    <td>Geben Sie eine gültige Ordner-URL relativ zur SharePoint-Website an, in der die verarbeiteten Listenelemente archiviert werden. </td>
  </tr>
  <tr>
    <td>CAML-Abfrage</td>
    <td>Nein, erweitert</td>
    <td>Geben Sie eine gültige CAML-Abfrage zum Filtern der Listenelemente an. Beispiel: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Inhalt</td>
    <td>Der Inhalt des Dokuments.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codierung für die Inhaltsübertragung der Nachricht. ("none"|"base64")</td>
  </tr>
</table>


##  Aktionen
Für die unten aufgeführten Aktionen wird davon ausgegangen, dass der Benutzer 'Shared Documents, Lists/Task' in den Connector-Paketeinstellungen angegeben hat, wobei 'Shared Documents' eine Dokumentbibliothek und 'Lists/Task' eine Liste ist. 

###1. Upload in freigegebene Dokumente (JSON)

Diese Aktion lädt das neue Dokument in 'Shared Documents' hoch. Die Eingabe ist ein stark typisiertes JSON-Objekt mit allen Spaltenfeldern der Dokumentbibliothek.

**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
 </tr>
  <tr>
    <td>Name</td>
    <td>Ja</td>
    <td>Der Name des Dokuments.</td>
  </tr>
  <tr>
    <td>Inhalt</td>
    <td>Ja</td>
    <td>Der Inhalt des Dokuments.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Ja</td>
    <td>Codierung für die Inhaltsübertragung der Nachricht. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Überschreiben erzwingen</td>
    <td>Ja</td>
    <td>Wenn diese Option auf TRUE festgelegt und ein Dokument mit dem angegebenen Namen vorhanden ist, wird dieses überschrieben.</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Ja</td>
    <td>Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Dokuments zur Dokumentbibliothek.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Ja</td>
    <td>Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Dokuments zur Dokumentbibliothek.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>Nein. Erweitert</td>
    <td>Dies ist einer der optionalen Parameter zum Hinzufügen eines Dokuments zur Dokumentbibliothek.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>Nein. Erweitert</td>
    <td>Dies ist einer der optionalen Parameter zum Hinzufügen eines Dokuments zur Dokumentbibliothek.</td>
  </tr>
</table>

<b>Hinweis:</b> Alle Parameter der Dokumentbibliothek werden dynamisch aufgefüllt. Die erforderlichen Parameter sind sichtbar, während sich die optionalen Parameter im erweiterten Abschnitt befinden.


**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Element-ID des Dokuments, das der Dokumentbibliothek hinzugefügt wurde.</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Ein erfolgreicher Upload des Dokuments gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>


 

###2. Abrufen aus freigegebenen Dokumenten (JSON)
Diese Aktion ruft das Dokument anhand der relativen URL (Ordnerstruktur) des Dokuments aus der Dokumentbibliothek ab.


**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Relativer Dokument-URI</td>
    <td>Nein</td>
    <td>Geben Sie die URL des Dokuments relativ zu 'Shared Documents' an. Beispiel: myspec1, myfolder/order</td>
  </tr>
</table>


**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Inhalt</td>
    <td>Dokumentinhalt</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codierung für die Inhaltsübertragung der Nachricht. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Eine erfolgreiche Aktionsausführung gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
  <tr>
    <td>Param1*</td>
    <td>Dies ist einer der Parameter eines Dokuments in der Dokumentbibliothek.</td>
  </tr>
  <tr>
    <td>Param2*</td>
    <td>Dies ist einer der Parameter eines Dokuments in der Dokumentbibliothek.</td>
  </tr>
</table>

<b>Hinweis:</b> Alle Parameter der Dokumentbibliothek werden dynamisch aufgefüllt und befinden sich im erweiterten Abschnitt.

 

###3. Löschen aus freigegebenen Dokumenten

Diese Aktion löscht das Dokument anhand der relativen URL (Ordnerstruktur) des Dokuments aus der Dokumentbibliothek.

**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Relativer Dokument-URI</td>
    <td>Nein</td>
    <td>Geben Sie die URL des Dokuments relativ zu 'Shared Documents' an. Beispiel: myspec1, myfolder/order</td>
  </tr>
</table>


**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Eine erfolgreiche Aktionsausführung gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>


###4. Einfügen in Aufgaben (JSON)

Diese Aktion fügt der Elementliste ein Element hinzu.

**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Ja</td>
    <td>Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Ja</td>
    <td>Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>Nein. Erweitert</td>
    <td>Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>Nein. Erweitert</td>
    <td>Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.</td>
  </tr>
</table>


<b>Hinweis:</b> Alle Parameter von 'List' werden dynamisch aufgefüllt. Die erforderlichen Parameter sind sichtbar, während sich die optionalen Parameter im erweiterten Abschnitt befinden.

 
**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Element-ID des hinzugefügten Listenelements.</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Ein erfolgreicher Einfügevorgang des Listenelements gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>


###5. Aktualisieren von Aufgaben (JSON)

Diese Aktion aktualisiert ein Element in der Elementliste.

**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Ja</td>
    <td>Element-ID des Listenelements.</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Nein</td>
    <td>Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Nein</td>
    <td>Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>Nein. Erweitert</td>
    <td>Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>Nein. Erweitert</td>
    <td>Dies ist einer der erforderlichen Parameter zum Hinzufügen eines Elements zur Liste.</td>
  </tr>
</table>

<b>Hinweis:</b> Alle Parameter von 'List' werden dynamisch aufgefüllt. Die erforderlichen Parameter sind sichtbar, während sich die optionalen Parameter im erweiterten Abschnitt befinden.


**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Eine erfolgreiche Aktualisierung des Listenelements gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>


###6. Abrufen eines Elements aus Aufgaben (JSON)

Diese Aktion ruft ein Element aus der Liste ab.


**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Ja</td>
    <td>Element-ID des Listenelements.</td>
  </tr>
</table>


**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Column1*</td>
    <td>Dies ist einer der Parameter in der Liste.</td>
  </tr>
  <tr>
    <td>Column2*</td>
    <td>Dies ist einer der Parameter in der Liste.</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Eine erfolgreiche Ausführung der Aktion gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>

<b>Hinweis:</b> Die Spalten in der Liste werden dynamisch aufgefüllt und in den Ausgabeparametern angezeigt.


###7. Löschen von Element aus den Aufgaben

Diese Aktion löscht ein Element aus der Elementliste.

 
**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Ja</td>
    <td>Element-ID des Listenelements.</td>
  </tr>
</table>


**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Ein erfolgreicher Löschvorgang des Listenelements gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>


###8. Auflisten freigegebener Dokumente (JSON)

Diese Aktion listet alle Dokumente in einer Dokumentbibliothek auf. Sie können eine Ansicht oder eine CAML-Abfrage zum Filtern der Dokumente verwenden.  

 
**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Ansichtsname</td>
    <td>Nein</td>
    <td>Geben Sie eine gültige Ansicht zum Filtern der zur Auswahl stehenden Dokumente an. Beispiel: 'Approved Orders'. Lassen Sie dieses Feld leer, um alle vorhandenen Dokumente zu verarbeiten. </td>
  </tr>
  <tr>
    <td>CAML-Abfrage</td>
    <td>Nein</td>
    <td>Geben Sie eine gültige CAML-Abfrage zum Filtern von Dokumenten an. Beispiel: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Dokumente</td>
    <td>Ein Array aller Dokumente. Jedes Dokument verfügt über die folgenden Felder. <br><br>
	Dokumente []<br>
	Name<br>
	Element-ID<br>
	Vollständige URL des Elements<br>
	Erweitert<br>
	Bearbeitungs-URL des Elements<br>
	Listenname<br>
	Vollständige Listen-URL<br>
	</td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Ein erfolgreicher Einfügevorgang des Listenelements gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>


###9. Hochladen in freigegebene Dokumente (XML)

Diese Aktion lädt das neue Dokument in 'Shared Documents' hoch. Das Eingabedokument sollte eine XML-Nutzlast sein. Die Antwort der Aktion ist eine XML-Nutzlast.
 

**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Name</td>
    <td>Ja</td>
    <td>Der Name des Dokuments.</td>
  </tr>
  <tr>
    <td>Inhalt</td>
    <td>Ja</td>
    <td>Der Inhalt des Dokuments.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Ja</td>
    <td>Codierung für die Inhaltsübertragung der Nachricht. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Überschreiben erzwingen</td>
    <td>Ja</td>
    <td>Wenn diese Option auf TRUE festgelegt und ein Dokument mit dem angegebenen Namen vorhanden ist, wird dieses überschrieben.</td>
  </tr>
</table>
 

**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Ausgabe-XML</td>
    <td>Die Antwort der Aktion "Hochladen" im XML-Format.</td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Ein erfolgreicher Upload des Dokuments gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>

###10. Abrufen aus freigegebenen Dokumenten (XML)

Diese Aktion ruft das Dokument anhand der relativen URL (Ordnerstruktur) des Dokuments aus der Dokumentbibliothek ab.

 
**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Relativer Dokument-URI</td>
    <td>Nein</td>
    <td>Geben Sie die URL des Dokuments relativ zu 'Shared Documents' an. Beispiel: myspec1, myfolder/order</td>
  </tr>
  <tr>
    <td>Dateityp</td>
    <td>Ja</td>
    <td>Geben Sie an, ob die Datei eine Binärdatei oder eine Textdatei ist.</td>
  </tr>
</table>


**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Ausgabe-XML</td>
    <td>Dokumentinhalt</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codierung für die Inhaltsübertragung der Nachricht. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Eine erfolgreiche Aktionsausführung gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>

###11. Einfügen in Aufgaben (XML)

Diese Aktion fügt der Elementliste ein Element hinzu. fAls Eingabe wird eine XML-Nutzlast erwartet.

** Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Eingabe-XML</td>
    <td>Ja</td>
    <td>Die XML-Nachricht mit den Werten der Felder des Listenelements, das eingefügt werden soll. Sie können die Transformieren-API-App zum Generieren der XML-Nachricht verwenden.</td>
  </tr>
</table>
 
<b>Hinweis:</b> Alle Parameter von 'List' werden dynamisch aufgefüllt. Die erforderlichen Parameter sind sichtbar, während sich die optionalen Parameter im erweiterten Abschnitt befinden.

 
**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Element-ID des hinzugefügten Listenelements.</td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Ein erfolgreicher Einfügevorgang des Listenelements gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>


###12. Aktualisieren von Aufgaben (XML)

Diese Aktion aktualisiert ein Element in der Elementliste. Als Eingabe wird eine XML-Nutzlast erwartet.


**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Ja</td>
    <td>Element-ID des Listenelements.</td>
  </tr>
  <tr>
    <td>Eingabe-XML</td>
    <td>Ja</td>
    <td>Die XML-Nachricht mit den Werten der Felder des Listenelements, das eingefügt werden soll. Sie können die Transformieren-API-App zum Generieren der XML-Nachricht verwenden.</td>
  </tr>
</table>

<b>Hinweis:</b> Alle Parameter von 'List' werden dynamisch aufgefüllt. Die erforderlichen Parameter sind sichtbar, während sich die optionalen Parameter im erweiterten Abschnitt befinden.

 
**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Eine erfolgreiche Aktualisierung des Listenelements gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>


###13. Abrufen eines Elements aus Aufgaben (XML)

Diese Aktion ruft ein Element aus der Liste ab.


**Eingaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Erforderlich</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Ja</td>
    <td>Element-ID des Listenelements.</td>
  </tr>
</table>

**Ausgaben:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Beschreibung</b></td>
  </tr>
  <tr>
    <td>Ausgabe-XML</td>
    <td>Die XML-Nachricht mit den Werten der Felder des ausgewählten Listenelements. </td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Eine erfolgreiche Ausführung der Aktion gibt den Statuscode 200 (OK) zurück.</td>
  </tr>
</table>


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!--HONumber=49-->