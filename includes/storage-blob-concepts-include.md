## <a name="what-is"></a>Was ist Blob-Speicherung?

Der Azure-Blobspeicher ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, beispielsweise Text- oder Binärdaten, auf die von überall auf der Welt
über HTTP oder HTTPS zugegriffen werden kann. Sie können den Blobspeicher verwenden, um Daten öffentlich auf der ganzen Welt zur Verfügung zu stellen oder um Anwendungsdaten privat zu speichern.

Blobspeicher werden hauptsächlich für folgende Zwecke verwendet:

-   Speichern von Bildern oder Dokumenten direkt auf einem Browser
-   Speichern von Dateien für verteilten Zugriff
-   Video- und Audio-Streaming
-   Ausführung von sicheren Backups und Wiederherstellungen im Notfall
-   Speicherung von Daten für Analysen durch einen lokalen oder in Azure gehosteten
    Dienst

## <a name="concepts"></a>Konzepte

Der Blob-Dienst umfasst die folgenden Komponenten:

![Blob1][Blob1]

-   **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/dn249410.aspx) finden Sie Details zur Speicherkontokapazität.

-   **Container:** Ein Container dient zur Gruppierung eines Satzes von Blobs. Alle Blobs müssen sich in Containern befinden. Ein Konto kann eine unbegrenzte Anzahl von Containern enthalten. Ein Container kann eine unbegrenzte Anzahl von Blobs speichern.

-   **Blob:** Eine Datei eines beliebigen Typs und von beliebiger Größe. Es gibt zwei Arten von Blobs, die in Azure Storage gespeichert werden können: Block- und Seitenblobs. Die meisten Dateien sind Block-Blobs. Ein einzelner Block-Blob kann bis zu 200 GB groß sein. In diesem Tutorial werden Block-Blobs verwendet. Seiten-Blobs, ein anderer Blob-Typ, können bis zu 1 TB groß sein und sind effizienter, wenn Bytebereiche in einer Datei häufig geändert werden. Weitere Informationen zu Blobs finden Sie unter [Grundlegendes zu Blockblobs und Seitenblobs][].

-   **URL-Format:** Blobs sind über das folgende URL
    -Format adressierbar: `http://<storage account>`.blob.core.windows.net/`<container>`/`<blob>`  
      
    Mit der folgenden Beispiel-URL kann z. B. einer der Blobs im Diagramm oben adressiert werden: `http://sally.blob.core.windows.net/movies/MOV1.AVI`


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!--HONumber=42-->
