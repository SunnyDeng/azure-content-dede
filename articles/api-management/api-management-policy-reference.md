<properties 
	pageTitle="Richtlinienreferenz für die Azure API-Verwaltung" 
	description="Erfahren Sie mehr über die verfügbaren Richtlinien zum Konfigurieren von API Management." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="sdanie"/>

# Richtlinienreferenz für die Azure API-Verwaltung

Dieser Abschnitt enthält einen Index für die Richtlinien in der [API Management Policy Reference][]. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien für die API-Verwaltung][].

Richtlinienausdrücken können als Attributwerten oder Text in der API Management-Richtlinien verwendet werden, die Richtlinie nicht anders angegeben. Einige Richtlinien wie z. B. die [Ablaufsteuerung][] und [Variable festlegen][] Richtlinien basieren auf Richtlinienausdrücken. Weitere Informationen finden Sie unter [Erweiterte Richtlinien][], [Richtlinieausdrücke][], und das folgende Video.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

## Richtlinie Referenz-index

-	[Richtlinien für Softwareeinschränkung][]
	-	[Überprüfen Sie HTTP-Header][] -setzt die Existenz bzw. den Wert eines HTTP-Headers.
	-	[Limit Aufruf Rate][] -verhindert, dass API-Nutzung durch Aufrufe und/oder die Nutzungsrate Bandbreite einschränken schnellt.
	-	[Schränken Aufrufer IPS-][] -Filter (ermöglicht/verweigert) Aufrufe von bestimmten IP-Adressen bzw. Adressbereiche.
	-	[Set nutzungskontingent][] -erneuert oder Lebensdauer Aufruf Volume und/oder Bandbreite Kontingent umsetzen.
	-	[Überprüfen von JWT][] -erzwingt ein JWT extrahiert eine angegebene HTTP-Header oder einem angegebenen Abfrageparameter als gültig und vorhanden ist.
-	[Erweiterte Richtlinien][]
	-	[Ablaufsteuerung][] – bedingt gilt Richtlinienanweisungen anhand der Ergebnisse der Auswertung eines booleschen [Ausdrücke][].
	-	[Variable festlegen][] -beibehalten ein Werts in einem benannten [Kontext][] Variable zugreifen.
-	[Authentifizierungsrichtlinien][]
	-	[Authentifizieren mit Basic][] -Authentifizierung mit einem Back-End-Dienst unter Verwendung der Standardauthentifizierung.
	-	[Authentifizieren mit Clientzertifikat][] -Authentifizierung mit einem Back-End-Dienst mit Clientzertifikaten.
-	[Richtlinien für die Zwischenspeicherung][] 
	-	[Aus Cache abrufen][] - Führt eine Cachesuche aus und gibt ggf. eine gültige Antwort aus dem Cache zurück.
	-	[Store zwischenzuspeicherndes][] -Antwort entsprechend der Konfiguration der angegebenen Cache zwischengespeichert.
-	[Plattformübergreifende Domänenrichtlinien][] 
	-	[Domänenübergreifende Aufrufe zulassen][] - Erlaubt API-Aufrufe aus browserbasierten Clients, die Adobe Flash und Microsoft Silverlight verwenden.
	-	[CORS][] - Fügt Unterstützung für Cross-Origin Resource Sharing (CORS) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients zu ermöglichen.
	-	[JSONP][] - Fügt Unterstützung für JSON mit Padding (JSONP) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients mit JavaScript zu ermöglichen.
-	[Transformation für Richtlinien][] 
	-	[Konvertieren von JSON und XML][] – konvertiert anfordern oder Antworttext von JSON in XML.
	-	[Konvertiert XML, JSON][] – konvertiert anfordern oder Antworttext aus XML, JSON.
	-	[Suchen und Ersetzen Sie die Zeichenfolge im Nachrichtentext][] – sucht nach einer Anforderung oder Antwort Teilzeichenfolge und durch eine andere Teilzeichenfolge ersetzt.
	-	[Maske URLs im Inhalt][] -schreibt (Masken) im Antworttext und im Location-Header verknüpft werden, so dass sie auf den entsprechenden Link über den Proxy verweisen.
	-	[Back-End-Dienst][] -ändert den Back-End-Dienst für eine eingehende Anforderung.
	-	[Nachrichtenkörper festlegen,][] -legt den Nachrichtentext für eingehende und ausgehende Anforderungen.
	-	[HTTP-Header setzen][] - Weist einem vorhandenen Antwort- und/oder Anforderungsheader einen Wert zu oder fügt einen neuen Antwort- und/oder Anforderungsheader hinzu.
	-	[Abfrageparameter setzen][] - Fügt Abfrageparameter hinzu, löscht diese oder ersetzt deren Werte.
	-	[URL rewrite][] -Anforderungs-URL aus seiner öffentlichen Form konvertiert, an das Formular vom Webdienst erwartet.


[Richtlinien für Softwareeinschränkung]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Überprüfen Sie HTTP-Header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit Aufruf Rate]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Schränken Aufrufer IPS-]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set nutzungskontingent]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Überprüfen von JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Erweiterte Richtlinien]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Ablaufsteuerung]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Variable festlegen]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Ausdrücke]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[Kontext]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables

[Authentifizierungsrichtlinien]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authentifizieren mit Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authentifizieren mit Clientzertifikat]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Richtlinien für die Zwischenspeicherung]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Aus Cache abrufen]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store zwischenzuspeicherndes]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Plattformübergreifende Domänenrichtlinien]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Domänenübergreifende Aufrufe zulassen]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation für Richtlinien]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Konvertieren von JSON und XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Konvertiert XML, JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Suchen und Ersetzen Sie die Zeichenfolge im Nachrichtentext]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Maske URLs im Inhalt]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Back-End-Dienst]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Nachrichtenkörper festlegen,]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[HTTP-Header setzen]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Abfrageparameter setzen]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[URL rewrite]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Richtlinien für die API-Verwaltung]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Richtlinieausdrücke]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->