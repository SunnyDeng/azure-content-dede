<properties 
	pageTitle="Verwalten von API-Apps"
	description="Erfahren Sie, wie Sie Azure App Service-API-Apps mithilfe des Azure-Portals und dem Server-Explorer von Visual Studio verwalten."
	services="app-service\api"
	documentationCenter=""
	authors="tdykstra"
	manager="wpickett"
	editor="jimbe"/>

<tags 
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="tdykstra"/>

# Verwalten von API-Apps in Azure App Service

Dieser Artikel zeigt, wie Sie das [Azure-Vorschauportal](https://portal.azure.com/) zum Ausführen verschiedener Verwaltungsaufgaben für API-Apps sowie für Überwachungsaufgaben einsetzen. Sie können u. a. die folgenden Aufgaben ausführen:

- Konfigurieren der Authentifizierung 
- Aktivieren der automatischen Skalierung
- Anzeigen von Protokollen
- Anzeigen, wie viele Anforderungen durchgeführt werden und wie viele Daten von einer API-App genutzt werden
- Sichern einer API-App und Erstellen von Warnungen
- Konfigurieren der rollenbasierten Zugriffssicherheit

Der Artikel zeigt außerdem, wie Sie einige Verwaltungsaufgaben im Fenster "Server-Explorer" in Visual Studio ausführen.

## Die Blätter "API-App" und "Gateway" im Azure-Vorschauportal

In Azure App Service ist eine API-App eine [Web-App](../app-service-web/app-service-web-overview.md), die über zusätzliche Funktionen zum Hosten von Webdiensten verfügt. Im Azure-Portal gibt es ein Blatt **API-App** zum Verwalten der API-spezifischen Funktionen sowie ein Blatt **API-App-Host** zum Verwalten der zugrunde liegenden Web-App.

Jede Ressourcengruppe, die mindestens eine API-App umfasst, verfügt auch über ein *Gateway*. Das Gateway fungiert als Proxy, es übernimmt die Authentifizierung und andere administrative Funktionen für alle API-Apps in einer Ressourcengruppe. Wie eine API-App ist ein Gateway eine Web-App mit zusätzlicher Funktionalität. Es gibt deshalb zwei Portalblätter zum Verwalten des Gateways: das Blatt **Gateway** für gatewayspezifische Funktionen und das Blatt **Gatewayhost** zum Verwalten der zugrunde liegenden Web-App.

### Aufgaben, die nur auf dem Blatt "API-App" ausgeführt werden können

Sie verwenden das Blatt **API-App** für die folgenden Aufgaben:

- Konfigurieren der Zugriffsebene – Klicken Sie auf **Einstellungen > Anwendungseinstellungen**. Der Standardwert lautet "Intern". Dies bedeutet, dass nur API-Apps in derselben Ressourcengruppe die API-App aufrufen dürfen. Weitere Informationen finden Sie unter [Schützen einer API-App](app-service-api-dotnet-add-authentication.md).   
- Konfigurieren der Aktualisierungsrichtlinie – Klicken Sie auf **Einstellungen > Anwendungseinstellungen**. Der Standardwert lautet **Ein**. Dies bedeutet, dass beim Veröffentlichen einer neuen Version der API-App im Marketplace Ihre API-App automatisch auf die neue Version aktualisiert wird, sofern sie keine Funktionsbeeinträchtigung (non-breaking) bedeutet.  
- Konfigurieren der Authentifizierung für ausgehende Aufrufe aus der API-App – Klicken Sie auf **Einstellungen > Authentifizierung**. Wenn die API-App einen externen Dienst aufruft, der eine Authentifizierung erfordert, werden hier die erforderlichen Konfigurationswerte eingegeben. Ein Dropbox-Connector erfordert beispielsweise eine Client-ID und einen geheimen Clientschlüssel für den Zugriff auf den Dropbox-Dienst.
- Konfigurieren von [RBAC](../role-based-access-control-configure.md) – Klicken Sie auf **Einstellungen > Benutzer**. Der hier konfigurierte Benutzerzugriff legt fest, wer auf die API-App-spezifischen Funktionen zugreifen kann. Verwenden Sie zum Konfigurieren von RBAC für die Web-App-Funktionen das Blatt **API-App-Host**. Normalerweise sollten die RBAC-Einstellungen für die API-App und den API-App-Host synchronisiert werden. Wenn Sie Zugriff auf die API-App erteilen, jedoch nicht auf den API-App-Host, können die Funktionen auf dem Blatt **API-App**, die tatsächlich den API-App-Host betreffen, nicht genutzt werden.
- Anzeigen der API-Definition – Klicken Sie im Abschnitt **Zusammenfassung** auf **API-Definition**, um eine Liste der von der API-App verfügbar gemachten Methoden anzuzeigen.
- [Installieren des Hybrid Connection Managers](../app-service-logic/app-service-logic-hybrid-connection-manager.md). Der Hybrid Connection Manager bietet Ihnen die Möglichkeit zur Verbindungsherstellung mit einem lokalen System, beispielsweise SQL Server oder SAP. Diese Hybridkonnektivität verwendet Azure Service Bus zur Verbindungsherstellung und zur Steuerung der Sicherheit zwischen Ihren Azure-Ressourcen und Ihren lokalen Ressourcen.

### Aufgaben, die sowohl auf dem Blatt "API-App" als auch auf dem Blatt "API-App-Host" ausgeführt werden können 

Über das Blatt **API-App** können Sie viele Aufgaben ausführen, die die zugrunde liegenden Web-App betreffen. Beispielsweise können Sie die folgenden Aufgaben ausführen:

* Sie können die Web-App, die die API-App hostet, beenden, starten und neu starten.  
- Durch Auswahl von **Anforderungen und Fehler** können Sie unterschiedliche Leistungsmetriken hinzufügen, beispielsweise gängige HTTP-Fehlercodes wie die HTTP-Statuscodes 200, 400 oder 500.
- Sie können Antwortzeiten, die Anzahl von Anforderungen an eine API-App sowie die Menge an ein- und ausgehenden Daten anzeigen. 
- Sie können E-Mail-Warnungen erstellen, wenn eine Metrik einen gewählten Schwellenwert überschreitet. 
- Es ist möglich, die **CPU**-Belegung durch eine API-App, das aktuelle **Nutzungskontingent** in MB und die maximale Datennutzung basierend auf Ihrem Tarif anzuzeigen.
- Über **Geschätzte Ausgaben** können Sie die potenziellen Kosten für die Ausführung Ihrer API-App anzeigen.
- Zeigen Sie die Anwendungsprotokolle und weitere IIS-Protokolle an, darunter Webserverprotokolle und FREB-Protokolle.
- Wählen Sie **Prozesse**, um den Prozess-Explorer zu öffnen. Dieser zeigt Ihre Webinstanzen und ihre Eigenschaften, einschließlich Threadanzahl und Arbeitsspeichernutzung.

Diese Aufgaben können auch über das Blatt **API-App-Host** ausgeführt werden. Dies ist der Grund dafür, dass die Benutzeroberfläche der zwei Blätter in vielen Bereichen identisch ist. Beispielsweise haben die Schaltflächen **Stopp**, **Start** und **Neustart** auf dem Blatt **API-App** exakt die gleiche Auswirkung wie die Schaltflächen **Stopp**, **Start** und **Neustart** auf dem Blatt **API-App-Host**. Ebenso sind die auf dem Blatt **API-App** bereitgestellten Überwachungsinformationen mit denen auf dem Blatt **API-App-Host** identisch.

Die einzigen Funktionen auf dem Blatt **API-App**, bei denen es sich nicht um Duplikate aus dem Blatt **API-App-Host** handelt, sind im vorherigen Abschnitt aufgeführt.

### Aufgaben, die nur auf dem Blatt "API-App-Host" ausgeführt werden können

Sie verwenden das Blatt **API-App-Host** für alle Aufgaben, die Sie für eine beliebige Web-App ausführen würden.

### Aufgaben, die nur auf dem Blatt "Gateway" ausgeführt werden können

Sie verwenden das Blatt **Gateway** für die folgenden Aufgaben:

- Konfigurieren des Authentifizierungsanbieters für eingehende Aufrufe an API-Apps – Klicken Sie auf **Einstellungen > Identität**. Wenn das Gateway Benutzer authentifizieren muss, bevor diese API-Apps in der Ressourcengruppe aufrufen, müssen hier die erforderlichen Konfigurationswerte eingegeben werden. Weitere Informationen finden Sie unter [Konfigurieren und Testen eines SaaS-Connectors in Azure App Service](app-service-api-connnect-your-app-to-saas-connector.md). 
- Konfigurieren von [RBAC](../role-based-access-control-configure.md) – Klicken Sie auf **Einstellungen > Benutzer**. Der hier konfigurierte Benutzerzugriff legt nur fest, wer auf die gatewayspezifischen Funktionen zugreifen kann. Die Einstellungen betreffen nicht die Funktionen, die mit allen Web-Apps gemeinsam verwendet werden.

### Aufgaben, die sowohl auf dem Blatt "Gateway" als auch auf dem Blatt "Gateway-Host" ausgeführt werden können 

Die Blätter "Gateway" und "Gateway-Host" nutzen die Benutzeroberfläche in gleicher Weise gemeinsam wie die Blätter "API-App" und "API-App-Hosts".

### Aufgaben, die nur auf dem Blatt "Gateway-Host" ausgeführt werden können

Sie verwenden das Blatt **Gatewayhost** für alle Aufgaben, die Sie für eine beliebige Web-App ausführen würden.

## <a id="navigate"></a>Navigation zu den Blättern „API-App“ und „Gateway“ 

Eine Möglichkeit, zum Blatt **API-App** zu wechseln besteht darin, die Suchfunktion im Azure-Portal zu verwenden. Klicken Sie auf der Startseite des Portals auf **Durchsuchen > API-Apps**, um alle API-Apps anzuzeigen, die Sie verwalten können.

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

### Navigieren zum Blatt "API-App"

Wenn Sie auf eine Zeile der Liste **API-Apps** klicken, zeigt das Portal das Blatt **API-App** an.

![](./media/app-service-api-manage-in-portal/apiappblade.png)

### Navigieren zum Blatt "API-App-Host"

Um zum Blatt **API-App-Host** zu gelangen, klicken Sie auf dem Blatt **API-App** auf **API-App-Host**.

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

### Navigieren zum Blatt "Gateway"

Um zum Blatt **Gateway** zu gelangen, klicken Sie auf den Link **Gateway** auf dem Blatt **API-App**.
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

### Navigieren zum Blatt "Gatewayhost"

Um zum Blatt **Gatewayhost** zu gelangen, klicken Sie auf den Link **Gatewayhost** auf dem Blatt **Gateway**.
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## Zugreifen auf API-Apps im Server-Explorer in Visual Studio

Im **Server-Explorer** von Visual Studio können Sie eine Remotedebuggingsitzung starten, Streamingprotokolle anzeigen und auf einen Menüeintrag klicken, der das Blatt "API-App" im Portal öffnet.

Um im Server-Explorer zu einer API-App zu wechseln, klicken Sie auf **Azure > App Service > [Name der Ressourcengruppe] > [Name der API-App]**, wie in der Abbildung gezeigt.

![](./media/app-service-api-manage-in-portal/se.png)

## Nächste Schritte

Dieser Artikel hat gezeigt, wie Sie das Azure-Portal zum Ausführen verschiedener Verwaltungsaufgaben für API-Apps verwenden. Informationen zu API-Apps, die Sie aus dem API-App-Katalog installieren, finden Sie auch unter [Verwalten und Überwachen integrierter API-Apps und Connectors](../app-service-logic/app-service-logic-monitor-your-connectors.md).

Informationen zum Verwalten von API-Apps über die Befehlszeile finden Sie in den Artikeln im Abschnitt **Automatisieren**, die links neben dem Artikel (bei breiten Browserfenstern) oder oben über dem Artikel (bei schmalen Browserfenstern) angezeigt werden.

<!---HONumber=August15_HO9-->