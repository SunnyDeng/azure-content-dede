<properties
	pageTitle="Erste Schritte mit Azure Mobile Engagement für eine Unity Android-Bereitstellung"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen für Unity-Apps, die auf iOS-Geräten bereitgestellt werden."
	services="mobile-engagement"
	documentationCenter="unity"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-unity-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Erste Schritte mit Azure Mobile Engagement für eine Unity Android-Bereitstellung

[AZURE.INCLUDE [Umschalter für das Hero-Tutorial](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement die Nutzung Ihrer App verstehen können und wie Sie Pushbenachrichtigungen an segmentierte Benutzer einer Unity-Anwendung senden, wenn Sie die Bereitstellung für ein Android-Gerät durchführen. In diesem Tutorial wird das klassische Roll-A-Ball-Tutorial von Unity als Ausgangspunkt verwendet. Sie sollten die Schritte in diesem [Tutorial](mobile-engagement-unity-roll-a-ball.md) ausführen, bevor Sie mit der Mobile Engagement-Integration beginnen, die in diesem Tutorial beschrieben wird.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Unity-Editor](http://unity3d.com/get-unity)
+ [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
+ Google Android SDK

> [AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).

##<a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre App

[AZURE.INCLUDE [Erstellen einer Mobile Engagement-App im Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End

###Importieren des Unity-Pakets

1. Laden Sie das [Mobile Engagement Unity-Paket](https://aka.ms/azmeunitysdk) herunter, und speichern Sie es auf Ihrem lokalen Computer. 

2. Wechseln Sie zu **Assets -> Paket importieren -> Benutzerdefiniertes Paket**, und wählen Sie das im vorherigen Schritt heruntergeladene Paket aus.

	![][70]

3. Stellen Sie sicher, dass alle Dateien ausgewählt sind, und klicken Sie auf die Schaltfläche **Importieren**.

	![][71]

4. Nach dem Importieren werden die importierten SDK-Dateien in Ihrem Projekt angezeigt.

	![][72]

###Aktualisieren von „EngagementConfiguration“

1. Öffnen Sie die **EngagementConfiguration**-Skriptdatei im SDK-Ordner, und aktualisieren **ANDROID\_CONNECTION\_STRING** gemäß der Verbindungszeichenfolge, die Sie zuvor im Azure-Portal abgerufen haben.  

	![][73]

2. Speichern Sie die Datei.

3. Führen Sie **Datei -> Engagement -> Android-Manifest generieren** aus. Dies ist das Plug-In, das vom Mobile Engagement SDK hinzugefügt wird. Wenn Sie darauf klicken, werden die Projekteinstellungen automatisch aktualisiert.

	![][74]

> [AZURE.IMPORTANT] Führen Sie diesen Schritt bei jeder Aktualisierung der **EngagementConfiguration**-Datei aus, da Ihre Änderungen sonst nicht in der App widergespiegelt werden.

###Konfigurieren der App für die einfache Nachverfolgung

1. Öffnen Sie das an das Player-Objekt angefügte **PlayerController**-Skript für die Bearbeitung. 

2. Fügen Sie die folgende using-Anweisung hinzu:

		using Microsoft.Azure.Engagement.Unity;

3. Fügen Sie der `Start()`-Methode Folgendes hinzu:
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###Bereitstellen und Ausführen der App
Stellen Sie sicher, dass das Android-SDK auf Ihrem Computer installiert ist, bevor Sie versuchen, diese Unity-App auf Ihrem Gerät bereitzustellen.

1. Verbinden Sie ein Android-Gerät mit dem Computer. 

2. Öffnen Sie **Datei -> Buildeinstellungen**.

	![][40]

3. Wählen Sie **Android**, und klicken Sie anschließend auf **Plattform wechseln**.

	![][51]

	![][52]

4. Klicken Sie auf **Player-Einstellungen**, und geben Sie einen gültigen Bundle Identifier an.

	![][53]

5. Klicken Sie abschließend auf **Erstellen und Ausführen**.

	![][54]

6. Unter Umständen werden Sie aufgefordert, den Namen eines Ordners zum Speichern des Android-Pakets anzugeben.

7. Wenn keine Fehler auftreten, wird das Paket auf dem verbundenen Gerät bereitgestellt, und das Unity-Spiel wird auf Ihrem Smartphone angezeigt.

##<a id="monitor"></a>Verbinden der App mit Überwachung in Echtzeit

[AZURE.INCLUDE [Verbinden der App mit Überwachung in Echtzeit](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

[AZURE.INCLUDE [Aktivieren von Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###Aktualisieren von „EngagementConfiguration“

1. Öffnen Sie die **EngagementConfiguration**-Skriptdatei im SDK-Ordner, und aktualisieren Sie **ANDROID\_GOOGLE\_NUMBER** mit der **Google Project Number**, die Sie zuvor über das Google Cloud Developer-Portal abgerufen haben. Da dies ein Zeichenfolgenwert ist, muss er in doppelte Anführungszeichen gesetzt werden. 

	![][75]

2. Speichern Sie die Datei .

3. Führen Sie **Datei -> Engagement -> Android-Manifest generieren** aus. Dies ist das Plug-In, das vom Mobile Engagement SDK hinzugefügt wird. Wenn Sie darauf klicken, werden die Projekteinstellungen automatisch aktualisiert.

	![][74]

###Konfigurieren der App für den Empfang von Benachrichtigungen

1. Öffnen Sie das an das Player-Objekt angefügte **PlayerController**-Skript für die Bearbeitung. 

2. Fügen Sie der `Start()`-Methode Folgendes hinzu:

		EngagementReachAgent.Initialize();

3. Nachdem die App aktualisiert wurde, können Sie sie gemäß der unten angegebenen Anleitung auf einem Gerät ausführen.

[AZURE.INCLUDE [Senden von Benachrichtigungen vom Portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png

<!---HONumber=AcomDC_0330_2016-->