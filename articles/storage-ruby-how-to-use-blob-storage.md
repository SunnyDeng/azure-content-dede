<properties 
	pageTitle="Verwenden des Blob-Speichers mit Ruby | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Azure-Blob-Dienst zum Hochladen, Herunterladen, Auflisten und Löschen von Blob-Inhalten verwenden. Die Beispiele wurden in Ruby geschrieben." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Verwenden des Blob-Speichers mit Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem
Azure-Blob-Dienst demonstriert. Die Beispiele wurden mit der Ruby-API erstellt.
Die behandelten Szenarien umfassen das **Hochladen, Auflisten, Herunterladen** und **Löschen** von Blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie 
unter [Erstellen einer Ruby-Anwendung in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Konfigurieren der Anwendung für den Speicherzugriff

Um den Azure-Speicher zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

	require "azure"

## Einrichten einer Azure-Speicherverbindung

Das Azure-Modul entnimmt den Umgebungsvariablen **AZURE_STORAGE_ACCOUNT** und **AZURE_STORAGE_ACCESS_KEY** 
die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Speicherkonto benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::BlobService** mit dem folgenden Code angeben:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


So rufen Sie diese Werte ab:

1. Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
2. Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.
3. Klicken Sie unten im Navigationsbereich auf **SCHLÜSSEL VERWALTEN**.
4. Im eingeblendeten Dialog werden der Name des Speicherkontos, der primäre Zugriffsschlüssel und der sekundäre Zugriffsschlüssel angezeigt. Verwenden Sie den primären oder sekundären Zugriffsschlüssel.

## Vorgehensweise: Erstellen eines Containers

Mit dem **Azure::BlobService**-Objekt können Sie mit Containern und Blobs arbeiten. Verwenden Sie die **create_container()**-Methode, um einen Container zu erstellen.

Im folgenden Beispiel wird ein Container erstellt oder ggf. ein Fehler ausgegeben.

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Wenn Sie die Dateien im Container öffentlich machen möchten, können Sie die Berechtigungen des Containers festlegen. 

Sie können einfach den <strong>create_container()</strong>-Aufruf ändern, um die **:public_access_level**-Option zu übergeben:

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


Gültige Werte für die **:public_access_level**-Option sind:

* **blob:** Gibt den vollständigen öffentlichen Lesezugriff auf Container- und Blob-Daten an. Mittels einer anonymen Anforderung kann ein Client die Blobs innerhalb eines Containers aufzählen, aber nicht die Container im Speicherkonto.

* **container:** Gibt den öffentlichen Lesezugriff auf Blobs an. Mittels einer anonymen Anforderung können die Blob-Daten im betreffenden Container gelesen werden, die Containerdaten sind jedoch nicht verfügbar. Ein Client kann nicht mittels einer anonymen Anforderung die Blobs innerhalb des Containers aufzählen.

Sie können aber auch die öffentliche Zugriffsstufe eines Container mithilfe der **set_container_acl()**-Methode ändern, um die öffentliche Zugriffsstufe.
 
Im folgenden Beispiel wird die öffentliche Zugriffsstufe auf **container** geändert:

	azure_blob_service.set_container_acl('test-container', "container")

## Vorgehensweise: Hochladen eines Blobs in einen Container

Um Inhalte in einen Blob hochzuladen, verwenden Sie die **create_block_blob()**-Methode, um das Blob zu erstellen, und verwenden Sie eine Datei oder Zeichenfolge als Inhalt des Blobs. 

Durch den folgenden Code wird die Datei **test.png** als neues Blob namens "image-blob" in den Container hochgeladen.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Vorgehensweise: Auflisten der Blobs in einem Container

Verwenden Sie die **list_containers()**-Methode, um die Container aufzulisten. 
Verwenden Sie die **list_blobs()**-Methode, um die Blobs innerhalb eines Containers aufzulisten. 

Auf diese Weise werden die URLs aller Blobs in allen Containern des Kontos ausgegeben.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Vorgehensweise: Herunterladen von Blobs

Um Blobs herunterzuladen, verwenden Sie die **get_blob()**-Methode zum Abrufen des Inhalts. 

Das folgende Beispiel demonstriert das Verwenden von **get_blob()** zum Herunterladen der Inhalte von "image-blob" und zum Schreiben in eine lokale Datei.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Vorgehensweise: Löschen eines Blobs
Verwenden Sie schließlich die **delete_blob()**-Methode, um einen Blob zu löschen. Das folgende Beispiel demonstriert das Löschen eines Blobs.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Blob-Speichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- Weitere Informationen finden Sie in der MSDN-Referenz: [Azure Storage](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Besuchen Sie den [Azure Storage-Teamblog](http://blogs.msdn.com/b/windowsazurestorage/).
- Besuchen Sie das [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby)-Repository auf GitHub.

<!--HONumber=49-->