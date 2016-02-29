<properties
    pageTitle="Erstellen einer Cordova-App in Azure Mobile App Service-Apps | Microsoft Azure"
    description="Befolgen Sie dieses Tutorial für die ersten Schritte bei der Verwendung mobiler Azure-App-Back-Ends für die Apache Cordova-Entwicklung."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="ggailey777"
    editor=""
    tags=""
    keywords="cordova,javascript,mobile,client" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="adrianha"/>

#Erstellen einer Apache Cordova-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Übersicht

Dieses Tutorial zeigt Ihnen, wie Sie einen cloudbasierten Back-End-Dienst mithilfe eines mobilen Azure-App-Back-Ends zu einer mobilen Apache Cordova-App hinzufügen. Sie erstellen sowohl ein neues Mobile App-Back-End als auch eine einfache _Todo list_-Apache Cordova-App, die App-Daten in Azure speichert.

Das Absolvieren dieses Tutorials ist Voraussetzung für alle anderen Apache Cordova-Tutorials zur Verwendung des Features „Mobile Apps“ von Azure App Service.

## Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Einen PC mit [Visual Studio Community 2015] oder höher.
* [Visual Studio-Tools für Apache Cordova].
* Ein [aktives Azure-Konto](https://azure.microsoft.com/pricing/free-trial/)

Sie können auch Visual Studio umgehen und direkt die Apache Cordova-Befehlszeile verwenden. Dies ist hilfreich, wenn Sie das Tutorial auf einem Macintosh-Computer ausführen. Das Kompilieren von Apache Cordova-Clientanwendungen über die Befehlszeile wird in diesem Tutorial nicht behandelt.

## Erstellen eines neuen mobilen Azure-App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Konfigurieren des Serverprojekts

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Herunterladen und Ausführen der Apache Cordova-App

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## Nächste Schritte

Nachdem Sie dieses Schnellstarttutorial abgeschlossen haben, fahren Sie mit einem der folgenden Tutorials fort:

* [Hinzufügen von Authentifizierung] zu Ihrer Apache Cordova-App.
* [Hinzufügen von Pushbenachrichtigungen] zu Ihrer Apache Cordova-App.

Erfahren Sie mehr über die Schlüsselkonzepte von Azure App Service.

* [Authentifizierung]
* [Pushbenachrichtigungen]

Erfahren Sie, wie Sie die SDKs nutzen,

* [ASP.NET Server-SDK]
* [Node.js Server-SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio-Tools für Apache Cordova]: https://www.visualstudio.com/de-DE/features/cordova-vs.aspx
[Hinzufügen von Authentifizierung]: app-service-mobile-cordova-get-started-users.md
[Hinzufügen von Pushbenachrichtigungen]: app-service-mobile-cordova-get-started-push.md
[Authentifizierung]: app-service-mobile-auth.md
[Pushbenachrichtigungen]: ../notification-hubs/notification-hubs-overview.md
[ASP.NET Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server-SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0218_2016-->