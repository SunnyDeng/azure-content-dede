> [AZURE.SELECTOR]
- [C on Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C on Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C on mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## Übersicht über das Szenario

In diesem Szenario erstellen Sie ein Gerät, das die folgende Telemetrie an die [vorkonfigurierte Lösung][lnk-what-are-preconfig-solutions] für die Remoteüberwachung sendet:

- Externe Temperatur
- Interne Temperatur
- Luftfeuchtigkeit

Zur Vereinfachung erzeugt der Code auf dem Gerät Beispielwerte. Sie sollten das Beispiel jedoch erweitern, indem Sie echte Sensoren an Ihr Gerät anschließen und tatsächliche Telemetriedaten senden.

Um dieses Tutorial abzuschließen, benötigen Sie ein aktives Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion][lnk-free-trial].

## Vorbereitung

Bevor Sie Code für Ihr Gerät schreiben, sollten Sie Ihre vorkonfigurierte Lösung für die Remoteüberwachung und anschließend ein Gerät in dieser Lösung bereitstellen.

### Bereitstellen der vorkonfigurierten Lösung für die Remoteüberwachung

Das von Ihnen erstellte Gerät sendet Daten an eine Instanz der vorkonfigurierten Lösung für die [Remoteüberwachung][lnk-remote-monitoring]. Unter [Erste Schritte mit Azure IoT Suite][lnk-getstarted] finden Sie Informationen zum Bereitstellen der vorkonfigurierten Lösung zur Remoteüberwachung in Ihrem Azure-Konto:

1. Klicken Sie auf der Seite https://www.azureiotsuite.com/ auf **+**, um eine neue Lösung zu erstellen.

2. Klicken Sie auf **Auswählen** im Bereich **Remoteüberwachung**, um die neue Projektmappe zu erstellen.

3. Geben Sie auf der Seite **Remoteüberwachungslösung erstellen** den gewünschten **Lösungsnamen** ein, wählen Sie die **Region** aus, in der die Bereitstellung erfolgen soll, und dann das zu verwendende Azure-Abonnement. Klicken Sie dann auf **Lösung erstellen**.

4. Warten Sie, bis der Bereitstellungsvorgang abgeschlossen ist.

> [AZURE.WARNING] Die vorkonfigurierten Lösungen verwenden abrechenbare Azure-Dienste. Achten Sie darauf, die vorkonfigurierte Lösung aus dem Abonnement zu entfernen, wenn Sie fertig sind, um unnötige Gebühren zu vermeiden. Auf der Seite https://www.azureiotsuite.com/ können Sie eine vorkonfigurierte Lösung vollständig aus dem Abonnement entfernen.

Wenn die Remoteüberwachungslösung bereitgestellt wurde, klicken Sie auf **Starten**, um das Lösungsdashboard im Browser zu öffnen.

![][img-dashboard]

### Bereitstellen des Geräts in der Remoteüberwachungslösung

> [AZURE.NOTE] Wenn Sie bereits ein Gerät in der Lösung bereits bereitgestellt haben, können Sie diesen Schritt überspringen. Für das Erstellen der Clientanwendung müssen Sie die Anmeldeinformationen des Geräts kennen.

Damit ein Gerät eine Verbindung mit der vorkonfigurierten Lösung herstellen kann, muss es sich mit gültigen Anmeldeinformationen identifizieren können. Sie können die Anmeldeinformationen des Geräts aus dem Lösungsdashboard abrufen und dann in Ihre Clientanwendung einfügen.

Um Ihrer Remoteüberwachungslösung ein neues Gerät hinzuzufügen, führen Sie folgende Schritte im Lösungsdashboard durch:

1.  Klicken Sie in der linken unteren Ecke des Dashboards auf **Gerät hinzufügen**.

    ![][1]

2.  Klicken Sie im Bereich **Benutzerdefiniertes Gerät** auf **Neues Element hinzufügen**.

    ![][2]

3.  Wählen Sie **Let me define my own Device ID** aus, geben Sie eine Geräte-ID wie z. B. **mydevice** ein, klicken Sie auf **ID prüfen**, um zu prüfen, ob der Name schon verwendet wird, und klicken Sie dann auf **Erstellen**, um das Gerät bereitzustellen.

    ![][3]

5. Notieren Sie die Anmeldeinformationen des Geräts (Geräte-ID, IoT Hub-Hostname und Geräteschlüssel). Ihre Clientanwendung benötigt diese Informationen zum Herstellen einer Verbindung von Ihrem Gerät mit der Remoteüberwachungslösung. Klicken Sie anschließend auf **Fertig**.

    ![][4]

6. Stellen Sie sicher, dass das Gerät im Abschnitt mit den Geräten richtig angezeigt wird. Der Status lautet **Ausstehend** bis das Gerät eine Verbindung mit der Remoteüberwachungslösung hergestellt hat.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-getstarted]: https://www.azureiotsuite.com/
[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0211_2016-->