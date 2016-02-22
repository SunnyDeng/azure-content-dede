<properties 
	pageTitle="Bereitstellen von Anwendungen auf Azure App Service." 
	description="Erfahren Sie, wie Sie Anwendungen in App Service bereitstellen" 
	keywords="App Service, Azure App Service, bereitstellen, Bereitstellung"
	services="app-service" 
	documentationCenter="" 
	authors="dariagrigoriu" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/09/2016" 
	ms.author="dariagrigoriu"/>

# Übersicht zur Azure App-Service Bereitstellung

Azure App Service bietet umfassende integrierte Funktionen zum Erstellen von leistungsstarken und flexiblen Bereitstellungsworkflows. Die App-Bereitstellung kann Optionen für fortlaufende Integration oder Veröffentlichung über die lokale Quellcodeverwaltung sowie WebDeploy und FTP wiederverwenden. Die empfohlene Methode zum Bereitstellen von Produktions-Apps ist der Austausch eines Bereitstellungsslots. Bereitstellungsslots stellen Staging- und Integrationsumgebungen dar, die Produktions-Apps zugeordnet sind. Bereitstellungsslots können konfiguriert und für den Empfang von Webdatenverkehr zur Überprüfung eingerichtet werden und bei Bedarf auf Produktions-Bereitstellung umgestellt werden. Hierbei entstehen durch automatisiertes Anfahren keine Ausfallzeiten. Die Schritte des Bereitstellungsworkflows können einfach über Release Management-Produkte wie z. B. Visual Studio Release Management automatisiert werden. Dies ist nützlich für die Koordination mit anderen Lösungsressourcen (z. B. Datenspeicher), Wiederholung sowie Replikation über mehrere Einheiten der Bereitstellung.

[AZURE.INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]

<!---HONumber=AcomDC_0211_2016-->