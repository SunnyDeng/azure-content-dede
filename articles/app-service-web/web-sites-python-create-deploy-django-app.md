<properties
	pageTitle="Erstellen von Web-Apps mit Django in Azure"
	description="In diesem Tutorial werden Sie in die Ausführung einer Python-App-Web in Azure App Service-Web-Apps eingeführt."
	services="app-service\web"
	documentationCenter="python"
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="hero-article" 
	ms.date="11/16/2015"
	ms.author="huvalo"/>


# Erstellen von Web-Apps mit Django in Azure

In diesem Tutorial werden die ersten Schritte bei der Ausführung von Python in [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) beschrieben. Web-Apps bietet uneingeschränktes kostenloses Hosting und schnelle Bereitstellung, und Sie können Python verwenden! Wenn die Anwendung größer wird, können Sie zu kostenpflichtigem Hosting wechseln und außerdem alle anderen Azure-Dienste integrieren.

Sie erstellen eine Anwendung mit dem Webframework Django (siehe auch alternative Versionen dieses Tutorials für [Flask](web-sites-python-create-deploy-flask-app.md) und [Bottle](web-sites-python-create-deploy-bottle-app.md)). Sie erstellen die Web-App aus dem Azure Marketplace, richten die Git-Bereitstellung ein und klonen das Repository lokal. Dann führen Sie die Anwendung lokal aus, nehmen Änderungen vor, führen ein Commit aus und übertragen die Änderungen in Azure. Das Tutorial zeigt, wie dies unter Windows oder Mac/Linux erfolgt.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.


## Voraussetzungen

- Windows, Mac oder Linux
- Python 2.7 oder 3.4
- setuptools, pip, virtualenv (nur Python 2.7)
- Git
- [Python Tools für Visual Studio][] (PTVS) – Hinweis: diese sind optional

**Hinweis**: TFS-Veröffentlichung wird derzeit für Python-Projekte nicht unterstützt.

### Windows

Falls Python 2.7 oder 3.4 (32 Bit) noch nicht installiert ist, empfehlen wir die Installation von [Azure SDK für Python 2.7] oder [Azure SDK für Python 3.4] über den Webplattform-Installer. Dadurch werden die 32-Bit-Version von Python, setuptools, pip, virtualenv usw. installiert (32-Bit-Python ist auf den Azure-Hostcomputern installiert). Alternativ können Sie Python von [python.org] abrufen.

Für Git empfehlen wir [Git für Windows] oder [GitHub für Windows]. Wenn Sie Visual Studio verwenden, können Sie die integrierte Unterstützung für Git nutzen.

Wir empfehlen auch die Installation von [Python Tools 2.2 für Visual Studio]. Dies ist optional, aber wenn Sie über [Visual Studio] einschließlich des kostenlosen Visual Studio Community 2013 oder Visual Studio Express 2013 für Web verfügen, erhalten Sie eine leistungsfähige Python-IDE.

### Mac/Linux

Sie sollten Python und Git bereits installiert haben, doch stellen Sie sicher, dass Sie über Python 2.7 oder 3.4 verfügen.


## Web-App-Erstellung im Portal

Der erste Schritt beim Erstellen einer App ist das Erstellen der Web-App über das [Azure-Portal](https://portal.azure.com).

1. Klicken Sie im Azure-Portal in der unteren linken Ecke auf **NEU**.
2. Klicken Sie auf **Web und mobil**.
3. Geben Sie im Suchfeld "python" ein.
4. Wählen Sie in den Suchergebnissen **Django** aus, und klicken Sie dann auf **Erstellen**.
5. Konfigurieren Sie die neue Django-App, indem Sie beispielsweise einen neuen App Service-Plan und eine neue Ressourcengruppe dafür erstellen. Klicken Sie dann auf **Erstellen**.
6. Konfigurieren von Git-Veröffentlichung für Ihre neu erstellte Webanwendung anhand der Instruktionen unter [fortlaufende Bereitstellung mit GIT in Azure-App-Dienst](web-sites-publish-source-control.md).

## Anwendungsübersicht

### Inhalt des Git-Repositorys

Hier sehen Sie eine Übersicht der Dateien, die sich im Git-Ausgangsrepository befinden, das wir im nächsten Abschnitt klonen.

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

Die Hauptquellen für die Anwendung. Besteht aus 3 Seiten (Index, Info, Kontakt) mit einem Masterlayout. Statische Inhalte und Skripts umfassen bootstrap, jquery, modernizr und respond.

    \manage.py

Lokale Verwaltung und Unterstützung von Entwicklungsservern. Hiermit können Sie die Anwendung lokal ausführen, die Datenbank synchronisieren usw.

    \db.sqlite3

Standarddatenbank. Enthält die für die Ausführung der Anwendung erforderlichen Tabellen, aber keine Benutzer (synchronisieren Sie die Datenbank zum Erstellen eines Benutzers).

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

Projektdateien für die Verwendung mit[Python Tools für Visual Studio].

    \ptvs_virtualenv_proxy.py

IIS-Proxy für virtuelle Umgebungen und PTVS-Unterstützung für Remotedebugging.

    \requirements.txt

Externe Pakete, die von dieser Anwendung benötigt werden. Das Bereitstellungsskript installiert die in dieser Datei aufgelisteten Pakete mit pip.

    \web.2.7.config
    \web.3.4.config

IIS-Konfigurationsdateien. Das Bereitstellungsskript verwendet die entsprechende Datei "web.x.y.config" und kopiert sie als "web.config".

### Optionale Dateien - Anpassung der Bereitstellung

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### Optionale Dateien - Python-Laufzeit

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### Zusätzliche Dateien auf dem Server

Einige Dateien sind auf dem Server vorhanden, werden jedoch nicht dem Git-Repository hinzugefügt. Diese werden vom Bereitstellungsskript erstellt.

    \web.config

IIS-Konfigurationsdatei. Wird anhand von "web.x.y.config" bei jeder Bereitstellung erstellt.

    \env\

Virtuelle Umgebung für Python. Wird während der Bereitstellung erstellt, wenn nicht bereits eine kompatible virtuelle Umgebung auf der Web-App vorhanden ist. Pakete, die in "requirements.txt" aufgeführt sind, werden mit pip installiert. pip überspringt die Installation, wenn die Pakete bereits installiert sind.

In den nächsten 3 Abschnitten wird beschrieben, wie Sie die Web-App-Entwicklung in 3 unterschiedlichen Umgebungen fortsetzen:

- Windows - mit Python Tools für Visual Studio
- Windows, über die Befehlszeile
- Mac/Linux, über die Befehlszeile


## Web-App-Entwicklung – Windows – Python Tools für Visual Studio

### Klonen des Repositorys

Klonen Sie zunächst das Repository mithilfe der URL, die im Azure-Portal bereitgestellt ist. Weitere Informationen finden Sie unter [Kontinuierliche Bereitstellung mit GIT in Azure App Service](web-sites-publish-source-control.md).

Öffnen Sie die Projektmappendatei (.sln), die im Stammverzeichnis des Repositorys enthalten ist.

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### Erstellen einer virtuellen Umgebung

Jetzt erstellen wir eine virtuelle Umgebung für die lokale Entwicklung. Klicken Sie mit der rechten Maustaste auf **Python-Umgebungen**, und wählen Sie **Virtuelle Umgebung hinzufügen...** aus.

- Stellen Sie sicher, dass der Name der Umgebung `env` lautet.

- Wählen Sie den Basisinterpreter. Achten Sie darauf, die gleiche Version von Python zu verwenden, die für Ihre Web-App ausgewählt ist (in runtime.txt oder im Blatt **Anwendungseinstellungen** Ihrer Web-App im Azure-Portal).

- Stellen Sie sicher, dass die Option zum Herunterladen und Installieren von Paketen aktiviert ist.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Klicken Sie auf **Erstellen**. Dadurch wird die virtuelle Umgebung erstellt, und die in "requirements.txt" aufgelisteten Abhängigkeiten werden installiert.

### Erstellen eines Superusers

In der Datenbank, die zur Anwendung gehört, ist kein Superuser definiert. Um die Anmeldefunktionalität in der Anwendung oder die Django-Administratoroberfläche (wenn Sie sie aktivieren) verwenden zu können, müssen Sie einen Superuser erstellen.

Führen Sie den folgenden Befehl über die Befehlszeile im Projektordner aus:

    env\scripts\python manage.py createsuperuser

Befolgen Sie die Eingabeaufforderungen zum Festlegen des Benutzernamens, Kennworts usw.

### Ausführen mithilfe des Entwicklungssservers

Drücken Sie F5, um mit dem Debuggen beginnen. Ihr Webbrowser wird automatisch auf der Seite geöffnet, die lokal ausgeführt.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

Sie können Haltepunkte im Quellcode setzen, die Überwachungsfenster verwenden, usw. In der [Dokumentation zu Python Tools für Visual Studio] finden Sie weitere Informationen zu den verschiedenen Features.

### Vornehmen von Änderungen

Jetzt können Sie experimentieren, indem Sie Änderungen an der Anwendungsquellen und/oder Vorlagen vornehmen.

Nachdem Sie Ihre Änderungen getestet haben, führen Sie für diese im Git-Repository einen Commit durch:

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### Installieren weiterer Pakete

Ihre Anwendung weist möglicherweise Abhängigkeiten über Python und Django hinaus auf.

Sie können zusätzliche Pakete mit pip installieren. Klicken Sie zum Installieren eines Pakets mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **Python-Paket installieren** aus.

Geben Sie beispielsweise zum Installieren des Azure SDK für Python, das Ihnen Zugriff auf den Azure-Speicher, den Service Bus und andere Azure-Dienste ermöglicht, `azure` ein:

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Klicken Sie mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **Generate requirements.txt** aus, um die Datei "requirements.txt" zu aktualisieren.

Führen Sie anschließend für die Änderungen an "requirements.txt" einen Commit im Git-Repository aus.

### Bereitstellen in Azure

Klicken Sie auf **Sync** oder **Push**, um eine Bereitstellung auszulösen. Bei Wahl von "Synchronisierung" erfolgt sowohl ein Push- als auch ein Pull-Vorgang.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

Die erste Bereitstellung nimmt einige Zeit in Anspruch, da eine virtuelle Umgebung erstellt wird, Pakete installiert werden usw.

Visual Studio zeigt nicht den Fortschritt der Bereitstellung an. Wenn Sie die Ausgabe prüfen möchten, lesen Sie den Abschnitt [Problembehandlung - Bereitstellung](#troubleshooting-deployment).

Navigieren Sie zur Azure-URL, um die Änderungen anzuzeigen.


## Web-App-Entwicklung – Windows - Befehlszeile

### Klonen des Repositorys

Klonen Sie zunächst das Repository mithilfe der URL, die im Azure-Portal bereitgestellt ist, und fügen Sie das Azure-Repository als Remoterepository hinzu. Weitere Informationen finden Sie unter [Kontinuierliche Bereitstellung mit GIT in Azure App Service](web-sites-publish-source-control.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### Erstellen einer virtuellen Umgebung

Wir erstellen eine neue virtuelle Umgebung zu Entwicklungszwecken (die nicht dem Repository hinzugefügt werden darf). Virtuelle Umgebungen in Python sind nicht verschiebbar, sodass jeder Entwickler, der an der Anwendung arbeitet, seine eigene lokal erstellen muss.

Achten Sie darauf, die gleiche Version von Python zu verwenden, die für Ihre Web-App ausgewählt ist (in runtime.txt oder im Blatt "Anwendungseinstellungen" Ihrer Web-App im Azure-Portal).

Für Python 2.7:

    c:\python27\python.exe -m virtualenv env

Für Python 3.4:

    c:\python34\python.exe -m venv env

Installieren Sie alle externen Pakete, die Ihre Anwendung benötigt. Sie können die Datei "requirements.txt" im Stammverzeichnis des Repositorys verwenden, um die Pakete in der virtuellen Umgebung zu installieren:

    env\scripts\pip install -r requirements.txt

### Erstellen eines Superusers

In der Datenbank, die zur Anwendung gehört, ist kein Superuser definiert. Um die Anmeldefunktionalität in der Anwendung oder die Django-Administratoroberfläche (wenn Sie sie aktivieren) verwenden zu können, müssen Sie einen Superuser erstellen.

Führen Sie den folgenden Befehl über die Befehlszeile im Projektordner aus:

    env\scripts\python manage.py createsuperuser

Befolgen Sie die Eingabeaufforderungen zum Festlegen des Benutzernamens, Kennworts usw.

### Ausführen mithilfe des Entwicklungssservers

Sie können die Anwendung unter einem Entwicklungsserver mit dem folgenden Befehl starten:

    env\scripts\python manage.py runserver

Die Konsole zeigt die URL und den Port, den der Server abhört:

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

Öffnen Sie Ihren Webbrowser mit dieser URL.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### Vornehmen von Änderungen

Jetzt können Sie experimentieren, indem Sie Änderungen an der Anwendungsquellen und/oder Vorlagen vornehmen.

Nachdem Sie Ihre Änderungen getestet haben, führen Sie für diese im Git-Repository einen Commit durch:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installieren weiterer Pakete

Ihre Anwendung weist möglicherweise Abhängigkeiten über Python und Django hinaus auf.

Sie können zusätzliche Pakete mit pip installieren. Geben Sie beispielsweise zum Installieren des Azure SDK für Python, das Ihnen Zugriff auf Azure-Speicher, den Service Bus und andere Azure-Dienste bereitstellt, Folgendes ein:

    env\scripts\pip install azure

Stellen Sie sicher, dass "requirements.txt" aktualisiert wird:

    env\scripts\pip freeze > requirements.txt

Führen Sie für die Änderungen einen Commit aus:

    git add requirements.txt
    git commit -m "Added azure package"

### Bereitstellen in Azure

Um eine Bereitstellung auszulösen, übertragen Sie die Änderungen per Push in Azure:

    git push azure master

Die Ausgabe des Bereitstellungsskripts wird mit Angaben zur Erstellung der virtuellen Umgebung, Installation von Paketen und Erstellung von "web.config" angezeigt.

Navigieren Sie zur Azure-URL, um die Änderungen anzuzeigen.


## Web-App-Entwicklung – Mac/Linux - Befehlszeile

### Klonen des Repositorys

Klonen Sie zunächst das Repository mithilfe der URL, die im Azure-Portal bereitgestellt ist, und fügen Sie das Azure-Repository als Remoterepository hinzu. Weitere Informationen finden Sie unter [Kontinuierliche Bereitstellung mit GIT in Azure App Service](web-sites-publish-source-control.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### Erstellen einer virtuellen Umgebung

Wir erstellen eine neue virtuelle Umgebung zu Entwicklungszwecken (die nicht dem Repository hinzugefügt werden darf). Virtuelle Umgebungen in Python sind nicht verschiebbar, sodass jeder Entwickler, der an der Anwendung arbeitet, seine eigene lokal erstellen muss.

Achten Sie darauf, die gleiche Version von Python zu verwenden, die für Ihre Web-App ausgewählt ist (in runtime.txt oder im Blatt "Anwendungseinstellungen" Ihrer Web-App im Azure-Portal).

Für Python 2.7:

    python -m virtualenv env

Für Python 3.4:

    python -m venv env

oder

	pyvenv env

Installieren Sie alle externen Pakete, die Ihre Anwendung benötigt. Sie können die Datei "requirements.txt" im Stammverzeichnis des Repositorys verwenden, um die Pakete in der virtuellen Umgebung zu installieren:

    env/bin/pip install -r requirements.txt

### Erstellen eines Superusers

In der Datenbank, die zur Anwendung gehört, ist kein Superuser definiert. Um die Anmeldefunktionalität in der Anwendung oder die Django-Administratoroberfläche (wenn Sie sie aktivieren) verwenden zu können, müssen Sie einen Superuser erstellen.

Führen Sie den folgenden Befehl über die Befehlszeile im Projektordner aus:

    env/bin/python manage.py createsuperuser

Befolgen Sie die Eingabeaufforderungen zum Festlegen des Benutzernamens, Kennworts usw.

### Ausführen mithilfe des Entwicklungssservers

Sie können die Anwendung unter einem Entwicklungsserver mit dem folgenden Befehl starten:

    env/bin/python manage.py runserver

Die Konsole zeigt die URL und den Port, den der Server abhört:

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

Öffnen Sie Ihren Webbrowser mit dieser URL.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### Vornehmen von Änderungen

Jetzt können Sie experimentieren, indem Sie Änderungen an der Anwendungsquellen und/oder Vorlagen vornehmen.

Nachdem Sie Ihre Änderungen getestet haben, führen Sie für diese im Git-Repository einen Commit durch:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installieren weiterer Pakete

Ihre Anwendung weist möglicherweise Abhängigkeiten über Python und Django hinaus auf.

Sie können zusätzliche Pakete mit pip installieren. Geben Sie beispielsweise zum Installieren des Azure SDK für Python, das Ihnen Zugriff auf Azure-Speicher, den Service Bus und andere Azure-Dienste bereitstellt, Folgendes ein:

    env/bin/pip install azure

Stellen Sie sicher, dass "requirements.txt" aktualisiert wird:

    env/bin/pip freeze > requirements.txt

Führen Sie für die Änderungen einen Commit aus:

    git add requirements.txt
    git commit -m "Added azure package"

### Bereitstellen in Azure

Um eine Bereitstellung auszulösen, übertragen Sie die Änderungen per Push in Azure:

    git push azure master

Die Ausgabe des Bereitstellungsskripts wird mit Angaben zur Erstellung der virtuellen Umgebung, Installation von Paketen und Erstellung von "web.config" angezeigt.

Navigieren Sie zur Azure-URL, um die Änderungen anzuzeigen.


## Problembehandlung - Paketinstallation

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Problembehandlung – virtuelle Umgebung

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Problembehandlung – Statische Dateien

Django arbeitet mit dem Konzept der Sammlung statischer Dateien. Dabei werden alle statischen Dateien aus ihrem ursprünglichen Speicherort in einen zentralen Ordner kopiert. Für diese Anwendung werden sie in `/static` kopiert.

Der Grund dafür ist, dass statische Dateien aus verschiedenen Django-'Apps' stammen können. Beispielsweise befinden sich die statischen Dateien der Django-Admin-Oberflächen in einem Unterordner der Django-Bibliothek in der virtuellen Umgebung. Statische Dateien, die von dieser Anwendung definiert werden, befinden sich in `/app/static`. Sobald Sie weitere Django-'Apps nutzen, verfügen Sie über statische Dateien, die sich an mehreren Stellen befinden.

Wenn die Anwendung im Debugmodus ausgeführt wird, stellt die Anwendung statischen Dateien an ihrem ursprünglichen Speicherort bereit.

Wenn die Anwendung im Freigabemodus ausgeführt wird, stellt die Anwendung die statischen Dateien **nicht** bereit. Der Webserver ist dafür zuständig, dass die Dateien bereitgestellt werden. Für diese Anwendung stellt IIS die statischen Dateien aus `/static` bereit.

Die Sammlung statischer Dateien erfolgt automatisch als Teil des Bereitstellungsskripts, wobei zuvor gesammelte Dateien gelöscht werden. Dies bedeutet, dass die Sammlung bei jeder Bereitstellung erfolgt, was die Bereitstellung verlangsamt, aber sicherstellt, dass veraltete Dateien nicht verfügbar sind. Dadurch vermeiden Sie ein potenzielles Sicherheitsproblem.

Wenn Sie die Sammlung statischer Dateien für die Django-Anwendung zu überspringen möchten:

    \.skipDjango

Dann müssen Sie die Sammlung manuell auf dem lokalen Computer ausführen:

    env\scripts\python manage.py collectstatic

Entfernen Sie anschließend den Ordner `\static` aus `.gitignore`, und fügen Sie ihn dem Git-Repository hinzu.


## Problembehandlung - Einstellungen

Verschiedene Einstellungen für die Anwendung können in `DjangoWebProject/settings.py` geändert werden.

Der Einfachheit halber ist für Entwickler der Debugmodus aktiviert. Ein angenehmer Nebeneffekt davon ist, dass bei einer lokalen Ausführung Bilder und andere statische Inhalte angezeigt werden, ohne statische Dateien sammeln zu müssen.

So deaktivieren Sie den Debugmodus:

    DEBUG = False

Wenn der Debugmodus deaktiviert ist, muss der Wert für `ALLOWED_HOSTS` so aktualisiert werden, dass er den Azure-Hostnamen enthält. Zum Beispiel:

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

Geben Sie zum Aktivieren Folgendes ein:

    ALLOWED_HOSTS = (
        '*',
    )

In der Praxis wird wohl eher eine komplexere Lösung für das Wechseln zwischen Debug- und Freigabemodus und den Abruf des Hostnamens gewünscht.

Sie können im Azure-Portal auf der Seite **KONFIGURIEREN** im Abschnitt **App-Einstellungen** Umgebungsvariablen festlegen. Dies kann zum Festlegen von Werten nützlich sein, die möglicherweise nicht in den Quellen (Verbindungszeichenfolgen, Kennwörter usw.) angezeigt werden sollen, oder die Sie in Azure und auf Ihrem lokalen Computer anders festlegen möchten. In `settings.py` können Sie die Umgebungsvariablen mit `os.getenv` abfragen.


## Verwenden einer Datenbank

Die Datenbank, die in der Anwendung enthalten ist, ist eine Sqlite-Datenbank. Diese Standarddatenbank ist besonders gut für die Entwicklung geeignet, da sie keinerlei Einrichtung erfordert. Die Datenbank wird in der Datei "sqlite3" im Projektordner gespeichert.

Azure bietet Datenbankdienste, die in einer Django-Anwendung einfach zu verwenden sind. Tutorial für die Verwendung von [SQL-Datenbank] und [MySQL] in einer Django-Anwendung zeigen, welche Schritte notwendig sind, um den Datenbankdienst zu erstellen, die Datenbankeinstellungen in `DjangoWebProject/settings.py` zu ändern und die Bibliotheken zu installieren.

Wenn Sie Ihre eigenen Datenbankserver verwalten möchten, können Sie dazu virtuelle Windows- oder Linux-Computer verwenden, die in Azure ausgeführt werden.


## Django-Administratoroberfläche

Beim Erstellen von Modellen wollen Sie bestimmt die Datenbank mit Daten auffüllen. Eine einfache Möglichkeit zum Hinzufügen und interaktiven Bearbeiten von Inhalten ist die Verwendung der Django-Administratoroberfläche.

Der Code für die Admin-Schnittstelle ist in den Anwendungsquellen auskommentiert, aber so deutlich markiert, dass Sie ihn problemlos aktivieren können (suchen Sie nach 'admin').

Nach seiner Aktivierung synchronisieren Sie die Datenbank, führen die Anwendung aus und navigieren zu `/admin`.


## Nächste Schritte

Folgen Sie diesen Links, um weitere Informationen zu Django- und Python-Tools für Visual Studio zu erhalten:

- [Dokumentation zu Django]
- [Python Tools für Visual Studio – Dokumentation]

Weitere Informationen zur Verwendung von SQL-Datenbank und MySQL:

- [Django und MySQL in Azure mit Python Tools für Visual Studio]
- [Django und SQL-Datenbank in Azure mit Python Tools für Visual Studio]

Weitere Informationen finden Sie im [Python Developer Center](/develop/python/).


## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).


<!--Link references-->
[Django und MySQL in Azure mit Python Tools für Visual Studio]: web-sites-python-ptvs-django-mysql.md
[Django und SQL-Datenbank in Azure mit Python Tools für Visual Studio]: web-sites-python-ptvs-django-sql.md
[SQL-Datenbank]: web-sites-python-ptvs-django-sql.md
[MySQL]: web-sites-python-ptvs-django-mysql.md

<!--External Link references-->
[Azure SDK für Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK für Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git für Windows]: http://msysgit.github.io/
[GitHub für Windows]: https://windows.github.com/
[Python Tools für Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Dokumentation zu Python Tools für Visual Studio]: http://aka.ms/ptvsdocs
[Python Tools für Visual Studio – Dokumentation]: http://aka.ms/ptvsdocs
[Dokumentation zu Django]: https://www.djangoproject.com/

<!---HONumber=AcomDC_1203_2015-->