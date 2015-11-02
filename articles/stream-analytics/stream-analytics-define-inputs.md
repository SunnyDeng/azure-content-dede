<properties 
	pageTitle="Definieren von Eingaben | Microsoft Azure" 
	description="Grundlegendes zu Stream Analytics-Eingaben" 
	keywords="Big Data-Analysen,Clouddienst,Internet der Dinge,verwalteter Dienst,Datenstromverarbeitung, Streaming Analytics, Streamingdaten"
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
	ms.date="10/13/2015"
	ms.author="jeffstok"/>

# Grundlegendes zu Stream Analytics-Eingaben

Azure Stream Analytics-Eingaben sind als Verbindung mit einer Datenquelle definiert. Stream Analytics verfügt über eine hervorragende Integration in die Azure-Quellen Event Hub, IoT Hub und Blobspeicher von innerhalb und außerhalb des Azure-Abonnements, unter dem Ihr Auftrag ausgeführt wird. Werden Daten mittels Push an diese Datenquelle übertragen, werden sie von dem Stream Analytics-Auftrag übernommen und in Echtzeit verarbeitet. Eingaben werden in zwei unterschiedliche Typen unterteilt: Datenstromeingaben und Verweisdateneingaben.

- **Datenstromeingaben**: Ein Datenstrom ist eine ungebundene Abfolge von eingehenden Ereignissen im Verlauf der Zeit. Stream Analytics-Aufträge müssen mindestens eine Datenstromeingabe enthalten, die vom Auftrag genutzt und umgewandelt werden soll. Blobspeicher, Event Hubs und IoT Hubs werden als Datenstrom-Eingabequellen unterstützt. Event Hubs werden verwendet, um Ereignisdatenströme von mehreren Geräten und Diensten, z. B. Aktivitätsfeeds in sozialen Medien, Börseninformationen oder Daten von Sensoren, zu sammeln. IoT Hubs sind zum Sammeln von Daten von verbundenen Geräten in IoT-Szenarien (Internet of Things) optimiert. Der Blobspeicher kann als Eingabequelle für das Erfassen von Massendaten als Datenstrom verwendet werden.  
- **Verweisdaten**: Stream Analytics unterstützt einen zweiten Eingabetyp, der als „Verweisdaten“ bezeichnet wird. Dabei handelt es sich um zusätzliche Daten, die in der Regel statisch sind oder sich nur selten ändern. Sie werden üblicherweise dazu verwendet, um Korrelationen und Suchvorgänge auszuführen. Azure-BLOB-Speicher ist derzeit die einzige unterstützte Eingabequelle für Verweisdaten. Blobs für Verweisdatenquellen sind auf eine Größe von 50 MB beschränkt. Informationen zum Erstellen von Verweisdateneingaben finden Sie unter [Verwenden von Verweisdaten](stream-analytics-use-reference-data.md).  

## Erstellen einer Event Hub-Datenstromeingabe

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) sind hoch skalierbare Veröffentlichen-Abonnieren-Ereigniserfasser. Er kann Millionen von Ereignissen pro Sekunde erfassen. Auf diese Weise können Sie riesige Datenmengen verarbeiten und analysieren, die von vernetzten Geräten und Anwendungen erzeugt werden. Er ist eine der am häufigsten verwendeten Eingaben für Stream Analytics. Event Hubs und Stream Analytics bieten zusammen eine End-to-End-Lösung für Echtzeitanalysen. Event Hubs ermöglichen es Kunden, Ereignisse in Echtzeit an Azure zu übergeben, sodass Stream Analytics-Aufträge diese in Echtzeit verarbeiten können. Kunden können z. B. Webklicks, Sensormesswerte oder Onlineprotokollereignisse an Event Hubs senden und dann Stream Analytics-Aufträge erstellen, die diese Event Hubs als Eingabedatenströme für die Filterung, Aggregation und Korrelation in Echtzeit verwenden.

Dabei gilt zu beachten, dass der Standardzeitstempel von Ereignissen, die von Event Hubs in Stream Analytics stammen, der Zeitstempel ist, an dem das Ereignis in Event Hub eingeht, also "EventEnqueuedUtcTime". Zum Verarbeiten der Daten als Datenstrom mit einem Zeitstempel in der Ereignisnutzlast muss das Schlüsselwort [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) verwendet werden.

## Verbrauchergruppen

Jede Eingabe an einen Stream Analytics Event Hub sollte für eine eigene Consumergruppe konfiguriert werden. Wenn ein Auftrag Selbstverknüpfungen oder mehrere Eingaben enthält, werden einige Eingaben im weiteren Verlauf möglicherweise von mehreren Lesern gelesen. Dies hat Einfluss auf die Gesamtzahl der Leser in einer einzelnen Consumergruppe. Zur Vermeidung der Überschreitung des Event Hub-Limits von fünf Lesern pro Consumergruppe pro Partition empfiehlt es sich, eine Consumergruppe für jeden Stream Analytics-Auftrag anzugeben. Beachten Sie, dass darüber hinaus ein Limit von 20 Verbrauchergruppen pro Event Hub gilt. Weitere Informationen finden Sie im [Programmierleitfaden für Event Hubs](./event-hubs/event-hubs-programming-guide.md).

## Konfigurieren von Event Hub als Eingabedatenstrom ##

In der folgenden Tabelle wird jede Eigenschaft in der Event-Eingaberegisterkarte mit einer entsprechenden Beschreibung erläutert:

| EIGENSCHAFTENNAME | BESCHREIBUNG |
|------|------|
| Eingabealias | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen. |
| Service Bus- Namespace | Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Service Bus-Namespace erstellt. |
| Event Hub | Der Name der Event Hub-Eingabe. |
| Event Hub-Richtlinienname | Die Richtlinie für den gemeinsamen Zugriff, die auf der Registerkarte "Event Hub-Konfiguration" erstellt werden kann. Jede Richtlinie für den gemeinsamen Zugriff umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Event Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. |
| Event Hub-Consumergruppe (Optional) | Die Verbrauchergruppe für das Erfassen von Daten aus den Event Hub. Wenn nichts angegeben wird, verwenden Stream Analytics-Aufträge die Standardverbrauchergruppe, um Daten vom Event Hub zu erfassen. Es wird empfohlen, für jeden Stream Analytics-Auftrag eine eigene Consumergruppe zu verwenden. |
| Ereignisserialisierungsformat | Um sicherzustellen, dass Ihre Abfragen wie erwartet funktionieren, muss Stream Analytics das Serialisierungsformat (JSON, CSV oder Avro) kennen, das Sie für eingehende Datenströme verwenden. |
| Codieren | Das einzige derzeit unterstützte Codierungsformat ist UTF-8. |

Wenn Ihre Daten aus einer Event Hub-Quelle stammen, können Sie auf einige Metadatenfelder in Ihrer Stream Analytics-Abfrage zugreifen. Die folgende Tabelle enthält die Felder und die entsprechenden Beschreibungen.

| EIGENSCHAFT | BESCHREIBUNG |
|------|------|
| EventProcessedUtcTime | Das Datum und die Uhrzeit der Verarbeitung des Ereignisses durch Stream Analytics. |
| EventEnqueuedUtcTime | Das Datum und die Uhrzeit des Ereignisempfangs durch die Event Hubs. |
| PartitionId | Die nullbasierte Partitions-ID für den Eingabeadapter. |

Sie können eine Abfrage beispielsweise wie folgt schreiben:

````
SELECT
	EventProcessedUtcTime,
	EventEnqueuedUtcTime,
	PartitionId
FROM Input
````

## Erstellen einer IoT Hub-Datenstromeingabe ##

IoT Hub ist ein hoch skalierbarer Veröffentlichen-Abonnieren-Ereigniserfasser, der für IoT-Szenarien optimiert ist. Dabei gilt zu beachten, dass der Standardzeitstempel von Ereignissen, die von IoT Hubs in Stream Analytics stammen, der Zeitstempel ist, an dem das Ereignis in IoT Hub eingeht, also "EventEnqueuedUtcTime". Zum Verarbeiten der Daten als Datenstrom mit einem Zeitstempel in der Ereignisnutzlast muss das Schlüsselwort [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) verwendet werden.

## Verbrauchergruppen ##

Jede Eingabe in einen Stream Analytics IoT Hub sollte für eine eigene Consumergruppe konfiguriert werden. Wenn ein Auftrag Selbstverknüpfungen oder mehrere Eingaben enthält, werden einige Eingaben im weiteren Verlauf möglicherweise von mehreren Lesern gelesen. Dies hat Einfluss auf die Gesamtzahl der Leser in einer einzelnen Consumergruppe. Zur Vermeidung der Überschreitung des IoT Hub-Limits von fünf Lesern pro Consumergruppe pro Partition empfiehlt es sich, eine Consumergruppe für jeden Stream Analytics-Auftrag anzugeben.

## Konfigurieren von IoT Hub als Eingabedatenstrom ##

In der folgenden Tabelle wird jede Eigenschaft auf der IoT Hub-Eingaberegisterkarte mit einer entsprechenden Beschreibung erläutert:

| EIGENSCHAFTENNAME | BESCHREIBUNG |
|------|------|
| Eingabealias | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen. |
| IoT Hub | Ein IoT Hub ist ein Container für einen Satz von Nachrichtenentitäten. |
| Endgerät | Der Name des IoT Hub-Endgeräts. |
| Der Name der SAS-Richtlinie | Die SAS-Richtlinie zum Gewähren des Zugriffs auf den IoT Hub. Jede Richtlinie für den gemeinsamen Zugriff umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| SAS-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den IoT Hub verwendet wird. |
| Consumergruppe (optional) | Die Consumergruppe für das Erfassen von Daten aus dem IoT Hub. Wenn nichts angegeben wird, verwenden Stream Analytics-Aufträge die Standardconsumergruppe, um Daten vom IoT Hub zu erfassen. Es wird empfohlen, für jeden Stream Analytics-Auftrag eine eigene Consumergruppe zu verwenden. |
| Ereignisserialisierungsformat | Um sicherzustellen, dass Ihre Abfragen wie erwartet funktionieren, muss Stream Analytics das Serialisierungsformat (JSON, CSV oder Avro) kennen, das Sie für eingehende Datenströme verwenden. |
| Codieren | Das einzige derzeit unterstützte Codierungsformat ist UTF-8. |

Wenn Ihre Daten aus einer IoT Hub-Quelle stammen, können Sie auf einige Metadatenfelder in Ihrer Stream Analytics-Abfrage zugreifen. Die folgende Tabelle enthält die Felder und die entsprechenden Beschreibungen.

| EIGENSCHAFT | BESCHREIBUNG |
|------|------|
| EventProcessedUtcTime | Das Datum und die Uhrzeit der Verarbeitung des Ereignisses. |
| EventEnqueuedUtcTime | Das Datum und die Uhrzeit des Ereignisempfangs durch den IoT Hub. |
| PartitionId | Die nullbasierte Partitions-ID für den Eingabeadapter. |
| IoTHub.MessageId | Wird verwendet, um die bidirektionale Kommunikation im IoT Hub zu korrelieren. |
| IoTHub.CorrelationId | Wird in Nachrichtenantworten und Feedback im IoT Hub verwendet. |
| IoTHub.ConnectionDeviceId | Die authentifizierte ID zum Senden dieser Nachricht, mit der IoT Hub servicebound-Nachrichten kennzeichnet. |
| IoTHub.ConnectionDeviceGenerationId | Die "generationID" des authentifizierten Geräts, von dem die Nachricht gesendet wurde, die von IoT Hub in servicebound-Nachrichten eingefügt wird. |
| IoTHub.EnqueuedTime | Zeitpunkt des Empfangs der Nachricht durch IoT Hub. |
| IoTHub.StreamId | Eine benutzerdefinierte Ereigniseigenschaft, die vom Absendergerät hinzugefügt wird. |

## Erstellen eines Blobspeichers als Datenstromeingabe ##

Für Szenarios mit großen Mengen unstrukturierter Daten, die in der Cloud gespeichert werden sollen, bietet BLOB-Speicher eine kostengünstige und skalierbare Lösung. Daten im [Blobspeicher](http://azure.microsoft.com/services/storage/blobs/) werden im Allgemeinen als „ruhende“ Daten angesehen, können aber als Datenstrom von Stream Analytics verarbeitet werden. Ein häufiges Szenario für Blobspeichereingaben mit Stream Analytics ist die Verarbeitung von Protokollen, wobei Telemetriedaten von einem System erfasst werden und analysiert und verarbeitet werden müssen, um aussagekräftige Daten zu extrahieren.

Es ist wichtig zu beachten, dass der Standardzeitstempel von Blob-Speicherereignissen in Stream Analytics der Zeitstempel ist, an dem das Blob zuletzt geändert wurde, sprich *BlobLastModifiedUtcTime*. Zum Verarbeiten der Daten als Datenstrom mit einem Zeitstempel in der Ereignisnutzlast muss das Schlüsselwort [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) verwendet werden.

In der folgenden Tabelle wird jede Eigenschaft in der Blobspeicher-Eingaberegisterkarte mit einer entsprechenden Beschreibung erläutert:

<table>
<tbody>
<tr>
<td>EIGENSCHAFTENNAME</td>
<td>BESCHREIBUNG</td>
</tr>
<tr>
<td>Eingabealias</td>
<td>Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen.</td>
</tr>
<tr>
<td>Speicherkonto</td>
<td>Der Name des Speicherkontos an, in dem sich die Blobdateien befinden.</td>
</tr>
<tr>
<td>Speicherkontoschlüssel</td>
<td>Der geheime Schlüssel, der dem Speicherkonto zugeordnet ist.</td>
</tr>
<tr>
<td>Speichercontainer
</td>
<td>Container stellen eine logische Gruppierung für Blobs bereit, die im Microsoft Azure-Blob-Dienst gespeichert sind. Wenn Sie ein Blob in den Blob-Dienst hochladen, müssen Sie einen Container für das Blob angeben.</td>
</tr>
<tr>
<td>Präfixmusters des Pfads [optional]</td>
<td>Dies entspricht dem Dateipfad, der verwendet wird, um Ihre BLOBs im angegebenen Containers zu suchen. In dem Pfad können Sie mindestens eine Instanz der 3 folgenden Variablen angeben:<BR>{date}, {time}<BR>{partition}<BR>Beispiel 1: cluster1/logs/{date}/{time}/{partition}<BR>Beispiel 2: cluster1/logs/{date}</td>
</tr>
<tr>
<td>Datumsformat [optional]</td>
<td>Wenn das date-Token im Pfadpräfix verwendet wird, können Sie das Datumsformat auswählen, unter dem die Dateien gespeichert werden. Beispiel: YYYY/MM/TT</td>
</tr>
<tr>
<td>Zeitformat [optional]</td>
<td>Wenn das time-Token im Pfadpräfix verwendet wird, können Sie das Zeitformat auswählen, unter dem die Dateien gespeichert werden. Der einzige derzeit unterstützte Wert ist HH</td>
</tr>
<tr>
<td>Ereignisserialisierungsformat</td>
<td>Um sicherzustellen, dass Ihre Abfragen wie erwartet funktionieren, muss Stream Analytics das Serialisierungsformat (JSON, CSV oder Avro) kennen, das Sie für eingehende Datenströme verwenden.</td>
</tr>
<tr>
<td>Codieren</td>
<td>Bei CSV und JSON ist UTF-8 gegenwärtig das einzige unterstützte Codierungsformat.</td>
</tr>
<tr>
<td>Trennzeichen</td>
<td>Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich.</td>
</tr>
</tbody>
</table>

Wenn Ihre Daten aus einer Blobspeicherquelle stammen, können Sie auf einige Metadatenfelder in Ihrer Stream Analytics-Abfrage zugreifen. Die folgende Tabelle enthält die Felder und die entsprechenden Beschreibungen.

| EIGENSCHAFT | BESCHREIBUNG |
|------|------|
| BlobName | Der Name des Eingabe-Blobs, aus dem das Ereignis stammt. |
| EventProcessedUtcTime | Das Datum und die Uhrzeit der Verarbeitung des Ereignisses durch Stream Analytics. |
| BlobLastModifiedUtcTime | Das Datum und die Uhrzeit der letzten Änderung des Blobs. |
| PartitionId | Die nullbasierte Partitions-ID für den Eingabeadapter. |

Sie können eine Abfrage beispielsweise wie folgt schreiben:

````
SELECT
	BlobName,
	EventProcessedUtcTime,
	BlobLastModifiedUtcTime
FROM Input
````


## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte
Sie haben nun Stream Analytics kennengelernt, einen verwalteten Dienst für Stream Analytics für Daten aus dem Internet der Dinge. Weitere Informationen zu diesem Dienst finden Sie unter:

- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=Oct15_HO4-->