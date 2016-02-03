<properties 
    pageTitle="Häufige Anwendungsfälle für DocumentDB | Microsoft Azure" 
    description="Erfahren Sie mehr über die fünf wichtigsten Anwendungsfälle für DocumentDB: benutzergenerierte Inhalte, Ereignisprotokollierung, Katalogdaten, Benutzereinstellungsdaten und das Internet der Dinge (Internet of Things, IoT)." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/30/2015" 
    ms.author="hawong"/>

# Häufige Anwendungsfälle für DocumentDB
Dieser Artikel bietet eine Übersicht über verschiedene häufige Anwendungsfälle für DocumentDB. Die Empfehlungen in diesem Artikel dienen als Ausgangspunkt für die Entwicklung Ihrer Anwendung mit DocumentDB.

Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten:
 
- Was sind häufige Anwendungsfälle für DocumentDB?
- Welche Vorteile bietet die Verwendung von DocumentDB als Speicher für benutzergenerierte Inhalte?
- Welche Vorteile bietet die Verwendung von DocumentDB als Katalogdatenspeicher?
- Welche Vorteile bietet die Verwendung von DocumentDB als Ereignisprotokollspeicher?
- Welche Vorteile bietet die Verwendung von DocumentDB als Datenspeicher für Benutzereinstellungen?
- Welche Vorteile bietet die Verwendung von DocumentDB als Datenspeicher für Systeme für das Internet der Dinge (Internet of Things, IoT)?

    
## Benutzergenerierte Inhalte
Ein häufiger Anwendungsfall für DocumentDB ist die Speicherung und Abfrage von benutzergenerierten Inhalten für Web- und mobile Anwendungen, insbesondere Anwendungen für soziale Medien. Einige Beispiele für benutzergenerierte Inhalte sind Chatsitzungen, Tweets, Blogbeiträge, Bewertungen und Kommentare. Benutzergenerierte Inhalte in Anwendungen für soziale Medien sind eine Mischung aus Freitext, Eigenschaften, Tags und Beziehungen, die nicht durch eine starre Struktur begrenzt sind.

Inhalte wie Chats, Kommentare und Beiträge können in DocumentDB gespeichert werden, ohne dass hierzu Transformationen oder komplexe ORM-Ebenen (Object Relational Mapping) erforderlich sind. Dateneigenschaften können beim Durchlaufen des Anwendungscodes ganz einfach hinzugefügt oder geändert werden, um die jeweiligen Anforderungen zu erfüllen. Auf diese Weise wird die Entwicklung erheblich beschleunigt.

Anwendungen, die in verschiedene soziale Netzwerke integriert werden, müssen auf Schemaänderungen dieser Netzwerke reagieren. Da Daten in DocumentDB standardmäßig und automatisch indiziert werden, können Daten jederzeit abgefragt werden. Daher verfügen diese Anwendungen über die Flexibilität, Projektionen entsprechend ihren jeweiligen Anforderungen abzurufen.

Viele soziale Anwendungen werden auf globaler Ebene ausgeführt und können nicht vorhersagbare Nutzungsmuster aufweisen. Flexibilität bei der Skalierung des Datenspeichers ist von grundlegender Bedeutung, da die Anwendungsschicht in Abhängigkeit von der Nutzung skaliert werden muss. Sie können den Speicher horizontal hochskalieren, indem Sie einem DocumentDB-Konto weitere Datenpartitionen hinzufügen. Darüber hinaus können Sie auch weitere DocumentDB-Konten über mehrere Regionen hinweg erstellen. Informationen zur regionalen Verfügbarkeit von DocumentDB-Diensten finden Sie unter [Azure-Region](http://azure.microsoft.com/regions/#services).

## Katalogdaten
Zu den Anwendungsfällen für Katalogdaten gehören das Speichern und Abfragen eines Satzes von Attributen für Entitäten wie beispielsweise Personen, Orte und Produkte. Einige Beispiele für Katalogdaten sind Benutzerkonten, Produktkataloge, Geräteregistrierungen für das Internet der Dinge und Stücklistensysteme. Attribute für diese Daten können variieren und sich im Laufe der Zeit ändern, um geänderte Anwendungsanforderungen zu erfüllen.

Betrachten Sie als Beispiel einen Produktkatalog für einen Automobilzulieferer. Jedes Teil verfügt zusätzlich zu den Attributen, die allen Teilen gemeinsam sind, über eigene Attribute. Darüber hinaus können sich Attribute für ein bestimmtes Teil ändern, wenn ein neues Modell auf den Markt kommt. Als JSON-Dokumentspeicher unterstützt DocumentDB flexible Schemata und ermöglicht es Ihnen, Daten mit geschachtelten Eigenschaften darzustellen. Daher eignet sich DocumentDB gut zum Speichern von Produktkatalogdaten.

## Protokolldaten
Anwendungsprotokolle werden häufig in großen Mengen ausgegeben und können je nach bereitgestellter Anwendungsversion oder der Komponente, die Ereignisse protokolliert, veränderliche Attribute aufweisen. Protokolldaten sind nicht durch komplexe Beziehungen oder starre Strukturen begrenzt. Protokolldaten werden in immer größerem Maß dauerhaft im JSON-Format gespeichert, da JSON ein einfaches Datenformat und leicht lesbar ist.
   
In Zusammenhang mit Ereignisprotokolldaten gibt es zwei typische Anwendungsfälle. Im ersten Anwendungsfall werden Ad-hoc-Abfragen in einer Teilmenge von Daten ausgeführt, um Probleme zu behandeln. Während der Problembehandlung wird zuerst eine Teilmenge von Daten aus den Protokollen abgerufen, üblicherweise unter Verwendung von Zeitreihen. Anschließend wird ein Drilldown durchgeführt, indem das Dataset nach Fehlerstufen oder Fehlermeldungen gefiltert wird. Hier bietet das Speichern von Ereignisprotokollen in DocumentDB einen Vorteil. In DocumentDB gespeicherte Protokolldaten werden standardmäßig und automatisch indiziert und können daher jederzeit abgefragt werden. Darüber hinaus können Protokolldaten über Datenpartitionen hinweg dauerhaft als Zeitreihe gespeichert werden. Ältere Protokolle können gemäß Ihrer Aufbewahrungsrichtlinie in den Cold Storage ausgelagert werden.

Der zweite Anwendungsfall betrifft Datenanalyseaufträge mit langer Laufzeit, die offline für große Mengen von Protokolldaten ausgeführt werden. Die Analysen von Serververfügbarkeiten, Anwendungsfehlern und Clickstreamdaten sind Beispiele für diesen Anwendungsfall. In der Regel wird Hadoop verwendet, um diese Arten von Analysen durchführen. Mit dem Hadoop-Connector für DocumentDB fungieren DocumentDB-Datenbanken als Datenquellen und -senken für Pig-, Hive- und Map/Reduce-Aufträge. Ausführliche Informationen zum Hadoop-Connector für DocumentDB finden Sie unter [Ausführen ein Hadoop-Auftrags mit DocumentDB und HDInsight](documentdb-run-hadoop-with-hdinsight.md).

## Benutzereinstellungsdaten
Heute sind die meisten modernen Web- und mobilen Anwendungen mit komplexen Ansichten und Funktionalitäten verbunden. Diese Ansichten und Funktionalitäten sind üblicherweise dynamisch und auf die Einstellungen bzw. die Stimmung des Benutzers oder auf Brandinganforderungen ausgerichtet. Daher müssen Anwendungen personalisierte Einstellungen effektiv abrufen können, um Benutzeroberflächenelemente und Funktionalitäten schnell darzustellen.

JSON ist ein effektives Format zur Darstellung von Daten für das Benutzeroberflächenlayout. Es ist nicht nur unkompliziert, sondern kann auch problemlos von JavaScript interpretiert werden. DocumentDB bietet optimierbare Konsistenzebenen, die schnelle Lesevorgänge sowie Schreibvorgänge mit geringer Latenz ermöglichen. Daher ist die Speicherung von Daten für das Benutzeroberflächenlayout einschließlich personalisierter Einstellungen als JSON-Dokument in DocumentDB eine effektive Möglichkeit zur Übertragung dieser Daten.

## Gerätesensordaten
IoT-Anwendungsfälle weisen einige gemeinsame Muster hinsichtlich der Erfassung, Verarbeitung und Speicherung von Daten auf. Erstens können diese Systeme große Mengen von Daten aus Gerätesensoren verschiedenster Gebietsschemata erfassen. Zum Zweiten verarbeiten und analysieren diese Systeme Daten, um in Echtzeit Erkenntnisse aus diesen Daten zu gewinnen. Und nicht zuletzt landen die meisten, wenn nicht sogar alle Daten schlussendlich in einem Datenspeicher zur Ad-hoc-Abfrage und Offlineanalyse.

Microsoft Azure bietet umfangreiche Dienste, die für IoT-Anwendungsfälle genutzt werden können. Zu den IoT-Diensten in Azure gehören Azure Event Hubs, Azure DocumentDB, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight und PowerBI.

Azure Event Hubs bietet einen hohen Durchsatz bei geringer Latenz und kann daher große Datenmengen erfassen und verarbeiten. Erfasste Daten, die für Erkenntnisse in Echtzeit verarbeitet werden müssen, können zur Echtzeitanalyse an Azure Stream Analytics weitergeleitet werden. Daten können für Ad-hoc-Abfragen in DocumentDB geladen werden. Sobald die Daten in DocumentDB geladen sind, können sie abgefragt werden. Die Daten in DocumentDB können im Rahmen von Echtzeitanalysen als Referenzdaten verwendet werden. Darüber hinaus können Daten durch Verknüpfen von DocumentDB-Daten mit HDInsight für Pig-, Hive- oder Map/Reduce-Aufträge weiter optimiert und verarbeitet werden. Die optimierten Daten werden anschließend zur Berichterstellung wieder in DocumentDB geladen.

Eine Beispiellösung für das Internet der Dinge unter Verwendung von DocumentDB, EventHubs und Storm finden Sie im [Beispielrepository "hdinsight-storm" in GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Weitere Informationen zu den Azure-Angeboten für das Internet der Dinge finden Sie unter [Ihr eigenes Internet der Dinge](http://www.microsoft.com/de-DE/server-cloud/internet-of-things.aspx).

## Nächste Schritte
 
Um mit DocumentDB zu beginnen, können Sie ein [Konto](http://azure.microsoft.com/pricing/free-trial/) erstellen und dann unserem [Lernpfad](https://azure.microsoft.com/documentation/learning-paths/documentdb/) folgen, um die Informationen zu DocumentDB zu erhalten, die Sie benötigen.

Wenn Sie mehr zu Kunden erfahren möchten, die DocumentDB verwenden, stehen Ihnen folgende Kundenberichte zur Verfügung:

- [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Führender Integrator bietet multinationalen Unternehmen innerhalb weniger Minuten globale Einblicke dank flexibler Cloudtechnologien.
- [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Mehr Personalisierung und Interaktivität in den Nachrichten, um engagierten Bürgern zielgerichtete Informationen zu bieten. 
- [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Große Marken verlassen sich auf SGS, um eine konsistente Farbdarstellung auf der ganzen Welt zu erzielen. Und SGS verlässt sich auf Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Telenor, einer der weltweit größten Mobilfunkanbieter, nutzt die Cloud, um so schnell reagieren zu können wie ein Startupunternehmen. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Der Speicher der Zukunft basiert auf schnellen Suchvorgängen und problemlosem Datenfluss.
 

<!---HONumber=AcomDC_0107_2016-->