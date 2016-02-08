Die Daten in Ihrem Microsoft Azure-Speicherkonto werden stets repliziert, um Beständigkeit und hohe Verfügbarkeit sicherzustellen sowie die [Azure Storage-SLA](https://azure.microsoft.com/support/legal/sla/) auch bei vorübergehend auftretenden Hardwareausfällen zu erfüllen. Wenn Sie ein Speicherkonto erstellen, müssen Sie eine der folgenden Replikationsoptionen auswählen:

- **Lokal redundanter Speicher (LRS)** Lokal redundanter Speicher verwaltet drei Kopien Ihrer Daten. LRS wird innerhalb eines einzelnen Standorts dreimal in einer einzelnen Region repliziert. LRS schützt Ihre Daten vor normalen Hardware-Ausfällen, jedoch nicht vor dem Ausfall eines einzelnen Standorts.  
  
	LRS wird zu günstigen Preisen angeboten. Für maximale Stabilität empfehlen wir, dass Sie georedundanten Speicher (nachfolgend beschrieben) verwenden.


- **Zonenredundanter Speicher (ZRS)** Zonenredundanter Speicher verwaltet drei Kopien Ihrer Daten. ZRS wird über zwei oder drei Standorte hinweg dreimal repliziert, entweder innerhalb einer einzelnen Region oder über zwei Regionen hinweg, wodurch eine höhere Stabilität als bei LRS entsteht. ZRS stellt sicher, dass Ihre Daten innerhalb einer einzelnen Region stabil sind.

	ZRS liefert eine höhere Stabilität als LRS. Für maximale Stabilität empfehlen wir jedoch, dass Sie georedundanten Speicher (nachfolgend beschrieben) verwenden.

	> [AZURE.NOTE] ZRS ist derzeit nur für Blockblobs verfügbar und wird nur in Version 2014-02-14 und höher unterstützt.
	> 
	> Sobald Sie Ihr Speicherkonto erstellt und die ZRS ausgewählt haben, ist die Wahl eines anderen Replikationstyps nicht mehr möglich.

- **Georedundanter Speicher (GRS)**. Nach der Erstellung Ihres Speicherkontos ist geografisch redundanter Speicher standardmäßig aktiviert. GRS bewahrt sechs Kopien Ihrer Daten auf. Mit GRS werden Ihre Daten dreimal innerhalb der primären Region und dreimal in einer sekundären Region hunderte von Kilometern von der primären Region entfernt repliziert, wodurch höchste Stabilität erreicht wird. Im Falle eines Ausfalls in der primären Region führt Azure Storage ein Failover auf die sekundäre Region aus. Durch GRS wird sichergestellt, dass Ihre Daten in zwei separaten Regionen stabil sind.


- **Georedundanter Speicher mit Lesezugriff (RA-GRS)** Georedundanter Speicher mit Lesezugriff repliziert Ihre Daten an einem sekundären geografischen Standort und stellt Lesezugriff auf die Daten am sekundären Standort bereit. Mithilfe von georedundantem Speicher mit Lesezugriff können Sie entweder vom primären oder vom sekundären Speicherort aus auf Ihre Daten zugreifen, falls einer der Speicherorte nicht verfügbar ist.

	> [AZURE.IMPORTANT] Die Art der Datenreplikation kann nach der Speicherkontoerstellung geändert werden, sofern bei der Kontoerstellung nicht die ZRS-Option angegeben wurde. Beachten Sie jedoch, dass unter Umständen zusätzlich einmalige Datenübertragungskosten anfallen, wenn Sie von LRS zu GRS oder RA-GRS wechseln.
 
Weitere Details zu den Speicherreplikationsoptionen finden Sie unter [Azure-Speicherreplikation](../articles/storage/storage-redundancy.md).

Preisinformationen für die Speicherkontoreplikation finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Architekturdetails zur Beständigkeit von Azure Storage finden Sie im [SOSP-Dokument zu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

<!---HONumber=AcomDC_0128_2016-->