<properties pageTitle="IPython Notebook - Azure-Lernprogramm" description="Dieses Lernprogramm beschreibt die Bereitstellung von IPython Notebook in Azure mithilfe von virtuellen Linux- oder Windows-Computern (VMs)." services="virtual-machines" documentationCenter="python" authors="huguesv" manager="wpickett" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo"/>





# IPython Notebook in Azure

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Das <a href="http://ipython.org">IPython-Projekt</a> bietet eine Sammlung von Tools für wissenschaftliche Berechnungen inklusive umfangreicher interaktiver Shells, leistungsstarker und leicht einsetzbarer paralleler Bibliotheken und einer webbasierten Umgebung mit dem Namen IPython Notebook. Notebook bietet eine Arbeitsumgebung für interaktive Berechnungen, die Codeausführung mit der Erstellung eines Live-Berechnungsdokuments vereint. Diese Notebook-Dateien enthalten beliebigen Text, mathematische Formeln, Eingabecode, Ergebnisse, Grafiken, Videos und beliebige andere Medientypen, die in modernen Webbrowsern angezeigt werden können.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">Lernprogramm anzeigen</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">Video wiedergeben</span></a> <span class="time">05:22</span></div>
</div>

Das IPython-Notizbuch ist eine hervorragende Wahl, egal ob Sie Python-Anfänger sind und die Sprache in einer interessanten und interaktiven Umgebung erlernen möchten, oder ob Sie ernsthafte parallele und technische Berechnungen durchführen möchten. Der folgende Screenshot illustriert diese Möglichkeiten und zeigt das IPython-Notizbuch in Kombination mit den SciPy- und matplotlib-Paketen, um die Struktur einer Tonaufnahme zu analysieren:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

Dieses Dokument beschreibt die Bereitstellung von IPython Notebook in Microsoft Azure mithilfe von virtuellen Linux- oder Windows-Computern (VMs).  Mit dem IPython-Notizbuch in Azure können Sie schnell und einfach webbasierte Schnittstellen für skalierbare Rechenressourcen mit dem gesamten Funktionsumfang von Python und den zahlreichen Bibliotheken erstellen.  Da die gesamte Installation in der Cloud erfolgt, können Benutzer diese Ressourcen ohne jegliche lokale Konfiguration einfach in einem modernen Webbrowser nutzen.

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Erstellen und Konfigurieren einer VM in Azure

Zunächst müssen Sie einen virtuellen Computer (VM) in Azure erstellen.
Diese VM ist ein komplettes Betriebssystem in der Cloud und wird für die Ausführung des IPython-Notizbuchs verwendet. Azure kann mit virtuellen Linux- und Windows-Computern umgehen, und wir werden die Einrichtung von IPython auf beiden Arten von virtuellen Computern behandeln.

### Linux-VM

Befolgen Sie die [hier][portal-vm-linux] aufgeführten Anweisungen, um einen virtuellen Azure-Computer der  *OpenSUSE*- oder  *Ubuntu*-Verteilung zu erstellen. Dieses Lernprogramm verwendet OpenSUSE 13.1 und Ubuntu Server 14.04 LTS. Wir gehen vom Standard-Benutzernamen  *azureuser* aus.

### Windows-VM

Befolgen Sie die [hier][portal-vm-windows] aufgeführten Anweisungen, um einen virtuellen Azure-Computer der  *Windows Server 2012 R2 Datacenter*-Verteilung zu erstellen. In diesem Lernprogramm gehen wir vom Standard-Benutzernamen  *azureuser* aus.

## Erstellen eines Endpunkts für IPython Notebook

Dieser Schritt gilt für virtuelle Linux- und Windows-Computer. Später konfigurieren wir IPython für die Ausführung des Notizbuch-Servers auf dem Port 9999. Um diesen Port öffentlich zugänglich zu machen, müssen wir zunächst einen Endpunkt im Azure-Verwaltungsportal erstellen. Dieser Endpunkt öffnet einen Port in der Azure-Firewall und ordnet den öffentlichen Port (HTTPS, 443) dem privaten Port der VM (9999) zu.

Um einen Endpunkt zu erstellen, öffnen Sie das VM-Dashboard, klicken Sie auf "Endpunkte" und anschließend auf "Endpunkt hinzufügen", und erstellen Sie einen neuen Endpunkt (in diesem Beispiel mit dem Namen  `ipython_nb`). Wählen Sie TCP als Protokoll aus, 443 für den öffentlichen Port und 9999 für den privaten Port:

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

Installieren Sie [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] und andere IPython-Abhängigkeiten mit:

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Um IPython und dessen Abhängigkeiten auf der Windows-VM zu installieren, verbinden Sie sich per Remotedesktop mit der VM. Führen Sie anschließend die folgenden Schritte aus, indem Sie die Befehlszeilenaktionen in der Windows PowerShell ausführen.

**Hinweis:**: Sie müssen einige Sicherheitseinstellungen ändern, um Downloads mit dem Internet Explorer ausführen zu können.  Klicken Sie im **Server-Manager** auf **Lokaler Server**, anschließend auf **Verstärkte Sicherheitskonfiguration für IE** und deaktivieren Sie die Option für Administratoren.  Sie können diese Option nach der IPython-Installation wieder aktivieren.

1.  Installieren Sie Python 2.7.8 (32 Bit) von [python.org](http://www.python.org/download). 
    Sie müssen auch  `C:\Python27` und `C:\Python27\Scripts` zu Ihrer  `PATH`-
    Umgebungsvariablen hinzufügen.

1.  Installieren Sie Pip und Setuptools durch Herunterladen der Datei **get-pip.py**
    von [https://pip.pypa.io/en/latest/installing.html](https://pip.pypa.io/en/latest/installing.html) und führen Sie dann den folgenden
    Befehl aus:

        python get-pip.py

1.  Installieren Sie [Tornado][tornado] und [PyZMQ][pyzmq] sowie andere IPython-Abhängigkeiten durch:

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  Laden Sie [NumPy][numpy] herunter und installieren Sie es mit dem
    `.exe`-Binärinstallationsprogramm auf der zugehörigen Website.  Zum Zeitpunkt der Erstellung dieses Dokuments ist die neueste Version **numpy-1.9.0-win32-superpack-python2.7.exe**.

1.  Laden Sie [Matplotlib][matplotlib] herunter und installieren Sie es mit dem
    `.exe`-Binärinstallationsprogramm auf der zugehörigen Website.  Zum Zeitpunkt der Erstellung dieses Dokuments ist die neueste Version **matplotlib-1.4.0.win32-py2.7.exe**.

1.  Herunterladen und Installieren von OpenSSL. Sie finden Windows-Versionen von OpenSSL unter [http://slproweb.com/products/Win32OpenSSL.html](http://slproweb.com/products/Win32OpenSSL.html).

	* Falls Sie eine **Light**-Version installieren, müssen Sie auch **Visual C++ 2008 Redistributable** installieren (ebenfalls auf dieser Seite erhältlich).

	* Sie müssen  `C:\OpenSSL-Win32\bin` in Ihre  `PATH`-Umgebungsvariable aufnehmen.

	> [AZURE.NOTE] Installieren Sie unbedingt Version 1.0.1g oder höher von OpenSSL, da diese Versionen Schutz vor der Heartbleed-Sicherheitslücke bieten.

1.  Installieren Sie IPython mit dem folgenden Befehl:

        easy_install ipython

1.  Öffnen eines Ports in der Windows-Firewall.  Unter Windows Server 2012 sperrt die Firewall eingehende Verbindungen standardmäßig.  Führen Sie die folgenden Schritte aus, um Port 9999 zu öffnen:

    - Starten Sie die **Windows-Firewall mit erweiterter Sicherheit** aus dem Startbildschirm.

    - Klicken Sie im linken Bereich auf **Eingehende Regeln**.

	- Klicken Sie im Aktionsbereich auf **Neue Regel...**.

	- Klicken Sie im Assistenten für neue eingehende Regeln auf **Port**.

	- Wählen Sie im nächsten Bildschirm **TCP** aus und geben Sie **9999** unter **Bestimmte lokale Ports** ein.

	- Akzeptieren Sie die Standardwerte, geben Sie der Regel einen Namen und klicken Sie auf Fertig stellen.

### Konfigurieren von IPython Notebook

Nun werden wir IPython Notebook konfigurieren. Sie müssen zunächst ein benutzerdefiniertes
IPython-Konfigurationsprofil erstellen, um die Konfigurationsinformationen zu kapseln:

    ipython profile create nbserver

Anschließend wechseln wir mit  `cd` in das Profilverzeichnis, um unser SSL-Zertifikat zu erstellen und die Profil-Konfigurationsdatei zu bearbeiten.

Unter Linux (OpenSUSE):

    cd ~/.config/ipython/profile_nbserver/

Unter Linux (Ubuntu):

    cd ~/.ipython/profile_nbserver/

Unter Windows:

    cd \users\azureuser\.ipython\profile_nbserver

Erstellen Sie das SSL-Zertifikat unter beiden Plattformen wie folgt:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Da wir ein selbstsigniertes SSL-Zertifikat erstellen, erhalten Sie eine Sicherheitswarnung, wenn Sie mit Ihrem Browser das Notizbuch aufrufen.  Für den langfristigen Produktionseinsatz sollten Sie ein korrekt signiertes Zertifikat verwenden, das Ihrer Organisation zugeordnet ist.  Da die Zertifikatsverwaltung nicht Teil dieser Demonstration ist, werden wir ein selbstsigniertes Zertifikat verwenden.

Neben dem Zertifikat müssen Sie auch ein Kennwort angeben, um Ihr Notizbuch vor unbefugtem Zugriff zu schützen.  Aus Sicherheitsgründen verwendet IPython verschlüsselte Kennwörter in der Konfigurationsdatei, daher müssen Sie Ihr Kennwort zunächst verschlüsseln.  IPython verfügt über ein Hilfsprogramm zu diesem Zweck; führen Sie den folgenden Befehl in einer Eingabeaufforderung aus:

    python -c "import IPython;print IPython.lib.passwd()"

Daraufhin werden Sie aufgefordert, Ihr Kennwort zweimal einzugeben, und das Kennwort wird anschließend wie folgt ausgegeben:

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
Als Nächstes bearbeiten wir die Profil-Konfigurationsdatei. Dies ist die
Datei "ipython_notebook_config.py" im Profilverzeichnis, in dem Sie sich befinden. Beachten Sie, dass diese Datei möglicherweise nicht vorhanden ist. In diesem Fall erstellen Sie sie.  Diese Datei verfügt über einige Felder, und standardmäßig sind alle auskommentiert.  Öffnen Sie diese Datei in einem Text-Editor Ihrer Wahl und stellen Sie sicher, dass mindestens der folgende Text enthalten ist:

    c = get_config()
    
    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'
    
    # You must give the path to the certificate file.
    
    # If using a Linux VM (OpenSUSE):
    c.NotebookApp.certfile = u'/home/azureuser/.config/ipython/profile_nbserver/mycert.pem'

    # If using a Linux VM (Ubuntu):
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'
    
    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'
    
    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '
    
    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Ausführen von IPython Notebook

Nun können wir IPython Notebook starten. Navigieren Sie dazu in das Verzeichnis, in dem Sie Notizbücher speichern möchten, und führen Sie den IPython-Notizbuch-Server aus:

    ipython notebook --profile=nbserver

Jetzt sollten Sie in der Lage sein, unter der folgenden Adresse auf IPython Notebook zuzugreifen:
`https://[Your Chosen Name Here].cloudapp.net`.

Beim ersten Zugriff auf IPython Notebook müssen Sie auf der Anmeldungsseite Ihr Kennwort eingeben:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

Nach der Anmeldung sehen Sie das "IPython-Notizbuch-Dashboard", die Steuerzentrale für sämtliche Notizbuchoperationen.  Auf dieser Seite können Sie neue Notizbücher erstellen, bestehende Notizbücher öffnen usw.:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Wenn Sie auf die Schaltfläche "Neues Notizbuch" klicken, öffnet sich die folgende Seite:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

Der mit einer  `In []:` -Eingabeaufforderung markierte Bereich ist der Eingabebereich. Hier können Sie
einen gültigen Python-Code eingeben, der ausgeführt wird, wenn Sie  `Shift-Enter` drücken oder auf die Schaltfläche "Wiedergeben" klicken (das nach rechts zeigende Dreieck in der Symbolleiste).

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

* als Berechnungs-Notizblock für die Aufzeichnung von Forschungsdaten zu einem Problem,

* für die Weitergabe von Ergebnissen an Kollegen, entweder in Form von  'live'-Berechnungen oder als
  Kopie mit festem Format (HTML, PDF),

* zur Verteilung und Live-Präsentation von Unterrichtsmaterialien, die Berechnungen betreffen,
  damit die Studenten sofort mit dem tatsächlichen Code experimentieren, ihn ändern und
  interaktiv erneut ausführen können,

* um "ausführbare Paper" bereitzustellen, die die Forschungsergebnisse in einer Weise darstellen,
  die sofort reproduziert, validiert und von anderen Benutzern erweitert werden kann,

* als Plattform für gemeinschaftliche Berechnungen: Mehrere Benutzer können sich beim
  selben Notebook-Server anmelden, um eine Berechnungssitzung gemeinsam zu nutzen,

* und vieles mehr...

Im IPython-Quellcode-Repository finden Sie ein gesamtes Verzeichnis mit [Notizbuch-Beispielen](https://github.com/ipython/ipython/tree/master/examples/notebooks), die Sie herunterladen und anschließend auf Ihrer eigenen IPython-VM in Azure ausprobieren können.
Laden Sie einfach die  `.ipynb`-Dateien von der Website herunter und laden Sie sie anschließend im Dashboard Ihrer Notizbuch-VM in Azure hoch (bzw. laden Sie die Dateien direkt in die VM herunter).

## Zusammenfassung

IPython-Notizbücher bieten eine leistungsstarke Schnittstelle für die interaktive Nutzung des Funktionsumfangs des Python-Ökosystems in Azure.  Die Notizbücher decken ein breites Spektrum von Anwendungsfällen ab, inklusive Erkundung und Erlernen von Python, Datenanalyse und -Darstellung, Simulation und parallele Berechnungen. Die resultierenden Notizbuchdokumente enthalten eine komplette Aufzeichnung aller ausgeführten Berechnungen und können an andere IPython-Benutzer weitergegeben werden.  Das IPython-Notizbuch kann auch als lokale Anwendung verwendet werden, eignet sich jedoch ideal für Cloud-Bereitstellungen in Azure.

Die Kernfunktionen von IPython sind auch in Visual Studio über die 
[Python-Tools für Visual Studio](http://pytools.codeplex.com) (PTVS) verfügbar. PTVS ist ein kostenloses Open Source-Plug-In von Microsoft und verwandelt Visual Studio in eine umfangreiche Python-Entwicklungsumgebung mit einem modernen Editor inklusive IntelliSense, Debugging, Profilerstellung und Integration für parallele Berechnungen.



[tornado]:      http://www.tornadoweb.org/          "Tornado"
[PyZMQ]:        https://github.com/zeromq/pyzmq     "PyZMQ"
[NumPy]:        http://www.numpy.org/               "NumPy"
[Matplotlib]:   http://matplotlib.sourceforge.net/  "Matplotlib"

[portal-vm-windows]: /de-de/manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /de-de/manage/linux/tutorials/virtual-machine-from-gallery/




<!--HONumber=42-->
