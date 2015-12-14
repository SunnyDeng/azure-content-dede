<properties 
	pageTitle="Windows Universal Apps SDK – Upgrade-Verfahren" 
	description="Windows Universal Apps SDK-Upgrade-Verfahren für Azure Mobile Engagement" 					
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
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Windows Universal Apps SDK – Upgrade-Verfahren

Wenn Sie bereits eine ältere Version von Engagement in Ihre Anwendung integriert haben, müssen Sie die folgenden Punkte beim Aktualisieren des SDK beachten.

Möglicherweise müssen Sie mehrere Verfahren befolgen, wenn Sie mehrere Versionen des SDK übersprungen haben. Wenn Sie beispielsweise von 0.10.1 zu 0.11.0 migrieren, müssen Sie zunächst den Vorgang "von 0.9.0 zu 0.10.1" ausführen und anschließend den Vorgang "von 0.10.1 zu 0.11.0".

##Von 3.1.0 zu 3.2.0

### Ressourcen
Dieser Schritt betrifft nur angepasste Ressourcen. Wenn Sie die Ressourcen, die vom SDK (Html, Bilder, Überlagerung) bereitgestellt werden, angepasst haben, müssen Sie diese vor dem Upgrade sichern und Ihre Anpassungen erneut auf die aktualisierten Ressourcen anwenden.

### Webansicht-Integration
In dieser Version wurden einige Verbesserungen für unterschiedliche Geräteausführungen eingeführt. Vergewissern Sie sich, dass die Integration der Webansicht folgende Kriterien erfüllt:

Auf der XAML-Seite ():

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

Und in der zugehörigen CS-Datei:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
			
			  #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

			  /* When page is left ensure to detach SizeChanged handler. */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
			    base.OnNavigatedFrom(e);
			  }
			  
			  /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
			  double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
			  double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
			  double width =  Window.Current.Bounds.Width;
			  double height =  Window.Current.Bounds.Height;
    #endif
			
			  /// <summary>
			  /// Set your webview elements to the correct size.
			  /// </summary>
			  /// <param name="width">The width of your current display.</param>
			  /// <param name="height">The height of your current display.</param>
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
			  /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
			  /// </summary>
			  /// <param name="sender">Original event trigger.</param>
			  /// <param name="e">Window Size Changed Event arguments.</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP			  
			  /// <summary>
			  /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
			  /// </summary>
			  /// <param name="sender">The related application view.</param>
			  /// <param name="e">Related event arguments.</param>
			  private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
			  {
			    double width = sender.VisibleBounds.Width;
			    double height = sender.VisibleBounds.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
    #endif
			  #endregion
			}
    }

##Von 2.0.0 zu 3.0.0

### Ressourcen
Dieser Schritt betrifft nur angepasste Ressourcen. Wenn Sie die Ressourcen, die vom SDK (Html, Bilder, Überlagerung) bereitgestellt werden, angepasst haben, müssen Sie diese vor dem Upgrade sichern und Ihre Anpassungen erneut auf die aktualisierten Ressourcen anwenden.

##Von 1.1.1 zu 2.0.0

Im Folgenden wird beschrieben, wie Sie die Migration einer SDK-Integration vom Capptain-Dienst, der von Capptain-SAS angeboten wird, in eine durch Azure Mobile Engagement unterstützte App durchführen.

> [Azure.IMPORTANT]Capptain und Mobile Engagement sind nicht dieselben Dienste, und die unten beschriebene Vorgehensweise hebt nur hervor, wie die Migration der Clientapp durchzuführen ist. Bei der Migration des SDK in die App werden Ihre Daten NICHT von den Capptain-Servern zu den Mobile Engagement-Servern migriert

Wenn Sie von einer früheren Version migrieren, ziehen Sie zunächst die Capptain-Website zurate, um zu 1.1.1 zu migrieren und wenden Sie dann das folgende Verfahren an

### NuGet-Paket

Ersetzen Sie **Capptain.WindowsPhone** durch das NuGet-Paket **MicrosoftAzure.MobileEngagement**.

### Anwenden von Mobile Engagement

Im SDK wird der Begriff `Engagement` verwendet. Sie müssen Ihr Projekt entsprechend dieser Änderung aktualisieren.

Sie müssen Ihr aktuelles Capptain-NuGet-Paket deinstallieren. Bedenken Sie, dass dadurch alle Änderungen, die Sie im Capptain-Ressourcenordner vorgenommen haben, entfernt werden. Wenn Sie diese Dateien beibehalten möchten, erstellen Sie eine Kopie davon.

Danach installieren Sie das neue Microsoft Azure Engagement-NuGet-Paket für Ihr Projekt. Sie finden es direkt auf der [NuGet-Website] oder im Index. Durch diese Aktion werden alle Ressourcendateien, die von Engagement verwendet werden, ersetzt und die neue Engagement-DLL Ihren Projektverweisen hinzugefügt.

Sie müssen Ihre Projektverweise bereinigen, indem Sie die Capptain-DLL-Verweise löschen. Wenn Sie dies nicht tun, kommt es zu Konflikten mit der Capptain-Version und Fehlern.

Wenn Sie Capptain-Ressourcen angepasst haben, kopieren Sie den Inhalt der alten Dateien, und fügen Sie ihn in die neuen Engagement-Dateien ein. Bitte beachten Sie, dass XAML- und CS-Dateien aktualisiert werden müssen.

Nach Abschluss dieser Schritte müssen Sie nur die alten Capptain-Verweise durch die neuen Engagement-Verweise ersetzen.

1. Alle Capptain-Namespaces müssen aktualisiert werden.

	Vor der Migration:
	
		using Capptain.Agent;
		using Capptain.Reach;
	
	Nach der Migration:
	
		using Microsoft.Azure.Engagement;

2. Alle Capptain-Klassen, die "Capptain" enthalten, sollten "Engagement" enthalten.

	Vor der Migration:
	
		public sealed partial class MainPage : CapptainPage
		{
		  protected override string GetCapptainPageName()
		  {
		    return "Capptain Demo";
		  }
		  ...
		}
	
	Nach der Migration:
	
		public sealed partial class MainPage : EngagementPage
		{
		  protected override string GetEngagementPageName()
		  {
		    return "Engagement Demo";
		  }
		  ...
		}

3. Für XAML-Dateien ändern sich ebenfalls Capptain-Namespace und Attribute.

	Vor der Migration:
	
		<capptain:CapptainPage
		...
		xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
		...
		</capptain:CapptainPage>
	
	Nach der Migration:
	
		<engagement:EngagementPage
		...
		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
		...
		</engagement:EngagementPage>

4. Änderungen der Überlagerungsseite
	> [AZURE.IMPORTANT]Die Überlagerung ändert sich ebenfalls. Der neue Namespace lautet `Microsoft.Azure.Engagement.Overlay`. Er muss in XAML- und CS-Dateien verwendet werden. Darüber hinaus muss `CapptainGrid` in `EngagementGrid` umbenannt werden, und `capptain_notification_content` und `capptain_announcement_content` müssen in `engagement_notification_content` bzw. `engagement_announcement_content` umbenannt werden.
	
	Überlagerung:
	
		<capptain:CapptainPageOverlay
		  xmlns:capptain="using:Capptain.Overlay"
		  ...
		</capptain:CapptainPageOverlay>
	
	Wird zu:
	
		<EngagementPageOverlay
		  engagement="using:Microsoft.Azure.Engagement.Overlay"
		  ...
		</engagement:EngagementPageOverlay>

5. Beachten Sie, dass andere Ressourcen wie Capptain-Bilder und HTML-Dateien auch für die Verwendung von "Engagement" umbenannt wurden.

### Projektdeklaration

In der Datei "Package.appxmanifest" wurde `File Type Associations` aktualisiert von:

 -   capptain\_reach\_content zu engagement\_reach\_content
 -   capptain\_log\_file zu engagement\_log\_file

### Anwendungs-ID/SDK-Schlüssel

Engagement verwendet eine Verbindungszeichenfolge. Sie müssen bei Mobile Engagement keine Anwendungs-ID und keinen SDK-Schlüssel angeben, sie müssen nur eine Verbindungszeichenfolge angeben. Sie können diese in der EngagementConfiguration-Datei einrichten.

Die Konfiguration von Engagement kann in der `Resources\EngagementConfiguration.xml`-Datei des Projekts festgelegt werden.

Bearbeiten Sie diese Datei, um Folgendes anzugeben:

-   Die Verbindungszeichenfolge der Anwendung zwischen den Tags `<connectionString>` und `<\connectionString>`.

Wenn Sie sie stattdessen zur Laufzeit angeben möchten, können Sie die folgende Methode vor der Initialisierung des Engagement-Agent aufrufen:

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
	
	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(args, engagementConfiguration);

Die Verbindungszeichenfolge für die Anwendung wird im klassischen Azure-Portal angezeigt.

### Namensänderung von Elementen

Alle Elemente namens *capptain* wurden in *engagement* umbenannt. Dasselbe gilt für die Umbenennung von *Capptain* in *Engagement*.

Beispiele für häufig verwendete Capptain-Elemente:

-   CapptainConfiguration heißt nun EngagementConfigurationCapptainConfiguration trägt nun den Namen EngagementConfiguration
-   CapptainAgent trägt nun den Namen EngagementAgent
-   CapptainReach trägt nun den Namen EngagementReach
-   CapptainHttpConfig trägt nun den Namen EngagementHttpConfig
-   GetCapptainPageName trägt nun den Namen GetEngagementPageName

Beachten Sie, dass sich die Umbenennung auch auf überschriebene Methoden auswirkt.

 

<!---HONumber=AcomDC_1203_2015-->