<properties 
	pageTitle="Erstellen einer neuen Application Insights-Ressource" 
	description="Richten Sie eine neue Anwendung ein, und erhalten Sie einen neuen Instrumentationsschlüssel. Application Insights überwacht die Leistung und Verwendung von Live-Anwendungen." 
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
	ms.date="06/19/2015" 
	ms.author="awills"/>

# Erstellen einer neuen Application Insights-Ressource



[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights zeigt Daten über Ihre Anwendung in einer Microsoft Azure-*Ressource* an. Die Erstellung einer neuen Ressource gehört daher zur [Einrichtung von Application Insights, um eine neue Anwendung zu überwachen][start]. In vielen Fällen kann dies automatisch über die IDE erfolgen. Diese Methode wird nach Möglichkeit empfohlen. In einigen Fällen wird eine Ressource jedoch manuell erstellt.

Nach dem Erstellen der Ressource erhalten Sie den Instrumentationsschlüssel und verwenden ihn zum Konfigurieren des SDK in der Anwendung. Dadurch wird die Telemetrie an die Ressource gesendet.

## Anmelden bei Microsoft Azure

Wenn Sie noch kein [Microsoft-Konto besitzen, erstellen Sie jetzt eins](http://live.com). (Wenn Sie Dienste wie Outlook.com, OneDrive, Windows Phone oder XBox Live verwenden, besitzen Sie bereits ein Microsoft-Konto.)

Außerdem benötigen Sie ein [Microsoft Azure](http://azure.com)-Abonnement. Wenn Ihr Team oder Ihre Organisation über ein Azure-Abonnement verfügt, kann der Besitzer Sie anhand Ihrer Windows Live-ID hinzufügen.

Sie können jedoch auch ein neues Abonnement erstellen. Mit der kostenlosen Testversion können Sie alle Funktionen von Azure testen. Nachdem der Testzeitraum abgelaufen ist, finden Sie möglicherweise das Abonnement mit nutzungsbasierter Bezahlung geeignet, da Ihnen kostenlose Dienste nicht in Rechnung gestellt werden.

Wenn Sie Zugang zu einem Abonnement besitzen, melden Sie sich über Ihre Live-ID unter [http://portal.azure.com](http://portal.azure.com) bei Application Insights an.


## Erstellen einer Application Insights-Ressource
  

Fügen Sie unter [portal.azure.com](https://portal.azure.com) eine neue Application Insights-Ressource hinzu:

![Klicken Sie auf "Neu > Application Insights".](./media/app-insights-create-new-resource/01-new.png)


* **Anwendungstyp** bestimmt den Inhalt des Blatts "Übersicht" und die im [Metrik-Explorer][metrics] verfügbaren Eigenschaften. Wenn Ihr App-Typ nicht angezeigt wird, wählen Sie einen der Webtypen für Webseiten und einen der Telefontypen für andere Geräte aus.
* **Ressourcengruppe** ist eine benutzerfreundliche Möglichkeit zum Verwalten von Eigenschaften wie der Zugriffssteuerung. Wenn Sie bereits andere Azure-Ressourcen erstellt haben, können Sie diese neue Ressource in derselben Gruppe platzieren.
* **Abonnement** ist Ihr Zahlungskonto in Azure.
* **Speicherort** ist der Ort, an dem Ihre Daten aufbewahrt werden. Dieser kann derzeit nicht geändert werden.
* **Zum Startmenü hinzufügen** legt für die Ressource eine Kachel für den Schnellzugriff auf Ihrer Azure-Startseite ab. Empfohlen.

Wenn Ihre App erstellt wurde, wird ein neues Blatt geöffnet. Dort werden die Leistungs- und Nutzungsdaten über Ihre App angezeigt.

Um bei der nächsten Anmeldung bei Azure dorthin zurückzugelangen, suchen Sie im Startmenü (Startseite) nach der Schnellstart-Kachel für Ihre App. Klicken Sie alternativ dazu auf "Durchsuchen", um sie zu finden.


## Kopieren Sie den Instrumentationsschlüssel.


Sie benötigen diesen in Kürze, um die Daten aus dem SDK in Ihrer App an die soeben erstellte Ressource zu leiten.

![Klicken Sie auf "Eigenschaften", wählen Sie den Schlüssel aus, und drücken Sie STRG+C.](./media/app-insights-create-new-resource/02-props.png)

## Konfigurieren Ihres SDK

Konfigurieren Sie das [SDK, das Sie in Ihrer Anwendung installieren][start] mithilfe des Instrumentationsschlüssels.

Dieser Schritt hängt stark von der Art der Anwendung ab, mit der Sie arbeiten.

In einigen Fällen installieren Sie Standardmodule, die Telemetrie senden, ohne dass Sie Code schreiben müssen. In jedem Fall können Sie [die API verwenden][api], um eigene Telemetriedaten zu senden.

## <a name="monitor"></a>Anzeigen von Telemetriedaten

Schließen Sie das Schnellstartblatt, um zum Blatt Ihrer Anwendung im Azure-Portal zurückzukehren.

Klicken Sie auf die Kachel "Search", um die [Diagnosesuche][diagnostic] zu öffnen. Dort werden die ersten Ereignisse angezeigt.

Klicken Sie nach einigen Sekunden auf "Aktualisieren", wenn Sie mehr Daten erwarten.

## Automatisches Erstellen einer Ressource

Sie können ein [PowerShell-Skript](app-insights-powershell-script-create-resource.md) schreiben, um eine Ressource automatisch zu erstellen.


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->