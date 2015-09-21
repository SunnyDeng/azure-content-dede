<properties 
	pageTitle="Ausführen eines Auftrags | Microsoft Azure" 
	description="Ausführen eines Auftrags – Lernpfadsegment"
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
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# Ausführen eines Auftrags

Nachdem Eingabe, Abfrage und Ausgabe für den Auftrag angegeben wurden, können Sie den Stream Analytics-Auftrag starten.

So starten Sie den Auftrag:

1.	Klicken Sie im Dashboard des Auftrags auf „Starten“ am unteren Rand der Seite.

    ![Schaltfläche "Start"](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)

2.	Geben Sie im Dialogfeld, das angezeigt wird, einen Wert für den **Ausgabestart** ein, um zu bestimmen, wann der Auftrag mit der Ausgabe beginnen soll. Die Standardeinstellung für Aufträge, die zuvor noch nicht gestartet wurden, ist die **Startzeit des Auftrags**, was bedeutet, dass der Auftrag sofort mit der Verarbeitung der Daten beginnt. Sie können auch eine **benutzerdefinierte** Zeit in der Vergangenheit (für die Verarbeitung von Verlaufsdaten) oder in der Zukunft (damit die Verarbeitung auf einen späteren Zeitpunkt verschoben wird) angeben. Für Fälle, in denen ein Auftrag bereits zuvor gestartet und beendet wurde, steht die Option **Last Stopped Time** zur Verfügung, um den Auftrag ab dem letzten Zeitpunkt der Ausgabe fortzusetzen und Datenverlust zu vermeiden.

    ![Startzeit](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)

3.	Klicken Sie am unteren Rand des Dialogfelds auf das Häkchen. Der Status ändert sich in „Starten“ und wechselt nach kurzer Zeit zu „Ausführen“, sobald der Auftrag gestartet wurde. Sie können den Fortschritt des Startvorgangs im Notification Hub überwachen:

    ![Status](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->