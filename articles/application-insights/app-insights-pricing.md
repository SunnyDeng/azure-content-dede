<properties 
	pageTitle="Preise und Kontingent für Application Insights verwalten" 
	description="Wählen Sie den Preisplan, den Sie benötigen" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="awills"/>

# Preise und Kontingent für Application Insights verwalten

*Application Insights befindet sich in der Vorschau.*

[Preise][pricing] für [Visual Studio Application Insights][start] basiert auf Datenvolumen pro Anwendung. Es gibt eine erhebliche kostenfreie Stufe, in der Sie die meisten Features mit einigen Einschränkungen erhalten.

Jede Ressource Application Insights wird als separater Dienst in Rechnung gestellt, und trägt dazu bei, um die Rechnung für Ihre Azure-Abonnement.

[Finden Sie unter den Preisen Schema][pricing].

## Überprüfen Sie Kontingente und Preis Plan für die Application Insights-Ressource

Das Kontingent + Preise Blade aus Ihrer Anwendung Ressourceneinstellungen kann geöffnet werden.

![Wählen Sie Einstellungen, Kontingent + Preisen.](./media/app-insights-pricing/01-pricing.png)

## Wie funktioniert das Kontingent?

* In jedem Monat Kalender kann die Anwendung auf eine bestimmte Menge an Application Insights Telemetriedaten senden, einrichten. Finden Sie unter der [Preise Schema][pricing] für die tatsächlichen Werte. 
* Das Kontingent hängt von der Preisstufe, die Sie ausgewählt haben.
* Das Kontingent wird von Mitternacht UTC am ersten Tag jedes Monats gezählt.
* Das Daten-Punkt-Diagramm zeigt, wie viel von Ihr Kontingent oben in diesem Monat verwendet wurde.
* Das Kontingent wird gemessen *Datenpunkte.* Ein einzelnen Datenpunkt ist Aufrufen einer der Methoden verfolgen, ob explizit im Code oder durch eines der standardmäßigen Telemetrie-Module aufgerufen. Jede Zeile im diagnostische Suche angezeigten ist ein Datenpunkt. Jede Messung einer Metrik wie z. B. einen Leistungsindikator ist ein Datenpunkt. 
* *Sitzungsdaten* wird nicht in das Kontingent gezählt. Dies schließt die Anzahl von Benutzern, Sitzungen, Umgebung und Device-Daten.


## Overage

Wenn die Anwendung mehr als das monatliche Kontingent sendet, können Sie:

* Bezahlen Sie für zusätzliche Daten. Finden Sie unter der [Preise Schema][pricing] Details. Sie können diese Option im Voraus auswählen. Diese Option ist nicht in der Preisstufe frei verfügbar.
* Aktualisieren Sie Ihre Preisstufe.
* Nichts Unternehmen. Sitzungsdaten weiterhin aufgezeichnet werden, aber andere Daten erscheinen nicht in diagnostische suchen oder Metriken Explorer.

## Premium-Testversion

Wenn Sie zuerst eine neue Application Insights-Ressource erstellen, wird es in der kostenlosen Stufe gestartet.

Zu jedem Zeitpunkt können Sie an der 30-Tage-Testversion Premium wechseln. Dies bietet Ihnen die Vorteile der Premium-Ebene. Nach 30 Tagen wird es automatisch, was auch immer Sie Stufen zurückgesetzt wurden zuvor -, sofern Sie explizit eine andere Ebene. Wählen Sie die Ebene, die Sie zu einem beliebigen Zeitpunkt während des Testzeitraums möchten, aber Sie erhalten weiterhin bis zum Ende des Zeitraums 30-Tage-Testversion.


## Wie wurde mein Quote verwendet?

Das Diagramm am unteren Rand der Preise Blade zeigt Datennutzung Punkt der Anwendung nach Punkt Datentyp gruppiert.

![Am unteren Rand der Preise blade](./media/app-insights-pricing/03-allocation.png)

Klicken Sie auf das Diagramm für weitere Details, oder ziehen Sie für die Details eines Zeitraums.

## Überprüfen Sie die Rechnung für Ihre Azure-Abonnement

Ihre Azure-Rechnung werden Gebühren für Application Insights hinzugefügt. Sehen Sie Details zu Ihren Azure Abrechnung im Bereich auf der Azure-Portal in Rechnung stellen die [Abrechnung Azure-Verwaltungsportal](https://account.windowsazure.com/Subscriptions).

![Wählen Sie im Menü Seite Abrechnung aus.](./media/app-insights-pricing/02-billing.png)




<!--Link references-->


[start]: app-insights-get-started.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->