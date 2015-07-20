

Die neuen Twitter v1.1 APIs erfordern eine Authentifizierung Ihrer App, um auf Ressourcen zugreifen zu können. Zunächst müssen Sie sich die Anmeldedaten für Zugriff mithilfe von OAuth 2.0 besorgen. Anschließend speichern Sie diese sicher in den App-Einstellungen für Ihren mobilen Service.

1. Sofern noch nicht geschehen, führen Sie die Schritte im Thema <a href="../articles/mobile-services/mobile-services-how-to-register-twitter-authentication.md/" target="_blank">Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services</a> durch. 
  
  	Twitter erstellt die Anmeldedaten, mithilfe derer Sie auf Twitter v1.1 APIs zugreifen können. Diese Anmeldeinformationen erhalten Sie über die Website für Twitter-Entwickler.

2. Navigieren Sie zur Website für <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter-Entwickler</a>, melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, und wählen Sie Ihre Twitter-App aus.

3. Notieren Sie sich auf der Registerkarte für die **Schlüssel und Zugriffstoken** der App die folgenden Werte:

	+ **Consumer key**
	+ **Consumer secret**
	+ **Access token**
	+ **Access token secret**

4. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

5. Klicken Sie auf die Registerkarte **Identität**, geben Sie die von Twitter erhaltenen Werte für **Consumer key** und **Consumer secret** ein, und klicken Sie dann auf **Speichern**.

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Klicken Sie auf die Registerkarte **Konfigurieren**, führen Sie einen Bildlauf nach unten bis **App-Einstellungen** durch, und geben Sie ein Paar aus **Name** und **Wert** für jedes der folgenden Elemente ein, die Sie von der Twitter-Site erhalten haben. Klicken Sie anschließend auf **Speichern**.

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	Dadurch wird das Twitter-Zugriffstoken in den App-Einstellungen gespeichert. Wie die Benutzeranmeldeinformationen auf der Registerkarte **Identität** werden auch die Zugangsberechtigungsdaten verschlüsselt in den App-Einstellungen gespeichert, sodass Sie auf diese in Ihren Serverskripts zugreifen können, ohne die Daten hart in der Skriptdatei codieren zu müssen. Weitere Informationen finden Sie unter [App-Einstellungen].

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: ../articles/mobile-services/mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App-Einstellungen]: http://msdn.microsoft.com/library/azure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=July15_HO2-->