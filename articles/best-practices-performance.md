<properties 
	pageTitle="Bewährte Methoden für Leistung – Azure" 
	description="Erfahren Sie mehr über bewährte Methoden für Leistung in Azure."
	services="cloud-services, sql-database, storage, service-bus, virtual-network" 
	documentationCenter=".net" 
	authors="Rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/1/2014" 
	ms.author="robb"/>

# Bewährte Methoden für die Azure-Anwendungsleistung

Dieser Leitfaden enthält empfohlene Anleitungen zu bewährten Methoden und Techniken, die befolgt werden sollten, um die Azure-Anwendungsleistung zu optimieren. 

Beachten Sie, dass die Verwendung von Azure viele Vorteile bietet: Leistung ist nur einer der Vorteile. Die Empfehlungen in diesem Dokument konzentrieren sich primär auf die Leistung. Es gibt andere Szenarien, in denen Leistung nicht so entscheidend ist. Angenommen, Sie möchten die physische Hardwareverwaltung an Azure übergeben, oder Sie finden die Option zur nutzungsbasierten Zahlung besonders ansprechend. In diesem Dokument wird nicht versucht, Szenarien zu bewerten, in denen die Leistung eine geringere Priorität hat. 

## Übersicht
 Leistung lässt sich definieren als ["die geleistete Menge sinnvoller Arbeit verglichen mit dem dafür benötigten Zeit- und Ressourcenaufwand"](http://go.microsoft.com/fwlink/?LinkId=252650). 


Diese Definition hat zwei Seiten: Metriken und Ressourcen. Leistungsmetriken sind Kennzahlen, die erreicht werden müssen, um Geschäftsanforderungen zu erfüllen. Hierzu gehören Dinge wie Antwortzeit, Durchsatz, Verfügbarkeit usw. Leistung umfasst auch den Grad der Ressourcennutzung, der zur Erreichung einer gegebenen Ebene von Leistungsmetriken erforderlich ist. Da Kosten fast immer eine Geschäftsanforderung sind und Ressourcen Geld kosten, impliziert Leistung eine möglichst effiziente Nutzung der Ressourcen. 

### Leistungslebenszyklus
Sie können die Leistung an zwei verschiedenen Punkten im Anwendungslebenszyklus beeinflussen:

- während des Entwurfs, wenn Sie grundlegende Architekturentscheidungen treffen, die sich auf die Leistung auswirken, und
- zur Laufzeit, wenn Sie Engpässe identifizieren, die Leistung überwachen und messen usw.

Beide Aktivitäten sind notwendig. Dieses Whitepaper konzentriert sich hauptsächlich auf die Entwurfsphase, weil architektonische Fehler zur Laufzeit schwerer korrigiert werden können.

#### Anwendungsmodellierung
Es ist wichtig, ein Modell der wichtigsten Kundenszenarien für Ihre Anwendung zu erstellen. In diesem Kontext steht "wichtig" für die größten Auswirkungen auf die Leistung. Wenn Sie diese Szenarien und die erforderlichen Anwendungsaktivitäten identifizieren, können Sie Machbarkeitstudien durchführen.

#### Leistungstests für Machbarkeitsstudien

Umfassende durchgängige Leistungstests sind ein wichtiger Schritt während Anwendungsentwurf und -bereitstellung. Azure-Anwendungen bestehen aus vielen Teilen, wozu benutzerdefinierte und von Microsoft bereitgestellte Komponenten gehören. Microsoft kann nicht jede mögliche Kombination dieser Komponenten testen. Daher sind umfassende und ordnungsgemäße Leistungstests Ihrer Anwendung ein wichtiger Schritt jeder Bereitstellung. 

Auf der Grundlage des von Ihnen erstellten Anwendungsmodells sollten Sie möglichst bald Machbarkeitsstudien und Auslastungstests für Ihre Anwendung durchführen, um die Anwendungsarchitektur zu validieren und sicherzustellen, dass die Anwendung die Leistungsanforderungen bezüglich Skalierbarkeit und Latenz erfüllt. Es ist äußerst wichtig, die anfängliche Architektur und Annahmen zu validieren. Sie möchten nicht herausfinden, dass die Anwendung der erwarteten Auslastung nicht gewachsen ist, wenn sie im Einsatz ist! Visual Studio stellt Funktionen zum Ausführen von Auslastungstests bereit, die im Artikel [Visual Studio-Auslastungstest in Azure - Übersicht](http://www.visualstudio.com/get-started/load-test-your-app-vs) beschrieben werden. 

### Wodurch zeichnet sich Azure bezüglich der Leistung aus

Die bedeutendsten Leistungsverbesserungen, die in Azure-Anwendungen erzielt werden können, resultieren aus der Skalierung und Partitionierung von Ressourcen. Zur Erstellung skalierbarer Anwendungen in Azure müssen die Ressourcen nach ihrer physischen Partitionierung horizontal skaliert werden: SQL-Datenbanken, Massenspeicher, Rechenknoten usw. Diese Partitionierung ermöglicht die parallele Ausführung von Anwendungsaufgaben und ist daher die Grundlage für hohe Leistung, das Azure die Ressourcen eines gesamten Rechenzentrums zur Verfügung stehen und Azure die physische Partitionierung für Sie handhabt. Um diesen Grad an allgemeiner Leistungsfähigkeit erreichen zu können, müssen die richtigen Entwurfsmuster für eine horizontale Skalierung verwendet werden. 


Diese hohe Leistung wird zwar bei der Anwendung als Ganzes erreicht, paradoxerweise sind aber die einzelnen Vorgänge in Azure wegen der höheren Netzwerklatenz, der höheren Zuverlässigkeit wegen Failover-Vorgängen usw. weniger performant als ihre lokalen Gegenstücke. Allerdings werden durch die Parallelität, die durch den richtigen Einsatz der Ressourcenpartitionierung erreicht wird, die einzelnen Leistungsdefizite mehr als wettgemacht. 

### Erforderliche Entwurfsaktivitäten

Einige Leistungsfaktoren ändern sich in Abhängigkeit vom Anwendungsszenario. Im nächsten Kapitel wird auf Folgendes eingegangen: Skalierung und Partitionierung von Ressourcen, geeignete Lokalisierung der Daten und optimierte Nutzung der Azure-Dienste. 


Im Kapitel danach werden Leistungsfaktoren behandelt, die jede Azure-Anwendung betreffen: Netzwerklatenz, kurzlebige Verbindungen usw. 

## Entwerfen für optimale Leistung in einer Cloud-Umgebung

Sie müssen die folgenden szenarioabhängigen Bereiche beim Entwurf einer Azure-Anwendung oder der Migration einer lokalen Anwendung zu Azure berücksichtigen: 

- Horizontale Skalierung und Partitionierung der Ressourcen: dies ist der grundlegende Mechanismus zum Erreichen von Parallelität und daraus resultierender Hochleistung. 
* Datenarchitektur: welche Art von Datenspeicher für die verschiedenen Teile der Anwendungsdaten verwendet werden soll 
* Einzelne Azure-Dienstoptimierungen 

Azure gewinnt seinen maximalen Leistungsvorteil aus der Fähigkeit, Ressourcen horizontal zu skalieren und zu partitionieren. Dies ermöglicht eine hochgradige Parallelisierung der Aktivitäten. Angesichts der riesigen Azure SQL-Datenbank ist dies ziemlich offensichtlich. Es trifft aber auch auf alle Ressourcen zu, die einen Engpass darstellen können. 

Azure bietet die folgenden Datenspeicheroptionen, und die Auswahl der richtigen Option wirkt sich stark auf die Leistung aus: 

* Azure SQL-Datenbank 
* Azure Caching 
* Azure-Tabellenspeicher 
* Azure Blob Storage 
* Azure-Laufwerke 
* Azure-Warteschlangen 
* Azure-Servicebus für Brokermessaging 
* Speicherlösungen für große Datenmengen wie Hadoop 

Da die Einzelheiten unterschiedlich sind, werden diese Optionen anhand der folgenden Szenarien besprochen: 

* Azure-Cloud-Dienst mit einer SQL-Datenbank 
* Azure-Cloud-Dienst mit intensiver Nutzung von Speicherwarteschlangen 
* Azure-Website mit MySQL als Back-End-Datenbank 
* Anwendungen für große Datenmengen 
* Anwendungen mit einer MySQL-Back-End-Datenbank 

### Szenario: SQL-Datenbank in einem Cloud-Dienst

Die meisten Grundregeln für einen guten Datenbankentwurf gelten auch für Azure SQL-Datenbank. Es ist eine Unmenge an Material verfügbar, in dem beschrieben wird, wie effektive SQL Server- oder Azure SQL-Datenbankschemata entworfen werden. Nachfolgend sind einige Referenzen zum Entwurf von SQL-Datenbankschemata aufgeführt: 

* [Grundlagen zu Datenbankentwurf und -modellierung]( http://go.microsoft.com/fwlink/?LinkId=252675) 
* [Wege zum Datenbankentwurf](http://go.microsoft.com/fwlink/?LinkId=252676) 
* [Datenbankentwurf](http://go.microsoft.com/fwlink/?LinkId=252677) 

Zwei wichtige Entwurfsaktivitäten sind bei Azure anders: 

* Geeignete Lokalisierung der Daten: Hierzu müssen unter Umständen einige relationale Daten in Azure Blobs oder Azure-Tabellen verschoben werden, sofern dies angebracht ist. 
* Sicherstellen der maximalen Skalierbarkeit: Entscheiden, ob und wie die Daten partitioniert werden sollen. 

#### Datenarchitektur: Verschieben von Daten aus einer SQL-Datenbank

Manche Daten, die häufig in einer lokalen SQL Server-Datenbank gespeichert werden, sollten in Azure an einen anderen Speicherort verschoben werden. 

##### Verschieben von Daten in Azure Blobs

Blob-Daten, z. B. Bilder oder Dokumente, sollten nicht in einer SQL-Datenbank, sondern im Azure Blob Storage gespeichert werden. Obwohl diese Daten häufig auf dem lokalen SQL Server gespeichert werden, ist es in der Cloud viel kostengünstiger, Blob Storage zu verwenden. In der Regel müssen Sie dann Fremdschlüssel, die auf Blob-Daten mit Blob Storage-Bezeichner verweisen, ersetzen, um die Blob-Daten weiterhin abrufen zu können. Zudem müssen Abfragen, die auf die Daten verweisen, geändert werden. 

##### Verschieben von SQL-Tabellen in Azure-Tabellenspeicher

Bei der Entscheidung, ob Sie Azure-Tabellenspeicher verwenden möchten, müssen Sie Kosten und Leistung zu berücksichtigen. Die Verwendung von Tabellenspeicher ist viel wirtschaftlicher als die Speicherung der Daten in einer SQL-Datenbank. Sie müssen jedoch genau betrachten, in welchem Umfang relationale SQL-Funktionen, wie Verknüpfungen, Filter, Abfragen usw. für die Daten genutzt werden. Wenn diese Funktionen wenig verwendet werden, dann eignen sich die Daten zum Speichern in einer Azure-Tabelle. 

Ein häufiges Entwurfsmuster, für das Tabellenspeicher in Frage kommt, ist eine Tabelle mit vielen Zeilen, z. B. die Tabelle "Customers" aus der Beispieldatenbank "AdventureWorks", bei der einige Spalten nur von einer kleinen Gruppe von Kunden verwendet werden, von der Mehrzahl der Kunden jedoch nicht. Es ist ein gängiges Entwurfsmuster, die Spalten in eine zweite Tabelle (vielleicht mit dem Namen "CustomerMiscellany") auszulagern, und eine optionale 1:0-Beziehung zwischen der Tabelle "Customer" und der zweiten Tabelle zu definieren. Sie können in Erwägung ziehen, die zweite Tabelle in den Tabellenspeicher zu verschieben. Dazu müssten Sie beurteilen, ob dies aufgrund der Tabellengröße und der Zugriffsmuster kostengünstig wäre. 

Nähere Informationen zum Tabellenspeicher finden Sie unter: 

* [Azure-Tabellenspeicher und Azure SQL-Datenbank - Vergleich und Gegenüberstellung](http://msdn.microsoft.com/library/jj553018.aspx)
* [Überlegungen zur Azure-Tabellenspeicherleistung](http://go.microsoft.com/fwlink/?LinkId=252663) 
* [SQL-Datenbank und Azure-Tabellenspeicher](http://go.microsoft.com/fwlink/?LinkId=252664) 
* [Optimieren der Leistung durch Batchverarbeitung für Azure-Tabellenspeichereinfügungen](http://go.microsoft.com/fwlink/?LinkID=252665), wo einige Leistungsergebnisse diskutiert werden. 
* [Handbuch zur Leistung und Flexibilität von SQL-Datenbanken](http://go.microsoft.com/fwlink/?LinkId=221876) 

#### Datenpartitionierung

Daten gehören zu den Ressourcen, die mit am häufigsten partitioniert werden. Wenn Sie einen Azure-Cloud-Dienst erstellen, dann sollten Sie die Verwendung des in SQL-Datenbanken integrierten Shardings, das über Verbunde verfügbar ist, in Betracht ziehen. 

Eine Übersicht über SQL-Datenbankverbunde finden Sie unter [Verbunde in der SQL-Datenbank]( http://go.microsoft.com/fwlink/?LinkId=252668).  

##### Entwurfsaufgaben für SQL-Verbunde

Bei Verwendung der SQL-Datenbankverbunde in einem Azure-Cloud-Dienst müssen die klassischen Entwurfsprinzipien etwas abgeändert werden. Allerdings sind die meisten Dinge, die einen guten Entwurf einer lokalen SQL Server-Datenbank auszeichnen, eine notwendige Ausgangsbasis zum Entwurf von SQL-Datenbankverbunde. Es gibt zwei Hauptentwurfsaufgaben, nämlich zu entscheiden: 

* auf was der Verbund basieren soll, und 

* wo Tabellen, die nicht Teil eines Verbunds sind, abgelegt werden sollen. 

Für die Entscheidung, auf das der Verbund basieren soll, müssen Sie das Datenbankschema untersuchen und Aggregationen verknüpfter Tabellen identifizieren. Ein Aggregat ist eine Gruppe von Tabellen, die alle, mit Ausnahme des Aggregatstamms, durch 1:n-Beziehungen über ihre Fremdschlüssel miteinander verknüpft sind. 

In der wohlbekannten Beispieldatenbank "AdventureWorks" ist beispielsweise die Menge {Customer, Order, OrderLine und möglicherweise einige andere Tabellen} ein mögliches Aggregat. Ein weiteres mögliches Aggregat ist {Supplier, Product, OrderLine, Order}. 

Jedes Aggregat eignet sich potenziell für die Bildung eines Verbunds. Sie müssen beurteilen, wo ein Größenwachstum zu erwarten ist, und die Arbeitsauslastung der Anwendung überprüfen: Abfragen, die gut zum Verbundschema passen, d. h. nicht Daten von mehreren Verbundmitgliedern abfragen, werden effizient ausgeführt. Abfragen, die nicht gut dazu passen, erfordern Logik auf der Anwendungsebene, weil in einer SQL-Datenbank derzeit keine datenbankübergreifenden Verknüpfungen unterstützt werden. 

Ein Beispiel für eine Entwurfsanalyse, in dem die Datenbank "AdventureWorks" überprüft wird, um einen Verbund zu bilden, und in dem die Entwurfsüberlegungen schrittweise erläutert werden, finden Sie unter [Scale-First Approach to Database Design with Federations: Teil 1 - Auswählen von Verbund und Verbundschlüssel](http://go.microsoft.com/fwlink/?LinkId=252671). 

Nachdem Sie entschieden haben, welche Tabellen einen Verbund bilden sollen, müssen Sie den Primärschlüssel der Aggregatstammtabelle den einzelnen verknüpften Tabellen jeweils als Spalte hinzufügen. 

Nach der Auswahl der Tabellen für den Verbund ist eine weitere Frage, wo die Referenztabellen sowie andere Datenbankobjekte gespeichert werden sollen. Eine eingehende Diskussion dieses Themas finden Sie unter [Scale-First Approach to Database Design with Federations: Teil 2 - Kommentieren und Bereitstellen von Verbundschemas](http://go.microsoft.com/fwlink/?LinkId=252672). Die Ausführung erweiterter Abfragen wird in [Teil 2] beschrieben( http://go.microsoft.com/fwlink/?LinkId=252673). 

                                            
##### Do-It-Yourself-Partitionierung

Es sind einige Beispiele verfügbar, in denen verschiedene Möglichkeiten zum Partitionieren von Daten gezeigt werden. Wenn Sie beschließen, Ihre SQL-Datenbankinstanz nicht mit Verbunden zu partitionieren, müssen Sie eine Partitionierungsmethode wählen, die für Ihre Anwendung geeignet ist. Hier einige Beispiele: 

* Einen umfassenden Beitrag, der vor der Veröffentlichung von Verbunden geschrieben wurde, finden Sie unter [Gewusst wie: Shard mit SQL-Datenbank](http://go.microsoft.com/fwlink/?LinkId=252678). 
* [Shard-Bibliothek für SQL Server und SQL-Datenbank]( http://go.microsoft.com/fwlink/?LinkId=252679) 

##### Partitionierung anderer Ressourcen

Sie können neben SQL-Datenbank auch andere Ressourcen partitionieren. Sie können beispielsweise Anwendungsserver partitionieren und sie bestimmten Datenbanken zuordnen. Angenommen, Ihre Anwendung enthält N Anwendungsserver und auch N Datenbanken. Wenn jeder Anwendungsserver auf jede Datenbank zugreifen kann, dann werden N horizontal skalierte Datenbankverbindungen benötigt und dadurch kann in manchen Fällen ein fixer Azure-Grenzwert überschritten werden. Wenn Sie jedoch jeden Anwendungsserver auf einige Datenbanken beschränken, dann wird die Anzahl verwendeter Verbindungen dadurch deutlich verringert. 

Abhängig von ihrer Anwendung, können Sie ähnliche Überlegungen für andere Ressourcen anstellen. 


#### Caching

Der Azure Cache Service stellt verteilten elastischen Arbeitsspeicher zum Zwischenspeichern von Dingen bereit, wie ASP.Net-Sitzungsstatus oder häufig referenzierte Werte aus SQL-Datenbankreferenztabellen. Weil sich die Objekte im verteilten Arbeitsspeicher befinden, sind beträchtliche Leistungsgewinne möglich. Weil Azure die Caching-Infrastruktur handhabt, ist die Implementierung mit geringen Entwicklungskosten verbunden. 

Planen Sie, ausreichend Cachingkapazitäten bereitzustellen, damit sie Objekte zwischenspeichern können, auf die häufig zugegriffen wird. In SQL-Datenbank werden häufig Referenztabellen verwendet, um numerische Codes in längere beschreibende Zeichenfolgen zu konvertieren. Diese Tabellen enthalten häufig Daten, wie Länder- und Städtenamen, gültige Postleitzahlen, Namen von Abteilungen in Ihrem Unternehmen usw. Bei kleineren Tabellen kann es sinnvoll sein, die gesamte Tabelle im Cache zu speichern, bei anderen empfiehlt es sich, nur die am häufigsten verwendeten Werte zu speichern. Der Leistungsgewinn resultiert aus Abfragen mit mehreren Verknüpfungen, die an diesen Daten ausgeführt werden: bei jedem Wert, der im Cache gefunden wird, werden einige Festplattenzugriffe gespeichert. Eine gute Einführung und Diskussion zum Thema Leistung und Caching in Azure finden Sie unter [Einführung zum Azure-Cache-Dienst](http://go.microsoft.com/fwlink/?LinkId=252680). Einen neueren Blogbeitrag zu diesem Thema finden Sie unter [Überlegungen zur Azure-Cache-Leistung](http://go.microsoft.com/fwlink/?LinkId=252681). 

#### Szenario: Verwenden des Queuing in Azure-Anwendungen

Ein Beispiel für dieses Szenario besteht in der Verwendung von StreamInsight, um Warteschlangen mit Nachrichten zu füllen, die später verarbeitet werden. 

Azure-Warteschlangen verwenden verwendet, um Nachrichten weiterzuleiten, Subsysteme zeitweilig zu entkoppeln und Lasten- und Belastungsausgleich bereitzustellen. 

Azure verfügt über zwei alternative Warteschlangentechnologien: Azure-Speicherwarteschlangen und Servicebus. 

Azure-Dienstwarteschlagen zeichnen sich durch Merkmale aus, wie große Warteschlangengröße, Verfolgen des Status und mehr. Servicebus bietet Features wie Veröffentlichen/Abonnieren, vollständige Integration in Windows Communication Foundation ("WCF"), automatische Erkennung von Duplikaten, garantierte FIFO-Zustellung (First-in first-out) und mehr. 

Einen ausführlicheren und detaillierten Vergleich der beiden Technologien finden Sie unter [Azure-Warteschlangen und Azure Service Bus-Warteschlangen - Vergleich und Gegenüberstellung](http://go.microsoft.com/fwlink/?LinkId=252682). 

Eine Beschreibung zur Service Bus-Leistung finden Sie unter [Bewährte Methoden zur Leistungsverbesserung mithilfe des Service Bus-Brokermessagings](http://go.microsoft.com/fwlink/?LinkID=252683). 

#### Szenario: Anwendungen für große Datenmengen

Große Datenmengen ("Big Data") sind häufig ein Nebenprodukt anderer Systeme oder Anwendungen. Beispiele: 

* Webprotokolle 

* Andere Diagnose-, Audit- und Überwachungsdateien 

* Seismische Protokolle von Ölfirmen 

* Klickdaten und andere Informationen, die Personen beim Surfen im Internet hinterlassen 

Große Datenmengen lassen sich anhand der folgenden Kriterien identifizieren: 

* Größe (in der Regel hunderte von Terabyte oder größer) 

* Typ: nicht-relationales, variables Schema, Dateien in einem Dateisystem 

Die Daten eignen sich in der Regel nicht zur Verarbeitung in einer relationalen Datenbank. 

Es gibt vier Hauptarten von Nicht-SQL-Datenspeicher: 

* Schlüssel/Wert 

* Dokument 

* Grafik 

* Spalte-Familie 

Azure stellt direkte Unterstützung für Hadoop bereit und ermöglicht auch die Nutzung anderer Technologien. Weitere Informationen zum Azure HDInsight-Dienst finden Sie unter: 

* [Große Datenmengen](/de-de/solutions/big-data/) 
* [Azure HDInsight-Dienst](/de-de/documentation/services/hdinsight/)
* [Erste Schritte mit dem Azure HDInsight-Dienst](/de-de/documentation/articles/hdinsight-get-started/)

Eine Diskussion von Fragen, die mit den verschiedenen Nicht-SQL-Speichermethoden verbunden sind, finden Sie unter: 

* [Lernen Sie NoSQL in Azure kennen](http://go.microsoft.com/fwlink/?LinkId=252729) 
* [AggregateOrientedDatabase](http://go.microsoft.com/fwlink/?LinkID=252731)
* [PolyglotPersistence](http://go.microsoft.com/fwlink/?LinkId=252732) 

#### Andere Leistungsoptimierungen einzelner Azure-Dienste

Viele der einzelnen Azure-Dienste verfügen über Funktionen und Einstellungen, die sich stark auf die Leistung auswirken können und daher analysiert werden müssen. 

##### Azure-Laufwerke

Sie können die Festplattennutzung einer vorhandenen Anwendung mit einem Azure-Laufwerk simulieren, das sich auf einen Windows-Blob stützt und daher den Ausfall einzelner Computer unbeschadet übersteht. 

##### Lokaler Speicher

Dieser Speicher ist zwar nicht über Computerausfälle hinweg persistent, kann aber zum Speichern von Informationen, auf die häufig zugegriffen wird, oder von Zwischenergebnissen, die an derer Stelle verwendet werden, genutzt werden. Das ist kostengünstig, da die Nutzung gebührenfrei ist. 

##### Azure-Zugriffssteuerungsdienst (Access Control Service, ACS)

Zwei Hauptfaktoren wirken sich auf die ACS-Ressourcennutzung und daher die Leistung aus: Tokengröße und Verschlüsselung. Nähere Informationen finden Sie unter [Leistungsrichtlinien für den Zugriffssteuerungsdienst (ACS)](http://go.microsoft.com/fwlink/?LinkId=252747). 

##### Serialisierung

Die Serialisierung ist kein selbstverständlicher Teil der Leistungsoptimierung, aber eine Verringerung des Netzwerkverkehrs kann in einigen Anwendungsszenarien signifikant sein. Ein Beispiel dazu, in welchem Umfang sich die Serialisierungsgrößen je nach Protokoll unterscheiden können, finden Sie in den Beispielen unter [Azure-Webanwendungen und Serialisierung](http://go.microsoft.com/fwlink/?LinkId=252749). 

Wenn die Menge der Daten, die verschoben werden, Leistungsproblem darstellt, dann verwenden Sie die kleinste verfügbare Serialisierungsgröße. Falls die Serialisierungsleistung nicht ausreicht, sollten Sie die Verwendung benutzerdefinierter oder anderer nicht von Microsoft stammender Serialisierungsformate von Drittanbietern in Betracht ziehen. Wie immer, sind Machbarkeitsstudien entscheidend. 


### Azure-Websites mit MySQL

Unter den folgenden Links finden Sie Ratschläge zur Leistung von MySQL: 

* Die Suche nach *performance* auf der Website [http://mysql.com]( http://go.microsoft.com/fwlink/?LinkId=252775) liefert viele Ressourcen. 
* Die Foren unter [ http://forums.mysql.com/list.php?24]( http://go.microsoft.com/fwlink/?LinkId=252776) sind weitere Ressourcen, die Sie konsultieren können. 



## Entwerfen für gemeinsam genutzte Systeme

Azure ist darauf ausgelegt, mehrere Anwendungen gleichzeitig auszuführen, die aus Gründen der Ausfallsicherheit auf mehreren Computern repliziert werden. Dies wirkt sich auf verschiedene Weise auf die Anwendungsleistung aus: 

* Kurzlebige Verbindungen 

* Ressourcendrosselung, welche die maximale Nutzung begrenzt 

* Netzwerklatenz 

* Physischer Standort der Dienste 

Diese Überlegungen gelten für alle Anwendungsarchitekturen, weil sie durch die physische Infrastruktur der Azure-Rechenzentren bestimmt werden. Eine ausführliche Diskussion finden Sie unter [Handbuch zur Leistung und Flexibilität von SQL-Datenbanken](http://go.microsoft.com/fwlink/?LinkID=252666). 

### Netzwerklatenz

Azure ist eine dienstbasierte Plattform gemeinsam genutzter Ressourcen und das bedeutet, dass zwei Arten von Latenzen oder Unterbrechungen regelmäßig auftreten. Die erste Art ist der Zeitraum, der erforderlich ist, um eine Anforderung über das Internet senden oder eine Antwort über das Internet zu erhalten. Da diese Anforderungen und Antworten eine beliebige Anzahl von Routern passieren können, bevor sie zum Client zurückkehren, sind Timeouts und Verbindungstrennungen häufiger als in lokalen, festen Netzwerken. Die zweite Art ist der Zeitraum, den ein System mit gemeinsam genutzten Ressourcen wie Azure benötigt, um aus Kontinuitätsgründen Sicherungsversionen der Daten zu erstellen und Anforderungen für Instanzen, die entfernt worden sind, zu ersetzen und umzuleiten. Es ist wichtig, dass Sie wissen, wie sich diese Latenzen und Fehler kompensieren lassen, um überhaupt Leistungsanforderungen in der Anwendung erfüllen zu können. Durch Auslastungstests mit realistischen Lasten gewinnen Sie mehr Informationen zu den Latenzen, die auftreten. 

Berücksichtigen Sie, dass wahrscheinlich größere Latenzen auftreten, da der physische Standort des Cloud-Rechenzentrums wahrscheinlich weiter entfernt ist als der lokale Server. 

### Physischer Standort der Dienste

Bringen Sie, wenn möglich, verschiedene Knoten oder Anwendungsebenen im gleichen Rechenzentrum unter. Andernfalls sind Netzwerklatenz und Kosten höher. 

Sie können beispielsweise die Webanwendung im gleichen Rechenzentrum wie die SQL-Datenbankinstanz, die darauf zugreift, unterbringen statt in einem anderen Rechenzentrum oder lokal im Unternehmen. 

### Kurzlebige Verbindungen

Ihre Anwendung MUSS in der Lage sein, mit getrennten Verbindungen umzugehen. Getrennte Verbindungen sind unvermeidlich und typisch für die Clould-Architektur (z. B. Vorgänge wie das Ersetzen inaktiver Knoten, das Aufteilen eines Verbundmitglieds in SQL-Datenbank usw.). Das beste Framework hierfür ist derzeit [Anwendungsblock für die Handhabung vorübergehender Fehler](http://go.microsoft.com/fwlink/?LinkID=236901). 

### Drosselung

Im Dienstleistungsbereich können Ressourcen sehr granular sein, und hier bezahlen Sie nur für das, was Sie nutzen. Allerdings gibt es für alle Ressourcen eine Mindestgarantie für Größe, Geschwindigkeit und Durchsatz (wichtig, wenn Sie eine Datenbank benötigen, die eine bestimmte Größe überschreitet). Gelegentlich wird ein Dienst, der wichtig ist, aber auch durch äußere Grenzen eingeschränkt. Weil Azure-Anwendungen in einer gemeinsam genutzten Umgebung mit anderen Anwendungen ausgeführt werden, wendet Azure einige Ressourceneinschränkungen an, die Sie berücksichtigen müssen. Wenn der Drosselungsgrenzwert für eine Ressource überschritten wird, verursacht eine weitere Anforderung dieser Ressource eine Ausnahme. 

### Physische Kapazität

Ein unabdingbares Element der Leistungsplanung ist die Kapazitätsplanung: Wenn Sie nicht ausreichend Speicher verschiedener Art bereitstellen, kann die Anwendung möglicherweise gar nicht ausgeführt werden. Ebenso kann unzureichende Arbeitsspeicher- oder Prozessorkapazität die Ausführungsgeschwindigkeit Ihrer Anwendung stark beeinträchtigen. 


Azure verringert den Kapazitätsplanungsaufwand enorm, weil sich viele der früher erforderlichen Aktivitäten, insbesondere die Beschaffung und Bereitstellung von Computern, stark verändert haben. In Azure konzentriert sich die Kapazitätsplanung nicht mehr auf die physischen Rechenelemente, sondern auf eine höhere Abstraktionsebene, auf der stattdessen geklärt werden muss, wie viele der folgenden Ressourcen benötigt werden: 

* Rechenknoten 
* Blob-Speicher 
* Tabellenspeicher 
* Warteschlangen usw. 

Aufgrund der Skalierbarkeit von Azure, sind die anfänglichen Entscheidungen bezüglich der Kapazität nicht in Stein gemeißelt: es ist relativ einfach, Azure-Ressourcen noch oben (oder unten) zu skalieren. Trotzdem ist es wichtig, eine genaue Kapazitätsplanung zu erstellen, da damit sichergestellt wird, dass der Bereitstellung der Anwendung in der Produktionsumgebung keine Versuchsphase in Bezug auf die Kapazität folgt. 

Für Anwendungen, deren Ressourcenanforderungen im Laufe der Zeit stark schwanken, sollten Sie die Verwendung von [Anwendungsblocks für die automatische Skalierung](http://go.microsoft.com/fwlink/?LinkId=252873) in Betracht ziehen. Mit diesem Block können Sie Regeln für die Skalierung von Rolleninstanzen festlegen. Zwei Arten von Regeln werden definiert: 

* Einschränkungsregeln, mit denen eine maximale/minimale Anzahl von Instanzen pro Tageszeit definiert wird 

* Reaktive Regeln, die wirksam werden, wenn eine Bedingung erfüllt wird, z. B. prozentuale CPU-Auslastung 

Sie können auch benutzerdefinierte Regeln definieren. Weitere Informationen finden Sie unter [Anwendungsblocks für die automatische Skalierung](http://go.microsoft.com/fwlink/?LinkId=252873). 



Die Kapazitätsplanung ist ein eigenes Spezialgebiet, und in diesem Dokument wird davon ausgegangen, dass Sie sie bereits abgeschlossen haben. Eine ausführliche Diskussion der Kapazitätsplanung in Azure finden Sie unter [Kapazitätsplanung für Warteschlangen und Themen in Service Bus](http://go.microsoft.com/fwlink/?LinkId=252875).



## Leistungsüberwachung und -optimierung zur Laufzeit

Selbst der sorgfältigste Entwurf ist keine Garantie dafür, dass zur Laufzeit keine Leistungsprobleme auftreten. Daher muss die Anwendungsleistung fortlaufend überwacht werden, damit überprüft wird, ob die erforderlichen Leistungsmetriken erreicht werden, und Situationen korrigiert werden können, in denen diese Metriken nicht erreicht werden. Auch bei gut entworfenen Anwendungen können unvorhergesehene Dinge eintreten, z. B. ein exponentielles Wachstum der Nutzung oder Änderungen der Laufzeitumgebung, die zu Leistungsproblemen führen und eine Optimierung erforderlich machen. Häufig ist das Erkennen und Beheben von Engpässen ein wesentlicher Bestandteil des Prozesses. 


Damit Lautzeitprobleme während der Laufzeit behandelt werden können, muss vorab Arbeit geleistet werden, um Protokollierungsfunktionen und eine ordnungsgemäße Ausnahmenbehandlung zu implementieren, sodass die Fehlerbehandlung genau dann durchgeführt werden kann, wenn Probleme auftreten. Dieser Bereich wird auf der Seite [Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen](http://go.microsoft.com/fwlink/?LinkID=252876) umfassend behandelt. 

Es sind Tools zum Überwachen der fortlaufenden Leistung jedes Azure-Diensts verfügbar. Außerdem sollten Protokollierungsfunktionen in die Anwendungen eingebaut werden, die detaillierte Informationen bereitstellen, die zur Fehlerbehebung und Lösung von Leistungsproblemen erforderlich sind. 

### SQL-Datenbank

Beachten Sie, dass SQL-Profiler derzeit nicht in Azure verfügbar ist. Es gibt verschiedene Problemumgehungen, um die erforderlichen Leistungsdaten zu erhalten. Eine Alternative während der Entwicklung besteht in der Durchführung von Eingangstests in einer lokalen Version der Datenbank, wobei SQL Profiler verfügbar ist. 

Sie können auch den Befehl SET STATISTICS verwenden und mit SQL Server Management Studio den von einer Abfrage generierten Ausführungsplan anzeigen, da das Programmieren effizienter Abfragen für die Leistung entscheidend ist. Eine ausführliche Diskussion und eine Schritt-für-Schritt-Erläuterung der Vorgehensweise finden Sie unter [Einblicke in die SQL-Datenbankleistung](http://go.microsoft.com/fwlink/?LinkId=252877). Ein weiterer interessanter Ansatz besteht in der Analyse und dem Vergleich der Leistung von [SQL-Datenbank und dem lokalen SQL Server](http://go.microsoft.com/fwlink/?LinkId=252878). 

Zwei Themen zu dynamischen Verwaltungssichten: 

* [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten](http://go.microsoft.com/fwlink/?LinkId=236195) 
* [Hilfreiche dynamische Verwaltungssichten für SQL-Datenbanken zur Analyse, ob SQL Profiler vermisst wird](http://go.microsoft.com/fwlink/?LinkId=252879) 

### Analyseressourcen und -tools

Eine Reihe von Drittanbietertools sind zum Analysieren der Azure-Leistung verfügbar: 

- [Cerebrata](http://go.microsoft.com/fwlink/?LinkId=252880) 
- [Leistungstests für SQL Server und SQL-Datenbanken: Enzo SQL Baseline](http://enzosqlbaseline.codeplex.com/) 

Weitere Ressourcen 

* [Anleitung zu Leistung und Flexibilität von SQL-Datenbanken](http://go.microsoft.com/fwlink/?LinkID=252666) 
* [SQL-Datenbank](http://go.microsoft.com/fwlink/?LinkId=246930) 
* [Speicher](http://go.microsoft.com/fwlink/?LinkId=246933) 
* [Netzwerke]( http://go.microsoft.com/fwlink/?LinkId=252882) 
* [Service Bus]( http://go.microsoft.com/fwlink/?LinkId=246934) 
* [Azure-Planung - Handbuch zum Integrieren von Azure in Ihre Umgebung nach dem Fällen einer Entscheidung](http://go.microsoft.com/fwlink/?LinkId=252884) 

<!--HONumber=35.2-->

<!--HONumber=46--> 
