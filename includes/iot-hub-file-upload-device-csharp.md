## Hochladen einer Datei von einem simulierten Gerät

In diesem Abschnitt ändern Sie die simulierte Geräteanwendung, die Sie in [Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub] erstellt haben, um Cloud-zu-Gerät-Nachrichten von IoT Hub zu empfangen.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das **SimulatedDevice**-Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**. 

    Daraufhin wird das Fenster "NuGet-Pakete verwalten" angezeigt.

2. Suchen Sie nach `WindowsAzure.Storage`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

    Daraufhin wird das [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage/) heruntergeladen, installiert und mit einem Verweis versehen.

3. Fügen Sie zu Beginn der Datei **Program.cs** die folgenden Anweisungen hinzu:

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. Ändern Sie in der **Program**-Klasse die **ReceiveC2dAsync**-Methode wie folgt:
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    Auf diese Weise kann **ReceiveC2dAsync** Nachrichten unterscheiden, deren `command`-Eigenschaft auf `FileUpload` festgelegt ist und die von der **UploadFileToBlobAsync**-Methode behandelt werden.

    Fügen Sie die Methode unten hinzu, um die Befehle für Dateiuploads zu behandeln.
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    Diese Methode verwendet das Azure Storage SDK zum Hochladen eines zufällig generierten 10 MB-BLOBs unter dem angegebenen URI. Weitere Informationen zum Hochladen von BLOBs finden Sie unter [Azure Storage – Verwenden von BLOBs].

> [AZURE.NOTE] Beachten Sie, dass diese Implementierung des simulierten Geräts die Cloud-zu-Gerät-Nachricht erst abschließt, nachdem das BLOB hochgeladen wurde. Dieser Ansatz vereinfacht die Verarbeitung der hochgeladenen Dateien im Back-End, da die Übermittlungsbestätigung die Verfügbarkeit der hochgeladenen Datei für die Verarbeitung bestätigt. Wie allerdings im [Entwicklungsleitfaden für IoT Hub][IoT Hub Developer Guide - C2D] beschrieben, wird eine Nachricht, die nicht vor dem *Sichtbarkeitstimeout* (normalerweise 1 Minute) abgeschlossen ist, zurück in die Gerätewarteschlange gestellt und erneut an die **ReceiveAsync()**-Methode übergeben. Bei Szenarien, in denen der Dateiupload länger dauern kann, empfiehlt es sich u. U. für das simulierte Gerät, die aktuellen Uploadaufträge in einem permanenten Speicher vorzuhalten. Dadurch kann das simulierte Gerät die Cloud-zu-Gerät-Nachricht vor Beendigung des Dateiuploads abschließen und dann das Back-End in einer Gerät-zu-Cloud-Nachricht über den Abschluss informieren.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure Storage – Verwenden von BLOBs]: ../storage/storage-dotnet-how-to-use-blobs.md#upload-a-blob-into-a-container

<!-- Images -->

<!---HONumber=AcomDC_0309_2016-->