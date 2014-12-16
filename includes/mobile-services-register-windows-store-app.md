
1. Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu [Anwendungsseite senden], melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **Anwendungsname**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Geben Sie einen Namen für Ihre Anwendung unter **Anwendungsname** ein, klicken Sie auf **Anwendungsname reservieren** und dann auf **Speichern**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3. Öffnen Sie in Visual Studio das Projekt, das Sie bei Ausführung des Lernprogramms [Erste Schritte mit Mobile Services] erstellt haben.

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **Anwendung dem Store zuordnen**. 

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	   	Daraufhin wird der Assistent zum **Zuordnen Ihrer Anwendung zum Windows Store** angezeigt.

5. Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto ein.

6. Wählen Sie die Anwendung aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und dann auf **Zuordnen**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)

Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

7. Klicken Sie auf der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf**Dienste**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. Klicken Sie auf der Seite "Dienste" auf **Live-Dienste-Website** unter **Azure Mobile Services**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

9. Notieren Sie sich in **App-Einstellungen** die Werte von **Client-ID**, **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[WACOM.NOTE]Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Informationen nicht weiter, und verteilen Sie sie nicht mit Ihrer Anwendung.

10. (Optional) Klicken Sie auf **API-Einstellungen**, aktivieren Sie **Erweiterte Umleitungssicherheit**, geben Sie den Wert `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` in **Umleitungs-URL** an, und klicken Sie auf **Speichern**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

	Dadurch wird die Microsoft-Kontoauthentifizierung für Ihre App aktiviert.

11. 	Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und anschließend auf Ihre App.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

12. Klicken Sie auf die Registerkarte **Push**, und geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** ein, die Sie von WNS in Schritt 4 erhalten haben. Klicken Sie dann auf **Speichern**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Klicken Sie auf die Registerkarte **Identität**. Beachten Sie, dass **Geheimer Clientschlüssel** und **Paket-SID** bereits durch den vorherigen Schritt Werte enthalten. Geben Sie die zuvor notierte **Client-ID** ein, und klicken Sie auf **Speichern**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
Sie können nun ein Microsoft-Konto für die Authentifizierung in Ihrer App verwenden.  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Übermitteln einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
