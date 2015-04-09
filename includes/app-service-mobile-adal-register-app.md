1. Registrieren Sie Ihr Mobile App-Back-End mit Ihrem Azure Active Directory-Mandanten anhand der Anweisungen im Thema zum [Konfigurieren der Mobile App mit Azure Active Directory].

2. Navigieren Sie im [Azure-Verwaltungsportal] zu **Active Directory**.

   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)

3. Wählen Sie Ihr Verzeichnis aus und dann oben die Registerkarte **Anwendungen**. Klicken Sie unten auf **Hinzufügen**, um eine neue App-Registrierung zu erstellen. 

4. Klicken Sie auf **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**.

5. Geben Sie im Assistenten zum Hinzufügen von Anwendungen einen **Namen** für Ihre Anwendung ein, und klicken Sie auf den Typ **Systemeigene Clientanwendung**. Klicken Sie dann, um den Vorgang fortzusetzen.

6. Geben Sie im Feld **Umleitungs-URI** den /login/done-Endpunkt für Ihr App Service-Gateway ein. Dieser Wert sollte so ähnlich lauten wie https://contoso.azurewebsites.net/login/done.

7. Nachdem die systemeigene Anwendung hinzugefügt wurde, klicken Sie auf die Registerkarte **Konfigurieren**. Kopieren Sie die **Client-ID**. Sie benötigen sie später.

8. Blättern Sie auf der Seite nach unten zum Abschnitt **Berechtigungen für andere Anwendungen**, und klicken Sie auf **Anwendung hinzufügen**.

9. Suchen Sie nach der zuvor registrierten Web-App, und klicken Sie auf das Plussymbol. Klicken Sie dann auf das Häkchen, um das Dialogfeld zu schließen.

10. Öffnen Sie für den neu erstellten Eintrag das Dropdownmenü mit den **delegierten Berechtigungen**, und wählen Sie **Zugriff (Anwendungsname)** aus. Klicken Sie dann auf **Speichern**.

   ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

Die Anwendung wird jetzt in AAD so konfiguriert, dass Benutzer mit dem einmaligen Anmelden von AAD anmelden können.

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[So konfigurieren Sie die Mobile App mit Azure Active Directory]: ../articles/app-service-how-to-configure-active-directory-authentication-preview.md

<!--HONumber=49-->