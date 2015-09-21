<properties 
	pageTitle="Stream Analytics-Warnungen | Microsoft Azure" 
	description="Grundlegendes zu Stream Analytics-Warnungen" 
	keywords="big data analytics,cloud service,internet of things,managed service,stream processing,streaming analytics,streaming data"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# Einrichten von Warnungen

## Seite "Überwachen"

Sie können eine Regel einrichten, die eine Warnung auslöst, wenn eine Metrik eine von Ihnen angegebene Bedingung erreicht.

Zum Beispiel: "Wenn Ausgabeereignisse in den letzten 15 Minuten < 100, wird eine E-Mail-Benachrichtigung an die folgende E-Mail-ID gesendet: xyz@company.com”.

Regeln können auf Metriken über das Portal eingerichtet oder [programmgesteuert](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) über Ereignisprotokolldaten konfiguriert werden.

## Einrichten von Warnungen über das Azure-Portal

Es gibt zwei Möglichkeiten zum Einrichten von Warnungen:

1.	Die Registerkarte "Überwachen" in Ihrem Stream Analytics-Auftrag  
2.	Das Vorgangsprotokoll in den Verwaltungsdiensten  

## Warnungen über die Registerkarte "Überwachen" des Auftrags im Portal

1.	Wählen Sie die Metrik in der Registerkarte "Überwachen", klicken Sie auf die Schaltfläche "Regel hinzufügen" im unteren Teil des Dashboards, und richten Sie dann die Regeln ein.  

    ![Dashboard](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	Definieren Sie den Namen und die Beschreibung der Warnung

    ![Erstellen einer Regel](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	Geben Sie die Schwellenwerte, das Warnungsauswertungsfenster und die Aktionen für die Warnung ein

    ![Definieren von Bedingungen](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## Einrichten von Warnungen über die Vorgangsprotokolle

1.	Wechseln Sie zur Registerkarte **Warnungen** in den Verwaltungsdiensten im [Azure-Portal](https://manage.windowsazure.com)  
2.	Klicken Sie auf **Regel hinzufügen**.  

    ![Kriterien](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	Definieren Sie den Namen und die Beschreibung der Warnung Wählen Sie 'Stream Analytics' als Diensttyp und den Namen des Auftrags als Dienstnamen.

    ![Definieren einer Warnung](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->