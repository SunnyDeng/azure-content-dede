1.  Klicken Sie auf die Registerkarte **Anwendungen** auf Ihrer Verzeichnisseite im [Azure-Verwaltungsportal][].

2.  Klicken Sie auf die integrierte Anwendungsregistrierung.

3.  Klicken Sie auf der Anwendungsseite auf **Konfigurieren**, und blättern Sie nach unten zum Abschntt **keys** der Seite.
4.  Klicken Sie auf die Option für **1 Jahr** als Geltungsdauer des neuen Schlüssels. Klicken Sie dann auf **Speichern**, un im Portal wird der neue Schlüsselwert angezeigt.
5.  Kopieren Sie **Client-D** und **Schlüssel**, die nach dem Speichern angezeigt werden. Beachten Sie, dass der Schlüsselwert nur einmal nach dem Speichern angezeigt wird.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6.  Blättern Sie zum Ende der Seite mit der integrierten Anwendungskonfiguration, aktivieren Sie die Berechtigung **Verzeichnisdaten lesen** für die Anwendung, und klicken Sie auf **Speichern**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)

7.  Navigieren Sie im [Azure-Verwaltungsportal][] zurück zu Ihrem mobilen Dienst, und klicken Sie auf die Registerkarte **Konfigurieren**. Blättern Sie nach unten zum Abschnitt **app settings**, fügen Sie die folgenden Anwendungseinstellungen hinzu, und klicken Sie auf **Speichern**.

    <table border="1">
    <tr>
    <th>Name der Anwendungseinstellung </th><th>Beschreibung                                                                                        </th>
    </tr>
    <tr>
    <td> AAD_CLIENT_ID  </td><td>Die Client-ID, die Sie im vorigen Schritt aus der integrierten Anwendung kopiert haben.</td>
    </tr>
    <tr>
    <td> AAD_CLIENT_KEY</td><td>Der Anwendungsschlüssel, den Sie in der integrierten AAD-Anwendung im Schritt oben generiert haben. </td>
    </tr>
    <tr>
    <td>AAD_TENANT_DOMAIN  </td><td>Ihr AAD-Domänenname. Dieser sollte so ähnlich lauten wie "meineDomäne.onmicrosoft.com"  </td>
    </tr>
    </table><br/>

    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)
