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
	ms.topic="get-started-article" 
	ms.date="05/19/2015"
	ms.author="bradyg;tarcher"/>

# Erstellen einer ASP.NET-API-App in Azure App Service

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Übersicht

In diesem Lernprogramm wird gezeigt, wie Sie zum Erstellen einer ASP.NET Web API-Projekt mit einer Visual Studio 2013-Vorlage, die das Projekt für die Bereitstellung in der Cloud als konfiguriert eine [API-Anwendung](app-service-api-apps-why-best-platform.md) in [Azure Anwendungsdiensts](../app-service/app-service-value-prop-what-is.md). Informationen zum Konfigurieren eines vorhandenen Web-API-Projekts für die Bereitstellung als API-Anwendung finden Sie unter [Konfigurieren Sie ein Web-API-Projekt als app-API-](app-service-dotnet-create-api-app-visual-studio.md).

Nachfolgende Lernprogramme in dieser Reihe zeigen, wie Sie das in diesem Lernprogramm erstellte API-App-Projekt [bereitstellen](app-service-dotnet-deploy-api-app.md) und [debuggen](../app-service-dotnet-remotely-debug-api-app.md).

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Für dieses Lernprogramm ist Version 2.5.1 oder höher des Azure-SDK für .NET erforderlich.

## Erstellen eines API-App-Projekts

1. Öffnen Sie Visual Studio 2013.

2. Wählen Sie **Datei > Neues Projekt** aus.

3. Wählen Sie die Vorlage **ASP.NET-Webanwendung**.

4. Stellen Sie sicher, dass die **Fügen Sie Application Insights Projekt** ist das Kontrollkästchen deaktiviert.

4. Geben Sie dem Projekt den Namen *ContactsList*.

	![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

5. Klicken Sie auf **OK**.

6. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Projektvorlage **Azure API-App** aus.

	![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

7. Klicken Sie auf **OK**, um das Projekt zu generieren.

Visual Studio erstellt ein Web-API-Projekt, das zur Bereitstellung als API-App konfiguriert ist.

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Nächste Schritte

Ihre API-App kann jetzt bereitgestellt werden. Dazu können Sie das Lernprogramm [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) befolgen.
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->