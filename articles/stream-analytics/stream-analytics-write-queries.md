<properties 
	pageTitle="Schreiben von Abfragen in Stream Analytics | Microsoft Azure" 
	description="Schreiben von Abfragen in Stream Analytics und Abfragen von Daten | Lernpfadsegment."
	keywords="Vorgehensweise für das Schreiben von Abfragen, Abfragen von Daten, Schreiben einer Abfrage, Schreiben von Abfragen"
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
	ms.date="12/11/2015" 
	ms.author="jeffstok"/>

# Schreiben von Abfragen in Stream Analytics

Das Schreiben von Abfragen für die Datenstrom-Verarbeitungslogik in Azure Stream Analytics wird als „beständige Abfrage“ implementiert, die vor dem Starten eines Auftrags definiert und für Daten ausgeführt wird, während sie den Auftrag erreicht. Die Datenumwandlung wird in einer SQL-ähnlichen Abfragesprache ausgedrückt, die größtenteils eine Teilmenge von T-SQL mit einigen hinzugefügten Spracherweiterungen wie [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) ist, die zum Ausdrücken von zeitlicher Semantik verwendet wird.

## Schreiben von Abfragen: ##

1. Klicken Sie im Stream Analytics-Auftrag im Azure-Verwaltungsportal auf **Abfrage**.

    ![Auswählen der Abfrage](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)

    Klicken Sie im Azure-Vorschauportal auf **Abfrage**.

    ![Vorschau – Auswählen der Abfrage](./media/stream-analytics-write-queries/query-preview-portal.png)

2.	Neue Aufträge verfügen über Abfragevorlagen, die Ihnen beim Einstieg helfen. Abfragevorlagen führen eine "Pass-Through"-Abfrage durch, die alle Felder aus den Eingabeereignissen in die Ausgabe überträgt.

    - Wenn Sie mindestens eine Eingabe und Ausgabe für den Auftrag definiert haben, können Sie den Platzhalter "[YourOutputAlias]" und die Felder "[YourInputAlias]" durch die Aliase der Eingabe und Ausgabe ersetzen, die Sie zuerst verwenden möchten. Darüber hinaus können Sie die Abfrage noch immer im Azure-Portal erstellen und testen, ohne Eingaben und Ausgaben für den Auftrag zu definieren.
    - Wenn Sie mehr als eine einfache Pass-Through-Abfrage verarbeiten möchten, können Sie die Abfragedefinition bearbeiten. Betrachten Sie zum Einstieg in die Erstellung von Abfragen einige der [hier](stream-analytics-stream-analytics-query-patterns.md) erfassten allgemeinen Abfragemuster.  
  
    ![Fenster mit Abfragedaten](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)

## Zum Überprüfen, ob die Abfrage von Daten funktioniert: ##

Sie können testen, ob sich die Abfrage wie erwartet verhält, indem Sie sie im Browser über eine oder mehrere lokale JSON-Dateien mit den Testdaten ausführen. Dadurch wird weder der Auftrag gestartet noch gibt es Auswirkungen auf die Abrechnung.

> [AZURE.NOTE]Derzeit wird das Testen von Abfragen im Browser im Azure-Vorschauportal nicht unterstützt.

1.	Stellen Sie sicher, dass keine Fehler in der Abfrage enthalten sind (andernfalls ist die Schaltfläche "Testen" deaktiviert), und klicken Sie dann auf die Schaltfläche "Testen".  

    ![Testen der Abfrage von Daten](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)

2.	Sie werden aufgefordert, für jede Eingabe die Dateien anzugeben, auf die in der Abfrage verwiesen wird. In diesem Beispiel bleibt die Vorlageabfrage so, wie sie ist, damit Sie im Dialogfeld dazu aufgefordert werden, eine Eingabe mit dem Namen "yourinputalias" vorzunehmen.

    ![Testen der Datenabfrage](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)

3.	Navigieren Sie zu einer Testdatei. Es sind mehrere Beispieldateien auf [Github] (https://github.com/Azure/azure-stream-analytics/tree/master/Sample Daten) erhältlich und Sie können auch Beispieldaten aus Ihren eigenen Datenstromeingaben über die Funktion "Beispieldaten" in der Eingaberegisterkarte abrufen.

    ![Abfrageeingabe](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)

4.	Nach dem Schließen des Dialogfelds läuft die Abfrage über die Testdaten und Sie sehen die Ergebnisse am unteren Rand der Abfrageseite.

    ![Abfragezusammenfassung](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1217_2015-->