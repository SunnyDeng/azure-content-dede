<properties 
	pageTitle="Überwachen eines Docker-Hosts in Application Insights" 
	description="In Application Insights können Leistungsindikatoren, Ereignisse und Ausnahmen von Docker-Hosts gemeinsam mit der Telemetrie von in Containern ausgeführten Apps angezeigt werden." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="awills"/>
 
# Überwachen eines Docker-Hosts in Application Insights

Leistungsindikatoren und Ausnahmeberichte von einem [Docker](https://www.docker.com/)-Host können in Application Insights in Diagrammen dargestellt werden. Installieren Sie die [Application Insights](app-insights-overview.md)-App in einem Container auf Ihrem Host, und sie zeigt allgemeine Leistungsindikatoren für den Host sowie für die anderen Images an.

Mit Docker verteilen Sie Ihre Apps zusammen mit allen Abhängigkeiten auf schlanke Container. Diese lassen sich auf allen Hostcomputern ausführen, auf denen ein Docker-Modul ausgeführt wird.

Application Insights kann die Leistung und die Aktivität des Docker-Hosts und der Docker-Container überwachen.

![Beispiel](./media/app-insights-docker/00.png)

(Und selbstverständlich: Wenn Sie den Quellcode der in den Containern ausgeführten Apps besitzen, sollten Sie [das Application Insights SDK in jeder dieser Apps installieren](app-insights-java-live), um detaillierte Leistungsprotokolle und eine Nutzungsverfolgung zu erhalten.)

## Erstellen einer Application Insights-Ressource

Eine Application Insights-Ressource ist der Ort, an dem Sie Ihre Daten anzeigen und analysieren. Diese wird im Microsoft Azure-Portal gehostet.

1.	Sie benötigen ein [Microsoft Azure](https://azure.com)-Abonnement. (Es gibt eine nutzungsbasierte Option, bei der Sie nichts bezahlen, wenn Sie keine Dienste verwenden, und Sie können den kostenlosen Free-Tarif von Application Insights nutzen.)
2.	Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und erstellen Sie eine neue Application Insights-Ressource. Wählen Sie als Anwendungstyp „Sonstiges“ aus. (Diese Auswahl wirkt sich nur auf die anfangs angezeigten Diagramme aus und ist nicht entscheidend.)

    ![Beispiel](./media/app-insights-docker/01-new.png)
3.	Sie können jederzeit zu Ihrer neuen Überwachungsressource gelangen, indem Sie die Suchfunktion mit der Option für „Alles“ verwenden. Aber wahrscheinlich haben Sie ohnehin auf dem Startseitendashboard eine Kachel für Ihre App platziert, die bei jedem Aufrufen des Portals angezeigt wird.

    Öffnen Sie nun die neue Ressource.
4.	Fügen Sie die Docker-Kachel hinzu: Wählen Sie **Bearbeiten**, dann **Kacheln hinzufügen**, und ziehen Sie die Docker-Kachel aus dem Katalog. Zu Beginn wird nur ein leeres Diagramm angezeigt.

    ![Beispiel](./media/app-insights-docker/03.png)

5. Öffnen Sie die Dropdownliste **Zusammenfassung**, und kopieren Sie den Instrumentierungsschlüssel. Sie benötigen ihn, um dem SDK mitzuteilen, wohin die Telemetrie gesendet werden soll.

Lassen Sie dieses Browserfenster geöffnet, Sie werden es gleich wieder brauchen, um Ihre Telemetrie anzuzeigen.


## Installieren von Application Insights auf dem Host
 
Nachdem nun einen Ort zum Anzeigen der Telemetrie vorhanden ist, können Sie die App einrichten, die die entsprechenden Daten sammelt und sendet. 1. Melden Sie sich bei Ihrem Docker-Host an. 2. Suchen Sie auf dem Docker-Hub nach dem Microsoft Application Insights-Image und ziehen Sie es zu Ihrem Host. 3. Legen Sie den Instrumentierungsschlüssel fest:

    docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444

4. Führen Sie das Image aus.

## Anzeigen der Telemetrie

Kehren Sie zu Ihrer Application Insights-Ressource im Azure-Portal zurück.

Dort werden Sie gleich die vom Docker-Container eingehenden Daten sehen, insbesondere wenn andere Container in Ihrem Docker-Modul ausgeführt werden.

Klicken Sie sich durch die Diagramme, um weitere Details anzuzeigen.

Hier sind einige Ansichten, die angezeigt werden können:

### Leistungsindikatoren nach Host, Aktivität nach Image


![Beispiel](./media/app-insights-docker/10.png)

Klicken Sie auf einen Imagenamen, um weitere Details anzuzeigen. Zum Anpassen der Ansicht klicken Sie auf ein Diagramm oder auf eine Tabellenüberschrift, oder verwenden Sie „Diagramm hinzufügen“.

[Erfahren Sie mehr über Metrik-Explorer](app-insights-metrics-explorer.md).

## Einzelne Ereignisse


![Beispiel](./media/app-insights-docker/12.png)

Um einzelne Ereignisse zu untersuchen, klicken Sie auf [Suche](app-insights-diagnostic-search.md). Suchen Sie die gewünschten Ereignisse, und filtern Sie sie bei Bedarf. Klicken Sie auf ein beliebiges Ereignis, um weitere Details anzuzeigen.
 
## Ausnahmen nach Containername
 

![Beispiel](./media/app-insights-docker/14.png)



## Fragen und Antworten

*Was bietet mir Application Insights, das Docker nicht leisten kann?*

* Eine detaillierte Aufschlüsselung der Leistungsindikatoren nach Container und Image.
* Eine Zusammenführung von Container- und App-Daten in einem Dashboard.
* Einen [Export der Telemetrie](app-insights-export-telemetry.md) in eine Datenbank, nach Power-BI oder in anderes Dashboard, um sie eingehender zu analysieren.

*Wie erhalte ich Telemetrie aus der App selbst?*

* Installieren Sie das Application Insights SDK in der App. So gehen Sie vor: bei [Java-Web-Apps](app-insights-java-get-started.md) und [Windows-Web-Apps](app-insights-asp-net.md).

<!---HONumber=AcomDC_1125_2015-->