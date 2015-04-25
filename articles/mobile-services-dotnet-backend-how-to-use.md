<properties 
	pageTitle="Verwenden des Mobile Services .NET-Back-End - Azure Mobile Services" 
	description="Erfahren Sie mehr über die Details des .NET-Back-End-Programmierungsmodells für Azure Mobile Services, beispielsweise zum Arbeiten mit Tabellendaten, APIs, Authentifizierung und geplanten Aufträgen." 
	services="" 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="mahender"/>
# Verwenden des Mobile Services .NET-Back-End

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">.NET-Back-End</a> | <a href="/de-de/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">JavaScript-Back-End</a></div>

Dieser Artikel enthält ausführliche Informationen und Beispiele zum Arbeiten mit .NET-Back-End in Azure Mobile Services. Der Artikel ist in folgende Abschnitte unterteilt:

+ [Einführung](#intro)
+ [Tabellenvorgänge](#table-scripts)

##<a name="intro"></a>Einführung

Das Mobile Services .NET-Back-End ermöglicht es, eine vielseitige Back-End-Geschäftslogik mithilfe der [ASP.NET Web API](http://www.asp.net/web-api) und Ihrer bevorzugten .NET-Sprache zu schreiben. Mobile Services stellt eine kleine Programmiermodelloberfläche als [NuGet-Pakete](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22) bereit, die nahtlosen Zugriff auf Ihren Dienst von den plattformübergreifenden Mobile Services-Client-SDKs für Windows, Android, iOS usw. ermöglicht. Diese APIs sorgen auch dafür, dass die Unterstützung für das Hinzufügen von Authentifizierung und Pushbenachrichtigungen so einfach wie möglich gemacht wird. Der größte Teil des Programmiermodells basiert allerdings auf Web API, und Entwickler, die damit vertraut sind, sollten sich gleich zuhause fühlen. 

##<a name="table-scripts"></a>Tabellenvorgänge

Das Mobile Services .NET-Back-End bietet eine universelle "Tabellen"-Abstraktion, die eine CRUD-basierte HTTP-API für die Datenbankspeicherung darstellt. Diese Abstraktion trennt Überlegungen zur Datenspeicherung von der Geschäftslogik und ermöglicht Ihrem mobilen Dienst, unterschiedliche Datenspeicher in einem konsistenten JSON-basierten Sendeformat bereitzustellen, das das plattformübergreifende Mobile Services-Client-SDK unmittelbar versteht. 

Am Stamm dieses Programmiermodells befindet sich die Klasse [**TableController<T>**](http://msdn.microsoft.com/library/dn643359.aspx), die einfach eine reguläre Web API [**ApiController**](http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx) ist, die für ein CRUD-Datenzugriffsmuster angepasst wurde. Der **TableController** kann eine Vielzahl von Datenspeichern verwenden, darunter SQL (über [Entity Framework](http://msdn.microsoft.com/data/ef.aspx)), [Azure Table Storage](http://azure.microsoft.com/documentation/services/storage/), [MongoDB](http://www.mongodb.org) oder ein eigener, angepasster Speicher.


<!--HONumber=42-->
