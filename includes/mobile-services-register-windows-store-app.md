
1. Wenn Sie Ihre App noch nicht registriert haben, navigieren Sie zu [Eine App-Seite abschicken] im "Dev-Center" für Windows Store-Apps, melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **App-Name**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Geben Sie in **App-Name** einen Namen für Ihre App ein, klicken Sie auf **App-Name reservieren**, und klicken Sie dann auf **Speichern**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3. Öffnen Sie in Visual Studio das Projekt, das Sie nach Beenden des Lernprogramms [Erste Schritte mit Mobile Services] erstellt haben.

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Speichern**, und klicken Sie dann auf **App mit Store verknüpfen...**. 

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	Der Assistent **App mit Windows Store verknüpfen** wird angezeigt.

5. Klicken Sie im Assistenten auf **Anmelden**, und melden Sie sich anschließend mit Ihrem Microsoft-Konto an.

6. Wählen Sie die App, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter**, und klicken Sie dann auf **Zuordnen**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)

   	Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

7. Klicken Sie in der Seite von Windows Dev Center für Ihre neue App auf **Dienste**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. Klicken Sie auf der Seite "Dienste" auf **Live Services-Website** unter**Azure Mobile Services**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

9. Notieren Sie in **App-Einstellungen** die Werte für **Client-ID**, **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Informationen nicht weiter und verteilen Sie sie nicht mit Ihrer Anwendung.

10. (Optional) Klicken Sie auf **API-Einstellungen**, aktivieren Sie **Bessere Umleitungssicherheit**, geben Sie den Wert `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` in **Umleitungs-URL** ein, klicken Sie dann auf **Speichern**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

	Dadurch wird die Microsoft-Kontoauthentifizierung für Ihre App aktiviert.

11. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre App.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

12. Klicken Sie auf die Registerkarte **Push**, geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** an, die Sie in Schritt 4 aus WNS erhalten haben, und klicken Sie dann auf **Speichern**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Klicken Sie auf die Registerkarte **Identität**. Beachten Sie, dass die Werte für **Geheimer Clientschlüssel** und **Paket-SID** bereits im vorherigen Schritt festgelegt wurden. Geben Sie die zuvor notierte **Client-ID** ein, und klicken Sie dann auf **Speichern**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
Sie können nun ein Microsoft-Konto für die Authentifizierung in Ihrer App verwenden.  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
<!--HONumber=42-->
