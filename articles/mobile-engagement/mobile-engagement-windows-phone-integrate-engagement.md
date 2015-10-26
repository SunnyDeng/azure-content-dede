<properties 
	pageTitle="Integration von Windows Phone Silverlight Engagement-SDK" 
	description="Integrieren von Azure Mobile Engagement in Windows Phone Silverlight-Apps" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#Integration von Windows Phone Silverlight Engagement-SDK

> [AZURE.SELECTOR] 
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Dieses Verfahren beschreibt die einfachste Möglichkeit zum Aktivieren der Azure Mobile Engagement-Funktionen zur Analyse und Überwachung in Ihrer Windows Phone Silverlight-Anwendung.

Die folgenden Schritte sind ausreichend, um den Bericht von Protokollen zu aktivieren, die zur Berechnung aller Statistiken zu Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Informationen notwendig sind. Der Bericht von Protokollen, die zum Berechnen weiterer Statistiken wie Ereignisse, Fehler und Aufträge erforderlich sind, muss manuell über die Engagement-API ausgeführt werden (Informationen hierzu finden Sie unten unter [Verwenden der erweiterten Mobile Engagement-Tagging-API in Ihrer Windows Phone Silverlight-App](mobile-engagement-windows-phone-use-engagement-api.md) unten), da diese Statistiken anwendungsabhängig sind.

##Unterstützte Versionen

Das Mobile Engagement-SDK für Windows Silverlight kann nur in Anwendungen für folgende Betriebssysteme eingebettet werden:

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE]Informationen zu Windows Phone 8.1 (nicht Silverlight) finden Sie im [Windows Universal-Integrationsverfahren](mobile-engagement-windows-store-integrate-engagement.md).

##Installieren Sie das Mobile Engagement Silverlight-SDK

Das Mobile Engagement-SDK für Windows Silverlight steht als NuGet-Paket unter dem Namen *MicrosoftAzure.MobileEngagement* zur Verfügung. Sie können es vom Nuget-Paket-Manager für Visual Studio installieren.

##Hinzufügen von Funktionen

Das Engagement-SDK benötigt einige Funktionen des Windows Phone Silverlight-SDK, damit es einwandfrei funktioniert.

Öffnen Sie die Datei `WMAppManifest.xml` und stellen Sie sicher, dass die folgenden Funktionen im Bereich `Capabilities` deklariert werden:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##Initialisieren des Engagement-SDK

### Engagement-Konfiguration

Die Engagement-Konfiguration erfolgt zentral in der Datei `Resources\EngagementConfiguration.xml` Ihres Projekts.

Bearbeiten Sie diese Datei, um Folgendes anzugeben:

-   Die Verbindungszeichenfolge der Anwendung zwischen den Tags `<connectionString>` und `<\connectionString>`.

Wenn Sie sie stattdessen zur Laufzeit angeben möchten, können Sie die folgende Methode vor der Initialisierung des Engagement-Agent aufrufen:

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(engagementConfiguration);

Die Verbindungszeichenfolge für die Anwendung wird im Azure-Verwaltungsportal angezeigt.

### Engagement-Initialisierung

Wenn Sie ein neues Projekt erstellen, wird eine Datei `App.xaml.cs` generiert. Diese Klasse erbt von `Application` und enthält viele wichtige Methoden. Sie wird auch zum Initialisieren des Engagement-SDK verwendet.

Ändern Sie `App.xaml.cs`:

-   Fügen Sie Ihre Anweisungen `using` hinzu:

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

> [AZURE.WARNING]Wir raten dringend davon ab, die Engagement-Initialisierung an einer anderen Stelle der Anwendung hinzuzufügen. Bedenken Sie jedoch, dass die Methode `EngagementAgent.Instance.Init` in einem dedizierten Thread und nicht im UI-Thread ausgeführt wird.

##Grundlegende Berichterstellung

### Empfohlene Methode: Überladen der `PhoneApplicationPage`-Klassen

Um den Bericht über alle Protokolle zu aktivieren, die zum Berechnen von Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Statistiken durch Engagement erforderlich sind, können Sie einfach alle Ihre `PhoneApplicationPage`-Unterklassen von den `EngagementPage`-Klassen erben lassen.

Hier ist ein Beispiel für eine Seite Ihrer Anwendung. Das Gleiche gilt für alle Seiten der Anwendung.

#### C#-Quelldatei

Ändern Sie die Datei Ihrer Seite `.xaml.cs`:

-   Fügen Sie Ihre Anweisungen `using` hinzu:

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

> [AZURE.WARNING]Wenn Ihre Seite von der Methode `OnNavigatedTo` erbt, stellen Sie den Aufruf von `base.OnNavigatedTo(e)` sicher. Andernfalls wird die Aktivität nicht berichtet. Tatsächlich ruft `EngagementPage` `StartActivity` von innerhalb der Methode `OnNavigatedTo` ab.

#### XAML-Datei

Ändern Sie die Datei Ihrer Seite `.xaml`:

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

Standardmäßig wird der Klassenname der Seite als der Name der Aktivität gemeldet, ohne Zusatz. Wenn die Klasse das Suffix „Page“ verwendet, löscht Engagement auch dieses.

Wenn Sie das Standardverhalten für den Namen außer Kraft setzen möchten, fügen Sie dem Code einfach Folgendes hinzu:

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

Diese Methoden werden von innerhalb der Methode `OnNavigatedTo` Ihrer Seite aufgerufen.

### Alternative Methode: Rufen Sie `StartActivity()` manuell auf

Wenn Sie Ihre `PhoneApplicationPage`-Klassen nicht überladen möchten oder können, können Sie stattdessen Ihre Aktivitäten durch direktes Aufrufen der `EngagementAgent`-Methoden starten.

Es empfiehlt sich, `StartActivity` innerhalb Ihrer Methode `OnNavigatedTo` von PhoneApplicationPage aufzurufen.

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		   base.OnNavigatedTo(e);
		   EngagementAgent.Instance.StartActivity("MyPage");
		}

> [AZURE.IMPORTANT]Stellen Sie sicher, dass Ihre Sitzung ordnungsgemäß beendet wird.
>
> Das SDK ruft die Methode `EndActivity` automatisch auf, wenn die Anwendung geschlossen wird. Daher wird **dringend** empfohlen, bei jeder Änderung der Benutzeraktivität die Methode `StartActivity` und **niemals** die Methode `EndActivity` aufzurufen. Diese Methode sendet die Nachricht an den Engagement-Server, dass der aktuelle Benutzer die Anwendung verlassen hat, und dies wirkt sich auf alle Anwendungsprotokolle aus.

##Erweiterte Berichterstellung

Möglicherweise möchten Sie anwendungsspezifische Ereignisse, Fehler und Aufträge melden; verwenden Sie dazu die anderen Methoden in der Klasse `EngagementAgent`. Mit der Engagement-API können alle erweiterten Funktionen von Engagement verwendet werden.

Weitere Informationen finden Sie unter [Verwenden der erweiterten Mobile Engagement-Tagging-API in Ihrer Windows Phone Silverlight-App](../mobile-engagement-windows-phone-use-engagement-api/).

##Erweiterte Konfiguration

### Deaktivieren der automatischen Absturzberichtsfunktion

Sie können die automatische Absturzberichtsfunktion von Engagement deaktivieren. Wenn dann eine unbehandelte Ausnahme auftritt, handelt Engagement nicht.

> [AZURE.WARNING]Wenn Sie diese Funktion deaktivieren möchten, bedenken Sie, dass Engagement Ihnen bei Auftreten eines nicht behandelten Absturzes der App weder eine Meldung zum Absturz sendet **NOCH** die Sitzung und Aufträge schließt.

Zum Deaktivieren der automatischen Berichterstellung bei Abstürzen passen Sie Ihre Konfiguration abhängig davon an, wie Sie sie deklariert haben:

#### In der Datei `EngagementConfiguration.xml`

Setzen Sie die Berichterstattung bei Abstürzen zwischen den Tags `<reportCrash>` und `</reportCrash>` auf `false`.

#### Im Objekt `EngagementConfiguration` während der Laufzeit

Setzen Sie Absturzbericht auf „false“ mithilfe des Objekts „EngagementConfiguration“.

		/* Engagement configuration. */

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		/* Disable Engagement crash reporting. */ engagementConfiguration.Agent.ReportCrash = false;

### Burst-Modus

Standardmäßig meldet der Engagement-Dienst Protokolle in Echtzeit. Wenn Ihre Anwendung sehr häufig Protokolle meldet, ist es besser, die Protokolle zu puffern und sie dann gleichzeitig in regelmäßigen Zeitintervallen zu melden (dies wird als „Burst-Modus“ bezeichnet).

Rufen Sie dazu die folgende Methode auf:

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Das Argument ist ein Wert in **Millisekunden**. Immer wenn Sie die Protokollierung in Echtzeit erneut aktivieren möchten, rufen Sie die Methode ohne Parameter oder mit dem Wert 0 auf.

Der Burst-Modus verlängert leicht die Akkulaufzeit, wirkt sich jedoch auf den Engagement-Monitor aus: Die Dauer von allen Sitzungen und Aufträgen wird auf den Burst-Schwellenwert gerundet (folglich sind eventuell Sitzungen und Aufträge, die kürzer als der Burst-Schwellenwert sind, möglicherweise nicht sichtbar). Es wird empfohlen, einen Burst-Schwellenwert von höchstens 30000 (30 s) zu verwenden. Sie müssen beachten, dass gespeicherte Protokolle auf 300 Elemente beschränkt sind. Wenn der Sendevorgang zu lange dauert, können einige Protokolle verloren gehen.

> [AZURE.WARNING]Der Burstschwellenwert kann nicht auf einen Zeitraum von weniger als einer Sekunde konfiguriert werden. Wenn Sie dies versuchen, zeigt das SDK eine Ablaufverfolgung mit einem Fehler an und setzt den Wert automatisch auf den Standardwert von 0 Sekunden zurück. Dadurch wird ausgelöst, dass das SDK die Protokolle in Echtzeit meldet.
 

<!---HONumber=Oct15_HO3-->