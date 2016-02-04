<properties
	pageTitle="Azure AD Connect: Problembehebung bei Konnektivitätsproblemen| Microsoft Azure"
	description="Erklärt, wie Konnektivitätsprobleme mit Azure AD Connect behoben werden können"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/11/2016"
	ms.author="andkjell"/>

# Problembehebung bei Konnektivitätsproblemen mit Azure AD Connect
Dieser Artikel erklärt, wie die Konnektivität zwischen Azure AD Connect und Azure AD funktioniert und wie Konnektivitätsprobleme behoben werden können. Diese Probleme können insbesondere in einer Umgebung mit einem Proxyserver auftreten.

## Konnektivitätsprobleme mit dem Installations-Assistent beheben
Azure AD Connect ist für die Verbindung mit Azure AD von zwei verschiedenen Konfigurationsmethoden abhängig. Da sie .Net-Anwendungen sind, benötigen der Installations-Assistent und das eigentliche Synchronisierungsmodul machine.config, um richtig konfiguriert zu werden. Außerdem besteht eine Abhängigkeit auf den Anmelde-Assistent und winhttp für eine funktionsfähige Konfiguration angewiesen.

In diesem Artikel zeigen wir Ihnen, wie Fabrikam durch seinen Proxy mit Azure AD verbunden wird. Der Proxyserver heißt fabrikamproxy und verwendet Port 8080.

Zunächst stellen wir sicher, dass [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) richtig konfiguriert ist. ![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [AZURE.NOTE]Laut einiger Blogs sollen Änderungen stattdessen an „miiserver.exe.config“ vorgenommen werden. Diese Datei wird jedoch mit jeder Aktualisierung überschrieben. Selbst wenn das System nach der Erstinstallation funktionieren sollte, ist das nach der ersten Aktualisierung nicht mehr der Fall. Daher wird empfohlen, stattdessen „machine.config“ zu aktualisieren.

Danach stellen wir sicher, dass winhttp konfiguriert wurde. Überprüfen Sie das mit [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity). ![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

Der Proxyserver muss die erforderlichen URLs geöffnet haben. Die offizielle Liste ist unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) zu finden.

Die folgende Tabelle zeigt die Grundvoraussetzungen für eine Verbindung mit Azure AD. Diese Liste beinhaltet keine optionalen Funktionen, wie die Kennwortrückschreibung oder Azure AD Connect Health. Sie soll bei der Problembehebung während der Erstkonfiguration helfen.

| URL | Port | Beschreibung |
| ---- | ---- | ---- |
| mscrl.microsoft.com | HTTP/80 | Wird verwendet um CRL-Listen (Zertifikatsperrlisten) herunterzuladen. |
| *.verisign.com | HTTP/80 |Wird verwendet um Zertifikatsperrlisten herunterzuladen. |
| *.windows.net | HTTPS/443 |Wird verwendet um sich in Azure AD anzumelden. |
| *.microsoftonline.com |HTTPS/443 | Wird verwendet, um das Azure AD-Verzeichnis zu konfigurieren und um Daten zu importieren und exportieren. |

## Fehler im Assistenten
Der Installations-Assistent verwendet zwei verschiedene Sicherheitskontexte. Auf der Seite **Mit Azure AD verbinden** verwendet er den aktuell angemeldeten Benutzer. Auf der Seite **Konfigurieren** wechselt er auf das [Konto, das den Dienst für das Synchronisierungsmodul ausführt](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). Die Proxykonfigurationen, die wir vornehmen, gelten für den ganzen Computer. Daher werden auftretende Probleme höchstwahrscheinlich bereits auf der **Mit Azure AD verbinden**-Seite des Assistenten angezeigt.

Dies sind die häufigsten Fehler im Installations-Assistenten:

### Der Installations-Assistent wurde nicht richtig konfiguriert
Dieser Fehler tritt auf, wenn der Assistent den Proxy selbst nicht erreichen kann. ![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Falls dies angezeigt wird, überprüfen Sie, ob [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) richtig konfiguriert wurde.
- Falls dies in Ordnung ist, befolgen Sie bitte die Schritte zur [Überprüfung der Proxykonnektivität](#verify-proxy-connectivity), um zu sehen, ob die Probleme außerhalb des Assistenten ebenfalls auftreten.

### Der Anmelde-Assistent wurde nicht richtig konfiguriert
Dieser Fehler tritt auf, wenn der Anmelde-Assistent den Proxy nicht erreichen kann oder der Proxy die Anfrage nicht zulässt. ![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Falls dies angezeigt wird, prüfen Sie die Proxykonfiguration in [netsh](active-directory-aadconnect-prerequisites.md#connectivity).![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Falls dies in Ordnung ist, befolgen Sie bitte die Schritte zur [Überprüfung der Proxykonnektivität](#verify-proxy-connectivity), um zu sehen, ob die Probleme außerhalb des Assistenten ebenfalls auftreten.

### Das Kennwort kann nicht überprüft werden
Falls sich der Installations-Assistent erfolgreich mit Azure AD verbinden konnte, aber das Kennwort selbst nicht überprüft werden kann, wird Ihnen Folgendes angezeigt: ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- Handelt es sich um ein temporäres Kennwort, das geändert werden muss? Handelt es sich um das richtige Kennwort? Versuchen Sie, sich bei https://login.microsoftonline.com (auf einem anderen Server als dem Azure AD Connect-Server) anzumelden und überprüfen Sie, ob das Konto verwendbar ist.
- Ist die MFA (Multi-Factor Authentication/mehrstufige Authentifizierung) für diesen Benutzer aktiviert? Falls ja, deaktivieren Sie diese.

### Proxykonnektivität überprüfen
Um nachzuprüfen, ob der Azure AD Connect-Server über eine Verbindung zum Proxy und dem Internet verfügt, verwenden wir einige PowerShell-Teile, um festzustellen, ob der Proxy Webanforderungen zulässt oder nicht. Führen Sie in PowerShell `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` aus. (Streng genommen ist der erste Aufruf an https://login.microsoftonline.com gerichtet. Dies würde auch funktionieren, jedoch antwortet der andere URI schneller.)

PowerShell wird die Konfiguration aus machine.config verwenden, um den Proxy zu kontaktieren. Die Einstellungen in winhttp/netsh sollten sich nicht auf diese Cmdlets auswirken.

Wenn der Proxy richtig konfiguriert ist, sollten Sie einen Erfolgsstatus erhalten: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Falls Sie die Meldung **Verbindung mit dem Remoteserver konnte nicht hergestellt werden** erhalten, versucht PowerShell entweder gerade einen direkten Aufruf ohne den Proxy zu verwenden oder das DNS ist nicht richtig konfiguriert. Gehen Sie sicher, dass die **machine.config**-Datei richtig konfiguriert ist. ![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Falls der Proxy nicht richtig konfiguriert ist, tritt folgender Fehler auf: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png) ![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| Error | Fehlertext | Kommentar |
| ---- | ---- | ---- |
| 403 | Verboten (403) | Der Proxy wurde für die angeforderte URL nicht geöffnet. Rufen Sie die Proxykonfigurationen erneut auf und stellen Sie sicher, dass die [URLs](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) geöffnet wurden. |
| 407 | Proxyauthentifizierung erforderlich | Der Proxyserver erfordert eine Anmeldung, die nicht erfolgt ist. Stellen Sie sicher, dass Sie in den Konfigurationen von machine.config eine entsprechende Einstellung vorgenommen haben, falls Ihr Proxyserver eine Authentifizierung erfordert. Gehen Sie außerdem sicher, dass Sie sowohl für den Benutzer, der den Assistenten ausführt, als auch für das Dienstkonto Domänenkonten verwenden. |

## Das Kommunikationsmuster zwischen Azure AD Connect und Azure AD
Falls Sie alle obigen Schritte ausgeführt haben und immer noch keine Verbindung herstellen können, sollten Sie sich Ihre Netzwerkprotokolle ansehen. Dieser Abschnitt dokumentiert ein normales und erfolgreiches Konnektivitätsmuster. Er zeigt auch häufig auftretende Meldungen, die Sie aber ignorieren können, wenn Sie die Netzwerkprotokolle lesen.

- Es wird versucht, https://dc.services.visualstudio.com aufzurufen. Für eine erfolgreiche Installation ist es nicht erforderlich, dass diese Versuche im Proxy geöffnet sind. Sie können daher ignoriert werden.
- Sie werden sehen, dass die DNS-Auflösung den tatsächlichen Host im DNS-Namen von nsatc.net und anderen Namespaces anzeigt, anstatt unter microsoftonline.com. Es werden jedoch keine Webdienstanfragen an die eigentlichen Servernamen gestellt und Sie müssen diese dem Proxy nicht hinzufügen.
- Die Endpunkte „adminwebservice“ und „provisioningapi“ (siehe unten in den Protokollen) sind Ermittlungsendpunkte und werden benutzt, um die tatsächlich zu verwendeten Endpunkte zu finden. Sie unterschieden sich je nach Ihrer Region.

### Verweiseproxyprotokolle
Hier nun ein Auszug eines echten Proxyprotokolls und der Seite des Installations-Assistenten, von der er entnommen wurde. (Doppelte Einträge, die zum selben Endpunkt führen, wurden entfernt). Sie können ihn als Referenz für Ihre eigenen Proxy- und Netzwerkprotokolle verwenden. Die tatsächlichen Endpunkte können in Ihrer Umgebung abweichen (besonders die *kursiv* gesetzten).

**Herstellen einer Verbindung mit Azure AD**

Time | URL
--- | ---
1/11/2016 8:31 | connect://login.microsoftonline.com:443
1/11/2016 8:31 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:32 | connect://login.microsoftonline.com:443
1/11/2016 8:33 | connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:33 | connect://*bwsc02-relay*.microsoftonline.com:443

**Konfigurieren**

Time | URL
--- | ---
1/11/2016 8:43 | connect://login.microsoftonline.com:443
1/11/2016 8:43 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:43 | connect://login.microsoftonline.com:443
1/11/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connect://*bba900-anchor*.microsoftonline.com:443
1/11/2016 8:44 | connect://login.microsoftonline.com:443
1/11/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:44 | connect://login.microsoftonline.com:443
1/11/2016 8:46 | connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | connect://*bwsc02-relay*.microsoftonline.com:443

**Erste Synchronisierung**

Time | URL
--- | ---
1/11/2016 8:48 | connect://login.windows.net:443
1/11/2016 8:49 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:49 | connect://*bba900-anchor*.microsoftonline.com:443
1/11/2016 8:49 | connect://*bba800-anchor*.microsoftonline.com:443

<!----HONumber=AcomDC_0121_2016-->