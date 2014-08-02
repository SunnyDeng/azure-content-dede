

Sie müssen Ihre App bei einem Identitätsanbieter registrieren, damit Sie Benutzer authentifizieren können. Sie müssen dann den vom Anbieter generierten geheimen Clientschlüssel bei Mobile Services registrieren.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

      ![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich den Wert der **URL des mobilen Service**.

       ![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    Möglicherweise müssen Sie diesen Wert an den Identitätsanbieter übermitteln, wenn Sie Ihre App registrieren.

3.  Wählen Sie aus der Liste unten einen unterstützten Identitätsanbieter aus, und folgen Sie den unten aufgeführten Schritten, um Ihre App bei diesem Anbieter zu registrieren:

 - [Microsoft Account](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
 - [Facebook-Login](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
 - [Twitter-Login](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
 - [Google-Login](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
 - [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    Notieren Sie die Clientidentität und die geheimen Werte, die vom Anbieter generiert wurden.

    **Sicherheitshinweis**

    Der vom Anbieter generierte geheime Schlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

1.  Klicken Sie dann im Verwaltungsportal auf die Registerkarte **Identität**, geben Sie den App-Bezeichner und die freigegebenen geheimen Werte ein, die Sie von Ihrem Identitätsanbieter erhalten haben, und klicken Sie auf **Speichern**.

      ![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

    Sowohl Ihr mobiler Dienst als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.


