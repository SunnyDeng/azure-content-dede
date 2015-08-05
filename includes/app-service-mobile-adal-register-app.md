1. Registrieren Sie Ihr Mobile App-Back-End mit Ihrem Azure Active Directory-Mandanten anhand der Anweisungen im Thema [Konfigurieren der Mobile App mit Azure Active Directory].

2. Navigieren Sie im **Azure-Verwaltungsportal** zu [Active Directory].

   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)

3. Wählen Sie Ihr Verzeichnis und anschließend die Registerkarte **Anwendungen** im oberen Bereich aus. Klicken Sie im unteren Bereich auf **HINZUFÜGEN**, um eine neue App-Registrierung zu erstellen. 

4. Klicken Sie auf **Von meiner Organisation entwickelte Anwendung hinzufügen**.

5. Geben Sie im Assistenten zum Hinzufügen von Anwendungen einen **Namen** für Ihre Anwendung ein, und klicken Sie auf den Typ **Systemeigene Clientanwendung**. Klicken Sie dann auf "Weiter".

6. Geben Sie im Feld **Umleitungs-URI** den /login/done-Endpunkt für Ihr App Service-Gateway ein. Dieser Wert sollte etwa so aussehen: https://contoso.azurewebsites.net/login/done.

7. Nachdem die systemeigene Anwendung hinzugefügt wurde, klicken Sie auf die Registerkarte **Konfigurieren**. Kopieren Sie die **Client-ID**. Sie benötigen sie später.

8. Führen Sie auf der Seite einen Bildlauf nach unten zum Abschnitt **Berechtigungen für andere Anwendungen** durch, und klicken Sie auf **Anwendung hinzufügen**.

9. Suchen Sie nach der zuvor registrierten Web-App, und klicken Sie auf das Pluszeichen. Klicken Sie dann auf das Häkchen, um das Dialogfeld zu schließen.

10. Öffnen Sie für den neu erstellten Eintrag das Dropdownmenü **Berechtigungen der Stellvertretung**, und wählen Sie **Zugriff (App-Name)** aus. Klicken Sie dann auf **Speichern**.

   ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

Die Anwendung wird jetzt in AAD so konfiguriert, dass Benutzer über AAD die einmalige Anmeldung nutzen können.

[Active Directory]: https://manage.windowsazure.com/
[Konfigurieren der Mobile App mit Azure Active Directory]: ../articles/app-service-how-to-configure-active-directory-authentication-preview.md

<!---HONumber=July15_HO4-->