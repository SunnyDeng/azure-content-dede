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

# Integrieren von Engagement Reach unter Android

> [AZURE.IMPORTANT]Bevor Sie dieser Anleitung folgen, müssen Sie das unter „Integrieren von Mobile Engagement unter Android“ beschriebene Integrationsverfahren befolgen.

## Standardintegration

Das Reach SDK erfordert **Android Support-Bibliothek (Version 4)**.

Die schnellste Möglichkeit zum Hinzufügen der Bibliothek zum Projekt in **Eclipse** erfolgt über `Right click on your project -> Android Tools -> Add Support Library...`.

Wenn Sie kein Eclipse verwenden, können Sie die entsprechenden Anweisungen [hier] lesen.

Kopieren Sie die Reach-Ressourcendateien aus dem SDK in Ihr Projekt:

-   Kopieren Sie Dateien aus dem Ordner `res/layout`, die mit dem SDK bereitgestellt wurden, in den Ordner `res/layout` Ihrer Anwendung.
-   Kopieren Sie Dateien aus dem Ordner `res/drawable`, die mit dem SDK bereitgestellt wurden, in den Ordner `res/drawable` Ihrer Anwendung.

Bearbeiten Sie Ihre `AndroidManifest.xml`-Datei:

-   Fügen Sie den folgenden Abschnitt (zwischen den Tags `<application>` und `</application>` hinzu):

			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT" />
			    <data android:mimeType="text/plain" />
			  </intent-filter>
			</activity>
			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT" />
			    <data android:mimeType="text/html" />
			  </intent-filter>
			</activity>
			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
			    <category android:name="android.intent.category.DEFAULT" />
			  </intent-filter>
			</activity>
			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="android.intent.action.BOOT_COMPLETED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
			    <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			  </intent-filter>
			</receiver>

-   Sie benötigen diese Berechtigung, um Systembenachrichtigungen wiederzugeben, die beim Start nicht angeklickt wurden (andernfalls werden sie auf dem Datenträger gespeichert, aber nicht mehr angezeigt: Sie müssen dies wirklich einbeziehen).

			<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   Geben Sie ein Symbol an, das für Benachrichtigungen verwendet wird (sowohl in der App als auch für das System), indem Sie den folgenden Abschnitt (zwischen den Tags `<application>` und `</application>`) kopieren und bearbeiten:

			<meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT]Dieser Abschnitt ist **obligatorisch**, wenn Sie beim Erstellen von Reach-Kampagnen die Verwendung von Systembenachrichtigungen planen. Android verhindert, dass Systembenachrichtigungen ohne Symbole angezeigt werden. Wenn Sie diesen Abschnitt daher auslassen, können Ihre Endbenutzer sie nicht empfangen.

-   Wenn Sie Kampagnen mit Systembenachrichtigungen erstellen, die eine allgemeine Übersicht verwenden, müssen Sie die folgenden Berechtigungen (hinter dem `</application>`-Tag) hinzufügen, falls diese nicht vorhanden sind:

			<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
			<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

-   Für Systembenachrichtigungen können Sie in der Reach-Kampagne auch angeben, ob das Gerät klingeln und/oder vibrieren soll. Damit es funktioniert, müssen Sie sicherstellen, dass Sie die folgende Berechtigung (hinter dem `</application>`-Tag) deklariert haben:

			<uses-permission android:name="android.permission.VIBRATE" />

	Ohne diese Berechtigung verhindert Android, dass Systembenachrichtigungen angezeigt werden, wenn Sie die Option zum Klingeln oder Vibrieren im Reichweitenkampagnen-Manager aktiviert haben.

-   Wenn Sie Ihre Anwendung mithilfe von **ProGuard** erstellt haben und Fehler in Bezug auf die Android Support-Bibliothek oder die Engagement-JAR-Datei aufgetreten sind, fügen Sie die folgenden Zeilen zur Datei `proguard.cfg` hinzu:

			-dontwarn android.**
			-keep class android.support.v4.** { *; }

**Ihre Anwendung ist jetzt bereit zum Empfangen und Anzeigen von Reichweitenkampagnen!**

## Behandeln des Datenpush

### Integration

Damit Ihre Anwendung einen Reach-Datenpush empfangen kann, müssen Sie eine Unterklasse von `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` erstellen und darauf in der Datei `AndroidManifest.xml` verweisen (zwischen den Tags `<application>` und/oder `</application>`):

			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
			  </intent-filter>
			</receiver>

Dann können Sie die Rückruffunktionen `onDataPushStringReceived` und `onDataPushBase64Received` außer Kraft setzen. Beispiel:

			public class MyDataPushReceiver extends EngagementReachDataPushReceiver
			{
			  @Override
			  protected Boolean onDataPushStringReceived(Context context, String category, String body)
			  {
			    Log.d("tmp", "String data push message received: " + body);
			    return true;
			  }
			
			  @Override
			  protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
			  {
			    Log.d("tmp", "Base64 data push message received: " + encodedBody);
			    // Do something useful with decodedBody like updating an image view
			    return true;
			  }
			}

### Category

Der category-Parameter ist optional, wenn Sie eine Datenpushkampagne erstellen und eine Filterung von Datenpushvorgängen ermöglichen. Dies ist hilfreich, wenn Sie über verschiedene Übertragungsempfänger verfügen, die unterschiedliche Datenpushtypen verarbeiten, oder Sie verschiedene Arten von `Base64`-Daten per Push übertragen und dabei vor der Analyse ihren Typ ermitteln möchten.

### Rückgabeparameter von Rückruffunktionen

Hier folgen einige Richtlinien zur ordnungsgemäßen Behandlung von Rückgabeparametern von `onDataPushStringReceived` und `onDataPushBase64Received`:

-   Ein Übertragungsempfänger sollte in der Rückruffunktion `null` zurückgeben, wenn ihm nicht bekannt ist, wie ein Datenpush behandelt wird. Sie können die Kategorie zur Ermittlung verwenden, ob der Übertragungsempfänger den Datenpush bearbeiten soll.
-   Einer der Übertragungsempfänger sollte in der Rückruffunktion `true` zurückgeben, wenn der Datenpush akzeptiert wird.
-   Einer der Übertragungsempfänger sollte in der Rückruffunktion `false` zurückgeben, wenn er den Datenpush erkennt, ihn aber aus einem beliebigen Grund verwirft. Geben Sie z. B. `false` zurück, wenn die empfangenen Daten ungültig sind.
-   Wenn ein Übertragungsempfänger `true` zurückgibt, während ein anderer für denselben Datenpush `false` zurückgibt, dann ist das Verhalten undefiniert. Daher sollten Sie niemals entsprechend vorgehen.

Der Rückgabetyp wird nur für Reach-Statistiken verwendet:

-   `Replied` wird erhöht, wenn einer der Übertragungsempfänger entweder `true` oder `false` zurückgegeben hat.
-   `Actioned` wird nur erhöht, wenn einer der Übertragungsempfänger `true` zurückgegeben hat.

## Empfangen von Kampagnen zu beliebigen Zeiten

Wenn das obige Integrationsverfahren befolgt wird, stellt der Engagement-Dienst nur eine Verbindung zu den Engagement-Servern her, wenn die Statistiken gemeldet werden müssen (zuzüglich einer Zeitüberschreitung von einer Minute). Folglich **können Reichweitenkampagnen nur während einer Benutzersitzung empfangen werden**. Erfreulicherweise kann Engagement so konfiguriert werden, dass es **Ihrer Anwendung gestattet wird, jederzeit Reichweitenkampagnen zu empfangen**, auch wenn sich das Gerät im Ruhemodus befindet (das Gerät muss natürlich über eine aktive Netzwerkverbindung verfügen, da Nachrichten verzögert werden, wenn das Gerät offline ist).

Damit Sie die „jederzeit mögliche“ Pushübertragung nutzen können, müssen Sie in Abhängigkeit von den Zielgeräten mindestens einen systemeigenen Pushdienst verwenden:

  - Google Play-Geräte: Verwenden Sie [Google Cloud Messaging], indem Sie die [Anleitung unter Integrieren von GCM mit Engagement](mobile-engagement-android-gcm-integrate.md) befolgen.
  - Amazon-Geräte: Verwenden Sie [Amazon Device Messaging], indem Sie die [Anleitung unter Integrieren von ADM mit Engagement](mobile-engagement-android-adm-integrate.md) befolgen

Wenn Sie Ihre Vorgehensweise auf Amazon- und Google Play-Geräte ausrichten möchten, ist es möglich, für die Entwicklung alles in eine „AndroidManifest.xml/APK“ zu verpacken. Bei der Übermittlung an Amazon wird die Anwendung möglicherweise zurückgewiesen, wenn der GCM-Code erkannt wird.

In diesem Fall sollten Sie mehrere APK-Dateien verwenden.

## Anpassen von Kampagnen

Sie können die im Reach-SDK bereitgestellten Layouts ändern, um Kampagnen anzupassen.

Sie sollten alle in den Layouts verwendeten Bezeichner sowie die Ansichtstypen beibehalten, die einen Bezeichner verwenden, insbesondere für Text- und Bildansichten. Einige Ansichten werden nur verwendet, um Bereiche auszublenden oder anzuzeigen, daher kann ihr Typ geändert werden. Überprüfen Sie den Quellcode, wenn Sie beabsichtigen, den Typ einer Ansicht in den bereitgestellten Layouts zu ändern.

### Benachrichtigungen

Es gibt zwei Arten von Benachrichtigungen: System- und In-App-Benachrichtigungen, die unterschiedliche Layoutdateien verwenden.

#### Systembenachrichtigungen

Sie müssen die **Kategorien** verwenden, um Systembenachrichtigungen anzupassen. Sie können zu [Kategorien](#categories) wechseln.

#### In-App-Benachrichtigungen

Eine In-App-Benachrichtigung ist standardmäßig eine Ansicht, die dank der Android `addContentView()`-Methode dynamisch zur Benutzeroberfläche der aktuellen Aktivität hinzugefügt wird. Dies wird als Benachrichtigungsüberlagerung bezeichnet. Benachrichtigungsüberlagerungen sind hervorragend für eine schnelle Integration geeignet, da sie von Ihnen keine Änderung des Layouts in Ihrer Anwendung erfordern.

Sie können einfach die Datei `engagement_notification_area.xml` nach Belieben ändern, um das Aussehen der Benachrichtigungsüberlagerungen zu modifizieren.

> [AZURE.NOTE]Die Datei  `engagement_notification_overlay.xml` ist die Datei, die zum Erstellen einer Benachrichtigungsüberlagerung verwendet wird. Sie enthält die Datei `engagement_notification_area.xml`. Sie können die Datei auch anpassen, um sie gemäß Ihren Anforderungen zu ändern (z. B. zur Positionierung des Benachrichtigungsbereichs innerhalb der Überlagerung).

##### Einbeziehen des Benachrichtigungslayouts im Rahmen einer Aktivitätsüberlagerung

Überlagerungen sind hervorragend für eine schnelle Integration geeignet, aber können in besonderen Fällen ungelegen kommen oder Nebeneffekte aufweisen. Das Überlagerungssystem kann auf einer Aktivitätsebene angepasst werden, wodurch es sich einfach gestaltet, Nebeneffekte für besondere Aktivitäten zu verhindern.

Sie können unser Benachrichtigungslayout dank der Android-Anweisung **include** in Ihr vorhandenes Layout einbeziehen. Nachfolgend finden Sie ein Beispiel eines geänderten `ListActivity`-Layouts, das nur ein `ListView` enthält.

**Vor der Engagement-Integration:**

			<?xml version="1.0" encoding="utf-8"?>
			<ListView
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:id="@android:id/list"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent" />

**Nach der Engagement-Integration:**

			<?xml version="1.0" encoding="utf-8"?>
			<LinearLayout
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:orientation="vertical"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent">
			
			  <ListView
			    android:id="@android:id/list"
			    android:layout_width="fill_parent"
			    android:layout_height="fill_parent"
			    android:layout_weight="1" />
			
			  <include layout="@layout/engagement_notification_area" />
			
			</LinearLayout>

In diesem Beispiel wurde ein übergeordneter Container hinzugefügt, da das ursprüngliche Layout eine Listenansicht als Element der obersten Ebene verwendet hat. Zudem wurde `android:layout_weight="1"` hinzugefügt, damit unter einer mit `android:layout_height="fill_parent"` konfigurierten Listenansicht eine Ansicht hinzugefügt werden kann.

Das Engagement Reach SDK erkennt automatisch, dass das Benachrichtigungslayout in diese Aktivität einbezogen wird, daher wird für diese Aktivität keine Überlagerung hinzugefügt.

> [AZURE.TIP]Wenn Sie „ListActivity“ in Ihrer Anwendung verwenden, verhindert eine sichtbare Reichweitenüberlagerung, dass Sie in der Listenansicht weiterhin auf angeklickte Elemente reagieren. Dies ist ein bekanntes Problem. Es wird empfohlen, das Benachrichtigungslayout wie im vorherigen Beispiel in Ihr eigenes Aktivitätenlayout einzubetten, um dieses Problem zu umgehen.

##### Deaktivieren der Anwendungsbenachrichtigung durch eine Aktivität

Wenn die Überlagerung nicht zu Ihrer Aktivität hinzugefügt werden soll, und Sie das Benachrichtigungslayout nicht in Ihr eigenes Layout einbeziehen möchten, können Sie die Überlagerung für diese Aktivität in der Datei `AndroidManifest.xml` deaktivieren, indem Sie wie im folgenden Beispiel einen `meta-data`-Abschnitt hinzufügen:

			<activity android:name="SplashScreenActivity">
			  <meta-data android:name="engagement:notification:overlay" android:value="false"/>
			</activity>

#### <a name="categories"></a> Kategorien

Wenn Sie die bereitgestellten Layouts ändern, verändern Sie das Aussehen Ihrer gesamten Benachrichtigungen. Mithilfe von Kategorien können Sie verschiedene zielgerichtete Layouts (Verhaltensweisen) für Benachrichtigungen definieren. Eine Kategorie kann beim Erstellen einer Reach-Kampagne angegeben werden. Bedenken Sie, dass Sie mithilfe von Kategorien auch Ankündigungen und Umfragen anpassen können (dies wird weiter unten in diesem Dokument beschrieben).

Sie müssen beim Initialisieren der Anwendung einen Aufruf hinzufügen, um einen Kategoriehandler für Ihre Benachrichtigungen zu registrieren.

> [AZURE.IMPORTANT]Bevor Sie fortfahren, finden Sie Informationen zur Warnung zum „android:process“-Attribut „<android-sdk-engagement-process> “ unter „Integrieren von Engagement unter Android“.

Im folgenden Beispiel wird angenommen, dass Sie die vorherige Warnung bestätigt haben und eine Unterklasse von `EngagementApplication` verwenden:

			public class MyApplication extends EngagementApplication
			{
			  @Override
			  protected void onApplicationProcessCreate()
			  {
			    // [...] other init
			    EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
			    reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
			  }
			}

Das `MyNotifier`-Objekt ist die Implementierung des Handlers für die Benachrichtigungskategorie. Es handelt sich entweder um eine Implementierung der `EngagementNotifier`-Schnittstelle oder eine Klasse der Standardimplementierung: `EngagementDefaultNotifier`.

Beachten Sie, dass dieselbe Benachrichtigung, die Sie wie folgt registrieren können, verschiedene Kategorien behandeln kann:

			reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Sie können Ihre Implementierung wie im folgenden Beispiel registrieren, um die Implementierung der Standardkategorie zu ersetzen:

			public class MyApplication extends EngagementApplication
			{
			  @Override
			  protected void onApplicationProcessCreate()
			  {
			    // [...] other init
			    EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
			    reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
			  }
			}

Die in einem Handler verwendete aktuelle Kategorie wird in den meisten Methoden als Parameter übergeben, den Sie in `EngagementDefaultNotifier` außer Kraft setzen können.

Er wird entweder als `String`-Parameter oder indirekt in einem `EngagementReachContent`-Objekt übergeben, das über eine `getCategory()`-Methode verfügt.

Sie können den größten Teil des Erstellungsprozesses für die Benachrichtigung durch erneute Definition der Methoden für `EngagementDefaultNotifier` ändern. Für eine erweiterte Anpassung können Sie die technische Dokumentation und den Quellcode zu Rate ziehen.

##### In-App-Benachrichtigungen

Wenn Sie für eine bestimmte Kategorie alternative Layouts verwenden möchten, können Sie diese wie im folgenden Beispiel implementieren:
			
			public class MyNotifier extends EngagementDefaultNotifier
			{
			  public MyNotifier(Context context)
			  {
			    super(context);
			  }
			
			  @Override
			  protected int getOverlayLayoutId(String category)
			  {
			    return R.layout.my_notification_overlay;
			  }
			
			
			  @Override
			  public Integer getOverlayViewId(String category)
			  {
			    return R.id.my_notification_overlay;
			  }
			
			  @Override
			  public Integer getInAppAreaId(String category)
			  {
			    return R.id.my_notification_area;
			  }
			}

**Beispiel für `my_notification_overlay.xml`:**

			<?xml version="1.0" encoding="utf-8"?>
			<RelativeLayout
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:id="@+id/my_notification_overlay"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent">
			
			  <include layout="@layout/my_notification_area" />
			
			</RelativeLayout>

Wie Sie sehen können, unterscheidet sich der Bezeichner für die Überlagerungsansicht vom Standardbezeichner. Es ist wichtig, dass jedes Layout einen eindeutigen Bezeichner für Überlagerungen verwendet.

**Beispiel für `my_notification_area.xml`:**

			<?xml version="1.0" encoding="utf-8"?>
			<merge
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent">
			
			  <RelativeLayout
			    android:id="@+id/my_notification_area"
			    android:layout_width="fill_parent"
			    android:layout_height="64dp"
			    android:layout_alignParentTop="true"
			    android:background="#B000">
			
			    <LinearLayout
			      android:orientation="horizontal"
			      android:layout_width="fill_parent"
			      android:layout_height="fill_parent"
			      android:gravity="center_vertical">
			
			      <ImageView
			        android:id="@+id/engagement_notification_icon"
			        android:layout_width="48dp"
			        android:layout_height="48dp" />
			
			      <LinearLayout
			        android:id="@+id/engagement_notification_text"
			        android:orientation="vertical"
			        android:layout_width="fill_parent"
			        android:layout_height="fill_parent"
			        android:layout_weight="1"
			        android:gravity="center_vertical">
			
			        <TextView
			          android:id="@+id/engagement_notification_title"
			          android:layout_width="fill_parent"
			          android:layout_height="wrap_content"
			          android:singleLine="true"
			          android:ellipsize="end"
			          android:textAppearance="@android:style/TextAppearance.Medium" />
			
			        <TextView
			          android:id="@+id/engagement_notification_message"
			          android:layout_width="fill_parent"
			          android:layout_height="wrap_content"
			          android:maxLines="2"
			          android:ellipsize="end"
			          android:textAppearance="@android:style/TextAppearance.Small" />
			
			      </LinearLayout>
			
			      <ImageView
			        android:id="@+id/engagement_notification_image"
			        android:layout_width="wrap_content"
			        android:layout_height="fill_parent"
			        android:adjustViewBounds="true" />
			
			      <ImageButton
			        android:id="@+id/engagement_notification_close_area"
			        android:visibility="invisible"
			        android:layout_width="wrap_content"
			        android:layout_height="fill_parent"
			        android:src="@android:drawable/btn_dialog"
			        android:background="#0F00" />
			
			    </LinearLayout>
			
			    <ImageButton
			      android:id="@+id/engagement_notification_close"
			      android:layout_width="wrap_content"
			      android:layout_height="fill_parent"
			      android:layout_alignParentRight="true"
			      android:src="@android:drawable/btn_dialog"
			      android:background="#0F00" />
			
			  </RelativeLayout>
			
			</merge>

Wie Sie sehen können, unterscheidet sich der Bezeichner der Benachrichtigungsbereichsansicht vom Standardbezeichner. Es ist wichtig, dass jedes Layout einen eindeutigen Bezeichner für Benachrichtigungsbereiche verwendet.

In diesem einfachen Beispiel zur Kategorie werden Anwendungsbenachrichtigungen (oder In-App-Benachrichtigungen) am oberen Bildschirmrand angezeigt. Die Standardbezeichner, die im Benachrichtigungsbereich selbst verwendet wurden, haben wir nicht geändert.

Wenn Sie diese ändern möchten, müssen Sie die `EngagementDefaultNotifier.prepareInAppArea`-Methode neu definieren. Es wird empfohlen, sich die technische Dokumentation und den Quellcode von `EngagementNotifier` und `EngagementDefaultNotifier` anzusehen, wenn Sie eine derartige erweiterte Anpassung wünschen.

##### Systembenachrichtigungen

Durch die Erweiterung von `EngagementDefaultNotifier` können Sie `onNotificationPrepared` außer Kraft setzen, um die Benachrichtigung zu ändern, die von der Standardimplementierung vorbereitet wurde.

Zum Beispiel:

			@Override
			protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
			  throws RuntimeException
			{
			  if ("ongoing".equals(content.getCategory()))
			    notification.flags |= Notification.FLAG_ONGOING_EVENT;
			  return true;
			}

In diesem Beispiel wird eine Systembenachrichtigung für einen Inhalt als fortlaufendes Ereignis angezeigt, wenn die „fortlaufende“ Kategorie verwendet wurde.

Wenn Sie das `Notification`-Objekt von Grund auf erstellen möchten, können Sie den Wert `false` an die Methode zurückgeben und `notify` selbst für `NotificationManager` aufrufen. In diesem Fall ist es wichtig, dass Sie ein `contentIntent`, ein `deleteIntent` und den Benachrichtigungsbezeichner behalten, der von `EngagementReachReceiver` verwendet wird.

Hier folgt ein entsprechendes Beispiel für eine derartige Implementierung:

			@Override
			protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
			{
			  /* Required fields */
			  NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
			    .setSmallIcon(notification.icon)              // icon is mandatory
			    .setContentIntent(notification.contentIntent) // keep content intent
			    .setDeleteIntent(notification.deleteIntent);  // keep delete intent
			
			  /* Your customization */
			  // builder.set...
			
			  /* Dismiss option can be managed only after build */
			  Notification myNotification = builder.build();
			  if (!content.isNotificationCloseable())
			    myNotification.flags |= Notification.FLAG_NO_CLEAR;
			
			  /* Notify here instead of super class */
			  NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
			  manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier
			
			  /* Return false, we notify ourselves */
			  return false;
			}

##### Ankündigungen für ausschließliche Benachrichtigung

Die Verwaltung der Ankündigung zum Klicken auf eine Benachrichtigung kann durch Außerkraftsetzung von `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` angepasst werden, indem das vorbereitete `Intent` geändert wird. Durch die Verwendung dieser Methode können Sie die Flags ganz einfach anpassen.

Gehen Sie wie folgt vor, um z. B. das Flag `SINGLE_TOP` hinzuzufügen:
			
			@Override
			protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
			  Intent intent)
			{
			  intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
			  return intent;
			}

Beachten Sie, dass die Systembenachrichtigungen ohne Aktions-URL jetzt die Anwendung für Legacy-Benutzer von Engagement starten, wenn diese sich im Hintergrund befunden hat, daher kann diese Methode mit einer Ankündigung ohne Aktions-URL aufgerufen werden. Dies sollte beim Anpassen des Zwecks berücksichtigt werden.

Sie können auch `EngagementNotifier.executeNotifAnnouncementAction` von Grund auf implementieren.

##### Benachrichtigungslebenszyklus

Bei Verwendung der Standardkategorie werden für das `EngagementReachInteractiveContent`-Objekt einige Lebenszyklusmethoden aufgerufen, um Statistiken bereitzustellen und den Kampagnenstatus zu aktualisieren:

-   Wenn die Benachrichtigung in der Anwendung oder Statusleiste angezeigt wird, wird die `displayNotification`-Methode (die die Statistiken meldet) von `EngagementReachAgent` aufgerufen, wenn `handleNotification` den Wert `true` zurückgibt.
-   Wird die Benachrichtigung geschlossen, wird die Methode `exitNotification` aufgerufen, es werden Statistiken bereitgestellt, und es können weitere Kampagnen verarbeitet werden.
-   Beim Anklicken der Benachrichtigung wird `actionNotification` aufgerufen, die Statistik gemeldet und das zugeordnete Ziel gestartet.

Wenn Ihre Implementierung von `EngagementNotifier` das Standardverhalten umgeht, müssen Sie diese Lebenszyklusmethoden selbst aufrufen. Das folgende Beispiel zeigt einige Fälle, in denen das Standardverhalten umgangen wird:

-   Es erfolgt keine Erweiterung von `EngagementDefaultNotifier`, d. h. Sie haben die Kategorieverarbeitung von Grund auf implementiert.
-   Für die Systembenachrichtigungen haben Sie `onNotificationPrepared` außer Kraft gesetzt und `contentIntent` oder `deleteIntent` im `Notification`-Objekt geändert.
-   Für In-App-Benachrichtigungen haben Sie `prepareInAppArea` außer Kraft gesetzt. Stellen Sie sicher, dass Sie mindestens `actionNotification` zu einem Ihrer Steuerelemente der Benutzeroberfläche zuordnen.

> [AZURE.NOTE]Wenn `handleNotification` eine Ausnahme auslöst, wird der Inhalt gelöscht und `dropContent` aufgerufen. Dies wird in den Statistiken gemeldet und die nächsten Kampagnen können jetzt verarbeitet werden.

### Ankündigungen und Umfragen

#### Layouts

Sie können die Dateien `engagement_text_announcement.xml`, `engagement_web_announcement.xml` und `engagement_poll.xml` ändern, um Textankündigungen, Webankündigungen und Umfragen anzupassen.

Diese Dateien haben zwei allgemeine Layouts für den Titel- und den unteren Bereich gemeinsam. Das Layout für den Titel ist `engagement_content_title.xml` und es verwendet die gleichnamige ziehbare Datei für den Hintergrund. Das Layout für die Aktions- und Beendigungsschaltflächen ist `engagement_button_bar.xml` und es verwendet die gleichnamige ziehbare Datei für den Hintergrund.

In einer Umfrage werden das Fragenlayout und ihre Optionen dynamisch durch mehrfache Verwendung der `engagement_question.xml`-Layoutdatei für die Fragen und der `engagement_choice.xml`-Datei für die Optionen gefüllt.

#### Kategorien

##### Alternative Layouts

Wie Benachrichtigungen können die Kampagnenkategorien dazu verwendet werden, alternative Layouts für Ankündigungen und Umfragen bereitzustellen.

Zum Erstellen einer Kategorie für eine Textankündigung können Sie z. B. `EngagementTextAnnouncementActivity` erweitern und darauf in der Datei `AndroidManifest.xml` verweisen:

			<activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.ANNOUNCEMENT"/>
			    <category android:name="my_category" />
			    <data android:mimeType="text/plain" />
			  </intent-filter>
			</activity>

Beachten Sie, dass die Kategorie im Zielfilter dazu verwendet wird, den Unterschied zur Standardankündigungsaktivität auszumachen.

Das Reach-SDK verwendet das Zielsystem, um die richtige Aktivität für eine bestimmte Kategorie aufzulösen, wobei auf die Standardkategorie ausgewichen wird, wenn bei der Auflösung ein Fehler auftritt.

Dann müssen Sie `MyCustomTextAnnouncementActivity` implementieren, wenn Sie nur das Layout ändern möchten (aber dieselben Ansichtsbezeichner behalten). Sie müssen die Klasse einfach wie im folgenden Beispiel definieren:

			public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
			{
			  @Override
			  protected String getLayoutName()
			  {
			    return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
			  }
			}

Ersetzen Sie zum Ersetzen der Standardkategorie von Textankündigungen einfach `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` durch Ihre Implementierung.

Webankündigungen und Umfragen können auf ähnliche Weise angepasst werden.

Für Webankündigungen können Sie `EngagementWebAnnouncementActivity` erweitern und Ihre Aktivität in der Datei `AndroidManifest.xml` wie im folgenden Beispiel deklarieren:

			<activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.ANNOUNCEMENT"/>
			    <category android:name="my_category" />
			    <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
			  </intent-filter>
			</activity>

Für Umfragen können Sie `EngagementPollActivity` erweitern und die Datei `AndroidManifest.xml` wie im folgenden Beispiel deklarieren:

			<activity android:name="com.your_company.MyCustomPollActivity">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.POLL"/>
			    <category android:name="my_category" />
			  </intent-filter>
			</activity>
			
##### Implementierung von Grund auf

Sie können Kategorien für Ihre Ankündigungsaktivitäten (und Umfrageaktivitäten) implementieren, ohne eine der `Engagement*Activity`-Klassen zu erweitern, die vom Reach-SDK bereitgestellt werden. Dies ist zum Beispiel hilfreich, wenn Sie ein Layout definieren möchten, dass nicht dieselben Ansichten wie die Standardlayouts verwendet.

Wie bei der erweiterten Benachrichtigungsanpassung wird empfohlen, sich den Quellcode der Standardimplementierung anzusehen.

Hier folgen einige Punkte, die es zu beachten gilt: Reach startet die Aktivität mit einem bestimmten Ziel (entsprechend dem Zielfilter) sowie einem zusätzlichen Parameter, bei dem es sich um den Inhaltsbezeichner handelt.

Sie können wie folgt vorgehen, um das Inhaltsobjekt abzurufen, dass die Felder enthält, die Sie beim Erstellen der Kampagne auf der Website angegeben haben:

			public class MyCustomTextAnnouncement extends EngagementActivity
			{
			  private EngagementAnnouncement mContent;
			
			  @Override
			  protected void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			
			    /* Get content */
			    mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
			    if (mContent == null)
			    {
			      /* If problem with content, exit */
			      finish();
			      return;
			    }
			
			    setContentView(R.layout.my_text_announcement);
			
			    /* Configure views by querying fields on mContent */
			    // ...
			  }
			}

Für die Statistiken sollten Sie melden, dass der Inhalt im `onResume`-Ereignis angezeigt wird:
			
			@Override
			protected void onResume()
			{
			 /* Mark the content displayed */
			 mContent.displayContent(this);
			 super.onResume();
			}

Vergessen Sie anschließend nicht, entweder `actionContent(this)` oder `exitContent(this)` für das Inhaltsobjekt aufzurufen, bevor die Aktivität in den Hintergrund wechselt.

Wenn Sie weder `actionContent` noch m`exitContent` Aufrufen, werden keine Statistiken gesendet (d. h. keine Analysen zur Kampagne) und noch wichtiger ist, dass die nächsten Kampagnen nicht benachrichtigt werden, bis der Anwendungsprozess neu gestartet wurde.

Die Ausrichtung oder andere Konfigurationsänderungen können die Ermittlung erschweren, ob die Aktivität in den Hintergrund wechselt. Die Standardimplementierung stellt sicher, dass der Inhalt beim Beenden gemeldet wird, wenn der Benutzer die Aktivität verlässt (durch Drücken von `HOME` oder `BACK`), jedoch nicht, wenn sich die Ausrichtung ändert.

Hier folgt der interessante Teil der Implementierung:

			@Override
			protected void onUserLeaveHint()
			{
			  finish();
			}
			
			@Override
			protected void onPause()
			{
			  if (isFinishing() && mContent != null)
			  {
			    /*
			     * Exit content on exit, this is has no effect if another process method has already been
			     * called so we don't have to check anything here.
			     */
			    mContent.exitContent(this);
			  }
			  super.onPause();
			}

Wenn Sie aufgerufen `actionContent(this)` und dann die Aktivität beendet haben, kann `exitContent(this)` ohne Auswirkungen sicher aufgerufen werden.

## Test

Überprüfen Sie jetzt Ihre Integration, indem Sie den Abschnitt „Testen der Engagement-Integration unter Android“ lesen.

[hier]: http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]: http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]: https://developer.amazon.com/sdk/adm.html

<!--HONumber=54-->