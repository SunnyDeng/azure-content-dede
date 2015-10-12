<properties
	pageTitle="Installieren von Python und SDK – Azure"
	description="Erfahren Sie mehr über die Installation von Python und des SDK zur Verwendung mit Azure."
	services=""
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="huvalo"/>

# Installieren von Python und SDK

Die Installation von Python auf Windows ist sehr einfach. Bei Mac- und Linux-Systemen ist sie bereits vorinstalliert. Dieser Leitfaden führt Sie durch die Installation und bereitet Ihren Computer für den Einsatz mit Azure vor.

## Was umfasst das Python Azure SDK?

Das Azure SDK für Python enthält Komponenten, mit deren Hilfe Sie Python-Anwendungen für Azure entwickeln, bereitstellen und verwalten können. Insbesondere enthält das Azure SDK für Python Folgendes:

* **Die Python-Clientbibliotheken für Azure**. Diese Basisklassenbibliotheken bieten eine Schnittstelle für den Zugriff auf Azure-Funktionen, wie z. B. Speicher- und Service Bus, und für die Verwaltung von Azure-Ressourcen, z. B. Speicherkonten, virtuellen Computern usw.
* **Die Azure-Emulatoren (nur Windows)**. Die Rechen- und Speicheremulatoren sind lokale Emulatoren von Datenverwaltungs- und Clouddiensten, mit denen Sie eine Anwendung lokal testen können. Die Azure-Emulatoren laufen nur unter Windows.

## Welche Python und welche Version sollte verwendet werden?

Verschiedene Ausführungen von Python-Übersetzern sind verfügbar. Einige Beispiele:

* CPython – der standardmäßige und am häufigsten verwendete Python-Übersetzer
* IronPython – Python-Übersetzer, der auf .Net/CLR läuft
* Jython – Python-Übersetzer, der auf der JVM läuft

Für das Python Azure SDK und die Azure-Dienste, z. B. Websites und Cloud Services, wurde nur **CPython** getestet und wird unterstützt. Wir empfehlen Version 2.7 oder 3.4.

## Wo erhalte ich Python?

Es gibt mehrere Möglichkeiten, CPython zu beziehen:

* Direkt unter [www.python.org][]
* Von angesehenen Distributoren wie [www.continuum.io][], [www.enthought.com][] oder [www.activestate.com][]
* Aus der Source erzeugen!

Sofern Sie keine spezifischen Anforderungen haben, empfehlen wir Ihnen die ersten beiden Optionen, wie unten beschrieben.

## Installation unter Windows, Linux und MacOS (nur Clientbibliotheken)

Wenn Sie Python bereits installiert haben, können Sie "pip" zum Installieren eines Pakets aller Clientbibliotheken in Ihrer vorhandenen Python 2.7- oder Python 3.3+-Umgebung verwenden. Auf diese Weise werden die Pakete aus dem [Python Package Index][] (PyPI) heruntergeladen.

Beachten Sie, dass Sie unter Linux und MacOS möglicherweise den `sudo`-Befehl verwenden müssen: `sudo pip install azure`

	pip install azure

Ab Version 1.0.0 sind die Bibliotheken in mehrere Pakete unterteilt. Sie können nur die Pakete installieren, die Sie benötigen, oder das gesamte Paket.

So installieren Sie die Azure Storage Runtime-Clientbibliotheken:

	pip install azure-storage

So installieren Sie die Azure Service Bus-Clientbibliotheken:

	pip install azure-servicebus

So installieren Sie die Clientbibliotheken für den Azure-Ressourcen-Manager (ARM):

	pip install azure-mgmt

So installieren Sie die Azure Service Management-Clientbibliotheken (ASM):

	pip install azure-servicemanagement-legacy


## Installation unter Windows (Python, Azure-Emulatoren und Clientbibliotheken)

Sie können die Installation mit dem Webplattform-Installer optimieren. Dazu gehört CPython auf [www.python.org][].

* [Microsoft Azure SDK für Python 2.7][]
* [Microsoft Azure SDK für Python 3.4][]

**Hinweis:** Unter Windows Server müssen Sie möglicherweise die Einstellungen für die verstärkte Sicherheitskonfiguration für IE konfigurieren, um den WebPI Installer herunterzuladen (Start/Verwaltung/Server-Manager/Lokaler Server: Klicken Sie dann auf die Option **Verstärkte Sicherheitskonfiguration für IE konfigurieren**, die auf "Aus" gesetzt ist).

### Python 2.7

Der WebPI Installer bietet alles, was Sie für die Entwicklung von Python Azure-Apps benötigen.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

Sobald die Installation abgeschlossen ist, geben Sie an der Eingabeaufforderung `python` ein, um sicherzustellen, dass alles reibungslos funktioniert hat. Je nachdem, welche Installationsmöglichkeit Sie verwendet haben, müssen Sie eventuell Ihre Pfadvariable entsprechend festlegen, damit (die richtige Version von) Python gefunden wird:

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

Nach der Installation sollten Ihnen Python und die Clientbibliotheken am Standartort zur Verfügung stehen:

		C:\Python27\Lib\site-packages\azure


### Python 3.4

Der WebPI Installer bietet alles, was Sie für die Entwicklung von Python Azure-Apps benötigen.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)

Sobald die Installation abgeschlossen ist, geben Sie "python" in die Eingabeaufforderung ein, um sicherzustellen, dass alles reibungslos funktioniert hat. Je nachdem, welche Installationsmöglichkeit Sie verwendet haben, müssen Sie eventuell Ihre Pfadvariable entsprechend festlegen, damit (die richtige Version von) Python gefunden wird:

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

Nach der Installation sollten Ihnen Python und die Clientbibliotheken am Standartort zur Verfügung stehen:

		C:\Python34\Lib\site-packages\azure

### Deinstallation auf Windows

Die WebPI-Produkte **Azure SDK for Python** sind keine Anwendungen im herkömmlichen Sinn, sondern vielmehr eine Sammlung verschiedener Produkte wie Python 2.7/3.4 (32 Bit), Azure-Clientbibliotheken für Python usw., die in einem Paket zusammengefasst sind. Daher gibt es für das Produkt auch kein konventionelles Deinstallationsprogramm. Sie müssen die Programme, die von dem Produkt installiert werden, einzeln in der Windows-Systemsteuerung deinstallieren.

Falls Sie **Azure SDK for Python** einmal neu installieren möchten, öffnen Sie einfach eine PowerShell-Eingabeaufforderung, und führen Sie den folgenden Befehl aus:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

Führen Sie WebPI dann erneut aus.

## Beziehen weiterer Pakete

Der [Python Package Index][] (PyPI) verfügt über eine umfassende Auswahl von Python-Bibliotheken. Wenn Sie eine Distribution installieren, verfügen Sie bereits über die wichtigsten Bits für eine Vielzahl an Szenarien von Webentwicklung bis hin zu technischem Computing.


## Python Tools für Visual Studio

[Python Tools for Visual Studio][] (PTVS) ist ein kostenloses/OSS-Plugin von Microsoft, das VS zu einer vollwertigen Python-IDE macht:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Die Verwendung von PTVS ist optional aber empfehlenswert, da Sie so bei Python- und Web-Projekten/Lösungen von Support, Debugging, Profilerstellung, interaktiven Fenstern, Vorlagenbearbeitung und IntelliSense profitieren.

PTVS erleichtert auch die Bereitstellung unter Microsoft Azure, mit Unterstützung für die Bereitstellung in [Cloud Services][] und auf [Websites][].

PTVS funktioniert mit Ihren vorhandenen Installationen von Visual Studio 2013 oder 2015. Dokumentation, Downloads und Diskussionen finden Sie unter [Python-Tools für Visual Studio].

## Python Azure-Szenarien für Linux und MacOS

Für Linux oder Mac OS sind dies die wichtigsten Azure-Szenarien, die unterstützt werden:

1. Nutzung von Azure Services über die Clientbibliotheken für Python

2. Ausführen Ihrer App in einem virtuellen Linux-Computer

3. Entwickeln und Veröffentlichen auf Azure-Websites mit Git

Bei dem ersten Szenario können Sie vielseitige Web-Apps erstellen, die von den Vorteilen der Azure PaaS-Funktionen profitieren, wie beispielsweise [Blob-Speicher][], [Warteschlangenspeicher][], [Tabellenspeicher][] usw. über Python-Wrapper für die Azure-REST-APIs. Diese funktionieren auf Windows, Mac und Linux identisch. Sie können diese Clientbibliotheken auch von Ihrem lokalen Entwicklungscomputer oder einem virtuellen Linux-Computer verwenden, der auf Azure ausgeführt wird.

Bei dem VM-Szenario starten Sie einen virtuellen Linux-Computer Ihrer Wahl (Ubuntu, CentOS, Suse) und führen eine beliebige Anwendung aus bzw. verwalten diese. Beispielsweise können Sie [IPython][]-REPL/Notebook auf Ihrem Windows/Mac/Linux-Computer ausführen und Ihren Browser auf einen virtuellen Linux- oder Windows-Computer mit Multi-Proc verweisen, der die IPython-Engine auf Azure ausführt. Weitere Informationen finden Sie im Tutorial [IPython-Notizbuch in Azure][].

Informationen zur Einrichtung eines virtuellen Linux-Computers finden Sie im Tutorial [Erstellen eines virtuellen Linux-Computers][].

Mit Git-Bereitstellung können Sie eine Python-Webanwendung entwickeln und sie auf einer Azure-Website von jedem beliebigen Betriebssystem aus veröffentlichen. Wenn Sie das Repository in Azure übertragen, erstellt es automatisch eine virtuelle Umgebung und installiert mit pip die erforderlichen Pakete.

Weitere Informationen zum Entwickeln und Veröffentlichen von Azure-Websites finden Sie in den Tutorials für [Erstellen von Websites mit Django][], [Erstellen von Websites mit Bottle][] und [Erstellen von Websites mit Flask][]. Weitere allgemeine Informationen zur Verwendung von allen kompatiblen WSGI-Frameworks finden Sie unter [Konfigurieren von Python mit Azure-Websites][].


## Weitere Software und Ressourcen:

* [Continuum Analytics Python-Distribution][]
* [Enthought Python-Distribution][]
* [ActiveState Python-Distribution][]
* [SciPy – Suite wissenschaftlicher Python-Bibliotheken][]
* [NumPy – numerische Bibliothek für Python][]
* [Django-Projekt – ein ausgereiftes Web-Framework/CMS][]
* [IPython – ein erweitertes REPL/Notebook für Python][]
* [IPython Notebook in Azure][]
* [Python-Tools für Visual Studio auf GitHub][]
* [Python Developer Center](/develop/python/)

[Continuum Analytics Python-Distribution]: http://continuum.io
[Enthought Python-Distribution]: http://www.enthought.com
[ActiveState Python-Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy – Suite wissenschaftlicher Python-Bibliotheken]: http://www.scipy.org
[NumPy – numerische Bibliothek für Python]: http://www.numpy.org
[Django-Projekt – ein ausgereiftes Web-Framework/CMS]: http://www.djangoproject.com
[IPython – ein erweitertes REPL/Notebook für Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook in Azure]: virtual-machines-python-ipython-notebook.md
[IPython-Notizbuch in Azure]: virtual-machines-python-ipython-notebook.md
[Cloud Services]: cloud-services-python-ptvs.md
[Websites]: web-sites-python-ptvs-django-mysql.md
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python-Tools für Visual Studio]: http://aka.ms/ptvs
[Python-Tools für Visual Studio auf GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK für Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK für Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Erstellen eines virtuellen Linux-Computers]: virtual-machines-linux-tutorial.md
[Erstellen von Websites mit Django]: web-sites-python-create-deploy-django-app.md
[Erstellen von Websites mit Bottle]: web-sites-python-create-deploy-bottle-app.md
[Erstellen von Websites mit Flask]: web-sites-python-create-deploy-flask-app.md
[Konfigurieren von Python mit Azure-Websites]: web-sites-python-configure.md
[Tabellenspeicher]: storage-python-how-to-use-table-storage.md
[Warteschlangenspeicher]: storage-python-how-to-use-queue-storage.md
[Blob-Speicher]: storage-python-how-to-use-blob-storage.md

<!---HONumber=Oct15_HO1-->