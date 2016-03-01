<properties
	pageTitle="Ermöglichen des Hybrid-Zugriffs mit App-Proxy | Microsoft Azure"
	description="Ermöglichen Sie den Zugriff auf Apps, die in Ihrem privaten Netzwerk ausgeführt werden, von außerhalb des Netzwerks mit Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	keywords="Anwendungszugriff, App-Proxy, Hybridzugriff"
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2016"
	ms.author="femila"/>

# Ermöglichen des Hybrid-Zugriffs mit App-Proxy
Mit dem Microsoft Azure Active Directory (AD)-Anwendungsproxy können Sie Zugriff auf Anwendungen, die sich an einem sicheren Ort in Ihrem privaten Netzwerk befinden, von jedem Ort und mit jedem Gerät gewähren. Nachdem Sie einen Anwendungsproxy-Connector in Ihrer Umgebung installiert haben, können Sie ihn mit Azure AD leicht konfigurieren.

Für die Aktivierung des Zugriffs auf eine Webanwendung gibt es nur eine Anforderung: Die Webanwendung muss für den Server zugänglich sein, auf dem der Connector installiert ist. Anders ausgedrückt: Sie können den Connector entweder direkt auf dem Anwendungsserver oder auf einem beliebigen anderen Server in Ihrer Umgebung installieren, solange der Connector auf die Webanwendung zugreifen kann.

##So funktioniert's
### Kurzübersicht
1. Connectors werden im lokalen Netzwerk bereitgestellt. (Aus Redundanz- und Skalierungsgründen können mehrere Connectors bereitgestellt werden.)
2. Der Connector stellt eine Verbindung mit dem Clouddienst her.
3. Der Connector und der Clouddienst leiten Benutzerdatenverkehr an Anwendungen.

 ![AzureAD-App-Proxy-Diagramm](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

### Ausführlichere Betrachtung
1. Der Benutzer greift auf die Anwendung über den Anwendungsproxy zu und wird zur Authentifizierung an die Azure AD-Anmeldeseite geleitet.
2. Nach der erfolgreichen Anmeldung wird ein Token generiert und an den Benutzer gesendet.
3. Der Benutzer sendet das Token an den Anwendungsproxy, der den Benutzerprinzipalnamen (UPN) und den Sicherheitsprinzipalnamen (SPN) aus dem Token abruft und die Anforderung dann an den Connector leitet.
4. Der Connector fordert im Namen des Benutzers ein Kerberos-Ticket an, das für die interne Authentifizierung (Windows) verwendet werden kann. Dies wird als eingeschränkte Kerberos-Delegierung bezeichnet.
5. Ein Kerberos-Ticket wird von Active Directory abgerufen.
6. Das Ticket wird an den Anwendungsserver gesendet und überprüft.
7. Die Antwort wird über den Anwendungsproxy an den Benutzer gesendet.

## Verwandte Artikel
- [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
- [Aktivieren des Azure AD-Anwendungsproxys](active-directory-application-proxy-enable.md#step-1-enable-application-proxy-in-azure-ad)
- [Veröffentlichen von Anwendungen über den Azure AD-App-Proxy](active-directory-application-proxy-publish.md)

<!---HONumber=AcomDC_0218_2016-->