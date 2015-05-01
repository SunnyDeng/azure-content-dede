<properties 
	pageTitle="Python-Websites mit Flask - Azure-Lernprogramm" 
	description="In diesem Lernprogramm werden Sie in die Ausführung einer Python-Website in Azure eingeführt."
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
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Erstellen von Websites mit Flask

In diesem Lernprogramm werden die ersten Schritte bei der Ausführung von Python auf Azure-Websites beschrieben.  Azure-Websites bieten uneingeschränktes kostenloses Hosting und schnelle Bereitstellung, und Sie können Python verwenden!  Wenn die Anwendung größer wird, können Sie zu kostenpflichtigem Hosting wechseln und außerdem alle anderen Azure-Dienste integrieren.

Sie erstellen eine Anwendung mit dem Webframework Flask (siehe auch alternative Versionen dieses Lernprogramms für [Django](web-sites-python-create-deploy-django-app.md) und [Bottle](web-sites-python-create-deploy-bottle-app.md)).  Sie erstellen die Website aus dem Azure-Katalog, richten die Git-Bereitstellung ein und klonen das Repository lokal.  Dann führen Sie die Anwendung lokal aus, nehmen Änderungen vor, führen ein Commit aus und übertragen die Änderungen in Azure.  Das Lernprogramm zeigt, wie dies unter Windows oder Mac/Linux erfolgt.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


## Voraussetzungen

- Windows, Mac oder Linux
- Python 2.7 oder 3.4
- setuptools, pip, virtualenv (nur Python 2.7)
- Git
- [Python Tools 2.1 für Visual Studio][] (optional)

**Hinweis**: TFS-Veröffentlichung wird derzeit für Python-Projekte nicht unterstützt.

### Windows

Falls Python 2.7 oder 3.4 (32 Bit) noch nicht installiert ist, empfehlen wir die Installation von [Azure SDK für Python 2.7][] oder [Azure SDK für Python 3.4][] über den Webplattform-Installer.  Dadurch werden die 32-Bit-Version von Python, setuptools, pip, virtualenv usw. installiert (32-Bit-Python ist auf den Azure-Hostcomputern installiert).  Alternativ können Sie Python von [python.org][] abrufen.

Für Git empfehlen wir [Git für Windows][] oder [GitHub für Windows][].  Wenn Sie Visual Studio verwenden, können Sie die integrierte Unterstützung für Git nutzen.

Wir empfehlen auch die Installation von [Python Tools 2.1 für Visual Studio][].  Dies ist optional, aber wenn Sie über [Visual Studio][] einschließlich des kostenlosen isual Studio Community 2013 oder Visual Studio Express 2013 for Web verfügen, erhalten Sie eine leistungsfähige Python-IDE.

### Mac/Linux

Sie sollten Python und Git bereits installiert haben, doch stellen Sie sicher, dass Sie über Python 2.7 oder 3.4 verfügen.


## Erstellen von Websites im Portal

Der erste Schritt beim Erstellen einer App ist das Erstellen der Website im Azure-Verwaltungsportal.  Melden Sie sich dazu beim Portal an, und klicken Sie unten links auf die Schaltfläche **NEU**. Daraufhin wird ein Fenster angezeigt. Klicken Sie auf **SERVER**, **WEBSITE**, **AUS KATALOG**.

![](./media/web-sites-python-create-deploy-flask-app/portal-create-site.png)

Ein Fenster wird angezeigt, in dem Apps aufgeführt werden, die im Katalog verfügbar sind. Klicken Sie links auf die Kategorie **APP FRAMEWORKS**, und wählen Sie **Flask** aus.

![](./media/web-sites-python-create-deploy-flask-app/portal-gallery-flask.png)

Geben Sie auf der nächsten Seite einen Namen und eine Region für Ihre Website ein, und klicken Sie auf die Schaltfläche "Fertig".

Die Website wird umgehend eingerichtet. Wenn Sie auf die Schaltfläche**Durchsuchen** auf der unteren Symbolleiste klicken, sehen Sie, dass Ihre neue Flask-Anwendung in Azure ausgeführt wird.

![](./media/web-sites-python-create-deploy-flask-app/portal-website-flask.png)
 
Nun fügen Sie Unterstützung für die Veröffentlichung mit Git hinzu.  Dazu wählen Sie **Bereitstellung über Quellcodeverwaltung einrichten** aus.

![](./media/web-sites-python-create-deploy-flask-app/portal-site-created.png)

Führen Sie im Dialogfeld **Bereitstellung einrichten** einen Bildlauf nach unten durch, und wählen Sie die Option **Lokales Git-Repository** aus. Klicken Sie auf den Pfeil nach rechts, um fortzufahren.

![](./media/web-sites-python-create-deploy-flask-app/portal-setup-deployment.png)

Nach dem Einrichten der Git-Veröffentlichung wird kurz eine Seite mit der Information zur Repository-Erstellung angezeigt. Nach der Erstellung sehen Sie die Anweisungen zum Herstellen einer Verbindung.  

![](./media/web-sites-python-create-deploy-flask-app/portal-repo-created.png)

Wir folgen diesen Anweisungen in den nächsten Abschnitten.


## Anwendungsübersicht

### Inhalt des Git-Repositorys

Hier sehen Sie eine Übersicht der Dateien, die sich im Git-Ausgangsrepository befinden, das wir im nächsten Abschnitt klonen.

    \FlaskWebProject\__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

Die Hauptquellen für die Anwendung.  Besteht aus 3 Seiten (Index, Info, Kontakt) mit einem Masterlayout.  Statische Inhalte und Skripts umfassen bootstrap, jquery, modernizr und respond.

    \runserver.py

Unterstützung für lokale Entwicklungsserver. Dient zum lokalen Ausführen der Anwendung.

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

Projektdateien für die Verwendung mit [Python Tools für Visual Studio](http://pytools.codeplex.com).

    \ptvs_virtualenv_proxy.py

IIS-Proxy für virtuelle Umgebungen und PTVS-Unterstützung für Remotedebugging.

    \requirements.txt

Externe Pakete, die von dieser Anwendung benötigt werden. Das Bereitstellungsskript installiert die in dieser Datei aufgelisteten Pakete mit pip.
 
    \web.2.7.config
    \web.3.4.config

IIS-Konfigurationsdateien.  Das Bereitstellungsskript verwendet die entsprechende Datei "web.x.y.config" und kopiert sie als "web.config".

### Optionale Dateien - Anpassung der Bereitstellung

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### Optionale Dateien - Python-Laufzeit

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### Zusätzliche Dateien auf dem Server

Einige Dateien sind auf dem Server vorhanden, werden jedoch nicht dem Git-Repository hinzugefügt.  Diese werden vom Bereitstellungsskript erstellt.

    \web.config

IIS-Konfigurationsdatei.  Wird anhand von "web.x.y.config" bei jeder Bereitstellung erstellt.

    \env\

Virtuelle Umgebung für Python.  Wird während der Bereitstellung erstellt, wenn nicht bereits eine kompatible virtuelle Umgebung auf der Website vorhanden ist.  Pakete, die in "requirements.txt" aufgeführt sind, werden mit pip installiert. pip überspringt die Installation, wenn die Pakete bereits installiert sind.

In den nächsten drei Abschnitten wird beschrieben, wie Sie die Websiteentwicklung in 3 unterschiedlichen Umgebungen fortsetzen:

- Windows - mit Python Tools für Visual Studio
- Windows, über die Befehlszeile
- Mac/Linux, über die Befehlszeile


## Website-Entwicklung - Windows - Python Tools für Visual Studio

### Klonen des Repositorys

Klonen Sie zunächst das Repository mithilfe der URL, die im Azure-Portal bereitgestellt ist.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-clone.png)

Öffnen Sie die Projektmappendatei (.sln), die im Stammverzeichnis des Repositorys enthalten ist.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### Erstellen einer virtuellen Umgebung

Jetzt erstellen wir eine virtuelle Umgebung für die lokale Entwicklung.  Klicken Sie mit der rechten Maustaste auf **Python-Umgebungen**, und wählen Sie **Virtuelle Umgebung hinzufügen...** aus.

- Stellen Sie sicher, dass der Name der Umgebung  `env` lautet.

- Wählen Sie den Basisinterpreter.  Stellen Sie sicher, dass Sie die gleiche Version von Python verwenden, die für Ihre Website (in "runtime.txt" oder der Seite zum Konfigurieren der Website) ausgewählt ist.

- Stellen Sie sicher, dass die Option zum Herunterladen und Installieren von Paketen aktiviert ist.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Klicken Sie auf **Erstellen**.  Dadurch wird die virtuelle Umgebung erstellt, und die in "requirements.txt" aufgelisteten Abhängigkeiten werden installiert.

### Ausführen mithilfe des Entwicklungssservers

Drücken Sie F5, um mit dem Debuggen beginnen. Ihr Webbrowser wird automatisch auf der Seite geöffnet, die lokal ausgeführt.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

Sie können Haltepunkte im Quellcode setzen, die Überwachungsfenster verwenden usw. In der [PTVS-Dokumentation][] finden Sie weitere Informationen zu den verschiedenen Features.

### Vornehmen von Änderungen

Jetzt können Sie experimentieren, indem Sie Änderungen an der Anwendungsquellen und/oder Vorlagen vornehmen.

Nachdem Sie Ihre Änderungen getestet haben, führen Sie für diese im Git-Repository einen Commit durch:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### Installieren weiterer Pakete

Ihre Anwendung weist möglicherweise Abhängigkeiten über Python und Flask hinaus auf.

Sie können zusätzliche Pakete mit pip installieren.  Klicken Sie zum Installieren eines Pakets mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **Python-Paket installieren**.

Geben Sie beispielsweise zum Installieren des Azure SDK für Python, das Ihnen Zugriff auf Azure-Speicher, den Service Bus und andere Azure-Dienste bereitstellt, Folgendes ein  `azure`:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Klicken Sie mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **"requirements.txt" generieren**, um die Datei "requirements.txt" zu aktualisieren.

Führen Sie anschließend für die Änderungen an "requirements.txt" einen Commit im Git-Repository aus.

### Bereitstellen in Azure

Klicken Sie auf, **Synchronisierung** oder **Push**, um eine Bereitstellung auszulösen.  Bei Wahl von "Synchronisierung" erfolgt sowohl ein Push- als auch ein Pull-Vorgang.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

Die erste Bereitstellung nimmt einige Zeit in Anspruch, da eine virtuelle Umgebung erstellt wird, Pakete installiert werden usw.

Visual Studio zeigt nicht den Fortschritt der Bereitstellung an.  Wenn Sie die Ausgabe prüfen möchten, lesen Sie den Abschnitt [Problembehandlung - Bereitstellung](#troubleshooting-deployment).

Navigieren Sie zur Azure-URL, um die Änderungen anzuzeigen.


## Website-Entwicklung - Windows - Befehlszeile

### Klonen des Repositorys

Klonen Sie zunächst das Repository mithilfe der URL, die im Azure-Portal bereitgestellt ist, und fügen Sie das Azure-Repository als Remoterepository hinzu.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Erstellen einer virtuellen Umgebung

Wir erstellen eine neue virtuelle Umgebung zu Entwicklungszwecken (die nicht dem Repository hinzugefügt werden darf).  Virtuelle Umgebungen in Python sind nicht verschiebbar, sodass jeder Entwickler, der an der Anwendung arbeitet, seine eigene lokal erstellen muss.

Stellen Sie sicher, dass Sie die gleiche Version von Python verwenden, die für Ihre Website (in "runtime.txt" oder der Seite zum Konfigurieren der Website) ausgewählt ist.

Für Python 2.7:

    c:\python27\python.exe -m virtualenv env

Für Python 3.4:

    c:\python34\python.exe -m venv env

Installieren Sie alle externen Pakete, die Ihre Anwendung benötigt. Sie können die Datei "requirements.txt" im Stammverzeichnis des Repositorys verwenden, um die Pakete in der virtuellen Umgebung zu installieren:

    env\scripts\pip install -r requirements.txt

### Ausführen mithilfe des Entwicklungssservers

Sie können die Anwendung unter einem Entwicklungsserver mit dem folgenden Befehl starten:

    env\scripts\python runserver.py

Die Konsole zeigt die URL und den Port, den der Server abhört:

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

Öffnen Sie Ihren Webbrowser mit dieser URL.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### Vornehmen von Änderungen

Jetzt können Sie experimentieren, indem Sie Änderungen an der Anwendungsquellen und/oder Vorlagen vornehmen.

Nachdem Sie Ihre Änderungen getestet haben, führen Sie für diese im Git-Repository einen Commit durch:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installieren weiterer Pakete

Ihre Anwendung weist möglicherweise Abhängigkeiten über Python und Flask hinaus auf.

Sie können zusätzliche Pakete mit pip installieren.  Geben Sie beispielsweise zum Installieren des Azure SDK für Python, das Ihnen Zugriff auf Azure-Speicher, den Service Bus und andere Azure-Dienste bereitstellt, Folgendes ein:

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


## Website-Entwicklung - Mac/Linux - Befehlszeile

### Klonen des Repositorys

Klonen Sie zunächst das Repository mithilfe der URL, die im Azure-Portal bereitgestellt ist, und fügen Sie das Azure-Repository als Remoterepository hinzu.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Erstellen einer virtuellen Umgebung

Wir erstellen eine neue virtuelle Umgebung zu Entwicklungszwecken (die nicht dem Repository hinzugefügt werden darf).  Virtuelle Umgebungen in Python sind nicht verschiebbar, sodass jeder Entwickler, der an der Anwendung arbeitet, seine eigene lokal erstellen muss.

Stellen Sie sicher, dass Sie die gleiche Version von Python verwenden, die für Ihre Website (in "runtime.txt" oder der Seite zum Konfigurieren der Website) ausgewählt ist.

Für Python 2.7:

    python -m virtualenv env

Für Python 3.4:

    python -m venv env

Installieren Sie alle externen Pakete, die Ihre Anwendung benötigt. Sie können die Datei "requirements.txt" im Stammverzeichnis des Repositorys verwenden, um die Pakete in der virtuellen Umgebung zu installieren:

    env/bin/pip install -r requirements.txt

### Ausführen mithilfe des Entwicklungssservers

Sie können die Anwendung unter einem Entwicklungsserver mit dem folgenden Befehl starten:

    env/bin/python runserver.py

Die Konsole zeigt die URL und den Port, den der Server abhört:

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

Öffnen Sie Ihren Webbrowser mit dieser URL.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### Vornehmen von Änderungen

Jetzt können Sie experimentieren, indem Sie Änderungen an der Anwendungsquellen und/oder Vorlagen vornehmen.

Nachdem Sie Ihre Änderungen getestet haben, führen Sie für diese im Git-Repository einen Commit durch:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installieren weiterer Pakete

Ihre Anwendung weist möglicherweise Abhängigkeiten über Python und Flask hinaus auf.

Sie können zusätzliche Pakete mit pip installieren.  Geben Sie beispielsweise zum Installieren des Azure SDK für Python, das Ihnen Zugriff auf Azure-Speicher, den Service Bus und andere Azure-Dienste bereitstellt, Folgendes ein:

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


## Troubleshooting - Deployment

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


## Problembehandlung - Paketinstallation

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


## Problembehandlung - Virtuelle Umgebung

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Nächste Schritte

Folgen Sie diesen Links, um weitere Informationen zu Flask- und Python Tools für Visual Studio zu erhalten: 
 
- [Dokumentation zu Flask][]
- [Python Tools für Visual Studio - Dokumentation][] 

Weitere Informationen zur Verwendung von Azure Table Storage und MongoDB:

- [Flask und MongoDB in Azure mit Python Tools 2.1 für Visual Studio][]
- [Flask und Azure Table Storage in Azure mit Python Tools 2.1 für Visual Studio][]


<!--Link references-->
[Flask und MongoDB in Azure mit Python Tools 2.1 für Visual Studio]: web-sites-python-ptvs-flask-table-storage.md
[Flask und Azure Table Storage in Azure mit Python Tools 2.1 für Visual Studio]: web-sites-python-ptvs-flask-mongodb.md

<!--External Link references-->
[Azure SDK für Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK für Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git für Windows]: http://msysgit.github.io/
[GitHub für Windows]: https://windows.github.com/
[Python Tools für Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[PTVS-Dokumentation]: http://pytools.codeplex.com/documentation
[Python Tools für Visual Studio - Dokumentation]: http://pytools.codeplex.com/documentation 
[Dokumentation zu Flask]: http://flask.pocoo.org/ 


<!--HONumber=52-->