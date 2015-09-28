<properties pageTitle="Azure AD B2C-Vorschau: Aufrufen von Web-APIs aus einer Android-Anwendung | Microsoft Azure" description="In diesem Artikel wird beschrieben, wie Sie eine Android-App für Aufgabenlisten erstellen, die eine node.js-Web-API mithilfe von OAuth 2.0-Bearertoken aufruft. Sowohl die Android-App als auch die Web-API verwenden Azure AD B2C zum Verwalten von Benutzeridentitäten und zum Authentifizieren von Benutzern." services="active-directory-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="brandwe"/>

# Azure AD B2C-Vorschau: Aufrufen von Web-APIs aus einer Android-Anwendung

Mit Azure AD B2C können Sie Ihren Android-Apps und Web-APIs in wenigen Schritten leistungsstarke Self-Service-Features zur Identitätsverwaltung hinzufügen. In diesem Artikel wird gezeigt, wie Sie eine Android-App für Aufgabenlisten erstellen, die eine node.js-Web-API mithilfe von OAuth 2.0-Bearertoken aufruft. Sowohl die Android-App als auch die Web-API verwenden Azure AD B2C zum Verwalten von Benutzeridentitäten und zum Authentifizieren von Benutzern.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]Für diesen Schnellstart benötigen Sie als Voraussetzung eine Web-API, die durch Azure AD mit B2C geschützt wird. Wir haben sowohl für .NET als auch für node.js eine für Sie vorbereitet. Bei dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass das node.js-Web-API-Beispiel konfiguriert wurde. Weitere Informationen dazu finden Sie unter [Azure AD B2C-Web-API-Lernprogramm für Node.js](active-directory-b2c-devquickstarts-api-node.md).

> [AZURE.NOTE]Dieser Artikel behandelt nicht das Implementieren von Anmeldung, Registrierung und Profilverwaltung mit Azure AD B2C. Er konzentriert sich auf das Aufrufen von Web-APIs, nachdem der Benutzer bereits authentifiziert wurde. Wenn nicht bereits erfolgt, sollten Sie mit dem [Lernprogramm "Erste Schritte mit .NET-Web-App"](active-directory-b2c-devquickstarts-web-dotnet.md) beginnen, um Informationen zu den Grundlagen von Azure AD B2C zu erhalten.

Für Android-Clients, die auf geschützte Ressourcen zugreifen müssen, ist unter Azure AD die Active Directory-Authentifizierungsbibliothek (ADAL) verfügbar. Die einzige Aufgabe der ADAL besteht darin, Ihrer App das Abrufen von Zugriffstoken zu erleichtern. Um Ihnen zu zeigen, wie einfach es geht, erstellen wir hier eine Android-Anwendung mit einer Aufgabenliste, mit der folgende Aktionen ausgeführt werden können:

-	Abrufen von Zugriffstoken zum Aufrufen einer Aufgabenlisten-API mit dem [OAuth 2.0-Authentifizierungsprotokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx)
-	Abrufen der Aufgabenliste eines Benutzers
-	Abmelden von Benutzern



### Schritt 1: Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Benutzer, Apps, Gruppen usw. Wenn Sie noch nicht über ein Verzeichnis verfügen, [erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie fortfahren.

### Schritt 2: Erstellen einer Anwendung

Nun müssen Sie eine App in Ihrem B2C-Verzeichnis erstellen, sodass Azure AD die Informationen erhält, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Die App und die Web-API werden in diesem Fall durch eine einzige **Anwendungs-ID** dargestellt, da sie zusammen eine logische App bilden. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Ersetzen Sie

- Fügen Sie der Anwendung eine **Web-App/Web-API** hinzu.
- Geben Sie `http://localhost:3000/auth/openid/return` als **Antwort-URL** ein – dies ist die Standard-URL für dieses Codebeispiel.
- Erstellen Sie einen **geheimen Schlüssel für Ihre Anwendung**, und notieren Sie ihn sich. Sie benötigen ihn später.
- Notieren Sie sich die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Sie benötigen sie ebenfalls in Kürze.

### Schritt 3: Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [**Richtlinie**](active-directory-b2c-reference-policies.md) definiert. Diese Anwendung enthält drei Oberflächen für die Identität: Registrierung, Anmeldung und Anmeldung mit Facebook. Sie müssen eine Richtlinie für jeden Typ erstellen, wie im [Artikel für Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Beachten Sie beim Erstellen der drei Richtlinien Folgendes:

- Wählen Sie den **Anzeigenamen** und einige andere Anmeldeattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsansprüche in den Richtlinien aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich den **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Er muss das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen in Kürze. 

Nachdem Sie die drei Richtlinien erstellt haben, können Sie Ihre App erstellen.

Beachten Sie, dass in diesem Artikel nicht behandelt wird, wie die gerade erstellten Richtlinien verwendet werden. Weitere Informationen zur Funktionsweise von Richtlinien in Azure AD B2C finden Sie im [Lernprogramm "Erste Schritte mit .NET-Web-App"](active-directory-b2c-devquickstarts-web-dotnet.md).

### Schritt 4: Herunterladen des Codes

Der Code für dieses Lernprogramm wird auf [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android) verwaltet. Zum Erstellen des Beispiels können Sie [ein Projektgerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip) oder das Gerüst klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**Das Herunterladen des Gerüsts ist für das Abschließen dieses Lernprogramms erforderlich.** Aufgrund der Komplexität der Implementierung einer voll funktionsfähigen Anwendung unter Android verfügt das **Gerüst** über UX-Code, der einmal ausgeführt wird, nachdem Sie das folgende Lernprogramm abgeschlossen haben. Dies soll für Entwickler Zeit sparen. Der UX-Code ist für das Thema des Hinzufügens von B2C zu einer Android-Anwendung nicht von Belang.

Die fertige App ist ebenfalls [als ZIP-Datei verfügbar](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) und unter `complete` im gleichen Repository enthalten.


Für die Erstellung mit Maven können Sie die Datei „pom.xml“ auf der obersten Ebene verwenden.


  * Führen Sie die Schritte im Abschnitt mit den [Voraussetzungen zum Einrichten von Maven für Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android) aus.
  * Einrichten des Emulators mit SDK 21
  * Wechseln Sie zu dem Stammordner, in dem Sie das Repository geklont haben.
  * Führen Sie den folgenden Befehl aus: mvn clean install.
  * Wechseln Sie in das Verzeichnis mit dem Schnellstartbeispiel: cd samples\\hello.
  * Führen Sie den folgenden Befehl aus: mvn android:deploy android:run.
  * Die App wird gestartet.
  * Geben Sie die Anmeldeinformationen des Testbenutzers ein, um sie auszuprobieren.

Zusätzlich zum AAR-Paket werden auch JAR-Pakete übermittelt.

### Schritt 5: Herunterladen der Android-ADAL und Hinzufügen zum Eclipse-Arbeitsbereich

Wir haben die Verwendung für Sie vereinfacht. Sie haben mehrere Möglichkeiten zur Verwendung dieser Bibliothek in Ihrem Android-Projekt:

* Sie können den Quellcode nutzen, um diese Bibliothek in Eclipse zu importieren und mit Ihrer Anwendung zu verknüpfen.
* Bei Verwendung von Android Studio können Sie das Paketformat *AAR* nutzen und auf die Binärdateien verweisen.

####Option 1: Quellcode per Git

Geben Sie einfach Folgendes ein, um den Quellcode des SDK über Git abzurufen:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"

####Option 2: Binärdateien per Gradle

Sie können die Binärdateien aus dem zentralen Repository von Maven abrufen. Das AAR-Paket kann wie folgt in Ihr Projekt unter Android Studio eingefügt werden:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:2.0-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0-alpha
}
```

####Option 3: AAR über Maven

Wenn Sie in Eclipse das m2e-Plug-In verwenden, können Sie die Abhängigkeit in Ihrer Datei „pom.xml“ angeben:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0-alpha</version>
    <type>aar</type>
</dependency>
```


####Option 5: JAR-Paket im Ordner „libs“
Sie können die JAR-Datei aus dem Repository von Maven abrufen und im Ordner *libs* Ihres Projekts ablegen. Außerdem müssen Sie die erforderlichen Ressourcen in Ihr Projekt kopieren, da sie in den JAR-Paketen nicht enthalten sind.


### Schritt 6: Hinzufügen von Verweisen auf die ADAL für Android zum Projekt


2. Fügen Sie einen Verweis auf Ihr Projekt hinzu, und geben Sie ihn als Android-Bibliothek an. Falls Sie unsicher sind, wie Sie dabei vorgehen sollen, [finden Sie hier weitere Informationen](http://developer.android.com/tools/projects/projects-eclipse.html).

3. Fügen Sie die Projektabhängigkeit für das Debuggen in Ihre Projekteinstellungen ein.

4. Aktualisieren Sie die Datei „AndroidManifest.xml“ Ihres Projekts, damit sie Folgendes enthält:

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

### Schritt 7: Hinzufügen von Code zum Aufrufen der ADAL für Android

Sie erstellen nun eine Hauptaktivität und benennen diese mit `ToDoActivity`.

Wir müssen die Aktivität initialisieren und einige Schaltflächen hinzufügen, die unsere Benutzeroberfläche steuern werden. Auch dies ist wieder ziemlich einfach und vertraut, wenn Sie bereits Android-Code geschrieben haben:

```
public class ToDoActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();
```

Anschließend werden in der gleichen Klasse (schließen Sie die Klammer noch nicht) die Schaltflächen für die Benutzeroberfläche hinzugefügt. Sie können erkennen, dass wir für Schaltflächen für die Facebook-Anmeldung und die E-Mail-Anmeldung hinzufügen. Hiermit werden unsere in der Datei `constants.java` definierten Richtlinien verwendet:

```
        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

    ```
Create an instance of AuthenticationContext at your main Activity:

    ```Java
 		 mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false, InMemoryCacheStore.getInstance());
            mAuthContext.getCache().removeAll();

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
    ```
  * NOTE: mContext is a field in your activity
 
 Let's also define our Result method `onActivityResult` that will be called when we have a result from the callback we'll write later:
 
 ```
 @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
 ```

Next, let's define our callback:

    ```Java
    mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY, Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getAccessToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
        }
        Toast.makeText(getApplicationContext(), TAG + "done", Toast.LENGTH_SHORT).show();
    }
        ```
        
 You may notice that this relies on methods we haven't written yet, such as `updateLoggedInUser()` and `getTasks()`. We'll write those below.	You can safely ignore the errors in Android Studio for now.

Explanation of the parameters:

  * ***SCOPES*** is required and is the scopes you are trying to reqeust access for. For the B2C preview this is the same as the Clientid but will change in the future.
  * ***POLICY*** is the policy for while you wish to authenticate the user. 
  * ***CLIENT_ID*** is required and comes from the AzureAD Portal.
  * You can setup redirectUri as your packagename. It is not required to be provided for the acquireToken call.
  * ***USER_HINT*** is the way we look up if the user is already in the cache and prompt the user if they are not found or the access token is invalid.
  * ***PROMPT*** helps to ask for credentials to skip cache and cookie.
  * ***Callback*** will be called after authorization code is exchanged for a token.

  The Callback will have an object of AuthenticationResult which has accesstoken, date expired, and idtoken info.

> [AZURE.NOTE]	Microsoft Intune's Company portal app provides the broker component and may be installed on the user's device. Developers should be prepared to allow for it's use as it provides SSO across all applications on the device. ADAL for Android will use the broker account if there is one user account created in the Authenticator. To use the broker, the developer needs to register a special redirectUri for broker usage. RedirectUri is in the format of msauth://packagename/Base64UrlencodedSignature. You can get your redirecturi for your app using the script `brokerRedirectPrint.ps1` or use API call `mContext.getBrokerRedirectUri()`. The signature is related to your signing certificates from the Google Play store.

 A Developer can skip the broker user with:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.WARNING] In order to reduce the complexity of this B2C Quickstart, we have opted in our sample to skip the broker. However, this is not recommended.

Next, let's create some helper methods that will get the token alone during our authentication calls to the Task API:

```
private void getToken(final AuthenticationCallback callback) {

        // one of the acquireToken overloads
        mAuthContext.acquireToken(onnstants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY,
                Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                "nux=1&" + Constants.EXTRA_QP, callback);
    }

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {
        Constants.CURRENT_RESULT = newToken;
    }
    
```

Finally, Your app manifest should have permissions to use AccountManager accounts: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


### Step 8. Call the Task API

Now that we have our Activity wired up and ready to go to do the heavy lifting of grabbing tokens, let's write our API to access the task server.

Our `getTasks` provides an array that represents the tasks in our server 
Our `addTask` and `deleteTask` do the subsequent action and return TRUE or FALSE if it was successful.

Let's write our `getTask` first:

```
private void getTasks() { if (Constants.CURRENT\_RESULT == null || Constants.CURRENT\_RESULT.getAccessToken().isEmpty()) return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(Constants.CURRENT_RESULT.getAccessToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }
 ```
 
 Außerdem schreiben wir eine Methode, die unsere Tabellen beim ersten Ausführen initialisiert:
 
 ``` private void initAppTables() { try { // Abrufen der zu verwendenden Mobile Service-Tabelleninstanz // mToDoTable = mClient.getTable(WorkItem.class); // mToDoTable.TABvLES\_URL = "/api/"; //mTextNewToDo = (EditText)findViewById(R.id.listViewToDo);

            // Create an adapter to bind the items with the view
            //mAdapter = new WorkItemAdapter(ToDoActivity.this, R.layout.listViewToDo);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 You'll see that this code requires some additional methods to do it's work. Let's write those now.
 
 ### Create endpoint URL generator
 
 We need to generate the endpoint URL that we'll be connecting to. Let's do that in the same class file:
 
 
 ```
     private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

    ```


Note that we add the access token to the request in the following code:

### Step 9: Let's write some UX methods

Android requires us to handle some callbacks in order to operate the app. These are `createAndShowDialog` and `onResume()`. This is pretty simple and very familiar if you've written Android code before. 

Let's write those now:

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }
    
```

And now manage our dialog callbacks:


```
/** * Erstellt ein Dialogfeld und zeigt es an * * @param exception Die im Dialogfeld anzuzeigende Ausnahme * @param title Der Titel des Dialogfelds */ private void createAndShowDialog(Exception exception, String title) { createAndShowDialog(exception.toString(), title); }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }
    
 ```
    


### Schritt 10: Ausführen der Beispiel-App

Abschließend werden Sie die App in Android Studio oder Eclipse erstellen und ausführen. Registrieren Sie sich bei der App, oder melden Sie sich bei der App an, und erstellen Sie Aufgaben für den angemeldeten Benutzer. Melden Sie sich ab, melden Sie sich als ein anderer Benutzer wieder an, und erstellen Sie für diesen Benutzer Aufgaben.

Beachten Sie, wie die Aufgaben pro Benutzer in der API gespeichert werden, da die API die Benutzeridentität aus dem empfangenen Zugriffstoken extrahiert.

Als Referenz stellen wir das vollständige Beispiel [hier als ZIP-Datei bereit](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Sie können es auch aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


<!--

### Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

### Important Information


#### Encryption

ADAL encrypts the tokens and store in SharedPreferences by default. You can look at the StorageHelper class to see the details. Android introduced AndroidKeyStore for 4.3(API18) secure storage of private keys. ADAL uses that for API18 and above. If you want to use ADAL for lower SDK versions, you need to provide secret key at AuthenticationSettings.INSTANCE.setSecretKey

#### Session cookies in Webview

Android webview does not clear session cookies after app is closed. You can handle this with sample code below:
```java
CookieSyncManager.createInstance(getApplicationContext()); CookieManager cookieManager = CookieManager.getInstance(); cookieManager.removeSessionCookie(); CookieSyncManager.getInstance().sync(); ``` Weitere Informationen zu Cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=Sept15_HO3-->