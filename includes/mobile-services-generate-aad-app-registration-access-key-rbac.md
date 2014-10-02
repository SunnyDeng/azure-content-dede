1.  Klicken Sie auf der Verzeichnisseite im [Azure-Verwaltungsportal][] auf die Registerkarte **Anwendungen**.

2.  Klicken Sie auf die Registrierung zu Ihrer integrierten Anwendung.

3.  Klicken Sie auf der Seite der Anwendung auf **Konfigurieren** und blättern Sie nach unten zum Abschnitt **Schlüssel**.
4.  Klicken Sie bei der Dauer des neuen Schlüssels auf **1 Jahr**. Klicken Sie dann auf **Speichern**. Das Portal zeigt den neuen Schlüsselwert an.
5.  Kopieren Sie die **Benutzer-ID** und den **Schlüssel**, die nach dem Speichern angezeigt werden. Beachten Sie, dass der Schlüsselwert nach dem Speichern nur einmal angezeigt wird.

    ![][]

6.  Blättern Sie auf der Seite zur Konfigurierung der integrierten Anwendung nach unten, aktivieren Sie die Berechtigung **Read directory data** und klicken Sie auf **Speichern**.

    ![][1]

7.  Navigieren Sie im [Azure-Verwaltungsportal][] zurück zu Ihrem mobilen Dienst und klicken Sie auf die Registerkarte **Konfigurieren**. Blättern Sie nach unten zum Abschnitt **App-Einstellungen**, fügen Sie die folgenden App-Einstellungen hinzu und klicken Sie auf **Speichern**.

    | Name der App-Einstellung | Beschreibung                                                                                            |
    |--------------------------|---------------------------------------------------------------------------------------------------------|
    | AAD\_CLIENT\_ID          | Die Benutzer-ID, die Sie in den vorhergehenden Schritten aus der integrierten App kopiert haben.        |
    | AAD\_CLIENT\_KEY         | Der App-Schlüssel, den Sie in den vorhergehenden Schritten in der AAD-integrierten App generiert haben. |
    | AAD\_TENANT\_DOMAIN      | Ihr AAD-Domänenname. Dieser sollte "mydomain.onmicrosoft.com" entsprechen.                              |
    | AAD\_GROUP\_ID           | Die Gruppen-ID, die Sie im vorhergehenden Abschnitt für die Gruppe "Sales" notiert haben.               |

    ![][2]

  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  []: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/client-id-and-key.png
  [1]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/app-perms.png
  [2]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/aad-app-settings.png
