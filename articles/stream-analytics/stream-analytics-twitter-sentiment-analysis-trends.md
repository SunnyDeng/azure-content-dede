<properties
	pageTitle="Twitter-Stimmungsanalyse in Echtzeit mit Stream Analytics | Microsoft Azure"
	description="Erfahren Sie, wie Sie Stream Analytics für Twitter-Stimmungsanalysen in Echtzeit verwenden. Schrittweise Anleitung von der Ereignisgenerierung bis hin zu Daten im Live-Dashboard."
	keywords="Echtzeit-Twitter,Stimmungsanalyse,Analyse von sozialen Medien,Analyse-Tools für soziale Medien"
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
	ms.workload="big-data"
	ms.date="11/06/2015"
	ms.author="jeffstok"/>


# Soziale Medienanalyse: Twitter-Stimmungsanalyse in Echtzeit in Azure Stream Analytics

In diesem Lernprogramm erfahren Sie, wie Sie eine Lösung für Stimmungsanalysen erstellen, indem Sie Twitter-Ereignisse in Echtzeit in Event Hubs einfügen, Stream Analytics-Abfragen zur Analyse der Daten schreiben und die Ergebnisse anschließend speichern oder ein Dashboard verwenden, um Echtzeitinformationen darzustellen.

Analysetools für soziale Medien können Unternehmen helfen, Thementrends zu verstehen, d. h. Themen und Einstellungen mit einer hohen Anzahl von Beiträgen in sozialen Medien. Für die Stimmungsanalyse – auch als Opinion Mining bezeichnet – werden Analysetools für soziale Medien verwendet, um Einstellungen zu einem Produkt, Meinungen usw. zu bestimmen.

## Szenario

Eine Nachrichtenwebsite ist daran interessiert, der Konkurrenz durch Seiteninhalte mit direkter Relevanz für die Leser voraus zu sein. Sie verwendet soziale Medienanalyse für Themen mit Relevanz für ihre Leser und führt dazu Stimmungsanalysen von Twitter-Daten in Echtzeit durch. Insbesondere werden Echtzeitanalysen des Tweet-Umfangs und der Stimmung im Hinblick auf wichtige Themen benötigt, um zu erkennen, welche Themen sich auf Twitter in Echtzeit zu Trendthemen entwickeln.

## Voraussetzungen
1.	Ein Twitter-Konto ist für dieses Lernprogramm erforderlich.  
2.	In diesem Lernprogramm wird eine Twitter-Clientanwendung verwendet, die sich auf GitHub befindet. Laden Sie ihn [hier](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClient) herunter und befolgen Sie die folgenden Schritte, um Ihre Lösung einzurichten.

## Erstellen einer Event Hub-Eingabe und einer Verbrauchergruppe

Die Beispielanwendung generiert Ereignisse und überträgt sie mithilfe von Push an eine Event Hubs-Instanz (kurz ein Event Hub). Service Bus Event Hubs sind die bevorzugte Methode der Ereigniserfassung für Stream Analytics. Weitere Informationen finden Sie unter „Event Hubs“ in der [Service Bus-Dokumentation](/documentation/services/service-bus/).

Gehen Sie folgendermaßen vor, um einen Event Hub zu erstellen.

1.	Klicken Sie im Azure-Portal auf **NEU** > **APP SERVICES** > **SERVICE BUS** > **EVENT HUB** > **SCHNELLERFASSUNG**, und geben Sie einen Namen, eine Region und einen neuen oder vorhandenen Namespace an, um einen neuen Event Hub zu erstellen.  
2.	Als bewährte Methode sollte jeder Stream Analytics-Auftrag aus einer einzelnen Event Hubs-Verbrauchergruppe lesen. Im Folgenden werden Sie durch den Prozess der Erstellung einer Verbrauchergruppe geführt und erfahren mehr über diese. Um eine Verbrauchergruppe zu erstellen, wechseln Sie zu dem neu erstellten Event Hub, klicken Sie auf die Registerkarte **VERBRAUCHERGRUPPEN** und anschließend auf **ERSTELLEN** am unteren Rand der Seite. Geben Sie einen Namen für die Verbrauchergruppe ein.
3.	Um Zugriff auf den Event Hub zu gewähren, müssen wir eine Richtlinie für gemeinsamen Zugriff erstellen. Klicken Sie auf die Registerkarte **KONFIGURIEREN** des Event Hubs.
4.	Erstellen Sie unter **RICHTLINIEN FÜR GEMEINSAMEN ZUGRIFF** eine neue Richtlinie mit Berechtigungen zum **VERWALTEN**.


  	![Unter "Gemeinsame Zugriffsrichtlinien" können Sie eine neue Richtlinie mit Verwaltungsberechtigungen erstellen.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.	Klicken Sie unten auf der Seite auf **Speichern**.
6.	Gehen Sie zum **DASHBOARD**, klicken Sie dann am unteren Rand auf **VERBINDUNGSINFORMATIONEN**, und kopieren und speichern Sie die Verbindungszeichenfolge. (Verwenden Sie das Symbol „Kopieren“, das unter dem Suchsymbol angezeigt wird.)

## Konfigurieren und Starten der Twitter-Clientanwendung

Wir haben eine Clientanwendung bereitgestellt, die über die [Streaming-APIs von Twitter](https://dev.twitter.com/streaming/overview) auf Twitter-Daten zugreift, um Tweet-Ereignisse zu einer parametrisierten Reihe von Themen zu sammeln. Das Open-Source-Tool [Sentiment140](http://help.sentiment140.com/) eines Drittanbieters wird verwendet, um jedem Tweet einen Stimmungswert zuzuweisen (0: negativ, 2: neutral, 4: positiv). Danach werden die Tweet-Ereignisse mithilfe von Push an den Event Hub übertragen.

Gehen Sie folgendermaßen vor, um die Anwendung einzurichten:

1.	[Laden Sie die TwitterClient-Lösung herunter.](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClient)
2.	Öffnen Sie App.config und ersetzen Sie oauth\_consumer\_key, oauth\_consumer\_secret, oauth\_token und oauth\_token\_secret mit den Twitter-Token mit Ihren Werten.  

	[Schritte zum Generieren eines OAuth-Zugriffstokens](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)

	Beachten Sie, dass Sie zum Generieren eines Tokens eine leere Anwendung erstellen müssen.  
3.	Ersetzen Sie die Werte EventHubConnectionString und EventHubName in App.config mit der Verbindungszeichenfolge und dem Namen Ihres Event Hubs.
4.	*Optional:* Passen Sie die Schlüsselwörter für die Suche an. Standardmäßig sucht diese Anwendung nach „Azure,Skype,XBox,Microsoft,Seattle“. Falls gewünscht, können Sie die Werte für twitter\_keywords in App.config anpassen.
5.	Erstellen Sie die Lösung.
6.	Starten Sie die Anwendung. Sie sehen nun, wie Ereignisse mit den Werten CreatedAt, Topic und SentimentScore an Ihren Event Hub gesendet werden:

	![Stimmungsanalyse: SentimentScore-Werte, die an einen Event Hub gesendet werden.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## Erstellen eines Stream Analytics-Auftrags

Nun, da wir einen Datenstrom von Tweet-Ereignissen haben, können wir einen Stream Analytics-Auftrag einrichten, um diese Ereignisse in Echtzeit zu analysieren.

### Bereitstellen eines Stream Analytics-Auftrags

1.	Klicken Sie im [Azure-Portal](https://manage.windowsazure.com/) auf **NEU** > **DATA SERVICES** > **STREAM ANALYTICS** > **SCHNELLERFASSUNG**.
2.	Geben Sie die folgenden Werte an, und klicken Sie dann auf **STREAM ANALYTICS-AUFTRAG ERSTELLEN**:

	* **AUFTRAGSNAME**: Geben Sie einen Auftragsnamen ein.
	* **REGION**: Wählen Sie die Region aus, in der der Auftrag ausgeführt werden soll. Ziehen Sie es in Betracht, den Auftrag und das Event Hub in derselben Region zu platzieren, um für eine bessere Leistung zu sorgen und sicherzustellen, dass Sie nicht für die Übertragung von Daten zwischen Regionen bezahlen.
	* **SPEICHERKONTO** – Wählen Sie das Speicherkonto, das Sie zum Speichern von Überwachungsdaten für alle Stream Analytics-Aufträge verwenden möchten, die innerhalb dieser Region ausgeführt werden. Sie haben die Möglichkeit, ein vorhandenes Speicherkonto auszuwählen oder ein neues zu erstellen.

3.	Klicken Sie im linken Bereich auf **STREAM ANALYTICS**, um die Stream Analytics-Aufträge aufzulisten.![Symbol des Stream Analytics-Diensts](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

4.	Der neue Auftrag wird mit dem Status **ERSTELLT** aufgeführt. Beachten Sie, dass die Schaltfläche **START** am unteren Seitenrand deaktiviert ist. Sie müssen die Auftragseingabe, -ausgabe und -abfrage konfigurieren, bevor Sie den Auftrag starten können.


### Festlegen der Auftragseingabe
1.	Klicken Sie in Ihrem Stream Analytics-Auftrag auf **EINGABEN** am oberen Rand der Seite und klicken Sie dann auf **HINZUFÜGEN**. Das aufgerufene Dialogfeld führt Sie durch eine Reihe von Schritten, um Ihre Eingabe einzurichten.
2.	Wählen Sie **DATENSTROM**, und klicken Sie dann auf die rechte Taste.
3.	Wählen Sie **EVENT HUB**, und klicken Sie dann auf die rechte Taste.
4.	Geben Sie die folgenden Werte auf der dritten Seite ein, oder wählen Sie sie aus:

	* **INPUT ALIAS**: Geben Sie einen Anzeigenamen für diese Auftragseingabe ein, wie z. B. TwitterStream. Beachten Sie, dass Sie diesen Namen später in der Abfrage verwenden werden. **EVENT HUB**: Wenn der Event Hub, den Sie erstellt haben, sich in demselben Abonnement wie der Stream Analytics-Auftrag befindet, wählen Sie den Namespace aus, in dem sich der Event Hub befindet.

		Wenn sich Ihr Event Hub in einem anderen Abonnement befindet, wählen Sie **Event Hub aus einem anderen Abonnement verwenden** aus, und geben Sie manuell folgende Informationen ein: **SERVICE BUS-NAMESPACE**, **EVENT HUB-NAME**, **NAME DER EVENT HUB-RICHTLINIE**, **SCHLÜSSEL DER EVENT HUB-RICHTLINIE** und **EVENT HUB-PARTITIONSANZAHL**.

	* **EVENT HUB-NAME**: Wählen Sie den Namen des Event Hubs aus.
	* **NAME DER EVENT HUB-RICHTLINIE**: Wählen Sie die Event Hub-Richtlinie aus, die Sie zuvor in diesem Lernprogramm erstellt haben.
	* **EVENT HUB-VERBRAUCHERGRUPPE**: in der Consumer-Gruppe, die zuvor in diesem Lernprogramm erstellt haben.
5.	Klicken Sie auf die rechte Taste.
6.	Geben Sie die folgenden Werte an:

	* **EVENT SERIALIZER FORMAT**: JSON
	* **CODIERUNG**: UTF8

7.	Klicken Sie auf das Häkchen, um diese Quelle hinzuzufügen und um zu überprüfen, ob Stream Analytics erfolgreich mit dem Event Hub verbunden werden kann.

### Festlegen der Auftragsabfrage

Stream Analytics unterstützt ein einfaches, deklaratives Abfragemodell zum Beschreiben der Transformationen. Weitere Informationen zur Sprache finden Sie in der [Azure Stream Analytics-Abfragesprachreferenz](https://msdn.microsoft.com/library/azure/dn834998.aspx). Dieses Lernprogramm hilft Ihnen beim Erstellen und Testen mehrerer Abfragen über Twitter-Daten.

#### Beispiel-Dateneingabe

Um die Abfrage mit den tatsächlichen Auftragsdaten zu überprüfen, können Sie die BEISPIELDATEN-Funktion verwenden, um Ereignisse aus Ihrem Datenstrom zu extrahieren und eine JSON-Datei der Ereignisse zum Testen zu erstellen.

1.	Wählen Sie „Eingaben“ in Ihrem Event Hub, und klicken Sie auf **BEISPIELDATEN** am unteren Rand der Seite.
2.	Geben Sie im daraufhin angezeigten Dialogfeld eine **STARTZEIT** für das Sammeln der Daten und die **DAUER** ein, während der zusätzliche Daten verbraucht werden sollen.
3.	Klicken Sie auf die Schaltfläche **DETAILS**, und dann auf den Link **Klicken Sie hier**, um die generierte JSON-Datei herunterzuladen und zu speichern.

#### Pass-Through-Abfrage
Wir beginnen mit einer einfachen Pass-Through-Abfrage, die alle Felder in einem Ereignis projiziert.

1.	Klicken Sie auf **ABFRAGE** oben auf der Seite des Stream Analytics-Auftrags.
2.	Ersetzen Sie im Code-Editor die ursprüngliche Abfragevorlage mit Folgendem:

		SELECT * FROM TwitterStream

	Stellen Sie sicher, dass der Name der Eingabequelle dem Namen der Eingabe entspricht, die Sie zuvor angegeben haben.

3.	Klicken Sie im Abfrage-Editor auf **TEST**.
4.	Navigieren Sie zu Ihrer JSON-Beispieldatei.
5.	Klicken Sie auf die Schaltfläche „Prüfen“. Die Ergebnisse werden unterhalb der Abfragedefinition angezeigt.

	![Unterhalb der Abfragedefinition angezeigte Ergebnisse](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### Anzahl der Tweets nach Thema: Rollierendes Fenster mit Aggregation

Um die Anzahl der Erwähnungen verschiedener Themen zu vergleichen, nutzen wir ein [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx), um alle fünf Sekunden die Anzahl der Erwähnungen abzurufen.

1.	Ändern Sie die Abfrage im Code-Editor in:

		SELECT System.Timestamp as Time, Topic, COUNT(*)
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY TUMBLINGWINDOW(s, 5), Topic

	Beachten Sie, dass diese Abfrage das Schlüsselwort **TIMESTAMP BY** verwendet, um ein Zeitstempel-Feld in der Nutzlast anzugeben, das für die zeitliche Berechnung verwendet wird. Würde dieses Feld nicht angegeben, dann würde der Vorgang die Zeit verwenden, zu der jedes Ereignis beim Event Hub eingeht. Weitere Informationen erhalten Sie unter „Arrival Time Vs Application Time“ in der [Stream Analytics-Abfragereferenz](https://msdn.microsoft.com/library/azure/dn834998.aspx).

	Diese Abfrage greift auch auf einen Zeitstempel für das Ende jedes Fenster mit **System.Timestamp** zu.

2.	Klicken Sie im Abfrage-Editor auf **ERNEUT AUSFÜHREN**, um die Ergebnisse der Abfrage anzuzeigen.

#### Identifizieren von Trendthemen: Schiebefenster

Zum Identifizieren von Trendthemen suchen wir nach Themen, die einen Schwellenwert für Erwähnungen in einem bestimmten Zeitraum überschreiten. Für die Zwecke dieses Lernprogramms suchen wir mithilfe eines [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx) nach Themen, die in den letzten 5 Sekunden mehr als 20-mal erwähnt werden.

1.	Ändern Sie die Abfrage im Code-Editor in:

		SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY SLIDINGWINDOW(s, 5), topic
		HAVING COUNT(*) > 20

2.	Klicken Sie im Abfrage-Editor auf **ERNEUT AUSFÜHREN**, um die Ergebnisse der Abfrage anzuzeigen.

	![Gleitendes Fenster – Abfrageausgabe](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### Anzahl der Erwähnungen und Stimmung: Rollierendes Fenster mit Aggregation

Die letzte Abfrage, die wir testen, verwendet ein TumblingWindow, um alle 5 Sekunden für jedes Thema die Anzahl der Erwähnungen sowie Durchschnitt, Minimum, Maximum und die Standardabweichung von der Bewertung der Stimmung abzurufen.

1.	Ändern Sie die Abfrage im Code-Editor in:

		SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
    	Max(SentimentScore), STDEV(SentimentScore)
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.	Klicken Sie im Abfrage-Editor auf **ERNEUT AUSFÜHREN**, um die Ergebnisse der Abfrage anzuzeigen.
3.	Dies ist die Abfrage, die wir für unser Dashboard verwenden. Klicken Sie unten auf der Seite auf **Speichern**.


## Erstellen der Ausgabetabelle

Nun, da wir einen Datenstrom, eine Event Hub-Eingabe zum Erfassen von Ereignissen und eine Abfrage zur Ausführung einer Umwandlung über den Stream definiert haben, ist der letzte Schritt die Definition einer Ausgabetabelle für den Auftrag. Die aggregierten Tweet-Ereignisse aus unserer Auftragsabfrage schreiben wir in ein Azure-Blob. Sie können die Ergebnisse je nach den spezifischen Anforderungen Ihrer Anwendung auch mithilfe von Push in eine SQL-Datenbank, einen Tabellenspeicher oder einen Event Hub übertragen.

Befolgen Sie die nachstehenden Schritte zum Erstellen eines Containers für Blob-Speicher, wenn Sie noch keinen erstellt haben:

1.	Verwenden Sie ein vorhandenes Speicherkonto oder erstellen Sie ein neues, indem Sie auf **NEU** > **DATA SERVICES** > **SPEICHER** > **SCHNELLERFASSUNG** klicken und die Anweisungen auf dem Bildschirm befolgen.
2.	Wählen Sie das Speicherkonto, klicken Sie anschließend auf **CONTAINER** am oberen Rand der Seite, und klicken Sie dann auf **HINZUFÜGEN**.
3.	Geben Sie einen **NAMEN** für den Container an legen Sie seinen **ZUGRIFF** auf „Öffentlicher Blob“ fest.

## Festlegen der Auftragsausgabe

1.	Klicken Sie in Ihrem Stream Analytics-Auftrag am oberen Rand der Seite auf **AUSGABE**, und klicken Sie dann auf **AUSGABE HINZUFÜGEN**. Das aufgerufene Dialogfeld führt Sie durch eine Reihe von Schritten, um Ihre Eingabe einzurichten.
2.	Wählen Sie **BLOB-SPEICHER**, und klicken Sie dann mit der rechten Maustaste.
3.	Geben Sie die folgenden Werte auf der dritten Seite ein, oder wählen Sie sie aus:

	* **AUSGABEALIAS**: Geben Sie einen Anzeigenamen für diese Auftragsausgabe ein.
	* **ABONNEMENT**: Befindet sich der Blob-Speicher, den Sie erstellt haben, in demselben Abonnement wie der Stream Analytics-Auftrag, wählen Sie **Storage-Konto aus dem aktuellen Abonnement verwenden**. Wenn sich der Speicher in einem anderen Abonnement befindet, wählen Sie **Storage-Konto von einem anderen Abonnement verwenden** und geben Sie manuell **SPEICHERKONTO**, **SPEICHERKONTOSCHLÜSSEL** und **CONTAINER** ein.
	* **SPEICHERKONTO**: Wählen Sie den Namen des Speicherkontos.
	* **CONTAINER**: Wählen Sie den Namen des Containers.
	* **DATEINAMENPRÄFIX**: Geben Sie ein Präfix ein, das beim Schreiben von Blob-Ausgaben verwendet wird.

4.	Klicken Sie auf die rechte Taste.
5.	Geben Sie die folgenden Werte an:
	* **EVENT SERIALIZER FORMAT**: JSON
	* **CODIERUNG**: UTF8
6.	Klicken Sie auf das Häkchen, um diese Quelle hinzuzufügen und zu überprüfen, ob Stream Analytics erfolgreich mit dem Speicherkonto verbunden werden kann.

## Auftrag starten

Nachdem wir Eingabe, Abfrage und Ausgabe für den Auftrag angegeben haben, können wir den Stream Analytics-Auftrag starten.

1.	Klicken Sie im **DASHBOARD** des Auftrags auf **STARTEN** am unteren Rand der Seite.
2.	Wählen Sie im daraufhin angezeigten Dialogfeld **AUFTRAGS-STARTZEIT**, und klicken Sie dann auf das Häkchen am unteren Rand des Dialogfelds. Der Status ändert sich in **Starten** und wechselt nach kurzer Zeit zu **Ausführen**.


## Anzeigen der Ausgabe für die Stimmungsanalyse

Sobald Ihr Auftrag ausgeführt wird und den Twitter-Datenstrom in Echtzeit verarbeitet, wählen Sie aus, wie die Ausgabe für die Stimmungsanalyse angezeigt werden soll. Verwenden Sie ein Tool wie [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) oder [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction), um die Auftragsausgabe in Echtzeit anzuzeigen. Von hier aus können Sie Ihre Anwendung erweitern, sodass diese über der Ausgabe ein an Ihre Bedürfnisse angepasstes Dashboard enthält, wie in der Abbildung unten gezeigt ([Power BI](https://powerbi.com/)).

![Soziale Medienanalyse: Ausgabe der Stimmungsanalyse (Opinion Mining) mit Stream Analytics in einem Power BI-Dashboard.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## Support
Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).


## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=Nov15_HO3-->