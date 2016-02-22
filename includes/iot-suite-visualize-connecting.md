## Anzeigen der Gerätetelemetrie im Dashboard

Das Dashboard in der Remoteüberwachungslösung ermöglicht Ihnen das Anzeigen der Telemetrie, die Ihre Geräte an IoT Hub senden.

1. Wechseln Sie in Ihrem Browser zum Dashboard der Remoteüberwachungslösung, und klicken Sie im linken Bereich auf **Geräte**, um zur **Geräteliste** zu gelangen.

2. In der **Geräteliste** wird Ihnen nun angezeigt, dass das Gerät den Status **Wird ausgeführt** hat.

    ![][18]

3. Klicken Sie auf **Dashboard**, um zu diesem zurückzukehren. Wählen Sie Ihr Gerät in der Dropdownliste **Anzuzeigendes Gerät** aus, um seine Telemetrie anzuzeigen. Die Telemetrie der Beispielanwendung ist mit 50 Einheiten für die Innentemperatur, 55 Einheiten für die Außentemperatur und 50 Einheiten für die Luftfeuchtigkeit konfiguriert. Bitte beachten Sie, dass im Dashboard standardmäßig nur die Werte für Temperatur und die Luftfeuchtigkeit angezeigt werden.

    ![][img-telemetry]

## Senden eines Befehls an ein Gerät

Das Dashboard in der Remoteüberwachungslösung ermöglicht es Ihnen, IoT Hub anzuweisen, Befehle an Ihre Geräte zu senden. In der Remoteüberwachungslösung können Sie beispielsweise einen Befehl senden, mit dem die Innentemperatur eines Geräts eingestellt wird.

1. Klicken Sie im Dashboard der Remoteüberwachungslösung im linken Bereich auf **Geräte**, um zur **Geräteliste** zu gelangen.

2. Klicken Sie in der **Geräteliste** auf die **Geräte-ID** für Ihr Gerät.

3. Klicken Sie im Bereich **Gerätedetails** auf **Befehle**.

    ![][13]

4. Wählen Sie in der Dropdownliste **Befehl** die Option **SetTemperature** aus, und geben Sie dann unter **Temperatur** einen neuen Temperaturwert ein. Klicken Sie auf **Befehl senden**, um den Befehl an das Gerät zu senden.

    ![][14]

    > [AZURE.NOTE] Der Befehlsverlauf zeigt anfänglich den Befehlsstatus **Ausstehend** an. Wenn das Gerät den Befehl bestätigt, ändert sich der Status in **Erfolgreich**.

5. Stellen Sie auf dem Dashboard sicher, dass das Gerät jetzt 75 als den neuen Temperaturwert sendet.

## Nächste Schritte

Der Artikel [Anpassen vorkonfigurierter Lösungen][lnk-customize] beschreibt einige Möglichkeiten zum Erweitern dieses Beispiels. Mögliche Erweiterungen umfassen die Verwendung echter Sensoren und die Implementierung zusätzlicher Befehle.

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-dev-messaging]: ../articles/iot-hub/iot-hub-devguide.md#messaging

<!---HONumber=AcomDC_0211_2016-->