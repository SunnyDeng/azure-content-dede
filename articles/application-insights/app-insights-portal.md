<properties
	pageTitle="Verwenden des Application Insights-Portals"
	description="Übersicht über die Verwendungsanalyse mit Application Insights"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="08/17/2015"
	ms.author="awills"/>

# Verwenden des Application Insights-Portals

Nachdem Sie [Application Insights für Ihr Projekt eingerichtet haben](app-insights-overview.md), werden Telemetriedaten zur Leistung und Nutzung Ihrer App in der Application Insights-Ressource Ihres Projekts im [Azure-Portal](https://portal.azure.com) angezeigt.

## Suchen von Telemetriedaten in Azure

Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und wechseln Sie zur Application Insights-Ressource, die Sie für Ihre App erstellt haben.

![Wählen Sie „Durchsuchen“, „Application Insights“ und dann Ihre App aus.](./media/app-insights-portal/00-start.png)

Auf der Übersichtsseite finden Sie grundlegende Telemetriedaten sowie Links zu weiteren Daten. Der Inhalt hängt vom Typ Ihrer App ab und kann angepasst werden.

## Das Blatt für die App-Übersicht

Das Blatt „Übersicht“ für Ihre App enthält die wichtigsten Diagramme für die Überwachung von Leistung und Nutzung. Der Inhalt hängt vom Typ Ihrer App ab und kann auf jeden Fall angepasst werden.


### Anpassen des Blatts „Übersicht“ 

Legen Sie fest, was in der Übersicht angezeigt werden soll. Unter „Anpassen“ können Sie Abschnittstitel einfügen, Kacheln und Diagramme anordnen, Elemente entfernen und neue Kacheln und Diagramme aus dem Katalog hinzufügen.

![Klicken Sie auf „...“ und „Anpassen“. Ziehen Sie Kacheln und Diagramme. Fügen Sie Kacheln aus dem Katalog hinzu.](./media/app-insights-portal/020-customize.png)


## Erstellen eigener Metrikdiagramme und -raster

### Bearbeiten von Diagrammen und Rastern

So fügen Sie dem Blatt ein neues Diagramm hinzu:

![Wählen Sie im Metrik-Explorer "Diagramm hinzufügen"](./media/app-insights-metrics-explorer/04-add.png)

Wählen Sie ein vorhandenes oder neues Diagramm aus, um dessen Anzeige zu bearbeiten:

![Wählen Sie eine oder mehrere Metriken aus](./media/app-insights-metrics-explorer/08-select.png)

Sie können in einem Diagramm mehr als eine Metrik anzeigen, wenngleich es Einschränkungen bezüglich der Kombinationen gibt, die zusammen angezeigt werden können. Sobald Sie eine Metrik auswählen, werden einige der anderen deaktiviert.

Wenn Sie [benutzerdefinierte Metriken](app-insights-api-custom-events-metrics.md#track-metric) in Ihrer App (Aufrufe von "TrackMetric" und "TrackEvent") programmiert haben, werden sie hier aufgelistet.

### Segmentieren der Daten

Wählen Sie ein Diagramm oder Raster aus, aktivieren Sie das Gruppieren, und wählen Sie eine Eigenschaft, nach der gruppiert werden soll:

![Wählen Sie "Gruppierung EIN" und dann in "Gruppieren nach" eine Eigenschaft aus](./media/app-insights-metrics-explorer/15-segment.png)

Wenn Sie benutzerdefinierte Metriken in Ihrer App programmiert haben und diese [Eigenschaftswerte](app-insights-api-custom-events-metrics.md#properties) enthalten, können Sie die Eigenschaft in der Liste auswählen.

Ist das Diagramm für segmentierte Daten zu klein? Passen Sie die Höhe an:

![Passen Sie den Schieberegler an](./media/app-insights-metrics-explorer/18-height.png)

### Filtern der Daten

So zeigen Sie nur die Metriken für eine ausgewählte Gruppe von Eigenschaftswerten an:

![Klicken Sie auf "Filter", erweitern Sie eine Eigenschaft, und überprüfen Sie einige Werte](./media/app-insights-metrics-explorer/19-filter.png)

Wenn Sie für eine bestimmte Eigenschaft keine Werte auswählen, entspricht dies der Auswahl aller Werte ohne Angabe von Filtern.

Beachten Sie die Anzahlen von Ereignissen neben jedem Eigenschaftswert. Wenn Sie Werte einer Eigenschaft auswählen, werden neben anderen Eigenschaftswerten die Anzahlen angepasst.

### Speichern des Blatts "Metriken"

Wenn Sie einige Diagramme erstellt haben, speichern Sie sie als Favoriten. Wenn Sie mit einem Organisationskonto arbeiten, können Sie wählen, ob Sie sie für andere Teammitglieder freigeben.

![Wählen Sie "Favoriten"](./media/app-insights-metrics-explorer/21-favorite-save.png)

Um das Blatt erneut zu verwenden, **wechseln Sie zum Blatt "Übersicht"** und öffnen "Favoriten":

![Wählen Sie "Favoriten" auf dem Blatt "Übersicht" aus](./media/app-insights-metrics-explorer/22-favorite-get.png)

Wenn Sie beim Speichern den Zeitbereich "Relativ" ausgewählt haben, wird das Fenster mit den neuesten Metriken aktualisiert. Wenn Sie den Zeitraum "Absolut" gewählt haben, werden jedes Mal dieselben Daten gezeigt.

### Zurücksetzen des Blatts

Wenn Sie ein Blatt bearbeiten, aber dann lieber wieder mit den ursprünglich gespeicherten Daten weiterarbeiten möchten, klicken Sie auf "Zurücksetzen".

![In den Schaltflächen am oberen Rand des Metrik-Explorers](./media/app-insights-metrics-explorer/17-reset.png)

## Erstellen einer Suchseite

Öffnen Sie die Diagnosesuche:

![Diagnosesuche öffnen](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Öffnen Sie das Blatt "Filter", und wählen Sie die Ereignistypen, die Sie anzeigen möchten. (Wenn Sie später die Filter wiederherstellen möchten, mit denen Sie das Blatt geöffnet haben, klicken Sie auf "Zurücksetzen".)

![Wählen Sie "Filter" und dann Telemetrietypen](./media/app-insights-diagnostic-search/02-filter-req.png)

### Filtern von Eigenschaftswerten

Sie können Ereignisse anhand der Werte ihrer Eigenschaften filtern. Die verfügbaren Eigenschaften hängen von den ausgewählten Ereignistypen ab.

Wählen Sie z. B. Anforderungen mit einem bestimmten Antwortcode aus.

![Erweitern Sie eine Eigenschaft, und wählen Sie einen Wert](./media/app-insights-diagnostic-search/03-response500.png)

Das Auswählen keiner Werte einer bestimmten Eigenschaft hat dieselbe Wirkung wie das Auswählen aller Werte, nämlich dass das Filtern anhand dieser Eigenschaft deaktiviert wird.


### Eingrenzen der Suche

Beachten Sie, dass die Zahlen rechts neben den Filterwerten anzeigen, wie viele Vorkommen es in der aktuell gefilterten Gruppe gibt.

In diesem Beispiel ist es klar, dass die Anforderung `Reports/Employees` die Mehrzahl der 500-er Fehler ausmacht:

![Erweitern Sie eine Eigenschaft, und wählen Sie einen Wert](./media/app-insights-diagnostic-search/04-failingReq.png)

Wenn Sie darüber hinaus auch anzeigen möchten, welche andere Ereignisse in diesem Zeitraum aufgetreten sind, können Sie **Ereignisse mit nicht definierten Eigenschaften einbeziehen** aktivieren.

### Speichern der Suche

Wenn Sie alle gewünschten Filter festgelegt haben, können Sie die Suche als Favoriten speichern. Wenn Sie mit einem Organisationskonto arbeiten, können Sie wählen, ob Sie sie für andere Teammitglieder freigeben.

![Klicken Sie auf "Favoriten", legen Sie den Namen fest, und klicken Sie auf "Speichern"](./media/app-insights-diagnostic-search/08-favorite-save.png)


Um die Suche erneut zu verwenden, **wechseln Sie zum Blatt "Übersicht"** und öffnen "Favoriten":

![Kachel "Favoriten"](./media/app-insights-diagnostic-search/09-favorite-get.png)

Wenn Sie mit dem Zeitraum "Relativ" gespeichert haben, enthält das neu geöffnete Blatt die neuesten Daten. Wenn Sie mit dem Zeitraum "Absolut" gespeichert haben, werden jedes Mal dieselben Daten gezeigt.

<!---HONumber=Oct15_HO3-->