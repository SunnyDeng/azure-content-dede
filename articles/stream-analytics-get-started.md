<properties linkid="manage-services-Stream-Analytics-get-started" urlDisplayName="Get Started" pageTitle="Erste Schritte mit Azure Stream Analytics | Azure" metaKeywords="" description="Get started using Azure Stream Analytics to process and transform events in Azure Service Bus Event Hub and store the results to Azure SQL Database." metaCanonical="" services="stream analytics" documentationCenter="" title="Get started with Azure Stream Analytics" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="stream analytics" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/28/2014" ms.author="jgao" />


# Erste Schritte mit Azure Stream Analytics

Azure Stream Analytics ist ein vollständig verwalteter Dienst, der eine geringe Latenz, Hochverfügbarkeit und eine skalierbare komplexe Ereignisverarbeitung durch das Streaming von Daten in der Cloud bietet. Weitere Informationen finden Sie unter [Einführung in Azure Stream Analytics][stream.analytics.introduction] und in der [Azure Stream Analytics-Dokumentation][stream.analytics.documentation].

Um schnell mit Stream Analytics arbeiten zu können, zeigt Ihnen dieses Lernprogramm, wie Sie Gerätetemperaturmessdaten aus einem [Azure Service Bus Event Hub][azure.event.hubs.documentation] nutzen und die Daten verarbeiten können und wie Sie die Ergebnisse an eine [Azure SQL-Datenbank][azure.sql.database.documentation] ausgeben können.  Das folgende Diagramm zeigt den Ereignisfluss von der Eingabe über die Verarbeitung bis hin zur Ausgabe:
  
![Azure Stream Analytics get started flow][img.get.started.flowchart]

##Generieren von Event Hub-Beispieldaten
Dieses Lernprogramm nutzt die Service Bus Event Hubs-Anwendung mit ersten Schritten, ein Codebeispiel in der MSDN-Code-Galerie, um ein neues Event Hub zu erstellen, Gerätetemperatur-Beispielsmesswerte zu generieren und die Gerätemessdaten an das Event Hub zu senden.

###Erstellen eines Service Bus-Namespaces
Die Beispielanwendung erstellt einen Event Hub in einem vorhandenen Service Bus-Namespace.  Sie können einen Service Bus-Namespace verwenden, den Sie bereits bereitgestellt haben, oder folgen Sie den unten stehenden Schritten, um einen neuen zu erstellen:

1.	Melden Sie sich beim [Azure-Verwaltungsportal][azure.management.portal] an.
2.	Klicken Sie am unteren Rand der Service Bus-Seite auf **ERSTELLEN**, und befolgen Sie die Anweisungen, um einen Namespace zu erstellen. Verwenden Sie als Typ **MESSAGING**.
3.	Klicken Sie auf den neu erstellten Namespace, und klicken Sie dann am unteren Rand der Seite auf **VERBINDUNGSINFORMATIONEN**.
4.	Kopieren Sie die Verbindungszeichenfolge. Sie werden sie später im Lernprogramm benötigen.

###Erstellen eines Azure-Speicherkontos

Diese Beispielanwendung erfordert ein Azure-Speicherkonto oder einen Speicheremulator, um den Anwendungsstatus zu verwalten. Sie können ein vorhandenes Speicherkonto verwenden oder die folgenden Schritte ausführen, um eins zu erstellen: 

1.	Erstellen Sie über das Portal ein neues Speicherkonto, indem Sie auf **NEU**, **DATENDIENSTE**, **SPEICHER**, **SCHNELLERFASSUNG** klicken und dann die Anweisungen befolgen.
2.	Wählen Sie das neu erstellte Speicherkonto aus, und klicken Sie dann unten auf der Seite auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
3.	Kopieren Sie den Namen des Speicherkontos und einen der Zugriffsschlüssel.

###Generieren von Event Hub-Beispieldaten

1.	Laden Sie [Service Bus Event Hubs Getting Started.zip](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Event-Hub-286fd097) auf Ihren Computer herunter und entpacken Sie die Datei.
2.	Öffnen Sie die Projektmappendatei **EventHubSample.sln** in Visual Studio.
3.	Öffnen Sie die Datei **app.config**.
4.	Geben Sie die Verbindungszeichenfolgen für das Speicherkonto und den Service Bus an. Die Schlüsselnamen sind **Microsoft.ServiceBus.ConnectionString** und **AzureStorageConnectionString**. Die Verbindungszeichenfolge für das Speicherkonto hat das folgende Format: 	

		DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<yourAccountKey>;
5.	Erstellen Sie die Projektmappe.
6.	Führen Sie die Anwendung aus dem bin-Ordner aus.  Die Nutzung lautet wie folgt: 

		BasicEventHubSample <eventhubname> <NumberOfMessagesToSend> <NumberOfPartitions> 

	Im folgenden Beispiel wird ein neues Event Hub namens **devicereadings** mit **16** Partitionen erstellt. Dann werden **200** Ereignisse an das Event Hub gesendet: 

		BasicEventHubSample devicereadings 200 16

 	![insert image here][img.stream.analytics.event.hub.client.output] 
 	

###Erstellen einer Richtlinie für den freigegebenen Zugriff für ein Event Hub
Zwar gibt es bereits eine Richtlinie für den freigegebenen Zugriff für den Service Bus-Namespace, die zum Herstellen einer Verbindung mit allen Elementen im Namespace verwendet werden kann, doch für die beste Vorgehensweise im Hinblick auf Sicherheit erstellen wir eine separate Richtlinie nur für das Event Hub.

1.	Klicken Sie im Service Bus-Arbeitsbereich im Portal auf den Namen des Service Bus-Namespaces.
2.	Klicken Sie am oberen Seitenrand auf **EVENT HUBS**.
3.	Klicken Sie für dieses Lernprogramm auf das Event Hub **devicereadings**.
4.	Klicken Sie am oberen Seitenrand auf **KONFIGURIEREN**.
5.	Erstellen Sie unter "Freigegebene Zugriffsrichtlinien" eine neue Richtlinie mit den Berechtigungen zum **Verwalten**.

	![][img.stream.analytics.event.hub.shared.access.policy.config]
6.	Klicken Sie unten auf der Seite auf **SPEICHERN**.
7.	Wenn sich der Event Hub in einem anderen Abonnement befindet als der Stream Analytics-Auftrag, müssen Sie die Verbindungsinformationen zur späteren Verwendung kopieren und speichern.  Klicken Sie dazu auf **DASHBOARD** und dann am unteren Rand auf **VERBINDUNGSINFORMATIONEN**, und speichern Sie die Verbindungszeichenfolge.


##Vorbereiten einer Azure SQL-Datenbank zum Speichern von Ausgabedaten
Azure Stream Analytics kann Daten in Azure SQL-Datenbank, Azure-BLOB-Speicher und Azure Event Hub ausgeben. In diesem Lernprogramm definieren Sie einen Auftrag, der Daten in eine Azure SQL-Datenbank ausgibt. Weitere Informationen finden Sie unter "Erste Schritte mit Microsoft Azure SQL-Datenbank.

###Erstellen einer Azure SQL-Datenbank
Wenn Sie bereits eine Azure SQL-Datenbank für dieses Lernprogramm haben, überspringen Sie diesen Abschnitt.

1.	Klicken Sie im Verwaltungsportal auf **NEU**, **DATENDIENSTE**, **SQL-DATENBANK**, **SCHNELLERFASSUNG**.  Geben Sie einen Datenbanknamen für einen vorhandenen oder neuen SQL-Datenbankserver an.
2.	Auswählen der neu erstellten Datenbank
3.	Klicken Sie auf **DASHBOARD** und dann im rechten Bereich der Seite auf **Verbindungszeichenfolgen anzeigen**, und kopieren Sie dann die Verbindungszeichenfolge **ADO.NET**. Sie werden sie später im Lernprogramm benötigen.  
4.	Stellen Sie sicher, dass die Firewalleinstellungen auf Serverebene es Ihnen ermöglichen, eine Verbindung mit der Datenbank herzustellen.  Dazu können Sie eine neue IP-Regel auf der Registerkarte "Konfigurieren" des Servers hinzufügen. Weitere Informationen, wie z. B. die Handhabung von dynamischen IP, finden Sie unter [http://msdn.microsoft.com/en-us/library/azure/ee621782.aspx](http://msdn.microsoft.com/en-us/library/azure/ee621782.aspx).

###Erstellen von Ausgabetabellen
1.	Öffnen Sie Visual Studio oder SQL Server Management Studio.
2.	Stellen Sie eine Verbindung mit der Azure SQL-Datenbank her.
3.	Verwenden Sie die folgenden T-SQL-Anweisungen, um zwei Tabellen in Ihrer Datenbank zu erstellen:

		CREATE TABLE [dbo].[PassthroughReadings] (
		    [DeviceId]      BIGINT NULL,
			[Temperature] BIGINT    NULL
		);

		GO
		CREATE CLUSTERED INDEX [PassthroughReadings]
		    ON [dbo].[PassthroughReadings]([DeviceId] ASC);
		GO

		CREATE TABLE [dbo].[AvgReadings] (
		    [WinStartTime]   DATETIME2 (6) NULL,
		    [WinEndTime]     DATETIME2 (6) NULL,
		    [DeviceId]      BIGINT NULL,
			[AvgTemperature] FLOAT (53)    NULL,
			[EventCount] BIGINT null
		);
		
		GO
		CREATE CLUSTERED INDEX [AvgReadings]
		    ON [dbo].[AvgReadings]([DeviceId] ASC);

	>[WACOM.NOTE] In einem Cluster gruppierte Indizes sind für alle SQL-Datenbanktabellen erforderlich, um Daten einzufügen.
	   
##Erstellen eines Stream Analytics-Auftrags

Nachdem Sie das Azure Service Bus Event Hub, die Azure SQL-Datenbank und die Ausgabetabellen erstellt haben, können Sie einen Stream Analytics-Auftrag erstellen.

###Bereitstellen eines Stream Analytics-Auftrags
1.	Klicken Sie im Verwaltungsportal auf **NEU**, **DATENDIENSTE**, **STREAM ANALYTICS**, **SCHNELLERFASSUNG**. 
2.	Geben Sie die folgenden Werte an, und klicken Sie dann auf **STREAM ANALYTICS-AUFTRAG ERSTELLEN**:

	- **AUFTRAGSNAME**: Geben Sie einen Auftragsnamen ein. Zum Beispiel **DeviceTemperatures**.
	- **REGION**: Wählen Sie die Region aus, in der der Auftrag ausgeführt werden soll. Azure Stream Analytics ist als Vorabversion derzeit nur in 2 Regionen verfügbar. Weitere Informationen finden Sie unter [Einschränkungen und bekannte Probleme von Azure Stream Analytics][stream.analytics.limitations]. Ziehen Sie es in Betracht, den Auftrag und das Event Hubs in derselben Region zu platzieren, um eine bessere Leistung sicherzustellen und um sicherzustellen, dass Sie nicht für die Übertragung von Daten zwischen Regionen bezahlen.
	- **SPEICHERKONTO**: Wählen Sie das Speicherkonto, das Sie zum Speichern von Überwachungsdaten für alle Stream Analytics-Aufträge verwenden möchten, die innerhalb dieser Region ausgeführt werden. Sie haben die Möglichkeit, ein vorhandenes Speicherkonto auszuwählen oder ein neues zu erstellen.
	
3.	Klicken Sie im linken Bereich auf **STREAM ANALYTICS**, um die Stream Analytics-Aufträge aufzulisten.

	![][img.stream.analytics.portal.button]
 
	Der neue Auftrag wird mit dem Status **NICHT GESTARTET** aufgeführt.  Beachten Sie, dass die Schaltfläche **START** am unteren Seitenrand deaktiviert ist. Sie müssen die Auftragseingabe, -ausgabe, -abfrage usw. konfigurieren, bevor Sie den Auftrag starten können. 

###Festlegen der Auftragseingabe

1.	Klicken Sie auf den Auftragsnamen.
2.	Klicken Sie am oberen Seitenrand auf **EINGABEN** und dann auf **EINGABE HINZUFÜGEN**. Das aufgerufene Dialogfeld führt Sie durch eine Reihe von Schritten, um Ihre Eingabe einzurichten.
3.	Wählen Sie **DATENSTROM**, und klicken Sie dann auf die rechte Taste.
4.	Wählen Sie **EVENT HUB**, und klicken Sie dann auf die rechte Taste.
5.	Geben Sie die folgenden Werte auf der dritten Seite ein, oder wählen Sie sie aus: 

	- **EINGABEALIAS**: Geben Sie einen Anzeigenamen für diesen Auftrag ein. Beachten Sie, dass Sie diesen Namen später in der Abfrage verwenden werden.
	- **EVENT HUB**: Wenn das Event Hub, das Sie erstellt haben, sich in demselben Abonnement wie der Stream Analytics-Auftrag befindet, wählen Sie den Namespace aus, in dem sich der Event Hub befindet.

		Wenn sich Ihr Event Hub in einem anderen Abonnement befindet, wählen Sie **Event Hub aus anderem Abonnement verwenden**, und geben Sie manuell den **SERVICE BUS-NAMESPACE**, **EVENT HUB-NAMEN**, **EVENT HUB-RICHTLINIENNAMEN**, **EVENT HUB-RICHTLINIENSCHLÜSSEL** und die **EVENT HUB-PARTITIONSANZAHL** ein.  

		>[WACOM.NOTE] In diesem Beispiel wird die Standardanzahl an Partitionen verwendet, sprich 16.
		
	- **EVENT HUB-NAME**: Wählen Sie den Namen des Azure Event Hubs aus, das Sie erstellt haben. Verwenden Sie für dieses Lernprogramm **devicereadings**.
	- **EVENT HUB-RICHTLINIENNAME**: Wählen Sie die Event Hub-Richtlinie aus, die Sie zuvor in diesem Lernprogramm erstellt haben.
 
	![][img.stream.analytics.config.input]

6.	Klicken Sie auf die rechte Taste.
7.	Geben Sie die folgenden Werte an:

	- **EREIGNISSERIALISIERUNGSFORMAT**: JSON
	- **CODIERUNG**: UTF8

8.	Klicken Sie auf das Häkchen, um diese Quelle hinzuzufügen und um zu überprüfen, ob Stream Analytics erfolgreich mit dem Event Hub verbunden werden kann.

###Festlegen der Auftragsausgabe
1.	Klicken Sie am oberen Seitenrand auf **AUSGABE** und dann auf **AUSGABE HINZUFÜGEN**.
2.	Wählen Sie **SQL-DATENBANK** aus, und klicken Sie dann auf die rechte Taste.
3.	Geben Sie die folgenden Werte ein bzw. wählen diese aus.  Verwenden Sie die ADO.NET-Verbindungszeichenfolge aus der Datenbank, um die folgenden Felder auszufüllen:

	- **SQL-DATENBANK**: Wählen Sie die SQL-Datenbank aus, die Sie zuvor in diesem Lernprogramm erstellt haben.  Wenn sie sich in demselben Abonnement befindet, wählen Sie die Datenbank aus dem Dropdown-Menü aus.   Wenn dies nicht der Fall ist, geben Sie den Servernamen und die Datenbankfelder manuell ein. 
	- **BENUTZERNAME**: Geben Sie den Anmeldenamen für die SQL-Datenbank ein.
	- **KENNWORT**: Geben Sie das Anmeldekennwort für die SQL-Datenbank ein.
	- **TABELLE**: Geben Sie die Tabelle an, an die die Ausgabe gesendet werden soll.  Verwenden Sie **PassthroughReadings**.

	![][img.stream.analytics.config.output]

4.	Klicken Sie auf das Häkchen, um die Ausgabe zu erstellen und um zu prüfen, ob Stream Analytics erfolgreich eine Verbindung zur SQL-Datenbank wie angegeben herstellen kann.

###Festlegen der Auftragsabfrage
Stream Analytics unterstützt ein einfaches, deklaratives Abfragemodell zum Beschreiben der Transformationen.  Weitere Informationen zur Sprache finden Sie in der Azure Stream Analytics-Abfragesprachreferenz.  

Dieses Lernprogramm beginnt mit einer einfachen Pass-Through-Abfrage, die Gerätetemperaturmesswerte in eine SQL-Datenbanktabelle ausgibt.

1.	Klicken Sie am oberen Seitenrand auf **Abfrage**.
2.	Fügen Sie dem Code-Editor Folgendes hinzu:

		Auswählen der Geräte-ID, Temperatur aus den Eingaben
Stellen Sie sicher, dass der Name der Eingabequelle dem Namen der Eingabe entspricht, die Sie zuvor angegeben haben.
3.	Klicken Sie unten auf der Seite auf **SPEICHERN** und dann zum Bestätigen auf **JA**.

##Starten des Auftrags
Standardmäßig beginnen Stream Analytics-Aufträge mit dem Lesen von eingehenden Ereignissen ab dem Zeitpunkt, an dem der Auftrag gestartet wird.  Da der Event Hub vorhandene Daten zur Verarbeitung enthält, muss der Auftrag so konfiguriert werden, dass diese historischen Daten genutzt werden.  

1.	Klicken Sie am oberen Seitenrand auf **KONFIGURIEREN**.
2.	Ändern Sie den Wert für **AUSGABESTART** in **BENUTZERDEFINIERTE ZEIT**, und legen Sie eine Startzeit fest.  Stellen Sie sicher, dass die Startzeit vor der Zeit liegt, zu der Sie BasicEventHubSample ausgeführt haben.  
3.	Klicken Sie unten auf der Seite auf **SPEICHERN** und dann zum Bestätigen auf **JA**.
3.	Klicken Sie am oberen Seitenrand auf **DASHBOARD** und dann unten auf der Seite auf **START**. Bestätigen Sie dies mit **JA**.  Im Bereich **Auf einen Blick** ändert sich der Wert für **STATUS** in **Wird gestartet**. Es kann einige Minuten dauern, bis der Startvorgang abgeschlossen ist und in den Status **Wird ausgeführt** wechselt.   


##Anzeigen der Auftragsausgabe

1.	Stellen Sie in Visual Studio oder SQL Server Management Studio eine Verbindung mit der SQL-Datenbank her, und führen Sie die folgende Abfrage aus: 

		SELECT * FROM PassthroughReadings

2.	Sie sehen Datensätze, die den Messereignissen vom Event Hub entsprechen.   

	![][img.stream.analytics.job.output1]

	Sie können die BasicEventHubSample-Anwendung erneut ausführen, um neue Ereignisse zu generieren und diese in Echtzeit an die Ausgabe zu verteilen, indem Sie die SELECT *-Abfrage erneut ausführen.
	
	Wenn Sie Probleme mit fehlenden oder unerwarteten Ausgaben haben, sehen Sie sich die Vorgangsprotokolle für den Auftrag an, die im rechten Bereich der Dashboard-Seite verknüpft sind.

##Beenden, Aktualisieren und erneutes Starten des Auftrags
Führen Sie jetzt eine interessantere Abfrage der Daten durch.
1.	Klicken Sie auf der Seite **DASHBOARD** oder **ÜBERWACHEN** auf **BEENDEN**.
2.	Ersetzen Sie auf der Seite **ABFRAGE** die vorhandene Abfrage durch die folgende, und klicken Sie dann auf **SPEICHERN**:

		SELECT DateAdd(second,-5,System.TimeStamp) as WinStartTime, system.TimeStamp as WinEndTime, DeviceId, Avg(Temperature) as AvgTemperature, Count(*) as EventCount 
		FROM input
		GROUP BY TumblingWindow(second, 5), DeviceId

	In dieser neuen Abfrage wird die Zeit verwendet, zu der das Ereignis mittels Push als Zeitstempel an Event Hub übertragen wurde, wodurch die Durchschnittstemperaturmesswerte alle 5 Sekunden und die Anzahl der Ereignisse, die in das 5-Sekunden-Fenster fallen, gesucht und projiziert werden.
3.	Klicken Sie auf der Seite **AUSGABE** auf **BEARBEITEN**.  Ändern Sie die Ausgabetabelle von PassthroughReadings in AvgReadings, und klicken Sie auf das Häkchensymbol.

4.	Klicken Sie auf der Seite **DASHBOARD** auf **START**.

##Anzeigen der Auftragsausgabe

1.	Stellen Sie in Visual Studio oder SQL Server Management Studio eine Verbindung mit der SQL-Datenbank her, und führen Sie die folgende Abfrage aus:

		SELECT * from AvgReadings

2.	Sie sehen die Datensätze in 5-Sekunden-Intervallen, die die Durchschnittstemperatur und Anzahl der Ereignisse für jedes Gerät zeigen: 

	![][img.stream.analytics.job.output2]
 
3.	 Führen Sie die BasicEventHubSample-Anwendung erneut aus, um die Verarbeitung der Ereignisse durch den derzeit ausgeführten Auftrag fortzusetzen.







##<a name="nextsteps"></a>Nächste Schritte
In diesem Lernprogramm haben Sie gelernt, wie Stream Analytics zur Verarbeitung von Wetterdaten verwendet wird. Weitere Informationen finden Sie in den folgenden Artikeln:


- [Einführung in Azure Stream Analytics][stream.analytics.introduction]
- [Azure Stream Analytics-Entwicklerhandbuch][stream.analytics.developer.guide]
- [Skalieren von Azure Stream Analytics-Aufträgen][stream.analytics.scale]
- [Einschränkungen und bekannte Probleme von Azure Stream Analytics][stream.analytics.limitations]
- [Azure Stream Analytics-Abfragesprachreferenz][stream.analytics.query.language.reference]
- [Azure Stream Analytics Management REST API Referenz][stream.analytics.rest.api.reference]




[img.stream.analytics.event.hub.client.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHClientOuput.png
[img.stream.analytics.event.hub.shared.access.policy.config]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHSharedAccessPolicyConfig.png
[img.stream.analytics.job.output2]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput2.png
[img.stream.analytics.job.output1]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput1.png
[img.stream.analytics.config.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureOutput.png
[img.stream.analytics.config.input]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureInput.png



[img.get.started.flowchart]: ./media/stream-analytics-get-started/StreamAnalytics.get.started.flowchart.png
[img.job.quick.create]: ./media/stream-analytics-get-started/StreamAnalytics.quick.create.png
[img.stream.analytics.portal.button]: ./media/stream-analytics-get-started/StreamAnalyticsPortalButton.png
[img.event.hub.policy.configure]: ./media/stream-analytics-get-started/StreamAnalytics.Event.Hub.policy.png
[img.create.table]: ./media/stream-analytics-get-started/StreamAnalytics.create.table.png
[img.stream.analytics.job.output]: ./media/stream-analytics-get-started/StreamAnalytics.job.output.png
[img.stream.analytics.operation.logs]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.png
[img.stream.analytics.operation.log.details]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.details.png


[azure.sql.database.firewall]: http://msdn.microsoft.com/en-us/library/azure/ee621782.aspx
[azure.event.hubs.documentation]: http://azure.microsoft.com/en-us/services/event-hubs/
[azure.sql.database.documentation]: http://azure.microsoft.com/en-us/services/sql-database/

[sql.database.introduction]: http://azure.microsoft.com/en-us/services/sql-database/
[event.hubs.introduction]: http://azure.microsoft.com/en-us/services/event-hubs/
[azure.blob.storage]: http://azure.microsoft.com/en-us/documentation/services/storage/
[azure.sdk.net]: ../dotnet-sdk/

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.scale]: ../stream-analytics-scale-jobs/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093




[azure.management.portal]: https://manage.windowsazure.com

