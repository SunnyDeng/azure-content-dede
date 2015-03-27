<properties 
	pageTitle="Integration des Android-SDKs für Azure Mobile Engagement" 
	description="Neueste Updates und Verfahren für das Android-SDK für Azure Mobile Engagement"
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

# So integrieren Sie GCM in Mobile Engagement

> [AZURE.IMPORTANT] Bevor Sie die in diesem Leitfaden beschriebenen Schritte ausführen, müssen Sie die im Dokument "So integrieren Sie Engagement auf Android" beschriebenen Verfahren ausführen.
>
> Das vorliegende Dokument ist nur hilfreich, wenn Sie das Reach-Modul für die jederzeitige Kampagnenunterstützung integriert haben. Lesen Sie zunächst das Dokument "So integrieren Sie Engagement Reach auf Android", um Informationen zum Integrieren von Reach-Kampagnen zu erhalten.

## Einführung

Durch die Integration von GCM kann Ihre Anwendung selbst dann Pushnachrichten empfangen, wenn sie nicht ausgeführt wird.

Über GCM werden nicht tatsächlich Kampagnendaten gesendet, es handelt sich lediglich um ein Hintergrundsignal, mit dem die Anwendung angewiesen wird, die Engagement-Pushnachricht abzurufen. Wenn die Anwendung beim Empfang einer GCM-Pushnachricht nicht ausgeführt wird, wird eine Verbindung mit den Engagement-Servern zum Abrufen der Pushnachricht ausgelöst. Die Engagement-Verbindung bleibt für etwa eine Minute aktiv, falls der Benutzer die Anwendung als Reaktion auf die Pushnachricht startet.

Engagement verwendet ausschließlich [Send-to-Sync]-Nachrichten mit dem `engagement.tickle`-Ausblendschlüssel.

> [AZURE.IMPORTANT] Über GCM können nur Geräte aktiviert werden, die über Android 2.2 oder höher verfügen, auf denen Google Play installiert ist und die über eine Google-Hintergrundverbindung verfügen. Dieser Code kann jedoch ohne Sicherheitsbedenken auch auf älteren Versionen des Android-SDK und auf Geräten integriert werden, die GCM nicht unterstützen (es werden nur "Intents" verwendet). Wenn die Anwendung nicht per GCM aktiviert werden kann, wird die Engagement-Benachrichtigung beim nächsten Anwendungsstart empfangen.


> [AZURE.WARNING] Wenn Ihr eigener Clientcode C2DM-Registrierungs-IDs nutzt und das Engagement-SDK zur Verwendung von GCM konfiguriert ist, tritt ein Konflikt bei den Registrierungs-IDs auf. Verwenden Sie GCM nur dann in Engagement, wenn Ihr eigener Code nicht C2DM verwendet.

## Anmelden bei GCM und Aktivieren des GCM-Diensts

Sofern nicht bereits geschehen, müssen Sie den GCM-Dienst über Ihr Google-Konto aktivieren.

Zum Zeitpunkt der Erstellung dieses Dokuments (5. Februar 2014) stand hierzu das folgende Verfahren zur Verfügung: [<http://developer.android.com/guide/google/gcm/gs.html>].

Führen Sie dieses Verfahren nur zum Aktivieren von GCM für Ihr Konto aus. Wenn Sie beim Abschnitt **Obtaining an API Key** (Abrufen eines API-Schlüssels) angelangt sind, lesen Sie nicht weiter, und führen Sie keine weiteren der dort beschriebenen Schritte aus. Kehren Sie stattdessen zu dieser Seite zurück.

Im Verfahren wird erläutert, dass der Wert für **Project Number** (Projektnummer) als **GCM sender ID** (GCM-Sende-ID) verwendet wird, Sie benötigen diesen Wert später in diesem Verfahren.

> [AZURE.IMPORTANT] **Project Number** (Projektnummer) darf nicht verwechselt werden mit **Project ID** (Projekt-ID). Die Projekt-ID kann abweichen (es handelt sich um einen Namen für neue Projekte). Sie benötigen zur Integration im Engagement-SDK den Wert für **Project Number** (Projektnummer). Dieser wird im Menü **Overview** (Übersicht) in der [Google Developers Console] angezeigt.

## SDK-Integration

### Verwalten von Geräteregistrierungen

Jedes Gerät muss einen Registrierungsbefehl an die Google-Server senden, anderenfalls sind sie nicht erreichbar.

Ein Gerät die Registrierung zum Empfang von GCM-Benachrichtigungen auch aufheben (die Registrierung für das Gerät wird automatisch aufgehoben, wenn die Anwendung deinstalliert wird).

Bei Verwendung der [GCM-Clientbibliothek] kann "android-sdk-gcm-receive" direkt gelesen werden.

Wenn Sie nicht bereits selbst ein Registrierungs-Intent gesendet haben, kann Engagement das Gerät automatisch für Sie registrieren.

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

## Erteilen von Engagement-Zugriff auf einen Server-API-Schlüssel

Sofern nicht bereits geschehen, erstellen Sie einen **Server API-Schlüssel** in der [Google Developers Console].

Der Serverschlüssel **DARF KEINE IP-Beschränkungen aufweisen**.

Zum Zeitpunkt der Erstellung dieses Dokuments (5. Februar 2014) stand das folgende Verfahren zur Verfügung:

-   Öffnen Sie die [Google Developers Console].
-   Wählen Sie dasselbe Projekt aus wie in früheren Schritten des Verfahrens (das Projekt mit dem Wert für **Project Number** (Projektnummer), den Sie in `AndroidManifest.xml` integriert haben).
-   Wechseln Sie zu "APIs & auth -\> Credentials" (APIs & Authentifizierung -\> Anmeldeinformationen), und klicken Sie im Abschnitt "Public API access" (Öffentlicher API-Zugriff) auf "CREATE NEW KEY" (Neuen Schlüssel erstellen).
-   Wählen Sie "Server key" (Serverschlüssel).
-   Lassen Sie den nächsten Bildschirm leer **(no IP restriction)** (keine IP-Einschränkung), und klicken Sie dann auf "Create" (Erstellen).
-   Kopieren Sie den generierten **API-Schlüssel**.
-   Wechseln Sie zu $/\#application/YOUR\_ENGAGEMENT\_APPID/native-push.
-   Bearbeiten Sie im GCM-Abschnitt den API-Schlüssel mithilfe des Schlüssels, den Sie soeben generiert und kopiert haben.

Sie können jetzt beim Erstellen von Reach-Ankündigungen und -Umfragen die Option "Any Time" (Jederzeit) auswählen.

> [AZURE.IMPORTANT] Engagement benötigt tatsächlich einen **Serverschlüssel**, ein Android-Schlüssel kann von den Engagement-Servern nicht verwendet werden.

## Test

Überprüfen Sie jetzt Ihre Integration, mithilfe der Informationen im Abschnitt "So testen Sie die Engagement-Integration auf Android".


[Send-to-Sync]:http://developer.android.com/google/gcm/adv.html#collapsible
[<http://developer.android.com/guide/google/gcm/gs.html>]:http://developer.android.com/guide/google/gcm/gs.html
[Google Developers Console]:https://cloud.google.com/console
[GCM-Clientbibliothek]:http://developer.android.com/guide/google/gcm/gs.html#libs
[Google Developers Console]:https://cloud.google.com/console


<!--HONumber=47-->
