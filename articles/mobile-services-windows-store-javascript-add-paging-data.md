<properties pageTitle="Add paging to data (JavaScript) - Azure Mobile Services" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store JavaScript app from Mobile Services." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Verfeinern von Mobile Services-Abfragen mit Paging

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-add-paging-data" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-windows-phone-add-paging-data" title="Windows Phone" class="current">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-ios-add-paging-data" title="iOS" class="current">iOS</a><a href="/de-de/documentation/articles/mobile-services-android-add-paging-data" title="Android" class="current">Android</a><a href="/de-de/documentation/articles/mobile-services-html-add-paging-data" title="HTML" class="current">HTML</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-add-paging-data" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-add-paging-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data" title=".NET-Backend">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-add-paging-data"  title="JavaScript-Backend" class="current">JavaScript-Backend</a></div>

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre Windows Store-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Take** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

> [WACOM.NOTE]Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten][Erste Schritte mit Daten].

[WACOM.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.



  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
  [mobile-services-javascript-paging]: ../includes/mobile-services-javascript-paging.md
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push/
