<properties 
	pageTitle="Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio | Azure" 
	description="Erfahren Sie, wie Sie die HDInsight Hadoop-Tools für Visual Studio installieren und nutzen, um eine Verbindung mit HDInsight herzustellen und Hive-Abfragen auszuführen." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="11/12/2014" 
	ms.author="jgao" 
	editor="cgronlun"/>

# Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio

Erfahren Sie, wie Sie die HDInsight Hadoop-Tools für Visual Studio installieren und nutzen, um eine Verbindung mit HDInsight herzustellen und Hive-Abfragen auszuführen. Weitere Informationen zur Verwendung von HDInsight finden Sie unter [Einführung in HDInsight][hdinsight.introduction] und [Erste Schritte mit HDInsight][hdinsight.get.started].

+ [Installation] 
+ [Verbinden mit Ihrem Azure-Abonnement]
+ [Navigieren durch die verknüpften Ressourcen]
+ [Ausführen von Hive-Abfragen]
+ [Nächste Schritte]


##Voraussetzungen

- Eine Arbeitsstation mit folgender Konfiguration

	- Windows 7 oder Windows 8.
	- Visual Studio 2012 mit [Update 4](http://www.microsoft.com/de-de/download/details.aspx?id=39305), Visual Studio 2013 mit [Update 3](http://www.microsoft.com/de-de/download/details.aspx?id=43721) oder [Visual Studio Express 2013](http://www.microsoft.com/de-de/download/details.aspx?id=43722).

	>[AZURE.NOTE] Die Tools sind derzeit nur auf Englisch verfügbar. 


## Installation

Die HDInsight-Tools für Visual Studio gehören zum Funktionsumfang des Microsoft Azure SDK. Das SDK können Sie mit dem [Webplattform-Installer](http://go.microsoft.com/fwlink/?LinkId=255386) installieren.

![HDinsight Tools for Visual Studio Web Platform installer][1]


## Verbinden mit Ihrem Azure-Abonnement
Mithilfe der HDInsight-Tools für Visual Studio können Sie eine Verbindung mit Ihren HDInsight-Clustern herstellen, einfache Verwaltungsvorgänge durchführen und Hive-Abfragen ausführen.

**So verbinden Sie sich mit Ihrem Azure-Abonnement**

1.	Öffnen Sie Visual Studio.
2.	Klicken Sie im Menü **Ansicht** auf **Server-Explorer**, um das Fenster von Server-Explorer zu öffnen.
3.	Erweitern Sie **Azure**, und klicken Sie dann auf **HDInsight-Cluster**. 

	>[AZURE.NOTE]Das Fenster **HDInsight-Aufgabenliste** ist geöffnet. Wenn es nicht angezeigt werden sollte, können Sie es öffnen, indem Sie im Menü **Ansicht** auf **Andere Fenster** und dann auf das Fenster **HDInsight-Aufgabenliste** klicken.  
4.	Geben Sie Ihre Azure-Anmeldeinformationen ein, und klicken Sie auf **Anmelden**. Dies ist nur erforderlich, wenn Sie bislang noch nicht in Visual Studio auf dieser Arbeitsstation eine Verbindung mit dem Azure-Abonnement hergestellt haben.
5.	Im Server-Explorer wird eine Liste vorhandener HDInsight-Cluster angezeigt. Wenn Sie noch keine Cluster haben, können Sie einen über das Verwaltungsportal, Azure PowerShell oder das HDInsight SDK bereitstellen.  Weitere Informationen finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision].

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	Erweitern Sie einen HDInsight-Cluster. Sie sehen dann **Hive-Datenbanken**, das Standardspeicherkonto und verknüpfte Speicherkonten. Sie können die einzelnen Elemente weiter erweitern. 

Nach Verbinden mit Ihrem Azure-Abonnement können Sie die folgenden Aufgaben ausführen:

**So stellen Sie in Visual Studio eine Verbindung mit dem Verwaltungsportal her**

- Erweitern Sie im Server-Explorer erst **Azure**, dann **HDInsight-Cluster**, klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und klicken Sie dann auf **Cluster im Azure-Portal verwalten**.

**So können Sie in Visual Studio Fragen stellen und Feedback geben**

- Klicken Sie im Menü **Extras** auf **HDInsight** und dann auf **MSDN-Forum**, um Fragen zu stellen, oder auf **Feedback geben**.

## Navigieren durch die verknüpften Ressourcen 

Im Server-Explorer werden das Standardspeicherkonto und verknüpfte Speicherkonten angezeigt. Erweitern Sie das Standardspeicherkonto, um die Container im Speicherkonto anzuzeigen. Sowohl das Standardspeicherkonto als auch der Standardcontainer sind markiert. Sie können auch mit der rechten Maustaste auf einen der Container klicken, um seinen Inhalt anzuzeigen.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Ausführen von Hive-Abfragen
[Apache Hive][apache.hive] ist eine Data Warehouse-Infrastruktur, die auf Hadoop aufbaut und Funktionen für Datenzusammenfassung, -abfragen und -analysen bietet. Die HDInsight-Tools für Visual Studio unterstützen das Ausführen von Hive-Abfragen in Visual Studio. Weitere Informationen zu Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight.hive].

###Anzeigen der Hive-Tabelle "hivesampletable"
Alle HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen  *hivesampletable*. Mithilfe dieser Tabelle zeigen wir Ihnen, wie Hive-Tabellen aufgelistet, die Tabellenschemas angezeigt und die Zeilen in der Hive-Tabelle aufgelistet werden.



**So können Sie Hive-Tabellen auflisten und das Hive-Tabellenschema anzeigen**

1.	Erweitern Sie in **Server-Explorer** zunächst **Azure**, dann **HDInsight-Cluster**, dann den Cluster, danach **Hive-Datenbanken**, anschließend **Standard** und schließlich **hivesampletable**, um das Tabellenschema anzuzeigen.
4.	Klicken Sie mit der rechten Maustaste auf **hivesampletable**, und klicken Sie dann auf **Die ersten 100 Zeilen anzeigen**, um die Zeilen aufzulisten. Dies entspricht dem Ausführen der folgenden Hive-Abfrage mithilfe des Hive-ODBC-Treibers:

		SELECT * FROM hivesampletable LIMIT 100

	Sie können die Anzahl der Zeilen anpassen. 
 
	![HDinsight Hive Visual Studio schema query][6]

###Erstellen von Hive-Tabellen

Zum Erstellen einer Hive-Tabelle können Sie entweder die Benutzeroberfläche oder Hive-Abfragen nutzen. Informationen zu Hive-Abfragen finden Sie unter [Ausführen von Hive-Abfragen](#run.queries).

**So erstellen Sie eine Hive-Tabelle**

1. Erweitern Sie in **Server-Explorer** zunächst **Azure**, dann **HDInsight-Cluster**, dann einen HDInsight-Cluster und danach **Hive-Datenbanken**. Klicken Sie mit der rechten Maustaste auf **Standard**, und klicken Sie dann auf **Tabelle erstellen**.
2. Konfigurieren Sie die Tabelle.
3. Klicken Sie auf **Tabelle erstellen**, um den Auftrag zum Erstellen der neuen Hive-Tabelle zu übermitteln.

	![hdinsight visual studio tools create hive table][7]

###<a name="run.queries"></a>Ausführen von Hive-Abfragen
Es gibt zwei Möglichkeiten zum Erstellen und Ausführen von Hive-Abfragen:

- Erstellen von Ad-hoc-Abfragen
- Erstellen einer Hive-Anwendung

**So können Sie Ad-hoc-Abfragen erstellen bzw. ausführen**

1. Erweitern Sie im **Server-Explorer** erst **Azure** und dann **HDInsight-Cluster**.
2. Klicken Sie mit der rechten Maustaste auf den Cluster, in dem Sie die Abfrage ausführen möchten, und klicken Sie dann auf **Hive-Abfrage schreiben**. 
3. Geben Sie Hive-Abfragen ein.
4. Klicken Sie auf **Übermitteln** oder **Übermitteln (Erweitert)**. Mit "Übermitteln (Erweitert)" konfigurieren Sie **Auftragsname**, **Argumente**, **Zusätzliche Konfigurationen** und **Statusverzeichnis** für das Skript:

	![hdinsight hadoop hive query][9]

	Nach Übermittlung des Auftrags wird das Fenster **Hive-Aufträge - Zusammenfassung** angezeigt.

	![hdinsight hadoop hive query][8]
5. Klicken Sie auf die Schaltfläche **Aktualisieren**, um den Auftragsstatus zu aktualisieren, bis sich der Auftragsstatus in **Abgeschlossen** ändert.
6. Klicken Sie unten auf die Links, um **Auftragsabfrage**, **Auftragsausgabe** und **Auftragsprotokoll** anzuzeigen.



**So können Sie eine Hive-Lösung erstellen bzw. ausführen**

1. Klicken Sie im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.
2. Wählen Sie im linken Bereich **HDInsight** und dann im mittleren Bereich **Hive-Anwendung** aus. Geben Sie die Eigenschaften ein, und klicken Sie auf **OK**.
3. Doppelklicken Sie im **Projektmappen-Explorer** auf **Script.hql**, um die Datei zu öffnen.

 
###Anzeigen von Hive-Aufträgen
Sie können für alle Hive-Aufträge die Auftragsabfrage und -ausgabe sowie das Auftragsprotokoll anzeigen.

**So zeigen Sie Hive-Aufträge an**

1. Erweitern Sie im **Server-Explorer** erst **Azure** und dann **HDInsight**. 
2. Klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und klicken Sie dann auf **Hive-Aufträge anzeigen**. Daraufhin wird eine Liste der Hive-Aufträge angezeigt, die im Cluster ausgeführt wurden. 
3. Klicken Sie in der Auftragsliste auf einen Auftrag, um ihn auszuwählen, und öffnen Sie dann im Fenster **Hive-Aufträge - Zusammenfassung** entweder **Auftragsabfrage**, **Auftragsausgabe** oder **Auftragsprotokoll**.

## Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie in Visual Studio eine Verbindung mit HDInsight-Clustern herstellen und Hive-Abfragen ausführen. Weitere Informationen finden Sie unter:

- [Verwenden von Hadoop Hive in HDInsight][hdinsight.hive]
- [Erste Schritte mit Hadoop in HDInsight][hdinsight.get.started]
- [Übermitteln von Hadoop-Aufträgen in HDInsight][hdinsight.submit.jobs]
- [Analysieren von Twitter-Daten mit Hadoop in HDInsight][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Installation]: #installation
[Verbinden mit Ihrem Azure-Abonnement]: #connect-to-your-azure-subscription
[Navigieren durch die verknüpften Ressourcen]: #navigate-the-linked-resources
[Ausführen von Hive-Abfragen]: #run-hive-queries
[Nächste Schritte]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/


[apache.hive]: http://hive.apache.org<!--HONumber=42-->
