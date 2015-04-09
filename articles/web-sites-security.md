<properties 
	pageTitle="Sichern einer Web-App in Azure App Service" 
	description="Erfahren Sie mehr über das Sichern einer Azure-Web-App." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>


#Sichern einer Web-App in Azure App Service

Eine der Herausforderungen bei der Entwicklung einer Web-App liegt darin, einen sicheren Dienst für Ihre Kunden bereitzustellen. In diesem Artikel erfahren Sie mehr über die Funktionen von [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), die Ihre Web-App sichern können.

> [AZURE.NOTE] Eine umfassende Erläuterung der Sicherheitsaspekte webbasierter Anwendungen wird über den Rahmen dieses Dokuments sprengen. Einen Ansatzpunkt für weitere Anleitungen zur Sicherung von Webanwendungen finden Sie unter [Open Web Application Security Project (OWASP))]( https://www.owasp.org/index.php/Main_Page),, insbesondere unter dem [Top 10-Projekt](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) (in englischer Sprache), das die derzeit 10 kritischsten Sicherheitsschwachstellen auflistet, die von OWASP-Mitgliedern bestimmt wurden.

##<a name="https"></a> Sichere Kommunikation

Wenn Sie den Domänennamen ***.azurewebsites.net** nutzen, der für Ihre Web-App erstellt wurde, können Sie HTTPS sofort verwenden, da ein SSL-Zertifikat für alle Domänennamen vom Typ ***.azurewebsites.net** bereitgestellt wird. Wenn Ihre Website einen [benutzerdefinierten Domänennamen](web-sites-custom-domain-name.md),  verwendet, können Sie ein SSL-Zertifikat hochladen, um [HTTPS](web-sites-configure-ssl-certificate.md für die benutzerdefinierte Domäne zu aktivieren.

##<a name="develop"></a> Sichere Entwicklung

### Veröffentlichung von Profilen und Einstellungen

Bei der Entwicklung von Anwendungen, der Durchführung von Verwaltungsaufgaben oder bei der Automatisierung von Aufgaben, die **Visual Studio**, **Web Matrix**, **Azure PowerShell** oder die **plattformübergreifende Azure-Befehlszeilenschnittstelle** verwenden, können Sie entweder eine Datei mit  *publish settings* oder mit  *publishing profile* nutzen. Beides authentisiert hat Sie für Azure und sollte geschützt werden, um unautorisierten Zugriff zu verhindern.

* Eine Datei mit **Veröffentlichungseinstellungen** enthält Folgendes:

	* ID Ihres Azure-Abonnements

	* Ein Verwaltungszertifikat, das Ihnen das Ausführen von Verwaltungsaufgaben für Ihr Abonnement ermöglicht *without having to provide an account name or password*.

* Eine Datei mit **Veröffentlichungsprofil** enthält Folgendes:

	* Informationen zum Veröffentlichen in Ihrer Web-App

Wenn Sie ein Hilfsprogramm nutzen, das Veröffentlichungseinstellungen oder ein Veröffentlichungsprofil verwendet, importieren Sie die Datei, die die Veröffentlichungseinstellungen oder das Profil enthält, in das Dienstprogramm und **löschen** Sie anschließend die Datei. Wenn Sie die Datei beibehalten müssen, beispielsweise um sie mit anderen zu teilen, die an dem Projekt mitarbeiten, speichern Sie sie an einem sicheren Ort, z.B. in einem **verschlüsselten** Verzeichnis mit begrenzten Berechtigungen.

Zusätzlich sollten Sie sicherstellen, dass die importierten Berechtigungsnachweise sicher sind. Beispielsweise speichern **Azure PowerShell** und die **plattformübergreifende Azure-Befehlszeilenschnittstelle** importierte Daten in Ihrem **Homeverzeichnis** (*~* auf Linux- oder OS X-Systemen und */Benutzer/IhrBenutzername* auf Windows-Systemen.) Als zusätzliche Sicherheitsmaßnahme können Sie diese Verzeichnisse mit Verschlüsselungstools **verschlüsseln**, die für Ihr Betriebssystem erhältlich sind.

### Konfigurationseinstellungen und Verbindungszeichenfolgen
Es ist üblich, Verbindungszeichenfolgen, Anmeldeinformationen zur Authentifizierung und andere sensible Daten in Konfigurationsdateien zu speichern. Leider können sich diese Dateien ungeschützt auf ihrer Website befinden oder in ein öffentliches Repository verschoben werden, wodurch diese Daten gefährdet werden können.

Azure App Service ermöglicht es Ihnen, Konfigurationsdaten als Teil der Laufzeitumgebung von Web-Apps als **Anwendungseinstellungen** und **Verbindungszeichenfolgen** zu speichern. Die Werte werden für Ihre Anwendung zur Laufzeit über  *environment variables* für die meisten Programmiersprachen freigegeben. Bei .NET-Anwendungen werden diese Werte in Ihre .NET-Konfiguration zur Laufzeit eingegeben.

**Anwendungseinstellungen** und **Verbindungszeichenfolgen** können mit dem [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) oder mit Hilfsprogrammen wie PowerShell oder der plattformübergreifenden Azure-Befehlszeilenschnittstelle konfiguriert werden.

Weitere Informationen zu Anwendungseinstellungen und Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Web-Apps](web-sites-configure.md)..

### FTPS

Azure bietet sicheren FTP-Zugriff auf das Dateisystem für Ihre Web-App mit **FTPS**. Dadurch erhalten Sie sicheren Zugriff auf den Anwendungscode der Web-App sowie auf die Diagnoseprotokolle. Den FTPS-Link für Ihre Web-App finden Sie auf der Seite **Dashboard** im [Azure-Verwaltungsportal](https://manage.windowsazure.com).

Weitere Informationen zu FTPS finden Sie unter [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Nächste Schritte

Um weitere Informationen zur Sicherheit der Azure-Plattform oder Informationen zur Meldung eines **sicherheitsrelevanten Ereignisses oder Missbrauchs** zu erhalten, bzw. um Microsoft zu informieren, dass Sie einen **Penetrationstest** Ihrer Seite durchführen, lesen Sie den Abschnitt über [Sicherheit im Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/security/).

Weitere Informationen zu den Dateien **web.config** oder **applicationhost.config** in Azure-Web-Apps finden Sie unter [Configuration options unlocked in Azure App Service web apps (in englischer Sprache)](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Weitere Informationen zum Protokollieren von Informationen in Azure-Web-Apps, die sich bei der Erkennung von Angriffen als nützlich erweisen können, finden Sie unter [Aktivieren der Diagnoseprotokollierung](web-sites-enable-diagnostic-log.md)..

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service and existing Azure services (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)

* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Reference for navigating the preview portal (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->