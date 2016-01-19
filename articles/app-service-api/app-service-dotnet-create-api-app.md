<properties
	pageTitle="Erstellen einer ASP.NET-API-App in Azure App Service | Microsoft Azure"
	description="Erfahren Sie, wie Sie mithilfe von Visual Studio 2013 eine ASP.NET-API-App in Azure App Service erstellen."
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Erstellen einer ASP.NET-API-App in Azure App Service

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Übersicht

In diesem Tutorial wird gezeigt, wie Sie ein ASP.NET-Web-API-Projekt erstellen, das zur Bereitstellung in der Cloud als [API-App in Azure App Service](app-service-api-apps-why-best-platform.md) konfiguriert wird. Informationen zum Konfigurieren eines vorhandenen Web-API-Projekts zur Bereitstellung als API-App finden Sie unter [Konfigurieren eines Web-API-Projekts als API-App](app-service-dotnet-create-api-app-visual-studio.md).

Dies ist ein schnelles und einfaches Tutorial, in dem Sie nur erfahren, wie Sie ein Visual Studio-Projekt mit einer Vorlage erstellen. Dies ist das erste Tutorial einer Reihe, in der Sie auch lernen, wie Sie das in diesem Tutorial erstellte API-App-Projekt [bereitstellen](app-service-dotnet-deploy-api-app.md) und [debuggen](../app-service-dotnet-remotely-debug-api-app.md) können. Weitere detaillierte Informationen zum Arbeiten mit API-Apps finden Sie im Abschnitt [Nächste Schritte](#next-steps) am Ende des Tutorials.

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

Für dieses Tutorial ist die Version 2.6 des Azure-SDK für .NET erforderlich.

## Erstellen eines API-App-Projekts

Wenn Sie in den Anweisungen aufgefordert werden, einen Namen für das Projekt einzugeben, geben Sie **ContactsList** ein.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Nächste Schritte

Ihre API-App kann jetzt bereitgestellt werden. Dazu können Sie das Tutorial [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) befolgen.

Informationen zur Verwendung von automatisch generiertem Clientcode zum Aufrufen von API-Apps, finden Sie unter [Nutzen einer API-App in Azure App Service aus einem .NET-Client](app-service-api-dotnet-consume.md).

Informationen über das Anpassen der automatisch generierten Swagger-Metadaten für eine API-App finden Sie unter [Anpassen von mit Swashbuckle generierten API-Definitionen](app-service-api-dotnet-swashbuckle-customize.md).

Informationen zum Erstellen, Löschen und Konfigurieren von API-Apps im Azure-Vorschauportal finden Sie unter [Verwalten von API-Apps in Azure App Service](app-service-api-manage-in-portal.md).

Informationen zur Authentifizierung von API-Apps-Benutzern finden Sie unter [Authentifizierung für API-Apps und mobile Apps in Azure App Service](../app-service/app-service-authentication-overview.md).

Informationen zu API-Apps-Funktionen finden Sie unter [Was sind API-Apps?](app-service-api-apps-why-best-platform.md).

<!---HONumber=AcomDC_0114_2016-->