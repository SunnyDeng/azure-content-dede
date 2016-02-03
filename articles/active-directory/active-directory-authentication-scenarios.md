
<properties
   pageTitle="Authentifizierungsszenarien für Azure AD | Microsoft Azure"
   description="Eine Übersicht über die fünf am häufigsten verwendeten Authentifizierungsszenarien für Azure Active Directory (AAD)"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/08/2016"
   ms.author="mbaldwin"/>

# Authentifizierungsszenarien für Azure AD

Azure Active Directory (Azure AD) vereinfacht die Authentifizierung für Entwickler mittels Identity as a Service, durch Unterstützung branchenüblicher Protokolle wie OAuth 2.0 und OpenID Connect sowie durch offene Quellbibliotheken für verschiedene Plattformen, sodass Sie schnell mit der Programmierung beginnen können. Dieses Dokument enthält Informationen zu den verschiedenen, von Azure AD unterstützten Szenarien und vereinfacht den Einstieg. Es ist in folgende Abschnitte unterteilt:

- [Grundlagen der Authentifizierung in Azure AD](#basics-of-authentication-in-azure-ad)

- [Ansprüche in Sicherheitstokens von Azure AD](#claims-in-azure-ad-security-tokens)

- [Grundlagen der Anwendungsregistrierung in Azure AD](#basics-of-registering-an-application-in-azure-ad)

- [Anwendungstypen und -szenarien](#application-types-and-scenarios)

  - [Webbrowser zu Webanwendung](#web-browser-to-web-application)

  - [Single-Page-Anwendung (SPA)](#single-page-application-spa)

  - [Systemeigene Anwendung zu Web-API](#native-application-to-web-api)

  - [Webanwendung zu Web-API](#web-application-to-web-api)

  - [Daemon- oder Serveranwendung zu Web-API](#daemon-or-server-application-to-web-api)



## Grundlagen der Authentifizierung in Azure AD

Lesen Sie diesen Abschnitt, falls Sie noch nicht mit den grundlegenden Authentifizierungskonzepten für Azure AD vertraut sind. Andernfalls können Sie diesen Abschnitt überspringen und mit [Anwendungstypen und -szenarien](#application-types-and-scenarios) fortfahren.

Betrachten wir das einfachste Szenario, bei dem eine Identität erforderlich ist: Ein Benutzer in einem Webbrowser muss sich gegenüber einer Webanwendung authentifizieren. Dieses Szenario ist unter [Webbrowser zu Webanwendung](#web-browser-to-web-application) näher beschrieben und ein guter Ausgangspunkt, um die Funktionen von Azure AD und das Konzept des Szenarios zu veranschaulichen. Berücksichtigen Sie die folgenden Diagramme für dieses Szenario:

![Übersicht über die Anmeldung bei Webanwendungen](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Im Anschluss folgen einige wichtige Informationen zu den Komponenten des obigen Diagramms:

- Als Identitätsanbieter ist Azure AD zuständig für die Überprüfung der Identität von Benutzern und Anwendungen, die im Verzeichnis einer Organisation vorhanden sind, und bei erfolgreicher Authentifizierung dieser Benutzer und Anwendungen auch für die Ausgabe von Sicherheitstokens.


- Eine Anwendung, deren Authentifizierung an Azure AD ausgelagert werden soll, muss in Azure AD registriert werden. Dadurch wird die App im Verzeichnis registriert und eindeutig identifiziert.


- Mit den Open Source-Authentifizierungsbibliotheken von Azure AD können sich die Entwickler um die Protokolldetails kümmern und die Authentifizierung für Sie vereinfachen. Weitere Informationen finden Sie unter [Azure Active Directory-Authentifizierungsbibliotheken](active-directory-authentication-libraries.md).


• Nach der Authentifizierung eines Benutzers muss die Anwendung das Sicherheitstoken des Benutzers überprüfen, um sicherzustellen, dass die Authentifizierung der betreffenden Parteien erfolgreich war. Mithilfe der bereitgestellten Authentifizierungsbibliotheken können Entwickler die Validierung beliebiger Token von Azure AD behandeln – einschließlich JSON-Webtoken (JWT) und SAML 2.0. Informationen zum Durchführen einer manuellen Überprüfung finden Sie in der Dokumentation für den [JWT-Tokenhandler](https://msdn.microsoft.com/library/dn205065.aspx).


> [AZURE.IMPORTANT]Azure AD verwendet die Verschlüsselung mit öffentlichem Schlüssel, um Token zu signieren und deren Gültigkeit zu überprüfen. Weitere Informationen zur Logik, die in Ihrer Anwendung enthalten sein muss, damit diese immer mit den neuesten Schlüsseln aktualisiert wird, finden Sie unter [Wichtige Informationen zum Signaturschlüsselrollover in Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).


• Der Fluss von Anforderungen und Antworten für den Authentifizierungsprozess ergibt sich aus dem verwendeten Authentifizierungsprotokoll (etwa OAuth 2.0, OpenID Connect, WS-Federation oder SAML 2.0). Diese Protokolle werden im Thema [Azure Active Directory-Authentifizierungsprotokolle](active-directory-authentication-protocols.md) sowie in den folgenden Abschnitten ausführlicher behandelt.

> [AZURE.NOTE]Azure AD unterstützt den OAuth 2.0- und den OpenID Connect-Standard. Diese machen ausgiebig Gebrauch von Trägertoken (auch in Form von JWTs). Ein Trägertoken ist ein einfaches Sicherheitstoken, das dem „Träger“ den Zugriff auf eine geschützte Ressource ermöglicht. In diesem Kontext ist der „Träger“ jede beliebige Partei, die das Token vorweisen kann. Um das Trägertoken zu erhalten, muss sich die Partei zwar zunächst bei Azure AD authentifizieren, falls jedoch keine Maßnahmen ergriffen werden, um das Token bei der Übertragung und Speicherung zu schützen, kann das Token von einer fremden Partei abgefangen und verwendet werden. Einige Sicherheitstoken verfügen über einen integrierten Mechanismus, der eine unbefugte Verwendung durch nicht autorisierte Parteien verhindert. Trägertoken besitzen dagegen keinen solchen Mechanismus und müssen über einen sicheren Kanal wie etwa Transport Layer Security (HTTPS) übertragen werden. Wird ein Trägertoken als Klartext gesendet, kann eine böswillige Partei das Token mithilfe eines Man-in-the-Middle-Angriffs abfangen und damit unautorisiert auf eine geschützte Ressource zugreifen. Die gleichen Sicherheitsprinzipien gelten für die (Zwischen-)Speicherung von Trägertoken zur späteren Verwendung. Stellen Sie daher sicher, dass Ihre Anwendung Trägertoken immer sicher überträgt und speichert. Weitere Sicherheitsüberlegungen zu Trägertoken finden Sie unter [RFC 6750, Abschnitt 5](http://tools.ietf.org/html/rfc6750).


Nachdem Sie nun mit den Grundlagen vertraut sind, finden Sie in den folgenden Abschnitten Informationen zur Funktionsweise der Bereitstellung in Azure AD sowie zu den gängigen, von Azure AD unterstützten Szenarien.


## Ansprüche in Sicherheitstoken von Azure AD

Von Azure AD ausgestellte Sicherheitstoken enthalten Ansprüche oder Assertionen von Informationen zum authentifizierten Antragsteller. Diese Ansprüche können von der Anwendung für unterschiedliche Aufgaben verwendet werden. Zu diesen Aufgaben zählen beispielsweise das Überprüfen des Tokens, das Ermitteln des Verzeichnismandanten des Antragstellers, das Anzeigen von Benutzerinformationen, das Bestimmen der Autorisierung des Antragstellers und vieles mehr. Welche Ansprüche in einem Sicherheitstoken enthalten sind, hängt von der Art des Tokens, von der Art der Anmeldeinformationen für die Benutzerauthentifizierung sowie von der Anwendungskonfiguration ab. Die folgende Tabelle enthält eine kurze Beschreibung der einzelnen Anspruchsarten, die von Azure AD ausgegeben werden. Weitere Informationen finden Sie unter [Unterstützte Token und Anspruchstypen](active-directory-token-and-claims.md).


| Anspruch | Beschreibung |
|-------|-------------|
| Anwendungs-ID | Die Anwendung, die das Token verwendet.
| Zielgruppe | Die Empfängerressource, für die das Token vorgesehen ist. |
| Kontextklassenreferenz für die Anwendungsauthentifizierung | Die Art der Clientauthentifizierung (öffentlicher oder vertraulicher Client). |
| Authentifizierungszeitpunkt | Datum und Uhrzeit der Authentifizierung. |
| Authentifizierungsmethode | Die Authentifizierungsmethode für den Antragsteller des Tokens (Kennwort, Zertifikat oder Ähnliches). |
| Vorname | Der Vorname des Benutzers gemäß Angabe in Azure AD. |
| Gruppen | Objekt-IDs von Azure AD-Gruppen, denen der Benutzer angehört. |
| Identitätsanbieter | Der Identitätsanbieter, der den Antragsteller des Tokens authentifiziert hat. |
| Ausgestellt um | Die Zeit, zu der das Token ausgestellt wurde (wird häufig für die Tokenaktualität verwendet). |
| Aussteller | Der STS, der das Token ausgestellt hat, sowie der Azure AD-Mandant. |
| Nachname | Der Nachname des Benutzers gemäß Angabe in Azure AD. |
| Name | Ein lesbarer Wert, der Aufschluss über den Antragsteller des Tokens gibt. |
| Objekt-ID | Ein unveränderlicher, eindeutiger Bezeichner des Antragstellers in Azure AD. |
| Roles | Anzeigenamen von Azure AD-Anwendungsrollen, die dem Benutzer erteilt wurden. |
| Bereich | Die Berechtigungen, die der Clientanwendung gewährt wurden. |
| Betreff | Der Prinzipal, für den das Token Informationen bestätigt. |
| Mandanten-ID | Ein unveränderlicher, eindeutiger Bezeichner des Verzeichnismandanten, der das Token ausgestellt hat. |
| Tokengültigkeitsdauer | Das Zeitintervall, für das ein Token gültig ist. |
| Benutzerprinzipalname | Der Benutzerprinzipalname des Antragstellers. |
| Version | Die Versionsnummer des Tokens. |


## Grundlagen der Anwendungsregistrierung in Azure AD

Jede Anwendung, deren Authentifizierung an Azure AD ausgelagert wird, muss in einem Verzeichnis registriert werden. Hierzu benötigt Azure AD Informationen zu Ihrer Anwendung – unter anderem die URL, an der sie sich befindet, die URL, an die nach der Authentifizierung Antworten gesendet werden sollen, sowie den URI zur Identifizierung Ihrer Anwendung. Diese Informationen sind aus folgenden Gründen erforderlich:

- Bei der Abwicklung von Anmeldungen oder beim Austausch von Token benötigt Azure AD Koordinaten für die Kommunikation mit der Anwendung. Dazu zählt Folgendes:

  - Anwendungs-ID-URI: Der Bezeichner für eine Anwendung. Dieser Wert wird bei der Authentifizierung an Azure AD gesendet und gibt an, für welche Anwendung der Aufrufer ein Token benötigt. Der Wert ist auch im Token enthalten, um der Anwendung mitzuteilen, dass es sich bei ihr um die gewünschte Zielanwendung handelt.


  - Antwort-URL und Umleitungs-URI: Bei einer Web-API oder Webanwendung ist die Antwort-URL der Ort, an den Azure AD die Authentifizierungsantwort sendet (einschließlich eines Tokens, sofern die Authentifizierung erfolgreich war). Bei einer systemeigenen Anwendung ist der Umleitungs-URI ein eindeutiger Bezeichner, an den Azure AD den Benutzer-Agent in einer OAuth 2.0-Anforderung umleitet.


  - Client-ID: Die ID für eine Anwendung. Diese ID wird bei der Registrierung der Anwendung von Azure AD generiert. Bei der Anforderung eines Autorisierungscodes oder -tokens werden die Client-ID und der Schlüssel im Rahmen der Authentifizierung an Azure AD gesendet.


  - Schlüssel: Der Schlüssel, der bei der Authentifizierung zusammen mit einer Client-ID an Azure AD gesendet wird, um eine Web-API aufzurufen.


- Azure AD muss sicherstellen, dass die Anwendung die erforderlichen Berechtigungen für den Zugriff auf Verzeichnisdaten, auf andere Anwendungen in Ihrer Organisation und Ähnliches besitzt.

Zum besseren Verständnis der Bereitstellung ist es hilfreich zu wissen, dass es zwei Kategorien von Anwendungen gibt, die entwickelt und in Azure AD integriert werden können:

- Einzelinstanzanwendung: Eine Einzelinstanzanwendung ist für die Verwendung in einer einzelnen Organisation vorgesehen. Dies sind in der Regel von einem Unternehmensentwickler erstellte Branchenanwendungen. Auf eine Einzelinstanzanwendung greifen nur Benutzer aus einem einzelnen Verzeichnis zu. Daher muss sie auch nur in einem Verzeichnis bereitgestellt werden. Diese Anwendungen werden üblicherweise von einem Entwickler in der Organisation registriert.


- Mehrinstanzenfähige Anwendung: Eine mehrinstanzenfähige Anwendung ist für die Verwendung in mehreren Organisationen vorgesehen. Hierbei handelt es sich in der Regel um SaaS-Anwendungen (Software as a Service), die von einem unabhängigen Softwarehersteller (Independent Software Vendor, ISV) geschrieben wurden. Mehrinstanzenfähige Anwendungen müssen in jedem Verzeichnis bereitgestellt werden, in dem sie verwendet werden. Für die Registrierung ist also jeweils die Zustimmung des Benutzers oder Administrators erforderlich. Dieser Zustimmungsprozess beginnt, wenn eine Anwendung im Verzeichnis registriert wurde und Zugriff auf die Graph-API (oder ggf. eine andere Web-API) erhält. Wenn sich ein Benutzer oder Administrator aus einer anderen Organisation für die Verwendung der Anwendung registriert, erscheint ein Dialogfeld mit den für die Anwendung erforderlichen Berechtigungen. Stimmt der Benutzer oder Administrator der Anwendung zu, erhält die Anwendung Zugriff auf die angegebenen Daten und wird schließlich im Verzeichnis des Benutzers oder Administrators registriert. Weitere Informationen finden Sie unter [Übersicht über das Consent Framework](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Bei der Entwicklung einer mehrinstanzenfähigen Anwendung müssen im Gegensatz zur Einzelinstanzanwendung einige zusätzliche Aspekte berücksichtigt werden. Ein Beispiel: Wenn Sie die Anwendung für Benutzer in mehreren Verzeichnissen verfügbar machen, müssen Sie ermitteln, in welchem Mandaten sich diese befinden. Eine Einzelinstanzanwendung muss nur im eigenen Verzeichnis nach einem Benutzer suchen. Eine mehrinstanzenfähige Anwendung muss dagegen einen bestimmten Benutzer in sämtlichen Verzeichnissen in Azure AD identifizieren. Für diese Aufgabe bietet Azure AD anstelle eines mandantenspezifischen Endpunkts einen gemeinsamen Authentifizierungsendpunkt, an den jede mehrinstanzenfähige Anwendung Anmeldeanforderungen richten kann. Der Endpunkt für alle Verzeichnisse in Azure AD ist „https://login.microsoftonline.com/common“. Ein mandantenspezifischer Endpunkt kann dagegen beispielsweise „https://login.microsoftonline.com/contoso.onmicrosoft.com“ sein. Der gemeinsame Endpunkt muss insbesondere bei der Anwendungsentwicklung berücksichtigt werden, da Sie die Logik implementieren müssen, die die Verarbeitung mehrerer Mandanten bei der Anmeldung, Abmeldung und Tokenüberprüfung ermöglicht.

Wenn Sie gerade eine Einzelinstanzanwendung entwickeln, diese aber für mehrere Organisationen verfügbar machen möchten, können Sie die Anwendung und deren Konfiguration problemlos in Azure AD ändern, um eine mehrinstanzenfähige Anwendung zu erhalten. Darüber hinaus verwendet Azure AD für alle Token in allen Verzeichnissen den gleichen Anmeldeschlüssel. Es spielt also keine Rolle, ob Sie die Authentifizierung in einer Einzelinstanzanwendung oder in einer mehrinstanzenfähigen Anwendung bereitstellen.

Jedes Szenario aus diesem Dokument enthält einen Unterabschnitt mit den entsprechenden Bereitstellungsanforderungen. Ausführlichere Informationen zur Bereitstellung einer Anwendung in Azure AD sowie zu den Unterschieden zwischen Einzelinstanzanwendungen und mehrinstanzenfähigen Anwendungen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](active-directory-integrating-applications.md). Der nächste Abschnitt erläutert gängige Anwendungsszenarien in Azure AD.

## Anwendungstypen und -szenarien

Jedes der in diesem Dokument beschriebenen Szenarien kann mit verschiedenen Programmiersprachen und Plattformen umgesetzt werden, und für jedes Szenario stehen [umfassende Codebeispiele auf GitHub](https://github.com/AzureADSamples) zur Verfügung. Sollte Ihre Anwendung einen bestimmten Teil oder ein Segment eines End-to-End-Szenarios benötigen, lässt sich diese Funktion in den meisten Fällen unabhängig hinzufügen. Wenn also beispielsweise Ihre systemeigene Anwendung eine Web-API aufruft, können Sie problemlos eine Webanwendung hinzufügen, die die Web-API ebenfalls aufruft. Das folgende Diagramm veranschaulicht die Szenarien und Anwendungstypen sowie das Hinzufügen verschiedener Komponenten:

![Anwendungstypen und -szenarien](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Azure AD unterstützt die folgenden fünf Hauptanwendungsszenarien:

- [Webbrowser zu Webanwendung](#web-browser-to-web-application): Ein Benutzer muss sich bei einer Webanwendung anmelden, die von Azure AD gesichert wird.

- [Single-Page-Anwendung (SPA)](#single-page-application-spa): Ein Benutzer muss sich bei einer Single-Page-Anwendung anmelden, die von Azure AD gesichert wird.

- [Systemeigene Anwendung zu Web-API](#native-application-to-web-api): Eine systemeigene Anwendung auf einem Smartphone, Tablet oder PC muss einen Benutzer authentifizieren, um Ressourcen von einer Web-API abzurufen, die von Azure AD gesichert wird.

- [Webanwendung zu Web-API](#web-application-to-web-api): Eine Webanwendung muss Ressourcen von einer Web-API abrufen, die von Azure AD gesichert wird.

- [Daemon- oder Serveranwendung zu Web-API](#daemon-or-server-application-to-web-api): Eine Daemon- oder Serveranwendung ohne Webbenutzeroberfläche muss Ressourcen von einer Web-API abrufen, die von Azure AD gesichert wird.

### Webbrowser zu Webanwendung

Dieser Abschnitt beschreibt eine Anwendung, die einen Benutzer in einem Webbrowser gegenüber einer Webanwendung authentifiziert. In diesem Szenario weist die Webanwendung den Browser des Benutzers an, diesen in Azure AD anzumelden. Azure AD gibt über den Browser des Benutzers eine Anmeldeantwort mit Benutzeransprüchen in einem Sicherheitstoken zurück. Dieses Szenario unterstützt Anmeldungen über WS-Federation, SAML 2.0 und OpenID Connect.


#### Diagramm
![Authentifizierungsfluss für „Browser zu Webanwendung“](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### Beschreibung des Protokollflusses


1. Wenn ein Benutzer die Anwendung aufruft und sich anmelden muss, wird er über eine Anmeldeanforderung an den Authentifizierungsendpunkt in Azure AD umgeleitet.


2. Der Benutzer meldet sich auf der Anmeldeseite an.


3. Ist die Authentifizierung erfolgreich, erstellt Azure AD ein Authentifizierungstoken und gibt eine Anmeldeantwort an die im Azure-Verwaltungsportal konfigurierte Antwort-URL der Anwendung zurück. Bei Produktionsanwendungen empfiehlt sich die Verwendung einer Antwort-URL mit HTTPS. Das zurückgegebene Token enthält Ansprüche für den Benutzer und Azure AD, die die Anwendung zum Überprüfen des Tokens benötigt.


4. Die Anwendung überprüft das Token mithilfe eines öffentlichen Signaturschlüssels und der Ausstellerinformationen aus dem Verbundmetadatendokument für Azure AD. Nach der Überprüfung des Tokens durch die Anwendung startet Azure AD eine neue Sitzung mit dem Benutzer. Anschließend kann der Benutzer bis zum Ablauf der Sitzung auf die Anwendung zugreifen.


#### Codebeispiele


Sehen Sie sich die Codebeispiele für Szenarien vom Typ „Webbrowser zu Webanwendung“ an. Schauen Sie außerdem regelmäßig vorbei: Wir fügen immer wieder neue Beispiele hinzu. [Webbrowser zu Webanwendung](active-directory-code-samples.md#web-browser-to-web-application)


#### Registrieren


- Einzelinstanzanwendung: Wenn Sie eine Anwendung nur für Ihre Organisation erstellen, muss diese über das Azure-Verwaltungsportal im Verzeichnis Ihres Unternehmens registriert werden.


- Mehrinstanzenfähige Anwendung: Wenn Sie eine Anwendung erstellen, die von Benutzern außerhalb Ihrer Organisation verwendet werden kann, muss diese ebenfalls im Verzeichnis Ihres Unternehmens registriert werden. Darüber hinaus muss sie aber auch im Verzeichnis jeder anderen Organisation registriert werden, von der die Anwendung verwendet wird. Um Ihre Anwendung in deren Verzeichnis verfügbar zu machen, können Sie einen Registrierungsprozess für Ihre Kunden einschließen, über den sie Ihrer Anwendung zustimmen können. Bei der Registrierung für Ihre Anwendung erscheint ein Dialogfeld mit den erforderlichen Berechtigungen für die Anwendung sowie mit einer Zustimmungsoption. Je nachdem, welche Berechtigungen erforderlich sind, wird unter Umständen die Zustimmung eines Administrators aus der anderen Organisation benötigt. Wenn der Benutzer oder Administrator seine Zustimmung gibt, wird die Anwendung in dessen Verzeichnis registriert. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](active-directory-integrating-applications.md).


#### Tokenablauf

Die Sitzung des Benutzers läuft ab, wenn die Gültigkeitsdauer des von Azure AD ausgestellten Tokens abläuft. Ihre Anwendung kann diesen Zeitraum bei Bedarf verkürzen und Benutzer beispielsweise bei zu langer Inaktivität abmelden. Nach Ablauf der Sitzung wird der Benutzer aufgefordert, sich erneut anzumelden.





### Single-Page-Anwendung (SPA)


Dieser Abschnitt beschreibt die Authentifizierung für eine Single-Page-Anwendung, deren Web-API-Back-End von Azure AD gesichert wird. Single-Page-Anwendungen setzen sich in der Regel aus einer im Browser ausgeführten JavaScript-Darstellungsschicht (Front-End) und einem Web-API-Back-End zusammen, das auf einem Server ausgeführt wird und die Geschäftslogik der Anwendung implementiert. In diesem Szenario verwendet das JavaScript-Front-End bei der Benutzeranmeldung die [Active Directory-Authentifizierungsbibliothek für JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) und das implizite OAuth 2.0-Gewährungsprotokoll, um ein ID-Token (id\_token) von Azure AD zu erhalten. Das Token wird zwischengespeichert und der Anforderung als Trägertoken angefügt, wenn der Client Aufrufe an das durch die OWIN-Middleware gesicherte Web-API-Back-End sendet.


#### Diagramm

![Single-Page-Anwendung – Diagramm](./media/active-directory-authentication-scenarios/single_page_app.png)

#### Beschreibung des Protokollflusses

1. Der Benutzer navigiert zu der Webanwendung.


2. Die Anwendung gibt das JavaScript-Front-End (Darstellungsschicht) an den Browser zurück.


3. Der Benutzer initiiert die Anmeldung (beispielsweise durch Klicken auf einen Anmeldelink). Der Browser sendet einen GET-Befehl an den Azure AD-Autorisierungsendpunkt, um ein ID-Token anzufordern. Diese Anforderung enthält die Client-ID und die Antwort-URL in den Abfrageparametern.


4. Azure AD überprüft die Antwort-URL anhand der registrierten Antwort-URL, die im Azure-Verwaltungsportal konfiguriert wurde.


5. Der Benutzer meldet sich auf der Anmeldeseite an.


6. Bei erfolgreicher Authentifizierung erstellt Azure AD ein ID-Token und gibt es als URL-Fragment (#) an die Antwort-URL der Anwendung zurück. Bei Produktionsanwendungen empfiehlt sich die Verwendung einer Antwort-URL mit HTTPS. Das zurückgegebene Token enthält Ansprüche für den Benutzer und Azure AD, die die Anwendung zum Überprüfen des Tokens benötigt.


7. Der im Browser ausgeführte JavaScript-Clientcode extrahiert das Token aus der Antwort und verwendet es zur Absicherung von Aufrufen an das Web-API-Back-End der Anwendung.


8. Der Browser ruft das Web-API-Back-End der Anwendung auf und übermittelt dabei das Zugriffstoken im Autorisierungsheader.

#### Codebeispiele


Sehen Sie sich die Codebeispiele für Szenarien vom Typ „Single-Page-Anwendung (SPA)“ an. Schauen Sie regelmäßig vorbei: Wir fügen immer wieder neue Beispiele hinzu. [Single-Page-Anwendung (SPA)](active-directory-code-samples.md#single-page-application-spa).


#### Registrieren


- Einzelinstanzanwendung: Wenn Sie eine Anwendung nur für Ihre Organisation erstellen, muss diese über das Azure-Verwaltungsportal im Verzeichnis Ihres Unternehmens registriert werden.


- Mehrinstanzenfähige Anwendung: Wenn Sie eine Anwendung erstellen, die von Benutzern außerhalb Ihrer Organisation verwendet werden kann, muss diese ebenfalls im Verzeichnis Ihres Unternehmens registriert werden. Darüber hinaus muss sie aber auch im Verzeichnis jeder anderen Organisation registriert werden, von der die Anwendung verwendet wird. Um Ihre Anwendung in deren Verzeichnis verfügbar zu machen, können Sie einen Registrierungsprozess für Ihre Kunden einschließen, über den sie Ihrer Anwendung zustimmen können. Bei der Registrierung für Ihre Anwendung erscheint ein Dialogfeld mit den erforderlichen Berechtigungen für die Anwendung sowie mit einer Zustimmungsoption. Je nachdem, welche Berechtigungen erforderlich sind, wird unter Umständen die Zustimmung eines Administrators aus der anderen Organisation benötigt. Wenn der Benutzer oder Administrator seine Zustimmung gibt, wird die Anwendung in dessen Verzeichnis registriert. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](active-directory-integrating-applications.md).

Wenn die Anwendung registriert wurde, muss sie für die Verwendung des impliziten OAuth 2.0-Gewährungsprotokolls konfiguriert werden. Dieses Protokoll ist für Anwendungen standardmäßig deaktiviert. Um das implizite OAuth 2.0-Gewährungsprotokoll für die Anwendung zu aktivieren, müssen Sie das Anwendungsmanifest aus dem Azure-Verwaltungsportal herunterladen, den Wert „oauth2AllowImplicitFlow“ auf „true“ festlegen und das Manifest anschließend wieder an das Portal hochladen. Ausführlichere Anweisungen finden Sie unter [Aktivieren der impliziten OAuth 2.0-Gewährung für Single-Page-Anwendungen](active-directory-integrating-applications.md).


#### Tokenablauf

Wenn Sie ADAL.js verwenden, um die Authentifizierung mit Azure AD zu verwalten, stehen Ihnen mehrere Features zur Verfügung, die das Aktualisieren abgelaufener Token sowie das Abrufen von Token für zusätzliche Web-API-Ressourcen erleichtern, die unter Umständen von der Anwendung aufgerufen werden. Wenn sich der Benutzer erfolgreich mit Azure AD authentifiziert, wird für den Benutzer eine durch ein Cookie gesicherte Sitzung zwischen Browser und Azure AD erstellt. Wichtig: Die Sitzung besteht zwischen dem Benutzer und Azure AD, nicht zwischen dem Benutzer und der auf dem Server ausgeführten Webanwendung. Wenn ein Token abläuft, ruft ADAL.js unter Verwendung dieser Sitzung automatisch ein weiteres Token ab. Die entsprechende Anforderung wird über ein verborgenes iFrame und unter Verwendung des impliziten OAuth-Gewährungsprotokolls gesendet und empfangen. Mit dem gleichen Mechanismus kann ADAL.js von Azure AD automatisch Zugriffstoken für andere Web-API-Ressourcen abrufen, die von der Anwendung aufgerufen werden. Dafür müssen die Ressourcen CORS (Cross-Origin Resource Sharing) unterstützen, im Verzeichnis des Benutzers registriert sein und bei der Anmeldung die erforderlichen Zustimmungen des Benutzers erhalten haben.


### Systemeigene Anwendung zu Web-API


Dieser Abschnitt beschreibt eine systemeigene Anwendung, die im Auftrag eines Benutzers eine Web-API aufruft. Dieses Szenario basiert auf dem OAuth 2.0-Autorisierungscode-Gewährungstyp mit einem öffentlichen Client (siehe Abschnitt 4.1 der [OAuth 2.0-Spezifikation](http://tools.ietf.org/html/rfc6749)). Die systemeigene Anwendung ruft unter Verwendung des OAuth 2.0-Protokolls ein Zugriffstoken für den Benutzer ab. Dieses Zugriffstoken wird dann in der Anforderung an die Web-API gesendet, die den Benutzer autorisiert und die gewünschte Ressource zurückgibt.

#### Diagramm

![Systemeigene Anwendung zu Web-API – Diagramm](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### Authentifizierungsfluss für die systemeigene Anwendung zu API

#### Beschreibung des Protokollflusses


Bei Verwendung der AD-Authentifizierungsbibliotheken werden Ihnen die meisten der hier beschriebenen Protokolldetails abgenommen (so etwa das Browser-Popup, die Tokenzwischenspeicherung und die Behandlung von Aktualisierungstoken).

1. Über ein Browser-Popup sendet die systemeigene Anwendung eine Anforderung an den Autorisierungsendpunkt in Azure AD. Diese Anforderung enthält die Client-ID und den Umleitungs-URI der systemeigenen Anwendung (gemäß Angabe im Verwaltungsportal) sowie den Anwendungs-ID-URI für die Web-API. Falls sich der Benutzer noch nicht angemeldet hat, wird er dazu aufgefordert.


2. Azure AD authentifiziert den Benutzer. Wenn es sich um eine mehrinstanzenfähige Anwendung handelt und für die Verwendung der Anwendung eine Zustimmung erforderlich ist, muss der Benutzer zustimmen, sofern nicht bereits geschehen. Nach der Zustimmung und einer erfolgreichen Authentifizierung gibt Azure AD eine Antwort mit einem Autorisierungscode an den Umleitungs-URI der Clientanwendung zurück.


3. Wenn Azure AD einen Autorisierungscode an den Umleitungs-URI zurückgibt, beendet die Clientanwendung die Interaktion mit dem Browser und extrahiert den Autorisierungscode aus der Antwort. Auf der Grundlage dieses Autorisierungscodes sendet die Clientanwendung eine Anforderung an den Token-Endpunkt von Azure AD. Diese Anforderung enthält den Autorisierungscode, Details zur Clientanwendung (Client-ID und Umleitungs-URI) sowie die gewünschte Ressource (Anwendungs-ID-URI für die Web-API).


4. Der Autorisierungscode sowie die Informationen zu Clientanwendung und Web-API werden von Azure AD überprüft. Bei erfolgreicher Validierung gibt Azure AD zwei Token zurück: ein JWT-Zugriffstoken und ein JWT-Aktualisierungstoken. Darüber hinaus gibt Azure AD grundlegende Benutzerinformationen wie Anzeigename und Mandanten-ID zurück.


5. Die Clientanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.


6. Wenn das Zugriffstoken abläuft, erhält die Clientanwendung eine Fehlermeldung mit dem Hinweis, dass sich der Benutzer erneut authentifizieren muss. Wenn die Anwendung über ein gültiges Aktualisierungstoken verfügt, kann damit ohne erneute Anmeldeaufforderung ein neues Zugriffstoken abgerufen werden. Wenn das Aktualisierungstoken abläuft, muss die Anwendung den Benutzer interaktiv neu authentifizieren.


> [AZURE.NOTE]Das von Azure AD ausgestellte Aktualisierungstoken kann für den Zugriff auf mehrere Ressourcen verwendet werden. Wenn Sie beispielsweise über eine Clientanwendung verfügen, die zum Aufrufen zweier Web-APIs berechtigt ist, kann mit dem Aktualisierungstoken auch ein Zugriffstoken für die andere Web-API abgerufen werden.


#### Codebeispiele


Sehen Sie sich die Codebeispiele für Szenarien vom Typ „Systemeigene Anwendung zu Web-API“ an. Schauen Sie außerdem regelmäßig vorbei: Wir fügen immer wieder neue Beispiele hinzu. [Systemeigene Anwendung zu Web-API](active-directory-code-samples.md#native-application-to-web-api)


#### Registrieren


- Einzelinstanzanwendung: Sowohl die systemeigene Anwendung als auch die Web-API müssen in Azure AD im gleichen Verzeichnis registriert werden. Die Web-API kann so konfiguriert werden, dass sie einen Satz von Berechtigungen verfügbar macht, die den Ressourcenzugriff der systemeigenen Anwendung beschränken. Die Clientanwendung wählt daraufhin die gewünschten Berechtigungen aus dem Dropdownmenü „Berechtigungen für andere Anwendungen“ des Azure-Verwaltungsportals aus.


- Mehrinstanzenfähige Anwendung: Die systemeigene Anwendung wird zum einen immer nur im Verzeichnis des Entwicklers oder des Herausgebers registriert. Zum anderen ist die systemeigene Anwendung so konfiguriert, dass sie die Berechtigungen angibt, die für eine ordnungsgemäße Verwendung erforderlich sind. Die Liste mit den erforderlichen Berechtigungen wird in einem Dialogfeld angezeigt, wenn ein Benutzer oder Administrator im Zielverzeichnis der Anwendung zustimmt. Dadurch wird die Anwendung in ihrer Organisation verfügbar. Einige Anwendungen benötigen nur Berechtigungen auf Benutzerebene. Diesen kann jeder Benutzer in der Organisation zustimmen. Andere Anwendungen benötigen Berechtigungen auf Administratorebene. Diesen kann ein Benutzer in der Organisation nicht zustimmen. Nur ein Verzeichnisadministrator kann seine Zustimmung für Anwendungen geben, die diese Berechtigungsebene erfordern. Wenn der Benutzer oder Administrator seine Zustimmung gibt, wird nur die Web-API in seinem Verzeichnis registriert. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](active-directory-integrating-applications.md).


#### Tokenablauf


Wenn die systemeigene Anwendung mithilfe ihres Autorisierungscodes ein JWT-Zugriffstoken abruft, erhält sie auch ein JWT-Aktualisierungstoken. Dank des Aktualisierungstokens kann der Benutzer bei Ablauf des Zugriffstokens erneut authentifiziert werden, ohne sich erneut anmelden zu müssen. Das Aktualisierungstoken wird dann zum Authentifizieren des Benutzers verwendet, und ein neues Zugriffstoken sowie ein neues Aktualisierungstoken werden erstellt.





### Webanwendung zu Web-API


Dieser Abschnitt beschreibt eine Webanwendung, die Ressourcen von einer Web-API abruft. In diesem Szenario kann die Webanwendung für die Authentifizierung und den Aufruf der Web-API entweder eine Anwendungsidentität oder eine delegierte Benutzeridentität verwenden. Im Falle des Anwendungsidentitätstyps werden in diesem Szenario OAuth 2.0-Clientanmeldeinformationen verwendet, um die Anwendung zu authentifizieren und auf die Web-API zuzugreifen. Da die Web-API bei Verwendung einer Anwendungsidentität keine Informationen zum Benutzer erhält, kann sie nur erkennen, dass sie von der Webanwendung aufgerufen wird. Wenn die Anwendung Informationen zum Benutzer erhält, werden diese über das Anwendungsprotokoll gesendet und nicht von Azure AD signiert. Die Web-API vertraut darauf, dass die Webanwendung den Benutzer authentifiziert hat. Aus diesem Grund wird dieses Modell als vertrauenswürdiges Subsystem bezeichnet.

Bei Verwendung der delegierten Benutzeridentität lässt sich das Szenario auf zwei Arten realisieren: mit OpenID Connect oder mit OAuth 2.0-Autorisierungscodegewährung und einem vertraulichen Client. Die Webanwendung ruft ein Zugriffstoken für den Benutzer ab, um gegenüber der Web-API zu belegen, dass der Benutzer erfolgreich bei der Webanwendung authentifiziert wurde und die Webanwendung für den Aufruf der Web-API eine delegierte Benutzeridentität erhalten hat. Dieses Zugriffstoken wird in der Anforderung an die Web-API gesendet, die den Benutzer autorisiert und die gewünschte Ressource zurückgibt.

#### Diagramm

![Webanwendung zu Web-API – Diagramm](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### Beschreibung des Protokollflusses

Die Anwendungsidentität und die delegierte Benutzeridentität werden im folgenden Ablauf veranschaulicht. Der Hauptunterschied zwischen den beiden Typen besteht darin, dass bei der delegierten Benutzeridentität zuerst ein Autorisierungscode abgerufen werden muss, damit sich der Benutzer anmelden und auf die Web-API zugreifen kann.

##### Anwendungsidentität mit OAuth 2.0-Clientanmeldeinformationen

1. Ein Benutzer ist in der Webanwendung bei Azure AD angemeldet (siehe Abschnitt [Webbrowser zu Webanwendung](#web-browser-to-web-application) weiter oben).


2. Die Webanwendung muss ein Zugriffstoken abrufen, damit sie sich gegenüber der Web-API authentifizieren und die gewünschte Ressource abrufen kann. Sie sendet eine Anfrage an den Token-Endpunkt von Azure AD. Diese Anfrage enthält die Anmeldeinformationen, die Client-ID und den Anwendungs-ID-URI der Web-API.


3. Azure AD authentifiziert die Anwendung und gibt ein JWT-Zugriffstoken zurück, das zum Aufrufen der Web-API verwendet wird.


4. Die Webanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.

##### Delegierte Benutzeridentität mit OpenID Connect

1. Ein Benutzer ist mit Azure AD bei einer Webanwendung angemeldet (siehe [Webbrowser zu Webanwendung](#web-browser-to-web-application) weiter oben). Wenn der Benutzer der Webanwendung noch keine Zustimmung für den Aufruf der Web-API erteilt hat, muss er diesen Schritt noch durchführen. Die Anwendung zeigt die erforderlichen Berechtigungen an. Handelt es sich bei einer dieser Berechtigungen um eine Berechtigung auf Administratorebene, kann die Zustimmung nicht durch einen normalen Benutzer im Verzeichnis erteilt werden. Dieser Zustimmungsprozess gilt nur für mehrinstanzenfähige Anwendungen. Einzelinstanzanwendungen verfügen bereits über die erforderlichen Berechtigungen. Bei der Anmeldung des Benutzers hat die Webanwendung ein ID-Token mit Benutzerinformationen sowie einen Autorisierungscode erhalten.


2. Auf der Grundlage des Autorisierungscodes von Azure AD sendet die Webanwendung eine Anforderung an den Token-Endpunkt von Azure AD. Diese Anforderung enthält den Autorisierungscode, Details zur Clientanwendung (Client-ID und Umleitungs-URI) sowie die gewünschte Ressource (Anwendungs-ID-URI für die Web-API).


3. Der Autorisierungscode sowie die Informationen zu Webanwendung und Web-API werden von Azure AD überprüft. Bei erfolgreicher Validierung gibt Azure AD zwei Token zurück: ein JWT-Zugriffstoken und ein JWT-Aktualisierungstoken.


4. Die Webanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.

##### Delegierte Benutzeridentität mit OAuth 2.0-Autorisierungscode

1. Ein Benutzer ist bereits bei einer Webanwendung angemeldet, und der Authentifizierungsmechanismus ist von Azure AD unabhängig.


2. Die Webanwendung benötigt einen Autorisierungscode, um ein Zugriffstoken abzurufen. Daher sendet sie über den Browser eine Anforderung an den Autorisierungsendpunkt von Azure AD. Diese Anforderung enthält die Client-ID und den Umleitungs-URI für die Webanwendung nach erfolgreicher Authentifizierung. Der Benutzer meldet sich bei Azure AD an.


3. Wenn der Benutzer der Webanwendung noch keine Zustimmung für den Aufruf der Web-API erteilt hat, muss er diesen Schritt noch durchführen. Die Anwendung zeigt die erforderlichen Berechtigungen an. Handelt es sich bei einer dieser Berechtigungen um eine Berechtigung auf Administratorebene, kann die Zustimmung nicht durch einen normalen Benutzer im Verzeichnis erteilt werden. Dieser Zustimmungsprozess gilt nur für mehrinstanzenfähige Anwendungen. Einzelinstanzanwendungen verfügen bereits über die erforderlichen Berechtigungen.


4. Wenn der Benutzer seine Zustimmung gegeben hat, erhält die Webanwendung den Autorisierungscode, den sie zum Abrufen eines Zugriffstokens benötigt.


5. Auf der Grundlage des Autorisierungscodes von Azure AD sendet die Webanwendung eine Anforderung an den Token-Endpunkt von Azure AD. Diese Anforderung enthält den Autorisierungscode, Details zur Clientanwendung (Client-ID und Umleitungs-URI) sowie die gewünschte Ressource (Anwendungs-ID-URI für die Web-API).


6. Der Autorisierungscode sowie die Informationen zu Webanwendung und Web-API werden von Azure AD überprüft. Bei erfolgreicher Validierung gibt Azure AD zwei Token zurück: ein JWT-Zugriffstoken und ein JWT-Aktualisierungstoken.


7. Die Webanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.

#### Codebeispiele

Sehen Sie sich die Codebeispiele für Szenarien vom Typ „Webanwendung zu Web-API“ an. Schauen Sie außerdem regelmäßig vorbei: Wir fügen immer wieder neue Beispiele hinzu. [Webanwendung zu Web-API](active-directory-code-samples.md#web-application-to-web-api)


#### Registrieren

- Einzelinstanzanwendung: Webanwendung und Web-API müssen sowohl bei Verwendung der Anwendungsidentität als auch bei Verwendung der delegierten Benutzeridentität im gleichen Verzeichnis in Azure AD registriert werden. Die Web-API kann so konfiguriert werden, dass sie einen Satz von Berechtigungen verfügbar macht, die den Ressourcenzugriff der Webanwendung beschränken. Bei Verwendung einer delegierten Benutzeridentität muss die Webanwendung die gewünschten Berechtigungen aus dem Dropdownmenü „Berechtigungen für andere Anwendungen“ des Azure-Verwaltungsportals auswählen. Bei Verwendung der Anwendungsidentität ist dieser Schritt nicht erforderlich.


- Mehrinstanzenfähige Anwendung: Die Webanwendung wird so konfiguriert, dass sie die Berechtigungen angibt, die für eine ordnungsgemäße Verwendung erforderlich sind. Die Liste mit den erforderlichen Berechtigungen wird in einem Dialogfeld angezeigt, wenn ein Benutzer oder Administrator im Zielverzeichnis der Anwendung zustimmt. Dadurch wird die Anwendung in ihrer Organisation verfügbar. Einige Anwendungen benötigen nur Berechtigungen auf Benutzerebene. Diesen kann jeder Benutzer in der Organisation zustimmen. Andere Anwendungen benötigen Berechtigungen auf Administratorebene. Diesen kann ein Benutzer in der Organisation nicht zustimmen. Nur ein Verzeichnisadministrator kann seine Zustimmung für Anwendungen geben, die diese Berechtigungsebene erfordern. Wenn der Benutzer oder Administrator seine Zustimmung gibt, werden die Webanwendung und die Web-API in dessen Verzeichnis registriert.

#### Tokenablauf

Wenn die Webanwendung mithilfe ihres Autorisierungscodes ein JWT-Zugriffstoken abruft, erhält sie auch ein JWT-Aktualisierungstoken. Dank des Aktualisierungstokens kann der Benutzer bei Ablauf des Zugriffstokens erneut authentifiziert werden, ohne sich erneut anmelden zu müssen. Das Aktualisierungstoken wird dann zum Authentifizieren des Benutzers verwendet, und ein neues Zugriffstoken sowie ein neues Aktualisierungstoken werden erstellt.


### Daemon- oder Serveranwendung zu Web-API


Dieser Abschnitt beschreibt eine Daemon- oder Serveranwendung, die Ressourcen von einer Web-API abruft. Der Abschnitt beinhaltet zwei untergeordnete Szenarien: eine Daemon-Anwendung, die auf der Grundlage von OAuth 2.0-Anmeldeinformationen eine Web-API aufruft, und eine Serveranwendung (z. B. eine Web-API), die auf der Grundlage der OAuth 2.0-Entwurfsspezifikation „On-Behalf-Of“ eine Web-API aufruft.

Für das Szenario mit der Daemon-Anwendung, die eine Web-API aufruft, ist es wichtig, einige Dinge zu verstehen: Da mit einer Daemon-Anwendung keine Benutzerinteraktion möglich ist, benötigt die Anwendung ihre eigene Identität. Ein Beispiel für eine Daemon-Anwendung wäre ein Batchauftrag oder ein im Hintergrund ausgeführter Betriebssystemdienst. Diese Art von Anwendung fordert auf der Grundlage ihrer Anwendungsidentität ein Zugriffstoken an und gibt dabei gegenüber Azure AD ihre Client-ID, ihre Anmeldeinformationen (Kennwort oder Zertifikat) sowie ihren Anwendungs-ID-URI an. Nach erfolgreicher Authentifizierung erhält die Daemon-Anwendung ein Zugriffstoken von Azure AD, mit dem sie dann die Web-API aufrufen kann.

Das Szenario mit der Serveranwendung, die eine Web-API aufruft, lässt sich am besten mit einem Beispiel verdeutlichen. Angenommen, ein Benutzer hat sich bei einer systemeigenen Anwendung authentifiziert, und diese systemeigene Anwendung muss eine Web-API aufrufen. Azure AD stellt ein JWT-Zugriffstoken für den Web-API-Aufruf aus. Wenn die Web-API ihrerseits eine nachgelagerte Web-API aufrufen muss, kann sie den On-Behalf-Of-Fluss verwenden, um die Identität des Benutzers zu delegieren und sich gegenüber der Web-API der zweiten Ebene zu authentifizieren.

#### Diagramm

![Daemon- oder Serveranwendung zu Web-API – Diagramm](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### Beschreibung des Protokollflusses

##### Anwendungsidentität mit OAuth 2.0-Clientanmeldeinformationen

1. Zuerst muss sich die Serveranwendung mit Azure AD ohne Benutzerinteraktion (also beispielsweise ohne interaktives Anmeldedialogfeld) als sie selbst authentifizieren. Sie sendet eine Anfrage an den Token-Endpunkt von Azure AD. Diese Anfrage enthält die Anmeldeinformationen, die Client-ID und den Anwendungs-ID-URI.


2. Azure AD authentifiziert die Anwendung und gibt ein JWT-Zugriffstoken zurück, das zum Aufrufen der Web-API verwendet wird.


3. Die Webanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.


##### Delegierte Benutzeridentität mit OAuth 2.0-Entwurfsspezifikation „On-Behalf-Of“

Im folgenden Ablauf wird davon ausgegangen, dass ein Benutzer in einer anderen Anwendung (beispielsweise in einer systemeigenen Anwendung) authentifiziert und auf der Grundlage der Benutzeridentität ein Zugriffstoken für die Web-API der ersten Ebene abgerufen wurde.

1. Die systemeigene Anwendung sendet das Zugriffstoken an die Web-API der ersten Ebene.


2. Die Web-API der ersten Ebene sendet eine Anforderung an den Token-Endpunkt von Azure AD. Diese enthält die Client-ID und die Anmeldeinformationen sowie das Zugriffstoken des Benutzers. Die Anforderung wird zudem mit einem Parameter vom Typ „on\_behalf\_of“ versehen, der angibt, dass die Web-API neue Token anfordert, um im Auftrag des ursprünglichen Benutzers eine nachgelagerte Web-API aufzurufen.


3. Azure AD überprüft, ob die Web-API der ersten Ebene über Zugriffsberechtigungen für die Web-API der zweiten Ebene verfügt, und validiert die Anforderung. Dabei werden an die Web-API der ersten Ebene ein JWT-Zugriffstoken und ein JWT-Aktualisierungstoken zurückgegeben.


4. Anschließend ruft die Web-API der ersten Ebene per HTTPS die Web-API der zweiten Ebene auf, indem sie die Tokenzeichenfolge an den Autorisierungsheader der Anforderung anfügt. Die Web-API der ersten Ebene kann die Web-API der zweiten Ebene aufrufen, solange das Zugriffstoken und die Aktualisierungstoken gültig sind.

#### Codebeispiele

Sehen Sie sich die Codebeispiele für Szenarien vom Typ „Daemon- oder Serveranwendung zu Web-API“ an. Schauen Sie außerdem regelmäßig vorbei: Wir fügen immer wieder neue Beispiele hinzu. [Server- oder Daemon-Anwendung zu Web-API](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### Registrieren

- Einzelinstanzanwendung: Die Daemon- oder Serveranwendung muss sowohl bei Verwendung der Anwendungsidentität als auch bei Verwendung der delegierten Benutzeridentität im gleichen Verzeichnis in Azure AD registriert werden. Die Web-API kann so konfiguriert werden, dass sie einen Satz von Berechtigungen verfügbar macht, die den Ressourcenzugriff der Daemon- oder Serveranwendung beschränken. Bei Verwendung einer delegierten Benutzeridentität muss die Serveranwendung die gewünschten Berechtigungen aus dem Dropdownmenü „Berechtigungen für andere Anwendungen“ des Azure-Verwaltungsportals auswählen. Bei Verwendung der Anwendungsidentität ist dieser Schritt nicht erforderlich.


- Mehrinstanzenfähige Anwendung: Die Daemon- oder Serveranwendung wird so konfiguriert, dass sie die Berechtigungen angibt, die für eine ordnungsgemäße Verwendung erforderlich sind. Die Liste mit den erforderlichen Berechtigungen wird in einem Dialogfeld angezeigt, wenn ein Benutzer oder Administrator im Zielverzeichnis der Anwendung zustimmt. Dadurch wird die Anwendung in ihrer Organisation verfügbar. Einige Anwendungen benötigen nur Berechtigungen auf Benutzerebene. Diesen kann jeder Benutzer in der Organisation zustimmen. Andere Anwendungen benötigen Berechtigungen auf Administratorebene. Diesen kann ein Benutzer in der Organisation nicht zustimmen. Nur ein Verzeichnisadministrator kann seine Zustimmung für Anwendungen geben, die diese Berechtigungsebene erfordern. Wenn der Benutzer oder Administrator seine Zustimmung gibt, werden die beiden Web-APIs in seinem Verzeichnis registriert.

#### Tokenablauf

Wenn die erste Anwendung mithilfe ihres Autorisierungscodes ein JWT-Zugriffstoken abruft, erhält sie auch ein JWT-Aktualisierungstoken. Dank des Aktualisierungstokens kann der Benutzer bei Ablauf des Zugriffstokens erneut authentifiziert werden, ohne zur Eingabe seiner Anmeldeinformationen aufgefordert zu werden. Das Aktualisierungstoken wird dann zum Authentifizieren des Benutzers verwendet, und ein neues Zugriffstoken sowie ein neues Aktualisierungstoken werden erstellt.

## Weitere Informationen

[Entwicklerhandbuch zu Azure Active Directory](active-directory-developers-guide.md)

[Azure Active Directory-Codebeispiele](active-directory-code-samples.md)

[Wichtige Informationen zum Signaturschlüsselrollover in Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx)

[OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)

<!---HONumber=AcomDC_0114_2016-->