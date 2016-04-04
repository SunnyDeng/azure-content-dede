

## Übersicht

Dieser Artikel beschreibt die verfügbaren Größen und Optionen für die VM-basierte Rechenressourcen, die Sie verwenden können, um Ihre Apps und Workloads auszuführen. Darüber hinaus werden Überlegungen zur Bereitstellung angestellt, die Sie berücksichtigen sollten, wenn Sie eine Verwendung dieser Ressourcen planen. Informationen zu den Preisen der unterschiedlichen Größen finden Sie unter [Virtual Machines – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/).

Informationen zu den allgemeinen Einschränkungen von virtuellen Azure-Geräten finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).

Die Standardgrößen bestehen aus mehreren Serien: A, D, DS, G und GS. Beachten Sie für einige dieser Größen die folgenden Punkte:

*   VMs der D-Serie dienen zum Ausführen von Anwendungen, die eine höhere Rechenleistung und eine höhere temporäre Datenträgerleistung erfordern. VMs der D-Serie bieten schnellere Prozessoren, ein höheres Verhältnis von Speicher zu Kern und ein SSD (Solid State Drive) für den temporären Datenträger. Einzelheiten finden Sie in der Ankündigung im Azure-Blog unter [New D-Series Virtual Machine Sizes](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) (Neue VM-Größen der D-Serie, in englischer Sprache).

*   Die Dv2-Serie, eine Nachfolgerin der ursprünglichen D-Serie, hat eine leistungsfähigere CPU. Die CPU der Dv2-Serie ist ca. 35 % schneller als die CPU der D-Serie. Sie basiert auf der neuesten Generation des 2,4-GHz-Intel Xeon ® E5-2673 v3-Prozessors (Haswell) und kann mit der Intel Turbo Boost Technology 2.0 bis auf 3,2 GHz gebracht werden. Der Dv2-Serie hat die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die D-Serie.

*   Virtuelle Computer der G-Serie bieten den meisten Arbeitsspeicher und werden auf Hosts mit Prozessoren der Intel Xeon E5 V3-Familie ausgeführt.

*   Virtuelle Computer der DS- und der GS-Serie können Premium-Speicher nutzen, der Hochleistungsspeicher mit geringer Latenz für E/A-intensive Workloads bietet. Diese virtuellen Computer nutzen Solid State Drives (SSDs) zum Hosten der Datenträger eines virtuellen Computers und stellen zudem einen lokalen SSD-Datenträgercache bereit. Premium-Speicher ist in bestimmten Regionen verfügbar. Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](../storage/storage-premium-storage.md).


*   Die VMs der A-Reihe können auf vielen verschiedenen Hardwaretypen und Prozessoren bereitgestellt werden. Die Größe ist basierend auf der Hardware gedrosselt, um eine konsistente Prozessorleistung für die ausgeführte Instanz zu ermöglichen – unabhängig von der Hardware, die für die Bereitstellung gewählt wird. Fragen Sie die virtuelle Hardware über die virtuelle Maschine ab, um die physische Hardware zu ermitteln, auf der diese Größe bereitgestellt wird.

*   Die Größe „A0“ ist auf der physischen Hardware „überzeichnet“. Nur für diese spezielle Größe kann es dazu kommen, dass sich andere Kundenbereitstellungen negativ auf die Leistung Ihrer aktiven Workload auswirken. Die relative Leistung ist unten als erwartete Baseline beschrieben und unterliegt einer ungefähren Variabilität von 15 Prozent.


Die Größe des virtuellen Computers wirkt sich auf den Preis aus. Die Größe beeinflusst auch die Verarbeitung, den Arbeitsspeicher und die Speicherkapazität des virtuellen Computers. Speicherkosten werden separat basierend auf bereits verwendete Seiten im Speicherkonto berechnet. Weitere Informationen finden Sie unter [Virtuelle Computer – Preisdetails](https://azure.microsoft.com/pricing/details/virtual-machines/) und [Preisdetails zu Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Ausführlichere Informationen zu virtuellen Computern finden Sie unter [Informationen zu Datenträgern und VHDs für virtuelle Computer](virtual-machines-linux-about-disks-vhds.md).


Die folgenden Überlegungen können Ihnen bei der Entscheidung über die Größe behilflich sein:


* Die Größen A8 bis A11 werden auch als *rechenintensive Instanzen* bezeichnet. Die Hardware, auf der diese Größen ausgeführt werden, wurde für rechenintensive Anwendungen mit hoher Netzwerkauslastung konzipiert und optimiert. Hierzu zählen beispielsweise HPC-Clusteranwendungen (High Performance Computing), Modellierung und Simulationen. Ausführliche Informationen und Überlegungen zum Verwenden dieser Größen finden Sie unter [Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen](virtual-machines-windows-a8-a9-a10-a11-specs.md).


*	Die Serien Dv2, D, G sowie deren DS/GS-Entsprechungen eignen sich ideal für Clientanwendungen, die schnellere CPUs oder bessere lokale Datenträgerleistung erfordern oder einen höheren Speicherbedarf haben. Sie bieten eine leistungsfähige Kombination für viele Anwendungen für den Unternehmenseinsatz.

*   Einige der physischen Hosts in Azure-Rechenzentren unterstützen möglicherweise keine der größeren VM-Größen, z. B. A5 bis A11. Daher wird möglicherweise die Fehlermeldung **Fehler beim Konfigurieren des virtuellen Computers<machine name>** oder **Fehler beim Erstellen des virtuellen Computers<machine name>** beim Ändern der Größe eines vorhandenen virtuellen Computers, beim Erstellen eines neuen virtuellen Computers in einem virtuellen Netzwerk, das vor dem 16. April 2013 erstellt wurde, oder beim Hinzufügen eines neuen virtuellen Computers zu einem vorhandenen Clouddienst angezeigt. Problemumgehungen für die einzelnen Bereitstellungsszenarios finden Sie unter [Error: “Failed to configure virtual machine”](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) (in englischer Sprache) im Supportforum.


## Überlegungen zur Leistung

Wir haben das Konzept der Azure-Berechnungseinheit ACU (Azure Compute Unit) erstellt, um eine Möglichkeit zum Vergleichen der Rechenleistung (CPU) zwischen den Azure-SKUs zu ermöglichen. Auf diese Weise können Sie leicht feststellen, welche SKU Ihren Leistungsanforderungen am ehesten entspricht. Zurzeit ist der ACU-Wert auf einem kleinen virtuellen Computer (Standard\_A1) auf den Standardwert 100 festgelegt, und an den übrigen SKUs kann ungefähr abgelesen werden, wie viel schneller die jeweilige SKU einen Standard-Benchmarktest ausführen kann.

>[AZURE.IMPORTANT] Die ACU ist nur ein Richtwert. Die Ergebnisse für Ihre Workload können abweichen.

<br>

|SKU-Familie |ACU/Kern |
|---|---|
|[Standard\_A0](#standard-tier-a-series) |50 |
|[Standard\_A1-4](#standard-tier-a-series) |100 |
|[Standard\_A5-7](#standard-tier-a-series) |100 |
|[A8-A11](#standard-tier-a-series) |225 *| |[D1–14](#standard-tier-d-series) |160 | |[D1–14v2](#standard-tier-dv2-series) |210–250*| |[DS1–14](#standard-tier-ds-series) |160 | |[G1–5](#standard-tier-g-series) |180–240*| |[GS1–5](#standard-tier-gs-series) |180–240*|


Mit * gekennzeichnete ACUs verwenden die Intel® Turbo-Technologie, um die CPU-Frequenz zu erhöhen eine Leistungssteigerung zu erzielen. Das Maß der Leistungssteigerung variiert basierend auf der Größe und Workload des virtuellen Computers sowie auf anderen Workloads, die auf dem gleichen Host ausgeführt werden.



## Größentabellen

In den folgenden Tabellen sind die Größe und die von den einzelnen Größen bereitgestellte Kapazität aufgeführt.

* Die Speicherkapazität wird unter Verwendung von 1024^3 Bytes als Maßeinheit für GB dargestellt. Dies wird manchmal als Gibibyte oder Binärdefinition (bzw. Definition zur Basis 2) bezeichnet. Denken Sie beim Vergleichen von Größen, die andere Basissysteme verwenden, jedoch daran, dass eine als Binärwert angegebene Größe möglicherweise kleiner als ein Dezimalwert zur Basis 10 erscheint (z. B. 1 GB), das Binärsystem jedoch mehr Kapazität als das Dezimalsystem bietet, da 1024^3 größer als 1000^3 ist.

* Bei der maximalen Netzwerkbandbreite handelt es sich um die maximale aggregierte Bandbreite, die pro VM-Typ zugewiesen wurde. Die maximale Bandbreite dient als Orientierungshilfe bei der Wahl des richtigen VM-Typs, um sicherzustellen, dass ausreichend Netzwerkkapazität verfügbar ist. Bei einem Wechsel zu „niedrig“, „mittel“, „hoch“ oder „sehr hoch“ ändert sich der Durchsatz entsprechend. Die tatsächliche Netzwerkleistung hängt von zahlreichen Faktoren ab. Hierzu zählen beispielsweise die Netzwerk- und Anwendungslast und die Netzwerkeinstellungen der Anwendung.


## Standard-Ebene: A-Serie

|Größe |CPU-Kerne|Arbeitsspeicher|Netzwerkkarten (max.)|Maximale Datenträgergröße|Max. Datenträger (jeweils 1023 GB)|Max. IOPS (500 pro Datenträger)| Maximale Netzwerkbandbreite |
|---|---|---|---|---|---|---|---|
|Standard\_A0 |1|768 MB|1| Temporär = 20 GB |1|1 x 500| niedrig |
|Standard\_A1 |1|1,75 GB|1|Temporär = 70 GB |2|2 x 500| mittel |
|Standard\_A2 |2|3,5 GB|1|Temporär = 135 GB |4|4 x 500| mittel |
|Standard\_A3 |4|7 GB|2|Temporär = 285 GB |8|8 x 500| hoch |
|Standard\_A4 |8|14 GB|4|Temporär = 605 GB |16|16 x 500| hoch |
|Standard\_A5 |2|14 GB|1|Temporär = 135 GB |4|4 x 500| mittel |
|Standard\_A6 |4|28 GB|2|Temporär = 285 GB |8|8 x 500| hoch |
|Standard\_A7 |8|56 GB|4|Temporär = 605 GB |16|16 x 500| hoch |


## Tarif „Standard“: A-Serie – rechenintensive Instanzen

Hinweis: Informationen und Überlegungen zum Verwenden dieser Größen finden Sie unter [Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen](virtual-machines-windows-a8-a9-a10-a11-specs.md).

|Größe |CPU-Kerne|Arbeitsspeicher|Netzwerkkarten (max.)|Maximale Datenträgergröße|Max. Datenträger (jeweils 1023 GB)|Max. IOPS (500 pro Datenträger)| Maximale Netzwerkbandbreite |
|---|---|---|---|---|---|---|---|
|Standard\_A8|8|56 GB|2| Temporär = 382 GB |16|16 x 500| hoch |
|Standard\_A9|16|112 GB|4| Temporär = 382 GB |16|16 x 500| sehr hoch |
|Standard\_A10|8|56 GB|2| Temporär = 382 GB |16|16 x 500| hoch |
|Standard\_A11|16|112 GB|4| Temporär = 382 GB |16|16 x 500| sehr hoch |

## Standard-Ebene: D-Serie

|Größe |CPU-Kerne|Arbeitsspeicher|Netzwerkkarten (max.)|Maximale Datenträgergröße|Max. Datenträger (jeweils 1023 GB)|Max. IOPS (500 pro Datenträger)| Maximale Netzwerkbandbreite |
|---|---|---|---|---|---|---|---|
|Standard\_D1 |1|3,5 GB|1|Temporär (SSD) =50 GB |2|2 x 500| mittel |
|Standard\_D2 |2|7 GB|2|Temporär (SSD) = 100 GB |4|4 x 500| hoch |
|Standard\_D3 |4|14 GB|4|Temporär (SSD) = 200 GB |8|8 x 500| hoch |
|Standard\_D4 |8|28 GB|8|Temporär (SSD) = 400 GB |16|16 x 500| hoch |
|Standard\_D11 |2|14 GB|2|Temporär (SSD) = 100 GB |4|4 x 500| hoch |
|Standard\_D12 |4|28 GB|4|Temporär (SSD) = 200 GB |8|8 x 500| hoch |
|Standard\_D13 |8|56 GB|8|Temporär (SSD) = 400 GB |16|16 x 500| hoch |
|Standard\_D14 |16|112 GB|8|Temporär (SSD) = 800 GB |32|32 x 500| sehr hoch |

## Standard-Ebene: Dv2-Serie

|Größe |CPU-Kerne|Arbeitsspeicher|Netzwerkkarten (max.)|Maximale Datenträgergröße|Max. Datenträger (jeweils 1023 GB)|Max. IOPS (500 pro Datenträger)| Maximale Netzwerkbandbreite |
|---|---|---|---|---|---|---|---|
|Standard\_D1\_v2 |1|3,5 GB|1|Temporär (SSD) =50 GB |2|2 x 500| mittel |
|Standard\_D2\_v2 |2|7 GB|2|Temporär (SSD) = 100 GB |4|4 x 500| hoch |
|Standard\_D3\_v2 |4|14 GB|4|Temporär (SSD) = 200 GB |8|8 x 500| hoch |
|Standard\_D4\_v2 |8|28 GB|8|Temporär (SSD) = 400 GB |16|16 x 500| hoch |
|Standard\_D5\_v2 |16|56 GB|8|Temporär (SSD) = 800 GB |32|32 x 500| sehr hoch |
|Standard\_D11\_v2 |2|14 GB|2|Temporär (SSD) = 100 GB |4|4 x 500| hoch |
|Standard\_D12\_v2 |4|28 GB|4|Temporär (SSD) = 200 GB |8|8 x 500| hoch |
|Standard\_D13\_v2 |8|56 GB|8|Temporär (SSD) = 400 GB |16|16 x 500| hoch |
|Standard\_D14\_v2 |16|112 GB|8|Temporär (SSD) = 800 GB |32|32 x 500| sehr hoch |

## Standard-Ebene: DS-Serie*

|Größe |CPU-Kerne|Arbeitsspeicher|Netzwerkkarten (max.)|Maximale Datenträgergröße|Max. Datenträger (jeweils 1023 GB)|Cachegröße (GB)|Max. Datenträger-IOPS &amp; Bandbreite| Maximale Netzwerkbandbreite |
|---|---|---|---|---|---|---|---|---|
|Standard\_DS1 |1|3,5|1|Lokales SSD-Laufwerk = 7 GB |2|43| 3\.200, 32 MB pro Sekunde | mittel |
|Standard\_DS2 |2|7|2|Lokales SSD-Laufwerk = 14 GB |4|86| 6\.400, 64 MB pro Sekunde | hoch |
|Standard\_DS3 |4|14|4|Lokales SSD-Laufwerk = 28 GB |8|172| 12\.800, 128 MB pro Sekunde | hoch |
|Standard\_DS4 |8|28|8|Lokales SSD-Laufwerk = 56 GB |16|344| 25\.600, 256 MB pro Sekunde | hoch |
|Standard\_DS11 |2|14|2|Lokales SSD-Laufwerk = 28 GB |4|72| 6\.400, 64 MB pro Sekunde | hoch |
|Standard\_DS12 |4|28|4|Lokales SSD-Laufwerk = 56 GB |8|144| 12\.800, 128 MB pro Sekunde | hoch |
|Standard\_DS13 |8|56|8|Lokales SSD-Laufwerk = 112 GB |16|288| 25\.600, 256 MB pro Sekunde | hoch |
|Standard\_DS14 |16|112|8|Lokales SSD-Laufwerk = 224 GB |32|576| 50\.000, 512 MB pro Sekunde | sehr hoch |

**Die bei einer DS-Serie möglichen Höchstwerte für E/A-Vorgänge pro Sekunde (IOPS) und Durchsatz (Bandbreite) werden durch die Größe des Datenträgers beeinflusst. Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](../storage/storage-premium-storage.md).

## Standard-Ebene: G-Serie

|Größe |CPU-Kerne|Arbeitsspeicher|Netzwerkkarten (max.)|Maximale Datenträgergröße|Max. Datenträger (jeweils 1023 GB)|Max. IOPS (500 pro Datenträger)| Maximale Netzwerkbandbreite |
|---|---|---|---|---|---|---|---|
|Standard\_G1 |2|28 GB|1|Lokales SSD-Laufwerk = 384 GB |4|4 x 500| hoch |
|Standard\_G2 |4|56 GB|2|Lokales SSD-Laufwerk = 768 GB |8|8 x 500| hoch |
|Standard\_G3 |8|112 GB|4|Lokales SSD-Laufwerk = 1.536 GB |16|16 x 500| sehr hoch | 
|Standard\_G4 |16|224 GB|8|Lokales SSD-Laufwerk = 3.072 GB |32|32 x 500| äußerst hoch |
|Standard\_G5 |32|448 GB|8|Lokales SSD-Laufwerk = 6.144 GB |64| 64 x 500 | äußerst hoch |

## Standard-Ebene: GS-Serie

|Größe |CPU-Kerne|Arbeitsspeicher|Netzwerkkarten (max.)|Maximale Datenträgergröße|Max. Datenträger (jeweils 1023 GB)|Cachegröße (GB)|Max. Datenträger-IOPS &amp; Bandbreite| Maximale Netzwerkbandbreite |
|---|---|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|1|Lokales SSD-Laufwerk = 56 GB |4|264| 5\.000, 125 MB pro Sekunde | hoch |
|Standard\_GS2|4|56|2|Lokales SSD-Laufwerk = 112 GB |8|528| 10\.000, 250 MB pro Sekunde | hoch | 
|Standard\_GS3|8|112|4|Lokales SSD-Laufwerk = 224 GB |16|1056| 20\.000, 500 MB pro Sekunde | sehr hoch |
|Standard\_GS4|16|224|8|Lokales SSD-Laufwerk = 448 GB |32|2112| 40\.000, 1.000 MB pro Sekunde | äußerst hoch |
|Standard\_GS5|32|448|8|Lokales SSD-Laufwerk = 896 GB |64|4224| 80\.000, 2.000 MB pro Sekunde | äußerst hoch |


## Hinweise: Standard\_A0–A4 mit Befehlszeilenschnittstelle (CLI) und PowerShell 

Im klassischen Bereitstellungsmodell unterscheiden sich einige VM-Größennamen in CLI und PowerShell:

* Standard\_A0 ist „Sehr klein“ 
* Standard\_A1 ist „Klein“
* Standard\_A2 ist „Mittel“
* Standard\_A3 ist „Groß“
* Standard\_A4 ist „Extragroß“

## Nächste Schritte

- Informationen zu Azure-Abonnements und Diensteinschränkungen, Kontingenten und Einschränkungen finden Sie [hier](../azure-subscription-service-limits.md).
- Weitere Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen (etwa für HPC-Workloads) finden Sie [hier](virtual-machines-windows-a8-a9-a10-a11-specs.md).

<!---HONumber=AcomDC_0323_2016-->