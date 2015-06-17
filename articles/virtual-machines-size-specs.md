<properties 
 pageTitle="Größen virtueller Computer" 
 description="Listet die verschiedenen Größen für virtuelle Computer und die zugehörigen Kapazitäten auf." 
 services="virtual-machines" 
 documentationCenter="" 
 authors="KBDAzure" 
 manager="timlt" 
 editor=""/>
<tags 
ms.service="virtual-machines" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="vm-multiple" 
 ms.workload="infrastructure-services"
 ms.date="05/26/2015" 
 ms.author="kathydav"/>

# Größen für Virtual Machines

## Übersicht

Dieser Artikel beschreibt die verfügbaren Größen und Optionen für die VM-basierte Rechenressourcen, die Sie verwenden können, um Ihre Apps und Arbeitsauslastungen auszuführen. Darüber hinaus werden Überlegungen zur Bereitstellung angestellt, die Sie berücksichtigen sollten, wenn Sie eine Verwendung dieser Ressourcen planen.

Azure Virtual Machines und Azure Cloud Services sind zwei von mehreren unterschiedlichen Typen von Compute-Ressourcen, die über Azure zur Verfügung stehen. Erläuterungen hierzu finden Sie unter [Compute Hosting Options Provided by Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926) (Compute-Hostingoptionen in Azure).

>[AZURE.NOTE]Zugehörige Einschränkungen von Azure finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](azure-subscription-service-limits.md).

## Überlegungen zur Planung

Virtuelle Computer sind in zwei Ebenen verfügbar: Basic und Standard. Beide Typen stellen verschiedene Größen zur Auswahl, aber einige Funktionen, die in der Standard-Ebene zur Verfügung stehen, wie z. B. Lastenausgleich und automatische Skalierung, werden in der Basic-Ebene nicht bereitgestellt. Die Standard-Ebene umfasst die Größen der A-Serie, D-Serie, DS-Serie und G-Serie.

*   VMs der D-Serie dienen zum Ausführen von Anwendungen, die eine höhere Rechenleistung und eine höhere temporäre Datenträgerleistung erfordern. VMs der D-Serie bieten schnellere Prozessoren, ein höheres Verhältnis von Speicher zu Kern und ein SSD (Solid State Drive) für den temporären Datenträger. Einzelheiten finden Sie in der Ankündigung im Azure-Blog unter [New D-Series Virtual Machine Sizes](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) (Neue VM-Größen der D-Serie, in englischer Sprache).  

*   VMs der DS-Serie können Premium-Speicher nutzen, der Hochleistungsspeicher mit geringer Latenz für e/a-intensive Arbeitsauslastungen bietet. Hier werden Solid-State-Laufwerke (SSDs) zum Hosten der Datenträger eines virtuellen Computers verwendet und ein lokaler SSD-Datenträgercache bereitgestellt. Premium-Speicher befindet sich in der Vorschau und ist in bestimmten Regionen verfügbar. Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](storage/storage-premium-storage-preview-portal.md).

*   VMs der G-Serie bieten die größte Größe und die beste Leistung und werden auf Hosts mit Prozessoren der Intel Xeon E5 V3-Familie ausgeführt.

Die Größe des virtuellen Computers wirkt sich auf den Preis aus. Die Größe beeinflusst auch die Verarbeitung, den Arbeitsspeicher und die Speicherkapazität des virtuellen Computers. Speicherkosten werden separat basierend auf bereits verwendete Seiten im Speicherkonto berechnet. Weitere Informationen finden Sie unter [Virtuelle Computer – Preisdetails](http://go.microsoft.com/fwlink/p/?LinkId=398570) und [Preisdetails zu Azure Storage](http://azure.microsoft.com/pricing/details/storage/). Weitere Informationen zu den in Azure-VMs verwendeten Datenträgern und zum Speicher finden Sie unter [Informationen zu Datenträgern und Images in Azure](https://msdn.microsoft.com/library/jj672979).

Die folgenden Überlegungen können Ihnen bei der Entscheidung über die Größe behilflich sein:

*   Die Größe A0\Basic_A0 ist nur bei Verwendung von Azure SDK, Version 1.3 oder höher, verfügbar.  

*   A1\Basic_A1 ist die kleinste empfohlene Größe für Produktionsarbeitsauslastungen.

*   Wählen Sie einen virtuellen Computer mit 4 oder 8 CPU-Kernen, wenn Sie SQL Server Enterprise Edition verwenden.

*   Einige der physischen Hosts in Azure-Rechenzentren unterstützen möglicherweise keine der größeren VM-Größen, z. B. A5 bis A11. Daher wird möglicherweise die Fehlermeldung **Fehler beim Konfigurieren des virtuellen Computers<machine name>** oder **Fehler beim Erstellen des virtuellen Computers<machine name>** beim Ändern der Größe eines vorhandenen virtuellen Computers, beim Erstellen eines neuen virtuellen Computers in einem virtuellen Netzwerk, das vor dem 16. April 2013 erstellt wurde, oder beim Hinzufügen eines neuen virtuellen Computers zu einem vorhandenen Cloud-Dienst angezeigt. Problemumgehungen für die einzelnen Bereitstellungsszenarien finden Sie im Thema [Fehler: "Fehler beim Konfigurieren des virtuellen Computers"](http://social.msdn.microsoft.com/Forums/WAVirtualMachinesforWindows/thread/9693f56c-fcd3-4d42-850e-5e3b56c7d6be) im Supportforum.

*   Die VM-Größen A8/A10 und A9/A11 haben die gleiche Kapazität. Die VM-Instanzen A8 und A9 umfassen einen zusätzlichen Netzwerkadapter, der mit einem RDMA (Remote Direct Memory Access)-Netzwerk für die schnelle Kommunikation zwischen virtuellen Computern verbunden ist. Die A8- und A9-Instanzen wurden für High Performance Computing-Anwendungen entwickelt, die während der Ausführung eine konstante Kommunikation mit niedriger Latenz zwischen den Knoten erfordern, z. B. MPI (Message Passing Interface)-Anwendungen. Die VM-Instanzen A10 und A11 enthalten keinen zusätzlichen Netzwerkadapter. Die A10- und A11-Instanzen sind für High Performance Computing-Anwendungen vorgesehen, die keine konstante Kommunikation mit niedriger Latenz zwischen den Knoten erfordern, auch bekannt als parametrische oder hochgradig parallele Anwendungen.

In den folgenden Tabellen sind die Größe und die Kapazität, die von den einzelnen Größen bereitgestellt wird, aufgeführt.

>[AZURE.NOTE]Die Speicherkapazität wird unter Verwendung von 1024^3 Bytes als Maßeinheit für GB dargestellt. Dies wird manchmal als Gibibyte oder Binärdefinition (bzw. Definition zur Basis 2) bezeichnet. Denken Sie beim Vergleichen von Größen, die andere Basissysteme verwenden, jedoch daran, dass eine als Binärwert angegebene Größe möglicherweise kleiner als ein Dezimalwert zur Basis 10 erscheint (z. B. 1 GB), das Binärsystem jedoch mehr Kapazität als das Dezimalsystem bietet, da 1024^3 größer als 1000^3 ist.

## Basic-Ebene

|Größe – Verwaltungsportal\Cmdlets und APIs|CPU-Kerne|Arbeitsspeicher|Max. Datenträgergrößen – virtueller Computer|Max. Datenträger (jeweils 1023 GB)|Max. IOPS (300 pro Datenträger)|
|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|<p>BS = 1023 GB</p><p>Temporär = 20 GB</p>|1|1 x 300|
|A1\Basic_A1|1|1,75 GB|<p>BS = 1023 GB</p><p>Temporär = 40 GB</p>|2|2 x 300|
|A2\Basic_A2|2|3,5 GB|<p>BS = 1023 GB</p><p>Temporär = 60 GB</p>|4|4 x 300|
|A3\Basic_A3|4|7 GB|<p>BS = 1023 GB</p><p>Temporär = 120 GB</p>|8|4 x 300|
|A4\Basic_A4|8|14 GB|<p>BS = 1023 GB</p><p>Temporär = 240 GB</p>|16|16 x 300|

## Standard-Ebene
### A- und D-Serie

|Größe – Verwaltungsportal\Cmdlets und APIs|CPU-Kerne|Arbeitsspeicher|Max. Datenträgergrößen – virtueller Computer|Max. Datenträger (jeweils 1023 GB)|Max. IOPS (500 pro Datenträger)|
|---|---|---|---|---|---|
|A0\ extra small (XS, sehr klein)|1|768 MB|<p>BS = 1023 GB</p><p>Temporär = 20 GB</p>|1|1 x 500|
|A1\small (S, klein)|1|1,75 GB|<p>BS = 1023 GB</p><p>Temporär = 70 GB</p>|2|2 x 500|
|A2\medium (M, mittel)|2|3,5 GB|<p>BS = 1023 GB</p><p>Temporär = 135 GB</p>|4|4 x 500|
|A3\large (L, groß)|4|7 GB|<p>BS = 1023 GB</p><p>Temporär = 285 GB</p>|8|8 x 500|
|A4\extra large (XL, sehr groß)|8|14 GB|<p>BS = 1023 GB</p><p>Temporär = 605 GB</p>|16|16 x 500|
|A5 (identisch)|2|14 GB|<p>BS = 1023 GB</p><p>Temporär = 135 GB</p>|4|4 x 500|
|A6 (identisch)|4|28 GB|<p>BS = 1023 GB</p><p>Temporär = 285 GB</p>|8|8 x 500|
|A7 (identisch)|8|56 GB|<p>BS = 1023 GB</p><p>Temporär = 605 GB</p>|16|16 x 500|
|A8 (identisch)|8|56 GB|<p><p>BS = 1023 GB</p><p>Temporär = 382 GB</p><blockquote><p>[AZURE.NOTE]Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</p></blockquote>|16|16 x 500|
|A9 (identisch)|16|112 GB|<p><p>BS = 1023 GB</p><p>Temporär = 382 GB</p><blockquote><p>[AZURE.NOTE]Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</p></blockquote>|16|16 x 500|
|A10 (identisch)|8|56 GB|<p><p>BS = 1023 GB</p><p>Temporär = 382 GB</p><blockquote><p>[AZURE.NOTE]Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</p></blockquote>|16|16 x 500|
|A11 (identisch)|16|112 GB|<p><p>BS = 1023 GB</p><p>Temporär = 382 GB</p><blockquote><p>[AZURE.NOTE]Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</p></blockquote>|16|16 x 500|
|Standard_D1 (identisch)|1|3,5 GB|<p>BS = 1023 GB</p><p>Temporär (SSD) = 50 GB</p>|2|2 x 500|
|Standard_D2 (identisch)|2|7 GB|<p>BS = 1023 GB</p><p>Temporär (SSD) = 100 GB</p>|4|4 x 500|
|Standard_D3 (identisch)|4|14 GB|<p>BS = 1023 GB</p><p>Temporär (SSD) = 200 GB</p>|8|8 x 500|
|Standard_D4 (identisch)|8|28 GB|<p>BS = 1023 GB</p><p>Temporär (SSD) = 400 GB</p>|16|16 x 500|
|Standard_D11 (identisch)|2|14 GB|<p>BS = 1023 GB</p><p>Temporär (SSD) = 100 GB</p>|4|4 x 500|
|Standard_D12 (identisch)|4|28 GB|<p>BS = 1023 GB</p><p>Temporär (SSD) = 200 GB</p>|8|8 x 500|
|Standard_D13 (identisch)|8|56 GB|<p>BS = 1023 GB</p><p>Temporär (SSD) = 400 GB</p>|16|16 x 500|
|Standard_D14 (identisch)|16|112 GB|<p>BS = 1023 GB</p><p>Temporär (SSD) = 800 GB</p>|32|32 x 500|


### Standard-Ebene – DS-Serie *

|Größe – Verwaltungsportal\Cmdlets und APIs|CPU-Kerne|Arbeitsspeicher|Max. Datenträgergrößen – virtueller Computer|Max. Datenträger (jeweils 1023 GB)|Cachegröße (GB)|Max. Datenträger-IOPS &amp; Bandbreite|
|---|---|---|---|---|---|---|
|Standard_DS1 (identisch)|1|3,5|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 7 GB</p>|2|43|<p>3.200</p><p>32 MB pro Sekunde</p>|
|Standard_DS2 (identisch)|2|7|<p>OS = 1023 GB</p><p>Lokales SSD-Laufwerk = 14 GB</p>|4|86|<p>6.400</p><p>64 MB pro Sekunde</p>|
|Standard_DS3 (identisch)|4|14|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 28 GB</p>|8|172|<p>12.800</p><p>128 MB pro Sekunde</p>|
|Standard_DS4 (identisch)|8|28|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 56 GB</p>|16|344|<p>25.600</p><p>256 MB pro Sekunde</p>|
|Standard_DS11 (identisch)|2|14|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 28 GB</p>|4|72|<p>6.400</p><p>64 MB pro Sekunde</p>|
|Standard_DS12 (identisch)|4|28|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 56 GB</p>|8|144|<p>12.800</p><p>128 MB pro Sekunde</p>|
|Standard_DS13 (identisch)|8|56|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 112 GB</p>|16|288|<p>25.600</p><p>256 MB pro Sekunde</p>|
|Standard_DS14 (identisch)|16|112|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 224 GB</p>|32|576|<p>50.000</p><p>512 MB pro Sekunde</p>|

*Die bei einer DS-Serie möglichen Höchstwerte für E/A-Vorgänge pro Sekunde (IOPS) und Durchsatz (Bandbreite) werden durch die Größe des Datenträgers beeinflusst. Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](storage/storage-premium-storage-preview-portal.md).

### Standard-Ebene – G-Serie

|Größe – Verwaltungsportal\Cmdlets und APIs|CPU-Kerne|Arbeitsspeicher|Max. Datenträgergrößen – virtueller Computer|Max. Datenträger (jeweils 1023 GB)|Max. IOPS (500 pro Datenträger)|
|---|---|---|---|---|---|
|Standard_G1 (identisch)|2|28 GB|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 384 GB</p>|4|4 x 500|
|Standard_G2 (identisch)|4|56 GB|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 768 GB</p>|8|8 x 500|
|Standard_G3 (identisch)|8|112 GB|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 1.536 GB</p>|16|16 x 500|
|Standard_G4 (identisch)|16|224 GB|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 3.072 GB</p>|32|32 x 500|
|Standard_G5 (identisch)|32|448 GB|<p>BS = 1023 GB</p><p>Lokales SSD-Laufwerk = 6.144 GB</p>|64|<p>64 x 500</p>|

### Siehe auch

#### Konzepte

[Einrichten eines Clouddiensts für Azure](https://msdn.microsoft.com/library/hh124108)

 [Konfigurieren von Größen für Clouddienste](https://msdn.microsoft.com/library/ee814754)

#### Weitere Ressourcen

[Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](azure-subscription-service-limits.md)
 
 [Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen](http://go.microsoft.com/fwlink/p/?linkid=328042)

[Größen für Cloud Services](cloud-services-sizes-specs.md)

<!---HONumber=58-->