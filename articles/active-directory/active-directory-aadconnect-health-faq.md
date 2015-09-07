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
	ms.date="08/14/2015"
	ms.author="billmath"/>


# Häufig gestellte Fragen zu Azure AD Connect Health

Dieses FAQ-Dokument beantwortet Fragen zu Azure AD Connect Health. Dazu gehören Fragen zur Verwendung des Diensts, einschließlich Abrechnungsmodell, Funktionen, Einschränkungen und Unterstützung.

## Allgemeine Fragen



**F: Ich habe mehrere Mandanten in Azure Active Directory. Wie kann ich zu dem Mandanten mit Azure Active Directory Premium wechseln?**

Sie können zwischen den Azure AD-Mandanten wechseln, indem Sie auf der linken Navigationsleiste auf "Startseite" klicken und anschließend in der oberen rechten Ecke den aktuell angemeldeten Benutzernamen sowie das gewünschte Mandantenkonto auswählen. Wenn das Konto des Mandanten hier nicht aufgeführt ist, wählen Sie "Abmelden", und melden Sie sich mit den globalen Administratoranmeldeinformationen für den Azure Active Directory Premium-Mandanten an.


## Fragen zur Installation



**F: Wie wirkt sich die Installation des Azure AD Connect Health-Agents auf die einzelnen Server aus?**

Die Installation des Azure AD Connect Health-Agents auf den AD FS-Servern wirkt sich in Bezug auf CPU, Arbeitsspeichernutzung, Netzwerkbandbreite und Speicher nur minimal aus.

Die folgenden Zahlen stellen Näherungswerte dar.

- CPU-Auslastung: ~ 1 % Zunahme
- Arbeitsspeichernutzung: bis zu 10 % des insgesamt verfügbaren Systemarbeitsspeichers
- Nutzung der Netzwerkbandbreite: ~ 1 MB pro 1000 AD FS-Anforderungen
>[AZURE.NOTE]Falls der Agent nicht mit Azure kommunizieren kann, speichert der Agent die Daten lokal bis zu einem Höchstwert von 10 % des gesamten Systemarbeitsspeichers. Sobald der Agent 10 % des gesamten physischen Speichers erreicht hat und keine Daten in den Dienst mehr hochladen kann, überschreiben die neuen AD FS-Transaktionen etwaige zwischengespeicherte Transaktionen, wobei bei den am seltensten verwendeten Transaktionen begonnen wird.

- Lokaler Pufferspeicher für AD Connect Health-Agent: ca. 20 MB
- Datenspeicher für Überwachungskanal erforderlich


Es wird empfohlen, 1024 MB (1 GB) Festplattenspeicher für den AD FS-Überwachungskanal für AD Connect Health-Agents bereitzustellen, um sämtliche Daten zu verarbeiten.

**F: Muss ich meine Server während der Installation der Azure AD Connect Health-Agents neu starten?**

Nein. Die Installation der Agents erfordert keinen Serverneustart. Während der Installation einiger vorbereitender Schritte muss der Server jedoch möglicherweise neu gestartet werden.

Beispielsweise erfordert die Installation des .NET 4.5-Frameworks unter Windows Server 2008 R2 einen Neustart des Servers.


**F: Nutzt der Azure AD Connect Health-Dienst einen Passthrough-Proxy über HTTP?**

Ja, sowohl der Registrierungsprozess als auch der normale Betrieb funktionieren über einen expliziten Proxy zum Weiterleiten von ausgehenden HTTP-Anforderungen. "Netsh WinHttp set Proxy" funktioniert in diesem Fall nicht, da der Agent System.Net statt der Microsoft Windows HTTP-Dienste verwendet, um Webanforderungen zu senden.

Führen Sie diesen Schritt vor Ausführung von Register-AdHealthAgent aus (letzter Schritt der Installation).


- Schritt 1: Hinzufügen eines Eintrags zur Datei "machine.config"


Suchen Sie nach der Datei "machine.config". Diese Datei befindet sich hier: %windir%\\Microsoft.NET\\Framework64[version]\\config\\machine.config</li>

Fügen Sie der Datei "machine.config" folgenden Eintrag unter dem <configuration></configuration> Element hinzu.
		
	<system.net>  
			<defaultProxy useDefaultCredentials="true">
       		<proxy 
        usesystemdefault="true" 
        proxyaddress="http://YOUR.PROXY.HERE.com"  
        bypassonlocal="true"/>
		</defaultProxy>
	</system.net> 

 

Zusätzliche <defaultProxy> Informationen finden Sie [hier] (https://msdn.microsoft.com/library/kd3cf2ex(v=vs.110))).

Mit diesen Einstellungen konfigurieren Sie .NET-Anwendungen systemweit so, dass bei .NET-Anforderungen über HTTP Ihr explizit definierter Proxy verwendet wird. Es wird nicht empfohlen, die einzelnen "app.config"-Dateien zu ändern, da diese Änderungen während einer automatischen Aktualisierung rückgängig gemacht werden. Wenn Sie die Datei "machine.config" bearbeiten, müssen Sie nur eine Datei ändern, und die Änderungen bleiben auch bei Aktualisierungen erhalten.

- Schritt 2: Konfigurieren des Proxys unter "Internetoptionen"

Öffnen Sie "Internet Explorer -> Einstellungen -> Internetoptionen -> Verbindungen -> LAN-Einstellungen".

Wählen Sie "Proxyserver für LAN verwenden" aus.

Wählen Sie "Erweitert", wenn Sie über unterschiedliche Proxyports für HTTP und HTTPS/Secure verfügen.




**F: Unterstützt der Azure AD Connect Health-Dienst die Standardauthentifizierung bei der Verbindung mit HTTP-Proxys?**

Nein. Ein Mechanismus zum Angeben eines frei wählbaren Benutzernamens und Kennworts für die Standardauthentifizierung wird derzeit nicht unterstützt.





## Fragen zum Betrieb



**F: Muss ich die Überwachung auf meinen AD FS-Anwendungsproxyservern oder meinen Webanwendungsproxyservern aktivieren?**

Nein, die Überwachung muss auf den AD FS-Anwendungsproxyservern oder Webanwendungsproxyservern nicht aktiviert sein. Sie muss nur auf den AD FS-Verbundservern aktiviert sein.



**F: Wie werden Azure AD Connect Health-Warnungen aufgelöst?**

Azure AD Connect Health-Warnungen werden basierend auf einer Erfolgsbedingung aufgelöst. Azure AD Connect Health-Agents erkennen und melden die Erfolgsbedingungen in regelmäßigen Abständen an den Dienst. Bei einigen Warnungen gilt eine zeitbasierte Unterdrückung. Dies bedeutet: Wenn die gleiche Fehlerbedingung nicht innerhalb von 48 Stunden nach Generieren der Warnung erneut festgestellt wird, wird die Warnung automatisch aufgelöst.




**F: Welche Firewallports muss ich öffnen, damit der Azure AD Connect Health-Agent funktioniert?**

Sie müssen die TCP/UDP-Ports 80 und 443 öffnen, damit der Azure AD Connect Health-Agent mit den Azure AD Connect Health-Dienstendpunkten kommunizieren kann.

## Verwandte Links

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health Agent-Installation für AD FS](active-directory-aadconnect-health-agent-install-adfs.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health-Vorgänge](active-directory-aadconnect-health-operations.md)

<!---HONumber=August15_HO9-->