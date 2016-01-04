

1.  Starten Sie auf dem Mac **Keychain Access**. Öffnen Sie **Kategorie** > **Meine Zertifikate**. Suchen Sie das zu exportierende SSL-Zertifikat (das Sie zuvor heruntergeladen haben), und legen Sie den Inhalt offen. Wählen Sie nur das Zertifikat ohne den privaten Schlüssel aus, und [exportieren Sie es](https://support.apple.com/kb/PH20122?locale=en_US).

2. Klicken Sie im Azure-Portal auf **Alle durchsuchen** > **App-Dienste** > Ihr Back-End > **Einstellungen** > **Mobil** > **Push** > **Erforderliche Einstellungen konfigurieren** > **+ Benachrichtigungs-Hub**, geben Sie einen Namen und einen Namespace für Ihren Benachrichtigungs-Hub ein, und klicken Sie dann auf die Schaltfläche **OK**.

  	![][1]

3. Klicken Sie auf dem Blatt **Create Notification Hub** auf die Schaltfläche **Erstellen**.
     
    Bevor Sie mit dem nächsten Schritt fortfahren, klicken Sie auf **Benachrichtigungen**, um sicherzustellen, dass die Einrichtung des Benachrichtigungs-Hubs abgeschlossen ist.

4. Klicken Sie im Azure-Portal auf **Alle durchsuchen** > **App-Dienste** > Ihr mobiles App-Back-End > **Einstellungen** > **Mobil** > **Push** > **Apple-Pushbenachrichtigungsdienste** > **Zertifikat hochladen**. Laden Sie die P12-Datei hoch, und wählen Sie den richtigen **Modus** aus (je nachdem, ob das von Ihnen erstellte Client-SSL-Zertifikat für die Entwicklung oder Verteilung galt). Ihr Dienst ist jetzt so konfiguriert, dass er mit Pushbenachrichtigungen unter iOS arbeitet.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png

<!---HONumber=AcomDC_1203_2015-->