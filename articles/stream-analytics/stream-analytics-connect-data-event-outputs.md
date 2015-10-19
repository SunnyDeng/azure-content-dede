<properties 
	pageTitle="Erstellen von Stream Analytics-Ausgaben | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Verbindung mit Ausgabezielen für Stream Analytics-Lösungen herstellen und diese konfigurieren." 
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
	ms.date="10/05/2015" 
	ms.author="jeffstok"/>

# Erstellen von Stream Analytics-Ausgaben

## Grundlegendes zu Stream Analytics-Ausgaben ##
---
Beim Erstellen eines Stream Analytics-Auftrags ist einer der zu beachtenden Aspekte, wie die Ausgabe des Auftrags genutzt (konsumiert) wird. Wie sehen sich die Consumer der Datentransformation die Ergebnisse des Stream Analytics-Auftrags an? Welche Tools verwenden die Consumer, um die Ausgabe zu analysieren? Ist Beibehaltung von Daten oder Data Warehousing erforderlich?

Azure Stream Analytics stellt sieben verschiedene Methoden zum Speichern und Anzeigen von Auftragsausgaben bereit: SQL-Datenbank, BLOB-Speicher, Event Hubs, Service Bus-Warteschlangen, Service Bus-Themen, Power BI und Tabellenspeicher. Dadurch wird sowohl einfaches Anzeigen von Auftragsausgabe als auch Flexibilität bei der Nutzung und Speicherung der Auftragsausgabe für Data Warehousing und andere Zwecke erreicht.

## Verwenden einer SQL-Datenbank als Ausgabe ##
---
Eine SQL-Datenbank kann als Ausgabe für relationale Daten oder für Anwendungen verwendet werden, die auf Inhalten aufsetzen, die in einer relationalen Datenbank gehostet werden. Weitere Informationen zu Azure SQL-Datenbanken, finden Sie unter [Azure SQL-Datenbank](http://azure.microsoft.com/services/sql-database/).

### Parameter ###

Damit Sie eine SQL-Datenbank verwenden können, benötigen Sie die folgenden Informationen über die SQL-Datenbank:

1. Servername
2. Datenbankname
3. Eine gültige Kombination aus Benutzername und Kennwort
4. Name der Ausgabetabelle. Diese Tabelle muss bereits vorhanden sein, sie wird nicht vom Auftrag erstellt.

### Hinzufügen einer SQL-Datenbank als Ausgabe ###

![Grafik1][graphic1]

Wechseln Sie zur Registerkarte "Ausgaben" des Auftrags, klicken Sie auf den Befehl **AUSGABE HINZUFÜGEN**, und klicken Sie dann auf "Weiter".

![Grafik2][graphic2]

Wählen Sie **SQL-Datenbank** als Ausgabe aus.

![Grafik3][graphic3]

Geben Sie die Datenbankinformationen auf der nächsten Seite ein. Der Ausgabealias ist der Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Datenbank weiterzuleiten. Ist nur eine einzige Ausgabe vorhanden, ist "output" der Standardalias.

![Grafik4][graphic4]

Befindet sich die Datenbank im selben Abonnement wie der Stream Analytics-Auftrag, ist die Option "SQL-Datenbank aus aktuellem Abonnement verwenden" verfügbar. Wählen Sie in der Dropdownliste die gewünschte Datenbank aus.

![Grafik5][graphic5]

Klicken Sie auf "Weiter", um diese Ausgabe hinzuzufügen. Es sollten zwei Vorgänge gestartet werden, wobei der erste das Hinzufügen der Ausgabe ist.

![Grafik6][graphic6]

Der zweite Vorgang ist ein Testen der Verbindung. Azure Stream Analytics versucht, eine Verbindung mit der SQL-Datenbank herzustellen, und überprüft, ob die eingegebenen Anmeldeinformationen korrekt sind und ob auf die Tabelle zugegriffen werden kann.

## Verwenden von BLOB-Speicher als Ausgabe ##
---
Eine Einführung in Azure-BLOB-Speicher und dessen Nutzung finden Sie in der Dokumentation unter [Verwenden des Blob-Speichers mit .NET](./articles/storage/storage-dotnet-how-to-use-blobs.md).

### Parameter ###

Damit Sie eine BLOB-Speicher-Ausgabe verwenden können, sind die folgenden Informationen erforderlich:

1. Befindet sich das Speicherkonto in einem anderen Abonnement als der Streamingauftrag, werden Felder angezeigt, in denen der "Speicherkontoname" und der "Speicherkontoschlüssel" angegeben werden können.
2. Der Containername
3. Das Präfix für Dateinamen
4. Das Serialisierungsformat, das für die Daten verwendet wird (Avro, CSV, JSON)

### Hinzufügen von BLOB-Speicher als Ausgabe ###

Wählen Sie "BLOB-Speicher" als Ausgabeziel aus.

![Grafik20][graphic20]

Geben Sie dann, wie nachstehend gezeigt, die Details ein:

![Grafik21][graphic21]

## Verwenden eines Event Hubs als Ausgabe ##
---
### Übersicht ###
 
Event Hubs sind hochgradig skalierbare Ereigniserfasser, die am häufigsten für die Datenerfassung von Stream Analytics verwendet werden. Ihre robuste Verarbeitung einer großen Anzahl von Ereignissen bedingt, dass sie sich auch perfekt für Auftragsausgabe eignen. Eine Verwendung eines Event Hubs als Ausgabe ergibt sich, wenn die Ausgabe eines Stream Analytics-Auftrags die Eingabe eines anderen Streamingauftrags ist. Weitere Informationen zu Event Hubs finden Sie im Portal für [Event Hubs](https://azure.microsoft.com/services/event-hubs/ "Event Hubs").
 
### Parameter ###

Es gibt einige Parameter, die für Event Hub-Datenströme konfiguriert werden müssen.

1. Service Bus-Namespace: Service Bus-Namespace des Event Hubs. Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Wenn ein neuer Event Hub erstellt wird, wird auch ein Service Bus-Namespace erstellt. 
2. Event Hub-Name: Name des Event Hubs. Dies ist der Name, der beim Erstellen des neuen Event Hubs angegeben wurde. 
3. Event Hub-Richtlinienname: Der Name der Richtlinie für den gemeinsamen Zugriff auf den Event Hub. Die Richtlinie für den gemeinsamen Zugriff auf einen Event Hub wird auf der Registerkarte "Konfigurieren" eingerichtet. Jede SAS-Richtlinie (Richtlinie für den gemeinsamen Zugriff) hat einen Namen, festgelegte Berechtigungen und generierte Zugriffsschlüssel.
4. Event Hub-Richtlinienschlüssel: Der primäre oder sekundäre Schlüssel der Richtlinie für den gemeinsamen Zugriff auf den Event Hub.  
5. Partitionsschlüsselspalte: Optionaler Parameter für die Event Hub-Ausgabe. Diese Spalte enthält den Partitionsschlüssel für die Event Hub-Ausgabe.

### Hinzufügen eines Event Hubs als Ausgabe ###

1. Klicken Sie am oberen Seitenrand auf **Ausgabe** und dann auf **Ausgabe hinzufügen**. Wählen Sie "Event Hub" als die Ausgabeoption aus, und klicken Sie auf die Nach-rechts-Schaltfläche am unteren Rand des Fensters.

    ![Grafik38][graphic38]

2. Geben Sie die relevanten Informationen in die Felder für die Ausgabe ein, und klicken Sie danach auf die Nach-rechts-Schaltfläche am unteren Rand des Fensters.

    ![Grafik36][graphic36]

3. Vergewissern Sie sich, dass die Felder "Ereignisserialisierungsformat", "Codierung" und "Format" auf die richtigen Werte festgelegt sind, und klicken Sie auf das Kontrollkästchen, um die Aktion abzuschließen.

    ![Grafik37][graphic37]

## Verwenden von Power BI als Ausgabe ##
---
### Übersicht ###
Power BI kann als Ausgabe für einen Stream Analytics-Auftrag verwendet werden, um Stream Analytics-Benutzern eine umfassende Visualisierungsumgebung bereitzustellen. Diese Funktionalität kann für betriebliche Dashboards, Erstellung von Berichten und metrikgesteuerte Berichterstellung verwendet werden. Weitere Informationen zu Power BI finden Sie auf der [Power BI](https://powerbi.microsoft.com/)-Website.

### Parameter ###

Es gibt einige Parameter, die für eine Power BI-Ausgabe konfiguriert werden müssen.

1. Ausgabealias – ein als Ausgabealias angezeigter Name, auf den sich einfach verweisen lässt. Dieser Ausgabealias ist besonders nützlich, wenn festgelegt ist, dass es mehrere Ausgaben für einen Auftrag geben soll. In diesem Fall wird in Ihrer Abfrage auf diesen Alias verwiesen. Verwenden Sie beispielsweise den Ausgabealiaswert = "AusgPBI".
2. Datasetname – geben Sie einen Datasetnamen ein, der für die Power BI-Ausgabe verwendet werden soll. Verwenden Sie beispielsweise "PBIdemo".
2. Tabellenname – geben Sie einen Tabellennamen unter dem Dataset der Power BI-Ausgabe ein. Verwenden Sie beispielsweise "PBIdemo". **Derzeit darf die Power BI-Ausgabe von Stream Analytics-Aufträgen nur eine Tabelle pro Dataset aufweisen.**

### Hinzufügen von Power BI als Ausgabe ###

1.  Klicken Sie am oberen Seitenrand auf **Ausgabe** und dann auf **Ausgabe hinzufügen**. Wählen Sie Power BI als Ausgabeoption aus.

    ![Grafik22][graphic22]

2.  Es wird ein Bildschirm ähnlich dem folgenden angezeigt.

    ![Grafik23][graphic23]

3.  In diesem Schritt geben Sie das Geschäfts- oder Schulkonto für die Autorisierung der Power BI-Ausgabe an. Wenn Sie noch nicht für Power BI angemeldet sind, wählen Sie **Jetzt anmelden** aus.
4.  Danach wird ein Bildschirm ähnlich dem folgenden angezeigt.

    ![Grafik24][graphic24]


>	[AZURE.NOTE] One should not explicitly create the dataset and table in the Power BI dashboard. The dataset and table will be automatically populated when the job is started and the job starts pumping output into Power BI. Note that if the job query doesn’t return any results, the dataset and table will not be created. Also be aware that if Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data will be overwritten.

*	Klicken Sie auf **OK** und anschließend auf **Verbindung testen**. Die Ausgabekonfiguration ist nun abgeschlossen.


## Verwenden von Azure-Tabellenspeicher für eine Ausgabe ##
---
Der Tabellenspeicher bietet eine hoch verfügbare, in hohem Maße skalierbare Speicherung, so dass eine Anwendung automatisch an die Bedürfnisse der Benutzer angepasst werden kann. Tabellenspeicher ist Microsofts NoSQL-Schlüssel-/Attributspeicher, der für strukturierte Daten genutzt werden kann, die weniger Einschränkungen hinsichtlich des Schemas haben. Azure-Tabellenspeicher kann zum Speichern von Daten für dauerhafte Archivierung und effizienten Abruf verwendet werden. Weitere Informationen zu Azure-Tabellenspeicher finden Sie unter [Azure-Tabellenspeicher](./articles/storage/storage-introduction.md).

### Parameter ###

Damit Sie einen Azure-Tabellenspeicher verwenden können, sind folgende Informationen erforderlich:

1. Der Name des Speicherkontos (wenn sich dieser Speicher in einem anderen Abonnement als der Streamingauftrag befindet)
2. Der Speicherkontoschlüssel (wenn sich dieser Speicher in einem anderen Abonnement als der Streamingauftrag befindet)
3. Name der Ausgabetabelle (wird erstellt, sofern nicht vorhanden)
4. Partitionsschlüssel (erforderlich)
5. Zeilenschlüssel

Informationen zum optimalen Entwurf von Partitions- und Zeilenschlüssel finden Sie im Artikel unter [Entwerfen einer Strategie für die skalierbare Partitionierung des Azure-Tabellenspeichers](https://msdn.microsoft.com/library/azure/hh508997.aspx).

### Hinzufügen von Azure-Tabellenspeicher als Ausgabe ###

![Grafik11][graphic11]

Wechseln Sie zur Registerkarte "Ausgaben" des Auftrags, klicken Sie auf den Befehl **AUSGABE HINZUFÜGEN**, und klicken Sie dann auf "Weiter".

![Grafik12][graphic12]

Wählen Sie **Tabellenspeicher** als Ausgabe aus.

![Grafik13][graphic13]

Geben Sie auf der nächsten Seite die Informationen zur Azure-Tabelle ein. Der Ausgabealias ist der Name, der in der Abfrage verwendet werden kann, um die Abfrageausgabe an diese Tabelle weiterzuleiten.

![Grafik14][graphic14]

![Grafik15][graphic15]

"Batchgröße" ist die Anzahl von Datensätzen für einen Batchvorgang. In der Regel ist die Standardeinstellung für die meisten Aufträge ausreichend. Ausführlichere Informationen zum Ändern dieser Einstellung finden Sie unter [TableBatchOperation-Klasse](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx).

Gibt es ein Azure Storage-Konto in dem Abonnement, das zum Erstellen des Auftrags verwendet wird, wählen Sie "Speicherkonto aus dem aktuellen Abonnement verwenden" und dann in der Dropdownliste das Speicherkonto aus.

Klicken Sie auf "Weiter", um diese Ausgabe hinzuzufügen. Es sollten zwei Vorgänge gestartet werden, wobei der erste das Hinzufügen der Ausgabe ist.

![Grafik16][graphic16]

Der zweite dient dem Testen der Verbindung. Azure Stream Analytics versucht, eine Verbindung mit dem Speicherkonto herzustellen, und überprüft, ob die eingegebenen Anmeldeinformationen richtig sind.

## Service Bus-Warteschlangen ##
---
### Einführung in die Konzepte für Service Bus-Warteschlangen ###
Service Bus-Warteschlangen bieten eine FIFO-Nachrichtenzustellung (First In, First Out) an einen Consumer oder an mehrere konkurrierende Consumer. Typischerweise wird erwartet, dass Nachrichten in der zeitlichen Reihenfolge von den Consumern empfangen und verarbeitet werden, in der sie der Warteschlange hinzugefügt wurden, und jede Nachricht wird nur von einem Nachrichtenconsumer empfangen und verarbeitet.

Weitere Informationen zu Service Bus-Warteschlangen finden Sie unter [Service Bus-Warteschlangen, -Themen und -Abonnements](https://msdn.microsoft.com/library/azure/hh367516.aspx "Service Bus-Warteschlangen, -Themen und -Abonnements") sowie unter [An Introduction to Service Bus Queues](http://blogs.msdn.com/b/appfabric/archive/2011/05/17/an-introduction-to-service-bus-queues.aspx "Eine Einführung in Service Bus-Warteschlangen").

### Parameter ###

Damit Sie eine Service Bus-Warteschlangenausgabe verwenden können, sind die folgenden Informationen erforderlich:

1. Ausgabealias – ein als Ausgabealias angezeigter Name, auf den sich einfach verweisen lässt. Dieser Ausgabealias ist besonders nützlich, wenn festgelegt ist, dass es mehrere Ausgaben für einen Auftrag geben soll. In diesem Fall wird in der Auftragsabfrage auf diesen Alias verwiesen.
2. Der Namespace und der Service Bus-Name.
3. Warteschlangenname - Warteschlangen sind Messagingentitäten, vergleichbar mit Event Hubs und Themen. Sie wurden für die Erfassung von Ereignisstreams von einer Reihe unterschiedlicher Geräte und Dienste entwickelt. Wenn eine Warteschlange erstellt wird, wird ihr auch ein bestimmter Name zugewiesen.
4. Das Serialisierungsformat, das für die Daten verwendet wird (Avro, CSV, JSON)

### Hinzufügen von Service Bus-Warteschlangen als Ausgabe ###

![Grafik31][graphic31]

Geben Sie dann, wie nachstehend gezeigt, die Details ein, und wählen Sie "Weiter" aus.

![Grafik32][graphic32]

Vergewissern Sie sich, dass das Datenformat und die Serialisierung richtig sind.

![Grafik33][graphic33]

## Service Bus-Themen ##
---
### Einführung in die Konzepte für Service Bus-Themen ###
Während Service Bus-Warteschlangen eine 1: 1-Kommunikationsmethode vom Absender zum Empfänger bereitstellen, bieten Service Bus Themen eine 1: n-Form der Kommunikation.

Weitere Informationen zu Service Bus-Themen finden Sie unter [Service Bus-Warteschlangen, -Themen und -Abonnements](https://msdn.microsoft.com/library/azure/hh367516.aspx "Service Bus-Warteschlangen, -Themen und -Abonnements") sowie unter [An Introduction to Service Bus Topics](http://blogs.msdn.com/b/appfabric/archive/2011/05/25/an-introduction-to-service-bus-topics.aspx "Eine Einführung in Service Bus-Themen").

### Parameter ###

Damit Sie eine Service Bus-Themenausgabe verwenden können, sind die folgenden Informationen erforderlich:

1. Ausgabealias – ein als Ausgabealias angezeigter Name, auf den sich einfach verweisen lässt. Dieser Ausgabealias ist besonders nützlich, wenn festgelegt ist, dass es mehrere Ausgaben für einen Auftrag geben soll. In diesem Fall wird in Ihrer Abfrage auf diesen Alias verwiesen.
2. Der Namespace und der Service Bus-Name.
3. Themenname - Themen sind Messagingentitäten, vergleichbar mit Event Hubs und Warteschlangen. Sie wurden für die Erfassung von Ereignisstreams von einer Reihe unterschiedlicher Geräte und Dienste entwickelt. Wenn ein Thema erstellt wird, wird ihm auch ein bestimmter Name zugewiesen. Die an ein Thema gesendeten Nachrichten sind nur verfügbar, wenn ein Abonnement erstellt wurde. Stellen Sie daher sicher, dass es mindestens ein Abonnement unter dem Thema gibt.
4. Das Serialisierungsformat, das für die Daten verwendet wird (Avro, CSV, JSON)

### Hinzufügen von Service Bus-Themen als Ausgabe ###

Wählen Sie "Service Bus-Thema" als Ausgabeziel aus.

![Grafik34][graphic34]

Geben Sie dann, wie nachstehend gezeigt, die Details ein, und wählen Sie "Weiter" aus.

![Grafik35][graphic35]

Vergewissern Sie sich, dass das Datenformat und die Serialisierung richtig sind.

![Grafik33][graphic33]


## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-outputs/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-outputs/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-outputs/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-outputs/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-outputs/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-outputs/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-outputs/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-outputs/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-outputs/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-outputs/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-outputs/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-outputs/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-outputs/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-outputs/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-outputs/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-outputs/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-outputs/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-outputs/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-outputs/19-stream-analytics-connect-data-event-input-output.png
[graphic20]: ./media/stream-analytics-connect-data-event-outputs/20-stream-analytics-connect-data-event-input-output.png
[graphic21]: ./media/stream-analytics-connect-data-event-outputs/21-stream-analytics-connect-data-event-input-output.png
[graphic22]: ./media/stream-analytics-connect-data-event-outputs/22-stream-analytics-connect-data-event-input-output.png
[graphic23]: ./media/stream-analytics-connect-data-event-outputs/23-stream-analytics-connect-data-event-input-output.png
[graphic24]: ./media/stream-analytics-connect-data-event-outputs/24-stream-analytics-connect-data-event-input-output.png
[graphic25]: ./media/stream-analytics-connect-data-event-outputs/25-stream-analytics-connect-data-event-input-output.png
[graphic26]: ./media/stream-analytics-connect-data-event-outputs/26-stream-analytics-connect-data-event-input-output.png
[graphic27]: ./media/stream-analytics-connect-data-event-outputs/27-stream-analytics-connect-data-event-input-output.png
[graphic28]: ./media/stream-analytics-connect-data-event-outputs/28-stream-analytics-connect-data-event-input-output.png
[graphic29]: ./media/stream-analytics-connect-data-event-outputs/29-stream-analytics-connect-data-event-input-output.png
[graphic30]: ./media/stream-analytics-connect-data-event-outputs/30-stream-analytics-connect-data-event-input-output.png
[graphic31]: ./media/stream-analytics-connect-data-event-outputs/31-stream-analytics-connect-data-event-input-output.png
[graphic32]: ./media/stream-analytics-connect-data-event-outputs/32-stream-analytics-connect-data-event-input-output.png
[graphic33]: ./media/stream-analytics-connect-data-event-outputs/33-stream-analytics-connect-data-event-input-output.png
[graphic34]: ./media/stream-analytics-connect-data-event-outputs/34-stream-analytics-connect-data-event-input-output.png
[graphic35]: ./media/stream-analytics-connect-data-event-outputs/35-stream-analytics-connect-data-event-input-output.png
[graphic36]: ./media/stream-analytics-connect-data-event-outputs/36-stream-analytics-connect-data-event-input-output.png
[graphic37]: ./media/stream-analytics-connect-data-event-outputs/37-stream-analytics-connect-data-event-input-output.png
[graphic38]: ./media/stream-analytics-connect-data-event-outputs/38-stream-analytics-connect-data-event-input-output.png

<!---HONumber=Oct15_HO2-->