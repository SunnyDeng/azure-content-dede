<properties 
	pageTitle="Windows Universal-Apps Reach SDK-Integration" 
	description="Integrieren von Azure Mobile Engagement Reach in Windows Universal-Apps"
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
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="piyushjo" />

#Windows Universal-Apps Reach SDK-Integration

Bevor Sie dieser Anleitung folgen, müssen Sie das unter [Integration des Windows Universal Engagement-SDK](mobile-engagement-windows-store-integrate-engagement.md) beschriebene Integrationsverfahren befolgen.

##Einbetten des Engagement Reach-SDK in das Windows Universal-Projekt

Sie müssen nichts hinzufügen. `EngagementReach`-Referenzen und -Ressourcen sind bereits in Ihrem Projekt enthalten.

> [AZURE.TIP]Sie können die Bilder anpassen, die sich im Ordner `Resources` Ihres Projekts befinden, insbesondere das Markensymbol (standardmäßig das Engagement-Symbol). In Universal-Apps können Sie auch den Ordner `Resources` in ihr freigegebenes Projekt bewegen, um den Inhalt zwischen den Anwendungen zu teilen. Sie müssen allerdings die Datei `Resources\EngagementConfiguration.xml` im Standardverzeichnis liegen lassen, da sie plattformabhängig ist.

##Aktivieren des Windows-Benachrichtigungsdienstes

Um den **Windows-Benachrichtigungsdienst** (auch als WNS, Windows Notification Service, bezeichnet) in Ihrer `Package.appxmanifest`-Datei in `Application UI` zu verwenden, klicken Sie auf `All Image Assets` im linken Feld. Rechts neben dem Feld i `Notifications`, ändern Sie `toast capable` von `(not set)` zu `Yes`.

Darüber hinaus müssen Sie Ihre App mit Ihrem Microsoft-Konto und der Engagement-Plattform synchronisieren. Gehen Sie im Engagement-Front-End zur Ihren App-Einstellungen unter `native push` und fügen Sie Ihre Anmeldedaten ein. Danach klicken Sie mit der rechten Maustaste auf das Projekt, wählen `store` und `Associate App with the Store...`.

##Initialisieren des Engagement-Reichweiten-SDK

Ändern Sie `App.xaml.cs`:

-   Fügen Sie Ihre Anweisungen `using` hinzu:

		using Microsoft.Azure.Engagement;

-   Fügen Sie `EngagementReach.Instance.Init` direkt nach `EngagementAgent.Instance.Init` in `OnLaunched` hinzu:

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		  EngagementReach.Instance.Init(args);
		}

-   Wenn Sie bei aktivierter App Engagement-Reichweite starten möchten, überschreiben Sie die `OnActivated`-Methode:

		protected override void OnActivated(IActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		  EngagementReach.Instance.Init(args);
		}

	Die `EngagementReach.Instance.Init` wird in einem dedizierten Thread ausgeführt. Sie müssen es nicht selbst ausführen.

> [AZURE.TIP]Sie können den Namen des WNS-Push-Kanals Ihrer Anwendung in der `Resources\EngagementConfiguration.xml`-Datei Ihres Projekts in `<channelName></channelName>` angeben. Standardmäßig erstellt Engagement einen Namen basierend auf der AppID. Sie müssen nicht selbst den Namen angeben, außer wenn Sie planen, den Push-Kanal außerhalb von Engagement zu verwenden.

##Integration

Engagement bietet zwei Möglichkeiten zur Implementierung von Reach-Benachrichtigungen und -Ankündigungen: die Overlay-Integration und die Webansicht-Integration.

Windows-sdk-engagement-overlay-integration erfordert nicht viel Code für Ihre Anwendung. Sie müssen lediglich Ihre Seiten, XAML- und CS-Dateien mit EngagementPageOverlay kennzeichnen. Wenn Sie außerdem die Standardansicht von Engagement anpassen, wird diese Anpassung mit allen gekennzeichneten Seiten geteilt und nur einmal definiert. Wenn Ihre Seiten jedoch von einem anderen Objekt als EngagementPageOverlay erben müssen, müssen Sie die Webansicht-Integration verwenden.

windows-sdk-engagement-webview-integration ist komplizierter zu implementieren. Aber wenn Ihre App-Seiten von einem anderen Objekt als „Seite“ geerbt werden, müssen Sie die Webansicht und ihr Verhalten integrieren.

> [AZURE.TIP]Sie sollten zunächst ein `<Grid></Grid>`-Element auf erster Ebene hinzufügen, um allen Seiteninhalt zu umgeben. Fügen Sie zur Webansicht-Integration einfach Webansicht als untergeordnetes Element dieses Rasters. Wenn die Engagement-Komponente an anderer Stelle eingerichtet werden muss, denken Sie daran, dass Sie die Anzeigengröße selbst einstellen müssen.

### Überlagerungsintegration

Engagement bietet eine Überlagerung für die Anzeige von Benachrichtigungen und Ankündigungen.

Wenn Sie Engagement verwenden möchten, verwenden Sie nicht windows-sdk-engagement-webview-integration.

Ändern Sie in der XAML-Datei den EngagementPage-Verweis auf EngagementPageOverlay.

-   Fügen Sie Ihre Namespace-Deklarationen hinzu:

			xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Ersetzen Sie `engagement:EngagementPage` durch `engagement:EngagementPageOverlay`:

**Mit EngagementPage:**

		<engagement:EngagementPage 
		    xmlns:engagement="using:Microsoft.Azure.Engagement">
		
		    <!-- layout -->
		</engagement:EngagementPage>

**Mit EngagementPageOverlay:**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		
		    <!-- layout -->
		</engagement:EngagementPageOverlay>

> **Mit EngagementPageOverlay für 8.1:**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		    <Grid>
		      <!-- layout -->
		    </Grid>
		</engagement:EngagementPageOverlay>

Kennzeichnen Sie dann in der CS-Datei Ihre Seite in „EngagementPageOverlay“ anstatt in „EngagementPage“ und importieren Sie „Microsoft.Azure.Engagement.Overlay“.

			using Microsoft.Azure.Engagement.Overlay;

-   Ersetzen Sie `EngagementPage` durch `EngagementPageOverlay`:

**Mit EngagementPage:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage
			  {
			    [...]
			  }
			}

**Mit EngagementPageOverlay:**

			using Microsoft.Azure.Engagement.Overlay;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPageOverlay 
			  {
			    [...]
			  }
			}

Jetzt verwendet diese Seite den Engagement-Überlagerungsmechanismus, Sie müssen keine Webansicht einfügen.

Die Engagement-Überlagerung verwendet das erste „Raster“-Element, das sie in der XAML-Datei findet, um Ihrer Seite zwei Webansichten hinzuzufügen. Wenn Sie nach der Stelle suchen möchten, an der Webansichten eingestellt werden, so können Sie ein Raster mit der Bezeichnung „EngagementGrid“ wie folgt definieren:

			<Grid x:Name="EngagementGrid"></Grid>

Sie können die Überlagerungsbenachrichtigung und Ankündigung direkt in ihren XAML- und CS-Dateien anpassen:

-   `EngagementAnnouncement.html` : Der HTML-Entwurf der `Announcement`-Webansicht
-   `EngagementOverlayAnnouncement.xaml` : Der xaml-Entwurf für `Announcement`
-   `EngagementOverlayAnnouncement.xaml.cs` : Der mit `EngagementOverlayAnnouncement.xaml` verknüpfte Code
-   `EngagementNotification.html` : Der HTML-Entwurf der `Notification`-Webansicht
-   `EngagementOverlayNotification.xaml` : Der xaml-Entwurf für `Notification`
-   `EngagementOverlayNotification.xaml.cs` : Der mit `EngagementOverlayNotification.xaml` verknüpfte Code
-   `EngagementPageOverlay.cs` : Der Anzeigecode der `Overlay`-Ankündigung und -Benachrichtigung.

### Webansicht-Integration

Wenn Sie diese verwenden möchten, verwenden Sie nicht windows-sdk-engagement-overlay-integration.

Zum Anzeigen des Engagement-Inhalts müssen Sie die beiden XAML-Webansichten auf jeder Seite integrieren und Benachrichtigungen und Ankündigungen anzeigen. Also fügen Sie diesen Code in Ihrer XAML-Datei hinzu:

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **Zur 8.1-Integration:**

			<engagement:EngagementPage
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <Grid>
			      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 
			      <!-- layout -->
			    </Grid>
			</engagement:EngagementPage>

Die zugeordnete CS-Datei muss so aussehen:

			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			   /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			
			    Window.Current.SizeChanged += DisplayProperties_OrientationChanged;
			  }
			
			  #region to implement
			  /* Allow webview script to notify system */
			  private void scriptEvent(object sender, NotifyEventArgs e)
			  {
			  }
			
			  /* When page is left ensure to detach SizeChanged handler */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
			    base.OnNavigatedFrom(e);
			  }
			
			  /* "width" is the current width of your application display */
			  double width = Window.Current.Bounds.Width;
			
			  /* "height" is the current height of your application display */
			  double height = Window.Current.Bounds.Height;
			
			  /// <summary>
			  /// Set your webview elements to the correct size
			  /// </summary>
			  /// <param name="width">The width of your current display</param>
			  /// <param name="height">The height of your current display</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that take the Windows.Current.SizeChanged and indicate that webview have to be resized
			  /// </summary>
			  /// <param name="sender">Original event trigger</param>
			  /// <param name="e">Window Size Changed Event argument</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			  }
			  #endregion
			}

> Diese in der Implementierung eingebettete Webansicht ändert ihre Größe, wenn der Gerätebildschirm gedreht wird.

##Behandeln von Datapush (optional)

Wenn Sie Ihre Anwendung Reichweitendaten per Push empfangen soll, müssen Sie zwei Ereignisse der EngagementReach-Klasse implementieren:

Fügen Sie in "App.xaml.cs" in "Public App(){}" Folgendes hinzu:

			EngagementReach.Instance.DataPushStringReceived += (body) =>
			{
			  Debug.WriteLine("String data push message received: " + body);
			  return true;
			};
			
			EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
			{
			  Debug.WriteLine("Base64 data push message received: " + encodedBody);
			  // Do something useful with decodedBody like updating an image view
			  return true;
			};

Sie sehen, dass der Rückruf jeder Methode einen booleschen Wert zurückgibt. Engagement sendet ein Feedback an sein Back-End nach der Verteilung der Daten per Push. Wenn der Rückruf "false" zurückgibt, wird das Feedback `exit` gesendet. Andernfalls lautet es `action`. Wird für die Ereignisse kein Rückruf festgelegt, wird das Feedback `drop` an Engagement zurückgegeben.

> [AZURE.WARNING]Engagement kann nicht mehrere Feedbacks für einen Daten-Push-Vorgang empfangen. Wenn Sie beabsichtigen, mehrere Handler für ein Ereignis festzulegen, beachten Sie, dass das Feedback dem letzten gesendeten Handler entspricht. In diesem Fall empfehlen wir, immer denselben Wert zurückzugeben, um verwirrendes Feedback vom Front-End zu vermeiden.

##Anpassen der Benutzeroberfläche (optional)

### Erster Schritt

Wir ermöglichen Ihnen, die Reichweiten-Benutzeroberfläche anzupassen.

Hierzu müssen Sie eine Unterklasse der `EngagementReachHandler`-Klasse erstellen.

**Beispielcode:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  internal class ExampleReachHandler : EngagementReachHandler
			  {
			   // Override EngagementReachHandler methods depending on your needs
			  }
			}

Legen Sie dann den Inhalt des `EngagementReach.Instance.Handler`-Feldes mit Ihrem benutzerdefinierten Objekt in der `App.xaml.cs`-Klasse mit der `App()`-Methode fest.

**Beispielcode:**

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  // your app initialization 
			  EngagementReach.Instance.Handler = new ExampleReachHandler();
			  // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE]Engagement verwendet standardmäßig seine eigene Implementierung von `EngagementReachHandler`. Sie müssen keine eigene erstellen, und wenn Sie dies tun, müssen Sie nicht jede Methode außer Kraft setzen. Das Standardverhalten ist die Auswahl des Engagement-Basisobjekts.

### Webansicht

Standardmäßig verwendet die Reichweite die eingebetteten Ressourcen der DLL, um die Benachrichtigungen und Seiten anzuzeigen.

Um eine vollständige Anpassung zu ermöglichen, verwenden wir nur die Webansicht. Wenn Sie Layouts anpassen möchten, überschreiben Sie direkt die Ressourcendateien `EngagementAnnouncement.html` und `EngagementNotification.html`. Für Engagement muss sämtlicher Code in `<body></body>` vorliegen, um ordnungsgemäß ausgeführt zu werden. Aber Sie können einen Tag zu `engagement_webview_area` hinzufügen.

Sie können sich außerdem dafür entscheiden, Ihre eigenen Ressourcen zu verwenden.

Sie können `EngagementReachHandler`-Methoden in Ihrer Unterklasse überschreiben, damit Engagement Ihre Layouts verwendet. Achten Sie dabei jedoch auf den eingebetteten Engagement-Mechanismus:

**Beispielcode:**
			
			// In your subclass of EngagementReachHandler
			
			public override string GetAnnouncementHTML()
			{
			  return base.GetAnnouncementHTML();
			}
			public override string GetAnnouncementName()
			{
			  return base.GetAnnouncementName();
			}
			public override string GetNotfificationHTML()
			{
			  return base.GetNotfificationHTML();
			}
			public override string GetNotfificationName()
			{
			  return base.GetNotfificationName();
			}


Standardmäßig ist AnnouncementHTML `ms-appx-web:///Resources/EngagementAnnouncement.html`. Es stellt die HTML-Datei dar, die den Inhalt einer Push-Nachricht (Textankündigung, Webankündigung und Umfrageankündigung) entwirft. Der AnnouncementName lautet `engagement_announcement_content`. Es ist der Name des Webansicht-Entwurfs auf Ihrer XAML-Seite.

NotificationHTML lautet `ms-appx-web:///Resources/EngagementNotification.html`. Es stellt die HTML-Datei dar, die die Benachrichtigung einer Push-Nachricht entwirft. Der NotificationName lautet `engagement_notification_content`. Es ist der Name des Webansicht-Entwurfs auf Ihrer XAML-Seite.

### Anpassung

Sie können Benachrichtigungs- und Ankündigungs-Webansichten nach Belieben anpassen, wenn Sie das Engagement-Objekt beibehalten. Stellen Sie sicher, dass das Webansichtenobjekt drei Mal beschrieben wird: Das erste Mal in Ihrer XAML, das zweite Mal in Ihrer CS-Datei in der „setwebview()“-Methode und das dritte Mal in der HTML-Datei.

-   Beschreiben Sie in der XAML die aktuelle Webansicht-Komponente des grafischen Layouts.
-   In der CS-Datei können Sie „setwebview()“ definieren, in dem Sie die Dimension der beiden Webansichten (Benachrichtigung, Ankündigung) einstellen. Es ist sehr effektiv, wenn die Anwendung die Größe ändert.
-   In der Engagement-HTML-Datei beschreiben wir den Inhalt der Webansicht, das Design und die Elementpositionen untereinander.

### Starten der Nachricht

Klickt ein Benutzer auf eine Benachrichtigung des Systems (einen Toast), startet Engagement die Anwendung, lädt den Inhalt der Push-Nachrichten und zeigt die Seite der entsprechenden Kampagne an.

Es besteht eine Verzögerung zwischen dem Start der Anwendung und der Anzeige der Seite (je nach Geschwindigkeit Ihres Netzwerks).

Um dem Benutzer zu verdeutlichen, dass etwas geladen wird, sollten Sie eine visuelle Information, wie z. B. eine Statusleiste oder eine Fortschrittsanzeige, bieten. Engagement kann dies nicht selbst verarbeiten, stellt jedoch ein paar Handler zur Verfügung.

Fügen Sie zum Implementieren des Rückrufs in „App.Xaml.cs“ in „Public App(){}“ Folgendes hinzu:

			/* The application has launched and the content is loading.
			 * You should display an indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
			
			/* The application has finished loading the content and the page
			 * is about to be displayed.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
			
			/* The content has been loaded, but an error has occurred.
			 * You can provide an information to the user.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Sie können den Rückruf in Ihrer „Public App(){}“-Methode der `App.xaml.cs`-Datei einstellen, vorzugsweise vor dem `EngagementReach.Instance.Init()`-Aufruf.

> [AZURE.TIP]Jeder Handler wird vom UI-Thread aufgerufen. Sie müssen sich keine Sorgen machen, wenn Sie eine MessageBox oder ein Benutzeroberflächenelement verwenden.

##Tipp zu benutzerdefinierten Schemas

Wir stellen benutzerdefinierte Schemaverwendung zur Verfügung. Sie können einen anderen URI-Typ vom Engagement-Front-End senden, der in Ihrer Engagement-Anwendung verwendet werden soll. Standardschemas wie `http, ftp, ...` werden von Windows verwaltet und ein Fenster wird angezeigt, wenn keine Standardanwendung auf dem Gerät installiert ist. Es können andere Schemas, wie z. B. Anwendungsschemas, verwendet werden. Darüber hinaus können Sie ein benutzerdefiniertes Schema für Ihre Anwendung verwenden.

Die einfachste Möglichkeit, ein benutzerdefiniertes Schema in Ihrer Anwendung einzurichten, besteht darin, `Package.appxmanifest` im Bereich `Declarations` zu öffnen. Wählen Sie `Protocol` im Bildlauffeld „Verfügbare Deklarationen“ und fügen Sie es hinzu. Bearbeiten Sie das Feld `Name` mit dem gewünschten Namen für das neue Protokoll.

Um dieses Protokoll nun zu verwenden, bearbeiten Sie `App.xaml.cs` mit der `OnActivated`-Methode und initialisieren Sie auch hier Engagement:

			/// <summary>
			/// Enter point when app his called by another way than user click
			/// </summary>
			/// <param name="args">Activation args</param>
			protected override void OnActivated(IActivatedEventArgs args)
			{
			  /* Init engagement like it was launch by a custom uri scheme */
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			
			  //TODO design action to do when app is launch
			
			  #region Custom scheme use
			  if (args.Kind == ActivationKind.Protocol)
			  {
			    ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
			
			    if (myProtocol.Uri.Scheme.Equals("protocolName"))
			    {
			      string path = myProtocol.Uri.AbsolutePath;
			    }
			  }
			  #endregion

<!--HONumber=54--> 