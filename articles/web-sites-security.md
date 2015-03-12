<properties 
	pageTitle="Sichern einer Azure-Website" 
	description="Erfahren Sie mehr über das Sichern einer Azure-Website." 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


#Sicherung einer Webanwendung in einer Azure-Website

Eine der Herausforderungen bei der Entwicklung einer Webanwendung liegt darin, einen sicheren Dienst für Ihre Kunden bereitzustellen. In diesem Artikel erfahren Sie mehr über die Funktionen von Azure-Websites, die Ihre Webanwendung sichern können.

> [AZURE.NOTE] Eine umfassende Erläuterung der Sicherheitsaspekte webbasierter Anwendungen wird über den Rahmen dieses Dokuments sprengen. Einen Ansatzpunkt für weitere Anleitungen zur Sicherung von Webanwendungen finden Sie unter [Open Web Application Security Project (OWASP)]( https://www.owasp.org/index.php/Main_Page), insbesondere unter dem [Top 10-Projekt](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), das die derzeit 10 kritischsten Sicherheitsschwachstellen auflistet, die von OWASP-Mitgliedern bestimmt wurden.

###Inhaltsverzeichnis

* [Sichere Kommunikation](#https)
* [Sichere Entwicklung](#develop)
* [Nächste Schritte](#next)
 
##<a name="https"></a>Sichere Kommunikation

Wenn Sie den Domänennamen ***.azurewebsites.net** nutzen, der für Ihre Website erstellt wurde, können Sie HTTPS sofort verwenden, da ein SSL-Zertifikat für alle Domänennamen vom Typ ***.azurewebsites.net** bereitgestellt wird. Wenn Ihre Seite einen [benutzerdefinierten Domänennamen](http://azure.microsoft.com/documentation/articles/web-sites-custom-domain-name/) verwendet, können Sie ein SSL-Zertifikat hochladen, um HTTPS für die benutzerdefinierte Domäne zu aktivieren.

##<a name="develop"></a>Sichere Entwicklung 

###Veröffentlichung von Profilen und Einstellungen

Bei der Entwicklung von Anwendungen, der Durchführung von Verwaltungsaufgaben oder bei der Automatisierung von Aufgaben, die **Visual Studio**, **Web Matrix**, **Azure PowerShell** oder die **plattformübergreifende Azure-Befehlszeilenschnittstelle** verwenden, können Sie entweder eine Datei mit  *publish settings* oder mit  *publishing profile* nutzen. Beides authentisiert hat Sie für Azure und sollte geschützt werden, um unautorisierten Zugriff zu verhindern.

* Eine Datei mit **Veröffentlichungseinstellungen** enthält Folgendes:

	* ID Ihres Azure-Abonnements

	* Ein Verwaltungszertifikat, das Ihnen das Ausführen von Verwaltungsaufgaben für Ihr Abonnement ermöglicht *without having to provide an account name or password*.

* Eine Datei mit **Veröffentlichungsprofil** enthält Folgendes:

	* Informationen, die auf Ihrer Azure-Website veröffentlicht werden können

Wenn Sie ein Hilfsprogramm nutzen, das Veröffentlichungseinstellungen oder ein Veröffentlichungsprofil verwendet, importieren Sie die Datei, die die Veröffentlichungseinstellungen oder das Profil enthält, in das Dienstprogramm und **löschen** Sie anschließend die Datei. Wenn Sie die Datei beibehalten müssen, beispielsweise um sie mit anderen zu teilen, die an dem Projekt mitarbeiten, speichern Sie sie an einem sicheren Ort, z.B. in einem **verschlüsselten** Verzeichnis mit begrenzten Berechtigungen.

Zusätzlich sollten Sie sicherstellen, dass die importierten Berechtigungsnachweise sicher sind. Beispielsweise speichern **Azure PowerShell** und die **plattformübergreifende Azure-Befehlszeilenschnittstelle** importierte Daten in Ihrem **Homeverzeichnis** (*~* auf Linux- oder OS X-Systemen und */Benutzer/IhrBenutzername* auf Windows-Systemen.) Als zusätzliche Sicherheitsmaßnahme können Sie diese Verzeichnisse mit Verschlüsselungstools **verschlüsseln**, die für Ihr Betriebssystem erhältlich sind.

###Konfigurationseinstellungen und Verbindungszeichenfolgen
Es ist üblich, Verbindungszeichenfolgen, Anmeldeinformationen zur Authentifizierung und andere sensible Daten in Konfigurationsdateien zu speichern. Leider können sich diese Dateien ungeschützt auf ihrer Website befinden oder in ein öffentliches Repository verschoben werden, wodurch diese Daten gefährdet werden können.

Azure-Websites ermöglicht es Ihnen, Konfigurationsdaten als Teil der Laufzeitumgebung von Websites als **Anwendungseinstellungen** und **Verbindungszeichenfolgen** zu speichern. Die Werte werden für Ihre Anwendung zur Laufzeit über  *environment variables* für die meisten Programmiersprachen freigegeben. Bei .NET-Anwendungen werden diese Werte in Ihre .NET-Konfiguration zur Laufzeit eingegeben.

**Anwendungseinstellungen** und **Verbindungszeichenfolgen** können mit dem Azure-Verwaltungsportal oder mit Hilfsprogrammen wie PowerShell oder der plattformübergreifenden Azure-Befehlszeilenschnittstelle konfiguriert werden.

Weitere Informationen zu Anwendungseinstellungen und Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Websites](/de-de/documentation/articles/web-sites-configure/)

###FTPS

Azure bietet sicheren FTP-Zugriff auf das Dateisystem für Ihre Website mit **FTPS**. Dadurch erhalten Sie sicheren Zugriff auf den Anwendungscode der Website sowie auf die Diagnoseprotokolle. Den FTPS-Link für Ihre Website finden Sie auf der Seite **Dashboard** im [Azure-Verwaltungsportal](https://manage.windowsazure.com).

Weitere Informationen zu FTPS finden Sie unter [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

##Nächste Schritte

Um weitere Informationen zur Sicherheit der Azure-Plattform oder Informationen zur Meldung eines **sicherheitsrelevanten Ereignisses oder Missbrauchs** zu erhalten, bzw. um Microsoft zu informieren, dass Sie einen **Penetrationstest** Ihrer Seite durchführen, lesen Sie den Abschnitt über Sicherheit im [Microsoft Azure Trust Center](/de-de/support/trust-center/security/).

Weitere Informationen zu den Dateien **web.config** oder **applicationhost.config** in Azure-Websites finden Sie unter [Freigegebene Konfigurationsoptionen in Azure-Websites](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Weitere Informationen zum Protokollieren von Informationen auf Azure-Websites, die sich bei der Erkennung von Angriffen als nützlich erweisen können, finden Sie unter [Aktivieren von Diagnoseprotokollen](/de-de/documentation/articles/web-sites-enable-diagnostic-log/).




<!--HONumber=42-->
