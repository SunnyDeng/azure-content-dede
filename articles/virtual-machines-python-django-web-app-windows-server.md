<properties urlDisplayName="Web with Django (Windows)" pageTitle="Python-Web-App mit Django &ndash; Azure-Lernprogramm" metaKeywords="Azure Django web app, Azure Django virtual machine" description="In diesem Lernprogramm erfahren Sie, wie Sie eine Django-basierte Website auf Azure mithilfe eines virtuellen Computers mit Windows Server 2012 R2 Datacenter hosten." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django-Webanwendung &quot;Hello World&quot;" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo" />

# Django-Webanwendung "Hello World"

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/de-de/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux">Mac/Linux</a></div>

In diesem Lernprogramm erfahren Sie, wie Sie eine Django-basierte Website unter Microsoft
Azure mithilfe eines virtuellen Computers mit Windows Server hosten können. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Django-basierte Anwendung, die in der Cloud ausgeführt wird.

Sie erhalten Informationen zu folgenden Themen:

-   Einrichten eines virtuellen Azure-Computers als Host von Django. In diesem Lernprogramm wird diese Aufgabe unter **Windows Server** beschrieben; das Gleiche gilt jedoch auch für eine Linux-VM, die in Azure gehostet wird.
-   Erstellen einer neuen Django-Anwendung unter Windows.

Im Rahmen dieses Lernprogramms erstellen Sie eine einfache
Webanwendung "Hello World". Die Anwendung wird auf einem virtuellen Azure-Computer gehostet.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.][Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Erstellen und Konfigurieren eines virtuellen Azure-Computers als Host von Django

1.  Befolgen Sie die [hier][hier] aufgeführten Anweisungen, um einen virtuellen Azure-Computer mit der *Windows Server 2012 R2 Datacenter*-Distribution zu erstellen.

2.  Weisen Sie Azure an, den Port **80**-Datenverkehr aus dem Web an Port **80** auf dem virtuellen Computer zu leiten.

-   Navigieren Sie im Azure-Portal zu Ihrem neu erstellten virtuellen Computer, und klicken Sie auf die Registerkarte *ENDPUNKTE*.
-   Klicken Sie am unteren Rand des Bildschirms auf *HINZUFÜGEN*.
    ![Endpunkt hinzufügen][Endpunkt hinzufügen]

-   Öffnen Sie *ÖFFENTLICHER PORT 80* des *TCP*-Protokolls als *PRIVATER PORT 80*.
    ![][0].

1.  Klicken Sie auf der Registerkarte *DASHBOARD* auf *Verbinden* zum Verwenden von *Remotedesktop*, um sich beim neu erstellten virtuellen Azure-Computer remote anzumelden.

**Wichtiger Hinweis:** Für alle unten stehenden Anweisungen müssen Sie ordnungsgemäß am virtuellen Computer angemeldet sein und Befehle dort anstelle von Ihrem lokalen Computer ausgeben.

## <span id="setup"></span> </a>Einrichten von Python und Django

**Hinweis:** Für den Download mithilfe von Internet Explorer müssen Sie möglicherweise die erweiterten Sicherheitseinstellungen für Internet Explorer („Start \> Verwaltung \> Server-Manager \> Lokaler Server“, klicken Sie dann auf **Verstärkte Sicherheitskonfiguration für IE konfigurieren**, legen Sie die Einstellung auf „Aus“ fest) konfigurieren.

1.  Installieren Sie den [Webplattform-Installer][Webplattform-Installer].
2.  Installieren Sie Python und WFastCGI mit dem Webplattform-Installer. Dadurch wird wfastcgi.py in Ihrem Python-Skriptordner installiert.

    1.  Starten Sie den Webplattform-Installer.
    2.  Geben Sie „WFastCGI“ in der Suchleiste ein.
    3.  Wählen Sie den Eintrag „WFactCGI“ für die Python-Version aus, die Sie verwenden möchten (2.7 oder 3.4). Beachten Sie, dass dadurch Python als eine Abhängigkeit von WFastCGI installiert wird.

3.  Wenn Sie Python 2.7 installiert haben, [befolgen Sie diese Anweisungen zum manuellen Installieren von pip][befolgen Sie diese Anweisungen zum manuellen Installieren von pip] (bei Python 3.4 ist pip bereits installiert).
4.  Installieren Sie Django mit pip.

    Python 2.7:

        c:\python27\scripts\pip install django

    Python 3.4:

        c:\python34\scripts\pip install django

## Einrichten von IIS mit FastCGI

1.  Installieren Sie IIS mit Unterstützung für FastCGI. Die Ausführung nimmt möglicherweise einige Minuten in Anspruch.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

### Python 2,7

Führen Sie diese Befehle nur dann aus, wenn Sie Python 2.7 verwenden.

1.  Richten Sie den Python FastCGI-Handler ein.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

2.  Registrieren Sie den Handler für diese Website.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

3.  Konfigurieren Sie den Handler, um Ihre Django-Anwendung auszuführen.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost

4.  Konfigurieren Sie PYTHONPATH, damit Ihre Django-Anwendung vom Python-Übersetzer gefunden werden kann.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost

5.  Weisen Sie dem FastCGI-zu-WSGI-Gateway den zu verwendenden WSGI-Handler zu.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

6.  Daraufhin sollte Folgendes angezeigt werden:

    ![IIS config1][IIS config1]

### Python 3.4

Führen Sie diese Befehle nur dann aus, wenn Sie Python 3.4 verwenden.

1.  Richten Sie den Python FastCGI-Handler ein.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py']"

2.  Registrieren Sie den Handler für diese Website.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python34\python.exe|C:\Python34\Scripts\wfastcgi.py',resourceType='Unspecified']"

3.  Konfigurieren Sie den Handler, um Ihre Django-Anwendung auszuführen.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost

4.  Konfigurieren Sie PYTHONPATH, damit Ihre Django-Anwendung vom Python-Übersetzer gefunden werden kann.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost

5.  Weisen Sie dem FastCGI-zu-WSGI-Gateway den zu verwendenden WSGI-Handler zu.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

6.  Daraufhin sollte Folgendes angezeigt werden:

    ![IIS config1][1]

## Erstellen einer neuen Django-Anwendung

1.  Geben Sie in *C:\\inetpub\\wwwroot* den folgenden Befehl zum Erstellen eines neuen Django-Projekts ein:

    Python 2.7:

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Python 3.4:

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![Ergebnis des Befehls 'New-AzureService'][Ergebnis des Befehls 'New-AzureService']

2.  Mit dem Befehl **django-admin** wird eine Grundstruktur für Django-basierte Websites erstellt:

-   **helloworld\\manage.py** hilft Ihnen, den Hostvorgang für Ihre Django-basierte Website zu starten und zu beenden.
-   **helloworld\\helloworld\\settings.py** enthält Django-Einstellungen für Ihre Anwendung.
-   **helloworld\\helloworld\\urls.py** enthält den Zuordnungscode zwischen den einzelnen URLs und der entsprechenden Ansicht.

1.  Erstellen Sie eine neue Datei mit dem Namen **views.py** im Verzeichnis *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Diese enthält die Ansicht, mit der die "Hello World"-Seite generiert wird. Starten Sie den Editor, und geben Sie Folgendes ein:

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  Ersetzen Sie den Inhalt der Datei **urls.py** durch den folgenden Code:

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

3.  Laden Sie dann die Webseite in Ihrem Browser.

![Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.][Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.]

## Herunterfahren des virtuellen Azure-Computers

Wenn Sie mit diesem Lernprogramm fertig sind, fahren Sie den neu erstellten virtuellen Azure-Computer herunter, und/oder entfernen Sie diesen, sodass die Ressourcen für andere Lernprogramme zur Verfügung stehen und anfallende Kosten für die Verwendung von Azure vermieden werden.

  [Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [hier]: /de-de/manage/windows/tutorials/virtual-machine-from-gallery/
  [Endpunkt hinzufügen]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  [0]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [Webplattform-Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [befolgen Sie diese Anweisungen zum manuellen Installieren von pip]: https://pip.pypa.io/en/latest/installing.html
  [IIS config1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-27.png
  [1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-34.png
  [Ergebnis des Befehls 'New-AzureService']: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png
