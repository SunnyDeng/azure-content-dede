<properties 
	pageTitle="Verwalten einer API-App" 
	description="Erfahren Sie, wie Sie API-Apps mithilfe des Azure-Vorschauportals und dem Server-Explorer in Visual Studio verwalten." 
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
	ms.date="04/20/2015" 
	ms.author="tdykstra"/>

# Verwalten einer API-App

Dieser Artikel zeigt, wie Sie das Azure-Vorschauportal zum Ausführen von Verwaltungsaufgaben für API-Apps verwenden, beispielsweise zum Konfigurieren der Authentifizierung und zum Einrichten der automatischen Skalierung. Der Artikel zeigt außerdem, wie Sie einige Verwaltungsaufgaben im Fenster "Server-Explorer" in Visual Studio ausführen.

## Grundlegendes zur API-App-Architektur

In Azure App Service ist eine API-App eine [Web-App](app-service-web-overview.md), die über zusätzliche Funktionen zum Hosten von Webdiensten verfügt. Im Azure-Vorschauportal gibt es ein Blatt **API-App** zum Verwalten der API-spezifischen Funktionen sowie ein Blatt **API-App-Host** zum Verwalten der zugrunde liegenden Web-App.

Jede Ressourcengruppe, die mindestens eine API-App umfasst, verfügt auch über ein *Gateway*. Das Gateway fungiert als Proxy, es übernimmt die Authentifizierung und andere administrative Funktionen für alle API-Apps in einer Ressourcengruppe. Wie eine API-App ist ein Gateway eine Web-App mit zusätzlicher Funktionalität. Es gibt deshalb zwei Portalblätter zum Verwalten des Gateways: das Blatt **Gateway** für gatewayspezifische Funktionen und das Blatt **Gatewayhost** zum Verwalten der zugrunde liegenden Web-App.

### Aufgaben im Blatt "API-App" 

Sie verwenden das Blatt **API-App** für die folgenden Aufgaben:

- Konfigurieren der Zugriffsebene – Klicken Sie auf **Einstellungen \> Anwendungseinstellungen**. Der Standardwert lautet "Intern". Dies bedeutet, dass nur API-Apps in derselben Ressourcengruppe die API-App aufrufen dürfen. Weitere Informationen finden Sie unter [Schützen einer API-App](app-service-api-dotnet-add-authentication.md).   
- Konfigurieren der Aktualisierungsrichtlinie – Klicken Sie auf **Einstellungen \> Anwendungseinstellungen**. Der Standardwert lautet **Ein**. Dies bedeutet, dass beim Veröffentlichen einer neuen Version der API-App im Marketplace Ihre API-App automatisch auf die neue Version aktualisiert wird, sofern sie keine Funktionsbeeinträchtigung \(non-breaking\) bedeutet.  
- Konfigurieren der Authentifizierung für ausgehende Aufrufe aus der API-App – Klicken Sie auf **Einstellungen \> Authentifizierung**. Wenn die API-App einen externen Dienst aufruft, der eine Authentifizierung erfordert, werden hier die erforderlichen Konfigurationswerte eingegeben. Ein Dropbox-Connector erfordert beispielsweise eine Client-ID und einen geheimen Clientschlüssel für den Zugriff auf den Dropbox-Dienst.
- Konfigurieren von [RBAC](role-based-access-control-configure.md) – Klicken Sie auf **Einstellungen \> Benutzer**. Der hier konfigurierte Benutzerzugriff legt fest, wer auf die API-App-spezifischen Funktionen zugreifen kann. Verwenden Sie zum Konfigurieren von RBAC für die Web-App-Funktionen das Blatt **API-App-Host**. Normalerweise sollten die RBAC-Einstellungen für die API-App und den API-App-Host synchronisiert werden. Wenn Sie Zugriff auf die API-App erteilen, jedoch nicht auf den API-App-Host, können die Funktionen im Blatt **API-App**, die tatsächlich den API-App-Host betreffen, nicht genutzt werden. Die Beziehung zwischen dem Blatt **API-App** und dem Blatt **API-App-Host** wird nachstehend erläutert.
- Anzeigen der API-Definition – Klicken Sie im Abschnitt **Zusammenfassung** auf **API-Definition**, um eine Liste der von der **API-App** verfügbar gemachten Methoden anzuzeigen.

### Gängige Funktionen in den Blättern "API-App" und "API-App-Host" 

Über das Blatt **API-App** können Sie viele Aufgaben ausführen, die die zugrunde liegenden Web-App betreffen. Beispielsweise gibt es Schaltflächen für Stopp, Start und Neustart der Web-App, die die API-App hostet. Diese Aufgaben können jedoch auch über das Blatt **API-App-Host** ausgeführt werden. Dies ist der Grund dafür, dass die Benutzeroberfläche der zwei Blätter in vielen Bereichen identisch ist. Die Schaltflächen **Stopp**, **Start** und **Neustart** im Blatt **API-App** haben exakt die gleiche Auswirkung wie die Schaltflächen **Stopp**, **Start** und **Neustart** im Blatt **API-App-Host**. Ebenso sind die im Blatt **API-App** bereitgestellten Überwachungsinformationen mit denen im Blatt **API-App-Host** identisch.

Die einzigen Funktionen im Blatt **API-App**, bei denen es sich nicht um Duplikate aus dem Blatt **API-App-Host** handelt, sind im vorherigen Abschnitt aufgeführt.

### Aufgaben im Blatt "API-App-Host"

Sie verwenden das Blatt **API-App-Host** für alle Aufgaben, die Sie für eine beliebige Web-App ausführen würden. Weitere Informationen finden Sie unter [Verwalten von Web-Apps im Portal](web-sites-manage.md).

### Aufgaben im Blatt "Gateway"

Sie verwenden das Blatt **Gateway** für die folgenden Aufgaben:

- Konfigurieren des Authentifizierungsanbieters für eingehende Aufrufe an API-Apps – Klicken Sie auf **Einstellungen \> Identität**. Wenn das Gateway Benutzer authentifizieren muss, bevor diese API-Apps in der Ressourcengruppe aufrufen, müssen hier die erforderlichen Konfigurationswerte eingegeben werden. Weitere Informationen finden Sie unter [Konfigurieren und Testen eines SaaS-Connectors in Azure App Service](app-service-api-connnect-your-app-to-saas-connector.md). 
- Konfigurieren von [RBAC](role-based-access-control-configure.md) – Klicken Sie auf **Einstellungen \> Benutzer**. Die Erläuterungen zur Beziehung zwischen der RBAC-Konfiguration in den Blättern "API-App" und "API-App-Host" gelten gleichermaßen für die Blätter "Gateway" und "Gatewayhost".

### Aufgaben im Blatt "Gatewayhost"

Sie verwenden das Blatt **Gatewayhost** für alle Aufgaben, die Sie für eine beliebige Web-App ausführen würden. Weitere Informationen finden Sie unter [Verwalten von Web-Apps im Portal](web-sites-manage.md).

## Navigieren zum Blatt "API-App" 

Eine Möglichkeit, zum Blatt **API-App** zu wechseln besteht darin, die Suchfunktion im Azure-Vorschauportal zu verwenden. Klicken Sie auf der Startseite des Portals auf **Durchsuchen \> API-Apps**, um alle API-Apps anzuzeigen, die Sie verwalten können.

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

Wenn Sie auf eine Zeile der Liste **API-App** klicken, zeigt das Portal das Blatt **API-App** an.

![](./media/app-service-api-manage-in-portal/apiappblade.png)

## Navigieren zum Blatt "API-App-Host"

Um zum Blatt **API-App-Host** zu gelangen, klicken Sie im Blatt **API-App** auf **API-App-Host**.

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

## Navigieren zum Blatt "Gateway"

Um zum Blatt **Gateway** zu gelangen, klicken Sie auf den Link **Gateway** im Blatt **API-App**.
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

## Navigieren zum Blatt "Gatewayhost"

Um zum Blatt **Gatewayhost** zu gelangen, klicken Sie auf den Link **Gatewayhost** im Blatt **Gateway**.
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## Zugreifen auf API-Apps im Server-Explorer in Visual Studio

Im Server-Explorer von Visual Studio können Sie eine Remotedebuggingsitzung starten, Streamingprotokolle anzeigen und auf einen Menüeintrag klicken, der das Blatt "API-App" im Portal öffnet.

Um im Server-Explorer zu einer API-App zu wechseln, klicken Sie auf **Azure \> App Service \> [Name der Ressourcengruppe] \> [Name der API-App]**, wie in der Abbildung gezeigt.

![](./media/app-service-api-manage-in-portal/se.png)

## Zusammenfassung

Dieser Artikel hat gezeigt, wie Sie das Azure-Vorschauportal zum Ausführen verschiedener Verwaltungsaufgaben für API-Apps verwenden. Weitere Informationen finden Sie unter [Was sind API-Apps?](app-service-api-apps-why-best-platform.md).


<!--HONumber=52-->
