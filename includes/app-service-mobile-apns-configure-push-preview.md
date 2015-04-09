Nachdem Sie Ihre App bei APNS registriert und Sie Ihr Projekt konfiguriert haben, müssen Sie als Nächstes Ihre mobile App für die Integration mit APNS konfigurieren.

1. Klicken Sie in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neue Zertifikat der Schnellstart-App unter **Schlüssel** oder **Meine Zertifikate**, klicken Sie auf **Exportieren**, nennen Sie Ihre Datei "QuickstartPusher", wählen Sie das **.p12**-Format, und klicken Sie dann auf **Sichern**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

    Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

>[AZURE.NOTE] In diesem Lernprogramm wird eine Datei namens QuickstartPusher.p12 erstellt. Name und Ort Ihrer Datei können verschieden sein.

2. Melden Sie sich auf dem [Azure-Vorschauportal] an, wählen Sie **Durchsuchen**, **Mobile Apps**, und klicken Sie dann auf Ihre App. Klicken Sie auf Pushbenachrichtigungsdienste.

3. Laden Sie im Apple Push Notification Service Ihr Zertifikat mit der **.p12**-Datei und das von Ihnen zugeordneten Kennwort hoch, und wählen Sie den gewünschten Modus aus.

Ihre App Service-Mobile-App ist damit für APNS konfiguriert.

<!-- URLs. -->
[Azure-Vorschauportal]: https://portal.azure.com/

<!--HONumber=49-->