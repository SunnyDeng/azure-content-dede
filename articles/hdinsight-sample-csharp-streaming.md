<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="Das C# Streaming-Wortz&auml;hlungsbeispiel mit Hadoop in HDInsight | Azure" metaKeywords="hadoop, hdinsight, hdinsight administration, hdinsight administration azure" description="Erfahren Sie, wie Sie eine Beispiel-TBD ausf&uuml;hren k&ouml;nnen." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Das C# Streaming-Wortz&auml;hlungsbeispiel mit Hadoop in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Das C# Streaming-Wortzählungsbeispiel mit Hadoop in HDInsight

Hadoop stellt MapReduce eine Streaming-API zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können. In diesem Lernprogramm erstellen Sie ein MapReduce-Programm in C#, das die Hadoop-Streamingschnittstelle verwendet und führen das Programm mithilfe von Azure PowerShell-Cmdlets in Azure HDInsight aus.

Im Beispiel ist sowohl der Mapper als auch der Reducer eine ausführbare Datei, die eine Eingabe aus [stdin][stdin] liest (zeilenweise) und die Ausgabe in [stdout][stdin] schreibt. Das Programm zählt alle Wörter im Text.

Wenn eine ausführbare Datei für **Mapper** angegeben wird, ruft jede Mapper-Task die ausführbare Datei als separaten Prozess auf, wenn der Mapper initialisiert wird. Die Mapper-Task konvertiert ihre Eingaben in Zeilen und übermittelt die Zeilen an den [stdin][stdin] des Prozesses. In der Zwischenzeit sammelt der Mapper die zeilenbasierten Ausgaben des stdout des Prozesses und konvertiert jede Zeile in ein Schlüssel/Wert-Paar, das als Ausgabe des Mappers gesammelt wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (mit Ausnahme des Tabulatorzeichens) ist der Wert. Wenn die Zeile kein Tabulatorzeichen enthält, wird die gesamte Zeile als Schlüssel betrachtet, und der Wert ist null.

Wenn eine ausführbare Datei für **Reducer** angegeben wird, ruft jede Reducer-Task die ausführbare Datei als separaten Prozess auf, wenn der Reducer initialisiert wird. Die Reducer-Task konvertiert ihre Schlüssel/Wert-Eingabepaare in Zeilen und übermittelt die Zeilen an den [stdin][stdin] des Prozesses. In der Zwischenzeit sammelt der Reducer die zeilenbasierten Ausgaben des [stdout][stdin] des Prozesses und konvertiert jede Zeile in ein Schlüssel/Wert-Paar, das als Ausgabe des Reducers gesammelt wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (mit Ausnahme des Tabulatorzeichens) ist der Wert.

Weitere Informationen zur Hadoop-Streaming-Schnittstelle finden Sie unter [HadoopStreaming][HadoopStreaming] (in englischer Sprache).

**Sie erhalten Informationen zu folgenden Themen:**

-   Ausführen eines C#-Streamingprogramms zur Analyse von Daten in einer Datei in HDInsight mithilfe von Azure PowerShell.
-   Schreiben von C#-Code, der die Hadoop-Streaming-Schnittstelle verwendet

**Voraussetzungen**:

-   Sie benötigen ein Azure-Abonnement. Hinweise zur Erstellung eines Kontos finden Sie auf der Seite [Azure kostenlos ausprobieren][Azure kostenlos ausprobieren].

-   Sie benötigen einen bereitgestellten HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Bereitstellen eines HDInsight-Clusters][Bereitstellen eines HDInsight-Clusters]

-   Sie müssen die Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen zur entsprechenden Vorgehensweise finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

## Themen in diesem Artikel

In diesem Thema wird die Ausführung des Beispiels demonstriert und der Java-Code für das MapReduce-Programm vorgestellt. Außerdem erhalten Sie eine Zusammenfassung der vermittelten Lerninhalte und einen Ausblick auf nächste Schritte. Der Artikel enthält die folgenden Abschnitte:

1.  [Ausführen des Beispiels mit Azure PowerShell][Ausführen des Beispiels mit Azure PowerShell]
2.  [Der C#-Code für Hadoop-Streaming][Der C#-Code für Hadoop-Streaming]
3.  [Zusammenfassung][Zusammenfassung]
4.  [Nächste Schritte][Nächste Schritte]

## <span id="run-sample"></span></a>Ausführen des Beispiels mit Azure PowerShell

**So führen Sie den MapReduce-Job aus**

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

2.  Legen Sie die zwei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  Führen Sie den folgenden Befehl aus, um den MapReduce-Job zu definieren.

        # Create a MapReduce job definition for the streaming job.
        $streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

    Die Parameter geben die Mapper- und Reducer-Funktionen sowie die Eingabedatei und die Ausgabedateien an.

4.  Führen Sie die folgenden Befehle aus, um den MapReduce-Job auszuführen, auf den Abschluss des Jobs zu warten und dann den Standardfehler zu drucken:

        # Run the C# Streaming MapReduce job.
        # Wait for the job to complete.
        # Print output and standard error file of the MapReduce job
        Select-AzureSubscription $subscriptionName
        $streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

5.  Führen Sie die folgenden Befehle aus, um die Ergebnisse der Wortzählung anzuzeigen.

        $subscriptionName = "<SubscriptionName>"   
        $storageAccountName = "<StorageAccountName>" 
        $containerName = "<ContainerName>"

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Blob storage container and account name

    $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $\_.Primary }
     $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        # Retrieve the output
        Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

        # The number of words in the text is:
        cat ./example/data/StreamingOutput/wc.txt/part-00000

    Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

## <span id="java-code"></span></a>Der C#-Code für Hadoop-Streaming

Das MapReduce-Programm verwendet die Anwendung "cat.exe" als Mapping-Schnittstelle zum Streamen des Texts in die Konsole und die Anwendung "wc.exe" als Reduce-Schnittstelle zum Zählen der Anzahl von Wörtern, die aus dem Dokument gestreamt werden. Sowohl der Mapper als auch der Reducer lesen Zeichen (zeilenweise) aus dem Standard-Eingabedatenstrom (stdin) und schreiben in den Standard-Ausgabedatenstrom (stdout).

    // The source code for the cat.exe (Mapper). 
     
    using System;
    using System.IO;

    namespace cat
    {
        class cat
        {
            static void Main(string[] args)
            {
                if (args.Length > 0)
                {
                    Console.SetIn(new StreamReader(args[0])); 
                }

                string line;
                while ((line = Console.ReadLine()) != null) 
                {
                    Console.WriteLine(line);
                }
            }
        }
    }

Der Mapper-Code in der Datei "cat.cs" verwendet ein StreamReader-Objekt, um die Zeichen des eingehenden Datenstroms in die Konsole zu lesen, die ihrerseits den Datenstrom mit der statischen Console.Writeline-Methode in den Standard-Ausgabedatenstrom schreibt.

    // The source code for wc.exe (Reducer) is:

    using System;
    using System.IO;
    using System.Linq;

    namespace wc
    {
        class wc
        {
            static void Main(string[] args)
            {
                string line;
                var count = 0;

                if (args.Length > 0){
                    Console.SetIn(new StreamReader(args[0]));
                }

                while ((line = Console.ReadLine()) != null) {
                    count += line.Count(cr => (cr == ' ' || cr == '\n'));
                }
                Console.WriteLine(count);
            }
        }
    }

Der Reducer-Code in der Datei "wc.cs" verwendet ein [StreamReader][StreamReader]-Objekt zum Lesen von Zeichen aus dem Standard-Eingabedatenstrom, die vom cat.exe-Mapper ausgegeben wurden. Beim Lesen der Zeichen mit der [Console.Writeline][Console.Writeline]-Methode zählt der Code die Wörter, indem Leerzeichen und Zeilenendezeichen am Ende jedes Worts gezählt werden; anschließend wird die Summe mit der [Console.Writeline][Console.Writeline]-Methode in den Standard-Ausgabedatenstrom geschrieben.

## <span id="summary"></span></a>Zusammenfassung

In diesem Lernprogramm haben Sie erfahren, wie ein MapReduce-Job mithilfe von Hadoop-Streaming in HDInsight bereitgestellt wird.

## <span id="next-steps"></span></a>Nächste Schritte

Lernprogramme mit anderen Beispielen und Anweisungen zur Verwendung von Pig-, Hive- und MapReduce-Jobs in Azure HDInsight mit Azure PowerShell finden Sie in den folgenden Themen:

-   [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight]
-   [Beispiel: Pi-Schätzung][Beispiel: Pi-Schätzung]
-   [Beispiel: Wortzählung][Beispiel: Wortzählung]
-   [Beispiel: 10-GB-Graysort][Beispiel: 10-GB-Graysort]
-   [Verwenden von Pig mit HDInsight][Verwenden von Pig mit HDInsight]
-   [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight]
-   [Dokumentation zum Azure HDInsight SDK][Dokumentation zum Azure HDInsight SDK]

  [stdin]: http://msdn.microsoft.com/de-de/library/3x292kth(v=vs.110).aspx
  [HadoopStreaming]: http://wiki.apache.org/hadoop/HadoopStreaming
  [Azure kostenlos ausprobieren]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Bereitstellen eines HDInsight-Clusters]: ../hdinsight-provision-clusters/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [Ausführen des Beispiels mit Azure PowerShell]: #run-sample
  [Der C#-Code für Hadoop-Streaming]: #java-code
  [Zusammenfassung]: #summary
  [Nächste Schritte]: #next-steps
  [StreamReader]: http://msdn.microsoft.com/de-de/library/system.io.streamreader.aspx
  [Console.Writeline]: http://msdn.microsoft.com/de-de/library/system.console.writeline
  [Erste Schritte mit Azure HDInsight]: ../hdinsight-get-started/
  [Beispiel: Pi-Schätzung]: ../hdinsight-sample-pi-estimator/
  [Beispiel: Wortzählung]: ../hdinsight-sample-wordcount/
  [Beispiel: 10-GB-Graysort]: ../hdinsight-sample-10gb-graysort/
  [Verwenden von Pig mit HDInsight]: ../hdinsight-use-pig/
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
  [Dokumentation zum Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx
