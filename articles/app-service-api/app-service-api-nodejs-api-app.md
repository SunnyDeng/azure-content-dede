<properties
	pageTitle="Erstellen und Bereitstellen einer Node.js-API-App in Azure App Service"
	description="Lernen Sie, wie Sie ein Node.js-API-App-Paket erstellen und in Azure App Service bereitstellen."
	services="app-service\api"
	documentationCenter="node"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra "/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="get-started-article"
	ms.date="02/25/2015"
	ms.author="bradygaster"/>

# Erstellen und Bereitstellen einer Node.js-API-App in Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Voraussetzungen
1. Ausführung von [Node.js](http://nodejs.org) auf einem Entwicklungscomputer (in diesem Beispiel wird die Installation von Node.js Version 4.2.2 vorausgesetzt)
1. [GitHub](https://github.com/)-Konto
1. Microsoft Azure-Konto ([kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/))
1. Installation von Git auf Ihrer lokalen Entwicklungsarbeitsstation

## Setupanweisungen
Die folgenden Befehle sollten über die Node.js-Befehlszeile ausgeführt werden. Mit dem Swaggerize Yo-Generator können Sie das Gerüst für den grundlegenden Node.js-Code errichten, den Sie zum Warten der in einer Swagger-JSON-Datei definierten HTTP-Anforderungen benötigen.
 
1. Installieren Sie die NPM-Module **yo** und **generator-swaggerize** global.

        npm install -g yo
	    npm install -g generator-swaggerize
		
1. Klonen Sie das [GitHub-Repository mit dem Beispielcode](https://github.com/Azure-Samples/app-service-api-node-contact-list).

		git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git
				
1. Führen Sie den Befehl aus, um das Gerüst für die API basierend auf der Datei **api.json** zu erstellen, die im Quellcode enthalten ist. Die Datei **api.json** ist eine Swagger-Datei, die für die tatsächliche API steht, für die Sie im nächsten Schritt das Gerüst mit dem Befehl „yo swaggerize“ erstellen.

        yo swaggerize
        
    **Hinweis:** „API.json“ ist nicht das Gleiche wie die Datei *apiapp.json* aus dem API-Apps-Vorschauzeitrahmen.

1. Mit Swaggerize wird das Gerüst für die Handler und Config für die Swagger-Metadaten erstellt, die in **api.json** enthalten sind. Während der Gerüsterstellung werden Ihnen einige Fragen gestellt, z. B. zu Ihrem GitHub-Benutzernamen und Ihrer E-Mail-Adresse. Diese Informationen werden zum Generieren der Datei **package.json** im Ordner der Anwendung verwendet. Bei den Fragen während der Gerüsterstellung ist es am wichtigsten, dass Sie bei der entsprechenden Frage die Option **express** wählen. In diesem Beispiel wird nämlich das express-Ansichtsmodul verwendet, um später die Swagger-Hilfeseite zu generieren, wenn Ihre API-App in Azure (oder lokal) ausgeführt wird.

	![Swaggerize-Befehlszeile](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
1. Navigieren Sie zu dem Ordner, der das Codegerüst enthält (in diesem Fall der Unterordner *ContactList*). Installieren Sie dann das NPM-Modul **jsonpath**.

        npm install --save jsonpath
        
    Die Ergebnisse der Installation werden in der Befehlszeilenoberfläche angezeigt.

    ![Jsonpath installieren](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Installieren Sie das NPM-Modul **swaggerize-ui**.

        npm install --save swaggerize-ui
        
    Die Ergebnisse der Installation werden in der Befehlszeilenoberfläche angezeigt.

    ![Swaggerize-Benutzeroberfläche installieren](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

1. Kopieren Sie den Ordner **lib** aus dem Ordner **start** in den Ordner **ContactList**, der mit dem Tool für die Gerüsterstellung (Scaffolder) erstellt wurde.

1. Ersetzen Sie den Code in der Datei **handlers/contacts.js** durch den unten angegebenen Code. Für diesen Code werden die JSON-Daten verwendet, die in der Datei **lib/contacts.json** gespeichert sind. Diese Datei wird über **lib/contactRepository.js** bereitgestellt. Mit dem neuen contacts.js-Code unten wird auf HTTP-Anforderungen geantwortet, um alle Kontakte abzurufen, die diesen Code verwenden.

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Ersetzen Sie den Code in der Datei **handlers/contacts/{id}.js** durch den unten angegebenen Code. Darin wird **lib/contactRepository.js** zum Abrufen des Kontakts verwendet, der mit der HTTP-Anforderung angefordert wird, und anschließend wird er als JSON-Nutzlast zurückgegeben.

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Ersetzen Sie den Code in der Datei **server.js** durch den unten angegebenen Code. Beachten Sie, dass die an der Datei „server.js“ vorgenommenen Änderungen mit Kommentaren hervorgehoben werden, damit Sie die durchgeführten Änderungen verfolgen können.

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth change
            app.setHost(undefined); // sixth and final change
        });

1. Aktivieren Sie den Server mit der ausführbaren Node.js-Befehlszeilendatei.

        node server.js

    Wenn Sie diesen Befehl ausführen, wird der Node.js-HTTP-Server gestartet, und die Bereitstellung Ihrer API beginnt.

1. Wenn Sie zu ****http://localhost:8000/contacts** navigieren, wird die JSON-Ausgabe der Kontaktliste angezeigt (oder Sie werden je nach Browser zum Herunterladen aufgefordert).

    ![API-Aufruf alle Kontakte](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Wenn Sie zu ****http://localhost:8000/contacts/2** navigieren, wird der Kontakt angezeigt, der von diesem ID-Wert dargestellt wird.

    ![API-Aufruf bestimmter Kontakt](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Die Swagger-JSON-Daten werden über den Endpunkt **/swagger** bereitgestellt:

    ![Contacts Swagger JSON](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. Die Swagger-Benutzeroberfläche wird über den Endpunkt **/docs** bereitgestellt. Auf der Swagger-Benutzeroberfläche können Sie die umfassenden HTML-Clientfunktionen verwenden, um Ihre API zu testen.

    ![Swagger-Benutzeroberfläche](media/app-service-api-nodejs-api-app/swagger-ui.png)

## Erstellen einer neuen API-App im Azure-Portal
In diesem Abschnitt gehen wir den Prozess der Erstellung einer neuen, leeren API-App in Azure durch. Anschließend verknüpfen Sie die App mit einem Git-Repository, damit Sie die fortlaufende Bereitstellung Ihrer Codeänderungen ermöglichen können.

Das GitHub-Repository, aus dem Sie den Quellcode geklont haben, ist nicht dasselbe Repository, in das Sie den Code für die Bereitstellung per Pushvorgang befördern. Das GitHub-Beispielrepository enthielt den „Start“-Zustand des Codes. Nachdem Sie nun das Gerüst für den „End“-Zustand des Codes erstellt haben, müssen Sie diesen Code nur per Pushvorgang in das Git-Repository befördern, das Ihrer API-App zugeordnet ist. Im ersten Schritt erstellen Sie Ihre API-App mit dem Azure-Portal. Anschließend gehen Sie wie folgt vor:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/). 

1. Erstellen Sie eine neue API-App.

    ![Neues API-App-Portal](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

1. Sie können entweder Ihre neue API-App einer vorhandenen Ressourcengruppe bzw. einem App Service-Plan hinzufügen, oder Sie können eine neue Ressourcengruppe und einen App Service-Plan erstellen, wie dies unten im Screenshot dargestellt ist.

    ![Blatt für API-App-Erstellung](media/app-service-api-nodejs-api-app/api-app-creation-blade.png)

1. Nachdem Ihre API-App im Portal erstellt wurde, navigieren Sie zum Blatt mit den Einstellungen für Ihre API-App. Dies ist unten dargestellt.

    ![Portalnavigation zu Einstellungen](media/app-service-api-nodejs-api-app/portal-nav-to-settings.png)

1. Klicken Sie im Menü „Einstellungen“ auf das Navigationselement **Anmeldeinformationen für Bereitstellung**. Fügen Sie nach dem Öffnen des Blatts einen Benutzernamen und ein Kennwort hinzu. Sie verwenden diese Angaben zum Veröffentlichen des Node.js-Codes für Ihre API-App. Klicken Sie auf dem Blatt **Anmeldeinformationen für die Bereitstellung festlegen** dann auf die Schaltfläche **Speichern**.

    ![Anmeldeinformationen für die Bereitstellung](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. Nachdem Sie die Anmeldeinformationen für die Bereitstellung eingerichtet haben, können Sie ein Git-Repository erstellen, das Ihrem App Service zugeordnet ist. Bei jedem Senden von Code an dieses Repository per Pushvorgang stellt Azure App Service Ihre Änderungen direkt auf Ihrer API-App-Instanz bereit. Klicken Sie zum Erstellen eines Git-Repositorys für die Zuordnung zu Ihrer Website wie unten gezeigt auf dem Blatt mit dem Menü „Einstellungen“ auf das Menüelement **Dauerhafte Bereitstellung**. Wählen Sie auf dem Blatt **Quelle auswählen** dann die Option **Lokales Git-Repository**. Klicken Sie anschließend auf die Schaltfläche „OK“, um das Git-Repository zu erstellen.

    ![Git-Repository erstellen](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Nachdem das Git-Repository erstellt wurde, ändert sich das Blatt, und Ihre aktiven Bereitstellungen werden angezeigt. Da es sich um ein neues Repository handelt, sollten in der Liste keine aktiven Bereitstellungen enthalten sein.

    ![Keine aktiven Bereitstellungen](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Der letzte Schritt ist das Kopieren der Git-Repository-URL aus dem Portal. Navigieren Sie hierzu zum Blatt für Ihre neue API-App, und sehen Sie sich den Abschnitt **Zusammenfassung** des Blatts an. Im Abschnitt „Zusammenfassung“ sollte die **Git-Klon-URL** angezeigt werden. Daneben befindet sich ein Symbol, mit dem die URL in die Zwischenablage kopiert wird. Klicken Sie entweder auf dieses Symbol, um die URL zu kopieren (die Schaltfläche wird angezeigt, wenn Sie mit der Maus auf die URL zeigen), oder wählen Sie die gesamte URL aus, und kopieren Sie sie in die Zwischenablage.

    ![Git-URL aus Portal abrufen](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Hinweis**: Sie benötigen die Git-Klon-URL im nächsten Schritt. Sie sollten sie also kurz an einem geeigneten Speicherort speichern.

Nachdem Sie nun über eine neue API-App mit dazugehörigem Git-Repository verfügen, können Sie den Code per Pushvorgang in das Repository befördern und die Azure-Funktionen für die dauerhafte Bereitstellung zum automatischen Bereitstellen Ihrer Änderungen verwenden.

## Bereitstellen Ihres API-App-Codes für Azure
Indem Sie die integrierten Azure App Service-Funktionen für die dauerhafte Bereitstellung verwenden, können Sie für Ihren Code einfach einen Commit in ein Git-Repository durchführen, das Ihrem App Service zugeordnet ist. Azure wählt dann Ihren Quellcode aus und stellt ihn für die API-App bereit.

1. Kopieren Sie den Ordner **src/end/ContactList**, der bei der swaggerize-Gerüsterstellung erstellt wurde, auf Ihren Desktop oder in einen anderen Ordner. Sie erstellen nämlich ein neues lokales Git-Repository für den Code, der außerhalb des Hauptrepositorys vorhanden sein soll, das Sie über GitHub geklont haben und das den Code für die ersten Schritte enthält. 

1. Verwenden Sie die Node.js-Befehlszeilenoberfläche, um zum neuen Ordner zu navigieren. Führen Sie darin den folgenden Befehl aus, um ein neues lokales Git-Repository zu erstellen.

        git init

    Mit diesem Befehl wird ein lokales Git-Repository erstellt, und es wird eine Bestätigung angezeigt, dass Ihr neues Repository initialisiert wurde.

    ![Neues lokales Git-Repository](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Verwenden Sie die Node.js-Befehlszeilenoberfläche zum Ausführen des folgenden Befehls, mit dem Ihrem lokalen Repository ein Git-Remoterepository hinzugefügt wird. Das Remoterepository ist das Repository, das Sie eben erstellt und Ihrer API-App zugeordnet haben, die in Azure ausgeführt wird.

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Hinweis**: Es ist ratsam, die obige Zeichenfolge „YOUR\_GIT\_CLONE\_URL\_HERE“ durch Ihre eigene Git-Klon-URL zu ersetzen, die Sie vorhin kopiert haben.

1. Führen Sie als Nächstes die beiden unten angegebenen Befehle über die Node.js-Befehlszeilenoberfläche aus.

        git add .
        git commit -m "initial revision"

    Nachdem diese beiden Befehle abgeschlossen sind, sollte in Ihrem Befehlszeilenfenster eine Anzeige erscheinen, die dem unten angegebenen Screenshot ähnelt.

    ![Git-Commit-Ausgabe](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Führen Sie den folgenden Befehl über die Node.js-Befehlszeile aus, um Ihren Code per Pushvorgang zu Azure zu befördern, wo eine Bereitstellung für Ihre API-App ausgelöst wird. Wenn Sie zum Eingeben eines Kennworts aufgefordert werden, nutzen Sie das Kennwort, das Sie oben zum Erstellen Ihrer Anmeldeinformationen für die Bereitstellung im Azure-Portal verwendet haben.

        git push azure master

1. Wenn Sie zurück zum Blatt **Dauerhafte Bereitstellung** für Ihre API-App navigieren, sehen Sie, dass die Bereitstellung durchgeführt wird.

    ![Bereitstellung wird durchgeführt](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Gleichzeitig spiegelt die Node.js-Befehlszeile den Status Ihrer Bereitstellung wider, während sie durchgeführt wird.

    ![Node.js-Bereitstellung wird durchgeführt](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

1. Nach Abschluss der Bereitstellung spiegelt das Blatt **Dauerhafte Bereitstellung** die erfolgreiche Bereitstellung der Codeänderungen für Ihre API-App wider. Kopieren Sie die **URL** im Abschnitt **Zusammenfassung** des Blatts für Ihre API-App.

    ![Bereitstellung abgeschlossen](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Geben Sie die URL Ihres Contacts-API-Aufrufs mit einem REST-API-Client an, z. B. Postman oder Fiddler (oder Ihrem Webbrowser). Dies sollte der Endpunkt **/contacts** Ihrer API-App sein.

    **Hinweis:** Die URL ähnelt http://myapiapp.azurewebsites.net/contacts.

    Wenn Sie eine GET-Anforderung für diesen Endpunkt ausgeben, sollte die JSON-Ausgabe Ihrer API-App angezeigt werden.

    ![Postman trifft auf API](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

## Nächste Schritte

Sie haben mit Node.js erfolgreich Ihre erste API-App erstellt und bereitgestellt. Im nächsten Tutorial der Reihe „Erste Schritte mit API-Apps“ wird gezeigt, wie Sie [API-Apps über JavaScript-Clients mit CORS nutzen](app-service-api-cors-consume-javascript.md).

Aufbauend auf diesem Beispiel haben Sie die Möglichkeit, den Handlern Code zum Speichern Ihrer Daten in einer Datenbank oder auf der Festplatte Ihrer API-App-Instanz hinzuzufügen. Nachdem Sie die fortlaufende Bereitstellung eingerichtet haben, ist das Ändern und Erweitern der API-App-Funktionalität so einfach wie das Ändern und Befördern des Codes in Ihr Git-Repository.

<!---HONumber=AcomDC_0302_2016-->