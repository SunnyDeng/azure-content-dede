<properties title="Azure Stream Analytics limitations in the preview release" pageTitle=" Einschränkungen von Stream Analytics in der Vorabversion | Azure" description="Erfahren Sie mehr über die Einschränkungen in der öffentlichen Vorschauversion von Azure Stream Analytics-Aufträgen." metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

#Einschränkungen und bekannte Probleme der Vorabversion von Azure Stream Analytics

Dieses Dokument beschreibt die Einschränkungen und bekannten Probleme von [Azure Stream Analytics][stream.analytics.documentation] in der Vorabversion.  In den meisten Fällen gibt es diese Grenzen, um ein frühes Feedback zu erhalten, oder sie basieren auf aktuellen Kapazitätsbeschränkungen. 
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##Themen in diesem Artikel
+ [Einschränkungen](#limitations) 
+ [Versionshinweise und bekannte Probleme](#knownissues)
+ [Nächste Schritte]

##<a name="limitations"></a> Einschränkungen

###Regionale Verfügbarkeit
Stream Analytics-Aufträge können in der Vorabversion nur in den Regionen "Zentral-USA" und "Westeuropa" bereitgestellt werden.

###Skalieren 
**Streaming-Einheit-Kontingent**

Aufgrund der aktuellen Kapazitätsbeschränkungen wird ein Kontingent von 12 Streaming-Einheiten pro Region und Abonnement erzwungen. Weitere Informationen finden Sie unter [Skalieren von Azure Stream Analytics-Aufträgen][stream.analytics.scale.jobs]. Wenn Sie diese Einschränkung in Ihrem Unternehmen lockern möchten, rufen Sie den [Microsoft-Support][microsoft.support] an. Wir werden uns bemühen, Ihre Wünsche im Rahmen der Einschränkungen des öffentlichen Angebots zu erfüllen. 

**Streaming-Einheit-Auslastung**

In dieser Vorabversion kann die Anzahl der für einen Auftrag bereitgestellten Streaming-Einheiten manchmal größer sein als die ausgewählte oder in Rechnung gestellte Menge.  Streaming-Einheiten werden außerdem nicht gedrosselt, was bedeutet, dass die beobachtete Leistung je nach Verfügbarkeit der Verarbeitungsressourcen möglicherweise höher als garantiert ist.

**Partitionsschlüssel**

Beim Skalieren Ihrer Abfrage mit PARTITION BY muss das zu partitionierende Feld "PartitionId" sein.  Die Partitionierung anhand anderer benutzerdefinierter Felder wird in einer zukünftigen Version aktiviert.
Details zum Skalieren Ihres Auftrags finden Sie unter [Skalieren von Azure Stream Analytics-Aufträgen][stream.analytics.scale.jobs].

###Eingaben



**Zeichencodierung**

Für die Eingabequellen CSV und JSON ist UTF-8 das einzige unterstützte Codierungsformat.


###Abfragekomplexität
Die maximale Anzahl von unterstützten Aggregatfunktionen in einer Stream Analytics-Auftragsabfragedefinition ist sieben.

###Lebenszyklusverwaltung

**Auftrags-Upgrade**

Zu diesem Zeitpunkt unterstützt Stream Analytics keine Echtzeitbearbeitungen der Definition oder Konfiguration eines laufenden Auftrags.  Um die Eingabe, Ausgabe, Abfrage, Skalierung oder Konfiguration eines laufenden Auftrags zu ändern, müssen Sie den Auftrag zunächst beenden.

**Beenden und erneutes Starten eines Auftrags**

Beim Beenden eines Auftrags wird kein Status zum Auftragsfortschritt beibehalten, d. h., es gibt derzeit keine Möglichkeit, einen neu gestarteten Auftrag an dem Punkt fortzusetzen, an dem er zuletzt beendet wurde.  Dies ist eine Einschränkung, die in einer zukünftigen Version behoben wird.  Bewährte Methoden zum Starten und Beenden von Aufträgen finden Sie im [Azure Stream Analytics-Entwicklerhandbuch][stream.analytics.developer.guide]. 

###Überwachung
Einige Metriken im Hinblick auf Auftragsnutzung und Leistung, wie z. B. Latenz, sind nicht in der Vorabversion verfügbar.  Die Vorabversion blendet den Auftragsdurchsatz auch nur im Hinblick auf die Ereignisanzahl, nicht die Größe ein.

##<a name="knownissues"></a>Versionshinweise / Bekannte Probleme

Dies enthält eine Liste der bekannten Probleme von Azure Stream Analytics. Dieser Abschnitt ändert sich, wenn wir Elemente aus der Liste entfernen, neue Probleme hinzukommen oder wenn wir mehr über bestehende Probleme erfahren.


###Erhöhte Event Hub-Berechtigungen erforderlich
Zu diesem Zeitpunkt benötigt Stream Analytics eine freigegebene Zugriffsrichtlinie mit Verwaltungsberechtigungen für Event Hub-Eingabequellen und -Ausgabequellen.

###Verzögerung bei der Azure-Speicherkontokonfiguration
Wenn Sie einen Stream Analytics-Auftrag zum ersten Mal in einer Region erstellen, werden Sie aufgefordert, ein neues Speicherkonto zu erstellen oder ein vorhandenes Konto anzugeben, um Stream Analytics-Aufträge in dieser Region zu überwachen.  Aufgrund der Wartezeit bei der Konfigurationsüberwachung werden Sie beim Erstellen eines anderen Stream Analytics-Auftrags in derselben Region innerhalb von 30 Minuten aufgefordert, ein zweites Speicherkonto anzugeben, anstatt das zuletzt konfigurierte in der Überwachungsspeicherkonto-Dropdown-Liste anzuzeigen.  Um die Erstellung eines unnötigen Speicherkontos zu vermeiden, warten Sie 30 Minuten nach der erstmaligen Erstellung eines Auftrags in einer Region, bis Sie zusätzliche Aufträge in dieser Region bereitstellen. 

###Aufträge verwenden die Standardverbrauchergruppe für Event Hub
Wenn ein Event Hub als Eingabe angegeben ist, verwenden Stream Analytics-Aufträge die Standardverbrauchergruppe, um Daten aus dem Event Hub zu erfassen.  Wenn dies ohne zusätzliche Konfiguration durchgeführt wird, bedeutet das, dass kein anderer Empfänger auf das Event Hub zugreifen kann.  Um ein Event Hub für mehrere Empfänger zu aktivieren, müssen zusätzliche Verbrauchergruppen konfiguriert werden.  Weitere Informationen finden Sie im [Event Hubs-Entwicklerhandbuch][azure.event.hubs.developer.guide].

###Hinzufügen einer Eingabe/Ausgabe - Event Hub 
Die dritte Seite der Dialogfelder "Eingabe hinzufügen" und "Ausgabe hinzufügen" für Event Hub-Datenquellen verfügt über eine Dropdownliste "Event Hub", die eine Liste mit Service Bus-Namespaces im aktuellen Abonnement und eine Option zum Herstellen einer Verbindung mit einem Event Hub in einem anderen Abonnement enthält.  Wenn Sie eine Verbindung mit einem Event Hub im selben Abonnement herstellen möchten, wählen Sie hier einen Service Bus-Namespace.  Wenn Sie eine Verbindung mit einem Event Hub außerhalb des Abonnements herstellen möchten, wählen Sie "Event Hub aus anderem Abonnement verwenden".  


###Mehrmaliger Verweis auf den gleichen Abfrageschritt nicht möglich
In dieser Vorabversion kann nicht mehr als einmal auf einen Unterabfrageschritt, der mit dem WITH-Schlüsselwort definiert ist, verwiesen werden.  Ein häufiges davon betroffenes Szenario ist eine Selbstverknüpfung mit Aliasen des gleichen Schritts.  Zur Umgehung dieses Verhaltens erstellen Sie zwei separate Schritte mit verschiedenen Namen und derselben Unterabfrage.

###Nicht unterstützte Typkonvertierungen führen zu NULL-Werten
Alle Ereigniswerte mit nicht unterstützten Typkonvertierungen im Abschnitt "Datentypen" der [Azure Stream Analytics-Abfragesprachreferenz][stream.analytics.query.language.reference] führen zu einem NULL-Wert.  In dieser Vorabversion findet keine Fehlerprotokollierung für diese Konvertierungsausnahmen statt. 

###Arbeitsspeicherproblem
Stream Analytics-Aufträge mit einem großen Toleranzfenster für unsortierte Ereignisse und/oder komplexe Abfragen, die eine große Statusmenge verwalten, führen dazu, dass nicht genügend Arbeitsspeicher verfügbar ist, wodurch der Auftrag neu gestartet wird. Die Start- und Beendigungsvorgänge sind in den Vorgangsprotokollen des Auftrags sichtbar.  Um dieses Verhalten zu vermeiden, skalieren Sie die Abfrage auf mehrere Partitionen.  In zukünftigen Versionen wird diese Einschränkung behoben werden, indem die Leistung von betroffenen Aufträgen verringert wird, anstatt sie neu zu starten.

###Leeres Event Hub-Shard in partitionierten Abfragen führt zu keiner Ausgabe
Beim Ausführen einer partitionierten Abfrage mit einer nicht partitionierten Unterabfrage als zweiten Schritt, generiert die Abfrage keine Ergebnisse, wenn eine der Event Hub-Partitionen für die Eingabe vollständig leer ist. Ein diesbezüglicher Fehler wirkt sich auf die Vorgangsprotokolle für den Auftrag aus.  Stellen Sie sicher, dass alle Event Hub-Partitionen jederzeit über eingehende Ereignisse verfügen, um dieses Problem zu vermeiden.

###Einschränkungen im Hinblick auf die SQL-Datenbank-Ereignismenge
Wenn Sie SQL-Datenbank als Ausgabenquelle verwenden, kann es für einen Stream Analytics-Auftrag bei einer sehr großen Ausgabedatenmenge zu einem Timeout kommen. Reduzieren Sie zum Beheben dieses Problems entweder die Ausgabemenge mithilfe von Aggregaten oder Join-Operatoren, oder wählen Sie stattdessen BLOB-Speicher oder Event Hub als Ausgabequelle aus.

###Große BLOB-Eingaben werden nicht unterstützt
Durch die Nutzung großer Dateien aus dem BLOB-Speicher können Stream Analytics-Aufträge abstürzen.  Um dieses Problem zu vermeiden, sorgen Sie dafür, dass jedes BLOB kleiner als 10 MB ist.

###LEFT OUTER JOIN wird nicht unterstützt
Der LEFT OUTER JOIN-Vorgang wird in der Stream Analytics-Abfragesprache aktiviert, aber nicht unterstützt.  Wenn eine Abfrage mit LEFT OUTER JOIN länger als ein paar Minuten ausgeführt wird, kommt es zu Arbeitsspeichernutzungsproblemen.  Wir empfehlen Ihnen, diesen Vorgang nicht für andere Szenarien als kurzlebige Abfrageexperimente zu verwenden.  Diese Einschränkung wird in einer zukünftigen Version behoben werden.


##<a name="nextsteps"></a>Nächste Schritte

- [Einführung in Azure Stream Analytics][stream.analytics.introduction]
- [Erste Schritte mit Azure Stream Analytics][stream.analytics.get.started]
- [Skalieren von Azure Stream Analytics-Aufträgen][stream.analytics.scale.jobs]
- [Einschränkungen und bekannte Probleme von Azure Stream Analytics][stream.analytics.limitations]
- [Azure Stream Analytics-Abfragesprachreferenz][stream.analytics.query.language.reference]
- [Azure Stream Analytics Management REST API Referenz][stream.analytics.rest.api.reference] 

<!--Anchors-->
[Einschränkungen]: #Limitations
[Versionshinweise und bekannte Probleme]: #Release-notes-and-known-issues
[Nächste Schritte]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.limitations]: ../stream-analytics-limitations/


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/en-us/library/azure/dn789972.aspx

[Link 1 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../virtual-machines-windows-tutorial/
[Link 2 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../web-sites-custom-domain-name/
[Link 3 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../storage-whatis-account/
