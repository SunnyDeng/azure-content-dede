1. Klicken Sie auf die Registerkarte **Anwendungen** auf der Verzeichnisseite im [Azure-Verwaltungsportal](https://manage.windowsazure.com/).
  
2. Klicken Sie auf die Registrierung zu Ihrer integrierten Anwendung.

3. Klicken Sie auf der Anwendungsseite auf **Konfigurieren** und scrollen Sie nach unten zum **Schlüssel**-Abschnitt der Seite. 
4. Klicken Sie auf **1 Jahr** als Dauer für einen neuen Schlüssel. Klicken Sie anschließend auf **Speichern** und das Portal zeigt den neuen Schlüsselwert an.
5. Kopieren Sie die **Client-ID** und den **Schlüssel**, die/der nach dem Speichern angezeigt wird. Beachten Sie, dass der Schlüsselwert nach dem Speichern nur einmal angezeigt wird. 

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6. Scrollen Sie nach unten bis zum Ende der Konfigurationsseite der integrierten Anwendung, und aktivieren Sie die Berechtigung **Verzeichnisdaten lesen** für die Anwendung, und klicken Sie auf **Speichern**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)


7. Navigieren Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) zurück zu Ihrem mobilen Dienst, und klicken Sie auf die Registerkarte **Konfigurieren**. Scrollen Sie nach unten zum Abschnitt **App-Einstellungen**, fügen Sie die folgenden App-Einstellungen hinzu, und klicken Sie auf **Speichern**. 

    <table border="1">
    <tr>
    <th>Name der App-Einstellung</th><th>Beschreibung</th>
    </tr>
    <tr>
    <td>AAD_CLIENT_ID</td><td>Die Benutzer-ID, die Sie in den vorhergehenden Schritten aus der integrierten App kopiert haben.</td>
    </tr>
    <tr>
    <td>AAD_CLIENT_KEY</td><td>Der App-Schlüssel, den Sie in den vorhergehenden Schritten in der AAD-integrierten App generiert haben.</td>
    </tr>
    <tr>
    <td>AAD_TENANT_DOMAIN</td><td>Ihr AAD-Domänenname. Dieser sollte "mydomain.onmicrosoft.com" entsprechen.</td>
    </tr>
    </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)
  <!--HONumber=42-->
