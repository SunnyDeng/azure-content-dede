<properties 
	pageTitle="Modellieren von Daten in Azure DocumentDB | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Daten für eine NoSQL-Dokumentendatenbank wie Azure DocumentDB modellieren." 
	services="documentdb" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="mimig1" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="ryancraw"/>

#Modellieren von Daten in DocumentDB#
Schemafreie Datenbanken wie DocumentDB erleichtern die Übernahme von Änderungen an Ihrem Datenmodell, dennoch sollten Sie etwas über Ihre Daten nachdenken.

Wie werden die Daten gespeichert? Wie wird Ihre Anwendung Daten abrufen und abfragen? Ist Ihre Anwendung lese- und schreibintensiv?

Nach dem Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

- Was sollte ich über ein Dokument in einer Dokumentendatenbank wissen?
- Was ist Datenmodellierung und warum sollte ich mich dafür interessieren? 
- Inwiefern unterscheidet sich das Modellieren von Daten in einer Dokumentendatenbank von einer relationalen Datenbank?
- Wie kann ich Datenbeziehungen in einer nicht-relationalen Datenbank ausdrücken?
- Wann bette ich Daten ein, und wann verknüpfe ich sie?

##Einbetten von Daten##
Versuchen Sie beim ersten Modellieren von Daten in einer Dokumentenablage, wie z. B. DocumentDB, Ihre Entitäten als **eigenständige Dokumente**, dargestellt im JSON-Format, zu behandeln.

Bevor wir uns stärker damit befassen, sehen Sie sich zunächst einmal genau an, wie Sie eine Entität in einer relationalen Datenbank modellieren, ein Konzept, mit dem viele von uns bereits vertraut sind. Das folgende Beispiel zeigt, wie eine Person in einer relationalen Datenbank gespeichert werden kann.

![Relationalen Datenbankmodell](./media/documentdb-modeling-data/relational-data-model.png)

Bei der Arbeit mit relationalen Datenbanken galt viele Jahre lang das Motto: normalisieren, normalisieren, normalisieren.

Beim Normalisieren von Daten wird in der Regel eine Entität, z. B. eine Person, in einzelne Datenelemente unterteilt. Im obigen Beispiel kann eine Person über mehrere Kontaktdetaildatensätze sowie mehrere Adressdatensätze verfügen. Wir gehen sogar noch einen Schritt weiter und unterteilen auch die Kontaktdetails, indem zusätzliche allgemeine Felder, wie z. B. Typ, extrahiert werden. Das Gleiche gilt für Adressen: Jeder Datensatz einem Typ wie z. B. *Privat* oder *Geschäftlich* zugeordnet.

Die beim Normalisieren von Daten geltende Prämisse besteht darin, dass das **Speichern von redundanten Daten in jedem Datensatz zu vermeiden** ist und dass stattdessen auf die einzelnen Daten verwiesen werden soll. Um in diesem Beispiel eine Person mit allen ihren Kontaktdaten und Adressen zu lesen, müssen Sie Verknüpfungen verwenden, um die Daten effektiv zur Laufzeit zu aggregieren.

	SELECT p.FirstName, p.LastName, a.City, cd.Detail
	FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Für das Aktualisieren einer einzelnen Person mit allen ihren Kontaktdaten und Adressen sind Schreibvorgänge über viele einzelne Tabellen hinweg erforderlich.

Sehen Sie sich jetzt an, wie Sie die gleichen Daten als eigenständige Entität in einer Dokumentendatenbank modellieren können.
		
	{
	    "id": "1",
	    "firstName": "Thomas",
	    "lastName": "Andersen",
	    "addresses": [
	        {            
	            "line1": "100 Some Street",
	            "line2": "Unit 1",
	            "city": "Seattle",
	            "state": "WA",
	            "zip": 98012
	        }
	    ],
	    "contactDetails": [
	        {"email: "thomas@andersen.com"},
	        {"phone": "+1 555 555-5555", "extension": 5555}
	    ] 
	}

Mit dem oben stehenden Ansatz haben wir jetzt den Personendatensatz **denormalisiert**, indem wir alle Informationen im Zusammenhang mit dieser Person, z. B. ihre Kontaktdaten und Adressen, in ein einzelnes JSON-Dokument **eingebettet** haben. Da wir nicht auf ein festes Schema beschränkt sind, haben wir darüber hinaus die Flexibilität, z. B. Kontaktdetails in vollständig verschiedenen Formen zu haben.

Das Abrufen eines vollständigen Personendatensatzes aus der Datenbank besteht jetzt aus einem einzelnen Lesevorgang einer einzelne Sammlung und für ein einzelnes Dokument. Das Aktualisieren eines Personendatensatzes zusammen mit den Kontaktinformationen und Adressen ist auch ein einzelner Schreibvorgang in einem einzelnen Dokument.

Durch das Denormalisieren von Daten muss Ihre Anwendung u. U. weniger Abfragen und Aktualisierungen ausgeben, um allgemeine Vorgänge abzuschließen.

###Wann Sie einbetten sollten

Verwenden Sie in der Regel eingebettete Datenmodelle in den folgenden Fällen:

- Zwischen Entitäten gibt es **contains**-Beziehungen.
- Zwischen Entitäten gibt es **eins-zu-viele**-Beziehungen.
- Es gibt eingebettete Daten, die sich **selten ändern**.
- Es gibt eingebettete Daten, die nicht **grenzenlos** wachsen.
- Es gibt eingebettete Daten, die von **integraler** Bedeutung für die Daten in einem Dokument sind.

> [AZURE.NOTE]In der Regel bieten denormalisierte Datenmodelle eine bessere **Leseleistung**.

###Wann Sie nicht einbetten sollten

In einer Dokumentendatenbank gilt zwar die Faustregel, dass alles denormalisiert wird und alle Daten in ein einzelnes Dokument eingebettet werden können, dies kann jedoch einige Situationen hervorrufen, die vermieden werden sollten.

Nehmen Sie beispielsweise diesen JSON-Ausschnitt.

	{
		"id": "1",
		"name": "What's new in the coolest Cloud",
		"summary": "A blog post by someone real famous",
		"comments": [
			{"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
			{"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
			…
			{"id": 100001, "author": "jane", "comment": "and on we go ..."},
			…
			{"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
			…
			{"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
		]
	}

So könnte eine Beitragsentität mit eingebetteten Kommentaren aussehen, wenn wir ein typisches Blog- oder CMS-System modellieren würden. Das Problem bei diesem Beispiel besteht darin, dass das Kommentar-Array **unbegrenzt** ist, d. h., es gibt (praktisch) keine Begrenzung hinsichtlich der Anzahl an Kommentaren zu einem einzelnen Beitrag. Dies wird zu einem Problem, da die Größe des Dokuments erheblich zunehmen kann.

> [AZURE.TIP]Dokumente in DocumentDB weisen eine Maximalgröße auf. Weitere Informationen dazu finden Sie unter [DocumentDB-Grenzen](documentdb-limits.md).

Mit zunehmender Größe des Dokuments wird die Möglichkeit zum Übertragen von Daten über das Netz sowie zum Lesen und Aktualisieren des Dokuments bei Skalierung beeinträchtigt.

In einem solchen Fall sollte lieber das folgende Modell in Betracht gezogen werden.
		
	Post document:
	{
		"id": 1,
		"name": "What's new in the coolest Cloud",
		"summary": "A blog post by someone real famous",
		"recentComments": [
			{"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
			{"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
			{"id": 3, "author": "jane", "comment": "....."}
		]
	}

	Comment documents:
	{
		"postId": 1
		"comments": [
			{"id": 4, "author": "anon", "comment": "more goodness"},
			{"id": 5, "author": "bob", "comment": "tails from the field"},
			...
			{"id": 99, "author": "angry", "comment": "blah angry blah angry"}
		]
	},
	{
		"postId": 1
		"comments": [
			{"id": 100, "author": "anon", "comment": "yet more"},
			...
			{"id": 199, "author": "bored", "comment": "will this ever end?"}
		]
	}

Bei diesem Modell sind die drei aktuellsten Kommentare in den Beitrag selbst eingebettet, wobei es sich diesmal um ein Array mit einer festen Grenze handelt. Die anderen Kommentare sind zu Stapeln von je 100 Kommentaren gruppiert und in separaten Dateien gespeichert. Die Größe des Stapels wurde auf 100 festgelegt, da unsere fiktive Anwendung es dem Benutzer ermöglicht, 100 Kommentare gleichzeitig zu laden.

Das Einbetten von Daten ist auch dann keine gute Idee, wenn die eingebetteten Daten häufig in Dokumenten verwendet und häufig geändert werden.

Nehmen Sie beispielsweise diesen JSON-Ausschnitt.

	{
	    "id": "1",
	    "firstName": "Thomas",
	    "lastName": "Andersen",
	    "holdings": [
	        {
	            "numberHeld": 100,
	            "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
	        },
	        {
	            "numberHeld": 50,
	            "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
	        }
	    ]
	}

Dabei kann es sich um das Aktienportfolio einer Person handeln. Wir haben uns dazu entschlossen, die Aktiendaten in jedes Portfoliodokument einzubetten. In einer Umgebung, in der verknüpfte Daten häufig geändert werden, wie z. B. eine Aktienhandelsanwendung, bedeutet das Einbetten von sich häufig ändernden Daten, dass Sie bei jedem Aktienhandel jedes Portfoliodokument aktualisieren müssen.

Die Aktie *zaza* kann jeden Tag mehrere Hundert Mal gehandelt werden, und Tausende Benutzer besitzen *zaza* in ihrem Portfolio. Bei einem Datenmodell wie dem obigen müssten wir viele Tausend Portfoliodokumente mehrmals täglich aktualisieren, was zu einem schlecht skalierbaren System führt.

##<a id="Refer"></a>Verweisen auf Daten##

Das Einbetten von Daten funktioniert somit in vielen Fällen gut, aber es ist klar, dass es Szenarien gibt, bei denen das Denormalisieren von Daten mehr Probleme verursacht als es wert ist. Was also können wir jetzt tun?

Relationale Datenbanken sind nicht der einzige Ort, an dem Sie Beziehungen zwischen Entitäten herstellen können. In einer Dokumentendatenbank können sich die Informationen in einem Dokument befinden, das sich tatsächlich auf Daten in anderen Dokumenten bezieht. Ich befürworte nicht für eine Minute, dass wir Systeme in DocumentDB erstellen sollen, die besser für eine relationale Datenbank oder eine andere Dokumentendatenbank geeignet sind, aber einfache Beziehungen sind in Ordnung und können sehr nützlich sein.

Im JSON-Abschnitt unten verwenden wir das vorherige Beispiel eines Aktienportfolios, aber dieses Mal verweisen wir auf den Aktieneintrag im Portfolio, anstatt ihn einzubetten. Auf diese Weise müssen wir nur dieses eine Aktiendokument aktualisieren, wenn sich der Aktieneintrag mehrmals am Tag ändert.

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }
	
    Stock documents:
    {
        "id": 1,
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": 2,
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }
    

Ein unmittelbarer Nachteil dieses Ansatzes besteht jedoch darin, dass, wenn Ihre Anwendung Informationen zu jeder Aktie anzeigen muss, die bei der Anzeige des Portfolios einer Person enthalten sein müssen, Sie mehrere Roundtrips zur Datenbank durchführen müssen, um die Informationen für jedes Aktiendokument zu laden. Hier haben wir beschlossen, die Effizienz der Schreibvorgänge zu verbessern, die mehrmals täglich durchgeführt werden, wir nehmen wiederum die Beeinträchtigung der Lesevorgänge hin, die sich potenziell weniger stark auf die Leistung dieses speziellen Systems auswirken.

> [AZURE.NOTE]Normalisierte Datenmodelle **können mehrere Roundtrips zum Server erfordern**.

### Was ist mit Fremdschlüsseln?
Da es derzeit kein Konzept für eine Einschränkung gibt, Fremdschlüssel oder anderweitig, sind alle Beziehungen zwischen Dokumenten in Dokumenten im Grunde "schwache Verknüpfungen" und werden nicht von der Datenbank selbst überprüft. Wenn Sie sicherstellen möchten, dass die Daten, auf die ein Dokument verweist, tatsächlich vorhanden sind, müssen Sie dies in der Anwendung durchführen oder serverseitige Trigger oder gespeicherte Prozeduren für DocumentDB verwenden.

###Wann Sie verweisen sollten
Verwenden Sie in der Regel normalisierte Datenmodelle in den folgenden Fällen:

- Zur Darstellung von **1:n**-Beziehungen.
- Zur Darstellung von **m:n**-Beziehungen.
- Wenn sich zugehörige Daten **häufig ändern**.
- Wenn referenzierte Daten möglicherweise **unbegrenzt** sind.

> [AZURE.NOTE]Wenn eine Normalisierung in der Regel eine bessere **Schreibleistung** bietet.

###Wo erstelle ich die Beziehung?
Das Wachstum der Beziehung hilft bei der Bestimmung, in welchem Dokument der Verweis gespeichert werden sollte.

Sehen Sie sich den unten stehenden JSON-Code an, in dem Verleger und Bücher modelliert werden.

	Publisher document:
	{
	    "id": "mspress",
	    "name": "Microsoft Press",
	    "books": [ 1, 2, 3, ..., 100, ..., 1000]
	}

	Book documents:
	{"id": 1, "name": "DocumentDB 101" }
	{"id": 2, "name": "DocumentDB for RDBMS Users" }
	{"id": 3, "name": "Taking over the world one JSON doc at a time" }
	...
	{"id": 100, "name": "Learn about Azure DocumentDB" }
	...
	{"id": 1000, "name": "Deep Dive in to DocumentDB" }

Wenn die Anzahl der Bücher für jeden Verleger klein ist und nur über begrenztes Wachstum verfügt, kann es nützlich sein, den Buchverweis im Verlegerdokument zu speichern. Wenn die Anzahl der Bücher pro Verleger jedoch unbegrenzt ist, würde dieses Datenmodell zu veränderbaren, wachsenden Arrays führen, wie im obigen Verlegerbeispieldokument gezeigt.

Durch ein paar Änderungen entsteht ein Modell, das weiterhin die gleichen Daten darstellt, aber diese großen veränderbaren Sammlungen werden vermieden.

	Publisher document: 
	{
	    "id": "mspress",
	    "name": "Microsoft Press"
	}
	
	Book documents: 
	{"id": 1,"name": "DocumentDB 101", "pub-id": "mspress"}
	{"id": 2,"name": "DocumentDB for RDBMS Users", "pub-id": "mspress"}
	{"id": 3,"name": "Taking over the world one JSON doc at a time"}
	...
	{"id": 100,"name": "Learn about Azure DocumentDB", "pub-id": "mspress"}
	...
	{"id": 1000,"name": "Deep Dive in to DocumentDB", "pub-id": "mspress"}

Im Beispiel oben haben wir die unbegrenzte Auflistung im Verlegerdokument gelöscht. Stattdessen haben wir nur einen Verweis zum Verleger in jedem Buchdokument.

###Wie modelliere ich Viele-zu-viele-Beziehungen?
In einer relationalen Datenbank *m:n*-Beziehungen häufig mit Verknüpfungstabellen modelliert, bei denen einfach Datensätze aus anderen Tabellen miteinander verknüpft werden.

![Verknüpfen von Tabellen](./media/documentdb-modeling-data/join-table.png)

Möglicherweise sind Sie versucht, dasselbe mit Dokumenten zu replizieren, wobei jedoch ein Datenmodell entsteht, das etwa folgendermaßen aussieht.

	Author documents: 
	{"id": 1, "name": "Thomas Andersen" }
	{"id": 2, "name": "William Wakefield" }
	
	Book documents:
	{"id": 1, "name": "DocumentDB 101" }
	{"id": 2, "name": "DocumentDB for RDBMS Users" }
	{"id": 3, "name": "Taking over the world one JSON doc at a time" }
	{"id": 4, "name": "Learn about Azure DocumentDB" }
	{"id": 5, "name": "Deep Dive in to DocumentDB" }
	
	Joining documents: 
	{"authorId": 1, "bookId": 1 }
	{"authorId": 2, "bookId": 1 }
	{"authorId": 1, "bookId": 2 }
	{"authorId": 1, "bookId": 3 }

Das würde auch funktionieren. Wenn Sie jedoch einen Autor mit seinen Büchern oder ein Buch mit seinem Autor laden, sind immer mindestens zwei zusätzliche Abfragen für die Datenbank erforderlich. Eine Abfrage, um das Dokument zu verknüpfen, und dann eine weitere Abfrage zum Abrufen des tatsächlichen zu verknüpfenden Dokuments.

Wenn die Verknüpfungstabelle einfach nur zwei Informationen miteinander verbindet, dann könnte man sie auch einfach komplett löschen. Stellen Sie sich einmal Folgendes vor:

	Author documents:
	{"id": 1, "name": "Thomas Andersen", "books": [1, 2, 3]}
	{"id": 2, "name": "William Wakefield", "books": [1, 4]}
	
	Book documents: 
	{"id": 1, "name": "DocumentDB 101", "authors": [1, 2]}
	{"id": 2, "name": "DocumentDB for RDBMS Users", "authors": [1]}
	{"id": 3, "name": "Learn about Azure DocumentDB", "authors": [1]}
	{"id": 4, "name": "Deep Dive in to DocumentDB", "authors": [2]}

Es gibt einen Autor, bei dem ich sofort weiß, welche Bücher er geschrieben hat, oder ich habe ein Buchdokument geladen, für das ich die IDs der Autoren kenne. Dadurch ersparen Sie sich Zwischenabfragen der Verknüpfungstabelle, wodurch wiederum die Anzahl der Serverroundtrips Ihrer Anwendung reduziert wird.

##<a id="WrapUp"></a>Hybriddatenmodelle##
Wir haben jetzt gesehen wie Daten eingebettet (bzw. denormalisiert) werden und wie auf Daten verwiesen wird (bzw. wie sie normalisiert werden), und beide Modelle haben ihre Vor- und Nachteile.

Doch es muss nicht immer ein Entweder-oder geben. Trauen Sie sich, die Systeme etwas zu vermischen.

Basierend auf bestimmten Verwendungsmustern und Arbeitsauslastungen gibt es möglicherweise Fälle, in denen eine Mischung aus eingebetteten und referenzierten Daten sinnvoll sein kann und zu einer einfacheren Anwendungslogik mit weniger Serverroundtrips führen kann, wobei gleichzeitig eine gute Leistung gewahrt wird.

Betrachten Sie das folgende JSON-Beispiel.

	Author documents: 
	{
	    "id": 1,
	    "firstName": "Thomas",
	    "lastName": "Andersen",		
	    "countOfBooks": 3,
	 	"books": [1, 2, 3],
		"images": [
			{"thumbnail": "http://....png"}
			{"profile": "http://....png"}
			{"large": "http://....png"}
		]
	},
	{
	    "id": 2,
	    "firstName": "William",
	    "lastName": "Wakefield",
	    "countOfBooks": 1,
		"books": [1, 4, 5],
		"images": [
			{"thumbnail": "http://....png"}
		]
	}
	
	Book documents:
	{
		"id": 1,
		"name": "DocumentDB 101",
		"authors": [
			{"id": 1, "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
			{"id": 2, "name": "William Wakefield", "thumbnailUrl": "http://....png"}
		]
	},
	{
		"id": 2,
		"name": "DocumentDB for RDBMS Users",
		"authors": [
			{"id": 1, "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
		]
	}

Hier haben wir (hauptsächlich) das eingebettete Modell verwendet, bei dem Daten aus anderen Entitäten im übergeordneten Dokument eingebettet sind, in dem jedoch auf andere Daten verwiesen wird.

Wenn Sie das Buchdokument betrachten, sehen Sie ein paar interessante Felder, wenn wir das Array der Autoren betrachten. Es gibt ein *id*-Feld, bei dem es sich um das Feld handelt, das für einen Rückverweis auf ein Autorendokument verwendet wird, üblicherweise in einem normalisierten Modell, es gibt jedoch auch die Felder *Name* und *ThumbnailUrl*. Wir hätten auch nur bei dem Feld *id* bleiben können, sodass sich die Anwendung alle zusätzlichen Informationen aus dem jeweiligen Autorendokument mithilfe der „Verknüpfung“ abruft, aber da unsere Anwendung den Namen des Autors und ein Miniaturbild mit jedem angezeigten Buch anzeigt, können wir einen Roundtrip zum Server pro Buch in einer Liste speichern, indem Sie **einige** Daten vom Autor denormalisieren.

Wenn sich der Name des Autors ändert oder das Foto aktualisiert werden soll, müssten wir jedes Buch, das von ihm je veröffentlicht wurde, aktualisieren. Bei unserer Anwendung ist dies jedoch, basierend auf der Annahme, dass die Autoren ihre Namen nicht sehr oft ändern, eine akzeptable Entwurfsentscheidung.

Im Beispiel gibt es vorab **berechnete Aggregatwerte**, um sich die teure Verarbeitung eines Lesevorgangs zu ersparen. Im Beispiel werden einige der im Autorendokument eingebetteten Daten zur Laufzeit berechnet. Jedes Mal, wenn ein neues Buch veröffentlicht wird, wird ein Buchdokument erstellt, **und** das Feld „CountOfBooks“ wird auf einen berechneten Wert festgelegt, basierend auf der Anzahl der Buchdokumente, die für einen bestimmten Autor vorhanden sind. Diese Optimierung wäre in schreibintensiven Systemen gut, in denen wir uns Berechnungen für Schreibvorgänge leisten können, um Lesevorgänge zu optimieren.

Die Möglichkeit über ein Modell mit vorab berechneten Felder zu verfügen, wird dank der Unterstützung von **Transaktionen mit mehreren Dokumenten** durch DocumentDB ermöglicht. Viele NoSQL-Speicher können keine Transaktionen über Dokumente hinweg durchführen und bevorzugen aufgrund dieser Einschränkung Entwurfsentscheidungen, wie z. B. "Immer alles einbetten". Mit DocumentDB können Sie serverseitige Trigger oder gespeicherte Prozeduren verwenden, mit denen Bücher eingefügt und Autoren in einer einzigen ACID-Transaktion aktualisiert werden. Sie **müssen** nicht alles in ein Dokument einbetten, nur um sicherzustellen, dass Ihre Daten konsistent bleiben.

##<a name="NextSteps"></a>Nächste Schritte

Die wichtigsten Erkenntnisse dieses Artikels bestehen darin, dass die Datenmodellierung in einer schemafreien Welt genauso wichtig ist wie eh und je.

Es gibt genauso wenig einen einzigen Weg, um Daten auf einem Bildschirm darzustellen, wie es nur einen einzigen Weg gibt, Ihre Daten zu modellieren. Sie müssen Ihre Anwendung kennen und wissen, wie die Daten erstellt, verwendet und verarbeitet werden. Erst dann können Sie durch Anwenden von einigen der hier vorgestellten Richtlinien damit beginnen, ein Modell zu erstellen, das die unmittelbaren Anforderungen der Anwendung berücksichtigt. Wenn sich Ihre Anwendungen ändern müssen, können Sie die Flexibilität einer schemafreien Datenbank nutzen, um diese Änderung zu übernehmen und um das Datenmodell problemlos weiterzuentwickeln.

Weitere Informationen zu Azure DocumentDB finden Sie auf der [Dokumentationsseite](../../services/documentdb/) des Diensts.

Informationen zum Optimieren von Indizes in Azure DocumentDB finden Sie im Artikel über die [Indizierungsrichtlinien](documentdb-indexing-policies.md).

Informationen zur horizontalen Partitionierung („Sharding“) Ihrer Daten auf mehreren Partitionen finden Sie unter [Partitionieren von Daten in DocumentDB](documentdb-partition-data.md).

Anleitungen für die Datenmodellierung und das Sharding für mehrinstanzenfähige Anwendungen finden Sie unter [Skalieren einer mehrinstanzenfähigen Anwendung mit Azure DocumentDB](http://blogs.msdn.com/b/documentdb/archive/2014/12/03/scaling-a-multi-tenant-application-with-azure-documentdb.aspx).
 

<!---HONumber=Nov15_HO4-->