<properties 
	pageTitle="Windows Store-SDK für Azure Mobile Engagement - Übersicht" 
	description="Neueste Updates und Verfahren für Windows Store-SDK für Azure Mobile Engagement" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store" class="current">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

# Integration von Engagement in Windows

Dieses Verfahren beschreibt die einfachste Möglichkeit zum Aktivieren der Engagement-Analyse- und Überwachungsfunktionen in Ihrer Windows-Anwendung.

Die folgenden Schritte sind ausreichend, um den Bericht von Protokollen zu aktivieren, die zur Berechnung aller Statistiken zu Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Informationen notwendig sind. Der Bericht von Protokollen, die zur Berechnung anderer Statistiken wie Ereignisse, Fehler und Aufträge erforderlich ist, muss manuell mithilfe der Engagement-API erfolgen (siehe  [So verwenden Sie die erweiterte Mobile Engagement API für Tags in Ihrer Windows Store-App](mobile-engagement-windows-store-use-engagement-api.md)), da diese Statistiken von der Anwendung abhängig sind.

## Unterstützte Versionen

Dieses Windows-SDK unterstützt nur:

-   Windows 8 C\-Store-Anwendung
-   Windows 8.1 C\-Store-Anwendung

## Einbetten des Engagement-SDK in Ihr Windows-Projekt

Betten Sie das Engagement-SDK mithilfe des NuGet-Paket-Managers für die Microsoft Entwicklungsplattform ein.

Das NuGet-Paket für Mobile Engagement ist noch nicht online verfügbar, daher müssen Sie unser SDK-Archiv herunterladen, es extrahieren und dann in Visual Studio mit der rechten Maustaste auf Ihre Projekt klicken, um zu "NuGet-Pakete verwalten", Einstellungen, zu gelangen und dort den Bibliotheksordner als neue Paketquelle hinzuzufügen.

> [AZURE.NOTE] NuGet ist der Hauptpaketmanager für die .NET-Projekte. Alles, was Sie zur Verwendung des Engagement-SDK brauchen, wird heruntergeladen, hinzugefügt und kopiert. Die Engagement Windows 8 C\-Metro-Anwendung verwendet Drittanbieter-Bibliotheken (Microsoft.Bcl.Build v1.0.10 und Microsoft.Bcl-Portabilitäspaket v1.1.3) für plattformübergreifende Portabilität. NuGet fordert Sie automatisch dazu auf, diese auch zu installieren. Weitere Informationen finden Sie auf der [NuGet-Website].

## Hinzufügen von Funktionen

Das Engagement-SDK benötigt einige Funktionen des Windows-SDK, damit es einwandfrei funktioniert.

Öffnen Sie die `Package.appxmanifest`-Datei und stellen Sie sicher, dass die folgenden Funktionen deklariert sind:

-   `Internet (Client)`

Gehen Sie in den `Declarations`-Bereich der Datei "Package.appxmanifest".

Wählen Sie in `Verfügbare Deklarationen` `Dateitypzuordnungen` aus und fügen Sie sie hinzu. Schreiben Sie im rechten Fenster in das Feld `Name` `engagement_log_file` und in das Feld "Dateityp" `.set`.

Wählen Sie dann in `Verfügbare Deklarationen` `Aktualisierung zwischengespeicherter Dateien`  und fügen Sie sie hinzu.

## Initialisieren des Engagement-SDK

### Engagement-Konfiguration

Die Engagement-Konfiguration ist in der `Resources\EngagementConfiguration.xml`-Datei des Projekts zentralisiert.

Bearbeiten Sie diese Datei, um Folgendes anzugeben:

-   Die Verbindungszeichenfolge der Anwendung zwischen den Tags `<connectionString>` und `<\connectionString>`.

Wenn Sie sie stattdessen zur Laufzeit angeben möchten, können Sie die folgende Methode vor der Engagement Agent-Initialisierung aufrufen:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

Die Verbindungszeichenfolge für die Anwendung wird im Azure-Portal angezeigt.

### Engagement-Initialisierung

Wenn Sie ein neues Projekt erstellen, wird eine `App.xaml.cs`-Datei erzeugt. Diese Klasse erbt von `Anwendung` und enthält viele wichtige Methoden. Sie wird auch zum Initialisieren des Engagement-SDK verwendet.

Ändern Sie die `App.xaml.cs`:

-   Fügen Sie Ihre `using` -Anweisungen hinzu:

			using Microsoft.Azure.Engagement;

-   Insert `EngagementAgent.Instance.Init` in the `OnLaunched` method:

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

-   Wenn die Anwendung mit einem benutzerdefinierten Schema gestartet wird, wird eine andere Anwendung oder die Befehlszeile mit der `OnActivated`-Methode aufgerufen. Außerdem müssen Sie den Engagement-Agent initiieren, wenn Ihre App aktiviert wird. Überschreiben Sie dazu die `OnActivated`-Methode:

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

> [AZURE.IMPORTANT] Wir raten dringend davon ab, die Engagement-Initialisierung an einer anderen Stelle der Anwendung hinzuzufügen.

## Grundlegende Berichterstellung

### Empfohlene Methode: Überladen Ihrer `Page`-Klassen

Um den Bericht über alle Protokolle zu aktivieren, die zum Berechnen von Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Statistiken durch Engagement erforderlich sind, können Sie einfach alle Ihre `Page` -Unterklassen von den `EngagementPage` -Klassen erben lassen.

Hier ist ein Beispiel für eine Seite Ihrer Anwendung. Das Gleiche gilt für alle Seiten der Anwendung.

#### C#-Quelldatei

Ändern Sie Ihre Seite `.xaml.cs`Datei:

-   Fügen Sie Ihre `using` -Anweisungen hinzu:

			using Microsoft.Azure.Engagement;

-   Ersetzen Sie `Page` durch `EngagementPage`:

**Ohne Engagement:**

			namespace Example
			{
			  public sealed partial class ExamplePage : Page
			  {
			    [...]
			  }
			}

**Mit Engagement:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.IMPORTANT] Wenn Ihre Seite die `OnNavigatedTo`-Methode überschreibt, rufen Sie unbedingt `base.OnNavigatedTo(e)` auf. Andernfalls wird die Aktivität nicht erfasst (`EngagementPage` ruft `StartActivity` innerhalb der `OnNavigatedTo`-Methode auf).

#### XAML-Datei

Ändern Sie Ihre Seite `.xaml`Datei:

-   Fügen Sie Ihre Namespace-Deklarationen hinzu:

			xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Ersetzen Sie `Page` durch `engagement:EngagementPage`:

**Ohne Engagement:**

			<Page>
			    <!-- layout -->
			    ...
			</Page>

**Mit Engagement:**

			<engagement:EngagementPage 
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <!-- layout -->
			    ...
			</engagement:EngagementPage >

#### Überschreiben Sie das Standardverhalten

Standardmäßig wird der Klassenname der Seite als der Name der Aktivität gemeldet, ohne Zusatz. Wenn die Klasse das Suffix "Page" verwendet, löscht Engagement auch dieses.

Wenn Sie das Standardverhalten für den Namen überschreiben möchten, fügen Sie Folgendes einfach Ihrem Code hinzu:

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			  /* your code */
			  return "new name";
			}

Wenn Sie einige zusätzliche Informationen mit Ihrer Aktivität zusammen melden möchten, können Sie dies Ihrem Code hinzufügen:

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			  /* your code */
			  return extra;
			}

Weitere Details zu den zusätzlichen Informationen finden Sie hier <windows-sdk-extras-parameters>.

Diese Methoden werden von innerhalb der `OnNavigatedTo`-Methode Ihrer Seite aufgerufen.

### Alternative Methode: Rufen Sie `StartActivity()` manuell auf

Wenn Sie Ihre `Page`-Klassen nicht überladen möchten oder können, können Sie stattdessen Ihre Aktivitäten durch direktes Aufrufen der  `EngagementAgent`-Methoden starten.

Es wird empfohlen,  `StartActivity` innerhalb Ihrer `OnNavigatedTo`- Methode der Seite aufzurufen.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] Das Windows-SDK ruft automatisch die  `EndActivity`-Methode auf, wenn die Anwendung geschlossen wird. Daher wird  *HIGHLY*empfohlen, die  `StartActivity`-Methode aufzurufen, sobald sich die Aktivität des Benutzers ändert, und die *NEVER*  `EndActivity`-Methode NIE aufzurufen, weil dadurch ein Beenden der aktuellen Sitzung erzwungen wird.

## Erweiterte Berichterstellung

Möglicherweise möchten Sie anwendungsspezifische Ereignisse, Fehler und Aufträge melden; verwenden Sie dazu die anderen Methoden in der  `EngagementAgent`-Klasse. Mit der Engagement-API können alle erweiterten Funktionen von Engagement verwendet werden.

Weitere Informationen finden Sie unter "Verwendung der Engagement-API unter Windows".

## Erweiterte Konfiguration

### Deaktivieren der automatischen Absturzberichtsfunktion

Sie können die automatische Absturzberichtsfunktion von Engagement deaktivieren. Wenn dann eine unbehandelte Ausnahme auftritt, handelt Engagement nicht.

> [AZURE.WARNING] Wenn Sie diese Funktion deaktivieren möchten, bedenken Sie, dass bei einem unbehandelten Absturz in Ihrer App Engagement den Absturz nicht sendet **UND** weder die Sitzung noch die Aufträge schließt.

Zum Deaktivieren der automatischen Absturzberichtsfunktion passen Sie Ihre Konfiguration an, je nachdem, wie Sie sie deklariert haben:

#### In der Datei  `EngagementConfiguration.xml`

Setzen Sie den Absturzbericht auf  `false` zwischen die Tags `<reportCrash>`  und `</reportCrash>`.

#### Im Objekt  `EngagementConfiguration` während der Laufzeit

Setzen Sie Absturzbericht auf "false" mithilfe des Objekts "EngagementConfiguration".

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Disable Engagement crash reporting. */
			engagementConfiguration.Agent.ReportCrash = false;

### Burst-Modus [Beta]

Standardmäßig meldet der Engagement-Dienst Protokolle in Echtzeit. Wenn Ihre Anwendung sehr häufig Protokolle meldet, ist es besser, die Protokolle zu puffern und sie dann gleichzeitig in regelmäßigen Zeitintervallen zu melden (dies wird als "Burst-Modus" bezeichnet).

Rufen Sie dazu die folgende Methode auf:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Das Argument ist ein Wert in **Millisekunden**. Immer wenn Sie die Protokollierung in Echtzeit erneut aktivieren möchten, rufen Sie die Methode ohne Parameter oder mit dem Wert 0 auf.

Der Burst-Modus verlängert leicht die Akkulaufzeit, wirkt sich jedoch auf den Engagement-Monitor aus: Die Dauer von allen Sitzungen und Aufträgen wird auf den Burst-Schwellenwert gerundet (folglich sind eventuell Sitzungen und Aufträge, die kürzer als der Burst-Schwellenwert sind, möglicherweise nicht sichtbar). Es wird empfohlen, einen Burst-Schwellenwert von höchstens 30000 (30 s) zu verwenden.

> [AZURE.WARNING] Der Burst-Schwellenwert kann nicht auf einen Wert unter 1 s konfiguriert werden. Wenn Sie dies versuchen, zeigt das SDK eine Ablaufverfolgung mit dem Fehler an und setzt sich automatisch auf den Standardwert, d. h. 0 s, zurück. Dadurch wird ausgelöst, dass das SDK die Protokolle in Echtzeit meldet.

[hier]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet-Website]:http://docs.nuget.org/docs/start-here/overview

<!--HONumber=47-->
