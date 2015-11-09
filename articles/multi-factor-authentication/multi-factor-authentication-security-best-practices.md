<properties 
	pageTitle="Bewährten Methoden für die Sicherheit bei Verwendung von MFA" 
	description="In diesem Dokument sind bewährte Methoden bei Verwendung von Azure MFA mit Azure-Konten beschrieben" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015" 
	ms.author="billmath"/>

# Bewährte Sicherheitsmethoden beim Einsatz von Azure Multi-Factor Authentication mit Azure AD-Konten

Im Hinblick auf die Verbesserung des Authentifizierungsprozesses ist die mehrstufige Authentifizierung für die meisten Organisationen die Option der Wahl. Azure MFA (Multi-Factor Authentication) ermöglicht es Unternehmen, ihre Sicherheits- und Complianceanforderungen zu erfüllen und gleichzeitig einen benutzerfreundlichen Anmeldevorgang bereitzustellen. In diesem Artikel werden verschiedene Best Practices erläutert, die Sie beim Planen einer Einführung von Azure MFA berücksichtigen sollten.

## Best Practices für Azure Multi-Factor Authentication in der Cloud
Damit alle Benutzer die mehrstufige Authentifizierung nutzen und von den erweiterten Features von Azure Multi-Factor Authentication profitieren können, müssen Sie Azure MFA für alle Benutzer aktivieren. Hierzu benötigen Sie eine der folgenden Komponenten:

- Azure AD Premium oder Enterprise Mobility Suite 
- Multi-Factor Authentication-Anbieter

### Azure AD Premium/Enterprise Mobility Suite

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Um Azure MFA unter Verwendung von Azure AD Premium oder Enterprise Mobility Suite in der Cloud zu nutzen, sollten Sie Ihren Benutzern zunächst Lizenzen zuweisen. Da Azure Multi-Factor Authentication Teil dieser Suites sind, benötigt Ihr Unternehmen keine weiteren Komponenten, um die Multi-Factor Authentication-Funktion für alle Benutzer zu implementieren.
 
Berücksichtigen Sie bei der Einrichtung von Multi-Factor Authentication folgende Aspekte:

- Sie müssen keinen Multi-Factor Authentication-Anbieter erstellen. Azure Multi-Factor Authentication ist in Azure AD Premium und Enterprise Mobility Suite bereits enthalten. Wenn Sie einen Authentifizierungsanbieter erstellen, fallen möglicherweise doppelte Kosten an.
- Azure AD Connect ist nur erforderlich, wenn Sie Ihre lokale Active Directory-Umgebung mit einem Azure AD-Verzeichnis synchronisieren. Wenn Sie lediglich ein Azure AD-Verzeichnis verwenden, das nicht mit einer lokalen Active Directory-Instanz synchronisiert wird, ist Azure AD Connect nicht erforderlich.


### Multi-Factor Authentication-Anbieter

![Multi-Factor Authentication-Anbieter](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Wenn Sie nicht über Azure AD Premium oder Enterprise Mobility Suite verfügen, sollten Sie zum Implementieren von Azure MFA in der Cloud zunächst einen MFA-Authentifizierungsanbieter erstellen. Wenngleich MFA standardmäßig für globale Administratoren mit Azure Active Directory verfügbar ist, muss die Multi-Factor Authentication-Funktion auf alle Benutzer angewendet werden, wenn Sie MFA für Ihr Unternehmen bereitstellen. Zu diesem Zweck ist ein Multi-Factor Authentication-Anbieter erforderlich. Bei der Auswahl des Authentifizierungsanbieters müssen Sie ein Verzeichnis auswählen und Folgendes berücksichtigen:

- Zum Erstellen eines Anbieters für mehrstufige Authentifizierung benötigen Sie kein Azure AD-Verzeichnis. 
- Wenn Sie Multi-Factor Authentication jedoch für alle Benutzer verfügbar machen möchten und/oder möchten, dass die globalen Administratoren Features wie das Verwaltungsportal, benutzerdefinierte Grußformeln und Berichte nutzen können, müssen Sie den Multi-Factor Authentication-Anbieter einem Azure AD-Verzeichnis zuordnen.
- DirSync oder AAD ist nur erforderlich, wenn Sie Ihre lokale Active Directory-Umgebung mit einem Azure AD-Verzeichnis synchronisieren. Wenn Sie nur ein Azure AD-Verzeichnis verwenden, das nicht mit einer lokalen Instanz von Active Directory synchronisiert ist, benötigen Sie weder DirSync noch AAD Sync.
- Wenn Sie über Azure AD Premium oder die Enterprise Mobility Suite verfügen, müssen Sie keinen Anbieter für mehrstufige Authentifizierung erstellen. Sie müssen einem Benutzer lediglich eine Lizenz zuweisen und können MFA anschließend für Benutzer aktivieren.
- Stellen Sie sicher, dass Sie das richtige Nutzungsmodell für Ihr Unternehmen auswählen (pro Authentifizierung oder pro aktiviertem Benutzer). Das Nutzungsmodell kann anschließend nicht mehr geändert werden.

### Benutzerkonto
Wenn Sie MFA für Ihre Benutzer aktivieren, können Benutzerkonten einen von drei Status aufweisen: deaktiviert, aktiviert oder erzwungen. Befolgen Sie die unten stehenden Richtlinien, um sicherzustellen, dass Sie die am besten geeignete Option für Ihre Bereitstellung verwenden:

- Wenn der Status eines Benutzers auf „Deaktiviert“ festgelegt ist, verwendet dieser Benutzer die Multi-Factor Authentication-Funktion nicht. Dies ist die Standardeinstellung.
- Wenn der Status eines Benutzers auf „Aktiviert“ festgelegt ist, kann der Benutzer Multi-Factor Authentication nutzen, hat den Registrierungsprozess jedoch noch nicht abgeschlossen. Er wird beim nächsten Anmelden aufgefordert, den Vorgang abzuschließen. Diese Einstellung hat keine Auswirkungen auf nicht browserbasierte Apps. Alle Apps werden weiterhin ausgeführt, bis die Registrierung abgeschlossen ist.
- Wenn der Status eines Benutzers auf „Erzwungen“ festgelegt ist, hat er die Registrierung möglicherweise abgeschlossen, dies ist jedoch nicht unbedingt der Fall. Wenn er die Registrierung abgeschlossen hat, verwendet er anschließend Multi-Factor Authentication. Anderenfalls wird der Benutzer beim nächsten Anmelden aufgefordert, die Registrierung abzuschließen. Diese Einstellung hat Auswirkungen auf nicht browserbasierte Apps. Diese Apps funktionieren erst, wenn App-Kennwörter erstellt und verwendet werden.
- Verwenden Sie die Vorlage für Benutzerbenachrichtigungen im Artikel [Erste Schritte mit Azure Multi-Factor Authentication in der Cloud](multi-factor-authentication-get-started-cloud.md), um eine E-Mail mit Informationen zur MFA-Implementierung an Ihre Benutzer zu senden.

### Unterstützungsmöglichkeiten

Die meisten Benutzer sind es gewöhnt, für die Authentifizierung lediglich Kennwörter zu verwenden. Daher ist es wichtig, dass Ihr Unternehmen das Bewusstsein für diesen Prozess bei sämtlichen Benutzern fördert. Durch dieses Bewusstsein werden Anrufe beim Helpdesk aufgrund kleinerer Probleme im Zusammenhang mit MFA vermieden. In einigen Szenarien muss MFA jedoch vorübergehend deaktiviert werden. Befolgen Sie die unten stehenden Richtlinien für den Umgang mit diesen Szenarien:

- Stellen Sie sicher, dass Ihre technischen Supportmitarbeiter für Szenarien geschult sind, in denen mobile Apps oder Mobiltelefone keine Nachrichten oder Anrufe empfangen, sodass sich der Benutzer nicht anmelden kann. Es kann eine Einmalumgehung aktiviert werden, damit sich der Benutzer einmalig anmelden kann, indem er Multi-Factor Authentication umgeht. Die Umgehung ist vorübergehend und läuft nach der angegebenen Anzahl von Sekunden ab. 
- Bei Bedarf können Sie die Funktion für vertrauenswürdige IPs in Azure MFA nutzen. Mit diesem Feature können Administratoren eines verwalteten Mandanten oder Partnerverbundmandanten Multi-Factor Authentication für Benutzer umgehen, die sich über das lokale Intranet des Unternehmens anmelden. Die Funktionen sind für Azure AD-Mandanten verfügbar, die Azure AD Premium, Enterprise Mobility Suite oder Azure Multi-Factor Authentication-Lizenzen verfügen.


## Bewährte Methoden für lokale Azure Multi-Factor Authentication-Implementierungen
Wenn Ihr Unternehmen die eigene Infrastruktur für die Implementierung von MFA nutzen möchte, muss ein lokaler Azure Multi-Factor Authentication-Server bereitgestellt werden. In der Abbildung unten sind die MFA-Serverkomponenten dargestellt:

![Multi-Factor Authentication-Anbieter](./media/multi-factor-authentication-security-best-practices/server.png) *Standardmäßig nicht installiert **Standardmäßig installiert, jedoch nicht aktiviert


Der Azure Multi-Factor Authentication-Server kann zum Schützen von Cloudressourcen und lokalen Ressourcen eingesetzt werden, auf die über Azure AD-Konten zugegriffen wird. Zu diesem Zweck ist jedoch ein Partnerverbund erforderlich. Das heißt, dass Sie über AD FS verfügen und für die Verbunddienste einen Partnerverbund mit Ihrem Azure AD-Mandanten konfiguriert haben müssen. Berücksichtigen Sie bei der Einrichtung des Multi-Factor Authentication-Servers Folgendes:

- Wenn Sie Azure AD-Ressourcen unter Verwendung von Active Directory-Verbunddiensten schützen, erfolgt die erste Stufe der Authentifizierung lokal mithilfe von AD FS. Die zweite Stufe erfolgt lokal mithilfe des Anspruchs.
- Es ist nicht erforderlich, den Azure Multi-Factor Authentication-Server auf dem AD FS-Verbundserver zu installieren. Der Multi-Factor Authentication-Adapter für AD FS muss jedoch auf einem Server mit Windows Server 2012 R2 installiert werden, auf dem AD FS ausgeführt wird. Sie können den Server auf einem anderen Computer installieren, sofern es sich um eine unterstützte Version handelt, und den AD FS-Adapter separat auf dem AD FS-Verbundserver installieren. Im folgenden Verfahren finden Sie Anweisungen, wie Sie den Adapter separat installieren.
- Der Installations-Assistent für den Multi-Factor Authentication AD FS-Adapter erstellt eine Sicherheitsgruppe namens "PhoneFactor Admins" in Active Directory und fügt dieser Gruppe dann das AD FS-Dienstkonto des Verbunddiensts hinzu. Es wird empfohlen, dass Sie auf dem Domänencontroller überprüfen, ob die Gruppe "PhoneFactor Admins" tatsächlich erstellt wurde und ob das AD FS-Dienstkonto Mitglied dieser Gruppe ist. Bei Bedarf fügen Sie das AD FS-Dienstkonto der Gruppe "PhoneFactor Admins" auf dem Domänencontroller manuell hinzu.

### Benutzerportal
Dieses Portal wird über eine IIS-Website (Internetinformationsdienste) ausgeführt, die Self-Service-Funktionen sowie umfangreiche Verwaltungsfunktionen für Benutzer bietet. Befolgen Sie die nachfolgenden Richtlinien, um diese Komponente zu konfigurieren:

- IIS 6 oder höher erforderlich
- ASP.NET v2.0.507207 muss installiert und registriert sein
- Dieser Server kann in einem Umkreisnetzwerk bereitgestellt werden.



### App-Kennwörter
Wenn für Ihr Unternehmen ein SSO-Verbund mit Azure AD konfiguriert ist und Sie Multi-Factor Authentication verwenden möchten, sollten Sie bei Verwendung von App-Kennwörtern Folgendes berücksichtigen (beachten Sie, dass dies nur bei Verwendung von SSO in einer Verbundkonfiguration gilt):

- Das App-Kennwort wird von Azure AD überprüft, sodass der Partnerverbund umgangen wird. Der Partnerverbund wird nur beim Einrichten des App-Kennworts verwendet.
- Die Kennwörter der SSO-Verbundbenutzer werden in der Organisations-ID gespeichert. Wenn der Benutzer das Unternehmen verlässt, muss diese Information zur Organisations-ID übertragen werden, und zwar mithilfe von DirSync in Echtzeit. Das Deaktivieren oder Löschen von Konten kann bis zu drei Stunden bei der Synchronisierung dauern, sodass das Deaktivieren bzw. Löschen des App-Kennworts verzögert wird.
- Lokale Einstellungen für die Clientzugriffssteuerung werden vom App-Kennwort nicht berücksichtigt.
- Für das App-Kennwort ist keine lokale Authentifizierung-Protokollierung / -Überwachungsfunktion verfügbar.
- Weitere Schulungen für Endbenutzer sind für den Microsoft Lync 2013-Client erforderlich. 
- Bestimmte erweiterte Architekturentwürfe erfordert möglicherweise, dass eine Kombination aus Organisationsbenutzername und Kennwörter sowie App-Kennwörter bei der Verwendung der Multi-Factor Authentication mit Clients erforderlich ist, je nachdem, wo die Authentifizierung stattfindet. Bei Clients, die sich gegenüber einer lokalen Infrastruktur authentifizieren, verwenden Sie einen Organisationsbenutzernamen und ein Organisationskennwort. Für Clients, die bei Azure AD authentifizieren, verwenden Sie das App-Kennwort.
- Benutzer können standardmäßig keine App-Kennwörter erstellen. Sollte dies in Ihrem Unternehmen oder in bestimmten Szenarien erforderlich sein, müssen Sie sicherstellen, dass die Option „Benutzern das Erstellen von App-Kennwörtern zum Anmelden bei nicht browserbasierten Apps gestatten“ ausgewählt ist.

## Weitere Überlegungen
In der nachfolgenden Liste sind zusätzliche Überlegungen und bewährte Methoden für die einzelnen Komponenten aufgeführt, die lokal bereitgestellt werden:

Methode|Beschreibung
:------------- | :------------- | 
[Active Directory-Verbunddienste](multi-factor-authentication-get-started-adfs.md)|Informationen zum Einrichten von Multi-Factor Authentication mit AD FS.
[RADIUS-Authentifizierung](multi-factor-authentication-get-started-server-radius.md)| Informationen über Einrichtung und Konfiguration des Azure MFA-Servers mit RADIUS.
[IIS-Authentifizierung](multi-factor-authentication-get-started-server-iis.md)|Informationen über Einrichtung und Konfiguration des Azure MFA-Servers mit IIS.
[Windows-Authentifizierung](multi-factor-authentication-get-started-server-windows.md)| Informationen über Einrichtung und Konfiguration des Azure MFA-Servers mit Windows-Authentifizierung.
[LDAP-Authentifizierung](multi-factor-authentication-get-started-server-ldap.md)|Informationen über Einrichtung und Konfiguration des Azure MFA-Servers mit LDAP-Authentifizierung.
[Remotedesktop-Gateway und Azure Multi-Factor Authentication-Server mithilfe von RADIUS](multi-factor-authentication-get-started-server-rdg.md)| Informationen zum Einrichten und Konfigurieren des Azure MFA-Servers mit Remotedesktop-Gateway unter Verwendung von RADIUS.
[Synchronisieren mit Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)|Informationen zum Einrichten und Konfigurieren der Synchronisierung zwischen Active Directory und dem Azure MFA-Server.
[Bereitstellen des mobilen App-Webdienstes für den Azure Multi-Factor Authentication-Server](multi-factor-authentication-get-started-server-webservice.md)|Informationen über das Einrichten und Konfigurieren des Azure MFA-Server-Webdienstes.
[Erweiterte VPN-Konfiguration mit Azure Multi-Factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md)|Informationen zum Konfigurieren von Cisco ASA-, Citrix Netscaler- und Juniper/Pulse-Geräten über sichere VPN-Verbindungen unter Verwendung von LDAP oder RADIUS. 


## Zusätzliche Ressourcen
In diesem Artikel sind einige bewährte Methoden für Azure MFA beschrieben. Zusätzlich sind weitere Ressourcen verfügbar, die Sie bei der Planung Ihrer MFA-Bereitstellung nutzen können. In der nachfolgenden Liste sind einige wichtige Artikel aufgeführt, die Sie bei diesen Aufgaben unterstützen können:

- [Berichte in Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
- [Einrichten von Azure Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md)
- [Azure Multi-Factor Authentication – Häufig gestellte Fragen](multi-factor-authentication-faq.md)

<!---HONumber=Nov15_HO1-->