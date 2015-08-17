<properties 
	pageTitle="Integration des Azure Mobile Engagement Android SDKs" 
	description="Neueste Updates und Verfahren für das Android SDK für Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

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

Laden Sie das Android-SDK [hier](http://go.microsoft.com/?linkid=9863935&clcid=0x409) herunter. Rufen Sie `mobile-engagement-VERSION.jar` ab, und fügen Sie dies zum `libs`-Ordner des Android-Projekts hinzu (erstellen Sie den Ordner "libs", sofern dieser noch nicht vorhanden ist).

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

-   Für einige Gerätemodelle kann der Engagement-Gerätebezeichner nicht aus der „ANDROID\_ID“ generiert werden (sie ist möglicherweise fehlerhaft oder nicht verfügbar). In diesem Fall generiert das SDK einen zufälligen Gerätebezeichner und versucht dann, ihn im externen Speicher des Geräts zu speichern, damit derselbe Gerätebezeichner von anderen Engagement-Anwendungen gemeinsam genutzt werden kann (er wird auch als gemeinsam genutzte Voreinstellung gespeichert, um sicherzustellen, dass die Anwendung unabhängig vom externen Speicher immer denselben Gerätebezeichner verwendet). Damit dieser Mechanismus ordnungsgemäß funktioniert, müssen Sie die folgende Berechtigung hinzufügen, wenn diese fehlt (vor dem `<application>`-Tag):

			<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

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

Die gemeldeten Bereiche werden dazu verwendet, um geografische Statistiken zu Benutzern, Sitzungen, Ereignissen und Fehlern zu berechnen. Sie können auch als Kriterium für Reach-Kampagnen verwendet werden. Der für ein Gerät zuletzt gemeldete bekannte Bereich kann mithilfe der [Geräte-API] abgerufen werden.

Fügen Sie Folgendes hinzu, um die verzögerte Berichterstellung für Bereichsspeicherorte zu aktivieren:

			<meta-data android:name="engagement:locationReport:lazyArea" android:value="true"/>

Sie müssen auch die folgende Berechtigung hinzufügen, sofern diese noch nicht vorhanden ist:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

### Echtzeit-Berichterstellung für Speicherorte

Mithilfe der Echtzeit-Berichterstellung für Speicherorte können der Längen- und Breitengrad gemeldet werden, die Geräten zugeordnet sind. Diese Art der Berichterstellung für Speicherorte verwendet ausschließlich Netzwerkspeicherorte (auf Basis von Zell-ID oder WLAN), und die Berichterstellung ist nur aktiv, wenn die Anwendung im Vordergrund ausgeführt wird (d. h. während einer Sitzung).

Echtzeit-Speicherorte werden *NICHT* zum Berechnen von Statistiken verwendet. Ihr einziger Zweck ist es, die Verwendung des Echtzeit-Geofencing <Reach-Audience-geofencing >Kriteriums in Reach-Kampagnen zu ermöglichen.

Fügen Sie Folgendes hinzu, um die Echtzeit-Berichterstellung für Speicherorte zu aktivieren:

			<meta-data android:name="engagement:locationReport:realTime" android:value="true" />

Sie müssen auch die folgende Berechtigung hinzufügen, sofern diese noch nicht vorhanden ist:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

#### GPS-basierte Berichterstellung

Die Echtzeit-Berichterstellung für Speicherorte verwendet standardmäßig nur netzwerkbasierte Speicherorte. Fügen Sie Folgendes hinzu, um die Verwendung von GPS-basierten Speicherorten (die viel genauer sind) zu aktivieren:

			<meta-data android:name="engagement:locationReport:realTime:fine" android:value="true" />

Sie müssen auch die folgende Berechtigung hinzufügen, sofern diese noch nicht vorhanden ist:

			<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Berichterstellung im Hintergrund

Die Echtzeit-Berichterstellung für Speicherorte ist standardmäßig nur aktiv, wenn die Anwendung im Vordergrund ausgeführt wird (d. h. während einer Sitzung). Fügen Sie Folgendes hinzu, um die Berichterstellung auch im Hintergrund zu aktivieren:

			<meta-data android:name="engagement:locationReport:realTime:background" android:value="true" />

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
[Geräte-API]: http://go.microsoft.com/?linkid=9876094
 

<!---HONumber=August15_HO6-->