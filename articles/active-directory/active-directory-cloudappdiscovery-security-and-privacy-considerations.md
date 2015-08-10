<properties 
	pageTitle="Überlegungen zu Sicherheit und Datenschutz in Cloud App Discovery" 
	description="Dieses Thema beschreibt Überlegungen zu Sicherheit und Datenschutz in Zusammenhang mit Cloud App Discovery." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Überlegungen zu Sicherheit und Datenschutz in Cloud App Discovery

Microsoft ist bestrebt, Ihre Privatsphäre zu schützen und Ihre Daten zu sichern und dabei Software und Dienste bereitzustellen, die Sie bei der Verwaltung der Sicherheit Ihres Unternehmens unterstützen. <br> Wir wissen, dass hohe Investitionen und viel Fachwissen erforderlich sind, um strikte Sicherheitsvorkehrungen durchzusetzen, wenn Sie Ihre Daten Dritten anvertrauen. Microsoft befolgt strenge Compliance- und Sicherheitsrichtlinien – von sicheren Verfahren während des gesamten Lebenszyklus der Softwareentwicklung bis hin zum Betrieb eines Diensts. <br> Das Sichern und Schützen von Daten besitzt bei Microsoft höchste Priorität.

In diesem Thema wird erläutert, wie Daten in Azure Active Directory Cloud App Discovery gesammelt, verarbeitet und geschützt werden.




##Übersicht

Cloud App Discovery ist ein Feature von Azure AD und wird in Microsoft Azure gehostet. <br> Cloud App Discovery Endpoint Agents werden verwendet, um Ermittlungsdaten für Anwendungen von IT-verwalteten Computern zu sammeln. <br> Die gesammelten Daten werden sicher über einen verschlüsselten Kanal an den Azure AD Cloud App Discovery-Dienst gesendet. <br> Die Cloud App Discovery-Daten für eine Organisation sind dann im Azure-Portal sichtbar.


<center>![How Cloud App Discovery Works](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center>


In den folgenden Abschnitten wird entsprechend dem Informationsfluss beschrieben, wie Daten auf dem Weg von Ihrer Organisation zum Cloud App Discovery-Dienst und schließlich zum Cloud App Discovery-Portal geschützt werden.



## Sammeln von Daten aus Ihrer Organisation

Um mithilfe des Azure Active Directory-Features Cloud App Discovery Einblicke in die von Mitarbeitern Ihrer Organisation verwendeten Anwendungen zu erhalten, müssen Sie zuerst Azure AD Cloud App Discovery Endpoint Agents auf Computern in Ihrer Organisation bereitstellen.

Administratoren des Azure Active Directory-Mandanten \(oder ihre Delegaten\) können das Agent-Installationspaket vom Azure-Portal herunterladen. Die Agents können manuell oder mithilfe von SCCM oder Gruppenrichtlinien auf mehreren Computern in der Organisation installiert werden.

Weitere Anweisungen zu Bereitstellungsoptionen finden Sie unter [Bereitstellungshandbuch für Cloud App Discovery mit Gruppenrichtlinien](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### Vom Agent gesammelte Daten

Die Informationen in der folgenden Liste werden vom Agent beim Herstellen von Verbindungen mit Webanwendungen gesammelt. Es werden nur Informationen für solche Anwendungen gesammelt, die der Administrator für die Anwendungsermittlung konfiguriert hat. <br> Die Liste der Anwendungen, für die Sie Metadaten sammeln möchten, kann im Portal auf der Registerkarte "Einstellungen" konfiguriert werden.



> [AZURE.NOTE]Nähere Informationen finden Sie unter [Erste Schritte mit Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).
 
**Informationskategorie**: Benutzerinformationen <br> **Beschreibung**: <br> Der Windows-Benutzername des Prozesses, der eine Anforderung an die Zielwebanwendung gestellt hat \(z. B.: DOMÄNE\\Benutzername\) sowie die Windows-Sicherheits-ID \(SID\) des Benutzers.


**Informationskategorie**: Prozessinformationen <br> **Beschreibung**: <br> Der Name des Prozesses, der die Anforderung an die Zielwebanwendung gestellt hat \(z. B.: "iexplore.exe"\).

**Informationskategorie**: Computerinformationen <br> **Beschreibung**: <br> Der NetBIOS-Name des Computers, auf dem der Agent installiert ist.

**Informationskategorie**: Informationen zum App-Datenverkehr <br> **Beschreibung**: <br>

Die folgenden Verbindungsinformationen:

- Die IP-Adressen und Portnummern der Quelle \(lokaler Computer\) und des Ziels. 

- Die öffentliche IP-Adresse der Organisation, über welche die Anforderung gesendet wird.

- Die Uhrzeit der Anforderung

- Der Umfang des gesendeten und empfangenen Datenverkehrs

- Die IP-Version \(4 oder 6\)

- Nur für TLS-Verbindungen: der Name des Zielhosts aus der Servernamensanzeige-Erweiterung oder dem Serverzertifikat.

Die folgenden HTTP-Informationen:

- Methode \(GET, POST usw.\) 

- Protokoll \(HTTP/1.1 usw.\)

- Benutzer-Agent-Zeichenfolge

- Hostname

- Ziel-URI \(ohne Abfragezeichenfolge\)

- Inhaltstypinformationen

- Verweis-URL-Informationen \(ohne Abfragezeichenfolge\)



> [AZURE.NOTE]Die oben stehenden HTTP-Informationen werden für alle nicht verschlüsselten Verbindungen erfasst. Bei TLS-Verbindungen werden diese Informationen nur aufgezeichnet, wenn die Einstellung "Verschärfte Inspektion" im Portal aktiviert ist. Diese Einstellung ist standardmäßig aktiviert. Nähere Informationen finden Sie unter [Erste Schritte mit Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).
 

 
### Funktionsweise des Agents

Die Agent-Installation umfasst zwei Komponenten:

- Eine Komponente für den Benutzermodus

- Eine Treiberkomponente für den Kernelmodus \(Windows-Filterplattformtreiber\)



Bei der Erstinstallation des Agents wird ein computerspezifisches vertrauenswürdiges Zertifikat auf dem Computer gespeichert, mit dem dann eine sichere Verbindung mit dem Cloud App Discovery-Dienst hergestellt wird. <br> Der Agent ruft in regelmäßigen Abständen über diese sichere Verbindung die Richtlinienkonfiguration vom Cloud App Discovery-Dienst ab. <br> Die Richtlinie enthält unter anderem Informationen über die zu überwachenden Cloudanwendungen und dazu, ob die automatische Aktualisierung aktiviert werden soll.

Während Webdatenverkehr auf dem Computer gesendet und empfangen wird, analysiert Cloud App Discovery den Datenverkehr und extrahiert die relevanten Metadaten \(siehe Tabelle oben\). <br> Einmal pro Minute lädt der Agent die gesammelten Metadaten über einen verschlüsselten Kanal zum Cloud App Discovery-Dienst hoch.

Für verschlüsselte Verbindungen wird ein zusätzlicher Schritt ausgeführt, um die Genauigkeit der gesammelten Daten zu verbessern.<br> Dies wird als verschärfte Inspektion bezeichnet. <br> Die Treiberkomponente fängt den verschlüsselten Datenverkehr ab und fügt sich selbst in den verschlüsselten Stream ein. Hierzu wird auf dem Computer ein selbstsigniertes Stammzertifikat erstellt, das die Clientanwendung veranlasst, dem Cloud App Discovery-Agent zu vertrauen. Dieses selbstsignierte Stammzertifikat ist als nicht exportierbar gekennzeichnet, und der Zugriff wird per ACL auf Administratoren beschränkt. Es bleibt immer auf dem Computer, auf dem es erstellt wurde, und wird nie freigegeben.


### Wahrung der Privatsphäre der Benutzer

Wir möchten Administratoren die nötigen Tools bieten, um entsprechend den Anforderungen ihrer Organisation das richtige Verhältnis zwischen detaillierten Einblicken in die Nutzung von Anwendungen einerseits und Benutzerdatenschutz andererseits festzulegen. Zu diesem Zweck haben wir die folgenden Optionen auf der Einstellungsseite im Portal bereitgestellt:

- **Datensammlung**: Administratoren können angeben, für welche Anwendungen oder Anwendungskategorien Ermittlungsdaten abgerufen werden sollen.


- **Verschärfte Inspektion**: Administratoren können angeben, ob der Agent HTTP-Datenverkehr für SSL/TLS-Verbindungen erfasst.



Der Cloud App Discovery Endpoint Agent sammelt nur die Informationen, die in der Tabelle oben beschrieben sind.



> [AZURE.NOTE]Nähere Informationen finden Sie unter [Erste Schritte mit Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).
 



## Senden von Daten an Cloud App Discovery

Die vom Agent erfassten Metadaten werden bis zu einer Minute lang oder bis die zwischengespeicherten Daten 5 MB erreicht haben, auf dem Computer zwischengespeichert. Anschließend werden sie komprimiert und über eine sichere Verbindung an den Cloud App Discovery-Dienst gesendet.

Wenn die Kommunikation zwischen Agent und Cloud App Discovery-Dienst aus irgendeinem Grund nicht möglich ist, werden die gesammelten Metadaten in einem lokalen Dateicache gespeichert. Auf diesen Cache können nur berechtigte Benutzer auf dem Computer \(z. B. die Gruppe "Administratoren"\) zugreifen. <br> Der Agent sendet die zwischengespeicherten Metadaten automatisch erneut, bis sie vom Cloud App Discovery-Dienst erfolgreich empfangen wurden.



## Empfangen der Daten auf der Dienstseite

Die Agents authentifizieren sich mithilfe des oben erwähnten computerspezifischen Clientauthentifizierungszertifikats beim Cloud App Discovery-Dienst und leiten Daten über einen verschlüsselten Kanal weiter. <br> Die Analysepipeline des Cloud App Discovery-Diensts verarbeitet Metadaten für jeden Kunden getrennt. Dazu werden die Daten über alle Phasen der Analysepipeline hinweg logisch partitioniert. Aus den analysierten Metadaten werden im Portal die verschiedenen Berichte erstellt.

Sowohl die unverarbeiteten als auch die analysierten Metadaten werden bis zu 180 Tage lang gespeichert. Darüber hinaus können Kunden die analysierten Metadaten in einem Azure-Blobspeicherkonto ihrer Wahl erfassen. Dies ermöglicht Offlineanalysen und eine längere Aufbewahrung der Daten.

## Zugreifen auf die Daten über das Azure-Portal

Um die Sicherheit der erfassten Metadaten zu gewährleisten, haben standardmäßig nur globale Administratoren des Mandanten Zugriff auf das Cloud App Discovery-Feature im Azure-Portal. <br> Administratoren können diesen Zugriff jedoch an andere Benutzer oder Gruppen delegieren.



> [AZURE.NOTE]Nähere Informationen finden Sie unter [Erste Schritte mit Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).
 


Jeder Benutzer, der auf die Daten im Portal zugreift, muss mit einer Azure AD Premium-Lizenz lizenziert sein.



**Weitere Ressourcen**


* [Wie ermittle ich nicht genehmigte Cloud-Apps, die in meiner Organisation verwendet werden?](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=July15_HO5-->