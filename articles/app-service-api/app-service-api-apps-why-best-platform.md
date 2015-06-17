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

API-Apps sind Bestandteil des [Azure App Service](../app-service/app-service-value-prop-what-is.md)-Pakets, das außerdem Web-Apps, mobile Apps und Logik-Apps umfasst.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

Der API-Apps Teil dieser Suite stellt eine umfassende Plattform und das Ökosystem für erstellen, hosten, verarbeiten und Verteilen von APIs in der Cloud und lokalen bereit.

>[AZURE.NOTE] [Azure API Management](/ Services/api-Verwaltung /) ist ein separater Dienst, die Funktionen wie z. B. Endpunkt Konsolidierung und Einschränkung bietet. Sie können die API Management mit API-Apps verwenden.
>
>API-Apps befindet sich derzeit in der Vorschau. API-Apps setzen auf [App Service-Web-Apps](../app-service-web/app-service-web-overview.md) auf, einem allgemein verfügbaren Dienst zum Erstellen und Hosten sicherer, unternehmenskritischer Anwendungen weltweit. Wenn Sie jetzt nach einem allgemein verfügbaren Dienst zum Erstellen Ihrer API suchen, ist Web-Apps eine hervorragende Option. API-Apps GA verläuft, müssen wir einen Pfad angeben, für die Teilnahme an vorhandenen Web-apps und Nutzung von Funktionen der API-Apps.

## Warum API-Apps?

Eine API-App ist eine [App Service-Web-App](../app-service-web/app-service-web-overview.md) mit zusätzlichen Funktionen zur Verbesserung von Entwicklung, Bereitstellung, Veröffentlichung, Nutzung, Verwaltung und Vermarktung von RESTful-Web-APIs.

### Web-app-Funktionen für Webdienste

Können Sie eine Webanwendung um einen Webdienst zu hosten, und Sie erhalten alle Webhosting-Funktionen der Azure-App-Service-Plattform:

- Automatisches Betriebssystempatching
- Sicherheit auf Unternehmensniveau
- Hohe Verfügbarkeit
- Automatische Skalierung und Lastenausgleich
- Verwenden von [WebJobs](../app-service-web/websites-webjobs-resources.md) zur Hintergrundverarbeitung
- Schnelle und einfache Bereitstellung und [viele Optionen für kontinuierliche Bereitstellung](../app-service-web/web-sites-deploy.md)
- Zugriff auf lokales Daten mithilfe von [hybridverbindungen](../integration-hybrid-connection-overview.md) und [VNET](../app-service-web/web-sites-integrate-with-vnet.md).

### API-Apps Funktionen jetzt in der Vorschau verfügbar

Wenn Sie API-Apps anstelle von Webanwendungen zum Hosten von Webdiensten verwenden, bietet die App-Dienst zusätzliche Funktionen:

- **Leicht Verbrauch** – integrierte [Swagger](http://swagger.io/) Unterstützung macht Ihre APIs verständlich sind eine Vielzahl von Clients. SDK-Apps-API kann Clientcode für Ihre APIs in einer Vielzahl von Sprachen wie c\#, Java und Javascript generieren.

- **Einfache Zugriffssteuerung** – integrierte Authentifizierungsdienste unterstützen Azure Active Directory oder Drittanbieter-services wie Facebook und Twitter. Sie können eine API-app Fgrom nicht authentifizierten Zugriff ohne Änderungen am Code schützen. Wenn Sie mit der Authentifizierungsdienste von vertraut [Azure Mobile Services](../mobile-services-windows-dotnet-how-to-use-client-library.md#authentication), API-apps baut auf dieses Framework und erweitert ihn auf Webdienste von API-Anwendungen gehostet werden. Das App-SDK können Sie eine vereinfachte Syntax für Autorisierungscode verwenden. Weitere Informationen finden Sie unter [Schützen einer API-App](app-service-api-dotnet-add-authentication.md).

- **Einfache Verbindung mit SaaS-Plattformen** – Die [Connector-API-Apps](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) im Azure Marketplace werden von Microsoft und Drittanbietern bereitgestellt, um den erforderlichen Code zur Interaktion mit SalesForce, Office 365, Twitter, Facebook, Dropbox und vielen weiteren Diensten zu vereinfachen.

- **Integration mit Logik apps** -API-apps, die Sie erstellen genutzt werden können, indem Sie [Logik für die App-Dienstanwendungen](../app-service-logic/app-service-logic-what-are-logic-apps.md).

- **Visual Studio-Integration** – Dedizierte Tools in Visual Studio optimieren das [Erstellen](app-service-dotnet-create-api-app.md), [Bereitstellen](app-service-dotnet-deploy-api-app.md), [Debuggen](app-service-dotnet-remotely-debug-api-app) und Verwalten von API-Apps.

Bringen Sie Ihre vorhandene API als-ist: Sie müssen den Code in Ihre vorhandenen APIs, um alle diese Funktionen nutzen, die nur Code bereitstellen, um einen API-Anwendung zu ändern. Sie können ASP.NET, Java, PHP, Node.js oder Python für Ihre APIs verwenden.

### Apps für die API-Funktionen in Zukunft verfügbar

Bald wird für die API-Apps-Plattform ein umfangreiches Ökosystem an APIs bereitstehen, über das die Freigabe Ihres Codes ganz einfach ist:

- **Öffentlicher und privater Marketplace** – Mit dem [Azure Marketplace](http://azure.microsoft.com/marketplace/) wird die Suche nach und das Bereitstellen vorkonfigurierter, von Microsoft und Drittanbietern entwickelten API-Apps für Ihr Azure-Abonnement vereinfacht. Es wird außerdem möglich sein, selbstentwickelte API-Apps zu paketieren und zu veröffentlichen, damit andere Entwickler diese für ihre Azure-Abonnements bereitstellen können. Wenn Sie Ihre APIs im Azure Marketplace veröffentlichen, können Sie festlegen, dass sie nur für Mitglieder Ihrer Organisation sichtbar sind. 

- **Automatisches Bereitstellen von Abhängigkeiten** – Immer dann, wenn Sie eine API-App aus dem Marketplace für Ihr Azure-Abonnement bereitstellen, stellt Azure automatisch abhängige API-Apps bereit und erstellt die erforderlichen Ressourcen. Ein API-App-Paket gibt an, von welchen API-Apps es abhängt und welche Azure-Ressourcen erforderlich sind.

- **Automatische Updates** – Wenn Sie den Code für ein API-App-Paket aktualisieren, das Sie freigegeben haben, können Sie das Update per Push an alle Benutzer weitergeben, die Ihre API-App installiert haben und ausführen. Dies funktioniert für Änderungen ohne Funktionsbeeinträchtigung (non-breaking) und für Benutzer, die den Empfang von Updates aktiviert haben.

Viele dieser Features, beispielsweise Marketplace und automatische Updates, stehen für die von Microsoft bereitgestellten API-Apps bereits zur Verfügung.

## API-Apps – Konzepte ##

- **Gateway** – Eine Web-App, die API-Verwaltungsfunktionen wie z. B. die Authentifizierung für alle API-Apps in einer Ressourcengruppe übernimmt. 
- **Swagger** – Ein Framework für die interaktive Dokumentation und Erkennung einer RESTful-API, wird in API-Apps standardmäßig verwendet. Weitere Informationen finden Sie unter [http://swagger.io/](http://swagger.io/).
- **Connectors** – Eine Art von API-App, welche die Verbindung mit SaaS-Plattformen wie z. B. Salesforce und Office 365 vereinfacht. Weitere Informationen finden Sie unter [Was sind Connectors und BizTalk-API-Apps?](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)
- **Trigger** – Eine REST-API, die von [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) aufgerufen werden kann, um einen Workflowprozess zu initiieren, wenn eine bestimmte Bedingung erfüllt ist. Beispielsweise kann eine API-App eine Methode bereitstellen, die von der Logik-App regelmäßig aufgerufen wird, um in einem Twitter-Feed nach einem bestimmten Schlagwort zu suchen. Weitere Informationen finden Sie unter [API-App-Trigger](app-service-api-dotnet-triggers.md).
- **Aktion** – Eine REST-API, die von [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) zum Verarbeiten von Daten aufgerufen werden kann, nachdem ein Workflow durch einen Trigger gestartet wurde. Beispielsweise könnte eine API-App eine Methode bereitstellen, die die Logik-App als Reaktion auf einen Tweet aufruft, der über den Twitter-Trigger gefunden wurde. Aktionen sind API-Methoden, die durch eine Swagger-API-Definition verfügbar gemacht werden.

## Erste Schritte

Arbeiten Sie zum Einstieg in API-Apps das Lernprogramm [Erstellen einer API-App](app-service-dotnet-create-api-app.md) durch.

Eine Liste der bekannten Probleme mit API-Apps finden Sie in [diesem MSDN-Forumsbeitrag](https://social.msdn.microsoft.com/Forums/en-US/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service](../app-service/app-service-value-prop-what-is.md).

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->