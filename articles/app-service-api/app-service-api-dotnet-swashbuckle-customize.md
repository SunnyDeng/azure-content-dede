
<properties 
	pageTitle="Anpassen von mit Swashbuckle generierten API-Definitionen" 
	description="Erfahren Sie, wie Sie Swagger-API-Definitionen anpassen können, die von Swashbuckle für eine API-App in Azure App Service generiert werden." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="bradyg"/>

# Anpassen von mit Swashbuckle generierten API-Definitionen 

## Übersicht

In diesem Artikel wird erläutert, wie Swashbuckle so angepasst wird, dass allgemeine Szenarios behandelt werden, bei denen Sie das Standardverhalten möglicherweise ändern möchten:

* Swashbuckle generiert doppelte Vorgangs-IDs für Überladungen von Controllermethoden.
* In Swashbuckle wird davon ausgegangen, dass die einzige gültige Antwort einer Methode "HTTP 200" (OK) ist. 
 
## Anpassen der Generierung von Vorgangs-IDs

Swashbuckle generiert Swagger-Vorgangs-ID, indem Controllername und Methodennamen miteinander verbunden werden. Aufgrund dieses Musters kommt es zu einem Problem, wenn Sie mehrere Überladungen einer Methode haben: Swashbuckle generiert doppelte Vorgang-IDs, was eine ungültige Swagger-JSON ist.

Bei dem folgenden Controllercode generiert Swashbuckle beispielsweise drei "Contact_Get"-Vorgang-IDs.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Sie können das Problem manuell beheben, indem Sie den Methoden eindeutige Namen geben, wie z. B. in dem die folgenden Beispiel:

* Get
* GetById
* GetPage

Die Alternative besteht darin, Swashbuckle zu erweitern, damit es automatisch eindeutige Vorgangs-IDs generiert.

Die folgenden Schritte zeigen, wie Sie Swashbuckle mithilfe der Datei *SwaggerConfig.cs* anpassen können, die mit der Visual Studio-API-Apps-Vorschauprojektvorlage in das Projekt eingeschlossen wird. Sie können Swashbuckle auch in einem Web-API-Projekt anpassen, das Sie für die Bereitstellung als API-App konfigurieren.

1. Erstellen einer benutzerdefinierten `IOperationFilter`-Implementierung 

	Die `IOperationFilter`-Schnittstelle bietet einen Erweiterungspunkt für Swashbuckle-Benutzer, die verschiedene Aspekte des Swagger-Metadatenprozesses anpassen möchten. Der folgende Code veranschaulicht eine Methode zum Ändern des Verhaltens zur Vorgangs-ID-Generation. Der Code fügt Parameternamen an den Namen der Vorgangs-ID an.

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. Rufen Sie in der Datei *App_Start\\SwaggerConfig.cs* die `OperationFilter`-Methode auf, damit Swashbuckle die neue `IOperationFilter`-Implementierung verwendet.

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Die Datei *SwaggerConfig.cs*, die vom Swashbuckle NuGet-Paket abgelegt wird, enthält zahlreiche auskommentierte Beispiele für Erweiterungspunkte. Die zusätzlichen Kommentare werden hier nicht angezeigt.

	Nach dieser Änderung wird die `IOperationFilter`-Implementierung verwendet und bewirkt, dass eindeutige Vorgangs-IDs generiert werden.
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>
	
## Zulassen anderer Antwortcodes als "200"

Standardmäßig wird in Swashbuckle davon ausgegangen, dass die Antwort "HTTP 200" (OK) die *einzige* legitime Antwort einer Web-API-Methode ist. In einigen Fällen sollen jedoch möglicherweise andere Antwortcodes zurückgegeben werden, ohne dass der Client eine Ausnahme auslöst. Der folgende Web-API-Code gibt beispielsweise ein Szenario an, bei dem der Client entweder "200" oder "404" als gültige Antwort akzeptieren soll.

	[ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

In diesem Szenario gibt der Swagger, den Swashbuckle standardmäßig generiert, nur einen legitimen HTTP-Statuscode, d. h. HTTP 200, an.

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Da in Visual Studio Code für den Client mit der Swagger-API-Definition erstellt wird, wird Clientcode erstellt, der für jede von "HTTP 200" abweichende Antwort eine Ausnahme auslöst. Der folgende Code wird von einem C#-Client für diese Web-API-Beispielmethode generiert.

	if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

Swashbuckle bietet zwei Möglichkeiten zum Anpassen der Liste der erwarteten generierten HTTP-Antwortcodes: Verwendung von XML-Kommentaren oder des `SwaggerResponse`-Attributs. Die Verwendung des Attributs ist einfacher, es ist jedoch nur in Swashbuckle 5.1.5 oder späteren Versionen verfügbar. Die Vorlage "new-project" für die API-Apps-Vorschau in Visual Studio 2013 enthält Version 5.0.0 von Swashbuckle. Wenn Sie die Vorlage verwendet haben und Swashbuckle nicht aktualisieren möchten, können Sie lediglich XML-Kommentare verwenden.

### Anpassen der erwarteten Antwortcodes mithilfe von XML-Kommentaren

Verwenden Sie diese Methode zum Angeben von Antwortcodes, wenn Sie über eine ältere Version als Swashbuckle 5.1.5 verfügen.

1. Fügen Sie zunächst XML-Dokumentationskommentare für die Methoden hinzu, für die Sie HTTP-Antwortcodes angeben möchten. Wenn in der oben abgebildeten Web-API-Beispielaktion die XML-Dokumentation angewendet wird, ergibt dies den folgenden Beispielcode. 

		/// <summary>
		/// Returns the specified contact.
		/// </summary>
		/// <param name="id">The ID of the contact.</param>
		/// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
		/// <response code="200">OK</response>
		/// <response code="404">Not Found</response>
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();
		
		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

1. Fügen Sie Anweisungen in der Datei *SwaggerConfig.cs* hinzu, um Swashbuckle anzuweisen, die XML-Dokumentationsdatei zu verwenden.

	* Öffnen Sie die Datei *SwaggerConfig.cs*, und erstellen Sie eine Methode für die *SwaggerConfig*-Klasse, um den Pfad zu der XML-Dokumentationsdatei anzugeben. 

			private static string GetXmlCommentsPath()
			{
			    return string.Format(@"{0}\XmlComments.xml", 
			        System.AppDomain.CurrentDomain.BaseDirectory);
			}

	* Scrollen Sie in der Datei *SwaggerConfig.cs* nach unten, bis Sie die im folgenden Screenshot abgebildete auskommentierte Codezeile sehen.

		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
	
	* Heben Sie die Auskommentierung der Zeile auf, um die Verarbeitung von XML-Kommentaren während der Swagger-Generierung zu aktivieren.
	
		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
	
1. Um die XML-Dokumentationsdatei zu generieren, wechseln Sie in die Eigenschaften des Projekts, und aktivieren Sie die XML-Dokumentationsdatei entsprechend den Vorgaben im folgenden Screenshot.

	![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png)

Nachdem Sie diese Schritte ausgeführt haben, werden die HTTP-Antwortcodes, die Sie in den XML-Kommentaren angegeben haben, in dem von Swashbuckle generierten Swagger-JSON-Code übernommen. Im Screenshot unten ist diese neue JSON-Nutzlast dargestellt.

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

Wenn Sie mithilfe von Visual Studio den Clientcode für die REST-API neu generieren, akzeptiert der C#-Code die beiden HTTP-Statuscodes "OK" und "Not Found", ohne eine Ausnahme auszulösen, sodass der Code die Rückgabe des Kontaktdatensatzes "null" entsprechend verarbeiten kann.

		if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
		{
		    HttpOperationException<object> ex = new HttpOperationException<object>();
		    ex.Request = httpRequest;
		    ex.Response = httpResponse;
		    ex.Body = null;
		    if (shouldTrace)
		    {
		        ServiceClientTracing.Error(invocationId, ex);
		    }
        	    throw ex;
		}

Den entsprechenden Code finden Sie in [diesem GitHub-Repository](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes). Zusammen mit dem Web-API-Projekt mit XML-Dokumentationskommentaren umfasst er ein Konsolenanwendungsprojekt, das einen generierten Client für diese API enthält.

### Anpassen der erwarteten Antwortcodes mit dem SwaggerResponse-Attribut

Das [SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs)-Attribut ist in Swashbuckle 5.1.5 und späteren Versionen verfügbar. Für den Fall, dass in Ihrem Projekt eine frühere Version vorliegt, wird in diesem Abschnitt zunächst erläutert, wie Sie das NuGet-Paket "Swashbuckle" aktualisieren, sodass Sie dieses Attribut verwenden können.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Web-API-Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**. 

	![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)

1. Klicken Sie auf die Schaltfläche *Aktualisieren* neben dem NuGet-Paket *Swashbuckle*.

	![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)

1. Fügen Sie den Web-API-Aktionsmethoden, für die Sie gültige HTTP-Antwortcodes angeben möchten, die *SwaggerResponse*-Attribute hinzu.

		[SwaggerResponse(HttpStatusCode.OK)]
		[SwaggerResponse(HttpStatusCode.NotFound)]
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();

		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

2. Fügen Sie eine `using`-Anweisung für den Namespace des Attributs hinzu:

		using Swashbuckle.Swagger.Annotations;
		
1. Navigieren Sie zu der URL */swagger/docs/v1* des Projekts. Die verschiedenen HTTP-Antwortcodes werden nun im Swagger-JSON-Format angezeigt.

	![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

Den entsprechenden Code finden Sie in [diesem GitHub-Repository](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes). Zusammen mit dem Web-API-Projekt mit dem *SwaggerResponse*-Attribut umfasst er ein Konsolenanwendungsprojekt, das einen generierten Client für diese API enthält.

## Nächste Schritte

In diesem Artikel wurde erläutert, wie die Generierung von Vorgangs-IDs und von gültigen Antwortcodes in Swashbuckle angepasst werden kann. Weitere Informationen finden Sie unter [Swashbuckle auf GitHub](https://github.com/domaindrivendev/Swashbuckle).
 

<!---HONumber=58_postMigration-->