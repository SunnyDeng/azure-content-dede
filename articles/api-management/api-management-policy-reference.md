<properties 
	pageTitle="Richtlinienreferenz für Azure API Management" 
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
	ms.date="09/15/2015" 
	ms.author="sdanie"/>

# Richtlinienreferenz für Azure API Management

Dieser Abschnitt stellt einen Index der Richtlinien bereit, die in der [API Management-Richtlinienreferenz][] enthalten sind. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management][].

Richtlinienausdrücke können als Attributwerte oder Textwerte in einer beliebigen API Management-Richtlinie verwendet werden, sofern in der Richtlinie nicht anders angegeben. Einige Richtlinien, beispielsweise [Ablaufsteuerung][] und [Variable festlegen][], basieren auf Richtlinienausdrücken. Weitere Informationen finden Sie unter [Erweiterte Richtlinien][] und [Richtlinienausdrücke][].

## Richtlinienreferenz – Index

-	[Richtlinien für die Zugriffsbeschränkung][]
	-	[HTTP-Header überprüfen][] – Erfordert das Vorhandensein und/oder einen Wert eines HTTP-Headers.
	-	[Aufruflimit][] – Begrenzt Aufrufe und/oder Bandbreitennutzung, um API-Lastspitzen zu verhindern.
	-	[Beschränkung für Aufrufer-IP][] – Filtert (erlaubt/blockiert) Aufrufe von bestimmten IP-Adressen und/oder Adressbereichen.
	-	[Nutzungskontingent festlegen][] – Ermöglicht die Durchsetzung eines erneuerbaren oder für die Lebensdauer gültigen Kontingents für Aufrufe und/oder Bandbreite.
	-	[JWT überprüfen][] – Erzwingt das Vorhandensein und die Gültigkeit eines JWT, das entweder aus einem angegebenen HTTP-Header oder aus einem angegebenen Abfrageparameter extrahiert wurde.
-	[Erweiterte Richtlinien][]
	-	[Ablaufsteuerung][] – Bedingte Anwendung von Richtlinienanweisungen basierend auf den Ergebnissen der Auswertung von booleschen [Ausdrücken][].
	-	[Variable festlegen][] – Speichert einen Wert in einer benannten [Kontextvariable][], um später darauf zugreifen zu können.
-	[Authentifizierungsrichtlinien][]
	-	[Standardauthentifizierung][] – Authentifizierung mit einem Back-End-Dienst unter Verwendung der Standardauthentifizierung.
	-	[Authentifizierung mit Clientzertifikat][] – Authentifizierung mit einem Back-End-Dienst unter Verwendung von Clientzertifikaten.
-	[Cachingrichtlinien][] 
	-	[Aus Cache abrufen][] – Führt eine Cachesuche aus und gibt ggf. eine gültige Antwort aus dem Cache zurück.
	-	[In Cache ablegen][] – Cacheantwort gemäß der angegebenen Konfiguration für die Cachesteuerung.
-	[Domänenübergreifende Richtlinien][] 
	-	[Domänenübergreifende Aufrufe zulassen][] – Erlaubt API-Aufrufe aus browserbasierten Clients, die Adobe Flash und Microsoft Silverlight verwenden.
	-	[CORS][] – Fügt Unterstützung für Cross-Origin Resource Sharing (CORS) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients zu ermöglichen.
	-	[JSONP][] – Fügt Unterstützung für JSON mit Padding (JSONP) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients mit JavaScript zu ermöglichen.
-	[Transformationsrichtlinien][] 
	-	[JSON in XML konvertieren][] – Konvertiert den Anforderungs- oder Antworttext von JSON in XML.
	-	[XML in JSON konvertieren][] – Konvertiert den Anforderungs- oder Antworttext von XML in JSON.
	-	[Zeichenfolge in Text ersetzen][] – Sucht nach einer Zeichenfolge in Antwort oder Anforderung und ersetzt diese durch eine andere Teilzeichenfolge.
	-	[URLs in Inhalt maskieren][] – Schreibt Links im Antworttext und im Standortheader um (maskiert), sodass diese über den Proxy auf den äquivalenten Link zeigen.
	-	[Back-End-Dienst festlegen][] – Ändert den Back-End-Dienst für eine eingehende Anforderung.
	-	[Text festlegen][] – Legt den Nachrichtentext für eingehende und ausgehende Anforderungen fest.
	-	[HTTP-Header setzen][] – Weist einem vorhandenen Antwort- und/oder Anforderungsheader einen Wert zu oder fügt einen neuen Antwort- und/oder Anforderungsheader hinzu.
	-	[Abfrageparameter setzen][] – Fügt Abfrageparameter hinzu, löscht diese oder ersetzt deren Werte.
	-	[URL umschreiben][] – Konvertiert eine Anforderung-URL von der öffentlichen Form in die vom Webdienst erwartete Form.

## Nächste Schritte

Weitere Informationen zu Richtlinienausdrücken finden Sie im folgenden Video.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Richtlinien für die Zugriffsbeschränkung]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[HTTP-Header überprüfen]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Aufruflimit]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Beschränkung für Aufrufer-IP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Nutzungskontingent festlegen]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[JWT überprüfen]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Erweiterte Richtlinien]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Ablaufsteuerung]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Variable festlegen]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Ausdrücken]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[Kontextvariable]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables

[Authentifizierungsrichtlinien]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Standardauthentifizierung]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authentifizierung mit Clientzertifikat]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Cachingrichtlinien]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Aus Cache abrufen]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[In Cache ablegen]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Domänenübergreifende Richtlinien]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Domänenübergreifende Aufrufe zulassen]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformationsrichtlinien]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[JSON in XML konvertieren]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[XML in JSON konvertieren]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Zeichenfolge in Text ersetzen]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[URLs in Inhalt maskieren]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Back-End-Dienst festlegen]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Text festlegen]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[HTTP-Header setzen]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Abfrageparameter setzen]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[URL umschreiben]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Richtlinien in API Management]: api-management-howto-policies.md
[API Management-Richtlinienreferenz]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Richtlinienausdrücke]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 

<!---HONumber=Sept15_HO3-->