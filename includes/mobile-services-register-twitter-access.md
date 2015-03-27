

Die neuen Twitter v1.1 APIs erfordern eine Authentifizierung Ihrer App, um auf Ressourcen zugreifen zu können. Zunächst müssen Sie sich die Anmeldedaten für Zugriff mithilfe von OAuth 2.0 besorgen. Anschließend speichern Sie diese sicher in den App-Einstellungen für Ihren mobilen Service.

1. Wenn Sie dies nicht bereits getan haben, führen Sie die Schritte im Thema <a href="/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services</a> aus. 
  
  	Twitter erstellt die erforderlichen Anmeldeinformationen, damit Sie auf Twitter v1.1 APIs zugreifen können. Diese Anmeldeinformationen erhalten Sie über die Website für Twitter-Entwickler. 

2. Navigieren Sie zur Website für <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter-Entwickler</a>-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, und wählen Sie Ihre Twitter-App aus.

3. Notieren Sie sich auf der Registerkarte für die **Schlüssel und Zugriffstoken** der App die folgenden Werte:

	+ **Verbraucherschlüssel**
	+ **Geheimer Verbraucherschlüssel**
	+ **Zugriffstoken**
	+ **Geheimer Zugriffstoken**

4. Melden Sie sich am [Microsoft Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihren mobilen Dienst.

5. Klicken Sie auf die Registerkarte **Identität**, geben Sie die Werte für **Verbraucherschlüssel** und **Geheimer Verbraucherschlüssel** ein, die Sie in Twitter erhalten haben, und klicken Sie auf **Speichern**. 

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Klicken Sie auf die Registerkarte **Konfigurieren**, blättern Sie nach unten bis **App-Einstellungen**, und geben Sie **Name** und **Wert** für jedes der folgenden Elemente ein, die Sie von der Twitter-Site erhalten haben. Klicken Sie anschließend auf **Speichern**.

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	Dadurch wird der Twitter-Zugriffstoken in den App-Einstellungen gespeichert. Die Zugangsberechtigungsdaten werden genau wie die Benutzeranmeldeinformationen auf der Registerkarte **Identität** in den App-Einstellungen gespeichert. Sie können darauf in Ihren Serverskripts zugreifen, ohne die Daten hart in der Skriptdatei codieren zu müssen. Weitere Informationen finden Sie unter [App-Einstellungen].

<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Microsoft Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services]: /documentation/articles/mobile-services-how-to-register-twitter-authentication
[Twitter-Entwickler]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App-Einstellungen]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
<!--HONumber=47-->
