<properties
	pageTitle="Integration des Azure Mobile Engagement Android SDKs"
	description="Neueste Updates und Verfahren für das Android SDK für Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="piyushjo" />

#Integrieren von Mobile Engagement unter Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

In diesem Verfahren wird die einfachste Art der Aktivierung der Analyse- und Überwachungsfunktionen von Mobile Engagement in Ihrer Android-Anwendung beschrieben.

> [AZURE.IMPORTANT]Die minimale API-Ebene des Android-SDKs muss „Level 10“ oder höher sein (Android 2.3.3 oder höher).

Mithilfe der folgenden Schritte kann der Protokollbericht aktiviert werden, der zum Berechnen aller Statistiken zu Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Informationen erforderlich ist. Der Bericht von Protokollen, die zur Berechnung anderer Statistiken wie Ereignisse, Fehler und Aufträge erforderlich ist, muss manuell mithilfe der Engagement-API erfolgen (siehe [So verwenden Sie die erweiterte Mobile Engagement API für Tags in Ihrer Android-App](mobile-engagement-android-use-engagement-api.md)), da diese Statistiken von der Anwendung abhängig sind.

##Einbetten des Engagement-SDKs und des Diensts in Ihr Android-Projekt

Laden Sie das Android-SDK [hier](https://aka.ms/vq9mfn) herunter. Rufen Sie `mobile-engagement-VERSION.jar` ab, und fügen Sie dies zum `libs`-Ordner des Android-Projekts hinzu (erstellen Sie den Ordner "libs", sofern dieser noch nicht vorhanden ist).

> [AZURE.IMPORTANT]Wenn Sie das Anwendungspaket mithilfe von ProGuard erstellen, müssen Sie einige Klassen behalten. Sie können den folgenden Codeausschnitt der Konfiguration verwenden:
>
>
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

Geben Sie die Engagement-Verbindungszeichenfolge durch Aufrufen der folgenden Methode in der Startprogrammaktivität an:

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

Die Verbindungszeichenfolge für die Anwendung wird im Azure-Portal angezeigt.

-   Fügen Sie die folgenden Android-Berechtigungen (vor dem `<application>`-Tag) hinzu, falls diese fehlen:

			<uses-permission android:name="android.permission.INTERNET"/>
			<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Fügen Sie den folgenden Abschnitt (zwischen den Tags `<application>` und `</application>` hinzu):

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

-   Ersetzen Sie `<Your application name>` durch den Namen Ihrer Anwendung.

> [AZURE.TIP]Das`android:label`-Attribut möglichst es Ihnen, den Namen des Engagement-Diensts so auszuwählen, wie er den Endbenutzern auf dem Bildschirm für aktive Dienste ihres Mobiltelefons angezeigt wird. Es wird empfohlen, für dieses Attribut den Wert `"<Your application name>Service"` festzulegen (z. B. `"AcmeFunGameService"`).

Durch die Angabe des Attributs `android:process` wird sichergestellt, dass der Engagement-Dienst im eigenen Prozess ausgeführt wird (durch Ausführen von Engagement in demselben Prozess wie die Anwendung wird der Thread „main/UI“ potenziell weniger reaktionsfähig).

> [AZURE.NOTE]Sämtlicher in `Application.onCreate()` hinzugefügter Code und andere Anwendungsrückruffunktionen werden für alle Prozesse der Anwendung ausgeführt, einschließlich des Engagement-Diensts. Dies kann unerwünschte Nebeneffekte haben (z. B. nicht erforderliche Speicherbelegungen und Threads im Engagement-Prozess, doppelte Übertragungsempfänger oder -dienste).

Wenn Sie `Application.onCreate()` außer Kraft setzen, wird empfohlen, den folgenden Codeausschnitt am Anfang der `Application.onCreate()`-Funktion hinzuzufügen:

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;

			   ... Your code...
			 }

Sie können dieselben Schritte für `Application.onTerminate()`, `Application.onLowMemory()` und `Application.onConfigurationChanged(...)` ausführen.

Sie können auch `EngagementApplication` anstelle von `Application` erweitern: Die Rückruffunktion `Application.onCreate()` übernimmt die Prozessprüfung und ruft `Application.onApplicationProcessCreate()` nur auf, wenn es sich bei dem aktuellen Prozess nicht um den Prozess handelt, der den Engagement-Dienst hostet. Dieselben Regeln gelten für die anderen Rückruffunktionen.

##Grundlegende Berichterstellung

### Empfohlene Methode: Überladen der `Activity`-Klassen

Um den Bericht für alle Protokolle zu aktivieren, die von Engagement zum Berechnen von Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Statistiken erforderlich sind, müssen Sie einfach dafür sorgen, dass all Ihre `*Activity`-Unterklassen von den entsprechenden `Engagement*Activity`-Klassen abgeleitet werden (wenn z. B. Ihre Legacy-Aktivität `ListActivity` erweitert, sorgen Sie dafür, dass sie `EngagementListActivity` erweitert).

**Ohne Engagement:**

			package com.company.myapp;

			import android.app.Activity;
			import android.os.Bundle;

			public class MyApp extends Activity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

**Mit Engagement:**

			package com.company.myapp;

			import com.microsoft.azure.engagement.activity.EngagementActivity;
			import android.os.Bundle;

			public class MyApp extends EngagementActivity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

> [AZURE.IMPORTANT]Stellen Sie bei der Verwendung von `EngagementListActivity` oder `EngagementExpandableListActivity` sicher, dass jeder Aufruf von `requestWindowFeature(...);` vor dem Aufruf von `super.onCreate(...);` erfolgt, da es andernfalls zu einem Absturz kommt.

Wir stellen Unterklassen von `FragmentActivity` und `MapActivity` bereit, aber zur Vermeidung von Problemen mit Anwendungen, die **ProGuard** verwenden, wurden sie nicht in `engagement.jar` einbezogen.

Sie finden diese Klassen im Ordner `src`, und Sie können sie in Ihr Projekt kopieren. Die Klassen befinden sich auch in **JavaDoc**.

### Alternative Methode: `startActivity()` und `endActivity()` manuell aufrufen.

Wenn Sie die `Activity`-Klassen nicht überladen können oder möchten, können Sie die Aktivitäten stattdessen durch direktes Aufrufen der Methoden von `EngagementAgent` starten und beenden.

> [AZURE.IMPORTANT]Das Android-SDK ruft die `endActivity()`-Methode niemals auf, auch nicht beim Schließen der Anwendung (unter Android werden Anwendungen eigentlich niemals geschlossen). Daher wird *DRINGEND* empfohlen, die `startActivity()`-Methode in der `onResume`-Rückruffunktion *ALLER* Aktivitäten und die `endActivity()`-Methode in der `onPause()`-Rückruffunktion *ALLER* Aktivitäten aufzurufen. Dies ist die einzige Möglichkeit, um sicherzustellen, dass die Sitzungen nicht verloren gehen. Wenn eine Sitzung verloren geht, wird die Verbindung vom Engagement-Dienst zum Engagement-Back-End niemals getrennt (da der Dienst verbunden bleibt, so lange eine Sitzung aussteht).

Beispiel:

			public class MyActivity extends Some3rdPartyActivity
			{
			  @Override
			  protected void onResume()
			  {
			    super.onResume();
			    String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
			    EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
			  }

			  @Override
			  protected void onPause()
			  {
			    super.onPause();
			    EngagementAgent.getInstance(this).endActivity();
			  }
			}

Dieses Beispiel ähnelt der `EngagementActivity`-Klasse und ihrer Varianten, deren Quellcode im Ordner `src` bereitgestellt wird.

##Test

Überprüfen Sie jetzt die Integration, indem Sie die mobile App in einem Emulator oder auf einem Gerät ausführen und sicherstellen, dass auf der Registerkarte "Überwachen" eine Sitzung registriert wird.

Die nächsten Abschnitte sind optional.

##Speicherortberichte

Wenn Sie die Speicherorte melden möchten, müssen Sie einige Zeilen zur Konfiguration hinzufügen (zwischen den Tags `<application>` und `</application>`).

### Verzögerte Bereichsspeicherortberichte

Die verzögerte Berichterstellung für Bereichsspeicherorte ermöglicht es Ihnen, das Land, die Region und den Ort zu melden, die Geräten zugeordnet sind. Diese Art der Berichterstellung für Speicherorte verwendet ausschließlich Netzwerkspeicherorte (auf Basis von Zell-ID oder WLAN). Der Gerätebereich wird höchstens einmal pro Sitzung gemeldet. Das GPS wird niemals verwendet, daher hat diese Art von Standortbericht sehr geringe (oder fast keine) Auswirkungen auf den Akku.

Die gemeldeten Bereiche werden dazu verwendet, um geografische Statistiken zu Benutzern, Sitzungen, Ereignissen und Fehlern zu berechnen. Sie können auch als Kriterium für Reach-Kampagnen verwendet werden.

Verzögerte Bereichsspeicherortberichte aktivieren Sie mithilfe der zuvor in diesem Verfahren erwähnten Konfiguration:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Sie müssen auch die folgende Berechtigung hinzufügen, sofern diese noch nicht vorhanden ist:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Sie können auch weiterhin ``ACCESS_FINE_LOCATION`` verwenden, wenn dies bereits Teil Ihrer Anwendung ist.

### Echtzeit-Berichterstellung für Speicherorte

Mithilfe der Echtzeit-Berichterstellung für Speicherorte können der Längen- und Breitengrad gemeldet werden, die Geräten zugeordnet sind. Diese Art der Berichterstellung für Speicherorte verwendet ausschließlich Netzwerkspeicherorte (auf Basis von Zell-ID oder WLAN), und die Berichterstellung ist nur aktiv, wenn die Anwendung im Vordergrund ausgeführt wird (d. h. während einer Sitzung).

Echtzeit-Speicherorte werden *NICHT* zum Berechnen von Statistiken verwendet. Ihr einziger Zweck ist es, die Verwendung des Echtzeit-Geofencing <Reach-Audience-geofencing >Kriteriums in Reach-Kampagnen zu ermöglichen.

Die Echtzeit-Berichterstellung für Speicherorte aktivieren Sie mithilfe der zuvor in diesem Verfahren erwähnten Konfiguration:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Sie müssen auch die folgende Berechtigung hinzufügen, sofern diese noch nicht vorhanden ist:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Sie können auch weiterhin ``ACCESS_FINE_LOCATION`` verwenden, wenn dies bereits Teil Ihrer Anwendung ist.

#### GPS-basierte Berichterstellung

Die Echtzeit-Berichterstellung für Speicherorte verwendet standardmäßig nur netzwerkbasierte Speicherorte. Verwenden Sie das Konfigurationsobjekt, um die Verwendung von GPS-basierten Speicherorten (die viel genauer sind) zu aktivieren:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Sie müssen auch die folgende Berechtigung hinzufügen, sofern diese noch nicht vorhanden ist:

			<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Berichterstellung im Hintergrund

Die Echtzeit-Berichterstellung für Speicherorte ist standardmäßig nur aktiv, wenn die Anwendung im Vordergrund ausgeführt wird (d. h. während einer Sitzung). Verwenden Sie das Konfigurationsobjekt, um die Berichterstellung auch im Hintergrund zu aktivieren:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE]Wenn die Anwendung im Hintergrund ausgeführt wird, werden nur netzwerkbasierte Speicherorte gemeldet, auch wenn Sie das GPS aktiviert haben.

Der Hintergrundbericht für Speicherorte wird beendet, wenn der Benutzer sein Gerät neu startet. Sie können dies hinzufügen, damit zur Startzeit automatisch ein Neustart erfolgt:

			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>

Sie müssen auch die folgende Berechtigung hinzufügen, sofern diese noch nicht vorhanden ist:

			<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### Android M-Berechtigungen

Ab Android M werden bestimmte Berechtigungen zur Laufzeit verwaltet und erfordern die Genehmigung des Benutzers.

Die Laufzeitberechtigungen werden für neue App-Installationen standardmäßig deaktiviert, wenn Sie auf die Android-API-Ebene 23 abzielen. Andernfalls sind sie standardmäßig aktiviert.

Der Benutzer kann diese Berechtigungen im Einstellungsmenü des Geräts aktivieren oder deaktivieren. Das Deaktivieren der Berechtigungen im Systemmenü beendet alle Hintergrundprozesse der Anwendung, was ein Systemverhalten ist und keine Auswirkungen auf die Fähigkeit hat, Pushbenachrichtigungen im Hintergrund zu empfangen.

Im Kontext von Mobile Engagement sind die Berechtigungen, die eine Genehmigung zur Laufzeit erfordern, folgende:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE` (nur bei Android-API-Ebene 23 als Ziel)

Der externe Speicher wird nur für die Überblicksfunktion von Reach verwendet. Wenn Sie feststellen, dass es störend ist, die Benutzer nach dieser Berechtigung aufzufordern, können Sie sie entfernen, wenn sie nur für Mobile Engagement verwendet wird. Beachten Sie, dass dadurch jedoch die Überblicksfunktion deaktiviert wird.

Für die Speicherortfunktionen sollten Sie Berechtigungen für Benutzer über ein Standarddialogfeld anfordern. Wenn der Benutzer zustimmt, müssen Sie ``EngagementAgent`` mitteilen, dass diese Änderung in Echtzeit vorgenommen wird (andernfalls wird die Änderung verarbeitet, sobald der Benutzer die Anwendung das nächste Mal startet).

Hier ist ein Codebeispiel, dass Sie in einer Aktivität Ihrer Anwendung verwenden können, um Berechtigungen anzufordern und das Ergebnis (wenn positiv) weiterzuleiten an ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##Erweiterte Berichterstellung

Wenn Sie anwendungsspezifische Ereignisse, Fehler und Aufträge optional melden möchten, müssen Sie die Engagement-API über die Methoden der `EngagementAgent`-Klasse verwenden. Ein Objekt dieser Klasse kann durch Aufrufen der statischen `EngagementAgent.getInstance()`-Methode abgerufen werden.

Die Engagement-API ermöglicht es, alle erweiterten Funktionen von Engagement zu verwenden. Ausführliche Informationen finden Sie unter „Verwenden der Engagement-API unter Android (sowie in der technischen Dokumentation der `EngagementAgent`-Klasse).

##Erweiterte Konfiguration (in „AndroidManifest.xml“)

Wenn Sie sicherstellen möchten, dass Statistiken bei der Verwendung von WLAN-Verbindungen oder bei Deaktiviertem Bildschirm in Echtzeit gesendet werden, fügen Sie die folgende optionale Berechtigung hinzu:

			<uses-permission android:name="android.permission.WAKE_LOCK"/>

Fügen Sie folgenden Code (zwischen den Tags `<application>` und `</application>`) hinzu, um Absturzberichte zu deaktivieren:

			<meta-data android:name="engagement:reportCrash" android:value="false"/>

Standardmäßig meldet der Engagement-Dienst Protokolle in Echtzeit. Wenn Ihre Anwendung Protokolle sehr häufig meldet, ist es besser, die Protokolle zu puffern und sie in regelmäßigen Abständen alle auf einmal zu melden (dies wird als „Burstmodus“ bezeichnet). Fügen Sie dazu Folgendes (zwischen den Tags `<application>` und `</application>`) hinzu:

			<meta-data android:name="engagement:burstThreshold" android:value="<interval between too bursts (in milliseconds)>"/>

Der Burst-Modus verlängert leicht die Akkulaufzeit, wirkt sich jedoch auf den Engagement-Monitor aus: Die Dauer von allen Sitzungen und Aufträgen wird auf den Burst-Schwellenwert gerundet (folglich sind eventuell Sitzungen und Aufträge, die kürzer als der Burst-Schwellenwert sind, möglicherweise nicht sichtbar). Es wird empfohlen, einen Burst-Schwellenwert von höchstens 30000 (30 s) zu verwenden.

Der Engagement-Dienst richtet die Verbindung zu unseren Servern standardmäßig sofort ein, sobald das Netzwerk verfügbar ist. Fügen Sie Folgendes (zwischen den Tags `<application>` und `</application>`) hinzu, um die Verbindung zu verschieben:

			<meta-data android:name="engagement:connection:delay" android:value="<delay (in milliseconds)>"/>

Eine Sitzung wird standardmäßig 10 Sekunden nach dem Ende ihrer letzten Aktivität beendet (was in der Regel durch Drücken der Start- oder Zurück-Taste, durch den Wechsel des Mobiltelefons in den Leerlauf oder durch den Wechsel zu einer anderen Anwendung ausgelöst wird). Dadurch wird eine Sitzungsteilung vermieden, wenn der Benutzer die Sitzung beendet und sehr schnell zur Anwendung zurückkehrt (dies kann bei einer Bildübernahme, beim Prüfen einer Benachrichtigung usw. auftreten). Sie können diesen Parameter ändern. Fügen Sie dazu Folgendes (zwischen den Tags `<application>` und `</application>`) hinzu:

			<meta-data android:name="engagement:sessionTimeout" android:value="<session timeout (in milliseconds)>"/>

##Deaktivieren der Protokollberichterstellung

### Verwenden eines Methodenaufrufs

Sie können Folgendes aufrufen, wenn von Engagement keine Protokolle mehr gesendet werden sollen:

			EngagementAgent.getInstance(context).setEnabled(false);

Dieser Aufruf ist persistent: er verwendet eine freigegebene Einstellungsdatei.

Wenn Engagement beim Aufrufen dieser Funktion aktiv ist, kann es bis zu einer Minute dauern, bis der Dienst beendet wird. Beim nächsten Start der Anwendung wird der Dienst jedoch überhaupt nicht gestartet.

Sie können die Protokollberichterstellung erneut aktivieren, indem Sie dieselbe Funktion mit `true` aufrufen.

### Integration in eigenem `PreferenceActivity`

Anstatt diese Funktion aufzurufen, können Sie diese Einstellung auch direkt im vorhandenen `PreferenceActivity` integrieren.

Sie können Engagement für die Verwendung Ihrer Einstellungsdatei (mit gewünschtem Modus) in der Datei `AndroidManifest.xml` mithilfe von `application meta-data` konfigurieren:

-   Der `engagement:agent:settings:name`-Schlüssel wird zum Definieren des Namens der freigegebenen Einstellungsdatei verwendet.
-   Der `engagement:agent:settings:mode`-Schlüssel wird zum Definieren des Modus der freigegebenen Einstellungsdatei verwendet. Sie sollten denselben Modus wie im `PreferenceActivity` verwenden. Der Modus muss als Zahl übergeben werden. Wenn Sie eine Kombination von konstanten Flags im Code verwenden, überprüfen Sie den Gesamtwert.

Engagement verwendet immer den booleschen `engagement:key`-Schlüssel innerhalb der Einstellungsdatei zum Verwalten dieser Einstellung.

Das folgende Beispiel von `AndroidManifest.xml` veranschaulicht die Standardwerte:

			<application>
			    [...]
			    <meta-data
			      android:name="engagement:agent:settings:name"
			      android:value="engagement.agent" />
			    <meta-data
			      android:name="engagement:agent:settings:mode"
			      android:value="0" />

Dann können Sie `CheckBoxPreference` wie folgt im Einstellungslayout hinzufügen:

			<CheckBoxPreference
			  android:key="engagement:enabled"
			  android:defaultValue="true"
			  android:title="Use Engagement"
			  android:summaryOn="Engagement is enabled."
			  android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094

<!---HONumber=AcomDC_1203_2015-->