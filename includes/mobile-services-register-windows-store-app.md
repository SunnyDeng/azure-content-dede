1.  Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu [Anwendungsseite senden][Anwendungsseite senden], melden Sie sich mit Ihrem Microsoft-Konto an und klicken Sie dann auf **Anwendungsname**.

    ![][0]

2.  Geben Sie einen Namen für Ihre Anwendung unter **Anwendungsname** ein, klicken Sie auf **Anwendungsname reservieren** und dann auf **Speichern**.

    ![][1]

    Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3.  Öffnen Sie in Visual Studio das Projekt, das Sie beim Ausführen des Lernprogramms [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] erstellt haben.

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **Anwendung dem Store zuordnen**.

    ![][2]

    Auf diese Weise wird der Assistent zum **Zuordnen Ihrer Anwendung zum Windows Store** angezeigt.

5.  Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto ein.

6.  Wählen Sie die Anwendung aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und dann auf **Zuordnen**.

    ![][3]

    Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.

7.  Klicken Sie auf der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf **Dienste**.

    ![][4]

8.  Klicken Sie auf der Seite "Dienste" auf **Live-Dienste-Website** unter **Azure Mobile Services**.

    ![][5]

9.  Notieren Sie sich unter **App-Einstellungen** die Werte von **Client-ID**, **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**.

    ![][6]

    > [WACOM.NOTE]Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Informationen nicht weiter und verteilen Sie sie nicht mit Ihrer Anwendung.

10. (Optional) Klicken Sie auf **API-Einstellungen**, aktivieren Sie **Erweiterte Umleitungssicherheit**, geben Sie in **Umleitungs-URL** den Wert `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` an, und klicken Sie auf **Speichern**.

    ![][7]

    Dadurch wird die Microsoft-Kontoauthentifizierung für Ihre App aktiviert.

11. Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

    ![][8]

12. Klicken Sie auf die Registerkarte **Push**, geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** ein, die Sie von WNS in Schritt 4 erhalten haben. Klicken Sie dann auf **Speichern**.

    ![][9]

13. Klicken Sie auf die Registerkarte **Identität**. Beachten Sie, dass **Geheimer Clientschlüssel** und **Paket-SID** bereits durch den vorherigen Schritt Werte enthalten. Geben Sie die zuvor notierte **Client-ID** ein, und klicken Sie auf **Speichern**.

    ![][10]

Sie können nun ein Microsoft-Konto für die Authentifizierung in Ihrer App verwenden.

  

  [Anwendungsseite senden]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
  [2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [7]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [8]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
  [9]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png
  [10]: ./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png
