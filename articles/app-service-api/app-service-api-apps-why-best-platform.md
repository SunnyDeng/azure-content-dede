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

API-Apps bieten eine leistungsfähige Plattform sowie ein Ökosystem zum Erstellen, Hosten, Nutzen und Bereitstellen von APIs in der Cloud und in lokalen Systemen. Stellen Sie Ihre API als API-App bereit, und profitieren Sie von einer für Unternehmen geeigneten Sicherheit, einfacher Zugriffssteuerung, hybrider und SaaS-Konnektivität, automatischer SDK-Generierung und nahtloser Integration in [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

API-Apps sind Teil des [Azure App Service](../app-service/app-service-value-prop-what-is.md), der auch Web-Apps, mobile Apps und Logik-Apps umfasst.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## Warum API-Apps?

API-Apps bieten Funktionen zum Entwickeln, bereitstellen, veröffentlichen, nutzen und verwalten von RESTful-Web-APIs. App Service stellt die folgenden Funktionen bereit, die heute als öffentliche Vorschau verfügbar sind:

- **Einfache Nutzung** – Die integrierte [Swagger](http://swagger.io/)-Unterstützung ermöglicht es, dass Ihre APIs auf einfache Weise von zahlreichen Clients genutzt werden können. Über das API-Apps SDK kann Clientcode für Ihre APIs in zahlreichen verschiedenen Sprachen generiert werden, darunter C#, Java und Javascript.

- **Einfache Zugriffssteuerung** – Integrierte Authentifizierungsdienste bieten Unterstützung für Azure Active Directory oder Drittanbieterdienste wie z. B. Facebook und Twitter. Sie können eine API-App vor einem nicht authentifizierten Zugriff schützen, ohne Änderungen an Ihrem Code vornehmen zu müssen. Wenn Sie mit den von [Azure Mobile Services](../mobile-services-windows-dotnet-how-to-use-client-library.md#authentication) bereitgestellten Authentifizierungsdiensten vertraut sind: API-Apps baut auf diesem Framework auf und weitet es auf APIs aus, die über API-Apps gehostet werden. Mit dem App Service SDK können Sie außerdem eine vereinfachte Syntax für Autorisierungscode verwenden. Weitere Informationen finden Sie unter [Schützen einer API-App](app-service-api-dotnet-add-authentication.md).

- **Einfache Verbindung mit SaaS-Plattformen** – Die [Connector-API-Apps](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) im Azure Marketplace werden von Microsoft und Drittanbietern bereitgestellt, um den erforderlichen Code zur Interaktion mit SalesForce, Office 365, Twitter, Facebook, Dropbox und vielen weiteren Diensten zu vereinfachen.

- **Integration in Logik-Apps** – Von Ihnen erstellte API-Apps können von [App Service-Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) genutzt werden.

- **Visual Studio-Integration** – Dedizierte Tools in Visual Studio optimieren das [Erstellen](app-service-dotnet-create-api-app.md), [Bereitstellen](app-service-dotnet-deploy-api-app.md), [Debuggen](app-service-dotnet-remotely-debug-api-app) und Verwalten von API-Apps.

Nutzen Sie eigene APIs in unveränderter Form: Es ist nicht erforderlich, den Code vorhandener APIs zu ändern, um all die API-App-Features zu nutzen – stellen Sie lediglich Ihren Code in einer API-App bereit. Verwenden Sie ASP.NET, Java, PHP, Node.js oder Python für Ihre APIs.

Darüber hinaus enthalten API-Apps [Features von App Service-Web-Apps](../app-service-web/app-service-web-overview.md).

### API-Apps-Features, die in Zukunft verfügbar sein werden

Bald wird für die API-Apps-Plattform ein umfangreiches Ökosystem an APIs bereitstehen, über das die Freigabe Ihres Codes ganz einfach ist:

- **Öffentlicher und privater Marketplace** – Mit dem [Azure Marketplace](http://azure.microsoft.com/marketplace/) wird die Suche nach und das Bereitstellen vorkonfigurierter, von Microsoft und Drittanbietern entwickelten API-Apps für Ihr Azure-Abonnement vereinfacht. Es wird außerdem möglich sein, selbstentwickelte API-Apps zu paketieren und zu veröffentlichen, damit andere Entwickler diese für ihre Azure-Abonnements bereitstellen können. Wenn Sie Ihre APIs im Azure Marketplace veröffentlichen, können Sie festlegen, dass sie nur für Mitglieder Ihrer Organisation sichtbar sind. 

- **Automatisches Bereitstellen von Abhängigkeiten** – Immer dann, wenn Sie eine API-App aus dem Marketplace für Ihr Azure-Abonnement bereitstellen, stellt Azure automatisch abhängige API-Apps bereit und erstellt die erforderlichen Ressourcen. Ein API-App-Paket gibt an, von welchen API-Apps es abhängt und welche Azure-Ressourcen erforderlich sind.

- **Automatische Updates** – Wenn Sie den Code für ein API-App-Paket aktualisieren, das Sie freigegeben haben, können Sie das Update per Push an alle Benutzer weitergeben, die Ihre API-App installiert haben und ausführen. Dies funktioniert für Änderungen ohne Funktionsbeeinträchtigung (non-breaking) und für Benutzer, die den Empfang von Updates aktiviert haben.

Viele dieser Features, beispielsweise Marketplace und automatische Updates, stehen für die von Microsoft bereitgestellten API-Apps bereits zur Verfügung.

>[AZURE.NOTE] [Azure API Management](/services/api-management/) ist ein separater Dienst, der Features wie Endpunktkonsolidierung und Drosselung bereitstellt. Sie können API Management mit API-Apps einsetzen.
>
>API-Apps befindet sich derzeit in der öffentlichen Vorschau. API-Apps setzen auf [App Service-Web-Apps](../app-service-web/app-service-web-overview.md) auf, einem allgemein verfügbaren Dienst zum Erstellen und Hosten sicherer, unternehmenskritischer Anwendungen weltweit. Wenn Sie jetzt nach einem allgemein verfügbaren Dienst zum Erstellen Ihrer API suchen, ist Web-Apps eine hervorragende Option. Sobald API-Apps allgemein verfügbar sind, wird eine Möglichkeit bereitgestellt, vorhandene Web-Apps zu migrieren und die Funktionen von API-Apps zu nutzen.

## API-Apps – Konzepte ##

- **Gateway** – Eine Web-App, die API-Verwaltungsfunktionen wie z. B. die Authentifizierung für alle API-Apps in einer Ressourcengruppe übernimmt. 
- **Swagger** – Ein Framework für die interaktive Dokumentation und Erkennung einer RESTful-API, wird in API-Apps standardmäßig verwendet. Weitere Informationen finden Sie unter [http://swagger.io/](http://swagger.io/).
- **Connectors** – Eine Art von API-App, welche die Verbindung mit SaaS-Plattformen wie z. B. Salesforce und Office 365 vereinfacht. Weitere Informationen finden Sie unter [Was sind Connectors und BizTalk-API-Apps?](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)
- **Trigger** – Eine REST-API, die von [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) aufgerufen werden kann, um einen Workflowprozess zu initiieren, wenn eine bestimmte Bedingung erfüllt ist. Beispielsweise kann eine API-App eine Methode bereitstellen, die von der Logik-App regelmäßig aufgerufen wird, um in einem Twitter-Feed nach einem bestimmten Schlagwort zu suchen. Weitere Informationen finden Sie unter [API-App-Trigger](app-service-api-dotnet-triggers.md).
- **Aktion** – Eine REST-API, die von [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) zum Verarbeiten von Daten aufgerufen werden kann, nachdem ein Workflow durch einen Trigger gestartet wurde. Beispielsweise könnte eine API-App eine Methode bereitstellen, die die Logik-App als Reaktion auf einen Tweet aufruft, der über den Twitter-Trigger gefunden wurde. Aktionen sind API-Methoden, die durch eine Swagger-API-Definition verfügbar gemacht werden.

## Erste Schritte

Arbeiten Sie zum Einstieg in API-Apps das Lernprogramm [Erstellen einer API-App](app-service-dotnet-create-api-app.md) durch.

Eine Liste der bekannten Probleme mit API-Apps finden Sie in [diesem MSDN-Forumsbeitrag](https://social.msdn.microsoft.com/Forums/de-de/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service](../app-service/app-service-value-prop-what-is.md).

 

<!---HONumber=62-->