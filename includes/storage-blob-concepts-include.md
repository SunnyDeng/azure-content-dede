## Was ist Blob-Speicher?

Der Azure-Blob-Speicher ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, beispielsweise Text- oder Binärdaten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Sie können den Blobspeicher verwenden, um Daten öffentlich auf der ganzen Welt zur Verfügung zu stellen oder um Anwendungsdaten privat zu speichern.

BLOB-Speicherungen werden hauptsächlich für folgende Zwecke verwendet:

-   Speichern von Bildern oder Dokumenten direkt auf einem Browser
-   Speichern von Dateien für verteilten Zugriff
-   Video- und Audio-Streaming
-   Ausführung von sicheren Backups und Wiederherstellungen im Notfall
-   Speicherung von Daten für Analysen durch einen lokalen oder von Azure gehosteten Dienst

## Konzepte des Blob-Diensts

Der BLOB-Dienst umfasst die folgenden Komponenten:

![Blob1][Blob1]

-   **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Weitere Informationen zur Kapazität der Speicherkonten finden Sie unter [Azure Storage Scalability and Performance Targets](storage-scalability-targets.md) (Skalierbarkeits- und Leistungsziele für Windows Azure-Speicher, in englischer Sprache).

-   **Container:** Ein Container dient zur Gruppierung eines Satzes von Blobs. Alle BLOBs müssen sich in Containern befinden. Ein Konto kann eine beliebige Anzahl von Containern enthalten. In einem Container kann eine beliebige Anzahl von BLOBs gespeichert sein.

-   **Blob:** Eine Datei von beliebiger Art und Größe. Es gibt zwei Arten von Blobs, die im Azure-Speicher gespeichert werden können: Block- und Seitenblobs. Die meisten Dateien sind Block-BLOBs. Ein einzelner Block-Blob kann bis zu 200 GB groß sein. In diesem Tutorial werden Block-BLOBs verwendet. Der andere Blob-Typ, Seiten-Blobs, kann bis zu 1 TB groß sein und ist effizienter, wenn Byte-Bereiche in einer Datei häufig geändert werden. Weitere Informationen über BLOBs finden Sie unter [Grundlegendes zu Block-BLOBs und Seiten-BLOBs](https://msdn.microsoft.com/library/azure/ee691964.aspx).

-   **URL-Format:** Blobs sind über das folgende URL-Format adressierbar: http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`
      
    Zur Adressierung eines der Blobs im Diagramm oben sollte die folgende Beispiel-URL verwendet werden:`http://sally.blob.core.windows.net/movies/MOV1.AVI`


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg


<!--HONumber=52-->
