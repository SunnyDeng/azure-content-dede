Sie müssen Ihre App bei einem Identitätsanbieter registrieren, damit Sie Benutzer authentifizieren können. Sie müssen dann den vom Anbieter generierten geheimen Clientschlüssel bei Mobile Services registrieren.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

    ![][0]

2.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich den Wert der **URL des mobilen Service**.

    ![][1]

    Möglicherweise müssen Sie diesen Wert an den Identitätsanbieter übermitteln, wenn Sie Ihre App registrieren.

3.  Wählen Sie aus der Liste unten einen unterstützten Identitätsanbieter aus, und folgen Sie den unten aufgeführten Schritten, um Ihre App bei diesem Anbieter zu registrieren:

-   [Microsoft Account][Microsoft Account]
-   [Facebook-Login][Facebook-Login]
-   [Twitter-Login][Twitter-Login]
-   [Google-Login][Google-Login]
-   [Azure Active Directory][Azure Active Directory]

    Notieren Sie die Clientidentität und die geheimen Werte, die vom Anbieter generiert wurden.

    <div class="dev-callout"><b>Sicherheitshinweis</b>
<p>Der vom Anbieter generierte geheime Schl&uuml;ssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schl&uuml;ssel mit niemandem, und geben Sie ihn nicht &uuml;ber Ihre App frei.</p>
</div>

1.  Klicken Sie dann im Verwaltungsportal auf die Registerkarte **Identität**, geben Sie den App-Bezeichner und die freigegebenen geheimen Werte ein, die Sie von Ihrem Identitätsanbieter erhalten haben, und klicken Sie auf **Speichern**.

    ![][2]

    Sowohl Ihr mobiler Dienst als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.



  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-register-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-register-authentication/mobile-service-uri.png
  [Microsoft Account]: /de-de/documentation/articles/mobile-services-how-to-register-microsoft-authentication/
  [Facebook-Login]: /de-de/documentation/articles/mobile-services-how-to-register-facebook-authentication/
  [Twitter-Login]: /de-de/documentation/articles/mobile-services-how-to-register-twitter-authentication/
  [Google-Login]: /de-de/documentation/articles/mobile-services-how-to-register-google-authentication/
  [Azure Active Directory]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [2]: ./media/mobile-services-register-authentication/mobile-identity-tab.png
