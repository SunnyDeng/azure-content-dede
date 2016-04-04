<properties 
	pageTitle="Grundlegendes zur Stream Analytics-Auftragsüberwachung | Microsoft Azure" 
	description="Grundlegendes zur Stream Analytics-Auftragsüberwachung" 
	keywords="Abfrageüberwachung"
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
	ms.date="03/18/2016" 
	ms.author="jeffstok"/>

# Grundlegendes zur Stream Analytics-Auftragsüberwachung und zum Überwachen von Abfragen

## Einführung: die Seite „Überwachen“

Im Azure-Verwaltungsportal und im Azure-Portal werden wichtige Leistungsmetriken angezeigt, die zum Überwachen der Leistung Ihrer Abfragen und Aufträge sowie für die Problembehandlung verwendet werden können.

Klicken Sie im Azure-Verwaltungsportal auf die Registerkarte **Überwachen** eines ausgeführten Stream Analytics-Auftrags, um diese Metriken anzuzeigen. Für die Leistungsmetriken, die auf der Seite „Überwachen“ angezeigt werden, tritt eine Verzögerung von höchstens 1 Minute auf.

  ![Auftragsüberwachungs-Dashboard](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)

Navigieren Sie im Azure-Portal zum Stream Analytics-Auftrag, dessen Metriken Sie interessieren, und zeigen Sie den Abschnitt **Überwachung** an.

  ![Azure-Portal – Auftragsüberwachungs-Dashboard](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)

Beim erstmaligen Erstellen eines Stream Analytics-Auftrags in einer Region müssen Sie "Diagnose" für diese Region konfigurieren. Zu diesem Zweck klicken Sie auf eine beliebige Stelle im Abschnitt **Überwachung**, und das Blatt **Diagnose** wird angezeigt. Hier können Sie die Diagnose aktivieren und ein Speicherkonto für Überwachungsdaten angeben.

  ![Azure-Portal – Konfigurieren der Abfragediagnose](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)

## Verfügbare Metriken für Stream Analytics


| Metrik | Definition |
|--------|-------------|
| Speichereinheitnutzung in % | Die Auslastung der Streamingeinheiten, die einem Auftrag über seine Registerkarte „Skalieren“ zugewiesen sind. Sollte dieser Indikator 80 % erreichen oder darüber steigen, ist die Wahrscheinlichkeit hoch, dass die Ereignisverarbeitung verzögert wird oder keine Fortschritte mehr macht. |
| Eingabeereignisse | Vom Stream Analytics-Auftrag erhaltene Datenmenge im Hinblick auf die Ereignisanzahl. Kann verwendet werden, um sicherzustellen, dass Ereignisse an die Eingabequelle gesendet werden. |
| Eingabeereignisbytes | Vom Stream Analytics-Auftrag erhaltene Datenmenge im Hinblick auf den Durchsatz in Byte. |
| Ausgabeereignisse | Vom Stream Analytics-Auftrag an das Ausgabeziel gesendete Datenmenge im Hinblick auf die Ereignisanzahl. |
| Ereignisse für falsche Reihenfolge | Anzahl der Ereignisse, die in falscher Reihenfolge empfangen und anhand der Richtlinie für die Ereignissortierung entweder verworfen oder mit einem angepassten Zeitstempel versehen wurden. Dies kann von der Konfiguration der Einstellung „Toleranzfenster für Fehlordnung“ beeinflusst werden. |
| Konvertierungsfehler | Anzahl der Datenkonvertierungsfehler im Zusammenhang mit einem Stream Analytics-Auftrag. |
| Laufzeitfehler | Die Anzahl von Fehlern, die beim Ausführen eines Stream Analytics-Auftrags festgestellt werden. |
| Ereignisse bei verspäteter Eingabe | Anzahl von Ereignissen, die spät von der Quelle eintreffen und entweder verworfen wurden oder über eine Anpassung des Zeitstempels verfügen (basierend auf der Konfiguration der Richtlinie für die Ereignissortierung unter der Einstellung „Toleranzfenster für Eingangsverzögerung“). |

## Anpassen der Überwachung im Azure-Verwaltungsportal ##

Bis zu 6 Metriken können in einem Diagramm angezeigt werden.

Wählen Sie Relativ oder Absolut im oberen Bereich des Diagramms, um zwischen relativen (nur endgültiger Wert für jede Metrik) und absoluten Werten (Y-Achse wird angezeigt) umzuschalten.

  ![Abfrageüberwachung relativ und absolut](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)

Metriken können im Überwachungsdiagramm in Aggregationen von 1 Stunde, 12 Stunden, 24 Stunden oder 7 Tagen angezeigt werden.

Wählen Sie 1 Stunde, 24 Stunden oder 7 Tage im oberen Bereich des Diagramms aus, um das Zeitintervall für das Metrikendiagramm zu ändern.

  ![Zeitskala für die Abfrageüberwachung](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)

Sie können Regeln festlegen, dass Sie per E-Mail benachrichtigt werden, falls für den Auftrag ein bestimmter Schwellenwert überschritten wird.

## Anpassen der Überwachung im Azure-Portal ##

Sie können den Diagrammtyp, die angezeigten Metriken und den Uhrzeitbereich in den Einstellungen unter "Diagramm bearbeiten" anpassen. Ausführliche Informationen finden Sie unter [Anpassen der Überwachung](../azure-portal/insights-how-to-customize-monitoring.md).

  ![Azure-Portal, Zeitskala für die Abfrageüberwachung](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)

## Auftragsstatus

Der Status von Stream Analytics-Aufträgen kann im klassischen Azure-Portal in der Liste mit den Aufträgen angezeigt werden. Sie können die Auftragsliste einblenden, indem Sie im klassischen Azure-Portal auf das Stream Analytics-Symbol klicken.

| Status | Definition |
|--------|------------|
| Erstellt | Ein Auftrag wurde erstellt, aber noch nicht gestartet. |
| Wird gestartet | Ein Benutzer hat auf „Auftrag starten“ geklickt, und der Auftrag wird gestartet. |
| Wird ausgeführt | Der Auftrag ist zugewiesen, verarbeitet Eingaben oder wartet auf die Verarbeitung von Eingaben. Wenn ein Auftrag den Status "Wird ausgeführt" aufweist, ohne eine Ausgabe zu erzeugen, ist es wahrscheinlich, dass das Zeitfenster für die Verarbeitung groß oder die Abfragelogik kompliziert ist. Ein weiterer Grund ist möglicherweise, dass derzeit keine Daten an den Auftrag gesendet werden. |
| Wird beendet | Ein Benutzer hat auf „Auftrag beenden“ geklickt, und der Auftrag wird beendet. |
| Beendet | Der Auftrag wurde beendet. |
| Heruntergestuft | Dieser Status gibt an, dass für einen Stream Analytics-Auftrag vorübergehende Fehler aufgetreten sind (z. B. Eingabe/Ausgabe-Fehler, Verarbeitungsfehler, Konvertierungsfehler usw.). Der Auftrag wird weiterhin ausgeführt, aber es werden viele Fehler generiert. Der Kunde muss sich um diesen Auftrag kümmern und kann die Vorgangsprotokolle für die Fehler anzeigen. |
| Fehler | Dies bedeutet, dass der Auftrag aufgrund von Fehlern fehlgeschlagen ist und die Verarbeitung beendet wurde. Der Kunde muss in den Vorgangsprotokollen nachsehen, um die Fehler zu beheben. |
| Wird gelöscht | Gibt an, dass der Auftrag gelöscht wird. |

## Diagnose

Im Azure-Verwaltungsportal enthält das Auftragsdashboard Informationen dazu, wo Sie nach der Diagnose suchen müssen, also im Eingaben-, Ausgaben- bzw. Vorgangsprotokoll. Sie können auf den Link klicken, um auf den entsprechenden Ort zuzugreifen und die Informationen zur Diagnose anzuzeigen.

  ![Fehler bei der Abfrageüberwachung](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)

Das Klicken auf die Eingabe- oder Ausgaberessource liefert ausführliche Diagnoseinformationen. Während der Auftrag ausgeführt wird, erfolgt eine Aktualisierung mit den neuesten Diagnoseinformationen.

  ![Abfragediagnose](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0323_2016-->