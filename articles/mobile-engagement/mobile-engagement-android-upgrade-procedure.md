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
	ms.date="02/29/2016" 
	ms.author="piyushjo" />


#Upgrade-Verfahren

Wenn Sie bereits eine ältere Version unseres SDK in Ihrer Anwendung integriert haben, müssen Sie die folgenden Punkte beim Aktualisieren des SDK beachten.

Möglicherweise müssen Sie mehrere Verfahren befolgen, wenn Sie mehrere Versionen des SDK verpasst haben. Wenn Sie beispielsweise von 1.4.0 zu 1.6.0 migrieren, müssen Sie zunächst den Vorgang „von 1.4.0 zu 1.5.0“ ausführen und anschließend den Vorgang „1.5.0 zu 1.6.0“.

Unabhängig von der Version, für die Sie das Upgrade ausführen, müssen Sie die `mobile-engagement-VERSION.jar` durch die neue Version ersetzen.

##Von 4.0.0 zu 4.1.0

Das SDK behandelt nun ein neues Berechtigungsmodell aus Android M.

Wenn Sie Standortfunktionen oder Übersichtsbenachrichtigungen verwenden, lesen Sie bitte [diesen Abschnitt](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Zusätzlich zum neuen Berechtigungsmodell wird nun das Konfigurieren von Standortfunktionen zur Laufzeit unterstützt. Die Kompatibilität mit Manifestparametern für den Standort ist zwar noch immer gegeben, allerdings jetzt veraltet. Um die Laufzeitkonfiguration zu verwenden, entfernen Sie die folgenden Abschnitte aus Ihrer Datei ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

und lesen Sie [dieses aktualisierte Verfahren](mobile-engagement-android-integrate-engagement.md#location-reporting), um stattdessen die Laufzeitkonfiguration zu verwenden.

##Von 3.0.0 zu 4.0.0

### Systemeigener Push

Ein systemeigener Push (GCM/ADM) wird jetzt auch für In-App-Benachrichtigungen verwendet, Sie müssen daher die Anmeldeinformationen für den systemeigenen Push für jeden Push-Kampagnentyp konfigurieren.

Wenn Sie die Schritte noch nicht ausgeführt haben, führen Sie [diese Anweisungen](mobile-engagement-android-integrate-engagement-reach.md#native-push) aus.

### AndroidManifest.xml

Die Reach-Integration wurde in ``AndroidManifest.xml`` geändert.

Ersetzen:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
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

nach

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Möglicherweise wird jetzt ein Ladebildschirm angezeigt, wenn Sie auf eine Ankündigung (mit Text/Webinhalten) oder eine Umfrage klicken. Sie müssen dies hinzufügen, damit diese Kampagnen in 4.0.0 verwendet werden können:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### Ressourcen

Betten Sie die neue `res/layout/engagement_loading.xml`-Datei in Ihr Projekt ein.

##Von 2.4.0 zu 3.0.0

Im Folgenden wird beschrieben, wie Sie die Migration einer SDK-Integration vom Capptain-Dienst, der von Capptain-SAS angeboten wird, in eine App von Azure Mobile Engagement durchführen. Wenn Sie von einer früheren Version migrieren, sehen Sie auf der Capptain-Website nach, wie eine Migration auf Version 2.4.0 durchgeführt wird. Führen Sie anschließend das folgende Verfahren aus.

>[AZURE.IMPORTANT] Capptain und Mobile Engagement sind nicht dieselben Dienste, und die unten beschriebene Vorgehensweise hebt nur hervor, wie die Migration der Clientapp durchzuführen ist. Bei der Migration des SDK in die App werden Ihre Daten NICHT von den Capptain-Servern zu den Mobile Engagement-Servern migriert.

### JAR-Datei

Ersetzen Sie `capptain.jar`durch `mobile-engagement-VERSION.jar` (im Ordner `libs`).

### Ressourcendateien

Jede von uns bereitgestellte Ressourcendatei (mit Präfix `capptain_`) muss durch die neuen Dateien ersetzt werden (Präfix `engagement_`).

Wenn Sie diese Dateien angepasst haben, müssen Sie Ihre Anpassungen auf die neuen Dateien erneut anwenden. Zudem müssen **alle Bezeichner in den Ressourcendateien umbenannt werden**.

### Anwendungs-ID

Engagement verwendet jetzt eine Verbindungszeichenfolge zum Konfigurieren der SDK-Bezeichner wie dem Anwendungsbezeichner.

Sie müssen die `EngagementAgent.init`-Methode in Ihrer Startprogrammaktivität wie folgt verwenden:

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

Die Verbindungszeichenfolge für die Anwendung wird im Azure-Portal angezeigt.

Entfernen Sie alle Aufrufe von `CapptainAgent.configure`, da diese Methode von `EngagementAgent.init` ersetzt wird.

Die `appId` kann nicht länger mithilfe von `AndroidManifest.xml` konfiguriert werden.

Entfernen Sie diesen Abschnitt aus der Datei `AndroidManifest.xml`, wenn diese vorhanden ist:

			<meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### Java-API

Jeder Aufruf einer beliebigen Java-Klasse des SDKs muss umbenannt werden, z. B. muss `CapptainAgent.getInstance(this)` in `EngagementAgent.getInstance(this)` sowie `extends CapptainActivity` in `extends EngagementActivity` umbenannt werden usw.

Wenn die Integration mit den Standardeinstellungsdateien des Agent erfolgt ist, wird jetzt der Standarddateiname `engagement.agent` verwendet, während der Schlüssel `engagement:agent` lautet.

Für die Erstellung von Webankündigungen wird jetzt die JavaScript-Bindung `engagementReachContent` verwendet.

### AndroidManifest.xml

Hier wurden eine Reihe von Änderungen vorgenommen. Der Dienst wird nicht mehr freigegeben und viele Empfänger können nicht länger exportiert werden.

Die Dienstdeklaration wurde vereinfacht, der Zielfilter und alle darin enthaltenen Metadaten entfernt sowie `exportable=false` hinzugefügt.

Es ist eine umfassende Umbenennung zur Verwendung von Engagement erfolgt.

Es sieht jetzt wie folgt aus:

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

Zur Aktivierung von Testprotokollen wurden die Metadaten jetzt zum Anwendungstag verschoben und umbenannt:

			<application>
			
			  <meta-data android:name="engagement:log:test" android:value="true" />
			
			  <service/>
			
			</application>

Alle anderen Metadaten wurden umbenannt. Hier ist die vollständige Liste (benennen Sie nur die von Ihnen verwendeten um):

			<meta-data
			  android:name="engagement:reportCrash"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:sessionTimeout"
			  android:value="10000"/>
			<meta-data
			  android:name="engagement:burstThreshold"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:connection:delay"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:locationReport:lazyArea"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:background"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:fine"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:agent:settings:name"
			  android:value="engagement.agent"/>
			<meta-data
			  android:name="engagement:agent:settings:mode"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:gcm:sender"
			  android:value="<YOUR_PROJECT_NUMBER>\n"/>
			<meta-data
			  android:name="engagement:adm:register"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:reach:notification:icon"
			  android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
			
			<activity android:name="SomeActivityWithoutReachOverlay">
			  <meta-data
			    android:name="engagement:notification:overlay"
			    android:value="false"/>
			</activity>

Google Play und die SmartAd-Nachverfolgung wurden aus dem SDK entfernt. Sie müssen dies einfach ohne Ersatz entfernen:

			<meta-data 
				android:name="capptain:track:installReferrerForwardList"
				android:value="com.class1,com.class2"/>
			<meta-data
				android:name="capptain:track:adservers"
				android:value="smartad" />

Die Reach-Aktivitäten sind jetzt wie folgt deklariert:

			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/plain"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/html"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			  </intent-filter>
			</activity>
			
Wenn Sie über benutzerdefinierte Reach-Aktivitäten verfügen, müssen Sie lediglich die Zielaktionen in `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` oder `com.microsoft.azure.engagement.reach.intent.action.POLL` ändern.

Die Übertragungsempfänger wurden umbenannt sowie `exported=false` hinzugefügt. Hier folgt die vollständige Liste der Empfänger mit den neuen Spezifikationen (benennen Sie nur die von Ihnen verwendeten um):

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
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
			  android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
			    <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
			  android:permission="com.amazon.device.messaging.permission.SEND">
			  <intent-filter>
			    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
			    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
			  </intent-filter>
			</receiver>

Der Nachverfolgungsempfänger wurde entfernt, daher müssen Sie diesen Abschnitt entfernen:

		  <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
		    <intent-filter>
		      <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
		      <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
		    </intent-filter>
		  </receiver>

Beachten Sie, dass die Deklaration Ihrer Implementierung des Übertragungsempfängers **EngagementMessageReceiver** in der Datei `AndroidManifest.xml` geändert wurde. Das liegt daran, dass die API zum Senden und Entfernen beliebiger XMPP-Nachrichten zu oder aus beliebigen XMPP-Entitäten und die API zum Senden und Empfangen von Nachrichten zwischen Geräten entfernt wurden. Daher müssen Sie auch die folgenden Rückruffunktionen aus Ihrer **EngagementMessageReceiver**-Implementierung entfernen:

			protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

und

			protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

löschen Sie dann alle Aufrufe von **EngagementAgent** für:

			sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

und

			sendXMPPMessage(android.os.Bundle msg)

### Proguard

Die Umbenennung kann sich auf die Proguard-Konfiguration auswirken. Die Regeln sehen jetzt wie folgt aus:

			-dontwarn android.**
			-keep class android.support.v4.** { *; }
			
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			  <methods>;
			}
 

<!---HONumber=AcomDC_0302_2016-->