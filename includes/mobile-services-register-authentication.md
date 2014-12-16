

Sie müssen Ihre App bei einem Identitätsanbieter registrieren, damit Sie Benutzer authentifizieren können. Dann müssen Sie den vom Anbieter generierten geheimen Clientschlüssel bei Mobile Services registrieren.

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und anschließend auf Ihren mobilen Dienst.

   	![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich den Wert von **Mobile Service URL**.

   	![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    Möglicherweise müssen Sie diesen Wert an den Identitätsanbieter übermitteln, wenn Sie Ihre App registrieren.

3. Wählen Sie aus der Liste unten einen unterstützten Identitätsanbieter aus, und folgen Sie den unten aufgeführten Schritten, um Ihre App bei diesem Anbieter zu registrieren:

 - <a href="/de-de/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Microsoft Account</a>
 - <a href="/de-de/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook-Login</a>
 - <a href="/de-de/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter-Login</a>
 - <a href="/de-de/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google-Login</a>
 - <a href="/de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Notieren Sie die Clientidentität und die geheimen Werte, die vom Anbieter generiert wurden.

    <div class="dev-callout"><b>Sicherheitshinweis</b>
	<p>Der vom Anbieter generierte geheime Schlüssel ist eine wichtige Sicherheitsanmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App weiter.</p>
    </div>

4. Klicken Sie dann im Verwaltungsportal auf die Registerkarte **Identityät**, geben Sie den App-Bezeichner und die freigegebenen geheimen Werte ein, die Sie von Ihrem Identitätsanbieter erhalten haben, und klicken Sie auf **Speichern**.

   	![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

	Sowohl Ihr mobiler Dienst als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.

<!-- URLs. -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
