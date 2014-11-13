<properties urlDisplayName="Run Hadoop Samples in HDInsight" pageTitle="Ausf&uuml;hren der Hadoop-Beispiele in HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="Erste Schritte mit dem Azure HDInsight-Dienst mit den mitgelieferten Beispielen. Verwenden von PowerShell-Skripts, die MapReduce-Programme auf Datenclustern ausf&uuml;hren." metaCanonical="" services="hdinsight" documentationCenter="" title="Ausf&uuml;hren der Hadoop-Beispiele in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Ausführen der Hadoop-Beispiele in HDInsight

Wir haben verschiedene Beispiele bereitgestellt, um Ihnen die ersten Schritte beim Ausführen von MapReduce-Aufträgen auf Hadoop-Clustern in Azure HDInsight zu erleichtern. Diese Beispiele sind in allen von HDInsight verwalteten Clustern verfügbar, die Sie erstellen. Führen Sie diese Beispiele aus, um sich mit den Azure PowerShell HDInsight-Cmdlets für die Ausführung von Aufträgen in Hadoop-Clustern vertraut zu machen.

MapReduce-Programme können außerdem programmgesteuert aus einer Anwendung heraus mit der Microsoft .NET API für HDInsight ausgeführt werden. Weitere Informationen zur Verwendung der HDInsight-APIs für die Auftragsübermittlung finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][Programmgesteuerte Übermittlung von Hadoop-Aufträgen].

Im Internet finden Sie umfangreiche Dokumentationen zu Hadoop-verwandten Technologien wie z. B. javabasierte MapReduce-Programmierung und Streaming sowie Dokumentationen zu Cmdlets in PowerShell-Skripts. Weitere Informationen zu diesen Ressourcen finden Sie im abschließenden Abschnitt **Ressourcen für HDInsight** des Artikels [Einführung in Azure HDInsight][Einführung in Azure HDInsight].

**Beschreibung der Beispiele**

Diese Beispiele bieten einen schnellen Einstieg in die Bereitstellung von Hadoop-Aufträgen und bieten eine umfangreiche Testumgebung zum Arbeiten mit den Konzepten und Skriptprozeduren des Dienstes. Dort finden Sie Beispiele für gängige Aufgaben wie z. B. Erstellung und Import von Datasets verschiedener Größen, Ausführen von Aufträgen und sequenzielles Zusammenstellen von Aufträgen sowie die Untersuchung der Ergebnisse Ihrer Aufträge. Die jeweiligen Datasets haben unterschiedliche Größen, sodass Sie die Leistungsunterschiede der Aufträge in Abhängigkeit von der Größe der Datasets beobachten können.

**Voraussetzungen**:

-   Sie benötigen ein Azure-Abonnement. Hinweise zur Erstellung eines Kontos finden Sie auf der Seite [Azure kostenlos ausprobieren][Azure kostenlos ausprobieren].

-   Sie benötigen einen bereitgestellten HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Bereitstellen eines HDInsight-Clusters][Bereitstellen eines HDInsight-Clusters]

-   Sie müssen die Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen zur entsprechenden Vorgehensweise finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

## Die Beispiele

HDInsight wird mit den folgenden Beispielen ausgeliefert.

-   [**Das Pi-Schätzungs-Beispiel**][**Das Pi-Schätzungs-Beispiel**] Dieses Lernprogramm beschreibt die Ausführung eines MapReduce-Programms mit HDInsight, das den Wert von Pi mithilfe einer statistischen (quasi Monte Carlo) Methode ermittelt.
-   [**Das Wortzahl-Beispiel**][**Das Wortzahl-Beispiel**] Dieses Lernprogramm beschreibt die Verwendung eines HDInsight-Clusters für die Ausführung eines MapReduce-Programms, das Worthäufigkeiten in einer Textdatei zählt.
-   [**Das 10-GB Graysort-Beispiel**][**Das 10-GB Graysort-Beispiel**] Dieses Lernprogramm beschreibt die Ausführung eines allgemeinen GraySort auf einer 10 GB-Datei mithilfe von HDInsight. Dieses Beispiel enthält drei ausführbare Aufträge: Teragen generiert die Daten, Terasort sortiert die Daten und Teravalidate prüft, ob die Daten korrekt sortiert wurden.
-   [**Das C#-Streamingbeispiel**][**Das C#-Streamingbeispiel**] Dieses Lernprogramm zeigt, wie Sie in C# ein MapReduce-Programm schreiben können, das die Hadoop-Streamingschnittstelle verwendet.

## Ausführen der Beispiele

Sie können diese Beispiele in der Azure PowerShell ausführen. Anweisungen für die einzelnen Beispiele finden Sie in den oben verlinkten Seiten.

## Nächste Schritte

In diesem Artikel und den Artikeln zu den einzelnen Beispielen haben Sie gelernt, wie Sie die in HDInsight-Clustern enthaltenen Beispiele in der Azure PowerShell ausführen können. Lernprogramme zu Pig, Hive und MapReduce mit HDInsight finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit dem Azure HDInsight-Dienst][Erste Schritte mit dem Azure HDInsight-Dienst]
-   [Verwenden von Pig mit HDInsight][Verwenden von Pig mit HDInsight]
-   [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight]
-   [Programmgesteuerte Übermittlung von Hadoop-Jobs][Programmgesteuerte Übermittlung von Hadoop-Aufträgen]
-   [Dokumentation zum Azure HDInsight SDK][Dokumentation zum Azure HDInsight SDK]
-   [HDInsight debuggen: Fehlermeldungen][HDInsight debuggen: Fehlermeldungen]

  [Programmgesteuerte Übermittlung von Hadoop-Aufträgen]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Einführung in Azure HDInsight]: ../hdinsight-hadoop-introduction/
  [Azure kostenlos ausprobieren]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Bereitstellen eines HDInsight-Clusters]: ../hdinsight-provision-clusters/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [**Das Pi-Schätzungs-Beispiel**]: ../hdinsight-sample-pi-estimator/
  [**Das Wortzahl-Beispiel**]: ../hdinsight-sample-wordcount/
  [**Das 10-GB Graysort-Beispiel**]: ../hdinsight-sample-10gb-graysort/
  [**Das C#-Streamingbeispiel**]: ../hdinsight-sample-csharp-streaming/
  [Erste Schritte mit dem Azure HDInsight-Dienst]: ../hdinsight-get-started/
  [Verwenden von Pig mit HDInsight]: ../hdinsight-use-pig/
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
  [Dokumentation zum Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx
  [HDInsight debuggen: Fehlermeldungen]: ../hdinsight-debug-jobs/
