<properties
	pageTitle="Der Azure AD v2.0-Endpunkt | Microsoft Azure"
	description="Ein Vergleich zwischen dem ursprünglichen Azure AD und den v2.0-Endpunkten."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Wo liegen die Unterschiede beim v2.0-Endpunkt?

Wenn Sie mit Azure Active Directory vertraut sind oder in der Vergangenheit Apps in Azure AD integriert haben, gibt es möglicherweise einige Unterschiede im v2.0-Endpunkt, die Sie nicht erwarten. In diesem Dokument werden die Unterschiede zu Ihrem Verständnis erläutert.

> [AZURE.NOTE]
	Nicht alle Szenarien und Funktionen von Azure Active Directory werden vom v2.0-Endpunkt unterstützt. Lesen Sie die Informationen zu den [Einschränkungen des v2.0-Endpunkts](active-directory-v2-limitations.md), um herauszufinden, ob Sie den v2.0-Endpunkt verwenden sollten.


## Microsoft-Konten und Azure AD-Konten
Der v2.0-Endpunkt ermöglicht Entwicklern das Schreiben von Apps, die eine Anmeldung sowohl von Microsoft-Konten als auch von Azure AD-Konten akzeptieren. Dazu wird ein einziger Endpunkt zur Authentifizierung genutzt. Dadurch können Sie die App so schreiben, dass sie Konten komplett ignoriert, und auch die Art des Kontos ignorieren kann, mit dem sich der Benutzer anmeldet. Natürlich *können* Sie die App so einrichten, dass Sie die Art des Kontos erkennt, das in einer bestimmten Sitzung verwendet wird, dies ist aber nicht erforderlich.

Wenn Ihre App beispielsweise [Microsoft Graph](https://graph.microsoft.io) aufruft, sind einige zusätzliche Funktionen und Daten für Unternehmensbenutzer verfügbar, z. B. SharePoint-Websites oder Verzeichnisdaten. Der Code kann jedoch für viele Vorgänge wie das [Lesen von Nachrichten eines Benutzers](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message) gleich geschrieben werden, egal ob für Microsoft-Konten oder Azure AD-Konten.

Das Integrieren Ihrer App mit Microsoft-Konten und Azure AD-Konten ist nun ein einfacher Prozess. Sie können einen einzelnen Satz von Endpunkten, eine einzelne Bibliothek und eine einzelne App-Registrierung verwenden, um Zugriff auf die Verbraucher- und Unternehmens-Welten zu erhalten. Weitere Informationen zum v2.0-Endpunkt finden Sie in [der Übersicht](active-directory-appmodel-v2-overview.md).


## Das neue App-Registrierungsportal
Der v2.0-Endpunkt kann nur an einem neuen Speicherort registriert werden: [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Dies ist das Portal, in dem Sie eine Anwendungs-ID erhalten, die Darstellung der Anmeldeseite Ihrer App anpassen und vieles mehr tun können. Für den Zugriff auf das Portal brauchen Sie nur ein von Microsoft unterstütztes Konto – entweder ein persönliches oder ein Geschäfts-, Uni- oder Schulkonto.

Wir werden in diesem App-Registrierungsportal mit der Zeit immer mehr Funktionen zur Verfügung stellen. Ziel ist es, dass dieses Portal der Ort wird, an dem Sie alles rund um Ihre Microsoft-Apps verwalten können.


## Eine App-ID für alle Plattformen
Im ursprünglichen Azure Active Directory-Dienst haben Sie möglicherweise mehrere unterschiedliche Apps für ein einziges Projekt registriert. Sie mussten separate App-Registrierungen für die systemeigenen Clients und Web-Apps verwenden:

![Benutzeroberfläche für die Registrierung der alten Anwendung](../media/active-directory-v2-flows/old_app_registration.PNG)

Wenn Sie z. B. sowohl eine Website als auch eine iOS-App erstellt haben, mussten Sie diese separat mit zwei verschiedenen Anwendungs-IDs registrieren. Wenn Sie eine Website und eine Back-End-Web-API hatten, haben Sie diese möglicherweise als separate Apps in Azure AD registriert. Wenn Sie eine App für iOS und Android hatten, kann dies ebenfalls der Fall sein.

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

Nun brauchen Sie lediglich eine einzige App-Registrierung und eine einzige Anwendungs-ID für jedes Ihrer Projekte. Sie können mehrere "Plattformen" zu einem einzelnen Projekt hinzufügen, und die entsprechenden Daten für jede Plattform, die Sie hinzufügen, angeben. Natürlich können Sie je nach Bedarf beliebig viele Apps erstellen, aber in den meisten Fällen sollte nur eine Anwendung-ID erforderlich sein.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

Unser Ziel ist es, dass dies zu einer vereinfachten App-Verwaltung und Entwicklungserfahrung führt, und sich eine konsolidiertere Ansicht eines einzelnen Projekts ergibt, an dem Sie möglicherweise arbeiten.


## Bereiche, keine Ressourcen
Im ursprünglichen Azure AD-Dienst kann sich eine App als **Ressource** oder als Empfänger von Token verhalten. Eine Ressource kann eine Anzahl von **Bereichen** oder **OAuth 2.0-Berechtigungen** definieren, die sie versteht, sodass Client-Apps Token für diese Ressource für einen bestimmten Satz von Bereichen anfordern können. Betrachten Sie als Beispiel für eine Ressource die Azure AD Graph-API:

- Ressourcenbezeichner, oder `AppID URI`: `https://graph.windows.net/`
- Bereiche, oder `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, usw.  

All dies gilt auch für den v2.0-Endpunkt. Eine App kann sich immer noch als Ressource verhalten, Bereiche definieren und durch einen URI identifiziert werden. Clientanwendungen können immer noch den Zugriff auf diese Bereiche anfordern. Allerdings hat sich die Art und Weise geändert, auf die ein Client solche Berechtigungen anfordert. In der Vergangenheit sah eine OAuth 2.0-Autorisierungsanforderung an Azure AD etwa wie folgt aus:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

wo der **Ressourcen**-Parameter anzeigt, für welche Ressource die Client-App eine Autorisierung anfordert. Azure AD hat die von der App benötigten Berechtigungen berechnet, und zwar basierend auf einer statischen Konfiguration im Azure-Portal. Die Token wurden entsprechend ausgestellt. Dieselbe OAuth 2.0-Autorisierungsanforderung sieht wie folgt aus:

```
GET https://login.microsoftonline.com/common/v2.0/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

wo der **Bereichs**-Parameter angibt, für welche Ressourcen und Berechtigungen die App eine Autorisierung anfordert. Die gewünschte Ressource ist immer noch in der Anforderung vorhanden – sie ist nun einfach von den einzelnen Werten des Bereichsparameters umgeben. Wenn der Bereichsparameter auf diese Weise verwendet wird, entspricht der v2.0-Endpunkt eher der OAuth 2.0-Spezifikation und richtet sich mehr nach den gängigen Methoden der Branche. Dadurch können Apps auch eine [inkrementelle Zustimmung](#incremental-and-dynamic-consent) ausführen, die im nächsten Abschnitt beschrieben wird.

## Inkrementelle und dynamische Zustimmung
Apps, die im allgemein verfügbaren Azure AD-Dienst registriert sind, mussten die erforderlichen OAuth 2.0-Berechtigungen zum Erstellungszeitpunkt der App im Azure-Portal angeben:

![Benutzeroberfläche für die Registrierung von Berechtigungen](../media/active-directory-v2-flows/app_reg_permissions.PNG)

Die für eine App erforderlichen Berechtigungen wurden **statisch** konfiguriert. Während die Konfiguration der App dadurch im Azure Portal existieren kann und der Code sauber und einfach bleibt, stellt es den Entwickler vor ein paar Probleme:

- Eine App musste zum Erstellungszeitpunkt alle Berechtigungen kennen, die jemals von der App benötigt wurde. Das Hinzufügen von Berechtigungen mit der Zeit war ein schwieriger Prozess.
- Eine App musste frühzeitig alle Ressourcen kennen, auf die sie je zugreifen wollte. Es war schwierig, Apps zu erstellen, die auf eine beliebige Anzahl von Ressourcen zugreifen konnten.
- Eine App mussten alle Berechtigungen anfordern, die je nach der ersten Anmeldung des Benutzers benötigt wurden. In einigen Fällen führte dies zu einer sehr langen Liste von Berechtigungen, was Endbenutzer davon abhielt, bei der ersten Anmeldung den Zugriff der App zu genehmigen.

Beim v2.0-Endpunkt können Sie die von Ihrer App benötigten Berechtigungen **dynamisch** zur Laufzeit angeben, während Ihre App normal genutzt wird. Zu diesem Zweck können Sie die Bereiche angeben, die Ihre App zu einem bestimmten Zeitpunkt benötigt, indem Sie sie in den `scope`-Parameter einer Autorisierungsanforderung einschließen:

```
GET https://login.microsoftonline.com/common/v2.0/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Mit der oben genannten Anforderungsberechtigung kann die App die Verzeichnisdaten des Benutzers in Azure AD lesen sowie Daten in das Verzeichnis schreiben. Wenn der Benutzer diesen Berechtigungen in der Vergangenheit für diese spezielle App zugestimmt hat, gibt er einfach seine Anmeldeinformationen ein und meldet sich in der App an. Wenn der Benutzer keiner Berechtigung zugestimmt hat, fordert der v2.0-Endpunkt ihn dazu auf, diesen Berechtigungen zuzustimmen. Weitere Informationen dazu finden Sie unter [Berechtigungen, Zustimmung und Bereiche](active-directory-v2-scopes.md).

Wenn Sie einer App erlauben, Berechtigungen dynamisch über den `scope`-Parameter anzufordern, erhalten Sie die vollständige Kontrolle über die Erfahrung des Benutzers. Falls gewünscht, können Sie die Zustimmungserfahrung auch vorziehen und alle Berechtigungen in einer ersten Autorisierungsanforderung erfragen. Wenn Ihre App eine große Anzahl von Berechtigungen erfordert, können Sie auch die Berechtigungen des Benutzers inkrementell erfassen, während er über die Zeit bestimmte Features Ihrer App verwendet.

## Bekannte Bereiche

#### Offlinezugriff
Für den v2.0-Endpunkt muss möglicherweise eine neue bekannte Berechtigung für Apps verwendet werden: der `offline_access`-Bereich. Alle Apps müssen diese Berechtigung anfordern, wenn sie im Auftrag eines Benutzers für einen längeren Zeitraum auf Ressourcen zugreifen wollen, selbst, wenn der Benutzer die App nicht aktiv verwendet. Der `offline_access`-Bereich wird dem Benutzer in den Zustimmungsdialogfeldern als "Auf Ihre Daten offline zugreifen" angezeigt, wofür der Benutzer seine Zustimmung gewähren muss. Durch Anfordern der `offline_access`-Berechtigung kann Ihre Web-App OAuth 2.0-Aktualisierungstoken vom v2. 0-Endpunkt erhalten. Aktualisierungstoken sind langlebig und können durch neue OAuth 2.0-Zugriffstoken für längere Zugriffszeiten ausgetauscht werden.

Wenn die App den `offline_access`-Bereich nicht anfordert, werden auch keine Aktualisierungstoken empfangen. Dies bedeutet, dass Sie beim Einlösen eines Autorisierungscodes im [OAuth 2.0-Authorisierungscodefluss](active-directory-v2-protocols.md#oauth2-authorization-code-flow) nur ein Zugriffstoken vom `/token`-Endpunkt erhalten. Dieses Zugriffstoken bleibt für einen kurzen Zeitraum (in der Regel eine Stunde) gültig, läuft aber anschließend ab. Zu diesem Zeitpunkt muss Ihre App den Benutzer zurück auf den `/authorize`-Endpunkt leiten, um einen neuen Autorisierungscode abzurufen. Während dieser Umleitung muss der Benutzer möglicherweise seine Anmeldeinformationen erneut eingeben oder den Berechtigungen erneut zustimmen, je nach Art der App.

Um mehr über OAuth 2.0, Aktualisierungstoken und Zugriffstoken zu erfahren, lesen Sie die [v2.0-Protokollreferenz](active-directory-v2-protocols.md).

#### OpenID, Profil und E-Mail

Im ursprünglichen Azure Active Directory-Dienst wurden im grundlegenden OpenID Connect-Anmeldefluss zahlreiche Benutzerinformationen im resultierenden ID-Token bereitgestellt. Die Ansprüche in einem ID-Token können u. a. Name, bevorzugten Benutzernamen, E-Mail-Adresse, Objekt-ID eines Benutzers enthalten.

Die Informationen, auf die der Bereich `openid` Ihrer App Zugriff gewährt, werden nun beschränkt. Der Bereich „openid“ erlaubt Ihrer App nur das Anmelden des Benutzers und das Empfangen eines App-spezifischen Bezeichners für den Benutzer. Falls Sie personenbezogene Informationen (Personally Identifiable Information, PII) über den Benutzer in Ihrer App erhalten möchten, müssen Sie über Ihre App zusätzliche Berechtigungen vom Benutzer einholen. Zu diesem Zweck führen wir zwei neue Bereiche ein: `email` und `profile`.

Der Bereich `email` ist sehr einfach aufgebaut. Ihre App hat damit über den Anspruch `email` im ID-Token Zugriff auf die primäre E-Mail-Adresse des Benutzers. Der Bereich `profile` ermöglicht Ihrer App den Zugriff auf alle anderen grundlegenden Benutzerinformationen: Name, bevorzugter Benutzername, Objekt-ID usw.

Hiermit können Sie für Ihre App den Weg der minimalen Offenlegung einschlagen. Sie können Benutzer nur um die Daten bitten, die für die Funktionsweise der App erforderlich sind. Weitere Informationen zu diesen Bereichen finden Sie in der [v2.0-Bereichsreferenz](active-directory-v2-scopes.md).

## Tokenansprüche

Die Ansprüche in den Token, die vom v2.0-Endpunkt ausgegeben werden, sind nicht identisch mit denen, die von den allgemein verfügbaren Azure AD-Enpunkten ausgegeben werden. Apps, die auf den neuen Dienst migriert werden, sollten nicht davon ausgehen, dass in ID-Token oder Zugriffstoken ein besonderer Anspruch vorhanden ist. Vom v2.0-Endpunkt ausgestellte Token sind mit den OAuth 2.0- und OpenID Connect-Spezifikationen kompatibel, folgen aber möglicherweise einer anderen Semantik, als der allgemein verfügbare Azure AD-Dienst.

Weitere Informationen zu den spezifischen Ansprüchen, die in v2.0-Token ausgegeben werden, finden Sie in der [v2.0-Tokenreferenz](active-directory-v2-tokens.md).

## Einschränkungen
Es gibt einige Einschränkungen, die Sie bei Verwendung des v2.0-Endpunkts kennen sollten. Sehen Sie im [Dokument zu den v2.0-Einschränkungen](active-directory-v2-limitations.md) nach, ob diese Einschränkungen für Ihr spezielles Szenario gelten.

<!---HONumber=AcomDC_0224_2016-->