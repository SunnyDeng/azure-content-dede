<properties 
	pageTitle="Bereitstellen des Benutzerportals für den Azure Multi-Factor Authentication-Server" 
	description="Dies ist die Seite für Azure Multi-Factor Authentication, auf der die ersten Schritte mit Azure MFA und dem Benutzerportal beschrieben werden." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
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

<ol>
<li>Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Benutzerportal-Symbol und dann auf die Schaltfläche "Benutzerportal installieren". <li>Klicken Sie auf "Weiter". <li>Klicken Sie auf "Weiter". <li>Wenn der Computer zu einer Domäne gehört und die Active Directory-Konfiguration zum Sichern der Kommunikation zwischen dem Benutzerportal und dem Azure Multi-Factor Authentication-Dienst unvollständig ist, wird der Schritt "Active Directory" angezeigt. Klicken Sie auf die Schaltfläche "Weiter", um diese Konfiguration automatisch abzuschließen. <li>Klicken Sie auf "Weiter". <li>Klicken Sie auf "Weiter". <li>Klicken Sie auf "Schließen". <li>Öffnen Sie auf einem beliebigen Computer einen Webbrowser, und navigieren Sie zu der URL, unter der das Benutzerportal installiert wurde (z. B. https://www.publicwebsite.com/MultiFactorAuth). Stellen Sie sicher, dass keine Zertifikatswarnungen oder -fehler angezeigt werden.</li>

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
- Wenn sich vor dem Benutzerportal-Webserver ein Reverseproxy oder eine Firewall befindet und SSL-Abladung durchführt, können Sie die Datei "web.config" für das Benutzerportal bearbeiten und dem <appSettings>-Abschnitt den folgenden Schlüssel hinzufügen, damit das Benutzerportal HTTP anstelle von HTTPS verwenden kann. <add key="SSL_REQUIRED" value="false"/>

#### So installieren Sie das Benutzerportal

<ol>




<li>Öffnen Sie auf dem Azure Multi-Factor Authentication-Server den Windows-Explorer, und navigieren Sie zu dem Ordner, in dem der Azure Multi-Factor Authentication-Server installiert ist (z.&#160;B. C:\Programme\Multi-Factor Authentication Server). Wählen Sie entsprechend dem Server, auf dem das Benutzerportal installiert wird, die 32-Bit- oder 64-Bit-Version der Datei "MultiFactorAuthenticationUserPortalSetup" aus. Kopieren Sie die Installationsdatei auf den mit dem Internet verbundenen Server.</li>

<li>Die Setupdatei muss auf dem mit dem Internet verbundenen Webserver mit Administratorrechten ausgeführt werden. Dies lässt sich am einfachsten erreichen, indem Sie eine Eingabeaufforderung als Administrator öffnen und zu dem Verzeichnis navigieren, in das die Installationsdatei kopiert wurde.</li>

<li>Führen Sie die Installationsdatei "MultiFactorAuthenticationUserPortalSetup64" aus, und ändern Sie ggf. den Namen der Website und des virtuellen Verzeichnisses.</li>

<li>Navigieren Sie nach Abschluss der Installation des Benutzerportals zu "C:\inetpub\wwwroot\MultiFactorAuth" (oder zum entsprechenden Verzeichnis basierend auf dem Namen des virtuellen Verzeichnisses), und bearbeiten Sie die Datei "Web.config".</li>

<li>Suchen Sie den Schlüssel USE_WEB_SERVICE_SDK, und ändern Sie den Wert von "false" in "true". Suchen Sie die Schlüssel WEB_SERVICE_SDK_AUTHENTICATION_USERNAME und WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD, und legen Sie die Werte auf den Benutzernamen und das Kennwort des Dienstkontos fest, das Mitglied der Sicherheitsgruppe "PhoneFactor Admins" ist (siehe oben im Abschnitt zu den Anforderungen). Geben Sie den Benutzernamen und das Kennwort zwischen den Anführungszeichen am Ende der Zeile ein (value=""/>). Es wird empfohlen, einen qualifizierten Benutzernamen (z.&#160;B. "Domäne\Benutzername" oder "Computer\Benutzername") zu verwenden.</li>

<li>Suchen Sie die Einstellung "pfup_pfwssdk_PfWsSdk", und ändern Sie den Wert "http://localhost:4898/PfWsSdk.asmx" in die URL des Webdienst-SDK, das auf dem Azure Multi-Factor Authentication-Server ausgeführt wird (z.&#160;B. "https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx"). Für diese Verbindung wird SSL verwendet. Deshalb müssen Sie auf das Webdienst-SDK mit dem Servernamen und nicht mit der IP-Adresse verweisen, da das SSL-Zertifikat für den Servernamen ausgestellt wurde und die URL dem Namen des Zertifikats entsprechen muss. Wenn der Servername nicht als IP-Adresse des Servers mit Internetzugriff aufgelöst wird, fügen Sie der HOSTS-Datei auf diesem Server einen Eintrag hinzu, um den Namen des Azure Multi-Factor Authentication-Servers seiner IP-Adresse zuzuordnen. Speichern Sie die Datei "Web.config", nachdem Änderungen vorgenommen wurden.</li>

<li>Wenn die Website, unter der das Benutzerportal installiert wurde (z. B. die Standardwebsite), noch nicht mit einem öffentlich signierten Zertifikat gebunden wurde, installieren Sie das Zertifikat auf dem Server, falls es nicht bereits installiert wurde. Öffnen Sie dann den IIS-Manager, und binden Sie das Zertifikat an die Website.</li>

<li>Öffnen Sie auf einem beliebigen Computer einen Webbrowser, und navigieren Sie zu der URL, unter der das Benutzerportal installiert wurde (z.&#160;B. "https://www.publicwebsite.com/MultiFactorAuth"). Stellen Sie sicher, dass keine Zertifikatswarnungen oder -fehler angezeigt werden.</li>

## Konfigurieren der Benutzerportal-Einstellungen im Azure Multi-Factor Authentication-Server
Nachdem das Portal installiert wurde, müssen Sie den Azure Multi-Factor Authentication-Server für die Verwendung des Portals konfigurieren.

### So konfigurieren Sie die Benutzerportal-Einstellungen im Azure Multi-Factor Authentication-Server




1. Klicken Sie auf dem Azure Multi-Factor Authentication-Server auf das Symbol "Benutzerportal". Geben Sie auf der Registerkarte "Einstellungen" im Textfeld "Benutzerportal-URL" die URL des Benutzerportals ein. Sofern die E-Mail-Funktion aktiviert wurde, wird diese URL in E-Mails eingefügt, die an Benutzer gesendet werden, wenn sie in den Azure Multi-Factor Authentication-Server importiert werden.
2. Wählen Sie die Einstellungen aus, die Sie im Benutzerportal verwenden möchten. Wenn beispielsweise Benutzer ihre Authentifizierungsmethoden steuern dürfen, stellen Sie sicher, dass "Methodenauswahl durch Benutzer zulassen" zusammen mit den zur Auswahl stehenden Methoden aktiviert ist.
3. Klicken Sie auf den Link "Hilfe" in der rechten oberen Ecke, um Erläuterungen zu den angezeigten Einstellungen zu erhalten.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>

<!---HONumber=July15_HO2-->