<properties 
	pageTitle="Starten von Streamingaufträgen in Stream Analytics | Microsoft Azure" 
	description="Ausführen eines Streamingauftrags in Azure Stream Analytics | Lernpfadsegment."
    keywords="Streamingaufträge"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"/>

# Ausführen von Streamingaufträgen in Azure Stream Analytics

Nachdem Eingabe, Abfrage und Ausgabe für den Auftrag angegeben wurden, können Sie den Stream Analytics-Auftrag starten.

So starten Sie den Auftrag:

1.	Klicken Sie im Azure-Portal im Dashboard des Auftrags auf **Starten** am unteren Rand der Seite.

    ![Schaltfläche „Auftrag starten“](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)

    Klicken Sie im Azure-Vorschauportal auf **Starten** am oberen Rand der Seite mit den Aufträgen.

    ![Azure-Vorschauportal, Schaltfläche „Auftrag starten“](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)

2.	Geben Sie einen Wert für den **Ausgabestart** ein, um zu bestimmen, wann der Auftrag mit der Ausgabe beginnen soll. Die Standardeinstellung für Aufträge, die zuvor noch nicht gestartet wurden, ist die **Startzeit des Auftrags**, was bedeutet, dass der Auftrag sofort mit der Verarbeitung der Daten beginnt. Sie können auch eine **benutzerdefinierte** Zeit in der Vergangenheit (für die Verarbeitung von Verlaufsdaten) oder in der Zukunft (damit die Verarbeitung auf einen späteren Zeitpunkt verschoben wird) angeben. Für Fälle, in denen ein Auftrag bereits zuvor gestartet und beendet wurde, steht die Option **Last Stopped Time** zur Verfügung, um den Auftrag ab dem letzten Zeitpunkt der Ausgabe fortzusetzen und Datenverlust zu vermeiden.

    ![Starten von Streamingaufträgen, Zeit](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)

    ![Azure-Vorschauportal, Starten von Streamingaufträgen, Zeit](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)

3.	Bestätigen Sie Ihre Auswahl. Der Status ändert sich in *Starten* und wechselt nach kurzer Zeit zu *Ausführen*, sobald der Auftrag gestartet wurde. Sie können den Fortschritt des **Startvorgangs** im **Notification Hub** überwachen:

    ![Streamingauftrag, Fortschritt](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)

    ![Azure-Vorschauportal, Streamingauftrag, Fortschritt](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0204_2016-->