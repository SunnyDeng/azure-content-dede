<properties
	pageTitle="Erstellen eines Jupyter/IPython Notebooks | Microsoft Azure"
	description="In diesem Artikel erfahren Sie, wie Sie Jupyter/IPython Notebook auf einem virtuellen Linux-Computer bereitstellen, der mit dem Ressourcen-Manager-Bereitstellungsmodell in Azure erstellt wurde."
	services="virtual-machines"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""
	tags=“azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="python"
	ms.topic="article"
	ms.date="05/20/2015"
	ms.author="huvalo"/>

# Jupyter Notebook in Azure

Das [Jupyter-Projekt](http://jupyter.org), früher das [IPython-Projekt](http://ipython.org), bietet eine Sammlung von Tools für wissenschaftliche Berechnungen mithilfe leistungsstarker interaktiver Shells, die Codeausführung mit der Erstellung eines Live-Berechnungsdokuments vereinen. Diese Notebook-Dateien enthalten beliebigen Text, mathematische Formeln, Eingabecode, Ergebnisse, Grafiken, Videos und beliebige andere Medientypen, die in modernen Webbrowsern angezeigt werden können. Jupyter Notebook ist eine hervorragende Wahl, ganz gleich, ob Sie Python-Anfänger sind und die Sprache in einer interessanten und interaktiven Umgebung erlernen möchten, oder ob Sie ernsthafte parallele und technische Berechnungen durchführen möchten.

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png) Analysieren der Struktur einer Tonaufnahme mithilfe von SciPy- und Matplotlib-Paketen


## Zwei Möglichkeiten der Verwendung von Jupyter: Azure-Notebooks oder benutzerdefinierte Bereitstellung
Azure umfasst einen Dienst, den Sie für [den schnellen Einstieg in Jupyter](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx) verwenden können. Mit dem Azure Notebook-Dienst erhalten Sie schnell und einfach Zugriff auf die webbasierte Jupyter-Schnittstelle für skalierbare Rechenressourcen mit dem gesamten Funktionsumfang von Python und den zahlreichen Bibliotheken. Da die Installation über den Dienst erfolgt, können Benutzer auf diese Ressourcen zugreifen, ohne dass Verwaltung und Konfiguration erforderlich sind.

Wenn der Notebook-Dienst für Ihr Szenario nicht ausgeführt werden kann, finden Sie später in diesem Artikel Informationen zum Bereitstellen von Jupyter Notebook in Microsoft Azure unter Verwendung von virtuellen Linux-Computern.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Erstellen und Konfigurieren einer VM in Azure

Zunächst müssen Sie einen virtuellen Computer (VM) in Azure erstellen. Dieser virtuelle Computer ist ein komplettes Betriebssystem in der Cloud und wird für die Ausführung von Jupyter Notebook verwendet. Azure unterstützt sowohl virtuelle Linux- als auch virtuelle Windows-Computer. Daher wird die Einrichtung von Jupyter für beide virtuelle Computertypen erläutert.

### Erstellen eines virtuellen Linux-Computers und Öffnen eines Ports für Jupyter

Befolgen Sie die [hier][portal-vm-linux] aufgeführten Anweisungen, um einen virtuellen Computer der *Ubuntu*-Distribution zu erstellen. In diesem Lernprogramm wird Ubuntu Server 14.04 LTS verwendet. Wir gehen vom Standardbenutzernamen *azureuser* aus.

Nachdem der virtuelle Computer bereitgestellt wurde, muss eine Sicherheitsregel für die Netzwerksicherheitsgruppe geöffnet werden. Wechseln Sie im Portal zu **Netzwerksicherheitsgruppen**, und öffnen Sie die Registerkarte für die Sicherheitsgruppe, die Ihrem virtuellen Computer entspricht. Sie müssen eine Regel für die Sicherheit eingehender Verbindungen mit den folgenden Einstellungen hinzufügen: **TCP** für das Protokoll, ***** für den (öffentlichen) Quellport und **9999** für den (privaten) Zielport.

![Screenshot](./media/virtual-machines-python-ipython-notebook/azure-add-endpoint.png)

Klicken Sie in Ihrer Netzwerksicherheitsgruppe auf **Netzwerkschnittstellen**, und notieren Sie sich die **öffentliche IP-Adresse**, da diese im nächsten Schritt für die Verbindung zum virtuellen Computer benötigt wird.

## Installieren der erforderlichen Software auf der VM

Um Jupyter Notebook auf dem virtuellen Computer ausführen zu können, müssen zunächst Jupyter und die entsprechenden Abhängigkeiten installiert werden. Stellen Sie eine Verbindung mit dem virtuellen Linux-Computer her. Verwenden Sie dazu SSH und das Benutzername/Kennwort-Paar, das Sie beim Erstellen des virtuellen Computers gewählt haben. In diesem Lernprogramm wird PuTTY verwendet und die Verbindung über Windows hergestellt.

### Installieren von Jupyter unter Ubuntu
Installieren Sie Anaconda, eine beliebte Python-Distribution für Data Science, über einen der unter [Continuum Analytics](https://www.continuum.io/downloads) bereitgestellten Links. Zum Zeitpunkt der Erstellung dieses Dokuments waren die neuesten Versionen über die unten aufgeführten Links verfügbar.

#### Anaconda-Installationen für Linux
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64&#160;Bit</href>
	</td>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64&#160;Bit</href>
	</td>
  </tr>
  <tr>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32&#160;Bit</href>
	</td>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32&#160;Bit</href>
	</td>  
  </tr>
</table>


#### Installieren von Anaconda3 2.3.0 64 Bit unter Ubuntu
Es folgt ein Beispiel, wie Sie Anaconda unter Ubuntu installieren können.

	# install anaconda
	cd ~
	mkdir -p anaconda
	cd anaconda/
	curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
	sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

	# clean up home directory
	cd ..
	rm -rf anaconda/

	# Update Jupyter to the latest install and generate its config file
	sudo /anaconda3/bin/conda install -f jupyter -y
	/anaconda3/bin/jupyter-notebook --generate-config


![Screenshot](./media/virtual-machines-python-ipython-notebook/anaconda-install.png)


### Konfigurieren von Jupyter und Verwenden von SSL
Nach der Installation müssen die Konfigurationsdateien für Jupyter eingerichtet werden. Wenn Probleme beim Konfigurieren von Jupyter auftreten, finden Sie möglicherweise nützliche Hinweise in der [Jupyter-Dokumentation für die Ausführung eines Notebook-Servers](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html) (in englischer Sprache).

Anschließend wechseln wir mit `cd` in das Profilverzeichnis, um unser SSL-Zertifikat zu erstellen und die Profil-Konfigurationsdatei zu bearbeiten.

Verwenden Sie unter Linux den folgenden Befehl.

    cd ~/.jupyter

Verwenden Sie den folgenden Befehl, um das SSL-Zertifikat (Linux und Windows) zu erstellen.

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Da wir ein selbstsigniertes SSL-Zertifikat erstellen, erhalten Sie eine Sicherheitswarnung, wenn Sie mit Ihrem Browser das Notebook aufrufen. Für den langfristigen Produktionseinsatz sollten Sie ein korrekt signiertes Zertifikat verwenden, das Ihrer Organisation zugeordnet ist. Da die Zertifikatsverwaltung nicht Teil dieser Demonstration ist, werden wir ein selbstsigniertes Zertifikat verwenden.

Neben dem Zertifikat müssen Sie auch ein Kennwort angeben, um Ihr Notebook vor unbefugtem Zugriff zu schützen. Aus Sicherheitsgründen verwendet Jupyter verschlüsselte Kennwörter in der Konfigurationsdatei, daher müssen Sie Ihr Kennwort zunächst verschlüsseln. IPython verfügt über ein Hilfsprogramm zu diesem Zweck; führen Sie den folgenden Befehl in einer Eingabeaufforderung aus.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Dadurch werden Sie aufgefordert, ein Kennwort einzugeben und zu bestätigen. Anschließend wird das Kennwort ausgegeben. Notieren Sie sich das Kennwort für den nächsten Schritt.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Als Nächstes bearbeiten wir die Konfigurationsdatei des Profils. Hierbei handelt es sich um die Datei `jupyter_notebook_config.py` in dem Verzeichnis, in dem Sie sich befinden. Beachten Sie, dass diese Datei möglicherweise nicht vorhanden ist. In diesem Fall erstellen Sie sie einfach. Diese Datei verfügt über einige Felder, und standardmäßig sind alle auskommentiert. Öffnen Sie diese Datei in einem Text-Editor Ihrer Wahl und stellen Sie sicher, dass mindestens der folgende Text enthalten ist. Ersetzen Sie das Kennwort durch den SHA1 aus dem vorherigen Schritt.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed :wqtraffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Ausführen von Jupyter Notebook

Nun können wir Jupyter Notebook starten. Navigieren Sie dazu in das Verzeichnis, in dem Sie Notebooks speichern möchten, und führen Sie den Jupyter Notebook-Server mit dem folgenden Befehl aus.

    /anaconda3/bin/jupyter-notebook

Jetzt sollten Sie in der Lage sein, unter der Adresse `https://[PUBLIC-IP-ADDRESS]:9999` auf Jupyter Notebook zuzugreifen.

Beim ersten Zugriff auf IPython Notebook müssen Sie auf der Anmeldungsseite Ihr Kennwort eingeben. Nach der Anmeldung sehen Sie das Jupyter Notebook-Dashboard, d. h. die Steuerzentrale für sämtliche Notebook-Vorgänge. Auf dieser Seite können Sie neue Notebooks erstellen und bestehende Notebooks öffnen.

![Screenshot](./media/virtual-machines-python-ipython-notebook/jupyter-tree-view.png)

### Verwenden von Jupyter Notebook

Wenn Sie auf die Schaltfläche **Neu** klicken, wird die folgende Seite geöffnet.

![Screenshot](./media/virtual-machines-python-ipython-notebook/jupyter-untitled-notebook.png)

In dem mit der `In []:`-Eingabeaufforderung gekennzeichneten Bereich können Sie gültigen Python-Code eingeben und diesen ausführen, indem Sie `Shift-Enter` drücken oder auf das "Wiedergabe"-Symbol klicken (ein nach rechts zeigendes Dreieck auf der Symbolleiste).

## Ein mächtiges Paradigma: Live-Berechnungsdokumente mit Rich-Media-Objekten

Das Notebook ist sehr intuitiv für Benutzer, die mit Python und Textverarbeitungsprogrammen vertraut sind, da es sich in gewisser Weise um beides handelt: Sie können Blöcke von Python-Code ausführen und gleichzeitig Notizen und anderen Text erstellen, indem Sie den Stil einer Zelle im Dropdownmenü der Symbolleiste von "Code" zu "Markdown" ändern.

Jupyter ist weit mehr als ein reines Textverarbeitungsprogramm, da Sie mit Jupyter Berechnungen und Rich-Media-Inhalte (Text, Grafiken, Videos und praktisch alle in einem modernen Browser darstellbaren Elemente) kombinieren können. Sie können Text, Code, Videos und vieles mehr kombinieren!

![Screenshot](./media/virtual-machines-python-ipython-notebook/jupyter-editing-experience.png)

Mit den vielen hervorragenden Python-Bibliotheken für wissenschaftliche und technische Berechnungen können Sie wie im folgenden Screenshot dargestellt einfache Berechnungen ebenso leicht in einer einzigen Umgebung umsetzen wie komplexe Netzwerkanalysen.

Dieses Paradigma der Mischung aus modernem Web und Live-Berechnungen bietet zahlreiche Vorzüge und eignet sich hervorragend für die Cloud. Notebooks können zu folgenden Zwecken verwendet werden:

* Als Berechnungs-Notizblock für die Aufzeichnung von Erforschungsdaten zu einem Problem.

* Für die Weitergabe von Ergebnissen an Kollegen, entweder in Form von Live-Berechnungen oder als Kopie mit festem Format (HTML, PDF).

* Für die Verteilung und Präsentation von Live-Unterrichtsmaterialien inklusive Berechnungen, anhand derer die Studenten mit dem tatsächlichen Code experimentieren, diesen verändern und sofort erneut ausführen können.

* Für die Bereitstellung von "ausführbaren Arbeiten", die Forschungsergebnisse auf eine Weise präsentieren, die von anderen sofort nachvollzogen, validiert und erweitert werden können.

* Als Plattform für gemeinschaftliche Berechnungen: Mehrere Benutzer können sich bei einem Notebook-Server anmelden und eine Live-Berechnungssitzung gemeinsam nutzen.


Im IPython-Quellcode-[Repository][] finden Sie ein gesamtes Verzeichnis mit Notebook-Beispielen, die Sie herunterladen und anschließend auf Ihrem eigenen virtuellen Jupyter-Computer in Azure ausprobieren können. Laden Sie einfach die `.ipynb`-Dateien von der Website herunter, und laden Sie die Dateien auf das Dashboard Ihrer Notebook-Azure-VM hoch(oder laden Sie sie direkt in die VM herunter).

## Zusammenfassung

Jupyter Notebook bietet eine leistungsstarke Schnittstelle für die interaktive Nutzung des Funktionsumfangs des Python-Ökosystems in Azure. Die Notebooks decken ein breites Spektrum von Anwendungsfällen ab, inklusive Erkundung und Erlernen von Python, Datenanalyse und -Darstellung, Simulation und parallele Berechnungen. Die resultierenden Notebook-Dokumente enthalten eine komplette Aufzeichnung aller ausgeführten Berechnungen und können an andere Jupyter-Benutzer weitergegeben werden. Jupyter Notebook kann als lokale Anwendung verwendet werden, eignet sich jedoch vor allem ideal für Cloudbereitstellungen in Azure.

Die Kernfunktionen von Jupyter sind auch in Visual Studio unter [Python Tools for Visual Studio][] (PTVS) verfügbar. PTVS ist ein kostenloses Open Source-Plug-In von Microsoft und verwandelt Visual Studio in eine umfangreiche Python-Entwicklungsumgebung mit einem modernen Editor inklusive IntelliSense, Debugging, Profilerstellung und Integration für parallele Berechnungen.

## Nächste Schritte

Weitere Informationen finden Sie im [Python Developer Center](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/de-DE/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[repository]: https://github.com/ipython/ipython
[Python Tools for Visual Studio]: http://aka.ms/ptvs

<!---HONumber=Oct15_HO3-->