<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Erstellen einer Node.js-Chatanwendung mit Socket.IO auf einer Azure-Website

Socket.IO ermöglicht die Echtzeitkommunikation zwischen Ihrem Node.js-Server und Clients über WebSockets. Außerdem unterstützt es Fallback auf andere Transportmechanismen (wie Long Polling), die mit älteren Browsern funktionieren. In diesem Lernprogramm wird das Hosten einer Socket.IO-basierten Chatanwendung als Azure-Website erläutert. Weitere Informationen über Socket.IO finden Sie unter [][]<http://socket.io/></a>.

> [WACOM.NOTE] Die Verfahren dieser Aufgabe gelten für Azure-Websites. Bei Verwendung von Cloud-Diensten finden Sie Informationen unter [Erstellen einer Node.js-Chatanwendung mit Socket.IO in einem Azure-Cloud-Dienst][Erstellen einer Node.js-Chatanwendung mit Socket.IO in einem Azure-Cloud-Dienst].

## <span id="Download"></span></a>Herunterladen des Chatbeispiels

Für dieses Projekt wird das Chatbeispiel aus dem
[Socket.IO GitHub-Repository][Socket.IO GitHub-Repository] verwendet. Führen Sie die folgenden Schritte aus, um das Beispiel
herunterzuladen und dem zuvor erstellten Projekt hinzuzufügen.

1.  Laden Sie eine [archivierte ZIP- oder GZ-Version][archivierte ZIP- oder GZ-Version] des Socket.IO-Projekts herunter (für dieses Dokument wurde Version 1.0.6 verwendet).

2.  Extrahieren Sie das Archiv, und kopieren Sie das Verzeichnis **examples\\chat**
     in ein neues Verzeichnis. Beispiel:
    **\\node\\chat**.

## <span id="Modify"></span></a>Ändern von App.js und Installieren von Modulen

Bevor die Anwendung in Azure bereitgestellt werden
kann, müssen einige kleinere Änderungen vorgenommen werden.

1.  Benennen Sie die Datei **index.js** in **app.js** um. Damit kann Azure erkennen, dass es sich hier um eine Node.js-Anwendung handelt.

2.  Öffnen Sie die Datei **app.js** im Editor oder in einem anderen Texteditor.

3.  Suchen Sie den Abschnitt **Module dependencies** am Anfang der Datei "app.js", und ändern Sie die Zeile mit `var io = require('../..')(server);` in `var io = require('socket.io')(server);`, wie unten gezeigt:

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

Nachdem Sie die Änderungen in "app.js" gespeichert haben,
gehen Sie folgendermaßen vor, um erforderliche Module zu installieren:

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **\\node\\chat**, und verwenden Sie den folgenden Befehl, um die von dieser Anwendung benötigten Module zu installieren:

        npm install

    Damit werden die Module installiert, die in der Datei package.json aufgeführt sind. Nach
    Abschluss des Befehls sollten Sie eine Ausgabe ähnlich der
    folgenden erhalten:

        express@3.4.8 node_modules\express
        ├── methods@0.1.0
        ├── merge-descriptors@0.0.1
        ├── debug@0.8.1
        ├── cookie-signature@1.0.1
        ├── range-parser@0.0.4
        ├── fresh@0.2.0
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── mkdirp@0.3.5
        ├── commander@1.3.2 (keypress@0.1.0)
        ├── send@0.1.4 (mime@1.2.11)
        └── connect@2.12.0 (uid2@0.0.3, pause@0.0.1, qs@0.6.6, bytes@0.2.1, raw-body@1.1.2, batch@0.5.0, negotiator@0.3.0, multiparty@2.2.0)

2.  Da dieses Beispiel ursprünglich ein Teil des Socket.IO GitHub-Repositorys
    war und über einen relativen Pfad auf die
    Socket.IO-Bibliothek direkt verwiesen wurde, wurde in der Datei "package.json"
    nicht auf Socket.IO verwiesen. Daher muss es mit dem folgenden Befehl installiert werden:

        npm install socket.io@1.0.6 -save

    > [WACOM.NOTE] Obwohl die Vorgehensweise in diesem Artikel auch mit neueren Versionen von Socket.IO funktionieren kann, erfolgte der Testdurchlauf mit Version 1.0.6.

## <span id="Publish"></span></a>Erstellen einer Azure-Website

Befolgen Sie diese Schritte, um eine Azure-Website zu erstellen und dann die Git-Veröffentlichung sowie die WebSocket-Unterstützung für die Website zu aktivieren.

> [WACOM.NOTE]Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **\\node\\chat**, und verwenden Sie folgenden Befehl, um eine neue Azure-Website zu erstellen und ein Git-Repository für die Website und das lokale Verzeichnis zu aktivieren. Dadurch wird auch ein Git-Remoterepository namens "azure" erstellt.

        azure site create mysitename --git

    Sie müssen "mysitename" durch einen eindeutigen Namen für Ihre Website ersetzen.

2.  Übergeben Sie die vorhandenen Dateien mit folgenden Befehlen an das lokale Repository:

        git add .
        git commit -m "Initial commit"

3.  Übertragen Sie die Dateien mit folgendem Push-Befehl an das Azure-Website-Repository:

        git push azure master

    Sie werden Statusnachrichten erhalten, wenn Module zum Server importiert werden. Sobald dieser Vorgang abgeschlossen ist, wird die Anwendung auf Ihrer Azure-Website gehostet.

    > [WACOM.NOTE] Während der Modulinstallation werden eventuell Fehler angezeigt wie "Das importierte Projekt ... wurde nicht gefunden". Diese können ignoriert werden.

4.  Socket.IO verwendet WebSockets, die nicht standardmäßig auf Azure aktiviert sind. Verwenden Sie folgenden Befehl, um Web Sockets zu aktivieren:

        azure site set -w

    Geben Sie bei entsprechender Aufforderung den Namen der Website ein.

    > [WACOM.NOTE]
    > Der Befehl "azure site set -w" funktioniert nur bei Version 0.7.4 oder höher der plattformübergreifenden Azure-Befehlszeilenschnittstelle. Sie können die Unterstützung von WebSocket im Azure-Verwaltungsportal aktivieren.
    >
    > Um WebSockets im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] zu aktivieren, wählen Sie die Seite "Konfigurieren" für Ihre Website aus. Wählen Sie dann für den WebSockets-Eintrag "EIN" aus, und klicken Sie auf "Speichern".
    >
    > ![websockets][websockets]

5.  Um die Website in Azure anzuzeigen, verwenden Sie folgenden Befehl, um den Webbrowser zu starten und zur gehosteten Website zu navigieren:

        azure site browse

Ihre Anwendung wird jetzt in Azure ausgeführt und kann
Chatnachrichten zwischen verschiedenen Clients mithilfe von Socket.IO vermitteln.

> [WACOM.NOTE] Zur Vereinfachung beschränkt sich dieses Beispiel auf das Chatten zwischen Benutzern, die mit derselben Instanz verbunden sind. Wenn der Cloud-Dienst also zwei Workerrolleninstanzen erstellt, können die Benutzer nur mit Benutzern chatten, die mit derselben Workerrolleninstanz verbunden sind. Wenn Sie die Anwendung für mehrere Rolleninstanzen skalieren möchten, können Sie eine Technologie wie Service Bus verwenden, um den Socket.IO-Speicherzustand für mehrere Instanzen freizugeben. Beispiele hierzu finden Sie in den Syntaxbeispielen für Service Bus Queues und Service Bus Topics im [Azure SDK für Node.js GitHub-Repository][Azure SDK für Node.js GitHub-Repository].

## Horizontales Skalieren

Socket.IO-Anwendungen können über einen **Adapter** horizontal skaliert werden, um Nachrichten und Ereignisse unter mehreren Anwendungsinstanzen zu verteilen. Es stehen zwar mehrere Adapter zur Verfügung, allerdings empfiehlt sich der Adapter [socket.io-redis][socket.io-redis], da sich dieser einfach mit der Azure-Redis-Cache-Funktion verwenden lässt.

> [WACOM.NOTE] Eine weitere Anforderung für das horizontale Skalieren einer Socket.IO-Lösung ist die Unterstützung persistenter Sitzungen. Persistente Sitzungen sind für Azure-Websites standardmäßig über das Azure-Anforderungsrouting aktiviert. Weitere Informationen finden Sie unter [Instance Affinity in Azure Web Sites][Instance Affinity in Azure Web Sites] (Instanzaffinität in Azure-Websites, in englischer Sprache).

### Erstellen eines Redis-Caches

Führen Sie die Schritte unter [Einen Cache erstellen in Azure Redis Cache][Einen Cache erstellen in Azure Redis Cache] aus, um einen neuen Cache zu erstellen.

> [WACOM.NOTE] Notieren Sie sich den **Hostnamen** und den **Primärschlüssel** für Ihren Cache, da Sie diese Informationen in den nächsten Schritten benötigen.

### Hinzufügen der Module "redis" und "socket.io-redis"

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **\\node\\chat**, und führen Sie folgenden Befehl aus:

        npm install socket.io-redis@0.1.3 redis@0.11.0 --save

    > [WACOM.NOTE] Die in diesem Befehl angegebenen Versionen sind die Versionen, die beim Testen dieses Artikels verwendet wurden.

2.  Fügen Sie in der Datei **app.js** die folgenden Zeilen direkt nach `var io = require('socket.io')(server);` ein.

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Ersetzen Sie **redishostname** und **rediskey** durch den Hostnamen und den Schlüssel für Ihren Redis-Cache.

    Damit wird ein Veröffentlichungs-/Abonnementclient für den zuvor erstellten Redis-Cache erstellt. Die Clients werden dann mit dem Adapter verwendet, um Socket.IO für die Verwendung des Redis-Caches zum Übergeben von Nachrichten und Ereignissen zwischen Instanzen Ihrer Anwendung zu konfigurieren.

    > [WACOM.NOTE] Obwohl der Adapter **socket.io-redis** direkt mit Redis kommunizieren kann, unterstützt die aktuelle Version (Stand 14.7.2014) die vom Azure-Redis-Cache benötigte Authentifizierung nicht. Daher wird die Erstverbindung über das Modul **redis** hergestellt und der Client dann an den Adapter **socket.io-redis** übergeben.
    >
    > Der Azure-Redis-Cache unterstützt zwar sichere Verbindungen über Port 6380, die in diesem Beispiel verwendeten Module jedoch nicht (Stand 14.7.2014). Im obigen Code wird daher der unsichere Standardport verwendet.

3.  Speichern Sie die geänderte Datei **app.js**.

### Übergeben der Änderungen und erneutes Bereitstellen

Verwenden Sie in der Befehlszeile im Verzeichnis **\\node\\chat** die folgenden Befehle, um die Änderungen zu übergeben und die Anwendung erneut bereitzustellen.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Nachdem Sie Änderungen an den Server übertragen wurden, können Sie Ihre Website mit dem folgenden Befehl auf mehrere Instanzen skalieren.

    azure site scale instances --instances #

Hierbei steht **\#** für die Anzahl der zu erstellenden Instanzen.

Sie können über mehrere Browser oder Computer eine Verbindung mit Ihrer Website herstellen, um sicherzustellen, dass Nachrichten korrekt an alle Clients gesendet werden.

## <span id="tshooting"></span></a>Problembehandlung

### Verbindungsbeschränkungen

Azure-Websites sind in verschiedenen SKUs erhältlich. Diese bestimmen, welche Ressourcen für Ihre Website zur Verfügung stehen. Dazu gehört auch die Anzahl der zulässigen WebSocket-Verbindungen. Weitere Informationen finden Sie unter [Preisdetails für Websites][Preisdetails für Websites].

### Kein Nachrichtenversand über WebSockets

Wenn Clientbrowser immer wieder auf Long Polling zurückfallen und WebSockets nicht verwenden, versuchen Sie den Grund wie folgt zu finden.

-   **Beschränken des Transports auf WebSockets**

    Damit WebSockets von Socket.IO als Nachrichtentransportmechanismus verwendet werden kann, müssen Server und Client WebSockets unterstützen. Ist dies bei einem der beiden nicht der Fall, handelt Socket.IO einen anderen Transportmechanismus wie Long Polling aus. Die Standardliste der von Socket.IO verwendeten Transportmechanismen lautet `websocket, htmlfile, xhr-polling, jsonp-polling`. Sie können erzwingen, dass nur WebSockets verwendet wird, indem Sie in der Datei **app.js** den folgenden Code nach der Zeile mit `, nicknames = {};` einfügen.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [WACOM.NOTE] Beachten Sie, dass ältere Browser ohne WebSockets-Unterstützung keine Verbindung mit der Website herstellen können, während der obige Code aktiv ist, da er die Kommunikation auf die alleinige Verwendung von WebSockets beschränkt.

-   **Verwenden von SSL**

    WebSockets stützt sich auf weniger häufig verwendete HTTP-Header wie den **Upgrade**-Header. Manche zwischengeschaltete Netzwerkgeräte wie Webproxys entfernen unter Umständen diese Header. Um dieses Problem zu vermeiden, können Sie die WebSocket-Verbindung über SSL einrichten.

    Dies lässt sich einfach erreichen, indem Sie `match origin protocol` für Socket.IO konfigurieren. Damit weisen Sie Socket.IO an, die WebSockets-Kommunikation so zu sichern wie dies bei der ursprünglichen HTTP/HTTPS-Anforderung für die Webseite der Fall war. Wenn ein Browser über eine HTTPS-URL auf Ihre Website zugreift, wird die nachfolgende WebSocket-Kommunikation durch Socket.IO über SSL gesichert.

    Um das Beispiel für diese Konfiguration entsprechend zu ändern, fügen Sie den folgenden Code in die Datei **app.js** nach der Zeile mit `, nicknames = {};` ein.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

-   **Prüfen der Einstellungen in "web.config"**

    Azure-Websites, die Node.js-Anwendungen hosten, leiten eingehende Anforderungen mithilfe der Datei **web.config** an die Node.js-Anwendungen weiter. Damit WebSockets korrekt mit Node.js-Anwendungen funktioniert, muss **web.config** folgenden Eintrag enthalten.

        <webSocket enabled="false"/>

    Damit wird das IIS-WebSockets-Modul deaktiviert, das eine eigene Implementierung von WebSockets enthält und mit Node.js-spezifischen WebSocket-Modulen wie Socket.IO in Konflikt gerät. Wenn diese Zeile fehlt oder auf `true` festgelegt ist, könnte dies der Grund dafür sein, dass der WebSocket-Transport bei Ihrer Anwendung nicht funktioniert.

    Normalerweise enthalten Node.js-Anwendungen keine **web.config**-Datei, sodass Azure-Websites eine solche Datei für Node.js-Anwendungen bei deren Bereitstellung automatisch generieren. Da diese Datei automatisch auf dem Server generiert wird, müssen Sie die FTP- oder FTPS-URL für Ihre Website zur Anzeige der Datei verwenden. Die FTP- und FTPS-URLs für Ihre Website können Sie im Azure-Verwaltungsportal einsehen, indem Sie Ihre Website und dann den Link **Dashboard** auswählen. Die URLs werden im Bereich **Auf einen Blick** angezeigt.

    > [WACOM.NOTE] Die Datei **web.config** wird von Azure-Websites nur generiert, wenn Ihre Anwendung keine bereitstellt. Wenn Sie eine **web.config**-Datei im Stammverzeichnis Ihres Anwendungsprojekts bereitstellen, wird diese von Azure-Websites verwendet.

    Ist der Eintrag nicht vorhanden oder auf den Wert `true` festgelegt, sollten Sie eine **web.config** im Stammverzeichnis Ihrer Node.js-Anwendung erstellen und den Wert `false` angeben. Zur Referenz folgt eine **web.config**-Standarddatei für eine Anwendung, die **app.js** als Einstiegspunkt verwendet.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js site to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js site entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    > [WACOM.NOTE] Wenn Ihre Anwendung einen anderen Einstiegspunkt als **app.js** verwendet, müssen Sie alle Vorkommen von **app.js** durch den korrekten Einstiegspunkt ersetzen. So könnten Sie **app.js** durch **server.js** ersetzen.

## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie Sie eine Chatanwendung erstellen, die auf einer Azure-Website gehostet wird. Sie können diese Anwendung auch als Azure-Cloud-Dienst hosten. Die dafür erforderlichen Schritte finden Sie unter [Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einem Azure-Cloud-Dienst][Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einem Azure-Cloud-Dienst].

  []: http://socket.io/
  [Erstellen einer Node.js-Chatanwendung mit Socket.IO in einem Azure-Cloud-Dienst]: http://www.windowsazure.com/de-de/develop/nodejs/tutorials/app-using-socketio/
  [Socket.IO GitHub-Repository]: https://github.com/Automattic/socket.io
  [archivierte ZIP- oder GZ-Version]: https://github.com/Automattic/socket.io/releases
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A7171371E
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [websockets]: ./media/web-sites-nodejs-chat-app-socketio/websockets.png
  [Azure SDK für Node.js GitHub-Repository]: https://github.com/WindowsAzure/azure-sdk-for-node
  [socket.io-redis]: https://github.com/automattic/socket.io-redis
  [Instance Affinity in Azure Web Sites]: http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
  [Einen Cache erstellen in Azure Redis Cache]: http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409
  [Preisdetails für Websites]: /de-de/pricing/details/web-sites/
  [Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einem Azure-Cloud-Dienst]: /de-de/develop/nodejs/tutorials/app-using-socketio/
