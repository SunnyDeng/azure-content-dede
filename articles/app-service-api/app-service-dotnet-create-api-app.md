<properties
	pageTitle="Erstellen einer ASP.NET-API-App in Azure App Service"
	description="Erfahren Sie, wie Sie mithilfe von Visual Studio 2013 eine ASP.NET-API-App in Azure App Service erstellen."
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="08/14/2015"
	ms.author="tdykstra"/>

# Erstellen einer ASP.NET-API-App in Azure App Service

> [AZURE.SELECTOR]
- [Visual Studio 2015 or 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Übersicht

In diesem Lernprogramm wird gezeigt, wie Sie ein ASP.NET-Web-API-Projekt erstellen, das zur Bereitstellung in der Cloud als [API-App in Azure App Service](app-service-api-apps-why-best-platform.md) konfiguriert wird. Informationen zum Konfigurieren eines vorhandenen Web-API-Projekts zur Bereitstellung als API-App finden Sie unter [Konfigurieren eines Web-API-Projekts als API-App](app-service-dotnet-create-api-app-visual-studio.md).

Nachfolgende Lernprogramme in dieser Reihe zeigen, wie Sie das in diesem Lernprogramm erstellte API-App-Projekt [bereitstellen](app-service-dotnet-deploy-api-app.md) und [debuggen](../app-service-dotnet-remotely-debug-api-app.md).

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

Für dieses Lernprogramm ist die Version 2.6 des Azure-SDK für .NET erforderlich.

## Erstellen eines API-App-Projekts

Wenn Sie in den Anweisungen aufgefordert werden, einen Namen für das Projekt einzugeben, geben Sie *ContactsList* ein.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Nächste Schritte

Ihre API-App kann jetzt bereitgestellt werden. Dazu können Sie das Lernprogramm [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) befolgen.
 

<!---HONumber=August15_HO8-->