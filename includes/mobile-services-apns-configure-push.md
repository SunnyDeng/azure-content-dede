Nachdem Sie Ihre App bei APNS registriert und Sie Ihr Projekt konfiguriert haben, müssen Sie als Nächstes Ihren mobilen Dienst für Integration mit APNS konfigurieren.

1.  In der Schlüsselbundverwaltung klicken Sie mit der rechten Maustaste auf das neue Zertifikat, klicken Sie auf **Exportieren**, nennen Sie Ihre Datei "QuickstartPusher", wählen Sie das **.p12**-Format, und klicken Sie dann auf **Sichern**.

    ![][]
Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

 > [WACOM.NOTE] In diesem Lernprogramm wird eine QuickstartPusher.p12-Datei erstellt. Name und Ort Ihrer Datei können verschieden sein.

2.  Melden Sie sich beim [Azure-Verwaltungsportal][] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

    ![][1]

3.  Klicken Sie auf die Registerkarte **Push** und dann auf **Hochladen**.

    ![][2]

    Der Dialog "Zertifikat hochladen" wird angezeigt.

4.  Klicken Sie auf **Datei**, wählen Sie die exportierte QuickstartPusher.p12-Zertifikatsdatei, geben Sie das **Kennwort** ein, vergewissern Sie sich, dass der richtige **Modus** gewählt ist, klicken Sie auf das Häkchensymbol und dann auf **Speichern**.

    ![][3]

    > [WACOM.NOTE] In diesem Lernprogramm werden Entwicklungszertifikate verwendet.

Ihr mobiler Dienst ist nun für APNS konfiguriert.

<!-- URLs. -->

  []: ./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-apns-configure-push/mobile-services-selection.png
  [2]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png
  [3]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png
