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
	ms.date="11/10/2014" 
	ms.author="bradsev"/>




# Ausführen der Hadoop-Beispiele in HDInsight

Wir haben verschiedene Beispiele bereitgestellt, um Ihnen die ersten Schritte beim Ausführen von MapReduce-Aufträgen auf Hadoop-Clustern in Azure HDInsight zu erleichtern. Diese Beispiele sind in allen von HDInsight verwalteten Clustern verfügbar, die Sie erstellen. Führen Sie diese Beispiele aus, um sich mit den Azure PowerShell HDInsight-Cmdlets für die Ausführung von Aufträgen in Hadoop-Clustern vertraut zu machen.

MapReduce-Programme können außerdem programmgesteuert aus einer Anwendung heraus mit der Microsoft .NET API für HDInsight ausgeführt werden. Weitere Informationen zur Verwendung der HDInsight-APIs für die Auftragsübermittlung finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][hdinsight-submit-jobs].

Im Internet finden Sie umfangreiche Dokumentationen zu Hadoop-verwandten Technologien wie z. B. javabasierte MapReduce-Programmierung und Streaming sowie Dokumentationen zu Cmdlets in PowerShell-Skripts. Weitere Informationen zu diesen Ressourcen finden Sie im abschließenden Abschnitt **Ressourcen für HDInsight** des Artikels [Einführung in Azure HDInsight][hdinsight-introduction].

**Beschreibung der Beispiele**

<p>Diese Beispiele bieten einen schnellen Einstieg in die Bereitstellung von Hadoop-Aufträgen und bieten eine umfangreiche Testumgebung zum Arbeiten mit den Konzepten und Skriptprozeduren des Dienstes. Dort finden Sie Beispiele für gängige Aufgaben wie z. B. Erstellung und Import von Datasets verschiedener Größen, Ausführen von Aufträgen und sequenzielles Zusammenstellen von Aufträgen sowie die Untersuchung der Ergebnisse Ihrer Aufträge. Die jeweiligen Datasets haben unterschiedliche Größen, sodass Sie die Leistungsunterschiede der Aufträge in Abhängigkeit von der Größe der Datasets beobachten können.</p>


**Voraussetzungen**:	

- Sie benötigen ein Azure-Abonnement. Hinweise zum Erstellen eines Kontos finden Sie auf der Seite [Testen Sie Azure noch heute kostenlos](http://azure.microsoft.com/de-de/pricing/free-trial/).

- Sie benötigen einen bereitgestellten HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Bereitstellen von HDInsight-Clustern](../hdinsight-provision-clusters/)

- Sie müssen die Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

## Die Beispiele ##

HDInsight wird mit den folgenden Beispielen ausgeliefert.

- [**Das Pi-Schätzungs-Beispiel**][hdinsight-sample-pi-estimator] Dieses Lernprogramm beschreibt die Ausführung eines MapReduce-Programms mit HDInsight, das den Wert von Pi mithilfe einer statistischen (Quasi-Monte-Carlo-Verfahren) Methode schätzt.
- [**Das Wortzählungsbeispiel**][hdinsight-sample-wordcount] Dieses Lernprogramm beschreibt die Verwendung eines HDInsight-Clusters für die Ausführung eines MapReduce-Programms, das Worthäufigkeiten in einer Textdatei zählt.
- [**Das 10-GB-Graysort-Beispiel**][hdinsight-sample-10gb-graysort] Dieses Lernprogramm beschreibt die Ausführung eines allgemeinen GraySort auf einer 10-GB-Datei mithilfe von HDInsight. Dieses Beispiel enthält drei ausführbare Aufträge: Teragen generiert die Daten, Terasort sortiert die Daten und Teravalidate prüft, ob die Daten korrekt sortiert wurden.
- [**Das C#-Streamingbeispiel**][hdinsight-sample-csharp-streaming] Dieses Lernprogramm zeigt, wie Sie ein MapReduce-Programm in C# schreiben können, das die Hadoop-Streamingschnittstelle verwendet. 


## Ausführen der Beispiele ##

Sie können diese Beispiele in der Azure PowerShell ausführen. Anweisungen für die einzelnen Beispiele finden Sie in den oben verlinkten Seiten.

## Nächste Schritte ##

In diesem Artikel und den Artikeln zu den einzelnen Beispielen haben Sie gelernt, wie Sie die in HDInsight-Clustern enthaltenen Beispiele in der Azure PowerShell ausführen können. Lernprogramme zu Pig, Hive und MapReduce mit HDInsight finden Sie in den folgenden Artikeln:

* [Erste Schritte mit dem Azure HDInsight-Dienst][hdinsight-get-started]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][hdinsight-submit-jobs]
* [Dokumentation zum Azure HDInsight SDK][hdinsight-sdk-documentation]
* [HDInsight debuggen: Fehlermeldungen][hdinsight-errors]


[hdinsight-errors]: ../hdinsight-debug-jobs/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-introduction]: ../hdinsight-hadoop-introduction/



[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/


<!--HONumber=42-->
