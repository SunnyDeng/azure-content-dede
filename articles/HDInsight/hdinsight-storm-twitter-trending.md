<properties
   pageTitle="Beliebte Twitter-Themen mit Apache Storm in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie Sie mit Trident eine Apache Storm-Topologie erstellen, die beliebte Twitter-Themen auf Grundlage von Hashtags ermittelt."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/28/2015"
   ms.author="larryfr"/>

#Ermitteln von beliebten Twitter-Themen mit Apache Storm in HDInsight

Erfahren Sie, wie Sie mit Trident eine Storm-Topologie erstellen, die beliebte Themen (Hashtags) auf Twitter ermittelt.

Trident ist eine allgemeine Abstraktion, die Tools wie Verknüpfungen, Aggregationen, Gruppierungen, Funktionen und Filter bereitstellt. Darüber hinaus bietet Trident Stammfunktionen für die statusbehaftete, inkrementelle Verarbeitung. In diesem Beispiel wird veranschaulicht, wie Sie mithilfe eines benutzerdefinierten Spouts, einer Funktion und verschiedenen, von Trident bereitgestellten integrierten Funktionen eine Topologie erstellen können.

> [AZURE.NOTE]Das Beispiel basiert in großen Teilen auf dem [Trident Storm](https://github.com/jalonsoramos/trident-storm)-Beispiel von Juan Alonso.

##Anforderungen

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java und das JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Ein Twitter-Entwicklerkonto

##Herunterladen des Projekts

Verwenden Sie folgenden Code, um das Projekt lokal zu klonen.

	git clone https://github.com/Blackmist/TwitterTrending

##Topologie

Die Topologie für dieses Beispiel sieht wie folgt aus:

![Topologie](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE]Dies ist eine vereinfachte Ansicht der Topologie. Mehrere Instanzen der Komponenten werden über die Knoten im Cluster verteilt.

Der Trident-Code, der die Topologie implementiert, lautet wie folgt:

	topology.newStream("spout", spout)
	    .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
	    .groupBy(new Fields("hashtag"))
	    .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
	    .newValuesStream()
	    .applyAssembly(new FirstN(10, "count"))
		.each(new Fields("hashtag", "count"), new Debug());

Im Code werden folgende Schritte ausgeführt:

1. Erstellt einen neuen Datenstrom aus dem Spout. Der Spout ruft Tweets von Twitter ab und filtert sie nach bestimmten Schlüsselwörtern (in diesem Beispiel sind das "Love", "Music" und "Coffee").

2. "HashtagExtractor" ist eine benutzerdefinierte Funktion, mit der Hashtags aus den einzelnen Tweets extrahiert werden. Diese werden in den Datenstrom ausgegeben.

3. Der Datenstrom wird nach Hashtag gruppiert und an einen Aggregator übergeben. Dieser Aggregator erstellt einen Zähler für die Häufigkeit des Auftretens einzelner Hashtags. Diese Daten werden im Arbeitsspeicher gespeichert. Abschließend wird ein neuer Datenstrom ausgegeben, der das Hashtag und den Zähler enthält.

4. Da wir für eine bestimmte Gruppe von Tweets nur an den am häufigsten verwendeten Hashtags interessiert sind, wird die **FirstN**-Assembly angewendet, um nur die 10 größten Werte zurückzugeben, die auf dem Feld "count" basieren.

> [AZURE.NOTE]Im Gegensatz zu Spout und "HashtagExtractor" verwenden wir integrierte Trident-Funktionen.
>
> Informationen zu integrierten Vorgängen finden Sie unter <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">Package storm.trident.operation.builtin</a>.
>
> Informationen zu anderen Trident-Statusimplementierungen als "MemoryMapState" finden Sie unter:
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Flexible Storm Trident-Suche</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">trident-redis</a>

###Spout

Der Spout **TwitterSpout** verwendet <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a>, um Tweets aus Twitter abzurufen. Es wird ein Filter (in diesem Beispiel "Love", "Music" und "Coffee") erstellt und eingehende Tweets (Status), die dem Filter entsprechen, werden in einer verknüpften Blockier-Warteschlange gespeichert. (Weitere Informationen finden Sie unter <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">LinkedBlockingQueue-Klasse</a>.) Abschließend werden die Elemente aus der Warteschlange abgerufen und in der Topologie ausgegeben.

###HashtagExtractor

Zum Extrahieren von Hashtags wird <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> verwendet, um alle im Tweet enthaltenen Hashtags abzurufen. Diese werden dann in den Datenstrom ausgegeben.

##Aktivieren von Twitter

Verwenden Sie die folgenden Schritte zum Registrieren einer neuen Twitter-Anwendung und zum Abrufen der Verbraucher- und Zugriffstokeninformationen, die zum Lesen von Twitter erforderlich sind:

1. Wechseln Sie zu <a href="https://apps.twitter.com" target="_blank">Twitter-Apps</a> und klicken Sie auf die Schaltfläche **Neue Anwendung erstellen**. Lassen Sie beim Ausfüllen des Formulars das Feld **Rückruf-URL** leer.

2. Wenn die App erstellt wurde, klicken Sie auf die Registerkarte **Keys and Access Tokens**.

3. Kopieren Sie die Informationen für den **Verbraucherschlüssel** und den **Geheimen Verbraucherschlüssel**.

4. Wählen Sie unten auf der Seite die Option zum **Erstellen von eigenen Zugriffstoken**, wenn keine Token vorhanden sind. Nachdem die Token erstellt wurden, kopieren Sie die Informationen für **Zugriffstoken** und das **Geheimwort für den Zugriffstoken**.

5. Öffnen Sie im zuvor geklonten Projekt **TwitterSpoutTopology** die Datei **resources/twitter4j.properties**, fügen Sie die in den vorherigen Schritten gesammelten Informationen ein, und speichern Sie dann die Datei.

##Erstellen der Topologie

Verwenden Sie den folgenden Code, um das Projekt zu erstellen.

		cd [directoryname]
		mvn compile

##Testen der Topologie

Verwenden Sie den folgenden Befehl, um die Topologie lokal zu testen:

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Nach dem Start der Topologie sollten Debuginformationen angezeigt werden, die die von der Topologie ausgegebenen Hashtags und deren Anzahl enthalten. Die Ausgabe sollte in etwa folgendermaßen aussehen:

	DEBUG: [Quicktellervalentine, 7]
	DEBUG: [GRAMMYs, 7]
	DEBUG: [AskSam, 7]
	DEBUG: [poppunk, 1]
	DEBUG: [rock, 1]
	DEBUG: [punkrock, 1]
	DEBUG: [band, 1]
	DEBUG: [punk, 1]
	DEBUG: [indonesiapunkrock, 1]

##Nächste Schritte

Nachdem Sie die Topologie lokal getestet haben, erhalten Sie jetzt Informationen zum Bereitstellen der Topologie: [Bereitstellen und Verwalten der Apache Storm-Topologien in HDInsight.](hdinsight-storm-deploy-monitor-topology.md)

Möglicherweise sind auch die folgenden Storm-Themen für Sie interessant:

* [Entwickeln von Java-Topologien für Storm in HDInsight mit Maven](hdinsight-storm-develop-java-topology.md)

* [Entwickeln von C#-Topologien für Storm in HDInsight mithilfe von Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Weitere Storm-Beispiele für HDInsight:

* [Beispiele für Storm-Topologien für Storm in HDInsight](hdinsight-storm-example-topology.md)

<!--HONumber=54--> 