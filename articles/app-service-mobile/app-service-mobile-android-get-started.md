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
    ms.date="11/20/2015"
    ms.author="yuaxu"/>

#Erstellen einer Android-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Übersicht

Dieses Lernprogramm zeigt Ihnen, wie Sie einen cloudbasierten Back-End-Dienst mithilfe eines mobilen Azure-App-Back-Ends zu einer mobilen Android-App hinzufügen. Sie erstellen sowohl ein neues Mobile App-Back-End als auch eine einfache _Todo list_-Android-App, die App-Daten in Azure speichert.

Das Absolvieren dieses Lernprogramms ist Voraussetzung für alle anderen Android-Lernprogramme zur Verwendung des Features "Mobile Apps" von Azure App Service.

## Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein [aktives Azure-Konto](http://azure.microsoft.com/pricing/free-trial/)

* Einen PC mit [Visual Studio Community 2013] oder höher

* Die [Android Developer Tools](https://developer.android.com/sdk/index.html), die die integrierte Entwicklungsumgebung Android Studio enthalten, sowie die neueste Android-Plattform

* Azure Mobile Android SDK, das automatisch als Teil des heruntergeladenen Schnellstartprojekts referenziert ist

## Erstellen eines neuen mobilen Azure-App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Konfigurieren des Serverprojekts

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Herunterladen und Ausführen der Android-App

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1203_2015--->