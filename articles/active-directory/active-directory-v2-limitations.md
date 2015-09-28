<properties
	pageTitle="App-Modell v2.0: Einschränkungen | Microsoft Azure"
	description="Eine Liste der Einschränkungen im Azure AD App-Modell v2.0."
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
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# App-Modell v2.0 (Vorschauversion): Einschränkungen

Es gibt mehrere Features und Funktionen im App-Modell v2.0, die während der Phase der öffentlichen Vorschauversion noch nicht unterstützt werden. Jede dieser Einschränkungen wird aufgehoben, bevor das App-Modell v2.0 allgemein verfügbar gemacht wird, allerdings sollten Sie diese kennen, wenn Sie während der öffentlichen Vorschauphase Apps erstellen.

> [AZURE.NOTE]Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

## Unterstützung für Produktions-Apps
Apps, die mit dem App-Modell v2.0 integriert werden, sollten der Öffentlichkeit nicht als Produktionsanwendungen bereitgestellt werden. Das App-Modell v2.0 befindet sich derzeit in der öffentlichen Vorschau. Wichtige Änderungen könnten daher jederzeit eingeführt werden, und es wird keine SLA durch den Dienst gewährt. Für möglicherweise auftretende Vorfälle wird kein Support gewährt. Wenn Sie bereit sind, die Risiken einer Abhängigkeit von einem Dienst zu übernehmen, der sich in der Entwicklung befindet, kontaktieren Sie uns unter @AzureAD, um den Umfang der App oder des Dienstes zu erörtern.

## Einschränkungen für Apps
Die folgenden Typen von Apps werden derzeit nicht in der öffentlichen Vorschau von App-Modell v2.0 unterstützt. Eine Beschreibung der unterstützten Typen von Apps finden Sie [in diesem Artikel](active-directory-v2-flows.md).

##### Singe Page-Apps (Javascript)
Viele moderne Apps verfügen über ein Single Page-App-Front-End, das in erster Linie in Javascript geschrieben ist und häufig SPA-Frameworks wie z. B. AngularJS, Ember.js oder Durandal verwendet. Der allgemein verfügbare Azure AD-Dienst unterstützt diese Apps mithilfe des [impliziten OAuth 2.0-Ablaufs](active-directory-v2-protocols.md#oauth2-implicit-flow). Dieser Ablauf steht jedoch für das App-Modell v2.0 noch nicht zur Verfügung. Dies wird sich in Kürze ändern.

Wenn Sie darauf gespannt sind, wie eine SPA mit dem App-Modell v2.0 funktioniert, können Sie die Authentifizierung mit dem [Web-Appvorgang](active-directory-v2-flows.md#web-apps) implementieren. Dies ist allerdings nicht die empfohlene Vorgehensweise, und die Dokumentation für dieses Szenario ist begrenzt. Wenn Sie ein Gefühl für das SPA-Szenario bekommen möchten, sehen Sie sich das [allgemein verfügbare Azure AD-SPA-Codebeispiel](active-directory-devquickstarts-angular.md) an.

##### Daemons und serverseitige Apps
Apps, die lang andauernde Prozesse enthalten oder ohne das Vorhandensein eines Benutzers arbeiten, benötigen ebenfalls die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen. Diese Apps können mithilfe der App-Identität (anstelle der delegierten Benutzeridentität) die Authentifizierung durchführen und Token erhalten. Dies funktioniert über den [OAuth 2.0-Client-Anmeldeinformationsfluss](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).

Dieser Vorgang wird derzeit nicht vom App-Modell v2.0 unterstützt, d. h. Apps können nur dann Token abrufen, nachdem ein interaktiver Benutzeranmeldevorgang aufgetreten ist. Der Vorgang für die Client-Anmeldeinformationen wird in naher Zukunft hinzugefügt. Falls die Client-Anmeldeinformationen an das allgemein verfügbare Azure AD-App-Modell übertragen werden sollen, sehen Sie sich das [Daemon-Beispiel auf GitHub](https://github.com/AzureADSamples/Daemon-DotNet) an.

##### Verkettete Web-APIs (Im-Auftrag-von)
Viele Architekturen umfassen eine Web-API, die eine andere heruntergestreamte Web-API aufrufen muss. Beide werden vom App-Modell v2.0 gesichert. Dieses Szenario kommt häufig in systemeigenen Clients mit Web-API-Back-End vor, das wiederum einen Microsoft Online-Dienst aufruft, wie z. B. Office 365 oder die Graph-API.

Dieses Szenario der verketteten Web-API kann mithilfe der Berechtigung für Anmeldeinformationen über den OAuth 2.0-JWT-Bearer unterstützt werden, auch bekannt als [Im-Auftrag-von-Vorgang](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). Der Im-Auftrag-von-Vorgang ist in der Vorschaufunktion des App-Modells v2.0 derzeit noch nicht implementiert. Um sich die Funktionsweise dieses Vorgangs im allgemein verfügbaren Azure AD-Dienst anzusehen, wechseln Sie zum [Im-Auftrag-von-Codebeispiel auf GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

##### Eigenständige Web-APIs
In der Vorschau des App-Modells v2.0 haben Sie die Möglichkeit, [eine Web-API zu entwickeln, die mithilfe von OAuth-Token](active-directory-v2-flows.md#web-apis)aus dem v2.0-Endpunkt gesichert wird. Allerdings kann die Web-API nur Token von einem Client empfangen, der die gleiche Anwendung-ID teilt. Das Erstellen eines Drittanbieter-Webdienstes, auf den von mehreren verschiedenen Clients zugegriffen werden kann, wird nicht unterstützt.

Um zu sehen, wie eine Web-API erstellt wird, die Token von einem bekannten Client mit der gleichen App-ID akzeptiert, schauen Sie in den App-Modell v2.0-Beispielen im Abschnitt [Erste Schritte](active-directory-appmodel-v2-overview.md#getting-started) nach.

## Einschränkungen für Benutzer
Derzeit wird jede Anwendung, die mit dem App-Modell v2.0 erstellt wird, für alle Benutzer mit einem Microsoft-Konto oder einem Azure AD-Konto veröffentlicht. Jeder Benutzer kann mit beiden Kontotypen erfolgreich zur App navigieren oder diese installieren, ihre Anmeldeinformationen im App-Modell v2.0 eingeben, und den Berechtigungen der App zustimmen. Sie können den Code der App so schreiben, dass die App Anmeldevorgänge von bestimmten Gruppen von Benutzern ablehnt. Allerdings verhindern Sie damit nicht, dass diese Benutzer der App zustimmen.

Ihre Apps können tatsächlich nicht die Benutzertypen einschränken, die sich in der App anmelden können. Sie können keine branchenspezifischen Apps erstellen (die auf Benutzer in einer Organisation beschränkt sind), Apps, die ausschließlich für Unternehmensbenutzer verfügbar sind (mit einem Azure AD-Konto), oder Apps, die nur für Verbraucher gedacht sind (mit einem Microsoft-Konto).

## Einschränkungen für App-Registrierungen
Zu diesem Zeitpunkt müssen alle Apps, die sich mit dem App-Modell v2.0 integrieren möchten, eine neue App-Registrierung unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com) erstellen. Alle vorhandenen Azure AD- oder Microsoft-Konto-Apps sind weder mit dem App-Modell v2.0 kompatibel, noch können Apps in einem anderen Portal als dem neuen App-Registrierungsportal registriert werden. Es gibt keinen Migrationspfad für eine App aus dem allgemein verfügbaren Azure AD-Dienst an das App-Modell v2.0.

Ebenso funktionieren Apps, die im neuen App-Registrierungsportal registriert wurden, ausschließlich mit dem App-Modell v2.0. Das App-Registrierungsportal kann nicht zum Erstellen von Apps verwendet werden, die erfolgreich in Azure AD oder Microsoft Account-Diensten integriert werden.

Apps, die im neuen Anwendungs-Registrierungsportal registriert sind, sind derzeit auf eine begrenzte Anzahl von Umleitungs-URI-Werten beschränkt. Der Umleitungs-URI für Web-Apps und -Dienste muss mit dem Schema oder `https` beginnen, während er für alle anderen Plattformen den hartcodierten Wert `urn:ietf:oauth:2.0:oob` verwenden muss.

Um zu erfahren, wie Sie eine Anwendung im neuen Anwendungs-Registrierungsportal registrieren, lesen Sie [diesen Artikel](active-directory-v2-app-registration.md).

## Einschränkungen für Dienste und APIs
Das App-Modell v2.0 unterstützt derzeit die Anmeldung in jeder App, die im neuen Anwendungs-Registrierungsportal registriert ist, sofern sie in der Liste [Authentifizierungsflüsse unterstützt](active-directory-v2-flows.md) fällt. Diese Apps können OAuth 2.0-Zugriffstoken jedoch nur für einen sehr begrenzten Satz von Ressourcen erhalten. Der v2.0-Endpunkt gibt Zugriffstoken nur für folgende Apps aus:

- Die App, die das Token angefordert hat. Eine App kann einen Zugriffstoken für sich selbst anfordern, wenn die logische App aus mehreren unterschiedlichen Komponenten oder Schichten besteht. Wenn Sie dieses Szenario in Aktion sehen möchten, besuchen Sie unsere [Erste Schritte](active-directory-appmodel-v2-overview.md#getting-started)-Tutorials.
- Outlook Mail-, Calendar- und Contacts-REST-APIs befinden sich unter https://outlook.office.com. Um zu erfahren, wie Sie eine App schreiben können, die auf diese APIs zugreift, gehen Sie zu den [Erste Schritte mit Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)-Tutorials.

In naher Zukunft werden weitere Microsoft Online-Dienste sowie der Support für Ihre Web-APIs und Dienste hinzugefügt.

## Einschränkungen für Bibliotheken und SDKs
Nicht alle Sprachen und Plattformen verfügen über Bibliotheken, die das App-Modell v2.0 unterstützen. Der Satz von Authentifizierungsbibliotheken ist zurzeit auft .NET, iOS, Android, NodeJS und Javascript beschränkt. Entsprechende Codebeispiele und Tutorials für jede Bibliothek stehen im Abschnitt [Erste Schritte](active-directory-appmodel-v2-overview.md#getting-started) zur Verfügung.

Wenn Sie ein App-Modell v2.0 mit einer anderen Sprache oder Plattform integrieren möchten, finden Sie Informationen dazu in der [OAuth 2.0- und OpenID Connect-Protokollreferenz](active-directory-v2-protocols.md), die Anweisungen zum Erstellen der HTTP-Nachrichten bietet, die zum Kommunizieren mit dem v2.0-Endpunkt erforderlich sind.

## Einschränkungen für Protokolle
Das App-Modell v2.0 unterstützt Open ID Connect und OAuth 2.0. Allerdings wurden nicht alle Features und Funktionen der einzelnen Protokolle in das App-Modell v2.0 integriert. Beispiele hierfür sind:

- Vollständige Unterstützung für den OpenID Connect-`prompt`-Parameter
- Der OpenID Connect-`login_hint`-Parameter
- Der OpenID Connect-`domain_hint`-Parameter
- Der OpenID Connect-`end_sesssion_endpoint`

Lesen Sie zum besseren Verständnis des Bereichs der Protokoll-Funktionalität im App-Modell v2.0 die [OpenID Connect- und OAuth 2.0-Protokollreferenz](active-directory-v2-protocols.md).

<!---HONumber=Sept15_HO3-->