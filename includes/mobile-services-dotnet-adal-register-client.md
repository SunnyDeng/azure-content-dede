## <a name="register-app-aad"></a>Client-App in Azure Active Directory registrieren

1.  Navigieren Sie im [Azure-Verwaltungsportal][] zu **Active Directory**, und klicken Sie dann auf Ihr Verzeichnis.

 ![][]

2.  Klicken Sie oben auf die Registerkarte **Applications** und anschließend auf **ADD**, um eine App hinzuzufügen.

 ![][1]

3.  Klicken Sie auf **Add an application my organization is developing**.

4.  Geben Sie im Assisstenten zum Hinzufügen von Anwendungen einen **Namen** für Ihre Anwendung ein, und klicken Sie auf den Typ **Systemeigene Clientanwendung**. Klicken Sie dann auf Continue.

 ![][2]

5.  Geben Sie im Feld **Weiterleitungs-URI** den /login/done-Endpunkt für Ihren mobilen Dienst ein. Dieser Wert sollte so ähnlich lauten wie <https://todolist.azure-mobile.net/login/done>.

 ![][3]

6.  Klicken Sie auf die Registerkarte **Konfigurieren** für die systemeigene Anwendung, und kopieren Sie die **Client-ID**. Sie benötigen sie später.

 ![][4]

7.  Blättern Sie nach unten zum Abschnitt **Berechtigungen für andere Anwendungen**, und gewähren Sie Vollzugriff auf die zuvor registrierte Mobile Services-Anwendung. Klicken Sie dann auf **Speichern**

 ![][5]

Der mobile Dienst ist jetzt in AAD so konfiguriert, dass er SSO-Anmeldungen von Ihrer Anwendung empfangen kann.

  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  []: ./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png
  [1]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png
  [2]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png
  [3]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png
  [4]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png
  [5]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png
