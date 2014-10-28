<properties linkid="mobile-services-dotnet-backend-how-to-use" urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="Use the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn the details of the .NET Backend programming model for Azure Mobile Services, including how to work with table data, APIs, authentication, and scheduled jobs" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>
# Verwenden des Mobile Services .NET-Back-End

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET-Backend" class="current">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript-Backend">JavaScript-Backend</a></div>

Dieser Artikel enthält ausführliche Informationen und Beispiele zum Arbeiten mit .NET-Back-End in Azure Mobile Services. Der Artikel ist in folgende Abschnitte unterteilt:

+ [Einführung][Einführung]
+ [Tabellenvorgänge][Tabellenvorgänge]

## <a name="intro"></a>Einführung

Das Mobile Services .NET-Back-End ermöglicht es, eine vielseitige Back-End-Geschäftslogik mithilfe der [ASP.NET Web API][ASP.NET Web API] und Ihrer bevorzugten .NET-Sprache zu schreiben. Mobile Services stellt eine kleine Programmiermodelloberfläche als [NuGet-Pakete][NuGet-Pakete] bereit, die nahtlosen Zugriff auf Ihren Dienst von den plattformübergreifenden Mobile Services-Client-SDKs für Windows, Android, iOS usw. ermöglicht. Diese APIs sorgen auch dafür, dass die Unterstützung für das Hinzufügen von Authentifizierung und Pushbenachrichtigungen so einfach wie möglich gemacht wird. Der größte Teil des Programmiermodells basiert allerdings auf Web API, und Entwickler, die damit vertraut sind, sollten sich gleich zuhause fühlen.

## <a name="table-scripts"></a>Tabellenvorgänge

Das Mobile Services .NET-Back-End bietet eine universelle „Tabellen“-Abstraktion, die eine CRUD-basierte HTTP-API für die Datenbankspeicherung darstellt. Diese Abstraktion trennt Überlegungen zur Datenspeicherung von der Geschäftslogik und ermöglicht Ihrem mobilen Dienst, unterschiedliche Datenspeicher in einem konsistenten JSON-basierten Sendeformat bereitzustellen, das das plattformübergreifende Mobile Services-Client-SDK unmittelbar versteht.

Am Stamm dieses Programmiermodells befindet sich die Klasse [**TableController<t>**][**TableController<t>**], die einfach eine reguläre Web API [**ApiController**][**ApiController**] ist, die für ein CRUD-Datenzugriffsmuster angepasst wurde. Der **TableController** kann eine Vielzahl von Datenspeichern verwenden, darunter SQL (über [Entity Framework][Entity Framework]), [Azure Table Storage][Azure Table Storage], [MongoDB][MongoDB] oder ein eigener, angepasster Speicher.

[.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-how-to-use/ ".NET-Backend"
[JavaScript-Backend]: /de-de/documentation/articles/mobile-services-how-to-use-server-scripts/ "JavaScript-Backend"
[Einführung]: #intro
[Tabellenvorgänge]: #table-scripts
[ASP.NET Web API]: http://www.asp.net/web-api
[NuGet-Pakete]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
[**TableController<t>**]: http://msdn.microsoft.com/library/dn643359.aspx
[**ApiController**]: http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx
[Entity Framework]: http://msdn.microsoft.com/data/ef.aspx
[Azure Table Storage]: http://azure.microsoft.com/documentation/services/storage/
[MongoDB]: http://www.mongodb.org
