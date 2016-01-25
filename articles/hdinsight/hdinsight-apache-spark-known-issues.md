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
	ms.date="01/13/2016" 
	ms.author="jgao"/>

# Bekannte Probleme von Apache Spark in Azure HDInsight (Linux)

In diesem Dokument werden sämtliche bekannte Probleme für die öffentliche Vorschauversion von HDInsight Spark erfasst.

##Verlust einer interaktiven Sitzung durch Livy
 
**Symptom:**

Wenn Livy neu gestartet wird, während noch eine interaktive Sitzung (von Ambari oder aufgrund eines VM-Neustarts mit Stammknoten 0) aktiv ist, geht eine interaktive Auftragssitzung verloren. Dadurch bleiben neue Aufträge unter Umständen im Zustand „Akzeptiert“ hängen und können nicht gestartet werden.

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

##Erster Notebook-Start dauert länger als erwartet 

**Symptom:**

Die Verarbeitung der ersten Anweisung in Jupyter Notebook mit Spark Magic kann über eine Minute dauern.

**Lösung:**
 
Keine Problemumgehung verfügbar. Manchmal dauert der Vorgang einfach eine Weile.

##Anpassung der Kern-/Arbeitsspeicherkonfiguration nicht möglich

**Symptom:**
 
Die standardmäßige Kern-/Arbeitsspeicherkonfiguration der Spark-/Pyspark-Kernel kann nicht verändert werden.

**Lösung:**
 
Dieses Feature ist in Arbeit.

##Jupyter Notebook-Timeout bei der Sitzungserstellung

**Symptom:**

Wenn dem Spark-Cluster nicht genügend Ressourcen zur Verfügung stehen, tritt bei der Sitzungserstellung für die Spark- und Pyspark-Kernel in Jupyter Notebook ein Timeout auf. Lösung:

1. Führen Sie folgende Schritte aus, um Ressourcen in Ihrem Spark-Cluster freizugeben:

    - Beenden Sie andere Spark-Notebooks über das entsprechende Menü, oder klicken Sie im Notebook-Explorer auf „Herunterfahren“.
    - Beenden Sie andere Spark-Anwendungen über YARN.

2. Starten Sie das Notebook, das Sie starten wollten, neu. Nun sollten genügend Ressourcen für die Sitzungserstellung verfügbar sein.

##Formatierungsproblem bei Notebook-Ausgabeergebnissen

**Symptom:**
 
Notebook-Ausgabeergebnisse sind nach der Ausführung einer Zelle aus den Spark- und Pyspark- Jupyter-Kernels falsch formatiert. Dies schließt erfolgreiche Ergebnisse von Zellausführungen sowie Spark-StackTraces oder andere Fehler ein.

**Lösung:**
 
Dieses Problem wird in einer zukünftigen Version behoben.

##Tippfehler in Beispiel-Notebooks (englische Version)
 
- **Python-Notebook 4 (Analysieren von Protokollen mit Spark unter Verwendung einer benutzerdefinierten Bibliothek)**

    „Let us assume you copy it over to wasb:///example/data/iislogparser.py“ muss „Let us assume you copy it over to wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py" heißen.

- **Python-Notebook 5 (Spark-Machine Learning – Vorhersageanalyse von Daten für die Lebensmittelüberwachung mithilfe von MLLib)**

    „A quick visualization can help us reason about the distribution of these outcomes“ enthält fehlerhaften Code, der nicht ausgeführt werden kann. Der Code muss wie folgt aussehen:

        countResults = df.groupBy('results').count().withColumnRenamed('count', 'cnt').collect() 
        labels = [row.results for row in countResults] 
        sizes = [row.cnt for row in countResults] 
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral'] 
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors) plt.axis('equal') 
        
- **Python-Notebook 5 (Spark-Machine Learning – Vorhersageanalyse von Daten für die Lebensmittelüberwachung mithilfe von MLLib)**

    Im Abschlusskommentar heißt es, dass die Falsch-Negativ-Rate 12,6 Prozent und die Falsch-Positiv-Rate 16,0 Prozent beträgt. Diese Zahlen sind nicht korrekt. Führen Sie den Code aus, um das Kreisdiagramm mit den korrekten Prozentsätzen anzuzeigen.

- **Python-Notebooks 6 und 7**

    Von der ersten Zelle wird die sc.stop()-Methode, die bei Beendigung des Notebooks aufgerufen werden soll, nicht erfolgreich registriert. Dies kann unter bestimmten Umständen zu Ressourcenverlusten für Spark führen. Führen Sie zur Vermeidung dieses Problems auf den Notebooks „import atexit; atexit.register(lambda: sc.stop())“ aus, bevor Sie sie beenden. Gehen Sie bei unbeabsichtigten Ressourcenverlusten gemäß der obigen Beschreibung zum Erzwingen der Beendigung der betreffenden YARN-Anwendungen vor.
     
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

<!---HONumber=AcomDC_0114_2016-->