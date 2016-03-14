<properties
	pageTitle="Zehn Dinge, die Sie mit der Data Science Virtual Machine machen können | Microsoft Azure"
	description="Führen Sie verschiedene Durchsuchungen von Daten und Modellierungsaufgaben auf der Data Science Virtual Machine aus."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/13/2016"
	ms.author="gokuma;weig;bradsev" />

# Zehn Dinge, die Sie mit der Data Science Virtual Machine machen können 

Die Microsoft Data Science Virtual Machine (DSVM) ist eine leistungsfähige Data Science-Entwicklungsumgebung, mit der Sie unterschiedliche Aufgaben zum Durchsuchen und Modellieren von Daten ausführen können. Die Umgebung wird bereits mit mehreren gängigen Datenanalysetools geliefert, sodass Sie schnell mit Ihrer Analyse für lokale Bereitstellungen, Cloud- oder Hybridbereitstellungen beginnen können. Die DSVM arbeitet eng mit zahlreichen Azure-Diensten zusammen und kann Daten lesen und verarbeiten, die bereits in Azure SQL Data Warehouse, Azure Data Lake, Azure Storage oder in DocumentDB gespeichert sind. Sie kann auch andere Analysetools wie Azure Machine Learning und Azure Data Factory nutzen.


In diesem Artikel erfahren Sie, wie Sie Ihre DSVM nutzen können, um verschiedene Data Science-Aufgaben auszuführen und mit anderen Azure-Diensten zu interagieren. Die folgenden Aufgaben können Sie z. B. auf der DSVM ausführen:

1. Durchsuchen von Daten und Entwickeln von Modellen lokal auf der DSVM unter Verwendung von Microsoft R Server, Python
2. Verwenden eines Jupyter-Notebooks zum Experimentieren mit Ihren Daten mithilfe von Python 2, Python 3, Microsoft R in einem Browser in einer Enterprise-tauglichen Version von R, entwickelt für Skalierbarkeit und Leistung
3. Operationalisieren von Modellen, die mit R und Python in Azure Machine Learning erstellt wurden, damit Clientanwendungen mithilfe einer einfachen Webdiensteschnittstelle auf Ihre Modelle zugreifen können
4. Verwalten von Azure-Ressourcen über PowerShell oder Azure-Portal 
5. Erweitern Ihres Speicherplatzes und Freigeben von umfangreichen Datasets/Codes für Ihr gesamtes Team durch Erstellen eines Azure-Dateispeichers als bereitstellbares Laufwerk auf Ihrer DSVM 
6. Freigeben von Code für Ihr Team über Github und Zugriff auf Ihr Repository mit den vorinstallierten Git Clients – Git Bash, Git GUI.
7. Zugriff auf verschiedene Azure Daten und Analysedienste wie Azure-Blobspeicher, Azure Data Lake, Azure, HDInsight (Hadoop), Azure DocumentDB, Azure SQL Data Warehouse und Datenbanken
8. Erstellen von Berichten und Dashboard mit Power BI Desktop – auf der DSVM vorinstalliert – und deren Bereitstellung in der Cloud
9. Dynamische Skalierung Ihrer DSVM gemäß Ihren Projektanforderungen 
10. Installieren zusätzlicher Tools auf Ihrem virtuellen Computer

>[AZURE.NOTE] Für viele der in diesem Artikel aufgelisteten zusätzlichen Datenspeicher- und Analysedienste fallen zusätzliche Nutzungsgebühren an. Genauere Angaben zu den Preisen finden Sie auf der Seite [Azure-Preise](https://azure.microsoft.com/pricing/).

**Voraussetzungen**

- Sie benötigen ein Azure-Abonnement. Sie können sich [hier](https://azure.microsoft.com/free/) für eine kostenlose Testversion registrieren. 

- Anleitungen für die Bereitstellung einer Data Science Virtual Machine in Ihrem Azure-Portal finden Sie unter [Creating a virtual machine](https://ms.portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm) (Erstellen eines virtuellen Computers).

## 1\. Durchsuchen von Daten und Entwickeln von Modellen mit Microsoft R Server oder Python

Sie können Ihre Datenanalysen mit Sprachen wie R und Python direkt auf der DSVM ausführen.

Für R können Sie eine IDE namens „Revolution R Enterprise 8.0“ verwenden, die Sie im Startmenü oder auf dem Desktop finden. Microsoft hat Bibliotheken zusätzlich zu Open Source/CRAN-R bereitgestellt, um skalierbare Analysen zu ermöglichen sowie das Analysieren von Datenmengen, deren Größe den Arbeitsspeicher überschreitet, in parallelen Datenblöcken. Sie können auch eine R-IDE ihrer Wahl wie [RStudio](https://www.rstudio.com/products/rstudio-desktop/) installieren.

Für Python können Sie eine IDE wie Visual Studio Community Edition verwenden, bei der die „Python Tools for Visual Studio“-Erweiterung (PTVS) vorinstalliert ist. Standardmäßig wird nur eine Basisinstallation von Python 2.7 (ohne Analysebibliothek wie SciKit, Pandas) auf PTVS konfiguriert. Um Anaconda Python 2.7 und 3.5 zu aktivieren, müssen Sie die folgenden Schritte ausführen:

* Erstellen Sie benutzerdefinierte Umgebungen für jede Version durch Navigieren zu **Tools** -> **Python Tools** -> **Python Environments**, und klicken Sie auf „**+ Custom**“ in Visual Studio 2015 Community Edition.
* Geben Sie eine Beschreibung ein, und legen Sie für die Umgebungspräfixpfade *c:\\anaconda* for Anaconda Python 2.7 ODER *c:\\anaconda\\envs\\py35* for Anaconda Python 3.5 fest 
* Klicken Sie auf **Auto Detect** und **Apply**, um die Umgebung zu speichern. 

So sieht das benutzerdefinierte Umgebungssetup in Visual Studio aus.

![PTVS-Setup](./media/machine-learning-data-science-vm-do-ten-things/PTVSSetup.png)

In der [PTVS-Dokumentation](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) finden Sie zusätzliche Informationen zum Erstellen der Python-Umgebungen.
  
Jetzt können Sie ein neues Python-Projekt erstellen. Navigieren Sie zu **File** -> **New** -> **Project** -> **Python**, und wählen Sie die Python-Anwendung aus, die sie erstellen. Sie können die Python-Umgebung für das aktuelle Projekt auf die gewünschte Version (Anaconda 2.7 oder 3.5) festlegen: Klicken Sie mit der rechten Maustaste auf **Python environment**, wählen Sie **Add/Remove Python Environments** aus, und wählen Sie dann die gewünschte Umgebung aus, die Sie dem Projekt zuordnen möchten. Weitere Informationen zum Arbeiten mit PTVS finden Sie auf der [Dokumentationsseite](https://github.com/Microsoft/PTVS/wiki) des Produkts.

## 2\. Verwenden eines Jupyter Notebooks zum Durchsuchen und Modellieren Ihrer Daten mit Python oder R

Das Jupyter Notebook ist eine leistungsfähige Umgebung, die eine browserbasierte „IDE“ für das Durchsuchen und Modellieren von Daten bereitstellt. Sie können Python 2, Python 3 oder R (sowohl Microsoft R Open als auch Microsoft R Server) in einem Jupyter Notebook verwenden.
 
Zum Starten des Jupyter Notebooks klicken Sie auf das Symbol **Jupyter Notebook** im Startmenü/auf dem Desktop. Auf der DSVM können Sie auch zu „https://localhost:9999/“ wechseln, um auf das Jupyter Notebook zuzugreifen. Wenn sie zur Eingabe eines Kennworts aufgefordert werden, verwenden Sie die Anweisungen auf der [DSVM-Dokumentationsseite](machine-learning-data-science-provision-vm.md/#how-to-create-a-strong-password-on-the-jupyter-notebook-server), um ein sicheres Kennwort für den Zugriff auf das Jupyter Notebook zu erstellen.

Sobald Sie das Notebook geöffnet haben, sehen Sie ein Verzeichnis, das einige Beispielnotebooks enthält, die in der DSVM vorkonfiguriert sind. Sie können jetzt:

- auf das Notebook klicken, um den Code zu sehen 
- jede Zelle über **UMSCHALT+EINGABE-Taste** ausführen 
- das gesamte Notebook ausführen, indem Sie auf **Cell** -> **Run** klicken
- ein neues Notebook erstellen, indem Sie auf das Jupyter-Symbol klicken (linke obere Ecke), dann auf der rechten Seite auf die Schaltfläche **New** klicken und die Notebook-Sprache (auch Kernels genannt) wählen. 

>[AZURE.NOTE] Derzeit werden Python 2.7, Python 3.5 und R unterstützt. Der R-Kernel unterstützt sowohl das Programmieren in Open Source-R als auch den Enterprise-skalierbaren Microsoft R-Server.

Sobald Sie das Notebook geöffnet haben, können Sie mit den Bibliotheken Ihrer Wahl Ihre Daten durchsuchen, das Modell erstellen und das Modell testen.


## 3\. Erstellen von Modellen mit R oder Python und Operationalisieren dieser Modelle durch Verwenden von Azure Machine Learning

Sobald Sie Ihr Modell erstellt und überprüft haben, ist der nächste Schritt in der Regel die Bereitstellung für die Produktion. Dies ermöglicht Ihren Clientanwendungen, die Modellvorhersagen auf Echtzeit- oder Batchmodusbasis aufzurufen. Azure Machine Learning bietet einen Mechanismus zum Operationalisieren eines in R oder Python erstellten Modells.

Wenn Sie Ihr Modell in Azure Machine Learning operationalisieren, steht ein Webdienst zur Verfügung, der Clients REST-Aufrufe erlaubt, die Eingabeparameter übergeben und Vorhersagen aus dem Modell als Ausgaben empfangen.

>[AZURE.NOTE] Wenn Sie sich noch nicht für AzureML registriert haben, erhalten Sie einen freien oder einen Standardarbeitsbereich, wenn Sie die Startseite von [AzureML Studio](https://studio.azureml.net/) besuchen und auf „Get Started“ klicken.

### Erstellen und Operationalisieren von Python-Modellen

Hier sehen Sie den Ausschnitt eines in einem Python Jupyter Notebook entwickelten Codes, der durch Verwenden der SciKit-Learn-Bibliothek ein einfaches Modell erstellt.
  
	#IRIS classification
	from sklearn import datasets
	from sklearn import svm
	clf = svm.SVC()
	iris = datasets.load_iris()
	X, y = iris.data, iris.target
	clf.fit(X, y) 
 
Die Methode, die zum Bereitstellen Ihrer Python-Modelle in Azure Machine Learning verwendet wird, umschließt die Vorhersage des Modells in eine Funktion und stattet sie mit Attributen aus, die von der vorinstallierten Azure Machine Learning-Python-Bibliothek bereitgestellt werden, die Ihre Azure Machine Learning-Arbeitsbereichs-ID, den API-Schlüssel, die Eingabe- und Rückgabeparameter bezeichnet.

	from azureml import services
	@services.publish(workspaceid, auth_token)
	@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
	@services.returns(int) #0, or 1, or 2
	def predictIris(sep_l, sep_w, pet_l, pet_w):
 	inputArray = [sep_l, sep_w, pet_l, pet_w]
	return clf.predict(inputArray)

Ein Client kann jetzt den Webdienst aufrufen. Es gibt benutzerfreundliche Wrapper, die die REST-API-Anforderungen erstellen. Hier ist ein Beispielcode zur Nutzung des Webdiensts.

	# Consume through web service URL and keys
	from azureml import services
	@services.service(url, api_key)
	@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
	@services.returns(float)
	def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

	IrisPredictor(3,2,3,4)


>[AZURE.NOTE] Die Azure Machine Learning-Bibliothek wird derzeit nur von Python 2.7 unterstützt.

### Erstellen und Operationalisieren von R-Modellen

Sie können R-Modelle, die auf der Data Science Virtual Machine oder anderswo erstellt wurden, auf Azure Machine Learning in einer Weise bereitstellen, die derjenigen für Python ähnelt. Gehen Sie wie folgt vor:

- Erstellen Sie wie unten gezeigt eine Datei „settings.json“, um Ihre Arbeitsbereichs-ID und Ihr Authentifizierungstoken bereitzustellen.
- Schreiben Sie einen Wrapper für die Vorhersagefunktion des Modells. 
- Rufen Sie ```publishWebService``` in der Azure Machine Learning-Bibliothek auf, um den Wrapper für die Funktion zu übergeben.  

Hier sehen Sie die Vorgehensweise und Codeausschnitte, die Sie verwenden können, um ein Modell als Webdienst in Azure Machine Learning einzurichten, zu erstellen, zu veröffentlichen oder zu nutzen.

#### Einrichtung

1.  Installieren Sie das AzureML-R-Paket, indem Sie ```install.packages("AzureML")``` in die IDE „Revolution R Enterprise 8.0“ oder in Ihre R-IDE eingeben. 
2.  RTools können Sie [hier](https://cran.r-project.org/bin/windows/Rtools/) herunterladen. Sie benötigen das Zip-Programm im Pfad (mit der Bezeichnung zip.exe), um Ihr R-Paket in AzureML zu operationalisieren. 
3.  Erstellen Sie eine Datei „settings.json“ in einem Verzeichnis namens ```.azureml``` in Ihrem Basisverzeichnis, und geben Sie die Parameter aus Ihrem Azure ML-Arbeitsbereich ein:

Struktur der Datei „settings.json“:

	{"workspace":{
	"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
	"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
	}}


#### Erstellen eines Modells in R und seine Veröffentlichung in Azure ML

	library(AzureML)
	ws <- workspace(config="~/.azureml/settings.json")

	if(!require("lme4")) install.packages("lme4")
	library(lme4)
	set.seed(1)
	train <- sleepstudy[sample(nrow(sleepstudy), 120),]
	m <- lm(Reaction ~ Days + Subject, data = train)
 
	# Define a prediction function to publish based on the model:
	sleepyPredict <- function(newdata){
  		predict(m, newdata=newdata)
	}
 
	ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### Nutzung des bereitgestellten Modells in Azure ML

Um das Modell aus einer Clientanwendung zu nutzen, verwenden wir die Azure Machine Learning-Bibliothek, um den veröffentlichten Webdienst anhand seines Namens mit dem API-Aufruf `services` zu suchen, und um den Endpunkt zu bestimmen. Dann rufen Sie einfach die Funktion `consume` auf und übergeben den Datenrahmen, der vorhergesagt werden soll. Der folgende Code wird verwendet, um das als Azure Machine Learning-Webdienst veröffentlichte Modell zu nutzen.


	library(AzureML)
	library(lme4)
	ws <- workspace(config="~/.azureml/settings.json")

	s <-  services(ws, name = "sleepy lm")
	s <- tail(s, 1) # use the last published function, in case of duplicate function names

	ep <- endpoints(ws, s)

	# OK, try this out, and compare with raw data
	ans = consume(ep, sleepstudy)$ans

Weitere Informationen über die Azure Machine Learning-R-Bibliothek finden Sie [hier](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## 4\. Verwalten von Azure-Ressourcen über PowerShell oder Azure-Portal

Die DSVM ermöglicht Ihnen nicht nur, Ihre Analyselösung lokal auf dem virtuellen Computer zu erstellen, sondern auch, auf Dienste der Azure-Cloud von Microsoft zuzugreifen. Azure bietet mehrere Compute-, Speicher-, Datenanalyse- und andere Dienste, die Sie von Ihrer DSVM aus verwalten, und auf die Sie von dort aus zugreifen können.

Um Ihr Azure-Abonnement und Ihre Cloudressourcen zu verwalten, können Sie in Ihrem Browser auf das [Azure-Portal](portal.azure.com) zugreifen. Sie können auch Azure PowerShell zur Verwaltung Ihres Azure-Abonnements und Ihrer Ressourcen über ein Skript verwenden. Sie können Azure PowerShell über eine Verknüpfung auf dem Desktop oder im Startmenü „Microsoft Azure Powershell“ ausführen. In der [Microsoft Azure PowerShell-Dokumentation](../powershell-azure-resource-manager.md) finden Sie weitere Informationen dazu, wie Sie Ihr Azure-Abonnement und Ihre Ressourcen mithilfe von Windows PowerShell-Skripts verwalten können.


## 5\. Erweitern Ihres Speichers mit einem Shared-Dateisystem

Datenanalysten können große DataSets, Code oder andere Ressourcen innerhalb des Teams freigeben. Die DSVM selbst hat etwa 70 GB verfügbaren Speicherplatz. Um Ihren Speicher zu erweitern, können Sie den Azure-Dateidienst verwenden und auf der DSVM einbinden, oder über eine REST-API darauf zugreifen.

>[AZURE.NOTE] Der maximale Speicherplatz der Azure-Dateidienstfreigabe beträgt 5 TB, und die Begrenzung für eine einzelne Datei beträgt 1 TB.

Azure PowerShell können Sie verwenden, um eine Azure-Dateidienstfreigabe zu erstellen. Dieses Skript können Sie unter Azure PowerShell ausführen, um eine Azure-Dateidienstfreigabe zu erstellen.

	# Authenticate to Azure. 
	Login-AzureRmAccount
	# Select your subscription
	Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
	# Create a new resource group. 
	New-AzureRmResourceGroup -Name <dsvmdatarg>
	# Create a new storage account. You can reuse existing storage account if you wish. 
	New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
	# Set your current working storage account
	Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>
	
	# Create a Azure File Service Share
	$s = New-AzureStorageShare <<teamsharename>>
	# Create a directory under the FIle share. You can give it any name
	New-AzureStorageDirectory -Share $s -Path <directory name>
	# List the share to confirm that everything worked
	Get-AzureStorageFile -Share $s


Die neu erstellte Azure-Dateidienstfreigabe können Sie nun in jeden virtuellen Computer in Azure einbinden. Die VM sollte sich in dem gleichen Azure-Rechenzentrum wie das Speicherkonto befinden, um Latenz und Datenübertragungsgebühren zu vermeiden. Mit den folgenden Befehlen binden Sie das Laufwerk in die DSVM ein, die Sie unter Azure PowerShell ausführen können.


	# Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
	cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>
	
	# Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
	net use z:  \<mydatadisk>.file.core.windows.net<<teamsharename>>


Jetzt können Sie auf dieses Laufwerk wie auf jedes normale Laufwerk auf der VM zugreifen.

## 6\. Freigeben von Code für Ihr Team mithilfe von Github 

Github ist ein Coderepository, wo Sie umfangreichen Beispielcode und Quellen für verschiedene Tools finden, die unterschiedliche Technologien einsetzen. Sie werden von der gesamten Entwicklercommunity verwendet. Sie nutzt Git als Technologie zum Nachverfolgen und speichern der Versionen der Codedateien. Github ist auch eine Plattform, auf der Sie Ihr eigenes Repository erstellen können, um Shared-Code und Dokumentation Ihres Teams zu speichern, die Versionskontrolle zu implementieren und auch zu steuern, wer Zugriffsrechte hat, um den Code anzuzeigen und dazu beizutragen. Auf den [Github-Hilfeseiten](https://help.github.com/) finden Sie weitere Informationen zur Verwendung von Git. Sie können Github als eine der Möglichkeiten nutzen, mit Ihrem Team zusammenzuarbeiten, von der Community entwickelten Code verwenden und wiederum Code zur Community beitragen.

Die DSVM enthält bereits Clienttools, die den Zugriff auf das Github-Repository sowohl über die Befehlszeile als auch die GUI ermöglichen. Das Befehlszeilentool zum Arbeiten mit Git und Github heißt Git Bash. Die auf der DSVM installierte Version von Visual Studio besitzt die Git-Erweiterungen. Sie finden die Startsymbole für diese Tools auf dem Desktop und im Startmenü.

Zum Herunterladen von Code aus einem Github-Repository verwenden Sie den Befehl ```git clone```. Um z. B. das von Microsoft veröffentlichte Data Science-Repository in das aktuelle Verzeichnis herunterzuladen, können Sie den folgenden Befehl ausführen, sobald Sie sich in ```git-bash``` befinden.

	git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

In Visual Studio können Sie den gleichen Klonvorgang ausführen. Der folgende Screenshot zeigt Ihnen, wie Sie in Visual Studio auf Git- und Github-Tools zugreifen können.

![Git in Visual Studio](./media/machine-learning-data-science-vm-do-ten-things/VSGit.PNG)


Weitere Informationen zur Verwendung von Git zur Arbeit mit Ihrem Github-Repository finden Sie in mehreren Quellen, die unter github.com verfügbar sind. Der [Spickzettel](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) ist eine nützliche Referenz.


## 7\. Zugriff auf verschiedene Azure-Daten und Analysedienste

### Azure Blob

Azure Blob ist ein zuverlässiger, wirtschaftlicher Cloudspeicher für große und kleine Datenmengen. Wir sehen uns nun an, wie Sie Daten zu Azure Blob verschieben und auf Daten zugreifen können, die in einem Azure-Blob gespeichert sind.

**Voraussetzung**

- **Erstellen Sie Ihr eigenes Azure-Blobspeicherkonto im [Azure-Portal](http://portal.azure.com).**

![Create\_Azure\_Blob](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)


- Stellen Sie sicher, dass sich das vorinstallierte Befehlszeilentool AzCopy in ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` befindet. Sie können das Verzeichnis, das „azcopy.exe“ enthält, Ihrer PATH-Umgebungsvariable hinzufügen, damit Sie beim Ausführen des Tools nicht den ganzen Befehlspfad eingeben müssen. Weitere Informationen zu AzCopy finden Sie in der [AzCopy-Dokumentation](../storage/storage-use-azcopy.md).

- Starten Sie den Azure Storage-Explorer über ein Symbol auf dem Desktop der VM. Sie finden es auch im Verzeichnis ```C:\Program Files (x86)\Neudesic\Azure Storage Explorer 6```.

![AzureStorageExplorer\_v4](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_v4.png)

**Verschieben von Daten vom virtuellen Computer in den Azure-Blobspeicher: AzCopy**

Zum Verschieben von Daten zwischen Ihren lokalen Dateien und dem Blobspeicher können Sie AzCopy in der Befehlszeile oder PowerShell verwenden: `AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt`

Ersetzen Sie **C:\\myfolder** mit dem Pfad, in dem Ihre Datei gespeichert wird, **mystorageaccount** durch Ihren Blobspeicher-Kontonamen, **mycontainer** durch den Containernamen, **storage account key** durch Ihren Blobspeicher-Zugriffsschlüssel. Sie finden die Anmeldeinformationen für Ihr Speicherkonto im [Azure-Portal](http://portal.azure.com).

![StorageAccountCredential\_v2](./media/machine-learning-data-science-vm-do-ten-things/StorageAccountCredential_v2.png)

Führen Sie den AzCopy-Befehl in PowerShell oder im Eingabeaufforderungsfenster aus. Hier sehen Sie ein Beispiel für die Nutzung des AzCopy-Befehls:


	# Copy *.sql from local machine to a Azure Blob
	"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql
	
	# Copy back all files from Azure Blob container to Local machine
	
	"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
	


Kurz nachdem Sie den AzCopy-Befehl zum Kopieren in einen Azure-Blobspeicher ausgeführt haben, werden Ihre Dateien im Azure Storage-Explorer angezeigt.

![AzCopy\_run\_finshed\_Storage\_Explorer\_v3](./media/machine-learning-data-science-vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)


**Verschieben von Daten vom virtuellen Computer in den Azure-Blobspeicher: Azure Storage-Explorer**

Außerdem können Sie Daten aus der lokalen Datei auf Ihrem virtuellen Computer mit dem Azure Storage-Explorer hochladen:

![](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_upload_v2.png)


**Lesen von Daten aus dem Azure-Blobspeicher: AML-Readermodul**

In Azure Machine Learning Studio können Sie mit einem **Readermodul** Daten aus Ihrem Blobspeicher lesen.

![AML\_ReaderBlob\_Module\_v3](./media/machine-learning-data-science-vm-do-ten-things/AML_ReaderBlob_Module_v3.png)


**Lesen von Daten aus dem Azure-Blobspeicher: Python ODBC**

Sie können die **BlobService**-Bibliothek verwenden, um Daten direkt aus dem Blob in einem Jupyter Notebook- oder Pythonprogramm zu lesen.

Importieren Sie zunächst die erforderlichen Pakete:

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage import BlobService
	import tables
	import time
	import zipfile
	import random

Geben Sie dann Ihre Anmeldeinformationen für den Azure-Blobspeicher an, und lesen Sie Daten aus dem Blobspeicher:

    CONTAINERNAME = 'xxx'
	STORAGEACCOUNTNAME = 'xxxx'
	STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
	BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
	localfilename = 'trip_data_1.csv'
	LOCALDIRECTORY = os.getcwd()
	LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

	#download from blob
	t1 = time.time()
	blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
	t2 = time.time()
	print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

	#unzipping downloaded files if needed
	#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
	#    z.extractall(LOCALDIRECTORY)

	df1 = pd.read_csv(LOCALFILE, header=0)
	df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
	print 'the size of the data is: %d rows and  %d columns' % df1.shape
	
Die Daten werden als Datenrahmen eingelesen:

![IPNB\_data\_readin](./media/machine-learning-data-science-vm-do-ten-things/IPNB_data_readin.PNG)


### Azure Data Lake

Azure Data Lake-Speicher ist ein Repository mit Hyperskalierung für Big Data-Analyseworkloads. Der Azure Data Lake-Speicher ist kompatibel mit Hadoop Distributed File System (HDFS) und funktioniert sowohl mit dem Hadoop-Ökosystem als auch mit Azure Data Lake Analytics. Wir zeigen Ihnen, wie Sie Daten in den Azure Data Lake-Speicher verschieben und Analysen mithilfe von Azure Data Lake Analytics ausführen.

**Voraussetzung**

- Erstellen Sie Ihre Azure Data Lake Analytics im [Azure-Portal](http://portal.azure.com).

![Azure\_Data\_Lake\_Create\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_Create_v2.png)


- Die **Azure Data Lake-Tools** in **Visual Studio**, die Sie unter diesem [Link](https://www.microsoft.com/download/details.aspx?id=49504) finden, sind bereits in der Visual Studio Community Edition installiert, die sich auf dem virtuellen Computer befindet. Wenn Sie sich nach dem Start von Visual Studio bei Ihrem Azure-Abonnement angemeldet haben, werden Ihr Azure Data Analytics-Konto und Ihr Speicher im linken Bereich von Visual Studio angezeigt. 

![Azure\_Data\_Lake\_PlugIn\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)


**Verschieben von Daten vom virtuellen Computer in Data Lake: Azure Data Lake Explorer**

Sie können **Azure Data Lake Explorer** zum Hochladen von Daten aus den lokalen Dateien auf Ihrem virtuellen Computer in den Data Lake-Speicher verwenden.

![Azure\_Data\_Lake\_UploadData](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Sie können auch eine Datenpipeline erstellen, um Ihre Datenverschiebung zu oder von Azure Data Lake umzusetzen, indem Sie [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/) verwenden. In diesem [Artikel](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) finden Sie eine Anleitung zum Erstellen einer Datenpipeline.

**Lesen von Daten aus dem Azure-Blobspeicher in Data Lake: U-SQL**

Wenn Ihre Daten sich im Azure-Blobspeicher befinden, können Sie die Daten mit einer U-SQL-Abfrage direkt aus dem Azure-Blobspeicher lesen. Stellen Sie vor dem Formulieren der U-SQL-Abfrage sicher, dass Ihr Blobspeicherkonto mit Azure Data Lake verknüpft ist. Rufen Sie das **Azure-Portal** auf, klicken Sie auf Ihrem Azure Data Lake Analytics-Dashboard auf **Add Data Source**, wählen Sie als Speichertyp **Azure Storage** aus, und geben Sie Ihren Azure Storage-Kontonamen und -Schlüssel an. Anschließend können Sie auf die auf dem Speicherkonto gespeicherten Daten verweisen.

![](./media/machine-learning-data-science-vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)


In Visual Studio können Sie Daten aus dem Blobspeicher lesen, einige Datenmanipulationen ausführen, Features entwickeln und die resultierenden Daten entweder in Azure Data Lake oder im Azure-Blobspeicher ausgeben. Wenn Sie auf die Daten im Blobspeicher verweisen, verwenden Sie ****wasb://**; wenn Sie auf die Daten in Azure Data Lake verweisen, verwenden Sie ****swbhdfs://**.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Sie können die folgenden U-SQL-Abfragen in Visual Studio verwenden:

	@a =
	    EXTRACT medallion string,
	            hack_license string,
	            vendor_id string,
	            rate_code string,
	            store_and_fwd_flag string,
	            pickup_datetime string,
	            dropoff_datetime string,
	            passenger_count int,
	            trip_time_in_secs double,
	            trip_distance double,
	            pickup_longitude string,
	            pickup_latitude string,
	            dropoff_longitude string,
	            dropoff_latitude string
	
	    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
	    USING Extractors.Csv();
	
	@b = 
	    SELECT vendor_id, 
	    COUNT(medallion) AS cnt_medallion,
	    SUM(passenger_count) AS cnt_passenger,
	    AVG(trip_distance) AS avg_trip_dist,
	    MIN(trip_distance) AS min_trip_dist,
	    MAX(trip_distance) AS max_trip_dist,
	    AVG(trip_time_in_secs) AS avg_trip_time
	    FROM @a
	    GROUP BY vendor_id;
	
	OUTPUT @b   
	TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
	USING Outputters.Csv();
	
	OUTPUT @b   
	TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
	USING Outputters.Csv();
	


Nachdem Ihre Abfrage an den Server gesendet wurde, wird ein Diagramm mit dem Status des Auftrags angezeigt.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Job_Status.PNG)


**Datenabfragen in Data Lake: U-SQL**

Nachdem das DataSet in Azure Data Lake aufgenommen wurde, können Sie die [U-SQL-Sprache](../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) zum Abfragen und Durchsuchen der Daten verwenden. Die U-SQL-Sprache ähnelt T-SQL, kombiniert aber einige Funktionen von C#, sodass Benutzer angepasste Module, benutzerdefinierte Funktionen usw. schreiben können. Sie können die Skripts aus dem vorherigen Schritt verwenden.

Nachdem die Abfrage an den Server gesendet wurde, finden Sie bald „tripdata\_summary.CSV“ in **Azure Data Lake Explorer**. Zur Datenvorschau können Sie mit der rechten Maustaste auf die Datei klicken.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_create_summary.png)

So zeigen Sie die Dateiinformationen an:

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_tripdata_summary.png)


### HDInsight Hadoop-Cluster

Azure HDInsight ist ein verwalteter Apache Hadoop-, Spark-, HBase- und Storm-Dienst in der Cloud. Sie können problemlos mit Azure HDInsight-Clustern der Data Science Virtual Machine arbeiten.

**Voraussetzung**

- Erstellen Sie Ihr eigenes Azure-Blobspeicherkonto im [Azure-Portal](http://portal.azure.com). Dieses Speicherkonto wird zum Speichern von Daten für HDInsight-Cluster verwendet.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)

- Anpassen von Azure HDInsight Hadoop-Clustern vom [Azure-Portal](machine-learning-data-science-customize-hadoop-cluster.md) aus

  - Sie müssen das mit dem HDInsight-Cluster erstellte Speicherkonto verknüpfen, sobald es erstellt ist. Mit diesem Speicherkonto wird auf Daten zugegriffen, die innerhalb des Clusters verarbeitet werden können.
	
![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_v4.PNG)

  - Sie müssen nach dem Erstellen den **Remotezugriff** auf den Hauptknoten des Clusters aktivieren. Merken Sie sich die hier angegebenen RAS-Anmeldeinformationen (die sich von denen beim Erstellen des Clusters unterscheiden). Sie werden diese später benötigen.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

  - Erstellen eines Azure ML-Arbeitsbereichs Ihre Machine Learning-Experimente werden in diesem ML-Arbeitsbereich gespeichert. Wählen Sie die hervorgehobenen Optionen im Portal aus, wie im Screenshot unten dargestellt. 

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space.PNG)


  - Geben Sie dann die Parameter für Ihren Azure ML-Arbeitsbereich ein.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

  - Hochladen von Daten mit IPython Notebook. Importieren Sie zunächst die erforderlichen Pakete, geben Sie Ihre Anmeldeinformationen ein, erstellen Sie eine Datenbank in Ihrem Speicherkonto, und laden Sie dann Daten in die HDI-Cluster hoch. 


		#Import required Packages
		import pyodbc
		import time as time
		import json
		import os
		import urllib
		import urllib2
		import warnings
		import re
		import pandas as pd
		import matplotlib.pyplot as plt
		from azure.storage.blob import BlobService
		warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


		#Create the connection to Hive using ODBC
		SERVER_NAME='xxx.azurehdinsight.net'
		DATABASE_NAME='nyctaxidb'
		USERID='xxx'
		PASSWORD='xxxx'
		DB_DRIVER='Microsoft Hive ODBC Driver'
		driver = 'DRIVER={' + DB_DRIVER + '}'
		server = 'Host=' + SERVER_NAME + ';Port=443'
		database = 'Schema=' + DATABASE_NAME
		hiveserv = 'HiveServerType=2'
		auth = 'AuthMech=6'
		uid = 'UID=' + USERID 
		pwd = 'PWD=' + PASSWORD
		CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
		connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
		cursor=connection.cursor()


		#Create Hive database and tables
		queryString = "create database if not exists nyctaxidb;"
		cursor.execute(queryString)
		
		queryString = """
		                create external table if not exists nyctaxidb.trip
		                ( 
		                    medallion string, 
		                    hack_license string,
		                    vendor_id string, 
		                    rate_code string, 
		                    store_and_fwd_flag string, 
		                    pickup_datetime string, 
		                    dropoff_datetime string, 
		                    passenger_count int, 
		                    trip_time_in_secs double, 
		                    trip_distance double, 
		                    pickup_longitude double, 
		                    pickup_latitude double, 
		                    dropoff_longitude double, 
		                    dropoff_latitude double)  
		                PARTITIONED BY (month int) 
		                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
		                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
		            """
		cursor.execute(queryString)
		
		queryString = """
		                create external table if not exists nyctaxidb.fare 
		                ( 
		                    medallion string, 
		                    hack_license string, 
		                    vendor_id string, 
		                    pickup_datetime string, 
		                    payment_type string, 
		                    fare_amount double, 
		                    surcharge double,
		                    mta_tax double,
		                    tip_amount double,
		                    tolls_amount double,
		                    total_amount double)
		                PARTITIONED BY (month int) 
		                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
		                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
		            """
		cursor.execute(queryString)
	
	
		#Upload data from blob storage to HDI cluster
		for i in range(1,13):
		    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
		    cursor.execute(queryString)
		    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
		    cursor.execute(queryString)


- Oder Sie folgen dieser [exemplarischen Vorgehensweise](machine-learning-data-science-process-hive-walkthrough.md) zum Hochladen von NYC-Taxi-Daten in den HDI-Cluster. Zu den wichtigsten Schritten zählen:

	- AzCopy: Herunterladen gezippter CSV-Dateien aus dem öffentlichen Blobspeicher in Ihren lokalen Ordner
	- AzCopy: Hochladen entzippter CSV-Dateien aus dem lokalen Ordner in den HDI-Cluster
	- Anmelden beim Hauptknoten des Hadoop-Clusters und Vorbereitung einer explorativen Datenanalyse

Nachdem die Daten in den HDI-Cluster geladen wurden, können Sie Ihre Daten im Azure Storage-Explorer überprüfen. Und Sie besitzen eine im HDI-Cluster erstellte Datenbank „nyctaxidb“.

![](./media/machine-learning-data-science-vm-do-ten-things/Upload_Data_to_HDI_cluster_Azure_Explorer.PNG)


**Durchsuchen von Daten: Hive-Abfragen in Python**

Da sich die Daten im Hadoop-Cluster befinden, können Sie das Paket „pyodbc“ verwenden, um Verbindungen mit Hadoop-Clustern herzustellen und die Datenbank mit Hive abzufragen, um Durchsuchungsvorgänge und Funktionsverarbeitungen durchzuführen. Sie können die vorhandenen Tabellen anzeigen, die wir im erforderlichen Schritt erstellt haben.

	queryString = """
	    show tables in nyctaxidb2;
	    """
	pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Betrachten wir nun die Anzahl der Datensätze in jedem Monat, und wie häufig Trinkgeld gegeben wurde, in der Fahrttabelle:

	queryString = """
	    select month, count(*) from nyctaxidb.trip group by month;
	    """
	results = pd.read_sql(queryString,connection)
	
	%matplotlib inline
	
	results.columns = ['month', 'trip_count']
	df = results.copy()
	df.index = df['month']
	df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)


	queryString = """
	    SELECT tipped, COUNT(*) AS tip_freq 
	    FROM 
	    (
	        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
	        FROM nyctaxidb.fare
	    )tc
	    GROUP BY tipped;
	    """
	results = pd.read_sql(queryString,connection)
	
	results.columns = ['tipped', 'trip_count']
	df = results.copy()
	df.index = df['tipped']
	df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Außerdem können wir die Entfernung zwischen dem Aufnahmestandort und dem Absetzstandort berechnen und sie dann mit der Fahrtstrecke vergleichen.

	queryString = """
	                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
	                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
	                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
	                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
	                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
	                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
	                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance 
	                    from nyctaxidb.trip 
	                    where month=1 
	                        and pickup_longitude between -90 and -30
	                        and pickup_latitude between 30 and 90
	                        and dropoff_longitude between -90 and -30
	                        and dropoff_latitude between 30 and 90;
	            """
	results = pd.read_sql(queryString,connection)
	results.head(5)


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

	results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude', 
	                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
	df = results.loc[results['trip_distance']<=100] #remove outliers
	df = df.loc[df['direct_distance']<=100] #remove outliers
	plt.scatter(df['direct_distance'], df['trip_distance'])


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)


Nun bereiten wir komprimierte Daten (1 %) für die Modellierung vor. Wir können diese Daten im AML-Readermodul verwenden.


		queryString = """
	    create  table if not exists nyctaxi_downsampled_dataset_testNEW (
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    pickup_hour string,
	    pickup_week string,
	    weekday string,
	    passenger_count int,
	    trip_time_in_secs double,
	    trip_distance double,
	    pickup_longitude double,
	    pickup_latitude double,
	    dropoff_longitude double,
	    dropoff_latitude double,
	    direct_distance double,
	    payment_type string,
	    fare_amount double,
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double,
	    tipped string,
	    tip_class string
	    )
	    row format delimited fields terminated by ','
	    lines terminated by '\\n'
	    stored as textfile;
		"""
		cursor.execute(queryString)

		--- now insert contents of the join into the above internal table

		queryString = """
	    insert overwrite table nyctaxi_downsampled_dataset_testNEW
	    select
	    t.medallion,
	    t.hack_license,
	    t.vendor_id,
	    t.rate_code,
	    t.store_and_fwd_flag,
	    t.pickup_datetime,
	    t.dropoff_datetime,
	    hour(t.pickup_datetime) as pickup_hour,
	    weekofyear(t.pickup_datetime) as pickup_week,
	    from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
	    t.passenger_count,
	    t.trip_time_in_secs,
	    t.trip_distance,
	    t.pickup_longitude,
	    t.pickup_latitude,
	    t.dropoff_longitude,
	    t.dropoff_latitude,
	    t.direct_distance,
	    f.payment_type,
	    f.fare_amount,
	    f.surcharge,
	    f.mta_tax,
	    f.tip_amount,
	    f.tolls_amount,
	    f.total_amount,
	    if(tip_amount>0,1,0) as tipped,
	    if(tip_amount=0,0,
	    if(tip_amount>0 and tip_amount<=5,1,
	    if(tip_amount>5 and tip_amount<=10,2,
	    if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
	    from
	    (
	    select
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    pickup_datetime,
	    dropoff_datetime,
	    passenger_count,
	    trip_time_in_secs,
	    trip_distance,
	    pickup_longitude,
	    pickup_latitude,
	    dropoff_longitude,
	    dropoff_latitude,
	    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
	    radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
	    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
	    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
	    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
	    rand() as sample_key
	
	    from trip
	    where pickup_latitude between 30 and 90
	        and pickup_longitude between -90 and -30
	        and dropoff_latitude between 30 and 90
	        and dropoff_longitude between -90 and -30
	    )t
	    join
	    (
	    select
	    medallion,
	    hack_license,
	    vendor_id,
	    pickup_datetime,
	    payment_type,
	    fare_amount,
	    surcharge,
	    mta_tax,
	    tip_amount,
	    tolls_amount,
	    total_amount
	    from fare
	    )f
	    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
	    where t.sample_key<=0.01
		"""
		cursor.execute(queryString)

Nach einer Weile können Sie sehen, dass die Daten in Hadoop-Cluster geladen wurden:
		
	queryString = """
	    select * from nyctaxi_downsampled_dataset limit 10;
	    """
	cursor.execute(queryString)
	pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)


**Lesen von Daten aus HDI mit AML: Readermodul**

Sie können auch das **Readermodul** in AML-Studio für den Datenbankzugriff in Hadoop-Clustern verwenden. Geben Sie die Anmeldeinformationen für Ihre HDI-Cluster und Ihr Azure Storage-Konto an, und Sie können die Datenbank in HDI-Clustern verwenden, um Machine Learning-Modelle zu erstellen.

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Reader_Hive.PNG)

Das bewertete Dataset kann dann angezeigt werden:

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Model_Results.PNG)


### Azure SQL Data Warehouse und Datenbanken

Azure SQL Data Warehouse ist ein elastisches Data Warehouse-as-a-Service-Angebot mit SQL Server-Funktionalität auf Unternehmensniveau.

Sie können Ihr Azure SQL Data Warehouse bereitstellen, indem Sie die Anweisungen in diesem [Artikel](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) befolgen. Nachdem Sie Ihr Azure SQL Data Warehouse bereitgestellt haben, können Sie diese [exemplarischen Vorgehensweise](machine-learning-data-science-process-sqldw-walkthrough.md) verwenden, um mit Daten in SQL Data Warehouse Daten hochzuladen, zu durchsuchen und zu modellieren.

#### Azure DocumentDB

Azure DocumentDB ist eine NoSQL-Datenbank in der Cloud. Sie ermöglicht Ihnen, mit Dokumenten wie JSON zu arbeiten und die Dokumente zu speichern und abzufragen.

Sie müssen die folgenden erforderlichen Schritte ausführen, um über die DSVM auf DocumentDB zuzugreifen.

1. Installieren Sie das DocumentDB Python SDK (führen Sie ```pip install pydocumentdb``` in der Befehlszeile aus).
1. Erstellen Sie DocumentDB-Konto und DocumentDB-Datenbank im [Azure-Portal](https://portal.azure.com).
1. Laden Sie das DocumentDB-Migrationstool [hier](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) herunter, und extrahieren Sie es in ein Verzeichnis Ihrer Wahl.
1. Importieren Sie JSON-Daten (Vulkan-Daten), die in einem [öffentlichen Blobspeicher](https://cahandson.blob.core.windows.net/samples/volcano.json) gespeichert sind, mit den folgenden Befehlsparametern des Migrationstools („dtui.exe“ aus dem Verzeichnis, in dem Sie das DocumentDB-Migrationstool installiert haben) in DocumentDB. Geben Sie die unten stehenden Quell-und Zielpositionsparameter ein. 

	/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

Sobald Sie die Daten importiert haben, können Sie in Jupyter das Notebook *DocumentDBSample* öffnen, das den Python-Code für den Zugriff auf DocumentDB und zur Ausführung einiger grundlegender Abfragen enthält. Weitere Informationen zu DocumentDB finden Sie auf der [Dokumentationsseite](https://azure.microsoft.com/documentation/learning-paths/documentdb/).


## 8\. Erstellen von Berichten und Dashboard mit Power BI Desktop 

Nun visualisieren wir die Volcano JSON-Datei aus dem obigen DocumentDB-Beispiel in Power BI, um visuelle Einblicke in die Daten zu erhalten. Eine ausführliche Anleitung finden Sie im [Power BI-Artikel](../documentdb/documentdb-powerbi-visualize.md). Die allgemeinen Schritte sind:

1. Öffnen Sie Power BI Desktop, und führen Sie „Get Data“ aus. Legen Sie folgenden URL-Namen fest: https://cahandson.blob.core.windows.net/samples/volcano.json.
2. Sie sollten die JSON-Datensätze in einer importierten Liste sehen.
3. Konvertieren Sie die Liste in eine Tabelle, damit PowerBI mit dem Inhalt arbeiten kann.
4. Erweitern Sie die Spalten durch Klicken auf das Erweiterungssymbol (das Symbol „Pfeil nach links und Pfeil nach rechts“ auf der rechten Seite der Spalte).
5. Beachten Sie, dass bei „Location“ das Feld „Record“ steht. Erweitern Sie den Datensatz und wählen Sie nur „coordinates“. „coordinates“ ist eine Listenspalte.
6. Fügen Sie eine neue Spalte hinzu, um die Listenspalte „coordinates“ in eine kommagetrennte Spalte „LatLong“ zu konvertieren, wobei die beiden Elemente im Listenfeld „coordinates“ mithilfe der Formel ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` verkettet werden. 
7. Konvertieren Sie schließlich die Spalte ```Elevation``` zu „Decimal“ und klicken Sie auf „**Close** and **Apply**“.

Anstelle der oben beschriebenen Schritte können Sie den folgenden Code, in dem die obigen Schritte ausformuliert sind, in den Erweiterten Editor in PowerBI einfügen, der Ihnen ermöglicht, die Datentransformationen in einer Abfragesprache zu schreiben.


	let
	    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
	    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
	    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
	    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
	    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
	    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
	in
	    #"Changed Type"
		


Jetzt befinden sich die Daten in Ihrem Power BI-Datenmodell. Ihr Power BI-Desktop sollte nun wie unten dargestellt aussehen.

![](./media/machine-learning-data-science-vm-do-ten-things/PowerBIVolcanoData.png)

Sie können beginnen, Berichte und Visualisierungen mit dem Datenmodell zu erstellen. Sie können die in diesem [Power BI-Artikel](../documentdb/documentdb-powerbi-visualize.md#build-the-reports) beschriebenen Schritte zum Erstellen eines Berichts verwenden. Das Endergebnis ist ein Bericht, der wie folgt aussieht.

![Power BI Desktop-Berichtsansicht – Power BI-Connector](./media/machine-learning-data-science-vm-do-ten-things/power_bi_connector_pbireportview2.png)

## 9\. Dynamische Skalierung Ihrer DSVM gemäß Ihren Projektanforderungen

Sie können die DSVM gemäß Ihren Projektanforderungen herauf- oder herabskalieren. Wenn Sie die VM abends oder am Wochenende nicht benötigen, können Sie sie einfach vom [Azure-Portal](https://portal.azure.com) aus herunterfahren.

>[AZURE.NOTE]  Es fallen Computegebühren an, wenn Sie nur die Betriebssystemschaltfläche zum Herunterfahren auf der VM verwenden.

Wenn Sie einige umfangreiche Analysen abwickeln müssen und mehr CPU- und/oder Arbeitsspeicher- und/oder Datenträgerkapazität benötigen, finden Sie eine große Auswahl an VM-Größen in Bezug auf CPU-Kerne, Speicherkapazität und Datenträgertypen (einschließlich Solid-State-Laufwerke), die Ihren Compute- und Budgetanforderungen entsprechen. Die vollständige Liste der VMs zusammen mit ihren stündlichen Computepreisen finden Sie auf der Seite [Azure Virtual Machines – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/).

Auch wenn Ihr Bedarf an VM-Verarbeitungskapazität sinkt (zum Beispiel: Sie haben erhebliche Arbeitsauslastung auf einen Hadoop- oder Spark-Cluster verschoben), können Sie den Cluster vom [Azure-Portal](https://portal.azure.com) aus in den Einstellungen Ihrer VM-Instanz herunterskalieren. Hier sehen Sie einen Screenshot.


![](./media/machine-learning-data-science-vm-do-ten-things/VMScaling.PNG)


## 10\. Installieren zusätzlicher Tools auf Ihrem virtuellen Computer

Wir haben mehrere Tools zu einem Paket zusammengefasst, die unserer Ansicht nach vielen der allgemeinen Datenanalyseanforderungen gerecht werden und Ihnen Zeit sparen sollten, da sie vermeiden, dass Sie Ihre Umgebungen einzeln installieren und konfigurieren müssen. Außerdem sparen Sie Geld, da Sie nur für die Ressourcen bezahlen, die Sie auch wirklich verwenden.

Sie können andere in diesem Artikel dargestellte Azure-Datendienste und -Analysedienste nutzen, um Ihre Analyseumgebung zu verbessern. Wir wissen, dass in manchen Fällen für Ihre Bedürfnisse zusätzliche Tools, einschließlich geschützter Drittanbietertools, erforderlich sein können. Sie haben vollen administrativen Zugriff auf den virtuellen Computer, um neue Tools zu installieren, die Sie benötigen. Sie können auch zusätzliche Pakete in Python und R installieren, die nicht vorinstalliert sind. Für Python können Sie entweder ```conda``` oder ```pip``` verwenden. Für R können Sie die ```install.packages()``` in der R-Konsole verwenden, oder Sie verwenden die IDE und wählen „**Packages** -> **Install Packages...**“ aus.

## Zusammenfassung
Dies sind nur einige Dinge, die mit der Microsoft Data Science Virtual Machine möglich sind. Sie können viele weitere Dinge tun, um sie als wirksame Analyseumgebung zu nutzen.

<!---HONumber=AcomDC_0302_2016-->