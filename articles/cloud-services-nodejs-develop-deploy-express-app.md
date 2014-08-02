<properties  linkid="dev-nodejs-basic-web-app-with-express" urlDisplayName="Web App with Express" pageTitle="Web App with Express (Node.js) - Azure Tutorial" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="An tutorial that builds on the cloud service tutorial, and demonstrates how to use the Express module." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="" solutions="" manager="" editor="" />

# Erstellen einer Node.js-Webanwendung mit Express in einem Azure-Cloud-Dienst

Die Core Runtime von Node.js umfasst eine Reihe elementarer Funktionen. Entwickler verwenden bei der Entwicklung einer Node.js-Anwendung häufig Module von Drittanbietern, die über zusätzliche Funktionen verfügen. In diesem Lernprogramm erstellen Sie eine neue Anwendung mit dem Modul [Express][1], das ein MVC-Framework zum Erstellen von Node.js-Webanwendungen bereitstellt.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Webbrowser, in dem 'Welcome to Express in Azure' angezeigt
wird](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## Erstellen eines Cloud-Dienst-Projekts

Führen Sie die folgenden Schritte aus, um ein neues Cloud-Dienst-Projekt namens 'expressapp' zu erstellen:

1.  Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Azure PowerShell**. Klicken Sie dann mit der rechten Maustaste auf **Azure PowerShell**, und wählen Sie **Als Administrator ausführen**.
    
    ![Azure
    PowerShell-Symbol](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
    
    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Ändern Sie das Verzeichnis auf **c:\node**, und geben Sie die folgenden Befehle ein, um eine neue Projektmappe mit dem Namen **expressapp** und eine Webrolle mit dem Namen **WebRole1** zu erstellen:
    
         PS C:\node> New-AzureServiceProject expressapp
         PS C:\Node> Add-AzureNodeWebRole

## Installieren von Express

1.  Starten Sie das Modul Express mit dem folgenden Befehl:
    
         PS C:\node\expressapp> npm install express -g
    
    Die Ausgabe des Befehls npm sollte ungefähr wie folgt aussehen.
    
    ![Windows PowerShell zeigt Ausgabe des Befehls npm install express
    an.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2.  Ändern Sie das Verzeichnis in **WebRole1**, und erzeugen Sie mit dem Befehl express eine neue Anwendung:
    
         PS C:\node\expressapp\WebRole1> express
    
    Sie werden aufgefordert, die zuvor erstellte Anwendung zu überschreiben. Geben Sie **y** oder **yes** ein, um fortzufahren. Express erzeugt die Datei app.js und eine Ordnerstruktur zum Erstellen der Anwendung.
    
    ![Ausgabe des Befehls
    express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)

3.  Löschen Sie die Datei **server.js**, und benennen Sie die erzeugte Datei **app.js** in **server.js** um.
    
        PS C:\node\expressapp\WebRole1> del server.js
        PS C:\node\expressapp\WebRole1> ren app.js server.js

4.  Geben Sie den folgenden Befehl ein, um weitere in der Datei package.json definierte Abhängigkeiten zu installieren.
    
        PS C:\node\expressapp\WebRole1> npm install
    
    ![Ausgabe des Befehls npm
    install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

5.  Verwenden Sie den folgenden Befehl, um die Anwendung im Azure-Emulator auszuführen:
    
        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
    
    ![Webseite mit 'Welcome to
    Express'](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Ändern der Ansicht

Ändern Sie nun die Ansicht, um die Meldung 'Welcome to Express in Azure' anzuzeigen.

1.  Geben Sie den folgenden Befehl ein, um die Datei index.jade zu öffnen:
    
        PS C:\node\expressapp\WebRole1> notepad views/index.jade
    
    ![Inhalt der Datei
    index.jade](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
    
    Jade ist das Standard-Ansichtsmodul, das von Express-Anwendungen
    verwendet wird. Weitere Informationen über das Ansichtsmodul Jade
    finden Sie unter [http://jade-lang.com][2].

2.  Ändern Sie die letzte Textzeile, indem Sie **in Azure** anhängen.
    
    ![Die Datei index.jade; die letzte Zeile lautet: p Welcome to
    \#{title} in
    Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Speichern Sie die Datei, und beenden Sie den Editor.

4.  Wenn Sie den Browser aktualisieren, werden die Änderungen angezeigt.
    
    ![Ein Browserfenster, die Seite enthält 'Welcome to Express in
    Azure'](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Stoppen Sie den Emulator nach dem Testen der Anwendung mit dem Cmdlet **Stop-AzureEmulator**.

## Veröffentlichen der Anwendung in Azure

Verwenden Sie im Fenster von Azure PowerShell das Cmdlet **Publish-AzureServiceProject**, um die Anwendung in einem Cloud-Dienst bereitzustellen.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Sobald die Bereitstellung abgeschlossen ist, wird der Browser geöffnet und die Webseite angezeigt.

![Webbrowser mit der Express-Seite. Die URL zeigt an, dass die Seite
jetzt auf Azure gehostet wird.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)



[1]: http://expressjs.com/
[2]: http://jade-lang.com