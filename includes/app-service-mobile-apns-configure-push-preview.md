Nachdem Sie Ihre App bei APNS registriert und Ihr Projekt konfiguriert haben, müssen Sie als Nächstes Ihre mobile App für die Integration mit APNS konfigurieren.

1. Klicken Sie in Schlüsselbund mit der rechten Maustaste auf das neue Zertifikat der Schnellstart-App in **Schlüssel** oder **Meine Zertifikate**, klicken Sie auf **Exportieren**, nennen Sie die Datei "QuickstartPusher", wählen Sie das Format **.p12** aus, und klicken Sie auf **Speichern**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

    Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

>[AZURE.NOTE]In diesem Lernprogramm wird eine Datei namens QuickstartPusher.p12 erstellt. Name und Ort Ihrer Datei können verschieden sein.

2. Melden Sie sich beim [Azure-Vorschauportal] an, wählen Sie **Durchsuchen**, **Mobile Apps**, und klicken Sie dann auf Ihre App. Klicken Sie auf "Pushbenachrichtigungsdienste".

3. Laden Sie im Apple Push Notification Service Ihr Zertifikat mit der **.p12**-Datei und das von Ihnen zugeordnete Kennwort hoch, und wählen Sie den gewünschten Modus aus.

Ihre mobile App Service-App ist damit für APNS konfiguriert.

<!-- URLs. -->
[Azure-Vorschauportal]: https://portal.azure.com/

<!--HONumber=54-->