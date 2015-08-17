#Datenverwaltung und Business Analytics

Daten in der Cloud zu verwalten und zu analysieren, ist ebenso wichtig wie das Verwalten und Analysieren von Daten in anderen Zusammenhängen. Azure stellt Ihnen dafür eine Reihe von Technologien zum Arbeiten mit relationalen und nichtrelationalen Daten zur Verfügung. In diesem Artikel werden die verschiedenen Optionen vorgestellt.

##Inhaltsverzeichnis      

- [Blob-Speicher](#blob)
- [Ausführen eines DBMS auf einem virtuellen Computer](#dbinvm)
- [SQL-Datenbank](#sqldb)
	- [SQL-Datensynchronisierung](#datasync)
	- [SQL-Datenberichte mithilfe virtueller Computer](#datarpt)
- [Table Storage](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob">Blob-Speicher</a>

Das Wort „Blob“ ist eine Kurzform für „Binary Large Object“ (binäres großes Objekt) und bezeichnet genau, was ein Blob ist: eine Sammlung binärer Informationen. Blobs sind zwar einfach konstruiert, jedoch sehr nützlich. [Abbildung 1](#Fig1) zeigt die Grundlagen des Blob-Speichers von Azure.

<a name="Fig1"></a>![Diagramm von Blobs][blobs]
 
**Abbildung 1: Blob-Speicher von Azure speichert binäre Daten - Blobs - in Containern.**

Um Blobs zu verwenden, erstellen Sie zuerst ein Azure-*Speicherkonto*. Dabei geben Sie das Azure-Datencenter an, in dem die mit diesem Konto erstellten Objekte gespeichert werden. Ein Blob gehört unabhängig davon, wo es sich befindet, stets zu einem Container in Ihrem Speicherkonto. Für den Zugriff auf ein Blob stellt eine Anwendung einen URL mit dem folgenden Format bereit:

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*Container*&gt;/&lt;*BlobName*&gt;

&lt;*StorageAccount*&gt; ist eine eindeutige ID, die beim Erstellen eines neuen Speicherkontos zugewiesen wird, während &lt;*Container*&gt; und &lt;*BlobName*&gt; die Namen eines bestimmten Containers und eines Blobs in diesem Container sind.

Azure stellt die beiden folgenden Arten von Blobs Die Auswahlmöglichkeiten sind:

- *Block*-Blobs, die jeweils bis zu 200 GB Daten enthalten können. Wie der Name besagt, ist ein Block-Blob in eine Reihe von Blöcken unterteilt. Wenn bei der Übertragung eines Block-Blobs ein Fehler auftritt, kann die Neuübertragung mit dem letzten Block fortgesetzt werden, sodass nicht das ganze Blob erneut gesendet werden muss. Block-Blobs sind sehr elementare Speicherformen und sind der heute am häufigsten verwendete Blob-Typ.

- *Seiten*-Blobs, die jeweils bis zu ein Terabyte groß sein können. Seiten-Blobs sind für den wahlfreien Zugriff vorgesehen. Daher ist jedes Blob in eine bestimmte Anzahl Seiten aufgeteilt. Eine Anwendung kann einzelne Seiten im Blob beliebig lesen und schreiben. In Azure Virtual Machines z. B. nutzen die von Ihnen erstellten virtuellen Computer Seiten-Blobs als beständigen Speicher für Betriebssystem- und Datenlaufwerke.

Unabhängig davon, ob Sie Block-Blobs oder Seiten-Blobs wählen, können Anwendungen auf verschiedene Weisen auf die Blob-Daten zugreifen. Folgende Optionen stehen dafür zur Verfügung:

- Direkt über ein RESTful- (d. h. HTTP-basiertes) Zugriffsprotokoll. Diese Option kann sowohl von Azure-Anwendungen als auch von externen Anwendungen, beispielsweise lokal ausgeführten Apps, verwendet werden.
- Über die Azure Storage Client-Bibliothek, die zusätzlich zum RESTful-Blob-Zugriffsprotokoll eine benutzerfreundlichere Oberfläche für Entwickler zur Verfügung stellt. Auch in diesem Fall können sowohl Azure-Anwendungen als auch externe Anwendungen mithilfe dieser Bibliothek auf Blobs zugreifen.
- Über Azure-Laufwerke, eine Option, bei der eine Azure-Anwendung ein Seiten-Blob als lokales Laufwerk mit einem NTFS-Dateisystem behandelt. Für die Anwendung sieht das Seiten-Blob wie ein gewöhnliches Windows Dateisystem aus, auf das mithilfe der Standarddatei E/A zugegriffen wird. Tatsächlich werden Lese- und Schreibvorgänge an das zugrunde liegende Seiten-Blob gesendet, das das Azure-Laufwerk implementiert. 

Zum Schutz vor Hardwareausfällen und zur Verbesserung der Verfügbarkeit wird jedes Blob in einem Azure-Datencenter auf drei Computern repliziert. Beim Schreiben eines Blobs werden alle drei Kopien aktualisiert, sodass bei späteren Lesevorgängen keine inkonsistenten Ergebnisse auftreten können. Sie können auch festlegen, dass die Daten eines Blobs in ein anderes Azure-Datencenter in derselben geografischen Region, jedoch mindestens 800 Kilometer entfernt, kopiert werden sollen. Dieser Kopiervorgang, die so genannte *Georeplikation*, findet innerhalb weniger Minuten nach der Aktualisierung des Blobs statt und ist hilfreich bei der Notfallwiederherstellung.

Daten in Blobs können auch über das Azure *Content Delivery Network (CDN)* zur Verfügung gestellt werden. Durch Zwischenspeichern von Kopien von Blob-Daten auf Dutzenden Servern überall auf der Welt kann das CDN den Zugriff auf Informationen beschleunigen, auf die wiederholt zugegriffen wird.

Dank ihrer Unkompliziertheit sind Blobs in vielen Situationen die richtige Option. Speichern und Streamen von Video- sowie Audiodaten sind nahe liegende Beispiele. Gleiches gilt für Sicherungen und andere Arten der Datenarchivierung. Entwickler können in Blobs zudem beliebige unstrukturierte Daten ablegen. Die Verfügbarkeit eines unkomplizierten Verfahrens zum Speichern und Zugreifen auf binäre Daten kann überraschend hilfreich sein.


## <a name="dbinvm"></a>Ausführen eines DBMS auf einem virtuellen Computer

Viele moderne Anwendungen sind auf ein bestimmtes Datenbank-Managementsystem (DBMS) angewiesen. Relationale Systeme wie SQL Server werden am häufigsten verwendet, doch werden nichtrelationale Ansätze, die allgemein als *Nicht-SQL*-Technologien bezeichnet werden, immer populärer. Damit Cloud-Anwendungen diese Datenmanagementoptionen nutzen können, ermöglicht Azure Virtual Machines die Ausführung eines (relationalen oder Nicht-SQL-) DBMS auf einem virtuellen Computer. [Abbildung 2](#Fig2) zeigt, wie dies bei SQL Server aussieht.

<a name="Fig2"></a>![Diagramm von SQL Server auf einem virtuellen Computer][SQLSvr-vm]
 
**Abbildung 2: Azure Virtual Machines ermöglicht die Ausführung eines DBMS auf einem virtuellen Computer, wobei Blobs für Persistenz sorgen.**

Für Entwickler und Datenbankadministratoren entspricht dieses Szenario im Wesentlichen der Ausführung der gleichen Software in ihrem eigenen Datencenter. Im hier gezeigten Beispiel können nahezu alle Fähigkeiten von SQL Server verwendet werden, und Sie haben vollen administrativen Zugriff auf das System. Natürlich sind Sie auch für die Verwaltung des Datenbankservers verantwortlich. In dieser Hinsicht gibt es keine Unterschiede zu einem lokalen Server.

Wie [Abbildung 2](#Fig2) zeigt, sind Ihre Datenbanken scheinbar auf dem lokalen Datenträger des virtuellen Computer gespeichert, auf dem der Server ausgeführt wird. Tatsächlich wird jedoch jeder Datenträger in ein Azure-Blob geschrieben. (Dies entspricht in etwa dem Einsatz eines SAN im Datencenter, wobei sich ein Blob ganz ähnlich wie ein LUN verhält.) Wie bei jedem Azure-Blob werden die darin enthaltenen Daten in einem Datencenter dreimal repliziert. Falls Sie es wünschen, werden sie außerdem in einem anderen Datencenter in derselben Region georepliziert. Zur Verbesserung der Zuverlässigkeit können auch Optionen wie die SQL Server-Datenbankspiegelung verwendet werden.

Eine weitere Einsatzmöglichkeit für SQL Server auf einem virtuellen Computer ist die Erstellung einer Hybridanwendung. Dabei werden die Daten in Azure verwaltet, während die Anwendungslogik lokal ausgeführt wird. Dies kann z. B. sinnvoll sein, wenn Anwendungen, die an mehreren Standorten oder auf verschiedenen mobilen Geräte ausgeführt werden, dieselben Daten gemeinsam nutzen müssen. Um die Kommunikation zwischen der Cloud-Datenbank und der lokalen Logik zu vereinfachen, kann eine Organisation mit Windows Azure Virtual Network eine VPN-Verbindung (virtuelles privates Netzwerk) zwischen einem Azure-Datencenter und ihrem eigenen lokalen Datencenter einrichten.


## <a name="sqldb"></a>SQL-Datenbank

Wenn strukturierte Daten in der Cloud verwaltet werden sollen, wird in der Regel zuerst ein DBMS auf einem virtuellen Computer in Betracht gezogen. Dies ist jedoch nicht die einzige Möglichkeit, und sie ist auch nicht immer die beste. In bestimmten Fällen ist zur Verwaltung von Daten der Ansatz "Platform as a Service (PaaS)" sinnvoller. Azure enthält eine PaaS-Technologie namens SQL-Datenbank, mit der Sie relationale Daten verwalten können. Diese Option ist in [Abbildung 3](#Fig3) dargestellt.

<a name="Fig3"></a>![Diagramm der SQL-Datenbank][SQL-db]
 
**Abbildung 3: SQL-Datenbank stellt einen gemeinsam genutzten relationalen PaaS-Speicherdienst bereit.**

Mit SQL-Datenbank verfügt jeder Kunde nicht über seine eigene physische Instanz von SQL Server. Stattdessen wird für jeden Kunden ein mehrinstanzenfähiger Dienst mit einem logischen SQL-Datenbankserver bereitgestellt. Alle Kunden nutzen gemeinsam die Rechen- und Speicherkapazität des Diensts. Wie beim Blob-Speicher werden alle Daten in SQL-Datenbank auf drei separaten Computern in einem Azure-Datencenter gespeichert, sodass Ihre Datenbanken vom Ansatz her hoch verfügbar sind.

Für eine Anwendung fungiert SQL-Datenbank weitgehend wie ein SQL-Server. Anwendungen können SQL-Abfragen an relationale Tabellen richten, gespeicherte T-SQL-Prozeduren verwenden und Transaktionen in mehreren Tabellen ausführen. Und da die Anwendungen auf SQL-Datenbank über das Tabular Data Stream (TDS)-Protokoll zugreifen, das auch für den Zugriff auf SQL Server verwendet wird, können sie über Entity Framework, ADO.NET, JDBC und weitere bekannte Datenzugriffsschnittstellen mit den Daten arbeiten.

Da SQL-Datenbank jedoch ein Clouddienst ist, der auf Azure-Datencentern ausgeführt wird, müssen Sie die physischen Aspekte des Systems wie z. B. die Datenträgernutzung nicht verwalten. Außerdem müssen Sie sich nicht um die Aktualisierung der Software oder andere untergeordnete administrative Aufgaben kümmern. Jede Kundenorganisation steuert natürlich weiterhin ihre eigenen Datenbanken einschließlich ihrer Schemata und Benutzeranmeldungen, doch viele banale administrative Aufgaben werden automatisch erledigt.

Obwohl SQL-Datenbank für die Anwendungen große Ähnlichkeit mit SQL Server hat, verhält es sich ein wenig anders als ein DBMS, das auf einem physischen oder virtuellen Computer ausgeführt wird. Da es auf gemeinsam genutzter Hardware ausgeführt wird, schwankt die Leistung mit der Auslastung der Hardware durch sämtliche Kunden. Daher kann z. B. die Leistung einer gespeicherten Prozedur in SQL-Datenbank von einem Tag zum andern variieren.

Derzeit können Sie mit SQL-Datenbank eine Datenbank bis zu einer Größe von 150 GB erstellen. Wenn Sie mit größeren Datenbanken arbeiten müssen, stellt der Dienst eine Option mit dem Namen *Verbund* bereit. Dazu erstellt ein Datenbankadministrator mindestens zwei *Verbundmitglieder*, bei denen es sich um separate Datenbanken mit jeweils eigenem Schema handelt. Die Daten werden auf diese Mitglieder verteilt. Dies wird häufig als *Sharding* (horizontales Partitionieren) bezeichnet, wobei jedem Mitglied ein eindeutiger *Verbundschlüssel* zugewiesen wird. Eine Anwendung richtet SQL-Abfragen an diese Daten und gibt dabei den Verbundschlüssel an. Dieser bestimmt das Verbundmitglied, das Ziel der Abfrage sein soll. Auf diese Weise ist auch bei sehr großen Datenmengen eine herkömmliche relationale Herangehensweise möglich. Wie immer gibt es jedoch auch hier neben Vorteilen einige Nachteile: Beispielsweise können sich weder Abfragen noch Transaktionen auf mehrere Verbundmitglieder beziehen. Wenn ein relationaler PaaS-Dienst die beste Wahl darstellt und diese Nachteile akzeptabel sind, kann ein SQL-Verbund eine gute Lösung sein.

SQL-Datenbank kann von Anwendungen verwendet werden, die unter Azure oder an anderen Orten ausgeführt werden, z. B. im lokalen Datencenter. Dadurch erweist sich SQL-Datenbank als nützlich für Cloud-Anwendungen, die relationale Daten benötigen, sowie für lokale Anwendungen, für die es vorteilhaft ist, Daten in der Cloud zu speichern. Eine mobile Anwendung kann z. B. zur Verwaltung gemeinsam genutzter relationaler Daten auf SQL-Datenbank zurückgreifen; Entsprechendes gilt für eine Inventuranwendung, die bei zahlreichen Händlern weltweit ausgeführt wird.

Die Überlegungen zur SQL-Datenbank werfen eine offensichtliche (und wichtige) Frage auf: Wann sollten SQL-Server auf einem virtuellen Computer ausgeführt werden und wann ist SQL-Datenbank eine bessere Wahl? Wie üblich, gibt es auch hier Vor- und Nachteile, und welcher Ansatz geeignet ist, hängt von den jeweiligen Anforderungen ab.

Um die Entscheidung zu vereinfachen, könnte SQL-Datenbank z. B. für neue Anwendungen verwendet werden; soll jedoch eine bestehende lokale Anwendung in die Cloud verschoben werden, wäre SQL Server auf einem virtuellen Computer die bessere Wahl. Es kann aber auch sinnvoll sein, in diese Entscheidung weitere Aspekte einzubeziehen. SQL-Datenbank ist beispielsweise benutzerfreundlicher, da der Aufwand für Einrichtung und Verwaltung minimal ist. Bei SQL Server auf einem virtuellen Computer hingegen lässt sich die Leistung besser vorhersagen, da es sich nicht um einen gemeinsam genutzten Dienst handelt und SQL Server außerdem größere nicht in einem Verbund organisierte Datenbanken unterstützt als SQL-Datenbank. SQL-Datenbank wiederum bietet die integrierte Replikation sowohl von Daten als auch von Verarbeitungsschritten und stellt mit sehr geringem Aufwand ein hoch verfügbares DBMS zur Verfügung. Während SQL Server mehr Steuerungsmöglichkeiten und etwas umfangreichere Optionen bereitstellt, ist SQL-Datenbank einfacher einzurichten und mit erheblich weniger Aufwand zu verwalten.

Zudem sollte auch darauf hingewiesen werden, dass SQL-Datenbank nicht der einzige PaaS-Datendienst ist, der unter Azure verfügbar ist. Microsoft-Partner bieten auch noch andere Optionen an. ClearDB beispielsweise bietet eine MySQL-PaaS-Lösung, während Cloudant eine Nicht-SQL-Option verkauft. PaaS-Datendienste sind die richtige Lösung in vielen Situationen, und daher ist dieser Datenverwaltungsansatz ein wichtiger Bestandteil von Azure.


### <a name="datasync"></a>SQL-Datensynchronisierung

Zwar verwaltet SQL-Datenbank drei Kopien jeder Datenbank in einem einzelnen Azure-Datencenter, doch werden die Daten zwischen den Azure-Datencentern nicht automatisch repliziert. Für diesen Zweck steht stattdessen der Dienst SQL-Datensynchronisierung zur Verfügung. [Abbildung 4](#Fig4) zeigt, wie dies aussieht.

<a name="Fig4"></a>![Diagramm der SQL-Datensynchronisierung][SQL-datasync]
 
**Abbildung 4: SQL-Datensynchronisierung synchronisiert Daten in SQL-Datenbank mit Daten in anderen Azure- und lokalen Datencentern.**

Wie das Diagramm zeigt, kann SQL-Datensynchronisierung Daten an verschiedenen Speicherorten synchronisieren. Angenommen, Sie führen eine Anwendung in mehreren Azure-Datencentern aus, wobei die Daten in SQL-Datenbank gespeichert sind. Mit SQL-Datensynchronisierung bleiben diese Daten stets synchronisiert. SQL-Datensynchronisierung kann auch Daten zwischen einem Azure-Datencenter und einer Instanz von SQL Server synchronisieren, die in einem lokalen Datencenter ausgeführt wird. Dies kann sinnvoll sein, um sowohl eine lokale Kopie von Daten, die von lokalen Anwendungen verwendet werden, als auch eine Cloud-Kopie zu verwalten, die von Anwendungen unter Azure verwendet wird. Und obwohl dies in der Abbildung nicht dargestellt wird, kann SQL-Datensynchronisierung auch zum Synchronisieren von Daten zwischen SQL-Datenbank und SQL Server verwendet werden, die auf einem virtuellen Computer in Azure oder an einem anderen Ort ausgeführt werden.

Die Synchronisierung kann bidirektional sein, und Sie legen genau fest, welche Daten synchronisiert werden und wie häufig dies geschieht. (Die Synchronisierung zwischen Datenbanken erfolgt jedoch nicht atomisch, sondern es gibt stets eine gewisse Verzögerung.) Unabhängig davon, wie die Synchronisierung verwendet wird, wird die Synchronisierung mit SQL-Datensynchronisierung vollständig per Konfiguration eingerichtet, und es muss kein Code geschrieben werden.


### <a name="datarpt"></a>SQL-Datenberichte mithilfe virtueller Computer

Sobald eine Datenbank Daten enthält, gibt es wahrscheinlich das Bestreben, Berichte zu erstellen, in denen diese Daten verwendet werden. Azure kann SQL Server Reporting Services (SSRS) auf Azure Virtual Machines ausführen. Dies entspricht funktional der lokalen Ausführung von SQL Server Reporting Services. Anschließend können Sie mit SSRS Berichte auf der Grundlage von Daten erstellen, die in einer Azure-SQL-Datenbank gespeichert sind. [Abbildung 5](#Fig5) zeigt, wie der Prozess funktioniert.

<a name="Fig5"></a>![Diagramm der SQL-Berichterstattung][SQL-report]
 
**Abbildung 5: SQL Server Reporting Services in Azure Virtual Machines stellen Berichtsdienste für Daten in SQL-Datenbank bereit.**

Bevor ein Benutzer einen Bericht sieht, wird festgelegt, wie der Bericht aussehen soll (Schritt 1). Mit SSRS auf einem virtuellen Computer kann dies mithilfe von zwei Tools geschehen: mit SQL Server Data Tools, Teil von SQL Server 2012, oder mit dessen Vorläufer Business Intelligence (BI) Development Studio. Wie bei SSRS werden diese Berichtsdefinitionen in der Berichtsdefinitionssprache (Report Definition Language, RDL) geschrieben. Nachdem die RDL-Dateien für einen Bericht erstellt wurden, werden sie zu einem virtuellen Computer in der Cloud hochgeladen (Schritt 2). Die Berichtsdefinition kann jetzt verwendet werden.

Anschließend greift ein Benutzer der Anwendung auf den Bericht zu (Schritt 3). Die Anwendung gibt diese Anforderung an den virtuellen SSRS-Computer weiter (Schritt 4), der SQL-Datenbank oder andere Datenquellen kontaktiert, um die benötigten Daten abzurufen (Schritt 5). SSRS verwendet diese Daten und die entsprechenden RDL-Dateien, um den Bericht zu generieren (Schritt 6), und sendet den Bericht danach zur Anwendung zurück (Schritt 7), die ihn dem Benutzer anzeigt (Schritt 8).

Das hier dargestellte Szenario einer Einbettung eines Berichts in eine Anwendung ist nicht die einzige Option. Es ist auch möglich, Berichte im Berichts-Manager auf dem virtuellen Computer, in SharePoint auf dem virtuellen Computer oder auf andere Weise anzuzeigen. Berichte können auch kombiniert werden, wobei ein Bericht einen Link zu einem anderen enthält.

Mit SSRS auf einem virtuellen Azure-Computer verfügen Sie in der Cloud über eine Berichterstattungslösung mit vollem Funktionsumfang. In Berichten können alle von SSRS unterstützten Datenquellen verwendet werden. Anwendungen und Berichte können eingebetteten Code oder Assemblys enthalten, um benutzerdefinierte Verhaltensweisen zu unterstützen. Berichte werden schnell ausgeführt und generiert, da Berichtsserverinhalte und -modul auf demselben virtuellen Server ausgeführt werden.



## <a name="tblstor"></a>Table Storage

Relationale Daten sind in vielen Situationen sinnvoll, jedoch nicht immer die richtige Wahl. Wenn eine Anwendung z. B. schnellen, einfachen Zugriff auf sehr große Mengen schwach strukturierter Daten benötigt, ist eine relationale Datenbank eventuell nicht gut geeignet. Eine Nicht-SQL-Technologie ist in diesem Fall wahrscheinlich die bessere Option.

Azure Table Storage ist ein Beispiel für einen solchen Nicht-SQL-Ansatz. Trotz seines Namens unterstützt Table Storage keine normalen relationalen Tabellen. Stattdessen stellt es einen *Schlüssel-/Wertspeicher* bereit. Dabei wird ein Datensatz mit einem bestimmten Schlüssel verknüpft, und anschließend erhält eine Anwendung über diesen Schlüssel Zugriff auf die Daten. Die Grundzüge dieses Vorgangs sind in [Abbildung 6](#Fig6) dargestellt.

<a name="Fig6"></a>![Diagramm von Table Storage][SQL-tblstor]
 
**Abbildung 6: Azure Table Storage ist ein Schlüssel-/Wertspeicher, der einen schnellen, einfachen Zugriff auf große Datenmengen ermöglicht.**

Wie Blobs werden alle Tabellen mit einem Azure-Speicherkonto verknüpft. Tabellen werden auch ähnlich wie Blobs mit einem URL folgenden Formats benannt:

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*TableName*&gt;

Wie die Abbildung zeigt, ist jede Tabelle in mehrere Partitionen aufgeteilt, die jeweils auf einem separaten Computer gespeichert werden können. (Dies ist wie bei einem SQL-Verbund eine Art von Sharding.) Sowohl Azure-Anwendungen als auch Anwendungen, die an anderen Orten ausgeführt werden, können entweder über das RESTful OData-Protokoll oder über die Azure Storage Client-Bibliothek auf eine Tabelle zugreifen.

Jede Partition in einer Tabelle enthält mehrere *Entitäten*, die jeweils bis zu 255 *Eigenschaften* enthalten. Jede Eigenschaft hat einen Namen, einen Typ (z. B. Binär, Boolesch, DatumZeit, Int oder Zeichenfolge) und einen Wert. Im Unterschied zum relationalen Speicher haben diese Tabellen kein festes Schema, sodass verschiedene Entitäten in derselben Tabelle Eigenschaften unterschiedlichen Typs enthalten können. Eine Entität hat z. B. nur eine Zeichenfolge-Eigenschaft, die einen Namen enthält, während eine andere Entität in derselben Tabelle zwei Int-Eigenschaften hat, die eine Kunden-ID und eine Bonitätseinstufung.

Um eine bestimmte Entität in einer Tabelle zu identifizieren, stellt eine Anwendung den Schlüssel der betreffenden Entität bereit. Der Schlüssel besteht aus zwei Teilen: ein *Partitionsschlüssel*, der eine bestimmte Partition kennzeichnet, und einem *Zeilenschlüssel*, der eine Entität innerhalb dieser Partition kennzeichnet. In [Abbildung 6](#Fig6) fordert der Client z. B. die Entität mit dem Partitionsschlüssel A und Zeilenschlüssel 3 an, und Table Storage gibt diese Entität zusammen mit allen darin enthaltenen Eigenschaften zurück.

Diese Struktur ermöglicht große Tabellen – eine einzelne Tabelle kann bis zu 100 TB Daten enthalten –, und sie ermöglicht einen schnellen Zugriff auf die darin enthaltenen Daten. Sie geht jedoch auch mit Einschränkungen einher. Transaktionale Aktualisierungen, die sich über verschiedene Tabellen oder auch Partitionen in einer einzelnen Tabelle erstrecken, werden z. B. nicht unterstützt. Mehrere Aktualisierungen einer Tabelle können nur dann in einer atomischen Transaktion gruppiert werden, wenn alle beteiligten Entitäten in derselben Partition enthalten sind. Außerdem gibt es keine Möglichkeit, eine Tabelle auf Basis des Werts ihrer Eigenschaften abzufragen, und Verknüpfungen mehrerer Tabellen werden nicht unterstützt. Und im Gegensatz zu relationalen Datenbanken unterstützen Tabellen gespeicherte Prozeduren nicht.

Azure Table Storage ist eine gute Option für Anwendungen, die schnell mit wenig Aufwand auf große Mengen schwach strukturierter Daten zugreifen müssen. Tabellen können z. B. von einer Internetanwendung verwendet werden, die Profildaten für zahlreiche Benutzer speichert. In diesem Fall kommt es auf schnellen Zugriff an, und die Anwendung benötigt wahrscheinlich nicht die volle Leistungsfähigkeit von SQL. Das Aufgeben dieses Funktionsumfangs zugunsten von mehr Geschwindigkeit und Größe kann gelegentlich sinnvoll sein, sodass Table Storage für bestimmte Probleme genau die richtige Lösung ist.


## <a name="hadoop"></a>Hadoop

Organisationen erstellen schon seit Jahrzehnten Data Warehouses. Diese Sammlungen von Informationen, die meist in relationalen Tabellen gespeichert sind, bieten Benutzern vielfältige Möglichkeiten der Bearbeitung und Nutzung von Daten. Mit SQL Server werden dazu z. B. üblicherweise Tools wie SQL Server Analysis Services verwendet.

Nehmen wir jedoch an, Sie möchten nichtrelationale Daten analysieren. Ihre Daten können die unterschiedlichste Form haben: Informationen von Sensoren oder RFID-Tags, Protokolldateien in Serverfarmen, Clickstreamdaten, die von Webanwendungen erzeugt werden, Bilder von medizinischen Diagnosegeräten, usw. Diese Daten können auch wirklich umfangreich sein, zum umfangreich, um in einem traditionellen Data Warehouse effektiv genutzt zu werden. Solche Probleme mit umfangreichen Daten, die noch vor wenigen Jahren selten waren, sind inzwischen sehr verbreitet.

Um umfangreiche Daten dieses Typs zu analysieren, hat sich unsere Branche weitgehend auf eine einzige Lösung geeinigt: die Open Source-Technologie Hadoop. Hadoop wird auf einem Cluster physischer oder virtueller Computer ausgeführt, verteilt die zu bearbeitenden Daten auf diese Computer und verarbeitet sie parallel. Je mehr Computer Hadoop verwendet, desto schneller können die jeweiligen Arbeiten ausgeführt werden.

Für Probleme dieser Art ist die öffentliche Cloud wie geschaffen. Statt unzählige lokale Server zu unterhalten, die möglicherweise weitgehend im Leerlauf arbeiten, bietet Hadoop in der Cloud die Möglichkeit, virtuelle Computer nur dann zu erstellen (und zu bezahlen), wenn Sie sie benötigen. Hinzu kommt jedoch noch, dass immer mehr umfangreiche Daten, die Sie mit Hadoop analysieren möchten, in der Cloud erstellt werden, was Ihnen die Mühe erspart, diese Daten zu verschieben. Um diese Synergien zu nutzen, bietet Microsoft in Azure einen Hadoop-Dienst an. [Abbildung 7](#Fig7) zeigt die wichtigsten Komponenten dieses Diensts.

<a name="Fig7"></a>![Diagramm von Hadoop][hadoop]

**Abbildung 7: Hadoop in Azure führt MapReduce-Jobs aus, die Daten mit mehreren virtuellen Computern parallel verarbeiten.**

Um Hadoop in Azure zu verwenden, fordern Sie diese Cloud-Plattform zuerst auf, ein Hadoop-Cluster zu erstellen, wobei Sie die Anzahl benötigter virtueller Computer angeben. Das Einrichten eines Hadoop-Clusters eine anspruchsvolle Aufgabe, die Sie sinnvollerweise Azure überlassen sollten. Wenn Sie das Cluster nicht mehr benötigen, fahren Sie es herunter. Sie sollten keine Rechenressourcen bezahlen, die Sie nicht nutzen.

Eine Hadoop-Anwendung, die üblicherweise als *Job* bezeichnet wird, verwendet ein als *MapReduce* bekanntes Programmiermodell. Die Abbildung zeigt, dass die Logik für einen MapReduce-Job gleichzeitig auf vielen virtuellen Computern ausgeführt wird. Dank der parallelen Datenverarbeitung kann Hadoop Daten wesentlich schneller analysieren als Einzelcomputerlösungen.

In Azure werden die mit einem MapReduce-Job bearbeiteten Daten in einem Blob-Speicher abgelegt. MapReduce-Jobs in Hadoop setzen jedoch voraus, dass die Daten im *Hadoop Distributed File System (HDFS)* gespeichert sind. HDFS hat gewisse Ähnlichkeit mit dem Blob-Speicher, denn Daten werden z. B. auf mehreren physischen Servern repliziert. Doch anstatt diese Funktionen lediglich zu kopieren, stellt Hadoop unter Azure den Blob-Speicher über die HDFS-API zur Verfügung, wie die Abbildung zeigt. Während es für die Logik eines MapReduce-Jobs so aussieht, als erfolge ein Zugriff auf gewöhnliche HDFS-Dateien, werden in Wirklichkeit aus Blobs gestreamte Daten bearbeitet. Und um die Bearbeitung derselben Daten durch mehrere Jobs zu unterstützen, ist es in Hadoop unter Azure auch möglich, Daten aus Blobs in ein vollwertiges HDFS auf den virtuellen Computern zu kopieren.

MapReduce-Jobs werden heute für gewöhnlich in Java geschrieben, ein Ansatz, den Hadoop unter Azure unterstützt. Microsoft hat zusätzlich dafür gesorgt, dass MapReduce-Jobs auch in anderen Sprachen wie C#, F# und JavaScript erstellt werden können. Ziel ist es, diese Technologie für umfangreiche Daten einer größeren Zahl von Entwicklern zugänglicher zu machen.

Neben HDFS und MapReduce enthält Hadoop weitere Technologien zur Analyse von Daten, ohne dass ein MapReduce-Job geschrieben werden muss. Pig z. B. ist eine höhere Programmiersprache, die auf die Analyse umfangreicher Daten zugeschnitten ist, während Hive über eine SQL-ähnliche Sprache namens HiveQL verfügt. Sowohl Pig als auch Hive erzeugen MapReduce-Jobs, mit denen HDFS-Daten verarbeitet werden, verbergen die Komplexität dieser Jobs jedoch vor ihren Benutzern. Beide Sprachen stehen mit Hadoop unter Azure zur Verfügung.

Microsoft stellt auch einen HiveQL-Treiber für Excel bereit. Über ein Excel-Add-In können Wirtschaftsanalytiker HiveQL-Abfragen (und somit MapReduce-Jobs) direkt in Excel erstellen und die Ergebnisse anschließend mit PowerPivot und anderen Excel-Tools verarbeiten und visualisieren. Hadoop unter Azure umfasst noch weitere Technologien, u. a. beispielsweise die Mahout-Bibliotheken für maschinelles Lernen, das Graph-Miningsystem Pegasus.

Die Analyse umfangreicher Daten ist wichtig, und daher ist Hadoop ebenfalls wichtig. Durch Bereitstellen von Hadoop als verwalteten Dienst in Azure mit Verknüpfungen zu bekannten Tools wie Excel möchte Microsoft einer größeren Gruppe von Benutzern Zugang zu dieser Technologie verschaffen.

Da generell gesehen alle Arten von Daten wichtig sind, enthält Azure verschiedene Optionen für die Datenverwaltung und die Geschäftsanalyse. Gleichgültig, was für Anwendungen Sie erstellen möchten, diese Cloud-Plattform bietet wahrscheinlich eine für Ihr Vorhaben geeignete Lösung.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png

<!---HONumber=August15_HO6-->