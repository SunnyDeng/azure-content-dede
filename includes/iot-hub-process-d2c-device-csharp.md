## Senden interaktiver Nachrichten vom simulierten Gerät

In diesem Abschnitt ändern Sie die simulierte Geräteanwendung so, dass sie interaktive Gerät-zu-Cloud-Nachrichten an IoT Hub sendet.

1. Fügen Sie in Visual Studio im **SimulatedDevice**-Projekt der **Program**-Klasse folgende Methode hinzu.
   
        private static async void SendDeviceToCloudInteractiveMessagesAsync()
        {
            while (true)
            {
                var interactiveMessageString = "Alert message!";
                var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
                interactiveMessage.Properties["messageType"] = "interactive";
                interactiveMessage.MessageId = Guid.NewGuid().ToString();

                await deviceClient.SendEventAsync(interactiveMessage);
                Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

                Thread.Sleep(10000);
            }
        }

    Diese Methode ist der `SendDeviceToCloudMessagesAsync()`-Methode, die unter [Erste Schritte mit IoT Hub] erstellt wurde, sehr ähnlich. Die einzigen Unterschiede bestehen darin, dass jetzt die `MessageId`-Systemeigenschaft und eine Benutzereigenschaft namens `messageType` festgelegt werden. Die `MessageId`-Eigenschaft wird auf eine GUID (Globally Unique Identifier) festgelegt, die zum Deduplizieren von Nachrichtenempfangsvorgängen verwendet wird. Die `messageType`-Eigenschaft wird verwendet, um interaktive von Datenpunktnachrichten zu unterscheiden. Diese Informationen werden in Nachrichteneigenschaften anstatt im Nachrichtentext übergeben. Auf diese Weise muss der Ereignisprozessor im Back-End zum bloßen Routing nicht die gesamte Nachricht deserialisieren.

> [AZURE.NOTE]Es ist wichtig, dass die zum Deduplizieren interaktiver Nachrichten verwendete `MessageId` auf dem Gerät erstellt wird, da Unterbrechungen der Netzwerkverbindung (oder andere Fehler) dazu führen können, dass dieselbe Nachricht vom Gerät mehrmals neu übertragen wird. Eine semantische Nachrichten-ID (z. B. ein Hash der relevanten Datenfelder der Nachricht) kann auch anstelle einer GUID verwendet werden.

2. Fügen Sie in der **Main**-Methode unmittelbar vor der Zeile `Console.ReadLine()` folgende Methode hinzu:

        SendDeviceToCloudInteractiveMessagesAsync();

> [AZURE.NOTE]Der Einfachheit halber wird in diesem Lernprogramm keine Wiederholungsrichtlinie implementiert. Es wird empfohlen, im Produktionscode Wiederholungsrichtlinien zu implementieren (z. B. einen exponentiellen Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler] beschrieben.

<!-- Links -->
[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

<!---HONumber=Oct15_HO3-->