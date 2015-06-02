<properties 
	pageTitle="Erstellen und Bereitstellen einer Node.js-Web-App für Azure mithilfe von WebMatrix" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von WebMatrix eine Node.js-Anwendung erstellen und in Azure App Service-Web-Apps bereitstellen." 
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
	ms.date="04/23/2015" 
	ms.author="mwasson"/>


# Erstellen und Bereitstellen einer Node.js-Web-App für Azure mithilfe von WebMatrix

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von WebMatrix eine Node.js-Anwendung erstellen und in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps bereitstellen. WebMatrix ist ein kostenloses Webentwicklungstool von Microsoft, das alles enthält, was Sie für die Entwicklung von Web-Apps benötigen. WebMatrix umfasst zahlreiche Features, die die Verwendung von Node.js einfach gestalten, einschließlich Codefertigstellung, vorbereitete Vorlagen und Editorunterstützung für Jade, LESS und CoffeeScript. Erfahren Sie mehr über [WebMatrix für Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409).

Nachdem Sie dieses Lernprogramm durchgearbeitet haben, verfügen Sie über eine Node.js-Web-App, die in Azure App Service ausgeführt wird.
 
Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![Node-Website auf Azure][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Anmelden bei Azure

Führen Sie diese Schritte aus, um eine Web-App in Azure App Service zu erstellen.

1. Starten Sie WebMatrix.
2. Wenn Sie WebMatrix zum ersten Mal verwenden, werden Sie aufgefordert, sich bei Azure anzumelden. Klicken Sie sonst auf die Schaltfläche **Anmelden**, und wählen Sie **Konto hinzufügen**. Melden Sie sich mit Ihrem Microsoft-Konto an.

	![Konto hinzufügen][addaccount]

3. Wenn Sie sich für ein Azure-Konto angemeldet haben, können Sie sich mit Ihrem Microsoft-Konto anmelden:

	![Anmelden bei Azure][signin]


## Erstellen einer Site für Azure mit einer integrierten Vorlage

1. Klicken Sie im Startbildschirm auf die Schaltfläche **Neu**, und wählen Sie **Vorlagenkatalog**, um eine neue Site aus dem Vorlagenkatalog zu erstellen:

	![Neue Site aus Vorlagenkatalog][sitefromtemplate]

2. Wählen Sie im Dialogfeld **Website aus Vorlage** die Option **Node** aus, und wählen Sie dann **Express-Website**. Klicken Sie anschließend auf **Next**. Wenn bestimmte Voraussetzungen für die Vorlage **Express-Website** fehlen, werden Sie aufgefordert, diese zu installieren.

	![Express-Vorlage auswählen][webmatrix-templates]

3. Wenn Sie bei Azure angemeldet sind, können Sie nun eine Azure Service-Web-App für eine lokale Website erstellen. Wählen Sie einen eindeutigen Namen und das Rechenzentrum aus, in dem die App Service-Web-App erstellt werden soll:

	![Site auf Azure erstellen][nodesitefromtemplateazure]
	
4. Nachdem WebMatrix die lokale Website und die App Service-Web-App erstellt hat, wird die WebMatrix IDE angezeigt.

	![WebMatrix-IDE][webmatrix-ide]

## Veröffentlichen der Anwendung in Azure

1. Klicken Sie in WebMatrix im Menüband **Startseite** auf **Veröffentlichen**, um das Dialogfeld **Vorschau veröffentlichen** für die Website aufzurufen.

	![Vorschau veröffentlichen][webmatrix-node-publishpreview]

2. Klicken Sie auf **Weiter**. Wenn die Veröffentlichung abgeschlossen ist, wird die URL für die App Service-Web-App unten in der WebMatrix-IDE angezeigt.

	![Veröffentlichung abgeschlossen][webmatrix-publish-complete]

3. Klicken Sie auf den Link, um die App Service-Web-App in Ihrem Browser zu öffnen.

	![Express-Web-App][webmatrix-node-express-site]

## Ändern und erneutes Veröffentlichen der Anwendung

Sie können die Anwendung auf einfache Weise ändern und neu veröffentlichen. Hier nehmen Sie eine einfache Änderung an der Überschrift in der **index.jade**-Datei vor und veröffentlichen die Anwendung dann neu.

1. Wählen Sie in WebMatrix die Option **Dateien**, und erweitern Sie dann den Ordner **Ansichten** folder. Öffnen Sie die Datei **index.php**, indem Sie darauf doppelklicken.

	![WebMatrix-Ansicht von index.jade][webmatrix-modify-index]

2. Ändern Sie die zweite Zeile wie folgt:

		p Welcome to #{title} with WebMatrix on Azure!

3. Speichern Sie die Änderungen, und klicken Sie dann auf das Symbol "Veröffentlichen". Klicken Sie abschließend im Dialogfeld **Vorschau veröffentlichen** auf **Weiter**, und warten Sie, bis das Update veröffentlicht wurde.

	![Vorschau veröffentlichen][webmatrix-republish]

4. Wenn die Veröffentlichung abgeschlossen ist, rufen Sie die aktualisierte App Service-Web-App über den zurückgegebenen Link auf.

	![Azure-Knoten-Web-App][webmatrix-node-completed]

## Nächste Schritte

Weitere Informationen über die Versionen von Node.js, die im Lieferumfang von Azure enthalten sind, und über die Angabe der Version, die mit Ihrer Anwendung verwendet werden soll, finden Sie unter [Festlegen einer Node.js-Version in einer Azure-Anwendung](nodejs-specify-node-version-azure-apps.md).

Wenn nach der Bereitstellung für Azure Probleme mit der Anwendung auftreten, finden Sie unter [Debuggen einer Node.js-Web-App in Azure App Service](web-sites-nodejs-debug.md) Informationen zur Problemdiagnose.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png

<!--HONumber=54-->