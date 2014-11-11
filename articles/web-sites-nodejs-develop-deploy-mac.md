<properties linkid="develop-node-create-a-website-mac" urlDisplayName="Website" pageTitle="Create a Node.js website on Mac - Azure tutorials" metaKeywords="Azure create website Node, Azure deploy website Node, website Node.js, Node website" description="Learn how to build and deploy a Node.js website in Azure. Sample code is written in Java." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Erstellen und Bereitstellen einer Node.js-Website für Azure

In diesem Lernprogramm erfahren Sie, wie Sie eine [Node][Node]-Anwendung erstellen und mit [Git][Git] auf einer Azure-Website bereitstellen. Die Anweisungen in diesem Lernprogramm gelten für alle Betriebssysteme, unter denen Node ausgeführt werden kann.

Wenn Sie lieber ein Video sehen möchten, so zeigt der Clip rechts dieselben Schritte wie dieses Lernprogramm.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Ein Browser, in dem die Nachricht „Hello World“ angezeigt wird.][Ein Browser, in dem die Nachricht „Hello World“ angezeigt wird.]

## Erstellen einer Azure-Website und Aktivieren der Git-Veröffentlichung

Befolgen Sie diese Schritte, um eine Azure-Website zu erstellen, und aktivieren Sie dann die Git-Veröffentlichung für die Website.

<div class="dev-callout"><strong>Hinweis</strong>
<p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Kostenlose Azure-Testversion</a>.</p>
</div>

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

    ![Das Azure-Portal mit markiertem Link „+NEU“.][Das Azure-Portal mit markiertem Link „+NEU“.]

3.  Klicken Sie auf **WEBSITE** und dann auf **SCHNELLERFASSUNG**. Geben Sie einen Wert für **URL** ein, und wählen Sie das Datencenter für Ihre Website in der Dropdownliste **REGION** aus. Klicken Sie auf das Häkchen unten im Dialogfeld.

    ![Das Dialogfeld „Schnellerfassung“][Das Dialogfeld „Schnellerfassung“]

4.  Nachdem der Websitestatus in **Wird ausgeführt** geändert wurde, klicken Sie auf den Namen der Website, um das **Dashboard** aufzurufen.

    ![Website-Dashboard öffnen][Website-Dashboard öffnen]

5.  Wählen Sie in der unteren rechten Ecke der Schnellstartseite **Bereitstellung über Quellcodeverwaltung einrichten** aus.

    ![Git-Veröffentlichung einrichten][Git-Veröffentlichung einrichten]

6.  Wählen Sie bei der Frage "Wo befindet sich Ihr Quellcode?" **Lokales Git-Repository** aus, und klicken Sie dann auf den Pfeil.

    ![Wo befindet sich Ihr Quellcode][Wo befindet sich Ihr Quellcode]

7.  Um die Git-Veröffentlichung aktivieren zu können, müssen Sie einen Benutzernamen und ein Kennwort angeben. Wenn Sie die Veröffentlichung für eine Azure-Website bereits zuvor aktiviert haben, werden Sie nicht nach Benutzernamen und Kennwort gefragt. Stattdessen wird ein Git-Repository anhand des zuvor angegebenen Benutzernamens und Kennworts erstellt. Notieren Sie sich den Benutzernamen und das Kennwort, da diese Angaben für die Git-Veröffentlichung auf allen von Ihnen erstellten Azure-Websites verwendet werden.

    ![Das Dialogfeld mit der Eingabeaufforderung für Benutzername und Kennwort.][Das Dialogfeld mit der Eingabeaufforderung für Benutzername und Kennwort.]

8.  Sobald das Git-Repository bereit ist, erhalten Sie Anweisungen zu den Git-Befehlen, die zum Einrichten eines lokalen Repositorys und zum anschließenden Übertragen der Dateien an Azure per Push verwendet werden.

    ![Zurückgegebene Git-Bereitstellungsanweisungen nach dem Erstellen eines Repositorys für die Website.][Zurückgegebene Git-Bereitstellungsanweisungen nach dem Erstellen eines Repositorys für die Website.]

## Lokales Erstellen und Testen der Anwendung

In diesem Abschnitt erstellen Sie eine Datei **server.js**, die das „Hello World“-Beispiel von [nodejs.org][Node] enthält. Das ursprüngliche Beispiel wurde geändert, indem process.env.PORT als Port, an dem beim Ausführen in einer Azure-Website gelauscht werden soll, hinzugefügt wurde.

1.  Erstellen Sie mithilfe eines Texteditors im Verzeichnis **helloworld** eine neue Datei namens **server.js**. Wenn das Verzeichnis **helloworld** nicht vorhanden ist, erstellen Sie es.
2.  Fügen Sie den folgenden Code als Inhalt der Datei **server.js** hinzu, und speichern Sie sie:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3.  Öffnen Sie die Befehlszeile, und verwenden Sie den folgenden Befehl, um die Webseite lokal zu starten:

        node server.js

4.  Öffnen Sie Ihren Browser, und navigieren Sie zu <http://localhost:1337>. Eine Website mit „Hello World“ wird wie im folgenden Screenshot angezeigt:

    ![Ein Browser, in dem die Nachricht „Hello World“ angezeigt wird.][1]

## Veröffentlichen der Anwendung

1.  Wechseln Sie über die Befehlszeile zum Verzeichnis **helloworld**, und geben Sie die folgenden Befehle ein, um ein lokales Git-Repository zu initialisieren.

        git init

    <div class="dev-callout"><strong>Git-Befehl nicht verf&uuml;gbar?</strong>
<p><a href="http://git-scm.com/" target="_blank">Git</a> ist ein verteiltes Versionskontrollsystem, mit dem Sie Ihre Azure-Website bereitstellen k&ouml;nnen. Installationsanweisungen f&uuml;r Ihre Plattform finden Sie auf der <a href="http://git-scm.com/download" target="_blank">Download-Seite f&uuml;r Git</a>.</p>
</div>

2.  Verwenden Sie die folgenden Befehle, um dem Repository Dateien hinzuzufügen:

        git add .
        git commit -m "initial commit"

3.  Fügen Sie ein Git-Remoteverzeichnis hinzu, um die Updates per Push an die zuvor erstellte Azure-Website zu übertragen. Verwenden Sie dazu den folgenden Befehl:

        git remote add azure [URL for remote repository]

    ![Zurückgegebene Git-Bereitstellungsanweisungen nach dem Erstellen eines Repositorys für die Website.][Zurückgegebene Git-Bereitstellungsanweisungen nach dem Erstellen eines Repositorys für die Website.]

4.  Übertragen Sie Ihre Änderungen per Push an Azure mit dem folgenden Befehl:

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

    Wenn Sie zur Registerkarte „Bereitstellungen“ der Azure-Website im Verwaltungsportal navigieren, wird die erste Bereitstellung im Bereitstellungsverlauf angezeigt:

    ![Git-Bereitstellungsstatus im Portal][Git-Bereitstellungsstatus im Portal]

5.  Wählen Sie mit der Schaltfläche **Durchsuchen** auf der Seite „Azure-Website“ im Verwaltungsportal Ihre Website aus.

## Veröffentlichen von Änderungen an der Anwendung

1.  Öffnen Sie die Datei **server.js** in einem Texteditor, und ändern Sie „Hello World\\n“ in „Hello Azure\\n“. Speichern Sie die Datei.
2.  Wechseln Sie über die Befehlszeile ins Verzeichnis **helloworld**, und führen Sie die folgenden Befehle aus:

        git add .
        git commit -m "changing to hello azure"
        git push azure master

    Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben. Wenn Sie zur Registerkarte „Bereitstellungen“ der Azure-Website im Verwaltungsportal navigieren, wird der aktualisierte Bereitstellungsverlauf angezeigt:

    ![Aktualisierter Git-Bereitstellungsstatus im Portal][Aktualisierter Git-Bereitstellungsstatus im Portal]

3.  Wählen Sie mit der Schaltfläche **Durchsuchen** Ihre Website aus, und beachten Sie, dass die Aktualisierungen angewandt wurden.

    ![Eine Webseite, die „Hello Azure“ anzeigt][Ein Browser, in dem die Nachricht „Hello World“ angezeigt wird.]

4.  Sie können die vorherige Bereitstellung wiederherstellen, indem Sie sie auf der Registerkarte „Bereitstellungen“ Ihrer Azure-Website im Verwaltungsportal auswählen und auf die Schaltfläche **Erneut bereitstellen** klicken.

## Nächste Schritte

In den Schritten dieses Artikels wird zum Erstellen einer Website das Azure-Portal verwendet; Sie können jedoch auch die [Azure-Befehlszeilen-Tools für Mac und Linux][Azure-Befehlszeilen-Tools für Mac und Linux] verwenden, um diese Aufgaben durchzuführen.

Node.js bietet ein vielseitiges Portfolio an Modulen, die von Ihren Anwendungen verwendet werden können. Weitere Informationen darüber, wie Azure-Websites mit Modulen zusammenarbeiten, finden Sie unter [Verwenden von Node.js-Modulen mit Azure-Anwendungen][Verwenden von Node.js-Modulen mit Azure-Anwendungen].

Weitere Informationen über die Versionen von Node.js, die im Lieferumfang von Azure enthalten sind, und über die Angabe der Version, die mit Ihrer Anwendung verwendet werden soll, finden Sie unter [Festlegen einer Node.js-Version in einer Azure-Anwendung][Festlegen einer Node.js-Version in einer Azure-Anwendung].

Wenn nach der Bereitstellung für Azure Probleme mit Anwendung auftreten, erhalten Sie unter [Debuggen einer Node.js-Anwendung in Azure-Websites][Debuggen einer Node.js-Anwendung in Azure-Websites] Informationen zur Problemdiagnose.

## Zusätzliche Ressourcen

-   [Windows Azure PowerShell][Windows Azure PowerShell]
-   [Azure-Befehlszeilen-Tools für Mac und Linux][Azure-Befehlszeilen-Tools für Mac und Linux]

  [Node]: http://nodejs.org
  [Git]: http://git-scm.com
  [Ein Browser, in dem die Nachricht „Hello World“ angezeigt wird.]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Das Dialogfeld „Schnellerfassung“]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
  [Website-Dashboard öffnen]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
  [Git-Veröffentlichung einrichten]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
  [Wo befindet sich Ihr Quellcode]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png
  [Das Dialogfeld mit der Eingabeaufforderung für Benutzername und Kennwort.]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
  [Zurückgegebene Git-Bereitstellungsanweisungen nach dem Erstellen eines Repositorys für die Website.]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png
  [1]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
  [Git-Bereitstellungsstatus im Portal]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
  [Aktualisierter Git-Bereitstellungsstatus im Portal]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png
  [Azure-Befehlszeilen-Tools für Mac und Linux]: /de-de/documentation/articles/xplat-cli/
  [Verwenden von Node.js-Modulen mit Azure-Anwendungen]: /de-de/documentation/articles/nodejs-use-node-modules-azure-apps/
  [Festlegen einer Node.js-Version in einer Azure-Anwendung]: /de-de/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Debuggen einer Node.js-Anwendung in Azure-Websites]: /de-de/documentation/articles/web-sites-nodejs-debug/
  [Windows Azure PowerShell]: /de-de/documentation/articles/install-configure-powershell/
