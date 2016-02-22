<properties
	pageTitle="Upgrade von Mobile Services auf Azure App Service – Node.js"
	description="Erfahren Sie, wie einfach Sie Ihre Mobile Services-Anwendung auf eine mobile App Service-App aktualisieren können."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="chrande"/>

# Aktualisieren eines vorhandenen Node.js-Azure Mobile Service auf App Service

Mobile App Service-Apps sind eine neue Methode zum Erstellen mobiler Anwendungen mit Microsoft Azure. Weitere Informationen finden Sie unter [Was sind Mobile Apps?].

In diesem Thema wird beschrieben, wie Sie eine vorhandene Node.js-Back-End-Anwendung von Azure Mobile Services auf das neue Mobile App Service-Apps upgraden. Während Sie dieses Upgrade ausführen, können Sie Ihre vorhandene Mobile Services-Anwendung weiter betreiben.

Wenn ein Mobil-Back-End auf Azure App Service aktualisiert wird, hat dieses Zugriff auf alle App Service-Features, und die Abrechnung erfolgt gemäß [App Service-Preisen], nicht gemäß den Mobile Services-Preisen.

## Vergleich von Migration und Upgrade

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Es wird empfohlen, vor einem Upgrade eine [Migration auszuführen](app-service-mobile-migrating-from-mobile-services.md). Auf diese Weise können Sie beide Versionen der Anwendung in den gleichen App Service-Plan übernehmen, ohne dass zusätzliche Kosten anfallen.

### Verbesserungen im Mobile Apps-Server-SDK für Node.js

Bei einem Upgrade auf das neue [Mobile Apps-SDK](https://www.npmjs.com/package/azure-mobile-apps) profitieren Sie von vielen Verbesserungen:

- Das auf dem [Express-Framework](http://expressjs.com/en/index.html) basierende neue Node-SDK ist besonders schlank und so konzipiert, dass es mit künftigen neuen Node-Versionen Schritt hält. Sie können das Anwendungsverhalten mit der Express-Middleware anpassen.

- Beträchtliche Leistungssteigerungen im Vergleich zum Mobile Services SDK.

- Sie können nun eine Website zusammen mit dem Mobil-Back-End hosten. Zudem ist es einfach, das Azure Mobile Apps SDK einer beliebigen vorhandenen expressv4-Anwendung hinzufügen.

- Das Mobile Apps SDK ist für die plattformübergreifende und lokale Entwicklung ausgelegt. Es kann lokal auf Windows-, Linux- und OS X-Plattformen ausgeführt und zur Entwicklung genutzt werden. Es ist nun einfach, häufig verwendete Node-Entwicklungstechniken zu verwenden, z. B. das Ausführen von [Mocha](https://mochajs.org/)-Tests vor der Bereitstellung.

- Sie können Redis mit systemeigenen Module wie [hiredis](https://www.npmjs.com/package/hiredis) verwenden. Sie müssen keine Binärdateien in Ihre Bereitstellungspakete einbeziehen, da App Service Ihre Npm-Pakete für Sie installiert.

## <a name="overview"></a>Grundlegende Übersicht über den Upgradevorgang

Anders als beim Mobile Apps-SDK für .NET lässt sich das Upgraden eines Node-Back-Ends von Mobile Services auf Mobile Apps nicht durch einen einfachen Austausch von Paketen bewerkstelligen. Sie besitzen nun den gesamten Anwendungsstapel, der sonst von Azure gesteuert wurde. Daher müssen Sie eine Express-Basis-App zum Hosten Ihres Mobil-Back-Ends erstellen. Für die Tabellen- und API-Controller sind die Konzepte ähnlich, aber Sie müssen jetzt Tabellenobjekte exportieren, und die Funktions-APIs wurden leicht geändert. Dieser Artikel führt Sie schrittweise durch die grundlegenden Strategien beim Upgrade. Bevor Sie die ersten Schritte für die Migration ausführen, sollten Sie jedoch die Anleitung [Verwenden des Node-SDK](app-service-mobile-node-backend-how-to-use-server-sdk.md) lesen.

>[AZURE.TIP] Sie sollten die restlichen Abschnitte in diesem Thema vollständig lesen, bevor Sie mit einem Upgrade beginnen. Notieren Sie sich alle Features, die Sie verwenden möchten, und die nachfolgend behandelt werden.

Die Mobile Services-Client-SDKs sind **nicht** mit dem neuen Mobile Apps-Server-SDK kompatibel. Um die Kontinuität von Diensten für Ihre App zu gewährleisten, sollten Sie keine Änderungen auf einer Website veröffentlichen, die derzeit veröffentlichte Clients bereitstellt. Stattdessen sollten Sie eine neue mobile App erstellen, die als Duplikat dient. Sie können diese Anwendung in den gleichen App Service-Plan aufnehmen, um zu vermeiden, dass zusätzliche Kosten anfallen.

Anschließend verfügen Sie über zwei Versionen der Anwendung: eine unveränderte Version, die veröffentlichte praktisch genutzte Apps bedient, und eine zweite Version, die Sie upgraden und als Ziel für eine neue Clientversion verwenden. Sie können den eigenen Code im eigenen Tempo verschieben und testen, aber sie sollten sicherstellen, dass alle Fehlerbehebungen, die Sie vornehmen, auf beide Versionen angewendet werden. Wenn Sie das Gefühl haben, dass die gewünschte Anzahl von praktisch genutzten Client-Apps auf die neueste Version aktualisiert wurde, können Sie die ursprüngliche migrierte App je nach Wunsch löschen. Solange die zweite Version im gleichen App Service-Plan gehostet wird wie Ihre vorhandene mobile App, fallen dafür keine zusätzlichen Kosten an.

Der vollständige Überblick über den Upgradevorgang sieht wie folgt aus:

1. Erstellen Sie eine neue mobile App.
2. Aktualisieren Sie das Projekt zur Verwendung der neuen Server-SDKs.
3. Veröffentlichen Sie das Projekt auf der neuen mobilen App.
4. Veröffentlichen Sie eine neue Version Ihrer Clientanwendung, die die neue mobile App nutzt.
5. (Optional) Löschen Sie die ursprünglich migrierte Mobile Service-App.

Löschung kann auftreten, wenn Sie keinen Datenverkehr auf Ihrer ursprünglich migrierten Mobile Service-App feststellen.

## <a name="mobile-app-version"></a> Starten des Upgrades
Der erste Schritt beim Upgrade besteht darin, die Mobile App-Ressource zum Hosten der neuen Version Ihrer Anwendung zu erstellen. Wenn Sie bereits einen vorhandenen mobilen Dienst migriert haben, sollten Sie diese Version im gleichen Hostingplan erstellen. Öffnen Sie das [Azure-Portal], und navigieren Sie zur migrierten Anwendung. Notieren Sie sich den App Service-Plan, in dem sie ausgeführt wird.

### Erstellen einer zweiten Anwendungsinstanz
Erstellen Sie als Nächstes eine zweite Anwendungsinstanz. Wenn Sie zur Auswahl Ihres App Service-Plans oder „Hostingplans“ aufgefordert werden, wählen Sie den Plan der migrierten Anwendung aus.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Sie möchten sehr wahrscheinlich dieselbe Datenbank und denselben Notification Hub wie in Mobile Services verwenden. Zum Kopieren dieser Werte öffnen Sie das [Azure-Portal], navigieren Sie zur ursprünglichen Anwendung, und klicken Sie dann auf **Einstellungen** > **Anwendungseinstellungen**. Kopieren Sie unterhalb von **Verbindungszeichenfolgen** die Werte `MS_NotificationHubConnectionString` und `MS_TableConnectionString`. Navigieren Sie zu Ihrer neuen Upgradewebsite, und fügen Sie die Werte ein, wobei alle vorhandenen Werte überschrieben werden. Wiederholen Sie diesen Vorgang für alle anderen für Ihre App erforderlichen Anwendungseinstellungen. Wenn Sie keinen migrierten Dienst verwenden, finden Sie die Verbindungszeichenfolgen und App-Einstellungen im [Azure-Portal] im Abschnitt „Mobile Services“ auf der Registerkarte **Konfigurieren**.

### Erstellen eines Mobile App-Basis-Back-Ends mit Node

Jedes Node.js-Back-End von Azure Mobile App Service-Apps verhält sich am Anfang wie eine ExpressJS-Anwendung. Sie können eine [Express](http://expressjs.com/en/index.html)-Basisanwendung wie folgt erstellen:

1. Erstellen Sie in einem Befehlsfenster oder PowerShell-Fenster ein neues Verzeichnis für Ihr Projekt.

        mkdir basicapp

2. Führen Sie „npm init“ aus, um die Paketstruktur zu initialisieren.

        cd basicapp
        npm init

    Im Rahmen des Befehls „npm init“ werden einige Fragen gestellt, um das Projekt initialisieren zu können. Unten ist eine Beispielausgabe angegeben.

    ![Ausgabe von npm init][0]

3. Installieren Sie die express- und azure-mobile-apps-Bibliotheken aus dem Repository „npm“.

        npm install --save express azure-mobile-apps

4. Erstellen Sie die Datei „app.js“, um den einfachen mobilen Server zu implementieren.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Important all tables in the 'tables' directory
        mobile.tables.import('./tables');
        mobile.api.import('./api');

        // Provide initialization of any tables that are statically defined
        mobile.tables.initialize().then(function () {
           // Add the mobile API so it is accessible as a Web API
           app.use(mobile);

           // Start listening on HTTP
           var port = process.env.PORT || 3000;
           app.listen(port, function () {
               console.log('Now listening on ', port)
           });
        });

Weitere Beispiele finden Sie in unseren [GitHub-Repository](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples).

## Aktualisieren des Serverprojekts

Mobile Apps stellt ein neues [Mobile App-Server-SDK] bereit, das im Wesentlichen die gleichen Funktionen wie die Mobile Services-Runtime bietet. Allerdings sind Sie nun der Besitzer der Runtime, Mobile Apps zwingt Ihnen keine Node-Version und keine Codeupdates auf. Wenn Sie die oben aufgeführten Schritte ausgeführt haben, verfügen Sie über eine Basisversion der Node-Mobil-Runtime. Sie können jetzt Tabellen und die API-Logik aus Mobile Services zu Mobile Apps verschieben, Ihre Serverkonfiguration anpassen, den Push aktivieren, die Authentifizierung konfigurieren usw.

### Basiskonfiguration

Der Server verfügt über sehr viele Konfigurationseinstellungen, aber es gibt dort auch eine ganze Reihe Standardwerte, die Ihnen die ersten Schritte erleichtern. Viele der Einstellungen werden für Sie im [Azure-Portal] über die Menüs **Daten**, **Authentifizierung/Autorisierung** und **Push** festgelegt. Wenn Sie bei der lokalen Entwicklung Daten, Authentifizierung und Push verwenden möchten, müssen Sie möglicherweise Ihre lokale Entwicklungsumgebung entsprechend konfigurieren.

Sie können Ihre Serverkonfiguration über Umgebungsvariable anpassen, die Sie in Ihrem Mobile App-Back-End über die App-Einstellungen festlegen können.

Sie können das Mobile Apps-SDK weiter anpassen, indem Sie ein [Konfigurationsobjekt](http://azure.github.io/azure-mobile-apps-node/global.html#configuration) an den Initialisierer übergeben oder im Stamm des Projekts [eine Datei mit dem Namen „azureMobile.js“ erstellen](app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-config-localdev).

### Arbeiten mit Daten und Tabellen

Das SDK enthält in der Standardeinstellung einen In-Memory-Datenanbieter, um den Start zu vereinfachen und zu beschleunigen. Sie sollten frühzeitig zur Nutzung einer SQL-Datenbank übergehen, weil der In-Memory-Anbieter bei einem Neustart alle Daten verliert und über mehrere Instanzen hinweg keine Konsistenz gewährleisten kann.

Um Ihre Geschäftslogik aus dem Mobile Service zu Mobile Apps zu verschieben, müssen Sie zuerst im Verzeichnis `./tables` eine Datei mit dem Namen Ihrer Tabelle und der Erweiterung „.js“ erstellen. Ein vollständiges Beispiel für eine Mobile App-Tabelle finden Sie auf [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/todo/tables/TodoItem.js). Die einfachste Version sieht so aus:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    module.exports = table;

Zum Portieren Ihrer Logik benötigen Sie für jede Ihrer `<tablename>.<operation>.js` eine Funktion für Ihre Tabelle. Nehmen wir als Beispiel eine read-Funktion.

Und zwar in einem Mobile Service mit einer TodoItem-Tabelle und einem read-Vorgang, der Elemente auf Basis der userID wie folgt filtert:

    function(query, user, request) {
        query.where({ userId: user.userId});
        request.execute();
    }

Die Funktion, die wir dem Azure Mobile Apps-Tabellencode hinzufügen, sieht wie folgt aus:

    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

Abfrage, Benutzer und Anforderung werden in einem Kontext kombiniert. Die folgenden Felder sind im Kontextobjekt verfügbar:

| Feld | Typ | Beschreibung |
| :------ | :--------------------- | :---------- |
| query | Queryjs/Abfrage | Die analysierte OData-Abfrage. |
| id | Zeichenfolge oder Zahl | Die ID, die der Anforderung zugeordnet ist. |
| item | Objekt | Das Element, das eingefügt oder gelöscht wird. |
| req | express.Request | Das aktuelle Expressanforderungsobjekt. |
| res | express.Response | Die aktuelle Expressantwortobjekt. |
| data | data | Der konfigurierte Datenanbieter. |
| tables | Funktion | Eine Funktion, die einen Tabellennamen als Zeichenfolge akzeptiert und ein Tabellenzugriffsobjekt zurückgibt. |
| user | Auth/Benutzer | Das authentifizierte Benutzerobjekt. |
| results | Objekt | Die Ergebnisse der Ausführung. |
| push | NotificationHubService | Der Notification Hubs-Dienst, sofern konfiguriert. |

Weitere Informationen finden Sie in der [aktuellen API-Dokumentation](http://azure.github.io/azure-mobile-apps-node).

### CORS

CORS (Cross-Origin Resource Sharing) kann über die [CORS-Konfigurationseinstellung](http://azure.github.io/azure-mobile-apps-node/global.html#corsConfiguration) im SDK aktiviert werden.

Bei der Verwendung von CORS ist ein wichtiger Aspekt zu beachten: Die Header `eTag` und `Location` müssen zulässig sein, damit die Client-SDKs ordnungsgemäß funktionieren.

### Pushbenachrichtigungen

Beim Azure Notification Hubs-SDK wurden seit Mobile Services einige wichtige Updates vorgenommen, sodass einige Notification Hubs-Funktionssignaturen nun anders sein können. Davon abgesehen sind die Funktionen ganz ähnlich wie bei Mobile Services. Das Azure Mobile Apps-SDK stellt eine Notification Hubs-Instanz bereit, wenn die App-Einstellung für Notification Hubs vorhanden ist, und macht sie auf `context.push` verfügbar. Ein Beispiel finden Sie auf [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/push-on-insert/tables/TodoItem.js). Der relevante Abschnitt ist:

    table.insert(function (context) {
        // For details of the Notification Hubs JavaScript SDK,
        // see https://azure.microsoft.com/de-DE/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/
        logger.silly('Running TodoItem.insert');

        // This push uses a template mechanism, so we need a template/
        var payload = '<toast><visual><binding template="Toast01"><text id="1">INSERT</text></binding></visual></toast>';

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    context.push.wns.send(null, payload, 'wns/toast', function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.silly('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
    });


### Geplante Aufträge
Geplante Aufträge sind nicht in Mobile Apps integriert. Deshalb müssen alle vorhandenen Aufträge aus Ihrem Mobile Service-Back-End manuell aktualisiert werden. Eine Option hierbei ist das Erstellen eines geplanten [WebJob] auf der Mobile App-Codesite. Sie können auch eine API einrichten, die Ihren Auftragscode enthält, und den [Azure Scheduler] zum Ansprechen dieses Endpunkts zum erwarteten Zeitpunkt konfigurieren.

## <a name="authentication"></a>Überlegungen zur Authentifizierung

Die Authentifizierungskomponenten von Mobile Services wurden jetzt in das App Service-Feature „Authentifizierung/Autorisierung“ verschoben. Weitere Informationen zum Aktivieren dieses Features für Ihre Website finden Sie im Thema [Hinzufügen von Authentifizierung zu Ihrer mobilen App](app-service-mobile-ios-get-started-users.md).

Für einige Anbieter, z. B. AAD, Facebook und Google, sollten Sie die vorhandene Registrierung aus der kopierten Anwendung nutzen können. Sie müssen lediglich zum Portal des Identitätsanbieters navigieren und eine neue Umleitungs-URL zur Registrierung hinzufügen. Anschließend konfigurieren Sie die App Service-Authentifizierung/Autorisierung mit der Client-ID und dem geheimen Schlüssel.

### Autorisierung von Controlleraktionen und Benutzeridentität

Sie können den Zugriff auf Ihre Tabelle einschränken, indem Sie ihn auf Tabellenebene mit `table.access = 'authenticated';` festlegen. Ein vollständiges Beispiel dafür finden Sie auf [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/authentication/tables/TodoItem.js).

Auf die Benutzeridentitätsinformationen können Sie über die `user.getIdentity`-Methode zugreifen, die [hier](http://azure.github.io/azure-mobile-apps-node/module-azure-mobile-apps_auth_user.html#~getIdentity) beschrieben ist.

## <a name="updating-clients"></a>Aktualisieren von Clients
Sobald Sie über ein betriebsbereites Mobile App-Back-End verfügen, können Sie an einer neuen Version Ihrer Clientanwendung arbeiten, die dieses nutzt. Mobile Apps enthält außerdem eine neue Version der Client-SDKs. Wie bei dem oben beschriebenen Serverupgrade müssen Sie vor der Installation der Mobile Apps-Versionen alle Verweise auf die Mobile Services-SDKs entfernen.

Einer der wichtigsten Unterschiede zwischen den Versionen ist, dass die Konstruktoren keinen Anwendungsschlüssel mehr benötigen. Sie brauchen jetzt nur noch die URL der mobilen App zu übergeben. Bei den .NET-Clients lautet der `MobileServiceClient`-Konstruktor z. B. nun:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Informationen zum Installieren der neuen SDKs und zum Verwenden der neuen Struktur finden Sie unter den folgenden Links:

- [iOS 3.0.0 oder höher](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) 2.0.0 oder höher](app-service-mobile-dotnet-how-to-use-client-library.md)

Wenn Ihre Anwendung Pushbenachrichtigungen verwendet, notieren Sie sich die speziellen Registrierungsanweisungen für jede Plattform, weil auch dort einige Änderungen vorgenommen wurden.

Wenn die neue Clientversion bereit ist, testen Sie sie mit dem aktualisierten Serverprojekt. Nachdem Sie sich vergewissert haben, dass sie funktioniert, können Sie eine neue Version der Anwendung für die Kunden freigeben. Nachdem Ihre Kunden diese Updates erhalten haben, können Sie die Mobile Services-Version Ihrer App schließlich löschen. An diesem Punkt haben Sie die vollständige Aktualisierung auf eine mobile App Service-App durchgeführt und dafür das aktuelle Mobile Apps-Server-SDK verwendet.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Was sind Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /de-DE/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App-Server-SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /de-DE/documentation/services/scheduler/
[WebJob]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[App Service-Preisen]: https://azure.microsoft.com/de-DE/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

[Azure-Portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/de-DE/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/de-DE/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_0211_2016-->