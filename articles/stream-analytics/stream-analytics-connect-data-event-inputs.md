<properties 
	pageTitle="Erstellen von Stream Analytics-Eingaben | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Verbindung mit den Eingabequellen für Stream Analytics-Lösungen herstellen, und wie Sie diese Quellen konfigurieren."
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
	ms.date="08/05/2015" 
	ms.author="jeffstok"/>

# Erstellen von Stream Analytics-Eingaben

## Grundlegendes zu Stream Analytics-Eingaben
---
Eine Stream Analytics Eingabe ist als eine Verbindung mit einer Datenquelle definiert. Werden Daten an diese Datenquelle gesendet, werden sie von dem Stream Analytics-Auftrag übernommen und in Echtzeit verarbeitet. Eingaben werden in zwei unterschiedliche Gruppen unterteilt: Datenstromeingaben und Verweisdateneingaben. Stream Analytics hat eine hervorragende Integration mit den Eingabequellen Event Hubs und BLOB-Speicher von innerhalb und außerhalb des Auftragsabonnements. Es werden die Datenformate Avro, CSV und JSON unterstützt.

## Datenstromeingaben
---
Im Grundsatz muss jede Stream Analytics-Auftragsdefinition mindestens eine Datenstrom-Eingabequelle enthalten, die vom Auftrag genutzt und umgewandelt werden soll. Azure-BLOB-Speicher und Azure Event Hubs werden als Datenstrom-Eingabequellen unterstützt. Azure Event Hubs-Eingabequellen werden verwendet, um Ereignisströme von mehreren Geräten und Diensten zu sammeln. Beispiele für Datenströme sind etwa Aktivitätsfeeds von sozialen Medien, Börseninformationen oder Daten von Sensoren.

Alternativ kann Azure-BLOB-Speicher als eine Eingabequelle für das Erfassen von Massendaten verwendet werden. Es ist wichtig zu beachten, dass sich die Daten bei Verwendung von Azure-BLOBs im Ruhezustand befinden, weshalb Stream Analytics sämtliche Daten, die in einem BLOB enthalten sind, so interpretiert, als hätten sie den Zeitstempel der Erstellung des BLOBs selbst. Das heißt, sofern die Datensätze im BLOB keine Zeitstempel enthalten, wird das Schlüsselwort TIMESTAMP BY verwendet.

## Verweisdateneingaben
---
Stream Analytics unterstützt einen zweiten Eingabequellentyp, der als Verweisdaten bezeichnet wird. Diese Daten sind zusätzliche Daten, die üblicherweise dazu verwendet werden, Korrelationen und Suchvorgänge auszuführen, und die Daten hier sind in der Regel statisch oder ändern sich selten. Azure-BLOB-Speicher ist derzeit die einzige unterstützte Eingabequelle für Verweisdaten. Blobs für Verweisdatenquellen sind auf eine Größe von 50 MB beschränkt.

Unterstützung für Aktualisieren von Verweisdaten kann aktiviert werden, indem in der Eingabekonfiguration ein Pfadmuster angegeben wird, für das die Token {date} und {time} verwendet werden. Der Auftrag lädt das entsprechende Blob anhand des im Blob-Namen codierten Datums und der Uhrzeit in der UTC-Zeitzone. Diese Abfolge von Verweisdaten-BLOBs mit codiertem Datum und codierter Uhrzeit ist erforderlich, um die Konsistenz der Ergebnisse zu gewährleisten. Gibt es beispielsweise eine Verzögerung in der Verarbeitung, und eine BLOB-Datei muss erneut geladen werden, wird erwartet, dass sich die Datei im selben Speicherort befindet, ohne geändert worden zu sein, denn andernfalls könnte eine andere Ausgabe angezeigt werden. Das einzige unterstützte Szenario besteht darin, neue BLOBs hinzuzufügen, für die im Pfadmuster ein zukünftiges Datum und eine zukünftige Uhrzeit codiert sind.

Wenn für den Auftrag im Portal z. B. eine Verweiseingabe mit dem Pfadmuster "/beispiel/{date}/{time}/produkte.csv" konfiguriert ist, wobei das Datumsformat gleich "JJJJ-MM-TT" und das Uhrzeitformat gleich "HH:mm" ist, erfasst der Auftrag am 16. April 2015 um 17:30 Uhr (UTC-Zeitzone) die Datei "/beispiel/2015-04-16/17:30/produkte.csv".


> [AZURE.NOTE]Derzeit suchen Stream Analytics-Aufträge nur nach aktualisierten Verweisdaten in einem BLOB, wenn die Uhrzeit mit der Uhrzeit zusammenfällt, die im BLOB-Namen codiert ist: Aufträge suchen z. B. am 16. April 2015 zwischen 17:30 Uhr und 17:30:59,999999999 Uhr (UTC-Zeitzone) nach "/beispiel/2015-04-16/17:30/produkte.csv". Wenn die Uhr auf 17:31 Uhr gesprungen ist, wird die Suche nach "/beispiel/2015-04-16/17:30/produkte.csv" beendet und die Suche nach "/beispiel/2015-04-16/17:31/produkte.csv" begonnen.

Der einzige Zeitpunkt, bei dem frühere Verweisdaten-BLOBs berücksichtigt werden, ist der Zeitpunkt, zu dem der Auftrag gestartet wird. Zu diesem Zeitpunkt sucht der Auftrag nach dem BLOB, in dessen Name der letzte Zeitpunkt (Datum und Uhrzeit) codiert ist, der vor dem Startzeitpunkt des Auftrags liegt (das neueste Verweisdaten-BLOB vor dem Startzeitpunkt des Auftrags). Dies erfolgt, damit sichergestellt ist, dass es beim Starten des Auftrags kein leeres Verweisdataset gibt. Wird kein BLOB gefunden, schlägt der Auftrag fehl, und es wird eine Diagnosemeldung angezeigt:

    “Initializing input without a valid reference data blob for UTC time <job start time>.”

 
## Erstellen einer Datenstromeingabe
---
Um eine Datenstromeingabe zu erstellen, navigieren Sie zur **Eingaben**-Registerkarte des Stream Analytics-Auftrags, und klicken Sie auf **Eingabe hinzufügen** am unteren Rand der Seite.

![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

 Wenn Sie eine Datenstromeingabe erstellen, werden zwei Optionen angezeigt: [**Event Hub**](Creating-an-Event-hub-input-data-stream) und [**BLOB-Speicher**](Creating-a Blob-storage-input-data-stream). Wählen Sie die entsprechende Option für den Typ des zu verarbeitenden Datenstroms aus.

![Bild2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

## Erstellen einer Event Hub-Datenstromeingabe
---
### Übersicht über Event Hubs
Event Hubs sind hochgradig skalierbare Ereigniserfasser, die am häufigsten für die Datenerfassung von Stream Analytics verwendet werden. Sie wurden für die Erfassung von Ereignisstreams von einer Reihe unterschiedlicher Geräte und Dienste entwickelt. Event Hubs und Stream Analytics bieten Kunden gemeinsam eine End-to-EndLösung für Echtzeitanalysen – mit Event Hubs können Kunden Ereignisse in Echtzeit in Azure einspeisen, die dann von Stream Analytics ebenfalls in Echtzeit verarbeitet werden können. Kunden können z. B. Webklicks, Sensorenmesswerte oder Onlineprotokollereignisse an Event Hubs senden und dann Stream Analytics-Aufträge erstellen, die diese Event Hubs als Eingabedatenströme für die Filterung, Aggregation und Zusammenführung in Echtzeit verwenden. Event Hubs können auch für den Datenausgang verwendet werden. Eine mögliche Verwendung von Event Hubs als Ausgabe ergibt sich, wenn die Ausgabe eines Stream Analytics-Auftrags die Eingabe eines anderen Streamingauftrags ist. Weitere Informationen zu Event Hubs finden Sie in der [Event Hubs](https://azure.microsoft.com/services/event-hubs/ "Event Hubs")-Dokumentation.

### Verbrauchergruppen
Jede Eingabe an einen Stream Analytics-Auftrag sollte für eine eigene Event Hub-Verbrauchergruppe konfiguriert werden. Wenn ein Auftrag Selbstverknüpfungen oder mehrere Eingaben enthält, werden einige Eingaben im weiteren Verlauf möglicherweise von mehreren Lesern gelesen, wodurch die Gesamtzahl der Leser in einer einzelnen Verbrauchergruppe die für den Event Hub geltende Beschränkung auf fünf Leser pro Verbrauchergruppe überschreiten kann. In diesem Fall muss die Abfrage in mehrere Abfragen und Zwischenergebnisse aufgeteilt werden, die dann über zusätzliche Event Hubs weitergeleitet werden. Beachten Sie, dass darüber hinaus ein Limit von 20 Verbrauchergruppen pro Event Hub gilt. Weitere Informationen finden Sie im [Programmierleitfaden für Event Hubs](https://msdn.microsoft.com/library/azure/dn789972.aspx "Programmierleitfaden für Ereignis-Hubs").

## Beispiel zum Erstellen einer Event Hub-Eingabe im Azure-Verwaltungsportal
---
Im Folgenden finden Sie eine schrittweise Anleitung zum Konfigurieren eines Event Hubs als Eingabe. Um eine Event Hub-Eingabe zu beginnen, sollte der Benutzer die folgende Informationen zu dem Event Hub gesammelt haben:

1. Eingabealias – der als Eingabealias angezeigte Name, auf den in der Auftragsabfrage verwiesen wird
2. Der Name des Service Bus-Namespace 
3. Der Name des Event Hubs
3. Der Name der Event Hub-Richtlinie
4. Optional: der Name der Event Hub-Consumergruppe
	- Die Verbrauchergruppe für das Erfassen von Daten aus den Event Hub. Wenn nichts angegeben wird, verwenden Stream Analytics-Aufträge die Standardverbrauchergruppe, um Daten vom Event Hub zu erfassen. Es wird empfohlen, für jeden Stream Analytics-Auftrag eine eigene Consumergruppe zu verwenden.
5. Das Serialisierungsformat, das für den Datenstrom verwendet wird (Avro, CSV, JSON)

Wählen Sie zunächst **EINGABE HINZUFÜGEN** auf der "Eingaben"-Seite des Stream Analytics-Auftrags aus.

![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

Wählen Sie dann "Event Hub" als Eingabe aus.

![Bild6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

Geben Sie als Nächstes die Informationen in die nachstehend gezeigten Felder für den Event Hub ein.

![Bild7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

Überprüfen Sie nun, ob das Ereignisserialisierungsformat für den Datenstrom korrekt ist.

![Bild8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

Wählen Sie dann **Abgeschlossen** aus. Daraufhin wird die Event Hub-Datenstromeingabe erstellt.

## Erstellen eines BLOB-Speichers als Datenstromeingabe
---
Für Szenarios mit großen Mengen unstrukturierter Daten, die in der Cloud gespeichert werden sollen, bietet BLOB-Speicher eine kostengünstige und skalierbare Lösung. Diese Daten werden grundsätzlich als "ruhende" Daten angesehen. Ein Szenario, in dem ein BLOB für eine Datenstromeingabe nützlich sein könnte, ist eine Protokollanalyse, wobei Protokolle bereits von Systemen erfasst sind und analysiert werden müssen sowie aussagekräftige Daten daraus extrahiert werden sollen. Ein weiteres Szenario könnten ruhende Data Warehouse-Daten sein. Weitere Informationen zu BLOB-Speicher finden Sie in der [BLOB-Speicher](http://azure.microsoft.com/services/storage/blobs/)-Dokumentation.

Im Folgenden finden Sie eine schrittweise Anleitung zum Konfigurieren eines BLOB-Speichers als Eingabe. Damit Sie eine Azure-BLOB-Speichereingabe verwenden können, sind folgende Informationen erforderlich:

1. Eingabealias – der als Eingabealias angezeigte Name, auf den in der Auftragsabfrage verwiesen wird
2. Befindet sich das Speicherkonto in einem anderen Abonnement als der Streamingauftrag, sind der "Speicherkontoname" und der "Speicherkontoschlüssel" erforderlich.
3. Der Containername
4. Das Präfix für Dateinamen
5. Das Serialisierungsformat, das für die Daten verwendet wird (Avro, CSV, JSON)

Klicken Sie auf der Registerkarte "Eingaben" des Stream Analytics-Auftrags auf **EINGABE HINZUFÜGEN**, und wählen Sie die Standardoption **Datenstrom** aus. ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

Wählen Sie als Nächstes **BLOB-Speicher** aus.

![Bild2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

Geben Sie nun die Informationen in die nachstehend gezeigten Felder für das Speicherkonto ein.

![Bild3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

> [AZURE.NOTE]Nach Klicken auf das Kontrollkästchen "Erweiterte Einstellungen konfigurieren" können Sie die Option "Präfixmuster des Pfads" angeben, damit BLOBs in einem benutzerdefinierten Pfad gelesen werden können. Ist diese Option nicht aktiviert, liest Stream Analytics alle BLOBs im Container.

![Bild4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

Wählen Sie als Nächstes die richtige Serialisierungseinstellung für die Daten. Die Optionen sind JSON, CSV und Avro.

![Bild5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

Wählen Sie dann **Abgeschlossen** aus. Daraufhin wird die BLOB-Speicher-Datenstromeingabe erstellt.

## Erstellen einer BLOB-Speicher-Verweisdateneingabe
---
Um Verweisdatenfunktionalität zu nutzen, kann BLOB-Speicher verwendet werden.

Im Folgenden finden Sie eine schrittweise Anleitung zum Konfigurieren von BLOB-Speicher als eine Verweisdateneingabe. Um starten zu können, sind die folgenden Informationen erforderlich:

1. Eingabealias – der als Eingabealias angezeigte Name, auf den in der Auftragsabfrage verwiesen wird
2. Befindet sich das Speicherkonto in einem anderen Abonnement als der Streamingauftrag, sind der "Speicherkontoname" und der "Speicherkontoschlüssel" erforderlich.
3. Der Containername
4. Das Präfix für Dateinamen
5. Das Serialisierungsformat, das für die Daten verwendet wird (CSV, JSON)
6. Das Pfadmuster. Dies entspricht dem Dateipfad, der verwendet wird, um Ihre BLOBs im angegebenen Containers zu suchen. In dem Pfad können Sie mindestens eine Instanz der beiden folgenden Variablen angeben: {date} und {time}.


Klicken Sie auf der Registerkarte "Eingaben" des Stream Analytics-Auftrags auf **EINGABE HINZUFÜGEN**, und wählen Sie die Standardoption **Verweisdaten** aus.

![Bild9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

Geben Sie nun die Informationen in die nachstehend gezeigten Felder für den BLOB-Speicher und das Speicherkonto ein.

![Bild10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

Scrollen Sie nach unten, um das Präfixmuster für die Pfadhierarchie, die das BLOB enthält, sowie die Werte für die Felder "Datumsformat" und "Zeitformat" anzugeben.

![Bild12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

Wählen Sie nun die richtige Serialisierungseinstellung für die Daten. Die Optionen sind JSON, CSV und Avro.

![Bild11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)

Wählen Sie dann **Abgeschlossen** aus. Daraufhin wird die BLOB-Speicher-Verweisdateneingabe erstellt.


## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-de/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=August15_HO6-->