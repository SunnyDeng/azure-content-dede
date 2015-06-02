

Registrieren Sie Ihre App zum Authentifizieren von Benutzern bei einem Identitätsanbieter. Registrieren Sie dann die vom Anbieter generierten Anmeldeinformationen des Clients mit Azure Mobile Services.

1. Melden Sie sich beim Azure-Verwaltungsportal an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

2. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich den Wert von **Mobile Service URL**. Möglicherweise müssen Sie diesen Wert an den Identitätsanbieter übermitteln, wenn Sie Ihre App registrieren.

3. Wählen Sie einen unterstützten Identitätsanbieter aus der Liste unten aus. Befolgen Sie die Schritte zum Registrieren Ihrer App mit diesem Anbieter. Notieren Sie die Clientidentität und die geheimen Werte, die vom Anbieter generiert wurden.

 - <a href="/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Microsoft-Konto</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google </a>
 - <a href="/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>

    > [AZURE.IMPORTANT]Der vom Anbieter generierte geheime Schlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

4. Klicken Sie dann im Verwaltungsportal auf die Registerkarte **Identität**, geben Sie den App-Bezeichner und die freigegebenen geheimen Werte ein, die Sie von Ihrem Identitätsanbieter erhalten haben, und klicken Sie auf **Speichern**. Sowohl Ihr mobiler Dienst als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.

    > [AZURE.IMPORTANT]Überprüfen Sie, ob Sie den richtigen URI für die Umleitung auf der Entwicklerwebsite Ihres Identitätsanbieters festgelegt haben. Wie in den verknüpften Anweisungen für die einzelnen Anbieter oben beschrieben, kann sich der Umleitungs-URI für einen .NET-Back-End-Dienst von einem JavaScript-Back-End-Dienst unterscheiden. Bei einem falsch konfigurierten Umleitungs-URI kann der Anmeldebildschirm möglicherweise nicht ordnungsgemäß angezeigt werden und die App weist unerwartete Störungen auf.

5. (Optional) Wiederholen Sie die Schritte 3 und 4 zum Konfigurieren weiterer Identitätsanbieter, die Ihre App unterstützen soll.

<!--HONumber=54-->