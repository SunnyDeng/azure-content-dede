<properties 
	pageTitle="Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure MFA-Server mit Windows Server 2012 R2 AD FS" 
	description="Auf dieser Seite zur Azure Multi-Factor Authentication werden die ersten Schritte mit Azure MFA und AD FS unter Windows Server 2012 R2 beschrieben." 
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
	ms.date="08/24/2015" 
	ms.author="billmath"/>


# Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit Windows Server 2012 R2 AD FS

Wenn Ihre Organisation einen Verbund mit Azure AD bildet und Sie lokale und Cloudressourcen sichern möchten, können Sie dazu den Azure Multi-Factor Authentication-Server verwenden, indem Sie diesen für die Verwendung mit AD FS so konfigurieren, dass die Multi-Factor Authentication bei wichtigen Endpunkten ausgelöst wird.

In dieser Dokumentation wird die Verwendung von Azure Multi-Factor Authentication-Server mit AD FS unter Windows Server 2012 R2 behandelt. Informationen zur Verwendung von Multi-Factor Authentication mit AD FS 2.0 finden Sie unter [Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Sichern von Windows Server 2012 R2 AD FS mit Azure Multi-Factor Authentication-Server

Bei der Installation von Azure Multi-Factor Authentication-Server haben Sie zwei Möglichkeiten:

- Lokale Installation von Azure Multi-Factor Authentication-Server auf demselben Server wie AD FS 
- Lokale Installation von Azure Multi-Factor Authentication-Adapter auf dem AD FS-Server und Installation des MFA-Servers auf einem anderen Computer

Bevor Sie beginnen, sollten Sie Folgendes beachten:

- Es ist nicht erforderlich, den Azure Multi-Factor Authentication-Server auf dem AD FS-Verbundserver zu installieren. Der Multi-Factor Authentication-Adapter für AD FS muss jedoch auf einem Server mit Windows Server 2012 R2 installiert werden, auf dem AD FS ausgeführt wird. Sie können den Server auf einem anderen Computer installieren, sofern es sich um eine unterstützte Version handelt, und den AD FS-Adapter separat auf dem AD FS-Verbundserver installieren. Im folgenden Verfahren finden Sie Anweisungen, wie Sie den Adapter separat installieren.

- Das angemeldete Konto muss über Berechtigungen zum Erstellen von Sicherheitsgruppen in Active Directory verfügen.

- Der Installations-Assistent für den Multi-Factor Authentication AD FS-Adapter erstellt eine Sicherheitsgruppe namens "PhoneFactor Admins" in Active Directory und fügt dieser Gruppe dann das AD FS-Dienstkonto des Verbunddiensts hinzu. Es wird empfohlen, dass Sie auf dem Domänencontroller überprüfen, ob die Gruppe "PhoneFactor Admins" tatsächlich erstellt wurde und ob das AD FS-Dienstkonto Mitglied dieser Gruppe ist. Bei Bedarf fügen Sie das AD FS-Dienstkonto der Gruppe "PhoneFactor Admins" auf dem Domänencontroller manuell hinzu.
  

### So installieren Sie den Azure Multi-Factor Authentication-Server lokal auf demselben Server wie AD FS

1. Laden Sie den Azure Multi-Factor Authentication-Server herunter und installieren Sie ihn auf dem AD FS-Verbundserver. Informationen zum Installieren des Multi-Factor Authentication-Servers finden Sie unter [Erste Schritte mit Azure Multi-Factor Authentication-Server](multi-factor-authentication-get-started-server.md).
2. Wählen Sie auf der Benutzeroberfläche von Azure Multi-Factor Authentication-Server das AD FS-Symbol aus und wählen Sie dann die Optionen "Benutzerregistrierung zulassen" und "Methodenauswahl durch Benutzer zulassen" aus.
3. Wählen Sie ggf. weitere Optionen aus.
4. Klicken Sie auf „AD FS-Adapter installieren“.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Wenn der Computer zu einer Domäne gehört und die Active Directory-Konfiguration zum Sichern der Kommunikation zwischen dem AD FS-Adapter und dem Multi-Factor Authentication-Dienst unvollständig ist, wird der Schritt "Active Directory" angezeigt. Klicken Sie auf die Schaltfläche "Weiter", um diese Konfiguration automatisch abzuschließen, oder aktivieren Sie das Kontrollkästchen "Automatische Active Directory-Konfiguration überspringen und Einstellungen manuell konfigurieren" und klicken Sie dann auf "Weiter".
6. Wenn der Computer zu einer Domäne gehört und die Active Directory-Konfiguration zum Sichern der Kommunikation zwischen dem AD FS-Adapter und dem Multi-Factor Authentication-Dienst unvollständig ist, wird der Schritt "Lokale Gruppe" angezeigt. Klicken Sie auf die Schaltfläche "Weiter", um diese Konfiguration automatisch abzuschließen, oder aktivieren Sie das Kontrollkästchen "Automatische Konfiguration der lokalen Gruppe überspringen und Einstellungen manuell konfigurieren" und klicken Sie dann auf "Weiter".
7. Der Installations-Assistent wird geöffnet. Klicken Sie auf "Weiter", damit Azure Multi-Factor Authentication-Server die Gruppe "PhoneFactor Admins" erstellt und dieser Gruppe das AD FS-Dienstkonto hinzufügt.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Klicken Sie im Schritt "Installer starten" auf "Weiter".
9. Klicken Sie im Multi-Factor Authentication AD FS Adapter-Installer auf "Weiter".
10. Klicken Sie auf "Schließen", wenn die Installation abgeschlossen ist.
11. Der Adapter wurde installiert und muss nun bei AD FS registriert werden. Öffnen Sie Windows PowerShell, und führen Sie Folgendes aus: „C:\\Programme\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1“. <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Jetzt muss die globale Authentifizierungsrichtlinie in AD FS bearbeitet werden, damit der neu registrierte Adapter verwendet werden kann. Navigieren Sie in der AD FS-Verwaltungskonsole zum Knoten "Authentifizierungsrichtlinien" und klicken Sie im Abschnitt "Multi-Factor Authentication" neben dem Unterabschnitt "Globale Einstellungen" auf den Link "Bearbeiten". Wählen Sie im Fenster "Globale Authentifizierungsrichtlinie bearbeiten" als zusätzliche Authentifizierungsmethode "Multi-Factor Authentication" aus und klicken Sie dann auf "OK". Der Adapter wird als "WindowsAzureMultiFactorAuthentication" registriert. Sie müssen den AD FS-Dienst neu starten, damit die Registrierung wirksam wird.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

An diesem Punkt ist der Multi-Factor Authentication-Server als zusätzlicher Authentifizierungsanbieter für die Verwendung mit AD FS eingerichtet.

## So installieren Sie den eigenständigen AD FS-Adapter mithilfe des Webdienst-SDKs
1. Installieren Sie das Webdienst-SDK auf dem Server mit Multi-Factor Authentication-Server.
2. Kopieren Sie die Dateien "MultiFactorAuthenticationAdfsAdapterSetup64.msi", "Register-MultiFactorAuthenticationAdfsAdapter.ps1", "Unregister-MultiFactorAuthenticationAdfsAdapter.ps1" und "MultiFactorAuthenticationAdfsAdapter.config" aus dem Verzeichnis "\\Program Files\\Multi-Factor Authentication Server" auf den Server, auf dem Sie den AD FS-Adapter installieren möchten.
3. Führen Sie "MultiFactorAuthenticationAdfsAdapterSetup64.msi" aus.
4. Klicken Sie im Multi-Factor Authentication AD FS-Adapter-Installer auf "Weiter", um die Installation durchzuführen.
5. Klicken Sie auf die Schaltfläche "Schließen", wenn die Installation abgeschlossen ist.
6. Bearbeiten Sie die Datei "MultiFactorAuthenticationAdfsAdapter.config" wie folgt:

Schritt "MultiFactorAuthenticationAdfsAdapter.config"| Unterschritt
:------------- | :------------- |
Legen Sie den Knoten "UseWebServiceSdk" auf "true" fest.||
Legen Sie "WebServiceSdkUrl" auf die URL des Multi-Factor Authentication-Webdienst-SDKs fest.||
Option 1: Konfigurieren Sie das Webdienst-SDK mit einem Benutzernamen und Kennwort.|<ol><li>Legen Sie „WebServiceSdkUsername“ auf ein Konto fest, das Mitglied der Sicherheitsgruppe „PhoneFactor Admins“ ist. Verwenden Sie das <domain><username>-Format.<li>Legen Sie „WebServiceSdkPassword“ auf das entsprechende Kontokennwort fest.</li></ol>
Option 2: Konfigurieren Sie das Webdienst-SDK mit einem Clientzertifikat.|<ol><li>Rufen Sie ein Clientzertifikat von einer Zertifizierungsstelle für den Server mit dem Webdienst-SDK ab.</li><li>Importieren Sie das Clientzertifikat in den persönlichen Zertifikatspeicher des lokalen Computers auf dem Server mit dem Webdienst-SDK. Hinweis: Stellen Sie sicher, dass sich das öffentliche Zertifikat der Zertifizierungsstelle unter den vertrauenswürdigen Stammzertifikaten befindet.</li><li>Exportieren Sie die öffentlichen und privaten Schlüssel des Clientzertifikats in eine PFX-Datei.</li><li>Exportieren Sie den öffentlichen Schlüssel im Base64-Format in eine CER-Datei.</li><li>Stellen Sie im Server-Manager sicher, dass die Funktion „Web Server (IIS)\\Web Server\\Security\\Client Certificate Mapping Authentication“ installiert ist.</li><li>Ist dies nicht der Fall, wählen Sie „Rollen und Features hinzufügen“ aus, um diese Funktion hinzuzufügen.</li><li>Doppelklicken Sie in IIS-Manager auf den Konfigurations-Editor der Website, die das virtuelle Verzeichnis des Webdienst-SDK enthält. Hinweis: Es ist sehr wichtig, dies auf Websiteebene und nicht auf Ebene des virtuellen Verzeichnisses vorzunehmen.</li><li>Navigieren Sie zum Abschnitt „system.webServer/security/authentication/iisClientCertificateMappingAuthentication“.</li><li>Legen Sie „Enabled“ auf „true“ fest.</li><li>Legen Sie „OneToOneCertificateMappingsEnabled“ auf „true“ fest.</li><li>Klicken Sie auf die Schaltfläche „...“ neben „OneToOneMappings“.</li><li>Klicken Sie auf den Link zum Hinzufügen.</li><li>Öffnen Sie die zuvor exportierte Base64-CER-Datei. Entfernen Sie -----BEGIN CERTIFICATE-----, -----END CERTIFICATE----- sowie alle Zeilenumbrüche. Kopieren Sie die resultierende Zeichenfolge.</li><li>Legen Sie das Zertifikat auf die im vorherigen Schritt kopierte Zeichenfolge fest.</li><li>Legen Sie „Aktiviert“ auf „true“ fest.</li><li>Legen Sie „userName“ auf ein Konto fest, das Mitglied der Sicherheitsgruppe „PhoneFactor Admins“ ist. Verwenden Sie das <domain><username>-Format.</li><li>Legen Sie das Kennwort auf das entsprechende Kontokennwort fest.</li><li>Schließen Sie den Auflistungs-Editor.</li><li>Klicken Sie auf den Link „Anwenden“.</li><li>Navigieren Sie zum virtuellen Verzeichnis des Webdienst-SDK.</li><li>Doppelklicken Sie auf „Authentifizierung“.</li><li>Vergewissern Sie sich, dass „ASP.NET-Identitätswechsel“ und die Standardauthentifizierung aktiviert und alle anderen Elemente deaktiviert sind.</li><li>Navigieren Sie erneut zum virtuellen Verzeichnis des Webdienst-SDK.</li><li>Doppelklicken Sie auf die SSL-Einstellungen.</li><li>Legen Sie die Clientzertifikate auf „Akzeptieren“ fest, und klicken Sie auf „Anwenden“.</li><li>Kopieren Sie die zuvor exportierte PFX-Datei auf den Server mit dem AD FS-Adapter.</li><li>Importieren Sie die PFX-Datei in den persönlichen Zertifikatspeicher des lokalen Computers.</li><li>Wählen Sie im Kontextmenü die Option „Private Schlüssel verwalten“, und gewähren Sie Lesezugriff für das Konto, mit dem der Dienst der Active Directory-Verbunddienste angemeldet ist.</li><li>Öffnen Sie das Clientzertifikat, und kopieren Sie den Fingerabdruck aus der Registerkarte „Details“.</li><li>Legen Sie in der Datei „MultiFactorAuthenticationAdfsAdapter.config“ „WebServiceSdkCertificateThumbprint“ auf die im vorherigen Schritt kopierte Zeichenfolge fest.</li></ol>
Bearbeiten Sie das Skript „Register-MultiFactorAuthenticationAdfsAdapter.ps1“, indem Sie „-ConfigurationFilePath“ <path> am Ende des Befehls „Register-AdfsAuthenticationProvider“ hinzufügen, wobei <path> der vollständige Pfad zur Datei „MultiFactorAuthenticationAdfsAdapter.config“ ist.|


Führen Sie nun das Skript „\\Programme\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1“ in PowerShell aus, um den Adapter zu registrieren. Der Adapter wird als "WindowsAzureMultiFactorAuthentication" registriert. Sie müssen den AD FS-Dienst neu starten, damit die Registrierung wirksam wird.




























 

 


 

 


 





 


 

























































































 


 

 






 

<!---HONumber=Oct15_HO3-->