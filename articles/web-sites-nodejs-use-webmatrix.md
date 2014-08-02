<properties linkid="web-site-with-webmatrix" urlDisplayName="Web site with WebMatrix" pageTitle="Node.js web site with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure web site." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build and deploy a Node.js web site to Azure using WebMatrix" authors="" solutions="" manager="" editor="" />

Erstellen einer Node.js-Website und Bereitstellen für Azure mit WebMatrix
=========================================================================

In diesem Lernprogramm erfahren Sie, wie Sie WebMatrix zum Entwickeln und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website verwenden. WebMatrix ist ein kostenloses Webentwicklungstool von Microsoft, das alles enthält, was Sie für die Entwicklung von Websites benötigen. WebMatrix umfasst zahlreiche Features, die die Verwendung von Node.js einfach gestalten, einschließlich Codefertigstellung, vorbereitete Vorlagen und Editorunterstützung für Jade, LESS und CoffeeScript. Erfahren Sie mehr über [WebMatrix für Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409).

Nach der Durchführung dieses Lernprogramms haben Sie eine in Azure ausgeführte Node.js-Website.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Node-Website auf Azure](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Anmelden bei Azure
------------------

Gehen Sie wie folgt vor, um eine Azure-Website zu erstellen.

**Hinweis**

Um an diesem Lernprogramm teilnehmen zu können, benötigen Sie ein Azure-Konto mit aktivierter Azure-Websitefunktion.

Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A7171371E).

1.  Starten Sie WebMatrix.
2.  Wenn Sie WebMatrix zum ersten Mal verwenden, werden Sie aufgefordert, sich bei Azure anzumelden. Klicken Sie sonst auf die Schaltfläche **Anmelden**, und wählen Sie **Konto hinzufügen**. Melden Sie sich mit Ihrem ****Microsoft-Konto an.

    ![Konto hinzufügen](./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png)

3.  Wenn Sie sich für ein Azure-Konto angemeldet haben, können Sie sich mit Ihrem Microsoft-Konto anmelden:

    ![Anmelden bei Azure](./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png)

Erstellen einer Site für Azure mit einer integrierten Vorlage
-------------------------------------------------------------

1.  Klicken Sie im Startbildschirm auf die Schaltfläche **Neu**, und wählen Sie **Vorlagenkatalog**, um eine neue Site aus dem Vorlagenkatalog zu erstellen:

    ![Neue Site aus Vorlagenkatalog](./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png)

2.  Wählen Sie im Dialogfeld **Website aus Vorlage** die Option **Node** aus, und wählen Sie dann **Express-Website**. Klicken Sie anschließend auf **Next**. Wenn bestimmte Voraussetzungen für die Vorlage **Express-Website** fehlen, werden Sie aufgefordert, diese zu installieren.

    ![Express-Vorlage auswählen](./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png)

3.  Wenn Sie bei Azure angemeldet sind, können Sie nun eine Azure-Website für eine lokale Site erstellen. Wählen Sie einen eindeutigen Namen, und wählen Sie das Data Center aus, in dem die Site erstellt werden soll:

    ![Site auf Azure erstellen](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png)

4.  Nachdem WebMatrix die Website erstellt hat, wird die WebMatrix IDE angezeigt.

    ![WebMatrix-IDE](./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png)

Veröffentlichen der Anwendung in Azure
--------------------------------------

1.  Klicken Sie in WebMatrix im Menüband **Startseite** auf **Veröffentlichen**, um das Dialogfeld **Vorschau veröffentlichen** aufzurufen.

    ![Vorschau veröffentlichen](./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png)

2.  Klicken Sie auf **Weiter**. Wenn die Veröffentlichung abgeschlossen ist, wird die URL für die Website auf Azure unten in der WebMatrix-IDE angezeigt.

    ![Veröffentlichung abgeschlossen](./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png)

3.  Klicken Sie auf den Link, um die Website in Ihrem Browser zu öffnen.

    ![Express-Website](./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png)

Ändern und erneutes Veröffentlichen der Anwendung
-------------------------------------------------

Sie können die Anwendung auf einfache Weise ändern und neu veröffentlichen. Hier nehmen Sie eine einfache Änderung an der Überschrift in der **index.jade**-Datei vor und veröffentlichen die Anwendung dann neu.

1.  Wählen Sie in WebMatrix die Option **Dateien**, und erweitern Sie dann den Ordner **Ansichten** folder. Öffnen Sie die Datei **index.php**, indem Sie darauf doppelklicken.

    ![WebMatrix-Ansicht von index.jade](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png)

2.  Ändern Sie die zweite Zeile wie folgt:

         p Welcome to #{title} with WebMatrix on Azure!

3.  Speichern Sie die Änderungen, und klicken Sie dann auf das Symbol "Veröffentlichen". Klicken Sie abschließend im Dialogfeld **Vorschau veröffentlichen** auf **Weiter**, und warten Sie, bis das Update veröffentlicht wurde.

    ![Vorschau veröffentlichen](./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png)

4.  Wenn die Veröffentlichung abgeschlossen ist, rufen Sie die aktualisierte Website über den zurückgegebenen Link auf.

    ![Node-Website auf Azure](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png)

Nächste Schritte
----------------

Weitere Informationen über die Versionen von Node.js, die im Lieferumfang von Azure enthalten sind, und über die Angabe der Version, die mit Ihrer Anwendung verwendet werden soll, finden Sie unter [Festlegen einer Node.js-Version in einer Azure-Anwendung](http://www.windowsazure.com/en-us/develop/nodejs/common-tasks/specifying-a-node-version/).

Wenn nach der Bereitstellung für Azure Probleme mit Anwendung auftreten, erhalten Sie unter [Debuggen einer Node.js-Anwendung in Azure-Websites](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/Debug-Website/) Informationen zur Problemdiagnose.

