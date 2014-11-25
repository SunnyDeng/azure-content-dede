Die neuen Twitter v1.1 APIs erfordern eine Authentifizierung Ihrer App, um auf Ressourcen zugreifen zu können. Zunächst müssen Sie sich die Anmeldedaten für Zugriff mithilfe von OAuth 2.0 besorgen. Anschließend speichern Sie diese sicher in den App-Einstellungen für Ihren mobilen Service.

1.  Führen Sie die Schritte im Thema [Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services][Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services] durch, falls noch nicht geschehen.

    Twitter erstellt die Anmeldedaten, mithilfe derer Sie auf Twitter v1.1 APIs zugreifen können. Diese Anmeldeinformationen erhalten Sie über die Website für Twitter-Entwickler.

2.  Navigieren Sie zur Website für [Twitter-Entwickler][Twitter-Entwickler]-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, navigieren Sie zu **My Applications**, und wählen Sie Ihre Twitter-App aus.

    ![][0]

3.  Auf der Registerkarte **Details** für die App notieren Sie sich die folgenden Werte:

    -   **Consumer key**
    -   **Consumer secret**
    -   **Access token**
    -   **Access token secret**

    ![][1]

4.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Service.

5.  Klicken Sie auf die Registerkarte **Identität**, geben Sie die von Twitter erhaltenen Werte für **Consumer key** und **Consumer secret** ein, und klicken Sie dann auf **Speichern**.

    ![][2]

6.  Klicken Sie auf die Registerkarte **Konfigurieren**, führen Sie einen Bildlauf nach unten bis **App-Einstellungen** durch, und geben Sie ein Paar aus **Name** und **Wert** für jedes der folgenden Elemente ein, die Sie von der Twitter-Site erhalten haben. Klicken Sie anschließend auf **Speichern**.

    -   `TWITTER_ACCESS_TOKEN`
    -   `TWITTER_ACCESS_TOKEN_SECRET`

    ![][3]

    Dadurch wird das Twitter-Zugriffstoken in den App-Einstellungen gespeichert. Wie die Benutzeranmeldeinformationen auf der Registerkarte **Identität** werden auch die Zugangsberechtigungsdaten verschlüsselt in den App-Einstellungen gespeichert, sodass Sie auf diese in Ihren Serverskripts zugreifen können, ohne die Daten hart in der Skriptdatei codieren zu müssen. Weitere Informationen finden Sie unter [App-Einstellungen][App-Einstellungen].



  [Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services]: /de-de/documentation/articles/mobile-services-how-to-register-twitter-authentication/
  [Twitter-Entwickler]: http://go.microsoft.com/fwlink/p/?LinkId=268300
  [0]: ./media/mobile-services-register-twitter-access/mobile-twitter-my-apps.png
  [1]: ./media/mobile-services-register-twitter-access/mobile-twitter-app-secrets.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png
  [3]: ./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png
  [App-Einstellungen]: http://msdn.microsoft.com/de-de/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
