<properties 
	pageTitle="" 
	description="Informationen zu den ersten Schritten mit dem Assistenten für Azure Active Directory (Web-API-Projekte)" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="patshea123"/>

# Erste Schritte mit Azure Active Directory \(Web-API-Projekte\)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##Erfordern von Authentifizierung für den Zugriff auf Controller
 
Alle Controller in Ihrem Projekt wurden mit dem Attribut **Authorize** versehen. Dieses Attribut erfordert, dass der Benutzer vor dem Zugriff auf die durch diese Controller definierten APIs authentifiziert werden muss. Wenn Sie anonymen Zugriff auf diesen Controller erlauben möchten, entfernen Sie dieses Attribut vom Controller. Wenn Sie die Berechtigungen präziser festlegen möchten, wenden Sie das Attribut auf jede Methode an, die Autorisierung erfordert, anstatt es auf die Controllerklasse anzuwenden.

[Weitere Informationen zu Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=July15_HO5-->