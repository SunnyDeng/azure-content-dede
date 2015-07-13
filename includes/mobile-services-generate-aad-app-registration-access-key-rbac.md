1. Klicken Sie auf die Registerkarte **Anwendungen** auf Ihrer Verzeichnisseite im [Azure-Verwaltungsportal](https://manage.windowsazure.com/).
  
2. Klicken Sie auf die Registrierung zu Ihrer integrierten Anwendung.

3. Klicken Sie auf der Seite der Anwendung auf **Konfigurieren** und blättern Sie nach unten zum Abschnitt **Schlüssel**.
4. Klicken Sie bei der Dauer des neuen Schlüssels auf **1 Jahr**. Klicken Sie dann auf **Speichern**. Das Portal zeigt den neuen Schlüsselwert an.
5. Kopieren Sie die **Benutzer-ID** und den **Schlüssel**, die nach dem Speichern angezeigt werden. Beachten Sie, dass der Schlüsselwert nach dem Speichern nur einmal angezeigt wird. 

    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/client-id-and-key.png)

6. Blättern Sie auf der Seite zur Konfigurierung der integrierten Anwendung nach unten, aktivieren Sie die Berechtigung **Verzeichnisdaten lesen**, und klicken Sie auf **Speichern**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/app-perms.png)


7. Navigieren Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) zurück zu Ihrem mobilen Dienst, und klicken Sie auf die Registerkarte **Konfigurieren**. Blättern Sie nach unten zum Abschnitt **App-Einstellungen**, fügen Sie die folgenden App-Einstellungen hinzu, und klicken Sie auf **Speichern**.

    <table border="1"> <tr> <th>Name der App-Einstellung</th><th>Beschreibung</th> </tr> <tr> <td>AAD_CLIENT_ID</td><td>Die Client-ID, die Sie in den vorhergehenden Schritten aus der integrierten App kopiert haben.</td> </tr> <tr> <td>AAD_CLIENT_KEY</td><td>Der App-Schlüssel, den Sie in den vorhergehenden Schritten in der AAD-integrierten App generiert haben.</td> </tr> <tr> <td>AAD_TENANT_DOMAIN</td><td>Ihr AAD-Domänenname. Sollte "mydomain.onmicrosoft.com" ähnlich sein</td> </tr> <tr> <td>AAD_GROUP_ID</td><td>Die Gruppen-ID, die Sie für die Gruppe "Sales" im vorherigen Abschnitt notiert haben</td> </tr> </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/aad-app-settings.png)
  

<!---HONumber=62-->