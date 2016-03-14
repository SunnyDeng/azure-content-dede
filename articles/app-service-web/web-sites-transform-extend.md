<properties
	pageTitle="Erweiterte Konfiguration und Erweiterungen von Azure App Service-Web-Apps"
	description="Verwenden Sie XML Document Transformation-Deklarationen (XDT) zum Transformieren der Datei ";ApplicationHost.config"; in Ihrer Azure App Service-Web-App und zum Hinzufügen privater Erweiterungen zum Aktivieren von benutzerdefinierten Verwaltungsaktionen."
	authors="cephalin"
	writer="cephalin"
	editor="mollybos"
	manager="wpickett"
	services="app-service"
	documentationCenter=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016"
	ms.author="cephalin"/>

# Erweiterte Konfiguration und Erweiterungen von Azure App Service-Web-Apps

Mithilfe von [XML Document Transformation-Deklarationen](http://msdn.microsoft.com/library/dd465326.aspx) (XDT) können Sie die Datei [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) in Ihrer Web-App in Azure App Service transformieren. Sie können XDT-Deklarationen außerdem dazu verwenden, private Erweiterungen hinzuzufügen, um benutzerdefinierte Web-App-Verwaltungsaktionen zu ermöglichen. Dieser Artikel enthält ein Beispiel für eine PHP-Manager-Web-App-Erweiterung, mit der PHP-Einstellungen über eine Weboberfläche verwaltet werden können.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>Erweiterte Konfiguration durch "ApplicationHost.config"
Die App Service-Plattform ermöglicht eine flexible Konfiguration von Web-Apps. Obwohl die standardmäßige IIS-Konfigurationsdatei "ApplicationHost.config" zur direkten Bearbeitung in App Service nicht zur Verfügung steht, unterstützt die Plattform ein deklaratives, auf XDT (XML Document Transformation) beruhendes Transformationsmodell für die Datei "ApplicationHost.config".

Um diese Transformationsfunktion zu nutzen, erstellen Sie die Datei ApplicationHost.xdt mit XDT-Inhalt und platzieren sie im Website-Stammverzeichnis (d:\\home\\site) in der [Kudu-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console). Möglicherweise müssen Sie die Web-App neu starten, damit die Änderungen wirksam werden.

Das folgende Beispiel für "applicationHost.xdt" zeigt, wie einer Web-App, die PHP 5.4 verwendet, eine neue benutzerdefinierte Umgebungsvariable hinzugefügt wird.

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.webServer>
    			<fastCgi>
      				<application>
         				<environmentVariables>
            					<environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
         				</environmentVariables>
      				</application>
    			</fastCgi>
  		</system.webServer>
	</configuration>


Im FTP-Stammverzeichnis finden Sie unter LogFiles\\Transform eine Protokolldatei mit Transformationsstatus und -details.

Weitere Beispiele finden Sie unter [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples).

**Hinweis**<br />Elemente in der Liste der Module unter `system.webServer` können nicht entfernt oder umgeordnet werden. Die Liste lässt sich jedoch erweitern.


##<a id="extend"></a> Erweitern Ihrer Web-App

###<a id="overview"></a> Überblick über private Web-App-Erweiterungen

App Service unterstützt Web-App-Erweiterungen als Erweiterungspunkt für Verwaltungsaktionen. Einige App Service-Plattformfunktionen sind tatsächlich als vorinstallierte Website-Erweiterungen implementiert. Während die vorinstallierten Plattformerweiterungen nicht geändert werden können, ist es möglich, private Erweiterungen für Ihre eigenen Web-Apps zu erstellen und zu konfigurieren. Diese Funktion stützt sich ebenfalls auf XDT-Deklarationen. Die wichtigsten Schritte zum Erstellen einer privaten Web-App-Erweiterung sind:

1. **Inhalt** der Web-App-Erweiterung: Erstellen einer von App Service unterstützten Webanwendung
2. **Deklaration** der Web-App-Erweiterung: Erstellen der Datei "ApplicationHost.xdt"
3. **Bereitstellung** der Web-App-Erweiterung: Ablegen von Inhalten im Ordner "SiteExtensions" unter `root`

Interne Links für die Web-App müssen auf einen Pfad relativ zum Anwendungspfad zeigen, der in der Datei "ApplicationHost.xdt" angegeben ist. Änderungen an der Datei "ApplicationHost.xdt" erfordern einen Neustart der Web-App.

**Hinweis**: Weitere Informationen zu diesen zentralen Elementen finden Sie unter [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Dort finden Sie ein detailliertes Beispiel, das die Schritte zum Erstellen und Aktivieren einer privaten Web-App-Erweiterung darstellt. Der Quellcode für das folgende PHP-Manager-Beispiel kann von [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager) heruntergeladen werden.

###<a id="SiteSample"></a> Beispiel für eine Web-App-Erweiterung: PHP Manager

PHP-Manager ist eine Web-App-Erweiterung, die es Web-App-Administratoren problemlos ermöglicht, PHP-Einstellungen in einer Weboberfläche anzuzeigen und zu konfigurieren, anstatt die PHP-INI-Dateien direkt bearbeiten zu müssen. Übliche Konfigurationsdateien für PHP sind die Datei "php.ini" im Ordner "Programme" und die Datei ".user.ini" im Stammordner der Web-App. Da die Datei "php.ini" auf der App Service-Plattform nicht direkt bearbeitet werden kann, verwendet die PHP-Manager-Erweiterung die Datei ".user.ini", um geänderte Einstellungen anzuwenden.

####<a id="PHPwebapp"></a> Die PHP-Manager-Webanwendung

Die folgende Seite ist die Startseite der PHP-Manager-Bereitstellung:

![Website transformieren - PHP-Benutzeroberfläche][TransformSitePHPUI]

Wie Sie sehen, entspricht eine Web-App-Erweiterung einer regulären Webanwendung, wobei jedoch zusätzlich die Datei "ApplicationHost.xdt" im Stammordner der Web-App abgelegt wird (ausführlichere Informationen über die Datei "ApplicationHost.xdt" finden Sie im nächsten Abschnitt dieses Artikels).

Die PHP-Manager-Erweiterung wurde mit der ASP.NET MVC 4-Webanwendungsvorlage von Visual Studio erstellt. Die folgende Ansicht im Projektmappen-Explorer zeigt die Struktur der PHP-Manager-Erweiterung.

![Website transformieren - Projektmappen-Explorer][TransformSiteSolEx]

Die einzige spezielle Logik, die für die Datei-E/A benötigt wird, ist die Angabe des Speicherorts für das Verzeichnis "wwwroot" der Web-App. Wie das folgende Codebeispiel zeigt, gibt die Umgebungsvariable "HOME" den Stammpfad der Web-App an, und der Pfad für "wwwroot" kann durch Anhängen von "site\\wwwroot" gebildet werden:

	/// <summary>
	/// Gives the location of the .user.ini file, even if one doesn't exist yet
	/// </summary>
	private static string GetUserSettingsFilePath()
	{
    		var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
    		if (rootPath == null)
    		{
        		rootPath = System.IO.Path.GetTempPath(); // For testing purposes
    		};
    		var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
    		return userSettingsFile;
	}


Wenn der Verzeichnispfad festgelegt ist, können Sie reguläre Datei-E/A-Operationen verwenden, um Dateien zu lesen und zu schreiben.

Ein Punkt, der bei Web-App-Erweiterungen Aufmerksamkeit erfordert, betrifft die Behandlung interner Links. Wenn Ihre HTML-Dateien Links mit absoluten Pfaden zu internen Links in Ihrer Web-App enthalten, müssen Sie darauf achten, dass diesen Links der Name der Erweiterung als Stammverzeichnis vorangestellt wird. Dies ist notwendig, da das Stammverzeichnis für Ihre Erweiterung jetzt "/`[your-extension-name]`/" anstatt einfach "/" lautet, sodass interne Links entsprechend aktualisiert werden müssen. Angenommen, Ihr Code enthält einen Link zum folgenden Element:

`"<a href="/Home/Settings">PHP Settings</a>"`

Wenn der Link zu einer Web-App-Erweiterung gehört, muss der Link folgende Form haben:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Sie können diese Anforderung umgehen, indem Sie in Ihrer Webanwendung entweder nur relative Pfade oder im Falle von ASP.NET-Webanwendungen die -Methode `@Html.ActionLink` verwenden, die die entsprechenden Links automatisch erstellt.

####<a id="XDT"></a> Die Datei "applicationHost.xdt"

Der Code für Ihre Web-App-Erweiterung wird unter "%HOME%\\SiteExtensions[Name-der-Erweiterung]" abgelegt. Dies wird als Erweiterungsstamm bezeichnet.

Um die Web-App-Erweiterung in der Datei "applicationHost.config" zu registrieren, müssen Sie im Erweiterungsstamm eine Datei namens "ApplicationHost.xdt" ablegen. Die Datei "ApplicationHost.xdt" muss folgenden Inhalt haben:

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.applicationHost>
    			<sites>
      				<site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
						<!-- NOTE: Add your extension name in the application paths below -->
        				<application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
        				<application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
          					<virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
        				</application>
      				</site>
    			</sites>
  		</system.applicationHost>
	</configuration>

Der Name, den Sie als Namen der Erweiterung auswählen, muss mit dem Namen des Stammordners der Erweiterung identisch sein.

Dadurch wird der Websiteliste `system.applicationHost` unter der SCM-Website ein neuer Anwendungspfad hinzugefügt. Die SCM-Website ist ein Websiteverwaltungsendpunkt, für den bestimmte Zugriffsanmeldeinformationen gelten. Die URL lautet `https://[your-site-name].scm.azurewebsites.net`.

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\SiteExtensions[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

###<a id="deploy"></a> Bereitstellung von Web-App-Erweiterungen

Zur Installation der Web-App-Erweiterung können Sie mit FTP alle Dateien der Webanwendung in den Ordner `\SiteExtensions[your-extension-name]` der Web-App kopieren, auf der Sie die Erweiterung installieren möchten. Kopieren Sie in diesen Ordner auch die Datei ApplicationHost.xdt. Starten Sie Ihre Web-App neu, um die Erweiterung zu aktivieren.

Die Web-App-Erweiterung sollte unter folgender Adresse angezeigt werden:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Sie sehen, dass die URL der URL für Ihre Web-App entspricht, bis auf die Tatsache, dass HTTPS verwendet wird und die Adresse ".scm" enthält.

Es ist möglich, alle privaten (nicht vorinstallierten) Erweiterungen für Ihre Web-App bei Entwicklungs- und Untersuchungsvorgängen zu deaktivieren, indem Sie eine App-Einstellung mit dem Schlüssel `WEBSITE_PRIVATE_EXTENSIONS` und dem Wert `0` hinzufügen

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 

<!---HONumber=AcomDC_0302_2016-->