<properties linkid="develop-python-ipython-notebook" urlDisplayName="IPython Notebook" pageTitle="IPython Notebook - Azure tutorial" metaKeywords="" description="A tutorial that shows how to deploy the IPython Notebook on Azure, using Linux or Windows virtual machines (VMs)." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="IPython Notebook on Azure" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# IPython Notebook in Azure

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Das <a href="http://ipython.org">IPython-Projekt</a> bietet eine Sammlung von Tools f&uuml;r wissenschaftliche Berechnungen inklusive umfangreicher interaktiver Shells, leistungsstarker und leicht einsetzbarer paralleler Bibliotheken und einer webbasierten Umgebung mit dem Namen IPython Notebook. Notebook bietet eine Arbeitsumgebung f&uuml;r interaktive Berechnungen, die Codeausf&uuml;hrung mit der Erstellung eines Live-Berechnungsdokuments vereint. Diese Notebook-Dateien enthalten beliebigen Text, mathematische Formeln, Eingabecode, Ergebnisse, Grafiken, Videos und beliebige andere Medientypen, die in modernen Webbrowsern angezeigt werden k&ouml;nnen.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">05:22</span></div>

</div>

IPython Notebook ist eine hervorragende Wahl, egal ob Sie Python-Anfänger sind und die Sprache in einer interessanten und
interaktiven Umgebung erlernen möchten, oder
ob Sie parallele und technische Berechnungen durchführen möchten. Der folgende Screenshot verdeutlicht diese
Möglichkeiten und zeigt IPython Notebook in Kombination mit den
SciPy- und matplotlib-Paketen, um die Struktur einer Tonaufnahme zu
analysieren:

![Screenshot][Screenshot]

Dieses Dokument beschreibt die Bereitstellung von IPython Notebook in Windows
Azure mithilfe von virtuellen Linux- oder Windows-Computern (VMs). Mit IPython
Notebook in Azure können Sie schnell und einfach webbasierte Schnittstellen für
skalierbare Serverressourcen mit dem gesamten Funktionsumfang von Python und den
zahlreichen Bibliotheken erstellen. Da die gesamte Installation in der Cloud erfolgt, können Benutzer diese
Ressourcen nutzen, ohne über einen gängigen
Webbrowser hinaus lokale Konfigurationsschritte durchführen zu müssen.

[WACOM.INCLUDE [create-account-and-vms-note][create-account-and-vms-note]]

## Erstellen und Konfigurieren einer VM in Azure

Der erste Schritt besteht darin, einen virtuellen Computer (Virtual Machine, VM) unter Azure zu erstellen.
Dieser virtuelle Computer bildet ein vollständiges Betriebssystem in der Cloud ab und wird zum Ausführen von
IPython Notebook verwendet. Azure unterstützt sowohl virtuelle Linux- als auch virtuelle
Windows-Computer. Deshalb werden wir die Einrichtung von IPython für beide virtuelle Computertypen erläutern.

### Linux-VM

Führen Sie [diese Anweisungen][diese Anweisungen] aus, um einen virtuellen Computer mit einer *OpenSUSE*- oder *Ubuntu*-Distribution zu erstellen. Dieses Lernprogramm verwendet OpenSUSE 12.3 und Ubuntu Server 13.04. Wir gehen vom Standard-Benutzernamen *azureuser* aus.

### Windows-VM

Führen Sie [diese Anweisungen][1] aus, um einen virtuellen Computer mit einer *Windows Server 2012 Datacenter*-Distribution zu erstellen. In diesem Lernprogramm gehen wir vom Standard-Benutzernamen *azureuser* aus.

## Erstellen eines Endpunkts für IPython Notebook

Dieser Schritt gilt für virtuelle Linux- und Windows-Computer. Später konfigurieren wir
IPython für die Ausführung des Notebook-Servers an Port 9999. Damit dieser Port öffentlich
verfügbar ist, müssen wir einen Endpunkt im Azure-Verwaltungsportal erstellen. Dieser
Endpunkt öffnet einen Port in der Azure-Firewall und ordnet den öffentlichen Port (HTTPS,
443) dem privaten Port der VM (9999) zu.

Um einen Endpunkt zu erstellen, öffnen Sie das VM-Dashboard, klicken Sie auf "Endpunkte" und anschließend auf "Endpunkt
hinzufügen", und erstellen Sie einen neuen Endpunkt (in diesem Beispiel mit dem Namen `ipython_nb`). Wählen
Sie TCP als Protokoll, 443 für den öffentlichen Port und 9999 für den privaten Port aus:

![Screenshot][2]

Nach diesem Schritt sieht die Registerkarte "Endpunkte" im Dashboard folgendermaßen aus:

![Screenshot][3]

## Installieren der erforderlichen Software auf der VM

Um IPython Notebook auf unserer VM ausführen zu können, müssen wir zunächst
Python und die entsprechenden Abhängigkeiten installieren.

### Linux (OpenSUSE)

Um IPython und dessen Abhängigkeiten zu installieren, verbinden Sie sich per SSH mit der Linux-VM,
und führen Sie die folgenden Schritte durch.

Installieren Sie [NumPy][NumPy], [Matplotlib][Matplotlib], [Tornado][Tornado] und andere IPython-Abhängigkeiten mit den folgenden Befehlen:

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install ipython

### Linux (Ubuntu)

Um IPython und dessen Abhängigkeiten zu installieren, verbinden Sie sich per SSH mit der Linux-VM,
und führen Sie die folgenden Schritte durch.

Installieren Sie [NumPy][NumPy], [Matplotlib][Matplotlib], [Tornado][Tornado] und andere IPython-Abhängigkeiten mit den folgenden Befehlen:

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Um IPython und dessen Abhängigkeiten auf der Windows-VM zu installieren, verbinden Sie sich per Remotedesktop mit der VM. Führen Sie anschließend die folgenden Schritte aus,
indem Sie alle Befehlszeilenaktionen in der Windows PowerShell ausführen.

**Hinweis**: Sie müssen einige Sicherheitseinstellungen ändern, um Downloads mit dem Internet Explorer ausführen zu können. Klicken Sie im **Server-Manager** auf **Lokaler Server**, anschließend auf **Verstärkte Sicherheitskonfiguration für IE** und deaktivieren Sie die Option für Administratoren. Sie können diese Option nach der IPython-Installation wieder aktivieren.

1.  Installieren Sie Python 2.7.5 (32-Bit) von [python.org][python.org].
    Sie müssen Ihrer `PATH`
    -Umgebungsvariable außerdem auch `C:\Python27` und `C:\Python27\Scripts` hinzufügen.

2.  Installieren Sie "distribute", indem Sie die Datei **distribute\_setup.py**
     von [python-distribute.org][python-distribute.org] herunterladen und anschließend den folgenden
    Befehl ausführen:

        python distribute_setup.py

3.  Installieren Sie [Tornado][Tornado] und [PyZMQ][PyZMQ] mit den folgenden Befehlen:

        easy_install tornado
        easy_install pyzmq

4.  Laden Sie [NumPy][NumPy] herunter, und installieren Sie das Paket mit dem
    `.exe`-Installationsprogramm für Binärdateien von der Website. Zum Zeitpunkt der Erstellung dieses Dokuments ist die neueste Version **numpy-1.7.1-win32-superpack-python2.7.exe**.

5.  Laden Sie [Matplotlib][Matplotlib] herunter, und installieren Sie es mit dem
    `.exe`-Installationsprogramm für Binärdateien von der Website. Zum Zeitpunkt der Erstellung dieses Dokuments ist die neueste Version **matplotlib-1.2.1.win32-py2.7.exe**.

6.  Herunterladen und Installieren von OpenSSL. Sie finden Windows-Versionen von OpenSSL unter [][]<http://slproweb.com/products/Win32OpenSSL.html></a>.

    -   Falls Sie eine **Light**-Version installieren, müssen Sie auch **Visual C++ 2008 Redistributable** installieren (ebenfalls auf dieser Seite erhältlich).

    -   Sie müssen `C:\OpenSSL-Win32\bin` in Ihre `PATH`-Umgebungsvariable aufnehmen.

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

### Konfigurieren von IPython Notebook

Nun werden wir IPython Notebook konfigurieren. Zunächst müssen wir ein benutzerdefiniertes
IPython-Konfigurationsprofil erstellen, um die Konfigurationsinformationen zu kapseln:

    ipython profile create nbserver

Anschließend wechseln wir mit `cd` in das Profilverzeichnis, um unser SSL-Zertifikat zu erstellen und die
Profil-Konfigurationsdatei zu bearbeiten.

Unter Linux (OpenSUSE):

    cd ~/.config/ipython/profile_nbserver/

Unter Linux (Ubuntu):

    cd ~/.ipython/profile_nbserver/

Unter Windows:

    cd \users\azureuser\.ipython\profile_nbserver

Erstellen Sie das SSL-Zertifikat unter beiden Plattformen wie folgt:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Da wir ein selbstsigniertes SSL-Zertifikat erstellen, erhalten Sie eine Sicherheitswarnung,
wenn Sie mit Ihrem Browser IPython Notebook aufrufen. Für den langfristigen
Produktionseinsatz sollten Sie ein korrekt signiertes Zertifikat verwenden,
das Ihrer Organisation zugeordnet ist. Da die Zertifikatverwaltung hier nicht erörtert wird,
werden wir ein selbstsigniertes Zertifikat verwenden.

Neben dem Zertifikat müssen Sie auch ein Kennwort angeben, um Notebook
vor unbefugtem Zugriff zu schützen. Aus Sicherheitsgründen verwendet IPython
verschlüsselte Kennwörter in der Konfigurationsdatei, daher müssen Sie Ihr
Kennwort zunächst verschlüsseln. IPython verfügt über ein Hilfsprogramm zu diesem Zweck; führen Sie den folgenden Befehl in einer Eingabeaufforderung aus:

    python -c "import IPython;print IPython.lib.passwd()"

Daraufhin werden Sie aufgefordert, Ihr Kennwort zweimal einzugeben. Das
Kennwort wird anschließend wie folgt ausgegeben:

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Nun werden wir die Profilkonfigurationsdatei
`ipython_notebook_config.py` im aktuellen Verzeichnis bearbeiten. Diese Datei enthält eine
Reihe von Feldern, die standardmäßig alle auskommentiert sind. Öffnen Sie diese
Datei in einem Text-Editor Ihrer Wahl, und stellen Sie sicher, dass
mindestens der folgende Text enthalten ist:

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

Nun können wir IPython Notebook starten. Navigieren Sie
dazu in das Verzeichnis, in dem Sie Notebooks speichern möchten, und führen Sie den
IPython Notebook-Server aus:

    ipython notebook --profile=nbserver

Jetzt sollten Sie in der Lage sein, unter der Adresse
`https://[Your Chosen Name Here].cloudapp.net` auf IPython Notebook zuzugreifen.

Beim ersten Zugriff auf IPython Notebook müssen Sie auf der Anmeldungsseite Ihr Kennwort eingeben:

![Screenshot][4]

Nach der Anmeldung sehen Sie das "IPython-Notebook-Dashboard", die
Steuerzentrale für sämtliche Notebook-Vorgänge. Auf dieser Seite können Sie neue
Notebooks erstellen, bestehende öffnen usw.:

![Screenshot][5]

Wenn Sie auf die Schaltfläche "New Notebook" klicken, öffnet sich die
folgende Seite:

![Screenshot][6]

In dem mit der `In []:`-Eingabeaufforderung gekennzeichneten Bereich können Sie
gültigen Python-Code eingeben und diesen ausführen, indem Sie `Shift-Enter` drücken oder
auf das "Wiedergabe"-Symbol klicken (ein nach rechts zeigendes Dreieck auf der Symbolleiste).

Da wir IPython Notebook für den automatischen Start mit NumPy- und matplotlib-Unterstützung
konfiguriert haben, können Sie sogar Diagramme erstellen, z. B.:

![Screenshot][7]

## Ein leistungsstarkes Beispiel: Dokumente mit Liveberechnung und Rich-Media-Inhalte

IPython Notebook sollte sich für jeden, der Python zusammen mit einem
Textverarbeitungsprogramm einsetzt, als vollkommen natürliche Verbindung erweisen: zum einen lassen sich
Python-Codeblöcke ausführen und zum anderen Notizen und sonstiger Text speichern, indem der
Stil einer Zelle über das Dropdownmenü der Symbolleiste von "Code" in "Markdown"
geändert wird:

![Screenshot][8]

Es handelt sich jedoch um mehr als reine Textverarbeitung, da Sie mit
IPython Notebook Berechnungen und Rich-Media-Inhalte (Text, Grafiken, Video und
praktisch alle in einem modernen Browser darstellbaren Elemente) kombinieren können. Für den schulischen Bereich können Sie zum Beispiel
Demonstrationsvideos mit Berechnungen kombinieren:

![Screenshot][9]

oder externe Websites, die weiterhin live nutzbar bleiben, in eine Notebook-
Datei einbetten:

![Screenshot][10]

Mit den vielen hervorragenden Python-Bibliotheken für wissenschaftliche und
technische Berechnungen können Sie einfache Berechnungen ebenso leicht in einer
einzigen Umgebung umsetzen wie komplexe Netzwerkanalysen:

![Screenshot][11]

Dieses Beispiel für die Kombination aus modernem Web und Liveberechnungen
bietet zahlreiche Vorzüge und eignet sich hervorragend für die Cloud. IPython Notebook kann
wie folgt eingesetzt werden:

-   als Berechnungs-Notizblock für die Aufzeichnung von Forschungsdaten zu einem Problem,

-   für die Weitergabe von Ergebnissen an Kollegen, entweder in Form von Liveberechnungen oder als
    Kopie mit festem Format (HTML, PDF),

-   für die Verteilung und Präsentation von Liveunterrichtsmaterialien inklusive Berechnungen,
    anhand derer die Studenten mit dem tatsächlichen Code experimentieren,
    diesen verändern und sofort erneut ausführen können,

-   für die Bereitstellung von "ausführbaren Arbeiten", die
    Forschungsergebnisse auf eine Weise präsentieren, die von anderen sofort nachvollzogen, validiert und erweitert werden können.

-   als gemeinsame Rechenplattform: Mehrere Benutzer können sich für gemeinsame Liveberechnungen beim selben
     Notebook-Server anmelden,

-   und vieles mehr...

Im IPython-Quellcoderepository finden Sie ein ganzes
Verzeichnis mit [Notebook-
Beispielen][Notebook-
Beispielen]
, die Sie auf Ihre eigene Azure IPython-VM herunterladen und dort ausprobieren können.
Laden Sie einfach die `.ipynb`-Dateien von der Website herunter, und laden Sie sie in das
Dashboard der Notebook Azure-VM (bzw. direkt auf ihre VM).

## Zusammenfassung

IPython Notebook bietet eine leistungsstarke Schnittstelle für die interaktive
Nutzung des Funktionsumfangs des Python-Ökosystems in Azure. Es deckt ein breites Spektrum von
Anwendungsfällen ab, inklusive Erkundung und Erlernen von Python, Datenanalyse und
-visualisierung, Simulation und parallele Berechnungen. Die resultierenden Notebook-
Dokumente enthalten eine komplette Aufzeichnung aller ausgeführten Berechnungen und
können an andere IPython-Benutzer weitergegeben werden. IPython Notebook kann auch als lokale
Anwendung verwendet werden, eignet sich jedoch ideal für Cloud-Bereitstellungen in Azure.

Die Kernfunktionen von IPython sind auch in Visual Studio unter
[Python Tools for Visual Studio][Python Tools for Visual Studio] (PTVS) verfügbar. PTVS ist ein kostenloses Open Source-Plug-In von
Microsoft und verwandelt Visual Studio in eine funktionsreiche Python-
Entwicklungsumgebung, die einen innovativen Editor mit IntelliSense, Debugging,
Profilerstellung und Integration paralleler Berechnungen umfasst.

  [IPython-Projekt]: http://ipython.org
  [Lernprogramm ansehen]: http://go.microsoft.com/fwlink/?LinkID=254535&clcid=0x409
  [Screenshot]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [diese Anweisungen]: /de-de/manage/linux/tutorials/virtual-machine-from-gallery/
  [1]: /de-de/manage/windows/tutorials/virtual-machine-from-gallery/
  [2]: ./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png
  [3]: ./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png
  [NumPy]: http://www.numpy.org/ "NumPy"
  [Matplotlib]: http://matplotlib.sourceforge.net/ "Matplotlib"
  [Tornado]: http://www.tornadoweb.org/ "Tornado"
  [python.org]: http://www.python.org/download
  [python-distribute.org]: http://python-distribute.org/
  [PyZMQ]: https://github.com/zeromq/pyzmq "PyZMQ"
  []: http://slproweb.com/products/Win32OpenSSL.html
  [4]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png
  [5]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png
  [6]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png
  [7]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png
  [8]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png
  [9]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png
  [10]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png
  [11]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png
  [Notebook-
  Beispielen]: https://github.com/ipython/ipython/tree/master/examples/notebooks
  [Python Tools for Visual Studio]: http://pytools.codeplex.com
