<properties
   pageTitle="Erstellen einer ASP.NET 5-API-App mit Visual Studio-Code"
   description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Visual Studio Code eine ASP.NET 5-API-App erstellen."
   services="app-service\api"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/01/2015" 
	ms.author="erikre"/>

# Erstellen einer ASP.NET 5-API-App mit Visual Studio-Code

> [AZURE.SELECTOR]
- [.NET - Visual Studio 2015](app-service-dotnet-create-api-app.md)
- [.NET - Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)
- [Node.js](app-service-api-nodejs-api-app.md)
- [Java](app-service-api-java-api-app.md)

## Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von [Visual Studio Code](http://code.visualstudio.com//Docs/whyvscode) eine ASP.NET 5-API-App erstellen. Das Design von ASP.NET wurde für ASP.NET 5 deutlich verändert. ASP.NET 5 ist ein neues plattformübergreifendes Open-Source-Framework zum Erstellen moderner cloudbasierter Webanwendungen mit .NET. Weitere Informationen finden Sie unter [Einführung in ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Weitere Informationen zu API-Apps finden Sie unter [Was sind API-Apps?](app-service-api-apps-why-best-platform.md).

> [AZURE.NOTE]Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie kein Konto haben, können Sie [sich für eine kostenlose Testversion registrieren](/pricing/free-trial/) oder [Ihre MSDN-Abonnentenvorteile aktivieren](/pricing/member-offers/msdn-benefits-details/). Sie können auch die kostenlosen [App Service-App-Beispiele](http://tryappservice.azure.com) nutzen.

## Voraussetzungen  

* Installieren Sie [Visual Studio Code](http://code.visualstudio.com/Docs/setup), und richten Sie ihn ein.
* Installieren Sie [Node.js](http://nodejs.org/download/).<br> [Node](http://nodejs.org/) ist eine Plattform für die Erstellung schneller und skalierbarer Serveranwendungen mit JavaScript. Node ist die Laufzeit (Node), [npm](http://www.npmjs.com/) der Paket-Manager für Node-Module. In diesem Lernprogramm erstellen Sie mithilfe von npm das Gerüst für eine ASP.NET 5-API-App.

## Installieren von ASP.NET 5 und DNX
ASP.NET 5/DNX ist ein schlanker .NET-Stapel für die Erstellung moderner Cloud- und Web-Apps für OS X, Linux und Windows. Er wurde von Grund auf neu als optimiertes Entwicklungsframework für Apps konzipiert, die entweder in der Cloud bereitgestellt oder lokal ausgeführt werden. Er besteht aus modularen Komponenten mit minimalem Mehraufwand und ermöglicht Ihnen eine flexible Lösungsentwicklung.

> [AZURE.NOTE]ASP.NET 5 und DNX (die .NET-Ausführungsumgebung) befinden sich für OS X und Linux noch in einer frühen Beta-/Vorschauphase.

Dieses Lernprogramm unterstützt Sie beim Einstieg in die Erstellung von Anwendungen mit den neuesten Entwicklungsversionen von ASP.NET 5 und DNX. Eine stabilere, veröffentlichte Version finden Sie unter [http://www.asp.net/vnext](http://www.asp.net/vnext). Die folgenden Anweisungen beziehen sich explizit auf Windows. Ausführlichere Installationsanweisungen für OS X, Linux und Windows finden Sie unter [Installieren von ASP.NET 5 und DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Wenn Sie .NET Version Manager (DNVM) unter Windows installieren möchten, führen Sie im Befehlsfenster folgenden Befehl aus:

	<pre class="prettyprint">
@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&amp;{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
</pre>Dadurch wird das DNVM-Skript herunterladen und Ihrem Benutzerprofil hinzugefügt.

2. Möglicherweise müssen Sie sich nach Eingabe des oben stehenden Befehls abmelden, damit die Änderung in der PATH-Umgebungsvariablen wirksam wird.
3. Überprüfen Sie den Speicherort von DNVM, indem Sie im Befehlsfenster folgenden Befehl ausführen: 

	<pre class="prettyprint">
where dnvm
</pre>Im Befehlsfenster wird ein Pfad wie der folgende angezeigt:

	![DNVM-Speicherort](./media/app-service-create-aspnet-api-app-using-vscode/00-where-dnvm.png)

4. Nachdem Sie nun über DNVM verfügen, müssen Sie damit DNX herunterladen, um Ihre Anwendungen ausführen zu können. Führen Sie im Befehlsfenster folgenden Befehl aus:

	<pre class="prettyprint">
dnvm upgrade
</pre>

5. Überprüfen Sie DNVM, und zeigen Sie die aktive Laufzeit an, indem Sie im Befehlsfenster Folgendes eingeben:

	<pre class="prettyprint">
dnvm list
</pre>Im Befehlsfenster werden die Details zur aktiven Laufzeit angezeigt.

	![DNVM-Speicherort](./media/app-service-create-aspnet-api-app-using-vscode/00b-dnvm-list.png)

Ausführlichere Installationsanweisungen für OS X, Linux und Windows finden Sie unter [Installieren von ASP.NET 5 und DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Erstellen der API-App 

In diesem Abschnitt erfahren Sie, wie Sie das Gerüst für eine neue ASP.NET-API-App erstellen. Sie verwenden npm (Node Package Manager), um [Yeoman](http://yeoman.io/), [Grunt](http://gruntjs.com/) und [Bower](http://bower.io/) zu installieren.

1. Öffnen Sie nach der Installation von Visual Studio Code und Node.js eine Eingabeaufforderung mit Administratorrechten, und navigieren Sie zu dem Speicherort, in dem alle Ihre ASP.NET-Projekte mit VSCode gespeichert werden sollen.
2. Geben Sie im Befehlsfenster Folgendes ein, um Yeoman und die unterstützenden Tools zu installieren:

	<pre class="prettyprint">
npm install -g yo grunt-cli generator-aspnet bower
</pre>

3. Geben Sie im Befehlsfenster Folgendes ein, um den Projektordner und das Gerüst für die App zu erstellen:

	<pre class="prettyprint">
yo aspnet
</pre>

4. Befolgen Sie die vom Generator bereitgestellten Anweisungen, indem Sie zum Typ der **Web-API-Anwendung** scrollen und diesen auswählen.

	![Yeoman – ASP.NET 5-Generator](./media/app-service-create-aspnet-api-app-using-vscode/01-yo-aspnet.png)

5. Legen Sie den Namen Ihrer neuen ASP.NET-API-App auf **ContactsList** fest. Dieser Name wird in dem Code verwendet, der weiter unten in diesem Lernprogramm bereitgestellt wird. <br> Yeoman erstellt einen neuen Ordner namens **ContactsList** sowie die erforderlichen Dateien für Ihre neue App.
6. Öffnen Sie **Visual Studio Code**.<br>. Sie können VSCode über das Befehlsfenster öffnen, indem Sie **code .** eingeben.
7. Wählen Sie im Menü **Datei** die Option **Ordner öffnen**, und wählen Sie den Ordner, in dem Ihre ASP.NET-API-App gespeichert ist.

	![Dialogfeld "Ordner auswählen"](./media/app-service-create-aspnet-api-app-using-vscode/02-open-folder.png)

	VSCode lädt Ihr Projekt und zeigt es im Fenster **Durchsuchen** an.

	![VSCode mit Projekt "Contact"](./media/app-service-create-aspnet-api-app-using-vscode/03-vscode-contacts-project.png)

8. Wählen Sie in **VSCode** im Menü **Ansicht** die Option **Befehlspalette** aus.
9. Geben Sie in der **Befehlspalette** die folgenden Befehle ein:

	<pre class="prettyprint">
dnx:dnu restore - (ContactsList)
</pre>Wenn Sie mit der Eingabe beginnen, wird die vollständige Befehlszeile aus der Liste angezeigt.

	![Befehl "Restore"](./media/app-service-create-aspnet-api-app-using-vscode/04-dnu-restore.png)

	Der Befehl "Restore" installiert die NuGet-Pakete, die zum Ausführen der Anwendung erforderlich sind. Nach Abschluss des Vorgangs wird ein Befehlsfenster mit dem Hinweis**Wiederherstellung abgeschlossen** angezeigt.

## Ändern der API-App

Nun ändern Sie die **ContactsList**-App, indem Sie eine **Contact**- und eine **ContactsController**-Klasse hinzufügen.

1. Platzieren Sie den Cursor auf dem Projektnamen **ContactsList**, und fügen Sie mithilfe des Symbols für neue Ordner einen neuen Ordner mit dem Namen *Models* ein.

	![Ordner für neue Modelle](./media/app-service-create-aspnet-api-app-using-vscode/07-new-folder.png)

2. Klicken Sie mit der rechten Maustaste auf den Ordner **Models**, um mithilfe des folgenden Codes eine neue Klassendatei namens *Contact.cs* hinzuzufügen:

	<pre class="prettyprint">
namespace ContactsList.Models
{
    public class Contact
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string EmailAddress { get; set; }
    }
}
</pre>

3. Klicken Sie mit der rechten Maustaste auf den Ordner **Controllers**, und fügen Sie eine *ContactsController.cs*-Datei hinzu, sodass der Code folgendermaßen aussieht:

	<pre class="prettyprint">
using System.Collections.Generic;
using Microsoft.AspNet.Mvc;
using ContactsList.Models;

namespace ContactsList.Controllers
{
    [Route("api/[controller]")]
    public class ContactsController : Controller
    {
        // GET: api/Contacts
        [HttpGet]
        public IEnumerable&lt;Contact> Get()
        {
            return new Contact[]{
                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
            };
        }
    }
}
</pre>

4. Stellen Sie sicher, dass alle Dateien gespeichert wurden, indem Sie **Datei** > **Alle speichern** auswählen.
5. Geben Sie in der **Befehlspalette** Folgendes ein, um die App lokal auszuführen:

	<pre class="prettyprint">
dnx: kestrel - (ContactsList, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001
</pre>Im Befehlsfenster wird *Gestartet* angezeigt. Sollte im Befehlsfenster nicht *Gestartet* angezeigt werden, überprüfen Sie, ob links unten in VSCode Fehler für Ihr Projekt angezeigt werden.

5. Öffnen Sie einen Browser, und navigieren Sie zur folgenden URL:

	****http://localhost:5001/api/Contacts**

	Anschließend können Sie die Inhalte von *Contacts.json* anzeigen. In dieser Datei wird folgender Inhalt angezeigt:

	![Zurückgegebene JSON-Inhalte](./media/app-service-create-aspnet-api-app-using-vscode/08-contacts-json.png)

## Ändern von API-App-Metadaten
Die Metadaten, mit denen ein ASP.NET-API-Projekt als API-App bereitgestellt werden kann, müssen in der Datei *apiapp.json* im Stammverzeichnis des Projekts enthalten sein.

1. Klicken Sie in VSCode mit der rechten Maustaste auf den Ordner *wwwroot*, und wählen Sie die Option **Neue Datei** aus.
2. Nennen Sie die neue Datei *apiapp.json*.<br> Stellen Sie sicher, dass sich *apiapp.json* im Ordner *wwwroot* befindet.
3. Fügen Sie der Datei *apiapp.json* folgenden Code hinzu:

	<pre class="prettyprint">
{
    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
    "id": "ContactsList",
    "namespace": "microsoft.com",
    "gateway": "2015-01-14",
    "version": "1.0.0",
    "title": "ContactsList",
    "summary": "",
    "author": "",
    "endpoints": null
}
</pre>

In der Datei *apiapp.json* können Sie einen Endpunkt für eine dynamische Swagger-API-Definitionsdatei (JSON) angeben, im Rahmen dieses Lernprogramms verwenden Sie jedoch eine statische API-Definitionsdatei. Ein Beispiel für die dynamische Swagger-Generierung finden Sie unter [Konfigurieren eines Web-API-Projekts als API-App](app-service-dotnet-create-api-app-visual-studio.md).

## Hinzufügen einer statischen Swagger-API-Definition
Um eine statische Swagger 2.0-API-Definitionsdatei bereitzustellen, müssen Sie einen Ordner im Webstamm erstellen und die API-Definitionsdatei darin speichern.

1. Erstellen Sie in VSCode im Ordner *wwwroot* einen Ordner namens *metadata*.
2. Klicken Sie mit der rechten Maustaste auf den Ordner *metadata*, und fügen Sie eine neue Datei namens *apiDefinition.swagger.json* ein. 
3. Fügen Sie der neuen Datei folgende JSON-Syntax hinzu:

	<pre class="prettyprint">
{
  "swagger": "2.0",
  "info": {
    "version": "v1",
    "title": "ContactsList"
  },
  "host": "MUST REPLACE THIS WITH YOUR HOST URL",
  "schemes": [
    "https"
  ],
  "paths": {
    "/api/Contacts": {
      "get": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Get",
        "consumes": [],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "responses": {
          "200": {
            „description“: „OK“,
            "schema": {
              "type": "array",
              "items": {
                "$ref": "#/definitions/Contact"
              }
            }
          }
        },
        "deprecated": false
      },
      "post": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Post",
        "consumes": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml",
          "application/x-www-form-urlencoded"
        ],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "parameters": [
          {
            "name": "contact",
            "in": "body",
            "required": true,
            "schema": {
              "$ref": "#/definitions/Contact"
            }
          }
        ],
        "responses": {
          "200": {
            „description“: „OK“,
            "schema": {
              "$ref": "#/definitions/Object"
            }
          }
        },
        "deprecated": false
      }
    }
  },
  "definitions": {
    "Contact": {
      "type": "object",
      "properties": {
        "Id": {
          "format": "int32",
          "type": "integer"
        },
        "Name": {
          "type": "string"
        },
        "EmailAddress": {
          "type": "string"
        }
      }
    },
    "Object": {
      "type": "object",
      "properties": {}
    }
  }
}
</pre>

Später in diesem Lernprogramm werden Sie die oben stehende URL-Platzhalterzeichenfolge durch die Azure-Host-URL ersetzen, die Sie später erstellen und kopieren.

## Erstellen einer API-App im Azure-Vorschauportal

> [AZURE.NOTE]Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie kein Konto haben, können Sie [sich für eine kostenlose Testversion registrieren](/pricing/free-trial/) oder [Ihre MSDN-Abonnentenvorteile aktivieren](/pricing/member-offers/msdn-benefits-details/). Sie können auch die kostenlosen [App Service-App-Beispiele](http://tryappservice.azure.com) nutzen.

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie links oben im Portal auf **NEU**.

3. Klicken Sie auf **Web + Mobil > API-App**.

	![Neue API-App in Azure](./media/app-service-create-aspnet-api-app-using-vscode/09-azure-newapiapp.png)

4. Geben Sie einen Wert für **Name** ein, beispielsweise "ContactsList".

5. Wählen Sie einen App Service-Plan aus, oder erstellen Sie einen neuen. Wenn Sie einen neuen Plan erstellen, wählen Sie Tarif, Standort und weitere Optionen.

	![Neues Blatt "API-App" in Azure](./media/app-service-create-aspnet-api-app-using-vscode/10-azure-newappblade.png)

6. Klicken Sie auf **Erstellen**.

	![Blatt "API-App"](./media/app-service-create-aspnet-api-app-using-vscode/11-azure-apiappblade.png)

	Wenn während der Erstellung der App das Kontrollkästchen **An Startmenü anheften** aktiviert war, können Sie die App ganz einfach finden, indem Sie auf **Start** oder **Durchsuchen** klicken. Wenn das Kontrollkästchen deaktiviert war, klicken Sie links auf **Benachrichtigungen**, um den Status der API-App-Erstellung anzuzeigen. Klicken Sie anschließend auf die Benachrichtigung, um zum Blatt für die neue API-App zu wechseln.

7. Klicken Sie auf **Einstellungen > Anwendungseinstellungen**.

8. Legen Sie die Zugriffsebene auf **Öffentlich (anonym)** fest.

9. Klicken Sie auf **Speichern**.

	![App-Einstellungen in Azure](./media/app-service-create-aspnet-api-app-using-vscode/12-azure-appsettings.png)

## Aktivieren der Git-Veröffentlichung über die neue API-App

Git ist ein verteiltes Versionskontrollsystem, mit dem Sie Ihre Azure-Website bereitstellen können. Sie speichern den Code, den Sie für Ihre API-App geschrieben haben, in einem lokalen Git-Repository. Anschließend stellen Sie Ihren Code in Azure bereit, indem Sie einen Pushvorgang zu einem Remoterepository durchführen. Diese Methode der Bereitstellung ist eine Funktion von App Service-Web-Apps, die Sie in einer API-App nutzen können, da API-Apps auf Web-Apps basieren: Eine API-App in Azure App Service ist eine Web-App mit zusätzlichen Funktionen für das Hosten von Webdiensten.

Im Portal verwalten Sie die API-App-spezifischen Funktionen im Blatt **API-App**. Die Funktionen, die eine API-App mit einer Web-App gemeinsam hat, werden im Blatt **API-App-Host** verwaltet. Sie wechseln daher in diesem Abschnitt zum Blatt **API-App-Host**, um die Git-Bereitstellungsfunktion zu konfigurieren.

1. Klicken Sie im Blatt für die API-App auf **API-App-Host**.

	![API-App-Host in Azure](./media/app-service-create-aspnet-api-app-using-vscode/13-azure-apiapphost.png)

2. Suchen Sie im Blatt **API-App** nach dem Abschnitt **Bereitstellung**, und klicken Sie auf ** Kontinuierliche Bereitstellung einrichten**. Sie müssen möglicherweise einen Bildlauf nach unten durchführen, um diesen Teil des Blatts anzuzeigen.

	![API-App-Host in Azure](./media/app-service-create-aspnet-api-app-using-vscode/14-azure-deployment.png)

3. Klicken Sie auf **Quelle auswählen > Lokales Git-Repository**.

5. Klicken Sie auf **OK**.

	![Lokales Git-Repository in Azure](./media/app-service-create-aspnet-api-app-using-vscode/15-azure-localrepository.png)

6. Wenn Sie noch keine Anmeldeinformationen für das Veröffentlichen einer API-App oder einer anderen App Service-App eingerichtet haben, holen Sie dies nun nach.

	* Klicken Sie auf **Anmeldeinformationen für die Bereitstellung einrichten**.

	* Geben Sie einen Benutzernamen und ein Kennwort ein. Dieses Kennwort benötigen Sie später bei der Git-Einrichtung.

	* Klicken Sie auf **Speichern**.

	![Anmeldeinformationen für die Bereitstellung in Azure](./media/app-service-create-aspnet-api-app-using-vscode/16-azure-credentials.png)

7. Klicken Sie im Blatt **API-App-Host** auf **Einstellungen > Eigenschaften**. Die URL des Git-Remoterepositorys, in dem Sie die Bereitstellung durchführen möchten, wird unter "GIT-URL" angezeigt.

8. Kopieren Sie die **GIT-URL** zur späteren Verwendung im Lernprogramm.

	![Git-URL in Azure](./media/app-service-create-aspnet-api-app-using-vscode/17-azure-giturl.png)

9. Kopieren Sie außerdem auf dem Blatt **API-App** die **URL**, die Sie zum Aktualisieren des "host"-Werts in der Datei *apiDefinition.swagger.json* verwenden werden.

	![URL in Azure](./media/app-service-create-aspnet-api-app-using-vscode/18-azure-url.png)

10. Öffnen Sie in VSCode die Datei *apiDefinition.swagger.json*, und ersetzen Sie den "host"-Wert "MUST REPLACE THIS WITH YOUR HOST URL" durch die **URL**, die Sie im vorherigen Schritt kopiert haben.
11. Entfernen Sie außerdem die Zeichen "https://" am Anfang des Hostwerts.
12. Speichern Sie Ihre Änderungen an der Datei *apiDefinition.swagger.json*.

## Veröffentlichen Ihrer API-App in Azure App Service

In diesem Abschnitt erstellen Sie ein lokales Git-Repository und führen einen Pushvorgang von diesem Repository zu Azure durch, um Ihre Beispielanwendung in der API-App bereitzustellen, die in Azure App Service ausgeführt wird.

1. Wählen Sie in VSCode in der linken Navigationsleiste die Option "Git" aus.
2. Wenn Git nicht bereits installiert ist, installieren Sie es, indem Sie einen der bereitgestellten Links ([Chocolatey](https://chocolatey.org/packages/git) oder [git-scm.com](http://git-scm.com/downloads)) auswählen. Falls Sie noch keine Erfahrung mit Git haben, entscheiden Sie sich für **git-scm.com**, und wählen Sie die entsprechende Option aus, um Git mit GitBash und über die Windows-Eingabeaufforderung verwenden zu können. 
3. Sobald Git installiert ist, starten Sie VSCode neu, und wählen Sie die Git-Option in der linken Leiste.
4. Wählen in VSCode Sie **Git-Repository initialisieren** aus, um sicherzustellen, dass die Git-Quellcodeverwaltung in Ihrem Arbeitsbereich aktiviert ist. 

	![Git-Initialisierung](./media/app-service-create-aspnet-api-app-using-vscode/19-initgit.png)

5. Fügen Sie eine Commit-Nachricht hinzu, und aktivieren Sie **Commit für alle**.

	![Git – Commit für alle](./media/app-service-create-aspnet-api-app-using-vscode/20-git-commit.png)

6. Suchen und öffnen Sie **GitBash**. Alternativ dazu können Sie auch die Windows-Eingabeaufforderung verwenden.
7. Wechseln Sie in **GitBash** zu Ihrem VSCode-Projektordner. Beispiel:

	<pre class="prettyprint">
cd c:\VSCodeProjects\ContactsList
</pre>

8. Erstellen Sie einen Remotebezug, um Updates mittels Push an die zuvor erstellte Web-App (API-App-Host) zu übertragen, und verwenden Sie hierbei die zuvor kopierte Git-URL (die auf ".git" endet):

	<pre class="prettyprint">
git remote add azure [URL for remote repository]
</pre>

9. Übertragen Sie Ihre Änderungen mit dem folgenden Befehl per Push an Azure:

	<pre class="prettyprint">
git push azure master
</pre>Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben. **Hinweis: Das Kennwort ist nicht sichtbar.**

	Die Ausgabe des oben stehenden Befehls führt zu einer Meldung, dass die Bereitstellung erfolgreich durchgeführt wurde:

	<pre class="prettyprint">
remote: Deployment successful.
To https://user@testsite.scm.azurewebsites.net/testsite.git
[new branch]      master -> master
</pre>

> [AZURE.NOTE]Wenn Sie Änderungen an Ihrer App vornehmen, können Sie sie erneut veröffentlichen. Aktivieren Sie hierzu in VSCode **Commit für alle**, und geben Sie anschließend in **GitBash** den Befehl **git push azure master** ein.

## Anzeigen der API-Definition im Azure-Vorschauportal
Nun, da Sie eine API in Ihrer API-App bereitgestellt haben, können Sie die API-Definition im Azure-Vorschauportal anzeigen. Im ersten Schritt starten Sie das Gateway neu, durch das Azure erkennt, dass die API-Definition einer API-App geändert wurde. Das Gateway ist eine Web-App, die API-Verwaltung und -Autorisierung für die API-Apps in einer Ressourcengruppe übernimmt.

1. Wechseln Sie im Azure-Vorschauportal zum Blatt **API-App** für die zuvor erstellte API-App, und klicken Sie auf den Link **Gateway**.
2. Klicken Sie auf dem Blatt **Gateway** auf **Neu starten**. Sie können dieses Blatt nun schließen.
3. Klicken Sie auf dem Blatt **API-App** auf **Neu starten**. 
4. Klicken Sie auf dem Blatt **API-App** auf **API-Definition**.<br> Das Blatt "API-Definition" zeigt zwei Methoden an. Wenn Sie die GET- und POST-Methoden nicht sofort sehen, warten Sie einige Sekunden, bis Azure die App aktualisiert hat. Klicken Sie auf dem Blatt **API-App** auf** API-Definition**.

## Ausführen der App in Azure
Wechseln Sie im Azure-Vorschauportal zum Blatt **API-App-Host** für Ihre API-App, und klicken Sie auf **Durchsuchen** . Fügen Sie am Ende Ihrer URL die Zeichenfolge **api/Contacts** ein, um die Kontaktinformationen anzuzeigen


## Zusammenfassung
In diesem Lernprogramm haben Sie erfahren, wie Sie eine API-App in Visual Studio Code erstellen. Weitere Informationen zu Visual Studio Code finden Sie unter [Visual Studio Code](https://code.visualstudio.com/Docs/). Weitere Informationen zu API-Apps finden Sie unter [Was sind API-Apps?](app-service-api-apps-why-best-platform.md).
 

<!---HONumber=Oct15_HO3-->