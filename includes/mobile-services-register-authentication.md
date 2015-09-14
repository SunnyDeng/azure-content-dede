
1. Klicken Sie auf "Azure-Portal" > **Mobile Services** > "Ihr mobiler Dienst" > **Dashboard**, und notieren Sie den Wert unter **Mobile Service-URL**.

2. Registrieren Sie Ihre App bei einem oder mehreren der folgenden Authentifizierungsanbieter:
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
   * [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md). 
   
   Notieren Sie die Clientidentität und die Werte der geheimen Clientschlüssel, die vom Anbieter generiert wurden. Veröffentlichen Sie den geheimen Clientschlüssel nicht, und geben Sie ihn nicht weiter.

3. Klicken Sie im Azure-Portal auf **Mobile Services** > Ihr mobiler Dienst > **Identität** > Ihre Identitätsanbietereinstellungen, und geben Sie die Client-ID und den geheimen Clientschlüssel von Ihrem Anbieter ein. 
 
Sie haben jetzt sowohl Ihren mobilen Dienst als auch Ihre App so konfiguriert, dass sie mit dem ausgewählten Authentifizierungsanbieter funktionieren. Sie können optional alle Schritte für weitere Identitätsanbieter wiederholen, die Sie unterstützen möchten.

    > [AZURE.IMPORTANT] Verify that you've set the correct redirect URI on your identity provider's developer site. As described in the linked instructions for each provider above, the redirect URI may be different for a .NET backend service vs. for a JavaScript backend service. An incorrectly configured redirect URI may result in the login screen not being displayed properly and the app malfunctioning in unexpected ways.

<!---HONumber=September15_HO1-->