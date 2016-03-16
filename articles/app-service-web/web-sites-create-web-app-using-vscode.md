<properties
   pageTitle="Erstellen einer ASP.NET 5-Web-App in Visual Studio"
   description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Visual Studio Code eine ASP.NET 5-Web-App erstellen."
   services="app-service\web"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="erikre"/>

# Erstellen einer ASP.NET 5-Web-App in Visual Studio

## Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie eine ASP.NET 5-Web-App mit [Visual Studio Code (VS Code)](http://code.visualstudio.com//Docs/whyvscode) erstellen und in [Azure App Service](../app-service/app-service-value-prop-what-is.md) bereitstellen.

> [AZURE.NOTE] Obwohl sich dieser Artikel auf Web-Apps bezieht, gilt er auch für API-Apps und mobile Apps.

Das Design von ASP.NET wurde für ASP.NET 5 deutlich verändert. ASP.NET 5 ist ein neues plattformübergreifendes Open Source-Framework zum Erstellen moderner cloudbasierter Web-Apps mit .NET. Weitere Informationen finden Sie unter [Introduction to ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html) (Einführung in ASP.NET 5; in englischer Sprache). Informationen zu Azure App Service-Web-Apps finden Sie unter [Web-Apps – Übersicht](app-service-web-overview.md).

[AZURE.INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## Voraussetzungen  

* Installieren Sie [VS Code](http://code.visualstudio.com/Docs/setup).
* Installieren Sie [Node.js](http://nodejs.org). Node.js ist eine Plattform für die Erstellung schneller und skalierbarer Serveranwendungen mit JavaScript. Node ist die Laufzeit, [npm](http://www.npmjs.com/) der Paket-Manager für Node-Module. In diesem Lernprogramm erstellen Sie mithilfe von npm das Gerüst für eine ASP.NET 5-Web-App.
* Installieren Sie Git (entweder über [Chocolatey](https://chocolatey.org/packages/git) oder über [git-scm.com](http://git-scm.com/downloads)). Falls Sie noch keine Erfahrung mit Git haben, entscheiden Sie sich für [git-scm.com](http://git-scm.com/downloads) und wählen Sie die Option **Git über die Windows-Eingabeaufforderung verwenden** aus. Legen Sie nach der Installation von Git den Git-Benutzernamen und die dazugehörige E-Mail-Adresse fest, da diese Angaben später in diesem Lernprogramm (beim Ausführen eines Commits in VS Code) benötigt werden.  

## Installieren von ASP.NET 5 und DNX
ASP.NET 5/DNX (die .NET-Ausführungsumgebung) ist ein schlanker .NET-Stapel für die Erstellung moderner Cloud- und Web-Apps für OS X, Linux und Windows. Er wurde von Grund auf neu als optimiertes Entwicklungsframework für Apps konzipiert, die entweder in der Cloud bereitgestellt oder lokal ausgeführt werden. Er besteht aus modularen Komponenten mit minimalem Mehraufwand und ermöglicht Ihnen eine flexible Lösungsentwicklung.

Dieses Lernprogramm unterstützt Sie beim Einstieg in die Anwendungserstellung mit den neuesten Entwicklungsversionen von ASP.NET 5 und DNX. Die folgenden Anweisungen beziehen sich explizit auf Windows. Ausführlichere Installationsanweisungen für OS X, Linux und Windows finden Sie unter [Installing ASP.NET 5 and DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx) (Installieren von ASP.NET 5 und DNX; in englischer Sprache).

1. Wenn Sie .NET Version Manager (DNVM) unter Windows installieren möchten, öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl aus:

		@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"

	Dadurch wird das DNVM-Skript herunterladen und Ihrem Benutzerprofilverzeichnis hinzugefügt.

2. **Starten Sie Windows neu**, um die DNVM-Installation abzuschließen.

	Nach dem Neustart von Windows können Sie die Eingabeaufforderung öffnen, um den Speicherort von DNVM zu überprüfen. Geben Sie dazu Folgendes ein:

		where dnvm

	In der Eingabeaufforderung erscheint ein Pfad wie der folgende:

	![DNVM-Speicherort](./media/web-sites-create-web-app-using-vscode/00-where-dnvm.png)

3. Nachdem Sie nun über DNVM verfügen, müssen Sie damit DNX herunterladen, um Ihre Anwendungen ausführen zu können. Führen Sie über die Eingabeaufforderung den folgenden Befehl aus:

		dnvm upgrade

	Überprüfen Sie DNVM, und zeigen Sie die aktive Laufzeit an, indem Sie an der Eingabeaufforderung Folgendes eingeben:

		dnvm list

	In der Eingabeaufforderung werden die Details zur aktiven Laufzeit angezeigt.

	![DNVM-Speicherort](./media/web-sites-create-web-app-using-vscode/00b-dnvm-list.png)

	Wenn mehrere DNX-Laufzeiten aufgeführt sind, können Sie an der Eingabeaufforderung die folgende Version (oder eine neuere Version) eingeben, um die aktive DNX-Laufzeit festzulegen. Legen Sie sie auf die gleiche Version fest, die auch vom ASP.NET 5-Generator verwendet wird, wenn Sie weiter unten in diesem Tutorial Ihre Web-App erstellen. *Sie müssen die aktive Laufzeit ggf. nicht ändern, wenn sie auf die neueste verfügbare festgelegt ist.*

		dnvm use 1.0.0-update1 –p

> [AZURE.NOTE] Ausführlichere Installationsanweisungen für OS X, Linux und Windows finden Sie unter [Installing ASP.NET 5 and DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx) (Installieren von ASP.NET 5 und DNX; in englischer Sprache).

## Erstellen der Web-App 

In diesem Abschnitt erfahren Sie, wie Sie das Gerüst für eine neue ASP.NET-Web-App erstellen. Hierzu installieren Sie mithilfe des Node Package Managers (NPM) Folgendes: [Yeoman](http://yeoman.io/) (Tool zur Erstellung von Anwendungsgerüsten; VS Code-Pendant zu **Datei > Neues Projekt** in Visual Studio), [Grunt](http://gruntjs.com/) (JavaScript-Aufgabenausführung) und [Bower](http://bower.io/) (clientseitiger Paket-Manager).

1. Öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und navigieren Sie zu dem Ort, an dem Sie Ihr ASP.NET-Projekt erstellen möchten. Erstellen Sie z. B. das Verzeichnis *vscodeprojects* im Stammverzeichnis von Laufwerk C:.

2. Geben Sie an der Eingabeaufforderung Folgendes ein, um Yeoman und die unterstützenden Tools zu installieren:

		npm install -g yo grunt-cli generator-aspnet bower

	> [AZURE.NOTE] Möglicherweise werden Sie in einer Warnmeldung darauf hingewiesen, dass die npm-Version veraltet ist. Diese Warnung sollte keine Auswirkung auf dieses Tutorial haben.

3. Geben Sie an der Eingabeaufforderung Folgendes ein, um den Projektordner und das Gerüst für die App zu erstellen:

		yo aspnet

4. Wählen Sie im ASP.NET 5-Generatormenü mithilfe der Pfeiltasten den Typ **Webanwendung Basic** aus und drücken Sie anschließend die **EINGABETASTE**.

	![Yeoman – ASP.NET 5-Generator](./media/web-sites-create-web-app-using-vscode/01-yo-aspnet.png)

5. Legen Sie den Namen Ihrer neuen ASP.NET-Web-App auf **SampleWebApp** fest. Dieser Name wird im gesamten Lernprogramm verwendet. Wenn Sie einen anderen Namen verwenden, müssen jedes Vorkommen von **SampleWebApp** durch diesen Namen ersetzen. Wenn Sie die **EINGABETASTE** drücken, erstellt Yeoman einen neuen Ordner namens **SampleWebApp** sowie die erforderlichen Dateien für Ihre neue App.

6. Verschieben Sie an der Eingabeaufforderung die Verzeichnisse in Ihren neuen Projektordner:

		cd SampleWebApp

7. Geben Sie an der Eingabeaufforderung auch den folgenden Befehl ein, um die erforderlichen NuGet-Pakete zum Ausführen der Anwendung zu installieren:

		dnu restore

8. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein, um VS Code zu öffnen:

		code .

## Ausführen der Web-App (lokal)

Nachdem Sie die Web-App erstellt und alle erforderlichen NuGet-Pakete abgerufen haben, können Sie die Web-App lokal ausführen.

1. Geben Sie in VS Code unter **Befehlspalette** den folgenden Befehl ein, um die verfügbaren Optionen für die Befehlsausführung anzuzeigen:

		dnx: Run Command

	> [AZURE.NOTE] Wenn der Omnisharp-Server aktuell nicht ausgeführt wird, wird er gestartet. Geben Sie den oben aufgeführten Befehl erneut ein.

	Wählen Sie dann den folgenden Befehl zum Ausführen der Web-App aus:
		
		dnx web - (SampleWebApp)

	Im Befehlsfenster wird angezeigt, dass die Anwendung gestartet wurde. Wenn diese Meldung im Befehlsfenster nicht angezeigt wird, überprüfen Sie, ob links unten in VS Code Fehler für Ihr Projekt angezeigt werden.
	
	> [AZURE.NOTE] Zum Aufrufen eines Befehls über die **Befehlspalette** ist das Zeichen **>** am Anfang der Befehlszeile erforderlich. Sie können die Details des Befehls **web** in der Datei *project.json* anzeigen.

2. Öffnen Sie einen Browser, und navigieren Sie zur folgenden URL:

	**http://localhost:5000**

	Die Standardseite der Web-App wird wie folgt angezeigt:

	![Lokale Web-App in einem Browser](./media/web-sites-create-web-app-using-vscode/08-web-app.png)

3. Schließen Sie Ihren Browser. Drücken Sie im **Befehlsfenster** die Tastenkombination **STRG+C**, um die Anwendung zu beenden und das **Befehlsfenster** zu schließen.

## Erstellen einer Web-App im Azure-Portal

Die folgenden Schritte dienen zum Erstellen einer Web-App im Azure-Portal:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie links oben im Portal auf **NEU**.

3. Klicken Sie auf **Web-Apps > Web-App**.

	![Azure – neue Web-App](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)

4. Geben Sie einen Wert für **Name** ein (beispielsweise **SampleWebAppDemo**). Dieser Name muss eindeutig sein. Die Eindeutigkeit wird vom Portal bei der Eingabe überprüft. Falls Sie sich für einen anderen Wert entscheiden, müssen Sie im weiteren Verlauf dieses Lernprogramms jedes Vorkommen von **SampleWebAppDemo** durch diesen anderen Wert ersetzen.

5. Wählen Sie einen vorhandenen **App Service-Plan** aus, oder erstellen Sie einen neuen Plan. Wenn Sie einen neuen Plan erstellen, wählen Sie Tarif, Standort und weitere Optionen. Weitere Informationen zu App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

	![Azure – Blatt für neue Web-App](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)

6. Klicken Sie auf **Erstellen**.

	![Blatt für Web-App](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## Aktivieren der Git-Veröffentlichung für die neue Web-App

Git ist ein verteiltes Versionskontrollsystem, mit dem Sie Ihre Azure App Service-Web-App bereitstellen können. Dazu speichern Sie den Code, den Sie für Ihre Web-App geschrieben haben, in einem lokalen Git-Repository und stellen ihn anschließend mithilfe eines Pushvorgangs zu einem Remoterepository in Azure bereit.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf **Durchsuchen**.

3. Klicken Sie auf **Web-Apps**, um eine Liste mit den Web-Apps anzuzeigen, die Ihrem Azure-Abonnement zugeordnet sind.

4. Wählen Sie die Web-App aus, die Sie in diesem Lernprogramm erstellt haben.

5. Klicken Sie auf dem Blatt der Web-App auf **Einstellungen** > **Dauerhafte Bereitstellung**.

	![Azure – Web-App-Host](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)

6. Klicken Sie auf **Quelle auswählen > Lokales Git-Repository**.

7. Klicken Sie auf **OK**.

	![Lokales Git-Repository in Azure](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)

8. Falls Sie noch keine Anmeldeinformationen für die Bereitstellung einer Web-App oder einer anderen App Service-App eingerichtet haben, holen Sie dies jetzt nach:

	* Klicken Sie auf **Einstellungen** > **Anmeldeinformationen für die Bereitstellung**. Das Blatt **Anmeldeinformationen für die Bereitstellung festlegen** wird angezeigt.

	* Geben Sie einen Benutzernamen und ein Kennwort ein. Dieses Kennwort benötigen Sie später bei der Git-Einrichtung.

	* Klicken Sie auf **Speichern**.

9. Klicken Sie auf dem Blatt der Web-App auf **Einstellungen > Eigenschaften**. Die URL des Git-Remoterepositorys, in dem Sie die Bereitstellung durchführen möchten, wird unter **GIT-URL** angezeigt.

10. Kopieren Sie den unter **GIT-URL** angegebenen Wert zur späteren Verwendung im Lernprogramm.

	![Git-URL in Azure](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## Veröffentlichen Ihrer Web-App in Azure App Service

In diesem Abschnitt erfahren Sie, wie Sie ein lokales Repository erstellen und einen Pushvorgang von diesem Repository an Azure durchführen, um Ihre Web-App in Azure bereitzustellen.

1. Wählen Sie in VS Code auf der linken Navigationsleiste die Option **Git** aus.

	![Git-Symbol in VS Code](./media/web-sites-create-web-app-using-vscode/git-icon.png)

2. Wählen Sie **Git-Repository initialisieren** aus, um sicherzustellen, dass in Ihrem Arbeitsbereich die Git-Quellcodeverwaltung aktiviert ist.

	![Git-Initialisierung](./media/web-sites-create-web-app-using-vscode/19-initgit.png)

3. Öffnen Sie das Befehlsfenster, und wechseln Sie zum Verzeichnis Ihrer Web-App. Geben Sie dann den folgenden Befehl ein:

		git config core.autocrlf false

	Mit diesem Befehl wird ein Problem mit Text bei CRLF- und LF-Endungen verhindert.

4. Fügen Sie in VS Code eine Commitnachricht hinzu, und klicken Sie anschließend auf das Häkchen**Commit für alle**.

	![Git – umfassendes Commit](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)

5. Nach Abschluss der Verarbeitung durch Git sehen Sie, dass im Git-Fenster unter **Änderungen** keine Dateien aufgeführt sind.

	![Git – keine Änderungen](./media/web-sites-create-web-app-using-vscode/no-changes.png)

6. Kehren Sie zum Befehlsfenster zurück, in dem die Eingabeaufforderung auf das Verzeichnis mit Ihrer Web-App zeigt.

7. Erstellen Sie einen Remotebezug, um Updates mittels Push an Ihre Web-App zu übertragen. Verwenden Sie hierbei die zuvor kopierte (auf ".git" endende) Git-URL:

		git remote add azure [URL for remote repository]

8. Konfigurieren Sie Git, um Ihre Anmeldeinformationen lokal zu speichern, sodass sie automatisch an Ihre Push-Befehle angehängt werden, die vom Visual Studio-Code generiert werden.

		git config credential.helper store

9. Übertragen Sie Ihre Änderungen mithilfe des folgenden Befehls per Push an Azure: Nach diesem ersten Push in Azure können Sie alle Push-Befehle über Visual Studio-Code ausführen.

		git push -u azure master

	Sie werden aufgefordert, das zuvor in Azure erstellte Kennwort einzugeben. **Hinweis: Das Kennwort ist nicht sichtbar.**

	Die Ausgabe des obigen Befehls endet mit dem Hinweis, dass die Bereitstellung erfolgreich durchgeführt wurde:

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		[new branch]      master -> master

> [AZURE.NOTE] Wenn Sie Änderungen an Ihrer App vornehmen, können Sie mithilfe der integrierten Git-Funktionen direkt in Visual Studio-Code die erneute Veröffentlichung ausführen, indem Sie die Option **Commit für alle** gefolgt von der Option **Push** verwenden. Sie finden Sie die Option **Push** im Dropdownmenü neben den Schaltflächen **Commit für alle** und **Aktualisieren**.

Wenn Sie an einem Projekt zusammenarbeiten müssen, sollten Sie zwischen Push-Vorgängen auf Azure Push-Vorgänge auf GitHub vornehmen.

## Ausführen der App in Azure
Nachdem Sie Ihre Web-App nun bereitgestellt haben, können wir sie ausführen, während sie in Azure gehostet ist.

Dies kann auf zwei Arten erfolgen:

* Öffnen Sie einen Browser, und geben Sie den Namen Ihrer Web-App wie folgt ein:   

		http://SampleWebAppDemo.azurewebsites.net
 
* Navigieren Sie im Azure-Portal zum Blatt für Ihre Web-App und klicken Sie auf **Durchsuchen**, um Ihre App anzuzeigen.
* in Ihrem Standardbrowser anzuzeigen.

![Azure-Web-App](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## Zusammenfassung
In diesem Lernprogramm wurde gezeigt, wie Sie eine Web-App in VS Code erstellen und in Azure bereitstellen. Weitere Informationen zu VS Code finden Sie im Artikel [Why Visual Studio Code?](https://code.visualstudio.com/Docs/) (Argumente für Visual Studio Code; in englischer Sprache). Informationen zu App Service-Web-Apps finden Sie unter [Web-Apps – Übersicht](app-service-web-overview.md).

<!---HONumber=AcomDC_0302_2016-->