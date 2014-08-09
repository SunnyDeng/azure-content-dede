<properties linkid="develop-python-web-app-with-django-mac" urlDisplayName="Web with Django" pageTitle="Python web app with Django on Mac - Azure tutorial" metaKeywords="" description="A tutorial that shows how to host a Django-based web site on Azure using a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application (mac-linux)" authors="" solutions="" manager="" editor="" />

Django-Webanwendung "Hello World" (Mac, Linux)
==============================================

[Windows](/de-de/develop/python/tutorials/web-app-with-django/ "Windows")[Mac/Linux](/de-de/develop/python/tutorials/django-hello-world-(maclinux)/ "Mac/Linux")

In diesem Lernprogramm erfahren Sie, wie Sie eine Django-basierte Website unter Azure mithilfe eines virtuellen Linux-Computers hosten können. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Django-basierte Anwendung, die in der Cloud ausgeführt wird.

Sie lernen Folgendes:

-   Einrichten eines virtuellen Azure-Computers als Host von Django. In diesem Lernprogramm wird diese Aufgabe für **Linux** beschrieben; das Gleiche gilt jedoch auch für eine Windows Server-VM, die in Azure gehostet wird.
-   Erstellen einer neuen Django-Anwendung unter Linux.

Im Rahmen dieses Lernprogramms erstellen Sie eine einfache Webanwendung "Hello World". Die Anwendung wird auf einem virtuellen Azure-Computer gehostet.

Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

Erstellen und Konfigurieren eines virtuellen Azure-Computers als Host von Django
--------------------------------------------------------------------------------

1.  Befolgen Sie die [hier](/de-de/manage/linux/tutorials/virtual-machine-from-gallery/) aufgeführten Anweisungen, um einen virtuellen Azure-Computer mit der *Ubuntu Server 12.04*-Distribution zu erstellen.

**Hinweis:** Sie müssen *nur* den virtuellen Computer erstellen. Hören Sie mit Abschnitt *Anmelden bei einem virtuellen Computer nach dessen Erstellung* auf.

1.  Weisen Sie Azure an, den Port **80**-Datenverkehr aus dem Web an Port **80** auf dem virtuellen Computer zu leiten.
    -   Navigieren Sie zu Ihrem neu erstellten virtuellen Computer im Azure Portal, und klicken Sie auf die Registerkarte *ENDPUNKTE*.
    -   Klicken Sie am unteren Rand des Bildschirms auf *ENDPUNKT HINZUFÜGEN*. ![Endpunkt hinzufügen](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
    -   Öffnen Sie *Öffentlicher Port 80* des *TCP*-Protokolls als *PRIVATER PORT 80*. ![Port 80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

Einrichten der Entwicklungsumgebung
-----------------------------------

**Hinweis:** Wenn Sie Python installieren müssen oder die Clientbibliotheken verwenden möchten, finden Sie weitere Informationen im [Installationshandbuch für Python](../python-how-to-install/).

Auf der Ubuntu Linux-VM ist Python 2.7 bereits vorab installiert; Apache oder Django sind jedoch noch nicht installiert. Befolgen Sie diese Schritte, um eine Verbindung zum virtuellen Computer herzustellen sowie um Apache und Django zu installieren.

1.  Öffnen Sie ein neues **Terminal**-Fenster.

2.  Geben Sie den folgenden Befehl ein, um eine Verbindung zum virtuellen Azure-Computer herzustellen.

        $ ssh eigenerbenutzername@eigeneVMUrl

3.  Geben Sie den folgenden Befehl zum Installieren von Django ein.

        $ sudo apt-get install python-setuptools
        $ sudo easy_install django

4.  Geben Sie den folgenden Befehl zum Installieren von Apache mit mod-wsgi ein:

        $ sudo apt-get install apache2 libapache2-mod-wsgi

Erstellen einer neuen Django-Anwendung
--------------------------------------

1.  Öffnen Sie das **Terminal**-Fenster, das Sie im vorherigen Abschnitt zum Herstellen einer SSH-Verbindung zum virtuellen Computer verwendet haben.

2.  Geben Sie die folgenden Befehle ein, um ein neues Django-Projekt zu erstellen:

    ![Das Ergebnis des Befehls 'django-admin'](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-dir.png)

    Mit dem Skript **django-admin.py** wird eine Grundstruktur für Django-basierte Websites erstellt: - **manage.py** hilft Ihnen, den Hostvorgang für Ihre Django-basierte Website zu starten und zu beenden. - **helloworld\\settings.py** enthält Django-Einstellungen für Ihre Anwendung. - **helloworld\\urls.py** enthält den Zuordnungscode zwischen den einzelnen URLs und der entsprechenden Ansicht.

3.  Erstellen Sie im Unterverzeichnis *helloworld* von *django\\helloworld* eine neue Datei mit dem Namen **views.py** als gleichgeordnetes Element von **urls.py**. Diese enthält die Ansicht, mit der die "Hello World"-Seite generiert wird. Starten Sie den Editor, und geben Sie Folgendes ein:

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

4.  Ersetzen Sie nun den Inhalt der Datei **urls.py** durch den folgenden Code:

        from django.conf.urls.defaults import patterns, include, url
        from helloworld.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

Bereitstellen und Ausführen der Django-Website
----------------------------------------------

1.  Bearbeiten Sie die Apache-Konfigurationsdatei **/etc/apache2/httpd.conf**, und fügen Sie Folgendes hinzu, ersetzen Sie dabei *benutzername* durch den Benutzernamen, den Sie beim Erstellen des virtuellen Computers angegeben haben:

        WSGIScriptAlias / /home/*benutzername*/django/helloworld/helloworld/wsgi.py
        WSGIPythonPath /home/*benutzername*/django/helloworld

        <Directory /home/*benutzername*/django/helloworld/helloworld>
        <Files wsgi.py>
        Order deny,allow
        Allow from all
        </Files>
        </Directory>

2.  Starten Sie Apache mit dem folgenden Befehl neu:

        $ sudo apachectl restart

3.  Laden Sie dann die Webseite in Ihrem Browser:

    ![Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

Herunterfahren des virtuellen Azure-Computers
---------------------------------------------

Fahren Sie den neu erstellten virtuellen Azure-Computer nach Abschluss des Lernprogramms herunter, und/oder entfernen Sie ihn, um Ressourcen für andere Lernprogramme freizugeben und um anfallende Azure-Nutzungsgebühren zu vermeiden.

