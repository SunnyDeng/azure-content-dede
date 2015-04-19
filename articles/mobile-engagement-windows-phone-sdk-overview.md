<properties 
	pageTitle="Integration von Azure Mobile Engagement für Windows Phone SDK" 
	description="Neueste Updates und Verfahren für Windows Phone-SDK für Azure Mobile Engagement" 					
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

# Windows Phone-SDK für Azure Mobile Engagement v2.0.0
Beginnen Sie hier, um alle Details zur Integration von Azure Mobile Engagement in einer Windows Phone-App abzurufen. Wenn Sie es zunächst nur probieren möchten, stellen Sie sicher, dass Sie das [15-Minuten-Lernprogramm](mobile-engagement-windows-phone-get-started.md) durchführen.

Klicken Sie, um den [SDK-Inhalt](mobile-engagement-windows-phone-sdk-content.md) anzuzeigen

## Integrationsverfahren

1. Beginnen Sie hier: [Integration von Mobile Engagement in Ihrer Windows Phone-App](mobile-engagement-windows-phone-integrate-engagement.md)

2. Für Benachrichtigungen: [Integration von Reach (Benachrichtigungen) in der Windows Phone-App](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Tag-Plan-Implementierung: [So verwenden Sie die erweiterte Mobile Engagement API für Tags in Ihrer Windows Phone-App](mobile-engagement-windows-phone-use-engagement-api.md)

## Versionshinweise

### 2.0.0 (17/02/2015)
-   Erste Version von Azure Mobile Engagement
-   appId/sdkKey-Konfiguration wird durch die Konfiguration einer Verbindungszeichenfolge ersetzt.
-   Verbesserungen der Sicherheit.

Eine frühere Version finden Sie unter [Vollständige Versionshinweise](mobile-engagement-windows-phone-release-notes.md)

## Upgrade-Verfahren

Wenn Sie bereits eine ältere Version unseres SDK in Ihrer Anwendung integriert haben, müssen Sie die folgenden Punkte beim Aktualisieren des SDK beachten.

Möglicherweise müssen Sie mehrere Verfahren befolgen, wenn Sie mehrere Versionen des SDK verpasst haben, siehe vollständige [Upgrade-Verfahren](mobile-engagement-windows-phone-upgrade-procedure/). Wenn Sie beispielsweise von 0.10.1 zu 0.11.0 migrieren, müssen Sie zunächst den Vorgang "von 0.9.0 zu 0.10.1" ausführen und anschließend den Vorgang "0.10.1 zu 0.11.0".

### Von 1.1.1 zu 2.0.0

Im Folgenden wird beschrieben, wie Sie die Migration einer SDK-Integration vom Capptain-Dienst, der von Capptain-SAS angeboten wird, in eine App von Azure Mobile Engagement durchführen. 

>[Azure.IMPORTANT] Capptain und Mobile Engagement sind nicht dieselben Dienste, und die unten beschriebene Vorgehensweise hebt nur hervor, wie die Migration der Clientapp durchzuführen ist. Bei der Migration des SDK in die App werden Ihre Daten NICHT von den Capptain-Servern zu den Mobile Engagement-Servern migriert

Wenn Sie von einer früheren Version migrieren, ziehen Sie zunächst die Capptain-Website zurate, um zu 1.1.1 zu migrieren und wenden Sie dann das folgende Verfahren an

#### NuGet-Paket

Ersetzen Sie Capptain.WindowsPhone.nupkg durch azuresdk-mobileengagement-windowsphone-VERSION.nupkg im Paket Lib-Archivordner.

#### Anwenden von Mobile Engagement

Das SDK verwendet den Begriff `Engagement`. Sie müssen Ihr Projekt entsprechend dieser Änderung aktualisieren.

Sie müssen Ihr aktuelles Capptain Nuget-Paket deinstallieren. Bedenken Sie, dass alle Änderungen im Capptain-Ressourcenordner entfernt werden. Wenn Sie diese Dateien beibehalten möchten, erstellen Sie eine Kopie davon.

Danach installieren Sie das neue Microsoft Azure Engagement Nuget-Paket für Ihr Projekt. Sie finden es direkt auf der [Nuget-Website]. oder im Index. Diese Aktion ersetzt alle Ressourcendateien, die von Engagement verwendet werden, und fügt die neue Engagement-DLL Ihren Projektverweisen hinzu.

Sie müssen Ihre Projektverweise bereinigen, indem Sie Capptain-DLL-Verweise löschen. Wenn Sie dies nicht tun, kommt es zu Konflikten mit der Capptain-Version und Fehler treten auf.

Wenn Sie Capptain-Ressourcen angepasst haben, kopieren Sie den Inhalt Ihrer alten Dateien, und fügen Sie ihn in die neuen Engagement-Dateien ein. Bitte beachten Sie, dass XAML- und CS-Dateien aktualisiert werden müssen.

Nach Abschluss dieser Schritte müssen Sie nur die alten Capptain-Verweise durch die neuen Engagement-Verweise ersetzen.

Alle Capptain-Namespaces müssen aktualisiert werden.

Vor der Migration:

			using Capptain.Agent;
			using Capptain.Reach;

After migration:

			using Microsoft.Azure.Engagement;

All Capptain classes that contain "Capptain" should contain "Engagement".

Before migration:

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

Für XAML-Dateien ändern sich ebenfalls Capptain-Namespace und Attribute.

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

Beachten Sie, dass andere Ressourcen wie Capptain-Bilder auch für die Verwendung von "Engagement" umbenannt wurden.

#### Anwendungs-ID/SDK-Schlüssel

Engagement verwendet eine Verbindungszeichenfolge. Sie müssen bei Mobile Engagement keine Anwendungs-ID und keinen SDK-Schlüssel angeben, sie müssen nur eine Verbindungszeichenfolge angeben. Sie können es auf der EngagementConfiguration-Datei einrichten.

Die Konfiguration von Engagement kann in der `Resources\EngagementConfiguration.xml`-Datei des Projekts festgelegt werden.

Bearbeiten Sie diese Datei, um Folgendes anzugeben:

-   Die Verbindungszeichenfolge der Anwendung zwischen den Tags `<connectionString>` und `<\connectionString>`.

Wenn Sie sie stattdessen zur Laufzeit angeben möchten, können Sie die folgende Methode vor der Engagement Agent-Initialisierung aufrufen:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

Die Verbindungszeichenfolge für die Anwendung wird im Azure-Portal angezeigt.

#### Änderung des Namens von Objekten

Alle Elemente mit dem Namen... Capptain... wurden umbenannt in ...Engagement... Für Capptain und Engagement gilt dasselbe.

Beispiele für häufig verwendete Capptain-Elemente:

> -   CapptainConfiguration trägt nun den Namen EngagementConfiguration
> -   CapptainAgent trägt nun den Namen EngagementAgent
> -   CapptainReach trägt nun den Namen EngagementReach
> -   CapptainHttpConfig trägt nun den Namen EngagementHttpConfig
> -   GetCapptainPageName trägt nun den Namen GetEngagementPageName

Beachten Sie, dass sich das Umbenennen auch auf überschriebene Methoden auswirkt.

### Aktualisieren von älteren Versionen

Siehe [Upgrade-Verfahren](mobile-engagement-windows-phone-upgrades/)

<!--HONumber=47-->
