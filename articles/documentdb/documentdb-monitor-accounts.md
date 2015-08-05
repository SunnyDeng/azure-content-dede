<properties 
	pageTitle="Überwachen eines DocumentDB-Kontos | Azure" 
	description="Erfahren Sie, wie Sie Ihr DocumentDB-Konto anhand von Leistungsmetriken (z. B. Anforderungen und Serverfehler) und Nutzungsmetriken (z. B. Speicherverbrauch) überwachen." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="mimig"/>

#Überwachen eines DocumentDB-Kontos  

Sie können Ihre DocumentDB-Konten im [Microsoft Azure-Vorschauportal](https://portal.azure.com/) überwachen. Es sind für jedes DocumentDB-Konto sowohl Leistungsmetriken (wie Anfragen und Serverfehler) als auch Nutzungsmetriken (wie verbrauchter Speicher) verfügbar.

## <a id="metrics"></a> Anzeigen von Leistungsmetriken für ein DocumentDB-Konto
1.	Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Durchsuchen**, dann auf **DocumentDB-Konten** und anschließend auf den Namen des DocumentDB-Kontos, dessen Leistungsmetriken Sie anzeigen möchten.
2.	Innerhalb des Bereichs **Überwachung** sehen Sie standardmäßig Folgendes:
	*	Gesamtanforderungen des aktuellen Tags.
	*	Durchschnittliche Anfragen pro Sekunde des aktuellen Tags. 
	
	![Screenshot des Fokus "Überwachung"](./media/documentdb-monitor-accounts/madocdb1.png)


3.	Wenn Sie auf das Detail **Anforderungen insgesamt** oder **Durchschnittliche Anforderungen pro Sekunde** klicken, wird ein detailliertes Blatt **Metrik** geöffnet.
4.	Das Blatt **Metrik** zeigt Details zu den ausgewählten Metriken. Im oberen Teil des Fensters befindet sich ein Diagramm und darunter eine Tabelle mit einer Aggregation der Werte der ausgewählten Metriken, also beispielsweise Durchschnitt, Minimum und Maximum. Das Fenster „Metrik“ zeigt auch eine Liste von Warnung, die definiert wurden. Sie ist gefiltert nach den im aktuellen Fenster „Metrik“ anzeigten Metriken (wenn Sie eine Reihe von Warnungen haben, dann werden auf diese Weise hier nur die relevanten gezeigt).   

	![Screenshot des Blatts "Metriken"](./media/documentdb-monitor-accounts/madocdb2.png)


## <a id="custom"></a>Anpassen der Leistungsmetrikansichten für ein DocumentDB-Konto

1.	Klicken Sie zum Anpassen der in einem bestimmten Detail angezeigten Metriken mit der rechten Maustaste auf das Metrikdiagramm, und wählen Sie **Diagramm bearbeiten**. ![Screenshot des Diagramms "Anforderungen insgesamt" mit hervorgehobener Schaltfläche "Diagramm bearbeiten"](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Das Blatt **Diagramm bearbeiten** bietet Optionen zum Ändern der in diesem Detail angezeigten Metriken sowie deren Zeitraum. ![Screenshot des Blatts "Diagramm bearbeiten"](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Sie können die im Detail angezeigten Metriken verändern, indem Sie einfach die verfügbaren Leistungsmetriken auswählen oder löschen und dann unten auf dem Blatt auf **Speichern** klicken.
4.	Um den Zeitraum zu ändern, wählen Sie einen anderen Zeitraum (z. B. **Letzte Stunde**) und klicken unten auf dem Blatt auf **Speichern**.  

	![Screenshot des Details "Zeitraum" auf dem Blatt "Diagramm bearbeiten" mit Eingabe eines benutzerdefinierten Zeitraums](./media/documentdb-monitor-accounts/madocdb5.png)


## <a id="create"></a>Erstellen von nebeneinander liegenden Diagrammen mit Leistungsmetriken
Im Azure-Vorschauportal können Sie nebeneinander liegende Metrikdiagramme erstellen.

1.	Klicken Sie zuerst mit der rechten Maustaste auf das Diagramm, das Sie klonen und ändern möchten, und wählen Sie **Anpassen**. 

	![Screenshot des Diagramms "Anforderungen insgesamt" mit hervorgehobener Option "Anpassen"](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Klicken Sie im Menü auf **Klonen**, um das Detail zu kopieren, und klicken Sie dann auf **Anpassung fertig**.

	![Screenshot des Diagramms "Anforderungen insgesamt" mit hervorgehobenen Optionen "Klonen" und "Anpassung fertig"](./media/documentdb-monitor-accounts/madocdb7.png)


Sie können diesen Bereich jetzt wie jeden anderen Metrikbereich behandeln und ihn sowie die dort angezeigte Zeitspanne anpassen. Auf diese Weise können Sie zwei verschiedene Metrikdiagramme gleichzeitig nebeneinander anzeigen. ![Screenshot des Diagramms "Anforderungen insgesamt" und des Diagramms "Anforderungen insgesamt – letzte Stunde"](./media/documentdb-monitor-accounts/madocdb8.png)

## <a id="view"></a>Anzeigen von Nutzungsmetriken für ein DocumentDB-Konto
1.	Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Durchsuchen**, dann auf **DocumentDB-Konten** und anschließend auf den Namen des DocumentDB-Kontos, dessen Nutzungsmetriken Sie anzeigen möchten.
2.	Innerhalb des Fokus **Nutzung** können Sie standardmäßig Folgendes anzeigen:
	*	Geschätzte Kosten
	*	Mit dem Konto verbrauchter Speicher
	*	Maximal verfügbarer Speicher des Kontos
	*	Nutzung von Benutzern und Berechtigungen
	*	Nutzung von Anlagen

	![Screenshot des Fokus "Nutzung"](./media/documentdb-monitor-accounts/madocdb9.png)
 
## <a id="setup"></a>Einrichten von Warnungen bei Leistungsmetriken für ein DocumentDB-Konto
1.	Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Durchsuchen**, dann auf **DocumentDB-Konten** und anschließend auf den Namen des DocumentDB-Kontos, für das Sie Warnungen für Leistungsmetriken einrichten möchten.
2.	Klicken Sie im Fokus **Vorgänge** auf das Detail **Warnungsregeln**.![Screenshot des Fokus "Vorgänge" mit ausgewähltem Detail "Warnungsregeln"](./media/documentdb-monitor-accounts/madocdb10.png)

3.	Klicken Sie auf dem Blatt "Warnungsregeln" auf **Warnung hinzufügen**.![Screenshot des Blatts "Warnungsregeln" mit hervorgehobener Schaltfläche "Warnung hinzufügen"](./media/documentdb-monitor-accounts/madocdb11.png)

4.	Legen Sie im Fenster **Warnungsregel hinzufügen** Folgendes fest:
	*	Den Namen der Warnungsregel, die Sie einrichten möchten.
	*	Eine Beschreibung der neuen Warnungsregel.
	*	Die Metrik für die Warnungsregel.
	*	Bedingung, Schwellenwert und Zeitraum die festlegen, wann die Warnung aktiviert wird. Zum Beispiel mehr als 5 Serverfehler im Verlauf der letzten 15 Minuten.
	*	Ob der Dienstadministrator oder Coadministratoren bei Auslösung der Warnung eine E-Mail erhalten.
	*	Weitere E-Mail-Adressen für Warnbenachrichtigungen. ![Screenshot des Blatts "Warnungsregel hinzufügen"](./media/documentdb-monitor-accounts/madocdb12.png)

 
## <a id="next"></a>Nächste Schritte
Weitere Informationen zur Kapazität von DocumentDB finden Sie unter [Verwalten der Kapazität von DocumentDB](documentdb-manage.md).

<!--Anchors-->
[How to view performance metrics for a DocumentDB account]: #How-to-view-performance-metrics-for-a-DocumentDB-account
[Customize performance metric views for a DocumentDB account]: #Customize-performance-metric-views-for-a-DocumentDB-account
[How to create side-by-side performance metric charts]: #How-to-create-side-by-side-performance-metric-charts
[How to view usage metrics for a DocumentDB account]: #How-to-view-usage-metrics-for-a-DocumentDB-account
[How to setup performance metric alerts for a DocumentDB account]: #How-to-setup-performance-metric-alerts-for-a-DocumentDB-account
[Next steps]: #Next-steps
 

<!---HONumber=July15_HO4-->