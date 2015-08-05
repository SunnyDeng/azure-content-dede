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
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Erste Schritte mit Azure Mobile Engagement für Windows Universal-Apps

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Engagement Ihre App-Nutzung nachvollziehen und Pushbenachrichtigungen an nach Segmenten eingeteilte Benutzer einer Windows Universal-Anwendung senden können. In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Mobile Engagement dargestellt. Darin erstellen Sie eine leere Windows Universal-App, die Basisdaten zur App-Nutzung erfasst und Push-Benachrichtigungen mit dem Windows Notification Service (WNS) empfängt. Nach Abschluss dieses Lernprogramms können Sie Pushbenachrichtigungen an alle Geräte oder zielspezifische Benutzer basierend auf ihren Geräteeigenschaften übertragen. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Mobile Engagement zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Visual Studio 2013
+ Das [Mobile Engagement Windows Universal SDK]

> [AZURE.IMPORTANT]Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für Windows Universal-Apps. Sie benötigen ein aktives Azure-Konto, um es abzuschließen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

##<a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre Windows Universal-App

1. Melden Sie sich beim Azure-Verwaltungsportal an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2. Klicken Sie auf **App-Dienste**, dann auf **Mobile Engagement** und anschließend auf **Erstellen**.

   	![][7]

3. Geben Sie in das Popupfenster, das angezeigt wird, die folgenden Informationen ein:

   	![][8]

	- **Name der Anwendung**: Geben Sie den Namen der Anwendung ein. Sie können jedes beliebige Zeichen verwenden.
	- **Platform**: Wählen Sie die Zielplattform (**Windows Universal**) für die App (Wenn Ihre App auf mehrere Plattformen ausgerichtet ist, wiederholen Sie dieses Lernprogramm für jede Plattform).
	- **Ressourcenname der Anwendung**: Dies ist der Name, mit dem Sie über APIs und URLs auf diese Anwendung zugreifen können. Sie können ausschließlich konventionelle URL-Zeichen verwenden. Der automatisch generierte Name sollte Ihnen eine starke Basis bieten. Außerdem sollen Sie den Plattformnamen anfügen, um einen Namenskonflikt zu vermeiden, da dieser Name eindeutig sein muss.
	- **Standort**: Wählen Sie das Rechenzentrum, in denen diese App (und vor allem die Sammlung) gehostet werden soll.
	- **Sammlung**: Wenn Sie bereits eine Anwendung erstellt haben, wählen Sie eine zuvor erstellte Sammlung; wählen Sie andernfalls „Neue Sammlung“.
	- **Name der Sammlung**: Dieser steht für die Anwendungsgruppe. Dadurch wird außerdem sichergestellt, dass sich alle Ihre Apps in einer Gruppe befinden, wodurch aggregierte Berechnungen von Metriken möglich sind. Sie sollten hier ggf. Ihren Firmenname oder Ihre Abteilung verwenden.

	> [AZURE.TIP]Wenn Ihre Universal App sowohl Windows und als auch Windows Phone als Plattform vorsieht, sollten Sie dennoch zwei Mobile Engagement-Anwendungen erstellen, also eine für jede unterstützte Plattform. Dadurch wird sichergestellt, dass Sie eine korrekte Aufteilung der Zielgruppe erstellen und entsprechend gezielte Benachrichtigungen für jede Plattform senden können.

4. Wählen Sie die Anwendung aus, die Sie soeben in der Registerkarte **Anwendungen** erstellt haben.

5. Klicken Sie auf **Verbindungsinformationen**, um die Verbindungseinstellungen für die SDK-Integration ihrer mobilen App anzuzeigen.

   	![][10]

6. Kopieren Sie die **Verbindungszeichenfolge** – diese benötigen Sie zum Identifizieren dieser App in Ihrem Anwendungscode und stellen Sie die Verbindung zu Mobile Engagement von Ihrer Universal-App her.

   	![][11]

##<a id="connecting-app"></a>Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine „einfache Integration“ dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung. Die vollständige Integrationsdokumentation finden Sie in der [Dokumentation zum Mobile Engagement Windows Universal SDK].

Wir erstellen eine einfache App mit Visual Studio, um die Integration zu veranschaulichen.

###Erstellen Sie ein neues Windows Universal-App-Projekt

Wenn Sie bereits über eine App verfügen und mit der Windows Universal-Entwicklung vertraut sind, können Sie diesen Schritt überspringen.

1. Starten Sie Visual Studio und wählen Sie im Startbildschirm **Neues Projekt...**.

2. Wählen Sie im Popupfenster `Store Apps` -> `Universal Apps` -> `Blank App (Universal Apps)`. Füllen Sie die App `Name` und `Solution name` aus und klicken Sie dann auf **OK**.

   	![][13]

Sie haben nun eine neues Projekt für eine Windows Universal-App erstellt, in die das Azure Mobile Engagement-SDK integriert wird.

###Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End.

1. Installieren Sie das NuGet-Paket [Mobile Engagement Windows Universal SDK] im Projekt. Wenn Sie sowohl für die Windows- als auch die Windows Phone-Plattform entwickeln, müssen Sie dies für beide Projekte tun. Das gleiche Nuget-Paket platziert die korrekten Plattform-spezifischen Binärdateien in jedem Projekt.

2. Öffnen Sie `Package.appxmanifest` und fügen Sie Folgendes hinzu, falls dies nicht automatisch geschieht:

		Internet (Client)

	![][20]

3. Nun fügen Sie die zuvor für Ihre Mobile Engagement-App kopierte Verbindungszeichenfolge in die Datei `Resources\EngagementConfiguration.xml` zwischen die Tags `<connectionString>` und `</connectionString>` ein:

	![][22]

	>[AZURE.TIP]Wenn Ihre App sowohl Windows und als auch Windows Phone als Plattform vorsieht, sollten Sie dennoch zwei Mobile Engagement-Anwendungen erstellen – eine für jede unterstützte Plattform. Dadurch wird sichergestellt, dass Sie eine korrekte Aufteilung der Zielgruppe erstellen und entsprechend gezielte Benachrichtigungen für jede Plattform senden können.

4. In der Datei `App.xaml.cs`:

	a. Fügen Sie die Anweisung `using` hinzu:

			using Microsoft.Azure.Engagement;

	b. Initialisieren Sie das SDK in der Methode `OnLaunched`:

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c. Fügen Sie Folgendes in der `OnActivated` -Methode ein und fügen Sie die Methode hinzu, falls sie nicht schon vorhanden ist:

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Aktivieren der Überwachung in Echtzeit

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End schicken. Wir erreichen dies durch die Erstellung von Unterklassen unserer `MainPage` mit der `EngagementPage`, die durch das Mobile Engagement-SDK bereitgestellt wird.

1. 	Fügen Sie die Anweisung `using` hinzu:

		using Microsoft.Azure.Engagement;

2. Ersetzen Sie die übergeordnete Klasse von `MainPage`, die vor `Page` kommt, durch `EngagementPage`:

	![][23]

3. In der Datei `MainPage.xml`:

	a. Fügen Sie Ihre Namespace-Deklarationen hinzu:

			xmlns:engagement="using:Microsoft.Azure.Engagement"

	b. Ersetzen Sie die `Page` im XML-Tagnamen durch `engagement:EngagementPage`

###Stellen Sie sicher, dass Ihre App mit der Echtzeitüberwachung verbunden ist.

In diesem Abschnitt erfahren Sie, wie Sie sicherstellen, dass Ihre App eine Verbindung mit dem Mobile Engagement-Back-End mithilfe der Echtzeitüberwachungsfunktion von Mobile Engagement herstellt.

1. Navigieren Sie zum Mobile Engagement-Portal.

	Stellen Sie im Azure-Portal sicher, dass Sie sich in der App befinden, die für dieses Projekt verwendet wird, und klicken Sie dann unten auf die Schaltfläche **Beginnen**.

	![][26]

2. Sie gelangen zu der Seite „Einstellungen“ des Engagement-Portals Ihrer App. Klicken Sie dort auf die Registerkarte **Überwachen**, wie unten dargestellt.![][30]

3. Die Überwachung ist bereit, in Echtzeit jedes Gerät anzuzeigen, auf dem Ihre App gestartet wird.

4. Wenn Sie sich wieder in Visual Studio befinden, starten Sie Ihre App im Emulator oder auf einem angeschlossenen Gerät.

5. Wenn es funktioniert hat, sollte jetzt eine Sitzung in der Überwachung in Echtzeit angezeigt werden! ![][33]

**Glückwunsch!** Sie haben den ersten Schritt dieses Lernprogramms mit einer App abgeschlossen, die eine Verbindung zum Mobile Engagement-Back-End herstellt, das bereits Daten sendet.

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern interagieren und diese mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen erreichen. Dieses Modul nennt sich REACH im Mobile Engagement-Portal. In den folgenden Abschnitten wird Ihre App für den Empfang eingerichtet.

###Aktivieren Sie Ihre App für den Empfang von WNS-Push-Benachrichtigungen

1. In der Datei `Package.appxmanifest` wählen Sie in der Registerkarte `Application` unter `Notifications` die Option `Yes` für `Toast capable:` aus:

	![][35]

###Initialisieren des REACH-SDK

1. Rufen Sie `EngagementReach.Instance.Init();` in `App.xaml.cs` gleich nach der Agent-Initialisierung in der Funktion `OnLaunched` auf:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. Rufen Sie `EngagementReach.Instance.Init(e);` in `App.xaml.cs` gleich nach der Agent-Initialisierung in der Funktion `OnActivated` auf:

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Sie sind für das Senden eines Toasts bereit. Nun stellen wir sicher, ob Sie diese Basisintegration richtig abgeschlossen haben.

###Gewähren von Zugriff auf Mobile Engagement zum Senden von Benachrichtigungen

1. Sie müssen Ihre App dann einer Windows Store-App zuweisen, um `Package security identifier (SID)` und `Secret Key` (Client Secret) zu erhalten. Sie können eine Anwendung aus dem [Windows Store Dev Center] erstellen und dann sichergehen, dass von Visual Studio die **App mit dem Store verknüpft ist**.

2. Navigieren Sie zu den **Einstellungen** Ihres Mobile Engagement-Portals und klicken Sie auf den Abschnitt `Native Push` auf der linken Seite.

3. Klicken Sie auf die Schaltfläche `Edit` zur Eingabe Ihrer `Package security identifier (SID)` und Ihrer `Secret Key` wie unten dargestellt:

	![][36]

##<a id="send"></a>Versenden von Benachrichtigungen an die App

Wir erstellen nun eine einfache Push-Benachrichtigungskampagne, die eine Push-Benachrichtigung an die App sendet.

1. Navigieren Sie zu der Registerkarte **REACH** in Ihrem Mobile Engagement-Portal.

2. Klicken Sie auf **Neue Ankündigung**, um die Push-Kampagne zu erstellen.![][37]

3. Richten Sie das erste Feld der Kampagne mit den folgenden Schritten ein:![][38]

	a. Benennen der Kampagne.

	b. Wählen Sie für die **Lieferzeit** *Jederzeit*, um eine Benachrichtigung darüber zu erhalten, ob die App gestartet wurde oder nicht.

	c. Geben Sie in den Benachrichtigungstext den **Titel** ein, der im Push-Vorgang fett gedruckt erscheint.

	d. Geben Sie dann Ihre Nachricht ein.

4. Scrollen Sie nach unten und wählen Sie im Inhaltsabschnitt **Nur Benachrichtigung**.![][39]

5. Sie haben das Festlegen der Basiskampagne abgeschlossen, scrollen Sie nun nach unten und klicken Sie auf die Schaltfläche **Erstellen**, um Ihre Kampagne zu speichern!

6. Als letzten Schritt klicken Sie auf **Aktivieren**, um die Kampagne zu aktivieren und Pushbenachrichtigungen zu senden. ![][41]

Nun sollte eine Popupbenachrichtigung von der Kampagne auf Ihrem Gerät erscheinen – die App sollte geschlossen sein, um sie sehen zu können. Wenn die App ausgeführt wurde, dann stellen Sie sicher, dass sie vor dem Aktivieren der Kampagne für einige Minuten geschlossen bleibt, um Popupbenachrichtigungen empfangen zu können. Falls Sie In-App-Benachrichtigungen integrieren möchten, sodass die Benachrichtigung in der App angezeigt wird, wenn sie geöffnet ist, schauen Sie nach unter [Windows Universal-Apps - Overlay-Integration].

<!-- URLs. -->
[Dokumentation zum Mobile Engagement Windows Universal SDK]: ../mobile-engagement-windows-store-integrate-engagement/
[Mobile Engagement Windows Universal SDK]: http://go.microsoft.com/?linkid=9864592
[Windows Store Dev Center]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Windows Universal-Apps - Overlay-Integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/UniversalAppCreation.png
[20]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-dotnet-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-dotnet-get-started/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[30]: ./media/mobile-engagement-windows-store-dotnet-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-store-dotnet-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-dotnet-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-content.png
[41]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-activate.png
 

<!---HONumber=July15_HO4-->