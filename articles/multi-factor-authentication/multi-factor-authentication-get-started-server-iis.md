<properties 
	pageTitle="IIS-Authentifizierung und Azure Multi-Factor Authentication-Server" 
	description="Dies ist die Azure Multi-Factor Authentication-Seite, die bei der Bereitstellung der IIS-Authentifizierung und des Azure Multi-Factor Authentication-Servers Unterstützung bietet." 
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

# IIS-Authentifizierung

Im Bereich IIS-Authentifizierung des Azure Multi-Factor Authentication-Servers können Sie die IIS-Authentifizierung für die Integration mit Microsoft IIS-Webanwendungen aktivieren und konfigurieren. Der Azure Multi-Factor Authentication-Server installiert ein Plug-In, das Anforderungen an den IIS-Webserver filtern kann, um Multi-Factor Authentication hinzuzufügen. Das IIS-Plug-In bietet Unterstützung für die formularbasierte Authentifizierung und die integrierte Windows-HTTP-Authentifizierung. Vertrauenswürdige IP-Adressen können auch konfiguriert werden, um interne IP-Adressen der zweistufigen Authentifizierung auszunehmen.


![IIS-Authentifizierung](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## Verwenden formularbasierter IIS-Authentifizierung mit dem Azure Multi-Factor Authentication-Server

Um eine IIS-Webanwendung zu sichern, die die formularbasierte Authentifizierung verwendet, installieren Sie den Azure Multi-Factor Authentication-Server auf dem IIS-Webserver, und konfigurieren Sie den Server wie im folgenden Verfahren dargestellt.

1. Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol "IIS-Authentifizierung".
2. Klicken Sie auf die Registerkarte "Formularbasiert".
3. Klicken Sie auf die Schaltfläche "Hinzufügen".
4. Damit die Variablen für Benutzername, Kennwort und Domäne automatisch erkannt werden, geben Sie die Anmelde-URL (z. B. https://localhost/contoso/auth/login.aspx) im Dialogfeld "Formularbasierte Website automatisch konfigurieren" ein, und klicken Sie auf "OK".
5. Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erforderlich", wenn alle Benutzer in den Server importiert wurden oder werden und die mehrstufige Authentifizierung verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert.
6. Wenn die Seitenvariablen nicht automatisch erkannt werden können, klicken Sie im Dialogfeld "Formularbasierte Website automatisch konfigurieren" auf die Schaltfläche "Manuell angeben".
7. Geben Sie im Dialogfeld "Formularbasierte Website hinzufügen" im Feld "Sende-URL" die URL der Anmeldeseite und optional einen Anwendungsnamen ein. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt. Weitere Informationen zur Sende-URL finden Sie in der Hilfedatei. 
8. Wählen Sie das richtige Anforderungsformat. Dieses ist für die meisten Webanwendungen auf "POST oder GET" festgelegt.
9. Geben Sie die Variablen für den Benutzernamen, das Kennwort und die Domäne ein (sofern auf der Anmeldeseite angezeigt). Sie müssen in einem Webbrowser zur Anmeldeseite navigieren, mit der rechten Maustaste auf die Seite klicken und "Quelltext anzeigen" auswählen, um die Namen der Eingabefelder auf der Seite zu ermitteln.
10. Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erfordern", wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert. Weitere Informationen zu dieser Funktion finden Sie in der Hilfedatei.
11.  Klicken Sie auf die Schaltfläche "Erweitert", um die erweiterten Einstellungen zu überprüfen, einschließlich der Optionen zum Auswählen einer benutzerdefinierten Verweigerungs-Auslagerungsdatei, zum Zwischenspeichern erfolgreicher Authentifizierungen bei der Website für einen bestimmten Zeitraum mithilfe von Cookies und zum Auswählen des Authentifizierungsverfahrens für die primären Anmeldeinformationen entweder gegen die Windows-Domäne, ein LDAP-Verzeichnis, oder einen RADIUS-Server. Klicken Sie abschließend auf die Schaltfläche "OK", um zum Dialogfeld "Formularbasierte Website hinzufügen" zurückzukehren. Weitere Informationen zu den erweiterten Einstellungen finden Sie in der Hilfedatei.
12. Klicken Sie auf die Schaltfläche "OK".
13. Sobald die URL und die Seitenvariablen erkannt bzw. eingegeben wurden, werden die Websitedaten im Bereich "Formularbasiert" angezeigt.
14. Lesen Sie den nachfolgenden Abschnitt "Aktivieren des IIS-Plug-Ins für den Azure Multi-Factor Authentication-Server", um die Konfiguration der IIS-Authentifizierung abzuschließen. 

## Verwenden der integrierten Windows-Authentifizierung mit dem Azure Multi-Factor Authentication-Server

Um eine IIS-Webanwendung zu sichern, die die integrierte Windows-HTTP-Authentifizierung verwendet, installieren Sie den Azure Multi-Factor Authentication-Server auf dem IIS-Webserver, und konfigurieren Sie den Server wie im folgenden Verfahren dargestellt.

1. Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol "IIS-Authentifizierung".
2. Klicken Sie auf die Registerkarte "HTTP". Klicken Sie auf die Registerkarte "Formularbasiert".
3. Klicken Sie auf die Schaltfläche "Hinzufügen".
4. Geben Sie im Dialogfeld "Basis-URL hinzufügen" im Feld "Basis-URL" die URL für die Website ein, auf der die HTTP-Authentifizierung erfolgt (z. B. http://localhost/owa), und optional einen Anwendungsnamen. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt.
5. Passen Sie die Leerlaufzeitüberschreitung und die maximale Sitzungsdauer an, wenn die Standardwerte nicht ausreichend sind.
6. Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erforderlich", wenn alle Benutzer in den Server importiert wurden oder werden und die mehrstufige Authentifizierung verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert. Weitere Informationen zu dieser Funktion finden Sie in der Hilfedatei. 
7. Aktivieren Sie bei Bedarf das Kontrollkästchen für das Cookie zur Zwischenspeicherung.
8. Klicken Sie auf die Schaltfläche "OK".
9. Lesen Sie den nachfolgenden Abschnitt [Aktivieren des IIS-Plug-Ins für den Azure Multi-Factor Authentication-Server](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server), um die Konfiguration der IIS-Authentifizierung abzuschließen. 


## Aktivieren von IIS-Plug-Ins für Azure Multi-Factor Authentication-Server

Nachdem Sie die URLs und Einstellungen für die formularbasierte oder die HTTP-Authentifizierung konfiguriert haben, müssen Sie die Speicherorte auswählen, in denen die IIS-Plug-Ins von Azure Multi-Factor Authentication in IIS geladen und aktiviert werden sollen. Gehen Sie dazu wie folgt vor:

1. Falls IIS 6 ausgeführt wird, klicken Sie auf die Registerkarte "ISAPI", und wählen Sie die Website, auf der die Webanwendung läuft (z. B. die Standardwebsite), um das ISAPI-Filter-Plug-In von Azure Multi-Factor Authentication für diese Website zu aktivieren.
2. Falls IIS 7 oder höher ausgeführt wird, klicken Sie auf die Registerkarte "Systemeigenes Modul", und wählen Sie den Server, Website(s) oder Anwendung(en) aus, um das IIS-Plug-In auf der/den gewünschten Ebene/n zu aktivieren.
3. Aktivieren Sie oben im Bildschirm das Kontrollkästchen "IIS-Authentifizierung aktivieren". Die ausgewählte IIS-Anwendung wird jetzt mit Azure Multi-Factor Authentication gesichert. Stellen Sie sicher, dass die Benutzer in den Server importiert wurden. Wenn Sie eine Positivliste von internen IP-Adressen verwenden möchten, damit bei der Anmeldung auf der Website von diesen Orten aus keine zweistufige Authentifizierung erforderlich ist, finden Sie weitere Informationen im Abschnitt "Vertrauenswürdige IPs" unten. 


## Vertrauenswürdige IP-Adressen

Über vertrauenswürdige IP-Adressen können Benutzer Azure Multi-Factor Authentication bei Websiteanforderungen umgehen, die von bestimmten IP-Adressen oder Subnetzen stammen. Beispielsweise können Benutzer, die sich vom Büro aus anmelden, von Azure Multi-Factor Authentication ausschließen. Dazu geben Sie das Bürosubnetz als Eintrag in "Vertrauenswürdige IPs" an. Verwenden Sie zum Konfigurieren von vertrauenswürdigen IP-Adressen folgendes Verfahren:

1. Klicken Sie im Abschnitt "IIS-Authentifizierung" auf die Registerkarte "Vertrauenswürdige IPs". 
2. Klicken Sie auf die Schaltfläche "Hinzufügen".
3. Im daraufhin angezeigten Dialogfeld "Vertrauenswürdige IP hinzufügen" wählen Sie das Optionsfeld "Eine IP", "IP-Bereich" oder "Subnetz" aus.
4. Geben Sie die IP-Adresse, den IP-Adressbereich oder das Subnetz ein, das der Positivliste hinzugefügt werden soll. Wenn Sie ein Subnetz eingeben, wählen Sie die entsprechende Netzmaske aus, und klicken Sie auf die Schaltfläche "OK". Der Eintrag wird der Positivliste hinzugefügt.

<!---HONumber=July15_HO4-->