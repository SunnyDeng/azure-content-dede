
<properties
	pageTitle="Optimieren Ihrer API-App für Logik-Apps"
	description="Dieser Artikel zeigt, wie Sie Ihre API-App für die optimale Verwendung mit Logik-Apps anpassen."
	services="app-service\api"
	documentationCenter=".net"
	authors="sameerch"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="sameerch;guayan;tarcher"/>

# Optimieren Ihrer API-App für Logik-Apps #

In diesem Artikel erfahren Sie, wie Sie die API-Definition Ihrer API-App anpassen, sodass sie optimal mit Logik-Apps funktioniert. Auf diese Weise wird die Benutzerfreundlichkeit für Ihre API-App verbessert, wenn sie im Logik-Apps-Designer verwendet wird.

## Voraussetzungen

Wenn Sie zum ersten Mal mit [API-Apps](app-service-api-apps-why-best-platform.md) in [Azure App Service](../app-service/app-service-value-prop-what-is.md) arbeiten, sollten Sie zunächst die mehrteilige Serie zum [Erstellen von API-Apps](app-service-dotnet-create-api-app.md) durcharbeiten.


## Hinzufügen von Anzeigenamen ##
Der Logik-Apps-Designer zeigt die Namen von Operationen, Feldern und Parametern an, die mitunter schwierig zu lesen sind, da sie programmgesteuert generiert werden. Zur Verbesserung der Lesbarkeit kann der Logik-Apps-Designer (sofern verfügbar) einen benutzerfreundlicheren Textwert – bezeichnet als *Anzeigename* – anstelle der Standardnamen für Operation, Feld und Parameter anzeigen. Hierzu prüft der Logik-Apps-Designer, ob bestimmte Eigenschaften in der Swagger-Metadatei enthalten sind, die über Ihre API-App bereitgestellt wird. Die folgenden Eigenschaften werden als Anzeigenamen verwendet:

* Operationen (Aktionen und Trigger): Der Wert der **summary**-Eigenschaft (sofern vorhanden), andernfalls der Wert der **operationId**-Eigenschaft. Beachten Sie, dass die Swagger 2.0-Spezifikation die Verwendung von bis zu 120 Zeichen für die **summary**-Eigenschaft zulässt.

* Parameter (Eingaben): Der Wert der **x-ms-summary**-Erweiterungseigenschaft (sofern vorhanden), andernfalls der Wert der **name**-Eigenschaft. Die **x-ms-summary**-Erweiterungseigenschaft muss dynamisch im Code festgelegt werden. Dieser Vorgang wird im Abschnitt "Verwenden von benutzerdefinierten Attributen zum Kommentieren von Erweiterungseigenschaften" in diesem Thema beschrieben. Die **name**-Eigenschaft kann unter Verwendung von ///-Kommentaren festgelegt werden. Dieser Vorgang wird im Abschnitt "Verwenden von XML-Kommentaren beim Generieren der API-Definition" in diesem Thema beschrieben.

* Schemafelder (Ausgabeantworten): Der Wert der **x-ms-summary**-Erweiterungseigenschaft (sofern vorhanden), andernfalls der Wert der **name**-Eigenschaft. Die **x-ms-summary**-Erweiterungseigenschaft muss dynamisch im Code festgelegt werden. Dieser Vorgang wird im Abschnitt "Verwenden von benutzerdefinierten Attributen zum Kommentieren von Erweiterungseigenschaften" in diesem Thema beschrieben. Die **name**-Eigenschaft kann unter Verwendung von ///-Kommentaren festgelegt werden. Dieser Vorgang wird im Abschnitt "Verwenden von XML-Kommentaren beim Generieren der API-Definition" in diesem Thema beschrieben.

**Hinweis:** Es wird empfohlen, Anzeigenamen mit einer Länge von 30 Zeichen oder weniger zu verwenden.

### Verwenden von XML-Kommentaren beim Generieren der API-Definition

Bei der Entwicklung mit Visual Studio ist es eine bewährte Methode, die API-Controller mithilfe von [XML-Kommentaren](https://msdn.microsoft.com/library/b2s063f7.aspx) mit Anmerkungen zu versehen. Bei der Kompilierung mit [/doc](https://msdn.microsoft.com/library/3260k4x7.aspx) erstellt der Compiler eine XML-Dokumentationsdatei. Das im API-App-SDK enthaltene Swashbuckle-Toolset kann diese Kommentare beim Generieren der API-Metadaten einschließen, und Sie können Ihr API-Projekt hierzu mithilfe der folgenden Schritte konfigurieren:

1. Öffnen Sie Ihr Projekt in Visual Studio.

2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.

	![Projekteigenschaften](./media/app-service-api-optimize-for-logic-apps/project-properties.png)

3. Führen Sie die folgenden Schritte aus, wenn die Eigenschaftenseiten für das Projekt angezeigt werden:

	- Wählen Sie die **Konfiguration**, für die die Einstellungen gelten. Typischerweise wählen Sie "Alle Konfigurationen", sodass die festgelegten Einstellungen sowohl für Debug- als aus für Releasebuilds gelten.
	
	- Wählen Sie die Registerkarte **Build** auf der linken Seite aus.
	
	- Vergewissern Sie sich, dass die Option **XML-Dokumentationsdatei** aktiviert ist. Visual Studio gibt einen Standarddateinamen vor, der auf dem Namen Ihres Projekts basiert. Sie können diesen Wert gemäß Ihrer Namenskonventionen ändern oder unverändert beibehalten.

	![Festlegen der XML-Doc-Eigenschaft](./media/app-service-api-optimize-for-logic-apps/xml-documentation-file-property.png)

4. Öffnen Sie die Datei *SwaggerConfig.cs* (diese befindet sich im Ordner **App_Start** des Projekts).

5. Fügen Sie am Anfang der Datei *SwaggerConfig.cs* **using**-Direktiven für die Namespaces **System** und **System.Globalization** ein.

		using System;
		using System.Globalization;
 
6. Suchen Sie in der Datei *SwaggerConfig.cs* nach einem **GetXmlCommentsPath**-Aufruf, und heben Sie die Auskommentierung der Zeile auf, sodass sie ausgeführt wird. Da Sie diese Methode noch nicht implementiert haben, unterstreicht Visual Studio den **GetXmlCommentsPath**-Aufruf um zu kennzeichnen, dass er im aktuellen Kontext nicht definiert ist. Das ist in Ordnung. Sie führen im nächsten Schritt die Implementierung durch.

7. Fügen Sie der **SwaggerConfig**-Klasse (definiert in der Datei *SwaggerConfig.cs*) die folgende Implementierung der **GetXmlCommentsPath**-Methode hinzu. Diese Methode gibt lediglich die XML-Dokumentationsdatei zurück, die Sie zuvor in den Projekteinstellungen festgelegt haben.

        public static string GetXmlCommentsPath()
        {
            return String.Format(CultureInfo.InvariantCulture, 
								 @"{0}\bin\ContactsList.xml", 
								 AppDomain.CurrentDomain.BaseDirectory);
        }

8. Abschließend geben Sie die XML-Kommentare für Ihre Controllermethoden an. Öffnen Sie hierzu eine der API-App-Controllerdateien, und geben Sie in einer leeren Zeile /// sowie eine Controllermethode ein, die Sie dokumentieren möchten. Visual Studio fügt automatisch einen kommentierten Abschnitt ein, in dem Sie eine Methodenzusammenfassung sowie Informationen zu Parametern und Rückgabewerten eingeben können.

Wenn Sie jetzt Ihre API-App erstellen und veröffentlichen, wird die Dokumentationsdatei gemeinsam mit den übrigen Elementen Ihrer API-App erzeugt und hochgeladen.

## Kategorisieren von erweiterten Operationen und Eigenschaften

Der Logik-Apps-Designer verfügt über einen eingeschränkten Anzeigebereich für Operationen, Parameter und Eigenschaften. Gleichzeitig kann eine API-App einen umfangreichen Satz an Operationen und Eigenschaften definieren. Als Ergebnis wird eine Vielzahl von Informationen auf kleinem Raum angezeigt, was für den Endbenutzer sehr unübersichtlich sein kann.

Für eine bessere Übersicht ermöglicht der Logik-Apps-Designer das Gruppieren der API-App-Operationen und Eigenschaften in benutzerdefinierten Kategorien. Durch eine sinnvolle Kategorisierung der Operationen und Eigenschaften kann die Benutzerfreundlichkeit einer API-App gesteigert werden, indem zunächst die wichtigsten Operationen und Eigenschaften angezeigt werden.

Um dies zu ermöglichen, sucht der Logik-Apps-Designer nach einer spezifischen benutzerdefinierten Herstellererweiterungseigenschaft in der Swagger-API-Definition Ihrer API-App. Diese Eigenschaft trägt den Namen **x-ms-visibility** und kann folgende Werte annehmen:

* leer oder "none": Diese Operationen und Eigenschaften sind bereits für den Benutzer sichtbar.

* "advanced": Da es sich um erweiterte Operationen und Eigenschaften handelt, sind sie standardmäßig ausgeblendet. Der Benutzer kann jedoch bei Bedarf problemlos auf sie zugreifen.

* "intern": Diese Operationen und Eigenschaften werden als Systemeigenschaften oder interne Eigenschaften behandelt und sind nicht für eine direkte Verwendung durch den Benutzer vorgesehen. Deshalb werden sie vom Designer ausgeblendet und stehen nur in der Codeansicht zur Verfügung. Für solche Eigenschaften können Sie die **x-ms-scheduler-recommendation**-Erweiterungseigenschaft angeben, um den Wert über den Logik-Apps-Designer festzulegen. Ein Beispiel finden Sie im Artikel [Hinzufügen von Triggern zu einer API-App](app-service-api-dotnet-triggers.md).


## Verwenden von benutzerdefinierten Attributen zum Kommentieren von Erweiterungseigenschaften

Wie zuvor erwähnt, werden benutzerdefinierte Herstellererweiterungseigenschaften verwendet, um die API-Metadaten zu kommentieren und so umfangreichere Informationen bereitzustellen, die im Logik-Apps-Designer verwendet werden können. Wenn Sie statische Metadaten zum Beschreiben Ihrer API-App verwenden, können Sie die Datei */metadata/apiDefinition.swagger.json* direkt in Ihrem Projekt bearbeiten, um die erforderlichen Erweiterungseigenschaften hinzuzufügen.

Für API-Apps mit dynamischen Metadaten können Sie benutzerdefinierte Attribute einsetzen, um Ihren Code zu kommentieren. Sie können anschließend einen Operationsfilter in der Datei *SwaggerConfig.cs* definieren, um nach den benutzerdefinierten Attributen zu suchen und die erforderliche Herstellererweiterung hinzufügen. Dieser Ansatz wird für das dynamische Generieren der **x-ms-summary**-Erweiterungseigenschaft nachstehend ausführlich beschrieben.

1. Definieren Sie eine Attributklasse namens **CustomSummaryAttribute**, die zum Kommentieren Ihres Codes verwendet wird.

	    [AttributeUsage(AttributeTargets.All)]
	    public class CustomSummaryAttribute : Attribute
	    {
	        public string SummaryText { get; internal set; }

	        public CustomSummaryAttribute(string summaryText)
	        {
	            this.SummaryText = summaryText;
	        }
	    }

2. Definieren Sie einen Operationsfilter namens **AddCustomSummaryFilter**, der in den Operationsparametern nach diesem benutzerdefinierten Attribut sucht.


	    using Swashbuckle.Swagger;

		...

		public class AddCustomSummaryFilter : IOperationFilter
	    {
	        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
	        {
	            if (operation.parameters == null)
	            {
	                // no parameter
	                return;
	            }

	            foreach (var param in operation.parameters)
	            {
	                var summaryAttributes = apiDescription.ParameterDescriptions.First(x => x.Name.Equals(param.name))
	                                        .ParameterDescriptor.GetCustomAttributes<CustomSummaryAttribute>();

	                if (summaryAttributes != null && summaryAttributes.Count > 0)
	                {
	                    // add x-ms-summary extension
	                    if (param.vendorExtensions == null)
	                    {
	                        param.vendorExtensions = new Dictionary<string, object>();
	                    }
	                    param.vendorExtensions.Add("x-ms-summary", summaryAttributes[0].SummaryText);
	                }
	            }
	        }
	    }

3. Bearbeiten Sie die Datei *SwaggerConfig.cs*, und fügen Sie die oben definierte Filterklasse hinzu.


            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                    {
                        ...
                        c.OperationFilter<AddCustomSummaryFilter>();
                        ...
                    }

4. Verwenden Sie die **CustomSummaryAttribute**-Klasse, um Ihren Code zu kommentieren, wie im folgenden Codeausschnitt gezeigt.

        /// <summary>
        /// Send Message
        /// </summary>
        /// <param name="queueName">The name of the Storage Queue</param>
        /// <param name="messageText">The message text to be sent</param>
        public void SendMessage(
            [CustomSummary("Queue Name")] string queueName,
            [CustomSummary("Message Text")] string messageText)
        {
             ...
        }

	Wenn Sie die oben gezeigte API-App erstellen, werden die folgenden API-Metadaten generiert:


			...
            "post": {
                ...
                "parameters": [
                    {
                        "name": "queueName",
                        "in": "query",
                        "description": "The name of the Storage Queue",
                        "required": true,
                        "x-ms-summary": "Queue Name",
                        "type": "string"
                    },
                    {
                        "name": "messageText",
                        "in": "query",
                        "description": "The message text to be sent",
                        "required": true,
                        "x-ms-summary": "Message Text",
                        "type": "string"
                    }
                ],
                ...

5. Ähnlich können Sie den Schemafilter **AddCustomSummarySchemaFilter** definieren, um die **x-ms-summary**-Erweiterungseigenschaft für Ihre Schemamodelle automatisch zu kommentieren (siehe nächstes Beispiel).

	    public class AddCustomSummarySchemaFilter: ISchemaFilter
	    {
	        public void Apply(Schema schema, SchemaRegistry schemaRegistry, Type type)
	        {
	            SetCustomSummary(schema, type.GetCustomAttribute<CustomSummaryAttribute>());

	            if (schema.properties != null)
	            {
	                foreach (var property in schema.properties)
	                {
	                    var summaryAttribute = type.GetProperty(property.Key).GetCustomAttribute<CustomSummaryAttribute>();
	                    SetCustomSummary(property.Value, summaryAttribute);
	                }
	            }
	        }

	        private static void SetCustomSummary(Schema schema, CustomSummaryAttribute summaryAttribute)
	        {
	            if (summaryAttribute != null)
	            {
	                if (schema.vendorExtensions == null)
	                {
	                    schema.vendorExtensions = new Dictionary<string, object>();
	                }
	                schema.vendorExtensions.Add("x-ms-summary", summaryAttribute.SummaryText);
	            }
	        }
	    }

## Zusammenfassung

In diesem Artikel haben Sie erfahren, wie Sie die Benutzerfreundlichkeit Ihrer API-App verbessern, wenn diese im Logik-Apps-Designer verwendet wird. Als bewährte Methode wird empfohlen, benutzerfreundliche Namen für alle Operationen (Aktionen und Trigger), Parameter und Eigenschaften bereitzustellen. Außerdem wird empfohlen, nicht mehr als 5 grundlegende Operationen bereitzustellen. Für Eingabeparameter lautet die Empfehlung, die Anzahl von grundlegenden Eigenschaften auf maximal 4 zu beschränken, für Eigenschaften liegt die Empfehlung bei 5 oder weniger. Die verbleibenden Operationen und Eigenschaften sollten als erweitert gekennzeichnet werden.
 

<!---HONumber=July15_HO3-->