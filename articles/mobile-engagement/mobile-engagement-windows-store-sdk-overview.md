<properties 
	pageTitle="Windows Universal SDK – Übersicht" 
	description="Übersicht über das Windows Universal SDK für Azure Mobile Engagement" 									
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

#Übersicht über das Windows Universal SDK für Azure Mobile Engagement

Starten Sie hier, um sich näher über die Integration von Azure Mobile Engagement in eine universelle Windows-App zu informieren. Wenn Sie es gleich ausprobieren möchten, sollten Sie unbedingt das [15-Minuten-Lernprogramm](mobile-engagement-windows-store-dotnet-get-started.md) bearbeiten.

Klicken Sie, um den [SDK-Inhalt](mobile-engagement-windows-store-sdk-content.md) anzuzeigen

##Integrationsverfahren

1. Hier starten: [Integrieren von Mobile Engagement in eine universelle Windows-App](mobile-engagement-windows-store-integrate-engagement.md)

2. Für Benachrichtigungen: [Integration von Reach (Benachrichtigungen) in einer universellen Windows-App](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. Tag-Plan-Implementierung: [Verwenden der erweiterten Mobile Engagement-Tagging-API in einer universellen Windows-App](mobile-engagement-windows-store-use-engagement-api.md)

##Versionshinweise

###3\.2.0 (20.11.2015)

-   Unterstützung für universelle Windows 10-Plattform-Anwendungen hinzugefügt.
-   Freigabefunktion für Pushkanal hinzugefügt, um Kanalkonflikte zu beheben (jetzt kompatibel mit Azure Notification Hubs).
-   Absturz beim Anfordern der Geräte-ID direkt nach der Initialisierung behoben.
-   Verbesserungen bei Konsolenprotokollen.
-   Absturz beim Analysieren einiger nicht behandelter Ausnahmen behoben.

Eine frühere Version finden Sie unter [Vollständige Versionshinweise](mobile-engagement-windows-store-release-notes.md)

##Upgrade-Verfahren

Wenn Sie bereits eine ältere Version von Engagement in Ihre Anwendung integriert haben, müssen Sie die folgenden Punkte beim Aktualisieren des SDK beachten.

Möglicherweise müssen Sie mehrere Verfahren ausführen, wenn Sie mehrere SDK-Versionen übersprungen haben. Umfassende Informationen finden Sie unter [Upgrade-Verfahren](mobile-engagement-windows-store-upgrade-procedure.md) . Wenn Sie beispielsweise von 0.10.1 zu 0.11.0 migrieren, müssen Sie zunächst den Vorgang "von 0.9.0 zu 0.10.1" ausführen und anschließend den Vorgang "von 0.10.1 zu 0.11.0".

###Von 3.1.0 zu 3.2.0

#### Ressourcen
Dieser Schritt betrifft nur angepasste Ressourcen. Wenn Sie die Ressourcen, die vom SDK (Html, Bilder, Überlagerung) bereitgestellt werden, angepasst haben, müssen Sie diese vor dem Upgrade sichern und Ihre Anpassungen erneut auf die aktualisierten Ressourcen anwenden.

#### Webansicht-Integration
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

### Upgrade von älteren Versionen

Siehe [Upgrade-Verfahren](mobile-engagement-windows-store-upgrade-procedure/)

<!---HONumber=AcomDC_1125_2015-->