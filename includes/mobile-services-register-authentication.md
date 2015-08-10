
* Klicken Sie auf "Azure-Portal" \> **Mobile Services** \> "Ihr mobiler Dienst" \> **Dashboard**, und notieren Sie den Wert unter **Mobile Service-URL**.

* Registrieren Sie Ihre App mit [Google](mobile-services-how-to-register-google-authentication.md), [Facebook](mobile-services-how-to-register-facebook-authentication.md), [Twitter](mobile-services-how-to-register-twitter-authentication.md), [Microsoft](mobile-services-how-to-register-microsoft-authentication.md) oder [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md). Notieren Sie die Clientidentität und die Werte der geheimen Clientschlüssel, die vom Anbieter generiert wurden. Veröffentlichen Sie den geheimen Clientschlüssel nicht, und geben Sie ihn nicht weiter.

* Klicken Sie auf "Azure-Portal" \> **Mobile Services** \> Ihren mobilen Dienst \> **Identität** \> Einstellungen Ihres Identitätsanbieters. Geben Sie die App-ID und den geheimen Schlüssel vom Anbieter ein. Sie haben jetzt sowohl Ihren mobilen Dienst als auch Ihre App so konfiguriert, dass sie mit dem ausgewählten Authentifizierungsanbieter funktionieren. Sie können optional alle Schritte für weitere Identitätsanbieter wiederholen, die Sie unterstützen möchten.

    > [AZURE.IMPORTANT]Überprüfen Sie, ob Sie den richtigen URI für die Umleitung auf der Entwicklerwebsite Ihres Identitätsanbieters festgelegt haben. Wie in den verknüpften Anweisungen für die einzelnen Anbieter oben beschrieben, kann sich der Umleitungs-URI für einen .NET-Back-End-Dienst von einem JavaScript-Back-End-Dienst unterscheiden. Bei einem falsch konfigurierten Umleitungs-URI kann der Anmeldebildschirm möglicherweise nicht ordnungsgemäß angezeigt werden und die App weist unerwartete Störungen auf.

<!---HONumber=July15_HO5-->