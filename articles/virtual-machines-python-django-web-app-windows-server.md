<properties linkid="develop-python-web-app-with-django" urlDisplayName="Web with Django (Windows)" pageTitle="Python web app with Django - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to host a Django-based website on Azure using a Windows Server 2008 R2 virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Django-Webanwendung "Hello World"

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/de-de/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux">Mac/Linux</a></div>

In diesem Lernprogramm erfahren Sie, wie Sie eine Django-basierte Website unter Windows
Azure mithilfe eines virtuellen Windows Server-Computers hosten können. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Django-basierte Anwendung, die in der Cloud ausgeführt wird.

Sie erhalten Informationen zu folgenden Themen:

-   Einrichten eines virtuellen Azure-Computers als Host von Django. In diesem Lernprogramm wird diese Aufgabe unter **Windows Server** beschrieben; das Gleiche gilt jedoch auch für eine Linux-VM, die in Azure gehostet wird.
-   Erstellen einer neuen Django-Anwendung unter Windows.

Im Rahmen dieses Lernprogramms erstellen Sie eine einfache
Webanwendung "Hello World". Die Anwendung wird auf einem virtuellen Azure-Computer gehostet.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.][Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Erstellen und Konfigurieren eines virtuellen Azure-Computers als Host von Django

1.  Befolgen Sie die [hier][hier] aufgeführten Anweisungen, um einen virtuellen Azure-Computer mit der *Windows Server 2012 Datacenter*-Distribution zu erstellen.

2.  Weisen Sie Azure an, den Port **80**-Datenverkehr aus dem Web an Port **80** auf dem virtuellen Computer zu leiten.

-   Navigieren Sie im Azure-Portal zu Ihrem neu erstellten virtuellen Computer, und klicken Sie auf die Registerkarte *ENDPUNKTE*.
-   Klicken Sie am unteren Rand des Bildschirms auf *ENDPUNKT HINZUFÜGEN*.
    ![Endpunkt hinzufügen][Endpunkt hinzufügen]

-   Öffnen Sie *ÖFFENTLICHER PORT 80* des *TCP*-Protokolls als *PRIVATER PORT 80*.
    ![][0].

1.  Verwenden Sie Windows *Remotedesktop*, um sich von einem Remotestandort aus am neu erstellten virtuellen Azure-Computer anzumelden.

**Wichtiger Hinweis:** Für alle unten stehenden Anweisungen müssen Sie ordnungsgemäß am virtuellen Computer angemeldet sein und Befehle dort anstelle von Ihrem lokalen Computer ausgeben.

## <span id="setup"></span> </a>Einrichten der Entwicklungsumgebung

Weitere Informationen zum Einrichten der Python- und Django-Umgebungen finden Sie im [Installationshandbuch][Installationshandbuch].

**Hinweis 1:** Sie müssen *nur* das **Django**-Produkt aus dem Windows WebPI-Installer auf dem virtuellen Azure-Computer installieren, um *dieses* spezielle Lernprogramm auszuführen zu können.

**Hinweis 2:** Zum Herunterladen des WebPI-Installers müssen Sie ggf. die IE ESC-Einstellungen konfigurieren (Start \> Verwaltung \> Server-Manager, klicken Sie dann auf **Verstärkte Sicherheitskonfiguration für IE konfigurieren**, die auf "Aus" gesetzt sind).

## Einrichten von IIS mit FastCGI

1.  Installieren Sie IIS mit Unterstützung für FastCGI.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

2.  Richten Sie den Python FastCGI-Handler ein.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

3.  Registrieren Sie den Handler für diese Website.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

4.  Konfigurieren Sie den Handler, um Ihre Django-Anwendung auszuführen.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='DjangoApplication.settings']" /commit:apphost

5.  Konfigurieren Sie PYTHONPATH, damit Ihre Django-Anwendung vom Python-Übersetzer gefunden werden kann.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\DjangoApplication']" /commit:apphost

    Daraufhin sollte Folgendes angezeigt werden:

    ![IIS config1][IIS config1]

6.  Weisen Sie dem FastCGI-zu-WSGI-Gateway den zu verwendenden WSGI-Handler zu:

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

7.  Laden Sie "wfastcgi.py" von [codeplex][codeplex] herunter, und speichern Sie es unter "C:\\Python27\\Scripts". Dabei handelt es sich um das Verzeichnis, das von vorherigen Befehlen zum Registrieren des FastCGI-Handlers verwendet wurde. Alternativ können Sie es mit dem Web Platform-Installer installieren. Suchen Sie nach "WFastCGI".

## Erstellen einer neuen Django-Anwendung

1.  Starten Sie cmd.exe.

2.  Wechseln Sie mithilfe des Befehls "cd" zum Verzeichnis "C:\\inetpub\\wwwroot".

3.  Geben Sie den folgenden Befehl ein, um ein neues Django-Projekt zu erstellen:

    C:\\Python27\\python.exe -m django.bin.django-admin startproject DjangoApplication

    ![Ergebnis des Befehls 'New-AzureService'][Ergebnis des Befehls 'New-AzureService']

Mit dem Skript **django-admin.py** wird eine Grundstruktur für Django-basierte Websites erstellt:

-   **manage.py** hilft Ihnen, den Hostvorgang für Ihre Django-basierte Website zu starten und zu beenden.
-   **DjangoApplication\\settings.py** enthält Django-Einstellungen für Ihre Anwendung.
-   **DjangoApplication\\urls.py** enthält den Zuordnungscode zwischen den einzelnen URLs und der entsprechenden Ansicht.

1.  Erstellen Sie im Unterverzeichnis *DjangoApplication* von *C:\\inetpub\\wwwroot\\DjangoApplication* eine neue Datei mit dem Namen **views.py** als gleichgeordnetes Element von **urls.py**. Diese enthält die Ansicht, mit der die "Hello World"-Seite generiert wird. Starten Sie den Editor, und geben Sie Folgendes ein:

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  Ersetzen Sie nun den Inhalt der Datei **urls.py** durch den folgenden Code:

        from django.conf.urls.defaults import patterns, include, url
        from DjangoApplication.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

3.  Laden Sie dann die Webseite in Ihrem Browser.

![Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.][Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.]

## Herunterfahren des virtuellen Azure-Computers

Wenn Sie mit diesem Lernprogramm fertig sind, fahren Sie den neu erstellten virtuellen Azure-Computer herunter, und/oder entfernen Sie diesen, sodass die Ressourcen für andere Lernprogramme zur Verfügung stehen und anfallende Kosten für die Verwendung von Azure vermieden werden.

  [Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [hier]: /de-de/manage/windows/tutorials/virtual-machine-from-gallery/
  [Endpunkt hinzufügen]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  [0]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [Installationshandbuch]: ../python-how-to-install/
  [IIS config1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis1.png
  [codeplex]: http://go.microsoft.com/fwlink/?LinkID=316392&clcid=0x409
  [Ergebnis des Befehls 'New-AzureService']: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png
