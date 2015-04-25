<properties 
	pageTitle="Node.js-Website mit WebMatrix - Azure-Lernprogramm" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von WebMatrix eine Node.js-Anwendung für eine Azure-Website entwickeln und bereitstellen können." 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


# Erstellen einer Node.js-Website und Bereitstellen für Azure mit WebMatrix

In diesem Lernprogramm erfahren Sie, wie Sie WebMatrix zum Entwickeln und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website verwenden. WebMatrix ist ein kostenloses Webentwicklungstool von Microsoft, das alles enthält, was Sie für die Entwicklung von Websites benötigen. WebMatrix umfasst zahlreiche Features, die die Verwendung von Node.js einfach gestalten, einschließlich Codefertigstellung, vorbereitete Vorlagen und Editorunterstützung für Jade, LESS und CoffeeScript. Erfahren Sie mehr über [WebMatrix für Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409).

Nach Durchführung dieses Lernprogramms haben Sie eine in Azure ausgeführte Node.js-Website.
 
Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Azure node Web site][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Anmelden bei Azure

Führen Sie die folgenden Schritte aus, um eine Azure-Website zu erstellen:

> [AZURE.NOTE] Sie benötigen ein Azure-Konto, bei dem die Azure-Websites-Funktion aktiviert ist, um dieses Lernprogramm abzuschließen. <br /> Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A7171371E"%20target="_blank").
<br />

1. Starten Sie WebMatrix.
2. Wenn Sie WebMatrix zum ersten Mal verwenden, werden Sie aufgefordert, sich bei Azure anzumelden.  Klicken Sie sonst auf die Schaltfläche **Anmelden**, und wählen Sie **Konto hinzufügen**.  Wählen Sie die **Anmeldung** über Ihr Microsoft-Konto.

	![Add Account][addaccount]

3. Wenn Sie sich für ein Azure-Konto angemeldet haben, können Sie sich mit Ihrem Microsoft-Konto anmelden:

	![Sign into Azure][signin]	


## Erstellen einer Site für Azure mit einer integrierten Vorlage

1. Klicken Sie im Startbildschirm auf die Schaltfläche **Neu**, und wählen Sie **Vorlagenkatalog**, um eine neue Site aus dem Vorlagenkatalog zu erstellen:

	![New site from Template Gallery][sitefromtemplate]

2. Wählen Sie im Dialogfeld **Website aus Vorlage** die Option **Node** und dann **Express-Website** aus. Klicken Sie abschließend auf **Weiter**. Wenn bestimmte Voraussetzungen für die Vorlage **Express-Website** fehlen, werden Sie aufgefordert, diese zu installieren.

	![select express template][webmatrix-templates]

3. Wenn Sie bei Azure angemeldet sind, können Sie nun eine Azure-Website für eine lokale Site erstellen.  Wählen Sie einen eindeutigen Namen, und wählen Sie das Data Center aus, in dem die Site erstellt werden soll: 

	![Create site on Azure][nodesitefromtemplateazure]
	
4. Nachdem WebMatrix die Website erstellt hat, wird die WebMatrix-IDE angezeigt.

	![webmatrix ide][webmatrix-ide]

##Veröffentlichen der Anwendung in Azure

1. Klicken Sie in WebMatrix auf dem Menüband **Startseite** auf **Veröffentlichen**, um das Dialogfeld **Vorschau veröffentlichen** aufzurufen.

	![publish preview][webmatrix-node-publishpreview]

2. Klicken Sie auf **Weiter**. Wenn die Veröffentlichung abgeschlossen ist, wird die URL für die Website auf Azure unten in der WebMatrix-IDE angezeigt.

	![publish complete][webmatrix-publish-complete]

3. Klicken Sie auf den Link, um die Website in Ihrem Browser zu öffnen.

	![Express web site][webmatrix-node-express-site]

##Ändern und erneutes Veröffentlichen der Anwendung

Sie können die Anwendung auf einfache Weise ändern und neu veröffentlichen. Hier nehmen Sie eine einfache Änderung an der Überschrift in der Datei **index.jade** vor und veröffentlichen die Anwendung dann neu.

1. Wählen Sie in WebMatrix die Option **Dateien** aus, und erweitern Sie dann den Ordner **Ansichten**. Öffnen Sie durch Doppelklick die Datei **index.jade**.

	![webmatrix viewing index.jade][webmatrix-modify-index]

2. Ändern Sie die zweite Zeile wie folgt:

		p Welcome to #{title} with WebMatrix on Azure!

3. Speichern Sie die Änderungen, und klicken Sie dann auf das Symbol "Veröffentlichen". Klicken Sie abschließend im Dialogfeld **Vorschau veröffentlichen** auf **Weiter**, und warten Sie, bis das Update veröffentlicht wurde.

	![publish preview][webmatrix-republish]

4. Wenn die Veröffentlichung abgeschlossen ist, rufen Sie die aktualisierte Website über den zurückgegebenen Link auf.

	![Azure node Web site][webmatrix-node-completed]

##Nächste Schritte

Weitere Informationen über die Versionen von Node.js, die im Lieferumfang von Azure enthalten sind, und über die Angabe der Version, die mit Ihrer Anwendung verwendet werden soll, finden Sie unter [Festlegen einer Node.js-Version in einer Azure-Anwendung](nodejs-specify-node-version-azure-apps.md)..

Wenn nach der Bereitstellung für Azure Probleme mit Anwendung auftreten, erhalten Sie unter [Debuggen einer Node.js-Anwendung in Azure-Websites](http://azure.microsoft.com/develop/nodejs/how-to-guides/Debug-Website/) Informationen zur Problemdiagnose.


[Azure-Verwaltungsportal]: http://manage.windowsazure.com
[WebMatrix-WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix für Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[Veröffentlichen einer Azure-Website mit Git]: /de-de/develop/nodejs/common-tasks/publishing-with-git/
[kostenlos]: /de-de/pricing/free-trial
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





<!--HONumber=42-->
