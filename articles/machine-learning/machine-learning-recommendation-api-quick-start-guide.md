<properties 
	pageTitle="Schnellstartanleitung: Machine Learning-Empfehlungs-API | Microsoft Azure" 
	description="Azure Machine Learning-Empfehlungen – Erste Schritte" 
	services="machine-learning" 
	documentationCenter="" 
	authors="luisca" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2015" 
	ms.author="luisca"/>

# Kurzanleitung für die Machine Learning Empfehlungen-API

Dieses Dokument beschreibt, wie Sie Ihren Dienst oder Ihre Anwendung zur Verwendung von Microsoft Azure Machine Learning-Empfehlungen einrichten. Weitere Informationen zur Empfehlungs-API finden Sie im [Katalog](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Allgemeine Übersicht

Zur Verwendung von Azure Machine Learning-Empfehlungen müssen Sie die folgenden Schritte ausführen:

* Erstellen eines Modells – ein Modell ist ein Container für Ihre Nutzungsdaten, Katalogdaten und das Empfehlungsmodell.
* Importieren von Katalogdaten – Kataloge enthalten Metadateninformationen zu den Elementen. 
* Importieren von Nutzungsdaten – Nutzungsdaten können auf zwei Arten (oder beide Arten) hochgeladen werden:
	* Durch Hochladen einer Datei, die die Verwendung von Daten enthält.
	* Durch das Senden von Ereignissen zur Datenerfassung.
	In der Regel laden Sie eine Nutzungsdatei hoch, damit Sie ein erstes Empfehlungsmodell (bootstrap) erstellen und dieses verwenden können, bis das System über das Datenerfassungsformat ausreichend Daten gesammelt hat.
* Entwickeln eines Empfehlungsmodells – Dies ist ein asynchroner Vorgang, in dem das Empfehlungssystem anhand aller Nutzungsdaten ein Empfehlungsmodell erstellt. Dieser Vorgang dauert einige Minuten oder mehrere Stunden, je nach Größe der Daten und der Build-Konfigurationsparameter. Beim Auslösen des Builds erhalten Sie eine Build-ID. Überprüfen Sie anhand dieser ID, wann der Buildprozess beendet wurde, bevor Sie beginnen, die Empfehlungen zu nutzen.
* Empfehlungen nutzen – Sie erhalten Empfehlungen für ein bestimmtes Element oder eine Liste von Elementen.

Die oben aufgeführten Schritte erfolgen über die Azure Machine Learning-Empfehlungs-API. Sie können [auch im Katalog](http://1drv.ms/1xeO2F3) eine Beispielanwendung herunterladen, in der die einzelnen Schritte implementiert sind.

##Einschränkungen

* Die Maximale Anzahl von Modellen pro Abonnement beträgt 10.
* Die maximale Anzahl von Elementen, die ein Katalog aufnehmen kann, beträgt 100.000.
* Die maximale Menge der Nutzungspunkte, die aufbewahrt werden, beträgt etwa 5.000.000. Die ältesten werden gelöscht, wenn neue hochgeladen oder gemeldet werden.
* Die maximale Größe der Daten, die in POST gesendet werden können (z. B. Importieren von Katalog- oder Nutzungsdaten), beträgt 200 MB.
* Die Anzahl der Transaktionen pro Sekunde bei Empfehlungsmodellbuilds, die nicht aktiv sind, beträgt etwa 2 T/s. Bei aktiven Empfehlungsmodellbuilds sind bis zu 20 T/s möglich.

##Integration

###Authentifizierung
Microsoft Azure Marketplace unterstützt sowohl die Standard- als auch die OAuth-Authentifizierungsmethode. Sie können die Kontoschlüssel einfach finden, indem Sie im Marketplace unter Ihren [Kontoeinstellungen](https://datamarket.azure.com/account/keys) zu den Schlüsseln navigieren.
####Standardauthentifizierung
Fügen Sie den Autorisierungsheader hinzu:

	Authorization: Basic <creds>
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
	
Konvertieren zu Base64 (C#)

	var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
	var creds = Convert.ToBase64String(bytes);
	
Konvertieren zu Base64 (JavaScript)

	var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
	



###Dienst-URI
Den Dienststamm-URI für die APIs zu Azure Machine Learning-Empfehlungen finden Sie [hier](https://api.datamarket.azure.com/amla/recommendations/v2/).

Der vollständige Dienst-URI wird unter Verwendung von Elementen der OData-Spezifikation angegeben.

###API-Version
Jeder API-Aufruf enthält am Ende einen Abfrageparameter namens apiVersion. Dieser muss auf 1.0 festgelegt sein.

###Groß-/Kleinschreibung bei IDs
Bei IDs, die von den APIs zurückgegeben werden, muss die Groß-/Kleinschreibung beachtet werden, wenn sie in nachfolgenden API-Aufrufen als Parameter weitergegeben werden. Beispielsweise muss bei Modell- und Katalog-IDs die Groß-/Kleinschreibung beachtet werden.

###Modellerstellung
Erstellen einer Anforderung „Modell erstellen“:

| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelName |	Es sind nur Buchstaben (A-Z, a-z), Zahlen (0-9), Bindestriche (-) und Unterstriche (\_) zulässig.<br>Max. Länge: 20 |
|	apiVersion | 1.0 |
|||
| Anforderungstext | KEINE |


**Antwort**:

HTTP-Statuscode: 200

- `feed/entry/content/properties/id` - Enthält die Modell-ID.
**Hinweis**: Bei der Modell-ID muss die Groß-/Kleinschreibung beachtet werden.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>


###Katalogdaten importieren

Wenn Sie durch mehrere Aufrufe mehrere Katalogdateien zum gleichen Modell hochladen, werden nur die neuen Katalogelemente eingefügt. Vorhandene Elemente bleiben innerhalb der ursprünglichen Werte.

| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells (Groß-/Kleinschreibung muss beachtet werden).  |
| filename | Textbezeichner des Katalogs.<br>Es sind nur Buchstaben (A-Z, a-z), Zahlen (0-9), Bindestriche (-) und Unterstriche (\_) zulässig.<br>Max. Länge: 50 |
| apiVersion | 1.0 |
|||
| Anforderungstext | Katalogdaten. Format:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>Name</th><th>Obligatorisch</th><th>Typ</th><th>Beschreibung</th></tr><tr><td>Element-ID</td><td>Ja</td><td>Alphanumerisch, max. Länge: 50</td><td>Eindeutiger Bezeichner eines Elements</td></tr><tr><td>Elementname</td><td>Ja</td><td>Alphanumerisch, max. Länge: 255</td><td>Elementname</td></tr><tr><td>Elementkategorie</td><td>Ja</td><td>Alphanumerisch, max. Länge: 255</td><td>Kategorie, zu der das Element gehört (z. B. Kochbücher, Drama…)</td></tr><tr><td>Beschreibung</td><td>Nein</td><td>Alphanumerisch, max. Länge: 4000</td><td>Beschreibung dieses Elements</td></tr></table><br>Max. Dateigröße: 200 MB.<br><br>Beispiel:<br><pre>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Buch<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: Belletristik (Byzanz-Reihe),Buch<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Buch<br>552a1940-21e4-4399-82bb-594b46d7ed54,Die Herren der Zäune,Buch</pre> |


**Antwort**:

HTTP-Statuscode: 200

- `Feed\entry\content\properties\LineCount` - Dies ist die Anzahl der zulässigen Zeilen.
- `Feed\entry\content\properties\ErrorCount` - Dies ist die Anzahl der Zeilen, die aufgrund eines Fehlers nicht eingefügt wurden.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
  		<subtitle type="text">Import catalog file</subtitle>
  		<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T06:58:04Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
 	 </entry>
	</feed>


###Nutzungsdaten importieren

####Hochladen einer Datei
In diesem Abschnitt wird gezeigt, wie Nutzungsdaten mithilfe einer Datei hochgeladen werden können. Sie können diese API mit Nutzungsdaten mehrmals aufrufen. Alle Nutzungsdaten werden für alle Aufrufe gespeichert.

| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells (Groß-/Kleinschreibung muss beachtet werden). |
| filename | Textbezeichner des Katalogs.<br>Es sind nur Buchstaben (A-Z, a-z), Zahlen (0-9), Bindestriche (-) und Unterstriche (\_) zulässig.<br>Max. Länge: 50 |
| apiVersion | 1.0 |
|||
| Anforderungstext | Nutzungsdaten. Format:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Name</th><th>Obligatorisch</th><th>Typ</th><th>Beschreibung</th></tr><tr><td>Benutzer-ID</td><td>Ja</td><td>Alphanumerisch</td><td>Eindeutiger Bezeichner eines Benutzers</td></tr><tr><td>Element-ID</td><td>Ja</td><td>Alphanumerisch, max. Länge: 50</td><td> Eindeutiger Bezeichner eines Elements</td></tr><tr><td>Zeit</td><td>Nein</td><td>Datum im Format: YYYY/MM/DDTHH:MM:SS (z. B. 2013/06/20T10:00:00)</td><td>Zeitstempel der Daten</td></tr><tr><td>Ereignis</td><td>Nein, wenn bereitgestellt, muss auch ein Datum angegeben werden</td><td>Eins der Folgenden:<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>Max. Dateigröße: 200 MB.<br><br>Beispiel:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Antwort**:

HTTP-Statuscode: 200

- `Feed\entry\content\properties\LineCount` - Dies ist die Anzahl der zulässigen Zeilen.
- `Feed\entry\content\properties\ErrorCount` - Dies ist die Anzahl der Zeilen, die aufgrund eines Fehlers nicht eingefügt wurden.
- `Feed\entry\content\properties\FileId` - Dies ist der Dateibezeichner.


OData-XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####Verwenden der Datenerfassung
In diesem Abschnitt wird gezeigt, wie Ereignisse in Echtzeit an Azure Machine Learning-Empfehlungen gesendet werden (in der Regel von Ihrer Website).

| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
|||
|Anforderungstext| Ereignisdateneintrag für jedes Ereignis, das Sie senden möchten. Senden Sie für die gleiche Benutzer- oder Browsersitzung die gleiche ID im Feld „SessionId“. (Siehe Beispielereignistext unten.)|


- Beispiel für das Ereignis „Click“:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		<SessionId>11112222</SessionId>
		<EventData>
		<EventData>
		<Name>Click</Name>
		<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
		</EventData>
		</EventData>
		</Event>

- Beispiel für das Ereignis „RecommendationClick“:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RecommendationClick</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Beispiel für das Ereignis „AddShopCart“:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Beispiel für das Ereignis „RemoveShopCart“:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RemoveShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Beispiel für das Ereignis „Purchase“:
		 <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		 <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		 <SessionId>11112222</SessionId>
		 <EventData>
		 <EventData>
			 <Name>Purchase</Name>
			 <PurchaseItems>
			 <PurchaseItems>
				 <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
				 <Count>3</Count>
			 </PurchaseItems>
		 </PurchaseItems>
		 </EventData>
		 </EventData>
		 </Event>

- Beispiel für das Senden von 2 Ereignissen („Click“ und „AddShopCart“):

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>Click</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      	<ItemName>itemName</ItemName>
      	<ItemDescription>item description</ItemDescription>
      	<ItemCategory>category</ItemCategory>
    	</EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    	</EventData>
  		</EventData>
		</Event>

**Antwort**: HTTP-Statuscode: 200

###Erstellen eines Empfehlungsmodells

| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId |	Dies ist der eindeutige Bezeichner des Modells (Groß-/Kleinschreibung muss beachtet werden). |
| userDescription | Dies ist der Textbezeichner des Katalogs. Beachten Sie, dass Sie bei Verwendung von Leerzeichen diese stattdessen mit "%20" codieren müssen. Siehe Beispiel oben. <br>Max. Länge: 50 |
| apiVersion | 1,0 |
|||
| Anforderungstext | KEINER |

**Antwort**:

HTTP-Statuscode: 200

Dies ist eine asynchrone API. Sie erhalten eine Build-ID als Antwort. Um zu erfahren, wenn der Build beendet wurde, rufen Sie die API „Get Builds Status of a Model“ (Buildstatus eines Modells abrufen) auf und suchen in der Antwort nach dieser Build-ID. Beachten Sie, dass das Erstellen eines Builds je nach Größe der Daten Minuten bis Stunden dauern kann.

Bis der Build erstellt ist, können Sie keine Empfehlungen nutzen.

Gültige Buildstatus:

- Create (Erstellen): Das Modell wurde erstellt.
- Queued (In Warteschlange): Die Modellerstellung wurde ausgelöst und in die Warteschlange eingereiht.
- Building (Erstellen): Das Modell wird erstellt.
- Success (Erfolg): Der Build wurde erfolgreich abgeschlossen.
- Error (Fehler): Der Build wurde mit einem Fehler beendet.
- Cancelled: Die Builderstellung wurde abgebrochen.
- Cancelling: Die Builderstellung wird abgebrochen.


Hinweis: Sie finden die Build-ID unter folgendem Pfad: `Feed\entry\content\properties\Id`

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###Abrufen des Buildstatus eines Modells

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`:|



|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells (Groß-/Kleinschreibung muss beachtet werden). |
|	onlyLastBuild |	Hier wird angegeben, ob der gesamte Buildverlauf des Modells oder nur der Status des letzten Builds zurückgegeben werden soll. |
|	apiVersion |	1,0 |


**Antwort**:

HTTP-Statuscode: 200

Die Antwort enthält einen Eintrag pro Build. Jeder Eintrag enthält die folgenden Daten:

- `feed/entry/content/properties/UserName`: Name des Benutzers
- `feed/entry/content/properties/ModelName`: Name des Modells
- `feed/entry/content/properties/ModelId`: Eindeutiger Bezeichner des Modells
- `feed/entry/content/properties/IsDeployed`: Bereitstellungszustand des Builds (aktiver Build)
- `feed/entry/content/properties/BuildId`: Eindeutiger Bezeichner des Builds
- `feed/entry/content/properties/BuildType`: Typ des Builds
- `feed/entry/content/properties/Status`: Status des Builds. Es gibt folgende Status: Error, Building, Queued, Cancelling, Cancelled, Success
- `feed/entry/content/properties/StatusMessage`: Detaillierte Statusmeldung (gilt nur für bestimmte Status)
- `feed/entry/content/properties/Progress`: Fortschritt des Builds (%)
- `feed/entry/content/properties/StartTime`: Startzeit des Builds
- `feed/entry/content/properties/EndTime`: Endzeit des Builds
- `feed/entry/content/properties/ExecutionTime`: Dauer des Builds
- `feed/entry/content/properties/ProgressStep` – Details zur aktuellen Phase eines Builds, der gerade verarbeitet wird.

Gültige Buildstatus:
- Created: Der Buildanforderungseintrag wurde erstellt.
- Queued: Die Buildanforderung wurde ausgelöst und in die Warteschlange eingereiht.
- Building: Der Build wird verarbeitet.
- Success: Der Build wurde erfolgreich erstellt.
- Error: Der Build wurde mit einem Fehler beendet.
- Cancelled: Der Build wurde abgebrochen.
- Cancelling: Der Build wird abgebrochen.

Gültige Werte für Buildtyp:
- Rank: Rangfolgebuild. (Informationen zu Rangfolgebuilds finden Sie in der Dokumentation zu Machine Learning-Empfehlungs-APIs.)
- Recommendation: Empfehlungsbuild.
- FBT: FBT-Build (häufig zusammen gekaufte Elemente).

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get builds status of a model</subtitle>
	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T17:51:10Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###Abrufen von Empfehlungen

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`:|



|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dies ist der eindeutige Bezeichner des Modells (Groß-/Kleinschreibung muss beachtet werden). |
| itemIds | Durch Trennzeichen getrennte Liste der Elemente, für die Empfehlungen erstellt werden sollen.<br>Max. Länge: 1024 |
| numberOfResults | Dies ist die Anzahl der erforderlichen Ergebnisse. |
| includeMetatadata | Zur zukünftigen Verwendung, stets „false“ |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200

Die Antwort enthält einen Eintrag pro empfohlenem Element. Jeder Eintrag enthält die folgenden Daten:

- `Feed\entry\content\properties\Id` - ID des empfohlenen Elements.
- `Feed\entry\content\properties\Name` - Name des Elements.
- `Feed\entry\content\properties\Rating` - Bewertung der Empfehlung, höhere Werte bedeuten höhere Zuverlässigkeit.
- `Feed\entry\content\properties\Reasoning` - Empfehlungsargumentation (z. B. Erklärungen für die Empfehlung).

OData-XML

Die folgende Beispielantwort enthält 10 empfohlene Elemente:

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###Aktualisieren des Modells
Sie können die Modellbeschreibung oder die aktive Build-ID aktualisieren.
*Aktive Build-ID*: Jeder Build für jedes Modell weist eine „Build-ID“ auf. Die aktive Build-ID ist der erste erfolgreich erstellte Build jedes neuen Modells. Wenn Sie über eine aktive Build-ID verfügen und für das gleiche Modell zusätzliche Builds erstellen, müssen Sie die aktive Build-ID gegebenenfalls ausdrücklich als Standard-Build-ID festlegen. Wenn Sie Empfehlungen nutzen und die zu verwendende Build-ID nicht definieren, wird automatisch der Standardbuild verwendet.

Wenn ein Empfehlungsmodell in der Produktion verwendet wird, können mit diesem Mechanismus neue Modelle erstellt und getestet werden, bevor sie in der Produktion verwendet werden.

| HTTP-Methode | URI |
|:--------|:--------|
|PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`:|


|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| id | Dies ist der eindeutige Bezeichner des Modells (Groß-/Kleinschreibung muss beachtet werden). |
| apiVersion | 1\.0 |
|||
| Anforderungstext | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>Beachten Sie, dass die Beschreibung der XML-Tags sowie die ActiveBuildId optional sind. Wenn Sie keine Beschreibung oder keine aktive Build-ID festlegen möchten, entfernen Sie das gesamte Tag. |

**Antwort**:

HTTP-Statuscode: 200

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Update an Existing Model</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
 	 <updated>2014-10-05T10:27:17Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
	</feed>

##Rechtliche Hinweise
Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden.
Einige der in diesem Dokument dargestellten Beispiele dienen nur zu Illustrationszwecken und sind frei erfunden. Keine Ähnlichkeit oder Verbindung ist beabsichtigt und ist rein zufällig.
Dieses Dokument gibt keine Rechte an geistigem Eigentum an irgendeinem Microsoft-Produkt. Sie dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
© 2014 Microsoft. Alle Rechte vorbehalten.
 

<!----HONumber=AcomDC_1217_2015-->