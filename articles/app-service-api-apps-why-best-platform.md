<properties 
	pageTitle="Was sind API-Apps?" 
	description="Erfahren Sie, warum Azure App Service die beste Plattform zum Entwickeln, Veröffentlichen und Hosten von RESTful-APIs ist." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="tdykstra"/>

# Was sind API-Apps?

API-Apps sind Bestandteil des [Azure App Service](app-service-value-prop-what-is.md)-Pakets, das außerdem Web-Apps, mobile Apps und Logik-Apps umfasst.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

Die API-Apps in diesem Paket bieten eine leistungsfähige Plattform sowie ein Ökosystem zum Erstellen, Nutzen und Bereitstellen von APIs in der Cloud und in lokalen Systemen.

>[AZURE.NOTE]API-Apps befindet sich derzeit in der Vorschau. API-Apps setzen auf [App Service-Web-Apps](app-service-web-overview.md) auf, einem allgemein verfügbaren Dienst zum Erstellen und Hosten sicherer, unternehmenskritischer Anwendungen weltweit. Wenn Sie jetzt nach einem allgemein verfügbaren Dienst zum Erstellen Ihrer API suchen, ist Web-Apps eine hervorragende Option. Sobald API-Apps allgemein verfügbar ist, wird eine Möglichkeit bereitgestellt, vorhandene Web-Apps zu migrieren und die Funktionen von API-Apps zu nutzen.

## Warum API-Apps?

Eine API-App ist eine [App Service-Web-App](app-service-web-overview.md) mit zusätzlichen Funktionen zur Verbesserung von Entwicklung, Bereitstellung, Veröffentlichung, Nutzung, Verwaltung und Vermarktung von RESTful-Web-APIs.

Dies bedeutet, dass API-Apps mit Web-Apps alle Webhostingfunktionen gemeinsam haben, die über die Azure App Service-Plattform bereitgestellt werden:

- Automatisches Betriebssystempatching
- Sicherheit auf Unternehmensniveau
- Hohe Verfügbarkeit
- Automatische Skalierung und Lastenausgleich
- Verwenden von [WebJobs](websites-webjobs-resources.md) zur Hintergrundverarbeitung
- Schnelle und einfache Bereitstellung, zahlreiche Bereitstellungsoptionen – Informationen zu den verfügbaren Bereitstellungsoptionen für API-Apps finden Sie unter [Bereitstellen von Web-Apps in Azure App Service](web-sites-deploy.md). 

Die zusätzlichen Features der API-Apps-Plattform vereinfachen das Entwickeln, Hosten und Nutzen von APIs:

- **Nutzen Sie eigene APIs in unveränderter Form** – Verwenden Sie ASP.NET, Java, PHP, Node.js oder Python für Ihre APIs. Ihre APIs können die Vorteile der API-Apps-Plattform nutzen, ohne hierzu verändert werden zu müssen.

- **Einfache Zugriffssteuerung** – Konfigurieren Sie Ihre APIs für eine Authentifizierung durch Azure Active Directory oder Drittanbieterdienste wie z. B. Facebook und Twitter, ohne dass hierzu Änderungen an Ihrem Code erforderlich sind. Verwenden Sie eine vereinfachte Syntax für den Autorisierungscode. Weitere Informationen finden Sie unter [Schützen einer API-App](app-service-api-dotnet-add-authentication.md).

- **Einfache Verbindung mit SaaS-Plattformen** – Die [Connector-API-Apps](app-service-logic-what-are-biztalk-api-apps.md) im Azure Marketplace werden von Microsoft und Drittanbietern bereitgestellt, um den erforderlichen Code zur Interaktion mit SalesForce, Office 365, Twitter, Facebook, Dropbox und vielen weiteren Diensten zu vereinfachen.

- **Zugriff auf lokale Daten** – API-Apps können APIs verfügbar machen, die über [Hybridverbindungen](integration-hybrid-connection-overview.md) und [VNET](web-sites-integrate-with-vnet.md) Daten aus Ihren eigenen Datencentern nutzen.

- **Einfache Nutzung** – Verwenden Sie die integrierte [Swagger](http://swagger.io/)-Unterstützung, damit Ihre APIs auf einfache Weise von zahlreichen Clients genutzt werden können. Oder nutzen Sie das API-Apps SDK, um Clientcode für Ihre APIs in zahlreichen verschiedenen Sprachen zu generieren, darunter C#, Java und Javascript.

- **Integration in Logik-Apps** – Von Ihnen erstellte API-Apps können von App Service-Logik-Apps genutzt werden.

- **Visual Studio-Integration** – Dedizierte Tools in Visual Studio optimieren das [Erstellen](app-service-dotnet-create-api-app.md), [Bereitstellen](app-service-dotnet-deploy-api-app.md), [Debuggen](app-service-dotnet-remotely-debug-api-app) und Verwalten von API-Apps.

Bald wird für die API-Apps-Plattform ein umfangreiches Ökosystem an APIs bereitstehen, über das die Freigabe Ihres Codes ganz einfach ist:

- **Öffentlicher und privater Marketplace** – Mit dem [Azure Marketplace](http://azure.microsoft.com/marketplace/) wird die Suche nach und das Bereitstellen vorkonfigurierter, von Microsoft und Drittanbietern entwickelten API-Apps für Ihr Azure-Abonnement vereinfacht. Es wird außerdem möglich sein, selbstentwickelte API-Apps zu paketieren und zu veröffentlichen, damit andere Entwickler diese für ihre Azure-Abonnements bereitstellen können. Wenn Sie Ihre APIs im Azure Marketplace veröffentlichen, können Sie festlegen, dass sie nur für Mitglieder Ihrer Organisation sichtbar sind. 

- **Automatisches Bereitstellen von Abhängigkeiten** – Immer dann, wenn Sie eine API-App aus dem Marketplace für Ihr Azure-Abonnement bereitstellen, stellt Azure automatisch abhängige API-Apps bereit und erstellt die erforderlichen Ressourcen. Ein API-App-Paket gibt an, von welchen API-Apps es abhängt und welche Azure-Ressourcen erforderlich sind.

- **Automatische Updates** – Wenn Sie den Code für ein API-App-Paket aktualisieren, das Sie freigegeben haben, können Sie das Update per Push an alle Benutzer weitergeben, die Ihre API-App installiert haben und ausführen. Dies funktioniert für Änderungen ohne Funktionsbeeinträchtigung (non-breaking) und für Benutzer, die den Empfang von Updates aktiviert haben.

Viele dieser Features, beispielsweise Marketplace und automatische Updates, stehen für die von Microsoft bereitgestellten API-Apps bereits zur Verfügung.

## API-Apps – Konzepte ##

- **Gateway** – Eine Web-App, die API-Verwaltungsfunktionen wie z. B. die Authentifizierung für alle API-Apps in einer Ressourcengruppe übernimmt. 
- **Swagger** – Ein Framework für die interaktive Dokumentation und Erkennung einer RESTful-API, wird in API-Apps standardmäßig verwendet. Weitere Informationen finden Sie unter [http://swagger.io/](http://swagger.io/).
- **Connectors** – Eine Art von API-App, welche die Verbindung mit SaaS-Plattformen wie z. B. Salesforce und Office 365 vereinfacht. Weitere Informationen finden Sie unter [Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md)
- **Trigger** – Eine REST-API, die von [Logik-Apps](app-service-logic-what-are-logic-apps.md) aufgerufen werden kann, um einen Workflowprozess zu initiieren, wenn eine bestimmte Bedingung erfüllt ist. Beispielsweise kann eine API-App eine Methode bereitstellen, die von der Logik-App regelmäßig aufgerufen wird, um in einem Twitter-Feed nach einem bestimmten Schlagwort zu suchen. Weitere Informationen finden Sie unter [API-App-Trigger](app-service-api-dotnet-triggers.md).
- **Aktion** – Eine REST-API, die von [Logik-Apps](app-service-logic-what-are-logic-apps.md) zum Verarbeiten von Daten aufgerufen werden kann, nachdem ein Workflow durch einen Trigger gestartet wurde. Beispielsweise könnte eine API-App eine Methode bereitstellen, die die Logik-App als Reaktion auf einen Tweet aufruft, der über den Twitter-Trigger gefunden wurde. Aktionen sind API-Methoden, die durch eine Swagger-API-Definition verfügbar gemacht werden.

## Erste Schritte

Arbeiten Sie zum Einstieg in API-Apps das Lernprogramm [Erstellen einer API-App](app-service-dotnet-create-api-app.md) durch.

Eine Liste der bekannten Probleme mit API-Apps finden Sie in [diesem MSDN-Forumsbeitrag](https://social.msdn.microsoft.com/Forums/en-US/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service](app-service-value-prop-what-is.md).


<!--HONumber=54-->