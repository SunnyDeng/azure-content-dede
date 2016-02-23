<properties
	pageTitle="Informationen zur Verwendung von Hadoop-Tools für Visual Studio für HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Hadoop-Tools für Visual Studio für HDInsight verwendet werden, um eine Verbindung mit einem Hadoop-Cluster herzustellen und eine Hive-Abfrage auszuführen."
	keywords="hadoop tools,hive query,visual studio"
	services="HDInsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="01/27/2016"
	ms.author="jgao"/>

# Erste Schritte bei der Verwendung von Hadoop-Tools für Visual Studio für HDInsight zum Ausführen einer Hive-Abfrage

Erfahren Sie, wie Sie HDInsight Tools für Visual Studio nutzen, um eine Verbindung mit HDInsight-Clustern herzustellen und Hive-Abfragen zu übermitteln. Weitere Informationen zur Verwendung von HDInsight finden Sie unter [Einführung in HDInsight][hdinsight.introduction] und [Erste Schritte mit HDInsight][hdinsight.get.started]. Weitere Informationen zum Herstellen einer Verbindung mit einem Storm-Cluster finden Sie unter [Entwickeln von C#-Topologien für Apache Storm in HDInsight mit Visual Studio][hdinsight.storm.visual.studio.tools].

**Voraussetzungen**

Um dieses Tutorial durchzuführen und die Hadoop-Tools in Visual Studio zu verwenden, benötigen Sie Folgendes:

- Einen Azure HDInsight-Cluster: Ein Linux-basierter oder ein Windows-basierter Cluster kann für die Schritte in diesem Dokument genutzt werden. In den folgenden Dokumenten finden Sie Informationen zum Erstellen eines Clusters:

	- [Erste Schritte mit Linux-basiertem HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
	- [Erste Schritte mit Windows-basiertem HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md)

- Eine Arbeitsstation mit folgender Software:

	- Windows 8.1, Windows 8 oder Windows 7
	- Visual Studio (eine der folgenden Versionen):
		- Visual Studio 2013 Community/Professional/Premium/Ultimate mit [Update 4](https://www.microsoft.com/download/details.aspx?id=44921)
		- Visual Studio 2015 (Community/Enterprise)

	>[AZURE.NOTE] Derzeit werden die HDInsight-Tools für Visual Studio nur mit der englischen Version bereitgestellt.


## Installieren von HDInsight-Tools für Visual Studio

HDInsight-Tools für Visual Studio und Microsoft Hive ODBC Driver gehören zum Microsoft Azure SDK für .NET, Version 2.5.1 oder höher. Sie können dies mit dem [Web Platform-Installer](http://go.microsoft.com/fwlink/?LinkId=255386) installieren. Sie müssen die Auswahl entsprechend Ihrer Version von Visual Studio vornehmen. Wenn Sie Visual Studio nicht installiert haben, können Sie das aktuelle Visual Studio Community und Azure SDK über den [Webplattform-Installer](http://go.microsoft.com/fwlink/?LinkId=255386) oder die folgenden Links installieren:

- [Visual Studio Community 2015 mit Microsoft Azure SDK](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2015CommunityAzurePack.appids) 
- [Visual Studio Community 2013 mit Microsoft Azure SDK](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2013CommunityAzurePack.appids) 
- [Microsoft Azure SDK für .NET (VS 2015)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2015AzurePack.appids) 
- [Microsoft Azure SDK für .NET (VS 2013)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2013AzurePack.appids) 

![Hadoop-Tools: HDInsight-Tools für Visual Studio – Webplattform-Installer][1]

## Herstellen einer Verbindung mit Azure-Abonnements
Mithilfe der HDInsight-Tools für Visual Studio können Sie eine Verbindung mit Ihren HDInsight-Clustern herstellen, einfache Verwaltungsvorgänge durchführen und Hive-Abfragen ausführen.

>[AZURE.NOTE] Informationen zum Herstellen einer Verbindung mit einem generischen Hadoop-Cluster finden Sie unter [Schreiben und Übermitteln von Hive-Abfragen mithilfe von Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).


**Verbinden mit Ihrem Azure-Abonnement**

1.	Öffnen Sie Visual Studio.
2.	Klicken Sie im Menü **Ansicht** auf **Server-Explorer**, um das Fenster "Server-Explorer" zu öffnen.
3.	Erweitern Sie erst **Azure** und dann **HDInsight**.

	>[AZURE.NOTE]Beachten Sie, dass das Fenster **HDInsight-Aufgabenliste** geöffnet sein sollte. Wenn es nicht angezeigt werden sollte, klicken Sie im Menü **Ansicht** auf **Andere Fenster** und dann auf das Fenster **HDInsight-Aufgabenliste**.  
4.	Geben Sie die Anmeldeinformationen für Ihr Azure-Abonnement ein, und klicken Sie auf **Anmelden**. Dies ist nur erforderlich, wenn Sie bislang noch nicht in Visual Studio auf dieser Arbeitsstation eine Verbindung mit dem Azure-Abonnement hergestellt haben.
5.	Im Server-Explorer wird eine Liste vorhandener HDInsight-Cluster angezeigt. Wenn Sie noch keine Cluster haben, können Sie einen über das Azure-Portal, Azure PowerShell oder das HDInsight SDK bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision].

	![Hadoop-Tools: HDInsight-Tools für Visual Studio – Clusterliste in Server-Explorer][5]
6.	Erweitern Sie einen HDInsight-Cluster. **Hive-Datenbanken**, das Standardspeicherkonto, verknüpfte Speicherkonten und das **Hadoop-Dienstprotokoll** werden angezeigt. Sie können die einzelnen Elemente weiter erweitern.

Nach dem Verbinden mit Ihrem Azure-Abonnement können Sie die folgenden Aufgaben ausführen:

**So stellen Sie in Visual Studio eine Verbindung mit dem Azure-Portal her**

- Erweitern Sie im Server-Explorer erst **Azure**, dann **HDInsight**, klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und klicken Sie dann auf **Cluster im Azure-Portal verwalten**.

**So können Sie in Visual Studio Fragen stellen und Feedback geben**

- Klicken Sie im Menü **Extras** auf **HDInsight** und dann auf **MSDN-Forum**, um Fragen zu stellen, oder auf **Feedback geben**.

## Navigieren durch die verknüpften Ressourcen

Im Server-Explorer werden das Standardspeicherkonto und verknüpfte Speicherkonten angezeigt. Wenn Sie das Standardspeicherkonto erweitern, werden die Container im Speicherkonto angezeigt. Das Standardspeicherkonto und der Standardcontainer sind markiert. Sie können auch mit der rechten Maustaste auf einen der Container klicken, um seinen Inhalt anzuzeigen.

![HDInsight-Tools für Visual Studio – Clusterliste in Server-Explorer][2]

## Ausführen einer Hive-Abfrage
[Apache Hive][apache.hive] ist eine Data Warehouse-Infrastruktur, die auf Hadoop aufbaut und Funktionen für Datenzusammenfassung, -abfragen und -analysen bietet. Die HDInsight-Tools für Visual Studio unterstützen das Ausführen von Hive-Abfragen in Visual Studio. Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight.hive].

Es ist zeitaufwändig, das Hive-Skript mit einem HDInsight-Cluster zu testen. Es kann mehrere Minuten oder länger dauern. HDInsight-Tools für Visual Studio können Hive-Skripts ohne Verbindung mit einem aktiven Cluster lokal überprüfen.

Mithilfe der HDInsight-Tools für Visual Studio können Benutzer auch anzeigen, was sich innerhalb des Hive-Auftrags befindet, indem sie die YARN-Protokolle bestimmter Hive-Aufträge sammeln und anzeigen.

### Anzeigen von **hivesampletable**
HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen *hivesampletable*. Mithilfe dieser Tabelle zeigen wir Ihnen, wie Hive-Tabellen aufgelistet, die Tabellenschemas angezeigt und die Zeilen in der Hive-Tabelle aufgelistet werden.



**So können Sie Hive-Tabellen auflisten und das Hive-Tabellenschema anzeigen**

1.	Erweitern Sie in **Server-Explorer** nacheinander **Azure** > **HDInsight** > den Cluster Ihrer Wahl > **Hive-Datenbanken** > **Standard** > **hivesampletable**, um das Tabellenschema anzuzeigen.
4.	Klicken Sie mit der rechten Maustaste auf **hivesampletable**, und klicken Sie dann auf **Die ersten 100 Zeilen anzeigen**, um die Zeilen aufzulisten. Dies entspricht dem Ausführen der folgenden Hive-Abfrage mithilfe des Hive-ODBC-Treibers:

		SELECT * FROM hivesampletable LIMIT 100

	Sie können die Anzahl der Zeilen anpassen.

	![Hadoop-Tools: HDinsight Hive Visual Studio-Schemaabfrage][6]

### Erstellen von Hive-Tabellen

Zum Erstellen einer Hive-Tabelle können Sie die Benutzeroberfläche oder Hive-Abfragen nutzen. Informationen zum Verwenden von Hive-Abfragen finden Sie unter [Ausführen von Hive-Abfragen](#run.queries).

**So erstellen Sie eine Hive-Tabelle**

1. Erweitern Sie in **Server-Explorer** nacheinander **Azure** > **HDInsight-Cluster** > einen HDInsight-Cluster > **Hive-Datenbanken**. Klicken Sie mit der rechten Maustaste auf **Standard**, und klicken Sie dann auf **Tabelle erstellen**.
2. Konfigurieren Sie die Tabelle.
3. Klicken Sie auf **Tabelle erstellen**, um den Auftrag zum Erstellen der neuen Hive-Tabelle zu übermitteln.

	![Hadoop-Tools: HDInsight-Tools für Visual Studio-Tools – Hive-Tabelle erstellen][7]

### <a name="run.queries"></a>Überprüfen und Ausführen von Hive-Abfragen
Es gibt zwei Möglichkeiten zum Erstellen und Ausführen von Hive-Abfragen:

- Erstellen von Ad-hoc-Abfragen
- Erstellen einer Hive-Anwendung

**So gehen Sie vor, um Ad-hoc-Abfragen zu erstellen, zu überprüfen und auszuführen**

1. Erweitern Sie in **Server-Explorer** erst **Azure** und dann **HDInsight-Cluster**.
2. Klicken Sie mit der rechten Maustaste auf den Cluster, in dem Sie die Abfrage ausführen möchten, und klicken Sie dann auf **Hive-Abfrage schreiben**.
3. Geben Sie die Hive-Abfragen ein. Beachten Sie, dass der Hive-Editor IntelliSense unterstützt. HDInsight-Tools für Visual Studio unterstützen das Laden von Remotemetadaten, wenn Sie Ihr Hive-Skript bearbeiten. Beispiel: Bei Eingabe von „SELECT * FROM“ listet IntelliSense alle vorgeschlagenen Tabellennamen auf. Wenn ein Tabellenname angegeben wird, werden die Spaltennamen von IntelliSense aufgeführt. Das Tool unterstützt fast alle Hive-DML-Anweisungen, Unterabfragen und die integrierten UDFs.

	![Hadoop-Tools: HDInsight-Tools für Visual Studio – IntelliSense][13]

	![Hadoop-Tools: HDInsight-Tools für Visual Studio – IntelliSense][14]

	> [AZURE.NOTE] Nur die Metadaten der Cluster, die in der HDInsight-Symbolleiste ausgewählt sind, werden vorgeschlagen.
4. (Optional): Klicken Sie auf **Skript überprüfen**, um die Syntaxfehler des Skripts zu überprüfen.

	![Hadoop-Tools: HDInsight-Tools für Visual Studio – lokale Überprüfung][10]

4. Klicken Sie auf **Übermitteln** oder **Übermitteln (Erweitert)**. Mit der Option "Übermitteln (Erweitert)" konfigurieren Sie **Auftragsname**, **Argumente**, **Zusätzliche Konfigurationen** und **Statusverzeichnis** für das Skript:

	![HDInsight Hadoop Hive-Abfrage][9]

	Nachdem der Auftrag übermittelt wurde, sehen Sie ein Fenster **Hive-Aufträge – Zusammenfassung**.

	![Zusammenfassung einer HDInsight Hadoop Hive-Abfrage][8]
5. Klicken Sie auf die Schaltfläche **Aktualisieren**, um den Status zu aktualisieren, bis sich der Auftragsstatus in **Abgeschlossen** ändert.
6. Klicken Sie unten auf die Links, um Folgendes anzuzeigen: **Auftragsabfrage**, **Auftragsausgabe**, **Auftragsprotokoll** oder **YARN-Protokoll** anzuzeigen.



**So erstellen Sie eine Hive-Lösung und führen sie aus**

1. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.
2. Wählen Sie im linken Bereich **HDInsight** und dann im mittleren Bereich **Hive-Anwendung** aus. Geben Sie die Eigenschaften ein, und klicken Sie auf **OK**.

	![Hadoop-Tools: HDInsight-Tools für Visual Studio – neues Hive-Projekt][11]
3. Doppelklicken Sie im **Projektmappen-Explorer** auf **Script.hql**, um die Datei zu öffnen.
4. Um das Hive-Skript zu überprüfen, können Sie entweder auf die Schaltfläche **Skript überprüfen** klicken oder im Hive-Editor mit der rechten Maustaste auf das Skript klicken und anschließend im Kontextmenü die Option **Skript überprüfen** auswählen.


### Anzeigen von Hive-Aufträgen
Sie können Auftragsabfragen, die Auftragsausgabe, Auftragsprotokolle und YARN-Protokolle für Hive-Aufträge anzeigen. Weitere Informationen finden Sie im vorherigen Screenshot.

Die neueste Version des Tools ermöglicht es Ihnen zu sehen, was sich innerhalb der Hive-Aufträge befindet, indem Sie die YARN-Protokolle sammeln und anzeigen. Ein YARN-Protokoll kann Ihnen bei der Untersuchung von Leistungsproblemen helfen. Weitere Informationen zur Vorgehensweise von HDInsight beim Erfassen von YARN-Protokollen finden Sie unter [Programmgesteuerter Zugriff auf HDInsight-Anwendungsprotokolle][hdinsight.access.application.logs].

**So zeigen Sie Hive-Aufträge an**

1. Erweitern Sie in **Server-Explorer** erst **Azure** und dann **HDInsight**.
2. Klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und klicken Sie dann auf **Aufträge anzeigen**. Daraufhin wird eine Liste der Hive-Aufträge angezeigt, die im Cluster ausgeführt wurden.
3. Klicken Sie in der Auftragsliste auf einen Auftrag, um ihn auszuwählen, und öffnen Sie dann im Fenster **Hive-Aufträge – Zusammenfassung** entweder **Auftragsabfrage**, **Auftragsausgabe**, **Auftragsprotokoll** oder **YARN-Protokoll**.

	![Hadoop-Tools: HDInsight-Tools für Visual Studio – Hive-Aufträge anzeigen][12]

### Schnellerer Weg zur Hive-Ausführung über HiveServer2

>[AZURE.NOTE] Dieses Feature funktioniert nur mit HDInsight-Clustern ab Version 3.2.

Die HDInsight-Tools haben bisher Hive-Aufträge via WebHCat (auch bekannt als Templeton) übermittelt. Es dauerte sehr lange, bis Auftragsdetails und Fehlerinformationen zurückgegeben wurden. Um dieses Leistungsproblem zu beheben, führen die HDInsight-Tools Hive-Aufträge jetzt über HiveServer2 direkt im Cluster aus, sodass RDP/SSH umgangen wird. Als weiterer Vorteil neben der Leistungsverbesserung können die Benutzer Hive nun in Tez-Diagrammen anzeigen, und sie können die Aufgabendetails anzeigen.

Für HDInsight-Cluster ab Version 3.2 wird eine Schaltfläche **Über HiveServer2 ausführen** angezeigt:

![hdinsight visual studio tools execute via hiveserver2](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png)

Wenn die Hive-Abfrage in Tez ausgeführt wurde, können Sie die zurückgestreamten Protokolle in Echtzeit sehen, und Sie sehen auch die Auftragsdiagramme.
 
![hdinsight visual studio tools fast path hive execution](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png)

**Unterschied zwischen dem Ausführen von Abfragen über HiveServer2 und Senden von Abfragen über WebHCat**

Das Ausführen von Abfragen über HiveServer2 hat zwar viele Leistungsvorteile, jedoch auch einige Einschränkungen. Einige der Einschränkungen beeinträchtigen die Einsatzmöglichkeit in der Produktion. In der folgenden Tabelle sind die Unterschiede aufgeführt:

| |Ausführen über HiveServer2 |Senden über WebHCat|
|---|---|---|
|Ausführen von Abfragen|Erübrigt den Aufwand in WebHCat (startet einen MapReduce-Auftrag namens "TempletonControllerJob").|Solange eine Abfrage über WebHCat ausgeführt wird, startet WebHCat einen MapReduce-Auftrag, der zusätzliche Latenz mit sich bringt.|
|Zurückstreamen von Protokollen|Nahezu in Echtzeit.|Die Auftragsausführungsprotokolle sind nur verfügbar, wenn der Auftrag abgeschlossen ist.|
|Anzeige des Auftragsverlaufs|Wenn eine Abfrage über HiveServer2 ausgeführt wird, wird der Auftragsverlauf (Auftragsprotokoll, Auftragsausgabe) nicht beibehalten. Die Anwendung kann in der YARN-Benutzeroberfläche mit begrenzten Informationen angezeigt werden.|Wenn eine Abfrage über WebHCat ausgeführt wird, wird der Auftragsverlauf (Auftragsprotokoll, Auftragsausgabe) beibehalten und kann mithilfe von Visual Studio/HDInsight SDK/PowerShell angezeigt werden. |
|Fenster schließen| 	Ausführen über HiveServer2 ist eine "synchrone" Methode, sodass Sie die Fenster geöffnet halten müssen; wenn die Fenster geschlossen werden, wird die Ausführung der Abfrage abgebrochen.|Senden über WebHCat ist eine "asynchrone" Methode, sodass Sie die Abfrage über WebHCat senden und Visual Studio schließen können. Sie können jederzeit zurückkehren und die Ergebnisse anzeigen.|


### Leistungsdiagramm für Tez Hive-Auftrag

In den HDInsight-Tools für Visual Studio wird das Anzeigen von Leistungsdiagrammen für die Hive-Aufträge unterstützt, die vom Tez-Ausführungsmodul ausgeführt werden. Informationen zum Aktivieren von Tez finden Sie unter [Verwenden von Hive in HDInsight][hdinsight.hive]. Nachdem Sie in Visual Studio einen Hive-Job gesendet haben, wird das Diagramm in Visual Studio angezeigt, wenn der Auftrag abgeschlossen ist. Sie müssen unter Umständen auf die Schaltfläche **Aktualisieren** klicken, um den aktuellen Auftragsstatus zu erhalten.

> [AZURE.NOTE] Dieses Feature ist nur für höhere HDInsight-Clusterversionen als 3.2.4.593 verfügbar und funktioniert nur für abgeschlossene Aufträge. Dies funktioniert sowohl für Windows-basierte als auch für Linux-basierte Cluster.

![Leistungsdiagramm für Hadoop Hive Tez](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png)

Um Ihnen das Verständnis der Hive-Abfragen zu erleichtern, wurde dem Tool in dieser Version die Ansicht für Hive-Auftragsoperatoren als neues Feature hinzugefügt. Sie müssen nur auf die die Scheitelpunkte des Auftragsdiagramms doppelklicken, um alle Operatoren im Scheitelpunkt anzuzeigen. Sie können auch auf einen bestimmten Operator zeigen, um weitere Details dazu anzuzeigen.

### Aufgabenausführungsansicht für Hive bei Tez-Aufträgen

Mit der Aufgabenausführungsansicht für Hive bei Tez-Aufträgen können Sie strukturierte und visualisierte Informationen zu Hive-Aufträgen gewinnen und sich mehr Auftragsdetails anzeigen lassen. Bei Leistungsproblemen können Sie in dieser Ansicht zusätzliche Details abrufen. Sie erfahren z. B., wie jede einzelne Aufgabe ausgeführt wird, und sehen die Details dazu (Lese- und Schreibvorgänge für Daten, geplanter Ausführungszeitpunkt, Startzeit, Endzeit usw.). Auf Basis dieser visualisierten Informationen können Sie die Auftragskonfigurationen oder die Systemarchitektur optimieren.

![HDInsight Visual Studio Tools Aufgabenausführungsanzeige](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png)

## Ausführen von Pig-Skripts

HDInsight-Tools für Visual Studio unterstützen die Erstellung und das Senden von Pig-Skripts an HDInsight-Cluster. Benutzer können ein Pig-Projekt über eine Vorlage erstellen und das Skript dann an HDInsight-Cluster senden.

## Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie in Visual Studio mithilfe des Hadoop-Toolpakets eine Verbindung mit HDInsight-Clustern herstellen und Hive-Abfragen ausführen. Weitere Informationen finden Sie unter:

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
[hdinsight-provision]: hdinsight/hdinsight-provision-clusters.md
[hdinsight.introduction]: hdinsight-introduction.md
[hdinsight.get.started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight.hive]: hdinsight/hdinsight-use-hive.md
[hdinsight.submit.jobs]: hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: hdinsight/hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: hdinsight/hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org

<!---HONumber=AcomDC_0218_2016-->