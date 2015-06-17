<properties
   pageTitle="Erstellen einer ASP.NET 5 API-Anwendung in Visual Studio-Code"
   description="In diesem Lernprogramm wird das Erstellen einer 5-API von ASP.NET app mit Visual Studio-Code veranschaulicht."
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
	ms.date="06/04/2015" 
	ms.author="erikre"/>

# Erstellen einer ASP.NET 5 API-Anwendung in Visual Studio-Code

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Übersicht

In diesem Lernprogramm wird gezeigt, wie Sie zum Erstellen einer ASP.NET 5-API-app mit [Visual Studio-Code](http://code.visualstudio.com//Docs/whyvscode). ASP.NET 5 ist eine umfangreiche Umgestaltung von ASP.NET. ASP.NET 5 ist ein neues Open-Source- und Multiplattform-Framework zum Erstellen von modernen Cloud-basierte Webanwendungen mithilfe von .NET. Weitere Informationen finden Sie unter [Einführung in ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Informationen über die API-Anwendungen finden Sie unter [Was API-apps sind?](app-service-api-apps-why-best-platform.md)

> [AZURE.NOTE]Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie nicht über ein Konto verfügen, können Sie [für eine kostenlose Testversion registrieren](/pricing/free-trial/) oder [Aktivieren Sie Ihre MSDN-Abonnementvorteile](/pricing/member-offers/msdn-benefits-details/). Sie können auch kostenlos ausprobieren der [App Service-Appbeispiele](http://tryappservice.azure.com).

## Voraussetzungen  

* Installieren und einrichten [Visual Studio-Code](http://code.visualstudio.com/Docs/setup).
* Installieren Sie [Node.js](http://nodejs.org/download/). < Br > [Knoten](http://nodejs.org/) ist eine Plattform für die schnelle und skalierbare Serveranwendungen, die mit JavaScript erstellen. Knoten ist die Common Language Runtime (Knoten) und [Npm](http://www.npmjs.com/) ist der Paket-Manager für Knoten Module. Um das Gerüst einer app 5 ASP.NET-API in diesem Lernprogramm verwenden Sie Npm.

## Installieren Sie ASP.NET 5 und DNX
ASP.NET 5/DNX ist ein schlankes .NET Stapel zum Erstellen von modernen Cloud und Web-apps, die auf OS X, Linux und Windows ausgeführt. Es wurde von Grund auf erstellt wurde, um eine optimierte Entwicklungsframework für Anwendungen bereitzustellen, die in der Cloud bereitgestellt oder lokal ausgeführte. Er besteht aus modulare Komponenten bei minimalem Aufwand so die Flexibilität beim Erstellen Ihrer Lösungen behalten.

> [AZURE.NOTE]ASP.NET 5 und DNX (.NET Execution Environment) auf OS X und Linux sind in einer frühen Beta-Vorschau-Zustand.

In diesem Lernprogramm dient zum Erstellen von Anwendungen mit den neuesten Versionen der Entwicklung ASP.NET 5 und DNX Einstieg. Auf Wunsch eine Erfahrung stabilere, freigegebene, wechseln Sie zu [http://www.asp.net/vnext](http://www.asp.net/vnext). Die folgenden Anweisungen gelten für Windows zur Verfügung. Ausführliche Installationsanweisungen für OS X, Linux und Windows finden Sie unter [ASP.NET 5 installieren und DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Um .NET Version Manager (DNVM) in Windows zu installieren, führen Sie den folgenden Befehl im Eingabeaufforderungsfenster ein:

	<pre class="prettyprint">
@powershell - NoProfile - ExecutionPolicy unrestricted - Befehl "&amp; {$Branch ="Dev"; Iex ((neues Objekt net.webclient). DownloadString ('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
</pre>Wird das Skript DNVM herunterladen, und fügen Sie sie in Ihrem Benutzerprofil.

2. Sie müssen möglicherweise melden Sie sich nach der Eingabe des obigen Befehls damit die Änderungen an der PATH-Umgebungsvariablen wirksam werden.
3. Überprüfen Sie den Speicherort des DNVM, indem Sie im Eingabeaufforderungsfenster folgenden Befehl ausführen: 

	<pre class="prettyprint">
wobei Dnvm
</pre>Das Befehlsfenster wird einen Pfad ähnlich der folgenden angezeigt:

	![Dnvm-Speicherort](./media/app-service-create-aspnet-api-app-using-vscode/00-where-dnvm.png)

4. Sie DNVM haben, müssen Sie es verwenden, um DNX herunterzuladen, um Ihre Anwendungen auszuführen. Führen Sie den folgenden Befehl im Befehlsfenster ein:

	<pre class="prettyprint">
Dnvm-upgrade
</pre>

5. Überprüfen Sie Ihre DNVM und zeigen Sie die aktive Laufzeit an, indem Sie im Eingabeaufforderungsfenster Folgendes eingeben:

	<pre class="prettyprint">
Dnvm Liste
</pre>Das Befehlsfenster zeigt die Details der aktiven Laufzeit:

	![Dnvm-Speicherort](./media/app-service-create-aspnet-api-app-using-vscode/00b-dnvm-list.png)

Ausführliche Installationsanweisungen für OS X, Linux und Windows finden Sie unter [ASP.NET 5 installieren und DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Die API-Anwendung zu erstellen. 

In diesem Abschnitt wird veranschaulicht, wie eine neue Anwendung ASP.NET-API app ein Gerüst. Verwenden Sie den Node Package Manager (Npm) installieren [Yeoman](http://yeoman.io/), [Leidige](http://gruntjs.com/), und [Bower](http://bower.io/).

1. Öffnen Sie nach der Installation von Visual Studio-Code und Node.js eine Eingabeaufforderung mit Administratorrechten, und navigieren Sie zum Speicherort, in dem alle Ihre ASP.NET-Projekte mit VSCode gefunden werden sollen.
2. Geben Sie Folgendes im Eingabeaufforderungsfenster Yeoman und die unterstützenden Tools zu installieren:

	<pre class="prettyprint">
Npm installieren -g "yo" mühselige Cli Generator Aspnet bower
</pre>

3. Geben Sie Folgendes im Befehlsfenster erstellen den Projektordner und Gerüst der app:

	<pre class="prettyprint">
yo Aspnet
</pre>

4. Befolgen Sie die Anweisungen, die vom Generator durch Durchführen eines Bildlaufs und zum Auswählen der **Web-API-Anwendung** Typ.

	![Yoman - ASP.NET 5 Generator](./media/app-service-create-aspnet-api-app-using-vscode/01-yo-aspnet.png)

5. Legen Sie den Namen der neuen ASP.NET-API Anwendung zu **noch**. Dieser Name wird im Code weiter unten in diesem Lernprogramm verwendet werden. <br> Yoman erstellt einen neuen Ordner mit dem Namen **noch** und die erforderlichen Dateien für Ihre neue Anwendung.
6. Open **Visual Studio-Code**.<br> Sie können die VSCode im Befehlsfenster öffnen, indem Sie eingeben **Code.**.
7. Aus der **Datei** klicken Sie im Menü **Ordner öffnen** und wählen Sie den Ordner, in dem Ihre app ASP.NET-API befindet.

	![Wählen Sie im Dialogfeld Ordner](./media/app-service-create-aspnet-api-app-using-vscode/02-open-folder.png)

	VSCode lädt das Projekt und Anzeigen der Daten in der **Durchsuchen** Fenster.

	![Anzeigen von VSCode Kontakt-Projekt](./media/app-service-create-aspnet-api-app-using-vscode/03-vscode-contacts-project.png)

8. In **VSCode**, aus der **Ansicht** klicken Sie im Menü **Befehl Palette**.
9. In der **Befehl Palette** Geben Sie die folgenden Befehle:

	<pre class="prettyprint">
DNX:DNU wiederherstellen – (noch)
</pre>Wie Sie mit der Eingabe beginnen, sehen Sie die vollständige Befehlszeile aus der Liste.

	![RESTORE-Befehl](./media/app-service-create-aspnet-api-app-using-vscode/04-dnu-restore.png)

	Restore-Befehl installiert die erforderlichen NuGet-Pakete, um die Anwendung auszuführen. Zeigt ein Befehlsfenster **Wiederherstellung abgeschlossen** sobald Sie bereit sind.

## Ändern Sie die API-Anwendung

Ändern Sie jetzt die **noch** app durch Hinzufügen einer **Kontakt** Klasse und ein **ContactsController** Klasse.

1. Platzieren Sie den Cursor über die **noch** Projektname, und fügen Sie einen neuen Ordner mit dem Namen *Modelle* verwenden das Ordnersymbol für neue.

	![Ordner für neue Modelle](./media/app-service-create-aspnet-api-app-using-vscode/07-new-folder.png)

2. Mit der rechten Maustaste die **Modelle** Ordner eine neue Klassendatei mit dem Namen hinzufügen *Contact.cs* durch den folgenden Code:

	<pre class="prettyprint">
Namespace ContactsList.Models
{
    public class Contact
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string EmailAddress { get; set; }
    }
}
</pre>

3. Mit der rechten Maustaste die **Controller** Ordner und fügen eine *ContactsController.cs* Datei, so dass er wie folgt aussieht:

	<pre class="prettyprint">
using System.Collections.Generic;
Verwenden von Microsoft.AspNet.Mvc;
Verwenden von ContactsList.Models;

Namespace ContactsList.Controllers
{
    [Route("api/[controller]")]
    public Class ContactsController: Controller
    {
        GET: api/Kontakte
        [HttpGet]
        Öffentliche IEnumerable &lt; Contact > Get()
        {
            Zurückgeben der neuen Kontakt [] {}
                neuen Kontakt {Id = 1, EmailAddress = "barney@contoso.com" Name = "Barney Polen"},
                neuen Kontakt {Id = 2, EmailAddress = "lacy@contoso.com" Name = "Lacy Barrera"},
                neuen Kontakt {Id = 3 "," EmailAddress = "lora@microsoft.com" Name = "Lora Riggs"}
            };
        }
    }
}
</pre>

4. Sicherstellen, dass alle Dateien gespeichert werden, durch Auswahl **Datei** > **alle**.
5. Aus der **Befehl Palette**, geben Sie Folgendes ein, um die Anwendung lokal ausführen:

	<pre class="prettyprint">
DNX: Kestrel - (noch, Microsoft.AspNet.Hosting--Server Kestrel - server.urls Http://localhost:5001
</pre>Zeigt das Befehlsfenster *gestartet* hinein. Wenn nicht im Befehlsfenster angezeigt *gestartet*, überprüfen Sie die unteren linken corning von VSCode Fehler in Ihrem Projekt.

5. Öffnen Sie einen Browser, und navigieren Sie zu dem folgenden URL:

	**http://localhost:5001/api/Contacts**

	Sie können dann den Inhalt anzeigen *Contacts.json*. Wenn Sie die Datei anzeigen, sehen Sie den folgenden Inhalt:

	![Zurückgegebene Json-Inhalt](./media/app-service-create-aspnet-api-app-using-vscode/08-contacts-json.png)

## Ändern Sie die Metadaten für API-app
Die Metadaten, die kann ein Projekt ASP.NET-API bereitgestellt werden, wie eine API-Anwendung muss, in enthalten sein einem *apiapp.json* Datei im Stammverzeichnis des Projekts.

1. VSCode, mit der rechten Maustaste die *Wwwroot* Ordner wählen Sie die **neue Datei** Option.
2. Nennen Sie die neue Datei *apiapp.json*. < Br > Stellen Sie sicher, dass *apiapp.json* befindet sich in der *Wwwroot* Ordner.
3. Fügen Sie die folgenden Optionen, um die *apiapp.json* Datei:

	<pre class="prettyprint">
{
    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
    "Id": "Noch",
    "Namespace": "microsoft.com",
    "Gateway": "2015-01-14"
    "Version": "1.0.0"
    "Title": "Noch",
    "Zusammenfassung": "",
    "Autor": "",
    "Endpunkte": null
}
</pre>

In der *apiapp.json* Datei können Sie einen Endpunkt für dynamische Swagger-API-Definition JSON angeben, aber in diesem Lernprogramm verwenden Sie eine statische API-Definitionsdatei. Ein Beispiel für die dynamische Generierung von Swagger finden Sie unter [Konfigurieren Sie ein Web-API-Projekt als app-API-](app-service-dotnet-create-api-app-visual-studio.md).

## Statische Swagger-API-Definition hinzufügen
Um eine statische Swagger 2.0-API-Definitionsdatei zu gewährleisten, müssen Sie erstellen einen Ordner im Web-Stammverzeichnis und die API-Definitionsdatei versehen.

1. Erstellen Sie einen Ordner namens in VSCode, *Metadaten* in den *Wwwroot* Ordner.
2. Mit der rechten Maustaste das neue *Metadaten* Ordner und fügen Sie eine neue Datei namens *apiDefinition.swagger.json*. 
3. Fügen Sie die folgende Json-Syntax in die neue Datei ein:

	<pre class="prettyprint">
{
  "swagger": "2.0"
  "Info": {}
    "Version": "v1",
    "Title": "Noch"
  },
  "Host": "Müssen ersetzen diese mit der HOST-URL",
  "Schemas": [
    "Https"
  ],
  "Pfade": {}
    "/ api/Contacts": {}
      "get": {}
        "Tags": [
          "Kontakte"
        ],
        "Vorgangs-ID": "Contacts_Get",
        "belegt":]
        "erstellt": [
          "Application/Json"
          "Text/Json"
          "Application/Xml"
          "Text/Xml"
        ],
        "Antworten": {}
          "200": {}
            "Description": "OK",
            "Schema": {}
              "Typ": "Array",
              "Elemente": {}
                "$ref": "#, Definitionen, Contact"
              }
            }
          }
        },
        "veraltet": false
      },
      "post": {}
        "Tags": [
          "Kontakte"
        ],
        "Vorgangs-ID": "Contacts_Post",
        "belegt": [
          "Application/Json"
          "Text/Json"
          "Application/Xml"
          "text/xml",
          "Application/X-www-form-urlencoded"
        ],
        "erstellt": [
          "Application/Json"
          "Text/Json"
          "Application/Xml"
          "Text/Xml"
        ],
        "Parameter": [
          {
            "Name": "Kontakt"
            "in": "Text".
            "required": "true"
            "Schema": {}
              "$ref": "#, Definitionen, Contact"
            }
          }
        ],
        "Antworten": {}
          "200": {}
            "Description": "OK",
            "Schema": {}
              "$ref": "#, Definitionen, Objekt"
            }
          }
        },
        "veraltet": false
      }
    }
  },
  "Definition": {}
    "Contact": {}
      "Typ": "Object",
      "Eigenschaften": {}
        "Id": {}
          "Format": "int32"
          "Typ": "Integer"
        },
        "Name": {}
          "Typ": "String"
        },
        "EmailAddress": {}
          "Typ": "String"
        }
      }
    },
    "Object": {}
      "Typ": "Object",
      "Eigenschaften": {}
    }
  }
}
</pre>

Später in diesem Lernprogramm wird die oben aufgeführten Host URL Platzhalter-Zeichenfolge mit Ihrer Azure-Host-URL ersetzt, die Sie erstellen und später kopieren.

## Erstellen einer API-App im Azure-Vorschauportal

> [AZURE.NOTE]Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie nicht über ein Konto verfügen, können Sie [für eine kostenlose Testversion registrieren](/pricing/free-trial/) oder [Aktivieren Sie Ihre MSDN-Abonnementvorteile](/pricing/member-offers/msdn-benefits-details/). Sie können auch kostenlos ausprobieren der [App Service-Appbeispiele](http://tryappservice.azure.com).

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie auf **Neu** an der Ecke oben links im Portal.

3. Klicken Sie auf **Web + Mobil > API-App**.

	![Azure neue API-Anwendung](./media/app-service-create-aspnet-api-app-using-vscode/09-azure-newapiapp.png)

4. Geben Sie einen Wert für **Name**, wie z. B. noch.

5. Wählen Sie einen App Service-Plan aus, oder erstellen Sie einen neuen. Wenn Sie einen neuen Plan erstellen, wählen Sie Tarif, Standort und weitere Optionen.

	![Azure neue API-App-Blade](./media/app-service-create-aspnet-api-app-using-vscode/10-azure-newappblade.png)

6. Klicken Sie auf **Erstellen**.

	![App-Blade-API](./media/app-service-create-aspnet-api-app-using-vscode/11-azure-apiappblade.png)

	Wenn Sie bleibt die **an Startmenü anheften** das Kontrollkästchen aktiviert, wenn die app erstellen finden Sie problemlos die Anwendung durch Klicken auf **Home** oder **Durchsuchen**. Wenn Sie das Kontrollkästchen deaktiviert haben, klicken Sie auf **Benachrichtigungen** auf der linken Seite finden Sie unter den API-app-Erstellungsstatus und klicken Sie auf die Benachrichtigung an das Fenster für die neue API-Anwendung zu wechseln.

7. Klicken Sie auf **Einstellungen > Anwendungseinstellungen**.

8. Legen Sie die Zugriffsebene auf **Öffentlich (anonym)** fest.

9. Klicken Sie auf **Speichern**.

	![Azure-App-Einstellungen](./media/app-service-create-aspnet-api-app-using-vscode/12-azure-appsettings.png)

## Aktivieren der Git-Veröffentlichung aus der neuen API-app

Git ist ein verteiltes Versionskontrollsystem, mit dem Sie Ihre Azure-Website bereitstellen können. Sie speichern den Code, den Sie für Ihre API-App geschrieben haben, in einem lokalen Git-Repository. Anschließend stellen Sie Ihren Code in Azure bereit, indem Sie einen Pushvorgang zu einem Remoterepository durchführen. Diese Methode der Bereitstellung ist eine Funktion von App Service-Web-Apps, die Sie in einer API-App nutzen können, da API-Apps auf Web-Apps basieren: Eine API-App in Azure App Service ist eine Web-App mit zusätzlichen Funktionen für das Hosten von Webdiensten.

Im Portal verwalten Sie die API-App-spezifischen Funktionen im Blatt **API-App**. Die Funktionen, die eine API-App mit einer Web-App gemeinsam hat, werden im Blatt **API-App-Host** verwaltet. Sie wechseln daher in diesem Abschnitt zum Blatt **API-App-Host**, um die Git-Bereitstellungsfunktion zu konfigurieren.

1. Klicken Sie im Blatt für die API-App auf **API-App-Host**.

	![Azure-API-App-Host](./media/app-service-create-aspnet-api-app-using-vscode/13-azure-apiapphost.png)

2. Suchen Sie im Blatt **API-App** nach dem Abschnitt **Bereitstellung**, und klicken Sie auf ** 	Kontinuierliche Bereitstellung einrichten**. Sie müssen möglicherweise einen Bildlauf nach unten durchführen, um diesen Teil des Blatts anzuzeigen.

	![Azure-API-App-Host](./media/app-service-create-aspnet-api-app-using-vscode/14-azure-deployment.png)

3. Klicken Sie auf **Quelle auswählen > Lokales Git-Repository**.

5. Klicken Sie auf **OK**.

	![Azure lokalen Git-Repository](./media/app-service-create-aspnet-api-app-using-vscode/15-azure-localrepository.png)

6. Wenn Sie noch keine Anmeldeinformationen für das Veröffentlichen einer API-App oder einer anderen App Service-App eingerichtet haben, holen Sie dies nun nach.

	* Klicken Sie auf **Anmeldeinformationen für die Bereitstellung einrichten**.

	* Geben Sie einen Benutzernamen und ein Kennwort ein. Sie benötigen dieses Kennwort später, wenn Sie Git einrichten.

	* Klicken Sie auf **Speichern**.

	![Bereitstellung von Azure-Anmeldeinformationen](./media/app-service-create-aspnet-api-app-using-vscode/16-azure-credentials.png)

7. Klicken Sie im Blatt **API-App-Host** auf **Einstellungen > Eigenschaften**. Die URL des Git-Remoterepositorys, in dem Sie die Bereitstellung durchführen möchten, wird unter "GIT-URL" angezeigt.

8. Kopieren der **GIT-URL** später im Lernprogramm.

	![Azure Git-URL](./media/app-service-create-aspnet-api-app-using-vscode/17-azure-giturl.png)

9. Darüber hinaus wird in der **API-Anwendung** Blade, Kopie der **URL** die Sie verwenden möchten, aktualisieren Sie den Wert "Host" in der *apiDefinition.swagger.json* Datei.

	![Azure-URL](./media/app-service-create-aspnet-api-app-using-vscode/18-azure-url.png)

10. Öffnen Sie in der VSCode, der *apiDefinition.swagger.json* Datei, und Ersetzen Sie den Hostwert "Müssen ersetzen diese mit der HOST-URL" mit den **URL** Sie im vorigen Schritt kopiert haben.
11. Entfernen Sie außerdem die Zeichen "https://" am Anfang der Hostwert.
12. Speichern Sie die Änderungen an der *apiDefinition.swagger.json* Datei.

## Ihre API-Anwendung in Azure-App-Dienst veröffentlichen

In diesem Abschnitt erstellen Sie ein lokales Git-Repository und führen einen Pushvorgang von diesem Repository zu Azure durch, um Ihre Beispielanwendung in der API-App bereitzustellen, die in Azure App Service ausgeführt wird.

1. Wählen Sie im VSCode auf der linken Navigationsleiste die Git-Option.
2. Wenn Git nicht bereits installiert ist, installieren Sie es durch Auswahl einer der bereitgestellten Links ([Chocolatey](https://chocolatey.org/packages/git) oder [Git scm.com](http://git-scm.com/downloads)). Wenn Sie Git neu sind, wählen Sie **Git scm.com** und wählen Sie die Option aus, um Git mit GitBash aus der Windows-Eingabeaufforderung verwenden. 
3. Nach der Installation Git VSCode starten und die Option Git auf dem linken Balken.
4. Wählen Sie im VSCode, **Initialize Git-Repository** sicherstellen, dass der Arbeitsbereich ist in der Git-Quellcodeverwaltung. 

	![Initialisieren von Git](./media/app-service-create-aspnet-api-app-using-vscode/19-initgit.png)

5. 
6. Fügen Sie eine Commit-Nachricht, und wählen Sie die **Commit alle** überprüfen.

	![Git Commit alle](./media/app-service-create-aspnet-api-app-using-vscode/20-git-commit.png)

6. Suchen und öffnen Sie **GitBash**. Als Alternative können Sie die Windows-Eingabeaufforderung.
7. In **GitBash**, wechseln Sie zum Ordner in den Projektordner VSCode. Zum Beispiel:

	<pre class="prettyprint">
CD c:\VSCodeProjects\ContactsList
</pre>

7. Erstellen Sie eine remote-Referenz zum Übertragen von Updates auf die Webanwendung (API-app-Host), die Sie zuvor erstellt haben, mit der Git-URL (".git" endet), die Sie zuvor kopiert haben:

	<pre class="prettyprint">
git remote add azure [URL for remote repository]
</pre>

8. Übertragen Sie Ihre Änderungen mit dem folgenden Befehl per Push an Azure:

	<pre class="prettyprint">
git push azure master
</pre>Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben. **Hinweis: Das Kennwort wird nicht angezeigt.**

	Die Ausgabe des obigen Befehls endet mit einer Meldung, dass die Bereitstellung erfolgreich ist:

	<pre class="prettyprint">
Remote: wurde erfolgreich bereitgestellt.
To https://user@testsite.scm.azurewebsites.net/testsite.git
[neue Verzweigung] Master-Master >
</pre>

> [AZURE.NOTE]Wenn Sie Änderungen an Ihrer Anwendung vornehmen, können Sie durch Auswahl veröffentlichen die **alle Commit** VSCode einchecken, und geben Sie dann die **Git Push Azure Master** -Befehl in **GitBash**.

## Anzeigen der API-Definition im Azure-Vorschauportal
Nun, da Sie eine API in Ihrer API-App bereitgestellt haben, können Sie die API-Definition im Azure-Vorschauportal anzeigen. Sie beginnen, durch einen Neustart des Gateways, wodurch Azure erkennt, dass eine API-app-API-Definition geändert wurde. Das Gateway ist eine Web-App, die API-Verwaltung und -Autorisierung für die API-Apps in einer Ressourcengruppe übernimmt.

1. Wechseln Sie in das Azure Preview-Portal zu den **API-Anwendung** Blade für die API-Anwendung, die Sie zuvor erstellt haben, und klicken Sie auf die **Gateway** Link.
2. In der **GATEWAY** Blade, klicken Sie auf **Starten**. Sie können dieses Blatt nun schließen.
3. In der **API-Anwendung** Blade, klicken Sie auf die **Starten**. 
4. In der **API-Anwendung** Blade, klicken Sie auf **-API-Definition**.<br> Das Blade-API-Definition werden zwei Methoden veranschaulicht. Wenn Sie die Get- und POST-Methoden nicht sofort sehen, warten Sie einige Sekunden für Azure, um die Anwendung zu aktualisieren. Klicken Sie auf **-API-Definition** in den **API-Anwendung** Blade.

## Führen Sie die Anwendung in Azure
Wechseln Sie in das Azure Preview-Portal zu den **APP-HOST-API** Blade für Ihre API-Anwendung und auf **Durchsuchen**. Fügen Sie dann **api/Contacts** am Ende der URL zum Anzeigen der Details des Kontakts.


## Zusammenfassung
In diesem Lernprogramm haben Sie gelernt, wie eine API-Anwendung in Visual Studio-Code erstellt wird. Weitere Informationen zu Visual Studio-Code, finden Sie unter [Visual Studio-Code](https://code.visualstudio.com/Docs/). Informationen über die API-Anwendungen finden Sie unter [Was API-apps sind?](app-service-api-apps-why-best-platform.md)
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->