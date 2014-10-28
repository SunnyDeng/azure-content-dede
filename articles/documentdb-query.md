<properties title="Query with Azure DocumentDB" pageTitle="Query with DocumentDB | Azure" description="DocumentDB's SQL query language supports a subset of ANSI SQL grammar and adds document-oriented support. Queries are served through up-to-date indexes that don't require index management."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Abfrage mit Azure DocumentDB

## Motivation

Die SQL-Abfragesprache von DocumentDB unterstützt eine Teilmenge der ANSI SQL-Grammatik und fügt dokumentenorientierte Unterstützung in Form geschachtelter Datenstrukturen, Arrays und Objektkonstruktion hinzu. Abfragen werden effizient mit DocumentDB-Indizes bearbeitet, die automatisch für alle Dokumenteneigenschaften aktualisiert werden, ganz ohne die zusätzliche Belastung einer Indexverwaltung. Entwickler können auch mithilfe benutzerdefinierter Funktionen (User-defined Functions, UDF) die volle Leistungsfähigkeit von JavaScript in Abfragen nutzen.

## Warum SQL-Abfragen?

DocumentDB unterstützt SQL und stellt damit eine sehr einfache und zugängliche Schnittstelle für die Abfrage von Daten zur Verfügung. SQL ist als Sprache strukturell einfach, und doch leistungsfähig. Es wird von vielen Anwendungsentwicklern eingesetzt. DocumentDB ist sehr stark auf offene Plattformen und Standards wie HTTP, JSON und JavaScript zugeschnitten.
Jetzt werden auch SQL-Abfragen über die REST-API-, .NET-, Node.js- und JavaScript-Client-SKDs unterstützt. SQL-Abfragen werden außerdem in gespeicherten Prozeduren und Triggern in der serverseitigen JavaScript-API unterstützt. Neben SQL-Abfragen unterstützt DocumentDB auch solche über LINQ im .NET-SDK.

# Datentypen

In DocumentDB wird das Datenformat für Dokumente und Metadaten in JavaScript Object Notation (JSON) ausgedrückt. JSON ist aufgrund der steigenden Anzahl von JavaScript-Anwendungen weit verbreitet, auch wegen seiner Entwicklung hin zu einem plattformunabhängigen Datenaustauschformat. Da JSON selbsterklärend ist, eignetet es sich auch gut für schemafreie Datenbanken.

Die in DocumentDB unterstützten einfachen Datentypen sind dieselben wie in JSON. Es verfügt über ein einfaches Typensystem, das aus Folgendem besteht:

-   Zeichenfolgen
-   Zahlen (IEEE754, doppelte Genauigkeit)
-   Boolesche Werte – wahr und falsch
-   NULL

Komplexere Datentypen lassen sich sowohl in JSON und DocumentDB durch Erstellen geschachtelter Objekte mithilfe des Operators { } und Arrays mithilfe des Operators [ ] darstellen.

# Abfragegrammatik

Mithilfe der SQL-Sprache von DocumentDB können Benutzer Daten aus JSON-Dokument mit den **SELECT**-Anweisungen abrufen. Hier das Beispiel einer SQL-Anweisung für eine Dokumentensammlung, die Meldungen auf einem sozialen Netzwerk enthält.

     SELECT p.message, p.user.id AS user_id, p.tags[0] AS first_tag 
     FROM posts p 
     WHERE p.type = "Comment"

Die Ausgabe zu einer Abfrage ist ein Array von JSON-Dokumentenfragmenten, eines für jedes Dokument, das den festgelegten Einschränkungen entspricht.

    {
      "Documents": [
        {
            "message": "JSON rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        },
        {
            "message": "SQL rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        }
      ]
    }

Die vollständige formale Grammatik (BNF) für die SQL-Abfragesprache findet sich am Ende dieses Dokuments.

# Die FROM-Klausel

Die **FROM**-Klausel ist normalerweise der Name der aktuellen Sammlung, auf der die Abfrage ausgeführt wird. Zum Beispiel wählt dies alle Dokumente in der Sammlung namens „posts“ aus. Wenn der Name falsch ist, wird dem Benutzer ein Fehler zurückgegeben.

    SELECT * 
    FROM posts

Das Schlüsselwort **ROOT** kann anstelle einer namentlichen Referenz auf die Sammlung verwendet werden, da sich durch das SDK oder die REST-API Abfragen auf den Umfang einer spezifischen Sammlung ausrichten lassen.

    SELECT * 
    FROM ROOT

Für die Ausgabeeigenschaften von FROM lassen sich Aliase erstellen. Das ist dann nützlich, wenn mehrere FROM-Quellen mit **JOINs** verwendet werden. Das wird später behandelt.

    SELECT P.* 
    FROM posts P

Es ist auch möglich, von einer geschachtelten Abfrage aus abzufragen. Wenn das Dokument mit den Meldungen beispielsweise ein Unterdokument mit dem Benutzer enthält, der die Meldung erstellt hat, dann ist es möglich, mit der folgenden Syntax abzufragen.

    SELECT U.* 
    FROM posts.user U

Die Quelle der FROM-Anweisung kann auch ein Skalararray sein (sortierter Satz).

    SELECT E
    FROM ["documentdb", "json", "sql"] E

Es ist auch möglich, die FROM-Anweisung bei der Auswertung von Skalaranweisungen ganz wegzulassen.

`SELECT "documentdb"`

# Die WHERE-Klausel (Filtern)

Die SQL-Sprache von DocumentDB unterstützt eine optionale **WHERE**-Klausel, die die Ergebnissätze auf Grundlage der festgelegten Bedingungen filtert, die das Dokument erfüllen muss. Der Filterausdruck kann logische Vergleiche mit irgendeiner Eigenschaft oder einem geschachtelten Pfad im Dokument enthalten.

In Abfragen lässt sich beispielsweise eine beliebige Eigenschaft wie Nachricht verwendeten. Mithilfe des automatischen Index' von DocumentDB wird das Filtern effizient ausgeführt.

    SELECT * 
    FROM posts p 
    WHERE p.message = "DocumentDB now supports SQL!"

Geschachtelte Eigenschaften lassen sich mithilfe der punktierten Schreibweise filtern. Die Pfade können eine willkürliche Länge haben, abhängig vom Schema des Dokuments.

    SELECT * 
    FROM posts p 
    WHERE p.user.name = "Azure DocumentDB"

Einzelne Werte in Arrays lassen sich mithilfe des des Dereferenzierungsoperators referenzieren. Die Arrayentladung wird mithilfe des Schlüsselworts **IN** unterstützt (wird später erläutert).

    SELECT * 
    FROM posts p 
    WHERE p.views[0] = 100

Bereichsvergleiche (\>, \<. \>=, \<=, !=) werden für numerische Werte unterstützt. Zahlen lassen sich auch mit dem Ergebnis eines arithmetischen Ausdrucks vergleichen, wie unten dargestellt.

    SELECT * 
    FROM posts p 
    WHERE p.views[0] > 100 * 2

Logische Operatoren können mithilfe der Operatoren AND, OR und NOT kombinieren, ganz wie in normalem SQL. Sie werden effizient durch Schnittmengen der Indizes für die festgelegten Eigenschaften verarbeitet.

    SELECT * 
    FROM posts p 
    WHERE ((p.user.name = "Azure DocumentDB")
    OR (p.views[0] = 100 AND p.message = "DocumentDB now supports SQL!"))

## Umgang mit fehlenden und Null-Eigenschaften

NULL wird als Schlüsselwort unterstützt und Eigenschaften lassen sich mit NULL so wie mit anderen Skalaren vergleichen.

    SELECT * 
    FROM posts p 
    WHERE p.message IS NULL

Da sich das Schema für Dokumente schnell ändern oder im Vorfeld unbekannt sein kann, unterstützt die Sprache spezielle Operatoren, die im Hinblick auf fehlende Eigenschaften prüfen – durch den Operator ISUNDEFINED, vergleichbar mit **undefined** in JavaScript.

    SELECT * 
    FROM posts p 
    WHERE p.message IS UNDEFINED

> Hinweis: Vergleichbar mit JavaScript = undefined gibt immer „false“ zurück.

# Die SELECT-Klausel (Projektion)

Die **SELECT**-Klausel kann zum Extrahieren bestimmten Eigenschaft aus der Abfrage verwendet werden. Der Sternoperator (\*) gibt die vollständigen Dokumente aus, die der Abfrage entsprechen.

    SELECT * 
    FROM posts

In der SELECT-Klausel lassen sich festgelegte Felder extrahieren. Felder können atomar (Zeichenfolgen, Zahlen, Boolesche) oder komplex (Arrays, JSON-Objekte) sein. Komplexe Typen in der SELECT-Klausel wie „user“ im Beispiel unten geben das gesamte JSON-Objekt unter der Eigenschaft zurück. Eigenschaften in der SELECT-Klausel müssen eindeutige Namen oder Aliase haben.

    SELECT posts.id, posts.user
    FROM posts

> Hinweis: Da JSON nicht geordnet ist, werden die einzelnen Eigenschaften im Dokument nicht in einer speziellen Reihenfolge zurückgegeben.

> Hinweis: Feldnamen müssen explizit qualifiziert werden. Da Dokumente kein festes Schema haben, ist das für die Laufzeit der Abfrage erforderlich, damit eine korrekte Bindung möglich ist.

Eine alternative Möglichkeit des Zugriff auf Eigenschaften besteht in der Verwendung der unten dargestellten Dictionary-Suchsyntax:

    SELECT posts.id, posts["user"], posts["from"]
    FROM posts

> Hinweis: Die Dictionary-Suchsyntax sollte eingesetzt werden, um Eigenschaftsnamen vermeiden zu können, die reservierte Schlüsselwörter wie FROM im Beispiel oben verwenden.

## Auswertung von Ausdrücken

Skalarausdrücke können auch mit Ausdrücken verwendet werden. Wenn kein Name festgelegt wurde, wird ein automatisch generierter als Platzhalter verwendet ($1, $2, $3 usw.).

    SELECT ((2 + 11 % 7) – 2)/3
    FROM posts

Die folgenden Ausdrücke lassen sich innerhalb von SQL-Abfrageausdrücken verwenden. Die Operatoren ähneln von ihrer Art her stark SQL. So ist Gleich (=) eine starke Gleichheit, was heißt: 5 != „5“.

|--------------------------|--------------------------------------------------------|
| Arithmetische Operatoren | +, -, \*, /, % (Modulo)                                |
| Logische Operatoren      | AND, OR, NOT                                           |
| Bitweise Operatoren      | & (Bitweises AND), | (Bitweises OR), ^ (Bitweises XOR) |
| Zeichenfolgeoperatoren   | || (Verkettung)                                        |
| Vergleichsoperatoren     | =, !=, \>, \<, \>=, \<=                                |

## JSON-Transformation

SQL-Abfragen können JSON-Fragmente zurückgeben, nicht nur komplette Dokumente – das lässt sich mit dem Schlüsselwort **VALUE** durchführen. Zum Beispiel gibt das folgende Dokument „2“ statt {„id“: „2“}.

    SELECT VALUE posts.id
    FROM posts

Komplexere JSON-Ausdrücke lassen sich wie in JavaScript mithilfe der Operatoren {} und [] konstruieren. Damit können Abfragen flexibel die Form von Ergebnissätzen transformieren.

    SELECT {"user_name": p.user.name, "recent_views": [p.views[0], p.views[1]]}
    FROM posts p

> Hinweis: Werte können irgendein gültiger Ausdruck sein, doch müssen Bezeichnungen Literale (Zeichenfolgen) sein.

# Verknüpfungen und Iterationen

In Dokumentendatenbanken sind die referenzierten Daten als Unterdokumente oder in Arrays eingebettet. Deshalb unterstützt die SQL-Sprache dokumenteninterne Verknüpfungen mithilfe des Schlüsselworts **JOIN**.

    SELECT p.id, tag 
    FROM posts p 
    JOIN tag IN p.tags

> Hinweis: Verknüpfungen sind formal betrachtet Kreuzverknüpfungen; da jedoch die Daten eingebettet sind, funktionieren Sie als interne Verknüpfungen.

Im Beispiel wird der Operator **IN** für die Iteration über alle Elemente einer Quelle verwendet. Die Quelle für eine IN-Klausel kann entweder ein Array oder ein Objekt sein. Wird der Iterator in einem Objekt verwendet, dann geht er durch jede Eigenschaft. IN kann auch wie unten dargestellt direkt verwendet werden. IN kann auch mit Array-Literalen verwendet werden.

    SELECT tag 
    FROM tag IN posts.tags

Mehrere Verknüpfungen können innerhalb einer SQL-Anweisung wie unten dargestellt verwendet werden:

    SELECT p.id, tag, liked
    FROM posts p 
    JOIN tag IN p.tags
    JOIN liked IN p.likes  

# Benutzerdefinierte Funktionen (User Defined Functions, UDFs)

DocumentDB-Abfragen unterstützen Programmiererweiterungen in Form benutzerdefinierter JavaScript-Funktionen. Jede Sammlung kann eine oder mehrere gespeicherte **UDFs** haben, die JSON-Fragmente transformieren, und diese per Namen direkt in Skripten referenzieren.

Zum Beispiel lässt sich eine „regex\_match“-Funktion mithilfe der benutzerdefinierten Funktionen erstellen:

    function regex_match(input, pattern) {
        getContext().getResponse().setBody(pattern.test(input));
    }

Sie kann dann in Abfragen wie diesem Beispiel referenziert werden:

    SELECT *
    FROM posts p
    WHERE regex_match(p.message, "doc.*db") = true

> Hinweis: UDFs haben nur auf die Eingabeparameter Zugriff. DocumentDB-Schreibvorgänge (lesen, schreiben, abfragen usw.) werden in UDFs nicht unterstützt.

> Hinweis: Aktuell kann in einer Abfrage nur eine UDF verwendet werden.

UDFs können nicht nur Literale, sondern auch JSON-Fragmente verarbeiten und zurückgeben. Zum Beispiel kann eine „array\_count“-Funktion wie folgt definiert werden:

    function array_count(input) {
        getContext().getResponse().setBody(input.length);
    }

Und sie lässt sich dann in Abfragen dazu verwenden, die Größe eines Arrays in einem Dokument zu finden:

    SELECT *
    FROM posts p
    WHERE array_count(p.likes) > 5

UDFs lassen sich auch in der SELECT- oder FROM-Klausel einer Abfrage festlegen. Beispiel:

    SELECT array_count(p.likes) AS count
    FROM posts p

Weitere Details zum Erstellen und Verwalten von UDFS finden Sie in der Dokumentation zur JavaScript-Programmierunterstützung.

# Auslagerung

Jede Ausführung einer Abfrage gibt einen Batch an Ergebnissen entsprechend der vom Client konfigurierten Seitengröße zurück. Damit alle Ergebnisse einer Abfrage gelesen werden können, müssen Anwendungen über jeden Ergebnisssatz paginieren, bis es keine Daten mehr zu lesen gibt. Wenn Sie beispielsweise nicht mehr als 10 Dokumente lesen möchten, können Clients die Seitengröße auf 10 festlegen, sodass die Höchstzahl der zurückgegebenen Dokumenten eingeschränkt ist. Beachten Sie, dass Abfragen weniger Ergebnisse als die Seitengröße zurückgeben können, bei einigen Abfragen sogar kein einziges. Um alle Ergebnisse einer Abfrage lesen zu können, müssen Clients den nächsten Batch mithilfe des Antwort-Fortsetzungstokens abrufen, bis dieser leer ist.

    // Fetch pages of results up to 10 at a time. FeedOptions is optional
    DocumentServiceQuery<Database> query = (
        from db in client.CreateDatabaseQuery(new FeedOptions { MaxItemCount = 10 })
        where db.Name == dbName
        select db).AsDocumentServiceQuery();

    while (query.HasMoreResults)
    {
        databases.AddRange(await query.ExecuteNextAsync<Database>());
    }

Das ist in der SQL-Grammatik nicht als das Schlüsselwort TOP oder LIMIT implementiert, da die Funktionalität bereits durch die Fortsetzungtokens im zugrunde liegenden Client-SDK bzw. der -REST-API enthalten ist. Weitere Informationen und Beispiele finden Sie in der REST-API-Dokumentation für GET-Dokumente oder die ReadFeed-Methoden in den SDKs.

# Abfragekonsistenzverhalten

DocumentDB unterstützt vier vom Entwickler optimierbare Konsistenzebenen – Strong, Bounded Staleness, Session und Eventual. Abfragen bieten dieselben Garantien wie die Konsistenzebenen. Standardmäßig entsprechen die Ergebnisse einer Abfrage garantiert der Konsistenzebene, die vom Client angefordert wurde. Der Index von DocumentDB ist protokollstrukturiert und so gestaltet, dass er unabhängig von der Menge immer aktuell und mit den Daten konsistent bleibt. Wenn Dokument eingefügt oder aktualisiert werden, sind sie in den Abfrageergebnissen sofort verfügbar.

Für Anwendungen, die „Eventual“-Konsistenz benötigen, lässt sich die Indizierungrichtlinie auf die Verwendung der „Lazy“-Indizierung konfigurierten. In diesem Fall wird der auf „Eventual“-konsistente Weise aktualisiert, wann immer die Sammlung im Leerlauf ist. Zusammenfassend finden Sie hier die Tabelle mit Abfragekonsistenzverhalten mit den unterschiedlichen Datenbank-Kontokonfigurationen.

| Datenkonsistenz   | Indizierungsrichtlinien | Abfrageverhalten  |
|-------------------|-------------------------|-------------------|
| Strong            | Consistent              | Strong            |
| Bounded Staleness | Consistent              | Bounded Staleness |
| Session           | Consistent              | Session           |
| Eventual          | Consistent              | Eventual          |
| Strong            | Verzögert               | Eventual          |
| Bounded Staleness | Verzögert               | Eventual          |
| Session           | Verzögert               | Eventual          |
| Eventual          | Verzögert               | Eventual          |

Weitere Details finden Sie in der Dokumentation der Indizierungrichtlinie und -konfiguration.

# APIs und SDKs

Abfragen lassen sich mithilfe von REST-API, Client-SDKs und der serverseitigen JavaScript-API mithilfe der SQL-Grammatik ausführen.

## Abfrage mithilfe der REST-API

Anwendungen können Abfragen an Sammlung mithilfe der REST-API POSTen. Das Folgende muss in jeder Abfrage enthalten sein

-   Header x-ms-documentdb-isquery: „True“, damit angegeben wird, dass dies eine Abfrage ist
-   Header Content-Type: „application/sql“, damit angegeben wird, dass die SQL-Sprache verwendet wird
-   Body enthält die SELECT-Anweisung
    <!-- -->

    POST .../docs/executeQuery HTTP/1.1
    authorization: ...
    x-ms-continuation:
    x-ms-activity-id: 82342881-769e-4113-a662-a85c7617ed5b
    x-ms-date: Fri, 30 May 2014 22:46:13 GMT
    Match:
    x-docdb-resource-id: 9MEKcum9C2g=
    x-docdb-entity-id:
    x-ms-documentdb-isquery: True
    Cache-Control: no-cache
    x-ms-version: 2014-02-25
    User-Agent: Microsoft.Azure.Documents.Client/1.0.0.0
    Content-Type: application/sql
    Host: ...
    Content-Length: 59
    Expect: 100-continue

    SELECT b.title FROM books b WHERE b.title = 'War and Peace'

> Hinweis: Die Abfrage mit GET oder Abfragezeichenfolgen werden nicht unterstützt.

## Abfrage mithilfe des .NET-SDKs

Das .NET-SDK unterstützt Abfragen mithilfe der Methode CreateDocumentQuery, die SQL-Abfragen als Zeichenfolgen unterstützt. Die Ausgabe der Abfrage erfolgt über eine IQueryable-Schnittstelle, die mithilfe der clientseitigen LINQ verarbeitet werden kann.

    IQueryable<dynamic> results = client.CreateDocumentQuery(collectionId).AsSQL<dynamic>(
       "SELECT b.title FROM books b WHERE b.title = 'War and Peace'");

Zusätzliche Informationen finden Sie in der .NET-SDK-Dokumentation.

## Abfrage in gespeicherten Prozeduren und Triggern

Die serverseitige JavaScript-API für gespeicherte Prozeduren und Trigger unterstützt auch Abfragen mithilfe von SQL.

    collection.queryDocuments(collection.GetSelfLink(),
    "SELECT b.title FROM books b WHERE b.title = 'War and Peace'",
    callback);

Zusätzliche Informationen finden Sie in der serverseitigen JavaScript-SDK-Dokumentation.

# Anhang A: SQL-Syntax

Die folgenden Eisenbahndiagramme zeigen die formale SQL-Grammatik für die DocumentDB-Abfragesprache

![][]
![][1]
![][2]
![][3]
![][4]
![][5]
![][6]
![][7]
![][8]
![][9]
![][10]
![][11]
![][12]
![][13]
![][14]
![][15]
![][16]

  []: ./media/documentdb-query/query1.png
  [1]: ./media/documentdb-query/query2.png
  [2]: ./media/documentdb-query/query3.png
  [3]: ./media/documentdb-query/query4.png
  [4]: ./media/documentdb-query/query5.png
  [5]: ./media/documentdb-query/query6.png
  [6]: ./media/documentdb-query/query7.png
  [7]: ./media/documentdb-query/query8.png
  [8]: ./media/documentdb-query/query9.png
  [9]: ./media/documentdb-query/query10.png
  [10]: ./media/documentdb-query/query11.png
  [11]: ./media/documentdb-query/query12.png
  [12]: ./media/documentdb-query/query13.png
  [13]: ./media/documentdb-query/query14.png
  [14]: ./media/documentdb-query/query15.png
  [15]: ./media/documentdb-query/query16.png
  [16]: ./media/documentdb-query/query17.png
