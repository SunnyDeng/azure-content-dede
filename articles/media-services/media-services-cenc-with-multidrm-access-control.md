<properties 
	pageTitle="CENC mit mehreren DRM-Systemen und Access Control: Referenzentwurf und -implementierung in Azure und Azure Media Services" 
	description="Weitere Informationen zur Lizenzierung des Microsoft® Smooth Streaming Client Porting Kit" 
	services="media-services" 
	documentationCenter="" 
	authors="willzhan"  
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2016"  
	ms.author="willzhan;kilroyh;yanmf;juliako"/>

#CENC mit mehreren DRM-Systemen und Access Control: Referenzentwurf und -implementierung in Azure und Azure Media Services

##Schlüsselwörter
 
Azure Active Directory, Azure Media Services, Azure Media Player, Dynamische Verschlüsselung, Lizenzübermittlung, PlayReady, Widevine, FairPlay, Common Encryption (CENC), Mehrere DRM-Systeme, Axinom, DASH, EME, MSE, JSON Web Token (JWT), Ansprüche, Moderne Browser, Schlüsselrollover, Symmetrischer Schlüssel, Asymmetrischer Schlüssel, OpenID Connect, X.509-Zertifikat.

##Themen in diesem Artikel

In diesem Artikel werden die folgenden Themen behandelt:

- [Einführung](media-services-cenc-with-multidrm-access-control.md#introduction)
	- [Übersicht über diesen Artikel](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [Ein Referenzentwurf](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [Zuordnen des Entwurfs zu der zu implementierenden Technologie](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [Implementierung](media-services-cenc-with-multidrm-access-control.md#implementation)
	- [Implementierungsverfahren](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
	- [Besondere Aspekte bei der Implementierung](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [Zusätzliche Themen für die Implementierung](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
	- [HTTP oder HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
	- [Azure Active Directory-Rollover von Signaturschlüsseln](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
	- [Wo befindet sich das Zugriffstoken?](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
	- [Was gilt für Livestreaming?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
	- [Was gilt für Lizenzserver außerhalb von Azure Media Services?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
	- [Was geschieht, wenn ich einen benutzerdefinierten STS verwenden möchte?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [Das fertige System und Tests](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
	- [Benutzeranmeldung](media-services-cenc-with-multidrm-access-control.md#user-login)
	- [Verwenden von Encrypted Media Extensions für PlayReady](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
	- [Verwenden von EME für Widevine](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
	- [Nicht berechtigte Benutzer](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
	- [Ausführen eines benutzerdefinierten Sicherheitstokendiensts](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [Zusammenfassung](media-services-cenc-with-multidrm-access-control.md#summary)

##Einführung

Es ist allgemein bekannt, dass es ist eine komplexe Aufgabe ist, ein DRM-Subsystem für eine OTT- (Over-The-Top) oder Onlinestreaminglösung zu entwerfen und zu erstellen. Zudem es ist üblich, dass Onlinevideoanbieter diese Aufgabe spezialisierten DRM-Dienstanbietern übertragen. Ziel dieses Dokuments ist es, einen Referenzentwurf und eine Referenzimplementierung eines kompletten DRM-Subsystems in einer OTT- oder Onlinestreaminglösung zu präsentieren.

Zielgruppe dieses Dokuments sind Entwickler, die an einem DRM-Subsystem für OTT- oder Onlinestreaming/Multi-Bildschirmlösungen arbeiten, oder Leser, die sich für ein DRM-Subsystem interessieren. Es wird angenommen, dass Leser mit mindestens einer der DRM-Technologien auf dem Markt vertraut sind, wie z. B. PlayReady, Widevine, FairPlay oder Adobe Access.

Zu DRM zählen wir auch CENC (Common Encryption) mit mehreren DRM-Systemen. Ein wichtiger Trend in der Onlinestreaming- und OTT- Branche ist das Verwenden von CENC mit mehreren systemeigenen DRM-Systemen auf verschiedenen Clientplattformen. Dies ist eine Abkehr vom vorherigen Trend der Verwendung eines einzelnen DRM-Subsystems und von dessen Client-SDK für verschiedene Clientplattformen. Bei Verwenden von CENC mit mehreren systemeigenen DRM-Systemen sind PlayReady und Widevine gemäß der Spezifikation [Common Encryption (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) verschlüsselt.

Die Vorteile von CENC mit mehreren DRM-Systemen sind wie folgt:

1. Eine Reduzierung der Kosten, da eine einzelne Verschlüsselungsverarbeitung für unterschiedliche Zielplattformen mit deren systemeigenen DRM-Systemen erfolgt.
1. Weniger Kosten für die Verwaltung verschlüsselter Assets, da nur eine einzige Kopie der verschlüsselte Assets erforderlich ist.
1. Wegfall der Kosten für die DRM-Clientlizenzierung, da der systemeigene DRM-Client in der Regel auf seiner systemeigenen Plattform kostenlos ist.

Microsoft ist zusammen mit einigen anderen wichtigen Anbietern ein aktiver Förderer von DASH und CENC. Microsoft Azure Media Services bietet Unterstützung für DASH und CENC. Aktuelle Ankündigungen finden Sie in den Blogs von Mingfei: [Announcing Google Widevine license delivery services in Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/) (Ankündigung von Google Widevine-Diensten zur Lizenzbereitstellung in Azure Media Services) und [Azure Media Services adds Google Widevine packaging for delivering multi-DRM stream](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) (Azure Media Services jetzt mit Google Widevine-Paketerstellung zum Bereitstellen von Multi-DRM-Datenströmen).

### Übersicht über diesen Artikel

Dieser Artikel bietet Folgendes:

1. Einen Referenzentwurf eines DRM-Subsystems unter Verwendung von CENC mit mehreren DRM-Systemen
1. Eine Referenzimplementierung auf der Microsoft Azure-/Azure Media Services-Plattform
1. Eine Erörterung verschiedener Entwurfs- und Implementierungsthemen

In diesem Artikel bezieht sich „mehrere DRM-Systeme“ auf Folgendes:

1. Microsoft PlayReady
1. Google Widevine
1. Apple FairPlay (noch nicht von Azure Media Services unterstützt)

In der folgenden Tabelle werden die systemeigene Plattform/App und die Browser vorgestellt, die von jedem DRM-System unterstützt werden.

**Clientplattform**|**Unterstützung für native DRM**|**Browser/App**|**Streamingformate**
----|------|----|----
**Smart-TVs, Operator-STBs, OTT-STBs**|Primär PlayReady und/oder Widevine und/oder andere|Linux, Opera, WebKit, andere|Verschiedene Formate
**Windows 10-Geräte (Windows-PC, Windows-Tablets, Windows Phone, Xbox)**|PlayReady|MS Edge/IE11/EME<br/><br/><br/>UWP|DASH (für HLS wird PlayReady nicht unterstützt)<br/><br/>DASH, Smooth Streaming (für HLS wird PlayReady nicht unterstützt) 
**Android-Geräte (Telefon, Tablet, TV)**|Widevine|Chrome/EME|DASH
**iOS (iPhone, iPad), OS X-Clients und Apple-TV**|FairPlay|Safari 8+/EME|HLS
**Plugin: Adobe Primetime**|Primetime Access|Browser-Plug-in|HDS, HLS

Unter Berücksichtigung des aktuellen Status der Bereitstellung für jedes DRM möchte ein Dienst in der Regel 2 oder 3 DRMs implementieren, um sicherzustellen, dass Sie alle Typen von Endpunkten auf bestmögliche Weise ansprechen.

Ein Kompromiss zwischen der Komplexität der Dienstlogik und der Komplexität auf der Clientseite sorgt dafür, dass auf den verschiedenen Clients ein gewisses Maß an Benutzerfreundlichkeit erzielt wird.

Beachten Sie die folgenden Fakten, wenn Sie Ihre Auswahl treffen:

- PlayReady ist nativ in jedem Windows-Gerät und einigen Android-Geräten implementiert und auf nahezu jeder Plattform über Software-SDKs verfügbar.
- Widevine ist nativ in jedem Android-Gerät, in Chrome und einigen anderen Geräten implementiert.
- FairPlay ist nur in iOS und Mac OS-Clients oder über iTunes verfügbar.

So sähe also eine typische Multi-DRM aus:

- Option 1: PlayReady und Widevine
- Option 2: PlayReady, Widevine und FairPlay


## Ein Referenzentwurf

In diesem Abschnitt präsentieren wir einen Referenzentwurf, der hinsichtlich der für seine Implementierung verwendeten Technologien agnostisch ist.

Ein DRM-Subsystem kann die folgenden Komponenten enthalten:

1. Schlüsselverwaltung
1. DRM-Verpackung
1. DRM-Lizenzbereitstellung
1. Berechtigungsüberprüfung
1. Authentifizierung/Autorisierung
1. Player
1. Ursprung/CDN

Das folgende Diagramm veranschaulicht die allgemeine Interaktion zwischen den Komponenten in einem DRM-Subsystem.

![DRM-Subsystem mit CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
Der Entwurf weist drei grundlegende Ebenen auf:

1. Die Ebene „Backoffice“ (schwarz), die nicht extern verfügbar gemacht wird
1. Die Ebene „DMZ“ (blau), die alle öffentlich zugänglichen Endpunkte enthält
1. Die Ebene „Öffentliches Internet“ (hellblau) mit dem CDN und Playern mit Datenverkehr über das öffentliche Internet
 
Es muss ein Content Management-Tool zum Steuern des DRM-Schutzes unabhängig von der Verschlüsselung (statisch oder dynamisch) vorhanden sein. Die Eingaben für die DRM-Verschlüsselung müssen Folgendes umfassen:

1. MBR-Videoinhalte
1. Inhaltsschlüssel
1. Lizenzerwerbs-URLs

Während der Wiedergabe ist der allgemeine Ablauf wie folgt:

1. Benutzer wird authentifiziert.
1. Autorisierungstoken wird für den Benutzer erstellt.
1. Durch DRM geschützte Inhalte (Manifest) werden in den Player heruntergeladen.
1. Player sendet Lizenzerwerbsanforderung zusammen mit Schlüssel-ID und Autorisierungstoken an den Lizenzserver.

Bevor wir mit dem nächsten Thema fortfahren, möchte ich kurz auf den Entwurf der Schlüsselverwaltung eingehen.

**Inhaltsschlüssel zu Asset**|**Szenario**
------|---------------------------
1:1|Der einfachste Fall, der zwar die beste Kontrolle bietet, aber auch im Allgemeinen zu den höchsten Kosten für die Lizenzbereitstellung führt. Für jedes geschützte Asset ist mindestens eine Lizenzanforderung erforderlich.
1:n|Derselbe Inhaltsschlüssel kann für mehrere Assets verwendet werden. Sie können beispielsweise für alle Assets in einer logischen Gruppe wie einem Genre oder einem Teilgenre denselben Inhaltsschlüssel verwenden.
n:1|Für jedes Asset sind mehrere Inhaltsschlüssel erforderlich. <br/><br/>Wenn Sie z. B. dynamischen CENC-Schutz mit mehreren DRM-Systemen für MPEG-DASH und dynamische AES-128-Verschlüsselung für HLS aktivieren müssen, benötigen Sie zwei separate Inhaltsschlüssel mit jeweils eigenem ContentKeyType-Element. (Für den Inhaltsschlüssel, der für dynamischen CENC-Schutz verwendet wird, sollte „ContentKeyType.CommonEncryption“ verwendet werden, während für den Inhaltsschlüssel, der für die dynamische AES-128-Verschlüsselung verwendet wird, „ContentKeyType.EnvelopeEncryption“ verwendet werden sollte.)<br/><br/>Ein weiteres Beispiel: Für den CENC-Schutz von DASH-Inhalten kann theoretisch ein Inhaltsschlüssel verwendet werden, um den Videodatenstrom zu schützen, und ein anderer Inhaltsschlüssel, um den Audiodatenstrom zu schützen. 
n:n|Eine Kombination der beiden vorherigen Szenarien: ein Satz von Schlüsseln wird für jedes der Assets in derselben Assetgruppe verwendet.


Ein weiterer wichtiger zu berücksichtigender Faktor ist die Verwendung permanenter und nicht permanenter Lizenzen.

Dieser Faktor ist wichtig,

weil er einen direkten Einfluss auf die Kosten der Lizenzbereitstellung hat, wenn dafür die öffentliche Cloud verwendet wird. Nachfolgend möchte ich zwei verschiedene Entwurfsmodelle veranschaulichen:



1. Monatliches Abonnement: Permanente Lizenz mit einer Zuordnung von Inhaltsschlüssel zu Asset des Typs 1:n. Für alle Kinderfilme verwenden wir z. B. für die Verschlüsselung einen einzelnen Inhaltsschlüssel. In diesem Fall gilt: 

	Gesamtanzahl der für alle Kinderfilme pro Gerät angeforderten Lizenzen = 1

1. Monatliches Abonnement: Keine permanente Lizenz mit einer Zuordnung von Inhaltsschlüssel zu Asset des Typs 1:1. In diesem Fall gilt:

	Gesamtanzahl der für alle Kinderfilme pro Gerät angeforderten Lizenzen = [Anzahl der konsumierten Filme] x [Anzahl der Sitzungen]

Wie Sie leicht erkennen können, führen die beiden unterschiedlichen Entwürfe zu überaus verschiedenen Lizenzanforderungsmustern und somit Lizenzbereitstellungskosten, wenn der Lizenzbereitstellungsdienst über eine öffentliche Cloud wie Azure Media Services bereitgestellt wird.

## Zuordnen des Entwurfs zu der zu implementierenden Technologie

Als Nächstes ordnen wir unseren allgemeinen Entwurf Technologien auf der Microsoft Azure-/Azure Media Services-Plattform zu, indem wir angeben, welche Technologie für jeden Baustein verwendet werden soll.

Die folgende Tabelle zeigt die Zuordnung:

**Baustein**|**Technologie**
------|-------
**Player**|[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/)
**Identitätsanbieter**|Azure Active Directory
**Secure Token Service (STS)**|Azure Active Directory
**Workflow des DRM-Schutzes**|Dynamischer Schutz in Azure Media Services
**DRM-Lizenzbereitstellung**|1\. Azure Media Services-Lizenzbereitstellung (PlayReady, Widevine, FairPlay) oder <br/>2. Axinom-Lizenzserver, <br/>3. Benutzerdefinierter PlayReady-Lizenzserver
**Ursprung**|Azure Media Services-Streaming-Endpunkt
**Schlüsselverwaltung**|Für Referenzimplementierung nicht erforderlich
**Content Management**|C#-Konsolenanwendung

Azure AD wird als Identitätsanbieter und STS (Secure Token Service) genutzt. Als Player wird die [Azure Media Player-API](http://amp.azure.net/libs/amp/latest/docs/) verwendet. Azure Media Services und Azure Media Player unterstützen DASH und CENC mit mehreren DRM-Systemen.

Das folgende Diagramm zeigt die allgemeine Struktur und den Datenfluss bei dieser Technologiezuordnung.

![CENC in AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Zum Einrichten der dynamischen CENC-Verschlüsselung verwendet das Content Management-Tool die folgenden Eingaben:

1. Offene Inhalte
1. Inhaltsschlüssel aus Schlüsselerstellung/-verwaltung
1. Lizenzerwerbs-URLs
1. Eine Liste mit Informationen aus Azure AD

Die Ausgabe des Content Management-Tools ist wie folgt:

1. „ContentKeyAuthorizationPolicy“ mit der Angabe, wie bei der Lizenzbereitstellung ein JWT-Token und DRM-Lizenzspezifikationen überprüft werden
1. „AssetDeliveryPolicy“ mit Angaben zu Streamingformat, DRM-Schutz und Lizenzerwerbs-URLs

Zur Laufzeit ist der Datenfluss wie folgt:

1. Bei der Benutzerauthentifizierung wird ein JWT-Token generiert.
1. Einer der im JWT-Token enthaltenen Ansprüche ist der Anspruch „Groups“, der die Gruppenobjekt-ID von „EntitledUserGroup“ enthält. Dieser Anspruch dient zur Übergabe der „Berechtigungsüberprüfung“.
1. Der Player lädt das Clientmanifest eines durch CENC geschützten Inhalts herunter. Ihm wird Folgendes angezeigt:
	1. Schlüssel-ID 
	1. der Schutz des Inhalts durch CENC
	1. Lizenzerwerbs-URLs

1. Der Player stellt eine Lizenzerwerbsanforderung basierend auf dem unterstützten Browser/DRM. In der Lizenzerwerbsanforderung werden auch die Schlüssel-ID und das JWT-Token übermittelt. Der Lizenzbereitstellungsdiensts prüft das JWT-Token und die enthaltenen Ansprüche, ehe die benötige Lizenz ausgestellt wird.

##Implementierung

###Implementierungsverfahren

Die Implementierung umfasst die folgenden Schritte:

1. Vorbereiten von Testassets: Codieren/Packen eines Testvideos in einer fragmentierten MP4-Datei mit mehreren Bitraten in Azure Media Services. Dieses Asset ist NICHT durch DRM geschützt. Der DRM-Schutz erfolgt später mithilfe einer dynamischen Schutzebene.
1. Erstellen von Schlüssel-ID und Inhaltsschlüssel (optional ausgehend vom Schlüsselwert). Für unsere Zwecke ist ein Schlüsselverwaltungssystem nicht erforderlich, da wir es nur mit einer einzelnen Gruppe von Schlüssel-IDs und Inhaltsschlüsseln für verschiedene Testassets zu tun haben.
1. Verwenden der AMS-API, um für die Testassets Lizenzbereitstellungsdienste für mehrere DRM-Systeme zu konfigurieren. Wenn Sie benutzerdefinierte Lizenzserver in Ihrem Unternehmen oder von beauftragten Unternehmen anstelle der Lizenzdienste in Azure Media Services nutzen, können Sie diesen Schritt überspringen und im Schritt zur Konfiguration der Lizenzbereitstelllung Lizenzerwerbs-URLs angeben. Die AMS-API ist erforderlich, um verschiedene detaillierte Konfigurationen anzugeben, z. B. die Einschränkung von Autorisierungsrichtlinien, Lizenzbereitstellungsvorlagen für verschiedene DRM-Lizenzdienste usw. Das Azure-Portal bietet derzeit nicht die für diese Konfiguration erforderliche Benutzeroberfläche. Informationen zur API und Beispielcode finden Sie im Artikel von Julia Kornich: [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-drm.md). 
1. Konfigurieren Sie mithilfe der AMS-API die Assetbereitstellungsrichtlinie für das Testasset. Informationen zur API und Beispielcode finden Sie im Artikel von Julia Kornich: [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-drm.md).
1. Erstellen und Konfigurieren eines Azure Active Directory-Mandanten in Azure
1. Erstellen einiger Benutzerkonten und Gruppen in Ihrem Azure Active Directory-Mandanten: Sie müssen mindestens die Gruppe „EntitledUser“ erstellen und dieser Gruppe einen Benutzer hinzufügen. Benutzer in dieser Gruppe durchlaufen während des Lizenzerwerbs eine Berechtigungsüberprüfung. Benutzer, die nicht zu dieser Gruppe gehören, bestehen die Authentifizierungsprüfung nicht und können deshalb keine Lizenzen erwerben. Die Mitgliedschaft in der Gruppe „EntitledUser“ ist ein erforderlicher Anspruch des Typs „groups“ im von Azure AD ausgestellten JWT-Token. Diese Anspruchsanforderung muss im Schritt zur Konfiguration von Lizenzbereitstellungsdiensten für mehrere DRM-Systeme angegeben werden.
1. Erstellen einer ASP.NET MVC-App, die Ihren Videoplayer hostet. Diese ASP.NET-App wird für den Azure Active Directory-Mandanten durch eine Benutzerauthentifizierung geschützt. Nach der Benutzerauthentifizierung bezogenen Zugriffstoken werden ordnungsgemäße Ansprüche hinzugefügt. Die OpenID Connect-API wird für diesen Schritt empfohlen. Sie müssen die folgenden NuGet-Pakete installieren:
	- Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
	- Install-Package Microsoft.Owin.Security.OpenIdConnect
	- Install-Package Microsoft.Owin.Security.Cookies
	- Install-Package Microsoft.Owin.Host.SystemWeb
	- Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
1. Erstellen Sie einen Player mithilfe der [Azure Media Player-API](http://amp.azure.net/libs/amp/latest/docs/). Bei der [ProtectionInfo-API von Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) können Sie angeben, welche DRM-Technologie auf unterschiedlichen DRM-Plattformen verwendet werden soll.
1. Testübersicht:

**DRM-System**|**Browser**|**Ergebnis für berechtigten Benutzer**|**Ergebnis für nicht berechtigten Benutzer**
---|---|-----|---------
**PlayReady**|MS Edge oder IE11 unter Windows 10|Erfolg|Kein Erfolg
**Widevine**|Chrome unter Windows 10|Erfolg|Kein Erfolg
**FairPlay** |Noch nicht festgelegt||

George Trifonov vom Azure Media Services-Team hat einen Blog mit detaillierten Schritten zur Einrichtung von Azure Active Directory für eine ASP.NET MVC-Player-App geschrieben: [Integrate Azure Media Services OWIN MVC based app with Azure Active Directory and restrict content key delivery based on JWT claims](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Ein weiterer Blog von George hat den Titel: [JWT token Authentication in Azure Media Services and Dynamic Encryption](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). Und hier ist sein [Beispiel für die Azure AD-Integration in die Azure Media Services-Schlüsselbereitstellung](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Weitere Informationen zu Azure Active Directory:

- Informationen für Entwickler bietet das [Entwicklerhandbuch zu Azure Active Directory](../active-directory/active-directory-developers-guide.md).
- Informationen für Administratoren finden Sie unter [Verwalten Ihres Azure AD-Verzeichnisses](../active-directory/active-directory-administer.md).

### Besondere Aspekte bei der Implementierung

Bei der Implementierung sind bestimmte Aspekte zu beachten. Die folgende Liste soll Ihnen helfen, eventuell auftretende Probleme zu beheben.

1. Die URL zu **Issuer** muss mit **„/“** enden.  

	**Audience** muss die Client-ID der Playeranwendung enthalten. Zudem sollten Sie auch **„/“** an das Ende der URL von „Issuer“ anhängen.

		<add key="ida:audience" value="[Application Client ID GUID]" />
		<add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

	Im [JWT-Decoder](http://jwt.calebb.net/) sollten **aud** und **iss** wie im nachstehenden JWT-Token angezeigt werden:
	
	![1\. Aspekt](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. Fügen Sie Berechtigungen für die Anwendung in AAD (auf der Registerkarte „Konfigurieren“ der Anwendung) hinzu. Dies ist für jede Anwendung (lokale und bereitgestellte Versionen) erforderlich.

	![2\. Aspekt](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. Verwenden Sie den richtigen Aussteller zum Einrichten eines dynamischen CENC-Schutzes:

		<add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
	
	Folgendes funktioniert nicht:
	
		<add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
	
	Die GUID ist die AAD-Mandanten-ID. Die GUID finden Sie im Azure-Portal im Popupmenü „Endpunkte“.

4. Erteilen Sie Berechtigungen in Form von Gruppenmitgliedschaftsansprüchen. Vergewissern Sie sich, dass die AAD-Anwendungsmanifestdatei Folgendes enthält:

	„groupMembershipClaims“: „All“ (der Standardwert ist NULL)

5. Legen Sie beim Erstellen von Einschränkungsanforderungen einen ordnungsgemäßen „TokenType“ fest.

		objTokenRestrictionTemplate.TokenType = TokenType.JWT;

	Da zusätzlich zu SWT (ACS) Unterstützung für JWT (AAD) hinzugefügt wird, ist „TokenType.JWT“ der standardmäßige „TokenType“. Wenn Sie SWT/ACS verwenden, muss die Festlegung auf „TokenType.SWT“ erfolgen.

## Zusätzliche Themen für die Implementierung
Als Nächstes erörtern wir weitere Themen in unserem Entwurf und unserer Implementierung.

###HTTP oder HTTPS?

Die ASP.NET MVC-Player-Anwendung, die wir entwickeln, muss Folgendes unterstützen:

1. Benutzerauthentifizierung über Azure AD, die unter HTTPS erfolgen muss
1. Austausch von JWT-Token zwischen Client und Azure AD, der unter HTTPS erfolgen muss
1. DRM-Lizenzerwerb durch den Client, der unter HTTPS erforderlich ist, wenn Lizenzen von Azure Media Services bereitgestellt werden. Natürlich ist HTTPS für die Lizenzbereitstellung bei der PlayReady-Produktsuite nicht zwingend vorgegeben. Wenn Ihr PlayReady-Lizenzserver sich außerhalb der Azure Media Services befindet, kann entweder HTTP oder HTTPS verwendet werden.

Daher wird empfohlen, dass die ASP.NET-Player-Anwendung HTTPS verwendet. Dies bedeutet, dass sich der Azure Media Player auf einer Seite unter HTTPS befindet. Für das Streaming ziehen wir dagegen HTTP vor, weshalb wir Probleme aufgrund gemischter Inhalte berücksichtigen müssen.

1. Der Browser lässt keine gemischten Inhalte zu. Aber Plug-Ins wie Silverlight und OSMF für Smooth Streaming und DASH lassen dies zu. Gemischte Inhalte sind ein Sicherheitsproblem. Der Grund ist eine Bedrohung durch die Möglichkeit des Einfügens von böswilligem JS-Code, wodurch Kundendaten gefährdet werden können. Browser blockieren solchen Code standardmäßig, weshalb bisher die einzige Umgehung darin besteht, auf Server- bzw. Ursprungsseite alle Domänen zuzulassen (egal ob HTTPS oder HTTP). Dies ist aber wahrscheinlich auch nicht sehr sinnvoll.
1. Am besten vermeiden Sie gemischte Inhalte und verwenden entweder HTTP oder HTTPS. Wenn Sie gemischte Inhalte wiedergeben, erfordert die Smooth Streaming-Technologie von Silverlight das Deaktivieren einer Warnung zu gemischten Inhalten. Smooth Streaming-Technologie von Adobe Flash verarbeitet gemischte Inhalte hingegen ohne entsprechende Warnung.
1. Wenn Ihr Streaming-Endpunkt vor August 2014 erstellt wurde, wird HTTPS nicht unterstützt. Erstellen und verwenden Sie in diesem Fall einen neuen Streaming-Endpunkt für HTTPS.

In der Referenzimplementierung für durch DRM geschützte Inhalte erfolgen die Ausführung der Anwendung und des Streamings unter HTTPS. Für offene Inhalte erfordert der Player keine Authentifizierung oder Lizenz, sodass Sie nach Wunsch HTTP oder HTTPS verwenden können.

### Azure Active Directory-Rollover von Signaturschlüsseln

Dies ist ein wichtiger Punkt, der bei Ihrer Implementierung berücksichtigt werden muss. Falls Sie dies nicht tun, funktioniert das fertige System höchstens 6 Wochen.

Azure AD befolgt den Branchenstandard zum Einrichten einer Vertrauensstellung zwischen sich selbst und Anwendungen, die Azure AD verwenden. Azure AD verwendet einen Signaturschlüssel, der aus einem Paar mit einem öffentlichen und einem privaten Schlüssel besteht. Wenn Azure AD ein Sicherheitstoken erstellt, das Informationen über den Benutzer enthält, wird dieses Token von Azure AD mithilfe des privaten Schlüssels signiert, bevor es zurück an die Anwendung gesendet wird. Um zu überprüfen, ob das Token gültig ist und tatsächlich von Azure AD stammt, muss die Anwendung die Signatur des Tokens überprüfen. Dies erfolgt mithilfe des von Azure AD verfügbar gemachten öffentlichen Schlüssels, der im Dokument mit den Verbundmetadaten des Mandanten enthalten ist. Dieser öffentliche Schlüssel (und der Signaturschlüssel, von dem er abgeleitet ist) entspricht demjenigen, der für alle Mandanten in Azure AD verwendet wird.

Detaillierte Informationen zum Azure AD-Schlüsselrollover finden Sie im Dokument: [Wichtige Informationen zum Signaturschlüsselrollover in Azure AD](http://msdn.microsoft.com/library/azure/dn641920.aspx/).

Im [Schlüsselpaar aus öffentlichem und privatem Schlüssel](https://login.windows.net/common/discovery/keys/)

- wird der private Schlüssel von Azure Active Directory verwendet, um ein JWT-Token zu generieren.
- wird der öffentliche Schlüssel von einer Anwendung wie den DRM-Lizenzbereitstellungsdiensten in AMS verwendet, um das JWT-Token zu überprüfen.
 
Aus Sicherheitsgründen wird dieses Zertifikat in regelmäßigen Abständen (alle 6 Wochen) von Azure Active Directory geändert. Im Fall von Sicherheitslücken kann das Schlüsselrollover jederzeit erfolgen. Daher müssen die Lizenzbereitstellungsdienste in AMS den verwendeten öffentlichen Schlüssel aktualisieren, sobald Azure AD das Schlüsselpaar ändert. Andernfalls misslingt die Tokenauthentifizierung in AMS, sodass keine Lizenz ausgestellt wird.

Dies wird erreicht, indem „TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument“ bei der Konfiguration der DRM-Lizenzbereitstellungsdienste festgelegt wird.

Der Fluss des JWT-Tokens ist wie folgt:

1.	Azure AD stellt das JWT-Token mit dem aktuellen privaten Schlüssel für einen authentifizierten Benutzer aus.
2.	Wenn ein Player mit CENC und mehreren DRM-Systemen geschützte Inhalte erkennt, wird zuerst das von Azure AD ausgestellte JWT-Token gesucht.
3.	Der Player sendet die Lizenzerwerbsanforderung mit dem JWT-Token an die Lizenzbereitstellungsdienste in AMS.
4.	Die Lizenzbereitstellungsdienste in AMS verwenden den aktuell gültigen öffentlichen Schlüssel von Azure AD , um das JWT-Token zu überprüfen, bevor Lizenzen ausgestellt werden.

Die DRM-Lizenzbereitstellungsdienste führen stets eine Suche nach dem aktuell gültigen öffentlichen Schlüssel von Azure AD durch. Der von Azure AD angebotene öffentliche Schlüssel dient als Schlüssel zum Überprüfen eines von Azure AD ausgestellten JWT-Tokens.

Was geschieht, wenn das Schlüsselrollover geschieht, nachdem AAD ein JWT-Token erstellt hat, jedoch bevor das JWT-Token von Playern an DRM-Lizenzbereitstellungsdienste in AMS zur Überprüfung gesendet wird?

Da ein Schlüssel sich jederzeit ändern kann, steht im Dokument mit den Verbundmetadaten immer mehr als ein gültiger öffentlicher Schlüssel zur Verfügung. Die Azure Media Services-Lizenzbereitstellung kann einen der im Dokument angegebenen Schlüssel verwenden, da ein Schlüssel bald geändert werden kann, ein anderer dessen Ersatz sein kann usw.

### Wo befindet sich das Zugriffstoken?

Wenn Sie sich unter [Anwendungsidentität mit OAuth 2.0-Clientanmeldeinformationen](active-directory-authentication-scenarios.md#web-application-to-web-api) ansehen, wie eine Web-App eine API-App aufruft, ist der Ablauf der Authentifizierung wie folgt:

1.	Ein Benutzer ist in der Webanwendung bei Azure AD angemeldet (siehe den Abschnitt [Webbrowser zu Webanwendung](active-directory-authentication-scenarios.md#web-browser-to-web-application)).
2.	Der Azure AD-Autorisierungsendpunkt leitet den Benutzer-Agent mit einem Autorisierungscode zurück zur Clientanwendung. Der Benutzer-Agent gibt den Autorisierungscode an den Umleitungs-URI der Clientanwendung zurück.
3.	Die Webanwendung muss ein Zugriffstoken abrufen, damit sie sich gegenüber der Web-API authentifizieren und die gewünschte Ressource abrufen kann. Sie sendet eine Anfrage an den Token-Endpunkt von Azure AD. Diese Anfrage enthält die Anmeldeinformationen, die Client-ID und den Anwendungs-ID-URI der Web-API. Sie legt den Autorisierungscode vor, um zu belegen, dass der Benutzer zugestimmt hat.
4.	Azure AD authentifiziert die Anwendung und gibt ein JWT-Zugriffstoken zurück, das zum Aufrufen der Web-API verwendet wird.
5.	Die Webanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.

In diesem anwendungsidentitätsbezogenen Datenfluss vertraut die Web-API darauf, dass die Webanwendung den Benutzer authentifiziert hat. Aus diesem Grund wird dieses Modell als vertrauenswürdiges Subsystem bezeichnet. Das [Diagramm auf dieser Seite](http://msdn.microsoft.com/library/azure/dn645542.aspx/) beschreibt, wie der Datenfluss zum Gewähren des Autorisierungscodes aussieht.

Beim Lizenzerwerb mit Tokeneinschränkung folgen wir demselben vertrauenswürdigen Subsystemmuster. Der Lizenzbereitstellungsdienst in Azure Media Services ist die Web-API-Ressource, d. h. die „Back-End-Ressource“, auf die eine Webanwendung zugreifen muss. Wo befindet sich also das Zugriffstoken?

In der Tat beziehen wir das Zugriffstoken von Azure AD. Nach erfolgreicher Benutzerauthentifizierung wird der Autorisierungscode zurückgegeben. Der Autorisierungscode wird anschließend zusammen mit der Client-ID und dem App-Schlüssel zum Austauschen des Zugriffstokens verwendet. Das Zugriffstoken dient für den Zugriff auf eine „Zeigeranwendung“, die die Azure Media Services-Lizenzbereitstellungsdienste darstellt bzw. auf diese zeigt.

Über die folgenden Schritte müssen wir die „Zeiger“-App in Azure AD registrieren und konfigurieren:

1.	Gehen Sie im Azure AD-Mandanten so vor:

	- Fügen Sie eine Anwendung (Ressource) mit einer Anmelde-URL: 

	https://[resource_name].azurewebsites.net/ und

	- App-ID-URL hinzu: 
	
	https://[aad_tenant_name].onmicrosoft.com/[resource_name]; 
2.	Fügen Sie einen neuen Schlüssel für die Ressourcen-App hinzu.
3.	Aktualisieren Sie die Manifestdatei der App so, dass die „groupMembershipClaims“-Eigenschaft den folgenden Wert hat: „groupMembershipClaims“: „All“.  
4.	Fügen Sie in der Azure AD-App, die auf die Player-Web-App zeigt, im Abschnitt „Berechtigungen für andere Anwendungen“ die Ressourcen-App hinzu, die zuvor in Schritt 1 hinzugefügt wurde. Aktivieren Sie unter „Berechtigungen der Stellvertretung“ das Kontrollkästchen „Auf [Ressourcenname] zugreifen“. Dadurch erhält die Web-App die Berechtigung zum Erstellen von Zugriffstoken für den Zugriff auf die Ressourcen-App. Dies muss für die lokale und bereitgestellte Version der Web-App erfolgen, wenn Sie mit Visual Studio und einer Azure-Web-App entwickeln.
	
Daher ist das von Azure AD ausgestellte JWT-Token tatsächlich das Zugriffstoken für den Zugriff auf diese „Zeiger“-Ressource.

### Was gilt für Livestreaming?

Bislang lag unser Schwerpunkt auf bedarfsabhängigen Assets. Was gilt für Livestreaming?

Die gute Nachricht ist, dass Sie genau den gleichen Entwurf und die gleiche Implementierung zum Schutz von Livestreaming in Azure Media Services verwenden können, indem Sie das Asset, das einem Programm zugeordnet ist, als „VOD-Asset“ (Video on Demand) behandeln.

Es ist bekannt, dass Sie für Livestreaming in Azure Media Services erst einen Kanal und dann ein Programm in diesem Kanal erstellen müssen. Um das Programm zu erstellen, müssen Sie ein Asset erstellen, das das Livearchiv für das Programm enthält. Um Live-Inhalte mit CENC und mehreren DRM-Systemen zu schützen, müssen Sie vor dem Start des Programms dieselben Einrichtungs- und Verarbeitungsschritte wie bei einem „VOD-Asset“ anwenden.

### Was gilt für Lizenzserver außerhalb von Azure Media Services?

Kunden haben möglicherweise in eine Lizenzserverfarm investiert, die sich entweder in ihrem eigenen Rechenzentrum befindet oder von DRM-Dienstanbietern gehostet wird. Glücklicherweise lässt Azure Media Services Content Protection einen Betrieb im Hybridmodus zu. Dabei werden Inhalte in Azure Media Services gehostet und dynamisch geschützt, während die DRM-Lizenzen von Servern außerhalb von Azure Media Services bereitgestellt werden. In diesem Fall müssen die folgenden Änderungen berücksichtigt werden:

1. Der Secure Token Service (STS) muss Token ausstellen, die zulässig sind und von der Lizenzserverfarm überprüft werden können. Beispielsweise erfordern die von Axinom bereitgestellten Widevine-Lizenzserver ein spezifisches JWT-Token, das „entitlement message“ enthält. Aus diesem Grund benötigen Sie einen STS zum Ausstellen solcher JWT-Token. Die Entwickler haben diese Implementierung abgeschlossen. Informationen finden Sie im folgenden Dokument im [Azure-Dokumentationscenter](https://azure.microsoft.com/documentation/): [Bereitstellen von Widevine-Lizenzen für Azure Media Services mithilfe von Axinom](media-services-axinom-integration.md). 
1. Sie müssen den Lizenzbereitstellungsdienst (ContentKeyAuthorizationPolicy) nicht mehr in Azure Media Services konfigurieren. Sie müssen die Lizenzerwerbs-URLs (für PlayReady, Widevine und FairPlay) bei der Konfiguration von „AssetDeliveryPolicy“ während der Einrichtung von CENC mit mehreren DRM-Systemen bereitstellen.
 
### Was geschieht, wenn ich einen benutzerdefinierten STS verwenden möchte?

Kunden können verschiedene Gründe für das Nutzen eines benutzerdefinierten STS (Secure Token Service) für die Bereitstellung von JWT-Token haben. Einige davon sind die folgenden:

1.	Der vom Kunden verwendete Identitätsanbieter unterstützt nicht den STS. In diesem Fall ist ein benutzerdefinierter STS möglicherweise eine Option.
2.	Der Kunde benötigt möglicherweise eine flexiblere oder strengere Kontrolle bei der Integration des STS in das Abrechnungssystem des Kunden für Abonnenten. Ein MVPD-Anbieter bietet ggf. mehrere OTT-Abopakete wie z. B. Premium, Standard, Sport usw. an. Der Programmanbieter möchte ggf. die Ansprüche in einem Token mit einem Paket eines Abonnenten abgleichen, sodass nur der Inhalt im entsprechenden Paket zur Verfügung gestellt wird. In diesem Fall bietet ein benutzerdefinierter STS die erforderliche Flexibilität und Kontrolle.

Zwei Änderungen müssen erfolgen, wenn Sie einen benutzerdefinierten STS verwenden:

1.	Wenn Sie den Lizenzbereitstellungsdienst für ein Asset konfigurieren, müssen Sie den vom benutzerdefinierten STS zu überprüfenden Sicherheitsschlüssel (weitere Details folgen) anstelle des aktuellen Schlüssels von Azure Active Directory angeben.
2.	Beim Generieren eines JTW-Tokens wird ein Sicherheitsschlüssel anstelle des private Schlüssels des aktuellen X.509-Zertifikats in Azure Active Directory angegeben.

Es gibt zwei Arten von Sicherheitsschlüsseln:

1.	Symmetrischer Schlüssel: Zum Generieren und Überprüfen eines JWT-Tokens wird der gleiche Schlüssel verwendet.
2.	Asymmetrischer Schlüssel: Ein Paar aus öffentlichem und privatem Schlüssel in einem X.509-Zertifikat wird mit einem privaten Schlüssel zum Verschlüsseln/Generieren eines JWT-Tokens und der öffentlichen Schlüssel zum Überprüfen des Tokens verwendet.

####Technische Hinweise

Bei Verwendung von .NET Framework/C# als Entwicklungsplattform muss das X.509-Zertifikat des asymmetrischen Schlüssels eine Schlüssellänge von mindestens 2048 aufweisen. Dies ist eine Voraussetzung für die „System.IdentityModel.Tokens.X509AsymmetricSecurityKey“--Klasse in .NET Framework. Andernfalls wird die folgende Ausnahme ausgelöst:

IDX10630: „System.IdentityModel.Tokens.X509AsymmetricSecurityKey“ für die Signierung darf nicht kleiner als 2048 Bits sein.

## Das fertige System und Tests

Wir durchlaufen verschiedene Szenarien im fertigen System, damit sich die Leser ein grundlegendes Bild des Verhaltens machen können, bevor sie ein Anmeldekonto erhalten.

Die Webanwendung des Players und die Anmeldung finden Sie [hier](https://openidconnectweb.azurewebsites.net/).

Wenn Sie ein „nicht integriertes“ Szenario benötigen, bei dem Videoassets in Azure Media Services entweder nicht oder durch DRM geschützt ohne Tokenauthentifizierung gehostet werden (und eine Lizenz für alle Anforderer ausgestellt wird), können Sie einen Test ohne Anmeldung ausführen (indem Sie auf HTTP umschalten, wenn Ihr Videostreaming über HTTP erfolgt).

Wenn Sie ein durchgängig integriertes Szenario brauchen, bei dem sich Videoassets unter dynamischem DRM-Schutz in Azure Media Services mit Tokenauthentifizierung befinden und JWT-Token von Azure AD generiert werden, müssen Sie sich anmelden.

### Benutzeranmeldung

Um das durchgängig integrierte DRM-System testen zu können, müssen Sie ein Konto erstellen oder hinzufügen.

Welches Konto wird benötigt?

Azure erlaubte ursprünglich nur den Zugriff durch Benutzer mit Microsoft-Konten. Inzwischen ist jedoch der Zugriff aus beiden Systemen zulässig. Dies wurde erreicht, indem alle Azure-Eigenschaften Azure AD für die Authentifizierung vertrauen, Azure AD Organisationsbenutzer authentifiziert und eine Verbundbeziehung erstellt wurde, in der Azure AD dem Privatnutzeridentitätssystem für Microsoft-Konten vertraut, um Privatnutzer zu authentifizieren. Daher kann Azure AD Microsoft-"Gastkonten" ebenso wie "systemeigene" Azure AD-Konten authentifizieren.

Da Azure AD der MSA-Kontendomäne (MSA) vertraut, können Sie beliebige Konten aus den folgenden Domänen dem benutzerdefinierten Azure AD-Mandanten hinzufügen und das Konto für die Anmeldung verwenden:

**Domänenname**|**Domäne**
-----------|----------
**Benutzerdefinierte Azure AD-Mandantendomäne**|somename.onmicrosoft.com
**Unternehmensdomäne**|microsoft.com
**Microsoft-Kontendomäne (MSA)**|outlook.com, live.com, hotmail.com

Sie können einen der Anbieter kontaktieren und bitten, ein Konto für Sie zu erstellen oder hinzuzufügen.

Nachstehend sehen Sie Screenshots verschiedener Anmeldeseiten, die von verschiedenen Domänenkonten verwendet werden.

**Benutzerdefiniertes Azure AD-Mandantendomänenkonto**: In diesem Fall sehen Sie die angepasste Anmeldeseite der benutzerdefinierten Azure AD-Mandantendomäne.

![Benutzerdefiniertes Azure AD-Mandantendomänenkonto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-Domänenkonto mit Smartcard**: In diesem Fall sehen Sie die Anmeldeseite, die von der IT-Abteilung von Microsoft mit einer zweistufigen Authentifizierung angepasst wurde.

![Benutzerdefiniertes Azure AD-Mandantendomänenkonto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-Konto (MSA)**: In diesem Fall sehen Sie die Anmeldeseite des Microsoft-Kontos für Endkunden.

![Benutzerdefiniertes Azure AD-Mandantendomänenkonto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### Verwenden von Encrypted Media Extensions für PlayReady

In einem modernen Browser mit Encrypted Media Extensions (EME) für die PlayReady-Unterstützung, z. B. Internet Explorer 11 unter Windows 8.1 und höher sowie Microsoft Edge unter Windows 10, ist PlayReady das zugrunde liegende DRM-System für EME.

![Verwenden von EME für PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Der dunkle Playerbereich ist darauf zurückzuführen, dass der PlayReady-Schutz verhindert, dass eine Bildschirmaufnahme von geschütztem Video erstellt wird.

Der folgende Bildschirm zeigt die Player-Plug-Ins und die Unterstützung von MSE/EME.

![Verwenden von EME für PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME in Microsoft Edge und Internet Explorer 11 unter Windows 10 erlauben den Aufruf von [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) auf Windows 10-Geräten, die sie unterstützen. PlayReady SL3000 entsperrt den Fluss der verbesserten Premium-Inhalte (4K, HDR usw.) und neuen Modellen für die Inhaltsübermittlung (frühes Fenster für erweiterte Inhalte).

Was die Windows-Geräte angeht: PlayReady ist das einzige DRM in der Hardware, das auf Windows-Geräten verfügbar ist (PlayReady SL3000). Ein Streamingdienst kann PlayReady über EME oder über eine UWP-Anwendung nutzen und mit PlayReady SL3000 eine höhere Videoqualität als ein anderes DRM bieten. In der Regel werden 2K-Inhalte über Chrome oder Firefox und 4K-Inhalte über Microsoft Edge/IE11 oder eine UWP-Anwendung auf dem gleichen Gerät gesendet (abhängig von Diensteinstellungen und Implementierung).


#### Verwenden von EME für Widevine

In einem modernen Browser mit EME/Widevine-Unterstützung, z. B. Chrome 41 und höher unter Windows 10, Windows 8.1, Mac OSX Yosemite und Chrome unter Android 4.4.4, ist Google Widevine das DRM-System hinter EME.

![Verwenden von EME für Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Beachten Sie, dass Widevine das Erstellen einer Bildschirmaufnahme von geschütztem Video nicht verhindert.

![Verwenden von EME für Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### Nicht berechtigte Benutzer

Wenn ein Benutzer nicht Mitglied der Gruppe „Berechtigte Benutzer“ ist, kann der Benutzer nicht die Berechtigungsüberprüfung bestehen, weshalb der Lizenzdienst für mehrere DRM-Systeme das Ausstellen der angeforderten Lizenz (wie nachstehend gezeigt) verweigert. Die ausführliche Beschreibung ist „Lizenzerwerb nicht erfolgreich“, die wie vorgesehen angezeigt wird.

![Nicht berechtigte Benutzer](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### Ausführen eines benutzerdefinierten Sicherheitstokendiensts

Bei einem Szenario mit einem benutzerdefinierten STS (Secure Token Service, Sicherheitstokendienst) wird das JWT-Token vom benutzerdefinierten STS entweder mithilfe eines symmetrischen oder asymmetrischen Schlüssels ausgestellt.

Symmetrischer Schlüssel (mit Chrome):

![Ausführen eines benutzerdefinierten STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Asymmetrischer Schlüssel über ein X.509-Zertifikat (mit modernem Microsoft-Browser).

![Ausführen eines benutzerdefinierten STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

In beiden genannten Fällen erfolgt die Benutzerauthentifizierung über Azure AD. Der einzige Unterschied ist, dass JWT-Token vom benutzerdefinierten STS anstatt von Azure AD ausgegeben werden. Beim Konfigurieren des dynamischen CENC-Schutzes bestimmt freilich die Beschränkung des Lizenzbereitstellungsdiensts den Typ des JWT-Tokens als entweder symmetrischen oder asymmetrischen Schlüssel.

## Zusammenfassung

In diesem Dokument haben wir uns mit CENC mit mehreren systemeigenen DRM-Systemen und einer Zugriffssteuerung über eine Tokenauthentifizierung sowie dem entsprechenden Entwurf und seiner Implementierung mithilfe von Azure, Azure Media Services und Azure Media Player beschäftigt.

- Der vorgestellte Referenzentwurf enthält alle in einem DRM-/CENC-Subsystem benötigten Komponenten.
- Ferner wurde eine Referenzimplementierung in Azure, Azure Media Services und Azure Media Player beschrieben.
- Darüber hinaus wurden verschiedene Aspekte mit direktem Bezug auf Entwurf und Implementierung behandelt.


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###Danksagungen 

William Zhang, Mingfei Yan, Roland Le Franc, Kilroy Hughes, Julia Kornich

<!---HONumber=AcomDC_0204_2016-->