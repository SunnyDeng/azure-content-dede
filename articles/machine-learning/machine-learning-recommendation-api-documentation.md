<properties 
	pageTitle="Machine Learning-Empfehlungs-APIs – Dokumentation | Microsoft Azure"
	description="Azure Machine Learning-Empfehlungs-APIs – Dokumentation für ein Empfehlungsmodul, das im Microsoft Azure Marketplace verfügbar ist."
	services="machine-learning"
	documentationCenter=""
	authors="AharonGumnik"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="LuisCa"/>

#Azure Machine Learning-Empfehlungs-APIs – Dokumentation

In diesem Dokument werden die Microsoft Azure Machine Learning-APIs für Empfehlungen erläutert.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1\. Allgemeine Übersicht
Dieses Dokument ist eine API-Referenz. Beginnen Sie mit dem Dokument „Azure Machine Learning Recommendation – Quick Start“ (Empfehlungen zu Azure Machine Learning – Schnellstart).

Die Azure Machine Learning-Empfehlungs-APIs können in 10 logische Gruppen unterteilt werden:

1.	<ins>Einfaches Modell</ins>: Dies sind APIs, mit denen Sie grundlegende Vorgänge für ein Modell ausführen können (z. B. Erstellen, Aktualisieren und Löschen eines Modells).
2.	<ins>Komplexes Modell</ins>: Dies sind APIs, mit denen Sie komplexere Einblicke in die Daten des Modells erhalten können.
3.	<ins>Modellgeschäftsregeln</ins>: Dies sind APIs, mit denen Sie Geschäftsregeln für die Empfehlungsergebnisse des Modells verwalten können.
4.	<ins>Katalog</ins>: Dies sind APIs, mit denen Sie grundlegende Vorgänge bei einem Modellkatalog ausführen können. Kataloge enthalten Metadateninformationen zu den Elementen der Nutzungsdaten.
5.	<ins>Feature</ins>: Dies sind APIs, mit denen Sie Details zu Katalogelementen einfügen und erfahren, wie Sie diese Informationen nutzen, um bessere Empfehlungen entwickeln zu können.
6.	<ins>Nutzungsdaten</ins>: Dies sind APIs, mit denen Sie grundlegende Vorgänge an den Modellnutzungsdaten ausführen können. Nutzungsdaten bestehen in der Grundform aus Zeilen mit Paaren von &#60;userId&#62;,&#60;itemId&#62;.
7.	<ins>Build</ins>: Dies sind APIs, mit denen Sie das Erstellen eines Modellbuilds auslösen und im Zusammenhang mit diesem Build grundlegende Vorgänge ausführen können. Sobald Sie sinnvolle Nutzungsdaten haben, können Sie die Erstellung eines Modells auslösen.
8.	<ins>Empfehlung</ins>: Dies sind APIs, mit denen Sie nach der Erstellung eines Modells Empfehlungen nutzen können.
9.	<ins>Benutzerdaten</ins>: Dies sind APIs, mit denen Sie Informationen zu den Nutzungsdaten der Benutzer abrufen können.
10.	<ins>Benachrichtigungen</ins>: Dies sind APIs, mit denen Sie Benachrichtigungen bei Problemen im Zusammenhang mit Ihren API-Vorgängen empfangen können. (Beispiel: Sie berichten Nutzungsdaten über die Datengewinnung, und bei der Verarbeitung von Ereignissen treten Fehler auf. Es wird eine Fehlerbenachrichtigung ausgegeben.)

##2\. Erweiterte Themen

###2\.1. Qualität der Empfehlungen

Das Erstellen eines Empfehlungsmodells ist in der Regel ausreichend, damit Empfehlungen vom System bereitgestellt werden können. Allerdings hängt die Qualität der Empfehlungen von der verarbeiteten Nutzung und der Abdeckung des Katalogs ab. Wenn beispielsweise viele „kalte“ Elemente (Elemente ohne nennenswerte Nutzung) vorhanden sind, ist es schwierig für das System, Empfehlungen für solche Elemente bereitzustellen oder sie als empfohlene Elemente zu verwenden. Um das Problem mit „kalten“ Elementen zu beheben, können die Metadaten der Elemente verwendet werden, um die Empfehlungen zu verbessern. Diese Metadaten werden als Funktionen bezeichnet. Typische Funktionen sind beispielsweise Buchautoren oder Filmschauspieler. Diese Funktionen werden im Katalog in Form von Schlüssel/Wert-Zeichenfolgen bereitgestellt. Die vollständige Format der Katalogdatei finden Sie im Abschnitt [Importieren von Katalogen](#81-import-catalog-data). Im folgenden Abschnitt wird die Verwendung von Features zur Verbesserung des Empfehlungsmodells erläutert.

###2\.2. Rangfolgebuild

Features können das Empfehlungsmodell verbessern, doch dazu ist die Verwendung von nützlichen Features erforderlich. Zu diesem Zweck wurde ein neuer Build zum Erstellen von Rangfolgen eingeführt Durch diesen Build wird die Nützlichkeit der Features in eine Rangfolge gebracht. Nützliche Features erhalten mindestens eine Rangwertung von 2. Wenn Sie wissen, welche Features nützlich sind, lösen Sie mit der Liste bzw. Unterliste nützlicher Features das Erstellen eines Empfehlungsbuilds aus. Diese Features können zum Verbessern sowohl „warmer“ als auch „kalter“ Elemente verwendet werden. Zum Verwenden der Features bei „warmen“ Elementen muss der Buildparameter `UseFeatureInModel` eingerichtet werden. Zum Verwenden der Features bei „kalten“ Elementen muss der Buildparameter `AllowColdItemPlacement` aktiviert werden. Hinweis: Es ist nicht möglich, `AllowColdItemPlacement` ohne `UseFeatureInModel` zu aktivieren.

###2\.3. Empfehlungsargumentation

Die Empfehlungsargumentation ist ein weiterer Nutzungsaspekt der Features. Das Empfehlungsmodul von Azure Machine Learning kann Features verwenden, um Erläuterungen („Argumentation“) zu den Empfehlungen bereitzustellen, um das Zutrauen desjenigen, der die Empfehlung verwendet, in das empfohlene Element zu erhöhen. Zum Aktivieren der Argumentation müssen die Parameter `AllowFeatureCorrelation` und `ReasoningFeatureList` eingerichtet werden, bevor ein Empfehlungsbuild angefordert wird.

##3\. Einschränkungen

- Die Maximale Anzahl von Modellen pro Abonnement beträgt 10.
- Die maximale Anzahl von Elementen, die ein Katalog aufnehmen kann, beträgt 100.000.
- Die maximale Menge der Nutzungspunkte, die aufbewahrt werden, beträgt etwa 5.000.000. Die ältesten werden gelöscht, wenn neue hochgeladen oder gemeldet werden.
- Die maximale Größe der Daten, die in POST gesendet werden können (z. B. Importieren von Katalog- oder Nutzungsdaten), beträgt 200 MB.
- Die Anzahl der Transaktionen pro Sekunde bei Empfehlungsmodellbuilds, die nicht aktiv sind, beträgt etwa 2 T/s. Bei aktiven Empfehlungsmodellbuilds sind bis zu 20 T/s möglich.

##4\. APIs – allgemeine Informationen

###4\.1. Authentifizierung
Befolgen Sie die Microsoft Azure Marketplace-Richtlinien in Bezug auf die Authentifizierung. Der Marketplace unterstützt sowohl die Standard- als auch die OAuth-Authentifizierungsmethode.

###4\.2. Dienst-URI
Den Dienststamm-URI für die APIs zu Azure Machine Learning-Empfehlungen finden Sie [hier](https://api.datamarket.azure.com/amla/recommendations/v3/).

Der vollständige Dienst-URI wird unter Verwendung von Elementen der OData-Spezifikation angegeben.

###4\.3. API-Version
Jeder API-Aufruf enthält am Ende einen Abfrageparameter namens apiVersion. Dieser muss auf 1.0 festgelegt sein.

###4\.4. Groß-/Kleinschreibung bei IDs
Bei IDs, die von den APIs zurückgegeben werden, muss die Groß-/Kleinschreibung beachtet werden, wenn sie in nachfolgenden API-Aufrufen als Parameter weitergegeben werden. Beispielsweise muss bei Modell- und Katalog-IDs die Groß-/Kleinschreibung beachtet werden.

##5\. Einfaches Modell

###5\.1. Modell erstellen
Es wird eine Anforderung „Modell erstellen“ erstellt.

| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelName |	Es sind nur Buchstaben (A-Z, a-z), Zahlen (0-9), Bindestriche (-) und Unterstriche (\_) zulässig.<br>Max. Länge: 20 | | apiVersion | 1.0 | ||| | Anforderungstext | NONE |


**Antwort**:

HTTP-Statuscode: 200

- `feed/entry/content/properties/id`: Enthält die Modell-ID. **Hinweis**: Bei der Modell-ID muss die Groß-/Kleinschreibung beachtet werden.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###5\.2. Modell abrufen
Hiermit wird eine Anforderung „Modell abrufen“ erstellt.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Beispiel:<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	id |	Dies ist der eindeutige Bezeichner des Modells (Groß-/Kleinschreibung muss beachtet werden). |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

Die Modelldaten finden Sie bei den folgenden Elementen:

- `feed/entry/content/properties/Id`: Dies ist die eindeutige ID des Modells.
- `feed/entry/content/properties/Name`: Dies ist der Name des Modells.
- `feed/entry/content/properties/Date`: Dies ist das Erstellungsdatum des Modells.
- `feed/entry/content/properties/Status`: Dies ist der Status des Modells. Einer der folgenden:
    - Created: Das Modell wird ohne Katalog und Nutzung erstellt.
	- ReadyForBuild: Das Modell wird mit Katalog und Nutzung erstellt.
- `feed/entry/content/properties/HasActiveBuild`: Hier wird angegeben, ob das Modell erfolgreich erstellt wurde.
- `feed/entry/content/properties/BuildId`: Dies ist die ID des aktiven Modellbuilds.
- `feed/entry/content/properties/Mpr`: Dies ist die mittlere Prozentrangwertung (MPR – weitere Informationen finden Sie unter ModelInsight) des Modells.
- `feed/entry/content/properties/UserName`: Dies ist der interne Benutzername des Modells.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Get A List Of All Models</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T14:35:51Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
		<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
	  </entry>
	</feed>

###5\.3. Alle Modelle abrufen
Hierdurch werden alle Modelle des aktuellen Benutzers abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>Beispiel:<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

- `feed/entry/content/properties/Id`: Dies ist die eindeutige ID des Modells.
- `feed/entry/content/properties/Name`: Dies ist der Name des Modells.
- `feed/entry/content/properties/Date`: Dies ist das Erstellungsdatum des Modells.
- `feed/entry/content/properties/Status`: Dies ist der Status des Modells. Einer der folgenden:
  - Created: Das Modell wird ohne Katalog und Nutzung erstellt.
  - ReadyForBuild: Das Modell wird mit Katalog und Nutzung erstellt.
- `feed/entry/content/properties/HasActiveBuild`: Hier wird angegeben, ob das Modell erfolgreich erstellt wurde.
- `feed/entry/content/properties/BuildId`: Dies ist die ID des aktiven Modellbuilds.
- `feed/entry/content/properties/Mpr`: Dies ist die MPR des Modells (weitere Informationen finden Sie unter ModelInsight).
- `feed/entry/content/properties/UserName`: Dies ist der interne Benutzername des Modells.
- `feed/entry/content/properties/UsageFileNames`: Dies ist eine kommagetrennte Liste von Modellnutzungsdateien.
- `feed/entry/content/properties/CatalogId`: Dies ist die ID des Modellkatalogs.
- `feed/entry/content/properties/Description`: Dies ist die Modellbeschreibung.
- `feed/entry/content/properties/CatalogFileName`: Dies ist der Dateiname des Modellkatalogs.

OData-XML


    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get A List Of All Models</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-28T14:35:51Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
					<d:Name m:type="Edm.String">vah-11111</d:Name>
					<d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
					<d:Status m:type="Edm.String">ReadyForBuild</d:Status>
					<d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
					<d:BuildId m:type="Edm.String">-1</d:BuildId>
					<d:Mpr m:type="Edm.String">0</d:Mpr>
					<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
					<d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
					<d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
					<d:Description m:type="Edm.String">short description</d:Description>
					<d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
				</m:properties>
			</content>
		</entry>
	</feed>

###5\.4. Modell aktualisieren

Sie können die Modellbeschreibung oder die aktive Build-ID aktualisieren.<br> <ins>Aktive Build-ID</ins>: Jeder Build für jedes Modell weist eine „Build-ID“ auf. Die aktive Build-ID ist der erste erfolgreich erstellte Build jedes neuen Modells. Wenn Sie über eine aktive Build-ID verfügen und für das gleiche Modell zusätzliche Builds erstellen, müssen Sie die aktive Build-ID gegebenenfalls ausdrücklich als Standard-Build-ID festlegen. Wenn Sie Empfehlungen nutzen und die zu verwendende Build-ID nicht angeben, wird automatisch der Standard-Build-ID verwendet.<br> Wenn ein Empfehlungsmodell in der Produktion verwendet wird, können mit diesem Mechanismus neue Modelle erstellt und getestet werden, bevor sie in der Produktion verwendet werden.


| HTTP-Methode | URI |
|:--------|:--------|
|PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>Beispiel:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	id | Dies ist der eindeutige Bezeichner des Modells (Groß-/Kleinschreibung muss beachtet werden). |
|	apiVersion | 1\.0 |
||| | Request Body | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>Beachten Sie, dass die Beschreibung der XML-Tags sowie die ActiveBuildId optional sind. Wenn Sie keine Beschreibung oder keine ActiveBuildId festlegen möchten, entfernen Sie das gesamte Tag.|

**Antwort**:

HTTP-Statuscode: 200

###5\.5. Modell löschen
Löscht ein vorhandenes Modell nach ID.

| HTTP-Methode | URI |
|:--------|:--------|
|LÖSCHEN |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Beispiel:<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	id |	Dies ist der eindeutige Bezeichner des Modells (Groß-/Kleinschreibung muss beachtet werden). |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Delete Model by Id</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T10:39:33Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
		<d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
	  </entry>
	</feed>

##6\. Komplexes Modell

###6\.1. Modelldateneinblicke
Diese API gibt statistische Daten zu den Nutzungsdaten zurück, mit denen dieses Modell erstellt wurde.

Nur für den Empfehlungsbuild verfügbar.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Beispiel:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

Die Daten werden als Sammlung von Eigenschaften zurückgegeben.

- `feed/entry/id/content/properties/key`: Enthält den Eigenschaftsnamen.
- `feed/entry/id/content/properties/value`: Enthält den Eigenschaftswert.

In der folgenden Tabelle werden die Werte dargestellt, die von den Schlüsseln repräsentiert werden.

|Schlüssel|Beschreibung|
|:-----|:----|
| AvgItemLength | Dies ist die durchschnittliche Anzahl der unterschiedlichen Benutzer pro Element. |
| AvgUserLength | Dies ist die durchschnittliche Anzahl der unterschiedlichen Elemente pro Benutzer. |
| DensificationNumberOfItems | Dies ist die Anzahl der Elemente nach dem Bereinigen der nicht modellierbaren Elemente. |
| DensificationNumberOfUsers | Dies ist die Anzahl der Nutzungspunkte nach dem Bereinigen der nicht modellierbaren Benutzer und Elemente. |
| DensificationNumberOfRecords | Dies ist die Anzahl der Nutzungspunkte nach dem Bereinigen der nicht modellierbaren Benutzer und Elemente. |
| MaxItemLength | Dies ist die Anzahl unterschiedlicher Benutzer des beliebtesten Elements. |
| MaxUserLength | Dies ist die maximale Anzahl der unterschiedlichen Elemente für einen Benutzer. |
| MinItemLength | Dies ist die maximale Anzahl der unterschiedlichen Benutzer für ein Element. |
| MinUserLength | Dies ist die minimale Anzahl der unterschiedlichen Elemente für einen Benutzer. |
| RawNumberOfItems | Dies ist die Anzahl der Elemente vor dem Bereinigen der nicht modellierbaren Elemente. |
| RawNumberOfUsers | Dies ist die Anzahl der Nutzungspunkte vor allen Bereinigungen. |
| RawNumberOfRecords | Dies ist die Anzahl der Nutzungspunkte vor allen Bereinigungen. |
| SamplingNumberOfItems | N/V |
| SamplingNumberOfRecords | N/V |
| SamplingNumberOfUsers | N/V |

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get data insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
	</entry>
	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
	<content type="application/xml">
		<m:properties>
			<d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
			<d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxItemLength</d:Key>
				<d:Value m:type="Edm.String">4,704</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    	<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxUserLength</d:Key>
				<d:Value m:type="Edm.String">190</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinItemLength</d:Key>
				<d:Value m:type="Edm.String">8</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinUserLength</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    </feed>

###6\.2. Modelleinblicke
Hierdurch wird ein Modelleinblick in den aktiven bzw. einen bestimmten Build zurückgegeben.

Nur für den Empfehlungsbuild verfügbar.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |Mit aktiver Build-ID:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Beispiel:<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>Mit bestimmter Build-ID:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
|	buildId |	Optional. Mit dieser Nummer wird ein erfolgreicher Build identifiziert. |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

Die Daten werden als Sammlung von Eigenschaften zurückgegeben.

- `feed/entry/id/content/properties/key`
- `feed/entry/id/content/properties/value`


In der folgenden Tabelle werden die Werte dargestellt, die von den Schlüsseln repräsentiert werden.

| Schlüssel | Beschreibung |
|:---- |:----|
| CatalogCoverage | Hier wird angegeben, welcher Teil des Katalogs mit Nutzungsmustern modelliert werden kann. Für die übrigen Elemente werden inhaltsbasierte Features benötigt. |
| Mpr | Dies ist die mittlere Prozentrangwertung des Modells. Niedrige Werte sind besser als höhere. |
| NumberOfDimensions | Dies ist die Anzahl der Dimensionen, die vom Faktorisierungsalgorithmus der Matrix verwendet wird. |


OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get model insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">CatalogCoverage</d:Key>
				<d:Value m:type="Edm.String">1.000</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">Mpr</d:Key>
				<d:Value m:type="Edm.String">0.367</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###6\.3. Modellprobe abrufen
Hierdurch wird eine Probe des Empfehlungsmodells abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Beispiel:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>Mit bestimmter Build-ID:<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>Beispiel:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

OData-XML

Die Antwort wird in reinem Textformat zurückgegeben:

<pre>
Ebene&#160;1
---------------
655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel
	655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel; Bewertung: 0,5215
	3f471802-f84f-44a0-99c8-6d2e7418eec1, Black Hawk Down: A Story of Modern War; Bewertung: 0,5151
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon; Bewertung: 0,5148
	6afc18e4-8c2a-43d1-9021-57543d6b11d8, Imajica; Bewertung: 0,5146
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List; Bewertung: 0,514
56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai
	56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai; Bewertung: 0,5218
	53156702-cc0c-443d-b718-6fb74b2491d3, Son of \; Bewertung: 0,5212
	fb8cf7a6-8719-46ee-97d4-92f931d77a3a, Smoke and Mirrors: Short Fictions and Illusions; Bewertung: 0,5188
	8f5fe006-79e4-4679-816b-950989d1db4b, A Place I've Never Been (zeitgenössische amerikanische Belletristik); Bewertung: 0,5156
	d8db4583-cc0f-49ce-bc95-b7fa3491623f, Happiness: A Novel; Bewertung: 0,5156
50471eec-9aeb-4900-84d7-21567ab18546, If the Buddha Dated: A Handbook for Finding Love on a Spiritual Path
	cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Divine Secrets of the Ya-Ya Sisterhood: A Novel; Bewertung: 0,5266
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel; Bewertung: 0,5252
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven; Bewertung: 0,5244
	e2cbf7ad-0636-4117-8b30-298da6df7077, Animal Dreams; Bewertung: 0,5227
	6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Confessions of an Ugly Stepsister: A Novel; Bewertung: 0,5222
5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club)
	5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club); Bewertung: 0,537
	5dcbac37-2946-4f2a-a0b3-bbe710f9409a, Up Island: A Novel; Bewertung: 0,5277
	bc5b69db-733b-4346-adde-3927544258f7, Downtown; Bewertung: 0,5275
	31fe5c63-3e5a-48d0-802b-d3b0f989a634, Have a Nice Day: A Tale of Blood and Sweatsocks; Bewertung: 0,5252
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea; Bewertung: 0,5238
68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived
	68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived; Bewertung: 0,5379
	6724862e-e4e7-4022-9614-1468d8b902ff, Little House on the Prairie; Bewertung: 0,5345
	cdedb837-1620-496d-94c4-6ccfed888320, Little House in the Big Woods; Bewertung: 0,5325
	382164ba-406b-4187-b726-d7a54b9d790d, The Tao of Pooh; Bewertung: 0,5309
	6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5, On the Banks of Plum Creek; Bewertung: 0,5285
37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships
	37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars, Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships; Bewertung: 0,5397
	f2be16d4-5faf-4d32-ab83-7ba74d29261e, Politically Correct Bedtime Stories: Modern Tales for Our Life and Times; Bewertung: 0,5207
	ef732c5c-334b-4d6b-ab82-7255eb7286d0, Honor Among Thieves; Bewertung: 0,5195
	0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, The Giving Tree; Bewertung: 0,5194
	883b360f-8b42-407f-b977-2f44ad840877, Scary Stories to Tell in the Dark: Collected from American Folklore (Spukgeschichten); Bewertung: 0,5184
ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball
	d008dae9-c73a-40a1-9a9b-96d5cf546f36, The Gulag Archipelago 1918-1956: An Experiment in Literary Investigation I-II; Bewertung: 0,5416
	ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball; Bewertung: 0,5403
	49dec30e-0adb-411a-b186-48eaabf6f8bc, Fatherland; Bewertung: 0,5394
	cc7964fd-d30f-478e-a425-93ddbdf094ed, Magic the Gathering: Arena Vol. 1; Bewertung: 0,5379
	8a1e9f36-97af-4614-bed9-24e3940a05f3, More Sniglets: Any Word That Doesn't Appear in the Dictionary but Should; Bewertung: 0,5377
12a6d988-be21-4a09-8143-9d5f4261ba16, A Dream of Eagles
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon; Bewertung: 0,5417
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List; Bewertung: 0,5416
	1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, The Family; Bewertung: 0,5371
	56daeffe-7d48-43cd-8ef8-7dffd0c103d3, Kilo Class; Bewertung: 0,5366
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender; Bewertung: 0,5366
df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish
	56d33036-dfda-46b9-8e2a-76cb03921bb0, The X-Files: Ground Zero; Bewertung: 0,5417
	0780cde8-6529-4e1d-b6c6-082c1b80e596, Twelve Red Herrings; Bewertung: 0,5416
	df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish; Bewertung: 0,5408
	400fe331-2c35-490c-adbc-b28b4b73d56c, Shall We Tell the President? Bewertung: 0,5383
	f86ad7d0-5c03-42b3-aebf-13d44aec8b30, Shades of Grace; Bewertung: 0,5358
de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology
	de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology; Bewertung: 0,5422
	b303538f-e2c6-4a2c-b425-8d21e684fc3e, My Uncle Oswald; Bewertung: 0,5385
	34b84627-48af-4a4c-96c4-b26fb3863f56, Midnight In the Garden of Good and Evil; Bewertung: 0,5379
	306cbaa7-b1a8-4142-9d55-e11b5018a7a8, The Street Lawyer; Bewertung: 0,5376
	e53b4baa-8c09-45c4-95c0-b6a26b98770b, Miss Smillas Feeling for Snow; Bewertung: 0,5367

Ebene&#160;2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman, Tony)
	352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman, Tony); Bewertung: 0,5425
	74c49398-bc10-4af5-a658-a996a1201254, Children of the Storm (Peters, Elizabeth); Bewertung: 0,5387
	9ba80080-196e-43fd-8025-391d963f77e7, The Floating Girl; Bewertung: 0,5372
	e68f81d5-7745-4cc7-b943-fedb8fcc2ced, Killer Smile (Scottoline, Lisa); Bewertung: 0,5353
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender; Bewertung: 0,5332
c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea; Bewertung: 0,5433
	c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days; Bewertung: 0,543
	a00ae6ad-4a7f-4211-9836-75ce8834eb11, Sniglets (Snig'lit: Any Word That Doesn't Appear in the Dictionary But Should); Bewertung: 0,5327
	6f6e192e-0d64-49ca-9b63-f09413ea1ee6, Politically Correct Holiday Stories: For an Enlightened Yuletide Season; Bewertung: 0,5307
	798051a8-147d-4d46-b0dc-e836325029e6, AGE OF INNOCENCE (MOVIE TIE-IN); Bewertung: 0,5301
73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics)
	cba8163f-6536-436b-8130-47b4a43c827f, Trust No One (The Official Guide to the X-Files Vol. 2); Bewertung: 0,5434
	5708e4cb-2492-49c0-94a8-cc413eec5d89, Small Gods (Scheibenwelt-Romane (Taschenbuch)); Bewertung: 0,5406
	73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics); Bewertung: 0,5403
	d885b0bd-ae4b-452d-bdf2-faa90197dbc9, The Color of Magic; Bewertung: 0,539
	b133a9c4-4784-4db3-b100-d0d6dffb94d2, The Truth Is Out There (The Official Guide to the X-Files Vol. 1); Bewertung: 0,5367
271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings
	271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings; Bewertung: 0,5445
	2de1c354-90ff-47c5-a0db-1bad7d88ef94, The Salaryman's Wife (Children of Violence Series); Bewertung: 0,5329
	d279416e-19c0-43f8-9ec9-a585947879ca, Zen Attitude; Bewertung: 0,5316
	c8f854d7-3de3-4b23-8217-f4f851670fd4, Revenge of the Cootie Girls: A Robin Hudson Mystery (Robin Hudson Mysteries (Taschenbuch)); Bewertung: 0,5305
	8ef4751c-7074-409e-a3ac-d49b222fc864, Where the Wild Things Are; Bewertung: 0,5289
9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God
	9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God; Bewertung: 0,5446
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees; Bewertung: 0,5389
	65ecbdd1-131c-40c3-a3d6-d86ca281377a, The God of Small Things; Bewertung: 0,5387
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries; Bewertung: 0,5355
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven; Bewertung: 0,5344
5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie-Plum-Romane (Taschenbuch))
	5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie-Plum-Romane (Taschenbuch)); Bewertung: 0,5446
	57169b2b-9a8a-486b-9aac-1ed98ce57168, Final Appeal; Bewertung: 0,5332
	efcb1bc4-7278-4a8f-b491-befde02070d6, Moment of Truth; Bewertung: 0,5329
	1efa91a2-993b-4c43-9f5c-3454fc12612d, Burn Factor; Bewertung: 0,5309
	24c59962-458a-4ec8-b95d-d694e861919c, At Home in Mitford (The Mitford Years); Bewertung: 0,5303
4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl
	4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl; Bewertung: 0,5449
	cd5f2c03-20cb-43be-a1fb-3b4233e63222, Pigs in Heaven; Bewertung: 0,5329
	19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Love in the Time of Cholera (Penguin Great Books of the 20th Century); Bewertung: 0,5267
	15689d09-c711-4844-84d8-130a90237b26, Bel Canto; Bewertung: 0,5245
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel; Bewertung: 0,5235
98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories
	f874b5a3-5d40-4436-94ff-0fa1c090ddf5, The Sun Also Rises (Scribner-Klassiker); Bewertung: 0,5451
	98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories; Bewertung: 0,5442
	0ce0014a-9a48-4013-a08a-7f2c11877930, H.M.S. Unseen; Bewertung: 0,5421
	15316ca6-1e38-425f-893d-691944a47000, More Scary Stories To Tell In The Dark; Bewertung: 0,5409
	329d5682-3dc3-4206-8aa2-eef4b1032258, Letters from the Earth; Bewertung: 0,54
5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover))
	5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)); Bewertung: 0,5462
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel; Bewertung: 0,5372
	604eb3bd-6026-4f51-bffd-9fb54f180400, Family Pictures: A Novel; Bewertung: 0,5341
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Taschenbuch)); Bewertung: 0,5334
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees; Bewertung: 0,5319
d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven
	d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven; Bewertung: 0,5491
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel; Bewertung: 0,5401
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries; Bewertung: 0,5393
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Taschenbuch)); Bewertung: 0,5382
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven; Bewertung: 0,5367

</pre>

##7\. Modellgeschäftsregeln
Es gibt 4 Typen von Regeln: <strong>BlockList</strong>: Mit dieser Funktion können Sie eine Sperrliste mit Elementen erstellen, die nicht in den Empfehlungsergebnissen zurückgegeben werden sollen. <strong>Upsale</strong>: Hiermit können Sie das Zurückgeben von Elementen in den Empfehlungsergebnissen erzwingen. <strong>WhiteList</strong>: Mit dieser Funktion können Sie eine Positivliste mit den einzigen Elementen erstellen, die in den Empfehlungsergebnissen zurückgegeben werden sollen (Gegenfunktion zur Sperrliste). <strong>PerSeedBlockList</strong>: Mit dieser Funktion können Sie eine Sperrliste pro Startwert erstellen, um für jedes Element eine Liste von Elementen festzulegen, die nicht in den Empfehlungsergebnissen zurückgegeben werden sollen.


###7\.1. Modellregeln abrufen

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Beispiel:<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

- `feed/entry/content/properties/Id`: Dies ist der eindeutige Bezeichner dieser Regel.
- `feed/entry/content/properties/Type`: Dies ist der Typ der Regel.
- `feed/entry/content/properties/Parameter`: Dies ist der Parameter der Regel.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get a list of rules for a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T12:58:57Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000043</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000044</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.2. Regel hinzufügen

| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/AddRule?apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
||| | Request Body | <ins>Zum Hinzufügen der BlockList-Regel:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins>Zum Hinzufügen der Upsale-Regel:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"]}</Value></ApiFilter>`<br><br><ins>Zum Hinzufügen der WhiteList-Regel:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins>Zum Hinzufügen der PerSeedBlockList-Regel:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|

**Antwort**:

HTTP-Statuscode: 200

Von der API wird die neu erstellte Regel mit Details zurückgegeben. Die Eigenschaft „Rules“ kann aus den folgenden Pfaden abgerufen werden:

- `feed/entry/content/properties/Id`: Dies ist der eindeutige Bezeichner dieser Regel.
- `feed/entry/content/properties/Type`: Dies ist der Typ der Regel („BlockList“ oder „Upsale“).
- `feed/entry/content/properties/Parameter`: Dies ist der Parameter der Regel.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Add A Rule</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T11:13:28Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AddARuleEntity</title>
		<updated>2014-11-05T11:13:28Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000041</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.3. Regel löschen

| HTTP-Methode | URI |
|:--------|:--------|
|LÖSCHEN |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>Beispiel:<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
|	filterId |	Dies ist der eindeutige Bezeichner des Filters. |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

###7\.4. Alle Regeln löschen

| HTTP-Methode | URI |
|:--------|:--------|
|LÖSCHEN |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Beispiel:<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

##8\. Katalog

###8\.1. Importieren von Katalogdaten

Wenn Sie durch mehrere Aufrufe mehrere Katalogdateien zum gleichen Modell hochladen, werden nur die neuen Katalogelemente eingefügt. Vorhandene Elemente bleiben innerhalb der ursprünglichen Werte. Mit dieser Methode können Sie keine Katalogdaten aktualisieren.

Die Katalogdaten müssen das folgende Format aufweisen:

- Ohne Features: `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Mit Features: `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

Hinweis: Die maximale Dateigröße beträgt 200 MB.

** Formatdetails **

| Name | Erforderlich | Typ | Beschreibung |
|:---|:---|:---|:---|
| Element-ID |Ja | [A-z], [a-z], [0-9], [\_] &#40;Unterstrich&#41;, [-] &#40;Bindestrich&#41;<br> Max. Länge: 50 | Eindeutiger Bezeichner eines Elements. | | Elementname | Ja | Alle alphanumerischen Zeichen<br> Max. Länge: 255 | Elementname. | | Elementkategorie | Ja | Alle alphanumerischen Zeichen <br> Max. Länge: 255 | Kategorie, zu der dieses Element gehört (z. B. Kochbücher, Drama…). Darf leer sein. | | Beschreibung | Nein, es sei denn, es sind Features vorhanden (darf jedoch leer sein) | Alle alphanumerischen Zeichen <br> Max. Länge: 4000 | Dies ist die Beschreibung des Elements. | | Featureliste | Nein | Alle alphanumerischen Zeichen <br> Max. Länge: 4000 | Dies ist eine kommagetrennte Liste im Format „Featurename=Featurewert“ zum Verbessern der Modellempfehlung; siehe Abschnitt [Erweiterte Themen](#2-advanced-topics). |


| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
| filename | Textbezeichner des Katalogs.<br>Es sind nur Buchstaben (A-Z, a-z), Zahlen (0-9), Bindestriche (-) und Unterstrich (\_) zulässig.<br>Max. Länge: 50 | | apiVersion | 1.0 | ||| | Anforderungstext | Beispiel (mit Features):<br/>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book,the book description,author=Richard Wright,publisher=Harper Flamingo Canada,year=2001<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book,,author=Nick Bantock,publisher=Harpercollins,year=1997<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book,,author=Timothy Findley, publisher=HarperFlamingo Canada, year=2001<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book,the book description,author=Magnus Mills, publisher=Arcade Publishing, year=1998</pre> |


**Antwort**:

HTTP-Statuscode: 200

Von der API wird ein Importbericht zurückgegeben. - `feed\entry\content\properties\LineCount`: Dies ist die Anzahl der zulässigen Zeilen. - `feed\entry\content\properties\ErrorCount`: Dies ist die Anzahl der Zeilen, die aufgrund eines Fehlers nicht eingefügt wurden.

OData-XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Import catalog file</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-05T06:58:04Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
	<entry>
   	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ImportCatalogFileEntity2</title>
		<updated>2014-10-05T06:58:04Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:LineCount m:type="Edm.String">4</d:LineCount>
				<d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.2. Katalog abrufen
Hierdurch werden alle Katalogelemente abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

Die Antwort enthält einen Eintrag pro Katalogelement. Jeder Eintrag enthält die folgenden Daten:

- `feed/entry/content/properties/ExternalId`: Dies ist die externe Katalogelement-ID, die vom Kunden bereitgestellt wird.
- `feed/entry/content/properties/InternalId`: Dies ist die interne Katalogelement-ID, die von den Azure Machine Learning-Empfehlungen generiert wurde.
- `feed/entry/content/properties/Name`: Dies ist der Name des Katalogelements.
- `feed/entry/content/properties/Category`: Dies ist die Kategorie des Katalogelements.
- `feed/entry/content/properties/Description`: Dies ist die Beschreibung des Katalogelements.
- `feed/entry/content/properties/Metadata`: Dies sind die Metadaten des Katalogelements.


OData-XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get All Catalog Items</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
		<entry>
        	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
				<d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
				<d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
				<d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
				<d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
				<d:Name m:type="Edm.String">Spadework</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
				<d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
				<d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.3. Katalogelemente anhand von Token abrufen

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
|	token |	Dies ist das Token des Katalogelementnamens. Muss mindestens 3 Zeichen enthalten. |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

Die Antwort enthält einen Eintrag pro Katalogelement. Jeder Eintrag enthält die folgenden Daten:

- `feed/entry/content/properties/ExternalId`: Dies ist die externe Katalogelement-ID, die vom Kunden bereitgestellt wird.
- `feed/entry/content/properties/InternalId`: Dies ist die interne Katalogelement-ID, die von den Azure Machine Learning-Empfehlungen generiert wurde.
- `feed/entry/content/properties/Name`: Dies ist der Name des Katalogelements.
- `feed/entry/content/properties/Category`: Dies ist die Kategorie des Katalogelements.
- `feed/entry/content/properties/Description`: Dies ist die Beschreibung des Katalogelements.
- `feed/entry/content/properties/Metadata`: Dies sind die Metadaten des Katalogelements.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get Catalog items that contain a token</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:48:19Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">CatalogItemsThatContainATokenEntity</title>
			<updated>2014-10-29T11:48:19Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    		<content type="application/xml">
      			<m:properties>
					<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
        			<d:Name m:type="Edm.String">Clara Callan</d:Name>
        			<d:Rating m:type="Edm.Double">0</d:Rating>
        			<d:Reasoning m:type="Edm.String"></d:Reasoning>
        			<d:Metadata m:type="Edm.String"></d:Metadata>
        			<d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
      			</m:properties>
			</content>
		</entry>
	</feed>

##9\. Nutzungsdaten
###9\.1. Importieren von Nutzungsdaten
####9\.1.1. Hochladen einer Datei
In diesem Abschnitt wird gezeigt, wie Nutzungsdaten mithilfe einer Datei hochgeladen werden können. Sie können diese API mit Nutzungsdaten mehrmals aufrufen. Alle Nutzungsdaten werden für alle Aufrufe gespeichert.

| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
| filename | Textbezeichner des Katalogs.<br>Es sind nur Buchstaben (A-Z, a-z), Zahlen (0-9), Bindestriche (-) und Unterstriche (_) zulässig.<br>Max. Länge: 50 | | apiVersion | 1.0 | ||| | Anforderungstext | Nutzungsdaten. Format:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Name</th><th>Obligatorisch</th><th>Typ</th><th>Beschreibung</th></tr><tr><td>Benutzer-ID</td><td>Ja</td><td>[A-z], [a-z], [0-9], [_] &#40;Unterstrich&#41;, [-] &#40;Bindestrich&#41;<br> Max. Länge: 255 </td><td>Eindeutiger Bezeichner eines Benutzers.</td></tr><tr><td>Element-ID</td><td>Ja</td><td>[A-z], [a-z], [0-9], [&#95;] &#40;Unterstrich&#41;, [-] &#40;Bindestrich&#41;<br> Max. Länge: 50</td><td>Eindeutiger Bezeichner eines Elements.</td></tr><tr><td>Zeit</td><td>Nein</td><td>Datum im Format: JJJJ/MM/TTTHH:MM:SS (z. B. 2013/06/20T10:00:00)</td><td>Zeitstempel der Daten.</td></tr><tr><td>Ereignis</td><td>Nein; wenn bereitgestellt, muss auch ein Datum angegeben werden</td><td>Eins der Folgenden:<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>Max. Dateigröße: 200MB<br><br>Beispiel:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Antwort**:

HTTP-Statuscode: 200

- `Feed\entry\content\properties\LineCount`: Dies ist die Anzahl der zulässigen Zeilen.
- `Feed\entry\content\properties\ErrorCount`: Dies ist die Anzahl der Zeilen, die aufgrund eines Fehlers nicht eingefügt wurden.
- `Feed\entry\content\properties\FileId`: Dies ist der Dateibezeichner.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####9\.1.2. Verwenden der Datenerfassung
In diesem Abschnitt wird gezeigt, wie Ereignisse in Echtzeit an Azure Machine Learning-Empfehlungen gesendet werden (in der Regel von Ihrer Website).

| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	apiVersion | 1,0 |
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

- Beispiel für das Ereignis „Purchase“: <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"> <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId> <SessionId>11112222</SessionId> <EventData> <EventData> <Name>Purchase</Name> <PurchaseItems> <PurchaseItems> <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId> <Count>3</Count> </PurchaseItems> </PurchaseItems> </EventData> </EventData> </Event>

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

###9\.2. Modellnutzungsdateien auflisten
Hierdurch werden von allen Modellnutzungsdateien Metadaten abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	forModelId |	Dies ist der eindeutige Bezeichner des Modells. |
|	apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

Die Antwort enthält einen Eintrag pro Nutzungsdatei. Jeder Eintrag enthält die folgenden Daten:

- `feed\entry\content\properties\Id`: Dies ist die ID der Nutzungsdatei.
- `feed\entry\content\properties\Length`: Dies ist die Größe der Nutzungsdatei in MB.
- `feed\entry\content\properties\DateModified`: Dies ist das Erstellungsdatum der Nutzungsdatei.
- `feed\entry\content\properties\UseInModel`: Hier wird angegeben, ob die Nutzungsdatei im Modell verwendet wird.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of model's usage files info</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
			<updated>2014-10-30T09:40:25Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
					<d:Length m:type="Edm.Double">0</d:Length>
					<d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
					<d:UseInModel m:type="Edm.String">true</d:UseInModel>
				</m:properties>
			</content>
		</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
				<d:Length m:type="Edm.Double">0.001</d:Length>
				<d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
				<d:UseInModel m:type="Edm.String">true</d:UseInModel>
          	</m:properties>
		</content>
	</entry>
</feed>

###9\.3. Nutzungsstatistiken abrufen
Hierdurch werden Nutzungsstatistiken abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId |	Dies ist der eindeutige Bezeichner des Modells. |
| startDate |	Dies ist das Startdatum. Format: yyyy/MM/ddTHH:mm:ss |
| endDate |	Dies ist das Enddatum. Format: yyyy/MM/ddTHH:mm:ss |
| eventTypes |	Dies ist eine kommagetrennte Zeichenfolge aus Ereignistypen oder null, um alle Ereignisse abzurufen. |
| apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

Dies ist eine Sammlung von Schlüssel/Wert-Elementen. Jede Sammlung enthält die Ereignissumme für einen bestimmten Ereignistyp nach Stunden gruppiert.

- `feed\entry[i]\content\properties\Key`: Enthält die Zeit (gruppiert nach Stunden) und den Ereignistyp.
- `feed\entry[i]\content\properties\Value`: Dies ist die Gesamtzahl der Ereignisse.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-18T11:39:16Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
				<d:Value m:type="Edm.String">5</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
          	</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
            	<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
        </content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###9\.4. Nutzungsdateiprobe abrufen
Hierdurch werden die ersten 2 KB des Nutzungsdateiinhalts abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId |	Dies ist der eindeutige Bezeichner des Modells. |
| fileId |	Dies ist der eindeutige Bezeichner der Modellnutzungsdatei. |
| apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

Die Antwort wird in reinem Textformat zurückgegeben: <pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 </pre>


###9\.5. Modellnutzungsdatei abrufen
Hierdurch wird der vollständige Inhalt der Nutzungsdatei abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| mId |	Dies ist der eindeutige Bezeichner des Modells. |
| fid |	Dies ist der eindeutige Bezeichner der Modellnutzungsdatei. |
| Download | 1 |
| apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

Die Antwort wird in reinem Textformat zurückgegeben: <pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260965,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 102758,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 112602,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 163925,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 262998,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 144717,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 </pre>

###9\.6. Nutzungsdatei löschen
Hierdurch wird die angegebene Modellnutzungsdatei gelöscht.

| HTTP-Methode | URI |
|:--------|:--------|
|LÖSCHEN |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Beispiel:<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId |	Dies ist der eindeutige Bezeichner des Modells. |
| fileId | Dies ist der eindeutige Bezeichner der zu löschenden Datei. |
| apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200


###9\.7. Alle Nutzungsdateien löschen
Hierdurch werden alle Modellnutzungsdateien gelöscht.

| HTTP-Methode | URI |
|:--------|:--------|
|LÖSCHEN |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Beispiel:<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId |	Dies ist der eindeutige Bezeichner des Modells. |
| apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

##10\. Merkmale
In diesem Abschnitt wird gezeigt, wie Featureinformationen abgerufen werden, z. B. die importierten Features, ihre Werte und ihr Rang, und wann dieser Rang zugeordnet wurde. Features werden als Teil der Katalogdaten importiert, und wenn ein Rangfolgebuild abgeschlossen ist, wird ihnen ein Rang zugeordnet. Der Rang der Features kann sich je nach Nutzungsdatenmuster und Elementtyp ändern. Bei konsistenter Nutzung und konsistenten Elementen dürfte der Rang jedoch nur geringfügigen Schwankungen unterliegen. Der Rang eines Features ist eine nicht-negative Zahl. Die Zahl 0 bedeutet, dass das Feature noch nicht eingestuft wurde (dies ist der Fall, wenn diese API vor dem Abschluss des ersten Rangfolgebuilds aufgerufen wurde). Das Datum, an dem der Rang zugeordnet wurde, wird als Aktualität der Bewertung bezeichnet.

###10\.1. Featureinformationen abrufen (für den letzten Rangfolgebuild)
Hierdurch werden Featureinformationen einschließlich Rang für den letzten erfolgreichen Rangfolgebuild abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27`:

| Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId |	Dies ist der eindeutige Bezeichner des Modells. |
|samplingSize| Dies ist die Anzahl der Werte, die für jedes Feature anhand der Daten im Katalog eingeschlossen werden sollen. <br/>Mögliche Werte sind:<br> -1: Alle Proben <br>0: Keine Proben <br>N - N Proben für jeden Featurenamen zurückgeben|
| apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |


**Antwort**:

HTTP-Statuscode: 200

Die Antwort enthält eine Liste der Informationseinträge der Features. Jeder Eintrag enthält folgende Elemente:

- `feed/entry/content/m:properties/d:Name`: Name des Features.
- `feed/entry/content/m:properties/d:RankUpdateDate`: Datum, an dem der Rang dem Feature zugeordnet wurde (Aktualität der Bewertung). Ein historisches Datum („0001-01-01T00:00:00“) bedeutet, dass kein Rangfolgebuild ausgeführt wurde.
- `feed/entry/content/m:properties/d:Rank`: Rang des Features (Gleitkommazahl). Bei einem Rang ab 2.0 gilt das Feature als gut.
- `feed/entry/content/m:properties/d:SampleValues`: Kommagetrennte Liste von Werten bis zur angeforderten Probengröße.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get the features of a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-01-08T13:15:02Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Author</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Publisher</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
		<contenttype="application/xml">
		<m:properties>
			<d:Name m:type="Edm.String">Year</d:Name>
			<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
			<d:Rank m:type="Edm.String">0</d:Rank>
			<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
		</m:properties>
		</content>
	</entry>
</feed>


###10\.2. Featureinformationen abrufen (für einen bestimmten Rangfolgebuild)

Hierdurch werden Featureinformationen einschließlich Rang für einen bestimmten Rangfolgebuild abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=<rankBuildId>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=1000551&apiVersion=%271.0%27`:

| Parametername |	Gültige Werte |
|:--------			|:--------			|
| modelId |	Dies ist der eindeutige Bezeichner des Modells. |
|samplingSize| Dies ist die Anzahl der Werte, die für jedes Feature anhand der Daten im Katalog eingeschlossen werden sollen.<br/> Mögliche Werte sind:<br> -1: Alle Proben <br>0: Keine Proben <br>N - N Proben für jeden Featurenamen zurückgeben|
|rankBuildId| Dies ist der eindeutige Bezeichner des Rangfolgebuilds oder -1 für den letzten Rangfolgebuild.|
| apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |


**Antwort**:

HTTP-Statuscode: 200

Die Antwort enthält eine Liste der Informationseinträge der Features. Jeder Eintrag enthält folgende Elemente:

- `feed/entry/content/m:properties/d:Name`: Name des Features.
- `feed/entry/content/m:properties/d:RankUpdateDate`: Datum, an dem der Rang dem Feature zugeordnet wurde (Aktualität der Bewertung). Ein historisches Datum („0001-01-01T00:00:00“) bedeutet, dass kein Rangfolgebuild ausgeführt wurde.
- `feed/entry/content/m:properties/d:Rank`: Rang des Features (Gleitkommazahl). Bei einem Rang ab 2.0 gilt das Feature als gut.
- `feed/entry/content/m:properties/d:SampleValues`: Kommagetrennte Liste von Werten bis zur angeforderten Probengröße.

OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get the features of a model</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:54:22Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Author</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">3.38867426</d:Rank>
					<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Publisher</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.67839336</d:Rank>
					<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Year</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.12352145</d:Rank>
					<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
				</m:properties>
			</content>
		</entry>
	</feed>


##11\. Entwickeln

  In diesem Abschnitt werden die verschiedenen APIs im Zusammenhang mit Builds erläutert. Es gibt 3 Arten von Builds: Empfehlungsbuilds, Rangfolgebuilds und FBT-Builds (Frequently Bought Together, häufig zusammen gekauft).

Empfehlungsbuilds dienen dazu, Empfehlungsmodelle für Vorhersagen zu erstellen. Für Vorhersagen (für diese Art Build) gibt es zwei Varianten: * I2I – auch bekannt als "Item to Item"-Empfehlungen – diese Option sagt anhand eines Elements oder einer Elementliste andere Elemente voraus, die wahrscheinlich von hohem Interesse sind. * U2I – auch bekannt als "User to Item"-Empfehlungen – diese Option sagt anhand einer Benutzer-ID (und optional einer Liste von Elementen) andere Elemente voraus, die wahrscheinlich für den Benutzer (sowie ggf. seine sonstige Elementauswahl) von hohem Interesse sind. U2I-Empfehlungen basieren auf dem Verlauf derjenigen Elemente, für die der Benutzer sich bis zu dem Zeitpunkt interessiert hat, zu dem das Modell entwickelt wurde.

Rangfolgebuilds sind technische Builds, welche die Nützlichkeit Ihrer Features vermitteln können. Um bei Empfehlungsmodellen mit Features die besten Ergebnisse zu erzielen, sollten Sie die folgenden Schritte ausführen: - Lösen Sie einen Rangfolgebuild aus (sofern die Bewertung Ihrer Features nicht stabil ist), und warten Sie, bis Sie die Featurebewertung erhalten. - Rufen Sie den Rang Ihrer Features durch einen Aufruf an die API [Get Features Info](#101-get-features-info-for-last-rank-build) ab. - Konfigurieren Sie einen Empfehlungsbuild mit den folgenden Parametern: - `useFeatureInModel`: Auf „True“ festlegen. - `ModelingFeatureList`: Auf eine kommagetrennte Liste mit einer Bewertung von mindestens 2.0 festlegen (je nach den im vorigen Schritt abgerufenen Rängen). - `AllowColdItemPlacement`: Auf „True“ festlegen. - Optional können Sie `EnableFeatureCorrelation` auf „True“ und `ReasoningFeatureList` auf die Liste der Features festlegen, die Sie bei Erläuterungen verwenden möchten (meist ist das die Featureliste, die beim Modellieren verwendet wird, oder eine Unterliste). - Lösen Sie den Empfehlungsbuild mit den konfigurierten Parametern aus.

Hinweis: Wenn Sie keine Parameter konfigurieren (z. B. den Empfehlungsbuild ohne Parameter aufrufen) oder die Featureverwendung nicht ausdrücklich deaktivieren (indem Sie z. B. `UseFeatureInModel` auf „False“ festlegen), werden die featurebezogenen Parameter mit den oben beschrieben Werten eingerichtet, sofern ein Rangfolgebuild vorhanden ist.

Rangfolgebuilds und Empfehlungsbuilds können ohne Einschränkungen zeitgleich für das gleiche Modell ausgeführt werden. Zwei Builds des gleichen Typs können jedoch nicht zeitgleich für das gleiche Modell ausgeführt werden.

FBT-Builds („Frequently bought together“, häufig zusammen gekauft) ist ein weiterer Empfehlungsalgorithmus, der manchmal auch als „konservativ“ bezeichnet wird. Er ist für nicht homogene Kataloge geeignet (homogen: Bücher, Filme, einige Lebensmittel, Mode; nicht homogen: Computer und Geräte, domänenübergreifend, sehr heterogen).

Hinweis: Wenn die von Ihnen hochgeladenen Nutzungsdateien das optionale Feld "Event Type" enthalten, werden zur FBT-Modellierung nur Purchase-Ereignisse verwendet. Wenn keine Ereignistyp angegeben ist, werden alle Ereignisse als "Purchase" betrachtet.


####11\.1 Buildparameter

Jeder Buildtyp kann über einen Satz von Parametern (s. unten) konfiguriert werden. Wenn Sie keine Parameter konfigurieren, werden den Parametern automatisch Werte zugewiesen. Hierzu werden die Informationen verwendet, die zum Zeitpunkt der Buildauslösung vorhanden waren.

#####11\.1.1. Nutzungskondensator
Benutzer oder Elemente mit wenigen Nutzungspunkten enthalten möglicherweise mehr Noise als Informationen. Es wird versucht, die Mindestzahl von Nutzungspunkten pro Benutzer/Element vorherzusagen, die in einem Modell verwendet werden sollten. Diese Anzahl liegt innerhalb des Bereichs, der von den Parametern „ItemCutoffLowerBound“ bei Elementen bzw. „ItemCutoffUpperBound“ „UserCutOffLowerBound“ und „UserCutoffUpperBound“ bei Benutzern festgelegt wird. Der Kondensatoreffekt auf Elemente oder Benutzer kann minimiert werden, indem mindestens eine der entsprechenden Grenzen auf null festgelegt wird.

#####11\.1.2. Parameter für Rangfolgebuilds

In der folgenden Tabelle sind die Parameter für Rangfolgebuilds aufgeführt.

|Schlüssel|Beschreibung|Typ|Gültiger Wert|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Die Anzahl der Iterationen, die vom Modell ausgeführt werden, beeinflusst die Gesamtrechenzeit sowie die Modellgenauigkeit. Je höher die Anzahl, desto höher die Genauigkeit, aber auch die Rechenzeit.| Ganze Zahl | 10-50 |
| NumberOfModelDimensions | Die Anzahl der Dimensionen bezieht sich auf die Anzahl der „Features“, die vom Modell in Ihren Daten gesucht werden. Wenn Sie die Anzahl der Dimensionen erhöhen, können Sie die Ergebnisse in kleineren Clustern feiner abstimmen. Durch zu viele Dimensionen wird jedoch verhindert, dass Korrelationen zwischen den Elementen gefunden werden. | Ganze Zahl | 10-40 |
|ItemCutOffLowerBound| Hierdurch wird die Elementuntergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
|ItemCutOffUpperBound| Hierdurch wird die Elementobergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
|UserCutOffLowerBound| Hierdurch wird die Benutzeruntergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
|UserCutOffUpperBound| Hierdurch wird die Benutzerobergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |

#####11\.1.3. Parameter für Empfehlungsbuilds
In der folgenden Tabelle sind die Parameter für Empfehlungsbuilds aufgeführt.

|Schlüssel|Beschreibung|Typ|Gültiger Wert|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Die Anzahl der Iterationen, die vom Modell ausgeführt werden, beeinflusst die Gesamtrechenzeit sowie die Modellgenauigkeit. Je höher die Anzahl, desto höher die Genauigkeit, aber auch die Rechenzeit.| Ganze Zahl | 10-50 |
| NumberOfModelDimensions | Die Anzahl der Dimensionen bezieht sich auf die Anzahl der „Features“, die vom Modell in Ihren Daten gesucht werden. Wenn Sie die Anzahl der Dimensionen erhöhen, können Sie die Ergebnisse in kleineren Clustern feiner abstimmen. Durch zu viele Dimensionen wird jedoch verhindert, dass Korrelationen zwischen den Elementen gefunden werden. | Ganze Zahl | 10-40 |
|ItemCutOffLowerBound| Hierdurch wird die Elementuntergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
|ItemCutOffUpperBound| Hierdurch wird die Elementobergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
|UserCutOffLowerBound| Hierdurch wird die Benutzeruntergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
|UserCutOffUpperBound| Hierdurch wird die Benutzerobergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
| Beschreibung | Dies ist eine Beschreibung des Builds. | String | Beliebiger Text, maximal 512 Zeichen |
| EnableModelingInsights | Hiermit können Sie Metriken zum Empfehlungsmodell verarbeiten. | Boolean | True/False |
| UseFeaturesInModel | Hier wird angegeben, ob Features verwendet werden können, um das Empfehlungsmodell zu verbessern. | Boolean | True/False |
| ModelingFeatureList | Dies ist die kommagetrennte Liste von Komponentennamen, die im Empfehlungsbuild verwendet werden soll, um die Empfehlung zu verbessern. | String | Featurenamen, bis zu 512 Zeichen |
| AllowColdItemPlacement | Hier wird angegeben, ob durch die Empfehlung ein „Push“ kalter Elemente anhand der Ähnlichkeit der Features erfolgen soll. | Boolean | True/False |
| EnableFeatureCorrelation | Hier wird angegeben, ob Features bei der Argumentation verwendet werden können. | Boolean | True/False |
| ReasoningFeatureList | Dies ist eine kommagetrennte Liste von Featurenamen, die für Argumentationssätze (z. B. Erklärungen von Empfehlungen) verwendet werden. | String | Featurenamen, bis zu 512 Zeichen |
| EnableU2I | Ermöglicht personalisierte Empfehlungen, auch bekannt als U2I ("User to Item"-Empfehlungen). | Boolean | True/False (Standardwert: true) |

#####11\.1.4. Parameter für FBT-Builds
In der folgenden Tabelle sind die Parameter für Empfehlungsbuilds aufgeführt.

|Schlüssel|Beschreibung|Typ|Gültiger Wert (Standard)|
|:-----|:----|:----|:---|
|FbtSupportThreshold | Dies ist ein Maß dafür, wie konservativ das Modell ist. Dies ein Maß für die Grauwerte von Elementen, die bei der Modellierung berücksichtigt werden.| Ganze Zahl | 3-50 (6) |
|FbtMaxItemSetSize | Begrenzt die Anzahl der Elemente in einem häufigen Satz.| Ganze Zahl | 2-3 (2) |
|FbtMinimalScore | Dies ist die Mindestbewertung, die ein häufiger Satz haben muss, um in die zurückgegebenen Ergebnisse eingeschlossen zu werden. Höhere Bewertungen sind besser als niedrigere.| Doppelt | 0 und höher (0) |

###11\.2. Auslösen eines Empfehlungsbuilds

  Standardmäßig wird durch diese API ein Empfehlungsmodellbuild ausgelöst. Zum Auslösen eines Rangfolgebuilds (zum Bewerten von Features) muss die Build-API-Variante mit dem Parameter „Build Type“ verwendet werden.


| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId |	Dies ist der eindeutige Bezeichner des Modells. |
| userDescription | Dies ist der Textbezeichner des Katalogs. Beachten Sie, dass Sie bei Verwendung von Leerzeichen diese stattdessen mit "%20" codieren müssen. Siehe Beispiel oben. <br>Max. Länge: 50 |
| apiVersion | 1\.0 |
||| | Request Body | Wird das Feld leer gelassen, so wird der Build mit den Standardbuildparametern ausgeführt.<br><br>Wenn Sie die Parameter festlegen möchten, senden Sie sie wie im folgenden Beispiel als XML in den Textkörper. (Eine Erläuterung der Parameter finden Sie im Abschnitt „Buildparameter“.)`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**Antwort**:

HTTP-Statuscode: 200

Dies ist eine asynchrone API. Sie erhalten eine Build-ID als Antwort. Um zu erfahren, wenn der Build beendet wurde, rufen Sie die API „Get Builds Status of a Model“ (Buildstatus eines Modells abrufen) auf und suchen in der Antwort nach dieser Build-ID. Beachten Sie, dass das Erstellen eines Builds je nach Größe der Daten Minuten bis Stunden dauern kann.

Bis der Build erstellt ist, können Sie keine Empfehlungen nutzen.

Gültige Buildstatus:

- Create: Buildanforderung wurde erstellt.
- Queued: Buildanforderung wurde gesendet und in die Warteschlange eingereiht.
- Building: Builderstellung wird durchgeführt.
- Success (Erfolg): Der Build wurde erfolgreich abgeschlossen.
- Error (Fehler): Der Build wurde mit einem Fehler beendet.
- Cancelled (Abgebrochen): Der Vorgang wurde abgebrochen.
- Cancelling: Für den Build wurde eine Abbruchanforderung gesendet.


Hinweis: Sie finden die Build-ID unter folgendem Pfad: `Feed\entry\content\properties\Id`

OData-XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###11\.3. Auslösen eines Builds (Empfehlung, Rang oder FBT)

| HTTP-Methode | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId |	Dies ist der eindeutige Bezeichner des Modells. |
| userDescription | Dies ist der Textbezeichner des Katalogs. Beachten Sie, dass Sie bei Verwendung von Leerzeichen diese stattdessen mit "%20" codieren müssen. Siehe Beispiel oben. <br>Max. Länge: 50 |
| buildType | Typ des aufzurufenden Builds: <br/>: „Recommendation“ für Empfehlungsbuild <br>: „Ranking“ für Rangplatzbuild <br/>: „Fbt“ für FBT-Build
| apiVersion | 1\.0 |
||| | Anforderungstext | Wird das Feld leer gelassen, wird der Build mit den Standardbuildparametern ausgeführt.<br><br>Wenn Sie Buildparameter festlegen möchten, senden Sie diese wie im folgenden Beispiel als XML in den Textkörper. (Eine Erläuterung und eine vollständige Liste der Parameter finden Sie im Abschnitt „Buildparameter“).`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

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
- Cancelled (Abgebrochen): Der Vorgang wurde abgebrochen.
- Cancelling (Abbrechen): Der Vorgang wird abgebrochen.

Hinweis: Sie finden die Build-ID unter folgendem Pfad: `Feed\entry\content\properties\Id`

OData-XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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




###11\.4. Buildstatus eines Modells abrufen
Hierdurch werden Builds und deren Status für ein angegebenes Modell abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`:|


|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	modelId |	Dies ist der eindeutige Bezeichner des Modells. |
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
- `feed/entry/content/properties/Status`: Status des Builds. Es gibt folgende Status: Error (Fehler), Building (Erstellen), Queued (In Warteschlange), Cancelling (Abbrechen), Cancelled (Abgebrochen), Success (Erfolg)
- `feed/entry/content/properties/StatusMessage`: Detaillierte Statusmeldung (gilt nur für bestimmte Status)
- `feed/entry/content/properties/Progress`: Fortschritt des Builds (%)
- `feed/entry/content/properties/StartTime`: Startzeit des Builds
- `feed/entry/content/properties/EndTime`: Endzeit des Builds
- `feed/entry/content/properties/ExecutionTime`: Dauer des Builds
- `feed/entry/content/properties/ProgressStep`: Details zur aktuellen Phase eines Buildfortschritts.

Gültige Buildstatus: - Created (Erstellt): Die Builderstellungsanforderung wurde erstellt. - Queued (In Warteschlange): Die Buildanforderung wurde ausgelöst und in die Warteschlange eingereiht. - Building (Erstellen): Der Build wird bearbeitet. - Success (Erfolg): Der Build wurde erfolgreich erstellt. - Error (Fehler): Der Build wurde mit einem Fehler beendet. - Cancelled (Abgebrochen): Der Build wurde abgebrochen. - Cancelling (Abbrechen): Der Build wird abgebrochen.

Gültige Werte für den Buildtyp: - Rank: Rangfolgebuild - Recommendation: Empfehlungsbuild


OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a model</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T17:51:10Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
		<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T17:51:10Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###11\.5. Abrufen von Buildstatus
Hierdurch werden die Buildstatus aller Modelle eines Benutzers abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`:|


|	Parametername |	Gültige Werte |
|:--------			|:--------								|
|	onlyLastBuild |	Hier wird angegeben, ob der gesamte Buildverlauf des Modells oder nur der Status des letzten Builds zurückgegeben werden soll. |
|	apiVersion |	1,0 |


**Antwort**:

HTTP-Statuscode: 200

Die Antwort enthält einen Eintrag pro Build. Jeder Eintrag enthält die folgenden Daten:

- `feed/entry/content/properties/UserName`: Name des Benutzers
- `feed/entry/content/properties/ModelName`: Name des Modells
- `feed/entry/content/properties/ModelId`: Eindeutiger Bezeichner des Modells
- `feed/entry/content/properties/IsDeployed`: Bereitstellungszustand des Builds
- `feed/entry/content/properties/BuildId`: Eindeutiger Bezeichner des Builds
- `feed/entry/content/properties/BuildType`: Typ des Builds
- `feed/entry/content/properties/Status`: Status des Builds. Es gibt folgende Status: Error (Fehler), Building (Erstellen), Queued (In Warteschlange), Cancelled (Abgebrochen), Cancelling (Abbrechen), Success (Erfolg)
- `feed/entry/content/properties/StatusMessage`: Detaillierte Statusmeldung (gilt nur für bestimmte Status)
- `feed/entry/content/properties/Progress`: Fortschritt des Builds (%)
- `feed/entry/content/properties/StartTime`: Startzeit des Builds
- `feed/entry/content/properties/EndTime`: Endzeit des Builds
- `feed/entry/content/properties/ExecutionTime`: Dauer des Builds
- `feed/entry/content/properties/ProgressStep`: Details zur aktuellen Phase eines Buildfortschritts.

Gültige Buildstatus: - Created (Erstellt): Die Builderstellungsanforderung wurde erstellt. - Queued (In Warteschlange): Die Buildanforderung wurde ausgelöst und in die Warteschlange eingereiht. - Building (Erstellen): Der Build wird bearbeitet. - Success (Erfolg): Der Build wurde erfolgreich erstellt. - Error (Fehler): Der Build wurde mit einem Fehler beendet. - Cancelled (Abgebrochen): Der Build wurde abgebrochen. - Cancelling (Abbrechen): Der Build wird abgebrochen.


Gültige Werte für den Buildtyp: - Rank: Rangfolgebuild - Recommendation: Empfehlungsbuild


OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T18:41:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T18:41:21Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###11\.6. Build löschen
Löscht einen Build.

HINWEIS: <br>Aktive Builds können nicht gelöscht werden. Das Modell muss auf einen anderen aktiven Build aktualisiert werden, bevor Sie es löschen.<br>In Bearbeitung befindliche Builds können nicht gelöscht werden. Brechen Sie den Build zunächst durch den Aufruf <strong>Cancel Build</strong> (Build abbrechen) ab.

| HTTP-Methode | URI |
|:--------|:--------|
|LÖSCHEN |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Beispiel:<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| buildId | Dies ist der eindeutige Bezeichner des Builds. |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200

###11\.7. Build abbrechen
Hierdurch wird ein Build mit dem Status „Building“ (Erstellen) abgebrochen.

| HTTP-Methode | URI |
|:--------|:--------|
|PUT |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| buildId | Dies ist der eindeutige Bezeichner des Builds. |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200

###11\.8. Buildparameter abrufen
Hierdurch werden Buildparameter abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| buildId | Dies ist der eindeutige Bezeichner des Builds. |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200

Von dieser API wird eine Sammlung von Schlüssel/Wert-Elementen zurückgegeben. Jedes Element steht für einen Parameter und dessen Wert: - `feed/entry/content/properties/Key`: Name des Buildparameters. - `feed/entry/content/properties/Value`: Wert des Buildparameters.

In der folgenden Tabelle werden die Werte dargestellt, die von den Schlüsseln repräsentiert werden.

|Schlüssel|Beschreibung|Typ|Gültiger Wert|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Die Anzahl der Iterationen, die vom Modell ausgeführt werden, beeinflusst die Gesamtrechenzeit sowie die Modellgenauigkeit. Je höher die Anzahl, desto höher die Genauigkeit, aber auch die Rechenzeit.| Ganze Zahl | 10-50 |
| NumberOfModelDimensions | Die Anzahl der Dimensionen bezieht sich auf die Anzahl der „Features“, die vom Modell in Ihren Daten gesucht werden. Wenn Sie die Anzahl der Dimensionen erhöhen, können Sie die Ergebnisse in kleineren Clustern feiner abstimmen. Durch zu viele Dimensionen wird jedoch verhindert, dass Korrelationen zwischen den Elementen gefunden werden. | Ganze Zahl | 10-40 |
|ItemCutOffLowerBound| Hierdurch wird die Elementuntergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
|ItemCutOffUpperBound| Hierdurch wird die Elementobergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
|UserCutOffLowerBound| Hierdurch wird die Benutzeruntergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
|UserCutOffUpperBound| Hierdurch wird die Benutzerobergrenze des Kondensators definiert. Siehe Abschnitt „Nutzungskondensator“ oben. | Ganze Zahl | Mindestens 2 (durch 0 wird der Kondensator deaktiviert) |
| Beschreibung | Dies ist eine Beschreibung des Builds. | String | Beliebiger Text, maximal 512 Zeichen |
| EnableModelingInsights | Hiermit können Sie Metriken zum Empfehlungsmodell verarbeiten. | Boolean | True/False |
| UseFeaturesInModel | Hier wird angegeben, ob Features verwendet werden können, um das Empfehlungsmodell zu verbessern. | Boolean | True/False |
| ModelingFeatureList | Dies ist die kommagetrennte Liste von Komponentennamen, die im Empfehlungsbuild verwendet werden soll, um die Empfehlung zu verbessern. | String | Featurenamen, bis zu 512 Zeichen |
| AllowColdItemPlacement | Hier wird angegeben, ob durch die Empfehlung ein „Push“ kalter Elemente anhand der Ähnlichkeit der Features erfolgen soll. | Boolean | True/False |
| EnableFeatureCorrelation | Hier wird angegeben, ob Features bei der Argumentation verwendet werden können. | Boolean | True/False |
| ReasoningFeatureList | Dies ist eine kommagetrennte Liste von Featurenamen, die für Argumentationssätze (z. B. Erklärungen von Empfehlungen) verwendet werden. | String | Featurenamen, bis zu 512 Zeichen |


OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get build parameters</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:50:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
			<titletype="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">Description</d:Key>
					<d:Value m:type="Edm.String">rankBuild</d:Value>
				</m:properties>
			</content>
		</entry>
	</feed>

##12\. Empfehlungen
###12\.1. Abrufen von Elementempfehlungen (für aktiven Build)

Hierdurch werden Empfehlungen des aktiven Builds vom Typ "Recommendation" (Empfehlung) oder "FBT" (Frequently Bought Together, häufig zusammen gekauft) basierend auf einer Liste von Seedelementen (Eingabeelementen) abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dies ist der eindeutige Bezeichner des Modells. |
| itemIds | Dies ist eine kommagetrennte Liste der Elemente, für die Empfehlungen erstellt werden sollen. <br>Wenn der aktive Build den Typ „FBT“ aufweist, können Sie nur ein Element senden. <br>Max. Länge: 1024 |
| numberOfResults | Dies ist die Anzahl der erforderlichen Ergebnisse. |
| includeMetatadata | Zur zukünftigen Verwendung, stets „false“ |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200


Die Antwort enthält einen Eintrag pro empfohlenem Element. Jeder Eintrag enthält folgende Daten: - `Feed\entry\content\properties\Id`: ID des empfohlenen Elements. - `Feed\entry\content\properties\Name`: Name des Elements. - `Feed\entry\content\properties\Rating`: Bewertung der Empfehlung (höhere Werte bedeuten höheres Zutrauen). - `Feed\entry\content\properties\Reasoning`: Empfehlungsargumentation (z. B. Erklärungen für die Empfehlung).

Die folgende Beispielantwort enthält 10 empfohlene Elemente.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
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

###12\.2. Abrufen von Elementempfehlungen (eines bestimmten Builds)

Hierdurch werden Empfehlungen eines bestimmten Builds vom Typ „Recommendation“ (Empfehlung) oder „Fbt“ abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dies ist der eindeutige Bezeichner des Modells. |
| itemIds | Dies ist eine kommagetrennte Liste der Elemente, für die Empfehlungen erstellt werden sollen. <br>Wenn der aktive Build den Typ „FBT“ aufweist, können Sie nur ein Element senden. <br>Max. Länge: 1024 |
| numberOfResults | Dies ist die Anzahl der erforderlichen Ergebnisse. |
| includeMetatadata | Zur zukünftigen Verwendung, stets „false“
| buildId | Dies ist die Build-ID, die für diese Empfehlungsanforderung verwendet werden soll. |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200


Die Antwort enthält einen Eintrag pro empfohlenem Element. Jeder Eintrag enthält folgende Daten: - `Feed\entry\content\properties\Id`: ID des empfohlenen Elements. - `Feed\entry\content\properties\Name`: Name des Elements. - `Feed\entry\content\properties\Rating`: Bewertung der Empfehlung (höhere Werte bedeuten höheres Zutrauen). - `Feed\entry\content\properties\Reasoning`: Empfehlungsargumentation (z. B. Erklärungen für die Empfehlung).

Eine Beispielantwort finden Sie unter 12.1.

###12\.3. Abrufen von FBT-Empfehlungen (für aktiven Build)

Hierdurch werden Empfehlungen des aktiven Builds vom Typ "FBT" basierend auf einer Liste von Seedelementen (Eingabeelementen) abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dies ist der eindeutige Bezeichner des Modells. |
| itemId | Element, für das die Empfehlung gilt. <br>Max. Länge: 1024 |
| numberOfResults | Dies ist die Anzahl der erforderlichen Ergebnisse. |
| minimalScore | Die Mindestbewertung, die ein häufiger Satz haben muss, um in die zurückgegebenen Ergebnisse eingeschlossen zu werden. |
| includeMetatadata | Zur zukünftigen Verwendung, stets „false“ |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200


Die Antwort umfasst einen Eintrag pro empfohlenem Elementsatz (ein Satz Elemente, die üblicherweise zusammen mit dem Seed- bzw. Eingabeelement gekauft werden). Jeder Eintrag enthält folgende Daten: – `Feed\entry\content\properties\Id1`: ID des empfohlenen Elements – `Feed\entry\content\properties\Name1`: Name des Elements – `Feed\entry\content\properties\Id2`: ID des zweiten empfohlenen Elements (optional) – `Feed\entry\content\properties\Name2`: Name des zweiten Elements (optional) – `Feed\entry\content\properties\Rating`: Bewertung der Empfehlung (höhere Werte bedeuten höhere Zuverlässigkeit) – `Feed\entry\content\properties\Reasoning`: Empfehlungsargumentation (z. B. Erklärungen für die Empfehlung)

Die folgende Beispielantwort enthält 3 empfohlene Elementsätze.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
		<d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###12\.4. Abrufen von FBT-Empfehlungen (eines bestimmten Builds)

Hierdurch werden Empfehlungen eines bestimmten Builds vom Typ "FBT" abgerufen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dies ist der eindeutige Bezeichner des Modells. |
| itemId | Element, für das die Empfehlung gilt. <br>Max. Länge: 1024 |
| numberOfResults | Dies ist die Anzahl der erforderlichen Ergebnisse. |
| minimalScore | Die Mindestbewertung, die ein häufiger Satz haben muss, um in die zurückgegebenen Ergebnisse eingeschlossen zu werden. |
| includeMetatadata | Zur zukünftigen Verwendung, stets „false“ |
| buildId | Dies ist die Build-ID, die für diese Empfehlungsanforderung verwendet werden soll. |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200


Die Antwort umfasst einen Eintrag pro empfohlenem Elementsatz (ein Satz Elemente, die üblicherweise zusammen mit dem Seed- bzw. Eingabeelement gekauft werden). Jeder Eintrag enthält folgende Daten: – `Feed\entry\content\properties\Id1`: ID des empfohlenen Elements – `Feed\entry\content\properties\Name1`: Name des Elements – `Feed\entry\content\properties\Id2`: ID des zweiten empfohlenen Elements (optional) – `Feed\entry\content\properties\Name2`: Name des zweiten Elements (optional) – `Feed\entry\content\properties\Rating`: Bewertung der Empfehlung (höhere Werte bedeuten höhere Zuverlässigkeit) – `Feed\entry\content\properties\Reasoning`: Empfehlungsargumentation (z. B. Erklärungen für die Empfehlung)

Eine Beispielantwort finden Sie unter 12.3.

###12\.5. Abrufen von Benutzerempfehlungen (für aktiven Build)

Hierdurch werden Benutzerempfehlungen eines Builds vom Typ "Recommendation" abgerufen, der als aktiver Build gekennzeichnet ist.

Die API gibt basierend auf dem Nutzungsverlauf des Benutzers eine Liste der vorhergesagten Elemente zurück.

Hinweise: 1. Für einen FBT-Build gibt es keine Benutzerempfehlungen. 2. Wenn der aktive Build ein FBT-Build ist, gibt diese Methode einen Fehler zurück.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dies ist der eindeutige Bezeichner des Modells. |
| bei der ersten | Dies ist der eindeutige Bezeichner des Benutzers. |
| numberOfResults | Dies ist die Anzahl der erforderlichen Ergebnisse. |
| includeMetatadata | Zur zukünftigen Verwendung, stets „false“ |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200


Die Antwort enthält einen Eintrag pro empfohlenem Element. Jeder Eintrag enthält folgende Daten: - `Feed\entry\content\properties\Id`: ID des empfohlenen Elements. - `Feed\entry\content\properties\Name`: Name des Elements. - `Feed\entry\content\properties\Rating`: Bewertung der Empfehlung (höhere Werte bedeuten höheres Zutrauen). - `Feed\entry\content\properties\Reasoning`: Empfehlungsargumentation (z. B. Erklärungen für die Empfehlung).

Eine Beispielantwort finden Sie unter 12.1.

###12\.6. Abrufen von Benutzerempfehlungen mit Elementliste (für aktiven Build)

Hierdurch werden Benutzerempfehlungen eines als aktiver Build gekennzeichneten Builds vom Typ "Recommendation" sowie zusätzlich eine Liste von Elementen abgerufen.

Die API gibt basierend auf dem Nutzungsverlauf des Benutzers eine Liste der vorhergesagten Elemente sowie die zusätzlich bereitgestellten Elemente zurück.

Hinweise: 1. Für einen FBT-Build gibt es keine Benutzerempfehlungen. 2. Wenn der aktive Build ein FBT-Build ist, gibt diese Methode einen Fehler zurück.


| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemIds=%27<itemIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`:|

|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dies ist der eindeutige Bezeichner des Modells. |
| bei der ersten | Dies ist der eindeutige Bezeichner des Benutzers. |
| itemIds | Dies ist eine kommagetrennte Liste der Elemente, für die Empfehlungen erstellt werden sollen. Max. Länge: 1024 |
| numberOfResults | Dies ist die Anzahl der erforderlichen Ergebnisse. |
| includeMetatadata | Zur zukünftigen Verwendung, stets „false“ |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200


Die Antwort enthält einen Eintrag pro empfohlenem Element. Jeder Eintrag enthält folgende Daten: - `Feed\entry\content\properties\Id`: ID des empfohlenen Elements. - `Feed\entry\content\properties\Name`: Name des Elements. - `Feed\entry\content\properties\Rating`: Bewertung der Empfehlung (höhere Werte bedeuten höheres Zutrauen). - `Feed\entry\content\properties\Reasoning`: Empfehlungsargumentation (z. B. Erklärungen für die Empfehlung).

Eine Beispielantwort finden Sie unter 12.1.

###12\.7. Abrufen von Benutzerempfehlungen (eines bestimmten Builds)

Hierdurch werden Benutzerempfehlungen eines bestimmten Builds vom Typ "Recommendation" abgerufen.

Die API gibt basierend auf dem Nutzungsverlauf des Benutzers (Nutzung in diesem bestimmten Build) eine Liste der vorhergesagten Elemente zurück.

Hinweis: Für einen FBT-Build gibt es keine Benutzerempfehlungen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`:|


|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dies ist der eindeutige Bezeichner des Modells. |
| bei der ersten | Dies ist der eindeutige Bezeichner des Benutzers. |
| numberOfResults | Dies ist die Anzahl der erforderlichen Ergebnisse. |
| includeMetatadata | Zur zukünftigen Verwendung, stets „false“ |
| buildId | Dies ist die Build-ID, die für diese Empfehlungsanforderung verwendet werden soll. |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200


Die Antwort enthält einen Eintrag pro empfohlenem Element. Jeder Eintrag enthält folgende Daten: - `Feed\entry\content\properties\Id`: ID des empfohlenen Elements. - `Feed\entry\content\properties\Name`: Name des Elements. - `Feed\entry\content\properties\Rating`: Bewertung der Empfehlung (höhere Werte bedeuten höheres Zutrauen). - `Feed\entry\content\properties\Reasoning`: Empfehlungsargumentation (z. B. Erklärungen für die Empfehlung).

Eine Beispielantwort finden Sie unter 12.1.


###12\.8. Abrufen von Benutzerempfehlungen mit Elementliste (für einen bestimmten Build)

Hierdurch werden Benutzerempfehlungen eines bestimmten Builds vom Typ "Recommendation" sowie die Liste zusätzlicher Elemente abgerufen.

Die API gibt basierend auf dem Nutzungsverlauf des Benutzers eine Liste der vorhergesagten Elemente sowie die Liste der zusätzlichen Elemente zurück.

Hinweis: Für einen FBT-Build gibt es keine Benutzerempfehlungen.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Beispiel<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`:|



|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dies ist der eindeutige Bezeichner des Modells. |
| bei der ersten | Dies ist der eindeutige Bezeichner des Benutzers. |
| itemIds | Dies ist eine kommagetrennte Liste der Elemente, für die Empfehlungen erstellt werden sollen. Max. Länge: 1024 |
| numberOfResults | Dies ist die Anzahl der erforderlichen Ergebnisse. |
| includeMetatadata | Zur zukünftigen Verwendung, stets „false“ |
| buildId | Dies ist die Build-ID, die für diese Empfehlungsanforderung verwendet werden soll. |
| apiVersion | 1,0 |

**Antwort:**

HTTP-Statuscode: 200


Die Antwort enthält einen Eintrag pro empfohlenem Element. Jeder Eintrag enthält folgende Daten: - `Feed\entry\content\properties\Id`: ID des empfohlenen Elements. - `Feed\entry\content\properties\Name`: Name des Elements. - `Feed\entry\content\properties\Rating`: Bewertung der Empfehlung (höhere Werte bedeuten höheres Zutrauen). - `Feed\entry\content\properties\Reasoning`: Empfehlungsargumentation (z. B. Erklärungen für die Empfehlung).

Eine Beispielantwort finden Sie unter 12.1.

##13\. Nutzungsverlauf eines Benutzers
Nachdem ein Empfehlungsmodell entwickelt wurde, ermöglicht es das System, den Verlauf eines Benutzers (also die mit einem bestimmten Benutzer verknüpften Elemente) abzurufen, der für den Build verwendet wurde. Mit dieser API kann der Nutzungsverlauf abgerufen werden.

Hinweis: Der Nutzungsverlauf ist zurzeit nur für Empfehlungsbuilds verfügbar.

###13\.1 Abrufen des Nutzungsverlaufs
Hierdurch wird die Liste der Elemente abgerufen, die im aktiven Build oder im angegebenen Build für die angegebene Benutzer-ID verwendet wurden.

| HTTP-Methode | URI |
|:--------|:--------|
|GET | Ruft den Nutzungsverlauf eines Benutzers für den aktiven Build ab:<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>Ruft den Nutzungsverlauf eines Benutzers für den angegebenen Build ab: `<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27`<br/><br/>Beispiel:`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277`|


|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Der eindeutige Bezeichner des Modells.|
| userId | Der eindeutige Bezeichner des Benutzers.
| buildId | Optionaler Parameter, mit dem angegeben werden kann, aus welchem Build der Nutzungsverlauf abgerufen werden soll.
| apiVersion | 1,0 |


**Antwort:**

HTTP-Statuscode: 200

Die Antwort enthält einen Eintrag pro empfohlenem Element. Jeder Eintrag enthält folgende Daten: – `Feed\entry\content\properties\Id`: ID des empfohlenen Elements – `Feed\entry\content\properties\Name`: Name des Elements – `Feed\entry\content\properties\Rating`: n. v. – `Feed\entry\content\properties\Reasoning`: n. v.

OData-XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get User History</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-05-26T15:32:47Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">CatalogItemsThatContainATokenEntity</title>
		<updated>2015-05-26T15:32:47Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Rating m:type="Edm.Double">0</d:Rating>
				<d:Reasoning m:type="Edm.String"/>
				<d:Metadata m:type="Edm.String"/>
				<d:FormattedRating m:type="Edm.Double" m:null="true"/>
			</m:properties>
		</content>
	</entry>
</feed>

##14\. Benachrichtigungen
Von den Azure Machine Learning-Empfehlungen werden Benachrichtigungen erstellt, wenn persistente Fehler im System auftreten. Es gibt 3 Typen von Benachrichtigungen: 1. Buildfehler: Diese Benachrichtigung wird bei jedem Buildfehler ausgelöst. 2. Fehler bei der Datenerfassungsverarbeitung: Diese Benachrichtigung wird ausgelöst, wenn innerhalb der letzten 5 Minuten mehr als 100 Fehler in der Verarbeitung von Nutzungsereignissen pro Modell vorliegen. 3. Fehler bei der Empfehlungsnutzung: Diese Benachrichtigung wird ausgelöst, wenn innerhalb der letzten 5 Minuten mehr als 100 Fehler in der Verarbeitung von Empfehlungsanforderungen pro Modell vorliegen.


###14\.1. Benachrichtigungen abrufen
Hierdurch werden alle Benachrichtigungen für alle Modelle oder für ein einzelnes Modell ab.

| HTTP-Methode | URI |
|:--------|:--------|
|GET |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Abrufen aller Benachrichtigungen für alle Modelle:<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>Beispiel für das Abrufen von Benachrichtigungen für ein bestimmtes Modell:<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dieser Parameter ist optional. Wenn er nicht angegeben wird, erhalten Sie alle Benachrichtigungen für alle Modelle. <br>Gültiger Wert: Eindeutiger Bezeichner des Modells.|
| apiVersion | 1,0 |
||| | Request Body | KEINE |

**Antwort:**

HTTP-Statuscode: 200

OData-XML

    The response includes one entry per notification. Each entry has the following data:
		* feed\entry\content\properties\UserName – Internal user name identification.
		* feed\entry\content\properties\ModelId – Model ID.
		* feed\entry\content\properties\Message – Notification message.
		* feed\entry\content\properties\DateCreated – Date that this notification was created in UTC format.
		* feed\entry\content\properties\NotificationType – Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of Notifications for a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-04T13:24:23Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
		<entry>
				<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfNotificationsForAUserEntity</title>
			<updated>2014-11-04T13:24:23Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
					<d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
					<d:Message m:type="Edm.String">Build failed for user</d:Message>
					<d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
					<d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
				</m:properties>
			</content>
		</entry>
	</feed>

###14\.2. Modellbenachrichtigungen löschen
Hierdurch werden alle gelesenen Benachrichtigungen für ein Modell gelöscht.

| HTTP-Methode | URI |
|:--------|:--------|
|LÖSCHEN |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>Beispiel:<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| modelId | Dies ist der eindeutige Bezeichner des Modells. |
| apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200

###14\.3. Benutzerbenachrichtigungen löschen
Hierdurch werden alle Benachrichtigungen für alle Modelle gelöscht.

| HTTP-Methode | URI |
|:--------|:--------|
|LÖSCHEN |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|	Parametername |	Gültige Werte |
|:--------			|:--------								|
| apiVersion | 1,0 |
||| | ||| | Request Body | KEINER |

**Antwort**:

HTTP-Statuscode: 200




##15\. Rechtliche Hinweise
Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument einschließlich URLs und anderer Verweise auf Websites können ohne Ankündigung geändert werden.<br><br> Einige der in diesem Dokument dargestellten Beispiele dienen nur zu Illustrationszwecken und sind frei erfunden. Ähnlichkeiten oder Verbindungen sind rein zufällig und nicht beabsichtigt.<br><br> Dieses Dokument gibt Ihnen keinerlei geistige Eigentums- oder anderweitige Rechte an irgendeinem Microsoft-Produkt. Sie dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.<br><br> © 2015 Microsoft. Alle Rechte vorbehalten.
 

<!---HONumber=September15_HO1-->