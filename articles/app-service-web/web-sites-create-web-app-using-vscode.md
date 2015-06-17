<properties
   pageTitle="Erstellen Sie eine ASP.NET 5-Webanwendung in Visual Studio-Code"
   description="In diesem Lernprogramm veranschaulicht erstellen Sie eine ASP.NET 5-Webanwendung, die mithilfe der Visual Studio-Code."
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
	ms.date="06/14/2015" 
	ms.author="erikre;tarcher"/>

# Erstellen Sie eine ASP.NET 5-Webanwendung in Visual Studio-Code

## Übersicht

In diesem Lernprogramm wird gezeigt, wie Sie zum Erstellen einer ASP.NET 5-Webanwendung mit [Visual Studio-Code (Visual Studio-Code)](http://code.visualstudio.com//Docs/whyvscode) und zum Bereitstellen des [Azure Anwendungsdiensts](../app-service/app-service-value-prop-what-is.md). ASP.NET 5 ist eine umfangreiche Umgestaltung von ASP.NET. ASP.NET 5 ist ein neues Open Source- und plattformübergreifende Framework zum Erstellen von modernen cloudbasierte Web apps mit .NET. Weitere Informationen finden Sie unter [Einführung in ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Informationen zum Azure-Anwendungsdiensts Webanwendungen finden Sie unter [Übersicht über Webanwendungen](app-service-web-overview.md).

[AZURE.INCLUDE [App-Service-Web-try-App-Service.MD](../../includes/app-service-web-try-app-service.md)]

## Voraussetzungen  

* Installieren Sie [VS-Code](http://code.visualstudio.com/Docs/setup).
* Installieren Sie [Node.js](http://nodejs.org/download/) - [Node.js](http://nodejs.org/) ist eine Plattform für die schnelle und skalierbare Serveranwendungen, die mit JavaScript erstellen. Knoten ist die Common Language Runtime (Knoten), und [Npm](http://www.npmjs.com/) ist der Paket-Manager für Knoten Module. Um das Gerüst einer ASP.NET 5-Webanwendung in diesem Lernprogramm verwenden Sie Npm.
* Installieren von Git - Sie können sie von einem dieser Speicherorte installieren: [Chocolatey](https://chocolatey.org/packages/git) oder [Git scm.com](http://git-scm.com/downloads). Wenn Sie Git neu sind, wählen Sie [Git scm.com](http://git-scm.com/downloads) und wählen Sie die Option aus, um Git mit GitBash und über die Windows-Eingabeaufforderung verwenden. Nach der Installation von Git müssen Sie auch den Git-Benutzernamen und e-Mail wie später in diesem Lernprogramm erforderlich ist (bei einem Commit von VS-Code).  

## Installieren Sie ASP.NET 5 und DNX
ASP.NET 5/DNX ist ein schlankes .NET Stapel zum Erstellen von modernen Cloud und Web-apps, die auf OS X, Linux und Windows ausgeführt. Es wurde von Grund auf erstellt wurde, um eine optimierte Entwicklungsframework für Anwendungen bereitzustellen, die in der Cloud bereitgestellt oder lokal ausgeführte. Er besteht aus modulare Komponenten bei minimalem Aufwand so die Flexibilität beim Erstellen Ihrer Lösungen behalten.

> [AZURE.NOTE]ASP.NET 5 und DNX (.NET Execution Environment) auf OS X und Linux sind in einer frühen Beta-Vorschau-Zustand.

In diesem Lernprogramm dient zum Erstellen von Anwendungen mit den neuesten Versionen der Entwicklung von ASP.NET 5 und DNX Einstieg. Die folgenden Anweisungen gelten für Windows zur Verfügung. Ausführliche Installationsanweisungen für OS X, Linux und Windows finden Sie unter [ASP.NET 5 installieren und DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Um .NET Version Manager (DNVM) in Windows zu installieren, öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl.

		@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"

	Wird das Skript DNVM herunterladen, und fügen Sie sie in Ihrem Benutzerprofil-Verzeichnis.

2. Starten Sie Windows zum Abschließen der Installation DNVM.

3. Öffnen Sie eine Eingabeaufforderung, und überprüfen Sie den Speicherort des DNVM durch folgende Eingabe.

		where dnvm

	Die Eingabeaufforderung wird einen Pfad ähnlich der folgenden angezeigt.

	![Dnvm-Speicherort](./media/web-sites-create-web-app-using-vscode/00-where-dnvm.png)

4. Nun, da Sie DNVM haben, müssen Sie damit DNX zum Ausführen Ihrer Anwendungen herunterladen. Führen Sie Folgendes an der Eingabeaufforderung ein.

		dnvm upgrade

5. Überprüfen Sie Ihre DNVM, und zeigen Sie die aktive Laufzeit an, indem Sie Folgendes an der Eingabeaufforderung eingeben.

		dnvm list

	Die Eingabeaufforderung zeigt die Details der aktive Laufzeit.

	![DNVM-Speicherort](./media/web-sites-create-web-app-using-vscode/00b-dnvm-list.png)

6. Wenn mehrere DNX Runtime aufgeführt ist, geben Sie Folgendes an der Eingabeaufforderung für die aktive DNX-Laufzeit auf die gleiche Version wie der festgelegt wird, die vom Generator ASP.NET 5 verwendet wird, wenn Sie Ihre Webanwendung weiter unten in diesem Lernprogramm erstellen.

		dnvm use 1.0.0-beta4 –p

> [AZURE.NOTE]Ausführliche Installationsanweisungen für OS X, Linux und Windows finden Sie unter [ASP.NET 5 installieren und DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Erstellen Sie die Web-app 

In diesem Abschnitt wird veranschaulicht, wie eine neue Anwendung ASP.NET Web-app ein Gerüst. Verwenden Sie den Node Package Manager (Npm) installieren [Yeoman](http://yeoman.io/) (Gerüstbau Anwendungstool - das Codeäquivalent zum Visual Studio von Visual Studio **Datei > Neues Projekt** Vorgang), [mühselige](http://gruntjs.com/) (JavaScript Task Runner) und [Bower](http://bower.io/) (Client-Side-Paket-Manager).

1. Öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und navigieren Sie zum Speicherort, in dem Sie ASP.NET-Projekt erstellen möchten.

2. Geben Sie Folgendes an der Eingabeaufforderung Yeoman und unterstützende Tools installieren.

		npm install -g yo grunt-cli generator-aspnet bower

3. Geben Sie Folgendes an der Eingabeaufforderung für das Projekt erstellen und ein Gerüst der app.

		yo aspnet

4. Verwenden Sie die Pfeiltasten zum Auswählen der **Webanwendung** Typ aus dem ASP.NET 5-Generator im Menü, und drücken Sie die & Lt; Geben Sie >.

	![Yeoman - ASP.NET 5 Generator](./media/web-sites-create-web-app-using-vscode/01-yo-aspnet.png)

5. Legen Sie den Namen der neuen ASP.NET Web-app zu **SampleWebApp**. Dieser Name im gesamten Lernprogramm verwendet wird, wenn Sie einen anderen Namen auswählen, müssen Sie es als Ersatz für jedes Vorkommen von **SampleWebApp**. Wenn Sie drücken & Lt; Geben Sie >, Yeoman erstellt einen neuen Ordner mit dem Namen **SampleWebApp** und die erforderlichen Dateien für Ihre neue Anwendung.

6. Öffnen Sie Visual Studio-Code, indem Sie an der Eingabeaufforderung folgenden Befehl eingeben.

		code .

7. Wählen Sie in Visual Studio-Code **Datei > Ordner öffnen**, und wählen Sie den Ordner mit der ASP.NET Web-app.

	![Wählen Sie im Dialogfeld Ordner](./media/web-sites-create-web-app-using-vscode/02-open-folder.png)

	VS-Code laden Sie Ihr Projekt, und zeigen Sie die Dateien in den **Durchsuchen** Windows.

	![Anzeigen von VSCode SampleWebApp-Projekt](./media/web-sites-create-web-app-using-vscode/03-vscode-project.png)

8. Wählen Sie **Ansicht > Befehl Palette**.

9. In der **Befehl Palette**, geben Sie die folgenden Befehle aus.

		dnx:dnu restore - (SampleWebApp)

	Wie Sie mit der Eingabe beginnen, sehen Sie die vollständige Befehlszeile aus der Liste.

	![RESTORE-Befehl](./media/web-sites-create-web-app-using-vscode/04-dnu-restore.png)

	Restore-Befehl installiert die erforderlichen NuGet-Pakete, um die Anwendung auszuführen. Zeigt eine Eingabeaufforderung **Wiederherstellung abgeschlossen** sobald Sie bereit sind.

## Die Webanwendung lokal ausführen

Nun, dass Sie die Webanwendung erstellt und die NuGet-Pakete für die Anwendung abgerufen, können Sie die Webanwendung lokal ausführen.

1. Aus der **Befehl Palette** in Visual Studio-Code, geben Sie Folgendes ein, um die Anwendung lokal ausführen.

		dnx: kestrel - (SampleWebApp, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001

	Zeigt das Befehlsfenster *gestartet* hinein. Wenn nicht im Befehlsfenster angezeigt *gestartet*, überprüfen Sie die unteren linken corning VS-Code auf Fehler in Ihrem Projekt.

5. Öffnen Sie einen Browser, und navigieren Sie zu dem folgenden URL.

	**http://localhost:5001**

	Die Standardseite der Webanwendung wird wie folgt angezeigt werden.

	![Lokale Webanwendung in einem browser](./media/web-sites-create-web-app-using-vscode/08-web-app.png)

## Erstellen einer Web-App im Azure-Vorschauportal

Die folgenden Schritte führt Sie durch die Erstellung einer Webanwendung in Azure Preview-Portal.

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie auf **Neu** an der Ecke oben links im Portal.

3. Klicken Sie auf **Web-Apps > Web App**.

	![Neue Azure Web-app](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)

4. Geben Sie einen Wert für **Name**, wie z. B. **SampleWebAppDemo**. Beachten Sie, das dieser Name muss eindeutig sein und das Portal erzwingen, dass beim Versuch, den Namen eingeben. Daher, wenn Sie ein Geben Sie einen anderen Wert auswählen, Sie müssen ersetzen Sie diesen Wert für jedes Vorkommen von **SampleWebAppDemo** die Sie in diesem Lernprogramm finden Sie unter.

5. Wählen Sie eine vorhandene **App planen Service** oder eine neue zu erstellen. Wenn Sie einen neuen Plan erstellen, wählen Sie Tarif, Standort und weitere Optionen. Weitere Informationen zu App Dienstpläne, finden Sie im Artikel [Azure-App-Dienst plant eine ausführliche Übersicht über](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

	![Azure neue Web-app-blade](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)

6. Klicken Sie auf **Erstellen**.

	![Web-app-blade](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## Aktivieren der Git-Veröffentlichung für die neue Webanwendung

Git ist ein verteiltes Versionskontrollsystem, mit denen Sie Ihre Azure-App-Service-Webanwendung bereitstellen. Speichern Sie den Code, den Sie für Ihre Webanwendung in einem lokalen Git-Repository zu schreiben, und den Code wird in Azure bereitgestellt werden, drücken Sie nach einem remote-Repository.

1. Melden Sie sich an den [Azure-vorschauportal](https://portal.azure.com).

2. Klicken Sie auf **Alle durchsuchen**.

3. Klicken Sie auf **Webanwendungen** zum Anzeigen einer Liste von Web-apps, die Ihre Azure-Abonnement zugeordnet.

4. Wählen Sie die Webanwendung, die Sie in diesem Lernprogramm erstellt haben.

5. Das Web-app-Fenster einen Bildlauf nach unten zu den **Bereitstellung** aus, und klicken Sie auf **fortlaufende Bereitstellung einrichten**.

	![Azure Web app-host](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)

6. Klicken Sie auf **Quelle auswählen > Lokales Git-Repository**.

7. Klicken Sie auf **OK**.

	![Azure lokalen Git-Repository](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)

8. Wenn Sie zuvor nicht die Anmeldeinformationen für das Veröffentlichen einer Webanwendung oder eine andere App-Dienstanwendung für die Bereitstellung eingerichtet haben, richten sie jetzt:

	* Klicken Sie auf **FTP** zum Festlegen von bereitstellungsanmeldeinformationen.

	* Geben Sie einen Benutzernamen und ein Kennwort ein. Sie benötigen dieses Kennwort später, wenn Sie Git einrichten.

	* Klicken Sie auf **Speichern**.

	![Bereitstellung von Azure-Anmeldeinformationen](./media/web-sites-create-web-app-using-vscode/16-azure-credentials.png)

9. Klicken Sie in Ihrer Web-app-Blade auf **Einstellungen > Eigenschaften**. Die URL des remote Git-Repositorys, die Sie bereitstellen, wird angezeigt, unter **
10.  URL **.

10. Kopieren der **GIT-URL** zur späteren Verwendung im Lernprogramm.

	![Azure Git-URL](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## Veröffentlichen Sie Ihre Webanwendung in Azure-App-Dienst

In diesem Abschnitt wird eine lokale Repository und Push Repository in Azure, um Ihre Webanwendung in Azure bereitzustellen erstellen aus.

1. Wählen Sie in Visual Studio-Code, der **Git** -Option in der linken Navigationsleiste.

	![Git-Symbol im Visual Studio-Code](./media/web-sites-create-web-app-using-vscode/git-icon.png)

2. Wählen Sie **Initialize Git-Repository** sicherstellen, dass der Arbeitsbereich ist in der Git-Quellcodeverwaltung.

	![Initialisieren von Git](./media/web-sites-create-web-app-using-vscode/19-initgit.png)

3. Fügen Sie eine Commit-Nachricht, und klicken Sie auf die **Commit alle** Häkchensymbol.

	![Git Commit alle](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)

4. Wenn Git Verarbeitung abgeschlossen ist, werden Sie feststellen, dass keine aufgeführt, die im Fenster Git unter Dateien **Änderungen**.

	![Git keine Änderungen](./media/web-sites-create-web-app-using-vscode/no-changes.png)

5. Öffnen Sie eine Eingabeaufforderung, und wechseln Sie in das Verzeichnis Ihrer Webanwendung.

6. Erstellen Sie eine remote-Referenz zum Übertragen von Updates auf Ihre Webanwendung mithilfe der Git-URL (".git" endet), die Sie zuvor kopiert haben.

		git remote add azure [URL for remote repository]

7. Drücken Sie die Änderungen in Azure durch Eingabe des folgenden Befehls.

		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben. **Hinweis: Das Kennwort wird nicht angezeigt.**

	Die Ausgabe des obigen Befehls endet mit einer Nachricht, die die Bereitstellung erfolgreich war.

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		[new branch]      master -> master

> [AZURE.NOTE]Wenn Sie Änderungen an Ihrer Anwendung vornehmen, können Sie durch Auswahl veröffentlichen die **alle Commit** -option in Visual Studio-Code, und geben Sie die die **Git Push Azure Master** Befehl an einer Eingabeaufforderung.

## Führen Sie die Anwendung in Azure
Nun, dass Sie Ihre Webanwendung bereitgestellt haben, führen wir die Anwendung zwar in Azure gehostet.

Dies kann auf zwei Arten erfolgen:

* Öffnen Sie einen Browser, und geben Sie den Namen Ihrer Webanwendung wie folgt.   

		http://SampleWebAppDemo.azurewebsites.net
 
* In das Azure Preview-Portal finden Sie im Web app-Fenster für Ihre Webanwendung, und klicken Sie auf **Durchsuchen** Ihre app in Ihrem Standard-Webbrowser anzeigen.

![Azure-Webanwendung](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## Zusammenfassung
In diesem Lernprogramm haben Sie gelernt, wie eine Webanwendung in Visual Studio Code erstellen und in Azure bereitstellen. Weitere Informationen zu Visual Studio-Code finden Sie im Artikel [Warum Visual Studio-Code?](https://code.visualstudio.com/Docs/). Informationen zu App-Service Web-apps, finden Sie unter [Übersicht über Webanwendungen](app-service-web-overview.md).

<!---HONumber=GIT-SubDir_Tue_AM_dede-->