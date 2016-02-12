<properties
 pageTitle="Korrelieren von Ereignissen im Zeitverlauf mithilfe von Storm und HBase in HDInsight"
 description="Erfahren Sie, wie Ereignisse, die zu unterschiedlichen Zeitpunkten eingehen, mithilfe von Storm und HBase in HDInsight korreliert werden."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="02/01/2016"
 ms.author="larryfr"/>

# Korrelieren von Ereignissen im Zeitverlauf mithilfe von Storm und HBase in HDInsight

Durch die Verwendung eines persistenten Datenspeichers für Apache Storm können Sie Dateneinträge korrelieren, die zu unterschiedlichen Zeitpunkten eingehen. Beispielsweise kann durch die Verknüpfung der An- und Abmeldungsereignisse für eine Benutzersitzung berechnet werden, wie lange die Sitzung gedauert hat.

In diesem Dokument erfahren Sie, wie Sie eine einfache C#-Storm-Topologie erstellen, die Anmelde-und Abmeldevorgänge für Benutzersitzungen verfolgt und die Sitzungsdauer berechnet. In dieser Topologie wird HBase als persistenter Datenspeicher verwendet. HBase ermöglicht es Ihnen auch, Batchabfragen an den historischen Daten auszuführen, um zusätzliche Einblicke zu gewinnen, wie z. B. wie viele Benutzersitzungen während eines bestimmten Zeitraums gestartet oder beendet wurden.

[AZURE.INCLUDE [nur für Windows](../../includes/hdinsight-windows-only.md)]

## Voraussetzungen

-	HDInsight-Tools für Visual Studio: Informationen zur Installation finden Sie unter [Erste Schritte mit den HDInsight Tools für Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

-	Apache Storm in HDInsight-Cluster

-	Apache HBase in HDInsight-Cluster

## Architektur

![Diagramm des Datenflusses in der Topologie](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

Zum Korrelieren von Ereignissen ist ein allgemeiner Bezeichner für die Ereignisquelle erforderlich. Z. B. eine Benutzer-ID, Sitzungs-ID oder andere Daten, die (a) eindeutig und (b) in allen Daten, die an Storm gesendet werden, enthalten sind. In diesem Beispiel wird ein GUID-Wert zur Darstellung der Sitzungs-ID verwendet.

Dieses Beispiel besteht aus zwei HDInsight-Clustern:

-	HBase: persistenter Datenspeicher für historische Daten

-	Storm: wird zum Erfassen eingehender Daten verwendet

Die Daten werden von der Storm-Topologie nach dem Zufallsprinzip generiert und beinhalten die folgenden Elemente:

-	Sitzungs-ID: eine GUID, die jede Sitzung eindeutig identifiziert

-	Ereignis: ein START- oder END-Ereignis. In diesem Beispiel tritt das START- immer vor dem END-Ereignis auf.

-	Zeit: die Uhrzeit des Ereignisses.

Diese Daten werden verarbeitet und in HBase gespeichert.

### Storm-Topologie

Beim Start einer Sitzung wird ein **START**-Ereignis von der Topologie empfangen und in HBase protokolliert. Sobald ein **END**-Ereignis empfangen wird, ruft die Topologie das **START**-Ereignis ab und berechnet den Zeitraum zwischen den beiden Ereignissen. Dieser Wert (**Duration**) wird dann zusammen mit den **END**-Ereignisinformationen in HBase gespeichert.

> [AZURE.IMPORTANT] Mit diese Topologie wird das grundlegende Muster beschrieben, in einer Produktionslösung müssten jedoch auch die folgenden Szenarien berücksichtigt werden:
>
> - Ereignisse, die nicht in der vorgesehenen Reihenfolge eintreffen
> - Doppelte Ereignisse
> - Gelöschte Ereignisse

Die Beispieltopologie besteht aus den folgenden Komponenten:

- 	Session.cs: Simuliert eine Benutzersitzung durch Erstellen einer zufälligen Sitzungs-ID, einer Startzeit und einer Sitzungsdauer

- 	Spout.cs: Erstellt 100 Sitzungen, gibt ein START-Ereignis aus, wartet auf den Ablauf des zufälligen Timeouts für jede Sitzung und gibt dann ein END-Ereignis aus. Anschließend werden beendete Sitzungen wiederverwendet, um neue Sitzungen zu generieren.

-	HBaseLookupBolt.cs: Verwendet die Sitzungs-ID, um die Sitzungsinformationen in HBase zu suchen. Während der Verarbeitung eines END-Ereignisses sucht das Programm das zugehörige START-Ereignis und berechnet die Dauer der Sitzung.

-	HBaseBolt.cs: Speichert Informationen in HBase.

-	TypeHelper.cs: Hilft bei der Typkonvertierung beim Lesen aus und Schreiben in HBase.

### HBase-Schema

In HBase werden die Daten in einer Tabelle mit den folgenden Schemaeinstellungen gespeichert:

-	Zeilenschlüssel: Die Sitzungs-ID wird als Schlüssel für die Zeilen in dieser Tabelle verwendet.

-	Spaltenfamilie: der Familienname lautet 'cf'. Spalten, die in dieser Familie gespeichert werden:

	- 	event: START oder END

	- 	time: die Uhrzeitangabe in Millisekunden, zu der das Ereignis aufgetreten ist

	-	duration: die Länge des Zeitraums zwischen Start- und END-Ereignis

-	VERSIONS: Die Familie 'cf' ist so konfiguriert, dass 5 Versionen jeder Zeile beibehalten werden.

	> [AZURE.NOTE] Versionen sind ein Protokoll der vorherigen Werte, die für einen bestimmte Zeilenschlüssel gespeichert wurden. Standardmäßig gibt HBase nur den Wert für die neueste Version einer Zeile zurück. In diesem Fall wird dieselbe Zeile für alle Ereignisse (START, END) verwendet. Jede Version einer Zeile wird durch den Timestampwert identifiziert. Dadurch wird eine historische Ansicht der Ereignisse bereitgestellt, die für eine bestimmte ID protokolliert wurden.

## Herunterladen des Projekts

Das Beispielprojekt kann von [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation) heruntergeladen werden.

Dieser Download enthält die folgenden C#-Projekte:

-	CorrelationTopology: C#-Storm-Topologie, die nach dem Zufallsprinzip Start- und End-Ereignisse für Benutzersitzungen ausgibt. Jede Sitzung dauert zwischen 1 und 5 Minuten.

-	SessionInfo: C#-Konsolenanwendung, die die HBase-Tabelle erstellt und Beispiele für Abfragen zur Rückgabe von Informationen über gespeicherte Daten enthält.

## Erstellen der Tabelle

1. Öffnen Sie das Projekt **SessionInfo** in Visual Studio.

2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **SessionInfo**, und wählen Sie **Eigenschaften** aus.

	![Screenshot des Menüs mit den ausgewählten Eigenschaften](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. Wählen Sie **Einstellungen**, legen Sie die folgenden Werte fest:

	- 	HBaseClusterURL: die URL für den HBase-Cluster. Beispiel: https://myhbasecluster.azurehdinsight.net

	- 	HBaseClusterUserName: das Administrator-/HTTP-Benutzerkonto für den Cluster.

	- 	HBaseClusterPassword: das Kennwort für das Administrator-/HTTP-Benutzerkonto.

	- 	HBaseTableName: der Name der Tabelle, die mit diesem Beispiel verwendet wird.

	-	HBaseTableColumnFamily: Der Spaltenfamilienname.

	![Bild des Dialogfelds "Einstellungen"](./media/hdinsight-storm-correlation-topology/settings.png)

5. Führen Sie die Lösung aus. Wenn Sie dazu aufgefordert werden, wählen Sie 'c' zum Erstellen der Tabelle in Ihrem HBase-Cluster.

## Erstellen und Bereitstellen der Storm-Topologie

1.	Öffnen Sie die Lösung **CorrelationTopology** in Visual Studio.

2.	Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **CorrelationTopology**, und wählen Sie "Eigenschaften" aus.

3.	Wählen Sie im Eigenschaftenfenster **Einstellungen**, und geben Sie die folgenden Informationen an. Die ersten 5 Werte sollten mit den Werte identisch sein, die im Projekt **SessionInfo** verwendet wurden:

	- 	HBaseClusterURL: die URL für den HBase-Cluster. Beispiel: https://myhbasecluster.azurehdinsight.net

	- 	HBaseClusterUserName: das Administrator-/HTTP-Benutzerkonto für den Cluster.

	- 	HBaseClusterPassword: das Kennwort für das Administrator-/HTTP-Benutzerkonto.

	- 	HBaseTableName: der Name der Tabelle, die in diesem Beispiel verwendet wird. Hier sollte derselbe Tabellenname wie für das SessionInfo-Projekt verwendet werden.

	- 	HBaseTableColumnFamily: Der Spaltenfamilienname. Hier sollte derselbe Spaltenfamilienname wie für das SessionInfo-Projekt verwendet werden.

	> [AZURE.IMPORTANT] Verändern Sie HBaseTableColumnNames nicht, da die Standardeinstellungen den Namen entsprechen, die in **SessionInfo** zum Abrufen von Daten verwendet werden.

4.  Speichern Sie die Eigenschaften aus, und erstellen Sie das Projekt.

5.	Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **An Storm in HDInsight übermitteln** aus. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen für Ihr Azure-Abonnement ein.

	![Bild des Storm-Menüelements "An Storm senden"](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6.	Wählen Sie im Dialogfeld **Topologie übermitteln** den Storm-Cluster, in dem diese Topologie ausgeführt werden.

	> [AZURE.NOTE] Bei der ersten Übermittlung einer Topologie kann das Abrufen der Namen Ihrer HDInsight-Cluster einige Sekunden dauern.

7.	Sobald die Topologie hochgeladen und an den Cluster gesendet wurde, wird die **Storm-Topologieansicht** geöffnet, und die ausgeführte Topologie wird angezeigt. Wählen Sie **CorrelationTopology** aus, und verwenden Sie die Schaltfläche "Aktualisieren" oben rechts auf der Seite, um die Topologieinformationen zu aktualisieren.

	![Bild der Topologie-Ansicht](./media/hdinsight-storm-correlation-topology/topologyview.png)

	Wenn die Topologie mit dem Generieren von Daten beginnt, erhöht sich der Wert in der Spalte **Emitted**.

	> [AZURE.NOTE] Wenn die **Storm-Topologieansicht** nicht automatisch geöffnet wird, gehen Sie folgendermaßen vor, um diese Ansicht zu öffnen:
	>
	> 1. Erweitern Sie im **Projektmappen-Explorer** zunächst **Azure** und dann **HDInsight**.
	>
	> 2. Klicken Sie mit der rechten Maustaste auf den Storm-Cluster, in dem die Topologie ausgeführt wird, und wählen Sie dann **View Storm Topologies** (Storm-Topologien anzeigen).

## Abfragen von Daten

Sobald Daten ausgegeben wurden, gehen Sie folgendermaßen vor, um die Daten abzufragen.

1. Kehren Sie zum Projekt **SessionInfo** zurück. Wenn es nicht ausgeführt wird, starten Sie eine neue Instanz dieses Projekts.

2. Wenn Sie dazu aufgefordert werden, wählen Sie **s**, um nach dem START-Ereignis zu suchen. Sie werden aufgefordert, eine Start- und eine Endzeit zum Definieren eines Zeitraums einzugeben. Nur Ereignisse zwischen diesen beiden Zeitangaben werden zurückgegeben.

	Verwenden Sie zur Eingabe der Start- und Endzeiten folgendes Format: HH:MM und "am" oder "pm". Beispiel: 11:20pm.

	Da die Topologie gerade gestartet wurde, wählen Sie eine Startzeit, die vor der Bereitstellung liegt, und als Endzeit die aktuelle Uhrzeit. Damit sollten die meisten START-Ereignisse erfasst werden, die beim Start generiert wurden. Wenn die Abfrage ausgeführt wird, sollte eine Liste von Einträgen angezeigt werden, die etwa wie folgt aussieht:

		Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

Die Suche nach END-Ereignissen wird auf dieselbe Weise wie die Suche nach START-Ereignissen ausgeführt. END-Ereignisse werden jedoch nach dem Zufallsprinzip zwischen 1 und 5 Minuten nach dem START-Ereignis generiert. Daher müssen Sie möglicherweise einige Zeitabschnitte ausprobieren, um die END-Ereignisse zu finden. END-Ereignisse enthalten außerdem die Dauer der Sitzung: die Differenz zwischen der Zeitangabe des START-Ereignisses und der Zeitangabe des END-Ereignisses. Es folgen Beispieldaten für END-Ereignisse:

	Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [AZURE.NOTE] Sie geben die Zeitwerte in Ortszeit ein, von der Abfrage werden jedoch UTC-Zeitwerte zurückgegeben.

##Beenden der Topologie

Wenn Sie die Topologie beenden möchten, kehren Sie zum Projekt **CorrelationTopology** in Visual Studio zurück. Wählen Sie in der **Storm-Topologieansicht** die Topologie aus, und verwenden Sie dann die Schaltfläche **Kill** am oberen Rand der Topologieansicht.

##Nächste Schritte

Weitere Storm-Beispiele finden Sie unter [Beispieltopologien für Storm in HDInsight](hdinsight-storm-example-topology.md).
 

<!---HONumber=AcomDC_0204_2016-->