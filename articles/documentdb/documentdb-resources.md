<properties 
	pageTitle="Ressourcenmodell und Konzepte von DocumentDB | Azure" 
	description="Microsoft Azure DocumentDB ist eine vollständig verwaltete NoSQL-Dokumentdatenbank, die ein hierarchisches Modell aus Datenbankkonten, Datenbanken, Sammlungen, gespeicherten Prozeduren, Triggern, benutzerdefinierten Funktionen (User Defined Functions, UDFs), Dokumenten, Anlagen, Medien, Benutzern und Berechtigungen zur Ressourcenverwaltung einsetzt."  
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="anhoh"/>

#Ressourcenmodell und Konzepte von DocumentDB

Die in DocumentDB verwalteten Datenbankentitäten werden als **Ressourcen** bezeichnet. Jede Ressource wird durch einen logischen URI eindeutig identifiziert. Sie können über standardmäßige HTTP-Verben, Anforderungs-/Antwortheader und Statuscodes mit den Ressourcen interagieren.

Nach dem Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

- Was sind die DocumentDB-Ressourcen?
- Wie gestaltet sich die Hierarchie der DocumentDB-Ressourcen?
- Wie unterscheiden sich systemdefinierte Ressourcen von benutzerdefinierten?
- Wie adressiere ich eine Ressource?
- Wie arbeite ich mit Sammlungen?
- Wie arbeite ich mit gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen?

##Hierarchisches Ressourcenmodell
Wie das folgende Diagramm veranschaulicht, besteht das **Ressourcenmodell** von DocumentDB aus Gruppen von Ressourcen, die unter einem Datenbankkonto angeordnet sind und jeweils über einen logischen und beständigen URI adressiert werden können. Eine Ressourcengruppe wird in diesem Dokument als **Feed** bezeichnet.

>[AZURE.NOTE]DocumentDB bietet ein hoch effizientes TCP-Protokoll mit einem RESTful-basierten Kommunikationsmodell, das über das [.NET Client-SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) verfügbar ist.

![][1] **Hierarchisches Ressourcenmodell unter einem Datenbankkonto**

Um mit Ressourcen zu arbeiten, müssen Sie über Ihr Azure-Abonnement [ein DocumentDB-Datenbankkonto erstellen](documentdb-create-account.md). Ein Datenbankkonto kann aus einer Reihe von **Datenbanken** mit jeweils mehreren **Sammlungen** bestehen, die jeweils wiederum **gespeicherte Prozeduren, Trigger, UDFs, Dokumente** und zugehörige **Anhänge** (Vorschaufunktion) enthalten. Einer Datenbank sind zudem **Benutzer** zugeordnet, die jeweils über eine Reihe von **Berechtigungen** verfügen, um auf Sammlungen, gespeicherte Prozeduren, Trigger, UDFs, Dokumente oder Anhänge zuzugreifen. Während Datenbanken, Benutzer, Berechtigungen und Sammlungen vom System definierte Ressourcen mit bekannten Schemas sind, enthalten Dokumente und Anhänge beliebige, benutzerdefinierte JSON-Inhalte.

|Ressource |Beschreibung
|-----------|-----------
|Datenbankkonto |Einem Datenbankkonto ist eine Gruppe von Datenbanken sowie eine festgelegte Menge von Blobspeicher für Anhänge (Vorschaufunktion) zugeordnet. Sie können mithilfe Ihres Azure-Abonnements mindestens ein Datenbankkonto erstellen. Jedem Datenbankkonto der Ebene "Standard" ist eine Mindestkapazität von einer S1-Sammlung zugewiesen. Weitere Informationen finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/documentdb/).
|Datenbank |Eine Datenbank ist ein logischer Container für Dokumentspeicher, der auf Sammlungen aufgeteilt ist. Sie ist auch ein Benutzercontainer.
|Benutzer |Logischer Namespace für Bereichs-/geteilte Berechtigungen. 
|Berechtigung |Ein Autorisierungstoken, das einem Benutzer für den autorisierten Zugriff auf eine bestimmte Ressource zugeordnet ist.
|Sammlung |Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik. Eine Sammlung ist eine fakturierbare Entität, bei der die Kosten durch die Leistungsebene bestimmt werden, die mit der Sammlung verknüpft ist. Die Leistungsebenen (S1, S2 und S3) bieten 10 GB Speicher und eine festgelegte Menge an Durchsatz. Weitere Informationen zu Leistungsstufen finden Sie unter [Leistungsebenen in DocumentDB](documentdb-performance-levels.md).
|Gespeicherte Prozedur |Mit JavaScript geschriebene Anwendungslogik, die mit einer Sammlung registriert und über Transaktion innerhalb des Datenbankmoduls ausgeführt wird.
|Trigger |Mit JavaScript geschriebene Anwendungslogik unter Berücksichtigung von mit Einfüge-, Ersetzungs- oder Löschvorgängen verknüpften Nebeneffekten.
|UDF |Eine in JavaScript geschriebene Anwendungslogik ohne Nebeneffekte. Mit UDFs kann ein benutzerdefinierter Abfrageoperator erstellt und damit die DocumentDB-Kernabfragesprache erweitert werden.
|Dokument |Benutzerdefinierter (beliebiger) JSON-Inhalt. Standardmäßig müssen kein Schema definiert oder sekundäre Indizes für alle zu einer Sammlung hinzugefügten Dokumente bereitgestellt werden.
|(Vorschau) Anhang |Ein Anhang ist ein besonderes Dokument mit Verweisen und verknüpften Metadaten für externe Blobs/Medien. Entwickler können auswählen, ob der Blob von DocumentDB verwaltet oder mit einem externen Blobdienstanbieter wie OneDrive, Dropbox usw. gespeichert wird. 


##Vergleich von system- und benutzerdefinierten Ressourcen
Ressourcen wie Datenbankkonten, Datenbanken, Sammlungen, Benutzer, Berechtigungen, gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen verfügen alle über ein festes Schema und werden als Systemressourcen bezeichnet. Im Gegensatz dazu weisen Ressourcen wie Dokumente und Anhänge keine Einschränkungen hinsichtlich des Schemas auf und sind somit ein Beispiel für benutzerdefinierte Ressourcen. In DocumentDB werden sowohl system- als auch benutzerdefinierte Ressourcen als standardkonformes JSON dargestellt und verwaltet. Alle Ressourcen, egal ob system- oder benutzerdefiniert, haben folgende Eigenschaften gemeinsam.

>[AZURE.NOTE]Beachten Sie, dass in allen systemgenierten Eigenschaften in einer Ressource ein Unterstrich (_) in ihrer JSON-Darstellung voransteht.


<table width="500"> 
<tbody>
<tr>
<td valign="top" ><p><b>Eigenschaft</b></p></td>
<td valign="top" ><p><b>Vom Benutzer einstellbar oder systemgeneriert?</b></p></td>
<td valign="top" ><p><b>Zweck</b></p></td>
</tr>

<tr>
<td valign="top" ><p>_rid</p></td>
<td valign="top" ><p>Systemgeneriert</p></td>
<td valign="top" ><p>Systemgenerierte, eindeutige und hierarchische ID der Ressource.</p></td>
</tr>

<tr>
<td valign="top" ><p>_etag</p></td>
<td valign="top" ><p>Systemgeneriert</p></td>
<td valign="top" ><p>ETag der Ressource, das für die Steuerung optimistischer Nebenläufigkeit erforderlich ist.</p></td>
</tr>

<tr>
<td valign="top" ><p>_ts</p></td>
<td valign="top" ><p>Systemgeneriert</p></td>
<td valign="top" ><p>Zuletzt aktualisierter Zeitstempel der Ressource.</p></td>
</tr>

<tr>
<td valign="top" ><p>_self</p></td>
<td valign="top" ><p>Systemgeneriert</p></td>
<td valign="top" ><p>Eindeutiger aufrufbarer URI der Ressource.</p></td>
</tr>

<tr>
<td valign="top" ><p>id</p></td>
<td valign="top" ><p>Vom Benutzer einstellbar</p></td>
<td valign="top" ><p>Benutzerdefinierter eindeutiger Name der Ressource.</p></td>
</tr>

</tbody>
</table>

###Übermittlungsdarstellung der Ressourcen
DocumentDB verfügt über keine proprietären Erweiterungen des JSON-Standards oder besonderer Codierungen, und kann nur mit standardkonformen JSON-Dokumenten verwendet werden.
 
###Adressieren einer Ressource
Alle Ressourcen können über URI aufgerufen werden. Der Wert der **_self**-Eigenschaft einer Ressource stellt den relativen URI der Ressource dar. Das Format des URI besteht aus den /<feed>/{_rid}-Pfadsegmenten: 

|Wert von "_self" |Beschreibung |-------------------|----------- |/dbs |Feed der Datenbanken in einem Datenbankkonto |/dbs/{_rid-db} |Datenbank mit einer eindeutigen ID-Eigenschaft mit dem Wert {_rid-db} |/dbs/{_rid-db}/colls/ |Feed der Sammlungen in einer Datenbank |/dbs/{_rid-db}/colls/{_rid-coll} |Sammlung mit der eindeutigen ID-Eigenschaft mit dem Wert {_rid-coll} |/dbs/{_rid-db}/users/ |Feed der Benutzer in einer Datenbank |/dbs/{_rid-db}/users/{_rid-user} |Benutzer mit einer eindeutigen ID-Eigenschaft mit dem Wert {_rid-user} |/dbs/{_rid-db}/users/{_rid-user}/permissions |Feed der Berechtigungen in einer Datenbank |/dbs/{_rid-db}/users/{_rid-user}/permissions/{_rid-permission} |Berechtigung mit der eindeutigen ID-Eigenschaft mit dem Wert {_rid-permission} 
  
Eine Ressource verfügt ebenfalls über einen eindeutigen benutzerdefinierten Namen, der über die ID-Eigenschaft der Ressource bereitgestellt wird. Die ID ist eine benutzerdefinierte Zeichenfolge mit bis zu 256 Zeichen Länge, die innerhalb des Kontexts einer bestimmten übergeordneten Ressource eindeutig ist. Der Wert der ID-Eigenschaft aller Dokumente innerhalb einer bestimmten Sammlung ist z. B. eindeutig, ist jedoch sammlungsübergreifend möglicherweise nicht eindeutig. Der Wert der ID-Eigenschaft aller Berechtigungen eines bestimmten Benutzers ist ebenso eindeutig, ist jedoch benutzerübergreifend möglicherweise eindeutig. Die _rid-Eigenschaft wird zum Erstellen eines aufrufbaren _self-Links einer Ressource verwendet.

Jede Ressource verfügt ebenfalls über eine systemgenerierte hierarchische Ressourcen-ID (auch als RID bezeichnet), die über die _rid-Eigenschaft verfügbar ist. Die RID codiert die gesamte Hierarchie einer bestimmten Ressource und ist eine einfache interne Darstellung, die zum Erzwingen einer dezentralen refentiellen Integrität verwendet. Die RID ist innerhalb eines Datenbankkontos eindeutig und wird von DocumentDB intern für effiziente Weiterleitung ohne partitionsübergreifendes Nachschlagen verwendet. 

Bei den Werten der _self- und _rid-Eigenschaften handelt es sich um alternative und kanonische Darstellungen einer Ressource.

##Datenbankkonten
Sie können mithilfe Ihres Azure-Abonnements ein oder mehrere DocumentDB-Datenbankkonten bereitstellen. Jedes Datenbankkonto der Ebene "Standard" erhält eine Mindestkapazität von einer S1-Sammlung.

Sie können [DocumentDB-Datenbankkonten erstellen und verwalten](documentdb-create-account.md), indem Sie das Azure-Portal unter [http://portal.azure.com/](http://portal.azure.com/) nutzen. Das Erstellen und Verwalten eines Datenbankkontos erfordert Administratorzugriff und kann nur unter Ihrem Azure-Abonnement ausgeführt werden.

###Eigenschaften von Datenbankkonten
Sie können die folgenden Eigenschaften im Rahmen der Bereitstellung und Verwaltung eines Datenbankkontos konfigurieren und lesen:

<table border="1" cellspacing="0" cellpadding="0" > 
<tbody>
<tr>
<td valign="top" ><p><b>Eigenschaftenname</b></p></td>
<td valign="top" ><p><b>Beschreibung</b></p></td>
</tr>

<tr>
<td valign="top" ><p>Konsistenzrichtlinie</p></td>
<td valign="top" ><p>Legen Sie diese Eigenschaft fest, um die Standardkonsistenzebene für alle Sammlungen unter Ihrem Datenbankkonto zu konfigurieren. Sie können die Konsistenzebene mithilfe des Anforderungsheaders [x-ms-consistency-level] anforderungsbasiert außer Kraft setzen. Zukünftig wird möglicherweise die Außerkraftsetzung der Konsistenzebene auf Sammlungsbasis unterstützt. </p>

<p>Beachten Sie, dass diese Eigenschaft nur für <i>benutzerdefinierte Ressourcen</i> gilt. Alle systemdefinierten Ressourcen werden konfiguriert, um Lese-/Abfragevorgänge mit hoher Konsistenz zu unterstützen.</p></td>
</tr>

<tr>
<td valign="top" ><p>Primärer und sekundärer Schlüssel</p></td>
<td valign="top" ><p>Dies sind die primären und sekundären Schlüssel, die den Administratorzugriff auf alle Ressourcen unter dem Datenbankkonto bereitstellen.</p></td>
</tr>

<tr>
<td valign="top" ><p>MaxMediaStorageUsageInMB (READ)</p></td>
<td valign="top" ><p>Maximale Medienspeichermenge, die für das Datenbankkonto zur Verfügung steht.</p></td>
</tr>

<tr>
<td valign="top" ><p>MediaStorageUsageInMB (READ)</p></td>
<td valign="top" ><p>Aktuelle Medienspeichernutzung für das Datenbankkonto.</p></td>
</tr>

</tbody>
</table>

Beachten Sie, dass Sie zusätzlich zur Bereitstellung, Konfiguration und Verwaltung Ihres Datenbankkontos über das Azure-Portal die DocumentDB-Datenbankkonten auch über [Azure DocumentDB-REST-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) und [Client-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) programmgesteuert erstellen und verwalten können.

##Datenbanken
Eine DocumentDB-Datenbank ist ein logischer Container einer oder mehrerer Sammlungen und Benutzer, wie im folgenden Diagramm dargestellt. Sie können eine beliebige Anzahl von Datenbanken unter einem DocumentDB-Datenbankkonto erstellen, abhängig von der angegebenen Grenze.

![][2] **Eine Datenbank ist ein logischer Container von Benutzern und Sammlungen**

Eine Datenbank kann praktisch unbegrenzten Dokumentspeicher enthalten, der in Sammlungen unterteilt ist, die die Transaktionsdomänen für die darin enthaltenen Dokumente bilden.

###Elastische Skalierbarkeit einer DocumentDB-Datenbank
Eine DocumentDB-Datenbank ist standardmäßig flexibel und kann von einigen GB zu mehreren Petabytes an SSD-gestütztem Dokumentspeicher und bereitgestelltem Durchsatz reichen.

Im Gegensatz zur Datenbank im traditionellen RDBMS ist der Gültigkeitsbereich einer Datenbank in DocumentDB nicht auf einen einzelnen Computer beschränkt. Mit DocumentDB können Sie weitere Sammlungen oder Datenbanken bzw. beides erstellen, wenn die Größenanforderungen Ihrer Anwendung zunehmen. Tatsächlich haben verschiedene Anwendungen von Erstanbietern bei Microsoft DocumentDB in einer Größenordnung für Verbraucher verwendet, indem extrem große DocumentDB-Datenbanken erstellt wurden, die jeweils Tausende Sammlungen mit mehreren Terabytes an Dokumentspeicher enthalten haben. Sie können eine Datenbank durch Hinzufügen oder Entfernen von Sammlungen vergrößern oder verkleinern, um den Größenanforderungen Ihrer Anwendung zu entsprechen.

Sie können je nach Angebot eine beliebige Anzahl von Sammlungen innerhalb einer Datenbank erstellen. Jede Sammlung verfügt über SSD-gesicherten Speicher und Durchsatz, der Ihnen je nach ausgewählter Leistungsebene bereitgestellt wird.

Eine DocumentDB-Datenbank ist ebenfalls ein Container für Benutzer. Ein Benutzer ist wiederum ein logischer Namespace für eine Reihe von Berechtigungen, die eine differenzierte Autorisierung und einen differenzierten Zugriff auf Sammlungen, Dokumente und Anhänge bieten.
 
Datenbanken können wie andere Ressourcen im DocumentDB-Ressourcenmodell einfach über [Azure DocumentDB-REST-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder eines der [Client-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) erstellt, ersetzt, gelöscht, gelesen oder aufgezählt werden. DocumentDB garantiert für das Lesen oder Abfragen der Metadaten einer Datenbankressource eine hohe Konsistenz. Das Löschen einer Datenbank stellt automatisch sicher, dass Sie nicht auf die darin enthaltenen Sammlungen oder Benutzer zugreifen können.

##Sammlungen
Eine DocumentDB-Sammlung ist ein Container für Ihre JSON-Dokumente. Eine Sammlung ist auch eine Maßeinheit für Transaktionen und Abfragen. Sie können eine DocumentDB-Datenbank durch Hinzufügen weiterer Sammlungen horizontal hochskalieren. Wenn Sie Ihre Anwendung weiter hochskalieren müssen, können Sie eine Sammlung hinzufügen, der – je nach Leistungsebene – SSD-gesicherter Speicher und eine festgelegte Menge an Durchsatz zugeordnet ist.
 
###Flexibler SSD-gestützter Dokumentspeicher
Eine Sammlung ist an sich flexibel. Durch das Hinzufügen oder Entfernen von Dokumenten wächst oder schrumpft sie automatisch. Obwohl die Nutzung von DocumentDB durch Erstbenutzer mit Tausenden Sammlungen innerhalb einer Datenbank getestet wurde, deren Größe jeweils von einigen Gigabytes bis zu mehreren Terabytes reichte, ist die Elastizität einer entsprechenden Sammlung in DocumentDB derzeit auf 10 GB begrenzt.

###Automatische Indizierung von Sammlungen
DocumentDB ist ein echtes schemaloses Datenbanksystem. Es setzt kein Schema für die JSON-Dokumente voraus. Während Sie Dokumente zu einer Sammlung hinzufügen, werden diese von DocumentDB automatisch indiziert und stehen dann für Sie für Abfragen bereit. Die automatische Indizierung von Dokumenten, ohne ein Schema oder sekundäre Indizes zu erfordern, ist ein entscheidendes Merkmal von DocumentDB und wird über für Schreibvorgänge optimierte, stufenlose und protokollstrukturierte Verfahren zur Indexwartung ermöglicht. DocumentDB unterstützt eine beständige Menge an extrem schnellen Schreibvorgängen, während weiterhin konsistente Abfragen bedient werden. Sowohl der Dokument- als auch der Indexspeicher werden zum Berechnen des Speichers verwendet, der von den einzelnen Sammlungen belegt wird. Sie können die mit der Indizierung einhergehenden Abstriche bei der Speicherung und Leistung kontrollieren, indem Sie die Indizierungsrichtlinie für eine Sammlung konfigurieren.

###Konfigurieren der Indizierungsrichtlinie einer Sammlung
Die Indizierungsrichtlinie der einzelnen Sammlungen gestattet es Ihnen, Kompromisse bei der Leistung und Speicherung einzugehen, die mit der Indizierung einhergehen. Die folgende Optionen stehen Ihnen im Rahmen der Indexkonfiguration zur Verfügung:

-	Wählen Sie, ob die Sammlung automatisch alle Dokumente indiziert oder nicht. Standardmäßig werden automatisch alle Dokumente indiziert. Sie können die automatische Indizierung deaktivieren und wahlweise nur bestimmte Dokumente zum Index hinzufügen. Umgekehrt können Sie wahlweise auch nur bestimmte Dokumente ausschließen. Sie können dies erreichen, indem Sie für die Indizierungsrichtlinie einer Sammlung für die "automatic"-Eigenschaft den Wert "true" oder "false" festlegen und beim Einfügen, Ersetzen oder Löschen eines Dokuments den Anforderungsheader "[x-ms-indexingdirective]" verwenden.  
-	Wählen Sie, ob bestimmte Pfade oder Muster in Ihren Dokumenten in den Index einbezogen oder aus diesem ausgeschlossen werden sollen. Dies können Sie durch die entsprechende Festlegung von "includedPaths" und "excludedPaths" für die Indizierungsrichtlinie einer Sammlung erreichen. Sie können auch die Speicher- und Leistungsabstriche für Bereichs- und Hashabfragen für bestimmte Pfadmuster konfigurieren. 
-	Wählen Sie zwischen synchronen (konsistenten) und asynchronen (flexiblen) Indexaktualisierungen. Der Index wird bei jedem Einfügen, Ersetzen oder Löschen eines Dokuments der Sammlung standardmäßig synchron aktualisiert. Auf diese Weise können die Abfragen dieselbe Konsistenzebene wie die von Dokumentlesevorgängen berücksichtigen. Obwohl DocumentDB für Schreibvorgänge optimiert ist und beständige Mengen an Dokumentschreibvorgängen zusammen mit der synchronen Indexwartung und der Bereitstellung konsistenter Abfragen unterstützt, können Sie bestimmte Sammlungen so konfigurieren, dass ihr Index flexibel aktualisiert wird. Die flexible Indizierung steigert die Schreibleistung noch weiter und ist ideal für Sammelerfassungsszenarien für Sammlungen mit hauptsächlich übermäßigen Lesevorgängen geeignet.

Die Indizierungsrichtlinie kann nur während der Erstellung einer Sammlung konfiguriert werden. Nachdem die Sammlung erstellt wurde, kann die Richtlinie nicht aktualisiert werden.

###Abfragen einer Sammlung
Die Dokumente in einer Sammlung können beliebige Schemas aufweisen, und Sie können Dokumente in einer Sammlung abfragen, ohne vorab ein Schema oder sekundäre Indizes bereitstellen zu müssen. Sie können die Sammlung mithilfe der [SQL-Abfragesprache von DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx), abfragen, die über JavaScript-basierte UDFs umfassende hierarchische und relationale Operatoren sowie Erweiterbarkeit bietet. Die JSON-Grammatik ermöglicht die Gestaltung von JSON-Dokumenten als Baumstrukturen mit den Beschriftungen als Strukturknoten. Dies wird sowohl über die automatischen Indizierungsverfahren sowie über den SQL-Abfragedialekt von DocumentDB erreicht. Die Abfragesprache von DocumentDB besteht aus drei Hauptaspekten:

1.	Eine kleine Auswahl von Abfragevorgängen, die natürlich zur Baumstruktur zugeordnet werden, einschließlich hierarchischer Abfragen und Projektionen. 
2.	Eine Teilmenge von relationalen Vorgängen, einschließlich der Kompositionen, Filter, Projektionen, Aggregaten und Selbstverknüpfungen. 
3.	Rein auf JavaScript basierende UDFs, die mit (1) und (2) funktionieren.  

Das Abfragemodell von DocumentDB versucht die Balance zwischen Funktionalität, Effizienz und Einfachheit zu finden. Das Datenbankmodul von DocumentDB kompiliert die SQL-Abfrageanweisungen und führt sie aus. Sie können eine Sammlung mithilfe der [Azure DocumentDB-REST-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder eines der [Client-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) abfragen. Das .NET-SDK verfügt über einen LINQ-Anbieter.

Im [Query Playground](https://www.documentdb.com/sql/demo) können Sie DocumentDB testen und SQL-Abfragen für unser DataSet ausführen..

###Mehrdokumenttransaktionen
Datenbanktransaktionen bieten ein sicheres und berechenbares Programmiermodell zur Behandlung von gleichzeitig an den Daten vorgenommenen Änderungen. In RDBMS umfasst die herkömmliche Vorgehensweise zum Erstellen der Geschäftslogik das Schreiben von **gespeicherten Prozeduren** und/oder **Triggern** sowie deren Übermittlung an den Datenbankserver zur transaktionalen Ausführung. In RDBMS muss der Anwendungsprogrammierer mit zwei verschiedenen Programmiersprachen umgehen:

- Die (nicht transaktionale) Anwendungsprogrammiersprache (z. B. JavaScript, Python, c#, Java usw.). 
- T-SQL, die transaktionale Programmiersprache, die intern von der Datenbank ausgeführt wird.

Aufgrund seiner direkt im Datenbankmodul verankerten starken Bindung an JavaScript und JSON bietet DocumentDB ein intuitives Programmiermodell zur Ausführung von JavaScript-basierter Anwendungslogik, die über gespeicherte Prozeduren und Trigger direkt in den Sammlungen erfolgt. Dies ermöglicht Folgendes:

- Effiziente Implementierung der Nebenläufigkeitssteuerung, Wiederherstellung und automatischen Indizierung der JSON-Objektdiagramme direkt im Datenbankmodul. 
- Natürliches Ausdrücken des Steuerungsablaufs, der Zuweisung und der Integration von Grundtypen zur Ausnahmeverarbeitung direkt mit Datenbanktransaktionen (im Sinne der JavaScript-Programmiersprache).   

Die auf Sammlungsebene registrierte JavaScript-Logik kann dann Datenbankvorgänge für die Dokumente der angegebenen Sammlung auslösen. Die auf JavaScript basierten gespeicherten Prozeduren und Trigger von DocumentDB werden implizit in eine umgebende ACID-Transaktion eingefasst, die die Momentaufnahmeisolation zwischen den Dokumenten in einer Sammlung einbezieht. Während des Ausführungsverlaufs wird die gesamte Transaktion abgebrochen, wenn JavaScript eine Ausnahme auslöst. Das sich ergebende Programmiermodell ist sehr einfach, aber dennoch leistungsstark. JavaScript-Entwickler erhalten ein "beständiges" Programmiermodell, während sie weiterhin ihre vertrauten Sprachkonstruktr und Bibliotheksstammfunktionen verwenden können.

Die Möglichkeit zur direkten Ausführung von JavaScript innerhalb des Datenbankmoduls im gleichen Adressraum wie der Pufferpool gestattet die leistungsfähige und transaktionale Ausführung von Datenbankvorgängen für die Dokumente einer Sammlung. Des Weiteren werden alle Impedanzabweichungen zwischen den Typsystemen von Anwendung und Datenbank aufgrund der starken Bindung des DocumentDB-Datenbankmoduls an JSON und JavaScript beseitigt.

Nach der Erstellung einer Sammlung können Sie gespeicherte Prozeduren, Trigger und UDFs für eine Sammlung registrieren. Verwenden Sie dazu die [Azure DocumentDB-REST-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder eines der [Client-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx). Im Anschluss an die Registrierung können Sie sie referenzieren und ausführen. Betrachten Sie die folgende gespeicherte Prozedur, die vollständig in JavaScript geschrieben ist, zwei Argumente (Buchtitel und Autorenname) übernimmt, ein neues Dokument erstellt, Abfragen für ein Dokument ausführt und dieses dann aktualisiert – alles unter dem Schutz einer impliziten ACID-Transaktion. Die gesamte Transaktion wird an einem beliebigen Punkt der Ausführung abgebrochen, wenn eine JavaScript-Ausnahme ausgelöst wird.

	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();        
	    var collectionLink = collectionManager.getSelfLink()
	        
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        {id: name, author: author},
	        function(err, documentCreated) {
	            if(err) throw new Error(err.message);
	            
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function(err, matchingDocuments) {
	                    if(err) throw new Error(err.message);
	                    
	                    context.getResponse().setBody(matchingDocuments.length);
	                   
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don’t need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);   
	                    }
	                })
	        })
	};

Der Client kann die obige JavaScript-Logik zur transaktionalen Ausführung über HTTP POST an die Datenbank übermitteln. Weitere Informationen zur Verwendung von HTTP-Methoden finden Sie unter [RESTful-Interaktionen mit DocumentDB-Ressourcen](documentdb-interactions-with-resources.md).

	client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
	   .then(function(createdStoredProcedure) {
	        return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
	            "NoSQL Distilled",
	            "Martin Fowler");
	    })
	    .then(function(result) {
	        console.log(result);
	    },
	    function(error) {
	        console.log(error);
	    });


Beachten Sie, dass kein Typsystemkonflikt vorliegt und keine "OR-Zuordnung" oder trickreiche Codegenerierungsmethode erforderlich ist, da die Datenbank JSON und JavaScript systemintern verarbeiten kann.

Gespeicherte Prozeduren und Trigger interagieren über ein wohldefiniertes Objektmodell, das den aktuellen Sammlungskontext offenlegt, mit einer Sammlung und den Dokumenten in einer Sammlung.

Sammlungen können in DocumentDB mithilfe der [Azure DocumentDB-REST-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder eines der [Client-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) problemlos erstellt, gelöscht, gelesen oder aufgezählt werden. DocumentDB bietet für das Lesen oder Abfragen der Metadaten einer Sammlung immer eine hohe Konsistenz. Das Löschen einer Sammlung stellt automatisch sicher, dass Sie nicht auf die darin enthaltenen Dokumente, Anhänge, gespeicherten Prozeduren, Trigger und benutzerdefinierten Funktionen zugreifen können.
##Gespeicherte Prozeduren, Trigger und UDFs
Wie im vorigen Abschnitt beschrieben, können Sie eine Anwendungslogik erstellen, die direkt innerhalb einer Transaktion im Datenbankmodul ausgeführt wird. Die Anwendungslogik kann vollständig in JavaScript geschrieben und als gespeicherte Prozedur, Trigger oder benutzerdefinierte Funktion (UDF) gestaltet werden. Der JavaScript-Code innerhalb einer gespeicherten Prozedur oder eines Triggers kann Dokumente in eine Sammlung einfügen, sie dort ersetzen, löschen, lesen oder abfragen. Andererseits kann der JavaScript-Code innerhalb einer benutzerdefinierten Funktion nur durch die Aufzählung der Dokumente der Abfrageergebnisgruppe ohne Nebenwirkungen berechnen und eine weitere Ergebnisgruppe erzeugen. Für die Mehrinstanzenfähigkeit erzwingt DocumentDB eine strikte, auf Reservierung basierende Ressourcensteuerung. Jede gespeicherte Prozedur, jeder Trigger oder jede benutzerdefinierte Funktion erhält für die Erledigung seiner Aufgaben einen festgelegten Anteil an den Betriebssystemressourcen. Zudem können gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen (UDFs) keine Verknüpfungen zu externen JavaScript-Bibliotheken herstellen und werden gesperrt, wenn sie den ihnen zugeordnete Ressourcenanteil überschreiten. Sie können gespeicherte Prozeduren, Trigger oder UDFs mithilfe der REST-APIs für eine Sammlung registrieren oder die Registrierung aufheben. Während der Registrierung werden eine gespeicherte Prozedur, ein Trigger oder eine benutzerdefinierte Funktion vorkompiliert und als Bytecode gespeichert, der später ausgeführt wird. Der folgende Abschnitt veranschaulicht, wie Sie eine gespeicherte Prozedur, einen Trigger und eine UDF mithilfe des DocumentDB-JavaScript-SDKs registrieren und ausführen und die Registrierung wieder aufheben. Das JavaScript-SDK ist ein einfacher Wrapper für die [DocumentDB-REST-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx).

###Registrieren einer gespeicherten Prozedur
Die Registrierung einer gespeicherten Prozedur erstellt eine neue gespeicherte Prozedurressource für eine Sammlung über HTTP POST.

	var storedProc = {
	    id: "validateAndCreate",
	    body: function (documentToCreate) {
	        documentToCreate.id = documentToCreate.id.toUpperCase();
	        
	        var collectionManager = getContext().getCollection();
	        collectionManager.createDocument(collectionManager.getSelfLink(),
	            documentToCreate,
	            function(err, documentCreated) {
	                if(err) throw new Error('Error while creating document: ' + err.message;
	                getContext().getResponse().setBody('success - created ' + 
	                        documentCreated.name);
	            });
	    }
	};
	
	client.createStoredProcedureAsync(collection._self, storedProc)
	    .then(function (createdStoredProcedure) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

###Ausführen einer gespeicherten Prozedur
Die Ausführung einer gespeicherten Prozedur erfolgt über das Auslösen einer HTTP POST-Methode für eine vorhandene gespeicherte Prozedurressource, indem im Anforderungstext Parameter an die Prozedur übergeben werden.

	var inputDocument = {id : "document1", author: "G. G. Marquez"};
	client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
	    .then(function(executionResult) {
	        assert.equal(executionResult, "success - created DOCUMENT1");
	    }, function(error) {
	        console.log("Error");
	    });

###Aufheben der Registrierung einer gespeicherten Prozedur
Das Aufheben der Registrierung für eine gespeicherte Prozedur erfolgt über das einfache Auslösen einer HTTP DELETE-Methode für eine vorhandene gespeicherte Prozedurressource.

	client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
	    .then(function (response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });


###Registrieren eines vorangestellten Triggers
Die Registrierung eines Triggers umfasst das Erstellen einer neuen Triggerressource für eine Sammlung über HTTP POST. Sie können angeben, ob es sich um einen vorangestellten oder nachgestellten Trigger handelt. Zudem können Sie die Art des Vorgangs angeben, der dem Trigger zugeordnet ist (z. B. Erstellen, Ersetzen, Löschen oder Alle).

	var preTrigger = {
	    id: "upperCaseId",
	    body: function() {
	            var item = getContext().getRequest().getBody();
	            item.id = item.id.toUpperCase();
	            getContext().getRequest().setBody(item);
	    },
	    triggerType: TriggerType.Pre,
	    triggerOperation: TriggerOperation.All
	}
	
	client.createTriggerAsync(collection._self, preTrigger)
	    .then(function (createdPreTrigger) {
	        console.log("Successfully created trigger");
	    }, function(error) {
	        console.log("Error");
	    });

###Ausführen eines vorangestellten Triggers
Die Ausführung eines Triggers erfolgt durch die Angabe des Namens (über den Anforderungsheader) eines vorhandenen Triggers während der Auslösung der POST-/PUT-/DELETE-Anforderung einer Dokumentressource.
 
	client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
	    .then(function(createdDocument) {
	        assert.equal(createdDocument.resource.id, "DOC1");
	    }, function(error) {
	        console.log("Error");
	    });

###Aufheben der Registrierung eines vorangestellten Triggers
Das Aufheben der Registrierung für einen Trigger erfolgt über das einfache Auslösen einer HTTP DELETE-Methode für eine vorhandene Triggerressource.

	client.deleteTriggerAsync(createdPreTrigger._self);
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

###Registrieren einer benutzerdefinierten Funktion (UDF)
Die Registrierung einer UDF umfasst das Erstellen einer neuen UDF-Ressource für eine Sammlung über HTTP POST.

	var udf = { 
	    id: "mathSqrt",
	    body: function(number) {
	            return Math.sqrt(number);
	    },
	};
	client.createUserDefinedFunctionAsync(collection._self, udf)
	    .then(function (createdUdf) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

###Ausführen einer benutzerdefinierten Funktion im Rahmen einer Abfrage
Eine UDF kann im Rahmen der SQL-Abfrage angegeben werden und wird als Methode verwendet, um die vorhandene [SQL-Abfragesprache von DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx) zu erweitern.

	var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
	client.queryDocuments(collection._self, filterQuery).toArrayAsync();
	    .then(function(queryResponse) {
	        var queryResponseDocuments = queryResponse.feed;
	    }, function(error) {
	        console.log("Error");
	    });

###Aufheben der Registrierung einer benutzerdefinierten Funktion (UDF) 
Das Aufheben der Registrierung für eine benutzerdefinierte Funktion erfolgt über das einfache Auslösen einer HTTP DELETE-Methode für eine vorhandene UDF-Ressource.

	client.deleteUserDefinedFunctionAsync(createdUdf._self)
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

Obwohl die obigen Codeausschnitte die Registrierung (POST), die Aufhebung der Registrierung (PUT), das Lesen/Auflisten (GET) und die Ausführung (POST) über das [DocumentDB-JavaScript-SDK](https://github.com/Azure/azure-documentdb-js) veranschaulicht haben, können Sie auch die [REST-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder andere [Client-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) verwenden.

##Dokumente
Sie können beliebige JSON-Dokumente zu einer Sammlung hinzufügen, sie dort ersetzen, löschen, lesen, aufzählen und abfragen. DocumentDB erfordert weder Schema noch sekundäre Indizes, um die dokumentübergreifende Abfrage in einer Sammlung zu unterstützen.

DocumentDB ist ein ganz und gar offener Datenbankdienst, der keine speziellen Datentypen (z. B. Datum/Uhrzeit) oder bestimmte Codierungen für JSON-Dokumente einführt. Beachten Sie, dass für DocumentDB keine speziellen JSON-Konventionen erforderlich sind, um die Beziehungen zwischen verschiedenen Dokumenten festzuschreiben. Die SQL-Abfragesprache von DocumentDB bietet sehr leistungsstarke hierarchische und relationale Abfrageoperatoren, um Dokumente ohne spezielle Anmerkungen oder die Festschreibung der Beziehungen zwischen Dokumenten mithilfe unterschiedlicher Eigenschaften abzufragen und zu projizieren.
 
Wie die anderen Ressourcen können Dokumente mithilfe der REST-APIs oder eines [Client-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) einfach erstellt, ersetzt, gelöscht, gelesen, aufgezählt und abgefragt werden. Durch das Löschen eines Dokuments wird sofort das Kontingent freigegeben, das allen geschachtelten Anhängen entspricht. Der Grad der Lesekonsistenz von Dokumenten folgt der Konsistenzrichtlinie des Datenbankkontos. Diese Richtlinie kann anforderungsbasiert in Abhängigkeit von den Anforderungen Ihrer Anwendung an die Datenkonsistenz außer Kraft gesetzt werden. Bei der Abfrage von Dokumenten folgt die Lesekonsistenz dem für die Sammlung festgelegten Indizierungsmodus. Die Konsistenz folgt der Konsistenzrichtlinie des Kontos.

##Anhänge und Medien
>[AZURE.NOTE]Anhangs- und Medienressourcen sind Vorschaufunktionen.
 
DocumentDB gestattet es Ihnen, binäre Blobs/Medien mit DocumentDB oder in einem eigenen Remotemedienspeicher zu speichern. Zudem haben Sie die Möglichkeit, die Metadaten eines Mediums in Form eines speziellen Dokuments, dem sogenannten Anhang, darzustellen. Ein Anhang in DocumentDB ist ein spezielles (JSON-)Dokument, das auf die/den an anderer Stelle gespeicherten Medien/Blob verweist. Ein Anhang ist einfach ein spezielles Dokument, das die Metadaten (z. B. Speicherort, Autor usw.) eines Mediums erfasst, das in einem Remotemedienspeicher gespeichert wird.

Betrachten Sie eine soziale Leseanwendung, die DocumentDB zum Speichern von Anmerkungen und Metadaten verwendet, einschließlich der Kommentare, Lesezeichen, Bewertungen usw., die einem E-Book für einen bestimmten Benutzer zugeordnet sind.

-	Der Buchinhalt selbst wird im Medienspeicher entweder als Teil des DocumentDB-Datenbankkontos oder eines Remotemedienspeicher zur Verfügung gestellt. 
-	Ein Anwendung kann die Metadaten der einzelnen Benutzer als individuelles Dokument speichern. Die Metadaten von Johannes für Buch1 können z. B. in einem Dokument gespeichert werden, auf das über "/sammlungen/johannes/dokumente/buch1" verwiesen wird. 
-	Anhänge, die auf die Inhaltsseiten eines bestimmten Buchs für einen Benutzer verweisen, werden unter dem entsprechenden Dokument gespeichert, z. B. "/sammlungen/johannes/dokumente/buch1/kapitel1", "/sammlungen/johannes/dokumente/buch1/kapitel2" usw. 

Beachten Sie, dass die Beispiele benutzerfreundliche IDs verwenden, um die Ressourcenhierarchie zu vermitteln. Der Zugriff auf Ressourcen erfolgt mithilfe eindeutiger Ressourcen-IDs über REST-APIs.

Für die von DocumentDB verwalteten Medien verweist die _media-Eigenschaft des Anhangs über seinen URI auf das Medium. DocumentDB stellt die Garbage Collection für die Medien sicher, wenn alle ausstehenden Referenzen entfernt werden. Der Anhang wird automatisch von DocumentDB generiert, wenn Sie neue Medien hochladen. Zudem wird die _media-Eigenschaft aufgefüllt, um auf das neu hinzugefügte Medium zu verweisen. Wenn Sie die Medien in einem von Ihnen verwalteten Remoteblobspeicher speichern (z. B. OneDrive, Azure Storage, DropBox usw.), können Sie weiterhin Anhänge verwenden, um auf die Medien zu verweisen. In diesem Fall erstellen Sie den Anhang selbst und füllen seine _media-Eigenschaft auf. 

Wie die anderen Ressourcen können Anhänge mithilfe der REST-APIs oder eines Client-SDKs einfach erstellt, ersetzt, gelöscht, gelesen und aufgezählt werden. Wie bei Dokumenten folgt der Grad der Lesekonsistenz von Anhängen der Konsistenzrichtlinie des Datenbankkontos. Diese Richtlinie kann anforderungsbasiert in Abhängigkeit von den Anforderungen Ihrer Anwendung an die Datenkonsistenz außer Kraft gesetzt werden. Bei der Abfrage von Anhängen folgt die Lesekonsistenz dem für die Sammlung festgelegten Indizierungsmodus. Die Konsistenz folgt der Konsistenzrichtlinie des Kontos.
##Benutzer
Ein DocumentDB-Benutzer stellt einen logischen Namespace für die Gruppierung von Berechtigungen dar. Ein DocumentDB-Benutzer kann einem Benutzer in einem Identitätsverwaltungssystem oder einer vordefinierten Anwendungsrolle entsprechen. Für DocumentDB stellt ein Benutzer einfach eine Abstraktion für die Gruppierung einer Reihe von Berechtigungen unter einer Datenbank dar.

Zur Implementierung der Mehrinstanzenfähigkeit für Ihre Anwendung können Sie Benutzer in DocumentDB erstellen, die den tatsächlichen Benutzern oder Mandanten Ihrer Anwendung entsprechen. Anschließend können Sie für einen bestimmten Benutzer Berechtigungen erstellen, die der Zugriffssteuerung über verschiedene Sammlungen, Dokumente oder Anhänge hinweg entsprechen.

Da sich die Skalierung Ihrer Anwendung an die zunehmende Anzahl der Benutzer anpassen muss, können Sie verschiedene Methoden übernehmen, um die Datei horizontal zu partitionieren. Sie können jeden Benutzer wie folgt gestalten:

-	Jeder Benutzer ist einer Datenbank zugeordnet.
-	Jeder Benutzer ist einer Sammlung zugeordnet. 
-	Dokumente entsprechen mehreren Benutzern für eine dedizierte Sammlung. 
-	Dokumente entsprechen mehreren Benutzern für eine Gruppe von Sammlungen.   

Unabhängig von der gewählten Strategie für die horizontale Partitionierung können Sie Ihre realen Benutzer als Benutzer in der DocumentDB-Datenbank abbilden und den einzelnen Benutzern differenzierte Berechtigungen zuweisen.

![][3] **Shardingstrategien und Benutzermodellierung**

Wie alle anderen Ressourcen können Benutzer in DocumentDB mithilfe der REST-APIs oder eines Client-SDKs einfach erstellt, ersetzt, gelöscht, gelesen und aufgezählt werden. DocumentDB bietet für das Lesen oder Abfragen der Metadaten einer Benutzerressource immer eine hohe Konsistenz. Es sollte dabei darauf hingewiesen werden, dass das Löschen eines Benutzers automatisch sicherstellt, dass Sie auf keine der ihm zugeordneten Berechtigungen zugreifen können. Obwohl das Kontingent der Berechtigungen als Teil des gelöschten Benutzers im Hintergrund von DocumentDB freigegeben wird, stehen die gelöschten Berechtigungen sofort wieder für Sie zur Verfügung.

##Berechtigungen
Hinsichtlich der Zugriffssteuerung werden Ressourcen wie Datenbankkonten, Datenbanken, Benutzer und Berechtigungen als *administrative* Ressourcen betrachtet, da sie Administratorrechte erfordern. Auf der anderen Seite werden Ressourcen wie Sammlungen, Dokumente, Anhänge, gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen (UDFs) einer angegebenen Datenbank zugeordnet und als *Anwendungsressource* betrachtet. Entsprechend den zwei Arten von Ressourcen und den Rollen, die Zugriff darauf haben (also Administrator und Benutzer), definiert das Autorisierungsmodell zwei Arten von *Zugriffsschlüsseln*: *Hauptschlüssel* und *Ressourcenschlüssel*. Der Hauptschlüssel ist eine Komponente des Datenbankkontos und wird dem Entwickler (oder Administrator) übermittelt, der das Datenbankkonto bereitstellt. Dieser Hauptschlüssel verfügt über eine Administratorsemantik, da er zum Autorisieren des Zugriffs auf administrative Ressourcen und Anwendungsressourcen verwendet werden kann. Im Gegensatz dazu, ist der Ressourcenschlüssel ein differenzierter Zugriffsschlüssel, der den Zugriff auf eine *bestimmte* Anwendungsressource gestattet. Somit erfasst er die Beziehung zwischen dem Benutzer einer Datenbank und den Berechtigungen, über die der Benutzer für eine bestimmte Ressource verfügt (Beispiel: Sammlung, Dokument, Anhang, gespeicherte Prozedur, Trigger oder benutzerdefinierte Funktion).

Die einzige Möglichkeit zum Abrufen eines Ressourcenschlüssels besteht darin, unter einem bestimmten Benutzer eine Berechtigungsressource zu erstellen. Beachten Sie, dass zum Erstellen oder Abrufen einer Berechtigung ein Hauptschlüssel im Autorisierungsheader angegeben werden muss. Eine Berechtigungsressource verknüpft die Ressource, den Zugriff und den Benutzer. Nach der Erstellung einer Berechtigungsressource muss der Benutzer lediglich den zugehörigen Ressourcenschlüssel präsentieren, um Zugriff auf die relevante Ressource zu erhalten. Daher kann ein Ressourcenschlüssel als logische und kompakte Darstellung der Berechtigungsressource betrachtet werden.

Wie bei allen anderen Ressourcen können Berechtigungen in DocumentDB mithilfe der REST-APIs oder eines Client-SDKs einfach erstellt, ersetzt, gelöscht, gelesen und aufgezählt werden. DocumentDB bietet für das Lesen oder Abfragen der Metadaten einer Berechtigung immer eine hohe Konsistenz.

##Nächste Schritte
Weitere Informationen zum Arbeiten mit Ressourcen mithilfe von HTTP-Befehlen finden Sie unter [RESTful-Interaktionen mit DocumentDB-Ressourcen](documentdb-interactions-with-resources.md).


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png
 

<!---HONumber=July15_HO1-->