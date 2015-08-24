\<properties pageTitle="Application Insights für C++-Apps" description="Analysieren Sie die Nutzung und Leistung Ihrer C++-App mit Application Insights." services="application-insights" documentationCenter="cpp" authors="crystk" manager="jakubo""/\>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="universal" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="06/03/2015" 
    ms.author="crystk"/>

# Application Insights für C++-Apps

Mit Visual Studio Application Insights können Sie Ihre mobile Anwendung auf Nutzung, Ereignisse und Abstürze überwachen.

## Anforderungen

Sie benötigen Folgendes:

* Ein Abonnement für [Microsoft Azure](http://azure.com). Sie melden sich mit einem Microsoft-Konto an, das Sie möglicherweise für Windows, XBox Live oder andere Microsoft-Clouddienste verwenden.
* Visual Studio 2015 oder höher.
* Universelle Windows 10-Anwendung

## Erstellen einer Application Insights-Ressource

Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie die Windows Store- oder die Windows Phone-Option.

![Wählen Sie "Neu", "Entwicklerdienste", "Application Insights".](./media/app-insights-windows-cpp/01-universal.png)

Auf dem nun geöffneten Blatt werden die Leistungs- und Nutzungsdaten über Ihre App angezeigt. Um bei der nächsten Anmeldung bei Azure dorthin zu gelangen, sollten Sie eine Kachel auf dem Startbildschirm anlegen. Klicken Sie alternativ auf "Durchsuchen", um das Blatt zu finden.

####  Erstellen Sie eine Kopie des Instrumentationsschlüssels.

Der Schlüssel identifiziert die Ressource, den Sie bald im SDK installieren können, um die Daten an die Ressource zu leiten.

![Klicken Sie auf "Eigenschaften", wählen Sie den Schlüssel aus, und drücken Sie STRG+C.](./media/app-insights-windows-cpp/02-props-asp.png)

## <a name="sdk"></a> Installieren des SDK in Ihrer Anwendung


1. Bearbeiten Sie die NuGet-Pakete Ihres Desktop-App-Projekts in Visual Studio.

    ![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie "NuGet-Pakete verwalten".](./media/app-insights-windows-cpp/03-nuget.png)

2. Installieren Sie das Application Insights SDK für C++-Apps.

    ![Aktivieren Sie **Vorabversion einschließen**, und suchen Sie nach "Application Insights".](./media/app-insights-windows-cpp/04-nuget.png)

3. Gehen Sie in den Projekteinstellungen für Veröffentlichung und Debugging folgendermaßen vor:
  - Fügen Sie "$\(SolutionDir\)packages\\ApplicationInsights-CPP.1.0.0-Beta\\src\\inc" zu den Projekteigenschaften hinzu -\> VC++-Verzeichnisse -\> Includeverzeichnisse
  - Fügen Sie "$\(SolutionDir\)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native\<PLATFORM TYPE\>\\release\\AppInsights\_Win10-UAP" zu den Projekteigenschaften hinzu -\> VC++-Verzeichnisse -\> Bibliotheksverzeichnisse

4. Fügen Sie "ApplicationInsights.winmd" aus "$\(SolutionDir\)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native\<PLATFORM TYPE\>\\release\\ApplicationInsights" als Verweis auf Ihr Projekt hinzu.
5. Fügen Sie "AppInsights\_Win10-UAP.dll" aus "$\(SolutionDir\)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native\<PLATFORM TYPE\>\\release\\AppInsights\_Win10-UAP" hinzu. Wechseln Sie zu den Eigenschaften und legen Sie die Inhalte auf JA fest. Damit wird die DLL in Ihr Buildverzeichnis kopiert.


#### So aktualisieren Sie das SDK auf zukünftige Versionen

Bei [Veröffentlichung eines neuen SDK](app-insights-release-notes-windows-cpp.md):

* Wählen Sie im NuGet-Paket-Manager das installierte SDK und dann die Aktion "Upgrade" aus.
* Wiederholen Sie die Installationsschritte mit der neuen Versionsnummer.

## Verwenden des SDK

Initialisieren Sie das SDK, und beginnen Sie mit der Nachverfolgung der Telemetrie.

1. In App.xaml.h: 
  - Fügen Sie Folgendes hinzu: `ApplicationInsights::CX::SessionTracking^ m_session;`
2. In App.xaml.cpp:
  - Fügen Sie Folgendes hinzu: `using namespace ApplicationInsights::CX;`

  - In App:App\(\)
	
     `// this will do automatic session tracking and automatic page view collection` `m_session = ref new ApplicationInsights::CX::SessionTracking();`

  - Wenn Sie den Stammframe erstellt haben \(üblicherweise am Ende von App::OnLaunched\), initialisieren Sie "m\_session":
	
    ```
    String^ iKey = L"<YOUR INSTRUMENTATION KEY>";
    m_session->Initialize(this, rootFrame, iKey);
	```

3. Um die Nachverfolgung an anderer Stelle in der Anwendung zu nutzen, können Sie eine Instanz des Telemetrieclients deklarieren.


```

    using namespace ApplicationInsights::CX;
    TelemetryClient^ tc = ref new TelemetryClient(L"<YOUR INSTRUMENTATION KEY>");
	tc->TrackTrace(L"This is my first trace");
    tc->TrackEvent(L"I'M ON PAGE 1");
    tc->TrackMetric(L"Test Metric", 5.03);
```


## <a name="run"></a> Ausführen des Projekts

Führen Sie die Anwendung aus, um Telemetriedaten zu generieren. Sie können sie entweder im Debugmodus auf dem Entwicklungscomputer ausführen, oder Sie können sie veröffentlichen und von Benutzern ausführen lassen.

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

[Verwenden der API zum Senden von benutzerdefinierten Ereignissen und Metriken][api]

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

 

<!---HONumber=August15_HO7-->