<properties linkid="develop-node-create-a-website-mac" urlDisplayName="Web site" pageTitle="Create a Node.js web site on Mac - Azure tutorials" metaKeywords="Azure create website Node, Azure deploy website Node, website Node.js, Node website" description="Learn how to build and deploy a Node.js web site in Azure. Sample code is written in Java." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build and deploy a Node.js web site to Azure" authors="" solutions="" manager="" editor="" />

Erstellen einer Node.js-Website und Bereitstellen für Azure
===========================================================

In diesem Lernprogramm erfahren Sie, wie Sie eine [Node]-Anwendung erstellen und mit [Git] auf einer Azure-Website bereitstellen. Die Anweisungen in diesem Lernprogramm gelten für alle Betriebssysteme, unter denen Node ausgeführt werden kann.

Wenn Sie lieber ein Video sehen möchten, so zeigt der Clip rechts dieselben Schritte wie dieses Lernprogramm.

Nachfolgend finden Sie einen Screenshot der fertigen Anwendung:

![Ein Browser, in dem die Nachricht „Hello World“ angezeigt wird.](./media/web-sites-nodejs-develop-deploy-mac/helloazure.png)

Erstellen einer Azure-Website und Aktivieren der Git-Veröffentlichung
---------------------------------------------------------------------

Befolgen Sie diese Schritte, um eine Azure-Website zu erstellen, und aktivieren Sie dann die Git-Veröffentlichung für die Website.

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose einmonatige Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A7171371E).

1.  Melden Sie sich beim [Windows Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2.  Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

    ![Das Azure-Portal mit markiertem Link „+NEU“.](./media/web-sites-nodejs-develop-deploy-mac/plus-new.png)

3.  Klicken Sie auf **WEBSITE** und dann **SCHNELLERFASSUNG**. Geben Sie einen Wert für **URL** ein, und wählen Sie das Datencenter für Ihre Website in der Dropdown-Liste **REGION** aus. Klicken Sie unten im Dialogfeld auf das Häkchen.

    ![Das Dialogfeld „Schnellerfassung“](./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png)

4.  Nachdem der Website-Status in **Running** geändert wurde, klicken Sie auf den Namen der Website, um das **Dashboard** aufzurufen.

    ![Geöffnetes Website-Dashboard](./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png)

5.  Wählen Sie in der unteren rechten Ecke der Schnellstartseite **Set up a deployment from source control** aus.

    ![Git-Veröffentlichung einrichten](./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png)

6.  Wählen Sie bei der Frage „Wo ist Ihr Quellcode?“ **Local Git repository** aus, und klicken Sie dann auf den Pfeil.

    ![Wo ist Ihr Quellcode](./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png)

7.  Zum Aktivieren der Git-Veröffentlichung müssen Sie einen Benutzernamen und ein Kennwort angeben. Wenn Sie die Veröffentlichung für eine Azure-Website bereits zuvor aktiviert haben, werden Sie nicht nach Benutzernamen und Kennwort gefragt. Stattdessen wird ein Git-Repository anhand des zuvor angegebenen Benutzernamens und Kennworts erstellt. Notieren Sie sich den Benutzernamen und das Kennwort, da diese Angaben für die Git-Veröffentlichung auf allen von Ihnen erstellten Azure-Websites verwendet werden.

    ![Das Dialogfeld mit der Eingabeaufforderung für Benutzername und Kennwort.](./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png)

8.  Sobald das Git-Repository bereit ist, erhalten Sie Anweisungen zu den Git-Befehlen, die zum Einrichten eines lokalen Repositorys und zum anschließenden Übertragen der Dateien an Azure per Push verwendet werden.

    ![Zurückgegebene Git-Bereitstellungsanwendungen nach dem Erstellen eines Repositorys für die Website.](./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png)

Erstellen und lokales Testen der Anwendung
------------------------------------------

In diesem Abschnitt erstellen Sie eine Datei **server.js**, die das „Hello World“-Beispiel von [nodejs.org] enthält. Das ursprüngliche Beispiel wurde geändert, indem process.env.PORT als Port, an dem beim Ausführen in einer Azure-Website gelauscht werden soll, hinzugefügt wurde.

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

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

4.  Öffnen Sie den Webbrowser, und navigieren Sie zu http://localhost:1337. Es wird eine Webseite mit dem Text „Hello World“ wie im nachfolgenden Screenshot dargestellt angezeigt:

    ![Ein Browser, in dem die Nachricht „Hello World“ angezeigt wird.](./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png)

Veröffentlichen der Anwendung
-----------------------------

1.  Wechseln Sie über die Befehlszeile zum Verzeichnis **helloworld**, und geben Sie die folgenden Befehle ein, um ein lokales Git-Repository zu initialisieren.

         git init

    **Git-Befehl nicht verfügbar?**

    [Git](http://git-scm.com/) ist ein verteiltes Versionskontrollsystem, mit dem Sie Ihre Azure-Website bereitstellen können. Installationsanweisungen für Ihre Plattform finden Sie auf der [Download-Seite für Git](http://git-scm.com/download).

2.  Verwenden Sie die folgenden Befehle, um dem Repository Dateien hinzuzufügen:

         git add .
         git commit -m "initial commit"

3.  Fügen Sie „git remote“ hinzu, um die Updates per Push an die Azure-Website zu übertragen, die Sie zuvor erstellt haben. Verwenden Sie dazu den folgenden Befehl:

         git remote add azure [URL für Remote-Repository]

    ![Zurückgegebene Git-Bereitstellungsanwendungen nach dem Erstellen eines Repositorys für die Website.](./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png)

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

    ![Git-Bereitstellungsstatus im Portal](./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png)

5.  Wählen Sie mit der Schaltfläche **Durchsuchen** auf der Seite „Azure-Website“ im Verwaltungsportal Ihre Website aus.

Veröffentlichen von Änderungen an Ihrer Anwendung
-------------------------------------------------

1.  Öffnen Sie die Datei **server.js** in einem Texteditor, und ändern Sie „Hello World\\n“ in „Hello Azure\\n“. Speichern Sie die Datei.
2.  Wechseln Sie über die Befehlszeile ins Verzeichnis **helloworld**, und führen Sie die folgenden Befehle aus:

         git add .
         git commit -m "changing to hello azure"
         git push azure master

    Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben. Wenn Sie zur Registerkarte „Bereitstellungen“ der Azure-Website im Verwaltungsportal navigieren, wird der aktualisierte Bereitstellungsverlauf angezeigt:

    ![Aktualisierter Git-Bereitstellungsstatus im Portal](./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png)

3.  Wählen Sie mit der Schaltfläche **Durchsuchen** Ihre Website aus, und beachten Sie, dass die Aktualisierungen angewandt wurden.

    ![Eine Webseite, die „Hello Azure“ anzeigt](./media/web-sites-nodejs-develop-deploy-mac/helloazure.png)

4.  Sie können die vorherige Bereitstellung wiederherstellen, indem Sie sie auf der Registerkarte „Bereitstellungen“ Ihrer Azure-Website im Verwaltungsportal auswählen und auf die Schaltfläche **Erneut bereitstellen** klicken.

Nächste Schritte
----------------

In diesem Artikel wurde in den Schritten Azure Portal zum Erstellen einer Website verwendet; dennoch können Sie auch die [Azure-Befehlszeilen-Tools für Mac und Linux](/de-de/develop/nodejs/how-to-guides/command-line-tools/) verwenden, um diese Aufgaben durchzuführen.

Node.js bietet ein vielseitiges Portfolio an Modulen, die von Ihren Anwendungen verwendet werden können. Weitere Informationen darüber, wie Azure-Website mit Modulen arbeiten, finden Sie unter [Verwenden von Node.js-Modulen mit Azure-Anwendungen](http://www.windowsazure.com/de-de/develop/nodejs/common-tasks/working-with-node-modules/).

Weitere Informationen über die Versionen von Node.js, die im Lieferumfang von Azure enthalten sind, und über die Angabe der Version, die mit Ihrer Anwendung verwendet werden soll, finden Sie unter [Festlegen einer Node.js-Version in einer Azure-Anwendung](http://www.windowsazure.com/de-de/develop/nodejs/common-tasks/specifying-a-node-version/).

Wenn nach der Bereitstellung für Azure Probleme mit Anwendung auftreten, erhalten Sie unter [Debuggen einer Node.js-Anwendung in Azure-Websites](http://www.windowsazure.com/de-de/develop/nodejs/how-to-guides/Debug-Website/) Informationen zur Problemdiagnose.

Zusätzliche Ressourcen
----------------------

-   [Azure PowerShell](/de-de/develop/nodejs/how-to-guides/powershell-cmdlets/)
-   [Azure-Befehlszeilen-Tools für Mac und Linux](/de-de/develop/nodejs/how-to-guides/command-line-tools/)

