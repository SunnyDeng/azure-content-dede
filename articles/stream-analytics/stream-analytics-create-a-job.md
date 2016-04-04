<properties 
	pageTitle="Erstellen eines Auftrags zur Verarbeitung von Datenanalysen für Stream Analytics | Microsoft Azure" 
	description="Erstellen eines Auftrags zur Verarbeitung von Datenanalysen für Stream Analytics | Lernpfadsegment."
	keywords="Verarbeitung von Datenanalysen"
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
	ms.date="03/18/2016" 
	ms.author="jeffstok"/>

# Erstellen eines Auftrags zur Verarbeitung von Datenanalysen für Stream Analytics

Die Ressource der obersten Ebene in Azure Stream Analytics ist ein Stream Analytics-Auftrag. Er besteht aus einer oder mehreren Eingabedatenquellen, einer Abfrage zum Ausdrücken der Datentransformation und mindestens einem Ausgabeziel, in das Ergebnisse geschrieben werden. Dadurch kann der Benutzer die Verarbeitung von Datenanalysen für Streamingdatenszenarien durchführen.

Erstellen Sie zuerst einen neuen Stream Analytics-Auftrag, um mit der Verwendung von Stream Analytics zu beginnen. Beachten Sie, dass diese Aktion keine Auswirkungen auf die Abrechnung hat, bis der Auftrag gestartet wird.

1.  Melden Sie sich online beim [klassischen Azure-Portal](http://manage.windowsazure.com) oder dem [Azure-Portal](https://portal.azure.com/) an.
2.  Im Portal: Klicken Sie auf **Neu**, dann je nach Portal auf **Datendienste** oder **Datenanalysen**, klicken Sie anschließend auf **Azure Stream Analytics** oder **Stream Analytics** und dann auf **Schnellerfassung**.

    ![Assistent für Aufträge zur Verarbeitung von Datenanalysen](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

    ![Erstellen eines Auftrags zur Verarbeitung von Datenanalysen](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)

3.  Geben Sie die gewünschte Konfiguration für den Stream Analytics-Auftrag an.
	- Geben Sie im Feld **Auftragsname** einen Namen zur Identifizierung des Stream Analytics-Auftrags ein. Wenn der **Auftragsname** überprüft wurde, wird im Auftragsnamenfeld ein grünes Häkchen angezeigt. Der **Auftragsname** darf nur alphanumerische Zeichen und den Bindestrich „-“ enthalten und muss zwischen 3 und 63 Zeichen lang sein.
	- Verwenden Sie die **Region** oder **Standort** im Azure-Portal, um den geografischen Standort anzugeben, an dem der Auftrag ausgeführt werden soll.
	- Bei Verwendung des Azure-Portals wählen oder erstellen Sie ein Speicherkonto für die Verwendung als das **regionale Überwachungskonto für Speicher**. Dieses Speicherkonto wird zum Speichern von Überwachungsdaten für alle Stream Analytics-Aufträge verwendet, die in dieser Region ausgeführt werden.
	- Bei Verwendung des Azure-Portals geben Sie eine neue oder vorhandene **Ressourcengruppe** an, um verwandte Ressourcen für Ihre Anwendung zu speichern.

4.  Sobald die Optionen für den neuen Stream Analytics-Auftrag konfiguriert sind, klicken Sie auf **Stream Analytics-Auftrag erstellen**. Die Erstellung des Stream Analytics-Auftrags kann einige Minuten dauern. Sie können den Fortschritt im Benachrichtigungshub überwachen, um den Status zu überprüfen.

    ![Benachrichtigungs-Hub für Aufträge zur Verarbeitung von Datenanalysen](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

    ![Auftrag zur Verarbeitung von Datenanalysen im Azure-Portal – Auftrag erstellen](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)

5.  Der neue Auftrag wird mit dem Status **Erstellt** aufgeführt. Beachten Sie, dass die Schaltfläche **Start** deaktiviert ist. Sie müssen die Auftragseingabe, -abfrage und -ausgabe konfigurieren, bevor Sie den Auftrag starten können.

    ![Auftrag zur Verarbeitung von Datenanalysen – Auftragsstatus](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

    ![Auftrag zur Verarbeitung von Datenanalysen im Azure-Portal – Auftragsstatus](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0323_2016-->