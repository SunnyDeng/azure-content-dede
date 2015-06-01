<properties 
	pageTitle="Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in Azure App Service" 
	description="Ein Lernprogramm, das die Verwendung von socket.io in einer node.js-Web-App zeigt, die auf Azure gehostet wird." 
	services="app-service\web" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>




# Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in Azure App Service

Socket.IO ermöglicht die Echtzeitkommunikation zwischen Ihrem Node.js-Server und Clients über WebSockets. Außerdem unterstützt es Fallback auf andere Transportmechanismen (wie Long Polling), die mit älteren Browsern funktionieren. In diesem Lernprogramm werden das Hosten einer Socket.IO-basierten Chat-Anwendung als Azure-Web-App erläutert und die [Skalierung](#scale-out) der Anwendung mit [Azure-Redis-Cache](http://azure.microsoft.com/documentation/services/cache) gezeigt. Weitere Informationen über Socket.IO finden Sie unter [http://socket.io/][socketio].

> [AZURE.NOTE] Die Verfahren in dieser Aufgabe gelten für [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Die Verfahren für Cloud-Dienste finden Sie unter <a href="http://www.windowsazure.com/develop/nodejs/tutorials/app-using-socketio/">Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einem Azure-Cloud-Dienst</a>.


## Herunterladen des Chatbeispiels

Für dieses Projekt verwenden wir das Chatbeispiel aus dem [Socket.IO
GitHub-Repository]. Führen Sie die folgenden Schritte aus, um das Beispiel herunterzuladen,
und fügen Sie es dem zuvor erstellen Projekt hinzu.

1.  Laden Sie eine [archivierte ZIP- oder GZ-Version][Version] des Socket.IO-Projekts herunter (für dieses Dokument wurde Version 1.3.5 verwendet).


3.  Extrahieren Sie das Archiv, und kopieren Sie das Verzeichnis **examples\\chat** in ein neues Verzeichnis. Beispiel: **\\node\\chat**.

## Ändern von "app.js" und Installieren von Modulen

1.  Benennen Sie die Datei **index.js** in **app.js** um. Dadurch kann Azure erkennen, dass es sich hier um eine Node.js-Anwendung handelt.

1.  Öffnen Sie die Datei **app.js** in einem Texteditor. Ändern Sie die Zeile "var io = require('../..')(server);" wie folgt:

		var express = require('express');
		var app = express();
		var server = require('http').createServer(app);
		// var io = require('../..')(server);
        // New:
		var io = require('socket.io')(server);
		var port = process.env.PORT || 3000;


3. Öffnen Sie die Datei **package.json**, und fügen Sie einen Verweis auf "socket.io" unter `dependencies` hinzu, wie unten dargestellt:

        "dependencies": {
		  "express": "3.4.8",
		  "socket.io": "1.3.5"
		}

4. Wechseln Sie über die Befehlszeile in das Verzeichnis **\\node\\chat**, und verwenden Sie npm, um die von dieser Anwendung benötigten Module zu installieren:

        npm install

    Dadurch werden die Module in einen Unterordner mit dem Namen **node_modules** installiert.

## Erstellen einer Azure-Web-App

Befolgen Sie diese Schritte, um eine Azure-Web-App zu erstellen und dann die Git-Veröffentlichung sowie die WebSocket-Unterstützung für die Web-App zu aktivieren.

> [AZURE.NOTE] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Kostenlose Azure-Testversion</a>.

1. Installieren Sie die plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli), und stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her. Weitere Informationen finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](xplat-cli)

2. Wenn Sie zum ersten Mal ein Repository in Azure einrichten, müssen Sie Anmeldeinformationen erstellen. Geben Sie in xplat-cli den folgenden Befehl ein.

		azure site deployment user set [username] [password] 


3. Wechseln Sie zum Verzeichnis **\\node\\chat**, und erstellen Sie mit dem folgenden Befehl eine neue Azure-Web-App und ein lokales Git-Repository. Mit dem Befehl wird auch ein Git-Remoterepository namens 'azure' erstellt.

		azure site create mysitename --git

	Sie müssen 'mysitename' durch einen eindeutigen Namen für Ihre Web-App ersetzen.

2. Übergeben Sie die vorhandenen Dateien mit folgenden Befehlen an das lokale Repository:

		git add .
		git commit -m "Initial commit"

3. Übertragen Sie die Dateien mit folgendem Push-Befehl an das Azure-Web-App-Repository:

		git push azure master

	Sie werden Statusnachrichten erhalten, wenn Module zum Server importiert werden. Sobald der Vorgang abgeschlossen ist, wird die Anwendung auf Ihrer Azure-Web-App gehostet.

 	> [AZURE.NOTE] Während der Modulinstallation werden eventuell Fehler angezeigt wie 'Das importierte Projekt ... wurde nicht gefunden'. Diese können ignoriert werden.

4. Socket.IO verwendet WebSockets, die nicht standardmäßig auf Azure aktiviert sind. Verwenden Sie folgenden Befehl, um Web Sockets zu aktivieren:

		azure site set -w

	Geben Sie den Namen der Web-App ein, wenn Sie dazu aufgefordert werden.

	>[AZURE.NOTE]
	>Der Befehl 'azure site set -w' funktioniert nur bei Version 0.7.4 oder höher der plattformübergreifenden Azure-Befehlszeilenschnittstelle. Sie können die Unterstützung von WebSocket im [Azure-Portal](https://portal.azure.com) aktivieren.
	>
	>Um WebSockets mithilfe des Azure-Portals zu aktivieren, klicken Sie auf dem Web-Apps-Blatt auf die Web-App, und klicken Sie auf **Alle Einstellungen** > **Anwendungseinstellungen**. Klicken Sie unter **WebSockets** auf **Ein**. Klicken Sie dann auf **Speichern**.
	
5. Um die Website in Azure anzuzeigen, starten Sie den Webbrowser mit folgendem Befehl, und navigieren Sie zur gehosteten Web-App:

		azure site browse

Ihre App wird jetzt in Azure ausgeführt und kann Chat-Nachrichten zwischen verschiedenen Clients mithilfe von Socket.IO vermitteln.

##Horizontales Skalieren

Socket.IO-Anwendungen können über einen __Adapter__ horizontal skaliert werden, um Nachrichten und Ereignisse unter mehreren Anwendungsinstanzen zu verteilen. Es stehen zwar mehrere Adapter zur Verfügung, allerdings empfiehlt sich der Adapter [socket.io-redis](https://github.com/automattic/socket.io-redis), da sich dieser einfach mit der Azure-Redis-Cache-Funktion verwenden lässt.

> [AZURE.NOTE] Eine weitere Anforderung für das horizontale Skalieren einer Socket.IO-Lösung ist die Unterstützung persistenter Sitzungen. Persistente Sitzungen sind für Azure-Web-Apps standardmäßig über das Azure-Anforderungsrouting aktiviert. Weitere Informationen finden Sie unter [Instanzaffinität in Azure-Websites](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/).

###Erstellen eines Redis-Caches

Führen Sie die Schritte unter [Erstellen eines Caches in Azure-Redis-Cache](http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409) aus, um einen neuen Cache zu erstellen.

> [AZURE.NOTE] Notieren Sie sich den __Hostnamen__ und den __Primärschlüssel__ für Ihren Cache, da Sie diese Informationen in den nächsten Schritten benötigen.

###Hinzufügen der Module "redis" und "socket.io-redis"

1. Wechseln Sie über die Befehlszeile in das Verzeichnis __\\node\\chat__, und führen Sie folgenden Befehl aus:

		npm install socket.io-redis@0.1.4 redis@0.12.1 --save

	> [AZURE.NOTE] Die in diesem Befehl angegebenen Versionen sind die Versionen, die beim Testen dieses Artikels verwendet wurden.

2. Ändern Sie die Datei __app.js__ so, dass die folgenden Zeilen direkt nach "var i/o = require('socket.io')(server);" folgen.

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		
		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));

	Ersetzen Sie __redishostname__ und __rediskey__ durch den Hostnamen und den Schlüssel für Ihren Redis-Cache.

	Damit wird ein Veröffentlichungs-/Abonnementclient für den zuvor erstellten Redis-Cache erstellt. Die Clients werden dann mit dem Adapter verwendet, um Socket.IO für die Verwendung des Redis-Caches zum Übergeben von Nachrichten und Ereignissen zwischen Instanzen Ihrer Anwendung zu konfigurieren.

	> [AZURE.NOTE] Obwohl der Adapter __socket.io-redis__ direkt mit Redis kommunizieren kann, unterstützt die aktuelle Version die vom Azure-Redis-Cache benötigte Authentifizierung nicht. Daher wird die Erstverbindung über das Modul __redis__ hergestellt und der Client dann an den Adapter __socket.io-redis__ übergeben.
	> 
	> Der Azure-Redis-Cache unterstützt zwar sichere Verbindungen über Port 6380, die in diesem Beispiel verwendeten Module jedoch nicht (Stand 14.7.2014). Im obigen Code wird daher der unsichere Standardport 6380 verwendet.

3. Speichern Sie die geänderte Datei __app.js__.

###Übergeben der Änderungen und erneutes Bereitstellen

Verwenden Sie in der Befehlszeile im Verzeichnis __\\node\\chat__ die folgenden Befehle, um die Änderungen zu übergeben und die Anwendung erneut bereitzustellen.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Nachdem Sie Änderungen an den Server übertragen wurden, können Sie Ihre Website mit dem folgenden Befehl auf mehrere Instanzen skalieren.

	azure site scale instances --instances #

Hierbei steht __#__ für die Anzahl der zu erstellenden Instanzen. 

Sie können über mehrere Browser oder Computer eine Verbindung mit Ihrer Web-App herstellen, um sicherzustellen, dass Nachrichten korrekt an alle Clients gesendet werden.

## Problembehandlung

###Verbindungsbeschränkungen

Azure-Web-Apps sind in verschiedenen SKUs erhältlich. Diese bestimmen, welche Ressourcen für Ihre Website zur Verfügung stehen. Dazu gehört auch die Anzahl der zulässigen WebSocket-Verbindungen. Weitere Informationen finden Sie auf der Seite [Preisdetails für Web-Apps][pricing].

###Kein Nachrichtenversand über WebSockets

Wenn Clientbrowser immer wieder auf Long Polling zurückfallen und WebSockets nicht verwenden, versuchen Sie den Grund wie folgt zu finden.

* **Beschränken des Transports auf WebSockets**

	Damit WebSockets von Socket.IO als Nachrichtentransportmechanismus verwendet werden kann, müssen Server und Client WebSockets unterstützen. Ist dies bei einem der beiden nicht der Fall, handelt Socket.IO einen anderen Transportmechanismus wie Long Polling aus. Die Standardliste der von Socket.IO verwendeten Transporte lautet "WebSocket, HTML-Datei, XHR-Abrufe, jsonp-Abrufe". Sie können erzwingen, dass nur WebSockets verwendet wird, indem Sie in der Datei **app.js** den folgenden Code nach der Zeile mit ", nicknames = {};" einfügen.

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [AZURE.NOTE] Beachten Sie, dass ältere Browser ohne WebSockets-Unterstützung keine Verbindung mit der Website herstellen können, während der obige Code aktiv ist, da er die Kommunikation auf die alleinige Verwendung von WebSockets beschränkt.

* **Verwenden von SSL**

	WebSockets stützt sich auf weniger häufig verwendete HTTP-Header wie den **Upgrade**-Header. Bestimmte zwischengeschaltete Netzwerkgeräte wie Webproxys entfernen unter Umständen diese Header. Um dieses Problem zu vermeiden, können Sie die WebSocket-Verbindung über SSL einrichten.

	Dies lässt sich einfach erreichen, indem Sie  `match origin protocol` für Socket.IO konfigurieren. Damit weisen Sie Socket.IO an, die WebSockets-Kommunikation so zu sichern wie dies bei der ursprünglichen HTTP/HTTPS-Anforderung für die Webseite der Fall war. Wenn ein Browser über eine HTTPS-URL auf Ihre Website zugreift, wird die nachfolgende WebSocket-Kommunikation durch Socket.IO über SSL gesichert.

	Um das Beispiel für diese Konfiguration entsprechend zu ändern, fügen Sie den folgenden Code in die Datei **app.js** nach der Zeile mit ", nicknames = {};" ein.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Prüfen der Einstellungen in "web.config"**

	Azure-Web-Apps, die Node.js-Anwendungen hosten, leiten eingehende Anforderungen mithilfe der Datei **web.config** an die Node.js-Anwendung weiter. Damit WebSockets ordnungsgemäß mit Node.js-Anwendungen funktioniert, muss die Datei **web.config** folgenden Eintrag enthalten.

		<webSocket enabled="false"/>

	Dadurch wird das Modul "IIS WebSockets" deaktiviert, das eine eigene Implementierung von WebSockets aufweist und Konflikte mit Node.js- spezifischen WebSocket-Modulen wie z. B. Socket.IO verursacht. Wenn diese Zeile nicht vorhanden ist oder auf  `true` festgelegt ist, ist dies möglicherweise der Grund, warum der WebSocket-Transport für Ihre Anwendung nicht funktioniert.

	Normalerweise enthalten Node.js-Anwendungen keine Datei **web.config**, sodass Azure-Websites eine solche Datei für Node.js-Anwendungen bei deren Bereitstellung automatisch generieren. Da diese Datei automatisch auf dem Server generiert wird, müssen Sie die FTP- oder FTPS-URL für Ihre Website zur Anzeige der Datei verwenden. Die FTP- und FTPS-URLs für Ihre Website können Sie im Azure-Verwaltungsportal einsehen, indem Sie Ihre Website und dann den Link **Dashboard** auswählen. Die URLs werden im Bereich **Auf einen Blick** angezeigt.

	> [AZURE.NOTE] Die Datei **web.config** wird von Azure-Websites nur generiert, wenn Ihre Anwendung keine bereitstellt. Wenn Sie eine Datei **web.config** im Stammverzeichnis Ihres Anwendungsprojekts bereitstellen, wird diese von Azure-Web-Apps verwendet.

	Wenn der Eintrag nicht vorhanden oder auf den Wert  `true` festgelegt ist, sollten Sie eine Datei **web.config** im Stammverzeichnis Ihrer Node.js-Anwendung erstellen und den Wert  `false` angeben.  Zur Referenz folgt eine **web.config**-Standarddatei für eine Anwendung, die **app.js** als Einstiegspunkt verwendet.

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
		      <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
		      <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
		    </handlers>
		    <rewrite>
		      <rules>
		        <!-- Do not interfere with requests for node-inspector debugging -->
		        <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
		          <match url="^app.js/debug[/]?" />
		        </rule>
		
		        <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
		        <rule name="StaticContent">
		          <action type="Rewrite" url="public{REQUEST_URI}"/>
		        </rule>
		
		        <!-- All other URLs are mapped to the node.js web app entry point -->
		        <rule name="DynamicContent">
		          <conditions>
		            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
		          </conditions>
		          <action type="Rewrite" url="app.js"/>
		        </rule>
		      </rules>
		    </rewrite>
		    <!--
		      Sie können mit den folgenden Optionen steuern, wie Knoten innerhalb von IIS gehostet werden:
		        * watchedFiles: durch Semikolons getrennte Liste von Dateien, in der Änderungen zum Neustart des Servers überwacht werden
		        * node_env: wird als NODE_ENV-Umgebungsvariable an Node weitergegeben
		        * debuggingEnabled - steuert, ob der integrierte Debugger aktiviert ist
		
		      Eine vollständige Liste von Optionen finden Sie unter "https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config".
		    -->
		    <!--<iisnode watchedFiles="web.config;*.js"/>-->
		  </system.webServer>
		</configuration>

	> [AZURE.NOTE] Wenn Ihre Anwendung einen anderen Einstiegspunkt als **app.js** verwendet, müssen Sie alle Vorkommen von **app.js** durch den ordnungsgemäßen Einstiegspunkt ersetzen. Sie können beispielsweise **app.js** durch **server.js** ersetzen.

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie eine Chat-Anwendung erstellt wird, die in einer Azure-Web-App gehostet wird. Sie können diese Anwendung auch als Azure-Cloud-Dienst hosten. Die dafür erforderlichen Schritte finden Sie unter [Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einem Azure-Cloud-Dienst][cloudservice].

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service and existing Azure services (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Reference for navigating the preview portal (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529715)

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Socket.IO GitHub repository]: https://github.com/Automattic/socket.io
[release]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[pricing]: /pricing/details/web-sites/

<!--HONumber=49-->