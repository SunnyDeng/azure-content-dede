
<properties
	pageTitle="Optimieren Sie Ihre App-API für Logik Apps"
	description="Dieser Artikel beschreibt, wie Ihre App-API mit Logik Apps Spaltenliste ergänzt"
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

# Optimieren Sie Ihre App-API für Logik Apps #

In diesem Artikel erfahren Sie, wie die API-Definition der API-Anwendung definiert, damit es funktioniert gut mit Logik Apps. Dies wird die Benutzerfreundlichkeit für Ihre API-Anwendung verbessern, wenn es im Designer Logik Apps verwendet wird.

## Voraussetzungen

Wenn Sie noch [API-apps](app-service-api-apps-why-best-platform.md) in [Azure Anwendungsdiensts](../app-service/app-service-value-prop-what-is.md), es wird empfohlen, beim Lesen der mehrteiligen Reihe auf [API-apps erstellen](app-service-dotnet-create-api-app.md)


## Hinzufügen von Anzeigenamen ##
Der Logik Apps-Designer zeigt die Namen Vorgänge, Felder und Parameter, die Sie möglicherweise manchmal schwierig zu lesen, wie sie programmgesteuert generiert werden. Zur besseren Lesbarkeit anzeigen Designer Logik Apps können, in denen es verfügbar ist, einen besser lesbaren Textwert - bekannt als eine *Anzeigename* – anstatt die Standardnamen Vorgang, Feld- und Parameter. Um dies zu erreichen, scannt der Logik Apps-Designer das Vorhandensein bestimmter Eigenschaften in den Swagger-Metadaten, die von der API-Anwendung bereitgestellt. Die folgenden Eigenschaften dienen als Anzeigenamen:

* Vorgänge (Aktion und Trigger) den Wert der **Zusammenfassung** Eigenschaft, falls vorhanden, andernfalls der Wert der **Vorgangs-ID** Eigenschaft. Beachten Sie, das bis zu 120 Zeichen für die Swagger 2.0-Spezifikation ermöglicht die **Zusammenfassung** Eigenschaft.

* Parameter (Eingaben) den Wert des der **X-ms-Zusammenfassung** Extension-Eigenschaft, falls vorhanden, andernfalls den Wert der **Name** Eigenschaft. Die **X-ms-Zusammenfassung** Erweiterungseigenschaft dynamisch im Code festgelegt werden muss. Dieser Prozess wird im Abschnitt "Using Custom Attribute Erweiterungseigenschaften Kommentieren" dieses Themas beschrieben. Die **Name** -Eigenschaft verwenden / / / Kommentare festgelegt werden. Dieser Prozess wird im Abschnitt "Using XML-Kommentare in API-Definition Generation" dieses Themas beschrieben.

* Schemafelder (Ausgabeantworten) den Wert des der **X-ms-Zusammenfassung** Extension-Eigenschaft, falls vorhanden, andernfalls den Wert der **Name** Eigenschaft. Die **X-ms-Zusammenfassung** Erweiterungseigenschaft dynamisch im Code festgelegt werden muss. Dieser Prozess wird im Abschnitt "Using Custom Attribute Erweiterungseigenschaften Kommentieren" dieses Themas beschrieben. Die **Name** -Eigenschaft verwenden / / / Kommentare festgelegt werden. Dieser Prozess wird im Abschnitt "Using XML-Kommentare in API-Definition Generation" dieses Themas beschrieben.

**Hinweis:** es wird empfohlen, die Länge von Ihrem Anzeigenamen auf 30 Zeichen zu halten.

### Verwenden von XML-Kommentaren in Generieren von API-Definitionen

Für die Entwicklung mit Visual Studio ist es gängige Praxis, kommentieren die API-Controller mit [XML-Kommentare](https://msdn.microsoft.com/library/b2s063f7.aspx). Bei der Kompilierung mit [/doc](https://msdn.microsoft.com/library/3260k4x7.aspx), erstellt der Compiler eine XML-Dokumentationsdatei. Swashbuckle-Toolset, die mit dem API-App-SDK enthalten kann diese Kommentare beim Generieren der Metadaten-API-integrieren, und Sie können Ihre API-Projekt, um dies tun, indem Sie die folgenden Schritte konfigurieren:

1. Öffnen Sie das Projekt in Visual Studio.

2. Aus der **Projektmappen-Explorer**, mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften**.

	![Projekteigenschaften](./media/app-service-api-optimize-for-logic-apps/project-properties.png)

3. Wenn Eigenschaftenseiten des Projekts angezeigt werden, führen Sie die folgenden Schritte aus:

	- Wählen Sie die **Konfiguration** für die die Einstellungen angewendet werden. In der Regel müssen Sie alle Konfigurationen auswählen, so dass die von Ihnen angegebenen Einstellungen, um sowohl Debug-als anwenden auch im Releasebuild.
	
	- Wählen Sie die **Erstellen** Registerkarte auf der linken Seite
	
	- Überprüfen Sie, ob die **XML-Dokumentationsdatei** Option aktiviert ist. Visual Studio stellt ein Standarddateiname basierend auf den Namen des Projekts zur Verfügung. Sie können setzen ihren Wert auf, was Ihren Benennungskonventionen erfordert oder lassen Sie es als-ist.

	![Legen Sie XML-Dokument-Eigenschaft](./media/app-service-api-optimize-for-logic-apps/xml-documentation-file-property.png)

4. Öffnen der *SwaggerConfig.cs* Datei (im Verzeichnis des Projekts **App_Start** Ordner).

5. Hinzufügen **mit** Direktiven am oberen Rand der *SwaggerConfig.cs* Datei für die **System** und **System.Globalization** Namespaces.

		using System;
		using System.Globalization;
 
6. Suchen Sie die *SwaggerConfig.cs* -Datei für einen Aufruf von **GetXmlCommentsPath**, und kommentieren Sie die Zeile, sodass es ausgeführt wird. Da diese Methode noch nicht implementiert haben, wird Visual Studio den Aufruf von unterstrichen **GetXmlCommentsPath** und anzugeben, dass im aktuellen Kontext nicht definiert ist. Das ist kein Problem. Sie müssen sie im nächsten Schritt implementieren.

7. Fügen Sie die folgende Implementierung von der **GetXmlCommentsPath** -Methode, um die **SwaggerConfig** Klasse (definiert der *SwaggerConfig.cs* Datei). Diese Methode gibt einfach die XML-Dokumentationsdatei zurück, die Sie zuvor in den Einstellungen für das Projekt angegeben.

        public static string GetXmlCommentsPath()
        {
            return String.Format(CultureInfo.InvariantCulture, 
								 @"{0}\bin\ContactsList.xml", 
								 AppDomain.CurrentDomain.BaseDirectory);
        }

8. Abschließend geben Sie die XML-Kommentare für die Controllermethoden. Hierzu öffnen Sie die API-app Controller-Dateien aus, und geben Sie / / / auf eine leere Zeile vor eine Controllermethode zu dokumentieren. Visual Studio fügt automatisch einen kommentierten Abschnitt, in dem können Sie eine Methode, die Zusammenfassung als auch Parameter angeben und Rückgabewertinformationen.

Nun, beim Erstellen und veröffentlichen Sie die API-app, sehen Sie, dass die Dokumentationsdatei auch in der Nutzlast ist und mit dem Rest der API-Anwendung hochgeladen.

## Kategorisieren Sie, erweiterte Prozesse und Eigenschaften

Der Designer Logik Apps beschränkte Platz auf dem Bildschirm für die Anzeige der Vorgänge, Parameter und Eigenschaften. Darüber hinaus kann eine App API einen umfangreichen Satz von Operationen und Eigenschaften definieren. Das Ergebnis so viele Informationen, die angezeigt wird, in einem kleinen Bereich zur Folge haben kann können mithilfe des Designers schwierig für den Endbenutzer.

Um die Übersichtlichkeit zu verringern, ermöglicht der Logik Apps-Designer, Prozesse und Eigenschaften der API-Anwendung in eine benutzerdefinierte Kategorien einzuteilen. Mithilfe einer entsprechenden Kategorisierung der Eigenschaften und Operationen kann eine API-Anwendung präsentieren zuerst die einfachste und nützliche Prozesse und Eigenschaften, um die Benutzerfreundlichkeit verbessern.

Um diese Möglichkeit bereitzustellen, sucht der Logik Apps-Designer das Vorhandensein einer bestimmten benutzerdefinierten Hersteller Extension-Eigenschaft in der Swagger-API-Definition der API-App. Diese Eigenschaft wird mit dem Namen **X-ms-Sichtbarkeit** und kann folgende Werte annehmen:

* leer, oder "none", diese Prozesse und Eigenschaften leicht können vom Benutzer angezeigt werden.

* "advanced", wie diese Prozesse und Eigenschaften erweitert werden, werden sie standardmäßig ausgeblendet. Allerdings kann Benutzer problemlos darauf zugreifen bei Bedarf.

* "Interner" diese Prozesse und Eigenschaften als System- oder internen Eigenschaften behandelt und nicht direkt vom Benutzer verwendet werden soll. Daher sind sie vom Designer ausgeblendet ist und nur in der Codeansicht verfügbar. Für diese Eigenschaften können Sie auch angeben der **X-ms-Scheduler-Empfehlung** Extension-Eigenschaft zum Festlegen des Werts durch den Logik Apps-Designer. Ein Beispiel finden Sie im Artikel auf [Trigger zu einer API-App hinzufügen](app-service-api-dotnet-triggers.md).


## Verwenden benutzerdefinierte Attribute Erweiterungseigenschaften zu kommentieren.

Wie bereits erwähnt, werden benutzerdefinierte Anbieter Erweiterungseigenschaften verwendet, kommentieren Sie die Metadaten-API-um umfangreichere Informationen bereitzustellen, die der Logik Apps-Designer verwenden können. Wenn Sie statische Metadaten zur Beschreibung der API-app verwenden, können Sie direkt bearbeiten der */metadata/apiDefinition.swagger.json* in Ihrem Projekt die notwendigen Erweiterungseigenschaften manuell hinzufügen.

Für API-apps, dynamische Metadaten verwenden, können Sie mithilfe von benutzerdefinierten Attributen Code mit Anmerkungen versehen. Sie können dann definieren ein Filters Vorgang in der *SwaggerConfig.cs* -Datei für die benutzerdefinierten Attribute zu suchen und Hinzufügen der erforderlichen Vendor-Erweiterung. Dieser Ansatz ist für ausführlich beschrieben unter dynamisch generieren die **X-ms-Zusammenfassung** Extension-Eigenschaft.

1. Definieren Sie eine Attributklasse namens **CustomSummaryAttribute** Kommentieren von Code verwendet wird.

	    [AttributeUsage(AttributeTargets.All)]
	    public class CustomSummaryAttribute : Attribute
	    {
	        public string SummaryText { get; internal set; }

	        public CustomSummaryAttribute(string summaryText)
	        {
	            this.SummaryText = summaryText;
	        }
	    }

2. Definieren Sie einen Vorgang Filter mit dem Namen **AddCustomSummaryFilter** die für dieses benutzerdefinierte Attribut in den Parametern Vorgang sieht.


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

3. Bearbeiten der *SwaggerConfig.cs* Datei, und fügen Sie die oben definierte Filterklasse.


            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                    {
                        ...
                        c.OperationFilter<AddCustomSummaryFilter>();
                        ...
                    }

4. Verwenden der **CustomSummaryAttribute** Klasse, um den Code mit Anmerkungen versehen, wie im folgenden Codeausschnitt gezeigt.

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

	Wenn Sie die oben genannten API-Anwendung erstellen, generiert es die folgende API-Metadaten:


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

5. Sie können auf ähnliche Weise SchemaFilter definieren **AddCustomSummarySchemaFilter** automatisch zu kommentieren der **X-ms-Zusammenfassung** Erweiterungseigenschaft für die Schemamodelle, wie im folgenden Beispiel.

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

In diesem Artikel haben gesehen, wie Sie die Benutzerfreundlichkeit der API-Anwendung zu verbessern, wenn es im Designer Logik Apps verwendet wird. Als bewährte Methode empfiehlt es sich, dass Sie richtige Anzeigenamen für alle Vorgänge (Aktionen und Trigger), Parameter und Eigenschaften angeben. Es wird auch empfohlen, dass Sie nicht mehr als fünf grundlegende Vorgänge bereitstellen. Für Eingabeparameter wird empfohlen, die Anzahl der grundlegenden Eigenschaften nicht mehr als 4 einzuschränken, und für Eigenschaften, die Empfehlung lautet, Version 5 oder niedriger. Der Rest der Operationen und Eigenschaften gekennzeichnet werden soll als erweitert.
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->