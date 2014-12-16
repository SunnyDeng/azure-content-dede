#Azure-Sicherheitsleitfaden

##Zusammenfassung

Beim Entwickeln von Anwendungen für Azure sind Identität und
	Zugriff sind die vorrangigen Sicherheitsaspekte, die Sie berücksichtigen müssen.
In diesem Thema werden die wichtigsten Sicherheitserwägungen im Zusammenhang mit Identitäts- und Zugriffsverwaltung in der Cloud erläutert, und es wird erklärt, wie Sie
Ihre Cloud-Anwendungen am besten schützen können.

##Übersicht

Die Sicherheit einer Anwendung ist eine Funktion ihrer Oberfläche. Je größer die Oberfläche,
die von der Anwendung offengelegt wird, desto größer sind die Sicherheitsbedenken. Weitere
Beispielsweise stellt eine Anwendung, die als ein unbeaufsichtigter Stapelverarbeitungsvorgang ausgeführt wird,
vom Sicherheitsstandpunkt her gesehen eine kleinere Oberfläche bereit als eine öffentlich verfügbare Website.

Wenn Sie zur Cloud migrieren, müssen Sie sich über
Infrastruktur und Netzwerke keine Gedanken machen, da diese in Rechenzentren
mit erstklassigen Sicherheitsverfahren Tools und Technologie verwaltet werden. Andererseits
bietet die Cloud eine größere systeminterne Oberfläche für die
Anwendung, die von Angreifern potenziell ausgenutzt werden kann. Der Grund hierfür ist,
dass viele Cloud-Technologien und Dienste als Endpunkte verfügbar gemacht werden
und nicht als im Arbeitsspeicher befindliche Komponenten. Azure-Speicher, Service Bus, SQL
-Datenbank (früher SQL Azure) und viele andere Dienste sind über
über ihre Endpunkte über das Netzwerk zugänglich.

Bei Cloudanwendungen sind die Anwendungsentwickler stärker dafür verantwortlich,
die Cloudanwendungen unter Einhaltung hoher Sicherheitsstandards zu entwerfen, zu entwickeln und zu warten,
um Angreifer fernzuhalten.
Betrachten Sie das folgende Diagramm (aus [Azure Security
Notes PDF](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx) von J.D. Meier): beachten Sie, dass der Infrastrukturteil vom Cloudanbieter
- in unserem Fall Azure - berücksichtigt wird, sodass mehr Sicherheitsaufgaben in
den Tätigkeitsbereich der Anwendungsentwickler fallen:

![Securing the application][01]

Von Vorteil ist, dass alle Sicherheitsentwicklungsverfahren,
Prinzipien und Techniken, die Sie bereits kennen, auch für die Entwicklung von
Cloudanwendungen gelten. Auf die folgenden Schlüsselbereiche muss eingegangen
werden:

-   Alle Eingaben werden auf Typ, Länge, Bereich und Zeichenfolgenmuster überprüft,
    um Injection-Angriffe zu vermeiden, und alle von der Anwendung zurückgegebenen Daten
    werden ordnungsgemäß bereinigt.
-   Vertrauliche Daten sollten im Ruhezustand und bei der Übertragung geschützt werden, um
    die Gefahr der Offenlegung von Informationen und Datenmanipulation zu verringern.
-   Überwachung und Protokollierung müssen ordnungsgemäß implementiert sein, um Repudiation-Bedrohungen
    zu verringern.
-   Authentifizierung und Autorisierung müssen unter Verwendung von bewährten
    Mechanismen, die auf der Plattform verfügbar sind, implementiert werden, um Identitäts-Spoofing und
    Rechteerweiterungen zu verhindern.

Eine vollständige Liste von Bedrohungen, Angriffen, Sicherheitsrisiken und
Gegenmaßnahmen finden Sie in ' [Cheat Sheet: Web
Application Security Frame](http://msdn.microsoft.com/de-de/library/ff649461.aspx) und [Security Guidance for Applications Index](http://msdn.microsoft.com/de-de/library/ff650760.aspx).

In der Cloud unterscheiden sich Authentifizierung und Zugriffssteuerungsmechanismen sehr
stark von den in lokalen Anwendungen verfügbaren Mechanismen. Es sind
mehr Authentifizierungs- und Zugriffsoptionen für Cloudanwendungen
verfügbar, die verwirrend sein können und daher zu fehlerhaften
Implementierungen führen können. Ein weiterer Unsicherheitsfaktor ist die Definition einer
Cloudanwendung. Beispielsweise kann eine Anwendung in der Cloud bereitgestellt werden, aber
einen von Active Directory bereitgestellten Authentifizierungsmechanismus verwenden. Andererseits
kann eine Anwendung lokal bereitgestellt werden, aber einen
Authentifizierungsmechanismen in der Cloud (z. B. von Azure
Active Directory-Zugriffssteuerung (vormals Access Control
Service (ACS) oder Zugriffssteuerungsdienst genannt)) verwenden.

##Bedrohungen, Schwachstellen und Angriffe

Eine Bedrohung ist ein potenziell schlechtes Ergebnis, das Sie vermeiden sollten, z. B.
die Offenlegung vertraulicher Informationen oder die Nichtverfügbarkeit eines Diensts.
Nach gängiger Praxis werden Bedrohungen mit dem Akronym "STRIDE" klassifiziert:

-   **S**poofing oder Identitätsdiebstahl
-   **D**atenfälschung/-manipulation
-   **R**epudiation von Aktionen
-   **O**ffenlegung von Informationen
-   **D**enial-of-Service
-   **R**echteerweiterungen

Sicherheitsrisiken sind fehler, die wir als Entwickler in den Code einbringen
und durch die eine Anwendung für Angreifer ausnutzbar wird. Beispielsweise das Senden
von vertrauliche Daten im Klartext ermöglicht die Offenlegung von Informationen
durch einen Angriff zur Ausspähung des Datenverkehrs.

Angriffe sind die Ausnutzung dieser Schwachstellen, um einer Anwendung zu
schaden. Beispielsweise ist Cross Site Scripting oder XSS ein
Agriff, der unbereinigte Ausgaben ausnutzt. Ein weiteres Beispiel ist
das Ausspähen einer Übertragung zum Abfangen unverschlüsselter Anmeldedaten. Diese
Angriffe können dazu führen, dass die Bedrohung einer Identitätsfälschung (Spoofing) umgesetzt wird. Einfach ausgedrückt,
können Sie Bedrohungen, Schwachstellen und Angriffe als etwas Schlechtes betrachten.
Betrachten Sie die folgenden Diagramme als Überblick über das Schlechte
im Zusammenhang mit in Azure bereitgestellten Webanwendungen (aus
[Azure Security Notes PDF](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx) von J. D. Meier):

![Threats Vulnerabilities and Attacks][02]

Sie als Entwickler können diese Sicherheitsrisiken steuern. Je weniger
Sicherheitsrisiken Sie eröffnen, desto weniger Möglichkeiten können
Angreifer ausnutzen.

Sicherheitsrisiken im Zusammenhang mit Identität und Zugriff machen Ihre Anwendungen für alle
Bedrohungen im STRIDE-Modell anfällig. Beispielsweise kann ein falsch implementierter
Authentifizierungsmechanismus zu einer identitätsfälschung und in der Folge
zur Offenlegung von Informationen, Datenmaniplulation, Rechterweiterungen
oder sogar zum Dienstausfall führen. Berücksichtigen Sie die folgenden
Fragen, die auf mögliche Sicherheitslücken in der Identitäts- und Zugriffsimplementierung
der Cloudanwendung hinweisen können:

-   Senden Sie Anmeldeinformationen unverschlüsselt an Ihre
    Azure-Dienste?
-   Speichern Sie Anmeldeinformationen für Azure-Dienste unverschlüsselt?
-   Folgen Ihre Anmeldeinformationen für Azure-Dienste den Richtlinien für starke
    Kennwörter?
-   Verlassen Sie sich darauf, dass Azure die Anmeldeinformationen prüft, oder
    verwenden Sie benutzerdefinierte Prüfmechanismen?
-   Beschränken Sie die Lebensdauer von Authentifierungssitzungen oder -token für Azure-Dienste
    auf ein angemessenes Zeitfenster?
-   Überprüfen Sie die Berechtigungen für jeden einzelnen Azure-Einstiegspunkt der
    verteilten Cloudanwendung?
-   Verfügt Ihr Autorisierungsmechanismus über eine Fehlerbehandlung, welche die Offenlegung
    vertraulicher Daten oder unbeschränkte Zugriffe unterbindet?

##Gegenmaßnahmen

Die beste Gegenmaßnahme gegen Angriffe besteht in der Verwendung der Identitäts- und
Zugriffsmechanismen, die auf der Platttform zur Verfügung stehen, statt der Implementierung
eigener Mechanismen. Betrachten Sie die folgenden bekannten Identitäts- und Zugriffstechnologien:

**Windows Identity Foundation (WIF).** WIF ist eine .NET-Laufzeitbibliothek,
die zum Lieferumfang von .NET Framework 4.5 gehört (sie ist auch als separater
Download für .NET 3.5/4.0 verfügbar). WIF übernimmt den Großteil der Verarbeitung von
Protokollen, wie WS-Federation und WS-Trust, und die Tokenverarbeitung, z. B.
Security Assertion Markup Language (SAML), sodass Sie keinen sehr
komplexen sicherheitsbezogenen Code in der Anwendung schreiben müssen. Im folgenden
sind Ressourcen mit detaillierten Informationen zu WIF aufgeführt:

-   [Windows Identity Foundation 4.5-Beispiele](http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication) in der MSDN Code Gallery.
-   [Windows Identity Foundation 4.5-Tools für Visual Studio 11 Beta](http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e) in der
    MSDN Code Gallery.
-   [Windows Identity Foundation-Laufzeit (.Net 3.5/4.0)](http://www.microsoft.com/de-de/download/details.aspx?id=17331) auf MSDN.
-   [Besipeile zu Windows Identity Foundation 3.5/4.0 und Visual Studio
    2008/2010-Vorlagen](http://www.microsoft.com/de-de/download/details.aspx?displaylang=en&id=4451) auf MSDN.

**Azure AD Access Control (vormals ACS genannt)**. 
Azure AD Access Control ist ein Cloud-Dienst, der den Security Token
Service (STS) bereitstellt und den Verbund mit verschiedenen Identitätsanbietern
zulässt, wie Active Directory in einem Unternehmen oder Internet-Identitätsanbieter wie
Windows Live-ID / Microsoft-Konto, Facebook, Google, Yahoo! und Open
ID 2.0-Identitätsanbieter. Die folgenden Ressourcen bieten detaillierte
Informationen über Azure AD Access Control:

-   [Access Control Service 2.0](http://msdn.microsoft.com/library/gg429786.aspx) 
-   [Szenarien und Lösungen mit ACS](http://msdn.microsoft.com/de-de/library/gg185920.aspx)
-   [ACS - Vorgehensweisen](http://msdn.microsoft.com/de-de/library/windowsazure/gg185939.aspx)
-   [A Guide to Claims-Based Identity and Access Control (in englischer Sprache)](http://msdn.microsoft.com/de-de/library/ff423674.aspx)
-   [Identity Developer Training Kit (nur in englischer Sprache)](http://www.microsoft.com/de-de/download/details.aspx?id=14347)
-   [MSDN-hosted Identity Developer Training Course (in englischer Sprache)](http://msdn.microsoft.com/de-de/IdentityTrainingCourse)

**Active Directory Federation Services (AD FS).**Active Directory
Federation Services (AD FS) 2.0 unterstützt anspruchsbasierte
Identitätslösungen, die Windows Server??- und Active Directory-Technologie
nutzen. AD FS 2.0 unterstützt die Protokolle WS-Trust, WS-Federation und SAML
. In den folgenden Ressourcen finden Sie detaillierte Informationen über AD
FS:

-   [AD FS 2.0 Content Map (in englischer Sprache)](http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx)
-   [Web SSO Design][Web SSO Design]
-   [Federated Web SSO Design][Federated Web SSO Design]

**Azure Shared Access Signatures.** Shared Access Signatures
ermöglicht Ihnen die Feinabstimmung des Zugriffs auf eine Blob- oder Containerressource. In
den folgenden Ressourcen finden Sie detaillierte Informationen über Shared Access
Signatures.

-   [Managing Access to Blobs and Containers (in englischer Sprache)](http://msdn.microsoft.com/de-de/library/ee393343.aspx)
-   [New Storage Feature: Shared Access Signatures (in englischer Sprache)](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [Shared Access Signatures Are Easy These Days (in englischer Sprache)](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)

##Szenarienübersicht

In diesem Abschnitt werden kurz die wichtigsten in diesem Thema behandelten Szenarien umrissen.
Nutzen Sie ihn als Orientierungshilfe, um die richtige Identitätslösung für Ihre Anwendung zu ermitteln
der Anwendung in der Stagingumgebung befindet.

-   **ASP.NET-Webformularanwendung mit Verbundauthentifizierung.** In
    diesem Szenario steuern Sie den Zugriff auf Ihre ASP.NET Web Forms-Anwendung mit
    einer Internetidentität wie Live ID / Microsoft-Konto oder
    einer in Windows Server Active Directory verwalteten Unternehmensidentität.
-   **WCF (SOAP)-Dienst mit Verbundauthentifizierung.**In diesem
    Szenario steuern Sie den Zugriff auf den WCF (SOAP)-Dienst über Service-
    Identitäten, die von Azure AD Access Control verwaltet werden.
-   **WCF (SOAP)-Dienst mit Verbundauthentifizierung, Identitäten in
    Active Directory.** In diesem Szenario steuern Sie den Zugriff auf Ihren WCF
    (SOAP)-Webdienst mit Identitäten, die von Windows
    Server Active Directory verwaltet werden.
-   **WCF (REST)-Dienst mit Verbundauthentifizierung.**In diesem
    Szenario steuern Sie den Zugriff auf den WCF (REST)-Dienst über Service-
    Identitäten, die von Azure AD Access Control verwaltet werden.
-   **WCF (REST)-Dienst mit Live-ID/Microsoft-Konto, Facebook,
    Google, Yahoo!, Open ID.** In diesem Szenario steuern Sie den Zugriff auf
    Ihren WCF (REST)-Dienst mit einer Internet-Identität wie Live-ID/
    Microsoft-Konto.
-   **ASP.NET-Webanwendung für den REST WCF-Dienst mit freigegebenem SWT-Token.** In
    diesem Szenario haben Sie eine verteilte Anwendung mit Front-End
    ASP.NET-Webanwendung und REST-Dienst, und Sie möchten den Kontext der
    Benutzer durch physische Ebenen leiten.
-   **Autorisierung über rollenbasierte Zugriffssteuerung (RBAC) in Ansprüche unterstützenden
    Anwendungen und Diensten.** In diesem Szenario möchten Sie eine auf
    Rollen basierende Autorisierungslogik in Ihrer Anwendung implementieren.
-   **Anspruchsbasierte Autorisierung in Ansprüche unterstützenden Anwendungen und
    Diensten.** In diesem Szenario möchten Sie eine auf komplexen
    Autorisierungsregeln basierende Autorisierungslogik implementieren.
-   **Azure Storage-Dienstidentität und Zugriffsszenarien.**In
    diesem Szenario müssen Sie auf sichere Weise den Zugriff auf Azure
    Speicher-Blobs und Container freigeben.
-   **Azure-SQL-Datenbankidentität und Zugriffsszenarien.**SQL-
    Datenbank untersützt nur die SQL-Serverauthentifizierung. Windows-
    Authentifizierung (integrierte Sicherheit) wird nicht unterstützt. Die Benutzer müssen
    jedes Mal, wenn sie eine Verbindung mit der SQL-Datenbank herstellen, Anmeldeinformationen (Benutzername und Kennwort)
    eingeben.
-   **Azure Service Bus-Identität und Zugriffsszenarien.**In diesem
    Szenario müssen Sie auf sichere Weise auf Azure Service Bus-Warteschlangen zugreifen.
-   **In-Memory-Cacheidentität und Zugriffszenarien.**In diesem Szenario
    müssen Sie auf sichere Weise auf Daten zugreifen, die vom In-Memory-Cache verwaltet werden.
-   **Azure Marketplace-Identität und Zugriffsszenarien.**In diesem
    Szenario müssen Sie auf sichere Weise auf Azure Marketplace-Datasets
    zugreifen.

##Azure-Identität und Zugriffsszenarien

In diesem Abschnitt werden gängige Identitäts- und Zugriffsszenarien für verschiedene
Anwendungsarchitekturen skizziert. Die Szenarienübersicht bieten einen schnellen
Überblick.

###ASP.NET-Webformularanwendung mit Verbundauthentifizierung

In diesem Anwendungsarchitekturszenario kann die Webanwendung
in Azure oder lokal bereitgestellt werden. Die Anwendung erfordert, dass
die Benutzer von Active Directory im Unternehmen authentifiziert werden oder von
Internet-Identitätsanbietern.

Sie verwenden in diesem Szenario Azure AD Access control und Windows
Identity Foundation.

![Azure Active Directory Access control][03]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Erstellen einer ersten Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von ACS](http://msdn.microsoft.com/de-de/library/gg429779.aspx)
-   [Gewusst wie: Hosten von Anmeldeseiten in einer ASP-NET-Webanwendung](http://msdn.microsoft.com/de-de/library/gg185926.aspx)
-   [Gewusst wie: Implementieren von Anspruchsautorisierung in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS](http://msdn.microsoft.com/de-de/library/gg185907.aspx)    
-   [Gewusst wie: Implementieren rollenbasierter Zugriffssteuerung (RBAC) in Ansprüche unterstützenden
    ASP.NET-Anwendungen mit WIF und ACS](http://msdn.microsoft.com/de-de/library/gg185914.aspx)
-   [Gewusst wie: Konfigurieren der Vertrauensstellung zwischen ACS und ASP.NET-Webanwendungen
    Verwenden von X.509-Zertifikaten](http://msdn.microsoft.com/de-de/library/gg185947.aspx)
-   [Codebeispiel: Einfache ASP.NET-Formulare](http://msdn.microsoft.com/de-de/library/gg185938.aspx)

###WCF (SOAP)-Dienst mit Dienstidentität

In diesem Anwendungsarchitekturszenario kann der WCF (SOAP)-Dienst
in Azure oder lokal bereitgestellt werden. Auf den Dienst wird als
Downstreamdienst von Webanwendungen oder sogar von anderen Webdiensten
zugegriffen. Sie müssen den Zugriff auf diesen Dienst mithilfe einer anwendungsspezifischen
Identität steuern. Stellen Sie ihn sich als Art von Anwendungspoolkonto vor, das Sie
in IIS verwendet haben. Die Technologie ist zwar unterschiedlich, aber die Ansätze sind
ähnlich, weil der Zugriff auf den Dienst über ein Anwendungskonto
und nicht über ein Endbenutzerkonto erfolgt.

Verwenden Sie die Funktion "Dienstidentität" in Azure AD Access Control.
Dies ist mit dem IIS-Anwendungspoolkonto vergleichbar, dass Sie zum
Bereitstellen von Anwendungen in Windows Server und IIS verwendet haben. Konfigurieren Sie Azure
AD Access Control für die Ausgabe von SAML-Token, die von WIF im
WCF (SOAP)-Dienst verarbeitet werden.

![WCF (SOAP) Service][04]


In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Hinzufügen von Dienstidentitäten mit einem X.509-Zertifikat, Kennwort
    der symmetrischen Schlüssel](http://msdn.microsoft.com/de-de/library/gg185924.aspx)
-   [Gewusst wie: Authentifizierung mit einem Clientzertifikat bei einem durch ACS geschützten WCF-Dienst
    Geschützt durch ACS](http://msdn.microsoft.com/de-de/library/hh289316.aspx)
-   [Gewusst wie: Authentifizierung mit einem Benutzernamen und einem Kennwort bei einem WCF-Dienst,
    der durch ACS geschützt wird](http://msdn.microsoft.com/de-de/library/gg185954.aspx)
-   [Codebeispiel: WCF-Zertifikatauthentifizierung](http://msdn.microsoft.com/de-de/library/gg185952.aspx)
-   [Codebeispiel: WCF-Benutzernamenauthentifizierung](http://msdn.microsoft.com/de-de/library/gg185927.aspx)

###WCF (SOAP)-Dienst mit Verbundauthentifizierung, Identitäten in Active Directory

In diesem Anwendungsarchitekturszenario kann der WCF (SOAP)-Dienst
in Azure oder lokal bereitgestellt werden. Sie müssen den Zugriff darauf
unter Verwendung einer Identität steuern, die von Windows Server
Active Directory (AD) verwaltet wird.

Verwenden Sie Azure AD Access Control, und konfigurieren Sie die Funtion für den Verbund mit
Windows Server AD FS. In diesem Fall müssen Sie die Dienstidentität nicht
mit Azure AD Access Control konfiguieren. Der Agent, der auf
den WCF (SOAP)-Dienst zugreifen muss, übergibt Anmeldeinformationen an AD FS und nach
der erfolgreichen Authentifizierung wird der Token von AD FS ausgegeben. Der Token wird
dann an Azure AD Access Control übermittelt und wieder an den
den Agent zurückgegeben. Der Agent verwendet den Token, um eine Anforderung an den WCF (SOAP)-Dienst
zu senden.

![WCF (SOAP) Service With AD][05]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Hinzufügen von Dienstidentitäten mit einem X.509-Zertifikat, Kennwort
    der symmetrischen Schlüssel](http://msdn.microsoft.com/de-de/library/gg185924.aspx)
-   [Gewusst wie: Konfigurieren von AD FS 2.0 als Identitätsanbieter](http://msdn.microsoft.com/de-de/library/gg185961.aspx)
-   [Gewusst wie: Verwenden des Verwaltungsdiensts zum Konfigurieren von AD FS 2.0 als
    Unternehmensidentitätsanbieter](http://msdn.microsoft.com/de-de/library/gg185905.aspx)
-   [Codebeispiel: WCF-Verbundauthentifizierung mit AD FS 2.0
](http://msdn.microsoft.com/de-de/library/hh127796.aspx)

###WCF (REST)-Dienst mit Dienstidentitäten

In diesem Szenario kann der WCF (REST)-Dienst in 
Azure oder lokal bereitgestellt werden. Auf den Dienst wird als nachgelagerter Dienst von
Webanwendungen oder sogar von anderen Webdiensten zugegriffen. Sie müssen den Zugriff auf diesen Dienst
mithilfe einer anwendungsspezifischen Identität steuern. Stellen Sie ihn sich als eine
Art von Anwendungspoolkonto vor, das Sie in IIS verwendet haben. Die Technologie ist zwar
 unterschiedlich, aber die Ansätze sind ähnlich, weil der Zugriff auf den Dienst
über ein Anwendungskonto und nicht über ein Endbenutzerkonto erfolgt.

Verwenden Sie die Funktion "Dienstidentität" in Azure AD Access Control.
Konfigurieren Sie Azure AD Access Control so, dass SWT (Simple Web Token)-Token
 ausgestellt werden. Um die SWT-Token im REST-Dienst zu verarbeiten, können Sie
entweder einen benutzerdefinierten Tokenhandler implementieren und in die WIF-Pipeline
integrieren oder den Token "manuell" analysieren, ohne die WIF-Infrastruktur
zu verwenden.

Beachten Sie das folgende Diagramm (WIF ist optional):

![REST Service][06]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Konfigurieren der Vertrauensstellung zwischen ACS und dem WCF-Dienst mithilfe symmetrischer
    Schlüssel](http://msdn.microsoft.com/de-de/library/gg185958.aspx)
-   [Gewusst wie: Authenticate to a REST WCF Service Deployed to Azure
    Using ACS](http://msdn.microsoft.com/de-de/library/hh289317.aspx)
-   [Codebeispiel: ASP.NET-Webdienst](http://msdn.microsoft.com/de-de/library/gg983271.aspx)
-   [Codebeispiel: Windows Phone 7-Anwendung](http://msdn.microsoft.com/de-de/library/gg983271.aspx)
-   [REST WCF With SWT Token Issued By Azure Access Control
    Service (ACS)](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)

###WCF (REST)-Dienst mit Live ID/Microsoft-Konto, Facebook, Google, Yahoo!, Open ID

In diesem Szenario kann Ihr WCF (REST)-Dienst in 
Azure oder lokal bereitgestellt werden. Sie müssen den Zugriff auf den Dienst mit einer öffentlichen
Internetidentität, wie Live ID/Microsoft-Konto oder Facebook, steuern.

Nutzen Sie Azure AD Access Control zum Ausstellen der SWT-Token. Um die
SWT-Token im REST-Dienst zu verarbeiten, können Sie einen benutzerdefinierten
Tokenhandler implementieren und in die WIF-Pipeline einbinden, oder Sie können ihn "manuell" analysieren,
ohne die WIF-Infrastruktur zu verwenden.

Es ist unbedingt zu beachten, dass die Implementierung dieses Szenarios erfordert, dass
die Anwendung das Webbrowser-Steuerelement verwendet, um die
Anmeldeinformationenen vom Endbenutzer zu erfassen. Dadurch eignet sie sich nicht für Szenarien, in denen von einer
ASP.NET-Webanwendung auf den REST-Dienst zugegriffen wird. Sie eignet sich nur für
Szenarien, in denen von der Clientanwendung des Benutzers aus auf den REST-Dienst zugegriffen wird,
z. B. eine Windows Phone 7-App oder ein Rich-Desktop-
Client. Der Hauptgrund für das Entfernen des Webbrowser-Steuerelements ist, dass
Internet-Identitäten keine Szenarien mit aktivem Profil (Webdienstszenarien)
an sich nicht unterstützen. Internet-Identitäten unterstützen vorwiegend Szenarien mit passiven Profilen,
(Webanwendungen), die sich auf Browserumleitungen stützen: Hier ist das
Webbrowser-Steuerelement nützlich.

Beachten Sie das folgende Diagramm (WIF ist optional und wird daher nicht gezeigt):

![WIF is optional][07]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Authenticate to a REST WCF Service Deployed to Azure Using ACS (in englischer Sprache)](http://msdn.microsoft.com/de-de/library/hh289317.aspx)
-   [Gewusst wie: Konfigurieren von Google als Identitätsanbieter](http://msdn.microsoft.com/de-de/library/gg185976.aspx)
-   [Gewusst wie: Konfigurieren von Facebook als Identitätsanbieter](http://msdn.microsoft.com/de-de/library/gg185919.aspx)
-   [Gewusst wie: Konfigurieren von Yahoo! als Identitätsanbieter](http://msdn.microsoft.com/de-de/library/gg185977.aspx)
-  [Codebeispiel: Windows Phone 7-Anwendung](http://msdn.microsoft.com/de-de/library/gg983271.aspx)
-   [REST WCF With SWT Token Issued By Azure Access Control
    Service (ACS)](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)


###ASP.NET-Webanwendung für den REST WCF-Dienst mit freigegebenem SWT-Token

In diesem Szenario haben Sie eine verteilte Anwendung mit Front-End
ASP.NET-Webanwendung und einen nachgelagerten REST-Dienst, und Sie möchten den Kontext der
Benutzer über physische Ebenen hinweg verwalten. Das ist manchmal erforderlich,
wenn die Autorisierungslogik oder Protokollierung auf der Grundlage der
Identität des Endbenutzers im nachgelagerten REST-Dienst implementiert werden soll.

Konfigurieren Sie Azure AD Access Control zur Ausgabe von SWT-Token. Der SWT-Token
wird für die Front-End-ASP.NET-Webanwendung ausgestellt und dann für
den nachgelagerten REST-Dienst freigegeben. In diesem Fall ist nur eine Partei
in Azure AD Access Control konfiguriert. Allerdings gibt es
einige Vorbehalte:

-   Da WIF keinen SWT-Tokenhandler bereitstellt, müssen Sie
    einen benutzerdefinierten Tokenhandler für die Verwendung mit der ASP.NET-
    Webanwendung implementieren. Sie sollten die verfügbaren WIF-Funtionen zur
    Unterstützung des WS-Verbund-Protokolls nutzen, das sich auf Browserumleitungen sützt,
    statt eigene Funktionen zu implementieren.
-   Wenn Sie einen benutzerdefinierten SWT-Tokenhandler implementieren, stellen Sie sicher, dass
    Bootstrap-Token berücksichtigt wird, um sicherzustellen, dass er beibehalten wird.
    Andernfalls können Sie ihn nicht freigeben und an den
    nachgelagerten REST-Dienst senden.
-   Sie müssen für einen REST-Dienst nicht WIF verwenden. Stattdessen können Sie
    den Token "manuell" analysieren, da in diesem Fall keine Umleitungen
    verarbeitet werden müssen.

![ASP.NET Web Application][08]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Konfigurieren von Google als Identitätsanbieter](http://msdn.microsoft.com/de-de/library/gg185976.aspx)
-   [Gewusst wie: Konfigurieren von Facebook als Identitätsanbieter](http://msdn.microsoft.com/de-de/library/gg185919.aspx)
-   [Gewusst wie: Konfigurieren von Yahoo! als Identitätsanbieter](http://msdn.microsoft.com/de-de/library/gg185977.aspx)
-   [ASP.NET Web App To REST WCF Service Delegation Using Shared SWT
    Token](http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82)

###Autorisierung über rollenbasierte Zugriffssteuerung (RBAC) in Ansprüche unterstützenden Anwendungen und Diensten

In diesem Szenario müssen Sie eine auf Benutzerrollen basierende Autorisierung in Ihrer
Webanwendung oder Ihrem Dienst implementieren: Benutzer mit den erforderlichen Rollen erhalten
Zugriff, und den Benutzern ohne die erforderlichen Rollen wird der Zugriff verweigert. Einfach ausgedrückt,
die Anwendung muss nur eine einfache Frage beantworten: "Hat der Benutzer die Rolle
 X?".

Dieses Szenario kann auf verschiedene Weise gelöst werden. Sie können Azure
AD Access Control, WIF Claims Authentication Manager,
samlSecurityTokenRequirement-Zuordnung oder Customer Role Manager verwenden.

WIF wird in jedem Fall verwendet. WIF unterstützt die IPrincipal.IsInRole("MyRole")
-Methode. In den meisten Fällen soll mit dem Schlüssel dafür gesorgt werden, dass der Rollentypanspruch
mit dem URI
"http://schemas.microsoft.com/ws/2008/06/identity/claims/role" im
Token vorhanden ist, sodass WIF die Rollenmitgliedschaft erfolgreich überprüfen kann, wenn die
IsInRole-Methode aufgerufen wird.

**Azure AD Access Control**. In dieser Implementierung wird das
Azure AD Access Control-Anspruchstransformationsregelmodul verwendet. Unter
Verwendung des Anspruchstransformationsregelmoduls können Sie jeden
eingehenden Anspruch in einen Rollentypanspruch umformen, wenn der Token auf die
Anwendung trifft, oder ein Dienst-WEF kann diesen Rollenanspruch analysieren, um sicherzustellen, dass der
IsInRole-Methodenaufruf erfolgreich ist.

![][09]

**WIF-ClaimsAuthenticationManager**. In dieser Implementierung wird
ClaimsAuthenticationManager als Erweiterungspunkt von WIF verwendet. Dieser
Ansatz ermöglicht es, beliebige eingehenden Ansprüche in einen Rollenanspruch
in der Anwendung umzuformen. Die Komplexität dieser Umformung wird nur durch
den Code, den Sie schreiben, begrenzt.

![][10]

**samlSecurityTokenRequriement-Zuordnung**. In dieser Implementierung wird
die SamlSecurityTokenRequirement-Konfiguration in der Datei "web.config" verwendet, um WIF mitzuteilen,
welche Anspruchstypen sich wie Rollenanspruchstypen verhalten. Beispiel: 
Wenn das Token beispielsweise Träger eines Anspruchs des Gruppentyps ist, können Sie es dem Rollenanspruchstyp
zuordnen. Mit dieser Vorgehensweise sind nur einfache Zuordnungen möglich.

![][11]

**Benutzerdefinierter RoleManager.** In dieser Implementierung implementieren Sie einen benutzerdefinierten
RoleManger. WIF wird verwendet, um die eingehenden Ansprüche bei der Implementierung
	benutzerdefinierter RoleManager-Schnittstellenmethoden wie GetAllRoles() zu untersuchen.

![][12]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Implementieren rollenbasierter Zugriffssteuerung (RBAC) in Ansprüche unterstützenden
    ASP.NET-Anwendungen mit WIF und ACS](http://msdn.microsoft.com/de-de/library/gg185914.aspx)
-   [Gewusst wie: Implementieren von Tokentransformationslogik mithilfe von Regeln](http://msdn.microsoft.com/de-de/library/gg185955.aspx)
-   [Authorization With RoleManager For Claims Aware (WIF) ASP.NET Web
    Applications](http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx)
-   Codebeispiel: Using Claims In IsInRole in [Windows Identity Foundation
    SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)

###Anspruchsbasierte Autorisierung in Ansprüche unterstützenden Anwendungen und Diensten

In diesem Szenario müssen Sie eine komplexe Autorisierungslogik in
Ihrer Webanwendung oder dem Dienst implementieren, und die IsInRole()-Methode ist
für Ihre Autorisierungsanforderungen nicht ausreichend. Wenn Ihr Autorisierungsansatz
auf Rollen basiert, empfiehlt sich das Implementieren der rollenbasierten Zugriffssteuerung,
die im vorigen Abschnitt beschrieben wurde.

Verwenden Sie ClaimsAuthorizationManager als den Erweiterungspunkt für WIF.
ClaimsAuthorizationManager ermöglicht externe Aufrufe zur Zugriffsprüfung, sodass
Ihr Anwendungscode übersichtlicher und besser verwaltbar ist als bei
bei der Implementierung von Zugriffsüberprüfungen im Anwendungscode.

![][13]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Implementieren von Tokentransformationslogik mithilfe von Regeln](http://msdn.microsoft.com/de-de/library/gg185955.aspx)
-   [Gewusst wie: Implementieren von Anspruchsautorisierung in einer Ansprüche unterstützenden ASP.NET-
    Anwendung mithilfe von WIF und ACS](http://msdn.microsoft.com/de-de/library/gg185907.aspx)
-   Codebeispiel: Anspruchsbasierte Autorisierung in [Windows Identity
    Foundation SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)


##Azure Storage-Dienstidentität und Zugriffsszenarien

In diesem Szenario müssen Sie auf sichere Weise den Zugriff auf Azure
Speicher-Blobs und Container freigeben.

Verwenden Sie Shared Access Signatures. Um von Ihrer eigenen Anwendung auf Ihr Speicherdienstkonto zuzugreifen,
verwenden Sie den freigegebenen Hashwert, der über das
Azure-Portal verfügbar ist, wenn Sie Speicherdienstkonten konfigurieren und
verwalten. Wenn Sie einer anderen Person Zugriff auf die Blobs und
Container in Ihrem Speicherdienstkonto gewähren möchten, verwenden Sie Shared Access Signatures
URLs.

	Achten Sie besonders auf die sichere Verwaltung der Informationen, um deren
Offenlegung zu vermeiden. Achten Sie zudem auf die Lebensdauer der Shared
Access Signatures.

![][14]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Managing Access to Blobs and Containers (in englischer Sprache)](http://msdn.microsoft.com/de-de/library/ee393343.aspx)
-   [New Storage Feature: Shared Access Signatures (in englischer Sprache)](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [Shared Access Signatures Are Easy These Days (in englischer Sprache)](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)


##Azure SQL-Datenbankidentität und Zugriffsszenarien

SQL-Datenbank untersützt nur die SQL-Serverauthentifizierung. Windows-
    Authentifizierung (integrierte Sicherheit) wird nicht unterstützt. Die Benutzer müssen
jedes Mal, wenn sie eine Verbindung mit der SQL-Datenbank herstellen, Anmeldeinformationen (Benutzername und Kennwort)
eingeben. Achten Sie besonders auf die Verwaltung von Benutzernamen und
Kennwörter, um die Offenlegung von Informationen zu vermeiden.

![][15]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Sicherheitsrichtlinien und Einschränkungen (Windows Azure SQL-Datenbank)](http://msdn.microsoft.com/de-de/library/windowsazure/ff394108.aspx#authentication)
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über sqlcmd](http://msdn.microsoft.com/de-de/library/windowsazure/ee336280.aspx)
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über ADO.NET](http://msdn.microsoft.com/de-de/library/windowsazure/ee336243.aspx)
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über ASP.NET](http://msdn.microsoft.com/de-de/library/windowsazure/ee621781.aspx)
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über WCF Data Services](http://msdn.microsoft.com/de-de/library/windowsazure/ee621789.aspx)
-  [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über PHP](http://msdn.microsoft.com/de-de/library/windowsazure/ff394110.aspx)
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über JDBC](http://msdn.microsoft.com/de-de/library/windowsazure/gg715284.aspx)
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über das ADO.NET Entity Framework](http://msdn.microsoft.com/de-de/library/windowsazure/ff951633.aspx)

##Azure Service Bus-Identität und Zugriffsszenarien

Service Bus und Azure AD Access Control verfügen über eine spezielle
Beziehung, sodass jeder Namespace für einen Service Bus-Dienst mit einem
gleichnamigen Namespace für den entsprechenden Access Control-Dienst mit der Endung
"-sb" verbunden wird. Der Grund für diese spezielle Beziehung ergibt sich aus der Art und Weise,
wie Service Bus und Access Control die gegenseitige Vertrauensbeziehung
und die zugehörigen kryptografischen Schlüssel verwalten. Nähere Informationen finden Sie
in den unten aufgeführten Ressourcen.

![][16]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Securing Service Bus with ACS](http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS) (Video)
-   [Securing Service Bus with ACS](https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849) (Präsentation)
-   [Service Bus-Authentifizierung und -Autorisierung mit dem Access Control
    Service](http://msdn.microsoft.com/de-de/library/hh403962.aspx)

##In-Memory-Cacheidentität und Zugriffszenarien

In-Memory-Cache (ehemals Azure Cache) stützt sich bei der Authentifizierung auf
Azure AD Access Control. Es werden freigegebene Schlüssel
verwendet, die über das Verwaltungsportal verfügbar sind. Verwenden Sie die Schlüssel im Code oder
in Konfigurationsdateien, wenn Sie auf den Cache zugreifen. Verwahren sie die Schlüssel unbedingt
an einem sicheren Ort, um Offenlegung von Informationen zu vermeiden.

![][17]


In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Gewusst wie: Programmgesteuertes Konfigurieren eines Cacheclients für Azure
    Cachedienst](http://msdn.microsoft.com/de-de/library/windowsazure/gg618003.aspx)
-   [Gewusst wie: Konfigurieren eines Cacheclients mit der Anwendungskonfigurationsdatei
    für Azure-Caches](http://msdn.microsoft.com/de-de/library/windowsazure/gg278346.aspx)
-   [Azure Service Bus- und Cachebeispiele](http://msdn.microsoft.com/de-de/library/ee706741.aspx) (Abschnitt
    Cachebeispiele)

##Azure Marketplace-Identität und Zugriffsszenarien

Bei jedem Zugriff auf ein Azure Marketplace-Dataset, gleichgültig ob kostenlos oder
gebührenpflichtig, muss der Benutzer authentifiziert werden, bevor Zugriff gewährt wird. Wenn Sie
eine Anwendung erstellen, muss der Authentifizierungsprozess im Code
enthalten sein. Beachten Sie die folgenden häufig auftretenden Szenarien:

###Zugriff auf eigenen DataSet

In diesem Szenario erstellen Sie eine Anwendung, die Datasets
in Ihrem Marketplace-Abonnement verwendet. Sie sind der Benutzer der Anwendung.
Die Anwendung kann unter Azure, lokal oder auf Marketplace bereitgestellt werden.

Verwenden Sie den gemeinsam verwendeten Schlüssel, der über das Marketplace-Abonnement
erhältlich ist. Sie erhalten den gemeinsam verwendeten Schlüssel über das Marketplace-Portal.

![][18]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Implementieren von HTTP-Standardauthentifizierung in Ihrer Marketplace-App](http://msdn.microsoft.com/de-de/library/gg193417.aspx)

###Zugriff von Benutzern auf meine DataSets

In diesem Szenario erstellen Sie eine Anwendung, die Benutzern den
Zugriff auf Ihr Dataset erlaubt. Die Anwendung kann auf Azure,
lokal oder im Marketplace bereitgestellt werden.

Verwenden Sie zum Lösen dieses Szenarios die OAuth-Delegierung. Die Benutzer werden aufgefordert, Ihre
Live ID-/Microsoft Account-Anmeldeinformationen einzugeben, und sie
werden dann durch den Zustimmungsprozess geleitet.

![][19]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [OAuth Web Client Example (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=219162)
-   [OAuth Rich Client Example (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=219163)

###Anwendungszugriff auf das Marketplace-API

In diesem Szenario erstellen Sie eine Anwendung, die auf die
Marketplace-API zugreift. Die Marketplace-API erfordert eine Authentifizierungm
damit Aufrufe erfolgreich ausgeführt werden können. Die Anwendung wird im
Azure Marketplace bereitgestellt.

![][20]

Im Marketing-Publishing-Kit finden Sie etaillierte Informationen über das
Implementieren der Authentifizierung.

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Anwendungs-Publishing-Kit herunterladen](http://go.microsoft.com/fwlink/?LinkId=221323)
-   [Introduction to Azure Marketplace for Applications (in englischer Sprache)](https://datamarket.azure.com/)

##Sicherheitsknöpfe

In diesem Abschnitt werden Sicherheitselemente für Windows Identity Foundation und
Azure AD Access Control beschrieben. Nutzen Sie den Abschnitt als einfach Sicherheitsprüfliste
für diese Technologien, wenn Sie Ihre Anwendung entwerfen und bereitstellen.

###Windows Identity Foundation

Nachfolgend werden wichtige Sicherheitselemente von WIF aufgeführt. Die folgenden Informationen sind ein
Auszug aus  [WIF Design Considerations](http://msdn.microsoft.com/de-de/library/ee517298.aspx) und [Windows Identity Foundation
(WIF) Sicherheit für ASP.NET-Webanwendungen - Bedrohungen und Gegenmaßnahmen](http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx)
.

-   **IssuerNameRegistry**. Nennt vertrauenswürdige Sicherheitstokendienste
    (STS). Stellen Sie sicher, dass nur vertrauenswürdige STS aufgelistet werden.
-   **cookieHandler requireSsl="true"**. Gibt an, ob Sitzungs-Cookies
    unter Verwendung des SSL-Protokolls übertragen werden.
-   **wsFederation's requireHttps="true"**. Gibt an, ob die
    Kommunikation des Verbundprotokolls mit dem Identitätsanbieter über
    das SSL-Protokoll erfolgt.
-   **tokenReplayDetection enabled="true"**. Gibt an, ob die Funktion zur
    Erkennung der Tokenausgabe aktiviert wird. Beachten Sie, dass diese Funktion
    zu Serveraffinität führt, das sie lokale Kopien der verwendeten Token verwaltet.
-   **audienceUris**. Legt das Zielpublikum für das Token fest. Wenn Ihre
    Anwendung ein Token empfängt, der nicht für Ihre Anwendung vorgesehen war, dann
    wird er von WIF zurückgewiesen.
-   **requestValidation** und **httpRuntime requestValidationType**.
    Aktiviert/deaktiviert die ASP.NET-Validierungsfunktion. Hinweise hierzu finden Sie
    in [Windows Identity Foundation (WIF): A Potentially
    Dangerous Request.Form Value Was Detected from the Client](http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx)

###Azure AD Access Control

Beachten Sie die folgenden Sicherheitselemente bei der Bereitstellung von Azure AD Access Control
. Die folgenden Informationen sind ein Auszug aus [ACS Security
Guidelines](http://msdn.microsoft.com/de-de/library/gg185962.aspx) und [Certificates and Keys Management Guidelines](http://msdn.microsoft.com/de-de/library/hh204521.aspx).

-   **Ablauf von STS-Token**. Legen Sie im Azure AD Access Control
    -Verwaltungsportal strikte Richtlinien für den Tokenablauf fest.
-   **Datenvalidierung bei Verwendung der Funktion "Fehler-URL"**. Azure
    AD Access Control Fehler-URL-Feature erfordert anonymen Zugriff auf die
    Seite der Anwendung, an die Fehlermeldungen gesendet werden. Unterstellen Sie, dass alle Daten, die auf
    diese Seite gelangen, von nicht vertrauenswürdigen Quellen stammen und gefährlich sind.
-   **Verschlüsseln von Token für hoch sensible Szenarien**. Zur Minderung
    des Risikos der Offenlegung von Informationen, die im Token verfügbar sind,
    ziehen Sie die Verschlüsselung der Token in Betracht.
-   **Verschlüsseln von Cookies mit RSA beim Bereitstellen in Azure**.
    WIF verschlüsselt Cookies standardmäßig mit DPAPI. Dies führt zu
    Serveraffinität und kann Ausnahmen auslösen, wenn die Bereitstellung in Webfarmen und
    Azure-Umgebungen erfolgt. Verwenden Sie stattdessen RSA in Webfarm- und 
    Azure-Szenarien.
-   **Tokensignaturzertifikate**. Erneuern Sie Tokensignaturzertifikaten
    regelmäßig, um Denial-of-Service zu vermeiden. Azure AD Access
    Control signiert alle ausgestellten Sicherheitstoken. X.509-Zertifikate werden
    zum Signieren verwendet, wenn Sie eine Anwendung erstellen, die SAML-Token nutzt,
    die von ACS ausgestellt wurden. Wenn Signaturzertifikate ablaufen, werden
    Fehlermeldungen angezeigt, wenn Sie versuchen, ein Token anzufordern.
-   **Tokensignaturschlüssel**. Erneuern Sie Token-Signaturschlüssel regelmäßig, um
    Denial-of-Service zu vermeiden. Azure AD Access Control signiert alle
    ausgestellten Sicherheitstoken. Symmetrische 256-Bit-Signaturschlüssel werden verwendet,
    wenn Sie eine Anwendung erstellen, der SWT-Token verwende, die von
    ACS ausgestellt wurden. Nach dem Ablauf von Signaturschlüsseln erhalten Sie Fehlermeldungen, wenn Sie versuchen,
    ein Token anzufordern.
-   **Tokenverschlüsselungszertifikate**. Erneuern Sie die Tokenverschlüsselungszertifikate
    regelmäßig, um Denial-of-Service zu vermeiden. Tokenverschlüsselung
    ist erforderlich, wenn eine abhängige Anwendung ein Webddienst
    ist, der Proof-of-possession-Token über as WS-Trust-Protokoll verwendet,
    in anderen Fällen ist die token-Verschlüsselung optional. Nach dem Ablauf von
    Verschlüsselungszertifikaten erhalten Sie Fehlermeldungen, wenn Sie versuchen, ein
    Token anzufordern.
-   **Tokenentschlüsselungszertifikate**. Erneuern Sie Tokenentschlüsselungszertifikate
    regelmäßig, um Denial-of-Service zu vermeiden. Azure
    AD Access Control kann veschlüsselte Token von WS-Federation
    Identitätsanbietern annehmen (z. B. AD FS 2.0). Ein X.509-Zertifikat,
    das in Azure AD Access Control gehostet wird, wird zur Verschlüsselung verwendet.
    Nach dem Ablauf von Entschlüsselungszertifikaten erhalten Sie Fehlermeldungen, wenn
    Sie versuchen, ein Token anzufordern.
-   **Anmeldeinformationen für die Dienstidentität**. Erneuern Sie die Anmeldeinformationen für die Dienstidentität
    regelmäßig, um Denial-of-Service zu vermeiden. Dienstidentitäten verwenden
    Anmeldeinformationen, die global für Ihren Azure AD
    Access Control-Namespace konfiguriert sind und es Anwendungen oder Clients ermöglichen,
    sich direkt bei Azure AD Access Control zu authentifizieren und
    einen Token zu erhalten. Es gibt drei Arten von Anmeldeinformationen, die der Azure
    AD Access Control-Dienstidentität zugeordnet werden können: Symmetrischer
    Schlüssel, Kennwort und X.509-Zertifikat. Es werden Ausnahmeen ausgelöst,
    wenn die Anmeldeinformationen abgelaufen sind.
-   **Anmeldeinformationen für das Azure AD Access Control-Verwaltungsdienstkonto
    **. Erneuern Sie die Anmeldeinformationen für den Verwaltungsdienst regelmäßig, um
    Denial-of-Service zu vermeiden. Der Azure AD Access Control-
    Verwaltungsdienst ist eine zentrale Komponente, die es Ihnen ermöglicht,
    programmgesteuert die Einstellungen für Ihren 
    Azure AD Access Control-Namespace zu verwalten und zu konfigurieren. Es gibt drei Arten von Anmeldeinformationen, die
    dem Verwaltungsdienstkonto zugeordnet werden können. Dabei handelt es sich um
    symmetrische Schlüssel, Kennwort und X.509-Zertifikat. Sie erhalten
    Ausnahmen, sobald die Anmeldeinformationen abgelaufen sind.
-   **Signatur- und Verschlüsselungszertifikate für WS-Federation-Identitätsanbieter
    **. Überprüfen Sie die Gültigkeit des Zertifikats WS-Federation-Identitätsanbieters,
    um Denial-of-Service zu vermeiden. WS-Federation-
    Identitätsanbieterzertifikate sind über die Metadaten verfügbar.
    Beim Konfigurieren eines WS-Federation-Identitätsanbieters, wie AD FS, wird das
    WS-Federation-Signaturzertifikat über die
    WS-Federation-Metadaten konfiguriert, die als URL oder als Datei vorliegen. Nach der
    Konfiguration des WS-Federation-Identitätsanbieters verwenden Sie den Azure AD
    Access Control-Verwaltungsdienst, um die Gültigkeit der Zertifikate
    zu prüfen. Nach Ablauf der Zertifikate werden Ausnahmen
    ausgelöst.

##Freigegebenes Hosting über Azure-Websites

Alle Szenarien und Lösungen, die in diesem Thema beschrieben wurden, gelten, wenn die
Anwendung in Azure-Websites gehostet wird.

##Azure Virtual Machines

Alle Szenarien und Lösungen, die in diesem Thema beschrieben wurden, gelten, wenn die
Anwendung wird auf Azure Virtual Machines gehostet wird.

##Ressourcen

-   [Identity Developer Training Kit (nur in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=214555)
-   [MSDN-hosted Identity Developer Training Course (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=214561)
-   [A Guide to Claims-Based Identity and Access Control (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=214562)
-   [Zugriffssteuerungsdienst (ACS)](http://msdn.microsoft.com/de-de/library/windowsazure/gg429786.aspx)
-   [ACS - Vorgehensweisen](http://msdn.microsoft.com/de-de/library/windowsazure/gg185939.aspx)
-   [Secure Azure Web Role ASP.NET Web Application Using Access Control Service v2.0 (in englischer Sprache)](http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx)
-   [Azure AD Access Control Service (ACS) Academy Videos (in englischer Sprache)](http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx)
-   [Microsoft Security Development Lifecycle (in englischer Sprache)](http://www.microsoft.com/security/sdl/default.aspx)
-   [SDL Threat Modeling Tool 3.1.8 (in englischer Sprache)](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955)
-   [Security and Privacy Blogs (in englischer Sprache)](http://www.microsoft.com/about/twc/en/us/blogs.aspx)
-   [Security Response Center (in englischer Sprache)](http://www.microsoft.com/security/msrc/default.aspx)
-   [Security Intelligence Report (in englischer Sprache)](http://www.microsoft.com/security/sir/)
-   [Security Development Lifecycle (in englischer Sprache)](http://www.microsoft.com/security/sdl/default.aspx)
-   [Security Developer Center (MSDN)](http://msdn.microsoft.com/security/)


[01]:./media/SecurityRX/01_SecuringTheApplication.gif
[02]:./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
[03]:./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
[04]:./media/SecurityRX/04_WCF(SOAP)Service.gif
[05]:./media/SecurityRX/05_AzureADAccessControl.gif
[06]:./media/SecurityRX/06_RESTService.gif
[07]:./media/SecurityRX/07_WIFisOptional.gif
[08]:./media/SecurityRX/08_ASPNETWebApptoREST.gif
[09]:./media/SecurityRX/09_RBAC.gif
[10]:./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
[11]:./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
[12]:./media/SecurityRX/12_CustomRoleManager.gif
[13]:./media/SecurityRX/13_ClaimsAuthorizationManager.gif
[14]:./media/SecurityRX/14_WindowsAzurestorage.gif
[15]:./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
[16]:./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
[17]:./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
[18]:./media/SecurityRX/18_IAccessMyDataset.gif
[19]:./media/SecurityRX/19_UsersAccessMyDatasets.gif
[20]:./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif

[Web SSO Design (in englischer Sprache)]: http://technet.microsoft.com/de-de/library/dd807033(WS.10).aspx
[Federated Web SSO Design (in englischer Sprache)]: http://technet.microsoft.com/de-de/library/dd807050(WS.10).aspx
