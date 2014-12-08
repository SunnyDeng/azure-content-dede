<properties title="Track usage in web applications with Application Insights" pageTitle="Nachverfolgung der Nutzung in Webanwendungen" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Nachverfolgung der Nutzung von Webanwendungen

Erfahren Sie, wie die Webanwendung genutzt wird. Richten Sie eine Nutzungsanalyse ein, um herauszufinden, welche Seiten sich Benutzer ansehen, wie viele zurückkehren und wie oft sie Ihre Website besuchen. Fügen Sie einige [benutzerdefinierte Ereignisse und Metriken][(Nachverfolgung)] hinzu, um detailliert die beliebtesten Features, die häufigsten Fehler zu analysieren und Ihre App für den Erfolg mit den Benutzern zu optimieren.

Telemetrie wird vom Client und vom Server erfasst. Clientdaten werden von allen modernen Webbrowsern erfasst, und Serverdaten können erfasst werden, wenn Ihre Plattform ASP.NET ist. (Muss nicht notwendigerweise in Azure ausgeführt werden.) 

* [Einrichten der Webnutzungsanalysen](#webclient)
* [Nutzungsanalyse](#usage)
* [Benutzerdefinierte Seitenanzahl für Einzelseiten-Apps](#spa)
* [Überprüfen die einzelnen Seitenereignisse](#inspect)
* [Detaillierte Nachverfolgung mit benutzerdefinierten Ereignissen und Metriken](#custom)
* [Video](#video)

## <a name="webclient"></a> Einrichten von Webclientanalysen

#### Abrufen einer Application Insights-Ressource in Azure

**Wenn Sie eine ASP.NET-Anwendung entwickeln**, [fügen Sie Application Insights Ihrem Webprojekt hinzu][Start], falls noch nicht geschehen. 

**Wenn Ihre Websiteplattform nicht ASP.NET ist:** Melden Sie sich bei [Microsoft Azure](http://azure.com) an, wechseln Sie ins [Vorschauportal](https://portal.azure.com), und fügen Sie eine Application Insights-Ressource hinzu.

![](./media/appinsights/appinsights-11newApp.png)

(Sie können darauf später mit der Schaltfläche "Durchsuchen" zugreifen.)



#### Hinzufügen unseres Skripts zu Ihren Webseiten

Rufen Sie im Schnellstart das Skript für Webseiten ab.

![](./media/appinsights/appinsights-06webcode.png)

Fügen Sie das Skript direkt vor dem </head>-Tag jeder Seite ein, die Sie nachverfolgen möchten. Wenn Ihre Website über eine Masterseite verfügt, können Sie das Skript dort ablegen. Beispielsweise würden Sie es in einem ASP.NET MVC-Projekt unter "View\Shared\_Layout.cshtml" ablegen.

## <a name="usage"></a>Nutzungsanalyse

Führen Sie Ihre Website aus, generieren Sie damit einige Telemetriedaten, und warten Sie ein bis zwei Minuten. Sie können sie durch Drücken von F5 auf dem Entwicklungscomputer ausführen oder auf dem Server bereitstellen.

Im Anwendungsübersichtsfenster werden Ihnen folgende Nutzungs-Tiles angezeigt:

![](./media/appinsights/appinsights-47usage.png)

*Noch keine Daten? Klicken Sie am oberen Seitenrand auf **Aktualisieren**.*

* **Sessions per browser**

    Eine *Sitzung* ist ein Zeitraum, der mit dem Öffnen einer Seite Ihrer Website durch den Benutzer beginnt. Dieser Zeitraum endet, nachdem der Benutzer für die Dauer von 30 Minuten keinerlei Webanforderungen mehr gesendet hat. 

    Klicken Sie, um das Diagramm zu vergrößern.

* **Top page views**

    Zeigt die Gesamtzählerstände der letzten 24 Stunden an.

    Klicken Sie auf die Seitenaufrufkachel, um einen detaillierteren Verlauf anzuzeigen.

![](./media/appinsights/appinsights-49usage.png)

Klicken Sie auf den Zeitraum, um den bis zu sieben Tage zurückliegenden Verlauf anzuzeigen.

Klicken Sie auf einen Graphen, um andere mögliche Metriken anzuzeigen.

![](./media/appinsights/appinsights-63usermetrics.png)

> [AZURE.NOTE] Deaktivieren Sie die Kontrollkästchen *aller* Metriken, um sie alle zu aktivieren. Metriken können nur in einigen Kombinationen angezeigt werden. Wenn Sie eine Metrik auswählen, werden die inkompatiblen deaktiviert.



## <a name="spa"></a> Benutzerdefinierte Seitenaufrufsanzahl für Einzelseitenanwendungen

Standardmäßig wird jedes Mal ein Seitenzähler angezeigt, wenn eine neue Seite in den Client-Browser geladen wird.  Sie möchten jedoch ggf. zusätzliche Seitenaufrufe zählen. Beispielsweise könnte eine Seite in Registerkarten gegliedert sein, und Sie möchten, dass jeder Wechsel von Registerkarten als Aufruf gezählt wird. Auf der Seite könnte auch neuer Inhalt durch JavaScript-Code geladen werden, ohne dass sich die Browser-URL ändert. 

Fügen Sie an geeigneter Stelle in Ihren Client-Code einen JavaScript-Aufruf wie diesen ein:

    appInsights.trackPageView(myPageName);

Der Seitenname kann die gleichen Zeichen wie eine URL enthalten, allerdings wird alles nach den Zeichen "#" oder "?" ignoriert.


## <a name="inspect"></a> Inspecting individual page view events

Normalerweise wird die Seitenaufruf-Telemetrie von Application Insights analysiert, und Sie erhalten nur kumulative Berichte, gemittelt über alle Benutzer. Sie können jedoch zu Debugging-Zwecken auch einzelne Seitenaufrufereignisse anzeigen.

Legen Sie im Fenster "Diagnosesuche" als Filter die Einstellung "Seitenansicht" fest.

![](./media/appinsights/appinsights-51searchpageviews.png)

Wählen Sie ein Ereignis, um weitere Details anzuzeigen.

> [AZURE.NOTE] Falls Sie [Suchen][Diagnose] verwenden, beachten Sie, dass nur ganze Wörter verglichen werden: "Abou" und "bout" stimmen nicht mit "About" überein, "Abou*" jedoch stimmt überein. Ein Suchbegriff darf nicht mit einem Platzhalterzeichen beginnen. Beispielsweise ergibt die Suche nach "*bou" nicht "About". 

> [Erfahren Sie mehr über Diagnosesuche][Diagnose]

## <a name="custom"></a> Detaillierte Nachverfolgung mit benutzerdefinierten Ereignissen und Metriken

Möchten Sie herausfinden, wofür die Benutzer Ihre App verwenden? Durch Einfügen von Aufrufen in den Client- und Servercode können Sie Ihre eigenen Telemetriedaten an Application Insights senden. Beispielsweise können Sie die Anzahl der Benutzer ermitteln, die Aufträge erstellen, ohne sie abzuschließen, oder welche Validierungsfehler am häufigsten auftreten oder was die durchschnittliche Punktzahl in einem Spiel ist.

[Erhalten Sie weitere Informationen zur API für benutzerdefinierte Ereignisse und Metriken][Nachverfolgung].

## <a name="video"></a> Video: Nachverfolgen der Nutzung

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a>Nächste Schritte

[Nutzungsverfolgung mit benutzerdefinierten Ereignissen und Metriken][Nachverfolgung]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



