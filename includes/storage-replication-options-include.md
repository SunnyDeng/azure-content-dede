Die Daten in Ihrem Speicherkonto werden repliziert, um Dauerhaftigkeit und hohe Verfügbarkeit zu garantieren, sodass die [Azure Storage-SLA](http://azure.microsoft.com/support/legal/sla/) auch bei vorübergehenden Hardware-Ausfällen erfüllt werden. Azure Storage wird weltweit in 15 Regionen bereitgestellt und umfasst auch Unterstützung für das Replizieren von Daten zwischen Regionen. Sie haben mehrere Optionen zum Replizieren der Daten in Ihrem Speicherkonto:

- *Lokal redundanter Speicher (LRS)* verwaltet drei Kopien Ihrer Daten. LRS wird innerhalb eines einzelnen Standorts dreimal in einer einzelnen Region repliziert. LRS schützt Ihre Daten vor normalen Hardware-Ausfällen, jedoch nicht vor dem Ausfall eines einzelnen Standorts.

	LRS wird zu günstigen Preisen angeboten. Für maximale Stabilität empfehlen wir, dass Sie georedundanter Speicher (nachfolgend beschrieben) verwenden.

- *Zonenredundanter Speicher (ZRS)* verwaltet drei Kopien Ihrer Daten. ZRS wird über zwei oder drei Standorte hinweg dreimal repliziert, entweder innerhalb einer einzelnen Region oder über zwei Regionen hinweg, wodurch eine höhere Stabilität als bei LRS entsteht. ZRS stellt sicher, dass Ihre Daten innerhalb einer einzelnen Region stabil sind.
 
	ZRS liefert eine höhere Stabilität als LRS. Für maximale Stabilität empfehlen wir jedoch, dass Sie georedundanten Speicher (nachfolgend beschrieben) verwenden.

	> [AZURE.NOTE]ZRS ist derzeit nur für Blockblobs verfügbar. Hinweis: Nachdem Sie Ihr Speicherkonto erstellt und die zonenredundante Replikation ausgewählt haben, können Sie diese nicht mehr so konvertieren, dass ein anderer Replikationstyp verwendet wird oder umgekehrt.

- *Georedundanter Speicher (GRS)* ist standardmäßig für Ihr Speicherkonto aktiviert, wenn Sie es erstellen. GRS verwaltet sechs Kopien Ihrer Daten. Mit GRS werden Ihre Daten dreimal innerhalb der primären Region und dreimal in einer sekundären Region hunderte von Kilometern von der primären Region entfernt repliziert, wodurch höchste Stabilität erreicht wird. Im Falle eines Ausfalls in der primären Region führt Azure Storage ein Failover auf die sekundäre Region aus. Durch GRS wird sichergestellt, dass Ihre Daten in zwei separaten Regionen stabil sind.

	> [AZURE.NOTE]Für maximale Stabilität wird GRS vor ZRS oder LRS empfohlen.

- *Schreibgeschützter georedundanter Speicher (RA-GRS)* – bietet alle Vorteile der oben beschriebenen georedundanten Speicherung, aber darüber hinaus auch Schreibzugriff auf Daten in der sekundären Region, falls die primäre Region nicht verfügbar ist. Der schreibgeschützte georedundante Speicher wird für maximale Verfügbarkeit und Dauerhaftigkeit empfohlen.

Weitere Informationen zu Replikationsoptionen finden Sie im [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/) und unter [Redundanzoptionen von Azure Storage](../articles/storage/storage-redundancy.md).
	
Die Preisunterschiede zwischen den unterschiedlichen Replikationsoptionen sind auf der Seite [Speicherpreisdetails](http://azure.microsoft.com/pricing/details/storage/) zu finden.

<!--HONumber=52-->
