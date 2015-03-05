<properties 
	pageTitle="IPython Notebook - Azure-Lernprogramm" 
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
	ms.date="02/05/2015" 
	ms.author="huvalo"/>





# IPython Notebook in Azure

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Das <a href="http://ipython.org">IPython-Projekt</a> enthält eine Sammlung von Tools für wissenschaftliche Berechnungen, die interaktive Shells, leistungsfähige und benutzerfreundliche parallele Bibliotheken und eine Web-basierte Umgebung namens IPython-Notizbuch enthält. Notebook bietet eine Arbeitsumgebung für interaktive Berechnungen, die Codeausführung mit der Erstellung eines Live-Berechnungsdokuments vereint. Diese Notebook-Dateien enthalten beliebigen Text, mathematische Formeln, Eingabecode, Ergebnisse, Grafiken, Videos und beliebige andere Medientypen, die in modernen Webbrowsern angezeigt werden können.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">Wiedergabe des Lernprogramms</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">Video wiedergeben</span></a> <span class="time">05:22</span></div>
</div>

Ganz gleich, ob Sie Python-Anfänger sind und es in einer aufregenden, 
interaktiven Umgebung lernen möchten oder einige ernsthafte parallele und technische Berechnungen durchführen wollen, das
IPython-Notebook ist eine gute Wahl. Als Veranschaulichung der Funktionen zeigt der
folgende Screenshot die Anwendung des IPython-Notebooks in Kombination mit
Scipy- und Matplotlib-Paketen zum Analysieren der Struktur einer Ton-
Aufnahme:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

In diesem Dokument erfahren Sie über die Bereitstellung des Python-Notebook auf Microsoft 
Azure, Linux oder Windows virtuelle Maschinen (VMs).  Durch Verwendung des IPython
Notebooks auf Azure können Sie eine webbasierte Schnittstelle für
skalierbare Rechenressourcen einfach bereitstellen, mit allen Funktionen von Python und seinen vielen
Bibliotheken.  Da jegliche Installation in der Cloud abgeschlossen wird, können Benutzer Zugriff auf diese
Ressourcen ohne die Notwendigkeit einer zusätzlichen lokalen Konfiguration jenseits eines einfachen modernen Web-
Browsers erhalten.

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Erstellen und Konfigurieren einer VM in Azure

Zunächst müssen Sie einen virtuellen Computer (VM) in Azure erstellen.
Dieser virtuelle Computer wird ein vollständiges Betriebssystem in der Cloud und wird verwendet, um
das IPython-Notebook zu beteiben. Azure kann sowohl auf Linux als auch Windows
virtuellen Computern ausgeführt werden, und es wird die Einrichtung von IPython auf beiden Arten von virtuellen Maschinen behandelt.

### Linux-VM

Befolgen Sie die [hier][portal-vm-linux] aufgeführten Anweisungen, um einen virtuellen Azure-Computer der  *OpenSUSE*- oder  *Ubuntu*-Verteilung zu erstellen. Dieses Lernprogramm verwendet OpenSUSE 13.2 und Ubuntu Server 14.04 LTS. Gehen wir vom Standardbenutzernamen *azureuser* aus.

### Windows-VM

Befolgen Sie die [hier][portal-vm-windows] aufgeführten Anweisungen, um einen virtuellen Azure-Computer der  *Windows Server 2012 R2 Datacenter*-Verteilung zu erstellen. In diesem Lernprogramm wird davon ausgegangen, dass der Benutzername  *azureuser* ist.

## Erstellen eines Endpunkts für IPython Notebook

Dieser Schritt gilt für virtuelle Linux- und Windows-Computer. Später wird konfiguriert, wie
IPython seinen Notebook-Server auf Port 9999 ausführt. Um diesen Port öffentlich
verfügbar zu machen, müssen wir einen Endpunkt in der Azure-Verwaltungsportal erstellen. Dieser
Endpunkt öffnet einen Port in der Azure-Firewall und ordnet die öffentlichen Port (HTTPS,
Port 443) den privaten Ports auf dem virtuellen Computer (9999) zu.

Erstellen Sie einen Endpunkt, besuchen Sie das VM-Dashboard, klicken Sie auf "Endpunkte", dann "Endpunkt
hinzufügen" und erstellen Sie einen neuen Endpunkt (namens `ipython_nb` in diesem Beispiel). Wählen Sie
TCP als Protokoll aus, für den öffentlichen Port 443 und 9999 für den privaten Port:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

Nach diesem Schritt sieht die Registerkarte "Endpunkte" im Dashboard folgendermaßen aus:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## Installieren der erforderlichen Software auf der VM

Um das IPython-Notizbuch auf unserer VM ausführen zu können, müssen wir zuerst IPython installieren und
seine Abhängigkeiten.

### Linux (OpenSUSE)

Führen Sie zur Installation von IPython und seinen Abhängigkeiten SSH mit Linux-VM und 
die folgenden Schritte aus.

Installieren Sie [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] und andere IPython-Abhängigkeiten mit:

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

IPython und seine Abhängigkeiten SSH mit Linux-VM installieren und führen Sie 
die folgenden Schritte aus.

Rufen Sie zunächst die neue Listen von Paketen auf:

    sudo apt-get update

Installieren Sie [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] und andere IPython-Abhängigkeiten mit:

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Um IPython und dessen Abhängigkeiten auf der Windows-VM zu installieren, verbinden Sie sich per Remotedesktop mit der VM. Führen Sie dann die folgenden Schritte aus, 
verwenden Sie dabei Windows PowerShell zum Ausführen von Aktionen für alle Aktionen der Befehlszeile.

**Hinweis**: Sie müssen einige Sicherheitseinstellungen ändern, um Downloads mit dem Internet Explorer ausführen zu können.  Von **Server-Manager**, klicken Sie auf **lokalen Server**, klicken Sie dann auf **verstärkte Sicherheitskonfiguration für IE** und deaktivieren Sie diese Option für Administratoren.  Sie können diese Option nach der IPython-Installation wieder aktivieren.

1.  Laden Sie die aktuelle 32-Bit-Version von [Python 2.7][] herunter und installieren Sie sie.  Sie müssen  `C:\Python27` and `C:\Python27\Scripts` zu Ihrer `PATH` Umgebungsvariable hinzufügen.

1.  Installieren Sie [Tornado][tornado] und [PyZMQ][pyzmq] und andere IPython-Abhängigkeiten mit:

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  Laden Sie [NumPy][numpy] mithilfe des  `.exe`-binärinstallationsprogramms auf der Website herunter und installieren Sie sie..  Zum Zeitpunkt der Erstellung dieses Dokuments ist die neueste Version **numpy-1.9.1-win32-superpack-python2.7.exe**.

1.  Installieren Sie [Matplotlib][matplotlib] durch praktische Übungen:

        pip install matplotlib==1.4.2

1.  Laden Sie [OpenSSL][] herunter und installieren Sie es.

	* Finden Sie das erforderliche **Visual C++ 2008 Redistributable** auf derselben Download-Seite.

	* Sie müssen  `C:\OpenSSL-Win32\bin` zu Ihrer `PATH`-Umgebungsvariable hinzufügen.

	> [AZURE.NOTE] Installieren Sie unbedingt Version 1.0.1g oder höher von OpenSSL, da diese Versionen Schutz vor der Heartbleed-Sicherheitslücke bieten.

1.  Installieren Sie IPython mit dem Befehl:

        pip install ipython==2.4

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

Danach  `cd` wir in das Profilverzeichnis, um unser SSL-Zertifikat zu erstellen und die 
die Profil-Konfigurationsdatei zu bearbeiten.

Unter Linux:

    cd ~/.ipython/profile_nbserver/

Unter Windows:

    cd \users\azureuser\.ipython\profile_nbserver

Erstellen Sie das SSL-Zertifikat (Linux und Windows) wie folgt:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Beachten Sie, dass, da wir ein selbstsigniertes SSL-Zertifikat erstellen, bei der Herstellung einer Verbindung 
zum Notebook Ihr Browser eine Sicherheitswarnung angezeigt.  Für langfristige
Verwendung in einer Produktionsumgebung sollten Sie ein ordnungsgemäß signiertes Zertifikat verwenden, das
Ihrer Organisation zugeordnet ist.  Da die Zertifikatsverwaltung nicht Gegenstand 
in dieser Demo ist, werden wir ein selbstsigniertes Zertifikat verwenden.

Neben der Verwendung eines Zertifikats müssen Sie auch ein Kennwort zum Schutz Ihres
Ihres Notebooks vor nicht autorisierter Verwendung schützen.  Aus Sicherheitsgründen verwendet IPython
verschlüsselte Kennwörter in der Konfigurationsdatei, daher müssen Sie zuerst Ihr
Kennwort verschlüsseln.  IPython verfügt über ein Hilfsprogramm zu diesem Zweck; führen Sie den folgenden Befehl in einer Eingabeaufforderung aus:

    python -c "import IPython;print IPython.lib.passwd()"

Dadurch werden Sie aufgefordert, ein Kennwort einzugeben und zu bestätigen und das
Kennwort wird wie folgt gedruckt:

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
Als Nächstes bearbeiten wir das Profil-Konfigurationsdatei, die die
`ipython_notebook_config.py` Datei im Verzeichnis ist.  Beachten Sie, dass diese Datei möglicherweise nicht vorhanden ist - erstellen Sie sie einfach.  Diese
Datei hat eine Anzahl von Feldern und standardmäßig sind allesamt auskommentiert.  Sie können 
diese Datei mit einem Text-Editor Ihrer Wahl öffnen und Sie sollten sicherzustellen, dass er 
mindestens den folgenden Inhalt hat:

    c = get_config()
    
    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'
    
    # You must give the path to the certificate file.
    
    # If using a Linux VM:
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

Nun können wir IPython Notebook starten. Zu diesem Zweck 
navigieren Sie zu dem Verzeichnis, in dem Sie Notebooks speichern und starten Sie den
IPython-Notebook-Server:

    ipython notebook --profile=nbserver

Jetzt sollten Sie in der Lage sein, unter der folgenden Adresse auf IPython Notebook zuzugreifen:
`https://[Ihr gewünschter Name hier]. cloudapp.net`.

Beim ersten Zugriff auf IPython Notebook müssen Sie auf der Anmeldungsseite Ihr Kennwort eingeben:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

Nachdem Sie sich angemeldet haben, sehen Sie das "IPython-Notebook Dashboard", das das 
Control-Center für alle Notebook-Vorgänge ist.  Auf dieser Seite können Sie 
neue Notebooks erstellen, bestehende öffnen usw.:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Wenn Sie auf die Schaltfläche "Neues Notizbuch" klicken, sehen Sie, wie sich die
Seite folgendermaßen öffnet:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

Der mit einer `In []:`-Eingabeaufforderung markierte Bereich ist der Eingabebereich. Hier können Sie
einen gültigen Python-Code eingeben und er wird bei der Eingabe von `Shift-Enter` ausgeführt oder
klicken Sie auf das Symbol "Play" (das nach rechts zeigendes Dreieck in der Symbolleiste).

Das Notebook ist so konfiguriert, dass es mit Numpy- und Matplotlib-Unterstützung
automatisch startet, Sie können auch Zahlen, erstellen z. B.:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## Ein mächtiges Paradigma: Live-Berechnungsdokumente mit Rich-Media-Objekten

Das Notizbuch sollte für jeden intuitiv bedienbar sein, der Python und
ein Textverarbeitungsprogramm verwendet hat, da es in gewisser Hinsicht eine Mischung aus beiden ist: Sie können
Blöcke von Python-Code ausführen, aber Sie können auch Notizen und anderen Text speichern, indem Sie 
das Format der Zelle von "Code" zu "Markdown" mithilfe des Dropdown-Menüs in der
Symbolleiste ausführen:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


Dies ist viel mehr als ein Textverarbeitungsprogramm, da das IPython-Notizbuch das
Mischen von Berechnungen und Rich Media (Text, Grafiken, Video und praktisch
alles, was in modernen Webbrowsern angezeigt werden kann) ermöglicht. Beispielsweise können Sie
Erklärungsvideos mit Berechnungen zu Lernzwecken mischen:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

oder externe Websites, die innerhalb einer Notebook-Datei live und nutzbar 
bleiben:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

Und mit der Leistungsfähigkeit von Pythons vielen hervorragenden Bibliotheken für wissenschaftliche und
technischen Berechnung kann eine einfache Berechnung kann ebenso leicht ausgeführt werden wie
eine komplexe Netzwerkanalyse, alles in einer Umgebung:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

Dieses Paradigma der Mischung aus moderner Web und live-Berechnung
bietet zahlreiche Vorzüge und eignet sich ideal für die Cloud das Notizbuch
kann auf folgende Weisen verwendet werden:

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

Wenn Sie den IPython-Quellcode aufrufen [Repository][], wird Ihnen ein ganzes
Verzeichnis mit Notizbuch-Beispielen angezeigt, das Sie herunterladen können, und dann können Sie mit Ihrer eigenen IPython-VM in Azure experimentieren.  Laden Sie einfach die `.ipynb` Dateien von der Website herunter und laden Sie die Dateien auf das Dashboard Ihres Notizbuch Azure-VM hoch(oder laden Sie sie direkt in die VM herunter).

## Zusammenfassung

Das IPython-Notizbuch bietet eine leistungsfähige Schnittstelle für den interaktiven Zugriff auf 
die Leistungsfähigkeit des Python-Ökosystems in Azure.  Es umfasst eine breite Palette von
Anwendungsbeispielen inklusive Erkundung und Erlernen von Python, Datenanalyse und
Visualisierung, Simulation und paralleler Berechnung. Die resultierenden Notebook-
Dokumente enthalten eine vollständige Aufzeichnung der Berechnungen, die ausgeführt werden, und
diese können für andere IPython-Benutzer freigegeben werden.  Das IPython-Notizbuch kann als eine
lokale Anwendung verwendet werden, aber es ist ideal für Cloud-Bereitstellungen in Azure geeignet

Die Kernfunktionen von IPython sind auch in Visual Studio über die 
[Python-Tools für Visual Studio][] (PTVS) verfügbar. PTVS ist ein kostenloses Open-Source-Plug-in 
von Microsoft, durch das Visual Studio zu einer erweiterten Python-Entwicklungs- 
Umgebung wird, das einen erweiterten Editor mit IntelliSense, debugging,  
Profilerstellung und paralleler Berechnungsintegration umfasst.



[Tornado]:      http://www.tornadoweb.org/          "Tornado"
[PyZMQ]:        https://github.com/zeromq/pyzmq     "PyZMQ"
[NumPy]:        http://www.numpy.org/               "NumPy"
[Matplotlib]:   http://matplotlib.sourceforge.NET/  "Matplotlib"

[portal-vm-windows]: /de-de/manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /de-de/manage/linux/tutorials/virtual-machine-from-gallery/

[Repository]: https://github.com/ipython/ipython
[Python-Tools für Visual Studio]: http://aka.ms/ptvs

[Python 2.7]: http://www.python.org/download
[OpenSSL]: http://slproweb.com/products/Win32OpenSSL.html

<!--HONumber=45--> 
