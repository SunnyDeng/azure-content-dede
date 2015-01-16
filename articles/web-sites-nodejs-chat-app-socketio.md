<properties urlDisplayName="Website Using Socket.IO" pageTitle="Node.js-Website mithilfe von Socket.io - Azure-Lernprogramm" metaKeywords="Azure Node.js socket.io-Lernprogramm, Azure Node.js socket.io, Azure Node.js-Lernprogramm" description="In diesem Lernprogramm wird die Verwendung von socket.io auf einer in Azure gehosteten Node.js-Website gezeigt." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />




#Erstellen einer Node.js-Chatanwendung mit Socket.IO auf einer Azure-Website

Socket.IO ermöglicht die Echtzeitkommunikation zwischen Ihrem Node.js-Server und Clients über WebSockets. Außerdem unterstützt es Fallback auf andere Transportmechanismen (wie Long Polling), die mit älteren Browsern funktionieren. In diesem Lernprogramm wird das Hosten einer Socket.IO-basierten Chatanwendung als Azure-Website erläutert. Weitere Informationen über Socket.IO finden Sie unter [http://socket.io/][socketio].

> [WACOM.NOTE] Die Verfahren dieser Aufgabe gelten für Azure-Websites; für Cloud-Dienste informieren Sie sich unter <a href="http://www.windowsazure.com/en-us/develop/nodejs/tutorials/app-using-socketio/">Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einem Azure-Cloud-Dienst</a>.


## <a id="Download"></a>Herunterladen des Chat-Beispiels

Für dieses Projekt verwenden wir das Chat-Beispiel aus dem [Socket.IO
GitHub-Repository]. Führen Sie die folgenden Schritte aus, um das Beispiel herunterzuladen,
und fügen Sie es dem zuvor erstellen Projekt hinzu.

1.  Laden Sie eine [archivierte ZIP- oder GZ-Version][release] des Socket.IO-Projekts herunter (für dieses Dokument wurde Version 1.0.6 verwendet).


3.  Extrahieren Sie das Archiv und kopieren Sie das Verzeichnis **examples\\chat**
    an einen neuen Speicherort. Beispiel: 
    **\\node\\chat**.

## <a id="Modify"></a>Ändern von App.js und Installieren von Modulen

Bevor Sie die Anwendung in Azure bereitstellen, müssen Sie
einige kleineren Änderungen vornehmen.

1.  Benennen Sie die Datei **index.js** in **app.js** um. Damit kann Azure erkennen, dass es sich hier um eine Node.js-Anwendung handelt.

1.  Öffnen Sie die Datei **app.js** im Editor oder in einem anderen Texteditor.

2.  Suchen Sie den Abschnitt **Module dependencies** am Anfang der Datei "app.js", und ändern Sie die Zeile "var io = require('../..')(server);" zu "var io = require('socket.io')(server);", wie unten dargestellt:

		var express = require('express');
		var app = express();
		var server = require('http').createServer(app);
		// var io = require('../..')(server);
		var io = require('socket.io')(server);
		var port = process.env.PORT || 3000;


Führen Sie die folgenden Schritte aus, um nach dem Speichern der Änderungen in "app.js"
die erforderlichen Module zu installieren:

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **\\node\\chat**,und verwenden Sie den folgenden Befehl, um die von dieser Anwendung benötigten Module zu installieren:

        npm install

    Damit werden die Module installiert, die in der Datei "package.json" aufgeführt sind. Nach
    Abschluss des Befehls sollten Sie eine Ausgabe ähnlich der folgenden
    erhalten:

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

2.  Da dieses Beispiel ursprünglich ein Teil des Socket.IO GitHub-
    Repositorys war und direkt die Socket.IO-Bibliothek über den
    relativen Pfad referenzierte, wurde Socket.IO nicht in der Datei
    "package.json" referenziert. Daher muss sie mit dem folgenden Befehl installiert werden:

        npm install socket.io@1.0.6 -save

	> [WACOM.NOTE] Obwohl die Vorgehensweise in diesem Artikel auch mit neueren Versionen von Socket.IO funktionieren kann, erfolgte der Testdurchlauf mit Version 1.0.6.

## <a id="Publish"></a>Erstellen einer Azure-Website

Befolgen Sie diese Schritte, um eine Azure-Website zu erstellen und dann die Git-Veröffentlichung sowie die WebSocket-Unterstützung für die Website zu aktivieren.

> [WACOM.NOTE] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten dazu finden Sie hier:<a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure Free Trial</a>.

1. From the command-line, change directories to the **\\node\chat** directory and use the following command to create a new Azure Website and enable a Git repository for the website and the local directory. This will also create a Git remote named 'azure'.

		azure site create mysitename --git

	You must replace 'mysitename' with a unique name for your website.

2. Commit the existing files to the local repository by using the following commands:

		git add .
		git commit -m "Initial commit"

3. Push the files to the Azure Website repository with the following command:

		git push azure master

	You will receive status messages as modules are imported on the server. Once this process has completed, the application will be hosted on your Azure Website.

 	> [WACOM.NOTE] During module installation, you may notice errors that 'The imported project ... was not found'. These can safely be ignored.

4. Socket.IO uses WebSockets, which are not enabled by default on Azure. To enable web sockets, use the following command:

		azure site set -w

	If prompted, enter the name of the website.

	>[WACOM.NOTE]
	>The 'azure site set -w' command will only work with version 0.7.4 or higher of the Azure Cross-Platform Command-Line Interface. You can also enable WebSocket support using the Azure Management Portal.
	>
	>To enable WebSockets using the [Azure Management Portal](https://manage.windowsazure.com), select the Configure page for your website, select 'ON' for the Web Sockets entry, and then click Save.
	>	
	>![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)
	
5. To view the website on Azure, use the following command to launch your web browser and navigate to the hosted website:

		azure site browse

Your application is now running on Azure, and can relay chat
messages between different clients using Socket.IO.

> [WACOM.NOTE] For simplicity, this sample is limited to chatting between users connected to the same instance. This means that if the cloud service creates two worker role instances, users will only be able to chat with others connected to the same worker role instance. To scale the application to work with multiple role instances, you could use a technology like Service Bus to share the Socket.IO store state across instances. For examples, see the Service Bus Queues and Topics usage samples in the <a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK for Node.js GitHub repository</a>.

##Scale out

Socket.IO applications can be scaled out by using an __adapter__ to distribute messages and events between multiple application instances. While there are several adapters available, the [socket.io-redis](https://github.com/automattic/socket.io-redis) adapter can be easily used with the Azure Redis Cache feature.

> [WACOM.NOTE] An additional requirement for scaling out a Socket.IO solution is support for sticky sessions. Sticky sessions are enabled by default for Azure Websites through Azure Request Routing. For more information, see [Instance Affinity in Azure Web Sites](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)

###Create a Redis cache

Perform the steps in [Create a cache in Azure Redis Cache](http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409) to create a new cache.

> [WACOM.NOTE] Save the __Host name__ and __Primary key__ for your cache, as these will be needed in the next steps.

###Add the redis and socket.io-redis modules

1. From a command-line, change to the __\\node\\chat__ directory and use the following command.

		npm install socket.io-redis@0.1.3 redis@0.11.0 --save

	> [WACOM.NOTE] The versions specified in this command are the versions used when testing this article.

2. Modify the __app.js__ file to add the following lines immediately after `var io = require('socket.io')(server);`

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		
		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));


	Replace __redishostname__ and __rediskey__ with the host name and key for your Redis cache.

	This will create a publish and subscribe client to the Redis cache created previously. The clients are then used with the adapter to configure Socket.IO to use the Redis cache for passing messages and events between instances of your application

	> [WACOM.NOTE] While the __socket.io-redis__ adapter can communicate directly to Redis, the current version (as of 7/14/2014) does not support the authentication required by Azure Redis cache. So the initial connection is created using the __redis__ module, then the client is passed to the __socket.io-redis__ adapter.
	> 
	> While Azure Redis Cache supports secure connections using port 6380, the modules used in this example do not support secure connections as of 7/14/2014. The above code uses the default, unsecure port of 6380.

3. Save the modified __app.js__

###Commit changes and redeploy

From the command-line in the __\\node\\chat__ directory, use the following commands to commit changes and redeploy the application.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Once the changes have been pushed to the server, you can scale your site across multiple instances by using the following command.

	azure site scale instances --instances #

Where __#__ is the number of instances to create. 

You can connect to your website from multiple browsers or computers to verify that messages are correctly sent to all clients.

##<a id="tshooting"></a>Troubleshooting

###Connection limits

Azure Websites is available in multiple SKUs, which determine the resources available to your site. This includes the number of allowed WebSocket connections. For more information, see the [Web Sites Pricing page][pricing].

###Messages aren't being sent using WebSockets

If client browsers keep falling back to long polling instead of using WebSockets, it may be because of one of the following.

* **Try limiting the transport to just WebSockets**

	In order for Socket.IO to use WebSockets as the messaging transport, both the server and client must support WebSockets. If one or the other does not, Socket.IO will negotiate another transport, such as long polling. The default list of transports used by Socket.IO is ` websocket, htmlfile, xhr-polling, jsonp-polling`. You can force it to only use WebSockets by adding the following code to the **app.js** file, after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [WACOM.NOTE] Note that older browsers that do not support WebSockets will not be able to connect to the site while the above code is active, as it restricts communication to WebSockets only.

* **Use SSL**

	WebSockets relies on some lesser used HTTP headers, such as the **Upgrade** header. Some intermediate network devices, such as web proxies, may remove these headers. To avoid this problem, you can establish the WebSocket connection over SSL.

	An easy way to accomplish this is to configure Socket.IO to `match origin protocol`. This instructs Socket.IO to secure WebSockets communication the same as the originating HTTP/HTTPS request for the web page. If a browser uses an HTTPS URL to visit your website, subsequent WebSocket communications through Socket.IO will be secured over SSL.

	To modify this example to enable this configuration, add the following code to the **app.js** file after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Verify web.config settings**

	Azure Websites that host Node.js applications use the **web.config** file to route incoming requests to the Node.js application. For WebSockets to function correctly with Node.js applications, the **web.config** must contain the following entry.

		<webSocket enabled="false"/>

	Damit wird das IIS-WebSockets-Modul deaktiviert, das eine eigene Implementierung von WebSockets enthält und mit Node.js-spezifischen WebSocket-Modulen wie Socket.IO in Konflikt gerät. Wenn diese Zeile fehlt oder auf "true" festgelegt ist, könnte dies der Grund dafür sein, dass der WebSocket-Transport bei Ihrer Anwendung nicht funktioniert.

	Normalerweise enthalten Node.js-Anwendungen keine **web.config**-Datei, sodass Azure-Websites eine solche Datei für Node.js-Anwendungen bei deren Bereitstellung automatisch generiert. Da diese Datei automatisch auf dem Server generiert wird, müssen Sie die FTP- oder FTPS-URL für Ihre Website zur Anzeige der Datei verwenden. Die FTP- und FTPS-URLs für Ihre Website können Sie im Azure-Verwaltungsportal einsehen, indem Sie Ihre Website und dann den Link **Dashboard** auswählen. Die URLs werden im Bereich **Auf einen Blick** angezeigt.

	> [WACOM.NOTE] Die Datei **web.config** wird von Azure-Websites nur generiert, wenn Ihre Anwendung keine bereitstellt. Wenn Sie eine **web.config**-Datei im Stammverzeichnis Ihres Anwendungsprojekts bereitstellen, wird diese von Azure-Websites verwendet.

	Ist der Eintrag nicht vorhanden oder auf den Wert "true" festgelegt, sollten Sie eine **web.config**-Datei im Stammverzeichnis Ihrer Node.js-Anwendung erstellen und den Wert "false" angeben.  Zur Referenz folgt eine **web.config**-Standarddatei für eine Anwendung, die **app.js** als Einstiegspunkt verwendet.

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

##Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie Sie eine Chatanwendung erstellen, die auf einer Azure-Website gehostet wird. Sie können diese Anwendung auch als Azure-Cloud-Dienst hosten. Die dafür erforderlichen Schritte finden Sie unter [Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einem Azure-Cloud-Dienst][cloudservice]..

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Socket.IO GitHub repository]: https://github.com/Automattic/socket.io
[release]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /en-us/develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[pricing]: /en-us/pricing/details/web-sites/
