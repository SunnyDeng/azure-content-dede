<properties
   pageTitle="Überwachen und Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche | Microsoft Azure"
   description="Erfahren Sie, wie Sie Ambari zum Überwachen und Verwalten von Linux-basierten HDInsight-Clustern verwenden. In diesem Dokument erfahren Sie, wie Sie die in HDInsight-Clustern enthaltene Ambari-Webbenutzeroberfläche verwenden."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/03/2015"
   ms.author="larryfr"/>

#Verwalten von HDInsight-Clustern mithilfe der Ambari-Webbenutzeroberfläche

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari vereinfacht die Verwaltung und Überwachung von Hadoop-Clustern durch die Bereitstellung einer benutzerfreundlichen Webbenutzeroberfläche und REST-API. Ambari ist in Linux-basierten HDInsight-Clustern enthalten und wird verwendet, um den Cluster zu überwachen und Konfigurationsänderungen vorzunehmen.

In diesem Dokument erfahren Sie, wie Sie die Ambari-Webbenutzeroberfläche mit einem HDInsight-Cluster verwenden.

> [AZURE.NOTE]Die Informationen in diesem Artikel gelten nur für Linux-basierte HDInsight-Cluster. Für Windows-basierte HDInsight-Cluster ist die Überwachung nur über die Ambari-REST-API verfügbar. Weitere Informationen finden Sie unter [Überwachen von Hadoop in HDInsight mit der Ambari-API](hdinsight-monitor-use-ambari-api.md).

##<a id="whatis"></a>Was ist Ambari?

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> vereinfacht die Hadoop-Verwaltung durch die Bereitstellung einer benutzerfreundlichen Webbenutzeroberfläche, die zum Bereitstellen, Verwalten und Überwachen von Hadoop-Clustern verwendet werden kann. Entwickler können diese Funktionen mithilfe der <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">Ambari-REST-APIs</a> in ihre Anwendungen integrieren.

Die Ambari-Webbenutzeroberfläche wird standardmäßig mit Linux-basierten HDInsight-Clustern bereitgestellt.

##Konnektivität

Die Ambari-Webbenutzeroberfläche ist in Ihrem HDInsight-Cluster unter HTTPS://CLUSTERNAME.azurehdidnsight.net verfügbar, wobei __CLUSTERNAME__ der Name Ihres Clusters ist.

> [AZURE.IMPORTANT]Zum Herstellen einer Verbindung mit Ambari in HDInsight ist HTTPS erforderlich. Sie müssen sich bei Ambari auch mit dem Administratorkontonamen (standardmäßig __Admin__) und dem Kennwort authentifizieren, die Sie bei der Erstellung des Clusters angegeben haben.

##SSH-Proxy

> [AZURE.NOTE]Während Ambari für Ihren Cluster direkt über das Internet erreichbar ist, werden einige Links der Ambari-Webbenutzeroberfläche (z. B. JobTracker) nicht im Internet verfügbar gemacht. Sie erhalten also Fehler, dass der Server nicht gefunden wurde, wenn Sie versuchen, auf diese Funktionen zuzugreifen, sofern Sie keinen SSH-Tunnel (Secure Shell) für den Webdatenverkehr per Proxy auf dem Stammknoten des Clusters verwenden.

Informationen zum Erstellen eines SSH-Tunnels, der mit Ambari funktioniert, finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

##Ambari-Webbenutzeroberfläche

Wenn Sie eine Verbindung mit der Ambari-Webbenutzeroberfläche herstellen, werden Sie aufgefordert, die Seite zu authentifizieren. Verwenden Sie den Clusteradministrator (standardmäßig „Admin“) und das Kennwort, die Sie während der Erstellung des Clusters verwendet haben.

Beachten Sie beim Öffnen der Seite die Leiste im oberen Bereich. Diese enthält die folgenden Informationen und Steuerelemente:

![Ambari-Navigation](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari-Logo**: Zeigt das Dashboard an, mit dem der Cluster überwacht werden kann.

* **Clustername (Anzahl) ops**: Zeigt die Anzahl der laufenden Ambari-Vorgänge an. Wenn Sie den Clusternamen oder die **(Anzahl) ops** auswählen, wird eine Liste der Hintergrundvorgänge angezeigt.

* **(Anzahl) alerts**: Warnungen oder kritische Warnungen, die ggf. für den Cluster angezeigt werden. Wenn Sie diese Option auswählen, wird eine Liste der Warnungen angezeigt.

* **Dashboard**: Zeigt das Dashboard an.

* **Services**: Informationen und Konfigurationseinstellungen für die Dienste im Cluster.

* **Hosts**: Informationen und Konfigurationseinstellungen für die Knoten im Cluster.

* **Alerts**: Eine Liste von Informationen, Warnungen und kritischen Warnungen.

* **Admin**: Softwarestapel/Dienste, die im Cluster installiert sind, Dienstkontoinformationen sowie die Kerberos-Sicherheit.

* Schaltfläche **Admin**: Ambari-Verwaltung, Benutzereinstellungen und Abmeldung.

##Überwachung

###Warnungen

Ambari bietet viele Warnungen, die eine der folgenden Statusangaben aufweisen:

* **OK**

* **Warning**

* **CRITICAL**

* **UNKNOWN**

Andere Warnungen als **OK** führen dazu, dass der Eintrag **(Anzahl) alerts** am Seitenanfang die Anzahl der Warnungen anzeigt. Wenn Sie diesen Eintrag auswählen, werden die Warnungen und deren Statusangaben angezeigt.

Warnungen sind in mehrere Standardgruppen angeordnet, die über die Seite **Alerts** angezeigt werden können.

![Seite "Warnungen"](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Sie können die Gruppen verwalten, indem Sie das Menü **Actions** und dann die Option **Manage Alert Groups** auswählen. Dadurch können Sie vorhandene Gruppen ändern oder neue Gruppen erstellen.

![Warnungsgruppen verwalten (Dialogfeld)](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Sie können auch über das Menü **Aktionen** Warnbenachrichtigungen erstellen. Dadurch können Sie Auslöser erstellen, die Benachrichtigungen per **E-Mail** oder **SNMP** senden, wenn bestimmte Kombinationen von Warnung und Schweregrad auftreten. Sie können z. B. eine Warnung senden, wenn eine der Benachrichtigungen in der Gruppe **YARN Default** auf **Critical** festgelegt ist.

![Warnung erstellen (Dialogfeld)](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

###bereitstellen

Die Registerkarte **Metrics** im Dashboard enthält eine Reihe von Widgets, die das Überwachen des Clusterstatus auf einen Blick erleichtern. Verschiedene Widgets wie **CPU Usage** bieten zusätzliche Informationen, wenn Sie darauf klicken.

![Dashboard mit Metriken](./media/hdinsight-hadoop-manage-ambari/metrics.png)

Die Registerkarte **Heatmaps** zeigt Metriken als farbige Wärmebilder an, die von Grün bis Rot reichen.

![Dashboard mit Heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Ausführlichere Informationen zu den Knoten im Cluster finden Sie unter **Hosts**. Wählen Sie dort den jeweiligen Knoten, an dem Sie interessiert sind.

![Hostdetails](./media/hdinsight-hadoop-manage-ambari/host-details.png)

###Dienste

Die Seitenleiste **Services** des Dashboards bietet einen schnellen Überblick über den Status der Dienste, die im Cluster aktiv sind. Es werden verschiedene Symbole verwendet, um den Status oder auszuführende Aktionen anzuzeigen, z. B. ein gelbes Recycling-Symbol, wenn ein Dienst wiederverwendet werden muss.

![Seitenleiste "Services"](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

Wenn Sie einen Dienst auswählen, werden weitere ausführliche Informationen zum Dienst angezeigt.

![Zusammenfassungsinformationen zum Dienst](./media/hdinsight-hadoop-manage-ambari/service-details.png)

####Quicklinks

Einige Dienste zeigen oben auf der Seite den Link **Quick Links** an. Hierüber können Sie auf dienstspezifische Webbenutzeroberflächen zugreifen. Beispiel:

* **Job History**: MapReduce-Auftragsverlauf.

* **Resource Manager**: Benutzeroberfläche des YARN-Ressourcen-Managers.

* **NameNode**: NameNode-Benutzeroberfläche im Hadoop Distributed File System (HDFS).

* **Oozie Web UI**: Oozie-Benutzeroberfläche.

Wenn Sie einen dieser Links öffnen, wird eine Registerkarte im Browser geöffnet, auf der die ausgewählte Seite angezeigt wird.

> [AZURE.NOTE]Wenn Sie einen **Quick Link** für einen Dienst auswählen, führt dies zum Fehler "Server nicht gefunden", sofern Sie nicht einen SSL-Tunnel (Secure Sockets Layer) verwenden, um den Webdatenverkehr per Proxy an den Cluster weiterzuleiten. Die Ursache ist, dass die Webanwendungen, mit denen diese Informationen angezeigt werden, nicht im Internet verfügbar gemacht werden.
>
> Informationen zum Verwenden eines SSL-Tunnels mit HDInsight finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

##Verwaltung

###Ambari-Benutzer, -Gruppen und -Berechtigungen

Mit HDInsight-Clustern sollten keine Benutzer, Gruppen und Berechtigungen verwaltet werden.

###Host

Die Seite **Hosts** listet alle Hosts im Cluster auf. Gehen Sie folgendermaßen vor, um Hosts zu verwalten.

![Seite "Hosts"](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE]Für HDInsight-Cluster sollte kein Host hinzugefügt, zurückgesetzt oder wiederhergestellt werden.

1. Wählen Sie die Hosts aus, die Sie verwalten möchten.

2. Verwenden Sie das Menü **Actions**, um die Aktion auswählen, die Sie durchführen möchten:

	* **Start all components**: Alle Komponenten auf dem Host starten.

	* **Stop all components**: Alle Komponenten auf dem Host beenden.

	* **Restart all components**: Alle Komponenten auf dem Host neu starten.

	* **Turn on maintenance mode**: Unterdrückt Warnungen für den Host. Dies sollte aktiviert werden, wenn Sie Aktionen ausführen, die Warnungen generieren, z. B. Neustarten eines Diensts, den ausgeführte Dienste benötigen.

	* **Turn off maintenance mode**: Versetzt den Host wieder in den normalen Warnmodus.

	* **Stop**: Beendet "DataNode" oder "NodeManager" auf dem Host.

	* **Start**: Startet "DataNode" oder "NodeManager" auf dem Host.

	* **Restart**: Beendet "DataNode" oder "NodeManager" auf dem Host und startet sie dann neu.

	* **Decommission**: Entfernt einen Host aus dem Cluster.

		> [AZURE.NOTE]Verwenden Sie diese Aktion nicht für HDInsight-Cluster.

	* **Recommission**: Fügt einen zuvor außer Betrieb gesetzten Host zum Cluster hinzu.

		> [AZURE.NOTE]Verwenden Sie diese Aktion nicht für HDInsight-Cluster.

###<a id="service"></a>Dienste

Verwenden Sie auf der Seite **Dashboard** oder **Dienste** am Ende der Dienstliste die Schaltfläche **Aktionen**, um alle Dienste zu beenden und zu starten.

![Dienstaktionen](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [AZURE.WARNING]Zwar wird __Dienst hinzufügen__ in diesem Menü aufgelistet, es sollte aber nicht verwendet werden, um dem HDInsight-Cluster Dienste hinzuzufügen. Neue Dienste sollten während der Clusterbereitstellung mit einer Skriptaktion hinzugefügt werden. Weitere Informationen zur Verwendung von Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).


Während die Schaltfläche **Actions** alle Dienste neu starten kann, ist es möglicherweise häufig erforderlich, dass Sie bestimmte Dienste starten, beenden oder neu starten möchten. Gehen Sie wie folgt vor, um Aktionen für einzelne Dienste auszuführen:

1. Wählen Sie auf der Seite **Dashboard** oder **Services** einen Dienst aus.

2. Verwenden Sie am oberen Rand der Registerkarte **Summary** die Schaltfläche **Service Actions**, und wählen Sie dann die auszuführende Aktion aus. Dadurch wird der Dienst auf allen Knoten neu gestartet.

	![Dienstaktion](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE]Der Neustart einiger Dienste bei aktivem Cluster kann dazu führen, dass Warnungen generiert werden. Um dies zu vermeiden, können Sie die Schaltfläche **Service Actions** verwenden, um den **Maintenance mode** für den Dienst zu aktivieren, bevor Sie den Neustart durchführen.

3. Nachdem eine Aktion ausgewählt wurde, wird der Eintrag **(Anzahl) ops** am Seitenanfang erhöht, um anzuzeigen, dass ein Hintergrundvorgang ausgeführt wird. Sofern die Anzeige konfiguriert ist, wird die Liste der Hintergrundvorgänge angezeigt.

	> [AZURE.NOTE]Wenn Sie den **Maintenance mode** für den Dienst aktiviert haben, denken Sie daran, ihn mithilfe der Schaltfläche **Service Actions** wieder zu deaktivieren, sobald der Vorgang abgeschlossen ist.

Gehen Sie wie folgt vor, um einen Dienst zu konfigurieren:

1. Wählen Sie auf der Seite **Dashboard** oder **Services** einen Dienst aus.

2. Wählen Sie die Registerkarte **Configs** aus. Daraufhin wird die aktuelle Konfiguration angezeigt. Zudem wird eine Liste der vorherigen Konfigurationen angezeigt.

	![Konfigurationen](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Verwenden Sie die angezeigten Felder, um die Konfiguration zu ändern, und klicken Sie dann auf **Save**. Sie können auch eine vorherige Konfiguration und dann **Make current** auswählen, um die vorherigen Einstellungen zu reaktivieren.

##Ambari-Ansichten

Ambari-Ansichten ermöglichen Entwicklern das Hinzufügen von Benutzeroberflächenelementen zur Ambari-Webbenutzeroberfläche mit dem [Ambari Views Framework](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight bietet für Hadoop-Clustertypen die folgenden Ansichten:

* Yarn-Warteschlangen-Manager: Der Warteschlangen-Manager bietet eine einfache Benutzeroberfläche zum Anzeigen und Ändern von YARN-Warteschlangen.
* Hive-Ansicht: In der Hive-Ansicht können Sie Hive-Abfragen direkt in Ihrem Webbrowser ausführen. Sie können Abfragen speichern sowie Ergebnisse anzeigen, im Clusterspeicher speichern oder auf Ihr lokales System herunterladen. Weitere Informationen zum Verwenden von Hive-Ansichten finden Sie unter [Verwenden von Hive-Ansichten mit HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).
* Tez-Ansicht: Mithilfe der Tez-Ansicht können Sie Aufträge besser verstehen und optimieren, indem Sie Informationen dazu anzeigen, wie Tez-Aufträge ausgeführt und welche Ressourcen von den Aufträgen verwendet werden.

<!---HONumber=AcomDC_1210_2015-->