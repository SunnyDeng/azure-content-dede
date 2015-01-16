<properties title="Interact with DocumentDB resources" pageTitle="Interagieren mit DocumentDB-Ressourcen | Azure" description="DocumentDB stellt Client-SDKs für .NET, Python, Node.js und JavaScript bereit, die alle einfache Wrapper über den zugrunde liegenden REST APIs darstellen." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#Interagieren mit DocumentDB-Ressourcen 
DocumentDB bietet ein einfaches und offenes RESTful-Programmiermodell über HTTP. In der Vorschauversion stellt DocumentDB Client-SDKs für .NET, Python, Node.js und JavaScript bereit, die alle über die zugrundeliegenden REST APIs einfache Wrapper darstellen. In zukünftigen Versionen werden auch SDKs für C++ und Java bereitgestellt. Schreiben Sie eigene SDKs für Ihre spezifische Programmierumgebung und teilen Sie sie mit der Community, da wir unsere SDKs erweitern. 

>[AZURE.NOTE] Darüber hinaus bietet es ein hoch effizientes TCP-Protokoll mit einem REST-konformen Kommunikationsmodell, und ist über das .NET Client-SDK verfügbar.  

##Ressourcen
Die vom DocumentDB verwalteten Entitäten werden als **Ressourcen** bezeichnet, die durch ihren logischen URI eindeutig identifiziert werden. Entwickler können über standardmäßige HTTP-Verben, Anforderungs-/Antwortheader und Statuscodes mit den Ressourcen interagieren. Wie in der folgenden Abbildung dargestellt, besteht das **Ressourcenmodell** von DocumentDB aus Ressourcensätzen in einem Datenbankkonto, wobei jeder Satz über einen logischen und stabilen URI aufrufbar ist. Ein Ressourcensatz wird in diesem Dokument als **Feed** bezeichnet.  

![][1]  

##Hierarchisches Ressourcenmodell unter einem Datenbankkonto ##

Als Kunde von DocumentDB beginnen Sie mit der Bereitstellung eines DocumentDB-**Datenbankkontos**, für das Sie Ihr Azure-Abonnement verwenden. Ein Datenbankkonto besteht aus einem Satz von **Datenbanken**, die jeweils mehrere **Sammlungen** beinhalten, welche wiederum **gespeicherte Prozeduren, Trigger, UDFs, Dokumente** und verwandte **Anhänge** beinhalten. Einer Datenbank sind zudem **Benutzer** zugeordnet, die jeweils über eine Reihe von **Berechtigungen** für den Zugriff auf verschiedene andere Auflistungen, gespeicherte Prozeduren, Trigger, UDFs, Dokumente oder Anhänge verfügen. Während Datenbanken, Benutzer, Berechtigungen und Sammlungen vom System definierte Ressourcen mit bekannten Schemata darstellen, sind in Dokumenten und Anhängen beliebige, benutzerdefinierte JSON-Inhalte enthalten.  

|Ressource 	|Beschreibung
|-----------|-----------
|Datenbankkonto	|Ein Datenbankkonto ist mit einer oder mehreren Kapazitätseinheiten verknüpft, die bereitgestellten Dokumentspeicher und -durchsatz, ein Satz an Datenbanken und Blobspeicher darstellen. Sie können mithilfe Ihres Azure-Abonnements mindestens ein Datenbankkonto erstellen.
|Datenbank	|Eine Datenbank ist ein logischer Container für Dokumentspeicher, der auf Sammlungen aufgeteilt ist. Sie ist auch ein Benutzercontainer.
|Benutzer	|Logischer Namespace für Bereichs-/geteilte Berechtigungen. 
|Berechtigung	|Ein Autorisierungstoken, das einem Benutzer für den autorisierten Zugriff auf eine bestimmte Ressource zugeordnet ist.
|Sammlung	|Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik.
|Gespeicherte Prozedur	|Mit JavaScript geschriebene Anwendungslogik, die mit einer Sammlung registriert und über Transaktion innerhalb des Datenbankmoduls ausgeführt wird.
|Auslöser	|Mit JavaScript geschriebene Anwendungslogik unter Berücksichtigung von mit Einfüge-, Ersetzungs- oder Löschvorgängen verknüpften Nebeneffekten.
|UDF	|Eine in JavaScript geschriebene Anwendungslogik ohne Nebeneffekte. Mit UDFs kann ein benutzerdefinierter Abfrageoperator erstellt und damit die DocumentDB-Kernabfragesprache erweitert werden.
|Dokument	|Benutzerdefinierter (beliebiger) JSON-Inhalt. Standardmäßig müssen kein Schema definiert oder sekundäre Indizes für alle zu einer Sammlung hinzugefügten Dokumente bereitgestellt werden.
|Anhang	|Anhänge sind besondere Dokumente mit Verweisen und Metadaten, die mit externem Blob/externen Medien verknüpft sind. Entwickler können auswählen, ob das Blob von DocumentDB verwaltet, oder in einem externen Blobdienstanbieter wie OneDrive, Dropbox usw. gespeichert wird. 

###Vergleich von system- und benutzerdefinierten Ressourcen
Ressourcen wie Datenbankkonten, Datenbanken, Sammlungen, Benutzer, Berechtigungen, gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen verfügen alle über ein festes Schema und werden als *Systemressourcen* bezeichnet. Im Gegensatz dazu weisen Ressourcen wie Dokumente und Anhänge keine Einschränkungen hinsichtlich des Schemas auf und sind somit ein Beispiel für *benutzerdefinierte Ressourcen*. In DocumentDB werden sowohl systemdefinierte als auch benutzerdefinierte Ressourcen als standardkonforme JSON dargestellt und verwaltet. Alle Ressourcen, egal ob system- oder benutzerdefiniert, haben folgende Eigenschaften gemeinsam.

>[AZURE.NOTE] Beachten Sie, dass in allen systemgenierten Eigenschaften in einer Ressource ein Unterstrich (_) in ihrer JSON-Darstellung voransteht.  


<table width="500"> 
<tbody>
<tr>
<td valign="top" ><p><b>Eigenschaft </b></p></td>
<td valign="top" ><p><b>Vom Benutzer einstellbar oder systemgeneriert?</b></p></td>
<td valign="top" ><p><b>Zweck</b></p></td>
</tr>

<tr>
<td valign="top" ><p>_rid</p></td>
<td valign="top" ><p>Systemgeneriert</p></td>
<td valign="top" ><p>Systemgeneriert, eindeutige und hierarchische ID der Ressource</p></td>
</tr>

<tr>
<td valign="top" ><p>_etag</p></td>
<td valign="top" ><p>Systemgeneriert</p></td>
<td valign="top" ><p>ETag der Ressource, das für die Steuerung optimistischer Parallelität erforderlich ist</p></td>
</tr>

<tr>
<td valign="top" ><p>_ts</p></td>
<td valign="top" ><p>Systemgeneriert</p></td>
<td valign="top" ><p>Zuletzt aktualisierter Zeitstempel der Ressource</p></td>
</tr>

<tr>
<td valign="top" ><p>_self</p></td>
<td valign="top" ><p>Systemgeneriert</p></td>
<td valign="top" ><p>Eindeutiger aufrufbarer URI der Ressource </p></td>
</tr>

<tr>
<td valign="top" ><p>id</p></td>
<td valign="top" ><p>Vom Benutzer einstellbar</p></td>
<td valign="top" ><p>Benutzerdefinierter eindeutiger Name der Ressource </p></td>
</tr>

</tbody>
</table>


###Übermittlungsdarstellung der Ressourcen
DocumentDB verfügt über keine proprietären Erweiterungen des JSON-Standards oder besonderer Codierungen, und kann nur mit standardkonformen JSON-Dokumenten verwendet werden.  
 
###Adressieren einer Ressource
Alle Ressourcen können über URI aufgerufen werden. Der Wert der **_self**-Eigenschaft einer Ressource stellt den relativen URI der Ressource dar. Das Format des URI besteht aus den /<feed>/{_rid}-Pfadsegmenten:  

|Value of the _self	|Description
|-------------------|-----------
|/dbs	|feed of databases under a database account
|/dbs/{_rid-db}	|Database with the unique id property with the value {_rid-db}
|/dbs/{_rid-db}/colls/	|feed of collections under a database 
|/dbs/{_rid-db}/colls/{_rid-coll}	|Collection with the unique id property with the value {_rid-coll}
|/dbs/{_rid-db}/users/	|feed of users under a database 
|/dbs/{_rid-db}/users/{_rid-user}	|User with the unique id property with the value {_rid-user}
|/dbs/{_rid-db}/users/{_rid-user}/permissions	|feed of permissions under a database 
|/dbs/{_rid-db}/users/{_rid-user}/permissions/{_rid-permission}	|Permission with the unique id property with the value {_rid-permission}  
  

Eine Ressource verfügt ebenfalls über einen eindeutigen benutzerdefinierten Namen, der über die ID-Eigenschaft der Ressource bereitgestellt wird. Die ID ist benutzerdefiniert und ist eine Zeichenfolge mit bis zu 256 Zeichen Länge, die innerhalb einer bestimmten übergeordneten Ressource eindeutig ist. Der Wert der ID-Eigenschaft aller Dokumente innerhalb einer bestimmten Sammlung ist z. B. eindeutig, ist jedoch sammlungsübergreifend möglicherweise nicht eindeutig. Der Wert der ID-Eigenschaft aller Berechtigungen eines bestimmten Benutzers ist ebenso eindeutig, ist jedoch benutzerübergreifend möglicherweise eindeutig. Die _rid-Eigenschaft wird zum Erstellen eines aufrufbaren _self-Links einer Ressource verwendet.   

Jede Ressource verfügt ebenfalls über eine systemgenerierte hierarchische Ressourcen-ID (auch als RID bezeichnet), die über die _rid-Eigenschaft verfügbar ist. Die RID codiert die gesamte Hierarchie einer bestimmten Ressource und ist eine einfache interne Darstellung, die zum Erzwingen einer dezentralen refentiellen Integrität verwendet. Die RID ist innerhalb eines Datenbankkontos eindeutig und wird von DocumentDB intern für effiziente Weiterleitung ohne getrenntes Nachschlagen verwendet. 

Bei den Werten der _self- und   _rid-Eigenschaften handelt es sich um alternative und kanonische Darstellungen einer Ressource.  
 
##Grundlegendes Interaktionsmodell
Die Ressourcen unterstützen folgende HTTP-Verben  mit der jeweiligen Standardinterpretation:

>[AZURE.NOTE] In Zukunft soll Unterstützung für ausgewählte Updates über PATCH hinzugefügt werden.  

1.	Mit POST wird eine neue Elementressource erstellt. 
2.	Mit GET wird eine vorhandene Element- oder Feedressource gelesen 
3.	Mit PUT wird eine vorhandene Elementressource ersetzt 
4.	Mit DELETE wird eine vorhandene  Elementressource gelöscht
5.	HEAD ist mit GET gleichzusetzen, allerdings ohne die Antwortnutzlast (z. B. nur Header)

In zukünftigen Versionen werden für Elementressourcen ebenfalls PATCH-Verben unterstützt.  

Wie in der Abbildung unten dargestellt, kann POST nur bei einer Feedressource verwendet werden; PUT und DELETE können nur bei einer Elementressource verwendet werden; GET und HEAD können sowohl bei Feed- als auch bei Elementressourcen verwendet werden. 

![][2]  

**Interaktionsmodell unter Verwendung der HTTP-Standardverben**

Für ein besseres Verständnis des Interaktionsmodells werden wir eine neue Ressource (oder auch INSERT) erstellen. Zum Erstellen einer neuen Ressource ist eine HTTP POST-Anforderung mit dem Anforderungstext notwendig, in dem die Darstellung der Ressource mit dem URI des Containerfeeds, zu dem die Ressource gehört, enthalten ist. Die einzige erforderliche Eigenschaft für die Anforderung ist die ID der Ressource.  

Als Beispiel führen Sie zum Erstellen einer neuen Datenbank den Befehl POST für eine Datenbankressource mit /dbs aus (durch Angeben der ID-Eigenschaft mit einem eindeutigen Namen) aus. Zum Erstellen einer neuen Sammlung führen Sie ebenso den Befehl POST für eine Sammlungsressource mit /dbs/_rid/colls/ usw. aus. Die Antwort beinhaltet die vollständig übermittelte Ressource mit den systemgenerierten Eigenschaften inklusive dem _self-Link der Ressource, mit dem zu anderen Ressourcen navigiert werden kann. Ein einfaches HTTP-basiertes Interaktionsmodell ist beispielsweise, dass ein Client z. B. mit einer HTTP-Anforderung eine neue Datenbank innerhalb eines Kontos erstellen kann.  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}
DocumentDB gibt eine erfolgreiche Antwort und einen Statuscode zurück, mit dem die erfolgreiche Erstellung der Datenbank angegeben wird.  

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

Der DocumentDB-Dienst gibt eine erfolgreiche Antwort und einen Statuscode zurück, mit dem die erfolgreiche Erstellung der Datenbank angegeben wird.  

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

Schließlich muss zum Ausführen der gespeicherten Prozedur im obigen Beispiel ein POST mit dem URI der gespeicherten Prozedurressource ausgegeben werden (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1). Dies ist im Folgenden dargestellt:  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

Der DocumentDB-Dienst gibt die folgende Antwort zurück:  

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

Beachten Sie, dass das Verb POST sowohl für das Erstellen einer neuen Ressource, als auch für das Ausführen einer gespeicherten Prozedur und das Erstellen einer SQL-Abfrage verwendet wird. Zur Veranschaulichung der Ausführung einer SQL-Abfrage berücksichtigen Sie Folgendes:  

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


Das Ersetzen oder Lesen einer Ressource läuft auf das Verwenden der Verben PUT (mit einem gültigen Anforderungstext) und GET mit dem _self-Link der Ressource hinaus. Ebenso läuft das Löschen einer Ressource auf die Verwendung des Verbs DELETE mit dem _self-Link der Ressource hinaus. Erwähnenswert ist auch, dass für die hierarchische Organisation von Ressourcen im DocumentDB-Ressourcenmodell das kaskadierende Löschen, bei dem beim Löschen einer übergeordneten Ressource alle von dieser abhängigen Ressourcen mitgelöscht werden, unterstützt sein muss. Abhängige Ressourcen können auf andere Knoten als die übergeordneten Ressourcen verteilt werden, sodass das Löschen verzögert werden könnte. Unabhängig von den Mechanismen der Garbage Collection wird beim Löschen einer Ressource das Kontingent sofort freigemacht und steht Ihnen zur Verfügung. Beachten Sie, dass die referentielle Integrität vom System gewährleistet wird. Sie können z. B. nicht eine Sammlung zu einer gelöschten Datenbank hinzufügen, oder ein Dokument einer nicht mehr vorhandenen Sammlung ersetzen oder abfragen.  
 
Das Verwenden von GET mit einem Feed von Ressourcen oder das Abfragen einer Sammlung kann zu Millionen von potenziellen Elementen führen. Daher ist es nicht sinnvoll, dass Server diese verwirklichen, und Clients sie im Zusammenhang mit einzelnen Roundtrips/Anforderungen und Antwortaustausch verwenden. Um diese zu adressieren, können Clients mit DocumentDB jeweils eine große Feedseite mit Seitenzahlen versehen. Die Clients können den [x-ms-continuationToken]-Antwortheader als Cursor zum Navigieren zur nächsten Seite verwenden.   

##Steuerung für optimistische Parallelität
Die meisten Webanwendungen beruhen auf einem auf der Steuerung für optimistische Parallelität basierenden Entitätstag, um unbeliebte Probleme wie "Update verloren" und "Gelöschtes verloren" zu vermeiden. Das Entitätstag ist ein HTTP-freundlicher, logischer, mit einer Ressource verknüpfter Zeitstempel. DocumentDB unterstützt die Steuerung für optimistische Parallelität, indem sichergestellt wird, dass jede HTPP-Antwort die mit der bestimmten Ressource (dauerhaft) verknüpfte Version beinhaltet. Die Parallelitätsprobleme werden in folgenden Fällen korrekt erkannt:  

1.	Wenn zwei Clients gleichzeitig mutierende Anforderungen (über die Verben PUT/DELETE) an eine Ressource mit der neuesten Version der Ressource (angegeben mit dem [if-match]-Anforderungsheader) erstellen, unterzieht das DocumentDB-Datenbankmodul diese der Steuerung für Transaktionsparallelität.
2.	Wenn ein Client eine ältere Version der Ressource darstellt (angegeben mit dem [if-match]-Anforderungsheader) wird die Anforderung abgelehnt.  

##Konnektivitätsoptionen
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

##Referenzen
-	REST [http://en.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	JSON-Spezifikation  [http://-www.ietf.org/rfc/rfc4627.txt](http://-www.ietf.org/rfc/rfc4627.txt)
-	HTTP-Spezifikation [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	Entitätstags [http://en.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[Abfragen von DocumentDB](/documentation/articles/documentdb-sql-query/)
-	[DocumentDB SQL-Referenz](http://go.microsoft.com/fwlink/p/?LinkID=510612)
-	[DocumentDB-Programmierung: Gespeicherte Prozeduren, Trigger und UDFs](/documentation/articles/documentdb-programming/)
-	[DocumentDB-Referenzdokumentation](http://go.microsoft.com/fwlink/p/?LinkID=402384)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources1.png
[2]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
