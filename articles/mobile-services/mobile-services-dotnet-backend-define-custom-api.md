<properties
	pageTitle="Definieren einer benutzerdefinierten API in einem mobilen .NET-Back-End-Dienst | Azure Mobile Services"
	description="Informationen zum Definieren eines benutzerdefinierten API-Endpunkts in einem mobilen .NET-Back-End-Dienst."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/06/2016"
	ms.author="glenga"/>


# Vorgehensweise beim Definieren eines benutzerdefinierten API-Endpunkts in einem mobilen .NET-Back-End-Dienst
> [AZURE.SELECTOR]
- [JavaScript-Back-End](./mobile-services-javascript-backend-define-custom-api.md)
- [.NET-Back-End](./mobile-services-dotnet-backend-define-custom-api.md)

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Informationen für die entsprechende Mobile Apps-Version dieses Themas finden Sie unter [Vorgehensweise: Definieren eines benutzerdefinierten API-Controllers](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-custom-api-controller).

Dieses Thema zeigt die Vorgehensweise beim Definieren eines benutzerdefinierten API-Endpunkts in einem mobilen .NET-Back-End-Dienst. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Mithilfe einer benutzerdefinierten API haben Sie mehr Kontrolle über das Messaging, einschließlich HTTP-Header und Textformat.

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

Informationen zum Aufrufen einer benutzerdefinierten API in Ihrer App mithilfe einer Mobile Services-Clientbibliothek finden Sie unter [Aufrufen einer benutzerdefinierten API](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api) in der Client-SDK-Referenz.


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0309_2016-->