<properties 
	pageTitle="Lokale Anwendungen mit Blob-Speicher (Java) | Microsoft Azure" 
	description="Lernen Sie, wie Sie eine Konsolenanwendung erstellen, die ein Bild in Azure hochlädt und anschließend das Bild in Ihrem Browser anzeigt. Die Codebeispiele wurden in Java geschrieben." 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Lokale Anwendungen mit Blob-Speicher

Das folgende Beispiel zeigt, wie Sie den Azure-Speicher zur Speicherung von Bildern in Azure verwenden können. Der folgende Code implementiert eine Konsolenanwendung, die ein Bild in Azure hochlädt
und anschließend eine HTML-Datei erstellt, die das Bild in Ihrem Browser anzeigt.

## Inhaltsverzeichnis

-   [Voraussetzungen][]
-   [Verwenden von Azure-Blobspeicher für Dateiuploads][]
-   [So löschen Sie einen Container][]

## <a name="bkmk_prerequisites"> </a>Voraussetzungen

1.  Java Developer Kit (JDK), Version 1.6 oder höher.
2.  Das Azure SDK ist installiert.
3.  Die JAR-Datei der Azure-Bibliotheken für Java und alle sonstigen JAR-Abhängigkeiten sind installiert und im Buildpfad Ihres Java-Compilers eingebunden. Weitere Informationen zur Installation der Azure-Bibliotheken für Java finden Sie auf der [Downloadseite des Azure-SDK für Java].
4.  Ein Azure-Speicherkonto wurde eingerichtet. Der folgende Code verwendet Kontonamen und Kontoschlüssel des Speicherkontos. Unter [Vorgehensweise: Erstellen eines Speicherkontos] finden Sie Informationen zum Einrichten von Speicherkonten, und unter [Vorgehensweise: Verwalten von Speicherkonten] erfahren Sie, wie Sie Ihren Kontoschlüssel ermitteln können.
5.  Sie haben eine lokale Bilddatei unter dem Pfad c:\\myimages\\image1.jpg erstellt. Alternativ können Sie den **FileInputStream**-Konstruktor im Beispiel verändern, um einen anderen Pfad bzw. Dateinamen zu verwenden.

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <a name="bkmk_uploadfile"> </a>Verwenden von Azure Blob-Speicher für Dateiuploads

Hier finden Sie eine Schritt-für-Schritt-Anleitung. Falls Sie diese überspringen möchten, finden Sie den vollständigen Code weiter unten in diesem Artikel.

Importieren Sie zunächst die Core-Klassen für den Azure-Speicher, die Azure Blobclientklassen, die Java IO-Klassen und die **URISyntaxException**-Klasse:

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

Deklarieren Sie eine Klasse mit dem Namen **StorageSample** und fügen Sie eine offene geschweifte Klammer an,
**{**.

    public class StorageSample {

Deklarieren Sie in der Klasse **StorageSample** eine Zeichenfolgenvariable für das Standard-Endpunktprotokoll, den Namen Ihres Speicherkontos und Ihren Speicherzugriffsschlüssel gemäß Ihrem Azure-Speicherkonto. Ersetzen Sie die Platzhalterwerte **your\_account\_name** und
**your\_account\_key** durch Ihren Kontonamen und Ihren Kontoschlüssel.

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

-   **CloudStorageAccount**: Wird zur Initialisierung des Konto-Objekts mit Ihrem Azure-Speicherkontonamen und -Schlüssel und zur Erstellung des Blob-Clientobjekts verwendet.
-   **CloudBlobClient**: Dient zum Zugriff auf den Blob-Dienst.
-   **CloudBlobContainer**: Dient zur Erstellung eines Blob-Containers, zum Auflisten der Blobs im Container und zum Löschen des Containers.
-   **CloudBlockBlob**: Ermöglicht den Upload einer lokalen Datei in den Container.

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

Erstellen Sie den Container. Diese Methode erstellt den Container, falls dieser nicht existiert (und gibt **true** zurück). Falls der Container existiert, wird
**false** zurückgegeben. Eine Alternative zu **createIfNotExist** ist die **create**-Methode (gibt einen Fehler zurück, falls der Container bereits existiert).

    container.createIfNotExist();

Anonymen Zugiff für den Container festlegen.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
     containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Abrufen eines Verweises auf den Blockblob, der den Blob im Azure-Speicher repräsentiert
.

    blob = container.getBlockBlobReference("image1.jpg");

Verwenden Sie den **File**-Konstruktor, um einen Verweis auf die lokale Datei abzurufen, die Sie hochladen möchten. (Stellen Sie sicher, dass die Datei existiert, bevor Sie den Code ausführen.)

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Laden Sie die lokale Datei hoch, indem Sie die **CloudBlockBlob.upload**-Methode aufrufen. Der erste Parameter für die **CloudBlockBlob.upload**-Methode ist ein
**FileInputStream**-Objekt, das die lokale Datei darstellt, die in den Azure-Speicher hochgeladen wird. Der zweite Parameter ist die Dateigröße in Byte.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Rufen Sie eine Hilfsfunktion namens **MakeHTMLPage** auf, um eine einfache HTML-Seite zu erstellen, die ein **&lt;image&gt;**-Element enthält, das auf den nun in Ihrem Azure-Speicherkonto vorhandenen Blob zeigt. (Der Code für
**MakeHTMLPage** wird weiter unten in diesem Artikel ebenfalls erläutert.)

    MakeHTMLPage(container);

Statusnachricht und Informationen über die erstellte HTML-Seite ausgeben.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Schließen Sie den **try**-Block mit einer geschweiften Klammer: **}**

Behandeln Sie die folgenden Ausnahmen:

-   **FileNotFoundException**: Kann vom **FileInputStream** - oder vom **FileOutputStream**-Konstruktor stammen.
-   **StorageException**: Kann von der Clientbibliothek für den Azure-Speicher geworfen werden.
-   **URISyntaxException**: Kann von der **ListBlobItem.getUri** -Methode stammen.
-   **Exception**: Allgemeine Fehlerbehandlung.

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

Schließen Sie **main** mit einer geschweiften Klammer: **}**

Erstellen Sie eine Methode mit dem Namen **MakeHTMLPage**, um eine einfache HTML-Seite zu generieren. Diese Methode hat einen Parameter vom Typ **CloudBlobContainer**, der zum Iterieren über die Liste der hochgeladenen Blobs verwendet wird. Diese Methode wirft Ausnahmen vom Typ **FileNotFoundException**, die aus dem **FileOutputStream**-Konstruktor stammen, und **URISyntaxException**, die von der **ListBlobItem.getUri**-Methode stammen kann. Fügen Sie eine offene geschweifte Klammer an, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Erstellen Sie eine lokale Datei mit dem Namen **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Schreiben Sie die Elemente **&lt;html&gt;**, **&lt;header&gt;** und
**&lt;body&gt;** in die lokale Datei.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Durchlaufen Sie die Liste der hochgeladenen Blobs. Erstellen Sie für jedes Blob ein **&lt;img&gt;**-Element in der HTML-Seite, dessen **src**-Attribut an den URI des Blobs gesendet wird, der in Ihrem Azure-Speicherkonto liegt.
In diesem Beispiel haben Sie zwar nur ein Bild hinzugefügt, aber dieser Code würde alle weiteren hochgeladenen Bilder ebenfalls durchlaufen.

Zur Vereinfachung geht dieses Beispiel davon aus, dass es sich bei allen hochgeladenen Blobs um Bilder handelt. Falls Sie Blobs hochgeladen haben, die keine Bilder sind, oder Seitenblobs anstelle von Blockblobs, müssen Sie Ihren Code entsprechend anpassen.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Schließen Sie das **&lt;body&gt;**- und das **&lt;html&gt;**-Element.

    stream.println("</body>");
    stream.println("</html>");

Schließen Sie die lokale Datei.

    stream.close();

Schließen Sie **MakeHTMLPage** mit einer geschweiften Klammer: **}**

Schließen Sie **StorageSample** mit einer geschweiften Klammer: **}**

Es folgt der vollständige Code für dieses Beispiel. Ersetzen Sie die Platzhalterwerte **your\_account\_name** und
**your\_account\_key** durch Ihren Kontonamen und Ihren Kontoschlüssel.

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
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

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExist();
                
                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");
                File fileReference = new File ("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            }
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

## <a name="bkmk_deletecontainer"> </a>So löschen Sie einen Container

Der Speicher wird Ihnen in Rechnung gestellt. Daher möchten Sie den
**gettingstarted**-Container möglicherweise löschen, nachdem Sie dieses Beispiel ausprobiert haben. Verwenden Sie zum Löschen eines Containers die Methode **CloudBlobContainer.delete**:

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Zum Aufrufen der **CloudBlobContainer.delete**-Methode ist der Prozess der Initialisierung von **CloudStorageAccount**-, **ClodBlobClient**- und
**CloudBlobContainer**-Objekten mit dem für die
**createIfNotExist**-Methode gezeigten Prozess identisch. Das folgende Beispiel löscht den Container mit dem Namen **gettingstarted**.

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

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

Einen Überblick über andere Blob-Speicherklassen und -methoden finden Sie unter [Verwenden
des Blob-Speicherdiensts aus Java].

  [Voraussetzungen]: #bkmk_prerequisites
  [Verwenden von Azure Blob-Speicher für Dateiuploads]: #bkmk_uploadfile
  [So löschen Sie einen Container]: #bkmk_deletecontainer
  [Herunterladen des Azure SDK für Java]: http://www.windowsazure.com/de-de/develop/java/
  [Vorgehensweise: Erstellen eines Speicherkontos]: http://www.windowsazure.com/de-de/manage/services/storage/how-to-create-a-storage-account/
  [Vorgehensweise: Verwalten von Speicherkonten]: http://www.windowsazure.com/de-de/manage/services/storage/how-to-manage-a-storage-account/
  [Verwenden des Blobspeicherdiensts aus Java]: http://www.windowsazure.com/de-de/develop/java/how-to-guides/blob-storage/
<!--HONumber=42-->
