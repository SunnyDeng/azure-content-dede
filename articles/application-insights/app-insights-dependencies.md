<properties 
	pageTitle="Diagnostizieren von Problemen mit Abhängigkeiten in Application Insights" 
	description="Finden von Fehlern und Leistungseinbußen, die von Abhängigkeiten verursacht werden" 
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
	ms.date="04/16/2015" 
	ms.author="awills"/>
 
# Diagnostizieren von Problemen mit Abhängigkeiten in Application Insights


Eine *Abhängigkeit* ist eine externe Komponente, die von Ihrer App aufgerufen wird. In der Regel handelt es sich um einen Dienst, der über HTTP oder eine Datenbank oder ein Dateisystem aufgerufen wird. In Visual Studio Application Insights können Sie leicht erkennen, wie lange die Anwendung auf Abhängigkeiten wartet und wie oft ein Abhängigkeitsaufruf nicht funktioniert.

## Einsatzgebiete

Aktuell steht die integrierte Abhängigkeitsüberwachung für folgende Apps und Dienste zur Verfügung:

* ASP.NET-Web-Apps und Dienste, die auf einem IIS-Server oder auf Azure ausgeführt werden

Für andere Typen wie Java-Web-Apps oder Geräte-Apps können Sie mithilfe der TrackDependency-API Ihre eigene Überwachung schreiben.

Der standardmäßig verfügbare Abhängigkeitsmonitor meldet derzeit Aufrufe an diese Abhängigkeitstypen:

* SQL-Datenbanken
* ASP.NET-Web- und WCF-Dienste
* Lokale oder Remote-HTTP-Aufrufe
* Azure DocumentDb, Tabelle, Blobspeicher und Warteschlange

In diesem Fall könnten Sie Ihre eigenen SDK-Aufrufe zum Überwachen anderer Abhängigkeiten schreiben.

## Einrichten der Abhängigkeitsüberwachung

Zum Überwachen der Abhängigkeit müssen Sie folgende Aktionen durchführen:

* Verwenden Sie den [Statusmonitor](app-insights-monitor-performance-live-website-now.md) auf dem IIS-Server, um die Überwachung zu aktivieren.
* Fügen Sie Ihrer Azure-Web-App oder dem virtuellen Computer die [Application Insights-Erweiterung](../insights-perf-analytics.md) hinzu.

(Für einen virtuellen Azure-Computer können Sie, wie auf jedem anderen Computer, entweder die Erweiterung in der Azure-Systemsteuerung oder den Statusmonitor installieren.)

Sie können die oben genannten Schritte für eine bereits bereitgestellte Web-App durchführen. Um eine standardmäßige Abhängigkeitsüberwachung zu erhalten, müssen Sie Application Insights nicht Ihrem Quellprojekt hinzufügen.

## Diagnostizieren von Leistungsproblemen der Abhängigkeit

So bewerten Sie die Leistung der Anforderungen an den Server

![Klicken Sie in Ihrer Anwendung in Application Insights auf der Seite "Übersicht" auf die Kachel "Leistung"](./media/app-insights-dependencies/01-performance.png)

Scrollen Sie nach unten, um das Raster der Anforderungen anzuzeigen:

![Liste der Anforderungen mit Mittelwerten und Anzahlen](./media/app-insights-dependencies/02-reqs.png)

Die zuerst aufgeführte Instanz dauert sehr lange. Mal sehen, ob wir herausfinden, wo die Zeit beansprucht wird.

Klicken Sie auf diese Zeile, um einzelne Anforderungsereignisse anzuzeigen:


![Liste der Anforderungsinstanzen](./media/app-insights-dependencies/03-instances.png)

Klicken Sie auf eine beliebige Instanz mit langer Ausführungsdauer, um diese näher zu überprüfen.

> [AZURE.NOTE]Scrollen Sie nach unten, um eine Instanz auszuwählen. Durch Wartezeiten in der Pipeline sind die Daten für die zuerst aufgeführten Instanzen möglicherweise unvollständig.

Scrollen Sie nach unten zu den Remoteabhängigkeitsaufrufen im Zusammenhang mit dieser Anforderung:

![Finden von Aufrufen von Remoteabhängigkeiten, Identifizieren ungewöhnlich langer Laufzeiten](./media/app-insights-dependencies/04-dependencies.png)

Der Großteil der Zeit zur Verarbeitung dieser Anforderung wurde für einen Aufruf eines lokalen Diensts aufgewendet.

Wählen Sie diese Zeile aus, um weitere Informationen zu erhalten:


![Klicken Sie sich durch diese Remoteabhängigkeit, um die Ursache herauszufinden](./media/app-insights-dependencies/05-detail.png)

Dieses Detail enthält genügend Informationen, um das Problem zu diagnostizieren.



## Fehler

Wenn Anforderungsfehler vorhanden sind, klicken Sie auf das Diagramm.

![Klicken Sie auf das Diagramm mit Anforderungsfehlern](./media/app-insights-dependencies/06-fail.png)

Klicken Sie sich durch einen Anforderungstyp und eine Anforderungsinstanz, um einen fehlerhaften Aufruf einer Remoteabhängigkeit zu finden.


![Klicken Sie auf den Anforderungstyp, klicken Sie auf die Instanz, um eine andere Ansicht derselben Instanz abzurufen, und klicken Sie darauf, um Details zur Ausnahme zu erhalten.](./media/app-insights-dependencies/07-faildetail.png)


<!--Link references-->

<!---HONumber=62-->