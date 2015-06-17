
<properties 
	pageTitle="Anpassen der Swashbuckle generierte API-Definitionen" 
	description="Informationen Sie zum Anpassen von Swagger API-Definitionen, die von Swashbuckle für eine API-Anwendung in Azure-App-Dienst generiert werden." 
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

# Anpassen der Swashbuckle generierte API-Definitionen 

## Übersicht

In diesem Artikel wird erläutert, wie Swashbuckle, um allgemeine Szenarien zu behandeln, sollten Sie das Standardverhalten ändern anpassen:

* Swashbuckle generiert doppelte Vorgang Bezeichner für Überladungen der Methoden des Controllers
* Swashbuckle wird davon ausgegangen, dass die einzigen gültige Antwort von einer Methode HTTP 200 (OK) 
 
## Anpassen der Generierung des Vorgangs-ID

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
	
## Antwortcodes als 200 zulassen

Standardmäßig Swashbuckle wird davon ausgegangen, dass eine Antwort HTTP 200 (OK) ist die *nur* legitime Antwort von einer Web-API-Methode. In einigen Fällen empfiehlt es sich, andere Antwortcodes zurück, ohne dass des Clients eine Ausnahme auslöst. Beispielsweise zeigt der folgende Code für die Web-API ein Szenario, in dem den Client 200 oder ein 404 als gültigen Antworten akzeptieren würde.

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

In diesem Szenario gibt der Swagger, die Swashbuckle standardmäßig generiert nur eine legitime HTTP-Statuscode HTTP 200.

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Da Visual Studio die Swagger-API-Definition zum Generieren von Code für den Client verwendet wird, erstellt er Clientcode, der für alle Antworten außer HTTP 200, eine Ausnahme auslöst. Der folgende Code wird von einem c\#-Client, die für dieses Beispiel Web-API-Methode generiert.

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

Swashbuckle bietet zwei Möglichkeiten zum Anpassen der Liste der erwarteten HTTP-Antwortcodes, die es erzeugt, unter Verwendung von XML-Kommentaren oder `SwaggerResponse` Attribut. Das Attribut ist einfacher, es ist jedoch nur in Swashbuckle 5.1.5 oder höher verfügbar. Die API-Apps Vorschau neuen-Projektvorlage in Visual Studio 2013 enthält Swashbuckle Version 5.0.0, wenn Sie die Vorlage verwendet und Swashbuckle aktualisieren möchten, ist Ihre einzige Option von XML-Kommentaren.

### Anpassen der erwarteten Antwortcodes mit XML-Kommentare

Verwenden Sie diese Methode, um die Antwortcodes angeben, ob Ihre Version Swashbuckle 5.1.5 vor.

1. Fügen Sie zunächst die XML-Dokumentationskommentare über die Methoden, die, denen Sie für HTTP-Antwortcodes angeben möchten. Machen Sie das Web-API-Beispiel ergibt oben dargestellte Aktion und die XML-Dokumentation zuweisen Code wie im folgenden Beispiel. 

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

1. Hinzufügen von Anweisungen in der *SwaggerConfig.cs* Datei leiten Swashbuckle die Nutzung der XML-Dokumentationsdatei.

	* Open *SwaggerConfig.cs* und erstellen Sie eine Methode für die *SwaggerConfig* Klasse, um den Pfad zu der XML-Dokumentationsdatei angeben. 

			private static string GetXmlCommentsPath()
			{
			    return string.Format(@"{0}\XmlComments.xml", 
			        System.AppDomain.CurrentDomain.BaseDirectory);
			}

	* Führen Sie einen Bildlauf nach unten der *SwaggerConfig.cs* Datei, bis Sie die auskommentierte Zeile von ähnlichen Code wie im Screenshot unten angezeigt.

		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
	
	* Kommentieren Sie die Zeile, um die XML-Kommentare, die während der Generierung von Swagger Verarbeitung zu ermöglichen.
	
		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
	
1. Um die XML-Dokumentationsdatei zu generieren, wechseln Sie in den Eigenschaften des Projekts aus, und aktivieren Sie die XML-Dokumentationsdatei wie im folgenden Screenshot gezeigt.

	![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png)

Nachdem Sie diese Schritte ausführen, wird der JSON-Swagger von Swashbuckle generiert HTTP-Antwortcodes wider, die Sie in die XML-Kommentare angegeben. Der folgende Screenshot zeigt diese neuen JSON-Nutzlast.

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

Wenn Sie Visual Studio erneut den Clientcode für die REST-API verwenden, akzeptiert der C#-Code HTTP OK und nicht gefunden-Statuscodes ohne das Auslösen einer Ausnahme, die im verwendeten Code zum Behandeln der Rückgabe von einer null Kontaktdatensatz Entscheidungen zu ermöglichen.

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

Der Code für diese Demo befinden sich im [GitHub-Repository](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes). Zusammen mit der Web-API ist Project mit XML-Dokumentationskommentaren markiert ein Konsolenanwendungsprojekt, das einen generierten Client für diese API enthält.

### Anpassen der erwarteten Antwortcodes mit dem SwaggerResponse-Attribut

Die [SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) -Attribut ist in Swashbuckle 5.1.5 und höher verfügbar. Für den Fall, dass Sie in Ihrem Projekt eine frühere Version haben, startet in diesem Abschnitt erläutern, wie das Swashbuckle NuGet-Paket zu aktualisieren, sodass Sie dieses Attribut verwenden können.

1. In **Projektmappen-Explorer**, mit der rechten Maustaste auf das Web-API-Projekt, und klicken Sie auf **NuGet-Pakete verwalten**. 

	![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)

1. Klicken Sie auf die *Update* neben der *Swashbuckle* NuGet-Paket.

	![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)

1. Hinzufügen der *SwaggerResponse* Attribute auf die Web-API-Aktionsmethoden für den gültigen HTTP-Antwortcodes erstellt werden sollen.

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

2. Hinzufügen einer `using` -Anweisung für das Attribut Namespace:

		using Swashbuckle.Swagger.Annotations;
		
1. Navigieren Sie zu den */swagger/docs/v1* URL des Projekts und die verschiedenen HTTP-Antwortcodes in der JSON-Swagger angezeigt werden.

	![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

Der Code für diese Demo befinden sich im [GitHub-Repository](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes). Versehen Sie zusammen mit dem Web-API-Projekt mit der *SwaggerResponse* -Attribut ist ein Konsolenanwendungsprojekt, das einen generierten Client für diese API enthält.

## Nächste Schritte

In diesem Artikel wurde gezeigt, wie anpassen, wie Swashbuckle Vorgang-Ids und gültige Antwortcodes generiert. Weitere Informationen finden Sie unter [Swashbuckle auf GitHub](https://github.com/domaindrivendev/Swashbuckle).
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->