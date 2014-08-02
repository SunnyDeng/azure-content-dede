<properties linkid="dev-ruby-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Ruby) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Ruby" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Blob Service from Ruby" authors="guayan" solutions="" manager="" editor="" />

Verwenden des Blob-Diensts von Ruby
===================================

Dieses Handbuch demonstriert Ihnen allgemeine Szenarien unter Verwendung des Blob-Diensts in Azure. Die Beispiele wurden mit der Ruby-API erstellt. Die behandelten Szenarien umfassen **Hochladen, Auflisten, Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

-   [Was ist der Blob-Dienst?](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#CreateAccount)
-   [Erstellen einer Ruby-Anwendung](#CreateRubyApp)
-   [Konfigurieren der Anwendung für den Speicherzugriff](#ConfigAccessStorage)
-   [Einrichten einer Azure-Speicherverbindung](#SetupStorageConnection)
-   [Gewusst wie: Erstellen eines Containers](#CreateContainer)
-   [Gewusst wie: Hochladen eines Blobs in einen Container](#UploadBlob)
-   [Gewusst wie: Auflisten der Blobs in einem Container](#ListBlobs)
-   [Gewusst wie: Herunterladen von Blobs](#DownloadBlobs)
-   [Gewusst wie: Löschen eines Blobs](#DeleteBlob)
-   [Nächste Schritte](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Erstellen eines Azure-Speicherkontos
------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Erstellen einer Ruby-Anwendung
------------------------------

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie im Thema zum [Erstellen einer Ruby-Anwendung in Azure](/en-us/develop/ruby/tutorials/web-app-with-linux-vm/).

Konfigurieren der Anwendung für den Speicherzugriff
---------------------------------------------------

Um den Azure-Speicher zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2.  Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

    require "azure"

Einrichten einer Azure-Speicherverbindung
-----------------------------------------

Das Azure-Modul liest die Umgebungsvariablen **AZURE\_STORAGE\_ACCOUNT** und **AZURE\_STORAGE\_ACCESS\_KEY** nach Informationen aus, die erforderlich sind, um eine Verbindung zum Azure-Speicherkonto herzustellen. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::BlobService** mit dem folgenden Code angeben:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

So rufen Sie diese Werte ab:

1.  Melden Sie sich auf dem [Windows Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
2.  Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.
3.  Klicken Sie unter im Navigationsbereich auf **MANAGE KEYS**.
4.  Im eingeblendeten Dialog wird der Name des Speicherkontos, der primäre Zugriffsschlüssel und der sekundäre Zugriffsschlüssel angezeigt. Als Zugriffsschlüssel können Sie den primären oder sekundären auswählen.

Gewusst wie: Erstellen eines Containers
---------------------------------------

Mit dem **Azure::BlobService**-Objekt können Sie mit Containern und Blobs arbeiten. Verwenden Sie die **create\_container()**-Methode, um einen Container zu erstellen.

Im folgenden Beispiel wird ein Container erstellt oder ggf. ein Fehler ausgegeben.

    azure_blob_service = Azure::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

Wenn Sie die Dateien im Container öffentlich machen möchten, können Sie die Berechtigungen des Containers festlegen.

Sie können einfach den **create\_container()**-Aufruf ändern, um die **:public\_access\_level**-Option zu übergeben:

    container = azure_blob_service.create_container("test-container", 
      :public_access_level => "<public access level>")

Gültige Werte für die **:public\_access\_level**-Option sind:

-   **blob:** Gibt vollständigen öffentlichen Lesezugriff für Container- und Blob-Daten an. Clients können Blobs innerhalb des Container über eine anonyme Anforderung aufzählen, können aber keine Container innerhalb des Speicherkontos aufzählen.

-   **container:** Gibt öffentlichen Lesezugriff für Blobs an. Blob-Daten innerhalb dieses Containers können über anonyme Anforderungen gelesen werden, Containerdaten sind aber nicht verfügbar. Clients können keine Blobs innerhalb des Containers über anonyme Anforderungen aufzählen.

Sie können aber auch die öffentliche Zugriffsstufe eines Container mithilfe der **set\_container\_acl()**-Methode ändern, um die öffentliche Zugriffsstufe anzugeben.

Im folgenden Beispiel wird die öffentliche Zugriffsstufe auf **container** geändert:

    azure_blob_service.set_container_acl('test-container', "container")

Gewusst wie: Hochladen eines Blobs in einen Container
-----------------------------------------------------

Um Inhalte in einen Blob hochzuladen, verwenden Sie die **create\_block\_blob()**-Methode, um den Blob zu erstellen, und verwenden Sie eine Datei oder Zeichenfolge als Inhalt des Blobs.

Durch den folgenden Code wird die Datei **test.png** als neuer Blob namens "image-blob" in den Container hochgeladen.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

Gewusst wie: Auflisten der Blobs in einem Container
---------------------------------------------------

Verwenden Sie die **list\_containers()**-Methode, um die Container aufzulisten. 
Verwenden Sie die **list\_blobs()**-Methode, um die Blobs innerhalb eines Containers aufzulisten.

Auf diese Weise werden die URLs aller Blobs in allen Containern des Kontos ausgegeben.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

Gewusst wie: Herunterladen von Blobs
------------------------------------

Um Blobs herunterzuladen, verwenden Sie die **get\_blob()**-Methode zum Abrufen des Inhalts.

Das folgende Beispiel demonstriert das Verwenden von **get\_blob()** zum Herunterladen der Inhalte von "image-blob" und zum Schreiben in eine lokale Datei.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

Gewusst wie: Löschen eines Blobs
--------------------------------

Verwenden Sie schließlich die **delete\_blob()**-Methode, um einen Blob zu löschen. Das folgende Beispiel demonstriert das Löschen eines Blobs.

    azure_blob_service.delete_blob(container.name, "image-blob")

Nächste Schritte
----------------

Nachdem Sie jetzt die Grundlagen des Blob-Speichers erlernt haben, folgen Sie diesen Links, um Informationen zum Ausführen komplexerer Speicheraufgaben zu erhalten.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   Besuchen Sie den [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/)
-   Besuchen Sie das [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby)-Repository auf GitHub

