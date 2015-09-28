<properties 
	pageTitle="Datenbankfragen zu DocumentDB – Häufig gestellte Fragen | Microsoft Azure" 
	description="Erhalten Sie Antworten auf häufig gestellte Fragen zum Azure DocumentDB-NoSql-Dokumentdatenbankdienst. Beantworten Sie Datenbankfragen zu Kapazität, Leistung und Skalierung." 
	keywords="Database questions, frequently asked questions, documentdb, azure, Microsoft azure"
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
	ms.date="09/01/2015" 
	ms.author="mimig"/>


#Häufig gestellte Fragen zu DocumentDB

## Datenbankfragen zu den Grundlagen von Microsoft Azure DocumentDB

### Was ist Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB ist eine umfassend skalierbare, als Dienst verfügbare NoSQL-Dokumentdatenbank, die umfangreiche Abfragen von nicht schematisierten Daten ermöglicht, konfigurierbare, zuverlässige Leistung bietet und eine schnelle Bereitstellung ermöglicht – und all das auf einer verwalteten Plattform, die auf der Leistungsfähigkeit und Reichweite von Microsoft Azure basiert. DocumentDB ist die geeignete Lösung für mobile und Webanwendungen, wenn vorhersagbarer Durchsatz, niedrige Latenz und schemafreie Datenmodelle wichtige Anforderungen sind. DocumentDB bietet Schemaflexibilität und umfassende Indizierung über ein systemeigenes JSON-Datenmodell und beinhaltet die Transaktionsunterstützung mehrerer Dokumente durch integriertes JavaScript.
  
Weitere Datenbankfragen, -antworten und -anweisungen zur Bereitstellung und Verwendung des Diensts finden Sie auf der [Dokumentationsseite für DocumentDB](http://azure.microsoft.com/documentation/services/documentdb/).

### Was für eine Art Datenbank ist DocumentDB?
DocumentDB ist eine dokumentorientierte NoSQL-Datenbank, die Daten im JSON-Format speichert. DocumentDB unterstützt geschachtelte, eigenständige Datenstrukturen, die mithilfe einer umfassenden DocumentDB-[SQL-Abfragegrammatik](documentdb-sql-query.md) abgefragt werden können. DocumentDB eine bietet hochleistungsfähige Transaktionsverarbeitung von serverseitigem JavaScript durch [gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen](documentdb-programming.md). Die Datenbank unterstützt außerdem vom Entwickler optimierbare Konsistenzebenen mit den zugehörigen [Leistungsebenen](documentdb-performance-levels.md).
 
### Verfügen DocumentDB-Datenbanken über Tabellen wie eine relationale Datenbank (RDBMS)?
Nein, DocumentDB speichert Daten als JSON-Dokumentsammlungen. Weitere Informationen zu den DocumentDB-Ressourcen finden Sie im Artikel [Ressourcenmodell und Konzepte von DocumentDB](documentdb-resources.md).

### Unterstützen DocumentDB-Datenbanken schemafreie Daten?
Ja, DocumentDB ermöglicht Anwendungen das Speichern beliebiger JSON-Dokumente ohne Schemadefinition oder -hinweise. Die Daten stehen unmittelbar zur Abfrage über die DocumentDB SQL-Abfrageschnittstelle zur Verfügung.

### Unterstützt DocumentDB ACID-Transaktionen?
Ja, DocumentDB unterstützt dokumentübergreifende Transaktionen, die in Form von gespeicherten JavaScript-Prozeduren und Triggern ausgedrückt werden. Die Transaktionen werden einer Sammlung zugeordnet und mit ACID-Semantik nach dem Prinzip „alles oder nichts“ ausgeführt. Sie sind dabei von anderem gleichzeitig ausgeführtem Code und Benutzeranforderungen isoliert. Falls bei der serverseitigen Ausführung des JavaScript-Anwendungscodes ein Ausnahmefehler auftritt, wird für die gesamte Transaktion ein Rollback durchgeführt.

### Was sind die typischen Anwendungsfälle für DocumentDB?  
DocumentDB ist eine gute Wahl für neue Web- und mobile Anwendungen, wenn Skalierung, Leistung und Abfrage schemafreier Daten wichtig sind. DocumentDB eignet sich für schnelle Entwicklungen und die Unterstützung kontinuierlicher Iterationen von Anwendungsdatenmodellen. Anwendungen, die von Benutzern erzeugte Inhalte und Daten verwalten, sind ein [häufiger Anwendungsfall von DocumentDB](documentdb-use-cases.md).

### Was sind die Größenbeschränkungen von DocumentDB?
DocumentDB-Konten können hinsichtlich Speicher und Durchsatz durch Hinzufügen von Sammlungen skaliert werden. Die Dienstkontingente für die Anzahl der Sammlungen finden Sie unter [DocumentDB-Grenzen](documentdb-limits.md). Wenn Sie weitere Sammlungen benötigen, [wenden Sie sich an den Support](documentdb-increase-limits.md), um das Kontokontingent zu erhöhen.

### Wie viel kostet Microsoft Azure DocumentDB?
Ausführliche Informationen dazu finden Sie auf der Seite [DocumentDB – Preise](http://go.microsoft.com/fwlink/p/?LinkID=402317). Die Nutzungsgebühren für DocumentDB werden bestimmt von der verwendeten Anzahl von Sammlungen, der Anzahl der Stunden, die die Sammlungen online waren, und der [Leistungsebene](documentdb-performance-levels.md) jeder Sammlung.

### Gibt es eine kostenlose Testversion?
Wenn Azure für Sie neu ist, können Sie sich für eine [kostenlose Testversion von Azure](https://azure.microsoft.com/pricing/free-trial/) registrieren. Sie erhalten ein Guthaben von 200 US-Dollar für 30 Tage, in denen Sie alle Azure-Dienste ausprobieren können. Wenn Sie ein MSDN-Abonnement besitzen, haben Sie Anspruch auf ein [kostenloses Azure-Guthaben von 150 US-Dollar pro Monat](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), das Sie für beliebige Azure-Dienste nutzen können.

### Wie kann ich zusätzliche Hilfe zu DocumentDB erhalten?
Wenn Sie Hilfe benötigen, kontaktieren Sie uns auf [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb) oder in den [MSDN-Foren für Entwickler zu Azure DocumentDB](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB), oder terminieren Sie einen [1:1-Chat mit dem Entwicklungsteam von DocumentDB](http://www.askdocdb.com/). Um bei den neuesten Features von DocumentDB auf dem Laufenden zu bleiben, folgen Sie uns auf [Twitter](https://twitter.com/DocumentDB).

## Einrichten von Microsoft Azure DocumentDB

### Wie registriere ich mich für Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB steht im [Azure-Vorschauportal][azure-portal] zur Verfügung. Zuerst müssen Sie sich für ein Azure-Abonnement anmelden. Nachdem Sie sich für ein Microsoft Azure-Abonnement registriert haben, können Sie Ihrem Azure-Abonnement ein DocumentDB-Konto hinzufügen. Anweisungen zum Hinzufügen eines DocumentDB-Kontos finden Sie unter [Erstellen eines DocumentDB-Datenbankkontos](documentdb-create-account.md).

### Was ist ein Hauptschlüssel?
Ein Hauptschlüssel ist ein Sicherheitstoken für den Zugriff auf alle Ressourcen eines Kontos. Personen, die über diesen Schlüssel verfügen, haben Lese- und Schreibzugriff auf alle Ressourcen im Datenbankkonto. Gehen Sie beim Verteilen der Hauptschlüssel mit Bedacht vor. Der primäre und der sekundäre Hauptschlüssel stehen auf dem Blatt **Schlüssel** im [Azure-Vorschauportal][azure-portal] zur Verfügung. Weitere Informationen zu Schlüsseln finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Zugriffsschlüsseln](documentdb-manage-account.md#keys).

### Wie erstelle ich eine Datenbank?
Datenbanken können wie folgt erstellt werden: über das [Azure-Vorschauportal]() gemäß [Erstellen einer DocumentDB-Datenbank](documentdb-create-database.md), über eines der [DocumentDB SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) oder über die [REST-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### Was ist eine Sammlung?
Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik. Abfragen und Transaktionen sind Sammlungen zugeordnet. Sie können heterogene (nicht zusammenhängende) JSON-Dokumente in einer Sammlung speichern, die alle automatisch indiziert werden.

Sammlungen sind zudem die Abrechnungseinheiten für DocumentDB. Die monatlichen Nutzungsgebühren für DocumentDB werden bestimmt von der verwendeten Anzahl von Sammlungen, der Anzahl der Stunden, die die Sammlungen online waren, und der [Leistungsebene](documentdb-performance-levels.md) jeder Sammlung. Weitere Informationen finden Sie unter [DocumentDB-Preise](https://azure.microsoft.com/pricing/details/documentdb/).

### Gibt es Beschränkungen für Datenbanken und Sammlungen?
Jede Sammlung ist mit einer Zuweisung an Datenbankspeicher und bereitgestelltem Durchsatz auf einer der unterstützten [Leistungsebenen](documentdb-performance-levels.md) ausgestattet. Außerdem sind für jede vom Dienst verwaltete Ressource Kontingente angegeben. Eine Übersicht aller Grenzwerte finden Sie unter [DocumentDB-Grenzen und -Kontingente](documentdb-limits.md). Informationen zum Beantragen einer Änderung Ihrer Kontobegrenzungen finden Sie unter [Anfordern von erhöhten DocumentDB-Kontolimits](documentdb-increase-limits.md).

### Wie richte ich Benutzer und Berechtigungen ein?
Sie können Benutzer und Berechtigungen mit einem der [DocumentDB SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) oder mithilfe der [REST-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) erstellen.

## Datenbankfragen zum Entwickeln mit Microsoft Azure DocumentDB

### Wie beginne ich beim Entwickeln mit DocumentDB?
[SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) sind für .NET, Python, Node.js, JavaScript und Java verfügbar. Entwickler können außerdem die [RESTful HTTP-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) zur Interaktion mit DocumentDB-Ressourcen auf einer Vielzahl von Plattformen und mit verschiedenen Sprachen nutzen.

Beispiele der [.NET](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples) und [Python](https://github.com/Azure/azure-documentdb-python) SDKs für DocumentDB sind auf GitHub verfügbar.

### Unterstützt DocumentDB SQL?
Die DocumentDB SQL-Abfragesprache bietet umfassende hierarchische und relationale Operatoren sowie die Erweiterbarkeit über JavaScript-basierte benutzerdefinierte Funktionen. Die JSON-Grammatik ermöglicht die Modellierung von JSON-Dokumenten als Struktur mit den Beschriftungen als Strukturknoten. Dies wird sowohl von den automatischen Indizierungstechniken als auch beim SQL-Abfragedialekt von DocumentDB verwendet. Ausführliche Informationen zur Verwendung der SQL-Grammatik finden Sie im Artikel [Abfragen von DocumentDB][query].

### Welche Datentypen werden von DocumentDB unterstützt?
Die in DocumentDB unterstützten primitiven Datentypen sind dieselben wie in JSON. JSON hat ein einfaches Typsystem, das aus Zeichenfolgen, Zahlen (IEEE754 mit doppelter Genauigkeit) und booleschen Werten – "true", "false" und NULL-Werten – besteht. Komplexere Datentypen wie DateTime, Guid, Int64 und Geometrie können sowohl in JSON als auch in DocumentDB durch das Erstellen von verschachtelten Objekten mit dem { }-Operator sowie Arrays mit dem [ ]-Operator dargestellt werden.

### Wie stellt DocumentDB Parallelität zur Verfügung?
DocumentDB unterstützt die Steuerung für optimistische Parallelität (OCC) durch HTTP-Entitätstags oder ETags. Jede DocumentDB-Ressource verfügt über einen ETag, und DocumentDB-Clients schließen ihre aktuelle Leseversion in Schreibanforderungen ein. Falls der ETag aktuell ist, wird die Änderung festgeschrieben. Wurde der Wert extern geändert, lehnt der Server den Schreibvorgang mit dem Antwortcode „HTTP 412“ für eine nicht zutreffende Voraussetzung ab. Clients müssen die neueste Version der Ressource lesen und die Anforderung wiederholen.

### Wie führe ich Transaktionen in DocumentDB durch?
DocumentDB unterstützt sprachintegrierte Transaktionen über gespeicherte JavaScript-Prozeduren und Trigger. Alle Datenbankoperationen in Skripts werden unter Momentaufnahmeisolation zur Sammlung zugeordnet. Eine Momentaufnahme der Dokumentversionen (ETags) wird zu Beginn der Transaktion angefertigt und erst dann festgeschrieben, wenn das Skript erfolgreich ausgeführt wurde. Falls JavaScript einen Fehler ausgibt, wird für die Transaktion ein Rollback ausgeführt. Weitere Einzelheiten finden Sie unter [Serverseitige DocumentDB-Programmierung](documentdb-programming.md).

### Wie kann ich Masseneinfügung für Dokumente in DocumentDB ausführen? 
Es gibt drei Möglichkeiten zur Masseneinfügung von Dokumenten in DocumentDB:

- Das Datenmigrationstool, beschrieben in [Importieren von Daten in DocumentDB](documentdb-import-data.md).
- Den Dokument-Explorer im Azure-Vorschauportal, beschrieben in [Massenhinzufügen von Dokumenten mit dem Dokument-Explorer](documentdb-view-json-document-explorer.md#BulkAdd).
- Gespeicherte Prozeduren, beschrieben in [DocumentDB-serverseitige Programmierung](documentdb-programming.md).

### Unterstützt DocumentDB Ressourcenlink-Zwischenspeicherung?
Ja. Da es sich bei DocumentDB um einen RESTful-Dienst handelt, sind Ressourcenlinks unveränderbar und können zwischengespeichert werden. DocumentDB-Clients können einen „If-None-Match“-Header für Lesevorgänge für jede Ressource wie Dokumente oder Sammlungen festlegen und ihre lokalen Kopien nur dann aktualisieren, wenn sich die Serverversion geändert hat.




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=Sept15_HO3-->