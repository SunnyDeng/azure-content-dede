<properties 
	pageTitle="Erstellen einer Node.js-Website auf Mac - Azure-Lernprogramme" 
	description="Erfahren Sie mehr über die Erstellung und Bereitstellung einer Node.js-Website in Azure. Der Beispielcode wurde in Java geschrieben." 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>






# Erstellen und Bereitstellen einer Node.js-Website für Azure

In diesem Lernprogramm erfahren Sie, wie Sie eine [Node] [nodejs.org]-Anwendung erstellen und mit [Git] auf einer Azure-Website bereitstellen. Die Anweisungen in diesem Lernprogramm gelten für alle Betriebssysteme, unter denen Node ausgeführt werden kann.

Wenn Sie sich dieses Lernprogramm lieber als Video ansehen, werden in den folgenden Clips ähnliche Schritte gezeigt:
[AZURE.VIDEO create-a-nodejs-site-deploy-from-github]
 
Unten finden Sie einen Screenshot der vollständigen Anwendung:

![A browser displaying the 'Hello World' message.][helloworld-completed]

##Erstellen einer Azure-Website und Aktivieren der Git-Veröffentlichung

Befolgen Sie diese Schritte, um eine Azure-Website zu erstellen, und aktivieren Sie dann die Git-Veröffentlichung für die Website.

> [AZURE.NOTE]
> Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Kostenlose Azure-Testversion</a>.
> 
> Wenn Sie Azure-Websites ausprobieren möchten, ehe Sie sich für ein Konto anmelden, besuchen Sie <a href="https://trywebsites.azurewebsites.net/?language=nodejs">https://trywebsites.azurewebsites.net</a>. Auf dieser Seite können Sie sofort und kostenlos eine befristete ASP.NET Starter Site in Azure-Websites erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

1. Melden Sie sich am [Azure-Verwaltungsportal] an.

2. Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

    ![The Azure Portal with the +NEW link highlighted.][portal-new-website]

3. Klicken Sie auf **WEBSITE** und dann auf **SCHNELLERFASSUNG**. Geben Sie einen Wert für **URL** ein, und wählen Sie das Datencenter für Ihre Website in der Dropdownliste **REGION** aus. Klicken Sie auf das Häkchen unten im Dialogfeld.

    ![The Quick Create dialog][portal-quick-create]

4. Nachdem der Websitestatus in **Wird ausgeführt** geändert wurde, klicken Sie auf den Namen der Website, um das **Dashboard** aufzurufen.

	![Open web site dashboard][go-to-dashboard]

6. Wählen Sie in der rechten unteren Ecke der Schnellstartseite **Bereitstellung über Quellcodeverwaltung einrichten** aus.

	![Set up Git publishing][setup-git-publishing]

6. Wählen Sie bei der Frage "Wo befindet sich Ihr Quellcode?" **Lokales Git-Repository** aus, und klicken Sie dann auf den Pfeil.

	![where is your source code][where-is-code]

7. Um die Git-Veröffentlichung aktivieren zu können, müssen Sie einen Benutzernamen und ein Kennwort angeben. Wenn Sie die Veröffentlichung für eine Azure-Website bereits zuvor aktiviert haben, werden Sie nicht nach Benutzernamen und Kennwort gefragt. Stattdessen wird ein Git-Repository anhand des zuvor angegebenen Benutzernamens und Kennworts erstellt. Notieren Sie sich den Benutzernamen und das Kennwort, da diese Angaben für die Git-Veröffentlichung auf allen von Ihnen erstellten Azure-Websites verwendet werden.

	![The dialog prompting for user name and password.][portal-git-username-password]

8. Sobald das Git-Repository bereit ist, erhalten Sie Anweisungen zu den Git-Befehlen, die zum Einrichten eines lokalen Repositorys und zum anschließenden Übertragen der Dateien an Azure per Push verwendet werden.

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

##Lokales Erstellen und Testen der Anwendung

In diesem Abschnitt erstellen Sie eine **server.js**-Datei mit dem Beispiel  'hello world' aus [nodejs.org]. Das ursprüngliche Beispiel wurde geändert, indem process.env.PORT als Port, an dem beim Ausführen in einer Azure-Website gelauscht werden soll, hinzugefügt wurde.

1. Erstellen Sie mithilfe eines Texteditors im Verzeichnis **helloworld** eine neue Datei namens **server.js**. Wenn das Verzeichnis **helloworld** nicht vorhanden ist, erstellen Sie es.
2. Fügen Sie den folgenden Code als Inhalt der Datei **server.js** hinzu, und speichern Sie sie:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Öffnen Sie die Befehlszeile, und verwenden Sie den folgenden Befehl, um die Webseite lokal zu starten:

        node server.js

4. Öffnen Sie Ihren Browser, und navigieren Sie zu http://localhost:1337. Eine Website mit "Hello World" wird wie im folgenden Screenshot angezeigt:

    ![A browser displaying the 'Hello World' message.][helloworld-localhost]

##Veröffentlichen der Anwendung

1. Wechseln Sie über die Befehlszeile zum Verzeichnis **helloworld**, und geben Sie die folgenden Befehle ein, um ein lokales Git-Repository zu initialisieren. 

		git init

	> [AZURE.NOTE] **Git-Befehl nicht verfügbar?**
	[Git](http://git-scm.com/%20target="_blank) ist ein verteiltes Versionskontrollsystem, mit dem Sie Ihre Azure-Website bereitstellen können. Installationsanweisungen für Ihre Plattform finden Sie auf der [Download-Seite für Git](http://git-scm.com/download%20target="_blank").

2. Verwenden Sie die folgenden Befehle, um dem Repository Dateien hinzuzufügen:

		git add .
		git commit -m "initial commit"

3. Fügen Sie ein Git-Remoteverzeichnis hinzu, um die Updates per Push an die zuvor erstellte Azure-Website zu übertragen. Verwenden Sie dazu den folgenden Befehl:

		git remote add azure [URL for remote repository]

    ![Git deployment instructions returned after creating a repository for the web site.][git-instructions]
 
4. Übertragen Sie Ihre Änderungen per Push an Azure mit dem folgenden Befehl:

		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort anzugeben, und sehen dann die folgende Ausgabe:

		Password for 'testsite.scm.azurewebsites.net':
		Counting objects: 3, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (3/3), 374 bytes, done.
		Total 3 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '5ebbe250c9'.
		remote: Preparing files for deployment.
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		 * [new branch]      master -> master
    
	Wenn Sie zur Registerkarte "Bereitstellungen" der Azure-Website im Verwaltungsportal navigieren, wird die erste Bereitstellung im Bereitstellungsverlauf angezeigt:

	![Git deployment status on the portal][git-deployments-first] 

5. Wählen Sie mit der Schaltfläche **Durchsuchen** auf der Seite "Azure-Website" im Verwaltungsportal Ihre Website aus.

##Veröffentlichen von Änderungen an der Anwendung

1. Öffnen Sie die Datei **server.js** in einem Text-Editor, und ändern Sie  'Hello World\n' in  'Hello Azure\n'. Speichern Sie die Datei.
2. Wechseln Sie über die Befehlszeile zum Verzeichnis **helloworld**, und führen Sie die folgenden Befehle aus:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben. Wenn Sie zur Registerkarte "Bereitstellungen" der Azure-Website im Verwaltungsportal navigieren, wird der aktualisierte Bereitstellungsverlauf angezeigt:
	
	![Git deployment status updated on the portal][git-deployments-second]

3. Wählen Sie über die Schaltfläche **Durchsuchen** Ihre Website aus, und beachten Sie, dass die Aktualisierungen angewendet wurden.

	![A web page displaying 'Hello Azure'][helloworld-completed]

4. Sie können die vorherige Bereitstellung wiederherstellen, indem Sie sie auf der Registerkarte "Bereitstellungen" Ihrer Azure-Website im Verwaltungsportal auswählen und auf die Schaltfläche **Erneut bereitstellen** klicken.

##Nächste Schritte

In den Schritten dieses Artikels wird zum Erstellen einer Website das Azure-Portal verwendet; Sie können jedoch auch die [Azure-Befehlszeilen-Tools für Mac und Linux] verwenden, um diese Aufgaben durchzuführen.

Node.js bietet ein vielseitiges Portfolio an Modulen, die von Ihren Anwendungen verwendet werden können. Weitere Informationen darüber, wie Azure-Websites mit Modulen zusammenarbeiten, finden Sie unter [Verwenden von Node.js-Modulen mit Azure-Anwendungen](/de-de/documentation/articles/nodejs-use-node-modules-azure-apps/)..

Weitere Informationen über die Versionen von Node.js, die im Lieferumfang von Azure enthalten sind, und über die Angabe der Version, die mit Ihrer Anwendung verwendet werden soll, finden Sie unter [Festlegen einer Node.js-Version in einer Azure-Anwendung](/de-de/documentation/articles/nodejs-specify-node-version-azure-apps/)..

Wenn nach der Bereitstellung in Azure Probleme mit der Anwendung auftreten, erhalten Sie unter [Debuggen einer Node.js-Anwendung in Azure-Websites](/de-de/documentation/articles/web-sites-nodejs-debug/) for information on diagnosing the problem. Anweisungen.


##Zusätzliche Ressourcen

* [Azure PowerShell]
* [Azure-Befehlszeilen-Tools für Mac und Linux]

[Azure PowerShell]: /de-de/documentation/articles/install-configure-powershell/

[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com

[Azure-Verwaltungsportal]: http://manage.windowsazure.com
[Azure-Befehlszeilen-Tools für Mac und Linux]: /de-de/documentation/articles/xplat-cli/

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-new-website]: ./media/web-sites-nodejs-develop-deploy-mac/plus-new.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-git-username-password]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
[git-instructions]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png

[git-deployments-first]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
[git-deployments-second]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png

[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[where-is-code]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png




<!--HONumber=42-->
