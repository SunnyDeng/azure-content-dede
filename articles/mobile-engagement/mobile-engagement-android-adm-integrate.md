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


#So integrieren Sie ADM in Engagement

> [AZURE.IMPORTANT]Bevor Sie dieser Anleitung folgen, müssen Sie das unter „Integrieren von Mobile Engagement unter Android“ beschriebene Integrationsverfahren befolgen.
>
> Das vorliegende Dokument ist nur hilfreich, wenn Sie das Reach-Modul für die jederzeitige Kampagnenunterstützung integriert haben. Lesen Sie zunächst das Dokument "So integrieren Sie Engagement Reach auf Android", um Informationen zum Integrieren von Reach-Kampagnen zu erhalten.

##Einführung

Durch die Integration von ADM kann Ihre Anwendung selbst dann Pushnachrichten empfangen, wenn sie nicht ausgeführt wird.

Über ADM werden nicht tatsächlich Kampagnendaten gesendet, es handelt sich lediglich um ein Hintergrundsignal, mit dem die Anwendung angewiesen wird, die Engagement-Pushnachricht abzurufen. Wenn die Anwendung beim Empfang einer ADM-Pushnachricht nicht ausgeführt wird, wird eine Verbindung mit den Engagement-Servern zum Abrufen der Pushnachricht ausgelöst. Die Engagement-Verbindung bleibt für etwa eine Minute aktiv, falls der Benutzer die Anwendung als Reaktion auf die Pushnachricht startet.

> [AZURE.IMPORTANT]Von Amazon Device Messaging werden nur Amazon Kindle-Geräte mit Android 4.0.3 oder höher unterstützt, dieser Code kann jedoch ohne Sicherheitsbedenken auf anderen Geräten integriert werden. Wenn die Anwendung nicht per ADM aktiviert werden kann, wird die Engagement-Benachrichtigung beim nächsten Anwendungsstart empfangen.

##Anmelden bei ADM

Sofern nicht bereits geschehen, müssen Sie ADM über Ihr Amazon-Konto aktivieren.

Detaillierte Informationen zum Vorgehen finden Sie hier: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Nach Abschluss des Verfahrens erhalten Sie:

-   OAuth-Anmeldeinformationen(eine Client-ID und einen Clientschlüssel) für Engagement, um Pushnachrichten an Ihre Geräte zu senden.
-   Einen API-Schlüsse, der in Ihre Anwendung integriert werden muss.

##SDK-Integration

### Verwalten von Geräteregistrierungen

Jedes Gerät muss einen Registrierungsbefehl an die ADM-Server senden, anderenfalls sind sie nicht erreichbar.

Wenn Sie bereits die [ADM-Clientbibliothek] verwenden und eine [ADM-Integration] durchgeführt haben, können Sie direkt zu "android-sdk-adm-receive" wechseln.

Wenn Sie ADM noch nicht integriert haben, bietet Engagement eine einfache Methode zur Aktivierung in Ihrer Anwendung:

Bearbeiten Sie Ihre `AndroidManifest.xml`-Datei:

-   Fügen Sie den Amazon-Namespace hinzu, die Datei sollte folgendermaßen beginnen:

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   Fügen Sie im `<application/>`-Tag diesen Abschnitt hinzu:

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>
		
		<meta-data android:name="engagement:adm:register" android:value="true" />

-   Nach dem Hinzufügen des Amazon-Tags erhalten Sie möglicherweise einen Buildfehler, wenn der Buildzielname des aktuellen Projekts unter Android 2.1 liegt. Sie müssen als Buildzielname **Android 2.1+** verwenden (keine Sorge, Sie können `minSdkVersion` weiterhin auf 4 festlegen).
-   Integrieren Sie den ADM-API-Schlüssel als Medienobjekt, indem Sie [dieses Verfahren] ausführen.

Folgen Sie anschließend den Anweisungen in den nächsten Abschnitten.

### Übermitteln der Registrierungs-ID an den Engagement-Pushdienst, um Benachrichtigungen zu erhalten

Um die Registrierungs-ID des Geräts an den Engagement-Pushdienst zu übermitteln und Benachrichtigungen zu empfangen, fügen Sie in der `AndroidManifest.xml`-Datei im Tag `<application/>` den folgenden Code ein (selbst, wenn Sie ADM ohne Engagement verwenden):

		<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
		  android:exported="false">
		  <intent-filter>
		    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
		  </intent-filter>
		</receiver>
		
		 <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
		   android:permission="com.amazon.device.messaging.permission.SEND">
		  <intent-filter>
		    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
		    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
		    <category android:name="<your_package_name>"/>
		  </intent-filter>
		</receiver>   

Stellen Sie sicher, dass Sie die folgenden Berechtigungen in der Datei `AndroidManifest.xml` festgelegt haben (vor dem Tag `</application>`).

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Gewähren von OAuth-Anmeldeinformationen für Engagement

Übermitteln Sie Ihre OAuth-Anmeldeinformationen (Client-ID und Clientschlüssel) an $/#application/YOUR_APPID/native-push.

Sie können jetzt beim Erstellen von Reach-Ankündigungen und -Umfragen die Option "Any Time "(Jederzeit) auswählen.


[<https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html
[ADM-Clientbibliothek]: https://developer.amazon.com/sdk/adm/setup.html
[ADM-Integration]: https://developer.amazon.com/sdk/adm/integrating-app.html
[dieses Verfahren]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
 

<!---HONumber=July15_HO4-->