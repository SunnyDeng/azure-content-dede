# Azure-Sicherheitsleitfaden

## Zusammenfassung

Beim Entwickeln von Anwendungen für Azure sind
Identität und Zugriff die vorrangigen Sicherheitsaspekte, die es zu
beachten gilt. In diesem Thema werden die wichtigsten Sicherheitserwägungen in Bezug auf Identität und
Zugriff in der Cloud erläutert. Außerdem wird beschrieben, wie Sie Ihre Cloudanwendungen am besten schützen können.

## Übersicht

Die Sicherheit einer Anwendung ist eine Funktion auf ihrer Oberfläche. Je mehr Oberfläche
die Anwendung nach außen hin zur Verfügung stellt, desto größer die Sicherheitsbedenken. Eine
Anwendung, über die einen unbeaufsichtigter Batchverarbeitungsprozess
ausgeführt wird, bietet aus einer Sicherheitsperspektive heraus beispielsweise weniger Oberfläche als eine öffentlich zugängliche Website.

Nach einem Umzug in die Cloud müssen Sie sich keine Sorgen mehr
um Infrastruktur und Netzwerke machen, da diese in Rechenzentren mit
erstklassigen Sicherheitsmethoden, -tools und -technologien verwaltet werden. Andererseits
vergrößert die Cloud naturgemäß die Oberfläche der Anwendung,
und dies kann von Hackern unter Umständen ausgenutzt werden. Der
Grund hierfür ist, dass viele Cloudtechnologien und -dienste im Gegensatz
zu In-Memory-Komponenten als Endpunkte exponiert sind. Azure-Speicher, Service Bus, die
SQL-Datenbank (vormals SQL Azure) und viele weitere Dienste sind über
ihre jeweiligen Endpunkte im Netzwerk zugänglich.

In Cloudanwendungen ruht mehr Verantwortung auf den Schultern der
Anwendungsentwickler. Um Angreifer fernzuhalten, müssen diese
Cloudanwendungen mit hohen Sicherheitsstandards entwickeln.
Sehen Sie sich das folgende Diagramm an (aus [Azure Security
Notes (PDF)][Azure Security
Notes (PDF)] von J.D. Meier, in englischer Sprache): Beachten Sie, wie der Cloudanbieter – in unserem Fall Azure –
den Infrastrukturteil übernimmt. Das bedeutet mehr
sicherheitsbezogene Aufgaben für die Anwendungsentwickler:

![Sichern der Anwendung][Sichern der Anwendung]

Die gute Nachricht ist, dass sämtliche Ihnen schon bekannten Methoden,
Prinzipien und Techniken der Sicherheitsentwicklung
auch beim Entwickeln von Cloudanwendungen noch Gültigkeit haben. Beachten Sie die folgenden zu berücksichtigenden
Schlüsselbereiche:

-   Alle Eingaben werden in Hinsicht auf Typ, Länge, Bereich und
    Zeichenfolgenmuster überprüft, um Angriffe durch Einschleusung
    zu vermeiden. Außerdem werden alle von der Anwendung zurückgegebenen Daten ordnungsgemäß gesichert.
-   Sensible Daten sollten sowohl im gespeicherten Zustand als auch
    bei der Übermittlung geschützt werden, um Bedrohungen durch das Offenlegen von Informationen und das Manipulieren von Daten zu verringern.
-   Überwachung und Protokollierung müssen ordnungsgemäß implementiert
    sein, um Bedrohungen durch Nichtabstreitbarkeit zu verringern.
-   Authentifizierung und Autorisierung sollten mithilfe auf der Plattform
    verfügbarer bewährter Mechanismen implementiert werden, um
    Bedrohungen durch Identitätsfälschungen und Berechtigungserhöhungen zu verhindern.

Eine vollständige Liste von Bedrohungen, Angriffen,
Schwachstellen und Gegenmaßnahmen finden Sie in "patterns & practices" unter [Cheat Sheet: Web
Application Security Frame][Cheat Sheet: Web
Application Security Frame] (in englischer Sprache) und [Security Guidance for Applications Index][Security Guidance for Applications Index] (in englischer Sprache).

In der Cloud unterscheiden sich die Mechanismen für Authentifizierung
und Zugriffsteuerung stark von solchen, die für lokale Anwendungen zur Verfügung stehen. Für
Cloudanwendungen werden wesentlich mehr Authentifizierungs- und
Zugriffsoptionen angeboten. Dies kann zu Verwirrung und in der
Folge zu mangelhaften Implementierungen führen. Noch mehr Verwirrung entsteht, wenn definiert werden soll,
was eine Cloudanwendung ist. Die Anwendung könnte beispielsweise in der Cloud bereitgestellt
werden, während ihr Authentifizierungsmechanismus über Active Directory erfolgt. Andererseits
könnte die Anwendung lokal bereitgestellt werden, während
die Authentifizierungsmechanismen in der Cloud liegen (z. B.
über Azure Active Directory Access Control (vormals
Access Control Service oder ACS)).

## Bedrohungen, Schwachstellen und Angriffe

Eine Bedrohung besteht in einem potenziell schlechten Ergebnis, das
Sie vermeiden möchten, z. B. die Offenlegung sensibler Daten oder
der Ausfall eines Diensts. Nach gängiger Praxis werden Bedrohungen mit dem Akronym "STRIDE" klassifiziert:

-   **S**poofing oder Identitätsdiebstahl
-   **T**ampering with data (Datenfälschung/-manipulation)
-   **R**epudiation of actions (Nichtanerkennung von Aktionen)
-   **I**nformation disclosure (Offenlegung von Daten)
-   **D**enial of Service
-   **E**levation of privileges (Rechteerweiterung)

Schwachstellen sind Fehler, die Entwickler versehentlich im Code
hinterlassen und die eine Anwendung angreifbar machen. Werden Daten
beispielsweise als unverschlüsselter Text gesendet, erzeugt dies die
Bedrohung einer Offenlegung von Daten, da der Datenverkehr ausgespäht werden könnte.

Angriffe sind das Ausnutzen dieser Schwachstellen, um einer
Anwendung zu schaden. Ein siteübergreifendes Scripting, oder XSS, ist
beispielsweise ein Angriff, der ungesicherte Ausgaben ausnutzt. Ein weiteres Beispiel
ist das Ausspähen einer Übertragung zum Abfangen unverschlüsselter Anmeldedaten. Diese
 Angriffe können dazu führen, dass die Bedrohung einer Identitätsfälschung (Spoofing) umgesetzt wird. Einfach
ausgedrückt, können Sie Bedrohungen, Schwachstellen und Angriffe
als schlechte Dinge betrachten. Die folgenden Diagramme liefern
Ihnen einen Überblick über diese schlechten Dinge im Kontext
einer in Azure bereitgestellten Webanwendung (aus [Azure Security Notes (PDF)][Azure Security
Notes (PDF)] von J.D. Meier, in englischer Sprache):

![Bedrohungen, Schwachstellen und Angriffe][Bedrohungen, Schwachstellen und Angriffe]

Sie, als ein Entwickler, können diese Schwachstellen steuern. Je weniger
Schwachstellen Sie hinterlassen, desto weniger Optionen bleiben
einem Angreifer.

Identitäts- und zugriffsbezogene Schwachstellen öffnen allen im
STRIDE-Modell genannten Bedrohungen Tür und Tor. Ein unsachgemäß implementierter
Authentifizierungsmechanismus kann beispielsweise zu einer Identitätsfälschung
und in der Folge zu einer Offenlegung von Informationen, zu
Datenmanipulationen, zu Vorgängen unter unbefugt erweiterten Rechten oder sogar zum Komplettausfall des Diensts führen. Beachten Sie die
folgenden Fragen. Sie können Ihnen Hinweise auf potenzielle
Schwachstellen in der Identitäts- und Zugriffsimplementierung Ihrer Cloud geben:

-   Versenden Sie Anmeldeinformationen unverschlüsselt an
    Ihre Azure-Dienste?
-   Speichern Sie Anmeldeinformationen für Azure-Dienste unverschlüsselt?
-   Folgen Ihre Anmeldeinformationen für Azure-Dienste den
    Richtlinien für starke Kennwörter?
-   Verlassen Sie sich bei der Überprüfung der
    Anmeldeinformationen auf Azure, oder verwenden Sie benutzerdefinierte Überprüfungsmechanismen?
-   Beschränken Sie die Authentifizierungssitzungen oder die
    Tokengültigkeitsdauer der Azure-Dienste auf ein angemessenes Zeitfenster?
-   Überprüfen Sie Berechtigungen für jeden einzelnen Azure-
    Einstiegspunkt Ihrer verteilten Cloudanwendung?
-   Ist ein Fehlschlagen Ihres Autorisierungsmechanismus sicher, ohne
    dass dabei sensible Daten offengelegt werden oder unbeschränkter Zugriff ermöglicht wird?

## Gegenmaßnahmen

Die beste Gegenmaßnahme gegen einen Angriff besteht darin, die von
der Plattform angebotenen Identitäts- und Zugriffsmechanismen
zu verwenden, anstatt Ihre eigenen zu implementieren. Berücksichtigen Sie die folgenden bedeutenden Identitäts- und Zugriffstechnologien:

**Windows Identity Foundation (WIF).** WIF ist eine .NET-Laufzeitbibliothek,
die in .NET Framework 4.5 enthalten ist (sie steht außerdem
als separater Download für .NET 3.5/4.0 zur Verfügung). WIF leistet die Schwerarbeit für
Behandlungsprotokolle wie WS-Verbund und WS-Trust und für
Tokenbehandlung wie SAML (Security Assertion Markup Language ).
Daher müssen Sie keinen allzu komplexen sicherheitsbezogenen Code in Ihre Anwendung schreiben. In den folgenden
Ressourcen finden Sie detaillierte Informationen über WIF:

-   [Beispiele für Windows Identity Foundation 4.5][Beispiele für Windows Identity Foundation 4.5] in der MSDN Code Gallery.
-   [Windows Identity Foundation 4.5-Tools für Visual Studio 11 Beta][Windows Identity Foundation 4.5-Tools für Visual Studio 11 Beta]
    in der MSDN Code Gallery.
-   [Windows Identity Foundation-Laufzeit (.Net 3.5/4.0)][Windows Identity Foundation-Laufzeit (.Net 3.5/4.0)] auf MSDN (in englischer Sprache).
-   [Beispiele für Windows Identity Foundation 3.5/4.0 und Vorlagen für
    Visual Studio 2008/2010][Beispiele für Windows Identity Foundation 3.5/4.0 und Vorlagen für
    Visual Studio 2008/2010] auf MSDN.

**Azure AD Access Control (früher ACS)**.
Azure AD Access Control ist ein Cloud-Dienst, der den Sicherheitstokendienst
(STS) bereitstellt und einen Verband mit verschiedenen Identitätsanbietern
, firmeneigenem Active Directory oder
Internetidentitätsanbietern wie Windows Live ID/Microsoft-Konto, Facebook, Google, Yahoo!
und OpenID 2.0 ermöglicht. In den folgenden Ressourcen finden Sie
ausführliche Informationen zu Azure AD Access Control:

-   [Access Control Service 2.0][Access Control Service 2.0]
-   [Szenarien und Lösungen mit ACS][Szenarien und Lösungen mit ACS]
-   [ACS – Vorgehensweisen][ACS – Vorgehensweisen]
-   [A Guide to Claims-Based Identity and Access Control (in englischer Sprache)][A Guide to Claims-Based Identity and Access Control (in englischer Sprache)]
-   [Identity Developer Training Kit (nur in englischer Sprache)][Identity Developer Training Kit (nur in englischer Sprache)]
-   [MSDN-hosted Identity Developer Training Course (in englischer Sprache)][MSDN-hosted Identity Developer Training Course (in englischer Sprache)]

**Active Directory-Verbunddienste (AD FS).**Active
Directory-Verbunddienste (AD FS) 2.0 unterstützt Ansprüche unterstützende
Identitätslösungen mit Windows Server- und Active Directory-
Technologie. AD FS 2.0 unterstützt die WS-Trust-, WS-Verbund- und SAML-
Protokolle. In den folgenden Ressourcen finden Sie detaillierte Informationen
über AD FS:

-   [AD FS 2.0 Content Map (in englischer Sprache)][AD FS 2.0 Content Map (in englischer Sprache)]
-   [Web SSO Design (in englischer Sprache)][Web SSO Design (in englischer Sprache)]
-   [Federated Web SSO Design (in englischer Sprache)][Federated Web SSO Design (in englischer Sprache)]

**Azure Shared Access Signatures.** Shared Access Signatures
ermöglicht Ihnen die Feinabstimmung des Zugriffs auf eine Blob- oder Containerressource. In
den folgenden Ressourcen finden Sie detaillierte Informationen über
Shared Access Signatures:

-   [Managing Access to Blobs and Containers (in englischer Sprache)][Managing Access to Blobs and Containers (in englischer Sprache)]
-   [New Storage Feature: Shared Access Signatures (in englischer Sprache)][New Storage Feature: Shared Access Signatures (in englischer Sprache)]
-   [Shared Access Signatures Are Easy These Days (in englischer Sprache)][Shared Access Signatures Are Easy These Days (in englischer Sprache)]

## Szenarienübersicht

In diesem Abschnitt werden kurz die wichtigsten in diesem Thema
behandelten Szenarien umrissen. Es verschafft Ihnen eine Übersicht,
wenn Sie die richtige Identitätslösung für Ihre Anwendung ermitteln möchten.

-   **ASP.NET-Webformularanwendung mit Verbundauthentifizierung.** In
    diesem Szenario steuern Sie den Zugriff auf Ihre ASP.NET-
    Webformularanwendung entweder über eine Internetidentität
    wie Live ID/Microsoft-Konto oder über eine firmeninterne Identität, die jeweils in Windows Server Active Directory verwaltet wird.
-   **WCF (SOAP)-Dienst mit Verbundauthentifizierung.** In diesem
    Szenario steuern Sie den Zugriff auf Ihren WCF (SOAP)-Dienst über
    Dienstidentitäten, die von Azure AD Access Control verwaltet werden.
-   **WCF (SOAP)-Dienst mit Verbundauthentifizierung, Identitäten in
    Active Directory.** In diesem Szenario steuern Sie den Zugriff auf Ihren
    WCF (SOAP)-Webdienst über Identitäten, die vom firmeninternen
    Windows Server Active Directory verwaltet werden.
-   **WCF (REST)-Dienst mit Verbundauthentifizierung.** In diesem
    Szenario steuern Sie den Zugriff auf Ihren WCF (REST)-Dienst über
    Dienstidentitäten, die über Azure AD Access Control verwaltet werden.
-   **WCF (REST)-Dienst mit Live ID/Microsoft-Konto, Facebook, Google,
    Yahoo!, Open ID.** In diesem Szenario steuern Sie den
    Zugriff auf Ihren WCF (REST)-Dienst über eine Internetidentität wie
    Live ID/Microsoft-Konto.
-   **ASP.NET-Webanwendung für den REST WCF-Dienst mit freigegebenem SWT-Token.** In
    diesem Szenario geht es um eine verteilte Anwendung
    mit einer Front-End-ASP.NET-Webanwendung und nachgelagertem
    REST-Dienst, bei der der Kontext des Endbenutzers durch physische Ebenen geleitet werden soll.
-   **Autorisierung über rollenbasierte Zugriffssteuerung (RBAC) in Ansprüche
    unterstützenden Anwendungen und Diensten.** In diesem Szenario soll in Ihrer Anwendung
    eine auf Rollen basierende Autorisierungslogik implementiert werden.
-   **Anspruchsbasierte Autorisierung in Ansprüche unterstützenden
    Anwendungen und Diensten.** In diesem Szenario soll in Ihrer Anwendung eine auf
    komplexen Autorisierungsregeln basierende Autorisierungslogik implementiert werden.
-   **Azure Storage-Dienstidentität und -Zugriffsszenarien.** In
    diesem Szenario müssen Sie auf sichere Weise Zugriff auf
    Azure Storage-Blobs und -Container freigeben.
-   **Azure SQL-Datenbankidentität und -Zugriffsszenarien.** Die
    SQL-Datenbank unterstützt nur SQL Server-Authentifizierung. Windows-
    Authentifizierung (integrierte Sicherheit) wird nicht unterstützt. Die Benutzer
    müssen bei jeder Verbindungsherstellung mit der SQL-Datenbank ihre
    Anmeldeinformationen (Benutzername und Kennwort) angeben.
-   **Azure Service Bus-Identität und -Zugriffsszenarien.** In diesem
    Szenario müssen Sie auf sichere Weise auf Azure Service Bus-Warteschlangen zugreifen.
-   **In-Memory-Cacheidentität und -Zugriffszenarien.** In diesem Szenario
    müssen Sie auf sichere Weise auf Daten zugreifen, die vom In-Memory-Cache verwaltet werden.
-   **Azure Marketplace-Identität und -Zugriffsszenarien.** In diesem
    Szenario müssen Sie auf sichere Weise auf
    Azure Marketplace-DataSets zugreifen.

## Azure-Identität und Zugriffsszenarien

In diesem Abschnitt werden gängige Identitäts- und Zugriffsszenarien für
verschiedene Anwendungsarchitekturen umrissen. Über die Szenarienübersicht können
Sie sich eine schnelle Orientierung verschaffen.

### ASP.NET-Webformularanwendung mit Verbundauthentifizierung

In diesem Anwendungsarchitekturszenario kann Ihre Webanwendung
in Azure oder lokal bereitgestellt werden. Die Anwendung erfordert, dass
sich der Benutzer entweder über firmeninternes Active Directory oder
über Internetidentitätsanbieter authentifiziert.

Verwenden Sie Azure AD Access Control und Windows Identity
Foundation, um dieses Szenario umzusetzen.

![Azure Active Directory Access Control][Azure Active Directory Access Control]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Erstellen einer ersten Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von ACS][Gewusst wie: Erstellen einer ersten Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von ACS]
-   [Gewusst wie: Hosten von Anmeldeseiten in einer ASP-NET-Webanwendung][Gewusst wie: Hosten von Anmeldeseiten in einer ASP-NET-Webanwendung]
-   [Gewusst wie: Implementieren von Anspruchsautorisierung in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS][Gewusst wie: Implementieren von Anspruchsautorisierung in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS]
-   [Gewusst wie: Implementieren von rollenbasierter Zugriffssteuerung (Role Based
    Access Control, RBAC) in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS][Gewusst wie: Implementieren von rollenbasierter Zugriffssteuerung (Role Based
    Access Control, RBAC) in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS]
-   [Gewusst wie: Konfigurieren der Vertrauensstellung zwischen ACS und ASP.NET-
    Webanwendungen mithilfe von X.509-Zertifikaten][Gewusst wie: Konfigurieren der Vertrauensstellung zwischen ACS und ASP.NET-
    Webanwendungen mithilfe von X.509-Zertifikaten]
-   [Codebeispiel: Einfache ASP.NET-Formulare][Codebeispiel: Einfache ASP.NET-Formulare]

### WCF (SOAP)-Dienst mit Dienstidentität

In diesem Anwendungsarchitekturszenario kann Ihr WCF (SOAP)-Dienst in
Azure oder lokal bereitgestellt werden. Der Zugriff auf diesen Dienst
erfolgt als nachgelagerter Dienst über eine Webanwendung oder sogar über
einen anderen Webdienst. Sie müssen den Zugriff auf diesen Dienst mithilfe einer
anwendungsspezifischen Identität steuern. Denken Sie dazu an die Art des Anwendungspoolkontos, das
Sie in IIS verwendet haben. Die Technologie ist zwar unterschiedlich,
die Herangehensweise ist jedoch ähnlich, da auf den
Dienst mithilfe eines Anwendungsbereichkontos anstelle eines Benutzerkontos zugegriffen wird.

Verwenden Sie die Funktion "Dienstidentität" in Azure AD
Access Control. Dies ähnelt dem IIS-Anwendungspoolkonto, das
Sie bei der Bereitstellung Ihrer Anwendungen in Windows Server und IIS verwendet haben. Konfigurieren Sie
Azure AD Access Control so, dass SAML-Token ausgestellt werden,
die von WIF im WCF (SOAP)-Dienst behandelt werden.

![WCF (SOAP)-Dienst][WCF (SOAP)-Dienst]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Hinzufügen von Dienstidentitäten mit einem X.509-Zertifikat,
    Kennwort oder symmetrischen Schlüssel][Gewusst wie: Hinzufügen von Dienstidentitäten mit einem X.509-Zertifikat,
    Kennwort oder symmetrischen Schlüssel]
-   [Gewusst wie: Authentifizierung mit einem Clientzertifikat bei einem durch
    ACS geschützten WCF-Dienst][Gewusst wie: Authentifizierung mit einem Clientzertifikat bei einem durch
    ACS geschützten WCF-Dienst]
-   [Gewusst wie: Authentifizierung mit einem Benutzernamen und einem Kennwort
    bei einem durch ACS geschützten WCF-Dienst][Gewusst wie: Authentifizierung mit einem Benutzernamen und einem Kennwort
    bei einem durch ACS geschützten WCF-Dienst]
-   [Codebeispiel: WCF-Zertifikatauthentifizierung][Codebeispiel: WCF-Zertifikatauthentifizierung]
-   [Codebeispiel: WCF-Benutzernamenauthentifizierung][Codebeispiel: WCF-Benutzernamenauthentifizierung]

### WCF (SOAP)-Dienst mit Verbundauthentifizierung, Identitäten in Active Directory

In diesem Anwendungsarchitekturszenario kann Ihr WCF (SOAP)-Dienst in
Azure oder lokal bereitgestellt werden. Sie müssen den Zugriff auf
diesen Dienst über eine Identität steuern, die von einem firmeneigenen Windows
Server Active Directory (AD) verwaltet wird.

Verwenden Sie Azure AD Access Control in einer Konfiguration
für einen Verbund mit Windows Server AD FS. In diesem Fall müssen Sie die Dienstidentität nicht
mit Azure AD Access Control konfigurieren. Der Agent, der auf den
WCF (SOAP)-Dienst zugreifen muss, übermittelt Anmeldedaten an AD FS.
Bei einer erfolgreichen Authentifizierung stellt AD FS anschließend das Token aus. Das Token
wird anschließend an Azure AD Access Control übermittelt und zurück
an den Agenten übergeben. Der Agent verwendet das Token, um die Anforderung an den WCF
(SOAP)-Dienst zu übermitteln.

![WCF (SOAP)-Dienst mit AD][WCF (SOAP)-Dienst mit AD]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Hinzufügen von Dienstidentitäten mit einem X.509-Zertifikat,
    Kennwort oder symmetrischen Schlüssel][Gewusst wie: Hinzufügen von Dienstidentitäten mit einem X.509-Zertifikat,
    Kennwort oder symmetrischen Schlüssel]
-   [Gewusst wie: Konfigurieren von AD FS 2.0 als Identitätsanbieter][Gewusst wie: Konfigurieren von AD FS 2.0 als Identitätsanbieter]
-   [Gewusst wie: Verwenden des Verwaltungsdiensts zum Konfigurieren von
    AD FS 2.0 als Unternehmensidentitätsanbieter][Gewusst wie: Verwenden des Verwaltungsdiensts zum Konfigurieren von
    AD FS 2.0 als Unternehmensidentitätsanbieter]
-   [Codebeispiel: WCF-Verbundauthentifizierung mit AD FS 2.0][Codebeispiel: WCF-Verbundauthentifizierung mit AD FS 2.0]

### WCF (REST)-Dienst mit Dienstidentitäten

In diesem Szenario kann Ihr WCF (REST)-Dienst in Azure
oder vor Ort bereitgestellt werden. Der Zugriff auf diesen Dienst erfolgt als nachgelagerter
Dienst über eine Webanwendung oder über einen anderen Webdienst. Sie müssen den Zugriff auf
diesen Dienst über eine anwendungsspezifische Identität steuern.
Denken Sie dazu an die Art des Anwendungspoolkontos, das Sie in IIS
verwendet haben. Die Technologie ist zwar unterschiedlich, die
Herangehensweise ist jedoch ähnlich, da auf den Dienst mithilfe eines Anwendungsbereichkontos anstelle eines Benutzerkontos zugegriffen wird.

Verwenden Sie die Funktion "Dienstidentität" in Azure AD Access Control.
Konfigurieren Sie Azure AD Access Control so,
dass SWT (Simple Web Token)-Token ausgestellt werden. Zur Behandlung des SWT-Tokens auf Seiten des REST-Diensts
können Sie entweder einen benutzerdefinierten Tokenhandler
implementieren und ihn in die WIF-Pipeline einfügen
oder das Token ohne Verwendung der WIF-Infrastruktur "manuell" analysieren.

Beachten Sie das folgende Diagramm (WIF ist optional):

![REST-Dienst][REST-Dienst]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Konfigurieren der Vertrauensstellung zwischen ACS und dem WCF-Dienst
    mithilfe symmetrischer Schlüssel][Gewusst wie: Konfigurieren der Vertrauensstellung zwischen ACS und dem WCF-Dienst
    mithilfe symmetrischer Schlüssel]
-   [Gewusst wie: Authenticate to a REST WCF Service Deployed to Azure
    Using ACS (in englischer Sprache)][Gewusst wie: Authenticate to a REST WCF Service Deployed to Azure
    Using ACS (in englischer Sprache)]
-   [Codebeispiel: ASP.NET-Webdienst][Codebeispiel: ASP.NET-Webdienst]
-   [Codebeispiel: Windows Phone 7-Anwendung][Codebeispiel: ASP.NET-Webdienst]
-   [REST WCF With SWT Token Issued By Azure Access Control
    Service (ACS)][REST WCF With SWT Token Issued By Azure Access Control
    Service (ACS)] (in englischer Sprache)

### WCF (REST)-Dienst mit Live ID/Microsoft-Konto, Facebook, Google, Yahoo!, Open ID

In diesem Szenario kann Ihr WCF (REST)-Dienst in Azure oder
vor Ort bereitgestellt werden. Sie müssen den Zugriff auf diesen Dienst mithilfe
einer öffentlichen Internetidentität wie Live ID/Microsoft-Konto oder Facebook steuern.

Verwenden Sie Azure AD Access Control zum Ausgeben von SWT-Token. Zur Behandlung
des SWT-Tokens auf Seiten des REST-Diensts können Sie einen
benutzerdefinierten Tokenhandler implementieren und ihn in die WIF-Pipeline
einfügen oder das Token ohne Verwendung der WIF-Infrastruktur "manuell" analysieren.

Wichtig: Bedenken Sie, dass die Anwendung zum Sammeln von
Benutzeranmeldeinformationen eine Webbrowsersteuerung verwenden
muss, um dieses Szenario zu implementieren. Dadurch ist diese Anwendung ungeeignet für Szenarien,
in denen über eine ASP.NET-Webanwendung auf den REST-Dienst zugegriffen wird. Sie eignet sich nur für
Szenarien, in denen der Zugriff auf den REST-Dienst über die
Clientanwendung des Benutzers erfolgt, z. B. eine Windows Phone 7-App
oder einen funktionsstarken Desktopclient. Der Hauptgrund für die Verwendung der Webbrowsersteuerung
besteht darin, dass Internetidentitäten aus sich heraus keine
Szenarien mit aktivem Profil (Webdienstszenarien) unterstützen. Internetidentitäten unterstützen hauptsächlich
Szenarien mit passivem Profil (Web-Apps), die sich auf Browserumleitungen stützen: Und genau da
erweist sich die Webbrowsersteuerung als praktisch.

Beachten Sie das folgende Diagramm (WIF ist optional und wird daher nicht gezeigt):

![WIF ist optional.][WIF ist optional.]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Authenticate to a REST WCF Service Deployed to Azure Using ACS (in englischer Sprache)][Gewusst wie: Authenticate to a REST WCF Service Deployed to Azure
    Using ACS (in englischer Sprache)]
-   [Gewusst wie: Konfigurieren von Google als Identitätsanbieter][Gewusst wie: Konfigurieren von Google als Identitätsanbieter]
-   [Gewusst wie: Konfigurieren von Facebook als Identitätsanbieter][Gewusst wie: Konfigurieren von Facebook als Identitätsanbieter]
-   [Gewusst wie: Konfigurieren von Yahoo! als Identitätsanbieter][Gewusst wie: Konfigurieren von Yahoo! als Identitätsanbieter]
-   [Codebeispiel: Windows Phone 7-Anwendung][Codebeispiel: ASP.NET-Webdienst]
-   [REST WCF With SWT Token Issued By Azure Access Control
    Service (ACS)][REST WCF With SWT Token Issued By Azure Access Control
    Service (ACS)] (in englischer Sprache)

### ASP.NET-Webanwendung für den REST WCF-Dienst mit freigegebenem SWT-Token

In diesem Szenario geht es um eine verteilte Anwendung mit einer
Front-End-ASP.NET-Webanwendung und nachgelagertem
REST-Dienst, bei der der Kontext des Endbenutzers über physische Ebenen verwaltet werden soll. Dies ist manchmal beim
Implementieren von Autorisierungslogik oder beim identitätsbasierten Anmelden
des Benutzers im nachgelagerten REST-Dienst erforderlich.

Konfigurieren Sie Azure AD Access Control zum Ausgeben von SWT-Token. Das SWT-Token
wird an die Front-End-ASP.NET-Webanwendung ausgestellt und
anschließend für den nachgelagerten REST-Dienst freigegeben. In diesem Fall ist nur eine abhängige
Seite in Azure AD Access Control konfiguriert. Es bestehen jedoch
mehrere Vorbehalte:

-   Da mit WIF kein SWT-Tokenhandler bereitgestellt wird, müssen Sie
    einen benutzerdefinierten Tokenhandler konfigurieren, der mit der ASP.NET-
    Webanwendung verwendet wird. Sie sollten sich auf die Schwerarbeit verlassen, die
    WIF für die Unterstützung des über Browserumleitungen
    funktionierenden WS-Verbundprotokolls leistet, anstatt es selbst zu implementieren.
-   Vergewissern Sie sich beim Implementieren eines benutzerdefinierten
    SWT-Tokenhandlers, dass das Bootstrap-Token adressiert
    wird. Dann ist sichergestellt, dass es beibehalten wird.
    Ansonsten können Sie es nicht freigeben und an den nachgelagerten REST-Dienst senden.
-   Die Verwendung von WIF ist bei einem REST-Dienst nicht unbedingt
    notwendig. Stattdessen können Sie das Token "manuell"
    analysieren, da in diesem Fall keine Umleitungen behandelt werden müssen.

![ASP.NET-Webanwendung][ASP.NET-Webanwendung]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Konfigurieren von Google als Identitätsanbieter][Gewusst wie: Konfigurieren von Google als Identitätsanbieter]
-   [Gewusst wie: Konfigurieren von Facebook als Identitätsanbieter][Gewusst wie: Konfigurieren von Facebook als Identitätsanbieter]
-   [Gewusst wie: Konfigurieren von Yahoo! als Identitätsanbieter][Gewusst wie: Konfigurieren von Yahoo! als Identitätsanbieter]
-   [ASP.NET Web App To REST WCF Service Delegation Using Shared SWT
    Token][ASP.NET Web App To REST WCF Service Delegation Using Shared SWT
    Token] (in englischer Sprache)

### Autorisierung über rollenbasierte Zugriffssteuerung (RBAC) in Ansprüche unterstützenden Anwendungen und Diensten

In diesem Szenario müssen Sie eine auf Benutzerrollen basierende
Autorisierung in Ihre Webanwendung oder Ihren Dienst implementieren: Benutzer mit den erforderlichen
Rollen erhalten Zugriff, während Benutzer ohne die erforderlichen Rollen abgewiesen werden. Einfach
ausgedrückt, muss die Anwendung nur eine einfache Frage beantworten:
"Hat der Benutzer die Rolle X?".

Dieses Szenario kann auf verschiedene Weise gelöst werden. Sie können Azure
AD Access Control, WIF-ClaimsAuthenticationManager,
eine samlSecurityTokenRequirement-Zuordnung oder einen benutzerdefinierten Rollen-Manager verwenden.

WIF kommt in allen Fällen zum Einsatz. WIF unterstützt die IPrincipal.IsInRole("MyRole")-
Methode. In den meisten Fällen liegt der Schlüssel darin sicherzustellen,
dass eine Forderung nach dem Rollentyp in der URL
<http://schemas.microsoft.com/ws/2008/06/identity/claims/role> im
Token vorhanden ist, sodass WIF die Rollenmitgliedschaft bei Aufruf der
IsInRole-Methode erfolgreich prüfen kann.

**Azure AD Access Control**. In dieser Implementierung
wird das Anspruchtransformationsregel-Modul von Azure AD Access Control verwendet. Durch
den Einsatz von Regeln für das Anspruchtransformationsregel-Modul
können Sie jeden eingehenden Anspruch in einen Anspruch auf einen
Regeltyp umwandeln, sodass WIF diesen Rollenanspruch analysieren
kann, wenn das Token auf die Anwendung oder einen Dienst trifft. Auf diese Weise wird sichergestellt, dass der IsInRole-Methodenaufruf erfolgreich ist.

![][0]

**WIF-ClaimsAuthenticationManager**. Verwenden Sie in dieser
Implementierung ClaimsAuthenticationManager als den Erweiterungspunkt von WIF. Mit dieser
Herangehensweise wandeln Sie beliebige eingehende Ansprüche auf
Ebene der Anwendung in einen Regelanspruchstyp um. Die Komplexität der Umwandlung ist lediglich
durch den von Ihnen geschriebenen Code beschränkt.

![][1]

**samlSecurityTokenRequriement-Zuordnung**. Verwenden Sie in dieser
Implementierung die Konfiguration "samlSecurityTokenRequirement" in web.config,
um WIF mitzuteilen, welche Anspruchstypen sich wie Regelanspruchstypen verhalten. Wenn das
Token beispielsweise Träger eines Anspruchs des Gruppentyps ist, können
Sie es dem Rollenanspruchstyp zuordnen. Mit dieser Vorgehensweise sind nur einfache Zuordnungen möglich.

![][2]

**Benutzerdefinierter RoleManager.** In dieser Implementierung implementieren
Sie einen benutzerdefinierten RoleManager. WIF überprüft eingehende Ansprüche beim Implementieren
von benutzerdefinierten RoleManager-Schnittstellenmethoden wie GetAllRoles().

![][3]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Implementieren von rollenbasierter Zugriffssteuerung (Role Based
    Access Control, RBAC) in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS][Gewusst wie: Implementieren von rollenbasierter Zugriffssteuerung (Role Based
    Access Control, RBAC) in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS]
-   [Gewusst wie: Implementieren von Tokentransformationslogik mithilfe von Regeln][Gewusst wie: Implementieren von Tokentransformationslogik mithilfe von Regeln]
-   [Authorization With RoleManager For Claims Aware (WIF) ASP.NET
    Web Applications][Authorization With RoleManager For Claims Aware (WIF) ASP.NET
    Web Applications] (in englischer Sprache)
-   Code Sample: Using Claims In IsInRole in [Windows Identity Foundation SDK
     (in englischer Sprache)][Windows Identity Foundation SDK
     (in englischer Sprache)]

### Anspruchsbasierte Autorisierung in Ansprüche unterstützenden Anwendungen und Diensten

In diesem Szenario müssen Sie komplexe Autorisierungslogik in Ihrer
Webanwendung oder Ihrem Dienst implementieren, und die IsInRole()-
Methode bringt keine zufriedenstellenden Ergebnisse für Ihre Autorisierungsanforderungen. Wenn Ihre
Autorisierungsmethode auf Rollen basiert, ziehen Sie in Erwägung, die im
vorherigen Abschnitt vorgestellte rollenbasierte Zugriffssteuerung zu implementieren.

Verwenden Sie ClaimsAuthorizationManager als den
Erweiterungspunkt für WIF. ClaimsAuthorizationManager ermöglicht externe Aufrufe
zur Zugriffsprüfung, mit denen Ihr Anwendungscode sauberer und
pflegefreundlicher angezeigt wird als über die Zugriffsprüfungen, die im Anwendungscode selbst implementiert sind.

![][4]

In den folgenden Ressourcen finden Sie Hinweise zum Implementieren dieses Szenarios:

-   [Gewusst wie: Implementieren von Tokentransformationslogik mithilfe von Regeln][Gewusst wie: Implementieren von Tokentransformationslogik mithilfe von Regeln]
-   [Gewusst wie: Implementieren von Anspruchsautorisierung in einer Ansprüche
    unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS][Gewusst wie: Implementieren von Anspruchsautorisierung in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS]
-   Code Sample: Claims based Authorization in [Windows Identity
    Foundation SDK][Windows Identity Foundation SDK
     (in englischer Sprache)] (in englischer Sprache)

## Azure Storage-Dienstidentität und Zugriffsszenarien

In diesem Szenario müssen Sie auf sichere Weise Zugriff auf
Azure Storage-Blobs und -Container freigeben.

Verwenden Sie Shared Access Signatures. Für den Zugriff auf Ihr Speicherdienstkonto
von Ihrer eigenen Anwendung aus verwenden Sie das freigegebene
Hash, das über das Azure-Portal beim Konfigurieren und Verwalten
Ihrer Speicherdienstkonten bereitgestellt wird. Wenn Sie einer anderen Person Zugriff auf die Blobs und Container
in Ihrem Speicherdienstkonto gewähren möchten, verwenden Sie dazu die URLs
für die Shared Access Signatures.

Achten Sie besonders auf den sicheren Umgang mit dem Daten, um eine
Offenlegung zu verhindern. Besondere Vorsicht ist auch bei der
Gültigkeitsdauer der Shared Access Signatures geboten.

![][5]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Managing Access to Blobs and Containers (in englischer Sprache)][Managing Access to Blobs and Containers (in englischer Sprache)]
-   [New Storage Feature: Shared Access Signatures (in englischer Sprache)][New Storage Feature: Shared Access Signatures (in englischer Sprache)]
-   [Shared Access Signatures Are Easy These Days (in englischer Sprache)][Shared Access Signatures Are Easy These Days (in englischer Sprache)]

## Azure SQL-Datenbankidentität und Zugriffsszenarien

Die SQL-Datenbank unterstützt nur SQL Server-Authentifizierung. Windows-
Authentifizierung (integrierte Sicherheit) wird nicht unterstützt. Die Benutzer
müssen bei jeder Verbindungsherstellung mit einer SQL-Datenbank
ihre Anmeldeinformationen (Benutzername und Kennwort) angeben. Seien Sie beim Verwalten Ihres Benutzernamens und
Kennworts besonders vorsichtig, um das Offenlegen dieser Informationen zu vermeiden.

![][6]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Sicherheitsrichtlinien und Einschränkungen (Windows Azure SQL-Datenbank)][Sicherheitsrichtlinien und Einschränkungen (Windows Azure SQL-Datenbank)]
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über sqlcmd][Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über sqlcmd]
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über ADO.NET][Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über ADO.NET]
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über ASP.NET][Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über ASP.NET]
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über WCF Data Services][Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über WCF Data Services]
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über PHP][Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über PHP]
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über JDBC][Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über JDBC]
-   [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über das ADO.NET Entity Framework][Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über das ADO.NET Entity Framework]

## Azure Service Bus-Identität und Zugriffsszenarien

Service Bus und Azure AD Access Control stehen in einer
besonderen Beziehung zueinander, da jeder Service Bus-
Dienstnamespace mit einem entsprechenden Access Control-Dienstnamespace gepaart ist.
Dieser trägt den gleichen Namen mit dem Zusatz "-sb". Der Grund für diese besondere Beziehung liegt in der
Art, wie Service Bus und Access Control ihre gegenseitige
Vertrauensbeziehung und die damit verknüpften Verschlüsselungsgeheimnisse verwalten. Weitere
Informationen finden Sie in den im Folgenden aufgeführten Ressourcen.

![][7]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Securing Service Bus with ACS][Securing Service Bus with ACS] (Video, in englischer Sprache)
-   [Securing Service Bus with ACS][8] (Folien, in englischer Sprache)
-   [Service Bus-Authentifizierung und -Autorisierung mit dem Access
    Control Service][Service Bus-Authentifizierung und -Autorisierung mit dem Access
    Control Service]

## In-Memory-Cacheidentität und Zugriffszenarien

In-Memory-Cache (vormals Azure Cache genannt) stützt sich
für die Authentifizierung auf Azure AD Access Control. Es verwendet die
freigegebenen Schlüssel, die über das Verwaltungsportal verfügbar sind. Verwenden Sie beim Cachezugriff
die Schlüssel im Code oder in den Konfigurationsdateien. Achten Sie auf eine sichere
Speicherung der Schlüssel, um das Offenlegen von Daten zu verhindern.

![][9]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Gewusst wie: Programmgesteuertes Konfigurieren eines Cacheclients
    (Azure Shared Caching)][Gewusst wie: Programmgesteuertes Konfigurieren eines Cacheclients
    (Azure Shared Caching)]
-   [Gewusst wie: Konfigurieren eines Cacheclients mithilfe der
    Anwendungskonfigurationsdatei (Azure Shared Caching)][Gewusst wie: Konfigurieren eines Cacheclients mithilfe der
    Anwendungskonfigurationsdatei (Azure Shared Caching)]
-   [Azure AppFabric-Beispiele][Azure AppFabric-Beispiele] (im Abschnitt "Cachebeispiele")

## Azure Marketplace-Identität und Zugriffsszenarien

Bei jedem kostenlosen oder kostenpflichtigen Zugriff auf einen
Azure Marketplace-DataSet muss der Benutzer authentifiziert werden, bevor er Zugriff erhält. Beim
Erstellen einer Anwendung, muss der Authentifizierungsvorgang im Code
enthalten sein. Beachten Sie die folgenden, häufig auftretenden Szenarien:

### Zugriff auf eigenen DataSet

In diesem Szenario erstellen Sie eine Anwendung, die DataSets in ihrem
Marketplace-Abonnement verbraucht. Sie sind der Benutzer der Anwendung.
Die Anwendung kann unter Azure, lokal oder auf Marketplace bereitgestellt werden.

Verwenden Sie den gemeinsam verwendeten Schlüssel, der
über das Marketplace-Abonnement erhältlich ist. Sie erhalten den gemeinsam verwendeten Schlüssel über die Verwendung des Marketing-Portals.

![][10]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Implementieren von HTTP-Standardauthentifizierung in Ihrer Marketplace-App][Implementieren von HTTP-Standardauthentifizierung in Ihrer Marketplace-App]

### Zugriff von Benutzern auf meine DataSets

In diesem Szenario erstellen Sie eine Anwendung, die Benutzern den
Zugriff auf Ihren DataSet ermöglicht. Die Anwendung kann unter Azure, lokal oder
auf Marketplace bereitgestellt werden.

Verwenden Sie zum Lösen dieses Szenarios die Delegierung "OAuth". Benutzer werden zur
Eingabe ihrer Anmeldeinformationen für Live-ID/Microsoft-Konto aufgefordert.
Anschließend werden sie durch den Zustimmungsvorgang geführt.

![][11]

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [OAuth Web Client Example (in englischer Sprache)][OAuth Web Client Example (in englischer Sprache)]
-   [OAuth Rich Client Example (in englischer Sprache)][OAuth Rich Client Example (in englischer Sprache)]

### Anwendungszugriff auf das Marketplace-API

In diesem Szenario erstellen Sie eine Anwendung, die auf die
Marketplace-API zugreift. Die Marketplace-API erfordert eine Authentifizierung
für die erfolgreiche Verarbeitung von Aufrufen. Die Anwendung wird in Azure
Marketplace bereitgestellt.

![][12]

Detaillierte Informationen über das Implementieren der
Authentifizierung finden Sie auch im Marketing-Publishing-Kit.

In den folgenden Ressourcen finden Sie Hinweise zum Lösen dieses Szenarios:

-   [Anwendungs-Publishing-Kit herunterladen][Anwendungs-Publishing-Kit herunterladen]
-   [Introduction to Azure Marketplace for Applications (in englischer Sprache)][Introduction to Azure Marketplace for Applications (in englischer Sprache)]

## Sicherheitsknöpfe

In diesem Abschnitt werden Sicherheitsköpfe für Windows Identity Foundation
und Azure AD Access Control vorgestellt. Verwenden Sie sie beim Entwerfen
und Bereitstellen Ihrer Anwendung als eine einfache Sicherheitscheckliste für diese Technologien.

### Windows Identity Foundation

Im folgenden werden wichtige Sicherheitsknöpfe von WIF aufgeführt. Die unten stehenden Informationen
sind ein Auszug aus [Überlegungen zum Entwurf][Überlegungen zum Entwurf] und [Windows Identity Foundation (WIF)
Security for ASP.NET Web Applications - Threats & Countermeasures][Windows Identity Foundation (WIF)
Security for ASP.NET Web Applications - Threats & Countermeasures]
 (in englischer Sprache).

-   **IssuerNameRegistry**. Legt vertrauenswürdige Sicherheitstokendienste
    (STS) fest. Vergewissern Sie sich, dass nur vertrauenswürdige STSs aufgeführt werden.
-   **cookieHandler requireSsl="true"**. Legt fest, ob Sitzungscookies
    über das SSL-Protokoll übertragen werden.
-   **wsFederation's requireHttps="true"**. Legt fest, ob die
    Kommunikation des Verbundprotokolls mit dem Identitätsanbieter über
    das SSL-Protokoll erfolgt.
-   **tokenReplayDetection enabled="true"**. Legt fest, ob die Funktion
    zur Erkennung einer Tokenwiedergabe aktiv ist. Achten Sie darauf, dass
    diese Funktion Serveraffinität verursacht, da sie lokale Kopien verwendeter Token verwaltet.
-   **audienceUris**. Legt das Zielpublikum für das Token fest. Wenn
    Ihre Anwendung ein Token erhält, das nicht für sie gedacht war,
    wird es von WIF abgelehnt.
-   **requestValidation** und **httpRuntime requestValidationType**.
    Aktiviert/deaktiviert die ASP.NET-Validierungsfunktion. Weitere hilfreiche
    Hinweise finden Sie unter [Windows Identity Foundation (WIF): A Potentially
    Dangerous Request.Form Value Was Detected from the Client][Windows Identity Foundation (WIF): A Potentially
    Dangerous Request.Form Value Was Detected from the Client] (in englischer Sprache)

### Azure AD Access Control

Beachten Sie die folgenden Sicherheitsknöpfe bei der Bereitstellung
mit Azure AD Access Control. Die unten stehenden Informationen sind ein Auszug aus [ACS-
Sicherheitsrichtlinien][ACS-
Sicherheitsrichtlinien] und [Verwaltungsrichtlinien für Zertifikate und Schlüssel][Verwaltungsrichtlinien für Zertifikate und Schlüssel].

-   **Ablauf von STS-Token**. Legen Sie über das
    Verwaltungsportal von Azure AD Access Control einen aggressiven Tokenablauf fest.
-   **Datenvalidierung bei Verwendung der Funktion "Fehler-URL"**. Die
    Funktion "Fehler-URL" von Azure AD Access Control erfordert einen anonymen
    Zugriff auf die Seite der Anwendung, an die sie Fehlermeldungen sendet. Gehen Sie bei sämtlichen
    Daten, die an diese Seite gehen, davon aus, dass sie gefährlich sind und aus einer nicht vertrauenswürdigen Quelle stammen.
-   **Verschlüsseln von Token für hoch sensible Szenarien**. Erwägen
    Sie ein Verschlüsseln der Token, um die Bedrohung durch
    eine Offenlegung der im Token vorhandenen Informationen zu verringern.
-   **Verschlüsseln von Cookies mit RSA beim Bereitstellen in Azure**.
    WIF verschlüsselt Cookies standardmäßig mit DPAPI. Dies führt zu
    Serveraffinität und kann bei Bereitstellung in Webfarm- und Azure-Umgebungen
    zu Ausnahmen führen. Verwenden Sie bei Webfarm- und
    Azure-Szenarien stattdessen RSA.
-   **Tokensignaturzertifikate**. Erneuern Sie Tokensignaturzertifikate
    regelmäßig, um einen Denial-of-Service-Angriff zu vermeiden. Azure AD Access
    Control signiert alle von ihm ausgegebenen Token. Beim Erstellen einer
    Anwendung, die von ACS ausgestellte SAML-Tokens nutzt, werden
    X.509-Zertifikate für die Signatur verwendet. Nach dem Ablauf von Signaturzertifikaten
    erhalten Sie Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern.
-   **Tokensignaturschlüssel**. Erneuern Sie Tokensignaturschlüssel
    regelmäßig, um einen Denial-of-Service-Angriff zu vermeiden. Azure AD Access Control signiert
    alle von ihm ausgegebenen Token. Beim Erstellen einer Anwendung, die von
    ACS ausgestellte SWT-Tokens nutzt, werden symmetrische
    256-Bit-Signaturschlüssel verwendet. Nach dem Ablauf von Signaturschlüsseln erhalten Sie
    Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern.
-   **Tokenverschlüsselungszertifikate**. Erneuern Sie
    Tokenverschlüsselungszertifikate regelmäßig, um einen Denial-of-Service-Angriff zu vermeiden. Eine
    Tokenverschlüsselung ist erforderlich, wenn es sich bei der
    Anwendung einer vertrauenden Seite um einen Webdienst handelt,
    der Proof-of-Possession-Token über das WS-Trust-Protokoll verwendet. In anderen Fällen ist eine Verschlüsselung optional. Nach dem Ablauf
    von Verschlüsselungszertifikaten erhalten Sie Fehlermeldungen,
    wenn Sie versuchen, ein Token anzufordern.
-   **Tokenentschlüsselungszertifikate**. Erneuern Sie
    Tokenentschlüsselungszertifikate regelmäßig, um einen Denial-of-Service-Angriff zu vermeiden. Azure
    AD Access Control kann verschlüsselte Token von WS-Verbund-Identitätsanbietern
    annehmen (z. B. AD FS 2.0). Zur Entschlüsselung
    wird ein in Azure AD Access Control gehostetes X.509-Zertifikat
    verwendet. Nach dem Ablauf von Entschlüsselungszertifikaten
    erhalten Sie Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern.
-   **Anmeldeinformationen für die Dienstidentität**. Erneuern Sie die Anmeldeinformationen
    für die Dienstidentität regelmäßig, um einen Denial-of-Service-Angriff zu vermeiden. Dienstidentitäten verwenden
    Anmeldeinformationen, die global für Ihren Azure AD
    Access Control-Namespace konfiguriert werden und die
    Anwendungen und Clients eine direkte Authentifizierung
    bei Azure AD Access Control ermöglichen, um ein Token zu erhalten. Die Azure AD Access Control-Dienstidentität
    kann drei Arten von Anmeldeinformationen zugeordnet werden: Symmetrischer
    Schlüssel, Kennwort und X.509-Zertifikat. Wenn die
    Anmeldeinformationen abgelaufen sind, beginnen Sie, Ausnahmen zu erhalten.
-   **Kontoanmeldeinformationen für den Azure AD Access
    Control-Verwaltungsdienst**. Erneuern Sie die Anmeldeinformationen für den
    Verwaltungsdienst regelmäßig, um einen Denial-of-Service-Angriff zu vermeiden. Der Verwaltungsdienst von
    Azure AD Access Control ist eine Schlüsselkomponente,
    mit der Sie die Einstellungen für den Azure
    AD Access Control-Namespace programmatisch verwalten und konfigurieren können. Das Verwaltungsdienstkonto
    kann mit drei Arten von Anmeldeinformationen verknüpft werden. Dabei
    handelt es sich um einen symmetrischen Schlüssel, ein Kennwort und ein X.509-Zertifikat. Wenn
    die Anmeldeinformationen abgelaufen sind, beginnen Sie, Ausnahmen zu erhalten.
-   **Signatur- und Verschlüsselungszertifikate für
    WS-Verbund-Identitätsanbieter**. Führen Sie eine Abfrage der Zertifikatgültigkeit
    für den WS-Verbund-Identitätsanbieter durch, um einen Denial-of-Service-Angriff zu vermeiden. Das Zertifikat des
    WS-Verbund-Identitätsanbieters steht über seine Metadaten
    zur Verfügung. Beim Konfigurieren eines WS-Verbund-
    Identitätsanbieters, z. B. AD FS, wird das WS-Verbund-
    Signaturzertifikat über WS-Verbund-Metadaten konfiguriert, die per URL oder als Datei verfügbar sind. Sobald Sie
    den WS-Verbund-Identitätsanbieter konfiguriert haben,
    fragen Sie die Gültigkeit seiner Zertifikate mithilfe des Verwaltungsdiensts
    von Azure AD Access Control ab. Nach dem Ablaufen des Zertifikats, werden Sie anfangen,
    Ausnahmen zu erhalten.

## Freigegebenes Hosting über Azure-Websites

Alle in diesem Thema umrissenen Szenarien und Lösungen sind gültig,
wenn die Anwendung auf Azure-Websites gehostet wird.

## Azure Virtual Machines

Alle in diesem Thema umrissenen Szenarien und Lösungen sind gültig,
wenn die Anwendung auf Azure Virtual Machines gehostet wird.

## Ressourcen

-   [Identity Developer Training Kit (nur in englischer Sprache)][13]
-   [MSDN-hosted Identity Developer Training Course (in englischer Sprache)][14]
-   [A Guide to Claims-Based Identity and Access Control (in englischer Sprache)][15]
-   [Zugriffssteuerungsdienst (ACS)][Zugriffssteuerungsdienst (ACS)]
-   [ACS – Vorgehensweisen][ACS – Vorgehensweisen]
-   [Secure Azure Web Role ASP.NET Web Application Using Access Control Service v2.0 (in englischer Sprache)][Secure Azure Web Role ASP.NET Web Application Using Access Control Service v2.0 (in englischer Sprache)]
-   [Azure AD Access Control Service (ACS) Academy Videos (in englischer Sprache)][Azure AD Access Control Service (ACS) Academy Videos (in englischer Sprache)]
-   [Microsoft Security Development Lifecycle (in englischer Sprache)][Microsoft Security Development Lifecycle (in englischer Sprache)]
-   [SDL Threat Modeling Tool 3.1.8 (in englischer Sprache)][SDL Threat Modeling Tool 3.1.8 (in englischer Sprache)]
-   [Security and Privacy Blogs (in englischer Sprache)][Security and Privacy Blogs (in englischer Sprache)]
-   [Security Response Center (in englischer Sprache)][Security Response Center (in englischer Sprache)]
-   [Security Intelligence Report (in englischer Sprache)][Security Intelligence Report (in englischer Sprache)]
-   [Security Development Lifecycle (in englischer Sprache)][Microsoft Security Development Lifecycle (in englischer Sprache)]
-   [Security Developer Center (MSDN)][Security Developer Center (MSDN)]

  [Sichern der Anwendung]: ./media/SecurityRX/01_SecuringTheApplication.gif
  [Security Guidance for Applications Index]: http://msdn.microsoft.com/de-de/library/ff650760.aspx
  [Bedrohungen, Schwachstellen und Angriffe]: ./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
  [Beispiele für Windows Identity Foundation 4.5]: http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication
  [Windows Identity Foundation 4.5-Tools für Visual Studio 11 Beta]: http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [Windows Identity Foundation-Laufzeit (.Net 3.5/4.0)]: http://www.microsoft.com/de-de/download/details.aspx?id=17331
  [Access Control Service 2.0]: http://msdn.microsoft.com/library/gg429786.aspx
  [Szenarien und Lösungen mit ACS]: http://msdn.microsoft.com/de-de/library/gg185920.aspx
  [ACS – Vorgehensweisen]: http://msdn.microsoft.com/de-de/library/windowsazure/gg185939.aspx
  [A Guide to Claims-Based Identity and Access Control (in englischer Sprache)]: http://msdn.microsoft.com/de-de/library/ff423674.aspx
  [Identity Developer Training Kit (nur in englischer Sprache)]: http://www.microsoft.com/de-de/download/details.aspx?id=14347
  [MSDN-hosted Identity Developer Training Course (in englischer Sprache)]: http://msdn.microsoft.com/de-de/IdentityTrainingCourse
  [AD FS 2.0 Content Map (in englischer Sprache)]: http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx
  [Web SSO Design (in englischer Sprache)]: http://technet.microsoft.com/de-de/library/dd807033(WS.10).aspx
  [Federated Web SSO Design (in englischer Sprache)]: http://technet.microsoft.com/de-de/library/dd807050(WS.10).aspx
  [Managing Access to Blobs and Containers (in englischer Sprache)]: http://msdn.microsoft.com/de-de/library/ee393343.aspx
  [New Storage Feature: Shared Access Signatures (in englischer Sprache)]: http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures
  [Shared Access Signatures Are Easy These Days (in englischer Sprache)]: http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days
  [Azure Active Directory Access Control]: ./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
  [Gewusst wie: Erstellen einer ersten Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von ACS]: http://msdn.microsoft.com/de-de/library/gg429779.aspx
  [Gewusst wie: Hosten von Anmeldeseiten in einer ASP-NET-Webanwendung]: http://msdn.microsoft.com/de-de/library/gg185926.aspx
  [Gewusst wie: Implementieren von Anspruchsautorisierung in einer Ansprüche unterstützenden ASP.NET-Anwendung mithilfe von WIF und ACS]: http://msdn.microsoft.com/de-de/library/gg185907.aspx
  [Codebeispiel: Einfache ASP.NET-Formulare]: http://msdn.microsoft.com/de-de/library/gg185938.aspx
  [WCF (SOAP)-Dienst]: ./media/SecurityRX/04_WCF(SOAP)Service.gif
  [Codebeispiel: WCF-Zertifikatauthentifizierung]: http://msdn.microsoft.com/de-de/library/gg185952.aspx
  [Codebeispiel: WCF-Benutzernamenauthentifizierung]: http://msdn.microsoft.com/de-de/library/gg185927.aspx
  [WCF (SOAP)-Dienst mit AD]: ./media/SecurityRX/05_AzureADAccessControl.gif
  [Gewusst wie: Konfigurieren von AD FS 2.0 als Identitätsanbieter]: http://msdn.microsoft.com/de-de/library/gg185961.aspx
  [Codebeispiel: WCF-Verbundauthentifizierung mit AD FS 2.0]: http://msdn.microsoft.com/de-de/library/hh127796.aspx
  [REST-Dienst]: ./media/SecurityRX/06_RESTService.gif
  [Codebeispiel: ASP.NET-Webdienst]: http://msdn.microsoft.com/de-de/library/gg983271.aspx
  [WIF ist optional.]: ./media/SecurityRX/07_WIFisOptional.gif
  [Gewusst wie: Konfigurieren von Google als Identitätsanbieter]: http://msdn.microsoft.com/de-de/library/gg185976.aspx
  [Gewusst wie: Konfigurieren von Facebook als Identitätsanbieter]: http://msdn.microsoft.com/de-de/library/gg185919.aspx
  [Gewusst wie: Konfigurieren von Yahoo! als Identitätsanbieter]: http://msdn.microsoft.com/de-de/library/gg185977.aspx
  [ASP.NET-Webanwendung]: ./media/SecurityRX/08_ASPNETWebApptoREST.gif
  [0]: ./media/SecurityRX/09_RBAC.gif
  [1]: ./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
  [2]: ./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
  [3]: ./media/SecurityRX/12_CustomRoleManager.gif
  [Gewusst wie: Implementieren von Tokentransformationslogik mithilfe von Regeln]: http://msdn.microsoft.com/de-de/library/gg185955.aspx
  [4]: ./media/SecurityRX/13_ClaimsAuthorizationManager.gif
  [5]: ./media/SecurityRX/14_WindowsAzurestorage.gif
  [6]: ./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
  [Sicherheitsrichtlinien und Einschränkungen (Windows Azure SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394108.aspx#authentication
  [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über sqlcmd]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336280.aspx
  [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über ADO.NET]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336243.aspx
  [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über ASP.NET]: http://msdn.microsoft.com/de-de/library/windowsazure/ee621781.aspx
  [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über WCF Data Services]: http://msdn.microsoft.com/de-de/library/windowsazure/ee621789.aspx
  [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über PHP]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394110.aspx
  [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über JDBC]: http://msdn.microsoft.com/de-de/library/windowsazure/gg715284.aspx
  [Gewusst wie: Herstellen einer Verbindung mit der Windows Azure SQL-Datenbank über das ADO.NET Entity Framework]: http://msdn.microsoft.com/de-de/library/windowsazure/ff951633.aspx
  [7]: ./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
  [Securing Service Bus with ACS]: http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS
  [8]: https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849
  [9]: ./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
  [Azure AppFabric-Beispiele]: http://msdn.microsoft.com/de-de/library/ee706741.aspx
  [10]: ./media/SecurityRX/18_IAccessMyDataset.gif
  [Implementieren von HTTP-Standardauthentifizierung in Ihrer Marketplace-App]: http://msdn.microsoft.com/de-de/library/gg193417.aspx
  [11]: ./media/SecurityRX/19_UsersAccessMyDatasets.gif
  [OAuth Web Client Example (in englischer Sprache)]: http://go.microsoft.com/fwlink/?LinkId=219162
  [OAuth Rich Client Example (in englischer Sprache)]: http://go.microsoft.com/fwlink/?LinkId=219163
  [12]: ./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif
  [Anwendungs-Publishing-Kit herunterladen]: http://go.microsoft.com/fwlink/?LinkId=221323
  [Introduction to Azure Marketplace for Applications (in englischer Sprache)]: https://datamarket.azure.com/
  [Überlegungen zum Entwurf]: http://msdn.microsoft.com/de-de/library/ee517298.aspx
  [Verwaltungsrichtlinien für Zertifikate und Schlüssel]: http://msdn.microsoft.com/de-de/library/hh204521.aspx
  [13]: http://go.microsoft.com/fwlink/?LinkId=214555
  [14]: http://go.microsoft.com/fwlink/?LinkId=214561
  [15]: http://go.microsoft.com/fwlink/?LinkId=214562
  [Zugriffssteuerungsdienst (ACS)]: http://msdn.microsoft.com/de-de/library/windowsazure/gg429786.aspx
  [Secure Azure Web Role ASP.NET Web Application Using Access Control Service v2.0 (in englischer Sprache)]: http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx
  [Azure AD Access Control Service (ACS) Academy Videos (in englischer Sprache)]: http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx
  [Microsoft Security Development Lifecycle (in englischer Sprache)]: http://www.microsoft.com/security/sdl/default.aspx
  [SDL Threat Modeling Tool 3.1.8 (in englischer Sprache)]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955
  [Security and Privacy Blogs (in englischer Sprache)]: http://www.microsoft.com/about/twc/en/us/blogs.aspx
  [Security Response Center (in englischer Sprache)]: http://www.microsoft.com/security/msrc/default.aspx
  [Security Intelligence Report (in englischer Sprache)]: http://www.microsoft.com/security/sir/
  [Security Developer Center (MSDN)]: http://msdn.microsoft.com/security/
