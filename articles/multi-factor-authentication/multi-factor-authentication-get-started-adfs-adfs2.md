<properties 
	pageTitle="Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit AD FS 2.0"
	description="Auf dieser Seite zur Azure Multi-Factor Authentication werden die ersten Schritte mit Azure MFA und AD FS 2.0 beschrieben."
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
# Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit AD FS 2.0

Wenn Ihre Organisation einen Verbund mit Azure Active Directory bildet und Sie lokale und Cloud-Ressourcen sichern möchten, können Sie dazu Azure Multi-Factor Authentication Server verwenden, indem Sie diesen für die Verwendung mit AD FS so konfigurieren, dass die Multi-Factor Authentication bei wichtigen Endpunkten ausgelöst wird.

In dieser Dokumentation wird die Verwendung von Azure Multi-Factor Authentication-Server mit AD FS 2.0 behandelt. Informationen zur Verwendung von Multi-Factor Authentication mit Windows Server 2012 R2 AD FS finden Sie unter [Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## AD FS 2.0 – Proxy
Zum Sichern von AD FS 2.0 mit einem Proxy installieren Sie Azure Multi-Factor Authentication-Server auf dem AD FS-Proxyserver und konfigurieren den Server wie folgt.

### So sichern Sie AD FS 2.0 mit einem Proxy

1. Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol "IIS-Authentifizierung".
2. Klicken Sie auf die Registerkarte "Formularbasiert".
3. Klicken Sie auf die Schaltfläche "Hinzufügen".
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Damit Benutzername, Kennwort und Domänenvariablen automatisch erkannt werden, geben Sie die Anmelde-URL (z. B. https://sso.contoso.com/adfs/ls) im Dialogfeld „Formularbasierte Website automatisch konfigurieren“ ein, und klicken Sie auf „OK“.
5. Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erfordern", wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert. Weitere Informationen zu dieser Funktion finden Sie in der Hilfedatei.
6. Wenn die Seitenvariablen nicht automatisch erkannt werden können, klicken Sie im Dialogfeld "Formularbasierte Website automatisch konfigurieren" auf die Schaltfläche "Manuell angeben".
7. Geben Sie im Dialogfeld „Formularbasierte Website hinzufügen“ im Feld „Sende-URL“ die URL der AD FS-Anmeldeseite (z. B. https://sso.contoso.com/adfs/ls) und optional einen Anwendungsnamen ein. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt. Weitere Informationen zur Sende-URL finden Sie in der Hilfedatei.
8. Legen Sie das Anforderungsformat auf "POST oder GET" fest.
9. Geben Sie die Username-Variable (ctl00$ContentPlaceHolder1$UsernameTextBox) und die Password-Variable (ctl00$ContentPlaceHolder1$PasswordTextBox) ein. Wenn auf Ihrer formularbasierten Anmeldeseite ein Textfeld für die Domäne angezeigt wird, geben Sie auch die Domain-Variable ein. Sie müssen in einem Webbrowser zur Anmeldeseite navigieren, mit der rechten Maustaste auf die Seite klicken und "Quelltext anzeigen" auswählen, um die Namen der Eingabefelder auf der Anmeldeseite zu ermitteln.
10. Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erfordern", wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Klicken Sie auf die Schaltfläche "Erweitert", um die erweiterten Einstellungen zu überprüfen, einschließlich der Optionen zum Auswählen einer benutzerdefinierten Verweigerungs-Auslagerungsdatei, zum Zwischenspeichern erfolgreicher Authentifizierungen bei der Website für einen bestimmten Zeitraum mithilfe von Cookies und zum Auswählen des Authentifizierungsverfahrens für die primären Anmeldeinformationen.
12. Da der AD FS-Proxyserver wahrscheinlich kein Mitglied der Domäne ist, verwenden Sie i. d. R. LDAP, um eine Verbindung mit dem Domänencontroller für den Import und die Vorauthentifizierung herzustellen. Klicken Sie im Dialogfeld "Erweiterte formularbasierte Website" auf die Registerkarte "Primäre Authentifizierung", und wählen Sie als Vorauthentifizierungstyp "LDAP-Bindung" aus.
13. Klicken Sie abschließend auf die Schaltfläche "OK", um zum Dialogfeld "Formularbasierte Website hinzufügen" zurückzukehren. Weitere Informationen zu den erweiterten Einstellungen finden Sie in der Hilfedatei.
14. Klicken Sie auf die Schaltfläche "OK", um das Dialogfeld zu schließen.
15. Sobald die URL und die Seitenvariablen erkannt bzw. eingegeben wurden, werden die Websitedaten im Bereich "Formularbasiert" angezeigt.
16. Klicken Sie auf die Registerkarte "Systemeigenes Modul", und wählen Sie den Server, die Website, unter der der AD FS-Proxy ausgeführt wird (z. B. "Standardwebsite"), oder die AD FS-Proxyanwendung (z. B. "ls" unter "adfs") aus, um das IIS-Plug-In auf der gewünschten Ebene zu aktivieren.
17. Aktivieren Sie oben im Bildschirm das Kontrollkästchen "IIS-Authentifizierung aktivieren".
18. Die IIS-Authentifizierung ist jetzt aktiviert. Um die Vorauthentifizierung für Active Directory (AD) über LDAP durchzuführen, müssen Sie jedoch die LDAP-Verbindung mit dem Domänencontroller konfigurieren. Klicken Sie dazu auf das Symbol "Verzeichnisintegration".
19. Aktivieren Sie auf der Registerkarte "Einstellungen" das Optionsfeld "Bestimmte LDAP-Konfiguration verwenden".
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
20. Klicken Sie auf die Schaltfläche "Bearbeiten".
21. Füllen Sie im Dialogfeld "LDAP-Konfiguration bearbeiten" die Felder mit den Informationen aus, die für die Verbindung mit dem Active Directory-Domänencontroller erforderlich sind. In der folgenden Tabelle finden Sie die Beschreibungen der Felder. Hinweis: Diese Informationen sind auch in der Hilfedatei von Azure Multi-Factor Authentication-Server enthalten.
22. Testen Sie die LDAP-Verbindung durch Klicken auf die Schaltfläche "Testen".
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
23. Wenn der Test der LDAP-Verbindungs erfolgreich war, klicken Sie auf die Schaltfläche "OK".
24. Als Nächstes klicken Sie auf das Symbol "Unternehmenseinstellungen" und wählen die Registerkarte "Benutzernamenauflösung" aus.
25. Wählen Sie das Optionsfeld "Eindeutiges LDAP-Bezeichnerattribut für den Abgleich von Benutzernamen verwenden" aus.
26. Wenn Benutzer ihren Benutzernamen im Anmeldeformular für den AD FS-Proxy im Format "Domäne\\Benutzername" eingeben, muss der Server beim Erstellen der LDAP-Abfrage die Domäne vom Benutzernamen trennen können. Dazu kann eine Registrierungseinstellung verwendet werden.
27. Öffnen Sie den Registrierungs-Editor auf einem 64-Bit-Server, und navigieren Sie zu "HKEY\_LOCAL\_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor". Auf einem 32-Bit-Server fehlt "Wow6432Node" im Pfad. Erstellen Sie einen neuen DWORD-Registrierungsschlüssel mit dem Namen "UsernameCxz\_stripPrefixDomain", und legen Sie den Wert auf "1" fest. Der AD FS-Proxy wird jetzt durch Azure Multi-Factor Authentication gesichert. Stellen Sie sicher, dass Benutzer aus Active Directory in den Server importiert wurden. Wenn Sie eine Positivliste von internen IP-Adressen verwenden möchten, damit bei der Anmeldung auf der Website von diesen Orten aus keine zweistufige Authentifizierung erforderlich ist, finden Sie weitere Informationen im Abschnitt "Vertrauenswürdige IPs" unten.

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 – direkt, ohne Proxy

Zum Sichern von AD FS ohne Proxy installieren Sie Azure Multi-Factor Authentication-Server auf dem AD FS-Proxyserver und konfigurieren den Server wie folgt.

### So sichern Sie AD FS 2.0 ohne Proxy
1. Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol "IIS-Authentifizierung".
2. Klicken Sie auf die Registerkarte "HTTP".
3. Klicken Sie auf die Schaltfläche "Hinzufügen".
4. Geben Sie im Dialogfeld „Basis-URL hinzufügen“ im Feld „Basis-URL“ die URL für die AD FS-Website, auf der die HTTP-Authentifizierung erfolgt (z. B. https://sso.domain.com/adfs/ls/auth/integrated), und optional einen Anwendungsnamen ein. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt.
5. Passen Sie bei Bedarf die Leerlaufzeitüberschreitung und die maximale Sitzungsdauer an.
6. Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erfordern", wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert. Weitere Informationen zu dieser Funktion finden Sie in der Hilfedatei.
7. Aktivieren Sie bei Bedarf das Kontrollkästchen für das Cookie zur Zwischenspeicherung.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Klicken Sie auf die Schaltfläche "OK".
9. Klicken Sie auf die Registerkarte "Systemeigenes Modul", und wählen Sie den Server, die Website, unter der der AD FS ausgeführt wird (z. B. "Standardwebsite"), oder die AD FS-Anwendung (z. B. "ls" unter "adfs") aus, um das IIS-Plug-In auf der gewünschten Ebene zu aktivieren.
10. Aktivieren Sie oben im Bildschirm das Kontrollkästchen "IIS-Authentifizierung aktivieren". AD FS wird jetzt durch Azure Multi-Factor Authentication gesichert. Stellen Sie sicher, dass Benutzer aus Active Directory in den Server importiert wurden. Wenn Sie eine Positivliste von internen IP-Adressen verwenden möchten, damit bei der Anmeldung auf der Website von diesen Orten aus keine zweistufige Authentifizierung erforderlich ist, finden Sie weitere Informationen im Abschnitt "Vertrauenswürdige IPs" unten.


## Vertrauenswürdige IP-Adressen
Über vertrauenswürdige IP-Adressen können Benutzer Azure Multi-Factor Authentication bei Websiteanforderungen umgehen, die von bestimmten IP-Adressen oder Subnetzen stammen. Beispielsweise können Benutzer, die sich vom Büro aus anmelden, von Azure Multi-Factor Authentication ausschließen. Dazu geben Sie das Bürosubnetz als Eintrag in "Vertrauenswürdige IPs" an.

### So konfigurieren Sie vertrauenswürdige IP-Adressen


1. Klicken Sie im Abschnitt "IIS-Authentifizierung" auf die Registerkarte "Vertrauenswürdige IPs".
1. Klicken Sie auf die Schaltfläche "Hinzufügen".
1. Im daraufhin angezeigten Dialogfeld "Vertrauenswürdige IP hinzufügen" wählen Sie das Optionsfeld "Eine IP", "IP-Bereich" oder "Subnetz" aus.
1. Geben Sie die IP-Adresse, den IP-Adressbereich oder das Subnetz ein, das der Positivliste hinzugefügt werden soll. Wenn Sie ein Subnetz eingeben, wählen Sie die entsprechende Netzmaske aus, und klicken Sie auf die Schaltfläche "OK". Die vertrauenswürdige IP wurde hinzugefügt.


<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=August15_HO9-->