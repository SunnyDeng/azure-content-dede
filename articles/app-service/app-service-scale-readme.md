<properties 
	pageTitle="Azure App Service: Skalierung von App Service-Anwendungen" 
	description="Erfahren Sie, wie Sie Anwendungen in App Service skalieren" 
	keywords="App-Dienst, Azure App Service, Skalierung, skalierbar, App Services-Plan, App Service-Kosten"
	services="app-service" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="byvinyal"/>
	
# Azure App Service: Skalierung von App Service-Anwendungen

Anwendungen in Azure App Service können [sehr groß werden](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/), doch das Skalieren einer Anwendung ist komplex, und es gibt keine Lösung, die sich für alle Probleme eignet. 3 Faktoren spielen beim korrekten Skalieren Ihrer Anwendung eine Rolle und tragen so zum Erfolg dieser bei:

1. Lernen Sie die Architektur Ihrer App und ihre Schwächen kennen.
	* Ist Ihre Anwendung zustandsbehaftet? Zustandslos?
	* Aus welchen Komponenten besteht Ihre Anwendung?
		* Welche Engpässe weist die Anwendung auf? 
	* Wo treten zuerst Fehler auf, wenn Ihre Anwendung ausgelastet wird?
2. Machen Sie sich die zu erwartende Auslastung und die Anforderungen an die Leistung bewusst.
	* Soll die Anwendung von tausend Benutzern verwendet werden? Oder einer Million?
	* Ist der entstehende Datenverkehr globalen Ursprungs oder auf einen geographischen Standort begrenzt?
	* Gibt es saisonale Abweichungen? Spitzenzeiten für den Datenverkehr? 
	* Wie schnell soll die App reagieren? 1 Sekunde? 1 Millisekunde?
3. Lernen Sie die Hostplattform Ihrer App kennen und nutzen Sie diese optimal.
	* Welche Features sollte Ich nutzen, um meine Skalierungsziele zu erreichen?
	
Dieser Abschnitt hilft Ihnen dabei, all diese Faktoren zu verstehen und eine Strategie zu entwickeln, bei der die benötigten App Service-Funktionen optimal genutzt werden, damit Sie ihre Skalierungsziele erreichen.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]

<!---HONumber=AcomDC_0211_2016-->