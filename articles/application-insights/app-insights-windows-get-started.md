<properties 
	pageTitle="Application Insights für Windows Phone- und Windows Store-Apps" 
	description="Analysieren Sie die Nutzung und Leistung von Windows-Geräteanwendungen mit Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2015" 
	ms.author="awills"/>

# Application Insights für Windows Phone- und Windows Store-Apps

*Application Insights befindet sich in der Vorschau.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Mit Visual Studio Application Insights können Sie Ihre veröffentlichte Anwendung auf Folgendes überwachen:

* [**Nutzung**][windowsUsage] – Erfahren Sie, wie viele Benutzer Ihre App hat und was sie mit Ihrer App machen.
* [**Abstürze**][windowsCrash] – Rufen Sie Diagnoseberichte zu Abstürzen ab, und machen Sie sich mit den Auswirkungen auf die Benutzer vertraut.

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)

Bei vielen Anwendungstypen [kann Visual Studio fast unbemerkt Application Insights Ihrer App hinzufügen](#ide). Aber da Sie diesen Artikel lesen, um besser zu verstehen, was passiert, werden wir diese Schritte manuell durchlaufen.

Sie benötigen Folgendes:

* Ein Abonnement für [Microsoft Azure][azure]
* Visual Studio 2013 oder höher

## 1. Erstellen einer Application Insights-Ressource 

Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource.

![Wählen Sie "Neu", "Entwicklerdienste", Application Insights](./media/app-insights-windows-get-started/01-new.png)

Eine [Ressource][roles] in Azure ist eine Instanz eines Diensts. In dieser Ressource werden Telemetriedaten aus Ihrer App analysiert und Ihnen angezeigt.

#### Kopieren des Instrumentationsschlüssels

Mit diesem Schlüssel wird die Ressource identifiziert. Sie benötigen ihn in Kürze, um das SDK für das Senden der Daten an die Ressource zu konfigurieren.

![Öffnen Sie die Dropdownliste "Essentials", und wählen Sie den Instrumentationsschlüssel aus](./media/app-insights-windows-get-started/02-props.png)


## 2. Hinzufügen des Application Insights SDK zu Ihren Apps

Fügen Sie in Visual Studio das entsprechende SDK Ihrem Projekt hinzu.

Wenn es sich um eine universelle Windows App handelt, wiederholen Sie die Schritte für sowohl das Windows Phone-Projekt als auch das Windows-Projekt.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete** verwalten aus.

    ![](./media/app-insights-windows-get-started/03-nuget.png)

2. Wählen Sie **Online**, **Vorabversion einschließen** aus, und suchen Sie nach "Application Insights".

    ![](./media/app-insights-windows-get-started/04-ai-nuget.png)

3. Wählen Sie **Application Insights für Windows-Anwendungen**

4. Bearbeiten Sie die Datei "ApplicationInsights.config" (die bei der NuGet-Installation hinzugefügt wurde). Fügen Sie Folgendes direkt vor dem Endtag ein:

    `<InstrumentationKey>`*den kopierten Schlüssel*`</InstrumentationKey>`

**Universelle Windows Apps**: Wiederholen Sie die Schritte für sowohl das Windows Phone-Projekt als auch das Windows Store-Projekt.

## <a name="network"></a>3. Aktivieren des Netzwerkzugriffs für Ihre App

Wenn Ihre App noch nicht [den ausgehenden Netzwerkzugriff anfordert](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx), müssen Sie diesen ihrer Manifestdatei als [erforderliche Funktion](https://msdn.microsoft.com/library/windows/apps/br211477.aspx) hinzufügen.

## <a name="run"></a>4. Ausführen des Projekts

[Führen Sie die Anwendung durch Drücken von F5 aus](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx), und verwenden Sie sie, um einige Telemetriedaten zu generieren.

In Visual Studio sehen Sie eine Anzahl der empfangenen Ereignisse.

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

Im Debugmodus werden Telemetriedaten gesendet, sobald sie generiert wurden. Im Freigabemodus werden Telemetrie auf dem Gerät gespeichert und erst gesendet, wenn die Anwendung fortgesetzt wird.

## <a name="monitor"></a>5. Anzeigen von Überwachungsdaten

Öffnen Sie Application Insights von Ihrem Projekt aus.

![Klicken Sie mit der rechten Maustaste auf Ihr Projekt und öffnen Sie das Azure-Portal](./media/app-insights-windows-get-started/appinsights-04-openPortal.png)


Zuerst sehen Sie lediglich einen oder zwei Punkte. Zum Beispiel:

![Klicken Sie, um weitere Daten anzuzeigen](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

Klicken Sie nach einigen Sekunden auf "Aktualisieren", wenn Sie mehr Daten erwarten.

Klicken Sie auf ein beliebiges Diagramm, um weitere Details anzuzeigen.


## <a name="deploy"></a>5. Veröffentlichen der Anwendung im Windows Store

[Veröffentlichen Sie die Anwendung](http://dev.windows.com/publish), und verfolgen Sie das Ansammeln der Daten, wenn Benutzer sie herunterladen und nutzen.

## Wie geht es weiter?

* [Erkennen und Diagnostizieren von Abstürzen in Ihrer App][windowsCrash]
* [Weitere Informationen zu Metriken][metrics]
* [Weitere Informationen zur Diagnosesuche][diagnostic]


## <a name="ide"></a>Automatisierte Einrichtung

Wenn Sie die Einrichtungsschritte lieber in Visual Studio ausführen möchten, ist dies bei Windows Phone-, Windows Store- und vielen andere Arten von Apps möglich.

###<a name="new"></a> Wenn Sie ein neues Windows-Anwendungsprojekt erstellen...

Wählen Sie Application Insights im Dialogfeld "Neues Projekt" aus.

Wenn Sie aufgefordert werden, sich anzumelden, verwenden Sie die Anmeldeinformationen Ihres Azure-Kontos (das vom Visual Studio Online-Konto getrennt ist).

![](./media/app-insights-windows-get-started/appinsights-d21-new.png)


###<a name="existing"></a>Oder falls es sich um ein vorhandenes Projekt handelt ...

Fügen Sie Application Insights im Projektmappen-Explorer hinzu.


![](./media/app-insights-windows-get-started/appinsights-d22-add.png)


## <a name="usage"></a>Nächste Schritte


[Erkennen und Diagnostizieren von Abstürzen in Ihrer App][windowsCrash]

[Diagnoseprotokolle anlegen und durchsuchen][diagnostic]


[Nachverfolgen der Nutzung Ihrer App][windowsUsage]

[Verwenden der API zum Senden benutzerdefinierter Telemetriedaten][api]

[Problembehandlung][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md

 

<!---HONumber=62-->