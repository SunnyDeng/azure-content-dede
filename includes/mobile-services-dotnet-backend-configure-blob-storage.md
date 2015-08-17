
##Installieren des Speicherclients im Mobildienstprojekt

Um eine SAS für das Hochladen von Bildern in den Blob-Speicher generieren zu können, müssen Sie zuerst das NuGet-Paket hinzufügen, das die Speicherclientbibliothek für Windows Store-Apps installiert.

1. Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf das Projekt für den mobilen Dienst, und wählen Sie dann **Manage NuGet Packages** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online** aus, wählen Sie **Stabile Only** aus, suchen Sie nach **WindowsAzure.Storage**, klicken Sie auf **Installieren** im Paket **Azure Storage**, und stimmen Sie den Lizenzverträgen zu.

  	![](./media/mobile-services-configure-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Die Clientbibliothek für Azure-Speicherdienste wird dem Projekt für den mobilen Dienst hinzugefügt.

##Aktualisieren der TodoItem-Definition im Datenmodell

Die TodoItem-Klasse definiert das Datenobjekt, und Sie müssen dieser Klasse dieselben Eigenschaften hinzufügen, die Sie dem Client hinzugefügt haben.

1. In Visual Studio 2013 öffnen Sie Ihr Projekt für den mobilen Service, erweitern Sie den DataObjects-Ordner und öffnen Sie dann die Projektdatei TodoItem.cs.
	
2. Fügen Sie der Klasse **TodoItem** die folgenden Eigenschaften hinzu:

        public string containerName { get; set; }
		public string resourceName { get; set; }
		public string sasQueryString { get; set; }
		public string imageUri { get; set; } 

	Diese Eigenschaften werden zum Generieren der SAS und zum Speichern der Bilddaten verwendet. Beachten Sie, dass die Groß-/Kleinschreibung dieser Eigenschaften der JavaScript-Backend-Version entspricht.

	>[AZURE.NOTE]Bei Verwendung des Standard-Datenbankinitialisierers können Sie mit Entity Framework die Datenbank löschen und erneut erstellen, sobald es eine Datenmodelländerung in der Code First-Modelldefinition erkennt. Um eine Datenmodelländerung durchzuführen und bestehende Daten in der Datenbank beizubehalten, müssen Sie Code First-Migrationen verwenden. Der Standard-Initialisierer kann nicht für eine SQL-Datenbank in Azure angewendet werden. Weitere Informationen finden Sie unter [Verwenden von Code First-Migrationen zur Aktualisierung des Datenmodells](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

##Aktualisieren des TodoItem-Controllers zur Erzeugung einer freigegebenen Zugriffssignatur SAS 

Die vorhandene **TodoItemController**-Klasse wird aktualisiert, sodass die **PostTodoItem**-Methode eine SAS generiert, wenn ein neues TodoItem-Element eingefügt wird. Außerdem gilt:

0. Wenn Sie das Speicherkonto noch nicht erstellt haben, lesen Sie [So erstellen Sie ein Speicherkonto].

1. Klicken Sie im Verwaltungsportal auf **Speicher**, klicken Sie auf das Speicherkonto, und klicken Sie dann auf **Schlüssel verwalten**.

2. Notieren Sie sich **Speicherkontoname** und **Zugriffsschlüssel**.
 
3. Klicken Sie in Ihrem mobilen Service auf die Registerkarte **Konfigurieren**, führen Sie einen Bildlauf nach unten bis **App-Einstellungen** durch, und geben Sie ein Paar aus **Name** und **Wert** für jedes der folgenden Elemente ein, die Sie vom Speicherkonto erhalten haben. Klicken Sie anschließend auf **Speichern**.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	Der Zugriffsschlüssel des Speicherkontos wird verschlüsselt in den App-Einstellungen gespeichert. Sie können auf diesen Schlüssel von jedem beliebigen Serverskript zur Laufzeit zugreifen. Weitere Informationen finden Sie unter [App-Einstellungen].

4. Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei Web.config für das Projekt des mobilen Services, und fügen Sie die folgenden neuen App-Einstellungen hinzu, wobei Sie die Platzhalter durch den Speicherkontonamen und den Zugriffsschlüssel ersetzen, die als App-Einstellungen im Portal festgelegt sind:

		<add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
		<add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

	Der mobile Service verwendet diese gespeicherten Einstellungen, wenn er auf dem lokalen Computer läuft, sodass Sie den Code testen können, bevor Sie ihn veröffentlichen. Bei Ausführung in Azure verwendet der mobile Service hingegen die im Portal festgelegten App-Einstellungswerte und ignoriert diese Projekteinstellungen.

7.  Öffnen Sie im Controller-Ordner die Datei TodoItemController.cs, und fügen Sie die folgenden **using**-Anweisungen hinzu:

		using System;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
  
8.  Ersetzen Sie die vorhandene **PostTodoItem**-Methode durch den folgenden Code:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            string storageAccountName;
            string storageAccountKey;

            // Try to get the Azure storage account token from app settings.  
            if (!(Services.Settings.TryGetValue("STORAGE_ACCOUNT_NAME", out storageAccountName) |
            Services.Settings.TryGetValue("STORAGE_ACCOUNT_ACCESS_KEY", out storageAccountKey)))
            {
                Services.Log.Error("Could not retrieve storage account settings.");
            }

            // Set the URI for the Blob Storage service.
            Uri blobEndpoint = new Uri(string.Format("https://{0}.blob.core.windows.net", storageAccountName));

            // Create the BLOB service client.
            CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, 
                new StorageCredentials(storageAccountName, storageAccountKey));

            if (item.containerName != null)
            {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.ToLower();

                // Create a container, if it doesn't already exist.
                CloudBlobContainer container = blobClient.GetContainerReference(item.containerName);
                await container.CreateIfNotExistsAsync();

                // Create a shared access permission policy. 
                BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

                // Enable anonymous read access to BLOBs.
                containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
                container.SetPermissions(containerPermissions);

                // Define a policy that gives write access to the container for 5 minutes.                                   
                SharedAccessBlobPolicy sasPolicy = new SharedAccessBlobPolicy()
                {
                    SharedAccessStartTime = DateTime.UtcNow,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5),
                    Permissions = SharedAccessBlobPermissions.Write
                };

                // Get the SAS as a string.
                item.sasQueryString = container.GetSharedAccessSignature(sasPolicy); 

                // Set the URL used to store the image.
                item.imageUri = string.Format("{0}{1}/{2}", blobEndpoint.ToString(), 
                    item.containerName, item.resourceName);
            }

            // Complete the insert operation.
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

   	Diese POST-Methode generiert eine neue SAS für das eingefügte Element, die 5 Minuten lang gültig ist, und weist den Wert der generierten SAS der `sasQueryString`-Eigenschaft des zurückgegebenen Elements zu. Die `imageUri`-Eigenschaft wird auch auf den Ressourcenpfad des neuen BLOB festgelegt, um die Imageanzeige während der Bindung in der Client-UI zu ermöglichen.

	>[AZURE.NOTE]Durch diesen Code wird eine SAS für ein einzelnes BLOB erstellt. Wenn Sie mehrere BLOBs in einen Container mit derselben SAS hochladen müssen, können Sie stattdessen die <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">generateSharedAccessSignature-Methode</a> mit einem leeren BLOB-Ressourcennamen wie den Folgenden aufrufen: <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

Als Nächstes aktualisieren Sie die Quickstart-App, um unter Verwendung der beim Einfügevorgang generierten SAS Funktionalität zum Hochladen von Images hinzuzufügen.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[So erstellen Sie ein Speicherkonto]: ../articles/storage/storage-create-storage-account.md
[App-Einstellungen]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=August15_HO6-->