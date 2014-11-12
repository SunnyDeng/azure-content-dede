<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Erste Schritte mit der Synchronisierung von Offlinedaten in Mobile Services

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/de-de/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS" class="current">iOS</a>
<a href="/de-de/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/de-de/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

Dieses Lernprogramm behandelt die Offlinesynchronisierungsfunktion von Mobile Services. Sie ermöglichen es Entwicklern, Apps zu schreiben, die selbst dann nutzbar sind, wenn der Endbenutzer keinen Netzwerkzugriff hat.

Die Offlinesynchronisierung hat eine Reihe potenzieller Nutzen:

* Verbesserte Reaktionsfähigkeit der App durch das lokale Caching von Serverdaten auf dem Gerät
* Macht Apps unempfindlich gegen Unterbrechungen der Netzwerkverbindung
* Endanwender können Daten selbst dann erstellen und ändern, wenn sie keinen Netzwerkzugriff haben, also in Szenarien mit wenig oder keiner Konnektivität
* Daten auf mehreren Geräten synchronisieren und Konflikte erkennen, wenn derselbe Datensatz von zwei Geräte aus geändert wird

Dieses Lernprogramm zeigt, wie die App mithilfe des Lernprogramms [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] geändert werden kann, sodass die Offlinefunktionen von Azure Mobile Services unterstützt werden. Anschließend fügen Sie Daten in einem Offlineszenario mit getrennter Verbindung hinzu, synchronisieren diese Elemente mit der Onlinedatenbank und melden sich daraufhin beim Azure-Verwaltungsportal an, um Änderungen an den Daten anzuzeigen, die bei der Ausführung der Anwendung vorgenommen wurden.

>[WACOM.NOTE] Sie benötigen ein Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].


## Abrufen der Beispiel-App

**Ein vollständiges Lernprogramm wird in Kürze zur Verfügung stehen. Bis dahin können Sie sich die Beispiele unter [iOS Offline ToDo List][iOS Offline ToDo List] und im [Companion Blog Post][Companion Blog Post] ansehen.**

<!-- ## Next steps

* [How to use the Mobile Services client library for iOS]
   -->

<!-- URLs. -->

[Windows Store C#]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows Store C#"
[Windows Phone]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
[iOS]: /de-de/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
[Xamarin.iOS]: /de-de/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
[Xamarin.Android]: /de-de/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-ios-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-ios-get-started-data/
[Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28
[iOS Offline ToDo List]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Companion Blog Post]: http://aka.ms/iosoffline
