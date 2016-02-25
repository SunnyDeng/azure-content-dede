<properties
	pageTitle="Sichern einer App in Azure App Service"
	description="Erfahren Sie, wie Sie eine Web-App, ein mobile App Back-End oder eine API-App in Azure App Service sichern."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="cephalin"/>


#Sichern einer App in Azure App Service

Dieser Artikel hilft Ihnen beim Einstieg ins Sichern Ihrer Web-App, Ihres mobile App Back-End oder Ihrer API-App in Azure App Service.

Die Sicherheit in Azure App Service basiert auf zwei Stufen:

- **Infrastruktur- und Plattform-Sicherheit** -Sie vertrauen darauf, dass Azure Ihnen die Dienste bereitstellt, die Sie benötigen, um die Cloud sicher verwenden zu können.
- **Anwendungssicherheit** - Sie müssen die Anwendung selbst sicher entwerfen. Dies schließt mit ein, wie Sie Azure Active Directory verwenden, wie Sie Zertifikate verwalten und die sichere Kommunikation mit verschiedenen Diensten gewährleisten. 

#### Infrastruktur und Plattform-Sicherheit
Da der App Service die Azure-VMs, den Speicher, Netzwerkschnittstellen, Web-Frameworks, Verwaltungs- und Integrationsfeatures und vieles mehr enthält, ist dieser aktiv gesichert und festgeschrieben und durchläuft kontinuierlich strenge Konformitätsprüfungen, um sicherzustellen, dass:

- Ihre App Service-Apps sowohl vom Internet als auch von den Azure-Ressourcen anderer Kunden isoliert sind.
- Die Kommunikation der geheimen Schlüssel (z. B. Verbindungszeichenfolgen) zwischen Ihrer App Service-App und anderen Azure-Ressourcen (z. B. SQL-Datenbank) in einer Ressourcengruppe, innerhalb von Azure bleibt und Netzwerkgrenzen nicht überschreitet. Geheime Schlüssel immer verschlüsselt sind.
- Die gesamte Kommunikation zwischen Ihrer App Service-App und externen Ressourcen, z. B. PowerShell-Verwaltung, Befehlszeilenschnittstelle, Azure-SDKs, REST-APIs und Hybrid-Verbindungen ordnungsgemäß verschlüsselt ist.
- Das 24-Stunden-Threat Management schützt die App Service-Ressourcen vor Malware, verteilten Denial-of-Service (DDoS), Man-in-the-Middle (MITM) und anderen Bedrohungen. 

Weitere Informationen zur Infrastruktur und Plattform-Sicherheit in Azure finden Sie unter [Azure Trust Center](/support/trust-center/security/).

#### Anwendungssicherheit

Während Azure dafür verantwortlich ist, die Infrastruktur und Plattform zu sichern, auf der Ihre Anwendung ausgeführt wird, ist es Ihre Aufgabe die Anwendung selbst zu sichern. Das heißt, Sie müssen Ihren Anwendungscode und den Inhalt in einer sicheren Weise entwickeln, bereitstellen und verwalten. Ansonsten kann Ihr Anwendungscode oder Inhalt noch immer anfällig für Sicherheitsrisiken sein, wie z. B.:

- Einschleusung von SQL-Befehlen
- Session Hijacking
- Cross-Site Scripting
- MITM-Angriffen auf Anwendungsebene
- DDoS auf Anwendungsebene

Eine umfassende Erläuterung der Sicherheitsaspekte webbasierter Anwendungen wird über den Rahmen dieses Dokuments sprengen. Weitere Informationen zur Sicherung von Webanwendungen finden Sie unter [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page), insbesondere unter dem [Top 10-Projekt](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), das die 10 derzeit kritischsten Sicherheitsschwachstellen auflistet, die von OWASP-Mitgliedern angegeben wurden.

## Ausführen von Penetrationstests für Ihre App

Eine der einfachsten Möglichkeiten, mit der Sie Ihre App Service-App auf Sicherheitslücken testen können, ist die Verwendung der [Integration in Tinfoil Security](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/), die diese per Mausklick auf Schwachstellen überprüft. Sie können die Testergebnisse in einem leicht verständlichen Bericht anzeigen lassen, und erfahren, wie Sie jede Sicherheitslücke mit einer Schritt-für-Schritt-Anleitung beheben können.

Wenn Sie lieber Ihre eigenen Penetrationstests ausführen möchten oder eine andere Scanner Suite oder einen anderen Anbieter verwenden möchten, befolgen Sie den [Azure Penetrationstests-Genehmigungsprozess](https://security-forms.azure.com/penetration-testing/terms) und erhalten Sie vorherige Genehmigungen zum Ausführen der gewünschten Penetrationstests.

##<a name="https"></a> Sichere Kommunikation mit Kunden

Wenn Sie den Domänennamen „**\*.azurewebsites.net**“ nutzen, der für Ihren App Service erstellt wurde, können Sie HTTPS sofort verwenden, da ein SSL-Zertifikat für alle Domänennamen vom Typ „**\*.azurewebsites.net**“ bereitgestellt wird. Wenn Ihre Site einen [kundenspezifischen Domänennamen](web-sites-custom-domain-name.md) verwendet, können Sie ein SSL-Zertifikat hochladen, um für die kundenspezifische Domäne [HTTPS zu aktivieren](web-sites-configure-ssl-certificate.md).

Aktivieren von [HTTPS](https://en.wikipedia.org/wiki/HTTPS) kann gegen MITM-Angriffe auf die Kommunikation zwischen Ihrer Anwendung und deren Benutzern schützen.

## Sichern der Datenebene

App Service ist mit der SQL-Datenbank so vernetzt, dass alle Verbindungszeichenfolgen durchgehend verschlüsselt werden. Nur auf dem virtuellen Computer, auf dem die Anwendung ausgeführt wird, kann diese entschlüsselt werden *und* auch nur wenn die Anwendung ausgeführt wird. Darüber hinaus enthält die Azure SQL-Datenbank viele Sicherheitsfeatures zum Schutz Ihrer Anwendungsdaten vor Bedrohungen aus dem Internet, darunter [ruhende Verschlüsselung](https://msdn.microsoft.com/library/dn948096.aspx), [immer verschlüsselt](https://msdn.microsoft.com/library/mt163865.aspx), [dynamische Datenmaskierung](../sql-database/sql-database-dynamic-data-masking-get-started.md) und [Bedrohungserkennung](sql-database-threat-detection-get-started). Wenn für Sie Anforderungen aufgrund vertraulicher Daten oder der Compliance gelten, finde Sie unter [Sichern Ihrer SQL-Datenbank](../sql-database/sql-database-security.md) weitere Informationen zum Schutz Ihrer Daten.

Wenn Sie eine Drittanbieter-Datenbank, wie z. B. ClearDB verwenden, sollten Sie sich an die vom Provider bereitgestellte Dokumentation zum Thema bewährte Sicherheitsmethoden halten.

##<a name="develop"></a>Sichere Entwicklung und Bereitstellung

### Veröffentlichung von Profilen und Einstellungen

Bei der Entwicklung von Anwendungen, der Durchführung von Verwaltungsaufgaben oder bei der Automatisierung von Aufgaben, die **Visual Studio**, **Web Matrix**, **Azure PowerShell** oder die **Azure-Befehlszeilenschnittstelle (Azure-CLI)** verwenden, können Sie entweder eine Datei mit *Veröffentlichungseinstellungen* oder mit *Veröffentlichungsprofil* nutzen. Beide Datentypen authentifizieren Sie bei Azure und sollten geschützt werden, um unautorisierten Zugriff zu verhindern.

* Eine Datei mit **Veröffentlichungseinstellungen** enthält

	* ID Ihres Azure-Abonnements

	* Ein Verwaltungszertifikat, das es Ihnen ermöglicht, Verwaltungsaufgaben für Ihr Abonnement durchzuführen, *ohne einen Kontonamen oder ein Passwort angeben zu müssen*.

* Eine Datei mit **Veröffentlichungsprofil** enthält

	* Informationen zum Veröffentlichen in Ihrer Web-App

Wenn Sie ein Dienstprogramm nutzen, das Veröffentlichungseinstellungen oder ein Veröffentlichungsprofil verwendet, importieren Sie die Datei, welche die Veröffentlichungseinstellungen oder das Profil enthält, in das Dienstprogramm und **löschen** Sie anschließend die Datei. Wenn Sie die Datei behalten müssen, beispielsweise um sie mit anderen zu teilen, die an dem Projekt mitarbeiten, speichern Sie sie an einem sicheren Ort, z.B. in einem *verschlüsselten* Verzeichnis mit begrenzten Berechtigungen.

Zusätzlich sollten Sie sicherstellen, dass die importierten Berechtigungsnachweise sicher sind. Beispielsweise speichern **Azure PowerShell** und die **Azure-Befehlszeilenschnittstelle** importierte Daten in Ihrem **Basisverzeichnis** (*~* bei Linux- oder OS X-Systemen und */Benutzer/IhrBenutzername* bei Windows-Systemen.) Als zusätzliche Sicherheit können Sie diese Verzeichnisse mit Verschlüsselungstools **verschlüsseln**, die für Ihr Betriebssystem erhältlich sind.

### Konfigurationseinstellungen und Verbindungszeichenfolgen
Es ist üblich, Verbindungszeichenfolgen, Berechtigungsnachweise zur Authentisierung und andere sensible Daten in Konfigurationsdateien zu speichern. Leider können sich diese Dateien ungeschützt auf ihrer Website befinden oder in ein öffentliches Repository verschoben werden, wodurch diese Daten gefährdet werden können. Eine einfache Suche, z. B. nach „[GitHub](https://github.com)“, kann unzählige Konfigurationsdateien mit sichtbaren geheimen Schlüsseln in den öffentlichen Repositories aufdecken.

Die bewährte Methode ist, diese Informationen aus Ihren App-Konfigurationsdateien herauszuhalten. Azure App Service ermöglicht es Ihnen, Konfigurationsdaten als Teil der Laufzeitumgebung von Web-Apps, als **App-Einstellungen** und als **Verbindungszeichenfolgen** zu speichern. Die Werte werden für Ihre Anwendung zur Laufzeit über *Umgebungsvariablen* für die meisten Programmiersprachen freigegeben. Bei .NET-Anwendungen werden diese Werte in Ihre .NET-Konfiguration zur Laufzeit eingegeben. Von diesen Situationen abgesehen, bleiben diese Konfigurationseinstellungen verschlüsselt, es sei denn Sie zeigen diese an oder konfigurieren sie mithilfe des [Azure-Portals](https://portal.azure.com) oder mit Hilfsprogrammen wie PowerShell oder der Azure-Befehlszeilenschnittstelle.

Das Speichern von Konfigurationsinformationen in App Service ermöglicht es dem App-Administrator, vertrauliche Daten für die Produktions-Apps zu sperren. Entwickler können einen separaten Satz von Konfigurationen für die App-Entwicklung nutzen und die Einstellungen können durch die Einstellungen in App Service automatisch ersetzt werden. Selbst die Entwickler dürfen die geheimen Schlüssel nicht kennen, die für die Produktions-App konfiguriert worden sind. Weitere Informationen zu App-Einstellungen und Verbindungszeichenfolgen in App Service finden Sie unter [Konfigurieren von Web-Apps](web-sites-configure.md).

### FTPS

Azure App Service bietet sicheren FTP-Zugriff auf das Dateisystem für Ihre App durch **FTPS**. Dadurch erhalten Sie sicheren Zugriff auf den Anwendungscode der Web-App sowie auf die Diagnoseprotokolle. Es wird empfohlen, immer FTPS anstelle von FTP zu verwenden.

Den FTPS-Link für Ihre App finden Sie, indem Sie folgende Schritte durchführen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Wählen Sie „**Alle durchsuchen**“ aus.
3. Wählen Sie auf dem Blatt „**Durchsuchen**“ die Option „**App Services**“ aus.
4. Wählen Sie auf dem Blatt „**App Services**“ die gewünschte App aus.
5. Wählen Sie auf dem Blatt der App „**Alle Einstellungen**“ aus.
6. Wählen Sie auf dem Blatt **Einstellungen** die Option **Eigenschaften** aus.
7. Die FTP- und FTPS-Links werden auf dem Blatt **Einstellungen** angezeigt. 

Weitere Informationen zu FTPS finden Sie unter [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## Nächste Schritte

Um weitere Informationen zur Sicherheit der Azure-Plattform oder Informationen zur Meldung eines **sicherheitsrelevanten Ereignisses oder Missbrauchs** zu erhalten, bzw. um Microsoft zu informieren, dass Sie einen **Penetrationstest** Ihrer Seite durchführen, lesen Sie den Abschnitt über Sicherheit im [Microsoft Azure-Vertrauunsstellungscenter](https://azure.microsoft.com/support/trust-center/security/).

Weitere Informationen zu den Dateien **web.config** oder **applicationhost.config** in App Service-Apps finden Sie unter [Freigegebene Konfigurationsoptionen in Azure App Service-Web-Apps](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Weitere Informationen zum Protokollieren von Informationen für App Service-Apps, die sich bei der Erkennung von Angriffen als nützlich erweisen können, finden Sie unter [Aktivieren der Diagnoseprotokollierung](web-sites-enable-diagnostic-log.md).

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen

* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

<!---HONumber=AcomDC_0211_2016-->