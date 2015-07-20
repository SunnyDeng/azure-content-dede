<properties 
	pageTitle="Durchführen eines Upgrades vom PhoneFactor-Agent auf den Azure Multi-Factor Authentication-Server" 
	description="In diesem Dokument werden die ersten Schritte mit dem Azure MFA-Server und das Durchführen eines Upgrades des älteren Phonefactor-Agents beschrieben." 
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

# Durchführen eines Upgrades vom PhoneFactor-Agent auf den Azure Multi-Factor Authentication-Server

Für das Upgrade von Version 5.x oder früher des PhoneFactor-Agents auf den Azure Multi-Factor Authentication-Server müssen der PhoneFactor-Agent und alle zugehörigen Komponenten deinstalliert werden, bevor der Multi-Factor Authentication-Server und die zugehörigen Komponenten installiert werden können.

## So führen Sie ein Upgrade vom PhoneFactor-Agent auf den Azure Multi-Factor Authentication-Server durch
<ol>
<li>Sichern Sie zunächst die PhoneFactor-Datendatei. Der Standardinstallationspfad lautet "C:\\Programme\\PhoneFactor\\Data\\Phonefactor.pfdata".


<li>Wenn das Benutzerportal installiert ist:</li>
<ol>
<li>Navigieren Sie zum Installationsordner, und sichern Sie die Datei "Web.config". Der Standardinstallationspfad lautet "C:\inetpub\wwwroot\PhoneFactor".</li>


<li>Wenn Sie dem Portal benutzerdefinierte Designs hinzugefügt haben, sichern Sie den benutzerdefinierten Ordner unter dem Verzeichnis "C:\inetpub\wwwroot\PhoneFactor\App_Themes".</li>


<li>Deinstallieren Sie das Benutzerportal entweder über den PhoneFactor-Agent (nur verfügbar, wenn auf demselben Server wie der PhoneFactor-Agent installiert) oder über "Programme und Funktionen" von Windows.</li></ol>




<li>Wenn der Webdienst der mobilen App installiert ist: <ol> <li>Wechseln Sie zum Installationsordner, und sichern Sie die Datei "Web.config". Der Standardinstallationspfad lautet "C:\\inetpub\\wwwroot\\PhoneFactorPhoneAppWebService".</li> <li>Deinstallieren Sie den Webdienst der mobilen App über "Programme und Funktionen" von Windows.</li></ol>

<li>Wenn das Webdienst-SDK installiert ist, deinstallieren Sie es über den PhoneFactor-Agent oder über "Programme und Funktionen" von Windows.

<li>Deinstallieren Sie den PhoneFactor-Agent über "Programme und Funktionen" von Windows.

<li>Installieren Sie den Multi-Factor Authentication-Server. Beachten Sie, dass der Installationspfad aus der Registrierung der vorherigen PhoneFactor-Agent-Installation übernommen wird. Daher erfolgt die Installation unter demselben Pfad (z. B. "C:\\Programme\\PhoneFactor"). Für Neuinstallationen wird ein anderer Standardinstallationspfad verwendet (z. B. "C:\\Programme\\Multi-Factor Authentication Server"). Während der Installation wird die noch vorhandene PhoneFactor-Agent-Datendatei aktualisiert. Deshalb sind nach dem Installieren des neuen Multi-Factor Authentication-Servers die Benutzer und Einstellungen noch vorhanden.

<li>Aktivieren Sie den Multi-Factor Authentication-Server, wenn Sie dazu aufgefordert werden, und stellen Sie sicher, dass er der richtigen Replikationsgruppe zugewiesen ist.

<li>Wenn das Webdienst-SDK zuvor installiert war, installieren Sie das neue Webdienst-SDK über die Benutzeroberfläche des Multi-Factor Authentication-Servers. Beachten Sie, dass der Standardname des virtuellen Verzeichnisses jetzt "MultiFactorAuthWebServiceSdk" statt "PhoneFactorWebServiceSdk" lautet. Wenn Sie den vorherigen Namen verwenden möchten, müssen Sie den Namen des virtuellen Verzeichnisses während der Installation ändern. Wenn Sie hingegen die Verwendung des neuen Standardnamens während der Installation zulassen, müssen Sie die URL in allen Anwendungen ändern, die auf das Webdienst-SDK verweisen, z. B. im Benutzerportal und im Webdienst der mobilen App, damit sie auf das richtige Verzeichnis verweist.

<li>Wenn das Benutzerportal zuvor auf dem PhoneFactor-Agent-Server installiert war, installieren Sie das neue Multi-Factor Authentication-Benutzerportal über die Benutzeroberfläche des Multi-Factor Authentication-Servers. Beachten Sie, dass der Standardname des virtuellen Verzeichnisses jetzt "MultiFactorAuth" statt "PhoneFactor" lautet. Wenn Sie den vorherigen Namen verwenden möchten, müssen Sie den Namen des virtuellen Verzeichnisses während der Installation ändern. Wenn Sie hingegen die Verwendung des neuen Standardnamens bei der Installation zulassen, klicken Sie im Multi-Factor Authentication-Server auf das Benutzerportal-Symbol, und aktualisieren Sie auf der Registerkarte "Einstellungen" die Benutzerportal-URL.

<li>Wenn das Benutzerportal und/oder der Webdienst der mobilen App zuvor auf einem anderen Server als der PhoneFactor-Agent installiert war: <ol> <li>Wechseln Sie zum Installationsspeicherort (z. B. "C:\\Programme\\PhoneFactor"), und kopieren Sie das entsprechende Installationsprogramm bzw. die entsprechenden Installationsprogramme auf den anderen Server. Für das Benutzerportal und den Webdienst der mobilen App gibt es 32-Bit- und 64-Bit-Installationsprogramme. Ihre Namen lauten "MultiFactorAuthenticationUserPortalSetupXX.msi" bzw. "MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi".</li> <li>Um das Benutzerportal auf dem Webserver zu installieren, öffnen Sie eine Eingabeaufforderung als Administrator, und führen Sie "MultiFactorAuthenticationUserPortalSetupXX.msi" aus. Beachten Sie, dass der Standardname des virtuellen Verzeichnisses jetzt "MultiFactorAuth" statt "PhoneFactor" lautet. Wenn Sie den vorherigen Namen verwenden möchten, müssen Sie den Namen des virtuellen Verzeichnisses während der Installation ändern. Wenn Sie hingegen die Verwendung des neuen Standardnamens bei der Installation zulassen, klicken Sie im Multi-Factor Authentication-Server auf das Benutzerportal-Symbol, und aktualisieren Sie auf der Registerkarte "Einstellungen" die Benutzerportal-URL. Vorhandene Benutzer müssen über die neue URL informiert werden.</li> <li>Wechseln Sie zum Installationsspeicherort des Benutzerportals (z. B. "C:\\inetpub\\wwwroot\\MultiFactorAuth"), und bearbeiten Sie die Datei "Web.config". Kopieren Sie die Werte in den Abschnitten "appSettings" und "applicationSettings" aus der ursprünglichen Datei "web.config", die vor dem Upgrade auf die neue Datei "web.config" gesichert wurde. Wenn beim Installieren des Webdienst-SDK der neue Standardname des virtuellen Verzeichnisses beibehalten wurde, ändern Sie die URL im Abschnitt "applicationSettings", damit sie auf den richtigen Speicherort verweist. Wenn in der vorherigen Datei "Web.config" weitere Standardeinstellungen geändert wurden, wenden Sie diese Änderungen auf die neue Datei "web.config" an.</li> <li>Um den Webdienst der mobilen App auf dem Webserver zu installieren, öffnen Sie eine Eingabeaufforderung als Administrator, und führen Sie "MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi" aus. Beachten Sie, dass der Standardname des virtuellen Verzeichnisses jetzt "MultiFactorAuthMobileAppWebService" statt "PhoneFactorPhoneAppWebService" lautet. Wenn Sie den vorherigen Namen verwenden möchten, müssen Sie den Namen des virtuellen Verzeichnisses während der Installation ändern. Sie sollten einen kürzeren Namen wählen, damit er von den Endbenutzern einfacher auf ihren Mobilgeräten eingegeben werden kann. Wenn Sie hingegen die Verwendung des neuen Standardnamens bei der Installation zulassen, klicken Sie im Multi-Factor Authentication-Server auf das Symbol "Mobile App", und aktualisieren Sie die URL des Webdiensts der mobilen App.</li> <li>Wechseln Sie zum Installationsspeicherort des Webdiensts der mobilen App (z. B. "C:\\inetpub\\wwwroot\\MultiFactorAuthMobileAppWebService"), und bearbeiten Sie die Datei "Web.config". Kopieren Sie die Werte in den Abschnitten "appSettings" und "applicationSettings" aus der ursprünglichen Datei "web.config", die vor dem Upgrade auf die neue Datei "web.config" gesichert wurde. Wenn beim Installieren des Webdienst-SDK der neue Standardname des virtuellen Verzeichnisses beibehalten wurde, ändern Sie die URL im Abschnitt "applicationSettings", damit sie auf den richtigen Speicherort verweist. Wenn in der vorherigen Datei "Web.config" weitere Standardeinstellungen geändert wurden, wenden Sie diese Änderungen auf die neue Datei "web.config" an.</li></ol>


 


 

<!---HONumber=July15_HO2-->