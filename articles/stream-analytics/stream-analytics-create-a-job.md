<properties 
	pageTitle="Erstellen eines Auftrags | Microsoft Azure" 
	description="Erstellen eines Auftrags – Lernpfadsegment"
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

# Erstellen eines Auftrags

Die Ressource der obersten Ebene in Azure Stream Analytics ist ein Stream Analytics-Auftrag. Er besteht aus einer oder mehreren Eingabedatenquellen, einer Abfrage zum Ausdrücken der Datentransformation und mindestens einem Ausgabeziel, in das Ergebnisse geschrieben werden.

Erstellen Sie einen neuen Stream Analytics-Auftrag, um mit der Verwendung von Stream Analytics zu beginnen. Beachten Sie, dass diese Aktion keine Auswirkungen auf die Abrechnung hat, bis der Auftrag gestartet wird.

1.  Melden Sie sich online beim [Microsoft Azure-Portal](http://manage.windowsazure.com) an.
2.  Klicken Sie auf **Neu**, auf **Data Services**, auf **Stream Analytics** und dann auf **Schnellerfassung**.

    ![Schnellerstellungs-Assistent](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

3.  Geben Sie die gewünschte Konfiguration für den Stream Analytics-Auftrag an.
	- Geben Sie im Feld **Auftragsname** einen Namen zur Identifizierung des Stream Analytics-Auftrag ein. Wenn der **Auftragsname** überprüft wurde, wird im Auftragsnamenfeld ein grünes Häkchen angezeigt. Der **Auftragsname** darf nur alphanumerische Zeichen und den Bindestrich „-“ enthalten und muss zwischen 3 und 63 Zeichen lang sein.
	- Geben Sie unter **Region** den geografischen Standort an, an dem der Auftrag ausgeführt werden soll.
	- Wählen oder erstellen Sie in **Regionales Überwachungskonto für Speicher** ein Speicherkonto. Dieses Speicherkonto wird zum Speichern von Überwachungsdaten für alle Stream Analytics-Aufträge verwendet, die in dieser Region ausgeführt werden.

4.  Sobald die Optionen für den neuen Stream Analytics-Auftrags konfiguriert sind, klicken Sie auf **Stream Analytics-Auftrag erstellen**. Die Erstellung des Stream Analytics-Auftrags kann einige Minuten dauern. Sie können den Fortschritt im Benachrichtigungshub überwachen, um den Status zu überprüfen.

    ![Benachrichtigungs-Hub](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

5.  Der neue Auftrag wird mit dem Status **Erstellt** aufgeführt. Beachten Sie, dass die Schaltfläche **Start** deaktiviert ist. Sie müssen die Auftragseingabe, -abfrage und -ausgabe konfigurieren, bevor Sie den Auftrag starten können.

    ![Auftragsstatus](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->