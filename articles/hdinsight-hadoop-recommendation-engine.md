<properties linkid="manage-services-hdinsight-recommendation-engine-using-mahout" urlDisplayName="Hadoop Recommendation Engine" pageTitle="Hadoop recommendation engine (.NET) | Azure" metaKeywords="Azure Apache Mahout, Azure recommendation example, Azure recommendation tutorial, Azure recommendation engine" description="A tutorial that teaches how to use the Apache Mahout recommendation engine with Azure to create song suggestions based on listening habits." disqusComments="1" umbracoNaviHide="1" title="Simple recommendation engine using Apache Mahout" authors="jgao" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Einfaches Empfehlungssystem auf Basis von Apache Mahout

Apache Mahout™ ist eine Bibliothek für maschinelles Lernen, die für den Einsatz in skalierbaren Anwendungen für maschinelles Lernen entwickelt wurde. Empfehlungsmodule wie Mahout sind eine der beliebtesten Anwendungsarten für maschinelles Lernen heutzutage und bieten zahlreiche offensichtliche Marketinganwendungen.

Apache Mahout stellt eine integrierte Implementierung für artikelbasiertes kollaboratives Filtern (Item-based Collaborative Filtering) zur Verfügung. Diese Methode wird verbreitet für das Data Mining zum Entwickeln von Empfehlungen eingesetzt. Item-based Collaborative Filtering wurde von Amazon.com entwickelt. Das Verfahren basiert auf der Idee, dass anhand von Daten zu Benutzerpräferenzen, die Korrelationen zwischen Artikelpräferenzen aufzeigen, die Vorlieben zukünftiger Benutzer aus einer ähnlichen Gruppe abgeleitet werden können.

In diesem Lernprogramm besuchen Sie die Website [Million Song Dataset][Million Song Dataset] und laden den [Datensatz][Datensatz] herunter, um Musikempfehlungen für Benutzer basierend auf von diesen bisher bevorzugt gehörten Titeln zu erstellen.

Sie erhalten Informationen zu folgenden Themen:

-   Verwenden von Empfehlungssystemen

Dieses Lernprogramm umfasst die folgenden Abschnitte:

1.  [Setup und Konfiguration][Setup und Konfiguration]
2.  [Überprüfen und Formatieren der Daten][Überprüfen und Formatieren der Daten]
3.  [Installieren von Mahout][Installieren von Mahout]
4.  [Ausführen des Mahout-Jobs][Ausführen des Mahout-Jobs]

## <a name="setup"></a>Setup und Konfiguration

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie Azure und die HDInsight-Vorschau eingerichtet und einen HDInsight-Cluster zum Ausführen eines Beispiels erstellt haben. Wenn Sie diese Voraussetzungen noch nicht erfüllen, finden Sie entsprechende Anweisungen im Lernprogramm [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight].

## <a name="segment1"></a>Überprüfen und Formatieren der Daten

Dieses Beispiel beschäftigt sich mit den verschiedenen Möglichkeiten, wie Benutzer eine Präferenz für bestimmte Musiktitel ausdrücken können. Es wird davon ausgegangen, dass die Vorliebe eines Benutzers für einen Musiktitel daran zu erkennen ist, wie oft er sich den Titel anhört. Anhand von in den Präferenzdaten erkannten Mustern können zukünftige Benutzerpräferenzen auf Basis einiger ihrer bisherigen musikalischen Vorlieben vorhergesagt werden. Ein Beispiel dieses Datensatzes finden Sie im Abschnitt **Description** der Webseite [Echo Nest Taste Profile Subset][Million Song Dataset]:

![The Echo Nest Taste Profile Subset][The Echo Nest Taste Profile Subset]

### Beispieldaten aus dem Million Song Dataset

Um diesen Datensatz mit Mahout verwenden zu können, müssen Sie zwei Schritte ausführen:

1.  Konvertieren der IDs von Musiktiteln und Benutzern in ganzzahlige Werte
2.  Speichern der neuen Werte mit ihrer Rangfolge in einer durch Komma getrennten Datei

Falls Visual Studio 2010 nicht auf Ihrem Computer installiert ist, überspringen Sie diesen Schritt, und fahren Sie mit dem Abschnitt "Ausführen des Mahout-Jobs" fort, um mit einer vorab generierten Version zu arbeiten.

Starten Sie als Erstes Visual Studio 2010. Wählen Sie in Visual Studio **Datei -\> Neu -\> Projekt**. Wählen Sie im Bereich **Installierte Vorlagen** im Knoten **Visual C#** die Kategorie **Fenster** aus, und wählen Sie dann **Konsolenanwendung** aus der Liste. Nennen Sie das Projekt "ConvertToMahoutInput", und klicken Sie auf **OK**.

![Erstellen einer Konsolenanwendung][Erstellen einer Konsolenanwendung]

### Erstellen einer Konsolenanwendung

1.  Nachdem Sie die Anwendung erstellt haben, öffnen Sie die Datei **Program.cs**, und fügen Sie der **Program**-Klasse die folgenden statischen Member hinzu:

        const char tab = '\u0009';
        static Dictionary<string, int> usersMapping = new Dictionary<string, int>();
        static Dictionary<string, int> songMapping = new Dictionary<string, int>(); 

2.  Fügen Sie als Nächstes die Anweisung `using System.IO;` hinzu, und füllen Sie die **Main**-Methode mit folgendem Code:

        var inputStream = File.Open(args[0], FileMode.Open);
        var reader = new StreamReader(inputStream);

        var outStream = File.Open("mInput.txt", FileMode.OpenOrCreate);
        var writer = new StreamWriter(outStream);

        var i = 1;

        var line = reader.ReadLine();
        while (!string.IsNullOrWhiteSpace(line))
        {
            i++;
            if (i > 5000)
            break;
            var outLine = line.Split(tab);

            int user = GetUser(outLine[0]);
            int song = GetSong(outLine[1]);

            writer.Write(user);
            writer.Write(',');
            writer.Write(song);
            writer.Write(',');
            writer.WriteLine(outLine[2]);

            line = reader.ReadLine();
        }

        Console.WriteLine("saved {0} lines to {1}", i, args[0]);

        reader.Close();
        writer.Close();

        SaveMapping(usersMapping, "users.csv");
        SaveMapping(songMapping, "mInput.csv");

        Console.WriteLine("Mapping saved");
        Console.ReadKey();

3.  Erstellen Sie jetzt die Funktionen **GetUser** und **GetSong**, mit denen die IDs in ganze Zahlen konvertiert werden:

        static int GetUser(string user)
        {
            if (!usersMapping.ContainsKey(user))
                usersMapping.Add(user, usersMapping.Count + 1);

            return usersMapping[user];
        }

        static int GetSong(string song)
        {
            if (!songMapping.ContainsKey(song))
                songMapping.Add(song, songMapping.Count + 1);

            return songMapping[song];
        }

4.  Zum Schluss erstellen Sie das Dienstprogramm, das die SaveMapping-Methode implementiert, mit der die Mapping-Verzeichnisse in CSV-Dateien gespeichert werden.

        static void SaveMapping(Dictionary<string, int> mapping, string fileName)
        {
            var stream = File.Open(fileName, FileMode.Create);
            var writer = new StreamWriter(stream);

            foreach (var key in mapping.Keys)
            {
                writer.Write(key);
                writer.Write(',');
                writer.WriteLine(mapping[key]);
            }

            writer.Close();
        }

5.  Laden Sie die Beispieldaten unter [diesem Link][Datensatz] herunter. Öffnen Sie nach dem Download die Datei **train\_triplets.txt.zip**, und extrahieren Sie **train\_triplets.txt**.

    Schließen Sie beim Ausführen des Dienstprogramms ein Befehlszeilenargument ein, das den Speicherort der Datei **train\_triplets.txt** angibt. Klicken Sie hierzu im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten **ConvertToMahoutInput**, und wählen Sie **Eigenschaften**. Wählen Sie auf der Seite mit den Projekteigenschaften die Registerkarte **Debuggen** auf der linken Seite aus, und fügen Sie den Pfad "\<localpath\>train\_triplets.txt" in das Textfeld **Befehlszeilenargumente** ein:

    ![Befehlszeilenargumente festlegen][Befehlszeilenargumente festlegen]

### Festlegen des Befehlszeilenarguments

-   Drücken Sie die Taste **F5**, um das Programm auszuführen. Öffnen Sie anschließend den Ordner **bin\\Debug** aus dem Speicherort des Projekts, und zeigen Sie die Ausgabe des Dienstprogramms an. "users.txt" und "mInput.txt" sollten vorhanden sein.

## <a name="segment2"></a>Installieren von Mahout

-   Öffnen Sie das HDInsight-Clusterportal, und klicken Sie auf das Symbol **Remotedesktop**.

    ![Das Symbol "Cluster verwalten"][Das Symbol "Cluster verwalten"]

### Das Symbol "Remotedesktop"

Mahout ist nicht standardmäßig in HDInsight enthalten. Da es jedoch Teil des Hadoop-Gesamtsystems ist, kann es von der [Mahout][Mahout]-Website heruntergeladen werden. Die neueste Version ist 0.7, die vorliegenden Anweisungen sind jedoch für Version 0.5 oder 0.7 gültig.

1.  Laden Sie zuerst [Mahout Version 0.7][Mahout Version 0.7] auf Ihren lokalen Computer herunter.

2.  Kopieren Sie das Programm dann auf den Cluster, indem Sie die lokale ZIP-Datei auswählen, mit STRG+C kopieren und dann mit STRG+V in Ihren Hadoop-Cluster einfügen.

    ![Mahout hochladen][Mahout hochladen]

### Kopieren von Mahout in den Hauptknoten

1.  Klicken Sie nach Abschluss des Kopiervorgangs mit der rechten Maustaste auf die ZIP-Datei, und extrahieren Sie die Mahout-Bereitstellung in den Ordner "C:\\apps\\dist". Mahout ist jetzt im Pfad "C:\\apps\\dist\\mahout-distriution-0.7" installiert.

2.  Benennen Sie den Ordner der Einfachheit halber in "c:\\apps\\dist\\mahout-0.7" um.

### <a name="segment3"></a>Ausführen des Mahout-Jobs

1.  Kopieren Sie die Datei **mInput.txt** aus dem Ordner **bin\\Debug** in den Pfad **c:\\** auf dem Remotecluster. Nach dem Kopieren extrahieren Sie die Datei. Wie bereits im vorherigen Abschnitt beschrieben, gehen Sie zum Kopieren einer Datei in eine RDP-Sitzung folgendermaßen vor: Wählen Sie die Datei auf dem lokalen Computer aus, drücken Sie STRG+C, wechseln Sie in das Fenster der RDP-Sitzung, und drücken Sie STRG+V.

2.  Erstellen Sie eine Datei mit der ID des Benutzers, für den Sie Empfehlungen generieren möchten. Erstellen Sie hierzu einfach eine Textdatei namens **users.txt** im Ordner **c:\\**, die die ID eines einzelnen Benutzers enthält.

<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Sie k&ouml;nnen Empfehlungen f&uuml;r mehrere Benutzer generieren, indem Sie deren IDs in eigene Zeilen eingeben. Wenn Sie Probleme beim Generieren der Dateien &quot;mInput.txt&quot; und &quot;users.txt&quot; haben, k&ouml;nnen Sie eine vorab generierte Version aus diesem github-<a href="https://github.com/wenming/BigDataSamples/tree/master/mahout">Repository</a> herunterladen. 

Es ist am praktischsten, alles in einer einzigen <a href="https://github.com/wenming/BigDataSamples/archive/master.zip">ZIP-Datei</a> herunterzuladen. Suchen Sie die Dateien &quot;users.txt&quot; und &quot;mInput.txt&quot;, und kopieren Sie sie in den Ordner c:\ des Remoteclusters.</p> 
</div>

An diesem Punkt sollten Sie ein Hadoop-Terminalfenster öffnen und zu dem Ordner mit den Dateien "users.txt" und "mInput.txt" navigieren.

![Mahout-Befehlsfenster][Mahout-Befehlsfenster]

### Hadoop-Befehlsfenster

1.  Kopieren Sie als Nächstes **mInput.txt** und **users.txt** in das HDFS. Hierzu öffnen Sie die **Hadoop-Befehlszeile** und führen die folgenden Befehle aus:

        hadoop dfs -copyFromLocal c:\mInput.txt input\mInput.txt
        hadoop dfs -copyFromLocal c:\users.txt input\users.txt

2.  Überprüfen Sie, ob die Datei in das HDFS kopiert wurden:

        hadoop fs -ls input/

    Folgendes Ergebnis sollte angezeigt werden:

        Found 2 items
        -rwxrwxrwx   1 writer supergroup      53322 2013-03-08 20:32 /user/writer/input/mInput.txt
        -rwxrwxrwx   1 writer supergroup        353 2013-03-08 20:33 /user/writer/input/users.txt

3.  Nun können Sie den Mahout-Job mit dem folgenden Befehl ausführen:

        c:\apps\dist\mahout-0.7\bin>hadoop jar c:\Apps\dist\mahout-0.7\mahout-core-0.7-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob -s SIMILARITY_COOCCURRENCE --input=input/mInput.txt --output=output --usersFile=input/users.txt

    Es gibt noch viele andere "distance"-Funktionen, mit denen das Empfehlungssystem den Featurevektor für verschiedene Benutzer vergleichen kann; experimentieren Sie, und ändern Sie die Similarity-Klasse in SIMILARITY\_COOCCURRENCE, SIMILARITY\_LOGLIKELIHOOD, SIMILARITY\_TANIMOTO\_COEFFICIENT, SIMILARITY\_CITY\_BLOCK, SIMILARITY\_COSINE, SIMILARITY\_PEARSON\_CORRELATION, SIMILARITY\_EUCLIDEAN\_DISTANCE. In diesem Lernprogramm wird nicht näher auf die Möglichkeiten der detaillierten Datenanalyse in Mahout eingegangen.

4.  Die Ausführung des Mahout-Jobs sollte mehrere Minuten dauern, anschließend wird eine Ausgabedatei erstellt. Führen Sie den folgenden Befehl aus, um eine lokale Kopie der Ausgabedatei zu erstellen:

        hadoop fs -copyToLocal output/part-r-00000 c:\output.txt

5.  Öffnen Sie die Datei **output.txt** aus dem Stammordner **c:\\**, und überprüfen Sie ihren Inhalt. Die Datei weist folgende Struktur auf:

        user    [song:rating,song:rating, ...]

6.  Wenn Sie andere Teile von Mahout in Ihrem Cluster verwenden möchten, speichern Sie eine Kopie der Datei "Mahout.cmd" im bin-Verzeichnis der Mahout-Bereitstellung.

## <a name="summary"></a>Zusammenfassung

Empfehlungssysteme sind heute ein wichtiges Hilfsmittel für viele Social Network-, Onlineshopping-, Streaming Media- und andere Internet-Websites. Mahout stellt ein vorkonfiguriertes Empfehlungssystem zur Verfügung, das benutzerfreundlich ist, viele nützliche Features umfasst und auf Hadoop skalierbar ist.

## Nächste Schritte

In diesem Artikel wird die Verwendung der Hadoop-Befehlszeile beschrieben, Sie können die Aufgaben jedoch auch mit der interaktiven HDInsight-Konsole ausführen. Weitere Informationen finden Sie unter [Guidance: HDInsight Interactive JavaScript and Hive Consoles][interactive-console].

  [Million Song Dataset]: http://labrosa.ee.columbia.edu/millionsong/tasteprofile
  [Datensatz]: http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip
  [Setup und Konfiguration]: #setup
  [Überprüfen und Formatieren der Daten]: #segment1
  [Installieren von Mahout]: #Segment2
  [Ausführen des Mahout-Jobs]: #segment2
  [Erste Schritte mit Azure HDInsight]: /de-de/manage/services/hdinsight/get-started-hdinsight/
  [The Echo Nest Taste Profile Subset]: ./media/hdinsight-hadoop-recommendation-engine/the-echo-nest-taste-profile-subset.png
  [Erstellen einer Konsolenanwendung]: ./media/hdinsight-hadoop-recommendation-engine/creating-a-console-application.png
  [Befehlszeilenargumente festlegen]: ./media/hdinsight-hadoop-recommendation-engine/setting-command-line-arguments.png
  [Das Symbol "Cluster verwalten"]: ./media/hdinsight-hadoop-recommendation-engine/the-manage-cluster-icon.png
  [Mahout]: http://mahout.apache.org/
  [Mahout Version 0.7]: http://www.apache.org/dyn/closer.cgi/mahout/
  [Mahout hochladen]: ./media/hdinsight-hadoop-recommendation-engine/uploading-mahout.PNG
  [Mahout-Befehlsfenster]: ./media/hdinsight-hadoop-recommendation-engine/mahout-commandwindow.PNG
