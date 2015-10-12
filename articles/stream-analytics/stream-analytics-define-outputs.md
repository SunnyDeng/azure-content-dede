<properties 
	pageTitle="Definieren von Ausgaben | Microsoft Azure" 
	description="Grundlegendes zu Stream Analytics-Ausgaben" 
	keywords="big data analytics,cloud service,internet of things,managed service,stream processing,streaming analytics,streaming data"
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
	ms.date="09/29/2015" 
	ms.author="jeffstok"/>

# Grundlegendes zu Stream Analytics-Ausgaben

Beim Erstellen eines Stream Analytics-Auftrags muss unter anderem beachtet werden, wie die Ausgabe des Auftrags genutzt (konsumiert) wird. Wie sehen sich die Consumer der Datentransformation die Ergebnisse des Stream Analytics-Auftrags an? Welche Tools verwenden die Consumer, um die Ausgabe zu analysieren? Ist Beibehaltung von Daten oder Data Warehousing erforderlich?

Um verschiedene Anwendungsmuster zu aktivieren, stellt Azure Stream Analytics sieben verschiedene Methoden zum Speichern und Anzeigen von Auftragsausgaben bereit. Unterstützte Ausgaben sind: SQL-Datenbank, Blobspeicher, Event Hubs, Service Bus-Warteschlangen, Service Bus-Themen, Power BI und Tabellenspeicher. Dadurch wird sowohl einfaches Anzeigen von Auftragsausgabe als auch Flexibilität bei der Nutzung und Speicherung der Auftragsausgabe für Data Warehousing und andere Zwecke erreicht.


## SQL-Datenbank ##

[Azure SQL-Datenbank](http://azure.microsoft.com/services/sql-database/) kann als Ausgabe für relationale Daten oder für Anwendungen verwendet werden, die auf Inhalten aufsetzen, die in einer relationalen Datenbank gehostet werden. Stream Analytics-Aufträge werden in eine vorhandene Tabelle in einer Azure SQL-Datenbank geschrieben. Beachten Sie, dass das Tabellenschema genau den Feldern und deren Typen entsprechen muss, die aus Ihrem Auftrag ausgegeben werden. Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer SQL-Datenbank-Ausgabe.

| Eigenschaftenname | Beschreibung |
|---------------|-------------|
| Ausgabealias | Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Datenbank weiterzuleiten. |
| Datenbank | Der Name der Datenbank, an die Sie die Ausgabe senden. |
| Servername | Der Servername der SQL-Datenbank. |
| Benutzername | Der Benutzername, der Schreibzugriff auf die Datenbank hat. |
| Kennwort | Das Kennwort, um eine Verbindung zur Datenbank herzustellen. |
| Tabelle | Der Name der Tabelle, in die die Ausgabe geschrieben wird. Beim Tabellennamen muss die Groß-/Kleinschreibung beachtet werden. Das Schema dieser Tabelle sollte genau der Anzahl Felder und ihrer Typen entsprechen, die von der Auftragsausgabe generiert werden. |

## Blob-Speicher ##

Blobspeicher bietet eine kostengünstige und skalierbare Lösung zum Speichern von großen Mengen unstrukturierter Daten in der Cloud. Eine Einführung in Azure-Blobspeicher und dessen Nutzung finden Sie in der Dokumentation unter [Verwenden des Blob-Speichers mit .NET](./articles/storage-dotnet-how-to-use-blobs.md).

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Blobausgabe.

<table>
<tbody>
<tr>
<td>EIGENSCHAFTENNAME</td>
<td>BESCHREIBUNG</td>
</tr>
<tr>
<td>Ausgabealias</td>
<td>Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Blobspeicher weiterzuleiten.</td>
</tr>
<tr>
<td>Speicherkonto</td>
<td>Der Name des Speicherkontos, an das Sie die Ausgabe senden.</td>
</tr>
<tr>
<td>Speicherkontoschlüssel</td>
<td>Der geheime Schlüssel, der dem Speicherkonto zugeordnet ist.</td>
</tr>
<tr>
<td>Speichercontainer</td>
<td>Container stellen eine logische Gruppierung für Blobs bereit, die im Microsoft Azure-Blobdienst gespeichert sind. Wenn Sie ein Blob in den Blobdienst hochladen, müssen Sie einen Container für das Blob angeben.</td>
</tr>
<tr>
<td>Präfixmusters des Pfads [optional]</td>
<td>Der Dateipfad, mit dem Ihre Blobs im angegebenen Container geschrieben werden.<BR>In dem Pfad können Sie mindestens eine Instanz der beiden folgenden Variablen verwenden, um die Frequenz anzugeben, in der Blobs geschrieben werden:<BR>{date}, {time}<BR>Beispiel 1: cluster1/logs/{date}/{time}<BR>Beispiel 2: cluster1/logs/{date}</td>
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
<td>Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt.</td>
</tr>
<tr>
<td>Codieren</td>
<td>Beim CSV- oder JSON-Format muss eine Codierung angegeben werden. Das einzige derzeit unterstützte Codierungsformat ist UTF-8.</td>
</tr>
<tr>
<td>Trennzeichen</td>
<td>Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der CSV-Daten. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich.</td>
</tr>
<tr>
<td>Format</td>
<td>Gilt nur für die JSON-Serialisierung. "Separate Zeile" gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. "Array" gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird.</td>
</tr>
</tbody>
</table>

## Event Hub

[Event Hubs](https://azure.microsoft.com/services/event-hubs/) ist ein hoch skalierbarer Veröffentlichen-Abonnieren-Ereigniserfasser. Er kann mehrere Millionen Ereignisse pro Sekunde erfassen. Eine Verwendung eines Event Hubs als Ausgabe ergibt sich, wenn die Ausgabe eines Stream Analytics-Auftrags die Eingabe eines anderen Streamingauftrags ist.

Es gibt einige Parameter, die erforderlich sind, um Event Hub-Datenströme als Ausgabe zu konfigurieren.

| Eigenschaftenname | Beschreibung |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ausgabealias | Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Event Hub weiterzuleiten. |
| Service Bus- Namespace | Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Service Bus-Namespace erstellt. |
| Event Hub | Der Name Ihrer Event Hub-Ausgabe. |
| Event Hub-Richtlinienname | Die Richtlinie für den gemeinsamen Zugriff, die auf der Registerkarte "Event Hub-Konfiguration" erstellt werden kann. Jede Richtlinie für den gemeinsamen Zugriff verfügt über einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Event Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. |
| Partitionsschlüsselspalte [optional] | Diese Spalte enthält den Partitionsschlüssel für die Event Hub-Ausgabe. |
| Ereignisserialisierungsformat | Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren | Bei CSV und JSON ist UTF-8 gegenwärtig das einzige unterstützte Codierungsformat. |
| Trennzeichen | Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich. |
| Format | Gilt nur für den JSON-Typ. "Separate Zeile" gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. "Array" gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. |
## Power BI

[Power BI](https://powerbi.microsoft.com/) kann als Ausgabe für einen Stream Analytics-Auftrag verwendet werden, um Stream Analytics-Benutzern eine umfassende Visualisierungsumgebung bereitzustellen. Diese Funktionalität kann für betriebliche Dashboards, Erstellung von Berichten und metrikgesteuerte Berichterstellung verwendet werden.

> [AZURE.NOTE]Zurzeit wird die Erstellung und Konfiguration von Power BI-Ausgaben im Azure-Vorschauportal nicht unterstützt.

### Autorisieren eines Power BI-Kontos

1.	Wenn Power BI im Azure-Verwaltungsportal als Ausgabe ausgewählt ist, werden Sie aufgefordert, einen vorhandenen Power BI-Benutzer zu autorisieren oder ein neues Power BI-Konto zu erstellen.  

    ![Autorisieren von Power BI-Benutzern](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2.	Erstellen Sie ein neues Konto, wenn Sie noch keines haben, und klicken Sie dann auf "Jetzt autorisieren". Es wird ein Bildschirm ähnlich dem folgenden angezeigt.

    ![Azure-Konto Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3.	In diesem Schritt geben Sie das Geschäfts- oder Schulkonto für die Autorisierung der Power BI-Ausgabe an. Wenn Sie noch nicht für Power  BI angemeldet sind, wählen Sie "Jetzt anmelden" aus. Das für Power BI verwendete Geschäfts- oder Schulkonto kann sich vom Azure-Abonnementkonto unterscheiden, bei dem Sie derzeit angemeldet sind.

### Konfigurieren der Eigenschaften der Power BI-Ausgabe

Sobald Sie das Power BI-Konto authentifiziert haben, können Sie die Eigenschaften für die Power BI-Ausgabe konfigurieren. Die folgende Tabelle enthält eine Liste von Eigenschaftennamen und der entsprechenden Beschreibung zum Konfigurieren der Power BI-Ausgabe.

| Eigenschaftenname | Beschreibung |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ausgabealias | Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Power BI-Ausgabe weiterzuleiten. |
| Datasetname | Geben Sie einen Datasetnamen an, der für die Power BI-Ausgabe verwendet werden soll. |
| Tabellenname | Geben Sie einen Tabellennamen unter dem Dataset der Power BI-Ausgabe ein. Derzeit darf die Power BI-Ausgabe von Stream Analytics-Aufträgen nur eine Tabelle pro Dataset aufweisen. |
| Gruppenname | Um die gemeinsame Nutzung von Daten mit anderen Power BI-Benutzern zu ermöglichen, müssen Sie Daten in Gruppen schreiben. Sie können Gruppen in Ihrem Power BI-Konto auswählen. Wählen Sie alternativ "Arbeitsbereich", wenn Sie nicht in eine Gruppe schreiben möchten. Zum Aktualisieren einer vorhandenen Gruppe muss die Power BI-Authentifizierung erneuert werden. |

> [AZURE.NOTE]Erstellen Sie das Dataset und die Tabelle nicht explizit im Power BI-Dashboard. Das Dataset und die Tabelle werden automatisch ausgefüllt, wenn der Auftrag gestartet wird und wenn der Auftrag damit beginnt, Ausgaben in Power BI zu speisen. Wenn die Auftragsabfrage keine Ergebnisse generiert, werden die Datasets und die Tabelle nicht erstellt. Wenn Power BI bereits über ein Dataset und eine Tabelle mit demselben Namen verfügt, der in diesem Stream Analytics-Auftrag angegeben wurde, beachten Sie bitte, dass die vorhandenen Daten überschrieben werden.

### Erneuern der Power BI-Autorisierung

Aufgrund einer vorübergehenden Einschränkung muss das Authentifizierungstoken alle 90 Tage manuell für sämtliche Aufträge mit Power BI-Ausgabe aktualisiert werden. Darüber hinaus muss Ihr Power BI-Konto erneut authentifiziert werden, wenn das Kennwort seit der Erstellung oder letzten Authentifizierung Ihres Auftrags geändert wurde. Dieses Problem zeigt sich daran, dass keine Auftragsausgabe erfolgt und in den Vorgangsprotokollen ein Benutzerauthentifizierungsfehler angezeigt wird:

  ![Power BI-Aktualisierungstoken-Fehler](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Um dieses Problem zu beheben, halten Sie den laufenden Auftrag an, und wechseln Sie zur Power BI-Ausgabe. Klicken Sie auf den Link "Autorisierung erneuern", und starten Sie den Auftrag ab dem letzten Anhaltepunkt neu, um Datenverlust zu vermeiden.

  ![Power BI-Erneuerungsautorisierung](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## Table Storage

[Azure-Tabellenspeicher](./articles/storage-introduction.md) bietet einen hoch verfügbaren, in hohem Maße skalierbaren Speicher, sodass eine Anwendung automatisch an die Bedürfnisse der Benutzer angepasst werden kann. Tabellenspeicher ist Microsofts NoSQL-Schlüssel-/Attributspeicher, der für strukturierte Daten genutzt werden kann, die weniger Einschränkungen hinsichtlich des Schemas haben. Azure-Tabellenspeicher kann zum Speichern von Daten für dauerhafte Archivierung und effizienten Abruf verwendet werden.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Tabellenausgabe.

| Eigenschaftenname | Beschreibung |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ausgabealias | Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Tabellenspeicher weiterzuleiten. |
| Speicherkonto | Der Name des Speicherkontos, an das Sie die Ausgabe senden. |
| Speicherkontoschlüssel | Der Zugriffsschlüssel, der dem Speicherkonto zugeordnet ist. |
| Tabellenname | Der Name der Tabelle. Die Tabelle wird erstellt, wenn sie noch nicht vorhanden ist. |
| Partitionsschlüssel | Der Name der Ausgabespalte mit dem Partitionsschlüssel. Der Partitionsschlüssel ist ein eindeutiger Bezeichner für die Partition innerhalb einer Tabelle, die den ersten Teil des Primärschlüssels einer Entität bildet. Dabei handelt es sich um einen Zeichenfolgenwert, der bis zu 1 KB groß sein kann. |
| Zeilenschlüssel | Der Name der Ausgabespalte, die den Zeilenschlüssel enthält. Der Zeilenschlüssel ist ein eindeutiger Bezeichner für eine Entität innerhalb einer Partition. Sie bildet den zweiten Teil des Primärschlüssels einer Entität. Der Zeilenschlüssel ist ein Zeichenfolgenwert, der bis zu 1 KB groß sein kann. |
| Batchgröße | Dies ist die Anzahl von Datensätzen für einen Batchvorgang. In der Regel ist die Standardeinstellung für die meisten Aufträge ausreichend. Ausführlichere Informationen zum Ändern dieser Einstellung finden Sie unter [TableBatchOperation-Klasse](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx). |

## Service Bus-Warteschlangen

[Service Bus-Warteschlangen](https://msdn.microsoft.com/library/azure/hh367516.aspx) bieten eine FIFO-Nachrichtenzustellung (First In, First Out) an einen Consumer oder an mehrere konkurrierende Consumer. Typischerweise wird erwartet, dass Nachrichten in der zeitlichen Reihenfolge von den Consumern empfangen und verarbeitet werden, in der sie der Warteschlange hinzugefügt wurden, und jede Nachricht wird nur von einem Nachrichtenconsumer empfangen und verarbeitet.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Warteschlangenausgabe.

| Eigenschaftenname | Beschreibung |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ausgabealias | Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Service Bus-Warteschlange weiterzuleiten. |
| Service Bus- Namespace | Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. |
| Warteschlangenname | Der Name der Service Bus-Warteschlange. |
| Name der Warteschlangenrichtlinie | Beim Erstellen einer Warteschlange können Sie auf der Registerkarte "Warteschlange konfigurieren" Richtlinien für den gemeinsamen Zugriff erstellen. Jede Richtlinie für den gemeinsamen Zugriff umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Schlüssel der Warteschlangenrichtlinie | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. |
| Ereignisserialisierungsformat | Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren | Bei CSV und JSON ist UTF-8 gegenwärtig das einzige unterstützte Codierungsformat. |
| Trennzeichen | Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich. |
| Format | Gilt nur für den JSON-Typ. "Separate Zeile" gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. "Array" gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. |

## Service Bus-Themen

Während Service Bus-Warteschlangen eine 1: 1-Kommunikationsmethode vom Absender zum Empfänger bereitstellen, bieten [Service Bus-Themen](https://msdn.microsoft.com/library/azure/hh367516.aspx) eine 1: n-Form der Kommunikation.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Tabellenausgabe.

| Eigenschaftenname | Beschreibung |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ausgabealias | Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an dieses Service Bus-Thema weiterzuleiten. |
| Service Bus- Namespace | Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Service Bus-Namespace erstellt. |
| Themenname | Themen sind Messagingentitäten, vergleichbar mit Event Hubs und Warteschlangen. Sie wurden für die Erfassung von Ereignisstreams von einer Reihe unterschiedlicher Geräte und Dienste entwickelt. Wenn ein Thema erstellt wird, wird ihm auch ein bestimmter Name zugewiesen. Die an ein Thema gesendeten Nachrichten sind nur verfügbar, wenn ein Abonnement erstellt wurde. Stellen Sie daher sicher, dass es mindestens ein Abonnement unter dem Thema gibt. |
| Name der Themenrichtlinie | Beim Erstellen eines Themas können Sie auf der Registerkarte "Thema konfigurieren" Richtlinien für den gemeinsamen Zugriff erstellen. Jede Richtlinie für den gemeinsamen Zugriff verfügt über einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Schlüssel der Themenrichtlinie | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. |
| Ereignisserialisierungsformat | Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren | Beim CSV- oder JSON-Format muss eine Codierung angegeben werden. Das einzige derzeit unterstützte Codierungsformat ist UTF-8. |
| Trennzeichen | Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich. |

## DocumentDB

[Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) ist ein vollständig verwalteter NoSQL-Dokumentendatenbankdienst, der Abfragen und Transaktionen über schemafreie Daten, vorhersagbare und zuverlässige Leistung sowie schnelle Entwicklung bietet.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer DocumentDB-Ausgabe.

<table>
<tbody>
<tr>
<td>EIGENSCHAFTENNAME</td>
<td>BESCHREIBUNG</td>
</tr>
<tr>
<td>Kontoname</td>
<td>Der Name des DocumentDB-Kontos. Dies kann auch der Endpunkt für das Konto sein.</td>
</tr>
<tr>
<td>Kontoschlüssel</td>
<td>Der gemeinsame Zugriffsschlüssel für das DocumentDB-Konto.</td>
</tr>
<tr>
<td>Datenbank</td>
<td>Der Name der DocumentDB-Datenbank.</td>
</tr>
<tr>
<td>Muster des Sammlungsnamen</td>
<td>Das Sammlungsnamenmuster für die zu verwendenden Sammlungen. Das Sammlungsnamenformat kann mit dem optionalen Token {partition} gebildet werden, wobei Partitionen bei 0 beginnen.<BR>Beispiel: Die Eingaben sind zulässig:<BR>MyCollection{partition}<BR>MyCollection<BR>Beachten Sie, dass Sammlungen vorhanden sein müssen, bevor der Stream Analytics-Auftrag gestartet wird, und nicht automatisch erstellt werden.</td>
</tr>
<tr>
<td>Partitionsschlüssel</td>
<td>Der Name des Felds in Ausgabeereignissen, das zur Angabe des Schlüssels für die Partitionierung der Ausgabe über Sammlungen hinweg verwendet wird.</td>
</tr>
<tr>
<td>Dokument-ID</td>
<td>Der Name des Felds in Ausgabeereignissen, das zur Angabe des Primärschlüssels verwendet wird, auf dem Einfüge- und Updatevorgänge basieren.</td>
</tr>
</tbody>
</table>


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

<!---HONumber=Oct15_HO1-->