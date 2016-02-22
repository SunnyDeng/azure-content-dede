<properties
    pageTitle="Erstellen einer Android-App für mobile Azure App Service-Apps | Microsoft Azure"
    description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung mobiler Azure-App-Back-Ends für die Android-Entwicklung."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="02/04/2016"
    ms.author="yuaxu"/>

#Erstellen einer Android-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Übersicht

Dieses Lernprogramm zeigt Ihnen, wie Sie einen cloudbasierten Back-End-Dienst mithilfe eines mobilen Azure-App-Back-Ends zu einer mobilen Android-App hinzufügen. Sie erstellen sowohl ein neues Mobile App-Back-End als auch eine einfache _Todo list_-Android-App, die App-Daten in Azure speichert.

Das Absolvieren dieses Lernprogramms ist Voraussetzung für alle anderen Android-Lernprogramme zur Verwendung des Features "Mobile Apps" von Azure App Service.

## Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Die [Android Developer Tools](https://developer.android.com/sdk/index.html) mit der integrierten Entwicklungsumgebung Android Studio sowie der neuesten Android-Plattform
* Azure Mobile Android SDK, das automatisch als Teil des heruntergeladenen Schnellstartprojekts referenziert ist
* Einen PC mit [Visual Studio Community 2013] oder neuer &ndash; für ein Node.js-Back-End nicht erforderlich
* Ein [aktives Azure-Konto](https://azure.microsoft.com/pricing/free-trial/)

## Erstellen eines neuen mobilen Azure-App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Konfigurieren des Serverprojekts

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Herunterladen und Ausführen der Android-App

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0211_2016-->