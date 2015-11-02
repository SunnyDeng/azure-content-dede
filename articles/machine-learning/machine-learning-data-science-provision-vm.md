<properties 
	pageTitle="Bereitstellen eines virtuellen Computers für Data Science | Microsoft Azure" 
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
	ms.date="10/20/2015" 
	ms.author="bradsev" />


# Bereitstellen eines virtuellen Computers für Data Science

## Einführung

Der virtuelle Computer für Data Science ist das Image eines virtueller Azure-Computers (VM), das vorinstalliert und mit einigen gängigen Tools, die häufig für Datenanalysen und Machine Learning verwendet werden, konfiguriert ist. Die enthaltenen Tools sind:

- Revolution R Open
- Anaconda Python Distribution
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server Express Edition


Data Science umfasst Iterationen einer Sequenz von Aufgaben: Suchen, Laden und Vorverarbeiten von Daten, Erstellen und Testen von Modellen sowie Bereitstellen der Modelle für die Nutzung in intelligenten Anwendungen. Es ist nicht ungewöhnlich, dass Datenanalysten eine Vielzahl von Tools für diese Aufgaben verwenden. Es kann sehr zeitaufwändig, die richtigen Versionen der Software zu finden und dann herunterzuladen und zu installieren. Der virtuelle Computer für Data Science kann diese Last beträchtlich vereinfachen.

Verwenden Sie die Data Science-VM, um diese Last zu erleichtern. Beginnen Sie Ihr Analyseprojekt, indem Sie ein Image erstellen, dass die Software umfasst, die häufig für Analysen und Machine Learning-Aufgaben in einer Vielzahl von Sprachen wie R, Python, SQL, C# verwendet wird. Visual Studio bietet eine benutzerfreundliche IDE zum Entwickeln und Testen von Code. Mit dem Azure SDK der VM können Sie Ihre Anwendungen mit verschiedenen Diensten auf der Cloudplattform von Microsoft erstellen.

## Voraussetzungen

Bevor Sie eine Azure-VM erstellen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**: Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Ein Azure-Speicherkonto**: Siehe [Erstellen eines Azure-Speicherkontos](storage-whatis-account.md). Das Speicherkonto kann beim Erstellen der VM erstellt werden, wenn Sie kein vorhandenes Konto verwenden möchten.


## Erstellen der Data Science-VM

Folgende Schritte müssen zum Erstellen Ihrer Instanz der Data Science-VM ausgeführt werden:

1.	Anmelden beim Azure-Portal
2.	Navigieren Sie zu **Neu** -> **Compute** -> **Marketplace**, und suchen Sie nach *Data Science Virtual Machine*. Wählen Sie die VM mit dem Titel „**Data Science Virtual Machine**“ aus, die von Microsoft veröffentlicht wurde, um eine Beschreibung dieser VM anzuzeigen.
3.	Klicken Sie unten auf die Schaltfläche **Erstellen**, um einen Assistenten aufzurufen. ![provision-data-science-vm](./media/machine-learning-data-science-provision-vm/provision-data-science-virtual-machine.png)
4.	 Die folgenden Abschnitte enthalten die Eingaben für jeden Schritt im Assistenten, der zum Erstellen der Data Science-VM verwendet wird.


 **1. Grundlagen**:

- **Name**: Name des Data Science-Servers, den Sie erstellen.
- **Benutzername**: ID des Administratoranmeldekontos
- **Kennwort**: Kennwort des Administratorkontos
- **Abonnement**: Wenn Sie über mehrere Abonnements verfügen, wählen Sie eines aus, über das der Computer erstellt und abgerechnet wird
- **Ressourcengruppe**: Sie können eine neue Gruppe erstellen oder eine vorhandene Gruppe verwenden
- **Standort**: Wählen Sie das Rechenzentrum aus, das am besten geeignet ist. Normalerweise ist es das Rechenzentrum, in dem der größte Teil Ihrer Daten gespeichert ist oder das Ihrem Standort am nächsten ist, um den schnellsten Netzwerkzugriff zu erreichen.

 **2. Größe**:

- Wählen Sie einen Servertyp aus, der die funktionalen Anforderungen und den Kostenrahmen erfüllt. Eine größere Auswahl an VM-Größen erhalten Sie durch die Auswahl von „Alle anzeigen“.

 **3. Einstellungen**

 **4. Datenträgertyp**:

- Wählen Sie „Premium“ aus, wenn Sie SSD vorziehen, wählen Sie sonst „Standard“ aus.

 **5. Speicherkonto**:


- Sie können ein neues Azure-Speicherkonto in Ihrem Abonnement erstellen oder ein vorhandenes an dem Standort verwenden, der im Schritt „Grundlagen“ des Assistenten ausgewählt wurde.
- In den meisten Fällen verwenden Sie einfach die Standardeinstellungen für die übrigen Parameter. Sie können den Mauszeiger über den Informationslink bewegen, um Hilfe zu einem bestimmten Feld anzuzeigen, falls Sie nicht die Standardeinstellungen verwenden möchten.
- Stellen Sie sicher, dass alle eingegebenen Informationen richtig sind.
- Klicken Sie auf **Kaufen**, um die Bereitstellung zu starten. Ein Link zu den Bedingungen der Transaktion wird bereitgestellt. Für die VM gelten keine über die Computekosten für die Servergröße, die Sie im Schritt **Größe** ausgewählt haben, hinausgehenden Kosten. 

Die Bereitstellung sollte ungefähr 10 bis 20 Minuten dauern. Der Status der Bereitstellung wird im Azure-Portal angezeigt.

## Zugreifen auf die Data Science-VM

Nach der VM-Erstellung können Sie sich mithilfe von Remotedesktop mit den Anmeldeinformationen des Administratorkontos, die Sie im Abschnitt „Grundlagen“ von Schritt 4 erstellt haben, anmelden.

Sobald Ihre VM erstellt und bereitgestellt wurde, können Sie beginnen, die installierten und konfigurierten Tools zu verwenden. Für viele der Tools gibt es Desktopsymbole und Kacheln im Startmenü.

## Auf der Data Science-VM installierte Tools

### R
Wenn Sie R für die Analysen verwenden möchten, ist auf der VM Revolution R Open (RRO) installiert. Dies ist eine Open-Source-Distribution von R, und sie ist vollständig mit CRAN-R kompatibel. RRO enthält das neueste Open-Source-R-Modul und die Intel Math Kernel Library. Eine IDE namens „RRO RGui“ ist ebenfalls auf der VM vorhanden. Sie können auch andere IDEs wie [RStudio](http://www.rstudio.com) herunterladen und verwenden.

### Python
Für die Entwicklung mithilfe von Python wurde Anaconda Python Distribution 2.7 installiert. Diese Distribution enthält die Python-Basisversion sowie etwa 300 der beliebtesten Pakete für Mathematik, Entwicklung und Datenanalysen. Sie können mit Anaconda gebündelte IDEs wie IDLE oder Spyder verwenden. Sie können eines dieser Tools über eine Suche mit der Suchleiste starten (**Windows-Taste**+**S**).

### IPython Notebook
Zur Anaconda Distribution gehört außerdem ein IPython Notebook, eine Umgebung zum Freigeben von Code und Analysen. Ein IPython Notebook-Server wurde vorkonfiguriert. Es gibt ein Desktopsymbol, um den Browser für den Zugriff auf den Notebook-Server zu starten. Wenn Sie die VM über Remotedesktop verwenden, können Sie auch [https://localhost:9999/](https://localhost:9999/) für den Zugriff auf den IPython Notebook-Server aufrufen. (Hinweis: Fahren Sie einfach fort, wenn Warnungen zu Zertifikaten angezeigt werden.)

### Visual Studio 2015 Community Edition
Visual Studio Community Edition ist auf der VM installiert. Dies ist eine kostenlose Version der beliebten IDE von Microsoft, die Sie zur Evaluierung und für sehr kleine Teams verwenden können. Sehen Sie sich die Nutzungsbedingungen ***hier*** an (vorläufiger Link). Öffnen Sie Visual Studio durch Doppelklicken auf das Desktopsymbol oder über das Menü **Start**. Sie können auch nach Programmen suchen, indem Sie die **Windows-Taste**+**S** drücken und „Visual Studio“ eingeben.

Hinweis: Es könnte eine Meldung angezeigt werden, die besagt, dass der Evaluierungszeitraum abgelaufen ist. Geben Sie die Anmeldeinformationen eines Microsoft-Kontos ein, oder erstellen Sie ein entsprechendes Konto, um Zugriff auf die Visual Studio Community Edition zu erhalten. Anschließend können Sie Projekte in Sprachen wie C# und Python erstellen.

### SQL Server Express
Eine eingeschränkte Version von SQL Server gehört ebenfalls zu Visual Studio Community Edition. Sie können auf SQL Server zugreifen, indem Sie **SQL Server Management Studio** starten. Ihr VM-Name wird als Servernamen angegeben. Verwenden Sie die Windows-Authentifizierung, wenn Sie als Administrator unter Windows angemeldet sind. Sobald SQL Server Management Studio gestartet wurde, können Sie andere Benutzer erstellen, Datenbanken erstellen, Daten importieren und SQL-Abfragen ausführen.

### Azure 
Mehrere Azure-Tools sind auf der VM installiert: Es gibt eine Desktopverknüpfung für den Zugriff auf die Azure SDK-Dokumentation. **AzCopy** wird zum Verschieben von Daten in das und aus dem Microsoft Azure-Speicherkonto verwendet. **Azure Storage Explorer** wird verwendet, um die Objekte zu durchsuchen, die in Ihrem Azure-Speicherkonto gespeichert sind. **Microsoft Azure PowerShell** ist eine Skriptsprache zur Verwaltung von Azure-Ressourcen in einer Skriptsprache, die auch auf Ihrer VM installiert ist.

###Power BI

Um Dashboards und hervorragende Visualisierungen zu erstellen, wurde **Power BI Desktop** installiert. Verwenden Sie dieses Tool, um Daten aus verschiedenen Quellen abzurufen, Dashboards und Berichte zu erstellen und diese in der Cloud zu veröffentlichen. Weitere Informationen finden Sie auf der [Power BI](http://powerbi.microsoft.com)-Website.

Hinweis: Für den Zugriff auf Power BI benötigen Sie ein Office 365-Konto.

## Zusätzliche Microsoft-Entwicklungstools
Der [**Microsoft-Webplattform-Installer**](https://www.microsoft.com/web/downloads/platform.aspx) kann verwendet werden, um andere Microsoft-Entwicklungstools zu suchen und herunterzuladen. Es gibt auch eine Verknüpfung mit dem Tool auf dem Desktop der VM.

<!---HONumber=Oct15_HO4-->