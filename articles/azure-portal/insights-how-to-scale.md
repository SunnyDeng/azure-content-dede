<properties 
	pageTitle="Manuelles oder automatisches Skalieren der Instanzenzahl" 
	description="Erfahren Sie, wie Sie Ihre Dienste in Azure skalieren können." 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="stepsic"/>

# Manuelles oder automatisches Skalieren der Instanzenzahl

Im [Azure-Portal](https://portal.azure.com/) können Sie die Anzahl der Instanzen für Ihren Dienst manuell festlegen oder Parameter für die automatische Skalierung festlegen. Dieser Vorgang wird als *horizontales Hochskalieren* oder *horizontales Herunterskalieren* bezeichnet.

Berücksichtigen Sie vor einer Skalierung basierend auf der Instanzenzahl, dass die Skalierung nicht nur von der Instanzenzahl, sondern auch vom **Tarif** beeinflusst wird. Die verschiedenen Tarife beinhalten eine unterschiedliche Anzahl von Kernen und Arbeitsspeichermenge, sodass sie für dieselbe Anzahl von Instanzen eine bessere Leistung bieten (*zentrales Hochskalieren* oder *zentrales Herunterskalieren*). Im vorliegenden Artikel wird insbesondere auf das *horizontale Hochskalieren* und das *horizontale Herunterskalieren* eingegangen.

Sie können im Portal skalieren und mit [REST-API](https://msdn.microsoft.com/library/azure/dn931953.aspx) oder [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) die manuelle oder automatische Skalierung anpassen.

## Manuelles Skalieren

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Durchsuchen**, und navigieren Sie zu der Ressource, die Sie skalieren möchten, z. B. zu einem **App Service-Plan**.

2. Die Kachel **Skalierung** in **Vorgänge** zeigt den Status der Skalierung: **Aus**, wenn Sie eine manuelle Skalierung durchführen, **Ein**, wenn Sie nach einer oder mehreren Leistungsmetriken skalieren. ![Kachel "Skalieren"](./media/insights-how-to-scale/Insights_UsageLens.png)

3. Durch Klicken auf die Kachel gelangen Sie zum Blatt **Skalierung**. Oben auf dem Blatt "Skalierung" wird der Verlauf automatischer Skalierungsvorgänge für den Dienst angezeigt. ![Blatt "Skalieren"](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
    
>[AZURE.NOTE]In diesem Diagramm werden nur Aktionen angezeigt, die im Rahmen der automatischen Skalierung ausgeführt werden. Wenn Sie die Instanzenzahl manuell anpassen, wird diese Änderung im Diagramm nicht angezeigt.

4. Sie können die Anzahl von **Instanzen** über den Schieberegler manuell anpassen.
5. Nach dem Klicken auf **Speichern** wird die Instanzenzahl praktisch sofort auf die gewünschte Anzahl skaliert. 

## Skalierung basierend auf einer voreingestellten Metrik

Wenn Sie die Instanzenzahl basierend auf einer Metrik automatisch anpassen möchten, wählen Sie in der Dropdownliste **Skalieren nach** die gewünschte Metrik aus. Einen **App Service-Plan** können Sie beispielsweise nach **CPU-Prozentsatz** skalieren.

1. Wenn Sie eine Metrik auswählen, werden ein Schieberegler und/oder Textfelder zur Eingabe der Instanzenzahl angezeigt, zwischen denen skaliert werden soll:

    ![Blatt "Skalieren" mit CPU-Prozentsatz](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)
    
    Die automatische Skalierung des Diensts wird niemals über die festgelegten Grenzwerte hinaus oder darunter durchgeführt – unabhängig von der Datenlast.

2. Im zweiten Schritt wählen Sie den Zielbereich für die Metrik aus. Im Abschnitt **CPU-Prozentsatz** können Sie beispielsweise einen Zielwert für den durchschnittlichen CPU-Prozentsatz aller Instanzen in Ihrem Dienst festlegen. Der Dienst wird horizontal hochskaliert, wenn der durchschnittliche CPU-Prozentsatz den definierten Maximalwert übersteigt. Ebenso wird der Dienst horizontal herunterskaliert, wenn der durchschnittliche CPU-Prozentsatz unter den Mindestwert sinkt.

3. Klicken Sie auf **Speichern**. Die automatische Skalierung prüft in Abständen von wenigen Minuten, ob sich Instanzenbereich und -ziel für Ihre Metrik innerhalb der festgelegten Grenzwerte befinden. Wenn Ihre Dienste zusätzlichen Datenverkehr verarbeiten müssen, erhalten Sie ohne Benutzereingriff weitere Instanzen.

## Skalieren basierend auf anderen Metriken

Sie können auch nach anderen Metriken skalieren als denjenigen, die in der Dropdownliste **Skalieren nach** voreingestellt sind. Es ist sogar möglich, einen komplexen Satz an Regeln für das horizontale Hochskalieren und das horizontale Herunterskalieren zu definieren.

### Hinzufügen oder Ändern einer Regel

1. Wählen Sie in der Dropdownliste **Skalieren nach** die Einstellung **Zeitplan- und Leistungsregeln**: ![Leistungsregeln](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. Wenn Sie die automatische Skalierung bereits verwenden, werden die Regeln für diese Skalierung angezeigt.

3. Um basierend auf einer anderen Metrik zu skalieren, klicken Sie auf die Zeile **Regel hinzufügen**. Sie können auch auf eine der vorhandenen Zeilen klicken, um die bisher verwendete Metrik für die Skalierung zu ändern. ![Hinzufügen einer Regel](./media/insights-how-to-scale/Insights_AddRule.png)

4. Jetzt müssen Sie auswählen, nach welcher Metrik die Skalierung erfolgen soll. Beim Auswählen einer Metrik müssen verschiedene Dinge berücksichtigt werden:
    * Die *Ressource*, aus der die Metrik bezogen wird. Typischerweise ist dies gleichzeitig die Ressource, die Sie skalieren. Wenn Sie jedoch nach der Tiefe einer Speicherwarteschlange skalieren, ist die Ressource die Warteschlange, nach der Sie skalieren möchten.
    * Der *Metrikname* selbst. 
    * Die *Zeitaggregation* der Metrik. Diese Einstellung gibt an, wie die Daten im Rahmen der *Dauer* kombiniert werden.
    
5. Nachdem Sie die Metrik ausgewählt haben, geben Sie den Schwellenwert und den Operator für die Metrik an. Beispielsweise können Sie **Größer als** **80 %** festlegen.

6. Anschließend wählen Sie die Aktion, die ausgeführt werden soll. Es gibt verschiedene Arten von Aktionen:
    * Erhöhen oder Verringern um – Führt zum Hinzufügen oder Entfernen der Anzahl von Instanzen, die Sie unter **Wert** definiert haben.
    * Erhöhen oder Verringern in Prozent – Führt zu einer Änderung der Instanzenzahl nach Prozentwerten. Sie können beispielsweise als **Wert** 25 festlegen. Wenn Sie zurzeit über 8 Instanzen verfügen, werden 2 hinzugefügt.
    * Erhöhen oder Verringern bis – Legt die Instanzenzahl auf die in **Wert** definierte Anzahl fest.

7. "Cool down" – Legt fest, wie lange diese Regel nach der vorherigen Skalierungsaktion bis zur nächsten Skalierung warten soll.
    
8. Klicken Sie nach dem Konfigurieren Ihrer Regel auf **OK**.

9. Nachdem Sie alle gewünschten Regeln konfiguriert haben, klicken Sie auf **Speichern**.

### Skalieren in mehreren Schritten

Die oben genannten Beispiele sind recht einfach strukturiert. Wenn Sie eine aggressivere Skalierung nach oben (oder unten) durchführen möchten, können Sie zusätzliche Skalierungsregeln für dieselbe Metrik hinzufügen. Sie können beispielsweise zwei Skalierungsregeln definieren, die auf dem CPU-Prozentsatz basieren:

1. Um 1 Instanz horizontal hochskalieren, wenn der CPU-Prozentsatz über 60 % liegt
2. Um 3 Instanzen horizontal hochskalieren, wenn der CPU-Prozentsatz über 85 % liegt

![Mehrere Skalierungsregeln](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Mit dieser Regel veranlassen Sie die Bereitstellung von zwei zusätzlichen Instanzen, wenn die Last vor einer Skalierung 85 % überschreitet.

## Skalieren basierend auf einem Zeitplan


Standardmäßig wird eine erstellte Skalierungsregel immer angewendet. Sie können dies sehen, wenn Sie auf die Profilüberschrift klicken:

![Profil](./media/insights-how-to-scale/Insights_Profile.png)

Möglicherweise möchten Sie jedoch tagsüber oder unter der Woche eine aggressivere Skalierung durchführen als am Wochenende. Sie könnten Ihren Dienst nach Geschäftsschluss sogar komplett abschalten.

1. Hierzu wählen Sie im Profil anstelle von **Immer** die Einstellung **Wiederholung** und geben an, wie oft das Profil angewendet werden soll.

2. Um beispielsweise ein Profil unter der Woche anzuwenden, deaktivieren Sie in der Dropdownliste **Tage** die Tage **Samstag** und **Sonntag**.

3. Wenn Sie ein Profil erstellen möchten, das tagsüber angewendet wird, legen Sie die **Startzeit** auf die Uhrzeit fest, an der Sie beginnen möchten. ![Standardwiederholung](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. Klicken Sie auf **OK**.

5. Als Nächstes müssen Sie das Profil hinzufügen, das zu den anderen Zeiten angewendet werden soll. Klicken Sie auf die Zeile **Profil hinzufügen**. ![Nach Geschäftsschluss](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. Benennen Sie das neue, zweite Profil. Sie könnten beispielsweise den Namen **Nach Geschäftsschluss** verwenden.

7. Wählen Sie dann erneut **Wiederholung**, und wählen Sie die Instanzenzahl, die Sie für diese Zeit verwenden möchten.

8. Wählen Sie wie beim Standardprofil die **Tage**, für die dieses Profil gelten soll, und geben Sie die **Startzeit** tagsüber an.

>[AZURE.NOTE]Die automatische Skalierung verwendet die Sommerzeitregeln für die gewählte **Zeitzone**. Während der Sommerzeit wird als UTC-Zeitunterschied jedoch der Unterschied zur Basiszeitzone angezeigt, nicht der Unterschied zur Sommerzeit.

9. Klicken Sie auf **OK**.

10. Jetzt müssen Sie die Regeln hinzufügen, die Sie für das zweite Profil anwenden möchten. Klicken Sie auf **Regel hinzufügen**, und definieren Sie eine Regel wie beim Standardprofil. ![Regel für Zeiten nach Geschäftsschluss hinzufügen](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. Stellen Sie sicher, dass Sie sowohl eine Regel für das horizontale Hochskalieren als auch eine Regel für das horizontale Herunterskalieren definieren, da die Instanzenzahl für das Profil ansonsten nur erhöht (oder verringert) wird.

12. Klicken Sie abschließend auf **Speichern**.

## Nächste Schritte

* [Überwachen von Dienstmetriken](insights-how-to-customize-monitoring.md), um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
* [Aktivieren von Überwachung und Diagnose](insights-how-to-use-diagnostics.md), um detaillierte Hochfrequenzmetriken zu Ihrem Dienst zu sammeln.
* [Empfangen von Warnbenachrichtigungen](insights-receive-alert-notifications.md), wenn ein Vorgangsereignis auftritt oder Metriken einen Schwellenwert überschreiten.
* [Überwachen der Anwendungsleistung](insights-perf-analytics.md), um präzise Informationen zur Leistung Ihres Codes in der Cloud zu ermitteln.
* [Anzeigen von Ereignissen und Überwachungsprotokollen](insights-debugging-with-events.md), um sich über sämtliche Aktivitäten Ihres Diensts zu informieren.
* [Überwachen der Verfügbarkeit und Reaktionsfähigkeit einer beliebigen Webseite](../app-insights-monitor-web-app-availability.md) mit Application Insights, um zu ermitteln, ob eine Seite offline ist.
 

<!---HONumber=August15_HO6-->