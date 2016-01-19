<properties
	pageTitle="Erste Schritte mit API-Apps und ASP.NET in Azure App Service | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit Visual Studio 2015 eine ASP.NET-API-App in Azure App Service erstellen, bereitstellen und nutzen."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/05/2016"
	ms.author="tdykstra"/>

# Erste Schritte mit API-Apps und ASP.NET in Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Übersicht

In diesem Tutorial erstellen Sie eine [App Service-API-App](app-service-api-apps-why-best-platform.md), stellen eine ASP.NET-Web-API für die API-App bereit und nutzen die API-App über einen ASP.NET MVC-Client. Im Tutorial wird davon ausgegangen, dass Sie mit ASP.NET vertraut sind, aber noch keine Erfahrung mit Microsoft Azure haben. Nach Abschluss des Tutorials verfügen Sie über eine Web-API und eine Clientanwendung, die in der Cloud ausgeführt wird.

Bei der Beispielanwendung handelt es sich um eine einfache Kontaktliste. Die folgende Abbildung veranschaulicht, wie mit der MVC-App die Daten angezeigt werden, die von der API empfangen werden.

![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

## Sie lernen Folgendes

Drei Features von Azure App Service sind für das Entwickeln und Hosten von APIs besonders hilfreich:

* Integrierte Unterstützung für API-Metadaten
* CORS-Unterstützung
* Unterstützung für Authentifizierung und Autorisierung
 
Dies ist das erste Tutorial in einer Reihe, in der diese Features vorgestellt werden. In diesem Tutorial geht es hauptsächlich um API-Metadaten, im zweiten um CORS, und in den restlichen Tutorials liegt der Schwerpunkt auf der Authentifizierung und Autorisierung.

In diesen Tutorials lernen Sie Folgendes:

* Vorbereiten Ihres Computers für die Azure-Entwicklung durch Installieren des Azure SDK für .NET
* Verwenden von API-Apps und Web-Apps in Azure App Service mit integrierten Tools von Visual Studio 2015
* Automatisieren der API-Ermittlung mit dem Swashbuckle-NuGet-Paket zum dynamischen Generieren der JSON-Daten für die Swagger-API
* Automatisches Generieren von Clientcode zum Nutzen einer API-App über einen .NET-Client
* Verwenden des Azure-Portals zum Konfigurieren des Endpunkts für die Metadaten der API-App
* Verwenden von CORS zum Aufrufen einer API-App aus einem JavaScript-Client, wenn der Client aus einer anderen Domäne als die API stammt
* Verwenden von Azure Active Directory (Azure AD) zum Schützen einer API vor Zugriff ohne Authentifizierung
* Nutzen einer geschützten API für Benutzer, die an Azure AD angemeldet sind
* Nutzen einer geschützten API mit einem Azure AD-Dienstprinzipal

## Voraussetzungen

[AZURE.INCLUDE [Voraussetzungen](../../includes/app-service-api-dotnet-get-started-prereqs.md)]

[AZURE.INCLUDE [set-up-dev-environment](../../includes/install-sdk-2015-2013.md)]

Für dieses Tutorial ist Version 2.8.1 des Azure-SDK für .NET erforderlich.

## Übersicht über die Beispielanwendung

Der Code, den Sie für eine API-App bereitstellen, und eine Web-App für dieses Tutorial finden Sie im GitHub-Repository [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list). Die Visual Studio-Projektmappe ContactsList enthält die folgenden Projekte, die in diesem Tutorial verwendet werden:

* **ContactsList.API**: Ein ASP.NET-Web-API-Projekt, mit dem eine Liste von Namen und E-Mail-Adressen zurückgegeben wird. Beim ersten Aufruf der Get-Methode werden drei hartcodierte Kontakte zurückgegeben, und mit den nachfolgenden Aufrufen der Methoden Put, Post und Delete werden die Änderungen in einer lokalen JSON-Datei gespeichert.
* **ContactsList.MVC**: Ein ASP.NET MVC-Client für die „ContactsList“-API.

In den nachfolgenden Tutorials werden andere Projekte der gleichen Projektmappe verwendet:

* **ContactsList.Angular:** AngularJS-Client zum Veranschaulichen der CORS-Unterstützung
* **ContactsList.Angular.AAD:** AngularJS-Client zum Veranschaulichen der Benutzerauthentifizierung
* **CompanyContacts.API:** ASP.NET-Web-API-Projekt zum Veranschaulichen der Dienstkontoauthentifizierung  

## Herunterladen der Beispielanwendung 

1. Laden Sie das Repository [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) herunter.

	Sie können eine [ZIP-Datei herunterladen](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list/archive/master.zip) oder das Repository auf Ihrem lokalen Computer klonen.

2. Öffnen Sie die Projektmappe „ContactsList“ in Visual Studio 2015 oder 2013.

2. Erstellen Sie die Projektmappe, um die NuGet-Pakete wiederherzustellen.

## Verwenden von Swagger-Metadaten und -Benutzeroberfläche

In Azure App Service ist Unterstützung für [Swagger 2.0](http://swagger.io/)-API-Metadaten integriert. Jede API-App kann einen URL-Endpunkt definieren, der Metadaten für die API im Swagger-JSON-Format zurückgibt. Die Metadaten, die von diesem Endpunkt zurückgegeben werden, können zum Generieren von Clientcode verwendet werden.

Um Swagger 2.0-Metadaten für ein ASP.NET-Web-API-Projekt bereitzustellen, installieren Sie das NuGet-Paket [Swashbuckle](https://www.nuget.org/packages/Swashbuckle). Bei Swashbuckle wird die Reflexion verwendet, um Metadaten dynamisch zu generieren. Das NuGet-Paket „Swashbuckle“ ist bereits im Projekt „ContactsList.API“ installiert, das Sie heruntergeladen haben. Es ist bereits installiert, wenn Sie mit der Projektvorlage **Azure-API-App** ein neues Projekt erstellen. (In Visual Studio: **Datei > Neu > Projekt > ASP.NET-Webanwendung > Azure-API-App**.)

In diesem Abschnitt des Tutorials geht es um die generierten Swagger 2.0-Metadaten, und dann probieren Sie eine Testbenutzeroberfläche aus, die auf den Swagger-Metadaten basiert.

2. Legen Sie das Projekt „ContactsList.API“ als Startprojekt fest. (Nicht das Projekt „CompanyContacts.API“, das in einem der späteren Tutorials verwendet wird.) 
 
4. Drücken Sie F5, um das Projekt im Debugmodus auszuführen.

	Der Browser wird geöffnet, und die Seite „403 Verboten“ wird angezeigt.

	![](./media/app-service-api-dotnet-get-started/403.png)

12. Fügen Sie in der Adressleiste des Browsers „`swagger/docs/v1`“ am Ende der Zeile hinzu, und drücken Sie die EINGABETASTE. (Die URL lautet `http://localhost:51864/swagger/docs/v1`.)

	Dies ist die Standard-URL, die von Swashbuckle zum Zurückgeben von Swagger 2.0-JSON-Metadaten für die API verwendet wird. Wenn Sie Internet Explorer verwenden, fordert der Browser Sie zum Herunterladen der Datei „v1.json“ auf.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Wenn Sie Chrome oder Edge verwenden, zeigt der Browser die JSON-Daten im Browserfenster an.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	Im folgenden Beispiel ist der erste Abschnitt der Swagger-Metadaten für die API mit der Definition für die Get-Methode zu sehen. Diese Metadaten stellen die Grundlage der Swagger-Benutzeroberfläche dar, die Sie in den folgenden Schritten verwenden. Sie nutzen sie auch in einem späteren Abschnitt des Tutorials, um Clientcode automatisch zu generieren.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ContactsList.API"
		  },
		  "host": "localhost:51864",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/Contacts": {
		      "get": {
		        "tags": [ "Contacts" ],
		        "operationId": "Contacts_Get",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/Contact" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. Schließen Sie den Browser.

3. Öffnen Sie im Projekt „ContactsList.API“ im **Projektmappen-Explorer** die Datei *App\_Start\\SwaggerConfig.cs*, und scrollen Sie dann nach unten zum folgenden Code. Heben Sie die Auskommentierung für diesen Code auf.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	Die Datei „SwaggerConfig.cs“ wird erstellt, wenn Sie das Swashbuckle-Paket in einem Projekt installieren. Die Datei bietet unterschiedliche Möglichkeiten zur Konfiguration von Swashbuckle.

	Mit dem Code, für den Sie die Auskommentierung aufgehoben haben, wird die Swagger-Benutzeroberfläche aktiviert, die Sie in den folgenden Schritten verwenden. Wenn Sie ein Web-API-Projekt erstellen, indem Sie die API-App-Projektvorlage verwenden, wird dieser Code als Sicherheitsmaßnahme standardmäßig auskommentiert.

5. Führen Sie das Projekt erneut aus.

3. Fügen Sie in der Adressleiste des Browsers „`swagger`“ am Ende der Zeile hinzu, und drücken Sie die EINGABETASTE. (Die URL lautet `http://localhost:51864/swagger`.)

4. Klicken Sie auf der Seite der Swagger-Benutzeroberfläche auf **Kontakte**, um die verfügbaren Methoden anzuzeigen.

	![](./media/app-service-api-dotnet-get-started/contactsmethods.png)

5. Klicken Sie auf **Abrufen > Ausprobieren**.

	Auf der Swagger-Benutzeroberfläche wird die ContactsList-Get-Methode aufgerufen, und die JSON-Ergebnisse werden angezeigt.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Klicken Sie auf **Post** und dann in das Feld unter **Modellschema**.

	Wenn Sie auf das Modellschema klicken, wird das Eingabefeld ausgefüllt, in dem Sie den Parameterwert für die Post-Methode angeben können.

	![](./media/app-service-api-dotnet-get-started/post.png)

7. Ändern Sie die JSON-Daten im Eingabefeld des Parameters `contact`, sodass es wie im folgenden Beispiel aussieht, oder geben Sie Ihren eigenen Namen bzw. Ihre E-Mail-Adresse an:

		{
		  "CreatedBy": "",
		  "EmailAddress": "carson@contoso.com",
		  "Id": 4,
		  "Name": "Alexander Carson"
		} 

10. Klicken Sie auf **Ausprobieren**.

	Die ContactsList-API gibt eine HTTP 200-Meldung und einen Antworttext zurück, in dem bestätigt wird, was hinzugefügt wurde.

11. Klicken Sie auf **Abrufen > Ausprobieren**.

	Die Antwort der Get-Methode enthält jetzt den neuen Kontakt.

12. Probieren Sie auch die Methoden „Put“, „Delete“ und „Get by ID“ aus, und schließen Sie dann den Browser.

Swashbuckle funktioniert mit allen ASP.NET-Web-API-Projekten. Wenn Sie die Generierung von Swagger-Metadaten einem vorhandenen Projekt hinzufügen möchten, können Sie einfach das Swashbuckle-Paket installieren. Um ein neues Projekt zu erstellen, verwenden Sie die ASP.NET-Projektvorlage **Azure API-App**, die in der folgenden Abbildung dargestellt ist.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

Mit dieser Vorlage wird ein Web-API-Projekt mit Installation von Swashbuckle erstellt.

**Hinweis:** Swashbuckle generiert standardmäßig ggf. doppelte Swagger-Vorgangs-IDs für Ihre Controllermethoden. Dies geschieht, wenn Ihr Controller überladene HTTP-Methoden aufweist, wie z. B. `Get()` und `Get(id)`. Informationen zur Behandlung von Überladungen finden Sie unter [Anpassen von mit Swashbuckle generierten API-Definitionen](app-service-api-dotnet-swashbuckle-customize.md). Wenn Sie ein Web-API-Projekt in Visual Studio mithilfe der Azure-API-App-Vorlage erstellen, wird Code, der eindeutige Vorgangs-IDs automatisch generiert, der Datei *SwaggerConfig.cs* hinzugefügt.

## Erstellen einer API-App in Azure und Bereitstellen des Projekts „ContactsList.API“ für die API-App

In diesem Abschnitt verwenden Sie Azure-Tools, die in den Visual Studio-Assistenten **Web veröffentlichen** integriert sind, um eine neue API-App in Azure zu erstellen. Sie können dann das Projekt „ContactsList.API“ für die neue API-App bereitstellen und die API aufrufen, indem Sie die Swagger-Benutzeroberfläche erneut ausführen, und zwar dieses Mal während der Ausführung in der Cloud.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt „ContactsList.API“ und dann auf **Veröffentlichen**.

3.  Klicken Sie im Schritt **Profil** im Assistenten **Web veröffentlichen** auf **Microsoft Azure App Service**.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Melden Sie sich an Ihrem Azure-Konto an, falls Sie dies noch nicht getan haben, oder aktualisieren Sie Ihre Anmeldeinformationen, falls sie abgelaufen sind.

4. Wählen Sie im App Service-Dialogfeld das gewünschte Azure-**Abonnement** aus, und klicken Sie dann auf **Neu**.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. Klicken Sie im Dialogfeld **App Service erstellen** auf der Registerkarte **Hosting** auf **Typ ändern** und dann auf **API-App**.

	![](./media/app-service-api-dotnet-get-started/apptype.png)

	Wenn Sie den Typ in **API-App** ändern, werden dadurch nicht die Features bestimmt, die für die neue App verfügbar sind. Die URL der API-Definition (wird später in diesem Tutorial beschrieben), CORS-Unterstützung (wird im nächsten Tutorial beschrieben) und Authentifizierung (wird in den letzten drei Tutorials dieser Reihe beschrieben) sind für Web-Apps und mobile Apps sowie API-Apps verfügbar. Das Erstellen einer App als API-App hat lediglich folgende Auswirkungen:

	a. Im Azure-Portal wird das Symbol oder der Text des App-Typs in den Blatteinstellungen und Listen mit Apps angezeigt. Auf dem Blatt **Einstellungen** erscheint der API-Abschnitt in der Liste für eine API-App gegenüber anderen App-Typen außerdem früher.

	b. In Visual Studio mit Azure SDK für .NET 2.8.1 legt Visual Studio die URL für die API-Definition während der Erstellung einer neuen ASP.NET-API-App fest, aber nicht für andere App-Typen.

4. Geben Sie einen **API-App-Namen** ein, der in der Domäne *azurewebsites.net* eindeutig ist.

	Visual Studio schlägt einen eindeutigen Namen vor, indem an den Projektnamen eine Zeichenfolge mit Datum und Uhrzeit angefügt wird. Wenn Sie möchten, können Sie diesen Namen akzeptieren.

	Wenn Sie einen Namen eingeben, der von einer anderen Person bereits verwendet wurde, wird rechts ein rotes Ausrufungszeichen anstelle eines grünen Häkchens angezeigt. Sie müssen dann einen anderen Namen eingeben.

	Dieser Name wird von Azure als Präfix für die URL Ihrer Anwendung verwendet. Die vollständige URL besteht aus diesem Namen und dem Zusatz *.azurewebsites.net*. Für den Namen `ContactsListAPI` lautet die URL z. B. `contactslistapi.azurewebsites.net`.

6. Geben Sie in der Dropdownliste **Ressourcengruppe** „ContactsListGroup“ oder einen anderen Namen ein, den Sie bevorzugen.

	In diesem Feld können Sie eine vorhandene [Ressourcengruppe](../azure-preview-portal-using-resource-groups.md) auswählen oder eine neue Ressourcengruppe erstellen. Geben Sie hierfür einen Namen ein, der sich von einer vorhandenen Ressourcengruppe in Ihrem Abonnement unterscheidet.

	Für dieses Tutorial ist es besser, eine neue Ressourcengruppe zu erstellen. Es ist dann einfacher, alle Azure-Ressourcen, die Sie für das Tutorial erstellen, in einem Schritt zu löschen.

4. Klicken Sie neben der Dropdownliste **App Service-Plan** auf die Schaltfläche **Neu**.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	Weitere Informationen zu App Service-Plänen finden Sie unter [App Service-Pläne – Übersicht](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Geben Sie im Dialogfeld **App Service-Plan konfigurieren** den Namen „ContactsListPlan“ oder einen anderen Namen ein, den Sie bevorzugen.

5. Wählen Sie in der Dropdownliste **Standort** den Standort aus, der Ihnen am nächsten liegt.

	Mit dieser Einstellung wird das Azure-Rechenzentrum angegeben, in dem Ihre App ausgeführt wird. In diesem Lernprogramm können Sie ohne spürbaren Unterschied eine beliebige Region auswählen. Für eine Produktions-App sollte sich der Server aber so nah wie möglich bei den Clients befinden, mit denen darauf zugegriffen wird, um [Latenz](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090) zu minimieren.

5. Klicken Sie in der Dropdownliste **Größe** auf **Free**.

	Für dieses Tutorial bietet der Tarif „Free“ eine ausreichende Leistung.

6. Klicken Sie im Dialogfeld **App Service-Plan konfigurieren** auf **OK**.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. Klicken Sie im Dialogfeld **App Service erstellen** auf **Erstellen**.

	Visual Studio erstellt die API-App und ein Veröffentlichungsprofil, das alle erforderlichen Einstellungen für die neue API-App enthält. In den folgenden Schritten verwenden Sie das neue Veröffentlichungsprofil, um das Projekt bereitzustellen.
 
	**Hinweis**: Es gibt auch andere Wege zum Erstellen von API-Apps in Azure App Service. In Visual Studio sind beim Erstellen eines neuen Projekts die gleichen Dialogfelder verfügbar. Sie können API-Apps auch erstellen, indem Sie das Azure-Portal, [Azure-Cmdlets für Windows PowerShell](../powershell-install-configure.md) oder die [plattformübergreifende Befehlszeilenschnittstelle](../xplat-cli.md) verwenden.

8. Klicken Sie auf der Registerkarte **Verbindung** des Assistenten **Web veröffentlichen** auf **Veröffentlichen**.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio stellt das Projekt „ContactsList.API“ in der neuen API-App bereit und öffnet einen Browser mit der URL der API-App. Im Browser wird die Seite mit dem Hinweis angezeigt, dass die Erstellung erfolgreich war.

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Fügen Sie der URL in der Adresszeile des Browsers „swagger“ hinzu, und drücken Sie dann die EINGABETASTE. (Die URL lautet `http://{apiappname}.azurewebsites.net/swagger`.)

	Im Browser wird die gleiche Swagger-Benutzeroberfläche angezeigt, die Sie bereits kennen, aber nun wird sie in der Cloud ausgeführt. Wenn Sie die Get-Methode ausprobieren, sehen Sie, dass Sie wieder die drei standardmäßigen Kontakte erhalten. Die von Ihnen vorgenommenen Änderungen wurden in einer lokalen Datei gespeichert. Alle Änderungen, die Sie jetzt vornehmen, werden im Dateisystem der Azure-API-App gespeichert.

12. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).
 
14. Klicken Sie auf **Durchsuchen > API-Apps > {Ihre neue API-App}**.

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. Klicken Sie auf **Einstellungen**. Suchen Sie im Blatt **Einstellungen** den Abschnitt „API“, und klicken Sie auf **API-Definition**.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	Auf dem Blatt „API-Definition“ können Sie die URL angeben, über die Swagger 2.0-Metadaten im JSON-Format zurückgegeben werden. Beim Erstellen der API-App wird die URL der API-Definition in Visual Studio auf den Standardwert festgelegt, den Sie schon kennen. Dies ist die Basis-URL der API-App mit dem Zusatz `/swagger/docs/v1`.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Wenn Sie eine API-App auswählen, um Clientcode dafür zu generieren, ruft Visual Studio die Metadaten über diese URL ab.

### API-Definitions-URL in Azure-Ressourcen-Manager-Tools

Sie können auch die API-Definitions-URL einer API-App mithilfe von Azure-Ressourcen-Manager-Tools wie z. B. Azure PowerShell, Befehlszeilenschnittstelle (CLI) oder [Ressourcen-Explorer](https://resources.azure.com/) konfigurieren.

Legen Sie die `apiDefinition`-Eigenschaft über den Ressourcentyp `Microsoft.Web/sites/config` für die Ressource `<site name>/web` fest. Beispiel: Wechseln Sie im **Ressourcen-Explorer** zu **Abonnements > {Ihr Abonnement} > Ressourcengruppen > {Ihre Ressourcengruppe} > Anbieter > Microsoft.Web > Websites > {Ihre Website} > Konfigurieren > Web**. Hier wird die `apiDefinition`-Eigenschaft angezeigt:

		"apiDefinition": {
		  "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
		}

## <a id="codegen"></a> Nutzen in einem .NET-Client mit generiertem Clientcode

Einer der Vorteile der Integration von Swagger in Azure API-Apps ist die automatische Codegenerierung. Generierte Clientklassen vereinfachen das Schreiben von Code, mit dem eine API-App aufgerufen wird.

In diesem Abschnitt erfahren Sie, wie Sie eine API-App über eine ASP.NET MVC-Web-App nutzen. Sie führen den MVC-Client und die Web-API zuerst lokal aus und stellen den Client dann für eine neue Web-App in Azure App Service bereit und führen sie in der Cloud aus.

### Generieren von Clientcode

Sie können Clientcode für eine API-App mit Visual Studio oder über die Befehlszeile generieren. In diesem Tutorial verwenden Sie Visual Studio. Informationen zur Vorgehensweise mit der Befehlszeile finden Sie auf GitHub.com im Repository [Azure/autorest](https://github.com/azure/autorest) in der Infodatei.

Das Projekt ContactsList.MVC enthält bereits den generierten Clientcode. Sie löschen und generieren den Code aber neu, damit die standardmäßige Ziel-URL auf Ihre eigene API-App zurückgesetzt wird.

1. Löschen Sie im **Projektmappen-Explorer** von Visual Studio im Projekt „ContactsList.MVC“ den Ordner *ContactsList.API*.

	Dieser Ordner wurde mithilfe des Codegenerierungsprozesses erstellt, den Sie gleich durchlaufen werden.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. Klicken Sie mit der rechten Maustaste auf das Projekt „ContactsList.MVC“, und klicken Sie dann auf **Hinzufügen > REST-API-Client**.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Klicken Sie im Dialogfeld **REST-API-Client hinzufügen** auf **Aus Microsoft Azure-API-App herunterladen** und dann auf **Durchsuchen**.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Erweitern Sie im Dialogfeld **App Service** die Ressourcengruppe **ContactsListGroup**, und wählen Sie Ihre API-App aus. Klicken Sie anschließend auf **OK**.

	In diesem Dialogfeld haben Sie mehrere Möglichkeiten zum Organisieren von API-Apps in der Liste, falls die Liste für den Bildlauf zu lang wird. Außerdem können Sie eine Suchzeichenfolge eingeben, um API-Apps nach dem Namen zu filtern.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Wenn Sie die API-App nicht in der Liste sehen, besteht die Möglichkeit, dass Sie beim Erstellen der API-App versehentlich den Schritt ausgelassen haben, bei dem der Typ von „Web-App“ in „API-App“ geändert wird. In diesem Fall können Sie eine neue API-App erstellen, indem Sie die vorherigen Schritte wiederholen. Sie müssen einen anderen Namen für die API-App wählen, es sei denn, Sie besuchen Sie das Portal, um zuerst die Web-App zu löschen.

	Beachten Sie Folgendes: Wenn Sie zurück zum Dialogfeld **REST-API-Client hinzufügen** wechseln, sehen Sie, dass das Textfeld mit dem URL-Wert der API-Definition gefüllt wurde. Diesen Wert kennen Sie bereits aus dem Portal.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	Eine alternative Möglichkeit zum Abrufen von Metadaten für die Codegenerierung ist das direkte Eingeben von Code, anstatt das Dialogfeld „Durchsuchen“ zu verwenden. Wenn Sie die API beispielsweise für eine Web-App bereitgestellt haben und diese im Dialogfeld „Durchsuchen“ nicht angezeigt wird, können Sie die URL zum Zurückgeben von Swagger-Metadaten hier manuell eingeben.

	Eine andere Alternative zum Abrufen der Metadaten ist die Verwendung der Option **Vorhandene Swagger-Metadatendatei auswählen**. Wenn Sie vor der Bereitstellung für Azure beispielsweise Clientcode generieren möchten, können Sie die lokale Ausführung nutzen und die Swagger-JSON-Datei herunterladen und hier auswählen.

9. Klicken Sie im Dialogfeld **REST-API-Client hinzufügen** auf **OK**.

	Visual Studio erstellt einen Ordner, der nach der API-App benannt ist, und generiert Clientklassen.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. Öffnen Sie *Controllers\\ContactsController.cs*, um den Code anzuzeigen, mit dem die API mithilfe generierten Clients aufgerufen wird.

	Der folgende Codeausschnitt zeigt, wie Sie das Clientobjekt instanziieren und die Get-Methode aufrufen.

		private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	Mit diesem Code wird die lokale IIS Express-URL des API-Projekts an den Clientklassenkonstruktor übergeben, damit Sie das MVC-Webprojekt und das API-Projekt lokal ausführen können. Wenn Sie den Konstruktorparameter weglassen, ist der Standardendpunkt die URL, aus der Sie den Code generiert haben.

6. Ihre Clientklasse wird unter einem anderen Namen basierend auf dem Namen Ihrer API-App generiert. Ändern Sie diesen Code, damit der Typname mit den Daten übereinstimmt, die in Ihrem Projekt generiert wurden. Wenn Sie Ihre API-App „ContactsListAPIContoso“ genannt hätten, würde der Code wie im folgenden Beispiel aussehen:

		private ContactsListAPIContoso db = new ContactsListAPIContoso(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

7. Erstellen Sie die Projektmappe.

Der Controller und die Ansichten des MVC-Projekts ähneln den Controllern und Ansichten, die als Entity Framework-Gerüst erstellt wurden, weil diese Vorgehensweise für die Erstellung verwendet wurde. Es wurde ein Entity Framework-Datenmodell erstellt, und dann wurden kleinere Änderungen vorgenommen, sodass anstelle eines Entity Framework-Datenbankkontexts der REST-API-Client verwendet werden kann.

### Lokales Ausführen

1. Legen Sie die Projekte „ContactsList.API“ und „ContactsList.MVC“ als Startprojekte fest. „ContactsList.API“ sollte dabei vor „ContactsList.MVC“ gestartet werden. (Klicken Sie mit der rechten Maustaste auf die Projektmappe, klicken Sie auf **Eigenschaften** und **Mehrere Startprojekte**, und legen Sie für jedes Projekt **Start** fest. Verwenden Sie die Symbole mit dem Pfeil nach oben bzw. unten, um sicherzustellen, dass „ContactsList.API“ in der Liste an erster Stelle steht.) 

2. Drücken Sie F5, um die Projekte zu starten.

	In einem Browser wird die Seite „403“ für die API angezeigt, und im anderen Browser wird die Startseite der MVC-App angezeigt.

3. Klicken Sie auf der Menüleiste des Browsers auf **Kontakte**, woraufhin die Startseite der MVC-App angezeigt wird.

	In der MVC-Benutzeroberfläche werden die lokal gespeicherten Kontakte angezeigt. Sie können über die UI Kontakte hinzufügen und löschen.

	![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

### Erstellen einer Web-App in Azure und Bereitstellen des Projekts „ContactsList.MVC“ für die Web-App

In diesem Abschnitt verwenden Sie die gleiche Methode, um eine Web-App zu erstellen, die Sie oben zum Erstellen einer API-App genutzt haben. Sie verwenden auch die gleiche Methode, um ein Webprojekt für die Azure-Web-App bereitzustellen.

#### Ändern des MVC-Projekts zum Verweisen auf die Azure-API-App 

Ändern Sie vor der Bereitstellung in Azure den API-Endpunkt im MVC-Projekt so, dass mit dem Code bei der Bereitstellung die von Ihnen erstellte Azure-API-App aufgerufen wird, und nicht localhost.

1. Öffnen Sie im Projekt „ContactsList.MVC“ die Datei *Controllers\\ContactsController.cs*.

2. Kommentieren Sie die Zeile aus, mit der die API-Basis-URL auf die localhost-URL festgelegt wird, und heben Sie die Auskommentierung für die Zeile auf, die keinen Konstruktorparameter aufweist. Der Code sieht jetzt wie im folgenden Beispiel aus, aber mit folgender Ausnahme: Beide Zeilen weisen den Klassennamen auf, mit dem der Name Ihrer API-App widergespiegelt wird.

		private ContactsListAPI db = new ContactsListAPI();
		//private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));

	Die standardmäßige Ziel-URL ist Ihre Azure-API-App, weil Sie den Code darüber generiert haben. Falls Sie eine andere Methode zum Generieren des Codes verwendet haben, müssen Sie die URL der Azure-API-App unter Umständen genauso wie die lokale URL angeben.

#### Erstellen einer Web-App zum Hosten der MVC-Website

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt „ContactsList.MVC“ und dann auf **Veröffentlichen**.

2. Klicken Sie im Assistenten **Web veröffentlichen** auf die Registerkarte **Profil**.

3.  Klicken Sie auf der Registerkarte **Profil** des Assistenten **Web veröffentlichen** auf **Microsoft Azure App Service**.

5. Klicken Sie im Dialogfeld **App Service** auf **Neu**.

3. Klicken Sie im Dialogfeld **App Service erstellen** auf der Registerkarte **Hosting** auf **Typ ändern**, und stellen Sie sicher, dass der Typ **Web-App** lautet.

4. Geben Sie einen **Web-App-Namen** ein, der in der Domäne *azurewebsites.net* eindeutig ist.

5. Wählen Sie das gewünschte Azure-**Abonnement** aus.

6. Wählen Sie in der Dropdownliste **Ressourcengruppe** die Ressourcengruppe aus, die Sie weiter oben erstellt haben.

4. Wählen Sie in der Dropdownliste **App Service-Plan** den Plan aus, den Sie bereits erstellt haben.

7. Klicken Sie auf **Erstellen**.

	Visual Studio erstellt die Web-App und das dazugehörige Veröffentlichungsprofil und zeigt den Schritt **Verbindung** des Assistenten **Web veröffentlichen** an.

### Bereitstellen des Projekts „ContactsList.Web“ für die neue Web-App

3.  Klicken Sie im Schritt **Verbindung** des Assistenten **Web veröffentlichen** auf **Veröffentlichen**.

	Visual Studio stellt das Projekt „ContactsList.MVC“ in der neuen Web-App bereit und öffnet einen Browser mit der URL der Web-App. Es wird die gleiche MVC-Benutzeroberfläche angezeigt, die Sie von der lokalen Ausführung kennen. Jetzt werden aber die Kontakte angezeigt, die im Dateisystem der Azure-API-App gespeichert sind.

	![](./media/app-service-api-dotnet-get-started/codegencontacts.png)

## Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie API-Apps erstellen, Code dafür bereitstellen, Clientcode generieren und die Apps dann über .NET-Clients nutzen. Im nächsten Tutorial der Reihe „Erste Schritte mit API-Apps“ wird gezeigt, wie Sie [API-Apps über JavaScript-Clients mit CORS nutzen](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_0114_2016-->