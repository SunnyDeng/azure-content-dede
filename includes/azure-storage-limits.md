Ressource|Standardlimit
---|---
Max. Anzahl von Speicherkonten pro Abonnement|100<sup>1</sup>
TB pro Speicherkonto|500 TB
Maximale Anzahl an Blob-Containern, Blobs, Dateifreigaben, Tabellen, Warteschlangen, Entitäten oder Meldungen pro Speicherkonto|Die einzige Einschränkung besteht in der Speicherkontokapazität von 500 TB.
Maximale Größe eines einzelnen Blob-Containers, einer Tabelle oder einer Warteschlange|500 TB
Maximale Anzahl von Blöcken in einem Blockblob oder einem Anfügeblob|50\.000
Maximale Größe eines Blocks in einem Blockblob oder einem Anfügeblob|4 MB
Maximale Größe eines Blockblobs oder eines Anfügeblobs|50\.000 x 4 MB (ca. 195 GB) 
Max. Größe eines Seitenblobs |1 TB
Maximale Größe einer Tabellenentität|1 MB
Max. Anzahl von Eigenschaften in einer Tabellenentität|252
Maximale Größe einer Nachricht in einer Warteschlange|64 KB
Maximale Größe einer Dateifreigabe|5 TB
Maximale Größe einer Datei in einer Dateifreigabe|1 TB
Maximale Anzahl an Dateien in einer Dateifreigabe|Die einzige Einschränkung besteht in der Gesamtkapazität der Dateifreigabe von 5 TB.
Max. 8 KB IOPS pro Freigabe|1000
Maximale Anzahl an Dateien in einer Dateifreigabe|Die einzige Einschränkung besteht in der Gesamtkapazität der Dateifreigabe von 5 TB.
Maximale Anzahl an Blob-Containern, Blobs, Dateifreigaben, Tabellen, Warteschlangen, Entitäten oder Meldungen pro Speicherkonto|Die einzige Einschränkung besteht in der Speicherkontokapazität von 500 TB.
Maximale Anzahl der gespeicherten Zugriffsrichtlinien pro Container, Dateifreigabe, Tabelle oder Warteschlange|5
Gesamtanfragerate (ausgehend von einer Objektgröße von 1 KB) pro Speicherkonto|Bis zu 20.000 IOPS, Entitäten pro Sekunde oder Nachrichten pro Sekunde
Zieldurchsatz bei Einzel-Blob|Bis zu 60 MB pro Sekunde, oder bis zu 500 Anforderungen pro Sekunde
Zieldurchsatz bei Einzelwarteschlange (Meldungen mit 1 KB)|Bis zu 2000 Meldungen pro Sekunde
Zieldurchsatz bei einzelner Tabellenpartition (Entitäten mit 1 KB)|Bis zu 2000 Entitäten pro Sekunde
Zieldurchsatz für eine einzelne Dateifreigabe|Bis 60 MB pro Sekunde
Max. Eingang<sup>2</sup> pro Speicherkonto (US-Regionen)|10 GBit/s mit aktiviertem GRS/ZRS<sup>3</sup>, 20 GBit/s bei LRS
Max. Ausgang<sup>2</sup> pro Speicherkonto (US-Regionen)|20 GBit/s mit aktiviertem RA-GRS/GRS/ZRS<sup>3</sup>, 30 GBit/s bei LRS
Max. Eingang<sup>2</sup> pro Speicherkonto (Regionen Europa und Asien)|5 GBit/s mit aktiviertem GRS/ZRS<sup>3</sup>, 10 GBit/s bei LRS
Max. Ausgang<sup>2</sup> pro Speicherkonto (Regionen Europa und Asien)|10 GBit/s mit aktiviertem RA-GRS/GRS/ZRS<sup>3</sup>, 15 GBit/s bei LRS

<sup>1</sup>Wenn Sie mehr als 100 Speicherkonten benötigen, wenden Sie sich an den [Azure-Support](http://azure.microsoft.com/support/faq/).

<sup>2</sup>*Eingehend* bezieht sich auf alle Daten (Anforderungen), die an ein Speicherkonto gesendet werden. *Ausgang* bezieht sich auf alle Daten (Antworten), die von einem Speicherkonto empfangen werden.

<sup>3</sup>Folgende Replikationsoptionen sind für Azure Storage verfügbar:

- **RA-GRS**: Georedundanter Speicher mit Lesezugriff (RA-GRS) Wenn RA-GRS aktiviert ist, sind die Ausgangsziele für den sekundären Standort mit denen für den primären Standort identisch.
- **GRS**: Georedundanter Speicher 
- **ZRS**: Zonenredundanter Speicher Derzeit nur für Blockblobs verfügbar. 
- **LRS**: Lokal redundanter Speicher 

<!---HONumber=AcomDC_1125_2015-->