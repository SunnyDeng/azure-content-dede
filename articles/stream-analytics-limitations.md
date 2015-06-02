<properties 
	pageTitle="Einschränkungen von Stream Analytics in der Vorabversion | Azure" 
	description="Erfahren Sie mehr über die Einschränkungen in der öffentlichen Vorschauversion von Azure Stream Analytics-Aufträgen." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/05/2015"
	ms.author="jgao"/>

# Einschränkungen und bekannte Probleme der Vorabversion von Azure Stream Analytics

Dieses Dokument beschreibt die Einschränkungen und bekannten Probleme von [Azure Stream Analytics][stream.analytics.documentation] in der Vorabversion. In den meisten Fällen gibt es diese Einschränkungen, um ein frühes Feedback zu erhalten, oder sie basieren auf aktuellen Kapazitätsbeschränkungen.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Einschränkungen

### Regionale Verfügbarkeit
Stream Analytics-Aufträge können in der Vorabversion nur in den Regionen "Central US" und "West Europe" bereitgestellt werden.

### Skalieren 
**Streaming-Einheitenkontingent**

Aufgrund der aktuellen Kapazitätsbeschränkungen wird ein Kontingent von 12 Streaming-Einheiten pro Region und Abonnement durchgesetzt. Weitere Informationen finden Sie unter [Skalieren von Azure Stream Analytics-Aufträgen][stream.analytics.scale.jobs]. Wenn Sie diese Einschränkung in Ihrem Unternehmen lockern möchten, rufen Sie den [Microsoft-Support][microsoft.support] an. Wir werden uns bemühen, Ihre Wünsche im Rahmen der Einschränkungen des öffentlichen Angebots zu erfüllen.

**Streaming-Einheitenauslastung**

In dieser Vorabversion kann die Anzahl der für einen Auftrag bereitgestellten Streaming-Einheiten manchmal größer sein als die ausgewählte oder in Rechnung gestellte Menge. Streaming-Einheiten werden außerdem nicht gedrosselt, was bedeutet, dass die beobachtete Leistung je nach Verfügbarkeit der Verarbeitungsressourcen möglicherweise höher als garantiert ist.

**Partitionsschlüssel**

Beim Skalieren Ihrer Abfrage mit **Partition by** muss das zu partitionierende Feld **PartitionId** sein. Die Partitionierung anhand anderer benutzerdefinierter Felder wird in einer zukünftigen Version aktiviert. Details zum Skalieren Ihres Auftrags finden Sie unter [Skalieren von Azure Stream Analytics-Aufträgen][stream.analytics.scale.jobs].

### Eingaben



**Zeichencodierung**

Für die Eingabequellen CSV und JSON ist UTF-8 das einzige unterstützte Codierungsformat.


### Abfragekomplexität
Die maximale Anzahl von unterstützten Aggregatfunktionen in einer Stream Analytics-Auftragsabfragedefinition ist sieben.

### Anzahl der Abfragen
Für eine bestimmte Eingabequelle werden maximal fünf Abfragen unterstützt.


### Lebenszyklusverwaltung

**Auftragsupgrade**

Zum aktuellen Zeitpunkt unterstützt Stream Analytics keine Echtzeitbearbeitungen der Definition oder Konfiguration eines laufenden Auftrags. Um die Eingabe, Ausgabe, Abfrage, Skalierung oder Konfiguration eines laufenden Auftrags zu ändern, müssen Sie den Auftrag zunächst beenden.

**Beenden und erneutes Starten eines Auftrags**

Beim Beenden eines Auftrags wird kein Status zum Auftragsfortschritt beibehalten, d. h., es gibt derzeit keine Möglichkeit, einen neu gestarteten Auftrag an dem Punkt fortzusetzen, an dem er zuletzt beendet wurde. Dies ist eine Einschränkung, die in einer zukünftigen Version behoben wird. Bewährte Methoden zum Starten und Beenden von Aufträgen finden Sie im [Azure Stream Analytics-Entwicklerhandbuch][stream.analytics.developer.guide].

### Überwachung
Einige Metriken im Hinblick auf Auftragsnutzung und Leistung, wie z. B. Latenz, sind nicht in der Vorabversion verfügbar. Die Vorabversion blendet den Auftragsdurchsatz auch nur im Hinblick auf die Ereignisanzahl, nicht die Größe ein.

## Versionshinweise/bekannte Probleme

Dieser Abschnitt enthält eine Liste der bekannten Probleme von Azure Stream Analytics. Dieser Abschnitt ändert sich, wenn wir Elemente aus der Liste entfernen, neue Probleme hinzukommen oder wenn wir mehr über bestehende Probleme erfahren.


### Erhöhte Event Hub-Berechtigungen erforderlich
Zu diesem Zeitpunkt benötigt Stream Analytics eine freigegebene Zugriffsrichtlinie mit Verwaltungsberechtigungen für Event Hub-Eingabequellen und -Ausgabeziele.

### Verzögerung bei der Azure-Speicherkontokonfiguration
Wenn Sie einen Stream Analytics-Auftrag zum ersten Mal in einer Region erstellen, werden Sie aufgefordert, ein neues Speicherkonto zu erstellen oder ein vorhandenes Konto anzugeben, um Stream Analytics-Aufträge in dieser Region zu überwachen. Aufgrund der Wartezeit bei der Konfigurationsüberwachung werden Sie beim Erstellen eines anderen Stream Analytics-Auftrags in derselben Region innerhalb von 30 Minuten aufgefordert, ein zweites Speicherkonto anzugeben, anstatt das zuletzt konfigurierte in der Dropdownliste **Monitoring Storage Account** anzuzeigen. Um die Erstellung eines unnötigen Speicherkontos zu vermeiden, warten Sie 30 Minuten nach der erstmaligen Erstellung eines Auftrags in einer Region, bis Sie zusätzliche Aufträge in dieser Region bereitstellen.

### Die Aufträge sollten jeweils über eine eigene Event Hub-Verbrauchergruppe verfügen.
Jede Eingabe an einen Stream Analytics-Auftrag sollte für eine eigene Event Hub-Verbrauchergruppe konfiguriert werden. Wenn ein Auftrag Selbstverknüpfungen oder mehrere Ausgaben enthält, werden einige Eingaben möglicherweise häufiger gelesen als andere, wodurch die Gesamtzahl der Leser in einer einzelnen Verbrauchergruppe die für den Event Hub geltende Beschränkung auf fünf Leser pro Verbrauchergruppe überschreiten kann. In diesem Fall muss die Abfrage in mehrere Abfragen und Zwischenergebnisse aufgeteilt werden, die dann über zusätzliche Event Hubs weitergeleitet werden. Beachten Sie, dass darüber hinaus ein Limit von 20 Verbrauchergruppen pro Event Hub besteht. Weitere Informationen finden Sie im [Event Hubs-Event Hubs-Programmierhandbuch][azure.event.hubs.developer.guide].

### Hinzufügen einer Eingabe/Ausgabe – Event Hub 
Die dritte Seite der Dialogfelder **Eingabe hinzufügen** und **Ausgabe hinzufügen** für Event Hub-Quellen verfügt über die Dropdownliste **Event Hub**, die eine Liste mit Service Bus-Namespaces im aktuellen Abonnement und eine Option zum Herstellen einer Verbindung mit einem Event Hub in einem anderen Abonnement enthält. Wenn Sie eine Verbindung mit einem Event Hub im selben Abonnement herstellen möchten, wählen Sie hier einen Service Bus-Namespace. Wenn Sie eine Verbindung mit einem Event Hub außerhalb des Abonnements herstellen möchten, wählen Sie **Use Event Hub from Another Subscription** aus.


### Mehrmaliger Verweis auf den gleichen Abfrageschritt nicht möglich
In dieser Vorabversion kann nicht mehr als einmal auf einen Unterabfrageschritt, der mit dem **WITH**-Schlüsselwort definiert ist, verwiesen werden. Ein häufiges davon betroffenes Szenario ist eine Selbstverknüpfung mit Aliasen des gleichen Schritts. Zur Umgehung dieses Verhaltens erstellen Sie zwei separate Schritte mit verschiedenen Namen und derselben Unterabfrage.

### Nicht unterstützte Typkonvertierungen führen zu NULL-Werten
Alle Ereigniswerte mit nicht unterstützten Typkonvertierungen im Abschnitt "Datentypen" der [Stream Analytics Query Language Reference][stream.analytics.query.language.reference] (in englischer Sprache) führen zu einem NULL-Wert. In dieser Vorabversion findet keine Fehlerprotokollierung für diese Konvertierungsausnahmen statt.

### Arbeitsspeicherproblem
Stream Analytics-Aufträge mit großer Toleranz für unsortierte Ereignisse und/oder komplexe Abfragen, die eine große Statusmenge verwalten, führen dazu, dass nicht genügend Arbeitsspeicher verfügbar ist, wodurch der Auftrag neu gestartet wird. Die Start- und Beendigungsvorgänge sind in den Vorgangsprotokollen des Auftrags sichtbar. Um dieses Verhalten zu vermeiden, skalieren Sie die Abfrage auf mehrere Partitionen. In zukünftigen Versionen wird diese Einschränkung behoben werden, indem die Leistung von betroffenen Aufträgen verringert wird, anstatt sie neu zu starten.

### Leeres Event Hub-Shard in partitionierten Abfragen führt zu keiner Ausgabe
Beim Ausführen einer partitionierten Abfrage mit einer nicht partitionierten Unterabfrage als zweitem Schritt generiert die Abfrage keine Ergebnisse, wenn eine der Event Hub-Partitionen für die Eingabe vollständig leer ist. Ein diesbezüglicher Fehler wirkt sich auf die Vorgangsprotokolle für den Auftrag aus. Stellen Sie sicher, dass alle Event Hub-Partitionen jederzeit über eingehende Ereignisse verfügen, um dieses Problem zu vermeiden.

### Einschränkungen im Hinblick auf die SQL-Datenbank-Ereignismenge
Wenn Sie eine SQL-Datenbank als Ausgabeziel verwenden, kann es für einen Stream Analytics-Auftrag bei einer sehr großen Ausgabedatenmenge zu einem Timeout kommen. Reduzieren Sie zum Beheben dieses Problems entweder die Ausgabemenge mithilfe von Aggregaten oder Filter-Operatoren, oder wählen Sie stattdessen den Azure-BLOB-Speicher oder Event Hubs als Ausgabeziel aus.

### Große BLOB-Eingaben werden nicht unterstützt
Durch die Nutzung großer Dateien aus dem BLOB-Speicher können Stream Analytics-Aufträge abstürzen. Um dieses Problem zu vermeiden, sorgen Sie dafür, dass jedes Blob kleiner als 10 MB ist.

### Leerzeichen in Spaltenüberschriften führen zu Ausgabeeinträgen mit NULL-Wert
Stream Analytics schneidet Leerzeichen in Spaltenüberschriften nicht ab. Leerzeichen am Anfang oder Ende eines Spaltennamens führen in der Auftragsausgabe zu Einträgen mit NULL-Wert.


## Support
Zusätzlichen Support erhalten Sie im [Azure Stream Analytics-Forum](stream-analytics-forum.md).


## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](stream-analytics-query-language-reference.md)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](stream-analytics-rest-api-reference.md) 

<!--Anchors-->
[Limitations]: #Limitations
[Release notes and known issues]: #Release-notes-and-known-issues
[Next steps]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.limitations]: stream-analytics-limitations.md


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!--HONumber=54-->