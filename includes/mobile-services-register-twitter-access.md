

Die neuen Twitter v1.1 APIs erfordern eine Authentifizierung Ihrer App, um auf Ressourcen zugreifen zu können. Zunächst müssen Sie sich die Anmeldedaten für Zugriff mithilfe von OAuth 2.0 besorgen. Anschließend speichern Sie diese sicher in den App-Einstellungen für Ihren mobilen Dienst.

1. Sofern noch nicht geschehen, führen Sie die Schritte im Thema <a href="/de-de/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services</a> durch. 
  
  	Twitter generiert die Anmeldedaten, mit deren Hilfe Sie auf Twitter v1.1 APIs zugreifen können. Diese Anmeldeinformationen erhalten Sie über die Website für Twitter-Entwickler. 

2. Navigieren Sie zur Website für <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter-Entwickler</a>, melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, navigieren Sie zu **My Applications**, und wählen Sie Ihre Twitter-App aus.

    ![](./media/mobile-services-register-twitter-access/mobile-twitter-my-apps.png)

3. Notieren Sie sich au f der Registerkarte **Details** für die App die folgenden Werte:

	+ **Consumer key**
	+ **Consumer secret**
	+ **Access token**
	+ **Access token secret**

	![](./media/mobile-services-register-twitter-access/mobile-twitter-app-secrets.png)

4. Melden Sie sich beim [Windows Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und anschließend auf Ihren mobilen Dienst.

5. Klicken Sie auf die Registerkarte **Identityät**, geben Sie die von Twitter erhaltenen Werte für **Consumer key** und **Consumer secret** ein, und klicken Sie dann auf click **Speichern**. 

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Klicken Sie auf die Registerkarte **Konfigurieren**, scrollen Sie nach unten zu **App-Einstellungen**, und geben Sie ein Paar aus **Name** und **Wert** für jedes der folgenden Elemente ein, die Sie von der Twitter-Site erhalten haben. Klicken Sie anschließend auf **Speichern**.

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	Dadurch wird der Twitter-Zugriffstoken in den App-Einstellungen gespeichert. Wie die Benutzeranmeldeinformationen auf der Registerkarte **Identität** werden auch die Zugangsberechtigungsdaten verschlüsselt in den App-Einstellungen gespeichert, sodass Sie auf diese in Ihren Serverskripts zugreifen können, ohne die Daten hart in der Skriptdatei codieren zu müssen. Weitere Informationen finden Sie unter [App-Einstellungen].

<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Windows Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services]: /de-de/documentation/articles/mobile-services-how-to-register-twitter-authentication
[Twitter-Entwickler]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App-Einstellungen]: http://msdn.microsoft.com/de-de/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
