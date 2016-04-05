<properties
	pageTitle="Erste Schritte mit Azure Mobile Engagement für Xamarin.Android"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen für Xamarin.Android-Apps."
	services="mobile-engagement"
	documentationCenter="xamarin"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Erste Schritte mit Azure Mobile Engagement für Xamarin.Android-Apps

[AZURE.INCLUDE [Umschalter für das Hero-Tutorial](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement die Nutzung Ihrer App verstehen können und wie Sie Pushbenachrichtigungen an segmentierte Benutzer einer Xamarin.Android-Anwendung senden. In diesem Tutorial wird ein einfaches Übertragungsszenario mit Mobile Engagement dargestellt. Darin erstellen Sie eine leere Xamarin.Android-App, die einfache Daten erfasst und Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Xamarin Studio](http://xamarin.com/studio) [Sie können auch Visual Studio mit Xamarin-Erweiterung verwenden, in den Anweisungen in diesem Tutorial wird jedoch Xamarin Studio-eingesetzt.]
+ [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).

##<a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre App

[AZURE.INCLUDE [Erstellen einer Mobile Engagement-App im Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine "einfache Integration" dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung.

Wir erstellen eine einfache App mit Xamarin Studio, um die Integration zu veranschaulichen.

###Erstellen eines neuen Xamarin.Android-Projekts

1. Starten Sie **Xamarin Studio** und klicken Sie dann auf **Datei** -> **Neu** -> **Projektmappe**. 

    ![][1]

2. Wählen Sie **Android-App**, und stellen Sie sicher, dass als Sprache **C#** ausgewählt ist. Klicken Sie auf **Weiter**.

    ![][2]

3. Geben Sie **App-Name** und **Organisations-ID** ein. Vergewissern Sie sich, dass **Google Play Services** aktiviert ist, und klicken Sie dann auf **Weiter**.

    ![][3]
	
4. Aktualisieren Sie ggf. **Projektname**, **Projektmappenname** und **Speicherort**, und klicken Sie auf **Erstellen**.

    ![][4]
 
Xamarin Studio erstellt die App, in die wir Mobile Engagement integrieren.

###Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End.

1. Klicken Sie im Projektmappenfenster mit der rechten Maustaste auf den Ordner **Pakete**, und wählen Sie **Pakete hinzufügen...**.

    ![][5]

2. Suchen Sie nach dem **Microsoft Azure Mobile Engagement Xamarin SDK**, und fügen Sie es zur Projektmappe hinzu.

    ![][6]
   
3. Öffnen Sie **MainActivity.cs**, und fügen Sie die folgenden using-Anweisungen hinzu:

		using Microsoft.Azure.Engagement;
		using Microsoft.Azure.Engagement.Activity;

4. Fügen Sie in der `OnCreate`-Methode Folgendes hinzu, um die Verbindung mit dem Mobile Engagement-Back-End zu initialisieren. Fügen Sie auch **ConnectionString** hinzu.

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
		EngagementAgent.Init(engagementConfiguration);

###Hinzufügen von Berechtigungen und einer Dienstdeklaration

1. Öffnen Sie im Eigenschaftenordner die Datei **Manifest.xml**. Wählen Sie die Registerkarte für die Quelle, um die XML-Quelle direkt zu aktualisieren.
 
2. Fügen Sie diese Berechtigungen in der Datei „Manifest.xml“ (im Ordner **Eigenschaften**) des Projekts unmittelbar vor oder nach dem `<application>`-Tag hinzu:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. Fügen Sie folgendes zwischen den Tags `<application>` und `</application>` hinzu, um den Agentdienst zu deklarieren:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

4. Ersetzen Sie in dem Code, den Sie gerade eingefügt haben, `"<Your application name>"` in der Beschriftung (in "label"). Dies wird im Menü **Einstellungen** angezeigt. Hier können Benutzer die auf dem Gerät ausgeführten Dienste sehen. Sie können z. B. das Wort "Dienst" in der Beschriftung hinzufügen.

###Senden eines Bildschirms an Mobile Engagement

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm an das Mobile Engagement-Back-End schicken. Stellen Sie dazu sicher, dass `MainActivity` von `EngagementActivity` anstelle von `Activity` erbt.

	public class MainActivity : EngagementActivity

##<a id="monitor"></a>Verbinden der App mit Überwachung in Echtzeit

[AZURE.INCLUDE [Verbinden der App mit Überwachung in Echtzeit](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern interagieren und diese mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen erreichen. Dieses Modul nennt sich REACH im Mobile Engagement-Portal. In den folgenden Abschnitten richten Sie Ihre App für den Empfang ein.

[AZURE.INCLUDE [Aktivieren von Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Aktivieren von In-App-Messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Senden von Benachrichtigungen vom Portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png

<!---HONumber=AcomDC_0330_2016-->