<properties 
	pageTitle="Erstellen einer Node.js-Web-App in Azure App Service" 
	description="Erfahren Sie mehr über die Erstellung und Bereitstellung einer Node.js-Web-App in Azure." 
	services="app-service\web" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>

# Erstellen und Bereitstellen einer Node.js-Web-App in Azure App Service

In diesem Lernprogramm erfahren Sie, wie Sie eine [Node] [nodejs.org]-Anwendung erstellen und mit [Git] in [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) bereitstellen. Die Anweisungen in diesem Lernprogramm gelten für alle Betriebssysteme, unter denen Node ausgeführt werden kann.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![A browser displaying the 'Hello World' message.][helloworld-completed]

##Erstellen einer Web-App und Aktivieren der Git-Veröffentlichung

Führen Sie diese Schritte aus, um eine Web-App zu erstellen und die Git-Veröffentlichung zu aktivieren.

> [AZURE.NOTE]
> Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie kein Konto haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](/de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) oder [sich für eine kostenlose Testversion registrieren](/de-de/pricing/free-trial/?WT.mc_id=A261C142F).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
 
2. Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

3. Klicken Sie auf **Web + Mobile**, dann auf **Web App**.

    ![][portal-quick-create]

4. Geben Sie einen Wert für **URL** ein.

5. Wählen Sie einen App Service-Plan aus, oder erstellen Sie einen neuen. Wenn Sie einen neuen Plan erstellen, wählen Sie die Preisstufe, den Speicherort und andere Optionen aus.

    ![][portal-quick-create2]

6. Klicken Sie auf **Erstellen**.

7. Sobald der Status zu **Wird ausgeführt** wechselt, wird im Portal automatisch das Blatt für Ihre Web-App geöffnet. 

	![][go-to-dashboard]

8. Klicken Sie auf **Bereitstellung**. (Möglicherweise müssen Sie einen Bildlauf durchführen, um diesen Teil des Blatts anzuzeigen.)

	![][deployment-part]

9. Klicken Sie auf **Quelle auswählen**, dann auf **Local Git Repository**. Klicken Sie auf **OK**.

	![][setup-git-publishing]


10. Klicken Sie auf den Bereich **Anmeldeinformationen für die Bereitstellung**. Erstellen Sie einen Benutzernamen und ein Kennwort. Klicken Sie auf **Speichern**. (Wenn Sie die Veröffentlichung für eine Web-App bereits zuvor aktiviert haben, müssen Sie diesen Schritt nicht ausführen.)

	![][deployment-credentials]


11. Für die Veröffentlichung führen Sie ein Push zu einem Git-Remote-Repository aus. Suchen Sie Sie die URL für das Repository, klicken Sie auf **Alle Einstellungen**, und klicken Sie dann auf **Eigenschaften**. Die URL wird unter "GIT URL" aufgeführt.     

	![][git-url]

##Lokales Erstellen und Testen der Anwendung

In diesem Abschnitt erstellen Sie eine **server.js**-Datei mit dem Beispiel  'hello world' aus [nodejs.org]. Das ursprüngliche Beispiel wurde geändert, indem process.env.PORT als Port, an dem beim Ausführen in einer Azure-Web-App gelauscht werden soll, hinzugefügt wurde.

1. Erstellen Sie mithilfe eines Texteditors im Verzeichnis **helloworld** eine neue Datei namens **server.js**. Wenn das Verzeichnis **helloworld** nicht vorhanden ist, erstellen Sie es.

2. Fügen Sie den folgenden Code als Inhalt der Datei **server.js** hinzu, und speichern Sie sie:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Öffnen Sie die Befehlszeile, und verwenden Sie den folgenden Befehl, um die Web-App lokal zu starten:

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

3. Fügen Sie ein Git-Remoteverzeichnis hinzu, um die Updates per Push an die zuvor erstellte Web-App zu übertragen. Verwenden Sie dazu den folgenden Befehl:

		git remote add azure [URL for remote repository]

 
4. Übertragen Sie Ihre Änderungen per Push an Azure mit dem folgenden Befehl:

		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben. Die Ausgabe sollte in etwa wie folgt aussehen:

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
    

5. Um Ihre App anzuzeigen, klicken Sie im Bereich **Web App** des Verwaltungsportals auf die Schaltfläche **Durchsuchen**.

##Veröffentlichen von Änderungen an der Anwendung

1. Öffnen Sie die Datei **server.js** in einem Text-Editor, und ändern Sie 'Hello World\n' in 'Hello Azure\n'. Speichern Sie die Datei.
2. Wechseln Sie über die Befehlszeile zum Verzeichnis **helloworld**, und führen Sie die folgenden Befehle aus:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben. 
	
3. Wählen Sie über die Schaltfläche **Durchsuchen** Ihre App aus, und beachten Sie, dass die Aktualisierungen angewendet wurden.

	![A web page displaying 'Hello Azure'][helloworld-completed]

4. Sie können durch die vorherige Bereitstellung wiederherstellen, indem Sie sie unter **Bereitstellungen** auswählen.

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##Nächste Schritte

In den Schritten dieses Artikels wird zum Erstellen einer Web-App das Azure-Portal verwendet; Sie können jedoch auch die [Azure-Befehlszeilen-Tools für Mac und Linux](xplat-cli.md) verwenden, um diese Aufgaben durchzuführen.

Node.js bietet ein vielseitiges Portfolio an Modulen, die von Ihren Anwendungen verwendet werden können. Weitere Informationen darüber, wie Web-Apps mit Modulen zusammenarbeiten, finden Sie unter [Verwenden von Node.js-Modulen mit Azure-Anwendungen](nodejs-use-node-modules-azure-apps.md).

Weitere Informationen über die Versionen von Node.js, die im Lieferumfang von Azure enthalten sind, und über die Angabe der Version, die mit Ihrer Anwendung verwendet werden soll, finden Sie unter [Festlegen einer Node.js-Version in einer Azure-Anwendung](nodejs-specify-node-version-azure-apps.md).

Wenn nach der Bereitstellung für Azure Probleme mit Anwendung auftreten, erhalten Sie unter [Debuggen einer Node.js-Anwendung auf Azure-Websites](web-sites-nodejs-debug.md) Informationen zur Problemdiagnose.


##Zusätzliche Ressourcen

* [Azure PowerShell](install-configure-powershell.md)
* [Azure-Befehlszeilen-Tools für Mac und Linux](xplat-cli.md)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service and existing Azure services (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Reference for navigating the preview portal (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529715)


[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com


[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png

[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png


[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png

[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png

[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png

[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png


[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png

<!--HONumber=49-->