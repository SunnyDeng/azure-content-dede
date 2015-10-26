<properties
	pageTitle="Verwenden von Mobile Services zum Hochladen von Daten in Blobspeicher (Android) | Mobile Services"
	description="Erfahren Sie, wie Sie mithilfe von Mobile Services Bilder in Azure Storage hochladen und von Ihrer Android-App auf die Bilder zugreifen."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="ricksal"/>

# Hochladen von Bildern auf Azure Storage aus einem Android-Gerät

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

In diesem Thema wird gezeigt, wie Sie Ihre Android Azure Mobile Services-App für das Hochladen von Bildern in Azure Storage aktivieren können.

Mobile Services verwendet zur Datenspeicherung eine SQL-Datenbank. Es ist jedoch effizienter, große binäre Objektdaten (BLOB-Daten) im Azure Storage zu speichern. In diesem Lernprogramm aktivieren Sie die Mobile Services-Schnellstart-App, um Bilder mit der Android-Kamera zu machen und diese in Azure Storage hochzuladen.


## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst den Mobile Services-Schnellstart abschließen: [Erste Schritte mit Mobile Services].

Für dieses Lernprogramm ist außerdem Folgendes erforderlich:

+ Ein [Azure Storage-Konto](../storage-create-storage-account.md)
+ Ein Android-Gerät mit einer Kamera

## Funktionsweise der App

Das Hochladen des Fotos umfasst mehrere Schritte:

- Zuerst nehmen Sie ein Foto auf und fügen eine „TodoItem“-Zeile in der SQL-Datenbank hinzu, die neue Metadatenfelder enthält, die von Azure Storage verwendet werden.
- Ein neues Mobile Services SQL-Skript zum **Einfügen** fragt Azure Storage nach einer Shared Access Signature (SAS).
- Dieses Skript gibt die SAS und einen URI für den Blob an den Client zurück.
- Der Client lädt das Foto mithilfe der SAS- und Blob-URI hoch.

Was bedeutet SAS?

Es ist nicht sicher, die für das Hochladen der Daten in den Azure Storage-Dienst erforderlichen Anmeldeinformationen innerhalb der Client-App zu speichern. Stattdessen müssen Sie diese Anmeldeinformationen in Ihrem mobilen Dienst speichern und dazu verwenden, eine Shared Access Signature (SAS) zu erstellen, die die Berechtigung zum Hochladen eines neuen Bildes erteilt. Die SAS, eine Anmeldeinformation mit einer Laufzeit von fünf Minuten, wird durch Mobile Services sicher an die Client-App zurückgegeben. Anschließend nutzt die App diese temporäre Anmeldeinformation zum Hochladen des Bildes. Weitere Informationen finden Sie unter [Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell](storage-dotnet-shared-access-signature-part-1.md).

## Codebeispiel
[Hier](https://github.com/Azure/mobile-services-samples/tree/master/UploadImages) finden Sie den abgeschlossenen Teil des Clientquellcodes dieser App. Damit Sie die App ausführen können, müssen Sie die Mobile Services-Back-End-Teile dieses Lernprogramms ausführen.

## Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]


## Aktualisieren Sie die Schnellstart-App, um Bilder aufzunehmen und hochzuladen.

### Verweisen auf die Azure Storage Android-Clientbibliothek

1. Um zur Bibliothek einen Verweis hinzuzufügen, fügen Sie in der Date **app** > **build.gradle** die folgende Zeile zum Abschnitt `dependencies` hinzu:

		compile 'com.microsoft.azure.android:azure-storage-android:0.6.0@aar'


2. Ändern Sie den Wert `minSdkVersion` auf 15 (die ist für die Kamera-API erforderlich).

3. Drücken Sie auf das Symbol **Sync Project with Gradle Files**.

### Aktualisieren Sie die Manifestdatei für Kamera und Speicherung.

1. Geben Sie an, dass Ihre App eine Kamera benötigt, indem Sie die folgende Zeile zu **AndroidManifest.xml** hinzufügen:

	    <uses-feature android:name="android.hardware.camera"
	        android:required="true" />

2. Geben Sie an, dass Ihre App die Berechtigung benötigt, in einen externen Speicher zu schreiben, indem Sie die folgende Zeile zu **AndroidManifest.xml** hinzufügen:

	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

	Beachten Sie, dass der externe Android-Speicher nicht unbedingt eine SD-Karte sein muss. Weitere Informationen finden Sie unter [Dateien speichern](http://developer.android.com/training/basics/data-storage/files.html).

### Aktualisieren von Ressourcendateien für die neue Benutzeroberfläche

1. Fügen Sie Titel für neue Schaltflächen hinzu, indem Sie Folgendes zur Datei **strings.xml** im Verzeichnis *Werte* hinzufügen:

	    <string name="preview_button_text">Take Photo</string>
	    <string name="upload_button_text">Upload</string>

2. Fügen Sie in der Datei **activity\_to\_do.xml** im Ordner **Res = > Layout** den folgenden Code vor den vorhandenen für die Schaltfläche **Hinzufügen** hinzu.

         <Button
             android:id="@+id/buttonPreview"
             android:layout_width="120dip"
             android:layout_height="wrap_content"
             android:onClick="takePicture"
             android:text="@string/preview_button_text" />

3. Ersetzen Sie das Schaltflächenelement **Hinzufügen** durch den folgenden Code:

         <Button
             android:id="@+id/buttonUpload"
             android:layout_width="100dip"
             android:layout_height="wrap_content"
             android:onClick="uploadPhoto"
             android:text="@string/upload_button_text" />


### Hinzufügen von Code für Fotoaufnahmen

1. Fügen Sie in **ToDoActivity.java** diesen Code zum Erstellen eines **Datei**-Objekts mit einem eindeutigen Namen hinzu.

		// Create a File object for storing the photo
	    private File createImageFile() throws IOException {
	        // Create an image file name
	        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
	        String imageFileName = "JPEG_" + timeStamp + "_";
	        File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
	        File image = File.createTempFile(
	                imageFileName,  /* prefix */
	                ".jpg",         /* suffix */
	                storageDir      /* directory */
	        );
	        return image;
	    }

5. Fügen Sie diesen Code zum Starten der Kamera-App von Android hinzu. Sie können jetzt fotografieren, und wenn Sie mit dem Ergebnis zufrieden sind, drücken Sie **Speichern**, sodass das Bild in der soeben erstellten Datei gespeichert wird.

		// Run an Intent to start up the Android camera
	    static final int REQUEST_TAKE_PHOTO = 1;
	    public Uri mPhotoFileUri = null;
	    public File mPhotoFile = null;

	    public void takePicture(View view) {
	        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	        // Ensure that there's a camera activity to handle the intent
	        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
	            // Create the File where the photo should go
	            try {
	                mPhotoFile = createImageFile();
	            } catch (IOException ex) {
	                // Error occurred while creating the File
	                //
	            }
	            // Continue only if the File was successfully created
	            if (mPhotoFile != null) {
	                mPhotoFileUri = Uri.fromFile(mPhotoFile);
	                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, mPhotoFileUri);
	                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
	            }
	        }
	    }

### Hinzufügen von Code zum Hochladen von Fotodateien in den Blobspeicher


1. Zunächst werden zum `ToDoItem`-Objekt einige neue Metadatenfelder hinzugefügt, indem der folgende Code zu **ToDoItem.java** hinzugefügt wird.

		/**
	     *  imageUri - points to location in storage where photo will go
	     */
	    @com.google.gson.annotations.SerializedName("imageUri")
	    private String mImageUri;

	    /**
	     * Returns the item ImageUri
	     */
	    public String getImageUri() {
	        return mImageUri;
	    }

	    /**
	     * Sets the item ImageUri
	     *
	     * @param ImageUri
	     *            Uri to set
	     */
	    public final void setImageUri(String ImageUri) {
	        mImageUri = ImageUri;
	    }

	    /**
	     * ContainerName - like a directory, holds blobs
	     */
	    @com.google.gson.annotations.SerializedName("containerName")
	    private String mContainerName;

	    /**
	     * Returns the item ContainerName
	     */
	    public String getContainerName() {
	        return mContainerName;
	    }

	    /**
	     * Sets the item ContainerName
	     *
	     * @param ContainerName
	     *            Uri to set
	     */
	    public final void setContainerName(String ContainerName) {
	        mContainerName = ContainerName;
	    }

	    /**
	     *  ResourceName
	     */
	    @com.google.gson.annotations.SerializedName("resourceName")
	    private String mResourceName;

	    /**
	     * Returns the item ResourceName
	     */
	    public String getResourceName() {
	        return mResourceName;
	    }

	    /**
	     * Sets the item ResourceName
	     *
	     * @param ResourceName
	     *            Uri to set
	     */
	    public final void setResourceName(String ResourceName) {
	        mResourceName = ResourceName;
	    }

	    /**
	     *  SasQueryString - permission to write to storage
	     */
	    @com.google.gson.annotations.SerializedName("sasQueryString")
	    private String mSasQueryString;

	    /**
	     * Returns the item SasQueryString
	     */
	    public String getSasQueryString() {
	        return mSasQueryString;
	    }

	    /**
	     * Sets the item SasQueryString
	     *
	     * @param SasQueryString
	     *            Uri to set
	     */
	    public final void setSasQueryString(String SasQueryString) {
	        mSasQueryString = SasQueryString;
	    }

2. Ersetzen Sie den 0-Parameterkonstruktor durch den folgenden Code:

	    /**
	     * ToDoItem constructor
	     */
	    public ToDoItem() {
	        mContainerName = "";
	        mResourceName = "";
	        mImageUri = "";
	        mSasQueryString = "";
	    }

3. Ersetzen Sie den Multi-Parameterkonstruktor durch den folgenden Code:

	    /**
	     * Initializes a new ToDoItem
	     *
	     * @param text
	     *            The item text
	     * @param id
	     *            The item id
	     */
	    public ToDoItem(String text,
	                    String id,
	                    String containerName,
	                    String resourceName,
	                    String imageUri,
	                    String sasQueryString) {
	        this.setText(text);
	        this.setId(id);
	        this.setContainerName(containerName);
	        this.setResourceName(resourceName);
	        this.setImageUri(imageUri);
	        this.setSasQueryString(sasQueryString);
	    }


4. Ersetzen Sie in der Datei **ToDoActivity.java** die **AddItem**-Methode in **ToDoActivity.java** durch den folgenden Code, mit dem das Bild hochgeladen wird.

	    /**
	     * Add a new item
	     *
	     * @param view
	     *            The view that originated the call
	     */
	    public void uploadPhoto(View view) {
	        if (mClient == null) {
	            return;
	        }

	        // Create a new item
	        final ToDoItem item = new ToDoItem();

	        item.setText(mTextNewToDo.getText().toString());
	        item.setComplete(false);
	        item.setContainerName("todoitemimages");

	        // Use a unigue GUID to avoid collisions.
	        UUID uuid = UUID.randomUUID();
	        String uuidInString = uuid.toString();
	        item.setResourceName(uuidInString);

	        // Send the item to be inserted. When blob properties are set this
	        // generates an SAS in the response.
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
		                    final ToDoItem entity = addItemInTable(item);

		                    // If we have a returned SAS, then upload the blob.
		                    if (entity.getSasQueryString() != null) {

	                       // Get the URI generated that contains the SAS
	                        // and extract the storage credentials.
	                        StorageCredentials cred =
								new StorageCredentialsSharedAccessSignature(entity.getSasQueryString());
	                        URI imageUri = new URI(entity.getImageUri());

	                        // Upload the new image as a BLOB from a stream.
	                        CloudBlockBlob blobFromSASCredential =
	                                new CloudBlockBlob(imageUri, cred);

	                        blobFromSASCredential.uploadFromFile(mPhotoFileUri.getPath());
  	                    }

	                    runOnUiThread(new Runnable() {
	                        @Override
	                        public void run() {
	                            if(!entity.isComplete()){
	                                mAdapter.add(entity);
	                            }
	                        }
	                    });
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };

	        runAsyncTask(task);

	        mTextNewToDo.setText("");
	    }


Mit diesem Code wird eine Anforderung zum Einfügen eines neuen TodoItem-Elements an den mobilen Dienst gesendet. Die Antwort enthält die SAS, die dann zum Hochladen des Bilds aus dem lokalen Speicher in einen Blob im Azure-Speicher verwendet wird.


## Testen des Hochladens von Bildern

1. Drücken Sie in Android Studio **Ausführen**. Wählen Sie im Dialogfeld das Gerät aus.

2. Wenn die Benutzeroberfläche der App angezeigt wird, geben Sie Text in das Textfeld mit der Bezeichnung **TODO-Element hinzufügen** ein.

3. Drücken Sie **Fotografieren**. Nehmen Sie ein Foto auf, sobald die Kamera-App gestartet wird. Drücken Sie auf das Häkchen, um das Foto zu akzeptieren.

4. Drücken Sie **Hochladen**. Beachten Sie, wie das ToDoItem wie gewohnt in die Liste hinzugefügt wurde.

5. Wechseln Sie im Microsoft Azure-Portal zu Ihrem Speicherkonto, und drücken Sie auf die Registerkarte **Container**, und anschließend auf den Namen des Containers in der Liste.

6. Eine Liste der hochgeladenen Blob-Dateien wird angezeigt. Wählen Sie eine aus, und drücken Sie **Herunterladen**.

7. Das hochgeladene Bild wird jetzt in einem Browserfenster angezeigt.


## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie nun gelernt haben, Ihre Bilder durch die Integration Ihres mobilen Dienstes in den Blob-Dienst sicher hochzuladen, können Sie sich einigen anderen Themen aus dem Bereich Backend-Dienste und Integration zuwenden:

+ [Senden von E-Mails in Mobile Services mit SendGrid]

  Lernen Sie, wie Sie mithilfe des E-Mail-Dienstes SendGrid eine E-Mail-Funktion zu Ihrem mobilen Dienst hinzufügen können. In diesem Thema wird erläutert, wie serverseitige Skripts zum Senden von E-Mails mithilfe von SendGrid hinzugefügt werden.

+ [Planen von Back-End-Aufträgen in Mobile Services]

  Lernen Sie, wie Sie die Auftragsplanerfunktoin in Mobile Services zur Definition von Skriptcode verwenden können, der gemäß einem von Ihnen definierten Zeitplan ausgeführt wird.

+ [Mobile Services: Serverskriptreferenz]

  Referenzthemen zur Verwendung von Serverskripts zur Ausführung serverseitiger Tasks sowie zur Integration in andere Azure-Komponenten und externe Ressourcen.

+ [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]

  Lernen Sie mehr über die Verwendung von Mobile Services mit .NET


<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Senden von E-Mails in Mobile Services mit SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Planen von Back-End-Aufträgen in Mobile Services]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Mobile Services: Serverskriptreferenz]: mobile-services-how-to-use-server-scripts.md
[Erste Schritte mit Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=Oct15_HO3-->