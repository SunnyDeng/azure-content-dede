<properties
   pageTitle="API-Implementierungsleitfaden | Microsoft Azure"
   description="Anleitung zur Implementierung einer API"
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
   ms.date="05/13/2015"
   ms.author="masashin"/>


# API-Implementierungsleitfaden

![](media/best-practices-api-implementation/pnp-logo.png)


Einige Themen in diesem Leitfaden werden noch diskutiert und können sich unter Umständen ändern. Wir freuen uns auf Ihr Feedback!

## Übersicht
Mit einer sorgfältig entworfenen REST-Web-API werden die Ressourcen, Beziehungen und Navigationsschemas definiert, auf die mit Clientanwendungen zugegriffen werden kann. Beim Implementieren und Bereitstellen einer Web-API sollten Sie die physischen Anforderungen der Umgebung berücksichtigen, in der die Web-API gehostet wird. Außerdem sollten Sie eher darauf achten, wie die Web-API erstellt wurde, als auf die logische Struktur der Daten. In diesem Leitfaden geht es hauptsächlich um die bewährten Methoden zur Implementierung einer Web-API und deren Veröffentlichung, um sie für Clientanwendungen verfügbar zu machen. Sicherheitsaspekte werden separat im Leitfaden zur API-Sicherheit beschrieben. Ausführliche Informationen zum Web-API-Design finden Sie im Leitfaden zum API-Design.

## Aspekte der Implementierung einer REST-Web-API
In den folgenden Abschnitten werden die bewährten Methoden zur Verwendung der ASP.NET-Web-API-Vorlage zum Erstellen einer REST-Web-API veranschaulicht. Ausführliche Informationen zur Verwendung der Web-API-Vorlage finden Sie auf der Microsoft-Website unter [Informationen zur ASP.NET-Web-API](http://www.asp.net/web-api).

## Aspekte der Implementierung des Anforderungsroutings

Bei einem Dienst, der mit der ASP.NET-Web-API implementiert wird, wird jede Anforderung an eine Methode in einer _Controller_-Klasse weitergeleitet. Das Web-API-Framework enthält zwei vorrangige Optionen für die Implementierung des Routings: _konventionsbasiertes_ Routing und _attributbasiertes_ Routing. Beachten Sie die folgenden Punkte, wenn Sie das beste Verfahren zum Weiterleiten von Anforderungen in Ihrer Web-API ermitteln möchten:

- **Machen Sie sich mit den Einschränkungen und Anforderungen des konventionsbasierten Routings vertraut**.

	Standardmäßig wird für das Web-API-Framework das konventionsbasierte Routing verwendet. Das Web-API-Framework erstellt eine anfängliche Routingtabelle mit dem folgenden Eintrag:

	```C#
	config.Routes.MapHttpRoute(
  		name: "DefaultApi",
	  	routeTemplate: "api/{controller}/{id}",
	  	defaults: new { id = RouteParameter.Optional }
	);
	```

	Routen können generisch sein und Literale wie _api_ und Variablen wie _{controller}_ und _{id}_ umfassen. Beim konventionsbasierten Routing können einige Elemente der Route optional sein. Das Web-API-Framework bestimmt wie folgt, welche Methode im Controller aufgerufen wird: Die HTTP-Methode der Anforderung wird mit dem ersten Teil des Methodennamens in der API abgeglichen, und anschließend werden alle optionalen Parameter abgeglichen. Wenn ein Controller mit dem Namen _orders_ die Methoden _GetAllOrders()_ oder _GetOrderByInt(int id)_ enthält, wird die GET-Anforderung \__http://www.adventure-works.com/api/orders/_ an die _GetAlllOrders()_-Methode geleitet, und die GET-Anforderung \__http://www.adventure-works.com/api/orders/99_ wird an die _GetOrderByInt(int id)_-Methode weitergeleitet. Falls keine übereinstimmende Methode verfügbar ist, die im Controller mit dem Präfix „Get“ beginnt, antwortet das Web-API-Framework mit der Nachricht „HTTP 405 (Method Not Allowed)“. Außerdem muss der in der Routingtabelle angegebene Name des Parameters (id) dem Namen des Parameters für die _GetOrderById_-Methode entsprechen. Andernfalls antwortet das Web-API-Framework mit der Antwort „HTTP 404 (Nicht gefunden)“.

	Dieselben Regeln gelten für POST-, PUT- und DELETE HTTP-Anforderungen. Eine PUT-Anforderung, mit der die Details von Bestellung 101 aktualisiert werden, wird an den URI \__http://www.adventure-works.com/api/orders/101_ geleitet. Der Text der Nachricht enthält die neuen Details der Bestellung, und diese Informationen werden als Parameter an eine Methode im orders-Controller mit einem Namen übergeben, der mit dem Präfix _Put_ beginnt, z. B. _PutOrder_.

	Die standardmäßige Routingtabelle ergibt keine Übereinstimmung mit einer Anforderung, bei der auf untergeordnete Ressourcen in einer REST-Web-API verwiesen wird, z. B. \__http://www.adventure-works.com/api/customers/1/orders_ (Suche nach den Details aller Bestellungen von „customer 1“). Zum Behandeln dieser Fälle können Sie der Routingtabelle benutzerdefinierte Routen hinzufügen:

	```C#
	config.Routes.MapHttpRoute(
	    name: "CustomerOrdersRoute",
	    routeTemplate: "api/customers/{custId}/orders",
	    defaults: new { controller="Customers", action="GetOrdersForCustomer" })
	);
	```

	Diese Route leitet Anforderungen, für die sich eine Übereinstimmung mit dem URI ergibt, an die _GetOrdersForCustomer_-Methode im _Customers_-Controller. Diese Methode muss einen einzelnen Parameter mit dem Namen _custI_ verwenden:

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    public IEnumerable<Order> GetOrdersForCustomer(int custId)
	    {
	        // Find orders for the specified customer
	        var orders = ...
	        return orders;
	    }
	    ...
	}
	```

	> [AZURE.TIP]Nutzen Sie das Standardrouting, wenn dies möglich ist, und vermeiden Sie das Festlegen von vielen komplizierten benutzerdefinierten Routen. Dies kann zu „Brüchigkeit“ (einfaches Hinzufügen von Methoden zu einem Controller, die zu mehrdeutigen Routen führen) und einer verringerten Leistung führen (je größer die Routingtabelle, desto größer ist der Aufwand für das Web-API-Framework beim Ermitteln, welche Route mit einem bestimmten URI übereinstimmt). Verwenden Sie einfache APIs und Routen. Weitere Informationen finden Sie im Abschnitt „Organisieren der Web-API basierend auf Ressourcen“ im API-Entwurfsleitfaden. Wenn Sie benutzerdefinierte Routen definieren müssen, ist die Verwendung des attributbasierten Routings ein guter Ansatz. Dies wird weiter unten in diesem Abschnitt beschrieben.

	Weitere Informationen zum konventionsbasierten Routing finden Sie auf der Microsoft-Website unter [Routing in der ASP.NET-Web-API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api).

- **Vermeiden Sie Mehrdeutigkeit beim Routing**.

	Das konventionsbasierte Routing kann zu mehrdeutigen Pfaden führen, wenn mehrere Methoden in einem Controller die gleiche Route aufweisen. In diesen Fällen antwortet das Web-API-Framework mit der Antwortnachricht „HTTP 500 (Internal Server Error)“ mit dem Text „Multiple actions were found that match the request“ (Es wurden mehrere Aktionen gefunden, die mit der Anforderung übereinstimmen).

- **Ziehen Sie das attributbasierte Routing vor**.

	Das attributbasierte Routing ist eine Alternative zum Verbinden von Routen mit Methoden in einem Controller. Anstatt sich auf die Musterabgleichfeatures des konventionsbasierten Routings zu verlassen, können Sie Methoden in einem Controller explizit mit den Details der Route versehen, der sie zugeordnet sein sollen. Dieser Ansatz trägt zur Vermeidung möglicher Mehrdeutigkeiten bei. Da explizite Routen zur Entwurfszeit definiert werden, ist dieser Ansatz außerdem effizienter als das konventionsbasierte Routing zur Laufzeit. Im folgenden Code wird veranschaulicht, wie Sie das _Route_-Attribut auf Methoden im Customers-Controller anwenden. Bei diesen Methoden wird auch das HttpGet-Attribut genutzt, um anzugeben, dass sie auf _HTTP GET_-Anforderungen antworten sollen. Mit diesem Attribut können Sie Ihre Methoden anhand eines bequemen Benennungsschemas benennen und müssen nicht das Schema verwenden, das beim konventionsbasierten Routing erwartet wird. Sie können Methoden auch mit den Attributen _HttpPost_, _HttpPut_ und _HttpDelete_ versehen, um Methoden zu definieren, die auf andere Arten von HTTP-Anforderungen antworten.

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers/{id}")]
	    [HttpGet]
	    public Customer FindCustomerByID(int id)
	    {
	        // Find the matching customer
	        var customer = ...
	        return customer;
	    }
	    ...
	    [Route("api/customers/{id}/orders")]
	    [HttpGet]
	    public IEnumerable<Order> FindOrdersForCustomer(int id)
	    {
	        // Find orders for the specified customer
	        var orders = ...
	        return orders;
	    }
	    ...
	}
	```

	Das attributbasierte Routing hat zudem den nützlichen Nebeneffekt, dass es als Dokumentation für Entwickler fungiert, die den Code später warten müssen. Es ist immer sofort klar, welche Methode zu welcher Route gehört, und mit dem _HttpGet_-Attribut wird angegeben, auf welche Art von HTTP-Anforderung die Methode antwortet.

	Beim attributbasierten Routing können Sie Einschränkungen festlegen, um vorzugeben, wie die Parameter abgeglichen werden. Mit Einschränkungen kann die Art des Parameters angegeben werden, und in einigen Fällen kann damit auch der zulässige Bereich der Parameterwerte angegeben werden. Im folgenden Beispiel muss der id-Parameter der _FindCustomerByID_-Methode eine Ganzzahl sein, die nicht negativ ist. Wenn eine Anwendung eine HTTP GET-Anforderung mit einer negativen Kundennummer übermittelt, antwortet das Web-API-Framework mit der Nachricht „HTTP 405 (Method Not Allowed)“:

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers/{id:int:min(0)}")]
	    [HttpGet]
	    public Customer FindCustomerByID(int id)
	    {
	        // Find the matching customer
	        var customer = ...
	        return customer;
	    }
	    ...
	}
	```

	Weitere Informationen zum attributbasierten Routing finden Sie auf der Microsoft-Website unter [Attributrouting in der Web-API 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2).

- **Unterstützen Sie Unicode-Zeichen in Routen**.

	Die Schlüssel zum Identifizieren von Ressourcen in GET-Anforderungen können Zeichenfolgen sein. In einer globalen Anwendung müssen Sie daher unter Umständen URIs unterstützen, die auch andere Zeichen als englische Zeichen enthalten.

- **Unterscheiden Sie Methoden, die nicht weitergeleitet werden sollen**.

	Wenn Sie das konventionsbasierte Routing verwenden, geben Sie Methoden an, die keinen HTTP-Aktionen entsprechen, indem Sie sie mit dem _NonAction_-Attribut versehen. Dies gilt normalerweise für Hilfsmethoden, die für die Verwendung mit anderen Methoden in einem Controller definiert sind. Mit diesem Attribut wird verhindert, dass diese Methoden von einer fehlgeleiteten HTTP-Anforderung abgeglichen und aufgerufen werden.

- **Berücksichtigen Sie die Vor- und Nachteile einer Platzierung der API in einer Unterdomäne**.

	Standardmäßig werden APIs von der ASP.NET-Web-API im Verzeichnis _/api_ einer Domäne angeordnet, z. B. \__http://www.adventure-works.com/api/orders_. Dieses Verzeichnis befindet sich in derselben Domäne wie alle anderen Dienste, die vom Host verfügbar gemacht werden. Es kann vorteilhaft sein, eine Web-API in ihre eigenen Unterdomänen aufzuteilen, die auf einem separaten Host ausgeführt werden (mit URIs wie \__http://api.adventure-works.com/orders_). Bei dieser Trennung können Sie die Web-API effektiver partitionieren und skalieren, ohne dass sich dies auf andere Webanwendungen oder Dienste auswirkt, die in der Domäne _www.adventure-works.com_ ausgeführt werden.

	Das Platzieren einer Web-API in einer anderen Unterdomäne kann aber auch zu Sicherheitsproblemen führen. Unter _www.adventure-works.com_ gehostete Webanwendungen oder Dienste, bei denen eine an einem anderen Ort ausgeführte Web-API aufgerufen wird, können die Richtlinie vieler Webbrowser zum identischen Ursprung verletzen. In diesem Fall ist es erforderlich, zwischen den Hosts das Cross-Origin Resource Sharing (CORS) zu ermöglichen. Weitere Informationen finden Sie im Leitfaden zur API-Sicherheit.

## Aspekte der Verarbeitung von Anforderungen

Nachdem eine Anforderung einer Clientanwendung erfolgreich an eine Methode in einer Web-API weitergeleitet wurde, muss die Anforderung so effizient wie möglich verarbeitet werden. Beachten Sie die folgenden Punkte, wenn Sie den Code zum Behandeln von Anforderungen implementieren:

- **GET-, PUT-, DELETE-, HEAD- und PATCH-Aktionen sollten idempotent sein**.

	Der Code, mit dem diese Anforderungen implementiert werden, sollte nicht mit Nebeneffekten verbunden sein. Eine Anforderung, die für eine Ressource wiederholt ausgeführt wird, sollte zum gleichen Ergebnis führen. Wenn Sie beispielsweise mehrere DELETE-Anforderungen an denselben URI senden, sollte dies immer die gleiche Auswirkung haben. Hierbei kann der HTTP-Statuscode in den Antwortnachrichten variieren (für die erste DELETE-Anforderung wird ggf. Statuscode 204 (No Content) zurückgegeben, während für eine nachfolgende DELETE-Anforderung Statuscode 404 (Nicht gefunden) zurückgegeben werden kann).

> [AZURE.NOTE]Der Artikel [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/) (Muster der Idempotenz) im Blog von Jonathan Oliver enthält eine Übersicht über die Idempotenz und ihre Verbindung mit Datenverwaltungsvorgängen.

- **Bei POST-Aktionen, mit denen neue Ressourcen erstellt werden, sollten keine nicht relevanten Nebeneffekte auftreten**.

	Wenn mit einer POST-Anforderung eine neue Ressource erstellt werden soll, sollten die Auswirkungen der Anforderung auf die neue Ressource beschränkt sein (sowie unter Umständen auf alle direkt verwandten Ressourcen, falls eine Verknüpfung besteht). In einem E-Commerce-System werden mit einer POST-Anforderung, mit der eine neue Bestellung für einen Kunden erstellt wird, ggf. auch Lagerbestände verändert und Rechnungsinformationen erzeugt. Es sollten aber keine Informationen geändert werden, die sich nicht direkt auf die Bestellung beziehen, und es sollten sich auch keine anderen Nebeneffekte für den Gesamtzustand des Systems ergeben.

- **Vermeiden Sie die Implementierung von umfangreichen POST-, PUT- und DELETE-Vorgängen**.

	Bauen Sie die Unterstützung von POST-, PUT- und DELETE-Anforderungen über Ressourcenauflistungen ein. Eine POST-Anforderung kann die Details für mehrere neue Ressourcen enthalten und diese derselben Auflistung hinzufügen. Mit einer PUT-Anforderung kann der gesamte Ressourcensatz in einer Auflistung ersetzt werden, und mit einer DELETE-Anforderung kann eine gesamte Auflistung entfernt werden.

	Beachten Sie, dass die in der ASP.NET-Web-API 2 enthaltene OData-Unterstützung das Zusammenfassen von Anforderungen in Batches ermöglicht. Eine Clientanwendung kann mehrere Web-API-Anforderungen verpacken und in einer einzelnen HTTP-Anforderung an den Server senden. Sie erhält dann eine einzelne HTTP-Antwort mit den Antworten für die einzelnen Anforderungen. Weitere Informationen finden Sie auf der Microsoft-Website unter [Einführung in die Batchunterstützung in der Web-API und Web-API-OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx).

- **Halten Sie sich an das HTTP-Protokoll, wenn Sie eine Antwort zurück an eine Clientanwendung senden**.

	Eine Web-API muss Nachrichten mit dem richtigen HTTP-Statuscode zurückgeben, damit der Client bestimmen kann, wie er das Ergebnis behandeln soll. Außerdem die richtigen HTTP-Header, damit der Client die Art des Ergebnisses versteht, sowie einen richtig formatierten Text, damit der Client das Ergebnis analysieren kann. Bei Verwendung der ASP.NET-Web-API-Vorlage besteht die Standardstrategie zum Implementieren von Methoden, die auf HTTP POST-Anforderungen antworten, einfach darin, eine Kopie der neu erstellten Ressource zurückzugeben. Dies ist im folgenden Beispiel dargestellt:

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers")]
	    [HttpPost]
	    public Customer CreateNewCustomer(Customer customerDetails)
	    {
	        // Add the new customer to the repository
	        // This method returns a customer with a unique ID allocated
	        // by the repository
	        var newCust = repository.Add(customerDetails);
	        // Return the newly added customer
	        return newCust;
	    }
	    ...
	}
	```

	Wenn der POST-Vorgang erfolgreich ist, erstellt das Web-API-Framework eine HTTP-Antwort mit dem Statuscode 200 (OK) und den Details des Kunden als Text der Nachricht. Gemäß HTTP-Protokoll sollte ein POST-Vorgang in diesem Fall aber den Statuscode 201 (Created) zurückgeben, und die Antwortnachricht sollte den URI der neu erstellten Ressource im Location-Header enthalten.

	Geben Sie Ihre eigene HTTP-Antwortnachricht zum Bereitstellen dieser Features über die `IHttpActionResult`-Schnittstelle an. Bei diesem Ansatz haben Sie eine gute Kontrolle über den HTTP-Statuscode, die Header in der Antwortnachricht und sogar das Format der Daten im Text der Antwortnachricht. Dies wird im folgenden Codebeispiel veranschaulicht. Diese Version der `CreateNewCustomer`-Methode liegt näher an den Erwartungen des Clients gemäß HTTP-Protokoll. Die `Created`-Methode der `ApiController`-Klasse erstellt die Antwortnachricht aus den angegebenen Daten und fügt den Location-Header den Ergebnissen hinzu:

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers")]
	    [HttpPost]
	    public IHttpActionResult CreateNewCustomer(Customer customerDetails)
	    {
	        // Add the new customer to the repository
	        var newCust = repository.Add(customerDetails);

	        // Create a value for the Location header to be returned in the response
	        // The URI should be the location of the customer including its ID,
	        // such as http://adventure-works.com/api/customers/99
	        var location = new Uri(...);

            // Return the HTTP 201 response,
            // including the location and the newly added customer
	        return Created(location, newCust);
	    }
	    ...
	}
	```

- **Unterstützen Sie die Inhaltsaushandlung**.

	Der Text einer Antwortnachricht kann Daten in unterschiedlichen Formaten enthalten. Bei einer HTTP GET-Anforderung können Daten beispielsweise im JSON- oder XML-Format zurückgegeben werden. Wenn der Client eine Anforderung sendet, kann diese einen Accept-Header zum Angeben der Datenformate enthalten, die behandelt werden können. Diese Formate werden als Medientypen angegeben. Ein Client, der eine GET-Anforderung zum Abrufen eines Bilds ausgibt, kann beispielsweise einen Accept-Header angeben, mit dem die für den Client verarbeitbaren Medientypen aufgeführt werden, z. B. „image/jpeg, image/gif, image/png“. Wenn die Web-API das Ergebnis zurückgibt, sollte sie die Daten mit einem dieser Medientypen formatieren und das Format im Content-Type-Header der Antwort angeben.

	Falls der Client keinen Accept-Header angibt, sollten Sie für den Text der Antwort ein geeignetes Standardformat verwenden. Beispiel: Das ASP.NET-Web-API-Framework nutzt für textbasierte Daten standardmäßig JSON.

	> [AZURE.NOTE]Das ASP.NET-Web-API-Framework führt eine automatische Erkennung von Accept-Headern durch und behandelt diese selbst basierend auf dem Typ der Daten im Text der Antwortnachricht. Wenn der Text einer Antwortnachricht beispielsweise ein CLR-Objekt (Common Language Runtime) enthält, formatiert die ASP.NET-Web-API die Antwort automatisch im JSON-Format, wobei der Content-Type-Header der Antwort auf „application/json“ festgelegt ist – es sei denn, der Client gibt an, dass er die Ergebnisse im XML-Format benötigt. In diesem Fall formatiert das ASP.NET-Web-API-Framework die Antwort im XML-Format und legt den Content-Type-Header der Antwort auf „text/xml“ fest. Es kann aber auch erforderlich sein, Accept-Header zu behandeln, für die verschiedene Medientypen explizit im Implementierungscode für einen Vorgang angegeben werden.

- **Stellen Sie Links zum Unterstützen der Navigation im HATEOAS-Stil und der Ermittlung von Ressourcen bereit**.

	Im Leitfaden zum API-Entwurf wird beschrieben, wie ein Client bei Verwendung des HATEOAS-Ansatzes navigieren und Ressourcen von einem anfänglichen Startpunkt aus ermitteln kann. Dies wird erreicht, indem Links mit URIs verwendet werden. Wenn ein Client eine HTTP GET-Anforderung zum Abrufen einer Ressource ausgibt, sollte die Antwort URIs enthalten, mit denen eine Clientanwendung alle direkt verwandten Ressourcen schnell finden kann. Es kann beispielsweise sein, dass ein Kunde in einer Web-API, die eine E-Commerce-Lösung unterstützt, viele Bestellungen aufgegeben hat. Wenn eine Clientanwendung die Details für einen Kunden abruft, sollte die Antwort Links enthalten, mit denen die Clientanwendung HTTP GET-Anforderungen zum Abrufen dieser Bestellungen senden kann. Darüber hinaus sollten mit Links im HATEOAS-Stil die anderen Vorgänge (POST, PUT, DELETE usw.) beschrieben werden, die von jeder verknüpften Ressource zusammen mit dem entsprechenden URI zum Durchführen der einzelnen Anforderungen unterstützt werden. Dieser Ansatz wird im Leitfaden zum API-Entwurf ausführlicher beschrieben.

	Derzeit gibt es keine Standards, mit denen die Implementierung von HATEOAS geregelt wird, aber im folgenden Beispiel wird ein möglicher Ansatz veranschaulicht. In diesem Beispiel wird mit einer HTTP GET-Anforderung zum Suchen nach den Details für einen Kunden eine Antwort zurückgegeben, die HATEOAS-Links enthält, mit denen auf die Bestellungen des Kunden verwiesen wird:

	```HTTP
	GET http://adventure-works.com/customers/2 HTTP/1.1
	Accept: text/json
	...
	```

	```HTTP
	HTTP/1.1 200 OK
	...
	Content-Type: application/json; charset=utf-8
	...
	Content-Length: ...
	{"CustomerID":2,"CustomerName":"Bert","Links":[
	  {"Relationship":"self",
	   "HRef":"http://adventure-works.com/customers/2",
	   "Action":"GET",
	   "LinkedResourceMIMETypes":["text/xml","application/json"]},
	  {"Relationship":"self",
	   "HRef":"http://adventure-works.com/customers/2",
	   "Action":"PUT",
	   "LinkedResourceMIMETypes":["application/x-www-form-urlencoded"]},
	  {"Relationship":"self",
	   "HRef":"http://adventure-works.com/customers/2",
	   "Action":"DELETE",
	   "LinkedResourceMIMETypes":[]},
	  {"Relationship":"orders",
	   "HRef":"http://adventure-works.com/customers/2/orders",
	   "Action":"GET",
	   "LinkedResourceMIMETypes":["text/xml","application/json"]},
	  {"Relationship":"orders",
	   "HRef":"http://adventure-works.com/customers/2/orders",
	   "Action":"POST",
	   "LinkedResourceMIMETypes":["application/x-www-form-urlencoded"]}
	]}
	```

	Die Kundendaten werden hierbei mit der `Customer`-Klasse dargestellt, wie im folgenden Codeausschnitt zu sehen ist. Die HATEOAS-Links sind in der `Links`-Auflistungseigenschaft enthalten:

	```C#
	public class Customer
	{
    	public int CustomerID { get; set; }
    	public string CustomerName { get; set; }
    	public List<Link> Links { get; set; }
    	...
	}

	public class Link
	{
    	public string Relationship { get; set; }
    	public string HRef { get; set; }
    	public string Action { get; set; }
    	public string [] LinkedResourceMIMETypes { get; set; }
	}
	```

	Der HTTP GET-Vorgang ruft die Kundendaten aus dem Speicher ab und erstellt ein `Customer`-Objekt und füllt anschließend die `Links`-Auflistung auf. Das Ergebnis ist als JSON-Antwortnachricht formatiert. Jeder Link umfasst die folgenden Felder:

	- Die Beziehung zwischen dem zurückgegebenen Objekt und dem Objekt, das vom Link beschrieben wird. Hierbei wird mit „self“ angegeben, dass der Link ein Verweis zurück auf das Objekt selbst ist (ähnlich wie bei einem `this`-Zeiger in vielen objektorientierten Sprachen), und „orders“ ist der Name einer Auflistung, in der die dazugehörigen Bestellinformationen enthalten sind.

	- Der Hyperlink (`HRef`) für das Objekt, das mit dem Link beschrieben wird, in Form eines URI.

	- Der Typ der HTTP-Anforderung (`Action`), die an diesen URI gesendet werden kann.

	- Das Format der Daten (`LinkedResourceMIMETypes`), die in der HTTP-Anforderung bereitgestellt werden sollen oder die je nach Art der Anforderung in der Antwort zurückgegeben werden können.

	Mit den HATEOAS-Links in der HTTP-Beispielantwort wird angegeben, dass eine Clientanwendung die folgenden Vorgänge durchführen kann:

	- Eine HTTP GET-Anforderung an den URI \__http://adventure-works.com/customers/2_, um die Details des Kunden (erneut) abzurufen. Die Daten können im XML- oder JSON-Format zurückgegeben werden.

	- Eine HTTP PUT-Anforderung an den URI \__http://adventure-works.com/customers/2_, um die Details des Kunden zu ändern. Die neuen Daten müssen in der Anforderungsnachricht im Format „x-www-form-urlencoded“ bereitgestellt werden.

	- Eine HTTP DELETE-Anforderung an den URI \__http://adventure-works.com/customers/2_, um den Kunden zu löschen. Die Anforderung erwartet keine zusätzlichen Informationen oder Rückgabedaten im Text der Antwortnachricht.

	- Eine HTTP GET-Anforderung an den URI \__http://adventure-works.com/customers/2/orders_, um nach allen Bestellungen des Kunden zu suchen. Die Daten können im XML- oder JSON-Format zurückgegeben werden.

	- Eine HTTP PUT-Anforderung an den URI \__http://adventure-works.com/customers/2/orders_, um für den Kunden eine neue Bestellung zu erstellen. Die Daten müssen in der Anforderungsnachricht im Format „x-www-form-urlencoded“ bereitgestellt werden.

## Aspekte der Behandlung von Ausnahmen
Wenn ein Vorgang im ASP.NET-Web-API-Framework eine unerwartete Ausnahme auslöst, gibt das Framework standardmäßig eine Antwortnachricht mit dem HTTP-Statuscode 500 (Internal Server Error) zurück. Häufig ist dieser allzu simple Ansatz allein nicht hilfreich und erschwert die Ermittlung der Ausnahmeursache. Daher sollten Sie zum Behandeln von Ausnahmen einen umfassenderen Ansatz wählen. Beachten Sie hierbei die folgenden Punkte:

- **Erfassen Sie Ausnahmen, und geben Sie eine aussagekräftige Antwort an Clients zurück**.

	Mit dem Code zum Implementieren eines HTTP-Vorgangs sollte eine umfassende Ausnahmebehandlung bereitgestellt werden, anstatt zuzulassen, dass unerwartete Ausnahmen ins Web-API-Framework gelangen. Wenn eine Ausnahme den erfolgreichen Abschluss des Vorgangs verhindert, kann die Ausnahme in der Antwortnachricht zurück übergeben werden. Sie sollte aber eine aussagekräftige Beschreibung des Fehlers enthalten, der die Ausnahme verursacht hat. Außerdem sollte die Ausnahme den passenden HTTP-Statuscode enthalten, anstatt einfach für jede Situation den Statuscode 500 zurückzugeben. Wenn eine Benutzeranforderung beispielsweise eine Datenbankaktualisierung bewirkt, die eine Verletzung einer Einschränkung darstellt (z. B. das versuchte Löschen eines Kunden, für den noch Bestellungen ausstehen), sollten Sie den Statuscode 409 (Conflict) und einen Nachrichtentext zurückgeben, um die Ursache des Konflikts anzugeben. Falls die Anforderung aufgrund einer anderen Bedingung nicht durchführbar ist, können Sie den Statuscode 400 (Bad Request) zurückgeben. Eine vollständige Liste mit HTTP-Statuscodes finden Sie auf der W3C-Website im Thema zu den [Statuscodedefinitionen](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

	Der folgende Code enthält ein Beispiel, bei dem unterschiedliche Bedingungen abgefangen werden und eine geeignete Antwort zurückgegeben wird.

	```C#
	[HttpDelete]
	[Route("customers/{id:int}")]
	public IHttpActionResult DeleteCustomer(int id)
	{
		try
		{
			// Find the customer to be deleted in the repository
			var customerToDelete = repository.GetCustomer(id);

			// If there is no such customer, return an error response
			// with status code 404 (Not Found)
			if (customerToDelete == null)
			{
					return NotFound();
			}

			// Remove the customer from the repository
			// The DeleteCustomer method returns true if the customer
			// was successfully deleted
			if (repository.DeleteCustomer(id))
			{
				// Return a response message with status code 204 (No Content)
				// To indicate that the operation was successful
				return StatusCode(HttpStatusCode.NoContent);
			}
			else
			{
				// Otherwise return a 400 (Bad Request) error response
				return BadRequest(Strings.CustomerNotDeleted);
			}
		}
		catch
		{
			// If an uncaught exception occurs, return an error response
			// with status code 500 (Internal Server Error)
			return InternalServerError();
		}
	}
	```

	> [AZURE.TIP]Fügen Sie keine Informationen ein, die für Angreifer beim Eindringen in Ihre Web-API nützlich sein könnten. Weitere Informationen finden Sie auf der Microsoft-Website auf der Seite [Ausnahmebehandlung in der ASP.NET-Web-API](http://www.asp.net/web-api/overview/error-handling/exception-handling).

	> [AZURE.NOTE]Viele Webserver fangen Fehlerbedingungen selbst ab, bevor sie die Web-API erreichen. Wenn Sie beispielsweise die Authentifizierung für eine Website konfigurieren und der Benutzer keine richtigen Authentifizierungsinformationen angibt, sollte der Webserver mit dem Statuscode 401 (Unauthorized) antworten. Nachdem ein Client authentifiziert wurde, kann Ihr Code eigene Überprüfungen durchführen, um zu bestätigen, dass der Client auf die angeforderte Ressource zugreifen kann. Wenn diese Autorisierung nicht erfolgreich ist, sollten Sie den Statuscode 403 (Forbidden) zurückgeben.

- **Behandeln Sie Ausnahmen auf konsistente Weise, und protokollieren Sie Informationen zu Fehlern**.

	Erwägen Sie zum einheitlichen Behandeln von Ausnahmen die Implementierung einer globalen Strategie zur Fehlerbehandlung in der gesamten Web-API. Dies erreichen Sie teilweise wie folgt: Sie erstellen einen Ausnahmefilter, der immer ausgeführt wird, wenn ein Controller eine unbehandelte Ausnahme auslöst, bei der es sich nicht um eine `HttpResponseException`-Ausnahme handelt. Dieser Ansatz ist auf der Microsoft-Website auf der Seite [Ausnahmebehandlung in der ASP.NET-Web-API](http://www.asp.net/web-api/overview/error-handling/exception-handling) beschrieben.

	Es gibt jedoch mehrere Situationen, in denen ein Ausnahmefilter eine Ausnahme nicht abfängt, z. B.:

	- Von Controllerkonstruktoren ausgelöste Ausnahmen

	- Von Meldungshandlern ausgelöste Ausnahmen

	- Während des Routings ausgelöste Ausnahmen

	- Beim Serialisieren des Inhalts für eine Antwortnachricht ausgelöste Ausnahmen

	Zum Behandeln dieser Fälle müssen Sie ggf. einen stärker angepassten Ansatz implementieren. Außerdem sollten Sie für eine Fehlerprotokollierung sorgen, bei der für jede Ausnahme alle Details erfasst werden. Dieses Fehlerprotokoll kann ausführliche Informationen enthalten, sofern es nicht für Clients über das Web zugänglich ist. Auf der Microsoft-Website wird im Artikel [Globale Fehlerbehandlung für die Web-API](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) eine mögliche Vorgehensweise für diese Aufgabe veranschaulicht.

- **Unterscheiden Sie zwischen clientseitigen und serverseitigen Fehlern**.

	Im HTTP-Protokoll wird zwischen Fehlern unterschieden, die aufgrund der Clientanwendung auftreten (HTTP 4xx-Statuscodes), und Fehlern, die aufgrund eines Problems auf dem Server auftreten (HTTP 5xx-Statuscodes). Achten Sie darauf, dass Sie diese Konvention in allen Fehlerantwortnachrichten befolgen.

<a name="considerations-for-optimizing"></a>
## Aspekte der Optimierung des clientseitigen Datenzugriffs

In einer verteilten Umgebung, z. B. mit einem Webserver und Clientanwendungen, ist das Netzwerk eines der Elemente, die am stärksten beachtet werden müssen. Es können sich erhebliche Engpässe ergeben, und zwar vor allem, wenn eine Clientanwendung häufig Anforderungen sendet oder Daten empfängt. Daher sollten Sie versuchen, die Menge des im Netzwerk übertragenen Datenverkehrs möglichst zu verringern. Beachten Sie beim Implementieren des Codes zum Abrufen und Verwalten von Daten die folgenden Punkte:

- **Unterstützen Sie die clientseitige Zwischenspeicherung**.

	Das HTTP 1.1-Protokoll unterstützt die Zwischenspeicherung auf Clients und Zwischenservern, über die eine Anforderung mit Cache-Control-Header weitergeleitet wird. Wenn eine Clientanwendung eine HTTP GET-Anforderung an die Web-API sendet, kann die Antwort einen Cache-Control-Header enthalten. Hiermit wird angegeben, ob die Daten im Text der Antwort vom Client oder einem Zwischenserver, über den die Anforderung weitergeleitet wurde, sicher zwischengespeichert werden können. Außerdem wird angegeben, nach welchem Zeitraum die Daten ablaufen und als veraltet angesehen werden. Das folgende Beispiel enthält eine HTTP GET-Anforderung und die dazugehörige Antwort mit einem Cache-Control-Header:

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	...
	```

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	Content-Length: ...
	{"OrderID":2,"ProductID":4,"Quantity":2,"OrderValue":10.00}
	```

	In diesem Beispiel wird mit dem Cache-Control-Header angegeben, dass die zurückgegebenen Daten nach 600 Sekunden ablaufen sollen. Außerdem sind sie nur für einen einzelnen Client geeignet und dürfen nicht in einem freigegebenen Cache gespeichert werden, der von anderen Clients verwendet wird (sie sind _private_). Im Cache-Control-Header kann anstelle von _private_ auch _public_ angegeben werden, damit die Daten in einem freigegebenen Cache gespeichert werden können. Oder es kann _no-store_ angegeben werden, wenn die Daten vom Client **nicht** zwischengespeichert werden dürfen. Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen Cache-Control-Header in einer Antwortnachricht erstellen:

	```C#
	public class OrdersController : ApiController
	{
    	...
    	[Route("api/orders/{id:int:min(0)}")]
    	[HttpGet]
    	public IHttpActionResult FindOrderByID(int id)
    	{
    		// Find the matching order
    		Order order = ...;
    		...
    		// Create a Cache-Control header for the response
    		var cacheControlHeader = new CacheControlHeaderValue();
    		cacheControlHeader.Private = true;
    		cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
    		...

    		// Return a response message containing the order and the cache control header
    		OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
    		{
    			CacheControlHeader = cacheControlHeader
    		};
    		return response;
    	}
    	...
	}
	```

	In diesem Code wird eine benutzerdefinierte `IHttpActionResult`-Klasse mit dem Namen `OkResultWithCaching` verwendet. Diese Klasse ermöglicht dem Controller das Festlegen des Cacheheaderinhalts:

	```C#
	public class OkResultWithCaching<T> : OkNegotiatedContentResult<T>
    {
        public OkResultWithCaching(T content, ApiController controller)
            : base(content, controller) { }

        public OkResultWithCaching(T content, IContentNegotiator contentNegotiator, HttpRequestMessage request, IEnumerable<MediaTypeFormatter> formatters)
            : base(content, contentNegotiator, request, formatters) { }

        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }

        public override async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = await base.ExecuteAsync(cancellationToken);

            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = ETag;

            return response;
        }
    }
	```

	> [AZURE.NOTE]Außerdem wird im HTTP-Protokoll auch die _no-cache_-Direktive für den Cache-Control-Header definiert. Es ist etwas verwirrend, dass diese Direktive nicht etwa „nicht zwischenspeichern“, sondern „zwischengespeicherte Informationen vor dem Zurückgeben per Server neu bewerten“ bedeutet. Die Daten können zwar zwischengespeichert werden, aber sie werden bei jeder Verwendung überprüft, um sicherzustellen, dass sie noch aktuell sind.

	Für die Cacheverwaltung ist die Clientanwendung oder der Zwischenserver verantwortlich, aber bei einer richtigen Implementierung kann Bandbreite gespart und die Leistung verbessert werden. Zu diesem Zweck wird verhindert, dass keine Daten mehr abgerufen werden müssen, die bereits vorher abgerufen wurden.

	Der _max-age_-Wert im Cache-Control-Header ist nur ein Anhaltspunkt und keine Garantie, dass sich die entsprechenden Daten während des angegebenen Zeitraums nicht ändern. Die Web-API sollte den max-age-Wert je nach der erwarteten Volatilität der Daten auf einen geeigneten Wert festlegen. Wenn dieser Zeitraum abgelaufen ist, sollte der Client das Objekt aus dem Cache entfernen.

	> [AZURE.NOTE]Die meisten modernen Webbrowser unterstützen die clientseitige Zwischenspeicherung, indem Anforderungen die passenden Cache-Control-Header hinzugefügt werden und die Header der Ergebnisse (wie beschrieben) untersucht werden. Einige ältere Browser speichern aber keine Werte zwischen, die über eine URL mit einer Abfragezeichenfolge zurückgegeben werden. Normalerweise ist dies kein Problem für benutzerdefinierte Clientanwendungen, bei denen basierend auf dem hier beschriebenen Protokoll eine eigene Strategie zur Cacheverwaltung implementiert wird.
	>
	> Einige ältere Proxys weisen das gleiche Verhalten auf und speichern unter Umständen keine Anforderungen zwischen, die auf URLs mit Abfragezeichenfolgen basieren. Dies kann für benutzerdefinierte Clientanwendungen ein Problem darstellen, die über einen Proxy dieser Art eine Verbindung mit einem Webserver herstellen.

- **Stellen Sie ETags zum Optimieren der Abfrageverarbeitung bereit**.

	Wenn eine Clientanwendung ein Objekt abruft, kann die Antwortnachricht auch ein _ETag_ (Entitätstag) enthalten. Ein ETag ist eine opake Zeichenfolge, mit der die Version einer Ressource angegeben wird. Wenn eine Ressource geändert wird, wird auch das ETag geändert. Dieses ETag sollte von der Clientanwendung als Teil der Daten zwischengespeichert werden. Im folgenden Codebeispiel wird veranschaulicht, wie Sie einer HTTP GET-Anforderung ein ETag als Teil der Antwort hinzufügen. In diesem Code wird die `GetHashCode`-Methode eines Objekts zum Generieren eines numerischen Werts verwendet, mit dem das Objekt identifiziert wird (Sie können diese Methode bei Bedarf außer Kraft setzen und einen eigenen Hashwert generieren, indem Sie einen Algorithmus verwenden, z. B. MD5):

	```C#
	public class OrdersController : ApiController
	{
    	...
    	public IHttpActionResult FindOrderByID(int id)
    	{
    		// Find the matching order
    		Order order = ...;
    		...

    		var hashedOrder = order.GetHashCode();
    		string hashedOrderEtag = String.Format(""{0}"", hashedOrder);
    		var eTag = new EntityTagHeaderValue(hashedOrderEtag);

    		// Return a response message containing the order and the cache control header
    		OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
    		{
    			...,
    			ETag = eTag
    		};
    		return response;
    	}
    	...
    }
	```

	Die von der Web-API bereitgestellte Antwortnachricht sieht wie folgt aus:

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	ETag: "2147483648"
	Content-Length: ...
	{"OrderID":2,"ProductID":4,"Quantity":2,"OrderValue":10.00}
	```

	> [AZURE.TIP]Aus Sicherheitsgründen sollten Sie nicht zulassen, dass sensible Daten oder Daten, die über eine authentifizierte Verbindung (HTTPS) zurückgegeben werden, zwischengespeichert werden.

	Eine Clientanwendung kann eine nachfolgende GET-Anforderung ausgeben, um jederzeit dieselbe Ressource abzurufen. Wenn sich die Ressource geändert hat (also ein anderes ETag aufweist), sollte die zwischengespeicherte Version verworfen und die neue Version dem Cache hinzugefügt werden. Falls eine Ressource umfangreich ist und für die Übertragung zurück auf den Client eine erhebliche Menge an Bandbreite erfordert, kann die Verwendung wiederholter Anforderungen zum Abrufen derselben Daten ineffizient werden. Als Lösung definiert das HTTP-Protokoll den folgenden Prozess zum Optimieren von GET-Anforderungen, die Sie in einer Web-API unterstützen sollten:

	- Der Client erstellt eine GET-Anforderung mit dem ETag für die derzeit zwischengespeicherte Version der Ressource, auf die in einem If-None-Match-HTTP-Header verwiesen wird:

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	If-None-Match: "2147483648"
	...
	```

	- Der GET-Vorgang in der Web-API ruft das aktuelle ETag für die angeforderten Daten ab („Order 2“ im obigen Beispiel) und vergleicht es mit dem Wert im If-None-Match-Header.

	- Wenn das aktuelle ETag für die angeforderten Daten mit dem von der Anforderung bereitgestellten ETag übereinstimmt, hat sich die Ressource nicht geändert. Die Web-API sollte also eine HTTP-Antwort mit leerem Nachrichtentext und dem Statuscode 304 (Not Modified) zurückgeben.

	- Wenn das aktuelle ETag für die angeforderten Daten nicht mit dem von der Anforderung bereitgestellten ETag übereinstimmt, haben sich die Daten geändert. Die Web-API sollte also eine HTTP-Antwort mit den neuen Daten im Nachrichtentext und dem Statuscode 200 (OK) zurückgeben.

	- Falls die angeforderten Daten nicht mehr vorhanden sind, sollte die Web-API eine HTTP-Antwort mit dem Statuscode 404 (Nicht gefunden) zurückgeben.

	- Der Client verwendet den Statuscode zum Verwalten des Cache. Wenn sich die Daten nicht geändert haben (Statuscode 304), kann das Objekt zwischengespeichert bleiben, und die Clientanwendung sollte weiterhin diese Version des Objekts nutzen. Wenn sich die Daten geändert haben (Statuscode 200), sollte das zwischengespeicherte Objekt verworfen und das neue Objekt eingefügt werden. Falls die Daten nicht mehr verfügbar sind (Statuscode 404), sollte das Objekt aus dem Cache entfernt werden.

	> [AZURE.NOTE]Wenn der Antwortheader den Cache-Control-Header „no-store“ enthält, sollte das Objekt unabhängig vom HTTP-Statuscode aus dem Cache entfernt werden.

	Im Code unten wird die erweiterte `FindOrderByID`-Methode zum Unterstützen des If-None-Match-Headers veranschaulicht. Beachten Sie Folgendes: Wenn der If-None-Match-Header weggelassen wird, wird immer die angegebene Bestellung abgerufen:

	```C#
	public class OrdersController : ApiController
	{
   		...
    	[Route("api/orders/{id:int:min(0)}")]
    	[HttpGet]
    	public IHttpActionResult FindOrderById(int id)
        {
            try
            {
                // Find the matching order
    		    Order order = ...;

                // If there is no such order then return NotFound
                if (order == null)
                {
                    return NotFound();
                }

                // Generate the ETag for the order
                var hashedOrder = order.GetHashCode();
                string hashedOrderEtag = String.Format(""{0}"", hashedOrder);

                // Create the Cache-Control and ETag headers for the response
                IHttpActionResult response = null;
                var cacheControlHeader = new CacheControlHeaderValue();
                cacheControlHeader.Public = true;
                cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
                var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                // Retrieve the If-None-Match header from the request (if it exists)
                var nonMatchEtags = Request.Headers.IfNoneMatch;

                // If there is an ETag in the If-None-Match header and
                // this ETag matches that of the order just retrieved,
                // then create a Not Modified response message
                if (nonMatchEtags.Count > 0 &&
                    String.Compare(nonMatchEtags.First().Tag, hashedOrderEtag) == 0)
                {
                    response = new EmptyResultWithCaching()
                    {
                        StatusCode = HttpStatusCode.NotModified,
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag
                    };
                }
                // Otherwise create a response message that contains the order details
                else
                {
                    response = new OkResultWithCaching<Order>(order, this)
                    {
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag
                    };
                }

                return response;
            }
            catch
            {
                return InternalServerError();
            }
        }
    ...
    }
	```

	Dieses Beispiel enthält eine zusätzliche benutzerdefinierte `IHttpActionResult`-Klasse mit dem Namen `EmptyResultWithCaching`. Diese Klasse dient lediglich als Wrapper für ein `HttpResponseMessage`-Objekt ohne Antworttext:

	```C#
    public class EmptyResultWithCaching : IHttpActionResult
    {
        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }
        public HttpStatusCode StatusCode { get; set; }
		public Uri Location { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = new HttpResponseMessage(StatusCode);
            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = this.ETag;
            response.Headers.Location = this.Location;
            return response;
        }
    }
	```

	> [AZURE.TIP]In diesem Beispiel wird das ETag für die Daten generiert, indem die Daten, die aus der zugrunde liegenden Datenquelle abgerufen werden, mit einem Hashwert versehen werden. Wenn das ETag auf andere Art berechnet werden kann, kann der Prozess weiter optimiert werden, und die Daten müssen nur dann aus der Datenquelle abgerufen werden, wenn sie sich geändert haben. Dieser Ansatz ist besonders nützlich, wenn die Daten sehr umfangreich sind oder das Zugreifen auf die Datenquelle zu einer längeren Wartezeit führen kann (beispielsweise bei einer Remotedatenbank als Datenquelle).

- **Verwenden Sie ETags zum Unterstützen der optimistischen Parallelität**.

	Zum Ermöglichen von Updates für zuvor zwischengespeicherte Daten unterstützt das HTTP-Protokoll die Strategie der optimistischen Parallelität. Wenn die Clientanwendung nach dem Abrufen und Zwischenspeichern einer Ressource nachfolgend eine PUT- oder DELETE-Anforderung sendet, um die Ressource zu ändern oder zu entfernen, sollte diese einen If-Match-Header mit Verweis auf das ETag enthalten. Mit diesen Informationen kann die Web-API dann bestimmen, ob die Ressource bereits von einem anderen Benutzer geändert wurde, seitdem sie abgerufen wurde, und wie folgt eine geeignete Antwort zurück an die Clientanwendung senden:

	- Der Client erstellt eine PUT-Anforderung mit den neuen Details für die Ressource und dem ETag für die derzeit zwischengespeicherte Version der Ressource, auf die in einem If-Match-HTTP-Header verwiesen wird. Das folgende Beispiel enthält eine PUT-Anforderung, mit der eine Bestellung aktualisiert wird:

	```HTTP
	PUT http://adventure-works.com/orders/1 HTTP/1.1
	If-None-Match: "2282343857"
	Content-Type: application/x-www-form-urlencoded
	...
	Date: Fri, 12 Sep 2014 09:18:37 GMT
	Content-Length: ...
	ProductID=3&Quantity=5&OrderValue=250
	```

	- Der PUT-Vorgang in der Web-API ruft das aktuelle ETag für die angeforderten Daten ab („Order 1“ im obigen Beispiel) und vergleicht es mit dem Wert im If-Match-Header.

	- Wenn das aktuelle ETag für die angeforderten Daten mit dem von der Anforderung bereitgestellten ETag übereinstimmt, hat sich die Ressource nicht geändert. Die Web-API sollte also das Update durchführen und eine Nachricht mit dem HTTP-Statuscode 204 (No Content) zurückgeben, wenn der Vorgang erfolgreich ist. Die Antwort kann Cache-Control- und ETag-Header für die aktualisierte Version der Ressource enthalten. Die Antwort sollte immer den Location-Header enthalten, mit dem auf den URI der gerade aktualisierten Ressource verwiesen wird.

	- Wenn das aktuelle ETag für die angeforderten Daten nicht mit dem von der Anforderung bereitgestellten ETag übereinstimmt, wurden die Daten von einem anderen Benutzer geändert, seitdem sie abgerufen wurden. Die Web-API sollte eine HTTP-Antwort mit einem leeren Nachrichtentext und dem Statuscode 412 (Precondition Failed) zurückgeben.

	- Falls die zu aktualisierende Ressource nicht mehr vorhanden ist, sollte die Web-API eine HTTP-Antwort mit dem Statuscode 404 (Nicht gefunden) zurückgeben.

	- Der Client nutzt den Statuscode und die Antwortheader zum Verwalten des Cache. Wenn die Daten aktualisiert wurden (Statuscode 204), kann das Objekt zwischengespeichert bleiben (sofern im Cache-Control-Header nicht „no-store“ angegeben ist), aber das ETag muss aktualisiert werden. Falls die Daten von einem anderen Benutzer geändert (Statuscode 412) oder Nicht gefunden wurden (Statuscode 404), sollte das zwischengespeicherte Objekt verworfen werden.

	Im nächsten Codebeispiel wird eine Implementierung des PUT-Vorgangs für den Orders-Controller veranschaulicht:

	```C#
	public class OrdersController : ApiController
	{
   		...
    	[HttpPut]
    	[Route("api/orders/{id:int}")]
    	        public IHttpActionResult UpdateExistingOrder(int id, DTOOrder order)
        {
            try
            {
                var baseUri = Constants.GetUriFromConfig();
                var orderToUpdate = this.ordersRepository.GetOrder(id);
                if (orderToUpdate == null)
                {
                    return NotFound();
                }

                var hashedOrder = orderToUpdate.GetHashCode();
                string hashedOrderEtag = String.Format(""{0}"", hashedOrder);

                // Retrieve the If-Match header from the request (if it exists)
                var matchEtags = Request.Headers.IfMatch;

                // If there is an Etag in the If-Match header and
                // this etag matches that of the order just retrieved,
                // or if there is no etag, then update the Order
                if (((matchEtags.Count > 0 &&
                     String.Compare(matchEtags.First().Tag, hashedOrderEtag) == 0)) ||
                     matchEtags.Count == 0)
                {
                    // Modify the order
                    orderToUpdate.OrderValue = order.OrderValue;
                    orderToUpdate.ProductID = order.ProductID;
                    orderToUpdate.Quantity = order.Quantity;

                    // Save the order back to the data store
                    // ...

                    // Create the No Content response with Cache-Control, ETag, and Location headers
                    var cacheControlHeader = new CacheControlHeaderValue();
                    cacheControlHeader.Private = true;
                    cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);

                    hashedOrder = order.GetHashCode();
                    hashedOrderEtag = String.Format(""{0}"", hashedOrder);
                    var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                    var location = new Uri(string.Format("{0}/{1}/{2}", baseUri, Constants.ORDERS, id));
                    var response = new EmptyResultWithCaching()
                    {
                        StatusCode = HttpStatusCode.NoContent,
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag,
                        Location = location
                    };

                    return response;
                }

                // Otherwise return a Precondition Failed response
                return StatusCode(HttpStatusCode.PreconditionFailed);
            }
            catch
            {
                return InternalServerError();
            }
        }
        ...
    }
	```

	> [AZURE.TIP]Die Verwendung des If-Match-Headers ist absolut optional. Wenn er weggelassen wird, versucht die Web-API stets, die angegebene Bestellung zu aktualisieren. Hierbei kann es unter Umständen vorkommen, dass ein Update eines anderen Benutzers versehentlich überschrieben wird. Geben Sie zur Vermeidung von Problemen aufgrund von verloren gegangenen Updates immer einen If-Match-Header an.

<a name="considerations-for-handling-large"></a>
## Aspekte zur Behandlung umfangreicher Anforderungen und Antworten

Wenn eine Clientanwendung Anforderungen ausgibt, bei denen Daten gesendet oder empfangen werden, kann es vorkommen, dass diese mehrere Megabyte groß (oder noch größer) sind. Das Warten auf den Abschluss der Übertragung dieser Datenmenge kann dazu führen, dass die Clientanwendung nicht mehr reagiert. Beachten Sie die folgenden Punkte, wenn Sie Anforderungen behandeln müssen, die größere Datenmengen enthalten:

- **Optimieren Sie Anforderungen und Antworten, die große Objekte enthalten**.

	Einige Ressourcen können große Objekte sein oder große Felder enthalten, z. B. Grafiken oder andere Arten von Binärdaten. Eine Web-API sollte das Streamen unterstützen, um das Hoch- und Herunterladen dieser Ressourcen zu optimieren.

	Das HTTP-Protokoll stellt das Verfahren für die segmentierte Transfercodierung bereit, mit dem große Datenobjekte zurück auf einen Client gestreamt werden können. Wenn der Client eine HTTP GET-Anforderung für ein großes Objekt sendet, kann die Web-API die Antwort in einzelnen _Segmenten_ („Chunks“) über eine HTTP-Verbindung zurücksenden. Die Länge der Daten in der Antwort ist am Anfang unter Umständen noch nicht bekannt (ggf. werden sie erst generiert). Der Server, auf dem die Web-API gehostet wird, sollte also mit jedem Segment eine Antwortnachricht senden, für die der Header „Transfer-Encoding: Chunked“ angegeben ist, und kein Content-Length-Header. Die Clientanwendung kann die einzelnen Segmente empfangen, um die vollständige Antwort zusammenzusetzen. Die Datenübertragung ist abgeschlossen, wenn der Server das letzte Segment mit einer Größe von null zurücksendet. Sie können das „Chunking“ in der ASP.NET-Web-API implementieren, indem Sie die `PushStreamContent`-Klasse verwenden.

	Das folgende Beispiel enthält einen Vorgang, mit dem auf HTTP GET-Anforderungen für die Produktbilder geantwortet wird:

	```C#
	public class ProductImagesController : ApiController
	{
    	...
    	[HttpGet]
        [Route("productimages/{id:int}")]
        public IHttpActionResult Get(int id)
        {
            try
            {
                var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);

                if (!BlobExists(container, string.Format("image{0}.jpg", id)))
                {
                    return NotFound();
                }
                else
                {
                    return new FileDownloadResult()
                    {
                        Container = container,
                        ImageId = id
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
    	...
	}
	```

	In diesem Beispiel ist `ConnectBlobToContainer` eine Hilfsmethode, mit der eine Verbindung mit einem angegebenen Container (Name wird nicht angezeigt) im Azure-Blobspeicher hergestellt wird. `BlobExists` ist eine weitere Hilfsmethode zum Zurückgeben eines booleschen Werts, mit dem angegeben wird, ob ein Blob mit dem angegebenen Namen im Blobspeichercontainer vorhanden ist.

	Für jedes Produkt ist das eigene Bild im Blobspeicher enthalten. Die `FileDownloadResult`-Klasse ist eine benutzerdefinierte `IHttpActionResult`-Klasse, bei der ein `PushStreamContent`-Objekt zum Lesen der Bilddaten aus dem entsprechenden Blob und asynchronen Übertragen als Inhalt der Antwortnachricht verwendet wird:

	```C#
	public class FileDownloadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            response.Content = new PushStreamContent(async (outputStream, _, __) =>
            {
                try
                {
                    CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
                    await blockBlob.DownloadToStreamAsync(outputStream);
                }
                finally
                {
                    outputStream.Close();
                }
            });

            response.StatusCode = HttpStatusCode.OK;
            response.Content.Headers.ContentType = new MediaTypeHeaderValue("image/jpeg");
            return response;
        }
    }
	```

	Sie können auch das Streamen zum Hochladen von Vorgängen verwenden, wenn ein Client eine neue Ressource bereitstellen muss (POST), die ein großes Objekt enthält. Im nächsten Beispiel wird die Post-Methode für den `ProductImages`-Controller veranschaulicht. Diese Methode ermöglicht dem Client das Hochladen eines neuen Produktbilds:

	```C#
	public class ProductImagesController : ApiController
	{
    	...
        [HttpPost]
        [Route("productimages")]
        public async Task<IHttpActionResult> Post()
        {
            try
            {
                if (!Request.Content.Headers.ContentType.MediaType.Equals("image/jpeg"))
                {
                    return StatusCode(HttpStatusCode.UnsupportedMediaType);
                }
                else
                {
                    var id = new Random().Next(); // Use a random int as the key for the new resource. Should probably check that this key has not already been used
                    var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);
                    return new FileUploadResult()
                    {
                        Container = container,
                        ImageId = id,
                        Request = Request
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
    	...
	}
	```

	In diesem Code wird eine andere benutzerdefinierte `IHttpActionResult`-Klasse mit dem Namen `FileUploadResult` verwendet. Diese Klasse enthält die Logik zum asynchronen Hochladen der Daten:

	```C#
    public class FileUploadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }
        public HttpRequestMessage Request { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
            await blockBlob.UploadFromStreamAsync(await Request.Content.ReadAsStreamAsync());
            var baseUri = string.Format("{0}://{1}:{2}", Request.RequestUri.Scheme, Request.RequestUri.Host, Request.RequestUri.Port);
            response.Headers.Location = new Uri(string.Format("{0}/productimages/{1}", baseUri, ImageId));
            response.StatusCode = HttpStatusCode.OK;
            return response;
        }
    }
	```

	> [AZURE.TIP]Die Datenmenge, die Sie für einen Webdienst hochladen können, ist beim Streamen nicht beschränkt. Eine einzelne Anforderung kann also zu einem riesigen Objekt führen, das Ressourcen in erheblichem Umfang verbraucht. Wenn die Web-API während des Streamingvorgangs ermittelt, dass die Menge der Daten in einer Anforderung nicht mehr akzeptabel ist, kann sie den Vorgang abbrechen und eine Antwortnachricht mit dem Statuscode 413 (Request Entity Too Large) zurückgeben.

	Sie können die Größe von großen Objekten, die über das Netzwerk übertragen werden, per HTTP-Komprimierung verringern. Dieser Ansatz ist hilfreich, um die Menge des Datenverkehrs im Netzwerk und die damit verbundene Netzwerklatenz zu reduzieren. Der Nachteil ist, dass auf dem Client und dem Server, auf dem die Web-API gehostet wird, zusätzlicher Verarbeitungsaufwand anfällt. Eine Clientanwendung, die den Eingang von komprimierten Daten erwartet, kann beispielsweise den Anforderungsheader „Accept-Encoding: gzip“ einfügen (auch andere Algorithmen zur Datenkomprimierung können angegeben werden). Wenn der Server die Komprimierung unterstützt, sollte die Antwort den Inhalt im gzip-Format im Nachrichtentext sowie den Antwortheader „Content-Encoding: gzip“ enthalten.

	> [AZURE.TIP]Sie können die codierte Komprimierung mit dem Streaming kombinieren. Komprimieren Sie die Daten vor dem Streamen, und geben Sie die gzip-Inhaltscodierung und die segmentierte Transfercodierung in den Nachrichtenheadern an. Beachten Sie außerdem, dass einige Webserver (z. B. Internet Information Server) so konfiguriert werden können, dass HTTP-Antworten automatisch komprimiert werden. Dies gilt unabhängig davon, ob die Web-API die Daten komprimiert oder nicht.

- **Implementieren Sie Teilantworten für Clients, die keine asynchronen Vorgänge unterstützen**.

	Als Alternative zum asynchronen Streaming kann eine Clientanwendung Daten für große Objekte explizit in Segmenten anfordern. Diese werden als Teilantworten bezeichnet. Die Clientanwendung sendet eine HTTP HEAD-Anforderung, um Informationen zum Objekt abzurufen. Wenn die Web-API Teilantworten unterstützt, sollte sie auf die HEAD-Anforderung mit einer Antwortnachricht reagieren, die einen Accept-Ranges-Header und einen Content-Length-Header zum Angeben der Gesamtgröße des Objekts enthält. Der Text der Nachricht sollte aber leer sein. Die Clientanwendung kann diese Informationen verwenden, um eine Reihe von GET-Anforderungen zu erstellen, mit denen ein Bereich für den zu empfangenden Bytebereich angegeben wird. Die Web-API sollte eine Antwortnachricht mit dem HTTP-Status 206 (Partial Content), einen Content-Length-Header zum Angeben des tatsächlichen Betrags an Daten im Text der Antwortnachricht und einen Content-Range-Header zurückgeben, mit dem angegeben wird, welchen Teil des Objekts (z. B. Bytes 4.000 bis 8.000) diese Daten darstellen.

	HTTP HEAD-Anforderungen und Teilantworten werden im Leitfaden zum API-Entwurf ausführlicher beschrieben.

- **Vermeiden Sie das Senden unnötiger Continue-Statusnachrichten in Clientanwendungen**.

	Eine Clientanwendung, die eine große Datenmenge an einen Server senden möchte, kann auch zuerst ermitteln, ob auf dem Server die Bereitschaft zum Annehmen der Anforderung besteht. Vor dem Senden der Daten kann die Clientanwendung eine HTTP-Anforderung mit einem Header „Expect: 100-Continue“ und einem Content-Length-Header zum Angeben der Größe für die Daten übermitteln. Der Nachrichtentext ist hierbei leer. Wenn der Server zum Behandeln der Anforderung bereit ist, sollte er mit einer Nachricht antworten, in der der HTTP-Status 100 (Continue) angegeben ist. Die Clientanwendung kann den Vorgang dann fortsetzen und die gesamte Anforderung senden, einschließlich der Daten im Nachrichtentext.

	Wenn Sie einen Dienst per IIS hosten, werden Expect: 100-Continue-Header vom Treiber „HTTP.sys“ automatisch erkannt und behandelt, bevor Anforderungen an Ihre Webanwendung übergeben werden. Dies bedeutet, dass Sie diese Header wahrscheinlich nicht in Ihrem Anwendungscode sehen, und Sie können davon ausgehen, dass per IIS bereits alle Nachrichten herausgefiltert wurden, die als ungeeignet oder zu groß angesehen werden.

	Wenn Sie Clientanwendungen mit dem .NET Framework erstellen, werden für alle POST- und PUT-Nachrichten standardmäßig zuerst Nachrichten mit Expect: 100-Continue-Headern gesendet. Wie auf der Serverseite auch, wird der Prozess vom .NET Framework transparent behandelt. Dieser Prozess führt aber dazu, dass jede POST- und PUT-Anforderung zwei Roundtrips zum Server verursacht. Dies gilt auch für kleine Anforderungen. Wenn von Ihrer Anwendung keine Anforderungen mit großen Datenmengen gesendet werden, können Sie dieses Feature deaktivieren. Verwenden Sie hierzu die `ServicePointManager`-Klasse, um `ServicePoint`-Objekte in der Clientanwendung zu erstellen. Ein `ServicePoint`-Objekt behandelt die Verbindungen, die der Client basierend auf dem Schema und den Hostfragmenten von URIs, mit denen Ressourcen auf dem Server identifiziert werden, mit einem Server herstellt. Sie können die `Expect100Continue`-Eigenschaft des `ServicePoint`-Objekts dann auf „false“ festlegen. Alle nachfolgenden POST- und PUT-Anforderungen, die vom Client über einen URI gesendet werden, der mit dem Schema und den Hostfragmenten des `ServicePoint`-Objekts übereinstimmt, werden ohne Expect: 100-Continue-Header gesendet. Im folgenden Code wird veranschaulicht, wie Sie ein `ServicePoint`-Objekt konfigurieren, mit dem alle an URIs gesendeten Anforderungen mit dem Schema `http` und dem Host `www.contoso.com` konfiguriert werden.

	```C#
	Uri uri = new Uri("http://www.contoso.com/");
	ServicePoint sp = ServicePointManager.FindServicePoint(uri);
	sp.Expect100Continue = false;
	```

	Sie können die statische `Expect100Continue`-Eigenschaft der `ServicePointManager`-Klasse auch so festlegen, dass der Standardwert dieser Eigenschaft für alle nachfolgend erstellten `ServicePoint`-Objekte angegeben wird. Weitere Informationen finden Sie auf der Microsoft-Website unter [ServicePoint-Klasse](https://msdn.microsoft.com/library/system.net.servicepoint.aspx).

- **Unterstützen Sie die Paginierung für Anforderungen, bei denen ggf. eine große Zahl von Objekten zurückgegeben wird**.

	Wenn eine Auflistung eine große Zahl von Ressourcen enthält, kann das Ausgeben einer GET-Anforderung an den entsprechenden URI zu erheblichem Verarbeitungsaufwand auf dem Hostserver der Web-API führen. Dies kann die Leistung beeinträchtigen und einen signifikanten Anstieg des Netzwerkdatenverkehrs und somit längere Wartezeiten nach sich ziehen.

	Zum Behandeln dieser Fälle sollte die Web-API Abfragezeichenfolgen unterstützen, mit denen die Clientanwendung Anforderungen verfeinern oder Daten in besser verwaltbaren, diskreten Blöcken (bzw. Seiten) abrufen kann. Das ASP.NET-Web-API-Framework analysiert Abfragezeichenfolgen und teilt sie in eine Reihe von Parameter-Wert-Paaren auf, die an die entsprechende Methode übergeben werden. Dabei werden die bereits beschriebenen Routingregeln befolgt. Die Methode sollte so implementiert werden, dass diese Parameter anhand der gleichen Namen akzeptiert werden, die in der Abfragezeichenfolge angegeben sind. Außerdem sollten diese Parameter optional sein (falls der Client die Abfragezeichenfolge in einer Anforderung weglässt) und über aussagekräftige Standardwerte verfügen. Im folgenden Code wird die `GetAllOrders`-Methode im `Orders`-Controller veranschaulicht. Mit dieser Methode werden die Details der Bestellungen abgerufen. Wenn für diese Methode keine Einschränkungen vorliegen, kann darüber ggf. eine große Datenmenge zurückgegeben werden. Mit den Parametern `limit` und `offset` soll der Umfang der Daten auf eine kleinere Teilmenge reduziert werden. In diesem Fall sind dies standardmäßig nur die ersten zehn Bestellungen:

	```C#
	public class OrdersController : ApiController
	{
    	...
    	[Route("api/orders")]
    	[HttpGet]
    	public IEnumerable<Order> GetAllOrders(int limit=10, int offset=0)
    	{
    	    // Find the number of orders specified by the limit parameter
    	    // starting with the order specified by the offset parameter
    	    var orders = ...
    	    return orders;
    	}
    	...
	}
	```

	Eine Clientanwendung kann eine Anforderung zum Abrufen von 30 Bestellungen ausgeben, bei der bei Offset = 50 begonnen wird. Hierfür wird der URI \__http://www.adventure-works.com/api/orders?limit=30&offset=50_ verwendet.

	> [AZURE.TIP]Sie sollten verhindern, dass Clientanwendungen Abfragezeichenfolgen angeben können, die zu einem URI mit einer Länge von mehr als 2.000 Zeichen führen. Viele Webclients und -server können keine URIs dieser Länge verarbeiten.

<a name="considerations-for-maintaining-responsiveness"></a>
## Aspekte zur Aufrechterhaltung der Reaktionsfähigkeit, Skalierbarkeit und Verfügbarkeit

Eine Web-API kann von vielen Clientanwendungen, die weltweit an den unterschiedlichsten Orten ausgeführt werden, gemeinsam verwendet werden. Es ist wichtig sicherzustellen, dass für die Implementierung der Web-API Folgendes gilt: Auch bei einer hohen Auslastung bleibt die Reaktionsfähigkeit erhalten, sie kann zur Unterstützung stark variierender Workloads skaliert werden, und für Clients, auf denen unternehmenskritische Vorgänge ausgeführt werden, wird die Verfügbarkeit garantiert. Beachten Sie die folgenden Punkte, wenn Sie ermitteln möchten, wie diese Anforderungen erfüllt werden können:

- **Stellen Sie die asynchrone Unterstützung für Anforderungen mit langer Ausführungsdauer bereit**.

	Eine Anforderung, deren Verarbeitung unter Umständen sehr lange dauern kann, sollte durchgeführt werden, ohne den übermittelnden Client zu blockieren. Die Web-API kann einige erste Überprüfungen durchführen, um die Anforderung zu validieren, eine separate Aufgabe zum Ausführen der Arbeit initiieren und dann eine Antwortnachricht mit dem HTTP-Code 202 (Accepted) zurückgeben. Die Aufgabe kann asynchron als Teil der Web-API-Verarbeitung ausgeführt werden, oder sie kann in einen Azure WebJob (wenn die Web-API von einer Azure-Website gehostet wird) oder eine Workerrolle (wenn die Web-API als Azure-Clouddienst implementiert wird) verlagert werden.

	> [AZURE.NOTE]Weitere Informationen zur Verwendung von WebJobs mit der Azure-Website finden Sie auf der Microsoft-Website unter [Ausführen von Hintergrundaufgaben mit Webaufträgen](web-sites-create-web-jobs.md).

	Außerdem sollte die Web-API über ein Verfahren zum Zurückgeben der Ergebnisse einer Verarbeitung an die Clientanwendung verfügen. Hierzu können Sie einen Abrufmechanismus für Clientanwendungen bereitstellen, um regelmäßig abzufragen, ob die Verarbeitung abgeschlossen ist, und das Ergebnis abzurufen. Sie können die Web-API auch so einrichten, dass nach Abschluss des Vorgangs eine Benachrichtigung gesendet wird.

	Sie können einen einfachen Abrufmechanismus implementieren, indem Sie einen _polling_-URI angeben, der als virtuelle Ressource fungiert. Gehen Sie hierbei wie folgt vor:

	1. Die Clientanwendung sendet die erste Anfrage an die Web-API.

	2. Die Web-API speichert Informationen zur Anforderung in einer Tabelle, die im Tabellenspeicher oder im Microsoft Azure Cache vorgehalten wird, und generiert einen eindeutigen Schlüssel für diesen Eintrag, z. B. in Form einer GUID.

	3. Von der Web-API wird die Verarbeitung als separate Aufgabe initiiert. Die Web-API zeichnet den Status der Aufgabe in der Tabelle als _Running_ auf.

	4. Die Web-API gibt eine Antwortnachricht mit dem HTTP-Statuscode 202 (Accepted) und der GUID des Tabelleneintrags im Text der Nachricht zurück.

	5. Wenn die Aufgabe abgeschlossen ist, speichert die Web-API die Ergebnisse in der Tabelle und legt den Status der Aufgabe auf _Complete_ fest. Beachten Sie Folgendes: Wenn die Durchführung der Aufgabe nicht erfolgreich ist, kann die Web-API auch Informationen zum Fehler speichern und den Status auf _Failed_ festlegen.

	6. Während der Ausführung der Aufgabe kann der Client mit seiner eigenen Verarbeitung fortfahren. Er kann regelmäßig eine Anforderung an den URI _/polling/{guid}_ senden. Hierbei steht _{guid}_ für die GUID, die von der Web-API in der 202-Antwortnachricht zurückgegeben wird.

	7. Die Web-API am URI _/polling{guid}_ fragt den Status der entsprechenden Aufgabe in der Tabelle ab und gibt eine Antwortnachricht mit dem HTTP-Statuscode 200 (OK) und dem Status zurück (_Running_, _Complete_ oder _Failed_). Wenn die Aufgabe abgeschlossen ist oder nicht erfolgreich war, kann die Antwortnachricht auch die Ergebnisse der Verarbeitung oder Informationen enthalten, die zur Ursache des Fehlers verfügbar sind.

	Falls Sie es vorziehen, Benachrichtigungen zu implementieren, können Sie beispielsweise die folgenden verfügbaren Optionen verwenden:

	- Verwenden Sie ein Azure Notification Hub, um asynchrone Antworten per Pushvorgang an Clientanwendungen zu übertragen. Ausführlichere Informationen finden Sie auf der Microsoft-Website unter [Azure Notification Hubs – Benachrichtigen von Benutzern](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md).

	- Verwenden Sie das Comet-Modell, um eine dauerhafte Netzwerkverbindung zwischen dem Client und dem Hostserver der Web-API aufrechtzuerhalten. Nutzen Sie diese Verbindung, um Nachrichten vom Server zurück an den Client zu übertragen. Eine Beispiellösung ist im Artikel [Erstellen einer einfachen Comet-Anwendung in Microsoft .NET Framework](https://msdn.microsoft.com/magazine/jj891053.aspx) des MSDN-Magazins enthalten.

	- Verwenden Sie SignalR, um Daten über eine dauerhafte Netzwerkverbindung in Echtzeit vom Webserver auf den Client zu übertragen. SignalR ist für ASP.NET-Webanwendungen als NuGet-Paket verfügbar. Weitere Informationen finden Sie auf der Website [ASP.NET SignalR](http://signalr.net/).

	> [AZURE.NOTE]Für Comet und SignalR werden jeweils dauerhafte Netzwerkverbindungen zwischen dem Webserver und der Clientanwendung verwendet. Dies kann die Skalierbarkeit beeinträchtigen, da eine große Anzahl von Clients ggf. eine ebenso große Anzahl von gleichzeitigen Verbindungen erfordern kann.

- **Stellen Sie sicher, dass jede Anforderung zustandslos ist**.

	Jede Anforderung sollte als atomarisch angesehen werden. Es sollten keine Abhängigkeiten zwischen der Anforderung einer Clientanwendung und allen nachfolgenden Anforderungen bestehen, die von demselben Client übermittelt werden. Dieser Ansatz ist in Bezug auf die Skalierbarkeit hilfreich. Instanzen des Webdiensts können auf einer Reihe von Servern bereitgestellt werden. Clientanforderungen können an all diese Instanzen geleitet werden, und die Ergebnisse sollten immer gleich sein. Die Verfügbarkeit wird aus einem ähnlichen Grund verbessert. Wenn ein Webserver ausfällt, können Anforderungen an eine andere Instanz weitergeleitet werden (per Azure Traffic Manager), während der Server neu gestartet wird. Dies hat keine negativen Auswirkungen auf Clientanwendungen.

- **Verfolgen Sie Clients nach, und implementieren Sie die Drosselung, um die Gefahr von DoS-Angriffen zu verringern**.

	Wenn ein bestimmter Client innerhalb eines bestimmten Zeitraums eine große Zahl von Anforderungen sendet, kann dies zu einer Monopolisierung des Diensts führen und die Leistung anderer Clients beeinträchtigen. Zur Beseitigung dieses Problems kann eine Web-API die Aufrufe von Clientanwendungen überwachen, indem entweder die IP-Adresse aller eingehenden Anforderungen nachverfolgt wird oder indem jeder authentifizierte Zugriff protokolliert wird. Sie können diese Informationen nutzen, um den Zugriff auf Ressourcen zu beschränken. Wenn ein Client einen festgelegten Grenzwert überschreitet, kann die Web-API eine Antwortnachricht mit dem Status 503 (Service Unavailable) zurückgeben und einen Retry-After-Header einfügen. Mit diesem Header wird angegeben, wann der Client die nächste Anforderung senden kann, ohne dass diese abgelehnt wird. Diese Strategie kann die Gefahr eines DoS-Angriffs (Denial Of Service) über Clients in Verbindung mit einem Zusammenbruch des Systems verringern.

- **Verwalten Sie dauerhafte HTTP-Verbindungen sorgfältig**.

	Das HTTP-Protokoll unterstützt dauerhafte HTTP-Verbindungen, sofern sie verfügbar sind. Mit der HTTP 1.0-Spezifikation wurde der Connection:Keep-Alive-Header hinzugefügt. Hiermit kann eine Clientanwendung für den Server angeben, dass dieselbe Verbindung zum Senden nachfolgender Anforderungen genutzt werden kann, anstatt neue Verbindungen zu öffnen. Die Verbindung wird automatisch geschlossen, wenn der Client die Verbindung nicht innerhalb eines vom Host definierten Zeitraums wiederverwendet. Dies ist das Standardverhalten in HTTP 1.1, das von Azure-Diensten verwendet wird. Es ist also nicht erforderlich, Keep-Alive-Header in Nachrichten einzufügen.

	Das Offenhalten einer Verbindung kann die Reaktionsfähigkeit verbessern, indem die Latenz und Netzwerküberlastung reduziert wird. Aber es kann sich negativ auf die Skalierbarkeit auswirken, wenn nicht benötigte Verbindungen länger als erforderlich geöffnet bleiben, da das gleichzeitige Herstellen von Verbindungen für andere Clients eingeschränkt wird. Außerdem kann es sich auf die Akkulaufzeit auswirken, wenn die Clientanwendung auf einem Mobilgerät ausgeführt wird. Falls die Anwendung nur gelegentlich Anforderungen an den Server sendet, kann das Aufrechterhalten einer offenen Verbindung dazu führen, dass der Akku schneller leer ist. Um sicherzustellen, dass eine Verbindung unter HTTP 1.1 nicht dauerhaft eingerichtet wird, kann der Client einen Connection:Close-Header in Nachrichten einfügen, um das Standardverhalten außer Kraft zu setzen. Wenn ein Server eine sehr große Anzahl von Clients behandelt, kann er einen Connection:Close-Header in Antwortnachrichten einfügen, mit dem die Verbindung geschlossen wird und Serverressourcen gespart werden.

	> [AZURE.NOTE]Dauerhafte HTTP-Verbindungen sind ein rein optionales Feature zum Reduzieren des Netzwerkaufwands, der mit dem wiederholten Einrichten eines Kommunikationskanals verbunden ist. Weder die Web-API noch die Clientanwendung sollten davon abhängig sein, dass eine dauerhafte HTTP-Verbindung verfügbar ist. Nutzen Sie keine dauerhaften HTTP-Verbindungen, um Benachrichtigungssysteme im Comet-Stil zu implementieren. Stattdessen sollten Sie Sockets (oder WebSockets, falls verfügbar) auf TCP-Ebene verwenden. Beachten Sie außerdem Folgendes: Der Nutzen von Keep-Alive-Headern ist eingeschränkt, wenn eine Clientanwendung mit einem Server über einen Proxy kommuniziert. Nur die Verbindung mit dem Client und dem Proxy ist dauerhafter Art.

## Aspekte zur Veröffentlichung und Verwaltung einer Web-API

Die Web-API muss in einer Hostumgebung bereitgestellt werden, um sie für Clientanwendungen verfügbar zu machen. Bei dieser Umgebung handelt es sich normalerweise um einen Webserver, aber es kann auch eine andere Art von Hostprozess sein. Berücksichtigen Sie beim Veröffentlichen einer Web-API die folgenden Punkte:

- Alle Anforderungen müssen authentifiziert und autorisiert werden, und die Zugriffssteuerung muss im angemessenen Umfang durchgesetzt werden.
- Eine kommerzielle Web-API kann in Bezug auf die Reaktionszeiten verschiedenen Qualitätsgarantien unterliegen. Es ist wichtig sicherzustellen, dass die Hostumgebung skalierbar ist, wenn die Auslastung im Laufe der Zeit erheblich variieren kann.
- Es kann erforderlich sein, Anforderungen aus Gründen der Monetarisierung zu messen.
- Es kann erforderlich sein, den Fluss des Datenverkehrs an die Web-API zu regulieren und für bestimmte Clients, die ihr Kontingent ausgeschöpft haben, eine Drosselung zu implementieren.
- Unter Umständen ist es gesetzlich vorgeschrieben, alle Anforderungen und Antworten zu protokollieren und zu überwachen.
- Zum Sicherstellen der Verfügbarkeit kann es erforderlich sein, die Integrität des Servers zu überwachen, auf dem die Web-API gehostet wird, und ihn bei Bedarf neu zu starten.

Es ist hilfreich, diese Probleme von den technischen Problemen in Bezug auf die Implementierung der Web-API abkoppeln zu können. Erwägen Sie aus diesem Grund das Erstellen einer so genannten [Fassade](http://en.wikipedia.org/wiki/Facade_pattern) (Façade), die als separater Prozess ausgeführt wird und Anforderungen an die Web-API weiterleitet. Mit dieser Fassade können die Verwaltungsvorgänge bereitgestellt und geprüfte Anforderungen an die Web-API weitergeleitet werden. Die Nutzung einer Fassade ist ferner mit vielen funktionellen Vorteilen verbunden, z. B.:

- Sie dient als Integrationspunkt für mehrere Web-APIs.
- Sie ermöglicht das Transformieren von Nachrichten und das Übersetzen von Kommunikationsprotokollen für Clients, die mit unterschiedlicher Technologie erstellt wurden.
- Sie ermöglicht das Zwischenspeichern von Anforderungen und Antworten, um die Auslastung des Servers zu reduzieren, auf dem die Web-API gehostet wird.

## Aspekte zum Testen einer Web-API
Eine Web-API sollte so gründlich wie jede andere Software getestet werden. Erwägen Sie die Erstellung von Komponententests zum Überprüfen der Funktionalität jedes Vorgangs, wie Sie dies auch bei jeder anderen Art von Anwendung tun. Weitere Informationen finden Sie auf der Microsoft-Website unter [Überprüfen von Code mithilfe von Komponententests](https://msdn.microsoft.com/library/dd264975.aspx).

> [AZURE.NOTE]Das Web-API-Beispiel dieses Leitfadens enthält ein Testprojekt, das verdeutlicht, wie Komponententests für ausgewählte Vorgänge durchgeführt werden.

Die Art einer Web-API bringt eigene zusätzliche Anforderungen in Bezug auf die Überprüfung der korrekten Funktionsweise mit sich. Achten Sie besonders auf die folgenden Aspekte:

- Testen Sie alle Routen, um zu überprüfen, ob dabei die richtigen Vorgänge aufgerufen werden. Achten Sie besonders darauf, ob der HTTP-Statuscode 405 (Method Not Allowed) unerwartet zurückgegeben wird. Dies kann auf eine fehlende Übereinstimmung zwischen einer Route und den HTTP-Methoden (GET, POST, PUT, DELETE) hindeuten, die auf dieser Route genutzt werden können.

	Senden Sie HTTP-Anforderungen an Routen, die dafür keine Unterstützung aufweisen, z. B. per Übermittlung einer POST-Anforderung an eine bestimmte Ressource (POST-Anforderungen sollten nur an Ressourcenauflistungen gesendet werden). In diesen Fällen _sollte_ die einzig gültige Antwort der Statuscode 405 (Not Allowed) sein.

- Vergewissern Sie sich, dass alle Routen richtig geschützt sind und geeignete Authentifizierungs- und Autorisierungsüberprüfungen aufweisen.

	> [AZURE.NOTE]Für einige Aspekte der Sicherheit, z. B. die Benutzerauthentifizierung, ist meist nicht die Web-API verantwortlich, sondern die Hostumgebung. Es ist trotzdem erforderlich, Sicherheitstests in den Bereitstellungsprozess einzubinden.

- Testen Sie die Ausnahmebehandlung, die von den einzelnen Vorgängen durchgeführt wird, und stellen Sie sicher, dass eine passende und aussagekräftige HTTP-Antwort zurück an die Clientanwendung übergeben wird.
- Achten Sie darauf, dass Anforderungs- und Antwortnachrichten richtig formatiert sind. Wenn eine HTTP POST-Anforderung beispielsweise die Daten für eine neue Ressource im Format „x-www-form-urlencoded“ enthält, müssen Sie bestätigen, dass der entsprechende Vorgang die Daten richtig analysiert, die Ressourcen erstellt und eine Antwort mit den Details der neuen Ressource zurückgibt, einschließlich des richtigen Location-Headers.
- Überprüfen Sie alle Links und URIs in Antwortnachrichten. Beispielsweise sollte eine HTTP POST-Nachricht den URI der neu erstellten Ressource zurückgeben. Alle HATEOAS-Links müssen gültig sein.

	> [AZURE.IMPORTANT]Wenn Sie die Web-API über einen API Management-Dienst veröffentlichen, sollten diese URIs die URL des Management-Diensts widerspiegeln, und nicht die URL des Webservers, auf dem die Web-API gehostet wird.

- Stellen Sie sicher, dass jeder Vorgang für unterschiedliche Eingabekombinationen die richtigen Statuscodes zurückgibt. Beispiel:
	- Wenn eine Abfrage erfolgreich ist, sollte der Vorgang den Statuscode 200 (OK) zurückgeben.
	- Wenn eine Ressource nicht gefunden wird, sollte der Vorgang den HTTP-Statuscode 404 (Nicht gefunden) zurückgeben.
	- Wenn der Client eine Anforderung sendet, mit der eine Ressource erfolgreich gelöscht wird, sollte der Statuscode 204 (No Content) lauten.
	- Wenn der Client eine Anforderung sendet, mit der eine neue Ressource erstellt wird, sollte der Statuscode 201 (Created) lauten.

Achten Sie auf unerwartete Antwortstatuscodes im Bereich 5xx. Diese Nachrichten werden normalerweise vom Server gemeldet, um anzugeben, dass eine gültige Anforderung nicht erfüllt werden konnte.

- Testen Sie die unterschiedlichen Anforderungsheaderkombinationen, die von einer Clientanwendung angegeben werden können, und stellen Sie sicher, dass die Web-API in Antwortnachrichten die erwarteten Informationen zurückgibt.

- Testen Sie Abfragezeichenfolgen. Wenn ein Vorgang optionale Parameter verwenden kann (z. B. Paginierungsanforderungen), sollten Sie die unterschiedlichen Kombinationen und die Reihenfolge von Parametern testen.

- Vergewissern Sie sich, dass asynchrone Vorgänge erfolgreich abgeschlossen werden. Wenn die Web-API das Streamen für Anforderungen unterstützt, die große binäre Objekte zurückgeben (z. B. Video oder Audio), sollten Sie sicherstellen, dass Clientanforderungen beim Streamen der Daten nicht blockiert werden. Wenn die Web-API das Abrufen für Datenänderungsvorgänge mit langer Ausführungsdauer implementiert, sollten Sie sicherstellen, dass die Vorgänge ihren Status während des Ablaufs richtig melden.

Außerdem sollten Sie Leistungstests erstellen und ausführen, um zu überprüfen, ob die Web-API auch in Notfällen zufriedenstellend arbeitet. Sie können mit Visual Studio Ultimate ein Projekt zum Testen der Webleistung und Auslastung erstellen. Weitere Informationen finden Sie auf der Microsoft-Website unter [Ausführen von Leistungstests für Ihre App](https://msdn.microsoft.com/library/dn250793.aspx).

## Veröffentlichen und Verwalten einer Web-API mit dem Azure API Management-Dienst

Azure stellt den [API Management-Dienst](http://azure.microsoft.com/documentation/services/api-management/) bereit, den Sie zum Veröffentlichen und Verwalten einer Web-API verwenden können. Hiermit können Sie einen Dienst generieren, der für eine oder mehrere Web-APIs als „Fassade“ (Façade) dient. Bei diesem Dienst handelt es sich selbst um einen skalierbaren Webdienst, den Sie mit dem Azure-Verwaltungsportal erstellen und konfigurieren können. Sie können diesen Dienst verwenden, um eine Web-API wie folgt zu veröffentlichen und zu verwalten:

1. Stellen Sie die Web-API auf einer Website, in einem Azure-Clouddienst oder auf einem virtuellen Azure-Computer bereit.

2. Verbinden Sie den API Management-Dienst mit der Web-API. Anforderungen, die an die URL der Verwaltungs-API gesendet werden, werden den URIs in der Web-API zugeordnet. Ein und derselbe API Management-Dienst kann Anforderungen an mehr als eine Web-API weiterleiten. So können Sie mehrere Web-APIs zu einem zentralen Management-Dienst zusammenfassen. Außerdem kann von mehr als einem API Management-Dienst auf dieselbe Web-API verwiesen werden, wenn Sie die Funktionalität, die für unterschiedliche Anwendungen verfügbar ist, einschränken oder partitionieren müssen.

	> [AZURE.NOTE]Die URIs in HATEOAS-Links, die als Teil der Antwort für HTTP GET-Anforderungen generiert werden, sollten auf die URL des API Management-Diensts verweisen, und nicht auf den Webserver, auf dem die Web-API gehostet wird.

3. Geben Sie für jede Web-API die HTTP-Vorgänge an, die von der Web-API zusammen mit den optionalen Parametern, die ein Vorgang als Eingabe verwenden kann, verfügbar gemacht werden. Sie können auch konfigurieren, ob der API Management-Dienst die von der Web-API empfangene Antwort zwischenspeichern soll, um wiederholte Anforderungen für dieselben Daten zu optimieren. Zeichnen Sie die Details der HTTP-Antworten auf, die von den einzelnen Vorgängen generiert werden können. Diese Informationen werden zum Generieren der Dokumentation für Entwickler verwendet. Es ist also wichtig, dass sie richtig und vollständig sind.

	Sie können Vorgänge entweder manuell definieren, indem Sie die Assistenten im Azure-Verwaltungsportal verwenden, oder Sie können sie aus einer Datei importieren, in der die Definitionen im WADL- oder Swagger-Format enthalten sind.

4. Konfigurieren Sie die Sicherheitseinstellungen zwischen dem API Management-Dienst und dem Webserver, auf dem die Web-API gehostet wird. Der API Management-Dienst unterstützt derzeit die Standardauthentifizierung und die wechselseitige Authentifizierung mit Zertifikaten sowie die OAuth 2.0-Benutzerautorisierung.

5. Erstellen Sie ein Produkt. Ein Produkt ist die Einheit der Veröffentlichung. Sie fügen die Web-APIs, die Sie zuvor mit dem Management-Dienst verbunden haben, dem Produkt hinzu. Wenn das Produkt veröffentlicht wird, werden die Web-APIs für Entwickler verfügbar gemacht.

	> [AZURE.NOTE]Vor dem Veröffentlichen eines Produkts können Sie auch Benutzergruppen definieren, die Zugriff auf das Produkt haben, und diesen Gruppen Benutzer hinzufügen. So haben Sie die Kontrolle darüber, welche Entwickler und Anwendungen die Web-API verwenden können. Wenn eine Web-API genehmigt werden muss, müssen Entwickler eine Anforderung an den Produktadministrator senden, bevor sie Zugriff erhalten können. Der Administrator kann dem Entwickler den Zugriff gewähren oder verweigern. Außerdem können vorhandene Entwickler blockiert werden, wenn sich die Umstände ändern.

6.	Konfigurieren Sie Richtlinien für jede Web-API. Mit Richtlinien werden beispielsweise folgende Aspekte geregelt: ob domänenübergreifende Aufrufe zulässig sind, wie Clients authentifiziert werden, ob zwischen den Datenformaten XML und JSON transparent konvertiert werden soll, ob Aufrufe für einen bestimmten IP-Bereich beschränkt werden sollen, Verwendungskontingente und ob die Aufrufrate begrenzt werden soll. Richtlinien können global über das gesamte Produkt hinweg, für eine einzelne Web-API in einem Produkt oder für einzelne Vorgänge in einer Web-API angewendet werden.

Ausführliche Informationen dazu, wie Sie diese Aufgaben durchführen, finden Sie auf der Microsoft-Website unter [API Management](http://azure.microsoft.com/services/api-management/). Der Azure API Management-Dienst stellt auch seine eigene REST-Schnittstelle bereit. Hiermit können Sie eine benutzerdefinierte Schnittstelle zum Vereinfachen des Prozesses erstellen, der zum Konfigurieren einer Web-API verwendet wird. Weitere Informationen finden Sie auf der Microsoft-Website unter [Azure API-Verwaltung für REST-API-Referenz](https://msdn.microsoft.com/library/azure/dn776326.aspx).

> [AZURE.TIP]Azure stellt den Azure Traffic Manager bereit, mit dem Sie das Failover und den Lastenausgleich implementieren und die Latenz über mehrere Instanzen einer Website hinweg, die an unterschiedlichen geografischen Orten gehostet wird, reduzieren können. Sie können den Azure Traffic Manager zusammen mit dem API Management-Dienst verwenden. Der API Management-Dienst kann Anforderungen über den Azure Traffic Manager an die Instanzen einer Website weiterleiten. Weitere Informationen finden Sie auf der Microsoft-Website unter [Traffic Manager-Lastenausgleichsmethoden](https://msdn.microsoft.com/library/azure/dn339010.aspx).

> Wenn Sie benutzerdefinierte DNS-Namen für Ihre Websites verwenden, sollten Sie in dieser Struktur den richtigen CNAME-Eintrag für jede Website konfigurieren, damit jeweils auf den DNS-Namen der Azure Traffic Manager-Website verwiesen wird.

## Unterstützen von Entwicklern beim Erstellen von Clientanwendungen
Entwickler, die Clientanwendungen erstellen, benötigen normalerweise Informationen dazu, wie sie auf die Web-API zugreifen können. Außerdem benötigen sie Dokumentation zu den Parametern, Datentypen, Rückgabetypen und Rückgabecodes, mit denen die verschiedenen Anforderungen und Antworten zwischen dem Webdienst und der Clientanwendung beschrieben werden.

### Dokumentieren der REST-Vorgänge für eine Web-API
Der Azure API Management-Dienst enthält ein Entwicklerportal, mit dem die von einer Web-API verfügbar gemachten REST-Vorgänge beschrieben werden. Wenn ein Produkt veröffentlicht wird, wird es in diesem Portal angezeigt. Entwickler können sich über dieses Portal für den Zugriff registrieren. Der Administrator kann die Anforderung dann genehmigen oder ablehnen. Wenn Entwickler die Genehmigung erhalten, wird ihnen ein Abonnementschlüssel zugewiesen. Dieser wird zum Authentifizieren der Aufrufe von Clientanwendungen verwendet, die von den Entwicklern entwickelt werden. Dieser Schlüssel muss bei jedem Web-API-Aufruf bereitgestellt werden. Andernfalls wird der Aufruf abgelehnt.

Außerdem bietet das Portal Folgendes:

- Dokumentation zum Produkt mit den verfügbaren Vorgängen, den erforderlichen Parametern und den unterschiedlichen Antworten, die zurückgegeben werden können. Beachten Sie, dass diese Informationen anhand der Details generiert werden, die in Schritt 3 in der Liste im Abschnitt [Veröffentlichen einer Web-API mit dem Microsoft Azure API Management-Dienst](#publishing-a-web-API) enthalten sind.

- Codeausschnitte, die das Aufrufen von Vorgängen für mehrere Sprachen veranschaulichen, z. B. JavaScript, C#, Java, Ruby, Python und PHP.

- Eine Entwicklerkonsole, über die Entwickler eine HTTP-Anforderung zum Testen der Vorgänge im Produkt und zum Anzeigen der Ergebnisse senden können.

- Eine Seite, auf der Entwickler Fehler oder aufgetretene Probleme melden können.

Über das Azure-Verwaltungsportal können Sie das Entwicklerportal anpassen und die Formatierung und das Layout ändern, um es an das Branding Ihres Unternehmens anzugleichen.

### Implementieren eines Client-SDK
Das Erstellen einer Clientanwendung, mit der REST-Anforderungen zum Zugreifen auf eine Web-API aufgerufen werden, erfordert das Schreiben einer beträchtlichen Menge an Code. Jede Anforderung muss richtig erstellt und formatiert werden, die Anforderung muss an den Hostserver des Webdiensts gesendet werden, und die Antwort muss analysiert werden, um zu ermitteln, ob die Anforderung erfolgreich war. Außerdem müssen alle zurückgegebenen Daten extrahiert werden. Um die Clientanwendung hiervon abzukoppeln, können Sie ein SDK bereitstellen, das die REST-Schnittstelle umschließt und diese Low-Level-Details in einem funktionelleren Methodensatz abstrahiert. Eine Clientanwendung verwendet diese Methoden, mit denen Aufrufe transparent in REST-Anforderungen konvertiert werden. Anschließend werden die Antworten zurück in Methodenrückgabewerte konvertiert. Dies ist ein gängiges Verfahren, das von vielen Diensten implementiert wird, einschließlich des Azure-SDK.

Die Erstellung eines clientseitigen SDK ist ein aufwendiges Unterfangen, da es konsistent implementiert und sorgfältig getestet werden muss. Ein Großteil dieses Prozesses kann aber „mechanisiert“ werden, und viele Anbieter stellen Tools bereit, mit denen viele Aufgaben automatisiert werden können.

## Überwachen einer Web-API

Je nach Art der Veröffentlichung und Bereitstellung Ihrer Web-API können Sie die Web-API direkt überwachen, oder Sie können Informationen zur Verwendung und Integrität sammeln, indem Sie den Datenverkehr analysieren, der den API Management-Dienst durchläuft.

### Direktes Überwachen einer Web-API
Wenn Sie Ihre Web-API mit der ASP.NET-Web-API-Vorlage (entweder als Web-API-Projekt oder Webrolle in einem Azure-Clouddienst) und Visual Studio 2013 implementiert haben, können Sie per ASP.NET Application Insights Daten zur Verfügbarkeit, Leistung und Verwendung sammeln. Application Insights ist ein Paket, mit dem Informationen zu Anforderungen und Antworten transparent nachverfolgt und aufgezeichnet werden, wenn die Web-API in der Cloud bereitgestellt wird. Nachdem das Paket installiert und konfiguriert wurde, müssen Sie in Ihrer Web-API zur Nutzung keinen Code ändern. Wenn Sie die Web-API auf einer Azure-Website bereitstellen, wird der gesamte Datenverkehr untersucht, und die folgenden statistischen Daten werden gesammelt:

- Reaktionszeit des Servers

- Anzahl von Serveranforderungen und die Details jeder Anforderung

- Langsamste Anforderungen in Bezug auf die durchschnittliche Reaktionszeit

- Details nicht erfolgreicher Anforderungen

- Anzahl von Sitzungen, die von unterschiedlichen Browsern und Benutzer-Agents initiiert werden

- Am häufigsten angezeigte Seiten (vor allem nützlich für Webanwendungen und nicht für Web-APIs)

- Unterschiedliche Benutzerrollen, mit denen auf die Web-API zugegriffen wird

Sie können diese Daten im Azure-Verwaltungsportal in Echtzeit anzeigen. Außerdem können Sie WebTests erstellen, mit denen die Integrität der Web-API überwacht wird. Ein WebTest sendet eine regelmäßige Anforderung an einen angegebenen URI in der Web-API und erfasst die Antwort. Sie können die Definition einer erfolgreichen Antwort angeben (z. B. HTTP-Statuscode 200). Und wenn die Anforderung diese Antwort nicht zurückgibt, können Sie vorgeben, dass eine Warnung an einen Administrator gesendet wird. Bei Bedarf kann der Administrator den Server neu starten, auf dem die Web-API gehostet wird, wenn er ausgefallen ist.

Weitere Informationen finden Sie auf der Microsoft-Website unter [Application Insights – Beginnen Sie damit, Integrität und Nutzung Ihrer Anwendung zu überwachen](app-insights-start-monitoring-app-health-usage/).

### Überwachen einer Web-API per API Management-Dienst

Wenn Sie Ihre Web-API mit dem API Management-Dienst veröffentlicht haben, enthält die Seite „API Management“ im Azure-Verwaltungsportal ein Dashboard, auf dem Sie die allgemeine Leistung des Diensts anzeigen können. Auf der Seite „Analytics“ können Sie auf die Details zur Verwendung des Produkts zugreifen. Diese Seite enthält die folgenden Registerkarten:

- **Verwendung**: Diese Registerkarte enthält Informationen zur Anzahl der durchgeführten API-Aufrufe und zur Bandbreite, die zum Behandeln dieser Aufrufe im Laufe der Zeit verwendet wurde. Sie können die Verwendungsdetails nach Produkt, API und Vorgang filtern.

- **Integrität**: Auf dieser Registerkarte können Sie das Ergebnis von API-Anforderungen (zurückgegebenen HTTP-Statuscodes), die Effektivität der Richtlinie für die Zwischenspeicherung, die API-Reaktionszeit und die Reaktionszeit des Diensts anzeigen. Sie können auch die Integritätsdaten nach Produkt, API und Vorgang filtern.

- **Aktivität**: Diese Registerkarte enthält eine Textzusammenfassung der Anzahl von erfolgreichen Aufrufen, fehlgeschlagenen Aufrufen, blockierten Aufrufen, der durchschnittlichen Reaktionszeit und Reaktionszeiten für Produkt, Web-API und Vorgang. Auf dieser Seite ist auch die Anzahl von Aufrufen angegeben, die von den einzelnen Entwicklern durchgeführt wurden.

- **Auf einen Blick**: Auf dieser Registerkarte wird eine Zusammenfassung der Leistungsdaten angezeigt, z. B. die Entwickler mit den meisten API-Aufrufen und die Produkte, Web-APIs und Vorgänge, von denen diese Aufrufe empfangen wurden.

Anhand dieser Informationen können Sie bestimmen, ob eine bestimmte Web-API oder ein Vorgang einen Engpass verursacht, und bei Bedarf die Hostumgebung skalieren und weitere Server hinzufügen. Sie können auch prüfen, ob eine oder mehrere Anwendungen eine unangemessen hohe Menge an Ressourcen verbrauchen, und entsprechende Richtlinien anwenden, um Kontingente festzulegen und die Aufrufraten zu beschränken.

> [AZURE.NOTE]Sie können die Details für ein veröffentlichtes Produkt ändern. Die Änderungen werden dann sofort angewendet. Beispielsweise können Sie einen Vorgang einer Web-API hinzufügen oder daraus entfernen, ohne dass Sie hierfür das Produkt neu veröffentlichen müssen, in dem die Web-API enthalten ist.

## Verwandte Muster
- Das Muster [Fassade](http://en.wikipedia.org/wiki/Facade_pattern) (Façade) beschreibt, wie eine Schnittstelle für eine Web-API bereitgestellt wird.

## Weitere Informationen
- Auf der Microsoft-Website unter [Informationen zur ASP.NET-Web-API](http://www.asp.net/web-api) finden Sie eine ausführliche Einführung in die Erstellung von REST-Webdiensten mit der Web-API.
- Auf der Microsoft-Website unter [Routing in der ASP.NET-Web-API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) wird beschrieben, wie das konventionsbasierte Routing im ASP.NET-Web-API-Framework funktioniert.
- Weitere Informationen zum attributbasierten Routing finden Sie auf der Microsoft-Website unter [Attributrouting in der Web-API 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2).
- Die Seite mit dem [Lernprogramm](http://www.odata.org/getting-started/basic-tutorial/) auf der OData-Website enthält eine Einführung in die Features des OData-Protokolls.
- Die Seite [ASP.NET-Web-API-OData](http://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) auf der Microsoft-Website enthält Beispiele und weitere Informationen zur Implementierung einer OData-Web-API mit ASP.NET.
- Auf der Seite [Einführung der Batchunterstützung für die Web-API und Web-API-OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) der Microsoft-Website wird beschrieben, wie Sie Batchvorgänge in einer Web-API mit OData implementieren.
- Der Artikel [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/) (Muster der Idempotenz) im Blog von Jonathan Oliver enthält eine Übersicht über die Idempotenz und ihre Verbindung mit Datenverwaltungsvorgängen.
- Die Seite [Statuscodedefinitionen](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) auf der W3C-Website enthält eine vollständige Liste mit HTTP-Statuscodes und den dazugehörigen Beschreibungen.
- Ausführliche Informationen zur Behandlung von HTTP-Ausnahmen mit der ASP.NET-Web-API finden Sie auf der Microsoft-Website unter [Ausnahmebehandlung in der ASP.NET-Web-API](http://www.asp.net/web-api/overview/error-handling/exception-handling).
- Im Artikel [Globale Fehlerbehandlung für die Web-API](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) auf der Microsoft-Website wird beschrieben, wie Sie eine globale Strategie zur Fehlerbehandlung und Protokollierung für eine Web-API implementieren.
- Die Seite [Ausführen von Hintergrundaufgaben mit Webaufträgen](web-sites-create-web-jobs.md) auf der Microsoft-Website enthält Informationen und Beispiele zur Verwendung von WebJobs zum Durchführen von Hintergrundvorgängen auf einer Azure-Website.
- Auf der Seite [Azure Notification Hubs – Benachrichtigen von Benutzern](notification-hubs-aspnet-backend-windows-dotnet-notify-users/) der Microsoft-Website wird beschrieben, wie Sie ein Azure Notification Hub zum Übertragen von asynchronen Antworten per Pushvorgang auf Clientanwendungen verwenden können.
- Auf der Seite [API Management](http://azure.microsoft.com/services/api-management/) der Microsoft-Website wird beschrieben, wie Sie ein Produkt veröffentlichen, das kontrollierten und sicheren Zugriff auf eine Web-API ermöglicht.
- Auf der Seite [Azure API-Verwaltung für REST-API-Referenz](https://msdn.microsoft.com/library/azure/dn776326.aspx) der Microsoft-Website wird beschrieben, wie Sie die API Management-REST-API zum Erstellen von benutzerdefinierten Verwaltungsanwendungen verwenden.
- Auf der Seite [Traffic Manager-Lastenausgleichsmethoden](https://msdn.microsoft.com/library/azure/dn339010.aspx) der Microsoft-Website wird zusammengefasst, wie Azure Traffic Manager verwendet werden kann, um für mehrere Instanzen einer Website, auf der eine Web-API gehostet wird, den Lastenausgleich für Anforderungen durchzuführen.
- Auf der Seite [Application Insights – Beginnen Sie damit, Integrität und Nutzung Ihrer Anwendung zu überwachen](app-insights-start-monitoring-app-health-usage.md) auf der Microsoft-Website werden ausführliche Informationen zum Installieren und Konfigurieren von Application Insights in einem ASP.NET-Web-API-Projekt bereitgestellt.
- Auf der Seite [Überprüfen von Code mithilfe von Komponententests](https://msdn.microsoft.com/library/dd264975.aspx) der Microsoft-Website werden ausführliche Informationen zum Erstellen und Verwalten von Komponententests mit Visual Studio bereitgestellt.
- Auf der Seite [Ausführen von Leistungstests für Ihre App](https://msdn.microsoft.com/library/dn250793.aspx) der Microsoft-Website wird beschrieben, wie Sie Visual Studio Ultimate zum Erstellen eines Projekts zum Testen der Webleistung und Auslastung verwenden.

<!---HONumber=August15_HO6-->