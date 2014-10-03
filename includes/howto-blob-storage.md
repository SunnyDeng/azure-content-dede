## <a name="what-is"> </a>Was ist BLOB-Speicherung?

Die BLOB-Speicherung in Azure ist ein Dienst zur Speicherung großer Mengen
unstrukturierter Daten, auf die von überall auf der Welt über
HTTP oder HTTPS zugegriffen werden kann Ein einzelner BLOB kann Hunderte von GB umfassen.

Ein Azure-Speicherkonto kann große Mengen an Blob-, Warteschlangen- und Tabellendaten enthalten. Weitere Informationen zur Kapazität der Speicherkonten finden Sie unter [Azure Storage Scalability and Performance Targets][] (Skalierbarkeits- und Leistungsziele für Windows Azure-Speicher, in englischer Sprache).

BLOB-Speicherungen werden hauptsächlich für folgende Zwecke verwendet:

-   Speichern von Bildern oder Dokumenten direkt auf einem Browser
-   Speichern von Dateien für verteilten Zugriff
-   Video- und Audio-Streaming
-   Ausführung von sicheren Backups und Wiederherstellungen im Notfall
-   Speicherung von Daten für Analysen durch einen lokalen oder von
    Azure gehosteten Dienst

Mit BLOB-Speicherung können Sie Daten öffentlich auf der ganzen Welt zur Verfügung stellen
oder auf privaten Zugriff über interne Anwendungen beschränken.

## <a name="concepts"> </a>Konzepte

Der BLOB-Dienst umfasst die folgenden Komponenten:

![Blob1][]

-   **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen
    über ein Speicherkonto. Weitere Informationen zur Kapazität der Speicherkonten finden Sie unter [Azure Storage Scalability and Performance Targets][] (Skalierbarkeits- und Leistungsziele für Windows Azure-Speicher, in englischer Sprache).

-   **Container:** Ein Container dient zur Gruppierung eines Satzes von Blobs.
    Alle BLOBs müssen sich in Containern befinden. Ein Konto kann eine
    beliebige Anzahl von Containern enthalten. In einem Container kann eine
    beliebige Anzahl von BLOBs gespeichert sein.

-   **BLOB:** Eine Datei von beliebiger Art und Größe. Es gibt zwei Arten von BLOBs,
    die im Azure-Speicher gespeichert werden können: Block- und Seiten-BLOBs.
    Most files are block blobs. Ein einzelner Block-BLOB kann bis zu 200 GB
    groß sein. In diesem Tutorial werden Block-BLOBs verwendet. Die andere Art von BLOBs,
    Seiten-BLOBs, kann bis zu 1 TB groß sein und ist effizienter, wenn
    Byte-Bereiche in einer Datei häufig geändert werden. Weitere Informationen
    zu BLOBs finden Sie unter [Grundlegendes zu Block-BLOBs und Seiten-BLOBs][]..

-   **URL-Format:** Blobs sind über das folgende URL-Format
    adressierbar:
    <http://>`<storage account>`.blob.core.windows.net/`<container>`/`<blob>`

    Mit der folgenden URL kann z. B. einer der Blobs im Diagramm oben
    adressiert werden:
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`

  [Azure Storage Scalability and Performance Targets]: http://msdn.microsoft.com/en-us/library/dn249410.aspx
  [Blob1]: ./media/howto-blob-storage/blob1.jpg
