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
	ms.date="03/10/2016"
	ms.author="piyushjo" />

#Integrieren von GCM mit Mobile Engagement

> [AZURE.IMPORTANT] Bevor Sie dieser Anleitung folgen, müssen Sie das unter „Integrieren von Mobile Engagement unter Android“ beschriebene Integrationsverfahren befolgen.
>
> Dieses Dokument ist nur relevant, wenn Sie das Reach-Modul bereits integriert haben und Pushübertragungen an Google Play-Geräte planen. Lesen Sie zunächst das Dokument "So integrieren Sie Engagement Reach auf Android", um Informationen zum Integrieren von Reach-Kampagnen zu erhalten.

##Einführung

Durch die Integration von GCM kann Ihre Anwendung Pushnachrichten empfangen.

GCM-Nutzlasten, die mithilfe von Push an das SDK übertragen werden, enthalten immer den `azme`-Schlüssel im Datenobjekt. Wenn Sie GCM in Ihrer Anwendung also zu einem anderen Zweck verwenden, können Sie Push-Vorgänge basierend auf diesem Schlüssel filtern.

> [AZURE.IMPORTANT] Über GCM können nur Geräte Pushnachrichten empfangen, die über Android 2.2 oder höher verfügen, auf denen Google Play installiert ist und die über eine Google-Hintergrundverbindung verfügen. Dieser Code kann jedoch ohne Sicherheitsbedenken auch auf Geräten integriert werden, die GCM nicht unterstützen (es werden nur „Intents“ verwendet).

##Erstellen eines Google Cloud Messaging-Projekts mit API-Schlüssel

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

> [AZURE.IMPORTANT] **Project Number** (Projektnummer) darf nicht mit **Project ID** (Projekt-ID) verwechselt werden.

##SDK-Integration

### Verwalten von Geräteregistrierungen

Jedes Gerät muss einen Registrierungsbefehl an die Google-Server senden, anderenfalls sind sie nicht erreichbar.

Ein Gerät die Registrierung zum Empfang von GCM-Benachrichtigungen auch aufheben (die Registrierung für das Gerät wird automatisch aufgehoben, wenn die Anwendung deinstalliert wird).

Wenn Sie das [Google Play SDK] nicht verwenden oder nicht bereits selbst eine Registrierungsanforderung gesendet haben, kann Engagement das Gerät automatisch für Sie registrieren.

Fügen Sie zur Aktivierung in der `AndroidManifest.xml`-Datei im Tag `<application/>` den folgenden Code ein:

			<!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
			<meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### Übermitteln der Registrierungs-ID an den Engagement-Pushdienst, um Benachrichtigungen zu erhalten

Um die Registrierungs-ID des Geräts an den Engagement-Pushdienst zu übermitteln und Benachrichtigungen zu empfangen, fügen Sie in der `AndroidManifest.xml`-Datei im Tag `<application/>` den folgenden Code ein (selbst, wenn Sie Geräteregistrierungen selbst verwalten):

			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>

			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
			    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
			    <category android:name="<your_package_name>" />
			  </intent-filter>
			</receiver>

Stellen Sie sicher, dass Sie die folgenden Berechtigungen in der Datei `AndroidManifest.xml` festgelegt haben (vor dem Tag `</application>`).

			<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
			<uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
			<permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##Gewähren des Zugriffs für Mobile Engagement auf den GCM-API-Schlüssel

Befolgen Sie [diesen Leitfaden](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key), um Mobile Engagement Zugriff auf den GCM-API-Schlüssel zu gewähren.

[Google Play SDK]: https://developers.google.com/cloud-messaging/android/start

<!---HONumber=AcomDC_0316_2016-->