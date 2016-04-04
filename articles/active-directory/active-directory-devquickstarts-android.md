<properties
	pageTitle="Azure AD Android – Erste Schritte | Microsoft Azure"
	description="In diesem Thema erfahren Sie, wie eine Android-Anwendung erstellt wird, die sich für die Anmeldung in Azure AD integriert und über OAuth durch Azure AD geschützte APIs aufruft."
	services="active-directory"
	documentationCenter="android"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="brandwe"/>

# Integrieren von Azure AD in eine Android-App

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Bei der Entwicklung einer Desktopanwendung ist es für Sie mit Azure AD einfach und problemlos möglich, Ihre Benutzer über deren Active Directory-Konten zu authentifizieren. Außerdem kann Ihre Anwendung damit auf sichere Weise alle Web-APIs nutzen, die per Azure AD geschützt sind, z. B. die Office 365-APIs oder die Azure-API.

Für Android-Clients, die auf geschützte Ressourcen zugreifen müssen, ist unter Azure AD die Active Directory-Authentifizierungsbibliothek (ADAL) verfügbar. Die einzige Aufgabe der ADAL besteht darin, Ihrer App das Abrufen von Zugriffstoken zu erleichtern. Um Ihnen zu zeigen, wie einfach es geht, erstellen wir hier eine Android-Anwendung mit einer Aufgabenliste, mit der folgende Aktionen ausgeführt werden können:

-	Abrufen von Zugriffstoken zum Aufrufen einer Aufgabenlisten-API mit dem [OAuth 2.0-Authentifizierungsprotokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx)
-	Abrufen der Aufgabenliste eines Benutzers
-	Abmelden von Benutzern

Zum Starten benötigen Sie einen Azure AD-Mandanten, in dem Sie Benutzer erstellen und eine Anwendung registrieren können. Wenn Sie noch nicht über einen Mandanten verfügen, können Sie hier [erfahren, wie Sie einen erhalten](active-directory-howto-tenant.md).

## Schritt 1: Herunterladen und Ausführen des „Node.js REST API TODO Sample Server“

Dieses Beispiel ist speziell für die Arbeit mit dem vorhandenen Beispiel zum Erstellen einer Aufgabenlisten-REST-API für Microsoft Azure Active Directory für einen einzelnen Mandanten gedacht. Dies ist eine Voraussetzung für den Schnellstart.

Informationen zur Einrichtung finden Sie unter den vorhandenen Beispielen:

* [Microsoft Azure Active Directory-Beispiel – REST-API-Dienst für „Node.js“](active-directory-devquickstarts-webapi-nodejs.md)

## Schritt 2: Registrieren Ihrer Web-API beim Microsoft Azure AD-Mandanten

**Wie gehe ich vor?**

*Microsoft Active Directory unterstützt das Hinzufügen von zwei Arten von Anwendungen. Web-APIs, die Dienste für Benutzer bereitstellen, und Anwendungen (im Web oder auf einem Gerät), die auf diese Web-APIs zugreifen. In diesem Schritt registrieren Sie die Web-API, die Sie lokal zum Testen dieses Beispiels ausführen. Normalerweise handelt es sich bei dieser Web-API um einen REST-Dienst, mit dem die Funktionen bereitgestellt werden, auf die Ihre Anwendung zugreifen soll. Microsoft Azure Active Directory kann jeden beliebigen Endpunkt schützen!*

*In diesem Thema wird davon ausgegangen, dass Sie die zuvor erwähnte Aufgabenlisten-REST-API registrieren. Allerdings funktioniert diese Methode für jede Web-API, die Azure Active Directory schützen soll.*

Schritte zum Registrieren einer Web-API bei Microsoft Azure AD

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie in der linken Navigationsleiste auf Active Directory.
3. Klicken Sie auf den Verzeichnismandanten, bei dem die Beispielanwendung registriert werden soll.
4. Klicken Sie auf die Registerkarte Anwendungen.
5. Klicken Sie in der Taskleiste auf Hinzufügen.
6. Klicken Sie auf „Eine von meinem Unternehmen entwickelte Anwendung hinzufügen“.
7. Geben Sie einen Anzeigenamen für die Anwendung ein, z. B. „TodoListService“, wählen Sie „Webanwendung und/oder Web-API“ aus, und klicken Sie auf „Weiter“.
8. Geben Sie als Anmelde-URL die Basis-URL für das Beispiel ein. Standardmäßig ist dies `https://localhost:8080`.
9. Geben Sie als App-ID-URI `https://<your_tenant_name>/TodoListService` ein, und ersetzen Sie dabei `<your_tenant_name>` durch den Namen Ihres Azure AD-Mandanten. Klicken Sie auf „OK“, um die Registrierung abzuschließen.
10. Klicken Sie im Azure-Portal auf die Registerkarte Konfigurieren Ihrer Anwendung.
11. **Suchen Sie die Client-ID, und kopieren Sie diese.** Sie benötigen diesen Wert später zum Konfigurieren Ihrer Anwendung.

## Schritt 3: Registrieren der systemeigenen Android-Beispiel-Clientanwendung

Zunächst müssen Sie Ihre Webanwendung registrieren. Danach müssen Sie Azure Active Directory über Ihre Anwendung informieren. Nur so kann die Anwendung mit der eben registrierten Web-API kommunizieren.

**Wie gehe ich vor?**

*Wie bereits erwähnt, unterstützt Microsoft Azure Active Directory das Hinzufügen von zwei Anwendungstypen. Web-APIs, die Dienste für Benutzer bereitstellen, und Anwendungen (im Web oder auf einem Gerät), die auf diese Web-APIs zugreifen. In diesem Schritt registrieren Sie die Anwendung dieses Beispiels. Dies ist erforderlich, damit die Anwendung den Zugriff auf die eben registrierte Web-API anfordern kann. Azure Active Directory verweigert Ihrer Anwendung sogar die Anforderung einer Anmeldung, wenn sie nicht registriert ist. Dies ist Teil der Sicherheit dieses Modells.*

*Hier wird davon ausgegangen, dass Sie die oben genannte Beispielanwendung registrieren. Die gleiche Methode funktioniert aber auch für jede selbst entwickelte Anwendung.*

**Warum integriere ich sowohl eine Anwendung als auch eine Web-API in ein- und denselben Mandanten?**

*Wie Sie vielleicht schon vermuten, können Sie auch eine Anwendung erstellen, von der auf eine externe API zugegriffen wird, die von einem anderen Mandanten aus in Azure Active Directory registriert wurde. Wenn Sie dies tun, werden Ihre Kunden aufgefordert, der Verwendung der API in der Anwendung zuzustimmen. Das Schöne ist aber, dass die Active Directory-Authentifizierungsbibliothek (ADAL) für iOS diese Zustimmung für Sie übernimmt! Wenn wir uns mit den erweiterten Funktionen beschäftigen, werden Sie feststellen, dass dies ein wichtiger Teil der Aufgaben ist, die für den Zugriff auf die Suite der Microsoft-APIs von Azure und Office sowie anderer Dienstanbieter erforderlich sind. Da Sie aber nun sowohl Ihre Web-API als auch Ihre Anwendung unter dem gleichen Mandanten registriert haben, wird Ihnen keine Aufforderung zur Zustimmung angezeigt. Dies ist eher die Regel, wenn Sie eine Anwendung nur für Ihr eigenes Unternehmen entwickeln.*

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie in der linken Navigationsleiste auf Active Directory.
3. Klicken Sie auf den Verzeichnismandanten, bei dem die Beispielanwendung registriert werden soll.
4. Klicken Sie auf die Registerkarte Anwendungen.
5. Klicken Sie in der Taskleiste auf Hinzufügen.
6. Klicken Sie auf „Eine von meinem Unternehmen entwickelte Anwendung hinzufügen“.
7. Geben Sie einen Anzeigenamen für die Anwendung ein, z. B. „TodoListClient-Android“, wählen Sie „Systemeigene Clientanwendung“ aus, und klicken Sie auf „Weiter“.
8. Geben Sie als Umleitungs-URI `http://TodoListClient` ein. Klicken Sie auf „Fertig stellen“.
9. Klicken Sie auf die Registerkarte „Konfigurieren“ der Anwendung.
10. Suchen Sie nach der Client-ID, und kopieren Sie diese. Sie benötigen diesen Wert später zum Konfigurieren Ihrer Anwendung.
11. Klicken Sie im Abschnitt „Berechtigungen für andere Anwendungen“ auf „Anwendung hinzufügen“. Wählen Sie in der Dropdownliste „Anzeigen“ die Option „Sonstiges“ aus, und klicken Sie auf das obere Häkchen. Suchen Sie nach „TodoListService“, und klicken Sie darauf. Klicken Sie anschließend auf das untere Häkchen, um die Anwendung hinzuzufügen. Wählen Sie in der Dropdownliste „Delegierte Berechtigungen“ die Option „Auf TodoListService zugreifen“ aus, und speichern Sie die Konfiguration.



Für die Erstellung mit Maven können Sie die Datei „pom.xml“ auf der obersten Ebene verwenden.

  * Klonen Sie dieses Repository in ein Verzeichnis Ihrer Wahl:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`

  * Führen Sie die Schritte im Abschnitt mit den [Voraussetzungen zum Einrichten von Maven für Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android) aus.
  * Richten Sie den Emulator mit SDK 19 ein.
  * Wechseln Sie zu dem Stammordner, in dem Sie das Repository geklont haben.
  * Führen Sie den folgenden Befehl aus: mvn clean install.
  * Wechseln Sie in das Verzeichnis mit dem Schnellstartbeispiel: cd samples\\hello.
  * Führen Sie den folgenden Befehl aus: mvn android:deploy android:run.
  * Die App wird gestartet.
  * Geben Sie die Anmeldeinformationen des Testbenutzers ein, um sie auszuprobieren.

Zusätzlich zum AAR-Paket werden auch JAR-Pakete übermittelt.

### Schritt 4: Herunterladen der Android-ADAL und Hinzufügen zum Eclipse-Arbeitsbereich

Wir haben die Verwendung für Sie vereinfacht. Sie haben mehrere Möglichkeiten zur Verwendung dieser Bibliothek in Ihrem Android-Projekt:

* Sie können den Quellcode nutzen, um diese Bibliothek in Eclipse zu importieren und mit Ihrer Anwendung zu verknüpfen.
* Bei Verwendung von Android Studio können Sie das Paketformat *AAR* nutzen und auf die Binärdateien verweisen.

####Option 1: ZIP-Datei mit Quellcode

Klicken Sie zum Herunterladen einer Kopie des Quellcodes rechts auf der Seite auf „Download ZIP“, oder klicken Sie [hier](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####Option 2: Quellcode per Git

Geben Sie einfach Folgendes ein, um den Quellcode des SDK über Git abzurufen:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####Option 3: Binärdateien per Gradle

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
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####Option 4: AAR über Maven

Wenn Sie in Eclipse das m2e-Plug-In verwenden, können Sie die Abhängigkeit in Ihrer Datei „pom.xml“ angeben:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####Option 5: JAR-Paket im Ordner „libs“
Sie können die JAR-Datei aus dem Repository von Maven abrufen und im Ordner *libs* Ihres Projekts ablegen. Außerdem müssen Sie die erforderlichen Ressourcen in Ihr Projekt kopieren, da sie in den JAR-Paketen nicht enthalten sind.


### Schritt 5: Hinzufügen von Verweisen auf die ADAL für Android zu Ihrem Projekt


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

7. Erstellen Sie eine Instanz von AuthenticationContext als Hauptaktivität. Die Details dieses Aufrufs liegen außerhalb des Umfangs dieser INFODATEI, aber das [Android-Beispiel für systemeigene Clients](https://github.com/AzureADSamples/NativeClient-Android) stellt eine gute Einstiegshilfe dar. Unten ist ein Beispiel aufgeführt:

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * HINWEIS: mContext ist ein Feld in Ihrer Aktivität.

8. Kopieren Sie diesen Codeblock, um die Beendigung der AuthenticationActivity zu behandeln, nachdem Benutzer Anmeldeinformationen eingegeben und den Autorisierungscode empfangen haben:

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Zum Anfordern eines Tokens definieren Sie einen Rückruf.

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Abschließend fordern Sie mit diesem Rückruf ein Token an:

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Erklärung der Parameter:

  * „resource“ ist erforderlich, und es handelt sich um die Ressource, auf die Sie zugreifen möchten.
  * „clientId“ ist erforderlich und stammt aus dem AzureAD-Portal.
  * Sie können „redirectUri“ als Ihren Paketnamen einrichten. Die Angabe für den Aufruf „acquireToken“ ist nicht erforderlich.
  * „PromptBehavior“ ist eine Hilfe zum Abfragen von Anmeldeinformationen, um Cache und Cookie zu überspringen.
  * „callback“ wird aufgerufen, nachdem der Autorisierungscode für ein Token ausgetauscht wurde.

  Der Rückruf verfügt über das Objekt „AuthenticationResult“, mit dem die Informationen „accesstoken“, „date expired“ und „idtoken“ bereitgestellt werden.

Optional: **acquireTokenSilent**

Sie können **acquireTokenSilent** aufrufen, um das Caching und die Aktualisierung des Tokens durchzuführen. Außerdem wird darüber die Synchronisierungsversion bereitgestellt. „userid“ wird als Parameter akzeptiert.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Broker**: Die Brokerkomponente wird über die Unternehmensportal-App von Microsoft Intune bereitgestellt. ADAL verwendet das Brokerkonto, falls unter diesem Authentifikator ein Benutzerkonto erstellt wurde und der Entwickler sich nicht für das Überspringen entschieden hat. Der Entwickler kann den Brokerbenutzer wie folgt überspringen:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Der Entwickler muss ein spezielles redirectUri-Element für die Brokernutzung registrieren. „redirectUri“ liegt im Format msauth://packagename/Base64UrlencodedSignature vor. Sie können das redirectUri-Element für Ihre App mit dem Skript „brokerRedirectPrint.ps1“ abrufen oder den API-Aufruf „mContext.getBrokerRedirectUri“ verwenden. Die Signatur bezieht sich auf Ihre Signaturzertifikate.

 Das aktuelle Brokermodell ist für einen einzelnen Benutzer ausgelegt. Über „AuthenticationContext“ wird die API-Methode zum Abrufen des Brokerbenutzers bereitgestellt.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 Der Brokerbenutzer wird zurückgegeben, wenn das Konto gültig ist.

 Ihr App-Manifest sollte über Berechtigungen zum Verwenden von Account Manager-Konten verfügen: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET\_ACCOUNTS
 * USE\_CREDENTIALS
 * MANAGE\_ACCOUNTS


Mit dieser exemplarischen Vorgehensweise haben Sie alles, was Sie für die erfolgreiche Integration in Azure Active Directory benötigen. Weitere Beispiele hierzu finden Sie im Repository „AzureADSamples/“ bei GitHub.

## Wichtige Informationen

### Anpassung

Bibliotheksprojektressourcen können von Ihren Anwendungsressourcen überschrieben werden. Dies kann beim Erstellen Ihrer App passieren. Aus diesem Grund können Sie das Layout der Authentifizierungsaktivität wie gewünscht anpassen. Sie müssen sicherstellen, dass die ID der Steuerelemente beibehalten wird, die von der ADAL genutzt werden (Webview).

### Broker

Die Brokerkomponente wird über die Unternehmensportal-App von Microsoft Intune bereitgestellt. Das Konto wird im Account Manager erstellt. Der Kontotyp lautet „com.microsoft.workaccount“. Es ist nur ein einzelnes SSO-Konto zulässig. Für diesen Benutzer wird ein SSO-Cookie erstellt, nachdem die Geräteabfrage (Challenge) für eine der Apps abgeschlossen wurde.

### Autoritäts-URL und AD FS

AD FS wird nicht als STS für die Produktion erkannt. Daher müssen Sie die Instanzenermittlung deaktivieren und für den AuthenticationContext-Konstruktor „false“ übergeben.

Für die Autoritäts-URL sind die STS-Instanz und der Mandantenname erforderlich: https://login.windows.net/yourtenant.onmicrosoft.com

### Abfragen von Cacheelementen

Die ADAL stellt den Standardcache unter SharedPreferences mit einigen einfachen Funktionen für die Cacheabfrage bereit. Sie können den aktuellen Cache aus AuthenticationContext abrufen:
```Java
 ITokenCacheStore cache = mContext.getCache();
```
Sie können auch eine eigene Cacheimplementierung bereitstellen, falls Sie sie anpassen möchten.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### PromptBehavior

ADAL verfügt über eine Option, mit der Sie das Verhalten der Eingabeaufforderung angeben können. PromptBehavior.Auto wird eingeblendet, wenn das Aktualisierungstoken ungültig ist und Benutzeranmeldeinformationen erforderlich sind. Mit PromptBehavior.Always wird die Cachenutzung übersprungen und immer die UI angezeigt.

### Automatische Tokenanforderung aus dem Cache und Aktualisierung

Bei dieser Methode wird kein UI-Popup verwendet, und es ist keine Aktivität erforderlich. Das Token wird aus dem Cache zurückgegeben, wenn es verfügbar ist. Wenn das Token abgelaufen ist, wird versucht, es zu aktualisieren. Falls das Aktualisierungstoken abgelaufen ist oder einen Fehler verursacht, wird eine AuthenticationException zurückgegeben.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

Mit dieser Methode können Sie auch einen Synchronisierungsaufruf durchführen. Sie können „null“ auf „callback“ (Rückruf) festlegen oder acquireTokenSilentSync verwenden.

### Diagnose

Unten sind die wichtigsten Informationsquellen zum Diagnostizieren von Problemen aufgeführt:

+ Ausnahmen
+ Protokolle
+ Netzwerkablaufverfolgung

Beachten Sie auch, dass die Korrelations-IDs zentraler Bestandteil der Diagnose in der Bibliothek sind. Sie können für Ihre Korrelations-IDs festlegen, dass sie bei Bedarf angefordert werden können, wenn Sie eine ADAL-Anforderung mit anderen Vorgängen in Ihrem Code korrelieren möchten. Wenn Sie keine Korrelations-ID festlegen, wird von der ADAL eine zufällige ID erzeugt. Diese Korrelations-ID wird dann auf alle Protokollmeldungen und Netzwerkaufrufe angewendet. Die selbst erzeugte ID ändert sich mit jeder Anforderung.

#### Ausnahmen

Dies ist natürlich die erste Diagnosemaßnahme. Wir versuchen, hilfreiche Fehlermeldungen für Sie bereitzustellen. Sie können uns ein Problem melden, falls Sie eine Fehlermeldung finden, die nicht hilfreich ist. Geben Sie dabei auch die Geräteinformationen an, z. B. Modell und SDK#.

#### Protokolle

Sie können die Bibliothek so konfigurieren, dass Protokollmeldungen generiert werden, die Sie zum Diagnostizieren von Problemen verwenden können. Sie können die Protokollierung konfigurieren, indem Sie den folgenden Aufruf verwenden. Hiermit können Sie einen Rückruf konfigurieren, der von der ADAL zum Weitergeben der Protokollmeldungen genutzt wird, wenn diese erzeugt werden.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
Meldungen können wie unten dargestellt in eine benutzerdefinierte Protokolldatei geschrieben werden. Leider gibt es keine standardmäßige Möglichkeit, Protokolle von einem Gerät abzurufen. Es sind einige Dienste verfügbar, die Ihnen hierbei behilflich sein können. Sie können auch eigene Wege erfinden, z. B. das Senden einer Datei an einen Server.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### Protokolliergrade

+ Error (Fehler) (Ausnahmen)
+ Warn (Warnung) (Warnung)
+ Info (Informationen) (Informationszwecke)
+ Verbose (Ausführlich) (mehr Details)

Der Protokolliergrad wird wie folgt festgelegt:
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Alle Protokollmeldungen werden zusätzlich zu allen benutzerdefinierten Protokollrückrufen an Logcat gesendet. Sie können ein Protokoll als Datei wie unten dargestellt aus Logcat abrufen:

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 Weitere Beispiele für adb-Befehle: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### Netzwerkablaufverfolgung

Sie können verschiedene Tools verwenden, um den per ADAL generierten HTTP-Datenverkehr zu erfassen. Dies ist besonders hilfreich, wenn Sie mit dem OAuth-Protokoll vertraut sind oder wenn Sie für Microsoft oder andere Supportkanäle Diagnoseinformationen bereitstellen müssen.

Fiddler ist das einfachste Tool für die Nachverfolgung von HTTP. Verwenden Sie die folgenden Links, um die Einrichtung der richtigen Aufzeichnung von ADAL-Netzwerkdatenverkehr durchzuführen. Damit das Tool nützlich ist, muss Fiddler – oder andere Tools, z. B. Charles – für die Aufzeichnung von unverschlüsseltem SSL-Datenverkehr konfiguriert werden. HINWEIS: Ablaufverfolgungen, die mit diesem Verfahren generiert werden, können äußerst sensible Berechtigungsinformationen enthalten, z. B. Zugriffstoken, Benutzernamen und Kennwörter. Wenn Sie Produktionskonten verwenden, sollten Sie diese Ablaufverfolgungen nicht für Dritte freigeben. Wenn Sie für eine Person eine Ablaufverfolgung bereitstellen müssen, um Support zu erhalten, ist das Nachstellen des Problems mit einem temporären Konto mit Benutzernamen und Kennwörtern ratsam, für die das Freigeben kein Problem darstellt.

+ [Einrichten von Fiddler für Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Konfigurieren von Fiddler-Regeln für ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### Dialogfeldmodus
Die acquireToken-Methode ohne Aktivität unterstützt die Dialogfeldaufforderung.

### Verschlüsselung

Mit ADAL werden die Token verschlüsselt und standardmäßig unter SharedPreferences gespeichert. Sehen Sie sich die StorageHelper-Klasse an, um einen Einblick in die Details zu erhalten. Für Android wurde AndroidKeyStore für 4.3 (API18) zum sicheren Speichern privater Schlüssel eingeführt. ADAL nutzt dieses Verfahren für API18 und höher. Falls Sie ADAL für frühere SDK-Versionen verwenden möchten, müssen Sie unter „AuthenticationSettings.INSTANCE.setSecretKey“ einen geheimen Schlüssel angeben.

### OAuth2-Beareranforderung

Die AuthenticationParameters-Klasse enthält Funktionen zum Abrufen des authorization\_uri-Werts aus der OAuth2-Beareranforderung.

### Sitzungscookies in Webview

In Android Webview werden Sitzungscookies nach dem Schließen der App nicht gelöscht. Dies können Sie mit dem unten angegebenen Beispielcode behandeln:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Weitere Informationen zu Cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### Außerkraftsetzungen von Ressourcen

Die ADAL-Bibliothek enthält die englischsprachigen Zeichenfolgen für die folgenden beiden ProgressDialog-Meldungen.

Diese sollten von Ihrer Anwendung überschrieben werden, falls lokalisierte Zeichenfolgen gewünscht werden.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### NTLM-Dialogfeld
ADAL Version 1.1.0 unterstützt das NTLM-Dialogfeld, das über das onReceivedHttpAuthRequest-Ereignis des WebViewClient-Elements verarbeitet wird. Dialogfeldlayout und Zeichenfolgen können angepasst werden.

### App-übergreifendes SSO
Informationen zum Aktivieren von App-übergreifendem SSO unter Android mit ADAL finden Sie [hier](active-directory-sso-android.md).


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0323_2016-->