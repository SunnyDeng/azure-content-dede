
Zunächst müssen Sie Ihre Anwendung auf der Website eines Identitätsanbieters registrieren. Danach können Sie die Anmeldeinformationen in Ihrem Mobile Service festlegen.

1. Navigieren Sie im [Azure-Verwaltungsportal] zu Ihrem Mobile Service. Klicken Sie auf **Dashboard**, und notieren Sie den Wert unter **Mobile Service-URL**.

2. Registrieren Sie Ihre Anwendung bei einem der folgenden unterstützten Identitätsanbieter.

	* [Google](mobile-services-how-to-register-google-authentication.md)
	* [Facebook](mobile-services-how-to-register-facebook-authentication.md)
	* [Twitter](mobile-services-how-to-register-twitter-authentication.md)
	* [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
	* [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md).  
	
    >[AZURE.IMPORTANT]Stellen Sie sicher, dass die Umleitungs-URI für Ihren Mobile Service auf der Entwicklerwebsite des Identitätsanbieters korrekt eingestellt ist. Wie in den verknüpften Anweisungen für die einzelnen Anbieter oben beschrieben, unterscheidet sich der Pfad der Umleitungs-URI für einen mobilen .NET-Back-End-Dienst (`/signin-<provider>`) von einem mobilen JavaScript-Back-End-Dienst (`/login/<provider>`). Eine falsch konfigurierte Umleitungs-URI verhindert, dass sich der Client in Ihrer Anwendung anmelden kann. <br/>Veröffentlichen Sie den geheimen Clientschlüssel nicht, und geben Sie ihn nicht weiter.

3. Klicken Sie in Ihrem Mobile Service im [Azure-Verwaltungsportal] auf die **Identität**-Registerkarte, und geben Sie die Anwendungs-ID und den geheimen App-Schlüssel, den Sie von Ihrem Identitätsanbieter erhalten haben, ein.

Nach der Konfiguration der Anwendung und des Mobile Service zur Unterstützung eines Identitätsanbieters für die Authentifizierung können Sie diese Schritte wiederholen, um weitere Identitätsanbieter zu unterstützen.

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!---HONumber=62-->