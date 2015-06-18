<properties 
	pageTitle="Einrichten eines virtuellen Azure-Computers für Data Science" 
	description="Richten Sie einen virtuellen Azure-Computer für die Verwendung in einer cloudbasierten Data Science-Umgebung mit IPython-Server ein." 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# Einrichten eines virtuellen Azure-Computers für Data Science

In diesem Thema wird das Bereitstellen und Konfigurieren eines virtuellen Azure-Computers als Teil einer cloudbasierten Data Science-Umgebung beschrieben. Der virtuelle Windows-Computer wird mit Unterstützung von Tools wie IPython Notebook, Azure-Speicher-Explorer und AzCopy sowie anderer Hilfsprogramme, die für Data Science-Projekte hilfreich sind, konfiguriert. Azure-Speicher-Explorer und AzCopy stellen z. B. hilfreiche Möglichkeiten zum Hochladen von Daten von Ihrem lokalen Computer in den Azure-Blob-Speicher oder das Herunterladen aus dem Blob-Speicher auf Ihren lokalen Computer bereit.

## <a name="create-vm"></a>Schritt 1: Erstellen eines allgemeinen virtuellen Azure-Computers

Wenn Sie bereits über einen virtuellen Azure-Computer verfügen und darauf einen IPython Notebook-Server einrichten möchten, können Sie diesen Schritt überspringen und mit [Schritt 2: Hinzufügen eines Endpunkts für IPython Notebooks auf einem vorhandenen virtuellen Computer](#add-endpoint) fortfahren. 
 
Bevor Sie mit der Erstellung eines virtuellen Computers in Azure beginnen, müssen Sie die Größe des Computers bestimmen, der zum Verarbeiten der Daten für das Projekt erforderlich ist. Kleinere Computer verfügen über weniger Arbeitsspeicher und weniger CPU-Kerne als leistungsstärkere Computer, aber sie sind auch preisgünstiger. Eine Liste der Computertypen und Preise finden Sie auf der Seite [Virtuelle Computer Preise](http://azure.microsoft.com/pricing/details/virtual-machines/). 

1. Melden Sie sich unter "https://manage.windowsazure.com" an, und klicken Sie links unten auf **+NEW**. Ein Fenster wird angezeigt. Wählen Sie **COMPUTE** -> **VIRTUAL MACHINE** -> **FROM GALLERY**.

	![Create workspace][24]

2. Wählen Sie eines der folgenden Images aus: 

	* Windows Server 2012 R2 Datacenter 
	* Windows Server Essentials Experience (Windows Server 2012 R2) 

	Klicken Sie anschließend unten rechts auf den Pfeil nach rechts, um auf die nächste Konfigurationsseite zu wechseln.
	
	![Create workspace][25]

3. Geben Sie einen Namen für den zu erstellenden virtuellen Computer ein, wählen Sie die Größe des Computers entsprechend den zu verarbeitenden Datenvolumen und der gewünschten Leistungsstärke aus (Speichergröße und Anzahl von Prozessorkernen), und geben Sie einen Benutzernamen und ein Kennwort für den Computer ein. Klicken Sie anschließend auf den Pfeil nach rechts, um zur nächsten Konfigurationsseite zu wechseln.

	![Create workspace][26]

4. Wählen Sie **REGION/AFFINITY GROUP/VIRTUAL NETWORK** mit dem **SPEICHERKONTO** aus, das Sie für diesen virtuellen Computer verwenden möchten, und wählen Sie dann diesas Speicherkonto aus. Fügen Sie im unteren Bereich im Feld **ENDPOINTS** einen Endpunkt hinzu, indem Sie den Namen des Endpunkts eingeben (in diesem Fall "IPython"). Sie können eine beliebige Zeichenfolge als **NAME** für den Endpunkt auswählen sowie eine beliebige ganze Zahl zwischen 0 und 65.536, sofern dieser **PUBLIC PORT** noch **verfügbar** ist. Der **PRIVATE PORT** muss **9999** lauten. Sie sollten **keine** öffentlichen Ports verwenden, denen bereits Internetdienste zugewiesen wurden. [Ports for Internet Services](http://www.chebucto.ns.ca/~rakerman/port-table.html) (in englischer Sprache) enthält eine Liste der Ports, die zugewiesen wurden und daher vermieden werden sollten. 

	![Create workspace][27]

	>[AZURE.NOTE] Wenn der Endpunkt in diesem Schritt hinzugefügt wird, können Sie [Schritt 2: Hinzufügen eines Endpunkts für IPython Notebooks auf einem vorhandenen virtuellen Computer](#add-endpoint) überspringen.

5. Aktivieren Sie das Kontrollkästchen, um den virtuellen Computer bereitzustellen. 

	![Create workspace][28]


Die Bereitstellung des virtuellen Computers dauert 15-25 Minuten. Nachdem der virtuelle Computer erstellt wurde, sollte als Status für diesen Computer **Running** angezeigt werden.

![Create workspace][29]
	
## <a name="add-endpoint"></a>Schritt 2: Hinzufügen eines Endpunkts für IPython Notebooks auf einem vorhandenen virtuellen Computer

Wenn Sie den virtuellen Computer mithilfe der Anweisungen in Schritt 1 erstellt haben, wurde der Endpunkt für IPython Notebook bereits hinzugefügt. Sie können diesen Schritt deshalb überspringen. 

Wenn der virtuelle Computer bereits vorhanden ist und Sie einen Endpunkt für IPython Notebook (die Installation erfolgt in Schritt 3) hinzufügen möchten, melden Sie sich zunächst im Azure-Verwaltungsportal an, wählen Sie den virtuellen Computer aus, und fügen Sie den Endpunkt für den IPython Notebook-Server hinzu. Die folgende Abbildung zeigt einen Screenshot des Portals nach dem Hinzufügen des Endpunkts für IPython Notebook auf einem virtuellen Windows-Computer. 

![Create workspace][17]

## <a name="run-commands"></a>Schritt 3: Installieren von IPython Notebook und anderen Tools

Nachdem der virtuelle Computer erstellt wurde, verwenden Sie zum Anmelden auf dem virtuellen Windows-Computer das Remotedesktop-Protokoll (RDP). Anweisungen dazu finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](../virtual-machines-log-on-windows-server.md). Öffnen Sie die **Eingabeaufforderung** (**kein PowerShell-Befehlsfenster**) als Administrator, und führen Sie den folgenden Befehl aus.
 
    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Nach Abschluss der Installation wird der IPython Notebook-Server automatisch im Verzeichnis *C:\Users\&#60;user name>\Documents\IPython Notebooks* gestartet.

Geben Sie, wenn Sie dazu aufgefordert werden, ein Kennwort für IPython Notebook und das Kennwort für den Administrator des Computers ein. Dadurch kann IPython Notebook auf dem Computer als Dienst ausgeführt werden. 

## <a name="access"></a>Schritt 4: Zugreifen auf IPython Notebooks von einem Webbrowser aus
Öffnen Sie für den Zugriff auf den IPython Notebook-Server einen Webbrowser, und geben Sie in das Textfeld die URL *https://&#60;virtual machine DNS name>:&#60;public port number>* ein. In diesem Beispiel sollte *&#60; public port number>* die Portnummer sein, die Sie beim Hinzufügen des IPython Notebook-Endpunkts angegeben haben. Bei Auswahl von *443* als öffentliche Portnummer kann der Zugriff auf IPython Notebook ohne explizite Angabe der Portnummer in das Textfeld "URL" erfolgen. Andernfalls ist die Angabe von **&#60;public port number>* aber erforderlich. 

Den *&#60;virtual machine DNS name>* finden Sie im Azure-Verwaltungsportal. Klicken Sie nach der Anmeldung im Verwaltungsportal auf **VIRTUAL MACHINES**, wählen Sie den erstellten Computer aus, und wählen Sie dann das **DASHBOARD** aus. Der DNS-Name wird wie in der folgenden Abbildung angezeigt:

![Create workspace][19]

Es wird eine Warnung wie _Es besteht ein Problem mit dem Sicherheitszertifikat der Website_ (Internet Explorer) oder _Dies ist keine sichere Verbindung_ (Chrome) angezeigt, wie in den folgenden Abbildungen dargestellt. Klicken Sie zum Fortfahren auf **Laden dieser Website fortsetzen (nicht empfohlen)** (Internet Explorer) oder auf **Erweitert** und dann **Zu &#60;*DNS Name* fortfahren> (unsicher)** (Chrome). Geben Sie dann das Kennwort ein, das Sie zuvor für den Zugriff auf IPython Notebooks festgelegt haben.

Internet Explorer:
![Create workspace][20]

Chrome:
![Create workspace][21]

Nach Ihrer Anmeldung bei IPython Notebook wird im Browser das Verzeichnis *DataScienceSamples* angezeigt. Dieses Verzeichnis enthält Beispiele für IPython Notebook, die von Microsoft freigegeben wurden, damit Benutzer Data Science-Aufgaben durchführen können. Diese Beispiele für IPython Notebooks werden während der Einrichtung des IPython Notebook-Servers aus dem [**GitHub-Repository**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) auf die virtuellen Computer ausgecheckt. Microsoft verwaltet und aktualisiert dieses Repository häufig. Sie können das GitHub-Repository regelmäßig auf aktualisierte Beispiele für IPython Notebooks überprüfen. 
![Create workspace][18]

## <a name="upload"></a>Schritt 5: Hochladen eines vorhandenen IPython Notebooks von einem lokalen Computer auf den IPython Notebook-Server

IPython Notebooks bieten eine einfache Möglichkeit für das Hochladen vorhandener IPython Notebooks von lokalen Computern auf den IPython Notebook-Server auf virtuellen Computern. Klicken Sie nach der Anmeldung bei IPython Notebook in einem Webbrowser auf das **Verzeichnis**, in das das IPython Notebook hochgeladen werden soll. Wählen Sie anschließend im **Datei-Explorer** eine IPYNB-Datei mit dem IPython Notebook auf dem lokalen Computer für das Hochladen aus, und ziehen Sie es in Ihrem Webbrowser in das Verzeichnis des IPython Notebooks. Klicken Sie auf die Schaltfläche **Upload**, um die IPYNB-Datei auf den IPython Notebook-Server hochzuladen. Andere Benutzer können es dann in ihren Webbrowsern verwenden.

![Create workspace][22]

![Create workspace][23]


## <a name="shutdown"></a>Herunterfahren und Freigeben von nicht genutzten virtuellen Computern

Virtuelle Azure-Computer werden **nach Nutzung abgerechnet**. Damit Sie nicht für ungenutzte virtuelle Computer bezahlen müssen, sollten Sie diese in den Status **Angehalten (Freigegeben)** versetzen, wenn sie nicht mehr benötigt werden.

> [AZURE.NOTE] Beim Herunterfahren virtueller Computer aus der VM heraus (mithilfe der Windows-Energieoptionen) wird der virtuelle Computer beendet, er bleibt jedoch weiterhin zugeordnet. Um sicherzustellen, dass virtuelle Computer nicht weiterhin abgerechnet werden, beenden Sie sie immer im [Azure-Verwaltungsportal](http://manage.windowsazure.com/). Sie können die VM auch über PowerShell beenden, indem Sie **ShutdownRoleOperation** mit der "PostShutdownAction" "StoppedDeallocated" aufrufen.

So fahren Sie den virtuellen Computer herunter und heben die Zuordnung auf:

1. Melden Sie sich mit Ihrem Konto auf dem [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.  

2. Wählen Sie in der Navigationsleiste links **VIRTUAL MACHINES** aus.

3. Klicken Sie in der Liste der virtuellen Computer auf den Namen des virtuellen Computers, und wechseln Sie zur Seite **DASHBOARD**.

4. Klicken Sie unten auf der Seite auf **SHUTDOWN**. 

![VM Shutdown][15]

Der virtuelle Computer wird freigegeben, aber nicht gelöscht. Sie können den virtuellen Computer jederzeit im Azure-Verwaltungsportal neu starten.

## Ihre Azure-VM ist damit einsatzbereit. Wie lauten die nächsten Schritte?

Sie können Ihren virtuellen Computer jetzt in Ihren Data Science-Übungen verwenden. Der virtuelle Computer kann auch als IPython Notebook-Server für das Durchsuchen und Verarbeiten von Daten und für andere Aufgaben in Verbindung mit Azure Machine Learning und dem Cloud Data Science-Vorgang verwendet werden. 

Die nächsten Schritte im Data Science-Ablauf finden Sie im [Leitfaden: Erweiterte Datenverarbeitung in Azure](machine-learning-data-science-advanced-data-processing.md). Dazu gehören auch das Verschieben von Daten nach HDInsight und das Verarbeiten und Extrahieren von Stichproben für das Trainieren anhand der Daten mit Azure Machine Learning.

* Informationen zum Verschieben von Daten aus dem Azure-Blob-Speicher nach HDInsight finden Sie unter [Erstellen und Laden von Daten in Hive-Tabellen aus Azure-Blob-Speicher](machine-learning-data-science-hive-tables.md).
* Weitere Informationen zum Verarbeiten von Daten in HDInsight mit Hive-Abfragen finden Sie unter [Übermitteln von Hive-Abfragen an HDInsight Hadoop-Cluster in Cloud Data Science](machine-learning-data-science-process-hive-tables.md).
* Informationen zum Extrahieren von Stichproben aus Daten in HDInsight finden Sie unter [Extrahieren von Stichproben aus Daten in Azure HDInsight Hive-Tabellen](machine-learning-data-science-sample-data-hive.md).



[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png




<!--HONumber=49--> 