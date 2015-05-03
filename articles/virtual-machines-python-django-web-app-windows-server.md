<properties 
	pageTitle="Python-Web-App mit Django - Azure-Lernprogramm" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie eine Django-basierte Website auf Azure mithilfe eines virtuellen Computers mit Windows Server 2012 R2 Datacenter hosten." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>




# Django-Webanwendung "Hello World"

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

In diesem Lernprogramm erfahren Sie, wie Sie eine Django-basierte Website unter Microsoft 
Azure mithilfe eines virtuellen Windows Server-Computers hosten können. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Django-basierte Anwendung, die in der Cloud ausgeführt wird.

Sie erhalten Informationen zu folgenden Themen:

* Einrichten eines virtuellen Azure-Computers als Host von Django. In diesem Lernprogramm wird diese Aufgabe unter **Windows Server** beschrieben; das Gleiche gilt jedoch auch für eine Linux-VM, die in Azure gehostet wird. 
* Erstellen einer neuen Django-Anwendung unter Windows.

Im Rahmen dieses Lernprogramms erstellen Sie eine einfache "Hello World"-Webanwendung.
"Hello World"-Webanwendung. Die Anwendung wird auf einem virtuellen Azure-Computer gehostet.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![A browser window displaying the hello world page on Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Erstellen und Konfigurieren eines virtuellen Azure-Computers als Host von Django

1. Befolgen Sie die [hier][portal-vm] aufgeführten Anweisungen, um einen virtuellen Azure-Computer der *Windows Server 2012 R2 Datacenter*-Verteilung zu erstellen.

1. Weisen Sie Azure an, den Port **80**-Datenverkehr aus dem Web an Port **80** auf dem virtuellen Computer zu leiten.
 - Navigieren Sie im Azure-Portal zu Ihrem neu erstellten virtuellen Computer, und klicken Sie auf die Registerkarte *ENDPUNKTE*.
 - Klicken Sie unten auf der Seite auf *HINZUFÜGEN*.
	![add endpoint](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

 - Öffnen Sie den *TCP* des *PUBLIC PORT 80*-Protokolls als *PRIVATE PORT 80*.
![][port80]
1. Klicken Sie auf der Registerkarte *DASHBOARD* auf *CONNECT*, um sich unter Verwendung von *Remote Desktop* beim neu erstellten virtuellen Azure-Computer remote anzumelden.  

**Wichtiger Hinweis:** Für alle nachfolgend aufgeführten Anweisungen müssen Sie ordnungsgemäß beim virtuellen Computer angemeldet sein und Befehle dort anstelle von Ihrem lokalen Computer ausgeben. 

## <a id="setup"> </a>Einrichten von Python und Django

**Hinweis:** Für den Download mithilfe von Internet Explorer müssen Sie möglicherweise die erweiterten Sicherheitseinstellungen für Internet Explorer konfigurieren ("Start > Verwaltung > Server-Manager > Lokaler Server", Klicken auf **Verstärkte Sicherheitskonfiguration für IE konfigurieren** und Festlegen der Einstellung auf "Aus").

1. Installieren Sie [Webplattform-Installer][].
1. Installieren Sie Python und WFastCGI mit dem Webplattform-Installer.  Dadurch wird wfastcgi.py in Ihrem Python-Skriptordner installiert.
	1. Starten Sie den Webplattform-Installer.
	1. Geben Sie "WFastCGI" in der Suchleiste ein. 
	1. Wählen Sie den Eintrag "WFactCGI" für die Python-Version aus, die Sie verwenden möchten (2.7 oder 3.4).  Beachten Sie, dass dadurch Python als eine Abhängigkeit von WFastCGI installiert wird. 
1. Installieren Sie Django mit pip.

    Python 2.7:

        c:\python27\scripts\pip install django

    Python 3.4:

        c:\python34\scripts\pip install django


## Einrichten von IIS mit FastCGI

1. Installieren Sie IIS mit Unterstützung für FastCGI.  Die Ausführung nimmt möglicherweise einige Minuten in Anspruch.

		start /wait %windir%\System32\\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI


### Python 2.7

Führen Sie diese Befehle nur dann aus, wenn Sie Python 2.7 verwenden.

1. Richten Sie den Python FastCGI-Handler ein.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"


1. Registrieren Sie den Handler für diese Website.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Konfigurieren Sie den Handler, um Ihre Django-Anwendung auszuführen.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost


1. Konfigurieren Sie PYTHONPATH, damit Ihre Django-Anwendung vom Python-Übersetzer gefunden werden kann.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost


1. Weisen Sie dem FastCGI-zu-WSGI-Gateway den zu verwendenden WSGI-Handler zu.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost


1. Daraufhin sollte Folgendes angezeigt werden:

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-27.png) 

### Python 3.4

Führen Sie diese Befehle nur dann aus, wenn Sie Python 3.4 verwenden.

1. Richten Sie den Python FastCGI-Handler ein.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py']"


1. Registrieren Sie den Handler für diese Website.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python34\python.exe|C:\Python34\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Konfigurieren Sie den Handler, um Ihre Django-Anwendung auszuführen.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost


1. Konfigurieren Sie PYTHONPATH, damit Ihre Django-Anwendung vom Python-Übersetzer gefunden werden kann.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost


1. Weisen Sie dem FastCGI-zu-WSGI-Gateway den zu verwendenden WSGI-Handler zu.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

1. Daraufhin sollte Folgendes angezeigt werden:

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-34.png) 


## Erstellen einer neuen Django-Anwendung


1.  Geben Sie in *C:\inetpub\wwwroot* den folgenden Befehl zum Erstellen eines neuen Django-Projekts ein:

    Python 2.7:

		C:\Python27\Scripts\django-admin.exe startproject helloworld

    Python 3.4:

		C:\Python34\Scripts\django-admin.exe startproject helloworld
    
	![The result of the New-AzureService command](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

1.  Mit dem Befehl **django-admin** wird eine Grundstruktur für Django-basierte Websites erstellt:
    
  -   **helloworld\manage.py** hilft Ihnen, den Hostvorgang für Ihre Django-basierte Website zu starten und zu beenden.
  -   **helloworld\helloworld\settings.py** enthält Django-Einstellungen für Ihre Anwendung.
  -   **helloworld\helloworld\urls.py** enthält den Zuordnungscode zwischen den einzelnen URLs und der entsprechenden Ansicht.



1.  Erstellen Sie eine neue Datei namens **views.py** im Verzeichnis  *C:\inetpub\wwwroot\helloworld\helloworld*. Diese enthält die Ansicht, mit der die "Hello World"-Seite generiert wird. Starten Sie den Editor, und geben Sie Folgendes ein:
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Ersetzen Sie den Inhalt der Datei **urls.py** durch den folgenden Code:

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)

1. Laden Sie dann die Webseite in Ihrem Browser.

![A browser window displaying the hello world page on Azure][1]

## Herunterfahren des virtuellen Azure-Computers

Wenn Sie mit diesem Lernprogramm fertig sind, fahren Sie den neu erstellten virtuellen Azure-Computer herunter, und/oder entfernen Sie diesen, sodass die Ressourcen für andere Lernprogramme zur Verfügung stehen und anfallende Kosten für die Verwendung von Azure vermieden werden.

[1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png

[portal-vm]: /manage/windows/tutorials/virtual-machine-from-gallery/

[Webplattform-Installer]: http://www.microsoft.com/web/downloads/platform.aspx


<!--HONumber=47-->
