<properties
	pageTitle="Azure AD B2C-Vorschau: Aufrufen von Web-APIs aus einer Android-Anwendung | Microsoft Azure"
	description="In diesem Artikel wird beschrieben, wie Sie eine Android-App für Aufgabenlisten erstellen, die eine node.js-Web-API mithilfe von OAuth 2.0-Bearertoken aufruft. Sowohl die Android-App als auch die Web-API verwenden Azure AD B2C zum Verwalten von Benutzeridentitäten und zum Authentifizieren von Benutzern."
	services="active-directory-b2c"
	documentationCenter="android"
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Azure AD B2C-Vorschau: Aufrufen von Web-APIs aus einer Android-Anwendung

Mit Azure AD B2C können Sie Ihren Android-Apps und Web-APIs in wenigen Schritten leistungsstarke Self-Service-Features zur Identitätsverwaltung hinzufügen. In diesem Artikel wird gezeigt, wie Sie eine Android-App für Aufgabenlisten erstellen, die eine node.js-Web-API mithilfe von OAuth 2.0-Bearertoken aufruft. Sowohl die Android-App als auch die Web-API verwenden Azure AD B2C zum Verwalten von Benutzeridentitäten und zum Authentifizieren von Benutzern.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

 	
> [AZURE.NOTE]
	Für diesen Schnellstart benötigen Sie als Voraussetzung eine Web-API, die durch Azure AD mit B2C geschützt wird. Wir haben sowohl für .NET als auch für node.js eine für Sie vorbereitet. Bei dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass das node.js-Web-API-Beispiel konfiguriert wurde. Weitere Informationen dazu finden Sie unter [Azure AD B2C-Web-API-Lernprogramm für Node.js](active-directory-b2c-devquickstarts-api-node.md).

 
> [AZURE.NOTE]
	Dieser Artikel behandelt nicht das Implementieren der Anmeldung, Registrierung und Profilverwaltung mit Azure AD B2C. Er konzentriert sich auf das Aufrufen von Web-APIs aufrufen, nachdem der Benutzer bereits authentifiziert wurde. Wenn nicht bereits erfolgt, sollten Sie mit dem [Lernprogramm "Erste Schritte mit .NET-Web-App"](active-directory-b2c-devquickstarts-web-dotnet.md) beginnen, um Informationen zu den Grundlagen von Azure AD B2C zu erhalten.


Für Android-Clients, die auf geschützte Ressourcen zugreifen müssen, ist unter Azure AD die Active Directory-Authentifizierungsbibliothek (ADAL) verfügbar. Die einzige Aufgabe der ADAL besteht darin, Ihrer App das Abrufen von Zugriffstoken zu erleichtern. Um Ihnen zu zeigen, wie einfach es geht, erstellen wir hier eine Android-Anwendung mit einer Aufgabenliste, mit der folgende Aktionen ausgeführt werden können:

-	Abrufen von Zugriffstoken zum Aufrufen einer Aufgabenlisten-API mit dem [OAuth 2.0-Authentifizierungsprotokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx)
-	Abrufen der Aufgabenliste eines Benutzers
-	Abmelden von Benutzern



### Schritt 1: Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Benutzer, Apps, Gruppen usw. Wenn Sie noch nicht über ein Verzeichnis verfügen, [erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie fortfahren.

### Schritt 2: Erstellen einer Anwendung

Nun müssen Sie eine App in Ihrem B2C-Verzeichnis erstellen, sodass Azure AD die Informationen erhält, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Die App und die Web-API werden in diesem Fall durch eine einzelne **Anwendungs-ID** dargestellt, da sie zusammen eine logische App bilden. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Ersetzen Sie

- Fügen Sie der Anwendung eine **Web-App/Web-API** hinzu.
- Geben Sie `urn:ietf:wg:oauth:2.0:oob` als **Antwort-URL** ein – dies ist die Standard-URL für dieses Codebeispiel.
- Erstellen Sie einen **geheimen Schlüssel für Ihre Anwendung**, und notieren Sie ihn sich. Sie benötigen sie später. Beachten Sie, dass dieser Wert vor der Verwendung in [XML-Escapezeichen](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) gesetzt werden muss.
- Notieren Sie die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Sie benötigen sie ebenfalls in Kürze.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

### Schritt 3: Erstellen der Richtlinien

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

In Azure AD B2C wird jede Benutzeroberfläche durch eine [**Richtlinie**](active-directory-b2c-reference-policies.md) definiert. Diese Anwendung enthält drei Oberflächen für die Identität: Registrierung, Anmeldung und Facebook-Anmeldung. Sie müssen eine Richtlinie jedes Typs erstellen, wie im [Artikel mit Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Wenn Sie die drei Richtlinien erstellen, beachten Sie Folgendes:

- Wählen Sie den **Anzeigenamen** und einige andere Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsansprüche in jeder Richtlinie aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie müssen das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen in Kürze. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie die drei Richtlinien erfolgreich erstellt haben, können Sie Ihre App erstellen.

Beachten Sie, dass in diesem Artikel nicht behandelt wird, wie die gerade erstellten Richtlinien verwendet werden. Weitere Informationen zur Funktionsweise von Richtlinien in Azure AD B2C finden Sie im [Lernprogramm "Erste Schritte mit .NET-Web-Apps"](active-directory-b2c-devquickstarts-web-dotnet.md).

### Schritt 4: Herunterladen des Codes

Der Code für dieses Lernprogramm wird [auf GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android) verwaltet. Zum Erstellen des Beispiels können Sie [ein Projektgerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip) oder das Gerüst klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **Das Herunterladen des Gerüsts ist für das Abschließen dieses Lernprogramms erforderlich.** Aufgrund der Komplexität der Implementierung einer voll funktionsfähigen Anwendung unter Android verfügt das **Gerüst** über UX-Code, der ausgeführt wird, nachdem Sie das folgende Lernprogramm abgeschlossen haben. Mit dieser Maßnahme sollen Entwickler Zeit sparen. Der UX-Code ist für das Thema des Hinzufügens von B2C zu einer Android-Anwendung nicht von Belang.

Die fertige App ist ebenfalls [als ZIP-Datei verfügbar](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) oder unter der Verzweigung `complete` im gleichen Repository enthalten.


Für die Erstellung mit Maven können Sie die Datei „pom.xml“ auf der obersten Ebene verwenden.


  * Führen Sie die Schritte im Abschnitt mit den [Voraussetzungen zum Einrichten von Maven für Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android) aus.
  * Einrichten des Emulators mit SDK 21
  * Wechseln Sie zu dem Stammordner, in dem Sie das Repository geklont haben.
  * Führen Sie den folgenden Befehl aus: mvn clean install.
  * Wechseln Sie in das Verzeichnis mit dem Schnellstartbeispiel: cd samples\\hello.
  * Führen Sie den folgenden Befehl aus: mvn android:deploy android:run.
  * Die App wird gestartet.
  * Geben Sie die Anmeldeinformationen des Testbenutzers ein, um sie auszuprobieren.

Zusätzlich zum AAR-Paket werden auch JAR-Pakete übermittelt.

### Schritt 5: Herunterladen der Android-ADAL und Hinzufügen zum Android Studio-Arbeitsbereich

Wir haben die Verwendung für Sie vereinfacht. Sie haben mehrere Möglichkeiten zur Verwendung dieser Bibliothek in Ihrem Android-Projekt:

* Sie können den Quellcode nutzen, um diese Bibliothek in Eclipse zu importieren und mit Ihrer Anwendung zu verknüpfen.
* Bei Verwendung von Android Studio können Sie das Paketformat *AAR* nutzen und auf die Binärdateien verweisen.



####Option 1: Binärdateien über Gradle (empfohlen)

Sie können die Binärdateien aus dem zentralen Repository von Maven abrufen. Das AAR-Paket kann wie folgt in Ihr Projekt unter Android Studio eingefügt werden (Beispiel: in `build.gradle`):

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

####Option 2: AAR über Maven

Wenn Sie in Eclipse das m2e-Plug-In verwenden, können Sie die Abhängigkeit in Ihrer Datei "`pom.xml`" angeben:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

####Option 3: Quelle über Git (letzte Möglichkeit)

Geben Sie einfach Folgendes ein, um den Quellcode des SDK über Git abzurufen:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"


### Schritt 6: Einrichten Ihrer Konfigurationsdatei

Wir verwenden die Konfiguration, die Sie oben im B2C-Portal zum Konfigurieren des Android-Projekts eingerichtet haben.

Öffnen Sie `helpes/Constants.java`, und fügen Sie die Werte für Folgendes ein:

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
**SCOPES**: Die Bereiche, die wir an den Server übergeben und die wir vom Server für den anzumeldenden Benutzer anfordern möchten. Für B2C Preview übergeben wir die „client\_id“. Zukünftig wird sich dies für das Lesen von Bereichen jedoch ändern. Dieses Dokument wird dann aktualisiert.
**ADDITIONAL\_SCOPES**: Hierbei handelt es sich um zusätzliche Bereiche, die Sie möglicherweise für Ihre Anwendung verwenden möchten. Wird zukünftig verwendet.
**CLIENT\_ID**: Die Anwendungs-ID, die Sie aus dem Portal erhalten haben.
**REDIRECT\_URL**: Die Umleitung, die für das Postback des Tokens erwartet wird.
**EXTRA\_QP**: Alle anderen Daten, die Sie im URL-codierten Format an den Server übergeben möchten.
**FB\_POLICY**: Die Richtlinie, die Sie aufrufen. Der wichtigste Teil dieser exemplarischen Vorgehensweise.
**EMAIL\_SIGNIN\_POLICY**: Die Richtlinie, die Sie aufrufen. Der wichtigste Teil dieser exemplarischen Vorgehensweise.
**EMAIL\_SIGNUP\_POLICY**: Die Richtlinie, die Sie aufrufen. Der wichtigste Teil dieser exemplarischen Vorgehensweise.

### Schritt 7: Hinzufügen von Verweisen auf die ADAL für Android zum Projekt


> [AZURE.NOTE]	Für ADAL für Android wird zum Aufrufen der Authentifizierung ein Intent-basiertes Modell verwendet. Intent-Elemente sind zum Ausführen von Schritten „oberhalb“ der App angeordnet. In diesem gesamten Beispiel – und allgemein für ADAL für Android – werden Intent-Elemente verwaltet, und es werden Informationen zwischen diesen Elementen übergeben.


Zuerst müssen wir Android über das Layout unserer Anwendung informieren, was auch die gewünschten „Intents()“ betrifft. Ich werde auf diese Intent-Elemente später noch ausführlicher eingehen.

Aktualisieren Sie die Datei „AndroidManifest.xml“ Ihres Projekts, damit sie alle Intent-Elemente enthält:

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Sie sehen, dass wir fünf Aktivitäten definieren, die wir verwenden möchten.

**AuthenticationActivity**: Stammt von ADAL und stellt das WebView-Element für die Anmeldung dar.

**LoginActivity**: Zeigt die Anmelderichtlinien und die Schaltflächen für die einzelnen Richtlinien an.

**SettingsActivity**: Ermöglicht das Ändern der App-Einstellungen zur Laufzeit.

**AddTaskActivity**: Ermöglicht das Hinzufügen von Aufgaben zur REST-API, die durch Azure AD geschützt wird.

**ToDoActivity**: Die Hauptaktivität, die zum Anzeigen von Aufgaben dient.



### Schritt 8: Erstellen der Anmeldeaktivität

Sie erstellen nun eine Hauptaktivität und geben ihr den Namen `LoginActivity`.

Erstellen Sie eine Datei namens `LoginActivity.java`.

Wir müssen die Aktivität initialisieren und einige Schaltflächen hinzufügen, die unsere Benutzeroberfläche steuern werden. Auch dies ist wieder ziemlich einfach und vertraut, wenn Sie bereits Android-Code geschrieben haben:

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

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

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
Wir erstellen Schaltflächen, mit denen unser ToDoActivity-Intent-Element (von dem ADAL aufgerufen wird, wenn wir ein Token benötigen) mit unserer eigenen Aktivität als Referenz und einem zusätzlichen Parameter aufgerufen wird. Dieser zusätzliche Parameter wird von der `intent.putExtra()`-Methode übergeben. Sie sehen, dass wir hier „thePolicy“ so wie in `Constants.java` angegeben definieren. So weiß das Intent-Element, welche Richtlinie während der Authentifizierung aufgerufen werden soll.

### Schritt 9: Erstellen der Settings-Aktivität

Dies ist lediglich eine Aktivität, mit der die UI mit den Einstellungen aufgefüllt wird.

Erstellen Sie eine Datei namens `SettingsActivity.java`.

Ein recht einfacher CRUD-Vorgang:

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

	//private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_settings);

        loadSettings();
//		checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//		checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


	}

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

	private void saveSettings(String key, boolean value) {
		SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
				Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
		Editor prefsEditor = prefs.edit();
		prefsEditor.putBoolean(key, value);
		prefsEditor.commit();
	}
}
```

### Schritt 10: Erstellen der AddTask-Aktivität

Hiermit können wir unserem REST-API-Endpunkt eine Aufgabe hinzufügen. Auch dies ist recht einfach.

Erstellen Sie eine Datei namens `AddTaskActivity.java`.

Und schreiben Sie Folgendes:

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

### Schritt 11: Erstellen der ToDoList-Aktivität

Jetzt geht es um die wichtigste Aktivität. Mit dieser Aktivität können wir ein Token aus Azure AD für die Richtlinie abrufen, um es dann zum Aufrufen des REST-API-Aufgabenservers zu verwenden.

Erstellen Sie eine Datei namens `ToDoActivity.java`.

Schreiben Sie Folgendes (die Aufrufe werden später erklärt):

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

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
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
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

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}
   
```

        
 Sie werden vielleicht feststellen, dass dies auf Methoden beruht, die wir noch nicht geschrieben haben, z. B. `updateLoggedInUser()`, `clearSessionCookie()` und `getTasks()`. Diese Methoden schreiben wir jetzt. Sie können die Fehler in Android Studio vorerst ohne Bedenken ignorieren.

Erklärung der Parameter:

  * ***SCOPES*** ist erforderlich. Dies sind die Bereiche, für die Sie Zugriff anfordern. Für die Preview-Version von B2C entspricht dies der Clientid, aber dies wird sich in Zukunft ändern.
  * ***POLICY*** ist die Richtlinie, für die der Benutzer authentifiziert werden soll. 
  * ***CLIENT\_ID*** ist erforderlich und stammt aus dem Azure AD-Portal.
  * Sie können „redirectUri“ als Ihren Paketnamen einrichten. Die Angabe für den Aufruf „acquireToken“ ist nicht erforderlich.
  * ***getUserInfo()*** verwenden wir für die Ermittlung, ob der Benutzer bereits im Cache enthalten ist. Der Benutzer erhält eine Aufforderung, wenn er nicht gefunden wird oder wenn das Zugriffstoken ungültig ist. Unten schreiben wir nun diese Methode.
  * ***PromptBehavior.always*** unterstützt Sie beim Abfragen von Anmeldeinformationen, um Cache und Cookie zu überspringen.
  * ***Callback*** wird aufgerufen, nachdem der Autorisierungscode für ein Token ausgetauscht wurde.

  Der Rückruf verfügt über das Objekt „AuthenticationResult“, mit dem die Informationen „accesstoken“, „date expired“ und „idtoken“ bereitgestellt werden.

> [AZURE.NOTE]	Die Unternehmensportal-App von Microsoft Intune stellt die Brokerkomponente bereit und kann auf dem Gerät des Benutzers installiert werden. Entwickler sollten auf ihre Nutzung vorbereitet sein, da damit SSO für alle Anwendungen auf dem Gerät bereitgestellt werden kann. ADAL für Android nutzt das Brokerkonto, wenn im Authentifikator ein Benutzerkonto erstellt wird. Um den Broker verwenden zu können, müssen Entwickler ein spezielles redirectUri-Element für die Brokernutzung registrieren. „redirectUri“ liegt im Format msauth://packagename/Base64UrlencodedSignature vor. Sie können "redirectUri" für Ihre App abrufen, indem Sie das Skript `brokerRedirectPrint.ps1` oder den API-Aufruf `mContext.getBrokerRedirectUri()` nutzen. Die Signatur bezieht sich auf die Signaturzertifikate aus dem Google Play Store.

 Ein Entwickler kann den Brokerbenutzer wie folgt überspringen:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Um die Komplexität dieser B2C-Schnellstartanleitung zu verringern, haben wir uns im Beispiel für das Auslassen des Brokers entschieden.

Als Nächstes erstellen wir einige Hilfsmethoden, mit denen das Token während unserer Authentifizierungsaufrufe der Aufgaben-API allein abgerufen wird.

**In derselben Datei** mit dem Namen `ToDoActivity.java`

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Außerdem fügen wir einige Methoden hinzu, mit denen wir unser AuthenticationResult (mit unserem Token) für die globalen CONSTANTS festlegen und abrufen („set“ und „get“). Dies ist erforderlich, da unsere anderen Aktivitäten keinen Zugriff auf das Token hätten (z. B. zum Hinzufügen einer Aufgabe in `AddTaskActivity.java`), obwohl **sResult** von `ToDoActivity.java` in seinen Abläufen verwendet wird.

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }

    
```
### Schritt 12: Erstellen einer Methode zum Zurückgeben eines UserIdentifier-Elements

ADAL für Android stellt den Benutzer in Form eines **UserIdentifier**-Objekts dar. So wird der Benutzer verwaltet, und wir wissen, ob derselbe Benutzer in unseren Aufrufen verwendet wird. Daher können wir uns auf den Cache verlassen und müssen keinen neuen Aufruf an den Server durchführen. Um dies zu vereinfachen, erstellen wir ein `getUserInfo()`-Element. So können wir ein UserIdentifier-Element zurückgeben, das wir für `acquireToken()` nutzen können. Außerdem erstellen wir eine getUniqueId()-Methode, mit der die ID des UserIdentifier-Elements in unserem Cache schnell zurückgegeben werden kann.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }
    
```
 
### Schritt 13: Schreiben von Hilfsmethoden

Wir müssen einige Hilfsmethoden schreiben, die uns beim Löschen der Cookies helfen und ein AuthenticationCallback-Element bereitstellen. Diese werden ausschließlich für das Beispiel verwendet, um sicherzustellen, dass wir uns beim Aufrufen unserer ToDo-Aktivität in einem bereinigten Zustand befinden.

**In derselben Datei** mit dem Namen `ToDoActivity.java`

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
``` 

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
    
```   

### Schritt 14: Aufrufen der Aufgaben-API

Da unsere Aktivität jetzt bereit für die anspruchsvolle Aufgabe der Tokenbeschaffung ist, schreiben wir unsere API für den Zugriff auf den Aufgabenserver.

Unser `getTasks`-Element stellt ein Array bereit, das für die Aufgaben auf unserem Server steht.

Zuerst schreiben wir unser `getTask`-Element:

**In derselben Datei** mit dem Namen `ToDoActivity.java`

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
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
 
 **In derselben Datei** mit dem Namen `ToDoActivity.java`
 
```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 Sie sehen, dass für diesen Code einige zusätzliche Methoden erforderlich sind, damit er funktioniert. Diese schreiben wir als Nächstes.
 
### Erstellen eines Endpunkt-URL-Generators
 
 Wir müssen die Endpunkt-URL generieren, mit der wir eine Verbindung herstellen. Dies führen wir in derselben Klassendatei durch:
 
 **In derselben Datei** mit dem Namen `ToDoActivity.java`
 
 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE\_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


Beachten Sie, dass wir das Zugriffstoken der Anforderung im folgenden Code hinzufügen:

### Schritt 15: Schreiben einiger UX-Methoden

Für Android müssen wird einige Rückrufe behandeln, um die App zu betreiben. Dies sind `createAndShowDialog` und `onResume()`. Dies sollte Ihnen ziemlich einfach und vertraut vorkommen, wenn Sie bereits Android-Code geschrieben haben.

Diese schreiben wir als Nächstes:

**In derselben Datei** mit dem Namen `ToDoActivity.java`

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

Jetzt verwalten wir unsere Dialogrückrufe:

**In derselben Datei** mit dem Namen `ToDoActivity.java`


```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

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

Das ist alles! Sie sollten nun über eine Datei `ToDoActivity.java` verfügen, die kompiliert werden kann. Auch das gesamte Projekt sollte sich zu diesem Zeitpunkt kompilieren lassen.
    


### Schritt 16: Ausführen der Beispiel-App

Abschließend werden Sie die App in Android Studio oder Eclipse erstellen und ausführen. Registrieren Sie sich bei der App, oder melden Sie sich bei der App an, und erstellen Sie Aufgaben für den angemeldeten Benutzer. Melden Sie sich ab, melden Sie sich als ein anderer Benutzer wieder an, und erstellen Sie für diesen Benutzer Aufgaben.

Beachten Sie, wie die Aufgaben pro Benutzer in der API gespeichert werden, da die API die Benutzeridentität aus dem empfangenen Zugriffstoken extrahiert.

Als Referenz stellen wir das [vollständige Beispiel hier als ZIP-Datei](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) bereit. Sie können es auch aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


### Wichtige Informationen


#### Verschlüsselung

Mit ADAL werden die Token verschlüsselt und standardmäßig unter SharedPreferences gespeichert. Sehen Sie sich die StorageHelper-Klasse an, um einen Einblick in die Details zu erhalten. Für Android wurde AndroidKeyStore für 4.3 (API18) zum sicheren Speichern privater Schlüssel eingeführt. ADAL nutzt dieses Verfahren für API18 und höher. Falls Sie ADAL für frühere SDK-Versionen verwenden möchten, müssen Sie unter „AuthenticationSettings.INSTANCE.setSecretKey“ einen geheimen Schlüssel angeben.

#### Sitzungscookies in Webview

In Android Webview werden Sitzungscookies nach dem Schließen der App nicht gelöscht. Sie können dies mit dem unten angegebenen Beispielcode behandeln:
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Weitere Informationen zu Cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=AcomDC_0224_2016-->