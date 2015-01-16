<properties title="Introduction to Azure Stream Analytics" pageTitle="Einführung in Stream Analytics | Azure" description="Grundlegendes zu Azure Stream Analytics" metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />


# Einführung in Azure Stream Analytics

Azure Stream Analytics ist ein vollständig verwalteter Dienst, der eine geringe Latenz, Hochverfügbarkeit und eine skalierbare komplexe Ereignisverarbeitung durch das Streaming von Daten in der Cloud bietet.

# Themen in diesem Artikel

+ [Motivation und Überblick](#motivation) 
+ [Wichtige Funktionen](#capabilities)
+ [Unternehmerische Motivation für die Wahl von Azure Stream Analytics](#decision)
+ [Szenarios und Anwendungsfälle](#scenarios)
+ [Nächste Schritte](#neststeps)


##<a name="motivation"></a>Motivation und Überblick 

Große Datenmengen werden heute mit hoher Geschwindigkeit über das Netz übertragen.  Organisationen, die diese Daten in Echtzeit verarbeiten und nutzen können, verbessern die Effizienz unter Umständen erheblich und setzen sich auf dem Markt von den Mitbewerbern ab.  Szenarios für die Datenstromanalyse in Echtzeit finden sich in allen Branchen: personalisierte Aktienhandelsanalysen und Warnungen von Finanzdienstleister in Echtzeit; Betrugserkennung in Echtzeit; Daten- und Identitätsschutzdienste; zuverlässige Erfassung und Analyse von Daten, die von Sensoren und Aktuatoren generiert wurden und in physische Objekte eingebettet sind (IoT); Webklickstream-Analyse und CRM-Anwendungen, die Warnungen ausgeben, wenn die Benutzerfreundlichkeit innerhalb eines Zeitraums beeinträchtigt wird.  Unternehmen suchen die flexibelste, zuverlässigste und kostengünstigste Möglichkeit, um eine solche Streaming-Datenanalyse in Echtzeit durchzuführen, um in einer stark durch Wettbewerb geprägten, modernen Geschäftswelt erfolgreich zu sein.  

Die Erstellung von Verarbeitungssystemen für Streamingdaten ist eine komplexe Aufgabe.  Streaming-Vorgänge, wie Korrelationen und Aggregationen, müssen nicht nur effizient implementiert werden, sondern darüber hinaus skalierbar und fehlertolerant sein.  Dazu kommen zusätzliche betriebliche Herausforderungen, darunter die Bereitstellung, das Debuggen und die Überwachung.  Die Kosten im Zusammenhang mit der Erstellung und Verwaltung einer solchen Lösung steigen schnell.  Größere Unternehmen haben sich damit abgefunden, solch hohen Kosten für benutzerdefinierte integrierte Lösungen zu bezahlen, während kleinere Unternehmen die Gelegenheit oft aufgrund der hohen Einstiegsbarriere und des damit verbundenen riesigen Kostenaufwands verpassen.  Azure Stream Analytics löst diese Herausforderungen.

Azure Stream Analytics ist ein vollständig verwalteter Datenstromberechnungsdienst in Echtzeit, der in Microsoft Azure gehostet wird und der eine sehr ausfallsichere und skalierbare, komplexe Ereignisverarbeitung von Streamingdaten mit geringer Latenz bietet.  Azure Stream Analytics ermöglicht es Entwicklern, Datenströme mühelos mit historischen Datensätzen zu kombinieren oder auf Daten zu verweisen, um schnell und einfach Geschäftsinformationen abzuleiten.  

Mit wenigen Mausklicks im Azure-Portal können Kunden einen Streaming-Auftrag mit einer SQL-ähnlichen Sprache erstellen, um Transformationen festzulegen und die Skalierung und Geschwindigkeit der gesamten Streaming-Pipeline zu überwachen. Der Dienst kann mühelos von einigen wenigen Kilobyte auf ein Gigabyte oder mehr an verarbeiteten Ereignissen pro Sekunde skaliert werden.  Bei den meisten anderen derzeit verfügbaren Streaming-Lösungen müssen Kunden komplexen, benutzerdefinierten Code schreiben. Mit Azure Stream Analytics können Kunden jedoch einen einfachen, deklarativen, vertrauten SQL-Code schreiben.

Azure Stream Analytics bietet verschiedene Operatoren von einfachen Filtern bis hin zu komplexen Korrelationen.  Die Definition von zeitbasierten Operationen im Fenstermodus, z. B. Fensteraggregate, die Korrelation von mehreren Datenströmen zum Erkennen von Mustern wie Sequenzen oder sogar der Vergleich aktueller Bedingungen mit historischen Werten und Modellen kann in wenigen Minuten mit dem einfachen Satz von SQL-ähnlichen Stream Analytics-Abfragesprachoperatoren durchgeführt werden.  Die Angabe einer Streaming-Pipeline ist so einfach wie die Konfiguration ihrer Eingaben und Ausgaben und die Bereitstellung einer SQL-ähnlichen Abfrage, die die gewünschten Transformationen beschreibt.  Während dies für die meisten einfachen Fällen genügt, profitieren komplexere Szenarios mit einer höheren Skalierung von der Konfigurierbarkeit von Stream Analytics.  Benutzer können bestimmen, wie viel Rechenleistung für jeden Schritt der Pipeline zum Erreichen des gewünschten Spitzendurchsatzes zugeteilt werden soll.

Azure Stream Analytics stellt derzeit direkt eine Verbindung zum Azure Event Hub für die Streamaufnahme und zu Azure-BLOBs für historische Daten her.  Mithilfe des Bereichs an Eingabe- und Ausgabeschnittstellen von Azure Event Hub kann Azure Stream Analytics leicht mit anderen Datenquellen und Verarbeitungsmodulen integriert werden, ohne den Streaming-Charakter der Berechnung zu verlieren.  Daten können aus Stream Analytics in Azure-Speicher geschrieben werden, um dann später erneut als eine Reihe von Ereignissen verarbeitet oder in anderen Formularen der Batch-Analyse mit HDInsight verwendet zu werden.  Azure Stream Analytics nutzt die jahrelange Arbeit von Microsoft Research bei der Entwicklung von hoch optimierten Streaming-Modulen für zeitempfindliche Verarbeitung sowie Sprachintegrationen für deren intuitive Spezifikationen.  Azure Stream Analytics wurde mithilfe der Open Source Community Hadoop, YARN und REEF für eine skalierte Verarbeitung entwickelt.


##<a name="capabilities"></a>Wichtige Funktionen

###Einfache Bedienung
Stream Analytics unterstützt ein einfaches, deklaratives Abfragemodell zum Beschreiben der Transformationen.  Zur Optimierung der Benutzerfreundlichkeit haben wir uns für eine SQL-Variante als DSL entscheiden und wir halten unsere Kunden von den erheblichen technischen Komplexitäten fern, die unserem Streaming-System zugrunde liegen.  Mithilfe der Stream Analytics-Abfragesprache können Sie schnell und einfach zeitliche Funktionen implementieren, darunter zeitlich basierte Verknüpfungen, Fensteraggregate, zeitliche Filter sowie andere allgemeine Vorgänge wie Verknüpfungen, Aggregate, Projektionen, Filter usw.

Während wir die Semantik von SQL auf verschiedene Weise erweitert haben, die für SQL-Benutzer intuitiv ist, halten wir die syntaktischen Grenzen des SQL-Standards ein, um die Implementierung und Toolverwendung zu erleichtern. Weitere Informationen zu unserer Abfragesprache finden Sie im Abschnitt "Ressourcen für Azure Stream Analytics".

###Skalierbar
Stream Analytics kann einen hohen Ereignisdurchsatz von bis zu 1 GB/s verarbeiten. Die Integration in Azure Event Hubs ermöglicht es der Lösung, Millionen Ereignisse pro Sekunde zu erfassen, die von verbundenen Geräten, aus Klickstreams, Protokolldateien usw. stammen. Um dies zu erreichen, nutzen wir die Partitionierungsmöglichkeit von Event Hubs, woraus sich Partitionen von bis zu 1 MB/s ergeben. Stream Analytics bietet während der Verarbeitung dieser aufgenommenen Ereignisse Unterstützung für die Partitionierung entlang der horizontalen und vertikalen Achse.  Ein Benutzer kann die Berechnung in eine Anzahl von logischen Schritten innerhalb der Abfragedefinition partitionieren, die jeweils die Möglichkeit haben, weiter in datenparallele Ausführungselemente partitioniert zu werden.  Im Laufe der Zeit wird Stream Analytics automatisch auf der Basis der Ereigniserfassungsrate, der Verarbeitungskomplexität und der erwarteten Wartezeiten skaliert, um Benutzern die Anpassung ihrer Arbeitsauslastung zu ermöglichen.  Weitere Informationen zum Implementieren der Skalierbarkeit finden Sie in den Links im Abschnitt "Ressourcen".  

###Zuverlässige, wiederholbare und schnelle Wiederherstellung
Stream Analytics garantiert keinen Datenverlust und bietet Business Continuity bei Knotenfehler dank der integrierten Wiederherstellungs- und Prüfpunktfunktionen.  Dies wird durch die Client-Anker-Modellarchitektur von Stream Analytics ermöglicht.  Der Dienst wurde so angelegt, um den Zustand zur Optimierung der Wiederaufnahme bei Knotenfehlern, erneuten Berechnungen und der Cacheausgabe zu erhalten, um Downstream-Fehler effizient zu behandeln.  

Mit der Möglichkeit, den Zustand intern beizubehalten, kann der Dienst wiederholbare Ergebnisse bereitstellen, um Ereignisse zu archivieren und die Verarbeitung in Zukunft erneut anzuwenden, wobei dieselben Ergebnisse erhalten werden.  Diese Funktion ermöglicht es Kunden, zeitlich zurückzugehen und Berechnungen für Szenarios wie Ursachenanalyse und Was-wäre-wenn-Analysen zu untersuchen.  Diese Funktionen bieten gemeinsam eine schnelle Wiederherstellung bei Verarbeitungsknotenfehlern, wobei der verlorene Zustand schnell erneut verarbeitet wird, indem ein früherer Zeitpunkt aufgerufen wird. 

###Niedrige Latenz
Stream Analytics wurde derzeit für beobachtbare End-to-End-Wartezeiten im Bereich von unter einer Sekunde optimiert.  Dadurch kann das System die Verarbeitung kontinuierlich mit einer hohen Durchsatzrate durchführen. Das Kommunikationsmodell ist ein Pull-basiertes, adaptives Batchverarbeitungsmodell, das auf konfigurierten Timeouts und Größenbeschränkungen basiert.  Daher werden Ereignisse und andere Datensätze im Stapel verarbeitet, bis die Grenzwerte erreicht sind.  Daher ist das System auch bei hohem Durchsatz in der Lage, niedrige Wartezeiten bereitzustellen.

###Verweisdaten
Stream Analytics bietet Benutzern die Möglichkeit, Verweisdaten festzulegen und zu verwenden.  Dabei kann es sich um historische Daten oder um Daten handeln, die im Lauf der Zeit weniger häufig geändert werden.  Das System vereinfacht die Verwendung von Verweisdaten, damit sie wie alle anderen eingehenden Ereignisstreams mit anderen in Echtzeit erfassten Ereignisstreams verbunden werden, um Transformationen durchzuführen.  Weitere Informationen für die Erstellung und Verwendung von Verweisdaten finden Sie im Abschnitt "Ressourcen".



##<a name="decision"></a> Unternehmerische Motivation für die Wahl von Azure Stream Analytics

###Kosten
Der Stream Analytics-Dienst wurde optimiert, damit Benutzer zu Beginn von den geringen Kosten profitieren können und um dann Echtzeit-Analyselösungen zu verwalten.  Für den Dienst gilt ein nutzungsbasiertes Zahlungsmodell.  Die Nutzung wird auf Grundlage der verarbeiteten Ereignismenge und dem Umfang der im Cluster bereitgestellten Rechenleistung berechnet, um die jeweiligen Streaming-Aufträge zu verarbeiten.  

###Betriebsbereit in wenigen Minuten
Mit Stream Analytics steht Ihnen eine hoch skalierbare Echtzeit-Ereignisverarbeitungslösung ohne Hardware oder andere Vorlaufkosten, ohne zeitaufwändige Installation oder Einrichtung zur Verfügung.  Azure übernimmt dies für Sie.  Die Anwendung ist in wenigen Minuten einsatzbereit, indem Sie einfach den Dienst über das Azure-Portal abonnieren.  Die benutzerfreundliche Oberfläche im Portal führt Sie schrittweise durch ein Schnellstart-Handbuch, um Ihre Eingabequelle und den Ausgabespeicher zu konfigurieren und zu testen, und bietet einen einfachen Abfrage-Editor mit Funktionen zur automatischen Vervollständigung und mit Empfehlungen.  

###Schnelle Entwicklung
Stream Analytics hilft Ihnen, Reibung und Komplexität bei der Entwicklung von Analysefunktionen für skalierte, verteilte Systeme zu verringern.  Entwickler beschreiben ihre gewünschte Transformation in einer SQL-basierten Abfragesprache. Das System übernimmt automatisch die Verteilung der Arbeitsauslastung für Skalierung, Leistung und Ausfallsicherheit, wodurch die Notwendigkeit einer prozeduralen Programmierung beseitigt wird, die für die meisten Streamingverarbeitungslösungen erforderlich ist.  Durch die integrierten, vordefinierten Funktionen und ein benutzerfreundliches Portal bietet Stream Analytics Entwicklern auf höchster Ebene eine Möglichkeit, eine Verarbeitung in Echtzeit schnell und mühelos durchzuführen, ohne sich über solche Dinge wie Infrastrukturbeschaffung, laufende Wartung und Skalierung Sorgen zu machen. 

##<a name="scenarios"></a>Szenarios und Anwendungsfälle

###Echtzeit-Analyse für das Internet der Dinge (IoT)
Da Geräte immer intelligenter werden und immer mehr Geräte mit Kommunikationsmöglichkeiten erstellt werden, entwickeln sich die Erwartungen im Hinblick auf die kommerzielle und private Nutzung der von diesen Geräten generierten und erfassten Daten. Es wird erwartet, dass die Daten aufgrund der schieren Masse schnell kombiniert und verarbeitet werden können, um einen besseren Einblick in die Umgebung und die regelmäßig verwendeten Geräte zu erhalten. Ein kanonisches IoT-Szenario kann anhand des Verkaufsautomatsbeispiels beschrieben werden. Die Verkaufsautomaten senden regelmäßig Daten wie z. B. Produktbestand, Status, Temperatur usw. an ein Feld-Gateway (wenn der Verkaufsautomat nicht IP-fähig ist) oder an ein Cloud-Gateway (bei IP-Fähigkeit) für die Aufnahme in das System. Der eingehende Datenstrom wird verarbeitet und umgewandelt, sodass die berechnete Ausgabe sofort über die Gateways an das Gerät zurück übertragen werden kann, um eine entsprechende Maßnahme zu ergreifen.  Wenn der Automat beispielsweise überhitzt, muss sich das Gerät möglicherweise selbst neu starten oder automatisch ohne Benutzereingriff ein Firmware-Upgrade durchführen.  Die verarbeitete Ausgabe kann auch andere Warnungen und Benachrichtigungen für einen Techniker auslösen, um automatisch auf Grundlage der Ereignisse geplant zu werden. 

Da immer mehr Daten erfasst und verarbeitet werden, kann Machine Learning auch dazu verwendet werden, um von dem im System erkannten Muster zu lernen und sie zu entwickeln. Zum Beispiel besteht die Möglichkeit, vorherzusagen, wann Automaten gewartet werden müssen, basierend auf Echtzeitereignisstreams, die in das System gespeist werden, oder eine vorbeugende Wartung auf der Route eines Technikers zu planen, bevor die Dinge schief gehen.

Dieses Muster des Informationsversands von einem Gerät an ein Verarbeitungssystem und das potenzielle Ergreifen von Maßnahmen basierend auf in Echtzeit verarbeiteten Ergebnissen ist im Allgemeinen bei IoT-Anwendungsfällen erkennbar.  Zu den anderen Szenarios zählen verbundene Autos, Klickstream-Analysen, Gebäudemanagement usw. Um diese Rückmeldungsschleife für niedrige Wartezeiten und hohen Durchsatz zu optimieren, kann Stream Analytics verwendet werden, um Daten vom Azure Event Hub zu erfassen, wobei die Daten verarbeitet und direkt wieder an das Event Hub für das jeweilige abonnierte Gerät zurückgesendet werden, um die entsprechenden Maßnahmen zu ergreifen.  


![Azure Stream Analytics real time analysis for the Internet of Things (IoT)][img.stream.analytics.scenario1]


###Telemetrie und Protokollanalyse über Dashboards
Mit zunehmender Anzahl an Geräten, Computern, Automaten und Anwendungen besteht ein häufiger Unternehmensanwendungsfall für die Geschäftsführung in der Notwendigkeit, die veränderten Geschäftsanforderungen zu überwachen und darauf zu reagieren, indem umfassende Analysen in nahezu Echtzeit erstellt werden. Das kanonische Telemetrie/Protokollanalyse-Szenario kann anhand des Onlinedienst- oder Anwendungsbeispiels beschrieben werden, das Muster kommt jedoch hauptsächlich bei Unternehmen vor, die die Anwendungs- oder Gerätetelemetrie protokollieren und erfassen. Die Anwendung oder der Dienst erfasst regelmäßig Zustandsdaten.  Es werden Daten erfasst, die den aktuellen Status der Anwendung oder Infrastruktur zu einem bestimmten Zeitpunkt darstellen, Benutzeranforderungsprotokolle und andere Daten, die Aktionen oder Aktivitäten innerhalb der Anwendung darstellen. Die Daten wurden in der Vergangenheit zur weiteren Verarbeitung in einem BLOB oder in einem anderen Datenspeichertyp gespeichert. Aufgrund des aktuellen Trends hin zum Echtzeit-Dashboarding sowie der Speicherung von Daten in einem BLOB oder einer anderen Art von Speicher für eine Verlaufsanalyse, suchen Kunden nach einer Möglichkeit, um den Strom eingehender Daten direkt zu verarbeiten und zu transformieren, damit sie sofort Endbenutzern in Form von Dashboards und/oder Benachrichtigungen bereitgestellt werden können, wenn Maßnahmen ergriffen werden müssen. Wenn beispielsweise eine Dienstwebsite ausfällt, kann das Betriebspersonal darüber benachrichtigt werden, damit eine Untersuchung eingeleitet und das Problem schnell behoben werden kann. In einigen dieser Anwendungsfälle überwacht ein Menschen in der Regel ein Echtzeit-Dashboard, das auf dem Dataset basiert, das nach der Verarbeitung der über Stream Analytics aufgenommenen Daten aktualisiert wurde. 
 
![Azure Stream Analytics telemetry and log analysis via dashboards][img.stream.analytics.scenario2]

###Ereignisarchivierung für die zukünftige Verarbeitung
Die Erwartungen für eine schnelle und flexible Ausführung im Unternehmen nehmen weiter zu. Unternehmen und Entwickler entscheiden sich für leicht zu bedienende, Cloud-basierte Plattformen, um dem Bedarf nach mehr Flexibilität nachzukommen, und suchen nach Plattformen, die es ihnen ermöglichen, einen kontinuierlichen Datenstrom, der von ihren Systemen in nahezu in Echtzeit generiert wird, zu erfassen und zu verarbeiten.  Diese Kunden können keinen Dienst nutzen, der für niedrige Latenzschreibvorgänge in einem Datenspeicher optimiert ist, und verlieren daher verschiedene wichtige Datensätze, die wertvolle Einblicke für ihr Unternehmen bieten könnten.  Das Szenario der kanonischen Ereignisarchivierung kann wie folgt beschrieben werden: Daten aus verschiedenen Geräte und Plattformen, die geografisch auf der ganzen Welt verteilt sind, werden mittels Push an einen zentralen Datensammler übergeben. Sobald die Daten am zentralen Standort sind, wird eine statusfreie Transformation ausgeführt, z. B. Bereinigung von personenbezogenen Informationen, Hinzufügen von Geokennzeichnung, IP-Suche usw. Die transformierten Daten werden dann im BLOB-Speicher in einer Weise archiviert, die leicht von HDInsight oder anderen Tools für die Offlineverarbeitung genutzt werden kann.

![Azure Stream Analytics event archival for future processing][img.stream.analytics.scenario3]
 







<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
##<a name="nextsteps"></a>Nächste Schritte

- [Erste Schritte mit Azure Stream Analytics][stream.analytics.get.started]
- [Azure Stream Analytics-Entwicklerhandbuch][stream.analytics.developer.guide]
- [Skalieren von Azure Stream Analytics-Aufträgen][stream.analytics.scale.jobs]
- [Einschränkungen und bekannte Probleme von Azure Stream Analytics][stream.analytics.limitations]
- [Azure Stream Analytics-Abfragesprachreferenz][stream.analytics.query.language.reference]
- [Azure Stream Analytics Management REST API Referenz][stream.analytics.rest.api.reference] 



<!--Image references-->
[img.stream.analytics.scenario1]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_01.png
[img.stream.analytics.scenario2]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_02.png
[img.stream.analytics.scenario3]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_03.png


<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
