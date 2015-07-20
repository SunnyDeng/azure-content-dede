<properties
	pageTitle="Verbessern der Geschäftskontinuität mit Azure-Regionspaaren"
	description="Verwenden Sie Regionspaare, um Anwendungen bei Datencenterausfällen stabil zu halten."
	services="multiple"
	documentationCenter=""
	authors="rboucher"
	manager="jwhit"
	editor="tysonn"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2015"
    ms.author="robb"/>

# Verbessern der Verfügbarkeit mithilfe von Azure-Regionspaaren

## Azure-Regionspaare – Erläuterung

Azure betreibt Datencenter in mehreren Gebieten auf der ganzen Erde. Bei einem Azure-Gebiet handelt es sich um einen definierten Bereich der Erde, der mindestens eine Azure-Region enthält. Eine Azure-Region ist ein Bereich in einem Gebiet mit mindestens einem Datencenter.

Jede Azure-Region bildet ein Paar mit einer anderen Region innerhalb des gleichen Gebiets (mit Ausnahme von "Brasilien Süd", das ein Paar mit einer Region außerhalb seines Gebiets bildet). Diese Paare werden als Regionspaare bezeichnet.


![Azure-Gebiet](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Abbildung 1 – Diagramm von Azure-Regionspaaren



| Gebiet | Regionspaare | |
| :-------------| :-------------   | :-------------   |
| Nordamerika | USA (Mitte/Norden) | USA (Mitte/Süden) |
| Nordamerika | USA (Ost) | USA (West) |
| Nordamerika | USA (Ost 2) | USA, Mitte |
| Europa | Nordeuropa | Westeuropa |
| Asien | Südostasien | Ostasien |
| China | Ostchina | Nordchina |
| Japan | Japan Ost | Japan (Westen) |
| Brasilien | Brasilien, Süden (1) | USA (Mitte/Süden) |
| Australien | Australien (Ost) | Australien (Südost)|
| US Government | US Government, Iowa | US Government, Virginia |

Tabelle 1: Übersicht über Azure-Regionspaare

> (1) Brasilien, Süden ist besonders, da dieses Gebiet ein Paar mit einer Region außerhalb des eigenen Gebiets bildet. Beachten Sie, dass USA, Mitte/Süden die sekundäre Region von Brasilien, Süden ist, wobei jedoch USA, Mitte/Süden nicht die sekundäre Region von Brasilien, Süden ist.

Wir empfehlen das Replizieren von Workloads zwischen Regionalpaaren, um von Richtlinien für Isolierung und Verfügbarkeit von Azure zu profitieren. Beispielsweise werden geplante Azure-Systemupdates in Regionspaaren sequenziell (nicht gleichzeitig) bereitgestellt. Das heißt, dass selbst im seltenen Fall eines fehlerhaften Updates beide Regionen nicht gleichzeitig betroffen sind. Darüber hinaus wird im unwahrscheinlichen Fall eines umfassenden Ausfalls die Wiederherstellung mindestens einer Region aus jedem Paar priorisiert.

## Beispiel eines Regionspaars
Die nachstehende Abbildung 2 zeigt ein hypothetisches Regionspaar. Die grünen Zahlen markieren die regionsübergreifenden Aktivitäten von drei Azure-Diensten (Azure Compute, Storage und Database) und ihre Konfiguration für eine regionsübergreifende Replikation. Die eindeutigen Vorteile einer Bereitstellung in Regionspaaren werden von den orangefarbenen Zahlen hervorgehoben.


![Übersicht über die Vorteile von Regionspaaren](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Abbildung 2 – Hypothetisches Azure-Regionspaar

![1Grün](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute** – Sie müssen zusätzliche Serverressourcen im Voraus bereitstellen, um sicherzustellen, dass Ressourcen während eines Notfalls in einer anderen Region zur Verfügung stehen. Es wird empfohlen, diese Ressourcen für die Notfallwiederherstellung in einem Regionspaar bereitzustellen.

![2Grün](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** – Georedundanter Speicher (GRS) wird beim Erstellen eines Speicherkontos standardmäßig konfiguriert. Mithilfe von GRS werden Ihre Daten dreimal in der primären Region und dreimal im Regionspaar repliziert. GRS lässt nur die Replikation innerhalb des Regionspaars zu und kann nicht anders konfiguriert werden. Weitere Informationen finden Sie unter [Redundanzoptionen für Azure Storage](../storage/storage-redundancy.md).


![3Grün](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL-Datenbanken** – Mithilfe der Azure SQL-Georeplikation können Sie eine asynchrone Replikation von Transaktionen auf andere Microsoft Azure-Datenbankserver konfigurieren. Die standardmäßige Georeplikation lässt nur die asynchrone Replikation in das Regionspaar zu. Bei Wahl der Option "Premium" für die Georeplikation können Sie die Replikation in jede Region der Welt konfigurieren. Allerdings wird empfohlen, dass Sie diese Ressourcen für die Notfallwiederherstellung in einem Regionspaar bereitstellen. Weitere Informationen finden Sie unter [Georeplikation in Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn783447.aspx).

![4Grün](./media/best-practices-availability-paired-regions/4Green.png) **Unabhängige Verwaltungsdienste** – Der Azure-Ressourcen-Manager (ARM) bietet grundsätzlich eine regionsübergreifende logische Isolierung von Dienstverwaltungskomponenten, für die keine Konfiguration erforderlich ist. Dies bedeutet, dass sich logische Fehler in einer Region weniger wahrscheinlich auf eine andere auswirken.

## Vorteile eines Regionspaars

Wie in Abbildung 2 dargestellt.

![5Orange](./media/best-practices-availability-paired-regions/5Orange.png) **Physische Isolierung** – Sofern möglich, sollte zwischen Azure-Datencentern in einem Regionspaar eine Entfernung von mindestens 480 km bestehen, was allerdings nicht in allen Gebieten zweckmäßig oder möglich ist. Durch die Trennung physischer Datencenter wird die Wahrscheinlichkeit einer gleichzeitigen Beeinträchtigung beider Regionen durch Naturkatastrophen, politische Unruhen, Stromausfälle oder physische Netzwerkausfälle verringert. Die Isolierung unterliegt den Einschränkungen des jeweiligen Gebiets (Größe, Verfügbarkeit der Energieversorgungs-/Netzwerkinfrastruktur, Vorschriften usw.).

![6Orange](./media/best-practices-availability-paired-regions/6Orange.png) **Konsistenter Anwendungsstatus** – Wenn Sie einen Dienst nutzen, der nur die Replikation in ein Regionspaar zulässt (z. B. georedundanten Speicher), sollten Sie sicherstellen, dass alle anderen Dienste für die Replikation auch das Regionspaar als Ziel haben. Dadurch wird ein konsistenter Anwendungsstatus während des Failovers sichergestellt.

![7Orange](./media/best-practices-availability-paired-regions/7Orange.png) **Reihenfolge der Regionswiederherstellung** – Im Falle eines umfassenden Ausfalls hat bei jedem Paar die Wiederherstellung einer der Regionen Priorität. Für Anwendungen, die in Regionspaaren bereitgestellt sind, wird die priorisierte Wiederherstellung einer der Regionen garantiert. Wenn eine Anwendung in Regionen bereitgestellt ist, die kein Paar bilden, kann sich die Wiederherstellung verzögern. Im schlimmsten Fall werden die gewählten Regionen ggf. zuletzt wiederhergestellt.

![8Orange](./media/best-practices-availability-paired-regions/8Orange.png) **Sequenzielle Updates** – Geplante Azure-Systemupdates werden zur Minimierung von Ausfallzeit, der Auswirkungen von Fehlern und logischen Ausfällen im seltenen Fall eines fehlerhaften Updates sequenziell (nicht gleichzeitig) eingespielt.


![9Orange](./media/best-practices-availability-paired-regions/9Orange.png) **Speicherort von Daten** – Eine Region befindet sich innerhalb desselben Gebiets wie ihr Paar (mit Ausnahme von Brasilien, Süden), um steuerliche und rechtliche Anforderungen an den Speicherort von Daten zu erfüllen.

<!---HONumber=July15_HO2-->