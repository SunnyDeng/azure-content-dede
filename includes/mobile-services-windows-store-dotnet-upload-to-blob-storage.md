## <a name="add-select-images"></a>Aktualisierung der Schnellstart-Client-App zum Aufnehmen und Hochladen von Bildern

1.  Öffnen Sie die Datei Package.appxmanifest in Visual Studio 2012, und aktivieren Sie auf der Registerkarte **Funktionen** die Funktionen **Webcam** und **Mikrofon**.

    ![][]

    Damit stellen Sie sicher, dass Ihre App eine am Computer angeschlossene Kamera nutzen kann. Bei der erstmaligen Ausführung der App wird der Benutzer dazu aufgefordert, Kamerazugang zu gestatten.

2.  Öffnen Sie die Datei MainPage.xaml und ersetzen Sie das **StackPanel**-Element direkt nach dem ersten **Aufgabe**-Element durch den folgenden Code:

        <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <Button Name="btnTakePhoto" Style="{StaticResource PhotoAppBarButtonStyle}"
                    Click="OnTakePhotoClick" />
            <Button Name="ButtonSave" Style="{StaticResource UploadAppBarButtonStyle}" 
                    Click="ButtonSave_Click"/>
        </StackPanel>

3.  Ersetzen Sie das **StackPanel**-Element in der **Datenvorlage** durch den folgenden Code:

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

    Dadurch wird ein Bild zu **ItemTemplate** hinzugefügt und dessen Bindungsquelle als URI des hochgeladenen Bilds im Blob-Speicherdienst festgelegt.

4.  Öffnen Sie die Projektdatei MainPage.xaml.cs, und fügen Sie die folgenden **using**-Anweisungen ein:

        using Windows.Media.Capture;
        using Windows.Storage;
        using Windows.UI.Popups;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;

5.  Fügen Sie in der TodoItem-Klasse die folgenden Eigenschaften hinzu:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }

        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }

        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }

        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

    > [WACOM.NOTE]Um neue Eigenschaften zum TodoItem-Objekt hinzuzufügen, muss das dynamische Schema im mobilen Service aktiviert sein. Wenn das dynamische Schema aktiviert ist, werden automatisch neue Spalten in die TodoItem-Tabelle eingefügt, die auf diese neuen Eigenschaften verweisen.

6.  Fügen Sie in der MainPage-Klasse den folgenden Code hinzu:

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;

        private async void OnTakePhotoClick(object sender, RoutedEventArgs e)
        {
            // Capture a new photo or video from the device.
            CameraCaptureUI cameraCapture = new CameraCaptureUI();
            media = await cameraCapture
                .CaptureFileAsync(CameraCaptureUIMode.PhotoOrVideo);
        }

    Dieser Code zeigt die Kamera-Benutzeroberfläche zum Aufnehmen eines Bilds an und sichert das Bild in einer Speicherdatei.

7.  Ersetzen Sie die vorhandene `InsertTodoItem`-Methode durch den folgenden Code:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;

            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = media.Name;
            }

            // Send the item to be inserted. When blob properties are set this
            // generates an SAS in the response.
            await todoTable.InsertAsync(todoItem);

            // If we have a returned SAS, then upload the blob.
            if (!string.IsNullOrEmpty(todoItem.SasQueryString))
            {
                // Get the URI generated that contains the SAS 
                // and extract the storage credentials.
                StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                var imageUri = new Uri(todoItem.ImageUri);

                // Instantiate a Blob store container based on the info in the returned item.
                CloudBlobContainer container = new CloudBlobContainer(
                    new Uri(string.Format("https://{0}/{1}",
                        imageUri.Host, todoItem.ContainerName)), cred);

                // Get the new image as a stream.
                using (var fileStream = await media.OpenStreamForReadAsync())
                {                                       
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(fileStream.AsInputStream());
                }

                // When you request an SAS at the container-level instead of the blob-level,
                // you are able to upload multiple streams using the same container credentials.
            }

            // Add the new item to the collection.
            items.Add(todoItem);
        }

    Dieser Code sendet eine Anfrage zum mobilen Service für Einfügen eines neuen TodoItem einschließlich des Bilddateinamens. Die Antwort enthält die SAS, die dann zum Einfügen des Bilds in den Blob-Speicher verwendet wird, und den URI des Bilds für Datenbindung.

Der letzte Schritt besteht darin, die App zu testen und sicherzustellen, dass das Hochladen funktioniert.

## <a name="test"></a>Testen des Hochladens von Bildern in der App

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie in das Textfeld unter **TodoItem einfügen** einen Text ein, und klicken Sie dann auf **Foto**.

    ![][1]

    Dadurch wird die Kamera-Aufnahme-Benutzeroberfläche angezeigt.

3.  Klicken Sie auf das Bild, um eine Aufnahme zu machen, und klicken Sie dann auf **OK**.

    ![][2]

4.  Klicken Sie auf **Hochladen**, um das neue Element einzufügen und das Bild hochzuladen.

    ![][3]

5.  Das neue Element wird zusammen mit dem hochgeladenen Bild in der Listenansicht angezeigt.

    ![][4]

    > [WACOM.NOTE]Das Bild wird automatisch vom Blob-Speicherdienst heruntergeladen, wenn die `imageUri`-Eigenschaft des neuen Elements an die **Image**-Steuerung gebunden ist.

  []: ./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png
  [1]: ./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar.png
  [2]: ./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-camera.png
  [3]: ./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png
  [4]: ./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png
