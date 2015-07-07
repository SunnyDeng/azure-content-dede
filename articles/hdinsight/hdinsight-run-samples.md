<properties
	pageTitle="Ausführen der Hadoop-Beispiele in HDInsight | Microsoft Azure"
	description="Erste Schritte mit dem Azure HDInsight-Dienst mit den mitgelieferten Beispielen. Verwenden von PowerShell-Skripts, die MapReduce-Programme auf Datenclustern ausführen."
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2015" 
	ms.author="bradsev"/>




#Ausführen der Hadoop-Beispiele in HDInsight

Wir haben verschiedene Beispiele bereitgestellt, um Ihnen die ersten Schritte beim Ausführen von MapReduce-Aufträgen auf Hadoop-Clustern in Azure HDInsight zu erleichtern. Diese Beispiele sind in allen von HDInsight verwalteten Clustern verfügbar, die Sie erstellen. Führen Sie diese Beispiele aus, um sich mit der Verwendung der Azure PowerShell-Cmdlets für die Ausführung von Aufträgen in Hadoop-Clustern vertraut zu machen.

MapReduce-Programme können außerdem programmgesteuert aus einer Anwendung heraus mit der Microsoft .NET-API für HDInsight ausgeführt werden. Weitere Informationen zur Verwendung der HDInsight-APIs für die Auftragsübermittlung finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][hdinsight-submit-jobs].

Im Internet finden Sie umfangreiche Dokumentationen zu Hadoop-verwandten Technologien, wie z. B. javabasierte MapReduce-Programmierung und Streaming, sowie Dokumentationen zu Cmdlets, die in Windwos PowerShell-Skripts verwendet werden. Weitere Informationen zu diesen Ressourcen finden Sie im abschließenden Abschnitt **Ressourcen für HDInsight** des Artikels [Einführung in Azure HDInsight][hdinsight-introduction].

**Beschreibung der Beispiele**

<p>Diese Beispiele bieten einen schnellen Einstieg in die Bereitstellung von Hadoop-Aufträgen und stellen eine umfangreiche Testumgebung zum Arbeiten mit den vom Dienst verwendeten Konzepten und Skriptverfahren bereit. Sie finden dort Beispiele für gängige Aufgaben, wie z. B. Erstellung und Import von Datasets verschiedener Größen, Ausführen von Aufträgen und sequenzielles Zusammenstellen von Aufträgen sowie die Untersuchung der Ergebnisse von Aufträgen. Die jeweiligen Datasets haben unterschiedliche Größen, sodass Sie beobachten können, wie sich die Größe der Datasets auf die Leistung der Aufträge auswirkt.</p>


**Voraussetzungen**:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Einen HDInsight-Cluster**. Anweisungen zu den verschiedenen Optionen beim Erstellen eines solchen Clusters finden Sie unter [Bereitstellung eines HDInsight-Clusters](hdinsight-provision-clusters.md).
- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## Die Beispiele ##

HDInsight wird mit den folgenden Beispielen ausgeliefert:

- [**Das Pi-Schätzungsbeispiel mit Hadoop**][hdinsight-sample-pi-estimator]: Zeigt die Ausführung eines MapReduce-Programms mit HDInsight, das den Wert von Pi mithilfe einer statistischen (Quasi-Monte-Carlo-) Methode ermittelt.
- [**Ausführen eines Hadoop MapReduce-Wortzählungsbeispiel in HDInsight**][hdinsight-sample-wordcount]: Beschreibt die Verwendung eines HDInsight-Clusters für die Ausführung eines MapReduce-Programms, das Worthäufigkeiten in einer Textdatei zählt.
- [**Das 10-GB-Graysort-Beispiel**][hdinsight-sample-10gb-graysort]: Zeigt die Ausführung eines allgemeinen GraySort auf einer 10 GB-Datei mithilfe von HDInsight. Drei Aufträge müssen ausgeführt werden: Teragen zum Generieren der Daten, Terasort zum Sortieren der Daten und Teravalidate zur Überprüfung der richtigen Sortierung der Daten.
- [**Das C#-Streamingbeispiel zur MapReduce-Wortzählung in Hadoop**][hdinsight-sample-csharp-streaming]: Dieses Lernprogramm zeigt, wie Sie in C# ein MapReduce-Programm schreiben können, das die Hadoop-Streamingschnittstelle verwendet.


## Ausführen der Beispiele ##

Sie können diese Beispiele mithilfe von Azure PowerShell ausführen. Anweisungen zum Vorgehen finden Sie jeweils in den oben genannten Beispielen.

##Nächste Schritte ##

In diesem Artikel und den Artikeln zu den einzelnen Beispielen haben Sie gelernt, wie Sie die in HDInsight-Clustern enthaltenen Beispiele mithilfe von Azure PowerShell ausführen können. Lernprogramme zu Pig, Hive und MapReduce mit HDInsight finden Sie in den folgenden Themen:

* [Erste Schritte bei der Nutzung von Hadoop mit Hive in HDInsight zur Analyse der Verwendung von Mobiltelefonen][hdinsight-get-started]
* [Verwenden von Pig mit Hadoop in HDInsight][hdinsight-use-pig]
* [Verwenden von Hive mit Hadoop in HDInsight][hdinsight-use-hive]
* [Übermitteln von Hadoop-Aufträgen in HDInsight][hdinsight-submit-jobs]
* [Azure HDInsight SDK-Dokumentation][hdinsight-sdk-documentation]
* [Debuggen von Hadoop in HDInsight: Fehlermeldungen][hdinsight-errors]


[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
 

<!---HONumber=62-->