<properties 
	pageTitle="Informationen zur Verwendung von Hadoop für Visual Studio-Tools für HDInsight | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Hadoop für Visual Studio-Tools für HDInsight installieren und verwenden, um eine Verbindung mit einem Hadoop-Cluster herzustellen und eine Hive-Abfrage auszuführen." 
	keywords="hadoop tools,hive query,visual studio"
	services="HDInsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="04/08/2015" 
	ms.author="jgao"/>

# Erste Schritte mit den Hadoop-Tools in Visual Studio für HDInsight zum Ausführen einer Hive-Abfrage

Erfahren Sie, wie Sie HDInsight Tools für Visual Studio nutzen, um eine Verbindung mit HDInsight-Clustern herzustellen und Hive-Abfragen zu übermitteln. Weitere Informationen zur Verwendung von HDInsight finden Sie unter [Einführung in HDInsight][hdinsight.introduction] und [Erste Schritte mit HDInsight][hdinsight.get.started]. Weitere Informationen zum Herstellen einer Verbindung mit einem Storm-Cluster finden Sie unter [Entwickeln von C#-Topologien für Apache Storm in HDInsight mit Visual Studio][hdinsight.storm.visual.studio.tools].

>[AZURE.NOTE]Mit der neuesten Version wurden einige neue Funktionen eingeführt, z. B. die IntelliSense-Unterstützung des Hive-Editors, die lokale Hive-Skriptprüfung und der YARN-Protokollzugriff.


## Voraussetzungen

Um dieses Lernprogramm durchzuführen und die Hadoop-Tools in Visual Studio zu verwenden, benötigen Sie Folgendes:

- Eine Arbeitsstation mit folgender Software:

	- Windows 8.1, Windows 8 oder Windows 7
	- Visual Studio (eine der folgenden Versionen):
		- Visual Studio 2012 Professional/Premium/Ultimate mit [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)
		- Visual Studio 2013 Community/Professional/Premium/Ultimate mit [Update 4](https://www.microsoft.com/download/details.aspx?id=44921)
		- Visual Studio 2015 RC (Community/Enterprise)

	>[AZURE.NOTE]Derzeit umfasst der Lieferumfang der HDInsight-Tools für Visual Studio nur die englische Version.


## Installieren von Hadoop-Tools für Visual Studio

HDInsight-Tools für Visual Studio ist Bestandteil des Microsoft Azure SDK für .NET Version 2.5.1 oder höher. Für die Installation können Sie den [Webplattform-Installer](http://go.microsoft.com/fwlink/?LinkId=255386) verwenden. Sie müssen das Paket auswählen, das Ihrer Version von Visual Studio entspricht. Mit dem Paket wird auch der Microsoft Hive-ODBC-Treiber (32 Bit und 64 Bit) installiert.

![Hadoop-Tools: HDInsight-Tools für Visual Studio Webplattform-Installer][1]


>[AZURE.NOTE]Wenn Sie Visual Studio 2015 oder 2012 verwenden und Azure SDK 2.5 installiert ist, müssen Sie die frühere Version manuell entfernen, bevor Sie die neueste Version installieren können. Visual Studio 2013 unterstützt die direkte Aktualisierung.

## Verbinden mit Ihrem Azure-Abonnement
Mithilfe der HDInsight-Tools für Visual Studio können Sie eine Verbindung mit Ihren HDInsight-Clustern herstellen, einfache Verwaltungsvorgänge durchführen und Hive-Abfragen ausführen.

>[AZURE.NOTE]Informationen zum Herstellen einer Verbindung mit dem HDInsight-Emulator finden Sie unter [Erste Schritte mit dem HDInsight-Emulator](../hdinsight-get-started-emulator.md/#vstools).

>[AZURE.NOTE]Informationen zum Herstellen einer Verbindung mit einem generischen Hadoop-Cluster (Vorschau) finden Sie unter [Schreiben und Übermitteln von Hive-Abfragen mit Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).


**So stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her**

1.	Öffnen Sie Visual Studio.
2.	Klicken Sie im Menü **Ansicht** auf **Server-Explorer**, um das Fenster "Server-Explorer" zu öffnen.
3.	Erweitern Sie erst **Azure** und dann **HDInsight**. 

	>[AZURE.NOTE]**HDInsight-Aufgabenliste****Andere Fenster****Ansicht****Fenster "HDInsight-Aufgabenliste"**  
4.	Geben Sie Ihre Azure-Anmeldeinformationen ein, und klicken Sie dann auf **Anmelden**. Dies ist nur erforderlich, wenn Sie auf dieser Arbeitsstation in Visual Studio noch keine Verbindung mit dem Azure-Abonnement hergestellt haben.
5.	Im Server-Explorer wird eine Liste vorhandener HDInsight-Cluster angezeigt. Wenn Sie über keine Cluster verfügen, können Sie einen Cluster über das Azure-Portal, Azure PowerShell oder das HDInsight SDK bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision].

	![Hadoop-Tools: HDInsight-Tools für Visual Studio, Clusterliste Server-Explorer][5]
6.	Erweitern Sie einen HDInsight-Cluster. Es werden **Hive-Datenbanken**, ein Standardspeicherkonto, verknüpfte Speicherkonten und das **Hadoop-Dienstprotokoll** angezeigt. Sie können die einzelnen Elemente weiter erweitern. 

Nach dem Verbinden mit Ihrem Azure-Abonnement können Sie die folgenden Aufgaben ausführen:

**So stellen Sie in Visual Studio eine Verbindung mit dem Azure-Portal her**

- Erweitern Sie im Server-Explorer **Azure** > **HDInsight**, klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und klicken Sie dann auf **Cluster im Azure-Portal verwalten**.

**So können Sie in Visual Studio Fragen stellen und Feedback geben**

- Klicken Sie im Menü **Extras** auf **HDInsight** und dann auf **MSDN-Forum**, um Fragen zu stellen, oder auf **Feedback abgeben**.

## Navigieren durch die verknüpften Ressourcen 

Im Server-Explorer werden das Standardspeicherkonto und alle verknüpften Speicherkonten angezeigt. Wenn Sie das Standardspeicherkonto erweitern, werden die Container im Speicherkonto angezeigt. Das Standardspeicherkonto sowie der Standardcontainer sind markiert. Sie können auch mit der rechten Maustaste auf einen der Container klicken, um seinen Inhalt anzuzeigen.

![HDInsight-Tools für Visual Studio, Clusterliste Server-Explorer][2]

## Ausführen einer Hive-Abfrage
[Apache Hive][apache.hive] ist eine Data Warehouse-Infrastruktur, die auf Hadoop aufbaut und Funktionen für Datenzusammenfassung, -abfragen und -analysen umfasst. Die HDInsight-Tools für Visual Studio unterstützen das Ausführen von Hive-Abfragen in Visual Studio. Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight.hive].

Es ist zeitaufwändig, das Hive-Skript mit einem HDInsight-Cluster zu testen. Es kann mehrere Minuten oder länger dauern. Mit HDInsight-Tools für Visual Studio können Hive-Skripts ohne Verbindung mit einem aktiven Cluster lokal überprüft werden.

Mithilfe von HDInsight-Tools für Visual Studio können Benutzer auch sehen, was sich in einem Hive-Auftrag befindet, indem sie die YARN-Protokolle bestimmter Hive-Aufträge sammeln und anzeigen.

###Anzeigen der Hive-Tabelle **hivesampletable** 
Alle HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen *hivesampletable*. Mithilfe dieser Tabelle zeigen wir Ihnen, wie Hive-Tabellen aufgelistet, die Tabellenschemas angezeigt und die Zeilen in der Hive-Tabelle aufgelistet werden.



**So können Sie Hive-Tabellen auflisten und das Hive-Tabellenschema anzeigen**

1.	Erweitern Sie im **Server-Explorer** die Knoten **Azure** > **HDInsight** > den gewünschten Cluster > **Hive-Datenbanken** > **Standard** > **hivesampletable**, um das Tabellenschema anzuzeigen.
4.	Klicken Sie mit der rechten Maustaste auf **hivesampletable**, und klicken Sie dann auf **View Top 100 Rows**, um die Zeilen aufzulisten. Dies entspricht dem Ausführen der folgenden Hive-Abfrage mithilfe des Hive-ODBC-Treibers:

		SELECT * FROM hivesampletable LIMIT 100

	Sie können die Anzahl der Zeilen anpassen.
 
	![Hadoop-Tools: HDInsight Hive für Visual Studio, Schemaabfrage][6]

###Erstellen von Hive-Tabellen

Hive-Tabellen können Sie entweder über die Benutzeroberfläche oder mithilfe von Hive-Abfragen erstellen. Informationen zur Verwendung von Hive-Abfragen finden Sie unter [Ausführen von Hive-Abfragen](#run.queries).

**So erstellen Sie eine Hive-Tabelle**

1. Erweitern Sie im **Server-Explorer** die Knoten **Azure** > **HDInsight-Cluster**, einen HDInsight-Cluster und dann **Hive-Datenbanken**. Klicken Sie mit der rechten Maustaste auf **Standard**, und klicken Sie dann auf **Tabelle erstellen**.
2. Konfigurieren Sie die Tabelle.
3. Klicken Sie auf **Tabelle erstellen**, um den Auftrag zum Erstellen der neuen Hive-Tabelle zu übermitteln.

	![Hadoop-Tools: HDInsight Visual Studio-Tools, Hive-Tabelle erstellen][7]

###<a name="run.queries"></a>Überprüfen und Ausführen von Hive-Abfragen
Es gibt zwei Möglichkeiten zum Erstellen und Ausführen von Hive-Abfragen:

- Erstellen von Ad-hoc-Abfragen
- Erstellen von Hive-Anwendungen

**So gehen Sie vor, um Ad-hoc-Abfragen zu erstellen, zu überprüfen und auszuführen**

1. Erweitern Sie im **Server-Explorer** erst **Azure** und dann **HDInsight-Cluster**.
2. Klicken Sie mit der rechten Maustaste auf den Cluster, in dem Sie die Abfrage ausführen möchten, und klicken Sie dann auf **Write a Hive Query**. 
3. Geben Sie die Hive-Abfragen ein. Beachten Sie, dass der Hive-Editor IntelliSense unterstützt. HDInsight-Tools für Visual Studio unterstützt das Laden der Remotemetadaten, wenn Sie Ihr Hive-Skript bearbeiten. Bei der Eingabe von "SELECT * FROM" werden von IntelliSense beispielsweise die vorgeschlagenen Tabellennamen aufgelistet. Wenn ein Tabellenname angegeben wird, werden die Spaltennamen von IntelliSense aufgeführt. 

	![Hadoop-Tools: HDInsight Visual Studio-Tools, IntelliSense][13]

	![Hadoop-Tools: HDInsight Visual Studio-Tools, IntelliSense][14]

	> [AZURE.NOTE] Nur die Metadaten der Cluster, die in HDInsight-Symbolleiste ausgewählt ist, wird vorgeschlagen.
4. (Optional) Klicken Sie auf **Skript überprüfen**, um die Syntaxfehler des Skripts zu überprüfen.

	![Hadoop-Tools: HDInsight-Tools für Visual Studio, lokale Überprüfung][10]

4. Klicken Sie auf **Übermitteln** oder **Übermitteln (Erweitert)**. Mit "Übermitteln (Erweitert)" konfigurieren Sie **Auftragsname**, **Argumente**, **Zusätzliche Konfigurationen** und **Statusverzeichnis** für das Skript:

	![HDInsight Hadoop, Hive-Abfrage][9]

	Nach Übermittlung des Auftrags wird das Fenster **Hive-Aufträge – Zusammenfassung** angezeigt.

	![Zusammenfassung einer Hive-Abfrage für HDInsight Hadoop][8]
5. Klicken Sie auf die Schaltfläche **Aktualisieren**, um den Auftragsstatus zu aktualisieren, bis sich der Auftragsstatus in **Abgeschlossen** ändert.
6. Klicken Sie unten auf die Links, um **Auftragsabfrage**, **Auftragsausgabe**, **Auftragsprotokoll** oder **YARN-Protokoll** anzuzeigen.



**So gehen Sie vor, um eine Hive-Lösung zu erstellen und auszuführen**

1. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.
2. Wählen Sie im linken Bereich die Option **HDInsight** und dann im mittleren Bereich die Option **Hive-Anwendung** aus. Geben Sie die Eigenschaften ein, und klicken Sie dann auf **OK**.

	![Hadoop-Tools: HDInsight Visual Studio-Tools, neues Hive-Projekt][11]
3. Doppelklicken Sie im **Projektmappen-Explorer** auf **Script.hql**, um die Datei zu öffnen. 
4. Um das Hive-Skript zu überprüfen, können Sie entweder auf die Schaltfläche **Skript überprüfen** klicken oder im Hive-Editor mit der rechten Maustaste auf das Skript klicken und anschließend im Kontextmenü die Option **Skript überprüfen** auswählen.

 
###Anzeigen von Hive-Aufträgen
Sie können Auftragsabfragen, die Auftragsausgabe, Auftragsprotokolle und YARN-Protokolle für Hive-Aufträge anzeigen. Weitere Informationen finden Sie im vorherigen Screenshot.

Mit der neuesten Version des Tools können Sie sehen, was sich in Ihren Hive-Aufträgen befindet, indem Sie die YARN-Protokolle sammeln und anzeigen. Das YARN-Protokoll kann Ihnen bei der Untersuchung von Leistungsproblemen helfen. Weitere Informationen zur Vorgehensweise von HDInsight beim Sammeln von YARN-Protokollen finden Sie unter [Programmgesteuerter Zugriff auf HDInsight-Anwendungsprotokolle][hdinsight.access.application.logs].

**So zeigen Sie Hive-Aufträge an**

1. Erweitern Sie im **Server-Explorer** erst **Azure** und dann **HDInsight**. 
2. Klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und klicken Sie dann auf **Hive-Aufträge anzeigen**. Daraufhin wird eine Liste der Hive-Aufträge angezeigt, die im Cluster ausgeführt wurden. 
3. Klicken Sie in der Auftragsliste auf einen Auftrag, um ihn auszuwählen, und öffnen Sie dann im Fenster **Hive-Aufträge – Zusammenfassung** entweder die Option **Auftragsabfrage**, **Auftragsausgabe**, **Auftragsprotokoll** oder **YARN-Protokoll**.

	![Hadoop-Tools: HDInsight Visual Studio-Tools, Hive-Aufträge anzeigen][12]
## Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie mit dem Hadoop Tools Package in Visual Studio eine Verbindung mit HDInsight-Clustern herstellen und Hive-Abfragen ausführen. Weitere Informationen finden Sie unter:

- [Verwenden von Hadoop Hive in HDInsight][hdinsight.hive]
- [Erste Schritte mit Hadoop in HDInsight][hdinsight.get.started]
- [Übermitteln von Hadoop-Aufträgen in HDInsight][hdinsight.submit.jobs]
- [Analysieren von Twitter-Daten mit Hadoop in HDInsight][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Installation]: #installation
[Connect to your Azure subscription]: #connect-to-your-azure-subscription
[Navigate the linked resources]: #navigate-the-linked-resources
[Run Hive queries]: #run-hive-queries
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png

<!--Link references-->
[hdinsight-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight.introduction]: ../hdinsight-introduction.md
[hdinsight.get.started]: ../hdinsight-get-started.md
[hdinsight.hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight.submit.jobs]: ../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: ../hdinsight/hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: ../hdinsight/hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org

<!--HONumber=54--> 