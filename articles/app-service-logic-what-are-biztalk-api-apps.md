<properties 
	pageTitle="Was sind Connectors und BizTalk-API-Apps?" 
	description="Erfahren Sie mehr über API-Apps, Connectors und BizTalk-API-Apps" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="joshtwist" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="jtwist"/>

# Was sind Connectors und BizTalk-API-Apps?

Azure App Services basiert auf dem Prinzip der Erweiterbarkeit und allgemeinen Konnektivität mittels API-Apps. Ein  *Connector* ist eine Art API-App mit Fokus auf Konnektivität. Connectors werden wie alle anderen API-Apps von Web-Apps, Mobile Apps und Logik-Apps verwendet. Connectors vereinfachen Verbindungen mit vorhandenen Diensten, helfen beim Verwalten der Authentifizierung und bieten Überwachungs-, Analyse- und weitere Funktionen.

Entwickler können eigene Apps-API erstellen und privat bereitstellen und künftig im Marketplace veröffentlichen und kommerziell verwerten. 

![API Apps Marketplace](./media/app-service-learn-about-flows-preview/Marketplace.png)

Um die Entwicklung von Lösungen mit Azure App Services zu beschleunigen, hat das Microsoft Azure-Team dem Marketplace verschiedene Connectors für zahlreiche gängige Szenarien hinzugefügt. Darüber hinaus sind zum Erweitern der Funktionalität von App Services für komplexe Integrationsszenarien auch verschiedene Premium- und BizTalk-Funktionen verfügbar.

[Liste der Connectors und API-Apps in Microsoft Azure App Service](app-service-logic-connectors-list.md)


## Protokollconnectors
App Services basiert auf Internet-Technologie und bevorzugt die Kommunikation über HTTP und gemeinsame Nutzung von Metadaten über offene Formate wie Swagger. Unternehmen müssen selbstverständlich über eine Vielzahl von Protokollen kommunizieren können. Die Protokollconnectors können Lücken schließen und machen die Kommunikation mit z. B. FTP-, SFTP-, POP3-/IMAP-, SMTP- und SOAP-Diensten so einfach wie einen HTTP-Aufruf.  

[Protokollconnectors in Microsoft Azure App Service](app-service-logic-protocol-connectors.md)


## SaaS-Connectors
SaaS-Connectors von App Services bieten eine einfache Möglichkeit zum Verbinden Ihrer Web-, mobilen und Logik-Apps mit einigen der größten SaaS-Anbieter, so z. B. mit Office 365, SalesForce, Sugar CRM, OneDrive, DropBox, Marketo und sogar Facebook.

[Connectors für soziale Netzwerke in Microsoft Azure App Service](app-service-logic-social-connectors.md)

[App- und Data Services-Connectors in Microsoft Azure App Service](app-service-logic-data-connectors.md)


## Premiumconnectors 
Die Premiumconnectors ermöglichen eine weitere Integration von App Services mit Enterprise-Anwendungen wie u. a. SAP, Siebel, Oracle und DB2.

[Enterprise-Connectors in Microsoft Azure App Service](app-service-logic-enterprise-connectors.md)


## BizTalk-API-Apps
Das Entwickeln geschäftskritischer Anwendungen erfordert mehr als bloß Konnektivität. Basierend auf der branchenweit führenden Integrationsplattform von Microsoft - BizTalk Server - bieten BizTalk-API-Apps erweiterte Integrationsmöglichkeiten, die einfach in Ihre Web-, mobilen und Logik-Apps eingeklinkt werden können. Schließt Batchverarbeitung und deren Aufhebung, VETR (Validierung, Extrahierung, Transformation, Routing) sowie Unterstützung für EDI-Formate wie X12, EDIFACT und AS2 ein.

[Business-to-Business-Connectors und API-Apps in Microsoft Azure App Service](app-service-logic-b2b-connectors.md)<br/>

[BizTalk-Integrations-API-Apps in Microsoft Azure App Service](app-service-logic-integration-connectors.md)

<!--HONumber=49-->