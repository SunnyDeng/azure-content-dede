<properties 
	pageTitle="Versionshinweise zur Visual Studio-Erweiterung für Application Insights" 
	description="Die neuesten Updates für Visual Studio-Tools für Application Insights" 
	services="application-insights" 
    documentationCenter=""
	authors="dimazaid" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="dimazaid"/>
 
# Versionshinweise zu Application Insights-Tools für Visual Studio v 4.1

##Version 4.1
Diese Version ergänzt frühere Versionen um eine Reihe neuer Funktionen und Verbesserungen. Sie müssen Update 1 auf Ihrem Computer installiert haben, um diese Version erhalten zu können.

### Springen von einer Ausnahme zu einer Methode in Quellcode
Benutzer, die sich über die Application Insights-Suche Ausnahmen von ihren Produktions-Apps anzeigen lassen, können nun direkt zu der Methode in ihrem Code springen, an der die Ausnahme auftritt. Sie müssen nur das richtige Projekt laden und wir kümmern uns um den Rest. (Weitere Informationen zum Suchfenster finden Sie in den unten stehenden Hinweisen zur Version 4.0).

#### Wie funktioniert Application Insights?

Sie müssen keine Projektmappe geöffnet haben, um AI-Search verwenden zu können. In diesem Fall wird im Bereich Stapelüberwachung eine Benachrichtigung angezeigt und viele Elemente in diesem Bereich werden abgeblendet angezeigt.


Falls Dateiinformationen verfügbar wären, könnten einige Elemente möglicherweise Links darstellen, doch das Projektinformationselement ist immer noch sichtbar.

Durch Klicken auf den Hyperlink gelangen Sie zu dem Ort, an dem die ausgewählte Methode in Ihrem Code liegt. Möglicherweise gibt es einen Unterschied in der Versionsnummer, aber diese Funktion wird in späteren Versionen folgen: wechseln Sie zur richtigen Version des Codes.

![Klicken auf die Ausnahme](./media/app-insights-release-notes-vsix/jumptocode.png)

###Neue Einstiegspunkte für Suchvorgänge im Projektmappen-Explorer 

![Einstiegspunkt im Projektmappen-Explorer](./media/app-insights-release-notes-vsix/searchentry.png)


###Erscheinen eines Popupfensters nach Beenden der Veröffentlichung
Ein Popupfenster wird angezeigt, nachdem das Projekt online veröffentlicht wurde, damit Sie Ihre Application Insights-Daten in der Produktion anzeigen können.

![Popup](./media/app-insights-release-notes-vsix/publishtoast.png)

## Version 4.0

###Application Insights-Daten in Visual Studio suchen
Genau wie bei der Suchenfunktion im Application Insights-Portal können Sie filtern und nach Ereignistypen, Eigenschaftswerten und Text suchen sowie einzelne Ereignisse überprüfen.

![Fenster „Suchen“](./media/app-insights-release-notes-vsix/search.png)

###Anzeigen von Daten aus Ihrer Local Box im Fenster Diagnosetools

Zusätzlich werden im Visual Studio Diagnosehub Ihre Telemetriedaten zusammen mit anderen Debugdaten angezeigt. Dies wird nur in ASP.NET 4.5 unterstützt. Unterstützung für ASP. NET-5 wird in zukünftigen Versionen verfügbar sein.

![Diagnosehub-Fenster](./media/app-insights-release-notes-vsix/diagtools.png)

###Hinzufügen des SDK zu Ihrem Projekt, ohne bei Azure angemeldet sein zu müssen

Sie müssen sich nicht mehr bei Azure anmelden, um Application Insights-Pakete Ihrem Projekt hinzuzufügen, egal ob im Dialogfeld „Neues Projekt“ oder im Kontextmenü des Projekts. Wenn Sie sich anmelden, wird das SDK wie üblich installiert und für das Senden von Telemetriedaten konfiguriert. Wenn Sie sich nicht anmelden, wird das SDK Ihrem Projekt hinzugefügt. Es generiert die Telemetriedaten für den Diagnosehub . Sie können es später konfigurieren, wenn Sie möchten.

![Dialogfeld „Neues Projekt“](./media/app-insights-release-notes-vsix/newproject.png)

###Unterstützung von Geräten

Im Rahmen des *Connect();*-Events 2015 haben wir [angekündigt](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/), dass unsere Mobile DevOps-Umgebung für Geräte HockeyApp ist. HockeyApp unterstützt Sie beim Verteilen der Beta-Builds an Ihre Tester, beim Sammeln und Analysieren von allen Abstürzen Ihrer App und beim Sammeln von Feedback direkt von Ihren Kunden. HockeyApp unterstützt Sie auf jeder beliebigen Plattform, auf der Sie Ihre mobile Anwendung erstellen, z. B. iOS, Android oder Windows oder eine plattformübergreifende Lösung wie Xamarin, Cordova oder Unity.

In zukünftigen Versionen der Application Insights-Erweiterung werden wir neue Funktionen einführen, die eine stärkere Integration zwischen HockeyApp und Visual Studio ermöglichen. Beginnen Sie mit HockeyApp, indem Sie einfach den NuGet-Verweis hinzufügen: weitere Informationen finden Sie in der [Dokumentation](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone).

 

<!---HONumber=AcomDC_0121_2016-->