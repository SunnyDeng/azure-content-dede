<properties 
	pageTitle="Transformieren und Erweitern Ihrer Website" 
	description="TBD" 
	authors="cephalin" 
	writer="cephalin" 
	editor="mollybos" 
	manager="wpickett" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# Transformieren und Erweitern Ihrer Website

Mithilfe von [XML Document Transformation](http://msdn.microsoft.com/de-de/library/dd465326.aspx)-Deklarationen (XDT) können Sie die Datei [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) in Ihren Azure-Websites transformieren. Sie können XDT-Deklarationen auch dazu verwenden, private Website-Erweiterungen hinzuzufügen, um benutzerdefinierte Websiteverwaltungsaktionen zu ermöglichen. Dieser Artikel umfasst ein Beispiel für eine PHP-Manager-Website-Erweiterung, mit der PHP-Einstellungen über eine Weboberfläche verwaltet werden können.


<!-- MINI TOC -->

* [Transformieren der Websitekonfiguration in der Datei "ApplicationHost.config"](#transform)
* [Erweitern der Website](#extend)
	* [Überblick über private Website-Erweiterungen](#overview)
	* [Beispiel einer Website-Erweiterung: PHP-Manager](#SiteSample)
		* [Die PHP-Manager-Webanwendung](#PHPwebapp)
		* [Die Datei "applicationHost.xdt"](#XDT)
	* [Bereitstellen der Website-Erweiterung](#deploy)

<h2><a id="transform"></a>Transformieren der Websitekonfiguration in der Datei "ApplicationHost.config"</h2>
Die Azure-Websites-Plattform ermöglicht eine flexible Konfiguration von Websites. Obwohl die standardmäßige IIS-Konfigurationsdatei "ApplicationHost.config" zur direkten Bearbeitung in Azure-Websites nicht zur Verfügung steht, unterstützt die Plattform ein deklaratives auf XDT (XML Document Transformation) beruhendes Transformationsmodell für die Datei "ApplicationHost.config".

Um diese Transformationsfunktion zu nutzen, erstellen Sie die Datei ApplicationHost.xdt mit XDT-Inhalt und platzieren sie im Website-Stammverzeichnis. Auf der Seite **Konfigurieren** im Azure-Portal legen Sie anschließend die Anwendungseinstellung  `WEBSITE_PRIVATE_EXTENSIONS` auf 1 fest (eventuell muss die Website neu gestartet werden). 

Das folgende Beispiel für applicationHost.xdt zeigt, wie einer Website, die PHP 5.4 verwendet, eine neue benutzerdefinierte Umgebungsvariable hinzugefügt wird.

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

 
Im FTP-Stammverzeichnis finden Sie unter LogFiles\Transform eine Protokolldatei mit Transformationsstatus und -details.

Weitere Beispiele finden Sie unter [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

**Hinweis:**<br />
Elemente in der Liste der Module unter  `system.webServer` können nicht entfernt oder umgeordnet werden. Die Liste lässt sich jedoch erweitern. 


<h2><a id="extend"></a>Erweitern der Website</h2>
<h3><a id="overview"></a>Überblick über private Website-Erweiterungen</h3>
Azure-Websites unterstützt Website-Erweiterungen als Erweiterungspunkt für Website-Verwaltungsaktionen. Einige Azure-Websites-Plattformfunktionen sind tatsächlich als vorinstallierte Website-Erweiterungen implementiert. Während die vorinstallierten Plattformerweiterungen nicht geändert werden können, ist es möglich, private Erweiterungen für Ihre eigenen Websites zu erstellen und zu konfigurieren. Diese Funktion stützt sich ebenfalls auf XDT-Deklarationen. Die wichtigsten Schritte zum Erstellen einer privaten Website-Erweiterung sind:

1. **Inhalt** der Website-Erweiterung: Erstellen einer von Azure-Websites unterstützten Webanwendung
2. **Deklaration** der Website-Erweiterung: Erstellen der Datei "ApplicationHost.xdt"
3. **Bereitstellung** der Website-Erweiterung: Ablegen von Inhalten im Ordner "SiteExtensions" unter  `root`
4.  **Aktivieren** der Website-Erweiterung: Festlegen der App-Einstellung  `WEBSITE_PRIVATE_EXTENSIONS` auf 1

Interne Links für die Webanwendung müssen auf einen Pfad relativ zum Anwendungspfad zeigen, der in der Datei ApplicationHost.xdt angegeben ist. Änderungen an der Datei ApplicationHost.xdt erfordern einen Neustart der Website. 

**Hinweis**: Weitere Informationen zu diesen zentralen Elementen finden Sie unter [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions). Dort finden Sie ein detailliertes Beispiel, das die Schritte zum Erstellen und Aktivieren einer privaten Website-Erweiterung darstellt. Der Quellcode für das folgende PHP-Manager-Beispiel kann von [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager) heruntergeladen werden.

<h3><a id="SiteSample"></a>Beispiel einer Website-Erweiterung: PHP-Manager</h3>

PHP-Manager ist eine Website-Erweiterung, die es Website-Administratoren ohne weiteres ermöglicht, PHP-Einstellungen in einer Weboberfläche anzuzeigen und zu konfigurieren, anstatt die PHP-INI-Dateien direkt zu bearbeiten. Übliche Konfigurationsdateien für PHP sind die Datei php.ini im Ordner Programme und die Datei .user.ini im Stammordner der Website. Da die Datei "php.ini" auf der Azure-Websites-Plattform nicht direkt bearbeitet werden kann, verwendet die PHP-Manager-Erweiterung die Datei ".user.ini", um geänderte Einstellungen anzuwenden.

<h4><a id="PHPwebapp"></a>Die PHP-Manager-Webanwendung</h4>
	
Die folgende Seite ist die Startseite der PHP-Manager-Website:

![TransformSitePHPUI][TransformSitePHPUI]

Wie Sie sehen, entspricht eine Website-Erweiterung einer regulären Webanwendung, wobei jedoch zusätzlich die Datei ApplicationHost.xdt im Stammordner der Website abgelegt wird (ausführlichere Informationen über die Datei ApplicationHost.xdt finden Sie im nächsten Abschnitt dieses Artikels).

Die PHP-Manager-Erweiterung wurde mit der ASP.NET MVC 4-Webanwendungsvorlage von Visual Studio erstellt. Die folgende Ansicht im Projektmappen-Explorer zeigt die Struktur der PHP-Manager-Website-Erweiterung.

![TransformSiteSolEx][TransformSiteSolEx]

Die einzige spezielle Logik, die für die Datei-E/A benötigt wird, ist die Angabe des Speicherorts für das Verzeichnis wwwroot der Website. Wie das folgende Codebeispiel zeigt, gibt die Umgebungsvariable "HOME" den Stammpfad der Website an, und der Pfad für wwwroot kann durch Anhängen von "site\wwwroot" gebildet werden:


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

Ein Punkt, der bei Website-Erweiterungen Aufmerksamkeit erfordert, betrifft die Behandlung interner Links.  Wenn Ihre HTML-Dateien Links mit absoluten Pfaden zu internen Links auf Ihrer Website enthalten, müssen Sie darauf achten, dass diesen Links der Name der Erweiterung als Website-Stammverzeichnis vorangestellt wird. Dies ist notwendig, da das Website-Stammverzeichnis für Ihre Erweiterung jetzt "/`[Name-der-Erweiterung]`/" anstatt einfach "/" lautet, sodass interne Links entsprechend aktualisiert werden müssen. Angenommen, Ihr Code enthält einen Link zum folgenden Element: 

`"<a href="/Home/Settings">PHP-Einstellungen</a>"`

Wenn der Link zu einer Website-Erweiterung gehört, muss der Link folgende Form haben:

`"<a href="/[your-site-name]/Home/Settings">Einstellungen</a>"` 

Sie können diese Anforderung umgehen, indem Sie in Ihrer Website entweder nur relative Pfade oder im Falle von ASP.NET-Websites die Methode "`@Html.ActionLink" verwenden, die die entsprechenden Links automatisch erstellt.

<h4><a id="XDT"></a>Die Datei "applicationHost.xdt"</h4>

Der Code für Ihre Website-Erweiterung wird unter %HOME%\SiteExtensions[Name-der-Erweiterung] abgelegt. Dies wird als Erweiterungsstamm bezeichnet.  

Um die Website-Erweiterung in der Datei applicationHost.config zu registrieren, müssen Sie im Erweiterungsstamm eine Datei namens ApplicationHost.xdt ablegen. Die Datei ApplicationHost.xdt muss folgenden Inhalt haben:

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

Dadurch wird der Websiteliste  `system.applicationHost` unter der SCM-Website ein neuer Anwendungspfad hinzugefügt. Die SCM-Website ist ein Websiteverwaltungsendpunkt, für den bestimmte Zugriffsanmeldeinformationen gelten. Sie hat die URL  `https://[your-site-name].scm.azurewebsites.net`.  

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

<h3><a id="deploy"></a>Bereitstellen der Website-Erweiterung</h3>

Zur Installation der Website-Erweiterung können Sie mit FTP alle Dateien der Webanwendung in den Ordner \SiteExtensions\[Name-der-Erweiterung] der Website kopieren, in der Sie die Erweiterung installieren möchten.  Kopieren Sie in diesen Ordner auch die Datei ApplicationHost.xdt.

Navigieren Sie im Microsoft Azure-Websites-Portal zur Registerkarte **Konfigurieren** für die Website, die Ihre Erweiterung enthält. Fügen Sie im Abschnitt **App-Einstellungen** den Schlüssel  `WEBSITE_PRIVATE_EXTENSIONS` hinzu, und weisen Sie ihm den Wert "1" zu.

![TransformSiteappSettings][TransformSiteappSettings]

Führen Sie im Azure-Portal schließlich einen Neustart Ihrer Website aus, um die Erweiterung zu aktivieren.

![TransformSiteRestart][TransformSiteRestart]

Die Website-Erweiterung sollte unter folgender Adresse angezeigt werden:


`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]` 

Sie sehen, dass die URL der URL für Ihre Website entspricht, bis auf die Tatsache, dass HTTPS verwendet wird und die Adresse ".scm" enthält. 

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
[TransformSiteappSettings]: ./media/web-sites-transform-extend/TransformSiteappSettings.png
[TransformSiteRestart]: ./media/web-sites-transform-extend/TransformSiteRestart.png







<!--HONumber=42-->
