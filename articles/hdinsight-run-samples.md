<properties
	pageTitle="Ausführen der Hadoop-Beispiele in HDInsight | Azure"
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

Wir haben verschiedene Beispiele bereitgestellt, um Ihnen die ersten Schritte beim Ausführen von MapReduce-Aufträgen auf Hadoop-Clustern in Azure HDInsight zu erleichtern. Diese Beispiele sind in allen von HDInsight verwalteten Clustern verfügbar, die Sie erstellen. Führen Sie diese Beispiele aus, um sich mit den Azure PowerShell-Cmdlets zum Ausführen von Aufträgen in Hadoop-Clustern vertraut zu machen.

MapReduce-Programme können außerdem programmgesteuert aus einer Anwendung heraus mit der Microsoft .NET-API für HDInsight ausgeführt werden. Weitere Informationen zur Verwendung der HDInsight-APIs für die Auftragsübermittlung finden Sie unter [Übermitteln von Hadoop-Aufträgen in HDInsight][hdinsight-submit-hadoop-jobs-programmatically].

Im Internet finden Sie umfangreiche Dokumentationen zu Hadoop-verwandten Technologien, wie z. B. javabasierte MapReduce-Programmierung und Streaming, sowie Dokumentationen zu den in Windows PowerShell-Skripts verwendeten Cmdlets. Weitere Informationen zu diesen Ressourcen finden Sie im abschließenden Abschnitt **Ressourcen für HDInsight** des Artikels [Einführung in Azure HDInsight][hdinsight-introduction].

**Beschreibung der Beispiele**

<p>Diese Beispiele bieten einen schnellen Einstieg in die Bereitstellung von Hadoop-Aufträgen und eine umfangreiche Testumgebung zum Arbeiten mit den Konzepten und Skriptprozeduren des Diensts. Dort finden Sie Beispiele für gängige Aufgaben, z.&#160;B. Erstellen und Importieren von DataSets verschiedener Größen, Ausführen von Aufträgen und sequenzielles Zusammenstellen von Aufträgen sowie die Untersuchen der Ergebnisse Ihrer Aufträge. Die jeweiligen DataSets haben unterschiedliche Größen, sodass Sie die Leistungsunterschiede der Aufträge in Abhängigkeit von der Größe der DataSets beobachten können.</p>


**Voraussetzungen**:

- Sie benötigen ein Microsoft Azure-Konto. Hinweise zum Erstellen eines Kontos finden Sie auf der Seite [Einen Monat kostenlos testen](http://azure.microsoft.com/pricing/free-trial/).

- Sie benötigen ein bereitgestelltes HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight mit benutzerdefinierten Optionen](hdinsight-provision-clusters.md).

- Sie müssen Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen zur entsprechenden Vorgehensweise finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

## Die Beispiele ##

HDInsight wird mit den folgenden Beispielen ausgeliefert:

- [**Pi-Schätzungs-Hadoop-Beispiel**][hdinsight-sample-pi-estimator]: beschreibt die Ausführung eines MapReduce-Programms mit HDInsight, das den Wert von Pi mithilfe einer statistischen Methode (Quasi-Monte-Carlo-Verfahren) ermittelt.
- [**Ausführen eines MapReduce-Wortzählungs-Beispiels in einem Hadoop-Cluster**][hdinsight-sample-wordcount]: beschreibt die Verwendung eines HDInsight-Clusters für die Ausführung eines MapReduce-Programms, das Worthäufigkeiten in einer Textdatei zählt.
- [**10-GB-Graysort-Hadoop-Beispiel**][hdinsight-sample-10gb-graysort]: beschreibt die Ausführung eines allgemeinen Graysorts auf eine 10-GB-Datei mithilfe von HDInsight. Es müssen drei Aufträge ausgeführt werden: TeraGen generiert die Daten, TeraSort sortiert die Daten und TeraValidate prüft, ob die Daten korrekt sortiert wurden.
- [**C#-Streaming-Wortzählungs-MapReduce-Beispiel in Hadoop**][hdinsight-sample-csharp-streaming]: zeigt, wie Sie in C# ein MapReduce-Programm schreiben können, das die Hadoop-Streamingschnittstelle verwendet.


## Ausführen der Beispiele ##

Sie können diese Beispiele mit Azure PowerShell ausführen. Es sind Anweisungen für die einzelnen Beispiele verfügbar.

##Nächste Schritte ##

In diesem Artikel und den Artikeln zu den einzelnen Beispielen haben Sie gelernt, wie Sie die in HDInsight-Clustern enthaltenen Beispiele mit Azure PowerShell ausführen können. Lernprogramme zu Pig, Hive und MapReduce mit HDInsight finden Sie in den folgenden Artikeln:

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


[powershell-install-configure]: install-configure-powershell.md

[hdinsight-get-started]: hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!--HONumber=54-->