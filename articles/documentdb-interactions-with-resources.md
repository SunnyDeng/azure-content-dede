<properties 
	pageTitle="RESTful-Interaktionen mit DocumentDB-Ressourcen | Azure" 
	description="Erfahren Sie, wie Sie RESTful-Interaktionen mit Microsoft Azure DocumentDB-Ressourcen unter Verwendung von HTTP-Verben durchführen." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="mimig"/>

# RESTful-Interaktionen mit DocumentDB-Ressourcen 

DocumentDB unterstützt die Verwendung von HTTP-Methoden zum Erstellen, Lesen, Ersetzen, Abrufen und Löschen von DocumentDB-Ressourcen.

Nach dem Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

- Wie funktionieren die HTTP-Standardmethoden mit DocumentDB-Ressourcen?
- Wie erstelle ich eine neue Ressource mithilfe von POST?
- Wie registriere ich eine gespeicherte Prozedur mithilfe von POST?
- Wie unterstützt DocumentDB die Nebenläufigkeitssteuerung?
- Was sind die Konnektivitätsoptionen für HTTP und TCP?

## Übersicht über HTTP-Verben
DocumentDB-Ressourcen unterstützen die folgenden HTTP-Verben mit ihrer Standardinterpretation:

1.	Mit POST wird eine neue Elementressource erstellt. 
2.	Mit GET wird eine vorhandene Element- oder Feedressource gelesen 
3.	Mit PUT wird eine vorhandene Elementressource ersetzt 
4.	Mit DELETE wird eine vorhandene Elementressource gelöscht.
5.	HEAD ist mit GET gleichzusetzen, allerdings ohne die Antwortnutzlast (z. B. nur Header) 

>[AZURE.NOTE] In Zukunft soll Unterstützung für ausgewählte Updates über PATCH hinzugefügt werden.  

Wie im folgenden Diagramm dargestellt, kann POST nur bei einer Feedressource ausgegeben werden; PUT und DELETE können nur bei einer Elementressource verwendet werden; GET und HEAD können sowohl bei Feed- als auch bei Elementressourcen verwendet werden. 

![][1]  

**Interaktionsmodell unter Verwendung der HTTP-Standardmethoden**

## Erstellen einer neuen Ressource mithilfe von POST 
Für ein besseres Verständnis des Interaktionsmodells werden wir eine neue Ressource (oder auch INSERT) erstellen. Zum Erstellen einer neuen Ressource ist eine HTTP POST-Anforderung mit dem Anforderungstext notwendig, in dem die Darstellung der Ressource mit dem URI des Containerfeeds enthalten ist, zu dem die Ressource gehört. Die einzige erforderliche Eigenschaft für die Anforderung ist die ID der Ressource.  

Als Beispiel führen Sie zum Erstellen einer neuen Datenbank den Befehl POST für eine Datenbankressource mit /dbs aus (durch Angeben der ID-Eigenschaft mit einem eindeutigen Namen) aus. Zum Erstellen einer neuen Sammlung führen Sie ebenso den Befehl POST für eine Sammlungsressource mit /dbs/_rid/colls/ usw. aus. Die Antwort beinhaltet die vollständig übermittelte Ressource mit den systemgenerierten Eigenschaften inklusive dem _self-Link der Ressource, mit dem zu anderen Ressourcen navigiert werden kann. Ein einfaches HTTP-basiertes Interaktionsmodell ist beispielsweise, dass ein Client mit einer HTTP-Anforderung eine neue Datenbank innerhalb eines Kontos erstellen kann.  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}

Der DocumentDB-Dienst gibt eine erfolgreiche Antwort und einen Statuscode zurück, mit dem die erfolgreiche Erstellung der Datenbank angegeben wird.  

	[201 Created]
	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/MyDb/",
	      "_ts": 1407370063,
	      "_etag": "\"00002100-0000-0000-0000-50e71fda0000\"",
	      "_colls": "colls/",
	      "_users": "users/"
	}
  
## Registrieren einer gespeicherten Prozedur mithilfe von POST
Als ein weiteres Beispiel für das Erstellen und Ausführen einer Ressource betrachten Sie die folgende vollständig in JavaScript geschriebene gespeicherte Prozedur.   

	function sproc(docToCreate, addedPropertyName, addedPropertyValue) {
	    var collectionManager = getContext().getCollection();
	    collectionManager.createDocument(collectionManager.getSelfLink(), docToCreate,   
	    function(err, docCreated) {
	        if(err) throw new Error('Error while creating document: ' + err.message);
	        
	        docCreated.addedPropertyName = addedPropertyValue;
	        getContext().getResponse().setBody(docCreated);
	    });
	}

Die gespeicherte Prozedur kann in einer Sammlung unter MyDb unter Verwendung des Befehls POST mit /dbs/_rid-db/colls/_rid-coll/sprocs registriert werden. 

	POST /dbs/MyDb/colls/MyColl/sprocs HTTP/1.1
	
	{
	  "id": "sproc1",
	  "body": "function (docToCreate, addedPropertyName, addedPropertyValue) {
	                var collectionManager = getContext().getCollection();
	                collectionManager.createDocument(collectionManager.getSelfLink(), 
	                            docToCreate, function(err, docCreated) {
	                    if(err) throw new Error('Error while creating document: ' + 
	                                                     err.message);
	                    
	                    docCreated.addedPropertyName = addedPropertyValue;
	                    getContext().getResponse().setBody(docCreated);
	                });
	            }"
	}
Der DocumentDB-Dienst gibt eine erfolgreiche Antwort und einen Statuscode zurück, mit dem die erfolgreiche Registrierung der gespeicherten Prozedur angegeben wird.  

	[201 Created]
	{
	      "id": "sproc1",
	      "_rid": "EoEi5w==",
	      "_self": "dbs/MyDb/colls/MyColl/sprocs/sproc1",
	      "_etag": "\"00002100-0000-0000-0000-50f71fda0000\"",
	       ...
	}

## Ausführen einer gespeicherten Prozedur mithilfe von POST
Zum Ausführen der gespeicherten Prozedur aus dem oben genannten Beispiel muss POST mit dem URI der gespeicherten Prozedurresssource ausgeführt werden (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1). Dies wird im folgenden Code veranschaulicht.  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

Der DocumentDB-Dienst gibt die folgende Antwort zurück.  

	HTTP/1.1 200 OK
	 { 
	  "id": "TestDocument",  
	  "_rid": "ZTlcANiwqwIBAAAAAAAAAA==",
	  "_ts": 1407370063,
	  "_self": "dbs/ZTlcAA==/colls/ZTlcANiwqwI=/docs/ZTlcANiwqwIBAAAAAAAAAA==/",
	  "_etag": "00000900-0000-0000-0000-53e2c34f0000",
	  "_attachments": "attachments/",
	  "book": "Autumn of the Patriarch", 
	  "price": 200
	}

Beachten Sie, dass das Verb POST für das Erstellen einer neuen Ressource sowie für das Ausführen einer gespeicherten Prozedur und das Erstellen einer SQL-Abfrage verwendet wird. Zur Veranschaulichung der Ausführung einer SQL-Abfrage berücksichtigen Sie Folgendes.  

	POST /dbs/MyDb/colls/MyColl/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/sql
	
	SELECT * FROM root.children

Der Dienst gibt eine Antwort mit den Ergebnissen der SQL-Abfrage zurück.   

	HTTP/1.1 200 Ok
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 81
	x-ms-request-charge: 1.43
	Content-Length: 287
	
	{"_rid":"sehcAIE2Qy4=","Documents":[[{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}],[{"familyName":"Merriam","givenName":"Jesse","gender":"female","grade":1},{"familyName":"Miller","givenName":"Lisa","gender":"female","grade":8}]],"count":2}


## Verwenden von PUT, GET und DELETE
Das Ersetzen oder Lesen einer Ressource läuft auf das Verwenden der Verben PUT (mit einem gültigen Anforderungstext) und GET mit dem _self-Link der Ressource hinaus. Ebenso läuft das Löschen einer Ressource auf die Verwendung des Verbs DELETE mit dem _self-Link der Ressource hinaus. Erwähnenswert ist auch, dass für die hierarchische Organisation von Ressourcen im DocumentDB-Ressourcenmodell das kaskadierende Löschen, bei dem beim Löschen einer übergeordneten Ressource alle von dieser abhängigen Ressourcen mitgelöscht werden, unterstützt sein muss. Abhängige Ressourcen können auf andere Knoten als die übergeordneten Ressourcen verteilt werden, sodass das Löschen verzögert werden könnte. Unabhängig von den Mechanismen der Garbage Collection wird beim Löschen einer Ressource das Kontingent sofort freigemacht und steht Ihnen zur Verfügung. Beachten Sie, dass die referentielle Integrität vom System gewährleistet wird. Sie können z. B. nicht eine Sammlung zu einer gelöschten Datenbank hinzufügen, oder ein Dokument einer nicht mehr vorhandenen Sammlung ersetzen oder abfragen.  
 
Das Verwenden von GET mit einem Feed von Ressourcen oder das Abfragen einer Sammlung kann zu Millionen von potenziellen Elementen führen. Daher ist es nicht sinnvoll, dass Server diese verwirklichen und Clients sie im Zusammenhang mit einzelnen Roundtrips/Anforderungen und Antwortaustausch verwenden. Um diese zu adressieren, können Clients mit DocumentDB jeweils eine große Feedseite mit Seitenzahlen versehen. Die Clients können den [x-ms-continuationToken]-Antwortheader als Cursor zum Navigieren zur nächsten Seite verwenden.   

## Steuerung für optimistische Parallelität
Die meisten Webanwendungen beruhen auf einem auf der Steuerung für optimistische Parallelität basierenden Entitätstag, um unbeliebte Probleme wie "Update verloren" und "Gelöschtes verloren" zu vermeiden. Das Entitätstag ist ein HTTP-freundlicher, logischer, mit einer Ressource verknüpfter Zeitstempel. DocumentDB unterstützt die Steuerung für optimistische Parallelität, indem sichergestellt wird, dass jede HTPP-Antwort die mit der bestimmten Ressource (dauerhaft) verknüpfte Version beinhaltet. Die Parallelitätsprobleme werden in folgenden Fällen korrekt erkannt:  

1.	Wenn zwei Clients gleichzeitig mutierende Anforderungen (über die Verben PUT/DELETE) an eine Ressource mit der neuesten Version der Ressource (angegeben mit dem [if-match] Anforderungsheader) erstellen, unterzieht das DocumentDB-Datenbankmodul diese der Steuerung für Transaktionsparallelität.
2.	Wenn ein Client eine ältere Version der Ressource darstellt (angegeben mit dem [if-match]-Anforderungsheader) wird die Anforderung abgelehnt.  

## Konnektivitätsoptionen
DocumentDB stellt ein logisches Adressierungsmodell zur Verfügung, bei dem jede Ressource über einen logischen und stabilen URI verfügt, der durch den jeweiligen _self-Link identifiziert wird. Da es sich um ein verteiltes Speichersystem, das auf mehrere Regionen verteilt ist, handelt, werden Ressourcen in verschiedenen Datenbankkonten in DocumentDB auf zahlreiche Computer verteilt, und jede Partition ist für hohe Verfügbarkeit repliziert. Die Replikate, die die Ressourcen einer bestimmten Partition verwalten, registrieren physische Adressen. Während physische Adressen sich mit der Zeit aufgrund von Fehlern ändern, bleiben ihre logischen Adressen stabil und konstant. Die Übersetzung von logischer zu physischer Adresse ist in einer Routingtabelle gespeichert, die intern auch als Ressource verfügbar ist. DocumentDB stellt zwei Konnektivitätsmodi zur Verfügung:  

1.	**Gatewaymodus:** Clients sind vor der Übersetzung von logischen zu physischen Adressen oder den Routingdetails geschützt; sie befassen sich mit logischen URIs und der REST-konformen Navigation des Ressourcenmodells. Die Clients geben die Anforderungen über einen logischen URI aus, und die Edgecomputer übersetzen den logischen URI in die physische Adresse des Replikats, das die Ressource verwaltet und die Anforderung weiterleitet. Da die Edgecomputer die Routingtabelle zwischenspeichern (und regelmäßig aktualisieren), ist das Routing sehr effizient. 
2.	**Direkter Konnektivitätsmodus:** Die Routingtabelle wird von Clients direkt in ihrem Prozessspeicher verwaltet und regelmäßig aktualisiert. Der Client kann eine direkte Verbindung mit den Replikaten herstellen und die Edgecomputer umgehen.   


<table width="300">
    <tbody>
        <tr>
            <td width="120" valign="top">
                <p>
                    <strong>Konnektivitätsmodus</strong>
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    <strong>Protokoll</strong>
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    <strong>Details</strong>
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    <strong>DocumentDB SDKs</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    Gateway
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    Anwendungen können unter Verwendung logischer URIs eine direkte Verbindung mit den Egdeknoten herstellen. Dabei fällt ein zusätzlicher Netzwerkhop an.
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    REST-APIs
                </p>
                <p>
                    .NET, JavaScript, Node.js, Python
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    Direkte Konnektivität
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS und
                </p>
                <p>
                    TCP
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    Die Anwendungen können direkt auf die Routingtabelle zugreifen und die direkte Verbindung mit den Replikaten durch das clientseitige Routing herstellen.
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    .NET
                </p>
            </td>
        </tr>
    </tbody>
</table>

## Nächste Schritte
Erkunden Sie die [Azure DocumentDB-REST-API-Referenz](https://msdn.microsoft.com/library/azure/dn781481.aspx) für weitere Informationen zum Arbeiten mit Ressourcen über die REST-API.

## Referenzen
-   [Referenz zur Azure DocumentDB-REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 
-	REST [http://en.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	JSON-Spezifikation   [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
-	HTTP-Spezifikation [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	Entitätstags [http://en.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[Abfragen von DocumentDB](documentdb-sql-query.md)
-	[DocumentDB-SQL-Referenz](https://msdn.microsoft.com/library/azure/dn782250.aspx)
-	[DocumentDB-Programmierung: Gespeicherte Prozeduren, Trigger und UDFs](../documentdb-programming/)
-	[DocumentDB-Referenzdokumentation](https://msdn.microsoft.com/library/azure/dn781482.aspx)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png

<!--HONumber=49-->