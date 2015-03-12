<properties 
	pageTitle="Skalieren von Websites" 
	description="Erfahren Sie, wie Sie Ihren Hostingplan in Azure skalieren." 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="application-insights" 
	documentationCenter=""/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2014-11-04" 
	ms.author="stepsic"/>

# Skalieren von Websites

Im Azure-Vorschauportal können Sie die Anzahl der Instanzen Ihres Webhosting-Plans manuell festlegen oder Parameter für die automatische Skalierung eingeben. Vor der Konfiguration der Skalierung für Ihren Webhosting-Plan ist zu beachten, dass die Größe der Instanzen Auswirkungen auf die Skalierung hat. Größere Instanzen haben mehr Kernspeicher und mehr Arbeitsspeicher, erzielen also bei derselben Anzahl Instanzen eine bessere Leistung.

Die Skalierung hat Auswirkungen auf den gesamten Webhosting-Plan. Wenn Sie eine Website erstellen, können Sie entweder einen komplett neuen Webhostingplan aufstellen oder von einem bereits vorhandenen Plan ausgehen. Sobald der Webhosting-Plan vorliegt, nutzen alle Sites gemeinsam dieselben Instanzen, werden also auch alle gemeinsam skaliert.

## Skalieren eines Webhosting-Plans

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Durchsuchen** und dann auf **Websites**, und klicken Sie dann auf den Namen der Website, um die Seite zu öffnen.
2. Im Bereich **Skalieren** unter **Vorgänge** wird im Fenster "Website" der Status des Webhosting-Plans angezeigt: **Aus**, wenn Sie manuell skalieren, **Performance**, wenn Sie mit einer oder mehrerer Performance-Metriken skalieren, und **Zeitplan**, wenn Sie mehrere automatische Skalierprofile aktiviert haben.  
    ![Scale part](./media/insights-how-to-scale/Insights_ScalePartOff.png)
3. Wenn Sie in diesem Teilbereich klicken, gelangen Sie zu dem Fenster **Skalieren**.   Oben in diesem Skalierungsfenster wird der Verlauf verschiedener automatischer Skalierungsvorgänge für Ihren Webhosting-Plan angezeigt.

    ![Scale blade](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
4. Die Anzahl der virtuellen Maschinen, auf denen Ihr Webhosting-Plan ausgeführt wird, können Sie über den Schieberegler **Instanzen** manuell einstellen.
5. Wenn Sie die Anzahl der Instanzen je nach Last automatisch anpassen möchten, wählen Sie im Modus **Automatische Skalierung** **Performance** aus. In diesem Fall können Sie **Zeitplan** im Azure -Vorschauportal nicht auswählen.  
    ![Scale blade with CPU Percentage](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png) 
6. Durch Auswahl von "Performance" veranlassen Sie zwei Änderungen:
Neben     - **Instance Range** können Sie nun eine Mindestanzahl und eine maximale Anzahl Instanzen auswählen. Die automatische Skalierung sorgt dafür, dass Sie, unabhängig von der Last, immer in diesem Bereich bleiben.
    - Im Abschnitt **Ziel-Metrik** können Sie die Leistungsmetriken festlegen.
7. Im Abschnitt **CPU-Prozente** können Sie einen Zielwert für den durchschnittlichen CPU-Prozentsatz aller Instanzen in Ihrem Webhosting-Plan festlegen. Wenn der durchschnittliche CPU-Prozentsatz den von Ihnen festgelegten Höchstwert überschreitet, wird hochskaliert.

Wenn die automatische Skalierung aktiviert ist, erscheint **Performance** im Skalierungsteilbereich des Fensters "Website", und der Skalierungsablauf wird im Diagramm dargestellt:

![Scale blade with CPU Percentage](./media/insights-how-to-scale/Insights_ScalePartBladeOn.png) 

Hinweis: Im Azure-Vorschauportal können Sie die Anzahl der Instanzen eines Webhosting-Plans im Modus Shared nicht ändern.

## Erweiterte Skalierung

Neu im Azure-Vorschauportal ist die Möglichkeit, basierend auf anderen Metriken als dem CPU-Prozentsatz zu skalieren und außerdem mit mehreren, komplexeren Regeln das Hoch- und Herunterskalieren zu optimieren.

### Skalieren anhand anderer Leistungsmetriken
Die Skalierung kann zusätzlich zum CPU-Prozentsatz auf folgenden Kriterien basieren:

- "Average Memory" - Wenn der durchschnittlich auf den Instanzen belegte Speicherplatz den spezifizierten Wert über- oder unterschreitet, werden Instanzen hinzugefügt oder entfernt.
- "HTTP Queue Depth" oder "Disk Queue Depth" - Wenn die Anzahl der Nachrichten in der Warteschlange der HTTP-Anfragen bzw. die Länge der Warteschlange zur Festplatte den spezifizierten Wert über- oder unterschreitet, werden Instanzen hinzugefügt oder entfernt.

Es gibt zwei Möglichkeiten der Skalierung auf Basis einer anderen Metrik. Soll nur auf Basis einer einzelnen Metrik skaliert werden, wählen Sie die spitze Klammer neben dem Schieberegler **CPU Percentage** aus. Das Fenster "Metrik-Details" wird geöffnet:

![Entry point to scale metrics](./media/insights-how-to-scale/Insights_ScaleMetricChevron.png)

Zur Skalierung anhand mehrerer Metriken klicken Sie in der Befehlsleiste auf **Metrik hinzufügen**:

![Add metrics](./media/insights-how-to-scale/Insights_AddMetric.png)

Das Fenster "Metrik-Detail" enthält alle Steuerelemente, die Sie für die Einrichtung eines optimalen Skalierungsprofils brauchen. Wählen Sie oben in diesem Fenster die neue Metrik für die Skalierung aus.

### Skalieren in mehreren Schritten

Unterhalb des Metrik-Diagramms werden zwei Abschnitte angezeigt: **Scale up rules** und **Scale down rules**. Der Dienst wird hochskaliert, wenn **eine beliebige** der Hochskalierungsregeln zur Anwendung kommt. Umgekehrt wird der Dienst herunterskaliert, wenn die Bedingungen **aller** Herunterskalierungsregeln zutreffen.

Für jede Regel wählen Sie:

- "Condition" - entweder "Greater" oder "Less"
- "Threshold" - die Zahl, die diese Metrik überschreiten muss, damit die Aktion ausgelöst wird
- "Over Past" - die Anzahl der Minuten, die diese Metrik durchschnittlich überschritten wird
- "Scale up by" oder "Scale down by" - der Umfang der Skalierungsaktion
- "Cool down" - wie lange diese Regel nach der vorherigen Skalierungsaktion bis zur nächsten Skalierung warten soll

![Multiple scale rules](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Wenn Sie mehrere Skalierungsregeln definieren, können Sie bei Leistungsschwankungen aggressiver nach oben oder unten skalieren. Sie können beispielsweise zwei Skalierungsregeln definieren:

1. Um 1 Instanz hochskalieren, wenn der CPU-Prozentsatz über 60 % liegt.
2. Um 3 Instanzen hochskalieren, wenn der CPU-Prozentsatz über 85 % liegt.

Mit dieser Regel veranlassen Sie die Bereitstellung von zwei zusätzlichen Instanzen, wenn die Last vor einer Skalierung 85 % überschreitet. 

<!--HONumber=46--> 
