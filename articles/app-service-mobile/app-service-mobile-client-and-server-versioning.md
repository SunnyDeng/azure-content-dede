<properties
  pageTitle="Client- und Server-SDK-Versionskontrolle in Mobile Apps und Mobile Services | Azure App Service"
  description="Liste der Client-SDKs und Kompatibilität mit Server-SDK-Versionen für Mobile Services und Azure Mobile Apps"
  services="app-service\mobile"
  documentationCenter=""
  authors="lindydonna" 
  manager="dwrede"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="12/15/2015"
  ms.author="donnam"/>

# Client- und Serverversionsverwaltung in Mobile Apps und Mobile Services

Die neueste Version von Azure Mobile Services ist das Feature **Mobile Apps** von Azure App Service.

<!-- Azure App Service offers a number of platform benefits over Mobile Services, including continuous integration and deployment, staging lots, and VNET support.
 -->

Die Mobile Apps-Client- und -Server-SDKs basieren ursprünglich auf den SDKs in Mobile Services, aber sie sind *nicht* miteinander kompatibel. Dies bedeutet, dass Sie ein *Mobile Apps*-Client-SDK mit einem *Mobile Apps*-Server-SDK und ebenso für *Mobile Services* verwenden müssen. Dieser Vertrag wird mit einem speziellen Headerwert durchgesetzt, der von den Client- und Server-SDKs verwendet wird: `ZUMO-API-VERSION`.

Hinweis: Wenn in diesem Dokument auf ein *Mobile Services*-Back-End verwiesen wird, heißt das nicht zwangsläufig, dass es unter Mobile Services gehostet wird. Es ist jetzt möglich, einen mobilen Dienst für die Ausführung unter App Service ohne Codeänderungen zu migrieren, aber für den Dienst würden dabei trotzdem *Mobile Services*-SDK-Versionen verwendet werden.

Weitere Informationen zum Migrieren zu App Service ohne Codeänderungen finden Sie im Artikel [Migrieren eines Mobile Service zu Azure App Service].

## Headerspezifikation

Der Schlüssel `ZUMO-API-VERSION` kann entweder im HTTP-Header oder in der Abfragezeichenfolge angegeben werden. Der Wert ist eine Versionszeichenfolge im Format **x.y.z**.

Beispiel:

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## Deaktivieren der Versionsüberprüfung

Sie können die Versionsüberprüfung deaktivieren, indem Sie den Wert **true** für die App-Einstellung **MS\_SkipVersionCheck** festlegen. Geben Sie dies entweder in der Datei „web.config“ oder im Abschnitt mit den Anwendungseinstellungen im Azure-Portal an.

> [AZURE.NOTE]Es gibt einige Änderungen des Verhaltens zwischen Mobile Services und Mobile Apps, vor allem in den Bereichen der Offlinesynchronisierung, Authentifizierung und Pushbenachrichtigungen. Sie sollten die Versionsüberprüfung nur deaktivieren, nachdem Sie mit umfassenden Tests sichergestellt haben, dass diese Änderungen des Verhaltens nicht zu Problemen bei den Funktionen der App führen.

## Zusammenfassung der Kompatibilität für alle Versionen

Im Diagramm unten ist die Kompatibilität zwischen allen Client- und Servertypen dargestellt. Ein Back-End ist entweder als Mobile **Services** oder Mobile **Apps** klassifiziert. Dies richtet sich nach dem verwendeten Server-SDK.

| | **Mobile Services** – Node.js oder .NET | **Mobile Apps** – Node.js oder .NET |
| ----------                | -----------------------             |   ----------------              |
| [Mobile Services-Clients] | OK, | Fehler* |
| [Mobile Apps-Clients] | Fehler* | OK, |

*Dies kann durch die Angabe von **MS\_SkipVersionCheck** gesteuert werden.


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Mobile Services-Client und -Server

Die unten in der Tabelle angegebenen Client-SDKs sind mit **Mobile Services** kompatibel.

Hinweis: Die Mobile Services-Client-SDKs senden *keinen* Headerwert für `ZUMO-API-VERSION`. Wenn der Dienst diesen Header- oder Abfragezeichenfolgenwert empfängt, wird ein Fehler zurückgegeben, es sei denn, Sie haben dies wie oben beschrieben ausdrücklich deaktiviert.

### <a name="MobileServicesClients"></a> Mobile *Services*-Client-SDKs

| Clientplattform | Version | Versionsheaderwert |
| -------------------               | ------------------------                                                  | -------------------  |
| Verwalteter Client (Windows, Xamarin) | [1\.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | – |
| iOS | [2\.2.2](http://aka.ms/gc6fex) | – |
| Android | [2\.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) | – |
| HTML | [1\.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | – |

### Mobile *Services*-Server-SDKs

| Serverplattform | Version                                                                                                        | Akzeptierter Versionsheader |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **Kein Versionsheader** |
| Node.js          | (demnächst verfügbar) |                        **Kein Versionsheader** |

<!-- TODO: add Node npm version -->

### Verhalten von Mobile Services-Back-Ends

| ZUMO-API-VERSION | Wert von MS\_SkipVersionCheck | Antwort |
| ---------------- | ---------------------------- | -------- |
| Nicht angegeben | Beliebig | 200 – OK |
| Beliebiger Wert | True | 200 – OK |
| Beliebiger Wert | False/Nicht angegeben | 400 – Ungültige Anforderung | 

## <a name="2.0.0"></a>Azure Mobile Apps-Client und -Server

### <a name="MobileAppsClients"></a> Mobile *Apps*-Client-SDKs

Die Versionsüberprüfung wurde ab den folgenden Versionen des Client-SDK für **Azure Mobile Apps** eingeführt:

| Clientplattform | Version | Versionsheaderwert |
| -------------------               | ------------------------  | -----------------    |
| Verwalteter Client (Windows, Xamarin) | [2\.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2\.0.0 |
| iOS | [3\.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2\.0.0 |
| Android | [3\.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3\.0.0 |

<!-- TODO: add HTML version when released -->

### Mobile *Apps*-Server-SDKs

Die Versionsüberprüfung ist in den folgenden Versionen des Server-SDK enthalten:

| Serverplattform | SDK | Akzeptierter Versionsheader |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2\.0.0 |
| Node.js | [azure-mobile-apps Version 1.0-beta1 (oder höher)](https://www.npmjs.com/package/azure-mobile-apps) | 2\.0.0 |

### Verhalten von Mobile Apps-Back-Ends

| ZUMO-API-VERSION | Wert von MS\_SkipVersionCheck | Antwort |
| ---------------- | ---------------------------- | -------- |
| x.y.z oder NULL | True | 200 – OK |
| Null | False/Nicht angegeben | 400 – Ungültige Anforderung |
| 1\.x.y | False/Nicht angegeben | 400 – Ungültige Anforderung |
| 2\.0.0 - 2.x.y | False/Nicht angegeben | 200 – OK |
| 3\.0.0-3.x.y | False/Nicht angegeben | 400 – Ungültige Anforderung |


## Nächste Schritte

- [Migrieren eines Mobile Service zu Azure App Service]


[Mobile Services-Clients]: #MobileServicesClients
[Mobile Apps-Clients]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrieren eines Mobile Service zu Azure App Service]: app-service-mobile-migrating-from-mobile-services.md

<!----HONumber=AcomDC_1217_2015-->
