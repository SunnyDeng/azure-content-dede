<properties pageTitle="Hinzufügen von Paging für Daten (JavaScript) - Azure Mobile Services" metaKeywords="" description="Erfahren Sie, wie Sie mithilfe des Auslagerns die Menge der von Ihrer Windows Store-JavaScript-App von Mobile Services zurückgegebenen Daten verwalten können." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />


# Verfeinern von Mobile Services-Abfragen mit Paging

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre Windows Store-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Take** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

>[WACOM.NOTE]Um einen Datenüberlauf auf mobilen Geräteclients zu verhindern, implementiert Mobile Services eine automatische Seitenbegrenzung, die standardmäßig die Antwort auf 50 Elemente begrenzt. Durch Angeben der Seitengröße können Sie explizit bis zu 1.000 Elemente in der Antwort anfordern.

Dieses Lernprogramm baut auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten] auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst mindestens das erste Lernprogramm der Reihe Arbeiten mit Daten abschließen, [Erste Schritte mit Daten]. 

[WACOM.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogrammreihe über Grundlagen des Arbeitens mit Daten in Mobile Services. Weitere Informationen zu Mobile Services:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer der App mit Windows-Konto authentifizieren.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

<!-- Anchors. -->

[Nächste Schritte]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/

[Verwaltungsportal]: https://manage.windowsazure.com/
