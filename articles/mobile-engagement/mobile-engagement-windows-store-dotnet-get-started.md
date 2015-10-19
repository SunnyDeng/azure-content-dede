<properties
	pageTitle="Erste Schritte mit Azure Mobile Engagement für Windows Universal-Apps"
	description="Erfahren Sie, wie Sie Azure Mobile Engagement mit Analysen und Pushbenachrichtigungen für Windows Universal-Apps verwenden."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Erste Schritte mit Azure Mobile Engagement für Windows Universal-Apps

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Engagement Ihre App-Nutzung nachvollziehen und Pushbenachrichtigungen an nach Segmenten eingeteilte Benutzer einer Windows Universal-Anwendung senden können. In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Mobile Engagement dargestellt. Darin erstellen Sie eine leere Windows Universal-App, die Basisdaten zur App-Nutzung erfasst und Push-Benachrichtigungen mit dem Windows Notification Service (WNS) empfängt.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Visual Studio 2013
+ NuGet-Paket [MicrosoftAzure.MobileEngagement]

> [AZURE.IMPORTANT]Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für Windows Universal-Apps. Sie benötigen ein aktives Azure-Konto, um es abzuschließen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-DE%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

##<a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre Windows Universal-App

[AZURE.INCLUDE [Erstellen einer Mobile Engagement-App im Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine „einfache Integration“ dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung. Die vollständige Integrationsdokumentation finden Sie im Artikel zur [Integration des Mobile Engagement Windows Universal SDK](../mobile-engagement-windows-store-sdk-overview/).

Wir erstellen eine einfache App mit Visual Studio, um die Integration zu veranschaulichen.

###Erstellen Sie ein neues Windows Universal-App-Projekt

In den folgenden Schritten wird die Verwendung von Visual Studio 2015 angenommen, obwohl die Schritte in früheren Versionen von Visual Studio ähnlich sind.

1. Starten Sie Visual Studio, und wählen Sie im **Startbildschirm** die Option **Neues Projekt** aus.

2. Wählen Sie im Popupfenster **Windows 8** -> **Universell** -> **Leere App (Universelle Windows 8.1-App)** aus. Geben Sie den **Namen** der App und den **Lösungsnamen** ein, und klicken Sie dann auf **OK**.

    ![][1]

> [AZURE.IMPORTANT]Azure Mobile Engagement unterstützt noch keine universellen Windows 10-Apps.

Sie haben nun eine neues Projekt für eine Windows Universal-App erstellt, in die das Azure Mobile Engagement-SDK integriert wird.

###Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End.

1. Installieren Sie das NuGet-Paket [MicrosoftAzure.MobileEngagement] im Projekt. Wenn Sie sowohl für die Windows- als auch die Windows Phone-Plattform entwickeln, müssen Sie dies für beide Projekte tun. Das gleiche Nuget-Paket platziert die korrekten Plattform-spezifischen Binärdateien in jedem Projekt.

2. Öffnen Sie **Package.appxmanifest**, und stellen Sie sicher, dass dort die folgende Funktion hinzugefügt ist:

		Internet (Client)

	![][2]

3. Nun fügen Sie die zuvor für Ihre Mobile Engagement-App kopierte Verbindungszeichenfolge in die Datei `Resources\EngagementConfiguration.xml` zwischen die Tags `<connectionString>` und `</connectionString>` ein:

	![][3]

	>[AZURE.TIP]Wenn Ihre App sowohl Windows und als auch Windows Phone als Plattform vorsieht, sollten Sie dennoch zwei Mobile Engagement-Anwendungen erstellen – eine für jede unterstützte Plattform. Dadurch wird sichergestellt, dass Sie eine korrekte Aufteilung der Zielgruppe erstellen und entsprechend gezielte Benachrichtigungen für jede Plattform senden können.

4. In der Datei `App.xaml.cs`:

	a. Fügen Sie die Anweisung `using` hinzu:

			using Microsoft.Azure.Engagement;

	b. Initialisieren Sie das SDK in der **OnLaunched**-Methode:

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c. Fügen Sie Folgendes in der **OnActivated**-Methode ein, und fügen Sie die Methode hinzu, falls sie nicht schon vorhanden ist:

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Aktivieren der Überwachung in Echtzeit

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End schicken.

1. 	Fügen Sie in **MainPage.Xaml.cs** die folgende `using`-Anweisung hinzu:

		using Microsoft.Azure.Engagement.Overlay;

2. Ersetzen Sie die **Page**-Basisklasse von **MainPage** durch **EngagementPageOverlay**:

		class MainPage : EngagementPageOverlay

3. In der Datei `MainPage.xaml`:

	a. Fügen Sie Ihre Namespace-Deklarationen hinzu:

		xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

	b. Ersetzen Sie **Page** im XML-Tagnamen durch **engagement:EngagementPageOverlay**.
	
> [AZURE.IMPORTANT]Wenn Ihre Seite die `OnNavigatedTo`-Methode überschreibt, rufen Sie unbedingt `base.OnNavigatedTo(e)` auf. Andernfalls wird die Aktivität nicht erfasst (`EngagementPage` ruft `StartActivity` innerhalb der `OnNavigatedTo`-Methode auf). Dies ist besonders wichtig in einem Windows Phone-Projekt, in dem die Standardvorlage eine `OnNavigatedTo`-Methode besitzt.

##<a id="monitor"></a>Verbinden der App mit Überwachung in Echtzeit

[AZURE.INCLUDE [Verbinden der App mit Überwachung in Echtzeit](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern interagieren und diese mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen erreichen. Dieses Modul nennt sich REACH im Mobile Engagement-Portal. In den folgenden Abschnitten richten Sie Ihre App für den Empfang ein.

###Aktivieren Sie Ihre App für den Empfang von WNS-Push-Benachrichtigungen

1. Legen Sie in der Datei `Package.appxmanifest` auf der Registerkarte **Anwendung** unter **Benachrichtigungen** die Option **Toastfähig** auf **Ja** fest.

	![][5]

###Initialisieren des REACH-SDK

1. Rufen Sie in `App.xaml.cs` **EngagementReach.Instance.Init();** in der **OnLaunched**-Funktion direkt nach der Agentinitialisierung auf:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. Rufen Sie in `App.xaml.cs` **EngagementReach.Instance.Init(e);** in der **OnActivated**-Funktion direkt nach der Agentinitialisierung auf:

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Sie sind bereits für das Senden einer Popupbenachrichtigung. und wir werden jetzt überprüfen, ob Sie diese einfache Integration ordnungsgemäß durchgeführt haben.

###Gewähren von Zugriff auf Mobile Engagement zum Senden von Benachrichtigungen

1. Sie müssen Ihre App einer Windows Store-App zuweisen, um Ihre **Paket-Sicherheits-ID (SID)** und Ihren **Geheimen Schlüssel** (Geheimer Clientschlüssel) zu erhalten. Sie können eine Anwendung aus dem [Windows Store Dev Center] erstellen und dann sicherstellen, dass Sie **Verknüpfen der App mit dem Store** aus Visual Studio verwenden.

	![][7]

2. Navigieren Sie zu den **Einstellungen** Ihres Mobile Engagement-Portals, und klicken Sie auf den Abschnitt **Systemeigener Push** auf der linken Seite.

3. Klicken Sie auf die Schaltfläche **Bearbeiten**, um Ihre **Paket-Sicherheits-ID (SID)** und Ihren **Geheimen Schlüssel** wie unten dargestellt einzugeben:

	![][6]

##<a id="send"></a>Versenden von Benachrichtigungen an die App

[AZURE.INCLUDE [Erstellen einer Windows-Pushkampagne](../../includes/mobile-engagement-windows-push-campaign.md)]

Wenn die App ausgeführt wurde, wird eine Benachrichtigung in der App angezeigt. Andernfalls wird eine Popupbenachrichtigung angezeigt, wenn die App geschlossen wurde. Wenn eine Benachrichtigung in der App angezeigt wird, jedoch keine Popupbenachrichtigung, und Sie die App in Visual Studio im Debugmodus ausführen, sollten Sie **Zyklusereignisse -> Anhalten** in der Symbolleiste auswählen, um sicherzustellen, dass die App tatsächlich angehalten wird. Wenn Sie beim Debuggen der Anwendung in Visual Studio auf die Schaltfläche "Anfang" geklickt haben, wird diese nicht immer angehalten, und während die Benachrichtigung in der App angezeigt wird, würde keine Popupbenachrichtigung ausgegeben werden.

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows Store Dev Center]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png

<!---HONumber=Oct15_HO2-->