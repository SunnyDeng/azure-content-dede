<properties title="Frequently asked questions about DocumentDB" pageTitle="Frequently asked questions about DocumentDB | Azure" description="Answers to frequently asked questions about Azure DocumentDB databases. Learn about capacity and request units, and understand how to scale to your application needs." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, faq"   services="documentdb" solutions="data-management"   authors="bradsev" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# Häufig gestellte Fragen zu DocumentDB

## Themen in diesem Artikel

-   [Grundlagen von Azure DocumentDB][Grundlagen von Azure DocumentDB]
-   [Einrichten von Azure DocumentDB][Einrichten von Azure DocumentDB]
-   [Entwickeln mit Azure DocumentDB][Entwickeln mit Azure DocumentDB]

## <span id="fundamentals"></span></a> Grundlagen von Microsoft Azure DocumentDB

### Was ist Microsoft Azure DocumentDB?

Microsoft Azure DocumentDB ist eine umfassend skalierbare, als Dienst verfügbare NoSQL-Dokumentdatenbank, die umfangreiche Abfragen von nicht schematisierten Daten ermöglicht, konfigurierbare, zuverlässige Leistung bietet und eine schnelle Bereitstellung ermöglicht – alles das auf einer verwalteten Plattform, die auf der Leistungsfähigkeit und Reichweite von Microsoft Azure basiert. DocumentDB ist die geeignete Lösung für mobile und Webanwendungen, wenn vorhersagbarer Durchsatz, niedrige Latenz und schemafreie Datenmodelle wichtig sind. DocumentDB bietet Schemaflexibilität und umfassende Indizierung über ein systemeigenes JSON-Datenmodell und beinhaltet die Transaktionsunterstützung mehrerer Dokumente durch integriertes JavaScript.

Anweisungen zur Bereitstellung und Verwendung des Diensts finden Sie auf der [Dokumentationsseite für DocumentDB][Dokumentationsseite für DocumentDB].

### Was für eine Art Datenbank ist DocumentDB?

DocumentDB ist eine dokumentorientierte NoSQL-Datenbank, die Daten im JSON-Format speichert. Mit DocumentDB werden verschachtelte, selbständige Datenstrukturen unterstützt, die mithilfe einer umfassenden DocumentDB SQL-Abfragegrammatik abgefragt werden können. DocumentDB bietet hochleistungsfähige Transaktionsverarbeitung von serverseitigem JavaScript durch gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen. Die Datenbank unterstützt außerdem vom Entwickler optimierbare Konsistenzebenen mit den zugehörigen Leistungsstufen.

### Verfügen DocumentDB-Datenbanken über Tabellen wie ein RDBMS?

Nein, DocumentDB speichert Daten als JSON-Dokumentsammlungen. Weitere Informationen zu den DocumentDB-Ressourcen finden Sie im Artikel „Ressourcenmodell und Konzepte von DocumentDB“.

### Unterstützen DocumentDB-Datenbanken schemafreie Daten?

Ja, DocumentDB ermöglicht Anwendungen das Speichern beliebiger JSON-Dokumente ohne Schemadefinition oder -hinweise. Die Daten stehen unmittelbar zur Abfrage über die DocumentDB SQL-Abfrageschnittstelle zur Verfügung.

### Unterstützt DocumentDB ACID-Transaktionen?

Ja, DocumentDB unterstützt dokumentübergreifende Transaktionen, die in Form von gespeicherten JavaScript-Prozeduren und Triggern ausgedrückt werden. Die Transaktionen werden einer Sammlung zugeordnet und mit ACID-Semantik nach dem Prinzip „alles oder nichts“ ausgeführt. Sie sind dabei von anderem gleichzeitig ausgeführtem Code und Benutzeranforderungen isoliert. Falls bei der serverseitigen Ausführung des JavaScript-Anwendungscodes ein Ausnahmefehler auftritt, wird für die gesamte Transaktion ein Rollback durchgeführt.

### Was sind die typischen Anwendungsfälle für DocumentDB?

DocumentDB ist eine gute Wahl für neue mobile und Webanwendungen, wenn Skalierung, Leistung und Abfrage schemafreier Daten wichtig sind. DocumentDB ist gut für schnelle Entwicklungen und für die Unterstützung kontinuierlicher Iterationen von Anwendungsdatenmodellen geeignet. Anwendungen, die von Benutzern erzeugte Inhalte und Daten verwalten, sind ein häufiger Verwendungszweck von DocumentDB.

### Wie sind die Größen- und Kapazitätsbeschränkungen?

Jedes Azure DocumentDB-Konto unterstützt eine maximale Anzahl von Kapazitätseinheiten, die im Azure-Portal konfiguriert werden kann. Wenn Sie weitere Kapazitätseinheiten benötigen, wenden Sie sich an den Support, um das Kontingent zu erhöhen. Weitere Informationen zu Ressourcenkontingenten finden Sie im Artikel zu den DocumentDB-Diensteinschränkungen.

### Wie viel kostet Microsoft Azure DocumentDB?

Ausführliche Informationen dazu finden Sie im Artikel [DocumentDB Preisdetails][DocumentDB Preisdetails].

## <span id="setup"></span></a> Einrichten von Azure DocumentDB

### Wie registriere ich mich für Microsoft Azure DocumentDB?

Microsoft Azure DocumentDB (Vorschau) ist im neuen Azure-Vorschauportal verfügbar. Zuerst müssen Sie sich für ein Azure-Abonnement anmelden. Nachdem Sie sich für das Microsoft Azure-Abonnement registriert haben, können Sie in der Galerie ein DocumentDB-Konto zum Azure-Abonnement hinzufügen.

### Was ist ein Hauptschlüssel?

Ein Hauptschlüssel ist ein Sicherheitstoken für den Zugriff auf alle Ressourcen eines Kontos. Personen, die über diesen Schlüssel verfügen, haben Lese- und Schreibzugriff auf alle Ressourcen im Datenbankkonto. Gehen Sie beim Verteilen der Hauptschlüssel mit Bedacht vor.

### Wie erstelle ich eine Datenbank?

Sie können Datenbanken mit einer der DocumentDB-SDKs oder mithilfe der REST-APIs erstellen. Informationen zum Entwickeln von Anwendungen erhalten Sie auf der [DocumentDB-Dokumentationsseite][Dokumentationsseite für DocumentDB] im Abschnitt „Entwickeln“.

### Was ist eine Sammlung?

Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik. Abfragen und Transaktionen sind Sammlungen zugeordnet. Sie können heterogene (nicht zusammenhängende) JSON-Dokumente in einer Sammlung speichern, die alle automatisch indiziert werden.

### Gibt es Beschränkungen für Datenbanken und Sammlungen?

Jede erworbene Kapazitätseinheit ist mit einer Zuweisung an Datenbankspeicher und bereitgestelltem Durchsatz ausgestattet. Außerdem sind für jede vom Dienst verwaltete Ressource Kontingente angegeben. Weitere Informationen finden Sie in den DocumentDB-Diensteinschränkungen.

### Wie richte ich Benutzer und Berechtigungen ein?

Sie können Benutzer und Berechtigungen mit einer der DocumentDB-SDKs oder mithilfe der REST-APIs erstellen. Ausführliche Informationen zum Entwickeln von Anwendungen erhalten Sie auf der [DocumentDB-Dokumentationsseite][Dokumentationsseite für DocumentDB] im Abschnitt „Entwickeln“.

## <span id="develop"></span></a>Entwickeln mit Microsoft Azure DocumentDB

### Wie beginne ich beim Entwickeln mit DocumentDB?

In der Vorschau sind SDKs für .NET, Python, Node.js und JavaScript verfügbar. Entwickler können außerdem die RESTful HTTP-APIs zur Interaktion mit DocumentDB-Ressourcen von einer Vielzahl von Plattformen und Sprachen nutzen. Informationen zur Verwendung dieser SDKs finden Sie auf der [DocumentDB-Dokumentationsseite][Dokumentationsseite für DocumentDB] im Abschnitt „Entwickeln“.

### Unterstützt DocumentDB SQL?

Die DocumentDB SQL-Abfragesprache bietet umfassende hierarchische und relationale Operatoren sowie die Erweiterbarkeit über JavaScript-basierte benutzerdefinierte Funktionen. Die JSON-Grammatik ermöglicht die Gestaltung von JSON-Dokumenten als Baumstrukturen mit den Beschriftungen als Strukturknoten. Dies wird sowohl beim automatischen Indizierungsverfahren als auch beim SQL-Abfragedialekt von DocumentDB ausgenutzt. Ausführliche Informationen zur Verwendung der SQL-Grammatik finden Sie im Artikel [Abfragen mit DocumentDB SQL][Abfragen mit DocumentDB SQL].

### Welche Datentypen werden von DocumentDB unterstützt?

Die in DocumentDB unterstützten primitiven Datentypen sind dieselben wie in JSON. JSON hat ein einfaches Typsystem, das aus Zeichenfolgen, Zahlen (IEEE754 mit doppelter Genauigkeit), booleschen Werten („true“ oder „false“) und Nullen besteht. Komplexere Datentypen wie DateTime, Guid, Int64 und Geometrie können sowohl in JSON als auch in DocumentDB durch das Erstellen von verschachtelten Objekten mit dem { }-Operator sowie Arrays mit dem [ ]-Operator dargestellt werden.

### Wie stellt DocumentDB Parallelität zur Verfügung?

DocumentDB unterstützt die Steuerung für optimistische Parallelität (OCC) durch HTTP-Entitätstags oder ETags. Jede DocumentDB-Ressource verfügt über einen ETag, und DocumentDB-Clients schließen ihre aktuelle Leseversion in Schreibanforderungen ein. Falls der ETag aktuell ist, wird die Änderung festgeschrieben. Wurde der Wert extern geändert, lehnt der Server den Schreibvorgang mit dem Antwortcode „HTTP 412“ für eine nicht zutreffende Voraussetzung ab. Clients müssen die neueste Version der Ressource lesen und die Anforderung wiederholen.

### Wie führe ich Transaktionen in DocumentDB durch?

DocumentDB unterstützt sprachintegrierte Transaktionen über gespeicherte JavaScript-Prozeduren und Trigger. Alle Datenbankoperationen in Skripts werden unter Momentaufnahmeisolation zur Sammlung zugeordnet. Eine Momentaufnahme der Dokumentversionen (ETags) wird zu Beginn der Transaktion angefertigt und erst dann festgeschrieben, wenn das Skript erfolgreich ausgeführt wurde. Falls JavaScript einen Fehler ausgibt, wird für die Transaktion ein Rollback ausgeführt.

### Wie kann ich Masseneinfügung für Dokumente in DocumentDB ausführen?

Die Unterstützung gespeicherter Prozeduren in DocumentDB bietet eine effiziente Möglichkeit für Stapeleinfügungen. Durch Entwicklung einer einfachen gespeicherten JavaScript-Prozedur, die Dokumente empfängt und einfügt, können Sie Masseneinfügungen ausführen. Dies hat den zusätzlichen Vorteil, dass die Masseneinfügung als Transaktion ausgeführt wird und die Sammlung einheitlich bleibt. Informationen zum Programmierungsmodell finden Sie auf der [DocumentDB-Dokumentationsseite][Dokumentationsseite für DocumentDB] im Abschnitt „Entwickeln“.

### Unterstützt DocumentDB Ressourcenlink-Zwischenspeicherung?

Ja. Da es sich bei DocumentDB um einen RESTful-Dienst handelt, sind Ressourcenlinks unveränderbar und können zwischengespeichert werden. DocumentDB-Clients können einen „If-None-Match“-Header für Lesevorgänge für jede Ressource wie Dokumente oder Sammlungen festlegen und ihre lokalen Kopien nur dann aktualisieren, wenn sich die Serverversion geändert hat.

  [Grundlagen von Azure DocumentDB]: #fundamentals
  [Einrichten von Azure DocumentDB]: #setup
  [Entwickeln mit Azure DocumentDB]: #develop
  [Dokumentationsseite für DocumentDB]: http://go.microsoft.com/fwlink/p/?LinkID=402319
  [DocumentDB Preisdetails]: http://go.microsoft.com/fwlink/p/?LinkID=402317
  [Abfragen mit DocumentDB SQL]: ../documentdb-sql-query/
