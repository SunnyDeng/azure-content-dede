<properties 
	pageTitle="Anpassen von Hadoop-Clustern für erweiterte Analyseprozesse und -technologien | Microsoft Azure" 
	description="Beliebte Python-Module, die in benutzerdefinierten Azure HDInsight Hadoop-Clustern zur Verfügung gestellt werden."
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="hangzh;bradsev" />

# Anpassen von Azure Hdinsight Hadoop-Clustern für erweiterte Analyseprozesse und -technologien

In diesem Artikel wird beschrieben, wie HDInsight Hadoop-Cluster durch die Installation von 64-Bit-Anaconda (Python 2.7) auf jedem Knoten angepasst werden, wenn sie im HDInsight-Dienst bereitgestellt werden. Diese Anpassung bereitet das Cluster für die Verwendung mit erweiterten Analyseprozessen und -technologien (ADAPT) vor, die für Azure Machine Learning genutzt werden. Es wird auch gezeigt, wie Sie auf den Hauptknoten zugreifen, um benutzerdefinierte Aufträge an den Cluster zu übermitteln.

Diese Anpassung macht viele beliebte Python-Module verfügbar, die Bestandteil von Anaconda sind. Dies geschieht sehr benutzerfreundlich in UDFs (User Defined Function, benutzerdefinierte Funktion), die entwickelt wurden, um Hive-Datensätze im Cluster zu verarbeiten. Eine Anleitung für in diesem Szenario verwendeten Prozeduren finden Sie unter [Übermitteln von Hive-Abfragen an HDInsight Hadoop-Cluster im erweiterten Analyseprozess](machine-learning-data-science-hive-queries.md).


## <a name="customize"></a>Anpassen von Azure HDInsight Hadoop-Clustern

Zum Erstellen eines angepassten HDInsight Hadoop-Clusters müssen Sie sich beim [**Azure-Verwaltungsportal**](https://manage.windowsazure.com/) anmelden. Klicken Sie links unten auf **New** und wählen Sie dann DATA SERVICES -> HDINSIGHT -> **CUSTOM CREATE** aus, um das Fenster **Cluster Details** zu öffnen.

![Arbeitsbereich erstellen][1]

Geben Sie den Namen des zu erstellenden Clusters auf der 1. Konfigurationsseite ein, und übernehmen Sie die Standardwerte für die anderen Felder. Klicken Sie auf den Pfeil, um zur nächsten Konfigurationsseite zu wechseln.

![Arbeitsbereich erstellen][2]

Geben Sie auf der 2. Konfigurationsseite die Anzahl der **DATA NODES** ein, wählen Sie eine Option für **REGION/VIRTUAL NETWORK** aus und wählen Sie dann die Größe für **HEAD NODE** und **DATA NODE** aus. Klicken Sie auf den Pfeil, um zur nächsten Konfigurationsseite zu wechseln.

>[AZURE.NOTE]Der Wert für **REGION/VIRTUAL NETWORK** muss mit der Region des Speicherkontos identisch sein, das für den HDInsight Hadoop-Cluster verwendet werden soll. Andernfalls wird auf der vierten Konfigurationsseite nicht das gewünschte Speicherkonto in der Dropdownliste **ACCOUNT NAME** angezeigt.

![Arbeitsbereich erstellen][3]

Geben Sie auf der 3. Konfigurationsseite einen Benutzernamen und ein Kennwort für den HDInsight Hadoop-Cluster ein. **Wählen Sie nicht** _Enter the Hive/Oozie Metastore_ aus. Klicken Sie anschließend auf den Pfeil, um zur nächsten Konfigurationsseite zu wechseln.

![Arbeitsbereich erstellen][4]

Geben Sie auf der 4. Konfigurationsseite den Speicherkontonamen und den Standardcontainer des HDInsight Hadoop-Clusters an. Wenn Sie in der Dropdownliste **DEFAULT CONTAINER** _Create default container_ auswählen, wird ein Container mit demselben Namen wie der Cluster erstellt. Klicken Sie auf den Pfeil, um zur letzten Konfigurationsseite zu wechseln.

![Arbeitsbereich erstellen][5]

Klicken Sie auf der letzten Konfigurationsseite zu **Script Actions** auf die Schaltfläche** add script action** und füllen Sie die Felder mit den folgenden Werten aus.
 
* **NAME** - eine beliebige Zeichenfolge als Name für diese Skriptaktion. 
* **NODE TYPE** - wählen Sie **All nodes** aus. 
* **SCRIPT URI** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1*	* *publicscripts* ist ein öffentlicher Container im Speicherkonto. 
	* *getgoing* wird für die Freigabe der PowerShell-Skriptdateien verwendet, um den Benutzern die Arbeit in Azure zu vereinfachen. 
* **PARAMETERS** - (leer lassen)

Klicken Sie abschließend auf das Häkchen, um die Erstellung des benutzerdefinierten HDInsight Hadoop-Clusters zu starten.

![Arbeitsbereich erstellen][6]

## <a name="headnode"></a>Zugreifen auf den Hauptknoten des Hadoop-Clusters

Sie müssen den Remotezugriff auf den Hadoop-Cluster in Azure aktivieren, damit Benutzer über RDP auf den Hauptknoten des Hadoop-Clusters zugreifen können.

1. Melden Sie sich beim [**Azure-Verwaltungsportal**](https://manage.windowsazure.com/) an, und wählen Sie links **HDInsight** aus. Wählen Sie Ihren Hadoop-Cluster aus der Liste der Cluster aus, klicken Sie auf die Registerkarte **CONFIGURATION**, und klicken Sie dann unten auf der Seite auf das **ENABLE REMOTE**.
	
	![Arbeitsbereich erstellen][7]

2. Geben Sie im Fenster **Configure Remote Desktop** den BENUTZERNAMEN und das KENNWORT ein, und wählen Sie das Ablaufdatum für den Remotezugriff aus. Klicken Sie anschließend auf das Häkchen, um den Remotezugriff auf den Hauptknoten des Hadoop-Clusters zu aktivieren.
	
	>[AZURE.NOTE]
	>
	>1. Der Benutzername und das Kennwort für den Remotezugriff sind nicht der Benutzername und das Kennwort, die Sie zum Erstellen des Hadoop-Clusters verwendet haben. Hierbei handelt es sich um andere Anmeldeinformationen.
	>
	>2. Das Ablaufdatum für den Remotezugriff muss sich innerhalb der nächsten 7 Tage ab dem aktuellen Datum befinden.

	![Arbeitsbereich erstellen][8]

3. Klicken Sie nach dem Aktivieren des Remotezugriffs unten auf der Seite auf **CONNECT**, um remote auf den Hauptknoten zuzugreifen. So melden Sie sich auf dem Hauptknoten des Hadoop-Clusters durch Eingabe der Anmeldeinformationen für den RAS-Benutzer an, die Sie zuvor angegeben haben.

	 ![Arbeitsbereich erstellen][9]

Die nächsten Schritte des Datenanalyseprozesses sind unter [Leitfaden: Erweiterte Datenverarbeitung in Azure](machine-learning-data-science-advanced-data-processing.md) aufgeführt. Dazu gehören u.a. auch das Verschieben von Daten nach HDInsight und das dortige Verarbeiten und Extrahieren dieser Daten für den Erhalt von Informationen anhand der Daten mit Azure Machine Learning.

Unter [Übermitteln von Hive-Abfragen an HDInsight Hadoop-Cluster](machine-learning-data-science-process-hive-tables.md)finden Sie Anweisungen zum Zugreifen auf die Python-Module in Anaconda vom Hauptknoten des Clusters aus. Dazu werden UDFs (User Defined Function, benutzerdefinierte Funktion) verwendet, um Hive-Datensätze, die im Cluster gespeichert sind, zu verarbeiten.

[1]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png
[2]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img2.png
[3]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png
[4]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png
[5]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png
[6]: ./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png
[7]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png
[8]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png
[9]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png

 

<!---HONumber=July15_HO4-->