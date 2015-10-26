<properties
	pageTitle="Python-Web-App mit Django | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie Sie eine Django-basierte Website auf Azure mithilfe eines virtuellen Computers mit Windows Server 2012 R2 Datacenter unter Verwendung des klassischen Bereitstellungsmodells hosten."
	services="virtual-machines"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""
	tags="azure-service-management"/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="huvalo"/>


# Django-Webanwendung "Hello World" auf einem virtuellen Windows Server-Computer

> [AZURE.SELECTOR]
- [Windows](virtual-machines-python-django-web-app-windows-server.md)
- [Mac/Linux](virtual-machines-python-django-web-app-linux.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.
 

In diesem Lernprogramm erfahren Sie, wie Sie eine Django-basierte Website unter Microsoft Azure mithilfe eines virtuellen Windows Server-Computers hosten können. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Django-basierte Anwendung, die in der Cloud ausgeführt wird.

Sie lernen Folgendes:

* Einrichten eines virtuellen Azure-Computers als Host von Django. In diesem Lernprogramm wird diese Aufgabe unter Windows Server beschrieben; das Gleiche gilt jedoch auch für eine Linux-VM, die in Azure gehostet wird.
* Erstellen einer neuen Django-Anwendung unter Windows.

Im Rahmen dieses Lernprogramms erstellen Sie eine einfache Webanwendung "Hello World". Die Anwendung wird auf einem virtuellen Azure-Computer gehostet.

Nachstehend finden Sie einen Screenshot der vollständigen Anwendung:

![Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Erstellen und Konfigurieren eines virtuellen Azure-Computers als Host von Django

1. Befolgen Sie die [hier](virtual-machines-windows-tutorial-classic-portal.md) aufgeführten Anweisungen, um einen virtuellen Azure-Computer mit der Windows Server 2012 R2 Datacenter-Distribution zu erstellen.

1. Weisen Sie Azure an, den Port 80-Datenverkehr aus dem Web an Port 80 auf dem virtuellen Computer zu leiten.
 - Navigieren Sie im Azure-Portal zu Ihrem neu erstellten virtuellen Computer, und klicken Sie auf die Registerkarte **ENDPUNKTE**.
 - Klicken Sie unten auf der Seite auf **HINZUFÜGEN**. ![Endpunkt hinzufügen](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

 - Öffnen Sie **ÖFFENTLICHER PORT 80 ** des **TCP**-Protokolls als **PRIVATER PORT 80**. ![][port80]
1. Klicken Sie auf der Registerkarte **DASHBOARD** auf **Verbinden** zum Verwenden von **Remotedesktop**, um sich beim neu erstellten virtuellen Azure-Computer remote anzumelden.  

**Wichtiger Hinweis:** Für alle nachfolgend aufgeführten Anweisungen müssen Sie ordnungsgemäß beim virtuellen Computer angemeldet sein und Befehle dort anstatt von Ihrem lokalen Computer aus eingeben.

## <a id="setup"> </a>Installation von Python, Django, WFastCGI

**Hinweis:** Für den Download mithilfe von Internet Explorer müssen Sie möglicherweise die erweiterten Sicherheitseinstellungen für Internet Explorer konfigurieren ("Start > Verwaltung > Server-Manager > Lokaler Server". Klicken Sie dann auf **Verstärkte Sicherheitskonfiguration für IE**, und legen Sie "Aus" fest.)

1. Installieren Sie das neueste Python 2.7 oder 3.4 über [python.org][].
1. Installieren Sie die Wfastcgi- und Django-Pakete mithilfe von PIP.

    Verwenden Sie für Python 2.7 den folgenden Befehl.

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    Verwenden Sie für Python 3.4 den folgenden Befehl.

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## Installieren von IIS mit FastCGI

1. Installieren Sie IIS mit Unterstützung für FastCGI. Die Ausführung nimmt möglicherweise einige Minuten in Anspruch.

		start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## Erstellen einer neuen Django-Anwendung

1.  Geben Sie in *C:\\inetpub\\wwwroot* den folgenden Befehl zum Erstellen eines neuen Django-Projekts ein:

    Verwenden Sie für Python 2.7 den folgenden Befehl.

		C:\Python27\Scripts\django-admin.exe startproject helloworld

    Verwenden Sie für Python 3.4 den folgenden Befehl.

		C:\Python34\Scripts\django-admin.exe startproject helloworld

	![Ergebnis des Befehls 'New-AzureService'](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

1.  Mit dem Befehl **django-admin** wird eine Grundstruktur für Django-basierte Websites erstellt:

  -   **helloworld\\manage.py** hilft Ihnen, den Hostvorgang für Ihre Django-basierte Website zu starten und zu beenden.
  -   **helloworld\\helloworld\\settings.py** enthält Django-Einstellungen für Ihre Anwendung.
  -   **helloworld\\helloworld\\urls.py** enthält den Zuordnungscode zwischen den einzelnen URLs und der entsprechenden Ansicht.

1.  Erstellen Sie eine neue Datei mit dem Namen **views.py** im Verzeichnis *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Diese enthält die Ansicht, mit der die "Hello World"-Seite generiert wird. Starten Sie den Editor, und geben Sie Folgendes ein:

		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Ersetzen Sie den Inhalt der Datei urls.py durch den folgenden Code.

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)

## Konfigurieren von IIS

1. Entsperren Sie den Abschnitt für Handler in der globalen Datei "applicationhost.config". Dies ermöglicht die Verwendung des Python-Handlers in "web.config".

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. Aktivieren Sie WFastCGI. Dadurch wird der globalen Datei "applicationhost.config" eine Anwendung hinzugefügt, die auf die ausführbare Datei des Python-Interpreters und das Skript "wfastcgi.py" verweist.

    Python 2.7:

        c:\python27\scripts\wfastcgi-enable

    Python 3.4:

        c:\python34\scripts\wfastcgi-enable

1. Erstellen Sie eine Datei "web.config" unter *C:\\inetpub\\wwwroot\\helloworld*. Der Wert des `scriptProcessor`-Attributs sollte mit der Ausgabe des vorherigen Schritts übereinstimmen. Weitere Informationen zu wfastcgi-Einstellungen finden Sie auf der Seite zu [wfastcgi][] auf PyPI.

    Python 2.7:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. Aktualisieren Sie den Speicherort der IIS-Standardwebsite auf den Django-Projektordner.

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. Laden Sie dann die Webseite in Ihrem Browser.

![Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.][1]


## Herunterfahren des virtuellen Azure-Computers

Wenn Sie mit diesem Lernprogramm fertig sind, fahren Sie den neu erstellten virtuellen Azure-Computer herunter, oder entfernen Sie diesen, sodass die Ressourcen für andere Lernprogramme zur Verfügung stehen und anfallende Kosten für die Verwendung von Azure vermieden werden.

[1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

<!---HONumber=Oct15_HO3-->