<properties 
	pageTitle="Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio | Azure" 
	description="Erfahren Sie, wie Sie die HDInsight Hadoop-Tools für Visual Studio installieren und nutzen, um eine Verbindung mit HDInsight herzustellen und Hive-Abfragen auszuführen." 
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
	ms.date="2/17/2015" 
	ms.author="jgao"/>

# Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio

Erfahren Sie, wie Sie HDInsight Tools für Visual Studio nutzen, um eine Verbindung mit HDInsight-Clustern herzustellen und Hive-Abfragen zu übermitteln. Weitere Informationen zur Verwendung von HDInsight finden Sie unter [Einführung in HDInsight][hdinsight.introduction] und [Erste Schritte mit HDInsight][hdinsight.get.started]. Weitere Informationen zum Herstellen einer Verbindung mit einem Storm-Cluster finden Sie unter [Entwickeln von C#-Topologien für Apache Storm in HDInsight mit Visual Studio][hdinsight.storm.visual.studio.tools]. 

>[WACOM.NOTE] Alle Features der HDInsight Tools, die für Windows-Cluster verfügbar sind, funktionieren auch in Linux-Clustern, mit Ausnahme von ein paar Features, die in Humboldt heute nicht unterstützt werden, z. B. die Anzeige von YARN-Protokollen eines bestimmten Hive-Auftrags. 

>Die neueste Version verfügt über einige neue Features, z. B. die Intellisense-Unterstützung des Hive-Editors, die lokale Hive-Skriptprüfung und der Zugriff auf YARN-Protokolle.

## Themen in diesem Artikel
+ [Installation] 
+ [Verbinden mit Ihrem Azure-Abonnement]
+ [Navigieren durch die verknüpften Ressourcen]
+ [Ausführen von Hive-Abfragen]
+ [Nächste Schritte]


## Voraussetzungen

- Eine Arbeitsstation mit folgender Konfiguration

	- Windows 7, Windows 8 oder Windows 8.1
	- Visual Studio mit den folgenden Versionen:
		- Visual Studio 2012 Professional/Premium/Ultimate mit [Update 4](http://www.microsoft.com/de-de/download/details.aspx?id=39305)
		- Visual Studio 2013 Professional/Premium/Ultimate mit [Update 4](https://www.microsoft.com/de-de/download/details.aspx?id=44921)
		- Visual Studio 2015 Preview

	>[WACOM.NOTE] Die Tools sind derzeit nur auf Englisch verfügbar. 


## Installation

HDInsight Tools für Visual Studio können mit dem [Webplattform-Installer](http://go.microsoft.com/fwlink/?LinkId=255386) installiert werden. Sie können die Tools mit dem Schlüsselwort "hdinsight" abfragen. Für jede Visual Studio-Version ist ein Paket vorhanden. Sie müssen das Paket auswählen, das Ihrer Version von Visual Studio entspricht.  Mit dem Paket werden auch die Microsoft Hive ODBC-Treiber in 32-Bit und 64-Bit installiert.

![HDinsight Tools for Visual Studio Web Platform installer][1]


>[WACOM.NOTE] Wenn Sie Visual Studio 2012 oder 2015 verwenden und die ältere Version des Azure-SDK installiert ist, müssen Sie die frühere Version manuell entfernen, bevor Sie die neueste Version installieren können. Visual Studio 2013 unterstützt die direkte Aktualisierung.

## Verbinden mit Ihrem Azure-Abonnement
Mithilfe der HDInsight-Tools für Visual Studio können Sie eine Verbindung mit Ihren HDInsight-Clustern herstellen, einfache Verwaltungsvorgänge durchführen und Hive-Abfragen ausführen.

**So verbinden Sie sich mit Ihrem Azure-Abonnement**

1.	Öffnen Sie Visual Studio.
2.	Klicken Sie im Menü **Ansicht** auf **Server-Explorer**, um das Fenster von Server-Explorer zu öffnen.
3.	Erweitern Sie erst **Azure** und dann **HDInsight**. 

	>[WACOM.NOTE]Das Fenster **HDInsight-Aufgabenliste** ist geöffnet. Wenn es nicht angezeigt werden sollte, können Sie es öffnen, indem Sie im Menü **Ansicht** auf **Andere Fenster** und dann auf das Fenster **HDInsight-Aufgabenliste** klicken.  
4.	Geben Sie Ihre Azure-Anmeldeinformationen ein, und klicken Sie auf **Anmelden**. Dies ist nur erforderlich, wenn Sie bislang noch nicht in Visual Studio auf dieser Arbeitsstation eine Verbindung mit dem Azure-Abonnement hergestellt haben.
5.	Im Server-Explorer wird eine Liste vorhandener HDInsight-Cluster angezeigt. Wenn Sie noch keine Cluster haben, können Sie einen über das Verwaltungsportal, Azure PowerShell oder das HDInsight SDK bereitstellen.  Weitere Informationen finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision].

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	Erweitern Sie einen HDInsight-Cluster. Es werden **Hive-Datenbanken**, das Standardspeicherkonto, verknüpfte Speicherkonten und das **Hadoop-Dienstprotokoll** angezeigt. Sie können die einzelnen Elemente weiter erweitern. 

Nach Verbinden mit Ihrem Azure-Abonnement können Sie die folgenden Aufgaben ausführen:

**So stellen Sie in Visual Studio eine Verbindung mit dem Verwaltungsportal her**

- Erweitern Sie im Server-Explorer erst **Azure**, dann **HDInsight**, klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und klicken Sie dann auf **Cluster im Azure-Portal verwalten**.

**So können Sie in Visual Studio Fragen stellen und Feedback geben**

- Klicken Sie im Menü **Extras** auf **HDInsight** und dann auf **MSDN-Forum**, um Fragen zu stellen, oder auf **Feedback geben**.

## Navigieren durch die verknüpften Ressourcen 

Im Server-Explorer werden das Standardspeicherkonto und verknüpfte Speicherkonten angezeigt. Erweitern Sie das Standardspeicherkonto, um die Container im Speicherkonto anzuzeigen. Sowohl das Standardspeicherkonto als auch der Standardcontainer sind markiert. Sie können auch mit der rechten Maustaste auf einen der Container klicken, um seinen Inhalt anzuzeigen.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Ausführen von Hive-Abfragen
[Apache Hive][apache.hive] ist eine Data Warehouse-Infrastruktur, die auf Hadoop aufbaut und Funktionen für Datenzusammenfassung, -abfragen und -analysen bietet. Die HDInsight-Tools für Visual Studio unterstützen das Ausführen von Hive-Abfragen in Visual Studio. Weitere Informationen zu Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight.hive].

Es ist zeitaufwändig, das Hive-Skript mit einem HDInsight-Cluster zu testen. Es kann mehrere Minuten oder länger dauern.  Die HDInsight Tools für Visual Studio können die Grammatik von Hive-Skripts ohne Verbindung mit einem aktiven Cluster lokal überprüfen.

Mithilfe der HDInsight Tools für Visual Studio können Benutzer auch anzeigen, was sich innerhalb des Hive-Auftrags befindet, indem sie die YARN-Protokolle bestimmter Hive-Aufträge sammeln und anzeigen.

### Anzeigen der Hive-Tabelle "hivesampletable"
Alle HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen  *hivesampletable*. Mithilfe dieser Tabelle zeigen wir Ihnen, wie Hive-Tabellen aufgelistet, die Tabellenschemas angezeigt und die Zeilen in der Hive-Tabelle aufgelistet werden.



**So können Sie Hive-Tabellen auflisten und das Hive-Tabellenschema anzeigen**

1.	Erweitern Sie in **Server-Explorer** zunächst **Azure**, dann **HDInsight**, dann einen Cluster, danach **Hive-Datenbanken**, anschließend **Standard** und schließlich **hivesampletable**, um das Tabellenschema anzuzeigen.
4.	Klicken Sie mit der rechten Maustaste auf **hivesampletable**, und klicken Sie dann auf **Die ersten 100 Zeilen anzeigen**, um die Zeilen aufzulisten. Dies entspricht dem Ausführen der folgenden Hive-Abfrage mithilfe des Hive-ODBC-Treibers:

		SELECT * FROM hivesampletable LIMIT 100

	Sie können die Anzahl der Zeilen anpassen. 
 
	![HDinsight Hive Visual Studio schema query][6]

### Erstellen von Hive-Tabellen

Zum Erstellen einer Hive-Tabelle können Sie entweder die Benutzeroberfläche oder Hive-Abfragen nutzen. Informationen zu Hive-Abfragen finden Sie unter [Ausführen von Hive-Abfragen](#run.queries).

**So erstellen Sie eine Hive-Tabelle**

1. Erweitern Sie in **Server-Explorer** zunächst **Azure**, dann **HDInsight-Cluster**, dann einen HDInsight-Cluster und danach **Hive-Datenbanken**. Klicken Sie mit der rechten Maustaste auf **Standard**, und klicken Sie dann auf **Tabelle erstellen**.
2. Konfigurieren Sie die Tabelle.
3. Klicken Sie auf **Tabelle erstellen**, um den Auftrag zum Erstellen der neuen Hive-Tabelle zu übermitteln.

	![hdinsight visual studio tools create hive table][7]

### <a name="run.queries"></a>Überprüfen und Ausführen von Hive-Abfragen
Es gibt zwei Möglichkeiten zum Erstellen und Ausführen von Hive-Abfragen:

- Erstellen von Ad-hoc-Abfragen
- Erstellen einer Hive-Anwendung

**So gehen Sie vor, um Ad-hoc-Abfragen zu erstellen, zu überprüfen und auszuführen**

1. Erweitern Sie im **Server-Explorer** erst **Azure** und dann **HDInsight-Cluster**.
2. Klicken Sie mit der rechten Maustaste auf den Cluster, in dem Sie die Abfrage ausführen möchten, und klicken Sie dann auf **Hive-Abfrage schreiben**. 
3. Geben Sie Hive-Abfragen ein. Beachten Sie, dass der Hive-Editor Intellisense unterstützt.
4. (Optional) Klicken Sie auf **Skript überprüfen**, um die Syntaxfehler des Skripts zu überprüfen.

	![hdinsight tools for Visual Studio local validation][10]

4. Klicken Sie auf **Übermitteln** oder **Übermitteln (Erweitert)**. Mit "Übermitteln (Erweitert)" konfigurieren Sie **Auftragsname**, **Argumente**, **Zusätzliche Konfigurationen** und **Statusverzeichnis** für das Skript:

	![hdinsight hadoop hive query][9]

	Nach Übermittlung des Auftrags wird das Fenster **Hive-Aufträge - Zusammenfassung** angezeigt.

	![hdinsight hadoop hive query][8]
5. Klicken Sie auf die Schaltfläche **Aktualisieren**, um den Auftragsstatus zu aktualisieren, bis sich der Auftragsstatus in **Abgeschlossen** ändert.
6. Klicken Sie unten auf die Links, um **Auftragsabfrage**, **Auftragsausgabe**, **Auftragsprotokoll** oder **YARN-Protokoll** anzuzeigen.



**So können Sie eine Hive-Lösung erstellen bzw. ausführen**

1. Klicken Sie im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.
2. Wählen Sie im linken Bereich **HDInsight** und dann im mittleren Bereich **Hive-Anwendung** aus. Geben Sie die Eigenschaften ein, und klicken Sie auf **OK**.
3. Doppelklicken Sie im **Projektmappen-Explorer** auf **Script.hql**, um die Datei zu öffnen. 
4. Um das Hive-Skript zu überprüfen, können Sie entweder auf die Schaltfläche "Skript überprüfen" klicken oder im Hive-Editor mit der rechten Maustaste auf das Skript klicken und anschließend im Kontextmenü die Option "Skript überprüfen" auswählen.

 
### Anzeigen von Hive-Aufträgen
Sie können die Auftragsabfrage, Auftragsausgabe, das Auftragsprotokoll und das YARN-Protokoll für Hive-Aufträge anzeigen.  Betrachten Sie hierzu den vorherigen Screenshot.

Die neueste Version des Tools ermöglicht es Ihnen zu sehen, was sich innerhalb der Hive-Aufträge befindet, indem Sie die YARN-Protokolle sammeln und anzeigen. Das YARN-Protokoll kann Ihnen bei der Untersuchung von Leistungsproblemen helfen. Weitere Informationen zur Vorgehensweise von HDInsight beim Sammeln von YARN-Protokollen finden Sie unter [Programmgesteuerter Zugriff auf HDInsight-Anwendungsprotokolle][hdinsight.access.application.logs].

**So zeigen Sie Hive-Aufträge an**

1. Erweitern Sie im **Server-Explorer** erst **Azure** und dann **HDInsight**. 
2. Klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und klicken Sie dann auf **Hive-Aufträge anzeigen**. Daraufhin wird eine Liste der Hive-Aufträge angezeigt, die im Cluster ausgeführt wurden. 
3. Klicken Sie in der Auftragsliste auf einen Auftrag, um ihn auszuwählen, und öffnen Sie dann im Fenster **Hive-Aufträge - Zusammenfassung** entweder **Auftragsabfrage**, **Auftragsausgabe**, **Auftragsprotokoll** oder **YARN-Protokoll**.

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
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/
[hdinsight.storm.visual.studio.tools]: ../hdinsight-storm-develop-csharp-visual-studio-topology/
[hdinsight.access.application.logs]: ../hdinsight-hadoop-access-yarn-app-logs/

[apache.hive]: http://hive.apache.org
<!--HONumber=45--> 
