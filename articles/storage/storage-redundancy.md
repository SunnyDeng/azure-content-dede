
<properties 
  pageTitle="Azure Storage-Replikation | Microsoft Azure" 
  description="Die Daten in Ihrem Microsoft Azure-Speicherkonto werden stets repliziert, um Beständigkeit und hohe Verfügbarkeit sicherzustellen. Die Redundanzoptionen umfassen den lokal redundanten Speicher (LRS), den zonenredundanten Speicher (ZRS), den georedundanten Speicher (GRS) und den georedundanten Speicher mit Lesezugriff (RA-GRS)." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="carmonm" 
  editor="tysonn"/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="02/17/2016" 
  ms.author="tamram"/>

# Azure Storage-Replikation

Die Daten in Ihrem Microsoft Azure-Speicherkonto werden stets repliziert, um Beständigkeit und hohe Verfügbarkeit sicherzustellen sowie die [Azure Storage-SLA](https://azure.microsoft.com/support/legal/sla/storage) auch bei vorübergehend auftretenden Hardwareausfällen zu erfüllen.

Wenn Sie ein Speicherkonto erstellen, müssen Sie eine der folgenden Replikationsoptionen auswählen:

- [Lokal redundanter Speicher (LRS)](#locally-redundant-storage)
- [Zonenredundanter Speicher (ZRS)](#zone-redundant-storage)
- [Georedundanter Speicher (GRS)](#geo-redundant-storage)
- [Georedundanter Speicher mit Lesezugriff (RA-GRS)](#read-access-geo-redundant-storage)

Die folgende Tabelle bietet eine schnelle Übersicht über die Unterschiede zwischen LRS, ZRS, GRS und RA-GRS. In den nachfolgenden Abschnitten werden die einzelnen Replikationstypen ausführlicher behandelt.


| Replikationsstrategie | LRS | ZRS | GRS | RA-GRS |
|:-----------------------------------------------------------------------------------|:----|:----|:----|:-------|
| Daten werden in mehreren Datencentern repliziert. | Nein | Ja | Ja | Ja |
| Daten können vom sekundären Standort sowie vom primären Standort aus gelesen werden. | Nein | Nein | Nein | Ja |
| Anzahl von Datenkopien, die auf separaten Knoten aufbewahrt werden. | 3 | 3 | 6 | 6 |


## Lokal redundanter Speicher

Lokal redundanter Speicher (LRS) repliziert Ihre Daten innerhalb der Region, in der Sie das Speicherkonto erstellt haben. Um die Beständigkeit zu maximieren, wird jede im Speicherkonto an Ihre Daten gerichtete Anforderung dreimal repliziert. Diese drei Replikate befinden sich in separaten Fehler- und Upgradedomänen. Eine Fehlerdomäne (FD) ist eine Gruppe von Knoten, die eine physische Fehlereinheit darstellen und als Knoten angesehen werden können, die zum gleichen physischen Gestell gehören. Eine Upgradedomäne (UD) ist eine Gruppe von Knoten, die während des Vorgangs eines Dienstupgrades (Rollouts) gemeinsam aktualisiert werden. Die drei Replikate sind auf UDs und FDs verteilt, um sicherzustellen, dass Daten verfügbar sind, auch wenn ein Hardwarefehler sich auf ein einzelnes Gestell auswirkt, und wenn Knoten während eines Rollouts aktualisiert werden. Eine Anforderung wird nur einmal erfolgreich zurückgegeben, nachdem sie in alle drei Replikate geschrieben wurde.

Wenngleich georedundanter Speicher (GRS) für die meisten Anwendungen empfohlen wird, kann unter bestimmten Umständen lokal redundanter Speicher sinnvoll sein:

- LRS ist kostengünstiger als GRS und bietet außerdem höheren Durchsatz. Wenn Ihre Anwendung Daten speichert, die problemlos wiederhergestellt werden können, können Sie sich für LRS entscheiden.

- Einige Anwendungen sind aufgrund von Datenvorschriften auf die Replikation von Daten innerhalb einer einzelnen Region beschränkt.

- Wenn Ihre Anwendung über eine eigene Georeplikationsstrategie verfügt, ist GRS ggf. nicht erforderlich.


## Zonenredundanter Speicher

Zonenredundanter Speicher (ZRS) repliziert Ihre Daten in zwei bis drei Rechenzentren, entweder innerhalb einer einzelnen Region oder in zwei Regionen, wodurch eine höhere Beständigkeit als bei LRS gewährleistet ist. Wenn für Ihr Speicherkonto ZRS aktiviert ist, sind Ihre Daten selbst bei einem Ausfall eines der Rechenzentren beständig gespeichert.


>[AZURE.NOTE]  ZRS ist derzeit nur für Blockblobs verfügbar und wird nur in Version 2014-02-14 und höher unterstützt. Hinweis: Nachdem Sie Ihr Speicherkonto erstellt und die zonenredundante Replikation ausgewählt haben, ist es nicht mehr möglich, einen anderen Replikationstyp zu wählen. Gleiches gilt für die anderen Replikationstypen.


## Georedundanter Speicher

Georedundanter Speicher (GRS) repliziert Ihre Daten in eine sekundäre Region, die Hunderte von Kilometern von der primären Region entfernt ist. Wenn für Ihr Speicherkonto GRS aktiviert ist, sind Ihre Daten beständig gespeichert, selbst bei einem regionalen Komplettausfall oder einem Notfall, nach dem die primäre Region nicht mehr wiederherstellbar ist.

Bei ein Speicherkonto mit aktiviertem GRS wird für ein Update zuerst ein Commit in der primären Region ausgeführt, in der es drei Mal repliziert wird. Danach wird das Update in die sekundäre Region repliziert, in der es auch dreimal in getrennten Fehler- und Upgradedomänen repliziert wird.


> [AZURE.NOTE] Bei GRS werden Anforderungen zum Schreiben von Daten asynchron in die sekundäre Region repliziert. Wichtig ist der Hinweis, dass die Entscheidung für GRS die Latenz von Anforderungen an die primäre Region nicht beeinflusst. Da eine asynchrone Replikation eine Verzögerung einschließt, ist es bei einem regionalen Notfall möglich, dass Änderungen, die noch nicht in die sekundäre Region repliziert wurden, möglicherweise verloren gehen, wenn die Daten nicht aus der primären Region wiederhergestellt werden können.
 
Wenn Sie ein Speicherkonto erstellen, wählen Sie die primäre Region für das Konto aus. Die sekundäre Region wird basierend auf der primären Region bestimmt und kann nicht geändert werden. In der folgenden Tabelle werden die Paare primärer und sekundärer Regionen gezeigt:
 
| Primär | Sekundär |
|---------------------|---------------------|
| USA (Mitte/Norden) | USA (Mitte/Süden) |
| USA (Mitte/Süden) | USA (Mitte/Norden) |
| USA (Ost) | USA (West) |
| USA (West) | USA (Ost) |
| USA (Ost 2) | USA (Mitte) |
| USA (Mitte) | USA (Ost 2) |
| Nordeuropa | Westeuropa |
| Westeuropa | Nordeuropa |
| Südostasien | Ostasien |
| Ostasien | Südostasien |
| Ostchina | Nordchina |
| Nordchina | Ostchina |
| Japan Ost | Japan (Westen) |
| Japan (Westen) | Japan Ost |
| Brasilien Süd | USA (Mitte/Süden) |
| Australien (Ost) | Australien (Südost) |
| Australien (Südost) | Australien (Ost) |
| Indien, Süden | Indien, Mitte |
| Indien, Mitte | Indien, Süden |


## Georedundanter Speicher mit Lesezugriff

Georedundanter Speicher mit Lesezugriff (RA-GRS) maximiert die Verfügbarkeit für das Speicherkonto, indem ein schreibgeschützter Zugriff auf Daten am sekundären Standort zusätzlich zur von GRS gebotenen Replikation in zwei Regionen bereitgestellt wird. Falls Daten in der primären Region nicht verfügbar sind, kann Ihre Anwendung Daten aus der sekundären Region lesen.

Wenn Sie den schreibgeschützten Zugriff auf Ihre Daten in der sekundären Region aktivieren, sind Ihre Daten zusätzlich zum primären Endpunkt für Ihr Speicherkonto auf einem sekundären Endpunkt verfügbar. Der sekundäre Endpunkt ähnelt dem primären Endpunkt, wobei das Suffix `–secondary` an den Kontonamen angefügt wird. Wenn Ihr primärer Endpunkt für den Blob-Dienst z. B. `myaccount.blob.core.windows.net` ist, dann ist Ihr sekundärer Endpunkt `myaccount-secondary.blob.core.windows.net`. Die Zugriffsschlüssel für das Speicherkonto sind für die primären und sekundären Endpunkte identisch.

## Nächste Schritte

- [Informationen zu Azure-Speicherkonten](storage-create-storage-account.md)
- [Skalierbarkeits- und Leistungsziele für Azure-Speicher](storage-scalability-targets.md)
- [Microsoft Azure Storage Redundancy Options and Read Access Geo Redundant Storage (in englischer Sprache)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Microsoft Azure Storage Emulator 3.1 with RA-GRS (in englischer Sprache)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/08/microsoft-azure-storage-emulator-3-1-with-ra-grs.aspx)
- [SOSP Paper - Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (SOSP-Dokument – Azure Storage: ein hochverfügbarer Cloudspeicherdienst mit starker Konsistenz)  

<!---HONumber=AcomDC_0218_2016-->