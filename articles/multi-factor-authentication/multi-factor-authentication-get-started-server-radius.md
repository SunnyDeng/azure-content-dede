<properties 
	pageTitle="RADIUS-Authentifizierung und Azure Multi-Factor Authentication-Server" 
	description="Dies ist die Azure Multi-Factor Authentication-Seite, die bei der Bereitstellung der RADIUS-Authentifizierung und des Azure Multi-Factor Authentication-Servers Unterstützung bietet." 
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



# RADIUS-Authentifizierung und Azure Multi-Factor Authentication-Server

Der Abschnitt für die RADIUS-Authentifizierung hilft Ihnen dabei, die RADIUS-Authentifizierung für den Azure Multi-Factor Authentication-Server zu aktivieren und zu konfigurieren. RADIUS ist ein Standardprotokoll, um Authentifizierungsanforderungen zu akzeptieren und diese Anforderungen zu verarbeiten. Der Azure Multi-Factor Authentication-Server fungiert als RADIUS-Server und wird zwischen Ihren RADIUS-Client (z. B. einer VPN-Appliance) und Ihr Authentifizierungsziel, z. B. Active Directory (AD), einem LDAP-Verzeichnis oder einen anderen RADIUS-Server eingefügt, um Multi-Factor Authentication hinzuzufügen. Damit Azure Multi-Factor Authentication funktioniert, müssen Sie den Azure Multi-Factor Authentication-Server so konfigurieren, dass er sowohl mit den Clientservern als auch dem Authentifizierungsziel kommunizieren kann. Der Azure Multi-Factor Authentication-Server akzeptiert Anforderungen von einem RADIUS-Client, überprüft die Anmeldeinformationen für das Authentifizierungsziel, fügt Azure Multi-Factor Authentication hinzu, und sendet eine Antwort zurück an den RADIUS-Client. Die gesamte Authentifizierung ist nur erfolgreich, wenn die primäre Authentifizierung und die mehrstufige Authentifizierung über Azure Multi-Factor Authentication erfolgreich verlaufen sind.

![RADIUS-Authentifizierung](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## Konfiguration der RADIUS-Authentifizierung

Zum Konfigurieren der RADIUS-Authentifizierung installieren Sie den Azure Multi-Factor Authentication-Server auf einem Windows-Server. Wenn Sie über eine Active Directory-Umgebung verfügen, sollte der Server der Domäne innerhalb des Netzwerks angehören. Verwenden Sie das folgende Verfahren, um den Azure Multi-Factor Authentication-Server zu konfigurieren:

1. Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol "RADIUS-Authentifizierung".
2. Aktivieren Sie das Kontrollkästchen für die RADIUS-Authentifizierung.
3. Ändern Sie auf der Registerkarte "Clients" den/die Authentifizierungsport/s und den/die Kontoführungsport/s, wenn der RADIUS-Dienst von Azure Multi-Factor Authentication an nicht-Standardports gebunden werden soll, um RADIUS-Anforderungen von Clients zu überwachen, die konfiguriert werden.
4. Klicken Sie auf die Schaltfläche "Hinzufügen".
5. Geben Sie im Dialogfeld "RADIUS-Client" die IP-Adresse der Appliance/des Servers ein, die/der auf dem Azure Multi-Factor Authentication-Server authentifiziert wird, einen Anwendungsnamen (optional) und einen gemeinsamen geheimen Schlüssel. Der gemeinsame geheime Schlüssel muss auf dem Azure Multi-Factor Authentication-Server und in der Appliance/auf dem Server identisch sein. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt.
6. Aktivieren Sie das Kontrollkästchen "Multi-Factor Authentication-Benutzerabgleich erfordern", wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert. Weitere Informationen zu dieser Funktion finden Sie in der Hilfedatei.
7. Überprüfen Sie das Feld "Alternatives OATH-Token aktivieren", wenn Benutzer die Authentifizierung über die mobile App von Multi-Factor Authentication verwenden, und Sie OATH-Kennungen als Alternative zur Authentifizierung mit Out-of-Band-Telefonanrufen, SMS oder Push-Benachrichtigungen verwenden möchten.
8. Klicken Sie auf die Schaltfläche "OK".
9. Sie können die Schritte 4 bis 8 wiederholen, um zusätzliche RADIUS-Clients hinzuzufügen.
10. Klicken Sie auf die Registerkarte "Ziel".
11. Wenn der Azure Multi-Factor Authentication-Server auf einem domänenverbundenen Server in einer Active Directory-Umgebung installiert ist, wählen Sie die Windows-Domäne.
12. Wenn der Benutzer mit einem LDAP-Verzeichnis authentifiziert werden sollen, wählen Sie die LDAP-Bindung. Bei der Verwendung von LDAP-Bindungen müssen Sie auf das Symbol "Verzeichnisintegration" klicken, und die LDAP-Konfiguration auf der Registerkarte "Einstellungen" bearbeiten, damit der Server in Ihr Verzeichnis binden kann. Anweisungen zum Konfigurieren von LDAP finden Sie im Konfigurationshandbuch zum LDAP-Proxy. 
13. Wenn Benutzer für einen anderen RADIUS-Server authentifiziert werden sollen, wählen Sie den/die RADIUS-Server.
14. Konfigurieren Sie den Server, auf den der Server die RADIUS-Anforderungen per Proxy weitergibt, durch Klicken auf die Schaltfläche "Hinzufügen".
15. Geben Sie im Dialogfeld "RADIUS-Server hinzufügen" die IP-Adresse des RADIUS-Servers und einen gemeinsamen geheimen Schlüssel ein. Der gemeinsame geheime Schlüssel muss auf dem Azure Multi-Factor Authentication-Server und dem RADIUS-Server identisch sein. Ändern Sie den Authentifizierungs- und Kontoführungsport, wenn der RADIUS-Server andere Ports verwendet.
16. Klicken Sie auf die Schaltfläche "OK". 
17. Sie müssen den Azure Multi-Factor Authentication-Server als RADIUS-Client auf dem anderen RADIUS-Server hinzufügen, damit aus dem Azure Multi-Factor Authentication-Server gesendete Zugriffsanforderungen verarbeitet werden. Sie müssen den gleichen gemeinsamen geheimen Schlüssel verwenden, der in Azure Multi-Factor Authentication-Server konfigurierten ist.
18. Sie können diesen Schritt wiederholen, um zusätzliche RADIUS-Server hinzuzufügen und die Reihenfolge zu konfigurieren, in der der Server diese mit den Schaltflächen "Nach oben" und "Nach unten" aufruft. Dadurch wird die Konfiguration des Azure Multi-Factor Authentication-Servers abgeschlossen. Der Server wartet jetzt auf den konfigurierten Ports auf RADIUS-Zugriffsanforderungen von den konfigurierten Clients.   


## RADIUS-Clientkonfiguration

Verwenden Sie die folgenden Richtlinien, um den RADIUS-Client zu konfigurieren:

- Konfigurieren Sie Ihre Appliance/Ihren Server zur Authentifizierung über RADIUS an die IP-Adresse des Azure Multi-Factor Authentication-Servers, die als RADIUS-Server fungiert. 
- Verwenden Sie den gleichen gemeinsamen geheimen Schlüssel, der weiter oben konfiguriert wurde. 
- Konfigurieren Sie den RADIUS-Timeout auf 30 bis 60 Sekunden, sodass genug Zeit ist, die Anmeldeinformationen des Benutzers zu überprüfen, die mehrstufige Authentifizierung auszuführen, deren Antwort zu empfangen, und dann auf die RADIUS-Zugriffsanforderung zu reagieren.

<!---HONumber=July15_HO4-->