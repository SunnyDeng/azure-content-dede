<properties title="Azure Notification Hubs Secure Push" pageTitle="Azure Notification Hubs Secure Push" metaKeywords="Azure push notifications, Azure notification hubs, secure push" description="Learn how to send secure push notifications to an Android app from Azure. Code samples written in Java and C#." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Azure Notification Hubs – Sichere Pushbenachrichtigungen

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/de-de/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS">iOS</a>
        <a href="/de-de/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android" class="current">Android</a>
</div>

Durch die Unterstützung von Pushbenachrichtigungen in Microsoft Azure haben Sie Zugriff auf eine benutzerfreundliche, plattformübergreifende und horizontal skalierte Pushinfrastruktur, die die Implementierung von Pushbenachrichtigungen sowohl für Endbenutzer- als auch für Unternehmensanwendungen für mobile Plattformen erheblich vereinfacht.

Es kann vorkommen, dass eine Anwendung etwas in die Benachrichtigung einschließen möchte, dass aufgrund gesetzlicher oder sicherheitsbedingter Einschränkungen nicht über die Standardinfrastruktur von Pushbenachrichtigungen übertragen werden kann. In diesem Lernprogramm wird beschrieben, wie Sie die gleiche Funktionalität erzielen, indem Sie vertrauliche Informationen über eine sichere authentifizierte Verbindung zwischen dem Clientgerät und dem App-Back-End senden..

Der generelle Ablauf sieht folgendermaßen aus:

1.  Das App-Back-End:

    -   Speichert die sichere Nutzlast in einer Back-End-Datenbank.
    -   Sendet die ID dieser Benachrichtigung an das Gerät (es werden keine sicheren Informationen gesendet).

2.  Bei Erhalt der Benachrichtigung reagiert die App auf dem Gerät folgendermaßen:

    -   Das Gerät kontaktiert das Back-End und fordert die sichere Nutzlast an.
    -   Die App kann die Nutzlast als Benachrichtigung auf dem Gerät anzeigen.

Es ist wichtig zu beachten, dass wir im vorstehenden Ablauf (und diesem Lernprogramm) davon ausgehen, dass das Gerät ein Authentifizierungstoken im lokalen Speicher ablegt, nachdem sich der Benutzer angemeldet hat. Dies gewährleistet einen völlig reibungslosen Ablauf, da das Gerät die sichere Nutzlast der Benachrichtigung mithilfe dieses Tokens abrufen kann. Wenn Ihre Anwendung keine Authentifizierungstoken auf dem Gerät speichert oder diese Token ablaufen können, sollte die Geräte-App nach Erhalt der Benachrichtigung eine generische Benachrichtigung anzeigen, in der der Benutzer zum Starten der App aufgefordert wird. Anschließend authentifiziert die App den Benutzer und zeigt die Nutzlast der Benachrichtigung an.

Dieses Lernprogramm zu sicheren Pushbenachrichtigungen veranschaulicht das sichere Senden einer Pushbenachrichtigung. Es baut auf dem Lernprogramm **Benachrichtigen von Benutzern** auf, daher sollten Sie die Schritte in diesem Lernprogramm zuerst durchführen.

> [AZURE.NOTE] Bei diesem Lernprogramm wird davon ausgegangen, dass Sie Ihren Notification Hub wie unter [Erste Schritte mit Notification Hubs (Android)][Erste Schritte mit Notification Hubs (Android)] beschrieben erstellt und konfiguriert haben.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Ändern des Android-Projekts

Nachdem Sie Ihr App-Back-End so geändert haben, dass lediglich die *ID* einer Benachrichtigung gesendet wird, müssen Sie Ihre Android-App so ändern, dass sie diese Benachrichtigung verarbeitet und einen Rückruf an das Back-End sendet, um die anzuzeigende sichere Nachricht abzurufen.
Hierzu müssen Sie sicherstellen, dass Ihre Android-App weiß, wie sie sich beim Back-End authentifiziert, wenn sie die Pushbenachrichtigungen erhält.

Wir ändern nun den Ablauf der *Anmeldung*, um den Wert des Authentifizierungsheaders in den freigegebenen Einstellungen Ihrer App zu speichern. Sie können entsprechende Mechanismen verwenden, um beliebige Authentifizierungstoken (z. B. OAuth-Token) zu speichern, die die App verwenden muss, ohne dass Benutzeranmeldeinformationen eingegeben werden müssen.

1.  Fügen Sie in Ihrem Android-App-Projekt die folgenden Konstanten am Anfang der **MainActivity**-Klasse hinzu:

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2.  Aktualisieren Sie in der **MainActivity**-Klasse die `getAuthorizationHeader()`-Methode mit folgendem Code:

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3.  Fügen Sie die folgenden `import`-Anweisungen am Anfang der **MainActivity**-Datei hinzu:

        import android.content.SharedPreferences;

Nun ändern wir den Handler, der beim Empfang der Benachrichtigung aufgerufen wird.

1.  Ändern Sie in der **MyHandler**-Klasse die `OnReceive()`-Methode so, dass sie folgenden Code enthält:

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;   
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

2.  Fügen Sie dann die `retrieveNotification()`-Methode hinzu, und ersetzen Sie dabei den Platzhalter `{back-end endpoint}` durch den Back-End-Endpunkt, den Sie beim Bereitstellen des Back-Ends erhalten haben:

        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

Diese Methode ruft Ihr Back-End auf, um den Benachrichtigungsinhalt unter Verwendung der in den freigegebenen Einstellungen gespeicherten Anmeldeinformationen abzurufen, und zeigt ihn als normale Benachrichtigung an. Die Benachrichtigung sieht für den App-Benutzer genauso aus wie jede andere Pushbenachrichtigung.

Beachten Sie, dass eine fehlende Authentifizierungsheader-Eigenschaft oder eine Ablehnung durch das Back-End nach Möglichkeit behandelt werden sollte. Wie diese Fälle im Einzelnen zu behandeln sind, hängt größtenteils von der Umgebung des Zielbenutzers ab. Eine Möglichkeit besteht darin, eine Benachrichtigung mit einer generischen Eingabeaufforderung anzuzeigen, damit sich der Benutzer zum Abrufen der eigentlichen Benachrichtigung authentifiziert.

## Ausführen der Anwendung

Gehen Sie zum Ausführen der Anwendung folgendermaßen vor:

1.  Stellen Sie sicher, dass **AppBackend** in Azure bereitgestellt ist. Falls Sie Visual Studio verwenden, führen Sie die Web-API-Anwendung **AppBackend** aus. Daraufhin wird eine ASP.NET-Webseite angezeigt.

2.  Unter Eclipse führen Sie die App auf einem physischen Android-Gerät oder im Emulator aus.

3.  Geben Sie in der Android-App-UI einen Benutzernamen und das Kennwort ein. Dies kann eine beliebige Zeichenfolge sein, beide müssen jedoch denselben Wert haben.

4.  Klicken Sie in der Android-App-UI auf **Log in**. Klicken Sie anschließend auf **Send push**.

  [notification-hubs-aspnet-backend-securepush]: ../includes/notification-hubs-aspnet-backend-securepush.md
