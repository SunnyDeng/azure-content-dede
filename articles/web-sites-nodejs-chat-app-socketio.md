<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build a Node.js Chat Application with Socket.IO on an Azure Web Site" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einer Azure-Website
===========================================================================

Socket.IO ermöglicht die Echtzeitkommunikation zwischen Ihrem node.js-Server und Clients. In diesem Lernprogramm wird das Hosten einer Socket.IO-basierten Chat-Anwendung als Azure-Website erläutert. Weitere Informationen über Socket.IO finden Sie unter <http://socket.io/>.

**Hinweis**

Die Verfahren dieser Aufgabe gelten für Azure-Websites; für Cloud-Dienste informieren Sie sich unter [Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einem Azure-Cloud-Dienst](http://www.windowsazure.com/en-us/develop/nodejs/tutorials/app-using-socketio/).

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Ein Browser, in dem die Chat-Anwendung angezeigt wird.](./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png)

Herunterladen des Chat-Beispiels
--------------------------------

Für dieses Projekt verwenden wir das Chat-Beispiel aus dem [Socket.IO GitHub-Repository](https://github.com/LearnBoost/socket.io/tree/0.9.14). Führen Sie die folgenden Schritte aus, um das Beispiel herunterzuladen und es dem zuvor erstellten Projekt hinzuzufügen.

1.  Erstellen Sie eine lokale Kopie des Repositorys mit der Schaltfläche **Klonen**. Sie können auch die Schaltfläche **ZIP** verwenden, um das Projekt herunterzuladen.

    ![In einem Browserfenster wird https://github.com/LearnBoost/socket.io/tree/master/examples/chat angezeigt, wobei das ZIP-Downloadsymbol hervorgehoben ist.](./media/web-sites-nodejs-chat-app-socketio/socketio-2.png)

2.  Navigieren Sie in der Verzeichnisstruktur des lokalen Repositorys, bis sie zum Verzeichnis **examples\\chat** gelangen. Kopieren Sie die Inhalte dieses Verzeichnis in ein separates Verzeichnis wie **\\node\\chat**.

Ändern von App.js und Installieren von Modulen
----------------------------------------------

Bevor die Anwendung in Azure bereitgestellt werden kann, müssen einige kleinere Änderungen vorgenommen werden. Führen Sie an der Datei app.js die folgenden Schritte aus:

1.  Öffnen Sie die Datei app.js im Editor oder in einem anderen Texteditor.

2.  Suchen Sie den Abschnitt **Module dependencies** am Anfang der Datei app.js, und ändern Sie die Zeile **sio = require('..//..//lib//socket.io')** in **sio = require('socket.io')**, wie unten dargestellt:

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Um sicherzustellen, dass die Anwendung am richtigen Port lauscht, öffnen Sie app.js im Editor oder Ihrem bevorzugten Texteditor, und ändern Sie die folgende Zeile, indem Sie **3000** durch **process.env.PORT** ersetzen, wie unten dargestellt:

        //app.listen(3000, function () {            //Original
        app.listen(process.env.PORT, function () {  //Aktualisiert
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Nachdem Sie die Änderungen in app.js gespeichert haben, gehen Sie folgendermaßen vor, um erforderliche Module zu installieren:

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **\\node\\chat**, und verwenden Sie den folgenden Befehl, um die von dieser Anwendung benötigten Module zu installieren:

        npm install

    Damit werden die Module installiert, die in der Datei package.json aufgeführt sind. Nach Abschluss des Befehls sollten Sie eine Ausgabe ähnlich der folgenden erhalten:

    ![Ausgabe des Befehls npm install](./media/web-sites-nodejs-chat-app-socketio/socketio-7.png)

2.  Da dieses Beispiel ursprünglich ein Teil des Socket.IO GitHub-Repositorys war und über einen relativen Pfad auf die Socket.IO-Bibliothek direkt verwiesen wurde, wurde in der Datei package.json nicht auf Socket.IO verwiesen. Daher muss es mit dem folgenden Befehl installiert werden:

        npm install socket.io -save

Erstellen einer Azure-Website und Aktivieren der Git-Veröffentlichung
---------------------------------------------------------------------

Befolgen Sie diese Schritte, um eine Azure-Website zu erstellen, und aktivieren Sie dann die Git-Veröffentlichung für die Website.

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A7171371E).

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **\\node\\chat**, und verwenden Sie folgenden Befehl, um eine neue Azure-Website zu erstellen und ein Git-Repository für die Website und das lokale Verzeichnis zu aktivieren. Dadurch wird auch ein Git-Remoterepository namens 'azure' erstellt.

         azure site create mysitename --git

    Sie müssen 'mysitename' mit einem eindeutigen Namen für die Website ersetzen.

2.  Überführen Sie die vorhandenen Dateien mit folgenden Befehlen zum lokalen Repository:

         git add .
         git commit -m "Initial commit"

3.  Übertragen Sie die Dateien mit folgendem Befehl zum Azure-Website-Repository:

         git push azure master

    Sie werden Statusnachrichten erhalten, wenn Module zum Server importiert werden. Sobald der Vorgang abgeschlossen ist, wird die Anwendung auf Ihrer Azure-Website gehostet.



    <b>Hinweis</b>
    <p>Während der Modulinstallation werden evtl. Fehler angezeigt wie 'Das importierte Projekt ... wurde nicht gefunden'. Diese können ignoriert werden.</p>
    </div>

1.  Socket.IO verwendet WebSockets, die nicht standardmäßig auf Azure aktiviert sind. Verwenden Sie folgenden Befehl, um Web Sockets zu aktivieren:

         azure site set -w

    Geben Sie den Namen der Website ein, wenn Sie dazu aufgefordert werden.

    > [WACOM.NOTE] Der Befehl 'azure site set -w' funktioniert nur bei Version 0.7.4 oder höher der plattformübergreifenden Azure-Befehlszeilenschnittstelle. Sie können die Unterstützung von WebSocket im Azure-Verwaltungsportal aktivieren.
    >
    > Um WebSockets im [Azure-Verwaltungsportal](https://manage.windowsazure.com) zu aktivieren, wählen Sie die Konfigurationsseite für Ihre Website, wählen Sie "ON" für den Web Sockets-Eintrag, und speichern Sie die Auswahl.
    >
    > ![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)

2.  Um die Website in Azure anzuzeigen, verwenden Sie folgenden Befehl, um den Webbrowser zu starten und zur gehosteten Website zu navigieren:

         azure site browse

Ihre Anwendung wird jetzt in Azure ausgeführt und kann Chat-Nachrichten zwischen verschiedenen Clients mithilfe von Socket.IO vermitteln.

**Hinweis**

Zur Vereinfachung beschränkt sich dieses Beispiel auf das Chatten zwischen Benutzern, die mit derselben Instanz verbunden sind. Wenn der Cloud-Dienst also zwei Workerrolleninstanzen erstellt, können die Benutzer nur mit Benutzern chatten, die mit derselben Workerrolleninstanz verbunden sind. Wenn Sie die Anwendung für mehrere Rolleninstanzen skalieren möchten, können Sie eine Technologie wie Service Bus verwenden, um den Socket.IO-Speicherzustand für mehrere Instanzen freizugeben. Beispiele hierzu finden Sie in den Syntaxbeispielen für Service Bus Queues und Service Bus Topics im [Azure SDK für Node.js GitHub-Repository](https://github.com/WindowsAzure/azure-sdk-for-node).

Nächste Schritte
----------------

In diesem Lernprogramm haben Sie erfahren, wie eine grundlegende Chat-Anwendung erstellt wird, die in einer Azure-Website gehostet wird. Sie können diese Anwendung auch als Azure-Cloud-Dienst hosten. Die dafür erforderlichen Schritte finden Sie unter [Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einem Azure-Cloud-Dienst](/en-us/develop/nodejs/tutorials/app-using-socketio/).

