<properties
	pageTitle="Verschieben von Web-App-Ressourcen in eine andere Ressourcengruppe"
	description="Beschreibt die Szenarien, in denen Sie Web-Apps und App Services aus einer Ressourcengruppe in eine andere verschieben können."
	services="app-service"
	documentationCenter=""
	authors="ZainRizvi"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="zarizvi"/>
	
# Unterstützte Konfigurationen für das Verschieben

Sie können Azure-Web-App-Ressourcen mit der [ARM-API zum Verschieben von Ressourcen](../resource-group-move-resources.md) verschieben.

Azure-Web-Apps unterstützen derzeit die folgenden Szenarien beim Verschieben:

* Verschieben des gesamten Inhalts einer Ressourcengruppe (Web-Apps, App Service-Pläne und Zertifikate) in eine andere Ressourcengruppe 
	* Hinweis: In diesem Szenario darf die Zielressourcengruppe keine Microsoft.Web-Ressourcen enthalten.
* Verschieben einzelner Web-Apps in eine andere Ressourcengruppe, während sie weiterhin im aktuellen App Service-Plan gehostet werden (Der App Service-Plan bleibt in der alten Ressourcengruppe.)

<!---HONumber=Oct15_HO4-->