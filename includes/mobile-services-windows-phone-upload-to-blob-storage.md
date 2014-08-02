

Aktualisierung der Schnellstart-Client-App zum Aufnehmen und Hochladen von Bildern
----------------------------------------------------------------------------------

In diesem Abschnitt aktualisieren Sie das Projekt vom Lernprogramm [Erste Schritte mit Mobile Services], um Fotos zu machen und diese zum Azure-Blob-Speicher hochzuladen. Zum Aufnehmen des Bilds wird in diesem Lernprogramm [CameraCaptureTask] von Namespace `Microsoft.Phone.Tasks` verwendet. Diese Klasse startet die Kamera-Benutzeroberfläche auf dem Windows Phone-Gerät, um das Foto aufzunehmen, und speichert das Bild automatisch in Eigene Aufnahmen auf dem Windows Phone-Gerät. Falls Sie die Bilder nicht in Eigene Aufnahmen ablegen möchten, verwenden Sie die stattdessen die [PhotoCamera]-Klasse im Namespace `Microsoft.Devices`.

1.  Erweitern Sie im Projektmappen-Explorer für Visual Studio unter dem Projekt **Eigenschaften**. Öffnen Sie dann die Datei WMAppManifest.xml, und aktivieren Sie auf der Registerkarte **Funktionen** die Kamera, indem Sie auf **ID\_CAP\_ISV\_CAMERA** klicken. Schließen Sie die Datei, um die Änderung zu speichern.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png)

	Damit stellen Sie sicher, dass Ihre App eine am Computer angeschlossene Kamera nutzen kann. Bei der erstmaligen Ausführung der App wird der Benutzer dazu aufgefordert, Kamerazugang zu gestatten.

2.  Öffnen Sie die Datei MainPage.xaml file und ersetzen Sie das **Grid**-Element namens **ContentPanel** durch den folgenden Code:

         <!--ContentPanel - place additional content here-->
         <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
             <Grid.RowDefinitions>
                 <RowDefinition Height="Auto" />
                 <RowDefinition Height="Auto" />
                 <RowDefinition Height="Auto" />
                 <RowDefinition Height="Auto" />
                 <RowDefinition Height="Auto" />
                 <RowDefinition Height="*" />
             </Grid.RowDefinitions>
             <Grid.ColumnDefinitions>
                 <ColumnDefinition Width="2*" />
                 <ColumnDefinition Width="2*" />
             </Grid.ColumnDefinitions>
             <TextBlock Grid.Row="0" Grid.ColumnSpan="2" Text="Enter some text below, click Capture Image to add a captured image. Then click Save to insert a new TodoItem item into your database" TextWrapping="Wrap" Margin="12"/>
             <TextBox Grid.Row="1" Grid.ColumnSpan="2" Name="TodoInput" Text="" />
             <Button Name="ButtonCaptureImage" Grid.Row="2" Click="ButtonCaptureImage_Click">Capture Image</Button>
             <Button Grid.Row ="2" Grid.Column="1" Name="ButtonSave" Click="ButtonSave_Click">Save</Button>
             <TextBlock Grid.Row="3" Grid.ColumnSpan="2" Text="Click refresh below to load the unfinished TodoItems from your database. Use the checkbox to complete and update your TodoItems" TextWrapping="Wrap" Margin="12" />
             <Button Grid.Row="4" Grid.ColumnSpan="2" Name="ButtonRefresh" Click="ButtonRefresh_Click">Refresh</Button>
             <phone:LongListSelector Grid.Row="5" Grid.ColumnSpan="2" Name="ListItems">
                 <phone:LongListSelector.ItemTemplate>
                     <DataTemplate>
                         <StackPanel Orientation="Vertical">
                             <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
                             <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" MaxHeight="150"/>
                         </StackPanel>
                     </DataTemplate>
                 </phone:LongListSelector.ItemTemplate>
             </phone:LongListSelector>
         </Grid>


        Dadurch wird eine neue Schaltfläche zum Starten von [CameraCaptureTask] und ein Bild zu **ItemTemplate** hinzugefügt, und dessen Bindungsquelle als URI des hochgeladenen Bilds im Blob-Speicherdienst festgelegt.

3.  Öffnen Sie die Projektdatei MainPage.xaml.cs, und fügen Sie die folgenden **using**-Anweisungen ein:

         using Microsoft.Phone.Tasks;
         using System.IO;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Blob;

4.  Aktualisieren Sie die TodoItem-Klasse in der Projektdatei MainPage.xaml.cs, indem Sie die folgenden Eigenschaften hinzufügen:

         [JsonProperty(PropertyName = "containerName")]
         public string ContainerName { get; set; }
            
         [JsonProperty(PropertyName = "resourceName")]
         public string ResourceName { get; set; }
            
         [JsonProperty(PropertyName = "sasQueryString")]
         public string SasQueryString { get; set; }
            
         [JsonProperty(PropertyName = "imageUri")]
         public string ImageUri { get; set; } 

        <div class="dev-callout"><b>Hinweis</b>
            <p>Um neue Eigenschaften zum TodoItem-Objekt hinzuzufügen, muss das dynamische Schema im mobilen Service aktiviert sein. Wenn das dynamische Schema aktiviert ist, werden automatisch neue Spalten in die TodoItem-Tabelle eingefügt, die auf diese neuen Eigenschaften verweisen.</p>
        </div>

5.  Aktualisieren Sie die MainPage-Klasse in der Projektdatei MainPage.xaml.cs. Fügen Sie den folgenden Code hinzu, um die [CameraCaptureTask] und ein Datenstromobjekt zum Verweis auf das aufgenommene Bild zu deklarieren:

         // Using the CameraCaptureTask to allow the user to capture a todo item image //
         CameraCaptureTask cameraCaptureTask;
            
         // Using a stream reference to upload the image to blob storage.
         Stream imageStream = null;

6.  Aktualisieren Sie die MainPage-Klasse in der Projektdatei MainPage.xaml.cs. Fügen Sie den folgenden Code hinzu, um den Konstruktor zur Erstellung der CameraCaptureTask zu erstellen, und fügen Sie einen Ereignishandler für das abgeschlossene Ereignis hinzu:

         // Constructor
         public MainPage()
         {
             InitializeComponent();
                
             cameraCaptureTask = new CameraCaptureTask();
             cameraCaptureTask.Completed += cameraCaptureTask_Completed;
         }
            
         void cameraCaptureTask_Completed(object sender, PhotoResult e)
         {
             imageStream = e.ChosenPhoto;
         }

7.  Aktualisieren Sie die MainPage-Klasse in der Projektdatei MainPage.xaml.cs. Fügen Sie den folgenden Code zur Anzeige der Kamera-Benutzeroberfläche hinzu, damit der Benutzer durch Klicken auf die Schaltfläche **Image aufnehmen** ein Bild aufnehmen kann:

         private void ButtonCaptureImage_Click(object sender, RoutedEventArgs e)
         {
             cameraCaptureTask.Show();
         }

8.  Aktualisieren Sie die MainPage-Klasse in der Projektdatei MainPage.xaml.cs. Ersetzen Sie die vorhandene `InsertTodoItem`-Methode durch den folgenden Code:

         private async void InsertTodoItem(TodoItem todoItem)
         {
             string errorString = string.Empty;            
                
             if (imageStream != null)
             {
                 // Set blob properties of TodoItem.
                 todoItem.ContainerName = "todoitemimages";
                 todoItem.ResourceName = Guid.NewGuid().ToString() + ".jpg";
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
                    
                 // Upload the new image as a BLOB from the stream.
                 CloudBlockBlob blobFromSASCredential =
                     container.GetBlockBlobReference(todoItem.ResourceName);
                 await blobFromSASCredential.UploadFromStreamAsync(imageStream);
                    
                 // When you request an SAS at the container-level instead of the blob-level,
                 // you are able to upload multiple streams using the same container credentials.

                 imageStream = null;
             }              
                
             // Add the new item to the collection.
             items.Add(todoItem);
             TodoInput.Text = "";
         }

    Dieser Code sendet eine Anfrage zum mobilen Service für Einfügen eines neuen TodoItem einschließlich des Bilddateinamens. Die Antwort enthält die SAS, die dann zum Einfügen des Bilds in den Blob-Speicher verwendet wird, und den URI des Bilds für Datenbindung.

Der letzte Schritt besteht darin, die App zu testen und sicherzustellen, dass das Hochladen funktioniert.

Testen des Hochladens von Bildern in der App
--------------------------------------------

1.  In Visual Studio können Sie die F5-Taste drücken, um die App im Emulator oder mit einem echten Zielgerät zu testen.

2.  Geben Sie einen Text in das Textfeld ein, und klicken Sie dann auf **Image aufnehmen**.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

	Dadurch wird die Kamera-Aufnahme-Benutzeroberfläche angezeigt.

1.  Klicken Sie auf die Bild- oder Schnappschuss-Schaltfläche auf dem Phone, um eine Aufnahme zu machen.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-wp8.png)

2.  Klicken Sie auf **zulassen**, um das Bild anzunehmen und die Kamera-Benutzeroberfläche zu verlassen.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png)

3.  Klicken Sie auf **Speichern**, um das neue Element einzufügen und das Bild hochzuladen.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-save-wp8.png)

4.  Das neue Element wird zusammen mit dem hochgeladenen Bild in der Listenansicht angezeigt.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

    > [WACOM.NOTE]Das Bild wird automatisch vom Blob-Speicherdienst heruntergeladen, wenn die `imageUri`-Eigenschaft des neuen Elements an die **Image**-Steuerung gebunden ist.


