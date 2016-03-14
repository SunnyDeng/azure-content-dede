<properties 
	pageTitle="Übersicht über die Media Services-REST-API | Microsoft Azure" 
	description="Übersicht über die Media Services-REST-API" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
 	ms.date="03/01/2016"  
	ms.author="juliako"/>


# Übersicht über die Media Services-REST-API 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Microsoft Azure Media Services ist ein Dienst, der OData-basierte HTTP-Anforderungen akzeptiert und mit ausführlichem JSON- oder atom+pub-Code darauf antworten kann. Da Media Services mit den Azure-Entwicklungsrichtlinien konform ist, muss jeder Client bei der Verbindung mit Media Services eine Reihe obligatorischer HTTP-Header verwenden. Darüber hinaus stehen verschiedene optionale Header zur Verfügung. In den folgenden Abschnitten werden die Header und HTTP-Verben beschrieben, die Sie verwenden können, um Anforderungen zu erstellen und Antworten von Media Services zu empfangen.

##Überlegungen 

Berücksichtigen Sie Folgendes, wenn Sie REST verwenden:

- Beim Abfragen von Entitäten gibt es ein Limit von 1.000 Entitäten, die gleichzeitig zurückgegeben werden können, da die öffentliche REST-Version 2 Abfrageergebnisse auf 1.000 Ergebnisse begrenzt. Sie müssen **Skip** und **Take** (.NET) bzw. **top** (REST) wie in [diesem .NET-Beispiel](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) und [diesem REST-API-Beispiel](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities) beschrieben verwenden. 

- Wenn Sie JSON verwenden und angeben, dass das Schlüsselwort **\_\_metadata** in der Anforderung verwendet werden soll (z. B. für Verweise auf ein verknüpftes Objekt), MÜSSEN Sie den **Accept**-Header auf das [ausführliche JSON-Format](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) festlegen (siehe folgendes Beispiel). OData versteht die **\_\_metadata**-Eigenschaft in der Anforderung nur, wenn Sie das ausführliche Format verwenden.

		POST https://media.windows.net/API/Jobs HTTP/1.1
		Content-Type: application/json;odata=verbose
		Accept: application/json;odata=verbose
		DataServiceVersion: 3.0
		MaxDataServiceVersion: 3.0
		x-ms-version: 2.11
		Authorization: Bearer <token> 
		Host: media.windows.net
		
		{
			"Name" : "NewTestJob", 
			"InputMediaAssets" : 
				[{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
		. . . 
		

## Von Media Services unterstützte standardmäßige HTTP-Anforderungsheader

Für jeden Media Services-Aufruf müssen Sie eine Reihe obligatorischer Header in Ihre Anforderung einschließen. Darüber hinaus stehen verschiedene optionale Header zur Auswahl. In der folgenden Tabelle sind die erforderlichen Header aufgeführt:


Header|Typ|Wert
---|---|---
Autorisierung|Bearer|Das Bearer-Token ist der einzig zulässige Autorisierungsmechanismus. Der Wert muss außerdem das von ACS bereitgestellte Zugriffstoken enthalten.
x-ms-version|Decimal|2\.11
DataServiceVersion|Decimal|3\.0
MaxDataServiceVersion|Decimal|3\.0



>[AZURE.NOTE] Da Media Services OData verwendet, um das zugrunde liegenden Repository für Medienobjekt-Metadaten über REST-APIs verfügbar zu machen, sollten der DataServiceVersion- Header und der MaxDataServiceVersion-Header in jede Anforderung eingeschlossen werden. Falls dies nicht geschieht, geht Media Services aktuell davon aus, dass der DataServiceVersion-Wert 3.0 verwendet wird.

Im Folgenden finden Sie eine Reihe optionaler Header:

Header|Typ|Wert
---|---|---
Date|RFC 1123-Datum|Zeitstempel der Anforderung
Accept|Content-Typ|Der angeforderte Inhaltstyp für die Antwort, Beispiele: <p> - application/json;odata=verbose<p> - application/atom+xml<p> Antworten können einen anderen Inhaltstyp wie z. B. einen Blobabruf aufweisen, wobei eine erfolgreiche Antwort den Blobstream als Nutzlast enthält.
Accept-Encoding|Gzip, deflate|GZIP- und DEFLATE-Codierung, falls zutreffend. Hinweis: Bei großen Ressourcen kann Media Services diesen Header ignorieren und unkomprimierte Daten zurückgeben.
Accept-Language|„en“, „es“ usw.|Gibt die bevorzugte Sprache für die Antwort an.
Accept-Charset|Charset-Typ, z. B. „UTF-8“|Der Standardwert ist UTF-8.
X-HTTP-Method|HTTP-Methode|Ermöglicht Clients oder Firewalls, die keine HTTP-Methoden wie PUT oder DELETE unterstützen, die Verwendung dieser Methoden über einen getunnelten GET-Aufruf.
Content-Typ|Content-Typ|Der Inhaltstyp des Anforderungstexts in PUT- oder POST-Anforderungen.
client-request-id|String|Ein vom Aufrufer definierter Wert, der die angegebene Anforderung identifiziert. Falls angegeben, wird dieser Wert in die Antwortnachricht eingeschlossen, um die Anforderung zuzuordnen. <p><p>**Wichtig**<p>Werte müssen auf 2096 Bytes (2 KB) begrenzt werden.

## Von Media Services unterstützte standardmäßige HTTP-Antwortheader

Im Folgenden lernen Sie einen Satz von Headern kennen, die je nach der angeforderten Ressource und beabsichtigten Aktion zurückgegeben werden können.


Header|Typ|Wert
---|---|---
request-id|String|Ein eindeutiger, vom Dienst generierter Bezeichner für den aktuellen Vorgang.
client-request-id|String|Ein Bezeichner, der vom Aufrufer in der ursprünglichen Anforderung angegeben wird, sofern vorhanden.
Date|RFC 1123-Datum|Das Datum, zu dem die Anforderung verarbeitet wurde.
Content-Typ|Variabel|Der Inhaltstyp des Antworttexts.
Content-Encoding|Variabel|GZIP oder DEFLATE, je nachdem, welche Codierung geeignet ist.


## Von Media Services unterstützte standardmäßige HTTP-Verben

Im Folgenden finden eine vollständige Liste der HTTP-Verben, die für HTTP-Anforderungen verwendet werden können:


Verb|Beschreibung
---|---
GET|Gibt den aktuellen Wert eines Objekts zurück.
POST|Erstellt ein Objekt auf Grundlage der bereitgestellten Daten oder sendet einen Befehl.
PUT|Ersetzt ein Objekt oder erstellt ein benanntes Objekt (falls zutreffend).
LÖSCHEN|Löscht ein Objekt.
MERGE|Aktualisiert ein vorhandenes Objekt anhand von Änderungen der benannten Eigenschaft.
HEAD|Gibt die Metadaten eines Objekts für eine GET-Antwort zurück.

##Einschränkung

Beim Abfragen von Entitäten gibt es ein Limit von 1.000 Entitäten, die gleichzeitig zurückgegeben werden können, da die öffentliche REST-Version 2 Abfrageergebnisse auf 1.000 Ergebnisse begrenzt. Sie müssen **Skip** und **Take** (.NET) bzw. **top** (REST) wie in [diesem .NET-Beispiel](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) und [diesem REST-API-Beispiel](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities) beschrieben verwenden.


## Ermitteln des Media Services-Modells

Um Media Services-Entitäten schnell aufzufinden, können Sie den $metadata-Vorgang verwenden. Dadurch können Sie alle gültigen Entitätstypen, Entitätseigenschaften, Zuordnungen, Funktionen, Aktionen usw. abrufen. Im folgenden Beispiel wird gezeigt, wie Sie den URI erstellen: https://media.windows.net/API/$metadata

Sie sollten "?api-version=2.x" an das Ende des URIs anhängen, wenn Sie die Metadaten in einem Browser anzeigen möchten. Andernfalls sollte der x-ms-version-Header nicht in die Anforderung eingeschlossen werden.



##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

  
  [Azure Classic Portal]: http://manage.windowsazure.com/



 

<!---HONumber=AcomDC_0302_2016-->