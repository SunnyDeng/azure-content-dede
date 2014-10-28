<properties linkid="dev-nodejs-cloud9" urlDisplayName="Deploying with Cloud9" pageTitle="Node.js deploying with Cloud9 - Azure tutorial" metaKeywords="Cloud9 IDE Azure, Azure node.js, Azure node apps" description="Learn how to use Cloud9 IDE to develop, build, and deploy a Node.js application to Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Deploying an Azure App from Cloud9" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Bereitstellen einer Azure-Anwendung von Cloud9

In diesem Lernprogramm wird beschrieben, wie Sie Cloud9-IDE zur
Entwicklung, Erstellung und Bereitstellung einer Node.js-Anwendung in Azure verwenden.

In diesem Lernprogramm lernen Sie Folgendes:

-   Erstellen eines Cloud9-IDE-Projekts
-   Bereitstellen des Projekts in Azure
-   Aktualisieren einer vorhandenen Azure-Bereitstellung
-   Verschieben von Projekten zwischen Staging- und Produktionsbereitstellungen

[Cloud9-IDE][Cloud9-IDE] bietet eine plattformübergreifende, browserbasierte
Entwicklungsumgebung. Eine der Funktionen, die Cloud9 für Node.js-Projekte unterstützt,
ist die direkte Bereitstellung in Azure von der
IDE-Schnittstelle aus. Cloud9 kann auch in GitHub- und BitBucket-
Repositorydienste integriert werden, sodass Sie Projekte problemlos mit anderen gemeinsam nutzen können.

Mit Cloud9 können Sie von vielen modernen Browsern und
Betriebssystemen aus Anwendungen entwickeln und in Azure bereitstellen, ohne
zusätzliche Entwicklungstools oder SDKs lokal installieren zu müssen. Die Schritte unten
werden anhand von Google Chrome auf einem Mac gezeigt.

## Anmeldung

Um Cloud9 zu verwenden, müssen Sie zuerst die Website besuchen und sich
[für ein Abonnement registrieren][Cloud9-IDE]. Sie können sich entweder mit einem
vorhandenen GitHub- oder BitBucket-Konto anmelden oder ein Cloud9-Konto erstellen. Sie
haben Sie Wahl zwischen einem kostenlosen Angebot und einem
Bezahlangebot, das mehr Funktionen bietet. Weitere Informationen finden Sie unter [Cloud9-IDE][Cloud9-IDE].

## Erstellen eines Node.js-Projekts

1.  Melden Sie sich bei Cloud9 an, klicken Sie auf das **+**-Symbol neben
    **My Projects**, und wählen Sie dann **Create a new project** aus.

    ![neues Cloud9-Projekt erstellen][neues Cloud9-Projekt erstellen]

2.  Geben Sie im Dialogfeld **Create a new project** einen Projektnamen,
    eine Zugriffsart und einen Projekttyp ein. Klicken Sie auf **Create**, um das Projekt zu erstellen.

    ![Cloud9-Dialogfeld "neues Projekt erstellen"][Cloud9-Dialogfeld "neues Projekt erstellen"]

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Einige Optionen erfordern ein kostenpflichtiges Cloud9-Paket.</p>
</div>

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Der Projektname des Cloud9-Projekts wird nicht f&uuml;r die Bereitstellung zu Azure verwendet.</p>
</div>

3.  Klicken Sie nach der Erstellung des Projekts auf **Start Editing**. Wenn Sie Cloud9-IDE erstmalig verwendet haben, werden Sie zu einem Rundgang durch den Dienst eingeladen. Wenn Sie den Rundgang überspringen und ihn später ansehen möchten, wählen Sie **Just the editor,please**.

    ![Bearbeitung des Cloud9-Projekts starten][Bearbeitung des Cloud9-Projekts starten]

4.  Wählen Sie **File** und dann **New File** aus, um eine neue
    Node-Anwendung zu erstellen.

    ![neue Datei im Cloud9-Projekt erstellen][neue Datei im Cloud9-Projekt erstellen]

5.  Es wird eine neue Registerkarte namens **Untitled1** angezeigt. Geben Sie
    folgenden Code auf der Registerkarte **Untitled1** ein, um die
    Node-Anwendung zu erstellen:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Durch die Verwendung von &quot;process.env.PORT&quot; wird sichergestellt, dass die Anwendung den korrekten Port ausw&auml;hlt, wenn sie im Cloud9-Debugger ausgef&uuml;hrt wird oder zu Azure bereitgestellt wird.</p>
</div>

6.  Wählen Sie **File** und dann **Save as**, um den Code zu speichern. Geben
    Sie im Dialogfeld **Save As** als Dateiname **server.js** ein,
    und klicken Sie dann auf **Save**.

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Sie werden m&ouml;glicherweise ein Warnsymbol bemerken, welches anzeigt, dass die req-Variable nicht verwendet wird. Sie k&ouml;nnen diese Warnung ignorieren.</p>
</div>

    ![server.js-Datei speichern][server.js-Datei speichern]

## Ausführen der Anwendung

<div class="dev-callout">
<strong>Hinweis</strong>
<p>Die in diesem Abschnitt gezeigten Schritte sind f&uuml;r eine Hello World-Anwendung ausreichend. F&uuml;r Anwendungen mit externen Modulen m&uuml;ssen Sie m&ouml;glicherweise eine spezielle Version von Node.js f&uuml;r die Debug-Umgebung ausw&auml;hlen. W&auml;hlen Sie dazu im Debug-Dropdownmen&uuml; <strong>Configure...</strong>, und w&auml;hlen Sie dann die spezielle Node.js-Version aus. Sie werden zum Beispiel m&ouml;glicherweise Authentifizierungsfehler erhalten, wenn Sie das &quot;azure&quot;-Modul verwenden, falls Sie &quot;Node.js 0.6.x&quot; nicht ausgew&auml;hlt haben.</p>
</div>

1.  Klicken Sie auf **Debug**, um die Anwendung im Cloud9-Debugger auszuführen.

    ![im Debugger ausführen][im Debugger ausführen]

2.  Es wird ein Ausgabefenster angezeigt. Klicken Sie auf die
    aufgelistete URL, um über ein Browserfenster auf die Anwendung zuzugreifen.

    ![Ausgabefenster][Ausgabefenster]

    Die entstandene Anwendung wird wie folgt aussehen:

    ![in Browser ausgeführte Anwendung][in Browser ausgeführte Anwendung]

3.  Klicken Sie auf **stop**, um das Debugging der Anwendung zu beenden.

## Erstellen eines Azure-Kontos

Um die Anwendung in Windows Azure bereitzustellen, benötigen Sie ein Konto. Wenn
Sie kein Azure-Konto besitzen, können Sie sich wie folgt für eine
kostenlose Testversion anmelden:

[WACOM.INCLUDE [create-azure-account](../includes/create-azure-account.md)]

## Erstellen einer Bereitstellung

1.  Wählen Sie **Deploy**, und klicken Sie dann auf **+**, um einen Bereitstellungsserver zu erstellen.

    [Erstellen einer neuen Bereitstellung][Erstellen einer neuen Bereitstellung]

2.  Geben Sie im Dialogfeld **Add a deploy target** einen Bereitstellungsnamen ein, und wählen Sie dann **Azure** in der Liste **Choose type**. Der Bereitstellungsname, den Sie angeben, wird zur Identifizierung der Bereitstellung in Cloud9 verwendet und entspricht nicht dem Bereitstellungsnamen in Azure.

3.  Wenn Sie erstmalig eine Cloud9-Umgebung erstellt haben, die Azure verwendet, müssen Sie Ihre Azure-Veröffentlichungseinstellungen konfigurieren. Führen Sie die folgenden Schritte aus, um diese Einstellungen herunterzuladen und in Cloud9 zu installieren:

    1.  Klicken Sie auf **Download Azure Settings**.

        ![Veröffentlichungseinstellungen herunterladen][Veröffentlichungseinstellungen herunterladen]

        Daraufhin wird das Azure-Verwaltungsportal geöffnet, und Sie werden aufgefordert, die Azure-Veröffentlichungseinstellungen herunterzuladen. Sie werden aufgefordert, sich bei Ihrem Azure-Konto anzumelden, bevor Sie beginnen können.

    2.  Speichern Sie die Veröffentlichungseinstellungsdatei auf einem lokalen Laufwerk.

    3.  Wählen Sie im Dialogfeld **Add a deploy target** die Option **Choose File**
        und dann die Datei aus, die Sie zuvor heruntergeladen haben.

    4.  Klicken Sie nach Auswahl der Datei auf **Upload**.

4.  Klicken Sie auf **+ Create new**, um einen neuen gehosteten Dienst zu erstellen. Ein *gehosteter Dienst* ist der Container, in dem die Anwendung gehostet wird, wenn diese in Azure bereitgestellt wird. Weitere Informationen erhalten Sie unter [Cloud-Dienste][Cloud-Dienste].

    ![neue Bereitstellung erstellen][neue Bereitstellung erstellen]

5.  Sie werden aufgefordert, den Namen des neuen gehosteten Diensts sowie die Konfigurationsoptionen einzugeben, wie etwa die Anzahl an Instanzen, Host-Betriebssystem und Rechenzentrum. Der angegebene Bereitstellungsname wird als Name des gehosteten Diensts in Azure verwendet. Der Name muss im Azure-System eindeutig sein.

    ![neuen gehosteten Dienst erstellen][neuen gehosteten Dienst erstellen]

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Im Dialogfeld <strong>Add a deploy target</strong> werden vorhandene gehostete Azure-Dienste unter dem Abschnitt <strong>Choose existing deployment</strong> aufgelistet; wenn Sie einen vorhandenen gehosteten Dienst ausw&auml;hlen, f&uuml;hrt dies dazu, dass dieses Projekt zu diesem Dienst bereitgestellt wird.</p>
</div>

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Wenn Sie <strong>Enable RDP</strong> ausw&auml;hlen und einen Benutzernamen und ein Kennwort bereitstellen, wird Remotedesktop f&uuml;r Ihre Umgebung erm&ouml;glicht.</p>
</div>

## Bereitstellen zur Azure-Produktionsumgebung

1.  Wählen Sie die in den vorherigen Schritten erstellte Bereitstellung aus. Es wird
    ein Dialogfeld mit Informationen zu dieser Bereitstellung und der
    Produktions-URL, die nach der Bereitstellung in Windows Azure verwendet wird,
    angezeigt.

    ![eine Bereitstellung auswählen][eine Bereitstellung auswählen]

2.  Wählen Sie **Deploy to Production environment**.

3.  Klicken Sie auf **Deploy**, um mit der Bereitstellung zu beginnen.

4.  Wenn Sie dieses Projekt erstmalig zu Azure bereitstellen, erhalten Sie eine Fehlermeldung **'No web.config found'**. Wählen Sie **Yes**, um die Datei zu erstellen. Dadurch wird eine "Web.cloud.config"-Datei zu Ihrem Projekt hinzugefügt.

    ![Nachricht "Keine web.config-Datei gefunden"][Nachricht "Keine web.config-Datei gefunden"]

5.  Wenn Sie dieses Projekt erstmalig zu Azure bereitstellen, erhalten Sie eine Fehlermeldung **'No 'csdef' file present'**. Wählen Sie **Yes**, um die .csdef-Datei zu erstellen. Dadurch wird eine 'ServiceDefinition.csdef'-Datei zu Ihrem Projekt hinzugefügt. "ServiceDefinition.csdef" ist eine Azure-spezifische Datei, die für die Veröffentlichung Ihrer Anwendung erforderlich ist. Weitere Informationen erhalten Sie unter [Cloud-Dienste][Cloud-Dienste].

6.  Sie werden aufgefordert, die Instanzengröße für diese Anwendung auszuwählen. Wählen Sie **Small**, und klicken Sie dann auf **Create**. Weitere Details zu Azure-VM-Größen finden Sie unter [Konfiguration der Größe einer virtuellen Maschine][Konfiguration der Größe einer virtuellen Maschine].

    ![Werte für csdef-Datei angeben][Werte für csdef-Datei angeben]

7.  Der Bereitstellungseintrag zeigt den Status des Bereitstellungsprozess an. Nach der Fertigstellung wird die Bereitstellungs als **Active** angezeigt.

    ![Bereitstellungsstatus][Bereitstellungsstatus]

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Projekte, die &uuml;ber Cloud 9-IDE bereitgestellt werden, werden der GUID als Bereitstellungsname in Azure zugewiesen.</p>
</div>

8.  Das Bereitstellungsdialogfeld enthält einen Link zur Produktions-URL. Klicken Sie nach Abschluss der Bereitstellung auf die URL, um zur in Azure ausgeführten Anwendung zu gelangen.

    ![Azure-Produktions-URL-Link][Azure-Produktions-URL-Link]

## Aktualisieren der Anwendung

Wenn Sie Änderungen an Ihrer Anwendung vornehmen, können Sie Cloud9 verwenden, um die aktualisierte Anwendung zum selben gehosteten Azure-Dienst bereitzustellen.

1.  Aktualisieren Sie in der Datei "server.js file" den Code, sodass "hello azure v2" auf dem Bildschirm angezeigt wird. Sie können den vorhandenen Code mit folgendem aktualisierten Code ersetzen:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  Wählen Sie **File** und dann **Save**, um den Code zu speichern.

## Bereitstellen der Aktualisierung zur Azure-Stagingumgebung

1.  Wählen Sie **Deploy to Staging**.

2.  Klicken Sie auf **Deploy**, um mit der Bereitstellung zu beginnen.

    Jeder gehostete Azure-Dienst unterstützt zwei Umgebungen, die Staging- und die Produktionsumgebung. Die Stagingumgebung entspricht genau der Produktionsumgebung, außer, dass Sie auf die Staginganwendung nur mit einer obfuskierten, GUID-basierten URL zugreifen können, die von Azure generiert wird. Sie können die Stagingumgebung verwenden, um die Anwendung zu testen. Nach dem Überprüfen der Änderungen können Sie die Stagingversion in die Produktion überführen, indem Sie einen VIP-Austausch (Austausch der virtuellen IP) durchführen, der später in diesem Lernprogramm beschrieben wird.

3.  Wenn die Anwendung in die Stagingumgebung bereitgestellt wird, wird die GUID-basierte Staging-URL in der Konsolenausgabe wie im Screenshot unten angezeigt. Klicken Sie auf die URL, um die Staginganwendung in einem Browser zu öffnen.

    ![Konsolenausgabe, die Staging-URL anzeigt][Konsolenausgabe, die Staging-URL anzeigt]

## Überführen der Aktualisierung zur Produktion über VIP-Austausch

Bei Bereitstellung eines Diensts in der Produktions- oder
Stagingumgebung wird dem Dienst in dieser Umgebung eine virtuelle
IP-Adresse (VIP) zugewiesen. Wenn Sie einen Dienst von der Bereitstellungsumgebung
in die Produktionsumgebung überführen möchten, kann dies ohne
erneute Bereitstellung erfolgen, indem ein VIP-Austausch erfolgt, welcher
die Staging- und Produktionsbereitstellungen tauscht. Ein VIP-Austausch überführt die getestete Staginganwendung
ohne Ausfallzeiten in der Produktionsumgebung in die Produktion. Weitere
Informationen finden Sie unter [Übersicht über das Verwalten von Bereitstellungen in Azure][Übersicht über das Verwalten von Bereitstellungen in Azure].

1.  Klicken Sie im Bereitstellungsdialogfeld auf den Link **Open portal**,
    um das Azure-Verwaltungsportal zu öffnen.

    [Link vom Bereitstellungsdialogfeld zum Azure-Verwaltungsportal][Link vom Bereitstellungsdialogfeld zum Azure-Verwaltungsportal]

2.  Melden Sie sich mit Ihren Anmeldedaten am Portal an.

3.  Wählen Sie links auf der Webseite **Gehostete Dienste,
    Speicherkonten CDN** aus, und klicken Sie dann auf **Gehostete Dienste**.

    ![Azure-Verwaltungsportal][Azure-Verwaltungsportal]

    Das Ergebnisfenster zeigt den gehosteten Dienst mit dem Namen, den Sie in Cloud9 angegeben haben, sowie zwei Bereitstellungen: eine mit dem **Umgebungs-** wert **Staging**, die zweite mit **Produktion**.

4.  Für den VIP-Austausch wählen Sie den gehosteten Dienst aus, und klicken Sie dann auf **VIP austauschen** im Menüband.

    ![VIP-Austausch][VIP-Austausch]

5.  Klicken Sie auf **OK** im VIP-Austauschdialogfeld, das angezeigt wird.

6.  Navigieren Sie zu Ihrer Produktionsanwendung. Sie werden bemerken, dass die Version der zuvor in der Stagingumgebung bereitgestellten Anwendung jetzt in der Produktion ist.

	![Produktionsanwendung, die auf Azure ausgeführt wird][Produktionsanwendung, die auf Azure ausgeführt wird]

## Verwenden von Remotedesktop

Wenn Sie bei der Erstellung der Bereitstellung RDP aktiviert und einen Benutzernamen sowie ein Kennwort angegeben haben, können Sie Remotedesktop verwenden, um eine Verbindung mit dem gehosteten Dienst herzustellen,
indem Sie eine bestimmte Instanz auswählen und dann im Menüband "Verbinden" auswählen.

![Mit einer Instanz verbinden][Mit einer Instanz verbinden]

Wenn Sie auf "Verbinden" klicken, werden Sie aufgefordert, eine .RDP-Datei zu öffnen oder herunterzuladen. Diese Datei enthält die erforderlichen Informationen für die Verbindung zu Ihrer Remotedesktopsitzung. Wenn Sie diese Datei auf einem Windows-System ausführen, werden Sie aufgefordert, den Benutzernamen und das Kennwort einzugeben, das Sie bei der Erstellung der Bereitstellung eingegeben haben.
Sie werden dann mit dem Desktop der ausgewählten Instanz verbunden.

<div class="dev-callout">
<strong>Hinweis</strong>
<p>Die RDP-Datei, welche f&uuml;r die Verbindung mit der gehosteten Instanz der Anwendung erforderlich ist, funktioniert nur mit der Remotedesktopanwendung in Windows.
</p>
</div>

## Beenden und Löschen der Anwendung

Bei Azure werden Rolleninstanzen pro genutzter Serverzeitstunde berechnet. Serverzeit wird genutzt, sobald die Anwendung bereitgestellt wird, selbst wenn die Instanzen nicht ausgeführt werden und sich im Status "beendet" befinden. Zusätzlich
beanspruchen sowohl Produktions- als auch Stagingumgebungen Serverzeit.

Cloud9 fokussiert sich auf die Bereitstellung einer IDE-Schnittstelle und bietet keine direkte Methode, um eine Anwendung zu beenden oder zu löschen, wenn diese erstmal in Azure bereitgestellt wurde. Führen Sie folgende Schritte aus, um eine in Azure gehostete Anwendung zu löschen:

1.  Klicken Sie im Bereitstellungsdialogfeld auf den Link **Open portal**, um das Azure-Verwaltungsportal zu öffnen.

    ![Link vom Bereitstellungsdialogfeld zum Azure-Verwaltungsportal][Link vom Bereitstellungsdialogfeld zum Azure-Verwaltungsportal]

2.  Melden Sie sich mit Ihren Anmeldedaten am Portal an.

3.  Wählen Sie links der Webseite **Gehostete Dienste, Speicherkonten CDN**, und klicken Sie dann auf **Gehostete Dienste**.

4.  Wählen Sie die Stagingbereitstellung (angezeigt durch den **Umgebungswert**). Klicken Sie im Menüband auf **Löschen**, um die Anwendung zu löschen.

    ![Bereitstellung löschen][Bereitstellung löschen]

5.  Wählen Sie die Produktionsbereitstellung, und klicken Sie auf **Löschen**, um diese Anwendung ebenfalls zu löschen.

## Zusätzliche Ressourcen

-   [Cloud9-Dokumentation][Cloud9-Dokumentation]

  [Cloud9-IDE]: http://cloud9ide.com/
  [neues Cloud9-Projekt erstellen]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png
  [Cloud9-Dialogfeld "neues Projekt erstellen"]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png
  [Bearbeitung des Cloud9-Projekts starten]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png
  [neue Datei im Cloud9-Projekt erstellen]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png
  [server.js-Datei speichern]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png
  [im Debugger ausführen]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png
  [Ausgabefenster]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png
  [in Browser ausgeführte Anwendung]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png
  [create-azure-account]: ../includes/create-azure-account.md
  [Veröffentlichungseinstellungen herunterladen]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png
  [Cloud-Dienste]: http://msdn.microsoft.com/de-de/library/windowsazure/jj155995.aspx
  [neue Bereitstellung erstellen]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png
  [neuen gehosteten Dienst erstellen]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png
  [eine Bereitstellung auswählen]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png
  [Nachricht "Keine web.config-Datei gefunden"]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png
  [Konfiguration der Größe einer virtuellen Maschine]: http://msdn.microsoft.com/de-de/library/windowsazure/ee814754.aspx
  [Werte für csdef-Datei angeben]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png
  [Bereitstellungsstatus]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png
  [Azure-Produktions-URL-Link]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png
  [Konsolenausgabe, die Staging-URL anzeigt]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png
  [Übersicht über das Verwalten von Bereitstellungen in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433027.aspx
  [VIP-Austausch]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png
  [Produktionsanwendung, die auf Azure ausgeführt wird]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png
  [Mit einer Instanz verbinden]: ./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png
  [Link vom Bereitstellungsdialogfeld zum Azure-Verwaltungsportal]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png
  [Bereitstellung löschen]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png
  [Cloud9-Dokumentation]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409
