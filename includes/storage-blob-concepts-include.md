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

-   **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto.
    Weitere Informationen zur Kapazität der Speicherkonten finden Sie unter [Azure Storage Scalability and Performance Targets](storage-scalability-targets.md) (Skalierbarkeits- und Leistungsziele für Microsoft Azure-Speicher, in englischer Sprache).

-   **Container:** Ein Container dient zur Gruppierung eines Satzes von Blobs.
    Alle BLOBs müssen sich in Containern befinden. Ein Konto kann eine beliebige Anzahl von Containern enthalten. In einem Container kann eine beliebige Anzahl von BLOBs gespeichert sein.

-   **Blob:** Eine Datei von beliebiger Art und Größe. Der Azure-Speicher bietet drei Arten von Blobs: Blockblobs, Seitenblobs und Anfügeblobs.
    
	*Blockblobs* eignen sich ideal zum Speichern von Text- oder Binärdateien, z. B. Dokumente und Mediendateien. *Anfügeblobs* ähneln Blockblobs dahingehend, dass sie aus Blöcken bestehen. Allerdings sind sie für Anfügevorgänge optimiert, sodass sie für Protokollierungsszenarien nützlich sind. Ein einzelnes Blockblob oder Anfügeblob kann bis zu 50.000 Blöcke mit jeweils bis zu 4 MB enthalten, für eine Gesamtgröße von etwas mehr als 195 GB (4 MB X 50.000).
    
	*Seitenblobs* können bis zu 1 TB groß sein und sind besonders für häufige Lese-und Schreibvorgänge effizient. Virtuelle Azure-Computer verwenden Seitenblobs als Betriebssystem und Datenträger.

	Weitere Informationen über Blobs finden Sie unter [Grundlegendes zu Blockblobs, Anfügeblobs und Seitenblobs](https://msdn.microsoft.com/library/azure/ee691964.aspx).

## Benennen von Containern und Blobs und verweisen auf diese

Sie können einen Blob im Speicherkonto über das folgende URL-Format adresssieren:
   
    http://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>  
      
Hier ist ein Beispiel für eine URL, die einen der Blobs im Diagramm oben adressiert:

    http://sally.blob.core.windows.net/movies/MOV1.AVI

### Benennungsregeln für Container

Ein Containername muss ein gültiger DNS-Name sein und den folgenden Regeln entsprechen:

- Der Containername muss aus Kleinbuchstaben bestehen.
- Containernamen müssen mit einem Buchstaben oder einer Zahl beginnen und dürfen nur Buchstaben, Zahlen und Bindestriche (-) enthalten.
- Jedem Bindestrich (-) muss unmittelbar ein Buchstabe oder eine Zahl vorangehen und folgen von einem Buchstaben oder einer Zahl; zudem dürfen nicht mehrere Bindestriche direkt aufeinander folgen.
- Containernamen müssen zwischen 3 und 63 Zeichen lang sein.

### Benennungsregeln für Blobs

Ein Blob-Name muss den folgenden Regeln entsprechen:

- Ein Blob-Name kann jede Kombination von Zeichen enthalten.
- Ein Blob-Name muss mindestens ein Zeichen lang sein und darf nicht mehr als 1024 Zeichen lang sein.
- Bei Blob-Namen wird Groß-/Kleinschreibung unterschieden.
- Reservierte URL-Zeichen müssen angemessen durch ein Escapezeichen geschützt werden.
- Die Anzahl der Pfadsegmente, die den Blob-Namen enthalten, darf 254 nicht überschreiten. Ein Pfadsegment ist die Zeichenfolge zwischen aufeinander folgenden Trennzeichen ( *z. B.* , einen Schrägstrich "/"), die dem Namen eines virtuellen Verzeichnisses entspricht.

Der Blob-Dienst basiert auf einem flachen Speicherschema. Sie können eine virtuelle Hierarchie erstellen, indem Sie ein Zeichen- oder Zeichenfolgentrennzeichen im Blob-Namen angeben, um eine virtuelle Hierarchie zu erstellen. Die folgende Liste zeigt einige Beispiele für einen gültigen und eindeutigen Blob-Namen:

	/a
	/a.txt
	/a/b
	/a/b.txt

Sie können das Trennzeichen verwenden, um Blobs hierarchisch aufzulisten.


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!-----HONumber=AcomDC_0224_2016-->