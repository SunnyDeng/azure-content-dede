<properties 
	pageTitle="Erste Schritte mit Azure Mobile Engagement für Windows Phone" 
	description="Erfahren Sie, wie Sie Azure Mobile Engagement mit Analysen und Pushbenachrichtigungen in Windows Phone verwenden."
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="C#" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Erste Schritte mit Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Universal">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Engagement Ihre App-Nutzung nachvollziehen und Pushbenachrichtigungen an nach Segmenten eingeteilte Benutzer einer Windows Phone-Anwendung senden können. 
Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Mobile Engagement. Sie erstellen eine leere Windows Phone-App, die grundlegende Daten erfasst und Pushbenachrichtigungen über den Microsoft-Pushbenachrichtigungsdienst empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie an alle Geräte oder bestimmte Zielbenutzer Pushbenachrichtigungen senden, die auf deren Geräteeigenschaften basieren. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Mobile Engagement zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren.


Für dieses Lernprogramm ist Folgendes erforderlich:

+ Visual Studio 2013
+ Das [Mobile Engagement Windows Phone SDK]

> [AZURE.IMPORTANT] Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für Windows Phone-Apps. Hierzu benötigen Sie ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

## <a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre App

1. Melden Sie sich im [Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **+NEU**.

2. Klicken Sie auf **App Services**, dann auf **Mobile Engagement** und schließlich auf **Erstellen**.

   	![][7]

3. Geben Sie im daraufhin angezeigten Popupfenster die folgenden Informationen ein:
 
   	![][8]

	1. **Name der Anwendung**: Sie können den Namen der Anwendung eingeben. Dabei können Sie jedes beliebige Zeichen verwenden.
	2. **Plattform**: Wählen Sie die Zielplattform für die App aus. (Wenn Ihre App auf mehrere Plattformen ausgerichtet ist, wiederholen Sie dieses Lernprogramm für jede Plattform.)
	3. **Name der Anwendungsressource**: Dies ist der Name, über den diese Anwendung über APIs und URLs zugänglich ist. Wir empfehlen die Verwendung herkömmlicher URL-Zeichen: Der automatisch generierte Name sollte bereits eine starke Grundlage darstellen. Wir empfehlen außerdem das Anhängen des Plattformnamens, um Namenskonflikte zu vermeiden, da dieser Name eindeutig sein muss.
	4. **Speicherort**: Wählen Sie das Rechenzentrum, in dem diese App (und vor allem die zugehörige Auflistung - siehe unten) gehostet werden soll.
	5. **Auflistung**: Wenn Sie bereits eine Anwendung erstellt haben, wählen Sie eine zuvor erstellte Auflistung. Andernfalls wählen Sie "Neue Auflistung".
	6. **Auflistungsname**: Dies stellt Ihre Gruppe von Anwendungen dar. Es wird außerdem sichergestellt, dass alle Ihre Apps sich in einer Gruppe befinden, die Ihnen aggregierte Berechnungen ermöglicht. Es wird dringend empfohlen, den Firmennamen oder die Abteilung zu verwenden.


	Wenn Sie fertig sind, klicken Sie auf das Häkchen, um die Erstellung Ihrer App zu beenden.

4. Wählen Sie jetzt auf der Registerkarte **Anwendung** die soeben erstellte App durch Klicken aus.
 
   	![][9]

5. Klicken Sie anschließend auf **Verbindungsinformationen**, um die Verbindungseinstellungen anzuzeigen, die in Ihre SDK-Integration aufgenommen werden sollen.
 
   	![][10]

6. Notieren Sie abschließend die **Verbindungszeichenfolge**, die Sie zum Identifizieren dieser App über den Anwendungscode benötigen.

   	![][11]

	>[AZURE.TIP] Mit dem Symbol "Kopieren" rechts neben der Verbindungszeichenfolge können Sie diese benutzerfreundlich in die Zwischenablage kopieren.

## <a id="connecting-app"></a>Verbinden Ihrer App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine "Basisintegration" gezeigt, also die minimal erforderliche Einrichtung zum Sammeln von Daten und zum Senden einer Pushbenachrichtigung. Die vollständige Integrationsdokumentation finden Sie in der [Dokumentation zum Mobile Engagement Windows Phone SDK].

Wir erstellen eine einfache App mit Visual Studio, um die Integration zu veranschaulichen.

### Erstellen eines neuen Windows Phone-Projekts

Wenn Sie bereits über eine App verfügen und mit der Windows Phone-Entwicklung vertraut sind, können Sie diesen Schritt überspringen.

1. Starten Sie Visual Studio, und wählen Sie im Startbildschirm **Neues Projekt**.

   	![][12]

2. (1) Wählen Sie im Popupfenster  `Windows Phone`. (2) Wählen Sie dann  `Windows Phone-App`. (3) Geben Sie unter  `Name` den Namen der App und (4) unter  `Lösungsname` den Lösungsnamen ein, und klicken Sie dann auf **OK**.

   	![][13]

Das Projekt wird jetzt mit der Demo-App erstellt, in die wir Mobile Engagement integrieren werden.

### Einbeziehen der SDK-Bibliothek in Ihr Projekt

Laden Sie die SDK-Bibliothek herunter, und integrieren Sie sie.

1. Laden Sie das [Mobile Engagement Windows Phone SDK] herunter.
2. Extrahieren Sie die ".tar.gz"-Datei in einen Ordner auf Ihrem Computer.
3. Wechseln Sie zu  `PROJEKT` und dann zu `NuGet-Pakete verwalten`.
4. Klicken Sie im Popupfenster auf "Einstellungen".
5. Drücken Sie dann die `+`-Taste, um eine neue Quelle zu erstellen.
	![][17]

6. Klicken Sie auf die Schaltfläche `...` im unteren Bereich, um die Quelle auszuwählen, navigieren Sie zum Ordner "lib" des extrahierten SDK-Downloads (siehe Schritt 2), und wählen Sie dann `Auswählen`.
	![][18]

7. Das SDK wird als Quelle hinzugefügt. Klicken Sie einfach auf `OK`.
	![][19]


### Verbinden der App mit dem Mobile Engagement-Back-End über die Verbindungszeichenfolge

1. Das Engagement-SDK benötigt einige Funktionen des Windows Phone-SDK, damit es einwandfrei funktioniert.
Öffnen Sie die Datei "WMAppManifest.xml", und stellen Sie sicher, dass die folgenden Funktionen im Bereich "Capabilities" deklariert werden:
	- ID_CAP_NETWORKING
	- ID_CAP_IDENTITY_DEVICE


	![][20]

2. Wechseln Sie zurück zum Azure-Portal auf die Seite **Verbindungsinformationen** Ihrer App, und kopieren Sie die **Verbindungszeichenfolge**.

	![][11]

3. Fügen Sie sie in die Datei `Resources\EngagementConfiguration.xml` zwischen den Tags `<connectionString>` und `</connectionString>` ein:

	![][21]

4. Gehen Sie in der Datei `App.xaml.cs` folgendermaßen vor:
	1. Fügen Sie die `using`-Anweisung hinzu.

			using Microsoft.Azure.Engagement;

	2. Initialisieren Sie das SDK in der Methode `Application_Launching`:
			
			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	3. Fügen Sie in `Application_Activated` Folgendes ein.

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

### Senden eines Bildschirms an Mobile Engagement

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End senden. Dies erreichen wir durch die Erstellung von Unterklassen für unsere `MainPage` mit der `EngagementPage`, die vom Mobile Engagement-SDK bereitgestellt wird.

1. Hierzu ersetzen Sie die übergeordnete Klasse von `MainPage`, die sich vor `PhoneApplicationPage` befindet, durch "EngagementPage":

	![][22]

	>[AZURE.NOTE] Vergessen Sie nicht, die Klasse aufzulösen, wenn sie rot unterstrichen angezeigt wird. Fügen Sie hierzu `using Microsoft.Azure.Engagement;` den `using`-Klauseln hinzu.

2. In der Datei `MainPage.xml`:
	1. Fügen Sie Ihre Namespace-Deklarationen hinzu:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
	2. Ersetzen Sie die `phone:PhoneApplicationPage` im XML-Tagnamen durch `engagement:EngagementPage`

## <a id="monitor"></a>Überprüfen der Verbindung Ihrer App mit der Echtzeitüberwachung

In diesem Abschnitt wird gezeigt, wie Sie sicherstellen, dass Ihre App über die Mobile Engagement-Funktion zur Echtzeitüberwachung eine Verbindung zum Mobile Engagement-Back-End herstellt.

1. Navigieren Sie zu Ihrem Mobile Engagement-Portal.

	Stellen Sie über Ihr Azure-Portal sicher, dass Sie sich in der für dieses Projekt verwendeten App befinden, und klicken Sie dann unten auf die Schaltfläche **Erfassen**.

	![][26]

2. Sie gelangen zur Einstellungsseite Ihres Engagement-Portals für Ihre App. Klicken Sie dort, wie unten gezeigt, auf die Registerkarte **Überwachen**.
	![][30]

3. Der Monitor zeigt Ihnen alle Geräte in Echtzeit an, auf denen Ihre App gestartet wird.
	![][31]

4. Starten Sie in Visual Studio Ihre App entweder im Emulator oder auf einem angeschlossenen Gerät, indem Sie auf das grüne Dreieck klicken und dann Ihr Gerät auswählen.

5. Wenn dies funktioniert hat, wird jetzt im Monitor eine Sitzung angezeigt! 
	![][33]

**Herzlichen Glückwunsch!** Sie haben den ersten Schritt dieses Lernprogramms erfolgreich mit einer App abgeschlossen, die eine Verbindung mit dem Mobile Service-Back-End herstellt, welches bereits Daten sendet.


## <a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mobile Engagement ermöglicht Ihnen die Interaktion und das Erreichen Ihrer Benutzer durch Pushbenachrichtigungen und In-App-Messaging im Rahmen von Kampagnen. Dieses Modul wird im Mobile Engagement-Portal als REACH bezeichnet.
In den folgenden Abschnitten wird Ihre App so eingerichtet, dass diese Nachrichten erhalten werden.

### Aktivieren des Empfangs von MPNS-Pushbenachrichtigungen in Ihrer App

Fügen Sie Ihrer Datei `WMAppManifest.xml` unter "Capabilities" neue Funktionen hinzu:

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

### Initialisieren des REACH-SDK

1. Rufen Sie in `App.xaml.cs` gleich nach der Agent-Initialisierung `EngagementReach.Instance.Init();`  in der Funktion `Application_Launching` auf:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. Rufen Sie in `App.xaml.cs` gleich nach der Agent-Initialisierung `EngagementReach.Instance.OnActivated(e);`  in der Funktion `Application_Activated` auf:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Fertig. Jetzt stellen wir sicher, dass diese Basisintegration richtig durchgeführt wurde.

## <a id="send"></a>Senden einer Benachrichtigung an Ihre App

Jetzt erstellen wir eine einfache Pushbenachrichtigungskampagne, die eine Pushbenachrichtigung an unsere App sendet.

1. Navigieren Sie in Ihrem Mobile Engagement-Portal zur Registerkarte **REACH**.
2. Klicken Sie auf **Neue Ankündigung**, um die Pushkampagne zu erstellen.
	![][35]

3. Richten Sie das erste Feld Ihrer Kampagne anhand der folgenden Schritte ein:
	![][36]

	1. Benennen Sie Ihre Kampagne mit einem beliebigen Namen.
	2. Legen Sie **Übermittlungszeit** auf *Any time* fest, damit die App auch dann eine Benachrichtigung erhält, wenn sie nicht gestartet wurde.
	3. Geben Sie im Benachrichtigungstext den Titel ein, der in der Pushnachricht in Fettformatierung angezeigt wird.
	4. Geben Sie anschließend Ihre Nachricht ein.

4. Führen Sie einen Bildlauf nach unten durch, und wählen Sie im Inhaltsabschnitt **Nur Benachrichtigung** aus.
![][37]

5. Sie haben eine grundlegende Kampagne festgelegt. Führen Sie nun erneut einen Bildlauf nach unten durch, und erstellen Sie Ihre Kampagne, um sie zu speichern.
![][38]

6. Als letzten Schritt aktivieren Sie die Kampagne.
![][39]

Daraufhin sollte eine Benachrichtigung auf Ihrem Gerät angezeigt werden, **Herzlichen Glückwunsch!**:
![][40]


<!-- URLs. -->
[Mobile Engagement Windows Phone SDK]: http://go.microsoft.com/?linkid=9874664

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-windows-phone-get-started/select-app.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-phone-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-phone-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-phone-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-phone-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-create.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!--HONumber=47-->
