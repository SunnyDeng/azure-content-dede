<properties linkid="develop-python-web-app-with-django-and-mysql" urlDisplayName="Web with Django + MySQL" pageTitle="Python web app with Django and MySQL - Azure tutorial" metaKeywords="Azure django web app, Azure Django MySQL, Azure django Python" description="A tutorial that teaches you how to use MySQL in with Django on an Azure virtual machine. Code samples written in Python." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World - MySQL Windows Edition" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Django Hello World - MySQL Windows-Edition

In diesem Lernprogramm wird beschrieben, wie MySQL zusammen mit Django auf einem einzelnen virtuellen Azure-Computer verwendet wird. Es wird davon ausgegangen, dass Sie bereits Erfahrung mit der Verwendung von Azure und Django haben. Eine Einführung in Azure und Django finden Sie unter [Django Hello World][Django Hello World]. In diesem Leitfaden wird ebenfalls davon ausgegangen, dass Sie bereits Erfahrung mit der Verwendung von MySQL haben. Eine Übersicht über MySQL finden Sie auf der [MySQL-Website][MySQL-Website].

In diesem Lernprogramm lernen Sie Folgendes:

-   Einrichten eines virtuellen Azure-Computers, auf dem MySQL und Django gehostet werden In diesem Lernprogramm wird die Durchführung dieser Aufgabe für Windows Server 2008 R2 beschrieben. Diesen Vorgang können Sie aber auch mit einem virtuellen Linux-Computer auf Azure ausführen.
-   Installieren eines [MySQL-Treibers][MySQL-Treibers] für Python
-   Konfigurieren einer vorhandenen Django-Anwendung zum Verwenden einer MySQL-Datenbank
-   Verwenden von MySQL direkt aus Python
-   Hosten und Ausführen der MySQL Django-Anwendung

Erweitern Sie das [Django Hello World][Django Hello World]-Beispiel durch Verwenden einer MySQL-Datenbank, die auf einem virtuellen Azure-Computer gehostet wird, sodass Sie einen interessanten Ersatz für *World* finden. Der Ersatz wird wiederum über eine MySQL-unterstützte Django *counter*-App festgelegt. Wie auch beim Hello World-Beispiel wird diese Django-Anwendung auf einem virtuellen Azure-Computer gehostet.

Die Projektdateien für dieses Lernprogramm werden im Verzeichnis **C:\\django\\helloworld** gespeichert, und die fertige Anwendung sieht etwa wie folgt aus:

![][0]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Einrichten eines virtuellen Computers, auf dem MySQL und Django gehostet werden

1.  Folgen Sie den [hier][hier] aufgeführten Anweisungen, um einen virtuellen Azure-Computer der *Windows Server 2008 R2*-Distribution zu erstellen.

2.  Öffnen Sie einen TCP-Port für MySQL-Transaktionen auf dem virtuellen Computer:

	-   Navigieren Sie im Azure-Portal zu Ihrem neu erstellten virtuellen Computer, und klicken Sie auf die Registerkarte *ENDPUNKTE*.
	-   Klicken Sie am unteren Bildschirmrand auf die Schaltfläche *ENDPUNKT HINZUFÜGEN*.
	-   Fügen Sie einen Endpunkt hinzu: NAME = **mysql**, PROTOKOLL = **TCP**, ÖFFENTLICHER PORT = **3306**, PRIVATER PORT = **3306**.

1.  Fügen Sie einen weiteren Endpunkt hinzu: NAME = **web**, PROTOKOLL = **TCP**, ÖFFENTLICHER PORT = **80**, PRIVATER PORT = **80**. Auf diese Weise werden externe Internetanforderungen zu dem Port umgeleitet, auf dem Django ausgeführt wird, nämlich *80*.

2.  Verwenden Sie Windows *Remotedesktop*, um sich von einem Remotestandort aus am neu erstellten virtuellen Azure-Computer anzumelden.

3.  Öffnen Sie den TCP-Port **80** auf dem virtuellen Computer:

	-   Wählen Sie im Menü **Start** die Option **Administratortools** und anschließend **Windows-Firewall mit erweiterter Sicherheit**.
	-   Klicken Sie im linken Bereich auf **Eingehende Regeln**. Wählen Sie rechts im Bereich **Aktionen** die Option **Neue Regel...**.
	-   Klicken Sie unter **Assistent für neue eingehende Regel** auf **Port** und anschließend auf **Weiter**.
	-   Wählen Sie **TCP** und anschließend **Bestimmte lokale Ports**. Geben Sie als Port "80" ein (den Port, auf dem Django lauscht), und klicken Sie auf **Weiter**.
	-   Klicken Sie auf **Verbindung zulassen** und anschließend auf **Weiter**.
	-   Klicken Sie erneut auf **Weiter**.
	-   Geben Sie einen Namen für die Regel an, z. B. "DjangoPort", und klicken Sie auf "Fertig stellen".

1.  Installieren Sie die neueste Version von [MySQL Community Server][MySQL Community Server] für Windows auf dem virtuellen Computer:

    **Hinweis**: Wir empfehlen, die Datenbank auf einer Datenpartition zu installieren, auf der das Betriebssystem nicht vorhanden ist.

	-   Führen Sie den [hier][MySQL Community Server] verfügbaren Link für das *Windows (x86, 64-Bit)-MSI-Installationsprogramm* aus, und laden Sie das entsprechende MSI-Installationsprogramm vom nächstgelegenen Downloadserver herunter. Nützliche Tipps:

    	-   Wählen Sie den Installationstyp *Vollständig* aus.
    	-   Wählen Sie im Konfigurationsassistenten *Detaillierte Konfiguration* aus.
    	-   **Vergewissern Sie sich, dass Sie das TCP/IP-Netzwerk für die Portnummer 3306 aktivieren. Richten Sie außerdem eine Firewallausnahme für den Port ein.**
    	-   Legen Sie ein Stammkennwort fest, und ermöglichen Sie den Stammzugriff von Remotecomputern.
	-   Installieren Sie eine beispielhafte ["world"-Datenbank]["world"-Datenbank] (MyISAM-Version):
    	-   Laden Sie [diese][diese] Zip-Datei auf den virtuellen Azure-Computer herunter.
    	-   **Entzippen Sie die Datei in das Verzeichnis *C:\\Benutzer\\Administrator\\Desktop\\world.sql*.**

1.  Nachdem Sie MySQL installiert haben, klicken Sie auf das Windows-Menü *Start*, und führen Sie den neu installierten *MySQL 5.5-Befehlszeilenclient* aus. Geben Sie die folgenden Befehle ein:

        CREATE DATABASE world;
        USE world;
        SOURCE C:\Users\Administrator\Desktop\world.sql
        CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
        CREATE DATABASE djangoazure;
        GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
        GRANT ALL ON world.* TO 'testazureuser'@'%';
        SELECT name from country LIMIT 1;

	Daraufhin sollte eine Antwort angezeigt werden, die etwa wie folgt aussieht:

  ![][2]

1.  Bevor Sie mit der Entwicklung Ihrer Django-Anwendung beginnen können, müssen Sie zunächst Python+Django auf dem virtuellen Computer installieren. Sie können dafür den [Webplattform-Installer][Webplattform-Installer] verwenden. Nachdem Sie den Webplattform-Installer eingerichtet haben, können Sie mit dessen Hilfe nach "Django" suchen und das Django (Python)-Produkt installieren.

	**Hinweis:** Sie müssen lediglich das *Django*-Produkt über WebPI installieren, damit dieses Lernprogramm zur Verfügung steht. Die Installation von *Python-Tools für Visual Studio* oder Azure Python SDK ist für unsere Zwecke **nicht** erforderlich.

1.  Installieren Sie das MySQL Python-Clientpaket. [Über diesen Link][Über diesen Link] können Sie das Paket direkt installieren. Führen Sie nach Abschluss den folgenden Befehl aus, um die Installation zu überprüfen:

  ![][1]

## Erweitern der Django Hello World-Anwendung

1.  Befolgen Sie die im Lernprogramm [Django Hello World][Django Hello World] aufgeführten Anweisungen, um eine einfache "Hello World"-Webanwendung in Django zu erstellen.

2.  Öffnen Sie **C:\\django\\helloworld\\helloworld\\settings.py** im Texteditor Ihrer Wahl. Ändern Sie das globale Wörterbuch **DATABASES**, sodass dort Folgendes steht:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'djangoazure',               
                'USER': 'testazureuser',  
                'PASSWORD': 'testazure',
                'HOST': '127.0.0.1', 
                'PORT': '3306',
                },
            'world': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'world',               
                'USER': 'testazureuser',  
                'PASSWORD': 'testazure',
                'HOST': '127.0.0.1', 
                'PORT': '3306',
                }
            }

Wie Sie sehen, wurden Django lediglich Anweisungen gegeben, wo die MySQL-Datenbank gesucht werden soll.

**Hinweis:** Sie **müssen** den *HOST*schlüssel so ändern, dass dieser mit der **permanenten** IP-Adresse des virtuellen Azure (MySQL)-Computers übereinstimmt. An dieser Stelle sollte *HOST* auf die Einstellung festgelegt werden, die vom Windows-Befehl *ipconfig* angegeben wird.

Nachdem Sie die Einstellung für *HOST* so geändert haben, dass diese mit der IP-Adresse des virtuellen MySQL-Computers übereinstimmt, speichern Sie diese Datei, und schließen Sie sie dann.

1.  Da wir nun unsere *djangoazure*-Datenbank referenziert haben, können wir diese endlich nutzen. Dazu erstellen wir ein Modell für eine einfache *counter*-App. Führen Sie die folgenden Befehle aus, sodass Django zur Erstellung dieses Modells angewiesen wird:

        cd C:\django\helloworld
        C:\Python27\python.exe manage.py startapp counter

Falls Django unter Verwendung des obigen Befehls keine Ausgabe mitteilt, wurde die Aufgabe erfolgreich durchgeführt.

1.  Hängen Sie den folgenden Text an die Datei **C:\\django\\helloworld\\counter\\models.py** an:

        class Counter(models.Model):
            count = models.IntegerField()
            def __unicode__(self):
                return u'%s' % (self.count)

Wir haben hier folglich eine Unterklasse der Django-*Modell*klasse mit der Bezeichnung *Counter* und einem einzigen Ganzzahlfeld, *count*, definiert. Dieses einfache Zählermodell dient letztendlich dazu, die Anzahl der Treffer für unsere Django-Anwendung zu erfassen.

1.  Als Nächstes machen wir Django bewusst, dass *Counter* vorhanden ist:

-   Bearbeiten Sie die Datei **C:\\django\\helloworld\\helloworld\\settings.py** erneut. Fügen Sie *"counter"* zum Tupel *INSTALLED\_APPS* hinzu.
-   Geben Sie an der Eingabeaufforderung Folgendes ein:

            cd C:\django\helloworld
            C:\Python27\python manage.py sql counter
            C:\Python27\python manage.py syncdb

    Mithilfe dieser Befehle wird das *Counter*-Modell in der aktiven Django-Datenbank gespeichert und eine Ausgabe ähnlich der folgenden wird angezeigt:

        C:\django\helloworld> C:\Python27\python manage.py sql counter
        BEGIN;
        CREATE TABLE `counter_counter` (
            `id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY,
            `count` integer NOT NULL
        )
        ;
        COMMIT;

        C:\django\helloworld> C:\Python27\python manage.py syncdb
        Creating tables ...
        Creating table auth_permission
        Creating table auth_group_permissions
        Creating table auth_group
        Creating table auth_user_user_permissions
        Creating table auth_user_groups
        Creating table auth_user
        Creating table django_content_type
        Creating table django_session
        Creating table django_site
        Creating table counter_counter

        You just installed Django's auth system, which means you don't have any superusers defined.
        Would you like to create one now? (yes/no): no
        Installing custom SQL ...
        Installing indexes ...
        Installed 0 object(s) from 0 fixture(s)

1.  Ersetzen Sie den Inhalt der Datei **C:\\django\\helloworld\\helloworld\\views.py**. Bei der neuen Implementierung der *hello*-Funktion weiter unten wird unser *Counter*-Modell zusammen mit der zuvor installierten separaten Beispieldatenbank *world* verwendet, sodass ein geeigneter Ersatz für die Zeichenfolge "*World*" erstellt wird:

        from django.http import HttpResponse
        import django.db
        from counter.models import Counter

        def getCountry(intId):
            #Connect to the MySQL sample database 'world'
            cur = django.db.connections['world'].cursor()
            #Execute a trivial SQL query which returns the name of 
            #all countries contained in 'world'
            cur.execute("SELECT name from country")
            tmp = cur.fetchall()
            #Clean-up after ourselves
            cur.close()
            if intId >= len(tmp):
                return "countries exhausted"
            return tmp[intId][0]

        def hello(request):
            if len(Counter.objects.all())==0:
                #when the database corresponding to 'helloworld.counter' is 
                #initially empty...
                c = Counter(count=0)
            else:
                c = Counter.objects.all()[0]
                c.count += 1
            c.save()       
            world = getCountry(int(c.count))
            return HttpResponse("<html><body>Hello <em>" + world + "</em></body></html>")

## Bereitstellen und Ausführen der Django-Website

Hinweis: Im Folgenden ist dargestellt, wie Sie Django in einer Testumgebung ausführen. Wenn Sie Django in Produktion ausführen möchten, befolgen Sie die Anweisungen im Abschnitt "Einrichten von IIS mit FastCGI" des Lernprogramms "Django Hello World". Der Windows-Firewall-Client zum Öffnen des Ports 80 für den Internetdatenverkehr auf dem virtuellen Windows Server 2K8 R2-Computer ist bei FastCGI nicht erforderlich.

1.  Wechseln Sie zurück zu einem Windows PowerShell-Fenster, und geben Sie die folgenden Befehle ein, um die Django-Website öffentlich bereitzustellen:

        PS C:\django\helloworld> $ipPort = 
        PS C:\django\helloworld> $ipPort = [string]$ipPort.AddressList[1]
        PS C:\django\helloworld> $ipPort += ":80"
        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort

    Der Parameter **runserver** weist Django an, unsere *helloworld*-Website auf dem TCP-Port *80* auszuführen. Die Ergebnisse dieses ausgeführten Befehls sehen etwa folgendermaßen aus:

        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort
        Validating models...

        0 errors found
        Django version 1.4, using settings 'helloworld.settings'
        Development server is running at http://123.34.56.78:80
        Quit the server with CTRL-BREAK.

2.  Öffnen Sie in Ihrem lokalen Webbrowser **http://*yourVmName*.cloudapp.net** (wobei *yourVmName* für den Namen steht, den Sie bei der Erstellung des virtuellen Computers verwendet haben). In der Anzeige sollte "Hello ... !" erscheinen (siehe Screenshot weiter unten). Dies bedeutet, dass Django auf dem virtuellen Computer ausgeführt wird und ordnungsgemäß funktioniert.

    ![][5]

	Aktualisieren Sie den Webbrowser mehrmals, sodass die Meldung von *"Hello **\<Land abc\>**"* in *"Hello **\<anderes Land\>**"* geändert wird.

1.  Damit die Website nicht länger von Django gehostet wird, wechseln Sie einfach zum PowerShell-Fenster, und drücken Sie **STRG+C**.

## Herunterfahren des virtuellen Azure-Computers

Wenn Sie mit diesem Lernprogramm fertig sind, fahren Sie den neu erstellten virtuellen Azure-Computer herunter, und/oder entfernen Sie diesen, sodass die Ressourcen für andere Lernprogramme zur Verfügung stehen und anfallende Kosten für die Verwendung von Azure vermieden werden.

  [Django Hello World]: http://windowsazure.com/de-de/documentation/articles/virtual-machines-python-django-web-app-windows-server
  [MySQL-Website]: http://dev.mysql.com/doc/
  [MySQL-Treibers]: http://pypi.python.org/pypi/MySQL-python/1.2.3
  [hier]: /de-de/manage/windows/tutorials/virtual-machine-from-gallery/
  [MySQL Community Server]: http://dev.mysql.com/downloads/mysql/
  ["world"-Datenbank]: http://dev.mysql.com/doc/index-other.html
  [diese]: http://downloads.mysql.com/docs/world.sql.zip
  [Webplattform-Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Über diesen Link]: http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=



[0]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png
[1]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png
[2]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png
[5]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png