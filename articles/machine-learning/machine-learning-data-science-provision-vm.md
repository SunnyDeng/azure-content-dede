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
	ms.date="11/23/2015" 
	ms.author="bradsev" />


# Bereitstellen der Microsoft Data Science Virtual Machine

## Einführung

Die Microsoft Data Science Virtual Machine ist das Image eines virtueller Azure-Computers (VM), das vorinstalliert und mit einigen gängigen Tools konfiguriert ist, die häufig für Datenanalysen und Machine Learning verwendet werden. Die enthaltenen Tools sind:

- Revolution R Open
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

- **Ein Azure-Abonnement**: Informationen zum Bezug finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Ein Azure-Speicherkonto**: Um eines zu erstellen, siehe [Erstellen eines Azure-Speicherkontos](storage-create-storage-account.md#create-a-storage-account). Das Speicherkonto kann alternativ beim Erstellen der VM erstellt werden, wenn Sie kein vorhandenes Konto verwenden möchten.


## Erstellen Ihrer Microsoft Data Science Virtual Machine

Es folgen die Schritte zum Erstellen einer Instanz der Microsoft Data Science Virtual Machine:

1.	Wechseln Sie im [Azure-Portal](https://portal.azure.com/#gallery/microsoft-ads.standard-data-science-vmstandard-data-science-vm) zur Auflistung der virtuellen Computer.
2.	 Klicken Sie unten auf die Schaltfläche **Erstellen**, um einen Assistenten aufzurufen. ![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 Die folgenden Abschnitte enthalten die **Eingaben** für jeden der **fünf Schritte** im Assistenten (im rechten Teil der obigen Abbildung aufgelistet), der zum Erstellen der Microsoft Data Science Virtual Machine wird. Die zum Konfigurieren der einzelnen Schritte erforderlichen Eingaben sind:

  **a. Grundlagen**:

    - **Name**: Name of your data science server you are creating.
    - **User Name**: Admin account login id
    - **Password**: Admin account password
    - **Subscription**: If you have more than one subscription, select the one on which the machine will be created and billed
    - **Resource Group**: You can create a new one or use an existing group
    - **Location**: Select the data center that is most appropriate. Usually it is the data center that has most of your data or is closest to your physical location for fastest network access

  **b. Größe**:

    - Select one of the server types that meets your functional requirement and cost constraints. You can get more choices of VM sizes by selecting “View All”

  **c. Einstellungen**:

    - **Disk Type**: Choose Premium if you prefer a solid state drive (SSD), else choose “Standard”.
    - **Storage Account**: You can create a new Azure storage account in your subscription or use an existing one in the same *Location* that was chosen on the Basics step of the wizard.
    - **Other parameters**: In most cases you will just use the default values. You can hover over the informational link for help on the specific fields in case you want to consider the use of non-default values.

  **d. Zusammenfassung**:

    - Verify that all information you entered is correct.

  **e. Kaufen**:

    - Click on **Buy** to start the provisioning. A link is provided to the terms of the transaction. The VM does not have any additional charges beyond the compute for the server size you chose in the **Size** step. 


Die Bereitstellung sollte ungefähr 10 bis 20 Minuten dauern. Der Status der Bereitstellung wird im Azure-Portal angezeigt.

## Zugreifen auf die Microsoft Data Science Virtual Machine

Nach der VM-Erstellung können Sie sich mithilfe von Remotedesktop mit den Anmeldeinformationen des Administratorkontos anmelden, die Sie im Abschnitt „Grundlagen“ von Schritt 4 erstellt haben.

Sobald Ihre VM erstellt und bereitgestellt wurde, können Sie beginnen, die installierten und konfigurierten Tools zu verwenden. Für viele der Tools gibt es Desktopsymbole und Kacheln im Startmenü.

## In der Microsoft Data Science Virtual Machine installierte Tools

### R
Wenn Sie R für die Analysen verwenden möchten, ist auf der VM Revolution R Open (RRO) installiert. Dies ist eine Open-Source-Distribution von R, und sie ist vollständig mit CRAN-R kompatibel. RRO enthält das neueste Open-Source-R-Modul und die Intel Math Kernel Library. Eine IDE namens „RRO RGui“ ist ebenfalls auf der VM vorhanden. Sie können nach Wunsch auch andere IDEs wie [RStudio](http://www.rstudio.com) herunterladen und verwenden.

### Python
Für die Entwicklung mithilfe von Python wurde Anaconda Python Distribution 2.7 installiert. Diese Distribution enthält die Python-Basisversion sowie etwa 300 der beliebtesten Pakete für Mathematik, Entwicklung und Datenanalysen. Sie können die Python-Tools für Visual Studio (PTVS), die mit der Visual Studio 2015 Community Edition installiert werden, oder eine der mit Anaconda gebündelten IDEs wie IDLE oder Spyder verwenden. Sie können eines dieser Tools über eine Suche auf der Suchleiste starten (**Windows-Taste**+**S**).

### IPython Notebook
Zur Anaconda Distribution gehört außerdem ein IPython Notebook, eine Umgebung zum Freigeben von Code und Analysen. Ein IPython Notebook-Server wurde vorkonfiguriert. Es gibt ein Desktopsymbol, um den Browser für den Zugriff auf den Notebook-Server zu starten. Wenn Sie die VM über Remotedesktop verwenden, können Sie auch [https://localhost:9999/](https://localhost:9999/) für den Zugriff auf den IPython Notebook-Server aufrufen. (Hinweis: Fahren Sie einfach fort, wenn Warnungen zu Zertifikaten angezeigt werden.)

### Visual Studio 2015 Community Edition
Visual Studio Community Edition ist auf der VM installiert. Dies ist eine kostenlose Version der beliebten IDE von Microsoft, die Sie zur Evaluierung und für sehr kleine Teams verwenden können. Die Lizenzbedingungen können Sie [hier](https://www.visualstudio.com/support/legal/mt171547) prüfen. Öffnen Sie Visual Studio durch Doppelklicken auf das Desktopsymbol oder über das **Startmenü**. Sie können auch nach Programmen suchen, indem Sie die **Windows-Taste**+**S** drücken und „Visual Studio“ eingeben.

Hinweis: Es könnte eine Meldung angezeigt werden, die besagt, dass der Evaluierungszeitraum abgelaufen ist. Geben Sie die Anmeldeinformationen eines Microsoft-Kontos ein, oder erstellen Sie ein entsprechendes Konto, um Zugriff auf die Visual Studio Community Edition zu erhalten. Anschließend können Sie Projekte in Sprachen wie C# und Python erstellen.

### SQL Server Express
Eine eingeschränkte Version von SQL Server gehört ebenfalls zu Visual Studio Community Edition. Sie können auf die SQL Server-Instanz zugreifen, indem Sie **SQL Server Management Studio** starten. Ihr VM-Name wird als Servernamen angegeben. Verwenden Sie die Windows-Authentifizierung, wenn Sie als Administrator unter Windows angemeldet sind. Sobald SQL Server Management Studio gestartet wurde, können Sie andere Benutzer erstellen, Datenbanken erstellen, Daten importieren und SQL-Abfragen ausführen.

### Azure 
Mehrere Azure-Tools sind auf der VM installiert: Es gibt eine Desktopverknüpfung für den Zugriff auf die Azure SDK-Dokumentation. – **AzCopy** wird zum Verschieben von Daten in das und aus dem Microsoft Azure Storeag-Konto verwendet. – **Azure Storage Explorer** wird verwendet, um die Objekte zu durchsuchen, die in Ihrem Azure-Speicherkonto gespeichert sind. – **Microsoft Azure PowerShell** ist eine Skriptsprache zur Verwaltung von Azure-Ressourcen in einer Skriptsprache, die auch auf Ihrer VM installiert ist.

###Power BI

Um Dashboards und aussagekräftige Visualisierungen zu erstellen, wurde **Power BI Desktop** installiert. Verwenden Sie dieses Tool, um Daten aus verschiedenen Quellen abzurufen, Dashboards und Berichte zu erstellen und diese in der Cloud zu veröffentlichen. Weitere Informationen finden Sie auf der [Power BI](http://powerbi.microsoft.com)-Website.

Hinweis: Für den Zugriff auf Power BI benötigen Sie ein Office 365-Konto.

## Zusätzliche Microsoft-Entwicklungstools
Der [**Microsoft-Webplattform-Installer**](https://www.microsoft.com/web/downloads/platform.aspx) kann verwendet werden, um andere Microsoft-Entwicklungstools zu suchen und herunterzuladen. Es gibt auch eine Verknüpfung mit dem Tool auf dem Desktop der Microsoft Data Science Virtual Machine.

<!---HONumber=AcomDC_1125_2015-->