<properties 
	pageTitle="Bekannte Probleme von Apache Spark in HDInsight | Microsoft Azure" 
	description="Bekannte Probleme von Apache Spark in HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2016" 
	ms.author="nitinme"/>

# Bekannte Probleme von Apache Spark in Azure HDInsight (Linux)

In diesem Dokument werden sämtliche bekannte Probleme für die öffentliche Vorschauversion von HDInsight Spark erfasst.

##Verlust einer interaktiven Sitzung durch Livy
 
**Symptom:**

Wenn Livy neu gestartet wird, während noch eine interaktive Sitzung (von Ambari oder aufgrund eines VM-Neustarts mit Stammknoten 0) aktiv ist, geht eine interaktive Auftragssitzung verloren. Dadurch bleiben neue Aufträge unter Umständen im Zustand „Akzeptiert“ hängen und können nicht gestartet werden.

**Lösung:**

Gehen Sie wie folgt vor, um das Problem zu umgehen:

1. Greifen Sie per SSH auf den Stammknoten zu. 
2. Führen Sie den folgenden Befehl aus, um die Anwendungs-IDs der interaktiven Aufträge zu ermitteln, die über Livy gestartet wurden. 

        yarn application –list

    Wenn die Aufträge mit einer interaktiven Livy-Sitzung ohne explizite Namensangabe gestartet wurden, lauten die Auftragsnamen standardmäßig „Livy“. Bei der von Jupyter Notebook gestarteten Livy-Sitzung beginnt der Auftragsname mit „remotesparkmagics\_*“.

3. Führen Sie den folgenden Befehl aus, um die Beendigung dieser Aufträge zu erzwingen.

        yarn application –kill <Application ID>

Neue Aufträge werden gestartet.

##Spark-Verlaufsserver startet nicht 

**Symptom:**
 
Der Spark-Verlaufsserver wird nach der Clustererstellung nicht automatisch gestartet.

**Lösung:**

Starten Sie den Verlaufsserver in Ambari manuell.

##Fehler beim Laden eines größeren Notebooks

**Symptom:**

Möglicherweise wird beim Laden von größeren Notebooks der Fehler **`Error loading notebook`** angezeigt.

**Lösung:**

Wenn Sie diesen Fehler erhalten, bedeutet dies nicht, dass Ihre Daten beschädigt oder verloren sind. Ihre Notebooks befinden sich weiter in `/var/lib/jupyter`, und Sie können sich über SSH mit dem Cluster verbinden, um darauf zuzugreifen. Sie können Ihre Notebooks zur Sicherung aus Ihrem Cluster auf den lokalen Computer kopieren (mit SCP oder WinSCP), um den Verlust wichtiger Daten im Notebook zu vermeiden. Anschließend können Sie über einen SSH-Tunnel an Port 8001 eine Verbindung mit Ihrem Hauptknoten herstellen, um ohne Umweg über das Gateway auf Jupyter zuzugreifen. Dort können die Ausgabe Ihres Notebooks löschen und es erneut speichern, um die Größe des Notebooks zu minimieren.

Um zu verhindern, dass dieser Fehler in Zukunft auftritt, müssen Sie einige bewährten Methoden befolgen:

* Es ist wichtig, die Größe von Notebooks niedrig zu halten. Alle Ausgaben Ihrer Spark-Aufträge, die an Jupyter zurückgesendet werden, werden beständig im Notebook gespeichert. Für Jupyter wird allgemein empfohlen, das Anwenden von `.collect()` auf große RDDs (Resilient Distributed Datasets) oder Datenframes zu vermeiden. Wenn Sie einen Blick auf den Inhalt eines RDD werfen möchten, erwägen Sie das Ausführen von `.take()` oder `.sample()`, damit Ihre Ausgabe nicht zu groß wird.
* Löschen Sie außerdem beim Speichern eines Notebooks alle Ausgabezellen, um die Größe zu verringern.



##Erster Notebook-Start dauert länger als erwartet 

**Symptom:**

Die Verarbeitung der ersten Anweisung in Jupyter Notebook mit Spark Magic kann über eine Minute dauern.

**Erklärung:**
 
Dies geschieht, wenn die erste Codezelle ausgeführt wird. Im Hintergrund werden dadurch die Sitzungskonfiguration initiiert und Spark-, SQL- sowie Hive-Kontexte festgelegt. Nachdem diese Kontexte festgelegt wurden, wird die erste Anweisung ausgeführt, die den Eindruck entstehen lässt, dass sie lange Zeit in Anspruch nimmt.

##Jupyter Notebook-Timeout bei der Sitzungserstellung

**Symptom:**

Wenn dem Spark-Cluster nicht genügend Ressourcen zur Verfügung stehen, tritt bei der Sitzungserstellung für die Spark- und Pyspark-Kernel in Jupyter Notebook ein Timeout auf.

**Lösung:**

1. Führen Sie folgende Schritte aus, um Ressourcen in Ihrem Spark-Cluster freizugeben:

    - Beenden Sie andere Spark-Notebooks über das entsprechende Menü, oder klicken Sie im Notebook-Explorer auf „Herunterfahren“.
    - Beenden Sie andere Spark-Anwendungen über YARN.

2. Starten Sie das Notebook, das Sie starten wollten, neu. Nun sollten genügend Ressourcen für die Sitzungserstellung verfügbar sein.

## Berechtigungsproblem im Spark-Protokollverzeichnis 

**Symptom:**
 
Wenn „hdiuser“ einen Auftrag mit „spark-submit“ übermittelt, tritt der Fehler „java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log“ (Zugriff verweigert) auf, und das Treiberprotokoll wird nicht geschrieben.

**Lösung:**
 
1. Fügen Sie „hdiuser“ der Hadoop-Gruppe hinzu. 
2. Erteilen Sie nach der Clustererstellung 777-Berechtigungen für „/var/log/spark“. 
3. Aktualisieren Sie den Spark-Protokollspeicherort mit Ambari auf ein Verzeichnis mit 777-Berechtigungen.  
4. Führen Sie „spark-submit“ als sudo aus. 

##Weitere Informationen

- [Übersicht: Apache Spark für Azure HDInsight (Linux)](hdinsight-apache-spark-overview.md)
- [Erste Schritte: Bereitstellen von Apache Spark für Azure HDInsight (Linux) und Ausführen von interaktiven Abfragen per Spark-SQL](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_0224_2016-->