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
	ms.date="07/03/2015"
	ms.author="cephalin"/>


#Sichern einer Web-App in Azure App Service

Eine der Herausforderungen bei der Entwicklung einer Web-App besteht darin, einen sicheren und zuverlässigen Dienst für Ihre Kunden bereitzustellen. In diesem Artikel erfahren Sie mehr über die Funktionen von [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), die Ihre Web-App sichern können.

> [AZURE.NOTE]Eine umfassende Erläuterung der Sicherheitsaspekte webbasierter Anwendungen wird über den Rahmen dieses Dokuments sprengen. Einen Ansatzpunkt für weitere Anleitungen zur Sicherung von Webanwendungen finden Sie unter [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page), insbesondere unter dem [Top 10-Projekt](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), das die derzeit 10 kritischsten Sicherheitsschwachstellen auflistet, die von OWASP-Mitgliedern angegeben wurden.

##<a name="https"></a> Sichern der Kommunikation

Wenn Sie den Domänennamen ***.azurewebsites.net** nutzen, der für Ihre Web-App erstellt wurde, können Sie HTTPS sofort verwenden, da ein SSL-Zertifikat für alle Domänennamen vom Typ ***.azurewebsites.net** bereitgestellt wird. Wenn Ihre Site einen [kundenspezifischen Domänennamen](web-sites-custom-domain-name.md) verwendet, können Sie ein SSL-Zertifikat hochladen, um für die kundenspezifische Domäne [HTTPS zu aktivieren](web-sites-configure-ssl-certificate.md).

##<a name="develop"></a> Sichern der Entwicklung

### Veröffentlichung von Profilen und Einstellungen

Bei der Entwicklung von Anwendungen, der Durchführung von Verwaltungsaufgaben oder bei der Automatisierung von Aufgaben, die **Visual Studio**, **Web Matrix**, **Azure PowerShell** oder die **Azure-Befehlszeilenschnittstelle (Azure-CLI)** verwenden, können Sie entweder eine Datei mit *Veröffentlichungseinstellungen* oder mit *Veröffentlichungsprofil* nutzen. Beides authentisiert hat Sie für Azure und sollte geschützt werden, um unautorisierten Zugriff zu verhindern.

* Eine Datei mit **Veröffentlichungseinstellungen** enthält

	* ID Ihres Azure-Abonnements

	* Ein Verwaltungszertifikat, das es Ihnen ermöglicht, Verwaltungsaufgaben für Ihr Abonnement durchzuführen, *ohne einen Kontonamen oder ein Passwort angeben zu müssen*.

* Eine Datei mit **Veröffentlichungsprofil** enthält

	* Informationen zum Veröffentlichen in Ihrer Web-App

Wenn Sie ein Dienstprogramm nutzen, das Veröffentlichungseinstellungen oder ein Veröffentlichungsprofil verwendet, importieren Sie die Datei, die die Veröffentlichungseinstellungen oder das Profil enthält, in das Dienstprogramm und **löschen** Sie anschließend die Datei. Wenn Sie die Datei behalten müssen, beispielsweise um sie mit anderen zu teilen, die an dem Projekt mitarbeiten, speichern Sie sie an einem sicheren Ort, z.B. in einem **verschlüsselten** Verzeichnis mit begrenzten Berechtigungen.

Zusätzlich sollten Sie sicherstellen, dass die importierten Berechtigungsnachweise sicher sind. Beispielsweise speichern **Azure PowerShell** und die **Azure-Befehlszeilenschnittstelle** importierte Daten in Ihrem **Basisverzeichnis** (*~* bei Linux- oder OS X-Systemen und */Benutzer/IhrBenutzername* bei Windows-Systemen.) Als zusätzliche Sicherheit können Sie diese Verzeichnisse mit Verschlüsselungstools **verschlüsseln**, die für Ihr Betriebssystem erhältlich sind.

### Konfigurationseinstellungen und Verbindungszeichenfolgen
Es ist üblich, Verbindungszeichenfolgen, Berechtigungsnachweise zur Authentisierung und andere sensible Daten in Konfigurationsdateien zu speichern. Leider können sich diese Dateien ungeschützt auf ihrer Website befinden oder in ein öffentliches Repository verschoben werden, wodurch diese Daten gefährdet werden können.

Azure App Service ermöglicht es Ihnen, Konfigurationsdaten als Teil der Laufzeitumgebung von Web-Apps als **App-Einstellungen** und **Verbindungszeichenfolgen** zu speichern. Die Werte werden für Ihre Anwendung zur Laufzeit über *Umgebungsvariablen* für die meisten Programmiersprachen freigegeben. Bei .NET-Anwendungen werden diese Werte in Ihre .NET-Konfiguration zur Laufzeit eingegeben.

**Anwendungseinstellungen** und **Verbindungszeichenfolgen** können mit dem [Azure-Vorschauportal](http://portal.azure.com) oder mit Hilfsprogrammen wie PowerShell oder der Azure-Befehlszeilenschnittstelle konfiguriert werden.

Weitere Informationen zu App-Einstellungen und Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Web-Apps](web-sites-configure.md).

### FTPS

Azure bietet sicheren FTP-Zugriff auf das Dateisystem für Ihre Web-App durch **FTPS**. Dadurch erhalten Sie sicheren Zugriff auf den Anwendungscode der Web-App sowie auf die Diagnoseprotokolle. Den FTPS-Link für Ihre Web-App finden Sie mit den folgenden Schritten:

1. Öffnen Sie das [Azure-Vorschauportal](http://portal.azure.com).
2. Wählen Sie **Alle durchsuchen** aus.
3. Wählen Sie auf dem Blatt **Durchsuchen** die Option **Web-Apps** aus.
4. Wählen Sie auf dem Blatt **Web-Apps** die gewünschte Web-App aus.
5. Wählen Sie auf dem Blatt der Web-App **Alle Einstellungen** aus.
6. Wählen Sie auf dem Blatt **Einstellungen** die Option **Eigenschaften** aus.
7. Die FTP- und FTPS-Links werden auf dem Blatt **Einstellungen** angezeigt. 

Weitere Informationen zu FTPS finden Sie unter [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Nächste Schritte

Um weitere Informationen zur Sicherheit der Azure-Plattform oder Informationen zur Meldung eines **sicherheitsrelevanten Ereignisses oder Missbrauchs** zu erhalten, bzw. um Microsoft zu informieren, dass Sie einen **Penetrationstest** Ihrer Seite durchführen, lesen Sie den Abschnitt über Sicherheit im [Microsoft Azure-Vertrauunsstellungscenter](http://azure.microsoft.com/support/trust-center/security/).

Weitere Informationen zu den Dateien **web.config** oder **applicationhost.config** in Web-Apps finden Sie unter [Freigegebene Konfigurationsoptionen in Azure App Service-Web-Apps](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Weitere Informationen zum Protokollieren von Informationen für Web-Apps, die sich bei der Erkennung von Angriffen als nützlich erweisen können, finden Sie unter [Aktivieren der Diagnoseprotokollierung](web-sites-enable-diagnostic-log.md).

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO4-->