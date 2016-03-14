<properties
	pageTitle="Kapazitätsplanung in Azure Search | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Die Kapazität in Azure Search wird aus den Partitionen und Replikaten erstellt, wobei es sich jeweils um eine abrechenbare Sucheinheit handelt."
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
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Kapazitätsplanung in Azure Search

In Azure Search können Sie die Kapazität spezifischer Rechenressourcen inkrementell anpassen. Sie können Partitionen erhöhen, wenn Sie mehr Speicher und E/A oder Replikate für eine verbesserte Abfrage- und Indizierungsleistung benötigen.

Skalierbarkeit wird verfügbar, wenn Sie einen Dienst zum [Basic-Tarif](http://aka.ms/azuresearchbasic) oder zu einem der [Standard-Tarife](search-limits-quotas-capacity.md) bereitstellen.

Bei beiden Tarifen wird die Kapazität in *Sucheinheit* (SU)-Schritten gekauft, wobei jede Partition und jedes Replikat als jeweils eine „SU“ zählen.

- Basic bietet bis zu 3 SU pro Dienst.
- Standard bietet bis zu 36 SU pro Dienst.

Sie müssen eine Kombination aus Partitionen und Replikaten auswählen, die unterhalb des Grenzwerts für den Tarif bleibt. Sie können beispielsweise den Standard-Dienst nicht auf 12 Partitionen und 6 Replikate hochskalieren, da dafür 72 Sucheinheiten (12 x 6) erforderlich wären und dies den Grenzwert von 36 Sucheinheiten pro Dienst übersteigt.

Im Allgemeinen gilt, dass die Suchanwendungen tendenziell mehr Replikate als Partitionen benötigen. Weitere Details finden Sie im Abschnitt zu [hoher Verfügbarkeit](#HA).

**Berechnen von Sucheinheiten für bestimmte Ressourcenkombinationen: R X P = SU**

Die Formel zum Berechnen der Anzahl der benötigten SUs lautet: Replikate multipliziert mit Partitionen. 3 Replikate multipliziert mit 3 Partitionen werden z. B. als 9 Sucheinheiten abgerechnet.

Beide Tarife beginnen mit einem Replikat und einer Partition, die als eine Sucheinheit (SU) gezählt werden. Dies ist die einzige Instanz, in der ein Replikat und eine Partition als eine einzelne Sucheinheit gerechnet werden. Jede zusätzliche Ressource, sei es ein Replikat oder eine Partition, wird als eigene SU gezählt.

Die Kosten pro SU werden vom Tarif bestimmt. Die Kosten pro SU für den Basic-Tarif sind niedriger als für den Standard-Tarif. Die Preise für jeden Tarif finden Sie in den [Preisdetails](https://azure.microsoft.com/pricing/details/search/).

## Basic-Tarif: Partitions- und Replikatskombinationen

Ein Basic-Dienst kann aus 1 Partition und bis zu 3 Replikaten bestehen, für einen maximalen Grenzwert von 3 SUs.

<table cellspacing="0" border="1">
<tr><td><b>3 Replikate</b></td><td>3 SU</td></tr>
<tr><td><b>2 Replikate</b></td><td>2 SU</td></tr>
<tr><td><b>1 Replikat:</b></td><td>1 SU</td></tr>
<tr><td></td><td><b>1 Partition</b></td></tr>
</table>

<a id="chart"></a>
## Standard-Tarif: Partitions- und Replikatskombinationen

Diese Tabelle enthält die Sucheinheiten, die für eine Unterstützung der Kombinationen aus Replikaten und Partitionen erforderlich sind. Dabei gilt ein Grenzwert von 36 Sucheinheiten (SU).

<table cellspacing="0" border="1">
<tr><td><b>12 Replikate</b></td><td>12 SU</td><td>24 SU</td><td>36 SU</td><td>N/V</td><td>–</td><td>N/V</td></tr>
<tr><td><b>6 Replikate</b></td><td>6 SU</td><td>12 SU</td><td>18 SU</td><td>24 SU</td><td>36 SU</td><td>N/V</td></tr>
<tr><td><b>5 Replikate</b></td><td>5 SU</td><td>10 SU</td><td>15 SU</td><td>20 SU</td><td>30 SU</td><td>N/V</td></tr>
<tr><td><b>4 Replikate</b></td><td>4 SU</td><td>8 SU</td><td>12 SU</td><td>16 SU</td><td>24 SU</td><td>N/V </td></tr>
<tr><td><b>3 Replikate</b></td><td>3 SU</td><td>6 SU</td><td>9 SU</td><td>12 SU</td><td>18 SU</td><td>36 SU</td></tr>
<tr><td><b>2 Replikate</b></td><td>2 SU</td><td>4 SU</td><td>6 SU</td><td>8 SU</td><td>12 SU</td><td>24 SU</td></tr>
<tr><td><b>1 Replikat:</b></td><td>1 SU</td><td>2 SU</td><td>3 SU</td><td>4 SU</td><td>6 SU</td><td>12 SU</td></tr>
<tr><td>N/V</td><td><b>1 Partition</b></td><td><b>2 Partitionen</b></td><td><b>3 Partitionen</b></td><td><b>4 Partitionen</b></td><td><b>6 Partitionen</b></td><td><b>12 Partitionen</b></td></tr>
</table>

Sucheinheiten, Preise und Kapazität werden auf der Azure-Website ausführlich erläutert. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/search/).

> [AZURE.NOTE] Die Anzahl der Replikate und Partitionen muss gleichmäßig in 12 unterteilt werden können (d. h. 1, 2, 3, 4, 6, 12). Der Grund dafür ist, dass Azure Search jeden Index vorab in 12 Shards unterteilt, damit er auf Partitionen verteilt werden kann. Wenn Ihr Dienst z. B. drei Partitionen aufweist und Sie einen neuen Index erstellen, enthält jede Partition 4 Shards des Indexes. Die Form, in der Azure Search Shards eines Indexes erstellt, ist ein Implementierungsdetail, dass sich bei zukünftigen Versionen ändern kann. Auch wenn die Anzahl heute 12 beträgt, sollten Sie nicht davon ausgehen, das dies auch in Zukunft immer so ist.

<a id="HA"></a>
## Auswählen einer Kombination von Partitionen und Replikaten für hohe Verfügbarkeit

Da ein Hochskalieren einfach und relativ schnell durchzuführen ist, wird im Allgemeinen empfohlen, mit einer Partition und einem oder zwei Replikaten zu beginnen und dann bei steigenden Abfragevolumen hochzuskalieren. Bei vielen Bereitstellungen bietet eine Partition ausreichend Speicher und E/A (bei 15 Millionen Dokumenten pro Partition).

Abfrageworkloads basieren jedoch auf Replikaten. Wenn Sie einen höheren Durchsatz oder hohe Verfügbarkeit benötigen, können zusätzliche Replikate erforderlich sein.

Allgemeine Empfehlungen für hohe Verfügbarkeit sind:

- 2 Replikate für hohe Verfügbarkeit von schreibgeschützten Workloads (Abfragen)
- 3 oder mehr Replikate für hohe Verfügbarkeit von Lese-/ Schreibworkloads (Abfragen und Indizierung)

Derzeit steht kein integrierter Mechanismus für die Notfallwiederherstellung bereit. Das Hinzufügen von Partitionen oder Replikaten wäre die falsche Strategie, um die Zielsetzungen für eine Notfallwiederherstellung zu erfüllen. Stattdessen sollten Sie das Hinzufügen von Redundanz auf Dienstebene in Betracht ziehen. Eine eingehendere Besprechung der Methoden finden Sie in [diesem Forumsbeitrag](https://social.msdn.microsoft.com/Forums/ee108a26-00c5-49f6-b1ff-64c66c8b828a/dr-and-high-availability-for-azure-search?forum=azuresearch).

> [AZURE.NOTE] Denken Sie daran, dass Vereinbarungen zum Servicelevel und Skalierbarkeit Features des Standarddiensts sind. Der kostenlose Dienst wird auf fester Ressourcenebene angeboten, wobei Replikate und Partitionen von mehreren Abonnenten gemeinsam genutzt werden. Wenn Sie mit dem kostenlosen Dienst begonnen haben und jetzt ein Upgrade durchführen möchten, müssen Sie einen neuen Azure Search-Dienst auf Standardebene erstellen und dann Indizes und Daten erneut in den neuen Dienst laden. Anweisungen zur Dienstbereitstellung finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md).

## Informationen zu Partitionen und Replikaten

**Partitionen** bieten Speicher und E/A. Ein einzelner Search-Dienst kann maximal 12 Partitionen umfassen. Jede Partition weist einen festen Grenzwert von 15 Millionen Dokumenten oder 25 GB Speicher auf, je nachdem, was zuerst erreicht wird. Wenn Sie Partitionen hinzufügen, können mehr Dokumente im Search-Dienst geladen werden. Ein Dienst mit einer einzelnen Partition, in dem anfänglich bis zu 25 GB Daten gespeichert werden, kann beispielsweise 50 GB speichern, wenn Sie eine zweite Partition zum Dienst hinzufügen.

**Replikate** sind Kopien der Suchmaschine. Ein einzelner Search-Dienst kann maximal 6 Replikate aufweisen. Sie benötigen mindestens 2 Replikate für Leseverfügbarkeit (Abfrage) und mindestens 3 Replikate für Lese-/Schreibverfügbarkeit (Abfrage, Indizierung).

Für jedes Replikat wird eine Kopie jedes Indexes ausgeführt. Beim Hinzufügen von Replikaten werden zusätzliche Kopien des Indexes online gestellt, um höhere Abfrageworkloads zu unterstützen und die Anforderungslast gleichmäßig auf mehrere Replikate zu verteilen. Wenn Sie über mehrere Indizes (z. B. 6) und 3 Replikate verfügen, weist jedes Replikat eine Kopie aller 6 Indizes auf.

Beachten Sie, dass keine festen Schätzungen für die Abfragen pro Sekunde (QPS) gegeben werden, da die Ausführung von Abfragen je nach Komplexität der jeweiligen Abfrage und konkurrierender Workloads stark variieren kann. Durchschnittlich kann ein Replikat etwa 15 QPS bedienen, doch ist der Durchsatz je nach Komplexität der Abfrage (Facettenabfragen sind komplexer) und Netzwerklatenz etwas höher oder niedriger. Darüber hinaus ist zu beachten, dass beim Hinzufügen von Replikaten zwar Skalierung und Leistung erhöht werden, das Endergebnis aber nicht streng linear ist: Das Hinzufügen von 3 Replikaten garantiert keinen dreifachen Durchsatz. Die Abfragewartezeit ist ein Indikator, dass möglicherweise zusätzliche Replikate erforderlich sind. Weitere Informationen zu QPS, einschließlich der Ansätze für die Schätzung der QPS für Ihre Workloads, finden Sie unter [Verwalten des Search-Dienstes](search-manage.md).

<!---HONumber=AcomDC_0302_2016-->