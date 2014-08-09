<properties linkid="manage-services-hdinsight-howto-run-samples" urlDisplayName="Run HDInsight Samples" pageTitle="Run the HDInsight samples | Azure" metaKeywords="" description="Get started using the Azure HDInsight service with the samples provided. Use PowerShell scripts that run MapReduce programs on data clusters." metaCanonical="" services="hdinsight" documentationCenter="" title="Run the HDInsight samples" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Ausführen der HDInsight-Beispiele
=================================

Für Ihre ersten Schritte mit Azure HDInsight stehen Ihnen verschiedene Beispiele zur Verfügung. Diese Beispiele sind in den einzelnen HDInsight-Clustern enthalten, die Sie erstellen. Sie können diese Beispiele ausführen, um sich mit den Azure PowerShell HDInsight-Cmdlets vertraut zu machen.

MapReduce-Programme können außerdem programmgesteuert aus einer Anwendung heraus mit der Microsoft .NET API für HDInsight ausgeführt werden. Weitere Informationen zur Verwendung der HDInsight-APIs für die Auftragsübermittlung finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen](/de-de/manage/services/hdinsight/submit-hadoop-jobs-programmatically/).

Im Internet finden Sie umfangreiche Dokumentationen zu Hadoop-verwandten Technologien wie z. B. javabasierte MapReduce-Programmierung und Streaming sowie Dokumentationen zu Cmdlets in PowerShell-Skripts. Weitere Informationen zu diesen Ressourcen finden Sie im abschließenden Abschnitt **Ressourcen für HDInsight** des Artikels [Einführung in Azure HDInsight](/de-de/manage/services/hdinsight/introduction-hdinsight/).

**Beschreibung der Beispiele**

Diese Beispiele bieten einen schnellen Einstieg in die Bereitstellung von Hadoop-Aufträgen und bieten eine umfangreiche Testumgebung zum Arbeiten mit den Konzepten und Skriptprozeduren des Dienstes. Dort finden Sie Beispiele für gängige Aufgaben wie z. B. Erstellung und Import von Datasets verschiedener Größen, Ausführen von Aufträgen und sequenzielles Zusammenstellen von Aufträgen sowie die Untersuchung der Ergebnisse Ihrer Aufträge. Die jeweiligen Datasets haben unterschiedliche Größen, sodass Sie die Leistungsunterschiede der Aufträge in Abhängigkeit von der Größe der Datasets beobachten können.

**Voraussetzungen**:

-   Sie benötigen ein Azure-Abonnement. Hinweise zur Erstellung eines Kontos finden Sie auf der Seite [Azure kostenlos ausprobieren](http://www.windowsazure.com/de-de/pricing/free-trial/).

-   Sie benötigen ein bereitgestelltes HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Bereitstellen eines HDInsight-Clusters](/de-de/manage/services/hdinsight/provision-hdinsight-clusters/)

-   Sie müssen die Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).

Die Beispiele
-------------

HDInsight wird mit den folgenden Beispielen ausgeliefert.

-   [**Das Pi-Schätzungs-Beispiel**](/de-de/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/) Dieses Lernprogramm beschreibt die Ausführung eines MapReduce-Programms mit HDInsight, das den Wert von Pi mithilfe einer statistischen (quasi Monte Carlo) Methode ermittelt.
-   [**Das Wortzahl-Beispiel**](/de-de/manage/services/hdinsight/howto-run-samples/sample-wordcount/) Dieses Lernprogramm beschreibt die Verwendung eines HDInsight-Clusters für die Ausführung eines MapReduce-Programms, das Worthäufigkeiten in einer Textdatei zählt.
-   [**Das 10-GB Graysort-Beispiel**](/de-de/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/) Dieses Lernprogramm beschreibt die Ausführung eines allgemeinen GraySort auf einer 10 GB-Datei mithilfe von HDInsight. Dieses Beispiel enthält drei ausführbare Aufträge: Teragen generiert die Daten, Terasort sortiert die Daten und Teravalidate prüft, ob die Daten korrekt sortiert wurden.
-   [**Das C\#-Streamingbeispiel**](/de-de/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/) Dieses Lernprogramm zeigt, wie Sie in C\# ein MapReduce-Programm schreiben können, das die Hadoop-Streamingschnittstelle verwendet.

Ausführen der Beispiele
-----------------------

Sie können diese Beispiele in der Azure PowerShell ausführen. Anweisungen für die einzelnen Beispiele finden Sie in den oben verlinkten Seiten.

Nächste Schritte
----------------

In diesem Artikel und den Artikeln zu den einzelnen Beispielen haben Sie gelernt, wie Sie die in HDInsight-Clustern enthaltenen Beispiele in der Azure PowerShell ausführen können. Lernprogramme zu Pig, Hive und MapReduce mit HDInsight finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit dem Azure HDInsight-Dienst](/de-de/manage/services/hdinsight/get-started-hdinsight/)
-   [Verwenden von Pig mit HDInsight](/de-de/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Verwenden von Hive mit HDInsight](/de-de/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Programmgesteuerte Übermittlung von Hadoop-Aufgaben](/de-de/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Dokumentation des Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)
-   [Debuggen von HDInsight: Fehlermeldungen](/de-de/manage/services/hdinsight/debug-error-messages/)

[Powershell-install-configure]: /de-de/documentation/articles/install-configure-powershell/

[hdinsight-debug-error-messages]: /de-de/manage/services/hdinsight/debug-error-messages/
[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
[getting-started]: /de-de/manage/services/hdinsight/get-started-hdinsight/

[hive]: /de-de/manage/services/hdinsight/using-hive-with-hdinsight/
[pig]: /de-de/manage/services/hdinsight/using-pig-with-hdinsight/
[pi-estimator]: /de-de/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/
[10gb-graysort]: /de-de/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/
[wordcount]: /de-de/manage/services/hdinsight/howto-run-samples/sample-wordcount/
[cs-streaming]: /de-de/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/

[submit-jobs-programmatically]: /de-de/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-resources]: /de-de/manage/services/hdinsight/introduction-hdinsight/
