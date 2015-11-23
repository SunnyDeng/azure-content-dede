## Senden einer Cloud-zu-Gerät-Nachricht vom App-Back-End

In diesem Abschnitt schreiben Sie eine Windows-Konsolen-App, die Cloud-zu-Gerät-Nachrichten an die simulierte Geräte-App sendet.

1. Erstellen Sie in der aktuellen Visual Studio-Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktop-App-Projekt. Nennen Sie das Projekt **SendCloudToDevice**.

   	![][20]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten...**

	Daraufhin wird das Fenster „NuGet-Pakete verwalten“ angezeigt.

3. Suchen Sie nach `Microsoft Azure Devices`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

	Daraufhin wird das [NuGet-Paket mit dem Dienst-SDK für Azure IoT] heruntergeladen, installiert und mit einem Verweis versehen.

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

		using Microsoft.Azure.Devices;

5. Fügen Sie der **Program**-Klasse die folgenden Felder hinzu, und ersetzen Sie dabei die Platzhalterwerte durch den IoT Hub-Namen und die IoT Hub-Verbindungszeichenfolge aus [Erste Schritte mit IoT Hub].

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Fügen Sie der **Program**-Klasse die folgende Methode hinzu:

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	Diese Methode sendet eine neue Cloud-zu-Gerät-Nachricht an das Gerät mit der ID `myFirstDevice`. Ändern Sie diesen Parameter entsprechend, falls Sie den in [Erste Schritte mit IoT Hub] verwendeten Parameter geändert haben.

7. Fügen Sie abschließend der **Main**-Methode die folgenden Zeilen hinzu:

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Klicken Sie in Visual Studio mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen** aus. Wählen Sie **Mehrere Startprojekte** aus, und wählen Sie dann die Aktion **Starten** für **ProcessDeviceToCloudMessages**, **SimulatedDevice** und **SendCloudToDevice** aus.

9.  Drücken Sie **F5**. Alle drei Anwendungen sollten gestartet werden. Wählen Sie die Fenster **SendCloudToDevice** aus, und drücken Sie die **EINGABETASTE**: Sie sollten sehen, dass die Nachricht von der simulierten App empfangen wird.

    ![][21]

## Empfangen von Übermittlungsfeedback
Es ist möglich, für jede Cloud-zu-Gerät-Nachricht Übermittlungsbestätigungen (oder Ablaufbestätigungen) von IoT Hub anzufordern. Auf diese Weise kann die Wiederholungs- oder Kompensierungslogik einfach vom Cloud-Back-End informiert werden. Weitere Informationen zu Cloud-zu-Gerät-Feedback finden Sie im [Entwicklungsleitfaden für IoT Hub][IoT Hub Developer Guide - C2D].

In diesem Abschnitt ändern Sie die **SendCloudToDevice**-App so, dass sie Feedback IoT Hub anfordert und empfängt.

1. Fügen Sie in Visual Studio im **SendCloudToDevice**-Projekt der **Program**-Klasse folgende Methode hinzu.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Beachten Sie, dass das Empfangsmuster mit dem Muster zum Empfangen von Cloud-zu-Gerät-Nachrichten von der Geräte-App identisch ist.

2. Fügen Sie in der **Main**-Methode unmittelbar nach der Zeile `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` folgende Methode hinzu:

        ReceiveFeedbackAsync();

3. Um für die Übermittlung der Cloud-zu-Gerät-Nachricht Feedback anzufordern, müssen Sie in der **SendCloudToDeviceMessageAsync**-Methode eine Eigenschaft angeben. Fügen Sie direkt nach der Zeile `var commandMessage = new Message(...);` folgende Zeile hinzu:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Führen Sie die Apps durch Drücken von **F5** aus. Alle drei Anwendungen sollten gestartet werden. Wählen Sie die Fenster **SendCloudToDevice** aus, und drücken Sie die **EINGABETASTE**: Sie sollten sehen, dass die Nachricht von der simulierten App empfangen wird. Nach einigen Sekunden sollte die Feedbacknachricht von der **SendCloudToDevice**-App empfangen werden.

    ![][22]

> [AZURE.NOTE]Der Einfachheit halber wird in diesem Lernprogramm keine Wiederholungsrichtlinie implementiert. Es wird empfohlen, im Produktionscode Wiederholungsrichtlinien zu implementieren (z. B. einen exponentiellen Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler] beschrieben.

<!-- Links -->

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[NuGet-Paket mit dem Dienst-SDK für Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Behandeln vorübergehender Fehler]: https://msdn.microsoft.com/de-DE/library/hh680901(v=pandp.50).aspx
[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png

<!---HONumber=Nov15_HO3-->