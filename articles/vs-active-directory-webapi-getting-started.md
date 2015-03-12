<properties 
	pageTitle="" 
	description="Informationen über die ersten Schritte mit dem Azure Active Directory-Assistenten (Web-API-Projekte)"
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Erste Schritte](/documentation/articles/vs-active-directory-webapi-getting-started/)
> - [Was ist passiert?](/documentation/articles/vs-active-directory-webapi-what-happened/)


##Erste Schritte mit Azure Active Directory (Web-API-Projekte)

#####Erfordern von Authentifizierung für den Zugriff auf Controller
 
Alle Controller in Ihrem Projekt wurden mit dem Attribut **Authorize** versehen. Dieses Attribut erfordert, dass der Benutzer vor dem Zugriff auf die durch diese Controller definierten APIs authentifiziert werden muss. Wenn Sie anonymen Zugriff auf diesen Controller erlauben möchten, entfernen Sie dieses Attribut vom Controller. Wenn Sie die Berechtigungen präziser festlegen möchten, wenden Sie das Attribut auf jede Methode an, die Autorisierung erfordert, anstatt es auf die Controllerklasse anzuwenden.

[Weitere Informationen zu Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
