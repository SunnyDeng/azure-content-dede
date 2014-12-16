<properties urlDisplayName="Install Python" pageTitle="Installieren von Python und SDK - Azure" metaKeywords="Azure Python SDK" description="Learn how to install Python and the SDK to use with Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="11/10/2014" ms.author="huvalo" />




# Installieren von Python und SDK
Die Installation von Python auf Windows ist sehr einfach. Bei Mac- und Linux-Systemen ist sie bereits vorinstalliert.  Dieser Leitfaden führt Sie durch die Installation und bereitet Ihren Computer für den Einsatz mit Azure vor.  Dieser Leitfaden unterstützt Sie bei den folgenden Punkten:

* Was umfasst das Python Azure SDK?
* Welche Python und welche Version sollte verwendet werden?
* Installation auf Windows
* Installation auf Mac und Linux

## Was umfasst das Python Azure SDK?

Das Azure SDK für Python enthält Komponenten, mit deren Hilfe Sie Python-Anwendungen für Azure entwickeln, bereitstellen und verwalten können. Insbesondere enthält das Azure SDK für Python Folgendes:

* **Die Python-Clientbibliotheken für Azure**. Diese Basisklassenbibliotheken bieten eine Schnittstelle für den Zugriff auf Azure-Funktionen wie Datenverwaltungs- und Cloud-Dienste.  
* **Die Azure-Emulatoren (nur Windows)**. Die Rechen- und Speicheremulatoren sind lokale Emulatoren von Datenverwaltungs- und Cloud-Diensten, mit denen Sie eine Anwendung lokal testen können. Die Azure-Emulatoren laufen nur unter Windows.


Die Hauptszenarien für diese Versionen sind:

* **Windows**: Cloud-Dienst, Website - beispielsweise eine Django-Website, die Webrollen verwendet
* **Mac/Linux**: IaaS - Ausführen beliebiger Anwendungen auf einem virtuellen Computer, Nutzung von Azure Services über Python

## Welche Python und welche Version sollte verwendet werden?

Verschiedene Ausführungen von Python-Übersetzern sind verfügbar. Einige Beispiele:

* CPython - der standardmäßige und am häufigsten verwendete Python-Übersetzer
* IronPython - Python-Übersetzer, der auf .Net/CLR läuft
* Jython - Python-Übersetzer, der auf der JVM läuft

Für diese Version wurde nur **CPython** getestet und wird unterstützt.  Wir empfehlen Version 2.7 oder 3.4.

## Wo erhalte ich Python?

Es gibt mehrere Möglichkeiten, CPython zu beziehen:

* Direkt unter www.python.org
* Von angesehenen Distributoren wie www.enthought.com, www.ActiveState.com oder www.continuum.io
* Aus der Source erzeugen!

Sofern Sie keine spezifischen Anforderungen haben, empfehlen wir Ihnen die ersten beiden Optionen, wie unten beschrieben.

## Installation auf Windows

Unter Windows können Sie die Installation mithilfe des Webplattform -Installers optimieren. Dazu gehört CPython auf www.python.org.

* [Microsoft Azure SDK für Python 2.7][]
* [Microsoft Azure SDK für Python 3,4][]

**Hinweis:** Unter Windows Server müssen Sie möglicherweise die Einstellungen für die verstärkte Sicherheitskonfiguration für IE konfigurieren, um den WebPI Installer herunterzuladen (Start/Verwaltung/Server-Manager/Lokaler Server, klicken Sie auf **Verstärkte Sicherheitskonfiguration für IE konfigurieren**, die auf "Aus" gesetzt sind)


### Python 2,7

Der WebPI Installer bietet alles, was Sie für die Entwicklung von Python Azure-Apps benötigen.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

Sobald Sie die Installation abgeschlossen haben, wird folgender Bildschirm angezeigt, der Ihre Installationsoptionen bestätigt:

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

Sobald die Installation abgeschlossen ist, geben Sie "python" in die Eingabeaufforderung ein, um sicherzustellen, dass alles reibungslos funktioniert hat.  Je nachdem, welche Installationsmöglichkeit Sie verwendet haben, müssen Sie eventuell Ihre Pfadvariable entsprechend festlegen, damit (die richtige Version von) Python gefunden wird:

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

Nach der Installation sollten Ihnen Python und die Clientbibliotheken am Standartort zur Verfügung stehen:

		C:\Python27\Lib\site-packages\azure


### Python 3.4

Der WebPI Installer bietet alles, was Sie für die Entwicklung von Python Azure-Apps benötigen.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)
 
Sobald Sie die Installation abgeschlossen haben, wird folgender Bildschirm angezeigt, der Ihre Installationsoptionen bestätigt:

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

Sobald die Installation abgeschlossen ist, geben Sie "python" in die Eingabeaufforderung ein, um sicherzustellen, dass alles reibungslos funktioniert hat.  Je nachdem, welche Installationsmöglichkeit Sie verwendet haben, müssen Sie eventuell Ihre Pfadvariable entsprechend festlegen, damit (die richtige Version von) Python gefunden wird:

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

Nach der Installation sollten Ihnen Python und die Clientbibliotheken am Standartort zur Verfügung stehen:

		C:\Python34\Lib\site-packages\azure


### Beziehen weiterer Pakete

Während es bei dieser Version primär um Web-Apps geht, finden Sie im [Python Package Index (PyPI)][] eine umfassende Auswahl anderer Software.  Wenn Sie eine Distribution installieren, verfügen Sie bereits über die wichtigsten Bits für eine Vielzahl an Szenarien von Webentwicklung bis hin zu technischem Computing.


### Python Tools for Visual Studio

Python Tools for Visual Studio (PTVS) ist ein kostenloses/OSS-Plugin von Microsoft, das VS zu einer vollwertigen Python-IDE macht:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Die Verwendung von PTVS ist optional aber empfehlenswert, da Sie so bei Projekten/Lösungen von Python und Django von Support, Debugging, Profilerstellung, interaktiven Fenstern, Vorlagenbearbeitung und IntelliSense profitieren.

PTVS erleichtert auch die Bereitstellung auf Microsoft Azure, mit Unterstützung für die [Bereitstellung in Cloud-Diensten und auf Websites][].

PTVS funktioniert mit Ihren vorhandenen Installationen von Visual Studio 2010, 2012 oder 2013.  Weitere Informationen und Downloads finden Sie unter [Python Tools for Visual Studio auf CodePlex][].  

## Deinstallation auf Windows

Die WebPI-Produkte **Azure SDK for Python** sind keine Anwendungen im herkömmlichen Sinn, sondern vielmehr eine Sammlung verschiedener Produkte wie Python 2.7/3.4 (32 Bit), Azure-Clientbibliotheken für Python usw., die in einem Paket zusammengefasst sind.  Daher gibt es für das Produkt auch kein konventionelles Deinstallationsprogramm. Sie müssen die Programme, die von dem Produkt installiert werden, einzeln in der Windows-Systemsteuerung deinstallieren.  

Falls Sie **Azure SDK for Python** einmal neu installieren möchten, öffnen Sie einfach eine PowerShell-Eingabeaufforderung, und führen Sie den folgenden Befehl aus:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

Führen Sie WebPI dann erneut aus.

## Installation auf Linux und MacOS

Python ist höchstwahrscheinlich bereits auf Ihrem Entwicklungscomputer installiert.  Sie können dies überprüfen, indem Sie den folgenden Code eingeben:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Hier sehen Sie nun, dass auf diesem Ubuntu Server 14.04 LTS VM, der auf Azure ausgeführt wird, CPython 2.7.6 installiert ist. Falls Sie ein Upgrade durchführen müssen, befolgen Sie die empfohlenen Paket-Upgrade-Anweisungen Ihres Betriebssystems.

Verwenden Sie **pip**, um die Python Azure-Clientbibliotheken von **PyPI** abzurufen und zu installieren:

	curl https://bootstrap.pypa.io/get-pip.py | sudo python
	
Der oben genannte Befehl fragt automatisch das Stammkennwort ab. Geben Sie es ein, und drücken Sie die Eingabetaste.  Nächster Schritt:
	
	sudo pip install azure

Die installierten Clientbibliotheken sollten nun unter **site-packages** angezeigt werden.  Unter MacOS:

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

Für die Entwicklung von Mac/Linux werden zwei wesentliche Szenarien unterstützt:

1. Nutzung von Azure Services über die Clientbibliotheken für Python

2. Ausführen Ihrer App in einem virtuellen Linux-Computer

Beim ersten Szenario können Sie vielseitige Web-Apps erstellen, die von den Vorteilen der Azure PaaS-Funktionen profitieren, wie beispielsweise Blob-Speicher, Warteschlangen usw. über Python-Wrapper für die Azure-REST-APIs. Diese funktionieren auf Windows, Mac und Linux identisch.  Beispiele finden Sie in den Lernprogrammen und in den Anleitungen.  Sie können diese Clientbibliotheken auch in einem virtuellen Linux-Computer verwenden.

Bei dem VM-Szenario starten Sie einen virtuellen Linux-Computer Ihrer Wahl (Ubuntu, CentOS, Suse) und führen eine beliebige Anwendung aus bzw. verwalten diese.  Beispielsweise können Sie [IPython](http://ipython.org)-REPL/Notebook auf Ihrem Windows/Mac/Linux-Computer ausführen und Ihren Browser auf einen virtuellen Linux- oder Windows-Computer mit Multi-Proc verweisen, der die IPython-Engine auf Azure ausführt. Weitere Informationen zur Installation von IPython finden Sie im entsprechenden Lernprogramm.

Weitere Informationen zur Einrichtung eines virtuellen Linux-Computers finden Sie im [Abschnitt zur Linux-Verwaltung.](/de-de/manage/linux/)

## Weitere Software und Ressourcen:

* [Enthought Python-Distribution][]
* [ActiveState Python-Distribution][]
* [SciPy - Suite wissenschaftlicher Python-Bibliotheken][]
* [NumPy - Numerische Bibliothek für Python][]
* [Django-Projekt - Ausgereiftes Web-Framework/CMS][]
* [IPython - Erweitertes REPL/Notebook für Python][]
* [IPython Notebook in Azure][]
* [Python Tools for Visual Studio auf CodePlex][]
* [Virtualenv][]


[Enthought Python-Distribution]: http://www.enthought.com 
[ActiveState Python-Distribution]: http://www.activestate.com
[SciPy - Suite wissenschaftlicher Python-Bibliotheken]: http://www.scipy.org
[NumPy - Numerische Bibliothek für Python]: http://www.numpy.org
[Django-Projekt - Ausgereiftes Web-Framework/CMS]: http://www.djangoproject.com 
[IPython - Erweitertes REPL/Notebook für Python]: http://ipython.org
[IPython Notebook in Azure]: /de-de/documentation/articles/virtual-machines-python-ipython-notebook
[Bereitstellung in Cloud-Diensten und auf Websites]: /de-de/documentation/articles/cloud-services-web-sites-python-django-app-with-ptvs/
[Python Tools for Visual Studio auf CodePlex]: http://pytools.codeplex.com 
[Python Package Index (PyPI)]: http://pypi.python.org/pypi
[Virtualenv]: http://pypi.python.org/pypi/virtualenv 
[Microsoft Azure SDK für Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK für Python 3,4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Einrichten einer Linux-VM über das Azure-Portal]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Verwenden des Azure-Befehlszeilentools für Mac und Linux]: ../../shared/chunks/crossplat-cmd-tools

