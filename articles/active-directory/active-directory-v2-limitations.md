<properties
	pageTitle="v2.0-Endpunkt: Einschränkungen | Microsoft Azure"
	description="Eine Liste der Einschränkungen des v2.0-Endpunkts in Azure AD."
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

# Sollte ich den v2.0-Endpunkt verwenden? 

Beim Erstellen von Anwendungen, die in Azure Active Directory integriert werden, müssen Sie entscheiden, ob der v2.0-Endpunkt und die Authentifizierungsprotokolle Ihren Anforderungen entsprechen. Das ursprüngliche Azure AD-App-Modell wird weiterhin vollständig unterstützt und umfasst in gewisser Hinsicht mehr Funktionen als v2.0. Der v2.0-Endpunkt bietet jedoch [entscheidende Vorteile](active-directory-v2-compare.md) für Entwickler, die Sie zur Verwendung des neuen Programmiermodells verleiten könnten. Im Laufe der Zeit wird v2.0 auf sämtliche Azure AD-Funktionen erweitert, sodass nur noch der v2.0-Endpunkt verwenden werden muss.

Zu diesem Zeitpunkt können Sie mit dem v2.0-Endpunkt zwei Kernfunktionen erreichen:

- Anmelden von Benutzern mit persönlichen und Geschäftskonten.
- Aufrufen der [konvergierten Outlook-API](https://dev.outlook.com).

Beide Funktionen können in mobilen, Web- und PC-Anwendungen gleichermaßen implementiert werden. Wenn diese relativ eingeschränkten Funktionen für Ihre Anwendung sinnvoll erscheinen, empfehlen wir die Verwendung des v2.0-Endpunkts. Wenn für die Anwendung zusätzliche Funktionen von Microsoft-Diensten erforderlich sind, sollten Sie weiterhin die bewährten Endpunkte von Azure AD und Microsoft-Konto verwenden. Mit der Zeit werden v2.0-Endpunkte sowohl Azure AD als auch das Microsoft-Konto umfassen, und wir werden alle Entwickler bei der Umstellung auf den v2.0-Endpunkt unterstützen.

In der Zwischenzeit können Sie anhand dieses Artikels ermitteln, ob der v2.0-Endpunkt für Sie geeignet ist. Dieser Artikel wird fortlaufend auf den aktuellen Status des v2.0-Endpunkts aktualisiert. Schauen Sie daher regelmäßig vorbei, um Ihre Anforderungen anhand der v2.0-Funktionen neu zu überprüfen.

Wenn Sie eine vorhandene App mit Azure AD einsetzen, die den v2.0-Endpunkt nicht verwendet, müssen Sie nicht bei Null anfangen. In Zukunft bieten wir eine Möglichkeit, vorhandene Azure AD-Anwendungen auf die Verwendung mit dem v2.0-Endpunkt umzustellen.

## Einschränkungen für Apps
Die folgenden App-Typen werden derzeit vom v2.0-Endpunkt nicht unterstützt. Eine Beschreibung der unterstützten Typen von Apps finden Sie [in diesem Artikel](active-directory-v2-flows.md).

##### Eigenständige Web-APIs
Mit dem v2.0-Endpunkt können Sie eine [Web-API erstellen, die mit dem OAuth 2.0-Token geschützt wird](active-directory-v2-flows.md#web-apis). Allerdings kann die Web-API nur Token von einer Anwendung empfangen, die die gleiche Anwendungs-ID aufweist. Das Erstellen einer Web-API, auf die von einem Client mit einer anderen Anwendungs-ID zugegriffen wird, wird nicht unterstützt. Dieser Client kann keine Berechtigungen für Ihre Web-API anfordern oder erhalten.

Wenn Sie wissen möchten, wie eine Web-API erstellt wird, die Token von einem Client mit der gleichen App-ID akzeptiert, sehen Sie in den Web-API-Beispielen zum v2.0-Endpunkt im Abschnitt [Erste Schritte](active-directory-appmodel-v2-overview.md#getting-started) nach.

##### Daemons und serverseitige Apps
Apps, die lang andauernde Prozesse enthalten oder ohne das Vorhandensein eines Benutzers arbeiten, benötigen ebenfalls die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen. Diese Apps können mithilfe der App-Identität (anstelle der delegierten Benutzeridentität) die Authentifizierung durchführen und Token erhalten. Dies funktioniert über den OAuth 2.0-Client-Anmeldeinformationsfluss.

Dieser Ablauf wird derzeit nicht vom v2.0-Endpunkt unterstützt, d. h. Apps können nur dann Token abrufen, nachdem ein interaktiver Benutzeranmeldeablauf stattgefunden hat. Der Ablauf für die Client-Anmeldeinformationen wird in naher Zukunft hinzugefügt. Wenn Sie sich für den Ablauf für Clientanmeldeinformationen mit dem ursprünglichen Azure AD-Endpunkt interessieren, sehen Sie sich das [Daemon-Beispiel auf GitHub](https://github.com/AzureADSamples/Daemon-DotNet) an.

##### „Im Auftrag von“-Web-API-Ablauf
Viele Architekturen umfassen eine Web-API, die eine weitere nachgeordnete Web-API aufrufen muss. Beide werden jeweils durch den v2.0-Endpunkt gesichert. Dieses Szenario kommt häufig bei nativen Clients mit Web-API-Back-End vor, das wiederum einen Microsoft Online-Dienst oder eine weitere benutzerdefinierte Web-API aufruft, die Azure AD unterstützt.

Dieses Szenario kann mithilfe der Berechtigung für Anmeldeinformationen über den OAuth 2.0-JWT-Bearer unterstützt werden, auch bekannt als „Im Auftrag von“-Ablauf. Der „Im Auftrag von“-Ablauf wird für den v2.0-Endpunkt derzeit jedoch noch nicht unterstützt. Um sich die Funktionsweise dieses Vorgangs im allgemein verfügbaren Azure AD-Dienst anzusehen, wechseln Sie zum [Im-Auftrag-von-Codebeispiel auf GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## Einschränkungen für App-Registrierungen
Zu diesem Zeitpunkt müssen alle Apps, die in den v2.0-Endpunkt integriert werden sollen, eine neue App-Registrierung unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com) erstellen. Keine der vorhandenen Azure AD- oder Microsoft-Konto-Apps sind mit dem v2.0-Endpunkt kompatibel. Dasselbe gilt für Apps, die in einem anderen Portal als dem neuen App-Registrierungsportal registriert werden. Wir planen eine Möglichkeit, vorhandene Anwendungen für die Verwendung als v2.0-App zu aktivieren. Zu diesem Zeitpunkt gibt es für eine App jedoch keinen Migrationspfad zum v2.0-Endpunkt.

Ebenso können Apps, die im neuen App-Registrierungsportal registriert sind, nicht mit dem ursprünglichen Azure AD-Authentifizierungsendpunkt verwendet werden. Sie können jedoch im App-Registrierungsportal erstellte Apps erfolgreich in den Microsoft-Konto-Authentifizierungsendpunkt, `https://login.live.com`, integrieren.

Apps, die im neuen Anwendungs-Registrierungsportal registriert sind, sind derzeit auf eine begrenzte Anzahl von Umleitungs-URI-Werten beschränkt. Der Umleitungs-URI für Web-Apps und -Dienste muss mit dem Schema oder `https` beginnen, während er für alle anderen Plattformen den hartcodierten Wert `urn:ietf:oauth:2.0:oob` verwenden muss.

Um zu erfahren, wie Sie eine Anwendung im neuen Anwendungs-Registrierungsportal registrieren, lesen Sie [diesen Artikel](active-directory-v2-app-registration.md).

## Einschränkungen für Dienste und APIs
Der v2.0-Endpunkt unterstützt derzeit die Anmeldung für jede App, die im neuen Anwendungsregistrierungsportal registriert ist, sofern sie in der Liste der [unterstützten Authentifizierungsabläufe](active-directory-v2-flows.md) enthalten ist. Diese Apps können OAuth 2.0-Zugriffstoken jedoch nur für einen sehr begrenzten Satz von Ressourcen erhalten. Der v2.0-Endpunkt gibt Zugriffstoken nur für folgende Apps aus:

- Die App, die das Token angefordert hat. Eine App kann einen Zugriffstoken für sich selbst anfordern, wenn die logische App aus mehreren unterschiedlichen Komponenten oder Schichten besteht. Wenn Sie dieses Szenario in Aktion sehen möchten, besuchen Sie unsere [Erste Schritte](active-directory-appmodel-v2-overview.md#getting-started)-Tutorials.
- Outlook Mail-, Calendar- und Contacts-REST-APIs befinden sich unter https://outlook.office.com. Um zu erfahren, wie Sie eine App schreiben können, die auf diese APIs zugreift, gehen Sie zu den [Erste Schritte mit Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)-Tutorials.
- Die Microsoft Graph-APIs. Weitere Informationen über Microsoft Graph und alle verfügbaren Daten finden Sie unter [https://graph.microsoft.io](https://graph.microsoft.io).

Zu diesem Zeitpunkt werden keine anderen Dienste unterstützt. In Zukunft werden weitere Microsoft Online-Dienste sowie der Support für Ihre eigenen Web-APIs und Dienste hinzugefügt.

## Einschränkungen für Bibliotheken und SDKs
Damit Sie den Vorgang ausprobieren können, haben wir eine experimentelle Version der Active Directory Authentication Library bereitgestellt, die mit dem v2.0-Endpunkt kompatibel ist. Allerdings befindet sich diese Version von ADAL in der Vorschauphase. Sie wird nicht unterstützt und in den nächsten Monaten erheblich überarbeitet. In unserem Abschnitt [Erste Schritte](active-directory-appmodel-v2-overview.md#getting-started) stehen Codebeispiele mit ADAL für .NET, iOS, Android und JavaScript zur Verfügung, wenn Sie schnell eine App mit dem v2.0-Endpunkt ausführen möchten.

Wenn Sie den v2.0-Endpunkt in einer Produktionsanwendung verwenden möchten, besitzen Sie die folgenden Optionen:

- Wenn Sie eine Webanwendung erstellen, können Sie unsere allgemein verfügbare serverseitige Middleware bedenkenlos für die Anmeldung und die Tokenüberprüfung einsetzen. Dazu gehören die OWIN Open ID Connect-Middleware für ASP.NET und unser NodeJS Passport-Plug-In. Codebeispiele, in denen diese Middleware verwendet wird, stehen in unserem Abschnitt [Erste Schritte](active-directory-appmodel-v2-overview.md#getting-started) ebenfalls zur Verfügung.
- Für andere Plattformen sowie für native und mobile Anwendungen können Sie die Integration mit dem v2.0-Endpunkt auch durchführen, indem Sie Nachrichten direkt in Ihrem Anwendungscode senden und empfangen. Die v2.0-Protokolle OpenID Connect und OAuth [wurden explizit dokumentiert](active-directory-v2-protocols.md), um Sie bei einer solchen Integration zu unterstützen.
- Zudem können Sie die Open ID Connect- und OAuth-Open Source-Bibliotheken für die Integration in den v2.0-Endpunkt verwenden. Das v2.0-Protokoll sollte ohne wesentliche Änderungen mit vielen Open Source-Protokollbibliotheken kompatibel sein. Die Verfügbarkeit solcher Bibliotheken ist abhängig von Sprache und Plattform, und auf den [Open ID Connect](http://openid.net/connect/)- und [OAuth 2.0](http://oauth.net/2/)-Websites wird eine Liste der gängigen Implementierungen verwaltet. Im Folgenden sind die Open Source-Clientbibliotheken und -Beispiele aufgeführt, die mit dem v2.0-Endpunkt getestet wurden. Beachten Sie, dass Funktionen wie z. B. die [dynamische Clientregistrierung mit OpenID Connect](https://openid.net/specs/openid-connect-registration-1_0.html) und Endpunkte zur Tokenüberprüfung noch nicht unterstützt werden und zur Verwendung des v2.0-Endpunkts möglicherweise in der Bibliothek deaktiviert werden müssen: 

  - [Java WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu Federation](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Android OpenID Connect-Beispiel](https://github.com/learning-layers/android-openid-connect)

## Einschränkungen für Protokolle
Der v2.0-Endpunkt unterstützt nur Open ID Connect und OAuth 2.0. Allerdings wurden nicht alle Features und Funktionen der einzelnen Protokolle in den v2.0-Endpunkt integriert. Beispiele hierfür sind:

- Der OpenID Connect-`end_sesssion_endpoint`
- Das Erteilen von OAuth 2.0-Clientanmeldeinformationen

Um den Umfang der im v2.0-Endpunkt unterstützten Protokollfunktionalität besser zu verstehen, lesen Sie unsere [OpenID Connect- und OAuth 2.0-Protokollreferenz](active-directory-v2-protocols.md).

## Erweiterte Azure AD-Features für Entwickler
Im Azure Active Directory-Dienst steht eine Reihe von Entwicklerfeatures zur Verfügung, die für den v2.0-Endpunkt noch nicht unterstützt werden. Dazu gehören:

- Gruppenansprüche für Azure AD-Benutzer
- Anwendungsrollen und Rollenansprüche

<!---HONumber=AcomDC_0224_2016-->