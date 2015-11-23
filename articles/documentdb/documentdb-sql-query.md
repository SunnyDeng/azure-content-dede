<properties 
	pageTitle="SQL-Abfragen für eine DocumentDB-Datenbank – Abfragen mit SQL | Microsoft Azure" 
	description="Erfahren Sie, wie DocumentDB SQL-Abfragen über hierarchische JSON-Dokumente für die automatische Indizierung unterstützt. Erkunden Sie eine SQL-Abfrage-Datenbankumgebung, die wirklich schemafrei ist." 
	keywords="Abfragendatenbank, SQL-Abfragen, SQL-Abfrage, Structured Query Language, documentdb, Azure, Microsoft Azure"
	services="documentdb" 
	documentationCenter="" 
	authors="arramac" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/13/2015" 
	ms.author="arramac"/>

# SQL-Abfrage in DocumentDB
Microsoft Azure DocumentDB unterstützt Dokumentabfragen mit SQL (Structured Query Language) über hierarchische JSON-Dokumente. DocumentDB funktioniert ohne Schema. Die direkte Integration des JSON-Datenmodells in das Datenbankmodul ermöglicht eine automatische Indexierung von JSON-Dokumenten ohne explizite Schemas oder die Erstellung sekundärer Indizes.

Bei der Entwicklung der Abfragesprache für DocumentDB hatten wir zwei Hauptziele:

-	**SQL-Nutzung** – Wir wollten SQL nutzen, anstatt eine neue Abfragesprache zu erfinden. Immerhin ist SQL eine der meistverwendeten und beliebtesten Abfragesprachen. DocumentDB-SQL bietet ein formelles Programmiermodell zur Durchführung umfassender Abfragen für JSON-Dokumente.
-	**SQL-Erweiterung** – Als JSON-Dokumentdatenbank, die JavaScript direkt im Datenbankmodul ausführen kann, wollten wir das JavaScript-Programmiermodell als Fundament für unsere Abfragesprache verwenden. Die SQL von DocumentDB verwendet Typsystem, Ausdrucksauswertung und Funktionsaufrufe von JavaScript. Dies wiederum bietet ein natürliches Programmiermodell für relationale Projektionen, hierarchische Navigation in JSON-Dokumenten, Selbstverknüpfungen und Aufrufe komplett in JavaScript geschriebener benutzerdefinierter Funktionen (User Defined Function UDF) sowie weitere Features. 

Diese Funktionen sind unserer Ansicht nach der Schlüssel zur Minimierung der Reibung zwischen Anwendung und Datenbank und sind entscheidend für die Produktivität von Entwicklern.

Für den Beginn empfiehlt sich folgendes Video, in dem Aravind Ramachandran die Abfragefunktionen von DocumentDB zeigt. Testen Sie außerdem unseren [Query Playground](http://www.documentdb.com/sql/demo), in dem Sie DocumentDB ausprobieren und SQL-Abfragen für unser Dataset ausführen können.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

Kehren Sie anschließend zu diesem Artikel zurück, in dem wir zu Beginn einige einfache JSON-Dokumente und SQL-Befehle durchgehen.

## Erste Schritte mit SQL-Befehlen (Structured Query Language) in DocumentDB
Um die DocumentDB-SQL-Sprache in Aktion zu sehen, beginnen wir mit einigen einfachen JSON-Dokumenten und betrachten einige einfache Abfragen für diese Dokumente. Betrachten Sie diese beiden JSON-Dokumente über zwei Familien. Beachten Sie, dass wir in DocumentDB keine Schemas oder sekundäre Indizes explizit erstellen müssen. Wir fügen einfach die JSON-Dokumente in eine DocumentDB-Sammlung ein und führen anschließend Abfragen aus. Hier haben wir ein einfaches JSON-Dokument für die Familie Andersen mit Eltern, Kindern (und deren Haustieren), Adresse und Registrierungsinformationen. Das Dokument enthält Zeichenfolgen, Zahlen, boolesche Werte, Arrays und verschachtelte Eigenschaften.

**Dokument**

	{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}


Nun folgt ein zweites Dokument mit einem kleinen Unterschied: `givenName` und `familyName` werden anstelle von `firstName` und `lastName` verwendet.

**Dokument**

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	            "givenName": "Jesse", 
	            "gender": "female", "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	            "familyName": "Miller", 
	             "givenName": "Lisa", 
	             "gender": "female", 
	             "grade": 8 }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "creationDate": 1431620462,
	    "isRegistered": false
	}



Wir werden nun einige Abfragen an die Daten ausführen, um einige der Schlüsselaspekte von DocumentDB-SQL besser zu verstehen. Die folgende Abfrage gibt z. B. die Dokumente zurück, in denen das ID-Feld den Text `AndersenFamily` enthält. Da es sich um `SELECT *` handelt, ist die Rückgabe der Abfrage das komplette JSON-Dokument:

**Abfragen**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Ergebnisse**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}]


Betrachten wir nun einen Fall, in dem wir die JSON-Ausgabe nach unseren Wünschen formatieren möchten. Diese Abfrage projiziert ein neues JSON-Objekt mit zwei ausgewählten Feldern (Name und Stadt), wenn die Stadt in der Adresse denselben Namen wie der Staat hat. In diesem Fall stimmt "NY, NY" überein.

**Abfragen**

	SELECT {"Name":f.id, "City":f.address.city} AS Family 
	FROM Families f 
	WHERE f.address.city = f.address.state

**Ergebnisse**

	[{
	    "Family": {
	        "Name": "WakefieldFamily", 
	        "City": "NY"
	    }
	}]


Die nächste Abfrage gibt alle Vornamen von Kindern der Familie zurück, deren ID `WakefieldFamily` entspricht, und zwar geordnet nach dem Wohnort.

**Abfragen**

	SELECT c.givenName 
	FROM Families f 
	JOIN c IN f.children 
	WHERE f.id = 'WakefieldFamily'
	ORDER BY f.address.city ASC

**Ergebnisse**

	[
	  { "givenName": "Jesse" }, 
	  { "givenName": "Lisa"}
	]


Beachten Sie einige der bemerkenswerten Aspekte der DocumentDB-Abfragesprache, die wir in den bisherigen Beispielen gesehen haben:
 
-	Da DocumentDB-SQL mit JSON-Werten arbeitet, werden baumförmige Entitäten anstelle von Spalten und Zeilen verarbeitet. Daher können Sie auf Knoten in der Baumstruktur in beliebiger Tiefe verweisen, z. B. `Node1.Node2.Node3…..Nodem`, ähnlich wie relationale SQL mit einem zweiteiligen Verweis auf `<table>.<column>`.   
-	Die strukturierte Abfragesprache arbeitet mit schemalosen Daten. Daher muss das Typsystem dynamisch gebunden werden. Derselbe Ausdruck kann unterschiedliche Typen in unterschiedlichen Dokumenten ergeben. Das Ergebnis einer Abfrage ist ein gültiger JSON-Wert, aber nicht garantiert innerhalb eines festen Schemas.  
-	DocumentDB unterstützt nur strikte JSON-Dokumente. Typsystem und Ausdrücke sind also auf JSON-Typen beschränkt. Weitere Informationen finden Sie unter [JSON-Spezifikation](http://www.json.org/).  
-	Eine DocumentDB-Sammlung ist ein schemaloser Container mit JSON-Dokumenten. Die Beziehungen in Datenentitäten innerhalb und zwischen Dokumenten in einer Sammlung werden implizit durch Einschluss erfasst, und nicht durch Beziehungen von primären Schlüsseln und Fremdschlüsseln. Dieser Aspekt ist wichtig angesichts der später in diesem Artikel besprochenen dokumentinternen Verknüpfungen.

## DocumentDB-Indexierung

Bevor wir die DocumentDB-SQL-Grammatik besser kennenlernen, sollten Sie sich mit dem Indizierungsdesign in DocumentDB vertraut machen.

Datenbankindizes dienen zur Ausführung von Abfragen verschiedenster Arten und Formen mit minimaler Ressourcennutzung (CPU, E/A) sowie mit gutem Durchsatz und niedriger Latenz. Die Auswahl der richtigen Indizes für Datenbankabfragen erfordert oft viel Planungs- und Testaufwand. Dieser Ansatz ist eine Herausforderung für schemalose Datenbanken, in denen die Daten nicht einem strikten Schema folgen und sich laufend verändern.

Daher haben wir uns für die Entwicklung des Indexierungs-Untersystems von DocumentDB die folgenden Ziele gesetzt:

-	Indizierung von Dokumenten ohne Schema: Das Indizierungsuntersystem benötigt keine Schemainformationen und stellt keinerlei Annahmen über das Schema der Dokumente an. 

-	Unterstützung für effiziente, umfassende hierarchische und relationale Abfragen: Der Index unterstützt die DocumentDB-Abfragesprache auf effiziente Weise und bietet Unterstützung für hierarchische und relationale Projektionen.

-	Unterstützung für konsistente Abfragen auch bei steigendem Volumen an Schreibvorgängen: Bei Workloads mit hohem Schreibaufwand und konsistenten Abfragen wird der Index inkrementell, effizient und online aktualisiert, um mit dem hohen Aufkommen an Schreibvorgängen Schritt zu halten. Die konsistente Indexaktualisierung ist entscheidend, um Abfragen auf der Konsistenzebene zu bieten, mit der die Benutzer den Dokumentdienst konfiguriert haben.

-	Mehrinstanzenfähigkeit: Beim reservierungsbasierten Modell für mandantenübergreifende Ressourcenverwaltung werden Indexaktualisierungen innerhalb des Budgets der pro Replikat verfügbaren Systemressourcen (CPU, Speicher und E/A-Vorgänge pro Sekunde) durchgeführt.

-	Speichereffizienz: Aus Kostengründen ist der zusätzlich benötigte Speicherplatz für den Index beschränkt und vorhersagbar. Dies ist entscheidend, da Entwickler mit DocumentDB kostenbasierte Kompromisse zwischen Index-Mehraufwand und Abfrageleistung treffen können.

Beispiele zum Konfigurieren der Indexrichtlinie für Sammlungen finden Sie in den [DocumentDB-Beispielen](https://github.com/Azure/azure-documentdb-net) auf der MSDN-Website. Wenden wir uns nun den Details der DocumentDB-SQL-Grammatik zu.


## Grundlagen der DocumentDB-SQL-Abfrage
Jede Abfrage besteht aus einer SELECT-Klausel und optionalen FROM- und WHERE-Klauseln nach ANSI-SQL-Standards. Normalerweise wird in jeder Abfrage die jeweilige Quelle in der From-Klausel aufgelistet. Anschließend wird in der WHERE-Klausel ein Filter auf die Quelle angewendet, um eine Teilmenge der JSON-Dokumente zurückzugeben. Zuletzt wird die SELECT-Klausel verwendet, um die abgefragten JSON-Werte in die ausgewählte Liste zu projizieren.
    
    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]    


## Die FROM-Klausel
Die `FROM <from_specification>`-Klausel ist optional, es sei denn, die Quelle wird später in der Abfrage gefiltert oder projiziert. Mit dieser Klausel wird die Datenquelle angegeben, auf der die Abfrage operiert. Normalerweise dient die gesamte Sammlung als Quelle, stattdessen kann aber auch eine Teilmenge der Sammlung angegeben werden.

Abfragen wie `SELECT * FROM Families` geben an, dass die gesamte Families-Sammlung als Quelle dient, die durchlaufen werden soll. Der Sonderbezeichner "ROOT" kann anstelle des Sammlungsnamens verwendet werden, um die Sammlung darzustellen. Die folgende Liste enthält die Regeln, die pro Abfrage erzwungen werden:

- Die Sammlung kann Aliase enthalten, z. B. `SELECT f.id FROM Families AS f` oder einfach `SELECT f.id FROM Families f`. Hier ist `f` das Äquivalent zu `Families`. `AS` ist ein optionales Schlüsselwort als Alias für den Bezeichner.

-	Nach der Aliasverwendung kann die Originalquelle nicht mehr gebunden werden. `SELECT Families.id FROM Families f` ist beispielsweise syntaktisch ungültig, da der Bezeichner „Families“ nicht mehr aufgelöst werden kann.

-	Alle referenzierten Eigenschaften müssen vollqualifiziert sein. Dies ist ohne strikte Schemaverwendung notwendig, um mehrdeutige Bindungen zu vermeiden. Daher ist `SELECT id FROM Families f` syntaktisch ungültig, da die `id`-Eigenschaft nicht gebunden ist.
	
### Unterdokumente
Die Quelle kann auch auf eine kleinere Teilmenge reduziert werden. Wenn Sie z. B. nur eine Teilstruktur in jedem Dokument auflisten möchten, kann der Stammknoten der Teilstruktur zur Quelle werden, wie im folgenden Beispiel gezeigt.

**Abfragen**

	SELECT * 
	FROM Families.children

**Ergebnisse**

	[
	  [
	    {
	        "firstName": "Henriette Thaulow",
	        "gender": "female",
	        "grade": 5,
	        "pets": [
	          {
	              "givenName": "Fluffy"
	          }
	        ]
	    }
	  ],
	  [
	    {
	        "familyName": "Merriam",
	        "givenName": "Jesse",
	        "gender": "female",
	        "grade": 1
	    },
	    {
	        "familyName": "Miller",
	        "givenName": "Lisa",
	        "gender": "female",
	        "grade": 8
	    }
	  ]
	]

Das obige Beispiel verwendet ein Array als Quelle. Sie können jedoch auch ein Objekt als Quelle verwenden, wie im folgenden Beispiel gezeigt. Jeder gültige JSON-Wert (mit Ausnahme von "Undefined"), der in der Quelle gefunden werden kann, wird für die Integration in das Abfrageergebnis herangezogen. Familien ohne `address.state`-Wert werden aus dem Abfrageergebnis ausgeschlossen.

**Abfragen**

	SELECT * 
	FROM Families.address.state

**Ergebnisse**

	[
	  "WA", 
	  "NY"
	]


## WHERE-Klausel
Die WHERE-Klausel (**`WHERE <filter_condition>`**) ist optional. Sie gibt die Bedingungen an, die die in der Quelle angegebenen JSON-Dokumente erfüllen müssen, um als Teil des Ergebnisses zurückgegeben zu werden. Jedes JSON-Dokument muss die angegebenen Bedingungen erfüllen, um in das Ergebnis einbezogen zu werden. Die WHERE-Klausel wird von der Indexebene verwendet, um die absolut kleinste Teilmenge von Quelldokumenten zu bestimmen, die Teil des Ergebnisses sein können.

Die folgende Abfrage fordert Dokumente an, die eine „name“-Eigenschaft enthalten, deren Eigenschaftswert gleich `AndersenFamily` ist. Alle anderen Dokumente, die keine „name“-Eigenschaft aufweisen oder deren Wert nicht gleich `AndersenFamily` ist, werden ausgeschlossen.

**Abfragen**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Ergebnisse**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


Das vorstehende Beispiel zeigt eine einfache Gleichheitsabfrage. DocumentDB-SQL unterstützt außerdem eine Vielzahl skalarer Ausdrücke. Die am häufigsten verwendeten Ausdrücke sind binäre und unäre Ausdrücke. Eigenschaftsverweise aus dem JSON-Quellobjekt sind ebenfalls gültige Ausdrücke.

Die folgenden binären Operatoren werden momentan unterstützt und können in Abfragen verwendet werden. Dies wird in den folgenden Beispielen veranschaulicht: <table> <tr> <td>Arithmetisch</td> <td>+,-,*,/,%</td> </tr> <tr> <td>Bitweise</td> <td>|, &, ^, <<, >>, >>> (Nullauffüllung, Verschiebung nach rechts) </td> </tr> <tr> <td>Logisch</td> <td>AND, OR</td> </tr> <tr> <td>Vergleich</td> <td>=, !=, >, >=, <, <=, <></td> </tr> <tr> <td>Zeichenfolge</td> <td>|| (Verkettung)</td> </tr> </table>

Betrachten wir nun einige Abfragen mit binären Operatoren.

	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade % 2 = 1     -- matching grades == 5, 1
	
	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade ^ 4 = 1    -- matching grades == 5
	
	SELECT *
	FROM Families.children[0] c
	WHERE c.grade >= 5     -- matching grades == 5


Die unären Operatoren "+,-, ~ und NOT" werden ebenfalls unterstützt und können in Abfragen verwendet werden, wie im folgenden Beispiel gezeigt:

	SELECT *
	FROM Families.children[0] c
	WHERE NOT(c.grade = 5)  -- matching grades == 1
	
	SELECT *
	FROM Families.children[0] c
	WHERE (-c.grade = -5)  -- matching grades == 5



Neben binären und unären Operatoren sind auch Eigenschaftsverweise zulässig. Beispielsweise gibt `SELECT * FROM Families f WHERE f.isRegistered` das JSON-Dokument zurück, das die `isRegistered`-Eigenschaft enthält und dessen Eigenschaftswert gleich dem JSON-Wert `true` ist. Alle anderen Werte (false, null, Undefined, `<number>`, `<string>`, `<object>`, `<array>` usw.) führen dazu, dass das Quelldokument aus dem Ergebnis ausgeschlossen wird.

### Gleichheits- und Vergleichsoperatoren
Die folgende Tabelle zeigt die Ergebnisse für Gleichheitsvergleiche in DocumentDB-SQL zwischen den einzelnen JSON-Typen. <table style = "width:300px"> <tbody> <tr> <td valign="top"> <strong>Operator</strong> </td> <td valign="top"> <strong>Undefined</strong> </td> <td valign="top"> <strong>Null</strong> </td> <td valign="top"> <strong>Boolean</strong> </td> <td valign="top"> <strong>Number</strong> </td> <td valign="top"> <strong>String</strong> </td> <td valign="top"> <strong>Object</strong> </td> <td valign="top"> <strong>Array</strong> </td> </tr> <tr> <td valign="top"> <strong>Undefined<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>Null<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>Boolean<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>Number<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>String<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>Object<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>Array<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> </tr> </tbody> </table>

Für andere Vergleichsoperatoren wie >, >=, !=, < und <= gelten die folgenden Regeln:

-	Vergleiche zwischen zwei unterschiedlichen Typen ergeben Undefined.
-	Vergleiche zwischen zwei Objekten oder zwei Arrays ergeben Undefined.   

Wenn das Ergebnis eines skalaren Ausdrucks im Filter Undefined ist, wird das entsprechende Dokument aus dem Ergebnis ausgeschlossen, da Undefined logisch nicht gleich "true" ist.

### Schlüsselwort BETWEEN
Mit BETWEEN können Abfragen für Wertebereiche wie ANSI SQL ausgedrückt werden. BETWEEN kann für alle JSON-Grundtypen (Zahl, Zeichenfolge, Boolesch und NULL) verwendet werden.

Diese Abfrage gibt beispielsweise alle Familiendokumente zurück, in denen das erste Kind die Klassen 1-5 besucht.

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Anders als in ANSI-SQL, kann die BETWEEN-Klausel wie im folgenden Beispiel in der FROM-Klausel verwendet werden.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Für schnellere Ausführungszeiten von Abfragen müssen Sie eine Indexrichtlinie erstellen, die einen Bereichindextyp für alle in der BETWEEN-Klausel gefilterten numerischen Eigenschaften/Pfade verwendet.

Der Hauptunterschied zwischen BETWEEN in DocumentDB und ANSI SQL besteht darin, dass Bereichsabfragen für Eigenschaften gemischte Typen enthalten können. "grade" kann beispielsweise in einigen Dokumenten eine Zahl (5) sein, in anderen eine Zeichenfolge ("grade4"). In diesen Fällen gibt, wie in JavaScript, ein Vergleich zwischen zwei unterschiedlichen Typen "undefined" zurück, und das Dokument wird übersprungen.

### Logische Operatoren (AND, OR und NOT)
Logische Operatoren arbeiten mit booleschen Werten. Es folgt eine Liste der logischen Wahrheitstabellen für diese Operatoren.

OR|True|False|Undefined
---|---|---|---
True|True|True|True
False|True|False|Undefined
Undefined|True|Undefined|Undefined

AND|True|False|Undefined
---|---|---|---
True|True|False|Undefined
False|False|False|False
Undefined|Undefined|False|Undefined

NOT| |
---|---
True|False
False|True
Undefined|Undefined

### IN-Schlüsselwort
Mit dem Schlüsselwort „IN“ kann überprüft werden, ob ein angegebener Wert mit einem Wert in einer Liste übereinstimmt. Bei dieser Abfrage werden beispielsweise alle Familiendokumente zurückgegeben, bei denen die ID entweder „WakefieldFamily“ oder „AndersenFamily“ lautet.
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

In diesem Beispiel werden alle Dokumente zurückgegeben, in denen der Bundesstaat mit einem der angegebenen Werte übereinstimmt.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

IN entspricht dem Verketten mehrerer OR-Klauseln. Da es aber mit nur einem Index verarbeitet werden kann, unterstützt DocumentDB einen höheren [Grenzwert](documentdb-limits.md) für die Anzahl von Argumenten, die in einer IN-Klausel angegeben werden.

### Ternäre (?) und koaleszierte (??) Operatoren
Ternäre und koaleszierte Operatoren können ähnlich wie in den gängigen Programmiersprachen wie C# und JavaScript zum Erstellen von bedingten Ausdrücken verwendet werden.

Der ternäre (?) Operator kann beim schnellen Erstellen von neuen JSON-Eigenschaften sehr nützlich sein. Sie können nun z. B. Abfragen zum Klassifizieren von Klassenstufen in einem für Menschen lesbaren Format wie Anfänger/Fortgeschrittene/Profis wie unten dargestellt erstellen.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Sie können auch die Aufrufe vom Operator wie in der folgenden Abfrage verschachteln.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Wie bei anderen Abfrageoperatoren werden Dokumente mit fehlenden verweisenden Eigenschaften im bedingten Ausdruck oder Dokumente mit unterschiedlichen verglichenen Typen in den Abfrageergebnissen ausgeschlossen.

Der koaleszierte (??) Operator kann zur effizienten Prüfung auf das Vorkommen einer Eigenschaft ("Defined") in einem Dokument verwendet werden. Dies ist bei Abfragen für teilweise strukturiert oder gemischte Datentypen nützlich. Diese Abfrage gibt z. B. "LastName", falls vorhanden, oder "Surname", falls nicht vorhanden zurück.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### Eigenschaftenaccessor in Anführungszeichen
Sie können auf Eigenschaften auch zugreifen, indem Sie den Anführungszeichenoperator `[]` für Eigenschaften verwenden. Beispielsweise sind `SELECT c.grade` und `SELECT c["grade"]` gleichwertig. Diese Syntax ist hilfreich, wenn Sie eine Eigenschaft mit Escapezeichen versehen müssen, die Leerzeichen oder Sonderzeichen enthält oder zufällig den gleichen Namen wie ein SQL-Schlüsselwort oder ein reserviertes Wort hat.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## Die SELECT-Klausel
Die SELECT-Klausel (**`SELECT <select_list>`**) ist obligatorisch und gibt an, welche Werte die Abfrage zurückgeben soll, wie auch in ANSI-SQL. Die gefilterte Teilmenge aus den Quelldokumenten wird an die Projektionsphase übergeben, in der die angegebenen JSON-Werte abgerufen werden und ein neues JSON-Objekt für jede übergebene Eingabe erstellt wird.

Das folgende Beispiel zeigt eine typische SELECT-Abfrage:

**Abfrage**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Ergebnisse**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


### Verschachtelte Eigenschaften
Im folgenden Beispiel werden zwei verschachtelte Eigenschaften projiziert: `f.address.state` und `f.address.city`.

**Abfrage**

	SELECT f.address.state, f.address.city
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Ergebnisse**

	[{
	  "state": "WA", 
	  "city": "seattle"
	}]


Die Projektion unterstützt auch JSON-Ausdrücke, wie im folgenden Beispiel gezeigt.

**Abfrage**

	SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Ergebnisse**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle", 
	    "name": "AndersenFamily"
	  }
	}]


Beachten Sie die Rolle von `$1`. Die `SELECT`-Klausel muss ein JSON-Objekt erstellen. Da kein Schlüssel angegeben ist, verwenden wir implizite Argumentvariablennamen beginnend mit `$1`. Diese Abfrage gibt z. B. zwei implizite Argumentvariablen mit den Bezeichnungen `$1` und `$2` zurück.

**Abfrage**

	SELECT { "state": f.address.state, "city": f.address.city }, 
	       { "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Ergebnisse**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "$2": {
	    "name": "AndersenFamily"
	  }
	}]


### Aliase
Wir werden das obige Beispiel nun erweitern und explizite Aliase für Werte verwenden. AS ist das Schlüsselwort für die Aliasvergabe. Dieses Schlüsselwort ist optional, wie Sie bei der Projektion des zweiten Werts als `NameInfo` sehen.

Wenn eine Abfrage zwei Eigenschaften mit demselben Namen hat, müssen Aliase verwendet werden, um mindestens eine der Eigenschaften umzubenennen, sodass diese im projizierten Ergebnis eindeutig sind.

**Abfrage**

	SELECT 
	       { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
	       { "name": f.id } NameInfo
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Ergebnisse**

	[{
	  "AddressInfo": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "NameInfo": {
	    "name": "AndersenFamily"
	  }
	}]


### Skalare Ausdrücke
Neben Eigenschaftsverweisen unterstützt die SELECT-Klausel auch skalare Ausdrücke wie Konstanten, arithmetische Ausdrücke, logische Ausdrücke usw. Hier sehen Sie z. B. eine einfache „Hello World“-Abfrage.

**Abfrage**

	SELECT "Hello World"

**Ergebnisse**

	[{
	  "$1": "Hello World"
	}]


Hier ist ein komplexeres Beispiel mit einem skalaren Ausdruck:

**Abfrage**

	SELECT ((2 + 11 % 7)-2)/3	

**Ergebnisse**

	[{
	  "$1": 1.33333
	}]


Das Ergebnis des skalaren Ausdrucks im folgenden Beispiel ist ein Boolean-Wert.

**Abfrage**

	SELECT f.address.city = f.address.state AS AreFromSameCityState
	FROM Families f	

**Ergebnisse**

	[
	  {
	    "AreFromSameCityState": false
	  }, 
	  {
	    "AreFromSameCityState": true
	  }
	]


### Objekt- und Arrayerstellung
Eine weitere Schlüsselfunktion von DocumentDB-SQL ist die Objekt- und Arrayerstellung. Im vorherigen Beispiel haben wir ein neues JSON-Objekt erstellt. Auf dieselbe Weise können Sie Arrays erstellen, wie in den folgenden Beispielen gezeigt.

**Abfrage**

	SELECT [f.address.city, f.address.state] AS CityState 
	FROM Families f	

**Ergebnisse**

	[
	  {
	    "CityState": [
	      "seattle", 
	      "WA"
	    ]
	  }, 
	  {
	    "CityState": [
	      "NY", 
	      "NY"
	    ]
	  }
	]

### VALUE-Schlüsselwort
Mit dem **VALUE**-Schlüsselwort können Sie JSON-Werte zurückgeben. Die folgende Abfrage gibt z. B. den skalaren Wert `"Hello World"` anstelle von `{$1: "Hello World"}` zurück.

**Abfragen**

	SELECT VALUE "Hello World"

**Ergebnisse**

	[
	  "Hello World"
	]


Die folgende Abfrage gibt den JSON-Wert ohne die `"address"`-Bezeichnung in den Ergebnissen zurück.

**Abfrage**

	SELECT VALUE f.address
	FROM Families f	

**Ergebnisse**

	[
	  {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }, 
	  {
	    "state": "NY", 
	    "county": "Manhattan", 
	    "city": "NY"
	  }
	]

Das folgende Beispiel zeigt, wie Sie primitive JSON-Werte zurückgeben können (auf der Blattebene der JSON-Baumstruktur).

**Abfrage**

	SELECT VALUE f.address.state
	FROM Families f	

**Ergebnisse**

	[
	  "WA",
	  "NY"
	]


###* Operator
Der Sonderoperator (*) wird unterstützt, um das Dokument unverändert zu projizieren. Wenn dieser Operator verwendet wird, dürfen keine weiteren projizierten Felder existieren. Abfragen wie `SELECT * FROM Families f` sind z. B. gültig, während `SELECT VALUE * FROM Families f ` und `SELECT *, f.id FROM Families f ` nicht gültig sind.

**Abfrage**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Ergebnisse**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}]

## ORDER BY-Klausel
Wie bei ANSI-SQL auch, können Sie beim Abfragen eine optionale Order By-Klausel einbinden. Die Klausel kann ein optionales ASC/DESC-Argument enthalten, um die Reihenfolge anzugeben, in der Ergebnisse abgerufen werden sollen. Eine ausführlichere Betrachtung von Order By finden Sie unter [Exemplarische Vorgehensweise: DocumentDB und „Order By“](documentdb-orderby.md).

Dies ist beispielsweise eine Abfrage, mit der Familien sortiert nach dem Namen des Wohnorts abgerufen werden.

**Abfrage**

	SELECT f.id, f.address.city
	FROM Families f 
	ORDER BY f.address.city
	
**Ergebnisse**
	
	[
	  {
	    "id": "WakefieldFamily",
	    "city": "NY"
	  },
	  {
	    "id": "AndersenFamily",
	    "city": "Seattle"	
	  }
	]

Und mit dieser Abfrage werden Familien sortiert nach dem Erstellungsdatum abgerufen. Dieser Wert wird als Zahl gespeichert, der die „Epoche“ repräsentiert, also die seit dem 1. Januar 1970 verstrichene Zeit in Sekunden.

**Abfrage**

	SELECT f.id, f.creationDate
	FROM Families f 
	ORDER BY f.creationDate DESC
	
**Ergebnisse**
	
	[
	  {
	    "id": "WakefieldFamily",
	    "creationDate": 1431620462
	  },
	  {
	    "id": "AndersenFamily",
	    "creationDate": 1431620472	
	  }
	]
	
## Erweiterte Abfragekonzepte für SQL-Datenbanken
### Iteration
Es wurde ein neues Konstrukt mit dem **IN**-Schlüsselwort in DocumentDB-SQL eingeführt, mit dem JSON-Arrays durchlaufen werden können. Die FROM-Quelle bietet Unterstützung für Iterationen. Beginnen wir mit dem folgenden Beispiel:

**Abfrage**

	SELECT * 
	FROM Families.children

**Ergebnisse**

	[
	  [
	    {
	      "firstName": "Henriette Thaulow", 
	      "gender": "female", 
	      "grade": 5, 
	      "pets": [{ "givenName": "Fluffy"}]
	    }
	  ], 
	  [
	    {
	        "familyName": "Merriam", 
	        "givenName": "Jesse", 
	        "gender": "female", 
	        "grade": 1
	    }, 
	    {
	        "familyName": "Miller", 
	        "givenName": "Lisa", 
	        "gender": "female", 
	        "grade": 8
	    }
	  ]
	]

Betrachten wir nun eine andere Abfrage, die untergeordnete Elemente in der Sammlung durchläuft. Beachten Sie den Unterschied im Ausgabe-Array. Dieses Beispiel teilt `children` und fügt das Ergebnis in ein einzelnes Array ein.

**Abfrage**

	SELECT * 
	FROM c IN Families.children

**Ergebnisse**

	[
	  {
	      "firstName": "Henriette Thaulow",
	      "gender": "female",
	      "grade": 5,
	      "pets": [{ "givenName": "Fluffy" }]
	  },
	  {
	      "familyName": "Merriam",
	      "givenName": "Jesse",
	      "gender": "female",
	      "grade": 1
	  },
	  {
	      "familyName": "Miller",
	      "givenName": "Lisa",
	      "gender": "female",
	      "grade": 8
	  }
	]

Mit dieser Funktion können Sie über einzelne Einträge im Array filtern, wie im folgenden Beispiel gezeigt.

**Abfrage**

	SELECT c.givenName
	FROM c IN Families.children
	WHERE c.grade = 8

**Ergebnisse**

	[{
	  "givenName": "Lisa"
	}]

### Joins
Die Möglichkeit, Tabellen zu verknüpfen, ist sehr wichtig in relationalen Datenbanken. Es ist die logische Grundvoraussetzung für die Planung normalisierter Schemas. Im Gegensatz dazu arbeitet DocumentDB mit denormalisierten Datenmodellen schemafreier Dokumente. Dies ist das logische Äquivalent zu "Selbstverknüpfungen".

Die Sprache unterstützt die Syntax "<from_source1> JOIN <from_source2> JOIN... JOIN <from_sourceN>. Dabei wird ein Satz von **N**-Tupeln (Tupel mit **N** Werten) zurückgegeben. Jedes Tupel enthält Werte, die durch Iteration aller Sammlungsaliase über deren jeweilige Sätze entstanden sind. In anderen Worten, dies ist ein komplettes Kreuzungsprodukt der an der Verknüpfung beteiligten Sätze.

Die folgenden Beispiele veranschaulichen die Funktionsweise der JOIN-Klausel. Das Ergebnis im folgenden Beispiel ist leer, da das Kreuzprodukt der einzelnen Quelldokumente und einem leeren Satz leer ist.

**Abfrage**

	SELECT f.id
	FROM Families f
	JOIN f.NonExistent

**Ergebnisse**

	[{
	}]


Im folgenden Beispiel findet die Verknüpfung zwischen dem Dokumentstamm und dem Unterstamm `children` statt. Es handelt sich um ein Kreuzprodukt zweier JSON-Objekte. Die Tatsache, dass die untergeordnete Struktur ein Array ist, macht für die Verknüpfung keinen Unterschied, da wir auf einem einzigen Stammknoten arbeiten, der das untergeordnete Array darstellt. Das Ergebnis enthält also nur zwei Ergebnisse, da das Kreuzprodukt der einzelnen Dokumente mit dem Array jeweils genau ein Dokument ergibt.

**Abfrage**

	SELECT f.id
	FROM Families f
	JOIN f.children
 
**Ergebnisse**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]


Das folgende Beispiel zeigt eine gewöhnlichere Verknüpfung:

**Abfrage**

	SELECT f.id
	FROM Families f
	JOIN c IN f.children 

**Ergebnisse**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]



Beachten Sie zunächst, dass `from_source` für die **JOIN**-Klausel ein Iterator ist. Der Fluss verläuft in diesem Fall also wie folgt:

-	Alle untergeordneten Elemente **c** im Array erweitern.
-	Kreuzprodukt mit dem Stammknoten des Dokuments **f** mit den einzelnen untergeordneten Elementen **c** anwenden, die im ersten Schritt vereinfacht wurden.
-	Zuletzt wird die Namenseigenschaft des Stammobjekts **f** alleine projiziert. 

Das erste Dokument (`AndersenFamily`) enthält nur ein untergeordnetes Element. Daher enthält der Ergebnissatz für dieses Dokument auch nur ein einzelnes Objekt. Das zweite Dokument (`WakefieldFamily`) enthält zwei untergeordnete Elemente. Daher ergibt das Kreuzprodukt ein separates Objekt für jedes untergeordnete Element und das Ergebnis enthält zwei Objekte, je eines pro untergeordnetem Element im Dokument. Die Stammfelder sind in beiden Dokumenten gleich, wie Sie es bei einem Kreuzprodukt erwarten würden.

Der wahre Nutzen von JOIN ist die Bildung von Tupeln aus dem Kreuzprodukt auf eine Weise, die auf anderen Wegen schwer zu projizieren ist. Außerdem könnten Sie Filter auf die Kombination von Tupeln anwenden, mit denen Benutzer eine Bedingung auswählen können, die von der Gesamtheit der Tupel erfüllt sein muss, wie im folgenden Beispiel gezeigt.

**Abfrage**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
 
**Ergebnisse**

	[
	  {
	    "familyName": "AndersenFamily", 
	    "childFirstName": "Henriette Thaulow", 
	    "petName": "Fluffy"
	  }, 
	  {
	    "familyName": "WakefieldFamily", 
	    "childGivenName": "Jesse", 
	    "petName": "Goofy"
	  }, 
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]



Dieses Beispiel ist eine natürliche Erweiterung des vorherigen Beispiels und zeigt eine doppelte Verknüpfung. Sie sehen das Kreuzprodukt in Form des folgenden Pseudocodes.

	for-each(Family f in Families)
	{	
		for-each(Child c in f.children)
		{
			for-each(Pet p in c.pets)
			{
				return (Tuple(f.id AS familyName, 
	              c.givenName AS childGivenName, 
	              c.firstName AS childFirstName,
	              p.givenName AS petName));
			}
		}
	}

`AndersenFamily` hat ein Kind mit einem Haustier. Das Kreuzprodukt ergibt also eine Zeile (1*1*1) für diese Familie. WakefieldFamily hat dagegen zwei Kinder, von denen allerdings nur "Jesse" Haustiere hat. Jesse hat jedoch zwei Haustiere. Das Kreuzprodukt ergibt also "1*1*2 = 2" Zeilen für diese Familie.

Im nächsten Beispiel wird ein zusätzlicher Filter für `pet` verwendet. Damit werden alle Tupel ausgeschlossen, bei denen der Haustiername nicht "Shadow" ist. Wir können also Tupel aus Arrays bilden, beliebige Elemente der Tupel filtern und beliebige Kombinationen aus Elementen projizieren.

**Abfrage**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
	WHERE p.givenName = "Shadow"

**Ergebnisse**

	[
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]


## JavaScript-Integration
DocumentDB bietet ein Programmiermodell zur Ausführung JavaScript-basierter Anwendungslogik direkt auf die Sammlungen über gespeicherte Prozeduren und Trigger. Damit ist Folgendes möglich:

-	Transaktionale CRUD-Operationen und Abfragen auf Dokumente in einer Sammlung mit hoher Leistung dank der tiefen Integration der JavaScript-Laufzeit direkt im Datenbankmodul. 
-	Eine natürliche Modellierung von Kontrollfluss, Variablen-Bereichssteuerung, Zuweisung und Integration der Ausnahmebehandlung für Datenbanktransaktionen. Weitere Informationen zur DocumentDB-Unterstützung für die JavaScript-Integration finden Sie in der Dokumentation für serverseitige JavaScript-Programmierung.

###Benutzerdefinierte Funktionen (User Defined Functions UDFs)
Neben den bereits in diesem Artikel definierten Typen bietet DocumentDB-SQL auch Unterstützung für benutzerdefinierte Funktionen (UDF). Insbesondere skalare UDFs werden unterstützt, bei denen Entwickler null oder mehrere Argumente übergeben und ein einziges Argumentergebnis zurückerhalten können. Für dieser Argumente wird geprüft, ob es sich um gültige JSON-Werte handelt.

Die Grammatik von DocumentDB-SQL wurde erweitert und unterstützt benutzerdefinierte Anwendungslogik mithilfe dieser benutzerdefinierten Funktionen. UDFs können in DocumentDB registriert und anschließend als Teil einer SQL-Abfrage referenziert werden. UDFs dienen in der Tat ausschließlich dazu, in Abfragen aufgerufen zu werden. Als Nebeneffekt haben UDFs im Gegensatz zu den anderen JavaScript-Typen (gespeicherte Prozeduren und Trigger) keinen Zugriff auf das Kontextobjekt. Da Abfragen schreibgeschützt ausgeführt werden, können sie entweder auf primären oder auf sekundären Replikaten ausgeführt werden. Daher werden UDFs normalerweise im Gegensatz zu anderen JavaScript-Typen für die Ausführung auf sekundären Replikaten entwickelt.

Hier sehen Sie ein Beispiel für die Registrierung einer UDF in der DocumentDB-Datenbank, speziell unterhalb einer Dokumentensammlung.

   
	   UserDefinedFunction regexMatchUdf = new UserDefinedFunction
	   {
	       Id = "REGEX_MATCH",
	       Body = @"function (input, pattern) { 
	                   return input.match(pattern) !== null;
	               };",
	   };
	   
	   UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
	       collectionSelfLink/* link of the parent collection*/, 
	       regexMatchUdf).Result;  
                                                                             
Im vorherigen Beispiel wird eine UDF mit dem Namen `REGEX_MATCH` erstellt. Es werden die beiden JSON-Zeichenfolgenwerte `input` und `pattern` akzeptiert, und es wird überprüft, ob der erste Wert mit dem im zweiten Wert angegebenen Muster übereinstimmt. Hierfür wird die string.match()-JavaScript-Funktion verwendet.


Wir können diese UDF nun in einer Abfrage in einer Projektion verwenden. UDFs müssen mit dem Präfix "udf." qualifiziert werden (unter Berücksichtigung der Groß-/Kleinschreibung), wenn sie aus Abfragen aufgerufen werden.

>[AZURE.NOTE]Vor dem 17.3.2015 hat DocumentDB UDF-Aufrufe ohne das Präfix „udf.“, z. B. SELECT REGEX\_MATCH(), unterstützt. Dieses Aufrufmuster ist veraltet.

**Abfrage**

	SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
	FROM Families

**Ergebnisse**

	[
	  {
	    "$1": true
	  }, 
	  {
	    "$1": false
	  }
	]

Die UDF kann auch in einem Filter verwendet werden, wie im folgenden Beispiel gezeigt – ebenfalls mit dem Präfix "udf." qualifiziert:

**Abfrage**

	SELECT Families.id, Families.address.city
	FROM Families
	WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Ergebnisse**

	[{
	    "id": "AndersenFamily",
	    "city": "Seattle"
	}]


UDFs sind gültige skalare Ausdrücke und können sowohl in Projektionen als auch in Filtern verwendet werden.

Wir erweitern den Funktionsumfang von UDFs im folgenden Beispiel um konditionale Logik:

	   UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
	   {
	       Id = "SEALEVEL",
	       Body = @"function(city) {
	       		switch (city) {
	       		    case 'seattle':
	       		        return 520;
	       		    case 'NY':
	       		        return 410;
	       		    case 'Chicago':
	       		        return 673;
	       		    default:
	       		        return -1;
	                }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(collection.SelfLink, seaLevelUdf);
	
	
Hier sehen Sie ein Verwendungsbeispiel für die UDF.

**Abfrage**

	SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
	FROM Families f	

**Ergebnisse**

	 [
	  {
	    "city": "seattle", 
	    "seaLevel": 520
	  }, 
	  {
	    "city": "NY", 
	    "seaLevel": 410
	  }
	]


Wie im vorherigen Beispiel gezeigt, vereinen UDFs den Funktionsumfang der JavaScript-Sprache mit DocumentDB-SQL zu einer umfassenden, programmierbaren Schnittstelle für komplexe verfahrensorientierte und konditionale Logik mithilfe der Fähigkeiten der integrierten JavaScript-Laufzeit.

DocumentDB-SQL übergibt die Argumente an die UDFs für die einzelnen Quelldokumente für die aktuelle Phase (WHERE-Klausel oder SELECT-Klausel) der UDF-Ausführung. Das Ergebnis wird nahtlos in die Gesamt-Ausführungspipeline aufgenommen. Wenn die von den UDF-Parametern referenzierten Eigenschaften nicht im JSON-Wert verfügbar sind, wird der Parameter als "Undefined" betrachtet und der UDF-Aufruf daher komplett übersprungen. Wenn das Ergebnis der UDF "Undefined" ist, wird es ebenfalls nicht in das Ergebnis aufgenommen.

UDFs sind also hervorragende Werkzeuge, um komplexe Geschäftslogik als Teil der Abfragen zu integrieren.

### Operatorauswertung
DocumentDB ist eine JSON-Datenbank und enthält daher Parallelen zu JavaScript-Operatoren und deren Auswertungslogik. Obwohl DocumentDB versucht, die JavaScript-Logik für die JSON-Unterstützung zu erhalten, weicht die Auswertung von Operationen doch in manchen Fällen davon ab.

In der DocumentDB-SQL ist der Typ eines Werts im Gegensatz zu herkömmlichem SQL oft nicht bekannt, bevor der Wert tatsächlich aus der Datenbank abgerufen wird. Um effiziente Abfragen zu ermöglichen, haben die meisten Operatoren strikte Typanforderungen.

DocumentDB-SQL führt im Gegensatz zu JavaScript keine impliziten Konvertierungen durch. Eine Abfrage wie `SELECT * FROM Person p WHERE p.Age = 21` stimmt beispielsweise mit Dokumenten überein, die eine Age-Eigenschaft mit dem Wert 21 enthalten. Alle weiteren Dokumente, deren Age-Eigenschaft mit der Zeichenfolge "21" übereinstimmt, oder sonstige Variationen wie "021", "21,0", "0021", "00021" usw. werden nicht zurückgegeben. Bei JavaScript würden diese Zeichenfolgenwerte dagegen implizit in Zahlen umgewandelt (je nach Operator, z. B.: ==). Dieser Unterschied ist wichtig für die effiziente Indexierung in DocumentDB-SQL.

## Parametrisierte SQL-Abfragen
DocumentDB unterstützt Abfragen mit Parametern, die mit der bekannten @-Notation ausgedrückt werden. Parametrisiertes SQL bietet stabile Fehlerbehandlung und Schutz von Benutzereingaben, wodurch eine versehentliche Offenlegung von Daten durch SQL-Injektion verhindert wird.

Sie können z. B. eine Abfrage erstellen, die "last name" und "adress state" als Parameter verwendet, und sie dann für unterschiedliche "last name"- und "address state"-Werte auf Grundlage von Benutzereingaben ausführen.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Diese Anforderung kann dann an DocumentDB als parametrisierte JSON-Abfrage wie im Folgenden dargestellt gesendet werden.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

Parameterwerte können alle gültigen JSON-Werte sein (Zeichenfolgen, Zahlen, boolesche Werte, Null, Arrays oder verschachteltes JSON). Da DocumentDB über weniger Schemata verfügt, werden Parameter nicht für jeden Typ überprüft.

##Integrierte Funktionen
DocumentDB unterstützt auch eine Reihe von integrierten Funktionen für häufige Vorgänge, die wie benutzerdefinierte Funktionen (User Defined Functions, UDFs) in Abfragen verwendet werden können.

<table>
<tr>
<td>Mathematische Funktionen</td>	
<td>ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN und TAN</td>
</tr>
<tr>
<td>Funktionen für die Typprüfung</td>	
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED und IS_PRIMITIVE</td>
</tr>
<tr>
<td>Zeichenfolgenfunktionen</td>	
<td>CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING und UPPER</td>
</tr>
<tr>
<td>Arrayfunktionen</td>	
<td>ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH und ARRAY_SLICE</td>
</tr>
<tr>
<td>Räumliche Funktionen</td>	
<td>ST_DISTANCE, ST_WITHIN, ST_ISVALID und ST_ISVALIDDETAILED</td>
</tr>
</table>

Wenn Sie derzeit eine UDF verwenden, für die jetzt eine entsprechende integrierte Funktion verfügbar ist, sollten Sie diese integrierte Funktion verwenden, da sie schneller und effizienter ausgeführt werden kann.

### Mathematische Funktionen
Jede mathematische Funktion führt eine Berechnung durch, üblicherweise basierend auf Eingabewerten, die als Argument bereitgestellt werden, und gibt einen numerischen Wert zurück. Im Folgenden finden Sie eine Tabelle der unterstützten integrierten mathematischen Funktionen.

<table>
<tr>
<td><strong>Verwendung</strong></td>
<td><strong>Beschreibung</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td>	
<td>Gibt den absoluten (positiven) Wert des angegebenen numerischen Ausdrucks zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">CEILING (num_expr)</a></td>	
<td>Gibt den kleinsten ganzzahligen Wert zurück, der größer oder gleich dem angegebenen numerischen Ausdruck ist.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td>	
<td>Gibt den größten ganzzahligen Wert zurück, der kleiner oder gleich dem angegebenen numerischen Ausdruck ist.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td>	
<td>Gibt den Exponenten des angegebenen numerischen Ausdrucks zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [,base])</a></td>	
<td>Gibt den natürlichen Logarithmus des angegebenen numerischen Ausdrucks oder den Logarithmus basierend auf der angegebenen Basis zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td>	
<td>Gibt den dekadischen Logarithmuswert des angegebenen numerischen Ausdrucks zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ROUND (num_expr)</a></td>	
<td>Gibt einen numerischen Wert zurück, gerundet auf den nächsten ganzzahligen Wert.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">TRUNC (num_expr)</a></td>	
<td>Gibt einen numerischen Wert zurück, gekürzt auf den nächsten ganzzahligen Wert.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">SQRT (num_expr)</a></td>	
<td>Gibt die Quadratwurzel des angegebenen numerischen Ausdrucks zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">SQUARE (num_expr)</a></td>	
<td>Gibt das Quadrat des angegebenen numerischen Ausdrucks zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POWER (num_expr, num_expr)</a></td>	
<td>Gibt die Potenz des angegebenen numerischen Ausdrucks über den angegebenen Wert zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SIGN (num_expr)</a></td>	
<td>Gibt den Vorzeichenwert (-1, 0, 1) des angegebenen numerischen Ausdrucks zurück.</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ACOS (num_expr)</a></td>	
<td>Gibt den Winkel in „rad“ (Bogenmaß) zurück, dessen Kosinus der angegebene numerische Ausdruck ist. Wird auch als Arkuskosinus bezeichnet.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ASIN (num_expr)</a></td>	
<td>Gibt den Winkel in „rad“ (Bogenmaß) zurück, dessen Sinus der angegebene numerische Ausdruck ist. Wird auch als Arkussinus bezeichnet.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ATAN (num_expr)</a></td>	
<td>Gibt den Winkel in „rad“ (Bogenmaß) zurück, dessen Tangens der angegebene numerische Ausdruck ist. Wird auch als Arkustangens bezeichnet.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>	
<td>Gibt den Winkel in „rad“ (Bogenmaß) zwischen der positiven X-Achse und dem Strahl vom Ursprung zum Punkt (y, x) zurück, wobei x und y die Werte der beiden angegebenen float-Ausdrücke sind.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td>	
<td>Gibt den trigonometrischen Kosinus des angegebenen Winkels in „rad“ (Bogenmaß) im angegebenen Ausdruck zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td>	
<td>Gibt den trigonometrischen Kotangens des angegebenen Winkels in „rad“ (Bogenmaß) im angegebenen numerischen Ausdruck zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">DEGREES (num_expr)</a></td>	
<td>Gibt den entsprechenden Winkel in Grad für einen in „rad“ (Bogenmaß) angegebenen Winkel zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI ()</a></td>	
<td>Gibt den konstanten Wert von PI zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANS (num_expr)</a></td>	
<td>Gibt das Bogenmaß zurück, wenn ein numerischer Ausdruck in Grad eingegeben wird.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SIN (num_expr)</a></td>	
<td>Gibt den trigonometrischen Sinus des angegebenen Winkels in „rad“ (Bogenmaß) im angegebenen Ausdruck zurück.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td>	
<td>Gibt den Tangens des Eingabeausdrucks im angegebenen Ausdruck zurück.</td>
</tr>

</table>

Sie können nun beispielsweise Abfragen wie die folgende ausführen:

**Abfrage**

    SELECT VALUE ABS(-4)

**Ergebnisse**

    [4]

Der Hauptunterschied zwischen den DocumentDB-Funktionen und ANSI SQL besteht darin, dass die Funktionen sich gut für Daten ohne und mit gemischtem Schema einsetzen lassen. Wenn Sie beispielsweise ein Dokument haben, in dem die "Size"-Eigenschaft fehlt oder einen nicht numerischen Wert wie "unbekannt" aufweist, wird dieses Dokument übersprungen anstatt einen Fehler zurückzugeben.

### Funktionen für die Typprüfung
Mit den Funktionen für die Typprüfung können Sie den Typ eines Ausdrucks in SQL-Abfragen prüfen. Typprüfungsfunktionen können verwendet werden, um den Typ von Eigenschaften innerhalb von Dokumenten während der Übertragung zu ermitteln, wenn dieser variabel oder unbekannt ist. Im Folgenden finden Sie eine Tabelle der unterstützten integrierten Typprüfungsfunktionen.

<table>
<tr>
  <td><strong>Verwendung</strong></td>
  <td><strong>Beschreibung</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "Array" ist.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "boolesch" ist.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "Null" ist.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "Zahl" ist.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "JSON-Objekt" ist.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "Zeichenfolge" ist.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Gibt einen booleschen Wert zurück, um anzugeben, ob der Eigenschaft ein Wert zugewiesen wurde.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Gibt einen booleschen Wert zurück, um anzugeben, ob der Typ des Werts eine Zeichenfolge, Zahl, ein boolescher Wert oder Null ist.</td>
</tr>

</table>

Mithilfe dieser Funktionen können Sie nun Abfragen wie die folgende ausführen:

**Abfrage**

    SELECT VALUE IS_NUMBER(-4)

**Ergebnisse**

    [true]

### Zeichenfolgenfunktionen
Die folgenden Skalarfunktionen führen einen Vorgang für einen Zeichenfolgen-Eingabewert durch und geben eine Zeichenfolge, einen numerischen Wert oder einen booleschen Wert zurück. Diese Tabelle enthält integrierte Zeichenfolgenfunktionen:

Verwendung|Beschreibung
---|---
[LENGTH (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|Gibt die Anzahl der Zeichen im angegebenen Zeichenfolgenausdruck zurück.
[CONCAT (str\_expr, str\_expr [, str\_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|Gibt eine Zeichenfolge zurück, die das Ergebnis der Verkettung von zwei oder mehr Zeichenfolgenwerten darstellt.
[SUBSTRING (str\_expr, num\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|Gibt einen Teil eines Zeichenfolgenausdrucks zurück.
[STARTSWITH (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck mit dem zweiten beginnt.
[ENDSWITH (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck mit dem zweiten endet.
[CONTAINS (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck den zweiten enthält.
[INDEX\_OF (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|Gibt die Anfangsposition des ersten Vorkommens des zweiten Zeichenfolgenausdrucks innerhalb des ersten angegebenen Zeichenfolgenausdrucks zurück, oder -1, wenn die Zeichenfolge nicht gefunden wird.
[LEFT (str\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|Gibt den linken Teil einer Zeichenfolge mit der angegebenen Anzahl von Zeichen zurück.
[RIGHT (str\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|Gibt den rechten Teil einer Zeichenfolge mit der angegebenen Anzahl von Zeichen zurück.
[LTRIM (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|Gibt einen Zeichenfolgenausdruck zurück, nachdem vorangestellte Leerzeichen entfernt wurden.
[RTRIM (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|Gibt einen Zeichenfolgenausdruck zurück, nachdem alle nachgestellten Leerzeichen entfernt wurden.
[LOWER (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|Gibt eine Zeichenfolge zurück, nachdem Großbuchstaben in Kleinbuchstaben konvertiert wurden.
[UPPER (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|Gibt eine Zeichenfolge zurück, nachdem Kleinbuchstaben in Großbuchstaben konvertiert wurden.
[REPLACE (str\_expr, str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|Ersetzt alle Vorkommen eines angegebenen Zeichenfolgenwerts durch einen anderen Zeichenfolgenwert.
[REPLICATE (str\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|Wiederholt einen Zeichenfolgenwert mit der angegebenen Häufigkeit.
[REVERSE (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|Gibt einen Zeichenfolgenwert in umgekehrter Reihenfolge zurück.

Mithilfe dieser Funktionen können Sie nun Abfragen wie die folgende ausführen: Beispielsweise können Sie den Familiennamen wie folgt in Großbuchstaben zurückgeben:

**Abfrage**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Ergebnisse**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Oder Zeichenfolgen wie in diesem Beispiel verketten:

**Abfrage**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Ergebnisse**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Zeichenfolgenfunktionen können auch in der WHERE-Klausel zum Filtern von Ergebnissen verwendet werden, wie im folgenden Beispiel:

**Abfrage**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Ergebnisse**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### Arrayfunktionen
Die folgenden Skalarfunktionen führen einen Vorgang für einen Arrayeingabewert durch und geben einen numerischen Wert, booleschen Wert oder Arraywert zurück. Diese Tabelle enthält integrierte Arrayfunktionen:

Verwendung|Beschreibung
---|---
[ARRAY\_LENGTH (arr\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|Gibt die Anzahl der Elemente des angegebenen Arrayausdrucks zurück.
[ARRAY\_CONCAT (arr\_expr, arr\_expr [, arr\_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|Gibt ein Array zurück, das das Ergebnis der Verkettung von zwei oder mehr Arraywerten darstellt.
[ARRAY\_CONTAINS (arr\_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|Gibt einen booleschen Wert zurück, um anzugeben, ob das Array den angegebenen Wert enthält.
[ARRAY\_SLICE (arr\_expr, num\_expr [, num\_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|Gibt einen Teil eines Arrayausdrucks zurück.

Arrayfunktionen können verwendet werden, um Arrays in JSON zu bearbeiten. Mit dieser Abfrage werden beispielsweise alle Dokumente zurückgegeben, in denen ein Elternteil „Robin Wakefield“ ist.

**Abfrage**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Ergebnisse**

    [{
      "id": "WakefieldFamily"
    }]

Dies ist ein weiteres Beispiel für die Verwendung von ARRAY\_LENGTH. Hierbei wird die Anzahl der Kinder pro Familie abgerufen.

**Abfrage**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Ergebnisse**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

Dies sind alle Informationen zu den integrierten Funktionen und zur SQL-Grammatik für DocumentDB. Als Nächstes sehen wir uns an, wie LINQ-Abfragen funktionieren und mit der bisher beschriebenen Grammatik interagieren.

### Räumliche Funktionen

DocumentDB unterstützt die folgenden integrierten OGC-Funktionen (Open Geospatial Consortium ) für das Abfragen von Geodaten. Weitere Informationen zur Unterstützung von Geodaten in DocumentDB finden Sie unter [Arbeiten mit Geodaten in Azure DocumentDB](documentdb-geospatial.md).

<table>
<tr>
  <td><strong>Verwendung</strong></td>
  <td><strong>Beschreibung</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Gibt den Abstand zwischen den beiden GeoJSON-Punktausdrücken zurück.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Gibt einen booleschen Ausdruck zurück, der angibt, ob der im ersten Argument angegebene GeoJSON-Punkt sich innerhalb des GeoJSON-Polygons im zweiten Argument befindet.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Gibt einen booleschen Wert zurück, der angibt, ob der angegebene GeoJSON-Punkt- oder -Polygon-Ausdruck gültig ist.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Gibt einen JSON-Wert mit einem booleschen Wert zurück, wenn der angegebene GeoJSON-Punkt oder -Polygon-Ausdruck gültig ist. Falls ungültig, wird außerdem der Grund als Zeichenfolge zurückgegeben.</td>
</tr>
</table>

Räumliche Funktionen können verwendet werden, um Entfernungsabfragen auf räumliche Daten anzuwenden. Hier ist z. B. eine Abfrage, die alle Familiendokumente zurückgibt, die sich innerhalb von 30 km von der angegebenen Position befinden. Dazu wird die integrierte ST\_DISTANCE-Funktion verwendet.

**Abfrage**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Ergebnisse**

    [{
      "id": "WakefieldFamily"
    }]

Wenn Sie die räumliche Indizierung in Ihre Indizierungsrichtlinie einschließen, werden "Entfernungsabfragen" effizient über den Index beantwortet. Weitere Informationen zur räumliche Indizierung finden Sie im Abschnitt weiter unten. Wenn Sie keinen räumlichen Index für die angegebenen Pfade haben, können Sie dennoch raumbezogene Abfragen ausführen, indem Sie den `x-ms-documentdb-query-enable-scan`-Anforderungsheader mit auf „True“ festgelegtem Wert angeben. In .NET erfolgt dies durch Übergeben des optionalen **FeedOptions**-Arguments an Abfragen mit auf „True“ festgelegter [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery)-Einstellung.

ST\_WITHIN kann verwendet werden, um zu prüfen, ob ein Punkt innerhalb eines Polygons liegt. Polygone werden häufig verwendet, um Umgrenzungen wie Postleitzahlen, Staatsgrenzen oder natürliche Gebilde darzustellen. Wenn Sie wiederum die räumliche Indizierung in Ihre Indizierungsrichtlinie einschließen, werden Abfragen nach enthaltenen Elementen effizient über den Index beantwortet.

Polygonargumente in ST\_WITHIN dürfen nur einen einzigen Ring enthalten, d. h. die Polygone dürfen keine Löcher aufweisen. Überprüfen Sie die [DocumentDB-Grenzen](documentdb-limits.md) auf die maximale Anzahl von Punkten in einem Polygon, die für eine ST\_WITHIN-Abfrage zulässig ist.

**Abfrage**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
    	'type':'Polygon', 
    	'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Ergebnisse**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE]Wenn (ähnlich wie bei der Funktionsweise nicht übereinstimmender Typen in DocumentDB-Abfragen) der in einem der Argumente angegebene Standortwert falsch formatiert oder ungültig ist, wird dieser mit **undefiniert** ausgewertet. Die ausgewerteten Dokumente werden aus den Abfrageergebnissen entfernt. Wenn Ihre Abfrage keine Ergebnisse zurückgibt, führen Sie ST\_ISVALIDDETAILED aus, um herauszufinden, warum der räumliche Typ ungültig ist.

ST\_ISVALID und ST\_ISVALIDDETAILED können verwendet werden, um zu prüfen, ob ein räumliches Objekt gültig ist. Die folgende Abfrage untersucht z. B. die Gültigkeit eines Punkts mit einem Längengradwert außerhalb des Gültigkeitsbereichs (-132,8). ST\_ISVALID gibt nur einen booleschen Wert zurück. ST\_ISVALIDDETAILED gibt den booleschen Wert und eine Zeichenfolge mit dem Grund zurück, warum das Argument als ungültig eingestuft wird.

**Abfrage**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Ergebnisse**

    [{
      "$1": false
    }]

Diese Funktionen können auch verwendet werden, um Polygone zu überprüfen. Beispielsweise verwenden wir hier ST\_ISVALIDDETAILED, um ein Polygon zu überprüfen, das nicht geschlossen ist.

**Abfrage**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
    	[ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
    	]]})

**Ergebnisse**

    [{
       "$1": { 
      	  "valid": false, 
      	  "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
      	}
    }]
    
Dies sind alle Informationen zu den integrierten Funktionen und zur SQL-Grammatik für DocumentDB. Als Nächstes sehen wir uns an, wie LINQ-Abfragen funktionieren und mit der bisher beschriebenen Grammatik interagieren.

## LINQ zu DocumentDB-SQL
LINQ ist ein .NET-Programmiermodell, das Berechnungen als Abfragen auf Streams von Objekten darstellt. DocumentDB bietet eine clientseitige Bibliothek als Schnittstelle zu LINQ und erleichtert die Konvertierung zwischen JSON- und .NET-Objekten sowie die Zuordnung einer Teilmenge von LINQ-Abfragen zu DocumentDB-Abfragen.

Die folgende Abbildung zeigt die Architektur für die Unterstützung von LINQ-Abfragen in DocumentDB. Mit dem DocumentDB-Client können Entwickler ein **IQueryable**-Objekt erstellen, das den DocumentDB-Abfrageanbieter direkt abfragt, der die LINQ-Abfrage wiederum in eine DocumentDB-Abfrage übersetzt. Anschließend wird die Abfrage an den DocumentDB-Server übergeben, um einen Ergebnissatz im JSON-Format abzurufen. Die zurückgegebenen Ergebnisse werden clientseitig in einen Stream von .NET-Objekten deserialisiert.

![Architektur für die Unterstützung von LINQ-Abfragen in DocumentDB][1]
 


### .NET- und JSON-Zuordnung
Die Zuordnung zwischen .NET- und JSON-Objekten erfolgt auf natürliche Weise. Jedes Datenelementfeld wird einem JSON-Objekt zugeordnet. Dabei wird der Feldname in den "key"-Teil des Objekts übernommen, und der "value"-Teil wird rekursiv zur Wertkomponente des Objekts zugeordnet. Betrachten Sie das folgende Beispiel. Das erstellte Family-Objekt wird wie hier gezeigt zum JSON-Dokument zugeordnet. Umgekehrt wird das JSON-Dokument zurück zu einem .NET-Objekt zugeordnet.

**C#-Klasse**

	public class Family
	{
	    [JsonProperty(PropertyName="id")]
	    public string Id;
	    public Parent[] parents;
	    public Child[] children;
	    public bool isRegistered;
	};
	
	public struct Parent
	{
	    public string familyName;
	    public string givenName;
	};
	
	public class Child
	{
	    public string familyName;
	    public string givenName;
	    public string gender;
	    public int grade;
	    public List<Pet> pets;
	};
	
	public class Pet
	{
	    public string givenName;
	};
	
	public class Address
	{
	    public string state;
	    public string county;
	    public string city;
	};
	
	// Create a Family object.
	Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
	Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
	Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
	Pet pet = new Pet { givenName = "Fluffy" };
	Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
	Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	            "givenName": "Jesse", 
	            "gender": "female", 
	            "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	          "familyName": "Miller", 
	          "givenName": "Lisa", 
	          "gender": "female", 
	          "grade": 8 
	        }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	};



### LINQ zu SQL-Übersetzung
Der DocumentDB-Abfrageanbieter führt eine bestmögliche Zuordnung von LINQ-Abfragen in DocumentDB-SQL-Abfragen durch. Die folgende Beschreibung geht davon aus, das Sie mit den Grundsätzen von LINQ vertraut sind.

Für das Typsystem werden alle primitiven JSON-Typen unterstützt: numerische Typen, boolesche Typen, String und null. Andere JSON-Typen werden nicht unterstützt. Die folgenden skalaren Ausdrücke werden unterstützt.

-	Konstante Werte – dazu gehören konstante Werte der primitiven Datentypen zum Zeitpunkt der Ausführung der Abfrage.

-	Indexausdrücke auf Eigenschaften/Arrays – diese Ausdrücke beziehen sich auf die Eigenschaft eines Objekts oder eines Arrayelements.

		family.Id;
		family.children[0].familyName;
		family.children[0].grade;
		family.children[n].grade; //n is an int variable

-	Arithmetische Ausdrücke - dazu gehören arithmetische Ausdrücke auf numerische und boolesche Werte. Eine vollständige Liste finden Sie in der SQL-Dokumentation.

		2 * family.children[0].grade;
		x + y;

-	Zeichenfolgenvergleiche - dazu gehören Vergleiche von Zeichenfolgenwerten mit konstanten Zeichenfolgen.
 
		mother.familyName == "Smith";
		child.givenName == s; //s is a string variable

-	Erstellungsausdrücke für Objekte/Arrays - diese Ausdrücke geben ein Objekt mit einem Verbundwerttyp oder anonymen Typ bzw. ein Array solcher Objekte zurück. Diese Werte können verschachtelt sein.

		new Parent { familyName = "Smith", givenName = "Joe" };
		new { first = 1, second = 2 }; //an anonymous type with 2 fields              
		new int[] { 3, child.grade, 5 };

### SQL-Abfrageoperatoren
Die folgenden Beispiele zeigen, wie einige der Standard-LINQ-Abfrageoperatoren in DocumentDB-Abfragen übersetzt werden.

#### Select-Operator
Die Syntax ist `input.Select(x => f(x))`, wobei `f` ein skalarer Ausdruck ist.

**LINQ Lambda-Ausdruck**

	input.Select(family => family.parents[0].familyName);

**SQL**

	SELECT VALUE f.parents[0].familyName
	FROM Families f



**LINQ Lambda-Ausdruck**

	input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL**

	SELECT VALUE f.children[0].grade + c
	FROM Families f 



**LINQ Lambda-Ausdruck**

	input.Select(family => new
	{
	    name = family.children[0].familyName,
	    grade = family.children[0].grade + 3
	});


**SQL**

	SELECT VALUE {"name":f.children[0].familyName, 
	              "grade": f.children[0].grade + 3 }
	FROM Families f



#### SelectMany-Operator
Die Syntax ist `input.SelectMany(x => f(x))`, wobei `f` ein skalarer Ausdruck ist, der einen Sammlungstyp zurückgibt.

**LINQ-Lambdaausdruck**

	input.SelectMany(family => family.children);

**SQL**

	SELECT VALUE child
	FROM child IN Families.children



#### Where-Operator
Die Syntax ist `input.Where(x => f(x))`, wobei `f` ein skalarer Ausdruck ist, der einen booleschen Wert zurückgibt.

**LINQ Lambda-Ausdruck**

	input.Where(family=> family.parents[0].familyName == "Smith");

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith" 



**LINQ Lambda-Ausdruck**

	input.Where(
	    family => family.parents[0].familyName == "Smith" && 
	    family.children[0].grade < 3);

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"
	AND f.children[0].grade < 3


### Zusammengesetzte SQL-Abfragen
Die obigen Operatoren können zu komplexeren Abfragen zusammengesetzt werden. Da DocumentDB verschachtelte Sammlungen unterstützt, kann die Zusammensetzung entweder verkettet oder verschachtelt werden.

#### Verkettung 

Die Syntax ist `input(.|.SelectMany())(.Select()|.Where())*`. Eine verkettete Abfrage kann mit einer optionalen `SelectMany`-Abfrage beginnen, gefolgt von mehreren `Select`- oder `Where`-Operatoren.


**LINQ Lambda-Ausdruck**

	input.Select(family=>family.parents[0])
	    .Where(familyName == "Smith");

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"



**LINQ Lambda-Ausdruck**

	input.Where(family => family.children[0].grade > 3)
	    .Select(family => family.parents[0].familyName);

**SQL**

	SELECT VALUE f.parents[0].familyName
	FROM Families f
	WHERE f.children[0].grade > 3



**LINQ Lambda-Ausdruck**

	input.Select(family => new { grade=family.children[0].grade}).
	    Where(anon=> anon.grade < 3);
            
**SQL**

	SELECT *
	FROM Families f
	WHERE ({grade: f.children[0].grade}.grade > 3)



**LINQ Lambda-Ausdruck**

	input.SelectMany(family => family.parents)
	    .Where(parent => parents.familyName == "Smith");

**SQL**

	SELECT *
	FROM p IN Families.parents
	WHERE p.familyName = "Smith"



#### Verschachtelung

Die Syntax ist `input.SelectMany(x=>x.Q())`, wobei „Q“ ein `Select`-, `SelectMany`- oder `Where`-Operator ist.

In verschachtelten Abfragen wird die innere Abfrage auf jedes Element der äußeren Abfrage angewendet. Hierbei ist wichtig, dass die innere Abfrage auf die Felder der Elemente in der äußeren Abfrage wie bei Selbstverknüpfungen verweisen kann.

**LINQ Lambda-Ausdruck**

	input.SelectMany(family=> 
	    family.parents.Select(p => p.familyName));

**SQL**

	SELECT VALUE p.familyName
	FROM Families f
	JOIN p IN f.parents


**LINQ Lambda-Ausdruck**

	input.SelectMany(family => 
	    family.children.Where(child => child.familyName == "Jeff"));
            
**SQL**

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = "Jeff"



**LINQ Lambda-Ausdruck**
            
	input.SelectMany(family => family.children.Where(
	    child => child.familyName == family.parents[0].familyName));

**SQL**

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = f.parents[0].familyName


## Ausführen von SQL-Abfragen
DocumentDB stellt Ressourcen über eine REST-API zur Verfügung, die in jeder Sprache aufgerufen werden kann, die HTTP/HTTPS-Anfragen unterstützt. Zusätzlich bietet DocumentDB Programmierbibliotheken für einige beliebte Sprachen an wie .NET, Node.js, JavaScript und Python. Die REST-API und die verschiedenen Bibliotheken unterstützen allesamt SQL-Abfragen. Das .NET SDK unterstützt LINQ-Abfragen zusätzlich zu SQL.

Die folgenden Beispiele zeigen, wie Sie eine Abfrage erstellen und auf ein DocumentDB-Datenbankkonto ausführen können.

### REST-API
DocumentDB bietet ein RESTful-Programmiermodell über HTTP. Datenbankkonten können im Rahmen eines Azure-Abonnements erstellt werden. Das Ressourcenmodell von DocumentDB besteht aus einem Satz von Ressourcen in einem Datenbankkonto, die jeweils über einen logischen und beständigen URI adressiert werden können. Ein Ressourcensatz wird in diesem Dokument als Feed bezeichnet. Ein Datenbankkonto besteht aus einem Satz von Datenbanken, die je mehrere Sammlungen enthalten, die wiederum Dokumente, UDFs und andere Ressourcentypen enthalten.

Die Interaktion mit diesen Ressourcen erfolgt über die HTTP-Verben GET, PUT, POST und DELETE mit deren Standardinterpretation. Das POST-Verb dient zur Erstellung neuer Ressourcen, zur Ausführung gespeicherter Prozeduren oder zum Ausführen von DocumentDB-Abfragen. Abfragen werden immer als schreibgeschützte Operationen ohne Nebeneffekte ausgeführt.

Die folgenden Beispiele zeigen POST für eine DocumentDB-Abfrage an eine Sammlung, die die beiden bisher behandelten Beispieldokumente enthält. Die Abfrage enthält einen einfachen Filter auf die JSON-Eigenschaft name. Beachten Sie die Verwendung der `x-ms-documentdb-isquery`- und Content-Type: `application/query+json`-Header, um anzugeben, dass es sich bei diesem Vorgang um eine Abfrage handelt.


**Anforderung**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
	

**Ergebnisse**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
	x-ms-item-count: 1
	x-ms-request-charge: 0.32
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "id":"AndersenFamily",
	         "lastName":"Andersen",
	         "parents":[  
	            {  
	               "firstName":"Thomas"
	            },
	            {  
	               "firstName":"Mary Kay"
	            }
	         ],
	         "children":[  
	            {  
	               "firstName":"Henriette Thaulow",
	               "gender":"female",
	               "grade":5,
	               "pets":[  
	                  {  
	                     "givenName":"Fluffy"
	                  }
	               ]
	            }
	         ],
	         "address":{  
	            "state":"WA",
	            "county":"King",
	            "city":"seattle"
	         },
	         "_rid":"u1NXANcKogEcAAAAAAAAAA==",
	         "_ts":1407691744,
	         "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
	         "_etag":"00002b00-0000-0000-0000-53e7abe00000",
	         "_attachments":"_attachments\/"
	      }
	   ],
	   "count":1
	}


Das zweite Beispiel zeigt eine komplexere Abfrage, die mehrere Ergebnisse der Verknüpfung zurückgibt.

**Anforderung**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json
	
    {      
        "query": "SELECT 
				     f.id AS familyName, 
				     c.givenName AS childGivenName, 
				     c.firstName AS childFirstName, 
				     p.givenName AS petName 
				  FROM Families f 
				  JOIN c IN f.children 
				  JOIN p in c.pets",     
        "parameters": [] 
    }


**Ergebnisse**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
	x-ms-item-count: 1
	x-ms-request-charge: 7.84
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "familyName":"AndersenFamily",
	         "childFirstName":"Henriette Thaulow",
	         "petName":"Fluffy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Goofy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Shadow"
	      }
	   ],
	   "count":3
	}


Wenn die Abfrageergebnisse nicht auf eine einzelne Ergebnisseite passen, gibt die REST-API ein Fortsetzungstoken im `x-ms-continuation-token`-Antwortheader zurück. Clients können einzelne Ergebnisseiten abfragen, indem sie den Header in nachfolgenden Abfragen angeben. Die Anzahl der Ergebnisse pro Seite kann über den `x-ms-max-item-count`-number-Header gesteuert werden.

Für die Verwaltung der Datenkonsistenzrichtlinie für Abfragen verwenden Sie den `x-ms-consistency-level`-Header, wie in allen REST-API-Anfragen. Für die Sitzungskonsistenz muss außerdem der letzte `x-ms-session-token`-Cookie-Header in der Abfrageanforderung wiederholt werden. Die Indexierungsrichtlinie der abgefragten Sammlung kann die Konsistenz der Abfrageergebnisse ebenfalls beeinflussen. Mit den Standardeinstellungen für Indexierungsrichtlinien stimmt der Index einer Sammlung immer mit dem aktuellen Dokumentinhalt überein, und Abfragen liefern die Daten in der gewünschten Konsistenz zurück. Wenn eine verzögerte Indexierungsrichtlinie verwendet wird, kann es passieren, dass veraltete Ergebnisse zurückgegeben werden. Weitere Informationen finden Sie unter [DocumentDB-Konsistenzebenen][consistency-levels].

Wenn die konfigurierte Indexierungsrichtlinie der Sammlung die angegebene Abfrage nicht unterstützen kann, gibt der DocumentDB-Server "400 Bad Request" zurück. Dies wird z. B. für Bereichsabfragen auf Pfade zurückgegeben, die für Hash (Gleichheits)-Suchvorgänge konfiguriert sind, oder für Pfade, die ausdrücklich von der Indexierung ausgeschlossen wurden. Mit dem `x-ms-documentdb-query-enable-scan`-Header kann angegeben werden, dass eine Abfrage einen Scan durchführen darf, wenn kein Index verfügbar ist.

### C# (.NET) SDK
Das .NET SDK unterstützt Abfragen per LINQ und SQL. Das folgende Beispiel zeigt, wie Sie die weiter oben in diesem Dokument gezeigte einfache Filterabfrage ausführen können.


	foreach (var family in client.CreateDocumentQuery(collectionLink, 
	    "SELECT * FROM Families f WHERE f.id = "AndersenFamily""))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

	foreach (var family in (
	    from f in client.CreateDocumentQuery(collectionLink)
	    where f.Id == "AndersenFamily"
	    select f))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in client.CreateDocumentQuery(collectionLink)
	    .Where(f => f.Id == "AndersenFamily")
	    .Select(f => f))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


Das Beispiel vergleicht zwei Eigenschaften auf Gleichheit in den einzelnen Dokumenten und verwendet anonyme Projektionen.


	foreach (var family in client.CreateDocumentQuery(collectionLink,
	    @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
	    FROM Families f 
	    WHERE f.address.city = f.address.state"))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
	foreach (var family in (
	    from f in client.CreateDocumentQuery<Family>(collectionLink)
	    where f.address.city == f.address.state
	    select new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .Where(f => f.address.city == f.address.state)
	    .Select(f => new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


Das nächste Beispiel zeigt Verknüpfungen mithilfe von LINQ-SelectMany.


	foreach (var pet in client.CreateDocumentQuery(collectionLink,
	      @"SELECT p
	        FROM Families f 
	             JOIN c IN f.children 
	             JOIN p in c.pets 
	        WHERE p.givenName = ""Shadow"""))
	{
	    Console.WriteLine("\tRead {0} from SQL", pet);
	}
	
	// Equivalent in Lambda expressions
	foreach (var pet in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .SelectMany(f => f.children)
	    .SelectMany(c => c.pets)
	    .Where(p => p.givenName == "Shadow"))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", pet);
	}



Der .NET-Client durchläuft automatisch alle Seiten der Abfrageergebnisse in den foreach-Blöcken, wie oben gezeigt. Die im Abschnitt zur REST-API vorgestellten Abfrageoptionen sind auch im .NET SDK über die Klassen `FeedOptions` und `FeedResponse` in der CreateDocumentQuery-Methode verfügbar. Die Anzahl der Ergebnisse pro Seite kann über die `MaxItemCount`-Einstellung gesteuert werden.

Entwickler können die Seitenaufteilung steuern, indem sie ein `IDocumentQueryable`-Element mit dem `IQueryable`-Objekt erstellen und anschließend die ` ResponseContinuationToken`-Werte auslesen und als `RequestContinuationToken` in `FeedOptions` zurückgeben. `EnableScanInQuery` kann festgelegt werden, um Suchen zu aktivieren, wenn die Abfrage nicht von der konfigurierten Indizierungsrichtlinie unterstützt werden kann.

Weitere Beispiele, die Abfragen enthalten, finden Sie unter [DocumentDB .NET-Beispiele](https://github.com/Azure/azure-documentdb-net).

### Serverseitige JavaScript-API 
DocumentDB bietet ein Programmiermodell zur Ausführung JavaScript-basierter Anwendungslogik direkt auf die Sammlungen über gespeicherte Prozeduren und Trigger. Die auf Sammlungsebene registrierte JavaScript-Logik kann anschließend Datenbankoperationen auf die Dokumente der jeweiligen Sammlung ausführen. Diese Operationen werden in ACID-Transaktionen der jeweiligen Umgebung gekapselt.

Das folgende Beispiel zeigt, wie Sie mithilfe von "queryDocuments" in der serverseitigen JavaScript-API Abfragen aus gespeicherten Prozeduren und Triggers heraus ausführen können.


	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();
	    var collectionLink = collectionManager.getSelfLink()
	
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        { name: name, author: author },
	        function (err, documentCreated) {
	            if (err) throw new Error(err.message);
	
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function (err, matchingDocuments) {
	                    if (err) throw new Error(err.message);
	context.getResponse().setBody(matchingDocuments.length);
	
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don't need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);
	                    }
	                })
	        });
	}


##Referenzen
1.	[Einführung in Azure DocumentDB][introduction]
2.	[DocumentDB-SQL-Spezifikation](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.	[DocumentDB .NET-Beispiele](https://github.com/Azure/azure-documentdb-net)
4.	[DocumentDB-Konsistenzebenen][consistency-levels]
5.	ANSI SQL 2011 [http://www.iso.org/iso/iso\_catalogue/catalogue\_tc/catalogue\_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.	JSON [http://json.org/](http://json.org/)
7.	JavaScript-Spezifikation [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.	LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.	Abfrageauswertungstechniken für große Datenbanken [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10.	Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11.	Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12.	Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13.     G. Graefe. The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 

<!---HONumber=Nov15_HO3-->