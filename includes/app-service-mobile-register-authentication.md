

Sie müssen Ihre App bei einem Identitätsanbieter registrieren, damit Sie Benutzer authentifizieren können. Sie müssen dann den vom Anbieter generierten geheimen Clientschlüssel bei Ihrem App Service registrieren.

1. Melden Sie sich beim [Azure-Vorschauportal] an, klicken Sie auf **Durchsuchen** und **Ressourcengruppe**, und wählen Sie dann die Ressourcengruppe der mobilen App aus.

2. Wählen Sie das Gateway aus, und notieren Sie den Wert der **URL** unter **Eigenschaften**. Möglicherweise müssen Sie diesen Wert an den Identitätsanbieter übermitteln, wenn Sie Ihre App registrieren.

   	![](./media/app-service-mobile-register-authentication/gateway-uri.png)

3. Wählen Sie aus der Liste unten einen unterstützten Identitätsanbieter aus, und folgen Sie den unten aufgeführten Schritten, um Ihre App bei diesem Anbieter zu konfigurieren:

 - <a href="/de-de/documentation/articles/app-service-mobile-how-to-configure-active-directory-authentication-preview/" target="_blank">Azure Active Directory</a>
 - <a href="/de-de/documentation/articles/app-service-mobile-how-to-configure-facebook-authentication-preview/" target="_blank">Facebook-Anmeldung</a>
 - <a href="/de-de/documentation/articles/app-service-mobile-how-to-configure-google-authentication-preview/" target="_blank">Google-Anmeldung</a>
 - <a href="/de-de/documentation/articles/app-service-mobile-how-to-configure-microsoft-authentication-preview/" target="_blank">Microsoft-Konto</a>
 - <a href="/de-de/documentation/articles/app-service-mobile-how-to-configure-twitter-authentication-preview/" target="_blank">Twitter-Anmeldung</a>

	Die Anwendung ist jetzt für die Arbeit mit dem ausgewählten Authentifizierungsanbieter konfiguriert.

4. (Optional) Wiederholen Sie den vorherigen Schritt zum Konfigurieren weiterer Identitätsanbieter, die Ihre App unterstützen soll. 

<!-- URLs. -->
[Azure-Vorschauportal]: https://portal.azure.com/

<!---HONumber=August15_HO6-->