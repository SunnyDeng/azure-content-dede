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
	ms.date="05/04/2015" 
	ms.author="kapiteir" />


#Android SDK für Azure Mobile Engagement

Beginnen Sie hier, um alle Details zum Integrieren von Azure Mobile Engagement in eine Android-App zu erhalten. Wenn Sie es zunächst nur probieren möchten, stellen Sie sicher, dass Sie das [15-Minuten-Lernprogramm](mobile-engagement-android-get-started.md) durchführen

Klicken Sie, um den [SDK-Inhalt](mobile-engagement-android-sdk-content.md) anzuzeigen.

##Integrationsverfahren
1. Beginnen Sie hier: [Integrieren von Mobile Engagement in Ihre Android-App](mobile-engagement-android-integrate-engagement.md)

2. Für Benachrichtigungen: [Integration von Reach (Benachrichtigungen) in Ihre Android-App](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [Integrieren von GCM mit Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [Integrieren von ADM mit Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Tag-Plan-Implementierung: [Verwenden der erweiterten Mobile Engagement-Tagging-API in Ihrer Android-App](mobile-engagement-android-use-engagement-api.md)


##Versionshinweise

###4.0.0 (06.07.2015)

-   Interne Protokolländerungen zur Steigerung der Zuverlässigkeit von Analysen und Push
-   Ein systemeigener Push (GCM/ADM) wird jetzt auch für In-App-Benachrichtigungen verwendet, Sie müssen daher die Anmeldeinformationen für den systemeigenen Push für jeden Push-Kampagnentyp konfigurieren.
-   Korrektur für Überblick-Benachrichtigung: Es wurde nach dem Push nur 10 angezeigt.
-   Korrektur für Klicken auf einen Link in einer Web-Ankündigung mit einer Standardaktions-URL.
-   Korrektur für seltenen Absturz im Zusammenhang mit der lokalen Speicherverwaltung.
-   Korrektur der Zeichenfolgenverwaltung für die dynamische Konfiguration.
-   Aktualisierung des Endbenutzer-Lizenzvertrags.

Frühere Versionen finden Sie unter [Vollständige Versionshinweise](mobile-engagement-android-release-notes.md)

##Upgrade-Verfahren

Wenn Sie bereits eine ältere Version unseres SDK in Ihrer Anwendung integriert haben, müssen Sie die folgenden Punkte beim Aktualisieren des SDK beachten.

Möglicherweise müssen Sie mehrere Verfahren ausführen, wenn Sie mehrere SDK-Versionen übersprungen haben. Siehe [Upgradeverfahren](mobile-engagement-android-upgrade-procedure.md) für vollständige Informationen Wenn Sie beispielsweise von 1.4.0 zu 1.6.0 migrieren, müssen Sie zunächst den Vorgang „von 1.4.0 zu 1.5.0“ ausführen und anschließend den Vorgang „1.5.0 zu 1.6.0“.

Unabhängig von der Version, für die Sie das Upgrade ausführen, müssen Sie die `mobile-engagement-VERSION.jar` durch die neue Version ersetzen.

###Von 3.0.0 zu 4.0.0

#### Systemeigener Push

Ein systemeigener Push (GCM/ADM) wird jetzt auch für In-App-Benachrichtigungen verwendet, Sie müssen daher die Anmeldeinformationen für den systemeigenen Push für jeden Push-Kampagnentyp konfigurieren.

Wenn Sie die Schritte noch nicht ausgeführt haben, führen Sie [diese Anweisungen](mobile-engagement-android-integrate-engagement-reach.md#native-push) aus.

#### AndroidManifest.xml

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

#### Ressourcen

Betten Sie die neue `res/layout/engagement_loading.xml`-Datei in Ihr Projekt ein.

<!---HONumber=July15_HO4-->