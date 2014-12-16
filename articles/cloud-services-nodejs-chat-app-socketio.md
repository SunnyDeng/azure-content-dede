<properties linkid="dev-nodejs-worker-app-with-socketio" urlDisplayName="App Using Socket.IO" pageTitle="Node.js application using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js application hosted on Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einem Azure-Cloud-Dienst

Socket.IO ermöglicht die Echtzeitkommunikation zwischen Ihrem node.js-Server
und Clients. In diesem Lernprogramm wird das Hosten
einer socket.IO-basierten Chat-Anwendung in Azure erläutert. Weitere Informationen
über Socket.IO finden Sie unter <http://socket.io/>.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Browserfenster, in dem der in Azure gehostete Dienst angezeigt wird][Browserfenster, in dem der in Azure gehostete Dienst angezeigt wird]

## Erstellen eines Cloud-Dienst-Projekts

Mit den folgenden Schritten wird das Cloud-Dienst-Projekt erstellt, in dem die Socket.IO-Anwendung gehostet wird.

1.  Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Azure PowerShell**. Klicken Sie dann mit der rechten Maustaste auf **Azure PowerShell**, und wählen Sie **Als Administrator ausführen**.

    ![Azure PowerShell-Symbol][Azure PowerShell-Symbol]

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Ändern Sie das Verzeichnis auf **c:\\node**, und geben Sie die folgenden Befehle ein, um eine neue Projektmappe mit dem Namen **chatapp** und eine Workerrolle mit dem Namen **WorkerRole1** zu erstellen:

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    Sie werden folgende Antwort erhalten:

    ![Ausgabe von new-azureservice und add-azurenodeworkerrolecmdlets][Ausgabe von new-azureservice und add-azurenodeworkerrolecmdlets]

## Herunterladen des Chat-Beispiels

Für dieses Projekt wird das Chat-Beispiel aus dem
[Socket.IO GitHub-Repository][Socket.IO GitHub-Repository] verwendet. Führen Sie die folgenden Schritte aus, um das Beispiel
herunterzuladen und es dem zuvor erstellten Projekt hinzuzufügen.

1.  Erstellen Sie eine lokale Kopie des Repositorys mit der Schaltfläche **Klonen**. Sie können auch die Schaltfläche **ZIP** verwenden, um das Projekt herunterzuladen.

    ![In einem Browserfenster wird https://github.com/LearnBoost/socket.io/tree/master/examples/chat angezeigt, wobei das ZIP-Downloadsymbol hervorgehoben ist.][In einem Browserfenster wird https://github.com/LearnBoost/socket.io/tree/master/examples/chat angezeigt, wobei das ZIP-Downloadsymbol hervorgehoben ist.]

2.  Navigieren Sie in der Verzeichnisstruktur des lokalen Repositorys, bis sie zum Verzeichnis **examples\\chat**
     gelangen. Kopieren Sie den Inhalt dieses Verzeichnisses in das zuvor erstellte Verzeichnis
    **C:\\node\\chatapp\\WorkerRole1**.

    ![Explorer zeigt den Inhalt des aus dem Archiv extrahierten Verzeichnisses "examples\chat" an.][chat-contents]

    Die hervorgehobenen Elemente im Screenshot oben sind die aus dem Verzeichnis **examples\\chat** kopierten Dateien.

3.  Löschen Sie im Verzeichnis **C:\\node\\chatapp\\WorkerRole1** die Datei **server.js**, und benennen Sie anschließend die Datei **app.js** in **server.js** um. Dadurch wird die Standarddatei **server.js** entfernt, die zuvor vom Cmdlet **Add-AzureNodeWorkerRole** erstellt wurde, und durch die Anwendungsdatei aus dem Chat-Beispiel ersetzt.

### Ändern von Server.js und Installieren von Modulen

Bevor die Anwendung im Azure-Emulator getestet wird, müssen
einige kleinere Änderungen vorgenommen werden. Führen Sie an der Datei "server.js"
die folgenden Schritte aus:

1.  Öffnen Sie die Datei server.js im Editor oder in einem anderen Texteditor.

2.  Suchen Sie den Abschnitt **Module dependencies** am Anfang der Datei server.js, und ändern Sie die Zeile **sio = require('..//..//lib//socket.io')** in **sio = require('socket.io')**, wie unten dargestellt:

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  Um sicherzustellen, dass die Anwendung am richtigen Port
    lauscht, öffnen Sie "server.js" im Editor oder Ihrem bevorzugten
    Texteditor und ändern die folgende Zeile, indem Sie **3000** durch **process.env.port** ersetzen, wie unten dargestellt:

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Nachdem Sie die Änderungen in "server.js" gespeichert haben,
gehen Sie folgendermaßen vor, um erforderliche Module zu
installieren. Testen Sie die Anwendung anschließend im Azure-Emulator:

1.  Ändern Sie mit **Azure PowerShell** das Verzeichnis auf **C:\\node\\chatapp\\WorkerRole1**, und verwenden Sie den folgenden Befehl, um die von dieser Anwendung benötigten Module zu installieren:

        PS C:\node\chatapp\WorkerRole1> npm install

    Damit werden die Module installiert, die in der Datei package.json aufgeführt sind. Nach
    Abschluss des Befehls sollten Sie eine Ausgabe ähnlich der
    folgenden erhalten:

    ![Ausgabe des Befehls npm install][Ausgabe des Befehls npm install]

2.  Da dieses Beispiel ursprünglich ein Teil des Socket.IO GitHub-Repositorys
    war und über einen relativen Pfad auf die
    Socket.IO-Bibliothek direkt verwiesen wurde, wurde in der Datei "package.json"
    nicht auf Socket.IO verwiesen. Daher muss es mit dem folgenden Befehl installiert werden:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### Testen und Bereitstellen

1.  Starten Sie den Emulator mit dem folgenden Befehl:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Wenn das Browserfenster geöffnet wird, geben Sie einen Spitznamen ein
    und drücken die Eingabetaste. So können Sie Nachrichten unter einem bestimmten Spitznamen posten. Um die
    Mehrbenutzerfunktion zu testen, öffnen Sie weitere Browserfenster
    mit derselben URL und geben andere Spitznamen ein.

    ![Zwei Browserfenster, in denen Chat-Nachrichten von Benutzer1 und Benutzer2 angezeigt werden][Zwei Browserfenster, in denen Chat-Nachrichten von Benutzer1 und Benutzer2 angezeigt werden]

3.  Stoppen Sie den Emulator nach dem Testen der Anwendung
    mit dem folgenden Befehl:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Verwenden Sie für die Bereitstellung der Anwendung in Azure das Cmdlet
    **Publish-AzureServiceProject**. Beispiel:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Verwenden Sie einen eindeutigen Namen, da der Ver&ouml;ffentlichungsprozess ansonsten fehlschl&auml;gt. Sobald die Bereitstellung erfolgt ist, wird der Browser ge&ouml;ffnet und navigiert zum bereitgestellten Dienst.</p>
<p>Wenn Sie eine Fehlermeldung erhalten, die besagt, dass der angegebene Abonnementname im importierten Ver&ouml;ffentlichungsprofil nicht vorhanden ist, m&uuml;ssen Sie das Ver&ouml;ffentlichungsprofil f&uuml;r Ihr Abonnement vor der Bereitstellung in Azure herunterladen und installieren. Weitere Informationen hierzu finden Sie im Abschnitt <b>Deploying the Application to Azure</b> (Bereitstellen der Anwendung in Azure) auf der Seite <a href="https://www.windowsazure.com/de-de/develop/nodejs/tutorials/getting-started/">Build and deploy a Node.js application to an Azure Cloud Service</a> (Erstellen und Bereitstellen einer Node.js-Anwendung in einem Azure-Cloud-Dienst, in englischer Sprache).</p>
</div>

    ![Browserfenster, in dem der in Azure gehostete Dienst angezeigt wird][Browserfenster, in dem der in Azure gehostete Dienst angezeigt wird]

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Wenn Sie eine Fehlermeldung erhalten, die besagt, dass der angegebene Abonnementname im importierten Ver&ouml;ffentlichungsprofil nicht vorhanden ist, m&uuml;ssen Sie das Ver&ouml;ffentlichungsprofil f&uuml;r Ihr Abonnement vor der Bereitstellung in Azure herunterladen und installieren. Weitere Informationen hierzu finden Sie im Abschnitt <b>Deploying the Application to Azure</b> (Bereitstellen der Anwendung in Azure) auf der Seite <a href="https://www.windowsazure.com/de-de/develop/nodejs/tutorials/getting-started/">Build and deploy a Node.js application to an Azure Cloud Service</a> (Erstellen und Bereitstellen einer Node.js-Anwendung in einem Azure-Cloud-Dienst, in englischer Sprache).</p>
</div>

Ihre Anwendung wird jetzt in Azure ausgeführt und kann
Chat-Nachrichten zwischen verschiedenen Clients mithilfe von Socket.IO vermitteln.

<div class="dev-callout">
<strong>Hinweis</strong>
<p>Zur Vereinfachung beschr&auml;nkt sich dieses Beispiel auf das Chatten zwischen Benutzern, die mit derselben Instanz verbunden sind. Wenn der Cloud-Dienst also zwei Workerrolleninstanzen erstellt, k&ouml;nnen die Benutzer nur mit Benutzern chatten, die mit derselben Workerrolleninstanz verbunden sind. Wenn Sie die Anwendung f&uuml;r mehrere Rolleninstanzen skalieren m&ouml;chten, k&ouml;nnen Sie eine Technologie wie Service Bus verwenden, um den Socket.IO-Speicherzustand f&uuml;r mehrere Instanzen freizugeben. Beispiele hierzu finden Sie in den Syntaxbeispielen f&uuml;r Service Bus Queues und Service Bus Topics im <a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK f&uuml;r Node.js GitHub-Repository</a>.</p>
</div>

## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie eine grundlegende Chat-Anwendung erstellt wird, die in einem Azure-Cloud-Dienst gehostet wird. Informationen zum Hosten dieser Anwendung in einer Azure-Website finden Sie unter [Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einer Azure-Website][Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einer Azure-Website].

  [Browserfenster, in dem der in Azure gehostete Dienst angezeigt wird]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure PowerShell-Symbol]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png
  [Ausgabe von new-azureservice und add-azurenodeworkerrolecmdlets]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png
  [Socket.IO GitHub-Repository]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [In einem Browserfenster wird https://github.com/LearnBoost/socket.io/tree/master/examples/chat angezeigt, wobei das ZIP-Downloadsymbol hervorgehoben ist.]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  [Ausgabe des Befehls npm install]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [Zwei Browserfenster, in denen Chat-Nachrichten von Benutzer1 und Benutzer2 angezeigt werden]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png
  [Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einer Azure-Website]: /de-de/develop/nodejs/tutorials/website-using-socketio/
  [chat-contents]:  ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png