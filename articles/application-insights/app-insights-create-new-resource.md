<properties 
	pageTitle="Erstellen einer neuen Application Insights-Ressource" 
	description="Richten Sie eine neue Anwendung, und erhalten Sie einen neuen instrumentationsschlüssel. Application Insights überwacht die Leistung und Verwendung von live-Anwendungen." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="awills"/>

# Erstellen einer neuen Application Insights-Ressource



[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights zeigt Daten über Ihre Anwendung in einer Microsoft Azure *Ressource*. Erstellen einer neuen Ressource ist daher Teil des [Application Insights einrichten, um eine neue Anwendung überwachen][start]. In vielen Fällen hierzu werden automatisch von der IDE, und das ist die empfohlene Methode, sofern diese verfügbar sind. Aber in einigen Fällen eine Ressource manuell erstellt.

Nach dem Erstellen der Ressource, die Sie den instrumentationsschlüssel abrufen und verwenden, konfigurieren Sie das SDK in der Anwendung. Die Telemetrie an die Ressource gesendet.

## Melden Sie sich beim Microsoft Azure

Wenn Sie besitzen eine [Microsoft-Konto, melden Sie sich jetzt](http://live.com). (Wenn Sie Dienste wie Outlook.com, OneDrive, Windows Phone oder XBox Live verwenden, verfügen Sie bereits über ein Microsoft-Konto.)

Sie benötigen außerdem ein Abonnement für [Microsoft Azure](http://azure.com). Wenn Ihr Team oder die Organisation ein Azure-Abonnement verfügt, kann der Besitzer Sie hinzufügen, mit Ihrer Windows Live ID

Oder Sie können ein neues Abonnement erstellen. Die kostenlose Testversion können Sie alles in Azure testen. Nachdem der Evaluierungszeitraum abgelaufen ist, finden Sie möglicherweise nutzungsbasierte Abonnements geeignet, wie Sie kostenlose Services in Rechnung gestellt wird nicht.

Wann haben Sie Zugriff auf ein Abonnement, die Anmeldung bei Application Insights unter [http://portal.azure.com](http://portal.azure.com), und verwenden Ihrer Live ID anmelden.


## Erstellen einer Application Insights-Ressource
  

In der [portal.azure.com](https://portal.azure.com), eine Ressource Application Insights hinzufügen:

![Klicken Sie auf "Neu > Application Insights"](./media/app-insights-create-new-resource/01-new.png)


* **Anwendungstyp** wirkt sich auf das Fenster "Übersicht" und in die verfügbaren Eigenschaften finden Sie [metrische Explorer][metrics]. Den Typ der app angezeigt wird, wählen Sie die Web-Typ für Webseiten und der Telefon-Typ für andere Geräte.
* **Ressourcengruppe** ist eine benutzerfreundliche Möglichkeit zum Verwalten von Eigenschaften wie Zugriffssteuerung. Wenn Sie bereits andere Azure-Ressourcen erstellt haben, können Sie auswählen, diese neue Ressource in derselben Gruppe platzieren.
* **Abonnement** ist Ihr Konto in Azure.
* **Speicherort** ist, in dem wir Ihre Daten beibehalten. Derzeit kann es nicht geändert werden.
* **Zum Startmenü hinzufügen** setzt eine Kachel Schnellzugriff für die Ressource auf Ihrer Azure-Homepage. Empfohlen.

Wenn Ihre Anwendung erstellt wurde, wird kein neuer Blade geöffnet. Dies ist, wo Sie Leistungs- und Nutzungsdaten über Ihre app sehen werden.

Abzurufende zurück zu diesem beim nächsten Mal in Azure, Anmeldung suchen für Ihre app Schnellstart-Kachel im Startmenü Board (Startseite). Oder klicken Sie auf Durchsuchen, um es auszuwählen.


## Kopieren Sie den Instrumentationsschlüssel.


Sie benötigen diese kurz darauf angewiesen die Daten aus dem SDK in Ihrer Anwendung auf die Ressource, die Sie gerade erstellt haben.

![Klicken Sie auf "Eigenschaften", wählen Sie den Schlüssel aus, und drücken Sie STRG+C](./media/app-insights-create-new-resource/02-props.png)

## Konfigurieren Sie Ihren SDK

Verwenden Sie den instrumentationsschlüssel konfigurieren [im SDK, die Sie in Ihrer Anwendung installieren][start].

Dieser Schritt hängt stark von der Art der Anwendung, mit der Sie arbeiten.

In einigen Fällen installieren Sie die standard-Module, die Telemetrie zu, ohne dass senden Sie keinen Code schreiben müssen. In allen Fällen können Sie [Verwenden Sie die API][api] eigene Telemetriedaten senden.

## <a name="monitor"></a>Siehe Telemetriedaten

Schließen Sie das Schnellstart-Blade zurückkehren zu Ihrer Anwendung Blade im Azure-Portal.

Klicken Sie auf die Kachel Suche finden Sie unter [diagnostische Suche][diagnostic], wobei die ersten Ereignisse angezeigt werden.

Klicken Sie nach einigen Sekunden auf "Aktualisieren", wenn Sie mehr Daten erwarten.



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-get-started.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->