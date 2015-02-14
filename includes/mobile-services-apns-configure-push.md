Nachdem Sie Ihre App bei APNS registriert und Sie Ihr Projekt konfiguriert haben, müssen Sie als Nächstes Ihren mobilen Dienst für Integration mit APNS konfigurieren.

1. Klicken Sie in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neue Zertifikat der Schnellstart-App unter **Schlüssel** oder **Meine Zertifikate**, klicken Sie auf **Exportieren**, nennen Sie Ihre Datei "QuickstartPusher", wählen Sie das **.p12**-Format, und klicken Sie dann auf **Sichern**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

  Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

>[AZURE.NOTE] In diesem Lernprogramm wird eine Datei namens QuickstartPusher.p12 erstellt. Name und Ort Ihrer Datei können verschieden sein.

2. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre App.

   	![](./media/mobile-services-apns-configure-push/mobile-services-selection.png)

3. Klicken Sie auf die Registerkarte **Push** und dann auf **Hochladen**.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png)

	Der Dialog "Zertifikat hochladen" wird angezeigt.

4. Klicken Sie auf **Datei**, wählen Sie die exportierte Zertifikatsdatei QuickstartPusher.p12, geben Sie das **Kennwort** ein, vergewissern Sie sich, dass der richtige **Modus** gewählt ist (entweder "Dev/Sandbox" oder "Prod/Production"), klicken Sie auf das Häkchensymbol und dann auf **Speichern**.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png)

    > [AZURE.NOTE] In diesem Lernprogramm werden Entwicklungszertifikate verwendet.

Ihr mobiler Dienst ist nun für APNS konfiguriert.

<!-- URLs. -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=42-->
