<properties title="Einf&uuml;hrung in Microsoft Azure DocumentDB" pageTitle="Einf&uuml;hrung in Microsoft Azure DocumentDB | Azure" description="Hier erfahren Sie mehr &uuml;ber Azure DocumentDB und dessen Wert f&uuml;r Cloud- und Mobilanwendungen. Zudem erfahren Sie, wie es Daten verwaltet und wie Sie es in der Anwendungsentwicklung verwenden." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

# Einführung in Microsoft Azure DocumentDB

Dieser Artikel enthält eine Einführung in Microsoft Azure DocumentDB für Entwickler, IT-Fachleute und geschäftliche Entscheidungsträger. In diesem Artikel lernen Sie Folgendes:

-   Was Azure DocumentDB ist und welchen Nutzen der Dienst für Ihre cloudbasierten und mobilen Anwendungen bietet
-   Wie Ihre Daten in Azure DocumentDB verwaltet werden
-   Wie Sie mithilfe von Azure DocumentDB auf Daten zugreifen und Anwendungen entwickeln
-   Welche Schritte erforderlich ist, um Ihre erste Azure DocumentDB-Anwendung zu erstellen

## Was ist Azure DocumentDB?

Moderne Anwendungen produzieren, verwenden und reagieren schnell auf sehr große Datenmengen. Diese Anwendungen entwickeln sich rasant – ebenso wie das ihnen zugrunde liegende Datenschema. Um dem zu begegnen, gehen Entwickler zunehmend dazu über, schemafreie NoSQL-Datenbanken als einfache, schnelle und flexible Lösungen für die Speicherung und Verarbeitung von Daten einzusetzen. Dabei können Anwendungsdatenmodelle und unstrukturierte Datenfeeds weiterhin schnell durchlaufen werden. Viele schemafreie Datenbanken ermöglichen jedoch keine nicht trivialen Abfragen und Transaktionsverarbeitung und erschweren so eine moderne Datenverwaltung. Microsoft hat Azure DocumentDB entwickelt, um diese Funktionen für die Verwaltung schemafreier Daten bereitzustellen.

Microsoft Azure DocumentDB ist ein NoSQL-Dokumentdatenbankdienst, der für moderne mobile und webbasierte Anwendungen entwickelt wurde. DocumentDB bietet gleichbleibend schnelle Lese- und Schreibvorgänge, Schemaflexibilität und die Möglichkeit, Datenbanken mühelos nach Bedarf zu skalieren. DocumentDB ermöglicht mithilfe des SQL-Dialekts komplexe Ad-hoc-Abfragen, unterstützt wohl definierte Konsistenzebenen und bietet Unterstützung für JavaScript-Transaktionsverarbeitung mehrerer Dokumente mithilfe des vertrauten Programmiermodells gespeicherter Prozeduren, Trigger und benutzerdefinierter Funktionen (UDFs).

Azure DocumentDB bietet systemeigene Unterstützung von JSON-Dokumenten, um die einfache Iteration des Anwendungsschemas zu ermöglichen. Der Dienst berücksichtigt die Verbreitung von JSON und JavaScript und beseitigt die Inkongruenz zwischen Anwendungstypsystem und Datenbankschema. Die enge Integration von JavaScript ermöglicht es den Entwicklern zudem, Anwendungslogik auf effiziente Weise direkt im Datenbankmodul innerhalb einer Datenbanktransaktion auszuführen.

Azure DocumentDB bietet die folgenden wesentlichen Funktionen und Vorteile:

-   **Ad-Hoc-Abfragen mit vertrauter SQL-Syntax:** Heterogene JSON-Dokumente können in DocumentDB gespeichert und mithilfe einer vertrauten SQL-Syntax abgefragt werden. DocumentDB nutzt eine sperrfreie, protokollstrukturierte Indizierungstechnologie für die gleichzeitige Ausführung zahlreicher Vorgänge, um sämtliche Dokumentinhalte automatisch zu indizieren. Dies ermöglicht umfassende Echtzeitabfragen, ohne Schemahinweise, Sekundärindizes oder Sichten angeben zu müssen.

-   **JavaScript-Ausführung innerhalb der Datenbank:** Anwendungslogik kann mithilfe von Standard-JavaScript als gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen ausgedrückt werden. Auf diese Weise kann die Anwendungslogik auf JSON-Daten angewendet werden, ohne dass Sie sich Gedanken über die Inkongruenz von Anwendung und Datenbankschema machen müssen. DocumentDB bietet eine umfassende transaktionale Ausführung von JavaScript-Anwendungslogik direkt im Datenbankmodul. Die enge Integration von JavaScript ermöglicht es, Einfüge-, Ersetzungs-, Lösch- und Auswahlvorgänge über ein JavaScript-Programm als isolierte Transaktion auszuführen.

-   **Optimierbare Konsistenzebenen:** Die Konsistenz kann über vier wohl definierte Ebenen abgestimmt werden, um für ein ausgewogenes Verhältnis von Konsistenz und Leistung zu sorgen. Für Abfragen und Lesevorgänge bietet DocumentDB vier verschiedene Konsistenzebenen – "strong", "bounded-staleness", "session" und "eventual". Mit diesen granularen, gut abgegrenzten Konsistenzebenen können fundierte Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz geschlossen werden.

-   **Vollständig verwaltet:** Sie müssen sich nicht mehr mit der Verwaltung von Datenbanken und Rechenressourcen befassen. Dank des vollständig verwalteten Microsoft Azure-Diensts müssen Sie sich nicht mit der Verwaltung virtueller Computer, der Bereitstellung und Konfiguration von Software oder mit komplexen Datenebenenupgrades herumschlagen. Alle Datenbanken werden automatisch gesichert und vor regionalen Ausfällen geschützt. Sie können einfach DocumentDB-Konten hinzufügen und nach Bedarf Kapazitäten bereitstellen. Dies ermöglicht es Ihnen, sich auf Ihre Anwendung zu konzentrieren, ohne sich mit dem Betrieb und der Verwaltung der Datenbank aufhalten zu müssen.

-   **Flexibel skalierbarer Durchsatz und Speicher:** DocumentDB kann einfach skaliert werden, um Ihren Anwendungsanforderungen gerecht zu werden. Die Skalierung erfolgt über detaillierte Einheiten reservierten SSD-gesicherten Speichers und Durchsatzes. Sie können DocumentDB mit vorhersagbarer Leistung flexibel skalieren, indem Sie zusätzliche Kapazitätseinheiten erwerben, wenn Ihre Anwendung wächst.

-   **Offen konzipiert:** Mithilfe Ihrer vorhandenen Kenntnisse und Tools finden Sie ganz schnell den Einstieg. Die Programmierung mit DocumentDB ist einfach und verständlich und erfordert keine neuen Tools oder benutzerdefinierten Erweiterungen für JSON oder JavaScript. Sie können auf sämtliche Datenbankfunktionen – einschließlich CRUD-, Abfrage- und JavaScript-Verarbeitung – über eine einfache HTTP-basierte RESTful-Schnittstelle zugreifen. DocumentDB bezieht vorhandene Formate, Sprachen und Standards ein und bietet gleichzeitig wichtige, darauf aufbauende Datenbankfunktionen.

Sie können Azure DocumentDB für die Speicherung flexibler Datasets verwenden, die den Abruf von Abfragen und Transaktionsverarbeitung erfordern. Anwendungsszenarien können Benutzerdaten für interaktive webbasierte und mobile Anwendungen sowie Speicherung, Abruf und Verarbeitung von JSON-Anwendungsdaten beinhalten. Eine Datenbank kann eine Reihe von JSON-Dokumenten speichern, entsprechend ist DocumentDB bestens für Anwendungen geeignet, die webbasiert ausgeführt werden.

## Azure DocumentDB-Ressourcen

Azure DocumentDB verwaltet Daten mithilfe wohl definierter Datenbankressourcen. Diese Ressourcen werden werden repliziert, um Hochverfügbarkeit zu bieten, und sind anhand ihres logischen URI eindeutig adressierbar. DocumentDB bietet ein einfaches HTTP-basiertes RESTful-Programmiermodell für alle Ressourcen.

Ein DocumentDB-Datenbankkonto ist ein eindeutiger Namespace, über den Sie Zugriff auf Azure DocumentDB erhalten. Um ein Datenbankkonto erstellen zu können, müssen Sie über ein Azure-Abonnement verfügen. Dies ist ein Plan, der Ihnen Zugriff auf eine Vielzahl von Azure-Diensten verschafft.

Alle Ressourcen in DocumentDB werden als JSON-Dokumente modelliert und gespeichert. Ressourcen werden als Elemente verwaltet, bei denen es sich um JSON-Dokumente mit Metadaten handelt, und als Feeds, bei denen es sich um Auflistungen von Elementen handelt. Gruppen von Elementen sind in ihren jeweiligen Feeds enthalten.

Die folgende Abbildung veranschaulicht die Beziehung zwischen den Azure DocumentDB-Ressourcen:

![][0]

Ein Datenbankkonto besteht aus einer Reihe von Datenbanken, die jeweils mehrere Auflistungen umfassen, die wiederum jeweils gespeicherte Prozeduren, Trigger, UDFs, Dokumente und zugehörige Anhänge enthalten können. Einer Datenbank sind zudem Benutzer zugeordnet, die jeweils über eine Reihe von Berechtigungen für den Zugriff auf verschiedene andere Auflistungen, gespeicherte Prozeduren, Trigger, UDFs, Dokumente oder Anhänge verfügen. Während es sich bei Datenbanken, Benutzern, Berechtigungen und Auflistungen um systemdefinierte Ressourcen mit bekannten Schemas handelt, enthalten Dokumente, gespeicherte Prozeduren, Trigger, UDFs und Anhänge beliebige benutzerdefinierte JSON-Inhalte.

## Entwickeln mit Azure DocumentDB

Azure DocumentDB stellt Ressourcen über eine REST-API zur Verfügung, die in jeder Sprache aufgerufen werden kann, die HTTP/HTTPS-Anfragen unterstützt. Zusätzlich bietet Azure DocumentDB Programmierbibliotheken für einige gängige Sprachen. Diese Bibliotheken vereinfachen viele Aspekte der Arbeit mit Azure DocumentDB durch Verarbeitung von Details wie Zwischenspeicherung von Adressen, Ausnahmeverwaltung und automatischen Neuversuchen. Bibliotheken sind aktuell für die folgenden Sprachen und Plattformen erhältlich. Weitere sind in Planung:

-   [.NET][.NET]
-   [Node.js][Node.js]
-   [JavaScript][JavaScript]
-   [Python][Python]

Abgesehen von den grundlegenden Erstellungs-, Lese-, Aktualisierungs- und Löschvorgängen bietet Azure DocumentDB eine funktionsreiche SQL-Abfrageschnittstelle für den Abruf von JSON-Dokumenten und serverseitige Unterstützung für die transaktionale Ausführung von JavaScript-Anwendungslogik. Die Schnittstellen für die Abfrage- und Skriptausführung sind über alle Plattformbibliotheken verfügbar. Gleiches gilt für die REST-APIs.

### SQL-Abfrage

Azure DocumentDB unterstützt die Abfrage von Dokumenten mithilfe einer SQL-Sprache, die im JavaScript-Typsystem verankert ist, sowie mithilfe von Ausdrücken, die umfassende hierarchische Abfragen unterstützen. Die DocumentDB-Abfragesprache ist eine einfache und dennoch effektive Schnittstelle für die Abfrage von JSON-Dokumenten. Die Sprache unterstützt einen Teil der ANSI SQL-Grammatik und sorgt für eine enge Integration von JavaScript-basierten Objekt-, Array-, Objektkonstruktions- und Funktionsaufrufen. DocumentDB stellt sein Abfragemodell ohne explizite Schema- oder Indizierungshinweise vom Entwickler bereit.

Benutzerdefinierte Funktionen (UDFs) können mit Azure DocumentDB registriert und als Teil einer SQL-Abfrage referenziert werden, um so die Grammatik zu erweitern und die benutzerdefinierte Anwendungslogik zu unterstützen. Diese UDFs werden als JavaScript-Programme programmiert und in der Datenbank ausgeführt.

Für .NET-Entwickler bietet Azure DocumentDB auch einen LINQ-Abfrageanbieter als Teil des .NET SDK.

### Transaktionen und JavaScript-Ausführung

Azure DocumentDB ermöglicht es Ihnen, Anwendungslogik als festgelegte Programme zu programmieren, die vollständig in JavaScript verfasst sind. Diese Programme werden für eine Auflistung registriert und unterstützen die Ausführung von Datenbankvorgängen in einer gegebenen Auflistung. Anwendungs-JavaScript kann für die Ausführung als Trigger, gespeicherte Prozedur oder UDF registriert werden. Trigger und gespeicherte Prozeduren können Dokumente erstellen, lesen, aktualisieren und löschen. UDFs hingegen werden ohne Schreibzugriff auf die Auflistung als Teil der Abfrageausführung ausgeführt.

Die JavaScript-Ausführung in DocumentDB wurde anhand der gleichen Konzepte modelliert wie relationale Datenbanksysteme. JavaScript dient dabei als moderner Ersatz für T-SQL. Sämtliche JavaScript-Logik wird innerhalb einer umgebenden ACI-Transaktion mit Snapshotisolation ausgeführt. Während des Ausführungsverlaufs wird die gesamte Transaktion abgebrochen, wenn JavaScript eine Ausnahme auslöst.

## Nächste Schritte

Informationen zu den ersten Schritten mit Azure DocumentDB finden Sie in folgenden Ressourcen:

-   [DocumentDB jetzt testen][DocumentDB jetzt testen]
-   [Informationen zu DocumentDB-Konzepten][Informationen zu DocumentDB-Konzepten]
-   [Interagieren mit DocumentDB][Interagieren mit DocumentDB]
-   [Erstellen eines Datenbankkontos][Erstellen eines Datenbankkontos]
-   [Erste Schritte mit DocumentDB][Erste Schritte mit DocumentDB]

  [0]: ./media/documentdb-introduction/intro.png
  [.NET]: http://go.microsoft.com/fwlink/?LinkID=402989
  [Node.js]: http://go.microsoft.com/fwlink/?LinkID=402990
  [JavaScript]: http://go.microsoft.com/fwlink/?LinkID=402991
  [Python]: http://go.microsoft.com/fwlink/?LinkID=402992
  [DocumentDB jetzt testen]: https://portal.azure.com/#gallery/Microsoft.DocumentDB
  [Informationen zu DocumentDB-Konzepten]: /documentation/articles/documentdb-resources/
  [Interagieren mit DocumentDB]: /documentation/articles/documentdb-interactions-with-resources/
  [Erstellen eines Datenbankkontos]: /documentation/articles/documentdb-create-account/
  [Erste Schritte mit DocumentDB]: /documentation/articles/documentdb-get-started/
