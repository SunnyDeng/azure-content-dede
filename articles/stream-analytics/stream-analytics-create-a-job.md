<properties 
	pageTitle="Erstellen eines Auftrags | Microsoft Azure" 
	description="Erstellen von Aufträgen für Stream Analytics | Lernpfadsegment."
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
	ms.date="12/01/2015" 
	ms.author="jeffstok"/>

# Erstellen eines Stream Analytics-Auftrags

Die Ressource der obersten Ebene in Azure Stream Analytics ist ein Stream Analytics-Auftrag. Er besteht aus einer oder mehreren Eingabedatenquellen, einer Abfrage zum Ausdrücken der Datentransformation und mindestens einem Ausgabeziel, in das Ergebnisse geschrieben werden.

Erstellen Sie einen neuen Stream Analytics-Auftrag, um mit der Verwendung von Stream Analytics zu beginnen. Beachten Sie, dass diese Aktion keine Auswirkungen auf die Abrechnung hat, bis der Auftrag gestartet wird.

1.  Melden Sie sich online beim [klassischen Microsoft Azure-Portal](http://manage.windowsazure.com) oder dem Azure-Portal an.
2.  Klicken Sie im klassischen Azure-Portal auf **Neu**, auf **Data Services**, auf **Stream Analytics** und dann auf **Schnellerfassung**.

    ![Schnellerstellungs-Assistent](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

    Klicken Sie im Azure-Portal auf „Neu“, auf „Daten und Analysen“, und dann auf „Azure Stream Analytics“.

    ![Klassisches Azure-Portal – Auftrag erstellen](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)

3.  Geben Sie die gewünschte Konfiguration für den Stream Analytics-Auftrag an.
	- Geben Sie im Feld **Auftragsname** einen Namen zur Identifizierung des Stream Analytics-Auftrags ein. Wenn der **Auftragsname** überprüft wurde, wird im Auftragsnamenfeld ein grünes Häkchen angezeigt. Der **Auftragsname** darf nur alphanumerische Zeichen und den Bindestrich „-“ enthalten und muss zwischen 3 und 63 Zeichen lang sein.
	- Verwenden Sie die **Region** im klassischen Azure-Portal oder den **Standort** im Azure-Portal, um den geografischen Standort anzugeben, an dem der Auftrag ausgeführt werden soll.
	- Bei Verwendung des klassischen Azure-Portals wählen oder erstellen Sie ein Speicherkonto für die Verwendung als das **regionale Überwachungskonto für Speicher**. Dieses Speicherkonto wird zum Speichern von Überwachungsdaten für alle Stream Analytics-Aufträge verwendet, die in dieser Region ausgeführt werden.
	- Bei Verwendung des Azure-Portals geben Sie eine neue oder vorhandene **Ressourcengruppe** an, um verwandte Ressourcen für Ihre Anwendung zu speichern.

4.  Sobald die Optionen für den neuen Stream Analytics-Auftrag konfiguriert sind, klicken Sie auf **Stream Analytics-Auftrag erstellen**. Die Erstellung des Stream Analytics-Auftrags kann einige Minuten dauern. Sie können den Fortschritt im Benachrichtigungshub überwachen, um den Status zu überprüfen.

    ![Benachrichtigungs-Hub](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

    ![Azure-Portal – Auftrag erstellen](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)

5.  Der neue Auftrag wird mit dem Status **Erstellt** aufgeführt. Beachten Sie, dass die Schaltfläche **Start** deaktiviert ist. Sie müssen die Auftragseingabe, -abfrage und -ausgabe konfigurieren, bevor Sie den Auftrag starten können.

    ![Auftragsstatus](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

    ![Azure-Portal – Auftragsstatus](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1203_2015-->