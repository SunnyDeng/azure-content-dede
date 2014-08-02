Der bestehende **TodoItemController** wird aktualisiert, sodass die **PostTodoItem**-Methode eine SAS generiert, wenn ein neues Todo-Element eingefügt wird.

1.  Wenn Sie das Speicherkonto noch nicht erstellt haben, lesen Sie [So erstellen Sie ein Speicherkonto](/en-us/manage/services/storage/how-to-create-a-storage-account).

2.  Klicken Sie im Verwaltungsportal auf **Speicher**, klicken Sie auf das Speicherkonto, und klicken Sie dann auf **Schlüssel verwalten**.

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png)

1.  Notieren Sie sich **Speicherkontoname** und **Zugriffsschlüssel**.

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

2.  Klicken Sie in Ihrem mobilen Service auf die Registerkarte **Konfigurieren**, führen Sie einen Bildlauf nach unten bis **App-Einstellungen** durch, und geben Sie ein Paar aus **Name** und **Wert** für jedes der folgenden Elemente ein, die Sie vom Speicherkonto erhalten haben. Klicken Sie anschließend auf **Speichern**.

    -   `STORAGE_ACCOUNT_NAME`
    -   `STORAGE_ACCOUNT_ACCESS_KEY`

    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

    Der Zugriffsschlüssel des Speicherkontos wird verschlüsselt in den App-Einstellungen gespeichert. Sie können auf diesen Schlüssel von jedem beliebigen Serverskript zur Laufzeit zugreifen. Weitere Informationen finden Sie unter [App-Einstellungen](http://msdn.microsoft.com/en-us/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7).

3.  Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei Web.config für das Projekt des mobilen Services, und fügen Sie die folgenden neuen App-Einstellungen hinzu, wobei Sie die Platzhalter durch den Speicherkontonamen und den Zugriffsschlüssel ersetzen, die als App-Einstellungen im Portal festgelegt sind:

         <add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
         <add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

    Der mobile Service verwendet diese gespeicherten Einstellungen, wenn er auf dem lokalen Computer läuft, sodass Sie den Code testen können, bevor Sie ihn veröffentlichen. Bei Ausführung in Azure verwendet der mobile Service hingegen die im Portal festgelegten App-Einstellungswerte und ignoriert diese Projekteinstellungen.

4.  Öffnen Sie im Controller-Ordner die Datei TodoItemController.cs, und fügen Sie die folgenden **using**-Anweisungen hinzu:

        using System;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;

5.  Ersetzen Sie die vorhandene **PostTodoItem**-Methode durch den folgenden Code:

        var azure = require('azure');
        var qs = require('querystring');
        var appSettings = require('mobileservice-config').appSettings;
            
        function insert(item, user, request) {
            // Get storage account settings from app settings. 
            var accountName = appSettings.STORAGE_ACCOUNT_NAME;
            var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
            var host = accountName + '.blob.core.windows.net';
            
            if ((typeof item.containerName !== "undefined") && (
            item.containerName !== null)) {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.toLowerCase();
            
                // If it does not already exist, create the container 
                // with public read access for blobs.        
                var blobService = azure.createBlobService(accountName, accountKey, host);
                blobService.createContainerIfNotExists(item.containerName, {
                    publicAccessLevel: 'blob'
                }, function(error) {
                    if (!error) {
            
                        // Provide write access to the container for the next 5 mins.        
                        var sharedAccessPolicy = {
                            AccessPolicy: {
                                Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
                                Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
                            }
                        };
            
                        // Generate the upload URL with SAS for the new image.
                        var sasQueryUrl = 
                        blobService.generateSharedAccessSignature(item.containerName, 
                        item.resourceName, sharedAccessPolicy);
            
                        // Set the query string.
                        item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
            
                        // Set the full path on the new new item, 
                        // which is used for data binding on the client. 
                        item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;
            
                    } else {
                        console.error(error);
                    }
                    request.execute();
                });
            } else {
                request.execute();
            }
        }

	![](./media/mobile-services-configure-blob-storage/mobile-insert-script-blob.png) 

	Dadurch wird die Funktion ersetzt, die aufgerufen wird, wenn ein Einfügevorgang in der Tabelle "TodoItem" mit einem neuen Skript erfolgt. Dieses neue Skript generiert eine neue SAS für den Einfügevorgang, die fünf Minuten gültig ist. Dann weist es den Wert der generierten SAS der Eigenschaft `sasQueryString` des zurückgegebenen Elements zu. Die Eigenschaft `imageUri` wird auch auf den Ressourcenpfad des neuen BLOB festgelegt, um die Imageanzeige während der Bindung in der Client-UI zu ermöglichen.

    >[WACOM.NOTE] Durch diesen Code wird eine SAS für ein einzelnes BLOB erstellt. Wenn Sie mehrere BLOBs in einen Container mit derselben SAS hochladen müssen, können Sie stattdessen die Methode <a href="http://go.microsoft.com/fwlink/
    LinkId=390455" target="_blank">generateSharedAccessSignature</a> mit einem leeren BLOB-Ressourcennamen wie den folgenden aufrufen: 
    <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

Als Nächstes aktualisieren Sie die Quickstart-App, um unter Verwendung der beim Einfügevorgang generierten SAS Funktionalität zum Hochladen von Images hinzuzufügen.

