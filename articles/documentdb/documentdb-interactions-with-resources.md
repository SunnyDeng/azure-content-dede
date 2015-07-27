<properties 
	pageTitle="RESTful-Interaktionen mit DocumentDB-Ressourcen | Azure" 
	description="Erfahren Sie, wie Sie RESTful-Interaktionen mit Microsoft Azure DocumentDB-Ressourcen unter Verwendung von HTTP-Verben durchführen." 
	services="documentdb" 
	authors="h0n" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="h0n"/>

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
4.	Mit DELETE wird eine vorhandene Elementressource gelöscht
5.	HEAD ist mit GET gleichzusetzen, allerdings ohne die Antwortnutzlast (z. B. nur Header) 

>[AZURE.NOTE]In Zukunft soll Unterstützung für ausgewählte Updates über PATCH hinzugefügt werden.

Wie im folgenden Diagramm dargestellt, kann POST nur bei einer Feedressource ausgegeben werden; PUT und DELETE können nur bei einer Elementressource verwendet werden; GET und HEAD können sowohl bei Feed- als auch bei Elementressourcen verwendet werden.

![][1]

**Interaktionsmodell unter Verwendung der HTTP-Standardmethoden**

## Erstellen einer neuen Ressource mithilfe von POST 
Für ein besseres Verständnis des Interaktionsmodells werden wir eine neue Ressource (oder auch INSERT) erstellen. Zum Erstellen einer neuen Ressource ist eine HTTP POST-Anforderung mit dem Anforderungstext notwendig, in dem die Darstellung der Ressource mit dem URI des Containerfeeds enthalten ist, zu dem die Ressource gehört. Die einzige erforderliche Eigenschaft für die Anforderung ist die ID der Ressource.

Beispielsweise führen Sie zum Erstellen einer neuen Datenbank den Befehl POST für eine Datenbankressource mit /dbs aus (durch Angeben der ID-Eigenschaft mit einem eindeutigen Namen). Zum Erstellen einer neuen Sammlung führen Sie ebenso den Befehl POST für eine Sammlungsressource mit /dbs/_rid/colls/ usw. aus. Die Antwort beinhaltet die vollständig übermittelte Ressource mit den systemgenerierten Eigenschaften inklusive dem _self-Link der Ressource, mit dem zu anderen Ressourcen navigiert werden kann. Ein einfaches HTTP-basiertes Interaktionsmodell ist beispielsweise, dass ein Client mit einer HTTP-Anforderung eine neue Datenbank innerhalb eines Kontos erstellen kann. 

	POST https://fabrikam.documents.azure.com/dbs
	{
	      "id":"MyDb"
	}

Der DocumentDB-Dienst gibt eine erfolgreiche Antwort und einen Statuscode zurück, mit dem die erfolgreiche Erstellung der Datenbank angegeben wird.

	HTTP/1.1 201 Created
	Content-Type: application/json
	x-ms-request-charge: 4.95
	...

	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/UoEi5w==/",
	      "_ts": 1407370063,
	      "_etag": "00000100-0000-0000-0000-54b636600000",
	      "_colls": "colls/",
	      "_users": "users/"
	}
  
## Registrieren einer gespeicherten Prozedur mithilfe von POST
Als ein weiteres Beispiel für das Erstellen und Ausführen einer Ressource betrachten Sie eine einfache, vollständig in JavaScript geschriebene gespeicherte „HelloWorld“-Prozedur.

 	function HelloWorld() {
 	var context = getContext();
 	var response = context.getResponse();
 	
        response.setBody("Hello, World");
     }

Die gespeicherte Prozedur kann in einer Sammlung unter MyDb unter Verwendung des Befehls POST mit /dbs/_rid-db/colls/_rid-coll/sprocs registriert werden.

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs HTTP/1.1
	
	{
	  "id": "HelloWorld",
	  "body": "function HelloWorld() {
	           var context = getContext();
 	           var response = context.getResponse();
 	           
 	           response.setBody("Hello, World");
        	   }"
	}
Der DocumentDB-Dienst gibt eine erfolgreiche Antwort und einen Statuscode zurück, mit dem die erfolgreiche Registrierung der gespeicherten Prozedur angegeben wird.

	HTTP/1.1 201 Created
	Content-Type: application/json
	x-ms-request-charge: 4.95
	...

	{
	       "body": "function HelloWorld() {
	           var context = getContext();
 	           var response = context.getResponse();
 	           
 	           response.setBody("Hello, World");
        	   }",
	      "id": "HelloWorld"
	      "_rid": "UoEi5w+upwABAAAAAAAAgA==",
	      "_ts" :  1421227641
	      "_self": "dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA==/",
	      "_etag": "00002100-0000-0000-0000-50f71fda0000"
	}

## Ausführen einer gespeicherten Prozedur mithilfe von POST
Zum Ausführen der gespeicherten Prozedur aus dem oben genannten Beispiel muss POST mit dem URI der gespeicherten Prozedurressource ausgeführt werden (/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA==/). Dies wird im folgenden Code veranschaulicht.

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA== HTTP/1.1
	
Der DocumentDB-Dienst gibt die folgende Antwort zurück.

	HTTP/1.1 200 OK
	
	"Hello World"

Beachten Sie, dass das Verb POST für das Erstellen einer neuen Ressource sowie für das Ausführen einer gespeicherten Prozedur und das Erstellen einer SQL-Abfrage verwendet wird. Zur Veranschaulichung der Ausführung einer SQL-Abfrage berücksichtigen Sie Folgendes.

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	x-ms-documentdb-query-enable-scan: True
	Content-Type: application/query+json
	...
	
	{"query":"SELECT f.LastName AS Name, f.Address.City AS City FROM Families f WHERE f.id='AndersenFamily' OR f.Address.City='NY'"}

Der Dienst gibt eine Antwort mit den Ergebnissen der SQL-Abfrage zurück.

	HTTP/1.1 200 Ok
	...
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 4
	x-ms-request-charge: 3.1
	Content-Type: application/json1
	...
	{"_rid":"UoEi5w+upwA=","Documents":[{"Name":"Andersen","City":"Seattle"},{"Name":"Wakefield","City":"NY"}],"_count":2}



## Verwenden von PUT, GET und DELETE
Das Ersetzen oder Lesen einer Ressource läuft auf das Verwenden der Verben PUT (mit einem gültigen Anforderungstext) und GET mit dem _self-Link der Ressource hinaus. Ebenso läuft das Löschen einer Ressource auf die Verwendung des Verbs DELETE mit dem _self-Link der Ressource hinaus. Erwähnenswert ist auch, dass für die hierarchische Organisation von Ressourcen im DocumentDB-Ressourcenmodell das kaskadierende Löschen, bei dem beim Löschen einer übergeordneten Ressource alle von dieser abhängigen Ressourcen mitgelöscht werden, unterstützt sein muss. Abhängige Ressourcen können auf andere Knoten als die übergeordneten Ressourcen verteilt werden, sodass das Löschen verzögert werden könnte. Unabhängig von den Mechanismen der Garbage Collection wird beim Löschen einer Ressource das Kontingent sofort freigemacht und steht Ihnen zur Verfügung. Beachten Sie, dass die referentielle Integrität vom System gewährleistet wird. Sie können z. B. nicht eine Sammlung zu einer gelöschten Datenbank hinzufügen, oder ein Dokument einer nicht mehr vorhandenen Sammlung ersetzen oder abfragen. 
 
Das Verwenden von GET mit einem Feed von Ressourcen oder das Abfragen einer Sammlung kann zu Millionen von potenziellen Elementen führen. Daher ist es nicht sinnvoll, dass Server diese verwirklichen und Clients sie im Zusammenhang mit einzelnen Roundtrips/Anforderungen und Antwortaustausch verwenden. Um diese zu adressieren, können Clients mit DocumentDB jeweils eine große Feedseite mit Seitenzahlen versehen. Die Clients können den [x-ms-continuation]-Antwortheader als Cursor zum Navigieren zur nächsten Seite verwenden.

## Steuerung für optimistische Parallelität
Die meisten Webanwendungen beruhen auf einem auf der Steuerung für optimistische Parallelität basierenden Entitätstag, um unbeliebte Probleme wie „Update verloren“ und „Gelöschtes verloren“ zu vermeiden. Das Entitätstag ist ein HTTP-freundlicher, logischer, mit einer Ressource verknüpfter Zeitstempel. DocumentDB unterstützt die Steuerung für optimistische Parallelität, indem sichergestellt wird, dass jede HTPP-Antwort die mit der bestimmten Ressource (dauerhaft) verknüpfte Version beinhaltet. Die Parallelitätsprobleme werden in folgenden Fällen korrekt erkannt:

1.	Wenn zwei Clients gleichzeitig mutierende Anforderungen (über die Verben PUT/DELETE) an eine Ressource mit der neuesten Version der Ressource (angegeben mit dem [if-match]-Anforderungsheader) erstellen, unterzieht das DocumentDB-Datenbankmodul diese der Steuerung für Transaktionsparallelität.
2.	Wenn ein Client eine ältere Version der Ressource darstellt (angegeben mit dem [if-match]-Anforderungsheader) wird die Anforderung abgelehnt.  

## Konnektivitätsoptionen
DocumentDB stellt ein logisches Adressierungsmodell zur Verfügung, bei dem jede Ressource über einen logischen und stabilen URI verfügt, der durch den jeweiligen _self-Link identifiziert wird. Da es sich um ein verteiltes Speichersystem, das auf mehrere Regionen verteilt ist, handelt, werden Ressourcen in verschiedenen Datenbankkonten in DocumentDB auf zahlreiche Computer verteilt, und jede Partition ist für hohe Verfügbarkeit repliziert. Die Replikate, die die Ressourcen einer bestimmten Partition verwalten, registrieren physische Adressen. Während physische Adressen sich mit der Zeit aufgrund von Fehlern ändern, bleiben ihre logischen Adressen stabil und konstant. Die Übersetzung von logischer zu physischer Adresse ist in einer Routingtabelle gespeichert, die intern auch als Ressource verfügbar ist. DocumentDB stellt zwei Konnektivitätsmodi zur Verfügung: 

1.	**Gateway-Modus:** Die Clients sind vor der Übersetzung von logischen zu physischen Adressen oder den Routingdetails geschützt; sie befassen sich mit logischen URIs und der REST-konformen Navigation des Ressourcenmodells. Clients erstellen Anforderungen unter Verwendung des logischen URI, und der Edgecomputer übersetzt den logischen URI in eine physische Adresse des Replikats, das die Ressourcen verwaltet und die Anforderungen weiterleitet. Mit dem Zwischenspeichern der Routingtabelle durch den Edgecomputer (und regelmäßiger Aktualisierung) ist das Routing äußerst effizient. 
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
                    REST API, .NET, Node.js, Java, Python, JavaScript
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
Lesen Sie die [Azure DocumentDB-REST-API-Referenz](https://msdn.microsoft.com/library/azure/dn781481.aspx) für weitere Informationen zum Arbeiten mit Ressourcen über die REST-API.

## Referenzen
- [Referenz zur Azure DocumentDB-REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 
- [Abfragen von DocumentDB](../documentdb-sql-query/)
- [DocumentDB-SQL-Referenz](https://msdn.microsoft.com/library/azure/dn782250.aspx)
- [DocumentDB-Programmierung: Gespeicherte Prozeduren, Trigger und UDFs](../documentdb-programming/)
- [DocumentDB-Referenzdokumentation](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- REST [http://de.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
- JSON-Spezifikation [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
- HTTP-Spezifikation [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
- Entitätstags [http://de.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
 

<!---HONumber=July15_HO3-->