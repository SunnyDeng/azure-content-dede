<properties 
	pageTitle="Verwalten von Preisen und Kontingenten für Application Insights" 
	description="Wählen Sie den benötigten Preisplan aus" 
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

# Verwalten von Preisen und Kontingenten für Application Insights

*Application Insights befindet sich in der Vorschau.*

[Preise][pricing] für [Visual Studio Application Insights][start] basieren auf dem Datenvolumen pro Anwendung. Es gibt einen grundlegenden Free-Tarif, in dem Sie mit wenigen Einschränkungen die meisten Features nutzen können.

Jede Application Insights-Ressource wird als separater Dienst abgerechnet und auf der Rechnung für Ihr Azure-Abonnement aufgeführt.

[Informationen hierzu finden Sie in der Preisübersicht.][pricing]

## Anzeigen der Kontingente und des Preisplans für Ihre Application Insights-Ressource

Sie können das Blatt zu Kontingenten und Preisen über die Ressourceneinstellungen Ihrer Anwendung öffnen.

![Wählen Sie "Einstellungen", "Kontingente + Preise".](./media/app-insights-pricing/01-pricing.png)

## Wie funktionieren Kontingente?

* In jedem Kalendermonat kann Ihre Anwendung maximal eine bestimmte Menge an Telemetriedaten an Application Insights senden. Die genauen Zahlen können Sie der [Preisübersicht][pricing] entnehmen. 
* Das Kontingent hängt von dem Tarif ab, den Sie ausgewählt haben.
* Das Kontingent beginnt um Mitternacht UTC am ersten Tag jedes Monats.
* Das Datenpunktediagramm zeigt, wie viel von Ihrem Kontingent in diesem Monat verwendet wurde.
* Das Kontingent wird in *Datenpunkten* gemessen. Ein einzelner Datenpunkt ist ein Aufruf einer der Nachverfolgungsmethoden, unabhängig davon, ob sie explizit in Ihrem Code oder durch eines der Standardtelemetriemodule aufgerufen wird. Jede in der Diagnosesuche angezeigte Zeile ist ein Datenpunkt. Jede Messung einer Metrik wie z. B. eines Leistungsindikators ist ein Datenpunkt. 
* *Sitzungsdaten* werden im Kontingent nicht berücksichtigt. Hierzu zählen die Anzahl von Benutzern oder Sitzungen sowie Umgebungs- und Gerätedaten.


## Overage

Wenn die Anwendung mehr als das monatliche Kontingent sendet, haben Sie folgende Möglichkeiten:

* Sie bezahlen für zusätzliche Daten. Informationen hierzu finden Sie in der [Preisübersicht][pricing]. Sie können diese Option im Voraus auswählen. Im Free-Tarif ist diese Option nicht verfügbar.
* Sie führen ein Upgrade für Ihren Tarif aus.
* Sie unternehmen nichts. Sitzungsdaten weiterhin aufgezeichnet, aber andere Daten werden in der Diagnosesuche oder im Metrik-Explorer nicht aufgeführt.

## Kostenlose Premium-Testversion

Wenn Sie erstmals eine neue Application Insights-Ressource erstellen, wird diese im Free-Tarif gestartet.

Sie können jederzeit zur kostenlosen 30-Tage-Testversion des Premium-Tarifs wechseln. Somit erhalten Sie die Vorteile des Premium-Tarifs. Nach 30 Tagen wird dieser automatisch auf den Tarif zurückgesetzt, den Sie zuvor verwendet haben, sofern Sie nicht explizit einen anderen Tarif auswählen. Wählen Sie den gewünschten Tarif zu einem beliebigen Zeitpunkt während des Testzeitraums aus. Sie erhalten weiterhin die kostenlose Testversion bis Ablauf des Zeitraums von 30 Tagen.


## Wie wurde mein Kontingent verwendet?

Das Diagramm im unteren Bereich des Blatts mit der Preisübersicht zeigt die Datenpunktnutzung Ihrer Anwendung gruppiert nach Datenpunkttypen an.

![Im unteren Bereich des Blatts mit der Preisübersicht](./media/app-insights-pricing/03-allocation.png)

Klicken Sie auf das Diagramm, um weitere Details einzublenden, oder ziehen Sie den Mauszeiger darüber, um einen Zeitraum im Detail anzuzeigen.

## Anzeigen der Rechnung für Ihr Azure-Abonnement

Die Gebühren für Application Insights werden Ihrer Azure-Rechnung hinzugefügt. Sie sehen die Details zu Ihrer Azure-Rechnung im Bereich "Abrechnung" des Azure-Portals oder im [Azure-Abrechnungsportal](https://account.windowsazure.com/Subscriptions).

![Wählen Sie im seitlichen Menü die Option "Abrechnung".](./media/app-insights-pricing/02-billing.png)




<!--Link references-->


[start]: app-insights-get-started.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=July15_HO3-->