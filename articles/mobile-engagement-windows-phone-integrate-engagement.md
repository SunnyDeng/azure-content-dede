<properties 
	pageTitle="Integration des Azure Mobile Engagement Windows Phone SDK" 
	description="Integration von Engagement in Windows Phone" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
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


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

# Integration von Engagement in Windows Phone

Dieses Verfahren beschreibt die einfachste Möglichkeit zum Aktivieren der Engagement-Funktionen zur Analyse und Überwachung in Ihrer Windows Phone-Anwendung.

Die folgenden Schritte sind ausreichend zum Aktivieren des Berichts von Protokollen, die zum Berechnen aller Statistiken in Bezug auf Benutzer, Sitzungen, Aktivitäten, Abstürze und technische Statistiken erforderlich sind. Der Bericht von Protokollen, die zum Berechnen weiterer Statistiken wie Ereignisse, Fehler und Aufträge erforderlich sind, muss manuell über die Engagement-API ausgeführt werden (Informationen hierzu finden Sie unten unter [Verwenden der erweiterten Mobile Engagement-Tagging-API in Ihrer Windows Phone-App](mobile-engagement-windows-phone-use-engagement-api.md) unten), da diese Statistiken anwendungsabhängig sind.

## Unterstützte Versionen

Das Engagement Windows Phone SDK kann nur in Anwendungen für folgende Betriebssysteme eingebettet werden:

-   Windows Phone OS 8.0
-   Windows Phone OS 8.1 mit Silverlight

Das Engagement Windows Phone SDK ist mit Windows Phone 8.0- und 8.1-Geräten kompatibel.

## Einbetten des Engagement SDK in das Windows Phone-Projekt

Das NuGet-Paket für Mobile Engagement ist noch nicht online verfügbar, daher müssen Sie unser SDK-Archiv herunterladen und extrahieren. Klicken Sie dann in Visual Studio mit der rechten Maustaste auf Ihr Projekt, wählen Sie "NuGet-Pakete verwalten" und "Einstellungen", und fügen Sie den "lib"-Ordner als neue Paketquelle hinzu.


> [AZURE.IMPORTANT] NuGet ist der Haupt-Paket-Manager für die .net-Projekte. Alles, was Sie zur Verwendung des Engagement-SDK benötigen, wird von ihm heruntergeladen, hinzugefügt und kopiert. Unter WP 8 verwendet Engagement Bibliotheken von Drittanbietern (Microsoft.Bcl.Build v1.0.10 und Microsoft.Bcl Portability Pack v1.1.3) für plattformübergreifende Portabilität. NuGet fordert Sie automatisch auf, diese ebenfalls zu installieren. Weitere Informationen finden Sie auf der [NuGet-Website](http://docs.nuget.org/docs/start-here/overview).

## Hinzufügen von Funktionen

Das Engagement-SDK benötigt einige Funktionen des Windows Phone-SDK, damit es einwandfrei funktioniert.

Öffnen Sie die Datei `WMAppManifest.xml` und stellen Sie sicher, dass die folgenden Funktionen im Bereich`Capabilities` deklariert werden:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

## Initialisieren des Engagement-SDK

### Engagement-Konfiguration

Die Engagement-Konfiguration erfolgt zentral in der Datei `Resources\EngagementConfiguration.xml` Ihres Projekts.

Bearbeiten Sie diese Datei, um Folgendes anzugeben:

-   Die Verbindungszeichenfolge der Anwendung zwischen den Tags `<connectionString>` und `<\connectionString>`.

Wenn Sie diese stattdessen zur Laufzeit angeben möchten, können Sie vor der Initialisierung des Engagement-Agents die folgende Methode aufrufen:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

Die Verbindungszeichenfolge für die Anwendung wird im Azure-Portal angezeigt.

### Engagement-Initialisierung

Wenn Sie ein neues Projekt erstellen, wird eine Datei `App.xaml.cs` generiert. Diese Klasse erbt von `Application` und enthält viele wichtige Methoden. Sie wird außerdem zum Initialisieren des Engagement-SDK verwendet.

Ändern Sie `App.xaml.cs`:

-   Fügen Sie Anweisungen unter `using` hinzu:

			using Microsoft.Azure.Engagement;

-   Fügen Sie `EngagementAgent.Instance.Init` in die Methode `Application_Launching` ein:

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

-   Fügen Sie `EngagementAgent.Instance.OnActivated` in die Methode `Application_Activated` ein:

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

> [AZURE.WARNING] Es wird dringend davon abgeraten, die Engagement-Initialisierung an einer anderen Stelle in der Anwendung hinzufügen. Bedenken Sie jedoch, dass die Methode `EngagementAgent.Instance.Init`in einem dedizierten Thread und nicht im UI-Thread ausgeführt wird.

## Grundlegende Berichterstellung

### Empfohlene Methode: Überladen der `PhoneApplicationPage`-Klassen

Um den Bericht über alle Protokolle zu aktivieren, die Engagement zum Berechnen von Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Statistiken benötigt, können Sie einfach festlegen, dass alle `PhoneApplicationPage`-Unterklassen von den `EngagementPage`-Klassen erben.

Das folgende Beispiel zeigt die Vorgehensweise für eine Seite Ihrer Anwendung. Dasselbe Verfahren gilt für alle Seiten Ihrer Anwendung.

#### C#-Quelldatei

Ändern Sie die Datei `.xaml.cs` Ihrer Seite:

-   Fügen Sie den `using`-Anweisungen Folgendes hinzu:

			using Microsoft.Azure.Engagement;

-   Ersetzen Sie `PhoneApplicationPage` durch `EngagementPage`:

**Ohne Engagement:**

			namespace Example
			{
			  public partial class ExamplePage : PhoneApplicationPage
			  {
			    [...]
			  }
			}

**Mit Engagement:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.WARNING] Wenn Ihre Seite von der Methode `OnNavigatedTo` erbt, stellen Sie den Aufruf von `base.OnNavigatedTo(e)` sicher. Andernfalls wird die Aktivität nicht berichtet. Tatsächlich wird `StartActivity` von `EngagementPage` innerhalb der Methode `OnNavigatedTo` aufgerufen.

#### XAML-Datei

Ändern Sie die Datei `.xaml` Ihrer Seite:

-   Fügen Sie Ihren Namespace-Deklarationen Folgendes hinzu:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Ersetzen Sie `phone:PhoneApplicationPage` durch `engagement:EngagementPage`:

**Ohne Engagement:**

			<phone:PhoneApplicationPage>
			    <!-- layout -->
			</phone:PhoneApplicationPage>

**Mit Engagement:**

			<engagement:EngagementPage 
			    xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
			
			    <!-- layout -->
			</engagement:EngagementPage >

#### Außerkraftsetzen des Standardverhaltens

Standardmäßig wird der Klassenname der Seite als der Name der Aktivität ohne Zusatzangaben angegeben. Wenn die Klasse das Suffix "Page" verwendet, wird dieses von Engagement ebenfalls entfernt.

Wenn Sie das Standardverhalten für den Namen außer Kraft setzen möchten, fügen Sie dem Code einfach Folgendes hinzu:

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			   /* your code */
			   return "new name";
			}

Wenn Sie einige zusätzliche Informationen zusammen mit der Aktivität melden möchten, können Sie dem Code Folgendes hinzufügen:

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			   /* your code */
			   return extra;
			}

Weitere Informationen zu den Zusatzangaben finden Sie [hier](../mobile-engagement-windows-phone-use-engagement-api/#extras-parameters).

Diese Methoden werden innerhalb der Methode `OnNavigatedTo` Ihrer Seite aufgerufen.

### Alternative Methode: Manuelles Aufrufen von `StartActivity()`

Wenn Sie Ihre `PhoneApplicationPage`-Klassen nicht überladen können oder möchten, starten Sie stattdessen Ihre Aktivitäten durch den direkten Aufruf von `EngagementAgent`-Methoden.

Es empfiehlt sich, `StartActivity` innerhalb Ihrer Methode `OnNavigatedTo` von PhoneApplicationPage aufzurufen.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			   base.OnNavigatedTo(e);
			   EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] Stellen Sie sicher, dass Ihre Sitzung ordnungsgemäß beendet wird.
>
> Das Windows Phone SDK ruft automatisch die Methode `EndActivity` auf, wenn die Anwendung geschlossen wird. Daher wird **dringend** empfohlen, bei jeder Änderung der Benutzeraktivität die Methode `StartActivity` und **niemals** die Methode `EndActivity` aufzurufen, da hierdurch die Beendigung der aktuellen Sitzung erzwungen wird.

## Erweiterte Berichterstellung

Optional können Sie besondere Anwendungsereignisse, Fehler und Aufträge in den Bericht aufnehmen. Verwenden Sie zu diesem Zweck die anderen Methoden in der `EngagementAgent`-Klasse. Die Engagement-API ermöglicht die Verwendung aller erweiterten Funktionen von Engagement.

Weitere Informationen finden Sie unter [Verwenden der erweiterten Mobile Engagement-Tagging-API in Ihrer Windows Phone-App](mobile-engagement-windows-phone-use-the-engagement-api.md).

## Erweiterte Konfiguration

### Deaktivieren der automatischen Berichte zu Abstürzen

Sie können die Engagement-Funktion zur automatischen Berichterstattung bei Abstürzen deaktivieren. Wenn eine nicht behandelte Ausnahme erfolgt, leitet Engagement keine Schritte ein.

> [AZURE.WARNING] Wenn Sie diese Funktion deaktivieren möchten, bedenken Sie, dass Engagement Ihnen bei Auftreten eines nicht behandelten Absturzes der App weder eine Meldung zum Absturz sendet **NOCH** die Sitzung und Aufträge schließt.

Zum Deaktivieren der automatischen Berichterstellung bei Abstürzen passen Sie Ihre Konfiguration abhängig davon an, wie Sie sie deklariert haben:

#### Über die Datei `EngagementConfiguration.xml`

Setzen Sie die Berichterstattung bei Abstürzen zwischen den Tags `<reportCrash>` und `</reportCrash>` auf `false`.

#### Über das Objekt `EngagementConfiguration` zur Laufzeit

Legen Sie die Berichterstattung bei Abstürzen über das Objekt "EngagementConfiguration" auf "false" fest.

			/* Engagement configuration. */

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			/* Disable Engagement crash reporting. */ engagementConfiguration.Agent.ReportCrash = false;

### Burstmodus [Beta]

Standardmäßig meldet der Engagement-Dienst die Protokolle in Echtzeit. Wenn Ihre Anwendung sehr häufig Protokolle meldet, ist es besser, die Protokolle zu puffern und regelmäßig alle auf einmal zu berichten. (Dies wird als "Burstmodus" bezeichnet.)

Rufen Sie hierzu folgende Methode auf:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Das Argument ist ein Wert in **Millisekunden**. Wenn Sie die Protokollierung in Echtzeit erneut aktivieren möchten, rufen Sie einfach die Methode ohne Parameter oder mit dem Wert 0 auf.

Der Burstmodus erhöht leicht die Akkulaufzeit, wirkt sich jedoch auf die Engagement-Überwachung aus: Die Dauer aller Sitzungen und Aufträge wird auf den Burstschwellenwert gerundet. (Sitzungen und Aufträge, die kürzer als der Burstschwellenwert sind, werden folglich möglicherweise nicht angezeigt.) Es wird empfohlen, einen Burstschwellenwert von nicht mehr als 30000 (30 Sek.) zu verwenden.

> [AZURE.WARNING] Der Burstschwellenwert kann nicht auf einen Zeitraum von weniger als einer Sekunde konfiguriert werden. Wenn Sie dies versuchen, zeigt das SDK eine Ablaufverfolgung mit einem Fehler an und setzt den Wert automatisch auf den Standardwert von 0 Sekunden zurück. Dadurch wird beim SDK die Protokollierung in Echtzeit ausgelöst.

<!--HONumber=47-->
