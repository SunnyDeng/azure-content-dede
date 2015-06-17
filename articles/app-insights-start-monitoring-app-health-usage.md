<properties 
	pageTitle="Hinzufügen des Application Insights SDK zu Ihrem Webprojekt" 
	description="Analysieren Sie die Auslastung, Verfügbarkeit und Leistung Ihrer lokalen oder Microsoft Azure-Webanwendung mit Application Insights." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="awills"/>

# Application Insights - Beginnen Sie damit, Integrität und Nutzung Ihrer Anwendung zu überwachen

*Application Insights befindet sich in der Vorschauphase.*

Mit Application Insights können Sie Ihre Live-Anwendung auf folgende Parameter hin überwachen:

* **Verfügbarkeit** - Wir testen Ihre URLs im Abstand von wenigen Minuten von weltweiten Standorten aus.
* **Leistung**   - Probleme und Ausnahmen hinsichtlich der Leistung werden erkannt und diagnostiziert.
* **Nutzung** - Stellen Sie fest, wie Benutzer mit Ihrer Anwendung umgehen, damit Sie die Anwendung verbessern können.

Alternative Möglichkeiten finden Sie unter [Erste Schritte mit Application Insights][start].

## <a name="add"></a>Hinzufügen von Application Insights zu Ihrem Projekt

Sie benötigen [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (oder neuer) und ein Konto in [Microsoft Azure](http://azure.com).

### Falls es sich um ein neues Projekt handelt ...

Wenn Sie in Visual Studio 2013 ein neues Projekt erstellen, achten Sie darauf, dass Application Insights ausgewählt ist. 


![Create an ASP.NET project](./media/appinsights/appinsights-01-vsnewp1.png)

Falls Sie erstmals ein Projekt erstellen, werden Sie nach einer Anmeldung bei Microsoft Azure Preview gefragt. (Dies ist nicht zu verwechseln mit Ihrem Visual Studio Online-Konto.)

Falls Sie der Azure-Ressource einen anderen Namen als ihrem Projekt zuweisen möchten oder die Ressource in derselben Gruppe wie ein anderes Projekt erscheinen soll, verwenden Sie **Einstellungen konfigurieren**. 

*Keine Option "Application Insights" vorhanden? Prüfen Sie, ob Sie Visual Studio 2013 Update 3 verwenden, ob Application Insights-Tools unter Erweiterungen und Updates aktiviert wurden und Sie ein Web-, Windows Store- oder Windows Phone-Projekt erstellen.*

### ... oder falls es sich um ein vorhandenes Projekt handelt

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie "Application Insights hinzufügen".

![Choose Add Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*Zur Einrichtung von Webnutzungsanalysen ist es noch ein weiterer Schritt, aber lassen Sie uns zunächst einige Daten betrachten ...*


### <a name="run"></a>2. Ausführen des Projekts

Starten Sie Ihre Anwendung mit F5, und probieren Sie es aus - öffnen Sie verschiedene Seiten.

In Visual Studio sehen Sie eine Anzahl der empfangenen Ereignisse.

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. Anzeigen der Überwachungsdaten

Öffnen Sie Application Insights von Ihrem Projekt aus.

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)


Suchen Sie Daten in den **Application health**-Kacheln. Zuerst sehen Sie lediglich einen oder zwei Punkte. Beispiel:

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

Klicken Sie auf eine Kachel, um weitere Details anzuzeigen. Sie können die in den Berichten angezeigten Daten ändern. [Erfahren Sie mehr über das Konfigurieren von Application Health-Berichten.][perf]


### <a name="deploy"></a>4. Bereitstellen der Anwendung

Stellen Sie die Anwendung bereit, und lassen Sie die Daten ansammeln.



Sobald Sie über eine Live-Anwendung verfügen, [richten Sie Webtests ein][availability], um sicherzustellen, dass sie live bleibt. 

![Example application monitor in Application Insights](./media/appinsights/appinsights-00-appblade.png)

### Möchten Sie den Namen Ihrer Anwendung im Portal ändern?

Sie können die Ressource ändern, an die Ihr Projekt Telemetriedaten sendet. 

(Die 'Ressource' ist das benannte Application Insights-Fenster, in dem Ihre Ergebnisse eingeblendet werden. Sie können mehr als eine Ressource in einer Gruppe anordnen - beispielsweise, wenn Sie über mehrere Projekte verfügen, die Teil einer Geschäftsanwendung sind.) 

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf "ApplicationInsights.config", und wählen Sie **Aktualisieren von Application Insights** aus. Dadurch wird ein Assistent geöffnet, in dem Sie eine andere vorhandene Ressource auswählen oder eine neue erstellen können.

Wechseln Sie danach zurück zum Portal, und löschen Sie die alte Ressource.

## <a name="video"></a>Video

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>Nächste Schritte

[Nachverfolgen der Nutzung Ihrer Webanwendung][usage]

[Die Leistung in Webanwendungen überwachen][perf]

[Erfassen und Durchsuchen von Diagnoseprotokollen][diagnostic]

[Problembehandlung][qna]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



<!--HONumber=46--> 
