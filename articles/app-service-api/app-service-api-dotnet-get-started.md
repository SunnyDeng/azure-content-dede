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
	ms.date="02/05/2016"
	ms.author="tdykstra"/>

# Erste Schritte mit API-Apps und ASP.NET in Azure App Service

[AZURE.INCLUDE [Auswahl](../../includes/app-service-api-get-started-selector.md)]

## Übersicht

Dies ist das erste Tutorial einer Reihe von Tutorials zur Verwendung der Features von Azure App Service, die als Unterstützung beim Entwickeln und Hosten von APIs dienen:

* Integrierte Unterstützung für API-Metadaten
* CORS-Unterstützung
* Unterstützung für Authentifizierung und Autorisierung

Sie stellen eine Beispielanwendung für zwei [API-Apps](app-service-api-apps-why-best-platform.md) und eine Web-App in Azure App Service bereit. Die Beispielanwendung ist eine Aufgabenliste, die über ein AngularJS-SPA-Front-End (Single-Page-Anwendung), eine ASP.NET-Web-API für die mittlere Ebene und eine ASP.NET-Web-API für die Datenebene verfügt. Dies ist in der Abbildung dargestellt.

![](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Hier ist ein Screenshot des SPA-Front-Ends angegeben.

![](./media/app-service-api-dotnet-get-started/todospa.png)

Wenn Sie dieses Tutorial abgeschlossen haben, sind die beiden Web-APIs für App Service-API-Apps einsatzbereit. Nach Abschluss des folgenden Tutorials wird die gesamte Anwendung in der Cloud ausgeführt, und die SPA ist in eine App Service-Web-App eingebettet. In den nachfolgenden Tutorials fügen Sie dann die Authentifizierung und Autorisierung hinzu.

## Sie lernen Folgendes

In diesem Lernprogramm lernen Sie Folgendes:

* Verwenden von API-Apps und Web-Apps in Azure App Service mit integrierten Tools von Visual Studio 2015
* Automatisieren der API-Ermittlung mit dem Swashbuckle-NuGet-Paket zum dynamischen Generieren der JSON-Daten für die Swagger-API
* Automatisches Generieren von Clientcode zum Nutzen einer API-App über einen .NET-Client
* Verwenden des Azure-Portals zum Konfigurieren des Endpunkts für die Metadaten der API-App

## Voraussetzungen

[AZURE.INCLUDE [Voraussetzungen](../../includes/app-service-api-dotnet-get-started-prereqs.md)]

## Herunterladen der Beispielanwendung 

1. Laden Sie das Repository [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) herunter.

	Sie können auf die Schaltfläche **Download ZIP** klicken oder das Repository auf Ihrem lokalen Computer klonen.

2. Öffnen Sie die Projektmappe ToDoList in Visual Studio 2015 oder 2013.

	Die Visual Studio-Projektmappe ist eine Beispielanwendung, bei der einfache Aufgabenelemente eingesetzt werden, die eine Beschreibung und einen Besitzer umfassen.

		public class ToDoItem 
		{ 
		    public int ID { get; set; } 
		    public string Description { get; set; } 
		    public string Owner { get; set; } 
		} 
 
	Die Projektmappe enthält drei Projekte:

	![](./media/app-service-api-dotnet-get-started/projectsinse.png)

	* **ToDoListAngular** – Front-End: AngularJS-SPA zum Aufrufen der mittleren Ebene 

	* **ToDoListAPI** – Mittlere Ebene: ASP.NET-Web-API-Projekt zum Aufrufen der Datenebene und Durchführen von CRUD-Operationen für Aufgabenelemente

	* **ToDoListDataAPI** – Datenebene: ASP.NET-Web-API-Projekt zum Durchführen von CRUD-Operationen für Aufgabenelemente. Aufgabenelemente werden im Arbeitsspeicher gespeichert. Dies bedeutet, dass jeweils alle Änderungen verloren gehen, wenn die Anwendung neu gestartet wird.

	Von der mittleren Ebene wird die Benutzer-ID im Feld `Owner` bereitgestellt, wenn die Datenebene aufgerufen wird. Im Code, den Sie herunterladen, lautet die Benutzer-ID immer „*“. Beim Hinzufügen der Authentifizierung in späteren Tutorials stellt die mittlere Ebene die tatsächliche Benutzer-ID für die Datenebene bereit.

2. Erstellen Sie die Projektmappe, um die NuGet-Pakete wiederherzustellen.

### Optional: Lokales Ausführen der Anwendung

In diesem Abschnitt stellen Sie sicher, dass Sie den Client lokal ausführen und die API aufrufen können, während sie ebenfalls lokal ausgeführt wird.

**Hinweis:** Diese Anweisungen gelten für Internet Explorer und Edge-Browser, da diese Browser ursprungsübergreifende Aufrufe von und an `http://localhost`-URLs ermöglichen. Wenn Sie Chrome verwenden, starten Sie den Browser mit dem Switch `--disable-web-security`. Wenn Sie Firefox verwenden, überspringen Sie diesen Abschnitt.

1. Legen Sie alle drei Projekte als Startprojekte fest, und zwar in der Reihenfolge ToDoListDataAPI, ToDoListAPI und dann ToDoListAngular. (Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Eigenschaften**, wählen Sie **Mehrere Startprojekte**, bringen Sie die Projekte in die richtige Reihenfolge, und legen Sie **Aktion** jeweils auf **Start** fest.)  

2. Drücken Sie F5, um die Projekte zu starten.

	Es werden drei Browserfenster geöffnet. In zwei Browserfenstern werden Seiten mit dem Fehler HTTP 403 angezeigt (Verzeichnissuche unzulässig). Dies ist für Web-API-Projekte normal. Im dritten Fenster wird die AngularJS-Benutzeroberfläche angezeigt.

3. Klicken Sie im Browserfenster, in dem die AngularJS-Benutzeroberfläche angezeigt wird, auf die Registerkarte **Aufgabenliste**.

	Auf der Benutzeroberfläche werden zwei standardmäßige Aufgabenelemente angezeigt.

	![](./media/app-service-api-dotnet-get-started/todospa.png)

4. Fügen Sie Aufgabenelemente hinzu, und bearbeiten und löschen Sie sie, um herauszufinden, wie die Anwendung funktioniert.

	Alle vorgenommenen Änderungen werden im Arbeitsspeicher gespeichert und gehen verloren, wenn Sie die Anwendung neu starten.

3. Schließen Sie die Browserfenster.

## Verwenden von Swagger-Metadaten und -Benutzeroberfläche

In Azure App Service ist Unterstützung für [Swagger 2.0](http://swagger.io/)-API-Metadaten integriert. Für jede API-App wird ein URL-Endpunkt angegeben, der Metadaten für die API im Swagger-JSON-Format zurückgibt. Die Metadaten, die von diesem Endpunkt zurückgegeben werden, können zum Generieren von Clientcode verwendet werden.

Für ein ASP.NET-Web-API-Projekt können Swagger-Metadaten dynamisch erstellt werden, indem das NuGet-Paket [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) verwendet wird. Das NuGet-Paket „Swashbuckle“ ist in den Projekten ToDoListDataAPI und ToDoListAPI, die Sie heruntergeladen haben, bereits installiert.

In diesem Abschnitt des Tutorials geht es um die generierten Swagger 2.0-Metadaten, und dann probieren Sie eine Testbenutzeroberfläche aus, die auf den Swagger-Metadaten basiert.

2. Legen Sie das Projekt ToDoListDataAPI als Startprojekt fest. 
 
4. Drücken Sie F5, um das Projekt im Debugmodus auszuführen.

	Der Browser wird geöffnet und zeigt die Seite mit dem Fehler „HTTP 403“ an.

12. Fügen Sie in der Adressleiste des Browsers „`swagger/docs/v1`“ am Ende der Zeile hinzu, und drücken Sie die EINGABETASTE. (Die URL lautet `http://localhost:45914/swagger/docs/v1`.)

	Dies ist die Standard-URL, die von Swashbuckle zum Zurückgeben von Swagger 2.0-JSON-Metadaten für die API verwendet wird.

	Wenn Sie Internet Explorer verwenden, fordert der Browser Sie zum Herunterladen der Datei *v1.json* auf.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Wenn Sie Chrome, Firefox oder Edge verwenden, zeigt der Browser die JSON-Daten im Browserfenster an.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	Im folgenden Beispiel ist der erste Abschnitt der Swagger-Metadaten für die API mit der Definition für die Get-Methode zu sehen. Diese Metadaten stellen die Grundlage der Swagger-Benutzeroberfläche dar, die Sie in den folgenden Schritten verwenden. Sie nutzen sie auch in einem späteren Abschnitt des Tutorials, um Clientcode automatisch zu generieren.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ToDoListDataAPI"
		  },
		  "host": "localhost:45914",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/ToDoList": {
		      "get": {
		        "tags": [ "ToDoList" ],
		        "operationId": "ToDoList_GetByOwner",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "parameters": [
		          {
		            "name": "owner",
		            "in": "query",
		            "required": true,
		            "type": "string"
		          }
		        ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/ToDoItem" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. Schließen Sie den Browser.

3. Öffnen Sie im Projekt ToDoListDataAPI im **Projektmappen-Explorer** die Datei *App\_Start\\SwaggerConfig.cs*, und scrollen Sie dann nach unten zum folgenden Code. Heben Sie die Auskommentierung für diesen Code auf.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	Die Datei *SwaggerConfig.cs* wird erstellt, wenn Sie das Swashbuckle-Paket in einem Projekt installieren. Die Datei bietet unterschiedliche Möglichkeiten zur Konfiguration von Swashbuckle.

	Mit dem Code, für den Sie die Auskommentierung aufgehoben haben, wird die Swagger-Benutzeroberfläche aktiviert, die Sie in den folgenden Schritten verwenden. Wenn Sie ein Web-API-Projekt erstellen, indem Sie die API-App-Projektvorlage verwenden, wird dieser Code als Sicherheitsmaßnahme standardmäßig auskommentiert.

5. Führen Sie das Projekt erneut aus.

3. Fügen Sie in der Adressleiste des Browsers „`swagger`“ am Ende der Zeile hinzu, und drücken Sie die EINGABETASTE. (Die URL lautet `http://localhost:45914/swagger`.)

4. Klicken Sie auf der Seite der Swagger-Benutzeroberfläche auf **ToDoList**, um die verfügbaren Methoden anzuzeigen.

	![](./media/app-service-api-dotnet-get-started/methods.png)

5. Klicken Sie auf **Get**.

6. Geben Sie ein Sternchen als Wert für den Parameter `owner` ein, und klicken Sie dann auf **Ausprobieren**.

	![](./media/app-service-api-dotnet-get-started/gettryitout1.png)

	Auf der Swagger-Benutzeroberfläche wird die ToDoList Get-Methode aufgerufen, und der Antwortcode und die JSON-Ergebnisse werden angezeigt.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Klicken Sie auf **Post** und dann in das Feld unter **Modellschema**.

	Wenn Sie auf das Modellschema klicken, wird das Eingabefeld ausgefüllt, in dem Sie den Parameterwert für die Post-Methode angeben können. (Falls dies in Internet Explorer nicht funktioniert, sollten Sie einen anderen Browser verwenden oder den Parameterwert im nächsten Schritt manuell eingeben.)

	![](./media/app-service-api-dotnet-get-started/post.png)

7. Ändern Sie den JSON-Code im Eingabefeld des Parameters `contact` so, dass er wie im folgenden Beispiel aussieht, oder geben Sie Ihren eigenen Beschreibungstext ein:

		{
		  "ID": 2,
		  "Description": "buy the dog a toy",
		  "Owner": "*"
		}

10. Klicken Sie auf **Ausprobieren**.

	Die ToDoList-API gibt den Antwortcode „HTTP 204“ zurück, der für eine erfolgreiche Durchführung steht.

11. Klicken Sie auf **Abrufen > Ausprobieren**.

	Die Antwort der Get-Methode enthält jetzt das neue Element.

12. Probieren Sie auch die Methoden „Put“, „Delete“ und „Get by ID“ aus.

14. Schließen Sie den Browser.

Swashbuckle funktioniert mit allen ASP.NET-Web-API-Projekten. Wenn Sie die Generierung von Swagger-Metadaten einem vorhandenen Projekt hinzufügen möchten, können Sie einfach das Swashbuckle-Paket installieren.

**Hinweis:** Swagger-Metadaten enthalten für jeden API-Vorgang eine eindeutige ID. Swashbuckle generiert standardmäßig ggf. doppelte Swagger-Vorgangs-IDs für Ihre Web-API-Controllermethoden. Dies geschieht, wenn Ihr Controller überladene HTTP-Methoden aufweist, z. B. `Get()` und `Get(id)`. Informationen zur Behandlung von Überladungen finden Sie unter [Anpassen von mit Swashbuckle generierten API-Definitionen](app-service-api-dotnet-swashbuckle-customize.md). Wenn Sie ein Web-API-Projekt in Visual Studio mithilfe der Azure-API-App-Vorlage erstellen, wird Code, der eindeutige Vorgangs-IDs automatisch generiert, der Datei *SwaggerConfig.cs* hinzugefügt.

## Erstellen einer API-App in Azure und Bereitstellen des Projekts ToDoListAPI für die API-App

In diesem Abschnitt verwenden Sie Azure-Tools, die in den Visual Studio-Assistenten **Web veröffentlichen** integriert sind, um eine neue API-App in Azure zu erstellen. Sie können dann das Projekt ToDoListDataAPI für die neue API-App bereitstellen und die API aufrufen, indem Sie die Swagger-Benutzeroberfläche erneut ausführen, und zwar dieses Mal während der Ausführung in der Cloud.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt ToDoListDataAPI, und klicken Sie dann auf **Veröffentlichen**.

	![](./media/app-service-api-dotnet-get-started/pubinmenu.png)

3.  Klicken Sie im Schritt **Profil** im Assistenten **Web veröffentlichen** auf **Microsoft Azure App Service**.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Melden Sie sich an Ihrem Azure-Konto an, falls Sie dies noch nicht getan haben, oder aktualisieren Sie Ihre Anmeldeinformationen, falls sie abgelaufen sind.

4. Wählen Sie im App Service-Dialogfeld das gewünschte Azure-**Abonnement** aus, und klicken Sie dann auf **Neu**.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

	Die Registerkarte **Hosten** des Dialogfelds **App Service erstellen** wird angezeigt.

	Da Sie ein Web-API-Projekt bereitstellen, für das Swashbuckle installiert wurde, nimmt Visual Studio an, dass Sie eine API-App erstellen möchten. Dies wird durch den Titel **API-App-Name** und durch die Tatsache angegeben, dass die Dropdownliste **Typ ändern** auf **API-App** festgelegt ist.

	![](./media/app-service-api-dotnet-get-started/apptype.png)

	Mit dem App-Typ werden nicht die Features bestimmt, die für die neue API-App, Web-App oder mobile App verfügbar sind. Alle API-App-Features, die in diesen Tutorials veranschaulicht werden, sind für alle drei Typen verfügbar. Die einzigen Unterschiede sind das im Azure-Portal angezeigte Symbol und der Text für den App-Typ sowie die Reihenfolge, in der Features auf einigen Seiten im Portal aufgeführt werden. Das Azure-Portal wird später in diesem Tutorial noch vorgestellt. Es ist eine Webschnittstelle zum Verwalten von Azure-Ressourcen.

4. Geben Sie einen **API-App-Namen** ein, der in der Domäne *azurewebsites.net* eindeutig ist, z. B. ToDoListDataAPI mit einer angehängten Zahl.

	Visual Studio schlägt einen eindeutigen Namen vor, indem an den Projektnamen eine Zeichenfolge mit Datum und Uhrzeit angefügt wird. Wenn Sie möchten, können Sie diesen Namen akzeptieren.

	Wenn Sie einen Namen eingeben, der von einer anderen Person bereits verwendet wurde, wird rechts ein rotes Ausrufungszeichen anstelle eines grünen Häkchens angezeigt. Sie müssen dann einen anderen Namen eingeben.

	Dieser Name wird von Azure als Präfix für die URL Ihrer Anwendung verwendet. Die vollständige URL besteht aus diesem Namen und dem Zusatz *.azurewebsites.net*. Für den Namen `ToDoListDataAPI` lautet die URL z. B. `todolistdataapi.azurewebsites.net`.

6. Klicken Sie in der Dropdownliste **Ressourcengruppe** auf **Neu**, und geben Sie dann „ToDoListGroup“ oder einen anderen Namen ein, den Sie bevorzugen.

	Eine Ressourcengruppe ist eine Sammlung mit Azure-Ressourcen, z. B. API-Apps, Datenbanken, VMs usw. Für dieses Tutorial ist es besser, eine neue Ressourcengruppe zu erstellen. Es ist dann einfacher, alle Azure-Ressourcen, die Sie für das Tutorial erstellen, in einem Schritt zu löschen.

	In diesem Feld können Sie eine vorhandene [Ressourcengruppe](../azure-preview-portal-using-resource-groups.md) auswählen oder eine neue Ressourcengruppe erstellen. Geben Sie hierfür einen Namen ein, der sich von einer vorhandenen Ressourcengruppe in Ihrem Abonnement unterscheidet.

4. Klicken Sie neben der Dropdownliste **App Service-Plan** auf die Schaltfläche **Neu**.

	Der Screenshot enthält Beispielwerte für die Optionen **API-App-Name**, **Abonnement** und **Ressourcengruppe**. Ihre Werte werden sich von diesen Werten unterscheiden.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	In den folgenden Schritten erstellen Sie einen App Service-Plan für die neue Ressourcengruppe. Mit einem App Service-Plan werden die Computeressourcen angegeben, auf denen Ihre API-App ausgeführt wird. Wenn Sie beispielsweise den Free-Tarif wählen, wird Ihre API-App auf freigegebenen VMs ausgeführt. Für einige kostenpflichtige Tarife wird sie dagegen auf dedizierten VMs ausgeführt. Weitere Informationen zu App Service-Plänen finden Sie unter [App Service-Pläne – Übersicht](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Geben Sie im Dialogfeld **App Service-Plan konfigurieren** den Namen „ToDoListPlan“ oder einen anderen Namen ein, den Sie bevorzugen.

5. Wählen Sie in der Dropdownliste **Standort** den Standort aus, der Ihnen am nächsten liegt.

	Mit dieser Einstellung wird das Azure-Rechenzentrum angegeben, in dem Ihre App ausgeführt wird. In diesem Lernprogramm können Sie ohne spürbaren Unterschied eine beliebige Region auswählen. Für eine Produktions-App sollte sich der Server aber so nah wie möglich bei den Clients befinden, mit denen darauf zugegriffen wird, um [Latenz](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090) zu minimieren.

5. Klicken Sie in der Dropdownliste **Größe** auf **Free**.

	Für dieses Tutorial bietet der Tarif „Free“ eine ausreichende Leistung.

6. Klicken Sie im Dialogfeld **App Service-Plan konfigurieren** auf **OK**.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. Klicken Sie im Dialogfeld **App Service erstellen** auf **Erstellen**.

	![](./media/app-service-api-dotnet-get-started/clickcreate.png)

	Die API-App wird in Visual Studio erstellt.

	**Hinweis**: Es gibt auch andere Wege zum Erstellen von API-Apps in Azure App Service. Wenn Sie in Visual Studio ein neues Projekt erstellen, können Sie Azure-Ressourcen dafür beispielsweise genauso wie eben für ein vorhandenes Projekt erstellen. Sie können API-Apps auch erstellen, indem Sie das [Azure-Portal](https://portal.azure.com/), [Azure-Cmdlets für Windows PowerShell](../powershell-install-configure.md) oder die [plattformübergreifende Befehlszeilenschnittstelle](../xplat-cli.md) verwenden.

	Wenn Visual Studio die Erstellung der API-App abgeschlossen hat, wird ein Veröffentlichungsprofil erstellt, das alle erforderlichen Einstellungen für die neue API-App enthält. In den folgenden Schritten verwenden Sie das neue Veröffentlichungsprofil, um das Projekt bereitzustellen.

8. Klicken Sie auf der Registerkarte **Verbindung** des Assistenten **Web veröffentlichen** auf **Weiter**.

	Sie können stattdessen auch gleich auf **Veröffentlichen** klicken, um das Projekt sofort für die neue API-App bereitzustellen. In diesem Tutorial verwenden Sie aber die anderen Registerkarten dieses Dialogfelds, um zu erfahren, welche Optionen darauf enthalten sind.

	![](./media/app-service-api-dotnet-get-started/connnext.png)

	Die nächste Registerkarte ist die Registerkarte **Einstellungen**. Hier können Sie die Buildkonfiguration ändern, um einen Debugbuild für das [Remotedebuggen](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) bereitzustellen. Die Registerkarte enthält außerdem mehrere **Dateiveröffentlichungsoptionen**:

	* Nicht übereinstimmende Dateien im Ziel entfernen
	* Während der Veröffentlichung vorkompilieren
	* Dateien aus dem App\_Data-Ordner ausschließen

	Für dieses Tutorial benötigen Sie diese Optionen nicht. Ausführliche Beschreibungen ihrer Funktionsweise finden Sie unter [Gewusst wie: Bereitstellen eines Webanwendungsprojekts mit der One-Click-Veröffentlichung in Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

14. Klicken Sie auf **Weiter**.

	![](./media/app-service-api-dotnet-get-started/settingsnext.png)

	Auf der Registerkarte **Vorschau** können Sie sehen, welche Dateien aus Ihrem Projekt in die API-App kopiert werden. Wenn Sie ein Projekt für eine API-App bereitstellen, für die Sie bereits eine Bereitstellung durchgeführt haben, werden nur geänderte Dateien kopiert. Wenn Sie eine Liste mit den kopierten Dateien anzeigen möchten, können Sie auf die Schaltfläche **Vorschau starten** klicken.

15. Klicken Sie auf **Veröffentlichen**.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio stellt das Projekt ToDoListDataAPI für die neue API-App bereit. Im Fenster **Ausgabe** wird die erfolgreiche Bereitstellung protokolliert, und in einem Browserfenster mit der URL der API-App wird eine Seite mit dem Hinweis zur erfolgreichen Erstellung angezeigt.

	![](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Fügen Sie der URL in der Adresszeile des Browsers „swagger“ hinzu, und drücken Sie dann die EINGABETASTE. (Die URL lautet `http://{apiappname}.azurewebsites.net/swagger`.)

	Im Browser wird die gleiche Swagger-Benutzeroberfläche angezeigt, die Sie bereits kennen, aber nun wird sie in der Cloud ausgeführt. Wenn Sie die Get-Methode ausprobieren, sehen Sie, dass wieder die beiden standardmäßigen Aufgabenelemente angezeigt werden. Dies liegt daran, dass die von Ihnen vorgenommenen Änderungen im Arbeitsspeicher des lokalen Computers gespeichert wurden.

12. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

	Das Azure-Portal ist eine Webschnittstelle zum Verwalten von Azure-Ressourcen, z. B. API-Apps.
 
14. Klicken Sie auf **Durchsuchen > App Services**.

	![](./media/app-service-api-dotnet-get-started/browseas.png)

15. Suchen Sie im Blatt **App Services** nach Ihrer neuen API-App, und klicken Sie darauf. (Im Azure-Portal werden Fenster, die auf der rechten Seite geöffnet werden, als *Blätter* bezeichnet.)

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

	Es werden zwei Blätter geöffnet: ein Blatt mit einer Übersicht zur API-App, und ein Blatt mit einer langen Liste mit Einstellungen, die Sie anzeigen und ändern können.

16. Suchen Sie im Blatt **Einstellungen** nach dem Abschnitt **API**, und klicken Sie auf **API-Definition**.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	Auf dem Blatt **API-Definition** können Sie die URL angeben, über die Swagger 2.0-Metadaten im JSON-Format zurückgegeben werden. Beim Erstellen der API-App wird die URL der API-Definition in Visual Studio auf den Standardwert für von Swashbuckle generierte Metadaten festgelegt, den Sie schon kennen. Dies ist die Basis-URL der API-App mit dem Zusatz `/swagger/docs/v1`.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Wenn Sie eine API-App auswählen, um Clientcode dafür zu generieren, ruft Visual Studio die Metadaten über diese URL ab.

## <a id="codegen"></a> Nutzen der API-App mit generiertem Clientcode

Einer der Vorteile der Integration von Swagger in Azure API-Apps ist die automatische Codegenerierung. Generierte Clientklassen erleichtern es, Code zu schreiben, der eine API-App aufruft.

In diesem Abschnitt erfahren Sie, wie Sie eine API-App per ASP.NET-Web-API-Code nutzen.

### Generieren von Clientcode

Sie können Clientcode für eine API-App mit Visual Studio oder über die Befehlszeile generieren. In diesem Tutorial verwenden Sie Visual Studio. Informationen zur Vorgehensweise mit der Befehlszeile finden Sie auf GitHub.com im Repository [Azure/autorest](https://github.com/azure/autorest) in der Infodatei.

Das Projekt ToDoListAPI verfügt bereits über den generierten Clientcode, aber wir werden ihn löschen und neu generieren, damit Sie die Vorgehensweise erlernen.

1. Löschen Sie im **Projektmappen-Explorer** von Visual Studio im Projekt „ToDoListAPI“ den Ordner *ToDoListDataAPI*.

	Dieser Ordner wurde mithilfe des Codegenerierungsprozesses erstellt, den Sie gleich durchlaufen werden.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. Klicken Sie mit der rechten Maustaste auf das Projekt „ToDoListAPI“, und klicken Sie dann auf **Hinzufügen > REST-API-Client**.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Klicken Sie im Dialogfeld **REST-API-Client hinzufügen** auf **Swagger-URL** und dann auf **Azure-Asset auswählen**.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Erweitern Sie im Dialogfeld **App Service** die für dieses Tutorial verwendete Ressourcengruppe, und wählen Sie Ihre API-App aus. Klicken Sie anschließend auf **OK**.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	In diesem Dialogfeld haben Sie mehrere Möglichkeiten zum Organisieren von API-Apps in der Liste, falls die Liste für den Bildlauf zu lang wird. Außerdem können Sie eine Suchzeichenfolge eingeben, um API-Apps nach dem Namen zu filtern.

	Beachten Sie Folgendes: Wenn Sie zurück zum Dialogfeld **REST-API-Client hinzufügen** wechseln, sehen Sie, dass das Textfeld mit dem URL-Wert der API-Definition gefüllt wurde. Diesen Wert kennen Sie bereits aus dem Portal.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	Eine alternative Möglichkeit zum Abrufen von Metadaten für die Codegenerierung ist das direkte Eingeben von Code, anstatt das Dialogfeld „Durchsuchen“ zu verwenden. Eine andere Alternative ist die Verwendung der Option **Vorhandene Swagger-Metadatendatei auswählen**. Wenn Sie beispielsweise vor der Bereitstellung für Azure Clientcode generieren möchten, können Sie das Web-API-Projekt lokal ausführen. Navigieren Sie zu der URL, unter der die Swagger-JSON-Datei bereitgestellt wird, speichern Sie die Datei, und wählen Sie sie hier aus.

9. Klicken Sie im Dialogfeld **REST-API-Client hinzufügen** auf **OK**.

	Visual Studio erstellt einen Ordner, der nach der API-App benannt ist, und generiert Clientklassen.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. Öffnen Sie im Projekt ToDoListAPI die Datei *Controllers\\ToDoListController.cs*, um den Code zum Aufrufen der API mit dem generierten Client anzuzeigen.

	Der folgende Codeausschnitt zeigt, wie mit dem Code das Clientobjekt instanziiert und die Get-Methode aufgerufen wird.

		private ToDoListDataAPI db = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	Der Konstruktorparameter ruft die Endpunkt-URL aus der `toDoListDataAPIURL`-App-Einstellung ab. In der Datei „Web.config“ ist dieser Wert auf die lokale IIS Express-URL des API-Projekts festgelegt, damit Sie die Anwendung lokal ausführen können. Wenn Sie den Konstruktorparameter weglassen, ist der Standardendpunkt die URL, aus der Sie den Code generiert haben.

6. Ihre Clientklasse wird unter einem anderen Namen basierend auf dem Namen Ihrer API-App generiert. Ändern Sie den Code in *Controllers\\ToDoListController.cs*, damit der Typname mit den Daten übereinstimmt, die in Ihrem Projekt generiert wurden. Wenn Sie Ihre API-App beispielsweise ToDoListDataAPI0121 nennen, sieht der Code wie folgt aus:

		private ToDoListDataAPI0121 db = new ToDoListDataAPI0121(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

### Erstellen einer API-App zum Hosten der mittleren Ebene

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt ToDoListAPI (nicht ToDoListDataAPI), und klicken Sie dann auf **Veröffentlichen**.

3.  Klicken Sie auf der Registerkarte **Profil** des Assistenten **Web veröffentlichen** auf **Microsoft Azure App Service**.

5. Klicken Sie im Dialogfeld **App Service** auf **Neu**.

3. Geben Sie im Dialogfeld **App Service erstellen** auf der Registerkarte **Hosten** einen **API-App-Namen** ein, der in der Domäne *azurewebsites.net* eindeutig ist.

5. Wählen Sie das gewünschte Azure-**Abonnement** aus.

6. Wählen Sie in der Dropdownliste **Ressourcengruppe** die Ressourcengruppe aus, die Sie weiter oben erstellt haben.

4. Wählen Sie in der Dropdownliste **App Service-Plan** den Plan aus, den Sie bereits erstellt haben. Dieser Wert ist der Standardwert.

7. Klicken Sie auf **Erstellen**.

	Visual Studio erstellt die API-App und das dazugehörige Veröffentlichungsprofil und zeigt den Schritt **Verbindung** des Assistenten **Web veröffentlichen** an.

3.  Klicken Sie im Schritt **Verbindung** des Assistenten **Web veröffentlichen** auf **Veröffentlichen**.

	Visual Studio stellt das Projekt ToDoListAPI in der neuen API-App bereit und öffnet einen Browser mit der URL der API-App. Die Seite mit dem Hinweis, dass die Erstellung erfolgreich war, wird angezeigt.

### Festlegen der API-App-URL für die Datenebene in der API-App für die mittlere Ebene

Wenn Sie jetzt die API-App der mittleren Ebene aufrufen, würde versucht werden, die Datenebene mit der localhost-URL aufzurufen, die noch in der Datei „Web.config“ enthalten ist. In diesem Abschnitt geben Sie die API-App-URL für die Datenebene in eine Umgebungseinstellung in der API-App für die mittlere Ebene ein. Wenn der Code in der API-App für die mittlere Ebene die URL-Einstellung der Datenebene abruft, überschreibt die Umgebungseinstellung die Angaben in der Datei „Web.config“.
 
1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/) und dann zum Blatt **API-App** für die API-App, die Sie zum Hosten des Projekts „TodoListAPI“ (mittlere Ebene) erstellt haben.

2. Klicken Sie im Blatt **Einstellungen** der API-App auf **App-Einstellungen**.
 
4. Scrollen Sie im Blatt **Anwendungseinstellungen** der API-App nach unten zum Abschnitt **App-Einstellungen**, und fügen Sie den folgenden Schlüssel und Wert hinzu:

	|Schlüssel|Wert|Beispiel
	|---|---|---|
	|toDoListDataAPIURL|https://{your data tier API app name = Ihr Name der API-App für die Datenebene}.azurewebsites.net|https://todolistdataapi0121.azurewebsites.net|

4. Klicken Sie auf **Speichern**.

	![](./media/app-service-api-dotnet-get-started/asinportal.png)

	Wenn der Code in Azure ausgeführt wird, überschreibt dieser Wert nun die localhost-URL in der Datei „Web.config“.

### Testen des Aufrufs von ToDoListDataAPI durch ToDoListAPI

11. Navigieren Sie in einem Browserfenster zur URL der neuen API-App der mittleren Ebene, die Sie gerade erstellt haben (per Klick auf die URL im Hauptblatt der API-App im Portal).

13. Fügen Sie der URL in der Adresszeile des Browsers „swagger“ hinzu, und drücken Sie dann die EINGABETASTE. (Die URL lautet `http://{apiappname}.azurewebsites.net/swagger`.)

	Im Browser wird die gleiche Swagger-Benutzeroberfläche wie vorher für ToDoListDataAPI angezeigt, aber jetzt ist `owner` kein erforderliches Feld für den Get-Vorgang. Der Grund ist, dass die API-App der mittleren Ebene diesen Wert für Sie an die API-App der Datenebene sendet. (Wenn Sie die Tutorials zur Authentifizierung durcharbeiten, sendet die mittlere Ebene für den Parameter `owner` echte Benutzer-IDs. Vorerst wird ein Sternchen hartcodiert.)

12. Probieren Sie die Get-Methode und die anderen Methoden aus, um zu überprüfen, ob die API-App der mittleren Ebene die API-App der Datenebene erfolgreich aufruft.

	![](./media/app-service-api-dotnet-get-started/midtierget.png)

## <a id="creating"></a> Optional: Erstellen eines API-App-Projekts von Grund auf

In diesem Tutorial laden Sie ASP.NET-Web-API-Projekte für die Bereitstellung in App Service herunter, anstatt Projekte von Grund auf neu zu erstellen. Zum Erstellen eines Projekts, das Sie in einer API-App bereitstellen möchten, können Sie ein typisches Web-API-Projekt erstellen und das Swashbuckle-Paket installieren. Oder Sie können die **Azure-API-App**-Vorlage für neue Projekte verwenden. Klicken Sie zum Verwenden dieser Vorlage auf **Datei > Neu > Projekt > ASP.NET-Webanwendung > Azure-API-App**.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

Die Projektvorlage **Azure-API-App** entspricht der Auswahl der ASP.NET 4.5.2-Vorlage **Leer**, dem Klicken auf das Kontrollkästchen zum Hinzufügen der Web-API-Unterstützung und dem anschließenden Installieren des Swashbuckle-Pakets. Außerdem wird mit der Vorlage Swashbuckle-Konfigurationscode hinzugefügt, der dafür bestimmt ist, die Erstellung von doppelten Swagger-Vorgangs-IDs zu verhindern.

## Optional: Ändern eines App-Typs

Wie bereits beschrieben, besteht der einzige Unterschied zwischen API-Apps, Web-Apps und mobilen Apps darin, wie sie im Portal dargestellt werden. Da alle dieselben Features aufweisen, ist es nicht erforderlich, einen App-Typ zu ändern.

Wenn Sie die Portaldarstellung ändern möchten, ist dies ein einfacher Vorgang. Sie können beispielsweise eine der API-Apps, die Sie gerade erstellt haben, in eine Web-App ändern, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie den Ressourcen-Explorer.

2. Erweitern Sie im linken Navigationsbereich die Option **Abonnements**, und erweitern Sie dann das Abonnement, das Sie gerade verwenden.

4. Erweitern Sie **resourceGroups** und dann die Ressourcengruppe, die Sie gerade verwenden.

5. Erweitern Sie **Microsoft.Web** und **sites**, und wählen Sie die API-App aus, die Sie ändern möchten.

6. Klicken Sie auf **Bearbeiten**.

8. Suchen Sie nach der `kind`-Eigenschaft, und ändern Sie sie von „api“ in „WebApp“.

	![](./media/app-service-api-dotnet-get-started/resexp.png)

9. Klicken Sie auf **Put**.

10. Wenn Sie zum Azure-Portal wechseln, sehen Sie, dass sich das Symbol geändert hat und der neue App-Typ angegeben wird.

## Optional: API-Definitions-URL in Azure-Ressourcen-Manager-Vorlagen

In diesem Tutorial wurde die API-Definitions-URL in Visual Studio und im Azure-Portal veranschaulicht. Sie können die API-Definitions-URL für eine API-App auch konfigurieren, indem Sie [Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md) in Befehlszeilentools verwenden, z. B. [Azure PowerShell](../powershell-install-configure.md) und die [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md).

Öffnen Sie die [Datei „azuredeploy.json“ im Repository für die Beispielanwendung dieses Tutorials](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json), um ein Beispiel für eine Azure-Ressourcen-Manager-Vorlage zum Festlegen der API-Definitionseigenschaft anzuzeigen. Suchen Sie nach dem Abschnitt der Vorlage, die wie im folgenden Beispiel aussieht:

		"apiDefinition": {
		  "url": "https://todolistdataapi.azurewebsites.net/swagger/docs/v1"
		}

## Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie API-Apps erstellen, Code dafür bereitstellen, Clientcode generieren und die Apps dann über .NET-Clients nutzen. Im nächsten Tutorial der Reihe „Erste Schritte mit API-Apps“ wird gezeigt, wie Sie [API-Apps über JavaScript-Clients mit CORS nutzen](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_0224_2016-->