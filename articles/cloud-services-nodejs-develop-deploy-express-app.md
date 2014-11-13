<properties urlDisplayName="Web App with Express" pageTitle="Webanwendung mit Express (Node.js) &ndash; Azure-Lernprogramm" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="Dieses Lernprogramm baut auf dem Lernprogramm zu Cloud-Diensten auf und zeigt, wie das Express-Modul verwendet wird." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Erstellen einer Node.js-Webanwendung mit Express in einem Azure-Cloud-Dienst" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />

# Erstellen einer Node.js-Webanwendung mit Express in einem Azure-Cloud-Dienst

Die Core Runtime von Node.js umfasst eine Reihe elementarer Funktionen.
Entwickler verwenden bei der Entwicklung einer
Node.js-Anwendung häufig Module von Drittanbietern, um zusätzliche Funktionen bereitzustellen. In diesem
Lernprogramm erstellen Sie eine neue Anwendung mit dem Modul [Express][Express], das ein MVC-Framework zum Erstellen von Node.js-Webanwendungen bereitstellt.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Webbrowser, in dem 'Welcome to Express in Azure' angezeigt wird][Webbrowser, in dem 'Welcome to Express in Azure' angezeigt wird]

## Erstellen eines Cloud-Dienst-Projekts

Führen Sie die folgenden Schritte aus, um ein neues Cloud-Dienst-Projekt namens 'expressapp' zu erstellen:

1.  Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Azure PowerShell**. Klicken Sie dann mit der rechten Maustaste auf **Azure PowerShell**, und wählen Sie **Als Administrator ausführen**.

    ![Azure PowerShell-Symbol][Azure PowerShell-Symbol]

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Ändern Sie das Verzeichnis auf **c:\\node**, und geben Sie die folgenden Befehle ein, um eine neue Projektmappe mit dem Namen **expressapp** und eine Webrolle mit dem Namen **WebRole1** zu erstellen:

        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

    > [WACOM.NOTE] **Add-AzureNodeWebRole** verwendet standardmäßig eine ältere Version von Node.js. Die obige Anweisung **Set-AzureServiceProjectRole** weist Azure an, die Version 0.10.21 von Node zu verwenden.

## Installieren von Express

1.  Installieren Sie den Express-Generator mit dem folgenden Befehl:

        PS C:\node\expressapp> npm install express-generator -g

    Die Ausgabe des Befehls npm sollte ungefähr wie folgt aussehen.

    ![Windows PowerShell zeigt Ausgabe des Befehls npm install express an.][Windows PowerShell zeigt Ausgabe des Befehls npm install express an.]

2.  Ändern Sie das Verzeichnis in **WebRole1**, und erzeugen Sie mit dem Befehl express eine neue Anwendung:

        PS C:\node\expressapp\WebRole1> express

    Sie werden aufgefordert, die zuvor erstellte Anwendung zu überschreiben. Geben Sie **y** oder **yes** ein, um fortzufahren. Express erzeugt die Datei app.js und eine Ordnerstruktur zum Erstellen der Anwendung.

    ![Ausgabe des Befehls express][Ausgabe des Befehls express]

3.  Geben Sie den folgenden Befehl ein, um weitere in der Datei "package.json"
    definierte Abhängigkeiten zu installieren:

        PS C:\node\expressapp\WebRole1> npm install

    ![Ausgabe des Befehls npm install][Ausgabe des Befehls npm install]

4.  Kopieren Sie mit folgendem Befehl die Datei **bin/www** nach **server.js**. Damit kann der Cloud-Dienst den Einstiegspunkt für diese Anwendung finden.

        PS C:\node\expressapp\WebRole1> copy bin/www server.js

    Nach Ausführung dieses Befehls müsste die Datei **server.js** im Verzeichnis "WebRole1" vorliegen.

5.  Entfernen Sie in **server.js** eines der '.'-Zeichen aus der folgenden Zeile.

        var app = require('../app');

    Nach dieser Änderung müsste die Zeile wie folgt aussehen.

        var app = require('./app');

    Diese Änderung ist erforderlich, da die Datei (ehemals **bin/www**,) in dasselbe Verzeichnis wie die benötigte App-Datei verschoben wurde. Speichern Sie die Datei **server.js** anschließend.

6.  Führen Sie die Anwendung mit dem folgenden Befehl im Windows
    Azure-Emulator aus:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

    ![Webseite mit 'Welcome to Express'][Webseite mit 'Welcome to Express']

## Ändern der Ansicht

Ändern Sie nun die Ansicht, um die Meldung "Welcome to Express in
Azure" anzuzeigen.

1.  Geben Sie den folgenden Befehl ein, um die Datei index.jade zu öffnen:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![Inhalt der Datei index.jade][Inhalt der Datei index.jade]

    Jade ist das Standard-Ansichtsmodul, das von Express-Anwendungen verwendet wird. Weitere Informationen
    über das Ansichtsmodul Jade finden Sie unter [][]<http://jade-lang.com></a>.

2.  Ändern Sie die letzte Textzeile, indem Sie **in Azure** anhängen.

    ![Die Datei index.jade; die letzte Zeile lautet: p Welcome to \#{title} in Azure][Die Datei index.jade; die letzte Zeile lautet: p Welcome to \#{title} in Azure]

3.  Speichern Sie die Datei, und beenden Sie den Editor.

4.  Wenn Sie den Browser aktualisieren, werden die Änderungen angezeigt.

    ![Ein Browserfenster, die Seite enthält 'Welcome to Express in Azure'][Ein Browserfenster, die Seite enthält 'Welcome to Express in Azure']

Stoppen Sie den Emulator nach dem Testen der Anwendung mit dem Cmdlet **Stop-AzureEmulator**.

## Veröffentlichen der Anwendung in Azure

Verwenden Sie im Fenster von Azure PowerShell das Cmdlet **Publish-AzureServiceProject**, um die Anwendung in einem Cloud-Dienst bereitzustellen.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Sobald die Bereitstellung abgeschlossen ist, wird der Browser geöffnet und die Webseite angezeigt.

![Webbrowser mit der Express-Seite. Die URL zeigt an, dass die Seite jetzt auf Azure gehostet wird.][Webbrowser, in dem 'Welcome to Express in Azure' angezeigt wird]

  [Express]: http://expressjs.com/
  [Webbrowser, in dem 'Welcome to Express in Azure' angezeigt wird]: ./media/cloud-services-nodejs-develop-deploy-express-app/node36.png
  [Azure PowerShell-Symbol]: ./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png
  [Windows PowerShell zeigt Ausgabe des Befehls npm install express an.]: ./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png
  [Ausgabe des Befehls express]: ./media/cloud-services-nodejs-develop-deploy-express-app/node23.png
  [Ausgabe des Befehls npm install]: ./media/cloud-services-nodejs-develop-deploy-express-app/node26.png
  [Webseite mit 'Welcome to Express']: ./media/cloud-services-nodejs-develop-deploy-express-app/node28.png
  [Inhalt der Datei index.jade]: ./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png
  []: http://jade-lang.com
  [Die Datei index.jade; die letzte Zeile lautet: p Welcome to \#{title} in Azure]: ./media/cloud-services-nodejs-develop-deploy-express-app/node31.png
  [Ein Browserfenster, die Seite enthält 'Welcome to Express in Azure']: ./media/cloud-services-nodejs-develop-deploy-express-app/node32.png
