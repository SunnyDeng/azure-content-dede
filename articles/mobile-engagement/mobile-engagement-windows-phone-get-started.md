<properties 
	pageTitle="Erste Schritte mit Azure Mobile Engagement für Windows Phone Silverlight-Apps" 
	description="Erfahren Sie, wie Sie Azure Mobile Engagement mit Analysen und Pushbenachrichtigungen für Windows Phone Silverlight-Apps verwenden."
	services="mobile-engagement" 
	documentationCenter="windows" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/30/2015" 
	ms.author="piyushjo" />
	
# Erste Schritte mit Azure Mobile Engagement für Windows Phone Silverlight-Apps

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md) 
- [iOS - Obj C](mobile-engagement-ios-get-started.md) 
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md) 

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Engagement Ihre App-Nutzung nachvollziehen und Pushbenachrichtigungen an nach Segmenten eingeteilte Benutzer einer Windows Phone Silverlight-Anwendung senden können. In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Mobile Engagement dargestellt. Sie erstellen eine leere Windows Phone Silverlight-App, die grundlegende Daten erfasst und Pushbenachrichtigungen über den Microsoft-Pushbenachrichtigungsdienst (MPNS) empfängt. Nach Abschluss dieses Lernprogramms können Sie Pushbenachrichtigungen an alle Geräte oder zielspezifische Benutzer basierend auf ihren Geräteeigenschaften übertragen (mithilfe von MPNS). Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Mobile Engagement zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren.

> [AZURE.NOTE]Informationen zu Windows Phone 8.1 (nicht Silverlight) finden Sie im [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)-Lernprogramm.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Visual Studio 2013
+ Das [Mobile Engagement Windows Phone SDK]

> [AZURE.IMPORTANT]Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für Windows Phone Silverlight-Apps. Hierzu benötigen Sie ein aktives Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

##<a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre Windows Phone Silverlight-App

1. Melden Sie sich beim Azure-Verwaltungsportal an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2. Klicken Sie auf **App-Dienste**, dann auf **Mobile Engagement** und anschließend auf **Erstellen**.

   	![][7]

3. Geben Sie in das Popupfenster, das angezeigt wird, die folgenden Informationen ein:
 
   	![][8]

	- **Name der Anwendung**: Geben Sie den Namen der Anwendung ein. Sie können jedes beliebige Zeichen verwenden.
	- **Platform**: Wählen Sie die Zielplattform (**Windows Phone Silverlight**) für die App (Wenn Ihre App auf mehrere Plattformen ausgerichtet ist, wiederholen Sie dieses Lernprogramm für jede Plattform). 
	- **Ressourcenname der Anwendung**: Dies ist der Name, mit dem Sie über APIs und URLs auf diese Anwendung zugreifen können. Sie können ausschließlich konventionelle URL-Zeichen verwenden. Der automatisch generierte Name sollte Ihnen eine starke Basis bieten. Außerdem sollen Sie den Plattformnamen anfügen, um einen Namenskonflikt zu vermeiden, da dieser Name eindeutig sein muss.
	- **Standort**: Wählen Sie das Rechenzentrum, in denen diese App (und vor allem die Sammlung) gehostet werden soll.
	- **Sammlung**: Wenn Sie bereits eine Anwendung erstellt haben, wählen Sie eine zuvor erstellte Sammlung; wählen Sie andernfalls „Neue Sammlung“.
	- **Name der Sammlung**: Dieser steht für die Anwendungsgruppe. Dadurch wird außerdem sichergestellt, dass sich alle Ihre Apps in einer Gruppe befinden, wodurch aggregierte Berechnungen von Metriken möglich sind. Sie sollten hier ggf. Ihren Firmenname oder Ihre Abteilung verwenden.

4. Wählen Sie die Anwendung aus, die Sie soeben in der Registerkarte **Anwendungen** erstellt haben.

5. Klicken Sie auf **Verbindungsinformationen**, um die Verbindungseinstellungen für die SDK-Integration ihrer mobilen App anzuzeigen.
 
   	![][10]

6. Kopieren Sie die **Verbindungszeichenfolge** – diese benötigen Sie zum Identifizieren dieser App in Ihrem Anwendungscode und stellen Sie die Verbindung zu Mobile Engagement von Ihrer Phone-App her.

   	![][11]

##<a id="connecting-app"></a>Verbinden Ihrer App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine „einfache Integration“ dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung. Die vollständige Integrationsdokumentation finden Sie in der [Dokumentation zum Mobile Engagement Windows Phone SDK].

Wir erstellen eine einfache App mit Visual Studio, um die Integration zu veranschaulichen.

###Erstellen eines neuen Windows Phone Silverlight-Projekts

1. Starten Sie Visual Studio und wählen Sie im Startbildschirm **Neues Projekt...**.

2. Wählen Sie im Popupfenster `Store Apps` -> `Windows Phone Apps` -> `Blank App (Windows Phone Silverlight)`. Füllen Sie die App `Name` und `Solution name` aus und klicken Sie dann auf **OK**.

   	![][13]

3. Sie können als Ziel entweder die Version **Windows Phone 8.0** oder **Windows Phone 8.1** auswählen.

Sie haben nun eine neue Windows Phone Silverlight-App erstellt, in die das Azure Mobile Engagement-SDK integriert wird.

###Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End. 

1. Installieren Sie das NuGet-Paket [Mobile Engagement Windows Phone SDK] im Projekt.

2. Öffnen Sie `WMAppManifest.xml` (im Ordner "Konfigurationseigenschaften") und stellen Sie sicher, dass folgende Deklarationen im Tag `<Capabilities />` vorliegen (fügen Sie sie hinzu, falls dies nicht der Fall ist):
		
		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

	![][20]

3. Nun fügen Sie die zuvor für Ihre Mobile Engagement-App kopierte Verbindungszeichenfolge in die Datei `Resources\EngagementConfiguration.xml` zwischen die Tags `<connectionString>` und `</connectionString>` ein:

	![][21]

4. In der Datei `App.xaml.cs`:

	a. Fügen Sie die Anweisung `using` hinzu

			using Microsoft.Azure.Engagement;

	b. Initialisieren Sie das SDK in der Methode `Application_Launching`:
			
			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c. Fügen Sie in `Application_Activated` Folgendes ein.

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>Aktivieren der Überwachung in Echtzeit

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End schicken. Wir erreichen dies durch die Erstellung von Unterklassen unserer `MainPage` mit der `EngagementPage`, die durch das Mobile Engagement-SDK bereitgestellt wird.

1. Fügen Sie die Anweisung `using` hinzu:

		using Microsoft.Azure.Engagement;

2. Ersetzen Sie die übergeordnete Klasse von `MainPage`, die vor `PhoneApplicationPage` kommt, wie unten dargestellt durch `EngagementPage`:

	![][22]

3. In der Datei `MainPage.xml`: a. Fügen Sie Ihre Namespace-Deklarationen hinzu:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
	
	b. Ersetzen Sie die `phone:PhoneApplicationPage` im XML-Tagnamen durch `engagement:EngagementPage`

###Stellen Sie sicher, dass Ihre App mit der Echtzeitüberwachung verbunden ist.

In diesem Abschnitt erfahren Sie, wie Sie sicherstellen, dass Ihre App eine Verbindung mit dem Mobile Engagement-Back-End mithilfe der Echtzeitüberwachungsfunktion von Mobile Engagement herstellt.

1. Navigieren Sie zum Mobile Engagement-Portal.

	Stellen Sie im Azure-Portal sicher, dass Sie sich in der App befinden, die für dieses Projekt verwendet wird, und klicken Sie dann unten auf die Schaltfläche **Beginnen**.

	![][26]

2. Sie gelangen zu der Seite „Einstellungen“ des Engagement-Portals Ihrer App. Klicken Sie dort auf die Registerkarte **Überwachen**, wie unten dargestellt.![][30]

3. Die Überwachung ist bereit, in Echtzeit jedes Gerät anzuzeigen, auf dem Ihre App gestartet wird.

4. Wenn Sie sich wieder in Visual Studio befinden, starten Sie Ihre App im Emulator oder auf einem angeschlossenen Gerät.

5. Wenn es funktioniert hat, sollte jetzt eine Sitzung in der Überwachung angezeigt werden!![][33]

**Glückwunsch!** Sie haben den ersten Schritt dieses Lernprogramms mit einer App abgeschlossen, die eine Verbindung zum Mobile Engagement-Back-End herstellt, das bereits Daten sendet.

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern interagieren und diese mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen ERREICHEN. Dieses Modul nennt sich REACH im Mobile Engagement-Portal. In den folgenden Abschnitten wird Ihre App für den Empfang eingerichtet.

###Aktivieren des Empfangs von MPNS-Pushbenachrichtigungen in Ihrer App

Fügen Sie Ihrer Datei `WMAppManifest.xml` unter "Capabilities" neue Funktionen hinzu:

		ID_CAP_PUSH_NOTIFICATION 
		ID_CAP_WEBBROWSERCOMPONENT 

![][34]

###Initialisieren des REICHWEITEN-SDK

1. Rufen Sie `EngagementReach.Instance.Init();` in `App.xaml.cs` gleich nach der Agent-Initialisierung in der Funktion `Application_Launching` auf:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. Rufen Sie `EngagementReach.Instance.OnActivated(e);` in `App.xaml.cs` gleich nach der Agent-Initialisierung in der Funktion `Application_Activated` auf:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Fertig. Jetzt stellen wir sicher, dass diese Basisintegration richtig durchgeführt wurde.

##<a id="send"></a>Versenden von Benachrichtigungen an die App

Wir erstellen nun eine einfache Push-Benachrichtigungskampagne, die eine Push-Benachrichtigung an die App sendet.

1. Navigieren Sie zu der Registerkarte **REACH** in Ihrem Mobile Engagement-Portal.

2. Klicken Sie auf **Neue Ankündigung**, um die Push-Kampagne zu erstellen.![][35]

3. Richten Sie das erste Feld der Kampagne mit den folgenden Schritten ein:![][36]

	1. Wählen Sie einen beliebigen Namen für die Kampagne.
	2. Wählen Sie für die **Lieferzeit** *Jederzeit*, um eine Benachrichtigung darüber zu erhalten, ob die App gestartet wurde oder nicht.
	3. Geben Sie in den Benachrichtigungstext den Titel ein, der im Push-Vorgang fett gedruckt erscheint
	4. Geben Sie dann Ihre Nachricht ein.

4. Scrollen Sie nach unten und wählen Sie im Inhaltsabschnitt **Nur Benachrichtigung**.![][37]

5. Sie haben das Festlegen der Basiskampagne abgeschlossen, scrollen Sie nun nach unten und klicken Sie auf die Schaltfläche **Erstellen**, um Ihre Kampagne zu speichern!

6. Als letzten Schritt klicken Sie auf **Aktivieren**, um die Kampagne zu aktivieren und Pushbenachrichtigungen zu senden. ![][39]

7. Auf Ihrem Gerät sollte nun folgende Benachrichtigung angezeigt werden: **Herzlichen Glückwunsch!**: ![][40]

<!-- URLs. -->
[Mobile Engagement Windows Phone SDK]: http://go.microsoft.com/?linkid=9874664
[Dokumentation zum Mobile Engagement Windows Phone SDK]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!--HONumber=54--> 