<properties linkid="develop-python-install-python" urlDisplayName="Install Python" pageTitle="Install Python and the SDK - Azure" metaKeywords="Azure Python SDK" description="Learn how to install Python and the SDK to use with Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="" solutions="" manager="" editor="" />

Installieren von Python und SDK
===============================

Die Installation von Python auf Windows ist sehr einfach. Bei Mac- und Linux-Systemen ist sie bereits vorinstalliert. Dieser Leitfaden führt Sie durch die Installation und bereitet Ihren Computer für den Einsatz mit Azure vor. Dieser Leitfaden unterstützt Sie bei den folgenden Punkten:

-   Was umfasst das Python Azure SDK?
-   Welche Python und welche Version sollte verwendet werden?
-   Installation auf Windows
-   Installation auf Mac und Linux

Was umfasst das Python Azure SDK?
---------------------------------

Das Azure SDK für Python enthält Komponenten, mit deren Hilfe Sie Python-Anwendungen für Azure entwickeln, bereitstellen und verwalten können. Insbesondere enthält das Azure SDK für Python Folgendes:

-   **Die Python-Clientbibliotheken für Azure**. Diese Basisklassenbibliotheken bieten eine Schnittstelle für den Zugriff auf Azure-Funktionen wie Datenverwaltungs- und Clouddienste.
-   **Die Azure-Befehlszeilentools für Mac und Linux**. Dies ist eine Gruppe von Befehlszeilentools zum Bereitstellen und Verwalten von Azure-Diensten wie Azure-Websites und virtuellen Azure-Computern. Diese Tools arbeiten auf jeder Plattform einschließlich Mac, Linux und Windows.
-   **PowerShell für Azure (nur Windows)**. Dies ist eine Gruppe von PowerShell-Cmdlets zum Bereitstellen und Verwalten von Azure-Diensten wie Clouddiensten und virtuellen Computern.
-   **Die Azure-Emulatoren (nur Windows)**. Die Rechen- und Speicheremulatoren sind lokale Emulatoren von Datenverwaltungs- und Clouddiensten, mit denen Sie eine Anwendung lokal testen können. Die Azure-Emulatoren laufen nur unter Windows.

Die Hauptszenarien für diese Versionen sind:

-   **Windows**: Clouddienst – beispielsweise eine Django-Website, die WebRoles verwendet
-   **Mac/Linux**: IaaS – Ausführen beliebiger Anwendungen auf einem virtuellen Computer, Nutzung von Azure Services über Python

Welche Python und welche Version sollte verwendet werden?
---------------------------------------------------------

Verschiedene Ausführungen von Python-Übersetzern sind verfügbar. Einige Beispiele:

-   CPython – der standardmäßige und am häufigsten verwendete Python-Übersetzer
-   IronPython – Python-Übersetzer, der auf .Net/CLR läuft
-   Jython – Python-Übersetzer, der auf der JVM läuft

Für dieser Version wurde nur **CPython** getestet und wird unterstützt. Wir empfehlen außerdem mindestens Version 2.7 zu verwenden. In naher Zukunft wird auch **IronPython** unterstützt werden.

Wo erhalte ich Python?
----------------------

Es gibt mehrere Möglichkeiten, CPython zu beziehen:

-   Direkt unter www.python.org
-   Von angesehenen Distributoren wie www.enthought.com oder www.ActiveState.com
-   Aus der Source erzeugen!

Sofern Sie keine spezifischen Anforderungen haben, empfehlen wir Ihnen die ersten beiden Optionen, wie unten beschrieben.

Installation auf Windows
------------------------

Für Windows können Sie den zur Verfügung gestellten [WebPI Installer](http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409) des Python Developer Center verwenden, um die Installation zu vereinfachen. CPython wird dann von www.python.org bezogen.

**Hinweis:** Unter Windows Server müssen Sie möglicherweise die Einstellungen für die verstärkte Sicherheitskonfiguration für IE konfigurieren, um den WebPI Installer herunterzuladen (Start/Adminstrative Tools/Server-Manager, klicken Sie auf **Verstärkte Sicherheitskonfiguration für IE konfigurieren**, deaktivieren Sie die Option).

![how-to-install-python-webpi-1](./media/python-how-to-install/how-to-install-python-webpi-1.png)

Der WebPI Installer bietet alles, was Sie für Python Azure-Apps benötigen sowie spezifischen Support für Django-Apps:

![how-to-install-Python-webpi-2](./media/python-how-to-install/how-to-install-python-webpi-2.png)

Sobald Sie die Installation abgeschlossen haben, wird folgender Bildschirm angezeigt, der Ihre Installationsoptionen bestätigt:

![how-to-install-python-webpi-3](./media/python-how-to-install/how-to-install-python-webpi-3.png)

Sobald die Installation abgeschlossen ist, geben Sie "python" in die Eingabeaufforderung ein, um sicherzustellen, dass alles reibungslos funktioniert hat. Je nachdem, welche Installationsmöglichkeit Sie verwendet haben, müssen Sie eventuell Ihre Pfadvariable entsprechend festlegen, damit (die richtige Version von) Python gefunden wird:

![how-to-install-python-win-run](./media/python-how-to-install/how-to-install-python-win-run.png)

Während es bei dieser Version primär um Web-Apps mit Django geht, finden Sie im [Python Package Index (PyPI)](http://pypi.python.org/pypi) eine umfassende Auswahl anderer Software. Wenn Sie eine Distribution installieren, verfügen Sie bereits über die wichtigsten Bits für eine Vielzahl an Szenarien von Webentwicklung bis hin zu technischem Computing.

Wenn Sie wissen möchten, welche Python-Pakete in **site-packages** installiert sind, geben Sie Folgendes ein, um den Speicherort zu ermitteln:

![how-to-install-python-win-site](./media/python-how-to-install/how-to-install-python-win-site.png)

Sie erhalten dann eine Liste mit den auf Ihrem System installierten Paketen.

Nach der Installation sollten Ihnen Python, Django und die Clientbibliotheken am Standartort zur Verfügung stehen:

     	C:\Python27\Lib\site-packages\windowsazure
        C:\Python27\Lib\site-packages\django

### Python Tools for Visual Studio

Python Tools for Visual Studio ist ein kostenloses/OSS-Plugin von Microsoft, das VS zu einer vollwertigen Python-IDE macht:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Die Verwendung von Python Tools for Visual Studio ist optional aber empfehlenswert, da Sie so bei Projekten/Lösungen von Python und Django von Support, Debugging, Profilerstellung, Vorlagenbearbeitung und IntelliSense, Bereitstellung in der Cloud usw. profitieren. Dieses Add-In funktioniert mit Ihrer vorhandenen VS2010-Installation. Falls Sie nicht über VS2010 verfügen, installiert WebPI die kostenlose Integrated Shell + PTVS, die Ihnen eine **völlig kostenlose** "VS Python Express"-IDE bietet. Weitere Informationen finden Sie unter [Python Tools for Visual Studio auf CodePlex](http://pytools.codeplex.com) (in englischer Sprache).

Hinweis: Während das PTVS-Plug-In klein ist, benötigen Sie für das Integrated Shell eine längere Downloadzeit. Die Integrated Shell-Version unterstützt außerdem derzeit die Funktion "Add Azure Deployment Project" nicht.

Deinstallation auf Windows
--------------------------

Das WebPI-Produkt **Azure SDK for Python June 2012** ist keine Anwendung im herkömmlichen Sinn, sondern vielmehr eine Sammlung verschiedener Produkte, wie Python 2.7 (32 Bit), Azure-Client-APIs für Python, Django usw., die in einem Paket zusammengefasst sind. Daher gibt es für das Produkt auch kein konventionelles Deinstallationsprogramm. Sie müssen die Programme, die von dem Produkt installiert werden, einzeln in der Windows-Systemsteuerung deinstallieren.

Falls Sie **Azure SDK for Python** einmal neu installieren möchten, öffnen Sie einfach eine PowerShell-Eingabeaufforderung, und führen Sie den folgenden Befehl aus:

    rm -force "HKLM:\SOFTWARE\Microsoft\Windows Azure SDK for Python - June 2012"

Führen Sie WebPI dann erneut aus.

Installation auf Linux und MacOS
--------------------------------

Python ist höchstwahrscheinlich bereits auf Ihrem Entwicklungscomputer installiert. Sie können dies überprüfen, indem Sie den folgenden Code eingeben:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Hier sehen wir nun, dass auf diesem virtuellen Azure Suse-Computer CPython 2.7.2 installiert ist, was für die Ausführung der Azure-Lernprogramme und der Django-Beispiele ausreicht. Falls Sie ein Upgrade durchführen müssen, befolgen Sie die empfohlenen Paket-Upgrade-Anweisungen Ihres Betriebssystems. Beachten Sie jedoch, dass es in der Regel besser ist, Python auf dem System so zu belassen und die neuere Version über [Virtualenv](http://pypi.python.org/pypi/virtualenv) zu installieren, da andere Programme möglicherweise von der ursprünglichen Version abhängig sind.

Verwenden Sie **pip**, um die Python Azure-Clientbibliotheken von **PyPI** abzurufen und zu installieren:

    curl https://raw.github.com/pypa/pip/master/contrib/get-pip.py | sudo python

Der oben genannte Befehl fragt automatisch das Stammkennwort ab. Geben Sie es ein, und drücken Sie die Eingabetaste. Als Nächstes:

    sudo /usr/local/bin/pip-2.7 install azure

Die installierten Clientbibliotheken sollten nun unter **site-packages** angezeigt werden. Unter MacOS:

![MacOS site-packages](./media/python-how-to-install/how-to-install-python-mac-site.png)

Für die Entwicklung von Mac/Linux werden für diese Version zwei wesentliche Szenarien unterstützt:

1.  Nutzung von Azure Services über die Clientbibliotheken für Python

2.  Ausführen Ihrer App in einem virtuellen Linux-Computer

Bei dem ersten Szenario können Sie vielseitige Web-Apps erstellen, die von den Vorteilen der Azure PaaS-Funktionen profitieren, wie beispielsweise Blob-Speicher, Warteschlangen usw. über Python-Wrapper für die Azure-REST-APIs. Diese funktionieren auf Windows, Mac und Linux identisch. Beispiele finden Sie in den Lernprogrammen und in den Anleitungen. Sie können diese Clientbibliotheken auch in einem virtuellen Linux-Computer verwenden.

Bei dem VM-Szenario starten Sie einen virtuellen Linux-Computer Ihrer Wahl (Ubuntu, CentOS, Suse) und führen eine beliebige Anwendung aus bzw. verwalten diese. Beispielsweise können Sie [IPython](http://ipython.org)-REPL/Notebook auf Ihrem Windows/Mac/Linux-Computer ausführen und Ihren Browser auf einen virtuellen Linux- oder Windows-Computer mit Multi-Proc verweisen, der die IPython-Engine auf Azure ausführt. Weitere Informationen zur Installation von IPython finden Sie im entsprechenden Lernprogramm.

Weitere Informationen zur Einrichtung eines virtuellen Linux-Computers finden Sie im [Abschnitt zur Linux-Verwaltung](/de-de/manage/linux/).

Weitere Software und Ressourcen:
--------------------------------

-   [Enthought Python-Distribution](http://www.enthought.com)
-   [ActiveState Python-Distribution](http://www.activestate.com)
-   [SciPy – Suite wissenschaftlicher Python-Bibliotheken](http://www.scipy.org)
-   [NumPy – Numerische Bibliothek für Python](http://www.numpy.org)
-   [Django-Projekt – Ausgereiftes Web-Framework/CMS](http://www.djangoproject.com)
-   [IPython – Erweitertes REPL/Notebook für Python](http://ipython.org)
-   [IPython Notebook auf Azure](http://windowsazure.com/de-de/documentation/articles/virtual-machines-python-ipython-notebook)
-   [Python Tools for Visual Studio auf CodePlex](http://pytools.codeplex.com)
-   [Virtualenv](http://pypi.python.org/pypi/virtualenv)


[Enthought Python Distribution]: http://www.enthought.com 
[ActiveState Python Distribution]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com 
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[IPython Notebook on Azure]: http://windowsazure.com/de-de/documentation/articles/virtual-machines-python-ipython-notebook
[Python Tools for Visual Studio on CodePlex]: http://pytools.codeplex.com 
[Python Package Index (PyPI)]: http://pypi.python.org/pypi
[Virtualenv]: http://pypi.python.org/pypi/virtualenv 
[WebPI installer]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Setting up a Linux VM via the Azure portal]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[How to use the Azure Command-Line Tools for Mac and Linux]: ../../shared/chunks/crossplat-cmd-tools

