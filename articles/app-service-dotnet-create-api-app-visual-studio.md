<properties 
	pageTitle="Konvertieren einer vorhandenen API in eine API-App" 
	description="Erfahren Sie, wie Sie ein Web-API-Projekt in Visual Studio für die Bereitstellung als API-App in Azure App Service konfigurieren." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Konvertieren einer vorhandenen API in eine API-App

## Übersicht

In diesem Lernprogramm erstellen Sie ein ASP.NET-Web-API-Projekt in Visual Studio und fügen dann NuGet-Pakete und Projektmetadaten hinzu, mit denen Sie das Projekt als API-App in Azure App Service veröffentlichen und bereitstellen. Außerdem erklärt das Lernprogramm, wie die API-App-Metadaten angepasst werden.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Erstellen eines Web-API-Projekts

1.  Öffnen Sie in Visual Studio das Dialogfeld **Neues Projekt**.

2.  Wählen Sie im Bereich **Installierte Vorlagen** den Knoten **Cloud** aus, und wählen Sie dann die Vorlage **ASP.NET-Webanwendung**.

3.  Benennen Sie das Projekt *ContactsList*, und klicken Sie auf **OK**.

	![New Project dialog](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate.png)

4. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage **Leer** aus, klicken Sie auf das Kontrollkästchen **Web-API**, deaktivieren Sie das Kontrollkästchen **In der Cloud hosten**, und klicken Sie dann auf **OK**.

	![Azure API App template](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate2.png)

	Visual Studio erstellt Projektdateien für ein leeres Web-API-Projekt.

	![Web API files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

3. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Modelle**, und klicken Sie dann im Kontextmenü auf **Hinzufügen > Klasse**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-class-v2.png) 

4. Nennen Sie die neue Datei *Contact.cs*, und klicken Sie dann auf **Hinzufügen**.

5. Ersetzen Sie den Inhalt der neuen Datei durch den folgenden Code. 

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. Klicken Sie mit der rechten Maustaste auf den Ordner **Controller**, und klicken Sie dann im Kontextmenü auf **Hinzufügen > Controller**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller.png)

6. Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **Web API 2 Controller - Leer** aus, und klicken Sie auf **Hinzufügen**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-dialog.png)

7. Nennen Sie den Controller **ContactsController**, und klicken Sie auf **Hinzufügen**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-name.png)

8. Ersetzen Sie den Code in der neuen Controllerdatei durch den folgenden Code. 

		using ContactsList.Models;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactsList.Controllers
		{
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]{
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"},
		            };
		        }
		    }
		}


Jetzt haben Sie ein funktionierendes Web-API-Projekt. Die einfachste Möglichkeit, die Funktionalität zu überprüfen, besteht darin, die Get-Methode von einem Browser aus aufzurufen.

6. Drücken Sie STRG+F5, um das Projekt auszuführen.

	Der Browser zeigt einen HTTP 403-Fehler an, da er ohne eine vollständige URL geöffnet wird, die auf die Get-Methode verweist.

7. Fügen Sie der URL in der Adresszeile des Browsers "api/contacts/get/" hinzu, und drücken Sie dann die EINGABETASTE.  Die endgültige URL sieht in etwa folgendermaßen aus:

		http://localhost:25735/api/contacts/get/

	Andere Browser reagieren unterschiedlich auf einen API-Aufruf. Wenn Sie den Internet Explorer verwenden, sehen Sie eine Downloadnachricht am unteren Rand des Browserfensters:

	![](./media/app-service-dotnet-create-api-app-visual-studio/get-response.png)

## Konfigurieren der Anwendung für die Bereitstellung als API-App

1. Klicken Sie im **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt (nicht die Projektmappe), und klicken Sie dann auf **Hinzufügen > Azure-API-App-SDK**.

	![Add API app metadata](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

2. Klicken Sie im Dialogfeld **API-App-Metadatenquelle wählen** auf **Automatische Metadatengenerierung**.

	![Choose automatic metadata](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	Diese Option aktiviert die dynamische Swagger-Benutzeroberfläche, die Sie später in diesem Lernprogramm kennen lernen werden. Als Alternative können Sie eine statische Swagger-API-Definitionsdatei bereitstellen. Wählen Sie dazu **Statische JSON-Datei mit Swagger-Metadaten angeben**. Visual Studio fordert Sie dann auf, eine Datei hochzuladen. Die von Ihnen hochgeladene Datei wird als *apiDefinition.Swagger.json* im Ordner *Metadata* gespeichert.

3. Klicken Sie auf **OK**.

	Visual Studio fügt eine Datei *apiapp.json* und einen Ordner *Metadata* hinzu.

	![API App files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatainse.png)

	Außerdem fügt Visual Studio Swashbuckle NuGet-Pakete hinzu. In den folgenden Schritten probieren Sie die dynamische Benutzeroberfläche mit API-Definitionen aus, die von Swagger bereitgestellt wird. 

6. Drücken Sie STRG+F5, um das Projekt auszuführen.

	Wie zuvor zeigt der Browser einen HTTP 403-Fehler an.

7. Fügen Sie der URL in der Adresszeile des Browsers "swagger/" hinzu, und drücken Sie dann die EINGABETASTE.  Die endgültige URL sieht in etwa folgendermaßen aus:

		http://localhost:25735/swagger/

8. Klicken Sie auf der Swagger-Seite auf **Kontakte**, um die verfügbaren Methoden anzuzeigen.
 
	Nur `Get` wird angezeigt, da Sie nur diese Methode in Ihrem Controller "Kontakte" erstellt haben. Die Seite zeigt die JSON-Beispielantwort an.

9. Klicken Sie auf die Get-Methode, um eine JSON-Beispielantwort und eine Schaltfläche **Testen Sie es** anzuzeigen.

 	![Swagger - expand Contacts](./media/app-service-dotnet-create-api-app-visual-studio/swagger1.png)

9. Klicken Sie auf **Testen Sie es**.

	Die Antwort, die Sie im ContactsController codiert haben, wird zurückgegeben.

 	![Swagger - Try it out](./media/app-service-dotnet-create-api-app-visual-studio/swagger2.png)

Ihr Web-API-Projekt kann jetzt als API-App in Azure App Service bereitgestellt werden. In den folgenden Abschnitten dieses Lernprogramms erhalten Sie Informationen zu den Metadaten, die Sie zum Anpassen Ihrer API-App ändern können. 

## Überprüfen von apiapp.json

Die Einstellungen in der Datei *apiapp.json* bestimmen, wie die API-App identifiziert wird und wie sie im Azure Marketplace dargestellt wird. In dieser Vorabversion umfasst Visual Studio nicht die Möglichkeit zum Veröffentlichen von API-Apps im Marketplace, sodass viele dieser Einstellungen keine Auswirkungen haben.

![apiapp.json in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/apiappjsoninse.png)

Der ursprüngliche Inhalt der Datei, die bei Auswahl des Menüeintrags **Azure-API-App-SDK** erstellt wird, entspricht in etwa dem folgenden Beispiel:

		{
		  "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		  "id": "ContactsList",
		  "namespace": "",
		  "gateway": "2015-01-14",
		  "version": "1.0.0",
		  "title": "ContactsList",
		  "summary": "",
		  "author": "",
		  "endpoints": {
		    "apiDefinition": "/swagger/docs/v1",
		    "status": null
		  }
		}

In der folgende Tabelle werden das Format und die Verwendung der Felder in der Datei sowie zusätzliche optionale Felder erläutert, die Sie hinzufügen können. 

**Hinweis:** Wie oben erwähnt, besitzen viele dieser Felder in dieser Vorabversion keine Auswirkungen, da sie die Präsentation der API-App im Azure Marketplace festlegen, Visual Studio jedoch noch nicht die Möglichkeit zum Veröffentlichen von API-Apps im Marketplace bietet.

| Name (fett = erforderlich) | Typ | Kommentare |
|:-----------|:------------|:------------|
|**id**           |string|Die ID des Pakets. Muss innerhalb eines Namespace eindeutig sein und darf keine Punkte, Schrägstriche (/) oder @-Zeichen enthalten. Wenn Sie das Projekt bereitstellen, überprüft Visual Studio, ob die ID eindeutig ist, und bietet Ihnen die Möglichkeit, sie bei Bedarf zu ändern. 
|**namespace**    |string|Der Namespace, der zusammen mit der **id**-Eigenschaft die API-App eindeutig identifiziert.  Die Eigenschaft ist erforderlich, der Wert kann jedoch eine leere Zeichenfolge sein. <br/><br/>Diese Eigenschaft ermöglicht Ihnen die Angabe einer Domäne, z. B. "contoso.com".  Wenn die gewünschte Paket-ID bereits belegt ist, können Sie eine Domäne hinzufügen, damit Sie die Paket-ID verwenden können. Wenn beispielsweise ContactsList in der API-Apps-Galerie bereits ohne Namespace vorhanden ist, können Sie ein ContactsList-Paket mit dem Namespace "contoso.com" hinzufügen. <br/><br/>Ein weiterer Grund für die Angabe der Domäne ist das Hinzufügen eines Pakets zur API-Apps-Galerie, auf das nur Mitglieder Ihrer Organisation zugreifen können. <br/><br/>Nach der Konvertierung von Punkten in Bindestriche und von Bindestrichen in zwei Bindestriche (--) wird der Namespace als Herausgebername im Marketplace verwendet.<br/><br/>Der Namespace ist "microsoft.com" für von Microsoft bereitgestellte API-Apps.  
|**version**      |string|[Semver](http://docs.nuget.org/Create/Versioning)-Format, z. B. 1.0.1, 1.1.0-alpha.
|**gateway**      |string|Gateway-Version, ausgedrückt als Datum, zum Beispiel: 2015-01-14. Ein *gateway* ist eine spezielle Web-App, über die alle Anforderungen an die API-Apps in einer Ressourcengruppe weitergeleitet werden. Eine ihrer wichtigsten Funktionen ist die Authentifizierung. Derzeit ist die einzige Gatewayversion 2015-01-14. In Zukunft, wenn neue Gatewayversionen freigegeben werden, gibt diese Eigenschaft Ihnen die Gelegenheit, fehlerhafte Änderungen zu vermeiden und die vorherige Gateway-API weiterhin zu verwenden. 
|**title**        |string|Der angezeigte Name der API-App.
|**summary**      |string|Eine kurze Zusammenfassung der API-App, max. 100 Zeichen.
|description      |string|Die vollständige Beschreibung der API-App. Kann HTML enthalten und max. 1.500 Zeichen umfassen.
|**author**       |string|Die Verfasser der API-App, max. 256 Zeichen.
|homepage         |URI|Die Homepage der API-App.
|endpoints        |object[]|Ein Array mit einem Element, das einen Endpunkt der API-Definition enthalten kann. 
|>>endpoints.apiDefinition|string|Relativer URI der dynamischen Benutzeroberfläche mit Swagger-API-Definition (z. B. "/swagger/docs/v1") oder einer statischen Swagger-API-Definitionsdatei. Für die ASP.NET-Web-API ist die dynamisch generierte Swagger-Benutzeroberfläche in der Regel die beste Wahl. Wenn dies in Ihrer API jedoch nicht funktioniert oder wenn Sie die ASP.NET-Web-API nicht verwenden, können Sie eine statische Definitionsdatei angeben. Zum Bereitstellen einer statischen Definitionsdatei können Sie hier den relativen URI angeben, der darauf verweist, oder diese Eigenschaft leer lassen und die statische API-Definitionsdatei als [apiDefinition.swagger.json](#apidef) in den Metadatenordner aufnehmen. 
|>>endpoints.status|URI|Zur zukünftigen Verwendung reserviert.
|categories       |string[]|Legt fest, wo das Paket in Azure Marketplace angezeigt wird. Gültige Werte sind: social, enterprise, integration, protocol, app-datasvc, other. Standard: other.
|license          |object|Die Lizenz der API-App.
|>>**license.type**|string|Der SPDX-Lizenzbezeichner, z. B. MIT.
|>>license.url    |url|Die absolute URL, die auf den vollständigen Lizenztext verweist.
|>>license.requireAcceptance|bool|Gibt an, ob eine Lizenz vor der Installation genehmigt werden muss. Standard: false.
|links            |object[]|Ein Array von Links, die der Marketplace-Seite hinzugefügt werden sollen.
|>>**links.text** |string|Der Text des Links.
|>>**links.url**  |url|Die URL des Links.
|authentication|object[]|Ein Array, das angibt, welche Art der Authentifizierung diese API-App benötigt, um ausgehende API-Aufrufe zu tätigen.  Z. B. muss ein DropBox-Connector bei DropBox und ein Salesforce-Connector bei Salesforce authentifiziert werden.
|>>authentication.type|string|Folgende Werte werden unterstützt (Groß-/Kleinschreibung nicht relevant): Box, DropBox, Facebook, GitHub, Google, Instagram, Marketo, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, SugarCRM, Twitter, Yammer. Basierend auf diesem Wert weiß das Portal, welche Konfigurationswerte als Client-ID und geheimer Clientschlüssel erforderlich sind. 
|>>authentication.scopes|string[]|Ein Array von Bereichen, die für den Authentifizierungstyp spezifisch sind.
|copyright        |string|Der Urheberrechtshinweis der API-Anwendung.
|brandColor       |string|Eine optionale Markenfarbe für die Benutzeroberfläche in CSS-kompatiblem Format, z. B. #abc, red.
|tags             |string[]|Eine Liste der Tags im Zusammenhang mit dem Paket.

<!--todo add when ready to document dependencies
|dependencies    |object[]|Ein Array mit Paketabhängigkeiten.
|>>dependencies.id|string|Die ID des Abhängigkeitspakets.
|>>dependencies.domain|string|Die Domäne des Abhängigkeitspakets.
|>>dependencies.version|string|Die Version des Abhängigkeitspakets.
-->

<!--todo add when ready to document status URI
Ein URI für eine Get-Methode eines Webdiensts, die einen Wert zurückgibt, der den aktuellen Status der API-App zeigt. Wenn Sie diesen URI angeben, zeigt das Portal den aktuellen Betriebsstatus der API-App sowie weitere Informationen über die API-App an. Beispiel:  wird ausgeführt, Kontingent fast erreicht, SSL-Zertifikat läuft ab usw. Das Format der vom Portal erwarteten JSON-Datei wird unten im Anschluss an diese Tabelle gezeigt. Wenn Sie keinen endpoints.status-URI angeben, zeigt das Portal den Status der Azure-Plattform als den Status der API-App an.
Hier sehen Sie ein Beispiel für das erwartete Format der JSON-Antwort von der Get-Methode, auf die `endpoints.status` verweist:
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
Die `name`-Eigenschaft ist eine kurze Beschreibung des Status, `message` ist eine längere Beschreibung und `level` kann für den normalen Status "Fehler", "Warnung" oder "Info" lauten.
-->

## Überprüfen des Metadatenordners

Der Metadatenordner kann Symbole und Screenshots für die API-Apps-Galerie, eine Swagger-Datei zur Dokumentierung der API sowie die Konfiguration der Benutzeroberfläche für das Azure-Portal enthalten. All diese Informationen sind optional.

<!-- todo: add later
an Azure Resource Manager template that specifies resources required, 
-->

![Metadata folder in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatafolderinse.png)

**Hinweis:** Wie oben für *apiapps.json* beschrieben, besitzen Metadaten, die sich auf die Darstellung der API-App in Azure Marketplace beziehen, für diese Vorabversion keine Auswirkungen, da Visual Studio die Möglichkeit zum Veröffentlichen von API-Apps im Marketplace nicht bereitstellt.

### Der Ordner "Metadata/icons"

Sie können Symbole für die Anzeige in der Galerie bereitstellen. 
Wenn Sie keine benutzerdefinierten Symbole angeben, werden generische API-App-Symbole verwendet. Symboldateien müssen im PNG-Format vorliegen und diesen Namens- und Größenvorgaben entsprechen:

<!--todo: also used in the workflow designer--> 

|Dateiname|Größe
|:-----|:-----:
|small.png|40 x 40
|medium.png|90 x 90
|large.png|115X115
|hero.png|815 x 290
|wide.png|255 x 115

### Der Ordner "Metadata/screenshots"

Sie können bis zu fünf Screenshots für die Anzeige in der Galerie bereitstellen. Die Bilddateien müssen im PNG-Format in der Größe 533 x 324 vorliegen.

### <a id="apidef"></a>Die Datei "Metadata/apiDefinition.swagger.json"

Anhand einer [Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md)-Formatdatei können Sie die API-Definition der API-App beschreiben. So können Sie API-Definitionen statisch im Paket verfügbar machen. 

<!--todo Explain why this is useful. Original text:
Static API definitions are required for workflow designers to understand the API App triggers and actions before provisioning.-->

<!-- todo: find out if there is something to replace this -- could be deploymentTemplates subfolder.
### Die Datei "resourceTemplate.delta.json"
Sie können eine benutzerdefinierte Azure-Ressourcen-Manager-Vorlage angeben, die während der Bereitstellung der API-App ausgeführt wird. Die in dieser Änderungsdatei angegebenen Ressourcen werden den Ressourcen hinzugefügt, die standardmäßig für eine API-App erstellt werden. Wenn Ihre API-App beispielsweise eine SQL-Datenbankinstanz erfordert, können Sie mit dieser Datei die automatische Bereitstellung der Datenbank  sowie die Einstellung der Verbindungszeichenfolge in den Konfigurationseinstellungen erreichen, wenn die API-App bereitgestellt wird.
-->  

### Die Datei "Metadata/UIDefinition.json"

Sie können Informationen der Benutzeroberfläche, wie z. B. Hinweise und Validierung, im [Azure
Marketplace-Format](https://auxdocs.azurewebsites.net/de-de/documentation/articles/gallery-items) bereitstellen.

### Der Ordner "Metadata/deploymentTemplates"

Visual Studio erstellt diesen Ordner, wenn Sie den Menüeintrag **Azure-API-App-SDK** auswählen. Für diese Vorabversion wird er jedoch nicht verwendet.

## Nächste Schritte

Ihr Web-API-Projekt kann jetzt als API-App bereitgestellt werden. Befolgen Sie hierzu das Lernprogramm [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md).

Weitere Informationen finden Sie unter [Was sind API-Apps?](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->