<properties
	pageTitle="Erste Schritte mit Azure Mobile Engagement für die Unity iOS-Bereitstellung"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen für Unity-Apps, die auf iOS-Geräten bereitgestellt werden."
	services="mobile-engagement"
	documentationCenter="unity"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-unity-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Erste Schritte mit Azure Mobile Engagement für die Unity iOS-Bereitstellung

[AZURE.INCLUDE [Umschalter für das Hero-Tutorial](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement die Nutzung Ihrer App verstehen können und wie Sie bei der Bereitstellung auf einem iOS-Gerät Pushbenachrichtigungen an segmentierte Benutzer einer Unity-Anwendung senden. In diesem Tutorial wird das klassische Roll-A-Ball-Tutorial von Unity als Ausgangspunkt verwendet. Es wird empfohlen, die Schritte in diesem [Tutorial](mobile-engagement-unity-roll-a-ball.md) auszuführen, bevor Sie mit der Mobile Engagement-Integration fortfahren, die im Tutorial weiter unten erläutert wird.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Unity-Editor](http://unity3d.com/get-unity)
+ [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
+ XCode-Editor

> [AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).

##<a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre iOS-App

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

1. Öffnen Sie die **EngagementConfiguration**-Skriptdatei im SDK-Ordner, und aktualisieren **IOS\_CONNECTION\_STRING** gemäß der Verbindungszeichenfolge, die Sie zuvor im Azure-Portal abgerufen haben.  

	![][73]

2. Speichern Sie die Datei.

###Konfigurieren der App für die einfache Nachverfolgung

1. Öffnen Sie das an das Player-Objekt angefügte **PlayerController**-Skript für die Bearbeitung. 

2. Fügen Sie die folgende using-Anweisung hinzu:

		using Microsoft.Azure.Engagement.Unity;

3. Fügen Sie der `Start()`-Methode Folgendes hinzu:
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###Bereitstellen und Ausführen der App

1. Verbinden Sie ein iOS-Gerät mit dem Computer. 

2. Öffnen Sie **Datei -> Buildeinstellungen**.

	![][40]

3. Wählen Sie **iOS**, und klicken Sie anschließend auf **Plattform wechseln**.

	![][41]

	![][42]

4. Klicken Sie auf **Player-Einstellungen**, und geben Sie eine gültige Paket-ID an.

	![][53]

5. Klicken Sie abschließend auf **Erstellen und Ausführen**.

	![][54]

6. Unter Umständen werden Sie aufgefordert, den Namen eines Ordners zum Speichern des iOS-Pakets anzugeben.

	![][43]

7. Wenn keine Fehler auftreten, wird das Projekt kompiliert, und Sie können es in Ihrer XCode-Anwendung öffnen.

8. Stellen Sie sicher, dass die **Paket-ID** im Projekt korrekt angegeben wurde.

	![][75]

10. Führen Sie die App nun in XCode aus, sodass das Paket auf dem verbundenen Gerät bereitgestellt wird. Das Unity-Spiel wird auf Ihrem Smartphone angezeigt.

##<a id="monitor"></a>Verbinden der App mit Überwachung in Echtzeit

[AZURE.INCLUDE [Verbinden der App mit Überwachung in Echtzeit](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern und mit REACH mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen interagieren. Dieses Modul nennt sich REACH im Mobile Engagement-Portal. Sie müssen keine zusätzlichen Konfigurationsschritte in Ihrer App vornehmen, um Benachrichtigungen zu erhalten, dies ist bereits eingerichtet.

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png

<!---HONumber=AcomDC_0330_2016-->