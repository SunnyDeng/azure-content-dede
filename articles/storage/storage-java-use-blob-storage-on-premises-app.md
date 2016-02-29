<properties
	pageTitle="Lokale Anwendungen mit Blob-Speicher (Java) | Microsoft Azure"
	description="Lernen Sie, wie Sie eine Konsolenanwendung erstellen, die ein Bild in Azure hochlädt und anschließend das Bild in Ihrem Browser anzeigt. Die Codebeispiele wurden in Java geschrieben."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="rmcmurray"/>

# Lokale Anwendungen mit Blob-Speicher

## Übersicht

Das folgende Beispiel zeigt, wie Sie den Azure-Speicher zur Speicherung von Bildern in Azure verwenden können. Der Code in diesem Artikel implementiert eine Konsolenanwendung, die ein Bild in Azure hochlädt und anschließend eine HTML-Datei erstellt, die das Bild in Ihrem Browser anzeigt.

## Voraussetzungen

- Java Developer Kit (JDK), Version 1.6 oder höher ist installiert.
- Das Azure SDK ist installiert.
- Die JAR-Datei der Azure-Bibliotheken für Java und alle sonstigen JAR-Abhängigkeiten sind installiert und im Buildpfad Ihres Java-Compilers eingebunden. Weitere Informationen zur Installation der Azure-Bibliotheken für Java finden Sie auf der [Downloadseite des Azure SDK für Java][].
- Ein Azure-Speicherkonto wurde eingerichtet. Der Code in diesem Artikel verwendet Kontonamen und Kontoschlüssel des Speicherkontos. Unter [Erstellen eines Speicherkontos] finden Sie Informationen zum Einrichten von Speicherkonten, und unter [Verwalten von Speicherkonten][] erfahren Sie, wie Sie Ihren Kontoschlüssel ermitteln können.
- Sie haben eine lokale Bilddatei unter dem Pfad c:\\myimages\\image1.jpg erstellt. Alternativ können Sie den **FileInputStream**-Konstruktor im Beispiel verändern, um einen anderen Pfad bzw. Dateinamen zu verwenden.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Verwenden von Azure Blob-Speicher für Dateiuploads

Hier finden Sie eine Schritt-für-Schritt-Anleitung. Falls Sie diese überspringen möchten, finden Sie den vollständigen Code weiter unten in diesem Artikel.

Importieren Sie zunächst die Core-Klassen für den Azure-Speicher, die Azure Blob-Clientklassen, die Java IO-Klassen und die **URISyntaxException**-Klasse.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

Deklarieren Sie eine Klasse mit dem Namen **StorageSample** und fügen Sie eine offene geschweifte Klammer an, **{**.

    public class StorageSample {

Deklarieren Sie in der Klasse **StorageSample** eine String-Variable für das Standard-Endpunktprotokoll, den Namen Ihres Speicherkontos und Ihren Speicherzugriffsschlüssel gemäß Ihres Azure-Speicherkontos. Ersetzen Sie die Platzhalterwerte **your\_account\_name** und **your\_account\_key** durch Ihren Kontonamen und Ihren Kontoschlüssel.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

Fügen Sie die **main**-Deklaration hinzu, öffnen Sie einen **try**-Block und fügen Sie die notwendigen geschweiften Klammern an, **{**.

    public static void main(String[] args)
    {
        try
        {

Deklarieren die folgenden Variablen (die Beschreibung gibt deren Verwendungszweck an):

-   **CloudStorageAccount**: Wird zur Initialisierung des Kontoobjekts mit Ihrem Azure-Speicherkontonamen und -schlüssel und zur Erstellung des Blob-Clientobjekts verwendet.
-   **CloudBlobClient**: Dient zum Zugriff auf den Blob-Dienst.
-   **CloudBlobContainer**: Dient zum Erstellen eines Blob-Containers, zum Auflisten der Blobs im Container und zum Löschen des Containers.
-   **CloudBlockBlob**: Ermöglicht das Hochladen einer lokalen Imagedatei in den Container.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Weisen Sie der **account**-Variable einen Wert zu.

    account = CloudStorageAccount.parse(storageConnectionString);

Weisen Sie der **serviceClient**-Variable einen Wert zu.

    serviceClient = account.createCloudBlobClient();

Weisen Sie der **container**-Variable einen Wert zu. Wir rufen einen Verweis auf einen Container mit dem Namen **gettingstarted** ab.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Erstellen Sie den Container. Diese Methode erstellt den Container, falls dieser nicht existiert (und gibt **true** zurück). Falls der Container existiert, wird **false** zurückgegeben. Eine Alternative zu **createIfNotExist** ist die **create**-Methode (gibt einen Fehler zurück, falls der Container bereits existiert).

    container.createIfNotExists();

Anonymen Zugiff für den Container festlegen.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Abrufen eines Verweises auf den Blockblob, der den Blob im Azure-Speicher repräsentiert.

    blob = container.getBlockBlobReference("image1.jpg");

Verwenden Sie den **File**-Konstruktor, um einen Verweis auf die lokale Datei abzurufen, die Sie hochladen möchten. Stellen Sie sicher, dass die Datei existiert, bevor Sie den Code ausführen.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Laden Sie die lokale Datei hoch, indem Sie die **CloudBlockBlob.upload**-Methode aufrufen. Der erste Parameter der **CloudBlockBlob.upload**-Methode ist ein **FileInputStream**-Objekt, das die lokale Datei repräsentiert, die in den Azure-Speicher hochgeladen wird. Der zweite Parameter ist die Dateigröße in Byte.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Rufen Sie eine Hilfsfunktion namens **MakeHTMLPage** auf, um eine einfache HTML-Seite zu erstellen, die ein **&lt;image&gt;**-Element enthält und für deren Quelle das nun in Ihrem Azure-Speicherkonto vorhandene Blob festgelegt ist. Der Code für **MakeHTMLPage** wird später in diesem Artikel ebenfalls besprochen.

    MakeHTMLPage(container);

Statusnachricht und Informationen über die erstellte HTML-Seite ausgeben.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Schließen Sie den **try**-Block mit einer geschweiften Klammer: **}**.

Behandeln Sie die folgenden Ausnahmen:

-   **FileNotFoundException**: Kann vom **FileInputStream**- oder **FileOutputStream**-Konstruktor ausgelöst werden.
-   **StorageException**: Kann von der Clientbibliothek für den Azure-Speicher ausgelöst werden.
-   **URISyntaxException**: Kann von der **ListBlobItem.getUri**-Methode ausgelöst werden.
-   **Exception**: Allgemeine Behandlung von Ausnahmen

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Schließen Sie **main** mit einer geschweiften Klammer: **}**.

Erstellen Sie eine Methode mit dem Namen **MakeHTMLPage**, um eine einfache HTML-Seite zu generieren. Diese Methode hat einen Parameter vom Typ **CloudBlobContainer**, der zum Iterieren über die Liste der hochgeladenen Blobs verwendet wird. Diese Methode wirft Ausnahmen vom Typ **FileNotFoundException**, die aus dem **FileOutputStream**-Konstruktor stammen, und **URISyntaxException**, die von der **ListBlobItem.getUri**-Methode geworfen werden kann. Fügen Sie eine offene geschweifte Klammer an, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Erstellen Sie eine lokale Datei mit dem Namen **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Schreiben Sie in die lokale Datei, und fügen Sie dabei die Elemente **&lt;html&gt;**, **&lt;header&gt;** und **&lt;body&gt;** hinzu.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Durchlaufen Sie die Liste der hochgeladenen Blobs. Erstellen Sie für jedes Blob ein **&lt;img&gt;**-Element auf der HTML-Seite, dessen **src**-Attribut an den URI des Blobs gesendet wird, der in Ihrem Azure-Speicherkonto liegt. In diesem Beispiel haben Sie zwar nur ein Bild hinzugefügt, aber dieser Code würde alle weiteren hochgeladenen Bilder ebenfalls durchlaufen.

Zur Vereinfachung geht dieses Beispiel davon aus, dass es sich bei allen hochgeladenen Blobs um Bilder handelt. Falls Sie Blobs hochgeladen haben, die keine Bilder sind, oder Seitenblobs anstelle von Blockblobs, müssen Sie Ihren Code entsprechend anpassen.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Schließen Sie das Element **&lt;body&gt;** und das Element **&lt;html&gt;**.

    stream.println("</body>");
    stream.println("</html>");

Schließen Sie die lokale Datei.

    stream.close();

Schließen Sie **MakeHTMLPage** mit einer geschweiften Klammer: **}**.

Schließen Sie **StorageSample** mit einer geschweiften Klammer: **}**.

Es folgt der vollständige Code für dieses Beispiel. Vergessen Sie nicht, die Platzhalterwerte **your\_account\_name** und **your\_account\_key** durch Ihren Kontonamen und Ihren Kontoschlüssel zu ersetzen.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

Der Beispielcode lädt nicht nur eine lokale Datei in Ihren Azure-Speicher hoch, sondern erstellt auch eine lokale Datei mit dem Namen namedindex.html, die Sie in Ihrem Browser öffnen können, um Ihr hochgeladenes Bild anzuzeigen.

Der Code enthält Ihren Kontonamen und Kontoschlüssel. Stellen Sie daher sicher, dass der Code an einem sicheren Ort liegt.

## So löschen Sie einen Container

Da der Speicher kostenpflichtig ist, sollten Sie den **gettingstarted**-Container löschen, nachdem Sie dieses Beispiel abgeschlossen haben. Verwenden Sie zum Löschen eines Containers die Methode **CloudBlobContainer.delete**.

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Um die **CloudBlobContainer.delete**-Methode aufzurufen, müssen Sie die **CloudStorageAccount**-, **ClodBlobClient**- und **CloudBlobContainer**-Objekte auf dieselbe Weise initialisieren wie für die **createIfNotExist**-Methode gezeigt wurde. Das folgende Beispiel löscht den Container mit dem Namen **gettingstarted**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Eine Übersicht über andere Blobspeicher-Klassen und Methoden finden Sie unter [Verwenden des Blob-Speicherdiensts in Python].

## Nächste Schritte

Unter den folgenden Links erhalten Sie weitere Informationen zu komplexeren Speicheraufgaben:

- [Azure Storage-SDK für Java][]
- [Referenz für Azure Storage-Client-SDKs][]
- [Azure Storage-REST-API][]
- [Azure Storage-Teamblog][]

  [Downloadseite des Azure SDK für Java]: http://go.microsoft.com/fwlink/?LinkID=525671
  [Erstellen eines Speicherkontos]: storage-create-storage-account.md#create-a-storage-account
  [Verwalten von Speicherkonten]: storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys
  [Verwenden des Blob-Speicherdiensts in Python]: storage-java-how-to-use-blob-storage.md
  [Azure Storage-SDK für Java]: https://github.com/azure/azure-storage-java
  [Referenz für Azure Storage-Client-SDKs]: http://dl.windowsazure.com/storage/javadoc/
  [Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/

<!---HONumber=AcomDC_0218_2016-->