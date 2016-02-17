<properties 
	pageTitle="Bereitstellen der Microsoft Data Science Virtual Machine | Microsoft Azure" 
	description="Konfigurieren und erstellen Sie für Analysen und Machine Learning einen virtuellen Computer für Data Science in Azure." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="bradsev" />


# Bereitstellen der Microsoft Data Science Virtual Machine

## Einführung

Die Microsoft Data Science Virtual Machine ist das Image eines virtueller Azure-Computers (VM), das vorinstalliert und mit einigen gängigen Tools konfiguriert ist, die häufig für Datenanalysen und Machine Learning verwendet werden. Die enthaltenen Tools sind:

- Microsoft R Server Developer Edition
- Anaconda Python Distribution
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server Express Edition
- Azure SDK


Data Science umfasst Iterationen einer Sequenz von Aufgaben: Suchen, Laden und Vorverarbeiten von Daten, Erstellen und Testen von Modellen sowie Bereitstellen der Modelle für die Nutzung in intelligenten Anwendungen. Es ist nicht ungewöhnlich, dass Datenanalysten eine Vielzahl von Tools für diese Aufgaben verwenden. Es kann sehr zeitaufwändig sein, die richtigen Versionen der Software zu finden und dann herunterzuladen und zu installieren. Die Microsoft Data Science Virtual Machine kann diesen Aufwand verringern.

Mit der Microsoft Data Science Virtual Machine können Sie gleich mit Ihrem Analyseprojekt loslegen. Sie können in einer Vielzahl von Sprachen, einschließlich R, Python, SQL und C#, an Aufgaben arbeiten. Visual Studio bietet eine benutzerfreundliche IDE zum Entwickeln und Testen von Code. Mit dem Azure SDK der VM können Sie Ihre Anwendungen mit verschiedenen Diensten auf der Cloudplattform von Microsoft erstellen.

Für dieses Data Science-VM-Image fallen keine Softwaregebühren an. Sie zahlen nur die Azure-Nutzungsgebühren, die von der Größe des virtuellen Computers abhängen, den Sie mit diesem VM-Image bereitstellen. Weitere Informationen zu den Computegebühren finden Sie [hier](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/).


## Voraussetzungen

Bevor Sie eine Microsoft Data Science Virtual Machine erstellen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**: Informationen zum Bezug finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Ein Azure-Speicherkonto**: Um eines zu erstellen, siehe [Erstellen eines Azure-Speicherkontos](storage-create-storage-account.md#create-a-storage-account). Das Speicherkonto kann alternativ beim Erstellen der VM erstellt werden, wenn Sie kein vorhandenes Konto verwenden möchten.


## Erstellen Ihrer Microsoft Data Science Virtual Machine

Es folgen die Schritte zum Erstellen einer Instanz der Microsoft Data Science Virtual Machine:

1.	Wechseln Sie im [Azure-Portal](https://ms.portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm) zur Auflistung der virtuellen Computer.
2.	 Klicken Sie unten auf die Schaltfläche **Erstellen**, um einen Assistenten aufzurufen. ![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 Die folgenden Abschnitte enthalten die **Eingaben** für jeden der **fünf Schritte** im Assistenten (im rechten Teil der obigen Abbildung aufgelistet), der zum Erstellen der Microsoft Data Science Virtual Machine wird. Die zum Konfigurieren der einzelnen Schritte erforderlichen Eingaben sind:

  **a. Grundlagen**:

   - **Name**: Name des Data Science-Servers, den Sie erstellen.
   - **Benutzername**: ID des Administratoranmeldekontos.
   - **Kennwort**: Kennwort des Administratorkontos.
   - **Abonnement**: Wenn Sie über mehrere Abonnements verfügen, wählen Sie eines aus, über das der Computer erstellt und abgerechnet wird.
   - **Ressourcengruppe**: Sie können eine neue Gruppe erstellen oder eine vorhandene Gruppe verwenden.
   - **Standort**: Wählen Sie das Rechenzentrum aus, das am besten geeignet ist. Normalerweise ist es das Rechenzentrum, in dem der größte Teil Ihrer Daten gespeichert ist oder das Ihrem Standort am nächsten ist, um den schnellsten Netzwerkzugriff zu erreichen.

  **b. Größe**:

   - Wählen Sie einen Servertyp aus, der die funktionalen Anforderungen und den Kostenrahmen erfüllt. Eine größere Auswahl an VM-Größen erhalten Sie durch die Auswahl von „Alle anzeigen“.

  **c. Einstellungen**:

   - **Datenträgertyp**: Wählen Sie „Premium“, wenn Sie Solid-State-Laufwerke (SSD) bevorzugen, andernfalls „Standard“.
   - **Speicherkonto**: Sie können ein neues Azure-Speicherkonto in Ihrem Abonnement erstellen oder ein vorhandenes an dem *Standort* verwenden, der im Schritt „Grundlagen“ des Assistenten ausgewählt wurde.
   - **Andere Parameter**: In den meisten Fällen verwenden Sie die Standardeinstellungen für die übrigen Parameter. Sie können den Mauszeiger über den Informationslink bewegen, um Hilfe zu einem bestimmten Feld anzuzeigen, falls Sie nicht die Standardeinstellungen verwenden möchten.

  **d. Zusammenfassung**:

   - Stellen Sie sicher, dass alle eingegebenen Informationen richtig sind.

  **e. Kaufen**:

   - Klicken Sie auf **Kaufen**, um die Bereitstellung zu starten. Ein Link zu den Bedingungen der Transaktion wird bereitgestellt. Für die VM gelten keine über die Computekosten für die Servergröße, die Sie im Schritt **Größe** ausgewählt haben, hinausgehenden Kosten. 


Die Bereitstellung sollte ungefähr 10 bis 20 Minuten dauern. Der Status der Bereitstellung wird im Azure-Portal angezeigt.

## Zugreifen auf die Microsoft Data Science Virtual Machine

Nach der VM-Erstellung können Sie sich mithilfe von Remotedesktop mit den Anmeldeinformationen des Administratorkontos anmelden, die Sie im Abschnitt „Grundlagen“ von Schritt 4 erstellt haben.

Sobald Ihre VM erstellt und bereitgestellt wurde, können Sie beginnen, die installierten und konfigurierten Tools zu verwenden. Für viele der Tools gibt es Desktopsymbole und Kacheln im Startmenü.

## Erstellen eines sicheren Kennworts für den Jupyter Notebook-Server 

Führen Sie den folgenden Befehl über die Befehlszeile der Data Science Virtual Machine aus, um Ihr eigenes sicheres Kennwort für den darauf installierten Jupyter Notebook-Server zu erstellen.

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Verwenden Sie ein sicheres Kennwort, sobald Sie dazu aufgefordert werden.

Der Kennworthash wird in der Ausgabe im Format "sha1:xxxxxx" angezeigt. Kopieren Sie diesen Kennworthash, und ersetzen Sie den vorhandenen Hash in Ihrer Notebook-Konfigurationsdatei in diesem Verzeichnis: **C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py**. Verwenden Sie den Parameternamen „***c.NotebookApp.password***”.

Ersetzen Sie nur den vorhandenen Hashwert innerhalb der Anführungszeichen. Die Anführungszeichen und das Präfix ***sha1:*** für den Parameterwert müssen beibehalten werden.

Zum Schluss müssen Sie den IPython-Server, der auf dem virtuellen Computer als geplanter Windows-Task mit der Bezeichnung „Start\_IPython\_Notebook“ ausgeführt wird, anhalten und erneut starten. Wenn das neue Kennwort nach dem Neustart dieses Tasks nicht akzeptiert wird, starten Sie den virtuellen Computer neu.

## In der Microsoft Data Science Virtual Machine installierte Tools

### Microsoft R Server Developer Edition
Für die Durchführung Ihrer Analysen ist auf der VM Microsoft R Server Developer Edition vorinstalliert. Microsoft R Server ist eine unterstützte, sichere und in großem Maße bereitstell- und skalierbare Analyseplattform für Unternehmen, die auf R basiert. Dank vielfältiger Big Data-Statistiken, Vorhersagemodellierung und Machine Learning-Funktionen unterstützt R Server Analysen vollumfänglich - von der Exploration über die Analyse bis hin zur Visualisierung und der Modellierung. Die Verwendung und Erweiterung der Open-Source-Sprache R sorgt für die vollständige Kompatibilität zwischen Microsoft R Server und in R geschriebenen Skripten, Funktionen und CRAN-Paketen für die Datenanalyse in Unternehmen. Mit der Möglichkeit, Daten parallel und in großen Stücken verarbeiten zu können, hebt Microsoft R Server die Speicherbeschränkungen der Open-Source-Sprache R auf und ermöglicht den Benutzern so auch die Analyse von Daten, deren Größe die des Hauptspeichers bei weitem überschreitet. Zusätzlich enthält die VM eine integrierte Entwicklungsumgebung für R (IDE), die Sie durch Klicken auf das Symbol „Revolution R Enterprise 8.0“ im Startmenü oder auf dem Desktop öffnen können. Sie können auch andere IDEs wie „[RStudio](http://www.rstudio.com)” herunterladen und verwenden.

### Python
Für die Entwicklung mithilfe von Python wurden Anaconda Python Distribution 2.7 und 3.5 installiert. Diese Distribution enthält die Python-Basisversion sowie etwa 300 der beliebtesten Pakete für Mathematik, Entwicklung und Datenanalysen. Sie können die Python-Tools für Visual Studio (PTVS), die mit der Visual Studio 2015 Community Edition installiert werden, oder eine der mit Anaconda gebündelten IDEs wie IDLE oder Spyder verwenden. Sie können eines dieser Tools über eine Suche auf der Suchleiste starten (**Windows-Taste**+**S**). **Hinweis**: Damit die Python-Tools für Visual Studio auf Anaconda Python 2.7 und 3.5 verweisen können, müssen Sie benutzerdefinierte Umgebungen für jede Version erstellen. Wechseln Sie in der Visual Studio 2015 Community Edition zu „Tools -> Python-Tools -> Python-Umgebungen“, und klicken Sie auf „+ Benutzerdefiniert“. Legen Sie die Umgebungspfade fest. Anaconda Python 2.7 wird unter C:\\Anaconda installiert, Anaconda Python 3.5 unter C:\\Anaconda\\envs\\py35. Ausführliche Schrittanleitungen hierzu finden Sie in der [PTVS-Dokumentation](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it).

### Jupyter Notebook
Zur Anaconda Distribution gehört außerdem ein Jupyter Notebook, eine Umgebung zum Freigeben von Code und Analysen. Es wurde bereits ein Jupyter Notebook-Server mit Python 2, Python 3 und R-Kernels konfiguriert. Es gibt ein Desktopsymbol namens „Jupyter Notebook“, um den Browser für den Zugriff auf den Notebook-Server zu starten. Wenn Sie die VM über Remotedesktop verwenden, können Sie auch [https://localhost:9999/](https://localhost:9999/) für den Zugriff auf den Jupyter Notebook-Server aufrufen. (Hinweis: Fahren Sie fort, wenn Warnungen zu Zertifikaten angezeigt werden). Es sind Beispiele für Notebooks enthalten - eines für Python und eines für R. Der Link zu den Beispielen wird Ihnen auf der Startseite für die Notebooks angezeigt, nachdem Sie sich mit dem im vorherigen Schritt festgelegten Passwort am Jupyter Notebook authentifiziert haben.

### Visual Studio 2015 Community Edition
Visual Studio Community Edition ist auf der VM installiert. Dies ist eine kostenlose Version der beliebten IDE von Microsoft, die Sie zur Evaluierung und für sehr kleine Teams verwenden können. Die Lizenzbedingungen können Sie [hier](https://www.visualstudio.com/support/legal/mt171547) prüfen. Öffnen Sie Visual Studio durch Doppelklicken auf das Desktopsymbol oder über das **Startmenü**. Sie können auch nach Programmen suchen, indem Sie die **Windows-Taste**+**S** drücken und „Visual Studio“ eingeben. Anschließend können Sie Projekte in Sprachen wie C# und Python erstellen. Zusätzlich werden Sie feststellen, dass bereits Plugins installiert wurden, die Ihnen die Arbeit mit Azure-Diensten wie Azure Data Catalog, Azure, HDInsight (Hadoop, Spark) und Azure Data Lake erleichtern.

Hinweis: Es könnte eine Meldung angezeigt werden, die besagt, dass der Evaluierungszeitraum abgelaufen ist. Geben Sie die Anmeldeinformationen eines Microsoft-Kontos ein, oder erstellen Sie ein entsprechendes Konto, um Zugriff auf die Visual Studio Community Edition zu erhalten.

### SQL Server Express
Eine eingeschränkte Version von SQL Server gehört ebenfalls zu Visual Studio Community Edition. Sie können auf die SQL Server-Instanz zugreifen, indem Sie **SQL Server Management Studio** starten. Ihr VM-Name wird als Servernamen angegeben. Verwenden Sie die Windows-Authentifizierung, wenn Sie als Administrator unter Windows angemeldet sind. Sobald SQL Server Management Studio gestartet wurde, können Sie andere Benutzer erstellen, Datenbanken erstellen, Daten importieren und SQL-Abfragen ausführen.

### Azure 
Mehrere Azure-Tools sind auf der VM installiert: – Es gibt eine Desktopverknüpfung für den Zugriff auf die Azure SDK-Dokumentation. – **AzCopy** wird zum Verschieben von Daten in das und aus dem Microsoft Azure Storage-Konto verwendet. – **Azure Storage Explorer** wird verwendet, um die Objekte zu durchsuchen, die in Ihrem Azure Storage-Konto gespeichert sind. – **Microsoft Azure PowerShell** ist ein Tool zur Verwaltung von Azure-Ressourcen in der PowerShell-Skriptsprache und ebenfalls auf Ihrer VM installiert.

###Power BI

Um Dashboards und aussagekräftige Visualisierungen zu erstellen, wurde **Power BI Desktop** installiert. Verwenden Sie dieses Tool, um Daten aus verschiedenen Quellen abzurufen, Dashboards und Berichte zu erstellen und diese in der Cloud zu veröffentlichen. Weitere Informationen finden Sie auf der [Power BI](http://powerbi.microsoft.com)-Website.

Hinweis: Für den Zugriff auf Power BI benötigen Sie ein Office 365-Konto.

## Zusätzliche Microsoft-Entwicklungstools
Der [**Microsoft-Webplattform-Installer**](https://www.microsoft.com/web/downloads/platform.aspx) kann verwendet werden, um weitere Microsoft-Entwicklungstools zu suchen und herunterzuladen. Es gibt auch eine Verknüpfung mit dem Tool auf dem Desktop der Microsoft Data Science Virtual Machine.

## Nächste Schritte
Mit den folgenden Schritten können Sie noch mehr lernen und entdecken.

* Verschaffen Sie sich einen Überblick über die verschiedenen Tools, die auf der Data Science-VM installiert sind, indem Sie das Startmenü öffnen und sich die Liste der installierten Tools ansehen.
* Navigieren Sie zu **C:\\Programme\\Microsoft\\MRO-for-RRE\\8.0\\R-3.2.2\\library\\RevoScaleR\\demoScripts**, um Anwendungsbeispiele der RevoScaleR-Bibliothek in R zu erhalten, die Datenanalysen auf Unternehmensebene unterstützt.  
* Erfahren Sie, wie Sie mithilfe des [Data Science Process](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) systematisch vollständige Analyselösungen erstellen. 
* Öffnen Sie den [Cortana Analytics Katalog](http://gallery.cortanaanalytics.com), um Anwendungsbeispiele der Cortana Analytics Suite für die Bereiche Machine Learning und Datenanalysen zu erhalten. Für den einfachen Zugriff haben wir auch ein Symbol auf dem Desktop und im Startmenü der virtuellen Maschine bereitgestellt. 

<!---HONumber=AcomDC_0128_2016-->