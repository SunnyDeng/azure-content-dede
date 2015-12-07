<properties 
	pageTitle="DocumentDB Java SDK | Microsoft Azure" 
	description="Wichtige Informationen zum Java SDK einschließlich Veröffentlichungstermine, Deaktivierungstermine und Änderungen an den einzelnen Versionen des DocumentDB Java SDK." 
	services="documentdb" 
	documentationCenter="java" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Java SDK

<table> <tr><td>** Herunterladen **</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr> <tr><td>** Mitwirken **</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr> <tr><td>** Dokumentation **</td><td>[Referenzdokumentation zum Java SDK](http://azure.github.io/azure-documentdb-java/)</td></tr> <tr><td>** Erste Schritte **</td><td>[Erste Schritte mit dem Java SDK](documentdb-java-application.md)</td></tr> <tr><td>** Derzeit unterstützte Laufzeit **</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr> </table></br>

## Versionsinformationen

### <a name="1.4.0"/>[1\.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Upsert implementiert. Neue UpsertXXX-Methoden hinzugefügt, um das „Upsert“-Feature zu unterstützen.
- ID-basiertes Routing implementiert. Keine öffentliche API-Änderungen, alle Änderungen sind intern.

### <a name="1.3.0"/>1.3.0
- Version ausgelassen, um die Versionsnummer in Einklang mit den anderen SDKs zu bringen.

### <a name="1.2.0"/>[1\.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- „GeoSpatial“-Index unterstützt.
- „Id“-Eigenschaft wird für alle Ressourcen überprüft. IDs für Ressourcen dürfen nicht die Zeichen ?, /, #, \\ enthalten und nicht mit einem Leerzeichen enden.
- Neue Überschrift „Fortschritt der Indextransformation“ zu „ResourceResponse“ hinzugefügt.

### <a name="1.1.0"/>[1\.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- V2-Indizierungsrichtlinie implementiert.

### <a name="1.0.0"/>[1\.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- Allgemeine Verfügbarkeit (GA) des SDK

## Veröffentlichungs- und Deaktivierungstermine
Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren, unterstützten Version zu gewährleisten.

Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher wird empfohlen, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren.

Anforderungen von DocumentDB mithilfe eines deaktivierten SDK werden vom Dienst abgelehnt.

> [AZURE.WARNING]Alle Versionen des Azure DocumentDB SDK für Java vor Version **1.0.0** werden am **29. Februar 2016** deaktiviert.

<br/>

| Version | Herausgabedatum | Deaktivierungstermine 
| ---	  | ---	         | ---
| [1\.4.0](#1.4.0) | 5. Oktober 2015 |---| [1\.3.0](#1.3.0) | 5. Oktober 2015 |---| [1\.2.0](#1.2.0) | 5. August 2015 |---| [1\.1.0](#1.1.0) | 9. Juli 2015 |---| [1\.0.1](#1.0.1) | 12. Mai 2015 |---| [1\.0.0](#1.0.0) | 7. April 2015 |---| 0.9.5-prelease | 9. März 2015 | 29. Februar 2016 | 0.9.4-prelease | 17. Februar 2015 | 29. Februar 2016 | 0.9.3-prelease | 13. Januar 2015 | 29. Februar 2016 | 0.9.2-prelease | 19. Dezember 2014 | 29. Februar 2016 | 0.9.1-prelease | 19. Dezember 2014 | 29. Februar 2016 | 0.9.0-prelease | 10. Dezember 2014 | 29. Februar 2016

## Häufig gestellte Fragen
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Weitere Informationen

Weitere Informationen zu DocumentDB finden Sie auf Seite zum Dienst [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_1125_2015-->