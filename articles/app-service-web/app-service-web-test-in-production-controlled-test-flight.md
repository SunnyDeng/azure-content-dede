<properties
	pageTitle="Test-Flighting-Bereitstellung (Betatesting) in Azure App Service"
	description="In diesem umfassenden Tutorial erfahren Sie, wie Sie Test-Flightings für neue Features in Ihrer App oder Betatests für Updates durchführen. Hierfür werden App Service-Funktionen wie kontinuierliche Veröffentlichung, Slots, Datenverkehrsrouting sowie die Integration von Application Insights miteinander kombiniert."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="cephalin"/>
# Test-Flighting-Bereitstellung (Betatesting) in Azure App Service

In diesem Tutorial erfahren Sie, wie Sie *Test-Flighting-Bereitstellungen* einrichten, indem Sie die verschiedenen Funktionen von [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) und [Azure Application Insights](/services/application-insights/) integrieren.

*Test-Flighting* ist ein Bereitstellungsprozess, bei dem eine neue Funktion oder eine Änderung mit einer begrenzten Anzahl echter Kunden getestet wird, und ist ein wichtiger Test in einem Produktionsszenario. Der Prozess ist mit dem Betatesting verwandt und wird zuweilen auch als „kontrollierter Test-Flight“ bezeichnet. Viele große Unternehmen mit Webpräsenz nutzen diese Vorgehensweise, um Updates ihrer Apps im Rahmen ihrer [agilen Bereitstellung](https://en.wikipedia.org/wiki/Agile_software_development) so früh wie möglich zu überprüfen. Mit Azure App Service können Sie das Testen in der Produktionsumgebung in die kontinuierliche Veröffentlichung und Application Insights integrieren, um das gleiche DevOps-Szenario zu implementieren. Dieser Ansatz bietet folgende Vorteile:

- **Sie erhalten wertvolles Feedback, _bevor_ Updates für die Produktion freigegeben werden**. Es ist gut, wenn Sie direkt nach der Veröffentlichung Feedback bekommen – noch viel besser ist es aber, Feedback bereits vor der Veröffentlichung zu erhalten. Sie können Updates zu jedem Zeitpunkt im Produktlebenszyklus mit echtem Benutzerdatenverkehr und echtem Benutzerverhalten testen.
- **Verbessern Sie die [kontinuierliche testgesteuerte Entwicklung (Continuous Test-Driven Development, CTDD)](https://en.wikipedia.org/wiki/Continuous_test-driven_development)**: Durch Integrieren des Testens in der Produktionsumgebung in die kontinuierliche Integration und Instrumentation mit Application Insights erfolgt die Überprüfung der Benutzer frühzeitig im Produktlebenszyklus und automatisch. So können Sie den Zeitaufwand für manuelle Testausführungen reduzieren.
- **Optimieren Sie den Testworkflow**: Durch Automatisieren des Testens in der Produktionsumgebung mit Instrumentation einer kontinuierlichen Überwachung können Sie verschiedene Testziele in einem einzigen Prozess erreichen, wie z. B. [Integration](https://en.wikipedia.org/wiki/Integration_testing), [Regression](https://en.wikipedia.org/wiki/Regression_testing), [Nutzbarkeit](https://en.wikipedia.org/wiki/Usability_testing), Barrierefreiheit, Lokalisierung, [Leistung](https://en.wikipedia.org/wiki/Software_performance_testing), [Sicherheit](https://en.wikipedia.org/wiki/Security_testing) und [Akzeptanz](https://en.wikipedia.org/wiki/Acceptance_testing).

Bei einer Test-Flighting-Bereitstellung geht es nicht nur um das Weiterleiten von Livedatenverkehr. Ziel einer solchen Bereitstellung ist es, so schnell wie möglich wichtige Einblicke zu erhalten, z. B. zu unerwarteten Fehlern, Leistungsabfällen oder Problemen mit der Benutzerfreundlichkeit. Denken Sie daran: Sie haben es mit echten Kunden zu tun. Stellen Sie also sicher, dass Sie Ihre Test-Flighting-Bereitstellung so einrichten, dass alle erforderlichen Daten erfasst werden, damit Sie eine fundierte Entscheidung zum nächsten Schritt treffen können. Dieses Tutorial zeigt, wie Sie Daten mithilfe von Application Insights erfassen, Sie können jedoch auch New Relic oder andere Technologien verwenden, die sich für Ihr Szenario eignen.

## Aufgaben

In diesem Tutorial lernen Sie, wie Sie folgende Szenarien kombinieren, um Ihre App Service-App in der Produktionsumgebung zu testen:

- [Weiterleiten von Produktionsdatenverkehr](app-service-web-test-in-production-get-start.md) an Ihre Beta-App
- [Instrumentieren Ihrer App](app-insights-web-track-usage.md), um hilfreiche Metriken abzurufen
- Kontinuierliche Bereitstellung Ihrer Beta-App und Verfolgen von Livemetriken der App
- Vergleichen von Metriken zwischen der Produktions-App und der Beta-App, um zu erfahren, welche Ergebnisse durch Codeänderungen erzielt werden

## Sie benötigen Folgendes

-	Ein Azure-Konto
-	Ein [GitHub](https://github.com/)-Konto
- Visual Studio 2015 – Sie können die [Community-Edition](https://www.visualstudio.com/de-DE/products/visual-studio-express-vs.aspx) herunterladen
-	Git-Shell (mit [GitHub für Windows](https://windows.github.com/) installiert) – Ermöglicht Ihnen, Git- und PowerShell-Befehle in derselben Sitzung auszuführen.
-	Neueste [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi)-Version
-	Grundlegende Kenntnisse der folgenden Komponenten:
	-	Bereitstellung der [Azure-Ressourcen-Manager](resource-group-overview.md)-Vorlage (siehe [Vorhersagbares Bereitstellen einer komplexen Anwendung in Azure](app-service-deploy-complex-application-predictably.md))
	-	[Git](http://git-scm.com/documentation)
	-	[PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen: + Sie können [kostenlos ein Azure-Konto erstellen](/pricing/free-trial/?WT.mc_id=A261C142F): – Sie erhalten ein Guthaben, das Sie zum Ausprobieren der kostenpflichtigen Azure-Dienste nutzen können. Sie können das Konto behalten und weiterhin kostenlose Azure-Dienste wie z. B. Web-Apps nutzen, wenn das Guthaben aufgebraucht sind. + Sie können von [Vorteilen für Visual Studio-Abonnenten](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) profitieren – Über Ihr Visual Studio-Abonnement erhalten Sie jeden Monat Gutschriften, die Sie für kostenpflichtige Azure-Dienste einsetzen können.
>
> Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Einrichten der Produktions-Web-App

>[AZURE.NOTE]Das Skript in diesem Tutorial konfiguriert automatisch die kontinuierliche Veröffentlichung aus Ihrem GitHub-Repository. Dies erfordert, dass Ihre Anmeldeinformationen für GitHub bereits in Azure gespeichert sind. Andernfalls misslingt die skriptgesteuerte Bereitstellung, wenn versucht wird, Quellcodeverwaltungseinstellungen für die Web-Apps zu konfigurieren.
>
>Um Ihre GitHub-Anmeldeinformationen in Azure zu speichern, erstellen Sie eine Web-App im [Azure-Portal](https://portal.azure.com) und [konfigurieren die GitHub-Bereitstellung](web-sites-publish-source-control.md#Step7). Dies ist nur einmalig erforderlich.

In einem typischen DevOps-Szenario verfügen Sie über eine Anwendung, die aktiv in Azure ausgeführt wird und an der Sie über die fortlaufende Veröffentlichung Änderungen vornehmen möchten. In diesem Szenario stellen Sie eine Vorlage in der Produktionsumgebung bereit, die Sie entwickelt und getestet haben.

1.	Erstellen Sie Ihre eigene Verzweigung des [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp)-Repositorys. Informationen zum Erstellen Ihrer Verzweigung finden Sie unter [Verzweigen eines Repositorys](https://help.github.com/articles/fork-a-repo/). Sobald die Verzweigung erstellt wurde, können Sie sie in Ihrem Browser einsehen.

	![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.	Öffnen Sie eine Git-Shell-Sitzung. Wenn Sie noch nicht über Git-Shell verfügen, installieren Sie nun [GitHub für Windows](https://windows.github.com/).
3.	Erstellen Sie einen lokalen Klon Ihrer Verzweigung, indem Sie den folgenden Befehl ausführen:

        git clone https://github.com/<your_fork>/ToDoApp.git

4.	Nachdem Sie den lokalen Klon erstellt haben, navigieren Sie zu *&lt;Repositorystammverzeichnis>*\\ARMTemplates, und führen Sie das Skript „deploy.ps1“ mit einem eindeutigen Suffix aus, wie im Folgenden gezeigt:

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>

4.	Geben Sie bei Aufforderung den gewünschten Benutzernamen und das Kennwort für den Datenbankzugriff ein. Merken Sie sich die Datenbankanmeldeinformationen, da Sie sie beim Aktualisieren der Ressourcengruppe angeben müssen.

	Der Fortschritt der Bereitstellung verschiedener Azure-Ressourcen sollte angezeigt werden. Sobald die Bereitstellung abgeschlossen ist, startet das Skript die Anwendung im Browser, und Sie hören einen Signalton. ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)

6.	Zurück in der Git-Shell-Sitzung führen Sie Folgendes aus:

        .\swap –Name ToDoApp<your_suffix>

	![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)

7.	Wenn das Skript abgeschlossen ist, navigieren Sie zurück zur Adresse des Front-Ends (http://ToDoApp*&lt;your_suffix>*.azurewebsites.net/), um zu prüfen, ob die Anwendung in der Produktionsumgebung ausgeführt wird.
5.	Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und sehen Sie sich an, was erstellt wurde.

	Sie sollten zwei Web-Apps in derselben Ressourcengruppe sehen, von denen eine das Suffix `Api` im Namen enthält. Wenn Sie die Ressourcengruppenansicht betrachten, sehen Sie auch SQL-Datenbank und -Server, den App Service-Plan und die Stagingslots für die Web-Apps. Navigieren Sie durch die verschiedenen Ressourcen, und vergleichen Sie diese mit *&lt;Repositorystammverzeichnis>\\ARMTemplates\\ProdAndStage.json*, um zu prüfen, wie sie in der Vorlage konfiguriert sind.

	![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

Sie haben die Produktions-App eingerichtet. Angenommen, Sie erhalten Rückmeldungen von Kunden, Ihre App sei nicht sehr benutzerfreundlich. Das möchten Sie untersuchen. Sie werden also Ihre App instrumentieren, damit diese Ihnen Feedback bietet.

## Untersuchen: Instrumentieren Ihrer Client-App für Überwachung/Metriken

5. Öffnen Sie *&lt;Repositorystammverzeichnis>*\\src\\MultiChannelToDo.sln in Visual Studio.
6. Stellen Sie alle NuGet-Pakete wieder her, indem Sie mit der rechten Maustaste auf die Projektmappe > **NuGet-Pakete für Projektmappe verwalten** > **Wiederherstellen** klicken.
6. Klicken Sie mit der rechten Maustaste auf **MultiChannelToDo.Web** > **Application Insights-Telemetrie hinzufügen** > **Einstellungen konfigurieren** > Ressourcengruppe ändern zu ToDoApp*&lt;Ihr\_suffix>* > **Application Insights zu Projekt hinzufügen**.
7. Öffnen Sie im Azure-Portal das Blatt für die Application Insight-Ressource **MultiChannelToDo.Web**. Klicken Sie dann im Detail **Anwendungsintegrität** auf **Erfahren Sie, wie Sie Daten zu Seitenladezeiten im Browser erfassen** > Code kopieren.
7. Fügen Sie den kopierten JS-Instrumentationscode zu *&lt;Repositorystammverzeichnis>*\\src\\MultiChannelToDo.Web\\app\\Index.cshtml hinzu, direkt vor das schließende `<heading>`-Tag. Der Code sollte den eindeutigen Instrumentationsschlüssel Ihrer Application Insight-Ressource enthalten.

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>

11. Senden Sie benutzerdefinierte Ereignisse für Mausklicks an Application Insights, indem Sie am Ende des Textes folgenden Code einfügen:

        <script>
            $(document.body).find("*").click(function(event) {

                appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
            });
        </script>

    Dieser JavaScript-Codeausschnitt sendet jedes Mal, wenn ein Benutzer irgendwo in der Web-App klickt, ein benutzerdefiniertes Ereignis an Application Insights.

12. Führen Sie für Ihre Änderungen in Git-Shell ein Commit aus, und pushen Sie die Änderungen in Ihre Verzweigung in GitHub. Warten Sie, bis die Browser auf den Clients aktualisiert werden.

        git add -A :/
        git commit -m "add AI configuration for client app"
        git push origin master

6.	Fügen Sie die bereitgestellten Änderungen der App in die Produktionsumgebung ein:

        .\swap –Name ToDoApp<your_suffix>

13. Navigieren Sie zu der von Ihnen konfigurierten Application Insights-Ressource. Klicken Sie auf „Benutzerdefinierte Ereignisse“.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

    Wenn Sie keine Metriken für benutzerdefinierte Ereignisse sehen, warten Sie einige Minuten, und klicken Sie auf **Aktualisieren**.

Sie sehen möglicherweise ein Diagramm ähnlich dem folgenden:

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

So könnte das Ereignisraster darunter aussehen:

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

Gemäß dem Code Ihrer ToDoApp-Anwendung gehört das **BUTTON**-Ereignis zur Schaltfläche zum Senden, und das **INPUT**-Ereignis gehört zum Textfeld. Soweit sieht alles ganz sinnvoll aus. Allerdings gibt es offenbar sehr viele Klicks, aber nur wenige Klicks auf Aufgaben (die **LI**-Ereignisse).

Sie schließen daraus, dass einige Benutzer nicht genau wissen, auf welche Teile der Benutzeroberfläche sie klicken können, da der Mauszeiger wie ein Textauswahltool aussieht, wenn er auf Listenelemente und deren Symbole zeigt.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

Dieses Beispiel ist möglicherweise nicht sehr realitätsnah. Es ging nur darum, zu zeigen, dass Sie Ihre App verbessern und anschließend eine Test-Flighting-Bereitstellung durchführen, um von tatsächlichen Kunden live Feedback zur Nutzbarkeit zu erhalten.

### Instrumentieren Ihrer Server-App für Überwachung/Metriken
Dies weicht ein wenig vom Thema ab, da es in dem hier vorgestellten Szenario nur um die Client-App geht. Der Vollständigkeit halber richten Sie jetzt jedoch auch die serverseitige App ein.

6. Klicken Sie mit der rechten Maustaste auf **MultiChannelToDo** > **Application Insights-Telemetrie hinzufügen** > **Einstellungen konfigurieren** > Ressourcengruppe ändern zu ToDoApp*&lt;Ihr\_Suffix>* > **Application Insights zu Projekt hinzufügen**.
12. Führen Sie für Ihre Änderungen in Git-Shell ein Commit aus, und pushen Sie die Änderungen in Ihre Verzweigung in GitHub. Warten Sie, bis die Browser auf den Clients aktualisiert werden.

        git add -A :/
        git commit -m "add AI configuration for server app"
        git push origin master

6.	Fügen Sie die bereitgestellten Änderungen der App in die Produktionsumgebung ein:

        .\swap –Name ToDoApp<your_suffix>

Das ist alles!

## Untersuchen: Hinzufügen von slotspezifischen Tags zu Ihren Client-App-Metriken

In diesem Abschnitt konfigurieren Sie die verschiedenen Bereitstellungsslots, um slotspezifische Telemetriedaten an die gleiche Application Insights-Ressource zu senden. Auf diese Weise können Sie Telemetriedaten aus verschiedenen Slots (Bereitstellungsumgebungen) vergleichen, um die Auswirkungen der Änderungen an Ihrer App leicht zu erkennen. Gleichzeitig können Sie den Produktionsdatenverkehr vom übrigen Datenverkehr trennen, sodass Sie Ihre Produktions-App nach Bedarf überwachen können.

Da Sie Daten zum Clientverhalten erfassen, [fügen Sie Ihrem JavaScript-Code in index.cshtml einen Telemetrieinitialisierer hinzu](app-insights-api-custom-events-metrics.md#js-initializer). Wenn Sie beispielsweise die serverseitige Leistung testen möchten, können Sie im Servercode ähnlich vorgehen (siehe [Application Insights-API für benutzerdefinierte Ereignisse und Metriken](app-insights-api-custom-events-metrics.md)).

1. Fügen Sie zuerst den Code zwischen die beiden `//`-Kommentare in den JavaScript-Block ein, den Sie zuvor zum `<heading>`-Tag hinzugefügt haben.

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    Dieser Initialisierercode sorgt dafür, dass das `appInsights`-Objekt jedem gesendeten Telemetrieereignis eine benutzerdefinierte Eigenschaft namens `Environment` hinzufügt.

2. Fügen Sie diese benutzerdefinierte Eigenschaft als [Sloteinstellung](web-sites-staged-publishing.md#AboutConfiguration) für Ihre Web-App in Azure hinzu. Zu diesem Zweck führen Sie den folgenden Befehl in der Git-Shell-Sitzung aus:

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    Die Web.config-Datei in Ihrem Projekt definiert die App-Einstellung für `environment` bereits. Wenn Sie mit dieser Einstellung die App lokal testen, werden die Metriken mit `VS Debugger` gekennzeichnet. Wenn Sie jedoch Ihre Änderungen per Push an Azure übertragen, findet und verwendet Azure stattdessen die App-Einstellung für `environment` in der Konfiguration der Web-App, und die Metriken werden mit `Production` gekennzeichnet.

3. Führen Sie für Ihre Änderungen in Git-Shell ein Commit aus, pushen Sie die Änderungen in Ihre Verzweigung in GitHub, und warten Sie, bis Ihre Benutzer die neue App verwenden (der Browser muss aktualisiert werden). Es dauert etwa 15 Minuten, bis die neue Eigenschaft in Ihrer Application Insights `MultiChannelToDo.Web`-Ressource angezeigt wird.

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master

4. Wechseln Sie nun wieder zum Blatt **Benutzerdefinierte Ereignisse**, und filtern Sie die Metriken nach `Environment=Production`. Nun sollten Sie alle neuen benutzerdefinierten Ereignisse im Produktionsslot mit diesem Filter sehen.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)

5. Klicken Sie auf die Schaltfläche **Favoriten**, um die aktuellen Metrik-Explorer-Einstellungen zu speichern, beispielsweise unter **Benutzerdefinierte Ereignisse: Produktion**. Sie können später ganz einfach zwischen dieser Ansicht und der Ansicht eines Bereitstellungsslots wechseln.

    > [AZURE.TIP]Eine noch leistungsstärkere Analyse erhalten Sie durch [Integrieren der Application Insights-Ressource in Power BI](app-insights-export-power-bi.md).

### Hinzufügen von slotspezifischen Tags zu Ihren Server-App-Metriken
Der Vollständigkeit halber richten Sie jetzt auch die serverseitige App ein. Im Gegensatz zur Client-App, die in JavaScript instrumentiert wird, werden slotspezifische Tags für die Server-App mithilfe von .NET-Code instrumentiert.

1. Öffnen Sie *&lt;Repositorystammverzeichnis>*\\src\\MultiChannelToDo\\Global.asax.cs. Fügen Sie den unten stehenden Codeblock direkt vor der schließenden geschweiften Klammer des Namespace ein.

		namespace MultiChannelToDo
		{
				...

				// Begin new code
		    public class ConfigInitializer
		    : ITelemetryInitializer
		    {
		        void ITelemetryInitializer.Initialize(ITelemetry telemetry)
		        {
		            telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
		        }
		    }
				// End new code
		}

2. Korrigieren Sie Fehler bei der Namensauflösung, indem Sie am Anfang der Datei die unten stehenden `using`-Anweisungen einfügen:

		using Microsoft.ApplicationInsights.Channel;
		using Microsoft.ApplicationInsights.Extensibility;

3. Fügen Sie unten stehenden Code am Anfang der `Application_Start()`-Methode ein:

		TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());

3. Führen Sie für Ihre Änderungen in Git-Shell ein Commit aus, pushen Sie die Änderungen in Ihre Verzweigung in GitHub, und warten Sie, bis Ihre Benutzer die neue App verwenden (der Browser muss aktualisiert werden). Es dauert etwa 15 Minuten, bis die neue Eigenschaft in Ihrer Application Insights `MultiChannelToDo`-Ressource angezeigt wird.

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## Update: Einrichten der Betaverzweigung

2. Öffnen Sie *&lt;Repositorystammverzeichnis>*\\ARMTemplates\\ProdAndStagetest.json, und suchen Sie die `appsettings`-Ressourcen (suchen Sie nach `"name": "appsettings"`). Es gibt vier Ressourcen, eine für jeden Slot. 

2. Fügen Sie für jede `appsettings`-Ressource eine `"environment": "[parameters('slotName')]"`-App-Einstellung am Ende des `properties`-Arrays ein. Denken Sie daran, die vorherige Zeile mit einem Komma zu beenden.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)
    
    Sie haben gerade die `environment`-App-Einstellung zu allen Slots in der Vorlage hinzugefügt.
    
2. In der gleichen Datei befinden sich auch die `slotconfignames`-Ressourcen (suchen Sie nach `"name": "slotconfignames"`). Es gibt zwei Ressourcen, eine für jede App.

2. Fügen Sie für jede `slotconfignames`-Ressource `"environment"` am Ende des `appSettingNames`-Arrays ein. Denken Sie daran, die vorherige Zeile mit einem Komma zu beenden.

    Sie haben gerade die `environment`-App-Einstellung für den jeweiligen Bereitstellungsslot für beide Apps festgelegt.

3. Führen Sie in der Git-Shell-Sitzung folgende Befehle mit dem gleichen Ressourcengruppensuffix aus, das Sie zuvor verwendet haben.

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta

4. Geben Sie bei Aufforderung die gleichen Anmeldeinformationen für SQL-Datenbank ein wie zuvor. Wenn Sie gefragt werden, ob Sie die Ressourcengruppe aktualisieren möchten, geben Sie zuerst `Y` und dann `ENTER` ein.

    Wenn das Skript ausgeführt wurde, sind alle Ressourcen in der ursprünglichen Ressourcengruppe noch vorhanden, es wurde jedoch ein neuer Slot namens „beta“ in der Gruppe erstellt, der die gleiche Konfiguration aufweist, wie der zu Anfang erstellte „Staging“-Slot.

    >[AZURE.NOTE]Diese Methode, bei der verschiedene Bereitstellungsumgebungen erstellt werden, unterscheidet sich von der Methode in [Agile Softwareentwicklung mit Azure App Service](app-service-agile-software-development.md). Hier erstellen Sie Bereitstellungsumgebungen mit Bereitstellungsslots, bei der anderen Methode erstellen Sie Bereitstellungsumgebungen mit Ressourcengruppen. Durch das Verwalten von Bereitstellungsumgebungen mit Ressourcengruppen können Sie die Produktionsumgebung für Entwickler sperren, das Testen in der Produktionsumgebung ist jedoch nicht so einfach wie mit Slots.

Wenn Sie möchten, können Sie auch eine Alpha-App erstellen, indem Sie folgendes Skript ausführen:

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

Für den Zweck dieses Tutorials verwenden Sie weiterhin Ihre Beta-App.

## Update: Übertragen der Updates in die Beta-App

Nun zurück zu der App, die Sie verbessern möchten.

1. Stellen Sie sicher, dass Sie sich in der Betaverzweigung befinden.

        git checkout beta

2. Suchen Sie in *&lt;Repositorystammverzeichnis>*\\src\\MultiChannelToDo.Web\\app\\Index.cshtml das `<li>`-Tag, und fügen Sie das `style="cursor:pointer"`-Attribut hinzu, wie unten gezeigt.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)

3. Führen Sie für Ihre Änderungen ein Commit aus, und pushen Sie die Änderungen in Azure.

4. Überprüfen Sie, ob die Änderung im Betaslot reflektiert wird, indem Sie zu http://todoapp*&lt;your_suffix>*-beta.azurewebsites.net/ wechseln. Wenn die Änderung noch nicht angezeigt wird, aktualisieren Sie den Browser, um den neuen JavaScript-Code abzurufen.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

Nachdem die Änderung im Betaslot aktiviert wurde, können Sie eine Test-Flighting-Bereitstellung ausführen.

## Überprüfen: Weiterleiten des Datenverkehrs an die Beta-App

In diesem Abschnitt leiten Sie Datenverkehr an die Beta-App weiter. Um das Prinzip zu verdeutlichen, werden Sie einen großen Teil des Benutzerdatenverkehrs an die App weiterleiten. In der Praxis richtet sich die Menge an Datenverkehr, die Sie weiterleiten, nach der jeweiligen Situation. Wenn Ihre Website sich in der Größenordnung von microsoft.com bewegt, genügt sicherlich weniger als ein Prozent des Gesamtdatenverkehrs, um belastbare Nutzungsdaten zu erhalten.

1. Führen Sie in der Git-Shell-Sitzung folgende Befehle aus, um die Hälfte des Produktionsdatenverkehrs an den Betaslot weiterzuleiten:

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

  Die `ReroutePercentage=50`-Eigenschaft gibt an, dass 50 % des Produktionsdatenverkehrs an die URL der Beta-App (festgelegt durch die `ActionHostName`-Eigenschaft) weitergeleitet wird.

2. Navigieren Sie jetzt zu http://ToDoApp*&lt;your_suffix>*.azurewebsites.net. 50 % des Datenverkehrs sollten jetzt an den Betaslot weitergeleitet werden.

3. Filtern Sie die Metriken in Ihrer Application Insights-Ressource nach der Umgebung „beta“.

    > [AZURE.NOTE]Wenn Sie diese gefilterte Ansicht als Favorit speichern, können Sie im Metrik-Explorer schnell zwischen der Produktions- und der Betaansicht wechseln.

In Application Insights sehen Sie möglicherweise eine Anzeige wie die folgende:

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

Dies zeigt nicht nur, dass es wesentlich mehr Klicks auf die `<li>`-Tags gibt, sondern auch einen erheblichen Anstieg von Klicks auf `<li>`-Tags. Daraus können Sie schließen, dass die Benutzer entdeckt haben, dass die neuen `<li>`-Tags angeklickt werden können, und nun ihre zuvor abgeschlossenen Aufgaben aus der App löschen.

Basierend auf den Daten Ihrer Test-Flighting-Bereitstellung entscheiden Sie jetzt, dass Ihre neue Benutzeroberfläche produktionsbereit ist.

## Live schalten: Verschieben des neuen Codes in die Produktionsumgebung

Nun können Sie Ihr Update in die Produktionsumgebung verschieben. Der große Vorteil bei diesem Ansatz ist: Sie wissen, dass Ihr Update überprüft wurde, _bevor_ Sie es in die Produktionsumgebung überführen. Sie können das Update also beruhigt bereitstellen. Da Sie die AngularJS-Client-App aktualisiert haben, haben Sie nur den clientseitigen Code geändert. Wenn Sie auch die Web-API-App im Back-End ändern müssen, können Sie Ihre Änderungen genauso einfach überprüfen.

1. Entfernen Sie in Git-Shell die Regel für die Datenverkehrsweiterleitung, indem Sie folgenden Befehl ausführen:

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()

2. Führen Sie diese Git-Befehle aus:

        git checkout master
        git pull origin master
        git merge beta
        git push origin master

2. Warten Sie einige Minuten, bis der neue Code im Stagingslot bereitgestellt wurde, und starten Sie dann http://ToDoApp*&lt;your_suffix>*-staging.azurewebsites.net, um zu überprüfen, ob das neue Update bereits im Stagingslot vorhanden ist. Denken Sie daran, dass der Masterzweig Ihrer Verzweigung mit dem Stagingslot Ihrer App verknüpft ist.

3. Überführen Sie jetzt den Stagingslot in die Produktion:

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## Zusammenfassung ##

Mit Azure App Service können kleine und mittelgroße Unternehmen ihre kundenorientierten Apps ganz einfach in der Produktionsumgebung testen – eine Methode, die bisher nur in großen Unternehmen angewendet wurde. In diesem Tutorial haben Sie die notwendigen Informationen erhalten, um App Service und Application Insights zusammen für Test-Flighting-Bereitstellungen sowie in anderen DevOps-Szenarien zum Testen in der Produktionsumgebung einzusetzen.

## Weitere Ressourcen ##

-   [Agile Softwareentwicklung mit Azure App Service](app-service-agile-software-development.md)
-   [Einrichten von Stagingumgebungen für Web-Apps in Azure App Service](web-sites-staged-publishing.md)
-	[Vorhersagbares Bereitstellen einer komplexen Anwendung in Azure](app-service-deploy-complex-application-predictably.md)
-	[Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md)
-	[JSONLint – The JSON Validator](http://jsonlint.com/)
-	[Git Branching – Basic Branching and Merging](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-	[Azure PowerShell](powershell-install-configure.md)
-	[Wiki zum Kudu-Projekt](https://github.com/projectkudu/kudu/wiki)

<!---HONumber=AcomDC_1203_2015-->