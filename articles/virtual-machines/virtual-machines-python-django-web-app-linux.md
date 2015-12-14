<properties 
	pageTitle="Python-Web-App mit Django unter Linux | Microsoft Azure" 
	description="Hier erfahren Sie, wie Sie mithilfe eines virtuellen Linux-Computers eine Django-basierte Webanwendung auf Azure hosten können." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""
	tags=“azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="huvalo"/>
	
# Django-Webanwendung "Hello World" auf einem virtuellen Linux-Computer

> [AZURE.SELECTOR]
- [Windows](virtual-machines-python-django-web-app-windows-server.md)
- [Mac/Linux](virtual-machines-python-django-web-app-linux.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Ressourcen-Manager-Modell.


In diesem Lernprogramm erfahren Sie, wie Sie eine Django-basierte Website unter Microsoft Azure mithilfe eines virtuellen Linux-Computers hosten können. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Django-basierte Anwendung, die in der Cloud ausgeführt wird.

Sie lernen Folgendes:

* Einrichten eines virtuellen Azure-Computers als Host von Django. In diesem Lernprogramm wird diese Aufgabe für **Linux** beschrieben; das Gleiche gilt jedoch auch für eine Windows Server-VM, die in Azure gehostet wird. 
* Erstellen einer neuen Django-Anwendung unter Linux.

Im Rahmen dieses Lernprogramms erstellen Sie eine einfache Webanwendung "Hello World". Die Anwendung wird auf einem virtuellen Azure-Computer gehostet.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Erstellen und Konfigurieren eines virtuellen Azure-Computers als Host von Django

1. Befolgen Sie die [hier](virtual-machines-linux-tutorial-portal-rm.md) aufgeführten Anweisungen, um einen virtuellen Azure-Computer der *Ubuntu Server 14.04 LTS*-Distribution zu erstellen. Wenn Sie dies bevorzugen, können Sie die Kennwortauthentifizierung statt eines öffentlichen SSH-Schlüssels wählen.

1. Bearbeiten Sie die Netzwerk-Sicherheitsgruppe wie in der Anleitung [hier](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) beschrieben, um eingehenden http-Datenverkehr an Port 80 zuzulassen.

1. Standardmäßig hat der neue virtuelle Computer keinen vollständig qualifizierten Domänennamen (FQDN). Sie können einen FQDN erstellen, indem Sie die Anleitung [hier](virtual-machines-create-fqdn-on-portal.md) befolgen. Dieser Schritt ist optional, um dieses Lernprogramm abzuschließen.

## <a id="setup"> </a>Einrichten der Entwicklungsumgebung

**Hinweis:** Wenn Sie Python installieren müssen oder die Clientbibliotheken verwenden möchten, finden Sie weitere Informationen im [Installationshandbuch für Python](../python-how-to-install.md).

Auf der Ubuntu Linux-VM ist Python 2.7 bereits vorab installiert; Apache oder Django sind jedoch noch nicht installiert. Befolgen Sie diese Schritte, um eine Verbindung zum virtuellen Computer herzustellen sowie um Apache und Django zu installieren.

1.  Öffnen Sie ein neues **Terminal**-Fenster.
    
1.  Geben Sie den folgenden Befehl ein, um eine Verbindung zum virtuellen Azure-Computer herzustellen. Wenn Sie keinen FQDN erstellt haben, können Sie unter Verwendung der öffentlichen IP-Adresse, die auf dem virtuellen Computer in der Zusammenfassung im klassischen Azure-Portal angezeigt wird, eine Verbindung herstellen.

		$ ssh yourusername@yourVmUrl

1.  Geben Sie den folgenden Befehl zum Installieren von Django ein.

		$ sudo apt-get install python-setuptools python-pip
		$ sudo pip install django

1.  Geben Sie den folgenden Befehl zum Installieren von Apache mit mod-wsgi ein:

		$ sudo apt-get install apache2 libapache2-mod-wsgi


## Erstellen einer neuen Django-Anwendung

1.  Öffnen Sie das **Terminal**-Fenster, das Sie im vorherigen Abschnitt zum Herstellen einer SSH-Verbindung zum virtuellen Computer verwendet haben.
    
1.  Geben Sie die folgenden Befehle ein, um ein neues Django-Projekt zu erstellen:

		$ cd /var/www
		$ sudo django-admin.py startproject helloworld

    Das Skript **django-admin.py** generiert eine grundlegende Struktur für Django-basierte Websites: - **helloworld/manage.py** erleichtert es Ihnen, das Hosten Ihrer Django-basierten Website zu starten und zu beenden. - **helloworld/helloworld/settings.py** enthält Django-Einstellungen für Ihre Anwendung. - **helloworld/helloworld/urls.py** enthält den Zuordnungscode zwischen jeder URL und deren Ansicht.

1.  Erstellen Sie eine neue Datei mit dem Namen **views.py** im Verzeichnis **/var/www/helloworld/helloworld**. Diese enthält die Ansicht, mit der die "Hello World"-Seite generiert wird. Starten Sie den Editor, und geben Sie Folgendes ein:
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Ersetzen Sie nun den Inhalt der Datei **urls.py** durch den folgenden Code:

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)


## Einrichten von Apache

1.  Erstellen Sie eine virtuelle Apache-Hostkonfigurationsdatei **/etc/apache2/sites-available/helloworld.conf**. Legen Sie den Inhalt wie folgt fest, und ersetzen Sie *yourVmName* durch die tatsächliche URL des verwendeten Computers (z. B. *pyubuntu*).

		<VirtualHost *:80>
		ServerName yourVmName
		</VirtualHost>
		WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
		WSGIPythonPath /var/www/helloworld

1.  Aktivieren Sie die Website mit dem folgenden Befehl:

        $ sudo a2ensite helloworld

1.  Starten Sie Apache mit dem folgenden Befehl neu:

        $ sudo service apache2 reload

1.  Laden Sie dann die Webseite in Ihrem Browser:

	![Ein Browserfenster, das die Seite 'Hello World' in Azure anzeigt.](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## Herunterfahren des virtuellen Azure-Computers

Wenn Sie mit diesem Lernprogramm fertig sind, fahren Sie den neu erstellten virtuellen Azure-Computer herunter, und/oder entfernen Sie diesen, sodass die Ressourcen für andere Lernprogramme zur Verfügung stehen und anfallende Kosten für die Verwendung von Azure vermieden werden.

<!---HONumber=AcomDC_1203_2015-->