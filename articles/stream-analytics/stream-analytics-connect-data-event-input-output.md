<properties 
	pageTitle="Herstellen von Verbindungen mit Ein- und Ausgaben | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Verbindung mit Eingabequellen und Ausgabezielen für Stream Analytics-Lösungen herstellen und diese konfigurieren." 
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
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#Herstellen von Verbindungen mit Ein- und Ausgaben
In diesem Dokument werden die verschiedenen Methoden zum Konfigurieren von Eingabequellen und Ausgabezielen für Stream Analytics-Lösungen beschrieben.

##Verwenden von SQL als Ausgabe

Sie können Azure-SQL-Datenbanken als Ausgabe für relationale Daten verwenden oder für Anwendungen, die von in einer Datenbank gehosteten Inhalten abhängig sind.

[http://azure.microsoft.com/services/sql-database/](http://azure.microsoft.com/services/sql-database/)

Um eine Azure-SQL-Datenbank verwenden zu können, benötigen Sie die folgenden Informationen über die Datenbank:

1. Servername
2. Datenbankname
3. Eine gültige Kombination aus Benutzername und Kennwort
4. Name der Ausgabetabelle

![Grafik1][graphic1]

Wechseln Sie zur Registerkarte "Ausgaben" des Projekts, klicken Sie auf den Befehl "AUSGABE HINZUFÜGEN", und klicken Sie dann auf Weiter.

![Grafik2][graphic2]


Wählen Sie "SQL-Datenbank" als Ausgabe aus.

![Grafik3][graphic3]

Geben Sie die Datenbankinformationen auf der nächsten Seite ein. Der Ausgabealias ist der Name, den Sie in Ihrer Abfrage zum Weiterleiten der Abfrageausgabe an diese Datenbank verwenden können. Der Standardalias bei einer Ausgabe ist "Ausgabe".

![Grafik4][graphic4]

Bei Verwendung einer Datenbank in dem von Ihnen verwendeten Abonnement können Sie "Use SQL Database from Current Subscription" und dann die Datenbank aus der Dropdownliste auswählen.

![Grafik5][graphic5]

Klicken Sie auf "Weiter", um diese Ausgabe hinzuzufügen. Es sollten zwei Vorgänge gestartet werden, wobei der erste das Hinzufügen der Ausgabe ist.

![Grafik6][graphic6]

Der zweite dient dem Testen der Verbindung. Azure Stream Analytics versucht, eine Verbindung mit der SQL-Datenbank herzustellen, und überprüft die eingegebenen Anmeldeinformationen und den Zugriff auf die Tabelle. Nach Abschluss sollte eine von zwei Meldungen angezeigt werden.

![Grafik7][graphic7]

![Grafik8][graphic8]



Bei der zweiten Meldung klicken Sie auf "DETAILS", um die Einzelheiten zum Fehler anzuzeigen.

![Grafik9][graphic9]

In diesem Beispiel waren die bereitgestellten Anmeldeinformationen nicht korrekt. Sie können die Anmeldeinformationen korrigieren und dann den Test erneut durchführen, indem Sie auf die Schaltfläche "VERBINDUNG TESTEN" klicken.

![Grafik10][graphic10]

##Verwenden von Event Hubs

###Übersicht
 
Event Hubs sind hochgradig skalierbare Ereigniserfasser, die am häufigsten für die Datenerfassung von Stream Analytics verwendet werden. Sie wurden für die Erfassung von Ereignisstreams von einer Reihe unterschiedlicher Geräte und Dienste entwickelt. Event Hubs und Stream Analytics bieten Kunden gemeinsam eine umfassende Lösung für Echtzeitanalysen – mit Event Hubs können Kunden Ereignisse in Azure in Echtzeit weiterleiten, die dann von Stream Analytics ebenfalls in Echtzeit verarbeitet werden. Kunden können z. B. Webklicks, Sensorenmesswerte oder Onlineprotokollereignisse auf Event Hubs veröffentlichen und dann Stream Analytics-Aufträge erstellen, die diese Event Hubs als Eingabedatenstrom für die Filterung, Aggregation und Zusammenführung in Echtzeit verwenden. Event Hubs können auch für den Datenausgang verwendet werden. Die häufigste Verwendung von Event Hubs als Ausgabe erfolgt bei der Ausgabe von Stream Analytics-Aufträgen an die Eingabe eines anderen Streamingauftrags.

###Verbrauchergruppen
Jede Eingabe an eine Stream Analytics-Auftrag sollte für eine eigene Event Hub-Verbrauchergruppe konfiguriert werden. Wenn ein Auftrag Selbstverknüpfungen oder mehrere Ausgaben enthält, werden einige Eingaben möglicherweise häufiger gelesen als andere, wodurch die Gesamtzahl der Leser in einer einzelnen Verbrauchergruppe die für den Event Hub geltende Beschränkung auf fünf Leser pro Verbrauchergruppe überschreiten kann. In diesem Fall muss die Abfrage in mehrere Abfragen und Zwischenergebnisse aufgeteilt werden, die dann über zusätzliche Event Hubs weitergeleitet werden. Beachten Sie, dass darüber hinaus ein Limit von 20 Verbrauchergruppen pro Event Hub. Weitere Informationen finden Sie im Event Hubs-Entwicklerhandbuch.

 
###Parameter
 
Es gibt einige Parameter für Event Hub-Datenströme, die Kunden konfigurieren müssen. Diese Parameter gelten für Eingabe- und Ausgabedatenströme von Event Hub, sofern nicht anders angegeben.

1. Service Bus-Namespace: Service Bus-Namespace des Event Hubs. Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Service Bus-Namespace erstellt. 
2. Event Hub-Name: Name des Event Hubs. Dies ist der Name, den Sie beim Erstellen des neuen Event Hubs angegeben haben. 
3. Event Hub-Richtlinienname: Der Name der Richtlinie für den gemeinsamen Zugriff auf den Event Hub. Die Richtlinie für den gemeinsamen Zugriff auf einen Event Hub wird auf der Registerkarte "Konfigurieren" eingerichtet. Jede Richtlinie für den gemeinsamen Zugriff umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel.
4. Event Hub-Richtlinienschlüssel: Der primäre oder sekundäre Schlüssel der Richtlinie für den gemeinsamen Zugriff auf den Event Hub.  
5. Event Hub-Verbrauchergruppe: Optionaler Parameter für Event Hub-Eingaben. Die Verbrauchergruppe für das Erfassen von Daten aus den Event Hub. Wenn nichts angegeben wird, verwenden Stream Analytics-Aufträge die Standardverbrauchergruppe, um Daten vom Event Hub zu erfassen. Es wird die Verwendung einer speziellen Verbrauchergruppe für jeden Stream Analytics-Auftrag empfohlen.

Partitionsschlüsselspalte: Optionaler Parameter für die Event Hub-Ausgabe. Die Datenattributspalte wird als Partitionsschlüssel für die Event Hub-Ausgabe verwendet.

##Verwenden von Azure-Tabellenausgaben

Sie können auch Azure-Tabellen für strukturierte Daten mit weniger Einschränkungen für das Schema verwenden. Der Azure-Tabellenspeicher kann zum Speichern von Daten für die dauerhafte Archivierung und den effizienten Abruf verwendet werden. Weitere Informationen finden Sie unter [Einführung in Azure Storage](http://azure.microsoft.com/storage-introduction/).
 
Um einen Azure-Tabellenspeicher verwenden zu können, benötigen Sie die folgenden Informationen über die Tabelle:

1. Name des Speicherkontos (wenn dieser Speicher zu einem anderen Abonnement als der Streamingauftrag gehört)
2. Speicherkontoschlüssel (wenn dieser Speicher zu einem anderen Abonnement als der Streamingauftrag gehört)
3. Name der Ausgabetabelle (wird erstellt, sofern nicht vorhanden)
4. Partitionsschlüssel (erforderlich)
5.   Zeilenschlüssel (derzeit noch erforderlich, aufgrund von Kunden-Feedback als optional geplant)

Informationen zum optimalen Entwurf von Partitions- und Zeilenschlüssel finden Sie im Artikel unter [Entwerfen einer Strategie für die skalierbare Partitionierung des Azure-Tabellenspeichers](https://msdn.microsoft.com/library/azure/hh508997.aspx).


![Grafik11][graphic11]


Wechseln Sie zur Registerkarte "Ausgaben" des Projekts, klicken Sie auf den Befehl "AUSGABE HINZUFÜGEN", und klicken Sie dann auf Weiter.


![Grafik12][graphic12]


Wählen Sie als Ausgabe "Tabellenspeicher" aus.


![Grafik13][graphic13]


Geben Sie auf der nächsten Seite die Informationen zur Azure-Tabelle ein. Der Ausgabealias ist der Name, den Sie in Ihrer Abfrage zum Weiterleiten der Abfrageausgabe an diese Tabelle verwenden können.


![Grafik14][graphic14]
![Grafik15][graphic15]

Die Batchgröße ist die Anzahl der Datensätze für einen Batchvorgang. Behalten Sie den Standardwert bei, wenn Sie nicht damit vertraut sind. Weitere Informationen finden Sie unter [https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx).


Bei Verwendung von Azure Storage in dem von Ihnen verwendeten Abonnement können Sie "Use Storage Account from Current Subscription" und dann das Speicherkonto aus der Dropdownliste auswählen.

Klicken Sie auf "Weiter", um diese Ausgabe hinzuzufügen. Es sollten zwei Vorgänge gestartet werden, wobei der erste das Hinzufügen der Ausgabe ist.

![Grafik16][graphic16]

Der zweite dient dem Testen der Verbindung. Azure Stream Analytics versucht, eine Verbindung mit dem Speicherkonto herzustellen, und überprüft die eingegebenen Anmeldeinformationen. Nach Abschluss sollte eine von zwei Meldungen angezeigt werden.

![Grafik17][graphic17]

Bei der zweiten Meldung klicken Sie auf "DETAILS", um die Einzelheiten zum Fehler anzuzeigen.

![Grafik18][graphic18]

In diesem Beispiel waren die bereitgestellten Anmeldeinformationen nicht korrekt. Sie können die Anmeldeinformationen korrigieren und dann den Test erneut durchführen, indem Sie auf die Schaltfläche "VERBINDUNG TESTEN" klicken.

![Grafik19][graphic19]

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-de/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-input-output/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-input-output/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-input-output/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-input-output/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-input-output/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-input-output/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-input-output/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-input-output/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-input-output/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-input-output/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-input-output/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-input-output/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-input-output/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-input-output/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-input-output/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-input-output/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-input-output/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-input-output/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-input-output/19-stream-analytics-connect-data-event-input-output.png
 

<!---HONumber=62-->