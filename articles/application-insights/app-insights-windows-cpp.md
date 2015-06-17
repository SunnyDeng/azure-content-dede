< Eigenschaften Seitentitel = "Application Insights für C++-Anwendungen" Description = "Analysieren der Nutzung und Leistung von C++-Anwendungen mit Application Insights" Dienste "Application Insights" DocumentationCenter = = "Cpp" Autoren = "Crystk" Manager "Jakubo" = "/ >

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="universal" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="06/03/2015" 
    ms.author="crystk"/>

# Application Insights für C++-Anwendungen

Mit Visual Studio Application Insights können Sie Ihre mobile Anwendung auf Nutzung, Ereignisse und Abstürze überwachen.

## Anforderungen

Sie benötigen Folgendes:

* Ein Abonnement für [Microsoft Azure](http://azure.com). Sie melden sich mit einem Microsoft-Konto an, das Sie möglicherweise für Windows, XBox Live oder andere Microsoft-Clouddienste verwenden.
* Visual Studio 2015 oder höher.

## Erstellen einer Application Insights-Ressource

Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie die Option Windows Phone oder Windows Store.

![Wählen Sie "Neu", "Entwicklerdienste", "Application Insights".](./media/app-insights-windows-cpp/01-universal.png)

Auf dem nun geöffneten Blatt werden die Leistungs- und Nutzungsdaten über Ihre App angezeigt. Um bei der nächsten Anmeldung bei Azure dorthin zu gelangen, sollten Sie eine Kachel auf dem Startbildschirm anlegen. Klicken Sie alternativ auf "Durchsuchen", um das Blatt zu finden.

####  Erstellen Sie eine Kopie des Instrumentationsschlüssels.

Der Schlüssel identifiziert die Ressource, den Sie bald im SDK installieren können, um die Daten an die Ressource zu leiten.

![Klicken Sie auf "Eigenschaften", wählen Sie den Schlüssel aus, und drücken Sie STRG+C](./media/app-insights-windows-cpp/02-props-asp.png)

## <a name="sdk"></a> Installieren Sie das SDK in Ihrer Anwendung


1. Bearbeiten Sie die NuGet-Pakete Ihres Desktop-App-Projekts in Visual Studio.

    ![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie "NuGet-Pakete verwalten".](./media/app-insights-windows-cpp/03-nuget.png)

2. Installieren Sie Application Insights SDK für C++-Anwendungen.

    ![Wählen Sie **Online**, **Vorabversion einschließen** aus, und suchen Sie nach "Application Insights"](./media/app-insights-windows-cpp/04-ai-nuget.png)

3. In Ihren Projekteinstellungen für die Release- und Debugversionen:
  - Hinzufügen von $(VC++-Verzeichnisse in den Projekteigenschaften SolutionDir)packages\\ApplicationInsights-CPP.1.0.0-Beta\\src\\inc -> -> Includeverzeichnisse
  - Hinzufügen von $(SolutionDir)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native < PLATTFORMTYP > \\release\\AppInsights_Win10-UAP in den Projekteigenschaften -> VC++-Verzeichnisse Bibliotheksverzeichnisse ->

4. Hinzufügen von ApplicationInsights.winmd als Verweis zum Projekt $(SolutionDir)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native < PLATTFORMTYP > \\release\\ApplicationInsights
5. Fügen Sie der AppInsights_Win10-UAP.dll von $(SolutionDir)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native < PLATTFORMTYP > \\release\\AppInsights_Win10-UAP. Wechseln Sie zu Eigenschaften und Inhalte auf "Ja" festgelegt. Dadurch wird die Dll in das Build-Verzeichnis kopiert.

## Verwenden des SDK

Initialisieren Sie das SDK, und beginnen Sie mit der Nachverfolgung der Telemetrie.

1. In App.xaml.h: 
  - Fügen Sie hinzu: `ApplicationInsights::CX::SessionTracking^ m_session;`
2. In App.xaml.cpp:
  - Fügen Sie hinzu: `using namespace ApplicationInsights::CX;`

  - In App:App()
	
     `// this will do automatic session tracking and automatic page view collection` `m_session = ref new ApplicationInsights::CX::SessionTracking();`

  - Nach dem Erstellen des Stamms-Frame (in der Regel am Ende des App::OnLaunched) initialisieren M_session:
	
    ```
    String^ iKey = L"<YOUR INSTRUMENTATION KEY>";
    m_session->Initialize(this, rootFrame, iKey);
	```

3. Um Nachverfolgung an anderer Stelle in der Anwendung zu verwenden, können Sie eine Instanz des Telemetrie Client deklarieren.


```

    using namespace ApplicationInsights::CX;
    TelemetryClient^ tc = ref new TelemetryClient(L"<YOUR INSTRUMENTATION KEY>");
	tc->TrackTrace(L"This is my first trace");
    tc->TrackEvent(L"I'M ON PAGE 1");
    tc->TrackMetric(L"Test Metric", 5.03);
```


## <a name="run"></a> Ausführen des Projekts

Führen Sie die Anwendung um Telemetriedaten zu generieren. Sie können es auf dem Entwicklungscomputer mehr im Debugmodus ausführen oder veröffentlichen und führen Sie es Benutzern ermöglichen.

## Anzeigen Ihrer Daten in Application Insights

Kehren Sie zu http://portal.azure.com zurück, und navigieren Sie zur Application Insights-Ressource.

Klicken Sie auf "Suchen", um [Diagnosesuche][diagnostic] zu öffnen. Dort werden die ersten Ereignisse angezeigt. Wenn nichts angezeigt wird, warten Sie eine oder zwei Minuten, und klicken Sie auf "Aktualisieren".

![Klicken Sie auf "Diagnosesuche".](./media/app-insights-windows-cpp/21-search.png)

Während Ihre App verwendet wird, werden die Daten auf dem Blatt "Übersicht" angezeigt.

![Blatt "Übersicht"](./media/app-insights-windows-cpp/22-oview.png)

Klicken Sie auf ein beliebiges Diagramm, um weitere Details zu erhalten. Zum Beispiel zu Abstürzen:

![Klicken Sie auf das Absturzdiagramm.](./media/app-insights-windows-cpp/23-crashes.png)


## <a name="usage"></a>Nächste Schritte

[Nachverfolgen der Nutzung Ihrer App][track]

[Verwenden Sie die API zum Senden von benutzerdefinierten Ereignissen und Metriken][api]

[Diagnosesuche][diagnostic]

[Metrik-Explorer][metrics]

[Problembehandlung][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->