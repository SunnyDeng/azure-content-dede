<properties 
	pageTitle="Node.js-Bereitstellung mit Cloud9 – Azure-Lernprogramm" 
	description="Hier erfahren Sie, wie Sie Cloud9-IDE zur Entwicklung, Erstellung und Bereitstellung einer Node.js-Anwendung in Azure verwenden." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="mwasson"/>






# Bereitstellen einer Azure-Anwendung von Cloud9

In diesem Lernprogramm wird beschrieben, wie Sie Cloud9-IDE zur Entwicklung, Erstellung und Bereitstellung einer Node.js-Anwendung in Azure verwenden.

In diesem Lernprogramm lernen Sie Folgendes:

-   Erstellen eines Cloud9-IDE-Projekts
-   Bereitstellen des Projekts in Azure
-   Aktualisieren einer vorhandenen Azure-Bereitstellung
-   Verschieben von Projekten zwischen Staging- und Produktionsbereitstellungen

[Cloud9-IDE][] bietet eine plattformübergreifende, browserbasierte Entwicklungsumgebung. Eine der Funktionen, die Cloud9 für Node.js-Projekte unterstützt, ist die direkte Bereitstellung zu Azure von der IDE-Schnittstelle aus. Cloud9 kann in GitHub- und BitBucket-Repositorydienste integriert werden, sodass Sie Projekte einfach gemeinsam mit anderen nutzen können.

Mit Cloud9 können Sie von vielen modernen Browsern und Betriebssystemen aus Anwendungen entwickeln und in Azure bereitstellen, ohne zusätzliche Entwicklungstools oder SDKs lokal bereitstellen zu müssen. Die Schritte unten werden anhand von Google Chrome auf einem Mac gezeigt.

## Anmeldung

Um Cloud9 verwenden zu können, müssen Sie zuerst die Website besuchen und sich [für ein Abonnement registrieren][Cloud9 IDE]. Sie können sich entweder mit einem vorhandenen GitHub- oder BitBucket anmelden, oder ein Cloud9-Konto erstellen. Sie können zwischen einem kostenlosen Angebot sowie einem Bezahlangebot wählen, das mehr Funktionen bietet. Weitere Informationen finden Sie unter [Cloud9-IDE][].

## Erstellen eines Node.js-Projekts

1.  Melden Sie sich bei Cloud9 an, klicken Sie auf das **+**-Symbol neben **My Projects**, und wählen Sie dann **Create a new project**.

	![neues Cloud9-Projekt erstellen](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png)

2.  Geben Sie im Dialogfeld **Create a new project** einen Projektnamen, eine Zugriffsart und einen Projekttyp ein. Klicken Sie auf **Create**, um das Projekt zu erstellen.

	![Cloud9-Dialogfeld "neues Projekt erstellen"](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png)

	> [AZURE.NOTE]Einige Optionen erfordern ein kostenpflichtiges Cloud9-Paket.
	   
	> [AZURE.NOTE]Der Projektname des Cloud9-Projekts wird nicht für die Bereitstellung zu Azure verwendet.

3.  Klicken Sie nach der Erstellung des Projekts auf **Start Editing**. Wenn Sie Cloud9-IDE erstmalig verwendet haben, werden Sie zu einem Rundgang durch den Dienst eingeladen. Wenn Sie den Rundgang überspringen und ihn später ansehen möchten, wählen Sie **Just the editor,please**.

	![Bearbeitung des Cloud9-Projekts starten](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png)

4.  Wählen Sie **File** und dann **New File**, um eine neue Node-Anwendung zu erstellen.

	![neue Datei im Cloud9-Projekt erstellen](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png)

5.  Es wird eine neue Registerkarte namens **Untitled1** angezeigt. Geben Sie folgenden Code auf der Registerkarte **Untitled1** ein, um die Node-Anwendung zu erstellen:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);
	
	> [AZURE.NOTE]Durch die Verwendung von "process.env.PORT" wird sichergestellt, dass die Anwendung den korrekten Port auswählt, wenn sie im Cloud9-Debugger ausgeführt wird oder zu Azure bereitgestellt wird.

6.  Wählen Sie **File** und dann **Save as**, um den Code zu speichern. Geben Sie im Dialogfeld **Save As** als Dateiname **server.js** ein, und klicken Sie dann auf **Save**.


	> [AZURE.NOTE]Sie werden möglicherweise ein Warnsymbol bemerken, welches anzeigt, dass die req-Variable nicht verwendet wird. Sie können diese Warnung ignorieren.

	![server.js-Datei speichern](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png)

## Ausführen der Anwendung

> [AZURE.NOTE]Die in diesem Abschnitt gezeigten Schritte sind für eine Hello World-Anwendung ausreichend. Für Anwendungen mit externen Modulen müssen Sie möglicherweise eine spezielle Version von Node.js für die Debug-Umgebung auswählen. Wählen Sie dazu im Debug-Dropdownmenü **Konfigurieren...** und dann die spezielle Node.js-Version aus. Sie erhalten zum Beispiel möglicherweise Authentifizierungsfehler erhalten, wenn Sie das azure-Modul verwenden und Sie nicht "Node.js 0.6.x" ausgewählt haben.


1.  Klicken Sie auf **Debug**, um die Anwendung im Cloud9-Debugger auszuführen.
	
	![im Debugger ausführen](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png)

2.  Es wird ein Ausgabefenster angezeigt. Klicken Sie auf die aufgelistete URL, um über ein Browserfenster auf die Anwendung zuzugreifen.

	![Ausgabefenster](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png)

	Die entstandene Anwendung wird wie folgt aussehen:

	![in Browser ausgeführte Anwendung](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png)

3.  Klicken Sie auf **stop**, um das Debugging der Anwendung zu beenden.

## Erstellen eines Azure-Kontos

Um die Anwendung in Windows Azure bereitzustellen, benötigen Sie ein Konto. Wenn Sie kein Azure-Konto haben, können Sie sich wie folgt für eine kostenlose Testversion anmelden:

[AZURE.INCLUDE [create-azure-account](../includes/create-azure-account.md)]


## Erstellen einer Bereitstellung

1.  Wählen Sie **Deploy**, und klicken Sie dann auf **+**, um einen Bereitstellungsserver zu erstellen.

    ![neue Bereitstellung erstellen][create a new deployment]

2.  Geben Sie im Dialogfeld **Add a deploy target** einen Bereitstellungsnamen ein, und wählen Sie dann **Azure** in der Liste **Choose type**. Der Bereitstellungsname, den Sie angeben, wird zur Identifizierung der Bereitstellung in Cloud9 verwendet und entspricht nicht dem Bereitstellungsnamen in Azure.

3.  Wenn Sie erstmalig eine Cloud9-Umgebung erstellt haben, die Azure verwendet, müssen Sie Ihre Azure-Veröffentlichungseinstellungen konfigurieren. Führen Sie die folgenden Schritte aus, um diese Einstellungen herunterzuladen und in Cloud9 zu installieren:

    1.  Klicken Sie auf **Download Azure Settings**.

        ![Veröffentlichungseinstellungen herunterladen](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png)

        Daraufhin wird das Azure-Verwaltungsportal geöffnet, und Sie werden aufgefordert, die Azure-Veröffentlichungseinstellungen herunterzuladen. Sie werden aufgefordert, sich bei Ihrem Azure-Konto anzumelden, bevor Sie beginnen können.

    2.  Speichern Sie die Veröffentlichungseinstellungsdatei auf einem lokalen Laufwerk.

    3.  Wählen Sie im Dialogfeld **Add a deploy target** die Option **Choose File**, und wählen Sie dann die Datei aus, die Sie zuvor heruntergeladen haben.

    4.  Klicken Sie nach Auswahl der Datei auf **Upload**.

4.  Klicken Sie auf **+ Create new**, um einen neuen gehosteten Dienst zu erstellen. Ein *gehosteter Dienst* ist der Container, in dem die Anwendung gehostet wird, wenn diese in Azure bereitgestellt wird. Weitere Informationen erhalten Sie unter [Cloud-Dienste][].

	![neue Bereitstellung erstellen](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png)

5.  Sie werden aufgefordert, den Namen des neuen gehosteten Diensts sowie die Konfigurationsoptionen einzugeben, wie etwa die Anzahl an Instanzen, Host-Betriebssystem und Rechenzentrum. Der angegebene Bereitstellungsname wird als Name des gehosteten Diensts in Azure verwendet. Der Name muss im Azure-System eindeutig sein.
	
	![neuen gehosteten Dienst erstellen](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png)

	> [AZURE.NOTE]Im Dialogfeld **Add a deploy target** werden vorhandene gehostete Azure-Dienste unter dem Abschnitt **Choose existing deployment** aufgelistet; wenn Sie einen vorhandenen gehosteten Dienst auswählen, führt dies dazu, dass dieses Projekt zu diesem Dienst bereitgestellt wird.

	> [AZURE.NOTE]Wenn Sie **Enable RDP** auswählen und einen Benutzernamen und ein Kennwort bereitstellen, wird Remotedesktop für Ihre Umgebung ermöglicht.


## Bereitstellen zur Azure-Produktionsumgebung

1.  Wählen Sie die in den vorherigen Schritten erstellte Bereitstellung aus. Es wird ein Dialogfeld angezeigt, das Informationen zu dieser Bereitstellung enthält, sowie die Produktions-URL, die nach der Bereitstellung zu Azure verwendet wird.

	![eine Bereitstellung auswählen](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png)

2.  Wählen Sie **Deploy to Production environment**.

3.  Klicken Sie auf **Deploy**, um mit der Bereitstellung zu beginnen.

4.  Wenn Sie dieses Projekt erstmalig zu Azure bereitstellen, erhalten Sie eine Fehlermeldung **'No web.config found'**. Wählen Sie **Yes**, um die Datei zu erstellen. Dadurch wird eine "Web.cloud.config"-Datei zu Ihrem Projekt hinzugefügt.
	
	![Nachricht "Keine web.config-Datei gefunden"](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png)

5.  Wenn Sie dieses Projekt erstmalig zu Azure bereitstellen, erhalten Sie eine Fehlermeldung **'No 'csdef' file present'**. Wählen Sie **Yes**, um die .csdef-Datei zu erstellen. Dadurch wird eine 'ServiceDefinition.csdef'-Datei zu Ihrem Projekt hinzugefügt. "ServiceDefinition.csdef" ist eine Azure-spezifische Datei, die für die Veröffentlichung Ihrer Anwendung erforderlich ist. Weitere Informationen erhalten Sie unter [Cloud-Dienste][].

6.  Sie werden aufgefordert, die Instanzengröße für diese Anwendung auszuwählen. Wählen Sie **Small**, und klicken Sie dann auf **Create**. Weitere Details zu Azure-VM-Größen finden Sie unter [Konfiguration der Größe einer virtuellen Maschine][].

	![Werte für csdef-Datei angeben](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png)

7.  Der Bereitstellungseintrag zeigt den Status des Bereitstellungsprozess an. Nach der Fertigstellung wird die Bereitstellungs als **Active** angezeigt.

	![Bereitstellungsstatus](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png)

	> [AZURE.NOTE]Projekte, die über Cloud 9-IDE bereitgestellt werden, werden der GUID als Bereitstellungsname in Azure zugewiesen.

8.  Das Bereitstellungsdialogfeld enthält einen Link zur Produktions-URL. Klicken Sie nach Abschluss der Bereitstellung auf die URL, um zur in Azure ausgeführten Anwendung zu gelangen.

	![Azure-Produktions-URL-Link](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png)

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

	![Konsolenausgabe, die Staging-URL anzeigt](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png)

## Überführen der Aktualisierung zur Produktion über VIP-Austausch

Wenn ein Dienst zur Produktions- oder Stagingumgebung bereitgestellt wird, wird dem Dienst in dieser Umgebung eine virtuelle IP-Adresse (VIP) zugewiesen. Wenn Sie einen Dienst von der Bereitstellungsumgebung zur Produktionsumgebung überführen möchten, kann dies ohne erneute Bereitstellung erfolgen, indem ein VIP-Austausch erfolgt, welcher die Staging- und Produktionsbereitstellungen tauscht. Ein VIP-Austausch überführt die gestestete Staginganwendung ohne Ausfallzeiten in der Produktionsumgebung in die Produktion. Weitere Informationen finden Sie unter [Übersicht über das Verwalten von Bereitstellungen in Windows Azure.][]

1.  Klicken Sie im Bereitstellungsdialogfeld auf den Link **Open portal**, um das Azure-Verwaltungsportal zu öffnen.

	![Link vom Bereitstellungsdialogfeld zum Azure-Verwaltungsportal][Link from deploy dialog to Azure Management Portal]

2.  Melden Sie sich mit Ihren Anmeldedaten am Portal an.

3.  Wählen Sie links der Webseite **Gehostete Dienste, Speicherkonten CDN**, und klicken Sie dann auf **Gehostete Dienste**.

	![Azure-Verwaltungsportal][Azure Management Portal]

	Das Ergebnisfenster zeigt den gehosteten Dienst mit dem Namen, den Sie in Cloud9 angegeben haben, sowie zwei Bereitstellungen: eine mit dem **Umgebungs-**Wert **Staging**, die zweite mit **Produktion**.

4.  Für den VIP-Austausch wählen Sie den gehosteten Dienst aus, und klicken Sie dann auf **VIP austauschen** im Menüband.

	![VIP-Austausch](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png)

5.  Klicken Sie auf **OK** im VIP-Austauschdialogfeld, das angezeigt wird.

6.  Navigieren Sie zu Ihrer Produktionsanwendung. Sie werden bemerken, dass die Version der zuvor in der Stagingumgebung bereitgestellten Anwendung jetzt in der Produktion ist.

	![Produktionsanwendung, die auf Azure ausgeführt wird](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png)

## Verwenden von Remotedesktop

Wenn Sie bei der Erstellung der Bereitstellung RDP aktiviert und einen Benutzernamen sowie ein Kennwort angegeben haben, können Sie Remotedesktop verwenden, um eine Verbindung zum gehosteten Dienst herzustellen, indem Sie eine bestimmte Instanz auswählen und dann auf dem Menüband "Verbinden" auswählen.

![Mit einer Instanz verbinden](./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png)

Wenn Sie auf "Verbinden" klicken, werden Sie aufgefordert, eine .RDP-Datei zu öffnen oder herunterzuladen. Diese Datei enthält die erforderlichen Informationen für die Verbindung zu Ihrer Remotedesktopsitzung. Wenn Sie diese Datei auf einem Windows-System ausführen, werden Sie aufgefordert, den Benutzernamen und das Kennwort einzugeben, das Sie bei der Erstellung der Bereitstellung eingegeben haben. Sie werden dann mit dem Desktop der ausgewählten Instanz verbunden.

> [AZURE.NOTE]Die .RDP-Datei, welche für die Verbindung der gehosteten Instanz der Anwendung erforderlich ist, funktioniert nur mit der Remotedesktop-Anwendung in Windows.

## Beenden und Löschen der Anwendung

Bei Azure werden Rolleninstanzen pro genutzter Serverzeitstunde berechnet. Serverzeit wird genutzt, sobald die Anwendung bereitgestellt wird, selbst wenn die Instanzen nicht ausgeführt werden und sich im Status "beendet" befinden. Zusätzlich verbrauchen sowohl Produktions- als auch Stagingumgebungen Serverzeit.

Cloud9 fokussiert sich auf die Bereitstellung einer IDE-Schnittstelle und bietet keine direkte Methode, um eine Anwendung zu beenden oder zu löschen, wenn diese erstmal in Azure bereitgestellt wurde. Führen Sie folgende Schritte aus, um eine in Azure gehostete Anwendung zu löschen:

1.  Klicken Sie im Bereitstellungsdialogfeld auf den Link **Open portal**, um das Azure-Verwaltungsportal zu öffnen.

	![Link vom Bereitstellungsdialogfeld zum Azure-Verwaltungsportal](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png)

2.  Melden Sie sich mit Ihren Anmeldedaten am Portal an.

3.  Wählen Sie links der Webseite **Gehostete Dienste, Speicherkonten CDN**, und klicken Sie dann auf **Gehostete Dienste**.

4.  Wählen Sie die Stagingbereitstellung (angezeigt durch den **Umgebungswert**). Klicken Sie im Menüband auf **Löschen**, um die Anwendung zu löschen.

	![Bereitstellung löschen](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png)

5.  Wählen Sie die Produktionsbereitstellung, und klicken Sie auf **Löschen**, um diese Anwendung ebenfalls zu löschen.

## Zusätzliche Ressourcen

-   [Cloud9-Dokumentation][]


[Cloud9 IDE]: http://cloud9ide.com/
[Cloud9-IDE]: http://cloud9ide.com/
[Cloud-Dienste]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Konfiguration der Größe einer virtuellen Maschine]: http://msdn.microsoft.com/library/windowsazure/ee814754.aspx
[Übersicht über das Verwalten von Bereitstellungen in Windows Azure.]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
[Cloud9-Dokumentation]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409

<!--HONumber=54-->