<properties 
	pageTitle="Diagnostizieren von Problemen mit Abhängigkeiten in Application Insights" 
	description="Suchen Sie, Fehler und die Leistung beeinträchtigt, die aufgrund von Abhängigkeiten" 
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


Ein *Abhängigkeit* ist eine externe Komponente, die von der Anwendung aufgerufen wird. Es ist in der Regel einen Dienst mit HTTP oder einer Datenbank oder einem Dateisystem aufgerufen. In Visual Studio Application Insights können Sie leicht erkennen, wie lange die Anwendung Abhängigkeiten wartet und wie oft ein Abhängigkeit Aufruf fehlschlägt.

## Wo Sie es verwenden können

Standardmäßig ist die Abhängigkeitsüberwachung für derzeit verfügbar:

* ASP.NET Web-apps und Dienste, die auf einem IIS-Server oder auf Azure ausgeführt.

Für andere Dateitypen, wie Java-Webanwendungen oder Geräte-apps können Sie Ihre eigenen Monitor mithilfe der TrackDependency-API schreiben.

Der abhängigkeitsmonitor für die Out-of-the-Box-Berichte derzeit Aufrufe an diese Typen von Abhängigkeiten:

* SQL-Datenbanken
* ASP.NET Web und Wcf-Dienste
* Lokale oder remote-HTTP-Aufrufe
* Azure DocumentDb, Tabelle, Blob-Speicher und Warteschlange

In diesem Fall könnten Sie Ihre eigenen SDK-Aufrufe von anderen Abhängigkeiten zu überwachen schreiben.

## Einrichten einer Abhängigkeitsüberwachung

Zum Überwachen der Abhängigkeit zu erhalten, müssen Sie folgende Aktionen durchführen:

* Verwenden Sie [Statusmonitor](app-insights-monitor-performance-live-website-now.md) auf dem IIS-Server verwenden, um die Überwachung zu aktivieren.
* Hinzufügen der [Insights Anwendungserweiterung](../insights-perf-analytics.md) an Ihren Azure Web App oder der VM.

(Für eine Azure-VM entweder können Sie installieren die Erweiterung in der Azure-Systemsteuerung oder installieren, wird der Status-Monitor, so wie Sie, auf jedem Computer.)

Sie können die oben genannten Schritte, um eine bereits bereitgestellte Webanwendung tun. Um standard-Abhängigkeit Überwachung zu erhalten, müssen Sie Application Insights-Source-Projekt hinzufügen.

## Diagnostizieren von Leistungsproblemen der Abhängigkeit

Um die Leistung der Anforderungen an den Server zu bewerten:

![Klicken Sie in Ihrer Anwendung in Application Insights auf der Seite "Übersicht" auf der Kachel Leistung](./media/app-insights-dependencies/01-performance.png)

Führen Sie einen Bildlauf nach unten zu den Blick auf das Raster der Anforderungen:

![Liste der Anforderungen mit Mittelwerten und zählt](./media/app-insights-dependencies/02-reqs.png)

Der oberste Eintrag dauert sehr lange. Sehen Sie, ob wir herausfinden, in denen die Verweildauer können.

Klicken Sie auf diese Zeile, um einzelne Ereignisse finden Sie unter:


![Liste der Anforderung vorkommen](./media/app-insights-dependencies/03-instances.png)

Klicken Sie auf einer beliebigen Instanz langer, um weiter zu überprüfen.

> [AZURE.NOTE]Scrollen Sie etwas eine Instanz auswählen. Wartezeit in der Pipeline kann dies bedeuten, dass die Daten für die Instanzen der obersten unvollständig ist.

Führen Sie einen Bildlauf nach unten zu den remote-Abhängigkeit Aufrufe im Zusammenhang mit dieser Anforderung:

![Finden Sie Aufrufe von Remote-Abhängigkeiten, identifizieren Sie ungewöhnliche Dauer zu.](./media/app-insights-dependencies/04-dependencies.png)

Sie sieht wie der Großteil der Zeit mit dem verarbeiten, die in einem Aufruf an einen lokalen Dienst diese Anforderung aufgewendet wurde.

Wählen Sie diese Zeile, um weitere Informationen zu erhalten:


![Klicken Sie auf, über die remote-Abhängigkeit zum Identifizieren der Ursache](./media/app-insights-dependencies/05-detail.png)

Das Detail enthält genügend Informationen, um das Problem zu diagnostizieren.



## Fehler

Fehlgeschlagene Anforderungen vorhanden sind, klicken Sie auf das Diagramm.

![Klicken Sie auf das Diagramm für fehlgeschlagene Anforderungen](./media/app-insights-dependencies/06-fail.png)

Klicken Sie auf eine Anforderungstyp und eine Anforderungsinstanz, um eine fehlerhafte Aufrufe einer remote-Abhängigkeit zu finden.


![Klicken Sie auf den Anforderungstyp, klicken Sie auf die Instanz in eine andere Ansicht der gleichen Instanz abzurufen, klicken Sie auf, um Details der Ausnahme zu erhalten.](./media/app-insights-dependencies/07-faildetail.png)


<!--Link references-->

<!---HONumber=GIT-SubDir_Tue_AM_dede-->