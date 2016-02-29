<properties 
	pageTitle="LDAP-Authentifizierung und Azure Multi-Factor Authentication-Server" 
	description="Dies ist die Azure Multi-Factor Authentication-Seite, die bei der Bereitstellung der LDAP-Authentifizierung und des Azure Multi-Factor Authentication-Servers Unterstützung bietet." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# LDAP-Authentifizierung und Azure Multi-Factor Authentication-Server 


Standardmäßig ist der Azure Multi-Factor Authentication-Server zum Importieren oder Synchronisieren von Benutzern aus Active Directory konfiguriert. Er kann jedoch auch zum Binden an andere LDAP-Verzeichnisse konfiguriert sein, z. B. an ein ADAM-Verzeichnis oder einen bestimmten Active Directory-Domänencontroller. Wenn die Verbindung mit einem Verzeichnis über LDAP konfiguriert ist, kann der Azure Multi-Factor Authentication-Server als LDAP-Proxy zur Ausführung von Authentifizierungen konfiguriert werden. Das ermöglicht auch die Verwendung einer LDAP-Bindung als RADIUS-Ziel für die Vorauthentifizierung von Benutzern bei der Verwendung der IIS-Authentifizierung oder für die primäre Authentifizierung im Azure Multi-Factor Authentication-Benutzerportal.

Wenn Sie Multi-Factor Authentication als LDAP-Proxy verwenden, wird der Azure Multi-Factor Authentication-Server zwischen dem LDAP-Client (z. B. VPN-Appliance, Anwendung) und dem LDAP-Verzeichnisserver eingefügt, um Multi-Factor Authentication hinzuzufügen. Damit Azure Multi-Factor Authentication funktioniert, muss der Azure Multi-Factor Authentication-Server für die Kommunikation sowohl mit den Clientservern als auch dem LDAP-Verzeichnis konfiguriert werden. In dieser Konfiguration akzeptiert der Azure Multi-Factor Authentication-Server LDAP-Anforderungen von Clientservern und -anwendungen und leitet sie zur Überprüfung der primären Anmeldeinformationen an den Ziel-LDAP-Verzeichnisserver weiter. Wenn die Antwort des LDAP-Verzeichnisses bestätigt, dass die primären Anmeldeinformationen gültig sind, führt Multi-Factor Authentication die zweistufige Authentifizierung aus und sendet eine Antwort zurück an den LDAP-Client. Die gesamte Authentifizierung ist nur erfolgreich, wenn sowohl die Authentifizierung beim LDAP-Server als auch die mehrstufige Authentifizierung erfolgreich verlaufen sind.





## Konfiguration der LDAP-Authentifizierung


Zum Konfigurieren der LDAP-Authentifizierung installieren Sie den Azure Multi-Factor Authentication-Server auf einem Windows-Server. Gehen Sie dazu wie folgt vor:

1. Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol "LDAP-Authentifizierung".
2. Aktivieren Sie das Kontrollkästchen für die LDAP-Authentifizierung.![LDAP-Authentifizierung](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png) 
3. Ändern Sie auf der Registerkarte "Clients" den TCP- und SSL-Anschluss, wenn der Azure Multi-Factor Authentication-LDAP-Dienst an nicht-Standardports gebunden werden soll, um LDAP-Anforderungen von Clients zu überwachen, die konfiguriert werden.
4. Wenn Sie LDAPS vom Client an den Azure Multi-Factor Authentication-Server verwenden möchten, muss ein SSL-Zertifikat auf dem Server installiert sein, auf das der Server ausgeführt wird. Klicken Sie auf die Schaltfläche "Durchsuchen" neben dem Feld "SSL-Zertifikat", und wählen Sie das installierte Zertifikat, das für die sichere Verbindung verwendet wird. 
5. Klicken Sie auf die Schaltfläche "Hinzufügen".
6. Geben Sie im Dialogfeld "LDAP-Client hinzufügen" die IP-Adresse der Appliance, des Servers oder der Anwendung ein, die auf dem Server authentifiziert wird, und einen Anwendungsnamen (optional). Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt.
7. Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erforderlich", wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert. Weitere Informationen zu dieser Funktion finden Sie in der Hilfedatei. 
8. Sie können die Schritte 5 bis 7 wiederholen, um zusätzliche LDAP-Clients hinzuzufügen.
9. Wenn die Multi-Factor Authentication zum Empfangen von LDAP-Authentifizierungen konfiguriert ist, müssen diese Authentifizierungen per Proxy an das LDAP-Verzeichnis weitergegeben werden. Daher zeigt die Registerkarte "Ziel" nur eine einzelne, grau unterlegte Option zum Verwenden eines LDAP-Ziels an. Zum Konfigurieren der LDAP-Verzeichnisverbindung klicken Sie auf das Symbol "Verzeichnisintegration". 
10. Aktivieren Sie auf der Registerkarte "Einstellungen" das Optionsfeld "Bestimmte LDAP-Konfiguration verwenden".
11. Klicken Sie auf die Schaltfläche "Bearbeiten".
12. Füllen Sie im Dialogfeld "LDAP-Konfiguration bearbeiten" die Felder mit den Informationen aus, die für die Verbindung mit dem LDAP-Verzeichnis erforderlich sind. In der folgenden Tabelle finden Sie die Beschreibungen der Felder. Hinweis: Diese Informationen sind auch in der Hilfedatei für den Azure Multi-Factor Authentication-Server enthalten.![Verzeichnisintegration](./media/multi-factor-authentication-get-started-server-ldap/ldap.png) 
13. Testen Sie die LDAP-Verbindung durch Klicken auf die Schaltfläche "Testen".
14. Wenn der Test der LDAP-Verbindung erfolgreich war, klicken Sie auf die Schaltfläche "OK". 
15. Klicken Sie auf die Registerkarte "Filter". Der Server ist so vorkonfiguriert, Container, Sicherheitsgruppen und Benutzer aus Active Directory zu laden. Wenn Sie eine Bindung an ein anderes LDAP-Verzeichnis benötigen, müssen Sie wahrscheinlich die angezeigten Filter bearbeiten. Klicken Sie auf den Hilfe-Link, um weitere Informationen zu Filtern zu erhalten.
16. Klicken Sie auf die Registerkarte "Attribute". Der Server ist so vorkonfiguriert, dass Attribute aus Active Directory zugeordnet werden.
17. Bei Bindung an ein anderes LDAP-Verzeichnis oder bei Änderung der vorkonfigurierten Attributzuordnungen klicken Sie auf die Schaltfläche "Bearbeiten".
18. Ändern Sie im Dialogfeld "Attribute bearbeiten" die LDAP-Attributzuordnungen für Ihr Verzeichnis. Attributnamen können eingegeben oder ausgewählt werden, indem Sie auf die Schaltfläche "..." neben jedem Feld klicken.
19. Klicken Sie auf den Hilfe-Link, um weitere Informationen zu Attributen zu erhalten.
20. Klicken Sie auf die Schaltfläche "OK".
21. Klicken Sie auf das Symbol "Unternehmenseinstellungen" und wählen Sie die Registerkarte "Benutzernamenauflösung" aus. 22. Wenn von einem Domänen-verbundenen Server auf Active Directory verbunden wird, sollten Sie das Optionsfeld "Windows-Sicherheits-IDs (SIDs) für den Abgleich von Benutzernamen verwenden" aktiviert lassen. Wählen Sie andernfalls das Optionsfeld "Eindeutiges LDAP-Bezeichnerattribut für den Abgleich von Benutzernamen verwenden" aus. Bei Auswahl dieser Option versucht der Azure Multi-Factor Authentication-Server, alle Benutzernamen in einen eindeutigen Bezeichner im LDAP-Verzeichnis aufzulösen. Eine LDAP-Suche erfolgt auf den Benutzernamenattributen, die in der Registerkarte "Verzeichnisintegration > Attribute" definiert werden. Wenn ein Benutzer authentifiziert wird, wird der Benutzername in den eindeutigen Bezeichner im LDAP-Verzeichnis aufgelöst, und der eindeutige Bezeichner wird für den Abgleich des Benutzers in der Multi-Factor Authentication-Datendatei verwendet. Dies ermöglicht Vergleiche ohne Berücksichtigung der Groß-und Kleinschreibung sowie lange als auch kurze Benutzernamenformate. Damit ist die Konfiguration für den Azure Multi-Factor Authentication-Server abgeschlossen. Der Server lauscht jetzt an den konfigurierten Ports nach LDAP-Zugriffsanforderungen von den konfigurierten Clients, und sendet diese Anfragen zur Authentifizierung per Proxy an das LDAP-Verzeichnis.


## LDAP-Clientkonfiguration

Verwenden Sie die folgenden Richtlinien, um den LDAP-Client zu konfigurieren:

- Konfigurieren Sie Ihre Appliance, den Server oder die Anwendung so, dass die Authentifizierung über LDAP an den Azure Multi-Factor Authentication-Server stattfindet, als wäre es Ihr LDAP-Verzeichnis. Sie sollten die gleichen Einstellungen verwenden, die Sie normalerweise für die direkte Verbindung mit Ihrem LDAP-Verzeichnis verwenden würden, mit Ausnahme des Servernamens oder der IP-Adresse, die dem Azure Multi-Factor Authentication-Server entsprechen. 
- Konfigurieren Sie das LDAP-Timeout auf 30 bis 60 Sekunden, sodass genug Zeit ist, die Anmeldeinformationen des Benutzers mit dem LDAP-Verzeichnis zu überprüfen, die zweistufige Authentifizierung durchzuführen, deren Antwort zu empfangen und dann auf die LDAP-Zugriffsanforderung zu reagieren. 
- Bei Verwendung von LDAPs muss die Appliance oder der Server, die das LDAP abfragen, dem auf dem Azure Multi-Factor Authentication-Server installierten SSL-Zertifikat vertrauen.

<!---HONumber=AcomDC_0218_2016-->