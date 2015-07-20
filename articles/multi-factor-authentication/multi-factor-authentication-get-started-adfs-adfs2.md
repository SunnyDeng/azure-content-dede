<properties 
	pageTitle="Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit AD FS 2.0" 
	description="Auf dieser Seite zur Azure Multi-Factor Authentication werden die ersten Schritte mit Azure MFA und AD FS 2.0 beschrieben." 
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
# Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit AD FS 2.0

Wenn Ihre Organisation einen Verbund mit Azure Active Directory bildet und Sie lokale und Cloud-Ressourcen sichern möchten, können Sie dazu Azure Multi-Factor Authentication Server verwenden, indem Sie diesen für die Verwendung mit AD FS so konfigurieren, dass die Multi-Factor Authentication bei wichtigen Endpunkten ausgelöst wird.

In dieser Dokumentation wird die Verwendung von Azure Multi-Factor Authentication-Server mit AD FS 2.0 behandelt. Informationen zur Verwendung von Multi-Factor Authentication mit Windows Server 2012 R2 AD FS finden Sie unter [Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## AD FS 2.0 – Proxy
Zum Sichern von AD FS 2.0 mit einem Proxy installieren Sie Azure Multi-Factor Authentication-Server auf dem AD FS-Proxyserver und konfigurieren den Server wie folgt.

### So sichern Sie AD FS 2.0 mit einem Proxy
<ol>
<li>Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol "IIS-Authentifizierung".</li>
<li>Klicken Sie auf die Registerkarte "Formularbasiert".</li>
<li>Klicken Sie auf die Schaltfläche "Hinzufügen".</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>

<li>Damit Benutzername, Kennwort und Domänenvariablen automatisch erkannt werden, geben Sie die Anmelde-URL (z.&#160;B. "https://sso.contoso.com/adfs/ls") im Dialogfeld "Formularbasierte Website automatisch konfigurieren" ein, und klicken Sie auf "OK".</li>
<li>Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erfordern", wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert. Weitere Informationen zu dieser Funktion finden Sie in der Hilfedatei.</li>
<li>Wenn die Seitenvariablen nicht automatisch erkannt werden können, klicken Sie im Dialogfeld "Formularbasierte Website automatisch konfigurieren" auf die Schaltfläche "Manuell angeben".</li>
<li>Geben Sie im Dialogfeld "Formularbasierte Website hinzufügen" im Feld "Sende-URL" die URL der AD FS-Anmeldeseite (z.&#160;B. "https://sso.contoso.com/adfs/ls"), und optional einen Anwendungsnamen ein. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt. Weitere Informationen zur Sende-URL finden Sie in der Hilfedatei.</li>
<li>Legen Sie das Anforderungsformat auf "POST oder GET" fest.</li>
<li>Geben Sie die Username-Variable (ctl00$ContentPlaceHolder1$UsernameTextBox) und die Password-Variable (ctl00$ContentPlaceHolder1$PasswordTextBox) ein. Wenn auf Ihrer formularbasierten Anmeldeseite ein Textfeld für die Domäne angezeigt wird, geben Sie auch die Domain-Variable ein. Sie müssen in einem Webbrowser zur Anmeldeseite navigieren, mit der rechten Maustaste auf die Seite klicken und "Quelltext anzeigen" auswählen, um die Namen der Eingabefelder auf der Anmeldeseite zu ermitteln.</li>
<li>Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erfordern", wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>

<li>Klicken Sie auf die Schaltfläche "Erweitert", um die erweiterten Einstellungen zu überprüfen, einschließlich der Optionen zum Auswählen einer benutzerdefinierten Verweigerungs-Auslagerungsdatei, zum Zwischenspeichern erfolgreicher Authentifizierungen bei der Website für einen bestimmten Zeitraum mithilfe von Cookies und zum Auswählen des Authentifizierungsverfahrens für die primären Anmeldeinformationen.</li>
<li>Da der AD FS-Proxyserver wahrscheinlich kein Mitglied der Domäne ist, verwenden Sie i.&#160;d.&#160;R. LDAP, um eine Verbindung mit dem Domänencontroller für den Import und die Vorauthentifizierung herzustellen. Klicken Sie im Dialogfeld "Erweiterte formularbasierte Website" auf die Registerkarte "Primäre Authentifizierung", und wählen Sie als Vorauthentifizierungstyp "LDAP-Bindung" aus.</li>
<li>Klicken Sie abschließend auf die Schaltfläche "OK", um zum Dialogfeld "Formularbasierte Website hinzufügen" zurückzukehren. Weitere Informationen zu den erweiterten Einstellungen finden Sie in der Hilfedatei.</li>
<li>Klicken Sie auf die Schaltfläche "OK", um das Dialogfeld zu schließen.</li>
<li>Sobald die URL und die Seitenvariablen erkannt bzw. eingegeben wurden, werden die Websitedaten im Bereich "Formularbasiert" angezeigt.</li>
<li>Klicken Sie auf die Registerkarte "Systemeigenes Modul", und wählen Sie den Server, die Website, unter der der AD FS-Proxy ausgeführt wird (z.&#160;B. "Standardwebsite"), oder die AD FS-Proxyanwendung (z.&#160;B. "ls" unter "adfs") aus, um das IIS-Plug-In auf der gewünschten Ebene zu aktivieren.</li>
<li>Aktivieren Sie oben im Bildschirm das Kontrollkästchen "IIS-Authentifizierung aktivieren".</li>
<li>Die IIS-Authentifizierung ist jetzt aktiviert. Um die Vorauthentifizierung für Active Directory (AD) über LDAP durchzuführen, müssen Sie jedoch die LDAP-Verbindung mit dem Domänencontroller konfigurieren. Klicken Sie dazu auf das Symbol "Verzeichnisintegration".</li>
<li>Aktivieren Sie auf der Registerkarte "Einstellungen" das Optionsfeld "Bestimmte LDAP-Konfiguration verwenden".</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>

<li>Klicken Sie auf die Schaltfläche "Bearbeiten".</li>
<li>Füllen Sie im Dialogfeld "LDAP-Konfiguration bearbeiten" die Felder mit den Informationen aus, die für die Verbindung mit dem Active Directory-Domänencontroller erforderlich sind. In der folgenden Tabelle finden Sie die Beschreibungen der Felder. Hinweis: Diese Informationen sind auch in der Hilfedatei von Azure Multi-Factor Authentication-Server enthalten.</li>

Feld| Beschreibung
:------------- | :-------------
Server|Geben Sie den Hostnamen oder die IP-Adresse des Servers ein, auf dem das LDAP-Verzeichnis ausgeführt wird. Sie können auch einen Sicherungsserver durch ein Semikolon getrennt angeben.<br> **Hinweis:** Wenn der Bindungstyp SSL ist, ist i. d. R. ein vollqualifizierter Hostname erforderlich, der mit dem auf dem LDAP-Verzeichnisserver installierten SSL-Zertifikat übereinstimmen muss. 
Basis-DN|Geben Sie den Distinguished Name des Basisverzeichnisobjekts an, von dem alle Verzeichnisabfragen ausgehen. Beispiel: dc=contoso,dc=com.
Bindungstyp|Wählen Sie den entsprechenden Bindungstyp für Bindungen zum Durchsuchen des LDAP-Verzeichnisses aus. Dieser wird für Importe, die Synchronisierung und die Auflösung des Benutzernamens verwendet.<ul><li>** Anonym** – Es wird eine anonyme Bindung ausgeführt. Bindungs-DN und Bindungskennwort werden nicht verwendet. Dies funktioniert nur, wenn anonyme Bindungen für das LDAP-Verzeichnis zugelassen werden und Berechtigungen Abfragen der entsprechenden Datensätze und Attribute erlauben.</li><li>**Einfache** – Bindungs-DN und Bindungskennwort werden im Nur-Text-Format zum Binden an das LDAP-Verzeichnis übergeben. Dies sollte nur für Testzwecke verwendet werden, um zu überprüfen, ob der Server erreicht werden kann und das Bindungskonto über die erforderlichen Zugriffsrechte verfügt. Es wird empfohlen, stattdessen SSL zu verwenden, nachdem das entsprechende Zertifikat installiert wurde.</li><li>**SSL** – Bindungs-DN und das Bindungskennwort werden mithilfe von SSL zum Binden an das LDAP-Verzeichnis verschlüsselt. Hierbei ist es erforderlich, dass ein Zertifikat auf dem LDAP-Verzeichnisserver installiert wird, der dem Azure Multi-Factor Authentication-Server vertraut.</li><li>**Windows** – Bindungsbenutzername und Bindungskennwort werden verwendet, um eine sichere Verbindung mit einem Active Directory-Domänencontroller oder einem ADAM-Verzeichnis herzustellen. Wenn "Bindungsbenutzername" leer ist, wird das Konto des angemeldeten Benutzers für die Bindung verwendet.</li></ul> 
Bindungsbenutzername|Geben Sie den Distinguished Name des Benutzerdatensatzes für das Konto ein, das für die Bindung an das LDAP-Verzeichnis verwendet wird. Der Bindungs-DN wird nur verwendet, wenn der Bindungstyp "Einfach" oder "SSL" ist.    
Bindungskennwort|Geben Sie das Bindungskennwort für den Bindungs-DN oder den Benutzernamen ein, der für die Bindung an das LDAP-Verzeichnis verwendet wird. Um das Kennwort für den AdSync-Dienst des Multi-Factor Authentication-Servers zu konfigurieren, muss die Synchronisierung aktiviert sein, und der Dienst muss auf dem lokalen Computer ausgeführt werden. Das Kennwort wird in den unter Windows gespeicherten Benutzernamen und Kennwörtern unter dem Konto gespeichert, unter dem der AdSync-Dienst des Azure Multi-Factor Authentication-Servers ausgeführt wird. Das Kennwort wird ebenfalls unter dem Konto gespeichert, unter dem die Benutzeroberfläche ausgeführt wird, sowie unter dem Konto, unter dem der Azure Multi-Factor Authentication-Server-Dienst ausgeführt wird. Hinweis: Da das Kennwort wird nur in den unter Windows gespeicherten Benutzernamen und Kennwörtern des lokalen Servers gespeichert wird, müssen Sie diesen Schritt auf jedem Multi-Factor Authentication-Server durchführen, der Zugriff auf das Kennwort benötigt. 
Abfragegrößenlimit|Geben Sie die maximale Anzahl der Benutzer an, die von einer Suche im Verzeichnis zurückgegeben wird. Dieser Grenzwert sollte mit der Konfiguration im LDAP-Verzeichnis übereinstimmen. Bei umfangreichen Suchläufen, bei denen die Auslagerung nicht unterstützt wird, versuchen Import- und Synchronisierungsvorgänge, Benutzer stapelweise abzurufen. Wenn Sie hier als Grenzwert einen höheren Wert als den im LDAP-Verzeichnis konfigurierten Grenzwert angeben, fehlen möglicherweise einige Benutzer. 



<li>Testen Sie die LDAP-Verbindung durch Klicken auf die Schaltfläche "Testen".</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
<li>Wenn der Test der LDAP-Verbindungs erfolgreich war, klicken Sie auf die Schaltfläche "OK".</li>
<li>Als Nächstes klicken Sie auf das Symbol "Unternehmenseinstellungen" und wählen die Registerkarte "Benutzernamenauflösung" aus.</li>
<li>Wählen Sie das Optionsfeld "Eindeutiges LDAP-Bezeichnerattribut für den Abgleich von Benutzernamen verwenden" aus.</li>
<li>Wenn Benutzer ihren Benutzernamen im Anmeldeformular für den AD FS-Proxy im Format "Domäne\Benutzername" eingeben, muss der Server beim Erstellen der LDAP-Abfrage die Domäne vom Benutzernamen trennen können. Dazu kann eine Registrierungseinstellung verwendet werden.</li>
<li>Öffnen Sie den Registrierungs-Editor auf einem 64-Bit-Server, und navigieren Sie zu "HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor". Auf einem 32-Bit-Server fehlt "Wow6432Node" im Pfad. Erstellen Sie einen neuen DWORD-Registrierungsschlüssel mit dem Namen "UsernameCxz_stripPrefixDomain", und legen Sie den Wert auf "1" fest. Der AD FS-Proxy wird jetzt durch Azure Multi-Factor Authentication gesichert. Stellen Sie sicher, dass Benutzer aus Active Directory in den Server importiert wurden. Wenn Sie eine Positivliste von internen IP-Adressen verwenden möchten, damit bei der Anmeldung auf der Website von diesen Orten aus keine zweistufige Authentifizierung erforderlich ist, finden Sie weitere Informationen im Abschnitt "Vertrauenswürdige IPs" unten.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 – direkt, ohne Proxy

Zum Sichern von AD FS ohne Proxy installieren Sie Azure Multi-Factor Authentication-Server auf dem AD FS-Proxyserver und konfigurieren den Server wie folgt.

### So sichern Sie AD FS 2.0 ohne Proxy
<ol>
<li>Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol "IIS-Authentifizierung".</li>
<li>Klicken Sie auf die Registerkarte "HTTP".</li>
<li>Klicken Sie auf die Schaltfläche "Hinzufügen".</li>
<li>Geben Sie im Dialogfeld "Basis-URL hinzufügen" im Feld "Basis-URL" die URL für die AD FS-Website, auf der die HTTP-Authentifizierung erfolgt, (z.&#160;B. "https://sso.domain.com/adfs/ls/auth/integrated") und optional einen Anwendungsnamen ein. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt.</li>
<li>Passen Sie bei Bedarf die Leerlaufzeitüberschreitung und die maximale Sitzungsdauer an.</li>
<li>Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erfordern", wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert. Weitere Informationen zu dieser Funktion finden Sie in der Hilfedatei.</li>
<li>Aktivieren Sie bei Bedarf das Kontrollkästchen für das Cookie zur Zwischenspeicherung.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>

<li>Klicken Sie auf die Schaltfläche "OK".</li>
<li>Klicken Sie auf die Registerkarte "Systemeigenes Modul", und wählen Sie den Server, die Website, unter der der AD FS ausgeführt wird (z.&#160;B. "Standardwebsite"), oder die AD FS-Anwendung (z.&#160;B. "ls" unter "adfs") aus, um das IIS-Plug-In auf der gewünschten Ebene zu aktivieren.</li>
<li>Aktivieren Sie oben im Bildschirm das Kontrollkästchen "IIS-Authentifizierung aktivieren". AD FS wird jetzt durch Azure Multi-Factor Authentication gesichert. Stellen Sie sicher, dass Benutzer aus Active Directory in den Server importiert wurden. Wenn Sie eine Positivliste von internen IP-Adressen verwenden möchten, damit bei der Anmeldung auf der Website von diesen Orten aus keine zweistufige Authentifizierung erforderlich ist, finden Sie weitere Informationen im Abschnitt "Vertrauenswürdige IPs" unten.</li>

## Vertrauenswürdige IP-Adressen
Über vertrauenswürdige IP-Adressen können Benutzer Azure Multi-Factor Authentication bei Websiteanforderungen umgehen, die von bestimmten IP-Adressen oder Subnetzen stammen. Beispielsweise können Benutzer, die sich vom Büro aus anmelden, von Azure Multi-Factor Authentication ausschließen. Dazu geben Sie das Bürosubnetz als Eintrag in "Vertrauenswürdige IPs" an.

### So konfigurieren Sie vertrauenswürdige IP-Adressen

<ol>
<li>Klicken Sie im Abschnitt "IIS-Authentifizierung" auf die Registerkarte "Vertrauenswürdige IPs".</li>
<li>Klicken Sie auf die Schaltfläche "Hinzufügen".</li>
<li>Im daraufhin angezeigten Dialogfeld "Vertrauenswürdige IP hinzufügen" wählen Sie das Optionsfeld "Eine IP", "IP-Bereich" oder "Subnetz" aus.</li>
<li>Geben Sie die IP-Adresse, den IP-Adressbereich oder das Subnetz ein, das der Positivliste hinzugefügt werden soll. Wenn Sie ein Subnetz eingeben, wählen Sie die entsprechende Netzmaske aus, und klicken Sie auf die Schaltfläche "OK". Der Eintrag wird der Positivliste hinzugefügt.</li>


<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=July15_HO2-->