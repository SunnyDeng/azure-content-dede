<properties
	pageTitle="Azure AD Connect Health – FAQ"
	description="Dieses FAQ-Dokument beantwortet Fragen zu Azure AD Connect Health. Dazu gehören Fragen zur Verwendung des Diensts, einschließlich Abrechnungsmodell, Funktionen, Einschränkungen und Unterstützung."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="billmath"/>


# Häufig gestellte Fragen zu Azure AD Connect Health

Dieses FAQ-Dokument beantwortet Fragen zu Azure AD Connect Health. Dazu gehören Fragen zur Verwendung des Diensts, einschließlich Abrechnungsmodell, Funktionen, Einschränkungen und Unterstützung.

## Allgemeine Fragen



**F: Ich verwalte mehrere Azure AD-Verzeichnisse. Wie kann ich zu dem Mandanten mit Azure Active Directory Premium wechseln?**

Sie können zwischen verschiedenen Azure AD-Verzeichnissen wechseln, indem Sie in der rechten oberen Ecke den derzeit angemeldeten Benutzernamen sowie das entsprechende Konto auswählen. Wenn das Konto hier nicht aufgeführt ist, wählen Sie "Abmelden" aus, und melden Sie sich dann mit den globalen Administratoranmeldeinformationen des Verzeichnisses an, für das Azure Active Directory Premium aktiviert ist.

## Fragen zur Installation



**F: Wie wirkt sich die Installation des Azure AD Connect Health-Agents auf die einzelnen Server aus?**

Die Installation des Azure AD Connect Health-Agents auf den AD FS-Servern wirkt sich in Bezug auf CPU, Arbeitsspeichernutzung, Netzwerkbandbreite und Speicher nur minimal aus.

Die folgenden Zahlen stellen Näherungswerte dar.

- CPU-Auslastung: ~ 1 % Zunahme
- Arbeitsspeichernutzung: bis zu 10 % des insgesamt verfügbaren Systemarbeitsspeichers
- Nutzung der Netzwerkbandbreite: ~ 1 MB pro 1000 AD FS-Anforderungen

>[AZURE.NOTE]Falls der Agent nicht mit Azure kommunizieren kann, speichert er die Daten bis zu einem definierten maximalen Grenzwert lokal. Sobald der Agent diesen Grenzwert erreicht, überschreiben die neuen ADFS-Transaktionen alle zwischengespeicherten Transaktionen, falls der Agent die Daten nicht auf den Dienst hochladen konnte. Die am seltensten verwendeten Transaktionen werden dabei zuerst überschrieben.

- Lokaler Pufferspeicher für AD Connect Health-Agent: ca. 20 MB
- Datenspeicher für Überwachungskanal erforderlich


Es wird empfohlen, 1024 MB (1 GB) Festplattenspeicher für den AD FS-Überwachungskanal für AD Connect Health-Agents bereitzustellen, um sämtliche Daten zu verarbeiten.

**F: Muss ich meine Server während der Installation der Azure AD Connect Health-Agents neu starten?**

Nein. Die Installation der Agents erfordert keinen Serverneustart. Während der Installation einiger vorbereitender Schritte muss der Server jedoch möglicherweise neu gestartet werden.

Beispielsweise erfordert die Installation des .NET 4.5-Frameworks unter Windows Server 2008 R2 einen Neustart des Servers.


**F: Nutzt der Azure AD Connect Health-Dienst einen Passthrough-Proxy über HTTP?**

Ja. Für laufende Vorgänge können Sie den Health-Agent zum Weiterleiten ausgehender HTTP-Anforderungen mithilfe eines HTTP-Proxys konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren des Azure AD Connect Health-Agents zur Verwendung eines HTTP-Proxys](active-directory-aadconnect-health-agent-install-adfs.md#configure-azure-ad-connect-health-agent-to-use-http-proxy).

Wenn Sie einen Proxy während der Agent-Registrierung konfigurieren müssen, müssen Sie die Proxyeinstellungen Ihres Internet Explorers ändern. <br> Öffnen Sie Internet Explorer -> Einstellungen -> Internetoptionen -> Verbindungen -> LAN-Einstellungen.<br> Wählen Sie „Proxyserver für das LAN verwenden“ aus.<br> Wählen Sie „Erweitert“ aus, wenn Sie über unterschiedliche Proxyports für HTTP und HTTPS/Secure verfügen.<br>


**F: Unterstützt der Azure AD Connect Health-Dienst die Standardauthentifizierung bei der Verbindung mit HTTP-Proxys?**

Nein. Ein Mechanismus zum Angeben eines frei wählbaren Benutzernamens und Kennworts für die Standardauthentifizierung wird derzeit nicht unterstützt.



## Fragen zum Betrieb



**F: Muss ich die Überwachung auf meinen AD FS-Anwendungsproxyservern oder meinen Webanwendungsproxyservern aktivieren?**

Nein, die Überwachung muss auf den AD FS-Anwendungsproxyservern oder Webanwendungsproxyservern nicht aktiviert sein. Sie muss nur auf den AD FS-Verbundservern aktiviert sein.



**F: Wie werden Azure AD Connect Health-Warnungen aufgelöst?**

Azure AD Connect Health-Warnungen werden basierend auf einer Erfolgsbedingung aufgelöst. Azure AD Connect Health-Agents erkennen und melden die Erfolgsbedingungen in regelmäßigen Abständen an den Dienst. Bei einigen Warnungen gilt eine zeitbasierte Unterdrückung. Dies bedeutet: Wenn die gleiche Fehlerbedingung nicht innerhalb von 48 Stunden nach Generieren der Warnung erneut festgestellt wird, wird die Warnung automatisch aufgelöst.




**F: Welche Firewallports muss ich öffnen, damit der Azure AD Connect Health-Agent funktioniert?**

Sie müssen die TCP/UDP-Ports 80, 443 und 5671 öffnen, damit der Azure AD Connect Health-Agent mit den Azure AD Connect Health-Dienstendpunkten kommunizieren kann.

## Verwandte Links

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installieren des Azure AD Connect Health-Agents](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-Vorgänge](active-directory-aadconnect-health-operations.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Verwenden von Azure AD Connect Health für die Synchronisierung](active-directory-aadconnect-health-sync.md)

<!---HONumber=Nov15_HO2-->