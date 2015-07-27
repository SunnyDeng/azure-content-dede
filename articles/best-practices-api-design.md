<properties
   pageTitle="Leitfaden zum API-Design | Microsoft Azure"
   description="Leitfaden zum Erstellen einer gut konzipierten API"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Leitfaden zum API-Design

![](media/best-practices-api-design/pnp-logo.png)

Einige Themen in diesem Leitfaden werden noch diskutiert und können sich unter Umständen ändern. Wir freuen uns auf Ihr Feedback!


## Übersicht

Zahlreiche moderne webbasierte Lösungen nutzen auf Webservern gehostete Webdienste, um Funktionen für Remoteclientanwendungen bereitzustellen. Die von einem Webdienst verfügbar gemachten Vorgänge bilden eine Web-API. Eine gut entworfene Web-API sollte Folgendes unterstützen:

- **Unabhängigkeit von der Plattform**. Clientanwendungen sollten die vom Webdienst bereitgestellte API nutzen können, ohne Anforderungen an die physische Implementierung der von der API bereitgestellten Daten oder Vorgänge zu stellen. Dazu muss die API allgemeine Standards einhalten, die es einer Clientanwendung und einem Webdienst ermöglichen, sich bezüglich der zu verwendenden Datenformate und der zwischen den Clientanwendungen und dem Webdienst ausgetauschten Datenstrukturen zu verständigen.

- **Serviceentwicklung**. Der Webdienst sollte weiterentwickelt werden können, und Funktionen sollten sich unabhängig von Client-Anwendungen hinzufügen (oder entfernen) lassen. Wenn sich die vom Webdienst bereitgestellten Funktionen ändern, sollten vorhandene Clientanwendungen weiterhin unverändert ausgeführt werden können. Außerdem sollten alle Funktionen auffindbar sein, damit Clientanwendungen sie vollständig nutzen können.

Dieser Leitfaden enthält Informationen zu den Problemen, die beim Entwerfen einer Web-API zu berücksichtigen sind.

## Einführung in Representational State Transfer (REST)

Roy Fielding schlug in seiner Dissertation im Jahr 2000 als Alternative zur Strukturierung der von Webdiensten verfügbar gemachten Vorgänge einen weiteren Architekturansatz vor: REST. REST ist ein Architekturstil zum Erstellen verteilter Systeme, die auf Hypermedia basieren. Ein wichtiger Vorteil des REST-Modells besteht darin, dass es auf offenen Standards basiert und die Implementierung des Modells oder der Clientanwendungen, die darauf zugreifen, nicht an eine bestimmte Implementierung bindet. Beispielsweise könnte ein REST-Webdienst mithilfe der Microsoft ASP.NET-Web-API implementiert werden, und Clientanwendungen könnten mit allen Sprachen und Toolsets entwickelt werden, die HTTP-Anforderungen generieren und HTTP-Antworten analysieren können.

> [AZURE.NOTE]: REST tatsächlich unabhängig von zugrundeliegenden Protokollen und nicht unbedingt an HTTP gebunden. Dennoch verwenden die meisten gängigen Implementierungen von Systemen, die auf REST basieren, HTTP als Anwendungsprotokoll für das Senden und Empfangen von Anforderungen. Dieses Dokument behandelt insbesondere die Zuordnung von REST-Prinzipien an Systeme, die mit HTTP betrieben werden.

Das REST-Modell verwendet ein Navigationsschema zur Darstellung von Objekten und Diensten über ein Netzwerk (als _Ressourcen_ bezeichnet). Viele Systeme, die REST implementieren, verwenden in der Regel das HTTP-Protokoll zum Übertragen von Anforderungen für den Zugriff auf diese Ressourcen. In diesen Systemen sendet eine Clientanwendung eine Anforderung in Form eines URI, der eine Ressource kennzeichnet, und einer HTTP-Methode (meist GET, POST, PUT oder DELETE), die angibt, welcher Vorgang für die Ressource ausgeführt wird. Der Hauptteil der HTTP-Anforderung enthält die zum Ausführen des Vorgangs erforderlichen Daten. Wichtig zu verstehen ist, dass REST ein zustandsloses Anforderungsmodell definiert. HTTP-Anforderungen sollten unabhängig sein und können in beliebiger Reihenfolge erfolgen, sodass der Versuch, Übergangsstatusinformationen zwischen Anforderungen beizubehalten, nicht möglich ist. Informationen werden ausschließlich in den Ressourcen selbst gespeichert, und jede Anforderung sollte eine atomare Operation sein. Ein REST-Modell implementiert im Grunde einen finiten Statuscomputer, wobei eine Anforderung eine Ressource von einem klar definierten, nicht flüchtigen Zustand in einen anderen Zustand versetzt.

> [AZURE.NOTE]Die Zustandslosigkeit einzelner Anforderungen im REST-Modell sorgt dafür, dass ein anhand dieser Prinzipien erstelltes System hochgradig skalierbar ist. Zwischen einer Clientanwendung, die eine Reihe von Anforderungen erstellt, und den bestimmten Webservern, die diese behandeln, muss keine Affinität beibehalten werden.

Ein weiterer wichtiger Aspekt beim Implementieren eines effektiven REST-Modells ist das Verstehen der Beziehungen zwischen den verschiedenen Ressourcen, auf welche das Modell Zugriff bietet. Diese Ressourcen sind in der Regel als Auflistungen und Beziehungen organisiert. Angenommen, die schnelle Analyse eines e-Commerce-Systems zeigt, dass zwei Auflistungen vorhanden sind, an denen Clientanwendungen wahrscheinlich interessiert sind: Bestellungen und Kunden. Für alle Bestellungen und Kunden sollten eigene eindeutige Schlüssel zu Identifikationszwecken vorhanden sein. Der URI zum Zugriff auf die Auflistung der Bestellungen kann einfach sein, z. B. _/Bestellungen_, und der URI für den Abruf aller Kunden könnte entsprechend _/Kunden_ lauten. Das Ausgeben einer HTTP GET-Anforderung an den URI _/Bestellungen_ sollte eine Liste zurückgeben, die alle als HTTP-Antwort codierten Bestellungen in der Auflistung zurückgibt:

```HTTP
GET http://adventure-works.com/orders HTTP/1.1
...
```

Die unten gezeigte Antwort codiert die Bestellungen als XML-Listenstruktur. Die Liste enthält 7 Bestellungen:

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
<OrderList xmlns:i="..." xmlns="..."><Order><OrderID>1</OrderID><OrderValue>99.90</OrderValue><ProductID>1</ProductID><Quantity>1</Quantity></Order><Order><OrderID>2</OrderID><OrderValue>10.00</OrderValue><ProductID>4</ProductID><Quantity>2</Quantity></Order><Order><OrderID>3</OrderID><OrderValue>16.60</OrderValue><ProductID>2</ProductID><Quantity>4</Quantity></Order><Order><OrderID>4</OrderID><OrderValue>25.90</OrderValue><ProductID>3</ProductID><Quantity>1</Quantity></Order><Order><OrderID>7</OrderID><OrderValue>99.90</OrderValue><ProductID>1</ProductID><Quantity>1</Quantity></Order></OrderList>
```
Zum Abrufen einer einzelnen Bestellung muss der Bezeichner für die Bestellung aus der Ressource _Bestellungen_ angegeben werden, z. B. _/Bestellungen/2_:

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
```

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
<Order xmlns:i="..." xmlns="...">
<OrderID>2</OrderID><OrderValue>10.00</OrderValue><ProductID>4</ProductID><Quantity>2</Quantity></Order>
```

> [AZURE.NOTE]Der Einfachheit halber werden in diesen Beispielen die Informationen in zurückgegebenen Antworten als XML-Textdaten angezeigt. Es gibt jedoch kein Grund, warum Ressourcen keinen anderen von HTTP unterstützten Datentyp enthalten sollten, z. B. binäre oder verschlüsselte Informationen; der Inhaltstyp in der HTTP-Antwort sollte den Typ angeben. Zudem kann ein REST-Modell u. U. dieselben Daten in verschiedenen Formaten wie XML oder JSON zurückgeben. In diesem Fall sollte der Webdienst zur Inhaltsaushandlung mit dem Client, der die Anforderung stellt, in der Lage sein. Die Anforderung kann einen _Annehmen_-Header enthalten, der das bevorzugte Format angibt, das der Client erhalten möchte, und der Webdienst sollte versuchen, dieses Format nach Möglichkeit zu berücksichtigen.

Beachten Sie, dass die Antwort von einer REST-Anforderung die standardmäßigen HTTP-Statuscodes verwendet. Beispielsweise sollte eine Anforderung, die gültige Daten zurückgibt, den HTTP-Antwortcode 200 (OK) enthalten, während eine Anforderung, die eine bestimmte Ressource nicht finden oder löschen kann, eine Antwort zurückgeben sollte, die den HTTP-Statuscode 404 (Nicht gefunden) enthält.

## Design und Struktur einer RESTful-Web-API

Einfachheit und Konsistenz sind entscheidend für das Entwerfen einer erfolgreichen Web-API. Eine Web-API, bei der diese beiden Faktoren berücksichtigt wurden, erleichtert das Erstellen von Clientanwendungen, die die API verwenden müssen.

Eine RESTful-Web-API dient der Bereitstellung einer Reihe von verbundenen Ressourcen sowie der Kernvorgänge, die einer Anwendung das Bearbeiten und das problemlose Wechseln zwischen diesen Ressourcen ermöglicht. Daher sollten die URIs, die eine typische RESTful-Web-API bilden, auf die bereitgestellten Daten ausgerichtet sein und die von HTTP bereitgestellten Funktionen auf diese Daten anwenden. Dieser Ansatz erfordert eine andere Einstellung als die übliche beim Entwerfen einer Reihe von Klassen in einer objektorientierten API, die in der Regel stärker vom Verhalten von Objekten und Klassen motiviert wird. Zudem sollte eine RESTful-Web-API zustandslos sein und nicht von Vorgängen abhängen, die in einer bestimmten Reihenfolge aufgerufen werden. In den folgenden Abschnitten sind die Punkte zusammengefasst, die beim Entwerfen einer RESTful-Web-API zu berücksichtigen sind.

### Organisieren der Web-API unter Berücksichtigung von Ressourcen

> [AZURE.TIP]Die von einem REST-Webdienst verfügbar gemachten URIs sollten auf Nomen basieren (die Daten, auf die die Web-API den Zugriff ermöglicht) und nicht auf Verben (wie eine Anwendung die Daten verwenden kann).

Konzentrieren sich auf die Geschäftseinheiten, die die Web-API verfügbar macht. Beispielsweise sind die primären Entitäten in einer Web-API zur Unterstützung des zuvor beschriebenen e-Commerce-Systems Kunden und Bestellungen. Prozesse wie das Aufgeben einer Bestellung können durch das Bereitstellen eines HTTP POST-Vorgangs durchgeführt werden, der die Bestellinformationen der Liste mit Bestellungen für den Kunden hinzufügt. Intern kann dieser POST-Vorgang z. B. Aufgaben wie das Überprüfen der Lagerbestände und die Rechnungsstellung übernehmen. Die HTTP-Antwort kann angeben, ob die Bestellung erfolgreich aufgegeben wurde oder nicht. Beachten Sie außerdem, dass eine Ressource nicht auf einem einzelnen physischen Datenelement basieren muss. Beispielsweise kann eine Bestellressource intern implementiert werden, indem zusammengefasste Informationen aus mehreren Zeilen über mehrere Tabellen in einer relationalen Datenbank verteilt, auf dem Client jedoch als einzelne Entität angezeigt werden.

> [AZURE.TIP]Vermeiden Sie das Entwerfen einer REST-Schnittstelle, die die interne Struktur der Daten, die sie verfügbar macht, spiegelt oder von ihr abhängig ist. REST dient nicht nur zum Implementieren von einfachen CRUD (Create, Retrieve, Update, Delete)-Vorgänge über separate Tabellen in einer relationalen Datenbank hinweg. REST dient der Zuordnung der Geschäftsentitäten und Vorgänge, die eine Anwendung für diese Entitäten ausführen kann, an die physische Implementierung dieser Entitäten, ein Client sollte jedoch nicht für diese physischen Entitäten verfügbar gemacht werden.

Einzelne Geschäftseinheiten sind selten isoliert vorhanden,(obwohl einige einzelne Objekte vorhanden sein können), sondern in der Regel in Auflistungen gruppiert. Für REST sind alle Entitäten und Auflistungen Ressourcen. In einer RESTful-Web-API verfügt jede Auflistung über einen eigenen URI innerhalb des Webdiensts, und das Ausführen einer HTTP GET-Anforderung über einen URI für eine Auflistung ruft eine Liste der Elemente in dieser Auflistung ab. Jedes einzelne Element verfügt auch über einen eigenen URI, und eine Anwendung kann eine weitere HTTP GET-Anforderung mit diesen URI zum Abrufen der Details des jeweiligen Elements senden. Sie sollten die URIs für Auflistungen und Elemente hierarchisch organisieren. Im e-Commerce-System kennzeichnet der URI _/Kunden_ die Auflistung des Kunden, und _/Kunden/5_ ruft die Details für den einzelnen Kunden mit der ID 5 aus dieser Auflistung ab. Dieser Ansatz trägt dazu bei, dass die Web-API intuitiv bleibt.

> [AZURE.TIP]Übernehmen Sie eine konsistente Benennungskonvention in URIs; im Allgemeinen ist es hilfreich, Nomen in Pluralform für URIs zu verwenden, die auf Auflistungen verweisen.

Sie müssen auch die Beziehungen zwischen verschiedenen Ressourcentypen und wie Sie diese Zuordnungen verfügbar machen können berücksichtigen. Kunden können z. B. null oder mehrere Bestellungen aufgeben. Ein natürliche Methode zur Darstellung dieser Beziehung ist durch einen URI wie z. B. _/Kunden/5/Bestellungen_, um alle Bestellungen für Kunde 5 zu suchen. Sie könnten mit der Zuordnung auch von einer Bestellung zurück auf einen bestimmten Kunden durch einen URI wie z. B. _/Bestellungen/99/Kunde_ darstellen, um den Kunden für Bestellungen 99 zu finden. Eine zu starke Erweiterung dieses Modells kann jedoch zu umständlichen Implementierungen führen. Eine bessere Lösung ist das Bereitstellen navigierbarer Links zu zugeordneten Ressourcen wie z. B. zum Kunden im Hauptteil der HTTP-Antwortnachricht, die zurückgegeben wird, wenn die Bestellung abgefragt wird. Dieser Mechanismus wird im Abschnitt „Verwenden des HATEOAS-Ansatzes, um die Navigation zu verwandten Ressourcen zu ermöglichen“ weiter unten in diesem Handbuch ausführlicher beschrieben.

In komplexeren Systemen sind möglicherweise zahlreiche weitere Entitätstypen vorhanden, und das Bereitstellen von URIs, die einer Clientanwendung das Navigieren über mehrere Ebenen von Beziehungen hinweg ermöglichen, z. B. _/Kunden/1/Bestellungen/99/Produkte_, um die Produktliste von Kunde 1 in Bestellung 99 abzurufen, kann verlockend sein. Dieses Maß an Komplexität kann jedoch schwierig zu verwalten sein und ist unflexibel, wenn sich die Beziehungen zwischen Ressourcen in der Zukunft ändern. Vielmehr sollten Sie sich bemühen, URIs relativ einfach zu halten. Beachten Sie, dass sobald eine Anwendung über einen Verweis auf eine Ressource verfügt, dieser Verweis zum Suchen von Elementen im Zusammenhang mit dieser Ressource verwendet werden kann. Die vorhergehende Abfrage kann durch den URI _/Kunden/1/Bestellungen_ ersetzt werden, um alle Bestellungen für Kunde 1 zu finden; anschließend wird der URI _/Bestellungen/99/Produkte_ zum Ermitteln der Produkte in dieser Bestellung abgefragt (vorausgesetzt Bestellung 99 wurde von Kunden 1 aufgegeben).

> [AZURE.TIP]Vermeiden Sie, dass komplexere URIs als _Auflistung/Element/Auflistung_ erforderlich sind.

Außerdem ist es wichtig zu berücksichtigen, dass alle Webanforderungen den Webserver belasten, und je höher die Anzahl der Anforderungen, desto höher die Last. Sie sollten versuchen, Ihre Ressourcen zu definieren, um „geschwätzige“ Web-APIs vermeiden, die eine große Anzahl von kleinen Ressourcen verfügbar machen. Eine solche API erfordert möglicherweise, dass eine Clientanwendung mehrere Anforderungen sendet, um alle erforderlichen Daten zu finden. Es kann vorteilhaft sein, Daten zu denormalisieren und Informationen zu größeren Ressourcen zusammenzufassen, die mit einer einzelnen Anforderung abgerufen werden können. Jedoch müssen Sie diesen Ansatz gegen den Aufwand für das Abrufen von Daten abwägen, die für den Client möglicherweise oft nicht erforderlich sind. Das Abrufen von großen Objekten kann die Latenz einer Anforderung erhöhen und zusätzliche Bandbreitenkosten für geringe Vorteile verursachen, wenn die zusätzlichen Daten nicht häufig verwendet werden.

Vermeiden Sie das Einführen von Abhängigkeiten zwischen der Web-API und der Struktur, dem Typ oder dem Speicherort der zugrundeliegenden Datenquellen. Befinden sich Ihre Daten beispielsweise in einer relationalen Datenbank, muss die Web-API nicht jede Tabelle als Auflistung von Ressourcen verfügbar machen. Betrachten Sie die Web-API als eine Abstraktion der Datenbank, und erstellen Sie bei Bedarf eine Zuordnungsebene zwischen der Datenbank und der Web-API. Sollte sich das Design oder die Implementierung der Datenbank dann ändern (z. B. wenn Sie von einer relationalen Datenbank mit einer Auflistung von normalisierten Tabellen zu einem denormalisierten NoSQL-Speichersystem wie z. B. einer Dokumentendatenbank wechseln), sind Clientanwendungen von diesen Änderungen isoliert.
> [AZURE.TIP]Die Quelle der Daten, die eine Web-API unterstützen, muss kein Datenspeicher sein; es kann sich auch um eine andere Branchenanwendung oder sogar eine ältere Anwendung handeln, die lokal innerhalb einer Organisation ausgeführt wird.

Schließlich ist es auch möglich, dass nicht jeder von einer Web-API für eine bestimmte Ressource implementierte Vorgang zugeordnet werden kann. Sie können solche Szenarien _ohne Ressourcen_ z. B. über HTTP GET-Anforderungen behandeln, die einen Teil der Funktionalität aufrufen und die Ergebnisse als eine HTTP-Antwortnachricht zurückgeben. Eine Web-API, die einfache Rechenvorgänge wie z. B. Addition und Subtraktion implementiert, könnte URIs bereitstellen, die diese Vorgänge als Pseudoressourcen verfügbar machen und die Abfragezeichenfolge zum Angeben der erforderlichen Parameter verwenden. Eine GET-Anforderung an den URI _/add?operand1=99&operand2=1_ könnte eine Antwortnachricht mit einem Text zurückgeben, der den Wert 100 enthält, und eine GET-Anforderung an den URI _/subtract?operand1=50&operand2=20_ könnte eine Antwortnachricht mit einem Text zurückgeben, der den Wert 30 enthält. Verwenden Sie diese URI-Formen jedoch nur sparsam.

### Definieren von Vorgängen im Hinblick auf HTTP-Methoden

Das HTTP-Protokoll definiert eine Reihe von Methoden, die einer Anforderung semantische Bedeutung zuweisen. Diese allgemeinen HTTP-Methoden werden von den meisten RESTful-Web-APIs verwendet:

- **GET**, um eine Kopie der Ressource am angegebenen URI abzurufen Der Text der Antwortnachricht enthält die Details der angeforderten Ressource.

- **POST**, um eine neue Ressource am angegebenen URI zu erstellen Der Text der Anforderungsnachricht enthält die Details der neuen Ressource. Beachten Sie, dass POST auch zum Auslösen von Vorgängen verwendet werden kann, die nicht tatsächlich Ressourcen erstellen.

- **PUT**, um die Ressource am angegebenen URI zu ersetzen oder zu aktualisieren Der Text der Anforderungsnachricht gibt die zu ändernde Ressource und die anzuwendenden Werte an.

- **DELETE**, um die Ressource am angegebenen URI zu entfernen.

> [AZURE.NOTE]Das HTTP-Protokoll definiert auch andere weniger häufig verwendete Methoden, z. B. die PATCH-Methode, mit der selektive Updates einer Ressource angefordert werden, die HEAD-Methode, die Beschreibung einer Ressource angefordert wird, die OPTIONS-Methode, die einer Clientinformation das Abrufen von Informationen zu den vom Server unterstützten Kommunikationsoptionen ermöglicht, und die TRACE-Methode, die einem Client das Anfordern von Informationen zu Test- und Diagnosezwecken ermöglicht.

Die Auswirkungen einer bestimmten Anforderung sollten davon abhängen, ob die Ressource, auf die sie angewendet wird, eine Auflistung oder ein einzelnes Element ist. In der folgende Tabelle sind anhand des e-Commerce-Beispiels die allgemeinen Konventionen zusammengefasst, die bei den meisten RESTful-Implementierungen verwendet werden. Beachten Sie, dass u. U. nicht alle diese Anforderungen implementiert werden; dies hängt vom jeweiligen Szenario ab.

| **Ressource** | **POST** | **GET** | **PUT** | **DELETE** |
|--------------|----------|---------|---------|------------|
| /Kunden | Neuen Kunden erstellen | Alle Kunden abrufen | Massenaktualisierung von Kunden (_falls implementiert_) | Alle Kunden entfernen |
| /Kunden/1 | Fehler | Details für Kunden 1 abrufen | Details von Kunde 1 aktualisieren, falls vorhanden; andernfalls Fehler zurückgeben | Kunde 1 entfernen |
| /Kunden/1/Bestellungen | Neue Bestellung für Kunden 1 erstellen | Alle Bestellungen für Kunde 1 abrufen | Massenaktualisierung von Bestellungen für Kunde 1 (_falls implementiert_) | Alle Bestellungen für Kunde 1 entfernen (_falls implementiert_) |

Der Zweck der GET und DELETE-Anforderungen ist relativ einfach, der Zweck und die Auswirkungen der POST- und PUT-Anforderungen sind jedoch u. U. weniger eindeutig.

Eine POST-Anforderung sollte eine neue Ressource mit Daten im Hauptteil der Anforderung erstellen. Im REST-Modell wenden Sie häufig POST-Anforderungen auf Ressourcen an, die Auflistungen sind; die neue Ressource wird der Auflistung hinzugefügt.

> [AZURE.NOTE]Sie können auch POST-Anforderungen definieren, die einige Funktionen auslösen (und nicht unbedingt zurückgeben); diese Art von Anforderung kann auf Auflistungen angewendet werden. Beispielsweise können Sie eine POST-Anforderung verwenden, um einen Arbeitszeitnachweis an einen Dienst für die Gehaltsabrechnung übergeben und die berechneten Steuern als Antwort zu erhalten.

Eine PUT-Anforderung dient zum Ändern einer vorhandenen Ressource. Ist die angegebene Ressource nicht vorhanden, kann die PUT-Anforderung einen Fehler zurückgegeben (in einigen Fällen wird die Ressource auch erstellt). PUT-Anforderungen werden am häufigsten auf Ressourcen angewendet, die einzelne Elemente (z. B. einen bestimmter Kunden oder eine Bestellung) sind; sie können auch auf Auflistungen angewendet werden, dies wird jedoch weniger häufig implementiert. Beachten Sie, dass PUT-Anforderungen im Gegensatz zu POST-Anforderungen idempotent sind. Wenn eine Anwendung die gleiche PUT-Anforderung mehrmals sendet, sollten die Ergebnisse immer gleich sein (die gleiche Ressource wird mit den gleichen Werten geändert); wenn eine Anwendung die gleiche POST-Anforderung wiederholt, ist das Ergebnis jedoch die Erstellung mehrerer Ressourcen.

> [AZURE.NOTE]Genau genommen ersetzt eine HTTP PUT-Anforderung eine vorhandene Ressource durch die im Text der Anforderung angegebenen Ressource. Wenn eine Auswahl von Eigenschaften in einer Ressource geändert werden, andere Eigenschaften jedoch unverändert bleiben sollen, sollte dies per HTTP PATCH-Anforderung implementiert werden. Allerdings lockern viele RESTful-Implementierungen diese Regel verwenden in beiden Situationen PUT.

### Verarbeiten von HTTP-Anforderungen
Die bei vielen HTTP-Anforderungen in der Clientanwendung enthaltenen Daten und die entsprechenden Antwortnachrichten vom Webserver können in einer Vielzahl von Formaten (oder Medientypen) angezeigt werden. Beispielsweise könnten die Daten, die die Details für einen Kunden oder eine Bestellung angeben, im XML-, JSON- oder einem anderen codierten und komprimierten Format bereitgestellt werden. Eine RESTful-Web-API sollte verschiedene Medientypen unterstützen, je nach den Anforderungen der Clientanwendung, die eine Anforderung sendet.

Wenn eine Clientanwendung eine Anforderung sendet, die Daten im Hauptteil einer Nachricht zurückgibt, kann sie die Medientypen angeben, die sie im Accept-Header der Anforderung behandeln kann. Der folgende Code veranschaulicht eine HTTP GET-Anforderung, die die Details von Kunde 1 abruft und anfordert, dass das Ergebnis im JSON-Format zurückgegeben wird (der Client sollte dennoch den Medientyp der Daten in der Antwort überprüfen, um das Format der zurückgegebenen Daten zu bestätigen):

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
Accept: application/json
...
```

Wenn der Webserver diesen Medientyp unterstützt, kann er eine Antwort zurückgeben, die einen Content-Type-Header enthält, der das Format der Daten im Text der Nachricht angibt:

> [AZURE.NOTE]Für eine optimale Interoperabilität sollten die Medientypen, auf die in den Accept- und Content-Type-Headern verwiesen wird, als MIME-Typen anstatt eines benutzerdefinierten Medientyps erkannt werden.

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"OrderID":2,"ProductID":4,"Quantity":2,"OrderValue":10.00}
```

Wenn der Webserver den angeforderten Medientyp nicht unterstützt, kann er diese Daten in einem anderen Format senden. In allen Fällen muss der Medientyp (z. B. _Text/XML_) im Content-Type-Header angegeben werden. Die Clientanwendung ist dafür verantwortlich, die Antwortnachricht zu analysieren und die Ergebnisse im Nachrichtentext richtig zu interpretieren.

Beachten Sie, dass der Webserver in diesem Beispiel die angeforderten Daten erfolgreich abruft und im Antwortheader den Statuscode für die erfolgreiche Ausführung zurückgibt. Werden keine übereinstimmenden Daten gefunden, sollte stattdessen der Statuscode 404 (nicht gefunden) zurückgegeben werden, und der Text der Antwortnachricht kann zusätzliche Informationen enthalten. Das Format dieser Informationen wird wie im folgenden Beispiel gezeigt durch den Content-Type-Header angegeben:

```HTTP
GET http://adventure-works.com/orders/222 HTTP/1.1
...
Accept: application/json
...
```

Bestellung 222 ist nicht vorhanden, daher sieht die Antwortnachricht wie folgt aus:

```HTTP
HTTP/1.1 404 Not Found
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"Message":"No such order"}
```

Wenn eine Anwendung eine HTTP PUT-Anforderung zum Aktualisieren einer Ressource sendet, gibt sie den URI der Ressource an und stellt die zu ändernden Daten im Text der Anforderungsnachricht bereit. Außerdem sollte auch das Format dieser Daten mithilfe des Content-Type-Headers angegeben werden. Ein gängiges Format für textbasierte Informationen ist _application/x-www-form-urlencoded_, das einen Satz von Name-Wert-Paaren umfasst, die mit dem &-Zeichen getrennt sind. Das nächste Beispiel zeigt eine HTTP PUT-Anforderung, die die Informationen in Bestellung 1 ändert:

```HTTP
PUT http://adventure-works.com/orders/1 HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
ProductID=3&Quantity=5&OrderValue=250
```

Wenn die Änderung erfolgreich ist, sollte sie idealerweise mit den Statuscode „HTTP 204“ reagieren, der angibt, dass der Prozess erfolgreich verarbeitet wurde, der Antworttext jedoch keine weiteren Informationen enthält. Der Location-Header in der Antwort enthält den URI der neu aktualisierten Ressource:

```HTTP
HTTP/1.1 204 No Content
...
Location: http://adventure-works.com/orders/1
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

> [AZURE.TIP]Wenn die Daten in einer HTTP PUT-Anforderungsnachricht Datums-und Uhrzeitinformationen enthalten, stellen Sie sicher, dass Ihr Webdienst Daten und Uhrzeiten akzeptiert, die nach dem ISO 8601-Standard formatiert sind.

Wenn die zu aktualisierende Ressource nicht vorhanden ist, kann der Webserver wie oben beschrieben mit einer „Nicht gefunden“-Antwort reagieren. Wenn der Server tatsächlich das Objekt selbst erstellt, kann er auch den Statuscodes „HTTP 200“ (OK) oder „HTTP 201“ (erstellt) zurückgeben, und der Antworttext kann die Daten für die neue Ressource enthalten. Wenn der Content-Type-Header der Anforderung ein Datenformat angibt, das der Webserver nicht verarbeiten kann, sollte er mit dem Statuscode „HTTP 415“ (Nicht unterstützter Medientyp) reagieren.

> [AZURE.TIP]Erwägen Sie das Implementieren von HTTP PUT-Massenvorgängen, die Stapelaktualisierungen für mehrere Ressourcen in einer Auflistung durchführen können. Die PUT-Anforderung sollte den URI der Auflistung angeben, und Anforderungstext sollte die Details der zu ändernden Ressourcen angeben. Dieser Ansatz trägt zum Reduzieren von „Geschwätzigkeit“ und zur Leistungssteigerung bei.

Das Format einer HTTP POST-Anforderung, die neue Ressourcen erstellt, ähnelt dem von PUT-Anforderungen; der Nachrichtentext enthält die Details der neuen Ressource, die hinzugefügt wird. Der URI gibt jedoch in der Regel die Auflistung an, der die Ressource hinzugefügt werden soll. Das folgende Beispiel erstellt eine neue Bestellung und fügt sie der Bestellungsauflistung hinzu:

```HTTP
POST http://adventure-works.com/orders HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
ProductID=5&Quantity=15&OrderValue=400
```

Wenn die Anforderung erfolgreich ist, sollte der Webserver mit einer Nachricht mit dem Statuscode „HTTP 201“ (erstellt) reagieren. Der Location-Header sollte den URI der neu erstellten Ressource enthalten, und der Text der Antwort sollte eine Kopie der neuen Ressource enthalten; der Content-Type-Header gibt das Format dieser Daten an:

```HTTP
HTTP/1.1 201 Created
...
Content-Type: application/json; charset=utf-8
Location: http://adventure-works.com/orders/99
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"OrderID":99,"ProductID":5,"Quantity":15,"OrderValue":400}
```

> [AZURE.TIP]Wenn die von einer PUT- oder POST-Anforderung bereitgestellten Daten ungültig sind, sollte der Webserver mit eine Nachricht mit dem Statuscode „HTTP 400“ (Ungültige Anforderung) reagieren. Der Text dieser Nachricht kann zusätzliche Informationen über das Problem mit der Anforderung und den erwarteten Formaten oder einen Link zu einer URL enthalten, die weitere Details bereitstellt.

Zum Entfernen einer Ressource stellt eine HTTP DELETE-Anforderung einfach den URI der zu löschenden Ressource bereit. Im folgenden Beispiel wird versucht, Bestellung 99 zu entfernen:

```HTTP
DELETE http://adventure-works.com/orders/99 HTTP/1.1
...
```

Wenn der Löschvorgang erfolgreich ist, sollte der Webserver mit dem Statuscode „HTTP 204“ reagieren, der angibt, dass der Prozess erfolgreich verarbeitet wurde, der Antworttext aber keine weiteren Informationen enthält (die gleiche Antwort wird auch von einem erfolgreichen PUT-Vorgang zurückgegeben, jedoch ohne einen Location-Header, da die Ressource nicht mehr vorhanden ist). Eine DELETE-Anforderung kann auch den Statuscode „HTTP 200“ (OK) oder 202 (akzeptiert) zurückgeben, wenn der Löschvorgang asynchron ausgeführt wird.

```HTTP
HTTP/1.1 204 No Content
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

Wenn die Ressource nicht gefunden wird, sollte der Webserver stattdessen die Nachricht 404 (nicht gefunden) zurückgeben.

> [AZURE.TIP]Wenn alle Ressourcen in einer Auflistung gelöscht werden müssen, aktivieren Sie eine HTTP DELETE-Anforderung, die für den URI der Auflistung angegeben wird, anstatt das Entfernen aller Ressourcen aus Auflistung nacheinander zu erzwingen.

### Filtern und Paginieren von Daten

Sie sollten sich bemühen, die URIs einfach und intuitiv zu halten. Das Verfügbarmachen einer Auflistung von Ressourcen durch einen einzigen URI ist in dieser Hinsicht hilfreich, kann jedoch dazu führen, dass Anwendungen große Datenmengen abrufen, auch wenn nur eine Teilmenge der Informationen erforderlich ist. Das Generieren einer großen Menge von Datenverkehr wirkt sich nicht nur auf die Leistung und die Skalierbarkeit des Webservers aus, sondern beeinträchtigt auch die Reaktionsfähigkeit von Clientanwendungen, die die Daten anfordern.

Wenn Bestellungen beispielsweise den für die Bestellung bezahlten Preis enthalten, muss eine Clientanwendung, die alle Bestellungen mit Kosten über einen bestimmten Wert abrufen soll, möglicherweise alle Bestellungen aus dem URI _/Bestellungen_ abrufen und diese dann lokal filtern. Dieser Prozess ist eindeutig sehr ineffizient; er verschwendet Netzwerkbandbreite und Verarbeitungsleistung auf dem Server, der die Web-API hostet.

Eine Lösung ist z. B. das Bereitstellen eines URI-Schemas wie _/Bestellungen/Bestellwert_größer_als_n_ wobei _n_ der Preis der Bestellung ist; dieser Ansatz ist jedoch nur für eine begrenzte Anzahl von Preisen geeignet. Wenn Sie Bestellungen auf Grundlage anderer Kriterien abfragen müssen, wird Ihnen möglicherweise eine lange Liste mit URIs bereitgestellt, die nicht intuitive Namen enthalten kann.

Eine bessere Strategie zum Filtern von Daten ist das Bereitstellen der Filterkriterien in der Abfragezeichenfolge, die an die Web-API übergeben wird, z. B. _/Bestellungen? Bestellwertgrenze = n_. In diesem Beispiel ist der entsprechende Vorgang in der Web-API für die Analyse und Behandlung der `ordervaluethreshold`-Parameter in der Abfragezeichenfolge und das Zurückgeben der gefilterten Ergebnisse in der HTTP-Antwort verantwortlich.

Einige einfache HTTP GET-Anforderungen über Auflistungsressourcen geben u. U. eine große Anzahl von Elementen zurück. Um dies zu vermeiden, sollten Sie die Web-API so entwerfen, dass die von jeder einzelnen Anforderung zurückgegebene Datenmenge begrenzt ist. Dazu unterstützen Sie Abfragezeichenfolgen, die es dem Benutzer ermöglichen, die maximale Anzahl von abzurufenden Elementen anzugeben (diese kann wiederum von einer Obergrenze abhängen, um Denial-of-Service-Angriff zu verhindern), und ein Startoffset in der Auflistung. Beispielsweise sollte die Abfragezeichenfolge im URI _/Bestellungen?Grenze=25&Offset=50_ 25 Bestellungen beginnend bei der 50. Bestellung in der Auflistung abrufen. Wie beim Filtern von Daten ist der Vorgang, der die GET-Anforderung in die Web-API implementiert, für die Analyse und Behandlung der `limit`- und `offset`-Parameter in der Abfragezeichenfolge verantwortlich. Zur Unterstützung von Clientanwendungen sollten GET-Anforderungen, die paginierte Daten zurückgeben, auch Metadaten enthalten, die die Gesamtanzahl der in der Auflistung verfügbaren Ressourcen angeben. Ziehen Sie auch andere intelligente Pagingstrategien in Betracht; weitere Informationen dazu finden Sie unter [API-Designhinweise: Smart Paging](http://bizcoder.com/api-design-notes-smart-paging).

Sie können eine ähnliche Strategie zum Sortieren von Daten während des Abrufs nutzen; Sie können einen Sortierparameter bereitstellen, der einen Feldnamen als Wert verwendet, z. B. _/Bestellungen?sortieren=ProductID_. Beachten Sie jedoch, dass dieser Ansatz die Zwischenspeicherung beeinträchtigen kann (Abfragezeichenfolgen-Parameter stellen einen Teil des Ressourcenbezeichners dar, der von zahlreichen Cacheimplementierungen als Schlüssel für zwischengespeicherte Daten verwendet wird).

Sie können diesen Ansatz erweitern, um die zurückgegebenen Felder zu begrenzen (projizieren), wenn ein einzelnes Ressourcenelement eine große Datenmenge enthält. Sie können z. B. einen Abfragezeichenfolgen-Parameter verwenden, der eine durch Trennzeichen getrennte Liste der Felder akzeptiert, z. B. _/Bestellungen?Felder=ProductID,Menge_.

> [AZURE.TIP]Geben Sie für alle optionalen Parameter in Abfragezeichenfolgen aussagekräftige Standardwerte an. Legen Sie z. B. den `limit`-Parameter auf 10 und den `offset`-Parameter auf 0 fest, wenn Sie die Paginierung implementieren; legen Sie den Sortierungsparameter auf den Schlüssel der Ressource fest, wenn Sie Bestellungen implementieren, und legen Sie den `fields`-Parameter auf alle Felder in der Ressource fest, wenn Sie Projektionen unterstützen.

### Behandeln von großen binären Ressourcen

Eine einzelne Ressource kann große binäre Felder wie z. B. Dateien oder Bilder enthalten. Zum Beheben der Übertragungsprobleme, die durch unzuverlässige und unterbrochene Verbindungen verursacht werden, und zur Verbesserung der Antwortzeiten erwägen Sie das Bereitstellen von Vorgängen, die der Clientanwendung das Abrufen solcher Ressourcen in Segmenten ermöglicht. Dazu sollte die Web-API den Accept-Ranges-Header für GET-Anforderungen für große Ressourcen unterstützen und im Idealfall HTTP HEAD-Anforderungen für diese Ressourcen implementieren. Der Accept-Ranges-Header gibt an, dass der GET-Vorgang Teilergebnisse unterstützt, und dass eine Clientanwendung GET-Anforderungen senden kann, die eine Teilmenge einer Ressource als Folge von Bytes zurückgeben kann. Eine HEAD-Anforderung ähnelt einer GET-Anforderung, gibt jedoch nur einen Header zurück, der die Ressource beschreibt, sowie einen leeren Nachrichtentext. Eine Clientanwendung kann eine HEAD-Anforderung ausgeben, um zu bestimmen, ob eine Ressource mithilfe von partiellen GET-Anforderungen abgerufen wird. Das folgende Beispiel zeigt eine HEAD-Anforderung, die Informationen zu einem Produktabbild abruft:

```HTTP
HEAD http://adventure-works.com/products/10?fields=ProductImage HTTP/1.1
...
```

Die Antwortnachricht enthält einen Header, der die Größe der Ressource (4580 Bytes) enthält, und den Accept-Ranges-Header, der angibt, dass der entsprechende GET-Vorgang Teilergebnisse unterstützt:

```HTTP
HTTP/1.1 200 OK
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 4580
...
```

Die Clientanwendung kann diese Informationen verwenden, um eine Reihe von GET-Vorgängen zu erstellen, mit denen das Bild in kleineren Segmenten abgerufen wird. Die erste Anforderung Ruft die ersten 2500 Bytes mithilfe des Range-Headers ab:

```HTTP
GET http://adventure-works.com/products/10?fields=ProductImage HTTP/1.1
Range: bytes=0-2499
...
```

Die Antwortnachricht gibt an, dass dies eine partielle Antwort ist, in dem der Statuscode „HTTP 206“ zurückgegeben wird. Der Content-Length-Header gibt die tatsächliche Anzahl der im Nachrichtentext (nicht die Größe der Ressource) zurückgegebenen Bytes an, und der Content-Range-Header gibt an, um welchen Teil der Ressource es sich handelt (0 – 2499 von 4580 Bytes):

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2500
Content-Range: bytes 0-2499/4580
...
_{binary data not shown}_
```

Eine nachfolgende Anforderung von der Clientanwendung kann mithilfe eines entsprechenden Range-Headers den Rest der Ressource abrufen:

```HTTP
GET http://adventure-works.com/products/10?fields=ProductImage HTTP/1.1
Range: bytes=2500-
...
```

Die entsprechende Ergebnisnachricht sollte wie folgt aussehen:

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2080
Content-Range: bytes 2500-4580/4580
...
```

## Verwenden des HATEOAS-Ansatzes, um die Navigation zu verwandten Ressourcen zu ermöglichen

Eine Hauptmotivation hinter REST besteht darin, dass der gesamte Ressourcensatz navigiert werden kann, ohne dass zuvor Kenntnisse des URI-Schemas erforderlich sind. Jede HTTP GET-Anforderung sollte über Hyperlinks in der Antwort die erforderlichen Informationen zum Finden der direkt auf das angeforderte Objekt bezogenen Ressourcen enthalten; außerdem sollten Informationen bereitgestellt werden, die die für jede dieser Ressourcen verfügbaren Vorgänge beschreiben. Dieses Prinzip wird als Hypertext als das Modul des Anwendungszustands (HATEOAS, Hypertext as the Engine of Application State) bezeichnet. Beim System handelt es sich im Grunde um einen finiten Statuscomputer, und die Antwort auf jede Anforderung enthält die notwendigen Informationen für den Wechseln von einem Zustand in einen anderen; weitere Informationen sollten nicht erforderlich sein.

> [AZURE.NOTE]Derzeit sind keine Standards oder Spezifikationen vorhanden, die die Modellierung des HATEOAS-Prinzips definieren. Die Beispiele in diesem Abschnitt veranschaulichen eine mögliche Lösung.

Beispielsweise sollten die in der Antwort für eine bestimmte Bestellung zurückgegebenen Daten zur Verarbeitung der Beziehung zwischen Kunden und Bestellungen URIs in Form eines Hyperlinks enthalten, die den Kunden identifizieren, der die Bestellung aufgegeben hat, und die Vorgänge, die für diesen Kunden ausgeführt werden können.

```HTTP
GET http://adventure-works.com/orders/3 HTTP/1.1
Accept: application/json
...
```

Der Text der Antwortnachricht enthält ein `Links`-Array (im Codebeispiel hervorgehoben), das die Art der Beziehung (_Kunde_), den URI des Kunden (_http://adventure-works.com/customers/3_), die Methode zum Abrufen der Details dieses Kunden (_GET_) und die MIME-Typen angibt, die der Webserver Typen für das Abrufen dieser Informationen unterstützt (_text/xml_ und _application/json_). Dies sind alle Informationen, die eine Clientanwendung zum Abrufen der Details des Kunden benötigt. Das Links-Array enthält außerdem Links für andere Vorgänge, die ausgeführt werden können, z. B. PUT (zum Ändern des Kunden zusammen mit dem Format, das der Webserver vom Client erwartet) und DELETE.

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"OrderID":3,"ProductID":2,"Quantity":4,"OrderValue":16.60,"Links":[(some links omitted){"Relationship":"customer","HRef":" http://adventure-works.com/customers/3", "Action":"GET","LinkedResourceMIMETypes":["text/xml","application/json"]},{"Relationship":"
customer","HRef":" http://adventure-works.com /customers/3", "Action":"PUT","LinkedResourceMIMETypes":["application/x-www-form-urlencoded"]},{"Relationship":"customer","HRef":" http://adventure-works.com /customers/3","Action":"DELETE","LinkedResourceMIMETypes":[]}]}
```

Der Vollständigkeit halber sollte das Links-Array Links auch auf sich selbst verweisende Informationen enthalten, die sich auf die abgerufene Ressource beziehen. Diese Links wurden im vorherigen Beispiel weggelassen, sind jedoch im folgenden Code hervorgehoben. Beachten Sie, dass in diesen Links die Beziehung _selbst_ verwendet wurde, um anzugeben, dass es sich um einen Verweis auf die vom Vorgang zurückgegebene Ressource handelt:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"OrderID":3,"ProductID":2,"Quantity":4,"OrderValue":16.60,"Links":[{"Relationship":"self","HRef":" http://adventure-works.com/orders/3", "Action":"GET","LinkedResourceMIMETypes":["text/xml","application/json"]},{"Relationship":" self","HRef":" http://adventure-works.com /orders/3", "Action":"PUT","LinkedResourceMIMETypes":["application/x-www-form-urlencoded"]},{"Relationship":"self","HRef":" http://adventure-works.com /orders/3", "Action":"DELETE","LinkedResourceMIMETypes":[]},{"Relationship":"customer",
"HRef":" http://adventure-works.com /customers/3", "Action":"GET","LinkedResourceMIMETypes":["text/xml","application/json"]},{"Relationship":" customer" (customer links omitted)}]}
```

Damit dieser Ansatz effektiv ist, müssen Clientanwendungen zum Abrufen und Analysieren dieser zusätzlichen Informationen bereit sein.

## Versionsverwaltung einer RESTful-Web-API

Außer in den einfachsten Fällen ist es sehr unwahrscheinlich, dass eine Web-API statisch bleibt. Aufgrund von veränderten Unternehmensanforderungen werden u. U. neue Auflistungen von Ressourcen hinzugefügt, die Beziehungen zwischen Ressourcen können sich ändern, und die Struktur der Daten in Ressourcen wird möglicherweise geändert. Das Aktualisieren einer Web-API, um neue oder unterschiedliche Anforderungen zu behandeln, ist ein relativ unkomplizierter Prozess. Sie müssen jedoch die Auswirkungen solcher Änderungen auf Client-Anwendungen berücksichtigen, die die Web-API nutzen. Das Problem ist, dass der Entwickler, der eine Web-API entwirft und implementiert, zwar die vollständige Kontrolle über diese API hat, jedoch nicht das gleiche Maß an Kontrolle über Clientanwendungen hat, die u. U. von remote agierenden Drittanbietern erstellt werden. Es kommt primär darauf an, dass vorhandene Clientanwendungen unverändert weiter ausgeführt werden können, und dass neue Clientanwendungen neue Features und Ressourcen nutzen können.

Die Versionsverwaltung ermöglicht einer Web-API das Angeben der Funktionen und Ressourcen, die sie verfügbar macht, und eine Clientanwendung kann Anforderungen an eine bestimmte Version einer Funktion oder einer Ressource senden. In den folgenden Abschnitten sind verschiedene Ansätze mit ihren jeweiligen Vor- und Nachteilen beschrieben.

### Keine Versionsverwaltung

Dies ist der einfachste Ansatz und ist möglicherweise für einige interne APIs akzeptabel. Umfassende Änderungen können als neue Ressourcen oder neue Links dargestellt werden. Das Hinzufügen von Inhalt zu vorhandenen Ressourcen stellt möglicherweise keine fehlerhafte Änderung dar, da Clientanwendungen, die diesen Inhalt nicht erwarten, ihn einfach ignorieren.

Beispielsweise sollte eine Anforderung an den URI _http://adventure-works.com/customers/3_ die Details eines einzelnen Kunden mit den von der Clientanwendung erwarteten Feldern `Id`, `Name`, und `Address` zurückgeben:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","Address":"1 Microsoft Way Redmond WA 98053"}]
```

> [AZURE.NOTE]Der Einfachheit und Verständlichkeit halber enthalten die in diesem Abschnitt gezeigten Beispielantworten keine HATEOAS-Links.

Wenn dem Schema der Kundenressource das `DateCreated` Feld hinzugefügt wird, sieht die Antwort wie folgt aus:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","DateCreated":"2014-09-04T12:11:38.0376089Z","Address":"1 Microsoft Way Redmond WA 98053"}]
```

Vorhandene Clientanwendungen werden möglicherweise weiterhin ordnungsgemäß ausgeführt, wenn sie in der Lage sind, unbekannte Felder zu ignorieren. Neue Clientanwendungen können für die Behandlung dieses neuen Felds konfiguriert werden. Allerdings können radikalere Änderungen am Schema von Ressourcen (z. B. das Entfernen oder Umbenennen von Feldern) oder Änderungen an den Beziehungen zwischen Ressourcen fehlerhafte Änderungen darstellen, die verhindern, dass vorhandene Clientanwendungen nicht ordnungsgemäß funktioniert. In diesen Fällen sollten Sie einen der folgenden Ansätze in Betracht ziehen.

### URI-Versionsverwaltung

Bei jeder Änderung der Web-API oder des Ressourcenschemas fügen Sie eine für jede Ressource eine Versionsnummer für den URI hinzu. Die bereits vorhandenen URIs sollten weiterhin Ressourcen zurückgeben, die ihrem ursprünglichen Schema entsprechen.

Erweiterung des vorherigen Beispiels: Wenn das Feld `Address` in untergeordnete Felder umstrukturiert wird, die jeden einzelnen Teil der Adresse enthalten (z. B. `StreetAddress`, `City`, `State` und `ZipCode`) kann diese Version der Ressource über einen URI verfügbar gemacht werden, die eine Versionsnummer enthält, z. B. http://adventure-works.com/v2/customers/3:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","DateCreated":"2014-09-04T12:11:38.0376089Z","Address":{"StreetAddress":"1 Microsoft Way","City":"Redmond","State":"WA","ZipCode":98053}}]
```

Dieser Versionsverwaltungsmechanismus ist sehr einfach, hängt jedoch vom Server ab, der die Anforderung an das entsprechende Endgerät weiterleitet. Dieser Mechanismus kann jedoch schwerfällig werden, wenn die API mehrere Iterationen durchläuft und der Server eine Reihe von verschiedenen Versionen unterstützen muss. Aus der Sicht einer Puristen rufen die Clientanwendungen außerdem in allen Fällen dieselben Daten (Kunde 3) ab; daher sollte sich der URI im Grunde nicht abhängig von der Version unterscheiden. Dieses Schema erschwert zudem die Implementierung von HATEOAS, da alle Links die Versionsnummer in den URIs enthalten müssen.

### Versionsverwaltung der Abfragezeichenfolge

Anstatt mehrere URIs bereitzustellen, können Sie auch die Version der Ressource angeben, indem Sie in der Abfragezeichenfolge einen Parameter an die HTTP-Anforderung anfügen, z. B. _http://adventure-works.com/customers/3?version=2_. Der Versionsparameter sollte einen aussagekräftigen Standardwert wie z. B. „1“ aufweisen, wenn er von älteren Clientanwendungen weggelassen wird.

Dieser Ansatz hat den semantischen Vorteil, dass dieselbe Ressource immer vom gleichen URI abgerufen wird; er hängt jedoch davon ab, dass der Code, der die Anforderung behandelt, die Abfragezeichenfolge analysiert und die entsprechende HTTP-Antwort zurücksendet. Dieser Ansatz bringt beim Implementieren von HATEOAS dieselben Schwierigkeiten mit sich, wie der Mechanismus für die URI-Versionsverwaltung.

> [AZURE.NOTE]Einige ältere Webbrowser und Webproxys nehmen keine Zwischenspeicherung Anforderungen vor, deren URL eine Abfragezeichenfolge enthält. Dies kann sich negativ auf die Leistung von Webanwendungen auswirken, die eine Web-API verwenden und in einem solchen Webbrowser ausgeführt werden.

### Header-Versionsverwaltung

Anstatt die Versionsnummer als Abfragezeichenfolgen-Parameter hinzuzufügen, können Sie einen benutzerdefinierten Header implementieren, der die Version der Ressource angibt. Dieser Ansatz erfordert, dass die Clientanwendung allen Anforderungen den entsprechenden Header hinzufügt; der Code, der die Clientanforderung behandelt, kann jedoch einen Standardwert (Version 1) verwenden, wenn der Versionsheader fehlt. In den folgenden Beispielen wird ein benutzerdefinierter Header mit dem Namen _Benutzerdefinierter Header_ verwendet. Der Wert dieses Headers gibt die Version der Web-API an.

Version 1:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=1
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","Address":"1 Microsoft Way Redmond WA 98053"}]
```

Version 2:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=2
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","DateCreated":"2014-09-04T12:11:38.0376089Z","Address":{"StreetAddress":"1 Microsoft Way","City":"Redmond","State":"WA","ZipCode":98053}}]
```

Beachten Sie, dass die HATEOAS-Implementierung wie auch bei den beiden vorhergehenden Ansätzen die Angabe der entsprechenden benutzerdefinierten Header in allen Links erfordert.

### Versionsverwaltung des Medientyps

Wenn eine Clientanwendung eine HTTP GET-Anforderung an einen Webserver sendet, sollte sie wie weiter oben in diesem Handbuch beschrieben anhand eines Accept-Headers das Format des Inhalts vorgeben, den sie verarbeiten kann. Der Zweck des _Accept_-Header besteht häufig darin, dass die Clientanwendung angeben kann, ob der Text der Antwort im XML-, JSON- oder einem anderen gängigen Format vorliegt, das der Client analysieren kann. Allerdings ist es möglich, benutzerdefinierte Medientypen zu definieren, die Informationen enthalten, die es der Clientanwendung ermöglichen, die erwartete Version einer Ressource anzugeben. Das folgende Beispiel zeigt eine Anforderung die einen _Accept_-Header mit dem Wert _application/vnd.adventure-works.v1+json_ angibt. Das Element _vnd.adventure works.v1_ weist den Webserver an, Version 1 der Ressource zurückzugeben, während das Element _json_ angibt, dass der Antworttext im JSON-Format zurückgegeben werden soll:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Accept: application/vnd.adventure-works.v1+json
...
```

Der Code zum Verarbeiten der Anforderung ist dafür verantwortlich, den _Accept_-Header zu verarbeiten und so weit wie möglich zu berücksichtigen (die Clientanwendung kann im _Accept_-Header mehrere Formate angeben; in diesem Fall kann der Webserver das am besten geeignete Format für den Antworttext auswählen). Der Webserver bestätigt das Format der Daten im Antworttext mithilfe des Content-Type-Headers:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/vnd.adventure-works.v1+json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","Address":"1 Microsoft Way Redmond WA 98053"}]
```

Wenn im Accept-Header keine bekannten Medientypen angegeben sind, kann der Webserver eine Antwortnachricht vom Typ HTTP 406 (Nicht akzeptabel) generieren oder eine Nachricht mit einem Standardmedientyp zurückgeben.

Dieser Ansatz ist wohl der ursprünglichste Mechanismus zur Versionsverwaltung und eignet sich natürlich für das HATEOAS-Prinzip, bei dem der MIME-Typ der zugehörigen Daten in Ressourcenlinks enthalten sein kann.

> [AZURE.NOTE]Beim Auswählen einer Strategie für die Versionsverwaltung sollten Sie auch die Auswirkungen auf die Leistung berücksichtigen, insbesondere auf die Zwischenspeicherung auf dem Webserver. Die Schemas der URI- und Abfragezeichenfolge-Versionsverwaltung sind insofern cachefreundlich, dass dieselbe Kombination aus URI und Abfragezeichenfolge jedes Mal auf dieselben Daten verweist.

> Die Versionsverwaltungsmechanismen für Header und Medientyp erfordern in der Regel zusätzliche Logik, um die Werte im benutzerdefinierten Header oder im Accept-Header zu untersuchen. In einer umfangreichen Umgebung können zahlreiche Clients, die verschiedene Versionen einer Web-API verwenden, zu einer erheblichen Menge von doppelt vorhandenen Daten in einem serverseitigen Cache führen. Dieses Problem wird akut, wenn eine Clientanwendung mit einem Webserver über einen Proxy kommuniziert, der Zwischenspeicherung implementiert und eine Anforderung nur an den Webserver weiterleitet, wenn sein Cache derzeit keine Kopie der angeforderten Daten enthält.

## Weitere Informationen

- Das [RESTful-Cookbook](http://restcookbook.com/) enthält eine Einführung zum Erstellen von RESTful-APIs.
- Die [Web-API-Checkliste](https://mathieu.fenniak.net/the-api-checklist/) enthält eine nützliche Liste der zu berücksichtigenden Punkte beim Entwerfen und Implementieren einer Web-API.

<!---HONumber=July15_HO3-->