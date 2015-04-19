<properties 
	pageTitle="Integration des Azure Mobile Engagement Windows Phone SDK" 
	description="Integration von Engagement Reach in Windows Phone"				
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# Integration des Engagement Reach-SDK in Ihrem Windows Phone-Projekt

Bevor Sie die in diesem Leitfaden beschriebenen Schritte ausführen, müssen Sie das im Dokument [Integration von Engagement in Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md) beschriebene Verfahren ausführen.

## Einbetten des Engagement Reach-SDK in das Windows Phone-Projekt

Sie müssen nichts hinzufügen. `EngagementReach`-Referenzen und -Ressourcen sind bereits in Ihrem Projekt enthalten.

> [AZURE.TIP]  Sie können die Bilder anpassen, die sich im Ordner `Resources` Ihres Projekts befinden, insbesondere das Markensymbol (standardmäßig das Engagement-Symbol).

## Hinzufügen von Funktionen

Das Engagement Reach-SDK benötigt einige zusätzliche Funktionen.

Öffnen Sie die Datei `WMAppManifest.xml`, und stellen Sie sicher, dass die folgenden Funktionen im Bereich `Capabilities` deklariert werden:

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

## Aktivieren des Microsoft-Pushbenachrichtigungsdiensts

Um den **Microsoft-Pushbenachrichtigungsdienst** (auch Microsoft Push Notification Service, MPNS) zu verwenden, muss die Datei `WMAppManifest.xml` ein `<App />`-Tag aufweisen, dessen `Publisher`-Attribut auf den Namen Ihres Projekts eingestellt ist.

## Initialisieren des Engagement Reach-SDK

### Engagement-Konfiguration

Die Engagement-Konfiguration erfolgt zentral in der Datei `Resources\EngagementConfiguration.xml` Ihres Projekts.

Bearbeiten Sie diese Datei, um die Reach-Konfiguration festzulegen:

-   *Optional*: Geben Sie zwischen den Tags `<enableNativePush>` und `</enableNativePush>` an, ob systemeigene Pushbenachrichtigungen (MPNS) aktiviert sind (standardmäßig `true`).
-   *Optional*: Geben Sie zwischen den Tags `<channelName>` und `</channelName>` den Namen des Pushkanals an. Setzen Sie denselben ein, den Ihre Anwendung aktuell verwendet, oder lassen Sie den Eintrag leer.

Wenn Sie diese stattdessen zur Laufzeit angeben möchten, können Sie vor der Initialisierung des Engagement-Agents die folgende Methode aufrufen:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			engagementConfiguration.Reach.EnableNativePush = true;                  /* [Optional] whether the native push (MPNS) is activated or not. */
			engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   /* [Optional] Provide the same channel name that your application may currently use. */
			
			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] Sie können den Namen des MPNS-Pushkanals Ihrer Anwendung angeben. Standardmäßig erstellt Engagement einen Namen basierend auf der App-ID. Sie müssen den Namen nicht selbst angeben, sofern Sie nicht planen, den Pushkanal außerhalb von Engagement zu verwenden.

### Engagement-Initialisierung

Ändern Sie `App.xaml.cs`:

-   Fügen Sie Anweisungen unter `using` hinzu:

			using Microsoft.Azure.Engagement;

-   Fügen Sie `EngagementReach.Instance.Init` direkt nach `EngagementAgent.Instance.Init` in `Application_Launching` hinzu:

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			   EngagementAgent.Instance.Init();
			   EngagementReach.Instance.Init();
			}

-   Fügen Sie `EngagementReach.Instance.OnActivated` in die Methode `Application_Activated` ein:

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			   EngagementReach.Instance.OnActivated(e);
			}

> [AZURE.IMPORTANT]  `EngagementReach.Instance.Init` wird in einem dedizierten Thread ausgeführt. Dies müssen Sie nicht selbst durchführen.

Übermittlung
----------

Microsoft stellt einige Regeln für die Verwendung von Pushbenachrichtigungen auf.

Aus der Microsoft-Dokumentation zu [Anwendungsrichtlinien], Abschnitt 2.9:

> 2.9.1) Die Anwendung muss zunächst die Benachrichtigungen beschreiben, die bereitgestellt werden sollen, und **die ausdrückliche Zustimmung des Benutzers (Abonnieren) einholen** sowie **einen Mechanismus bereitstellen, über den der Benutzer den Empfang von Pushbenachrichtigungen abbestellen kann**. Alle Benachrichtigungen, die mithilfe des Microsoft-Pushbenachrichtigungsdiensts bereitgestellt werden, müssen der dem Benutzer bereitgestellten Beschreibung entsprechen und alle anwendbaren [Anwendungsrichtlinien], [Inhaltsrichtlinien] und [zusätzlichen Anforderungen für bestimmte Anwendungstypen] erfüllen.

**Kurz**: Sie müssen den Benutzer bitten, den Empfang von Pushbenachrichtigungen zu akzeptieren. Fügen Sie dann in den Einstellungen die Möglichkeit zum Deaktivieren von Pushbenachrichtigungen hinzu.

Das EngagementReach-Objekt bietet zwei Methoden zum Verwalten der Aktivierung/Deaktivierung, `EnableNativePush()` und `DisableNativePush()`. Beispielsweise könnten Sie in den Einstellungen eine Option mit einem Schalter zum Deaktivieren oder Aktivieren von MPNS erstellen.

Sie können MPNS auch über die Engagement-Konfiguration\<windows-phone-sdk-reach-configuration\> deaktivieren.

> 2.9.2) Durch die Anwendung und deren Nutzung des Microsoft-Pushbenachrichtigungsdiensts darf die Netzwerkkapazität oder Bandbreite des Microsoft-Pushbenachrichtigungsdiensts nicht übermäßig beansprucht oder ein Windows Phone, ein anderes Microsoft-Gerät oder ein Dienst nicht durch übermäßige Pushbenachrichtigungen über Gebühr belastet werden, wie von Microsoft nach eigenem Ermessen festgelegt. Durch die Anwendung und deren MPNS-Nutzung dürfen Microsoft-Netzwerke oder -Server bzw. Server von Drittanbietern oder Netzwerke, die mit dem Microsoft-Pushbenachrichtigungsdienst verbunden sind, nicht beschädigt oder behindert werden.

**Kurz**: Sie sollten nicht zu viele Pushbenachrichtigungen verwenden. Engagement kümmert sich darum.

> 2.9.3) Der Microsoft-Pushbenachrichtigungsdienst darf nicht zum Senden unternehmenskritischer oder anderer Benachrichtigungen verwendet werden, die über Leben und Tod entscheiden. Hierzu gehören uneingeschränkt auch entscheidende Benachrichtigungen im Zusammenhang mit einem medizinischen Gerät oder zustand. MICROSOFT SCHLIESST AUSDRÜCKLICH JEDE GEWÄHRLEISTUNG DAFÜR AUS, DASS DIE VERWENDUNG DES MICROSOFT-PUSHBENACHRICHTIGUNGSDIENSTS ODER DIE ÜBERMITTLUNG VON BENACHRICHTIGUNGEN MIT DEM MICROSOFT-PUSHBENACHRICHTIGUNGSDIENST UNTERBRECHUNGSFREI, FEHLERFREI ODER AUF ANDERE WEISE GARANTIERT IN ECHTZEIT ERFOLGT.

**Kurz**: Verlassen Sie sich beim Senden kritischer Informationen nicht auf MPNS. Engagement verwendet MPNS, daher gilt diese Regel auch für Kampagnen, die über das Engagement-Front-End erstellt werden.

**Wir können nicht dafür garantieren, dass die Anwendung den Überprüfungsprozess besteht, wenn Sie diese Empfehlungen nicht beachten.**

## Verwenden von Datenpushvorgängen (optional)

Wenn Ihre Anwendung Reach-Datenpushes empfangen soll, müssen Sie zwei Ereignisse der EngagementReach-Klasse implementieren:

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

Sie sehen, dass der Rückruf jeder Methode einen booleschen Wert zurückgibt. Engagement sendet nach dem Versand des Datenpushs ein Feedback an das Back-End. Wenn der Rückruf "false" zurückgibt, wird das Feedback `exit` gesendet. Andernfalls lautet es `action`. Wird für die Ereignisse kein Rückruf festgelegt, wird das Feedback `drop` an Engagement zurückgegeben.

> [AZURE.WARNING] Engagement kann für einen Datenpush nicht mehrere Feedbacks erhalten. Wenn Sie beabsichtigen, mehrere Handler für ein Ereignis festzulegen, beachten Sie, dass das Feedback dem zuletzt gesendeten entspricht. In diesem Fall wird empfohlen, immer denselben Wert zurückzugeben, damit am Front-End kein mehrdeutiges Feedback eintrifft.

## Anpassen der Benutzeroberfläche (optional)

### Erster Schritt

Sie können die Reach-Benutzeroberfläche anpassen.

Hierzu müssen Sie eine Unterklasse der Klasse `EngagementReachHandler` erstellen.

**Beispielcode:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			   internal class ExampleReachHandler : EngagementReachHandler
			   {
			      // Override EngagementReachHandler methods depending on your needs
			   }
			}

Legen Sie dann den Inhalt des Feldes `EngagementReach.Instance.Handler` mit Ihrem benutzerdefinierten Objekt in der Klasse `App.xaml.cs` innerhalb der Methode `Application_Launching` fest.

**Beispielcode:**

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			   // your app initialization 
			   EngagementReach.Instance.Handler = new ExampleReachHandler();
			   // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE] Engagement verwendet standardmäßig eine eigene Implementierung von `EngagementReachHandler`. Sie müssen keine eigene erstellen. Falls Sie dies jedoch möchten, müssen Sie nicht jede Methode außer Kraft setzen. Als Standardverhalten wird das Engagement-Basisobjekt ausgewählt.

### Layouts

Standardmäßig verwendet Reach die eingebetteten Ressourcen der DLL für die Anzeige der Benachrichtigungen und Seiten.

Sie können sich jedoch für die Verwendung Ihrer eigenen Ressourcen entscheiden, um Ihre Marke in diesen Komponenten darzustellen.

Sie können `EngagementReachHandler`-Methoden in der Unterklasse außer Kraft setzen, damit Engagement Ihre Layouts verwendet:

**Beispielcode:**

			// In your subclass of EngagementReachHandler
			
			public override string GetTextViewAnnouncementUri()
			{
			   // return the path of your own xaml
			}
			
			public override string GetWebViewAnnouncementUri()
			{
			   // return the path of your own xaml
			}
			
			public override string GetPollUri()
			{
			   // return the path of your own xaml
			}
			
			public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
			{
			   // return a new instance of your own notification
			}

> [AZURE.TIP] Die Methode `CreateNotification` kann null zurückgeben. Die Benachrichtigung wird nicht angezeigt, und die Reach-Kampagne wird gelöscht.

Zur Vereinfachung der Layoutimplementierung bieten wir auch eine eigene XAML, die als Grundlage für Ihren Code dienen kann. Sie befindet sich im Engagement-SDK-Archiv (/src/reach/).

> [AZURE.WARNING] Die bereitgestellten Quellen stimmen genau mit den von uns verwendeten überein. Wenn Sie sie also direkt ändern möchten, vergessen Sie nicht, den Namespace und den Namen zu ändern.

### Benachrichtigungsposition

Standardmäßig wird eine App-interne Benachrichtigung in der linken unteren Ecke der Anwendung angezeigt. Sie können dieses Verhalten ändern, indem Sie die Methode `GetNotificationPosition` des Objekts `EngagementReachHandler` außer Kraft setzen.

			// In your subclass of EngagementReachHandler
			
			public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
			{
			   // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
			}

Derzeit können Sie zwischen der Position `BOTTOM` (Standard) und `TOP` wählen.

### Starten der Nachricht

Klickt ein Benutzer auf eine Systembenachrichtigung (ein Popup), startet Engagement die App, lädt den Inhalt der Pushnachrichten und zeigt die Seite für die entsprechende Kampagne an.

Zwischen dem Start der Anwendung und der Anzeige der Seite gibt es eine Verzögerung (abhängig von der Geschwindigkeit des Netzwerks).

Um den Benutzer auf den Ladevorgang hinzuweisen, sollten Sie eine visuelle Information, z. B. eine Statusanzeige, bereitstellen. Engagement kann dies nicht selbst übernehmen, bietet jedoch einige Handler an.

So implementieren Sie den Rückruf:

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

Sie können den Rückruf in der `Application_Launching`-Methode der Datei `App.xaml.cs` festlegen, vorzugsweise vor dem Aufruf von `EngagementReach.Instance.Init()`.

> [AZURE.TIP] Jeder Handler wird vom UI-Thread aufgerufen. Bei Verwendung eines Nachrichtenfelds oder UI-bezogener Elemente müssen Sie sich keine Gedanken machen.


[Anwendungsrichtlinien]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Inhaltsrichtlinien]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Zusätzliche Anforderungen für bestimmte Anwendungstypen]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

<!--HONumber=47-->
