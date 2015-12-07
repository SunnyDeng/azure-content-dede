<properties
	pageTitle="Verwenden des Application Insights-Portals"
	description="Nachdem Sie Ihre App zum Senden von Telemetriedaten an Application Insights konfiguriert haben, erfahren Sie in dieser Anleitung, wie Sie sich im Portal zurechtfinden."
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
	ms.date="11/23/2015"
	ms.author="awills"/>

# Verwenden des Application Insights-Portals

Nachdem Sie [Application Insights für Ihr Projekt eingerichtet haben](app-insights-overview.md), werden Telemetriedaten zur Leistung und Nutzung Ihrer App in der Application Insights-Ressource Ihres Projekts im [Azure-Portal](https://portal.azure.com) angezeigt.

## Finden der Telemetriedaten

Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und wechseln Sie zur Application Insights-Ressource, die Sie für Ihre App erstellt haben.

![Wählen Sie „Durchsuchen“, „Application Insights“ und dann Ihre App aus.](./media/app-insights-portal/00-start.png)

Auf der Übersichtsseite finden Sie grundlegende Telemetriedaten sowie Links zu weiteren Daten. Der Inhalt hängt vom Typ Ihrer App ab und kann angepasst werden.



## Zeitbereich

Sie können den Zeitbereich der Diagramme oder Raster auf jedem Blatt ändern.

![Öffnen Sie das Blatt "Übersicht" Ihrer Anwendung im Azure-Portal](./media/app-insights-portal/03-range.png)


Klicken Sie auf "Aktualisieren", wenn Sie einige Daten erwarten, die noch nicht angezeigt wurden. Diagramme aktualisieren sich regelmäßig selbst, wobei die Intervalle für größere Zeiträume länger sind. Im Freigabemodus kann es eine Weile dauern, bis Daten durch die Analysepipeline in ein Diagramm gelangen.

Um einen Teil des Diagramms zu vergrößern, ziehen Sie die Maus über den gewünschten Bereich, und klicken Sie auf das Lupensymbol:

![Ziehen Sie die Maus über einen Teil eines Diagramms.](./media/app-insights-portal/12-drag.png)



## Granularität und Punktwerte

Bewegen Sie die Maus über dem Diagramm, um die Werte der Metriken zu diesem Zeitpunkt anzuzeigen.

![Bewegen Sie den Mauszeiger über einem Diagramm](./media/app-insights-portal/02-focus.png)

Der Wert der Eigenschaft zu einem bestimmten Zeitpunkt wird über das vorherige Abtastintervall aggregiert.

Das Abtastintervall, auch als „Granularität“ bezeichnet, wird oben auf dem Blatt angezeigt.

![Die Kopfzeile eines Blatts.](./media/app-insights-portal/11-grain.png)

Sie können die Granularität auf dem Blatt „Zeitbereich“ anpassen:

![Die Kopfzeile eines Blatts.](./media/app-insights-portal/grain.png)

Die verfügbaren Granularitäten hängen vom ausgewählten Zeitbereich ab. Die expliziten Granularitäten sind Alternativen für die „automatische“ Granularität für den Zeitbereich.

## Das Blatt für die App-Übersicht

Das Übersichtsblatt (bzw. die Seite) für Ihre App zeigt eine Zusammenfassung der wichtigen Diagnosemetriken für Ihre App an und ist das Tor zu den anderen Features des Portals.

Klicken Sie auf:

* **ein beliebiges Diagramm oder eine beliebige Kachel**, um weitere Details anzuzeigen.
* **Diagnose**, um zu den vordefinierten Seiten von anderen Metriken zu gelangen.
* **Metrik-Explorer**, um Metrikseiten Ihrer Wahl zu erstellen.
* **Suche**, um spezifische Instanzen von Ereignissen zu untersuchen, z. B. Anforderungen, Ausnahmen oder Protokollablaufverfolgungen.


![Hauptwege zum Anzeigen von Telemetriedaten](./media/app-insights-portal/010-oview.png)


### Anpassen des Blatts „Übersicht“ 

Legen Sie fest, was in der Übersicht angezeigt werden soll. Unter „Anpassen“ können Sie Abschnittstitel einfügen, Kacheln und Diagramme anordnen, Elemente entfernen und neue Kacheln und Diagramme aus dem Katalog hinzufügen.

![Klicken Sie auf "Bearbeiten". Ziehen Sie Kacheln und Diagramme. Fügen Sie Kacheln aus dem Katalog hinzu. Klicken Sie anschließend auf „Fertig“.](./media/app-insights-portal/020-customize.png)

### Anpassen des Azure-Dashboards


Das Dashboard des Azure-Portals ist die Startseite, die Sie gleich nach der Anmeldung beim Portal sehen. Dort können Sie Kacheln (Gruppen von Diagrammen) aus mehreren Ressourcen platzieren.

Um eine Kachel aus dem Application Insights-Übersichtsblatt auf dem Portaldashboard anzuheften, wählen Sie die Überschrift der Kachel aus und wählen dann „...“.

Wenn Sie ein umfassenderes Dashboard wünschen, verwenden Sie [Power BI](https://azure.microsoft.com/blog/application-insights-content-pack-for-power-bi/) zum Anzeigen der Telemetrie.

## Blätter für Metriken

Wenn Sie sich vom Übersichtsblatt durchklicken, um weitere Details zu erhalten, befinden Sie sich im Metrik-Explorer (auch wenn ein spezifischerer Titel angezeigt wird).

Über die Schaltfläche „Metrik-Explorer“ können Sie außerdem ein neues Blatt erstellen, das Sie bearbeiten und sichern können.


![Klicken Sie auf dem Blatt „Übersicht“ auf „Metriken“.](./media/app-insights-portal/16-metrics.png)

### Bearbeiten von Diagrammen und Rastern

So fügen Sie dem Blatt ein neues Diagramm hinzu:

![Wählen Sie im Metrik-Explorer "Diagramm hinzufügen"](./media/app-insights-portal/04-add.png)

Wählen Sie ein vorhandenes oder neues Diagramm aus, um dessen Anzeige zu bearbeiten:

![Wählen Sie eine oder mehrere Metriken aus](./media/app-insights-portal/08-select.png)

Sie können in einem Diagramm mehr als eine Metrik anzeigen, wenngleich es Einschränkungen bezüglich der Kombinationen gibt, die zusammen angezeigt werden können. Sobald Sie eine Metrik auswählen, werden einige der anderen deaktiviert.

Wenn Sie [benutzerdefinierte Metriken](app-insights-api-custom-events-metrics.md#track-metric) in Ihrer App programmiert haben (Aufrufe von „TrackMetric“ sowie an „TrackEvent“-Aufrufe angefügte Metriken), werden sie hier aufgelistet.

### Segmentieren der Daten

Wählen Sie ein Diagramm oder Raster aus, aktivieren Sie das Gruppieren, und wählen Sie eine Eigenschaft, nach der gruppiert werden soll:

![Wählen Sie "Gruppierung EIN" und dann in "Gruppieren nach" eine Eigenschaft aus](./media/app-insights-portal/15-segment.png)

Wenn Sie benutzerdefinierte Metriken in Ihrer App programmiert haben und diese [Eigenschaftswerte](app-insights-api-custom-events-metrics.md#properties) enthalten, können Sie die Eigenschaft in der Liste auswählen.

Ist das Diagramm für segmentierte Daten zu klein? Passen Sie die Höhe an:

![Passen Sie den Schieberegler an](./media/app-insights-portal/18-height.png)

### Filtern der Daten

So zeigen Sie nur die Metriken für eine ausgewählte Gruppe von Eigenschaftswerten an:

![Klicken Sie auf "Filter", erweitern Sie eine Eigenschaft, und überprüfen Sie einige Werte](./media/app-insights-portal/19-filter.png)

Wenn Sie für eine bestimmte Eigenschaft keine Werte auswählen, entspricht dies der Auswahl aller Werte ohne Angabe von Filtern.

Beachten Sie die Anzahlen von Ereignissen neben jedem Eigenschaftswert. Wenn Sie Werte einer Eigenschaft auswählen, werden neben anderen Eigenschaftswerten die Anzahlen angepasst.

### Speichern des Blatts "Metriken"

Wenn Sie einige Diagramme erstellt haben, speichern Sie sie als Favoriten. Wenn Sie mit einem Organisationskonto arbeiten, können Sie wählen, ob Sie sie für andere Teammitglieder freigeben.

![Wählen Sie "Favoriten"](./media/app-insights-portal/21-favorite-save.png)

Um das Blatt erneut zu verwenden, **wechseln Sie zum Blatt "Übersicht"** und öffnen "Favoriten":

![Wählen Sie "Favoriten" auf dem Blatt "Übersicht" aus](./media/app-insights-portal/22-favorite-get.png)

Wenn Sie beim Speichern den Zeitbereich "Relativ" ausgewählt haben, wird das Fenster mit den neuesten Metriken aktualisiert. Wenn Sie den Zeitraum "Absolut" gewählt haben, werden jedes Mal dieselben Daten gezeigt.

### Zurücksetzen des Blatts

Wenn Sie ein Blatt bearbeiten, aber dann lieber wieder mit den ursprünglich gespeicherten Daten weiterarbeiten möchten, klicken Sie auf "Zurücksetzen".

![In den Schaltflächen am oberen Rand des Metrik-Explorers](./media/app-insights-portal/17-reset.png)

## Suche

Die Suche zeigt einzelne Ereignisse an, z. B. Seitenaufrufe, Anforderungen, Ausnahmen, Protokollablaufverfolgungen und benutzerdefinierte Ereignisse. Aggregierte Metriken oder Instanzen des TrackMetric()-Aufrufs werden nicht angezeigt.

> [AZURE.NOTE]Wenn die Anwendung viele Telemetriedaten generiert (und Sie Version 2.0.0-beta3 oder höher des ASP.NET-SDK verwenden), reduziert das adaptive Stichprobenmodul automatisch die an das Portal gesendete Datenmenge, indem nur ein repräsentativer Bruchteil der Ereignisse gesendet wird. Ereignisse, die mit derselben Anforderung im Zusammenhang stehen, werden als Gruppe aus- oder abgewählt, sodass Sie zwischen verwandten Ereignissen navigieren können. [Erfahren Sie mehr über das Erstellen von Stichproben](app-insights-sampling.md).

Öffnen Sie die Diagnosesuche:

![Diagnosesuche öffnen](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Öffnen Sie das Blatt "Filter", und wählen Sie die Ereignistypen, die Sie anzeigen möchten. (Wenn Sie später die Filter wiederherstellen möchten, mit denen Sie das Blatt geöffnet haben, klicken Sie auf "Zurücksetzen".)

![Wählen Sie "Filter" und dann Telemetrietypen](./media/app-insights-portal/02-filter-req.png)

### Filtern von Eigenschaftswerten

Sie können Ereignisse anhand der Werte ihrer Eigenschaften filtern. Die verfügbaren Eigenschaften hängen von den ausgewählten Ereignistypen ab.

Wählen Sie z. B. Anforderungen mit einem bestimmten Antwortcode aus.

![Erweitern Sie eine Eigenschaft, und wählen Sie einen Wert](./media/app-insights-portal/03-response500.png)

Das Auswählen keiner Werte einer bestimmten Eigenschaft hat dieselbe Wirkung wie das Auswählen aller Werte, nämlich dass das Filtern anhand dieser Eigenschaft deaktiviert wird.

> [AZURE.NOTE]Wenn die Anwendung viele Telemetriedaten generiert, reduziert das adaptive Stichprobenmodul automatisch die an das Portal gesendete Datenmenge, indem nur ein repräsentativer Bruchteil der Ereignisse gesendet wird. Ereignisse, die Teil des gleichen Vorgangs sind, werden als Gruppe aus- oder abgewählt, sodass Sie zwischen verwandten Ereignissen navigieren können. [Erfahren Sie mehr über das Erstellen von Stichproben](app-insights-sampling.md).


### Eingrenzen der Suche

Beachten Sie, dass die Zahlen rechts neben den Filterwerten anzeigen, wie viele Vorkommen es in der aktuell gefilterten Gruppe gibt.

In diesem Beispiel ist es klar, dass die Anforderung `Reports/Employees` die Mehrzahl der 500-er Fehler ausmacht:

![Erweitern Sie eine Eigenschaft, und wählen Sie einen Wert](./media/app-insights-portal/04-failingReq.png)

Wenn Sie darüber hinaus auch anzeigen möchten, welche andere Ereignisse in diesem Zeitraum aufgetreten sind, können Sie **Ereignisse mit nicht definierten Eigenschaften einbeziehen** aktivieren.

### Speichern der Suche

Wenn Sie alle gewünschten Filter festgelegt haben, können Sie die Suche als Favoriten speichern. Wenn Sie mit einem Organisationskonto arbeiten, können Sie wählen, ob Sie sie für andere Teammitglieder freigeben.

![Klicken Sie auf "Favoriten", legen Sie den Namen fest, und klicken Sie auf "Speichern"](./media/app-insights-portal/08-favorite-save.png)


Um die Suche erneut zu verwenden, **wechseln Sie zum Blatt "Übersicht"** und öffnen "Favoriten":

![Kachel "Favoriten"](./media/app-insights-portal/22-favorite-get.png)

Wenn Sie mit dem Zeitraum "Relativ" gespeichert haben, enthält das neu geöffnete Blatt die neuesten Daten. Wenn Sie mit dem Zeitraum "Absolut" gespeichert haben, werden jedes Mal dieselben Daten gezeigt.

<!---HONumber=AcomDC_1125_2015-->