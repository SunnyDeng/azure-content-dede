<properties
	pageTitle="Überlegungen zu Sicherheit und Datenschutz in Cloud App Discovery | Microsoft Azure"
	description="Dieses Thema beschreibt Überlegungen zu Sicherheit und Datenschutz in Zusammenhang mit Cloud App Discovery."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="markusvi"/>

# Überlegungen zu Sicherheit und Datenschutz in Cloud App Discovery

Microsoft ist bestrebt, Ihre Privatsphäre zu schützen und Ihre Daten zu sichern und dabei Software und Dienste bereitzustellen, die Sie bei der Verwaltung der Sicherheit Ihres Unternehmens unterstützen. <br> Wir wissen, dass hohe Investitionen und viel Fachwissen erforderlich sind, um strikte Sicherheitsvorkehrungen durchzusetzen, wenn Sie Ihre Daten Dritten anvertrauen. Microsoft befolgt strenge Compliance- und Sicherheitsrichtlinien – von sicheren Verfahren während des gesamten Lebenszyklus der Softwareentwicklung bis hin zum Betrieb eines Diensts. <br> Das Sichern und Schützen von Daten besitzt bei Microsoft höchste Priorität.

In diesem Thema wird erläutert, wie Daten in Azure Active Directory Cloud App Discovery gesammelt, verarbeitet und geschützt werden.




##Übersicht

Cloud App Discovery ist ein Feature von Azure AD und wird in Microsoft Azure gehostet. <br> Der Cloud App Discovery-Endpunkt-Agent wird verwendet, um Ermittlungsdaten für Anwendungen von IT-verwalteten Computern zu sammeln. <br> Die gesammelten Daten werden sicher über einen verschlüsselten Kanal an den Azure AD Cloud App Discovery-Dienst gesendet. <br> Die Cloud App Discovery-Daten für eine Organisation sind dann im Azure-Portal sichtbar. <br>


<center>![Funktionsweise von Cloud App Discovery](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png) </center> <br>


In den folgenden Abschnitten wird entsprechend dem Informationsfluss beschrieben, wie Daten auf dem Weg von Ihrer Organisation zum Cloud App Discovery-Dienst und schließlich zum Cloud App Discovery-Portal geschützt werden.



## Sammeln von Daten aus Ihrer Organisation

Um mithilfe des Azure Active Directory-Features Cloud App Discovery Einblicke in die von Mitarbeitern Ihrer Organisation verwendeten Anwendungen zu erhalten, müssen Sie zuerst den Azure AD Cloud App Discovery-Endpunkt-Agent auf Computern in Ihrer Organisation bereitstellen.

Administratoren des Azure Active Directory-Mandanten (oder ihre Delegaten) können das Agent-Installationspaket vom Azure-Portal herunterladen. Der Agent kann manuell oder mithilfe von SCCM oder Gruppenrichtlinien auf mehreren Computern in der Organisation installiert werden.

Weitere Anweisungen zu Bereitstellungsoptionen finden Sie unter [Bereitstellungshandbuch für Cloud App Discovery mit Gruppenrichtlinien](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx). <br>

### Vom Agent gesammelte Daten

Die Informationen in der folgenden Liste werden vom Agent beim Herstellen einer Verbindung mit einer Webanwendung gesammelt. Es werden nur Informationen für solche Anwendungen gesammelt, die der Administrator für die Ermittlung konfiguriert hat. <br> Sie können die Liste der Cloud-Apps bearbeiten, die der Agent über das Blatt "Cloud App Discovery" im Microsoft [Azure-Portal](https://portal.azure.com/) unter **Einstellungen** -> **Datensammlung** -> **App-Sammlungsliste** überwacht. Weitere Informationen finden Sie unter [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) (in englischer Sprache) <br> **Informationskategorie**: Benutzerinformationen <br> **Beschreibung**: <br> Der Windows-Benutzername des Prozesses, der eine Anforderung an die Zielwebanwendung gestellt hat (z. B. DOMÄNE\\Benutzername) sowie die Windows-Sicherheits-ID (SID) des Benutzers.


**Informationskategorie**: Prozessinformationen <br> **Beschreibung**: <br> Der Name des Prozesses, der die Anforderung an die Zielwebanwendung gestellt hat (z. B. "iexplore.exe").

**Informationskategorie**: Computerinformationen <br> **Beschreibung**: <br> Der NetBIOS-Name des Computers, auf dem der Agent installiert ist.

**Informationskategorie**: Informationen zum App-Datenverkehr <br> **Beschreibung**: <br>

Die folgenden Verbindungsinformationen:

- Die IP-Adressen und Portnummern der Quelle (lokaler Computer) und des Ziels.

- Die öffentliche IP-Adresse der Organisation, über welche die Anforderung gesendet wird.

- Die Uhrzeit der Anforderung

- Der Umfang des gesendeten und empfangenen Datenverkehrs

- Die IP-Version (4 oder 6)

- Nur für TLS-Verbindungen: der Name des Zielhosts aus der Servernamensanzeige-Erweiterung oder dem Serverzertifikat.

Die folgenden HTTP-Informationen:

- Methode (GET, POST usw.)

- Protokoll (HTTP/1.1 usw.)

- Benutzer-Agent-Zeichenfolge

- Hostname

- Ziel-URI (ohne Abfragezeichenfolge)

- Inhaltstypinformationen

- Verweis-URL-Informationen (ohne Abfragezeichenfolge)



> [AZURE.NOTE] Die oben stehenden HTTP-Informationen werden für alle nicht verschlüsselten Verbindungen erfasst. Bei TLS-Verbindungen werden diese Informationen nur aufgezeichnet, wenn die Einstellung "Verschärfte Inspektion" im Portal aktiviert ist. Diese Einstellung ist standardmäßig aktiviert. Weitere Informationen finden Sie unten und unter [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) (in englischer Sprache).


Zusätzlich zu den Daten, die der Agent über die Netzwerkaktivität sammelt, sammelt er auch anonyme Informationen über die Software- und Hardwarekonfiguration, Fehlerberichte und Informationen, wie der Agent verwendet wird.

<br><br>
### Funktionsweise des Agents

Die Agent-Installation umfasst zwei Komponenten:

- Eine Komponente für den Benutzermodus

- Eine Treiberkomponente für den Kernelmodus (Windows-Filterplattformtreiber)



Bei der Erstinstallation des Agents wird ein computerspezifisches vertrauenswürdiges Zertifikat auf dem Computer gespeichert, mit dem dann eine sichere Verbindung mit dem Cloud App Discovery-Dienst hergestellt wird. <br> Der Agent ruft in regelmäßigen Abständen über diese sichere Verbindung die Richtlinienkonfiguration vom Cloud App Discovery-Dienst ab. <br> Die Richtlinie enthält unter anderem Informationen über die zu überwachenden Cloudanwendungen und dazu, ob automatische Updates aktiviert werden sollen.

Während Webdatenverkehr auf dem Computer von Chrome oder Internet Explorer gesendet und empfangen wird, analysiert der Cloud App Discovery-Agent den Datenverkehr und extrahiert die relevanten Metadaten (siehe Abschnitt **Vom Agent gesammelte Daten** oben). <br> Einmal pro Minute lädt der Agent die gesammelten Metadaten über einen verschlüsselten Kanal zum Cloud App Discovery-Dienst hoch.

Die Treiberkomponente fängt den verschlüsselten Datenverkehr ab und fügt sich selbst in den verschlüsselten Stream ein. Weitere Informationen finden Sie im Abschnitt **Abfangen von Daten von verschlüsselten Verbindungen (verschärfte Inspektion)** weiter unten.


### Wahrung der Privatsphäre der Benutzer

Wir möchten Administratoren die nötigen Tools bieten, um entsprechend den Anforderungen ihrer Organisation das richtige Verhältnis zwischen detaillierten Einblicken in die Nutzung von Anwendungen einerseits und Benutzerdatenschutz andererseits festzulegen. Zu diesem Zweck haben wir die folgenden Optionen auf der Einstellungsseite im Portal bereitgestellt:

- **Datensammlung**: Administratoren können angeben, für welche Anwendungen oder Anwendungskategorien Ermittlungsdaten abgerufen werden sollen.

- **Verschärfte Inspektion**: Administratoren können angeben, ob der Agent HTTP-Datenverkehr für SSL/TLS-Verbindungen erfasst (die sog. **'Verschärfte Inspektion'**). Mehr dazu im nächsten Abschnitt.

- **Zustimmungsoptionen**: Administratoren können über das Cloud App Discovery-Portal wählen, ob Benutzer über die Datensammlung durch den Agent informiert werden und ob Benutzer vor dem Sammeln der Benutzerdaten ihre Zustimmung geben müssen.

Der Cloud App Discovery-Endpunkt-Agent sammelt nur die Informationen, die im Abschnitt **Vom Agent gesammelte Daten** oben beschrieben sind.


### Abfangen von Daten aus verschlüsselten Verbindungen (verschärfte Inspektion)
Wie bereits erwähnt können Administratoren den Agent zum Überwachen von Daten aus verschlüsselten Verbindungen ('verschärfte Inspektion') konfigurieren. TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) ist eines der am häufigsten verwendeten Protokolle, das heute im Internet verwendet wird. Durch die Verschlüsselung der Kommunikation mit TLS kann ein Client einen sicheren und privaten Kommunikationskanal mit einem Webserver herstellen. TLS bietet grundlegenden Schutz bei der Übergabe von Anmeldeinformationen zur Authentifizierung und vor Offenlegung von vertraulichen Informationen.

Während der durchgängig sicher verschlüsselte Kanal von TLS eine grundlegende Sicherheit und Datenschutz ermöglicht, wird das Protokoll häufig zu schädlichen oder böswilligen Zwecken missbraucht. Dies geht so weit, dass TLS oft als "universelles Protokoll zur Firewallumgehung" bezeichnet wird. Die Ursache des Problems ist, dass die meisten Firewalls die TLS-Kommunikation nicht überprüfen können, da die Daten auf Anwendungsebene mit SSL verschlüsselt werden. Mit diesem Wissen nutzen Angreifer häufig TLS zur Übermittlung von bösartigen Inhalten an einen Benutzer. Den Angreifern ist dabei klar, dass selbst die intelligentesten Firewalls auf Anwendungsebene TLS nicht erkennen und einfach die TLS-Kommunikation zwischen Hosts weitergeleitet werden muss. Häufig nutzen Endbenutzer TLS zum Umgehen der Zugriffskontrolle, die von den Firewalls und Proxyservern ihres Unternehmens erzwungen werden, um die Verbindung zu öffentlichen Proxys herzustellen und nicht-TLS-Protokolle durch die Firewall zu tunneln, die andernfalls von der Richtlinie blockiert werden.

Mithilfe der verschärften Inspektion kann der Cloud App Discovery-Agent als vertrauenswürdiger Man-in-the-Middle fungieren. Wenn eine Clientanforderung für den Zugriff auf eine durch HTTPS geschützte Ressource erfolgt, fängt der Endpunkt-Agent-Treiber die Verbindung ab und stellt eine neue Verbindung mit dem Zielserver her, über die das SSL-Zertifikat für den Client abgerufen wird. Der Agent überprüft dann, ob das Zertifikat vertrauenswürdig ist (indem er sicherstellt, dass es nicht gesperrt wurde, und andere Zertifikatprüfungen ausführt). Nach einer erfolgreichen Prüfung kopiert der Endpunkt-Agent die Informationen aus dem Serverzertifikat und erstellt mit diesen Informationen ein eigenes Serverzertifikat – bekannt als Abfangzertifikat. Das Abfangzertifikat wird vom Endpunkt-Agent umgehend mit einem Stammzertifikat signiert, das im vertrauenswürdigen Windows-Zertifikatspeicher installiert ist. Dieses selbstsignierte Stammzertifikat ist als nicht exportierbar gekennzeichnet, und der Zugriff wird per ACL auf Administratoren beschränkt. Es bleibt immer auf dem Computer, auf dem es erstellt wurde, und wird nie freigegeben. Wenn die Clientanwendung des Endbenutzers das Abfangzertifikat empfängt, stuft sie es als vertrauenswürdig ein, da die Zertifikatkette bis hin zum Stammzertifikat erfolgreich validiert werden kann. Dieser Prozess ist aus Sicht des Endbenutzers größtenteils transparent, weist jedoch einige Vorsichtsmaßnahmen auf, wie im Folgenden beschrieben.

Durch Aktivieren der verschärften Inspektion kann der Cloud App Discovery-Endpunkt-Agent von TLS verschlüsselte Kommunikationen entschlüsseln und überprüfen, sodass der Dienst Rauschen reduzieren und Einblicke in die Verwendung von verschlüsselten Cloud-Apps geben kann.

#### Ein Wort der Warnung
Bevor Sie die verschärfte Inspektion aktivieren, wird dringend empfohlen, dass Sie den Rechts- und Personalabteilungen Ihres Unternehmens Ihre Absichten mitteilen, um deren Zustimmung zu erhalten. Das Prüfen der privaten, verschlüsselten Kommunikation des Endbenutzers ist offensichtlich eine vertrauliche Angelegenheit. Bevor die verschärfte Inspektion in der Produktionsumgebung umgesetzt wird, stellen Sie sicher, dass die Sicherheit im Unternehmen und Richtlinien der akzeptierten Verwendungen aktualisiert wurden, um anzugeben, dass die verschlüsselte Kommunikation untersucht wird. Benutzerbenachrichtigungen und Ausnahmen für Websites, die als vertraulich eingestuft werden (z. B. Bank- und medizinische Sites), sind möglicherweise erforderlich, wenn Sie Cloud App Discovery zur Überwachung konfigurieren. Administratoren können, wie oben beschrieben, über das Cloud App Discovery-Portal wählen, ob Benutzer über die Datensammlung durch den Agent informiert werden und ob Benutzer vor dem Sammeln der Benutzerdaten ihre Zustimmung geben müssen.

### Bekannte Probleme und Nachteile
Es gibt einige Fälle, in denen das Abfangen durch TLS Auswirkungen auf die Endbenutzererfahrung haben kann:

- Zertifikate für die erweiterte Überprüfung rendern die Adressleiste des Webbrowsers in grün, als visueller Hinweis, dass Sie eine vertrauenswürdige Website besuchen. TLS-Prüfung kann keine erweiterte Überprüfung im selbst an den Client ausgegebenen Zertifikat duplizieren, sodass Websites, die erweiterte Überprüfung nutzen, normal funktionieren, die Adressleiste jedoch nicht grün angezeigt wird.  

- Anheften öffentlicher Schlüssel (auch bezeichnet als Zertifikatfixierung) dient zum Schutz von Benutzern vor Man-in-the-Middle-Angriffen und nicht autorisierten Zertifizierungsstellen. Wenn das Stammzertifikat einer angehefteten Seite nicht mit einer der bekannten, vertrauenswürdigen Zertifizierungsstellen übereinstimmt, lehnt der Browser die Verbindung mit einer Fehlermeldung ab. Da das Abfangen durch TLS tatsächlich ein Man-in-the-Middle ist, schlagen solche Verbindungen fehl.

- Wenn Benutzer auf das Schlosssymbol in der Adressleiste des Browsers klicken, um die Websiteinformationen zu überprüfen, wird keine Kette angezeigt, die bei der Zertifizierungsstelle endet , die zum Signieren des Websitezertifikats verwendet wurde. Stattdessen wird eine Zertifikatkette angezeigt, die im vertrauenswürdigen Windows-Zertifikatspeicher endet.

Um das Auftreten dieser Probleme zu reduzieren, verfolgen wir Clouddienste und Clientanwendungen, von denen bekannt ist, dass sie erweiterte Überprüfung oder Anheften von öffentlichen Schlüssel nutzen, und weisen den Endpunkt-Agent an, das Abfangen von betroffenen Verbindungen zu vermeiden. Selbst in diesen Fällen erhalten Sie weiterhin Berichte über die Verwendung dieser Cloud-Apps und die Menge der übertragenen Daten. Da diese jedoch keiner verschärften Inspektion unterzogen werden, sind auch keine ausführlichen Informationen über die Verwendung der Apps verfügbar.


## Senden von Daten an Cloud App Discovery

Die vom Agent erfassten Metadaten werden bis zu einer Minute lang oder bis die zwischengespeicherten Daten 5 MB erreicht haben, auf dem Computer zwischengespeichert. Anschließend werden sie komprimiert und über eine sichere Verbindung an den Cloud App Discovery-Dienst gesendet.

Wenn die Kommunikation zwischen Agent und Cloud App Discovery-Dienst aus irgendeinem Grund nicht möglich ist, werden die gesammelten Metadaten in einem lokalen Dateicache gespeichert. Auf diesen Cache können nur berechtigte Benutzer auf dem Computer (z. B. die Gruppe "Administratoren") zugreifen. <br> Der Agent sendet die zwischengespeicherten Metadaten automatisch erneut, bis sie vom Cloud App Discovery-Dienst erfolgreich empfangen wurden.



## Empfangen der Daten auf der Dienstseite

Die Agents authentifizieren sich mithilfe des oben erwähnten computerspezifischen Clientauthentifizierungszertifikats beim Cloud App Discovery-Dienst und leiten Daten über einen verschlüsselten Kanal weiter. <br> Die Analysepipeline des Cloud App Discovery-Diensts verarbeitet Metadaten für jeden Kunden getrennt. Dazu werden die Daten über alle Phasen der Analysepipeline hinweg logisch partitioniert. Aus den analysierten Metadaten werden im Portal die verschiedenen Berichte erstellt.

Sowohl die unverarbeiteten als auch die analysierten Metadaten werden bis zu 180 Tage lang gespeichert. Darüber hinaus können Kunden die analysierten Metadaten in einem Azure-Blobspeicherkonto ihrer Wahl erfassen. Dies ermöglicht Offlineanalysen und eine längere Aufbewahrung der Metadaten.

## Zugreifen auf die Daten über das Azure-Portal

Um die Sicherheit der erfassten Metadaten zu gewährleisten, haben standardmäßig nur globale Administratoren des Mandanten Zugriff auf das Cloud App Discovery-Feature im Azure-Portal. <br> Administratoren können diesen Zugriff jedoch an andere Benutzer oder Gruppen delegieren.



> [AZURE.NOTE] Nähere Informationen finden Sie unter [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) (in englischer Sprache).

<br> Jeder Benutzer, der auf die Daten im Portal zugreift, muss mit einer Azure AD Premium-Lizenz lizenziert sein.



**Weitere Ressourcen**


* [Wie ermittle ich nicht genehmigte Cloud-Apps, die in meiner Organisation verwendet werden?](active-directory-cloudappdiscovery-whatis.md)

<!---HONumber=AcomDC_0128_2016-->