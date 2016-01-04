<properties 
	pageTitle="Windows Universal-Apps Engagement SDK-Integration" 
	description="Integrieren von Azure Mobile Engagement in Windows Universal-Apps" 					
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
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

# Windows Universal-Apps Engagement SDK-Integration

> [AZURE.SELECTOR] 
- [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Dieses Verfahren beschreibt die einfachste Möglichkeit zum Aktivieren der Engagement-Funktionen zur Analyse und Überwachung in Ihrer Windows Universal-Anwendung.

Die folgenden Schritte sind ausreichend, um den Bericht von Protokollen zu aktivieren, die zur Berechnung aller Statistiken zu Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Informationen notwendig sind. Der Bericht von Protokollen, die zur Berechnung anderer Statistiken wie Ereignisse, Fehler und Aufträge erforderlich ist, muss manuell mithilfe der Engagement-API erfolgen (siehe [So verwenden Sie die erweiterte Mobile Engagement-API für Tags in Ihrer Windows Universal-App](mobile-engagement-windows-store-use-engagement-api.md)), da diese Statistiken von der Anwendung abhängig sind.

## Unterstützte Versionen

Das Mobile Engagement-SDK für Windows Universal-Apps kann nur in Windows-Runtime- und Universal Windows-Plattformanwendungen für folgende Betriebssysteme integriert werden:

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (Desktop- und Mobile-Familien)

> [AZURE.NOTE]Informationen für Windows Phone Silverlight finden Sie im [Windows Universal Silverlight-Integrationsverfahren](mobile-engagement-windows-phone-integrate-engagement.md).

## Installieren der Mobile Engagement Universal Apps-SDK

### Alle Plattformen

Das Mobile Engagement-SDK für Windows Universal-App steht als NuGet-Paket unter dem Namen *MicrosoftAzure.MobileEngagement* zur Verfügung. Sie können es vom Nuget-Paket-Manager für Visual Studio installieren.

### Windows 8.x und Windows Phone 8.1

NuGet stellt automatisch die SDK-Ressourcen im `Resources`-Ordner im Stammverzeichnis Ihres Anwendungsprojekts bereit.

### Unterstützung für Windows 10 Universal Windows-Plattform-Anwendungen

NuGet stellt die SDK-Ressourcen in Ihrer UWP-Anwendung noch nicht automatisch bereit. Sie müssen dies manuell durchführen, bis die Bereitstellung von Ressourcen in NuGet wieder eingeführt wird:

1.  Öffnen Sie den Datei-Explorer.
2.  Navigieren Sie zum folgenden Speicherort (wobei **x.x.x** für die Version des Engagements steht, das Sie installieren): *%USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\**x.x.x** \\content\\win81*
3.  Ziehen Sie den **Ressourcen**-Ordner per Drag-und-Drop aus dem Datei-Explorer in das Stammverzeichnis des Projekts in Visual Studio.
4.  Wählen Sie in Visual Studio Ihr Projekt aus, und aktivieren Sie das Symbol **Alle Dateien anzeigen** über dem **Projektmappen-Explorer**.
5.  Einige Dateien sind nicht im Projekt enthalten. Um die Dateien in einem Vorgang zu importieren, klicken Sie mit der rechten Maustaste auf den **Ressourcen**-Ordner, und wählen Sie **Aus Projekt ausschließen** aus. Klicken Sie dann mit der rechten Maustaste erneut auf den **Ressourcen**-Ordner, und wählen Sie **Zu Projekt hinzufügen** aus, um den gesamten Ordner wieder einzuschließen. Alle Dateien aus dem **Ressourcen**-Ordner sind jetzt im Projekt enthalten.

Das extrahierte Engagement-Paket finden Sie auch unter *$(Solutiondir)\\Packages* oder an dem in der *NuGet.config*-Datei definierten Speicherort.

## Hinzufügen von Funktionen

Das Engagement-SDK benötigt einige Funktionen des Windows-SDK, damit es einwandfrei funktioniert.

Öffnen Sie die Datei `Package.appxmanifest` und stellen Sie sicher, dass die folgenden Funktionen deklariert sind:

-   `Internet (Client)`

## Initialisieren des Engagement-SDK

### Engagement-Konfiguration

Die Engagement-Konfiguration erfolgt zentral in der Datei `Resources\EngagementConfiguration.xml` Ihres Projekts.

Bearbeiten Sie diese Datei, um Folgendes anzugeben:

-   Die Verbindungszeichenfolge der Anwendung zwischen den Tags `<connectionString>` und `<\connectionString>`.

Wenn Sie sie stattdessen zur Laufzeit angeben möchten, können Sie die folgende Methode vor der Initialisierung des Engagement-Agent aufrufen:
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Connection string for my Windows Store App. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

Die Verbindungszeichenfolge für die Anwendung wird im klassischen Azure-Portal angezeigt.

### Engagement-Initialisierung

Wenn Sie ein neues Projekt erstellen, wird eine Datei `App.xaml.cs` generiert. Diese Klasse erbt von `Application` und enthält viele wichtige Methoden. Sie wird auch zum Initialisieren des Engagement-SDK verwendet.

Ändern Sie `App.xaml.cs`:

-   Fügen Sie Ihre Anweisungen `using` hinzu:

		using Microsoft.Azure.Engagement;

-   Definieren Sie eine Methode, um die Engagement-Initialisierung einmal für alle Aufrufe freizugeben:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
		
		  // or
		
		  EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Rufen Sie `InitEngagement` in der `OnLaunched`-Methode auf:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
          InitEngagement(e);
		}

-   Wenn die Anwendung mit einem benutzerdefinierten Schema gestartet wird, wird eine andere Anwendung oder die Befehlszeile mit der Methode `OnActivated` aufgerufen. Außerdem müssen Sie das Engagement-SDK initiieren, wenn Ihre App aktiviert wird. Überschreiben Sie dazu die Methode `OnActivated`:

		protected override void OnActivated(IActivatedEventArgs args)
		{
          InitEngagement(args);
		}

> [AZURE.IMPORTANT]Wir raten dringend davon ab, die Engagement-Initialisierung an einer anderen Stelle der Anwendung hinzuzufügen.

## Grundlegende Berichterstellung

### Empfohlene Methode: Überladen der `Page`-Klassen

Um den Bericht über alle Protokolle zu aktivieren, die zum Berechnen von Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Statistiken durch Engagement erforderlich sind, können Sie einfach alle Ihre `Page`-Unterklassen von den `EngagementPage`-Klassen erben lassen.

Hier ist ein Beispiel für eine Seite Ihrer Anwendung. Das Gleiche gilt für alle Seiten der Anwendung.

#### C#-Quelldatei

Ändern Sie die Datei Ihrer Seite `.xaml.cs`:

-   Fügen Sie Ihre Anweisungen `using` hinzu:

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

> [AZURE.IMPORTANT]Wenn Ihre Seite die Methode `OnNavigatedTo` überschreibt, rufen Sie unbedingt `base.OnNavigatedTo(e)` auf. Andernfalls wird die Aktivität nicht erfasst (`EngagementPage` ruft `StartActivity` innerhalb der `OnNavigatedTo`-Methode auf).

#### XAML-Datei

Ändern Sie die Datei Ihrer Seite `.xaml`:

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

Standardmäßig wird der Klassenname der Seite als der Name der Aktivität gemeldet, ohne Zusatz. Wenn die Klasse das Suffix „Page“ verwendet, löscht Engagement auch dieses.

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

Diese Methoden werden von innerhalb der Methode `OnNavigatedTo` Ihrer Seite aufgerufen.

### Alternative Methode: Rufen Sie `StartActivity()` manuell auf

Wenn Sie Ihre `Page`-Klassen nicht überladen möchten oder können, können Sie stattdessen Ihre Aktivitäten durch direktes Aufrufen der `EngagementAgent`-Methoden starten.

Es wird empfohlen, `StartActivity` innerhalb Ihrer Methode `OnNavigatedTo` der Seite aufzurufen.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT]Stellen Sie sicher, dass Ihre Sitzung ordnungsgemäß beendet wird.
> 
> Das Windows Universal-SDK ruft automatisch die Methode `EndActivity` auf, wenn die Anwendung geschlossen wird. Daher wird **dringend** empfohlen, bei jeder Änderung der Benutzeraktivität die Methode `StartActivity` und **niemals** die Methode `EndActivity` aufzurufen. Diese Methode benachrichtigt den Engagement-Server, dass der aktuelle Benutzer die Anwendung verlassen hat, und dies wirkt sich auf alle Anwendungsprotokolle aus.

## Erweiterte Berichterstellung

Möglicherweise möchten Sie anwendungsspezifische Ereignisse, Fehler und Aufträge melden; verwenden Sie dazu die anderen Methoden in der Klasse `EngagementAgent`. Mit der Engagement-API können alle erweiterten Funktionen von Engagement verwendet werden.

Weitere Informationen finden Sie unter [Verwenden der erweiterten Mobile Engagement-Tagging-API in Ihrer Windows Universal-App](../mobile-engagement-windows-store-use-engagement-api/).

##Erweiterte Konfiguration

### Deaktivieren der automatischen Absturzberichtsfunktion

Sie können die automatische Absturzberichtsfunktion von Engagement deaktivieren. Wenn dann eine unbehandelte Ausnahme auftritt, handelt Engagement nicht.

> [AZURE.WARNING]Wenn Sie diese Funktion deaktivieren möchten, bedenken Sie, dass bei einem unbehandelten Absturz in Ihrer App Engagement den Absturz nicht sendet **UND** weder die Sitzung noch die Aufträge schließt.

Zum Deaktivieren der automatischen Absturzberichtsfunktion passen Sie Ihre Konfiguration an, je nachdem, wie Sie sie deklariert haben:

#### In der Datei `EngagementConfiguration.xml`

Setzen Sie die Berichterstattung bei Abstürzen zwischen den Tags `<reportCrash>` und `</reportCrash>` auf `false`.

#### Im Objekt `EngagementConfiguration` während der Laufzeit

Setzen Sie Absturzbericht auf „false“ mithilfe des Objekts „EngagementConfiguration“.

		/* Engagement configuration. */
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		
		/* Disable Engagement crash reporting. */
		engagementConfiguration.Agent.ReportCrash = false;

### Burst-Modus

Standardmäßig meldet der Engagement-Dienst Protokolle in Echtzeit. Wenn Ihre Anwendung sehr häufig Protokolle meldet, ist es besser, die Protokolle zu puffern und sie dann gleichzeitig in regelmäßigen Zeitintervallen zu melden (dies wird als „Burst-Modus“ bezeichnet).

Rufen Sie dazu die folgende Methode auf:

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Das Argument ist ein Wert in **Millisekunden**. Immer wenn Sie die Protokollierung in Echtzeit erneut aktivieren möchten, rufen Sie die Methode ohne Parameter oder mit dem Wert 0 auf.

Der Burst-Modus verlängert leicht die Akkulaufzeit, wirkt sich jedoch auf den Engagement-Monitor aus: Die Dauer von allen Sitzungen und Aufträgen wird auf den Burst-Schwellenwert gerundet (folglich sind eventuell Sitzungen und Aufträge, die kürzer als der Burst-Schwellenwert sind, möglicherweise nicht sichtbar). Es wird empfohlen, einen Burst-Schwellenwert von höchstens 30000 (30 s) zu verwenden. Sie müssen beachten, dass gespeicherte Protokolle auf 300 Elemente beschränkt sind. Wenn der Sendevorgang zu lange dauert, können einige Protokolle verloren gehen.

> [AZURE.WARNING]Der Burst-Schwellenwert kann nicht auf einen Wert unter 1 s konfiguriert werden. Wenn Sie dies versuchen, zeigt das SDK eine Ablaufverfolgung mit dem Fehler an und setzt sich automatisch auf den Standardwert, d. h. 0 s, zurück. Dadurch wird ausgelöst, dass das SDK die Protokolle in Echtzeit meldet.

[here]: http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]: http://docs.nuget.org/docs/start-here/overview
 

<!---HONumber=AcomDC_1203_2015-->