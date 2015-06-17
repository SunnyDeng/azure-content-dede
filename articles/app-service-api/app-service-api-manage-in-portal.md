<properties 
	pageTitle="Verwalten und Überwachen von integrierten und benutzerdefinierten API-Anwendungen" 
	description="zum Verwalten von API-apps mithilfe der Azure-Vorschau-Portal und die Server-Explorer von Visual Studio." 
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
	ms.date="06/14/2015" 
	ms.author="tdykstra"/>

# Verwalten und Überwachen von integrierten und benutzerdefinierten API-Anwendungen

Verwenden Sie das Azure Preview Portal API-app-Verwaltungsaufgaben durchführen.

Nach dem Erstellen Ihrer eigenen API-App oder eine integrierte API-App konfigurieren, können Sie das Portal verwenden, verwalten und Überwachen von API-Anwendungen. Beim Erstellen Ihrer eigenen App API können Sie auch einige Verwaltungsaufgaben im Server-Explorer in Visual Studio ausführen.

Einige ausgeführte Aufgaben umfassen:

- Konfigurieren der Authentifizierung 
- Aktivieren Sie die automatische Skalierung
- Protokolle anzeigen
- Finden Sie, wie viele Anforderungen vorgenommen werden, und wie viele Daten von der API-Anwendung verwendet wird
- Sichern Sie Ihre App API und erstellen Warnungen
- Aktivieren der Sicherheit für die Folie und Hinzufügen von Benutzern und Rollen.

In diesem Thema werden einige der verschiedenen Optionen beschrieben.

Um diese integrierten Funktionen anzuzeigen, öffnen Sie die API-App in der Vorschau [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). Ist die API-App in Ihrem Startmenü, wählen sie das Eigenschaftenfenster zu öffnen. Sie können auch auswählen, **Durchsuchen**, Option **API-Apps**, und wählen Sie die API-App:

![](./media/app-service-api-manage-in-portal/browse.png)

## Grundlegendes zur API-App-Architektur

In Azure-App-Dienst jede API-Anwendung ist eine Separate [Webanwendung](../app-service-web/app-service-web-overview.md) die zusätzliche Funktionen zum Hosten von Webdiensten hat. In der Azure-vorschauportal ist ein **API-Anwendung** Blade für die Verwaltung der API-spezifische Features, und ein **API-App-Host** Blade für die Verwaltung der eigenen zugrunde liegenden Webanwendung.

Jede Ressourcengruppe, die mindestens eine API-App umfasst, verfügt auch über ein *Gateway*. Das Gateway fungiert als Proxy, es übernimmt die Authentifizierung und andere administrative Funktionen für alle API-Apps in einer Ressourcengruppe. Wie ein API-Anwendung ist ein Gateway für eine Webanwendung mit zusätzlichen Funktionen. Sind zwei separate Portal Blades für die Verwaltung des Gateways: ein **Gateway** Blade für Gateway-spezifische Funktionen und ein **Gatewayhost** Blade für die Verwaltung der eigenen zugrunde liegenden Webanwendung.

## Aktualisieren Sie die Einstellungen der benutzerdefinierten API-app

> [AZURE.NOTE]Dieser Abschnitt gilt für benutzerdefinierte API-Anwendungen, die von Ihnen erstellt werden. Es gilt nicht für die integrierte Microsoft-Connectors und API-apps, die auf dem Markt verfügbar.

Wenn Sie die API App öffnen, sind Sie in der API-App-Blade. Sie können **Einstellungen** für zusätzliche Konfigurationsoptionen:

![](./media/app-service-api-manage-in-portal/apiappblade.png)

Beispiele hierfür sind:

- **Konfigurieren Zugriffsebene** (** Settings ** > **Anwendungseinstellungen**): der Standardwert ist intern, d. h. nur API-apps in der gleichen Ressourcengruppe Aufrufen die API-Anwendung zulässig sind. Weitere Informationen finden Sie unter [Schützen einer API-App](app-service-api-dotnet-add-authentication.md).   
- **Konfigurieren der Aktualisierungsrichtlinie** (** Settings ** > **Anwendungseinstellungen**): Standardwert ist **auf**. Dies bedeutet, dass eine neue Version der API-app auf dem Markt veröffentlicht wird, Ihre API-Anwendung automatisch auf die neue Version aktualisiert wird, wenn eine nicht unterbrechende Änderung ist.  
- **Konfigurieren der Authentifizierung für ausgehende Aufrufe von der API-app** (** Settings ** > **Authentifizierung**): Wenn die API-Anwendung einen externen Dienst, die eine Authentifizierung erforderlich ist aufruft, werden hier die erforderliche Konfigurationswerte eingegeben. Ein Dropbox-Connector erfordert beispielsweise eine Client-ID und einen geheimen Clientschlüssel für den Zugriff auf den Dropbox-Dienst.
- **Konfigurieren [RBAC](../role-based-access-control-configure.md)** (** Settings ** > **Benutzer**): Benutzerzugriff auf die hier konfigurierten bestimmt nur, wer die API-app-spezifische Features zugreifen kann. Verwenden Sie zum Konfigurieren von RBAC für die Web-App-Funktionen das Blatt **API-App-Host**. Normalerweise sollten die RBAC-Einstellungen für die API-App und den API-App-Host synchronisiert werden. Wenn Sie Zugriff auf die API-App erteilen, jedoch nicht auf den API-App-Host, können die Funktionen im Blatt **API-App**, die tatsächlich den API-App-Host betreffen, nicht genutzt werden. Die Beziehung zwischen dem Blatt **API-App** und dem Blatt **API-App-Host** wird nachstehend erläutert.
- **Anzeigen-API-Definition** (** API Definition **): die **Zusammenfassung** Abschnitt enthält eine Liste aller offenen Methoden der **API-Anwendung**.

#### Vergleich von API-Anwendung und API-app-host 

Viele der Aufgaben und Funktionen in der API-Anwendung sind auch verfügbar, wenn Sie auswählen **app-Host-API-**:

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

Sie können:

- Beenden Sie, starten Sie, und starten Sie die Webanwendung, die die benutzerdefinierte API-Anwendung hostet. Diese Aufgaben können auch **app-Host-API-**. Die beiden Blades weisen in Bezug auf die gleiche UI. 
- Die Überwachungsinformationen zur Verfügung gestellt, auf die **API-Anwendung** Blade ist identisch mit was **app-Host-API-** zeigt. 
- Die einzigen Tasks in **API-Anwendung** Blade, die keine Duplikate aus der **app-Host-API-** zuvor aufgeführt, wie die Authentifizierung und die anderen aufgeführten Tasks zu konfigurieren.

Sie können **app-Host-API-** für alle Aufgaben, die Sie für alle Web-app tun würden. Weitere Informationen finden Sie unter [Verwalten von Web-Apps im Portal](../app-service-web/web-sites-manage.md).

#### Vergleich zwischen dem Gateway und das Gateway Aufgaben hosten

![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

In **Gateway**, können Sie:

- **Authentifizierung Anbieterkonfiguration für eingehende Anrufe an die API-apps** (** Settings ** > **Identität**): das Gateway muss Benutzer authentifizieren, bevor Sie dadurch eine API-apps in der Ressourcengruppe aufrufen, werden hier die erforderliche Konfigurationswerte eingegeben. Weitere Informationen finden Sie unter [Konfigurieren und Testen eines SaaS-Connectors in Azure App Service](app-service-api-connnect-your-app-to-saas-connector.md). 
- **Konfigurieren [RBAC](../role-based-access-control-configure.md)** (** Settings ** > **Benutzer**): die gleichen Kommentare über erklärt die Beziehung zwischen RBAC-Konfiguration auf die API-Anwendung und die API-app-Host Blades eingegeben betrifft das Gateway und Gateway Blades zu hosten.

Sie können **Gatewayhost** für alle Aufgaben, die Sie für alle Web-app tun würden. Weitere Informationen finden Sie unter [Verwalten von Web-Apps im Portal](../app-service-web/web-sites-manage.md).

## Anzeigen Sie die von Ihnen eingegebene Eigenschaften

Wenn Sie die API-App öffnen, gibt es mehrere Funktionen und Aufgaben, die Sie tun können, beispielsweise:

- Verwenden der **Start** und **Beenden** Schaltflächen zum Steuern der API-App.
- Produkt-Updates mit den zugrunde liegenden Dateien durch Ihre App API vorgenommen werden, kann durch Klicken auf **Update** auf die neuesten Versionen zu erhalten. Wenn ein Update oder ein Update vorhanden ist, z. B. durch Klicken auf **Aktualisieren** Ihrer App-API enthält dieses Update wird automatisch aktualisiert. 
- Wählen Sie **Planen der Änderung** Upgrade oder Downgrade anhand Ihrer Datenverwendung der API-App. Dieses Feature können auch Ihre Daten anzuzeigen.
- Wenn Sie einen integrierten Anschluss (z. B. die SQL-Connector) konfigurieren, können Sie optional einen Tabellennamen eingeben. Ein Schema auf Grundlage der Tabelle wurde automatisch erstellt und ist bei **herunterladen Schemas**. Dieses Schema können dann um eine Transformation oder eine Karte zu erstellen. 
- Wählen Sie **-API-Definition** zum Öffnen der Swagger-Datei für Ihre spezifische API automatisch erstellt.

## Installieren Sie den Hybrid-Verbindungs-Manager

> [AZURE.NOTE]Dieser Abschnitt gilt für die integrierte Microsoft-Connectors und API-apps, die auf dem Markt verfügbar. Es gilt nicht für benutzerdefinierte API-Anwendungen, die von Ihnen erstellt werden.

Der Hybrid-Verbindungs-Manager ermöglicht die Verbindung mit einem lokalen System, wie SQL Server oder SAP. Diese hybridkonnektivität verwendet Azure-Servicebus herstellen und die Sicherheit zwischen Azure-Ressourcen und Ihre lokalen Ressourcen zu steuern.

Finden Sie unter [Hybrid Connection Manager in Azure-App-Dienst mit](../app-service-logic/app-service-logic-hybrid-connection-manager.md).

## Überwachen der Leistung
Leistungsmetriken sind integrierte Features, und jede API-App, benutzerdefinierte oder integrierte enthalten. Diese Metriken sind spezifisch für Ihren API-Anwendung in Azure gehostet.

Sie können:

- Wählen Sie **Anforderungen und Fehler** um unterschiedliche Performance-Werte einschließlich häufig bekannte HTTP-Fehlercodes hinzuzufügen, wie z. B. 200, 400 und 500 HTTP-Statuscodes. Sie können auch finden Sie unter Antwortzeiten zu sehen, wie viele Anforderungen an die API-App vorgenommen werden und sehen, wie viele Daten eingeht, und wie viele Daten gesendet wird. Auf der Grundlage der Leistungsmetrik, können Sie e-Mail-Benachrichtigungen, wenn eine Metrik einen Ihrer Wahl Schwellenwert erstellen. 
- In **Nutzung**, können Sie sehen, wie viel **CPU** wird von der API-App verwendet wird, überprüfen Sie die aktuelle **Nutzungskontingent** in MB und die maximale Datennutzung basieren auf Ihre Kosten-Kategorie finden Sie unter. **Geschätzt verbringen** hilft Ihnen festzustellen, die potenziellen Kosten für Ihre API-App ausgeführt wird.
- Zeigen Sie die Anwendungs- und andere IIS-Protokolle, einschließlich von Webserverprotokollen und FREB-Protokolle.
- Wählen Sie **Prozesse** Process Explorer geöffnet. Zeigt Ihre webinstanzen und deren Eigenschaften, einschließlich der Thread-Anzahl und Speicherauslastung.

Mithilfe dieser Tools können bestimmen Sie, ob Planen der App Service nach oben skaliert oder nach unten, basierend auf Ihren Geschäftsanforderungen skaliert werden. Diese Funktionen sind in das Portal integriert, ohne zusätzliche Tools erforderlich.

## Steuern der Sicherheits

API-Apps verwendet rollenbasierte Sicherheit. Diese Rollen gelten für die gesamte Azure-Erfahrung, einschließlich API-Apps und anderen Azure-Ressourcen. Die Rollen beinhalten:

Rolle | Beschreibung
--- | ---
Besitzer | Haben Sie vollen Zugriff auf die Verwaltungsfunktionen und anderen Benutzern oder Gruppen gewähren Sie Zugriff.
Mitwirkender | Haben Sie vollen Zugriff auf die Verwaltungsfunktionen. Kann nicht auf andere Benutzer oder Gruppen Zugriff.
Reader | Alle Ressourcen mit Ausnahme der geheime Schlüssel können angezeigt werden.
Benutzerzugriffsadministrator | Kann anzeigen, alle Ressourcen, Rollen erstellen, verwalten und erstellen/verwalten Tickets unterstützen.

Finden Sie unter [einer rollenbasierten Zugriffssteuerung in Microsoft Azure-Portal](../role-based-access-control-configure.md).

Sie können problemlos hinzufügen von Benutzern und Zuweisen von bestimmten Rollen an die API-App. Sie zeigt das Portal die Benutzer, die Zugriff und ihrer zugewiesenen Rolle verfügen.

- Wählen Sie **Benutzer** zum Hinzufügen eines Benutzers eine Rolle zuweisen und Entfernen eines Benutzers.
- Wählen Sie **Rollen** um alle Benutzer in einer bestimmten Rolle zu sehen, einer Rolle einen Benutzer hinzufügen und Entfernen eines Benutzers aus einer Rolle. 


## Zugreifen auf API-Apps im Server-Explorer in Visual Studio

> [AZURE.NOTE]Dieser Abschnitt gilt für benutzerdefinierte API-Anwendungen, die von Ihnen erstellt werden. Es gilt nicht für die integrierte Microsoft-Connectors und API-apps, die auf dem Markt verfügbar.

Im Server-Explorer von Visual Studio können Sie eine Remotedebuggingsitzung starten, Streamingprotokolle anzeigen und auf einen Menüeintrag klicken, der das Blatt "API-App" im Portal öffnet.

Um einen API-Anwendung im Server-Explorer zu erhalten, klicken Sie auf **Azure > Anwendungsdiensts > [der Ressourcengruppenname] > [der API-Name]**:

![](./media/app-service-api-manage-in-portal/se.png)

## Weitere Informationen

[Was sind die API-Anwendungen?](app-service-api-apps-why-best-platform.md)<br/> [Rollenbasierte Zugriffssteuerung in Microsoft Azure-portal](../role-based-access-control-configure.md)<br/>
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->