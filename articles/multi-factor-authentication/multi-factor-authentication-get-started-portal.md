<properties 
	pageTitle="Bereitstellen des Benutzerportals für den Azure Multi-Factor Authentication-Server" 
	description="Dies ist die Seite für Azure Multi-Factor Authentication, auf der die ersten Schritte mit Azure MFA und dem Benutzerportal beschrieben werden." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="billmath"/>

# Bereitstellen des Benutzerportals für den Azure Multi-Factor Authentication-Server

Im Benutzerportal kann der Administrator das Azure Multi-Factor Authentication-Benutzerportal installieren und konfigurieren. Das Benutzerportal ist eine IIS-Website, die es Benutzern ermöglicht, sich für Azure Multi-Factor Authentication zu registrieren und ihre Konten zu verwalten. Die Benutzer können ihre Telefonnummer oder PIN ändern oder bei der nächsten Anmeldung Azure Multi-Factor Authentication umgehen.

Die Benutzer melden sich mit ihrem normalen Benutzernamen und Kennwort beim Benutzerportal an und rufen entweder Azure Multi-Factor Authentication auf oder beantworten Sicherheitsfragen, um die Authentifizierung abzuschließen. Wenn die Benutzerregistrierung zulässig ist, konfiguriert der Benutzer seine Telefonnummer und PIN, wenn er sich zum ersten Mal beim Benutzerportal anmeldet.

Es können Benutzerportaladministratoren eingerichtet werden, und ihnen kann die Berechtigung zum Hinzufügen neuer Benutzer und Aktualisieren vorhandener Benutzer erteilt werden.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## Bereitstellen des Benutzerportals auf demselben Server wie der Azure Multi-Factor Authentication-Server

Die folgenden Voraussetzungen müssen erfüllt sein, um das Benutzerportal auf demselben Server wie der Azure Multi-Factor Authentication-Server zu installieren:

- IIS, einschließlich ASP.NET und IIS 6-Metabasiskompatibilität (für IIS 7 oder höher), muss installiert sein. 
- Der angemeldete Benutzer muss über Administratorrechte für den Computer und ggf. die Domäne verfügen. Der Grund dafür ist, dass das Konto Berechtigungen zum Erstellen von Active Directory-Sicherheitsgruppen benötigt.

### So stellen Sie das Benutzerportal für den Azure Multi-Factor Authentication-Server bereit

1. Klicken Sie auf dem Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol für das Benutzerportal und dann auf die Schaltfläche „Benutzerportal installieren“. 
1. Klicken Sie auf Weiter.
1. Klicken Sie auf Weiter.
1. Wenn der Computer zu einer Domäne gehört und die Active Directory-Konfiguration zum Sichern der Kommunikation zwischen dem Benutzerportal und dem Azure Multi-Factor Authentication-Dienst unvollständig ist, wird der Schritt „Active Directory“ angezeigt. Klicken Sie auf die Schaltfläche "Weiter", um diese Konfiguration automatisch abzuschließen.
1. Klicken Sie auf Weiter.
1. Klicken Sie auf Weiter.
1. Klicken Sie auf Schließen.
1. Öffnen Sie auf einem beliebigen Computer einen Webbrowser, und navigieren Sie zu der URL, unter der das Benutzerportal installiert wurde (z. B. https://www.publicwebsite.com/MultiFactorAuth). Stellen Sie sicher, dass keine Zertifikatswarnungen oder -fehler angezeigt werden.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## Bereitstellen des Azure Multi-Factor Authentication-Server-Benutzerportals auf einem anderen Server

Um die Azure Multi-Factor Authentication-App zu verwenden, müssen folgende Voraussetzungen erfüllt sein, damit die App erfolgreich mit dem Benutzerportal kommunizieren kann:

Informationen zu den Hardware- und Softwareanforderungen finden Sie unter "Hardware- und Softwareanforderungen":

- Sie müssen Version 6.0 oder höher des Azure Multi-Factor Authentication-Servers verwenden.
- Das Benutzerportal muss auf einem Webserver mit Internetzugriff installiert sein, auf dem Microsoft® Internetinformationsdienste (IIS) 6.x, 7.x oder höher ausgeführt wird.
- Stellen Sie bei Verwendung von IIS 6.x sicher, dass ASP.NET v2.0.50727 installiert, registriert und auf "Zulässig" festgelegt ist.
- Bei Verwendung von IIS 7.x oder höher erforderliche Rollendienste umfassen ASP.NET und IIS 6-Metabasiskompatibilität.
- Das Benutzerportal sollte mit einem SSL-Zertifikat gesichert sein.
- In IIS 6.x, IIS 7.x oder höher muss das Azure Multi-Factor Authentication-Webdienst-SDK auf dem Server installiert sein, auf dem der Azure Multi-Factor Authentication-Server installiert ist.
- Das Azure Multi-Factor Authentication-Webdienst-SDK muss mit einem SSL-Zertifikat gesichert sein.
- Das Benutzerportal muss eine SSL-Verbindung mit dem Azure Multi-Factor Authentication-Webdienst-SDK herstellen können.
- Das Benutzerportal muss sich beim Azure Multi-Factor Authentication-Webdienst-SDK mit den Anmeldeinformationen eines Dienstkontos, das Mitglied der Sicherheitsgruppe "PhoneFactor Admins" ist, authentifizieren können. Dieses Dienstkonto und diese Gruppe sind in Active Directory vorhanden, wenn der Azure Multi-Factor Authentication-Server auf einem in die Domäne eingebundenen Server ausgeführt wird. Dieses Dienstkonto und diese Gruppe sind lokal auf dem Azure Multi-Factor Authentication-Server vorhanden, wenn dieser nicht in eine Domäne eingebunden ist.

Zum Installieren des Benutzerportals auf einem anderen Server als dem Azure Multi-Factor Authentication-Server sind die folgenden drei Schritte erforderlich:

1. Installieren des Webdienst-SDK
2. Installieren des Benutzerportals
3. Konfigurieren der Benutzerportal-Einstellungen im Azure Multi-Factor Authentication-Server


### Installieren des Webdienst-SDK

Wenn das Azure Multi-Factor Authentication-Webdienst-SDK nicht bereits auf dem Azure Multi-Factor Authentication-Server installiert ist, wechseln Sie zu diesem Server, und öffnen Sie den Azure Multi-Factor Authentication-Server. Klicken Sie auf das Symbol des Webdienst-SDK, klicken Sie auf die Schaltfläche "Webdienst-SDK installieren", und folgen Sie den angezeigten Anweisungen. Das Webdienst-SDK muss mit einem SSL-Zertifikat gesichert sein. Ein selbst signiertes Zertifikat ist für diesen Zweck zulässig. Dieses muss jedoch in den Speicher "Vertrauenswürdige Stammzertifizierungsstellen" des lokalen Computerkontos auf dem Benutzerportal-Webserver importiert werden, damit dieser das Zertifikat beim Initiieren einer SSL-Verbindung als vertrauenswürdig einstuft.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### Installieren des Benutzerportals

Beachten Sie folgende Punkte, bevor Sie das Benutzerportal auf einem anderen Server installieren:

- Es ist hilfreich, auf dem mit dem Internet verbundenen Webserver einen Webbrowser zu öffnen und die URL des Webdienst-SDK aufzurufen, die in die Datei "Web.config" eingegeben wurde. Wenn der Webdienst erfolgreich im Browser aufgerufen werden kann, werden Sie zur Eingabe von Anmeldeinformationen aufgefordert. Geben Sie den Benutzernamen und das Kennwort, die in die Datei "Web.config" eingegeben wurden, genau wie in der Datei angezeigt ein. Stellen Sie sicher, dass keine Zertifikatswarnungen oder -fehler angezeigt werden.
- Wenn sich vor dem Benutzerportal-Webserver ein Reverseproxy oder eine Firewall befindet und SSL-Abladung durchführt, können Sie die Datei „web.config“ für das Benutzerportal bearbeiten und dem <appSettings>-Abschnitt den folgenden Schlüssel hinzufügen, damit das Benutzerportal HTTP anstelle von HTTPS verwenden kann. <add key="SSL_REQUIRED" value="false"/>

#### So installieren Sie das Benutzerportal

1. Öffnen Sie auf dem Azure Multi-Factor Authentication-Server den Windows-Explorer, und navigieren Sie zu dem Ordner, in dem der Azure Multi-Factor Authentication-Server installiert ist (z. B. C:\Programme\Multi-Factor Authentication Server). Wählen Sie entsprechend dem Server, auf dem das Benutzerportal installiert wird, die 32-Bit- oder 64-Bit-Version der Datei "MultiFactorAuthenticationUserPortalSetup" aus. Kopieren Sie die Installationsdatei auf den mit dem Internet verbundenen Server.
2. Die Setupdatei muss auf dem mit dem Internet verbundenen Webserver mit Administratorrechten ausgeführt werden. Dies lässt sich am einfachsten erreichen, indem Sie eine Eingabeaufforderung als Administrator öffnen und zu dem Verzeichnis navigieren, in das die Installationsdatei kopiert wurde.
3. Führen Sie die Installationsdatei "MultiFactorAuthenticationUserPortalSetup64" aus, und ändern Sie ggf. den Namen der Website und des virtuellen Verzeichnisses.
4. Navigieren Sie nach Abschluss der Installation des Benutzerportals zu "C:\inetpub\wwwroot\MultiFactorAuth" (oder zum entsprechenden Verzeichnis basierend auf dem Namen des virtuellen Verzeichnisses), und bearbeiten Sie die Datei "Web.config".
5. Suchen Sie den Schlüssel USE_WEB_SERVICE_SDK, und ändern Sie den Wert von "false" in "true". Suchen Sie die Schlüssel WEB_SERVICE_SDK_AUTHENTICATION_USERNAME und WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD, und legen Sie die Werte auf den Benutzernamen und das Kennwort des Dienstkontos fest, das Mitglied der Sicherheitsgruppe "PhoneFactor Admins" ist (siehe oben im Abschnitt zu den Anforderungen). Geben Sie den Benutzernamen und das Kennwort zwischen den Anführungszeichen am Ende der Zeile ein (value=""/>). Es wird empfohlen, einen qualifizierten Benutzernamen (z. B. „Domäne\Benutzername“ oder „Computer\Benutzername“) zu verwenden.
6. Suchen Sie die Einstellung "pfup_pfwssdk_PfWsSdk", und ändern Sie den Wert "http://localhost:4898/PfWsSdk.asmx" in die URL des Webdienst-SDK, das auf dem Azure Multi-Factor Authentication-Server ausgeführt wird (z. B. https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx)). Für diese Verbindung wird SSL verwendet. Deshalb müssen Sie auf das Webdienst-SDK mit dem Servernamen und nicht mit der IP-Adresse verweisen, da das SSL-Zertifikat für den Servernamen ausgestellt wurde und die URL dem Namen des Zertifikats entsprechen muss. Wenn der Servername nicht als IP-Adresse des Servers mit Internetzugriff aufgelöst wird, fügen Sie der HOSTS-Datei auf diesem Server einen Eintrag hinzu, um den Namen des Azure Multi-Factor Authentication-Servers seiner IP-Adresse zuzuordnen. Speichern Sie die Datei "Web.config", nachdem Änderungen vorgenommen wurden.
7. Wenn die Website, unter der das Benutzerportal installiert wurde (z. B. die Standardwebsite), noch nicht mit einem öffentlich signierten Zertifikat gebunden wurde, installieren Sie das Zertifikat auf dem Server, falls es nicht bereits installiert wurde. Öffnen Sie dann den IIS-Manager, und binden Sie das Zertifikat an die Website.
8. Öffnen Sie auf einem beliebigen Computer einen Webbrowser, und navigieren Sie zu der URL, unter der das Benutzerportal installiert wurde (z. B. https://www.publicwebsite.com/MultiFactorAuth). Stellen Sie sicher, dass keine Zertifikatswarnungen oder -fehler angezeigt werden.



## Konfigurieren der Benutzerportal-Einstellungen im Azure Multi-Factor Authentication-Server
Nachdem das Portal installiert wurde, müssen Sie den Azure Multi-Factor Authentication-Server für die Verwendung des Portals konfigurieren.

Der Azure Multi-Factor Authentication-Server stellt mehrere Optionen für das Benutzerportal bereit. Die folgende Tabelle enthält eine Liste dieser Optionen sowie eine Erläuterung zu deren Verwendung.

Benutzerportal – Einstellungen|Beschreibung|
:------------- | :------------- | 
Benutzerportal-URL| Hier können Sie die URL eingeben, über die das Portal gehostet wird.
Primäre Authentifizierung| Hier können Sie den Typ der Authentifizierung für die Anmeldung beim Portal angeben. Mögliche Optionen: Windows-, Radius- oder LDAP-Authentifizierung.
Benutzeranmeldung zulassen|Ermöglicht Benutzern die Eingabe von Benutzername und Kennwort auf der Anmeldeseite des Benutzerportals. Ist diese Option nicht ausgewählt, sind die Felder ausgeblendet.
Benutzerregistrierung zulassen|Ermöglicht dem Benutzer die Registrierung bei der mehrstufigen Authentifizierung. Dazu wird der Benutzer auf einem Setupbildschirm zur Eingabe zusätzlicher Informationen wie z. B. einer Telefonnummer aufgefordert. Die Eingabeaufforderung für die Ersatzrufnummer ermöglicht dem Benutzer die Angabe einer sekundären Telefonnummer. Die Eingabeaufforderung für das Drittanbieter-OATH-Token ermöglicht dem Benutzer die Angabe eines Drittanbieter-OATH-Tokens.
Benutzern das Initiieren einer Einmalumgehung ermöglichen| Dies ermöglicht Benutzer das Initiieren einer Einmalumgehung. Wenn ein Benutzer diese Einstellung festlegt, wird sie bei seiner nächsten Anmeldung übernommen. Die Eingabeaufforderung für die Gültigkeit der Umgehung in Sekunden zeigt dem Benutzer ein Feld an, in dem er den Standardwert von 300 Sekunden ändern kann. Andernfalls gilt die Einmalumgehung nur für 300 Sekunden.
Benutzern die Auswahl der Methode ermöglichen| Ermöglicht Benutzern das Festlegen ihrer primäre Kontaktmethode. Dabei kann es sich um einen Telefonanruf, eine Textnachricht, die mobile App oder ein OATH-Token handeln.
Benutzern die Auswahl der Sprache ermöglichen| Ermöglicht dem Benutzer das Ändern der Sprache, die für den Telefonanruf, die Textnachricht, die mobile App oder das OATH-Token verwendet wird.
Benutzern das Aktivieren der mobilen App ermöglichen| Ermöglicht Benutzern das Generieren eines Aktivierungscodes, um den Aktivierungsprozess der mit dem Server verwendeten mobilen App durchzuführen. Sie können auch die Anzahl der Geräte festlegen, auf denen Benutzer die App aktivieren können. Zwischen 1 und 10.
Sicherheitsfragen als Alternative verwenden|Ermöglicht Ihnen das Verwenden von Sicherheitsfragen, falls die mehrstufige Authentifizierung fehlschlägt. Sie können die Anzahl der Sicherheitsfragen festlegen, die richtig beantwortet werden müssen.
Benutzern das Zuordnen von Drittanbieter-OATH-Token ermöglichen| Ermöglicht Benutzern das Angeben eines Drittanbieter-OATH-Tokens.
OATH-Token als Alternative verwenden|Ermöglicht die Verwendung eines OATH-Token für den Fall, dass die mehrstufige Authentifizierung nicht erfolgreich ist. Sie können auch den Sitzungstimeout in Minuten angeben.
Aktivieren der Protokollierung|Aktiviert die Protokollierung im Benutzerportal. Die Protokolldateien befinden sich unter: C:\Programme\Multi-Factor Authentication Server\Logs.

Die meisten dieser Einstellungen sind für den Benutzer sichtbar, sobald sie aktiviert sind und der Benutzer sich beim Benutzerportal anmeldet.

![Benutzerportaleinstellungen](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### So konfigurieren Sie die Benutzerportal-Einstellungen im Azure Multi-Factor Authentication-Server




1. Klicken Sie auf dem Azure Multi-Factor Authentication-Server auf das Symbol "Benutzerportal". Geben Sie auf der Registerkarte "Einstellungen" im Textfeld "Benutzerportal-URL" die URL des Benutzerportals ein. Sofern die E-Mail-Funktion aktiviert wurde, wird diese URL in E-Mails eingefügt, die an Benutzer gesendet werden, wenn sie in den Azure Multi-Factor Authentication-Server importiert werden.
2. Wählen Sie die Einstellungen aus, die Sie im Benutzerportal verwenden möchten. Wenn beispielsweise Benutzer ihre Authentifizierungsmethoden steuern dürfen, stellen Sie sicher, dass "Methodenauswahl durch Benutzer zulassen" zusammen mit den zur Auswahl stehenden Methoden aktiviert ist.
3. Klicken Sie auf den Link "Hilfe" in der rechten oberen Ecke, um Erläuterungen zu den angezeigten Einstellungen zu erhalten.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## Registerkarte „Administratoren“
Auf dieser Registerkarte können Sie problemlos Benutzer hinzufügen, die über Administratorrechte verfügen sollen. Beim Hinzufügen eines Administrators können Sie seine Berechtigungen präzise abstimmen. So können Sie sicherstellen, dass dem Administrator nur die erforderlichen Berechtigungen gewährt werden. Klicken Sie einfach auf die Schaltfläche „Hinzufügen“, wählen Sie dann den Benutzer und die entsprechenden Berechtigungen aus, und klicken Sie dann auf „Hinzufügen“.

![Benutzerportaladministratoren](./media/multi-factor-authentication-get-started-portal/admin.png)


## Sicherheitsfragen
Auf dieser Registerkarte können Sie die Sicherheitsfragen angeben, die Benutzer beantworten müssen, wenn die Option „Sicherheitsfragen als Alternative verwenden“ ausgewählt ist. Der Azure Multi-Factor Authentication-Server verfügt über Standardfragen, die Sie verwenden können. Sie können auch die Reihenfolge ändern oder eigene Fragen hinzufügen. Beim Hinzufügen eigener Fragen können Sie die Sprache angeben, in der diese angezeigt werden sollen.

![Benutzerportal-Sicherheitsfragen](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## Weitergegebene Sitzungen

## SAML
Ermöglicht Ihnen das Einrichten des Benutzerportals zum Akzeptieren von Ansprüchen von einem Identitätsanbieter, der SAML nutzt. Sie können die Timeout-Sitzung, das Verifizierungszertifikat und die URL für die Umleitung zur Abmeldung angeben.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## Vertrauenswürdige IP-Adressen
Auf dieser Registerkarte können Sie einzelne IP-Adressen oder IP-Adressbereiche angeben, die hinzugefügt werden können, damit die mehrstufige Authentifizierung umgangen wird, wenn sich ein Benutzer mit einer dieser IP-Adressen anmeldet.

![Vertrauenswürdige IPs des Benutzerportals](./media/multi-factor-authentication-get-started-portal/trusted.png)

## Self-Service-Benutzerregistrierung
Wenn Sie möchten, dass Ihre Benutzer sich anmelden und registrieren, müssen Sie die Optionen „Benutzeranmeldung zulassen“ und „Benutzerregistrierung zulassen“ auswählen. Denken Sie daran, dass die ausgewählten Einstellungen die Benutzeranmeldung beeinflussen.

Wenn sich ein Benutzer z. B. beim Benutzerportal meldet und auf die Schaltfläche „Anmelden“ klickt, wird er auf die Seite zur Benutzereinrichtung von Azure Multi-Factor Authentication geleitet. Je nachdem, wie Sie Azure Multi-Factor Authentication konfiguriert haben, kann der Benutzer seine Authentifizierungsmethode auswählen.

Wenn er die Authentifizierungsmethode „Sprachanruf“ auswählt oder für die Verwendung dieser Methode vorkonfiguriert wurde, fordert die Seite den Benutzer zur Eingabe seiner primären Rufnummer und ggf. der Durchwahl auf. Möglicherweise kann er auch eine Ersatzrufnummer eingeben.

![Vertrauenswürdige IPs des Benutzerportals](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Wenn der Benutzer bei der Authentifizierung eine PIN verwenden muss, fordert ihn die Seite auch zur Eingabe einer PIN auf. Nach der Eingabe der Telefonnummer(n) und PIN (falls zutreffend) klickt der Benutzer auf die Schaltfläche „Jetzt für Authentifizierung angerufen werden“. Azure Multi-Factor Authentication führt eine Authentifizierung per Telefonanruf an die primäre Telefonnummer des Benutzers durch. Der Benutzer muss den Anruf annehmen, ggf. seine PIN eingeben und # drücken, um zum nächsten Schritt der Selbstregistrierung zu gelangen.

Wenn der Benutzer die Authentifizierung per SMS-Text auswählt oder für die Verwendung dieser Methode vorkonfiguriert wurde, fordert die Seite den Benutzer auf, seine Mobiltelefonnummer anzugeben. Wenn der Benutzer bei der Authentifizierung eine PIN verwenden muss, fordert ihn die Seite auch zur Eingabe einer PIN auf. Nach der Eingabe der Telefonnummer und PIN (falls zutreffend) klickt der Benutzer auf die Schaltfläche „Jetzt Textmitteilung für Authentifizierung senden“. Azure Multi-Factor Authentication führt eine SMS-Authentifizierung über das Mobiltelefon des Benutzers durch. Der Benutzer muss die SMS empfangen, die einen Einmalkenncode (OTP) enthält, und mit diesem OTP sowie ggf. seiner PIN darauf antworten, um zum nächsten Schritt der Selbstregistrierung zu gelangen.

![Benutzerportal-SMS](./media/multi-factor-authentication-get-started-portal/text.png)

Wenn der Benutzer die Authentifizierungsmethode für die mobile App auswählt oder für die Verwendung dieser Methode vorkonfiguriert wurde, fordert die Seite den Benutzer auf, die App „Azure Multi-Factor Authentication“ auf seinem Gerät zu installieren und einen Aktivierungscode zu generieren. Nach der Installation der App „Azure Multi-Factor Authentication“ klickt der Benutzer auf die Schaltfläche „Aktivierungscode generieren“.

>[AZURE.NOTE]Zur Verwendung der App „Azure Multi-Factor Authentication“ muss der Benutzer Pushbenachrichtigungen für sein Gerät aktivieren.

Die Seite zeigt dann einen Aktivierungscode und eine URL zusammen mit einem Barcodebild an. Wenn der Benutzer bei der Authentifizierung eine PIN verwenden muss, fordert ihn die Seite auch zur Eingabe einer PIN auf. Der Benutzer gibt den Aktivierungscode und die URL in die App „Azure Multi-Factor Authentication“ ein oder verwendet den Barcodescanner, um das Barcodebild zu scannen und klickt auf die Schaltfläche „Aktivieren“.

Nach Abschluss die Aktivierung klickt der Benutzer auf die Schaltfläche „Jetzt authentifizieren“. Azure Multi-Factor Authentication führt eine Authentifizierung in der mobilen App des Benutzers durch. Der Benutzer muss seine PIN (falls zutreffend) eingeben die Schaltfläche „Authentifizieren“ in seiner mobilen App drücken, um mit dem nächsten Schritt der Selbstregistrierung fortzufahren.


Wenn die Administratoren den Azure Multi-Factor Authentication-Server für das Erfassen von Sicherheitsfragen und -antworten konfiguriert haben, wird der Benutzer auf die Seite mit den Sicherheitsfragen weitergeleitet. Der Benutzer muss vier Sicherheitsfragen auswählen und die entsprechenden Antworten bereitstellen.

![Benutzerportal-Sicherheitsfragen](./media/multi-factor-authentication-get-started-portal/secq.png)

Die Selbstregistrierung des Benutzers ist jetzt abgeschlossen, und der Benutzer ist am Benutzerportal angemeldet. Benutzer können sich jederzeit erneut am Benutzerportal anmelden, um ihre Telefonnummern, PINs, Authentifizierungsmethoden und Sicherheitsfragen zu ändern, wenn die Administratoren dies zulassen.

 

<!---HONumber=July15_HO4-->