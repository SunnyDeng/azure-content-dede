<properties 
	pageTitle="Vorgangsprotokolle | Microsoft Azure" 
	description="Stream Analytics-Vorgangsprotokolle" 
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

# Vorgangsprotokolle

Alle Azure-Dienste stellen Benutzern operative Protokollierungsnachrichten bereit, um Details im Zusammenhang mit Verwaltungsvorgängen zu erfassen. In Azure Stream Analytics können diese Informationen für Debugzwecke wie z. B. das Anzeigen des Auftragsstatus, Auftragsfortschritts und von Fehlermeldungen verwendet werden, um den Fortschritt eines Auftrags im Laufe der Zeit von Beginn an, während der Verarbeitung und bis zur Ausgabe nachzuverfolgen.

## Suchen nach Vorgangsprotokollen

Für den Zugriff auf Vorgangsprotokolle gibt es zwei Möglichkeiten:

- Dashboard des Stream Analytics-Auftrags  
- Verwaltungsdienste im Azure-Portal  

## Dashboard des Stream Analytics-Auftrags

Auf der Dashboard-Registerkarte eines Stream Analytics-Auftrags wird eine Verknüpfung mit den zugehörigen Protokollen angezeigt. Wenn Sie auf diese Verknüpfung klicken, werden die Filter so festgelegt, dass aktuelle Protokolle für diesen Auftrag angezeigt werden.

  ![Verwaltungsdienste auswählen](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)

## Verwaltungsdienste im Azure-Portal

So navigieren Sie manuell zu den Vorgangsprotokollen für Stream Analytics und andere Dienste im Azure-Portal:

1.	Klicken Sie im [Azure-Portal](https://manage.windowsazure.com) auf **Verwaltungsdienste**.
2.	Wählen Sie **Stream Analytics** für **Typ** und den Namen des Auftrags für **Dienstname** aus.  

    ![Stream Analytics auswählen](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)

## Verwendung von Vorgangsprotokollen

Sie können nach Zeitraum und Status filtern, um die Protokolle für Ihren Auftrag anzuzeigen.

Klicken Sie am unteren Fensterrand auf die Schaltfläche **Details**, um weitere Details zu einem ausgewählten Ereignis anzuzeigen.

  ![Details auswählen](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)

## Debuggen eines fehlerhaften Auftrags

Klicken Sie auf das Suchsymbol, und geben Sie „fehlerhaft“ ein. Dadurch wird ein Ergebnis aller Protokolle mit Fehlern zurück.

  ![Debuggen eines fehlerhaften Auftrags](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)

Wählen Sie einen der Fehler aus, und klicken auf die **Details**, um weitere Informationen zu diesem Fehler zu erhalten. Einige Fehlermeldungen enthalten auch Informationen zur Behebung des Problems.

  ![Vorgangsdetails](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)

Für den Fall, dass Sie den [Support](http://azure.microsoft.com/support/options/) kontaktieren dem Team über das [MSDN-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics) Informationen bereitstellen müssen, beachten Sie die Vorgangsdetails, insbesondere die **Korrelations-ID**.

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->