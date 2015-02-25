<properties pageTitle="Hinzufügen von Paging für Daten (Windows Phone) | Mobile Developer Center" description="Erfahren Sie, wie Sie mithilfe des Auslagerns die Menge der von Ihrer Windows Phone-App von Mobile Services zurückgegebenen Daten verwalten können." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="glenga"/>

# Verfeinern von Mobile Services-Abfragen mit Paging

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre Windows Phone-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die **Take**- und **Skip**-Abfragemethoden im Client, um bestimmte "Seiten" von Daten abzufragen.

>[AZURE.NOTE]Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm baut auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen Anwendung](/de-de/documentation/articles/mobile-services-windows-phone-get-started-data/). auf Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie dieses Lernprogramm abschließen.  

[AZURE.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogrammreihe über Grundlagen des Arbeitens mit Daten in Mobile Services. Weitere Informationen zu Mobile Services:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer der App mit einem Windows-Konto authentifizieren.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

<!-- Anchors. -->

[Nächste Schritte]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-phone-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-push/


[Verwaltungsportal]: https://manage.windowsazure.com/


<!--HONumber=42-->
