<properties linkid="manage-services-hdinsight-sample-csharp-streaming" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight C# streaming wordcount sample | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run a sample TBD." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight C# streaming wordcount sample" authors="bradsev" />

Das HDInsight C\# Streaming-Wortzählbeispiel
============================================

Hadoop stellt MapReduce eine Streaming-API zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können. In diesem Lernprogramm erfahren Sie, wie Sie MapReduce-Programme in C\# schreiben (diese Sprache verwendet die Hadoop-Streaming-Schnittstelle) und wie Sie die Programme mit Azure PowerShell in Azure HDInsight ausführen.

Im Beispiel ist sowohl der Mapper als auch der Reducer eine ausführbare Datei, die eine Eingabe aus [stdin](http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx) liest (zeilenweise) und die Ausgabe in [stdout](http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx) schreibt. Das Programm zählt alle Wörter im Text.

Wenn eine ausführbare Datei für **Mapper** angegeben wird, ruft jede Mapper-Task die ausführbare Datei als separaten Prozess auf, wenn der Mapper initialisiert wird. Die Mapper-Task konvertiert ihre Eingaben in Zeilen und übermittelt die Zeilen an den [stdin](http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx) des Prozesses. In der Zwischenzeit sammelt der Mapper die zeilenbasierten Ausgaben des stdout des Prozesses und konvertiert jede Zeile in ein Schlüssel/Wert-Paar, das als Ausgabe des Mappers gesammelt wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (mit Ausnahme des Tabulatorzeichens) ist der Wert. Wenn die Zeile kein Tabulatorzeichen enthält, wird die gesamte Zeile als Schlüssel betrachtet, und der Wert ist null.

Wenn eine ausführbare Datei für **Reducer** angegeben wird, ruft jede Reducer-Task die ausführbare Datei als separaten Prozess auf, wenn der Reducer initialisiert wird. Die Reducer-Task konvertiert ihre Schlüssel/Wert-Eingabepaare in Zeilen und übermittelt die Zeilen an den [stdin](http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx) des Prozesses. In der Zwischenzeit sammelt der Reducer die zeilenbasierten Ausgaben des [stdout](http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx) des Prozesses und konvertiert jede Zeile in ein Schlüssel/Wert-Paar, das als Ausgabe des Reducers gesammelt wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (mit Ausnahme des Tabulatorzeichens) ist der Wert.

Weitere Informationen zur Hadoop-Streaming-Schnittstelle finden Sie unter [HadoopStreaming](http://wiki.apache.org/hadoop/HadoopStreaming) (in englischer Sprache).

**Sie erhalten Informationen zu folgenden Themen:**

-   Verwenden von Azure PowerShell zum Ausführen eines C\#-Streaming-Programms zum Analysieren von Daten in einer Datei in HDInsight
-   Schreiben von C\#-Code, der die Hadoop-Streaming-Schnittstelle verwendet

**Voraussetzungen**:

-   Sie benötigen ein Azure-Abonnement. Optionen zum Anmelden für ein Konto finden Sie auf der Seite [Kostenlose Testversion](http://www.windowsazure.com/en-us/pricing/free-trial/).

-   Sie benötigen einen bereitgestellten HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Bereitstellen eines HDInsight-Clusters](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)

-   Sie müssen Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Themen in diesem Artikel
------------------------

In diesem Thema wird die Ausführung des Beispiels demonstriert und der Java-Code für das MapReduce-Programm vorgestellt. Außerdem erhalten Sie eine Zusammenfassung der vermittelten Lerninhalte und einen Ausblick auf nächste Schritte. Der Artikel enthält die folgenden Abschnitte:

1.  [Ausführen des Beispiels mit Azure PowerShell](#run-sample)
2.  [Der C\#-Code für Hadoop-Streaming](#java-code)
3.  [Zusammenfassung](#summary)
4.  [Nächste Schritte](#next-steps)

Ausführen des Beispiels mit Azure PowerShell
--------------------------------------------

**So führen Sie den MapReduce-Job aus**

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Legen Sie die zwei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

         $subscriptionName = "<SubscriptionName>"   # Name des Azure-Abonnements
         $clusterName = "<ClusterName>"             # Name des HDInsight-Clusters

3.  Führen Sie den folgenden Befehl aus, um den MapReduce-Job zu definieren.

         # MapReduce-Jobdefinition für den Streamingjob erstellen.
         $streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

    Die Parameter geben die Mapper- und Reducer-Funktionen sowie die Eingabedatei und die Ausgabedateien an.

4.  Führen Sie die folgenden Befehle aus, um den MapReduce-Job auszuführen, auf den Abschluss des Jobs zu warten und dann den Standardfehler zu drucken:

         # C#-Streaming-MapReduce-Job ausführen.
         # Auf Abschluss des Jobs warten.
         # Ausgabe und Standardfehlerdatei des MapReduce-Jobs drucken
         Select-AzureSubscription $subscriptionName
         $streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

5.  Führen Sie die folgenden Befehle aus, um die Ergebnisse der Wortzählung anzuzeigen.

         $subscriptionName = "<SubscriptionName>"   
         $storageAccountName = "<StorageAccountName>" 
         $containerName = "<ContainerName>"


         # Aktuelles Abonnement auswählen
         Select-AzureSubscription $subscriptionName
                  
         # Blob-Speichercontainer und Kontoname

         $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

         # Ausgabe abrufen
         Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

         # Die Anzahl der Wörter im Text beträgt:
         cat ./example/data/StreamingOutput/wc.txt/part-00000

    Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

Der C\#-Code für Hadoop-Streaming
---------------------------------

Das MapReduce-Programm verwendet die Anwendung "cat.exe" als Mapping-Schnittstelle zum Streamen des Texts in die Konsole und die Anwendung "wc.exe" als Reduce-Schnittstelle zum Zählen der Anzahl von Wörtern, die aus dem Dokument gestreamt werden. Sowohl der Mapper als auch der Reducer lesen Zeichen (zeilenweise) aus dem Standard-Eingabedatenstrom (stdin) und schreiben in den Standard-Ausgabedatenstrom (stdout).

    // Der Quellcode für cat.exe (Mapper). 
     
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

    // Der Quellcode für wc.exe (Reducer) lautet:

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

Der Reducer-Code in der Datei "wc.cs" verwendet ein [StreamReader](http://msdn.microsoft.com/en-us/library/system.io.streamreader.aspx)-Objekt zum Lesen von Zeichen aus dem Standard-Eingabedatenstrom, die vom cat.exe-Mapper ausgegeben wurden. Beim Lesen der Zeichen mit der [Console.Writeline](http://msdn.microsoft.com/en-us/library/system.console.writeline)-Methode zählt der Code die Wörter, indem Leerzeichen und Zeilenendezeichen am Ende jedes Worts gezählt werden; anschließend wird die Summe mit der [Console.Writeline](http://msdn.microsoft.com/en-us/library/system.console.writeline)-Methode in den Standard-Ausgabedatenstrom geschrieben.

Zusammenfassung
---------------

In diesem Lernprogramm haben Sie erfahren, wie ein MapReduce-Job mithilfe von Hadoop-Streaming in HDInsight bereitgestellt wird.

Nächste Schritte
----------------

Lernprogramme mit anderen Beispielen und Anweisungen zur Verwendung von Pig-, Hive- und MapReduce-Jobs in Azure HDInsight mit Azure PowerShell finden Sie in den folgenden Themen:

-   [Erste Schritte mit Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Beispiel: Pi-Schätzung](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Beispiel: Wortzählung](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Beispiel: 10-GB-Graysort](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Verwenden von Pig mit HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Verwenden von Hive mit HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Dokumentation des Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

