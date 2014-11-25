<properties linkid="web-site-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle="Node.js website with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure website." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Erstellen einer Node.js-Website und Bereitstellen für Azure mit WebMatrix

In diesem Lernprogramm erfahren Sie, wie Sie WebMatrix zum Entwickeln und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website verwenden. WebMatrix ist ein kostenloses Webentwicklungstool von Microsoft, das alles enthält, was Sie für die Entwicklung von Websites benötigen. WebMatrix umfasst zahlreiche Features, die die Verwendung von Node.js einfach gestalten, einschließlich Codefertigstellung, vorbereitete Vorlagen und Editorunterstützung für Jade, LESS und CoffeeScript. Erfahren Sie mehr über [WebMatrix für Azure][WebMatrix für Azure].

Nach Durchführung dieses Lernprogramms haben Sie eine in Azure ausgeführte Node.js-Website.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Node-Website auf Azure][Node-Website auf Azure]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Anmelden bei Azure

Führen Sie die folgenden Schritte aus, um eine Azure-Website zu erstellen:

<div class="dev-callout"><strong>Hinweis</strong>
<p>Sie ben&ouml;tigen ein Azure-Konto, bei dem die Azure-Websites-Funktion aktiviert ist, um dieses Lernprogramm abzuschlie&szlig;en.</p>
<p>Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Kostenlose Azure-Testversion</a>.</p>
</div>

1.  Starten Sie WebMatrix.
2.  Wenn Sie WebMatrix zum ersten Mal verwenden, werden Sie aufgefordert, sich bei Azure anzumelden. Klicken Sie sonst auf die Schaltfläche **Anmelden**, und wählen Sie **Konto hinzufügen**. **Melden** Sie sich mit Ihrem Microsoft-Konto **an**.

    ![Konto hinzufügen][Konto hinzufügen]

3.  Wenn Sie sich für ein Azure-Konto angemeldet haben, können Sie sich mit Ihrem Microsoft-Konto anmelden:

    ![Anmelden bei Azure][Anmelden bei Azure]

## Erstellen einer Site für Azure mit einer integrierten Vorlage

1.  Klicken Sie im Startbildschirm auf die Schaltfläche **Neu**, und wählen Sie **Vorlagenkatalog**, um eine neue Site aus dem Vorlagenkatalog zu erstellen:

    ![Neue Site aus Vorlagenkatalog][Neue Site aus Vorlagenkatalog]

2.  Wählen Sie im Dialogfeld **Website aus Vorlage** die Option **Node** aus, und wählen Sie dann **Express-Website**. Klicken Sie anschließend auf **Next**. Wenn bestimmte Voraussetzungen für die Vorlage **Express-Website** fehlen, werden Sie aufgefordert, diese zu installieren.

    ![Express-Vorlage auswählen][Express-Vorlage auswählen]

3.  Wenn Sie bei Azure angemeldet sind, können Sie nun eine Azure-Website für eine lokale Site erstellen. Wählen Sie einen eindeutigen Namen, und wählen Sie das Data Center aus, in dem die Site erstellt werden soll:

    ![Site auf Azure erstellen][Site auf Azure erstellen]

4.  Nachdem WebMatrix die Website erstellt hat, wird die WebMatrix-IDE angezeigt.

    ![WebMatrix-IDE][WebMatrix-IDE]

## Veröffentlichen der Anwendung in Azure

1.  Klicken Sie in WebMatrix im Menüband **Startseite** auf **Veröffentlichen**, um das Dialogfeld **Vorschau veröffentlichen** aufzurufen.

    ![Vorschau veröffentlichen][Vorschau veröffentlichen]

2.  Klicken Sie auf **Weiter**. Wenn die Veröffentlichung abgeschlossen ist, wird die URL für die Website auf Azure unten in der WebMatrix-IDE angezeigt.

    ![Veröffentlichung abgeschlossen][Veröffentlichung abgeschlossen]

3.  Klicken Sie auf den Link, um die Website in Ihrem Browser zu öffnen.

    ![Express-Website][Express-Website]

## Ändern und erneutes Veröffentlichen der Anwendung

Sie können die Anwendung auf einfache Weise ändern und neu veröffentlichen. Hier nehmen Sie eine einfache Änderung an der Überschrift in der **index.jade**-Datei vor und veröffentlichen die Anwendung dann neu.

1.  Wählen Sie in WebMatrix die Option **Dateien**, und erweitern Sie dann den Ordner **Ansichten** folder. Öffnen Sie die Datei **index.php**, indem Sie darauf doppelklicken.

    ![WebMatrix-Ansicht von index.jade][WebMatrix-Ansicht von index.jade]

2.  Ändern Sie die zweite Zeile wie folgt:

        p Welcome to #{title} with WebMatrix on Azure!

3.  Speichern Sie die Änderungen, und klicken Sie dann auf das Symbol "Veröffentlichen". Klicken Sie abschließend im Dialogfeld **Vorschau veröffentlichen** auf **Weiter**, und warten Sie, bis das Update veröffentlicht wurde.

    ![Vorschau veröffentlichen][1]

4.  Wenn die Veröffentlichung abgeschlossen ist, rufen Sie die aktualisierte Website über den zurückgegebenen Link auf.

    ![Node-Website auf Azure][Node-Website auf Azure]

## Nächste Schritte

Weitere Informationen über die Versionen von Node.js, die im Lieferumfang von Azure enthalten sind, und über die Angabe der Version, die mit Ihrer Anwendung verwendet werden soll, finden Sie unter [Festlegen einer Node.js-Version in einer Azure-Anwendung][Festlegen einer Node.js-Version in einer Azure-Anwendung].

Wenn nach der Bereitstellung für Azure Probleme mit Anwendung auftreten, erhalten Sie unter [Debuggen einer Node.js-Anwendung in Azure-Websites][Debuggen einer Node.js-Anwendung in Azure-Websites] Informationen zur Problemdiagnose.

  [WebMatrix für Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Node-Website auf Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
  [Konto hinzufügen]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
  [Anmelden bei Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
  [Neue Site aus Vorlagenkatalog]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
  [Express-Vorlage auswählen]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png
  [Site auf Azure erstellen]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
  [WebMatrix-IDE]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
  [Vorschau veröffentlichen]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png
  [Veröffentlichung abgeschlossen]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
  [Express-Website]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
  [WebMatrix-Ansicht von index.jade]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
  [1]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
  [Festlegen einer Node.js-Version in einer Azure-Anwendung]: /de-de/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Debuggen einer Node.js-Anwendung in Azure-Websites]: http://www.windowsazure.com/de-de/develop/nodejs/how-to-guides/Debug-Website/
