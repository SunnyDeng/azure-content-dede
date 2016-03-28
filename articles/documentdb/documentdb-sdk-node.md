<properties 
	pageTitle="DocumentDB Node.js SDK | Microsoft Azure" 
	description="Wichtige Informationen zum Node.js SDK einschließlich Veröffentlichungstermine, Deaktivierungstermine und Änderungen an den einzelnen Versionen des DocumentDB Node.js SDK." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/08/2016" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Node.js SDK

<table>
<tr><td>**Download**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Mitwirken**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Dokumentation**</td><td>[Node.js SDK Reference Documentation](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Beispiele**</td><td>[Node.js Code Samples](https://github.com/Azure/azure-documentdb-node/tree/master/samples)</td></tr>
<tr><td>**Erste Schritte**</td><td>[Erste Schritte mit dem Node.js-SDK] (documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Aktuelle unterstützte Plattform**</td><td>[Node.js v0.10] (https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##Versionshinweise
###<a name="1.5.6"/>1.5.6</a>

- Fehler in „RangePartitionResolver.resolveForRead“ behoben, durch den aufgrund einer ungültigen Verkettung der Ergebnisse keine Links zurückgegeben wurden.

###<a name="1.5.5"/>1.5.5</a>

- Problem mit „hashParitionResolver resolveForRead()“ behoben: Bei einem nicht angegebenen Partitionsschlüssel wurde eine Ausnahme ausgelöst, anstatt eine Liste aller registrierten Links zurückgegeben.

###<a name="1.5.4"/>1.5.4</a>

- Problem [Nr. 100](https://github.com/Azure/azure-documentdb-node/issues/100) behoben – Dedizierter HTTPS-Agent: Vermeiden Sie das Ändern des globalen Agents zu DocumentDB-Zwecken. Verwenden Sie einen dedizierten-Agent für alle Anforderungen der Bibliothek.

###<a name="1.5.3"/>1.5.3</a>

- Problem [Nr. 81](https://github.com/Azure/azure-documentdb-node/issues/81) behoben – Ordnungsgemäße Behandlung von Bindestrichen in Medien-IDs.

###<a name="1.5.2"/>1.5.2</a>

- Problem [Nr. 95](https://github.com/Azure/azure-documentdb-node/issues/95) behoben – Warnung des EventEmitter-Listeners über Speicherverluste.

###<a name="1.5.1"/>1.5.1</a>

- Problem [92](https://github.com/Azure/azure-documentdb-node/issues/90) behoben – Umbenennung des Ordners „Hash“ in „hash“ für Systeme, bei denen die Groß-/Kleinschreibung beachtet wird.

### <a name="1.5.0"/>1.5.0</a>

- Unterstützung für Sharding implementiert, indem Hash- und Bereichspartitionsresolver hinzugefügt wurden.

### <a name="1.4.0"/>1.4.0</a>

- Upsert implementiert. Neue „upsertXXX“-Methoden für „DocumentClient“. 

### <a name="1.3.0"/>1.3.0</a>

- Version ausgelassen, um die Versionsnummer in Einklang mit den anderen SDKs zu bringen.

### <a name="1.2.2"/>1.2.2</a>

- Q Promises Wrapper auf neues Repository aufgeteilt.
- Paketdatei für Npm-Registrierung aktualisiert.

### <a name="1.2.1"/>1.2.1</a>

- ID-basiertes Routing implementiert.
- Problem [49](https://github.com/Azure/azure-documentdb-node/issues/49) korrigiert – „current“-Eigenschaft in Konflikt mit „current()“-Methode.

### <a name="1.2.0"/>1.2.0</a>

- Unterstützung für „GeoSpatial“-Index hinzugefügt.
- „Id“-Eigenschaft wird für alle Ressourcen überprüft. IDs für Ressourcen dürfen nicht die Zeichen ?, /, #, &#47;&#47;, enthalten und nicht mit einem Leerzeichen enden. 
- Neue Überschrift „Fortschritt der Indextransformation“ zu „ResourceResponse“ hinzugefügt.

### <a name="1.1.0"/>1.1.0</a>

- V2-Indizierungsrichtlinie implementiert.

### <a name="1.0.3"/>1.0.3</a>

- Problem [40](https://github.com/Azure/azure-documentdb-node/issues/40) – „eslint“- und „grunt“-Konfigurationen im Core und Promise SDK implementiert.

### <a name="1.0.2"/>1.0.2</a>

- Problem [45](https://github.com/Azure/azure-documentdb-node/issues/45) – Promise Wrapper enthält keinen fehlerhaften Header.

### <a name="1.0.1"/>1.0.1</a>

- Möglichkeit zum Abfragen von Konflikten durch Hinzufügen von „readConflicts“, „readConflictAsync“ und „queryConflicts“ implementiert.
- API-Dokumentation aktualisiert.
- Problem [41](https://github.com/Azure/azure-documentdb-node/issues/41) – „client.createDocumentAsync“-Fehler  

### <a name="1.0.0"/>1.0.0</a>

- Allgemeine Verfügbarkeit (GA) des SDK

## Veröffentlichungs- und Deaktivierungstermine
Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten.

Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher wird empfohlen, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren.

Anforderungen von DocumentDB mithilfe eines deaktivierten SDK werden vom Dienst abgelehnt.

> [AZURE.WARNING]
Alle Versionen des Azure DocumentDB SDK für Node.js vor Version **1.0.0** werden am **29. Februar 2016** deaktiviert.

<br/>

| Version | Herausgabedatum | Deaktivierungstermine 
| ---	  | ---	         | ---
| [1.5.6](#1.5.6) | 8. März 2016 |
| [1.5.5](#1.5.5) | 2. Februar 2016 |--- 
| [1.5.4](#1.5.4) | 1. Februar 2016 |--- 
| [1.5.2](#1.5.2) | 26. Januar 2016 |--- 
| [1.5.2](#1.5.2) | 22. Januar 2016 |--- 
| [1.5.1](#1.5.1) | 4. Januar 2016 |--- 
| [1.5.0](#1.5.0) | 31. Dezember 2015 |--- 
| [1.4.0](#1.4.0) | 6. Oktober 2015 |--- 
| [1.3.0](#1.3.0) | 6. Oktober 2015 |--- 
| [1.2.2](#1.2.2) | 10. September 2015 |--- 
| [1.2.1](#1.2.1) | 15. August 2015 |--- 
| [1.2.0](#1.2.0) | 5. August 2015 |--- 
| [1.1.0](#1.1.0) | 9. Juli 2015 |--- 
| [1.0.3](#1.0.3) | 4. Juni 2015 |--- 
| [1.0.2](#1.0.2) | 23. Mai 2015 |--- 
| [1.0.1](#1.0.1) | 15. Mai 2015 |--- 
| [1.0.0](#1.0.0) | 8. April 2015 |--- 
| 0.9.4-prelease | 6. April 2015 | 29. Februar 2016
| 0.9.3-prelease | 14. Januar 2015 | 29. Februar 2016 
| 0.9.2-prelease | 18. Dezember 2014 | 29. Februar 2016
| 0.9.1-prelease | 22. August 2014 | 29. Februar 2016 
| 0.9.0-prelease | 21. August 2014 | 29. Februar 2016


## Häufig gestellte Fragen
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Weitere Informationen

Weitere Informationen zu DocumentDB finden Sie auf der Seite zum Dienst [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!----HONumber=AcomDC_0309_2016-->