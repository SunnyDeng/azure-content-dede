
1. Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu [Anwendungsseite senden], melden Sie sich mit Ihrem Microsoft-Konto an und klicken Sie dann auf **Anwendungsname**.

   ![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Wählen Sie **Eine neue App erstellen, indem Sie einen eindeutigen Namen reservieren** und klicken Sie auf **Weiter**. Geben Sie dann einen Namen für Ihre App in **App-Name** ein, klicken Sie auf **App-Name reservieren**, und klicken Sie dann auf **Speichern**.

   ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3. Öffnen Sie in Visual Studio das Projekt, das Sie beim Ausführen des Lernprogramms [Erste Schritte mit Mobile Services] erstellt haben.

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Windows Store-App-Projekt, klicken Sie auf **Store**, und klicken Sie dann auf **App mit Store verknüpfen**.

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	Auf diese Weise wird der Assistent zum **Zuordnen Ihrer Anwendung zum Windows Store** angezeigt.

5. Klicken Sie im Assistenten auf **Anmelden** und melden Sie sich dann mit Ihrem Microsoft-Konto an. Wählen Sie die App aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und anschließend auf **Zuordnen**.

   	Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.

6. (Optional) Für universelle Windows-Apps wiederholen Sie die Schritte 4 und 5 für das Windows Phone Store-Projekt.

6. Klicken Sie auf der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf **Dienste**.

   ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png)

7. Klicken Sie auf der Seite "Dienste" auf **Live-Dienste-Website** unter **Azure Mobile Services**.

![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

8. Klicken Sie auf **API-Einstellungen**, aktivieren Sie das Kontrollkästchen **Mobile oder Desktop-Client-App**, legen Sie die mobile Dienst-URL als **Zieldomäne** fest, geben Sie den Wert `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/` in die **Umleitungs-URL** ein, und klicken Sie anschließend auf **Speichern**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

9. Notieren Sie sich unter **App-Einstellungen** die Werte von **Client-ID**, **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**.

   ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Informationen nicht weiter und verteilen Sie sie nicht mit Ihrer Anwendung.

10. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

11. Klicken Sie auf die Registerkarte **Identität**, geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** ein, die Sie von WNS in Schritt 4 erhalten haben. Klicken Sie dann auf **Speichern**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Klicken Sie auf die Registerkarte **Identität**. Beachten Sie, dass **Geheimer Clientschlüssel** und **Paket-SID** bereits durch den vorherigen Schritt Werte enthalten. Geben Sie die zuvor notierte **Client-ID** ein, und klicken Sie auf **Speichern**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
Sie können nun ein Microsoft-Konto für die Authentifizierung in Ihrer App verwenden.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Anwendungsseite senden]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=54-->