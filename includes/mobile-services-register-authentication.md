
1. Klicken Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) auf **Mobile Services** > Ihr mobiler Dienst > **Dashboard**, und notieren Sie den **Mobile Service-URL**-Wert.

2. Registrieren Sie Ihre App bei einem oder mehreren der folgenden Authentifizierungsanbieter:
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
   * [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md). 
   
    Notieren Sie die Clientidentität und die Werte der geheimen Clientschlüssel, die vom Anbieter generiert wurden. Veröffentlichen Sie den geheimen Clientschlüssel nicht, und geben Sie ihn nicht weiter.

3. Klicken Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) auf **Mobile Services** > Ihr mobiler Dienst > **Identität** > Ihre Identitätsanbietereinstellungen, und geben Sie die Client-ID und den geheimen Clientschlüssel von Ihrem Anbieter ein. 
 
Sie haben jetzt sowohl Ihren mobilen Dienst als auch Ihre App so konfiguriert, dass sie mit dem ausgewählten Authentifizierungsanbieter funktionieren. Sie können optional alle Schritte für weitere Identitätsanbieter wiederholen, die Sie unterstützen möchten.

> [AZURE.IMPORTANT]Überprüfen Sie, ob Sie den richtigen URI für die Umleitung auf der Entwicklerwebsite Ihres Identitätsanbieters festgelegt haben. Wie in den verknüpften Anweisungen für die einzelnen Anbieter oben beschrieben, kann sich der Umleitungs-URI für einen .NET-Back-End-Dienst von einem JavaScript-Back-End-Dienst unterscheiden. Bei einem falsch konfigurierten Umleitungs-URI kann der Anmeldebildschirm möglicherweise nicht ordnungsgemäß angezeigt werden und die App weist unerwartete Störungen auf.

<!---HONumber=AcomDC_1203_2015-->