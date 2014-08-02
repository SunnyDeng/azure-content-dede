<properties linkid="manage-services-hdinsight-howto-sdk" urlDisplayName="HDInsight SDK" pageTitle="How to use the HDInsight .NET libraries | Azure" metaKeywords="" description="Learn how to get the HDInsight NuGet packages and use them from your .NET application." metaCanonical="" services="hdinsight" documentationCenter="" title="Use the Hadoop .NET SDK with HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Verwenden des Hadoop .NET SDK mit HDInsight
===========================================

Das Hadoop .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit Hadoop in .NET. In diesem Lernprogramm erfahren Sie, wie Sie das Hadoop .NET SDK herunterladen und zur Erstellung einer einfachen .NET-basierten Anwendung verwenden können, die Hive-Abfragen über den Azure HDInsight-Dienst ausführt. Mit einer actors.txt-Datei werden Sie eine Anwendung schreiben, um die Schauspieler mit den meisten Awards zu ermitteln.

Klicken Sie [hier](https://account.windowsazure.com/PreviewFeatures), um HDInsight zu aktivieren.

Themen in diesem Artikel
------------------------

-   [Download und Installation des Hadoop .NET SDK](#install)
-   [Vorbereitung für das Lernprogramm](#prepare)
-   [Erstellen der Anwendung](#create)
-   [Ausführen der Anwendung](#run)
-   [Nächste Schritte](#nextsteps)

Download und Installation des Hadoop .NET SDK
---------------------------------------------

Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) installieren. Das SDK enthält die folgenden Komponente:

-   **MapReduce-Bibliothek** - Vereinfacht die Erstellung von MapReduce-Jobs in .NET-Sprachen über die Hadoop-Streamingschnittstelle.

-   **LINQ to Hive-Clientbibliothek** - Übersetzt C\#- oder F\#-LINQ-Abfragen in HiveQL-Abfragen und führt diese auf dem Hadoop-Cluster aus. Die Bibliothek ist außerdem in der Lage, beliebige HiveQL-Abfragen aus .NET-Anwendungen auszuführen.

-   **WebClient-Bibliothek** - Enthält Clientbibliotheken für *WebHDFS* und *WebHCat*.

    -   **WebHDFS-Clientbibliothek** - Arbeitet mit Dateien in HDFS und im Azure Blob-Speicher.

    -   **WebHCat-Clientbibliothek** - Verwaltet die Planung und Ausführung von Jobs im HDInsight-Cluster.

Installieren Sie die Bibliotheken mit der folgenden NuGet-Syntax:

    install-package Microsoft.Hadoop.MapReduce
    install-package Microsoft.Hadoop.Hive 
    install-package Microsoft.Hadoop.WebClient 

Diese Befehle installieren die Bibliotheken und fügen Verweise zum aktuellen Visual Studio-Projekt hinzu.

Vorbereitung für das Lernprogramm
---------------------------------

Sie benötigen ein [Azure-Abonnement](http://www.windowsazure.com/en-us/pricing/free-trial/) und ein [Azure-Speicherkonto](http://www.windowsazure.com/en-us/manage/services/storage/how-to-create-a-storage-account/), bevor Sie fortfahren. Außerdem müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen. Anweisungen zum Abrufen dieser Informationen finden Sie im Abschnitt *Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln* des Artikels [How to Manage Storage Accounts](/en-us/manage/services/storage/how-to-manage-a-storage-account/) (Verwalten von Speicherkonten, in englischer Sprache).

Außerdem müssen Sie die in diesem Lernprogramm verwendete Datei Actors.txt herunterladen. Führen Sie die folgenden Schritte aus, um diese Datei in Ihre Entwicklungsumgebung herunterzuladen:

1.  Erstellen Sie einen Ordner C:\\Tutorials auf Ihrem lokalen Computer.

2.  Laden Sie [Actors.txt](http://www.microsoft.com/en-us/download/details.aspx?id=37003) herunter und speichern Sie die Datei unter C:\\Tutorials.

Erstellen der Anwendung
-----------------------

In diesem Abschnitt erfahren Sie, wie Sie Dateien programmgesteuert in Ihr Hadoop-Cluster hochladen und Hive-Jobs mithilfe von LINQ to Hive ausführen können.

1.  Öffnen Sie Visual Studio 2012.

2.  Klicken Sie im Menü Datei auf **Neu** und anschließend auf **Projekt**.

3.  Unter Neues Projekt können Sie die folgenden Werte eingeben bzw. auswählen:

    <table data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
 <tr data-morhtml="true">
 <th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Eigenschaft</th>
 <th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Wert</th></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Kategorie</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Vorlagen/Visual C#/Windows</td></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vorlage</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konsolenanwendung</td></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SimpleHiveJob</td></tr>
 </table>


4.  Klicken Sie auf **OK**, um das Projekt zu erstellen.

5.  Klicken Sie im Menü **Extras** auf **Paketverwaltung** und anschließend auf **Paketverwaltungskonsole**.

6.  Führen Sie die folgenden Befehle in der Konsole aus, um die Pakete zu installieren.

         install-package Microsoft.Hadoop.Hive 
         install-package Microsoft.Hadoop.WebClient 

    Diese Befehle installieren .NET-Bibliotheken und fügen Verweise zum aktuellen Visual Studio-Projekt hinzu.

7.  Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8.  Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

         using Microsoft.Hadoop.WebHDFS.Adapters;
         using Microsoft.Hadoop.WebHDFS;
         using Microsoft.Hadoop.Hive;

9.  Fügen Sie in der Main()-Funktion den folgenden Code ein:

         // actors.txt in den Blob-Speicher hochladen
         var asvAccount = [Storage-account-name.blob.core.windows.net];
         var asvKey = [Storage account key];
         var asvContainer = [Container name];
         var localFile = "C:/Tutorials/Actors.txt";
         var hadoopUser = [Hadoop user name]; // The HDInsight cluster user
         var hadoopUserPassword = [Hadoop user password]; // The HDInsight cluster user password
         var clusterURI = [HDInsight cluster URL]; //"https://HDInsightCluster Name.azurehdinsight.net:563";
            
         var storageAdapter = new BlobStorageAdapter(asvAccount, asvKey, asvContainer, true);
         var HDFSClient = new WebHDFSClient(hadoopUser, storageAdapter);
            
         Console.WriteLine("Creating MovieData directory and uploading actors.txt...");
         HDFSClient.CreateDirectory("/user/hadoop/MovieData").Wait();
         HDFSClient.CreateFile(localFile, "/user/hadoop/MovieData/Actors.txt").Wait();
            
         // Hive-Verbindung erstellen
         var hiveConnection = new HiveConnection(
           new System.Uri(clusterURI),
           hadoopUser, 
           hadoopUserPassword,
           asvAccount, 
           asvKey);
            
         // Existierende Tabellen mit dem Namen Actors löschen
         // Nur benötigt, wenn Sie diese Anwendung mehrfach ausführen
         // und ältere Actors-Tabellen löschen möchten.
         //hiveConnection.GetTable<HiveRow>("Actors").Drop();

         Console.WriteLine("Creating a Hive table named 'Actors'...");
         string command =
           @"CREATE TABLE Actors(
                     MovieId string, 
                     ActorId string,
                     Name string, 
                     AwardsCount int) 
                     row format delimited fields 
                 terminated by ',';";
         hiveConnection.ExecuteHiveQuery(command).Wait();
            
         Console.WriteLine("Load data from Actors.txt into the 'Actors' table in Hive...");
         command =
           @"LOAD DATA INPATH 
                 '/user/hadoop/MovieData/Actors.txt'
             OVERWRITE INTO TABLE Actors;";
         hiveConnection.ExecuteHiveQuery(command).Wait();
            
         Console.WriteLine("Performing Hive query...");
         var result = hiveConnection.ExecuteQuery(@"select name, awardscount
                   from actors sort by awardscount desc
                   limit 1");
         result.Wait();

         Console.WriteLine("The results are: {0}", result.Result.ReadToEnd());
         Console.WriteLine("\nPress any key to continue.");
         Console.ReadKey();

10. Aktualisieren Sie die Konstanten in Ihrer Anwendung. Der Azure HDInsight-Dienst verwendet Azure Blob-Speicher als Standarddateisystem. Bei der Einrichtung von HDInsight wird ein Blob als Standarddateisystem festgelegt. Sie können entweder diesen Standarddateisystem-Container oder einen Container in einem anderen Blob-Speicher verwenden. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/).

    Falls Sie den Standarddateisystem-Container verwenden, finden Sie Speicherkontoname, Speicherschlüssel und Containername in der *c:\\apps\\dist\\hadoop-1.1.0-SNAPSHOT\\conf\>core-site.xml*-Konfigurationsdatei, indem Sie sich mit dem Cluster verbinden. Suchen Sie nach *fs.default.name*, um den Standarddateisystem-Container zu ermitteln; Suchen Sie nach *fs.azure.account.key*, um Speicherkontoname und Kontoschlüssel zu ermitteln.

Ausführen der Anwendung
-----------------------

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. Daraufhin öffnet sich ein Konsolenfenster und zeigt die Anwendungsschritte an, während die Daten hochgeladen, in einer Hive-Tabelle abgelegt und zuletzt abgefragt werden. Nach Abschluss der Anwendung und Rückgabe der Ergebnisse können Sie die Anwendung mit einer beliebigen Taste beenden.

![HDI.HadoopSDKOutput](./media/hdinsight-use-hadoop-dotnet-sdk/HDI.HadoopSDKOutput.PNG "Konsolenanwendung")

**Hinweis**

Die einzelnen Anwendungsschritte können Sekunden oder sogar Minuten in Anspruch nehmen. Die Uploaddauer der Datei Actors.txt hängt von Ihrer Internetverbindung zum Azure-Rechenzentrum ab, während die Dauer der anderen Schritte von der Clustergröße abhängt.

**Hinweis**

Der LOAD DATA INPATH-Vorgang verschiebt die Datei Actors.txt in den von Hive kontrollierten Dateisystem-Namespace. Dabei wird die Datei Actors.txt aus dem Upload-Ordner entfernt. Daher müssen Sie die Datei Actors.txt bei jeder Ausführung der Anwendung erneut hochladen.

Weitere Informationen zum Hochladen von Dateien in Hive finden Sie unter [Hive Getting Started](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-DMLOperations) (in englischer Sprache).

Nächste Schritte
----------------

Sie sind nun in der Lage, eine .NET-Anwendung mit dem Hadoop .NET SDK zu erstellen. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Verwenden von Pig mit HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Verwenden von MapReduce mit HDInsight](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [Verwenden von Hive mit HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)

