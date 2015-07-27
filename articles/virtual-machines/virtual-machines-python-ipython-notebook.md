<properties 
	pageTitle="IPython Notebook – Azure-Lernprogramm" 
	description="Dieses Lernprogramm beschreibt die Bereitstellung von IPython Notebook in Azure mithilfe von virtuellen Linux- oder Windows-Computern (VMs)." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="huvalo"/>


# IPython Notebook in Azure

Das [IPython-Projekt](http://ipython.org) bietet eine Sammlung von Tools für wissenschaftliche Berechnungen inklusive umfangreicher interaktiver Shells, leistungsstarker und leicht einsetzbarer paralleler Bibliotheken und einer webbasierten Umgebung mit dem Namen IPython-Notizbuch. Notebook bietet eine Arbeitsumgebung für interaktive Berechnungen, die Codeausführung mit der Erstellung eines Live-Berechnungsdokuments vereint. Diese Notebook-Dateien enthalten beliebigen Text, mathematische Formeln, Eingabecode, Ergebnisse, Grafiken, Videos und beliebige andere Medientypen, die in modernen Webbrowsern angezeigt werden können.

Das IPython-Notizbuch ist eine hervorragende Wahl, egal ob Sie Python-Anfänger sind und die Sprache in einer interessanten und interaktiven Umgebung erlernen möchten, oder ob Sie ernsthafte parallele und technische Berechnungen durchführen möchten. Der folgende Screenshot illustriert diese Möglichkeiten und zeigt das IPython-Notizbuch in Kombination mit den SciPy- und matplotlib-Paketen, um die Struktur einer Tonaufnahme zu analysieren:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

Dieses Dokument beschreibt die Bereitstellung von IPython Notebook in Microsoft Azure mithilfe von virtuellen Linux- oder Windows-Computern (VMs). Mit dem IPython-Notizbuch in Azure können Sie schnell und einfach webbasierte Schnittstellen für skalierbare Rechenressourcen mit dem gesamten Funktionsumfang von Python und den zahlreichen Bibliotheken erstellen. Da die gesamte Installation in der Cloud erfolgt, können Benutzer diese Ressourcen ohne jegliche lokale Konfiguration einfach in einem modernen Webbrowser nutzen.

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Erstellen und Konfigurieren einer VM in Azure

Zunächst müssen Sie einen virtuellen Computer (VM) in Azure erstellen. Diese VM ist ein komplettes Betriebssystem in der Cloud und wird für die Ausführung des IPython-Notizbuchs verwendet. Azure kann mit virtuellen Linux- und Windows-Computern umgehen, und wir werden die Einrichtung von IPython auf beiden Arten von virtuellen Computern behandeln.

### Linux-VM

Führen Sie [diese Anweisungen][portal-vm-linux] aus, um einen virtuellen Computer mit einer *OpenSUSE*- oder *Ubuntu*-Distribution zu erstellen. In diesem Lernprogramm werden OpenSUSE 13.2 und Ubuntu Server 14.04 LTS verwendet. Wir gehen vom Standard-Benutzernamen *azureuser* aus.

### Windows-VM

Führen Sie [diese Anweisungen][portal-vm-windows] aus, um einen virtuellen Computer mit einer *Windows Server 2012 R2 Datacenter*-Distribution zu erstellen. In diesem Lernprogramm gehen wir vom Standard-Benutzernamen *azureuser* aus.

## Erstellen eines Endpunkts für IPython Notebook

Dieser Schritt gilt für virtuelle Linux- und Windows-Computer. Später konfigurieren wir IPython für die Ausführung des Notizbuch-Servers auf dem Port 9999. Um diesen Port öffentlich zugänglich zu machen, müssen wir zunächst einen Endpunkt im Azure-Verwaltungsportal erstellen. Dieser Endpunkt öffnet einen Port in der Azure-Firewall und ordnet den öffentlichen Port (HTTPS, 443) dem privaten Port der VM (9999) zu.

Um einen Endpunkt zu erstellen, öffnen Sie das VM-Dashboard, klicken Sie auf "Endpunkte" und anschließend auf "Endpunkt hinzufügen", und erstellen Sie einen neuen Endpunkt (in diesem Beispiel mit dem Namen `ipython_nb`). Wählen Sie TCP als Protokoll aus, 443 für den öffentlichen Port und 9999 für den privaten Port:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

Nach diesem Schritt sieht die Registerkarte "Endpunkte" im Dashboard folgendermaßen aus:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## Installieren der erforderlichen Software auf der VM

Um das IPython-Notizbuch auf unserer VM ausführen zu können, müssen wir zunächst Python und die entsprechenden Abhängigkeiten installieren.

### Linux (OpenSUSE)

Um IPython und dessen Abhängigkeiten zu installieren, verbinden Sie sich per SSH mit der Linux-VM und führen Sie die folgenden Schritte durch.

Installieren Sie [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] und andere IPython-Abhängigkeiten mit:

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

Um IPython und dessen Abhängigkeiten zu installieren, verbinden Sie sich per SSH mit der Linux-VM und führen Sie die folgenden Schritte durch.

Rufen Sie zunächst die neue Listen von Paketen auf:

    sudo apt-get update

Installieren Sie [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] und andere IPython-Abhängigkeiten mit:

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Um IPython und dessen Abhängigkeiten auf der Windows-VM zu installieren, verbinden Sie sich per Remotedesktop mit der VM. Führen Sie anschließend die folgenden Schritte aus, indem Sie die Befehlszeilenaktionen in der Windows PowerShell ausführen.

**Hinweis**: Sie müssen einige Sicherheitseinstellungen ändern, um Downloads mit Internet Explorer ausführen zu können. Klicken Sie im **Server-Manager** auf **Lokaler Server**, anschließend auf **Verstärkte Sicherheitskonfiguration für IE** und deaktivieren Sie die Option für Administratoren. Sie können diese Option nach der IPython-Installation wieder aktivieren.

1.  Laden Sie die aktuelle 32-Bit-Version von [Python 2.7][] herunter, und installieren Sie sie. Sie müssen `C:\Python27` und `C:\Python27\Scripts` zu der `PATH`-Umgebungsvariablen hinzufügen.

1.  Installieren Sie [Tornado][tornado] und [PyZMQ][pyzmq] und andere IPython-Abhängigkeiten mit:

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  Laden Sie [NumPy][numpy] mithilfe des `.exe`-binärinstallationsprogramms von der Website herunter, und installieren Sie sie. Zum Zeitpunkt der Erstellung dieses Dokuments ist **numpy-1.9.1-win32-superpack-python2.7.exe** die neueste Version.

1.  Installieren Sie [Matplotlib][matplotlib] wie folgt:

        pip install matplotlib==1.4.2

1.  Laden Sie [OpenSSL][] herunter, und installieren Sie es.

	* Finden Sie das erforderliche **Visual C++ 2008 Redistributable** auf derselben Download-Seite.

	* Sie müssen `C:\OpenSSL-Win32\bin` der `PATH`-Umgebungsvariable hinzufügen.

	> [AZURE.NOTE] Installieren Sie unbedingt Version 1.0.1g oder höher von OpenSSL, da diese Versionen Schutz vor der Heartbleed-Sicherheitslücke bieten.

1.  Installieren Sie IPython mit dem Befehl:

        pip install ipython==2.4

1.  Öffnen eines Ports in der Windows-Firewall. Unter Windows Server 2012 sperrt die Firewall eingehende Verbindungen standardmäßig. Führen Sie die folgenden Schritte aus, um Port 9999 zu öffnen:

    - Starten Sie die **Windows-Firewall mit erweiterter Sicherheit** aus dem Startbildschirm.

    - Klicken Sie im linken Bereich auf **Eingehende Regeln**.

	- Klicken Sie im Aktionsbereich auf **Neue Regel...**.

	- Klicken Sie im Assistenten für neue eingehende Regeln auf **Port**.

	- Wählen Sie im nächsten Bildschirm **TCP** aus und geben Sie **9999** unter **Bestimmte lokale Ports** ein.

	- Akzeptieren Sie die Standardwerte, geben Sie der Regel einen Namen und klicken Sie auf Fertig stellen.

### Konfigurieren von IPython Notebook

Nun werden wir das IPython-Notizbuch konfigurieren. Zunächst müssen wir ein IPython-Konfigurationsprofil erstellen, um die Konfigurationsdaten zu kapseln:

    ipython profile create nbserver

Anschließend wechseln wir mit `cd` in das Profilverzeichnis, um unser SSL-Zertifikat zu erstellen und die Profil-Konfigurationsdatei zu bearbeiten.

Unter Linux:

    cd ~/.ipython/profile_nbserver/

Unter Windows:

    cd \users\azureuser.ipython\profile_nbserver

Erstellen Sie das SSL-Zertifikat (Linux und Windows) wie folgt:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Da wir ein selbstsigniertes SSL-Zertifikat erstellen, erhalten Sie eine Sicherheitswarnung, wenn Sie mit Ihrem Browser das Notizbuch aufrufen. Für den langfristigen Produktionseinsatz sollten Sie ein korrekt signiertes Zertifikat verwenden, das Ihrer Organisation zugeordnet ist. Da die Zertifikatsverwaltung nicht Teil dieser Demonstration ist, werden wir ein selbstsigniertes Zertifikat verwenden.

Neben dem Zertifikat müssen Sie auch ein Kennwort angeben, um Ihr Notizbuch vor unbefugtem Zugriff zu schützen. Aus Sicherheitsgründen verwendet IPython verschlüsselte Kennwörter in der Konfigurationsdatei, daher müssen Sie Ihr Kennwort zunächst verschlüsseln. IPython verfügt über ein Hilfsprogramm zu diesem Zweck; führen Sie den folgenden Befehl in einer Eingabeaufforderung aus:

    python -c "import IPython;print IPython.lib.passwd()"

Daraufhin werden Sie aufgefordert, Ihr Kennwort zweimal einzugeben, und das Kennwort wird anschließend wie folgt ausgegeben:

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
Als Nächstes bearbeiten wir die Konfigurationsdatei des Profils. Hierbei handelt es sich um die `ipython_notebook_config.py`-Datei im Profilverzeichnis, in dem Sie sich befinden. Beachten Sie, dass diese Datei möglicherweise nicht vorhanden ist. Erstellen Sie sie einfach. Diese Datei verfügt über einige Felder, und standardmäßig sind alle auskommentiert. Öffnen Sie diese Datei in einem Text-Editor Ihrer Wahl und stellen Sie sicher, dass mindestens der folgende Text enthalten ist:

    c = get_config()
    
    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'
    
    # You must give the path to the certificate file.
    
    # If using a Linux VM:
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'
    
    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser.ipython\profile_nbserver\mycert.pem'
    
    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '
    
    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Ausführen von IPython Notebook

Nun können wir das IPython-Notizbuch starten. Navigieren Sie dazu in das Verzeichnis, in dem Sie Notizbücher speichern möchten, und führen Sie den IPython-Notizbuch-Server aus:

    ipython notebook --profile=nbserver

Jetzt sollten Sie in der Lage sein, unter der Adresse `https://[Your Chosen Name Here].cloudapp.net` auf IPython Notebook zuzugreifen:

Beim ersten Zugriff auf IPython Notebook müssen Sie auf der Anmeldungsseite Ihr Kennwort eingeben:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

Nach der Anmeldung sehen Sie das "IPython-Notizbuch-Dashboard", die Steuerzentrale für sämtliche Notizbuchoperationen. Auf dieser Seite können Sie neue Notizbücher erstellen, bestehende Notizbücher öffnen usw.:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Wenn Sie auf die Schaltfläche "Neues Notizbuch" klicken, öffnet sich die folgende Seite:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

In dem mit der `In []:`-Eingabeaufforderung gekennzeichneten Bereich können Sie gültigen Python-Code eingeben und diesen ausführen, indem Sie `Shift-Enter` drücken oder auf das "Wiedergabe"-Symbol klicken (ein nach rechts zeigendes Dreieck auf der Symbolleiste).

Da wir das Notizbuch für den automatischen Start mit NumPy- und matplotlib-Unterstützung konfiguriert haben, können Sie sogar Diagramme erstellen, z. B.:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## Ein mächtiges Paradigma: Live-Berechnungsdokumente mit Rich-Media-Objekten

Das Notizbuch ist seht intuitiv für Benutzer, die mit Python und Textverarbeitungsprogrammen vertraut sind, da es sich in gewisser Weise um beides handelt: Sie können Blöcke von Python-Code ausführen und gleichzeitig Notizen und anderen Text erstellen, indem Sie den Stil einer Zelle im Dropdownmenü der Symbolleiste von "Code" zu "Markdown" ändern:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


Es handelt sich jedoch um mehr als reine Textverarbeitung, da Sie mit dem IPython-Notizbuch Berechnungen und Rich-Media-Inhalte (Text, Grafiken, Video und praktisch alle in einem modernen Browser darstellbaren Elemente) mischen können. Sie können zum Beispiel Erklärungsvideos mit Berechnungen zu Bildungszwecken mischen:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

oder externe Websites, die weiterhin live und nutzbar bleiben, in eine Notebook-Datei einbetten:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

Mit den vielen hervorragenden Python-Bibliotheken für wissenschaftliche und technische Berechnungen können Sie einfache Berechnungen ebenso leicht in einer einzigen Umgebung umsetzen wie komplexe Netzwerkanalysen:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

Dieses Paradigma der Mischung aus modernem Web und Live-Berechnungen bietet zahlreiche Vorzüge und eignet sich hervorragend für die Cloud. Notizbücher können zu folgenden Zwecken verwendet werden:

* als Berechnungs-Notizblock für die Aufzeichnung von Erforschungsdaten zu einem Problem,

* für die Weitergabe von Ergebnissen an Kollegen, entweder in Form von Live-Berechnungen oder als Kopie mit festem Format (HTML, PDF),

* für die Verteilung und Präsentation von Live-Unterrichtsmaterialien inklusive Berechnungen, anhand derer die Studenten mit dem tatsächlichen Code experimentieren, diesen verändern und sofort erneut ausführen können,

* für die Bereitstellung von "ausführbaren Arbeiten" die Forschungsergebnisse auf eine Weise präsentieren, die von anderen sofort nachvollzogen, validiert und erweitert werden können.

* als Plattform für gemeinschaftliche Berechnungen: Mehrere Benutzer können sich bei einem Notizbuch-Server anmelden und eine Live-Berechnungssitzung gemeinsam nutzen.

* und vieles mehr...

Im IPython-Quellcode-[Repository][] finden Sie ein gesamtes Verzeichnis mit Notizbuch-Beispielen, die Sie herunterladen und anschließend auf Ihrer eigenen IPython-VM in Azure ausprobieren können. Laden Sie einfach die `.ipynb`-Dateien von der Website herunter, und laden Sie die Dateien auf das Dashboard Ihres Notizbuch Azure-VM hoch(oder laden Sie sie direkt in die VM herunter).

## Zusammenfassung

IPython-Notizbücher bieten eine leistungsstarke Schnittstelle für die interaktive Nutzung des Funktionsumfangs des Python-Ökosystems in Azure. Die Notizbücher decken ein breites Spektrum von Anwendungsfällen ab, inklusive Erkundung und Erlernen von Python, Datenanalyse und -Darstellung, Simulation und parallele Berechnungen. Die resultierenden Notizbuchdokumente enthalten eine komplette Aufzeichnung aller ausgeführten Berechnungen und können an andere IPython-Benutzer weitergegeben werden. Das IPython-Notizbuch kann auch als lokale Anwendung verwendet werden, eignet sich jedoch ideal für Cloud-Bereitstellungen in Azure.

Die Kernfunktionen von IPython sind auch in Visual Studio unter [Python Tools for Visual Studio][] (PTVS) verfügbar. PTVS ist ein kostenloses Open Source-Plug-In von Microsoft und verwandelt Visual Studio in eine umfangreiche Python-Entwicklungsumgebung mit einem modernen Editor inklusive IntelliSense, Debugging, Profilerstellung und Integration für parallele Berechnungen.



[tornado]: http://www.tornadoweb.org/ "Tornado"
[pyzmq]: https://github.com/zeromq/pyzmq "PyZMQ"
[numpy]: http://www.numpy.org/ "NumPy"
[matplotlib]: http://matplotlib.sourceforge.net/ "Matplotlib"

[portal-vm-windows]: /manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /manage/linux/tutorials/virtual-machine-from-gallery/

[repository]: https://github.com/ipython/ipython
[python Tools for visual studio]: http://aka.ms/ptvs

[python 2.7]: http://www.python.org/download
[openssl]: http://slproweb.com/products/Win32OpenSSL.html
 

<!----HONumber=July15_HO3-->