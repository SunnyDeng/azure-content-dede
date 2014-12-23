<properties title="Securing an Azure Website" pageTitle="Sichern einer Azure-Website" description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


#Sicherung einer Webanwendung in einer Azure-Website

Eine der Herausforderungen bei der Entwicklung einer Webanwendung liegt darin, einen sicheren Dienst für Ihre Kunden bereitzustellen. In diesem Artikel erfahren Sie mehr über die Funktionen von Azure-Websites, die Ihre Webanwendung sichern können.

> [WACOM.NOTE] Eine vollständige Erörterung von Sicherheitsfragen für webbasierte Anwendungen liegt außerhalb des Reichweite dieses Dokuments. Einen Ansatzpunkt für weitere Anleitungen zur Sicherung von Webanwendungen finden Sie unter [Open Web Application Security Project (OWASP)], insbesondere unter dem [Top 10-Projekt](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), das die derzeit 10 kritischsten Sicherheitsschwachstellen auflistet, die von OWASP-Mitgliedern angegeben wurden.

###Inhaltsverzeichnis

* [Sichern der Kommunikation](#https)
* [Sichern der Entwicklung](#develop)
* [Nächste Schritte](#next)
 
##<a name="https"></a>Sichern der Kommunikation

Wenn Sie den Domänennamen ***.azurewebsites.net**, der für Ihre Website erstellt wurde, nutzen, können Sie HTTPS sofort verwenden, da ein SSL-Zertifikat für alle Domänennamen ***.azurewebsites.net** bereitgestellt wird. Wenn Ihre Seite einen [kundenspezifischen Domänennamen](http://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/) verwendet, können Sie ein SSL-Zertifikat hochladen, um HTTPS für die kundenspezifische Domäne zu aktivieren.

##<a name="develop"></a>Sichern der Entwicklung 

###Veröffentlichung von Profilen und Einstellungen

Bei der Entwicklung von Anwendungen, der Durchführung von Verwaltungsaufgaben oder bei der Automatisierung von Aufgaben, die **Visual Studio**, **Web Matrix**, **Azure PowerShell** oder die **plattformübergreifende Azure-Befehlszeilenschnittstelle**verwenden, können Sie entweder eine Datei mit "Veröffentlichungseinstellungen" oder mit "Veröffentlichungsprofil" nutzen. Beides authentifiziert Sie bei Azure und sollte geschützt werden, um unbefugten Zugriff zu verhindern.

* Eine Datei mit **Veröffentlichungseinstellungen** enthält:

	* ID Ihres Azure-Abonnements

	* Ein Verwaltungszertifikat, das es Ihnen ermöglicht, Verwaltungsaufgaben für Ihr Abonnement durchzuführen, ohne einen Kontonamen oder ein Kennwort angeben zu müssen.

* Eine Datei mit **Veröffentlichungsprofil** enthält:

	* Informationen, die auf Ihrer Azure-Website veröffentlicht werden können

Wenn Sie ein Dienstprogramm nutzen, das Veröffentlichungseinstellungen oder ein Veröffentlichungsprofil verwendet, importieren Sie die Datei, die die Veröffentlichungseinstellungen oder das Profil enthält, in das Dienstprogramm und **löschen** Sie anschließend die Datei. Wenn Sie die Datei behalten müssen, beispielsweise um sie mit anderen zu teilen, die an dem Projekt mitarbeiten, speichern Sie sie an einem sicheren Ort, z.B. in einem **verschlüsselten** Verzeichnis mit begrenzten Berechtigungen.

Zusätzlich sollten Sie sicherstellen, dass die importierten Berechtigungsnachweise sicher sind. Beispielsweise speichern **Azure PowerShell** und die **plattformübergreifende Azure-Befehlszeilenschnittstelle** importierte Daten in Ihrem **Homeverzeichnis** (*~* auf Linux oder OS X-Systemen und */Benutzer/IhrBenutzername* auf Windows-Systemen.) Als zusätzliche Sicherheit können Sie diese Verzeichnisse mit Verschlüsselungstools **verschlüsseln**, die für Ihr Betriebssystem erhältlich sind.

###Konfigurationseinstellungen und Verbindungszeichenfolgen
Es ist üblich, Verbindungszeichenfolgen, Berechtigungsnachweise zur Authentifizierung und andere sensible Daten in Konfigurationsdateien zu speichern. Leider können sich diese Dateien ungeschützt auf ihrer Website befinden oder in ein öffentliches Repository verschoben werden, wodurch diese Daten gefährdet werden können.

Azure-Websites ermöglicht es Ihnen, Konfigurationsdaten als Teil der Website-Laufzeitumgebung als **App-Einstellungen** und **Verbindungszeichenfolgen** zu speichern. Die Werte werden für Ihre Anwendung zur Laufzeit über Umgebungsvariablen für die meisten Programmiersprachen freigegeben. Bei .NET-Anwendungen werden diese Werte in Ihre .NET-Konfiguration zur Laufzeit eingegeben.

**Anwendungseinstellungen** und **Verbindungszeichenfolgen** können mit dem Azure-Verwaltungsportal oder mit Dienstprogrammen, wie PowerShell oder der plattformübergreifenden Azure-Befehlszeilenschnittstelle, konfiguriert werden.

Weitere Informationen zu Anwendungseinstellungen und Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Websites](/en-us/documentation/articles/web-sites-configure/).

###FTPS

Azure bietet sicheren FTP-Zugriff auf das Dateisystem für Ihre Website mit **FTPS**. Dadurch erhalten Sie sicheren Zugriff auf den Anwendungscode der Website sowie auf die Diagnoseprotokolle. Den FTPS-Link für Ihre Website finden Sie auf der Seite **Dashboard** im [Azure-Verwaltungsportal](https://manage.windowsazure.com).

Weitere Informationen zu FTPS finden Sie unter [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

##Nächste Schritte

Um weitere Informationen zur Sicherheit der Azure-Plattform oder Informationen zur Meldung eines **sicherheitsrelevanten Ereignisses oder Missbrauchs** zu erhalten, bzw. um Microsoft zu informieren, dass Sie einen **Penetrationstest** Ihrer Seite durchführen, lesen Sie den Abschnitt über Sicherheit im [Microsoft Azure-Vertrauensstellungscenter](/en-us/support/trust-center/security/).

Weitere Informationen zu **web.config**- oder **applicationhost.config**-Dateien in Azure-Websites finden Sie unter [Freigegebene Konfigurationsoptionen in Azure-Websites](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Weitere Informationen zum Protokollieren von Informationen auf Azure-Websites, die sich bei der Erkennung von Angriffen als nützlich erweisen können, finden Sie unter [Aktivieren von Diagnoseprotokollen](/en-us/documentation/articles/web-sites-enable-diagnostic-log/).
