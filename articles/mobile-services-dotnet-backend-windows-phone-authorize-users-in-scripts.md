<properties pageTitle="Dienstseitige Autorisierung (Windows Phone) | Mobile Dev Center" description="Erfahren Sie, wie Sie Benutzer im .NET-Backend von Azure Mobile Services autorisieren." services="" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga"/>

# Dienstweite Autorisierung von Mobile Services-Benutzern

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]


In diesem Thema erfahren Sie, wie Sie authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer Windows Phone-App autorisieren. In diesem Lernprogramm fügen Sie Code zu den Datenzugriffsmethoden in Ihrem Controller hinzu. Die Methoden filtern Abfragen auf der Grundlage der Benutzer-ID eines authentifizierten Benutzers und stellen somit sicher, dass jeder Benutzer ausschließlich seine eigenen Daten einsehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart, und baut auf dem vorherigen Lernprogramm [Erste Schritte mit der Authentifizierung] auf. Sie müssen zuerst [Erste Schritte mit der Authentifizierung] abschließen, bevor Sie mit diesem Lernprogramm beginnen.  

## <a name="register-scripts"></a>Modifizieren der Datenzugriffsmethoden

[AZURE.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)] 


## Testen der App

1. Öffnen Sie in Visual Studio 2013 für Windows Phone das Projekt, das Sie bei der Bearbeitung des Lernprogramms [Erste Schritte mit der Authentifizierung] geändert haben.

2. Drücken Sie F5, um die App auszuführen und sich mit dem ausgewählten Identitätsanbieter anzumelden. 

   	Beachten Sie, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte Benutzer-ID eingefügt wurden und jetzt Nullwerte haben.

3. Geben Sie in der App in das Textfeld einen Text ein, und klicken Sie dann auf **Speichern**.

   	![][3]

   	Dadurch wird der Text und die Benutzer-ID in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten Benutzer-ID-Wert aufweist, wird es vom mobilen Dienst zurückgegeben.

6. (Optional) Wenn Sie zusätzliche Anmeldekonten haben, können Sie verifizieren, dass Benutzer nur ihre eigenen Daten sehen können, indem Sie die App schließen (Alt+F4) und dann erneut ausführen. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein und überprüfen, dass die unter dem vorherigen Konto eingegebenen Elemente nicht angezeigt werden. 

## Nächste Schritte

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Authentifizierung veranschaulichen. Weitere Informationen zu Mobile Services:

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.
  
* [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. -->
[Registrieren von Serverskripts]: #register-scripts
[Nächste Schritte]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push

[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/


<!--HONumber=42-->
