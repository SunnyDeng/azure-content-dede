<properties 
	pageTitle="Erste Schritte mit dem Webdienst der mobilen App für den MFA-Server" 
	description="Die Azure Multi-Factor Authentication-App verfügt über eine zusätzliche Out-of-Band-Authentifizierungsoption. Diese ermöglicht es, dass der MFA-Server Pushbenachrichtigungen an Benutzer sendet." 
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
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# Erste Schritte mit dem Webdienst der mobilen App für den MFA-Server

Die Azure Multi-Factor Authentication-App verfügt über eine zusätzliche Out-of-Band-Authentifizierungsoption. Anstelle eines automatischen Telefonanrufs oder einer SMS an den Benutzer bei der Anmeldung wird bei Multi-Factor Authentication eine Pushbenachrichtigung an die Azure Multi-Factor Authentication-App auf dem Smartphone oder Tablet des Benutzers gesendet. Der Benutzer tippt in der App einfach auf "Authentifizieren" (oder gibt eine PIN ein und tippt auf "Authentifizieren"), um sich anzumelden.

Um die Azure Multi-Factor Authentication-App verwenden zu können, müssen folgende Voraussetzungen erfüllt sein, damit die App erfolgreich mit dem Webdienst der mobilen App kommunizieren kann:

- Informationen zu den Hardware- und Softwareanforderungen finden Sie unter "Hardware- und Softwareanforderungen".
- Sie müssen Version 6.0 oder höher des Azure Multi-Factor Authentication-Servers verwenden.
- Der Webdienst der mobilen App muss auf einem Webserver mit Internetverbindung installiert sein, auf dem Microsoft® Internetinformationsdienste (IIS) 6.x oder 7.x ausgeführt wird.
- Stellen Sie bei Verwendung von IIS 6.x sicher, dass ASP.NET, Version 2.0.50727, installiert, registriert und auf "Zulässig" festgelegt ist.
- Bei Verwendung von IIS 7.x erforderliche Rollendienste umfassen ASP.NET und IIS 6-Metabasiskompatibilität.
- Der Webdienst der mobilen App muss über eine öffentliche URL erreichbar sein.
- Der Webdienst der mobilen App muss mit einem SSL-Zertifikat gesichert sein.
- In IIS 6.x oder IIS 7.x muss das Azure Multi-Factor Authentication-Webdienst-SDK auf dem Server installiert sein, auf dem der Azure Multi-Factor Authentication-Server installiert ist.
- Das Azure Multi-Factor Authentication-Webdienst-SDK muss mit einem SSL-Zertifikat gesichert sein.
- Der Webdienst der mobilen App muss eine SSL-Verbindung mit dem Azure Multi-Factor Authentication-Webdienst-SDK herstellen können.
- Der Webdienst der mobilen App muss sich beim Azure Multi-Factor Authentication-Webdienst-SDK mit den Anmeldeinformationen eines Dienstkontos, das Mitglied der Sicherheitsgruppe „PhoneFactor Admins“ ist, authentifizieren können. Dieses Dienstkonto und diese Gruppe sind in Active Directory vorhanden, wenn der Azure Multi-Factor Authentication-Server auf einem in die Domäne eingebundenen Server ausgeführt wird. Dieses Dienstkonto und diese Gruppe sind lokal auf dem Azure Multi-Factor Authentication-Server vorhanden, wenn dieser nicht in eine Domäne eingebunden ist.


Zum Installieren des Benutzerportals auf einem anderen Server als dem Azure Multi-Factor Authentication-Server sind die folgenden drei Schritte erforderlich:

1. Installieren des Webdienst-SDK
2. Installieren des Webdiensts der mobilen App
3. Konfigurieren der Einstellungen für die mobile App im Azure Multi-Factor Authentication-Server
4. Aktivieren der Azure Multi-Factor Authentication-App für Endbenutzer

## Installieren des Webdienst-SDK

Wenn das Azure Multi-Factor Authentication-Webdienst-SDK nicht bereits auf dem Azure Multi-Factor Authentication-Server installiert ist, wechseln Sie zu diesem Server, und öffnen Sie den Azure Multi-Factor Authentication-Server. Klicken Sie auf das Symbol des Webdienst-SDK, klicken Sie auf die Schaltfläche "Webdienst-SDK installieren", und folgen Sie den angezeigten Anweisungen. Das Webdienst-SDK muss mit einem SSL-Zertifikat gesichert sein. Ein selbst signiertes Zertifikat ist für diesen Zweck zulässig. Dieses muss jedoch in den Speicher "Vertrauenswürdige Stammzertifizierungsstellen" des lokalen Computerkontos auf dem Benutzerportal-Webserver importiert werden, damit dieser das Zertifikat beim Initiieren einer SSL-Verbindung als vertrauenswürdig einstuft.

<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## Installieren des Webdiensts der mobilen App
Beachten Sie folgende Punkte, bevor Sie den Webdienst der mobilen App installieren:

- Wenn das Azure Multi-Factor Authentication-Benutzerportal bereits auf dem Server mit Internetverbindung installiert ist, können der Benutzername, das Kennwort und die URL für das Webdienst-SDK aus der Datei "Web.config" des Benutzerportals kopiert werden. 
- Es ist hilfreich, auf dem mit dem Internet verbundenen Webserver einen Webbrowser zu öffnen und die URL des Webdienst-SDK aufzurufen, die in die Datei "Web.config" eingegeben wurde. Wenn der Webdienst erfolgreich im Browser aufgerufen werden kann, werden Sie zur Eingabe von Anmeldeinformationen aufgefordert. Geben Sie den Benutzernamen und das Kennwort, die in die Datei "Web.config" eingegeben wurden, genau wie in der Datei angezeigt ein. Stellen Sie sicher, dass keine Zertifikatswarnungen oder -fehler angezeigt werden.
- Wenn sich vor dem Webserver mit dem Webdienst der mobilen App ein Reverseproxy oder eine Firewall befindet und SSL-Abladung durchführt, können Sie die Datei "Web.config" für das Benutzerportal bearbeiten und dem <appSettings>-Abschnitt den folgenden Schlüssel hinzufügen, damit der Webdienst der mobilen App HTTP anstelle von HTTPS verwenden kann. SSL ist aber weiterhin für die Verbindung von der mobilen Anwendung mit der Firewall/dem Reverseproxy erforderlich. <add key="SSL_REQUIRED" value="false"/> 

### So installieren Sie den Webdienst der mobilen App

<ol>
<li>Öffnen Sie auf dem Azure Multi-Factor Authentication-Server den Windows-Explorer, und navigieren Sie zu dem Ordner, in dem der Azure Multi-Factor Authentication-Server installiert ist (z.&#160;B. „C:\Programme\Azure Multi-Factor Authentication Server“). Wählen Sie entsprechend dem Server, auf dem der Webdienst der mobilen App installiert wird, die 32-Bit- oder 64-Bit-Version der Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup installation-Installationsdatei aus. Kopieren Sie die Installationsdatei auf den mit dem Internet verbundenen Server.</li>

<li>Die Setupdatei muss auf dem mit dem Internet verbundenen Webserver mit Administratorrechten ausgeführt werden. Dies lässt sich am einfachsten erreichen, indem Sie eine Eingabeaufforderung als Administrator öffnen und zu dem Verzeichnis navigieren, in das die Installationsdatei kopiert wurde.</li>

<li>Führen Sie die Multi-Factor AuthenticationMobileAppWebServiceSetup-Installationsdatei aus, ändern Sie ggf. die Website, und ändern Sie das virtuelle Verzeichnis in einen kurzen Namen wie "PA". Es wird empfohlen, für das virtuelle Verzeichnis einen kurzen Namen festzulegen, da der Benutzer die URL des Webdiensts der mobilen App bei der Aktivierung auf dem mobilen Gerät eingeben muss.</li>

<li>Navigieren Sie nach Abschluss der Installation von Azure Multi-Factor AuthenticationMobileAppWebServiceSetup zu "C:\inetpub\wwwroot\PA" (oder zum entsprechenden Verzeichnis basierend auf dem Namen des virtuellen Verzeichnisses), und bearbeiten Sie die Datei "Web.config".</li>

<li>Suchen Sie die Schlüssel WEB_SERVICE_SDK_AUTHENTICATION_USERNAME und WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD, und legen Sie die Werte auf den Benutzernamen und das Kennwort des Dienstkontos fest, das Mitglied der Sicherheitsgruppe "PhoneFactor Admins" ist (siehe oben im Abschnitt zu den Anforderungen). Dabei kann es sich um das gleiche Konto handeln, das als Identität des Azure Multi-Factor Authentication-Benutzerportals verwendet wird, sofern dieses bereits installiert wurde. Geben Sie den Benutzernamen und das Kennwort zwischen den Anführungszeichen am Ende der Zeile ein (value=""/>). Es wird empfohlen, einen qualifizierten Benutzernamen (z.&#160;B. "Domäne\Benutzername" oder "Computer\Benutzername") zu verwenden.</li>

<li>Suchen Sie die Einstellung „pfMobile App Web Service_pfwssdk_PfWsSdk“, und ändern Sie den Wert „http://localhost:4898/PfWsSdk.asmx“ in die URL des Webdienst-SDK, das auf dem Azure Multi-Factor Authentication-Server ausgeführt wird (z.&#160;B. „https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx“). Für diese Verbindung wird SSL verwendet. Deshalb müssen Sie auf das Webdienst-SDK mit dem Servernamen und nicht mit der IP-Adresse verweisen, da das SSL-Zertifikat für den Servernamen ausgestellt wurde und die URL dem Namen des Zertifikats entsprechen muss. Wenn der Servername nicht als IP-Adresse des Servers mit Internetzugriff aufgelöst wird, fügen Sie der HOSTS-Datei auf diesem Server einen Eintrag hinzu, um den Namen des Azure Multi-Factor Authentication-Servers seiner IP-Adresse zuzuordnen. Speichern Sie die Datei "Web.config", nachdem Änderungen vorgenommen wurden.</li>

<li>Wenn die Website, unter der der Webdienst der mobilen App installiert wurde (z.&#160;B. "Standardwebsite"), noch nicht mit einem öffentlich signierten Zertifikat gebunden wurde, installieren Sie das Zertifikat auf dem Server, sofern es nicht bereits installiert ist, öffnen Sie den IIS-Manager, und binden Sie das Zertifikat an die Website.</li>

<li>Öffnen Sie auf einem beliebigen Computer einen Webbrowser, und navigieren Sie zu der URL, unter der der Webdienst der mobilen App installiert wurde (z.&#160;B. "https://www.publicwebsite.com/PA"). Stellen Sie sicher, dass keine Zertifikatswarnungen oder -fehler angezeigt werden.</li>

### Konfigurieren der Einstellungen für die mobile App im Azure Multi-Factor Authentication-Server
Nachdem Sie den Webdienst der mobilen App installiert haben, müssen Sie den Azure Multi-Factor Authentication-Server für die Verwendung mit dem Portal konfigurieren.

#### So konfigurieren Sie die Einstellungen für die mobile App im Azure Multi-Factor Authentication-Server

1. Klicken Sie auf dem Azure Multi-Factor Authentication-Server auf das Symbol "Benutzerportal". Wenn Benutzer ihre Authentifizierungsmethoden selbst steuern dürfen, aktivieren Sie auf der Registerkarte "Einstellungen" unter "Methodenauswahl durch Benutzer zulassen" die Option "Mobile App". Wenn diese Funktion nicht aktiviert ist, müssen Endbenutzer sich an den Helpdesk wenden, um die Aktivierung für die mobile App abzuschließen.
2. Aktivieren Sie das Kontrollkästchen "Aktivierung der mobilen Anwendung durch Benutzer zulassen".
3. Aktivieren Sie das Kontrollkästchen "Benutzerregistrierung zulassen".
4. Klicken Sie auf das Symbol "Mobile App".
5. Geben Sie die URL, die für das virtuelle Verzeichnis verwendet wird, das bei der Installation von Azure Multi-Factor AuthenticationMobileAppWebServiceSetup erstellt wurde. Ein Kontoname kann in das Eingabefeld eingegeben werden. Dieser Firmenname wird in der mobilen App angezeigt. Wenn Sie das Feld leer lassen, wird der Name des Anbieters für mehrstufige Authentifizierung angezeigt, der im Azure-Verwaltungsportal erstellt wurde. 



<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

<!---HONumber=AcomDC_0224_2016-->