

Die neuen Twitter v1.1 APIs erfordern eine Authentifizierung Ihrer App, um auf Ressourcen zugreifen zu können. Zunächst müssen Sie sich die Anmeldedaten für Zugriff mithilfe von OAuth 2.0 besorgen. Anschließend speichern Sie diese sicher in den App-Einstellungen für Ihren mobilen Service.

1.  Führen Sie die Schritte im Thema [Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services](/en-us/documentation/articles/mobile-services-how-to-register-twitter-authentication/) durch, falls noch nicht geschehen.

	Twitter erstellt die Anmeldedaten, mithilfe derer Sie auf Twitter v1.1 APIs zugreifen können. Diese Anmeldedaten können Sie über die Twitter Developers-Website bekommen.

1.  Navigieren Sie zur [Twitter Developers](http://go.microsoft.com/fwlink/p/?LinkId=268300)-Website, melden Sie sich mit Ihren Twitter-Anmeldeinformationen an, navigieren Sie zu **My Applications**, und wählen Sie Ihre Twitter-App aus.

    ![](./media/mobile-services-register-twitter-access/mobile-twitter-my-apps.png)

2.  Auf der Registerkarte **Details** für die App notieren Sie sich die folgenden Werte:

    -   **Consumer key**
    -   **Consumer secret**
    -   **Access token**
    -   **Access token secret**

    ![](./media/mobile-services-register-twitter-access/mobile-twitter-app-secrets.png)

3.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Service.

4.  Klicken Sie auf die Registerkarte **Identität**, geben Sie die von Twitter erhaltenen Werte für **Consumer key** und **Consumer secret** ein, und klicken Sie dann auf **Speichern**.

    ![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

5.  Klicken Sie auf die Registerkarte **Konfigurieren**, führen Sie einen Bildlauf nach unten bis **App-Einstellungen** durch, und geben Sie ein Paar aus **Name** und **Wert** für jedes der folgenden Elemente ein, die Sie von der Twitter-Site erhalten haben. Klicken Sie anschließend auf **Speichern**.

    -   `TWITTER_ACCESS_TOKEN`
    -   `TWITTER_ACCESS_TOKEN_SECRET`

    ![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

    Dadurch wird das Twitter-Zugriffstoken in den App-Einstellungen gespeichert. Wie die Benutzeranmeldeinformationen auf der Registerkarte **Identität** werden auch die Zugangsberechtigungsdaten verschlüsselt in den App-Einstellungen gespeichert, sodass Sie auf diese in Ihren Serverskripts zugreifen können, ohne die Daten hart in der Skriptdatei codieren zu müssen. Weitere Informationen finden Sie unter [App-Einstellungen](http://msdn.microsoft.com/en-us/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7).


