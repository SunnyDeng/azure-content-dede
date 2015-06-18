<properties 
	pageTitle="Azure Storage-Redundanzoptionen | Microsoft Azure" 
	description="Die Daten in Ihrem Microsoft Azure-Speicherkonto werden stets repliziert, um Beständigkeit und hohe Verfügbarkeit sicherzustellen. Machen Sie sich mit Replikationsoptionen für Ihr Azure-Speicherkonto vertraut." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="tamram"/>

# Azure Storage-Redundanzoptionen



Die Daten in Ihrem Microsoft Azure-Speicherkonto werden stets repliziert, um Beständigkeit und hohe Verfügbarkeit sicherzustellen sowie die [Azure Storage-SLA](http://azure.microsoft.com/support/legal/sla/) auch bei vorübergehend auftretenden Hardwareausfällen zu erfüllen. Architekturdetails zur Beständigkeit von Azure Storage finden Sie im [SOSP-Dokument zu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

Wenn Sie Ihr Speicherkonto erstellen, müssen Sie eine der folgenden Replikationsoptionen auswählen:  

- **Lokal redundanter Speicher (LRS).** Lokal redundanter Speicher bewahrt drei Kopien Ihrer Daten auf. LRS wird dreimal innerhalb eines einzigen Rechenzentrums in einer Region repliziert. LRS schützt Ihre Daten vor normalen Hardwarefehlern, jedoch nicht vor dem Ausfall eines einzelnen Rechenzentrums.
  
LRS wird zu günstigen Preisen angeboten. Für maximale Stabilität empfehlen wir, dass Sie georedundanten Speicher (nachfolgend beschrieben) verwenden.


- **Zonenredundanter Speicher (ZRS).** Zonenredundanter Speicher bewahrt drei Kopien Ihrer Daten auf. ZRS wird dreimal in zwei bis drei Rechenzentren entweder in einer Region oder zwei Regionen repliziert, wodurch eine höhere Beständigkeit als bei LRS gewährleistet ist. ZRS sorgt dafür, dass Ihre Daten innerhalb einer einzelnen Region beständig sind.
ZRS liefert eine höhere Stabilität als LRS. Für maximale Stabilität empfehlen wir jedoch, dass Sie georedundanten Speicher (nachfolgend beschrieben) verwenden.  

	> [AZURE.NOTE] ZRS ist derzeit nur für Blockblobs verfügbar.  
	> Sobald Sie Ihr Speicherkonto erstellt und die zonenredundante Replikation ausgewählt haben, ist die Wahl eines anderen Typs von Replikation nicht mehr möglich.
 


- **Georedundanter Speicher (GRS)**. Nach der Erstellung Ihres Speicherkontos ist geografisch redundanter Speicher standardmäßig aktiviert. GRS bewahrt sechs Kopien Ihrer Daten auf. Mit GRS werden Ihre Daten dreimal innerhalb der primären Region und dreimal in einer sekundären Region Hunderte von Kilometern von der primären Region entfernt repliziert, wodurch höchste Beständigkeit erreicht wird. Im Falle eines Ausfalls in der primären Region führt Azure Storage ein Failover auf die sekundäre Region aus. Durch GRS wird sichergestellt, dass Ihre Daten in zwei separaten Regionen stabil sind.


- **Georedundanter Speicher mit Lesezugriff (RA-GRS)**. Georedundanter Speicher mit Lesezugriff repliziert Ihre Daten an einem sekundären geografischen Standort und stellt Lesezugriff auf die Daten am sekundären Standort bereit. Mithilfe von georedundantem Speicher mit Lesezugriff können Sie entweder vom primären oder vom sekundären Speicherort aus auf Ihre Daten zugreifen, falls einer der Speicherorte nicht verfügbar ist.

	> [AZURE.WICHTIG] Sie können ändern, wie Ihre Daten nach Erstellen des Speicherkontos repliziert werden, aber beachten Sie, dass zusätzliche Kosten für eine einmalige Datenübertragung anfallen können, wenn Sie von LRS zu GRS oder RA-GRS wechseln. Wenn Sie beim Erstellen des Kontos GRS auswählen, können Sie anschließend nicht zu einem anderen Typ von Replikation wechseln.
 

Die folgende Tabelle bietet einen schnellen Überblick über die Unterschiede zwischen LRS, ZRS, GRS und RA-GRS. In den nachfolgenden Abschnitten werden die einzelnen Replikationstypen ausführlicher behandelt.


|Replikationsstrategie|LRS|ZRS|GRS|RA-GRS 
|--------------------|---|---|---|------
|Daten werden in mehreren Rechenzentren repliziert|Nein|Ja|Ja|Ja|
|Daten können vom sekundären Standort sowie vom primären Standort aus gelesen werden|Nein|Nein|Ja|Ja
|Anzahl von Kopien von Daten, die auf separaten Knoten aufbewahrt werden|3|3|6|6
 

Preisinformationen für die Speicherkontoreplikation finden Sie unter [Speicher - Preisdetails](http://azure.microsoft.com/pricing/details/storage/).

## Lokal redundanter Speicher (LRS)

Lokal redundanter Speicher repliziert Ihre Daten innerhalb der Region, in der Sie das Speicherkonto erstellt haben. Um die Beständigkeit zu maximieren, wird jede im Speicherkonto an Ihre Daten gerichtete Anforderung dreimal repliziert. Diese drei Replikate befinden sich in separaten Fehler- und Upgradedomänen. Eine Fehlerdomäne (FD) ist eine Gruppe von Knoten, die eine physische Fehlereinheit darstellen und als Knoten angesehen werden können, die zum gleichen physischen Gestell gehören. Eine Upgradedomäne (UD) ist eine Gruppe von Knoten, die während des Vorgangs des Dienstupgrades (Rollouts) gemeinsam aktualisiert werden. Die drei Replikate sind auf UDs und FDs verteilt, um sicherzustellen, dass Daten verfügbar sind, auch wenn ein Hardwarefehler sich auf ein einzelnes Gestell auswirkt, und wenn Knoten während eines Rollouts aktualisiert werden. Eine Anforderung wird nur einmal erfolgreich zurückgegeben, nachdem sie in alle drei Replikate geschrieben wurde.

Wenngleich geografisch redundanter Speicher für die meisten Anwendungen empfohlen wird, kann lokal redundanter Speicher unter bestimmten Umständen wünschenswert sein:  

- LRS ist kostengünstiger als GRS und bietet außerdem höheren Durchsatz. Wenn Ihre Anwendung Daten speichert, die problemlos wiederhergestellt werden können, können Sie sich für LRS entscheiden.

- Einige Anwendungen sind aufgrund von Datenvorschriften auf die Replikation von Daten innerhalb einer einzelnen Region beschränkt.

- Wenn Ihre Anwendung über eine eigene Georeplikationsstrategie verfügt, ist GRS ggf. nicht erforderlich.


## Zonenredundanter Speicher (ZRS)

Zonenredundanter Speicher repliziert Ihre Daten in zwei bis drei Rechenzentren, entweder innerhalb einer einzelnen Region oder in zwei Regionen, wodurch eine höhere Beständigkeit als bei LRS gewährleistet ist. Wenn für Ihr Speicherkonto ZRS aktiviert ist, sind Ihre Daten selbst bei einem Ausfall eines der Rechenzentren beständig gespeichert.


>[AZURE.NOTE]  ZRS ist derzeit nur für Blockblobs verfügbar. Hinweis: Nachdem Sie Ihr Speicherkonto erstellt und die zonenredundante Replikation ausgewählt haben, können Sie diese nicht mehr so konvertieren, dass ein anderer Replikationstyp verwendet wird oder umgekehrt.


## Georedundanter Speicher (GRS)


Georedundante Speicher repliziert Ihre Daten in eine sekundäre Region, die Hunderte von Kilometern von der primären Region entfernt ist. Wenn für Ihr Speicherkonto GRS aktiviert ist, sind Ihre Daten beständig gespeichert, selbst bei einem regionalen Komplettausfall oder einem Notfall, nach dem die primäre Region nicht mehr wiederherstellbar ist. 

Bei ein Speicherkonto mit aktiviertem GRS wird für ein Update zuerst ein Commit in der primären Region ausgeführt, in der es drei Mal repliziert wird. Danach wird das Update in die sekundäre Region repliziert, in der es auch dreimal in getrennten Fehler- und Upgradedomänen repliziert wird.

 
> [AZURE.NOTE] Bei GRS werden Anforderungen zum Schreiben von Daten asynchron in die sekundäre Region repliziert. Wichtig ist der Hinweis, dass die Entscheidung für GRS die Latenz von Anforderungen an die primäre Region nicht beeinflusst. Da eine asynchrone Replikation eine Verzögerung einschließt, ist es bei einem regionalen Notfall möglich, dass Änderungen, die noch nicht in die sekundäre Region repliziert wurden, möglicherweise verloren gehen, wenn die Daten nicht aus der primären Region wiederhergestellt werden können.
 
Wenn Sie ein Speicherkonto erstellen, wählen Sie die primäre Region für das Konto aus. Die sekundäre Region wird basierend auf der primären Region bestimmt und kann nicht geändert werden. In der folgenden Tabelle werden die Paare primärer und sekundärer Regionen gezeigt:

|Primär            |Sekundär        
| ---------------  |----------------
|USA, Norden-Mitte |USA, Süden-Mitte
|USA, Süden-Mitte  |USA, Norden-Mitte
|USA, Osten        |USA, Westen        
|USA, Westen       |USA, Osten         
|USA, Osten 2      |USA, Mitte      
|USA, Mitte        |USA, Osten 2       
|Nordeuropa        |Westeuropa     
|Westeuropa        |Nordeuropa    
|Südostasien       |Ostasien       
|Ostasien          |Südostasien 
|Ostchina          |Nordchina     
|Nordchina         |Ostchina      
|Ostjapan          |Westjapan      
|Westjapan         |Ostjapan      
|Südbrasilien      |USA, Süden-Mitte
|Ostaustralien     |Südostaustralien
|Südostaustralien  |Ostaustralien  

 
## Georedundanter Speicher mit Lesezugriff (RA-GRS)

Georedundanter Speicher mit Lesezugriff maximiert die Verfügbarkeit für das Speicherkonto, indem ein schreibgeschützter Zugriff auf Daten am sekundären Standort zusätzlich zur von GRS gebotenen Replikation in zwei Regionen bereitgestellt wird. Falls Daten in der primären Region nicht verfügbar sind, kann Ihre Anwendung Daten aus der sekundären Region lesen.

Wenn Sie den schreibgeschützten Zugriff auf Ihre Daten in der sekundären Region aktivieren, sind Ihre Daten zusätzlich zum primären Endpunkt für Ihr Speicherkonto auf einem sekundären Endpunkt verfügbar. Der sekundäre Endpunkt ähnelt dem primären Endpunkt, wobei das Suffix "-secondary' an den Kontonamen angefügt wird. Wenn Ihr primärer Endpunkt für den Blob-Dienst z. B.  `myaccount.blob.core.windows.net` ist, dann ist Ihr sekundärer Endpunkt  `myaccount-secondary.blob.core.windows.net`. Die Zugriffsschlüssel für das Speicherkonto sind für die primären und sekundären Endpunkte identisch.

## Nächste Schritte

- [Azure Storage-Skalierbarkeits- und Leistungsziele](storage-scalability-targets.md) 
- [Microsoft Azure Storage-Redundanzoptionen und georedundanter Speicher mit Lesezugriff ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Microsoft Azure Storage Emulator 3.1 mit RA-GRS ](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/08/microsoft-azure-storage-emulator-3-1-with-ra-grs.aspx)
- [SOSP-Dokument zu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)


<!--HONumber=52--> 