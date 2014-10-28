<properties pageTitle="Add paging to data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Phone app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Verfeinern von Mobile Services-Abfragen mit Paging

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-add-paging-data" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-windows-phone-add-paging-data" title="Windows Phone" class="current">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-ios-add-paging-data" title="iOS" class="current">iOS</a><a href="/de-de/documentation/articles/mobile-services-android-add-paging-data" title="Android" class="current">Android</a><a href="/de-de/documentation/articles/mobile-services-html-add-paging-data" title="HTML" class="current">HTML</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-add-paging-data" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-add-paging-data" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data" title=".NET-Backend">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-windows-phone-add-paging-data"  title="JavaScript-Backend" class="current">JavaScript-Backend</a></div>

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre Windows Phone-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Take** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

> [WACOM.NOTE]Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten][Erste Schritte mit Daten].

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging][mobile-services-windows-dotnet-paging]]

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-windows-store-javascript-add-paging-data "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-windows-phone-add-paging-data "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-ios-add-paging-data "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-android-add-paging-data "Android"
  [HTML]: /de-de/documentation/articles/mobile-services-html-add-paging-data "HTML"
  [Xamarin.iOS]: /de-de/documentation/articles/partner-xamarin-mobile-services-ios-add-paging-data "Xamarin.iOS"
  [Xamarin.Android]: /de-de/documentation/articles/partner-xamarin-mobile-services-android-add-paging-data "Xamarin.Android"
  [.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data ".NET-Backend"
  [JavaScript-Backend]: /de-de/documentation/articles/mobile-services-windows-phone-add-paging-data "JavaScript-Backend"
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-data/
  [mobile-services-windows-dotnet-paging]: ../includes/mobile-services-windows-dotnet-paging.md
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-users/
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-push/
