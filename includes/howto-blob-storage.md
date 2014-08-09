
## <a name="what-is"> </a>Was ist BLOB-Speicherung?

Die BLOB-Speicherung in Azure ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Ein einzelner BLOB kann mehrere hundert Gigabytes groß sein, und in einem einzelnen Speicherkonto können sich BLOBs mit einer Gesamtgröße von bis zu 200 TB befinden, wenn dieses Konto am 8. Juni 2012 oder später angelegt wurde. Speicherkonten, die vorher angelegt wurden, können BLOBs mit insgesamt 100 TB Größe enthalten. Weitere Informationen zur Kapazität der Speicherkonten finden Sie unter [Azure Storage Scalability and Performance Targets][1]
(Skalierbarkeits- und Leistungsziele für Azure-Speicher, in englischer
Sprache).

BLOB-Speicherungen werden hauptsächlich für folgende Zwecke verwendet:

* Speichern von Bildern oder Dokumenten direkt auf einem Browser
* Speichern von Dateien für verteilten Zugriff
* Video- und Audio-Streaming
* Ausführung von sicheren Backups und Wiederherstellungen im Notfall
* Speicherung von Daten für Analysen durch einen lokalen oder von Azure
  gehosteten Dienst

Mit BLOB-Speicherung können Sie Daten öffentlich auf der ganzen Welt zur Verfügung stellen oder auf privaten Zugriff über interne Anwendungen beschränken.

## <a name="concepts"> </a>Konzepte

Der BLOB-Dienst umfasst die folgenden Komponenten:

![Blob1](./media/howto-blob-storage/blob1.jpg)

* **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über
  ein Speicherkonto. Weitere Informationen zur Kapazität der
  Speicherkonten finden Sie unter [Azure Storage Scalability and
  Performance Targets][1] (Skalierbarkeits- und Leistungsziele für
  Azure-Speicher, in englischer Sprache).

* **Container:** Ein Container dient zur Gruppierung eines Satzes von
  BLOBs. Alle BLOBs müssen sich in Containern befinden. Ein Konto kann
  eine beliebige Anzahl von Containern enthalten. In einem Container
  kann eine beliebige Anzahl von BLOBs gespeichert sein.

* **BLOB:** Eine Datei von beliebiger Art und Größe. Es gibt zwei Arten
  von BLOBs, die im Azure-Speicher gespeichert werden können: Block- und
  Seiten-BLOBs. Die meisten Dateien sind Block-BLOBs. Ein einzelner
  Block-BLOB kann bis zu 200 GB groß sein. In diesem Tutorial werden
  Block-BLOBs verwendet. Die andere Art von BLOBs, Seiten-BLOBs, kann
  bis zu 1 TB groß sein und ist effizienter, wenn Byte-Bereiche in einer
  Datei häufig geändert werden. Weitere Informationen über BLOBs finden
  Sie unter [Grundlegendes zu Block-BLOBs und Seiten-BLOBs][2].

* **URL-Format:** BLOBs sind über das folgende URL-Format adressierbar: 
  
	http://`<storage 
	account>`.blob.core.windows.net/`<container>`/`<blob>`
  
	Zur Adressierung eines der BLOBs im Diagramm oben sollte die folgende
	Beispiel-URL verwendet werden:  
   `http://sally.blob.core.windows.net/movies/MOV1.AVI`



[1]: http://msdn.microsoft.com/de-de/library/dn249410.aspx
[2]: http://msdn.microsoft.com/de-de/library/windowsazure/ee691964.aspx