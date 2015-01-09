<properties urlDisplayName="Configuring Python with Azure Websites" pageTitle="Konfigurieren von Python mit Azure-Websites" metaKeywords="" description="This tutorial describes options for authoring and configuring a basic Web server Gateway Interface (WSGI) compliant Python application on Azure Websites." metaCanonical="" services="web-sites" documentationCenter="Python" title="Configuring Python with Azure Websites" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="08/01/2014" ms.author="huvalo" />




# Konfigurieren von Python mit Azure-Websites #

Dieses Lernprogramm beschreibt die Optionen für das Erstellen und Konfigurieren einer grundlegenden Web Server Gateway Interface (WSGI)-kompatiblen Python-Anwendung auf Azure-Websites. Die ersten Schritte mit Azure-Websites sind leicht, und Ihre Python-Anwendung kann skaliert und auf andere Azure-Dienste ausgedehnt werden. Die Azure-Websites-Plattform umfasst Python (wahlweise 2.7.3 oder 3.4.0) und den generischen FastCGI-Handler für Python "wfastcgi.py". Sie müssen lediglich die Website für die Verwendung des Python-Handlers konfigurieren. 

> [WACOM.NOTE] Sie können die Python-Version auswählen, die Sie im Azure-Websites-Portal verwenden möchten. Dazu öffnen Sie die Registerkarte "Konfigurieren" Ihrer Website und ändern die Einstellung **Python-Version**.

Ein komplexeres Beispiel für die Konfiguration des Django-Frameworks auf Azure-Websites finden Sie in folgendem Lernprogramm: 
[http://www.windowsazure.com/de-de/develop/python/tutorials/web-sites-with-django](http://www.windowsazure.com/de-de/develop/python/tutorials/web-sites-with-django).  

## WSGI-Unterstützung

WSGI ist ein Python-Standard entsprechend [PEP 3333],(http://www.python.org/dev/peps/pep-3333/) der eine Schnittstelle zwischen Webserver und Python definiert. Es bietet eine standardisierte Schnittstelle zum Schreiben verschiedener Webanwendungen und Frameworks mit Python.  Beliebte Python-Webframeworks nutzen heute WSGI.  Azure-Websites bietet Ihnen die Unterstützung für solche Frameworks. Darüber hinaus können erfahrenere Benutzer auch ihre eigenen Frameworks erstellen, sofern der benutzerdefinierte Handler die Richtlinien der WSGI-Spezifikation befolgt.

## Websiteerstellung

Für dieses Lernprogramm wird davon ausgegangen, dass Sie über ein Azure-Abonnement verfügen und Zugang zum Azure-Verwaltungsportal haben. Detaillierte Anweisungen zum Erstellen einer Website finden Sie unter [http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-create-websites](http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-create-websites).
 
Wenn Sie noch keine Website haben, können Sie eine über das Azure-Verwaltungsportal erstellen. Wählen Sie die Funktion WEBSITES und dann SCHNELLERFASSUNG aus, und geben Sie eine URL für Ihre Website an.

![](./media/web-sites-python-configure/configure-python-create-website.png)

## Git-Veröffentlichung

Verwenden Sie die Registerkarte SCHNELLSTART oder DASHBOARD für die neu erstellte Website, um die Git-Veröffentlichung zu konfigurieren.  In diesem Lernprogramm wird die Python-Website mit Git erstellt, verwaltet und auf Azure-Websites veröffentlicht. 

![](./media/web-sites-python-configure/configure-python-git.png)

Sobald die Git-Veröffentlichung eingerichtet ist, wird ein Git-Repository erstellt und der Website zugewiesen.  Die URL des Repository wird angezeigt und kann ab sofort verwendet werden, um Daten aus der lokalen Entwicklungsumgebung mittels Push in die Cloud zu übertragen. Stellen Sie beim Veröffentlichen von Anwendungen mittels Git sicher, dass auch ein Git-Client installiert ist, und verwenden Sie die bereitgestellten Anweisungen, um den Websiteinhalt per Push auf Azure-Websites zu übertragen.

## Websiteinhalt

Am Beispiel einer grundlegenden Python-Anwendung mit einem allgemeinen WSGI-Handler wird veranschaulicht, wie wenig Arbeitsaufwand erforderlich ist, um von der Python-Unterstützung in Azure-Websites zu profitieren.  Anhand dieses Grundgerüsts einer Python-Anwendung können Sie dann verschiedene Lösungen entwickeln, wobei deren Komplexität von dem unten gezeigten Beispiel bis hin zu einem umfassenden Webframework reicht.  

Nachfolgend sehen Sie den Code für den allgemeinen WSGI-Handler. Das ist vergleichbar mit den von [PEP 3333](http://www.python.org/dev/peps/pep-3333/) empfohlenen Spezifikationen als Ausgangspunkt für eine WSGI-kompatible Anwendung. Dieser Inhalt wurde in einer Datei namens "ConfigurePython.py" gespeichert, die in einem Ordner namens "ConfigurePython" im Stammverzeichnis der Website erstellt wurde:

	def application(environ, start_response):
	    status = '200 OK'
	    response_headers = [('Content-type', 'text/plain')]
	    start_response(status, response_headers)
	    yield 'Hello from Azure Websites\n'

*application* ist ein mit Python aufrufbares Element, das als Einstiegspunkt dient, der von einem WSGI-kompatiblen Server aufgerufen wird. Dieses aufrufbare Objekt akzeptiert zwei Positionsargumente: 

* *environ*: ein Wörterbuch mit verschiedenen Umgebungsvariablen
* *start_response*: ein aufrufbares Element des Webservers für die Übertragung des HTTP-Status und der Antwort-Header

Dieser Handler gibt den einfachen Text "Hello from Azure Websites" für jede eingehende Anforderung zurück.

## Konfigurationsoptionen

Es gibt zwei verschiedene Optionen zum Konfigurieren Ihrer Python-Anwendung mit Azure-Websites.

<h3 id="option1">Option 1: Portal</h3>

1,1. Registrieren Sie den FastCGI-Handler auf der Registerkarte KONFIGURIEREN im Portal.
In diesem Beispiel wird der FastCGI-Handler für Python verwendet, der im Lieferumfang von Azure-Websites enthalten ist. Verwenden Sie gleichermaßen die folgenden Pfade für Ihren Skriptprozessor und Ihr FastCGI-Handler-Argument:

* Pfad für Python-Skriptprozessor: D:\python27\python.exe
* Pfad für Python FastCGI-Handler: D:\python27\scripts\wfastcgi.py

![](./media/web-sites-python-configure/configure-python-handler-mapping.png)

1,2. Konfigurieren Sie die Anwendungseinstellungen über dieselbe Registerkarte KONFIGURIEREN im Portal.
Die Anwendungseinstellungen werden in Umgebungsvariablen umgewandelt. Dies ist ein Mechanismus, den Sie für Konfigurationswerte verwenden können, die von Ihrer Python-Anwendung benötigt werden. Bei dieser allgemeinen Beispielanwendung wurde Folgendes konfiguriert:

* PYTHONPATH informiert Python über das Verzeichnis, in dem nach Modulen gesucht werden soll. Azure-Websites bietet "D:\home\site\wwwroot" als syntaktischen Zucker, der auf das Stammverzeichnis Ihrer Website verweist. 
* WSGI\_HANDLER zeichnet einen Modul- oder Paketnamen sowie das zu verwendende Attribut auf.

![](./media/web-sites-python-configure/configure-python-app-settings.png)

<h3 id="option2">Option 2: web.config</h3>
Alternativ kann für die Konfiguration eine web.config-Datei im Stammverzeichnis der Website für die nachfolgend beschriebenen Aktionen verwendet werden. Die Option "web.config" bietet ein besseres Portabilitätspotenzial für eine Webanwendung. Es gibt zwei Ansätze für das Weiterleiten von Anfragen an die Webanwendung: Festlegen eines Handlers, der den *-Pfad verarbeitet, der wiederum IIS anweist, jede eingehende Anforderung durch Python zu leiten; oder Festlegen eines spezifischen Pfads, den Python verarbeitet, und anschließende Bereitstellung einer URL-Neuschreibung, um verschiedene URLs an den ausgewählten Pfad umzuleiten.  Wir empfehlen zur Leistungssteigerung letzteren Ansatz, wobei eine leere Handlerdatei im Stammverzeichnis der Website verwendet wird, die als Anforderungsziel dient (in unserem Beispiel "handler.fcgi"). Im vorherigen Szenario müssen alle Anforderungen, auch die für statischen Inhalt (z. B. Bilddateien und Stylesheets), Python durchlaufen, wobei die Optimierungen des Webservers im Hinblick auf den Zugriff auf statische Dateien umgekehrt werden.  Der letztere Ansatz ermöglicht die effiziente Bereitstellung von statischem Inhalt und ruft Python nur auf, wenn es notwendig ist.

2,1. Legen Sie die PYTHONPATH-Variable fest. 
> Dadurch wird Python darüber informiert, wo es nach dem Anwendungscode suchen soll. "D:\home\site\wwwroot" wird hier ebenfalls als absoluter Pfad zur Website verwendet.

2,2. Legen Sie die Variable WSGI\_HANDLER fest.
> Azure-Websites verwendet diesen Wert, um Python anzuweisen, den WSGI-Handler aufzurufen.  Der Wert dieser Variable ist ein Python-Ausdruck, der bei Ausführung ein aufrufbares Element zurückgeben sollte, das einen WSGI-Handler darstellt. 

2,3. Fügen Sie einen Handler für Python hinzu.
> Damit wird Azure Websites informiert, dass Python Anfragen übernehmen soll, die an den Pfad handler.fcgi erfolgen. Es ist wichtig, dass die Handlersyntax dabei genauso aussieht, wie in dem unten stehenden Beispiel im Tag &lt;handlers&gt;, es sei denn, Sie verwenden Ihren eigenen FastCGI-Handler oder Ihr eigenes Python-Entwicklerstack.

2,4. Schreiben Sie URLs für "handler.fcgi" neu.
> Die kontinuierliche Anforderung von "handler.fcgi" ist möglicherweise nicht die beste Lösung. Zur Auswahl des Pfads mit Dateien, die vom Python-Handler verarbeitet werden sollen, haben wir eine URL-Neuschreibung verwendet, damit alle URLs von unserem Python-Handler verarbeitet werden.

	<configuration>
  		<appSettings>
    		<add key="pythonpath" value="D:\home\site\wwwroot\ConfigurePython" />
    		<add key="WSGI_HANDLER" value="ConfigurePython.application" />
  		</appSettings>
  		<system.webServer>
    		<handlers>
      			<add name="PythonHandler" 
           		verb="*" path="handler.fcgi" 
           		modules="FastCgiModule" 
           		scriptProcessor="D:\Python27\Python.exe|D:\Python27\Scripts\wfastcgi.py" 
           		resourceType="Either" />
   			</handlers>
			<rewrite>
	    		<rules>
					<rule name="Configure Python" stopProcessing="true">
		    			<match url="(.*)" ignoreCase="false" />
		    			<conditions>
							<add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
		    			</conditions>
		    			<action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
					</rule>
	    		</rules>
			</rewrite>
  		</system.webServer>
	</configuration> 

Die Ordnerstruktur für das Beispiel im Stammverzeichnis der Website sieht folgendermaßen aus (die Groß-/Kleinschreibung von Ordner- und Dateinamen muss bei Python beachtet werden und wird in "web.config" deutlich):

* ConfigurePython\ConfigurePython.py
* web.config
* handler.fcgi

Da alle URLs zu "handler.fcgi" neu geschrieben werden und dieser Pfad per FastCGI an Python übergeben wird, muss eine Platzhalterdatei mit demselben Namen erstellt werden, damit IIS keinen HTTP 404-Fehler zurückgibt. Die Ursache dafür ist das interne Verhalten des IIS FastCGI-Moduls, das ein Vorhandensein der angeforderten Datei zwingend erforderlich macht, bevor sie an die festgelegte Skriptprozessoranwendung übergeben wird.

Navigieren Sie zu Ihrer Website, um die richtige Konfiguration zu testen. In diesem Beispiel wird die Meldung "Hello from Azure Websites" beim Zugriff angezeigt.

![](./media/web-sites-python-configure/configure-python-result.png)

\n<!--HONumber=35.1--> 
