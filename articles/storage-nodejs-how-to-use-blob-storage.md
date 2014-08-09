<properties linkid="dev-nodejs-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Node.js) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Blob Service from Node.js" authors="" solutions="" manager="" editor="" />

Verwenden des Blob-Dienstes aus Node.js
=======================================

Dieses Handbuch demonstriert Ihnen allgemeine Szenarien unter Verwendung des Blob-Dienstes in Azure. Die Beispiele sind in C\# geschrieben und verwenden die Node.js-API. Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

-   [Was ist der Blob-Dienst?](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#create-account)
-   [Erstellen einer Node.js-Anwendung](#create-app)
-   [Konfigurieren der Anwendung für den Speicherzugriff](#configure-access)
-   [Einrichten einer Azure-Speicherverbindungszeichenfolge](#setup-connection-string)
-   [Vorgehensweise: Erstellen eines Containers](#create-container)
-   [Vorgehensweise: Hochladen eines Blobs in einen Container](#upload-blob)
-   [Vorgehensweise: Auflisten der Blobs in einem Container](#list-blob)
-   [Vorgehensweise: Herunterladen von Blobs](#download-blobs)
-   [Vorgehensweise: Löschen eines Blobs](#delete-blobs)
-   [Nächste Schritte](#next-steps)

Was ist der Blob-Dienst?
------------------------

Der Blob-Dienst in Azure dient zur Speicherung großer Mengen unstrukturierter Daten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Ein einzelner Blob kann Hunderte von Gigabyte enthalten, und ein einzelnes Speicherkonto kann bis zu 100TB an Blobs enthalten. Blobs werden hauptsächlich für folgende Zwecke verwendet:

-   Speichern von Bildern oder Dokumenten direkt auf einem Browser
-   Speichern von Dateien für verteilten Zugriff
-   Video- und Audio-Streaming
-   Ausführung von sicheren Backups und Wiederherstellungen im Notfall
-   Speicherung von Daten für Analysen durch einen lokalen oder in Azure gehosteten Dienst

Mit Blobs können Sie Daten öffentlich weltweit zur Verfügung stellen oder auf privaten Zugriff über interne Anwendungen beschränken.

Konzepte
--------

Der Blob-Dienst umfasst die folgenden Komponenten:

![Blob1](./media/storage-nodejs-how-to-use-blob-storage/blob1.jpg)

-   **URL-Format:** Blobs sind über das folgende URL-Format adressierbar:

        http://storageaccount.blob.core.windows.net/container/blob  

    Mit der folgenden URL kann einer der Blobs im Diagramm adressiert werden:

        http://sally.blob.core.windows.net/movies/MOV1.AVI

-   **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Dies ist die höchste Ebene des Namespaces für den Zugriff auf Blobs. Ein Konto kann eine beliebige Anzahl von Containern enthalten, solange deren Gesamtgröße 100TB nicht überschreitet.

-   **Container:** Ein Container dient zur Gruppierung eines Satzes von Blobs. Alle Blobs müssen sich in Containern befinden. Ein Konto kann eine beliebige Anzahl von Containern enthalten. In einem Container kann eine beliebige Anzahl von Blobs gespeichert sein.

-   **Blob:** Eine Datei von beliebiger Art und Größe. Blobs existieren in zwei verschiedenen Formen: Block und Seite. Die meisten Dateien sind Block-Blobs. Ein einzelner Block-Blob kann bis zu 200 GB groß sein. In diesem Lernprogramm werden Block-Blobs verwendet. Die andere Art von Blobs, Seiten-Blobs, kann bis zu 1 TB groß sein und ist effizienter, wenn Byte-Bereiche in einer Datei häufig geändert werden.

Erstellen eines Azure-Speicherkontos
------------------------------------

Für Speichervorgänge benötigen Sie ein Azure-Speicherkonto. Mit den folgenden Schritten können Sie ein Speicherkonto erstellen. (Sie können ein Speicherkonto auch mithilfe der [REST-API](http://msdn.microsoft.com/de-de/library/windowsazure/hh264518.aspx) erstellen.)

1.  Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2.  Klicken Sie unten im Navigationsbereich auf **+NEW**.

    ![+Neu](./media/storage-nodejs-how-to-use-blob-storage/plus-new.png)

3.  Klicken Sie auf **Speicherkonto** und anschließend auf **Schnellerfassung**.

    ![Dialogfeld "Schnellerfassung"](./media/storage-nodejs-how-to-use-blob-storage/quick-storage.png)

4.  Geben Sie im Feld "URL" einen Unterdomänennamen ein, der im URI für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Wert wird der Hostname im URI, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.

5.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Speicher befinden soll. Wenn Sie Speicher aus Ihrer Windows Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie auch Ihre Anwendung bereitstellen.

6.  Klicken Sie auf **Speicherkonto erstellen**.

Erstellen einer Node.js-Anwendung
---------------------------------

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](/de-de/develop/nodejs/tutorials/create-a-website-(mac)/), [Node.js-Cloud-Dienst]({localLink:2221} "Node.js-Webanwendung") (mit der Windows PowerShell), oder [Website mit WebMatrix](/de-de/develop/nodejs/tutorials/web-site-with-webmatrix/).

Konfigurieren der Anwendung für den Speicherzugriff
---------------------------------------------------

Um Azure-Speicher verwenden zu können, müssen Sie das Node.js-Paket azure herunterladen und verwenden. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den REST-Speicherdiensten kommunizieren.

### Verwenden von Node-Paket-Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure** in das Befehlsfenster ein. Die Ausgabe dieses Befehls sollte wie folgt aussehen:

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Sie können den Befehl **ls** manuell aufrufen, um sich davon zu überzeugen, dass der Ordner **node\_modules** erstellt wurde. In diesem Ordner finden Sie ein **azure**-Paket, das die für den Speicherzugriff benötigten Bibliotheken enthält.

### Importieren des Pakets

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen, in der Sie den Speicher nutzen möchten:

    var azure = require('azure');

Einrichten einer Azure-Speicherverbindung
-----------------------------------------

Das Azure-Modul entnimmt den Umgebungsvariablen AZURE\_STORAGE\_ACCOUNT und AZURE\_STORAGE\_ACCESS\_KEY die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Speicherkonto benötigt werden. Falls diese Umgebungsvariablen nicht gesetzt sind, müssen Sie die Kontoinformationen beim Aufruf von **createBlobService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen in einer Konfigurationsdatei für einen Azure-Cloud-Dienst finden Sie unter [Node.js-Clouddienst mit Speicher](/de-de/develop/nodejs/tutorials/web-app-with-storage/).

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für einen Azure-Cloud-Dienst finden Sie unter [Node.js-Webanwendung mit Speicher](/de-de/develop/nodejs/tutorials/web-site-with-storage/)

Vorgehensweise: Erstellen eines Containers
------------------------------------------

Mit dem **BlobService**-Objekt können Sie auf Container und Blobs zugreifen. Der folgende Code erstellt ein **BlobService**-Objekt. Fügen Sie die folgende Zeile am Anfang von **server.js** hinzu:

    var blobService = azure.createBlobService();

Alle Blobs befinden sich in einem Container. Der Aufruf von **createContainerIfNotExists** im **BlobService**-Objekt gibt den angegebenen Container zurück, falls dieser existiert, oder erstellt andernfalls einen neuen Container mit dem angegebenen Namen. Standardmäßig ist der neue Container privat, und Sie müssen Ihren Zugriffsschlüssel angeben, um Blobs aus diesem Container herunterzuladen.

    blobService.createContainerIfNotExists(containerName, function(error){
        if(!error){
            // Container existiert und ist privat
        }
    });

Falls Sie die Dateien in diesem Container öffentlich machen möchten, um diese ohne Zugriffsschlüssel abrufbar zu machen, können Sie die Zugriffsebene des Containers auf **blob** oder **container** setzen. Mit der Zugriffsebene **blob** erlauben Sie anonymen Lesezugriff auf Blobinhalte und Metadaten innerhalb dieses Containers, jedoch nicht auf Container-Metadaten wie z. B. die Auflistung aller Blobs in einem Container. Mit der Zugriffsebene **container** erlauben Sie anonymen Lesezugriff auf Blobinhalte und Metadaten sowie auf Container-Metadaten. Das folgende Beispiel zeigt, wie Sie die Zugriffsebene auf **blob** setzen können:

    blobService.createContainerIfNotExists(containerName
        , {publicAccessLevel : 'blob'}
        , function(error){
            if(!error){
                // Container existiert und ist öffentlich
            }
        });

Alternativ können Sie die Zugriffsebene eines Containers mit **setContainerAcl** angeben. Das folgende Beispiel ändert die Zugriffsebene des Containers:

    blobService.setContainerAcl(containerName
        , 'container'
        , function(error){
            if(!error){
                // Container-Zugriffsebene auf 'container' gesetzt
            }
        });

### Filter

Mit **BlobService** können Sie optionale Filteroperationen auf Operationen ausführen. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit einer Signatur implementieren:

     function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

     function (returnObject, finalCallback, next)

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder andernfalls einfach "finallCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Der folgende Code erstellt ein **BlobService**-Objekt, das den **ExponentialRetryPolicyFilter** verwendet:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobService = azure.createBlobService().withFilter(retryOperations);

Vorgehensweise: Hochladen eines Blobs in einen Container
--------------------------------------------------------

Um Daten als Blob hochzuladen, verwenden Sie eine der Methoden **createBlockBlobFromFile**, **createBlockBlobFromStream** oder **createBlockBlobFromText**. **createBlockBlobFromFile** lädt den Inhalt einer Datei hoch, während **createBlockBlobFromStream** den Inhalt eines Streams hochlädt. **createBlockBlobFromText** lädt den angegebenen Text hoch.

Das folgende Beispiel lädt den Inhalt der Datei **test1.txt** in den Blob 'test1' hoch.

    blobService.createBlockBlobFromFile(containerName
        , 'test1'
        , 'test1.txt'
        , function(error){
            if(!error){
                // Datei wurde hochgeladen
            }
        });

Vorgehensweise: Auflisten der Blobs in einem Container
------------------------------------------------------

Um alle Blobs in einem Container aufzulisten, verwenden Sie die Methode **listBlobs** mit einer **for**-Schleife, um die Namen der Blobs in einem Container auszugeben. Der folgende Code gibt den **Namen** der einzelnen Blobs in einem Container in der Konsole aus.

    blobService.listBlobs(containerName, function(error, blobs){
        if(!error){
            for(var index in blobs){
                console.log(blobs[index].name);
            }
        }
    });

Vorgehensweise: Herunterladen von Blobs
---------------------------------------

Um Daten aus einem Blob herunterzuladen, verwenden Sie **getBlobToFile**, **getBlobToStream** oder **getBlobToText**. Das folgende Beispiel verwendet **getBlobToStream**, um den Inhalt des **test1**-Blobs herunterzuladen und mithilfe eines Streams in der **output.txt**-Datei zu speichern:

    var fs=require('fs');
    blobService.getBlobToStream(containerName
        , 'test1'
        , fs.createWriteStream('output.txt')
        , function(error){
            if(!error){
                // Blob in Stream geschrieben
            }
        });

Vorgehensweise: Löschen eines Blobs
-----------------------------------

Um einen Blob zu löschen, rufen Sie **deleteBlob** auf. Das folgende Beispiel löscht den Blob mit dem Namen 'blob1'.

    blobService.deleteBlob(containerName
        , 'blob1'
        , function(error){
            if(!error){
                // Blob wurde gelöscht
            }
        });

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen von Blobspeicher vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx).
-   Besuchen Sie den [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/).
-   Besuchen Sie das [Azure SDK für Node](https://github.com/WindowsAzure/azure-sdk-for-node)-Repository auf GitHub.

