<properties 
	pageTitle="Übersicht über die Media Services-REST-API - Azure" 
	description="Übersicht über die Media Services-REST-API" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="juliako"/>


# Übersicht über die Media Services-REST-API 

Microsoft Azure Media Services ist ein Dienst, der OData-basierte HTTP-Anforderungen akzeptiert und mit ausführlichem JSON- oder atom+pub-Code darauf antworten kann. Da Media Services mit den Azure-Entwicklungsrichtlinien konform ist, muss jeder Client bei der Verbindung mit Media Services eine Reihe obligatorischer HTTP-Header verwenden. Darüber hinaus stehen verschiedene optionale Header zur Verfügung. In den folgenden Abschnitten werden die Header und HTTP-Verben beschrieben, die Sie verwenden können, um Anforderungen zu erstellen und Antworten von Media Services zu empfangen.


## Von Media Services unterstützte standardmäßige HTTP-Anforderungsheader

Für jeden Media Services-Aufruf müssen Sie eine Reihe obligatorischer Header in Ihre Anforderung einschließen. Darüber hinaus stehen verschiedene optionale Header zur Auswahl. In der folgenden Tabelle sind die erforderlichen Header aufgeführt:


<table border="1">
<tr><th>Header</th><th>Typ</th><th>Wert</th></tr>
<tr><td>Autorisierung</td><td>Bearer</td><td>Das Bearer-Token ist der einzig zulässige Autorisierungsmechanismus. Der Wert muss außerdem das von ACS bereitgestellte Zugriffstoken enthalten.</td></tr>
<tr><td>x-ms-version</td><td>Decimal</td><td>2.9</td></tr>
<tr><td>DataServiceVersion</td><td>Decimal</td><td>3,0</td></tr>
<tr><td>MaxDataServiceVersion</td><td>Decimal</td><td>3,0</td></tr>
</table><br/>


>[AZURE.NOTE] Da Media Services OData verwendet, um das zugrunde liegenden Repository für Medienobjekt-Metadaten über REST-APIs verfügbar zu machen, sollten der DataServiceVersion- Header und der MaxDataServiceVersion-Header in jede Anforderung eingeschlossen werden. Falls dies nicht geschieht, geht Media Services aktuell davon aus, dass der DataServiceVersion-Wert 3.0 verwendet wird.

Im Folgenden finden Sie eine Reihe optionaler Header:

<table border="1">
<tr><th>Header</th><th>Typ</th><th>Wert</th></tr>
<tr><td>Date</td><td>RFC 1123-Datum</td><td>Zeitstempel der Anforderung</td></tr>
<tr><td>Accept</td><td>Content-Typ</td><td>Der angeforderte Inhaltstyp für die Antwort, z. B.:
<ul><li>application/json;odata=verbose</li><li>application/atom+xml</li></ul></br> Antworten können unterschiedliche Inhaltstypen aufweisen, z. B. einen BLOB-Abruf, bei dem eine erfolgreiche Antwort den BLOB-Stream als Nutzlast enthält.</td></tr>
<tr><td>Accept-Encoding</td><td>Gzip, deflate</td><td>GZIP- und DEFLATE-Codierung, falls zutreffend. Hinweis: Bei großen Ressourcen kann Media Services diesen Header ignorieren und unkomprimierte Daten zurückgeben.
</td></tr>
<tr><td>Accept-Language</td><td>"en", "es" usw.</td><td>Gibt die bevorzugte Sprache für die Antwort an.</td></tr>
<tr><td>Accept-Charset</td><td>Charset-Typ, z. B. "UTF-8"</td><td>Der Standardwert ist UTF-8.</td></tr>
<tr><td>X-HTTP-Method</td><td>HTTP-Methode</td><td>Ermöglicht Clients oder Firewalls, die keine HTTP-Methoden wie PUT oder DELETE unterstützen, die Verwendung dieser Methoden über einen getunnelten GET-Aufruf.</td></tr>
<tr><td>Content-Typ</td><td>Content-Typ</td><td>Der Inhaltstyp des Anforderungstexts in PUT- oder POST-Anforderungen.</td></tr>
<tr><td>client-request-id</td><td>String</td><td>Ein vom Aufrufer definierter Wert, der die angegebene Anforderung identifiziert. Falls angegeben, wird dieser Wert in die Antwortnachricht eingeschlossen, um die Anforderung zuzuordnen. <br/><br/>
<b>Wichtig</b><br/>
Werte sollten bei 2.096 Bytes (2 KB) begrenzt sein.</td></tr>
</table><br/>


## Von Media Services unterstützte standardmäßige HTTP-Antwortheader

Im Folgenden lernen Sie einen Satz von Headern kennen, die je nach der angeforderten Ressource und beabsichtigten Aktion zurückgegeben werden können.


<table border="1">
<tr><th>Header</th><th>Typ</th><th>Wert</th></tr>
<tr><td>request-id</td><td>String</td><td>Ein eindeutiger, vom Dienst generierter Bezeichner für den aktuellen Vorgang.</td></tr>
<tr><td>client-request-id</td><td>String</td><td>Ein Bezeichner, der vom Aufrufer in der ursprünglichen Anforderung angegeben wird, sofern vorhanden.</td></tr>
<tr><td>Date</td><td>RFC 1123-Datum</td><td>Das Datum, zu dem die Anforderung verarbeitet wurde.</td></tr>
<tr><td>Content-Typ</td><td>Variabel</td><td>Der Inhaltstyp des Antworttexts.</td></tr>
<tr><td>Content-Encoding</td><td>Variabel</td><td>GZIP oder DEFLATE, je nachdem, welche Codierung geeignet ist.</td></tr>
</table><br/>

## Von Media Services unterstützte standardmäßige HTTP-Verben

Im Folgenden finden eine vollständige Liste der HTTP-Verben, die für HTTP-Anforderungen verwendet werden können:


<table border="1">
<tr><th>Verb</th><th>Beschreibung</th></tr>
<tr><td>GET</td><td>Gibt den aktuellen Wert eines Objekts zurück.</td></tr>
<tr><td>POST</td><td>Erstellt ein Objekt auf Grundlage der bereitgestellten Daten oder sendet einen Befehl.</td></tr>
<tr><td>PUT</td><td>Ersetzt ein Objekt oder erstellt ein benanntes Objekt (falls zutreffend).</td></tr>
<tr><td>LÖSCHEN</td><td>Löscht ein Objekt.</td></tr>
<tr><td>MERGE</td><td>Aktualisiert ein vorhandenes Objekt anhand von Änderungen der benannten Eigenschaft.</td></tr>
<tr><td>HEAD</td><td>Gibt die Metadaten eines Objekts für eine GET-Antwort zurück.</td></tr>
</table><br/>

## Ermitteln des Media Services-Modells

Um Media Services-Entitäten schnell aufzufinden, können Sie den $metadata-Vorgang verwenden. Dadurch können Sie alle gültigen Entitätstypen, Entitätseigenschaften, Zuordnungen, Funktionen, Aktionen usw. abrufen. Im folgenden Beispiel wird gezeigt, wie Sie den URI erstellen.: https://media.windows.net/API/$metadata.

Sie sollten "?api-version=2.x" an das Ende des URIs anhängen, wenn Sie die Metadaten in einem Browser anzeigen möchten. Andernfalls sollte der x-ms-version-Header nicht in die Anforderung eingeschlossen werden.


<!-- Anchors. -->


<!-- URLs. -->
  
  [Verwaltungsportal]: http://manage.windowsazure.com/




<!--HONumber=52-->