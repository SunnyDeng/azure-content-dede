<properties
	pageTitle="Konfigurieren von IIS Express für lokales Testen von einem mobilen Dienst | Azure Mobile Services"
	description="Erfahren Sie, wie Sie IIS Express konfigurieren müssen, um Verbindungen zu einem lokalen mobilen Dienstprojekt zu Testzwecken zu ermöglichen."
	authors="ggailey777"
	manager="dwrede"
	services="mobile-services"
	documentationCenter=""
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="glenga"/>

# Konfigurieren des lokalen Webservers für Verbindungen mit einem lokalen mobilen Dienst

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Mit Azure Mobile Services können Sie unter Verwendung einer der unterstützten .NET-Sprachen einen eigenen mobilen Dienst in Visual Studio erstellen und in Azure veröffentlichen. Einer der Hauptvorteile der Verwendung eines .NET-Back-Ends für Ihren mobilen Dienst ist die Möglichkeit, diesen auf Ihrem lokalen oder einem virtuellen Computer auszuführen, zu testen und zu debuggen, bevor Sie ihn in Azure veröffentlichen.

Um einen mobilen Dienst lokal mit Clients zu testen, die auf einem Emulator, einem virtuellen Computer oder einer separaten Arbeitsstation laufen, müssen Sie den lokalen Webserver und den Hostcomputer so konfigurieren, dass sie Verbindungen mit der IP-Adresse und dem Port der Arbeitsstation erlauben. In diesem Thema wird gezeigt, wie Sie IIS Express so konfigurieren, dass Verbindungen mit Ihrem lokal gehosteten mobilen Dienst möglich sind.

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../../includes/mobile-services-how-to-configure-iis-express.md)]

<!---HONumber=AcomDC_0211_2016-->