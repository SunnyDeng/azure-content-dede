## Bereitstellen eines Azure-Speicherkontos
Da vom simulierten Gerät eine Datei in ein Azure Storage-BLOB hochgeladen wird, müssen Sie über ein Azure-Speicherkonto verfügen. Sie können ein vorhandenes Konto verwenden oder mithilfe der Anweisungen unter [Informationen zu Azure Storage] ein neues Konto erstellen. Notieren Sie die Verbindungszeichenfolge für das Speicherkonto.

## Senden eines Azure-BLOB-URIs an das simulierte Gerät

In diesem Abschnitt ändern Sie die unter [Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub] erstellte **SendCloudtoDevice**-Konsolen-App, indem Sie ihr einen Azure-BLOB-URI mit einer SAS (Shared Access Signature) hinzufügen. Dadurch ist das Cloud-Back-End in der Lage, ausschließlich dem Empfänger der C2D-Nachricht Schreibzugriff auf das Blob zu gewähren.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das **SendCloudtoDevice**-Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**. 

    Daraufhin wird das Fenster "NuGet-Pakete verwalten" angezeigt.

2. Suchen Sie nach `WindowsAzure.Storage`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

    Daraufhin wird das [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage/) heruntergeladen, installiert und mit einem Verweis versehen.

3. Fügen Sie zu Beginn der Datei **Program.cs** die folgenden Anweisungen hinzu:

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. Fügen Sie in der **Programm**-Klasse die folgenden Klassenfelder hinzu, und ersetzen Sie die Verbindungszeichenfolge durch diejenige für Ihr Speicherkonto.

        static string storageConnectionString = "{storage connection string}";

    Fügen Sie die folgende Methode hinzu (Sie können beliebige BLOB-Containernamen ersetzen, dieses Lernprogramm verwendet **iothubfileuploadtutorial**):
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    Diese Methode erstellt einen neuen Blobverweis und generiert einen SAS-URI (Shared Access Signature), wie unter [Erstellen und Verwenden einer SAS mit Blob Storage](../storage/storage-dotnet-shared-access-signature-part-2.md) beschrieben. Beachten Sie, dass die oben genannte Methode einen Signatur-URI generiert, der 24 Stunden gültig ist. Wenn das Zielgerät mehr Zeit zum Hochladen der Datei benötigt (weil es z. B. selten eine Verbindung herstellt, eine unzuverlässige Verbindung zum Hochladen einer umfangreichen Datei nutzt), könnten Sie längere Ablaufzeiten für Signaturen in Betracht ziehen.

5. Ändern Sie **SendCloudToDeviceMessageAsync** auf folgende Weise:

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Diese Methode sendet eine Cloud-zu-Gerät-Nachricht, die zwei Anwendungseigenschaften enthält: Eine identifiziert diese Nachricht als Befehl zum Hochladen einer Datei, und die andere enthält den BLOB-URI. Sie fordert zusätzlich vollständige Übermittlungsbestätigungen an. Beachten Sie, dass die Informationen in den beiden Anwendungseigenschaften in einen Nachrichtentext serialisiert werden können. Das Serialisieren und Deserialisieren der Informationen würde jedoch zusätzlichen Verarbeitungsaufwand verursachen.

<!-- Links -->

[Informationen zu Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->

<!----HONumber=AcomDC_0309_2016-->