<properties 
	pageTitle="Einführung in DocumentDB, eine JSON-Datenbanken | Microsoft Azure" 
	description="Informationen zu Azure DocumentDB, eine JSON-Datenbank. Dieses Dokumentendatenbank ist für große Datenmengen, flexible Skalierbarkeit und hohe Verfügbarkeit konzipiert." 
	keywords="JSON-Datenbank, Dokumentendatenbank"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2015" 
	ms.author="mimig"/>

# Einführung in DocumentDB: JSON-NoSQL-Datenbanken

Bei DocumentDB handelt es sich um eine NoSQL-Dokumentendatenbank für Big Data-Lösungen zur Verarbeitung von JSON-Daten, die eine einfache Skalierung und hohe Verfügbarkeit erfordern.

Eine schnelle Möglichkeit, mehr über die JSON-Datenbank zu erfahren und die Datenbank in Aktion zu sehen, bieten die drei folgenden Schritte:

1. Sehen Sie sich das zweiminütige Video[Was ist DocumentDB?](http://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/)an, das die Vorteile von DocumentDB präsentiert.
2. Sehen Sie sich das drei minütige Video [Erstellen einer DocumentDB in Azure](http://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) an, das die ersten Schritte mit DocumentDB im Azure-Vorschauportal veranschaulicht.
3. Besuchen Sie den [Query Playground](http://www.documentdb.com/sql/demo), in dem Sie die verschiedenen Aktivitäten durchlaufen können, um mehr über die umfassende Abfragefunktionalität in DocumentDB zu erfahren. Wechseln Sie dann zur Registerkarte "Sandbox", um eigene benutzerdefinierten SQL-Abfragen auszuführen und mit DocumentDB zu experimentieren.

Kehren Sie dann zurück zu diesem Artikel, in dem Dinge vertieft werden und Sie die Antworten auf die folgenden Fragen erhalten:

-	[Was DocumentDB ist und welchen Nutzen der Dienst für cloudbasierte und mobile Anwendungen bietet?](#what-is-azure-documentdb)
-	[Wie werden meine Daten in DocumentDB verwaltet, und wie kann ich auf sie zugreifen?](#data-management)
-	[Wie kann ich mithilfe von DocumentDB Anwendungen entwickeln?](#develop)
-	[Welche sind die nächsten Schritte zum Erstellen einer DocumentDB-Anwendung?](#next-steps)  

## Was ist Azure DocumentDB?  

Moderne Anwendungen produzieren, verwenden und reagieren schnell auf sehr große Datenmengen. Diese Anwendungen entwickeln sich rasant – ebenso wie das ihnen zugrunde liegende Datenschema. Um dem zu begegnen, gehen Entwickler zunehmend dazu über, schemafreie NoSQL-Dokumentdatenbanken als einfache, schnelle und skalierbare Lösungen für die Speicherung und Verarbeitung von Daten einzusetzen. Dabei können Anwendungsdatenmodelle und unstrukturierte Datenfeeds weiterhin schnell durchlaufen werden. Viele schemafreie Datenbanken ermöglichen jedoch keine komplexen Abfragen und Transaktionsverarbeitung und erschweren so eine moderne Datenverwaltung. Hier kommt DocumentDB ins Spiel. Microsoft hat DocumentDB entwickelt, um diese Anforderungen beim Verwalten schemafreier Daten zu erfüllen.

DocumentDB ist ein wirklich schemaloser NoSQL-Dokumentdatenbankdienst, der für moderne mobile und webbasierte Anwendungen entwickelt wurde. DocumentDB bietet gleichbleibend schnelle Lese- und Schreibvorgänge, Schemaflexibilität und die Möglichkeit, Datenbanken mühelos nach Bedarf zu skalieren. Es setzt kein Schema für die indizierten JSON-Dokumente voraus. Alle Dokumente in der Datenbank werden automatisch indiziert, ohne dass ein Schema oder die Erstellung sekundärer Indizes erwartet oder angefordert wird. DocumentDB ermöglicht mithilfe einer SQL-Sprache komplexe Ad-hoc-Abfragen, unterstützt wohl definierte Konsistenzebenen und bietet Unterstützung für JavaScript-Transaktionsverarbeitung mehrerer Dokumente mithilfe des vertrauten Programmiermodells gespeicherter Prozeduren, Trigger und benutzerdefinierter Funktionen (UDFs).

Als JSON-Datenbank bietet DocumentDB systemeigene Unterstützung von JSON-Dokumenten, um die einfache Iteration des Anwendungsschemas zu ermöglichen. Der Dienst berücksichtigt die Verbreitung von JSON und JavaScript und beseitigt den Konflikt zwischen anwendungsdefinierten Objekten und Datenbankschema. Die enge Integration von JavaScript ermöglicht es den Entwicklern zudem, Anwendungslogik auf effiziente Weise direkt im Datenbankmodul innerhalb einer Datenbanktransaktion auszuführen.

Azure DocumentDB bietet die folgenden wesentlichen Funktionen und Vorteile:

-	**Ad-Hoc-Abfragen mit vertrauter SQL-Syntax**: Heterogene JSON-Dokumente können in DocumentDB gespeichert und mithilfe einer vertrauten SQL-Syntax abgefragt werden. DocumentDB nutzt eine sperrfreie, protokollstrukturierte Indizierungstechnologie für die gleichzeitige Ausführung zahlreicher Vorgänge, um sämtliche Dokumentinhalte automatisch zu indizieren. Dies ermöglicht umfassende Echtzeitabfragen, ohne Schemahinweise, Sekundärindizes oder Sichten angeben zu müssen. Erfahren Sie mehr unter [Abfragen von DocumentDB](documentdb-sql-query.md). 

-	**JavaScript-Ausführung innerhalb der Datenbank**: Anwendungslogik kann mithilfe von Standard-JavaScript als gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen ausgedrückt werden. Auf diese Weise kann die Anwendungslogik auf Daten angewendet werden, ohne dass Sie sich Gedanken über den Konflikt zwischen Anwendung und Datenbankschema machen müssen. DocumentDB bietet eine umfassende transaktionale Ausführung von JavaScript-Anwendungslogik direkt im Datenbankmodul. Die enge Integration von JavaScript ermöglicht es, Einfüge-, Ersetzungs-, Lösch- und Auswahlvorgänge über ein JavaScript-Programm als isolierte Transaktion auszuführen. Weitere Einzelheiten finden Sie unter [Serverseitige DocumentDB-Programmierung](documentdb-programming.md).

-	**Optimierbare Konsistenzebenen:** Die Konsistenz kann über vier wohl definierte Ebenen abgestimmt werden, um für ein ausgewogenes Verhältnis von Konsistenz und Leistung zu sorgen. Für Abfragen und Lesevorgänge bietet DocumentDB vier verschiedene Konsistenzebenen – „strong“, „bounded-staleness“, „session“ und „eventual“. Mit diesen granularen, gut abgegrenzten Konsistenzebenen können fundierte Kompromisse zwischen Konsistenz,
-	Verfügbarkeit und Latenz geschlossen werden. Weitere Informationen finden Sie unter [Verwenden von Konsistenzebenen zum Maximieren der Verfügbarkeit und Leistung in DocumentDB](documentdb-consistency-levels.md).

-	**Vollständig verwaltet:** Sie müssen sich nicht mehr mit der Verwaltung von Datenbanken und Rechenressourcen befassen. Dank des vollständig verwalteten Microsoft Azure-Diensts müssen Sie sich nicht mit der Verwaltung virtueller Computer, der Bereitstellung und Konfiguration von Software oder mit komplexen Datenebenenupgrades herumschlagen. Alle Datenbanken werden automatisch gesichert und vor regionalen Ausfällen geschützt. Sie können einfach DocumentDB-Konten hinzufügen und nach Bedarf Kapazitäten bereitstellen. Dies ermöglicht es Ihnen, sich auf Ihre Anwendung zu konzentrieren, ohne sich mit dem Betrieb und der Verwaltung der Datenbank aufhalten zu müssen.

-	**Flexibel skalierbarer Durchsatz und Speicher:** Die DocumentDB-JSON-Datenbank lässt sich problemlos zentral hoch- und herunterskalieren, um Ihren Anwendungsanforderungen gerecht zu werden. Die Skalierung erfolgt über detaillierte Einheiten (Sammlungen) reservierten SSD-gesicherten Speichers und Durchsatzes. Sie können DocumentDB mit vorhersagbarer Leistung flexibel zentral skalieren, indem Sie zusätzliche Einheiten erstellen, wenn Ihre Anwendung wächst.

-	**Offen konzipiert:** Mithilfe Ihrer vorhandenen Kenntnisse und Tools finden Sie ganz schnell den Einstieg. Die Programmierung mit DocumentDB ist einfach und verständlich und erfordert keine neuen Tools oder benutzerdefinierten Erweiterungen für JSON oder JavaScript. Sie können auf sämtliche Datenbankfunktionen – einschließlich CRUD-, Abfrage- und JavaScript-Verarbeitung – über eine einfache HTTP-basierte RESTful-Schnittstelle zugreifen. DocumentDB bezieht vorhandene Formate, Sprachen und Standards ein und bietet gleichzeitig wichtige, darauf aufbauende Datenbankfunktionen.

Sie können DocumentDB für die Speicherung flexibler Datasets verwenden, die den Abruf von Abfragen und Transaktionsverarbeitung erfordern. Anwendungsszenarien können Benutzerdaten für interaktive webbasierte und mobile Anwendungen sowie Speicherung, Abruf und Verarbeitung von JSON-Anwendungsdaten beinhalten. Eine Datenbank kann eine Reihe von JSON-Dokumenten speichern, entsprechend ist DocumentDB bestens für Anwendungen geeignet, die webbasiert ausgeführt werden.

##<a name="data-management"></a>Azure DocumentDB-Ressourcen
Azure DocumentDB verwaltet Daten mithilfe wohl definierter Datenbankressourcen. Diese Ressourcen werden werden repliziert, um Hochverfügbarkeit zu bieten, und sind anhand ihres logischen URI eindeutig adressierbar. DocumentDB bietet ein einfaches HTTP-basiertes RESTful-Programmiermodell für alle Ressourcen.

Ein DocumentDB-Datenbankkonto ist ein eindeutiger Namespace, über den Sie Zugriff auf Azure DocumentDB erhalten. Um ein Datenbankkonto erstellen zu können, müssen Sie über ein Azure-Abonnement verfügen. Dadurch erhalten Sie Zugriff auf eine Vielzahl von Azure-Diensten.

Alle Ressourcen in DocumentDB werden als JSON-Dokumente modelliert und gespeichert. Ressourcen werden als Elemente verwaltet, bei denen es sich um JSON-Dokumente mit Metadaten handelt, und als Feeds, bei denen es sich um Auflistungen von Elementen handelt. Gruppen von Elementen sind in ihren jeweiligen Feeds enthalten.

Die folgende Abbildung veranschaulicht die Beziehung zwischen den DocumentDB-Ressourcen:

![Die hierarchische Beziehung zwischen Ressourcen in DocumentDB, einer JSON-NoSQL-Datenbank][1]

Ein Datenbankkonto besteht aus einer Reihe von Datenbanken, die jeweils mehrere Auflistungen umfassen, die wiederum jeweils gespeicherte Prozeduren, Trigger, UDFs, Dokumente und zugehörige Anhänge enthalten können. Einer Datenbank sind zudem Benutzer zugeordnet, die jeweils über eine Reihe von Berechtigungen für den Zugriff auf verschiedene andere Auflistungen, gespeicherte Prozeduren, Trigger, UDFs, Dokumente oder Anhänge verfügen. Während es sich bei Datenbanken, Benutzern, Berechtigungen und Auflistungen um systemdefinierte Ressourcen mit bekannten Schemas handelt, enthalten Dokumente, gespeicherte Prozeduren, Trigger, UDFs und Anhänge beliebige benutzerdefinierte JSON-Inhalte.

##<a name="develop"></a> Entwickeln mit Azure DocumentDB
DocumentDB stellt Ressourcen über eine REST-API zur Verfügung, die in jeder Sprache aufgerufen werden kann, die HTTP/HTTPS-Anfragen unterstützt. Zusätzlich bietet DocumentDB Programmierbibliotheken für einige gängige Sprachen. Diese Bibliotheken vereinfachen viele Aspekte der Arbeit mit Azure DocumentDB durch Verarbeitung von Details wie Zwischenspeicherung von Adressen, Ausnahmeverwaltung und automatischen Neuversuchen. Bibliotheken sind aktuell für die folgenden Sprachen und Plattformen erhältlich:

Herunterladen | Dokumentation
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [.NET-Bibliothek](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Node.js-Bibliothek](http://dl.windowsazure.com/documentDB/nodedocs/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Java-Bibliothek](http://dl.windowsazure.com/documentdb/javadoc/)
[JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) | [JavaScript-Bibliothek](http://dl.windowsazure.com/documentDB/jsclientdocs/)
– | [Serverseitiges JavaScript SDK](http://dl.windowsazure.com/documentDB/jsserverdocs/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Python-Bibliothek](http://dl.windowsazure.com/documentDB/pythondocs/)

Abgesehen von den grundlegenden Erstellungs-, Lese-, Aktualisierungs- und Löschvorgängen bietet Azure DocumentDB eine funktionsreiche SQL-Abfrageschnittstelle für den Abruf von JSON-Dokumenten und serverseitige Unterstützung für die transaktionale Ausführung von JavaScript-Anwendungslogik. Die Schnittstellen für die Abfrage- und Skriptausführung sind über alle Plattformbibliotheken verfügbar. Gleiches gilt für die REST-APIs.

### SQL-Abfrage
DocumentDB unterstützt die Abfrage von Dokumenten mithilfe einer SQL-Sprache, die im JavaScript-Typsystem verankert ist, sowie mithilfe von Ausdrücken, die umfassende hierarchische Abfragen unterstützen. Die DocumentDB-Abfragesprache ist eine einfache und dennoch effektive Schnittstelle für die Abfrage von JSON-Dokumenten. Die Sprache unterstützt einen Teil der ANSI SQL-Grammatik und sorgt für eine enge Integration von JavaScript-basierten Objekt-, Array-, Objektkonstruktions- und Funktionsaufrufen. DocumentDB stellt sein Abfragemodell ohne explizite Schema- oder Indizierungshinweise vom Entwickler bereit.

Benutzerdefinierte Funktionen (UDFs) können mit DocumentDB registriert und als Teil einer SQL-Abfrage referenziert werden, um so die Grammatik zu erweitern und die benutzerdefinierte Anwendungslogik zu unterstützen. Diese UDFs werden als JavaScript-Programme programmiert und in der Datenbank ausgeführt.

Für .NET-Entwickler bietet DocumentDB auch einen LINQ-Abfrageanbieter als Teil des [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx).

### Transaktionen und JavaScript-Ausführung
DocumentDB ermöglicht es Ihnen, Anwendungslogik als festgelegte Programme zu programmieren, die vollständig in JavaScript verfasst sind. Diese Programme werden für eine Auflistung registriert und unterstützen die Ausführung von Datenbankvorgängen in einer gegebenen Auflistung. JavaScript kann für die Ausführung als Trigger, gespeicherte Prozedur oder UDF registriert werden. Trigger und gespeicherte Prozeduren können Dokumente erstellen, lesen, aktualisieren und löschen. UDFs hingegen werden ohne Schreibzugriff auf die Auflistung als Teil der Abfrageausführung ausgeführt.

Die JavaScript-Ausführung in DocumentDB wurde anhand der gleichen Konzepte modelliert wie relationale Datenbanksysteme. JavaScript dient dabei als moderner Ersatz für Transact-SQL. Sämtliche JavaScript-Logik wird innerhalb einer umgebenden ACI-Transaktion mit Snapshotisolation ausgeführt. Während des Ausführungsverlaufs wird die gesamte Transaktion abgebrochen, wenn JavaScript eine Ausnahme auslöst.

## Nächste Schritte
Wenn Sie bereits ein Azure-Konto haben, Sie können die erste Schritte mit DocumentDB im [Azure-Vorschauportal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) unternehmen, indem Sie [ein DocumentDB-Datenbankkonto erstellen](documentdb-create-account.md).

Wenn Sie nicht über ein Konto verfügen, können Sie:

- Sich für eine [kostenlose Testversion von Azure](https://azure.microsoft.com/pricing/free-trial/) registrieren. Sie erhalten ein Guthaben von 200 US-Dollar für 30 Tage, in denen Sie alle Azure-Dienste ausprobieren können. 
- Wenn Sie ein MSDN-Abonnement haben, sind Sie für ein [kostenloses Azure-Guthaben von 150 US-Dollar pro Monat](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) berechtigt, das Sie für beliebige Azure-Dienste nutzen können. 

Wenn Sie mehr erfahren möchten, besuchen Sie unseren [Lernpfad](http://azure.microsoft.com/documentation/learning-paths/documentdb/), um alle Lernressourcen kennenzulernen, die Ihnen zur Verfügung stehen.


[1]: ./media/documentdb-introduction/json-database-resources1.png
 

<!---HONumber=Nov15_HO4-->