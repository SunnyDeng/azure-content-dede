<properties
	pageTitle="Aktivieren des Azure AD-Anwendungsproxys | Microsoft Azure"
	description="Erläutert, wie Sie den Azure AD-Anwendungsproxy betriebsbereit machen."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/19/2015"
	ms.author="rkarlin"/>

# Aktivieren des Azure AD-Anwendungsproxys
> [AZURE.NOTE]Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Der Microsoft Azure AD-Anwendungsproxy ermöglicht Ihnen das Veröffentlichen von Anwendungen, z. B. SharePoint-Websites, Outlook Web Access und IIS-basierten Apps innerhalb Ihres privaten Netzwerks und bietet den sicheren Zugriff für Benutzer außerhalb Ihres Netzwerks. Mitarbeiter können sich auf ihren eigenen Geräten von zu Hause aus bei Ihren Apps anmelden und sich über diesen cloudbasierten Proxy authentifizieren.

Dieser Abschnitt führt Sie durch die Aktivierung des Microsoft Azure AD-Anwendungsproxys für Ihr Cloudverzeichnis in Azure AD, die Installation des Anwendungsproxyconnectors in Ihrem privaten Netzwerk und die Registrierung des Connectors bei Ihrem Microsoft Azure AD-Mandantenabonnement.

##Voraussetzungen für den Anwendungsproxy
Bevor Sie die Anwendungsproxydienste aktivieren und verwenden können, benötigen Sie Folgendes:

- Ein Microsoft Azure-Administratorkonto. Wenn Sie kein solches Konto besitzen, können Sie es hier einrichten.
- Einen Server, auf dem Windows Server 2012 R2 bzw. Windows 8.1 oder höher installiert ist, auf dem Sie den Anwendungsproxyconnector installieren können. Der Server muss in der Lage sein, HTTPS-Anforderungen an die Anwendungsproxydienste in der Cloud zu senden, und er muss über eine HTTPS-Verbindung mit den Anwendungen verfügen, die Sie veröffentlichen möchten. 
- Wenn im Pfad eine Firewall platziert ist, vergewissern Sie sich, dass die Firewall HTTPS-Anforderungen (TCP) zulässt, die vom Connector für den Anwendungsproxy stammen. Der Connector verwendet diese Ports zusammen mit untergeordneten Domänen, die Teil der Domäne auf hoher Ebene sind: msappproxy.net. Stellen Sie sicher, dass **alle** folgenden Ports für den **ausgehenden** Datenverkehr geöffnet sind:

Portnummer | Beschreibung
--- | ---
80 | Ermöglicht ausgehenden HTTP-Verkehr für die Sicherheitsüberprüfung.
443 | Ermöglicht die Benutzerauthentifizierung für Azure AD (nur für den Connectorregistrierungsprozess erforderlich).
10100 - 10120 | Ermöglicht das Zurücksenden von LOB HTTP-Antworten an den Proxy.
9352, 5671 | Ermöglicht eine Kommunikation zwischen Connector und Azure-Dienst für eingehende Anforderungen.
9350 | Optional. Verbessert die Leistung für eingehende Anforderungen.
8080 | Aktiviert die Bootstrapsequenz des Connectors und ermöglicht eine automatische Aktualisierung des Connectors.
9090 | Ermöglicht die Connectorregistrierung (nur für den Connectorregistrierungsprozess erforderlich).
9091 | Ermöglicht die automatische Erneuerung des Vertrauenszertifikats für den Connector.
 
Wenn Ihre Firewall Datenverkehr gemäß Ursprungsbenutzern erzwingt, öffnen Sie diese Ports für den Datenverkehr aus Windows-Diensten, die als Netzwerkdienst ausgeführt werden. Stellen Sie außerdem sicher, dass Port 8080 für "NT Authority\\System" aktiviert ist.


##Schritt 1: Aktivieren des Anwendungsproxys in Azure AD
1. Melden Sie sich als Administrator beim Azure-Verwaltungsportal an.
2. Gehen Sie zu Active Directory, und wählen Sie das Verzeichnis aus, in dem Sie den Anwendungsproxy aktivieren möchten.
3. Klicken Sie auf "Konfigurieren", führen Sie einen Bildlauf nach unten zu "Anwendungsproxy" aus, und stellen Sie "Anwendungsproxydienste für dieses Verzeichnis aktivieren" auf "Aktiviert" ein.

	![Aktivieren des Anwendungsproxys](./media/active-directory-application-proxy-enable/app_proxy_enable.png) <p>
4. Klicken Sie im unteren Bildschirmbereich auf "Jetzt herunterladen". Damit gelangen Sie zur Downloadseite. Lesen und akzeptieren Sie die Lizenzbedingungen, und klicken Sie auf "Herunterladen", um die Windows Installer-Datei (.exe) für den Anwendungsproxy-Connector herunterzuladen. 

##Schritt 2: Installieren und Registrieren des Connectors
1. Führen Sie die Datei "AADApplicationProxyConnectorInstaller.exe" auf dem Server aus, den Sie vorbereitet haben (siehe Voraussetzungen für den Anwendungsproxy).
2. Befolgen Sie die Anweisungen des Assistenten für die Installation.
3. Während der Installation werden Sie aufgefordert, den Connector bei Ihrem aktiven Anwendungsproxy-Konto zu registrieren.
<p>Geben Sie Ihre globalen Azure AD-Administratoranmeldeinformationen ein:
- Stellen Sie sicher, dass sich der Administrator, der den Connector registriert, im gleichen Verzeichnis befindet, in dem Sie den Anwendungsproxydienst aktiviert haben. Wenn die Mandantendomäne beispielsweise "contoso.com" lautet, sollte der Administrator sich als admin@contoso.com oder mit einem anderen Aliasnamen in dieser Domäne anmelden. Außerdem ist ein globaler Administrator des Azure AD-Mandanten erforderlich. Ihr globaler Administratormandant kann von Ihren Microsoft Azure-Anmeldeinformationen abweichen.
- Wenn auf dem Server, auf dem Sie den Azure AD-Connector installieren, die Option "Verstärkte Sicherheitskonfiguration für IE" aktiviert ist, wird der Registrierungsbildschirm möglicherweise gesperrt. Befolgen Sie in diesem Fall die Anweisungen in der Fehlermeldung, um den Zugriff zuzulassen. Stellen Sie sicher, dass die verstärkte Sicherheitskonfiguration für Internet Explorer deaktiviert ist.
- Wenn die Connectorregistrierung nicht erfolgreich ist, lesen Sie "Problembehandlung von Anwendungsproxys".

4. Nach Abschluss der Installation werden zwei neue Dienste auf dem Server hinzugefügt, wie unten dargestellt. Diese sind der Connectordienst, der Konnektivität ermöglicht, und ein automatischer Updatedienst, der in regelmäßigen Abständen prüft, ob neue Versionen des Connectors verfügbar sind, und den Connector bei Bedarf aktualisiert. Klicken Sie im Installationsfenster auf "Fertig stellen", um die Installation abzuschließen.
	![Anwendungsproxyconnectordienst](http://i.imgur.com/zsVJKOz.png) <p>

5. Sie sind nun bereit für den Schritt "Veröffentlichen von Anwendungen mit einem Anwendungsproxy".

Wenn Sie den Connector deinstallieren möchten, stellen Sie nach der Deinstallation des Connector- und Updatediensts sicher, dass der Computer neu gestartet wird, um den Dienst vollständig zu entfernen. <p>Wenn hohe Verfügbarkeit gewünscht wird, müssen Sie mindestens einen weiteren Connector bereitstellen. Wiederholen Sie zum Bereitstellen eines weiteren Connectors die oben beschriebenen Schritte 2 und 3. Jeder Connector muss separat registriert werden.



## Weitere Informationen
Der Anwendungsproxy bietet Ihnen noch viele weitere Möglichkeiten:

- [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md)
- [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](active-directory-application-proxy-custom-domains.md)
- [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivieren des bedingten Zugriffs](active-directory-application-proxy-conditional-access.md)
- [Arbeiten mit Anwendungen, die Ansprüche unterstützen](active-directory-application-proxy-claims-aware-apps.md)
- [Problembehandlung von Anwendungsproxys](active-directory-application-proxy-troubleshoot.md)

## Weitere Informationen zum Anwendungsproxy
- [Onlinehilfe anzeigen](active-directory-application-proxy-enable.md)
- [Blog zum Anwendungsproxy aufrufen](http://blogs.technet.com/b/applicationproxyblog/)
- [Sehen Sie sich unsere Videos auf Channel 9 an!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Zusätzliche Ressourcen

* [Als Organisation für Azure registrieren](sign-up-organization.md)
* [Azure-Identität](fundamentals-identity.md)
* [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md)

<!---HONumber=Oct15_HO4-->