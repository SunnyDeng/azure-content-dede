<properties 
   pageTitle="Bereitstellen und Verwalten von Storm-Topologien in HDInsight | Azure" 
   description="Erfahren Sie, wie Sie Storm-Topologien mithilfe des Storm-Dashboards bereitstellen, überwachen und verwalten, das in Apache Storm in HDInsight enthalten ist." 
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
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Bereitstellen und Verwalten von Apache Storm-Topologien in HDInsight

Mithilfe des Storm-Dashboards können Sie Apache Storm-Topologien für Ihren HDInsight-Cluster über Ihren Webbrowser ganz einfach bereitstellen und ausführen. Sie können das Dashboard auch zum Überwachen und Verwalten von aktiven Topologien verwenden. Wenn Sie Visual Studio verwenden, bieten die HDInsight Tools für Visual Studio ähnliche Features in Visual Studio.

Sowohl das Storm-Dashboard als auch die Storm-Features der HDInsight Tools sind von der Storm-REST-API abhängig, mit der Sie eigene Lösungen zur Überwachung und Verwaltung erstellen können.

## Voraussetzungen

* **Apache Storm in HDInsight**: Informationen zum Erstellen eines Clusters finden Sie unter <a href="../hdinsight-storm-getting-started/" target="_blank">Erste Schritte mit Apache Storm in HDInsight</a>

* Für das **Storm-Dashboard**: Ein zeitgemäßer Webbrowser, der HTML5 unterstützt.

* Für **Visual Studio**: Azure SDK 2.5.1 oder höher und die HDInsight Tools für Visual Studio. Informationen zum Installieren und Konfigurieren der HDInsight Tools für Visual Studio finden Sie unter <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">Erste Schritte mit den HDInsight Tools für Visual Studio</a>.

	Eine der folgenden Versionen von Visual Studio:

	* Visual Studio 2012 mit <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">Update 4</a>

	* Visual Studio 2013 mit <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">Update 4</a> oder <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Community</a>

	* <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

	> [AZURE.NOTE] Die HDInsight Tools für Visual Studio unterstützen derzeit nur Storm auf der HDInsight-Clusterversion 3.2.

## Storm-Dashboard

Das Storm-Dashboard steht im Sturm-Cluster zur Verfügung. Die URL lautet **https://&lt;Clustername>.azurehdinsight.net/**, wobei **Clustername** den Namen des Storm-Clusters in HDInsight angibt. Sie können auch über den Link **Storm-Dashboard** von Ihrem Ihrem Cluster-Dashboard im Azure-Portal auf das Dashboard zugreifen.

![the portal with storm dashboard highlighted][hdinsight-dashboard]

Wählen Sie oben im Storm-Dashboard die Option **Topologie übermitteln** aus. Befolgen Sie die Anweisungen auf der Seite, um eine Beispieltopologie auszuführen oder eine bereits erstellte Topologie hochzuladen und auszuführen.

![the submit topology page][storm-dashboard-submit]

### Storm-Benutzeroberfläche

Wählen Sie im Storm-Dashboard den Link **Storm-Benutzeroberfläche** aus. Dadurch werden die Informationen zum Cluster sowie alle aktiven Topologien angezeigt.

![the storm ui][storm-dashboard-ui] 

> [AZURE.NOTE] Bei einigen Versionen von Internet Explorer kann es passieren, dass die Storm-Benutzeroberfläche nach dem ersten Öffnen nicht aktualisiert wird. Möglicherweise werden neue übermittelte Topologien nicht angezeigt oder es wird eine Topologie als aktiv angezeigt, die Sie zuvor beendet haben. Microsoft ist sich des Problems bewusst und arbeitet an einer Lösung.

#### Hauptseite

Die Hauptseite der Storm-Benutzeroberfläche bietet die folgenden Informationen.

* **Clusterzusammenfassung**: grundlegende Informationen zum Storm-Cluster

* **Topologiezusammenfassung**: eine Liste der aktiven Topologien Verwenden Sie die Links in diesem Abschnitt, um weitere Informationen zu bestimmten Topologien anzuzeigen.

* **Supervisor-Zusammenfassung**: Informationen zum Storm-Supervisor

* **Nimbus-Konfiguration**: die Nimbus-Konfiguration für den Cluster

#### Topologiezusammenfassung

Wenn Sie einen Link aus dem Abschnitt **Topologiezusammenfassung** auswählen, werden die folgenden Informationen zur Topologie angezeigt.

* **Topologiezusammenfassung**: grundlegende Informationen zur Topologie

* **Topologieaktionen**: Verwaltungsaktionen, die für die Topologie ausgeführt werden können

	* **Aktivieren**: setzt die Verarbeitung einer deaktivierten Topologie fort
	
	* **Deaktivieren**: hält eine aktive Topologie an
	
	* **Ausgleichen**: passt die Parallelität der Topologie an Sie sollten aktive Topologien ausgleichen, nachdem Sie die Anzahl der Knoten im Cluster geändert haben. Dadurch kann die Topologie die Parallelität anpassen, um die höhere/geringere Anzahl der Knoten im Cluster zu kompensieren.
	
		Weitere Informationen finden Sie unter <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Grundlegendes zur Parallelität einer Storm-Topologie</a>
	
	* **Beenden**: beendet eine Storm-Topologie nach dem angegebenen Timeout

* **Topologiestatistik**: Statistiken zur Topologie Verwenden Sie die Links in der Spalte **Fenster**, um den Zeitrahmen für die verbleibenden Einträge auf der Seite festzulegen.

* **Spouts**: die von der Topologie verwendeten Spouts Verwenden Sie die Links in diesem Abschnitt, um weitere Informationen zu bestimmten Spouts anzuzeigen.

* **Bolts**: die von der Topologie verwendeten Bolts Verwenden Sie die Links in diesem Abschnitt, um weitere Informationen zu bestimmten Bolts anzuzeigen.

* **Topologiekonfiguration**: die Konfiguration der ausgewählten Topologie

#### Spout und Bolt: Zusammenfassung

Wenn Sie im Abschnitt **Spouts** oder **Bolts** einen Spout auswählen, werden die folgenden Informationen zum ausgewählten Element angezeigt.

* **Komponentenübersicht**: grundlegende Informationen zum Spout oder Bolt

* **Statistik für Spout/Bolt**: Statistiken zum Spout oder Bolt Verwenden Sie die Links in der Spalte **Fenster**, um den Zeitrahmen für die verbleibenden Einträge auf der Seite festzulegen.

* **Eingabestatistik** (nur Bolt): Informationen zum Eingabedatenstrom, der vom Bolt verbraucht wird

* **Ausgabestatistik**: Informationen zu den Datenströmen, die von diesem Spout oder Bolt ausgegeben werden

* **Ausführer**: Informationen zu den Instanzen von Spout oder Bolt Wählen Sie den Eintrag **Port** für einen bestimmten Ausführer aus, um ein Protokoll mit Diagnoseinformationen anzuzeigen, das für diese Instanz generiert wurde.

* **Fehler**: Fehlerinformationen für diesen Spout oder Bolt.

## HDInsight Tools für Visual Studio

Mithilfe der HDInsight Tools können C#- oder hybride Topologien an Ihre Storm-Cluster übermittelt werden. Für die folgenden Schritte wird eine Beispielanwendung verwendet. Informationen zum Erstellen Ihrer eigenen Topologien mithilfe der HDInsight Tools finden Sie unter [Entwickeln von C#-Topologien mithilfe der HDInsight Tools für Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Gehen Sie wie folgt vor, um ein Beispiel für den Storm-Cluster in HDInsight bereitzustellen und dann die Topologie anzuzeigen und zu verwalten.

1. Wenn Sie die neueste Version der HDInsight Tools für Visual Studio noch nicht installiert haben, finden Sie Informationen dazu unter <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">Erste Schritte mit den HDInsight Tools für Visual Studio</a>.

2. Öffnen Sie Visual Studio, wählen Sie **Datei**, **Neu**, und klicken Sie dann auf **Projekt**.

3. Erweitern Sie im Dialogfeld **Neues Projekt** die Option **Installiert**, **Vorlagen**, und wählen Sie dann **HDInsight** aus. Wählen Sie in der Liste der Vorlagen die Option **Storm-Beispiel** aus. Geben Sie unten im Dialogfeld einen Namen für die Anwendung ein.

	![image](./media/hdinsight-storm-deploy-monitor/sample.png)

1. Klicken Sie im **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **An Storm in HDInsight übermitteln** aus.

	> [AZURE.NOTE] Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen für Ihr Azure-Abonnement ein. Wenn Sie über mehrere Abonnements verfügen, melden Sie sich bei dem Abonnement an, das Ihren Storm-Cluster in HDInsight enthält.

2. Wählen Sie Ihren Storm-Cluster in HDInsight aus der Dropdownliste **Storm-Cluster** und dann die Option **Übermitteln** aus. Sie können über das Fenster **Ausgabe** überwachen, ob die Übermittlung erfolgreich ausgeführt wurde.

3. Sobald die Topologie erfolgreich übermittelt wurde, sollten die **Storm-Topologien** für den Cluster angezeigt werden. Wählen Sie die Topologie aus der Liste aus, um Informationen zur aktiven Topologie anzuzeigen.

	![visual studio monitor](./media/hdinsight-storm-deploy-monitor/vsmonitor.png)

	> [AZURE.NOTE] Sie können die **Storm-Topologien** auch im **Server-Explorer** anzeigen, indem Sie **Azure**, **HDInsight** erweitern und dann mit der rechten Maustaste auf einen Storm-Cluster in HDInsight klicken und **Storm-Topologien anzeigen** auswählen.

	Verwenden Sie die Links für die Spouts oder Bolts, um Informationen zu diesen Komponenten anzuzeigen. Für jedes ausgewählte Element wird ein neues Fenster geöffnet.

4. Wählen Sie in der Ansicht **Topologiezusammenfassung** die Option **Beenden** aus, um die Topologie zu beenden.

	> [AZURE.NOTE] Storm-Topologien werden weiterhin ausgeführt, bis sie beendet werden oder der Cluster gelöscht wird.

## REST-API

Die Storm-Benutzeroberfläche baut auf der REST-API auf, sodass Sie mithilfe der API ähnliche Verwaltungs- und Überwachungsfunktionen ausführen können. Mithilfe der REST-API können Sie benutzerdefinierte Tools zum Verwalten und Überwachen von Storm-Topologien erstellen.

Eine Dokumentation zur Storm-REST-API finden Sie unter <a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md</a>. Die folgenden Informationen gelten für die Verwendung der REST-API mit Apache Storm in HDInsight.

### Basis-URI

Der Basis-URI für die REST-API auf HDInsight-Clustern ist **https://&lt;Clustername>.azurehdinsight.net/stormui/api/v1/**, wobei **Clustername** den Namen des Storm-Clusters in HDInsight angibt.

### Authentifizierung

Anforderungen an die REST-API müssen die **Standardauthentifizierung** mit dem Benutzernamen und Kennwort des HDInsight-Clusteradministrators verwenden. 

> [AZURE.NOTE] Da die Standardauthentifizierung unverschlüsselt gesendet wird, sollten Sie **immer** HTTPS verwenden, um die Kommunikation mit dem Cluster zu schützen.

### Rückgabewerte

Von der REST-API zurückgegebene Informationen sind möglicherweise nur innerhalb des Clusters oder auf den Computern in demselben Azure Virtual Network wie der Cluster verwendbar. Auf den für Zookeeper-Server zurückgegebenen vollqualifizierten Domänennamen (FQDN) kann z. B. nicht über das Internet zugegriffen werden.

## Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Topologien mithilfe des Storm-Dashboards bereitstellen und überwachen, lesen Sie jetzt die Informationen zum [Entwickeln von C#-Topologien mithilfe der HDInsight Tools für Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)oder zum [Entwickeln von Java-basierten Topologien mit Maven](hdinsight-storm-develop-java-topology.md).


[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor/storm-ui-summary.png

<!--HONumber=47-->
