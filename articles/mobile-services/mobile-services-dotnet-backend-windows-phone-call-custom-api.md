<properties 
	pageTitle="Aufrufen einer benutzerdefinierten API aus einer Windows Phone-App - Mobile Services" 
	description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren und dann von einer Windows Phone-App aufrufen, die Azure Mobile Services verwendet." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Aufrufen einer benutzerdefinierten API aus dem Client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

In diesem Thema wird gezeigt, wie Sie eine benutzerdefinierte API aus einer Windows Phone-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das Erledigt-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf `true` festlegt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Dieses Lernprogramm baut auf dem "GetStartedWithData"-Beispiel auf, einer einfachen TodoList-App. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Hinzufügen von Mobile Services zu einer vorhandenen App](mobile-services-dotnet-backend-windows-phone-get-started-data.md) ausführen.

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](../../includes/mobile-services-windows-phone-call-custom-api.md)]


## Nächste Schritte

In diesem Thema wurde gezeigt, wie Sie mithilfe der **invokeApiAsync**-Methode eine relativ einfache benutzerdefinierte API Ihrer Windows Phone-App aufrufen. Weitere Informationen zum Verwenden der **InvokeApiAsync**-Methode finden Sie im Beitrag [Custom API in Azure Mobile Services](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx) (in englischer Sprache).

Weitere Informationen finden Sie in den folgenden Mobile Services-Themen:

* [Mobile Services: Serverskriptreferenz] <br/>Erfahren Sie mehr über das Erstellen von benutzerdefinierten APIs.

* [Speichern von Serverskripts in der Quellcodeverwaltung] <br/> Erfahren Sie, wie Sie die Quellcodeverwaltungsfunktion nutzen können, um benutzerdefinierten API-Skript-Code einfacher und sicherer zu entwickeln und zu veröffentlichen.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Speichern von Serverskripts in der Quellcodeverwaltung]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO4-->