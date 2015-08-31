<properties
	pageTitle="Grenzwerte für den Azure Search-Dienst"
	description="Grenzwerte für Azure Search, die bei der Kapazitätsplanung verwendet werden, sowie Höchstwerte für Anforderungen und Antworten."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.date="08/18/2015"
	ms.author="heidist"/>

#Grenzwerte für den Azure Search-Dienst

Die Höchstwerte für Speicher, Workloads und Mengen von Indizes, Dokumenten und anderen Objekten hängen von Ihrem Azure Search-Abonnement ab. Der kostenlose Dienst ist für Evaluierungszwecke und Machbarkeitsstudien gedacht und weist niedrigere Grenzwerte für alle Objekte und Workloads auf, sodass Ressourcen in ausgewogenerer Form gemeinsam genutzt werden können.

Der Standarddienst wird auf dedizierten Computern ausgeführt, die nur von Ihrem Dienst verwendet werden. Die alleinige Verwendung eines dedizierten Diensts bietet Ihnen die Möglichkeit des Hoch- oder Herunterskalierens mit höherer Speicher- und Verarbeitungskapazität auf jeder Ebene, einschließlich der Mindestkonfiguration.

##Höchstwerte für einen kostenlosen (gemeinsam genutzten) Search-Dienst

Azure-Abonnenten können den gemeinsam genutzten Search-Dienst (mehrere Mandanten) für die Entwicklung oder sehr kleine Suchanwendungen verwenden. Der gemeinsam genutzte Dienst ist im Azure-Abonnement enthalten. Es ist eine kostenfreie Option, die Ihnen das Ausprobieren des Diensts ermöglicht, bevor Sie sich dafür registrieren. Sie umfasst Folgendes:

Objekt|Begrenzung
------|-----
Maximale Anzahl der Indizes|3
Maximale Anzahl der Felder pro Index|1000
Maximale Dokumentanzahl|10\.000
Maximale Speichergröße|50 MB
Maximale Anzahl der Partitionen|N/V
Maximale Anzahl der Replikate|N/V
Maximale Anzahl der Sucheinheiten|N/V
Maximale Anzahl der Indexer|3
Maximale Anzahl der Indexer-Datenquellen|3
Maximale Anzahl der indizierten Dokumente pro Indexer-Aufruf|10\.000

Beachten Sie, dass Abfragen keine Kontingente oder Höchstwerte zugeordnet sind. Die Abfragen pro Sekunde (Queries per Second, QPS) sind variabel und hängen von der verfügbaren Bandbreite und der Konkurrenz um Systemressourcen ab. Die Azure-Computing- und Speicherressourcen zur Unterstützung des gemeinsam genutzten Diensts werden von mehreren Abonnenten gemeinsam verwendet, sodass der QPS-Wert für Ihre Lösung in Abhängigkeit davon variiert, wie viele andere Workloads gleichzeitig ausgeführt werden.

##Höchstwerte für einen (dedizierten) Search-Standarddienst

Beim Standardtarif werden in einem dedizierten Search-Dienst nur Ihre Daten gespeichert und nur Ihre Workloads ausgeführt. Im Gegensatz zum gemeinsam genutzten Dienst ist die Ressourcenzuordnung für einen dedizierten Search-Dienst anpassbar und kann auf jede benötigte Ebene skaliert werden. Sie können die Ressourcenebenen für Partitionen (Hochskalieren von Speicher) und Replikate (für hohe Verfügbarkeit und Hochskalieren von QPS und Indizierungsleistung) unabhängig voneinander festlegen. Genauere Einblicke in die verschiedenen Ressourcenkonfigurationen erhalten Sie unter [Verwalten Ihrer Suchlösung](search-manage.md).

Die folgende Tabelle enthält eine Liste der Höchstgrenzen, doch sollten Sie auch die Tabelle weiter unten betrachten, um eine Vorstellung von der Kapazität im Zusammenhang mit zulässigen [Kombinationen von Partitionen und Replikaten](#chart) zu erhalten.

Objekt|Begrenzung
------|----
Maximale Anzahl der Indizes|50 pro Search-Dienst
Maximale Anzahl der Felder pro Index|1000
Maximale Dokumentanzahl|15 Mio. pro Partition
Maximale Speichergröße|25 GB pro Partition
Maximale Anzahl der Partitionen|12 pro Search-Dienst
Maximale Anzahl der Replikate|6 pro Search-Dienst
Maximale Anzahl der Sucheinheiten|36 pro Search-Dienst
Maximale Anzahl der Indexer|50 pro Search-Dienst
Maximale Anzahl der Indexer-Datenquellen|50 pro Search-Dienst
Maximale Anzahl der indizierten Dokumente pro Indexer-Aufruf|Unbegrenzt

Kapazität in Azure Search kann in Schritten mit der Bezeichnung Sucheinheiten erworben werden. Der Standardtarif ermöglicht bis zu 36 Sucheinheiten pro Search-Dienst. Dieser Grenzwert hat Vorrang vor den individuellen Grenzwerten für Partitionen und Replikate. Sie können den Dienst z. B. nicht auf 12 Partitionen und 6 Replikate hochskalieren, da dazu 72 Sucheinheiten (12 x 6) erforderlich wären und dies den Grenzwert von 36 Sucheinheiten pro Dienst übersteigt.

##Informationen zu Partitionen und Replikaten

**Partitionen** bieten Speicher und E/A. Ein einzelner Search-Dienst kann maximal 12 Partitionen umfassen. Jede Partition weist einen festen Grenzwert von 15 Millionen Dokumenten oder 25 GB Speicher auf, je nachdem, was zuerst erreicht wird. Wenn Sie Partitionen hinzufügen, können mehr Dokumente im Search-Dienst geladen werden. Ein Dienst mit einer einzelnen Partition, in dem anfänglich bis zu 25 GB Daten gespeichert werden, kann beispielsweise 50 GB speichern, wenn Sie eine zweite Partition zum Dienst hinzufügen.

**Replikate** sind Kopien der Suchmaschine. Ein einzelner Search-Dienst kann maximal 6 Replikate aufweisen. Sie benötigen mindestens 2 Replikate für Leseverfügbarkeit (Abfrage) und mindestens 3 Replikate für Lese-/Schreibverfügbarkeit (Abfrage, Indizierung).

Für jedes Replikat wird eine Kopie jedes Indexes ausgeführt. Beim Hinzufügen von Replikaten werden zusätzliche Kopien des Indexes online gestellt, um höhere Abfrageworkloads zu unterstützen und die Anforderungslast gleichmäßig auf mehrere Replikate zu verteilen. Wenn Sie über mehrere Indizes (z. B. 6) und 3 Replikate verfügen, weist jedes Replikat eine Kopie aller 6 Indizes auf.

Beachten Sie, dass keine festen Schätzungen für die Abfragen pro Sekunde (QPS) gegeben werden, da die Ausführung von Abfragen je nach Komplexität der jeweiligen Abfrage und konkurrierender Workloads stark variieren kann. Durchschnittlich kann ein Replikat etwa 15 QPS bedienen, doch ist der Durchsatz je nach Komplexität der Abfrage (Facettenabfragen sind komplexer) und Netzwerklatenz etwas höher oder niedriger. Darüber hinaus ist zu beachten, dass beim Hinzufügen von Replikaten zwar Skalierung und Leistung erhöht werden, das Endergebnis aber nicht streng linear ist: Das Hinzufügen von 3 Replikaten garantiert keinen dreifachen Durchsatz. Die Abfragewartezeit ist ein Indikator, dass möglicherweise zusätzliche Replikate erforderlich sind.

<a id="chart"></a>
##Unterstützte Kombinationen von Partitionen und Replikaten

Wie bereits erwähnt, basiert der effektive Grenzwert für Partitionen und Replikate auf der Kombination von Ihnen ausgewählter Ressourcen, wobei die Grenze von 36 Sucheinheiten pro Dienst eingehalten wird. Ressourcen werden in Form von Sucheinheiten (Search Units, SU) zugeordnet. Ein dedizierter Search-Dienst umfasst zunächst ein Replikat und eine Partition als eine Sucheinheit.

Zusätzliche Kapazität wird als Partitionen multipliziert mit Replikaten berechnet, wodurch sich eine Gesamtzahl der Sucheinheiten ergibt, die zur Unterstützung einer bestimmten Konfiguration erforderlich sind.

In der folgenden Tabelle sind Replikate auf der vertikalen Achse und Partitionen auf der horizontalen Achse aufgelistet. Die Schnittpunkte zeigen die Anzahl der Sucheinheiten, die zur Unterstützung der jeweiligen Kombination erforderlich sind, wobei der Grenzwert von 36 Sucheinheiten (SU) pro Dienst beachtet wird. Wenn Sie beispielsweise über 6 Replikate und 2 Partitionen verfügen möchten, würde diese Konfiguration 12 Sucheinheiten erfordern. Zur Verwendung von 4 Replikaten und 2 Partitionen wären 8 Sucheinheiten erforderlich. Im Allgemeinen gilt, dass die meisten Suchanwendungen tendenziell mehr Replikate als Partitionen benötigen.

<table cellspacing="0" border="1">
<tr><td><b>6 Replikate</b></td><td>6 SU</td><td>12 SU</td><td>18 SU</td><td>24 SU</td><td>36 SU</td><td>N/V</td></tr>
<tr><td><b>5 Replikate</b></td><td>5 SU</td><td>10 SU</td><td>15 SU</td><td>20 SU</td><td>30 SU</td><td>N/V</td></tr>
<tr><td><b>4 Replikate</b></td><td>4 SU</td><td>8 SU</td><td>12 SU</td><td>16 SU</td><td>24 SU</td><td>N/V </td></tr>
<tr><td><b>3 Replikate</b></td><td>3 SU</td><td>6 SU</td><td>9 SU</td><td>12 SU</td><td>18 SU</td><td>36 SU</td></tr>
<tr><td><b>2 Replikate</b></td><td>2 SU</td><td>4 SU</td><td>6 SU</td><td>8 SU</td><td>12 SU</td><td>24 SU</td></tr>
<tr><td><b>1 Replikat</b>:</td><td>1 SU</td><td>2 SU</td><td>3 SU</td><td>4 SU</td><td>6 SU</td><td>12 SU</td></tr>
<tr><td>N/V</td><td><b>1 Partition</b></td><td><b>2 Partitionen</b></td><td><b>3 Partitionen</b></td><td><b>4 Partitionen</b></td><td><b>6 Partitionen</b></td><td><b>12 Partitionen</b></td></tr> 
</table>

Sucheinheiten, Preise und Kapazität werden auf der Azure-Website ausführlich erläutert. Weitere Informationen finden Sie unter [Preise](http://azure.microsoft.com/pricing/details/search/).

> [AZURE.NOTE]Die ausgewählte Anzahl der Partitionen muss gleichmäßig in 12 unterteilt werden können (d. h. 1, 2, 3, 4, 6, 12). Der Grund dafür ist, dass Azure Search jeden Index vorab in 12 Shards unterteilt, damit er auf Partitionen verteilt werden kann. Wenn Ihr Dienst z. B. drei Partitionen aufweist und Sie einen neuen Index erstellen, enthält jede Partition 4 Shards des Indexes. Die Form, in der Azure Search Shards eines Indexes erstellt, ist ein Implementierungsdetail, dass sich bei zukünftigen Versionen ändern kann. Auch wenn die Anzahl heute 12 beträgt, sollten Sie nicht davon ausgehen, das dies auch in Zukunft immer so ist.

##Auswählen einer Kombination von Partitionen und Replikaten für hohe Verfügbarkeit

Da ein Hochskalieren einfach und relativ schnell durchzuführen ist, wird im Allgemeinen empfohlen, mit einer Partition und einem oder zwei Replikaten zu beginnen und dann bei steigenden Abfragevolumen hochzuskalieren. Bei vielen Bereitstellungen bietet eine Partition ausreichend Speicher und E/A (bei 15 Millionen Dokumenten pro Partition).

Abfrageworkloads basieren jedoch auf Replikaten. Wenn Sie einen höheren Durchsatz oder hohe Verfügbarkeit benötigen, können zusätzliche Replikate erforderlich sein.

Allgemeine Empfehlungen für hohe Verfügbarkeit sind:

- 2 Replikate für hohe Verfügbarkeit von schreibgeschützten Workloads (Abfragen)
- 3 oder mehr Replikate für hohe Verfügbarkeit von Lese-/ Schreibworkloads (Abfragen und Indizierung)

Derzeit steht kein integrierter Mechanismus für die Notfallwiederherstellung bereit. Das Hinzufügen von Partitionen oder Replikaten wäre die falsche Strategie, um die Zielsetzungen für eine Notfallwiederherstellung zu erfüllen. Stattdessen sollten Sie das Hinzufügen von Redundanz auf Dienstebene in Betracht ziehen. Eine eingehendere Besprechung der Methoden finden Sie in [diesem Forumsbeitrag](https://social.msdn.microsoft.com/Forums/ee108a26-00c5-49f6-b1ff-64c66c8b828a/dr-and-high-availability-for-azure-search?forum=azuresearch).

> [AZURE.NOTE]Denken Sie daran, dass Vereinbarungen zum Servicelevel und Skalierbarkeit Features des Standarddiensts sind. Der kostenlose Dienst wird auf fester Ressourcenebene angeboten, wobei Replikate und Partitionen von mehreren Abonnenten gemeinsam genutzt werden. Wenn Sie mit dem kostenlosen Dienst begonnen haben und jetzt ein Upgrade durchführen möchten, müssen Sie einen neuen Azure Search-Dienst auf Standardebene erstellen und dann Indizes und Daten erneut in den neuen Dienst laden. Anweisungen zur Dienstbereitstellung finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-portal.md).

##Grenzwerte für API-Schlüssel

API-Schlüssel werden für die Dienstauthentifizierung verwendet. Es gibt zwei Arten. Administratorschlüssel werden im Anforderungsheader angegeben. Abfrageschlüssel werden in der URL angegeben. Ausführliche Informationen zur Schlüsselverwaltung finden Sie unter [Verwalten Ihres Suchdiensts in Microsoft Azure](search-manage.md).

- Maximal 2 Administratorschlüssel pro Dienst
- Maximal 50 Abfrageschlüssel pro Dienst

##Grenzwerte für Anforderungen

- Maximal 16 MB pro Anforderung
- Maximale URL-Länge von 8 KB 
- Maximal 1000 Dokumente pro Batch von Hochlade-, Zusammenführungs- oder Löschvorgängen für Indizes
- Maximal 32 Felder in $orderby-Klausel
- Maximale Suchbegriffgröße ist 32.766 Bytes (32 KB minus 2 Bytes) von UTF-8-codiertem Text

##Grenzwerte für Antworten

- Maximale Rückgabe von 1000 Dokumenten pro Seite mit Suchergebnissen
- Maximale Rückgabe von 100 Vorschlägen pro Anforderung der Vorschlags-API

<!---HONumber=August15_HO8-->