## Empfangen von Nachrichten vom simulierten Gerät

In diesem Abschnitt ändern Sie die simulierte Geräteanwendung, die Sie in [Erste Schritte mit IoT Hub] erstellt haben, um Cloud-zu-Gerät-Nachrichten von IoT Hub zu empfangen.

1. Fügen Sie in Visual Studio im **SimulatedDevice**-Projekt der **Program**-Klasse folgende Methode hinzu.
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    Die `ReceiveAsync`-Methode gibt die empfangene Nachricht asynchron zurück, sobald sie vom Gerät empfangen wird. Nach Ablauf eines festlegbaren Timeoutzeitraums (hier wird der Standardwert 1 Minute verwendet) gibt sie *NULL* zurück. Falls dies geschieht, soll der Code weiter auf neue Nachrichten warten, was durch die Zeile `if (receivedMessage == null) continue` gewährleistet ist.

    Durch den Aufruf von `CompleteAsync()` wird IoT Hub benachrichtigt, dass die Nachricht erfolgreich verarbeitet wurde und sicher aus der Gerätewarteschlange entfernt werden kann. Falls die Nachricht von der Geräte-App nicht vollständig verarbeitet werden kann, wird sie von IoT Hub erneut übermittelt. Dabei ist es wichtig, dass die Nachrichtenverarbeitungslogik in der Geräte-App *idempotent* ist, d. h., dass der mehrfache Empfang derselben Nachricht immer dasselbe Resultat ergeben muss. Eine Anwendung kann eine Nachricht auch vorübergehend verwerfen (`Abandon`), wodurch die Nachricht von IoT Hub zur zukünftigen Nutzung in der Warteschlange beibehalten werden muss, oder eine Nachricht ablehnen (`Reject`), wodurch sie dauerhaft aus der Warteschlange entfernt wird. Weitere Informationen zum Lebenszyklus von Cloud-zu-Gerät-Nachrichten finden Sie im [Entwicklungsleitfaden für IoT Hub][IoT Hub Developer Guide - C2D].

> [AZURE.NOTE]Wird HTTP/1 anstelle von AMQP als Transport verwendet, wird `ReceiveAsync` sofort zurückgegeben. Das unterstützte Muster für Cloud-zu-Gerät-Nachrichten mit HTTP/1 entspricht Geräten, die nicht permanent verbunden sind und weniger häufig Nachrichten abrufen (d. h. seltener als alle 25 Minuten). Die Ausgabe von mehr HTTP/1-Empfangsvorgängen führt dazu, dass die Anforderungen von IoT Hub gedrosselt werden. Weitere Informationen zu den Unterschieden zwischen der AMQP- und HTTP/1-Unterstützung sowie zur IoT Hub-Drosselung finden Sie im [Entwicklungsleitfaden für IoT Hub][IoT Hub Developer Guide - C2D].

2. Fügen Sie in der **Main**-Methode unmittelbar vor der Zeile `Console.ReadLine()` folgende Methode hinzu:

        ReceiveC2dAsync();

> [AZURE.NOTE]Der Einfachheit halber wird in diesem Lernprogramm keine Wiederholungsrichtlinie implementiert. Es wird empfohlen, im Produktionscode Wiederholungsrichtlinien zu implementieren (z. B. einen exponentiellen Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler] beschrieben.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=Oct15_HO3-->