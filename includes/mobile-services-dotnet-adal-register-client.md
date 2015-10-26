## <a name="register-app-aad"></a>Registrieren der Client-App in Azure Active Directory

1. Navigieren Sie im **Azure-Verwaltungsportal** zu [Active Directory], und klicken Sie dann auf Ihr Verzeichnis.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

2. Klicken Sie oben auf die Registerkarte **Applications** und anschließend auf **ADD**, um eine App hinzuzufügen. 

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png)

3. Klicken Sie auf **Add an application my organization is developing**.

4. Geben Sie im Assistenten zum Hinzufügen von Anwendungen einen **Namen** für Ihre Anwendung ein, und klicken Sie auf den Typ **Systemeigene Clientanwendung**. Klicken Sie dann auf Continue.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png)

5. Geben Sie im Feld **Weiterleitungs-URI** den /login/done-Endpunkt für Ihren mobilen Dienst ein. Dieser Wert sollte etwa so aussehen: https://todolist.azure-mobile.net/login/done.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png)

6. Klicken Sie auf die Registerkarte **Konfigurieren** für die systemeigene Anwendung, und kopieren Sie die **Client-ID**. Sie benötigen sie später.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png)

7. Blättern Sie auf der Seite nach unten zum Abschnitt **Berechtigungen für andere Anwendungen**, und klicken Sie auf die Schaltfläche **Anwendung hinzufügen**. Wählen Sie **Andere** aus dem Menü "Anzeigen", und suchen Sie nach "todo". Klicken Sie auf **TodoList**, um den mobilen Dienst, den Sie zuvor registriert haben, hinzuzufügen, und klicken Sie zum Abschluss auf das Häkchen. Gewähren Sie Zugriff auf die mobile Dienstanwendung. Klicken Sie dann auf **Speichern**

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png)

Der mobile Dienst ist jetzt in AAD so konfiguriert, dass er SSO-Anmeldungen von Ihrer Anwendung empfangen kann.


[Active Directory]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO3-->