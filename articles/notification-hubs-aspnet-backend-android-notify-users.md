<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="elioda" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda" />

# Azure Notification Hubs – Benachrichtigen von Benutzern

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/de-de/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
        <a href="/de-de/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android" class="current">Android</a>
</div>

Durch die Unterstützung von Pushbenachrichtigungen in Azure haben Sie Zugriff auf eine benutzerfreundliche, plattformübergreifende und horizontal skalierte Pushinfrastruktur, die die Implementierung von Pushbenachrichtigungen sowohl für Endbenutzer- als auch für Unternehmensanwendungen für mobile Plattformen erheblich vereinfacht. In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an einen bestimmten App-Benutzer auf einem bestimmten Gerät senden. Zum Authentifizieren von Clients und zum Generieren von Benachrichtigungen wird ein ASP.NET WebAPI-Back-End verwendet, wie im Thema [Registrieren von App-Back-End aus][Registrieren von App-Back-End aus] gezeigt. Dieses Lernprogramm basiert auf dem Notification Hub, den Sie im Lernprogramm **Erste Schritte mit Notification Hubs** erstellt haben.

> [AZURE.NOTE] Bei diesem Lernprogramm wird davon ausgegangen, dass Sie Ihren Notification Hub wie unter [Erste Schritte mit Notification Hubs (Android)][Erste Schritte mit Notification Hubs (Android)] beschrieben erstellt und konfiguriert haben.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Erstellen des Android-Projekts

Im nächsten Schritt erstellen Sie die Android-Anwendung.

1.  Führen Sie die Schritte im Lernprogramm [Erste Schritte mit Notification Hubs (Android)][Erste Schritte mit Notification Hubs (Android)] aus, um Ihre App zu erstellen und für den Empfang von Pushbenachrichtigungen von GCM zu konfigurieren.

2.  Öffnen Sie die Datei „res/layout/activity\_main.xml“, und ersetzen Sie den Inhalt wie folgt:

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.notifyusers.MainActivity"
            android:orientation="vertical">
            <EditText
                android:id="@+id/usernameText"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="@string/usernameHint" >
            </EditText>
            <EditText
                android:id="@+id/passwordText"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="@string/passwordHint"
                android:inputType="textPassword" />
            <Button
                android:id="@+id/buttonLogin"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/loginButton"
                android:onClick="login" />
            <Button
                android:id="@+id/buttonSend"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/sendButton"
                android:onClick="sendPush" />
        </LinearLayout>

3.  Öffnen Sie die Datei „res/values/strings.xml“, und fügen Sie die folgenden Zeilen hinzu:

        <string name="usernameHint">Username</string>
        <string name="passwordHint">Password</string>
        <string name="loginButton">1. Log in</string>
        <string name="sendButton">2. Send push</string>

    Das Layout von „main\_activity.xml“ sollte jetzt wie folgt aussehen:

    ![][0]

4.  Erstellen Sie nun eine **RegisterClient**-Klasse im selben Paket wie die **MainActivity**-Klasse. Ersetzen Sie `{backend endpoint}` durch den Back-End-Endpunkt, den Sie im vorherigen Abschnitt erhalten haben.

        import java.io.IOException;
        import java.io.UnsupportedEncodingException;
        import java.util.Set;

        import org.apache.http.HttpResponse;
        import org.apache.http.HttpStatus;
        import org.apache.http.client.ClientProtocolException;
        import org.apache.http.client.HttpClient;
        import org.apache.http.client.methods.HttpPost;
        import org.apache.http.client.methods.HttpPut;
        import org.apache.http.client.methods.HttpUriRequest;
        import org.apache.http.entity.StringEntity;
        import org.apache.http.impl.client.DefaultHttpClient;
        import org.apache.http.util.EntityUtils;
        import org.json.JSONArray;
        import org.json.JSONException;
        import org.json.JSONObject;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.util.Log;

        public class RegisterClient {
            private static final String PREFS_NAME = "ANHSettings";
            private static final String REGID_SETTING_NAME = "ANHRegistrationId";
            private static final String BACKEND_ENDPOINT = "{backend endpoint}/api/register";
            SharedPreferences settings;
            protected HttpClient httpClient;
            private String authorizationHeader;

            public RegisterClient(Context context) {
                super();
                this.settings = context.getSharedPreferences(PREFS_NAME, 0);
                httpClient =  new DefaultHttpClient();
            }

            public String getAuthorizationHeader() {
                return authorizationHeader;
            }

            public void setAuthorizationHeader(String authorizationHeader) {
                this.authorizationHeader = authorizationHeader;
            }

            public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
                String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

                JSONObject deviceInfo = new JSONObject();
                deviceInfo.put("Platform", "gcm");
                deviceInfo.put("Handle", handle);
                deviceInfo.put("Tags", new JSONArray(tags));

                int statusCode = upsertRegistration(registrationId, deviceInfo);

                if (statusCode == HttpStatus.SC_OK) {
                    return;
                } else if (statusCode == HttpStatus.SC_GONE){
                    settings.edit().remove(REGID_SETTING_NAME).commit();
                    registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                    statusCode = upsertRegistration(registrationId, deviceInfo);
                    if (statusCode != HttpStatus.SC_OK) {
                        Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                        throw new RuntimeException("Error upserting registration");
                    }
                } else {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            }

            private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                    throws UnsupportedEncodingException, IOException,
                    ClientProtocolException {
                HttpPut request = new HttpPut(BACKEND_ENDPOINT+"/"+registrationId);
                request.setEntity(new StringEntity(deviceInfo.toString()));
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                request.addHeader("Content-Type", "application/json");
                HttpResponse response = httpClient.execute(request);
                int statusCode = response.getStatusLine().getStatusCode();
                return statusCode;
            }

            private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
                if (settings.contains(REGID_SETTING_NAME))
                    return settings.getString(REGID_SETTING_NAME, null);

                HttpUriRequest request = new HttpPost(BACKEND_ENDPOINT+"?handle="+handle);
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                HttpResponse response = httpClient.execute(request);
                if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                    throw new RuntimeException("Error creating Notification Hubs registrationId");
                }
                String registrationId = EntityUtils.toString(response.getEntity());
                registrationId = registrationId.substring(1, registrationId.length()-1);

                settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

                return registrationId;
            }
        }

    Diese Komponente implementiert die REST-Aufrufe, die für das Kontaktieren des App-Back-Ends erforderlich sind, um sich für Pushbenachrichtigungen zu registrieren. Außerdem werden die vom Notification Hub erstellten *registrationIds* lokal gespeichert, wie unter [Registrierung vom App-Back-End aus][Registrieren von App-Back-End aus] beschrieben. Beachten Sie, dass ein Authentifizierungstoken aus dem lokalen Speicher verwendet wird, wenn Sie auf die Schaltfläche **Log in and register** klicken.

5.  Entfernen Sie in der **MainActivity**-Klasse die privaten Felder für **NotificationHub**, und fügen Sie ein Feld für **RegisterClient** hinzu:

        //private NotificationHub hub;
        private RegisterClient registerClient;

6.  Entfernen Sie dann in der **onCreate**-Methode die Initialisierung des **hub**-Felds und der **registerWithNotificationHubs**-Methode. Fügen Sie anschließend die folgenden Zeilen hinzu, die eine Instanz der **RegisterClient**-Klasse initialisieren. Die Methode sollte die folgenden Zeilen enthalten:

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
            gcm = GoogleCloudMessaging.getInstance(this);

            registerClient = new RegisterClient(this);

            setContentView(R.layout.activity_main);
        }

7.  Fügen Sie anschließend die folgenden Methoden hinzu, und ersetzen Sie `{backend endpoint}` durch den Back-End-Endpunkt, den Sie im vorherigen Abschnitt erhalten haben.

        @Override
        protected void onStart() {
            super.onStart();
            Button sendPush = (Button) findViewById(R.id.buttonSend);
            sendPush.setEnabled(false);
        }

        public void login(View view) throws UnsupportedEncodingException {
            this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

            final Context context = this;
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(SENDER_ID);
                        registerClient.register(regid, new HashSet<String>());
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    Button sendPush = (Button) findViewById(R.id.buttonSend);
                    sendPush.setEnabled(true);
                    Toast.makeText(context, "Logged in and registered.",
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

        public void sendPush(View view) throws ClientProtocolException, IOException {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpPost("{backend endpoint}/api/notifications");
                        request.addHeader("Authorization", "Basic "+getAuthorizationHeader());
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error sending notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error sending notification");
                        }
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to send notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
            return basicAuthHeader;
        }

    Der Rückruf für **Log in** erzeugt ein Standardauthentifizierungstoken basierend auf dem eingegebenen Benutzernamen und Kennwort (dies kann ein beliebiges von Ihrem Authentifizierungsschema verwendetes Token sein) und verwendet dann `RegisterClient`, um das Back-End aufzurufen. Der Rückruf für **Send push** ruft das Back-End auf, um eine sichere Benachrichtigung an alle Geräte dieses Benutzers auszulösen.

## Ausführen der Anwendung

Gehen Sie zum Ausführen der Anwendung folgendermaßen vor:

1.  Stellen Sie sicher, dass **AppBackend** in Azure bereitgestellt ist. Falls Sie Visual Studio verwenden, führen Sie die Web-API-Anwendung **AppBackend** aus. Daraufhin wird eine ASP.NET-Webseite angezeigt.

2.  Unter Eclipse führen Sie die App auf einem physischen Android-Gerät oder im Emulator aus.

3.  Geben Sie in der Android-App-UI einen Benutzernamen und das Kennwort ein. Dies kann eine beliebige Zeichenfolge sein, beide müssen jedoch denselben Wert haben.

4.  Klicken Sie in der Android-App-UI auf **Log in**. Klicken Sie anschließend auf **Send push**.

  [Registrieren von App-Back-End aus]: http://msdn.microsoft.com/de-de/library/dn743807.aspx
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  [0]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users1.PNG
