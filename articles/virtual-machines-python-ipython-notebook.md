<properties linkid="develop-python-ipython-notebook" urlDisplayName="IPython Notebook" pageTitle="IPython Notebook - Azure tutorial" metaKeywords="" description="A tutorial that shows how to deploy the IPython Notebook on Azure, using Linux or Windows virtual machines (VMs)." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="IPython Notebook on Azure" authors="" solutions="" manager="" editor="" />

IPython-Notizbuch in Azure
==========================

Das [IPython-Projekt](http://ipython.org) bietet eine Sammlung von Tools für wissenschaftliche Berechnungen inklusive umfangreicher interaktiver Shells, leistungsstarker und leicht einsetzbarer paralleler Bibliotheken und einer webbasierten Umgebung mit dem Namen IPython-Notizbuch. Das Notizbuch bietet eine Arbeitsumgebung für interaktive Berechnungen, die Codeausführung mit der Erstellung eines Live-Berechnungsdokuments vereint. Diese Notizbuchdateien enthalten beliebigen Text, mathematische Formeln, Eingabecode, Ergebnisse, Grafiken, Videos und beliebige andere Medientypen, die in modernen Webbrowsern angezeigt werden können.

[Lernprogramm ansehen](http://go.microsoft.com/fwlink/?LinkID=254535&clcid=0x409) [Video abspielen](http://go.microsoft.com/fwlink/?LinkID=254535&clcid=0x409) 5:22

Das IPython-Notizbuch ist eine hervorragende Wahl, egal ob Sie Python-Anfänger sind und die Sprache in einer interessanten und interaktiven Umgebung erlernen möchten, oder ob Sie ernsthafte parallele und technische Berechnungen durchführen möchten. Der folgende Screenshot illustriert diese Möglichkeiten und zeigt das IPython-Notizbuch in Kombination mit den SciPy- und matplotlib-Paketen, um die Struktur einer Tonaufnahme zu analysieren:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

Dieses Dokument beschreibt die Bereitstellung des IPython-Notizbuchs in Azure mithilfe von virtuellen Linux- oder Windows-Computern (VMs). Mit dem IPython-Notizbuch in Azure können Sie schnell und einfach webbasierte Schnittstellen für skalierbare Rechenressourcen mit dem gesamten Funktionsumfang von Python und den zahlreichen Bibliotheken erstellen. Da die gesamte Installation in der Cloud erfolgt, können Benutzer diese Ressourcen ohne jegliche lokale Konfiguration einfach in einem modernen Webbrowser nutzen.

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

Erstellen und Konfigurieren einer VM in Azure
---------------------------------------------

Zunächst müssen Sie einen virtuellen Computer (VM) in Azure erstellen. Diese VM ist ein komplettes Betriebssystem in der Cloud und wird für die Ausführung des IPython-Notizbuchs verwendet. Azure kann mit virtuellen Linux- und Windows-Computern umgehen, und wir werden die Einrichtung von IPython auf beiden Arten von virtuellen Computern behandeln.

### Linux-VM

Führen Sie [diese Anweisungen](/de-de/manage/linux/tutorials/virtual-machine-from-gallery/) aus, um einen virtuellen Computer mit einer *OpenSUSE*- oder *Ubuntu*-Distribution zu erstellen. Dieses Lernprogramm verwendet OpenSUSE 12.3 und Ubuntu Server 13.04. Wir gehen vom Standard-Benutzernamen *azureuser* aus.

### Windows-VM

Führen Sie [diese Anweisungen](/de-de/manage/windows/tutorials/virtual-machine-from-gallery/) aus, um einen virtuellen Computer mit einer *Windows Server 2012 Datacenter*-Distribution zu erstellen. In diesem Lernprogramm gehen wir vom Standard-Benutzernamen *azureuser* aus.

Erstellen eines Endpunkts für das IPython-Notizbuch
---------------------------------------------------

Dieser Schritt gilt für virtuelle Linux- und Windows-Computer. Später konfigurieren wir IPython für die Ausführung des Notizbuch-Servers auf dem Port 9999. Um diesen Port öffentlich zugänglich zu machen, müssen wir zunächst einen Endpunkt im Azure-Verwaltungsportal erstellen. Dieser Endpunkt öffnet einen Port in der Azure-Firewall und ordnet den öffentlichen Port (HTTPS, 443) dem privaten Port der VM (9999) zu.

Um den Endpunkt zu erstellen, öffnen Sie das VM-Dashboard, klicken Sie auf "Endpunkte" und anschließend auf "Endpunkt hinzufügen" und erstellen Sie einen neuen Endpunkt (mit dem Namen `ipython_nb` in diesem Beispiel). Wählen Sie TCP als Protokoll aus, 443 für den öffentlichen Port und 9999 für den privaten Port:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

Nach diesem Schritt sieht die Registerkarte "Endpunkte" im Dashboard folgendermaßen aus:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

Installieren der erforderlichen Software auf der VM
---------------------------------------------------

Um das IPython-Notizbuch auf unserer VM ausführen zu können, müssen wir zunächst Python und die entsprechenden Abhängigkeiten installieren.

### Linux (OpenSUSE)

Um IPython und dessen Abhängigkeiten zu installieren, verbinden Sie sich per SSH mit der Linux-VM und führen Sie die folgenden Schritte durch.

Installieren Sie [NumPy](http://www.numpy.org/ "NumPy"), [Matplotlib](http://matplotlib.sourceforge.net/ "Matplotlib"), [Tornado](http://www.tornadoweb.org/ "Tornado") und andere IPython-Abhängigkeiten mit den folgenden Befehlen:

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install ipython

### Linux (Ubuntu)

Um IPython und dessen Abhängigkeiten zu installieren, verbinden Sie sich per SSH mit der Linux-VM und führen Sie die folgenden Schritte durch.

Installieren Sie [NumPy](http://www.numpy.org/ "NumPy"), [Matplotlib](http://matplotlib.sourceforge.net/ "Matplotlib"), [Tornado](http://www.tornadoweb.org/ "Tornado") und andere IPython-Abhängigkeiten mit den folgenden Befehlen:

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Um IPython und dessen Abhängigkeiten auf der Windows-VM zu installieren, verbinden Sie sich per Remotedesktop mit der VM. Führen Sie anschließend die folgenden Schritte aus, indem Sie die Befehlszeilenaktionen in der Windows PowerShell ausführen.

**Hinweis**: Sie müssen einige Sicherheitseinstellungen ändern, um Downloads mit dem Internet Explorer ausführen zu können. Klicken Sie im **Server-Manager** auf **Lokaler Server**, anschließend auf **Verstärkte Sicherheitskonfiguration für IE** und deaktivieren Sie die Option für Administratoren. Sie können diese Option nach der IPython-Installation wieder aktivieren.

1.  Installieren Sie Python 2.7.5 (32 Bit) von [python.org](http://www.python.org/download). Sie müssen außerdem `C:\Python27` und `C:\Python27\Scripts` zu Ihrer `PATH` Umgebungsvariable hinzufügen.

2.  Installieren Sie distribute, indem Sie die Datei **distribute\_setup.py** von [python-distribute.org](http://python-distribute.org/) herunterladen und anschließend den folgenden Befehl ausführen:

        python distribute_setup.py

3.  Installieren Sie [Tornado](http://www.tornadoweb.org/ "Tornado") und [PyZMQ](https://github.com/zeromq/pyzmq "PyZMQ") mit den folgenden Befehlen:

        easy_install tornado
        easy_install pyzmq

4.  Laden Sie [NumPy](http://www.numpy.org/ "NumPy") herunter und installieren Sie das Paket mit dem `.exe`-Binärinstallationsprogramm auf der Website. Zum Zeitpunkt der Erstellung dieses Dokuments ist die neueste Version **numpy-1.7.1-win32-superpack-python2.7.exe**.

5.  Laden Sie [Matplotlib](http://matplotlib.sourceforge.net/ "Matplotlib") herunter und installieren Sie das Paket mit dem `.exe`-Binärinstallationsprogramm auf der Website. Zum Zeitpunkt der Erstellung dieses Dokuments ist die neueste Version **matplotlib-1.2.1.win32-py2.7.exe**.

6.  Herunterladen und Installieren von OpenSSL. Sie finden Windows-Versionen von OpenSSL unter <http://slproweb.com/products/Win32OpenSSL.html>.

    -   Falls Sie eine **Light**-Version installieren, müssen Sie auch **Visual C++ 2008 Redistributable** installieren (ebenfalls auf dieser Seite erhältlich).

    -   Fügen Sie `C:\OpenSSL-Win32\bin` zu Ihrer `PATH`-Umgebungsvariable hinzu.

    > [WACOM.NOTE] Installieren Sie unbedingt Version 1.0.1g oder höher von OpenSSL, da diese Versionen Schutz vor der Heartbleed-Sicherheitslücke bieten.

7.  Installieren Sie IPython mit dem folgenden Befehl:

        easy_install ipython

8.  Öffnen eines Ports in der Windows-Firewall. Unter Windows Server 2012 sperrt die Firewall eingehende Verbindungen standardmäßig. Führen Sie die folgenden Schritte aus, um Port 9999 zu öffnen:

    -   Starten Sie die **Windows-Firewall mit erweiterter Sicherheit** aus dem Startbildschirm.

    -   Klicken Sie im linken Bereich auf **Eingehende Regeln**.

    -   Klicken Sie im Aktionsbereich auf **Neue Regel...**.

    -   Klicken Sie im Assistenten für neue eingehende Regeln auf **Port**.

    -   Wählen Sie im nächsten Bildschirm **TCP** aus und geben Sie **9999** unter **Bestimmte lokale Ports** ein.

    -   Akzeptieren Sie die Standardwerte, geben Sie der Regel einen Namen und klicken Sie auf Fertig stellen.

### Konfigurieren des IPython-Notizbuchs

Nun werden wir das IPython-Notizbuch konfigurieren. Zunächst müssen wir ein IPython-Konfigurationsprofil erstellen, um die Konfigurationsdaten zu kapseln:

    ipython profile create nbserver

Anschließend wechseln wir per `cd` in das Profilverzeichnis, um unser SSL-Zertifikat zu erstellen und die Profil-Konfigurationsdatei zu bearbeiten.

Unter Linux (OpenSUSE):

    cd ~/.config/ipython/profile_nbserver/

Unter Linux (Ubuntu):

    cd ~/.ipython/profile_nbserver/

Unter Windows:

    cd \users\azureuser\.ipython\profile_nbserver

Erstellen Sie das SSL-Zertifikat unter beiden Plattformen wie folgt:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Da wir ein selbstsigniertes SSL-Zertifikat erstellen, erhalten Sie eine Sicherheitswarnung, wenn Sie mit Ihrem Browser das Notizbuch aufrufen. Für den langfristigen Produktionseinsatz sollten Sie ein korrekt signiertes Zertifikat verwenden, das Ihrer Organisation zugeordnet ist. Da die Zertifikatsverwaltung nicht Teil dieser Demonstration ist, werden wir ein selbstsigniertes Zertifikat verwenden.

Neben dem Zertifikat müssen Sie auch ein Kennwort angeben, um Ihr Notizbuch vor unbefugtem Zugriff zu schützen. Aus Sicherheitsgründen verwendet IPython verschlüsselte Kennwörter in der Konfigurationsdatei, daher müssen Sie Ihr Kennwort zunächst verschlüsseln. IPython verfügt über ein Hilfsprogramm zu diesem Zweck; führen Sie den folgenden Befehl in einer Eingabeaufforderung aus:

    python -c "import IPython;print IPython.lib.passwd()"

Daraufhin werden Sie aufgefordert, Ihr Kennwort zweimal einzugeben, und das Kennwort wird anschließend wie folgt ausgegeben:

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Nun werden wir die Profil-Konfigurationsdatei `ipython_notebook_config.py` im aktuellen Verzeichnis bearbeiten. Diese Datei enthält standardmäßig eine Reihe von Einträgen, die standardmäßig allesamt auskommentiert sind. Öffnen Sie diese Datei in einem Text-Editor Ihrer Wahl und stellen Sie sicher, dass mindestens der folgende Text enthalten ist:

    c = get_config()

    # Startet die Darstellungsunterstützung immer mit matplotlib
    c.IPKernelApp.pylab = 'inline'

    # Geben Sie den Pfad der Zertifikatdatei an.

    # Für Linux-VMs (OpenSUSE):
    c.NotebookApp.certfile = u'/home/azureuser/.config/ipython/profile_nbserver/mycert.pem'

    # Für Linux-VMs (Ubuntu):
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'

    # Für Windows-VMs:
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'

    # Generieren Sie Ihr eigenes Kennwort wie oben beschrieben
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Netzwerk- und Browserdetails. Wir verwenden einen konstanten Port (9999),
    # den wir bei der Azure-Einrichtung für Datenverkehr geöffnet haben

    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Ausführen des IPython-Notizbuchs

Nun können wir das IPython-Notizbuch starten. Navigieren Sie dazu in das Verzeichnis, in dem Sie Notizbücher speichern möchten, und führen Sie den IPython-Notizbuch-Server aus:

    ipython notebook --profile=nbserver

Sie sollten nun unter der Adresse `https://[Ihr ausgewählter Name].cloudapp.net` auf Ihr IPython-Notizbuch zugreifen können.

Beim ersten Zugriff auf Ihr Notizbuch müssen Sie auf der Anmeldungsseite Ihr Kennwort eingeben:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

Nach der Anmeldung sehen Sie das "IPython-Notizbuch-Dashboard", die Steuerzentrale für sämtliche Notizbuchoperationen. Auf dieser Seite können Sie neue Notizbücher erstellen, bestehende Notizbücher öffnen usw.:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Wenn Sie auf die Schaltfläche "Neues Notizbuch" klicken, öffnet sich die folgende Seite:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

In dem mit `In []:` markierten Bereich können Sie gültigen Python-Code eingeben und diesen ausführen, indem Sie `Umschalt-Eingabetaste` drücken oder auf das "Abspielen"-Symbol klicken (ein nach rechts zeigendes Dreieck in der Symbolleiste).

Da wir das Notizbuch für den automatischen Start mit NumPy- und matplotlib-Unterstützung konfiguriert haben, können Sie sogar Diagramme erstellen, z. B.:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

Ein mächtiges Paradigma: Live-Berechnungsdokumente mit Rich-Media-Objekten
--------------------------------------------------------------------------

Das Notizbuch ist seht intuitiv für Benutzer, die mit Python und Textverarbeitungsprogrammen vertraut sind, da es sich in gewisser Weise um beides handelt: Sie können Blöcke von Python-Code ausführen und gleichzeitig Notizen und anderen Text erstellen, indem Sie den Stil einer Zelle im Dropdownmenü der Symbolleiste von "Code" zu "Markdown" ändern:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)

Es handelt sich jedoch um mehr als reine Textverarbeitung, da Sie mit dem IPython-Notizbuch Berechnungen und Rich-Media-Inhalte (Text, Grafiken, Video und praktisch alle in einem modernen Browser darstellbaren Elemente) mischen können. Sie können zum Beispiel Erklärungsvideos mit Berechnungen zu Bildungszwecken mischen:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

oder externe Websites, die weiterhin live und nutzbar bleiben, in einer Notizbuchdatei einbetten:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

Mit den vielen hervorragenden Python-Bibliotheken für wissenschaftliche und technische Berechnungen können Sie einfache Berechnungen ebenso leicht in einer einzigen Umgebung umsetzen wie komplexe Netzwerkanalysen:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

Dieses Paradigma der Mischung aus modernem Web und Live-Berechnungen bietet zahlreiche Vorzüge und eignet sich hervorragend für die Cloud. Notizbücher können zu folgenden Zwecken verwendet werden:

-   als Berechnungs-Notizblock für die Aufzeichnung von Erforschungsdaten zu einem Problem,

-   für die Weitergabe von Ergebnissen an Kollegen, entweder in Form von Live-Berechnungen oder als Kopie mit festem Format (HTML, PDF),

-   für die Verteilung und Präsentation von Live-Unterrichtsmaterialien inklusive Berechnungen, anhand derer die Studenten mit dem tatsächlichen Code experimentieren, diesen verändern und sofort erneut ausführen können,

-   für die Bereitstellung von "ausführbaren Arbeiten" die Forschungsergebnisse auf eine Weise präsentieren, die von anderen sofort nachvollzogen, validiert und erweitert werden können.

-   als Plattform für gemeinschaftliche Berechnungen: Mehrere Benutzer können sich bei einem Notizbuch-Server anmelden und eine Live-Berechnungssitzung gemeinsam nutzen.

-   und vieles mehr...

Im IPython-Quellcode-Repository finden Sie ein gesamtes Verzeichnis mit [Notizbuch-Beispielen](https://github.com/ipython/ipython/tree/master/examples/notebooks), die Sie herunterladen und anschließend auf Ihrer eigenen IPython-VM in Azure ausprobieren können. Laden Sie einfach die `.ipynb`-Dateien von der Website herunter und laden Sie sie anschließend im Dashboard Ihrer Notizbuch-VM in Azure hoch (bzw. laden Sie die Dateien direkt in die VM herunter).

Zusammenfassung
---------------

IPython-Notizbücher bieten eine leistungsstarke Schnittstelle für die interaktive Nutzung des Funktionsumfangs des Python-Ökosystems in Azure. Die Notizbücher decken ein breites Spektrum von Anwendungsfällen ab, inklusive Erkundung und Erlernen von Python, Datenanalyse und -Darstellung, Simulation und parallele Berechnungen. Die resultierenden Notizbuchdokumente enthalten eine komplette Aufzeichnung aller ausgeführten Berechnungen und können an andere IPython-Benutzer weitergegeben werden. Das IPython-Notizbuch kann auch als lokale Anwendung verwendet werden, eignet sich jedoch ideal für Cloud-Bereitstellungen in Azure.

Die Kernfunktionen von IPython sind auch in Visual Studio unter [Python Tools for Visual Studio](http://pytools.codeplex.com) (PTVS) verfügbar. PTVS ist ein kostenloses Open Source-Plug-In von Microsoft und verwandelt Visual Studio in eine umfangreiche Python-Entwicklungsumgebung mit einem modernen Editor inklusive IntelliSense, Debugging, Profilerstellung und Integration für parallele Berechnungen.

