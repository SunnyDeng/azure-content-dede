<properties 
	pageTitle="Echtzeitereignisverarbeitung mit Stream Analytics | Microsoft Azure" 
	description="Erfahren Sie, wie durch die Interaktion einer Reihe von Azure-Diensten Echtzeitereignisverarbeitung und Analysen ermöglicht werden." 
	services="stream-analytics,event-hubs,storage,sql-database" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="stream-analytics" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="jeffstok"/>

# Referenzarchitektur: Echtzeitereignisverarbeitung mit Microsoft Azure Stream Analytics

Die Referenzarchitektur für Echtzeit Ereignisverarbeitung mit Azure-Stream-Analysen soll ein generischer Plan für die Bereitstellung von Echtzeit-Plattform als Service (PaaS) Datenstromverarbeitung Lösung mit Microsoft Azure bereitstellen.

## Zusammenfassung

In der Vergangenheit stützten sich Analyselösungen auf Funktionen, wie z. B. ETL (Extract, Transform, Load) und Datawarehousing, wobei die Daten vor der Analyse gespeichert werden. Veränderte Anforderungen, darunter immer schneller eintreffende Daten, bringen dieses vorhandene Modell an seine Grenzen. Die Möglichkeit, Daten in sich bewegenden Streams vor der Speicherung zu analysieren, ist eine Lösung, und obwohl diese Fähigkeit nicht neu ist, wird der Ansatz in den verschiedenen Zweigen der Branche selten verwendet.

Microsoft Azure stellt einen umfassenden Katalog von Analysetechnologien bereit, die ein ganzes Spektrum an verschiedenen Lösungsszenarien und Anforderungen unterstützen können. Angesichts der vielfältigen Angebote kann die Auswahl der für eine End-to-End-Lösung bereitzustellenden Azure-Dienste eine Herausforderung darstellen. In diesem Artikel werden die Funktionen und die Interaktion zwischen verschiedenen Azure-Diensten beschrieben, die eine Ereignis-Streaming-Lösung unterstützen. Darüber hinaus werden einige Szenarien erläutert, in denen Kunden von diesem Ansatz profitieren können.

## Inhalt:

- Kurzfassung
- Einführung in Echtzeitanalysen
- Nutzen von Echtzeitdaten in Azure
- Allgemeine Szenarien für Echtzeitanalysen
- Architektur und Komponenten
	- Datenquellen
	- Datenintegrationsebene
	- Echtzeitanalysenebene
	- Datenspeicherebene
	- Präsentations-/Nutzungsebene
- Zusammenfassung

**Autor:** Charles Feddersen, Lösungsarchitekt, Data Insights Center of Excellence, Microsoft Corporation

**Veröffentlicht:** Januar 2015

**Version:** 1.0

**Download:** [Referenzarchitektur: Echtzeitereignisverarbeitung mit Microsoft Azure Stream Analytics](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)


## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

 

<!---HONumber=Oct15_HO2-->