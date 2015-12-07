<properties 
	pageTitle="Herstellen von Verbindungen mit Stream Analytics-Eingaben | Microsoft Azure" 
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
	ms.date="11/23/2015" 
	ms.author="jeffstok"/>

# Herstellen von Verbindungen mit Stream Analytics-Eingabequellen

## Grundlegendes zu Stream Analytics-Eingaben
---
Eine Stream Analytics Eingabe ist als eine Verbindung mit einer Datenquelle definiert. Stream Analytics verfügt über eine hervorragende Integration in die Azure-Quellen Event Hub und Blobspeicher von innerhalb und außerhalb des Auftragsabonnements. Werden Daten an diese Datenquelle gesendet, werden sie von dem Stream Analytics-Auftrag übernommen und in Echtzeit verarbeitet. Eingaben werden in zwei unterschiedliche Typen unterteilt: Datenstromeingaben und Verweisdateneingaben.

## Datenstromeingaben
---
Stream Analytics-Aufträge müssen mindestens eine Datenstromeingabe enthalten, die vom Auftrag genutzt und umgewandelt werden soll. Azure-BLOB-Speicher und Azure Event Hubs werden als Datenstrom-Eingabequellen unterstützt. Azure Event Hubs werden verwendet, um Ereignisdatenströme von mehreren Geräten und Diensten, z. B. Aktivitätsfeeds in sozialen Medien, Börseninformationen oder Daten von Sensoren, zu sammeln. Alternativ kann Azure-BLOB-Speicher als eine Eingabequelle für das Erfassen von Massendaten verwendet werden.

## Verweisdateneingaben
---
Stream Analytics unterstützt einen zweiten Eingabetyp, der als Verweisdaten bezeichnet wird. Diese Daten sind zusätzliche Daten, die üblicherweise dazu verwendet werden, Korrelationen und Suchvorgänge auszuführen, und die Daten hier sind in der Regel statisch oder ändern sich selten. Azure-BLOB-Speicher ist derzeit die einzige unterstützte Eingabequelle für Verweisdaten. Blobs für Verweisdatenquellen sind auf eine Größe von 50 MB beschränkt.

## Erstellen einer Event Hub-Datenstromeingabe
---
### Übersicht über Event Hubs
Event Hubs sind hochgradig skalierbare Ereigniserfasser und die am häufigsten verwendete Methode für die Datenerfassung in Stream Analytics-Aufträgen. Event Hubs und Stream Analytics bieten Kunden gemeinsam eine End-to-EndLösung für Echtzeitanalysen – mit Event Hubs können Kunden Ereignisse in Echtzeit in Azure einspeisen, die dann von Stream Analytics ebenfalls in Echtzeit verarbeitet werden können. Kunden können z. B. Webklicks, Sensorenmesswerte oder Onlineprotokollereignisse an Event Hubs senden und dann Stream Analytics-Aufträge erstellen, die diese Event Hubs als Eingabedatenströme für die Filterung, Aggregation und Zusammenführung in Echtzeit verwenden. Event Hubs können auch für den Datenausgang verwendet werden. Weitere Informationen zu Event Hubs finden Sie in der [Event Hubs](https://azure.microsoft.com/services/event-hubs/ "Event Hubs")-Dokumentation.

### Verbrauchergruppen
Jede Eingabe an einen Stream Analytics Event Hub sollte für eine eigene Consumergruppe konfiguriert werden. Wenn ein Auftrag Selbstverknüpfungen oder mehrere Eingaben enthält, werden einige Eingaben im weiteren Verlauf möglicherweise von mehreren Lesern gelesen. Dies hat Einfluss auf die Gesamtzahl der Leser in einer einzelnen Consumergruppe. Zur Vermeidung der Überschreitung des Event Hub-Limits von fünf Lesern pro Consumergruppe pro Partition empfiehlt es sich, eine Consumergruppe für jeden Stream Analytics-Auftrag anzugeben. Beachten Sie, dass darüber hinaus ein Limit von 20 Verbrauchergruppen pro Event Hub gilt. Weitere Informationen finden Sie im [Programmierleitfaden für Event Hubs](https://msdn.microsoft.com/library/azure/dn789972.aspx "Programmierleitfaden für Ereignis-Hubs").

## Erstellen eines Event Hub-Eingabedatenstroms
---
### Hinzufügen eines Event Hubs als Datenstromeingabe  ###

1. Klicken Sie auf der Registerkarte "Eingaben" des Stream Analytics-Auftrags auf **EINGABE HINZUFÜGEN**, und wählen Sie die Standardoption **Datenstrom** aus. Klicken Sie dann mit der rechten Maustaste.

    ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. Wählen Sie als Nächstes **Event Hub** aus.

    ![Bild6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

3. Geben Sie Werte in die folgenden Felder ein, oder wählen Sie sie aus, und klicken Sie mit der rechten Maustaste:

    - **Eingabealias**: Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen.  
    - **Service Bus-Namespace**: Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Service Bus-Namespace erstellt.  
    - **Event Hub**: Der Name Ihrer Event Hub-Eingabe.  
    - **Event Hub-Richtlinienname**: Die freigegebene Zugriffsrichtlinie, die auf der Registerkarte "Event Hub-Konfiguration" erstellt werden kann. Jede Richtlinie für den gemeinsamen Zugriff umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel.  
    - **Event Hub-Consumergruppe** (optional): Die Consumergruppe für das Erfassen von Daten aus dem Event Hub. Wenn nichts angegeben wird, verwenden Stream Analytics-Aufträge die Standardverbrauchergruppe, um Daten vom Event Hub zu erfassen. Es wird empfohlen, für jeden Stream Analytics-Auftrag eine eigene Consumergruppe zu verwenden.  

    ![Bild7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

4. Geben Sie die folgenden Einstellungen an:

    - **Ereignisserialisierungsformat**: Um sicherzustellen, dass Ihre Abfragen wie erwartet funktionieren, muss Stream Analytics das Serialisierungsformat (JSON, CSV oder Avro) kennen, das Sie für eingehende Datenströme verwenden.  
    - **Codierung**: UTF-8 ist gegenwärtig das einzige unterstützte Codierungsformat.  

    ![Bild8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

5. Klicken Sie auf das Häkchen, um den Assistenten abzuschließen und um zu überprüfen, ob Stream Analytics erfolgreich mit dem Event Hub verbunden werden kann.

## Erstellen eines Blobspeichers als Datenstromeingabe
---
Für Szenarios mit großen Mengen unstrukturierter Daten, die in der Cloud gespeichert werden sollen, bietet BLOB-Speicher eine kostengünstige und skalierbare Lösung. Daten im Blobspeicher werden im Allgemeinen als "ruhende" Daten angesehen, können aber als Datenstrom von Stream Analytics verarbeitet werden. Ein häufiges Szenario für Blobspeichereingaben mit Stream Analytics ist die Verarbeitung von Protokollen, wobei Telemetriedaten von einem System erfasst werden und analysiert und verarbeitet werden müssen, um aussagekräftige Daten zu extrahieren. Es ist wichtig zu beachten, dass der Standardzeitstempel von Blobspeicherereignissen in Stream Analytics der Zeitstempel ist, zu dem das Blob erstellt wurde. Zum Verarbeiten der Daten als Datenstrom mit einem Zeitstempel in der Ereignisnutzlast muss das Schlüsselwort [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) verwendet werden. Weitere Informationen zu Blobspeichern finden Sie in der Dokumentation zu [Blobspeichern](http://azure.microsoft.com/services/storage/blobs/).

### Hinzufügen eines Blobspeichers als Datenstromeingabe  ###

1. Klicken Sie auf der Registerkarte "Eingaben" des Stream Analytics-Auftrags auf **EINGABE HINZUFÜGEN**, und wählen Sie die Standardoption **Datenstrom** aus. Klicken Sie dann mit der rechten Maustaste.

    ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. Wählen Sie **Blobspeicher** aus, und klicken Sie mit der rechten Maustaste.

    ![Bild2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

3. Geben Sie Werte in die folgenden Felder ein, bzw. wählen Sie diese aus:

    - **Eingabealias**: Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen.  
    - **Speicherkonto**: Befindet sich das Speicherkonto in einem anderen Abonnement als der Streamingauftrag, sind der Speicherkontoname und der Speicherkontoschlüssel erforderlich.  
    - **Speichercontainer**: Container stellen eine logische Gruppierung für Blobs bereit, die im Microsoft Azure-Blobdienst gespeichert sind. Wenn Sie ein Blob in den Blobdienst hochladen, müssen Sie einen Container für das Blob angeben.  

    ![Bild3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

4. Klicken Sie auf das Kontrollkästchen **Erweiterte Einstellungen konfigurieren** für die Option, um das Präfixmuster des Pfads zu konfigurieren, damit Blobs in einem benutzerdefinierten Pfad gelesen werden können. Ist diese Option nicht aktiviert, liest Stream Analytics alle BLOBs im Container.

    ![Bild4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

5. Wählen Sie folgende Einstellungen aus:

    - **Ereignisserialisierungsformat**: Um sicherzustellen, dass Ihre Abfragen wie erwartet funktionieren, muss Stream Analytics das Serialisierungsformat (JSON, CSV oder Avro) kennen, das Sie für eingehende Datenströme verwenden.  
    - **Codierung**: UTF-8 ist gegenwärtig das einzige unterstützte Codierungsformat.  


    ![Bild5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

6. Klicken Sie auf das Häkchen, um den Assistenten abzuschließen und um zu überprüfen, ob Stream Analytics erfolgreich mit dem Blobspeicherkonto verbunden werden kann.

## Erstellen von Blobspeicher-Verweisdaten
---
Ein Blobspeicher kann zum Definieren von Verweisdaten für einen Stream Analytics-Auftrag verwendet werden. Dies sind statische oder sich langsam ändernde Daten, die zum Durchführen von Suchvorgängen oder zum Korrelieren von Daten verwendet werden. Die Unterstützung für das Aktualisieren von Verweisdaten kann aktiviert werden, indem in der Eingabekonfiguration ein Pfadmuster angegeben wird, für das die Token {date} und {time} verwendet werden. Stream Analytics aktualisiert Definitionen von Verweisdaten auf der Grundlage dieses Pfadmusters. Beispiel: Das Muster `"/sample/{date}/{time}/products.csv"` mit dem Datumsformat "YYYY-MM-DD" und dem Zeitformat "HH:mm" weist Stream Analytics an, das aktualisierte Blob `"/sample/2015-04-16/17:30/products.csv"` am 16. April 2015 um 17:30 (UTC-Zeitzone) abzurufen.

> [AZURE.NOTE]Derzeit suchen Stream Analytics-Aufträge nur nach aktualisierten Verweisdaten in einem BLOB, wenn die Uhrzeit mit der Uhrzeit zusammenfällt, die im BLOB-Namen codiert ist: Aufträge suchen z. B. am 16. April 2015 zwischen 17:30 Uhr und 17:30:59,999999999 Uhr (UTC-Zeitzone) nach "/beispiel/2015-04-16/17:30/produkte.csv". Wenn die Uhr auf 17:31 Uhr gesprungen ist, wird die Suche nach "/beispiel/2015-04-16/17:30/produkte.csv" beendet und die Suche nach "/beispiel/2015-04-16/17:31/produkte.csv" begonnen.

Der einzige Zeitpunkt, bei dem frühere Verweisdaten-BLOBs berücksichtigt werden, ist der Zeitpunkt, zu dem der Auftrag gestartet wird. Zu diesem Zeitpunkt sucht der Auftrag nach dem BLOB, in dessen Name der letzte Zeitpunkt (Datum und Uhrzeit) codiert ist, der vor dem Startzeitpunkt des Auftrags liegt (das neueste Verweisdaten-BLOB vor dem Startzeitpunkt des Auftrags). Dies erfolgt, damit sichergestellt ist, dass es beim Starten des Auftrags kein leeres Verweisdataset gibt. Wird kein BLOB gefunden, schlägt der Auftrag fehl, und es wird eine Diagnosemeldung angezeigt:

### Hinzufügen von Blobspeicher als Verweisdaten  ###

1. Klicken Sie auf der Registerkarte "Eingaben" des Stream Analytics-Auftrags auf **EINGABE HINZUFÜGEN**, und wählen Sie **Verweisdaten** aus. Klicken Sie dann mit der rechten Maustaste.

    ![Bild9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

2.	Geben Sie Werte in die folgenden Felder ein, bzw. wählen Sie diese aus:

    - **Eingabealias**: Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen.  
    - **Speicherkonto**: Befindet sich das Speicherkonto in einem anderen Abonnement als der Streamingauftrag, sind der Speicherkontoname und der Speicherkontoschlüssel erforderlich.  
    - **Speichercontainer**: Container stellen eine logische Gruppierung für Blobs bereit, die im Microsoft Azure-Blobdienst gespeichert sind. Wenn Sie ein Blob in den Blobdienst hochladen, müssen Sie einen Container für das Blob angeben.  
    - **Pfadmuster**: Dies entspricht dem Dateipfad, der verwendet wird, um Ihre Blobs im angegebenen Container zu suchen. In dem Pfad können Sie mindestens eine Instanz der beiden folgenden Variablen angeben: {date}, {time}.  

    ![Bild10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

3. Wählen Sie folgende Einstellungen aus:

    - **Ereignisserialisierungsformat**: Um sicherzustellen, dass Ihre Abfragen wie erwartet funktionieren, muss Stream Analytics das Serialisierungsformat (JSON, CSV oder Avro) kennen, das Sie für eingehende Datenströme verwenden.  
    - **Codierung**: UTF-8 ist gegenwärtig das einzige unterstützte Codierungsformat.  

    ![Bild12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

4.	Klicken Sie auf das Häkchen, um den Assistenten abzuschließen und um zu überprüfen, ob Stream Analytics erfolgreich mit dem Blobspeicherkonto verbunden werden kann.

    ![Bild11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)


## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1125_2015-->