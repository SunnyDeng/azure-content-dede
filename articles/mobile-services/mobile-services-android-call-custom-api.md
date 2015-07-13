<properties 
	writer="ricksal" 
	pageTitle="Aufrufen einer benutzerdefinierten API über einen Android-Client | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren und dann von einer Android-App aufrufen, die Azure Mobile Services verwendet." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="ricksal"/>

# Aufrufen einer benutzerdefinierten API aus dem Client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

In diesem Thema wird gezeigt, wie Sie eine benutzerdefinierte API aus einer Android-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Mit der im Rahmen dieses Themas erstellten benutzerdefinierten API können Sie eine einzelne POST-Anforderung senden, die das *completed*-Kennzeichen für sämtliche todo-Einträge in der Tabelle Ihres mobilen Diensts auf `true` festlegen. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügen diese Funktionen zu der App hinzu, welche Sie beim Abschluss entweder des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] erstellen.


>[AZURE.NOTE]Den Quellcode der fertig gestellten App finden Sie <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">hier</a>.

##Voraussetzungen

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]


[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]


## Nächste Schritte

In diesem Thema wurde gezeigt, wie Sie mithilfe der **InvokeApi**-Methode eine relativ einfache benutzerdefinierte API Ihrer Android-App aufrufen. Weitere Informationen zum Verwenden der **InvokeApi**-Methode finden Sie im Beitrag [Custom API in Azure Mobile Services](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx) (in englischer Sprache).

Weitere Informationen finden Sie in den folgenden Mobile Services-Themen:

* [Mobile Services: Serverskriptreferenz] <br/>Erfahren Sie mehr über das Erstellen von benutzerdefinierten APIs.

* [Speichern von Serverskripts in der Quellcodeverwaltung] <br/> Erfahren Sie, wie Sie die Quellcodeverwaltungsfunktion nutzen können, um benutzerdefinierten API-Skript-Code einfacher und sicherer zu entwickeln und zu veröffentlichen.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Erste Schritte mit Mobile Services]: mobile-services-android-get-started.md
[Erste Schritte mit Daten]: mobile-services-android-get-started-data.md
[Get started with authentication]: mobile-services-android-get-started-users.md
[Get started with push notifications]: ../mobile-services-android-get-started-push.md

[Speichern von Serverskripts in der Quellcodeverwaltung]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=62-->