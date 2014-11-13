<properties urlDisplayName="Blob Service" pageTitle="Verwenden des Blob-Speichers (Ruby) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Ruby" description="Erfahren Sie, wie Sie den Azure-Blobdienst zum Hochladen, Herunterladen, Auflisten und L&ouml;schen von Blob-Inhalten verwenden. Die Beispiele wurden in Ruby geschrieben." metaCanonical="" services="storage" documentationCenter="Ruby" title="Verwenden des Blob-Diensts von Ruby" authors="guayan" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Verwenden des Blob-Diensts von Ruby

Dieses Handbuch demonstriert Ihnen allgemeine Szenarien unter Verwendung des
Blob-Diensts in Azure. Die Beispiele wurden mit der Ruby Azure-API erstellt.
Zu den behandelten Szenarien gehören das **Hochladen, Auflisten, Herunterladen** und **Löschen** von Blobs.
Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

-   [Was ist der Blob-Dienst?][Was ist der Blob-Dienst?]
-   [Konzepte][Konzepte]
-   [Erstellen eines Azure-Speicherkontos][Erstellen eines Azure-Speicherkontos]
-   [Erstellen einer Ruby-Anwendung][Erstellen einer Ruby-Anwendung]
-   [Konfigurieren der Anwendung für den Speicherzugriff][Konfigurieren der Anwendung für den Speicherzugriff]
-   [Einrichten einer Azure-Speicherverbindung][Einrichten einer Azure-Speicherverbindung]
-   [Gewusst wie: Erstellen eines Containers][Gewusst wie: Erstellen eines Containers]
-   [Gewusst wie: Hochladen eines Blobs in einen Container][Gewusst wie: Hochladen eines Blobs in einen Container]
-   [Gewusst wie: Auflisten der Blobs in einem Container][Gewusst wie: Auflisten der Blobs in einem Container]
-   [Gewusst wie: Herunterladen von Blobs][Gewusst wie: Herunterladen von Blobs]
-   [Gewusst wie: Löschen eines Blobs][Gewusst wie: Löschen eines Blobs]
-   [Nächste Schritte][1]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <span id="CreateAccount"></span></a>Erstellen eines Azure-Speicherkontos

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="CreateRubyApp"></span></a>Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie im Thema zum [Erstellen einer Ruby-Anwendung in Azure][Erstellen einer Ruby-Anwendung in Azure].

## <span id="ConfigAccessStorage"></span></a>Konfigurieren der Anwendung für den Speicherzugriff

Um den Azure-Speicher zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2.  Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

    require "azure"

## <span id="SetupStorageConnection"></span></a>Einrichten einer Azure-Speicherverbindung

Das Azure-Modul liest die Umgebungsvariablen **AZURE\_STORAGE\_ACCOUNT** und **AZURE\_STORAGE\_ACCESS\_KEY**
 nach Informationen aus, die erforderlich sind, um eine Verbindung zum Azure-Speicherkonto herzustellen. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::BlobService** mit dem folgenden Code angeben:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

So rufen Sie diese Werte ab:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.
3.  Klicken Sie unter im Navigationsbereich auf **MANAGE KEYS**.
4.  Im eingeblendeten Dialog wird der Name des Speicherkontos, der primäre Zugriffsschlüssel und der sekundäre Zugriffsschlüssel angezeigt. Als Zugriffsschlüssel können Sie den primären oder sekundären auswählen.

## <span id="CreateContainer"></span></a>Gewusst wie: Erstellen eines Containers

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

## <span id="UploadBlob"></span></a>Gewusst wie: Hochladen eines Blobs in einen Container

Um Inhalte in einen Blob hochzuladen, verwenden Sie die **create\_block\_blob()**-Methode, um den Blob zu erstellen, und verwenden Sie eine Datei oder Zeichenfolge als Inhalt des Blobs.

Durch den folgenden Code wird die Datei **test.png** als neuer Blob namens "image-blob" in den Container hochgeladen.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <span id="ListBlobs"></span></a>Gewusst wie: Auflisten der Blobs in einem Container

Verwenden Sie die Methode **list\_containers()** zum Auflisten der Container.
Um die Blobs in einem Container aufzulisten, verwenden Sie die **list\_blobs()**-Methode.

Auf diese Weise werden die URLs aller Blobs in allen Containern des Kontos ausgegeben.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <span id="DownloadBlobs"></span></a>Gewusst wie: Herunterladen von Blobs

Um Blobs herunterzuladen, verwenden Sie die **get\_blob()**-Methode zum Abrufen des Inhalts.

Das folgende Beispiel demonstriert das Verwenden von **get\_blob()** zum Herunterladen der Inhalte von "image-blob" und zum Schreiben in eine lokale Datei.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <span id="DeleteBlob"></span></a>Gewusst wie: Löschen eines Blobs

Verwenden Sie schließlich die **delete\_blob()**-Methode, um einen Blob zu löschen. Das folgende Beispiel demonstriert das Löschen eines Blobs.

    azure_blob_service.delete_blob(container.name, "image-blob")

## <span id="NextSteps"></span></a>Nächste Schritte

Nachdem Sie jetzt die Grundlagen des Blob-Speichers erlernt haben, folgen Sie diesen Links, um Informationen zum Ausführen komplexerer Speicheraufgaben zu erhalten.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure][Speichern und Zugreifen auf Daten in Azure]
-   Besuchen Sie den [Blog des Azure-Speicherteams][Blog des Azure-Speicherteams]
-   Besuchen Sie das [Azure SDK for Ruby][Azure SDK for Ruby]-Repository auf GitHub

  [Nächste Schritte]: #next-steps
  [Was ist der Blob-Dienst?]: #what-is
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #CreateAccount
  [Erstellen einer Ruby-Anwendung]: #CreateRubyApp
  [Konfigurieren der Anwendung für den Speicherzugriff]: #ConfigAccessStorage
  [Einrichten einer Azure-Speicherverbindung]: #SetupStorageConnection
  [Gewusst wie: Erstellen eines Containers]: #CreateContainer
  [Gewusst wie: Hochladen eines Blobs in einen Container]: #UploadBlob
  [Gewusst wie: Auflisten der Blobs in einem Container]: #ListBlobs
  [Gewusst wie: Herunterladen von Blobs]: #DownloadBlobs
  [Gewusst wie: Löschen eines Blobs]: #DeleteBlob
  [1]: #NextSteps
  [Erstellen einer Ruby-Anwendung in Azure]: /de-de/develop/ruby/tutorials/web-app-with-linux-vm/
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
  [Blog des Azure-Speicherteams]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure SDK for Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
