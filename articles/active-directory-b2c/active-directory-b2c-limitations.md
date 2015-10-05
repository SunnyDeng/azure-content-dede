<properties
	pageTitle="Preview-Version von Azure Active Directory B2C: Einschränkungen | Microsoft Azure"
	description="Enthält eine Liste der Einschränkungen in Bezug auf Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Preview-Version von Azure Active Directory B2C: Einschränkungen

Es gibt mehrere Features und Funktionen von Azure Active Directory (AD) B2C, die in der Preview-Version noch nicht unterstützt werden. Viele dieser Einschränkungen werden aufgehoben, bevor Azure AD B2C allgemein verfügbar ist. Sie sollten aber damit vertraut sein, wenn sie Azure AD B2C während der Preview-Phase zum Erstellen von Consumer-orientierten Anwendungen verwenden.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Probleme bei der Erstellung von Azure AD B2C-Verzeichnissen

Es gibt bekannte Probleme, die beim [Erstellen eines Azure AD B2C-Verzeichnisses](active-directory-b2c-get-started) auftreten können. Dieser [Artikel](active-directory-b2c-support-create-directory.md) enthält Informationen hierzu.

## Brandingprobleme in Bestätigungs-E-Mails und auf Seiten für die Self-Service-Kennwortzurücksetzung

Die standardmäßigen Bestätigungs-E-Mails und Seiten für die Self-Service-Kennwortzurücksetzung enthalten die Brandingelemente "Microsoft" und "Azure". Wir werden diese entfernen. Mit dem [Feature für Unternehmensbranding](./active-directory/active-directory-add-company-branding.md) können Sie das Branding auf diesen Seiten ändern, sodass diese Brandingelemente nicht mehr angezeigt werden.

## Unterstützung für Produktionsanwendungen

Anwendungen, die in Azure AD B2C integriert sind, sollten nicht als Anwendungen auf Produktionsebene öffentlich zugänglich gemacht werden. Azure AD B2C befindet sich derzeit in der Preview-Phase. Wichtige Änderungen können daher jederzeit eingeführt werden, und durch den Dienst wird keine Vereinbarung zum Servicelevel (SLA) gewährt. Für möglicherweise auftretende Vorfälle wird kein Support gewährt. Wenn Sie bereit sind, die Risiken einer Abhängigkeit von einem Dienst zu übernehmen, der sich noch in der Entwicklung befindet, sollten Sie uns einen Tweet an @AzureAD senden, um den Umfang Ihrer Anwendung bzw. des Dienstes zu erörtern.

## Einschränkungen für Anwendungen

Die folgenden Arten von Anwendungen werden in der Preview-Version von Azure AD B2C derzeit nicht unterstützt. Eine Beschreibung der unterstützten Typen von Anwendungen finden Sie in [diesem Artikel](active-directory-b2c-apps).

### Single-Page-Anwendungen (JavaScript)

Viele moderne Anwendungen verfügen über ein Single-Page-Application (SPA)-Front-End, das in erster Linie in JavaScript geschrieben ist und häufig SPA-Frameworks verwendet, z. B. AngularJS, Ember.js, Durandal usw. Dieser Ablauf ist in der Preview-Version von Azure AD B2C noch nicht verfügbar.

### Daemons/Serverseitige Anwendungen

Anwendungen, die lang andauernde Prozesse enthalten oder ohne das Vorhandensein eines Benutzers arbeiten, benötigen ebenfalls die Möglichkeit, auf sichere Ressourcen zuzugreifen, z. B. Web-APIs. Diese Anwendungen können mithilfe der Anwendungsidentität (anstelle der delegierten Consumeridentität) die Authentifizierung durchführen und Token erhalten. Dies funktioniert über den [OAuth 2.0-Client-Anmeldeinformationsfluss](active-directory-b2c-protocols.md#oauth2-client-credentials-grant-flow). Dieser Ablauf ist in der Preview-Version von Azure AD B2C noch nicht verfügbar. Dies bedeutet, dass Anwendungen nur dann Token erhalten können, nachdem ein interaktiver Consumer-Anmeldeablauf durchgeführt wurde.

### Eigenständige Web-APIs

In der Vorschauversion von Azure AD B2C können Sie eine [Web-API erstellen, die mit OAuth 2.0-Token geschützt wird](active-directory-b2c-apps.md#web-apis). Allerdings kann die Web-API nur Token von einem Client empfangen, der die gleiche Anwendungs-ID aufweist. Das Erstellen einer Web-API, auf die von mehreren unterschiedlichen Clients zugegriffen werden kann, wird nicht unterstützt.

## Einschränkungen für Bibliotheken und SDKs

Nicht alle Sprachen und Plattformen verfügen über Bibliotheken, die die Preview-Version von Azure AD B2C unterstützen. Der Satz von Authentifizierungsbibliotheken ist zurzeit auf .NET, iOS, Android und NodeJS beschränkt. Entsprechende Schnellstart-Lernprogramme für jede Bibliothek stehen im Abschnitt [Erste Schritte](active-directory-b2c-overview.md#getting-started) zur Verfügung.

Wenn Sie eine Anwendung mit einer anderen Sprache oder Plattform in die Vorschauversion von Azure AD B2C integrieren möchten, finden Sie Informationen dazu in der [OAuth 2.0- und OpenID Connect-Protokollreferenz](active-directory-b2c-protocols.md). Sie enthält Anweisungen zum Erstellen der HTTP-Nachrichten, die zum Kommunizieren mit dem Azure AD B2C-Dienst erforderlich sind.

## Einschränkungen für Protokolle

Die Preview-Version von Azure AD B2C unterstützt OpenID Connect und OAuth 2.0. Allerdings wurden nicht alle Features und Funktionen der einzelnen Protokolle implementiert. Lesen Sie zum besseren Verständnis des Umfangs der unterstützten Protokollfunktionen in der Vorschauversion von Azure AD B2C die [OpenID Connect- und OAuth 2.0-Protokollreferenz](active-directory-b2c-protocols.md).

## Einschränkungen für Token

Viele der von der Preview-Version von Azure AD B2C ausgestellten Token werden als JSON-Webtoken (JWTs) implementiert. Aber nicht alle Informationen, die in JWTs enthalten sind (als „Ansprüche“ bezeichnet), sind richtig, oder sie fehlen sogar. Beispiele hierfür sind die Ansprüche „sub“ und „preferred\_username“. Sie können davon ausgehen, dass sich einige Dinge während der Preview-Phase ändern werden. Lesen Sie sich die [Token-Referenz](active-directory-b2c-tokens.md) durch, um ein besseres Verständnis für die Token zu entwickeln, die vom Azure AD B2C-Dienst derzeit ausgegeben werden.

## Probleme mit der Benutzerverwaltung im Azure-Portal

Sie können über das Azure-Vorschauportal auf B2C-Features zugreifen. Sie können aber das Azure-Portal verwenden, um auf andere Verzeichnisfeatures zuzugreifen, z. B. die Benutzerverwaltung. Derzeit treten im Azure-Vorschauportal einige bekannte Probleme mit der Benutzerverwaltung auf (Registerkarte **Benutzer**).

- Für einen Benutzer mit lokalem Konto (also ein Consumer, der sich mit E-Mail-Adresse und Kennwort oder Benutzername und Kennwort anmeldet) entspricht das Feld **Benutzername** nicht dem Anmeldebezeichner (E-Mail-Adresse oder Benutzername), der während der Anmeldung verwendet wird. Dies liegt daran, dass das im Azure-Portal angezeigte Feld eigentlich der Benutzerprinzipalname ist, der in B2C-Szenarien nicht verwendet wird. Wenn Sie den Anmeldebezeichner des lokalen Kontos anzeigen möchten, finden Sie das Benutzerobjekt dazu im [Graph-Explorer](https://graphexplorer.cloudapp.net/). Auf das gleiche Problem stoßen Sie bei einem Benutzer mit einem Konto bei einem sozialen Netzwerk (also ein Consumer, der sich mit Facebook, Google+ usw. anmeldet), aber in diesem Fall ist kein eigentlicher Anmeldebezeichner vorhanden.

    ![Lokales Konto – UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Bei einem Benutzer mit lokalem Konto können Sie keine Felder ändern und auf der Registerkarte **Profil** keine Änderungen speichern. Dies soll in Kürze behoben werden.

## Einschränkungen beim Löschen von Azure AD B2C-Verzeichnissen

Sie können ein Azure AD B2C-Verzeichnis im Azure-Portal nicht löschen.

<!---HONumber=Sept15_HO4-->