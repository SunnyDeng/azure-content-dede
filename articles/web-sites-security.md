<properties title="Securing an Azure Website" pageTitle="Securing an Azure Website." description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

## Sicherung einer Webanwendung in einer Azure-Website

Eine der Herausforderungen bei der Entwicklung einer Webanwendung liegt darin, einen sicheren Dienst für Ihre Kunden bereitzustellen. In diesem Artikel erfahren Sie mehr über die Funktionen von Azure-Websites, die Ihre Webanwendung sichern können.

> [WACOM.NOTE] Eine vollständige Erörterung von Sicherheitsfragen für webbasierte Anwendungen liegt außerhalb des Reichweite dieses Dokuments. Einen Ansatzpunkt für weitere Anleitungen zur Sicherung von Webanwendungen finden Sie unter [Open Web Application Security Project (OWASP)][Open Web Application Security Project (OWASP)], insbesondere unter dem [Top 10-Projekt][Top 10-Projekt], das die derzeit 10 kritischsten Sicherheitsschwachstellen auflistet, die von OWASP-Mitgliedern angegeben wurden.

### Inhaltsverzeichnis

-   [Sichern der Kommunikation][Sichern der Kommunikation]
-   [Sichern der Entwicklung][Sichern der Entwicklung]
-   [Nächste Schritte][Nächste Schritte]

## <a name="https"></a>Sichern der Kommunikation

Wenn Sie den Domainnamen ***.azurewebsites.net**, der für Ihre Website erstellt wurde, nutzen, können Sie HTTPS sofort verwenden, da ein SSL-Zertifikat für alle Domainnamen* **.azurewebsites.net** bereitgestellt wird. Wenn Ihre Seite einen [kundenspezifischen Domainnamen][kundenspezifischen Domainnamen] verwendet, können Sie ein SSL-Zertifikat hochladen, um HTTPS für die kundenspezifische Domain zu aktivieren.

Weitere Informationen finden Sie unter [HTTPS für eine Azure-Website aktivieren][HTTPS für eine Azure-Website aktivieren].

### HTTPS erzwingen

Azure-Websites erzwingen *keine* HTTPS-Verschlüsselung. Besucher können auch über HTTP auf Ihre Seite zugreifen. Dadurch können sensible Daten gefährdet werden. Verwenden Sie das Modul **URL Rewrite**, um HTTPS zu erzwingen. Das Modul URL Rewrite ist bei Azure-Websites enthalten. Damit können Sie Regeln definieren, die auf eingehende Anfragen angewendet werden, bevor diese Anfragen an Ihre Anwendung weitergegeben werden. Es kann für Anwendungen verwendet werden, die in einer Programmiersprache geschrieben sind, die von Azure-Websites unterstützt wird.

> [WACOM.NOTE] .NET MVC-Anwendungen sollten den [RequireHttps][RequireHttps]-Filter anstelle von URL Rewrite verwenden. Weitere Informationen zur Verwendung von RequireHttps finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung für eine Azure-Website][Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung für eine Azure-Website].
>
> Informationen zur programmatischen Weiterleitung von Anfragen, die andere Programmiersprachen und Frameworks verwenden, finden Sie in der Dokumentation für diese Technologien.

Die Regeln zu URL Rewrite sind in einer **web.config**-Datei im Stammverzeichnis Ihrer Anwendung gespeichert. Das folgende Beispiel enthält eine grundlegende URL-Umschreibungsregel, die den eingehenden Datenverkehr zur Verwendung von HTTPS zwingt.

<a name="example"></a>**Beispiel URL-Umschreibung Web.Config**

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <rewrite>
          <rules>
            <rule name="Force HTTPS" enabled="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{HTTPS}" pattern="off" />
              </conditions>
              <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

Diese Regel funktioniert durch die Rückgabe eines HTTP-Statuscode von 301 (Permanent Redirect), wenn der Benutzer eine Seite mit HTTP anfragt. Der Code 301 leitet die Anfrage an die gleiche URL weiter, die der Besucher angefragt hat, aber ersetzt den HTTP-Teil der Anfrage mit HTTPS. <HTTP://contoso.com> würde beispielsweise zu <HTTPS://contoso.com> weitergeleitet werden.

> [WACOM.NOTE] Wenn Ihre Anwendung in **Node.js**, **PHP**, **Python Django** oder **Java** geschrieben ist, enthält sich wahrscheinliche keine web.config-Datei. Dennoch verwenden **Node.js**, **Python Django** und **Java** eine web.config, wenn sie auf Azure-Websites gehostet werden. Azure erstellt die Datei während der Bereitstellung automatisch. Deshalb sehen Sie sie nicht. Wenn Sie eine solche Datei als Teil Ihrer Anwendung einfügen, wird die von Azure automatisch generierte Datei überschrieben.

### .NET

Ändern Sie die web.config-Datei bei .NET-Anwendungen für Ihre Anwendung und fügen Sie den Abschnitt **\<rewrite\>** aus dem [Beispiel][Beispiel] zum Abschnitt **\<system.WebServer\>** hinzu.

Wenn Ihre web.config-Datei bereits einen Anschnitt **\<rewrite\>** enthält, fügen Sie die **\<rule\>** aus dem [Beispiel][Beispiel] als ersten Eintrag im Abschnitt **\<rules\>** hinzu.

### PHP

Speichern Sie bei PHP-Anwendungen einfach das [Beispiel][Beispiel] als web.config-Datei im Stammverzeichnis Ihrer Anwendung und stellen Sie die Anwendung auf Ihrer Azure-Website erneut bereit.

### Node.js, Python Django und Java

Für Node.js-, Python Django- und Java-Anwendungen wird automatisch eine web.config-Datei erstellt, wenn sie keine haben. Sie existiert aber nur auf dem Server, da sie während der Bereitstellung erstellt wird. Die automatisch generierte Datei enthält Einstellungen, die Azure befehlen, wie Ihre Anwendung gehostet werden soll.

Um die automatisch generierte Datei von der Website abzurufen und zu ändern, verwenden Sie die folgenden Schritte.

1.  Laden Sie die Datei über FTP herunter (siehe [Hochladen/herunterladen von Dateien über FTP und Sammeln von Diagnoseprotokollen][Hochladen/herunterladen von Dateien über FTP und Sammeln von Diagnoseprotokollen]).

2.  Fügen Sie die Datei dem Stammverzeichnis Ihrer Anwendung hinzu.

3.  Fügen Sie die Umschreibungsregeln mithilfe der folgenden Informationen hinzu.

    -   **Node.js und Python Django**

        Die web.config-Datei, die für Node.js- und Python Django-Anwendungen erstellt wurde, hat bereits einen Abschnitt **\<rewrite\>**, der **\<rule\>**-Einträge enthält, die für die ordnungsgemäße Funktion der Seite benötigt werden. Um die Seite zur Verwendung von HTTPS zu zwingen, fügen Sie die **\<rule\>** aus dem Beispiel als ersten Eintrag im Abschnitt **\<rules\>** hinzu. Damit wird HTTPS erzwungen, während die übrigen Regeln bestehen bleiben.

    -   **Java**

        Die web.config-Datei für Java-Anwendungen mit Apache Tomcat enthält keinen Abschnitt **\<rewrite\>**. Deshalb müssen Sie den Abschnitt **\<rewrite\>** aus dem Beispiel zum Abschnitt **\<system.webServer\>** hinzufügen.

4.  Stellen Sie das Projekt (einschließlich der aktualisierten web.config-Datei) erneut für Azure bereit

Sobald Sie eine web.config-Datei mit einer Umschreibungsregeln zur Erzwingung von HTTPS bereitstellen, sollte sie sofort in Kraft treten und alle Anfragen zu HTTPS umleiten.

Weitere Informationen zum IIS-URL-Rewrite-Modul finden Sie unter der Dokumentation [URL-Rewrite][URL-Rewrite].

## <a name="develop"></a>Sichern der Entwicklung

### Veröffentlichung von Profilen und Einstellungen

Bei der Entwicklung von Anwendungen, der Durchführung von Verwaltungsaufgaben oder bei der Automatisierung von Aufgaben, die **Visual Studio**, **Web Matrix**, **Azure PowerShell** oder die **plattformübergreifende Azure-Befehlszeilenschnittstelle** verwenden, können Sie entweder eine Datei mit *Veröffentlichungseinstellungen* oder mit *Veröffentlichungsprofil* nutzen. Beides authentisiert hat Sie für Azure und sollte geschützt werden, um unautorisierten Zugriff zu verhindern.

-   Eine Datei mit **Veröffentlichungseinstellungen** enthält

    -   ID Ihres Azure-Abonnements

    -   Ein Verwaltungszertifikat, das es Ihnen ermöglicht, Verwaltungsaufgaben für Ihr Abonnement durchzuführen, *ohne einen Kontonamen oder ein Passwort angeben zu müssen*.

-   Eine Datei mit **Veröffentlichungsprofil** enthält

    -   Informationen, die auf Ihrer Azure-Website veröffentlicht werden können

Wenn Sie ein Dienstprogramm nutzen, das Veröffentlichungseinstellungen oder ein Veröffentlichungsprofil verwendet, importieren Sie die Datei, die die Veröffentlichungseinstellungen oder das Profil enthält, in das Dienstprogramm und **löschen** Sie anschließend die Datei. Wenn Sie die Datei behalten müssen, beispielsweise um sie mit anderen zu teilen, die an dem Projekt mitarbeiten, speichern Sie sie an einem sicheren Ort, z.B. in einem **verschlüsselten** Verzeichnis mit begrenzten Berechtigungen.

Zusätzlich sollten Sie sicherstellen, dass die importierten Berechtigungsnachweise sicher sind. Beispielsweise speichern **Azure PowerShell** und die **plattformübergreifende Azure-Befehlszeilenschnittstelle** importierte Daten in Ihrem **Homeverzeichnis** (*~* auf Linux- oder OS X-Systemen und */Benutzer/IhrBenutzername* auf Windows-Systemen.) Als zusätzliche Sicherheit können Sie diese Verzeichnisse mit Verschlüsselungstools **verschlüsseln**, die für Ihr Betriebssystem erhältlich sind.

### Konfigurationseinstellungen und Verbindungszeichenfolgen

Es ist üblich, Verbindungszeichenfolgen, Berechtigungsnachweise zur Authentisierung und andere sensible Daten in Konfigurationsdateien zu speichern. Leider können sich diese Dateien ungeschützt auf ihrer Website befinden oder in ein öffentliches Repository verschoben werden, wodurch diese Daten gefährdet werden können.

Azure-Websites ermöglicht es Ihnen, Konfigurationsdaten als Teil der Website-Laufzeitumgebung als **Anwendungseinstellungen** und **Verbindungszeichenfolgen** zu speichern. Die Werte werden für Ihre Anwendung zur Laufzeit über *Umgebungsvariablen* für die meisten Programmiersprachen freigegeben. Bei .NET-Anwendungen werden diese Werte in Ihre .NET-Konfiguration zur Laufzeit eingegeben.

**Anwendungseinstellungen** und **Verbindungszeichenfolgen** können mit dem Azure-Verwaltungsportal oder mit Dienstprogrammen, wie PowerShell oder der plattformübergreifenden Azure-Befehlszeilenschnittstelle, konfiguriert werden.

Weitere Informationen zu Anwendungseinstellungen und Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Websites][Konfigurieren von Websites].

### FTPS

Azure bietet sicheren FTP-Zugriff auf das Dateisystem für Ihre Website mit **FTPS**. Dadurch erhalten Sie sicheren Zugriff auf den Anwendungscode der Website sowie auf die Diagnoseprotokolle. Den FTPS-Link für Ihre Website finden Sie auf der Seite **Dashboard** im [Azure-Verwaltungsportal][Azure-Verwaltungsportal].

Weitere Informationen zu FTPS finden Sie unter [File Transfer Protocol][File Transfer Protocol].

## Nächste Schritte

Um weitere Informationen zur Sicherheit der Azure-Plattform oder Informationen zur Meldung eines **sicherheitsrelevanten Ereignisses oder Missbrauchs** zu erhalten, bzw. um Microsoft zu informieren, dass Sie einen **Penetrationstest** Ihrer Seite durchführen, lesen Sie den Abschnitt über Sicherheit im [Microsoft Azure-Vertrauunsstellungscenter][Microsoft Azure-Vertrauunsstellungscenter].

Weitere Informationen zu **web.config**- oder **applicationhost.config**-Dateien in Azure Websites finden Sie unter [Freigegebene Konfigurationsoptionen in Azure-Websites][Freigegebene Konfigurationsoptionen in Azure-Websites].

Weitere Informationen zum Protokollieren von Informationen auf Azure-Websites, die sich bei der Erkennung von Angriffen als nützlich erweisen können, finden Sie unter [Aktivieren von Diagnoseprotokollen][Aktivieren von Diagnoseprotokollen].

  [Open Web Application Security Project (OWASP)]: https://www.owasp.org/index.php/Main_Page
  [Top 10-Projekt]: https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project
  [Sichern der Kommunikation]: #https
  [Sichern der Entwicklung]: #develop
  [Nächste Schritte]: #next
  [kundenspezifischen Domainnamen]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-custom-domain-name/
  [HTTPS für eine Azure-Website aktivieren]: /de-de/documentation/articles/web-sites-configure-ssl-certificate/
  [RequireHttps]: http://msdn.microsoft.com/de-de/library/system.web.mvc.requirehttpsattribute.aspx
  [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung für eine Azure-Website]: /de-de/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [Beispiel]: #example
  [Hochladen/herunterladen von Dateien über FTP und Sammeln von Diagnoseprotokollen]: http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx
  [URL-Rewrite]: http://www.iis.net/downloads/microsoft/url-rewrite
  [Konfigurieren von Websites]: /de-de/documentation/articles/web-sites-configure/
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [File Transfer Protocol]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [Microsoft Azure-Vertrauunsstellungscenter]: /de-de/support/trust-center/security/
  [Freigegebene Konfigurationsoptionen in Azure-Websites]: http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/
  [Aktivieren von Diagnoseprotokollen]: /de-de/documentation/articles/web-sites-enable-diagnostic-log/
