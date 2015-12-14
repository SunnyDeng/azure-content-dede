<properties 
	pageTitle="Ausgaben hinzufügen | Microsoft Azure" 
	description="Hinzufügen von Ausgaben zu Stream Analytics-Aufträgen | Lernpfadsegment."
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

# **Hinzufügen von Ausgaben** zu Stream Analytics-Aufträgen

Azure Stream Analytics-Aufträge können mit einer oder mehreren Ausgaben verknüpft werden, die eine Verbindung zu einer vorhandenen Datensenke definieren. Während Ihr Stream Analytics-Auftrag eingehende Daten verarbeitet und umwandelt, wird ein Strom von Ausgabeereignissen in die Ausgabe Ihres Auftrags geschrieben.

Stream Analytics-Ausgaben können zum Beziehen von Echtzeitdashboards oder Warnungen, zum Auslösen von nachgelagerten Workflows oder einfach zum Archivieren von Daten für die spätere Stapelverarbeitung verwendet werden. Stream Analytics bietet eine erstklassige Integration in verschiedene Azure-Diensten, die hier ausführlich dokumentiert sind.

So fügen Sie Ihrem Stream Analytics-Auftrag eine Ausgabe hinzu:

1. Klicken Sie im klassischen Azure-Portal auf **Ausgaben** und dann in Ihrem Stream Analytics-Auftrag auf **Ausgabe hinzufügen**.

    ![Ausgaben hinzufügen](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)

    Klicken Sie im Azure-Portal in Ihrem Stream Analytics-Auftrag auf die Kachel **Ausgaben**.

    ![Azure-Portal – Ausgaben hinzufügen](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Geben Sie den Typ der Ausgabe an:

    ![Datentyp auswählen](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)

    ![Azure-Portal – Datentyp auswählen](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Geben Sie im Feld **Ausgabealias** einen Anzeigenamen für diese Ausgabe ein. Dieser Name kann später in der Abfrage Ihres Auftrags zum Verweisen auf die Ausgabe verwendet werden.
    
    Geben Sie den Rest der erforderlichen Verbindungseigenschaften ein, um eine Verbindung mit Ihrer Ausgabe herzustellen. Diese Felder unterscheiden sich je nach Ausgabetyp und sind hier im Detail definiert.

    ![Eigenschaften hinzufügen](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)

4. Je nach Ausgabetyp müssen Sie möglicherweise angeben, wie die Daten serialisieren oder formatiert werden. Die spezifischen Serialisierungseinstellungen für die einzelnen Ausgabetypen sind hier dokumentiert.

    Geben Sie den Rest der erforderlichen Verbindungseigenschaften ein, um eine Verbindung mit Ihrer Datenquelle herzustellen. Diese Felder unterscheiden sich je nach Eingabe- und Quelltyp und sind [hier](stream-analytics-create-a-job.md) im Detail definiert.

    ![Event Hub hinzufügen](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)

    ![Azure-Portal – Event Hub hinzufügen](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)

> [Azure.Note]Jedes Ausgabeelement, das dem Auftrag hinzugefügt wird, muss vorhanden sein, bevor der Auftrag gestartet wird und Ereignisse übertragen werden. Wird beispielsweise ein Blob Storage als Ausgabe verwendet, erstellt der Auftrag nicht automatisch ein Speicherkonto. Es muss vom Benutzer erstellt werden, bevor der ASA-Auftrag gestartet wird.

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1203_2015-->