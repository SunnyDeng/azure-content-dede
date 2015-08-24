Ressource|Standardlimit
---|---
Max. Anzahl von Speicherkonten pro Abonnement|100<sup>1</sup>
TB pro Speicherkonto|500 TB
Maximale Größe eines einzelnen Blob-Containers, einer Tabelle oder einer Warteschlange|500 TB
Max. Anzahl von Blöcken in einen Blockblob|50\.000
Maximale Größe eines Blocks in einem Blockblob|4 MB
Maximale Größe eines Blockblobs|50\.000 x 4 MB \(ca. 195 GB\) 
Max. Größe eines Seitenblobs |1 TB
Maximale Größe einer Tabellenentität|1 MB
Max. Anzahl von Eigenschaften in einer Tabellenentität|252
Maximale Größe einer Nachricht in einer Warteschlange|64 KB
Maximale Größe einer Dateifreigabe|5 TB
Maximale Größe einer Datei in einer Dateifreigabe|1 TB
Maximale Anzahl an Dateien in einer Dateifreigabe|Die einzige Einschränkung besteht in der Gesamtkapazität der Dateifreigabe von 5 TB.
Maximale Anzahl an Blob-Containern, Blobs, Dateifreigaben, Tabellen, Warteschlangen, Entitäten oder Meldungen pro Speicherkonto|Die einzige Einschränkung besteht in der Speicherkontokapazität von 500 TB.
Maximal 8 KB IOPS pro persistentem Laufwerk \(virtueller Computer mit Basic-Tarif\)|300<sup>2</sup>
Maximal 8 KB IOPS pro persistentem Laufwerk \(virtueller Computer mit Standard-Tarif\)|500<sup>2</sup>
Gesamtanfragerate \(ausgehend von einer Objektgröße von 1 KB\) pro Speicherkonto|Bis zu 20.000 IOPS, Entitäten pro Sekunde oder Nachrichten pro Sekunde
Zieldurchsatz bei Einzel-Blob|Bis zu 60 MB pro Sekunde, oder bis zu 500 Anforderungen pro Sekunde
Zieldurchsatz bei Einzelwarteschlange \(Meldungen mit 1 KB\)|Bis zu 2000 Meldungen pro Sekunde
Zieldurchsatz bei einzelner Tabellenpartition \(Entitäten mit 1 KB\)|Bis zu 2000 Entitäten pro Sekunde
Zieldurchsatz für eine einzelne Dateifreigabe \(Vorschau\)|Bis 60 MB pro Sekunde
Max. Eingang<sup>3</sup> pro Speicherkonto \(US-Regionen\)|10 GBit/s mit aktiviertem GRS/ZRS<sup>4</sup>, 20 GBit/s bei LRS
Max. Ausgang<sup>3</sup> pro Speicherkonto \(US-Regionen\)|20 GBit/s mit aktiviertem GRS/ZRS<sup>4</sup>, 30 GBit/s bei LRS
Max. Eingang<sup>3</sup> pro Speicherkonto \(Regionen Europa und Asien\)|5 GBit/s mit aktiviertem GRS/ZRS<sup>4</sup>, 10 GBit/s bei LRS
Max. Ausgang<sup>3</sup> pro Speicherkonto \(Regionen Europa und Asien\)|10 GBit/s mit aktiviertem GRS/ZRS<sup>4</sup>, 15 GBit/s bei LRS

<sup>1</sup>Wenn Sie mehr als 100 Speicherkonten benötigen, wenden Sie sich an den [Azure-Support](http://azure.microsoft.com/support/faq/).

<sup>2</sup>Die gesamte Anforderungsrate für ein Speicherkonto ist auf 20.000 IOPS beschränkt. Wenn ein virtueller Computer die maximalen IOPS pro Datenträger belegt, sollten Sie sicherstellen, dass die gesamten IOPS auf allen virtuellen Festplatten der virtuellen Computer die Beschränkung für das Speicherkonto \(20.000 IOPS\) nicht überschreiten, um eine Drosselung zu vermeiden.

Basierend auf der Begrenzung für Transaktionen können Sie die Anzahl der Datenträger mit hoher Auslastung, die in einem Speicherkonto unterstützt werden, grob berechnen. Im Basic-Tarif liegt die maximal zulässige Anzahl der Datenträger mit hoher Auslastung für einen virtuellen Computer beispielsweise bei 66 Datenträgern \(20.000/300 IOPS pro Datenträger\) und im Standard-Tarif bei ungefähr 40 Datenträgern \(20.000/500 IOPS pro Datenträger\). Beachten Sie jedoch, dass das Speicherkonto eine größere Anzahl von Datenträgern unterstützen kann, wenn nicht alle Datenträger gleichzeitig eine hohe Auslastung aufweisen.

<sup>3</sup>*Eingang* bezieht sich auf alle Daten \(Anforderungen\), die an ein Speicherkonto gesendet werden. *Ausgang* bezieht sich auf alle Daten \(Antworten\), die von einem Speicherkonto empfangen werden.

<sup>4</sup>GRS bedeutet "Georedundanter Speicher". ZRS bedeutet "Zonenredundanter Speicher", der nur für Blockblobs zur Verfügung steht. LRS bedeutet "Lokal redundanter Speicher".

<!---HONumber=August15_HO7-->