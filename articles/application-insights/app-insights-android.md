<properties
    pageTitle="Application Insights für Android-Apps | Microsoft Azure"
    description="Analysieren Sie die Nutzung und Leistung Ihrer Android-App mit Application Insights."
    services="application-insights"
    documentationCenter="android"
    authors="alancameronwills"
    manager="ronmart"/>

<tags
    ms.service="application-insights"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="04/28/2015"
    ms.author="awills"/>

# Application Insights für Android-Apps

Mit Visual Studio Application Insights können Sie Ihre mobile Anwendung auf Nutzung, Ereignisse und Abstürze überwachen.

## Anforderungen

Sie benötigen Folgendes:

* Ein Abonnement für [Microsoft Azure](http://azure.com). Sie melden sich mit einem Microsoft-Konto an, das Sie möglicherweise für Windows, XBox Live oder andere Microsoft-Clouddienste verwenden.
* Android Studio
* Android SDK, Version 9 oder höher.

## Erstellen einer Application Insights-Ressource

Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie die Android-Option.

![Wählen Sie "Neu", "Entwicklerdienste", "Application Insights".](./media/app-insights-android/11-new.png)

Auf dem nun geöffneten Blatt werden die Leistungs- und Nutzungsdaten über Ihre App angezeigt. Um bei der nächsten Anmeldung bei Azure dorthin zu gelangen, sollten Sie eine Kachel auf dem Startbildschirm anlegen. Klicken Sie alternativ auf "Durchsuchen", um das Blatt zu finden.

## Installieren des Plug-Ins für Application Insights in Android Studio

(Falls Sie dies noch nicht durchgeführt haben.)

1.  Starten Sie Android Studio, und konfigurieren Sie die Plug-Ins.

    ![Wählen Sie "Konfigurieren".](./media/app-insights-android/01-configure.png)

2.  Wählen Sie das Application Insights Android Studio-Plug-In aus, und installieren Sie es.

    ![Wählen Sie das Plug-In aus.](./media/app-insights-android/03-select-plugin.png)

## <a name="sdk"></a>Installieren des SDK in Ihrer Anwendung


1.  Wählen Sie **Extras** > **Integrate Application Insights SDK** aus.

    ![Integrieren von Application Insights](./media/app-insights-android/04-tools-integrate.png)

3.  Erstellen Sie eine Komponente in Ihrem Abonnement.

    ![Erstellen einer Komponente](./media/app-insights-android/07-create-component.png)

    Verwenden Sie den Instrumentationsschlüssel, den Sie von Ihrer Application Insights-Ressource erhalten haben.

4.  Synchronisieren Sie Gradle, um das SDK herunterladen und in das Projekt zu integrieren.

    ![Synchronisieren von Gradle-Dateien zum Herunterladen des SDK](./media/app-insights-android/08-successful-integration.png)

    (Weitere Informationen finden Sie auf der [Nutzungsseite](http://go.microsoft.com/fwlink/?LinkID=533220).)

An dieser Stelle wurde der Datei "build.gradle" des Moduls der folgende Verweis hinzugefügt. Berechtigungen für `INTERNET` und `ACCESS_NETWORK_STATE` sowie ein Metadaten-Tag mit dem Instrumentationsschlüssel der Komponente wurden der Datei `AndroidManifest.xml` des Moduls hinzugefügt.

```java

    dependencies {
    compile 'com.microsoft.azure:applicationinsights-android:+'
    }
```

```xml

    <manifest>
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application>
        <meta-data
            android:name="com.microsoft.applicationinsights.instrumentationKey"
            android:value="${AI_INSTRUMENTATION_KEY}" />
    </application>
    </manifest>
```

#### Optional: Festlegen des Instrumentationsschlüssels im Code

Es ist auch möglich, den Instrumentationsschlüssel im Code festzulegen. Dadurch wird der in `AndroidManifest.xml` festgelegte Schlüssel überschrieben.

```java

    ApplicationInsights.setup(this, "<YOUR-IKEY-GOES-HERE>");
    ApplicationInsights.start();
```


## Verwenden des SDK

Initialisieren Sie das SDK, und beginnen Sie mit der Nachverfolgung der Telemetrie.

Fügen Sie der Stammaktivität Ihrer App den folgenden Import hinzu:

```java

     import com.microsoft.applicationinsights.library.ApplicationInsights;
```

Fügen Sie dem `onCreate`-Rückruf der Aktivität Folgendes hinzu:

```java

    ApplicationInsights.setup(this.getApplicationContext(), this.getApplication());
    ApplicationInsights.start();
```

Wenn `ApplicationInsights.start()` aufgerufen wird, beginnt das SDK mit dem Nachverfolgen von Android-Lebenszyklusaktivität und unbehandelten Ausnahmen.

> [AZURE.NOTE]Lebenszyklusereignisse der Anwendung werden nur im Android SDK Version 15 und höher (Ice Cream Sandwich+), erfasst.

Darüber hinaus können benutzerdefinierte Ereignisse, Ablaufverfolgungen, Metriken und behandelte Ausnahmen erfasst werden. Verwenden Sie eine der [Application Insights-APIs][api], um Telemetriedaten zu senden.

* "TrackEvent(eventName)" für andere Benutzeraktionen
* "TrackTrace(logEvent)" für die [Diagnoseprotokollierung][diagnostic]
* "TrackHandledException(exception)" in Catch-Klauseln
* "TrackMetric(name, value)" in einer Hintergrundaufgabe, um reguläre Berichte von Metriken zu senden, die nicht an bestimmte Ereignisse angefügt sind

Der folgende Code ist ein Beispiel für die Initialisierung und manuelle Telemetrieerfassung:

```java

    public class MyActivity extends Activity {

      @Override
      protected void onCreate(Bundle savedInstanceState) {

        ApplicationInsights.setup(this);
        //... other initialization code ...//
        ApplicationInsights.start();

        // track telemetry data
        TelemetryClient client = TelemetryClient.getInstance();
        HashMap<String, String> properties = new HashMap<String, String>();
        properties.put("property1", "my custom property");
        client.trackEvent("sample event", properties);
        client.trackTrace("sample trace");
        client.trackMetric("sample metric", 3);
        client.trackHandledException(new Exception("sample exception"));
      }
    }
```

## <a name="run"></a> Ausführen des Projekts

Führen Sie Ihre Anwendung aus (UMSCHALT+F10 in Windows, STRG+R in OS X), um Telemetriedaten zu generieren.

## Anzeigen Ihrer Daten in Application Insights

Kehren Sie zu http://portal.azure.com zurück, und navigieren Sie zur Application Insights-Ressource.

Klicken Sie auf **Suchen**, um [Diagnosesuche][diagnostic] zu öffnen. Dort werden die ersten Ereignisse angezeigt. Wenn keine Ereignisse angezeigt werden, warten Sie eine oder zwei Minuten, und klicken Sie dann auf **Aktualisieren**.

![Klicken Sie auf "Diagnosesuche".](./media/app-insights-android/21-search.png)

Während Ihre App verwendet wird, werden die Daten auf dem Blatt "Übersicht" angezeigt.

![Blatt "Übersicht"](./media/app-insights-android/22-oview.png)

Klicken Sie auf ein beliebiges Diagramm, um weitere Details zu erhalten. Zum Beispiel zu Abstürzen:

![Klicken Sie auf das Absturzdiagramm.](./media/app-insights-android/23-crashes.png)


## <a name="usage"></a>Nächste Schritte

[Nachverfolgen der Nutzung Ihrer App][track]

[Diagnosesuche][diagnostic]

[Metrik-Explorer][metrics]

[Problembehandlung][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Oct15_HO3-->