<properties
	pageTitle="HDInsight – Versionshinweise | Azure"
	description="HDInsight – Versionshinweise."
	services="hdinsight"
	documentationCenter=""
	editor="cgronlun"
	manager="paulettm"
	authors="nitinme"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="nitinme"/>


#Microsoft HDInsight – Versionshinweise

## Hinweise für die HDInsight-Version vom 27.04.2015 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster:

* HDInsight 2.1.10.537.1486660 (HDP 1.3.12.0-01795 – unverändert)
* HDInsight 3.0.6.537.1486660 (HDP 2.0.13.0-2117 – unverändert)
* HDInsight 3.1.3.537.1486660 (HDP 2.1.12.0-2329 – unverändert)
* HDInsight 3.2.3.537.1486660 (HDP 2.2.2.2-4)
* SDK 1.5.8

Diese Version enthält die folgenden Updates.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Betroffener Bereich (z.&#160;B. Dienst, Komponente oder SDK)</p></th>
<th>Clustertyp (z.&#160;B. Hadoop, HBase oder Storm)</th>
<th>JIRA (falls zutreffend)</th>
</tr>


<tr>
<td>DLL-Abhängigkeit behoben</td>
<td>Entfernt die Abhängigkeit von HDInsight vom Komponententest-Framework.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/V</td>
</tr>

<tr>
<td>Fehlerkorrektur für Racebedingung</td>
<td>Eine Anfrage zum Erstellen eines Clusters wartet jetzt auf die Annahme der PUT-Anforderung vor dem Abruf des Status</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/V</td>
</tr>
</table>

## Hinweise für die HDInsight-Version vom 14.04.2015 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 – unverändert)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 – unverändert)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 – unverändert)
* HDInsight 3.2.3.525.1459730 (HDP 2.2.2.2-2)
* SDK 1.5.6

Diese Version enthält die folgenden Updates.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Betroffener Bereich (z.&#160;B. Dienst, Komponente oder SDK)</p></th>
<th>Clustertyp (z.&#160;B. Hadoop, HBase oder Storm)</th>
<th>JIRA (falls zutreffend)</th>
</tr>


<tr>
<td>Fehlerbehebungen</td>
<td>Fehlerbehebungen für Apache TEZ 2214 und TEZ 1923 sind in dieser Version von HDI 3.2 enthalten. Diese werden speziell für bestimmte Hive-Abfragen auf Tez benötigt, bei denen beträchtliche Datenmengen gemischt werden müssen.
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## Hinweise für die HDInsight-Version vom 06.04.2015 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 – unverändert)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 – unverändert)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 – unverändert)
* HDInsight 3.2.3.521.1453250 (HDP 2.2.2.2-1)
* SDK 1.5.6

Diese Version enthält die folgenden Updates.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Betroffener Bereich (z.&#160;B. Dienst, Komponente oder SDK)</p></th>
<th>Clustertyp (z.&#160;B. Hadoop, HBase oder Storm)</th>
<th>JIRA (falls zutreffend)</th>
</tr>


<tr>
<td>HDInsight .NET SDK 1.5.6</td>
<td>Updates zum Entfernen einiger interner Klassen für HDInsight unter Linux.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/V</td>
</tr>

<tr>
<td>Avro-Bibliothek 1.5.6</td>
<td>Hinzugefügt <b>KnownTypeAttribute</b> für <b>GetAllKnownTypes</b>-Methode. NullReferenceException behoben, wenn ein Typ für die GetAllKnownTypes-Methode NULL ist.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/V</td>
</tr>

<tr>
<td>Fehlerbehebungen</td>
<td>Verschiedene Fehlerbehebungen für den Dienst</td>
<td>Dienst</td>
<td>Alle</td>
<td>N/V</td>
</tr>

</table>

## Hinweise für die HDInsight-Version vom 01.04.2015 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster:

* HDInsight 2.1.10.513.1431705 (HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705 (HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705 (HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705 (HDP 2.2.2.1-2600)
* SDK 1.5.5

Diese Version enthält die folgenden Updates.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Betroffener Bereich (z.&#160;B. Dienst, Komponente oder SDK)</p></th>
<th>Clustertyp (z.&#160;B. Hadoop, HBase oder Storm)</th>
<th>JIRA (falls zutreffend)</th>
</tr>


<tr>
<td>Möglichkeit zum Aktivieren/Deaktivieren von Remotedesktop-Anmeldeinformationen auf Windows-Clustern über das .NET SDK</td>
<td>Programmgesteuerte Unterstützung für das Aktivieren oder Deaktivieren von RDP-Anmeldeinformationen in Windows-Clustern.</td>
<td>SDK</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>Möglichkeit zum Aktivieren von Remotedesktop-Anmeldeinformationen in Clustern während diese bereitgestellt werden</td>
<td>Programmgesteuerte Unterstützung für die Aktivierung von Remotedesktop-Anmeldeinformationen beim Erstellend es Clusters. Dadurch entfallen die beiden Schritte für die erste Bereitstellung des Clusters und der anschließenden Aktivierung von Remotedesktop.</td>
<td>SDK</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>Python-Upgrade auf 2.7.8</td>
<td>Upgrade von Python in HDInsight-Clustern auf Version&#160;2.7.8, die einige wichtige sicherheitsrelevante Korrekturen für die HDInsight-Versionen 2.1, 3.0, 3.1 und 3.2 enthält</td>
<td>Dienst</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>Änderung der YARN-Konfiguration</td>
<td>Die YARN-Konfiguration "yarn.resourcemanager.max-completed-applications" wurde für alle Clustertypen für die HDInsight-Versionen 3.1 und 3.2 in 1000 geändert. Dieser Wert steuert nur die Liste der abgeschlossenen Anwendungen in der Benutzeroberfläche von YARN. Zum Abrufen von Informationen zu Anwendungen, die vor der auf der Benutzeroberfläche angezeigten Liste der Anwendungen übermittelt wurden, wechseln Sie direkt in den Verlauf auf dem Server.</td>
<td>YARN</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>Ändern der Größe von Knoten in einem HBase-Cluster</td>
<td>HBase-Cluster ermöglichen nun das Ändern der Größe von Knoten (nach oben und unten) für die HDInsight-Versionen 3.1 und 3.2</td>
<td>Dienst</td>
<td>HBase</td>
<td>N/V</td>
</tr>

<tr>
<td>JDBC-Upgrade</td>
<td>Upgrade des SQL-JDBC-Treibers auf Version sqljdbc_4.0.2206.100 für die HDInsight-Version 3.2. Diese Version enthält wichtige Sicherheitserweiterungen.</td>
<td>HDP</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>Aktualisierung der JVM-Konfiguration</td>
<td>Änderung der JVM-Konfiguration "networkaddress.cache.ttl" vom Standardwert –1 in 300&#160;Sekunden für die HDInsight-Versionen 3.1 und 3.2. Dieser Konfigurationswert steuert die Cacherichtlinie für die erfolgreiche Namenssuche im Name Service. Damit wird ein Fehler im Zusammenhang mit dem Vergrößern und Verkleinern von HBase-Clustern behoben.</td>
<td>Dienst</td>
<td>HBase</td>
<td>N/V</td>
</tr>

<tr>
<td>Upgrade auf Azure Storage SDK für Java&#160;2.0</td>
<td>Upgrade für HDInsight-Version&#160;3.2 zur Verwendung der neuesten Version des Azure Storage SDK für Java. Diese enthält mehrere wichtige Fehlerbehebungen gegenüber der aktuellen Version&#160;0.6.0.</td>
<td>HDP</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>Upgrade auf Apache WASB-Quellcode</td>
<td>HDInsight-Version&#160;3.2 verwendet jetzt den neuesten Code für die WASB-Dateisystemtreiber von Apache Hadoop. Durch diese Änderung wird der WASB-Treiber jetzt als separate JAR-Datei verpackt. Dies ist lediglich eine Paketveränderung und keine Änderungen am Verhalten des WASB-Treibers. Der Name der JAR-Datei ist "hadoop-azure-2.6.0.jar".</td>
<td>HDP</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>JAR-Dateinamenaktualisierung in HDInsight&#160;3.2</td>
<td>Dieses Update für die HDInsight-Version&#160;3.2 enthält verschiedene Fehlerbehebungen, außerdem wurden einige interne JAR-Dateien als Paketteil von HDP aktualisiert. Beachten Sie, dass diese JAR-Dateien Bestandteil des HDP-Pakets nicht für die direkte Verwendung in Kundenanwendungen vorgesehen sind. Anwendungen sollten ihre eigenen Pakete der JAR-Dateien erstellen, damit ein Upgrade auf die in HDP integrierten JARs Kundenanwendungen nicht beschädigt.</td>
<td>HDP</td>
<td>Alle</td>
<td>N/V</td>
</tr>

</table>


## Hinweise für die HDInsight-Version vom 03.03.2015 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster:

* HDInsight 2.1.10.488.1375841 (HDP 1.3.9.0-01351 – unverändert)
* HDInsight 3.0.6.488.1375841 (HDP 2.0.9.0-2097 – unverändert)
* HDInsight 3.1.3.488.1375841 (HDP 2.1.10.0-2290 – unverändert)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340 – unverändert)
* SDK 1.5.0 (unverändert)

Diese Version enthält das folgende Update.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Betroffener Bereich (z.&#160;B. Dienst, Komponente oder SDK)</p></th>
<th>Clustertyp (z.&#160;B. Hadoop, HBase oder Storm)</th>
<th>JIRA</th>
</tr>


<tr>
<td>Verbesserungen bei der Zuverlässigkeit</td>
<td>Die Fehlerbehebungen ermöglichen eine bessere Skalierbarkeit des Diensts bei erhöhter Auslastung in Bezug auf die Clustererstellung.</td>
<td>Dienst</td>
<td>Alle</td>
<td>N/V</td>
</tr>



</table>


## Hinweise für die HDInsight-Version vom 18.02.2015 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster:

* HDInsight 2.1.10.471.1342507 (HDP 1.3.9.0-01351 – unverändert)
* HDInsight 3.0.6.471.1342507 (HDP 2.0.9.0-2097 – unverändert)
* HDInsight 3.1.3.471.1342507 (HDP 2.1.10.0-2290 – unverändert)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0-2340)
* SDK 1.5.0

Diese Version enthält die folgenden Updates.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Betroffener Bereich (z.&#160;B. Dienst, Komponente oder SDK)</p></th>
<th>Clustertyp (z.&#160;B. Hadoop, HBase oder Storm)</th>
<th>JIRA (falls zutreffend)</th>
</tr>


<tr>
<td>HDInsight-3.2-Cluster</td>
<td>Hadoop&#160;2.6/HDP2.2 mit HDInsight-3.2-Clustern verfügbar. Die Version enthält wichtige Updates für alle Open-Source-Komponenten. Weitere Informationen finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen</a> und <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">HDP 2.2.0.0 Release Notes</a> (in englischer Sprache).</td>
<td>Open-Source-Software</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>HDInsight unter Linux (Vorschau)</td>
<td>Cluster können unter Ubuntu Linux bereitgestellt werden. Weitere Informationen finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-get-started/" target ="_blank">Hadoop tutorial: Get started using Hadoop with Hive in HDInsight on Linux (preview)</a> (Hadoop-Lernprogramm: Erste Schritte mit Hadoop und Hive in HDInsight unter Linux (Vorschau), in englischer Sprache).</td>
<td>Dienst</td>
<td>Hadoop</td>
<td>N/V</td>
</tr>

<tr>
<td>Allgemeine Verfügbarkeit von Storm</td>
<td>Apache Storm-Cluster sind allgemein verfügbar. Weitere Informationen finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-storm-getting-started/" target="_blank">Apache Storm tutorial: Get started with the Storm Starter samples for big data analytics on HDInsight</a> (Apache Storm-Lernprogramm: Erste Schritte mit Storm Starter-Beispielen für die Big Data-Analyse in HDInsight, in englischer Sprache).</td>
<td>Dienst</td>
<td>Storm</td>
<td>N/V</td>
</tr>

<tr>
<td>Größe virtueller Computer</td>
<td>Azure HDInsight ist für mehr Typen und Größen von virtuellen Computern verfügbar. HDInsight kann jetzt die für allgemeine Zwecke konzipierten Größen A2 bis A7, Knoten der D-Serie mit Solid-State-Laufwerken (SSDs) und mit um 60&#160;% schnelleren Prozessoren sowie die Größen A8 und A9 mit InfiniBand-Unterstützung für schnelle Netzwerkverbindungen verwenden.</td>
<td>Dienst</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>Cluster-Skalierung</td>
<td>Sie können die Anzahl der Knoten eines ausgeführten HDInsight-Clusters ändern, ohne ihn löschen oder neu erstellen zu müssen. Derzeit bieten nur die Clustertypen Hadoop Query und Apache Storm diese Möglichkeit&#160;– demnächst wird dies jedoch auch mit Apache HBase-Cluster möglich sein. Weitere Informationen finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-cluster-scaling/" target="_blank">Clusterskalierung in HDInsight</a>.</td>
<td>Dienst</td>
<td>Hadoop, Storm</td>
<td>N/V</td>
</tr>

<tr>
<td>Visual Studio-Tools</td>
<td>Zusätzlich zu vollständigen Tools für Apache Sturm wurden die Tools für Apache Hive in Visual Studio aktualisiert, um die Anweisungsvervollständigung, lokale Überprüfungen und verbesserte Unterstützung für das Remotedebuggen zu implementieren. Weitere Informationen finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio</a>.</td>
<td>Tools</td>
<td>Hadoop</td>
<td>N/V</td>
</tr>

<tr>
<td>Hadoop-Connector für DocumentDB</td>
<td>Mit dem Hadoop-Connector für DocumentDB können Sie komplexe Aggregationen, Analysen und Bearbeitungen in schemalosen JSON-Dokumenten in DocumentDB-Sammlungen oder verschiedenen Datenbankkonten ausführen. Weitere Informationen und ein Lernprogramm finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/documentdb-run-hadoop-with-hdinsight/" target="_blank">Ausführen eines Hadoop-Auftrags mit DocumentDB und HDInsight</a>.</td>
<td>Dienst</td>
<td>Hadoop</td>
<td>N/V</td>
</tr>

<tr>
<td>Fehlerbehebungen</td>
<td>Behebung verschiedener geringfügiger Programmfehler für HDInsight-Dienste. Es wurden keine Änderungen am Verhalten gegenüber den Kunden vorgenommen.</td>
<td>Dienst</td>
<td>Alle</td>
<td>N/V</td>
</tr>

</table>


## Hinweise für die HDInsight-Version vom 06.02.2015 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster:

* HDInsight 2.1.10.463.1325367 (HDP 1.3.9.0-01351 – unverändert)
* HDInsight 3.0.6.463.1325367 (HDP 2.0.9.0-2097 – unverändert)
* HDInsight 3.1.2.463.1325367 (HDP 2.1.10.0-2290)
* SDK N/V

Diese Version enthält die folgenden Updates.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Betroffener Bereich (z.&#160;B. Dienst, Komponente oder SDK)</p></th>
<th>Clustertyp (z.&#160;B. Hadoop, HBase oder Storm)</th>
<th>JIRA (falls zutreffend)</th>
</tr>


<tr>
<td>Fehlerbehebungen</td>
<td>Behebung verschiedener geringfügiger Programmfehler für HDInsight-Dienste. Es wurden keine Änderungen am Verhalten gegenüber den Kunden vorgenommen.</td>
<td>Dienst</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>HDP&#160;2.1-Wartungsupdate</td>
<td>Update für HDInsight&#160;3.1 für die Bereitstellung von HDP 2.1.10.0. Weitere Informationen finden Sie in den <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">Release Notes HDP-2.1.10</a> (Versionshinweisen zu HDP-2.1.10, in englischer Sprache). </td>
<td>Open-Source-Software</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>Updates an HDP-Binärdateien</td>
<td>Es gibt einige JAR-Dateien in HBase, bei denen die Dateinamen aktualisiert wurden. Diese JAR-Dateien werden intern von verwendet HBase, damit die Kunden keine Abhängigkeit der Namen dieser JAR-Dateien benötigen. Das umfasst:
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>Open-Source-Software</td>
<td>HBase</td>
<td>N/V</td>
</tr>

</table>


## Hinweise für die HDInsight-Version vom 29.1.2015 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster:

* HDInsight 2.1.10.455.1309616 (HDP 1.3.9.0-01351 – unverändert)
* HDInsight 3.0.6.455.1309616 (HDP 2.0.9.0-2097 – unverändert)
* HDInsight 3.1.2.455.1309616 (HDP 2.1.9.0-2196 – unverändert)
* SDK N/V

Diese Version enthält das folgende Update.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Betroffener Bereich (z.&#160;B. Dienst, Komponente oder SDK)</p></th>
<th>Clustertyp (z.&#160;B. Hadoop, HBase oder Storm)</th>
<th>JIRA (falls zutreffend)</th>
</tr>


<tr>

<td>Fehlerbehebungen</td>
<td>Wir haben einige wichtige Fehlerbehebungen vorgenommen, die die Zuverlässigkeit der HDInsight-Cluster bei Azure-Upgrades verbessern.</td>
<td>Dienst</td>
<td>Alle</td>
<td>N/V</td>
</tr>



</table>


## Hinweise für die HDInsight-Version vom 5.1.2015 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster:

* HDInsight 2.1.10.420.1246118 (HDP 1.3.9.0-01351 – unverändert)
* HDInsight 3.0.6.420.1246118 (HDP 2.0.9.0-2097 – unverändert)
* HDInsight 3.1.2.420.1246118 (HDP 2.1.9.0-2196 – unverändert)


Diese Version enthält die folgenden Updates.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Komponente</th>
<th>Clustertyp</th>
<th>JIRA (falls zutreffend)</th>
</tr>


<tr>
<td>Beispiele für Twitter-Trendanalyse und Mahout-basierte Filmempfehlungen</td>
<td><p>In dieser Version verfügt die HDInsight-Abfragekonsole über zwei zusätzliche Beispiele:</p>

<p><b>Twitter-Trendanalyse</b><br>
Öffentliche APIs von Websites wie Twitter sind eine nützliche Datenquelle für Analyse und Verständnis beliebter Trends. In diesem Lernprogramm erfahren Sie, wie Sie mit Hive eine Liste der Twitter-Benutzer erhalten, die die meisten Tweets mit einem bestimmten Wort gesendet haben. </p>

<p><b>Mahout-Filmempfehlung</b><br>
Apache Mahout ist eine Apache Hadoop-Bibliothek für maschinelles Lernen. Mahout enthält Algorithmen zur Verarbeitung von Daten wie etwa Filtern, Klassifizierung und Clustering. In diesem Lernprogramm verwenden Sie ein Empfehlungsmodul, um Filmempfehlung auf Grundlage von Filmen, die Ihre Freunde gesehen haben, zu erstellen.</p></td>
<td>Abfragekonsole</td>
<td>Hadoop</td>
<td>N/V</td>
</tr>

<tr>
<td>Ändern Sie den Wert in den Standardwert für die Hive-Konfiguration: "hive.auto.convert.join.noconditionaltask.size"</td>
<td><p>Diese Größenkonfiguration gilt für automatisch konvertierte Map-Verknüpfungen. Der Wert steht für die Summe der Größen von Tabellen, die in Hashzuordnungen konvertiert werden können und die in den Arbeitsspeicher passen. Bei einer früheren Version wurde der Wert vom Standardwert 10&#160;MB auf 128&#160;MB erhöht. Der neue Wert von 128&#160;MB führte jedoch zu Auftragsfehlern aufgrund fehlenden Arbeitsspeichers. In dieser Version wird der Standardwert wieder auf 10&#160;MB zurückgesetzt. Kunden können je nach Abfragen und Tabellengröße diesen Wert weiterhin während der Clustererstellung überschreiben. Weitere Informationen zu dieser Einstellung und das überschreiben, finden Sie in der Hortonworks-Dokumentation unter <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Optimize Auto Join Conversion</a> (Optimieren automatischer Konvertierungen von Verknüpfungen, in englischer Sprache). </p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>N/V</td>
</tr>

</table>


## Hinweise für die HDInsight-Version vom 23.12.2014 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster sind:

* HDInsight 2.1.10.420.1246783 (HDP-Version unverändert)
* HDInsight 3.0.6.420.1246783 (HDP-Version unverändert)
* HDInsight 3.1.1.420.1246783 (HDP-Version unverändert)

Diese Version enthält das folgende Update.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Komponente</th>
<th>Clustertyp</th>
<th>JIRA (falls zutreffend)</th>
</tr>


<tr>
<td>Periodische Clustererstellungsfehler aufgrund übermäßiger Auslastung</td>
<td><p>Verbesserter Algorithmus für das Herunterladen von HDP-Paketen während der Clustererstellung ermöglicht eine robustere Behandlung von Fehlern aufgrund übermäßiger Auslastung.</p></td>
<td>Dienst</td>
<td>Hadoop, Hbase, Storm</td>
<td>N/V</td>
</tr>



</table>


## Hinweise für die HDInsight-Version vom 18.12.2014 ##

Diese Version enthält die folgenden Aktualisierungen von Komponenten.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Komponente</th>
<th>Clustertyp</th>
<th>JIRA (falls zutreffend)</th>
</tr>

<tr>
<td><a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">Clusteranpassung – Allgemeine Verfügbarkeit</a></td>
<td><p>Eine Anpassung bietet Ihnen die Möglichkeit, die Azure HDInsight-Cluster mit in der Apache Hadoop-Umgebung verfügbaren Projekten anzupassen. Mit dieser neuen Funktion können Sie experimentieren und Hadoop-Projekte auf Azure HDInsight bereitstellen. Dies wird mithilfe der **Skriptaktion**-Funktion ermöglicht, mit der Sie Hadoop-Cluster mit benutzerdefinierten Skripts auf beliebige Weise ändern können. Diese Anpassung ist für alle HDInsight-Clustertypen, einschließlich Hadoop, HBase und Storm, verfügbar. Um die Leistungsfähigkeit dieser Funktion zu demonstrieren, haben wir den Installationsprozess der beliebten Module <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-spark-install/" target="_blank">Spark</a>, <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-r-scripts/" target="_blank">R</a>, <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-solr-install/" target="_blank">Solr</a> und <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-giraph-install/" target="_blank">Giraph</a>.&#160;Diese Version bietet Kunden auch die Möglichkeit, ihre benutzerdefinierte Skriptaktion im Azure-Portal anzugeben. Die Version enthält Richtlinien und bewährte Methoden zum Erstellen von benutzerdefinierten Skriptaktionen mit Hilfsmethoden und bietet Richtlinien zum Testen der Skriptaktion. </p></td>
<td>Allgemeine Verfügbarkeit von Features</td>
<td>Alle</td>
<td>N/V</td>
</tr>


</table>


## Hinweise für die HDInsight-Version vom 05.12.2014 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster sind:

* HDInsight 2.1.9.406.1221105 (HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105 (HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105 (HDP 2.1.9.0-2196)
* HDInsight SDK nicht verfügbar

Diese Version enthält die folgenden Aktualisierungen von Komponenten.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Komponente</th>
<th>Clustertyp</th>
<th>JIRA (falls zutreffend)</th>
</tr>

<tr>
<td>Fehlerbehebung: Zeitweiliger Fehler beim Hinzufügen vieler Partitionen zu einer Tabelle in einer Hive-DLL. </td>
<td><p>Wenn beim Hinzufügen vieler Partitionen zu einer Hive-Tabelle ein zeitweiliger Verbindungsfehler auftritt, kann die Hive-DLL ausfallen. Der folgende Auszug ist bei Auftreten dieses Fehlers im Hive-Fehlerprotokoll enthalten: </p><p>"ERROR [main]: ql.Driver (SessionState.java:printError(547)) - FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:java.lang.RuntimeException: commitTransaction was called but openTransactionCalls = 0. Dies ist wahrscheinlich ein Hinweis auf unausgeglichene Aufrufe von openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>HIVE-482 (Dies ist interner JIRA-Code, der nicht extern in Anführungszeichen gesetzt werden kann. Wird hier zur Bezugnahme angegeben.)</td>
</tr>

<tr>
<td>Fehlerbehebung: Gelegentliches Hängen in der HDInsight-Abfragekonsole</td>
<td>Wenn es dazu kommt, ist der folgende Auszug im WebHCat-Protokoll für den WebHCat-Startauftrag enthalten: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Could not load history file {wasb url to the history file}"</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (Dies ist interner JIRA-Code, der nicht extern in Anführungszeichen gesetzt werden kann. Wird hier zur Bezugnahme angegeben.)</td>
</tr>

<tr>
<td>Fehlerbehebung: Gelegentliche Spitze bei Latenz von HBase-Abfragen</td>
<td>Wenn es dazu kommt, bemerken Benutzer eine gelegentliche Spitze von drei Sekunden bei der Latenz von HBase-Abfragen.&#160;</td>
<td>HDInsight Cluster Gateway</td>
<td>HBase</td>
<td>N/V</td>
</tr>

<tr>
<td>Name der HDP-JAR-Datei ändert sich</td>
<td>Für die HDI-Clusterversion&#160;3.0 gelten für von HDP installierte JAR-Dateien verschiedene Änderungen. "jetty-6.1.26.jar" wurde durch"jetty-6.1.26.hwx.jar" ersetzt."jetty-util-6.1.26.jar" wurde durch "jetty-util-6.1.26.hwx.jar" ersetzt. Diese Änderungen gelten für Hadoop-, Mahout-, WebHCat- und Oozie-Projekte.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/V</td>
</tr>

</table>


## Hinweise für die HDInsight-Version vom 21.11.2014 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster sind:

* HDInsight 2.1.9.382.1169709 (keine Änderung im Vergleich zum 14.11.2014)
* HDInsight 3.0.5.382.1169709 (keine Änderung im Vergleich zum 14.11.2014)
* HDInsight 3.1.1.382.1169709 (keine Änderung im Vergleich zum 14.11.2014)
* HDINsight SDK 1.4.0

Diese Version enthält die folgenden Aktualisierungen von Komponenten.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Komponente</th>
<th>Clustertyp</th>
<th>JIRA (falls zutreffend)</th>
</tr>

<tr>
<td>Zugreifen auf Anwendungsprotokolle</td>
<td>Fähigkeit zum programmgesteuerten Aufzählen von Anwendungen, die für Ihre Cluster ausgeführt wurden, und zum Herunterladen relevanter anwendungs- oder containerspezifischer Protokolle zum Beheben von Fehlern in problematischen Anwendungen.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/V</td>
</tr>

<tr>
<td>Fähigkeit zum Angeben des Regionsnamens in IHdInsightClient.DeleteCluster </td>
<td>Das Azure HDInsight SDK ermöglicht die Angabe eines Regionsnamens, wenn **DeleteCluster** verwendet wird. Dadurch werden Konflikte bei Kunden beseitigt, die über zwei Ressourcen mit demselben Namen in unterschiedlichen Regionen verfügt haben und keine davon löschen konnten.</td>
<td>SDK</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>Das **ClusterDetails**-Objekt gibt nun das Feld **DeploymentID** zurück, das einen eindeutigen Bezeichner des Clusters darstellt. Diese ist bei Versuchen der Erstellung von Clustern mit demselben Namen garantiert eindeutig.</td>
<td>SDK</td>
<td>Alle</td>
<td>N/V</td>
</tr>
</table>

## Hinweise für die HDInsight-Version vom 11.14.2014 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster sind:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Diese Version enthält die folgenden neuen Features, Aktualisierungen von Komponenten und Fehlerbehebungen.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Komponente</th>
<th>Clustertyp</th>
<th>JIRA (falls zutreffend)</th>
</tr>

<tr>
<td>Skriptaktion (Vorschau)</td>
<td>Vorschau auf das Feature zur Clusteranpassung, das die Änderung von Hadoop-Clustern auf beliebige Weisen mithilfe benutzerdefinierter Skripts ermöglicht. Mithilfe dieses Features können Benutzer experimentieren und Projekte, die über das Apache Hadoop-Anbietersystem zur Verfügung stehen, in Azure HDInsight-Clustern bereitstellen. Dieses Anpassungsfeature steht für alle Arten von HDInsight-Clustern einschließlich Hadoop, HBase und Storm zur Verfügung.</td>
<td>Neues Feature</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>Vordefinierte Aufträge für die Analyse von Azure-Website- und Speicherprotokollen</td>
<td>Die HDInsight-Abfragekonsole bietet einen Einstiegskatalog, der Lösungen unterstützt, die für Ihre Daten oder Beispieldaten funktionieren.
<p>**Lösungen, die für Ihre Daten funktionieren**:<br>
Wir haben Aufträge für einige der gängigsten Datenanalyseszenarios entwickelt, um Ihnen einen Ausgangspunkt für das Erstellen eigener Lösungen zu bieten. Sie können Ihre eigenen Daten mit dem Auftrag verwenden, um zu prüfen, ob er funktioniert. Wenn Sie soweit sind, können Sie das Gelernte nutzen, um eine Lösung zu entwickeln, die dem vordefinierten Auftrag folgt.</p>
<p>**Lösungen, die für Beispieldaten funktionieren**:<br>
Erfahren Sie, wie Sie mit HDInsight arbeiten, indem Sie verschiedene einfache Szenarios durchlaufen, z.&#160;B. das Analysieren von Webprotokollen und Sensordaten. Sie lernen, wie Sie mit HDInsight solche Daten analysieren und wie Sie andere Anwendungen und Dienste mit diesen Daten verbinden. Die Visualisierung von Daten durch Verbinden mit Microsoft Excel ist ein Beispiel der Leistungsfähigkeit dieses Ansatzes.</p></td>
<td>Abfragekonsole</td>
<td>Hadoop</td>
<td>N/V</td>
</tr>

<tr>
<td>Behebung des Arbeitsspeicherverlusts in Templeton</td>
<td>Ein Arbeitsspeicherverlust in Templeton wurde behoben, der sich auf Kunden ausgewirkt hat, die über langfristig ausgeführte Cluster verfügen oder Hunderte von Auftragsanforderungen pro Sekunde senden. Das Problem manifestierte sich als Templeton 5xx-Fehler, und die Problemumgehung war der Neustart des Diensts. Diese Problemumgehung ist nicht mehr erforderlich.</td>
<td>Templeton</td>
<td>Alle</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>


**Hinweis**: Zum Veranschaulichen der neuen durch die Clusteranpassung verfügbaren Möglichkeiten wurden die Verfahren mit Skriptaktionen zum Installieren von Spark- und R-Modulen für einen Cluster dokumentiert. Weitere Informationen finden Sie unter:

* [Installieren und Verwenden von Spark 1.0 in HDInsight-Clustern](hdinsight-hadoop-spark-install.md)
* [Installieren und Verwenden von R in HDInsight Hadoop-Clustern](hdinsight-hadoop-r-scripts.md)




## Hinweise für die HDInsight-Version vom 07.11.2014 ##

Die vollständigen Versionsnummern für mit dieser Version bereitgestellte HDInsight-Cluster sind:

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

Diese Version enthält die folgenden Aktualisierungen von Komponenten.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
<th>Komponente</th>
<th>Clustertyp</th>
<th>JIRA (falls zutreffend)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Diese Version basiert auf Hortonworks Data Platform (HDP) 2.1.7. Weitere Informationen finden Sie in den <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">Release Notes HDP-2.1.7</a> (Versionshinweisen zu HDP-2.1.7, in englischer Sprache).</td>
<td>HDP</td>
<td>Alle</td>
<td>N/V</td>
</tr>

<tr>
<td>YARN Timeline Server</td>
<td>Der YARN Timeline Server (auch bekannt als Generic Application History Server) wurde standardmäßig aktiviert. Der Timeline Server bietet allgemeine Informationen zu vervollständigten Anwendungen wie Anwendungs-ID, Anwendungsname, Anwendungszustand sowie Uhrzeit der Anwendungsübermittlung und -vervollständigung. Diese Anwendungsinformationen können vom Hauptknoten durch Zugriff auf "http://headnodehost:8188" oder Ausführung des folgenden YARN-Befehls abgerufen werden: "yarn application –list –appStates ALL". Diese Informationen können auch remote über die REST API unter "https://{ClusterDnsName}.azurehdinsight.net/ws/v1/applicationhistory/" abgerufen werden. Ausführlichere Informationen finden Sie unter <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a>.</td>
<td>Dienst, YARN</td>
<td>Hadoop, HBase</td>
<td>N/V</td>
</tr>

<tr>
<td>Clusterbereitstellungs-ID</td>
<td>Ab der SDK-Version 1.3.3.1.5426.29232 können Benutzer auf eine von HDInsight ausgestellte eindeutige ID für jeden Cluster zugreifen. Dadurch können Kunden eindeutige Instanzen von Clustern bestimmen, wenn ein DNS-Name in Erstellungs-/Löschszenarios wiederverwendet wird.</td>
<td>SDK</td>
<td>Alle</td>
<td>N/V</td>
</tr>
</table>


**Hinweis**: Der Fehler, der das Anzeigen der vollständigen Vollversionsnummer im Portal oder deren Rückgabe durch das SDK oder Windows PowerShell verhindert hat, wurde in dieser Version behoben wurde.

## Hinweise für die Version vom 15.10.2014 ##

Dieser Hotfix korrigiert einen Speicherverlust in Templeton, der insbesondere Templeton-Benutzer mit hohen Anforderungen betraf. Manche Benutzer mit hohen Anforderungen an Templeton haben Fehlercodes 500 erhalten, da nicht genügend Arbeitsspeicher für die Bearbeitung der Anforderungen vorhanden war. Der Templeton-Dienst musste in diesem Fall neu gestartet werden, um das Problem zu beheben. Dieses Problem wurde korrigiert.


## Hinweise für die Version vom 07.10.2014 ##

* Beim Verwenden des Ambari-Endpunkts "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}" gibt das Feld *host_name* nun den vollqualifizierten Domänennamen (FQDN) des Knotens anstelle des Hostnamens zurück. Anstelle von "**headnode0**" erhalten Sie z. B. nun den FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**". Diese Änderung erleichtert Szenarios, in denen mehrere Clustertypen wie z. B. HBase und Hadoop in einem einzigen virtuellen Netzwerk (VNET) bereitgestellt werden. Dies ist z. B. der Fall, wenn HBase als Back-End-Plattform für Hadoop verwendet wird.

* Wir haben neue Speichereinstellungen für die Standardbereitstellung des HDInsight-Clusters bereitgestellt. Mit den vorherigen Speichereinstellungen wurde die Anzahl der bereitgestellten CPU-Kerne nicht ausreichend berücksichtigt. Diese neuen Arbeitsspeichereinstellungen sollten gemäß den Empfehlungen von Hortonworks bessere Standardeinstellungen bieten. Um diese zu ändern, konsultieren Sie die SDK-Referenzdokumentation bezüglich des Änderns der Clusterkonfiguration. Die folgende Tabelle enthält die neuen Speichereinstellungen für ein Standard-HDInsight-Cluster mit vier CPU-Kernen (acht Container). (Die Werte der vorherigen Versionen sind in Klammern angegeben).

<table border="1"> <tr><th>Komponente</th><th>Speicherzuweisung</th></tr> <tr><td> yarn.scheduler.minimum-allocation</td><td>768 MB (bislang 512 MB)</td></tr> <tr><td> yarn.scheduler.maximum-allocation</td><td>6144 MB (unverändert)</td></tr> <tr><td>yarn.nodemanager.resource.memory</td><td>6144 MB (unverändert)</td></tr> <tr><td>mapreduce.map.memory</td><td>768 MB (bislang 512 MB)</td></tr> <tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (bislang -Xmx410m)</td></tr> <tr><td>mapreduce.reduce.memory</td><td>1536 MB (bislang 1024 MB)</td></tr> <tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (bislang -Xmx819m)</td></tr> <tr><td>yarn.app.mapreduce.am.resource</td><td>768 MB (bislang 1024 MB)</td></tr> <tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (bislang -Xmx819m)</td></tr> <tr><td>mapreduce.task.io.sort</td><td>256 MB (bislang 200 MB)</td></tr> <tr><td>tez.am.resource.memory</td><td>1536 MB (unverändert)</td></tr>

</table><br>

Weitere Informationen zu den Speichereinstellungen für YARN und MapReduce in der von HDInsight verwendeten Hortonworks Data Platform finden Sie unter [Determine HDP Memory Configuration Settings](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html) (Ermitteln der HDP-Speichereinstellungen, in englischer Sprache). Hortonworks stellt außerdem ein Tool zum Berechnen der korrekten Speichereinstellungen bereit.

Azure-PowerShell- und HDInsight SDK-Fehlermeldung: "*Cluster is not configured for Http Services access*":

* Dieser Fehler ist ein bekanntes [Kompatibilitätsproblem](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight), das auftritt, wenn sich die Versionen von HDInsight SDK oder Azure PowerShell von der des Clusters unterscheiden. Alle ab dem 15.8. erstellten Cluster unterstützen die neuen Bereitstellungsfähigkeiten für virtuelle Netzwerke. Diese Fähigkeit wird jedoch von älteren Versionen von HDInsight SDK oder Azure PowerShell nicht korrekt interpretiert. Das Resultat ist ein Fehler bei der Übermittlung mancher Aufträge. Falls Sie Aufträge mit HDInsight SDK APIs oder Azure PowerShell-Cmdlets übermitteln (**Use-AzureHDInsightCluster** oder **Invoke-Hive**), kann es passieren, dass diese Operationen mit der Fehlermeldung "*Cluster <clustername> is not configured for HTTP services access*" fehlschlagen. (Abhängig von der Operation) erhalten Sie ggf. andere Fehlermeldungen, z. B. "*Cannot connect to cluster*".

* Diese Kompatibilitätsprobleme sind in den neuesten Versionen von HDInsight SDK und Azure PowerShell korrigiert. Sie sollten daher Ihr HDInsight SDK auf 1.3.1.6 oder eine neuere Version und die Azure PowerShell-Tools auf 0.8.8 oder eine neuere Version aktualisieren. Sie erhalten das neueste HDInsight SDK über [](http://nuget.codeplex.com/wikipage?title=Getting%20Started) und die Azure PowerShell-Tools unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure/).



## Hinweise für die HDInsight-Version 3.1 vom 12.9.2014##

* Diese Version basiert auf Hortonworks Data Platform (HDP) 2.1.5. Eine Liste der in dieser Version korrigierten Fehler finden Sie auf der Seite [Fixed in this Release](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) (Korrekturen in dieser Version, in englischer Sprache) auf der Hortonworks-Website.
* Die Datei "avro-mapred-1.7.4.jar” im Pig-Bibliotheksverzeichnis wurde geändert in "avro-mapred-1.7.4-hadoop2.jar". Diese Datei enthält eine kleine Fehlerkorrektur, die keine Funktionen beeinträchtigt. Wir empfehlen unseren Kunden, in ihren Abhängigkeiten nicht direkt auf den Namen der JAR-Datei zu verweisen, um Probleme zu vermeiden, wenn Namen von Dateien geändert werden.


## Hinweise für die Version vom 21.08.2014 ##

* Es wird die folgende neue WebHCat-Konfiguration (HIVE-7155) hinzugefügt, mit der die Standardspeicherbegrenzung für einen Templeton-Controllerauftrag auf 1 GB festgelegt wird. (Der vorherige Standardwert war 512 MB.)

	 templeton.mapper.memory.mb (=1024)

	* Mit dieser Änderung wird der folgende Fehler behoben, der bei bestimmten Hive-Abfragen aufgrund der niedrigeren Speicherbegrenzungen auftrat: "Container is running beyond physical memory limits".
	* Um die Einstellung auf den alten Standardwert zurückzusetzen, können Sie diesen Konfigurationswert zum Zeitpunkt der Clustererstellung über Azure PowerShell auf 512 festlegen. Verwenden Sie folgenden Befehl:

		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* Der Hostname der Zookeeper-Rolle wurde in *zookeeper* geändert. Das wirkt sich auf die Namensauflösung im Cluster aus; die externen REST APIs sind aber nicht betroffen. Wenn Sie Komponenten haben, die den Hostnamen *zookeepernode* verwenden, müssen Sie sie für die Verwendung des neuen Namens aktualisieren. Die neuen Namen der drei Zookeeper-Knoten lauten:
	* zookeeper0
	* zookeeper1
	* zookeeper2
* Die HBase-Versionssupportmatrix wurde aktualisiert. Nur die HDInsight-Version 3.1 (HBase-Version 0.98) wird für HBase-Produktionsarbeitslasten unterstützt. Version 3.0 (die zur Vorschau verfügbar war) wird zukünftig nicht mehr unterstützt.

## Hinweise zu Clustern, die vor dem 8.15.2014 erstellt wurden ##

Möglicherweise treten in Azure PowerShell oder im HDInsight SDK Fehler mit der Nachricht "Cluster <clustername> is not configured for HTTP services access" (bzw. je nach Operation andere Fehlermeldungen wie z. B.: "Cannot connect to cluster") auf. Der Grund hierfür sind Versionsunterschiede zwischen Azure PowerShell oder HDInsight SDK und einem Cluster. Alle ab dem 15.8. erstellten Cluster unterstützen die neuen Bereitstellungsfähigkeiten für virtuelle Netzwerke. Diese Fähigkeit wird von älteren Versionen von Azure PowerShell oder HDInsight SDK nicht korrekt interpretiert. Das Resultat ist ein Fehler bei der Übermittlung mancher Aufträge. Falls Sie Aufträge mit HDInsight SDK APIs oder Azure PowerShell-Cmdlets übermitteln (z. B. "Use-AzureHDInsightCluster" oder "Invoke-AzureHDInsightHiveJob"), kann es passieren, dass diese Operationen mit einer der oben beschriebenen Fehlermeldungen fehlschlagen.

Diese Kompatibilitätsprobleme sind in den neuesten Versionen von HDInsight SDK und Azure PowerShell korrigiert. Sie sollten daher Ihr HDInsight SDK auf 1.3.1.6 oder eine neuere Version und die Azure PowerShell-Tools auf 0.8.8 oder eine neuere Version aktualisieren. Sie erhalten über [NuGet][nuget-link] Zugriff auf das neueste HDInsight SDK. Zugriff auf die Azure PowerShell-Tools erhalten Sie mithilfe des [Microsoft Webplattform-Installers][webpi-link].


## Hinweise für die Version vom 28.07.2014 ##

* **HDInsight ist in neuen Regionen verfügbar**: Die geografische Präsenz von HDInsight wurde auf drei neue Regionen ausgeweitet. HDInsight-Kunden können in diesen Regionen Cluster erstellen:
	* Ostasien
	* USA Nord Mitte
	* USA (Mitte/Süden)
* HDInsight-Version 1.6 (HDP 1.1 und Hadoop 1.0.3) und HDInsight Version 2.1 (HDP1.3 und Hadoop 1.2) werden vom Azure-Portal entfernt. Sie können weiterhin Hadoop-Cluster für diese Versionen erstellen, indem Sie Azure PowerShell-Cmdlets ([New-AzureHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx)) oder das [HDInsight SDK](http://msdn.microsoft.com/library/azure/dn469975.aspx) verwenden. Weitere Informationen finden Sie auf der Seite [HDInsight-Komponentenversionen](../hdinsight-component-versioning/).
* Änderungen an Hortonworks Data Platform (HDP) in dieser Version:

<table border="1"> <tr><th>HDP</th><th>Änderungen</th></tr> <tr><td>HDP 1.3/HDI 2.1</td><td>Keine Änderungen</td></tr> <tr><td>HDP 2.0/HDI 3.0</td><td>Keine Änderungen</td></tr> <tr><td>HDP 2.1/HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## Hinweise für die Version vom 24.06.2014 ##

Diese Version enthält Verbesserungen des HDInsight-Diensts:

* **HDP 2.1-Verfügbarkeit**: HDInsight 2.1 mit HDP 3.1 ist jetzt allgemein verfügbar und die Standardversion für neue Cluster.
* **HBase – Verbesserungen am Azure-Portal**: HBase-Cluster werden in der Vorschau verfügbar gemacht. Über das Portal können HBase-Cluster mit drei Klicks erstellt werden.

![](http://i.imgur.com/cmOl5fM.png)

Mit HBase können Sie verschiedene Arbeitslasten in Echtzeit in HDInsight erstellen, von interaktiven Websites mit großen Datasets bis zu Diensten, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Der nächste Schritt besteht im Analysieren der Daten in diesen Arbeitslasten mit Hadoop-Aufträgen. Dies ist in HDInsight dank Azure PowerShell und Hive-Cluster-Dashboard möglich.

### Apache Mahout auf HDInsight 3.1 vorinstalliert ###

 [Mahout](http://hortonworks.com/hadoop/mahout/) ist in Hadoop-Clustern unter HDInsight 3.1 vorinstalliert. Sie können also Mahout-Aufträge ausführen, ohne eine zusätzliche Clusterkonfiguration vorzunehmen. Sie können beispielsweise remote auf einen Hadoop-Cluster über das Remote Desktop Protocol (RDP) zugreifen und ohne zusätzliche Schritte den Mahout-Befehl "Hello World" ausführen:

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Eine umfassendere Erklärung dieses Verfahrens finden Sie in der Dokumentation zum [Breiman-Beispiel](https://mahout.apache.org/users/classification/breiman-example.html) auf der Apache Mahout-Website.


### Hive-Abfragen können Tez in HDInsight 3.1 verwenden ###

Hive 0.13 ist in HDInsight 3.1 verfügbar und kann Abfragen mit Tez ausführen, was für erhebliche Leistungsverbesserungen genutzt werden kann. Tez ist nicht standardmäßig für Hive-Abfragen aktiviert. Sie müssen die Option aktivieren, um sie verwenden zu können. Aktivieren Sie Tez, indem Sie den folgenden Codeausschnitt ausführen:

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks hat eine detaillierte Aufschlüsselung der Leistungsverbesserungen bei Hive-Abfragen mit Tez veröffentlicht, die sich aus Standardvergleichstests ergaben. Einzelheiten finden Sie unter [Benchmark von Apache Hive 13 für Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Weitere Einzelheiten zur Verwendung von Hive mit Tez finden Sie unter [Hive on Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) (in englischer Sprache).

###Globale Verfügbarkeit
Mit der Freigabe von HDInsight auf Hadoop 2.2 hat Microsoft HDInsight in allen größeren Azure-Gebieten bereitgestellt. Insbesondere Datencenter in Westeuropa und Südostasien wurden online geschaltet. So können die Kunden Cluster in einem Rechenzentrum finden, das sich in Ihrer Nähe befindet und potenziell in einer Zone mit ähnlichen Anforderungen zur Einhaltung von Vorschriften liegt.


###Empfehlungen und Warnungen für unterschiedliche Clusterversionen

**Oozie-Metastores mit HDInsight 3.1-Cluster sind nicht abwärtskompatibel mit HDInsight 2.1-Clustern und können nicht mit dieser älteren Version verwendet werden**.

Benutzerdefinierte Oozie-Metastore-Datenbanken mit einem HDInsight 3.1-Cluster können nicht mit einem HDInsight 2.1-Cluster wiederverwendet werden. Dies gilt auch dann, wenn der Metastore ursprünglich mit einem HDInsight 2.1-Cluster erstellt wurde. Dieses Szenario wird nicht unterstützt, da für das Metastore-Schema bei der Verwendung mit einem HDInsight 3.1-Cluster ein Upgrade durchgeführt wird und es daher nicht mehr mit dem Metastore kompatibel ist, der ein HDInsight 2.1-Cluster benötigt. Jeder Versuch der Wiederverwendung eines Oozie-Metastore, der bereits mit einem HDInsight 3.1-Cluster verwendet wurde, wird das HDInsight 2.1-Cluster unbrauchbar machen.

**Oozie-Metastores können nicht zwischen Clustern gemeinsam genutzt werden.**

Oozie-Metastores sind an einen bestimmten Cluster gebunden und können nicht zwischen Clustern gemeinsam genutzt werden.

###Wichtige Änderungen

**Präfixsyntax**: In HDInsight 3.1- und 3.0-Clustern wird nur die "wasb://"-Syntax unterstützt. Die ältere "asv://"-Syntax wird in HDInsight 2.1- und 1.6-Clustern unterstützt, nicht aber in HDInsight 3.1- oder 3.0-Clustern. Dies bedeutet, dass alle an einen HDInsight-Cluster der Version 3.1 oder 3.0 übermittelten Aufträge, die explizit die "asv://"-Syntax verwenden, fehlschlagen werden. Stattdessen sollte die "wasb://"-Syntax verwendet werden. An HDInsight-Cluster der Version 3.1 oder 3.0 gesendete Aufträge, die mithilfe eines vorhandenen Metastores erstellt wurden, der explizite Verweise auf Ressourcen mit der "asv://"-Syntax enthält, schlagen ebenfalls fehl. Diese Metastores müssen mit der "wasb://"-Syntax neu erstellt werden, um die Ressourcen zu adressieren.


**Ports**: Die vom HDInsight-Dienst verwendeten Ports wurden geändert. Die Portnummern, die verwendet wurden, befanden sich im kurzlebigen Portbereich des Windows-Betriebssystems. Ports werden automatisch von einem vordefinierten kurzlebigen Bereich für kurzlebige internetprotokollbasierte Kommunikationen zugewiesen. Die neue Reihe der zulässigen Portnummern des HDP-Diensts (Hortonworks Data Platform) befinden sich außerhalb dieses Bereichs, um Konflikte zu vermeiden, die mit den Ports auftreten könnten, welche von auf dem Hauptknoten ausgeführten Diensten verwendet werden. Die neuen Portnummern dürften keine fehlerhaften Änderungen verursachen. Es werden die folgenden Nummern verwendet:

 **HDInsight 1.6 (HDP 1.1)** <table border="1"> <tr><th>Name</th><th>Wert</th></tr> <tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr> <tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr> <tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr> <tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr> <tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr> <tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr> <tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr> <tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr> <tr><td>templeton.port</td><td>30111</td></tr> </table><br>

 **HDInsight 3.1 und 3.0 (HDP 2.1 und 2.0)** <table border="1"> <tr><th>Name</th><th>Wert</th></tr> <tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr> <tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr> <tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr> <tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr> <tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr> <tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr> <tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr> <tr><td>templeton.port</td><td>30111</td></tr> </table><br>

###Abhängigkeiten

Die folgende Abhängigkeiten wurden in HDInsight 3.x (HDP 2.x) hinzugefügt:

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

Die folgenden Abhängigkeiten bestehen in HDInsight 3.x (HDP2.x) nicht mehr:

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###Versionsänderungen

Die folgenden Versionsänderungen wurden zwischen HDInsight 2.x (HDP1.x) und HDInsight 3.x (HDP2.x) vorgenommen:

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###Treiber
Der JDBC-Treiber (Java Database Connnectivity) für SQL Server wird intern von HDInsight und nicht für externe Vorgänge verwendet. Wenn Sie eine Verbindung zu HDInsight über ODBC (Open Database Connectivity) herstellen möchten, verwenden Sie den Microsoft Hive ODBC-Treiber. Weitere Informationen finden Sie unter [Verbinden von Excel über den Microsoft Hive ODBC-Treiber mit HDInsight](hdinsight-connect-excel-hive-odbc-driver.md).


### Fehlerbehebungen ###

In dieser Version wurden die folgenden HDInsight-Versionen mit mehreren Fehlerbehebungen aktualisiert:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## Hortonworks-Versionshinweise ##

Versionshinweise für die HDPs (Hortonworks Data Platform), die von den Clustern der jeweiligen HDInsight-Versionen verwendet werden, finden sich an folgenden Speicherorten:

* HDInsight, Version 3.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.1.7][hdp-2-1-7] basiert. Dies ist der Hadoop-Standardcluster, der bei Verwendung des Azure-Portals nach dem 07.11.2014 erstellt wird. Vor dem 07.11.2014 erstellte HDInsight 3.1-Cluster basierten auf [Hortonworks Data Platform 2.1.1][hdp-2-1-1].

* HDInsight, Version 3.0 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.0][hdp-2-0-8] basiert.

* HDInsight, Version 2.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.3][hdp-1-3-0] basiert.

* HDInsight, Version 1.6 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.1][hdp-1-1-0] basiert.

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/

<!--HONumber=54-->