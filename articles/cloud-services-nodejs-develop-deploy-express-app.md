<properties 
	pageTitle="Webanwendung mit Express (Node.js) - Azure-Lernprogramm" 
	description="Dieses Lernprogramm baut auf dem Lernprogramm zu Cloud-Diensten auf und zeigt, wie das Express-Modul verwendet wird." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Erstellen einer Node.js-Webanwendung mit Express in einem Azure-Cloud-Dienst

Die Core Runtime von Node.js umfasst eine Reihe elementarer Funktionen.
Entwickler verwenden bei der Entwicklung einer Node.js-Anwendung häufig Module von Drittanbietern, die über zusätzliche Funktionen verfügen. In diesem Lernprogramm erstellen Sie eine neue Anwendung mit dem Modul [Express][], das ein MVC-Framework zum Erstellen von Node.js-Webanwendungen bereitstellt.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![A web browser displaying Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## Erstellen eines Cloud-Dienst-Projekts

Führen Sie die folgenden Schritte aus, um ein neues Cloud-Dienst-Projekt namens 'expressapp' zu erstellen:

1. Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Azure PowerShell**. Klicken Sie dann mit der rechten Maustaste auf **Azure PowerShell**, und wählen Sie **Als Administrator ausführen**.

	![Azure PowerShell icon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2. Ändern Sie das Verzeichnis in **c:\\node**, und geben Sie die folgenden Befehle ein, um eine neue Projektmappe mit dem Namen **expressapp** und eine Webrolle mit dem Namen **WebRole1** zu erstellen:

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node\expressapp> Add-AzureNodeWebRole
		PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

	> [AZURE.NOTE] Standardmäßig verwendet **Add-AzureNodeWebRole** eine ältere Version von "Node.js". Die obige **Set-AzureServiceProjectRole**-Anweisung weist Azure an, um v0.10.21 von Node zu verwenden. 

## Installieren von Express

1. Installieren Sie den Express-Generator mit dem folgenden Befehl:

		PS C:\node\expressapp> npm install express-generator -g

	Die Ausgabe des Befehls npm sollte ungefähr wie folgt aussehen. 

	![Windows PowerShell displaying the output of the npm install express command.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Ändern Sie das Verzeichnis in **WebRole1**, und erzeugen Sie mit dem Befehl "express" eine neue Anwendung:

        PS C:\node\expressapp\WebRole1> express

	Sie werden aufgefordert, die zuvor erstellte Anwendung zu überschreiben. Geben Sie **y** oder **yes** ein, um fortzufahren. Express erzeugt die Datei app.js und eine Ordnerstruktur zum Erstellen der Anwendung.

	![The output of the express command](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Geben Sie den folgenden Befehl ein, um weitere in der Datei package.json definierte Abhängigkeiten zu installieren.

        PS C:\node\expressapp\WebRole1> npm install

	![The output of the npm install command](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  Kopieren Sie mit folgendem Befehl die Datei **bin/www** nach **server.js**. Damit kann der Cloud-Dienst den Einstiegspunkt für diese Anwendung finden.

		PS C:\node\expressapp\WebRole1> copy bin/www server.js

	Nach Ausführung dieses Befehls müsste die Datei **server.js** im Verzeichnis "WebRole1" vorliegen.

7.  Entfernen Sie in **server.js** eines der "."-Zeichen aus der folgenden Zeile.

		var app = require('../app');

	Nach dieser Änderung müsste die Zeile wie folgt aussehen.

		var app = require('./app');

	Diese Änderung ist erforderlich, da die Datei (ehemals **bin/www**,) in dasselbe Verzeichnis wie die benötigte App-Datei verschoben wurde. Speichern Sie die Datei **server.js** anschließend.

8.  Verwenden Sie den folgenden Befehl, um die Anwendung im Azure-Emulator auszuführen:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![A web page containing welcome to express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Ändern der Ansicht

Ändern Sie nun die Ansicht, um die Meldung "Welcome to Express in
Azure" anzuzeigen.

1.  Geben Sie den folgenden Befehl ein, um die Datei index.jade zu öffnen:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![The contents of the index.jade file.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade ist das Standard-Ansichtsmodul, das von Express-Anwendungen verwendet wird. Weitere Informationen über das Ansichtsmodul Jade finden Sie unter [http://jade-lang.com][].

2.  Ändern Sie die letzte Textzeile, indem Sie **in Azure** anhängen.

	![The index.jade file, the last line reads: p Welcome to \#{title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Speichern Sie die Datei, und beenden Sie den Editor.

4.  Wenn Sie den Browser aktualisieren, werden die Änderungen angezeigt.

	![A browser window, the page contains Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Stoppen Sie den Emulator nach dem Testen der Anwendung mit dem Cmdlet **Stop-AzureEmulator**.

## Veröffentlichen der Anwendung in Azure

Verwenden Sie im Fenster von Azure PowerShell das Cmdlet **Publish-AzureServiceProject**, um die Anwendung in einem Cloud-Dienst bereitzustellen.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Sobald die Bereitstellung abgeschlossen ist, wird der Browser geöffnet und die Webseite angezeigt.

![A web browser displaying the Express page. The URL indicates it is now hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)


  [Node.js-Webanwendung]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com


<!--HONumber=45--> 
